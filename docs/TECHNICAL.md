# Technical Documentation

## Architecture Overview

### Application Structure

The Prayer Times Visualization is built as a single-page application with the following architecture:

```
prayer-times-graph.html
├── HTML Structure
│   ├── Location Controls (Search Input, DST Toggle, Years Selector)
│   ├── Chart Container (Canvas + Legend)
│   └── Loading States
├── CSS Styling
│   ├── Dark Theme Variables
│   ├── Responsive Layout
│   └── Animation Keyframes
└── JavaScript Application
    ├── PrayerTimesApp Class
    ├── Event Handlers
    └── Chart.js Integration
```

## Core Components

### 1. PrayerTimesApp Class

The main application class manages all functionality:

```javascript
class PrayerTimesApp {
    constructor() {
        this.chart = null;              // Chart.js instance
        this.allData = {};              // Processed prayer time data
        this.rawPrayerData = {};        // Raw API response cache
        this.currentLocation = null;     // Current location object
        this.prayerColors = {...};      // Color mapping for prayers
        this.prayerOrder = [...];       // Display order for prayers
    }
}
```

### 2. Location Services

#### getUserLocation()
Attempts to get user's location via browser geolocation API:
- **Success**: Returns `{latitude, longitude, name: 'Your Location'}`
- **Failure/Denied**: Falls back to Mecca coordinates
- **No Geolocation**: Uses Mecca as default

#### geocodeCity(cityName)
Converts city names to coordinates using Nominatim API:
- **Input**: City name string (e.g., "New York", "London")
- **Output**: `{lat, lon, display_name}` object
- **Rate Limiting**: Includes User-Agent header for API compliance
- **Error Handling**: Returns null on failure

#### searchAndChangeLocation(cityName)
Handles the search workflow:
1. Disables search button and shows "Searching..." state
2. Calls geocodeCity() for coordinates  
3. Updates currentLocation and display text
4. Clears cache and reloads prayer data
5. Resets search button state

### 3. Data Management

#### API Integration
Uses the **Aladhan Prayer Times API** with the following parameters:
- **Base URL**: `https://api.aladhan.com/v1/calendar/`
- **Method**: 2 (Islamic Society of North America)
- **Format**: Monthly calendar data
- **No timezone string** to avoid API errors

#### Caching Strategy
```javascript
// Cache key format: "latitude-longitude-year-month"
const cacheKey = `${latitude}-${longitude}-${year}-${month}`;
this.rawPrayerData[cacheKey] = apiResponse;
```

Benefits:
- **DST Toggle Performance**: No API re-calls during DST changes
- **Rate Limit Compliance**: Reduces API requests
- **Offline Resilience**: Cached data available for chart regeneration

#### Data Processing Pipeline
1. **Raw API Data**: Monthly calendar with prayer times
2. **DST Processing**: Conditional time adjustment for DST toggle
3. **Data Transformation**: Convert to Chart.js compatible format
4. **Dataset Creation**: Generate filled area datasets

### 4. Chart Visualization

#### Dataset Structure
Creates layered datasets for filled areas between prayer times:

```javascript
// Top fill (midnight to Fajr) - Night time
{
    data: fajrData.map(point => ({x: point.x, y: 0})),
    backgroundColor: ishaColor + 'CC',
    fill: '+1'  // Fill to next dataset
}

// Prayer time lines with fills
{
    label: 'Fajr',
    data: fajrData,
    borderColor: fajrColor,
    backgroundColor: fillColor,
    fill: '-1'  // Fill to previous dataset
}

// Bottom fill (Isha to midnight) - Night time
{
    data: ishaData.map(point => ({x: point.x, y: 24})),
    backgroundColor: ishaColor + 'CC', 
    fill: '-1'
}
```

#### Chart Configuration
- **Type**: Line chart with area fills
- **Time Scale**: X-axis using Chart.js time adapter
- **Reversed Y-axis**: 00:00 at top, 24:00 at bottom
- **Interactive Tooltips**: Show exact prayer times on hover
- **Annotations**: Year boundaries and "Today" marker

## Algorithms

### 1. Time Conversion
```javascript
timeToDecimal(timeStr) {
    const [hours, minutes] = timeStr.split(':').map(Number);
    return hours + minutes / 60;  // 14:30 becomes 14.5
}
```

