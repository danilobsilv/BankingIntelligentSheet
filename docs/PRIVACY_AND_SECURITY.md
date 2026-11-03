# Privacy and security

**Banking Intelligent Sheet is in active development and is not publicly launched.** This page describes engineering intentions and current boundaries; it is not a certification, legal privacy notice, or claim of production readiness.

## Design priorities

- Users should explicitly review transactions before committing imported financial records.
- Imported source amounts and dates should remain distinct from editable personal annotations.
- Authenticated requests and ownership checks should prevent access across users.
- Sensitive uploaded source documents and secrets should not appear in public repositories, test fixtures, or application logs.
- Import confirmation should use database transactions, locks, and idempotency to avoid duplicate financial writes.
- The application should fail safely when file formats or financial classifications are ambiguous.

## Data lifecycle

The experimental import pipeline discards complete uploaded raw-file bytes after parsing, while temporarily retaining normalized source rows for review and provenance. A scheduled retention and cleanup policy for eligible expired/cancelled previews is a pre-beta work item. Confirmed records and necessary audit/provenance data require separate, conservative retention handling.

No guarantee about full deletion, regulatory compliance, or production retention timing is made here.

## Public repository hygiene

This repository intentionally contains no application source, private environment configurations, bank statements, financial records, or API keys. Public issues are **not** a place for real transactions, screenshots showing balances or personal identifiers, password-reset information, or security credentials.

## Responsible disclosure

If you identify a possible vulnerability, **do not publish exploit details or personal data in a public issue**. Use GitHub's private vulnerability reporting feature if it is enabled on this repository, or contact the maintainer through an appropriate private channel.

Do not assume this public showcase repository is a live service or public bug-bounty program.

## Institutional independence

This is an independent software project. Names of banks or financial services appear solely to describe possible input formats or compatibility targets; they do not imply endorsement, affiliation, or access to a bank's APIs.
