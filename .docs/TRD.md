
# Technical Requirement Document (TRD): ArogyaMesh

### 1. System Architecture Diagram

```
 [ASHA Mobile App (Offline First)]           [PHC Doctor / Admin Portal (Web)]
  - React Native / Expo[cite: 1]                       - Next.js (App Router)[cite: 1]
  - WatermelonDB (SQLite)[cite: 1]                     - Tailwind CSS[cite: 1]
  - On-Device Whisper.tflite / IndicWav2Vec[cite: 1]   - TanStack Query
         │                                                    │
         ▼ (Encrypted Delta Sync / REST)                      ▼
 ┌────────────────────────────────────────────────────────────────────────────┐
 │                            BACKEND INFRASTRUCTURE                         │
 │                                                                            │
 │  [Clerk Auth / Mobile OTP][cite: 1] ◄──► [FastAPI Python Microservice]   │
 │                                             - Whisper Speech Processor[cite: 1]    │
 │                                             - IndicNLP & ICD-10 Mapper[cite: 1]    │
 │                                             - Risk Scoring Pipeline[cite: 1]       │
 │                                                        │                   │
 │                                                        ▼                   │
 │                                             [Supabase PostgreSQL][cite: 1]          │
 │                                             - PostGIS Spatial Extension    │
 │                                             - Row-Level Security (RLS)     │
 │                                             - Edge Storage (Audio Blobs)   │
 └────────────────────────────────────────────────────────────────────────────┘

```

---

### 2. Proposed Tech Stack Specifications

| Layer | Component | Recommended Technology | Technical Justification |
| --- | --- | --- | --- |
| **Mobile Client** | Framework | React Native (Expo)

 | Cross-platform, rich ecosystem for native audio recording and hardware access.

 |
| **Local Storage** | Offline Database | WatermelonDB (over SQLite)

 | Lazy-loading, reactive observable architecture built for handling thousands of records offline with high performance.

 |
| **Admin & OPD** | Web Frontend | Next.js (React), Tailwind CSS

 | Server-side rendering for snappy OPD loading and role-based doctor dashboards.

 |
| **Authentication** | Auth Service | Clerk

 | Secure JWT session management, RBAC, and seamless mobile phone/OTP verification workflows.

 |
| **Core Database** | Cloud Backend | Supabase (PostgreSQL)

 | Native Row-Level Security (RLS), real-time change subscriptions, and integrated object storage for audio files.

 |
| **AI / NLP** | Speech & Triage | Whisper API / IndicWav2Vec, Lightweight LLM

 | Converts regional Indian voice inputs into structured English clinical data mapped to ICD-10 codes.

 |

---

### 3. Data Schema (PostgreSQL / WatermelonDB)

```sql
-- Patients Core Table
CREATE TABLE patients (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    abha_id VARCHAR(32) UNIQUE,
    full_name VARCHAR(128) NOT NULL,
    dob DATE NOT NULL,
    gender VARCHAR(16) NOT NULL,
    village_code VARCHAR(32) NOT NULL,
    created_by_worker UUID REFERENCES auth.users(id),
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW(),
    deleted BOOLEAN DEFAULT FALSE
);

-- Clinical Encounters & Triaging Records
CREATE TABLE clinical_encounters (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    patient_id UUID REFERENCES patients(id) ON DELETE CASCADE,
    worker_id UUID NOT NULL,
    encounter_date TIMESTAMPTZ DEFAULT NOW(),
    systolic_bp INT,
    diastolic_bp INT,
    blood_glucose_mg_dl NUMERIC(5,2),
    hemoglobin_g_dl NUMERIC(4,2),
    fetal_heart_rate INT,
    raw_symptoms_text TEXT,
    voice_recording_url TEXT,
    icd10_codes TEXT[], -- Array of mapped codes (e.g., ["O14.0", "E11.9"])
    triage_priority VARCHAR(16) CHECK (triage_priority IN ('ROUTINE', 'MODERATE', 'CRITICAL')),
    sync_status VARCHAR(16) DEFAULT 'SYNCED',
    updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Referral Records
CREATE TABLE referrals (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    encounter_id UUID REFERENCES clinical_encounters(id),
    referring_facility VARCHAR(128) NOT NULL,
    target_facility VARCHAR(128) NOT NULL,
    reason_for_referral TEXT NOT NULL,
    qr_payload TEXT NOT NULL,
    status VARCHAR(32) DEFAULT 'PENDING' CHECK (status IN ('PENDING', 'ACCEPTED', 'COMPLETED')),
    created_at TIMESTAMPTZ DEFAULT NOW()
);

```

---

### 4. Offline Synchronization Protocol

```
[Mobile App (WatermelonDB)]                        [Cloud (Supabase)]
            │                                               │
            ├─── 1. Query local records where synced = FALSE ──┤
            │                                               │
            ├─── 2. POST /api/sync/pull (last_pulled_at) ────►│
            │                                               │
            │◄── 3. Return updated server records ───────────┤
            │       (Resolve conflicts via Last-Write-Wins) │
            │                                               │
            ├─── 4. POST /api/sync/push (local changes batch)►│
            │       - Encrypted records payload             │
            │       - Edge function validates schema & RLS  │
            │                                               │
            │◄── 5. Acknowledge sync (HTTP 200) ────────────┤
            │                                               │
            └─── 6. Update local records: set synced = TRUE ─┘

```

---

### 5. Vernacular Voice-to-ICD-10 Pipeline

```
[ASHA Speaks in Telugu/Hindi]
            │
            ▼
[Local Audio Recording (.m4a)]
            │
            ▼ (Offline: Local Cache / Online: Stream to API)
[FastAPI Microservice Engine]
    ├── 1. Speech-to-Text: OpenAI Whisper / IndicWav2Vec (Extracts Regional Text)[cite: 1]
    ├── 2. Translation & NER: Maps local idioms to English clinical terminology[cite: 1]
    └── 3. Semantic Code Matcher: Matches entities to ICD-10-CM vector embeddings[cite: 1]
            │
            ▼
[Structured Output JSON]
{
  "transcription": "గర్భిణీ స్త్రీకి విపరీతమైన తలనొప్పి మరియు కాళ్ళ వాపు ఉన్నాయి",
  "translated": "Pregnant woman has severe headache and pedal edema",
  "extracted_entities": {
    "symptoms": ["headache", "pedal edema"],
    "condition": "pregnancy",
    "icd10": ["O14.90", "R60.0"]
  },
  "triage_alert": "CRITICAL - High Risk for Preeclampsia"
}

```

---
