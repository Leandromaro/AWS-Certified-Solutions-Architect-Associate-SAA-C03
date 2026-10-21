# Module 03: Compute Services

## 📚 Overview

AWS compute services for SAA-C03 exam: EC2, Lambda, containers, Auto Scaling, and Load Balancers.

**Exam Weight**: ~20-25%

---

## 🎯 Key Services

1. **Amazon EC2** - Virtual servers
2. **AWS Lambda** - Serverless functions
3. **Auto Scaling** - Automatic capacity management
4. **Elastic Load Balancing** - Distribute traffic
5. **ECS/EKS/Fargate** - Container services 
7. **Elastic Beanstalk** - PaaS deployment

 - Amazon ECS and EKS are container orchestrators, while AWS Fargate is a serverless compute engine that runs containers for either orchestrator. ECS is Amazon's proprietary, low-overhead orchestrator ($0 control plane fee), whereas EKS is managed Kubernetes ($0.10/hr control plane fee) for cross-platform portability and rich ecosystem tools.

 - Core Concept: Orchestration vs. Compute

    • Amazon ECS (Elastic Container Service): AWS-native, proprietary orchestrator. It uses custom concepts like tasks, task definitions, and services, offering deep integration with AWS features and a low learning curve.
    • Amazon EKS (Elastic Kubernetes Service): Managed upstream Kubernetes. It uses standard Kubernetes primitives (pods, deployments, Helm charts) and provides high portability across different clouds or on-premises environments.
    • AWS Fargate: A serverless compute layer. It is not a standalone orchestrator; instead, it replaces self-managed EC2 virtual machines so you run tasks (ECS) or pods (EKS) without provisioning or patching servers

---

## 1. Amazon EC2

### Instance Types (CRAM FACTS)
- **C** - Compute Optimized
- **R** - Memory Optimized (RAM)
- **M** - General Purpose (Medium)
- **T** - Burstable Performance
- **I/D/H** - Storage Optimized

### Pricing Models
| Model | Discount | Use Case |
|-------|----------|----------|
| On-Demand | None | Short-term, unpredictable |
| Reserved (1-3 yr) | Up to 72% | Steady state |
| Savings Plans | Up to 66% | Flexible commitment |
| Spot | Up to 90% | Fault-tolerant workloads |
| Dedicated | None | Compliance, licensing |

### Placement Groups
- **Cluster**: Low latency, same AZ
- **Spread**: Max 7/AZ, critical apps
- **Partition**: Distributed apps (Hadoop, Kafka)

---

## 2. Auto Scaling

### Scaling Policies
1. **Target Tracking**: Maintain metric (e.g., CPU 50%)
2. **Step Scaling**: Add/remove based on alarms
3. **Scheduled**: Time-based scaling
4. **Predictive**: ML-based forecasting

### Key Concepts
- Min/Desired/Max capacity
- Health checks
- Lifecycle hooks
- Cooldown periods (default 300s)

---

## 3. Elastic Load Balancing

| Type | Layer | Use Case | Features |
|------|-------|----------|----------|
| ALB | 7 (HTTP/HTTPS) | Microservices, containers | Path/host routing, Lambda targets |
| NLB | 4 (TCP/UDP) | Extreme performance | Static IP, millions req/sec |
| GWLB | 3 (Network) | Virtual appliances | Firewalls, IDS |
| CLB | 4 & 7 | Legacy | Not recommended |

### Features
- Health checks
- Sticky sessions
- Cross-zone load balancing
- SSL/TLS termination

**Elastic Load Balancing (ELB)** distributes incoming traffic across multiple targets such as EC2 instances, containers, and IP addresses.

AWS provides four types of load balancers:

| Load Balancer | OSI Layer | Protocols | Main Use |
|---|---|---|---|
| **ALB** | Layer 7 | HTTP, HTTPS, gRPC | Web applications, APIs, microservices |
| **NLB** | Layer 4 | TCP, UDP, TLS, QUIC | High-performance and low-latency applications |
| **GWLB** | Layer 3/7 | IP, GENEVE | Firewalls and security appliances |
| **CLB** | Layer 4/7 | TCP, SSL, HTTP, HTTPS | Legacy applications |

## Network Load Balancer (NLB)

- Operates at **Layer 4**.
- Supports **TCP, UDP, TLS, and QUIC**.
- Designed for **high performance and very low latency**.
- Supports **static IP addresses and Elastic IPs**.
- Suitable for applications that require large amounts of network traffic.

> **NLB = high-performance network-level load balancing**

## Gateway Load Balancer (GWLB)

- Used to deploy and scale **third-party virtual network appliances**.
- Commonly used with:
  - Firewalls
  - IDS/IPS
  - Security appliances
- Uses **GENEVE** for traffic encapsulation.
- Allows traffic to be transparently inspected by network appliances.

> **GWLB = traffic inspection through network appliances**

## Classic Load Balancer (CLB)

- The **older generation** of AWS load balancers.
- Supports both Layer 4 and basic Layer 7 functionality.
- Mainly used for **legacy applications**.
- For new architectures, prefer **ALB, NLB, or GWLB**.

> **CLB = legacy load balancing**

## Quick Decision Guide

