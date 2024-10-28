# Memphis Wildfire Impact

## Goal

This project involves creating a smoke impact estimate for an assigned U.S. city based on wildfire data from the past 60 years. Using this estimate, comparisons are drawn against EPA AQI data, and a predictive model is developed to forecast smoke impacts through 2050. The analysis aims to support civic institutions in making informed decisions on wildfire impact mitigation

## Development Environment Setup

Please use the **[environment.yml](dev_env_setup/environment.yml)** and/or the **[requirements.txt](dev_env_setup/requirements.txt)** file to set up the development environment on your local machine.

Environment.yml and requirements.txt can be used to create a conda environment. Please reference the latest Anaconda documentation to determine how to install conda, and create an environment using an environment.yml file. As of 7 Oct 2024, this information is contained at these links:

- [Conda: Getting Started Guide](https://docs.conda.io/projects/conda/en/latest/user-guide/getting-started.html)
- [Conda: Managing Environments](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html)

## Data Flow Diagram

## Stage 0: Data Acquisition

### _Stage 0 - Fire Data_

**Description** Download the [combined wildland fire polygons dataset](https://www.sciencebase.gov/catalog/item/61aa537dd34eb622f699df81). Please reference the [Wildland Fire Polygon Metadata](raw/Wildland_Fire_Polygon_Metadata.xml) file for more information regarding all the variables included in the `combined wildland fire polygons dataset` file.

For the purposes of this project, the following variables are relevant:

| Variable         | Description                                                                                                         |
| ---------------- | ------------------------------------------------------------------------------------------------------------------- |
| OBJECTID         | Unique identification for the polygon and its attributes.                                                           |
| USGS_Assigned_ID | Assigned unique identification for the polygon and its attributes, ensuring consistency if the dataset is modified. |
| Source_Datasets  | List of original datasets that contributed to the polygon or attributes, with the count of polygons in parentheses. |
| Fire_Year        | The year of the focal fire boundary as assigned during dataset creation.                                            |
| GIS_Acres        | The area of the fire polygon in acres, calculated using the Calculate Geometry tool in ArcGIS Pro.                  |
| Geometry         | List of latitude/longitude points representing the perimeter of each fire polygon.                                  |

Note: The the fire polygon data only (reliably) provides a year for each fire - it does not (reliably) provide specific start and end dates for the fire.

_Notebook File:_ [_Stage 0 - Fire Data Acquisition_](stage0_fire_acquisition.ipynb)

### _Stage 0 - EPA AQI Data_

## Stage 1: Data Processing

### _Stage 1 - Fire Data_

### _Stage 1 - EPA AQI Data_

## Stage 2: Data Analysis
