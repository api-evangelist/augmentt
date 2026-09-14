---
name: augmentt-tenant-security-posture-brief
description: Produce a client-ready security brief for one managed Microsoft 365 tenant from the Augmentt posture, threat and summary reports, mapped to the compliance frameworks each check satisfies.
api: Augmentt API
spec: openapi/augmentt-api-openapi.yml
operations:
  - listCustomers
  - getCustomer
  - getPostureReport
  - getPostureReportAllCompanies
  - getThreatReport
  - getSummaryReport
generated: '2026-09-14'
method: generated
source: openapi/augmentt-api-openapi.yml + https://support.augmentt.com/kb/en/augmentt-api-548051
---

# Per-tenant security posture brief

Use this to write the security section of a QBR, or to answer "how is this client doing" for one
tenant.

## Steps

1. **Resolve the client.** `listCustomers` → `GET /v1/customers`, match on `customer_name`, take
   `id`. Note `applied_template` — that is the numeric id of the Posture Template applied to this
   customer, or null if none is. The API does not return template *names*, so report the id and say
   so rather than inventing a name. `applied_template` is a Posture Template, not a Compliance Audit
   assessment.
2. **Posture.** `getPostureReport` → `GET /v1/reports/posture/{customerId}`.
   - `postureRecommendations.count` of `.total` is the headline.
   - `configurationStatus` gives `configured`, `partiallyConfigured`, `notConfigured`,
     `notMeasured`, `resolved`.
   - **Exclude `securityChecks.ignored` from every count you present.** Those checks are disabled for
     this tenant, and the documentation states they are already excluded from `allMonitored` and from
     the `configurationStatus` totals. Listing them alongside real failures overstates the problem —
     but *do* mention how many are ignored, because a tenant with many disabled checks has a
     different story than one with none.
   - For each check in `notConfigured` and `partiallyConfigured`, carry through `securityCheck`,
     `checkId`, `category`, `source`, the license requirement, the Microsoft Secure Score impact and
     the compliance details. The `source` field says whether the check is `internal` (built and
     maintained by Augmentt) or comes from an external framework such as maester — a client asking
     "who says so?" is asking about this field.
3. **Benchmark against the estate.** `getPostureReportAllCompanies` → `GET /v1/reports/posture`
   gives the same `postureRecommendations` and `configurationStatus` shape for every company, so you
   can say whether this tenant is ahead of or behind the MSP's own average. One call.
4. **Threat.** `getThreatReport` → `GET /v1/reports/threat/{customerId}` — last 90 days, fixed.
   - `totalRiskDetections` and `riskDetections` by severity.
   - `riskDetections.top5Types` names the actual detection rules that fired.
   - `riskDetections.top5AccountsAtRisk` names the users to talk about.
   - `locationOfRiskDetections.risksByCountry` supports the "where is this coming from" slide.
   - `integrationComplianceScore.value` is the Microsoft Identity Score;
     `integrationSecurityScore` is the Microsoft Secure Score with `count` of `total` points.
5. **Summary / what was prevented.** `getSummaryReport` → `GET /v1/reports/summary/{customerId}` —
   last 90 days, fixed. `totalPreventedIncidents`, `incidentTrends[]`, and the prevented breakdowns:
   risky sign-ins, risky accounts, risky countries, IP addresses, data loss events and legacy auth
   attempts. `preventedIncidents.incidents[]` gives the per-incident `type`, `userImpacted`,
   `action`, `category`, `location` and `date` — this is the evidence behind the headline number.
6. **Map to frameworks.** The compliance details on each posture check say which of CIS Microsoft
   365 Foundations, CISA SCuBA, NIST CSF 2.0, Essential Eight, CMMC or HIPAA the check satisfies.
   Group `notConfigured` checks by framework so a regulated client sees their own framework's gaps
   rather than a generic list. See `conformance/augmentt-conformance.yml`.

## Rules for the brief

- Never present a number without its window. Threat and summary are the **last 90 days**; the API
  takes no date parameters and there is no way to ask for a different period.
- A 404 on a company you can see in the portal usually means the `customerId` came from a different
  organization, or the company is deactivated.
- This API is read-only. Every recommendation in the brief is an action for a human in the Augmentt
  portal; nothing here can remediate.
