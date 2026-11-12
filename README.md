# Bincom Dev Center — Tasks Worked On

This repository documents selected cybersecurity and DevSecOps tasks I worked on during my time with **Bincom Dev Center**.

The projects below demonstrate my practical experience in application security, secure CI/CD, security automation, software supply-chain security, container security, policy-as-code, vulnerability management, and cloud security.

----
# 2. Enterprise DevSecOps & Container Supply Chain Security
---

# AI Agent Runtime Security — eBPF Runtime Tool Guardrails & Autonomous Threat Interception

Designed, implemented, and tested an AI Agent Runtime Security framework within the Enterprise DevSecOps Pipeline to evaluate AI agent tool-abuse risks, enforce application-level security guardrails, detect runtime activity through eBPF instrumentation, and automate container-level security response. The framework combines a locally hosted Ollama language model, Python-based AI agent tooling, adversarial testing, protected tool execution, Cilium Tetragon runtime monitoring, and a guarded Python SOAR response bridge.

### Key Activities

- Designed and implemented a Python-based AI agent runtime security framework using a locally hosted Ollama model (`llama3.2`) and controlled tool-execution capabilities.
- Deployed Ollama using Docker with persistent model storage, a loopback-bound API endpoint, and dedicated Docker networking.
- Developed a baseline AI agent with file-reading and shell-execution capabilities to establish an application-level security reference before introducing tool guardrails.
- Configured the baseline agent container with isolation controls, including a non-root execution identity (`10001:10001`), read-only root filesystem, dropped Linux capabilities, `no-new-privileges`, and resource limits.
- Created synthetic workspace fixtures containing simulated private information to support controlled security experiments without exposing real credentials.
- Implemented a red-team testing harness covering indirect prompt injection, file-path parameter tampering, unrestricted shell execution, and protected-file access.
- Demonstrated that the baseline agent could read a synthetic private workspace file, access an external path (`/etc/hostname`), and execute an unrestricted shell command that created a marker file.
- Tested indirect prompt injection through a poisoned document and observed response contamination, while confirming that unauthorized tool execution was not established in that test.
- Verified that an attempted `/etc/shadow` read was denied by the container operating-system permission boundary.
- Developed a protected Python agent with fixture-only file access, path-traversal protection, symbolic-link escape prevention, and restrictions against non-regular file access.
- Replaced unrestricted shell execution with an explicit command allowlist permitting only `pwd` and `ls`.
- Implemented shell-syntax validation to reject command chaining, pipes, redirection, command substitution, background execution, and other unauthorized shell operations.
- Implemented fail-closed JSONL audit logging, ensuring that operations requiring audit persistence are rejected when the required audit write fails.
- Executed the protected-agent unit-test suite and verified that all 14 tests passed.
- Performed live model-driven protected-agent tests, confirming that approved fixture access remained functional while unauthorized private-file access and `touch` execution requests were denied.
- Verified that the expected marker file was absent after the unauthorized shell-execution attempt.
- Deployed Cilium Tetragon v1.8.0 to provide kernel-level runtime observability through eBPF instrumentation.
- Verified host readiness for eBPF monitoring, including BTF kernel information, required BPF kernel options, and the cgroup v2 filesystem.
- Developed a Tetragon tracing policy targeting `security_file_permission` events involving write-permission requests under `/workspace/output`.
- Configured the Tetragon policy in monitor-only mode to capture matching runtime events without blocking the underlying operation.
- Successfully captured a real kernel-level write-permission event from a disposable test container.
- Preserved the captured event in `evidence/tetragon/workspace-write-event.jsonl`, including policy, kernel hook, process, user, file path, permission, return value, and container metadata.
- Developed a Python SOAR bridge to validate incoming Tetragon security events and verify target-container eligibility before initiating quarantine.
- Implemented event-validation controls covering policy identity, kernel hook, action, file path, permission value, return value, and container identity.
- Implemented target-container validation covering expected container identity, image, security labels, running state, and required isolation characteristics.
- Established dry-run-by-default behavior and explicit `--execute` authorization for live quarantine operations.
- Configured the bridge to stop eligible disposable containers and verify their resulting state before reporting quarantine success.
- Executed the SOAR regression-test suite and verified that all 14 tests passed.
- Performed live SOAR testing and successfully quarantined the designated disposable container, `ai-agent-soar-test`.
- Verified the quarantined container's final state through Docker inspection, confirming `exited` with exit code `137`.
- Recorded a SOAR bridge reaction time of 2.570 seconds, measured after event receipt rather than as complete end-to-end detection and response latency.
- Demonstrated live Tetragon event ingestion by the SOAR bridge and quarantine execution.
- Documented the evidence-correlation qualification: the separately preserved Tetragon event and quarantine records contain different container IDs and therefore do not independently establish a matching event-to-container chain.
- Preserved protected-agent audit logs, red-team results, Tetragon kernel-event evidence, SOAR execution logs, Docker inspection results, and supporting screenshots.
- Performed integrity checks covering the original baseline agent source, protected-package SHA-256 checksums, Python compilation, and expected evidence files.
- Verified that the Tetragon JSONL evidence contained a valid JSON object and that the project README passed `git diff --check`.
- Completed project documentation and published the implementation, tests, integrity records, and supporting evidence to the remote GitHub repository.
- Committed and successfully pushed the project to the repository's `main` branch in commit `79bfa88`, containing 27 changed files and 2,337 insertions.

