# AI Security Best Practices

This document outlines **essential AI security best practices** for teams building, deploying, or operating LLM-powered systems. It is intentionally **short, concise, and production-focused**.

---

## 1. Data & Privacy

- Never send **secrets, credentials, tokens, or passwords** to LLMs
- Mask or anonymize **PII and sensitive user inputs** before model calls
- Use **environment variables or secret managers** for API keys (never hardcode)
- Encrypt sensitive data **at rest and in transit**

---

## 2. Access Control

- Restrict model usage using **Role-Based Access Control (RBAC)**
- Rotate API keys **regularly**
- Use **separate API keys** for dev, staging, and production environments

---

## 3. Prompt & Output Safety

- Validate and sanitize all user inputs
- Protect against **prompt injection** (never blindly append user input)
- Use strict **output validation** (schemas, allowlists)
- Never execute **model-generated code** directly
- Keep **system and user messages separate**
- Do not mix trusted and untrusted content in the same prompt field

---

## 4. Model Usage Controls

- Set **token limits** to prevent abuse
- Configure **spending caps and budget alerts** at the API provider level
- Apply **rate limiting and request throttling**
- Log requests and responses **without sensitive data**

---

## 5. Monitoring & Governance

- Track model usage, latency, and error rates
- Set alerts for **abnormal spikes or misuse**
- Maintain **audit logs** for compliance and investigations
- Require **human review** for high-risk or high-impact outputs
- Periodically review prompts and guardrails for drift
- Define clear ownership for AI systems (no orphaned models)

---

## 6. Tool & Agent Security

- Apply strict **allowlists** for tools and function calls
- Validate tool arguments using schemas and type checks
- Follow **least privilege** for agent permissions
- Never allow models to dynamically select arbitrary tools or endpoints

---

## 7. Output Abuse & Misuse Prevention

- Block executable content (shell commands, SQL, JS) where not required
- Scan outputs for prompt-leakage and policy bypass attempts
- Do not rely on model output for authentication or authorization decisions
- Add post-processing guards for URLs, files, and commands

---

## 8. Training, Fine-Tuning & Data Lifecycle

- Exclude sensitive, confidential, or regulated data from training sets
- Validate datasets for PII leakage before fine-tuning
- Document data sources, ownership, and consent
- Define retention and deletion policies for AI-related data

---

## 9. Supply Chain & Vendor Risk

- Review security posture of AI vendors and SDKs
- Pin dependency versions and monitor updates
- Track model version changes and behavior regressions
- Maintain fallback or degradation strategies for model outages

---

## 10. Incident Response for AI Systems

- Define AI-specific incident categories (leaks, misuse, hallucinations)
- Maintain a kill-switch to disable AI features quickly
- Preserve prompts and outputs for forensic analysis (without sensitive data)
- Feed incident learnings back into prompts and guardrails

---

## Key Takeaways

- Treat LLMs as **untrusted, probabilistic systems**
- Assume user input can be malicious
- Validate inputs, prompts, tools, and outputs
- Security must be layered and continuously reviewed

---



