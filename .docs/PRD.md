# Product Requirement Document (PRD): ArogyaMesh

**ArogyaMesh** is an offline-first clinical decision support and health-record platform designed for rural Primary Health Centres (PHCs) and frontline Accredited Social Health Activist (ASHA) workers. It enables vernacular voice-based patient intake, offline edge triaging for maternal and chronic conditions, automated ICD-10 clinical coding, and low-bandwidth record synchronization.

---

### 1. Problem Statement & Objectives

* **Paper-Heavy Workload:** Frontline ASHA workers spend up to 40% of their field time manually updating physical registers, leading to data entry errors and delayed clinical interventions.
* **Connectivity Bottlenecks:** Rural Sub-Centres and PHCs operate in intermittent or zero-connectivity environments where cloud-only Electronic Health Record (EHR) systems fail.
* **Delayed Referrals:** Early signs of high-risk pregnancies (HRP) and non-communicable diseases (NCDs) go unnoticed in the field due to a lack of immediate clinical decision support at the point of care.

**Core Objectives:**

* Provide zero-latency, 100% offline data capture and automated local delta synchronization.


* Eliminate manual text entry for ASHA workers via vernacular voice intake and edge processing.


* Standardize unstructured rural health notes into structured ICD-10 diagnostic entries and standardized referral slips for PHC Medical Officers.



---

### 2. User Personas

| Persona | Role | Key Pain Points | Primary Goal |
| --- | --- | --- | --- |
| **Sunita Devi (ASHA Worker)** | Field Collector | Low English/typing literacy; carries 6+ heavy registers across door-to-door visits. | Record vitals/symptoms by speaking in her native language; receive immediate risk alerts.

 |
| **Dr. Rajesh (PHC Medical Officer)** | Primary Care Physician | Overwhelmed OPD; receives illegible, unstandardized paper slips without longitudinal history. | View structured patient summaries, auto-coded symptoms (ICD-10), and prioritized triage queues.

 |
| **District Health Officer (Admin)** | Health Administrator | Delayed epidemiological visibility; manual aggregation of maternal and NCD indicators. | Access aggregated public health metrics, disease outbreak heatmaps, and supply stock reports. |

---

### 3. List of 20 Key Features

#### A. Voice & Edge Intake Layer

1. **Vernacular Voice Note Intake:** Speech-to-text pipeline transcribing regional dialects (e.g., Telugu, Hindi) locally using quantized acoustic models to capture raw symptom narratives.


2. **Clinical Entity Extraction:** Local Natural Language Processing (NLP) extracting key symptoms, duration, anatomical locations, and medication history from raw transcriptions.
3. **Automated ICD-10 / SNOMED Mapping:** Translates localized clinical observations into standardized medical terminology and diagnostic codes.


4. **Interactive Voice Prompts:** Step-by-step audio wizard in the local dialect prompting ASHA workers for missing vital signs (e.g., blood pressure, fetal heart rate, blood glucose).

#### B. Offline Database & Synchronization

5. **Local-First Reactive Edge Storage:** High-performance local database on the mobile device to enable instant read/write operations without network connectivity.


6. **Conflict-Free Bidirectional Sync (CRDT):** Deterministic delta-synchronization protocol resolving concurrent record edits when the device reconnects to Wi-Fi/cellular networks.


7. **P2P Mesh Field Transfer:** Direct Wi-Fi Direct or Bluetooth Low Energy (BLE) sync allowing ASHA workers to offload collected batch records to the PHC tablet without internet.
8. **Chunked & Resumable Media Sync:** Background network daemon uploading voice audio files in encrypted chunks over 2G/low-bandwidth connections.

#### C. Clinical Decision Support & Risk Triaging

9. **High-Risk Pregnancy (HRP) Flagging Engine:** Rule-based and lightweight ML classifier evaluating antenatal care (ANC) markers (e.g., BP > 140/90, severe anemia, pedal edema).


10. **NCD Triaging Matrix:** Cardiovascular and diabetes risk calculator scoring patients based on age, BMI, vitals, and family history.
11. **Pediatric Growth & Immunization Tracker:** WHO-standardized growth curve engine with automated edge reminders for upcoming or overdue vaccines.
12. **Critical Red-Flag Alarms:** Visual and auditory urgent-care alerts indicating conditions requiring immediate hospital escalation (e.g., postpartum hemorrhage, acute respiratory distress).

#### D. Referral & Clinical Workflow

13. **Dynamic QR-Coded Referral Slips:** Generates encrypted, offline-readable QR codes containing compressed patient history for physical referral handoffs to secondary hospitals.


14. **PHC Triage OPD Queue:** Medical Officer dashboard automatically sorting daily incoming patients by clinical severity instead of arrival sequence.
15. **One-Tap Prescription & Tele-Consult Integration:** Simple interface for PHC doctors to record treatments and forward complex records for district-level tele-consultation.
16. **Voice-to-Text Clinical Summary for Doctors:** Auto-compiles the ASHA field worker's notes into a clean, chronological 3-line case summary for rapid OPD review.

#### E. Identity, Security & Analytics

17. **ABHA (Ayushman Bharat Health Account) Integration:** Native verification and linking of patient ABHA IDs via OTP/demographic matching.
18. **Role-Based Encrypted Data Storage:** AES-256 local database encryption with role-based access control (RBAC) ensuring ASHA workers only view their assigned catchment area.
19. **Automated Monthly Indicator Reports:** One-click generation of national health portal compliance forms, eliminating manual calculation of monthly performance indicators.
20. **Epidemiological Cluster Heatmaps:** District-level dashboard aggregating synced field records to identify emerging infectious disease spikes (e.g., Dengue, Malaria, Diarrhea) in real time.

---
