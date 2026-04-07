# DevOps Interview Questions (66-120)

## CI/CD & GitOps (66-77)

**Q66.** What is CI/CD, and how does continuous integration differ from delivery/deployment?  

**Answer:** CI automates building/testing on code changes; delivery/deployment automates releasing to environments.  
**Explanation:** CI focuses on correctness early; delivery/deployment focuses on safe, repeatable releases.  

**Q67.** Why can trunk-based development improve CI outcomes compared to long-lived branches?

**Answer:** It reduces merge conflicts and shortens feedback loops.  
**Explanation:** Smaller, more frequent changes make failures easier to isolate and fix.  

**Q68.** How do you structure CI pipeline stages and artifacts?

**Answer:** Build and test first, then produce versioned artifacts, and finally deploy/promote those artifacts.  
**Explanation:** Separating build from deploy prevents “rebuild drift” and improves traceability.  

**Q69.** What is artifact immutability, and why does it matter?

**Answer:** Artifacts should not change once published (same version => same binary/container).  
**Explanation:** Immutability ensures you can reproduce exactly what ran in production and rollback safely.  

**Q70.** Compare blue/green and canary releases.

**Answer:** Blue/green shifts all traffic between two environments; canary gradually rolls out to a subset.  
**Explanation:** Both minimize risk; canary is often better for frequent releases when you want early signal.  

**Q71.** What does GitOps mean in practice?

**Answer:** The desired state is stored in Git and continuously reconciled to the cluster.  
**Explanation:** Tools like Argo CD/Flux monitor Git and apply changes declaratively.  

**Q72.** Webhook-triggered pipelines vs polling: what are trade-offs?

**Answer:** Webhooks are event-driven and typically faster; polling is simpler but adds latency and cost.  
**Explanation:** Many teams prefer webhooks for responsiveness, plus fallback polling for reliability.  

**Q73.** How should secrets be handled in CI pipelines?

**Answer:** Use managed secret stores (vault/SSM), encrypted CI variables, and strict access controls.  
**Explanation:** Never commit secrets; avoid printing secrets to logs and restrict who can read them.  

**Q74.** What role do manual approvals and quality gates play?

**Answer:** They block deployments until automated checks (and sometimes humans) pass.  
**Explanation:** Quality gates reduce risk when changes can impact critical systems or require compliance signoff.  

**Q75.** How does build caching reduce CI time? 

**Answer:** Cache dependencies/layers so repeated builds reuse unchanged work.  
**Explanation:** Docker layer caching and remote caches can significantly speed up builds.  

**Q76.** How do SBOM and SCA integrate into CI/CD?

**Answer:** Generate an SBOM for builds and run SCA to detect vulnerable dependencies.  
**Explanation:** Scanning earlier reduces time to remediate and supports compliance reporting.  

**Q77.** What ensures reproducible builds? 

**Answer:** Pin dependency versions, use lockfiles, and build in controlled environments (often containerized).  
**Explanation:** Reproducibility reduces “works on my machine” and improves auditability.  

## Terraform & IaC (78-89)

**Q78.** What is Terraform state, and why is it critical?

**Answer:** State is the local/remote record of resource instances Terraform manages.  
**Explanation:** Without correct state, Terraform cannot reliably compute diffs or track resource identity.  

**Q79.** Difference between `terraform plan` and `terraform apply`?

**Answer:** `plan` computes the execution steps; `apply` executes them.  
**Explanation:** `plan` should be reviewed in CI to catch unintended changes.  

**Q80.** Why use a remote state backend? 

**Answer:** To enable collaboration, persistence, and features like locking.  
**Explanation:** Remote state reduces the risk of inconsistent local state across teams and pipelines.  

**Q81.** What is state locking and why do you need it?

**Answer:** Locking prevents concurrent `apply` operations that could corrupt or diverge state.  
**Explanation:** It is usually implemented via a datastore lock (e.g., DynamoDB for AWS).  

**Q82.** `count` vs `for_each`: when should you prefer each?

**Answer:** Prefer `for_each` for stable mapping by keys; `count` is index-based and can cause churn on reordering.  
**Explanation:** Key-based addressing improves predictability and reduces resource recreation.  

**Q83.** What is drift, and how do you detect it?

**Answer:** Drift is when real infrastructure diverges from Terraform state; detection happens during `plan` (refresh) and comparisons.  
**Explanation:** Running `plan` against the latest reality reveals unexpected changes.  

**Q84.** How do you handle sensitive values/outputs in Terraform?

