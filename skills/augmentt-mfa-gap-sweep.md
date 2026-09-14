---
name: augmentt-mfa-gap-sweep
description: Find every user across every managed Microsoft 365 tenant who is not protected by MFA, and produce a per-client remediation list an MSP technician can work.
api: Augmentt API
spec: openapi/augmentt-api-openapi.yml
operations:
  - listCustomers
  - getMfaReportAllCompanies
  - getMfaReport
generated: '2026-09-14'
method: generated
source: openapi/augmentt-api-openapi.yml + https://support.augmentt.com/kb/en/augmentt-api-548051
---

# MFA gap sweep across every managed tenant

Use this when someone asks "who still isn't on MFA?" across an MSP's whole book of clients.

## Before you start

- Base URL is regional. Use `https://api.augmentt.com` (NAM), `https://api.eu.augmentt.com` (EU) or
  `https://api.apac.augmentt.com` (APAC) — whichever region the Augmentt workspace lives in.
- Send **both** headers on every request: `AccessKeyId` and `AccessKeySecret`. Exact spelling, no
  surrounding whitespace. A 401 `UNAUTHORIZED` almost always means one of those two things.
- Every operation here is a GET with no request body and no side effects. Nothing you do can change
  state in a client tenant — this API cannot reset MFA, issue a Temporary Access Pass or offboard a
  user. Remediation happens in the Augmentt portal, not here.

## Steps

1. **Get the shape of the estate.** `listCustomers` → `GET /v1/customers`. Keep `id` (this is the
   `customerId` every later call needs), `customer_name`, `parent_id` and `deactivated`.
   Companies with a non-null `deactivated` are excluded from report data — drop them now rather than
   chasing a 404 later.
2. **Take the roll-up first.** `getMfaReportAllCompanies` → `GET /v1/reports/mfa`. Read
   `mfaStatus.protected`, `mfaStatus.notProtected` and `mfaStatus.signInBlocked` for the whole
   estate, and the same three fields per company in `companies[]`. Rank companies by
   `notProtected` descending — that is your work queue. One call, whole estate; do not loop the
   per-company endpoint to build this.
3. **Pull detail only for the companies that need work.** For each company at the top of the queue,
   `getMfaReport` → `GET /v1/reports/mfa/{customerId}`. Walk `employees[]` and select users where
   `mfaStatus.status` is not `PROTECTED`.
4. **Separate the two kinds of gap, because they have different fixes.**
   - `mfaRegistration` is `Not Registered` → the user has never enrolled. Fix is enrollment.
   - `mfaRegistration` is `Registered` but `mfaStatus.status` is not `PROTECTED` → enrolled but not
     *enforced*. Fix is policy, and `mfaConfigurations.configurationStatus` plus
     `mfaConfigurations.affectedByCAs[]` tells you which Conditional Access policy should have caught
     them (or that none does).
5. **Do not report sign-in-blocked users as a risk.** `signInBlocked` means the account cannot be
   accessed at all. The documentation is explicit that this state is safe even though it is not
   MFA-compliant. Counting it as a gap inflates every number you give the client.
6. **Flag the privileged ones first.** Sort the unprotected set by `role[]` — anything containing
   `Global Administrator` goes to the top of the remediation list regardless of tenant size.
7. **Output.** One row per user: company name, `email`, `displayName`, `role[]`, `licenseType[]`,
   `mfaStatus.status`, `mfaRegistration`, `authenticationType[]`, and the CA policy names from
   `affectedByCAs[]`. Group by company, ordered by the company's `notProtected` count.

## Errors you will actually hit

| Status | Code | What to do |
|---|---|---|
| 401 | `UNAUTHORIZED` | Check both header names and strip whitespace from the values. |
| 403 | `FORBIDDEN` | Keys are fine; API access is not enabled for this organization. Contact Augmentt support — you cannot fix this in code. |
| 404 | `NOT_FOUND` | The `customerId` is not in this organization, or the path is wrong. Re-read it from `/v1/customers`. |
| 500 | `INTERNAL_ERROR` | Retry. It is the only retriable status here. |

No rate limits are published, and no `Retry-After` or `RateLimit-*` header is returned. Pace the
per-company loop conservatively and back off on 500s.