### 2. DST Calculation
Uses US Daylight Saving Time rules:
- **Start**: Second Sunday in March
- **End**: First Sunday in November

```javascript
isDaylightSavingTime(date) {
    const year = date.getFullYear();
    
    // Second Sunday in March
    const march = new Date(year, 2, 1);
    const dstStart = new Date(year, 2, 1 + (7 - march.getDay()) % 7 + 7);
    
    // First Sunday in November  
    const november = new Date(year, 10, 1);
    const dstEnd = new Date(year, 10, 1 + (7 - november.getDay()) % 7);
    
    return date >= dstStart && date < dstEnd;
}
```

### 3. Prayer Time Processing
For DST disabled mode, adjusts times during DST period:
```javascript
if (this.isDaylightSavingTime(currentDate)) {
    let adjustedHours = hours - 1;  // Move back 1 hour
    if (adjustedHours < 0) adjustedHours = 23;  // Handle midnight rollover
}
```

## Performance Optimizations

### 1. Lazy Loading
- Chart creation deferred until data is ready
- Progressive data fetching with loading indicators
- Smooth transitions between loading states

### 2. Rate Limiting
```javascript
// 50ms delay between API requests
await new Promise(resolve => setTimeout(resolve, 50));
```

### 3. Smart Caching
- Cache raw API responses by location and date
- Reprocess cached data for DST toggles
- Prevent unnecessary API calls during UI changes

### 4. Efficient Rendering
- Chart.js optimizations for large datasets
- Minimal DOM manipulation during updates
- CSS transitions for smooth state changes

## Error Handling

### 1. API Failures
```javascript
try {
    const response = await fetch(url);
    const data = await response.json();
    
    if (data.code !== 200 || !data.data) {
        console.error('API Error:', data);
        return null;  // Graceful failure
    }
} catch (error) {
    console.error('Fetch error:', error);
    return null;
}
```

### 2. Geocoding Failures
- Shows user-friendly error messages
- Falls back to previous location on search failure
- Validates input before making API calls

### 3. Chart Errors
- Destroys and recreates chart on major errors
- Provides "Try Again" button for failed loads
- Console logging for debugging

## Browser Compatibility

### Modern Features Used
- **ES6 Classes**: Main application architecture
- **Async/await**: API calls and data processing  
- **CSS Variables**: Theme system
- **Fetch API**: HTTP requests
- **Canvas API**: Chart rendering (via Chart.js)

### Polyfill Requirements for Older Browsers
```html
<!-- For IE11 support, add these polyfills: -->
<script src="https://polyfill.io/v3/polyfill.min.js?features=es6,fetch"></script>
```

## Security Considerations

### 1. API Security
- **No API keys exposed** - using public APIs only
- **Rate limiting** prevents abuse
- **Input validation** before making requests

### 2. XSS Prevention  
- **No innerHTML with user input** 
- **Parameterized API calls** with encodeURIComponent()
- **Content Security Policy** ready

### 3. Privacy
- **Geolocation permission** required for browser location
- **No data storage** beyond session cache
- **No tracking or analytics** by default

## Deployment Options

### 1. Static Hosting
- **GitHub Pages**: Direct HTML file hosting
- **Netlify/Vercel**: Drag-and-drop deployment
- **CDN**: Global distribution for performance

### 2. Web Server
```bash
# Simple Python server
python -m http.server 8000

# Node.js with Express
const express = require('express');
const app = express();
app.use(express.static('.'));
app.listen(8000);
```

### 3. Docker Container
```dockerfile
FROM nginx:alpine
COPY prayer-times-graph.html /usr/share/nginx/html/index.html
EXPOSE 80
```

## Testing Strategy

### 1. Manual Testing Checklist
- [ ] Location search with various cities
- [ ] DST toggle functionality  
- [ ] Years selector (1-5 years)
- [ ] Chart interactions (zoom, pan, tooltips)
- [ ] Mobile responsive design
- [ ] Error states and recovery

### 2. Browser Testing
- [ ] Chrome (latest 3 versions)
- [ ] Firefox (latest 3 versions) 
- [ ] Safari (latest 2 versions)
- [ ] Edge (latest 2 versions)

