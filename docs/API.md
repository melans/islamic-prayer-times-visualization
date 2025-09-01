# API Documentation

## Overview

This application integrates with external APIs to provide accurate prayer times and location services. This document details the API usage, request/response formats, and integration patterns.

## Prayer Times API - Aladhan

### Base URL
```
https://api.aladhan.com/v1/
```

### Endpoints Used

#### 1. Calendar Endpoint
**GET** `/calendar/{year}/{month}`

Retrieves prayer times for an entire month.

**Parameters:**
- `year` (integer, required): 4-digit year (e.g., 2024)
- `month` (integer, required): Month number 1-12
- `latitude` (float, required): Geographic latitude (-90 to 90)
- `longitude` (float, required): Geographic longitude (-180 to 180) 
- `method` (integer, optional): Calculation method (default: 2)

**Example Request:**
```
GET https://api.aladhan.com/v1/calendar/2024/1?latitude=40.7128&longitude=-74.0060&method=2
```

**Response Format:**
```json
{
  "code": 200,
  "status": "OK",
  "data": [
    {
      "timings": {
        "Fajr": "06:15",
        "Sunrise": "07:20", 
        "Dhuhr": "12:09",
        "Asr": "14:23",
        "Sunset": "16:58",
        "Maghrib": "16:58",
        "Isha": "18:23"
      },
      "date": {
        "readable": "01 Jan 2024",
        "timestamp": "1704067200",
        "gregorian": {
          "date": "01-01-2024",
          "format": "DD-MM-YYYY",
          "year": "2024",
          "month": {
            "number": 1,
            "en": "January"
          }
        }
      }
    }
  ]
}
```

### Calculation Methods

The `method` parameter determines which Islamic organization's calculation rules to use:

| Method | Organization | Region |
|--------|-------------|---------|
| 1 | University of Islamic Sciences, Karachi | Pakistan, India, Bangladesh |
| 2 | Islamic Society of North America (ISNA) | North America (Default) |
| 3 | Muslim World League | Europe, Far East, parts of USA |
| 4 | Umm Al-Qura University, Makkah | Saudi Arabia |
| 5 | Egyptian General Authority of Survey | Egypt, Syria, Iraq, Lebanon, Malaysia |
| 7 | Institute of Geophysics, University of Tehran | Iran, Afghanistan, parts of Central Asia |
| 8 | Gulf Region | Kuwait, Qatar, Bahrain, Oman, UAE |
| 9 | Kuwait | Kuwait |
| 10 | Qatar | Qatar |
| 11 | Majlis Ugama Islam Singapura, Singapore | Singapore |
| 12 | Union Organization Islamic de France | France |
| 13 | Diyanet İşleri Başkanlığı, Turkey | Turkey |
| 14 | Spiritual Administration of Muslims of Russia | Russia |

### Rate Limiting

**Current Implementation:**
- 50ms delay between consecutive requests
- No API key required
- No explicit rate limits documented by Aladhan

**Best Practices:**
- Cache responses to minimize requests
- Use batch requests when possible  
- Handle 429 status codes gracefully
- Include User-Agent header

**Error Handling:**
```javascript
if (data.code !== 200 || !data.data) {
    console.error('API Error:', data);
    // Handle error appropriately
}
```

## Geocoding API - Nominatim (OpenStreetMap)

### Base URL
```
https://nominatim.openstreetmap.org/
```

### Endpoints Used

#### 1. Search Endpoint
**GET** `/search`

Converts place names to geographic coordinates.

**Parameters:**
- `q` (string, required): Search query (city name, address, etc.)
- `format` (string, required): Response format (`json`, `xml`, `jsonv2`)
- `limit` (integer, optional): Maximum number of results (default: 10)
- `countrycodes` (string, optional): Restrict to specific countries
- `addressdetails` (integer, optional): Include address breakdown (0 or 1)

**Example Request:**
```
GET https://nominatim.openstreetmap.org/search?q=New%20York&format=json&limit=1
```

**Response Format:**
```json
[
  {
    "place_id": 297485335,
    "licence": "Data © OpenStreetMap contributors, ODbL 1.0.",
    "osm_type": "relation",
    "osm_id": 175905,
    "boundingbox": ["40.4773991", "40.9175771", "-74.2590879", "-73.7002721"],
    "lat": "40.7127281",
    "lon": "-74.0060152",
    "display_name": "New York, United States",
    "class": "place",
    "type": "city",
    "importance": 1.0394608079849
  }
]
```

### Usage Policies

**Attribution Required:**
- Must credit OpenStreetMap contributors
- Include attribution in documentation/about sections

**Rate Limits:**
- Maximum 1 request per second
- No more than 1000 requests per day per IP
- Include descriptive User-Agent header

**Required Headers:**
```javascript
headers: {
    'User-Agent': 'PrayerTimesApp/1.0 (contact@example.com)'
}
```

### Error Handling

**Common Error Scenarios:**
- Empty results array: Location not found
- Network errors: Connection issues
- Rate limit exceeded: HTTP 429 status

**Implementation:**
```javascript
try {
    const response = await fetch(url, {
        headers: {
            'User-Agent': 'PrayerTimesApp/1.0'
        }
    });
    
    if (!response.ok) {
        throw new Error(`HTTP ${response.status}: ${response.statusText}`);
    }
    
    const data = await response.json();
    
    if (!data || data.length === 0) {
        return null; // No results found
    }
    
    return data[0]; // Return first result
} catch (error) {
    console.error('Geocoding error:', error);
    return null;
}
```

