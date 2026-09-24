# DocuWallet --- Project Documentation Set

**Digital Document Wallet: Store, Understand and Renew** A semester
project for a secure multilingual document wallet with AI-assisted
expiry extraction and renewal reminders.

**Generated:** 23 September 2026 · **Language:** English · **Version:**
v1.0

------------------------------------------------------------------------

## What this is

This folder defines a buildable Phase 1 of a digital document wallet. A
user uploads or photographs personal documents, the system stores the
originals securely, uses OCR and AI-assisted extraction to identify
document metadata such as document type, issuing authority, issue date
and expiry date, and creates reminders before renewal is due.

The central design rule is that **AI extraction is a proposal, not the
source of truth**. The user sees the extracted fields, corrects them
when necessary, and confirms the expiry date and renewal lead time
before reminders become active.

The system is multilingual: it must accept documents containing
different scripts and languages, preserve the original text, and extract
normalized metadata without requiring the user to translate the document
manually.

------------------------------------------------------------------------

## Files

  ---------------------------------------------------------------------------
  \#                      File                        Purpose
  ----------------------- --------------------------- -----------------------
  01                      `01-business-strategy.md`   Product positioning,
                                                      target users,
                                                      differentiation, reuse
                                                      and growth strategy

  02                      `02-project-passport.md`    Project prerequisites,
                                                      SMART objective,
                                                      success criteria,
                                                      metrics, scope and
                                                      constraints

  03                      `03-BRD.md`                 Business Requirements
                                                      Document: goals, AS-IS,
                                                      stakeholders, risks,
                                                      requirements, options,
                                                      budget

  04                      `04-FRD.md`                 Detailed functional
                                                      requirements and
                                                      acceptance criteria

  05                      `05-NFR.md`                 Performance,
                                                      reliability, security,
                                                      privacy, multilingual
                                                      quality, usability and
                                                      operations

  06                      `06-HLD.md`                 High-level
                                                      architecture, stack,
                                                      data model, flows,
                                                      AI/OCR boundary and
                                                      security design

  07                      `07-roadmap-gantt.md`       13-week implementation
                                                      roadmap, gates, Gantt
                                                      and critical path

  08                      `08-test-plan.md`           Functional test plan
                                                      plus
                                                      extraction/reminder
                                                      quality measurement
                                                      protocol

  09                      `09-agent-build-guide.md`   Repository layout and
                                                      build slices for an AI
                                                      coding agent
  ---------------------------------------------------------------------------

------------------------------------------------------------------------

## Reading order

**Student:** 01 → 02 → 07.\
**Before coding:** 06 → 04 → 05 → 09.\
**Before evaluation:** 08.\
**Supervisor/reviewer:** 02 → 03 → 06.

------------------------------------------------------------------------

## The five things the project is judged on

1.  A user can securely upload or photograph a real-looking test
    document and retrieve it later.
2.  Multilingual OCR/extraction produces candidate metadata and clearly
    exposes uncertainty.
3.  No reminder is activated until the user confirms the expiry date and
    renewal rule.
4.  Renewal reminders fire at the correct configured lead time and are
    not duplicated.
5.  Evaluation uses a labelled multilingual test set and reports
    field-level accuracy, date accuracy and reminder correctness.

------------------------------------------------------------------------

## Fixed Phase 1 decisions

  -----------------------------------------------------------------------
  Decision                            Reason
  ----------------------------------- -----------------------------------
  AI never silently commits an expiry A wrong date can cause a missed
  date                                renewal; user confirmation is the
                                      safety boundary

  Original document is preserved      The wallet is a document
                                      repository, not only a metadata
                                      database

  Renewal rules are configurable      Different document types and
                                      jurisdictions require different
                                      advance periods

  Phase 1 supports image/PDF uploads, Keeps the project independently
  not external government             testable
  integrations                        

  Reminders contain minimal sensitive Notifications should not expose
  information                         document numbers or full document
                                      content

  Multilingual support is required    It is part of the core problem, not
                                      a later cosmetic feature
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## Open questions

  -----------------------------------------------------------------------
  ID                Question          Owner             Needed by
  ----------------- ----------------- ----------------- -----------------
  OQ-1              Final product     Student           Week 1
                    name                                

  OQ-2              Exact defence     Student /         Week 1
                    date              supervisor        

  OQ-3              Primary reminder  Student           Week 2
                    channels for                        
                    Phase 1: email                      
                    only, or email +                    
                    in-app                              

  OQ-4              Initial supported Student           Week 2
                    language test set                   

  OQ-5              Initial           Student           Week 2
                    document-type                       
                    catalogue and                       
                    default renewal                     
                    lead times                          

  OQ-6              Weekly            Student           Week 1
                    development hours                   
                    available                           

  OQ-7              Hosting/storage   Student           Week 3
                    region and                          
                    provider                            

  OQ-8              Whether document  Student /         Week 3
                    sharing/export is supervisor        
                    required by the                     
                    course                              
  -----------------------------------------------------------------------

> Privacy, retention and legal requirements vary by jurisdiction. Phase
> 1 should use synthetic, redacted or owner-controlled test documents
> unless the course explicitly approves another evaluation method.
