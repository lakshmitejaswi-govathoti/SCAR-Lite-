# Supply Chain Resilience Agent (SCRA) Lite - Salesforce Agentforce Project

This repository contains the configuration details for SCRA Lite, an AI-powered agent built declaratively on the Salesforce Platform using Agent Builder and Flow.

## Overview

SCRA Lite is an internal-facing agent designed to assist Supply Chain Managers within Salesforce. When supplier disruptions occur, managers can quickly interact with the agent via chat to:

1.  **Log the disruption details:** Creates a structured `Case` record in Salesforce.
2.  **Find pre-approved alternative suppliers:** Queries `Account` records based on product category to provide immediate recommendations.

This aims to streamline the initial response process during critical supply chain events, improving efficiency and data capture.

## Features

*   **Report Supplier Disruptions:** Captures supplier name, affected product categories, and disruption reason via conversation.
*   **Automated Case Logging:** Creates a `Case` record with a specific Record Type (`Supply Chain Disruption`), sets Status to `Mitigation Required`, Origin to `Agent Reported`, and populates relevant details.
*   **Find Alternative Suppliers:** Queries `Account` records flagged as `Is Approved Alternative Supplier` and matching the specified `Supplied Product Categories`.
*   **Conversational Interface:** Built using Salesforce Agent Builder with defined Intents, Entities, and Dialogs.
*   **Declarative Automation:** Uses Salesforce Flow for backend logic (record creation and querying) without requiring custom Apex code.

## Technology Stack

*   **Platform:** Salesforce Platform (Lightning Experience)
*   **Core Technologies:**
    *   Salesforce Agent Builder (Einstein Bots)
    *   Salesforce Flow (Autolaunched Flows)
    *   Salesforce Standard Objects (Account, Case) with Custom Fields & Record Types
    *   Salesforce Embedded Service Chat
    *   Salesforce Lightning App Builder

## Setup Instructions

This project requires a Salesforce org (like the ones provided for hackathons or a Developer Edition org). The setup involves configuring Salesforce objects, building Flows, setting up the Agent in Agent Builder, and deploying the chat interface.

**Prerequisites:** Access to a Salesforce org with administrative privileges.

**Setup Phases:**

1.  **Phase 1: Salesforce Setup (Objects, Fields, Record Type, Data)**
    *   Customize the `Account` object by adding `Is Approved Alternative Supplier` (Checkbox) and `Supplied Product Categories` (Text Area) fields.
    *   Customize the `Case` object by creating a `Supply Chain Disruption` Record Type, adding custom fields (`Disruption Reason`, `Affected Product Categories`, `Disrupted Supplier`), and adding custom `Status` and `Origin` picklist values.
    *   Configure Page Layouts.
    *   Populate sample `Account` data representing primary and alternative suppliers.

2.  **Phase 2: Build Helper Flows**
    *   Create `SCRA_LogDisruptionCase` (Autolaunched Flow): Takes disruption details as input and creates a `Case` record.
    *   Create `SCRA_FindAlternativeSuppliers` (Autolaunched Flow): Takes a product category as input, queries `Account` records based on custom fields, loops through results, and returns a concatenated string of alternative supplier names.

3.  **Phase 3: Configure Agent Builder**
    *   Create a new Agent named `SCRA Lite`.
    *   Define Intents: `Report Disruption` and `Find Alternatives` with relevant training phrases.
    *   Define Entities: `SupplierName`, `ProductCategory`, `Reason` (all Text type).
    *   Build Dialogs: `Welcome`, `Main Menu`, `Report Disruption`, `Find Alternatives`.
        *   Use `Question` elements to gather information and extract entities into variables.
        *   Use `Action` elements to call the corresponding Flows, passing variables as inputs and storing outputs.
        *   Use `Message` elements to provide responses and confirmations.
        *   Use `Rule` elements for conditional logic (checking if alternatives were found) and navigation (returning to Main Menu).
    *   Activate the Agent.

4.  **Phase 4: Testing**
    *   Use the Agent Builder `Preview` feature to test the conversational flows for both reporting disruptions and finding alternatives.
    *   Verify that `Case` records are created correctly in Salesforce.
    *   Verify that the correct alternative suppliers are returned based on sample data.

