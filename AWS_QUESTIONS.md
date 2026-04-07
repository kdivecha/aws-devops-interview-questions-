# AWS Interview Questions (1-65)

## IAM & STS (1-10)

**Q1.** What is the difference between authentication and authorization in AWS?
**Answer:** Authentication verifies identity; authorization determines what actions that identity can perform.
**Explanation:** AWS IAM policies control authorization after the caller is identified (e.g., via credentials).

**Q2.** When would you use an IAM user vs an IAM role?
**Answer:** Prefer roles over users for most applications and cross-account access.
**Explanation:** IAM users have long-term credentials; roles provide temporary credentials via STS and support least-privilege scoping.

**Q3.** What does AWS STS `AssumeRole` do?
**Answer:** It lets a principal obtain temporary security credentials for a role.
**Explanation:** Temporary credentials reduce blast radius and enable cross-account or privilege elevation under controlled trust policies.

**Q4.** What is a role trust policy?
**Answer:** The trust policy defines which principals can assume the role.
**Explanation:** The trust policy is evaluated during `AssumeRole`, while permission policies define what the role can do.

**Q5.** What are permissions boundaries and when do you use them?
**Answer:** They cap the maximum permissions a role or user can grant.
**Explanation:** They provide an organizational guardrail even if someone attaches broad policies.

**Q6.** How can you enforce MFA for access to AWS resources?
**Answer:** Use policy conditions like `aws:MultiFactorAuthPresent` and/or AWS MFA enforcement settings.
**Explanation:** Conditional enforcement ties access decisions to whether MFA was used to authenticate.

**Q7.** Explain identity-based vs resource-based policies.
**Answer:** Identity-based policies attach to IAM identities; resource-based policies attach to the resource (e.g., S3 bucket, SQS queue).
**Explanation:** Resource policies enable granting cross-account access directly on the resource.

**Q8.** What is an AWS Organizations Service Control Policy (SCP)?
**Answer:** An SCP restricts permissions for accounts in an organization.
**Explanation:** SCPs are permission boundaries at the org level (they can effectively deny even if IAM allows).

**Q9.** What is role chaining and why is it risky?
**Answer:** Role chaining happens when a role uses temporary credentials to assume another role.
**Explanation:** It can unintentionally expand privileges and complicate auditing; AWS recommends minimizing chains and tightening trust.

**Q10.** How do you apply least privilege effectively?
**Answer:** Start with narrow permissions, validate with policy simulations, and iteratively adjust.
**Explanation:** Tools like IAM Access Analyzer and CloudTrail insights help identify unused or overbroad permissions.

## VPC & Networking (11-25)

**Q11.** What does a VPC CIDR block represent, and what constraint applies to it?
**Answer:** It defines the private IP address range for the VPC.
**Explanation:** VPC CIDR ranges must not overlap with other connected VPCs over peering/transit.

**Q12.** How do public and private subnets differ?
**Answer:** Public subnets have a route to an Internet Gateway; private subnets do not (unless special routes exist).
**Explanation:** Public subnets typically route `0.0.0.0/0` to an IGW; private subnets route outbound to NAT.

**Q13.** Compare security groups and network ACLs (NACLs).
**Answer:** Security groups are stateful and act like instance-level firewalls; NACLs are stateless and subnet-level.
**Explanation:** With SGs, return traffic is automatically allowed; with NACLs, both inbound and outbound rules must allow traffic.

**Q14.** NAT Gateway vs NAT instance: what is the typical guidance?
**Answer:** Prefer NAT Gateways for managed reliability; NAT instances are more customizable but operationally heavier.
**Explanation:** NAT Gateways are highly available and reduce the need to manage patching and scaling.

**Q15.** How do security group rules handle ephemeral ports?
**Answer:** For inbound rules that allow a source and port, return traffic is permitted automatically.
**Explanation:** SGs are stateful, so you typically do not need explicit outbound/return rules for established connections.

