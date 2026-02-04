Part 3: Incident Response Plan
Scenario:
A support agent reports that they were able to view customer records belonging to a different company by modifying a URL parameter: /customers/view?company_id=1234 → changed to company_id=1235
This indicates a Broken Access Control / IDOR vulnerability affecting multi‑tenant isolation.

1. Immediate Actions (Next 1 Hour)
A. Disable the affected functionality immediately
Temporarily block access to the vulnerable endpoint or feature to prevent further exposure.

B. Revoke active sessions for support agents and low‑privilege roles
Ensures no one continues exploiting the flaw, intentionally or accidentally.

C. Notify internal stakeholders
Alert the CTO, security lead, and engineering team that a potential data breach is in progress.
Preserve logs and evidence. Enable log retention, snapshot relevant API logs, and ensure no automated cleanup occurs.
Assign the role of sealing log archives immediately after collection to a designated security officer to maintain the chain of custody, enhancing the integrity of evidence for future forensic analysis.

E. Confirm the vulnerability
Security engineer reproduces the issue to validate scope and severity.
F. Assess whether the issue affects all tenants
Quickly test a small sample of company IDs to determine if the flaw is systemic.


3. Investigation Checklist
A. How Widespread Is the Vulnerability?
• Test all endpoints that reference company_id, customer_id, or similar identifiers.
• Check both web and mobile API calls for consistent authorisation failures.
• Review backend code to identify where authorisation checks are missing.
• Determine whether the issue affects all roles or only specific ones (e.g., Support Agent).
B. What Data Was Potentially Exposed?
• Identify which objects (customers, appointments, notes, payment history) are accessible across tenants.
• Review logs for cross‑tenant access patterns.
• Determine whether file attachments in S3 were also accessible via predictable URLs.
• Map which companies’ data could have been accessed and by whom.
C. Was It Exploited Before This Report?
• Search logs for unusual patterns such as sequential ID enumeration.
• Look for repeated 200 responses for mismatched company_id values.
• Check for large data downloads or spikes in API traffic.
• Review support agent activity for anomalies.
• Correlate timestamps with suspicious login locations or IP addresses.

4. Root Cause Analysis
Possible Technical Causes
A. Missing server‑side authorisation checks
The backend validates request format but does not verify that the user belongs to the tenant owning the resource.
B. Client-side enforced access control
Authorisation logic may be incorrectly implemented in the frontend instead of the backend.
C. Inconsistent use of middleware or authorisation modules
Some routes may bypass the central authorisation layer due to developer oversight or legacy code.
Most Likely Cause and Why
Missing server-side authorisation checks are the most likely cause. This is the most common root cause of IDOR vulnerabilities in multi-tenant SaaS platforms, especially when endpoints rely on predictable IDs and lack a strict tenant-ownership check in the database query.
An underlying pattern that contributes to this issue is scattered inline SQL queries without tenant filters. Such code can lack proper authorisation logic, making it prone to security breaches. Highlighting this anti-pattern offers developers a clear red flag to systematically refactor the codebase, ensuring all queries consistently verify tenant ownership before executing.

6. Fix Validation Tests
To verify the fix is complete and robust:
A. Negative Access Tests
• Attempt to access another company’s customer records using modified IDs.
• Confirm the API returns 403 Forbidden or equivalent.
B. Role-Based Access Tests
• Test all roles (Admin, Manager, Sales Rep, Support Agent) to ensure correct access boundaries.
• Confirm no role can access another tenant’s data.
C. Multi‑Tenant Isolation Tests
• Validate that every object (customer, appointment, file, note) is tied to a tenant and cannot be accessed cross‑tenant.
D. API-Level Tests
• Use automated scripts to attempt ID enumeration.
• Confirm that all unauthorised requests are blocked and logged.
E. Regression Tests
• Ensure the fix does not break legitimate functionality for valid users.
• Confirm that performance and response times remain acceptable.
