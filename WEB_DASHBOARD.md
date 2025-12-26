# Web Dashboard Guide

Complete guide for using the Honey Scraper Web Dashboard.

## Quick Start

```bash
# Start the web dashboard
sudo systemctl start honey-scraper-web

# Enable auto-start on boot
sudo systemctl enable honey-scraper-web

# Access in your browser
http://YOUR_SERVER_IP:5000
```

## Features

### 📊 Real-Time Statistics Dashboard
- **Total Stores** - Number of stores in database
- **Domains Scraped** - Number of domains processed
- **Total Coupons** - Available coupon codes
- **Active Stores** - Currently active stores
- **Top Countries** - Store distribution by country

### 🎮 Scraper Control Panel
Start and stop scraping with options:
- **Test Mode** - Scrape 10 domains (quick test)
- **Limited Mode** - Scrape custom number of domains
- **Resume Mode** - Continue from where you left off
- **Full Scrape** - Process all ~178k domains

**Live Progress Monitoring:**
- Current domain being processed
- Progress bar with percentage
- Domains processed vs total
- Stores saved count
- Error count
- Real-time status updates (refreshes every 2 seconds)

### 🔍 Searchable Store Database
- **Search** - Find stores by name, domain, or URL
- **Filter by Country** - Show stores from specific countries
- **Active Only** - Filter to show only active stores
- **Pagination** - Browse through 50 stores per page
- **View Details** - Click any store to see full information

### 📄 Store Details View
- Basic information (name, URL, country, status)
- 30-day shopper statistics
- All available coupon codes
- Coupon usage statistics
- Partial URLs

### 💾 Data Export
- **Export to CSV** - Download as spreadsheet
- **Export to JSON** - Download as JSON file
- Timestamped filenames for organization

## Usage Examples

### Starting a Test Scrape

1. Open dashboard in browser: `http://YOUR_SERVER_IP:5000`
2. Click "▶️ Start Scraping" button
3. Select "Test Mode (10 domains)"
4. Check "Skip already scraped domains" (recommended)
5. Click "Start"
6. Watch progress in real-time!

### Searching for Stores

1. Use the search bar in "Stores Database" section
2. Type store name, domain, or URL
3. Results update automatically after 0.5 seconds
4. Click "View Details" to see full information

### Filtering by Country

1. Use the "All Countries" dropdown
2. Select a country (shows store count)
3. Browse filtered results
4. Combine with search for more specific results

### Monitoring Long-Running Scrape

1. Start a scrape (Limited or Full mode)
2. Dashboard shows:
   - Current domain being processed
   - Progress bar with percentage
   - Number of stores saved
   - Any errors encountered
3. Status updates every 2 seconds automatically
4. Leave browser open to monitor, or close and come back later

### Exporting Data

1. Click "📥 Export CSV" or "📥 Export JSON"
2. File downloads automatically
3. Filename includes timestamp
4. Contains all current database data

## API Endpoints

The dashboard provides a REST API:

### Statistics
```
GET /api/stats
```
Returns database statistics and top countries.

### Scraper Status
```
GET /api/scraper/status
```
Returns current scraper state and progress.

### Start Scraper
```
POST /api/scraper/start
Content-Type: application/json

{
  "max_domains": 100,      // null for all domains
  "skip_existing": true
}
```

### Stop Scraper
```
POST /api/scraper/stop
```

### Get Stores
```
GET /api/stores?page=1&per_page=50&search=amazon&country=US&active_only=true
```

### Get Store Details
```
GET /api/store/{store_id}
```

### Get Countries
```
GET /api/countries
```

### Export Data
```
GET /api/export/csv
GET /api/export/json
```

## Running the Dashboard

### As a Service (Recommended)
```bash
# Start
sudo systemctl start honey-scraper-web

# Stop
sudo systemctl stop honey-scraper-web

# Status
sudo systemctl status honey-scraper-web

# Enable auto-start
sudo systemctl enable honey-scraper-web

# View logs
tail -f web_dashboard.log
```

### Manually
```bash
# Using helper script
./start_web.sh

# Or directly
source venv/bin/activate
python web_dashboard.py --host 0.0.0.0 --port 5000
```

### Command Line Options
```bash
python web_dashboard.py --help
python web_dashboard.py --host 0.0.0.0 --port 5000 --debug
```

## Accessing from Different Devices

