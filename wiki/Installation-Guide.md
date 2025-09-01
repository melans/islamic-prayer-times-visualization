# Installation Guide

## 🚀 Quick Start Options

### Option 1: Use the Live Demo (Recommended)
No installation needed! Simply visit:
**https://sites.google.com/view/muslimprayertimesvisualization/**

### Option 2: Download and Run Locally

#### Step 1: Download the File
```bash
# Using wget
wget https://raw.githubusercontent.com/melans/islamic-prayer-times-visualization/main/prayer-times-graph.html

# Or using curl
curl -O https://raw.githubusercontent.com/melans/islamic-prayer-times-visualization/main/prayer-times-graph.html
```

#### Step 2: Open in Browser
Simply double-click the downloaded `prayer-times-graph.html` file, or:
```bash
# macOS
open prayer-times-graph.html

# Linux
xdg-open prayer-times-graph.html

# Windows
start prayer-times-graph.html
```

### Option 3: Clone the Repository

#### Prerequisites
- Git installed on your system
- Modern web browser (Chrome, Firefox, Safari, Edge)

#### Clone and Run
```bash
# Clone the repository
git clone https://github.com/melans/islamic-prayer-times-visualization.git

# Navigate to the directory
cd islamic-prayer-times-visualization

# Open the HTML file
open prayer-times-graph.html
```

### Option 4: Run with Local Web Server

For the best experience, especially if you plan to modify the code:

#### Using Python (Python 3)
```bash
# Navigate to the project directory
cd islamic-prayer-times-visualization

# Start a simple HTTP server
python -m http.server 8000

# Open your browser to http://localhost:8000
```

#### Using Node.js
```bash
# Install http-server globally (one-time)
npm install -g http-server

# Navigate to the project directory
cd islamic-prayer-times-visualization

# Start the server
http-server

# Open your browser to http://localhost:8080
```

#### Using PHP
```bash
# Navigate to the project directory
cd islamic-prayer-times-visualization

# Start PHP's built-in server
php -S localhost:8000

# Open your browser to http://localhost:8000
```

## 🌐 Deploy Your Own Instance

### GitHub Pages
1. Fork the repository to your GitHub account
2. Go to Settings → Pages
3. Select Source: Deploy from branch
4. Select Branch: main, Folder: / (root)
5. Save and wait a few minutes
6. Your app will be live at: `https://[your-username].github.io/islamic-prayer-times-visualization/`

### Netlify
1. Fork the repository
2. Go to [Netlify](https://netlify.com)
3. Click "Add new site" → "Import an existing project"
4. Connect your GitHub and select the repository
5. Click "Deploy site"
6. Your app will have a unique Netlify URL

### Vercel
1. Fork the repository
2. Go to [Vercel](https://vercel.com)
3. Click "New Project"
4. Import your GitHub repository
5. Click "Deploy"
6. Your app will have a unique Vercel URL

### Google Sites
1. Download `prayer-times-graph.html`
2. Go to [Google Sites](https://sites.google.com)
3. Create a new site
4. Click Embed → Embed code
5. Paste the entire HTML content
6. Publish your site

## 🔧 System Requirements

### Minimum Requirements
- **Browser**: Chrome 80+, Firefox 75+, Safari 13+, Edge 80+
- **JavaScript**: Enabled
- **Internet**: Required for API calls and map services
- **Screen**: 1024x768 or higher recommended

### Optimal Requirements
- **Browser**: Latest version of Chrome, Firefox, or Safari
- **Screen**: 1920x1080 or higher for best visualization
- **Connection**: Broadband for smooth data loading

## ⚙️ Configuration

### Change Default Location
Edit line 347 in `prayer-times-graph.html`:
```javascript
const defaultLocation = {
    latitude: 21.3891,  // Change to your latitude
    longitude: 39.8579, // Change to your longitude
    default: true,
    name: 'Mecca, Saudi Arabia' // Change to your city
};
```

### Modify Prayer Calculation Method
Edit line 515 to change the calculation method:
```javascript
// Change method=2 to your preferred method
const url = `https://api.aladhan.com/v1/calendar/${year}/${month}?latitude=${latitude}&longitude=${longitude}&method=2`;
```

Methods:
- 1: University of Islamic Sciences, Karachi
- 2: Islamic Society of North America (Default)
- 3: Muslim World League
- 4: Umm Al-Qura University, Makkah
- 5: Egyptian General Authority

## 🐛 Troubleshooting

### Location Not Working
- Ensure you've allowed location permissions in your browser
- Check if you're using HTTPS (required for geolocation)
- Try searching for your city manually

### Data Not Loading
- Check your internet connection
- Verify the APIs are accessible (not blocked by firewall)
- Open browser console (F12) to check for errors

### Display Issues
- Ensure JavaScript is enabled
- Try a different browser
- Clear browser cache and reload

## 💡 Tips

1. **Bookmark the page** for quick access
2. **Add to home screen** on mobile for app-like experience
3. **Use fullscreen mode** (F11) for best visualization
4. **Try extreme locations** like "Anchorage" or "Ushuaia" for interesting patterns

## 🆘 Need Help?

- Check the [[FAQ]] page
- Open an [issue on GitHub](https://github.com/melans/islamic-prayer-times-visualization/issues)
- Contact: anssary@googlemail.com

---

[← Back to Home](Home)