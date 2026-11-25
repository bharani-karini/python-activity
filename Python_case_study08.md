# Case Study 49: Tuple-Based Product Catalogue Audit

---

## 1. Problem Statement

Store product records as immutable tuples containing product ID, product name, unit price, stock quantity, and reorder threshold. Audit the catalogue to detect invalid records, calculate the total valid inventory value, and identify products requiring replenishment.

## 2. Objectives

- To store structured product records as tuples inside a list.
- To use tuple unpacking to access fields cleanly during iteration.
- To detect invalid records such as duplicate IDs, non-positive prices, and negative quantities or reorder thresholds.
- To calculate the total inventory value of valid products.
- To identify products requiring replenishment.

## 3. Assumptions

- Each product tuple follows the format: `(product_id, name, unit_price, stock_qty, reorder_level)`.
- A record is invalid if:
  - The product ID is duplicated.
  - The unit price is less than or equal to zero.
  - The stock quantity is negative.
  - The reorder threshold is negative.
- Only valid records are included in the inventory valuation.
- Valid products with `stock_qty <= reorder_level` require replenishment.
- Product IDs are converted to uppercase before validation.

## 4. Algorithm

1. Start the program.
2. Input the number of product records, `n`.
3. Read the product ID, name, unit price, stock quantity, and reorder threshold for each product.
4. Store each product as a tuple inside the catalogue list.
5. Initialize:
   - `seen_ids` as an empty list.
   - `invalid_records` as an empty list.
   - `reorder_items` as an empty list.
   - `total_inventory_value` as `0.0`.
6. Iterate through the catalogue using tuple unpacking.
7. Check whether the product ID is duplicated or any numerical field violates the validation rules.
8. If invalid, add the record and its reason to `invalid_records`.
9. Otherwise:
   - Add the product ID to `seen_ids`.
   - Calculate `price * qty` and add it to the total inventory value.
   - If `qty <= reorder`, add the product to `reorder_items`.
10. Display the audit report, including invalid records, total inventory value, and replenishment alerts.
11. Stop the program.

## 5. Flowchart

```text
              [ Start ]
                  |
                  v
       [ Read number of products ]
                  |
                  v
       [ Input product records ]
                  |
                  v
       [ Store records as tuples ]
                  |
                  v
       [ Initialize audit variables ]
                  |
                  v
          [ More records? ]
             /       \
           Yes        No
            |          |
            v          v
      [ Unpack tuple ] [ Print Audit Report ]
            |                  |
            v                  v
      [ Validate record ]    [ End ]
            |
            v
   < Duplicate ID or invalid
     price/quantity/threshold? >
          /          \
        Yes           No
         |             |
         v             v
 [ Log invalid record ] [ Add ID to seen_ids ]
         |             |
         |             v
         |      [ Calculate price * qty ]
         |             |
         |             v
         |       [ Update total value ]
         |             |
         |             v
         |       < qty <= reorder? >
         |          /       \
         |        Yes        No
         |         |          |
         |         v          |
         |   [ Add to reorder list ]
         |         |          |
         +---------+----------+
                   |
                   v
           [ Next record ]
```

## 6. Pseudocode

```text
BEGIN
    READ n
    SET catalogue = []

    FOR i FROM 1 TO n DO
        READ pid, name, price, qty, reorder
        APPEND (pid, name, price, qty, reorder) TO catalogue
    ENDFOR

    SET seen_ids = []
    SET total_value = 0.0
    SET invalid_list = []
    SET reorder_list = []

    FOR EACH (pid, name, price, qty, reorder) IN catalogue DO

        IF pid IN seen_ids THEN
            APPEND (pid, name, "Duplicate Product ID")
            TO invalid_list

        ELSE IF price <= 0 THEN
            APPEND (pid, name, "Invalid price")
            TO invalid_list

        ELSE IF qty < 0 OR reorder < 0 THEN
            APPEND (pid, name, "Negative stock or reorder level")
            TO invalid_list

        ELSE
            APPEND pid TO seen_ids
            SET total_value = total_value + (price * qty)

            IF qty <= reorder THEN
                APPEND (pid, name, qty, reorder)
                TO reorder_list
            ENDIF
        ENDIF

    ENDFOR

    PRINT invalid_list
    PRINT total_value
    PRINT reorder_list
END
```

## 7. Python Source Code

