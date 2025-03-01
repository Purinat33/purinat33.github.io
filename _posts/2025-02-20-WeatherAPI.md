---
title: "Weather Report GUI application"
categories: [Software Development, Small Application]
---

# Weather Report GUI App: A Practical Project

## Introduction

I developed a GUI-based Weather Report application using Python's Tkinter. This application allows users to search for cities with a partial search, fetch weather conditions, and check air quality index (AQI) using an API call. This project not only helped me explore GUI development but also gave me hands-on experience with API integration and data parsing.

## Objectives

The primary goals of this project were:

- To create an intuitive GUI application for weather reports.
- To implement a search function that allows users to find cities with partial input.
- To fetch and display weather details, including temperature, condition, and AQI.
- To enhance my understanding of working with APIs, JSON responses, and GUI frameworks.

## Features

- **City Search with Autocomplete**: Users can input a partial city name, and the app returns possible matches.
- **Weather Details**: Fetches and displays real-time weather conditions such as temperature, humidity, and atmospheric conditions.
- **Air Quality Index (AQI)**: Shows PM2.5 levels with color-coded indicators for easy understanding.
- **Simple and Intuitive UI**: A user-friendly interface with labels, buttons, and separators for clear data representation.

## Technical Implementation

- **Programming Language**: Python
- **GUI Framework**: Tkinter
- **API Used**: [WeatherAPI](https://www.weatherapi.com/)
- **Libraries Used**:
  - `requests` for making API calls
  - `tkinter` for GUI elements
  - `dotenv` for managing API keys securely
  - `PIL` for handling images

## Code Overview

The application starts with a Tkinter window and a text input for city searches. When a user enters a city name and clicks "Search City," the app makes an API request to fetch matching city names. Users can then select a city, triggering another API request to retrieve detailed weather data.

Here is a breakdown of key functionalities:

- **City Search Function**: Fetches matching cities from the API and displays them in a list.
- **Weather Fetching Function**: Retrieves real-time weather data based on the selected city ID.
- **AQI Analysis**: Displays air quality with color-coded labels indicating safety levels.

## Practical Usage

This application can be used in various scenarios, such as:

- **Travel Planning**: Quickly check weather conditions before planning a trip.
- **Health Awareness**: Monitor AQI levels to determine outdoor activity safety.
- **Learning Project**: Ideal for beginners to practice API integration and GUI development.

## What I've Learned

- **API Integration**: Understanding how to send API requests and parse JSON responses.
- **GUI Development**: Building interactive applications with Tkinter.
- **Data Handling**: Extracting and displaying relevant data efficiently.
- **User Experience Considerations**: Ensuring the application remains simple and informative for end users.

## Future Improvements

To enhance the app, I plan to:

- Implement caching for API responses to reduce redundant calls.
- Improve the search function with dynamic suggestions as users type.
- Add a graphical weather representation using icons.
- Expand to include a forecast feature for upcoming days.

## Conclusion

This project was a valuable learning experience, combining API integration with GUI design. It showcases how simple tools like Tkinter can be used to build practical applications. I hope this write-up inspires others to explore similar projects and continue learning.

Check out the project source code: [GitHub Repository](https://github.com/Purinat33/Weather-Report-via-API/blob/main/main.py)

## Screenshot:

![Demo](/assets/img/demo1.png)

![Showcase](/assets/img/demo2.png)
