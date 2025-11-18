# WSJT-X ADIF Log Visualizer & Mapper

## A web-based visual logger and analytics tool for amateur radio operators using WSJT-X. Plot your QSOs on an interactive map, monitor your log in real-time and upload to QRZ.com. Track progress toward WAC, WAZ and WPX awards.
<img width="1912" height="964" alt="Screenshot 2025-11-17 184517" src="https://github.com/user-attachments/assets/00315547-8db6-463e-bb88-9f94440f8061" />

### The Core Functionality
- **Interactive Map Visualization**: Plot all your QSO on a maidenhead grid map with color-coded markers by band
- **Real-time Log Monitoring**: Automatically reload and update the map as you make new contacts.
- **Maidenhead Grid Overlay**: Toggle a grid overlay to visualize grid squares
- **Contact Path Visualization**: Show animated paths to your last 5 contacts
- **Multiple Map Styles**: Choose from OpenStreetMap default, CartoDB Positron, or CartoDB Voyager
<img width="1880" height="931" alt="Screenshot 2025-11-17 184654" src="https://github.com/user-attachments/assets/0b378d54-d141-44a5-9027-361525d03017" />

### ADIF Log Management
- **Comprehensive Log Viewer**: Browse and filter your entire log
- **Advanced Filtering**: Filter by callsign, band, mode, and date range
- **CSV Export**: Export filtered logs to CSV format
- **QRZ.com Integration**: Upload your log directly to QRZ.com (requires API key)
<img width="1881" height="928" alt="Screenshot 2025-11-17 184804" src="https://github.com/user-attachments/assets/c02527c2-8824-4d0a-b775-ee5068919d93" />

### View Analytics & Progress toward WAC, WAZ and WPX Awards
- **WAC Progress**: Track Worked All Continents (6 continents)
- **WAZ Progress**: Track Worked All Zones (40 CQ zones)
- **WPX Progress**: Track unique prefixes worked
- **Visual Charts**: See your activity by continent, CQ zone, country, and band
- **Statistics Dashboard**: View total QSOs, FT8/FT4 counts, unique grids, max distance, and more

### Requirements
Built and Tested in Window 11. If you're using MacOS or Linux, it may not work as expected...or it might
- Your browser must allow Javascript to run. There are no ads, trackers, cookies, etc. Chrome, Firefox, Edge, Safari work fine
- You'll use Python's built-in server, so be sure to install Python 3.x.x
- WSJT-X's wsjtx_log.adi ADIF log file is used by default. You can choose another as you desire.
- Optional: QRZ.com API key is required for log uploads

## Installation

### 1. Download Files from this Github repository

Clone or download this repository:
```bash
git clone https://github.com/yourusername/wsjtx-log-mapper.git
```
Or go to the repository and download the wsjtx_adif_visualizer.zip file to your PC

### 2. Required Files

Ensure you have these files in your directory:
- `index.html` - Main application
- `config.json` - Your configuration file
- `cty.json` - Country/DXCC entity database
- `wsjtx_log.adi` - Your WSJT-X log file (or configured name)
- `start_servers.py` - Starts 2 Python minimal http servers on ports 8000 (web page server) and 8001 (CORS proxy for QRZ.com)

### 3. Edit config.json Configuration File

Edit the downloaded `config.json` file with your station details: 

```json
{
  "callsign": "yourcall",
  "qth": {
    "latitude": 00.0000,
    "longitude": -00.0000,
    "grid": "<yourgrid>"
  },
  "logFile": "wsjtx_log.adi",
  "refreshInterval": 3000,
  "defaultMapStyle": "dark",
  "display": {
    "title": "WSJT-X ADIF Log Mapper-Visualizer",
    "callsignFontSize": "24pt",
    "titleFontSize": "18pt"
  },
  "autoStart": {
    "realtimeMonitoring": true
  },
  "qrz": {
    "apiKey": "0000-0000-0000-0000",
    "autoUpload": false,
    "uploadInterval": 300000
  }
}
```

**config.json values:**

