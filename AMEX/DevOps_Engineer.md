# AMEX

**YOE---> 3 yrs**

# 1. What's the difference between Docker and Kubernetes?

**Answer:**

> Docker is primarily a containerization platform. It allows us to build Docker images, run containers, manage container networking and volumes, and package an application with its dependencies.
>
> Kubernetes is a container orchestration platform. It manages containers across multiple nodes and provides features such as scheduling, scaling, service discovery, self-healing, rolling deployments, load balancing and declarative configuration.
>
> For example, if I have one application and I want to run it as a container, Docker may be sufficient. But if I have hundreds of containers running across multiple servers and I need automatic scaling, failover, rolling deployments and service discovery, Kubernetes is more appropriate.

### Simple comparison

```text
Docker
   │
   ├── Build image
   ├── Run container
   ├── Network
   └── Volume

Kubernetes
   │
   ├── Schedule containers
   ├── Scale applications
   ├── Service discovery
   ├── Self-healing
   ├── Rolling updates
   └── Load balancing
```

**Important:** Don't say "Kubernetes replaces Docker." Modern Kubernetes can use containerd or other CRI-compatible runtimes.

---

# 2. How do you reduce downtime during deployments?

**Answer:**

> I use a combination of rolling deployments, multiple replicas, readiness probes, proper resource configuration and traffic management.
>
> In Kubernetes, I normally deploy multiple replicas and use a rolling update strategy. Kubernetes creates new Pods and waits for the new Pods to become Ready before terminating the old Pods.
>
> I also configure readiness probes so that traffic is only sent to healthy Pods.
>
> For critical applications, I can use blue-green or canary deployments. With canary deployment, I gradually send traffic to the new version and monitor metrics such as error rate, latency and CPU before increasing traffic.

Example:

```yaml
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxUnavailable: 0
    maxSurge: 1
```

Then:

```text
Version 1
Pod-1 ──┐
Pod-2 ──┼── Service
Pod-3 ──┘

       ↓ Deployment

Version 1 + Version 2

Pod-1 ──┐
Pod-2 ──┼── Service
Pod-v2 ─┤
Pod-v2 ─┘

       ↓

Version 2
Pod-v2 ──┐
Pod-v2 ──┼── Service
Pod-v2 ──┘
```

---

# 3. What agents have you deployed?

This question can mean **monitoring/logging/CI agents**, so don't answer with only one technology.

Based on your experience, a good answer is:

> I have worked with agents used for monitoring, logging and CI/CD. For monitoring, I have worked with Prometheus-related exporters and monitoring agents. For centralized logging and analysis, I have worked with Splunk forwarders. I have also worked with Jenkins agents for distributed CI/CD execution.
>
> Depending on the environment, agents are deployed either directly on servers, through packages, or as containers/DaemonSets in Kubernetes.

You can mention:

```text
Jenkins Agent
Splunk Universal Forwarder
Prometheus Node Exporter
CloudWatch Agent
```

**Be careful:** Only name an agent if you can explain where it runs, what data it collects, and how it communicates.

---

# 4. Suppose there are 100 applications. How do you perform log analysis?

This is a **very good SRE interview question**.

Don't say:

> "I log into each server and check logs."

Instead:

> I would centralize the logs rather than analyzing 100 applications individually.
>
> Each application would send its logs to a centralized logging platform. For example, in an enterprise environment I have worked with Splunk. Logs can be collected using forwarders or agents and sent to the centralized Splunk platform.
>
> I would standardize the log format and include fields such as application name, environment, hostname, timestamp, severity, request ID and correlation ID.
>
> Then I can search across all 100 applications and correlate events.

Architecture:

```text
Application 1 ─┐
Application 2 ─┤
Application 3 ─┤
Application 4 ─┤
     ...        ├──► Log Agents/Forwarders
Application 100┘
                       │
                       ▼
                    Splunk
                       │
             ┌─────────┼─────────┐
             ▼         ▼         ▼
          Search     Alerts    Dashboard
```

For troubleshooting, I'd typically filter by:

```text
Application
Environment
Time range
Severity
Request ID
Correlation ID
Error code
Host/Pod
```

This is much more scalable than manually checking individual servers.

---

# 5. Difference between SRE and DevOps?

This is an important one.

> DevOps is primarily a culture and set of practices that brings development and operations together, with emphasis on automation, CI/CD, infrastructure automation and faster software delivery.
>
> SRE is an engineering discipline that applies software engineering practices to operations, with strong emphasis on reliability, availability, scalability, observability and measurable service objectives.
>
> DevOps focuses heavily on improving the software delivery lifecycle, while SRE puts stronger emphasis on maintaining reliability of services in production.
>
> In practice, there is significant overlap.

