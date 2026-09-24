# BRD --- Digital Document Wallet (DocuWallet)

Business Requirements Document · Phase 1 · v1.0 · English

------------------------------------------------------------------------

# 1. General Project Information

## 1.1 Document Purpose

This BRD defines the business problem, goals, scope, stakeholders,
risks, high-level requirements, solution options and implementation
boundaries for Phase 1.

## 1.2 Project Information

  -----------------------------------------------------------------------
  Field                               Value
  ----------------------------------- -----------------------------------
  Project Name                        Digital Document Wallet --- Phase
                                      1: Store, Understand and Renew

  Working Name                        DocuWallet

  Initiator                           Student author

  Customer                            Individual document owner /
                                      academic pilot user

  Sponsor                             Self-funded / university course

  Document Author                     Student author

  Creation Date                       23.09.2026
  -----------------------------------------------------------------------

## 1.3 Change History

  -----------------------------------------------------------------------
  Version                 Date                    Changes
  ----------------------- ----------------------- -----------------------
  1.0                     23.09.2026              Initial
                                                  project-specific
                                                  version derived from
                                                  course documentation
                                                  structure

  -----------------------------------------------------------------------

## 1.4 Related Documents

  Document                      Path
  ----------------------------- ---------------------------
  Business Strategy             `01-business-strategy.md`
  Project Passport              `02-project-passport.md`
  Functional Requirements       `04-FRD.md`
  Non-Functional Requirements   `05-NFR.md`
  High-Level Design             `06-HLD.md`
  Roadmap / Gantt               `07-roadmap-gantt.md`
  Test Plan                     `08-test-plan.md`
  Build Guide                   `09-agent-build-guide.md`

## 1.5 Approval Status

  -----------------------------------------------------------------------
  Approver          Role              Status            Comment
  ----------------- ----------------- ----------------- -----------------
  `[TBD]`           Course supervisor Pending           Confirms scope
                                                        and evaluation
                                                        method

  `[TBD]`           Pilot user        Pending           Confirms AS-IS
                                                        workflow and
                                                        usability
  -----------------------------------------------------------------------

## 1.6 Glossary

  -----------------------------------------------------------------------
  Term                                Meaning
  ----------------------------------- -----------------------------------
  OCR                                 Optical Character Recognition

  VLM                                 Vision-Language Model

  Extraction                          Converting document content into
                                      structured fields

  Ground truth                        Human-labelled correct value used
                                      for evaluation

  Expiry date                         Date after which the document is no
                                      longer valid

  Renewal lead time                   How long before expiry the user
                                      should start/receive renewal
                                      reminders

  Rule catalogue                      Configurable defaults by document
                                      type/jurisdiction

  Verified field                      Extracted or manually entered value
                                      explicitly confirmed by the user

  Provenance                          Where a value came from: OCR, AI,
                                      user entry or rule catalogue

  Document version                    One issued instance in a chain of
                                      renewed/replaced documents

  Minimal notification                Reminder that avoids sensitive
                                      document identifiers/content
  -----------------------------------------------------------------------

------------------------------------------------------------------------

# 2. Executive Summary

People keep important documents across physical folders, phone
galleries, email and cloud drives. Even when a scan is stored safely,
the user must still remember when it expires and how early renewal
should begin. This becomes harder when documents use different languages
and when different document types have different renewal lead times.

DocuWallet provides a secure document wallet that accepts images/PDFs,
extracts multilingual text and candidate metadata, identifies expiry
dates, asks the user to verify the result, applies a configurable
renewal rule, and sends reminders before the renewal window.

The project deliberately does **not** trust AI enough to schedule from
an unverified date. Human confirmation is the control that turns
probabilistic extraction into deterministic scheduling.

**Target outcome:** ≥90% exact expiry-date extraction accuracy on a
labelled multilingual evaluation set, followed by 100% correct
scheduling for confirmed data and zero duplicate reminder sends.

------------------------------------------------------------------------

# 3. Business Goals and Scope

## 3.1 Strategic Goals

1.  Convert a semester project into a reusable secure document-lifecycle
    product.
2.  Demonstrate a measurable multilingual AI capability rather than an
    unverified demo.
3.  Build an architecture that can later support family accounts,
    institutions and additional notification channels.
4.  Keep Phase 1 narrow enough to complete and evaluate.

## 3.2 Project Goals

  ---------------------------------------------------------------------------
  ID                Goal              Baseline           Target
  ----------------- ----------------- ------------------ --------------------
  G-01              Centralize        Scattered/manual   100% of evaluation
                    expiring                             docs
                    documents                            stored/retrievable

  G-02              Reduce manual     Fully manual       Automatic candidate
                    metadata entry                       extraction for
                                                         supported docs

  G-03              Extract expiry    0 automated        ≥90% exact-match
                    dates accurately                     overall

  G-04              Prevent unsafe AI n/a                100% reminders based
                    automation                           on verified
                                                         dates/rules

  G-05              Automate renewal  Manual             100% correct
                    timing            memory/calendar    schedule after
                                                         confirmation

  G-06              Support           Manual             ≥3 evaluated
                    multilingual      interpretation     languages in Phase 1
                    documents                            

  G-07              Preserve renewal  Manual replacement Version chain
                    history                              retained
  ---------------------------------------------------------------------------