### 3. Performance Testing
- [ ] Large dataset loading (5 years of data)
- [ ] API rate limiting compliance
- [ ] Memory usage during long sessions
- [ ] Chart rendering performance

## Development Workflow

### 1. Local Development
```bash
# No build process required - edit directly
code prayer-times-graph.html

# Test locally
open prayer-times-graph.html
# or 
python -m http.server 8000
```

### 2. Debugging
- **Console logging**: Extensive logging for API calls and data processing
- **Browser DevTools**: Network tab for API monitoring
- **Chart.js DevTools**: Available as browser extension

### 3. Code Organization
The single-file approach keeps everything together but follows these organization principles:
- **CSS**: Organized by component (variables, layout, typography, etc.)
- **JavaScript**: Class-based with logical method grouping
- **HTML**: Semantic structure with accessibility considerations

## Extreme Latitude Behavior

### Mathematical Patterns in Prayer Times

The application showcases stunning mathematical patterns at extreme latitudes that demonstrate the intersection of Islamic jurisprudence with astronomical science:

#### Arctic Locations (e.g., Anchorage, Alaska - 61°N)
```javascript
// Summer (June 21st - Solar maximum)
fajr: ~02:00,    // Astronomical dawn very early
isha: ~23:30     // Astronomical dusk very late

// Winter (December 21st - Solar minimum)  
fajr: ~09:00,    // Astronomical dawn very late
isha: ~16:00     // Astronomical dusk very early
```

**Visual Pattern**: Sharp "mountain peak" sine waves with dramatic seasonal swings

#### Antarctic Locations (e.g., Ushuaia, Argentina - 54°S)
```javascript
// Summer (December 21st - Solar maximum in Southern Hemisphere)
fajr: ~04:00,    // Dawn moderately early
isha: ~23:00     // Dusk very late

// Winter (June 21st - Solar minimum in Southern Hemisphere)
fajr: ~08:00,    // Dawn moderately late  
isha: ~17:00     // Dusk early
```

**Visual Pattern**: Gentle, inverted sine waves showing hemispheric seasonal opposition

### Scientific Explanation

#### 1. Earth's Axial Tilt (23.5°)
- Creates seasonal variation in daylight hours
- Higher latitudes experience more extreme seasonal changes
- Effect amplifies dramatically above 60° latitude

#### 2. Astronomical Twilight Calculations
Islamic prayer times use **astronomical dawn/dusk** (sun 18° below horizon):
```javascript
// Prayer time calculation follows precise astronomical rules:
fajr: astronomical_dawn,     // Sun -18° below horizon (morning)
isha: astronomical_dusk      // Sun -18° below horizon (evening)  
```

#### 3. Hemispheric Opposition
- **Northern summer = Southern winter**
- **Arctic white nights = Antarctic polar darkness**
- Prayer time curves show perfect mathematical inversion

### Code Implementation for Extreme Latitudes

The application handles extreme latitudes gracefully:
```javascript
// No special case handling needed - Islamic calculation methods
// naturally adapt to any latitude using astronomical formulas
const sunPosition = calculateSolarPosition(latitude, longitude, date);
const twilightTime = calculateAstronomicalTwilight(sunPosition);
```

#### Edge Cases Handled:
- **Polar day/night**: When sun never rises/sets
- **Rapid transitions**: Quick seasonal changes in spring/fall
- **Precision**: Maintains accuracy even at 80°+ latitudes

### Educational Value

These extreme patterns demonstrate:
1. **Mathematical beauty in Islamic jurisprudence**
2. **Universal applicability** of prayer time calculations
3. **Harmony between religion and natural science**  
4. **Visual storytelling** through data visualization

## Future Enhancements

### 1. Technical Improvements
- **Web Workers**: Background API data fetching
- **Service Worker**: Offline functionality and caching
- **WebAssembly**: Faster prayer time calculations
- **IndexedDB**: Persistent local storage

### 2. Feature Additions  
- **Multiple locations**: Compare prayer times across cities
- **Export formats**: iCal, CSV, JSON export options  
- **Themes**: Light mode and custom color schemes
- **Languages**: RTL support and translations

### 3. Mobile Optimizations
- **Touch gestures**: Swipe navigation for years
- **PWA**: Progressive Web App with home screen installation
- **Push notifications**: Prayer time reminders
- **Offline mode**: Cached data for offline viewing