# OMMA Platform Data Flow Diagrams (DFD)

This document contains the Data Flow Diagrams (DFD) for the OMMA Platform based on the provided architecture blueprint. The visual style follows the supplied reference layouts using Mermaid.

## Level 0 Context Diagram
The Level 0 diagram provides a high-level overview of the entire OMMA system and its interactions with external entities.

```mermaid
%%{init: { 'theme': 'base', 'themeVariables': { 'primaryColor': '#ffffff', 'primaryBorderColor': '#000000', 'primaryTextColor': '#000000', 'lineColor': '#000000', 'background': '#ffffff' } }}%%
graph LR
    Customer[Customer]
    Admin[Admin]
    Artist[Artist]
    
    P0["<b>0</b><hr/>OMMA Art Commission Platform"]
    
    Customer -- "Sends Commission Request<br/>Submits Payment" --> P0
    P0 -- "Views Art Discovery Feed<br/>Receives Order Status" --> Customer
    
    Artist -- "Uploads Portfolio<br/>Delivers Artwork" --> P0
    P0 -- "Sends Commission Requests<br/>Issues Payouts" --> Artist
    
    Admin -- "Updates System Settings<br/>Moderates Content" --> P0
    P0 -- "Reports & Analytics<br/>Flagged Content" --> Admin
```

---

## Level 1 Data Flow Diagram
The Level 1 diagram decomposes the system into main functional processes and generalized data stores/services. 

```mermaid
%%{init: { 'theme': 'base', 'themeVariables': { 'primaryColor': '#ffffff', 'primaryBorderColor': '#000000', 'primaryTextColor': '#000000', 'lineColor': '#000000', 'background': '#ffffff' } }}%%
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
    P1["<b>1.0</b><hr/>Manage User Profiles"]
    P2["<b>2.0</b><hr/>Discover Artworks"]
    P3["<b>3.0</b><hr/>Manage Commission Lifecycle"]
    P4["<b>4.0</b><hr/>Handle Escrow Payments"]
    P5["<b>5.0</b><hr/>Authenticate Art"]
    P6["<b>6.0</b><hr/>Manage Artist Forum"]
    
    %% Flows
    Customer -- "Fills-out Info" --> P1
    Artist -- "Fills-out Info" --> P1
    P1 -- "Saves Profile Data" --> Database
    
    Customer -- "Browses & Filters" --> P2
    P2 -- "Retrieves Data" --> Database
    P2 -- "Fetches Assets" --> Storage
    
    Customer -- "Submits Request<br/>Messages & Revisions" --> P3
    Artist -- "Accepts Request<br/>Messages & Delivery" --> P3
    P3 -- "Updates Order State" --> Database
    P3 -- "Uploads Media" --> Storage
    
    Customer -- "Checks Out" --> P4
    P4 -- "Processes Escrow" --> Payment
    Payment -- "Payment Confirmed" --> P4
    P4 -- "Notifies Payment State" --> P3
    
    P3 -- "Final Upload" --> P5
    P5 -- "Sends File Scan" --> AI_Auth
    AI_Auth -- "Returns Fingerprint" --> P5
    P5 -- "Records Auth Badge" --> Database
    
    Artist -- "Posts Content" --> P6
    P6 -- "Stores Threads" --> Database
    
    Admin -- "Moderates / Approves" --> P1
    Admin -- "Resolves Disputes" --> P3
```

---

## Level 2 Data Flow Diagram (Process 3.0: Commission Lifecycle)
The Level 2 diagram provides a detailed zoom-in of the `3.0 Manage Commission Lifecycle` process, breaking down the steps from initial request to final payout.

```mermaid
%%{init: { 'theme': 'base', 'themeVariables': { 'primaryColor': '#ffffff', 'primaryBorderColor': '#000000', 'primaryTextColor': '#000000', 'lineColor': '#000000', 'background': '#ffffff' } }}%%
graph TD
    %% External Entities
    Customer[Customer]
    Artist[Artist]
    
    %% Stores & Services
    Database[(Commission & Message DB)]
    Payment[Payment System]
    P5["<b>5.0</b><hr/>Authenticate Art"]
    
    %% Sub-Processes
    P31["<b>3.1</b><hr/>Submit Brief"]
    P32["<b>3.2</b><hr/>Negotiate Terms"]
    P33["<b>3.3</b><hr/>Hold Escrow"]
    P34["<b>3.4</b><hr/>WIP & Revision"]
    P35["<b>3.5</b><hr/>Deliver Final"]
    P36["<b>3.6</b><hr/>Release Payout"]
    
    %% Flows
    Customer -- "Provides Brief & Deadline" --> P31
    P31 -- "Saves PENDING Request" --> Database
    
    Database -- "Loads Pending" --> P32
    Artist -- "Accepts/Counters" --> P32
    P32 -- "Updates to AWAITING_DEPOSIT" --> Database
    
    Customer -- "Payment Details" --> P33
    P33 -- "Requests Escrow" --> Payment
    Payment -- "Escrow OK" --> P33
    P33 -- "Updates to IN_PROGRESS" --> Database
    
    Artist -- "Uploads WIP" --> P34
    Customer -- "Approves / Revises" --> P34
    P34 -- "Saves Messages & Revisions" --> Database
    
    Artist -- "Uploads Final Delivery" --> P35
    P35 -- "Requests Verification" --> P5
    P5 -- "Verification Passed" --> P35
    Customer -- "Approves Delivery" --> P35
    P35 -- "Marks DELIVERED" --> Database
    
    P35 -- "Signals Completion" --> P36
    P36 -- "Requests Payout" --> Payment
    Payment -- "Payout Confirmed" --> P36
    P36 -- "Updates to COMPLETED" --> Database
    P36 -- "Sends Funds" --> Artist
```