5.  **Phase 5: Deployment**
    *   Set up an `Embedded Service Deployment` for Chat, linking it to the `SCRA Lite` agent.
    *   Use the `Lightning App Builder` to create a new App Page or edit an existing one.
    *   Add the `Embedded Service Chat` component to the page and configure it to use the created deployment.
    *   Activate the page for relevant users/apps.

**Note:** For detailed, click-by-click instructions on implementing each phase within Salesforce Setup, please refer to the specific build guide documentation provided alongside this project.

## Testing Verification

Use the Agent Builder Preview feature or the deployed chat component.

1.  **Test based *exactly* on the detailed step-by-step instructions you provided for building the SCRA Lite Agent.

```markdown
# Supply Chain Resilience Agent (SCRA) Lite

## Overview

SCRA Lite is an internal-facing AI agent built entirely on the Salesforce Platform using Agent Builder (Einstein Bots) and Salesforce Flow. It assists Supply Chain Managers by allowing them to quickly report supplier disruptions and find pre-approved alternative suppliers via a conversational chat interface.

This project aims to streamline the initial response to supply chain issues, ensuring disruptions are formally logged and potential solutions are identified rapidly.

## Features

*   **Report Supplier Disruptions:** Users can conversationally provide details about a supplier disruption (supplier name, reason, affected categories). The agent logs this information by creating a `Case` record in Salesforce with a specific Record Type and details.
*   **Find Alternative Suppliers:** Users can ask for alternative suppliers for a specific product category. The agent queries `Account` records flagged as approved alternatives for that category and returns a list of names.

## Technology Stack

*   **Platform:** Salesforce Platform (Lightning Experience)
*   **AI / Conversation:** Salesforce Agent Builder (Einstein Bots)
*   **Automation:** Salesforce Flow (Autolaunched Flows)
*   **Data Storage:** Standard Salesforce Objects (`Account`, `Case`) with custom fields and Record Types.
*   **Deployment:** Embedded Service Chat, Lightning App Builder

## Setup and Configuration Instructions

Follow these steps precisely within your Salesforce org to build and configure SCRA Lite.

### Prerequisites

*   Access to a Salesforce Org (Developer Edition, Sandbox, or specific Hackathon Org).
*   Appropriate permissions to customize objects, create flows, configure Agent Builder, and manage Embedded Service.

### Phase 1: Salesforce Configuration (Objects, Fields, Record Types, Data)

1.  **Navigate to Setup:** Click the Gear Icon ⚙️ -> Setup.
2.  **Navigate to Object Manager:** Click Object Manager in the left sidebar.
3.  **Customize Account Object:**
    *   Go to `Account` -> Fields & Relationships -> New.
    *   Create **Checkbox** field:
        *   Label: `Is Approved Alternative Supplier`
        *   Default Value: `Unchecked`
        *   Field Name: `Is_Approved_Alternative_Supplier__c`
        *   Set Visibility & Add to Layouts. Save & New.
    *   Create **Text Area** field:
        *   Label: `Supplied Product Categories`
        *   Field Name: `Supplied_Product_Categories__c`
        *   Set Visibility & Add to Layouts. Save.
4.  **Customize Case Object:**
    *   Go to `Case` -> Record Types -> New.
        *   Existing Record Type: `-- Master --`
        *   Label: `Supply Chain Disruption`
        *   Name: `Supply_Chain_Disruption`
        *   Active: Checked
        *   Enable for relevant profiles. Next.
        *   Assign Page Layout (`Case Layout`). Save.
    *   Go to `Case` -> Fields & Relationships -> New.
    *   Create **Text Area** field:
        *   Label: `Disruption Reason`
        *   Field Name: `Disruption_Reason__c`
        *   Set Visibility & Add to Layouts. Save & New.
    *   Create **Text Area** field:
        *   Label: `Affected Product Categories`
        *   Field Name: `Affected_Product_Categories__c`
        *   Set Visibility & Add to Layouts. Save & New.
    *   Create **Lookup Relationship** field:
        *   Related To: `Account`
        *   Label: `Disrupted Supplier`
        *   Field Name: `Disrupted_Supplier__c`
        *   Set Visibility & Add to Layouts. Save.
    *   Go to `Case` -> Fields & Relationships -> Click `Status` field.
        *   Under Case Status Picklist Values, click New. Value: `Mitigation Required`. Save.
    *   Go to `Case` -> Fields & Relationships -> Click `Case Origin` field.
        *   Under Case Origin Picklist Values, click New. Value: `Agent Reported`. Save.
    *   Go to `Case` -> Page Layouts -> Click layout assigned to `Supply Chain Disruption` Record Type.
        *   Drag `Disrupted Supplier`, `Disruption Reason`, `Affected Product Categories` fields onto the layout. Save.
5.  **Populate Sample Data:**
    *   Go to App Launcher -> Accounts -> New.
    *   Create Sample Accounts:
        *   `Primary Components Inc.` (Supplied Product Categories: `Electronics`, Is Approved Alternative Supplier: `Unchecked`)
        *   `Alt Electronics Ltd.` (Supplied Product Categories: `Electronics`, Is Approved Alternative Supplier: `Checked`)
        *   `Global Packaging Co.` (Supplied Product Categories: `Packaging`, Is Approved Alternative Supplier: `Unchecked`)
        *   `Backup Box Supplies` (Supplied Product Categories: `Packaging`, Is Approved Alternative Supplier: `Checked`)

### Phase 2: Build Helper Flows

1.  **Navigate to Flows:** Setup -> Quick Find: `Flows` -> Flows.
2.  **Create Flow 1: Log Disruption Case**
    *   Click New Flow -> Autolaunched Flow (No Trigger) -> Create.
    *   **Create Variables (Input):**
        *   `input_DisruptedSupplierName` (Text, Available for input)
        *   `input_DisruptionReason` (Text, Available for input)
        *   `input_AffectedCategories` (Text, Available for input)
    *   **Add Element: Create Records** (`Create Disruption Case`)
        *   Object: `Case`
        *   Set Fields: Use separate resources...
        *   Field Mappings:
            *   `RecordTypeId`: *Paste the 15/18 digit ID of instructions you provided for building SCRA Lite. This README explains what the project is and how to replicate it using those exact steps.

```markdown
# SCRA Lite - Supply Chain Resilience Agent Lite (Salesforce Agentforce Project)

