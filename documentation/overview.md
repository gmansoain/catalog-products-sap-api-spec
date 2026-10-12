## Overview

Catalog Products SAP System API provides raw CRUD access to the SAP product master for internal Mule Process APIs.
It is part of the `catalog` domain and connects to the SAP ERP product master, mirrored in the `ecommerce_sap.products` MySQL table on the demo EC2 host.

**Owner:** Catalog Platform Team
**Contact:** #catalog-platform / catalog-platform@leno.com
**SLA Tier:** Silver

## What this API does

- Exposes canonical `Product` records with camelCase mirrors of the raw SAP columns (`productId`, `productDescription`, `productCategory`, `imagePath`, `unitPrice`, `currency`, `brand`, `longDescription`, `weightKg`, `warrantyMonths`, `isActive`).
- Supports create, read (list + by ID), full replace, partial update, and soft-delete via the standard REST verbs.
- Publishes `/alive` and `/ready` health-check endpoints for functional monitoring.

## Who uses this API

| Consumer | Use case |
|----------|----------|
| Products Process API | Aggregates SAP product records with inventory and pricing before exposing them to Experience APIs. |
| Data Ingestion Batch | Nightly reconciliation between SAP and the storefront read-model. |

## Related APIs

- [[catalog-products-process-api]] — canonical product API consumed by Experience layers; sits on top of this System API.
- [[inventory-stock-sap-api]] — sibling System API for stock levels; aggregated alongside this API at the Process layer.

## What this API is not

- Not a public API — Experience-layer traffic must route through the Products Process API.
- Not a search API — supports simple filtering only. Faceted / free-text search is a separate capability at the Experience layer.
- Not a source of pricing promotions — `unitPrice` is the SAP list price; promotions live in the pricing domain.
