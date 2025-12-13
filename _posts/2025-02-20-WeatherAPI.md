---
title: "Weather Report GUI application"
categories: [Software Development, Small Application]
toc: true
---

# Weather Report GUI Application (Tkinter + WeatherAPI)

A lightweight desktop GUI that lets users **search cities**, fetch **current weather**, and view **air quality (PM2.5/AQI)** using API calls. Built to practice real-world API integration, JSON parsing, and GUI state handling.

- Repo: https://github.com/Purinat33/Weather-Report-via-API/blob/main/main.py
- API: https://www.weatherapi.com/

---

## What the app does

### Core features

- **City search (partial input):** type part of a city name and retrieve matches
- **Current weather display:** temperature, condition, humidity, and related details
- **Air quality (AQI / PM2.5):** shows air quality with clear visual indicators
- **Simple UI:** built for fast “search → select → view” usage

---

## Why I built it

I wanted a small but realistic project that combines:

- GUI event handling (buttons, lists, selection state)
- external API calls (requests, failures, rate limits)
- parsing nested JSON safely
- basic UX decisions (clear layout and readable results)

---

## User flow

1. User enters a partial city name
2. App calls the API to fetch possible city matches
3. User selects a city from the list
4. App requests the selected city’s current weather + air quality
5. UI updates labels and indicators with the new data

---

## Technical design

### Tech stack

- Python
- Tkinter (GUI)
- `requests` (HTTP)
- `dotenv` (API key management)

### Key implementation points (what matters in a GUI + API app)

- **Separation of concerns**
  - UI components handle display + user events
  - API functions handle requests + JSON parsing
- **State management**
  - selected city and latest response are stored and used to refresh the UI cleanly
- **Error handling**
  - invalid city searches, empty results, network errors, and API failures should surface as readable messages (instead of crashing)

> In small GUI apps, stability comes from handling “bad input / bad network / bad API response” gracefully.

---

## AQI display (interpretation)

The app displays PM2.5 / AQI in a user-friendly way (e.g., color-coded labels). This makes the result readable at a glance and connects the data to a practical decision: “Is it a good idea to be outdoors right now?”

---

## What I learned

- How to integrate a third-party API and parse nested JSON safely
- GUI event-driven programming (callbacks, selection events, updating widgets)
- Basic UX: building a simple flow that feels responsive and clear
- Securing secrets with environment variables (`.env`) instead of hardcoding keys

---

## Future improvements

- **Caching** recent responses to reduce repeated API calls
- **Live autocomplete** as the user types (debounced search)
- **Forecast tab** (next 3–7 days)
- Better visual presentation (icons for conditions, graphs for AQI/temperature)
- Packaging into an executable (PyInstaller) for easy distribution

---

## Screenshots

![Demo](/assets/img/demo1.png){: .shadow w="850" }

![Showcase](/assets/img/demo2.png){: .shadow w="850" }
