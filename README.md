# hydroq-api

[![PyPI](https://img.shields.io/pypi/v/hydroq-api.svg)](https://pypi.org/project/hydroq-api/)
[![Tests](https://github.com/lvg77/hydroq-api/actions/workflows/test.yml/badge.svg)](https://github.com/lvg77/hydroq-api/actions/workflows/test.yml)
[![Changelog](https://img.shields.io/github/v/release/lvg77/hydroq-api?include_prereleases&label=changelog)](https://github.com/lvg77/hydroq-api/releases)
[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](https://github.com/lvg77/hydroq-api/blob/main/LICENSE)

Get your Hydro Quebec electiricty data

The project is influenced by [hydroqc](https://gitlab.com/hydroqc/hydroqc) package. You should use that library if you need more detailed implementation of the HQ API.

## Installation

Install this library using `pip`:
```bash
pip install hydroq-api
```
## Usage

The hydroq-api library provides a simple interface to authenticate and interact with Hydro Quebec’s electricity data API. At its core, the library offers the `HydroQuebec` class which handles the login flow, token refreshing, and session management for accessing additional web-based endpoints.

### Notes
- The library supports retrieving electricity usage data from Hydro Quebec
- Daily usage can be retrieved for approximately 2 years of historical data plus one additional year worth of daily data as "same day" last data reference
- Hourly usage can be retrieved for 24 hours
- Monthly usage can be retrieved for approximately 1 year of historical data plus one additional year worth of monthly data as "same month" last data reference
- Authentication is required before retrieving usage data
- Ensure you have a valid Hydro Quebec account to use this library

Below are some examples demonstrating how to use the library.

### Basic Authentication and Data Retrieval

First, install the library using pip:

```bash
pip install hydroq-api
```
Then in your Python code, you can authenticate and use the client as follows:
```python
from hydroq_api import HydroQuebec

# Replace with your actual Hydro Quebec credentials
email = "YOUR_EMAIL@example.com"
password = "YOUR_PASSWORD"

# Initialize the HydroQuebec client
client = HydroQuebec(email, password)

# Login to obtain access tokens and set up your session
client.login()

# Now you can access the access token and other properties
print("Access Token:", client.access_token)
```

### Refreshing Tokens
The library handles token refreshes under the hood. If you need to manually refresh the token (for example, if you detect it is about to expire), you can call the refresh method:
```python
from hydroq_api import HydroQuebec

client = HydroQuebec("YOUR_EMAIL@example.com", "YOUR_PASSWORD")
client.login()

# Suppose you want to refresh the token proactively:
if client.refresh_token:
    # This is an internal call to refresh the token.
    # In typical usage, token refreshing happens automatically as needed.
    success = client._refresh_token()
    if success:
        print("New Access Token:", client.access_token)
```
### Retrieving Consumption Data
The main functionality of the hydroq-api package is to retrieve your electricity consumption data at different granularities: hourly, daily, and monthly. After authenticating and setting up your client, you can call the relevant methods to fetch the desired data.
```python
from hydroq_api import HydroQuebec

# Initialize and login to the HydroQuebec client
client = HydroQuebec("YOUR_EMAIL@example.com", "YOUR_PASSWORD")
client.login()

# Retrieve hourly consumption data
hourly_data = client.get_hourly_usage(date="2023-09-01")
print("Hourly Data:", hourly_data)

# Retrieve daily consumption data
daily_data = client.get_daily_usage(start_date="2023-09-01", end_daet="2023-09-30")
print("Daily Data:", daily_data)

# Retrieve monthly consumption data for a particular year
monthly_data = client.get_monthly_usage()
print("Monthly Data:", monthly_data)
```
Those mehtods will return the data as python dictionaries.
Here is an example of the data structure returned by the `get_daily_usage` method:
```
{'success': True,
 'results': [{'courant': {'dateJourConso': '2025-02-17',
    'zoneMessageHTMLQuot': None,
    'consoRegQuot': 122.05,
    'consoHautQuot': 0,
    'consoTotalQuot': 122.05,
    'codeConsoQuot': 'R',
    'tempMoyenneQuot': -12,
    'codeTarifQuot': 'D',
    'affichageTarifFlexQuot': False,
    'indTempTarifBiEnergieQuot': False,
    'codeEvenementQuot': ''},
   'compare': {'dateJourConso': '2024-02-19',
    'zoneMessageHTMLQuot': None,
    'consoRegQuot': 81.83,
    'consoHautQuot': 0,
    'consoTotalQuot': 81.83,
    'codeConsoQuot': 'R',
    'tempMoyenneQuot': -10,
    'codeTarifQuot': 'D',
    'affichageTarifFlexQuot': False,
    'indTempTarifBiEnergieQuot': False,
    'codeEvenementQuot': ''}},
    ...
```

## Development

To contribute to this library, first checkout the code. Then create a new virtual environment:
```bash
cd hydroq-api
python -m venv venv
source venv/bin/activate
```
Now install the dependencies and test dependencies:
```bash
python -m pip install -e '.[test]'
```
To run the tests:
```bash
python -m pytest
```
