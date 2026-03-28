# 08 — Cloud Computing, Architecture & Deployment

---

> **Context**: Your Celebal internship touched cloud/data engineering. TCS Digital expects foundational cloud awareness. Don't overclaim — know the concepts clearly and relate them to your project.

---

## 8.1 Cloud Service Models — The Pizza Analogy

```
┌─────────────────────────────────────────────────────────────────┐
│                    PIZZA ANALOGY                                │
│                                                                 │
│  Traditional IT = Make pizza at home (you manage EVERYTHING)    │
│  IaaS          = Buy dough, sauce, cheese (you assemble & bake)│
│  PaaS          = Buy frozen pizza (you just bake it)           │
│  SaaS          = Order delivery (you just eat)                 │
└─────────────────────────────────────────────────────────────────┘
```

| Model | You Manage | Provider Manages | Example |
|-------|-----------|-----------------|---------|
| **IaaS** (Infrastructure) | OS, Runtime, App, Data | Servers, Storage, Network, Virtualization | AWS EC2, Azure VMs, Google Compute Engine |
| **PaaS** (Platform) | App, Data | Everything else (OS, Runtime, Servers) | Heroku, Google App Engine, Azure App Service |
| **SaaS** (Software) | Nothing — just use it | Everything | Gmail, Salesforce, Power BI Online, Google Docs |

**Q: "Where does your project fit in the cloud model?"**
> "Currently, my pipeline runs locally — on-premises essentially. If I were to deploy it, I'd use **IaaS** (like an EC2 instance) to run the Python ETL pipeline, with **PaaS** (like AWS RDS) for the managed MySQL database. The Power BI dashboard layer is already **SaaS** — it connects to the database and handles visualization without me managing any infrastructure."

### Cloud Deployment Models

| Model | Description | Use Case |
|-------|------------|----------|
| **Public Cloud** | Shared infrastructure (AWS, Azure, GCP) | Startups, scalable apps |
| **Private Cloud** | Dedicated to one organization | Banks, government (data sovereignty) |
| **Hybrid Cloud** | Mix of public + private | Enterprise with compliance needs |
| **Multi-Cloud** | Multiple public cloud providers | Avoid vendor lock-in |

---

## 8.2 Docker & Containerization

### What is Docker?

**Problem it solves**: "It works on my machine" — Docker ensures your application runs the same everywhere by packaging it with ALL its dependencies.

### VMs vs Containers

| Feature | Virtual Machine | Docker Container |
|---------|----------------|-----------------|
| **What it virtualizes** | Entire OS + hardware | Only the application layer |
| **Size** | GBs (full OS image) | MBs (only app + dependencies) |
| **Boot time** | Minutes | Seconds |
| **Resource usage** | Heavy (each VM has full OS) | Lightweight (shares host OS kernel) |
| **Isolation** | Strong (separate OS) | Process-level (shares kernel) |
| **Use case** | Running different OS types | Deploying microservices |

### Dockerfile Basics

```dockerfile
# A Dockerfile for your Crime Data Pipeline
FROM python:3.9-slim                    # Base image
WORKDIR /app                            # Set working directory
COPY requirements.txt .                 # Copy dependency file
RUN pip install -r requirements.txt     # Install dependencies
COPY . .                                # Copy project files
CMD ["python", "main.py"]              # Default command
```

**Q: "How would you containerize your crime data pipeline?"**
> "I'd write a Dockerfile based on `python:3.9-slim`, install Pandas, SQLAlchemy, and pymysql from requirements.txt, copy the project files, and set the entrypoint to `python main.py`. For the MySQL dependency, I'd use `docker-compose` to define both the Python app container and a MySQL container, with a shared network and volume for data persistence."

### Key Docker Commands
```bash
docker build -t crime-pipeline .    # Build image from Dockerfile
docker run crime-pipeline           # Run container
docker ps                           # List running containers
docker images                       # List local images
docker stop <container_id>          # Stop container
docker-compose up                   # Start multi-container app
```

---

## 8.3 Microservices vs Monolithic Architecture

