# Ocean Carrier Alliances Project

This project analyses the effects of ocean carrier alliances on maritime transport economics. Using data from S&P's PIERS Bill of Lading database as well as Drewery's rate reports, we inspect the effects of carrier alliances on various metrics including frequency of service and prices. 

## Structural overview:

### Project Files

The project consists of the following files:
- Data files containing information from the Bills of Lading (BOLs) for all maritime containerized shipments that crossed the US Border from 2005 through the end of 2023.
    - Since they are large (>200M rows), these files are in [parquet format](https://www.databricks.com/glossary/what-is-parquet). The raw data were downloaded in csv format from S&P's PIERS BOL database and processed in the [PIERS Data Project](https://github.com/epistemetrica/PIERS-Data-Project).
    - Note: these data are not included in the github repo but may be available on request from Jake Wagner. 
- Data files containing monthly rate data for each shipping lane
    - These data were obtained from Drewery CFR Insight reports.
- oca_data_prep.ipynb
    - a Jupyter notebook that cleans and prepares the various data files for analysis. 
- oca_analysis.ipynb
    - a Jupyter notebook containing models and other analysis of the data. 
- this README.md

Current versions of the notebooks and README can be found at on the github repo. 

### Python Environment and Libraries

All files were written in Python 3.12, and the following libraries are used:
- polars v0.20.18 
- numpy v1.26.2
- pandas v2.1.3
- plotly_express v0.4.1
- scikit-learn v1.3.2

## Data Challenges

Although the PIERS BOL database is extensive, covering the entirety of maritime containerized cargo that crossed the US border since 2005, the dataset comes with signficant challenges. 
- The most siginficant hurdle lines in the fact that volumes (TEUs, weight, and quantity) are entirely absent from the data prior to ~2015. 
    - this is an open question to S&P, but we have waning hope of getting any explanation from them. 
    - for the time being, we fill missing TEUs with the mean.
- We have not been able to determine a sensible way of associating the various description columns with other metrics. For example, the commodity descriptions sometimes describe 4 different commodities, but the volume column for that bill contains 3 volume metrics. 
    - this is also an open question to S&P
    - for now, we split the total TEUs for each bill evenly across the listed commodity codes. 
- According to S&P, the date columns 

## Model

The initial model for the project inspects the effects of carrier alliances on frequency of service. We estimate this using the following equation:

$$ S_{ijct} = b X_{ijct} + a_1 AM_{ct}PC_{ijc} + a_2 AM_{ct} + a_3 PC_{ijc} + \epsilon_{ijct} $$

where:
- $i$ is the departure port
- $j$ is the arrival port
- $c$ is the carrier
- $t$ is the time period, which we aggregate to months (e.g., May 2019)
- $S$ is the frequency of service, i.e. the number of voyages provided to that lane by the carrier in the given month. 
- $X$ are the correction variables
- $AM$ is an indicator of whether or not the carrier is part of an alliance
- $PC$ is an indicator of whether or not the lane was serviced by that carrier before the alliance took effect. 

