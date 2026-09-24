# Build Guide for an AI Coding Agent --- Digital Document Wallet

Phase 1 · v1.0 · Use with `04-FRD.md`, `05-NFR.md`, `06-HLD.md` and
`08-test-plan.md`.

------------------------------------------------------------------------

## 0. Working rule

Build in small slices. The coding agent must not reinterpret the project
into a generic file manager or an autonomous AI system.

**Standing instruction:**

> Implement only the named slice. Read the relevant FRD/NFR/HLD sections
> first. AI-extracted fields are candidates until explicitly verified.
> Do not schedule reminders from unverified data. Do not expose
> originals publicly. Keep OCR, extraction and notification providers
> behind interfaces. End every slice with tests, visible failure
> handling, and an ADR when an architectural decision is made.

------------------------------------------------------------------------

## 1. Repository layout

``` text
docuwallet/
├── docs/
│   ├── 00-README.md
│   ├── 01-business-strategy.md
│   ├── 02-project-passport.md
│   ├── 03-BRD.md
│   ├── 04-FRD.md
│   ├── 05-NFR.md
│   ├── 06-HLD.md
│   ├── 07-roadmap-gantt.md
│   ├── 08-test-plan.md
│   ├── 09-agent-build-guide.md
│   ├── adr/
│   └── evaluation/
├── config/
│   ├── document-types.json
│   └── renewal-rules.json
├── prisma/schema.prisma
├── src/
│   ├── app/
│   ├── domain/
│   │   ├── documents/
│   │   ├── verification/
│   │   ├── renewal-rules/
│   │   ├── scheduling/
│   │   └── versioning/
│   ├── ports/
│   │   ├── ocr/
│   │   ├── extraction/
│   │   ├── notification/
│   │   └── storage/
│   ├── jobs/
│   ├── db/
│   └── lib/
├── tests/
│   ├── unit/
│   ├── integration/
│   ├── e2e/
│   └── fixtures/
└── .github/workflows/ci.yml
```

`src/domain/` must remain framework/provider independent.

------------------------------------------------------------------------

## 2. Build slices

  ---------------------------------------------------------------------------------
  \#                Slice              Requirements      Exit condition
  ----------------- ------------------ ----------------- --------------------------
  S0                Skeleton + CI      N6.4              App runs, one test green

  S1                Schema + owner     F1, HLD §5        T-01 passes
                    isolation                            

  S2                Private object     F2                T-02/T-03 pass
                    storage                              

  S3                Wallet list/detail F10, F11.1        Uploaded file retrievable
                                                         only by owner

  S4                Job queue + status F14               Failure/retry visible
                    UI                                   

  S5                OCR port +         F3                OCR result stored; T-04
                    language                             passes

  S6                Extraction port +  F4/F5             Type/date candidates
                    schema                               stored; T-05 passes

  S7                Verification UI    F6                T-06/T-07 pass

  S8                Rule catalogue +   F7                T-08 passes
                    precedence                           

  S9                Schedule           F8                T-09 passes
                    calculator                           

  S10               Notification       F9                T-10 passes; real test
                    port + dispatch                      email received

  S11               Dashboard/search   F10               Due/expired/needs-review
                                                         filters work

  S12               Renewal version    F11               T-11 passes
                    chain                                

  S13               Delete/export      F12               T-12 passes

  S14               Evaluation         F13               Frozen-set CSV generated
                    instrumentation                      

  S15               Hardening          N3/N4/N6/N8       CI/security/evidence pack
                                                         green
  ---------------------------------------------------------------------------------

------------------------------------------------------------------------

## 3. Per-slice prompt template

``` text
Slice: S<N> — <name>

Read:
- docs/06-HLD.md sections <...>
- docs/04-FRD.md requirements <...>
- docs/05-NFR.md requirements <...>
- docs/08-test-plan.md tests <...>

Implement exactly the named requirements.

Non-negotiable constraints:
- AI fields remain unverified until explicit user confirmation.
- No reminder may be scheduled from an unverified expiry/rule.
- Original files are private and owner-scoped.
- src/domain has no framework/provider imports.
- OCR, extraction, notification and storage use ports/interfaces.
- Verified values cannot be silently overwritten by reprocessing.
- Do not put document numbers or OCR text in logs/notifications.
- Do not add dependencies outside the HLD stack without explaining why.

Deliver:
1. implementation;
2. tests;
3. migration/config changes;
4. ADR if needed;
5. README/setup change if needed;
6. explicit list of anything not implemented.
```

------------------------------------------------------------------------

## 4. Common agent failure modes

  ------------------------------------------------------------------------
  Trap                    Wrong implementation    Required implementation
  ----------------------- ----------------------- ------------------------
  AI trust                Save extracted expiry   Save candidate; require
                          as final                verification

  Missing date            Model invents a         `null` + needs-review
                          plausible expiry        

  Date ambiguity          Parses `03/04/27`       Flag ambiguity or use
                          silently                contextual evidence and
                                                  still require review

  Rules                   Hardcodes passport =    Config/rule table with
                          180 days in scheduler   provenance and override

  Reprocessing            Replaces verified       Preserve verified value;
                          expiry                  store new candidate
                                                  separately

  Files                   Public bucket URL       Private storage + short
                                                  signed URL

  Notifications           Includes full document  Minimal
                          number                  label/status/link only

  Idempotency             Retry sends twice       Unique
                                                  occurrence/idempotency
                                                  key + provider result

  Versioning              Overwrites old passport New version linked to
                          scan                    old

  Multilingual claim      Says "supports all      Declare tested languages
                          languages"              and report metrics

  Evaluation              Tunes on the same       Freeze labelled
                          examples repeatedly     evaluation set before
                                                  final tuning
  ------------------------------------------------------------------------

------------------------------------------------------------------------

## 5. Definition of done

-   [ ] T-01...T-12 pass.
-   [ ] Private file access and cross-user isolation verified.
-   [ ] ≥40-document frozen multilingual evaluation completed.
-   [ ] Expiry-date exact-match result calculated overall and per
    language.
-   [ ] No-expiry negative set produces no fabricated verified dates.
-   [ ] Every active reminder is based on a verified expiry and
    confirmed rule.
-   [ ] Reminder schedule matrix passes 100%.
-   [ ] Notification retry produces zero duplicates.
-   [ ] New document type/rule can be added without editing scheduler
    code.
-   [ ] OCR/extraction/notification providers each have a real
    implementation and stub.
-   [ ] Deletion and export demonstrated.
-   [ ] README reproduces local setup in ≤15 minutes.
-   [ ] ≥6 ADRs exist.
-   [ ] Student can explain every core domain module.

------------------------------------------------------------------------

## 6. Minimal survival build

If the schedule collapses, build only:

**login → upload one document → private store → OCR/extract expiry →
user confirms → choose lead time → schedule → send one reminder →
retrieve document later**

Keep multilingual evaluation and the verification gate. Cut visual
polish, calendar view, bulk upload and advanced export before cutting
the core renewal loop.
