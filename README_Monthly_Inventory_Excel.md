# Monthly Inventory Excel Workflow

This guide explains how to use the Excel-based workflow for recording and reconciling your monthly ingredient inventory in SaleEase.

### Show and Print the Monthly Inventory

- Go to the **Monthly Inventory** page in the admin panel.
- Click the **Preview & Print Stock** button to view and print the current month's inventory.
- Expected usage is auto-calculated on the frontend based on recipes, sales, purchases, and previous inventory.

**Template columns:**
| Ingredient | Expected | Actual |
| ---------- |--------- | ------ |
| Coffee | 740 | |
| Milk | 8600 | |

---

### Generate and Download the Excel Template

- Go to the **Monthly Inventory** page in the admin panel.
- The system will automatically select the previous month (e.g., June 2025) and fetch its data.
- Only the **Actual Count** column is editable in Excel.
- Click **Download Excel Template**.
- The template will include all ingredients and their expected usage for the month (auto-calculated from recipes, sales, and purchases).

**Template columns:**
| Ingredient | Expected | Actual |
| ---------- | -------- | ------ |
| Coffee | 740 | |
| Milk | 8600 | |

---

### Fill in Actual Counts in Excel

- Open the downloaded Excel file.
- For each ingredient, enter the **Actual Count** after your physical inventory count.
- Save the file.

---

### Upload the Filled Excel Sheet

- Back in the app, click **Upload Actual Inventory Sheet**.
- In the dialog, select your filled Excel file and click **Upload**.
- In the dialog, select the month for which you are uploading the inventory (e.g., July 2025).
- The app will preview the data in a table inside the dialog:

| Ingredient | Expected | Actual | Surplus/Deficit |
| ---------- | -------- | ------ | --------------- |
| Coffee     | 740      | 750    | +10             |
| Milk       | 8600     | 8500   | -100            |

- Review the preview. Surplus/deficit is auto-calculated on the frontend before saving.

---

### Save to Database

- If the preview is correct, click **Save to Database**.
- The app will record the actual counts and surplus/deficit for the month.
- These values will be used as the starting point for the next month's inventory.

---

### How Next Month's Expected Inventory is Calculated

- At the start of each new month, the system uses the previous month's `actual` counts as the starting inventory for each ingredient.
- The expected usage for the new month is calculated as:

  `expected = previous_actual + purchases - sales_usage`

  - `previous_actual`: The `actual` value from the last month's `monthly_inventory` record for each ingredient.
  - `purchases`: Total quantity of the ingredient purchased during the new month (from purchase records).
  - `sales_usage`: Total quantity of the ingredient used in recipes for all sales during the new month.

- This ensures that inventory tracking is continuous and discrepancies are carried forward, improving accuracy over time.
- All calculations are performed on the frontend for preview and Excel generation, and validated on the backend for integrity.

---

## Notes

- You can repeat this process for each month.
- Do not change ingredient names or expected usage.
- If you make a mistake, simply re-upload a corrected sheet, It will overwrite the month's data.
- The month format is always `YYYY-M` (e.g. `2025-6`), not `YYYY-MM`.

---

## Technical Details (for Developers/IT)

- All inventory data for a month is stored in a single `monthly_inventory` table record per month, with ingredient records as a JSON array.
- Key columns: `month` (format: `YYYY-M`, e.g. `2025-6`), `ingredient_records` (JSON), `created_at`, `updated_at`.
- Each ingredient record: `{ ingredient_id, ingredient_name, expected, actual, surplus_deficit }`.
- Constraints: unique (month), foreign keys.
- All changes are transactional and logged. Only admins can upload/modify.
- See `scheme.sql` and backend code for details.

**Table schema example:**

```sql
CREATE TABLE monthly_inventory (
  id INT PRIMARY KEY AUTO_INCREMENT,
  month VARCHAR(7) NOT NULL, -- format: 'YYYY-M' (no leading zero, e.g. '2025-6')
  ingredient_records JSON NOT NULL, -- array of ingredient records
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  updated_at DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  UNIQUE(month)
);
```

**ingredient_records JSON structure:**

Each row's `ingredient_records` field is a JSON array of objects, one per ingredient, for example (IDs are strings):

```json
[
  {
    "ingredient_id": "ING-001",
    "ingredient_name": "Coffee",
    "expected": 740,
    "actual": 750,
    "surplus_deficit": 10
  },
  {
    "ingredient_id": "ING-002",
    "ingredient_name": "Milk",
    "expected": 8600,
    "actual": 8500,
    "surplus_deficit": -100
  }
]
```

- All fields are required for each ingredient.
- `surplus_deficit` is always calculated as `actual - expected`.
- Ingredient IDs are always strings (e.g. 'ING-001').

---

For troubleshooting or more details, contact your system administrator or see the in-app help section.
