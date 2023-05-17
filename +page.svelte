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

<main class="max-w-md mx-auto p-6 bg-gray-900 text-white h-screen flex flex-col justify-center items-center">
  <h1 class="text-3xl font-bold mb-8 text-orange-500 animate-bounce">Weather App</h1>

  <button class="location-btn mb-8 bg-orange-500 hover:bg-orange-600 text-white font-semibold py-2 px-4 rounded" on:click={getLocation}>
    Get Weather for Current Location
  </button>

  <div class="search-container mb-8">
    <input class="search-input border border-white text-orange-500 placeholder-gray-400 px-4 py-2 mr-4 focus:outline-none focus:border-orange-500 transform hover:scale-105 transition-all duration-300" bind:value={city} placeholder="Enter a city" />
    <button class="search-btn bg-purple-500 hover:bg-purple-600 text-white font-semibold py-2 px-4 rounded transform hover:rotate-12 transition-all duration-300" on:click={getWeather}>
      Get Weather
    </button>
  </div>

  {#if error}
    <p class="error text-red-500 font-semibold mb-8 animate-pulse">{error}</p>
  {:else if weatherData}
    <div class="weather-container bg-gray-800 p-6 rounded shadow">
      <h2 class="location text-2xl mb-4 text-orange-500 animate-pulse">{weatherData.location.name}</h2>
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
    font-family: Arial, sans-serif;
    background-color: #101010;
    color: #fff;
  }

  .heading {
    font-size: 24px;
    margin-bottom: 20px;
    font-weight: bold;
    color: #e07a5f;
    text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.2);
  }

  .location-btn,
  .search-btn,
  .clear-btn {
    background-color: #e07a5f;
    color: #fff;
    border: none;
    padding: 10px 20px;
    font-size: 16px;
    cursor: pointer;
    transition: background-color 0.3s ease;
    border-radius: 5px;
    box-shadow: 2px 2px 4px rgba(0, 0, 0, 0.2);
  }

  .location-btn:hover,
  .search-btn:hover,
  .clear-btn:hover {
    background-color: #d96247;
  }

  .divider {
    border: none;
    height: 1px;
    background-color: #666;
    margin: 20px 0;
  }

  .search-container {
    display: flex;
    justify-content: center;
    align-items: center;
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
    color: #999;
  }

  .search-input:focus {
    outline: none;
    border-color: #e07a5f;
  }

  .weather-container {
    margin-top: 20px;
    padding: 20px;
    border-radius: 5px;
    background-color: #222;
    box-shadow: 2px 2px 4px rgba(0, 0, 0, 0.2);
  }

  .location {
    font-size: 20px;
    margin-bottom: 10px;
    color: #e07a5f;
    text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.2);
  }

  .temperature {
    font-size: 16px;
    color: #fff;
  }

  .condition {
    margin-top: 10px;
    font-size: 14px;
    color: #ccc;
  }

  .date-time {
    font-size: 12px;
    margin-bottom: 10px;
    color: #888;
  }

  .details {
    margin-top: 10px;
    font-size: 14px;
    color: #ccc;
  }

  .error {
    color: #ff4747;
    font-weight: bold;
    margin-top: 20px;
  }

  .clear-btn {
    background-color: #e07a5f;
    color: #fff;
    border: none;
    padding: 10px 20px;
    font-size: 16px;
    cursor: pointer;
    transition: background-color 
0.3s ease;
margin-top: 20px;
border-radius: 5px;
box-shadow: 2px 2px 4px rgba(0, 0, 0, 0.2);
}

.clear-btn:hover {
background-color: #d96247;
}
</style>
  
