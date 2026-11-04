# MediSathi — মেডিসাথী

> Repo: https://github.com/ariandesu/MediSathi
> Live: https://medisathi-clean.vercel.app (per-visitor demo accounts on MongoDB + live Muse AI)

Plain-language health record helper for low health literacy (Bangla/English).
Digitize prescriptions + lab reports, review extracted values, get simple explanations,
log daily check-ins, see timeline + trends, generate source-linked summaries, ask grounded
questions, print a doctor-ready report.

## One-paragraph pitch

MediSathi helps people with low health literacy digitize old prescriptions and lab reports
(photos/PDFs, with on-device OCR in Bangla + English), review and correct the extracted values,
get plain-language explanations from Muse Spark, log a daily wellness check-in, view a timeline
with trend charts, generate a dated, source-linked health summary, ask questions grounded in
their own records, and print a doctor-ready report. It is explicitly **not** a diagnostic tool.

## Demo

- Repo: https://github.com/ariandesu/MediSathi
- Live: https://medisathi-clean.vercel.app
- Default language: Bangla (`bn`), switchable in the header (cookie `medisathi-locale`)

### Test without uploading real data (demo mode)

Click **"Try the demo"** on the login page — you get your own private per-visitor seeded demo account with synthetic records.
Or pick 1 of 5 sample neighbors: 3 recovering (low hemoglobin 9.1 → 10.7, high fasting sugar 152 → 121, high cholesterol 245 → 201) and 2 healthy (steady labs, steady habits).

The public home page opens the demo in English or Bangla with one tap, on Rahima's records.

1. **Home** — judge tour, one "next step" card and your latest lab results as range bars.
2. **Records → Documents** — 3 sample files per profile (2 confirmed labs showing a trend, 1 as-written note needing review), each with a synthetic paper. Open one that needs checking and tap a value: it lights up on the paper. Fix anything wrong, tap **Looks right**, then **Save record**. The server refuses to save until every value is checked. **Sample blood test** and **Sample prescription** send a sample paper through the real pipeline: OCR on the device, then Muse, about 30 seconds.
3. **Check-in** — tap **Add** to log sleep, water or activity with − / + buttons. Tapping an urgent sign (e.g. chest pain) opens an urgent-care alert with a **Call 999** button.
4. **Records → Timeline** — latest lab results, 30-day wellness trends (missing days are gaps, never zero), every entry, and **Explain in simple words** per record.
5. **Ask** — grounded Q&A with citations that link to timeline records. Mic button for voice input, speaker button to read the answer aloud.
6. **Doctor** — print-ready doctor report and the latest summary (**Generate summary**; live Muse AI when `MODEL_API_KEY` is set).

Old URLs (`/documents`, `/timeline`, `/summary`, `/report`) redirect to the new pages.

All fixture data is synthetic and clearly watermarked **"SAMPLE — NOT REAL PATIENT DATA"**.

## Screenshots

Bangla UI, desktop + mobile. All data shown is synthetic (`SAMPLE — NOT REAL PATIENT DATA`).

### Landing · Login · Sign up

| Landing (desktop) | Landing (mobile) |
|---|---|
| ![Landing page on desktop](docs/screenshots/desktop/01-landing.jpg) | ![Landing page on mobile](docs/screenshots/mobile/01-landing.jpg) |

| Login — 5 sample neighbors (desktop) | Login (mobile) |
|---|---|
| ![Login with demo profiles on desktop](docs/screenshots/desktop/02-login.jpg) | ![Login on mobile](docs/screenshots/mobile/02-login.jpg) |

| Sign up (desktop) | Sign up (mobile) |
|---|---|
| ![Sign up on desktop](docs/screenshots/desktop/03-signup.jpg) | ![Sign up on mobile](docs/screenshots/mobile/03-signup.jpg) |

### Home · Records · Review

| Home (desktop) | Home (mobile) |
|---|---|
| ![Dashboard on desktop](docs/screenshots/desktop/04-dashboard.jpg) | ![Dashboard on mobile](docs/screenshots/mobile/04-dashboard.jpg) |

| Documents (desktop) | Documents (mobile) |
|---|---|
| ![Documents on desktop](docs/screenshots/desktop/05-records-documents.jpg) | ![Documents on mobile](docs/screenshots/mobile/05-records-documents.jpg) |

| Timeline (desktop) | Timeline (mobile) |
|---|---|
| ![Timeline on desktop](docs/screenshots/desktop/06-records-timeline.jpg) | ![Timeline on mobile](docs/screenshots/mobile/06-records-timeline.jpg) |

| Side-by-side review (desktop) | Review (mobile) |
|---|---|
| ![Review page on desktop](docs/screenshots/desktop/07-review.jpg) | ![Review page on mobile](docs/screenshots/mobile/07-review.jpg) |

### Check-in · Ask · Doctor report

| Check-in (desktop) | Check-in (mobile) |
|---|---|
| ![Check-in on desktop](docs/screenshots/desktop/08-checkin.jpg) | ![Check-in on mobile](docs/screenshots/mobile/08-checkin.jpg) |

| Check-in saved (desktop) | Check-in saved (mobile) |
|---|---|
| ![Check-in saved on desktop](docs/screenshots/desktop/09-checkin-saved.jpg) | ![Check-in saved on mobile](docs/screenshots/mobile/09-checkin-saved.jpg) |

| Ask (desktop) | Ask (mobile) |
|---|---|
| ![Ask on desktop](docs/screenshots/desktop/10-ask.jpg) | ![Ask on mobile](docs/screenshots/mobile/10-ask.jpg) |

