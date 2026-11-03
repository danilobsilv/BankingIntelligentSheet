# Roadmap and project status

> This roadmap reflects the current project direction. It is not a launch commitment, and priorities can change based on testing and user feedback.

## Implemented internally

- [x] Java 21 / Spring Boot backend and React / TypeScript frontend foundation
- [x] PostgreSQL schema management with Flyway
- [x] Containerized local development and automated CI
- [x] User authentication and ownership-scoped financial resources
- [x] Bank accounts, categories, manual transactions, and monthly checking-account summaries
- [x] OFX upload, secure preview, review, cancellation, and atomic confirmation
- [x] Credit-card domain and distinct card financial operations
- [x] Experimental card CSV parser with review, classification, confirmation, and deduplication
- [x] Integration testing with synthetic documents and PostgreSQL

## Beta prerequisites — in progress

- [ ] Establish safe, scheduled retention/purging of eligible temporary preview records
- [ ] Verify representative, authorized, anonymized bank-issued export layouts
- [ ] Keep unsupported bank CSV ingestion disabled until compatibility is demonstrated
- [ ] Complete privacy and security operational review
- [ ] Establish repeatable staging deployment, backup/restore procedure, monitoring, and incident response basics
- [ ] Validate end-user experience in an actual browser and complete pilot acceptance criteria

## Planned exploration

- [ ] Additional supported bank document formats, including PDFs and spreadsheets, after format-specific verification
- [ ] Improved classification and financial insights
- [ ] More accessible export/reporting workflows
- [ ] Optional advanced/premium capabilities, subject to product validation

## Access and pricing direction

The intended product direction is **free manual uploads and free essential finance features**. No paid-tier feature list, price, release date, or public registration availability has been announced.

## What is not currently claimed

- Verified compatibility with actual Nubank exports
- Production-ready public availability
- Open banking / Open Finance account connectivity
- Automatic matching of checking-account payments with credit-card invoices
- Automated reconciliation of invoice totals
- Guaranteed support for PDFs/XLS from any bank

This repository is a public showcase, while application implementation remains private.