## Description

SCRA Lite is an internal-facing AI agent built entirely on the Salesforce Platform using Agent Builder (Einstein Bots) and Salesforce Flow. It's designed for Supply Chain Managers to quickly react to supplier disruptions by:

1.  **Logging Disruption Details:** Allowing managers to report which supplier is disrupted, the reason, and affected product categories via chat. The agent automatically creates a structured 'Supply Chain Disruption' Case record in Salesforce.
2.  **Finding Alternative Suppliers:** Enabling managers to ask for alternative suppliers for a specific product category. The agent queries Account records flagged as approved alternatives and returns a list of names.

This agent streamlines the initial response process during critical supply chain events, leveraging data already within Salesforce.

## Features

*   Conversational interface via Salesforce Chat.
*   Intent recognition for "Report Disruption" and "Find Alternatives".
*   Extraction of key details (Supplier Name, Product Category, Reason) using Entities.
*   Automated creation of 'Supply Chain Disruption' Case records via Flow.
*   Automated querying of Account records to find approved alternative suppliers based on product category via Flow.
*   Declarative build - No custom Apex code required.

## Technologies Used

*   **Platform:** Salesforce Platform (Lightning Experience)
*   **Core Tools:**
    *   Agent Builder (Einstein Bots)
    *   Salesforce Flow (Autolaunched Flows)
    *   Salesforce Objects (Standard: Account, Case; Custom Fields, Record Type)
    *   Lightning App Builder
    *   Embedded Service Chat
*   **Database:** Native Salesforce Database

## Setup Instructions

Follow these steps precisely to build and configure SCRA Lite in your Salesforce org.

### Phase 1: Salesforce Configuration (Objects, Fields, Record Type, Data)

