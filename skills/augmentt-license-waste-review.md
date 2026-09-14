---
name: augmentt-license-waste-review
description: Reconcile Augmentt module seat consumption against Microsoft 365 license assignment and activity across every managed tenant, and surface the seats a client is paying for and not using.
api: Augmentt API
spec: openapi/augmentt-api-openapi.yml
operations:
  - listCustomers
  - listCustomerLicenses
  - getMicrosoftLicenseReportAllCompanies
  - getMicrosoftLicenseReport
  - getThreatReport
generated: '2026-09-14'
method: generated
source: openapi/augmentt-api-openapi.yml + https://support.augmentt.com/kb/en/augmentt-api-548051
---

# License waste review

Use this for a quarterly business review, a renewal conversation, or "where can this client cut
Microsoft spend".

## The single most important distinction

There are two different things called "licenses" and confusing them will produce a wrong number:

- `GET /v1/customers/licenses` (`listCustomerLicenses`) — **your** Augmentt module consumption:
  Secure, Engage and Discover seat counts. This is what the MSP is billed for by Augmentt.
- `GET /v1/reports/license` (`getMicrosoftLicenseReportAllCompanies`) — the **client's** Microsoft
  365 licensing: plan names, enabled vs assigned counts, renewal dates.

## Steps

1. `listCustomers` → `GET /v1/customers`. Build the id → name map. Drop anything with `deactivated`
   set.
2. `listCustomerLicenses` → `GET /v1/customers/licenses`. For each company you now have
   `licenses[]` of `{type, count}` where type is `discover`, `engage` or `secure`.
3. `getMicrosoftLicenseReportAllCompanies` → `GET /v1/reports/license`.
   **Check the response before anything else.** If it is an empty array `[]` with a 200, the
   organization is not subscribed to *Licensing Report Essentials* — that is an entitlement gap, not
   an empty estate, and you must stop and say so rather than reporting zero waste.
4. **Check the snapshot flags before comparing months.** `missingLatestMonth` and
   `missingPreviousMonth` tell you whether the month-over-month delta is real. The report is
   generated against the current day, so the latest month is frequently not finalized. If
   `missingLatestMonth` is true, compare the two previous months instead and say which months you
   used.
5. **Compute the gap per plan** from `overview`: for each plan in `overview.assigned[]`, compare
   `count` (assigned) against `total` (owned in the subscription). Owned-minus-assigned is unassigned
   spend. Use `overview.licenses[].change` and `trends[]` to say whether it is growing.
6. **Drill into the companies worth drilling into.** For a company with a large gap,
   `getMicrosoftLicenseReport` → `GET /v1/reports/license/{customerId}` adds `renewals[]` — the
   `nextLifecycleDateTime`, `estimatedCommitment` and `subscriptionTotalLicenses` per subscription.
   A gap that can be acted on before the next renewal date is the only gap worth raising.
7. **Cross-check against activity, not just assignment.** An assigned licence on a dormant account is
   the most defensible cut. `getThreatReport` → `GET /v1/reports/threat/{customerId}` returns
   `atRiskAccounts.inactiveAccounts` with an `overview` of inactive vs active and `accounts[]`
   carrying `lastActive`, `isInactive`, `licenseType[]` and `lastAppActivity`. Intersect
   `isInactive: true` with a non-empty `licenseType[]`.
8. **Dedupe before you count.** The same user appears in up to three buckets of
   `atRiskAccounts` (mfaStatus, mfaRegistration, inactiveAccounts). Join on `userId`.
9. **Output.** Per client: Augmentt seats by module; Microsoft plans owned vs assigned vs active;
   the inactive-but-licensed user list; the next renewal date per affected subscription; and an
   explicit note of which months the comparison used.

## Constraints to state in the output

- The reporting period is fixed. The API accepts no date-range parameters; threat and summary data
  are the last 90 days and the license report is a current-day snapshot.
- Deactivated companies are excluded from every roll-up, so the totals describe active clients only.
