# Project Passport (PID §2)

**Project:** Digital Document Wallet --- Phase 1: Store, Understand and
Renew\
**Phase:** Definition of Scope\
**Version:** Draft v1.0\
**Date:** 23 September 2026\
**Language:** English

------------------------------------------------------------------------

## 2.1 Prerequisites

1.  Important personal documents are commonly distributed across paper
    folders, phone galleries, email attachments and cloud folders.
2.  Users often discover an expiry date only when a document is urgently
    needed.
3.  Different document types may require renewal days, weeks or months
    before the printed expiry date.
4.  The required advance period is not universal; it can depend on
    document type, issuing authority, jurisdiction or user
    circumstances.
5.  Manually entering every date into a calendar is repetitive and
    error-prone.
6.  Documents may be written in different languages and scripts, so a
    single-language OCR workflow is insufficient.
7.  A wrong AI-extracted date can be worse than no automation if it
    creates false confidence.
8.  Personal documents can contain sensitive identifiers and images,
    requiring secure storage and conservative notifications.

------------------------------------------------------------------------

## 2.2 Objective

**By the end-of-semester defence in December 2026, deliver a secure web
application that stores image/PDF documents, extracts expiry-related
metadata from a declared multilingual test set, requires user
verification of the extracted expiry date and renewal rule, and
automatically creates reminders at the correct lead time --- achieving
≥90% exact expiry-date extraction accuracy on the labelled evaluation
set, 100% correct scheduling for user-confirmed dates/rules, zero
duplicate reminder sends, and successful retrieval of 100% of stored
evaluation documents after service restarts.**

  -----------------------------------------------------------------------
  SMART dimension                     Content
  ----------------------------------- -----------------------------------
  Specific                            Secure document wallet; image/PDF
                                      upload; multilingual OCR; document
                                      classification; expiry/issue date
                                      extraction; user confirmation;
                                      configurable renewal lead time;
                                      reminders; search and version
                                      history.

  Measurable                          ≥90% exact expiry-date accuracy on
                                      declared evaluation set; 100%
                                      schedule correctness after
                                      confirmation; 0 duplicate
                                      reminders; 100% retrieval;
                                      processing latency measured per
                                      document.

  Achievable                          Uses established OCR/vision/LLM
                                      services or local models behind
                                      provider interfaces. Human
                                      verification limits the risk of
                                      extraction errors.

  Relevant                            Replaces fragmented storage and
                                      manual date tracking with one
                                      controlled renewal workflow.

  Time-bound                          13-week semester plan; end-to-end
                                      slice by Week 6--7; evaluation by
                                      Weeks 10--11; defence package by
                                      Week 12--13.
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## 2.3 Success Criteria

  -----------------------------------------------------------------------
  \#                                  Criterion
  ----------------------------------- -----------------------------------
  2.3.1                               User uploads a JPG/PNG/PDF, the
                                      original is stored, and a document
                                      record is created without manual
                                      metadata entry.

  2.3.2                               The system detects or accepts the
                                      document language and extracts a
                                      candidate expiry date for the
                                      declared multilingual evaluation
                                      set.

  2.3.3                               No reminder becomes active until
                                      the user confirms or corrects the
                                      expiry date and renewal lead time.

  2.3.4                               ≥90% of evaluation documents have
                                      the exact ground-truth expiry date
                                      extracted before correction.

  2.3.5                               100% of confirmed dates/rules
                                      produce the expected reminder
                                      timestamp; no duplicate reminder is
                                      sent under retry.

  2.3.6                               100% of evaluation documents remain
                                      retrievable after restart, with
                                      metadata and version links intact.

  2.3.7                               The user can replace an expired
                                      document with a renewed version
                                      without losing the historical
                                      version.

  2.3.8                               Deletion removes the original file,
                                      derived previews/text, metadata and
                                      future reminders.
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## 2.4 Metrics

  -------------------------------------------------------------------------
  Metric                    Baseline                Target
  ------------------------- ----------------------- -----------------------
  Exact expiry-date         0 automated             ≥90% overall; report
  extraction accuracy                               per language

  Document-type             0 automated             ≥90% on supported
  classification accuracy                           catalogue

  Field                     n/a                     Measured; lower is
  confirmation/correction                           better, no invented
  rate                                              target before pilot

  Processing latency        n/a                     ≤30 s p90 for a typical
                                                    1--3 page document

  Correct reminder          Manual calendar         100%
  scheduling after                                  
  confirmation                                      

  Duplicate reminder sends  n/a                     0

  Retrieval success after   Fragmented/manual       100%
  restart                                           

  Unsupported/uncertain     n/a                     100%; never silently
  extraction surfaced to                            guessed
  user                                              

  Evaluation set size       0                       ≥40 documents, ≥3
                                                    languages, ≥4 document
                                                    types
  -------------------------------------------------------------------------

