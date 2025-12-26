# Ubuntu LXC Deployment Guide

Complete guide for deploying the Honey Scraper on an Ubuntu LXC container.

## Quick Start (3 Steps)

```bash
# 1. Copy files to your LXC container
# 2. Run the installer
sudo bash install.sh

# 3. Start the service
sudo systemctl start honey-scraper
```

## Detailed Setup

### 1. Copy Project to LXC Container

From your Windows machine:

```powershell
# Option A: Using SCP (if SSH enabled)
scp -r d:\Projekte\Code\honey_scraper username@lxc-ip:/home/username/

# Option B: Using git (recommended)
# First, commit and push to GitHub from Windows
git add .
git commit -m "Initial commit"
git push

# Then on the LXC container:
git clone https://github.com/angeeinstein/honey_scraper.git
cd honey_scraper
```

### 2. Run the Installer

```bash
# Make the installer executable
chmod +x install.sh

# Run the installer
sudo bash install.sh
```

The installer will:
- ✅ Check for existing installation
- ✅ Install all system dependencies (Python, pip, sqlite3, etc.)
- ✅ Create Python virtual environment
- ✅ Install Python packages
- ✅ Create systemd service
- ✅ Create helper scripts
- ✅ Set proper permissions
- ✅ Test installation

### 3. Start Scraping

**Option A: Run as Service (Recommended for long-running)**
```bash
# Start the service
sudo systemctl start honey-scraper

# Enable auto-start on boot
sudo systemctl enable honey-scraper

# Check status
sudo systemctl status honey-scraper

# View live logs
tail -f scraper.log
```

**Option B: Interactive Mode**
```bash
./run.sh
# Then choose from the menu
```

## Managing the Scraper

### Service Commands

```bash
# Start
sudo systemctl start honey-scraper

# Stop
sudo systemctl stop honey-scraper

# Restart
sudo systemctl restart honey-scraper

# Status
sudo systemctl status honey-scraper

# Enable auto-start on boot
sudo systemctl enable honey-scraper

# Disable auto-start
sudo systemctl disable honey-scraper

# View service logs
sudo journalctl -u honey-scraper -f
```

### Helper Scripts

```bash
# Check status and stats
./status.sh

# Update dependencies
./update.sh

# Run interactively
./run.sh
```

### Monitoring Progress

```bash
# View live scraper logs
tail -f scraper.log

# View error logs
tail -f scraper_error.log

# Check database stats
./status.sh

# Or manually query database
sqlite3 honey_stores.db "SELECT COUNT(*) FROM stores;"
```

## Command Line Options

The scraper supports command-line arguments for automation:

```bash
# Auto-resume mode (for service)
python scraper.py auto

# View stats only
python scraper.py stats

# Scrape specific number of domains
python scraper.py limit=100
```

## File Locations

```
/home/username/honey_scraper/
├── scraper.py              # Main scraper script
├── install.sh              # Installer script
├── run.sh                  # Interactive runner
├── status.sh               # Status checker
├── update.sh               # Update script
├── requirements.txt        # Python dependencies
├── honey_stores.db         # SQLite database (created after first run)
├── scraper.log            # Scraper output logs
├── scraper_error.log      # Error logs
└── venv/                  # Python virtual environment
```

Service file: `/etc/systemd/system/honey-scraper.service`

## Updating

### Update Code

```bash
# If using git
git pull

# Run update script
./update.sh

# Restart service
sudo systemctl restart honey-scraper
```

### Re-run Installer

```bash
sudo bash install.sh
# Choose option 1 to update
```

## Troubleshooting

### Service won't start

```bash
# Check service status
sudo systemctl status honey-scraper

# Check logs
tail -f scraper_error.log

# Check service logs
sudo journalctl -u honey-scraper -n 50
```

### Check if scraper is working

```bash
# Test script directly
./run.sh
# Choose option 1 (test mode)
```

### Database locked

```bash
# Stop the service
sudo systemctl stop honey-scraper

# Check for processes
ps aux | grep scraper

# Kill if needed
pkill -f scraper.py
```

### Disk space issues

```bash
# Check disk usage
df -h

# Check database size
du -h honey_stores.db

# Check log sizes
du -h *.log
```

### Reset everything

```bash
# Stop service
sudo systemctl stop honey-scraper

# Backup database (optional)
cp honey_stores.db honey_stores.db.backup

# Remove database (to start fresh)
rm honey_stores.db

# Restart service
sudo systemctl start honey-scraper
```

## Performance Tuning

### Adjust Request Delay

Edit `scraper.py` and change the delay:

```python
# Default is 0.5 seconds
scraper = HoneyScraper(delay=0.5)

# Faster (use carefully)
scraper = HoneyScraper(delay=0.3)

# Slower (more respectful)
scraper = HoneyScraper(delay=1.0)
```

### Run Multiple Instances

To speed up scraping, you could run multiple instances with different domain ranges, but this requires modifying the script to accept start/end domain indices.

## Backup & Export

### Backup Database

```bash
# Manual backup
cp honey_stores.db backups/honey_stores_$(date +%Y%m%d).db

# Automated daily backup (add to crontab)
0 0 * * * cp /path/to/honey_stores.db /path/to/backups/honey_stores_$(date +\%Y\%m\%d).db
```

### Export Data

```bash
# Run interactively
./run.sh

# Choose option 5 for JSON
# Choose option 6 for CSV

# Or run directly
source venv/bin/activate
python -c "from scraper import HoneyScraper; s = HoneyScraper(); s.export_to_csv('export.csv')"
```

## Security Considerations

The systemd service runs with these security settings:
- ✅ No new privileges
- ✅ Private tmp
- ✅ Protected system directories
- ✅ Protected home directory
- ✅ Only reads/writes to install directory

## Uninstall

```bash
# Stop and disable service
sudo systemctl stop honey-scraper
sudo systemctl disable honey-scraper

# Remove service file
sudo rm /etc/systemd/system/honey-scraper.service
sudo systemctl daemon-reload

# Remove files (backup database first!)
rm -rf /path/to/honey_scraper
```

## Support

Check the following files for more information:
- `README.md` - Project overview
- `USAGE.md` - Usage instructions
- `scraper.py` - Source code with comments

For issues, check:
1. Service status: `sudo systemctl status honey-scraper`
2. Scraper logs: `tail -f scraper.log`
3. Error logs: `tail -f scraper_error.log`
4. System logs: `sudo journalctl -u honey-scraper`
