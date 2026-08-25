# Unreachable Destination OD Flow

Interactive web map for exploring survey-reported unmet transit needs across origin ZIPs, counties, and desired destinations in the Houston-Galveston region.

This application visualizes where survey respondents live or start their trip, where they want to go, and how many survey mentions are associated with each origin-destination connection. The values represent unmet transit needs reported through the survey and should not be interpreted as observed or actual trip volumes.

## Features

- Origin-destination flow map with directional connection styling
- Selectable OD connections with survey mention popups
- County and ZIP/ZCTA summary layers
- Top destination panel that responds to active filters
- Filters for origin county, destination region, destination, age, disability, income, and race
- Built-in user guide popup

## Run Locally

Serve the folder with a local static server:

```powershell
python -m http.server 8001
```

Then open:

```text
http://localhost:8001/
```