## 3.3 Success Criteria

See Project Passport §2.3. The release is successful only if storage,
extraction, verification and reminders work end-to-end.

## 3.4 Project Boundaries

### In Scope

Authentication; document upload; encrypted storage; OCR; language
detection; supported-type classification; metadata extraction;
expiry/issue date normalization; confidence/provenance; user
correction/confirmation; rule catalogue; custom lead time; reminder
scheduling; email/in-app reminders; due dashboard; search/filter;
document version chain; delete/export; metrics.

### Out of Scope

Government integrations; automatic renewal submission;
authenticity/forgery detection; e-signature; payment; legal advice; full
enterprise DMS; public sharing; biometric verification; unlimited
handwritten-document support; model training on user documents.

## 3.5 Business Risks

  --------------------------------------------------------------------------------------------------
  ID           Risk                  Probability           Impact Mitigation
  ------------ ---------------- ---------------- ---------------- ----------------------------------
  BR-01        Wrong expiry               Medium             High Mandatory user confirmation;
               extraction                                         confidence display

  BR-02        Multilingual OCR             High      Medium/High Fixed evaluation language set;
               inconsistent                                       provider abstraction

  BR-03        Sensitive-data         Low/Medium        Very High Encryption, access control,
               exposure                                           minimal notifications, deletion

  BR-04        Scope expands to           Medium             High Scope freeze; defer
               general DMS                                        sharing/e-signature/integrations

  BR-05        Renewal rule is            Medium             High Treat defaults as suggestions;
               wrong for                                          user confirms/overrides
               jurisdiction                                       

  BR-06        Notification               Medium           Medium Retries, idempotency, delivery log
               provider failure                                   

  BR-07        AI/provider cost           Medium           Medium File/page limits, provider
               grows                                              abstraction, cost metrics

  BR-08        Evaluation set             Medium           Medium Multiple layouts, languages, image
               too easy                                           qualities, negative cases
  --------------------------------------------------------------------------------------------------

## 3.6 Constraints and Dependencies

### Constraints

C1--C7 from Project Passport apply. In addition, sensitive values must
not appear in logs and raw OCR/AI outputs must be access-controlled.

### Dependencies

-   OCR/VLM or document-AI provider
-   Database and object storage
-   Email provider
-   Scheduler/job queue
-   Browser camera/file APIs
-   A labelled multilingual evaluation set

------------------------------------------------------------------------

# 4. Source Data

## 4.1 Project History

The idea begins from a practical problem: a user owns many documents and
images, some with expiry dates and different renewal requirements. The
system should store them, understand the date automatically using AI,
support multiple languages, and remind the owner early enough to renew
them.

## 4.2 Current State (AS-IS)

### Problems and negative impact

-   Files are distributed across multiple places.
-   Expiry dates are remembered manually.
-   Calendar events require duplicate manual entry.
-   The user may not know the correct advance-renewal period.
-   Foreign-language documents increase manual effort.
-   Replacing an expired scan can destroy history.
-   Search is based on filenames rather than document meaning.

### Tools currently used

Paper folders, phone gallery, file system/cloud drive, email
attachments, calendar/reminder apps, manual notes.

### AS-IS process

``` mermaid
flowchart LR
    A[Receive document] --> B[Store paper/photo/PDF somewhere]
    B --> C[Maybe rename file]
    C --> D[Maybe manually read expiry date]
    D --> E[Maybe create calendar reminder]
    E --> F[Remember to renew]
```

### Business rules

1.  A reminder date cannot be later than the expiry date.
2.  A user-confirmed date overrides any AI value.
3.  A user-specific renewal lead time overrides a catalogue default.
4.  Unsupported/uncertain documents must be flagged, not guessed.
5.  Renewal creates a new document version; it does not overwrite
    history.

## 4.3 Stakeholders

  -----------------------------------------------------------------------
  Stakeholder             Need                    Influence
  ----------------------- ----------------------- -----------------------
  Document owner          Secure storage,         High
                          accurate dates, useful  
                          reminders               

  Student/developer       Buildable and           High
                          defensible scope        

  Course supervisor       Complete requirements,  High
                          architecture and        
                          evaluation evidence     

  Future                  Shared lifecycle        Future
  family/organization     management              
  admin                                           

  OCR/AI provider         Receives processing     External
                          requests                

  Notification provider   Delivers reminders      External
  -----------------------------------------------------------------------

------------------------------------------------------------------------

# 5. Solution Requirements

