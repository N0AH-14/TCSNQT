# 20 — DevOps, CI/CD & Networking Devices

---

> **TCS Digital 2026 expects awareness of DevOps practices and CI/CD pipelines, especially since enterprise data teams use them daily. Plus some computer networking topics that were thin in the original File 10.**

---

## 20.1 DevOps — What, Why, and How

### What is DevOps?

**DevOps** = **Dev**elopment + **Op**erations — a culture and set of practices that unifies software development and IT operations to deliver software faster, more reliably, and with continuous feedback.

```
TRADITIONAL                          DEVOPS
┌──────────┐   "throw over    ┌──────────────────────────────────┐
│ Dev Team │   the wall" →    │  Dev + Ops work together         │
│ (writes  │   ┌──────────┐   │  ┌────────────────────────────┐  │
│  code)   │   │ Ops Team │   │  │  Continuous Integration    │  │
└──────────┘   │ (deploys │   │  │  Continuous Delivery       │  │
               │  & runs) │   │  │  Continuous Monitoring     │  │
               └──────────┘   │  │  Shared Responsibility     │  │
                              │  └────────────────────────────┘  │
 Slow releases, blame game    │  Fast releases, shared ownership │
                              └──────────────────────────────────┘
```

### DevOps vs Agile

| Feature | Agile | DevOps |
|---------|-------|--------|
| **Focus** | Software development process | Development + Deployment + Operations |
| **Goal** | Deliver working software iteratively | Deliver software continuously and reliably |
| **Scope** | Planning → Development → Testing | Development → Testing → Deployment → Monitoring |
| **Feedback** | From product owner/client each sprint | From production systems in real-time |
| **Overlap** | Agile IS part of DevOps (development practices) | DevOps extends Agile into operations |

**Q: "What's the relationship between Agile and DevOps?"**
> "Agile focuses on HOW we build software (iterative sprints, daily stand-ups). DevOps extends this to HOW we deploy and operate it (CI/CD pipelines, infrastructure automation, monitoring). You can be Agile without DevOps, but modern enterprise teams like TCS use both together."

---

## 20.2 CI/CD Pipeline — The Core of DevOps

### CI — Continuous Integration

**What**: Developers merge code into a shared repository frequently (multiple times a day). Each merge triggers automatic build and test.

```
Developer pushes code to GitHub
        │
        ▼
┌──────────────────────────────┐
│  CI Server (GitHub Actions,  │
│  Jenkins, GitLab CI)         │
│                              │
│  1. Pull latest code         │
│  2. Install dependencies     │
│  3. Run unit tests           │
│  4. Run linting/code quality │
│  5. Build artefact           │
│                              │
│  ✅ All pass? → Merge        │
│  ❌ Any fail? → Block merge  │
└──────────────────────────────┘
```

### CD — Continuous Delivery / Deployment

| Term | Definition |
|------|-----------|
| **Continuous Delivery** | Code is always in a deployable state. Deployment to production requires manual approval |
| **Continuous Deployment** | Every change that passes all tests is automatically deployed to production — no manual step |

```
Full CI/CD Pipeline:

Code Push → Build → Unit Tests → Integration Tests → Staging Deploy → Acceptance Tests → Production Deploy
    │                                                                                          │
    └── Automated ──────────────────────────────────────────────────────────Automated ──────────┘
```

### CI/CD in Your Project Context

**Q: "How would you set up CI/CD for your crime data pipeline?"**
> "I'd use **GitHub Actions** (free for public repos):
> 1. **On every push**: Run `pytest` on transform functions — verify that `clean_data()` handles nulls, type conversions, and edge cases correctly
> 2. **On merge to main**: Run the full ETL on a small test dataset (1000 rows) to verify end-to-end pipeline integrity
> 3. **Scheduled run**: A weekly GitHub Action that fetches the latest data from the LA Open Data API and runs the full pipeline
> 4. **Alerting**: If any step fails, notify via email or Slack webhook"

