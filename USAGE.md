# How to Run the Honey Scraper

## Quick Start

### 1. Install Dependencies
```powershell
pip install -r requirements.txt
```

### 2. Run the Scraper
```powershell
python scraper.py
```

### 3. Choose an Option
The script will show you a menu:
- **Option 1**: Test mode (10 domains) - Quick test
- **Option 2**: Full scrape (~178k domains) - Takes many hours!
- **Option 3**: Custom limit - Your choice
- **Option 4**: View stats - See what's in the database
- **Option 5**: Export to JSON - Get data as JSON file
- **Option 6**: Export to CSV - Get data as spreadsheet
- **Option 7**: Resume - Continue interrupted scraping

## What Happens

1. **Downloads all domain list** (~178k domains in one request)
2. **For each domain**:
   - Gets store IDs for that domain
   - Fetches full store details (name, coupons, stats, etc.)
   - Saves to SQLite database
3. **Data is stored in**: `honey_stores.db` (SQLite database file)

## Features

✅ **Resume capability** - If interrupted, run option 7 to continue  
✅ **No memory issues** - Data goes straight to database  
✅ **Progress tracking** - See stats anytime with option 4  
✅ **Export options** - Get JSON or CSV when done  
✅ **Full data** - Everything from the API is saved  

## Database Tables

- **stores** - Main store information
- **coupons** - All coupon codes with metadata
- **partial_urls** - Store URL mappings
- **scraped_domains** - Tracking which domains are done

## Example Workflow

```powershell
# 1. Test with 10 domains first
python scraper.py
# Choose: 1

# 2. Check what you got
python scraper.py
# Choose: 4 (view stats)

# 3. Export to CSV to view in Excel
python scraper.py
# Choose: 6 (export to CSV)

# 4. Run full scrape (takes hours!)
python scraper.py
# Choose: 2

# 5. If it stops, resume later
python scraper.py
# Choose: 7 (resume)
```

## Tips

- Start with option 1 (test mode) to verify it works
- Full scrape will take 10-20+ hours depending on your connection
- Database file will be ~100-300 MB when complete
- You can safely stop (Ctrl+C) and resume later with option 7
- Use option 4 anytime to see progress
