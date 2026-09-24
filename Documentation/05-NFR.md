# Non-Functional Requirements --- Digital Document Wallet

Phase 1 · v1.0 · Every requirement has a target and a verification
method.

------------------------------------------------------------------------

## N1 --- Performance

  ------------------------------------------------------------------------
  ID                Requirement       Target            Verification
  ----------------- ----------------- ----------------- ------------------
  N1.1              Upload            ≤2 s after upload 10-run timing
                    acknowledgement   completes         

  N1.2              OCR + extraction  ≤30 s p90         Evaluation metrics
                    for typical 1--3                    
                    page file                           

  N1.3              Wallet list load  ≤2 s              Seeded performance
                    with 200                            test
                    documents                           

  N1.4              Document detail   ≤1.5 s excluding  Manual/automated
                    metadata load     original download timing

  N1.5              Reminder dispatch within 5 min of   Scheduled vs sent
                    timing            due time          timestamp
  ------------------------------------------------------------------------

------------------------------------------------------------------------

## N2 --- Reliability and data integrity

  -----------------------------------------------------------------------
  ID                Requirement       Target            Verification
  ----------------- ----------------- ----------------- -----------------
  N2.1              Original-file     100% hash match   SHA-256 test
                    integrity         after             
                                      upload/download   

  N2.2              No lost verified  0 losses          Kill/restart
                    metadata across                     integration test
                    restart                             

  N2.3              Notification      0 duplicates      Forced retry test
                    idempotency                         

  N2.4              Processing job    ≥3 attempts with  Fault injection
                    retry             backoff           

  N2.5              Verified fields   0 silent          Integration test
                    not overwritten   overwrites        
                    by reprocessing                     

  N2.6              Deletion          0 permanent       Fault injection
                    consistency       half-deleted      
                                      records           

  N2.7              Version chain     100% linked       DB
                    integrity         correctly         constraint/test
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## N3 --- Security

  -------------------------------------------------------------------------
  ID                Requirement       Target              Verification
  ----------------- ----------------- ------------------- -----------------
  N3.1              TLS               TLS 1.2+; HTTP      Deployment scan
                                      redirected          

  N3.2              Encryption at     DB/object storage   Provider
                    rest              encryption enabled  configuration
                                                          evidence

  N3.3              Owner isolation   0 cross-user reads  Automated
                                                          two-user access
                                                          test

  N3.4              Signed/private    Original files      URL expiry test
                    file access       never public;       
                                      signed URLs expire  
                                      ≤15 min             

  N3.5              Secrets           0 provider secrets  CI secret/build
                                      in client           scan
                                      bundle/repository   

  N3.6              Sensitive logging 0 document          Log inspection
                                      numbers/full OCR    test
                                      text in normal logs 

  N3.7              Upload safety     MIME/signature      Malicious-file
                                      validation; active  test
                                      content not         
                                      executed            

  N3.8              Auth token        Single-use where    Auth tests
                    replay/expiry     applicable; bounded 
                                      lifetime            

  N3.9              Dependency risk   0 known             Dependency audit
                                      high/critical       
                                      vulnerabilities at  
                                      defence             
  -------------------------------------------------------------------------

------------------------------------------------------------------------

## N4 --- Privacy and data governance

  -------------------------------------------------------------------------------------------
  ID                Requirement       Target                                Verification
  ----------------- ----------------- ------------------------------------- -----------------
  N4.1              AI provider use   Every provider and data field listed  HLD data-flow
                    documented                                              review

  N4.2              Model training    User files not intentionally used to  Provider/config
                                      train project models                  review

  N4.3              Deletion          Owner can delete document-derived     T-10
                                      data in one action                    

  N4.4              Data minimization Notifications contain no full         Template
                                      identifiers/OCR text                  assertion

  N4.5              Storage region    Region declared before pilot          Configuration
                                                                            evidence

  N4.6              Evaluation        Synthetic/redacted/owner-controlled   Dataset manifest
                    privacy           documents only unless approved        

  N4.7              Exportability     User can export originals + metadata  Export test
  -------------------------------------------------------------------------------------------

