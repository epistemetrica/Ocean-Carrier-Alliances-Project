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
- oca_analyse_usda.ipynb
    - a Jupyter notebook containing summary statistics, models, and other analyses of the data for the USDA report. 
- this README.md

Current versions of the notebooks and README can be found at on the github repo. 

### Python Environment and Libraries

All files were written in Python 3.12.0, and the following libraries are used:
- polars v1.1.0 
- numpy v1.26.2
- pandas v2.1.3
- plotly_express v0.4.1
- scikit-learn v1.3.2
- statsmodels 0.14.1
- geopy 2.4.1

## Data Challenges

### PIERS Data

Although the PIERS BOL database is extensive, covering the entirety of maritime containerized cargo that crossed the US border since 2005, the dataset comes with signficant challenges. 
- The most siginficant hurdle lines in the fact that volumes (TEUs, weight, and quantity) are entirely absent from the data prior to ~2015. 
    - this is an open question to S&P, but we have waning hope of getting any explanation from them. 
    - for the time being, we fill missing TEUs with the mean.
- We have not been able to determine a sensible way of associating the various description columns with other metrics. For example, the commodity descriptions sometimes describe 4 different commodities, but the volume column for that bill contains 3 volume metrics. 
    - this is also an open question to S&P
    - for now, we split the total TEUs for each bill evenly across the listed commodity codes. 
- According to S&P, the date column contains the arrival date of the vessel in port; however, the actual data data are not consistent with this understanding, as BOLs showing the same vessel and lane data sometimes occur 1-5 days apart, even on lanes that take weeks to travel. 
    - we address this inconsistency by applying an HDBSCAN algorithm to cluster the dates together
    - this allows us to accurately analyze how many times a ship visits a given port and how many total voyages are offered from that port in a given time frame - an important metric of service quality in shipping. 

### Other Data Sources

Data from Drewery's CFRI reports are processed via the [PDF Data Processing Project](https://github.com/epistemetrica/PDF-data-processing). At the moment, only the 2015–2023 reports are interpretable by the scripts in that project. Future efforts may pull rate data from older reports, but for the time being we analyze rates only where available. 

Vessel capacity data from the [US Army Corp of Engineers](https://ndclibrary.sec.usace.army.mil/searchResults?series=Foreign%20Traffic%20Vessel%20Entrances%20Clearances) are clean and extensive, but only cover the 2013–2022 time period. Many ships, of course, were in service before and after that time period, but vessel capacities are increasingly missing as time moves outside that window. 

## Project Deliverables

1. Transportation Review Board (TRB) Report 
    - The initial report for this project was submitted to the TRB on Aug 1 2024. TRBAM-25-05784. 
2. USDA Report 
    - this is the primary deliverable to satisfy the grant with which this work is funded, and investigates the effects of OCAs on containerized US agricultural exports.
3. Journal Submission
    - the final deliverable will be a submission to a peer-reviewed journal; the exact form and content is still under development. 