```text
HTTP/HTTPS + intelligent routing → ALB
TCP/UDP + high performance       → NLB
Firewall / IDS / IPS              → GWLB
Legacy application                → CLB
---
```

## 4. AWS Lambda

### Limits
- Max execution: **15 minutes**
- Memory: 128 MB - 10 GB
- /tmp storage: 512 MB - 10 GB
- Concurrent executions: 1,000 (default)

### Invocation Types
1. **Synchronous**: API Gateway, CLI
2. **Asynchronous**: S3, SNS, EventBridge
3. **Event Source Mapping**: SQS, Kinesis, DynamoDB Streams

### Pricing
- $0.20 per 1M requests
- $0.00001667 per GB-second


# AWS Elastic Load Balancing (ELB)

**ELB** distributes incoming traffic across multiple targets such as EC2 instances, containers, and IP addresses.

| Load Balancer | Layer | Main Use |
|---|---|---|
| **ALB** | Layer 7 | HTTP/HTTPS, APIs, microservices |
| **NLB** | Layer 4 | TCP/UDP, high performance, static IPs |
| **GWLB** | Layer 3/7 | Firewalls, IDS/IPS, security appliances |
| **CLB** | Layer 4/7 | Legacy applications |

### Quick Guide

- **ALB** → Intelligent HTTP/HTTPS routing
- **NLB** → High-performance TCP/UDP
- **GWLB** → Security appliance integration
- **CLB** → Legacy

---

# AWS Lambda

## Limits

- Max execution: **15 minutes**
- Memory: **128 MB - 10 GB**
- `/tmp`: **512 MB - 10 GB**
- Concurrent executions: **1,000 default**

## Invocation Types

### 1. Synchronous

The caller **waits for the response**.

```text
Client → Lambda → Response
```

Examples: API Gateway, CLI, ALB.

> **Synchronous = call and wait.**

### 2. Asynchronous

The caller **does not wait** for Lambda to finish.

```text
Event → Lambda → Processing
```

Examples: S3, SNS, EventBridge.

Lambda can automatically retry failed invocations.

> **Asynchronous = send and continue.**

### 3. Event Source Mapping

Lambda **polls the event source** and processes records in batches.

```text
SQS / Kinesis / DynamoDB Streams
              ↓
           Lambda
```

Examples: SQS, Kinesis, DynamoDB Streams, MSK.

> **Event Source Mapping = Lambda polls and processes batches.**

## Quick Comparison

| Type | Caller Waits? | Examples | Concept |
|---|---|---|---|
| **Synchronous** | Yes | API Gateway, CLI | Request → Response |
| **Asynchronous** | No | S3, SNS, EventBridge | Background processing |
| **Event Source Mapping** | No | SQS, Kinesis, DynamoDB Streams | Poll + batch |

## SAA-C03 Tip

```text
HTTP/HTTPS routing → ALB
TCP/UDP + performance → NLB
Security appliances → GWLB
Legacy → CLB

Call + wait → Synchronous
Send + continue → Asynchronous
Poll + batch → Event Source Mapping
```
---

## 5. Container Services

### ECS (Elastic Container Service)
**Launch Types:**
- **EC2**: You manage instances
- **Fargate**: Serverless, AWS manages

**Components:**
- Task Definition
- Task
- Service
- Cluster

### EKS (Elastic Kubernetes Service)
- Managed Kubernetes
- Compatible with standard K8s
- $0.10/hour cluster cost

### Fargate
- Serverless containers
- Works with ECS and EKS
- Pay per task

---

## 6. Other Services

### Elastic Beanstalk (PaaS)
- Deploy code, AWS handles infrastructure
- Supports: Java, .NET, PHP, Node.js, Python, Ruby, Go, Docker
- Auto scaling, load balancing included

### AWS Batch
- Managed batch computing
- Automatic resource provisioning
- Uses Spot instances for cost savings

### Lightsail
- Simple VPS
- Fixed monthly pricing
- Pre-configured templates

---

## 🎯 Exam Tips

### Decision Tree
```
Need compute?
├─ Run code without servers? → Lambda
├─ Containers?
│  ├─ Without managing servers? → Fargate
│  ├─ With Kubernetes? → EKS
│  └─ AWS orchestration? → ECS
├─ Just deploy code? → Elastic Beanstalk
└─ Full control? → EC2
```

### Common Scenarios
- **Variable workload** → Auto Scaling + Spot
- **Lowest latency networking** → Cluster placement group
- **Content-based routing** → ALB
- **Millions of req/sec, static IP** → NLB
- **Steady 3-year workload** → Reserved Instances
- **Fault-tolerant batch job** → Spot Instances
- **Run code on events** → Lambda
- **Serverless containers** → Fargate

### Key Points
- EC2 instance store = ephemeral (lost on stop)
- EBS = persistent block storage (AZ-specific)
- T instances = burstable CPU
- Spot instances = 2-minute warning before termination
- Lambda max = 15 minutes
- ALB = Layer 7, content routing
- NLB = Layer 4, extreme performance
- Auto Scaling ensures min/max capacity

---

**Estimated Study Time**: 8-10 hours  
**Difficulty**: ⭐⭐⭐

[⬅️ Previous: IAM](../02-IAM/README.md) | [Next: Storage ➡️](../04-Storage/README.md) | [📚 Main](../README.md)

