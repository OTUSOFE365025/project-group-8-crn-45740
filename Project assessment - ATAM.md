# Project assessment - ATAM
## *1. Scenario Analysis*
### Scenario 1 - SEC–S1: Student attempts to view another student’s grades
| **Element** | **Description** |
|------|-----------|
| Attribute | Security & Privacy |
| Stimulus | A logged-in student attempts to access another student's grade record. |
| Environment | Normal system operation; user is authenticated but unauthorized for the resource.|
| Response |API Gateway checks RBAC rules using SSO token, detects insufficient permissions, blocks request, and returns no grade data.


### Scenario 2 - SEC–S2: Multiple unauthorized access attempts occur
| **Element** | **Description** |
|------|-----------|
| Attribute | Security |
| Stimulus | A user repeatedly attempts to access a protected resource. |
| Environment | High backend activity during peak usage. |
| Response | API Gateway rejects each request and logs all attempts into the Monitoring Component. |

### Scenario 3 -SEC–S3 / MOD–S1: Admin updates user roles or permissions
| **Element** | **Description** |
|------|-----------|
| Attribute | Modifiability & Security |
| Stimulus | Admin modifies a student’s or instructor’s roles or permissions. |
| Environment | Normal system operation with active users. |
| Response | User/Profile Service updates permissions; API Gateway applies new rules on the next request. |

### Scenario 4 -PERF–S1: High volume of login and token validation requests
| **Element** | **Description** |
|------|-----------|
| Attribute | Performance |
| Stimulus | Many students try to log in simultaneously. |
| Environment | Heavy concurrent load on authentication services. |
| Response |SSO generates signed tokens efficiently; API Gateway validates tokens quickly without blocking. |

### Scenario 5 - PERF–S2: High volume of academic data retrieval requests
| **Element** | **Description** |
|------|-----------|
| Attribute | Performance & Security |
| Stimulus | Students repeatedly request grades, history, or participation data. |
| Environment | High traffic from multiple front-end clients. |
| Response | API Gateway reuses signed tokens, routes requests efficiently, and denies unauthorized requests early. |

## *ATAM risk assessment table*
| **ID** | **Scenario** | **Quality Attribute** | **Architectural Decision** | **Sensitivity** | **Tradeoff** | **Risk** | **Non-Risk** |
|----|---------|--------------------|------------------------|-------------|----------|------|---------|
| 1 | SEC–S1: student attempts to view another student's grades | Security & Privacy | AD1 – API Gateway enforces RBAC using SSO token | S1 | T1 | R1 | NR1|
| 2 | SEC–S1 / SEC–S2 | Security & Auditability | AD3 – Gateway logs all requests to Monitoring Component | S2 | T2 | R2 | NR2|
| 3 | SEC–S3 & MOD–S1 | Modifiability & Security | AD2 – Roles & permissions stored in User/Profile Service | S3 | T3 | R3 | NR3 |
| 4 | PERF–S1 / PERF–S2 | Performance & Security | AD4 – SSO issues signed identity tokens reused across requests | S4 | T4 | R4 | NR4 |
| 5 | SEC–S1 / SEC–S2 / PERF–S2 | Security & Performance | AD5 – Unauthorized requests denied at API Gateway and logged | S5 | T5 | R5 | NR5 |

## *2. Description of the risks, non-risks, sensitivity and tradeoffs.*
### 2.1 Risks (R)
- R1. Incorrect or incomplete authorization rules in the API Gateway may allow unauthorized access to private academic information.
- R2. High volumes of access attempts may overload the logging process, causing missing entries in the Monitoring Component.
- R3. Outdated or inconsistent role/permission data in the User/Profile Service may grant users more privileges than intended.
- R4. Token lifetime settings may cause performance issues (too frequent re-authentication) or security vulnerabilities (tokens valid for too long).
- R5. If access checks are performed too late in the pipeline, partial information may be exposed before the request is denied.

### 2.2 Non-Risks (NR)
- NR1. Centralizing access control in the API Gateway ensures consistent enforcement of security rules across all services.
- NR2. Authorization is kept separate from the UI and AI Engine logic, preventing bypass through client-side behaviour or prompts.
- NR3. Unauthorized requests are blocked at the gateway even if the Monitoring Component is temporarily unavailable.
- NR4. Using signed identity tokens reduces the need to send credentials repeatedly, lowering the risk of credential leakage.
- NR5. The layered architecture and independent services keep privacy-critical functions isolated from unrelated components.

### 2.3 Sensitivity Points (S)
- S1. The accuracy of authorization rules in the API Gateway directly determines whether private data is protected.
- S2. The Monitoring Component’s capacity influences how reliably unauthorized access attempts are recorded.
- S3. The role and permission structure in the User/Profile Service strongly affects both security enforcement and ease of updates.
- S4. Token expiration and signing parameters impact the balance between secure authentication and response time.
- S5. Logging granularity (what is logged and how often) impacts system overhead and the usefulness of security records.

2.4 Tradeoffs (T)
- T1. Stronger authorization checks improve security but increase request processing time in the API Gateway.
- T2. Detailed logging improves incident analysis but increases storage usage and processing overhead.
- T3. A flexible and expressive role model improves maintainability but increases configuration and testing complexity.
- T4. Short-lived tokens improve security but require more frequent validation, potentially affecting system performance.
- T5. Centralizing access control in the API Gateway simplifies policy enforcement but creates a potential bottleneck under high load.

## *ATAM Utility tree* 
<pre>
Utility
 ├── Security & Privacy (H, H)
 │     ├── SEC–S1 (H, H):
 │     │       Deny unauthorized access to academic records 
 │     │       and log the attempt.
 │     └── SEC–S2 (H, M):
 │             Ensure denied attempts are tied to identity
 │             and timestamp reliably.
 │
 ├── Performance (M, M)
 │     ├── PERF–S1 (H, M):
 │     │       Token validation + access checks must
 │     │       complete within acceptable response time.
 │     └── PERF–S2 (M, H):
 │             High-volume academic data requests
 │             must not degrade system responsiveness.
 │
 └── Modifiability (M, M)
       ├── MOD–S1 (H, M):
       │       Adding new roles/permissions should only
       │       require updates in Profile Service + Gateway.
       └── MOD–S2 (M, M):
               Changing monitoring backend must not
               affect request-handling logic.
(H = High priority, M = Medium priority, L = Low effort)
</pre>
