# WeatherInfo-rpa
WeatherInfo is a UiPath Studio project that demonstrates end to end browser automation and structured data capture without relying on third party APIs. The workflow:
1.	Loads cities from an Excel DataTable (Sheet1 → cities).
2.	Navigates Microsoft Edge to Google and performs a natural language query per city.
3.	Extracts the temperature in °C, humidity, precipitation, wind, and the textual weather condition from the Google weather panel using resilient anchors.
4.	Writes results into the results worksheet for analysis or downstream processing.
This project is great for learning:
•	DataTable manipulation in UiPath
•	Robust UI selectors with anchors
•	Browser automation patterns with NApplicationCard
•	Iterating over Excel driven workloads
Inputs: cities_temp.xlsx with Sheet1!Cities
Outputs: cities_temp.xlsx → results sheet with columns: Cities, Temperature, Precipitation, Humidity, Wind, Sky.
Tip: if you intend to run this unattended at scale, migrate the scraping to a stable weather API or add retries/waits; DOM based scraping from consumer websites can be brittle.

**Features**
•	Read city names from cities_temp.xlsx (Sheet1)
•	Launch Microsoft Edge and query Google per city
•	Extract:
o	Temperature (°C)
o	Precipitation (%)
o	Humidity (%)
o	Wind (e.g., km/h, mph)
o	Sky/Condition (e.g., Clear, Cloudy)
•	Persist results to results sheet in the same workbook
•	Uses modern UiPath UIAutomationNext activities and resilient selectors

**Tech Stack**
•	UiPath Studio (tested on modern profiles)
•	Microsoft Edge (Chromium)
•	Excel via UiPath.Excel.Activities
•	UI scraping via UiPath.UIAutomationNext.Activities

**Referenced namespaces & packages (high level)**
•	UiPath.Excel.Activities, UiPath.UIAutomationNext.Activities, UiPath.System.Activities
•	Standard .NET assemblies used by UiPath workflows
The XAML defines the workflow class WeatherInfo and includes selectors for Google’s weather widgets (wob_tm, wob_pp, wob_hm, wob_ws, wob_dc).

**Project Structure**
WeatherInfo/
├─ WeatherInfo.xaml          # Main UiPath workflow (Sequence)
├─ cities_temp.xlsx          # Input/Output workbook
└─ README.md                 # This file

**Prerequisites**
•	UiPath Studio (2021+ recommended)
•	Microsoft Edge installed
•	Excel desktop (or Excel-compatible environment)
•	Internet access to reach Google
If you use a corporate machine, ensure Edge/Google access is allowed and the UiPath browser extension is enabled.

**Setup**
1.	Clone / Download this repository.
2.	Open UiPath Studio → Open → select WeatherInfo.xaml.
3.	Ensure the following dependencies are available (Manage Packages):
o	UiPath.Excel.Activities
o	UiPath.UIAutomationNext.Activities
o	UiPath.System.Activities
4.	Place an Excel file named cities_temp.xlsx in the project root with a Sheet1 that includes a column Cities. Example:
Cities
London
Warsaw
Lagos

**Run**
1.	In UiPath Studio, click Run.
2.	The bot launches Edge to https://www.google.com.
3.	For each row in Sheet1!Cities it types: what is the temperature in <City> and presses Enter.
4.	It collects weather values from the Google weather panel and assigns them into the current DataRow:
o	Temperature ← integer parse of wob_tm
o	Precipitation ← text from wob_pp
o	Humidity ← text from wob_hm
o	Wind ← text from wob_ws
o	Sky ← text from wob_dc
5.	After all rows are processed, it writes the enriched DataTable into results sheet of cities_temp.xlsx.
You should end up with a second sheet like:
Cities	Temperature	Precipitation	Humidity	Wind	Sky
London	16	20%	63%	11 km/h	Cloudy

**How it Works (workflow highlights)**
•	Input: ui:ReadRange reads Sheet1 into a DataTable variable cities.
•	Browser scope: uix:NApplicationCard targets Edge on Google.
•	Loop: ui:ForEachRow iterates rows.
•	Typing & submit: ui:TypeInto to Google’s search box → uix:NKeyboardShortcuts to press Enter.
•	Scraping: uix:NGetText elements anchored to Google’s weather widget IDs (wob_*).
•	Assignment: Assign activities write values back into the DataRow columns.
•	Output: ui:WriteRange to results sheet.

**Configuration**
•	Workbook path: Controlled by variable ExcelPath → default cities_temp.xlsx.
•	Sheet names: Input Sheet1, output results (hardcoded in activities).
•	Units: The workflow clicks/reads the °C view; you can extend it to °F if needed.

**Notes & Limitations**
•	Fragile UI: Google’s DOM/IDs and layout can change. If selectors like wob_tm shift, update the NGetText selectors.
•	Locale/Language: If your Google results aren’t in English, text anchors (e.g., “Precipitation”, “Wind”) may differ.
•	Rate-limiting & ToS: This uses interactive scraping of Google’s UI. Review Google’s Terms of Service for automated access and keep volumes modest. Consider adding delays/randomization.
•	Accuracy: Data reflects what Google shows at run time; values can differ from official meteorological APIs.
•	Headless: Designed for attended runs; headless/robotic execution may need extra hardening.

**Troubleshooting**
•	Search box not found: Re-record the selector for the textarea with id APjFqb or use a more robust anchor.
•	Weather panel missing: Google sometimes shows a different card—try adding waits/retries or searching weather <City> instead.
•	Parsing errors: Ensure Temperature column accepts integers and the scraped value is numeric (strip symbols if needed).
•	WriteRange overwrites: WriteRange writes the whole table; if you need appends, switch to AppendRange.

**Roadmap Ideas**
•	Add retry scopes and timeouts around each scrape.
•	Parameterize input/output sheet names and file paths.
•	Add logging and screenshots on error.
•	Support °F and unit normalization.
•	Swap to a weather API with keys for robustness (Open Meteo, OpenWeather, etc.).
•	Create a Dispatcher/Performer split (queue of cities; parallelization).

**Contributing**
PRs and issues welcome! Please describe the change and test with a small list of cities.

**License**
Specify the license of your choice (e.g., MIT, Apache 2.0). Add a LICENSE file accordingly.

 **Acknowledgements**
•	UiPath community docs and samples
•	Google weather panel DOM (wob_* elements)

 **Appendix** 
 Key Selectors (from XAML)
•	Search box: <webctrl id='APjFqb' tag='TEXTAREA' />
•	Temperature: <webctrl id='wob_tm' tag='SPAN' class='wob_t q8U8x' />
•	Precipitation: <webctrl id='wob_pp' tag='SPAN' />
•	Humidity: <webctrl id='wob_hm' tag='SPAN' />
•	Wind: <webctrl id='wob_ws' tag='SPAN' class='wob_t' />
•	Condition: <webctrl id='wob_dc' tag='SPAN' />
