# OMMA Platform Data Flow Diagrams (DFD)

This document contains the Data Flow Diagrams (DFD) for the OMMA Platform based on the provided architecture blueprint. The visual style follows the supplied reference layouts using Mermaid.

## Level 0 Context Diagram
The Level 0 diagram provides a high-level overview of the entire OMMA system and its interactions with external entities.

```mermaid
%%{init: { 'flowchart': { 'curve': 'step', 'nodeSpacing': 150, 'rankSpacing': 150 }, 'theme': 'base', 'themeVariables': { 'primaryColor': '#ffffff', 'primaryBorderColor': '#000000', 'primaryTextColor': '#000000', 'lineColor': '#000000', 'background': '#ffffff', 'edgeLabelBackground': 'rgba(255, 255, 255, 0)' } }}%%
graph LR
    Customer[Customer]
    Admin[Admin]
    Artist[Artist]
    
    P0["0<br/>OMMA Art Commission<br/>Platform"]
    
    Customer -- "Sends Commission Request<br/>Submits Payment" --> P0
    P0 -- "Views Art Discovery Feed<br/>Receives Order Status" --> Customer
    
    Artist -- "Uploads Portfolio<br/>Delivers Artwork" --> P0
    P0 -- "Sends Commission Requests<br/>Issues Payouts" --> Artist
    
    Admin -- "Updates System Settings<br/>Moderates Content" --> P0
    P0 -- "Reports and Analytics<br/>Flagged Content" --> Admin
```

---

## Level 1 Data Flow Diagram
The Level 1 diagram decomposes the system into main functional processes and generalized data stores/services. 

```mermaid
%%{init: { 'flowchart': { 'curve': 'step', 'nodeSpacing': 150, 'rankSpacing': 150 }, 'theme': 'base', 'themeVariables': { 'primaryColor': '#ffffff', 'primaryBorderColor': '#000000', 'primaryTextColor': '#000000', 'lineColor': '#000000', 'background': '#ffffff', 'edgeLabelBackground': 'rgba(255, 255, 255, 0)' } }}%%
graph TD
    %% External Entities
    Customer[Customer]
    Artist[Artist]
    Admin[Admin]
    
    %% Generalized Providers
    Payment[Payment System]
    AI_Auth[AI Detection Service]
    Storage[Media Storage]
    
    %% Database Store
    Database[(Platform Database)]
    
    %% Processes
    P1["1.0<br/>Manage User Profiles"]
    P2["2.0<br/>Discover Artworks"]
    P3["3.0<br/>Manage Commission Lifecycle"]
    P4["4.0<br/>Process Secure Payments"]
    P5["5.0<br/>Authenticate Art"]
    P6["6.0<br/>Manage Artist Forum"]
    
    %% Flows
    Customer -- "Fills-out Info" --> P1
    Artist -- "Fills-out Info" --> P1
    Admin -- "Moderates / Approves" --> P1
    P1 -- "Saves Profile Data" --> Database
    
    Customer -- "Browses and Filters" --> P2
    P2 -- "Retrieves Data" --> Database
    P2 -- "Fetches Assets" --> Storage
    
    Customer -- "Submits Request<br/>Messages and Revisions" --> P3
    Artist -- "Accepts Request<br/>Messages and Delivery" --> P3
    Admin -- "Resolves Disputes" --> P3
    P3 -- "Updates Order State" --> Database
    P3 -- "Uploads Media" --> Storage
    
    Customer -- "Checks Out" --> P4
    P4 -- "Notifies Payment State" --> P3
    P4 <--> |"Processes Payment<br/>Payment Confirmed"| Payment
    
    P3 -- "Final Upload" --> P5
    P5 <--> |"Sends File Scan<br/>Returns Fingerprint"| AI_Auth
    P5 -- "Records Auth Badge" --> Database
    
    Artist -- "Posts Content" --> P6
    P6 -- "Stores Threads" --> Database
```

---

## Level 2 Data Flow Diagram (Process 3.0: Commission Lifecycle)
The Level 2 diagram provides a detailed zoom-in of the `3.0 Manage Commission Lifecycle` process, breaking down the steps from initial request to final payout.

```mermaid
%%{init: { 'flowchart': { 'curve': 'step', 'nodeSpacing': 150, 'rankSpacing': 150 }, 'theme': 'base', 'themeVariables': { 'primaryColor': '#ffffff', 'primaryBorderColor': '#000000', 'primaryTextColor': '#000000', 'lineColor': '#000000', 'background': '#ffffff', 'edgeLabelBackground': 'rgba(255, 255, 255, 0)' } }}%%
graph TD
    %% External Entities
    Customer[Customer]
    Artist[Artist]
    
    %% Stores & Services
    Database[(Commission and Message DB)]
    Payment[Payment System]
    P5["5.0<br/>Authenticate Art"]
    
    %% Sub-Processes
    P31["3.1<br/>Submit Brief"]
    P32["3.2<br/>Negotiate Terms"]
    P33["3.3<br/>Secure Payment Funds"]
    P34["3.4<br/>WIP and Revision"]
    P35["3.5<br/>Deliver Final"]
    P36["3.6<br/>Release Payout"]
    
    %% Flows
    Customer -- "Provides Brief and Deadline" --> P31
    P31 -- "Saves PENDING Request" --> Database
    
    Database -- "Loads Pending" --> P32
    Artist -- "Accepts/Counters" --> P32
    P32 -- "Updates to AWAITING_DEPOSIT" --> Database
    
    Customer -- "Payment Details" --> P33
    P33 <--> |"Requests Payment Hold<br/>Hold Confirmed"| Payment
    P33 -- "Updates to IN_PROGRESS" --> Database
    
    Artist -- "Uploads WIP" --> P34
    Customer -- "Approves / Revises" --> P34
    P34 -- "Saves Messages and Revisions" --> Database
    
    Artist -- "Uploads Final Delivery" --> P35
    P35 <--> |"Requests Verification<br/>Verification Passed"| P5
    Customer -- "Approves Delivery" --> P35
    P35 -- "Marks DELIVERED" --> Database
    
    P35 -- "Signals Completion" --> P36
    P36 <--> |"Requests Payout<br/>Payout Confirmed"| Payment
    P36 -- "Updates to COMPLETED" --> Database
    P36 -- "Sends Funds" --> Artist
```
