# Advanced Orchard Microservice Simulation Challenge (Apple Tree Edition)

## Introduction

In this challenge, you will design and develop a microservice simulating **apple crop yield potential** based on weather patterns throughout a **growing season**. This simulation recognizes that apple yield isn't determined daily but is influenced by cumulative weather and critical events during specific developmental stages (like bloom, fruit set, and growth). Your task is to create a RESTful API, containerized with Docker, complemented by well-written tests and clear documentation.

### Key Requirements

-   **Language**: Choose Golang, TypeScript, Java, or Rust.
-   **API**: A single RESTful POST endpoint for simulation.
-   **Docker**: Containerize the microservice.
-   **Testing**: Implement meaningful tests covering critical weather events (frost, drought, heatwaves) during different growth stages.
-   **Documentation**: Provide detailed instructions in `README.md`.

### The Challenge: Apple Crop Yield Simulation

Create a REST API with a single POST endpoint that simulates the final potential apple crop yield for a season, considering the impact of weather throughout key developmental stages.

-   **Endpoint**: `POST /simulate-yield`

    **Request Body** (JSON):
    ```json
    {
      "tree_count": 150,
      "potential_yield_per_tree": 500, // Max potential apples per tree in a PERFECT season
      "growing_season_data": [
        // Data spanning from bud break through harvest
        {"day": 1, "stage": "Bud Break", "temperature_celsius": 10, "rainfall_mm": 5, "frost_occurred": false},
        {"day": 2, "stage": "Bud Break", "temperature_celsius": 12, "rainfall_mm": 2, "frost_occurred": false},
        // ... days passing ...
        {"day": 30, "stage": "Bloom", "temperature_celsius": 18, "rainfall_mm": 0, "frost_occurred": false},
        {"day": 31, "stage": "Bloom", "temperature_celsius": -1, "rainfall_mm": 0, "frost_occurred": true}, // Critical Frost Event!
        {"day": 32, "stage": "Bloom", "temperature_celsius": 15, "rainfall_mm": 25, "wind_speed_kmh": 10}, // Heavy rain during bloom
        // ... days passing ...
        {"day": 60, "stage": "Fruit Set", "temperature_celsius": 22, "rainfall_mm": 1, "frost_occurred": false},
        // ... days passing ...
        {"day": 100, "stage": "Fruit Growth", "temperature_celsius": 35, "rainfall_mm": 0, "frost_occurred": false}, // Heat stress
        {"day": 101, "stage": "Fruit Growth", "temperature_celsius": 36, "rainfall_mm": 0, "frost_occurred": false}, // Continued heat stress
        // ... days passing ...
        {"day": 170, "stage": "Pre-Harvest", "temperature_celsius": 20, "rainfall_mm": 5, "wind_speed_kmh": 50}, // High wind risk
        // ... up to harvest day ...
      ]
    }
    ```

