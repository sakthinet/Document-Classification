# Document Processing Flow Example - HR/Finance/Banking/Insurance

## 🏢 **EXAMPLE SCENARIO: HR DEPARTMENT DOCUMENT PROCESSING**

### **Document Types to Process:**
- **Employee Contracts** (Confidential)
- **Payroll Records** (Restricted) 
- **Performance Reviews** (Internal)
- **Company Policies** (Public)
- **Training Materials** (Public)

---

## 📱 **STEP 1: FRONTEND SOURCE SELECTION**

```
┌─────────────────────────────────────────────────────────────────┐
│                   BLAZOR FRONTEND INTERFACE                    │
│                                                                 │
│  👤 HR Manager logs in → Dashboard                             │
│                                                                 │
│  📁 DATA SOURCE CONFIGURATION:                                 │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │  📂 Source Type Selection:                             │   │
│  │     🔘 Network Folder: \\HR-SERVER\Documents          │   │
│  │     ⚪ SharePoint: https://company.sharepoint.com/hr│   │
│  │     ⚪ Database: HR Management System                  │   │
│  │                                                         │   │
│  │  📋 Document Type Mapping:                             │   │
│  │     • Folder: "Contracts" → Classification: CONFIDENTIAL│   │
│  │     • Folder: "Payroll" → Classification: RESTRICTED   │   │
│  │     • Folder: "Reviews" → Classification: INTERNAL     │   │
│  │     • Folder: "Policies" → Classification: PUBLIC      │   │
│  │                                                         │   │
│  │  🎯 Processing Rules:                                  │   │
│  │     • OCR Language: English + Spanish                  │   │
│  │     • AI Classification: Enable                        │   │
│  │     • PII Detection: Enable for HR documents           │   │
│  │     • Retention Policy: 7 years                        │   │
│  │                                                         │   │
│  │  ☁️ Output Destinations:                               │   │
│  │     • Confidential → Local NAS Only                   │   │
│  │     • Restricted → Local + Encrypted Azure Backup     │   │
│  │     • Internal → Local + Azure Selective Sync         │   │
│  │     • Public → Local + Full Azure Sync                │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│  🚀 [START PROCESSING] Button                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## 🌐 **STEP 2: API GATEWAY LAYER**

```
┌─────────────────────────────────────────────────────────────────┐
│                      API GATEWAY (YARP)                        │
│                                                                 │
│  📨 Request: POST /api/processing/start                        │
│  🔐 Authentication: Azure AD Token Validation                  │
│  👤 User: hr.manager@company.com                               │
│  🏢 Tenant: Company-HR-Department                              │
│                                                                 │
│  📋 Request Payload:                                           │
│  {                                                              │
│    "sourceType": "NetworkFolder",                              │
│    "sourcePath": "\\\\HR-SERVER\\Documents",                   │
│    "documentTypes": [                                          │
│      {"folder": "Contracts", "classification": "CONFIDENTIAL"},│
│      {"folder": "Payroll", "classification": "RESTRICTED"},    │
│      {"folder": "Reviews", "classification": "INTERNAL"},      │
│      {"folder": "Policies", "classification": "PUBLIC"}        │
│    ],                                                           │
│    "processingRules": {                                        │
│      "ocrLanguages": ["en", "es"],                             │
│      "enableAIClassification": true,                           │
│      "enablePIIDetection": true                                │
│    }                                                            │
│  }                                                              │
│                                                                 │
│  🎯 Route to: Configuration Service                            │
└─────────────────────────────────────────────────────────────────┘
```

---

## ⚙️ **STEP 3: BUSINESS SERVICES LAYER**

```
┌─────────────────────────────────────────────────────────────────┐
│                  CONFIGURATION SERVICE                         │
│                                                                 │
│  📝 Validate Request:                                          │
│     ✅ User has permission for HR document processing          │
│     ✅ Source path is accessible                               │
│     ✅ Classification levels are valid                         │
│     ✅ Tenant has sufficient processing quota                  │
│                                                                 │
│  💾 Store Configuration:                                       │
│     • Save to PostgreSQL: processing_jobs table               │
│     • Job ID: hr-proc-20241221-001                            │
│     • Status: PENDING                                          │
│     • Tenant: Company-HR-Department                           │
│                                                                 │
│  📤 Create Airflow DAG Request:                               │
│  {                                                              │
│    "job_id": "hr-proc-20241221-001",                          │
│    "dag_name": "hr_document_processing",                       │
│    "source_config": { ... },                                   │
│    "classification_rules": { ... },                            │
│    "output_routing": {                                         │
│      "CONFIDENTIAL": ["local_nas"],                            │
│      "RESTRICTED": ["local_nas", "azure_encrypted"],           │
│      "INTERNAL": ["local_nas", "azure_selective"],             │
│      "PUBLIC": ["local_nas", "azure_full"]                     │
│    }                                                            │
│  }                                                              │
│                                                                 │
│  🎯 Send to: Airflow API                                      │
└─────────────────────────────────────────────────────────────────┘
```

---

## 🔄 **STEP 4: WORKFLOW ORCHESTRATION (AIRFLOW 3.x)**

```
┌─────────────────────────────────────────────────────────────────┐
│                   APACHE AIRFLOW 3.X DAG                       │
│                                                                 │
│  📊 DAG: hr_document_processing                                │
│  🆔 Job ID: hr-proc-20241221-001                              │
│                                                                 │
│  🔄 Task Flow:                                                 │
│                                                                 │
│  [1] 📂 INGESTION TASK                                        │
│      ├── Scan: \\HR-SERVER\Documents                          │
│      ├── Find: 247 PDF files, 89 Word docs, 156 images        │
│      ├── Queue: Create processing tasks per document           │
│      └── Status: 492 documents queued for processing          │
│                 ↓                                               │
│  [2] 🔍 OCR PROCESSING (Parallel Workers)                     │
│      ├── Worker 1: employee_contract_001.pdf                  │
│      ├── Worker 2: payroll_summary_dec.xlsx                   │
│      ├── Worker 3: performance_review_john.docx               │
│      ├── OCR Engine: Azure Document Intelligence + PaddleOCR  │
│      └── Output: Extracted text + document structure          │
│                 ↓                                               │
│  [3] 🤖 AI CLASSIFICATION (ML Workers)                        │
│      ├── Analyze: Document content + filename + folder path   │
│      ├── Classify: CONFIDENTIAL/RESTRICTED/INTERNAL/PUBLIC    │
│      ├── Extract: PII detection (SSN, employee IDs, salaries) │
│      ├── Confidence: 95% average classification accuracy      │
│      └── Tag: Document metadata with classification           │
│                 ↓                                               │
│  [4] 📊 CLASSIFICATION RESULTS                                │
│      ├── CONFIDENTIAL: 45 documents (Employee contracts)      │
│      ├── RESTRICTED: 78 documents (Payroll, SSN data)         │
│      ├── INTERNAL: 123 documents (Reviews, internal memos)    │
│      ├── PUBLIC: 246 documents (Policies, training materials) │
│      └── UNKNOWN: 0 documents (manual review queue)           │
│                                                                 │
│  🎯 Trigger: Classified Output Routing                        │
└─────────────────────────────────────────────────────────────────┘
```

---

## 🎯 **STEP 5: CLASSIFIED OUTPUT ROUTING**

```
┌─────────────────────────────────────────────────────────────────┐
│                CLASSIFICATION-BASED ROUTING ENGINE             │
│                                                                 │
│  📋 Routing Decision Matrix:                                   │
│                                                                 │
│  🔒 CONFIDENTIAL Documents (45 docs):                         │
│      📄 Example: "John_Doe_Employment_Contract_2024.pdf"       │
│      ├── 🏢 PRIMARY: Local NAS (/confidential/contracts/)     │
│      ├── 🚫 CLOUD: No cloud storage (policy restricted)       │
│      ├── 🔍 SEARCH: Local Qdrant only (encrypted vectors)     │
│      └── 📊 METADATA: PostgreSQL (access_level: CONFIDENTIAL) │
│                                                                 │
│  🔐 RESTRICTED Documents (78 docs):                           │
│      📄 Example: "Payroll_Summary_December_2024.xlsx"          │
│      ├── 🏢 PRIMARY: Local NAS (/restricted/payroll/)         │
│      ├── ☁️ BACKUP: Azure Blob (encrypted, key vault)        │
│      ├── 🔍 SEARCH: Local Qdrant + Azure AI Search (encrypted)│
│      └── 📊 METADATA: PostgreSQL + Azure SQL (encrypted)      │
│                                                                 │
│  🏢 INTERNAL Documents (123 docs):                            │
│      📄 Example: "Performance_Review_Q4_2024.docx"            │
│      ├── 🏢 PRIMARY: Local NAS (/internal/reviews/)           │
│      ├── ☁️ SYNC: Azure Blob (selective sync)                │
│      ├── 🔍 SEARCH: Local Qdrant + Azure AI Search           │
│      └── 📊 METADATA: PostgreSQL + Azure SQL                 │
│                                                                 │
│  📖 PUBLIC Documents (246 docs):                              │
│      📄 Example: "Employee_Handbook_2024.pdf"                 │
│      ├── 🏢 PRIMARY: Local NAS (/public/policies/)            │
│      ├── ☁️ FULL SYNC: Azure Blob + CDN                      │
│      ├── 🔍 SEARCH: Local Qdrant + Azure AI Search + Global  │
│      └── 📊 METADATA: PostgreSQL + Azure SQL + Search Index  │
└─────────────────────────────────────────────────────────────────┘
```

---

## 💾 **STEP 6: FINAL STORAGE LOCATIONS**

### **🏢 ON-PREMISES STORAGE:**

```
Local Infrastructure:
├── 📁 NAS Storage (/mnt/hr-documents/):
│   ├── /confidential/contracts/ (45 docs) 🔒
│   ├── /restricted/payroll/ (78 docs) 🔐  
│   ├── /internal/reviews/ (123 docs) 🏢
│   └── /public/policies/ (246 docs) 📖
│
├── 🔍 Qdrant Vector Database:
│   ├── Collection: "hr_confidential" (45 embeddings)
│   ├── Collection: "hr_restricted" (78 embeddings)  
│   ├── Collection: "hr_internal" (123 embeddings)
│   └── Collection: "hr_public" (246 embeddings)
│
├── 🗄️ PostgreSQL Database:
│   ├── Table: classified_documents (492 records)
│   ├── Table: processing_jobs (1 job completed)
│   ├── Table: audit_logs (1,476 log entries)
│   └── Vectors: pgvector embeddings (492 vectors)
│
└── ⚡ Redis Cache:
    ├── Session: hr.manager@company.com
    ├── Job Status: hr-proc-20241221-001 = COMPLETED
    └── Search Cache: Recent queries and results
