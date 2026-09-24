# Functional Requirements --- Digital Document Wallet

Phase 1 · v1.0 · Traces to `03-BRD.md` §5.1

**MUST** = required for Phase 1. **SHOULD** = implement if schedule
allows. **WON'T** = explicitly deferred.

------------------------------------------------------------------------

## Actors

  -----------------------------------------------------------------------
  Actor                               Description
  ----------------------------------- -----------------------------------
  User                                Authenticated owner of the wallet
                                      and its documents

  System                              Background OCR/extraction,
                                      scheduling, reminders, retention
                                      and metrics

  Admin                               Operational role only; cannot
                                      casually browse document contents
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## F1 --- Authentication and account

  -----------------------------------------------------------------------
  ID                      Priority                Requirement
  ----------------------- ----------------------- -----------------------
  F1.1                    MUST                    User can authenticate
                                                  securely by email magic
                                                  link or equivalent
                                                  passwordless method.

  F1.2                    MUST                    Every document query is
                                                  scoped to the
                                                  authenticated owner.

  F1.3                    MUST                    Session/token expiry
                                                  and logout are
                                                  supported.

  F1.4                    MUST                    Account deletion
                                                  initiates deletion of
                                                  owned files, derived
                                                  data and pending
                                                  reminders.

  F1.5                    WON'T                   Organization roles and
                                                  team invitations.
  -----------------------------------------------------------------------

**AC F1.2** --- Given users A and B, when B requests A's document
URL/ID, then access is denied and no metadata is leaked.

------------------------------------------------------------------------

## F2 --- Document upload and capture

  -----------------------------------------------------------------------
  ID                      Priority                Requirement
  ----------------------- ----------------------- -----------------------
  F2.1                    MUST                    Accept JPG, JPEG, PNG
                                                  and PDF.

  F2.2                    MUST                    Validate MIME type,
                                                  file signature and
                                                  configured size/page
                                                  limits.

  F2.3                    MUST                    Preserve the original
                                                  file byte-for-byte.

  F2.4                    MUST                    User can upload from
                                                  file picker; camera
                                                  capture is supported
                                                  where browser permits.

  F2.5                    MUST                    Upload creates a
                                                  processing job and
                                                  visible status.

  F2.6                    SHOULD                  Multi-file upload.

  F2.7                    WON'T                   Office document formats
                                                  in Phase 1.
  -----------------------------------------------------------------------

**AC F2.2** --- Given an executable renamed `.pdf`, when uploaded, then
it is rejected before OCR/storage processing.

------------------------------------------------------------------------

## F3 --- OCR and language handling

  -----------------------------------------------------------------------
  ID                      Priority                Requirement
  ----------------------- ----------------------- -----------------------
  F3.1                    MUST                    OCR runs server-side
                                                  through an
                                                  `OcrProvider`
                                                  interface.

  F3.2                    MUST                    System stores raw OCR
                                                  text separately from
                                                  normalized metadata.

  F3.3                    MUST                    System records detected
                                                  language/script and
                                                  allows user correction.

  F3.4                    MUST                    Phase 1 evaluation
                                                  includes at least three
                                                  declared languages.

  F3.5                    MUST                    OCR failure produces a
                                                  visible
                                                  retry/manual-entry
                                                  path, never an empty
                                                  successful result.

  F3.6                    SHOULD                  Preserve page/region
                                                  references for
                                                  extracted fields.
  -----------------------------------------------------------------------

**AC F3.5** --- Given OCR provider failure, then document status becomes
`ocr_failed`, no extracted expiry is marked verified, and the user can
retry or enter metadata manually.

------------------------------------------------------------------------

## F4 --- Document classification

  -----------------------------------------------------------------------
  ID                      Priority                Requirement
  ----------------------- ----------------------- -----------------------
  F4.1                    MUST                    System proposes a
                                                  document type from a
                                                  configurable supported
                                                  catalogue.

  F4.2                    MUST                    `unknown/other` is a
                                                  valid result.

  F4.3                    MUST                    User can correct the
                                                  proposed type.

  F4.4                    MUST                    Type choice controls
                                                  which default renewal
                                                  rules and expected
                                                  fields are offered.

  F4.5                    WON'T                   Unlimited ontology or
                                                  legal document
                                                  interpretation.
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## F5 --- Metadata and expiry extraction

  -----------------------------------------------------------------------
  ID                      Priority                Requirement
  ----------------------- ----------------------- -----------------------
  F5.1                    MUST                    Extraction runs through
                                                  a `DocumentExtractor`
                                                  interface and returns
                                                  structured
                                                  schema-validated
                                                  output.

  F5.2                    MUST                    Candidate fields
                                                  include `issue_date`,
                                                  `expiry_date`,
                                                  `document_number` when
                                                  applicable,
                                                  `issuing_authority`
                                                  when available, and
                                                  optional holder/display
                                                  name.

  F5.3                    MUST                    Dates are stored as
                                                  normalized ISO dates
                                                  plus original raw text.

  F5.4                    MUST                    Every extracted field
                                                  records provenance and
                                                  confidence when the
                                                  provider supplies it.

  F5.5                    MUST                    Ambiguous date formats
                                                  are flagged for review.

  F5.6                    MUST                    A missing expiry date
                                                  is represented as
                                                  `null`, never invented.

  F5.7                    MUST                    Invalid structured
                                                  output fails the
                                                  extraction job rather
                                                  than persisting partial
                                                  trusted metadata.

  F5.8                    SHOULD                  UI highlights the
                                                  source text/region used
                                                  for the expiry
                                                  candidate.
  -----------------------------------------------------------------------

