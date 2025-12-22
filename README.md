# Multi-Document Loan App — UiPath Performer

A comprehensive UiPath automation solution that implements a **multi-document loan application process** using the **Robotic Enterprise Framework (REFramework / Transactional Business Process)** template.

This project automates the end-to-end processing of loan applications, ensuring scalability, reliability, and clear exception handling through Orchestrator queues, assets, and storage buckets.

---

## Repository

**GitHub:** SontuCoder/Multi-Document-Loan-App-UiPath-Performer  
**Branch:** `main`

---

## Summary

This automation handles the **Performer layer** of a loan processing system.  
It consumes transactions from Orchestrator queues, processes completed upstream actions, validates loan data, interacts with storage buckets and databases, and sends customer notifications.

The solution follows REFramework standards for:
- Transactional execution
- Logging
- Exception handling
- Config-driven behavior

---

## Key Features

- REFramework-based state machine and transactional execution
- Orchestrator Queue integration
- UiPath Storage Bucket integration
- Centralized configuration using Config.xlsx and Orchestrator Assets
- Credential management via Orchestrator Assets or Windows Credential Manager
- Screenshot capture on system exceptions
- Modular and extensible workflow design

---

## Prerequisites

- UiPath Studio / UiPath Robot (Windows)
- UiPath Orchestrator (recommended)
- Configured Orchestrator Queue
- Configured Orchestrator Assets:
  - Queue name
  - Credential asset names
  - Application paths and timeouts
- Storage Bucket configured in Orchestrator
- Database access for loan validation (optional but recommended)

---

## High-Level Workflow

### 1. Initialization
- Loads configuration from `Config.xlsx` and Orchestrator Assets
- Initializes required applications
- Validates environment readiness

### 2. Transaction Retrieval
- Fetches transaction items from the configured Orchestrator Queue
- Each transaction represents a loan-related action to be processed

### 3. Transaction Processing
- Delegates business logic execution to modular workflows
- Handles validations, DB checks, and loan creation logic

### 4. Cleanup
- Gracefully closes applications
- Updates transaction status
- Releases system resources

---

## Performer Logic (Detailed)

The **Performer** is responsible for executing loan-related actions once upstream processes have completed successfully.

### Step-by-Step Performer Flow

### 1. Take Action Details from Queue
- Retrieve transaction item from Orchestrator Queue
- Extract:
  - Action status
  - Storage bucket reference
  - Customer and loan metadata

---

### 2. Validate Action Status
- If action status is **Completed** → Continue processing
- If action status is **Not Completed / Failed**:
  - Mark transaction as failed
  - Exit transaction gracefully

---

### 3. Fetch Output File from Storage Bucket
- Access the configured UiPath Storage Bucket
- Download the ZIP output file related to the transaction
- Store the file locally in the `Output` directory

---

### 4. Unzip and Extract Loan Details
- Unzip the downloaded archive
- Extract and parse loan-related information:
  - Customer details
  - Loan application data
  - Supporting documents
- Normalize extracted data for validation

---

### 5. Verify Existing Loan in Database
- Query database to check if a loan already exists for the customer
- Decision logic:
  - **Loan already exists**
    - Throw a **Business Exception**
    - Send failure notification email
    - Mark transaction as *Failed – Duplicate Loan*
  - **Loan does not exist**
    - Proceed to loan creation

---

### 6. Create New Loan
- Create a new loan record in the system/database
- Capture the generated **Loan ID**
- Ensure all validations and mandatory fields are satisfied

---

### 7. Send Customer Notification
- Send confirmation email to the customer
- Email includes:
  - Loan creation success message
  - Generated Loan ID
  - Next steps or reference information

---

### 8. Update Transaction Status
- Mark transaction as **Successful** in Orchestrator
- Attach output details if required (Loan ID, remarks, timestamps)

---

## Error Handling Strategy

### Business Exceptions
- Duplicate loan detected
- Invalid or incomplete loan data

### System Exceptions
- Storage bucket access failures
- ZIP extraction errors
- Database connectivity issues
- Application crashes

### On Exception:
- Screenshot captured using `TakeScreenshot.xaml`
- Error logged via REFramework logging mechanism
- Email notification sent when applicable
- Transaction status updated accordingly

---

## Configuration

- Update Assets with:
  - Orchestrator Queue name
  - Storage Bucket name
  - Asset names
  - Application paths and timeouts
- Ensure all Orchestrator Assets and credentials are available

---

## Running the Project

1. Open the project in UiPath Studio
2. Configure Assets and Queue in Orchestrator
3. Publish the package (optional) or run locally
4. Add transactions to the configured queue
5. Monitor execution via Orchestrator logs

---

## Development Notes

- New business steps can be added under `20. Process Works/`
- Invoke new workflows from `Process.xaml`
- Follow REFramework logging and exception patterns for consistency

---

## Troubleshooting

- Check `Output/` folder for downloaded and extracted files
- Review UiPath Robot and Orchestrator logs
- Validate Config.xlsx and Asset mappings

---

## Author

**Subhadip Maity**  
Automation Developer – RPA  
UiPath | Orchestrator | REFramework

---
