# WSJT-X ADIF Log Visualizer & Mapper

## A browser-based visual WSJT-X log visualizer, mapper and analytics tool for amateur radio operators. See your QSOs on an interactive map, monitor your log in real-time and upload to QRZ.com (with API). Track progress toward WAC, WAZ and WPX awards.
<img width="1912" height="964" alt="Screenshot 2025-11-17 184517" src="https://github.com/user-attachments/assets/00315547-8db6-463e-bb88-9f94440f8061" />

### The Core Functionality
- **Interactive Map Visualization**: Plot all your QSO on a maidenhead grid map with color-coded markers by band
- **Real-time Log Monitoring**: Automatically reload and update the map as you make new contacts.
- **Maidenhead Grid Overlay**: Toggle a grid overlay to visualize grid squares
- **Contact Path Visualization**: Show animated paths to your last 5 contacts
- **Multiple Map Styles**: Choose from OpenStreetMap default, CartoDB Positron, or CartoDB Voyager
<img width="1880" height="931" alt="Screenshot 2025-11-17 184654" src="https://github.com/user-attachments/assets/0b378d54-d141-44a5-9027-361525d03017" />

### ADIF Log Map Visualization
- **Comprehensive Log Viewer**: Browse and filter your entire WSJT-X log
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

# So How does this all work?

WSJT-X ADIF Log Visualizer & Mapper is a browser app. It is single HTML (index.html) with a bunch of JavaScript in it. All open 
source and easy to understand. The web app is served to your browser via a simple Python web server. All you do is open a 
Windows CMD prompt as Administrator, run one simple Python script then point your browser to http://localhost:8000.

Note that this app is designed to work locally on a Windows 11 (or 10) PC. It reads local WSJT-X log files. It will work as a
web-based app on a remote server but the real-time monitoring feature will be disabled. You can display a static version of the 
app if you manually upload your wsjtx_log.adi file to the root of your remote web server, but it won't automatically update
when you add new QSO entries to your log.

Because the Python http.server is local to ports 8000 and 8001, it won't interfere with any SSH, FTP, Telnet or other servers 
you may have running on your local PC. In fact, as long as you keep the CMD window you started the server in open, you'll have
continued access to WSJT-X ADIF Log Visualizer & Mapper instance...so remember to keep that CMD window open, and minimized.

# Simple and Easy Installation

There is nothing to install per se. Just download the wsjtx-log-mapper.zip file from this Github release repository, un-zip the
files within to a directory as described below, start the Python http.server instance and you are up and running. You can run 
WSJT-X ADIF Log Visualizer & Mapper either directly from WSJT-X's user directory - recommended - OR in a separate directory of 
your choice. For instance, C:\wsjtx-webserver.

## 1. Install Python 3.x first if you haven't done so already. You can download from 

                                        https://www.python.org/downloads/windows/

## 1A. Download the wsjtx-log-mapper.zip file from this Github repository directly to your PC:

- Option 1 (Recommended): Download and un-zip wsjtx-log-mapper.zip into the WSJT-X local user directory: 
                                     
                                      C:\Users\<your_user_name>\AppData\Local\WSJT-X
                                     
The advantage here is that all the files are contained in the same directory with required wsjtx_log.adi log file. Nice and tidy.
Putting these files in your local user WSJT-X directory has no impact on the operation of WSJT-X.
* Note that if you can't find this directory, the AppData folder may be hidden. Use your Explorer app to unhide it.

- Option 2: Download and un-zip the wsjtx-log-mapper.zip into a separate directory such as C:\wsjtx-webserver. This keeps all the
files separate from your WSJT-X files but requires a symlink to C:\Users\<your_user_name>\AppData\Local\WSJT-X\wsjtx_log.adi 
be created in that separate directory so the wsjtx_log.adi file can be found. To do that, open a CMD window as Administrator, CD 
to the desired directory where you un-zipped the download and issue the command:

                    mklink "C:\Users\AppData\Local\WSJT-X\wsjtx_log.adi" "C:\wsjtx-webserver\wsjtx_log.adi"

