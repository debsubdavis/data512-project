# Memphis Wildfire Impact

# Goal

This project involves creating a smoke impact estimate for an assigned U.S. city based on wildfire data from the past 60 years. Using this estimate, comparisons are drawn against EPA AQI data, and a predictive model is developed to forecast smoke impacts through 2050. The analysis aims to support civic institutions in making informed decisions on wildfire impact mitigation

# Part 1 PDF

The PDF required for the reflection and visualization extended captions is saved here:

[Part 1 Writeup PDF](part1_reflection/Data512_Part_1_Writeup.pdf)

# Development Environment Setup

Please use the **[environment.yml](dev_env_setup/environment.yml)** and/or the **[requirements.txt](dev_env_setup/requirements.txt)** file to set up the development environment on your local machine.

Environment.yml and requirements.txt can be used to create a conda environment. Please reference the latest Anaconda documentation to determine how to install conda, and create an environment using an environment.yml file. As of 7 Oct 2024, this information is contained at these links:

- [Conda: Getting Started Guide](https://docs.conda.io/projects/conda/en/latest/user-guide/getting-started.html)
- [Conda: Managing Environments](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html)

# Data Flow Diagram

_Will add after Part 2 of Project Assignment_

# Stage 0: Data Acquisition

## _Stage 0 - Fire Data_

### **Description: ** Download the [combined wildland fire polygons dataset](https://www.sciencebase.gov/catalog/item/61aa537dd34eb622f699df81). Please reference the [Wildland Fire Polygon Metadata](raw/Wildland_Fire_Polygon_Metadata.xml) file for more information regarding all the variables included in the `combined wildland fire polygons dataset` file.

A version of this data is also saved in a Google Drive folder [here](https://drive.google.com/file/d/1xLqM2NlGolveJ4BPkoRlvvVKyQ50QO25/view?usp=drive_link). Please note this file has restricted access.

For the purposes of this project, the following variables are relevant from the dataset:

| Variable         | Description                                                                                                         |
| ---------------- | ------------------------------------------------------------------------------------------------------------------- |
| OBJECTID         | Unique identification for the polygon and its attributes.                                                           |
| USGS_Assigned_ID | Assigned unique identification for the polygon and its attributes, ensuring consistency if the dataset is modified. |
| Source_Datasets  | List of original datasets that contributed to the polygon or attributes, with the count of polygons in parentheses. |
| Fire_Year        | The year of the focal fire boundary as assigned during dataset creation.                                            |
| GIS_Acres        | The area of the fire polygon in acres, calculated using the Calculate Geometry tool in ArcGIS Pro.                  |
| Geometry         | List of latitude/longitude points representing the perimeter of each fire polygon.                                  |

Note: The the fire polygon data only (reliably) provides a year for each fire - it does not (reliably) provide specific start and end dates for the fire.

### _Notebook File:_ [_Stage 0 - Fire Data Acquisition_](stage0_fire_acquisition.ipynb)

### _Outputs_: [Filtered Fire Data JSON](https://drive.google.com/drive/folders/18XVSqhz8zUvdYDgk6LWIDjq-bf18rqgZ?usp=drive_link)

Since the file is so large, a version of this data is saved in the Google Drive folder linked above, instead of included in this repository. Please note this file has restricted access.

A single record in the output file is too long to include in this README. The relevant variables that will be used later on are describe above, and also include the following:

| Variable   | Description                                                                                                                      |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------- |
| `distance` | Distance to a reference location, such as a city, typically measured in miles or kilometers based on data source specifications. |

Note: this section may change after Part 2 of the project. (If Part 2 ends up requiring the use of additional variables from the orignal, raw, dataset)

## _Stage 0 - EPA AQI Data_

### **Description**

Obtain data for Memphis, TN for 1961 - 2022 via the US Environmental Protection Agency (EPA) Air Quality Service (AQS) API. This is a historical API and does not provide real-time air quality data. The [documentation](https://aqs.epa.gov/aqsweb/documents/data_api.html) for the API provides definitions of the different call parameter and examples of the various calls that can be made to the API.

For the purposes of this project the following variables are relevant:

| Variable           | Description                                                                                           |
| ------------------ | ----------------------------------------------------------------------------------------------------- |
| `site_code`        | Unique identifier code for the monitoring site where the data was collected.                          |
| `site_name`        | Name of the monitoring site location.                                                                 |
| `pollutant_number` | Numeric code associated with the specific pollutant being measured.                                   |
| `pollutant_name`   | Common name of the pollutant measured at the monitoring site (e.g., PM2.5, Ozone).                    |
| `local_date`       | Date of the measurement recorded in the local time zone of the monitoring site.                       |
| `aqi`              | Air Quality Index (AQI) value, representing the pollution level based on the pollutant concentration. |

### _Notebook File:_ [_Stage 1 - EPA AQI Data Acquisition_](stage1_epa_processing.ipynb)

### _Outputs:_ [epa_aqi_csv](intermediate/stage0-output/epa_aqi.csv)

### _epa_aqi_csv Schema_:

| Variable           | Data Type | Description                                                                                    |
| ------------------ | --------- | ---------------------------------------------------------------------------------------------- |
| `site_code`        | `string`  | Unique code identifying the monitoring site where the pollutant data was collected.            |
| `site_name`        | `string`  | Name of the monitoring site location. (Empty if not provided in the data.)                     |
| `pollutant_number` | `integer` | Numeric code that uniquely identifies the pollutant being measured.                            |
| `pollutant_name`   | `string`  | Common name of the pollutant measured (e.g., Sulfur dioxide).                                  |
| `local_date`       | `string`  | Date when the measurement was recorded, formatted as YYYYMMDD.                                 |
| `aqi`              | `integer` | Air Quality Index (AQI) value, indicating pollution level for the recorded date and pollutant. |

# Stage 1: Data Processing

## _Stage 1 - Fire Data_

### _Description:_

Processes fire data to estimate the smoke impact on Memphis based on fire size, distance, and estimated duration. It first loads fire attributes, checks for data consistency, and calculates a “smoke impact” score for each fire by normalizing fire size and distance factors. Smoke impacts are then amortized over each fire’s estimated duration, and cumulative yearly smoke impacts are computed and saved for further analysis.

For more specific information about the smoke impact calculation, please reference the stage1_fire_processing notebook file linked below.

### _Notebook file:_ [Stage 1 - Fire Data Processing](stage1_fire_processing.ipynb)

### _Inputs:_ [Filtered Fire Data JSON](https://drive.google.com/file/d/1DtCaovDwGylEoWULV8GdJHHAmsSFXfDr/view?usp=drive_link)

Schema for Filtered Fire Data JSON is the same as outlined above

### _Outputs_

- [si_per_FIRE](intermediate/stage1-output/smoke_impacts_per_FIRE.csv)
- [si_per_YEAR](intermediate/stage1-output/smoke_impacts_per_YEAR.csv)

#### _si_per_DAY Schema_

Note: Used chatGPT 4o model to help me generate the table below

| Variable                       | Description                                                                              |
| ------------------------------ | ---------------------------------------------------------------------------------------- |
| `OBJECTID`                     | Unique identifier for each fire record.                                                  |
| `USGS_Assigned_ID`             | USGS-assigned unique identifier for the fire record.                                     |
| `Assigned_Fire_Type`           | Type of fire, e.g., "Wildfire" or "Prescribed Burn."                                     |
| `fire_year`                    | Year when the fire occurred.                                                             |
| `Fire_Polygon_Tier`            | Quality or reliability tier for the fire polygon data.                                   |
| `Fire_Attribute_Tiers`         | Quality tiers assigned to various attributes for this fire.                              |
| `total_acres_burned`           | Total area burned in acres as calculated from GIS data.                                  |
| `GIS_Hectares`                 | Total area burned in hectares as calculated from GIS data.                               |
| `Source_Datasets`              | Source datasets for the fire record (e.g., NIFC Interagency Fire Perimeter History).     |
| `Listed_Fire_Types`            | Fire types associated with this record, including confidence indicators.                 |
| `Listed_Fire_Names`            | Name(s) of the fire, as recorded in the source data.                                     |
| `Listed_Fire_Codes`            | Code associated with the fire, if provided.                                              |
| `Listed_Fire_IDs`              | Additional fire identifiers, if any.                                                     |
| `Listed_Fire_IRWIN_IDs`        | IRWIN IDs for the fire, if available.                                                    |
| `Listed_Fire_Dates`            | Dates associated with the fire (e.g., discovery, control, or containment dates).         |
| `Listed_Fire_Causes`           | Cause(s) of the fire, if provided (e.g., Natural, Human).                                |
| `Listed_Fire_Cause_Class`      | Classification of the fire's cause.                                                      |
| `Listed_Rx_Reported_Acres`     | Reported acres for prescribed burns, if applicable.                                      |
| `Listed_Map_Digitize_Methods`  | Method used to digitize the fire polygon data.                                           |
| `Listed_Notes`                 | Additional notes related to the fire record.                                             |
| `Processing_Notes`             | Notes on data processing for this fire record.                                           |
| `Wildfire_Notice`              | Notice regarding the accuracy and completeness of wildfire data, especially before 1984. |
| `Prescribed_Burn_Notice`       | Notice on completeness of prescribed burn data, particularly for older records.          |
| `Wildfire_and_Rx_Flag`         | Flag indicating whether the record is for a wildfire or prescribed burn.                 |
| `Overlap_Within_1_or_2_Flag`   | Flag for possible spatial overlap with other fire polygons.                              |
| `Circleness_Scale`             | A scale measuring the "circularity" of the fire polygon.                                 |
| `Circle_Flag`                  | Flag indicating whether the polygon closely approximates a circle.                       |
| `Exclude_From_Summary_Rasters` | Indicates whether to exclude this polygon from summary raster data.                      |
| `Shape_Length`                 | Length of the perimeter of the fire polygon in GIS units.                                |
| `Shape_Area`                   | Area of the fire polygon in GIS units.                                                   |
| `distance`                     | Distance from the fire to a reference location, such as a city.                          |
| `smoke_impact`                 | Calculated smoke impact based on fire size and proximity.                                |
| `fire_duration`                | Estimated duration of the fire in days.                                                  |
| `amortized_smoke_impact`       | Smoke impact amortized over the estimated fire duration.                                 |

#### _si_per_YEAR Schema_

## _Stage 1 - EPA AQI Data_

### _Description_

Processes EPA AQI data by loading pollutant measurements, extracting date components, and filtering for fire season dates (May through October). It calculates daily and annual average AQI values specifically for the fire season, saving both daily and yearly summaries to CSV files for further analysis. The code also includes print statements to display column headers and basic dataset information at each step.

### _Notebook file:_ [Stage 1 - AQI Data Processing](stage1_epa_processing.ipynb)

### _Inputs:_ [epa_aqi_csv](intermediate/stage0-output/epa_aqi.csv)

Schema for epa_aqi_csv is the same as the schema outlined in Stage 0 above

### _Outputs_

Both are filtered to only include data from May 1 - October 31 for each year included in the files

- [epa_AQI_per_DAY](intermediate/stage1-output/epa_AQI_per_DAY.csv)
- [epa_AQI_per_YEAR](intermediate/stage1-output/epa_AQI_per_YEAR.csv)

#### _epa_AQI_per_DAY Schema_

| Variable           | Data Type | Description                                                                                           |
| ------------------ | --------- | ----------------------------------------------------------------------------------------------------- |
| `site_code`        | `integer` | Unique identifier for the monitoring site where the data was collected.                               |
| `site_name`        | `string`  | Name of the monitoring site location. (Empty if not provided in the data.)                            |
| `pollutant_number` | `integer` | Numeric code associated with the specific pollutant being measured.                                   |
| `pollutant_name`   | `string`  | Common name of the pollutant measured at the monitoring site (e.g., Sulfur dioxide).                  |
| `local_date`       | `string`  | Date of the measurement recorded in YYYYMMDD format.                                                  |
| `aqi`              | `integer` | Air Quality Index (AQI) value, representing the pollution level based on the pollutant concentration. |
| `year`             | `integer` | Year the measurement was taken, extracted from `local_date`.                                          |
| `month`            | `integer` | Month the measurement was taken, extracted from `local_date`.                                         |
| `day`              | `integer` | Day the measurement was taken, extracted from `local_date`.                                           |

#### _epa_AQI_per_YEAR Schema_

| Variable                  | Data Type | Description                                                                                                        |
| ------------------------- | --------- | ------------------------------------------------------------------------------------------------------------------ |
| `year`                    | `integer` | Year of the air quality measurement, extracted from `local_date`.                                                  |
| `pollutant_number`        | `integer` | Numeric code associated with the specific pollutant measured.                                                      |
| `pollutant_name`          | `string`  | Common name of the pollutant measured (e.g., Sulfur dioxide).                                                      |
| `average_aqi_fire_season` | `float`   | Average Air Quality Index (AQI) value for the pollutant during the fire season (May to October) in the given year. |

# Stage 2: Combined Analysis

### _Description_

Analyzes wildfire and air quality data to assess the impact of wildfires on air quality in Memphis. It processes distance data, fire statistics, and smoke impact estimates, filtering data for the fire season (May to October) and calculating annual averages. The script then visualizes the results through various plots, comparing scaled smoke impact estimates with AQI measurements over time and saving the output for further analysis.

### _Notebook file:_ [Stage 2 - Combined Analysis](stage2_combined_analysis.ipynb)

### _Inputs_

- [si_per_FIRE](intermediate/stage1-output/smoke_impacts_per_FIRE.csv)
- [si_per_YEAR](intermediate/stage1-output/smoke_impacts_per_YEAR.csv)
- [epa_AQI_per_YEAR](intermediate/stage1-output/epa_AQI_per_YEAR.csv)
- [Filtered Fire Data JSON](https://drive.google.com/file/d/1DtCaovDwGylEoWULV8GdJHHAmsSFXfDr/view?usp=drive_link)

All schemas described above

### _Outputs_

- [SCALED_smoke_impacts_per_YEAR](intermediate/stage2-output/SCALED_smoke_impacts_per_YEAR.csv)

#### SCALED_smoke_impacts_per_YEAR schema

| Variable                        | Data Type | Description                                                                                   |
| ------------------------------- | --------- | --------------------------------------------------------------------------------------------- |
| `year`                          | `integer` | Calendar year for the record, covering the range of analysis years.                           |
| `fire_year`                     | `float`   | Year when the fire occurred (matches `year` unless data is filled in based on other sources). |
| `total_amortized_smoke_impact`  | `float`   | Sum of smoke impacts amortized over fire durations for all fires in the given year.           |
| `total_fire_duration`           | `float`   | Cumulative fire duration across all fires in the year, measured in days.                      |
| `total_acres_burned`            | `float`   | Total acres burned by wildfires in the year.                                                  |
| `avg_daily_smoke_impact`        | `float`   | Average daily smoke impact from wildfires, calculated across all days in the year.            |
| `scaled_avg_daily_smoke_impact` | `float`   | Scaled average daily smoke impact, adjusted to a align with air quality index comparisons.    |

# Stage 3: Model Training

### _Description_

Trains a model to predict the impact of wildfire smoke using a Gradient Boosting Regressor with optimized hyperparameters. It preprocesses and normalizes wildfire data, removes outliers, and applies bootstrapping with cross-validation to identify the model with the best performance. The final model is saved for future use, and residuals are plotted to visualize the model's accuracy in predicting smoke impact.

### _Notebook file:_ [Stage 3 - Model Training](stage3_model_training.ipynb)

### _Inputs_

- [si_per_FIRE](intermediate/stage1-output/smoke_impacts_per_FIRE.csv)
- [SCALED_smoke_impacts_per_YEAR](intermediate/stage2-output/SCALED_smoke_impacts_per_YEAR.csv)

Schemas described above

### _Outputs_

- [normalize_and_weight_features](intermediate/stage2-output/normalize_and_weight_features.csv)
- [best_model_file](https://drive.google.com/file/d/1hLMsotlUFZAgxQNI9mniyg5IJ3m-PBxO/view?usp=drive_link)

Model file saved in Google Drive folder to keep repo size from becoming too inflated

#### normalize_and_weight_features schema

| Variable                 | Data Type | Description                                                                                       |
| ------------------------ | --------- | ------------------------------------------------------------------------------------------------- |
| `acres_weight`           | `integer` | Weight assigned to the total acres burned, used to normalize the impact of fire size.             |
| `dist_weight`            | `integer` | Weight assigned to the distance from Memphis, used to normalize the impact of distance.           |
| `max_total_acres_burned` | `float`   | Maximum total acres burned in the dataset, used as a normalization factor for future predictions. |

# Stage 4: Model Predictions

### _Description_

Generates future predictions for wildfire size, distance from Memphis, and associated smoke impact from 2025 to 2050. It calculates 5-year rolling averages of historical data to predict future fire trends, then normalizes and weights these predictions before applying a trained Gradient Boosting model to estimate smoke impact. Finally, the predicted smoke impact is visualized over time to analyze potential trends through 2050.

### _Notebook file:_ [Stage 4 - Model Predictions](stage4_model_predictions.ipynb)

### _Inputs_

- [normalize_and_weight_features](intermediate/stage2-output/normalize_and_weight_features.csv)
- [best_model_file](https://drive.google.com/file/d/1hLMsotlUFZAgxQNI9mniyg5IJ3m-PBxO/view?usp=drive_link)

Schemas described above