------------------------------------------------------------------------

## 2.5 Scope

  -----------------------------------------------------------------------
  In Scope --- Phase 1                Out of Scope / Deferred
  ----------------------------------- -----------------------------------
  User authentication                 Government portal integration

  Upload JPG, PNG and PDF             Automatic renewal submission

  Secure original-file storage        Legal validity verification /
                                      forgery detection

  OCR/text extraction                 E-signature

  Multiple languages/scripts          Collaborative enterprise DMS

  Document-type classification from   Unlimited arbitrary document
  supported catalogue                 workflows

  Issue/expiry date extraction        Automatic payment of renewal fees

  Confidence/provenance display       Legal advice on renewal eligibility

  User correction and confirmation    Guaranteed interpretation of
                                      handwritten documents

  Configurable renewal lead time      Full offline native mobile app

  Default rule by document type +     Family/team sharing unless time
  user override                       allows

  Email/in-app reminder engine        SMS/WhatsApp/push unless selected
                                      later

  Due-soon/expired dashboard          Real government databases

  Search/filter by type, status and   Biometric identity verification
  date                                

  Version chain for renewed documents Public sharing links

  Delete/export own data              Training models on user documents
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## 2.6 Impact Assessment

  -----------------------------------------------------------------------
  Area                                Impact
  ----------------------------------- -----------------------------------
  User workflow                       Moves from scattered storage/manual
                                      calendar entry to one
                                      upload-and-confirm workflow

  Data                                Introduces secure originals, OCR
                                      text, extracted metadata, rule
                                      configuration and reminder history

  AI/OCR                              Adds external or local processing
                                      of document content

  Notifications                       Adds scheduled outbound reminders

  Privacy                             Requires strong access control,
                                      encryption, deletion and minimal
                                      notification content

  Multilingual support                Requires language-aware
                                      OCR/extraction and per-language
                                      evaluation

  Renewal process                     Makes lead time explicit and
                                      configurable instead of assumed
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## 2.7 Constraints and Assumptions

### Constraints

  -----------------------------------------------------------------------
  ID                                  Constraint
  ----------------------------------- -----------------------------------
  C1                                  Semester delivery window is
                                      approximately 13 weeks; exact
                                      defence date is OQ-2.

  C2                                  AI-extracted expiry dates cannot
                                      activate reminders without user
                                      confirmation.

  C3                                  Phase 1 evaluates a declared subset
                                      of languages; "multilingual" does
                                      not mean every language has equal
                                      measured quality.

  C4                                  Notifications must not contain
                                      document numbers, full OCR text or
                                      document images.

  C5                                  Original files and derived metadata
                                      must be deletable by the owner.

  C6                                  Budget must remain suitable for a
                                      student project; expensive OCR/LLM
                                      calls require usage limits.

  C7                                  Phase 1 does not claim legal
                                      validation, authenticity
                                      verification or automatic renewal.
  -----------------------------------------------------------------------

### Assumptions

  -----------------------------------------------------------------------
  ID                                  Assumption
  ----------------------------------- -----------------------------------
  A1                                  Most target documents contain a
                                      machine-readable printed
                                      expiry/valid-until date.

  A2                                  User can correct fields when
                                      extraction is uncertain.

  A3                                  A configurable rule catalogue can
                                      represent the Phase 1 renewal
                                      lead-time cases.

  A4                                  Email is available for account
                                      access and at least one reminder
                                      channel.

  A5                                  Evaluation documents can be
                                      synthetic, redacted or
                                      owner-controlled.
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## Open Issues

  -----------------------------------------------------------------------
  ID                Issue             Owner             Deadline
  ----------------- ----------------- ----------------- -----------------
  OQ-1              Final product     Student           Week 1
                    name                                

  OQ-2              Exact defence     Student /         Week 1
                    date              supervisor        

  OQ-3              Reminder channels Student           Week 2

  OQ-4              Evaluation        Student           Week 2
                    languages                           

  OQ-5              Initial document  Student           Week 2
                    catalogue and                       
                    default lead                        
                    times                               

  OQ-6              Weekly available  Student           Week 1
                    hours                               

  OQ-7              Hosting/storage   Student           Week 3
                    region                              
  -----------------------------------------------------------------------
