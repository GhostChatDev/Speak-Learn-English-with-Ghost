# Ghost Speak — Build and Release Roadmap

Companion to README.md. This is a plan, not a claim that the app or tests already exist. iOS-first: Swift 6, SwiftUI, SwiftData, iOS 17+. Backend: TypeScript, maintained Node.js LTS, PostgreSQL.

## Delivery strategy

Build one complete offline learning loop before connecting AI. Then add evaluated text tutoring, reliable audio input/output, account sync, and verified billing. A visually complete mockup is not a functioning release.

Initial reviewed pair: Spanish explanations → English learning, beginner level, English app interface. Architecture supports other pairs; release coverage grows only after review. Product scope follows the four references: study path, topic practice, vocabulary, and real progress.

### Scheduling assumptions

Rough planning allowance: **12–18 weeks** for an experienced iOS engineer with part-time backend help, design support, and language reviewers. This is not a delivery guarantee. A solo developer may need substantially longer; App Review, content production, language evaluation, and legal review can extend the schedule.

Use milestone acceptance rather than dates as the authority. Do not activate subscriptions until their connected value and server checks pass. If budget is tight, ship a clearly described free offline edition first and defer paid AI.

## Milestone 0 — Scope, design, and contracts

**Indicative effort:** 3–5 working days. Dependency: none.

Deliver:
- Confirm working name, original mascot/art direction, ownership/licensing, supported pair, adult/general-audience eligibility, and free/paid boundaries.
- Docs/DesignSystem.md: tokens, type, components, states, accessible path/list layouts, light/dark behavior.
- Docs/ContentAuthoring.md: teaching objectives, exercise schemas, IDs, accepted variants, source/target locales, audio licensing.
- Docs/PrivacyDataMap.md: data fields, device/server/provider flows, retention, deletion and backup policy.
- Wireframes for onboarding, five tabs, lesson, conversation, reviews, failure states, settings, and paywall.
- ADRs covering offline-first storage, custom server sync rather than CloudKit, and turn-based AI before real-time voice.

Gate:
- Each screenshot feature maps to an original Ghost Speak screen.
- Supported language pairs and unavailable capabilities are explicit.
- Every stored/transmitted data category has a purpose and retention owner.
- The first 5-minute learner journey is defined without login/payment blockers.

## Milestone 1 — Xcode foundation and Ghost UI shell

**Indicative effort:** 4–6 days. Dependency: M0.

Implement in small file-level steps:
1. iOS/GhostSpeak/App/GhostSpeakApp.swift — app entry with initial environment wiring.
2. App/RootView.swift — onboarding versus five-tab root.
3. DesignSystem/GhostTheme.swift — colors, typography, spacing, semantic styles.
4. DesignSystem/GhostButton.swift and GhostCard.swift — accessible reusable controls.
5. DesignSystem/GhostMascotView.swift — original mascot with reduced-motion state.
6. Feature root views and navigation destinations, without fake working actions.
7. .github/workflows/ios.yml — macOS runner with available, explicitly chosen Xcode/simulator; build and test scheme.

Gate:
- Xcode build/test succeeds on chosen supported environment.
- Independent tab navigation and onboarding routing work after relaunch.
- Largest supported text sizes remain usable; VoiceOver labels and focus order are sensible.
- Controls have real actions or are clearly unavailable, not decorative imitations.
- Simulator screenshots reviewed in light/dark and small/large iPhone layouts.

## Milestone 2 — Content models, validation, persistence

**Indicative effort:** 4–6 days. Dependency: M1; content authoring proceeds in parallel.

Deliver:
- Domain models for preferences, capabilities, course, lesson, typed exercises, vocabulary, scenarios.
- Versioned SwiftData schema and migration plan; repository interfaces and concrete local implementations.
- Bundled manifest and one reviewed vertical-slice lesson with 8–12 exercises.
- scripts/validate-content.mjs with documented command, package configuration where required, and CI integration.
- Recovery UI for decoding/storage failure; no silent destructive reset.

