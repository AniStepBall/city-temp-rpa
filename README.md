# WeatherInfo-rpa
WeatherInfo is a lightweight UiPath automation that turns a simple Excel list of city names into a live weather snapshot. It opens Edge, asks Google for each city’s weather, scrapes key metrics (°C, precipitation, humidity, wind, conditions), and saves everything back to the file—no external API keys required.


**Features**
Read city names from cities_temp.xlsx (Sheet1)

Launch Microsoft Edge and query Google per city

Extract

Temperature (°C)

Precipitation (%)

Humidity (%)

Wind (e.g., km/h, mph)

Sky/Condition (e.g., Clear, Cloudy)

Persist results to results sheet in the same workbook

Uses modern UiPath UIAutomationNext activities and resilient selectors


**Tech Stack**

UiPath Studio (tested on modern profiles)

Microsoft Edge (Chromium)

Excel via UiPath.Excel.Activities

UI scraping via UiPath.UIAutomationNext.Activities

Referenced namespaces & packages (high level)

UiPath.Excel.Activities, UiPath.UIAutomationNext.Activities, UiPath.System.Activities

Standard .NET assemblies used by UiPath workflows

The XAML defines the workflow class WeatherInfo and includes selectors for Google’s weather widgets (wob_tm, wob_pp, wob_hm, wob_ws, wob_dc).

**Project Structure**

WeatherInfo/
├─ WeatherInfo.xaml          # Main UiPath workflow (Sequence)
├─ cities_temp.xlsx          # Input/Output workbook
└─ README.md                 # This file

**Prerequisites**

UiPath Studio (2021+ recommended)

Microsoft Edge installed

Excel desktop (or Excel-compatible environment)

Internet access to reach Google

If you use a corporate machine, ensure Edge/Google access is allowed and the UiPath browser extension is enabled.


**Setup**

Clone / Download this repository.

Open UiPath Studio → Open → select WeatherInfo.xaml.

Ensure the following dependencies are available (Manage Packages):

UiPath.Excel.Activities

UiPath.UIAutomationNext.Activities

UiPath.System.Activities

Place an Excel file named cities_temp.xlsx in the project root with a Sheet1 that includes a column Cities. Example:

Cities

London

Warsaw

Lagos