## Application API Integration

### Data Flow Architecture

```
User Input (City Name)
        ↓
Nominatim Geocoding API
        ↓
Coordinates (lat, lon)
        ↓
Aladhan Prayer Times API  
        ↓
Raw Prayer Time Data
        ↓
Processing & Caching
        ↓
Chart Visualization
```

### Caching Strategy

**Cache Structure:**
```javascript
{
    rawPrayerData: {
        "40.7128--74.0060-2024-1": [...], // lat-lon-year-month
        "40.7128--74.0060-2024-2": [...],
        // ... more cached responses
    }
}
```

**Cache Key Format:**
```javascript
const cacheKey = `${latitude}-${longitude}-${year}-${month}`;
```

**Cache Benefits:**
- Reduces API calls during DST toggles
- Improves performance for repeated requests
- Enables offline functionality for cached data
- Respects API rate limits

### Error Recovery Patterns

**1. Graceful Degradation**
```javascript
// Try geocoding, fall back to default location
const location = await this.geocodeCity(cityName) || defaultLocation;
```

**2. Retry Logic**
```javascript
let retries = 3;
while (retries > 0) {
    try {
        const result = await apiCall();
        return result;
    } catch (error) {
        retries--;
        if (retries === 0) throw error;
        await sleep(1000 * (4 - retries)); // Exponential backoff
    }
}
```

**3. User Feedback**
```javascript
// Show loading states
searchBtn.disabled = true;
searchBtn.textContent = 'Searching...';

// Provide error messages
alert(`Could not find location "${cityName}". Please try a different city name.`);
```

### Performance Optimizations

**1. Request Batching**
```javascript
// Load multiple months concurrently (with rate limiting)
const promises = months.map((month, index) => 
    delay(index * 50).then(() => fetchPrayerTimes(year, month))
);
const results = await Promise.all(promises);
```

**2. Smart Loading**
```javascript
// Only fetch data that's not already cached
if (!this.rawPrayerData[cacheKey]) {
    const data = await this.fetchPrayerTimes(lat, lon, year, month);
    this.rawPrayerData[cacheKey] = data;
}
```

**3. Progressive Enhancement**
- Show loading indicators during API calls
- Enable interactions as data becomes available
- Cache processed data for instant DST toggles

## Testing API Integration

### Unit Testing

**Mock API Responses:**
```javascript
const mockPrayerTimesResponse = {
    code: 200,
    status: "OK", 
    data: [
        {
            timings: {
                Fajr: "05:30",
                Sunrise: "07:00",
                // ... other prayers
            },
            date: {
                gregorian: {
                    date: "01-01-2024"
                }
            }
        }
    ]
};

// Mock fetch for testing
global.fetch = jest.fn(() =>
    Promise.resolve({
        ok: true,
        json: () => Promise.resolve(mockPrayerTimesResponse)
    })
);
```

### Integration Testing

**Test Scenarios:**
1. **Valid City Search**: "New York" → Should return NYC coordinates
2. **Invalid City Search**: "XYZ123" → Should return null gracefully  
3. **Network Error**: Offline → Should show error message
4. **Rate Limiting**: Rapid requests → Should handle delays properly
5. **Malformed Response**: Invalid JSON → Should not crash app

### API Monitoring

**Health Checks:**
```javascript
async function checkAPIHealth() {
    try {
        // Test Aladhan API
        const prayerResponse = await fetch('https://api.aladhan.com/v1/calendar/2024/1?latitude=21.3891&longitude=39.8579&method=2');
        const aladhanHealthy = prayerResponse.ok;
        
        // Test Nominatim API  
        const geoResponse = await fetch('https://nominatim.openstreetmap.org/search?q=London&format=json&limit=1');
        const nominatimHealthy = geoResponse.ok;
        
        return { aladhan: aladhanHealthy, nominatim: nominatimHealthy };
    } catch (error) {
        return { aladhan: false, nominatim: false };
    }
}
```

**Usage Metrics:**
- Track API response times
- Monitor error rates by endpoint
- Log rate limit encounters
- Measure cache hit rates

## Security Considerations

### API Keys
- **No API keys required** for current integrations
- All APIs used are public and free
- No sensitive authentication data exposed

### Input Validation
```javascript
function validateCityName(input) {
    // Basic validation for geocoding input
    if (!input || typeof input !== 'string') return false;
    if (input.trim().length === 0) return false;
    if (input.length > 200) return false; // Reasonable length limit
    
    // Allow letters, numbers, spaces, common punctuation
    const validPattern = /^[a-zA-Z0-9\s\-,.'()]+$/;
    return validPattern.test(input);
}
```

### CORS Handling
```javascript
// All APIs support CORS for browser requests
// No proxy or server-side calls required
const response = await fetch(url, {
    headers: {
        'User-Agent': 'PrayerTimesApp/1.0'
    }
    // CORS headers handled automatically
});
```

### Privacy Protection
- **No personal data stored** beyond session
- **Geolocation permission** required for browser location
- **No tracking** of user searches or locations
- **Local caching only** - no data sent to third parties