### Tools & Technologies

`Python` `Ollama` `llama3.2` `AWS EC2` `Ubuntu Linux` `Docker` `Docker Networking` `Linux Security` `Cilium Tetragon v1.8.0` `eBPF` `Kubernetes` `k3s` `Bash` `Shell Security` `JSONL Audit Logging` `SOAR` `Runtime Threat Detection` `Prompt Injection Testing` `Red-Team Testing` `Application-Level Guardrails` `Path Traversal Protection` `Symbolic-Link Protection` `Container Isolation` `Automated Quarantine` `Regression Testing` `SHA-256` `Git` `GitHub`

 [View AI Agent Runtime Security — eBPF Runtime Tool Guardrails & Autonomous Threat Interception](https://docs.google.com/document/d/1G_wSZ9JkTDwEVgGWWuQSNWwUq0W9GLCr/edit?usp=sharing&ouid=117891865848714392423&rtpof=true&sd=true)
 
---
# Automated Security Chaos Engineering — Fault Injection & Control Resilience Testing

Implemented and tested an automated security chaos-engineering framework within the Enterprise DevSecOps Pipeline to evaluate the resilience, availability, recovery behavior, and security enforcement of Kubernetes security controls. Developed guarded Python-based fault-injection playbooks for Kyverno admission control, Loki security telemetry, and Tetragon runtime protection. The framework incorporates dry-run-by-default execution, explicit authorization for live experiments, bounded fault windows, baseline validation, automated recovery checks, rollback verification, machine-readable evidence, and a read-only resilience auditor.

### Key Activities

- Designed and implemented a Python-based chaos-engineering framework for controlled fault injection and security-control resilience testing on an AWS EC2-hosted, single-node k3s Kubernetes cluster.
- Established a centralized safety configuration with dry-run-by-default behavior, explicit `--execute` authorization, protected namespace allowlists, experiment-specific fault-duration limits, baseline checks, recovery validation, and rollback safeguards.
- Implemented three primary fault-injection playbooks targeting:
  - Kyverno admission-controller and webhook availability.
  - Loki security-telemetry service recovery.
  - Tetragon runtime-security agent self-healing.
- Implemented a Kyverno admission recovery experiment that temporarily interrupts Service endpoint availability, tests fail-closed admission behavior, restores the original Service selector, and verifies endpoint and admission recovery.
- Executed four final Kyverno acceptance runs successfully. Each validated the outage-window rejection behavior, Service selector restoration, endpoint recovery, and post-recovery admission.
- Verified that all five admission requests in each final Kyverno acceptance run were rejected during the tested webhook outage, with the expected `FAIL_CLOSED_WEBHOOK_UNAVAILABLE` result.
- Recorded Kyverno controller and endpoint recovery times, with final acceptance runs recovering approximately 19.7–22.0 seconds after the restoration command.
- Retained the complete Kyverno experiment history in the results directory. The historical inventory contains 15 records: four validated successful runs and eleven unsuccessful development records, comprising four live runs that accepted at least one outage-window request and seven aborted attempts.
- Implemented a Loki telemetry recovery experiment that introduced a bounded 15-second service outage and validated service restoration, persistent-volume availability, and dependent telemetry components.
- Verified Loki recovery in 42.044 seconds after restoration, with the Loki StatefulSet returning to Ready, the 5 GiB persistent volume remaining Bound, and Alloy and Tetragon passing post-recovery health checks.
- Implemented a Tetragon recovery experiment that deleted a verified runtime-security agent pod and measured automatic DaemonSet replacement and readiness.
- Verified Tetragon agent recovery in 24.354 seconds, within the configured 60-second recovery limit.
- Integrated runtime-security detection evidence from Tetragon, including controlled shell-execution activity, and verified delivery of a test security alert through the Slack ChatOps escalation bridge.
- Preserved runtime-security detection and recovery evidence in machine-readable JSON results and supporting screenshots.
- Added bounded Prometheus monitoring evidence and resource observations to support post-experiment assessment of the security and telemetry components.
- Developed a read-only Python resilience auditor to inspect saved experiment records, validate recovery and fail-closed evidence, evaluate detection fidelity, and generate a machine-readable resilience scorecard.
- Extended the auditor with a separate Kyverno acceptance-campaign assessment so that the four designated final acceptance records could be evaluated independently from the complete historical experiment inventory.
- Verified the final resilience scorecard:
  - Availability: `PASS`
  - Detection Fidelity: `PASS`
  - Fail-Closed Integrity: `PASS`
- Preserved the separate Kyverno historical inventory result as `MIXED_RESULTS`, ensuring that unsuccessful development records were not hidden by the final acceptance result.
- Disclosed that the four-record acceptance campaign and corresponding auditor extension were designated after the results were known. The auditor also records that this designation timing is an operator/process assertion rather than something it independently verifies.
- Documented operational limitations, including the absence of a sustained telemetry-loss or storage-saturation test, the inability to establish uninterrupted Tetragon detection while its agent was unavailable, and the limited scope of the Prometheus resource snapshot.
- Verified that the final auditor operates in read-only mode and does not perform fault injection or modify Kubernetes resources.
- Restored the Kyverno execution safety switch to `False` after live testing and verified that subsequent `--execute` attempts are blocked when the switch is disabled.
- Committed and pushed the final auditor implementation and generated audit results to the repository's `main` branch.
- Preserved experiment scripts, safety configuration, Kubernetes manifests, JSON results, auditor output, screenshots, and supporting project evidence under the `chaos-engineering/` directory.

### Tools & Technologies

`Python` `AWS EC2` `Ubuntu Linux` `Kubernetes` `k3s` `Kyverno v1.19.2` `Kyverno Admission Control` `Tetragon` `Tetragon v1.8.0` `Grafana Loki 3.7.9` `Grafana Alloy v1.21.3` `Prometheus` `LogQL` `Slack ChatOps` `Systemd` `Bash` `kubectl` `Helm` `JSON` `Git` `GitHub` `Chaos Engineering` `Fault Injection` `Security Observability` `Runtime Threat Detection` `Fail-Closed Validation` `Automated Recovery Testing` `Resilience Auditing`

 [View Automated Security Chaos Engineering — Fault Injection & Control Resilience Testing](https://docs.google.com/document/d/1P8XHFDfscmyjQ1A8WHorMcPXfu4p1UYb/edit?usp=sharing&ouid=117891865848714392423&rtpof=true&sd=true)

---
## Advanced Runtime Threat Observability, eBPF Tetragon Security Telemetry & Automated ChatOps Escalation

Implemented and tested an advanced runtime threat observability and automated security alerting pipeline within the Enterprise DevSecOps Environment. Deployed Cilium Tetragon to capture kernel-level security events using eBPF, developed custom TracingPolicies to detect suspicious process execution and sensitive-file access attempts, and integrated Grafana Alloy with Loki for centralized security telemetry. Developed a Python-based ChatOps bridge to process Tetragon events and deliver structured, real-time security notifications to Slack.

### Key Activities

- Deployed Cilium Tetragon v1.8.0 into the AWS EC2-based k3s Kubernetes environment using Helm.
- Configured Tetragon to integrate with the Kubernetes API and k3s containerd runtime socket.
- Enabled process credential and namespace monitoring to enrich runtime security events with workload and process metadata.
- Verified Tetragon agent and operator deployment, eBPF sensor initialization, BTF discovery, and JSON event export.
- Configured persistent Tetragon JSON event logging at `/var/log/tetragon/tetragon.log`.
- Created a custom TracingPolicy named `namespace-escape` to identify suspicious executable activity associated with namespace escape and privilege escalation.
- Configured detection selectors for:
  - `nsenter` execution.
  - `unshare` execution.
  - Executables launched from `/tmp/`.
  - Executables launched from `/dev/shm/`.
- Deployed controlled Kubernetes test workloads and executed all four targeted namespace-related detection scenarios.
- Verified that all four namespace-escape policy selectors generated matching Tetragon events, with zero missed program probes reported.
- Created a second custom TracingPolicy named `sensitive-file-write` to monitor write-capable `openat2` operations targeting sensitive filesystem locations.
- Configured monitoring for:
  - `/etc/passwd`
  - `/etc/shadow`
  - `/var/run/secrets/`
- Used controlled workloads and direct `openat2` syscall tests to generate sensitive-file access events.
- Verified matching Tetragon events for the tested sensitive paths, including write-capable open attempts against protected secret locations.
- Deployed Grafana Loki for centralized storage and querying of runtime security telemetry.
- Configured Grafana Alloy as a DaemonSet to collect Tetragon JSON logs from the host filesystem and forward them to Loki.
- Verified successful log ingestion into Loki using the `{job="tetragon"}` stream selector.
- Developed LogQL queries to isolate process execution events, namespace-escape detections, sensitive-file write-capable open attempts, and combined security-policy events.
- Validated all four LogQL queries and confirmed that the combined policy query returned ten matching security events in the inspected dataset.
- Developed a Python-based ChatOps bridge named `tetragon_chatops_bridge.py` to consume Tetragon events through the official `tetra` CLI and Tetragon gRPC event stream.
- Implemented event parsing and classification for:
  - Kubernetes shell execution.
  - Namespace-escape policy detections.
  - Sensitive-file write-capable open attempts.
- Enriched security notifications with available workload and process metadata, including timestamps, node, pod, namespace, container ID, image information, process binary, parent process, command, UID/GID, PID, and policy details.
- Implemented Slack Block Kit message formatting with structured security event fields and readable line breaks.
- Integrated the Python bridge with Slack using an incoming webhook secured outside the Git repository.
- Added retry handling and HTTP response validation for Slack notification delivery.
- Implemented event filtering to ensure shell-execution alerts are associated with Kubernetes workloads containing pod and namespace metadata.
- Added command-line testing options, including `--dry-run` and `--max-events`.
- Executed six synthetic validation tests covering host-level shell filtering, Kubernetes shell acceptance, policy event preservation, and Slack message formatting. All six tests passed.
- Verified dry-run processing for shell-execution, namespace-escape, and sensitive-file detections.
- Configured the ChatOps bridge as a systemd service to support persistent event monitoring and automated notification delivery.
- Verified successful live Slack notification delivery following service recovery.
- Captured Slack alert evidence, including two MEDIUM-severity alerts and one HIGH-severity alert.
- Investigated a temporary Kubernetes control-plane responsiveness incident that affected API communication, health checks, and event-stream continuity.
- Restored service health and verified successful recovery through a subsequent live Slack notification test.
- Validated Python syntax using `python3 -m py_compile` and checked repository changes using `git diff --check`.
- Updated the GitHub repository with the runtime security policies, observability configuration, LogQL queries, and Python ChatOps bridge.
- Committed and successfully pushed the implementation to the `main` branch.
- Preserved policy manifests, deployment configurations, test outputs, Slack alert screenshots, and supporting evidence for the runtime security report.

### Tools & Technologies

`Cilium Tetragon v1.8.0` `eBPF` `Linux Kernel Tracing` `BTF` `TracingPolicy CRDs` `Kubernetes` `k3s` `AWS EC2` `Helm` `containerd` `Grafana Alloy v1.21.3` `Grafana Loki v3.7.9` `LogQL` `Python 3` `Tetragon gRPC` `tetra CLI` `Slack API` `Slack Block Kit` `Incoming Webhooks` `systemd` `Linux` `Bash` `Git` `GitHub` `Runtime Threat Detection` `Security Telemetry` `Automated ChatOps` `Security Incident Alerting`

 [View Advanced Runtime Threat Observability, eBPF Tetragon Security Telemetry & Automated ChatOps Escalation](https://docs.google.com/document/d/14GGgz3sXjYkhvY49hyv7JqIgWkADro-7/edit?usp=sharing&ouid=117891865848714392423&rtpof=true&sd=true)
 
---

## Phase 8 — Container Supply Chain Attestation, Cosign Cryptographic Image Signing & In-Cluster Kyverno Verification

Implemented and tested container supply-chain security controls within the Enterprise DevSecOps Pipeline using Cosign, Syft, Grype, and Kyverno. Strengthened container image integrity and software provenance by introducing cryptographic image signing, Software Bill of Materials (SBOM) generation, vulnerability scanning, and admission-time verification before workloads are allowed to run in Kubernetes.

### Key Activities

- Integrated Cosign v3.1.3 into the GitHub Actions container build workflow to digitally sign application images using GitHub OIDC keyless signing.
- Built and published API and worker container images to Amazon ECR and GitHub Container Registry (GHCR).
- Resolved immutable image digests and used digest-based signing and verification to establish image authenticity.
- Generated CycloneDX Software Bill of Materials (SBOMs) for API and worker images using Syft v1.55.0.
- Integrated Grype vulnerability scanning into the CI workflow to scan generated SBOMs.
- Created and attached CycloneDX SBOM attestations to the corresponding container image digests using Cosign.
- Successfully verified image signatures and SBOM attestations against the expected GitHub Actions OIDC issuer and workflow identity.
- Generated a separate encrypted Cosign static keypair and secured the private key through restricted directory and file permissions.
- Demonstrated manual digest-based image signing and public-key verification using a local Docker Registry.
- Deployed Kyverno v1.19.2 and implemented an `ImageValidatingPolicy` named `require-signed-devsecops-images`.
- Configured Kyverno with `validationActions: Deny` and `failurePolicy: Fail` to enforce fail-closed verification for the configured application image repositories.
- Configured admission-time verification of Cosign image signatures and CycloneDX SBOM attestations.
- Performed positive and negative admission tests, confirming that:
  - A properly signed and attested API image was admitted and reached `Running` status.
  - An unsigned container image was rejected because no valid Cosign signature was found.
  - A modified image digest was rejected because the registry could not find the requested manifest.
- Verified Kyverno policy readiness and reviewed admission-controller logs to confirm signature and attestation verification results.
- Updated the GitHub repository with the Phase 8 Kyverno policy and improved unsigned-image verification workflow.
- Verified that the Phase 8 repository changes were pushed successfully to the `main` branch.
- Preserved policy manifests, CI workflow evidence, verification outputs, screenshots, and supporting documentation for the Phase 8 report.

### Tools & Technologies

`Cosign v3.1.3` `Syft v1.55.0` `Grype` `Kyverno v1.19.2` `ImageValidatingPolicy` `CycloneDX` `SBOM` `GitHub Actions` `GitHub OIDC` `Amazon ECR` `GHCR` `Docker` `Kubernetes` `k3s` `AWS EC2` `Linux` `kubectl` `Container Supply Chain Security` `Cryptographic Image Signing` `Software Provenance` `Admission Control`

 [View Phase 8 — Container Supply Chain Attestation](https://docs.google.com/document/d/1Z-NLJiiEMqbZUmZK4qRYQUBV9o3mztQf/edit?usp=sharing&ouid=117891865848714392423&rtpof=true&sd=true)

---
## Phase 7 — Zero Trust Service Mesh, Envoy L7 Telemetry & OPA Gatekeeper Guardrails

Implemented and tested Zero Trust security controls in a local Kubernetes environment using Istio, Envoy, and OPA Gatekeeper, strengthening workload-to-workload security through mutual TLS (mTLS), identity-based authorization, Layer 7 telemetry, and Kubernetes admission guardrails.

### Key Activities

- Deployed Istio 1.31.1 in a local k3s Kubernetes cluster and configured Envoy sidecar injection for selected workloads.
- Configured and validated STRICT mutual TLS (mTLS) using Istio PeerAuthentication.
- Tested plaintext communication from a workload outside the mesh and observed a connection reset with status `000`, consistent with STRICT mTLS enforcement.
- Configured Envoy Layer 7 access logging to capture HTTP status codes, request duration, response flags, and authorization-denial details.
- Implemented Istio AuthorizationPolicy resources to control access to the protected `httpbin` workload based on service-account identity and HTTP methods.
- Validated authorized GET requests returning HTTP `200` and unauthorized requests returning HTTP `403`, with Envoy RBAC denial evidence.
- Configured reader and writer permissions and verified that:
  - The reader identity could perform GET requests but was denied POST requests.
  - The writer identity could perform POST requests but was denied GET requests.
- Deployed OPA Gatekeeper v3.23.1 and implemented custom Rego ConstraintTemplates for required Istio sidecar presence and container security-context requirements.
- Performed positive and negative Pod admission tests, confirming rejection of noncompliant configurations and successful admission of a compliant test Pod.
- Refreshed the Gatekeeper audit and observed zero current violations for the selected constraints.
- Preserved policy manifests, test outputs, screenshots, and supporting documentation as Phase 7 evidence.
- Documented the architecture, implementation, validation outcomes, and repository deliverables.

### Tools & Technologies

`Istio 1.31.1` `Envoy` `OPA Gatekeeper v3.23.1` `Rego` `AuthorizationPolicy` `PeerAuthentication` `STRICT mTLS` `Kubernetes` `k3s` `Docker` `WSL2` `Linux` `kubectl` `HTTP` `Layer 7 Telemetry` `Zero Trust`

 [View Phase 7 — Zero Trust Service Mesh, Envoy L7 Telemetry & OPA Gatekeeper Guardrails](https://docs.google.com/document/d/1AhL3qxEfvHvhpMIZgrFBwA7el96n2csp/edit?usp=sharing&ouid=117891865848714392423&rtpof=true&sd=true)
 
---

## Phase 6 — SPIFFE/SPIRE Zero Trust Workload Identity & mTLS

Implemented and tested SPIFFE/SPIRE workload identity and application-layer mutual TLS (mTLS) in a local Kubernetes environment, establishing authenticated communication between Python client and server workloads using SPIFFE identities.

### Key Activities

- Deployed SPIRE components in the `spire-system` namespace within a local k3s cluster.
- Registered the client and server workloads with SPIRE using Kubernetes namespace and service-account selectors.
- Configured workload identities for the client and server:
  - `spiffe://devsecops.local/ns/devsecops-test/sa/mtls-client`
  - `spiffe://devsecops.local/ns/devsecops-test/sa/mtls-server`
- Verified SPIRE registration entries and confirmed an X.509 SVID time-to-live (TTL) of 3,600 seconds for both workload entries.
- Implemented and validated mutual TLS communication between the Python client and server workloads.
- Verified successful authenticated communication, with client and server SPIFFE identities recorded alongside HTTP 200 OK and `mTLS SUCCESS`.
- Captured a negative TLS test in which a connection without an accepted client certificate was rejected with `TLSV13_ALERT_CERTIFICATE_REQUIRED`.
- Recorded initial and renewed server SVID metadata, including certificate serial numbers, SHA-256 fingerprints, and validity windows.
- Preserved server-side rotation logs, certificate metadata snapshots, Kubernetes pod-state information, and subsequent successful mTLS client traffic as evidence of the observed SVID rotation sequence.
- Documented deployment manifests, test logs, screenshots, and supporting evidence under `evidence/phase6`.
- Recorded observed Kubernetes and cluster instability without attributing it to an unverified root cause.

### Tools & Technologies

`SPIFFE` `SPIRE` `mTLS` `X.509 SVIDs` `Python` `Kubernetes` `k3s` `Docker` `WSL2` `Linux` `kubectl` `TLS`

 [View Phase 6 — SPIFFE/SPIRE Zero Trust Workload Identity & mTLS](https://docs.google.com/document/d/1xdQDy-DYAO5L68sAmbLcx8HK14GuBMMg/edit?usp=sharing&ouid=117891865848714392423&rtpof=true&sd=true)

---

## Phase 5 — High-Performance Edge Verification & OCI Signature Mirroring

Implemented a local Zot OCI registry mirror and static public-key image verification to improve container supply-chain verification performance while preserving fail-closed admission controls.

### Key Activities

- Deployed and validated an in-cluster Zot OCI registry mirror.
- Configured a Kyverno ImageValidatingPolicy to use a static Cosign public key stored in the `cosign-static-public-key` Kubernetes ConfigMap.
- Verified the mirrored API image against its immutable SHA-256 digest.
- Validated the Zot registry API response with HTTP 200.
- Verified the mirrored API image and checked its associated SBOM and provenance-related artifacts.
- Confirmed that unsigned images, tampered-content images, and images with registry-manifest resolution failures were denied admission.
- Investigated remote keyless verification without caching, remote keyless verification with caching, and local static-key verification using Zot.
- Completed 20 successful digest-pinned server-side dry-run admission requests for Mode C.
- Measured a mean Kyverno policy-execution time of approximately 150.85 ms, with 19 of 20 evaluations at or below 500 ms and 17 of 20 at or below 250 ms.
- Recorded end-to-end request elapsed times of 2,560.3–14,445.6 ms across the 20 Mode C requests. These measure the broader request path, not Kyverno policy-execution latency alone.
- Preserved the existing production keyless policy and fail-closed enforcement.
- Documented remote registry and DNS-related diagnostic failures without claiming unverified root causes or benchmark results.

### Tools & Technologies

`GitHub Actions` `Kyverno` `Cosign` `Sigstore` `Zot OCI Registry` `SLSA` `CycloneDX` `GHCR` `Kubernetes` `k3s` `Docker` `WSL2` `Prometheus`

🔗 [View Phase 5 — High-Performance Edge Verification & OCI Signature Mirroring](https://docs.google.com/document/d/1zLSLH3M-U3kMf5e03eVFP8mHUgoP1Qj1/edit?usp=sharing&ouid=117891865848714392423&rtpof=true&sd=true)

## Phase 4 — Cloud Supply Chain Security & Admission Performance

Extended the enterprise DevSecOps pipeline with Kyverno image-verification caching, private container registry artifact inspection, and admission performance validation.

### Key Activities

- Enabled Kyverno image-verification caching with a maximum of 1,000 entries and a 60-minute TTL.
- Preserved fail-closed admission enforcement using `failurePolicy: Fail` and `validationActions: Deny`.
- Inspected the private Amazon ECR repository and its immutable API image digest.
- Identified three OCI referrers associated with the target API image.
- Inspected OCI image-manifest and Sigstore bundle media types in ECR artifact metadata.
- Reviewed the existing GitHub Actions container build, signing, SBOM, and provenance workflow.
- Benchmarked admission latency and investigated signed-image verification timeouts.
- Documented the limitations of the existing Mode A/B/C benchmark records.
- Identified cache-hit validation, complete signature-mirror verification, and sub-500 ms admission performance as outstanding validation requirements.

### Tools & Technologies

`GitHub Actions` `Kyverno` `Cosign` `Sigstore` `SLSA` `CycloneDX` `Amazon ECR` `OCI` `Kubernetes` `k3s` `Docker` `WSL2`

🔗 [View Phase 4 — Cloud Supply Chain Security & Admission Performance ](https://docs.google.com/document/d/17zmPmCCFMweXEnKvXdTvwYcokFRgbHql/edit?usp=sharing&ouid=117891865848714392423&rtpof=true&sd=true)

## Phase 3 — Container Supply Chain Security Enhancements

Extended the enterprise DevSecOps pipeline with additional container supply-chain security and validation controls.

### Key Activities

- Established the Kyverno image-verification cache baseline.
- Added SLSA build provenance generation for API and worker images.
- Generated provenance using immutable SHA-256 image digests.
- Verified provenance attestation records for both container images.
- Benchmarked Kyverno admission latency.
- Confirmed the Kubernetes admission webhook maximum timeout of 30 seconds.
- Verified the worker SBOM attestation directly with Cosign in 21.14 seconds.
- Preserved fail-closed admission enforcement.
- Documented caching and infrastructure optimization requirements for production.

### Tools & Technologies

`GitHub Actions` `Kyverno` `SLSA` `Cosign` `Sigstore` `CycloneDX` `GHCR` `Kubernetes` `k3s` `Docker` `WSL2`

🔗 [View Phase 3 — Container Supply Chain Security Enhancements ](https://docs.google.com/document/d/1OyogyKmFmXrlxAQGA8rmEqqxshvPaDjT/edit?usp=sharing&ouid=117891865848714392423&rtpof=true&sd=true)


## Phase 2 Preparation — Kubernetes Admission Control, SBOM Attestation & Secret Scanning

Extended the Phase 1 container supply-chain pipeline with cluster-level image trust enforcement, signed SBOM attestations, and layered secret scanning.

The project now applies security controls before commit, during build and signing, and at Kubernetes admission time.

### Key Activities

- Built a local single-node Kubernetes cluster using k3s on WSL2.
- Installed Kyverno for Kubernetes admission control.
- Created a Kyverno ImageValidatingPolicy requiring Cosign-signed project images, pinned to the GitHub Actions workflow identity.
- Re-validated the policy with strict schema validation (without `--validate=false`) and fixed a misplaced `matchImageReferences` field exposed by strict validation.
- Confirmed the policy enforces `Deny` with `failurePolicy: Fail`.
- Tested admission with an intentionally unsigned image and confirmed it was denied and no pod was created.
- Re-ran the CI negative test and confirmed unsigned images fail Cosign verification and skip deployment authorization.
- Attached CycloneDX SBOMs to API and worker image digests as signed in-toto attestations using Cosign.
- Added separate attestation verification steps and required both image-signature and SBOM-attestation verification before deployment authorization.
- Extended the Kyverno policy to require a signed CycloneDX SBOM attestation and confirmed that a signed image without a valid attestation is denied.
- Investigated the initial signed-and-attested admission timeout against the 30-second Kyverno webhook limit.
- Measured Cosign attestation verification latency, checked cluster/API and registry connectivity, and stabilized the local WSL2/k3s environment.
- Increased the Kyverno webhook timeout from 30 seconds to 60 seconds for the local validation environment.
- Admitted a signed and SBOM-attested worker image by immutable digest and confirmed the image was successfully pulled, the container was created, and the container started.
- Added a TruffleHog hook to the pre-commit workflow, verified it passes on clean content, and confirmed it blocks a staged test secret with exit code 183.
- Confirmed the full Git-history TruffleHog scan continues to run in GitHub Actions as the backstop.

### Validation Summary

| Control | Result |
|---|---|
| k3s local cluster | PASS |
| Kyverno installation | PASS |
| Signed-image admission policy | PASS |
| Strict Kyverno policy validation | PASS |
| Unsigned image denied in CI | PASS |
| Unsigned image denied at Kubernetes admission | PASS |
| Signed image without SBOM attestation denied | PASS |
| Signed + SBOM-attested image admitted | PASS |
| Signed + SBOM-attested container pulled and started | PASS |
| TruffleHog clean-content pre-commit test | PASS |
| TruffleHog staged-secret negative test | PASS |
| Full Git-history secret scan in CI | PASS |

### Known Limitations & Next Steps

- The validation was performed on a single-node local k3s/WSL2 environment and should not be treated as evidence of production-scale Kubernetes performance.
- The Kyverno webhook timeout was increased to 60 seconds to accommodate attestation verification latency observed in the local environment. A production deployment should establish an appropriate timeout through measured registry, transparency-log, and admission-controller performance.
- The local TruffleHog pre-commit hook currently uses the repository's Windows amd64 binary. A portable cross-platform hook implementation would be preferable for teams using different developer environments.

### Tools & Technologies

`k3s` `WSL2` `Kubernetes` `Kyverno` `Cosign` `Sigstore` `CycloneDX` `Syft` `GitHub Actions` `GHCR` `TruffleHog` `pre-commit`

🔗 [View Phase 2 Kubernetes Admission Control, SBOM Attestation & Secret Scanning ](https://docs.google.com/document/d/1Dll4HMlT7S1ud3dQH-0B2lTO5RuwNGLD/edit?usp=sharing&ouid=117891865848714392423&rtpof=true&sd=true)

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
