# LocaleLens

**Open-source neighbourhood intelligence for the UK.**

LocaleLens is a geospatial data platform that brings together UK property, planning, transport, amenity, and location data to help people understand an area from a single postcode.

Rather than only showing what exists in an area today, LocaleLens also analyses historical activity to surface transparent signals about how an area is changing.

## What LocaleLens Does

Enter a UK postcode and explore:

- Property sales and historical price trends
- Nearby shops, restaurants, pharmacies, GP surgeries, gyms, cinemas, and other amenities
- Parks and recreational spaces
- Bus stops, railway/Tube stations, routes, and major roads
- Nearby planning applications and developments
- Historical planning and property activity
- Explainable area-change signals

LocaleLens focuses on **evidence rather than opaque scores**.

Instead of:

> Area Score: 82/100

LocaleLens aims to tell you:

> Planning activity increased 24% over the recent period compared with its historical baseline.

and provide the underlying data used to calculate that signal.

## Why LocaleLens?

Information about a neighbourhood is often fragmented across property websites, government datasets, planning portals, transport systems, and mapping services.

LocaleLens explores what happens when those datasets are normalised around a common geographical model and made accessible through one API.

The project is also an exploration of:

- Geospatial data engineering
- Large dataset ingestion
- Spatial indexing
- PostgreSQL/PostGIS
- Historical data analysis
- Change detection
- API design
- Open government data

## Core Principles

### Search

A postcode is the primary entry point into the system.

### Explore

Understand properties, amenities, transport, parks, and other features surrounding a location.

### Developments

See planning applications and development activity taking place nearby.

### Change

Analyse historical data to identify measurable changes in an area.

### Explainability

Signals should always expose the evidence behind them rather than relying on unexplained ratings.

## Example

```text
LocaleLens
────────────────────────────

HA6 2XX

PROPERTY
Median sold price        £xxx,xxx
Transactions             xxx
Historical change        +x.x%

NEARBY
Supermarkets             5
Restaurants              21
Parks                    6
GP surgeries             3

TRANSPORT
Bus stops                14
Stations                 2

DEVELOPMENT
Planning applications    37
Approved                 19

AREA SIGNALS
↑ Planning activity      +24%
→ Transaction activity    +2%
↑ Median sold price       +7%
```

## Architecture

```text
                     UK DATA SOURCES
                           │
                           ▼
                    Ingestion Layer
                           │
                  Parse / Clean / Normalise
                           │
                           ▼
                 PostgreSQL + PostGIS
                           │
              ┌────────────┼────────────┐
              │            │            │
           Spatial      Analytics     Signals
           Queries       Engine        Engine
              │            │            │
              └────────────┼────────────┘
                           │
                         FastAPI
                           │
                           ▼
                       Next.js
                           │
                           ▼
                    LocaleLens UI
```

LocaleLens ingests and normalises data into its own geospatial model where permitted rather than relying on multiple external APIs for every user request.

## Technology

### Backend

- Python
- FastAPI
- Pydantic
- SQLAlchemy
- Alembic

### Data

- PostgreSQL
- PostGIS
- Polars/Pandas
- GeoPandas
- Shapely

### Frontend

- Next.js
- TypeScript
- MapLibre or Leaflet

### Infrastructure

- Docker
- GitHub Actions
- Pytest

## API

The initial API is organised around UK postcodes.

```http
GET /v1/areas/{postcode}

GET /v1/areas/{postcode}/properties
GET /v1/areas/{postcode}/property-history

GET /v1/areas/{postcode}/places
GET /v1/areas/{postcode}/transport

GET /v1/areas/{postcode}/planning
GET /v1/areas/{postcode}/planning-history

GET /v1/areas/{postcode}/signals
```

Spatial endpoints will support parameters such as radius, category, and result limits.

```http
GET /v1/areas/HA62XX/places?radius=1000&category=supermarket
```

## Area Signals

LocaleLens does not attempt to predict whether an area is a good investment or a good place to live.

Instead, it detects measurable changes.

Examples include:

- Increasing/decreasing planning activity
- Increasing/decreasing property transaction activity
- Changes in median sold prices
- Changes in development activity

Every signal should answer:

1. What changed?
2. By how much?
3. Compared with what?
4. What data produced the result?

## Project Status

🚧 **Currently under active development.**

The first release is being built as a focused MVP covering postcode search, property intelligence, nearby places, transport, planning activity, and area-change signals.

See [`PROJECT_SPEC.md`](PROJECT_SPEC.md) for the current scope.

## Open Source

LocaleLens is intended to be an open-source project.

Contributions, bug reports, feature suggestions, data-source improvements, and discussions about geospatial architecture are welcome as the project develops.

## Disclaimer

LocaleLens aggregates and analyses information from third-party and public datasets.

Information may be incomplete, delayed, inaccurate, or subject to licensing and source-specific limitations. LocaleLens should not be treated as legal, financial, surveying, planning, property valuation, or investment advice.

Always verify important information with the relevant authoritative source.