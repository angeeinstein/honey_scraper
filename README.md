# Honey Store Data Scraper

Scrapes store information from Honey's public API endpoints with a web dashboard for monitoring and control.

## ✨ Features

- 🗄️ **SQLite Database** - Efficient storage for ~178,000+ stores
- 🌐 **Web Dashboard** - Beautiful UI to control and monitor scraping
- 📊 **Real-time Progress** - Watch scraping progress live
- 🔍 **Searchable Database** - Search and filter stores by name, domain, country
- 🎯 **Smart Resume** - Continue interrupted scraping automatically
- 💾 **Data Export** - Export to CSV or JSON
- 🔄 **Auto-restart** - systemd service with automatic restart
- 📈 **Statistics** - Store counts, coupons, top countries

### Data Collected

- Store metadata (name, country, URLs, logos)
- Shopper statistics (24h, 30d, trends)
- Available coupon codes with usage stats
- Technical configuration (DOM selectors, timing)
- Affiliate information
- Partial URL mappings

## 🚀 Quick Start

### Ubuntu/Debian (Recommended for Production)

```bash
# Clone the repository
git clone https://github.com/angeeinstein/honey_scraper.git
cd honey_scraper

# Run the comprehensive installer
chmod +x install.sh
sudo bash install.sh

# Start the web dashboard
sudo systemctl start honey-scraper-web

# Access dashboard in browser
http://YOUR_SERVER_IP:5000
```

### Windows (Development/Testing)

```powershell
# Clone repository
git clone https://github.com/angeeinstein/honey_scraper.git
cd honey_scraper

# Install dependencies
pip install -r requirements.txt

# Run interactively
python scraper.py

# Or start web dashboard
python web_dashboard.py
# Then open http://localhost:5000
```

## 📖 Documentation

- **[DEPLOYMENT.md](DEPLOYMENT.md)** - Ubuntu LXC deployment guide
- **[WEB_DASHBOARD.md](WEB_DASHBOARD.md)** - Web dashboard usage guide
- **[USAGE.md](USAGE.md)** - Command-line usage instructions

## 🌐 Web Dashboard

The web dashboard provides:

- **📊 Live Statistics** - Total stores, domains, coupons, active stores
- **🎮 Scraper Control** - Start/stop with test/limited/resume/full modes
- **📈 Progress Monitoring** - Real-time progress bar and status
- **🔍 Store Browser** - Search, filter, and browse all stores
- **💾 Export Tools** - Download CSV or JSON exports
- **🌍 Country Filter** - Browse stores by country

