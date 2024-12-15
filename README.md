# E2E Data Pipeline and Reporting for NBA Statistics

## Introduction

This project delivers a streamlined solution for NBA statistics, offering a simplified version of key and advanced metrics for teams and players, without replicating the official [NBA Stats](https://www.nba.com/stats). The primary goal is to provide an end-to-end template, from data warehousing to business intelligence reports, rather than creating a production-ready pipeline.

[Live Dashboard URL](insert url here)

## Project Requirements
- Scalable and flexible data pipeline
- Daily batch processing from reliable public sources
- Interactive, publicly accessible web-based dashboard
- Low to no cost

## Detailed Design
![Diagram](https://github.com/mbo0000/nba_e2e_data_pipeline/blob/main/images/pipeline.png)

### 1. Snowflake Management and Config
This template is *OPTIONAL* for personal project but highly recommend for a group based or a bigger project. Tho the config is specific to this project, it can be adjusted/enhanced to your projects needs.

With account admin role, config the following:

- **Warehouse**: 
    - loading: Uses by extraction tools for scheduled data extraction and load.
    - transforming: Uses by dbt for data transformation and proccess.
    - reports: Default for BI tools and analytics users.

- **Database**: 
    - staging: temporary landing zone for all new raw data.
    - raw: stores all raw data and newly ingested data from the staging layer. 
    - clean: consumption layer where all raw data are processed, transformed and ready for use.
    - analytics: analytics models

- **Role**:
    - pc_user: assigned to data extract/load tools, such as the python dockerize container or 3rd party tools like Fivetran and Stitch. It also can be assign to orchestration tools, such as Airflow. 
    - transformer: this role is owned by transforming tools, such as dbt. 
    - analytics: assigned to BI tools and analytics users.
        - users assigned analytics role should **not** have access to *staging* and *raw* databases. 
        - users with this role should only have SELECT permission for all tables in *clean* and *analytics* databases.
    - OPTIONAL dev: role for developers and admins.

- **User**:
    - data_loader: credential for extraction and load tools
    - airflow: credential for Airflow connection
    - dbt_cloud: credential for dbt and dbt cloud connections
    - bi_conn: credential for Streamlit connection
    - analytics users if needed

Snowflake layers
![Diagram](insert url)

### 2. Extraction and Load

For the extraction and loading process, we leverage a Python-based solution running in a Docker container. This method allows for portability and repeatability.
- **Data Sources**: Sourced from various NBA API endpoints. Execution will be triggerred by Airflow per preset scheduled.
- **Process**: Once extracted, the raw data is loaded into Snowflake's staging area, ready for the next steps in the pipeline.

### 3. Orchestration
We will use Airflow as our orchestration tool to execute DAGs according to scheduled frequencies.
- **DAG Generation**: DAGs will be procedurally generated using a template and configs for each endpoint. 
- **Tasks**: Each DAG included tasks from data extraction, updating table schema, moving data from staging to raw, and cleanup operations to maintain system hygiene.
- **Automation**: Ensuring tasks are performed on a specified schedule with minimal manual intervention, enhancing reliability and efficiency.

### 4. Transformation(dbt)
The data transformation phase is handled by the Data Build Tool (dbt).
- **Transformation Process**: Data in the raw layer is transformed using dbt, which allows us to apply models and configurations for the clean and analytics layers.
- **Environment**: We utilize dbt Cloud for project hosting, providing a platform for daily execution and facilitating CI.
- **Benefits**: This setup enables us to maintain high data quality and consistency, and readily adapt to changing project requirements.

### 5. BI Solution

For our Business Intelligence (BI) solution, we use [Streamlit](https://streamlit.io/cloud), a web-based dashboard application.
- **Data Access**: Streamlit connects seamlessly and securely to our Snowflake databases.
- **Deployment**: The application is hosted on the Streamlit server, providing easy public access to dashboards.
- **User Interaction**: Streamlit allows users to interact with and visualize data through intuitive interfaces, making insights accessible and actionable.

See [Resources section](#resources) for Airflow, Python data extraction container, dbt setups

## Future Work & Improvement
- Consider serverless hosting(AWS, GCP) instead of local.
- Deployment pipeline for CI/CD
- [Unit test](https://docs.getdbt.com/docs/build/unit-tests) on selected models

## Resources
- [NBA API github repo](https://github.com/swar/nba_api) this goes into the extractor repo 
- streamlit snowflake connection: https://docs.streamlit.io/develop/tutorials/databases/snowflake
streamlit installation and getting started with virtual environment: https://docs.streamlit.io/get-started/installation/command-line
dbt snowflake config: https://www.getdbt.com/blog/how-we-configure-snowflake

advance stat dashboard https://medium.com/@toky-axel/building-an-interactive-basketball-stats-dashboard-using-api-and-jupyter-notebook-in-python-3b2c2c191ec9
template for creating project readme: https://www.startdataengineering.com/post/data-engineering-project-to-impress-hiring-managers/
https://towardsdatascience.com/creating-multipage-applications-using-streamlit-efficiently-b58a58134030

replace player id for image: https://cdn.nba.com/headshots/nba/latest/1040x760/1630346.png

## Change Log:
