# Test and Measurement Plan --- Digital Document Wallet

Phase 1 · v1.0 · Two questions: **does it work?** and **how accurately
does the AI extract renewal data?**

------------------------------------------------------------------------

# Part A --- Test Plan

## A1. Test levels

  -----------------------------------------------------------------------
  Level                   Tool                    Covers
  ----------------------- ----------------------- -----------------------
  Unit                    Vitest                  Date normalization,
                                                  rule precedence,
                                                  schedule calculation,
                                                  notification template

  Integration             Vitest + test           Owner isolation,
                          DB/storage stubs        verified-field
                                                  protection, job
                                                  retries, deletion

  End-to-end              Playwright              Upload → verify →
                                                  reminder workflow,
                                                  cross-user access,
                                                  versioning

  Evaluation              Labelled dataset runner OCR/type/expiry
                                                  accuracy by
                                                  language/type

  Manual                  Checklist               Mobile upload, preview,
                                                  usability, live demo
  -----------------------------------------------------------------------

## A2. The twelve tests that matter

  -----------------------------------------------------------------------
  \#                      Test                    Verifies
  ----------------------- ----------------------- -----------------------
  T-01                    Cross-user document     F1.2, N3.3
                          access denied           

  T-02                    Fake/invalid file       F2.2, N3.7
                          rejected                

  T-03                    Original                F2.3, N2.1
                          upload/download hash    
                          identical               

  T-04                    OCR failure gives       F3.5
                          retry/manual path       

  T-05                    Missing expiry is not   F5.6, N5.4
                          fabricated              

  T-06                    Unverified AI date      F6.3
                          schedules zero          
                          reminders               

  T-07                    Verified field survives F6.6, N2.5
                          re-processing unchanged 

  T-08                    Rule precedence uses    F7
                          document override       
                          before defaults         

  T-09                    90/30/7-day schedule    F8.1
                          generated exactly       

  T-10                    Notification retry      F9.4, N2.3
                          produces no duplicate   

  T-11                    Renewal creates new     F11
                          version and archives    
                          old future reminders    

  T-12                    Delete removes          F12, N4.3
                          original, derived data  
                          and future reminders    
  -----------------------------------------------------------------------

## A3. Fault injection matrix

  -----------------------------------------------------------------------
  Failure                             Expected behavior
  ----------------------------------- -----------------------------------
  OCR provider 500/timeout            Retry; terminal `ocr_failed`;
                                      manual entry available

  Extractor invalid JSON              `extraction_failed`; no trusted
                                      partial metadata

  Notification timeout after provider Idempotency prevents duplicate
  accepted message                    

  Process restart mid-job             Job resumes/retries; stored
                                      original remains

  Storage deletion succeeds but DB    `deletion_pending`; retry to
  cleanup fails                       completion

  Scheduler misses one interval       Due unsent occurrence is sent late
                                      and delay is logged

  AI returns impossible date          Validation flags review; cannot
                                      auto-verify

  Reprocess returns different expiry  Verified value remains
                                      authoritative; new candidate shown
                                      separately
  -----------------------------------------------------------------------

## A4. Definition of done per feature

A feature is done when its acceptance criteria pass, failure behavior is
visible, automated tests are in CI where practical, security/privacy
assumptions are documented, and any architectural decision is recorded.

------------------------------------------------------------------------

# Part B --- Measurement Plan

## B1. Evaluation dataset protocol

Create a frozen manifest before tuning the final extraction
prompt/model.

Minimum recommended set:

-   ≥40 documents total;
-   ≥3 languages;
-   ≥4 document types;
-   multiple layouts and image qualities;
-   at least 5 documents with no expiry date;
-   ambiguous numeric dates where day/month order matters;
-   multi-page PDFs;
-   synthetic/redacted/owner-controlled content only unless separately
    approved.

For each document, label:

`document_type`, `language`, `issue_date`, `expiry_date`,
`issuing_authority` where applicable, `has_expiry`, and notes about
ambiguity.

Do not pass ground truth into the extraction prompt.

## B2. Extraction evaluation protocol

1.  Run every frozen document through the same production
    upload/OCR/extraction path.
2.  Record provider/model/version and latency.
3.  Compare normalized predicted expiry date to exact ground truth.
4.  Compare document type to ground truth.
5.  Record whether the UI correctly required review.
6.  Report overall and per-language/per-type results.
7.  Manually classify failures: OCR error, language detection, wrong
    field selection, date normalization, hallucination, poor image,
    unsupported layout.

## B3. Reminder evaluation protocol

Use deterministic test clocks or accelerated time.

Test at minimum:

-   single lead time;
-   multiple lead times;
-   expiry at month/year boundary;
-   leap year;
-   user timezone differing from server;
-   daylight-saving transition where relevant;
-   user override replacing catalogue default;
-   expiry correction after occurrences already exist;
-   retry after notification timeout;
-   overdue document.

Expected schedule is computed independently in the test fixture and
compared with stored occurrences.

## B4. Metric definitions

  -----------------------------------------------------------------------
  Metric                              Definition
  ----------------------------------- -----------------------------------
  Expiry exact-match accuracy         documents with predicted normalized
                                      expiry exactly equal to ground
                                      truth / documents with an expiry

  No-expiry precision                 documents correctly left without an
                                      expiry / ground-truth no-expiry
                                      documents

  Type accuracy                       exact supported document-type
                                      matches / evaluated supported
                                      documents

  Correction rate                     verified fields changed by user /
                                      extracted fields presented

  Processing latency                  upload-complete timestamp to
                                      review-ready timestamp

  Schedule correctness                expected reminder occurrences
                                      exactly matching generated
                                      occurrences

  Duplicate-send rate                 duplicate notifications / logical
                                      occurrences

  Retrieval success                   retrievable originals with correct
                                      hash / stored evaluation originals
  -----------------------------------------------------------------------

## B5. Threats to validity

-   Small dataset may not represent real-world document diversity.
-   Synthetic/redacted documents can be cleaner than production
    documents.
-   Languages may have unequal sample counts.
-   One OCR/AI provider can bias results.
-   Exact-date accuracy does not measure every useful metadata field.
-   A user-confirmation gate improves safety but adds manual effort.
-   Reminder correctness in simulated time does not prove long-term
    provider reliability.

These limitations should be stated in the final report even when results
are strong.

## B6. Success thresholds

  -----------------------------------------------------------------------
  Outcome                             Interpretation
  ----------------------------------- -----------------------------------
  Expiry accuracy ≥90%, schedule      Core Phase 1 targets met
  100%, duplicates 0                  

  Expiry \<90% but review gate works  AI quality is the bottleneck;
  and schedule is correct             system safety boundary still
                                      validated

  Expiry strong but schedule errors   Renewal engine is not ready;
  exist                               scheduling must be fixed before
                                      demo claim

  Hallucinated dates occur without    Release-blocking defect
  review                              

  Cross-user access or public-file    Release-blocking security defect
  exposure occurs                     
  -----------------------------------------------------------------------

## B7. Evidence pack

-   frozen dataset manifest;
-   evaluation CSV;
-   per-language/type accuracy table;
-   failure examples with redacted images;
-   CI run for T-01...T-12;
-   reminder schedule matrix;
-   security configuration screenshots;
-   ADR log;
-   3-minute demo video;
-   Evaluation Report.