| Doctor report (desktop) | Doctor report (mobile) |
|---|---|
| ![Doctor report on desktop](docs/screenshots/desktop/11-doctor-report.jpg) | ![Doctor report on mobile](docs/screenshots/mobile/11-doctor-report.jpg) |

| Health summary (desktop) | Health summary (mobile) |
|---|---|
| ![Health summary on desktop](docs/screenshots/desktop/12-doctor-summary.jpg) | ![Health summary on mobile](docs/screenshots/mobile/12-doctor-summary.jpg) |

## Stack

- Next.js 16 App Router, TypeScript (strict), Tailwind CSS v4
- shadcn/ui only (base-nova) + lucide-react + sonner + recharts
- pnpm; deploy target: Vercel
- Auth.js v4 (credentials + JWT, bcryptjs); MongoDB (GridFS) or in-memory demo store
- OpenAI SDK → Muse Spark (Meta Model API) or canned FixtureAI in demo mode

## Runtime modes

| `MONGODB_URI` | `MODEL_API_KEY` | Data layer | AI layer |
|---------------|-----------------|------------|----------|
| absent | absent | MemoryRepository (seeded fixtures) | FixtureAI (sample) |
| set | absent | MongoRepository | FixtureAI |
| absent | set | MemoryRepository | MuseAI |
| set | set | MongoRepository | MuseAI |

Demo mode builds with **only `AUTH_SECRET`** — no Mongo or Muse credentials required.

## Environment variables

Create `.env.local` (git-ignored, never commit):

```
AUTH_SECRET=            # REQUIRED. Generate: openssl rand -base64 32
MONGODB_URI=            # optional; absent => demo mode
MONGODB_DB=medisathi
MODEL_API_KEY=          # optional; absent => fixture AI
MUSE_MODEL=muse-spark-1.3
MUSE_BASE_URL=https://api.meta.ai/v1
```

`.env.example` contains the empty-key template.

## Local setup

```bash
# 1. Install deps
pnpm install

# 2. Copy env template (real secrets go in .env.local, never committed)
cp .env.example .env.local
# edit .env.local with your values (AUTH_SECRET required)

# 3. Dev server
pnpm dev

# 4. Verify checks
pnpm lint && pnpm typecheck && pnpm test && pnpm build

# 5. Build must also pass with only AUTH_SECRET (demo mode):
mv .env.local /tmp/ && pnpm build && mv /tmp/.env.local .env.local
```

## Architecture

```mermaid
graph TD
    User[User / Patient] -->|1. Upload photo/PDF| Browser[Browser: tesseract.js OCR, compress ≤2MB]
    Browser -->|OCR text + file| Next[Next.js App Router]
    Next -->|validate MIME/magic/size| Repo[(Repository: Memory or Mongo+GridFS)]
    Repo -->|AI extraction| AI[AI: Muse Spark or FixtureAI]
    AI -->|fields + agreement| Next
    Next --> Review[Review page: side-by-side, confirm]
    Review -->|Confirm| Record[(MedicalRecord)]
    User -->|2. Daily check-in| Wellness[Wellness entry]
    User -->|3. Timeline + trends| Timeline[Timeline: records + check-ins, charts]
    User -->|4. Summary| Summary[AI summary + grounding filter]
    User -->|5. Ask| Ask[Grounded Q&A, citations]
    User -->|6. Report| Report[Print-optimized doctor report]
```

## Privacy statement (honest)

- HTTPS in transit (Vercel); MongoDB Atlas at-rest encryption.
- OCR runs **in your browser** (tesseract.js); document bytes never leave your device for OCR.
- Documents **ARE sent to the Meta Model API (Muse Spark)** for interpretation — not end-to-end encrypted.
- Demo mode uses synthetic data only (`isSample`); resets on reload.
- Not end-to-end encrypted. Do not upload real sensitive documents if you require E2EE.

## Medical disclaimer

MediSathi explains your records in simple language. It is **not a diagnostic tool** and **never replaces a doctor**.
It never prescribes, never changes doses, never says "you don't need a doctor."
If you have severe symptoms (chest pain, difficulty breathing, fainting, severe bleeding, confusion, stroke signs),
call **999** (Bangladesh) or go to the nearest hospital immediately.

## Deploy (Vercel)

1. Connect the GitHub repo to Vercel.
2. Add env vars in Vercel project settings: `AUTH_SECRET`, `MONGODB_URI`, `MONGODB_DB`, `MODEL_API_KEY`, `MUSE_MODEL`, `MUSE_BASE_URL`.
3. Atlas: add Vercel IP (or 0.0.0.0/0) to Network Access list.
4. Run once: `pnpm tsx scripts/init-indexes.ts` (creates Mongo indexes idempotently).
5. Deploy. The app works in demo mode until env vars are set in Vercel.

## Switching to MongoDB

Set `MONGODB_URI` (and `MONGODB_DB`) in `.env.local` / Vercel. The app auto-creates indexes on first
connection and via `scripts/init-indexes.ts`. Contract tests run against both `MemoryRepository` and
`MongoRepository` (mongodb-memory-server).

## Checks (must pass before push)

```
pnpm lint && pnpm typecheck && pnpm test && pnpm build
```

Build verified with empty env and with only `AUTH_SECRET`.

## Docs

- `AGENTS.md` — build spec (milestones M1–M9)
- `docs/ARCHITECTURE.md` — this architecture
- `docs/THREAT_MODEL.md` — threats & mitigations
- `docs/DECISIONS.md` — decision log
