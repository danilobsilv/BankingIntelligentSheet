# Import pipeline, previews and idempotency

BIS treats document ingestion as a review workflow rather than a direct insert. The private implementation supports OFX and explicitly registered CSV profiles, plus a separately gated PDF path. A parser profile is evidence for one grammar; it is not a promise that every export from that institution is compatible.

## Processing stages

1. **Select the destination and profile.** The authenticated user chooses an owned account or card and an available parser profile. Capability and entitlement checks happen before processing.
2. **Bound and identify the document.** Upload size is bounded, the document signature/structure is checked where applicable, and a SHA-256 digest identifies the source attempt.
3. **Parse and normalize.** The profile converts source rows into signed amounts, dates, descriptions, ledger-specific metadata and source provenance. Unsupported layouts fail rather than being guessed.
4. **Validate.** Dates, amounts, signs, ownership context, source evidence and financial rules are checked. A parser does not get to bypass authorization or confirmation rules.
5. **Persist a preview.** The preview contains review entries and source facts, including inclusion state and provenance. It is not a confirmed transaction.
6. **Review.** The user can include/exclude rows and edit permitted annotations. Original financial fields remain distinguishable from review metadata.
7. **Confirm explicitly.** The request carries the preview version and an idempotency key. The backend revalidates state, ownership, expiry and duplicate rules under a transaction before posting selected rows.
8. **Report the outcome.** The preview becomes confirmed, cancelled or expired; audit and import-entry records preserve the result.

## Why preview state is persisted

Keeping a preview in the database lets the review survive a page refresh and gives confirmation a stable version to check. It also prevents a client from submitting a changed local copy after another operation has updated the preview. The cost is lifecycle management: expired previews and incomplete attempts need explicit retention and cleanup policies.

## Duplicate and retry behavior

Document reservations use the authenticated user, destination and document digest. Confirmation idempotency records the request identity and result. A lost HTTP response therefore does not require a new financial intent. The backend can return the existing outcome or a safe uncertainty instead of inserting again.

Trusted external transaction IDs are used only when the parser has established that they are trustworthy. If an identifier conflicts with an existing trusted occurrence, confirmation fails closed. When two entries have equal dates, amounts and descriptions but separate source occurrences, the system preserves both rather than treating equality as proof of duplication.

## Failure behavior

- Invalid OFX, unsupported CSV headers or an unknown profile are rejected before persistence.
- Ownership failures do not reveal another user's account or preview.
- Preview expiry, cancellation and version conflicts stop confirmation without changing the confirmed ledger.
- Ambiguous duplicates or incomplete financial evidence remain review-blocking rather than being silently “fixed”.
- A database failure rolls back related confirmation writes.

PDF AI adds external-AI consent, budget admission, structured-response validation, source-evidence matching and independent reconciliation. It is disabled by default and does not provide a fallback that silently calls another model. See [AI safety](AI_SAFETY.md).

## What this does not claim

Passing a synthetic fixture does not validate a bank's real export layout. OCR, arbitrary PDFs, automatic card-invoice reconciliation and universal CSV dialect detection are not implied by the presence of a generic import screen. New formats require an explicit profile, representative evidence and regression coverage.
