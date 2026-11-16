# Ghost Speak — Product and Architecture

A standalone, Ghost Chat–inspired language-learning app: guided lessons, practical conversations, vocabulary reviews, and honest progress tracking.

**Status:** implementation specification, not a built app. The folders and endpoints below are proposed deliverables. No app code or tests accompany these documents. Read ROADMAP.md for implementation order and release gates.

## 1. Product direction

**Promise:** A little practice. A lot more confidence.

Daily loop: resume a lesson → learn useful phrases → listen and respond → practice a real scenario → receive brief feedback → review saved words later.

Translate the supplied screenshots into an original identity:
- Winding study trail → floating lesson orbs on a spectral path.
- Conversation topic grid → rounded Ghost scenario cards.
- Illustrated vocabulary → original/licensed artwork, examples, and audio.
- Speaking/listening dashboards → measured activity, not promotional sample totals.
- Stars and locks → documented completion rules and prerequisites.

Ghost Speak is a working name; check trademark, domain, and store availability. It does not automatically inherit Ghost Chat accounts, wallets, provider access, or model subscriptions. Use original branding assets rather than copying the reference app.

## 2. Platform and scope

- Initial platform: iPhone, iOS 17+, Swift 6, SwiftUI, Observation, SwiftData, Swift Concurrency.
- Audio: AVFoundation; optional Apple Speech transcription where supported.
- Server: TypeScript, maintained Node.js LTS, PostgreSQL, HTTPS, managed secrets.
- Billing: StoreKit 2, only after the connected service and verified entitlements work.
- Architecture: offline-first learning; server-mediated AI and optional account sync.
- First content pack: Spanish explanations → English learning, beginner level, English interface initially.
- Launch inventory: 4 units, 20 lessons, 10 guided scenarios; native/proficient editorial review required.
- Later candidates: English → Spanish/French, followed by other pairs after evaluation.

Not first-release scope: live avatars, social feeds, leaderboards, real-time voice, pronunciation scoring, unlimited languages, custom model training, or a web/Android client. No vector database or Kubernetes is necessary initially.

## 3. Screens and navigation

Five tabs, each with its own NavigationStack: **Learn · Practice · Words · Progress · Profile**.

| Area | Required experience |
|---|---|
| Onboarding | Native/explanation language, target locale, level, daily goal, optional reminder; first lesson without registration |
| Learn | Daily goal, resume card, units, winding lesson path, locked/available/in-progress/complete states, accessible list alternative |
| Lesson | Progress, answer feedback, retry, save/resume, exit confirmation where needed, completion summary |
| Practice | Topic grid, scenario details, guided or AI mode, text entry, optional editable transcription, replay, hint, finish |
| Words | Due today, learning/mastered/saved lists, search, word detail, review queue |
| Progress | Active time, completed lessons, words introduced/mastered, recording/listening activity, streak |
| Profile | Learning preferences, audio, appearance, reminders, export/delete, optional account, sync and subscription controls |

Lesson types: multiple choice, word ordering, listening comprehension, vocabulary matching, and guided speaking/typed response. Do not ask AI to replace authored answer keys for deterministic exercises.

Topics include greetings, small talk, routine, family, food, travel, shopping, work, hobbies, and weekend plans. Every scenario defines roles, objective, level, teaching pair, useful phrases, and available modes.

## 4. Ghost design system

Proposed tokens, not claims about Ghost Chat's internal design values:

| Token | Hex |
|---|---|
| Background | #0B0B12 |
| Surface / elevated | #171722 / #222232 |
| Primary / secondary text | #F6F5FC / #B8B5CB |
| Ghost lavender | #B6A0FF |
| Spectral mint | #7DE3C1 |
| Gold / error coral | #F5CC78 / #FF8D9B |

Use rounded system typography, an 8-point spacing rhythm, 16–20-point screen margins, 20–28-point card radii, and minimum 44 × 44-point touch targets. Original ghost mascot states: idle, listening, thinking, celebration; provide static alternatives.

Support VoiceOver, Dynamic Type, Reduce Motion, measured contrast, light/dark themes, and states distinguishable without color. Prepare mixed-direction and right-to-left layouts before adding RTL teaching packs. Do not manually reverse strings.

## 5. Multilingual contract

Store separately: interface locale, native language, explanation language, target language, target locale/dialect, learner level, and optional transliteration preference. Use BCP-47 tags where appropriate; a flag is not a language identifier.

A reviewed teaching pair is a product capability, not an automatic translation of another course. Track curriculum, interface localization, AI, transcription, and voice availability independently. Do not advertise every language a model understands as a supported course. Hide unsupported pairs or clearly label experimental practice.

Arabic varieties, regional Portuguese/Spanish, and script aids need explicit content decisions. Beginner level is a teaching setting, not a certified CEFR assessment.

## 6. AI strategy

**Recommendation:** evaluate a current stable Gemini Flash-class model for everyday tutoring, a stronger Gemini/OpenAI text model for difficult feedback, and Claude as a quality challenger if budget allows. Select exact production model IDs only after checking current provider documentation and evaluation results. Pin versions server-side where available.

Do not declare one provider best for all languages. General multilingual reasoning benchmarks help shortlist candidates but do not establish tutoring quality, accent understanding, or learning outcomes. See the Artificial Analysis multilingual index and the education study linked below.

Evaluate text tutoring, transcription, and spoken output separately:
- Text: correction accuracy, naturalness, explanation-language adherence, difficulty control.
- Transcription: learner accents, noise, silence, code-switching, locale availability.
- Speech output: intelligibility, target locale, pronunciation, latency, cost.
- Pronunciation assessment: separate acoustic evaluation; transcript matching is not a pronunciation score.

Start with one approved text model and one approved fallback. Add language-specific routing only when measured benefits justify it. Do not silently send data to undisclosed fallback providers. If no approved provider works, offer guided practice.

For each launch pair, maintain at least 100 reviewed text cases including valid answers, common mistakes, ambiguous phrases, register, code-switching, and instruction attacks. Use native/proficient teaching reviewers; double-review a shared subset and adjudicate disagreements. Test voice with consented/licensed recordings separately. Log model, prompt, dataset, rubric, cost, and latency versions.

The server owns scenario, languages, locale, level, and bounded history. Treat learner text as untrusted data. Validate response fields: target-language reply, up to three corrections referring to actual learner text, explanation-language feedback, optional suggested vocabulary, and bounded objective feedback. Do not request hidden reasoning. AI cannot grant entitlements, execute arbitrary tools, or authoritatively complete lessons.

## 7. Proposed repository structure

```text
GhostSpeak/
  README.md
  ROADMAP.md
  Docs/                       DesignSystem, ContentAuthoring, API, PrivacyDataMap, ReleaseChecklist
  .github/workflows/          iOS and server CI configurations
  iOS/
    GhostSpeak.xcodeproj/
    GhostSpeak/
      App/                    App entry, environment, root, routing
      DesignSystem/           Theme, buttons, cards, mascot, accessibility helpers
      Domain/                 Content models, grading, unlocks, review scheduling, time/streak logic
      Data/
        Persistence/          Versioned SwiftData schema and migration plan
        Repositories/         Content, progress, words, sync
        Network/              API client, DTOs, typed errors
      Services/               Audio coordinator, speech, notifications, auth, Keychain, billing, export
      Features/
        Onboarding/
        Learn/
        Lesson/
        Practice/
        Words/
        Progress/
        Profile/
        Subscription/
      Resources/
        Assets.xcassets/
        Localizable.xcstrings
        PrivacyInfo.xcprivacy
        Content/
          manifest.json
          language-capabilities.json
          es-to-en-a1/         course.json, vocabulary.json, scenarios.json
        Audio/
    GhostSpeakTests/
    GhostSpeakUITests/
  server/
    package.json
    package-lock.json
    tsconfig.json
    .env.example
    migrations/
    src/
      index.ts
      config.ts
      routes/
      middleware/
      db/
      services/               Conversations, quotas, entitlements, sync, deletion
      providers/              Provider contract, approved adapters, model router
    tests/
  evaluation/                 Datasets, rubrics, versioned results, evaluation instructions
  scripts/                    Content validator
```

