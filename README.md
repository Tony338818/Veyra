# Veyra

**Prove eligibility. Not your medical history.**

Veyra is an open-source privacy engineering project exploring how patients could verify their eligibility for clinical trials without unnecessarily disclosing the medical information used to determine that eligibility.

The project combines healthcare interoperability, deterministic eligibility evaluation, verifiable credentials, and privacy-preserving cryptography.

---

## The Problem

Clinical trial eligibility can depend on highly sensitive information:

- Age
- Diagnoses
- Laboratory results
- Current medications
- Previous treatments
- Medical procedures
- Existing conditions

Determining whether someone qualifies may therefore require access to significant amounts of medical information before the patient even knows whether they are eligible.

Veyra explores a different question:

> **If a clinical trial only needs to know whether a patient satisfies its eligibility criteria, how much of the underlying medical information does it actually need to see?**

The long-term architecture looks like:

```text
Healthcare Provider
        │
        │ FHIR health data
        ▼
Credential Issuer
        │
        │ Verifiable health credentials
        ▼
      Patient
        │
        │ Select trial
        ▼
Eligibility Engine
        │
        │ Evaluate criteria
        ▼
Privacy Proof Engine
        │
        │ Minimum-disclosure proof
        ▼
      Patient
        │
        │ One-time proof / code / QR
        ▼
Clinical Trial Verifier
        │
        ▼
Eligibility Verified
```

---

## Example

Suppose a clinical trial requires:

```text
Age                     18–45
Diagnosis               Type 2 diabetes
HbA1c                   > 6.5%
Medication X            Not currently taking
```

The patient's health record might contain:

```text
Date of birth            2002-04-17
Diagnosis                Type 2 diabetes
HbA1c                    7.1%
Current medications      A, B
Other diagnoses          ...
Other observations       ...
```

But the trial may only need to establish:

```text
Age requirement satisfied             ✓
Diagnosis requirement satisfied       ✓
HbA1c requirement satisfied           ✓
Medication exclusion satisfied        ✓

Overall eligibility             VERIFIED
```

Veyra explores how that verification can happen without unnecessarily revealing values such as the patient's exact date of birth, complete medication history, unrelated diagnoses, or other medical information.

---

# Core Principles

### Privacy by Design

Medical information should not be disclosed simply because it is available.

### Minimum Disclosure

A verifier should receive only the information required for the verification being performed.

### Patient Control

Patients should control when their health credentials are used to generate eligibility proofs.

### Interoperability

Veyra should use established healthcare standards rather than creating proprietary representations of patient records.

### Verifiability

Eligibility should eventually be independently verifiable rather than relying on an untrusted `true` or `false` response.

### Deterministic Decisions

The core eligibility engine should use explicit machine-readable rules.

AI may eventually help structure trial criteria, but it should not silently determine whether a patient satisfies medical eligibility requirements.

---

# How Veyra Works

The project is being developed in layers.

## 1. Represent the Patient

Synthetic patient information is represented using FHIR resources such as:

```text
Patient
Condition
Observation
MedicationRequest
Procedure
```

For example:

```text
Patient
│
├── DOB
│
├── Conditions
│   └── Type 2 diabetes
│
├── Observations
│   └── HbA1c: 7.1%
│
└── Medications
    └── Medication A
```

---

## 2. Represent the Trial

Human-readable trial eligibility requirements are represented as deterministic machine-readable predicates.

For example:

```json
{
  "resource": "Observation",
  "code": "hba1c",
  "operator": ">",
  "value": 6.5,
  "unit": "%"
}
```

---

## 3. Evaluate Eligibility

The eligibility engine evaluates the trial criteria against the patient's FHIR record.

```text
FHIR Patient Record
        +
Structured Trial Criteria
        │
        ▼
Eligibility Engine
        │
        ▼
SATISFIED
NOT_SATISFIED
UNKNOWN
```

`UNKNOWN` is important.

Missing medical information should not automatically be interpreted as evidence that a criterion failed.

---

## 4. Issue Trusted Credentials