-   **Logic**:
    -   `tree_count`: Number of apple trees in the orchard.
    -   `potential_yield_per_tree`: The theoretical maximum number of apples one tree could produce in an absolutely perfect season. This is the starting point before deductions.
    -   `growing_season_data`: An array representing the key weather factors for each day of the growing season (e.g., from bud break to harvest, typically 150-200+ days depending on variety and location).
        -   `day`: Day number within the growing season.
        -   `stage`: The phenological stage of the trees on that day (e.g., "Dormant", "Bud Break", "Bloom", "Fruit Set", "Fruit Growth", "Ripening", "Pre-Harvest"). *This simplifies the logic significantly.*
        -   `temperature_celsius`: Average or relevant daily temperature.
        -   `rainfall_mm`: Daily rainfall.
        -   `frost_occurred`: Boolean indicating if a damaging frost occurred (e.g., temp <= 0°C during vulnerable stages).
        -   `wind_speed_kmh` (Optional but useful): Particularly relevant during bloom (pollination) and pre-harvest (fruit drop).

    -   **Simulation Approach**:
        1.  **Start with Maximum Potential**: Calculate initial `max_potential_yield = tree_count * potential_yield_per_tree`.
        2.  **Iterate Through Season**: Process the `growing_season_data` day by day.
        3.  **Apply Stage-Specific Stress Factors**: Reduce the `max_potential_yield` based on adverse weather during critical stages. These reductions are often **multiplicative** and can be capped (yield can't go below zero).
            -   **Bloom Stage**:
                -   `frost_occurred`: Severe reduction (e.g., -50% to -90% for a single hard frost, depending on severity/duration).
                -   Temperatures too low (< 12-15°C) or too high (> 28-30°C): Moderate reduction due to poor pollination activity (e.g., -5% to -15% per day).
                -   Heavy Rain (> 20-25mm): Moderate reduction, poor pollination (e.g., -10% per day).
                -   High Wind (> 30-35 km/h): Moderate reduction, poor pollination (e.g., -5% per day).
            -   **Fruit Set Stage**:
                -   Extreme Heat Stress (e.g., > 32-35°C for multiple days): Can increase "June drop", moderate reduction (e.g., -1% to -5% cumulative per heatwave).
                -   Drought Stress (e.g., multiple consecutive days with < 2mm rain AND high temps): Similar reduction to heat stress.
            -   **Fruit Growth Stage**:
                -   Severe Drought: Primarily affects fruit *size* and *quality*, but severe, prolonged drought can reduce the *number* of harvestable fruit. Model as a smaller reduction (e.g., -0.5% per day of severe drought after X consecutive days).
                -   Extreme Heat (> 35-38°C): Can cause sunburn, reducing marketable yield. Model as a small reduction in total count (e.g., -0.2% per extreme heat day).
                -   Hail: (If adding hail data) Can cause significant physical damage, reducing marketable yield. Model as a % reduction based on severity.
            -   **Pre-Harvest / Harvest Stage**:
                -   High Winds (> 40-50 km/h): Can knock fruit off trees. Significant reduction potential (e.g., -5% to -20% per high-wind event close to harvest).

        4.  **Final Yield**: The remaining value of `max_potential_yield` after all deductions is the simulated yield.

-   **Response** (JSON):
    ```json
    {
      "estimated_total_yield": 85000 // Example: yield reduced from initial potential
    }
    ```
    *(Renamed field for clarity)*

### Docker

-   Provide a Dockerfile to containerize the service.
-   Include instructions for building and running the container.

### Testing

-   Write tests simulating different scenarios:
    -   A perfect season (should yield close to `tree_count * potential_yield_per_tree`).
    -   A season with a devastating frost during bloom.
    -   A season with poor pollination weather (cold/wet/windy bloom).
    -   A season with significant summer drought/heat affecting fruit set/growth.
    -   A season with high winds just before harvest.
    -   Edge cases (e.g., empty `growing_season_data`, zero trees).

### Documentation

Your `README.md` should cover:

1.  Setup instructions.
2.  **Design Rationale**: Explain the shift from daily yield to seasonal potential influenced by critical stages. Briefly justify the chosen impact factors and stages.
3.  API Usage: Clear examples of the POST request and expected responses for different weather scenarios.
4.  Running Tests.

## Evaluation Criteria

-   **Code Quality**: Clean, efficient, maintainable code reflecting the simulation logic.
-   **Functionality**: The API performs as expected, applying reductions based on stage-specific weather events.
-   **Agricultural Plausibility**: The model, while simplified, reflects the basic principles of how weather affects apple yield over a season more accurately than the original challenge.
-   **Testing**: Comprehensive tests covering critical scenarios and edge cases.
-   **Documentation**: Clear, concise, and complete.

## Bonus: Historical Analysis for Risk Assessment

### Additional Task

Leverage historical simulation data to provide insights into seasonal risks rather than "optimal planting time" (which is less relevant for perennial trees).

-   **Store Data**: Persist the input `growing_season_data` and the resulting `estimated_total_yield` for each simulation run in a database (e.g., PostgreSQL, SQLite, MongoDB).
-   **New Endpoint**: `GET /historical-risk-analysis`
    -   **Functionality**: Analyze the stored historical data. Identify which weather events (e.g., bloom frosts, summer heatwaves) and during which stages have historically had the most significant negative impact on simulated yield in this orchard's context.
    -   **Response Example** (JSON):
        ```json
        {
          "analysis_period_simulations": 50, // Number of simulations analyzed
          "major_yield_loss_factors": [
            {"event": "Frost during Bloom", "average_yield_reduction_percent": 45.5, "frequency_percent": 20.0},
            {"event": "High Wind Pre-Harvest", "average_yield_reduction_percent": 15.0, "frequency_percent": 30.0},
            {"event": "Prolonged Summer Drought", "average_yield_reduction_percent": 10.2, "frequency_percent": 50.0}
          ],
          "overall_average_yield_as_percent_of_potential": 75.8
        }
        ```

### Documentation

Expand the `README.md` to include:

1.  Explanation of the new `historical-risk-analysis` endpoint and its purpose.
2.  Details on the database schema used for storing simulation data.
3.  How the analysis identifies key risk factors from historical data.

## Submission

-   Host your code on a public platform (e.g., GitHub).
-   Ensure the repository is accessible for review.
-   Alternatively, zip and send your archive as instructed.