![Dashboard Preview](https://via.placeholder.com/800x400?text=Web+Dashboard+Preview)

### Starting the Dashboard

```bash
# As a service (Ubuntu/Linux)
sudo systemctl start honey-scraper-web
sudo systemctl enable honey-scraper-web

# Manual start
python web_dashboard.py --host 0.0.0.0 --port 5000

# Access at
http://localhost:5000
```

## 💻 Command Line Usage

### Interactive Mode

```bash
python scraper.py
```

Choose from menu:
1. Test mode (10 domains)
2. Full scrape (all ~178k domains)
3. Custom limit
4. View statistics
5. Export to JSON
6. Export to CSV
7. Resume scraping

### Command Line Arguments

```bash
# Auto-resume mode (for services)
python scraper.py auto

# View statistics
python scraper.py stats

# Custom limit
python scraper.py limit=100
```

### Programmatic Usage

```python
from scraper import HoneyScraper

# Create scraper (uses SQLite database)
scraper = HoneyScraper(delay=0.5, db_path="honey_stores.db")

# Scrape domains
scraper.scrape_all_stores(max_domains=10)

# Get statistics
stats = scraper.get_stats()
print(f"Total stores: {stats['total_stores']}")

# Export data
scraper.export_to_csv("export.csv")
scraper.export_to_json("export.json")
```

## 🗄️ Database Schema

### Tables

- **stores** - Main store information (28 fields)
- **coupons** - All coupon codes with metadata
- **partial_urls** - Store URL mappings
- **scraped_domains** - Tracking for resume capability

### Query Examples

```sql
-- Get all stores in Germany with coupons
SELECT s.name, COUNT(c.id) as coupon_count
FROM stores s
LEFT JOIN coupons c ON s.store_id = c.store_id
WHERE s.country = 'DE'
GROUP BY s.store_id
HAVING coupon_count > 0;

-- Top 10 stores by shoppers
SELECT name, country, shoppers_30d
FROM stores
ORDER BY shoppers_30d DESC
LIMIT 10;

-- Stores by country
SELECT country, COUNT(*) as count
FROM stores
GROUP BY country
ORDER BY count DESC;
```

## 🔧 Installation (Detailed)

### Prerequisites

- Python 3.6+
- pip
- SQLite3 (included with Python)

### Install Dependencies

```bash
pip install -r requirements.txt
```

Requirements:
- `requests>=2.31.0` - HTTP requests
- `flask>=3.0.0` - Web dashboard

### Ubuntu/Debian System Setup

The `install.sh` script handles:
- System package installation (python3, pip, sqlite3, git)
- Virtual environment setup
- Python dependency installation
- systemd service creation (scraper + web dashboard)
- Helper script creation
- Firewall configuration
- Permission setup
- Installation testing

## 🐧 systemd Services

### Scraper Service

```bash
# Start/stop/status
sudo systemctl start honey-scraper
sudo systemctl stop honey-scraper
sudo systemctl status honey-scraper

# Enable auto-start on boot
sudo systemctl enable honey-scraper

# View logs
tail -f scraper.log
sudo journalctl -u honey-scraper -f
```

### Web Dashboard Service

```bash
# Start/stop/status
sudo systemctl start honey-scraper-web
sudo systemctl stop honey-scraper-web
sudo systemctl status honey-scraper-web

# Enable auto-start
sudo systemctl enable honey-scraper-web

# View logs
tail -f web_dashboard.log
```

## 📊 Performance & Timing

- **178,000+ domains** with 0.5s delay ≈ **25-30 hours**
- **Resume capability** - Can stop and continue anytime
- **Progress tracking** - Monitor in real-time via web dashboard
- **Memory efficient** - Streams to database, low RAM usage
- **Database size** - ~100-300 MB for complete dataset

## 🔌 API Endpoints

### Honey API (scraped)

1. `GET /v2/stores/partials/supported-domains` - All ~178k domains
2. `GET /v3?operationName=ext_getStorePartialsByDomain` - Domain → Store IDs
3. `GET /v3?operationName=ext_getStoreById` - Full store details

### Web Dashboard API

- `GET /api/stats` - Database statistics
- `GET /api/scraper/status` - Scraper status and progress
- `POST /api/scraper/start` - Start scraping
- `POST /api/scraper/stop` - Stop scraping
- `GET /api/stores` - Paginated store list (with search/filter)
- `GET /api/store/{id}` - Store details
- `GET /api/countries` - Country list
- `GET /api/export/csv` - Export CSV
- `GET /api/export/json` - Export JSON

## 🎯 Use Cases

- **E-commerce Research** - Analyze coupon strategies across stores
- **Market Analysis** - Study store distribution by country
- **Affiliate Marketing** - Find stores with affiliate programs
- **Coupon Aggregation** - Build coupon database
- **Competitor Analysis** - Track competitor presence
- **Data Science** - Large dataset for analysis/ML

## 📁 Project Structure

```
honey_scraper/
├── scraper.py              # Main scraper with SQLite storage
├── web_dashboard.py        # Flask web application
├── install.sh              # Comprehensive installer script
├── requirements.txt        # Python dependencies
├── templates/
│   └── dashboard.html      # Web dashboard UI
├── honey_stores.db         # SQLite database (created on first run)
├── scraper.log            # Scraper logs
├── web_dashboard.log      # Dashboard logs
├── run.sh                 # Interactive runner (created by installer)
├── status.sh              # Status checker (created by installer)
├── update.sh              # Dependency updater (created by installer)
├── start_web.sh           # Web dashboard starter (created by installer)
├── README.md              # This file
├── USAGE.md               # CLI usage guide
├── DEPLOYMENT.md          # Deployment guide
└── WEB_DASHBOARD.md       # Dashboard guide
```

## 🛠️ Helper Scripts

Created by `install.sh`:

```bash
./run.sh        # Run scraper interactively
./status.sh     # Show service status and database stats
./update.sh     # Update dependencies
./start_web.sh  # Start web dashboard manually
```

## 🔒 Security

**Web Dashboard:**
- ⚠️ No authentication by default
- Recommended: Use firewall rules, SSH tunnels, or reverse proxy with auth
- See [WEB_DASHBOARD.md](WEB_DASHBOARD.md) for security setup

**systemd Services:**
- Runs as non-root user
- Sandboxed with systemd security settings
- Read-only system directories
- Private /tmp

## 🐛 Troubleshooting

### Database locked

```bash
# Stop scraper service
sudo systemctl stop honey-scraper

# Check for running processes
ps aux | grep scraper
```

### Web dashboard won't start

```bash
# Check logs
tail -f web_dashboard.log
tail -f web_dashboard_error.log

# Check port availability
sudo netstat -tulpn | grep 5000

# Verify Flask is installed
pip list | grep -i flask
```

### Scraper not resuming

```bash
# Check scraped domains
sqlite3 honey_stores.db "SELECT COUNT(*) FROM scraped_domains;"

# View database stats
python scraper.py stats
```

### Installation issues

```bash
# Re-run installer
sudo bash install.sh
# Choose option 1 to update

# Test manually
./run.sh
```

## 📈 Future Enhancements

Potential additions:
- User authentication for web dashboard
- Scheduled scraping (cron jobs)
- Email notifications on completion/errors
- Advanced analytics and charts
- Price tracking (if available)
- Store change detection
- Multi-threaded scraping
- Docker containerization

## 🤝 Contributing

Contributions welcome! Areas for improvement:
- Additional data export formats
- Enhanced search/filtering
- Performance optimizations
- Additional statistics/charts
- Documentation improvements

## ⚖️ Legal & Ethical Use

- This scraper uses **public API endpoints**
- Respects rate limits with configurable delays
- For educational and research purposes
- Follow Honey's Terms of Service
- Don't overload their servers

## 📄 License

MIT License - See LICENSE file for details

## 🙏 Acknowledgments

- Honey (PayPal) for providing public API endpoints
- Flask for the web framework
- SQLite for efficient data storage

## 📞 Support

For issues or questions:
1. Check documentation files (USAGE.md, DEPLOYMENT.md, WEB_DASHBOARD.md)
2. Review logs (`scraper.log`, `web_dashboard.log`)
3. Check service status: `sudo systemctl status honey-scraper`
4. Open an issue on GitHub

---

**Last Updated:** December 2025  
**Version:** 2.0.0 (with Web Dashboard)