Later phases introduce a simulated healthcare provider capable of issuing cryptographically signed health credentials.

These credentials allow another system to verify that a medical assertion came from a trusted issuer and has not been modified.

---

## 5. Generate Privacy-Preserving Proofs

Instead of revealing:

```text
Date of birth = 2002-04-17
```

the desired system should eventually be capable of proving something closer to:

```text
Age >= 18
```

without unnecessarily revealing the underlying date of birth.

Veyra will explore techniques including:

- Verifiable Credentials
- Selective disclosure
- Verifiable Presentations
- Predicate proofs
- Zero-knowledge proofs

The exact cryptographic system will be selected based on the requirements discovered during development.

---

## 6. Verify Eligibility

The eventual verifier receives a trial-specific proof.

```text
Patient
   │
   │ proof / QR / one-time code
   ▼
Trial Verifier
   │
   ▼
VERIFIED
```

The verifier should be able to determine that the required eligibility conditions were satisfied without receiving unrelated medical information.

---

# Architecture

```text
                 ┌─────────────────────┐
                 │ Healthcare Provider │
                 └──────────┬──────────┘
                            │
                           FHIR
                            │
                            ▼
                 ┌─────────────────────┐
                 │  Credential Issuer  │
                 └──────────┬──────────┘
                            │
                     Signed Credentials
                            │
                            ▼
                 ┌─────────────────────┐
                 │       Patient       │
                 └──────────┬──────────┘
                            │
                    Select Clinical Trial
                            │
                            ▼
                 ┌─────────────────────┐
                 │ Eligibility Engine  │
                 └──────────┬──────────┘
                            │
                     Eligibility Result
                            │
                            ▼
                 ┌─────────────────────┐
                 │    Proof Engine     │
                 └──────────┬──────────┘
                            │
                  Minimum-Disclosure Proof
                            │
                            ▼
                 ┌─────────────────────┐
                 │   Trial Verifier    │
                 └──────────┬──────────┘
                            │
                            ▼
                         VERIFIED
```

---

# Technology

### Backend

- Python
- FastAPI
- Pydantic

### Healthcare

- HL7 FHIR
- UK healthcare interoperability concepts where applicable
- Standard clinical terminology where appropriate

### Privacy & Security

The project will progressively investigate:

- Digital signatures
- Verifiable Credentials
- Selective disclosure
- Zero-knowledge proofs

### Engineering

- Docker
- Pytest
- GitHub Actions

A blockchain is **not** required for Veyra.

Privacy-focused distributed infrastructure may be investigated later only if it solves a concrete problem involving trust, credential status, auditability, or verification.

---

# Current Milestone

The first milestone deliberately contains **no blockchain and no zero-knowledge proofs**.

```text
Synthetic FHIR Patient
          +
Structured Trial Criteria
          │
          ▼
Eligibility Engine
          │
          ▼
SATISFIED / NOT_SATISFIED / UNKNOWN
```

The healthcare and eligibility layers must work correctly before cryptographic privacy mechanisms are introduced.

---

# Project Status

🚧 **Active development**

See [`PROJECT_SPEC.md`](PROJECT_SPEC.md) for architecture, requirements and development phases.

---

# Open Source

Veyra is being developed as an open-source engineering project.

Potential contribution areas include:

- FHIR interoperability
- Eligibility operators
- Clinical terminology
- Trial-criteria modelling
- Credential systems
- Cryptographic proof systems
- Security analysis
- Threat modelling
- Testing
- Documentation

---

# Privacy & Safety

Veyra must use **synthetic patient data** during development.

Real patient medical records should not be committed to this repository or used in public demonstrations.

Veyra is not intended to diagnose patients, recommend treatments, replace clinicians, determine whether participating in a trial is medically advisable, or operate as a production healthcare system.

---

# Disclaimer

Veyra is an experimental open-source software engineering project exploring privacy-preserving clinical trial eligibility verification.

It is not an NHS service, medical device, clinical decision-support system, healthcare provider, or clinical trial recruitment service.