```python
# Case Study 49: Tuple-Based Product Catalogue Audit

n = int(input("Enter number of product records to audit: "))
catalogue = []

# Read product records and store them as tuples
for i in range(1, n + 1):
    print(f"\nProduct {i}:")

    pid = input("  Product ID: ").strip().upper()
    name = input("  Product Name: ").strip()
    price = float(input("  Unit Price (Rs.): "))
    qty = int(input("  Stock Quantity: "))
    reorder = int(input("  Reorder Threshold: "))

    record = (pid, name, price, qty, reorder)
    catalogue.append(record)

# Initialize audit variables
seen_ids = []
invalid_records = []
reorder_items = []
total_inventory_value = 0.0

# Audit catalogue using tuple unpacking
for pid, name, price, qty, reorder in catalogue:

    if pid in seen_ids:
        invalid_records.append(
            (pid, name, "Duplicate Product ID")
        )

    elif price <= 0:
        invalid_records.append(
            (pid, name, f"Invalid price (Rs. {price})")
        )

    elif qty < 0 or reorder < 0:
        invalid_records.append(
            (pid, name, "Negative stock or reorder level")
        )

    else:
        seen_ids.append(pid)

        total_inventory_value += price * qty

        if qty <= reorder:
            reorder_items.append(
                (pid, name, qty, reorder)
            )

# Display audit report
print("\n================ PRODUCT CATALOGUE AUDIT REPORT ================")

print(
    f"Total Valid Inventory Value: "
    f"Rs. {total_inventory_value:,.2f}"
)

print("\n1. Invalid Records Detected:")

if len(invalid_records) == 0:
    print("   None")
else:
    for pid, name, reason in invalid_records:
        print(f"   - [{pid}] {name}: {reason}")

print("\n2. Items Requiring Replenishment:")

if len(reorder_items) == 0:
    print("   All valid items have sufficient stock.")
else:
    for pid, name, qty, reorder in reorder_items:
        print(
            f"   - [{pid}] {name}: Stock = {qty} "
            f"(Threshold = {reorder})"
        )

print("================================================================")
```

## 8. Test Cases

### Test Data

| Record | Product ID | Product Name | Price (Rs.) | Quantity | Reorder Level | Result |
|---|---|---|---:|---:|---:|---|
| 1 | P101 | Wireless Mouse | 650 | 8 | 10 | Valid |
| 2 | P102 | Mechanical Keyboard | 2400 | 15 | 5 | Valid |
| 3 | P101 | USB Hub | 500 | 20 | 5 | Invalid: Duplicate ID |
| 4 | P103 | Webcam | -1200 | 4 | 5 | Invalid: Non-positive price |

### Inventory Calculation

Only valid records are included in the inventory valuation.

- Wireless Mouse = 650 × 8 = Rs. 5,200
- Mechanical Keyboard = 2400 × 15 = Rs. 36,000

**Total Valid Inventory Value:**

```text
5200 + 36000 = Rs. 41,200
```

### Replenishment Check

| Product | Stock | Reorder Level | Status |
|---|---:|---:|---|
| Wireless Mouse | 8 | 10 | Replenishment Required |
| Mechanical Keyboard | 15 | 5 | Sufficient Stock |

## 9. Sample Output

```text
Enter number of product records to audit: 4

Product 1:
  Product ID: P101
  Product Name: Wireless Mouse
  Unit Price (Rs.): 650
  Stock Quantity: 8
  Reorder Threshold: 10

Product 2:
  Product ID: P102
  Product Name: Mechanical Keyboard
  Unit Price (Rs.): 2400
  Stock Quantity: 15
  Reorder Threshold: 5

Product 3:
  Product ID: P101
  Product Name: USB Hub
  Unit Price (Rs.): 500
  Stock Quantity: 20
  Reorder Threshold: 5

Product 4:
  Product ID: P103
  Product Name: Webcam
  Unit Price (Rs.): -1200
  Stock Quantity: 4
  Reorder Threshold: 5

================ PRODUCT CATALOGUE AUDIT REPORT ================
Total Valid Inventory Value: Rs. 41,200.00

1. Invalid Records Detected:
   - [P101] USB Hub: Duplicate Product ID
   - [P103] Webcam: Invalid price (Rs. -1200.0)

2. Items Requiring Replenishment:
   - [P101] Wireless Mouse: Stock = 8 (Threshold = 10)
================================================================
```

## 10. Conclusion

The Tuple-Based Product Catalogue Audit demonstrates how Python tuples can store structured and immutable product records. Tuple unpacking makes record processing simple and readable, while validation helps identify duplicate IDs and invalid numerical values. The program also calculates the total value of valid inventory and identifies products that require replenishment.

This approach provides a clear and organized method for basic inventory auditing using Python lists, tuples, loops, and conditional statements.

---

**