**Q16.** What is a VPC endpoint, and why would you use one?
**Answer:** A VPC endpoint privately connects your VPC to AWS services without traversing the public internet.
**Explanation:** It reduces exposure and can improve control/compliance via private routing and policies.

**Q17.** Distinguish gateway endpoints and interface endpoints.
**Answer:** Gateway endpoints integrate with route tables (commonly for S3/DynamoDB); interface endpoints use ENIs (for many other services).
**Explanation:** Gateway endpoints do not require NAT/IGW for the target service.

**Q18.** When would you choose VPC peering?
**Answer:** You need direct connectivity between two VPCs with non-overlapping CIDRs and simple routing.
**Explanation:** Peering is point-to-point and does not provide transitive routing.

**Q19.** What is the key advantage of AWS Transit Gateway (TGW)?
**Answer:** Centralized hub-and-spoke connectivity across many VPCs and accounts.
**Explanation:** TGW supports attachments and scalable interconnect patterns without full mesh peering.

**Q20.** What is AWS PrivateLink (interface endpoints) used for?
**Answer:** Private, endpoint-based access to supported AWS services and many partner services.
**Explanation:** It creates a private network path to a service without public exposure.

**Q21.** What is an Elastic IP (EIP) and when is it needed?
**Answer:** A static public IPv4 address for your account.
**Explanation:** Common uses include stable public IPs for NAT devices or EC2 instances across restarts.

**Q22.** Why do instances generally keep private IPs in a VPC?
**Answer:** Private IPs enable controlled internal routing and reduce direct internet exposure.
**Explanation:** Outbound internet access is typically handled via NAT, and inbound via load balancers or gateways.

**Q23.** What does enabling DNS hostnames/resolution change in a VPC?
**Answer:** It affects whether instances get DNS names and whether DNS resolution works inside the VPC.
**Explanation:** Many AWS service integrations rely on DNS resolution for endpoints and service discovery.

**Q24.** What are DHCP options sets?
**Answer:** They customize DHCP-provided network settings like DNS server and domain name.
**Explanation:** They affect how instances in the VPC resolve DNS queries.

**Q25.** How would you troubleshoot “instance cannot reach service” connectivity?
**Answer:** Check route tables, SGs, NACLs, instance firewall, DNS resolution, then VPC flow logs.
**Explanation:** Most connectivity failures map to incorrect routing or security controls rather than application code.

## EC2, Load Balancing, Auto Scaling (26-35)

**Q26.** Difference between EBS and instance store?
**Answer:** EBS is persistent block storage; instance store is ephemeral and tied to the lifecycle of the host.
**Explanation:** Instance store data is lost when the instance stops/terminates or hardware is replaced.

**Q27.** What is an Auto Scaling Group (ASG) used for?
**Answer:** It maintains capacity by launching/terminating instances based on scaling policies and health checks.
**Explanation:** ASGs automate response to load and improve availability.

**Q28.** What are ASG lifecycle hooks?
**Answer:** They pause instance launch/termination to run custom actions.
**Explanation:** Hooks are useful for draining, registration, or pre-provisioning steps.

**Q29.** Application Load Balancer (ALB) vs Network Load Balancer (NLB)?
**Answer:** ALB operates at Layer 7 (HTTP/HTTPS), NLB at Layer 4 (TCP/UDP).
**Explanation:** ALB supports advanced HTTP routing and WebSockets; NLB focuses on high-throughput L4 forwarding.

**Q30.** What is sticky sessions, and when can it matter?
**Answer:** It keeps a user on the same target to preserve session affinity.
**Explanation:** It can help when state is stored in the application instance; better is usually to store session state externally.

**Q31.** What is connection draining?
**Answer:** It lets in-flight connections continue for a configured period during deregistration/scale-in.
**Explanation:** This reduces user impact when instances are removed from a target group.

**Q32.** How do ASG and load balancer health checks differ?
**Answer:** ASG health checks determine instance replacement; load balancer target health determines traffic routing.
**Explanation:** They can use different signals, so you often tune both with appropriate grace periods.

