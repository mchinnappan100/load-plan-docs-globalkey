# Preparing Load Plan JSON using External Id field (Global_key__c) and using Salesforce Migration Tool (SMT)

> In this document we explain how to write Load Plan JSON using External Id filed ```Global_key__c```  for using data migration from one org to another org using Salesforce Migration Tool (SMT)

---

## Table of Contents

- [Overview](#overview)
- [UseCase](#usecase)
- [Setup](#setup)
- [Using SMT](#using-smt)
- [Using Compare tool](#compare-tool)



## Overview

**Salesforce Migraion Tool**
([SMT](https://mchinnappan100.github.io/pages2/smt/smt.html)) is a modern desktop application designed to help data migration between Salesforce organizations. It supports complex data migrations, including single and two-step processes, using JSON load plans to define objects, queries, and field mappings. The tool leverages the Salesforce CLI and Bulk API 2.0 for secure and efficient data operations.

It uses Load Plan (JSON) file for Data Migrtion between Salesforce organizations. Load Plan defines the objects, queries, and field mappings for migration. Below is an example structure with enhanced formatting:

```json
[
  {
    "object": "Manufacturer__c",
    "compositeKeys": [
      "Global_Key__c"
    ],
    "compositeKeys_v1": [
      "Name"
    ],
    "query": "SELECT Global_Key__c, Name, Country__c FROM Manufacturer__c",
    "query_v1": "SELECT Name, Country__c FROM Manufacturer__c",
    "fieldMappings": {
      "Global_Key__c": "Global_Key__c",
      "Name": "Name",
      "Country__c": "Country__c"
    }
  },
  {
    "object": "VehicleCustomer__c",
    "compositeKeys": [
      "Global_Key__c"
    ],
    "compositeKeys_v1": [
      "Name"
    ],
    "query": "SELECT Global_Key__c, Name, Email__c FROM VehicleCustomer__c",
    "query_v1": "SELECT Name, Email__c FROM VehicleCustomer__c",
    "fieldMappings": {
      "Global_Key__c": "Global_Key__c",
      "Name": "Name",
      "Email__c": "Email__c"
    }
  },
  {
    "object": "Vehicle__c",
    "compositeKeys": [
      "Global_Key__c"
    ],
    "compositeKeys_v1": [
      "Name"
    ],
    "query": "SELECT Global_Key__c, Name, Model__c, Manufacturer__r.Global_Key__c, VehicleCustomer__r.Global_Key__c FROM Vehicle__c",
    "query_v1": "SELECT Name, Model__c, Manufacturer__r.Name, VehicleCustomer__r.Name FROM Vehicle__c",
    "fieldMappings": {
      "Global_Key__c": "Global_Key__c",
      "Name": "Name",
      "Model__c": "Model__c",
      "Manufacturer__c": {
        "lookup": {
          "object": "Manufacturer__c",
          "key": "Global_Key__c",
          "field": "Manufacturer__r.Global_Key__c"
        }
      },
      "VehicleCustomer__c": {
        "lookup": {
          "object": "VehicleCustomer__c",
          "key": "Global_Key__c",
          "field": "VehicleCustomer__r.Global_Key__c"
        }
      }
    }
  }
]
```
----

In the document we will explain how to build the load plan json for the given use case

## Usecase

Here is the ERD for use case and csv data for the objects involved:



### 🧱 Entity Relationship Diagram

```mermaid
erDiagram

    Manufacturer__c {
        string Id
        string Name
        string Global_Key__c
        string Country__c
    }

    VehicleCustomer__c {
        string Id
        string Name
        string Global_Key__c
        string Email__c
    }

    Vehicle__c {
        string Id
        string Name
        string Global_Key__c
        string Model__c
        lookup Manufacturer__c
        lookup Customer__c
    }

    Manufacturer__c ||--o{ Vehicle__c : "manufactures"
    VehicleCustomer__c ||--o{ Vehicle__c : "owns"
```

#### Manufacturer__c 
![Manufacturer__c ERD](img/manufacturer-erd-1.png)


```csv
Global_Key__c,Name,Country__c
MFG-001,Tesla,USA
MFG-002,Toyota,Japan
MFG-003,Ford,USA
```

| Name   | Global_Key__c | Country |
| ------ | ------------- | ------- |
| Tesla  | MFG-001       | USA     |
| Toyota | MFG-002       | Japan   |


#### VehicleCustomer__c 
![VehicleCustomer__c ERD](img/VehicleCustomer__c-erd-1.png)

```csv
Global_Key__c,Name,Email__c
CUST-001,John Doe,john@test.com
CUST-002,Jane Smith,jane@test.com
CUST-003,Bob Lee,bob@test.com
```

| Name       | Global_Key__c | Email                                 |
| ---------- | ------------- | ------------------------------------- |
| John Doe   | CUST-001      | [john@test.com](mailto:john@test.com) |
| Jane Smith | CUST-002      | [jane@test.com](mailto:jane@test.com) |
| Bob Lee    | CUST-003      |[jbobe@test.com](mailto:bob@test.com)  |



#### Vehicle__c
![Vehicle__c ERD](img/vehicle-erd-1.png)

```csv

Global_Key__c,Name,Model__c,Manufacturer__r.Global_Key__c,VehicleCustomer__r.Global_Key__c
VEH-001,Tesla Model S,Model S,MFG-001,CUST-001
VEH-002,Tesla Model X,Model X,MFG-001,CUST-002
VEH-003,Toyota Camry,Camry,MFG-002,CUST-003
VEH-004,Ford Explorer,Explorer,MFG-003,CUST-001

```

----

## Setup

- You need 2 orgs: af102 and af200 in our case

- Make sure both the orgs source-org: af102 and  target-org: af200 have this data model created

- Make sure to populate these 3 objects in source-org af102


## Using SMT

### Make Connection to these 2 orgs

![SMT Connection](img/smt-connection.png)

### Load the Load Plan for this use case

![SMT Load plan](img/smt-load-plan-1.png)


### Data  Migration

![SMT Data Migration-1](img/smt-migration-1.png)

#### Note
- First run **Generate CSV Only** to check the csv creation, for Example: for the object Vehicle__c

![Vehicle CSV Gen](img/vehicle-csv-1.png) 

- Load the objects in the order mentioned in the laod-plan json

- First time SMT will create Inserts for the object

- You can click **Start Migration** to finish the inserts


![Vehicle Inserted](img/vehicle-inserted.png)

![Manufacturer-Inserted](img/manufacturer-inserted.png)




#### Updates

![Data Update for VehicleCusotmer](img/vehcileCustomer-update-1.png)

- After update run SMT for the object VehicleCustomer__c
- SMT will update the record(s)


- Clicking **Generate CSV Only**  will provide a CSV :

![generate csv vehicleCsutomer__c](img/smt-vehicleCustomer-update-csv-1.png)

![SMT update-1](img/smt-update-1.png)

- You can click **Start Migration** to finish this update


![VenicleCustomer-Update](img/vehicleCustomer-updated.png)

![Vehicle Final update](img/smt-vehicle-latest.png)



## Compare Tool

- Tool used [sf-data-util](https://www.npmjs.com/package/sf-data-util)

```bash
sf-data-util compare -s af102 -t af200 -l load-plan-vehicle.json -o Vehicle__c

```


### Vehicle__c 
![vehicle-compare-1](img/vehicle-compare-1.png)
---
![vehicle-compare-2](img/vehicle-compare-2.png)
---

### VehicleCustomer__c

```bash
sf-data-util compare -s af102 -t af200 -l load-plan-vehicle.json -o VehicleCustomer__c
```

![vehicleCustomer](img/vehicleCustomer-compare-1.png)

### Manufacturer__c

```bash
sf-data-util compare -s af102 -t af200 -l load-plan-vehicle.json -o Manufacturer__c   
```

![vehicleCustomer-compare-1](img/manufacturer-compare-1.png)





 