These directories are not empty files to create in advance. Implement files as milestones need them. Each feature normally has a root view, an observable feature model where useful, and focused subviews. The app is an Xcode target, not a Swift package; add Package.swift only if extracting reusable packages later.

## 8. Architecture and data

Flow: **View → feature model → repository/service → local storage or backend**.

UI state owners use @MainActor. Keep grading, scheduling, unlocks, and metrics independent of UI/storage frameworks. Inject clocks, calendars, audio, repositories, and API dependencies. Keep SwiftData objects within their owning context/actor; use immutable Sendable snapshots across concurrency boundaries. Save answers before advancing. Make completion and rewards idempotent. Cancel work when leaving a feature. Never silently reset a failing database.

| Record group | Main fields/responsibility |
|---|---|
| Course/Unit/Lesson | Stable IDs, teaching pair, level, prerequisites, content version |
| Exercise | Typed payload, authored accepted answers, feedback, optional audio |
| Vocabulary/Scenario | Locale-aware terms/examples or roles/objectives/guided script |
| Learner preferences | Separate language settings, goal, learning timezone |
| Attempt/Answer | Attempt ID, content version, response, first-attempt result, modality |
| VocabularyProgress/ReviewEvent | Due date, interval, rating history, lapses |
| PracticeSession/ActivityEvent | Unique event ID, session, timestamp, duration, modality, learning-day key |
| Server records | Users, revocable sessions, ordered turns, usage ledger, entitlements, sync cursors/tombstones |

Version bundled content separately from persistence schemas. Retain attempt content versions so updates do not reinterpret historical results. Store credentials in Keychain/server secrets, never learning records.

## 9. Learning and measurement rules

First course: First Contact, Everyday Life, Out in the World, Real Connections; five lessons each. Each lesson has 8–12 reviewed exercises, 6–10 relevant words/phrases, a practical objective, explanations, and licensed/original audio.

Grading uses stable choice IDs and unique token IDs for repeated words. Normalization is exercise-specific; preserve distinctions that matter. Completion requires the authored required attempts and feedback acknowledgment. Proposed stars: completion = one; at least 70% first-attempt accuracy = two; at least 90% = three. Exclude ungradable items; if none are gradable, show completion without an accuracy star rating. Replays do not repeat first-completion rewards.

Initial review scheduler is a transparent heuristic, not FSRS: Again retries after 10 minutes and resets to relearning; Hard gives new/relearning items 1 day, otherwise rounded interval ×1.2; Good gives new/relearning items 1 day, otherwise rounded interval ×2; Easy gives new/relearning items 3 days, otherwise rounded interval ×3. Minimum intervals for existing items: 1/2/3 days respectively; cap at 180 days. Use calendar-day arithmetic for day intervals, absolute elapsed time for 10 minutes. Save atomically and reject duplicate ratings. Mastered means three distinct successful review days plus interval ≥14 days; Again removes that label.

Measure active intervals, excluding background/pause/prolonged inactivity. Union overlapping intervals for total active time. Listening counts actual playback. If silence cannot be excluded, call the speaking metric recording practice time. Typed answers do not count as speaking. Streaks use completed activities and a documented learning timezone; device timezone changes must not rewrite history.

## 10. Connected API and security

Proposed HTTPS routes:
- POST /v1/auth/apple; POST /v1/auth/refresh; POST /v1/auth/logout.
- GET /v1/config; GET /v1/usage.
- POST /v1/conversations; POST /v1/conversations/{id}/turns; POST /v1/conversations/{id}/finish.
- DELETE /v1/conversations/{id}.
- POST /v1/sync/push; GET /v1/sync/pull.
- POST /v1/entitlements/reconcile; POST /v1/apple/notifications.
- POST /v1/account/export; DELETE /v1/account.

Authenticate resource ownership on every endpoint. Turns require mutation IDs, expected conversation version, bounded lengths/history, durable idempotency, server-owned ordering, and rate limits. Reserve quota atomically before a paid call, reconcile actual usage, and release/reconcile failures. Unknown provider outcomes must not create uncontrolled paid retries. Clients cannot choose arbitrary paid model IDs or claim their own entitlements.

