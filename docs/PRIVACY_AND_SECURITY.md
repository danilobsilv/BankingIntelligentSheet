# Privacy, security & disclosure boundaries

> **Design intentions and current limits — not a compliance certification, penetration-test report, legal privacy policy or production-readiness claim.** Banking Intelligent Sheet is undergoing internal MVP validation and has not launched publicly.

## Principles

- **Human confirmation before durable financial import.** A preview is a review step, not a posting to the financial ledger.
- **Preserve source facts.** Source dates and amounts remain distinguishable from user annotations and classification.
- **Scope information by authenticated user.** Domain access includes authorization and ownership checks; documentation does not claim that every security control has completed independent audit.
- **Conservative duplicates and retries.** Favor review of ambiguous source records; make confirmation safe to repeat.
- **Financial meaning matters.** Planning, card usage, goals, reserves and investment valuation are not automatically ordinary checking-account income or expenses.
- **Operational and commercial controls are independent.** Feature availability never replaces authentication, permissions, domain validation, or consent.
- **Limited claims.** Experimental parsing and optional AI need their own compatibility, privacy, budget, and operational reviews before public use.

## Sensitive-data lifecycle

Raw documents are processed within bounded import workflows, while temporary normalized review records and confirmed financial/audit records have distinct purposes. Temporary data lifecycle and production retention/deletion policy are subject to pre-beta validation; this page intentionally states no universal deletion guarantees.

Do not infer that the public repository contains a functioning bank integration, hosts uploaded financial data, or accepts financial statements.

## Responsible disclosure

Do not publish vulnerabilities with exploit instructions, tokens, financial documents, account numbers, identifying screenshots, or personal data in public issues. Use private vulnerability reporting when available or another verified private contact route. This repository is not a public bug bounty or deployed application.

## What we deliberately do not publish

The public architectural diagrams show conceptual modules and flows, **not** complete database schemas, internal API catalogs, authentication mechanics, private infrastructure, provider settings, secret-management configuration, defense thresholds, or incident playbooks. Their omission is intentional and should not be mistaken for absence of engineering controls.

## Legal and institutional boundaries

This showcase is not a public application release, a guarantee of bank compatibility, or an affiliation with financial institutions. Bank names, if referenced, indicate potential document-format research only and do not imply partnership or endorsement.