**Goal:** Prepare the necessary Salesforce data structure.

1.  **Navigate to Setup -> Object Manager.**
2.  **Customize Account Object:**
    *   Go to `Account` -> `Fields & Relationships`.
    *   Create New Field:
        *   Type: `Checkbox`, Label: `Is Approved Alternative Supplier`, Default: `Unchecked`. Save.
    *   Create New Field:
        *   Type: `Text Area`, Label: `Supplied Product Categories`. Save.
3.  **Customize Case Object:**
    *   Go to `Case` -> `Record Types`.
    *   Create New Record Type:
        *   Label: `Supply Chain Disruption`, Name: `Supply_Chain_Disruption`, Active: `Checked`. Enable for relevant profiles. Assign a Page Layout (e.g., `Case Layout`). Save.
    *   Go to `Case` -> `Fields & Relationships`.
    *   Create New Field:
        *   Type: `Text Area`, Label: `Disruption Reason`. Save.
    *   Create New Field:
        *   Type: `Text Area`, Label: `Affected Product Categories`. Save.
    *   Create New Field:
        *   Type: `Lookup Relationship`, Related To: `Account`, Label: `Disrupted Supplier`. Save.
    *   Modify `Status` field picklist values: Add `Mitigation Required`.
    *   Modify `Case Origin` field picklist values: Add `Agent Reported`.
    *   Go to `Case` -> `Page Layouts`. Edit the layout assigned to the `Supply Chain Disruption` Record Type.
    *   Drag the `Disrupted Supplier`, `Disruption Reason`, and `Affected Product Categories` fields onto the layout. Save.
4.  **Populate Sample Data:**
    *   Go to the `Accounts` tab. Create sample Account records representing suppliers:
        *   `Primary Components Inc.` (Supplied Product Categories: `Electronics`, Is Approved Alternative Supplier: `Unchecked`)
        *   `Alt Electronics Ltd.` (Supplied Product Categories: `Electronics`, Is Approved Alternative Supplier: `Checked`)
        *   `Global Packaging Co.` (Supplied Product Categories: `Packaging`, Is Approved Alternative Supplier: `Unchecked`)
        *   `Backup Box Supplies` (Supplied Product Categories: `Packaging`, Is Approved Alternative Supplier: `Checked`)

### Phase 2: Flow Creation

**Goal:** Create the backend automation triggered by the agent.

1.  **Navigate to Setup -> Flows.**
2.  **Create Flow 1: Log Disruption Case**
    *   Type: `Autolaunched Flow (No Trigger)`.
    *   Create Input Variables: `input_DisruptedSupplierName` (Text), `input_DisruptionReason` (Text), `input_AffectedCategories` (Text). Ensure 'Available for input' is checked.
    *   Add `Create Records` Element:
        *   Object: `Case`.
        *   Set Fields: Use separate resources/literals.
        *   `RecordTypeId`: *Paste the 15/18 digit ID of the 'Supply Chain Disruption' record type here.*
        *   `Subject`: Formula ` "Disruption Reported for: " & {!input_DisruptedSupplierName} `
        *   `Description`: Formula ` "Reason: " & {!input_DisruptionReason} & "\nAffected Categories: " & {!input_AffectedCategories} `
        *   `Status`: `Mitigation Required`
        *   `Origin`: `Agent Reported`
        *   `Priority`: `High`
    *   Save Flow: Label/API Name: `SCRA_LogDisruptionCase`. Activate.
