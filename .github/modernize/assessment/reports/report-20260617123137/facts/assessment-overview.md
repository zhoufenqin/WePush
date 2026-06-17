# Assessment Overview

This document provides a navigation entry point for all supplementary analysis documents generated as part of the WePush application assessment. Each document covers a specific architectural or technical dimension of the codebase.

## Supplementary Documents

| Document | Description |
|---|---|
| [Architecture Diagram](architecture-diagram.md) | Two-layer architecture visualization: application-level technology stack diagram and component relationship diagram for WePush |
| [Dependency Map](dependency-map.md) | Visual map of all external Maven dependencies grouped by functional category (messaging SDKs, cloud provider SDKs, database/ORM, scheduling, GUI, logging, utilities) with version and compatibility risk analysis |
| [API & Service Communication Contracts](api-service-contracts.md) | Outbound API contracts consumed by WePush across all 14 messaging channels, communication patterns, security posture, and end-to-end sequence diagram |
| [Data Architecture & Persistence Layer](data-architecture.md) | Entity model ER diagram, MyBatis mapper inventory, SQLite/MySQL database configuration, caching strategy, and data classification & sensitivity analysis |
| [Configuration & Externalized Settings Inventory](configuration-inventory.md) | Complete inventory of all configuration sources, properties (with defaults), secrets handling, startup parameters, and framework/runtime versions |
| [Core Business Workflows](business-workflows.md) | End-to-end documentation of the five primary business workflows (batch push, scheduled push, recipient import, message preview, infinity task), domain entity descriptions, and business rules & decision logic |