## 5.1 Key Functional Requirements

  -----------------------------------------------------------------------
  ID                                  Requirement
  ----------------------------------- -----------------------------------
  FR-01                               Authenticate the document owner

  FR-02                               Upload/capture JPG, PNG and PDF
                                      documents

  FR-03                               Store the original and generate
                                      safe previews

  FR-04                               Detect/accept document language

  FR-05                               OCR/extract raw text

  FR-06                               Classify supported document type or
                                      mark unknown

  FR-07                               Extract issue date, expiry date and
                                      selected metadata with
                                      provenance/confidence

  FR-08                               Require user review/confirmation
                                      before reminder activation

  FR-09                               Apply configurable default renewal
                                      lead-time rules

  FR-10                               Allow user-specific override of
                                      renewal timing

  FR-11                               Generate due reminder occurrences
                                      correctly

  FR-12                               Deliver reminders idempotently and
                                      record outcomes

  FR-13                               List/search/filter documents and
                                      due status

  FR-14                               Show due-soon and expired dashboard

  FR-15                               Link renewed documents into version
                                      chains

  FR-16                               Delete a document and all derived
                                      data/future reminders

  FR-17                               Export user metadata and originals

  FR-18                               Record extraction/scheduling
                                      metrics for evaluation
  -----------------------------------------------------------------------

## 5.2 Key Non-Functional Requirements

Security, privacy, multilingual quality, reliability, performance,
maintainability, accessibility, observability and student-budget
operation. Detailed in `05-NFR.md`.

## 5.3 Key Data Requirements

Core entities: User, Document, DocumentVersion, FileObject, OCRResult,
ExtractedField, RenewalRule, ReminderOccurrence, NotificationAttempt,
ProcessingJob, AuditEvent, EvaluationMetric.

Every extracted field stores: field name, raw value, normalized value,
source/provenance, confidence if available, model/provider version,
verified status, verified timestamp.

## 5.4 Key Integration Requirements

  -------------------------------------------------------------------------------
  Integration             Data sent               Purpose
  ----------------------- ----------------------- -------------------------------
  OCR/AI provider         Document bytes or page  OCR/classification/extraction
                          images                  

  Object storage          Original and preview    Persistence

  Email provider          Email + minimal         Notification
                          reminder text           

  Scheduler/queue         Internal IDs/timestamps Reliable background work
  -------------------------------------------------------------------------------

## 5.5 Migration Requirements

No legacy-system migration in Phase 1. Optional bulk upload is deferred.
Seed data is synthetic/redacted.

## 5.6 Process Change Requirements

TO-BE process:

``` mermaid
flowchart LR
    A[Upload/capture] --> B[Secure store]
    B --> C[OCR + language/type detection]
    C --> D[Extract candidate fields]
    D --> E[User verifies expiry + renewal rule]
    E --> F[Schedule reminders]
    F --> G[Renew]
    G --> H[Upload new version]
```

## 5.7 Change Management Plan

-   10-minute onboarding walkthrough.
-   Explain the "AI proposes, user verifies" rule.
-   Show due dashboard and correction flow.
-   Provide visible failure states and retry actions.
-   Collect pilot feedback after evaluation tasks.

------------------------------------------------------------------------

# 6. Solutions and Implementation

## 6.1 Solution Options

  ------------------------------------------------------------------------
  Option            Description       Advantages        Disadvantages
  ----------------- ----------------- ----------------- ------------------
  A                 Local-only        Maximum privacy   Harder
                    desktop/mobile                      deployment/sync;
                    prototype                           weaker course demo

  B                 Single web        Fastest build,    Requires careful
                    application +     accessible,       third-party
                    managed           testable          privacy controls
                    DB/storage +                        
                    pluggable AI                        
                    providers                           

  C                 Microservices +   Scalable          Excessive
                    separate OCR      architecture      complexity for
                    pipeline                            one-semester
                                                        project
  ------------------------------------------------------------------------

**Selected:** Option B.

## 6.2 Scope of First Release

MVP path: **login → upload → OCR/extract → verify → set renewal lead
time → schedule → reminder → dashboard → upload renewed version**.

## 6.3 Road Map

See `07-roadmap-gantt.md`.

## 6.4 Resource Plan

One student/developer. Optional supervisor and 1--3 pilot users for
usability/evaluation. No dedicated DevOps, QA or designer.

## 6.5 Budget Estimate

  -----------------------------------------------------------------------
  Item                                Phase 1 approach
  ----------------------------------- -----------------------------------
  Hosting                             Free/low-cost student tier

  Database                            Managed free/low-cost PostgreSQL

  Object storage                      Low-volume object storage

  OCR/AI                              Free credits or capped
                                      pay-as-you-go

  Email                               Free transactional tier

  Domain                              Optional

  Total                               Target ≤ equivalent of €25/month
                                      during development, excluding
                                      optional domain
  -----------------------------------------------------------------------

------------------------------------------------------------------------

# Appendices

## Open Questions Summary

OQ-1 through OQ-8 from `00-README.md` remain open until explicitly
resolved.