**Answer:** Mark variables/outputs as `sensitive` and ensure they are not logged.  
**Explanation:** Sensitive values still flow through Terraform, but should be protected from UI/log exposure.  

**Q85.** What are Terraform modules, and why use them?

**Answer:** Modules encapsulate reusable infrastructure patterns behind an interface of inputs/outputs.  
**Explanation:** Modules reduce duplication and enable consistent best practices across teams.  

**Q86.** When should you use `lifecycle` `ignore_changes`?

**Answer:** When specific attributes are managed externally or expected to change outside Terraform.  
**Explanation:** Use sparingly because it can hide real configuration drift.  

**Q87.** How do you reduce risk when Terraform replaces resources?

**Answer:** Use `create_before_destroy`, and set safeguards like `prevent_destroy` where appropriate.  
**Explanation:** Replacement can cause downtime; these options help preserve availability.  

**Q88.** Terraform workspaces vs separate environments/state: what is a common best practice?

**Answer:** Prefer separate state backends per environment for clarity and safety.  
**Explanation:** Workspaces can be useful, but separate backends reduce confusion and accidental cross-environment changes.  

**Q89.** How should Terraform authenticate to AWS securely?

**Answer:** Use IAM roles with OIDC (for CI) or short-lived credentials; avoid long-lived access keys.  
**Explanation:** Short-lived identity reduces credential leakage risk and improves auditing.  

## Containers & Kubernetes (90-99)

**Q90.** What is the difference between a container image and a running container?

**Answer:** An image is a static template; a container is a running instance created from the image.  
**Explanation:** Images are immutable artifacts; containers have runtime state like processes and mounted volumes.  

**Q91.** Name core Kubernetes control plane components and their roles.

**Answer:** API server (requests), scheduler (placement), controller-manager (reconciliation), etcd (cluster state).  
**Explanation:** Controllers continuously converge actual state to desired state.  

**Q92.** Deployment vs StatefulSet: what is the difference?

**Answer:** Deployments manage stateless replicas; StatefulSets manage stable identities and ordered rollouts for stateful workloads.  
**Explanation:** StatefulSets work well when stable hostnames/storage bindings matter.  

**Q93.** Pod vs Node, and how does scheduling relate to resources?

**Answer:** Pods run on nodes; the scheduler chooses nodes based on constraints and available capacity using requests/limits.  
**Explanation:** Resource requests influence placement and bin packing decisions.  

**Q94.** Explain Kubernetes Service types (ClusterIP, NodePort, LoadBalancer).

**Answer:** ClusterIP is internal; NodePort exposes on nodes; LoadBalancer creates an external load balancer (where supported).  
**Explanation:** Services provide stable networking and load balancing for sets of pods.  

**Q95.** Ingress resource vs Ingress controller.

**Answer:** Ingress defines routing rules; the controller implements those rules using a specific proxy/load balancer.  
**Explanation:** Ingress is declarative; the controller is the runtime component.  

**Q96.** What are resource requests and limits, and how do they affect QoS?

**Answer:** Requests reserve scheduling resources; limits cap runtime usage and determine QoS class (Guaranteed/Burstable).  
**Explanation:** Correct requests improve bin packing and reduce noisy-neighbor issues.  

**Q97.** Readiness vs liveness probes.

**Answer:** Readiness controls whether a pod receives traffic; liveness decides when to restart the container.  
**Explanation:** Readiness prevents routing to unready pods; liveness recovers from hung states.  

**Q98.** ConfigMap vs Secret in Kubernetes.

**Answer:** ConfigMap stores non-sensitive configuration; Secret stores sensitive data.  
**Explanation:** Secrets require extra care (RBAC, encryption at rest, audit logs) beyond just base64 encoding.  

**Q99.** How do you persist data in Kubernetes?

**Answer:** Use volumes with PersistentVolume (PV) and PersistentVolumeClaim (PVC) for durable storage.  
**Explanation:** Pod-level ephemeral storage is not guaranteed; PV/PVC maps to actual storage backends.  

## Monitoring, Logging, Metrics (100-107)

**Q100.** What are the three pillars of observability?

**Answer:** Logs, metrics, and traces.  
**Explanation:** Together they help answer what happened (logs), how it changed (metrics), and why/where across services (traces).  

**Q101.** Define SLI, SLO, and SLA.

**Answer:** SLI is the measured indicator; SLO is the target for that indicator; SLA is the contractual guarantee.  
**Explanation:** SLOs operationalize SLAs into measurable targets teams can manage with error budgets.  

