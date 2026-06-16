# TIDEGENT — AI-Powered Disk Cleanup Recommender

## Problem Statement

Traditional disk cleanup tools rely heavily on static rules such as file age, file size, or predefined system folders. While effective for removing temporary files, these approaches often fail to distinguish between important user files and genuinely redundant data, increasing the risk of accidental deletion.

Organizations and individual users frequently accumulate large volumes of outdated archives, temporary files, duplicate files, logs, and generated content. Manual cleanup is time-consuming, inconsistent, and prone to human error.

TIDEGENT addresses this challenge by combining AI-driven file classification, duplicate detection, safety validation, and human approval workflows to provide intelligent cleanup recommendations while minimizing deletion risks.

---

# Architecture

```text
USER
  |
  v
Discord Commands
  |
--------------------------------------------------------------
|            |             |             |                  |
v            v             v             v                  v

/agent      /custom      /restore      /delete      /create_test_data
threshold   threshold       all           all

  |
  v
discord_bot.py
  |
  v
scanner.py
  |
  v
Metadata Extraction
  |
  v
--------------------------------------------------------------
|               |               |               |
v               v               v               v

File Size    Extension      Category      Last Accessed

  |
  v
Duplicate Detection
  |
-----------------------------
|                           |
Duplicate?                 No
|                           |
v                           v

SAFE_DELETE           cache_manager.py
                            |
                            v
                  Threshold Changed?
                            |
                 -------------------
                 |                 |
                Yes               No
                 |                 |
                 v                 v
          Force Reanalysis     Check Cache
                                     |
                          ---------------------
                          |                   |
                     Cache Hit?          Cache Miss
                          |                   |
                          v                   v

                Compare Cached Age      classifier.py
                with Current Age             |
                          |                 |
                -------------------         |
                |                 |         |
      Difference < Limit?        No         |
                |                 |         |
               Yes                |         |
                |                 |         |
                v                 |         |
      Use Cached Result           |         |
                                  |         |
                                  v         v
                           Force Reanalysis
                                  |
                                  v
                          Ollama + Qwen 2.5
                                  |
                                  v
                       KEEP / SAFE_DELETE
                                  |
                                  v
                    Rule-Based Safety Layer
                                  |
-----------------------------------------------------------------------
|                         |                      |                    |
v                         v                      v                    v

Temporary Files     Generated Files      User Documents        Archives

(cache,tmp,log)    (export,download)    (project,report,notes) (backup,zip)

      |                    |                    |                   |
      v                    v                    v                   v

Aggressive           Moderate          Conservative         Moderate

      |
      v

Age Override Rules

      |
      v

Final Decision
KEEP / SAFE_DELETE / REVIEW
      |
      v

Human Approval Workflow
      |
      v

Recycle Bin (deleted_files)
      |
-----------------------------
|                           |
v                           v

Restore All          Permanent Delete All

      |
      v

PDF Reports & Audit Logs
```

---

# Features

### AI-Powered File Classification

* Uses Qwen 2.5 running locally through Ollama
* Classifies files as:

  * KEEP
  * SAFE_DELETE
  * MANUAL_REVIEW

### Duplicate File Detection

* SHA-256 hashing
* Automatically identifies exact duplicate files
* Retains the newest version and flags duplicates for deletion

### Intelligent Caching

* Stores previous classification results
* Detects threshold changes
* Detects significant file age changes
* Prevents unnecessary LLM re-classification

### Multi-Layer Safety Validation

Additional protection layer beyond AI recommendations:

* Temporary Files → aggressive cleanup
* Generated Files → moderate cleanup
* Archives → moderate cleanup
* User Documents → conservative cleanup

### Human-in-the-Loop Approval

No files are automatically deleted.

Users must explicitly approve cleanup actions through Discord before files are moved.

### Recycle Bin Support

Deleted files are moved to:

```text
deleted_files/
```

instead of being permanently removed.

### Restore Functionality

Restore all deleted files back to the monitored directory.

### Permanent Deletion

Permanently remove files from the recycle bin when required.

### PDF Report Generation

Automatically generates:

* Classification Reports
* Deletion Reports
* Restore Reports
* Permanent Delete Reports

### Discord Bot Interface

Supports commands such as:

```text
/start
/agent_threshold
/custom_threshold
/list_files
/list_files_rb
/create_test_data
/restore
/delete
/help
```

### Test Data Generation

* Generate configurable numbers of demo files
* Simulates realistic file categories
* Injects duplicate files for testing

---

# Tech Stack

## Backend

* Python
* Discord.py

## AI / Machine Learning

* Ollama
* Qwen 2.5

## Data Processing

* Metadata Analysis
* SHA-256 Hashing

## Optimization

* JSON-Based Caching
* Cache Validation Logic

## Reporting

* ReportLab PDF Generation

## Automation

* File System Automation
* Workflow Automation

## Bot Interface

* Discord Slash Commands
* Interactive Approval Workflows

---

# Results

### Evaluation Dataset

* Approximately 90–100 generated test files
* Multiple file categories
* Duplicate file scenarios
* Long-term and short-term access patterns

### Performance

| Metric                 | Result      |
| ---------------------- | ----------- |
| Files Evaluated        | ~100        |
| Human Review Agreement | ~92%        |
| Duplicate Detection    | Implemented |
| Human Approval Gate    | Implemented |
| Recycle Bin Recovery   | Implemented |
| PDF Audit Reports      | Implemented |

### Key Outcome

TIDEGENT achieved approximately **92% agreement with human evaluation** during testing while maintaining a low false-deletion rate through its multi-layer safety framework.

The system prioritizes safe recommendations over aggressive cleanup, ensuring potentially important files are preserved or routed for manual review.

---

# Future Enhancements

* Content-aware file analysis
* Semantic file understanding
* Multi-user support
* Web dashboard
* Cloud deployment
* Enterprise audit logging
* Fine-tuned classification models
* File embedding and similarity detection

---

## Author

**Deepanath J**

Artificial Intelligence & Data Science Undergraduate

TIDEGENT — Intelligent Disk Cleanup Through AI-Assisted Decision Making
