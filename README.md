# IT Asset Lifecycle & Automation System

## Project Overview
This project replaces manual hardware tracking with a relational Dataverse-backed system. It automates the transition between asset states (e.g., "Available" to "In Maintenance" or "On Loan") and provides real-time notifications to the SecOps team.

## Core Architecture
* **Frontend**: Model-Driven Power App with relational subgrids.
* **Backend**: Dataverse (Relational Schema).
* **Automation**: Power Automate Cloud Flows (Logic Apps).
* **Communication**: Microsoft Teams (Adaptive Cards & Webhooks).

## Technical Implementation Highlights

### 1. Zero-Trust RBAC Model
Instead of using broad administrative roles, I engineered a custom **IT Technician** security role. 
* **Principle of Least Privilege**: Technicians have full CRUD access to maintenance and checkout logs but restricted access to environment-wide configurations.
* **Validation**: Assigned and tested for user 'Alex Cortez' to ensure zero permission deadlocks during automation.

### 2. State-Machine Automation
Built logic flows that act as the "brain" of the inventory system:
* **Maintenance Sync**: When a technician adds a repair log, the master asset record's status is automatically flipped to "In Maintenance".
* **Checkout Sync**: When a device is loaned out, the system updates the availability status to "On Loan" and logs the borrower.

### 3. Overcoming Dataverse OData Limitations
One major technical hurdle was the "Blank Lookup" issue where Dataverse triggers only provide GUIDs (ID strings) rather than text. 
* **The Solution**: Implemented "Get a row by ID" steps for both the Asset and Technician tables.
* **The Result**: Successfully fetched human-readable strings (e.g., "ABARTH", "Alex Cortez") for the Teams notification payload.

## Data Schema
| Table | Relationship | Key Fields |
| :--- | :--- | :--- |
| All Assets | Primary (1) | Tag ID, Device Availability (Choice) |
| Maintenance Logs | Child (N) | Service Ticket ID, Work Performed |
| Checkouts | Child (N) | Loan ID, Borrower (Lookup) |
