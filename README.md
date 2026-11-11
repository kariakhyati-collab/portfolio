# Portfolio: Khyati Karia

These are case studies of production systems I designed and shipped while working in the Founder's Office.
They are client systems, so **the code is private and client names and data are left out**. Each one is described by its industry. I'm happy to walk through any of them live in an interview.

**At a glance:** 90+ production apps · 2,000+ commits · 20+ AI agents running for real businesses · US and India clients

| # | System | Industry | Core tech |
|---|---|---|---|
| 1 | [Multi-tenant AI SDR platform](#1-multi-tenant-ai-sdr-platform) | B2B manufacturing & distribution | Next.js, Postgres, Claude, Resend |
| 2 | [Speed-to-lead voice agent](#2-speed-to-lead-voice-agent) | Healthcare, home services, industrial gas | Retell, Vercel cron, Postgres, Sheets |
| 3 | [Vertical CRMs with AI built in](#3-vertical-crms-with-ai-built-in) | 15+ industries | Next.js, Neon, Gmail/Outlook OAuth, Claude |
| 4 | [Construction proposal builder](#4-construction-proposal-builder) | Commercial windows & doors | Next.js, PDF parsing, Claude |
| 5 | [Order-to-cash & AP agents](#5-order-to-cash--ap-agents) | Manufacturing | ERP connectors, Composio (Outlook), Postgres |
| 6 | [LOI desk & follow-up engine](#6-loi-desk--follow-up-engine) | Commercial lending | Next.js, signed webhooks, Resend |
| 7 | [B2B websites at scale](#7-b2b-websites-at-scale) | 20+ industrial & professional-services firms | Static generators, Vercel, Cloudflare R2, Replicate |

---

## 1. Multi-tenant AI SDR platform

**Problem.** Mid-size manufacturers get inbound leads from Meta ads, web forms and marketplaces, and sales reps answer them hours or days later. Many buyers have already gone to a competitor by then.

**What I built.** One platform that runs a separate AI sales agent for each client ("tenant"). Each agent:
- pulls new leads from the client's lead sheets or database every few minutes, with a separate column mapping for each sheet
- researches the buyer and writes a first email in the client's voice, based on the client's real product catalogue (crawled and structured ahead of time)
- follows each client's own rules: no prices quoted, trade buyers only, country limits, required CC addresses, spam-phrase allow-lists
- sends through each client's own sending domain, follows up on a schedule, and passes warm replies to a named human
- shows the client a **supplier view** with every lead, every email and its current status

**Safety.** Every agent starts as a **draft in dry-run mode**, and a `review_first_email` gate holds each first email until a human approves it. Going live is an explicit, logged switch.

**Scale.** 20+ client agents on one deployment. Some were later moved into their own repos when a client needed a custom flow.

**Stack.** Next.js · Postgres · Claude (structured output) · Resend · Google Sheets API · Vercel cron

---

## 2. Speed-to-lead voice agent

**Problem.** For booking-driven businesses (clinics, lawn care, gas suppliers), the first company to call a new lead back usually wins the job.

**What I built.** A two-step poller. First it mirrors new form leads from Postgres into a Google Sheet that the ops team already uses. Then it places an AI voice call (Retell) to each **new** lead, usually about **40 seconds after the form is submitted**. Calls wait for a spam check to finish first. Call outcomes are written back to the sheet, and a read-only call dashboard for the client shows volume, outcomes and booked consultations.

**Safety.** Three gates have to agree before any call is placed: a `CALLS_DRY_RUN` environment flag, an on/off cell in the sheet that the client controls, and a status check on each lead. Repeat enquiries are detected and handled on purpose.

**Stack.** Retell · Vercel cron (every minute) · Postgres · Google Sheets (service account) · Next.js dashboard

---

## 3. Vertical CRMs with AI built in

**Problem.** Small industrial firms run sales from Outlook and spreadsheets. Generic CRMs don't fit how they quote, and nobody keeps them updated.

**What I built.** A house CRM template, adapted for 15+ industries (aircraft parts, specialty ceramics, packaging, landscaping, printing, legal, fractional services, security software and more):
- **Mailbox sync** with Gmail and Outlook (OAuth), so threads attach to contacts and deals on their own
- **AI-drafted replies and quotes** that read the thread and the firm's catalogue. A human sends them.
- live **lead sync** from the company's website forms every 5 minutes
- importers for existing CRM exports, so a client switches with their full history
- company-wide sign-up links, admin password reset, and a primary domain for each client

**Stack.** Next.js (App Router) · Neon Postgres · Drizzle · Claude · Google & Microsoft OAuth · Vercel

---

## 4. Construction proposal builder

**Problem.** A commercial window & door installer built every bid by hand. They read the architect's schedules, sent the job to manufacturers for quotes, typed the quoted lines into a tally sheet, then wrote a proposal. Each bid took hours, and typing mistakes cost real money.

**What I built.** I mapped the workflow from the owner's own files, then built the pipeline:
- an **architect-schedule reader** that turns plan pages into opening lines
- per-manufacturer **scope sheets** and a quote wizard
- a **Price step** where tally lines × markup + sales tax (two tax methods, with a city tax-rate lookup) produce the base price
- proposal templates written in the owner's own wording, plus job search and "start from an earlier quote"

**Stack.** Next.js · Postgres · Claude (document extraction) · PDF generation

---

## 5. Order-to-cash & AP agents

**Problem.** Manufacturers' finance teams match payments to invoices by hand, chase overdue accounts by email, and type supplier invoices into an old ERP.

**What I built.**
- **O2C:** imports invoices from the ERP's export layout (QuickBooks Desktop), matches lockbox payments, and drafts collections emails by how many days the invoice is overdue
- **AP:** a connector for an IQMS-on-Oracle ERP, invoice capture from Outlook (through Composio), and AP metrics
- handles real-world edge cases such as payments recorded before their invoice, which otherwise break the balances

**Stack.** Next.js · Neon Postgres · ERP connectors · Composio · Claude

---

## 6. LOI desk & follow-up engine

A letter-of-intent generator for a commercial lender, with a pipeline of leads, calls and tasks, rules for logging call outcomes, real document upload, a handoff to the lender's servicing partner, two **signed webhooks**, and email from the lender's own domain with a guard against sending from the wrong address.

**Stack.** Next.js · Postgres · Resend · HMAC-signed webhooks

---

## 7. B2B websites at scale

20+ marketing sites for industrial and professional-services companies:
- **generator-built sites** (one has 102 pages) where the content lives in data files and the HTML is never edited by hand
- redirect migrations that keep SEO when an old site is replaced
- an **AI hero video built at deploy time**: models on Replicate render the clip, with a still-image fallback and a reduced-motion setting
- hosting on Vercel and on Cloudflare R2

---

## Personal projects

- **[Ved-Compass](https://ved-compass.vercel.app)**: an AI study planner that builds a personal study schedule.