**Q33.** What is an instance refresh in an ASG?
**Answer:** A controlled rolling replacement of instances based on a new launch template/config.
**Explanation:** It updates instances safely without waiting for natural scaling events.

**Q34.** How do you design EC2-based systems for high availability?
**Answer:** Use multiple AZs, a load balancer, health checks, and automated scaling/replacement.
**Explanation:** Availability improves when a single AZ or instance failure does not cause service downtime.

**Q35.** How does AWS Systems Manager Session Manager replace SSH?
**Answer:** It provides shell access via the SSM agent and IAM permissions, without inbound SSH.
**Explanation:** It reduces exposed ports and enables tighter access control and auditing.

## Storage (S3/EBS/EFS/Glacier) (36-45)

**Q36.** What are common S3 storage classes and how do they differ?
**Answer:** Classes trade off cost vs retrieval frequency/latency.
**Explanation:** Standard is general-purpose; Infrequent Access saves cost for rare access; Glacier optimizes for archival durability and lower retrieval frequency.

**Q37.** How would you describe S3 object consistency for reads after writes?
**Answer:** S3 provides strong read-after-write consistency for new objects and overwrite operations.
**Explanation:** This simplifies application logic compared to eventual consistency models in older systems.

**Q38.** What are S3 lifecycle policies?
**Answer:** They automate transitions between storage classes and/or expiration of objects.
**Explanation:** Lifecycle policies reduce manual management and help control storage costs.

**Q39.** Why use S3 versioning?
**Answer:** It preserves prior versions to help recover from overwrites and deletes.
**Explanation:** Versioning can be critical for resilience against operator error and application bugs.

**Q40.** Pre-signed URLs vs making objects public: when to prefer which?
**Answer:** Use pre-signed URLs for time-limited access to private objects.
**Explanation:** Pre-signed URLs reduce long-lived exposure and support auditing/expiration.

**Q41.** Compare EBS `gp3` vs `io2`.
**Answer:** `gp3` targets general-purpose workloads; `io2` is for provisioned high IOPS with strong durability requirements.
**Explanation:** Choose based on performance and durability/SLA needs, not just throughput.

**Q42.** How do EBS snapshots relate to AMIs?
**Answer:** Snapshots capture point-in-time EBS volumes; AMIs are images that reference snapshots needed for boot.
**Explanation:** AMIs typically bundle the boot volume and launch configuration.

**Q43.** EFS vs EBS: when is each appropriate?
**Answer:** EFS provides shared file storage (NFS) across instances; EBS provides block storage for a single instance or attached volumes.
**Explanation:** Use EFS when multiple instances need shared filesystem semantics.

**Q44.** Why and how do you use S3 multipart upload?
**Answer:** For large objects, it improves upload reliability and enables parallel parts.
**Explanation:** Multipart uploads also allow resuming failed uploads and can speed up transfers.

**Q45.** What are S3 event notifications commonly used for?
**Answer:** Triggering downstream workflows like Lambda processing, queue messages, or stream ingestion.
**Explanation:** This enables event-driven architectures without polling.

## Databases & Data (46-55)

**Q46.** RDS vs Aurora: what is the practical difference?
**Answer:** Aurora is MySQL/PostgreSQL-compatible and offers features like autoscaling and storage-layer optimizations.
**Explanation:** RDS supports a broader set of engines, while Aurora focuses on specific engine compatibility plus performance/reliability advantages.

**Q47.** What does Multi-AZ for RDS provide?
**Answer:** Failover to a standby instance in another AZ.
**Explanation:** Typically improves availability by keeping a synchronized or readily available replica.

**Q48.** What are read replicas used for?
**Answer:** Scaling read-heavy workloads off the primary database.
**Explanation:** Replicas offload SELECT traffic and can improve throughput without major schema changes.

**Q49.** Explain point-in-time recovery (PITR) for RDS.
**Answer:** It lets you restore the database to a specific time within the retention window.
**Explanation:** Backups plus logs enable rollback to a desired timestamp.