Offline learning needs no account. Connected services require an optional account with server-verified Sign in with Apple and revocable app sessions. Sync local events through the backend; do not mix CloudKit and custom synchronization without redesign. Use unique events, cursor-based pulls, deterministic review ordering, and deletion tombstones. Confirm guest migration; isolate account caches and prevent deleted data reappearing from another device.

## 11. Audio, permissions, and privacy

One coordinator owns audio sessions. Handle permission, recording, transcription, editing, sending, waiting, playback, cancellation, interruption, and failure. Stop recording on navigation/backgrounding; prevent tutor audio from being recaptured. Cap recording duration and allow typed fallback. Bundle essential listening clips for reliable offline use; device voices/recognizers may require downloads or network access.

Configuration when corresponding features are added:
- NSMicrophoneUsageDescription for recording.
- NSSpeechRecognitionUsageDescription for Apple Speech.
- Request UserNotifications authorization after opt-in; there is no generic notification usage-description key.
- Sign in with Apple and In-App Purchase configuration/capabilities when implemented.
- Audit PrivacyInfo.xcprivacy against actual APIs/SDKs; do not guess required-reason codes.
- No background-audio capability for the foreground-only first version.

Initial design transcribes on the client and does not upload raw audio to the app backend. This does not guarantee Apple transcription is on-device; check availability and disclose network processing. Explain AI providers and text sent before first AI use. Proposed backend context retention is 24 hours after inactivity; verify provider terms and operational deletion before promising it. Saving transcripts is optional. Never claim zero retention/no training without verifying contracts. Exclude tokens and routine conversation bodies from logs.

Provide local guest export/reset, authenticated account export/deletion, retention documentation, and backup-deletion policy. Account deletion revokes sessions but does not automatically cancel App Store subscriptions. Define age eligibility before launch; do not market to children without a separate compliance plan.

## 12. Billing and operations

Proposed free tier: starter course, guided practice, reviews, bundled audio. Paid tier: metered AI and expanded reviewed content. Choose prices and quotas only after measuring text/audio usage, retries, hosting, database, monitoring, support, and platform fees.

Load localized StoreKit prices. Handle restore, pending, cancellation, unverified transactions, expiry, refund, and revocation. Verify server entitlements before paid AI access; process verified App Store server notifications. Review current storefront rules before release. Do not sell a simulated feature or promise unlimited AI without abuse/margin testing.

Track crash-free use, p50/p95 latency, schema failures, cost per completed session, quota rejection, sync errors, and deletion-job completion. An AI kill switch must preserve offline learning.

## 13. Setup and definition of done

Once implementation exists: Xcode → File → New → Project → iOS → App; choose GhostSpeak, SwiftUI, Swift, a unique bundle ID and signing team. Set iOS 17+, add files under iOS/GhostSpeak with correct target membership, and configure the versioned SwiftData container. Select the GhostSpeak scheme and an iPhone simulator, then Product → Run (⌘R). Use Product → Test (⌘U) for tests. Audio/permissions require physical-device checks too.

Server setup must include its package manifest, lockfile, tested scripts, database migrations, secret names, and development instructions when implemented. No provider credentials belong in the repository or app binary.

A working release means real, persisted learning; reviewed content; measured metrics; functioning guided practice offline; evaluated connected AI with quotas; safe audio fallbacks; tested sync/deletion; verified billing if sold; passing CI and device checks; and completed privacy/accessibility/store review preparation. ROADMAP.md defines the milestone gates.

## Sources and implementation checks

- Multilingual benchmark and methodology entry point: https://artificialanalysis.ai/models/multilingual
- Education reliability research, with task/language limitations: https://arxiv.org/html/2504.17720v2
- Speech recognition provider discovery: https://platform.openai.com/docs/guides/speech-to-text
- Speech output provider discovery: https://platform.openai.com/docs/guides/text-to-speech
- Google voice capability discovery: https://ai.google.dev/gemini-api/docs/live-api/capabilities

Provider catalogs, prices, language availability, retention policies, and Apple requirements can change. Recheck primary documentation during implementation and before release; these references are not a guarantee of support for any particular teaching pair.
