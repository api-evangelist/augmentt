---
title: "Managing GDAP in Multi-CSP Environments: Best Practices for 2026"
url: "https://www.augmentt.com/blog/gdap-multi-csp-environments-best-practice/"
date: "Thu, 26 Mar 2026 13:34:00 +0000"
author: "Levi Rose"
feed_url: "https://www.augmentt.com/feed/"
---
<p>GDAP relationships don&#8217;t scale themselves. What works fine for five customer tenants becomes an operational bottleneck at fifty, and a genuine risk at two hundred when expiring relationships start slipping through the cracks.</p>



<p>Microsoft&#8217;s Partner Center handles GDAP setup well enough for individual relationships, but it wasn&#8217;t built for MSPs managing sprawling multi-tenant environments. This guide covers the mechanics of GDAP relationships, the challenges that compound at scale, and the practices that turn GDAP from administrative overhead into a repeatable, secure foundation for your managed services.</p>



<h2 class="wp-block-heading">What is GDAP and why MSPs need it</h2>



<p>Managing Granular Delegated Admin Privileges (GDAP) across multiple CSP environments comes down to three things: creating standardized role-based access templates, mapping those templates to security groups in your partner tenant, and using Partner Center to handle customer approvals. Instead of granting blanket admin access, you assign only the specific Microsoft Entra roles each technician actually uses—and those assignments expire after a set period.</p>



<p>GDAP replaced Delegated Admin Privileges (DAP), which Microsoft fully deprecated in 2023. The old model gave CSP partners standing Global Administrator access to every customer tenant, indefinitely. GDAP flips that approach entirely.</p>



<ul class="wp-block-list">
<li><strong>GDAP:</strong> Time-bound, role-specific access where partners request only the Entra roles they need, with relationships that expire and require renewal</li>



<li><strong>DAP (deprecated):</strong> The legacy model that automatically granted Global Administrator rights to CSP partners with no expiration</li>



<li><strong>Zero Trust alignment:</strong> GDAP enforces least privilege, meaning partners receive the minimum access required for their work—nothing more</li>
</ul>



<h2 class="wp-block-heading">How GDAP relationships work in Microsoft Partner Center</h2>



<p>A GDAP relationship is essentially a formal agreement between your CSP partner tenant and a customer&#8217;s Microsoft 365 tenant. It spells out which roles your team can use, how long the access lasts, and which security groups can exercise those permissions.</p>



<h3 class="wp-block-heading">GDAP roles and security group assignments</h3>



<p>Here&#8217;s where GDAP differs from what you might expect: it assigns Microsoft Entra roles to security groups, not individual users. You create groups in your partner tenant—something like &#8220;Helpdesk Tier 1&#8221; or &#8220;Security Admins&#8221;—and then assign those groups to the GDAP relationship.</p>



<p>The roles MSPs typically request include Exchange Administrator for mailbox work, Intune Administrator for device policies, User Administrator for account provisioning, and Security Reader for monitoring. Global Administrator? Rarely necessary when you scope roles properly.</p>



<h3 class="wp-block-heading">Least privilege access and role scoping</h3>



<p>Least privilege means requesting only the roles your technicians actually use day-to-day. A helpdesk tech resetting passwords doesn&#8217;t need Exchange Administrator rights. A security analyst reviewing sign-in logs doesn&#8217;t need User Administrator access.</p>



<p>The practical benefit is straightforward: if a technician&#8217;s credentials get compromised, the attacker only gains access to that user&#8217;s limited role assignments—not full administrative control over customer tenants.</p>



<h3 class="wp-block-heading">Cross-tenant access settings for CSP partners</h3>



<p>Cross-tenant access settings control how external organizations, including CSP partners, interact with a customer&#8217;s tenant. When a customer approves a GDAP relationship, they&#8217;re trusting your partner tenant to authenticate users who will access their environment.</p>



<p>Customers can configure inbound access policies to require specific authentication methods from partner users. This explains why enforcing MFA on your partner tenant matters—some customers configure their tenants to reject access from partners without strong authentication.</p>



<h2 class="wp-block-heading">How to set up a GDAP relationship step by step</h2>



<p>The GDAP setup workflow stays consistent, though you&#8217;ll repeat it for each customer tenant. Once you understand the process, you can spot where automation and standardization save the most time.</p>



<h3 class="wp-block-heading">1. Request a GDAP relationship in Partner Center</h3>



<p>In Partner Center, go to Customers, select the customer, and choose &#8220;Request admin relationship.&#8221; You&#8217;ll pick the specific Entra roles you want and set a duration—up to 730 days, or roughly two years. Each customer tenant requires its own separate request.</p>



<h3 class="wp-block-heading">2. Customer approval and admin consent</h3>



<p>Partner Center generates a unique approval link that you send to your customer. A Global Administrator in the customer&#8217;s tenant clicks the link and approves the relationship. If nobody approves within 90 days, the link expires.</p>



<h3 class="wp-block-heading">3. Assign Microsoft Entra roles to security groups</h3>