**AC F5.6** --- Given a document with no expiry date, when extraction
completes, then `expiry_date=null`, status requires manual review, and
no reminder is scheduled.

------------------------------------------------------------------------

## F6 --- Verification and correction

  ------------------------------------------------------------------------
  ID                      Priority                Requirement
  ----------------------- ----------------------- ------------------------
  F6.1                    MUST                    User sees extracted
                                                  values before
                                                  activation.

  F6.2                    MUST                    User can edit document
                                                  type, issue date, expiry
                                                  date and renewal lead
                                                  time.

  F6.3                    MUST                    Reminder scheduling is
                                                  blocked until expiry
                                                  date and renewal rule
                                                  are explicitly
                                                  confirmed.

  F6.4                    MUST                    Confirmation stores user
                                                  ID, timestamp, confirmed
                                                  value and original
                                                  candidate.

  F6.5                    MUST                    User-entered/confirmed
                                                  value becomes
                                                  authoritative without
                                                  deleting extraction
                                                  provenance.

  F6.6                    MUST                    Re-processing never
                                                  silently overwrites a
                                                  verified field.
  ------------------------------------------------------------------------

**AC F6.3** --- Given an AI candidate expiry date exists but is
unconfirmed, when scheduler runs, then zero reminder occurrences exist.

------------------------------------------------------------------------

## F7 --- Renewal rules

  -----------------------------------------------------------------------
  ID                      Priority                Requirement
  ----------------------- ----------------------- -----------------------
  F7.1                    MUST                    A rule can define one
                                                  or more lead times,
                                                  e.g. 90, 30 and 7 days
                                                  before expiry.

  F7.2                    MUST                    Rule catalogue can
                                                  provide defaults by
                                                  document type and
                                                  optional jurisdiction.

  F7.3                    MUST                    User can override
                                                  defaults for a specific
                                                  document.

  F7.4                    MUST                    User can add a custom
                                                  renewal-start date when
                                                  the rule cannot be
                                                  represented by simple
                                                  lead days.

  F7.5                    MUST                    The UI labels catalogue
                                                  rules as
                                                  defaults/suggestions,
                                                  not legal guarantees.

  F7.6                    MUST                    Rule changes regenerate
                                                  only future reminder
                                                  occurrences.

  F7.7                    SHOULD                  User can save a custom
                                                  rule as their personal
                                                  default for that type.
  -----------------------------------------------------------------------

**AC F7.3** --- Given a passport default of 180 days and a user override
of 120 days, then the document uses 120 days and retains 180 only as
catalogue provenance.

------------------------------------------------------------------------

## F8 --- Reminder scheduling

  -----------------------------------------------------------------------
  ID                      Priority                Requirement
  ----------------------- ----------------------- -----------------------
  F8.1                    MUST                    Scheduler creates
                                                  reminder occurrences
                                                  from the verified
                                                  expiry date and
                                                  confirmed rule.

  F8.2                    MUST                    Occurrences use the
                                                  user's timezone.

  F8.3                    MUST                    No occurrence is
                                                  scheduled after expiry
                                                  unless explicitly
                                                  configured as overdue
                                                  notification.

  F8.4                    MUST                    Updating expiry/rule
                                                  cancels superseded
                                                  future occurrences and
                                                  creates new ones.

  F8.5                    MUST                    Each logical occurrence
                                                  has a unique
                                                  idempotency key.

  F8.6                    MUST                    Past occurrences remain
                                                  in history.
  -----------------------------------------------------------------------

**AC F8.1** --- Given expiry `2027-06-30` and confirmed lead times
90/30/7 days, then exactly three pre-expiry occurrences are generated on
the mathematically correct dates in the user's timezone.

------------------------------------------------------------------------