### Simple way to remember

```text
DevOps
   ↓
Build + Test + Deploy + Automate

SRE
   ↓
Reliability + Availability + Performance
+ Incident Response + Observability
```

You can then mention:

> In my experience, I have worked across both areas because my responsibilities included infrastructure automation, CI/CD, monitoring, incident management and production support.

---

# 6. Have you migrated from on-premises to cloud? What challenges did you face?

This question requires **care**.

Don't claim an on-prem-to-AWS migration if you haven't actually done one.

Given your experience, I'd answer honestly:

> I have worked extensively with AWS infrastructure and automation, but I would distinguish that from claiming that I personally led a complete on-premises-to-cloud migration.
>
> From an architecture and DevOps perspective, the key challenges I would evaluate in such a migration are application dependencies, network connectivity, security, data migration, downtime, DNS changes, IAM, monitoring and rollback.

Then explain the approach:

```text
On-Prem
   │
   ├── Applications
   ├── Databases
   ├── Dependencies
   └── Network
          │
          ▼
      Assessment
          │
          ▼
       AWS VPC
          │
     ┌────┴────┐
     ▼         ▼
   Apps      Database
```

### Challenges

**1. Dependency discovery**

> Before migration, I need to identify application-to-application and database dependencies.

**2. Network**

> VPN/Direct Connect, routing, firewall rules, DNS and IP addressing need to be planned.

**3. Data migration**

> Large databases require planning around replication, synchronization and cutover.

**4. Security**

> IAM, security groups, encryption, secrets and compliance need to be addressed.

**5. Downtime**

> We can minimize downtime using replication and phased cutover.

**6. Rollback**

> A migration should have a tested rollback strategy.

This answer is much safer than pretending you personally migrated an entire enterprise.

---

# 7. How does endpoint authentication work in Kubernetes?

This question can mean **Kubernetes API endpoint authentication**.

A strong answer:

> When a client such as kubectl communicates with the Kubernetes API Server, the request is sent to the Kubernetes API endpoint over HTTPS.
>
> The API Server authenticates the identity using mechanisms such as client certificates, bearer tokens, OIDC or service account tokens, depending on the configuration.
>
> After authentication, Kubernetes performs authorization. The authorization layer determines whether that identity has permission to perform the requested operation, commonly using RBAC.
>
> Finally, admission controllers can validate or modify the request before it is persisted.

The flow:

```text
kubectl
   │
   │ HTTPS
   ▼
Kubernetes API Server
   │
   ▼
Authentication
   │
   ▼
Authorization (RBAC)
   │
   ▼
Admission Controllers
   │
   ▼
API Server
   │
   ▼
etcd
```

### Important distinction

```text
Authentication
      ↓
"Who are you?"

Authorization
      ↓
"What are you allowed to do?"
```

Example:

```bash
kubectl get pods
```

Kubernetes first determines **who you are**, then checks whether you have permission to `get` Pods in that namespace.

---

# 8. How did you troubleshoot a Pod CrashLoopBackOff?

This is one where you should give a **methodical troubleshooting process**.

> First, I check the Pod status and restart count.

```bash
kubectl get pods
```

Then:

```bash
kubectl describe pod <pod-name>
```

I look at:

* Events
* Container state
* Exit code
* Last State
* Restart count
* Readiness/liveness probes
* Image
* Environment variables
* Volume mounts

Then I check logs:

```bash
kubectl logs <pod-name>
```

If the container has already restarted:

```bash
kubectl logs <pod-name> --previous
```

Then I investigate based on the evidence.

For example:

```text
CrashLoopBackOff
       │
       ├── Application error?
       │
       ├── Wrong command/args?
       │
       ├── Missing environment variable?
       │
       ├── Config/Secret issue?
       │
       ├── Permission issue?
       │
       ├── OOMKilled?
       │
       ├── Liveness probe failure?
       │
       └── Dependency unavailable?
```

In your recent Alpine example, this is exactly what happened.

You had:

```yaml
command:
  - sh
  - c
  - sleep 3600
```

instead of:

```yaml
command:
  - sh
  - -c
  - sleep 3600
```

The container exited with an error, Kubernetes restarted it, and eventually it entered `CrashLoopBackOff`.

That's a good **hands-on example** to use if an interviewer asks you for a simple troubleshooting scenario.

---

# 9. What are SLA and SLO?

### SLA

**Service Level Agreement**

An SLA is a contractual agreement between a service provider and customer.

Example:

> The service provider guarantees 99.9% availability.

There can be business consequences if the SLA isn't met.

### SLO

**Service Level Objective**

An SLO is an internal or operational target for reliability.

Example:

```text
SLO = 99.95% availability
```