<p>After approval, you map the granted roles to your internal security groups. This step determines which technicians can actually use the access. You might assign User Administrator to your &#8220;Helpdesk&#8221; group and Security Reader to your &#8220;SOC Analysts&#8221; group.</p>



<h3 class="wp-block-heading">4. Configure GDAP expiration and auto-extension</h3>



<p>GDAP relationships expire based on the duration you set during the request. Auto-extend, when enabled, automatically renews the relationship before expiration with the same role assignments—no customer re-approval required. Without auto-extend, you&#8217;ll request a new relationship and get customer approval all over again.</p>



<h3 class="wp-block-heading">5. Audit GDAP activity logs</h3>



<p>Partner Center logs GDAP relationship changes, and customer tenants log administrative actions taken by partner users. Reviewing these logs helps verify that technicians are using appropriate access and surfaces any unusual activity.</p>



<h2 class="wp-block-heading">GDAP challenges MSPs face in multi-CSP environments</h2>



<p>The GDAP model works fine for individual relationships. But MSPs managing dozens or hundreds of customer tenants run into operational friction that compounds quickly.</p>



<h3 class="wp-block-heading">Manual and repetitive setup across tenants</h3>



<p>Each GDAP relationship requires individual configuration. Partner Center doesn&#8217;t offer native bulk setup, so onboarding 50 new customers means repeating the same workflow 50 times. The manual process introduces inconsistency and eats up technician hours.</p>



<h3 class="wp-block-heading">No bulk role assignment in Partner Center</h3>



<p>After customers approve relationships, you still assign security groups to roles one relationship at a time. For MSPs with large customer bases, this step alone can take hours during onboarding or when adjusting role assignments across your portfolio.</p>



<h3 class="wp-block-heading">Tracking expirations across hundreds of relationships</h3>



<p>GDAP relationships expire. Without centralized tracking, you risk losing access to customer tenants unexpectedly—often discovering the problem only when a technician can&#8217;t complete a support ticket. Microsoft doesn&#8217;t send proactive expiration warnings to partners.</p>



<h3 class="wp-block-heading">Inconsistent role scoping between CSP partners</h3>



<p>When customers work with multiple CSPs—perhaps one for licensing and another for managed services—each partner has independent GDAP relationships with potentially overlapping or conflicting role assignments. This creates confusion about who has what access and complicates security audits.</p>



<figure class="wp-block-table"><table class="has-fixed-layout"><tbody><tr><th>Challenge</th><th>Under DAP</th><th>Under GDAP</th></tr><tr><td>Access scope</td><td>All-or-nothing Global Admin</td><td>Granular role selection</td></tr><tr><td>Duration</td><td>Indefinite</td><td>Time-limited (requires renewal)</td></tr><tr><td>Multi-CSP visibility</td><td>Limited</td><td>Per-relationship tracking required</td></tr><tr><td>Bulk management</td><td>Not applicable</td><td>Not natively supported</td></tr></tbody></table></figure>



<h2 class="wp-block-heading">GDAP security best practices for multi-tenant MSPs</h2>



<p>Standardizing your GDAP approach across all customer tenants reduces risk and makes your security posture auditable. The following practices work whether you manage 20 tenants or 200.</p>



<h3 class="wp-block-heading">Use a third-party multi-tenant tool</h3>



<p>Native Partner Center workflows weren&#8217;t designed for MSP-scale operations. Multi-tenant management platforms centralize GDAP visibility, automate repetitive tasks, and provide the single-pane-of-glass view that Partner Center lacks. <a href="https://augmentt.com" rel="noreferrer noopener" target="_blank">Augmentt&#8217;s Secure Autopilot</a>, for example, surfaces GDAP status alongside security configurations across all your customer tenants from one dashboard.</p>



<h3 class="wp-block-heading">Tiered security groups for L1 L2 and L3 technicians</h3>



<p>Create separate security groups mapped to different GDAP role sets based on technician tier. Your L1 helpdesk team might get Password Administrator and Helpdesk Administrator, while L3 engineers get broader roles like Exchange Administrator or Security Administrator.</p>



<p>This structure lets junior technicians handle routine tasks without accessing sensitive configurations. It also simplifies onboarding—add a new hire to the appropriate group, and they inherit the correct GDAP access across all customers automatically.</p>



<h3 class="wp-block-heading">Standardized least privilege role templates</h3>



<p>Build reusable role templates for common MSP scenarios rather than selecting roles ad-hoc for each customer. A &#8220;Standard Managed Services&#8221; template might include User Administrator, Exchange Administrator, and Intune Administrator. A &#8220;Security Monitoring Only&#8221; template might include just Security Reader and Reports Reader.</p>



<h3 class="wp-block-heading">MFA enforcement and authentication strength policies</h3>



<p>Requiring phishing-resistant MFA for all technicians accessing customer tenants via GDAP is increasingly standard practice. You can configure authentication strength conditional access policies in your partner tenant to enforce this requirement. Customers increasingly audit their CSP partners&#8217; authentication practices, so this protects both sides.</p>



<h3 class="wp-block-heading">Regular access reviews and attestation workflows</h3>



