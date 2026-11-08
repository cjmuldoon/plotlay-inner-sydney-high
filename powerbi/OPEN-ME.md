# Opening the Power BI report

`InnerSydneyHigh.pbip` is a Power BI Project: the report (`InnerSydneyHigh.Report`, PBIR format) and the semantic model (`InnerSydneyHigh.SemanticModel`, TMDL) saved as text, so the repo can show every change. Power BI Desktop opens it like a `.pbix`.

## What you need

- Power BI Desktop for Windows, a recent version (2026). If Desktop is older than about mid 2025, turn on **File, Options and settings, Options, Preview features, Power BI Project (.pbip) save option** and **Store reports using enhanced metadata format (PBIR)**, then restart Desktop.
- The repo folder on the Windows machine, wherever you cloned or unzipped it.

## Open it

1. In Windows, open File Explorer and go to the `powerbi` folder, wherever you cloned or unzipped the repo.
2. Double-click `InnerSydneyHigh.pbip`, or in Desktop: **File, Open report, Browse reports**, file type *Power BI project files (\*.pbip)*.
3. The visuals are empty the first time. Select **Refresh** on the Home ribbon.
   - By default the data is read from the repo's public copy on GitHub (`DataFolder` is `https://raw.githubusercontent.com/cjmuldoon/plotlay-inner-sydney-high/main/data/`). If Desktop asks how to connect, choose **Anonymous** and **Connect**.
   - To read the files on disk instead: **Home, Transform data, Edit parameters**, set `DataFolder` to the full path of the repo's `data` folder **ending with a backslash**, then **OK** and **Apply changes**.
   - If Desktop warns about privacy levels, choose **Ignore privacy levels** for this file (all sources are public open data) or set them to Public.
4. After the refresh, with the **Year** slicer on **2025** (as the report opens; it is a dropdown that takes one year at a time), the **Overview** cards read 1,161 students, 24,892 m² of floor in use (estimated), 21.4 m² of floor space per student (estimated) and 4.9 m² of grounds per student, the README's figures. Pick **2020** in the Year dropdown and they read 147 students, 4,331 m² (the heritage buildings only) and 29.5 m². If they do, everything is joined correctly. If the slicer is ever cleared, the cards show the latest year, 2025.
5. The **Buildings** page reads 24,892 m² of floor space (estimated) against 18,154 m² of approved GFA: 1.37 times the total and 1.97 times the enclosed GFA.

## How the model fits together

`Buildings` has one row per building shape (the key the shapes carry: P the podium, T the tower, H1 to H4 the heritage buildings). `Site` is one row: the three NSW Cadastre lots as one shape. `Enrolments` (the school's headcount) and `FloorInUse` have one row per year and join to `Years`, which the year slicer uses. `ApprovedFloorAreas` holds the gross floor areas quoted from the approved plans (SSD 7610).

- The tower stands on the podium. **Site footprint m2** leaves it out (3,770 m² on 5,687 m² of grounds); every floor measure counts it.
- **Floor in use m2 estimated** adds the buildings in use in the year picked (`in_use_from`): the heritage buildings from 2020, the new building from 2021. `FloorInUse` carries the same figure from the plan, as a check (**Floor in use m2 plan table**).
- The per-student measures divide site totals by the year's headcount. There is no per-building student figure.
- Floor space is an estimate: each building's footprint times its storeys, counting every storey over the whole footprint (undercroft, terraces, stairs, lifts, plant, walls). That is why it is larger than the approved GFA.

## The map

- **Shape Map** (Overview page). The custom map, `data/geometry/buildings.topojson`, is saved inside the report (`InnerSydneyHigh.Report/StaticResources/RegisteredResources/buildings.topojson`, a copy that keeps only the building key), so the six buildings draw on open, coloured by part. The tower T is the last shape in the file, so it draws over the podium P.
- If the map shows the United States instead (an older Desktop can ignore a saved map): select it, then **Format visual** (the paintbrush), **Visual**, **Map settings**, **Map type: Custom map**, **Add map type** (or **Browse**), and choose `..\data\geometry\buildings.topojson`. Under **View map keys** the keys P, T and H1 to H4 should match. Projection: Mercator. If the Shape Map is not in the Visualizations pane, turn it on in **File, Options and settings, Options, Preview features, Shape map visual** and restart Desktop.
- If the Shape Map comes up empty after opening, drag the fields back: Location `Buildings[key]`, Legend `Buildings[part]`.
- Optional, **Azure Maps**: add a reference layer from `..\data\geometry\buildings.geojson`, join property `key`, map style **Grayscale (light)** or **Blank**. Do not publish satellite basemap screenshots.

## Save a .pbix too

After the refresh: **File, Save as**, type **Power BI file (\*.pbix)**, save as `powerbi\InnerSydneyHigh.pbix`. It includes the data.

## Publish (only if you choose to)

1. **Home, Publish**, sign in, pick a workspace.
2. In the service, the semantic model's **Settings, Data source credentials**: the GitHub address uses **Anonymous**. Scheduled refresh then works with no gateway.
3. **Publish to web** makes a public embed link; anyone with the link can see the report. Only if you intend it to be public.

## Shot list for the website and the video

Capture at 1920 × 1080 (Windows display scaling 100%), Plotlay theme on. Snipping Tool (Win+Shift+S) for stills, Win+Alt+R or Clipchamp for clips. Save to `powerbi/screenshots/`.

| # | Shot | How | Length |
|---|---|---|---|
| 1 | Overview | The Shape Map with the tower on the podium, the four cards for 2025 and the line by year | still |
| 2 | Step through the years | Pick 2020 to 2025 in the Year dropdown: the cards follow, floor per student peaks at 69.5 m² in 2021 and falls to 21.4 m² | 6 s clip |
| 3 | Pick a building | Click P on the map: the tooltip shows its floor estimate; the cards stay at site level | 3 s clip |
| 4 | Buildings | The table, the floor by building (P is 50.7% of the floor) and the cards against the approved GFA | still |
| 5 | Approved floor areas | Hover the GFA bars: covered terraces are counted as GFA in their own line | 3 s clip |
| 6 | Model view | The six tables, Enrolments and FloorInUse joined to Years | still |
| 7 | Power Query | **Transform data**: the `DataFolder` parameter and the `GetFile` function | still |

Keep the footer with the attribution visible in every shot.

## What's in the folder

- `InnerSydneyHigh.pbip`: open this.
- `InnerSydneyHigh.SemanticModel/definition/`: `tables/*.tmdl` (columns, measures, Power Query), `relationships.tmdl`, `expressions.tmdl` (the `DataFolder` parameter and a small `GetFile` function).
- `InnerSydneyHigh.Report/definition/`: one folder per page and visual (PBIR), `report.json` (the Plotlay theme registered).
- Pages: Overview, Buildings.
