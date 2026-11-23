# Financial invariants and ledger boundaries

This note records the rules that keep a report from treating every monetary event as the same kind of cash flow. It describes the behavior implemented in the private backend at the time of the public MVP validation; it is not a public API specification.

## Ledger separation

| Record | Source of truth | Included in realized checking cash flow? | Important exclusion |
| --- | --- | --- | --- |
| Confirmed account transaction | Account transaction ledger | Yes, when its positive amount, direction and date are valid | Pending previews and skipped duplicates |
| Credit-card purchase or invoice activity | Card ledger | No | Do not add card purchase and bank invoice payment together |
| Expected income / planned expense | Planning ledger | No | A plan is not a payment |
| Goal contribution / withdrawal | Goal movement ledger | No | Does not create an account transaction |
| Reserve movement | Reserve ledger | No | Does not silently debit a bank account |
| Investment purchase, sale or valuation | Investment ledger/position history | No | Valuation is not realized income |

The reporting layer exposes these as labeled scopes. A partial wealth view contains registered reserves and investments; it is not a complete net-worth statement because liabilities, external assets and unsupported balances are outside the model.

## Confirmation invariants

The durable account ledger changes only after a user confirms a reviewable import or creates an explicit manual transaction. Import confirmation revalidates ownership, preview state, expiry, version and inclusion choices. Statement parsers may represent source movements with a sign, but normalization derives `INCOME` or `EXPENSE` and stores the positive magnitude in the account `amount` column. The confirmation then writes the selected transactions, import-entry provenance, statement reservation and preview state in one database transaction.

The resulting invariants are:

1. A cancelled, expired or unconfirmed preview cannot create a confirmed transaction.
2. A transaction belongs to the authenticated owner's account; an identifier from another owner is not a usable shortcut.
3. A repeated confirmation intent is idempotent. The stored result is returned rather than inserting a second set of rows.
4. A duplicate skipped during confirmation does not contribute to realized totals.
5. A failure before commit leaves the confirmed ledger unchanged.

Trusted external transaction identifiers are accepted only when the parser explicitly marks them as trusted. Similar date/amount/description values are not sufficient to prove that two occurrences are duplicates; legitimate repeated purchases must remain reviewable.

## Budget semantics

Budget limits are planning constraints, not transaction blocks. Spending is derived from confirmed account transactions in the selected month using exact decimal arithmetic. Positive income is excluded from expense totals. A transaction that is only in a preview, was excluded by the user, or was rejected as a duplicate is not counted.

Global and category budgets have different meanings:

- A global budget can provide a consolidated comparison between total eligible expenses and one monthly limit.
- A category budget provides progress for that category only.
- Multiple category limits are not added together to manufacture a “total budget”. The same expense may participate in its category view and in a global view, but it is still one financial transaction.
- When there is no global budget, consolidated `limit`, `remaining` and `percentageUsed` are intentionally unavailable even though individual category progress can be shown.

Budget alert state is derived from the same transaction query. Threshold transitions are persisted with a generation and unique event key, so a page refresh or a confirmed transaction recalculation does not duplicate an alert.

## Corrections and classification

Changing a category changes classification, not the stored positive amount, direction, account, source provenance or posting date. Deterministic categorization rules can suggest or explicitly reclassify eligible history, but manual classifications are protected unless the user chooses otherwise. A rule cannot create, delete or alter money.

Planning, goal, reserve and investment operations likewise change their own ledgers. They are deliberately not mirrored into checking transactions because doing so without an explicit reconciliation event would double-count cash flow.

## Concurrency and auditability

Money is represented with PostgreSQL `NUMERIC` and Java `BigDecimal`. Account transaction and
preview amounts are positive magnitudes with an explicit `INCOME`/`EXPENSE` direction; parser
values can be signed before normalization, and report queries aggregate the stored magnitude by
direction. Goal and reserve movements likewise store positive amounts and use an explicit effect
when calculating balances. Services use transactions around related writes, user/account locks
where a limit or ownership race matters, unique constraints for idempotency and optimistic
versions for reviewable records. These are database-local guarantees; the design does not claim a
distributed lock service.

For a deeper import-specific state model, see [Import pipeline](IMPORT_PIPELINE.md). For AI reservations and uncertain billing, see [AI safety](AI_SAFETY.md).