### GitHub Actions — Basic Example

```yaml
# .github/workflows/ci.yml
name: ETL Pipeline CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Set up Python
      uses: actions/setup-python@v5
      with:
        python-version: '3.9'
    
    - name: Install dependencies
      run: pip install -r requirements.txt
    
    - name: Run tests
      run: pytest tests/
    
    - name: Run linting
      run: pip install flake8 && flake8 *.py
```

---

## 20.3 Jenkins — The Enterprise CI/CD Tool

TCS uses Jenkins extensively. Know the basics:

| Concept | What It Is |
|---------|-----------|
| **Jenkins** | Open-source automation server for CI/CD pipelines |
| **Pipeline** | A series of stages defined in a `Jenkinsfile` (code-as-config) |
| **Stage** | A logical grouping of steps (Build, Test, Deploy) |
| **Agent** | The machine where the pipeline runs |
| **Trigger** | What starts the pipeline (code push, schedule, manual) |
| **Plugin** | Extensions that add functionality (Git, Docker, Slack, etc.) |

```groovy
// Jenkinsfile (Declarative Pipeline)
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'pip install -r requirements.txt'
            }
        }
        stage('Test') {
            steps {
                sh 'pytest tests/'
            }
        }
        stage('Deploy') {
            steps {
                sh 'python main.py'
            }
        }
    }
    post {
        failure {
            mail to: 'work.noah14@gmail.com',
                 subject: 'Pipeline Failed',
                 body: 'Check Jenkins logs'
        }
    }
}
```

---

## 20.4 Infrastructure as Code (IaC) — Awareness Level

| Tool | What It Does |
|------|-------------|
| **Terraform** | Provision cloud infrastructure (create EC2, RDS, VPCs) using code |
| **Ansible** | Configure servers (install software, manage configs) using playbooks |
| **Kubernetes (K8s)** | Orchestrate Docker containers at scale (auto-scaling, load balancing, self-healing) |
| **Docker Compose** | Define multi-container applications (already in your File 08) |

**Q: "What is Kubernetes?"**
> "Kubernetes orchestrates Docker containers at scale. If Docker is a single shipping container, Kubernetes is the port authority that decides where containers go, replaces broken ones, and scales up when traffic increases. It handles deployment, scaling, and management of containerized applications. TCS uses it extensively for microservices architecture."

---

## 20.5 Monitoring & Logging — The "Ops" in DevOps

| Tool | Purpose |
|------|---------|
| **Prometheus + Grafana** | Metrics collection and visualization (CPU, memory, response times) |
| **ELK Stack** (Elasticsearch, Logstash, Kibana) | Centralized log aggregation, search, and visualization |
| **Splunk** | Enterprise log analysis (TCS uses this) |
| **New Relic / Datadog** | Application Performance Monitoring (APM) |

**Your Project Context:**
> "My pipeline already has logging (`logs/project.log`). In production, I'd send these logs to an ELK stack for centralized monitoring. I'd also add metrics — track chunk processing time, row counts, and error rates — and visualize them in Grafana to detect pipeline degradation early."

---

## 20.6 Computer Networking Extras (Gaps from File 10)

### Networking Devices — Know the Differences

| Device | OSI Layer | Function |
|--------|-----------|----------|
| **Hub** | Layer 1 (Physical) | Broadcasts data to ALL connected devices. Dumb — no intelligence |
| **Switch** | Layer 2 (Data Link) | Forwards data only to the intended device using MAC addresses. Smart hub |
| **Router** | Layer 3 (Network) | Routes data between different networks using IP addresses. Connects your LAN to the internet |
| **Gateway** | Layer 3+ | Connects networks with different protocols (e.g., your LAN to a cloud VPC) |
| **Modem** | Layer 1-2 | Converts digital signals to analog (for ISP connection) and vice versa |
| **Load Balancer** | Layer 4-7 | Distributes incoming traffic across multiple servers |

