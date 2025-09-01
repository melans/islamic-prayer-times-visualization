# Frequently Asked Questions (FAQ)

## 🌍 General Questions

### What is this tool?
The Islamic Prayer Times Visualization is an interactive web application that displays Muslim prayer times as beautiful charts, showing patterns across months and years for any location worldwide.

### Is it free to use?
Yes! This tool is completely free and open source under the MIT license. You can use it, modify it, and share it freely.

### Do I need to install anything?
No installation required! Just open the webpage in any modern browser. For the live demo, visit: https://sites.google.com/view/muslimprayertimesvisualization/

### Does it work offline?
The initial data load requires internet connection to fetch prayer times from the API. However, once loaded, the data is cached and you can toggle DST without internet.

## 📍 Location Questions

### Why does it show "Your Location"?
When you allow browser location access, it uses your device's GPS/location services. "Your Location" indicates it's using your actual coordinates rather than a searched city.

### Can I search for any city?
Yes! The search supports any city worldwide. Just type the city name (e.g., "New York", "London", "Dubai") and click Search or press Enter.

### What if my city isn't found?
Try these alternatives:
- Add the country name: "Dallas, USA" or "Paris, France"
- Try a nearby major city
- Use latitude/longitude coordinates directly

### Why does it default to Mecca?
Mecca is the holiest city in Islam and the direction of prayer (Qibla) for Muslims worldwide, making it a meaningful default location.

## 📊 Visualization Questions

### What do the different colors represent?
- **Light Blue** - Fajr (Dawn Prayer)
- **White** - Sunrise  
- **Yellow** - Dhuhr (Noon Prayer)
- **Orange** - Asr (Afternoon Prayer)
- **Purple** - Maghrib (Sunset Prayer)
- **Navy** - Isha (Night Prayer)

### Why do some locations have dramatic curves?
Locations at extreme latitudes (like Alaska or Argentina) experience dramatic seasonal changes in daylight hours, creating the beautiful sine wave patterns you see.

### What's the Y-axis showing?
The Y-axis shows 24-hour time (00:00 to 24:00), with midnight at the top and bottom. It's reversed so morning prayers appear at the top of the chart.

### Can I zoom in on specific dates?
Currently, the chart shows the full year(s) selected. Hover over any point to see the exact prayer times for that date.

## ⚙️ Technical Questions

### What calculation method is used?
By default, the app uses Method 2 (Islamic Society of North America - ISNA). This can be changed in the code if needed.

### What's DST?
DST stands for Daylight Saving Time. When enabled, prayer times are adjusted for regions that observe daylight saving time changes.

### How accurate are the prayer times?
The prayer times are calculated using the Aladhan API, which uses established astronomical formulas and is widely trusted by the Muslim community.

### Can I change the number of years displayed?
Yes! Use the "Years" dropdown to select 1-5 years of data. The chart automatically adjusts its size.

## 🐛 Troubleshooting

### The chart isn't loading
1. Check your internet connection
2. Make sure JavaScript is enabled
3. Try refreshing the page (Ctrl+F5)
4. Check browser console for errors (F12)

### Location permission was denied
1. Click the lock icon in your browser's address bar
2. Find "Location" and change it to "Allow"
3. Refresh the page

### Prayer times seem incorrect
1. Verify your location is correct
2. Check the DST toggle setting
3. Compare with local mosque times (slight variations are normal due to different calculation methods)

### The page looks broken
1. Use a modern browser (Chrome, Firefox, Safari, Edge)
2. Clear your browser cache
3. Disable browser extensions that might interfere

## 🎨 Customization

### Can I change the colors?
Yes! Edit the CSS variables in the HTML file:
```css
--fajr-color: #5dade2;
--sunrise-color: #ffffff;
--dhuhr-color: #ffdc00;
--asr-color: #ff851b;
--maghrib-color: #8b008b;
--isha-color: #001f3f;
```

### Can I use a different calculation method?
Yes! Change the `method` parameter in the API URL (line 515). See [[API Integration]] for available methods.

### Can I add this to my website?
Absolutely! You can:
1. Embed it in an iframe
2. Host the HTML file on your server
3. Integrate the code into your existing site

## 💰 Support & Contribution

### How can I support this project?
- ⭐ Star the GitHub repository
- ☕ [Buy me a coffee via PayPal](https://www.paypal.com/paypalme/anssary)
- 🔄 Share with your community
- 🐛 Report bugs and suggest features
- 💻 Contribute code improvements

### Can I contribute to the code?
Yes! We welcome contributions. See [[Contributing Guidelines]] for details.

### Where do I report bugs?
Please open an issue on [GitHub Issues](https://github.com/melans/islamic-prayer-times-visualization/issues)

### How do I request new features?
Open a [GitHub Discussion](https://github.com/melans/islamic-prayer-times-visualization/discussions) or issue with your feature request.

## 📱 Mobile & Compatibility

### Does it work on mobile?
Yes! The design is responsive and works on all modern mobile devices.

### Which browsers are supported?
- ✅ Chrome 80+
- ✅ Firefox 75+
- ✅ Safari 13+
- ✅ Edge 80+
- ❌ Internet Explorer (not supported)

### Can I install it as an app?
You can add it to your home screen:
- **iOS**: Safari → Share → Add to Home Screen
- **Android**: Chrome → Menu → Add to Home Screen

## 🔒 Privacy & Security

### Is my location data stored?
No. Location data is only used to fetch prayer times and is never stored or transmitted to any third party.

### Is it safe to use?
Yes! The app is open source, uses secure HTTPS connections, and doesn't collect any personal data.

### What APIs are used?
- **Aladhan API** - For prayer time calculations
- **Nominatim (OpenStreetMap)** - For city geocoding

## 📚 Educational

### Why do prayer times change?
Prayer times are based on the sun's position, which changes daily due to Earth's orbit and axial tilt, creating seasonal variations.

### What are the extreme latitude examples for?
They demonstrate how Islamic prayer calculations adapt to extreme conditions like Arctic white nights and Antarctic polar darkness, showing the mathematical beauty of the system.

### Can this be used for education?
Absolutely! It's perfect for:
- Teaching astronomy and Earth sciences
- Understanding Islamic practices
- Demonstrating mathematical patterns in nature
- Exploring geography and time zones

---

## Still have questions?

- 📧 Email: anssary@googlemail.com
- 💬 [GitHub Discussions](https://github.com/melans/islamic-prayer-times-visualization/discussions)
- 🐛 [GitHub Issues](https://github.com/melans/islamic-prayer-times-visualization/issues)

---

[← Back to Home](Home)