### From Server
```
http://localhost:5000
```

### From Same Network
```
http://YOUR_SERVER_IP:5000
```

### From Internet (requires port forwarding)
```
http://YOUR_PUBLIC_IP:5000
```

**Security Note:** The dashboard has no authentication. If exposing to the internet, use a reverse proxy with authentication (nginx, caddy) or firewall rules.

## Configuration

### Change Port
Edit the service file or use command line argument:
```bash
python web_dashboard.py --port 8080
```

### Change Host
Default is `0.0.0.0` (all interfaces). To restrict:
```bash
python web_dashboard.py --host 127.0.0.1  # localhost only
```

## Troubleshooting

### Dashboard won't start
```bash
# Check if port is in use
sudo netstat -tulpn | grep 5000

# Check service status
sudo systemctl status honey-scraper-web

# View logs
tail -f web_dashboard.log
tail -f web_dashboard_error.log
```

### Can't access from other devices
```bash
# Check firewall
sudo ufw status

# Open port if needed
sudo ufw allow 5000/tcp

# Check if service is listening on all interfaces
sudo netstat -tulpn | grep 5000
```

### Database locked error
The scraper and dashboard can both access the database simultaneously, but if you see errors:
```bash
# Stop scraper service temporarily
sudo systemctl stop honey-scraper

# Or wait for current domain to finish
```

### Scraper won't start from dashboard
- Check that scraper isn't already running
- Check `scraper.log` for errors
- Verify database is accessible
- Ensure proper permissions

## Best Practices

1. **Start with Test Mode** - Always test with 10 domains first
2. **Monitor Progress** - Keep dashboard open during scraping
3. **Use Resume Mode** - If interrupted, use resume to continue
4. **Regular Exports** - Export data periodically for backups
5. **Check Stats** - Use "🔄 Refresh Stats" to update numbers
6. **Search First** - Search before scrolling through all pages
7. **Filter by Country** - Narrow down results by country

## Security Considerations

⚠️ **Important:** The web dashboard has no built-in authentication!

To secure it:

### Option 1: Firewall (Recommended)
```bash
# Only allow specific IP
sudo ufw allow from YOUR_IP to any port 5000

# Or use SSH tunnel
ssh -L 5000:localhost:5000 user@server
# Then access at http://localhost:5000
```

### Option 2: Reverse Proxy with Authentication
Use nginx or Caddy with HTTP Basic Auth:

```nginx
server {
    listen 80;
    server_name your-domain.com;
    
    location / {
        auth_basic "Honey Scraper";
        auth_basic_user_file /etc/nginx/.htpasswd;
        proxy_pass http://localhost:5000;
    }
}
```

### Option 3: VPN
Run dashboard on private network only, access via VPN.

## Performance Tips

- Dashboard uses minimal resources (~50MB RAM)
- Real-time updates use simple polling (2-second interval)
- Database queries are indexed for performance
- Pagination limits load to 50 stores per page
- Large exports may take a moment (handled server-side)

## Integration with Other Tools

### Use with curl
```bash
# Get stats
curl http://localhost:5000/api/stats

# Start scraper
curl -X POST http://localhost:5000/api/scraper/start \
  -H "Content-Type: application/json" \
  -d '{"max_domains": 100, "skip_existing": true}'

# Export data
curl http://localhost:5000/api/export/csv -O
```

### Use with Python
```python
import requests

# Get stats
stats = requests.get('http://localhost:5000/api/stats').json()
print(f"Total stores: {stats['stats']['total_stores']}")

# Start scraper
response = requests.post('http://localhost:5000/api/scraper/start', 
    json={'max_domains': 10, 'skip_existing': True})
```

## Mobile Access

The dashboard is responsive and works on mobile devices:
- Access same URL from phone/tablet
- Touch-friendly buttons and controls
- Responsive layout adapts to screen size
- Works on iOS, Android, any modern browser

## Support

For issues:
1. Check service status: `sudo systemctl status honey-scraper-web`
2. View logs: `tail -f web_dashboard.log`
3. Check error logs: `tail -f web_dashboard_error.log`
4. Verify Flask is installed: `pip list | grep -i flask`
5. Test manually: `./start_web.sh`

## Features Roadmap

Potential future additions:
- User authentication
- Scheduled scraping
- Email notifications
- Advanced filtering
- Charts and graphs
- Multi-user support
- API rate limiting