------------------------------------------------------------------------

## N5 --- AI/OCR and multilingual quality

  ---------------------------------------------------------------------------------
  ID                Requirement       Target                     Verification
  ----------------- ----------------- -------------------------- ------------------
  N5.1              Expiry            ≥90% overall on declared   Ground-truth
                    exact-match       test set                   evaluation
                    accuracy                                     

  N5.2              Per-language      Accuracy reported          Evaluation
                    reporting         separately for every       CSV/report
                                      evaluated language         

  N5.3              Document-type     ≥90% on supported          Ground-truth
                    accuracy          catalogue                  evaluation

  N5.4              Missing-date      0 fabricated expiry dates  Test documents
                    behavior          in negative set            with no expiry

  N5.5              Ambiguity         100%                       Curated ambiguity
                    handling          ambiguous/low-confidence   set
                                      cases require review       

  N5.6              Date              100% verified dates stored Unit/integration
                    normalization     as ISO date without        tests
                                      timezone drift             

  N5.7              Provider          Provider/model/version     DB query
                    reproducibility   recorded for every         
                                      extraction                 
  ---------------------------------------------------------------------------------

------------------------------------------------------------------------

## N6 --- Maintainability and reuse

  ------------------------------------------------------------------------------------
  ID                Requirement       Target                         Verification
  ----------------- ----------------- ------------------------------ -----------------
  N6.1              Provider          OCR, extraction and            Code review
                    abstraction       notification each behind       
                                      interface + stub               

  N6.2              Rule              New document type/default lead Demonstration ≤10
                    configuration     time added without editing     min
                                      scheduler code                 

  N6.3              Domain test       ≥70% statements for            CI coverage
                    coverage          date/rule/scheduling/version   
                                      logic                          

  N6.4              Reproducible      clone → local run ≤15 min      Fresh-machine
                    setup                                            test

  N6.5              ADR log           ≥6 meaningful architecture     `docs/adr/`
                                      decisions                      

  N6.6              Schema migrations All DB changes                 Repository review
                                      migration-controlled           
  ------------------------------------------------------------------------------------

------------------------------------------------------------------------

## N7 --- Usability and accessibility

  ---------------------------------------------------------------------------
  ID                Requirement          Target             Verification
  ----------------- -------------------- ------------------ -----------------
  N7.1              Upload-to-reminder   New user completes Pilot task
                    workflow             unaided after ≤10  
                                         min walkthrough    

  N7.2              AI status clarity    User can           Usability check
                                         distinguish        
                                         proposed vs        
                                         verified values    

  N7.3              Correction effort    Expiry/type/rule   Task test
                                         editable from one  
                                         review screen      

  N7.4              Destructive          100% permanent     UI test
                    confirmation         deletes require    
                                         explicit           
                                         confirmation       

  N7.5              Responsive UI        Usable on current  Manual matrix
                                         desktop and mobile 
                                         browsers           

  N7.6              Accessibility        Keyboard usable;   Accessibility
                                         labels on form     audit
                                         controls;          
                                         reasonable         
                                         contrast           
  ---------------------------------------------------------------------------

------------------------------------------------------------------------

## N8 --- Cost and operations

  -----------------------------------------------------------------------
  ID                Requirement       Target            Verification
  ----------------- ----------------- ----------------- -----------------
  N8.1              Development       Target ≤€25/month Monthly cost
                    operating cost    equivalent        sheet

  N8.2              Per-document AI   Measured and      Provider usage vs
                    cost              exported          processed docs

  N8.3              Backup            DB backup         Restore rehearsal
                                      configured;       
                                      restore procedure 
                                      documented        

  N8.4              Observability     Failed jobs and   Fault injection
                                      notification      
                                      failures visible  

  N8.5              Deployment        One documented    README/CI
                                      production        evidence
                                      deployment        
                                      procedure         
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## Out of scope for Phase 1

High-availability SLA, enterprise SSO, legal authenticity verification,
certified archival compliance, automatic government renewal, biometric
verification, and guaranteed support for every world language.
