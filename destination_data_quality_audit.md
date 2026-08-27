# Destination Data Quality Audit

Source checked: `od_data.json`

Boundary checked: `county_boundaries.geojson`

Cleanup applied to `od_data.json`: yes. The original survey destination names were preserved. Canonical review fields were added to every record, and active coordinates were corrected for the suspected bad geocodes while preserving the previous coordinate values in `destination_lat_original` and `destination_lon_original`.

County boundary set:
Matagorda, Wharton, Liberty, Chambers, Brazoria, Austin, Montgomery, Fort Bend, Waller, Galveston, Harris, Walker, Colorado.

## Summary

- Trip records checked: 729
- Unique destination coordinate points: 94
- Missing destination latitude/longitude values: 0
- Unique destination points outside the 13-county boundary before cleanup: 14
- Records represented by outside-boundary destinations before cleanup: 51
- Unique destination points outside the 13-county boundary after cleanup: 8
- Records represented by outside-boundary destinations after cleanup: 42

## Outside 13-County Boundary Before Cleanup

These destinations are outside the 13-county polygon set. Some are likely valid out-of-region destinations; others look like geocoding errors because the label suggests a Houston-area street, corridor, or neighborhood.

| Records | Destination | Current latitude | Current longitude | Recommendation |
|---:|---|---:|---:|---|
| 17 | Dallas | 32.777977 | -96.796215 | Keep as out-of-region destination. |
| 15 | Austin | 30.264979 | -97.746598 | Keep as out-of-region destination. |
| 5 | San Antonio | 29.425171 | -98.494614 | Keep as out-of-region destination. |
| 4 | Pelican Island | 28.378150 | -96.392160 | Correct coordinate to Galveston/Pelican Island area, or group to Galveston. |
| 1 | Beaumont | 30.086152 | -94.102010 | Keep as out-of-region destination. |
| 1 | Brenham | 30.160692 | -96.398466 | Keep as out-of-region destination unless survey scope should exclude it. |
| 1 | Copperfield | 30.676827 | -96.292597 | Correct coordinate to NW Harris County. Current point is near College Station area. |
| 1 | El Paso | 31.760106 | -106.492292 | Keep as out-of-region destination. |
| 1 | Fort Worth | 32.749904 | -97.330339 | Keep as out-of-region destination. |
| 1 | Fuqua Street | 27.998526 | -97.070435 | Correct coordinate to Houston/Harris County if this means Houston's Fuqua Street. |
| 1 | Kirkwood Road | 29.270218 | -98.703931 | Correct coordinate to Houston/Harris County if this means Houston's Kirkwood Road. |
| 1 | Shepherd Drive | 30.929131 | -97.523215 | Correct coordinate to Houston/Harris County if this means Houston's Shepherd Drive. |
| 1 | Texarkana | 33.442210 | -94.064080 | Keep as out-of-region destination. |
| 1 | Westheimer Road | 33.164710 | -97.106548 | Correct coordinate to Houston/Harris County if this means Houston's Westheimer Road. |

## Remaining Outside 13-County Boundary After Cleanup

The remaining outside-boundary destinations appear to be valid out-of-region cities rather than geocoding errors.

| Records | Destination |
|---:|---|
| 17 | Dallas |
| 15 | Austin |
| 5 | San Antonio |
| 1 | Beaumont |
| 1 | Brenham |
| 1 | El Paso |
| 1 | Fort Worth |
| 1 | Texarkana |

## Inside Boundary But Still Suspicious

These pass the 13-county spatial test but still deserve manual review because the label and point appear inconsistent.

| Records | Destination | Current county by point | Current latitude | Current longitude | Recommendation |
|---:|---|---|---:|---:|---|
| 3 | MD Anderson Cancer Center | Harris County | 30.001200 | -95.561300 | Correct to Texas Medical Center area, then group under Texas Medical Center or keep as a named medical destination. |
| 1 | Greenspoint | Harris County | 29.745102 | -95.373969 | Correct to north Houston/Greenspoint area. Current point is near central Houston. |
| 1 | Garth Road | Chambers County | 29.762024 | -94.481734 | Review. If the respondent meant Baytown/Garth Road, coordinate should likely be farther west. |

## Recommended Canonical Destination Groups

Use a separate field such as `destination_group` or an external lookup table rather than overwriting the original `destination`. This keeps the raw survey response available while allowing cleaner summaries and map filters.

### Strong Merge Recommendations

These are close together and represent either the same named destination, a venue within a broader destination, or a more recognizable parent place.

| Current destination | Recommended group |
|---|---|
| NRG Stadium | NRG Park |
| Kemah Boardwalk | Kemah |
| The Strand, Galveston | Galveston |
| Galveston Seawall | Galveston |
| Pelican Island | Galveston |
| UTMB | Galveston or UTMB/Galveston Medical |
| Moody Gardens | Galveston or Moody Gardens |
| MD Anderson Cancer Center | Texas Medical Center |
| Toyota Center | Downtown Houston |
| Minute Maid Park | Downtown Houston |
| Deerbrook Mall | Humble |

### Conditional Merge Recommendations

Use these only if the map should summarize broad travel markets instead of preserving neighborhood-level detail.

| Current destination | Recommended group | Notes |
|---|---|---|
| Midtown Houston | Downtown Houston | Good for broad central-Houston summaries. Keep separate for neighborhood detail. |
| Montrose | Central Houston | Keep separate if neighborhood detail is important. |
| Houston Museum District | Central Houston or Texas Medical Center/Museum District | Recognized district; merging loses useful detail. |
| Hermann Park | Texas Medical Center/Museum District | Close to TMC and Museum District. |
| The Galleria | Uptown/Galleria | Keep separate from Downtown Houston. |
| Greenway Plaza | Greenway/Upper Kirby | Keep separate from Downtown Houston. |
| Gulfton | Southwest Houston | Do not merge with Galleria unless using very broad zones. |
| Sharpstown | Southwest Houston | Do not merge with Galleria unless using very broad zones. |
| Richmond | Richmond/Rosenberg | Useful combined Fort Bend market. |
| Rosenberg | Richmond/Rosenberg | Useful combined Fort Bend market. |
| Missouri City | Missouri City/Stafford | Optional; city-level data should keep separate. |
| Stafford | Missouri City/Stafford | Optional; city-level data should keep separate. |

### Do Not Merge Automatically

These are similar by name or close by distance but represent distinct recognized destinations.

| Destination A | Destination B | Reason |
|---|---|---|
| University of Houston | University of Houston-Clear Lake | Different campuses and travel markets. |
| Downtown Houston | Texas Medical Center | Close enough to cluster by distance in a broad analysis, but meaningfully distinct destinations. |
| Downtown Houston | The Galleria | Distinct major activity centers. |
| NASA Johnson Space Center | League City | Close regionally, but JSC is a specific employment/visitor destination. |
| NASA Johnson Space Center | Clear Lake | Keep separate unless using a broad Clear Lake/NASA zone. |
| William P. Hobby Airport | Pasadena | Distinct destination types. |
| George Bush Intercontinental Airport | Humble | Distinct destination types. |

## Recommended Workflow

1. Correct the suspicious coordinates first.
2. Add a lookup table with `destination`, `destination_group`, `review_status`, and notes.
3. Use `destination_group` for top-destination charts and broad filters.
4. Keep original `destination` available in popups and detailed filters.
5. Keep true out-of-region cities as outside-region destinations unless the analysis scope should exclude them entirely.