Validation includes:
- Unique IDs, valid references, reachable prerequisites, no prerequisite cycles.
- Supported locale pairs and content versions.
- Valid choice IDs/answer keys and ordering token IDs, including repeated words.
- Audio references exist, attribution is recorded, no required field is empty.
- Correct number of options/matching items and coherent exercise payload types.

Gate:
- Invalid fixtures fail validation with useful errors.
- A learner preference and attempt survive termination/relaunch.
- Corrupt content or unavailable storage produces a recoverable state.
- First schema is explicitly versioned before test users accumulate data.

## Milestone 3 — Complete offline lesson loop

**Indicative effort:** 7–10 days. Dependency: M2.

Deliver:
- All five exercise presenters and pure grading policies.
- Attempt save/resume, first-attempt scoring, feedback, completion and replay.
- Unlock policy and accessible path/list views.
- Activity timer with injected clock/calendar.
- At least one full unit of reviewed lessons and bundled audio.

Required tests:
- Choice grading, repeated ordering tokens, accepted variants, Unicode/diacritics according to authored policy.
- Zero gradable items and boundary scores of 70% and 90%.
- Exit mid-answer, force quit, resume, duplicate completion, replay rewards.
- Prerequisite completion and content-version changes.
- Backgrounding, pause, idle timeout, overlapping activity intervals.

Gate: complete lesson one → persist result → unlock lesson two → relaunch → resume correctly, all in airplane mode. No network dependency for bundled course content.

## Milestone 4 — Words, reviews, and trustworthy progress

**Indicative effort:** 5–7 days. Dependency: M3.

Deliver:
- Word lists/detail/search, save/remove behavior, review queue.
- Pure ReviewScheduler with Again/Hard/Good/Easy rules defined in README.md.
- Review history, due counts, mastery labeling.
- Progress screens using real activity and completion events.
- Local export/reset and optional local reminders.

Required tests:
- All rating branches, repeated taps, interval cap and rounding.
- 10-minute elapsed-time retry versus calendar-day scheduling across DST.
- Mastery gained/lost, duplicate reviews, empty queue.
- Midnight, learning timezone, travel/device timezone changes, same-day events.
- Export Unicode and language metadata; reset requires confirmation and removes intended data.

Gate: no sample statistics leak into production. Due counts match stored schedules. Reset/export and denied-notification states work.

## Milestone 5 — Guided practice and reliable audio

**Indicative effort:** 5–8 days. Dependency: M3; can overlap M4.

Deliver:
- Topic browser, scenario details, deterministic guided dialogue, text response mode.
- One AudioSessionCoordinator with mutually exclusive capture/playback.
- Bundled playback, optional system TTS, optional Apple Speech transcription with editable result.
- Permission explanations and typed fallback.
- Scenario finish summary without fabricated AI or pronunciation scores.

Physical-device matrix:
- Permission not determined, granted, denied, restricted; unavailable locale/network.
- Silent input, partial result, transcription error, cancel, maximum recording duration.
- Incoming call/audio interruption, headphone disconnect, Bluetooth route changes.
- Navigate away/background while recording or playing; repeat sessions without resource leaks.
- Tutor audio does not feed back into learner capture.

Gate: guided practice remains useful without speech permission, and essential listening works offline. Speaking/recording metrics are labeled according to actual measurement.

## Milestone 6 — Secure backend and text AI vertical slice

**Indicative effort:** 7–10 days plus model review. Dependencies: M2 and privacy contract; integrate UI after M5.

Deliver:
- Runnable server project: package.json, lockfile, tsconfig, tested scripts, .env.example with names only, migrations, setup/deployment docs.
- Server-verified Sign in with Apple, revocable sessions, token refresh and Keychain integration.
- Conversation start/turn/finish/delete, authoritative usage endpoint, approved provider adapter.
- Validated tutor response schema and bilingual prompt versions.
- Request ownership checks, bounded inputs/history, rate limits, timeouts, quota reservation/reconciliation, durable idempotency.
- Guided fallback, explicit disclosure/consent, loading/cancel/retry/error UI.
- Server tests and CI; API documentation and operational kill switch.

