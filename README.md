# Google ADK Agent — Travel Planner Agent Example

A compact example demonstrating how to compose Google ADK-style agents and grounding tools to build a travel-planning assistant.

## ✨ Project snapshot

- Purpose: Show how small, focused agents (news, places, inspiration) and deterministic tools (search, place lookup) can be composed into a useful assistant.
- Example scope: travel planning — find destinations, surface recent news/events, and suggest activities using tools.

## Key ideas

- Agent composition: small agents perform single responsibilities and the root agent orchestrates them.
- Tool grounding: deterministic tools (e.g., OpenStreetMap/Overpass, web grounding) provide up-to-date facts to agents.
- Extensible: add new tools or supporting agents without changing the orchestration logic.

## Files you care about

- `main.py` — entry point (prints a greeting; developer scaffold).
- `pyproject.toml` — project metadata and Python requirements.
- `travel_planner/tools.py` — tool implementations and wrappers (place lookups, search grounding, location helpers).
- `travel_planner/supporting_agents.py` — smaller helper agents (news_agent, places_agent, travel_inspiration_agent).
- `travel_planner/agents.py` — root agent composition and orchestration (entrypoint for the travel planner logic).

## Quick start

1. Ensure a compatible Python version (see `pyproject.toml` or your environment). Python 3.10+ is recommended.
2. Create and activate a virtual environment and install deps if you add any to `pyproject.toml`.

Run the minimal entrypoint:

```bash
python3 main.py
```

This prints a small greeting in the example repository. The actual agent orchestration logic lives under `travel_planner/` and can be wired into a CLI, web server, or notebook.

## How this example is organized

- Tools (`travel_planner/tools.py`) expose deterministic functions like:
  - `find_nearby_places_open` — query OpenStreetMap/Overpass for nearby POIs without paid APIs.
  - `google_search_grounding` — a wrapper to perform web-grounded lookups (model/tool integration point).
  - `location_search_tool` — a thin tool layer that agents call for structured place results.

- Supporting agents (`travel_planner/supporting_agents.py`) implement focused behaviors:
  - `news_agent` — gathers timely local news or alerts relevant to a trip.
  - `places_agent` — compiles lists of POIs, addresses, short descriptions.
  - `travel_inspiration_agent` — suggests destinations or itineraries by composing the above.

- Root agent (`travel_planner/agents.py`) receives user-level input and delegates to the supporting agents and tools, returning a combined, user-friendly response.

## Example usage (conceptual)

- Ask the root agent: “I want a 3-day trip to Kyoto in May — any events, things to do, and recommended neighborhoods?”
- Root agent calls `travel_inspiration_agent`, which:
  1. Uses `news_agent` + `google_search_grounding` to check for major events/closures.
 2. Uses `places_agent` + `find_nearby_places_open` to list curated points of interest.
 3. Assembles a short itinerary with links and practical notes.

The project keeps the tools returning structured JSON-like results so the agents can reason deterministically about the data.

## Extending the project

- Add a new tool: implement in `travel_planner/tools.py`, return structured results, then register it where agents expect tools.
- Add a new agent: create it in `travel_planner/supporting_agents.py` (or a new module) and add orchestration logic in `travel_planner/agents.py`.
- Integrate real LLMs: replace model placeholders or local/remote model calls inside agent implementations. Add API key injection and robust error handling.

## Design notes & recommendations

- Keep tools deterministic and well-typed (JSON schema) so agents can rely on consistent outputs.
- Cache and rate-limit external calls (Overpass/Google) and add retries for network resilience.
- Add unit tests for tool outputs and integration tests for agent orchestration.

## Contributing

Contributions welcome. Small suggestions:

- Add a bug report or feature request via issues.
- Provide a PR that adds a focused tool or supporting agent and tests.
