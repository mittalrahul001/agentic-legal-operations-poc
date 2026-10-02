# Security and responsible-use notes

## POC boundary

- All examples use synthetic data.
- The workflow performs no production system writes.
- No API keys, n8n credentials, local databases, execution logs, or environment files are included.
- Approval rules are illustrative and must not be treated as legal advice or corporate policy.

## Production requirements

- Classify and minimize data before model access.
- Use an approved model gateway with explicit retention and training controls.
- Enforce SSO, MFA, RBAC/ABAC, purpose limitation, and separation of duties.
- Apply privilege-aware access controls and legal-hold/retention requirements.
- Keep sensitive payloads out of routine application and telemetry logs.
- Treat messages and attachments as untrusted input and defend against prompt injection.
- Give agents read/propose access only; grant short-lived write credentials exclusively to bounded connector services after approval.
- Bind approvals to an immutable case version and hash.
- Use idempotency keys, durable checkpoints, read-back reconciliation, and tested recovery procedures.

## Reporting

This is an interview prototype, not a deployed service. Please do not submit real employee, customer, privileged, confidential, or regulated information.

