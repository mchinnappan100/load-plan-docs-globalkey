```

┌─────────────────────────────────────────────────────────────────────────────┐
│                     SALESFORCE DATA MIGRATION FLOW                         │
└─────────────────────────────────────────────────────────────────────────────┘


   ┌──────────────────────────┐
   │ 1️⃣  Schema Discovery     │
   │                          │
   │ sf-schema-builder        │
   │                          │
   │ • Build source schema    │
   │ • Analyze relationships  │
   │ • Understand dependencies│
   └────────────┬─────────────┘
                │
                ▼
   ┌──────────────────────────┐
   │ 2️⃣  Load Sequencing      │
   │                          │
   │ sf-load-sequencer-ui     │
   │                          │
   │ • Determine object order │
   │ • Resolve dependencies   │
   │ • Generate load sequence │
   └────────────┬─────────────┘
                │
                ▼
   ┌──────────────────────────┐
   │ 3️⃣  Load Plan Creation   │
   │                          │
   │ Salesforce Load Plan     │
   │ Generator                │
   │                          │
   │ • Generate per-object    │
   │   load plans             │
   │ • Create JSON configs    │
   │ • Combine plans into     │
   │   ordered array          │
   └────────────┬─────────────┘
                │
                ▼
   ┌──────────────────────────┐
   │ 4️⃣  Data Population      │
   │                          │
   │ SF Utils                 │
   │                          │
   │ • Upload CSV files       │
   │ • Use Bulk API 2.0       │
   │ • High-volume loading    │
   │ • Faster processing      │
   └────────────┬─────────────┘
                │
                ▼
   ┌──────────────────────────┐
   │ 5️⃣  Org-to-Org Migration │
   │                          │
   │ Salesforce Migration     │
   │ Tool (SMT) Studio        │
   │                          │
   │ • Migrate between orgs   │
   │ • Validate data          │
   │ • Complete deployment    │
   └──────────────────────────┘


┌─────────────────────────────────────────────────────────────────────────────┐
│                           END-TO-END PROCESS                               │
│                                                                             │
│  Schema → Sequence → Load Plan → CSV Upload → Org Migration               │
└─────────────────────────────────────────────────────────────────────────────┘
```