```

### **☁️ AZURE CLOUD STORAGE:**

```
Azure Resources:
├── 📦 Blob Storage (company-hr-documents):
│   ├── /restricted/ (78 docs - encrypted) 🔐
│   ├── /internal/ (123 docs - selective sync) 🏢  
│   └── /public/ (246 docs - full sync + CDN) 📖
│
├── 🔍 Azure AI Search (hr-search-index):
│   ├── Index: "hr_documents" (447 documents)
│   ├── Vectors: OpenAI embeddings (447 vectors)
│   ├── Fields: content, metadata, classification
│   └── Filters: classification_level, department
│
├── 🗄️ Azure SQL Database (backup):
│   ├── Table: document_metadata (447 records)
│   └── Sync: Automatic from on-premises PostgreSQL
│
└── 🔐 Key Vault:
    ├── Keys: Document encryption keys
    ├── Secrets: Storage connection strings  
    └── Certificates: TLS certificates
```

---

## 📊 **PROCESSING RESULTS SUMMARY**

### **📈 PROCESSING METRICS:**
- **Total Documents**: 492 processed
- **Processing Time**: 2.5 hours (avg 18 seconds/document)
- **Classification Accuracy**: 95% (23 docs flagged for manual review)
- **Storage Distribution**:
  - 🔒 **Local Only**: 45 docs (9%)
  - 🔐 **Local + Encrypted Cloud**: 78 docs (16%)
  - 🏢 **Local + Selective Cloud**: 123 docs (25%)
  - 📖 **Local + Full Cloud**: 246 docs (50%)

### **🔍 SEARCH CAPABILITIES:**
- **Local Vector Search**: All 492 documents (instant access)
- **Cloud Vector Search**: 447 documents (global access)
- **Hybrid Search**: Text + semantic + classification filters
- **Access Control**: Role-based filtering by classification level

### **📱 FRONTEND NOTIFICATIONS:**
```
✅ Processing Complete: HR Document Batch
📊 Results: 492/492 documents processed successfully  
🔒 Security: Documents classified and stored per policy
🔍 Search: Vector search enabled for all documents
⏱️ Duration: 2h 35m (within SLA)
📈 Accuracy: 95% classification confidence
🚨 Action: 23 documents require manual review
```

This example shows how HR documents flow from frontend selection through the entire processing pipeline, with classification-based routing to appropriate storage locations while maintaining both local control and cloud scalability.