3.  **Create Flow 2: Find Alternative Suppliers**
    *   Type: `Autolaunched Flow (No Trigger)`.
    *   Create Input Variable: `input_AffectedCategories` (Text, Available for input).
    *   Create Output Variable: `output_AlternativeSupplierNames` (Text, Available for output, Default: `{!$GlobalConstant.EmptyString}`).
    *   Add `Get Records` Element:
        *   Object: `Account`.
        *   Conditions: `Is_Approved_Alternative_Supplier__c` Equals `{!$GlobalConstant.True}` **AND** `Supplied_Product_Categories__c` Contains `{!input_AffectedCategories}`.
        *   Store: All records, Automatically store all fields.
    *   Add `Loop` Element:
        *   Collection: Record Collection from `Get Records`.
    *   Inside Loop, Add `Assignment` Element:
        *   Variable: `{!output_AlternativeSupplierNames}` -> Operator: `Add` -> Value: `{!Loop_Through_Alternatives.Name}` (Current Item from Loop -> Name).
        *   Variable: `{!output_AlternativeSupplierNames}` -> Operator: `Add` -> Value: `; ` (Semicolon and space literal).
    *   Connect Assignment back to Loop.
    *   Save Flow: Label/API Name: `SCRA_FindAlternativeSuppliers`. Activate.

### Phase 3: Agent Builder Configuration

**Goal:** Build the conversational agent interface.

1.  **Navigate to Setup -> Einstein Bots / Agent Builder.**
2.  Create **New Agent**: Name `SCRA Lite`, Start from Scratch. Complete the wizard.
3.  **Configure Intents:**
    *   Create Intent `Report Disruption` with sample utterances (e.g., "Report a supplier disruption", "Supplier X is down").
    *   Create Intent `Find Alternatives` with sample utterances (e.g., "Find alternative suppliers", "Who else can supply Electronics?").
4.  **Configure Entities:**
    *   Create Entity `SupplierName` (Type: Text).
    *   Create Entity `ProductCategory` (Type: Text).
    *   Create Entity `Reason` (Type: Text).
5.  **Configure Dialogs:**
    *   **Welcome Dialog:** Edit Message block (e.g., "Welcome! I am the Supply Chain Resilience Agent Lite...").
    *   **Main Menu Dialog:** Edit Question block:
        *   Message: "How can I help...?"
        *   *If using Menu Items:* Add choices "Report a Disruption" (Action: Start Dialog `Report Disruption`) and "Find Alternative Suppliers" (Action: Start Dialog `Find Alternatives`). *If using Intent-based navigation:* Message instructs user what to type, set to "Wait for customer input".
    *   **Create Dialog:** `Report Disruption`. Link to `Report Disruption` Intent.
        *   Add `Question`: "Okay, which supplier...?" -> Extract `SupplierName` -> Save to `Var_SupplierName` (New Text Var).
        *   Add `Question`: "What product categories...?" -> Extract `ProductCategory` -> Save to `Var_ProductCategory` (New Text Var).
        *   Add `Question`: "Briefly, what is the reason...?" -> Extract `Reason` -> Save to `Var_Reason` (New Text Var).
        *   Add `Action`: Type `Flow`, Name `SCRA_LogDisruptionCase`. Map Inputs: `input_DisruptedSupplierName` = `{!Var_SupplierName}`, `input_DisruptionReason` = `{!Var_Reason}`, `input_AffectedCategories` = `{!Var_ProductCategory}`.
        *   Add `Message`: Confirmation message using variables (e.g., Case Creation:**
    *   Initiate conversation.
    *   Indicate intent to "Report a Disruption".
    *   Provide sample data for Supplier Name (`Primary Components Inc.`), Category (`Electronics`), Reason (`Factory fire`).
    *   **Expected Result:** Agent confirms logging. Verify a `Case` record is created in Salesforce with Type "Supply Chain Disruption", Status "Mitigation Required", Origin "Agent Reported", and correct Subject/Description.

2.  **Test Alternative Finding (Success):**
    *   Initiate conversation.
    *   Indicate intent to "Find Alternative Suppliers".
    *   Provide Category: `Electronics`.
    *   **Expected Result:** Agent responds with `Alt Electronics Ltd.; `.

3.  **Test Alternative Finding (Success - Different Category):**
    *   Indicate intent to "Find Alternative Suppliers".
    *   Provide Category: `Packaging`.
    *   **Expected Result:** Agent responds with `Backup Box Supplies; `.

4.  **Test Alternative Finding (No Results):**
    *   Indicate intent to "Find Alternative Suppliers".
    *   Provide Category: `Gadgets` (or any category not in sample data).
    *   **Expected Result:** Agent responds with a message indicating no alternatives were found for that category.
