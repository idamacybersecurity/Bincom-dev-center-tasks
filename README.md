# Bincom Dev Center — Tasks Worked On

This repository documents selected cybersecurity and DevSecOps tasks I worked on during my time with **Bincom Dev Center**.

The projects below demonstrate my practical experience in application security, secure CI/CD, security automation, software supply-chain security, container security, policy-as-code, vulnerability management, and cloud security.
---

# 2. Enterprise DevSecOps & Container Supply Chain Security

## Phase 1 — Enterprise DevSecOps Pipeline & Container Supply Chain Security Engine

Designed and implemented an enterprise-style automated security pipeline for containerized applications.

The project applies security controls from development through container build, software supply-chain analysis, image signing, and deployment authorization.

### Key Activities

- Created a multi-service Python container application.
- Containerized API and worker services.
- Implemented automated Python SAST using Bandit.
- Implemented full Git-history secret scanning using TruffleHog.
- Created OPA/Rego security policies.
- Enforced Kubernetes security requirements.
- Enforced Dockerfile security requirements.
- Added centralized pre-commit policy checks using Conftest.
- Generated CycloneDX Software Bill of Materials (SBOMs) using Syft.
- Verified Python dependencies and container base-image packages in the SBOM.
- Scanned SBOMs for known vulnerabilities using Grype.
- Implemented a HIGH/CRITICAL vulnerability security gate for findings with known fixes.
- Published security-approved container images to GitHub Container Registry.
- Implemented keyless container image signing using Cosign/Sigstore.
- Verified signed images using immutable SHA-256 digests.
- Implemented a signed-image deployment authorization gate.
- Tested the deployment gate using an intentionally unsigned image.
- Confirmed that unsigned images could not pass signature verification.
- Configured GitHub Actions OIDC authentication with AWS IAM.
- Verified temporary AWS credential generation without storing long-lived AWS access keys.

### Tools & Technologies

`GitHub Actions` `Docker` `Bandit` `TruffleHog` `OPA` `Rego` `Conftest` `Syft` `CycloneDX` `Grype` `GHCR` `Cosign` `Sigstore` `AWS IAM` `OIDC` `Kubernetes`

