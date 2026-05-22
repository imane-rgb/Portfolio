---
title: "ORION-7: Distributed Space Logistics Database"
date: 2025-12-15
description: "Enterprise-grade distributed relational database for interplanetary logistics, with RBAC, T-SQL automation, and Raspberry Pi cluster deployment."
tags: ["SQL Server", "T-SQL", "Distributed Systems", "RBAC", "Linux", "Raspberry Pi", "Database"]
icon: "🛰️"
filetype: "sql"
github: "https://github.com/imane-ibnelhabib/orion-7"
hw_specs:
  - { key: "Database",     value: "SQL Server" }
  - { key: "Language",     value: "T-SQL" }
  - { key: "Normalization",value: "1NF → 3NF" }
  - { key: "Access Control",value: "RBAC (Role-Based)" }
  - { key: "Cluster Nodes",value: "Raspberry Pi (Linux)" }
  - { key: "Architecture", value: "Distributed HA Cluster" }
  - { key: "Automation",   value: "Stored Procedures & Triggers" }
  - { key: "Domain",       value: "Interplanetary Logistics" }
---

## Overview

**ORION-7** is a highly secure, enterprise-grade relational database architecture
designed to handle complex interplanetary logistics and data structures across
distributed environments.

The system maintains strict transactional data integrity while orchestrating automated
administrative tasks via optimized T-SQL stored procedures and triggers, deployed as
a highly available distributed cluster across Linux-operated Raspberry Pi nodes.

## Architecture

```
┌─────────────────────────────────────────┐
│           ORION-7 Cluster               │
│                                         │
│  [Pi Node 1 — Primary]                  │
│  [Pi Node 2 — Replica]                  │
│  [Pi Node 3 — Replica]                  │
│         ↕ Replication                   │
│  [SQL Server on Linux / Docker]         │
└─────────────────────────────────────────┘
         ↕ RBAC Layer
  [Mission Control] [Logistics] [Audit]
```

## Key Features

- **Rigorous Normalization** — Schema designed from 1NF through 3NF, eliminating redundancy and ensuring referential integrity across all logistics entities
- **T-SQL Automation** — Stored procedures handle cargo manifests, route assignments, and resource allocation; triggers enforce business rules and audit trails automatically
- **RBAC Framework** — Granular role-based access control with distinct permission sets for mission controllers, logistics operators, and auditors
- **Distributed Cluster** — Physical database deployed across Raspberry Pi nodes running Linux, providing high availability and fault tolerance
- **Transactional Integrity** — Full ACID compliance across all interplanetary shipment and resource operations

## Schema Design

{{< codeblock lang="sql" filename="schema_core.sql" >}}
-- Cargo manifest table (3NF normalized)
CREATE TABLE CargoManifest (
    ManifestID      INT           PRIMARY KEY IDENTITY,
    MissionID       INT           NOT NULL REFERENCES Mission(MissionID),
    CargoTypeID     INT           NOT NULL REFERENCES CargoType(CargoTypeID),
    OriginNodeID    INT           NOT NULL REFERENCES LogisticsNode(NodeID),
    DestNodeID      INT           NOT NULL REFERENCES LogisticsNode(NodeID),
    MassKg          DECIMAL(10,3) NOT NULL CHECK (MassKg > 0),
    DepartureUTC    DATETIME2     NOT NULL,
    ArrivalUTC      DATETIME2,
    StatusID        TINYINT       NOT NULL DEFAULT 1,
    CreatedAt       DATETIME2     NOT NULL DEFAULT SYSUTCDATETIME()
);

-- Trigger: auto-audit on status change
CREATE TRIGGER trg_ManifestAudit
ON CargoManifest AFTER UPDATE AS
BEGIN
    INSERT INTO AuditLog (TableName, RecordID, ChangedAt, ChangedBy)
    SELECT 'CargoManifest', i.ManifestID, SYSUTCDATETIME(), SYSTEM_USER
    FROM inserted i;
END;
{{< /codeblock >}}

## RBAC Implementation

{{< codeblock lang="sql" filename="rbac_setup.sql" >}}
-- Define roles
CREATE ROLE MissionController;
CREATE ROLE LogisticsOperator;
CREATE ROLE AuditViewer;

-- Grant granular permissions
GRANT SELECT, INSERT, UPDATE ON CargoManifest TO LogisticsOperator;
GRANT SELECT                  ON CargoManifest TO AuditViewer;
GRANT EXECUTE                 ON SCHEMA::dbo   TO MissionController;

-- Deny direct table access to controllers (stored procs only)
DENY INSERT, UPDATE, DELETE ON CargoManifest TO MissionController;
{{< /codeblock >}}

## Distributed Deployment

The cluster runs SQL Server on Linux across three Raspberry Pi nodes, configured
with synchronous replication for zero data loss on primary failover. Each node
operates independently and can serve read queries, with the primary handling
all write transactions.
