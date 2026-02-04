Broken Multi‑Tenant Access Controls (IDOR / Authorisation Failures) is a high-prioritised risk
• It directly connects to the incident in Part 3, showing consistency in your reasoning
• It’s one of the most common and high‑impact SaaS failures
• It gives a clear, realistic attack path
• There are excellent real‑world examples you can reference
• It demonstrates a strong understanding of SaaS architecture, RBAC, and tenant isolation


Part 2: Deep into the Security Analysis.
Selected Risk: Broken Multi‑Tenant Access Controls (IDOR / Authorisation Failures)

A. Technical Explanation: Step-by-Step Attack Path.
There is a clear attacker's logic and realistic SaaS behaviour.
1. The attacker logs in as a legitimate user. They may be a Sales Rep or Support Agent from Company A with valid credentials.


2. They inspect a request in the web dashboard or mobile app.
For example: /customers/view?company_id=1234&customer_id=9876


3. They modify the company_id or customer_id parameter.
They change it to another value, such as: /customers/view?company_id=1235&customer_id=1001


4. The backend fails to enforce tenant isolation
The API checks whether the request is well‑formed, but not whether the user belongs to that tenant.


5. The attacker receives another company’s customer data.
This may include names, emails, phone numbers, addresses, payment history, and appointment notes.


6. They automate the attack
Using a script or proxy tool (Burp Suite, Postman, Python), they iterate through IDs and extract thousands of records.


7. Full multi‑tenant breach occurs
The attacker can now enumerate all customers across all businesses using the platform.



B. Real-World Example
Incident:
IDOR vulnerability disclosed via HackerOne
Summary: A security researcher identified an IDOR flaw where modifying an object ID in an API request allowed access to another user’s private data. This issue stemmed from missing authorisation checks on object-level resources, a classic multi‑tenant access control failure.
Source: https://hackerone.com/reports/591295



C. Defence Strategy:Three Controls
These are  some of the realistic, implementable, and show strong security reasoning.
Control 1: Enforce Server‑Side Tenant Isolation (Object-Level Authorisation)
• What to implement:
Every API request must validate that the authenticated user belongs to the tenant that owns the requested resource.
Example: SELECT * FROM customers WHERE customer_id = X AND company_id = user.company_id
• Why it works:
It prevents attackers from accessing objects that do not belong to their tenant, even if they tamper with IDs.
• How to verify:
 ◦ Automated tests for every endpoint
 ◦ Negative tests (attempt to access another tenant’s data)
 ◦ Code review focusing on authorisation middleware
• Trade-off:
Requires refactoring if the current architecture mixes tenant logic across services.


Control 2: Replace Incremental IDs with UUIDs
• What to implement:
Use non‑guessable identifiers (UUIDv4) for customer, appointment, and company IDs.
• Why it works:
It prevents the attackers from easily enumerating IDs or guessing valid objects.
• How to verify:
 ◦ Inspect database schema
 ◦ Confirm all API responses and requests use UUIDs
 ◦ Attempt brute‑force enumeration in testing
• Trade-off:
Migration effort and potential impact on legacy integrations.


Control 3: Centralised Authorisation Middleware
• What to implement:
A single middleware layer that enforces RBAC plus tenant checks before any controller logic runs.
• Why it works:
This ensures consistent authorisation across all endpoints and reduces developer error.
• How to verify:
 ◦ Unit tests for middleware
 ◦ API gateway logs
 ◦ Security review of routes to confirm no bypasses
• The trade-off:
Adds complexity and may require restructuring existing routes or services.
