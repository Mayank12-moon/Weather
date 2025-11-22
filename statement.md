Scope of the Project
The scope of this project is to provide a command-line interface (CLI) tool that retrieves, processes, and displays current weather information for a specified city.

API Interaction: It focuses on securely interacting with the OpenWeatherMap API, handling HTTP requests, and parsing the resulting JSON data.

Data Processing: It includes a necessary utility function (kelvin_to_units) to perform unit conversions (Kelvin to Celsius and Fahrenheit).

User Interface: The interface is strictly text-based and runs in the terminal.

"Real-Time" Tracking: It implements a basic loop with a fixed refresh interval (10 minutes) to simulate continuous monitoring, allowing users to track weather changes over time without manually restarting the script.

Error Handling: A key part of the scope is robust error handling for API connection issues, bad HTTP status codes, missing data, and API-specific errors (like "city not found").

Target Users
The primary target users for this script are individuals who are comfortable working in a command-line environment and have a basic understanding of Python or development tools.

Developers/Programmers: They can use this as a foundational example for API integration, web scraping, and creating utility scripts.

Hobbyists/Enthusiasts: Anyone interested in quickly checking a city's weather without relying on a web browser or graphical application.

System Administrators: They might integrate this script into larger system monitoring or automation tools to log weather conditions at specific times.

High-Level Features
The application provides the following core capabilities:

Weather Data Fetching:

Retrieves the current weather conditions for any globally recognized city using its name.

Handles necessary API key management and parameter construction.

Data Conversion & Calculation:

Performs temperature unit conversion from Kelvin (the API's default) to both Celsius and Fahrenheit for user readability.

Comprehensive Display:

Presents a structured, easy-to-read report in the console.

Includes essential weather metrics: Temperature, "Feels Like" Temperature, Humidity, Pressure, Wind Speed, and a textual Condition/Description (e.g., "Clouds / Overcast Clouds").

Astronomical Data:

Displays the Sunrise and Sunset times (in UTC).

Robust Error Handling:

Provides clear, specific messages for common issues like API key errors, Network Connection failures, City Not Found errors, and internal Data Parsing issues.

Continuous Monitoring:

Offers a time-based refresh loop to automatically update the weather report at regular intervals (10 minutes) for continuous tracking.