| Aspect | Monolithic | Microservices |
|--------|-----------|---------------|
| **Structure** | One large codebase | Multiple small, independent services |
| **Deployment** | Deploy entire app for any change | Deploy individual services independently |
| **Scaling** | Scale everything together | Scale only the service that needs it |
| **Technology** | One tech stack | Each service can use different stack |
| **Failure impact** | One bug can crash everything | One service failure doesn't take down all |
| **Complexity** | Simple to develop, complex to scale | Complex to develop, simple to scale |
| **Communication** | Function calls (in-process) | REST APIs, message queues (inter-process) |
| **Best for** | Small teams, early-stage products | Large teams, enterprise applications |

**Q: "Is your project monolithic or microservices?"**
> "It's monolithic — and appropriately so for its scale. The ETL, transformation, and visualization modules are separate Python files but run as one process. For a production system at TCS scale, I'd decompose it into microservices: an **ingestion service** (handles data extraction), a **transformation service** (cleaning), a **storage service** (database operations), and a **visualization API** (serves dashboard data). They'd communicate via REST APIs or a message queue like RabbitMQ."

---

## 8.4 Scalability Stress Test

**Q: "If 10,000 users hit your Crime Data Power BI dashboard simultaneously, what breaks first?"**

> "The **MySQL database** breaks first — it becomes the bottleneck. A single MySQL instance can handle maybe 500-1000 concurrent connections efficiently. Here's my recovery plan:
>
> 1. **Read Replicas** — Create read-only copies of the database. Dashboard queries (all SELECTs) go to replicas, writes go to the primary. This distributes read load across multiple instances.
>
> 2. **Caching Layer** — Add Redis or Memcached between the dashboard and the database. Frequently requested aggregations (crime counts by district, monthly trends) are cached. Cache TTL of 15 minutes is reasonable since crime data doesn't change minute-by-minute.
>
> 3. **Connection Pooling** — Instead of each dashboard user opening a new database connection, use a connection pool (SQLAlchemy already supports this) that maintains a fixed number of reusable connections.
>
> 4. **CDN for Static Assets** — Power BI dashboards embedded in web apps can use a CDN for static resources.
>
> 5. **Materialized Views** — Pre-compute common aggregations as materialized views in MySQL, updated periodically. Dashboard queries hit these pre-computed tables instead of scanning 8M rows."

---

## 8.5 ETL vs ELT

| Feature | ETL | ELT |
|---------|-----|-----|
| **Process** | Extract → Transform → Load | Extract → Load → Transform |
| **Transform location** | In a staging area/pipeline (Python, SSIS) | Inside the data warehouse (SQL, Snowflake) |
| **Best for** | On-premises, smaller data, relational DBs | Cloud data warehouses, big data |
| **Speed** | Slower (transform before loading) | Faster (leverage warehouse's compute power) |
| **Your project** | ✅ You use ETL — transform in Python, then load to MySQL | Could use ELT if you loaded raw data to a cloud warehouse first |

**Q: "Why did you choose ETL over ELT?"**
> "My project runs locally with MySQL as the target. MySQL doesn't have the massive parallel processing capability of cloud warehouses like Snowflake or BigQuery. Transforming data in Python before loading ensures I only store clean, properly typed data in MySQL — reducing storage and improving query performance. In a cloud-native setup, I'd consider ELT to leverage the warehouse's distributed compute."

---

## 8.6 Quick-Reference Cloud Questions

| Question | Answer |
|----------|--------|
| What is elasticity? | Automatic scaling up/down based on demand (e.g., more EC2 instances during traffic spikes) |
| What is a load balancer? | Distributes incoming traffic across multiple servers to prevent overload |
| What is a CDN? | Content Delivery Network — caches content at edge locations closer to users |
| What is CI/CD? | Continuous Integration (auto-build/test on code push) + Continuous Deployment (auto-deploy to production) |
| What is serverless? | Cloud runs your code without you managing servers (AWS Lambda). Pay per execution, not per hour |

---

*Next: [09_OS_FUNDAMENTALS.md](./09_OS_FUNDAMENTALS.md) — Operating System Concepts*
