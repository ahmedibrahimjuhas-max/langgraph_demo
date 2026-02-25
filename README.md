# LangGraph Demo Notebooks

This repository contains 4 Jupyter notebooks that progressively build LangGraph agent patterns, from a basic routed graph to a side-by-side comparison with a ReAct-style tool-calling node, plus a FastAPI streaming example.

## What You Will find

- How to model shared state with `TypedDict`.
- How to build explicit multi-node LangGraph workflows with conditional routing.
- How to integrate weather data from OpenWeather.
- How to compare explicit node routing vs LLM-driven tool calling (ReAct style).
- How to expose a LangGraph workflow through FastAPI with optional SSE-style streaming.

## Notebooks Overview

### 1) `langgraph_weather_or_joke.ipynb`

Builds a 3-node explicit graph:

- `router_node`: classifies intent (`weather` vs `joke`) and extracts `city` or `topic`.
- `weather_node`: calls OpenWeather API and returns a friendly response.
- `joke_node`: generates a short joke based on topic.

Graph shape:

`START -> router_node -> (weather_node | joke_node) -> END`

This notebook is the clearest starting point for understanding explicit routing with LangGraph.

### 2) `langgraph_explicit_vs_react.ipynb`

Compares two architectures on the same prompts:

- Approach A: explicit router + weather/joke nodes.
- Approach B: single ReAct node where the LLM decides whether to call tools (`get_weather`, `tell_joke`).

Includes a helper (`run_both`) to run the same input through both approaches and compare behavior/results.

### 3) `weather_node_2.ipynb`

Implements a weather-focused graph with:

- A JSON-only router instruction that classifies whether input is weather-related.
- `router` node that extracts route + city.
- `weather` node that handles missing city and API failures.

Useful for learning stricter routing contracts and robust input handling.

### 4) `weather_tutorial.ipynb`

Builds a simple 2-step graph and serves it via FastAPI:

- `agent` node extracts city from user text.
- `weather_tool` node fetches weather text via `OpenWeatherMapAPIWrapper`.
- `/ask` endpoint returns JSON response.
- Optional streamed response mode (`text/event-stream`) emits character chunks.

This notebook bridges notebook experimentation to API deployment patterns.

## Prerequisites

- Python 3.10+
- Jupyter
- OpenAI API key
- OpenWeather API key

Install core dependencies (adjust as needed per notebook):

```bash
pip install langgraph langchain langchain-openai langchain-community fastapi uvicorn python-dotenv requests
```

## Environment Variables

Create a `.env` file (or update notebook paths accordingly):

```env
OPENAI_API_KEY=your_openai_api_key
OPENWEATHER_API_KEY=your_openweather_api_key
```

Notes:

- Some code paths also check `OPENWEATHERMAP_API_KEY`.
- Several notebooks currently load `.env` from an absolute local path. If needed, replace with your own path or `load_dotenv()` from project root.

## Suggested Learning Order

1. `langgraph_weather_or_joke.ipynb`
2. `langgraph_explicit_vs_react.ipynb`
3. `weather_node_2.ipynb`
4. `weather_tutorial.ipynb`

## Run Tips

- Start with deterministic settings (`temperature=0`) while debugging router behavior.
- Validate weather API errors (bad city, missing key, quota) and keep graceful fallbacks.
- For FastAPI testing, run notebook cells first, then move code into a `.py` app module for production usage.

## License

This project is licensed under the terms in [LICENSE](LICENSE).