<p>Scheduling quarterly reviews of which security groups have access to which customer tenants helps catch stale assignments. Technicians leave or change roles, and role assignments drift from operational needs over time. Regular reviews support compliance requirements and reduce standing access risk.</p>



<h2 class="wp-block-heading">How to track GDAP expiration and renewals at scale</h2>



<p>Expired GDAP relationships mean lost access at the worst possible time—usually when a customer has an urgent issue. Proactive tracking prevents these disruptions before they happen.</p>



<ul class="wp-block-list">
<li><strong>Partner Center reports:</strong> You can export relationship data manually, but this requires regular attention and doesn&#8217;t provide alerts</li>



<li><strong>PowerShell scripts:</strong> The Partner Center API supports automated queries, though scripts require maintenance as Microsoft updates the API</li>



<li><strong>Third-party multi-tenant platforms:</strong> Centralized dashboards with automated expiration alerts and PSA integration work well here. Augmentt surfaces expiring relationships alongside other tenant health indicators, creating tickets before access lapses.</li>
</ul>



<h2 class="wp-block-heading">How to centralize GDAP visibility across all customer tenants</h2>



<p>A unified view of GDAP status across your entire customer base transforms GDAP from an administrative burden into operational intelligence. Instead of checking relationships one by one, you see everything in context.</p>



<h3 class="wp-block-heading">Unified dashboards for GDAP relationship status</h3>



<p>An effective GDAP dashboard shows relationship status, expiration dates, assigned roles, and customer tenant mapping in one view. You can quickly identify which customers have relationships expiring soon, which have non-standard role assignments, and which lack relationships entirely.</p>



<h3 class="wp-block-heading">Automated alerts for expiring GDAP relationships</h3>



<p>Automated alerting prevents access loss by notifying your team before relationships expire. Effective alerts include the customer name, expiration date, and assigned roles so technicians can take action without researching the relationship details first.</p>



<h3 class="wp-block-heading">PSA integration for GDAP renewal tickets</h3>



<p>Integrating GDAP expiration alerts with your PSA creates actionable tickets that fit your existing workflow. A ticket created 30 days before expiration gives your team time to coordinate with the customer if re-approval is needed—rather than scrambling after access disappears.</p>



<h2 class="wp-block-heading">Turning GDAP into a scalable MSP advantage</h2>



<p>MSPs who standardize and automate GDAP management deliver better security outcomes while reducing operational overhead. The discipline GDAP requires—least privilege roles, time-limited access, documented relationships—aligns with the security practices customers increasingly expect from their partners.</p>



<p>Rather than treating GDAP as a compliance checkbox, consider it infrastructure for your managed services. Consistent role templates, tiered technician access, and centralized visibility become competitive differentiators when customers evaluate their CSP partners&#8217; security maturity.</p>



<blockquote class="wp-block-quote is-layout-flow wp-block-quote-is-layout-flow">
<p><strong>Ready to simplify GDAP management across all your tenants?</strong> Augmentt provides <a href="https://www.augmentt.com/gdap-set-up-and-configuration/" rel="noreferrer noopener" target="_blank">centralized GDAP visibility,</a> automated expiration tracking, and one-click security actions—so your team spends less time in Partner Center and more time delivering value to customers.</p>
</blockquote>



<h2 class="wp-block-heading">FAQs about managing GDAP across multiple CSP environments</h2>



<h3 class="wp-block-heading">Can a customer have GDAP relationships with multiple CSP partners at the same time?</h3>



<p>Yes, a customer tenant can maintain active GDAP relationships with multiple CSP partners simultaneously. Each relationship has independently scoped roles and expiration dates, so one partner might have Exchange Administrator access while another has only Security Reader permissions.</p>



<h3 class="wp-block-heading">What happens to GDAP access when a customer switches CSP providers?</h3>



<p>GDAP relationships are tied to the specific CSP partner tenant, so switching providers requires the new CSP to request a fresh GDAP relationship and the customer to approve it. The old partner&#8217;s relationship remains active until it expires or the customer explicitly removes it.</p>



<h3 class="wp-block-heading">How do I handle GDAP when working with both direct and indirect CSP models?</h3>



<p>Each CSP relationship—whether direct or through a distributor—requires its own GDAP configuration. MSPs operating in both models manage separate relationships per customer, which can mean duplicate setup work for the same tenant.</p>



<h3 class="wp-block-heading">What is the difference between GDAP auto-extend and creating a new GDAP relationship?</h3>



<p>Auto-extend automatically renews an existing GDAP relationship before expiration, preserving the same role assignments without requiring customer re-approval. Creating a new relationship starts fresh, requiring customer approval and manual security group assignment.</p>



<h3 class="wp-block-heading">Which Microsoft Entra roles are required for common MSP tasks under GDAP?</h3>



<p>Common MSP tasks map to specific roles: Exchange Administrator for mailbox management, Intune Administrator for device policies, User Administrator for account provisioning, and Security Reader for monitoring. Global Administrator is rarely necessary when you follow least privilege principles.</p>
