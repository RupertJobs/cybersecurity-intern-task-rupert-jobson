Part 1 — Threat Model & Attack Surface Analysis
1. Five Most Critical Attack Surfaces
Attack Surface 1: Authentication & Session Management
Credential stuffing, weak passwords, lack of MFA, or stolen session tokens enable attackers to take over accounts.
• Impact:
Account takeover of Admin or Manager roles leading to full access to customer data, configuration changes, and potential data exfiltration.
• Likelihood:
Medium: Credential attacks are common, but mitigations like rate limiting or MFA may already exist.


Attack Surface 2: Multi‑Tenant Access Controls (Authorisation Layer)
• Threat:
Broken access controls or IDOR flaws enable one business to access another's customer data by adjusting IDs in URLs or APIs.
• Impact:
Severe data breach exposing PII, appointment notes, payment history, and internal communications across multiple clients.
• Likelihood:
High: Multi‑tenant authorisation bugs are extremely common in young SaaS products, especially those built quickly.



Attack Surface 3: API Backend & Third‑Party Integrations
• Threat:
Insecure API endpoints, missing authorisation checks, weak input validation, or compromised third‑party integrations (email/calendar sync).
• Impact:
Data extraction at scale, unauthorised API calls, or attackers abusing integrations to send emails or pull sensitive data.
• Likelihood:
Medium: APIs expand the attack surface and are often less tested than the UI.



Attack Surface 4: Cloud Infrastructure & Data Storage (AWS RDS + S3)
• Threat:
Misconfigured S3 buckets, overly permissive IAM roles, exposed database credentials, or insecure network configurations.
• Impact:
Full database compromise, mass data exfiltration, or ransomware-style destruction of stored files.
• Likelihood:
Low-Medium: AWS defaults are secure, but misconfigurations remain one of the top causes of cloud breaches.



Attack Surface 5: Web & Mobile Client Interfaces
• Threat:
XSS, insecure local storage, exposed API keys, or tampering with client-side logic to bypass restrictions.
• Impact:
Session hijacking, unauthorised actions, or leakage of sensitive customer data through compromised browsers or mobile devices.
• Likelihood:
Medium: Web/mobile apps are frequently targeted, but the impact depends on the specific vulnerabilities present.



2. Top 3 Prioritised Risks
Based on impact likelihood, the top three risks are:
1. Broken Multi‑Tenant Access Controls (IDOR / Authorisation Failures)
2. Authentication & Session Management Weaknesses
3. Insecure API Backend & Third‑Party Integrations


3. Prioritisation Logic
These three risks were prioritised because:
1. Multi‑tenant access control failures pose the highest impact and highest likelihood.
A single IDOR or authorisation flaw can expose every client’s data to every other client, which is catastrophic for a SaaS CRM.
This is also directly supported by the incident scenario in Part 3, meaning the platform already exhibits signs of this weakness.
2. Authentication weaknesses enable full account takeover.
If attackers compromise Admin or Manager accounts, they gain unrestricted access to customer records, appointments, and communication tools.
Credential attacks are extremely common, making this a high‑value target.
3. API vulnerabilities allow scalable, automated exploitation.
APIs often bypass UI‑level protections and are easier to script against.
A single insecure endpoint can quickly leak thousands of records, making this a high-impact, medium-likelihood risk.
Why do these matters arise before onboarding enterprise clients
Enterprise customers expect strong tenant isolation, robust authentication, and secure APIs.
Any weakness in these areas would:
• Block enterprise adoption
• Create regulatory exposure (GDPR)
• Damage trust and reputation
• Lead to high‑severity breaches involving sensitive customer data
These three risks represent the most realistic, most damaging, and most urgent issues for a growing SaaS CRM platform.
