# High-Level Design --- Digital Document Wallet

Phase 1 · v1.0 · Implements `04-FRD.md` and `05-NFR.md`

------------------------------------------------------------------------

## 1. Design principles

1.  **AI proposes; user verifies.** Probabilistic extraction never
    directly becomes an authoritative renewal date.
2.  **Originals are immutable.** Preserve the uploaded file; corrections
    change metadata, not source bytes.
3.  **Rules are configuration.** Document-type renewal lead times do not
    belong in scheduler source code.
4.  **Slow work is asynchronous.** OCR, extraction, preview generation,
    notifications and deletion are jobs.
5.  **Sensitive files are private by default.**
6.  **Provider boundaries are explicit.** OCR/extraction/notification
    can be swapped.
7.  **Version history is a feature.** Renewal creates a linked new
    version.

------------------------------------------------------------------------

## 2. Technology stack

  --------------------------------------------------------------------------
  Layer                   Choice                  Rationale
  ----------------------- ----------------------- --------------------------
  Framework               Next.js + TypeScript    One codebase for UI/API;
                                                  suitable for solo build

  UI                      Tailwind CSS +          Fast consistent interface
                          component library       

  Database                PostgreSQL              Strong relational
                                                  integrity for
                                                  versions/rules/reminders

  ORM                     Prisma                  Typed schema and readable
                                                  migrations

  Auth                    Auth.js or equivalent   Passwordless option and
                                                  server-side session
                                                  support

  Object storage          Private S3-compatible   Originals/previews,
                          storage                 encryption, signed URLs

  Queue                   Postgres-backed worker  Fewer services; persistent
                                                  jobs

  Scheduler               Platform cron + queue   Reliable due-reminder
                                                  dispatch

  OCR                     Provider behind         Swappable multilingual OCR
                          `OcrProvider`           

  Extraction              Structured-output       Schema-validated metadata
                          VLM/LLM behind          extraction
                          `DocumentExtractor`     

  Notification            Email provider behind   Delivery
                          `Notifier`              tracking/idempotency

  Validation              Zod/JSON Schema         Reject malformed AI output

  Tests                   Vitest + Playwright     Unit/integration/E2E

  Hosting                 Managed web platform    Low operational overhead
  --------------------------------------------------------------------------

No microservices are required for Phase 1.

------------------------------------------------------------------------

## 3. Context

``` mermaid
graph TB
    U[User browser/mobile browser] --> APP[DocuWallet web application]
    APP --> DB[(PostgreSQL)]
    APP --> OS[(Private object storage)]
    APP --> Q[Job queue]
    Q --> OCR[OCR provider]
    Q --> EXT[Extraction provider]
    Q --> NOTIF[Notification provider]
    CRON[Scheduler/Cron] --> APP
    NOTIF --> U
```

### Third parties receiving data

  -------------------------------------------------------------------------------------
  Party             Data              Purpose           Control
  ----------------- ----------------- ----------------- -------------------------------
  OCR provider      File/page image   Text recognition  Prefer
                                                        no-training/limited-retention
                                                        terms

  Extraction        OCR text and/or   Structured fields Minimize sent content where
  provider          page image                          possible

  Object storage    Original +        Persistence       Private bucket, encryption
                    previews                            

  Email provider    Email + minimal   Delivery          No full identifiers/OCR text
                    reminder                            
  -------------------------------------------------------------------------------------

------------------------------------------------------------------------

## 4. Component view

``` mermaid
graph LR
    subgraph Web
      UP[Upload/Capture]
      RV[Review & Verify]
      WL[Wallet/Search]
      DD[Document Detail]
      DS[Due Dashboard]
    end

    subgraph Domain
      DOC[Document Service]
      VER[Verification Service]
      RULE[Renewal Rule Engine]
      SCH[Schedule Calculator]
      VERS[Version Service]
      DEL[Deletion Service]
    end

    subgraph Ports
      OP[OcrProvider]
      EP[DocumentExtractor]
      NP[Notifier]
      SP[ObjectStorage]
    end

    subgraph Jobs
      J1[OCR]
      J2[Extract]
      J3[Preview]
      J4[Dispatch reminders]
      J5[Delete]
    end

    UP --> DOC
    RV --> VER
    WL --> DOC
    DD --> DOC
    DS --> SCH
    DOC --> RULE
    VER --> RULE
    RULE --> SCH
    DOC --> VERS
    J1 --> OP
    J2 --> EP
    J4 --> NP
    DOC --> SP
```

------------------------------------------------------------------------

## 5. Data model

``` mermaid
erDiagram
    USER ||--o{ DOCUMENT : owns
    DOCUMENT ||--o{ DOCUMENT_VERSION : versions
    DOCUMENT_VERSION ||--|| FILE_OBJECT : stores
    DOCUMENT_VERSION ||--o| OCR_RESULT : produces
    DOCUMENT_VERSION ||--o{ EXTRACTED_FIELD : has
    DOCUMENT_VERSION ||--o{ FIELD_VERIFICATION : confirms
    DOCUMENT_VERSION }o--o| RENEWAL_RULE : uses
    DOCUMENT_VERSION ||--o{ REMINDER_OCCURRENCE : schedules
    REMINDER_OCCURRENCE ||--o{ NOTIFICATION_ATTEMPT : sends
    USER ||--o{ CUSTOM_RULE : defines
    DOCUMENT_VERSION ||--o{ PROCESSING_JOB : has
    USER ||--o{ AUDIT_EVENT : produces
    DOCUMENT_VERSION ||--o{ EVALUATION_METRIC : measures
```

