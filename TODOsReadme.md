## Monthly Inventory

- [x] Make monthly inventory screen and add it to admin panel (layout matches inventory management)
- [x] Preview and print monthly inventory report based on actual counts
- [x] Generate and export Excel template for the selected month (auto-filled with ingredients and expected usage)
- [x] Show table of the upcoming monthly inventory data with columns: Ingredient ID, Ingredient Name, Expected Usage, Actual Count (empty)
- [x] Upload filled Excel sheet in dialog
  - [x] Upload filled Excel sheet using the dialog (with in-dialog preview) to review and confirm the previewed data (surplus/deficit auto-calculated)
  - [x] Save actual counts and surplus/deficit to the database for the month (save button)
  - [x] Add/update monthly inventory record via backend POST API (auto-detects insert or update)
  - [x] Month picker in upload dialog, value passed to backend
  - [x] Dialog DRY: uses generic ImportExcelDialog with extraContent for month picker
