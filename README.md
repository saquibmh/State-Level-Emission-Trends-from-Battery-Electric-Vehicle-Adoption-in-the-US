# State-Level Emission Trends from Battery Electric Vehicle Adoption in the U.S.

This repository contains the code and data used to analyze household and vehicle emissions based on the National Household Travel Survey (NHTS) data. The analysis involves calculating gasoline and upstream emissions for conventional vehicles and upstream emissions for electric vehicles (EVs), then aggregating these emissions at the household level. The current emission file can be used to estimate current emissions, while the EV emission file can be used to calculate BEV emissions for current electricity generation across states along with the projected changes based on the Cambium model 2022 along with changes. The data folder consists of all datasets that have been used in the actual analysis

## Project Description

This project aims to estimate household-level emissions related to vehicle usage. It utilizes NHTS data to calculate emissions based on vehicle characteristics (year, fuel type, fuel efficiency, miles traveled) and regional electricity generation emission factors.

The process involves:

1.  **Data Loading and Initial Cleaning:** Loading vehicle, household, population, and income data, as well as regional electricity emission data.
2.  **Calculating Gasoline Vehicle Emissions:** Implementing a function to estimate gasoline emissions based on vehicle year and fuel efficiency (MPG or MPGe), then calculating annual emissions based on miles traveled.
3.  **Calculating EV Upstream Emissions:** Estimating upstream emissions for electric vehicles based on regional electricity emission factors and vehicle efficiency.
4.  **Merging Data:** Combining vehicle data with regional electricity emission data and household-level information.
5.  **Aggregating Emissions at Household Level:** Grouping data by household ID and summing the emissions from all vehicles within a household.
6.  **Incorporating Weights:** Using household weights to get representative estimates.
7.  **Calculating Weighted Averages:** Determining weighted average miles traveled, efficiency, and emissions per household.

## Code Description

* **Imports Libraries:** Imports necessary libraries such as pandas, numpy, scikit-learn, and tensorflow/keras (although the ML libraries are imported but not fully utilized in the provided snippet).
* **Loads Data:** Reads CSV files (`vehpub.csv`, `hhpub.csv`, `HH_by_State.csv`, `HHFAMINC.csv`, `eGRID2021_summary_tables.csv`) from a specified Google Drive path. **Note:** This path is specific to the user's Google Drive and needs to be adjusted for others to run the code.
* **`calculate_new_column_value` Function:** This function calculates the grams of CO2 equivalent per mile for gasoline vehicles based on their model year (`VEHYEAR`), fuel type (`HFUEL`), and fuel efficiency (`FEGEMPG` or `FEGEMPGA`). It uses different formulas based on the vehicle's model year.
* **Gas Emission Calculation:** Applies the `calculate_new_column_value` function to the `Vehicle` DataFrame to create a `Gas_Emission` column.
* **Annual Emission Calculation (Gasoline):** Calculates `Gasoline_emission`, `Annual_emission_upstream` (25% of gasoline emission), and `Annual_emission` (total for gasoline vehicles).
* **EV Upstream Emission Adjustment:** Adjusts the `EV_upstream_emission` based on `FEGEMPG` or `FEGEMPGA` for EV fuel types. **Note:** It seems the initial `EV_upstream_emission` column is expected to exist in the `Vehicle` DataFrame before this step, but its creation isn't shown in the provided snippet.
* **Merging with Electricity Data:** Merges the `Vehicle` DataFrame with the `elec` (eGRID data) DataFrame based on the state (`HHSTATE`).
* **Calculating Total EV Upstream Emissions:** Calculates `EV_upstream_emission_total` by multiplying `EV_upstream_emission` with the regional CO2e emission factor and a conversion factor.
* **Further Data Cleaning:** Removes rows with negative `FEGEMPGA` for EVs and sets `Annual_emission` and `EV_upstream_emission_total` to 0 for specific fuel types.
* **Household-Level Aggregation:** Groups the `merged_df` by `HOUSEID` and calculates the sum of `Annual_emission`, `EV_upstream_emission_total`, `BESTMILE`, and the mean of `FEGEMPG`. It also keeps the first occurrence of household-level features like `HHFAMINC`, `HHSTATE`, `WTHHFIN`, and `HBHUR`.
* **Merging with Household Data:** Merges the aggregated data with the `Household` DataFrame.
* **Incorporating Weights:** Calculates weighted emissions, miles, and efficiency using the household weight (`WTHHFIN_x`).
* **Final Aggregation and Calculation of Averages:** Groups the data (now in `summary_df`) and calculates the sum of weighted miles, weights, weighted efficiency, and weighted emissions. It then calculates weighted average miles per household, average efficiency, and average emissions.
* **Displaying Results:** Uses `display()` to show intermediate and final DataFrames.

## Data Sources

The analysis relies on the following datasets:

* **National Household Travel Survey (NHTS):**
    * `vehpub.csv`: Vehicle-level data.
    * `hhpub.csv`: Household-level data.
* **U.S. Census Bureau:**
    * `Household`: Household counts by state .
* **Environmental Protection Agency (EPA):**
    * `eGRID2021_summary_tables.csv`: Regional electricity generation emission factors.

**Note:** Access to these datasets may require registration or be publicly available on the respective organizations' websites. The specific versions used in this analysis are from eGRID.

## Setup

To run this code, you will need:

1.  **Python 3:** Ensure you have Python 3 installed.
2.  **Required Libraries:** Install the necessary Python libraries using pip:
    ```bash
    pip install pandas numpy matplotlib seaborn ipython
    ```
3.  **Data Files:** Download the NHTS and eGRID datasets from the data file.
4.  **Adjust File Paths:** Modify the file paths in the script to point to the location where you have saved the downloaded CSV files.

The script will output the processed DataFrames, including the final weighted average emissions per household.
