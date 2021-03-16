

# **Amazon Mobile DIDP Runbook**

[TOC]

#### CHANGE LOG

****

| Version |   Date   |              Editor/Reviewer               |          Comments           |
| :-----: | :------: | :----------------------------------------: | :-------------------------: |
|   1.0   | 3/9/2021 | Dexter Taylor<br />Kristoffer Buenaventura | First draft for DIDP Amazon |
|         |          |                                            |                             |

#### OVERVIEW

****

The Amazon Mobile DIDP pipeline is an automated tool that pulls a **daily** Sales file from the Amazon Reporting API and inserts it into an AWS S3 bucket.

#### PREREQUISITE UTILITIES & INSTALLATION:

****

- **Python 3.6 or higher**
- Ensure that PATH includes /usr/local/bin
- Unix command-line utilities: 
  - `pip`
    - Python package-management system
    - Install via: `sudo apt-get install python3-pip`
  - `make` 
    - Build automation tool
    - Install via: `sudo apt-get install build-essential`
  - `curl`
    - Tool  to download or transfer files/data from or to a server using FTP, HTTP, HTTPS, SCP, SFTP, SMB and other supported protocols on Linux or Unix-like system.
    - Install via: `sudo apt-get install curl`
  - `jq`
    - Unix filter utility for JSON data
    - Install via: `sudo apt-get install jq`
  - `pipenv`
    - Python packaging tool
    - Install via: `sudo pip3 install pipenv`
  - `dos2unix`
    - Tool to convert line breaks in a text file from Unix format to DOS format and vice versa
    - Install via: `sudo apt-get install dos2unix`
  - `postgresql-client`
    - Relational DMS package
    - Install via: `sudo apt-get install postgresql-client`
  - `postgresql-contrib`
    - Relational DMS package
    - Install via: `sudo apt-get install postgresql-contrib`
  - `libpq-dev`
    - Set of library functions that allow client programs to interface with PostgreSQL backend
    - Install via: `sudo apt-get install libpq-dev`

**To set up the tooling environment:**

- issue `pipenv install` to install the Python dependencies
- issue `pipenv shell` to start the virtual environment
  - Or, if you are not using a virtual environment, issue `$pip install -r requirements.txt`

#### DATA PIPELINES

****

Because each T2 label has its own Amazon Developer account, we have created a separated Make pipeline for each label.**

At a high level, the make pipeline process does not change by label. 

###### Amazon Data Pipeline Process

- 

###### Naming Conventions for Make Targets

- Generally, there are 3 types of Make Targets:
  - Pipeline targets
    - Purpose: Runs a data pipeline
    - Syntax begins with "pipeline-"
      - Example: `pipeline-amazon-rockstar-data`
  - Generator targets
    - Purpose: Generates a set of configuration files
    - Syntax begins with "generate-"
      - Example: `generate-livestream-configs-airtime`
  - Clean-up targets
    - Purpose: Cleans up temporary data files
    - Syntax begins with "clean-"
      - Example: `clean-amazon-rockstar-data`

###### Amazon Mobile Make Targets (Unix)

*Disclaimer*: **Please make sure to disconnect from the GlobalProtect VPN before running the make targets or they will fail.**

- 2K

  - ```makefile
    make pipeline-amazon-2k-data
    ```

- Rockstar

  - ```makefile
    make pipeline-amazon-rockstar-data
    ```

- Social Point

  - ```makefile
    make pipeline-amazon-socialpoint-data
    ```

#### ENVIRONMENT VARIABLE SETUP

****

Before any data pipeline can be run successful, there are several environment variables that need to be set up

###### **AWS S3 & SNS Information**

- `AMAZON_S3_BUCKET`
  - The name of the AWS S3 bucket to which pipeline targets will upload files. It should not include the `s3://` prefix OR any subfolders.
  - Currently set to: `t2-entapps-edw-dev-landing`
- `SNS_EIH_X_TOPIC`
  - The ARN of the SNS topic used for pipeline notifications, where X is equal to the label attached to the pipeline:
    - `SNS_EIH_2K_TOPIC`
    - `SNS_EIH_ROCKSTAR_TOPIC`
    - `SNS_EIH_SOCIALPOINT_TOPIC`

###### S3 Location

The full path of the S3 locations do NOT have to be set manually. Only the env variable `AMAZON_S3_BUCKET` must be set.

- 2K
  - `s3://t2-entapps-edw-dev-landing/didp/amazon/mobile/raw/2k`

- Rockstar

  - `s3://t2-entapps-edw-dev-landing/didp/amazon/mobile/raw/rockstar`

- Social Point

  - `s3://t2-entapps-edw-dev-landing/didp/amazon/mobile/raw/socialpoint`

###### Amazon Client Secrets

- `2K_CLIENT_SECRET`
- `ROCKSTAR_CLIENT_SECRET`
- `SOCIALPOINT_CLIENT_SECRET`

`Init Shell Scripts & .GITIGNORE`

#### GIT REPOSITORY

****

The Amazon DIDP is part of Enterprise Integration Hub and the related scripts are included in below Github repo:

- https://github.take2games.com/Enterprise-Applications/enterprise-integration-hub

#### DATA RECONCILIATION

****

Data reconciliation for January 2021 Sales data between Amazon API & Portal files:

- **Vendor SKU: com.2KSports.WWE2K14Mobile**
- **Title: WWE SuperCard**
- **Item Name: WWE SuperCard**

###### Portal File

- Pulled via Amazon Reporting UI:
  - ![image-20210316095301703](C:\Users\kristoffer.buenavent\AppData\Roaming\Typora\typora-user-images\image-20210316095301703.png)
- ![image-20210316095200648](C:\Users\kristoffer.buenavent\AppData\Roaming\Typora\typora-user-images\image-20210316095200648.png)

###### API File

- Pulled via API call:
  - ![image-20210316095413250](C:\Users\kristoffer.buenavent\AppData\Roaming\Typora\typora-user-images\image-20210316095413250.png)

- ![image-20210316095017931](C:\Users\kristoffer.buenavent\AppData\Roaming\Typora\typora-user-images\image-20210316095017931.png)



#### RESOURCES & API DOCUMENTATION

****

Amazon Mobile API documentation can be found here: 

- https://developer.amazon.com/docs/reports-promo/reporting-API.html