## F9 --- Reminder delivery

  -----------------------------------------------------------------------
  ID                      Priority                Requirement
  ----------------------- ----------------------- -----------------------
  F9.1                    MUST                    Due occurrences are
                                                  delivered through a
                                                  `Notifier` interface.

  F9.2                    MUST                    Phase 1 supports email
                                                  and/or in-app according
                                                  to OQ-3.

  F9.3                    MUST                    Notification contains
                                                  minimal content:
                                                  document display
                                                  label/type, due status
                                                  and secure application
                                                  link; never full
                                                  document number or OCR
                                                  text.

  F9.4                    MUST                    Delivery is idempotent
                                                  under retry.

  F9.5                    MUST                    Sent/failed/bounced
                                                  status and provider
                                                  message ID are
                                                  recorded.

  F9.6                    MUST                    Failed sends retry with
                                                  backoff and become
                                                  visible to the user
                                                  after terminal failure.

  F9.7                    SHOULD                  User can snooze a
                                                  reminder without
                                                  changing the
                                                  authoritative expiry
                                                  date.
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## F10 --- Wallet, search and dashboard

  -----------------------------------------------------------------------
  ID                      Priority                Requirement
  ----------------------- ----------------------- -----------------------
  F10.1                   MUST                    List documents with
                                                  display name/type,
                                                  status and expiry date.

  F10.2                   MUST                    Filter by document type
                                                  and status: valid, due
                                                  soon, expired, needs
                                                  review.

  F10.3                   MUST                    Search by user-visible
                                                  label and selected
                                                  metadata.

  F10.4                   MUST                    Dashboard shows next
                                                  expiries ordered by
                                                  renewal urgency.

  F10.5                   MUST                    Needs-review documents
                                                  are visually separated
                                                  from verified
                                                  documents.

  F10.6                   SHOULD                  Calendar/timeline view.
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## F11 --- Document details and versions

  -------------------------------------------------------------------------
  ID                      Priority                Requirement
  ----------------------- ----------------------- -------------------------
  F11.1                   MUST                    User can view
                                                  original/preview, OCR
                                                  text, extracted fields,
                                                  verification state and
                                                  reminder schedule.

  F11.2                   MUST                    User can upload a
                                                  renewed/replacement
                                                  document as a new version
                                                  linked to the previous
                                                  document.

  F11.3                   MUST                    Previous versions remain
                                                  read-only until
                                                  explicitly deleted.

  F11.4                   MUST                    New version runs through
                                                  extraction/verification
                                                  independently.

  F11.5                   MUST                    Marking a new version
                                                  active archives the
                                                  previous version's future
                                                  reminders.
  -------------------------------------------------------------------------

------------------------------------------------------------------------

## F12 --- Deletion and export

  -----------------------------------------------------------------------
  ID                      Priority                Requirement
  ----------------------- ----------------------- -----------------------
  F12.1                   MUST                    Delete operation
                                                  removes original,
                                                  previews, OCR text,
                                                  extracted fields and
                                                  future reminders for
                                                  the selected
                                                  document/version.

  F12.2                   MUST                    Destructive deletion
                                                  requires typed
                                                  confirmation.

  F12.3                   MUST                    Partial deletion enters
                                                  `deletion_pending` and
                                                  retries.

  F12.4                   MUST                    User can export their
                                                  originals plus
                                                  structured metadata.

  F12.5                   SHOULD                  Account-level export
                                                  ZIP/JSON.
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## F13 --- Metrics and evaluation support

  -----------------------------------------------------------------------
  ID                      Priority                Requirement
  ----------------------- ----------------------- -----------------------
  F13.1                   MUST                    Record upload-to-result
                                                  latency, OCR
                                                  provider/model,
                                                  extractor model,
                                                  detected language,
                                                  proposed type,
                                                  correction flags and
                                                  confirmation time.

  F13.2                   MUST                    Evaluation mode allows
                                                  ground-truth fields to
                                                  be attached to test
                                                  documents without
                                                  exposing them to
                                                  extraction prompts.

  F13.3                   MUST                    Export evaluation CSV
                                                  with prediction, ground
                                                  truth, correctness and
                                                  latency.

  F13.4                   MUST                    Reminder test mode can
                                                  use
                                                  accelerated/simulated
                                                  time without changing
                                                  production scheduling
                                                  code.

  F13.5                   SHOULD                  Dashboard shows
                                                  aggregate accuracy by
                                                  language and document
                                                  type.
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## F14 --- Operational behavior

  -----------------------------------------------------------------------------------
  ID                      Priority                Requirement
  ----------------------- ----------------------- -----------------------------------
  F14.1                   MUST                    OCR, extraction, preview
                                                  generation, notification and
                                                  deletion are queued jobs.

  F14.2                   MUST                    Jobs have
                                                  `queued/running/succeeded/failed`
                                                  states, retry count and visible
                                                  failure.

  F14.3                   MUST                    Provider credentials remain
                                                  server-side.

  F14.4                   MUST                    Provider outage degrades the
                                                  affected feature without making
                                                  stored documents inaccessible.

  F14.5                   MUST                    Processing is idempotent: retry
                                                  does not create duplicate field
                                                  sets or reminders.
  -----------------------------------------------------------------------------------

------------------------------------------------------------------------

## Traceability

  BRD     FRD
  ------- ---------
  FR-01   F1
  FR-02   F2
  FR-03   F2, F11
  FR-04   F3
  FR-05   F3
  FR-06   F4
  FR-07   F5
  FR-08   F6
  FR-09   F7
  FR-10   F7
  FR-11   F8
  FR-12   F9
  FR-13   F10
  FR-14   F10
  FR-15   F11
  FR-16   F12
  FR-17   F12
  FR-18   F13