**Q50.** How would you decide between DynamoDB and a relational database?
**Answer:** Choose DynamoDB for key-value/NoSQL access patterns with predictable partitions; choose relational for complex joins and transactional modeling.
**Explanation:** Schema flexibility differs, and query capabilities (including joins) are limited in DynamoDB.

**Q51.** Provisioned vs on-demand capacity in DynamoDB?
**Answer:** Provisioned lets you set capacity targets; on-demand scales automatically with traffic.
**Explanation:** On-demand is often simpler for spiky or uncertain traffic; provisioned can be cheaper for steady workloads.

**Q52.** What is a partition key and why can it cause hotspots?
**Answer:** Partition keys determine item distribution across partitions; skewed keys concentrate traffic on one partition.
**Explanation:** Hot partitions limit throughput even when overall table capacity seems sufficient.

**Q53.** What is DynamoDB TTL (time to live)?
**Answer:** It schedules automatic deletion of items after a specified timestamp.
**Explanation:** TTL supports data lifecycle management without application-driven cleanup loops.

**Q54.** What are ElastiCache use cases and engines?
**Answer:** Caching to reduce latency and load; common engines include Redis and Memcached.
**Explanation:** Redis also supports richer data structures and patterns like caching, queues, and session stores.

**Q55.** How do you secure databases on AWS?
**Answer:** Use encryption (at rest and in transit), restrict network access (SGs), and apply IAM authentication where supported.
**Explanation:** Security is defense-in-depth: identity, network controls, and encryption/compliance features.

## Serverless (56-59)

**Q56.** When would you choose Lambda over containerized services?
**Answer:** When workloads are event-driven and can run in short-lived stateless functions.
**Explanation:** Lambda scales automatically per event and typically reduces ops overhead.

**Q57.** What are Lambda concurrency controls?
**Answer:** Concurrency limits control how many instances can run simultaneously.
**Explanation:** Reserved concurrency can protect downstream systems; overall concurrency can prevent runaway scaling.

**Q58.** API Gateway vs ALB for routing to Lambda: what differs?
**Answer:** API Gateway is purpose-built for API traffic and can integrate tightly with Lambda; ALB routes HTTP at L7 but typically uses target groups.
**Explanation:** The choice depends on API features (auth, throttling, WebSockets) and architecture constraints.

**Q59.** Why does Lambda in a VPC affect cold start?
**Answer:** It must attach elastic network interfaces (ENIs) to access VPC resources, which can add latency.
**Explanation:** Properly sizing subnets/ENI reuse and using VPC endpoints can mitigate overhead.

## AWS Security & Compliance (60-65)

**Q60.** Distinguish encryption in transit vs encryption at rest.
**Answer:** In transit protects data while traveling over networks; at rest protects stored data on disk.
**Explanation:** AWS commonly provides TLS for transit and KMS-managed keys for at rest.

**Q61.** How can you restrict access using an interface endpoint condition?
**Answer:** Use condition keys like `aws:SourceVpce` to allow requests only via a specified VPC endpoint.
**Explanation:** This ties access to private network paths rather than public IPs.

**Q62.** What is AWS KMS, and how do key policies relate to IAM?
**Answer:** KMS manages cryptographic keys and usage; key policies define who can use/administrate keys.
**Explanation:** Both key policies and IAM policies can be evaluated, depending on configuration and grant usage.

**Q63.** Why might you treat security groups and NACLs differently in audits?
**Answer:** They have different scope and behavior (stateful vs stateless), impacting how you document enforcement.
**Explanation:** Compliance evidence may require showing both inbound/outbound behavior and subnet-level controls.

**Q64.** What do CloudTrail and AWS Config provide?
**Answer:** CloudTrail records API activity; AWS Config tracks resource configuration changes over time.
**Explanation:** Together they support auditability: who did what vs how resources changed.

**Q65.** Detection vs prevention in AWS security: what’s the difference?
**Answer:** Prevention blocks or restricts actions; detection identifies suspicious or noncompliant behavior.
**Explanation:** Services like WAF/SCPs prevent; GuardDuty/Config rules detect and help trigger responses.

