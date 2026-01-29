# Production-Grade Cloud Networking Project 

## Project Objective
Design and implement a scalable, production-ready AWS VPC following enterprise best practices for CIDR planning, multi-size subnets, and controlled internet access.

---

## Network Architecture

### VPC Configuration
- **VPC Name:** `Ashu-VPC`
- **CIDR Block:** `10.0.0.0/16`
- **Tenancy:** Default

![VPC Creation](file:///home/ashu/Downloads/notes-aws/task/pics/Screenshot_20260128_113501.png)

### Subnet Design (Unequal Sizes)
We implemented 6 subnets with varying capacities, allocating the largest blocks first to optimize IP space.

| Subnet Name | Purpose | CIDR Block | Capacity (IPs) | Type |
| :--- | :--- | :--- | :--- | :--- |
| **Shared** | Internal Services | `10.0.0.0/19` | 8,187 | Private |
| **Platform** | Containers / Tools | `10.0.32.0/20` | 4,091 | Private |
| **App** | App Tier | `10.0.56.0/21` | 2,043 | Private |
| **Web** | Web Tier | `10.0.64.0/22` | 1,019 | Private |
| **Edge** | Ingress / LB | `10.0.68.0/23` | 507 | Public |
| **Admin** | Bastion / Ops | `10.0.70.0/24` | 251 | Public |

![Subnet Analysis](file:///home/ashu/Downloads/notes-aws/task/pics/Screenshot_20260128_114337.png)
![IP Capacity Validation](file:///home/ashu/Downloads/notes-aws/task/pics/Screenshot_20260128_114415.png)

---

## Routing & Internet Access

### Internet Gateway
An Internet Gateway was created and attached to allow controlled internet access for public subnets.

![IGW Attachment](file:///home/ashu/Downloads/notes-aws/task/pics/Screenshot_20260128_114720.png)

### Route Table Architecture
A structured routing policy ensures that internet access is intentional and secure.

| Route Table | Target | Destination | Associated Subnets |
| :--- | :--- | :--- | :--- |
| **Public_RT** | Internet Gateway | `0.0.0.0/0` | Admin, Edge |
| **Private-RT** | Local | `10.0.0.0/16` | Web, App, Platform, Shared |

![Public Route Config](file:///home/ashu/Downloads/notes-aws/task/pics/Screenshot_20260128_115123.png)
![Private Route Config](file:///home/ashu/Downloads/notes-aws/task/pics/Screenshot_20260128_115254.png)

> [!IMPORTANT]
> Private subnets have **no default route** to the internet, ensuring full isolation from external threats.

---

## Implementation & Validation

### 1. Subnet Associations
The following screenshots show the explicit association of subnets with their respective route tables.

![Public Associations](file:///home/ashu/Downloads/notes-aws/task/pics/Screenshot_20260128_115312.png)
![Private Associations](file:///home/ashu/Downloads/notes-aws/task/pics/Screenshot_20260128_115352.png)

### 2. Verification Environment
Instances were deployed in both Public and Private segments to verify routing behavior.

![EC2 Verification Setup](file:///home/ashu/Downloads/notes-aws/task/pics/Screenshot_20260128_140814.png)
![Bastion Instance Details](file:///home/ashu/Downloads/notes-aws/task/pics/Screenshot_20260128_140834.png)

---

## Technical Outcomes
- **Public Subnets:** Reach the internet via IGW.
- **Private Subnets:** Fully isolated from the internet (Local routing only).
- **Scalability:** Over 50% of the `/16` space remains available for future growth.