**Q102.** What is the RED/USE approach?

**Answer:** RED focuses on rate, errors, duration; USE focuses on utilization, saturation, errors.  
**Explanation:** These frameworks help standardize metrics selection for alerts and capacity planning.  

**Q103.** How do you design alerts to reduce noise?

**Answer:** Base alerts on user-impact and SLOs, use multi-window/threshold logic, and include runbook links.  
**Explanation:** Noisy alerts reduce trust and increase time-to-mitigate during incidents.  

**Q104.** What is distributed tracing, and when is it valuable?

**Answer:** Tracing shows end-to-end request paths across services and their latencies.
**Explanation:** It is valuable when failures/performance issues span multiple microservices.

**Q105.** How do you correlate logs with traces?

**Answer:** Propagate trace/span IDs through headers and log them in structured logs.  
**Explanation:** This allows you to jump from a trace to relevant log lines quickly.  

**Q106.** Why use sampling in tracing?

**Answer:** To reduce overhead and cost while still capturing representative data.  
**Explanation:** You can sample consistently or dynamically based on traffic/error rates.  

**Q107.** Why does log retention matter?

**Answer:** It affects debugging usefulness, compliance, and storage costs.  
**Explanation:** Retention policies should balance legal requirements, incident needs, and cost controls.  

## Reliability & SRE (108-115)

**Q108.** What is an error budget, and how is it used?

**Answer:** It is the allowed amount of SLO violation over time.  
**Explanation:** If the error budget is low, you reduce risky changes to protect service reliability.  

**Q109.** What is chaos testing?

**Answer:** Controlled experiments that inject failures to validate resilience.  
**Explanation:** The goal is to ensure systems fail safely and recover predictably.  

**Q110.** Explain backpressure and appropriate timeouts.

**Answer:** Backpressure limits demand to avoid overload; timeouts bound waiting and prevent resource exhaustion.  
**Explanation:** Retry logic should include jitter and not retry infinitely (often with circuit breakers).  

**Q111.** How should you conduct a blameless incident postmortem?

**Answer:** Focus on system/process improvements, gather timeline facts, and produce clear action items.  
**Explanation:** Tools like RCA and structured “5 whys” help uncover contributing factors.  

**Q112.** High availability vs disaster recovery: what differs?

**Answer:** HA minimizes downtime for partial failures; DR enables restoring service after a broader disaster.  
**Explanation:** DR typically involves recovery plans, backups, replication, and tested runbooks.  

**Q113.** Define RTO and RPO.

**Answer:** RTO is the time to restore service; RPO is the maximum acceptable data loss window.  
**Explanation:** These drive backup frequency, replication strategy, and operational readiness.  

**Q114.** Key considerations for multi-region designs.

**Answer:** Data replication, failover behavior, latency, and consistency trade-offs.  
**Explanation:** Multi-region is not automatic; you must plan DNS/routing, data sync, and operational runbooks.  

**Q115.** How do canary or rolling releases improve reliability?

**Answer:** They limit blast radius by gradually shifting traffic or updating replicas.  
**Explanation:** You can abort/rollback when metrics indicate problems before full rollout.  

## Security & Supply Chain (116-120)

**Q116.** What is a software supply chain risk?

**Answer:** Risks introduced through dependencies, build systems, artifacts, or registries.  
**Explanation:** Compromised CI, malicious dependencies, or tampered images can lead to real production compromise.  

**Q117.** How can you verify container image provenance?

**Answer:** Use signed images and attestations (e.g., cosign) and enforce verification in deployment.  
**Explanation:** Provenance ties an artifact to the expected builder and source commit.  

**Q118.** How does least privilege apply to Kubernetes RBAC?

**Answer:** Create narrowly scoped Roles/ClusterRoles and bind them to ServiceAccounts with only necessary permissions.  
**Explanation:** Limit access by namespace, avoid cluster-wide bindings, and review permissions periodically.  

**Q119.** How do you prevent secrets from leaking into git or CI logs?

**Answer:** Use secret scanning, enforce pre-commit checks, and redact/avoid echoing sensitive values.  
**Explanation:** Prevention should happen before secrets enter the pipeline; redaction is a secondary control.  

**Q120.** What are the key steps of an incident response playbook?

**Answer:** Detect, triage, contain, eradicate, recover, and conduct a postmortem with corrective actions.  
**Explanation:** For security incidents, you often also rotate credentials/tokens and validate the full scope of impact.  