### Key tables

  -----------------------------------------------------------------------
  Table                               Important fields
  ----------------------------------- -----------------------------------
  `user`                              id, email, timezone, created_at

  `document`                          id, user_id, display_name,
                                      active_version_id, created_at

  `document_version`                  id, document_id,
                                      previous_version_id, type,
                                      language, status, issued_at,
                                      expires_at, verified_at

  `file_object`                       id, version_id, storage_key, mime,
                                      size, sha256

  `ocr_result`                        version_id, raw_text, language,
                                      provider, model_version

  `extracted_field`                   version_id, field_key, raw_value,
                                      normalized_value, confidence,
                                      provenance

  `field_verification`                version_id, field_key,
                                      candidate_value, confirmed_value,
                                      confirmed_at

  `renewal_rule`                      id, document_type, jurisdiction,
                                      lead_days_json, source, version

  `custom_rule`                       user_id, document_type,
                                      lead_days_json

  `reminder_occurrence`               version_id, due_at_utc, local_date,
                                      type, status, idempotency_key

  `notification_attempt`              occurrence_id, provider_id,
                                      attempted_at, result

  `processing_job`                    type, status, retry_count,
                                      error_code

  `audit_event`                       user_id, action, entity_id,
                                      created_at, non_sensitive_metadata

  `evaluation_metric`                 version_id, ground_truth_json,
                                      prediction_json, correctness_json,
                                      latency_ms
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## 6. Key flows

### 6.1 Upload to verified metadata

``` mermaid
sequenceDiagram
    actor U as User
    participant A as App
    participant S as Storage
    participant Q as Queue
    participant O as OCR
    participant E as Extractor
    U->>A: Upload image/PDF
    A->>S: Store private original
    A->>Q: Enqueue OCR
    Q->>O: Process pages
    O-->>Q: Raw text + language
    Q->>E: Extract structured fields
    E-->>Q: Type/dates/metadata
    Q-->>A: Save candidates
    A-->>U: Review screen
    U->>A: Correct + confirm expiry/rule
    A->>A: Generate reminder occurrences
```

### 6.2 Reminder dispatch

``` mermaid
sequenceDiagram
    participant C as Cron
    participant A as App
    participant Q as Queue
    participant N as Notifier
    C->>A: Find due occurrences
    A->>Q: Lock/enqueue unsent occurrence
    Q->>N: Send minimal reminder
    N-->>Q: Provider message ID/result
    Q->>A: Mark sent/failed idempotently
```

### 6.3 Renewal/versioning

User opens expiring document → selects **Upload renewed version** → new
immutable version created → OCR/extraction → user verifies → new version
becomes active → future reminders for old version are cancelled →
historical old version remains accessible.

------------------------------------------------------------------------

## 7. Renewal-rule design

Rules have explicit precedence:

1.  document-specific user override;
2.  user's saved custom default for that document type;
3.  catalogue default matching type + jurisdiction;
4.  catalogue generic type default;
5.  no default → user must enter rule manually.

A rule can contain multiple lead times, for example `[180, 90, 30, 7]`
days. A custom absolute renewal-start date is also supported.

The system must never present a catalogue rule as legal advice. The
review screen states the source of the rule and requires confirmation.

------------------------------------------------------------------------

## 8. Multilingual extraction design

Pipeline:

1.  preprocess page/preview;
2.  detect language/script where possible;
3.  OCR preserving original text;
4.  structured extraction with a language-independent schema;
5.  normalize dates;
6.  validate schema;
7.  run sanity checks (`issue_date <= expiry_date`, plausible year
    range);
8.  surface candidate + confidence/provenance;
9.  require user confirmation.

The normalized schema uses stable English field keys even when source
text is Arabic, Russian, French or another language. The raw source
value is always preserved.

------------------------------------------------------------------------

## 9. Security design

-   Private object storage only.
-   Signed URLs expire quickly and are owner-scoped.
-   Server-side provider credentials.
-   Encryption at rest and in transit.
-   File signature/MIME validation.
-   No active execution of uploaded content.
-   Logs use internal IDs, not document numbers or OCR text.
-   Minimal reminder templates.
-   Cross-user repository filtering on every data access.
-   Deletion job removes storage before final DB tombstone/cleanup and
    retries on partial failure.
-   Audit events avoid sensitive payloads.

------------------------------------------------------------------------

## 10. Environments

  -------------------------------------------------------------------------
  Environment                         Data
  ----------------------------------- -------------------------------------
  Local                               Synthetic fixtures only

  Test/CI                             Generated test documents and stubs

  Evaluation                          Synthetic/redacted/owner-controlled
                                      labelled set

  Production/pilot                    Only after privacy/provider
                                      configuration is reviewed
  -------------------------------------------------------------------------

------------------------------------------------------------------------

## 11. ADRs to record

1.  Web monolith vs microservices.
2.  OCR provider choice.
3.  Extraction provider/model and structured-output strategy.
4.  Private object-storage provider/region.
5.  Passwordless authentication choice.
6.  Postgres-backed queue choice.
7.  Rule precedence and representation.
8.  Notification provider and idempotency design.
9.  Evaluation-language set.
