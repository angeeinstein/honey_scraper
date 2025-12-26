# Honey Store Data Scraper

Scrapes store information from Honey's public API endpoints.

## Features

- Fetches all ~178,000 supported domains
- Gets store IDs for each domain
- Retrieves detailed store information including:
  - Store metadata (name, country, URLs)
  - Shopper statistics
  - Available coupon codes
  - Technical configuration
  - Affiliate information

## Installation

```bash
pip install requests
```

## Usage

### Basic Usage

```bash
python scraper.py
```

The script will prompt you to choose:
1. **Test mode** - Process first 10 domains (quick test)
2. **Full scrape** - All ~178k domains (takes many hours)
3. **Custom limit** - Specify number of domains

### Programmatic Usage

```python
from scraper import HoneyScraper

# Create scraper with 0.5 second delay between requests
scraper = HoneyScraper(delay=0.5)

# Test with 10 domains
scraper.scrape_all_stores(max_domains=10, output_file="honey_stores_test.json")

# Full scrape (will take hours!)
scraper.scrape_all_stores(output_file="honey_stores_full.json")

# Export to CSV
scraper.export_to_csv("honey_stores_test.json", "honey_stores_test.csv")
```

## Output Format

### JSON Output
```json
[
  {
    "domain": "amazon.de",
    "storeId": "136389739121320038",
    "partialURL": "amazon.de",
    "details": {
      "name": "Amazon Germany",
      "country": "DE",
      "url": "https://www.amazon.de",
      "shoppers30d": 306112,
      "publicCoupons": [...],
      ...
    }
  }
]
```

### CSV Output
Flattened data with key fields: domain, storeId, name, country, url, shoppers30d, num_coupons, etc.

## Performance Notes

- **178,000 domains** at 0.5s delay = ~25 hours minimum
- Progress is saved every 100 domains
- Adjust `delay` parameter to balance speed vs. server load
- Consider running in batches or overnight

## API Endpoints Used

1. `GET /v2/stores/partials/supported-domains` - All domains
2. `GET /v3?operationName=ext_getStorePartialsByDomain` - Domain → Store IDs
3. `GET /v3?operationName=ext_getStoreById` - Store details

## Data Fields Available

- Store information: name, country, URL, logo
- Activity metrics: shoppers24h, shoppers30d, savings data
- Coupon codes: code, description, success rates
- Technical config: DOM selectors, timing, site type
- Affiliate information
- User-generated content

## Tips

- Start with test mode to verify everything works
- Use custom limit for specific analyses
- Monitor disk space (full dataset will be large)
- Consider filtering by country or specific domains