### 2. Un-Zip the wsjtx-log-mapper.zip to where you want to run it from and ensure you have these files in your directory:

- `index.html` - Main application
- `config.json` - Your configuration file
- `cty.json` - Country/DXCC entity database
- `wsjtx_log.adi` - Your WSJT-X log file (or configured name)
- `start_servers.py` - Starts 2 Python minimal http servers on ports 8000 (web page server) and 8001 (CORS proxy for QRZ.com)
- `favicon.ico` - The webpage's little icon

### 3. Edit config.json Configuration File with Notepad (Notepad++ is better!)

Edit the downloaded `config.json` file with your station details: Dont't forget to put in your QRZ.com logbook API key! If
you don't have one, the app will QRZ Upload button will turn grey and will not function.

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

### 4. Start the Python Web Server.

The application uses a very minimal and simple local Python web server that serves the WSJT-ADIF-Log-Visualizera webpage on port 8000
Simply open a CMD windows as Admininstrator, navigate to where you un-zipped wsjtx-log-mapper.zip and issue the command:  

                                          python start_servers.py

### 6. Open a browser window or tab and navigate to: 

                                          http://localhost:8000

If all goes as expected (it's really quite simple) you should see the main page -
<img width="1912" height="964" alt="Screenshot 2025-11-17 184517" src="https://github.com/user-attachments/assets/00315547-8db6-463e-bb88-9f94440f8061" />


# How to Use WSJT-X ADIF Log Visualizer & Mapper 
(it's almost self-explanatory!)

### Real-time Monitoring

1. Check "Real-time Log Monitoring" checkbox
2. The map updates automatically as you log contacts
3. Refresh interval is shown (default: 3.0s)
4. Status shows "● LIVE - Monitoring..."
5. The .adi file refresh time is set in the 'config.json' file

### Manual Upload

1. Click "Upload & Plot" button
2. Select your ADIF file
3. The Map updates and cd c:\usersplots all contacts immediately

### Viewing Your Log

1. Click "View Log" button
2. Use filters to search:
   - **Callsign**: Search for specific stations - Click on a callsign to see bring up QRZ page in a new tab
   - **Band**: Filter by band
   - **Mode**: Filter by mode (FT8, FT4, or whatever WSJT-X mode you used)
   - **Date Range**: Filter by date
3. Click "Export CSV" to download filtered data
<img width="1838" height="935" alt="image" src="https://github.com/user-attachments/assets/b20b1f23-cbf3-4adc-a2cb-90ea79da4463" />

### Map Features

**Map Styles:**
- Expand the "🗺️ Map Features" panel (If it is minimized)
- Choose from 3 map styles - CartoDB Positron (Light) is pretty minimal!
- Enable/disable Maidenhead grid overlay - You may need to toggle the checkbox
- Show/hide last 5 contact paths - You may need to to toggle the checkbox
<img width="1871" height="969" alt="image" src="https://github.com/user-attachments/assets/18e8ad0c-6777-4c88-948f-744c013a7132" />

**Interacting with Markers:**
- **Hover**: See grid square and latest contact
- **Click**: View scrollable detailed contact list for that grid. Click on a callsign to see bring up QRZ page in a new tab
- **Color**: Indicates primary band used
<img width="1874" height="972" alt="image" src="https://github.com/user-attachments/assets/a568168a-cba3-47d5-8c4e-17fbc32fb7d3" />

### Analytics

1. Click "📊 Analytics" button
2. View award progress:
   - **WAC**: Continents worked (6 total)
   - **WAZ**: CQ Zones worked (40 total)
   - **WPX**: Unique prefixes worked
<img width="1826" height="353" alt="image" src="https://github.com/user-attachments/assets/38e0c6a7-78ed-4f01-8e15-ad7a8d1756fd" />

3. Explore charts:
   - Contacts by continent
   - Contacts by CQ zone
   - Top 10 countries
   - Band distribution
<img width="1802" height="789" alt="image" src="https://github.com/user-attachments/assets/0785e41a-d44f-4bfe-896d-4920575ede1a" />

### QRZ.com Upload

1. Ensure QRZ API key is configured in `config.json`
2. Ensure CORS proxy is running - it starts automatically when you run `python start_server.py`
3. Click "Upload to QRZ" button - an error message usually indicates that dupes were found and not added. Just a warning.
4. View upload history and status
<img width="1872" height="967" alt="image" src="https://github.com/user-attachments/assets/0e260693-6476-4371-9086-052457e52e54" />

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

# Troubleshooting

### Map Doesn't Load
- Ensure web server is running - it starts when you run `python start_servers.py`
- Check browser console for errors (F12) - Maybe you're blocking OpenStreetMap?
- Verify `config.json` is valid JSON. Be careful when you edit this file!

### Real-time Monitoring Not Working
- Verify log file path in `config.json` - Normally the wsjtx_log.adi is in the same directory
- Check that log file is readable - normally it is
- Ensure WSJT-X is writing to the correct file - Especially if you customized your WSJT-X instance

### No Contacts Showing
- Verify ADIF file contains valid grid squares
- Check that contacts have `<gridsquare>` tags
- WSJT-X must be logging grid squares

### QRZ Upload Fails
- Verify YOUR API key is correct
- Ensure CORS proxy is running on port 8001 - It normally starts when you run `python start_servers.py'
- Check QRZ.com status - Do you have an active account? You need an API

### Country Lookups Not Working
- Ensure `cty.json` exists - this comes with the download files.
- Check browser console for loading errors - Try Firefox, Chrome, Opera or Edge
- Re-download CTY.dat if needed from the WSJT-X Sourceforge website

# Technical Details
This was a tricky project. Lot's of ins, outs and what-have-yous (to quote Jeff Lebowski). I would be less than forthcoming if I
didn't mention that I turned to Anthropic's Claude.ai Sonnet 4.5 for assistance. Getting things to work with the CORS issue and 
coding efficiently are important and Claude can be a lifesaver. Don't be ashamed to vibe coding.

### Dependencies

**JavaScript Libraries (CDN):**
- Leaflet 1.9.4 - Interactive maps - That is how we get the maps and themes from OpenStreetMap. 
- Chart.js 3.9.1 - Analytics charts - Looks nice don't they!

**Data Files:**
- `cty.json` - DXCC entity database (converted from CTY.dat with a Python script)

### Browser Storage

This application does **not** use localStorage, sessionStorage, cookies or any intrusive nonsense. All data is:
- Loaded from ADIF file
- Kept in memory during session and then released
- Re-loaded on page refresh.

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
- 6-character grids: center of subsquare. But we don't see the label because of room
- Accuracy sufficient for mapping a big old dot in the middle of a grid

## Contributing

Contributions welcome! Please:
1. Fork the repository - Have fun.
2. Create a feature branch - Improve it!
3. Submit a pull request

## License

This project is released under the MIT License.

## Credits

- **CTY.dat**: Country lookup data from Jim Reisert AD1C
- **Leaflet**: Open-source mapping library
- **Chart.js**: Charting library
- **OpenStreetMap**: Map tile provider - And you DON't need an API account. Just don't abuse it
- **CartoDB**: Additional map styles

## Support

For issues or questions:
- Open an issue on here on my GitHub page

## Changelog

### Version 1.0.0 (2025) - probably the only version
- Initial release
- Real-time monitoring
- Interactive mapping
- Log viewer with filtering
- QRZ.com upload - with CORS resolved!
- Award tracking (WAC, WAZ, WPX) - Can't figue out how to do more than that
- Analytics dashboard. For statistics nerds like me

---

**73 and Enjoy - ErnieTech**