| Option | Description | Example |
|--------|-------------|---------|
| `callsign` | Your callsign | "W3ZAZ" |
| `qth.latitude` | Your station latitude | 40.01235 |
| `qth.longitude` | Your station longitude | -72.01235 |
| `qth.grid` | Your Maidenhead grid square | "FN20qn" |
| `logFile` | ADIF log filename | "wsjtx_log.adi" |
| `refreshInterval` | Real-time refresh interval (ms) | 3000 |
| `autoStart.realtimeMonitoring` | Start monitoring on load | true |
| `qrz.apiKey` | Your QRZ.com API key | "0000-0000-0000-0000" |

### 4. Copy ALL the files from the .zip to c:\users\<your_user_name>\AppData\local\WSJT-X
* Note that if you can't find this directory, the AppData folder may be hidden.

Copy or symlink your WSJT-X log file to the application directory:

**Windows:**
```cmd
copy "%LOCALAPPDATA%\WSJT-X\wsjtx_log.adi" .
```

**Linux/Mac:**
```bash
ln -s ~/.local/share/WSJT-X/wsjtx_log.adi .
```

Or configure WSJT-X to log directly to this directory.

### 5. Start the Web Server

The application requires a local web server due to CORS restrictions.

**Python 3:**
```bash
python -m http.server 8000
```

**Python 2:**
```bash
python -m SimpleHTTPServer 8000
```

**Node.js:**
```bash
npx http-server -p 8000
```

### 6. Open in Browser

Navigate to: `http://localhost:8000`

## QRZ.com Upload Setup

To enable QRZ.com log uploads, you need to set up a CORS proxy.

### 1. Get Your QRZ API Key

1. Log in to QRZ.com
2. Go to Settings → Logbook
3. Find your API key

### 2. Set Up CORS Proxy

Create `qrz_proxy.py`:

```python
#!/usr/bin/env python3
from http.server import HTTPServer, BaseHTTPRequestHandler
import urllib.request
import urllib.parse

class CORSRequestHandler(BaseHTTPRequestHandler):
    def do_OPTIONS(self):
        self.send_response(200)
        self.send_header('Access-Control-Allow-Origin', '*')
        self.send_header('Access-Control-Allow-Methods', 'POST, OPTIONS')
        self.send_header('Access-Control-Allow-Headers', 'Content-Type')
        self.end_headers()

    def do_POST(self):
        content_length = int(self.headers['Content-Length'])
        post_data = self.rfile.read(content_length)
        
        req = urllib.request.Request(
            'https://logbook.qrz.com/api',
            data=post_data,
            headers={'Content-Type': self.headers['Content-Type']}
        )
        
        try:
            response = urllib.request.urlopen(req)
            result = response.read()
            
            self.send_response(200)
            self.send_header('Access-Control-Allow-Origin', '*')
            self.send_header('Content-Type', 'text/plain')
            self.end_headers()
            self.wfile.write(result)
        except Exception as e:
            self.send_response(500)
            self.end_headers()
            self.wfile.write(str(e).encode())

if __name__ == '__main__':
    server = HTTPServer(('localhost', 8001), CORSRequestHandler)
    print('QRZ CORS proxy running on port 8001...')
    server.serve_forever()
```

### 3. Run the Proxy

```bash
python3 qrz_proxy.py
```

### 4. Add API Key to Config

Update your `config.json`:
```json
{
  "qrz": {
    "apiKey": "your-qrz-api-key-here",
    "autoUpload": false,
    "uploadInterval": 300000
  }
}
```

## Usage

### Real-time Monitoring

1. Check "Real-time Log Monitoring" checkbox
2. The map updates automatically as you log contacts
3. Refresh interval is shown (default: 5.0s)
4. Status shows "● LIVE - Monitoring..."

### Manual Upload

1. Click "Upload & Plot" button
2. Select your ADIF file
3. Map plots all contacts immediately

### Viewing Your Log

1. Click "View Log" button
2. Use filters to search:
   - **Callsign**: Search for specific stations
   - **Band**: Filter by band
   - **Mode**: Filter by mode (FT8, FT4, etc.)
   - **Date Range**: Filter by date
