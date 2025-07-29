# RoninVault - Pokemon Card Inventory Management System

## Overview

RoninVault is a Pokemon card inventory management tool that processes transaction data from Ronin to determine which cards need to be shipped to the vault versus shipped directly to customers. The system automatically categorizes items, calculates costs, and provides export functionality for tracking purposes.

## How It Works

### The Problem
We need to determine which Pokemon cards should be:
1. **Vault Only** - Shipped to vault for storage
2. **Vault + Ship** - Shipped to vault, then forwarded to customers

### Data Sources

1. **Pokemon Pack Data** (from Ronin, twice weekly)
   - Contains transaction records with status indicators
   - Status meanings:
     - `claimed` or blank = ship to vault
     - `buyback` = sold back, no action needed

2. **REF Data** (reference catalog)
   - Master Pokemon card database with prices, grades, cert numbers
   - Maps token IDs to card details and pricing

3. **Product Purchases** (internal customer orders)
   - Shows which cards customers have purchased
   - Used to determine if vault items need customer shipping

### Processing Logic

1. **Basic Categorization**: Cards marked "claimed" or blank go to vault
2. **Customer Override**: If a card appears in Product Purchases, it becomes "Vault + Ship"
3. **Edge Case Handling**: Buyback items that appear in purchases still get shipped (noted appropriately)
4. **Cost Calculation**: NEC Cost = Reference Price ÷ 1.15 (rounded up to whole dollars)

### Output Categories

- **"Vault Only"**: Cards to store in vault
- **"Vault + Ship"**: Cards to vault first, then ship to customers (matches product purchases count exactly)

## Step-by-Step Process

### 1. Retrieve Data from Ronin
- Download the latest Pokemon pack data (twice weekly export)
- Save as CSV file

### 2. Run Database Query
Execute this query to get product purchases data:

```sql
SELECT 
  pp.*, 
  p.cert_number
FROM 
  product_purchases pp
LEFT JOIN 
  products p ON pp.product_id = p.id
WHERE 
  seller_id = '486902';
```

### 3. Export Product Purchases Data
- Export query results as CSV
- Save with descriptive filename (e.g., `product_purchases_MMDDYYYY.csv`)

### 4. Upload Data to Tool
1. Open `index.html` in your browser
2. Upload REF data (first time only - it's stored persistently)
3. Upload Pokemon pack data (Ronin export)
4. Upload product purchases data (database export)

### 5. Process Data
1. Click "Process Data" button
2. Review summary statistics
3. Export results using preferred format:
   - CSV for spreadsheet import
   - Excel format for direct use
   - Copy to clipboard for quick pasting
   - Save snapshot locally for records

### 6. Track and Compare Results
- Import results into NEC_TO_SHIP tracking sheet: [NEC Tracking Sheet](https://docs.google.com/spreadsheets/d/1r1cvwC6ck8sSxn8f-6j_wuJEC-wgZPTRLusQgLwwht4/edit?gid=0#gid=0)
- Create new dated tab for each data dump
- Compare against previous exports to determine:
  - What has been shipped/vaulted
  - What still needs to go out
  - Amount owed calculations

## Data Verification (QC Process)

### Using the Cert Comparison Tool

1. **Prepare Comparison File**:
   - Create a CSV with two columns:
     - `cert_number_new`: Cert numbers from your current tool output
     - `cert_number_shipped`: Cert numbers from previously shipped items
   
2. **Run Comparison**:
   - Click "Open Cert Comparison Tool" button in results section
   - Upload your comparison CSV file
   - Click "Analyze Comparison"

3. **Review Results**:
   - **Match Percentage**: Shows data integrity (expect ~98-99%)
   - **Missing Items**: Items shipped previously but not in current analysis
   - **New Items**: Items in current analysis not yet shipped

### What Good Results Look Like
- High match percentage (98%+)
- Few missing items (normal due to data flow timing)
- New items count matches expected new inventory

## Files in This Project

- **`index.html`**: Main processing tool
- **`cert-comparison.html`**: Data verification tool
- **`REF_test - Sheet1.csv`**: Reference data sample
- **`certsshipped.csv`**: Previously shipped items for comparison
- **`CLAUDE.md`**: Technical documentation

## Technical Notes

- REF data is stored persistently in browser localStorage
- All processing happens client-side (no server required)
- Supports various CSV formats and handles quoted fields properly
- Cost calculations round up to whole dollars as required
- Export formats compatible with Google Sheets and Excel

## Troubleshooting

### Common Issues
1. **NaN Prices**: Check REF data formatting, ensure price fields contain valid numbers
2. **Missing Items**: Verify all three data files are uploaded correctly
3. **Count Mismatches**: Review product purchases data for completeness

### Data Quality Checks
- Vault + Ship count should equal Product Purchases count
- REF data should contain pricing for all token IDs
- Cert numbers should be consistent across all files

## Support

For technical issues or questions about the tool functionality, refer to the CLAUDE.md file for detailed technical specifications and data structure information.