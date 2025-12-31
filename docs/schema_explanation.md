

```md
# Schema Explanation

This document explains the rationale behind the schema design used in the Amazon-style e-commerce analytics project.

The schema is designed for **analytical correctness**, not application development.

---

## customers

**Purpose:**  
Stores unique customer identities.

**Grain:**  
1 row = 1 customer

**Key Columns:**
- `customer_id` — surrogate primary key
- `email` — unique business identifier
- `signup_date` — supports cohort and lifecycle analysis

**Excluded by design:**
- Addresses (order-level concern)
- Behavioral metrics (derived from orders)

---

## categories

**Purpose:**  
Groups products for higher-level analysis.

**Grain:**  
1 row = 1 category

**Key Columns:**
- `category_id`
- `category_name`

**Note:**  
Hierarchies (parent categories) are intentionally excluded to keep scope focused.

---

## products

**Purpose:**  
Stores product master data.

**Grain:**  
1 row = 1 product

**Key Columns:**
- `product_id`
- `category_id` (FK)
- `price` — current list price

**Important:**  
Transaction prices are not stored here to preserve historical accuracy.

---

## orders

**Purpose:**  
Represents checkout transactions.

**Grain:**  
1 row = 1 order

**Key Columns:**
- `order_id`
- `customer_id` (FK)
- `order_date`
- `order_status`

**Note:**  
Revenue is not stored at the order level.

---

## order_items

**Purpose:**  
Captures line-level purchased items.

**Grain:**  
1 row = 1 product per order

**Key Columns:**
- `order_item_id`
- `order_id` (FK)
- `product_id` (FK)
- `quantity`
- `unit_price`

**Why this matters:**  
This table defines the **true revenue grain**.

---

## payments

**Purpose:**  
Tracks financial transactions for orders.

**Grain:**  
1 row = 1 payment attempt

**Key Columns:**
- `payment_id`
- `order_id` (FK)
- `payment_method`
- `payment_status`
- `amount`

**Design choice:**  
Payments are separated from orders to reflect real-world financial flows.

---

## Design Summary

- Schema follows normalized, analytics-first design
- Grain is explicitly defined for every table
- Derived metrics are computed in queries, not stored
- Schema is extensible for future domains (returns, reviews, logistics)

This design mirrors how real analytics systems are structured in production environments.
