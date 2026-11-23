# AI PDF safety and cost controls

PDF parsing with Gemini is an experimental import path. It is deliberately separate from deterministic OFX/CSV profiles and remains disabled by default. The model returns an extraction proposal; Java validation and human review remain authoritative.

## Admission before a provider call

The service checks authentication, destination ownership, explicit external-AI consent, capability/entitlement, model configuration, PDF size and structure, cache eligibility and the persistent kill switch before contacting the provider. A bounded PDF text/page estimate is converted to a conservative input/output token bound. If the estimated request cannot fit the configured per-document budget, it is rejected without a provider call.

The current implementation is pinned to `gemini-3.1-flash-lite`, one generation attempt, structured JSON, and a configured maximum output-token allowance. Cache hits reuse a compatible successful result; they do not reserve or generate another request. No automatic retry, model fallback, token-counting request or health check is used.

## Reservation lifecycle

Budget admission is an atomic PostgreSQL reservation. Provider usage is settled after a response using reported input/output accounting. A cost greater than the reservation is recorded as an overrun and can suspend further paid generations pending operator review. If the provider may already have billed but usage is unavailable, the attempt remains `UNKNOWN_BILLING`; the reservation is not silently released.

These are application-level controls. They reduce foreseeable overspend and prevent duplicate calls, but they cannot guarantee the provider's final invoice. Historical usage and uncertain reservations are retained for reconciliation and are not rewritten to make a new request eligible.

## Response validation

The structured response is checked for schema version, document type, dates, signed decimal amounts, ledger/classification, transaction completeness, source page/evidence and duplicate source occurrences. For checking statements, the backend independently verifies:

`opening balance + sum(signed movements) = closing balance`

Finding a source string on a page is evidence that the model cited a location; it is not proof that the amount, sign, classification or complete coverage is correct. The signed amount is used for statement reconciliation and direction validation; if the result reaches an account preview, normal import normalization stores a positive magnitude with an explicit `INCOME`/`EXPENSE` direction. Ambiguous or unverifiable entries fail closed or remain review-blocking under the existing policy. A valid response never confirms transactions automatically.

## Privacy boundary

The frontend has no Gemini credential. Raw PDFs, full prompts, full model responses and provider error bodies are not written to operational logs. Tests use synthetic PDFs and fake providers. Any future real-document processing requires explicit disclosure of external processing, retention review and an authorized environment. The public repository intentionally omits provider credentials, customer documents and internal endpoint details.

## Operational recovery

Operators can inspect sanitized usage status, reservation, model and failure code without exposing financial contents. A kill switch blocks new paid generations while existing cached previews and confirmed records remain available. Recovery from `UNKNOWN_BILLING` or a reservation overrun requires an authorized operational decision based on the provider/accounting state; it is not an automatic retry path.
