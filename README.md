# Sales Transaction Management with Automated Audit Logging Project


### **a) `sales` table**

This table stores sales transactions.
**Columns:**

* `transaction_id INT PRIMARY KEY` → Unique identifier for each sale.
* `sale_date DATE` → The date of the sale.
* `sale_time TIME` → Time of the sale.
* `customer_id INT` → ID of the customer making the purchase.
* `category VARCHAR(50) NOT NULL` → Product category.
* `quantity INT CHECK (quantity > 0)` → Number of items sold; must be positive.
* `price_per_unit DECIMAL(10,2) CHECK (price_per_unit > 0)` → Price per unit; must be positive.
* `cogs DECIMAL(10,2)` → Cost of goods sold.
* `total_sale DECIMAL(10,2)` → Total sale amount (quantity × price_per_unit). Initially `NULL`, will be calculated using triggers.

---

### **b) `sales_audit` table**

This table keeps a record of changes to the `sales` table.
**Columns:**

* `transaction_id INT` → References the sale that was inserted or updated.
* `action_type VARCHAR(10)` → Type of action (`INSERTED` or `UPDATE`).
* `action_time DATETIME` → Timestamp of the action.

---

## **2. Data Insertions**

* Initial sales data is inserted into the `sales` table with 200+ rows, covering multiple categories, dates, and customers.

* Some `total_sale` values are left `NULL` because they will be automatically calculated by triggers.

* There are also **manual insertions** for transactions 201 and 202.

---

## **3. Triggers**

### **a) `before_sales_insert`**

* **Timing:** BEFORE INSERT
* **Purpose:** Automatically calculates `total_sale` as `quantity * price_per_unit` before inserting a row into `sales`.

### **b) `after_sales_insert`**

* **Timing:** AFTER INSERT
* **Purpose:** Logs the insertion in `sales_audit` with `action_type = 'INSERTED'` and current timestamp.

### **c) `before_sales_update`**

* **Timing:** BEFORE UPDATE
* **Purpose:** Recalculates `total_sale` whenever a sale is updated.

### **d) `after_sales_update`**

* **Timing:** AFTER UPDATE
* **Purpose:** Logs the update in `sales_audit` with `action_type = 'UPDATE'` and current timestamp.

---

## **4. Updates and Inserts with Triggers**

* Several **bulk updates** modify the `quantity` and `price_per_unit` for some `transaction_id`s.
* New inserts (transactions 201, 202) are also performed.
* The triggers ensure `total_sale` is calculated and all changes are logged in the `sales_audit` table.

---

## **5. Summary of Functionality**

1. `sales` table tracks detailed transactional data.
2. `sales_audit` table tracks all insert and update actions for auditing purposes.
3. `total_sale` is **automatically calculated** using BEFORE triggers.
4. Every change (insert/update) is **logged in `sales_audit`** using AFTER triggers.
5. The script also demonstrates **bulk updates and new inserts**, showing the triggers in action.

---
