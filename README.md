# ArogyaMesh (SIH 2026)

**ArogyaMesh** is an offline-first clinical decision support and health-record platform designed for rural Primary Health Centres (PHCs) and frontline ASHA workers.

---

## 📁 Repository Structure

```
SIH/
├── .docs/                 # Specifications & Documentation
│   ├── PRD.md             # Product Requirement Document
│   ├── TRD.md             # Technical Architecture & Data Schema
│   ├── milestones.md      # Phased Milestones
│   └── FEATURES.md        # 6-Person Feature Breakdown & Work Allocation Matrix
├── web/                   # Doctor OPD Portal & Admin Dashboard (Next.js + Tailwind CSS)
├── mobile/                # (Upcoming) Frontline ASHA App (React Native / Expo + WatermelonDB)
├── backend/               # (Upcoming) AI Speech & Triage Service (FastAPI + Whisper + IndicNLP)
└── .gitignore
```

---

## 👥 Team Work Allocation & Feature Matrix

Detailed task assignments, story points, acceptance criteria, and tech stack per member are documented in [`.docs/FEATURES.md`](.docs/FEATURES.md).

| Member | Role | Assigned Features |
| :--- | :--- | :--- |
| **Member 1 (Lead)** | Lead Architect & Sync Engine | F05, F06, F18, E2E Integration |
| **Member 2** | Mobile Frontend & UX (ASHA App) | F01 (UI), F04, F09 (UI), F11, F12, F13 (Gen) |
| **Member 3** | Voice & NLP AI Engineer | F01 (STT API), F02 (NER), F16 (AI Summarizer) |
| **Member 4** | Clinical AI & Diagnostic Rules | F03 (ICD-10 Matcher), F09 (HRP), F10 (NCD) |
| **Member 5** | Backend, Cloud Sync & Hardware P2P | F07 (P2P Mesh), F08 (Media Sync), F17 (ABHA), F18 (RLS), F19 (SQL), F20 (PostGIS) |
| **Member 6** | Web OPD Portal & Analytics | F10 (UI), F13 (Scanner), F14 (OPD Queue), F15 (Rx), F16 (UI), F19 (Reports), F20 (Heatmaps) |

---

## 🚀 Getting Started with the Web App (`web/`)

### Prerequisites
- Node.js 18+ & npm

### Running the Web Portal Locally
```bash
cd web
npm install
npm run dev
```
Open [http://localhost:3000](http://localhost:3000) in your browser to view the application.

---

## 🌿 Git & Pull Request Workflow

To keep our repository clean and avoid conflicts:

1. **Pull the latest `master` / `main` branch:**
   ```bash
   git checkout master
   git pull origin master
   ```

2. **Create a dedicated feature branch:**
   ```bash
   # Branch naming format: feature/<member-name>-<feature-name>
   git checkout -b feature/m2-voice-recorder
   ```

3. **Commit your work with clear messages:**
   ```bash
   git add .
   git commit -m "feat(mobile): add vernacular audio recording component with expo-av"
   ```

4. **Push your branch and open a Pull Request:**
   ```bash
   git push origin feature/m2-voice-recorder
   ```
   Open a PR against the `master` / `main` branch for review.