3. Click "Export CSV" to download filtered data
4. Click column headers to sort (if JavaScript sorting added)

### Map Features

**Map Styles:**
- Toggle the "🗺️ Map Features" panel
- Choose from 3 map styles
- Enable/disable Maidenhead grid overlay
- Show/hide last 5 contact paths

**Interacting with Markers:**
- **Hover**: See grid square and latest contact
- **Click**: View detailed contact list for that grid
- **Color**: Indicates primary band used

### Analytics

1. Click "📊 Analytics" button
2. View award progress:
   - **WAC**: Continents worked (6 total)
   - **WAZ**: CQ Zones worked (40 total)
   - **WPX**: Unique prefixes worked
3. Explore charts:
   - Contacts by continent
   - Contacts by CQ zone
   - Top 10 countries
   - Band distribution

### QRZ.com Upload

1. Ensure QRZ API key is configured
2. Ensure CORS proxy is running
3. Click "Upload to QRZ" button
4. View upload history and status

## Band Color Coding

| Band | Color |
|------|-------|
| 160m | Dark Red |
| 80m | Orange Red |
| 60m | Dark Orange |
| 40m | Gold |
| 30m | Yellow Green |
| 20m | Green |
| 17m | Cyan |
| 15m | Royal Blue |
| 12m | Blue Violet |
| 10m | Deep Pink |
| 6m | Hot Pink |
| 2m | Plum |
| 70cm | Orchid |
| 23cm | Purple |

## Troubleshooting

### Map Doesn't Load
- Ensure web server is running
- Check browser console for errors (F12)
- Verify `config.json` is valid JSON

### Real-time Monitoring Not Working
- Verify log file path in `config.json`
- Check that log file is readable
- Ensure WSJT-X is writing to the correct file

### No Contacts Showing
- Verify ADIF file contains valid grid squares
- Check that contacts have `<gridsquare>` tags
- WSJT-X must be logging grid squares

### QRZ Upload Fails
- Verify API key is correct
- Ensure CORS proxy is running on port 8001
- Check QRZ.com status

### Country Lookups Not Working
- Ensure `cty.json` exists
- Check browser console for loading errors
- Re-download CTY.dat if needed

## Technical Details

### Dependencies

**JavaScript Libraries (CDN):**
- Leaflet 1.9.4 - Interactive maps
- Chart.js 3.9.1 - Analytics charts

**Data Files:**
- `cty.json` - DXCC entity database (converted from CTY.dat)

### Browser Storage

This application does **not** use localStorage or sessionStorage. All data is:
- Loaded from ADIF file
- Kept in memory during session
- Re-loaded on page refresh

### ADIF Parsing

The application parses these ADIF fields:
- `CALL` - Callsign
- `GRIDSQUARE` - Maidenhead grid
- `QSO_DATE` - Date (YYYYMMDD)
- `TIME_ON` - Time (HHMM or HHMMSS)
- `BAND` - Band
- `MODE` - Mode
- `SUBMODE` - Submode (FT8, FT4, etc.)
- `RST_SENT` - Signal report sent
- `RST_RCVD` - Signal report received

### Grid Conversion

Maidenhead grid squares are converted to lat/lon:
- 4-character grids: center of square
- 6-character grids: center of subsquare
- Accuracy sufficient for mapping

## Contributing

Contributions welcome! Please:
1. Fork the repository
2. Create a feature branch
3. Submit a pull request

## License

This project is released under the MIT License.

## Credits

- **CTY.dat**: Country lookup data from Jim Reisert AD1C
- **Leaflet**: Open-source mapping library
- **Chart.js**: Charting library
- **OpenStreetMap**: Map tile provider
- **CartoDB**: Additional map styles

## Support

For issues or questions:
- Open an issue on GitHub
- Contact: [Your Contact Info]

## Changelog

### Version 1.0.0 (2024)
- Initial release
- Real-time monitoring
- Interactive mapping
- Log viewer with filtering
- QRZ.com upload
- Award tracking (WAC, WAZ, WPX)
- Analytics dashboard

---

**73 and Enjoy WSJT-X! 📻**