Do not add multiple production providers before one is secure and evaluated. Validate the exact production model ID instead of hard-coding a marketing name. Keep provider secrets server-side.

Required tests:
- Cross-account access denied, expired/revoked tokens, invalid Apple identity assertions.
- Malformed model JSON, overlong output, wrong-language output, refusal/provider failure.
- Concurrent duplicate turns, mismatched conversation version, quota races and depleted balance.
- Timeout before/after provider acceptance and unknown billing outcome.
- Attempted prompt injection cannot grant access or override server configuration.
- Logs do not contain secrets or routine learner conversations.

Gate: a real authorized learner gets validated bilingual feedback through the backend; a duplicate retry does not create a second accepted turn or uncontrolled spend. Server disablement returns the app to guided practice safely.

## Milestone 7 — Language quality and content completion

**Indicative effort:** 1–2 weeks, overlapping M3–M6. Dependency: reviewed schema; AI evaluation needs M6 candidate implementation.

Deliver:
- Complete 20 lessons, 10 scenarios, vocabulary/examples and licensed audio.
- At least 100 text evaluation cases for the launch teaching pair.
- Native/proficient teacher review, with two reviewers on a shared subset and adjudication.
- Versioned candidate results: correction precision, semantic preservation, explanation-language adherence, naturalness, schema validity, latency, and billed cost.
- Separate voice/transcription test corpus using consented/licensed samples.

Proposed launch gates, fixed before running final evaluation:
- All shipped content receives editorial sign-off; no unresolved known critical teaching errors.
- ≥95% correct/meaning-preserving feedback on adjudicated applicable cases.
- ≥98% target/explanation-language adherence on applicable cases.
- 100% output validation before rendering; invalid outputs always have safe fallback.
- Median reviewer naturalness/usefulness ≥4/5; no unresolved critical safety issue.
- Proposed p95 text-turn completion ≤10 seconds on a documented normal-network/device test profile; otherwise reduce scope or disclose a measured alternative target before release.
- Cost per completed session fits the approved quota/margin budget.

These are product acceptance targets, not measured results or evidence of broad teaching effectiveness. Small datasets do not establish universal reliability. Expand evaluation for every new language pair and rerun after model/prompt changes.

## Milestone 8 — Account sync and privacy completion

**Indicative effort:** 5–8 days. Dependency: M4 and M6.

Deliver:
- Push/pull event synchronization with mutation IDs, cursoring, deterministic reconciliation, tombstones.
- Confirmed guest-to-account migration and account-isolated local data.
- Authenticated export/deletion, session revocation, context expiry jobs, documented backup retention.
- Account UI, sync status, retry, sign-out and account switching.

Required tests:
- Two devices offline, duplicate uploads, out-of-order reviews, interrupted sync.
- Guest migration retry, user cancellation, account switch, stale-device restore.
- Deleted records stay deleted; deletion cancels/restricts future requests.
- Export and deletion cannot access another account's data.
- Retention jobs are observable and provider retention matches disclosures.

Gate: progress converges deterministically across two devices, and deletion does not resurrect data. Any legally required retained records are minimized and explained.

## Milestone 9 — Subscriptions and cost controls

**Indicative effort:** 4–7 days. Dependency: evaluated AI (M7), identity/backend (M6), privacy completion (M8).

Deliver:
- StoreKit products, localized pricing, purchase/restore UI, subscription management entry.
- Server verification, transaction/account association policy, verified server notifications and entitlement reconciliation.
- Clearly described metered quota and offline entitlement behavior.
- Refund/revocation handling and real budget alerts/hard limits.

