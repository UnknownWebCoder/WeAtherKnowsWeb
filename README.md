<html>
<head>
  <title>Weather Knows the 3 C's</title>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: linear-gradient(to top, #ccefff, #ffffff);
      text-align: center;
      padding: 30px;
    }
    h1 {
      color: #2b6cb0;
    }
    .section {
      margin: 20px 0;
      padding: 15px;
      border-radius: 10px;
      background-color: #f8f9fa;
      box-shadow: 0 2px 5px rgba(0,0,0,0.05);
    }
    canvas {
      max-width: 600px;
      margin: 0 auto;
    }
  </style>
</head>
<body>

  <h1>🌤️ Weather Knows the 3 C's</h1>
  <center><h2>Visualizing Weather Through Information Design</h2></center>
  <p>A student-focused weather site contains forecast data so you can plan safer, smarter, and greener.</p>

  <nav>
    <a href="#About">About</a>
  </nav>

  <div class="section" id="cloud-section">
    <h2>☁️ Cloud Status</h2>
    <p id="cloud-status">Loading...</p>
  </div>

  <div class="section" id="chart-section">
    <h2>📊 Weekly Temperature Chart</h2>
    <canvas id="tempChart" width="400" height="200"></canvas>
  </div>
	
  <div class="section" id="clarity-section">
    <h2>🔍 Clarity Report</h2>
    <p id="clarity-report">Loading...</p>
  </div>

 <script>
    const clarityMessages = {
      "Clear": "The sky is clear and bright. Perfect for outdoor activities!",
      "Partly Cloudy": "Some clouds in the sky, but it’s still a nice day.",
      "Cloudy": "The sky is fully covered with clouds. Might feel a bit gloomy.",
      "Stormy": "Storms expected. Best to stay indoors and safe."
    };

    const ctx = document.getElementById('tempChart').getContext('2d');
    const tempChart = new Chart(ctx, {
      type: 'line',
      data: {
        labels: ["Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun"],
        datasets: [{
          label: "Temperature (°C)",
          data: [],
          backgroundColor: "rgba(135,206,250, 0.2)",
          borderColor: "rgba(30,144,255, 1)",
          borderWidth: 2,
          fill: true,
          tension: 0.3,
          pointRadius: 5,
          pointBackgroundColor: "#1E90FF"
        }]
      },
      options: {
        responsive: true,
        scales: {
          y: { beginAtZero: false }
        }
      }
    });

    const apiKey = "85a4ce8b9cbb48af8c641914252310";
    const city = "Manila"; // change if you want another city

    async function fetchWeather() {
      try {
        // Use proxy to bypass GitHub Pages CORS restriction
        const proxy = "https://api.allorigins.win/raw?url=";
        const url = `https://api.openweathermap.org/data/2.5/forecast?q=${city}&units=metric&appid=${apiKey}`;
        const response = await fetch(proxy + encodeURIComponent(url));

        if (!response.ok) throw new Error("Weather data unavailable");
        const data = await response.json();

        const currentWeather = data.list[0].weather[0].main;
        document.getElementById("cloud-status").textContent = currentWeather;

        let message = clarityMessages["Cloudy"];
        if (currentWeather.includes("Clear")) message = clarityMessages["Clear"];
        if (currentWeather.includes("Clouds")) message = clarityMessages["Partly Cloudy"];
        if (currentWeather.includes("Rain") || currentWeather.includes("Storm")) message = clarityMessages["Stormy"];
        document.getElementById("clarity-report").textContent = message;

        const dailyTemps = [];
        for (let i = 0; i < data.list.length; i += 8) {
          dailyTemps.push(data.list[i].main.temp);
        }

        tempChart.data.datasets[0].data = dailyTemps.slice(0, 7);
        tempChart.update();

      } catch (error) {
        document.getElementById("cloud-status").textContent = "Error fetching weather data";
        document.getElementById("clarity-report").textContent = "Please check your API key or internet connection.";
        console.error(error);
      }
    }

    fetchWeather();
 </script>

</body>
</html>
