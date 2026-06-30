# Init-System

A clean, minimal dark-themed custom new tab page. No frameworks, no backend — just HTML, CSS and vanilla JS.

## Features

- Live clock with centered, large time display + full date
- Weather via [Open-Meteo](https://open-meteo.com/en/docs) using the browser's geolocation, no API key required
- Centered Google search bar
- Collapsible notes panel, persisted with `localStorage`
- Dark, minimal UI with a subtle fade-in animation on load

## Setup
**Note:** You can also just see the website under: https://github.com/Facticee/new-tab-site
1. Clone or download this repo.
2. Open `index.html` in your browser to make sure everything works (weather requires you to allow location access).
3. Set it as your browser's new tab page:
   - **Chrome/Edge**: use an extension like *New Tab Redirect* and point it to the local file path or host the files (e.g. via GitHub Pages).
   - **Firefox**: Settings > Home > Startpage


## How it works

### Clock & date

```js
function updateClock() {
    const now = new Date();
    document.getElementById('live-uhr').textContent = now.toLocaleTimeString();
    document.getElementById('big-time').textContent = now.toLocaleTimeString(undefined, {
        hour: '2-digit',
        minute: '2-digit'
    });
    document.getElementById('date').textContent = now.toLocaleDateString(undefined, {
        weekday: 'long', day: '2-digit', month: 'long', year: 'numeric'
    });
}
updateClock();
setInterval(updateClock, 1000);
```

### Weather

Pulls current temperature and weather code from the Open-Meteo API based on the browsers geolocation, then changes the numeric weather code to readable text:

```js
async function loadWeather(lat, lon) {
    const params = new URLSearchParams({
        latitude: lat,
        longitude: lon,
        current: 'temperature_2m,weather_code'
    });

    const response = await fetch(`https://api.open-meteo.com/v1/forecast?${params}`);
    const data = await response.json();

    const temperature = data.current.temperature_2m;
    const weatherText = weatherMap[data.current.weather_code] || 'Weather';

    weatherEl.innerHTML = `<span class="temp">${Math.round(temperature)}°C</span> ${weatherText}`;
}
```

If geolocation is denied or unavailable, the widget will just output "No location".

### Notes panel

A small floating `✎` button toggles a notes panel. Content is saved to `localStorage` on every keystroke, so notes persist across new tabs:

```js
notesArea.value = localStorage.getItem('newtab-notes') || '';
notesArea.addEventListener('input', () => {
    localStorage.setItem('newtab-notes', notesArea.value);
});
```

## Customization

- I used CSS variables forthe colors. You can find them on top of the `style.css` (`--bg`, `--bg2`, `--text`, `--text-2`, `--farben`, `--border`) — change those to retheme the whole page.

## Credits

- Weather data from [Open-Meteo](https://open-meteo.com/en/docs)