🔗 [View Enterprise DevSecOps Phase 1 Project ](https://docs.google.com/document/d/1ODbjnt8f5GdSkER5ngmTdgwZR2VR4kTB/edit?usp=sharing&ouid=117891865848714392423&rtpof=true&sd=true)
---

# 1. Python Source Code Security & CI/CD Automation

This project started with a security assessment of a Python Flask application and progressively developed into an automated Shift-Left security pipeline.

The project was completed across five phases, with each phase introducing additional security controls and automation.


## Phase 5 — Security Automation Enhancements

Extended the security automation with dependency management, framework-specific security policies, secret scanning, and CI/CD workflow improvements.

### Key Activities

- Configured Dependabot for automated dependency monitoring.
- Automated GitHub Actions dependency updates.
- Automated Python dependency monitoring.
- Preserved immutable SHA pinning during automated Actions updates.
- Expanded custom Semgrep policies for Flask/web security.
- Added detection for wildcard CORS configuration.
- Added detection for disabled CSRF protection.
- Added detection for unsafe raw SQL construction.
- Added detection for Flask debug mode.
- Integrated pre-commit secret scanning using detect-secrets.
- Verified secret detection using a controlled fake credential.
- Cleaned up superseded CI/CD workflows.

### Tools & Technologies

`Dependabot` `Semgrep` `detect-secrets` `Pre-Commit` `GitHub Actions` `Python` `Flask`

🔗 [View Phase 5 Project](https://docs.google.com/document/d/19rKBinuTWfiKq4vU0B_OZ50iFDCLDMUC/edit?usp=sharing&ouid=117891865848714392423&rtpof=true&sd=true)

---

## Phase 4 — Supply Chain Hardening & Custom Security Policies

Hardened the existing CI/CD security pipeline and introduced organization-specific security policies and local Shift-Left security controls.

### Key Activities

- Replaced mutable GitHub Actions version tags with immutable commit SHAs.
- Reduced GitHub Actions supply-chain risk.
- Created custom Semgrep security policies.
- Added detection for unsafe `eval()` usage.
- Added detection for unsafe `exec()` usage.
- Added detection for credential-related logging.
- Added detection for insecure random generation.
- Implemented local pre-commit security checks.
- Integrated Bandit and Semgrep into the pre-commit workflow.
- Validated the hardened pipeline through GitHub Actions.

### Tools & Technologies

`GitHub Actions` `Semgrep` `Bandit` `Pre-Commit` `Supply Chain Security` `Policy-as-Code`

🔗 [View Phase 4 Project](https://docs.google.com/document/d/18zj3tJRvWSfn0dxFyTkfHtFSiUrKK6PM/edit?usp=sharing&ouid=117891865848714392423&rtpof=true&sd=true)

---

## Phase 3 — Deep SAST, SCA & GitHub SARIF Integration

Expanded the security pipeline from a single SAST tool into a multi-tool application-security workflow.

### Key Activities

- Integrated Semgrep alongside Bandit.
- Added Software Composition Analysis using pip-audit.
- Generated Bandit and Semgrep results in SARIF format.
- Integrated security findings with GitHub Code Scanning.
- Added Python and OWASP-oriented Semgrep rules.
- Scanned Python dependencies for known vulnerabilities.
- Maintained automated security gates in CI/CD.
- Validated the complete workflow through a pull request.

### Tools & Technologies

`Bandit` `Semgrep` `pip-audit` `SARIF` `GitHub Code Scanning` `SAST` `SCA` `GitHub Actions`

🔗 [View Phase 3 Project](https://docs.google.com/document/d/1TJS_NbyISTUwphPO-TzeyK2_-yDtgkHW/edit?usp=sharing&ouid=117891865848714392423&rtpof=true&sd=true)

---

## Phase 2 — CI/CD Security Automation & SAST Guardrails

Moved the security testing from a manual process into an automated GitHub Actions CI/CD pipeline.

### Key Activities

- Integrated Bandit into GitHub Actions.
- Configured automated SAST on pushes and pull requests.
- Implemented a blocking security gate.
- Tested the pipeline against the vulnerable baseline application.
- Confirmed that insecure code caused the pipeline to fail.
- Remediated the identified security issues.
- Verified the remediated application through a pull request.
- Generated Bandit security reports as workflow artifacts.

### Tools & Technologies

`Git` `GitHub` `GitHub Actions` `Python` `Bandit` `CI/CD` `SAST`

🔗 [View Phase 2 Project](https://docs.google.com/document/d/1jtouyCqhDDJJgtvIhMuXo_TZEcqe0cbt/edit?usp=sharing&ouid=117891865848714392423&rtpof=true&sd=true)

---
## Phase 1 — Python Source Code Security Audit

Performed static analysis and manual source-code review of a Python Flask application.

### Key Activities

- Performed Python SAST using Bandit.
- Conducted manual source-code security review.
- Identified six security findings.
- Investigated SQL Injection risk.
- Investigated OS Command Injection.
- Identified weak MD5 password hashing.
- Reviewed hardcoded secrets and insecure Flask configuration.
- Documented remediation recommendations.

### Tools & Technologies

`Python` `Flask` `Bandit` `SAST` `OWASP`

🔗 [View Phase 1 Project](https://docs.google.com/document/d/1LuoU0R_oLNwzCabguDPLGto5_HtA2GnU/edit?usp=sharing&ouid=117891865848714392423&rtpof=true&sd=true)

---

## About This Repository

This repository serves as a structured record of selected technical tasks and security projects completed during my time with **Bincom Dev Center**.

Each project link provides additional technical details, implementation evidence, security controls, and documentation.

The projects are maintained as part of my practical cybersecurity and DevSecOps portfolio.

---

**Victory Idama**  
Cybersecurity | SOC | Cloud Security | DevSecOps
