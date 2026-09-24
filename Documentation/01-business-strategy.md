# Business Strategy --- Digital Document Wallet

**Working name:** DocuWallet\
**Date:** 23 September 2026\
**Audience:** student, supervisor, competition jury, potential pilot
users

------------------------------------------------------------------------

## 1. Product position

The project is not merely a cloud folder and not merely an OCR demo.

> **DocuWallet is a secure personal document wallet that understands
> when documents expire and turns that information into verified renewal
> reminders.**

Generic cloud storage can keep scans. Calendar applications can store
dates. OCR can read text. The product value is the controlled chain
connecting all three:

**document → multilingual extraction → user verification → renewal rule
→ reminder → renewed version**

The user should not have to remember which passport, residence permit,
insurance document, licence, certificate, subscription document or
institutional record expires next.

------------------------------------------------------------------------

## 2. Target users and initial wedge

  -----------------------------------------------------------------------
  Dimension               Phase 1 choice          Rationale
  ----------------------- ----------------------- -----------------------
  User                    Individual managing     Clear owner, simple
                          personal/household      permissions, easy
                          documents               academic evaluation

  Documents               Expiring identity,      High reminder value and
                          travel, insurance,      structured dates
                          education, vehicle and  
                          administrative          
                          documents               

  Input                   Photo/image and PDF     Covers phone scans and
                                                  downloaded documents

  Languages               Multilingual            Multilinguality is
                          OCR/extraction;         core, but quality must
                          evaluation on a fixed   be measurable
                          declared subset         

  Output                  Verified metadata +     Narrower and more
                          renewal reminder        defensible than a
                                                  general AI document
                                                  assistant
  -----------------------------------------------------------------------

Possible later buyers/users include families, international students,
expatriates, HR departments, mobility services, universities and small
businesses.

------------------------------------------------------------------------

## 3. Differentiation

The defensible part is not OCR alone. It is the **expiry lifecycle**.

  Capability                                      Ordinary storage   Calendar   OCR scanner   DocuWallet
  --------------------------------------------- ------------------ ---------- ------------- ------------
  Stores original document                                     Yes         No     Sometimes          Yes
  Reads document text                                           No         No           Yes          Yes
  Handles multiple languages                                    No        n/a     Sometimes     Required
  Extracts expiry candidate                                     No         No     Sometimes          Yes
  Requires human verification                                  n/a     Manual        Rarely          Yes
  Applies document-specific renewal lead time                   No     Manual            No          Yes
  Tracks renewed versions                                   Manual         No            No          Yes
  Produces due/overdue dashboard                                No    Partial            No          Yes

------------------------------------------------------------------------

## 4. Product rules that preserve long-term value

1.  **Provider abstraction.** OCR/extraction and notification providers
    sit behind interfaces so they can be changed.
2.  **Original + normalized data.** Preserve the uploaded file and
    extracted raw text; store normalized dates separately.
3.  **Confidence + provenance.** Every AI-extracted field stores
    confidence/provenance and whether it was user-confirmed.
4.  **Versioned documents.** A renewed document becomes a new version
    linked to the prior one; history is not overwritten.
5.  **Config-driven document types.** Renewal defaults live in
    configuration/data, not hardcoded business logic.
6.  **Metrics built in.** Extraction corrections and reminder outcomes
    are measurable.
7.  **Privacy by design.** Encryption, minimal notifications, access
    control and deletion are core requirements.

------------------------------------------------------------------------

## 5. Evidence is the academic asset

A convincing evaluation should include a labelled test set containing
several document layouts and languages. For every document, create
ground-truth values for document type, issue date, expiry date and any
other evaluated fields.

The Evaluation Report should state:

1.  dataset composition by language and document type;
2.  OCR/extraction method;
3.  field-level exact-match accuracy;
4.  expiry-date accuracy;
5.  percentage of documents requiring user correction;
6.  reminder scheduling accuracy;
7.  processing latency;
8.  failure cases and threats to validity.

The strongest claim is not "the AI works." It is a measured statement
such as: "On the declared evaluation set, the system extracted the
correct expiry date on X/Y documents, and all confirmed dates produced
the expected reminder schedule."

------------------------------------------------------------------------

## 6. Growth path

  -----------------------------------------------------------------------
  Stage                               Product
  ----------------------------------- -----------------------------------
  Phase 1                             Personal wallet, upload,
                                      multilingual extraction,
                                      verification, reminders

  Phase 2                             Household/shared vault, richer
                                      reminder channels, document renewal
                                      workflows

  Phase 3                             Institution integrations, automatic
                                      import, organization accounts

  Phase 4                             API/platform for universities,
                                      HR/mobility and compliance
                                      workflows
  -----------------------------------------------------------------------

Potential monetization after academic validation: freemium personal
tier, paid family storage, premium notification channels, and B2B
per-seat/per-organization plans.

------------------------------------------------------------------------

## 7. Risks to the product idea

  -----------------------------------------------------------------------
  Risk                    Why it matters          Response
  ----------------------- ----------------------- -----------------------
  OCR/date extraction     Wrong reminders destroy Mandatory verification
  errors                  trust                   before activation

  Privacy concerns        Documents can contain   Encryption, minimal
                          highly sensitive data   retention, clear
                                                  deletion, no training
                                                  on user files

  Too many document types Rules become            Start with a small
                          unmanageable            configurable catalogue

  "Just use Google        Weakens differentiation Demonstrate automatic
  Drive + Calendar"                               extraction, renewal
  objection                                       rules, version history
                                                  and due dashboard

  Multilingual quality    One model may not       Declare evaluated
  varies                  perform equally across  languages and show
                          scripts                 per-language metrics

  Scope creep into full   Threatens semester      Keep collaboration,
  document management     delivery                e-signature and
                                                  government integration
                                                  out of Phase 1
  -----------------------------------------------------------------------

------------------------------------------------------------------------

## 8. First two weeks

1.  Fix the initial language set and document catalogue.
2.  Define at least 30--50 synthetic/redacted evaluation documents
    across those languages.
3.  Define the ground-truth schema.
4.  Decide default renewal lead-time rules and which rules must always
    be user-entered.
5.  Create the repository, CI, ADR log and security assumptions.
6.  Prototype one end-to-end path: upload → extract expiry → confirm →
    schedule one reminder.
