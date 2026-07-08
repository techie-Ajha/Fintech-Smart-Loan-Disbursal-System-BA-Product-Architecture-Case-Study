# Fintech-Smart-Loan-Disbursal-System-BA-Product-Architecture-Case-Study
An internal dashboard for bank officers to review loan applications requiring manual verification. It displays application statistics, applicant details, CIBIL score, AI risk assessment, and provides options to approve disbursal or reject applications, enabling secure and efficient loan decision-making.

# Fintech Smart Loan Disbursal System — BRD & Product Architecture Case Study

## 📌 1. Project Overview & Problem Statement
* **The Problem:** Legacy retail banking loan processes in India take **7 to 10 business days** due to physical form filing, manual document tracking, physical couriers, and repetitive portal-based verification checks. This leads to massive customer drop-off rates and high operational costs.
* **The Solution:** A secure, automated, API-first loan evaluation and disbursal ecosystem designed to reduce the complete loan lifecycle from days to **under 60 seconds** without requiring human intervention for prime applicants.

---

## 📊 2. Gap Analysis & Process Comparison Matrix

| Metric / Process Step | As-Is Process (Manual & Legacy) | To-Be Process (Our Automated System) | Technical Bridge / Solution |
| :--- | :--- | :--- | :--- |
| **Turnaround Time (TAT)** | 7 to 10 business days due to manual document routing. | **Under 1 Minute** from submission to final account credit. | Asynchronous Node.js micro-workflows and cloud infrastructure. |
| **Identity & KYC Check** | Manual verification of physical photocopies of Aadhaar and PAN cards by branch staff. | **Instant e-KYC Verification** executed directly on the dashboard. | Direct integration with the **DigiLocker API** for real-time data fetch. |
| **Credit Risk Evaluation** | Credit officers manually log into external portals, type details, and assess static reports. | **Automated Credit Score Pulling** and parsing logic. | **CIBIL API** integration executing automated scoring checks within 3 seconds. |
| **Loan Rejection Handling** | Rejection letters are physically printed and couriered, causing an additional 3-4 days delay. | **Instant Dashboard Rejection Notification** triggered within 2 seconds. | Automatic evaluation routing that flags and logs reasons inside PostgreSQL. |
| **Data & Record Keeping** | Stored across physical ledger files, paper cabinets, and slow core banking systems. | Automated data segregation based on transaction mutations. | **PostgreSQL** for strict relational audit trails and **MongoDB** for flexible profile scaling. |

---

## 📄 3. Business & Functional Requirements Document (BRD/FRD)

### 3.1 Scope Matrix
* **In-Scope:**
  * Secure multi-factor authentication using mobile OTP gateways.
  * Instant identity extraction via authenticated DigiLocker API connections.
  * Real-time automated parsing of credit limits using the CIBIL API layer.
  * Data architectural split: Strict structured logging in **PostgreSQL** for compliance/rejections; non-relational document indexing in **MongoDB Atlas** for managing elastic, unstructured customer loan profiles.
  * Seamless webhook execution triggering automated payment gateways (RazorpayX/IMPS).
* **Out-of-Scope:**
  * Physical verification visits or manual file auditing by on-field bank agents.
  * Integration with external employment scraping tools (e.g., parsing LinkedIn/Naukri data fields).
  * Post-disbursal debt collections, automated EMI recovery, or manual legal handling systems.

### 3.2 Core Functional Requirements (FRs)
* **FR-1:** The system **must** prevent unauthenticated entry and validate the borrower's session via a secure 6-digit OTP mapping to their Aadhaar-linked phone network.
* **FR-2:** The backend **must** pass sanitized PAN entries to the third-party CIBIL REST endpoints within 1 second of step submission.
* **FR-3:** If the returned credit metric is **less than 700 ($< 700$)**, the automated gate **must** instantly kill the process, flash a standardized rejection alert to the frontend under 2 seconds, and commit an immutable log entry inside **PostgreSQL**.
* **FR-4:** If the score is **greater than or equal to 700 ($\ge 700$)**, the system **must** pipeline the payload into the core engine to calculate dynamic repayment capabilities and update metadata inside **MongoDB**.

### 3.3 Non-Functional Requirements (NFRs)
* **NFR-1 (Security):** All critical Personally Identifiable Information (PII) including financial balances, Aadhaar keys, and PAN variables **must** be encrypted in transit and at rest using the **AES-256 standard**.
* **NFR-2 (Latency):** The cumulative orchestration loop (DigiLocker check + CIBIL API parsing + Risk scoring) **must** compute in less than **3 seconds** under nominal network loads.
* **NFR-3 (Scalability):** The backend execution environment **must** easily sustain up to **10,000 concurrent loan applications** without latency inflation or application crashing.

---

## 👥 4. User Personas & System Entities

### 4.1 Target Personas
* **The Retail Borrower:** Wants a fast, documentation-free interface with a visual progress indicator to secure emergency cash instantly without physical office visits.
* **The Credit Operations Manager:** Needs a data-dense analytical interface showing aggregate processing numbers alongside transparent AI risk markers for system auditing.

### 4.2 Data Management Architecture
* **Relational Database Schema (PostgreSQL):** Handles fixed relational objects (`Application_ID` as UUID, `CIBIL_Score`, `Timestamp`, and `Rejection_Reason`) ensuring high ACID compliance for financial audit trails.
* **Document Object Schema (MongoDB Atlas):** Organizes elastic structural models (`Repayment_Tenures`, variable income streams, metadata buckets, and dynamic loan configurations) allowing horizontal scaling.

---

## ⚠️ 5. Exception & Boundary Error Handling
* **API Timeout Interception:** If external systems (such as the CIBIL provider gateway) fail to return data within a 3-second threshold window, the application logic **must** intercept the failure, log the server fault trace, and transition the consumer to a graceful retry view to prevent app crashes.
* **Security Rate Limiting:** Incorrect input submittals during verification states **must** lock out consecutive input submissions after 3 continuous failed tries for 15 minutes to block malicious brute-force attempts.

---

## 🧠 6. Key Learnings (Interview Anchors)
> "Through this system design, I mastered the process of mapping complex operational business flows into clear API boundaries. I learned to structure structured vs. unstructured business problems by choosing **PostgreSQL** for relational compliance reporting and **MongoDB** for flexible loan lifecycle storage. Additionally, writing precise, measurable requirements (like sub-3-second latencies and strict out-of-scope parameters) taught me how an effective product blueprint mitigates edge-case errors before the development lifecycle even begins."

## 📂 7. Project Artifacts & Links

* 📄 **Complete BRD/FRD Word Document:** [Download BRD_Document.docx](./documentation/BRD_Document.docx)
* 🎨 **Interactive Figma Wireframes:** [View Figma Prototype Link](PASTE_YOUR_FIGMA_SHARE_LINK_HERE)
* 📊 **Lucidchart Workflows:** [View Process Maps](./documentation/process_maps.png)