Required tests with StoreKit configuration and sandbox as appropriate:
- Successful, pending, cancelled, failed, unverified, expired, refunded and revoked purchase states.
- Restore on another device; purchase/account mismatch policy; duplicate server notifications.
- Offline launch, stale client entitlement, concurrent paid requests, downgrade and quota renewal.

Gate: client-side flags cannot unlock server spending. No chargeable tier advertises unavailable value. Review current Apple requirements and storefront-specific rules before release.

## Milestone 10 — Beta, accessibility, and App Store release

**Indicative effort:** 1–2 weeks plus review time. Dependencies: all included launch milestones.

Deliver:
- TestFlight beta, feedback/crash process, store listing, original screenshots, support URL, privacy policy.
- Accurate App Privacy answers, audited privacy manifest/required-reason APIs and SDKs.
- Accessibility/device matrix, migrations/recovery tests, security and operational review.
- Release build, versioned content/model configuration, rollback instructions, incident owner.
- App Review notes with working reviewer access if required and explanation of AI behavior/billing.

Release checklist:
- [ ] CI passes for iOS, server, content validation, and pure-domain tests.
- [ ] Clean installation and upgrade from previous beta preserve expected data.
- [ ] Offline lesson/review/guided path works without account.
- [ ] All declared language pairs/capabilities match the shipped product.
- [ ] Permission denial, network loss, server failure, quota exhaustion, and AI kill switch tested.
- [ ] VoiceOver, Dynamic Type, Reduce Motion, contrast, and small-screen layouts reviewed.
- [ ] Export, deletion, sign-out, restore purchases, expiry and revocation verified.
- [ ] No secrets, test statistics, unlicensed assets, debug bypasses, or placeholder purchase flows.
- [ ] Cost limits, retention jobs, alerts, database backups and restore drill verified.
- [ ] Privacy/age/marketing claims checked against actual behavior.
- [ ] Store screenshots show real functionality; no unsupported fluency or pronunciation claims.

Gate: release only the capabilities that passed their gates. A local-only edition must explicitly omit paid AI/sync claims. Keep a known-issues register and block critical data loss, security, billing, or teaching defects.

## Test ownership and execution

- Pure Swift domain tests: grading, unlocks, scheduler, streak, timer, event reconciliation. Run independently of Apple UI where possible, plus XCTest in the app test target.
- iOS integration/UI tests: SwiftData, migration, resume, navigation, accessibility, audio lifecycle.
- Server unit/integration tests: auth, ownership, schemas, ordering, idempotency, quota and subscription transitions, sync/deletion.
- Editorial evaluation: native/proficient reviewers, versioned rubrics and adjudication.
- Physical devices: microphone, routes, interruptions, actual performance and privacy prompts.

A passing Linux Foundation test does not prove SwiftUI or Apple framework compilation. A passing simulator build does not prove real-device speech behavior. Record exactly which environment each result covers.

## Scope cuts if the schedule slips

Cut in this order: mascot animation polish → advanced analytics visuals → secondary locales → additional AI providers → server voice → real-time voice. Reduce content count only by explicitly changing the release contract and store claims. Do not cut data integrity, permission fallbacks, privacy, billing verification, content review, or basic accessibility.

## Post-launch

1. Fix reliability, content mistakes, and cost regressions first.
2. Review anonymized/minimized operational signals; never log raw conversations by default for analytics.
3. Add English → Spanish/French only after the same pair-specific gates.
4. Evaluate real-time voice when turn-based practice demonstrates learner value and sustainable cost.
5. Add validated acoustic pronunciation feedback only with appropriate evidence and careful labeling.
6. Consider a more sophisticated review scheduler after sufficient data, migration design, and testing.

## Immediate first implementation slice

Start with the Xcode project and Ghost theme, then one persisted lesson on a short path. The first demonstrable result is: onboard → complete a reviewed greetings lesson → hear bundled audio → save a word → see real progress → relaunch offline with state intact. This is the foundation for the full app, not a throwaway mockup.