You can also have:

```text
Latency SLO
Error-rate SLO
Availability SLO
```

### Add SLI

A strong interview answer includes all three:

```text
SLI = What we measure

SLO = What target we want

SLA = What we promise the customer
```

Example:

```text
SLI → Availability = 99.97%

SLO → Target = 99.95%

SLA → Contractual commitment = 99.9%
```

---

# 10. Provide the agent which you worked for customer/company

I think the interviewer may be asking:

> "Which agents have you deployed for the customer/company?"

I'd answer:

> In my previous environments, I have worked with Jenkins agents for distributed CI/CD execution and Splunk Universal Forwarders for centralized log collection. I have also worked with monitoring agents/exporters for infrastructure monitoring.
>
> For Jenkins, agents are used to execute builds away from the controller. For Splunk, the Universal Forwarder collects logs from hosts and forwards them to the Splunk infrastructure.

Then explain the architecture:

```text
Developer
    │
    ▼
 Jenkins Controller
    │
    ▼
 Jenkins Agent
    │
    ▼
 Build / Test / Deploy
```

and:

```text
Application Server
       │
       ▼
Splunk Universal Forwarder
       │
       ▼
 Splunk Indexers
       │
       ▼
Search / Dashboard
```

---

# 11. Tell me the cloud architecture you have worked on

This is probably one of the **most important questions for you**.

Use your AWS experience.

> I have worked primarily with AWS-based infrastructure. The architecture typically follows a multi-tier VPC design with public and private subnets across availability zones.
>
> Public subnets contain components such as load balancers and controlled access points, while application workloads are placed in private subnets.
>
> Infrastructure is provisioned using Terraform. CI/CD is handled through Jenkins, and monitoring and logging are handled using tools such as Prometheus, Grafana and Splunk.

Architecture:

```text
                         Internet
                            │
                            ▼
                    ┌───────────────┐
                    │     ALB       │
                    └───────┬───────┘
                            │
                ┌───────────┴───────────┐
                │                       │
                ▼                       ▼
          Private Subnet          Private Subnet
                │                       │
                ▼                       ▼
          Application 1           Application 2
                │                       │
                └───────────┬───────────┘
                            │
                            ▼
                         Database

        ┌────────────────────────────────────┐
        │              AWS VPC                │
        │                                    │
        │ Public Subnets → ALB               │
        │ Private Subnets → Applications     │
        │ Private DB → Database              │
        └────────────────────────────────────┘

Terraform → Infrastructure
Jenkins   → CI/CD
Prometheus/Grafana → Metrics
Splunk    → Logs
```

Then mention:

> I also used security groups, IAM, VPC routing, NAT gateways, EBS and other AWS services depending on the application requirements.

---

# 12. Explain a production issue you faced

This is where you should use a **real incident**, not a textbook example.

A good structure is:

```text
Situation
    ↓
Impact
    ↓
Investigation
    ↓
Root Cause
    ↓
Resolution
    ↓
Prevention
```

### Example answer

> We had a production issue where an application experienced availability/performance problems. My first step was to determine the scope of impact and whether it was application-specific or infrastructure-wide.
>
> I checked monitoring dashboards and alerts to identify when the problem started. Then I correlated the application metrics with infrastructure metrics and logs.
>
> I used centralized logs to identify the relevant errors and checked the affected hosts/services.
>
> Once I identified the root cause, we applied the immediate remediation to restore service. After service was restored, we performed a root-cause analysis and implemented preventive measures such as additional monitoring, alerting and automation.

But **don't stop there** in an interview. They will ask:

> "What exactly was the problem?"

So you need to prepare **2–3 real incidents from your experience** with concrete technical details.

---

# ⭐ The Most Important Part For Your Interviews

Based on the questions you've received, I see a pattern.

The interviewer isn't just testing:

> "Do you know Kubernetes?"

They're testing whether you can operate **production infrastructure**.

Your preparation should therefore be:

```text
                 DEVOPS / SRE
                      │
       ┌──────────────┼──────────────┐
       ▼              ▼              ▼
    CLOUD          KUBERNETES     AUTOMATION
       │              │              │
      AWS          EKS/K8s       Terraform
       │              │           Python
       │              │          Ansible
       ▼              ▼              ▼
  Networking       Helm          CI/CD
  IAM              ArgoCD        Jenkins
  VPC              RBAC          GitHub Actions
  ALB              Storage
  DNS              Networking
       │              │
       └──────────────┼──────────────┘
                      ▼
                 OBSERVABILITY
                      │
               ┌──────┼──────┐
               ▼      ▼      ▼
             Logs   Metrics  Traces
               │      │      │
             Splunk Prometheus OTel
                    Grafana
```

