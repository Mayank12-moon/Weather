# Weather
Its an program that gives real time weather data of the different places
import requests
import time
import sys
from typing import Optional, Dict, Any

# --- Configuration ---
# You need a free API key from a service like OpenWeatherMap.
# 1. Go to openweathermap.org and sign up.
# 2. Get your API key from your account dashboard.
API_KEY = "99f5bdb50c54b7dee5ba24fba32cd9b1"  # API KEY
BASE_URL = "http://api.openweathermap.org/data/2.5/weather"


# Function to safely convert Kelvin to Celsius and Fahrenheit
def kelvin_to_units(kelvin: float) -> tuple[float, float]:
    """Converts Kelvin to Celsius and Fahrenheit."""
    celsius = kelvin - 273.15
    fahrenheit = (celsius * 9 / 5) + 32
    return round(celsius, 2), round(fahrenheit, 2)


def get_current_weather(city: str) -> Optional[Dict[str, Any]]:
    """Fetches weather data from the OpenWeatherMap API for a given city."""
    if API_KEY == "YOUR_OPENWEATHERMAP_API_KEY":
        print("\n[ERROR] Please replace 'YOUR_OPENWEATHERMAP_API_KEY' with your actual API key.")
        return None

    # Define the parameters for the API call
    params = {
        'q': city,
        'appid': API_KEY,
        # We will use 'standard' (Kelvin) units and convert them ourselves for clarity
        'units': 'standard'
    }

    print(f"\nFetching weather for {city}...")
    try:
        # 1. Make the HTTP GET request
        response = requests.get(BASE_URL, params=params, timeout=10)
        response.raise_for_status()  # Raise an exception for bad status codes (4xx or 5xx)

        # 2. Parse the JSON response
        data = response.json()

        # 3. Check for API-specific errors (e.g., city not found, usually denoted by 'cod' != 200)
        if data.get("cod") != 200:
            print(f"[ERROR] API returned error: {data.get('message', 'Unknown error')}")
            return None

        return data

    except requests.exceptions.RequestException as e:
        # Catch network errors, connection timeouts, or bad HTTP status codes
        print(f"[NETWORK ERROR] Could not connect to the API: {e}")
        return None
    except Exception as e:
        print(f"[PROCESSING ERROR] An unexpected error occurred: {e}")
        return None


def display_weather(data: Dict[str, Any]):
    """Extracts and prints key weather information from the parsed data."""
    try:
        # Extract core weather details
        main = data['main']
        weather_info = data['weather'][0]
        sys_info = data['sys']

        # Convert temperature from Kelvin to C/F
        temp_k = main['temp']
        temp_c, temp_f = kelvin_to_units(temp_k)

        # Get times (converted from Unix timestamp to readable time)
        sunrise_utc = time.strftime('%H:%M:%S UTC', time.gmtime(sys_info['sunrise']))
        sunset_utc = time.strftime('%H:%M:%S UTC', time.gmtime(sys_info['sunset']))

        # Print the formatted report
        print("-" * 50)
        print(f"WEATHER REPORT FOR: {data['name']}, {sys_info['country']}")
        print(f"Last Updated (Unix Epoch): {data['dt']}")
        print("-" * 50)
        print(f"Condition: {weather_info['main']} ({weather_info['description'].title()})")
        print(f"Temperature: {temp_c}°C / {temp_f}°F")
        print(f"Feels Like : {kelvin_to_units(main['feels_like'])[0]}°C")
        print(f"Humidity   : {main['humidity']}%")
        print(f"Pressure   : {main['pressure']} hPa")
        print(f"Wind Speed : {data['wind']['speed']} m/s")
        print(f"Sunrise    : {sunrise_utc}")
        print(f"Sunset     : {sunset_utc}")
        print("-" * 50)

    except (KeyError, IndexError) as e:
        print(f"[PARSING ERROR] Failed to extract data. Key missing: {e}")
    except Exception as e:
        print(f"[DISPLAY ERROR] An error occurred while displaying data: {e}")


def main():
    """Main application loop to run the tracker."""
    print("--- Python Real-Time Weather Tracker ---")

    # Check if a city was passed as a command-line argument
    if len(sys.argv) > 1:
        city_name = " ".join(sys.argv[1:])
    else:
        # Prompt the user for a city if no argument is provided
        city_name = input("Enter the city name (e.g., Tokyo, London, Paris): ").strip()

    if not city_name:
        print("No city entered. Exiting.")
        return

    # To achieve "real-time" tracking, we can loop the fetch process
    REFRESH_INTERVAL_SECONDS = 60 * 10  # Refresh every 10 minutes (OpenWeatherMap updates every 10 minutes)

    while True:
        weather_data = get_current_weather(city_name)

        if weather_data:
            display_weather(weather_data)

        # Wait before the next refresh
        print(f"\nWaiting for {REFRESH_INTERVAL_SECONDS} seconds before next refresh... (Press Ctrl+C to stop)")
        try:
            time.sleep(REFRESH_INTERVAL_SECONDS)
        except KeyboardInterrupt:
            print("\nTracker stopped by user.")
            break
        except Exception as e:
            print(f"Error during sleep: {e}")
            break


if __name__ == "__main__":
    main()
