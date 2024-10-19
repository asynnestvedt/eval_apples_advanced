# Advanced Orchard AI Microservice Simulation Challenge

## Introduction

In this challenge, you will design and develop a microservice that merges AI-driven technology with a simulation of apple crop yield based on weather patterns. Your task is to create a RESTful API, containerized with Docker, complemented by well-written tests and clear documentation. This version introduces a crop yield simulation based on a season's weather data.

### Key Requirements

- **Language**: Choose Golang, TypeScript, Java, or Rust.
- **API**: A single RESTful POST endpoint.
- **Docker**: Containerize the microservice.
- **Testing**: Implement meaningful tests to cover different weather conditions and edge cases.
- **Documentation**: Provide detailed instructions in `README.md`.

### The Challenge: AI-Powered Apple Crop Yield Simulation

Create a REST API with a single POST endpoint that simulates apple crop yield over a particular season, factoring in weather data.

- **Endpoint**: `POST /simulate-yield`
  
  **Request Body** (JSON):
  ```json
  {
    "tree_count": 150,
    "apples_per_tree": 120,
    "season_length_days": 100,
    "weather_data": [
      {"day": 1, "temperature": 22, "rainfall_mm": 10, "wind_speed_kmh": 5},
      {"day": 2, "temperature": 28, "rainfall_mm": 2, "wind_speed_kmh": 15},
      ...
    ]
  }
  ```

- **Logic**:
  - The `tree_count` represents the number of trees in the orchard.
  - The `apples_per_tree` represents the maximum number of apples each tree can produce per day in optimal conditions.
  - The `season_length_days` represents the length of the season.
  - The `weather_data` is an array representing the weather for each day of the season, with fields for temperature (in °C), rainfall (in mm), and wind speed (in km/h).
  - The simulation should adjust the daily yield based on weather conditions. For example:
    - Ideal temperature: 20°C to 30°C. Outside this range, apple production decreases by 2% per degree.
    - Ideal rainfall: 5mm to 20mm. Outside this range, production decreases by 5% per missing/excess mm.
    - High wind speeds (>30 km/h) reduce production by 10% per day.

  **Formula** (example logic):
  - Daily yield = `tree_count` * `apples_per_tree` * `weather_adjustment`.
  - The `weather_adjustment` is calculated based on the weather data provided, affecting the base yield.

- **Response** (JSON):
  ```json
  {
    "total_apples_yielded": 1300000
  }
  ```

### Docker

- Provide a Dockerfile to containerize the service.
- Include instructions for building and running the container.

### Testing

- Write tests to verify the API's functionality, including different weather patterns and edge cases (e.g., no rainfall, extreme temperatures, etc.).
- Include clear instructions to run the test in the documentation.

### Documentation

Your `README.md` should cover:

1. Setup instructions.
2. Design rationale.
3. Example usage of the API with different input parameters, including weather data.

## Evaluation Criteria

- **Code Quality**: Clean, efficient, maintainable code.
- **Functionality**: The API performs as expected, with correct handling of weather-based adjustments.
- **Testing**: The tests should cover different weather conditions and edge cases, and be well-structured.
- **Documentation**: Clear and concise, covering all necessary steps.

## Bonus: Weather Pattern Analysis

### Additional Task

Track and analyze the weather data to identify optimal conditions for future harvests. Store this data in a database and provide an endpoint that suggests the best planting times based on historical weather patterns.

- **New Endpoint**: `GET /optimal-planting-time`
  - Analyze stored weather data to suggest the best planting time (e.g., when temperatures and rainfall are most likely to be ideal).
  
- **Database**: Store weather data and yield results over multiple seasons to generate insights.

### Documentation

Expand the `README.md` to include:

1. Explanation of the new `optimal-planting-time` endpoint.
2. Documentation on how the analysis is performed using historical data.

## Submission

- Host your code on a public platform (e.g., GitHub).
- Ensure the repository is accessible for review.
- Alternatively, zip and send your archive to the email provided during your interview.