```
Your Home Network:
Modem → Router → Switch → Your PC
                       → Power BI dashboards
                       → Other devices

TCS Enterprise:
Internet → Firewall → Load Balancer → Server Cluster
                                    → Application Servers
                                    → Database Servers
```

### What is a Proxy Server?

```
Client → Proxy Server → Target Server
         │
         ├── Caching: stores frequently requested content
         ├── Filtering: blocks restricted websites
         ├── Anonymity: hides client's real IP
         └── Logging: records all traffic for audit
```

| Type | Direction | Use Case |
|------|-----------|----------|
| **Forward Proxy** | Client → Proxy → Server | Employee internet access at TCS (content filtering) |
| **Reverse Proxy** | Client → Proxy → Server cluster | Nginx in front of Flask app (load balancing, SSL termination) |

### Cookies vs Sessions vs Tokens

| Feature | Cookies | Sessions | Tokens (JWT) |
|---------|---------|----------|-------------|
| **Stored where** | Client browser | Server memory/database | Client (localStorage/cookie) |
| **State** | Stateless (data in cookie) | Stateful (server tracks) | Stateless (self-contained) |
| **Size limit** | ~4KB | No limit (server-side) | No strict limit |
| **Security** | Can be stolen (XSS) | More secure (server-side) | Signed, self-verifying |
| **Scalability** | Scales well | Hard to scale (sticky sessions) | Scales well (stateless) |
| **Use case** | Preferences, tracking | Traditional web apps | Modern SPAs, APIs |

### What is a Socket?

**Socket** = IP Address + Port Number — the endpoint for two-way network communication.

```python
# Your MySQL connection uses sockets:
# Socket = localhost:3306
# localhost = 127.0.0.1 (IP address)
# 3306 = MySQL's default port

# Common ports to know:
# 80   = HTTP
# 443  = HTTPS
# 3306 = MySQL
# 5432 = PostgreSQL
# 22   = SSH
# 21   = FTP
# 25   = SMTP (email)
# 53   = DNS
```

### What is a CDN (Content Delivery Network)?

```
Without CDN:
User in Jaipur ──────long distance──────→ Server in USA
                  (high latency: ~200ms)

With CDN:
User in Jaipur ── short hop ──→ CDN Edge Server in Mumbai ── cached content
                  (low latency: ~20ms)
                  
If content not cached → CDN fetches from origin server, caches for next user
```

| CDN Provider | Used By |
|-------------|---------|
| CloudFlare | Most websites |
| AWS CloudFront | Amazon ecosystem |
| Akamai | Enterprise (banks, media) |
| Azure CDN | Microsoft ecosystem |

---

## 20.7 Quick-Fire DevOps & Networking Questions

| Question | Answer |
|----------|--------|
| What is CI/CD? | Continuous Integration (auto build/test on code push) + Continuous Delivery (auto deploy to staging) / Deployment (auto deploy to production) |
| Jenkins vs GitHub Actions? | Jenkins: self-hosted, highly customizable, enterprise standard. GitHub Actions: cloud-hosted, simpler, free for public repos |
| What is a webhook? | An HTTP callback — a server sends an automatic POST request to your URL when an event occurs (e.g., GitHub sends a webhook when code is pushed) |
| What is blue-green deployment? | Run two identical environments (blue=current, green=new). Switch traffic to green after testing. If green fails, switch back to blue instantly |
| What is canary deployment? | Roll out new version to a small subset of users first (5%). If stable, gradually increase. If issues, rollback only the canary |
| Hub vs Switch? | Hub broadcasts to all; Switch intelligently sends only to the destination device using MAC address table |
| What is ARP? | Address Resolution Protocol — converts IP address to MAC address on a local network |
| What is port forwarding? | Redirecting traffic from one port to another, or from a public IP to a private IP behind a router |

---

*This file covers DevOps, CI/CD, and additional networking concepts missing from the original 16-file set.*
