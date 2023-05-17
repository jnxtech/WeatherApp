<script>
  let apiKey = 'b5662c5bf39047ba9a2175616231605';
  let city = '';
  let weatherData = null;
  let error = '';

  async function getWeather() {
    try {
      const response = await fetch(
        `https://api.weatherapi.com/v1/current.json?key=${apiKey}&q=${city}`
      );
      if (response.ok) {
        weatherData = await response.json();
        error = '';
      } else {
        weatherData = null;
        error = 'Error retrieving weather data. Please try again.';
      }
    } catch (err) {
      console.error(err);
      weatherData = null;
      error = 'An error occurred. Please try again later.';
    }
  }

  function getCurrentDateTime() {
    const now = new Date();
    const options = {
      weekday: 'long',
      month: 'long',
      day: 'numeric',
      hour: 'numeric',
      minute: 'numeric',
    };
    return now.toLocaleString('en-US', options);
  }

  function getWindSpeed() {
    return weatherData ? `${weatherData.current.wind_kph} km/h` : '';
  }

  function getHumidity() {
    return weatherData ? `${weatherData.current.humidity}%` : '';
  }

  function getPressure() {
    return weatherData ? `${weatherData.current.pressure_mb} mb` : '';
  }

  function getLocation() {
    if ('geolocation' in navigator) {
      navigator.geolocation.getCurrentPosition(async (position) => {
        try {
          const { latitude, longitude } = position.coords;
          const response = await fetch(
            `https://api.weatherapi.com/v1/current.json?key=${apiKey}&q=${latitude},${longitude}`
          );
          if (response.ok) {
            weatherData = await response.json();
            error = '';
            city = weatherData.location.name;
          } else {
            weatherData = null;
            error = 'Error retrieving weather data. Please try again.';
          }
        } catch (err) {
          console.error(err);
          weatherData = null;
          error = 'An error occurred. Please try again later.';
        }
      });
    } else {
      error = 'Geolocation is not supported by your browser.';
    }
  }

  function clearData() {
    city = '';
    weatherData = null;
    error = '';
  }
</script>

<main class="max-w-md mx-auto p-6 bg-gradient-to-br from-purple-900 to-indigo-900 text-white h-screen flex flex-col justify-center items-center">
  <h1 class="text-4xl font-bold mb-8 text-pink-500 animate-pulse">Weather App</h1>

  <button class="location-btn mb-8 bg-pink-500 hover:bg-pink-600 text-white font-semibold py-2 px-4 rounded" on:click={getLocation}>
    Get Weather for Current Location
  </button>

  <div class="search-container mb-8">
    <input class="search-input border border-white text-pink-500 placeholder-pink-400 px-4 py-2 mr-4 focus:outline-none focus:border-pink-500 transform hover:scale-105 transition-all duration-300" bind:value={city} placeholder="Enter a city" />
    <button class="search-btn bg-purple-500 hover:bg-purple-600 text-white font-semibold py-2 px-4 rounded transform hover:rotate-12 transition-all duration-300" on:click={getWeather}>
      Get Weather
    </button>
  </div>

  {#if error}
    <p class="error text-red-500 font-semibold mb-8 animate-pulse">{error}</p>
  {:else if weatherData}
    <div class="weather-container bg-gray-800 p-6 rounded shadow">
      <h2 class="location text-2xl mb-4 text-pink-500 animate-pulse">{weatherData.location.name}</h2>
      <p class="temperature text-xl mb-2">Temperature: <span class="text-green-400">{weatherData.current.temp_c}°C</span></p>
      <p class="condition text-lg mb-2">Condition: <span class="text-blue-400">{weatherData.current.condition.text}</span></p>
      <p class="date-time text-sm text-gray-400 mb-4">{getCurrentDateTime()}</p>
      <p class="details text-lg mb-2">Wind Speed: {getWindSpeed()}</p>
      <p class="details text-lg mb-2">Humidity: {getHumidity()}</p>
      <p class="details text-lg">Pressure: {getPressure()}</p>
    </div>
  {/if}

  <button class="clear-btn bg-purple-500 hover:bg-purple-600 text-white font-semibold py-2 px-4 rounded mt-8 transform hover:translate-x-2 transition-all duration-300" on:click={clearData}>
    Clear
  </button>
</main>

<style>
  main {
    max-width: 400px;
    margin: 0 auto;
    padding: 20px;
    text-align: center;
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  }

  h1 {
    font-size: 36px;
    margin-bottom: 20px;
    font-weight: bold;
    color: #ff00ff;
    text-shadow: 2px 2px 4px rgba(255, 0, 255, 0.2);
  }

  .location-btn,
  .search-btn,
  .clear-btn {
    background-color: #ff00ff;
    color: #fff;
    border: none;
    padding: 10px 20px;
    font-size: 16px;
    cursor: pointer;
    transition: background-color 0.3s ease;
    border-radius: 5px;
    box-shadow: 2px 2px 4px rgba(255, 0, 255, 0.2);
  }

  .location-btn:hover,
  .search-btn:hover,
  .clear-btn:hover {
    background-color: #e600e6;
  }

  .search-input {
    background-color: transparent;
    border: none;
    border-bottom: 1px solid #fff;
    padding: 8px;
    color: #fff;
    font-size: 16px;
    margin-right: 10px;
    transition: border-color 0.3s ease;
  }

  .search-input::placeholder {
    color: #cc00cc;
  }

  .search-input:focus {
    outline: none;
    border-color: #ff00ff;
  }

  .weather-container {
    margin-top: 20px;
    padding: 20px;
    border-radius: 5px;
    background-color: rgba(0, 0, 0, 0.8);
    box-shadow: 2px 2px 4px rgba(255, 0, 255, 0.2);
  }

  .location {
    font-size: 24px;
    margin-bottom: 10px;
    color: #ff00ff;
    text-shadow: 1px 1px 2px rgba(255, 0, 255, 0.2);
  }

  .temperature {
    font-size: 20px;
    color: #00ff00;
  }

  .condition {
    margin-top: 10px;
    font-size: 18px;
    color: #00ccff;
  }

  .date-time {
    font-size: 14px;
    margin-bottom: 10px;
    color: #888;
  }

  .details {
    margin-top: 10px;
    font-size: 18px;
    color: #cc00cc;
  }

  .error {
    color: #ff0000;
    font-weight: bold;
    margin-top: 20px;
  }

  .clear-btn {
    background-color: #ff00ff;
    color: #fff;
    border: none;
    padding: 10px 20px;
    font-size: 16px;
    cursor: pointer;
    transition: background-color 0.3s ease;
    margin-top: 20px;
    border-radius: 5px;
    box-shadow: 2px 2px 4px rgba(255, 0, 255, 0.2);
  }

  .clear-btn:hover {
    background-color: #e600e6;
  }
</style>
