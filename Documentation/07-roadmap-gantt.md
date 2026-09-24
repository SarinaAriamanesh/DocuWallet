# Roadmap and Gantt --- Digital Document Wallet

13 weeks · 23.09.2026 → defence week in December 2026\
Assumption: \~12--15 h/week for one executor; update after OQ-6.

------------------------------------------------------------------------

## Gantt

``` mermaid
gantt
    title DocuWallet — Phase 1
    dateFormat YYYY-MM-DD
    axisFormat %d %b
    excludes weekends

    section Gate 0 · Scope
    Confirm languages, doc types, reminder channel :a1, 2026-09-23, 6d
    Repo, CI, ADR log, security assumptions        :a2, 2026-09-23, 6d

    section Gate 1 · Evaluation design
    Build labelled document set                    :b1, 2026-09-28, 10d
    Define ground truth + metrics                   :b2, 2026-09-30, 6d
    Evaluation set frozen                          :milestone, m1, 2026-10-09, 0d

    section Gate 2 · Storage foundation
    Auth + DB schema + private storage              :c1, 2026-10-05, 10d
    Upload + preview + retrieval                    :c2, 2026-10-12, 7d
    Secure wallet slice working                     :milestone, m2, 2026-10-16, 0d

    section Gate 3 · AI extraction
    OCR + language handling                        :d1, 2026-10-19, 7d
    Type/date extraction + validation               :d2, 2026-10-26, 7d
    Review/confirmation UI                          :d3, 2026-10-29, 6d
    Multilingual extraction measured                :milestone, m3, 2026-11-04, 0d

    section Gate 4 · Renewal loop
    Rule catalogue + overrides                      :e1, 2026-11-02, 7d
    Scheduler + reminder occurrences                :e2, 2026-11-09, 6d
    Email/in-app dispatch + idempotency              :e3, 2026-11-09, 6d
    End-to-end renewal loop                         :milestone, crit, m4, 2026-11-13, 0d

    section Gate 5 · Product hardening
    Search/dashboard/versioning                     :f1, 2026-11-16, 7d
    Deletion/export/security tests                  :f2, 2026-11-16, 7d
    Scope freeze                                    :milestone, m5, 2026-11-20, 0d

    section Gate 6 · Evaluation
    Run multilingual evaluation                     :crit, g1, 2026-11-23, 10d
    Analyze errors + reminder tests                 :g2, 2026-12-01, 5d
    Evaluation report                               :g3, 2026-12-03, 6d

    section Close
    README, demo video, evidence pack               :h1, 2026-12-07, 5d
    Defence rehearsal + release tag                 :h2, 2026-12-11, 4d
```

------------------------------------------------------------------------

## Milestones and gates

  -----------------------------------------------------------------------
  \#                      Milestone               Gate condition
  ----------------------- ----------------------- -----------------------
  M1                      Evaluation set frozen   ≥40 labelled docs, ≥3
                                                  languages, ≥4 supported
                                                  types,
                                                  negative/ambiguous
                                                  cases included

  M2                      Secure wallet           Login → upload →
                                                  private store →
                                                  retrieve original
                                                  works; cross-user
                                                  access test passes

  M3                      Extraction measured     OCR/extraction runs on
                                                  frozen set; accuracy by
                                                  language/type is known

  M4                      End-to-end renewal loop Upload → extract →
                                                  verify → rule →
                                                  occurrence → real
                                                  reminder

  M5                      Scope freeze            No new major features;
                                                  only defects/evaluation
                                                  blockers

  M6                      Defence-ready           Tests green, evaluation
                                                  report complete, demo
                                                  rehearsed
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## Weekly breakdown

  Week   Focus                Deliverable
  ------ -------------------- -----------------------------------------------------
  1      Scope decisions      Languages, types, channels, repo, ADRs
  2      Dataset              Labelled evaluation set + ground-truth schema
  3      Foundation           Auth, schema, private storage
  4      Wallet               Upload, preview, retrieval, owner isolation
  5      OCR                  Multilingual OCR + language metadata
  6      Extraction           Type/date extraction + validation + manual fallback
  7      Verification/rules   Confirm/correct UI + rule precedence
  8      Reminders            Scheduling, idempotent delivery; **M4**
  9      Product              Search, due dashboard, version chain
  10     Hardening            Deletion/export/security; scope freeze
  11     Evaluation           Frozen-set run, reminder matrix, usability tasks
  12     Analysis             Error analysis, report, screenshots, README
  13     Defence              Demo, evidence pack, release tag

------------------------------------------------------------------------

## Buffer and what to cut

If schedule slips, cut in this order:

1.  calendar/timeline visualization;
2.  multi-file upload;
3.  saved personal rule defaults;
4.  account-level ZIP export;
5.  rich source-region highlighting;
6.  in-app reminder channel if email is already complete.

**Never cut:** private storage, multilingual extraction evaluation, user
verification gate, configurable renewal lead time, correct/idempotent
reminders, deletion, cross-user isolation.

------------------------------------------------------------------------

## Critical path

`scope → labelled dataset → secure upload → OCR → expiry extraction → verification → renewal rule → scheduling → reminder → evaluation → report`
