# **Amazon Mobile DIDP Runbook**

[TOC]

#### CHANGE LOG

****

| Version |   Date   |              Editor/Reviewer               |          Comments           |
| :-----: | :------: | :----------------------------------------: | :-------------------------: |
|   1.0   | 3/9/2021 | Dexter Taylor<br />Kristoffer Buenaventura | First draft for DIDP Amazon |
|         |          |                                            |                             |



#### PREREQUISITE UTILITIES & INSTALLATION:

****

- **Python 3.5 or higher**
- Unix command-line utilities: 
  - `pip`
    - Python package-management system
    - Install via: `$sudo apt-get install python3-pip`
  - `make` 
  - `curl`
    - `curl` command is a tool to download or transfer files/data from or to a server using FTP, HTTP, HTTPS, SCP, SFTP, SMB and other supported protocols on Linux or Unix-like system.
    - Install via: `$sudo apt-get install curl`
  - `jq`
    - Unix filter utility for JSON data
    - Install via: `$sudo apt-get install jq`
  - `pipenv`
    - Python packaging tool
    - Install via: `$sudo apt-get install pipenv`
  - `dos2unix`
    - Tool to convert line breaks in a text file from Unix format to DOS format and vice versa
    - Install via: `$sudo apt-get install dos2unix`
  - `postgresql-client`
    - Install via (check with Dex)
  - `postgresql-contrib`
    - Install via (check with Dex)
  - `libpq-dev`
    - Install via (check with Dex)

**To set up the tooling environment:**

- issue `$pipenv install` to install the Python dependencies
- issue `$pipenv shell` to start the virtual environment
  - Or, if you are not using a virtual environment, issue `$pip install -r requirements.txt`

#### MAKE PIPELINES

****

Because each T2 label has its own Amazon Developer account, we have created a separated Make pipeline for each label.**

At a high level, the make pipeline process does not change by label. 

###### Make Pipeline Process

###### Make Targets (Unix)

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

Before any Make Pipeline can be run successful, there are several Environment & Session variables that will need to be set up:

- `S3_BUCKET_PATH`

  - The path of the AWS S3 bucket to which pipeline targets will upload files. It should not include the `s3://` prefix OR any subfolders.

- `SNS_NOTIFICATION_TOPIC`

  - Set this to the ARN of the SNS topic you plan to use for pipeline notifications

- `SONY_ACCOUNT_ID`

- `SONY_ACCOUNT_TOKEN`

- `SONY_SESSION_TOKEN` 

  - Set this to the output of the following command:

    ```
    curl -v --user $SONY_ACCOUNT_ID:$SONY_ACCOUNT_TOKEN "https://api.domo.com/oauth/token?grant_type=client_credentials&scope=data" |jq -r .access_token
    ```

    

  - **There is a script for this: `get_sony_token.sh`**




> For the Sony data pipelines:

run the desired make target from the Makefile. 

Example: "make pipeline-sony-americas" will pull 30-day sales tallies for the 
Sony Americas region. There is one target for each of the four regions we care about:
americas, europe, japan, and asia.

--> For the Epic data pipelines:

issue "source get_epic_token.sh"
run the desired make target from the Makefile.

Example: "make pipeline-epic-2k" will pull Epic sales data for the 2K label. There is one 
target for each of the three labels we care about: 2K, Rockstar, and Private Division.

--> For the Tubular data pipelines:

ensure that the environment variable TUBULAR_API_KEY contains a valid API key
run the desired make target.

--> For the Snowflake data API service:

issue "make api-run"

if you add new transforms to the Snap configuration OR you alter the data shape for 
an existing transform, issue "make api-regen" and then "make api-run"

The service is configured to listen on port 6050. Issue a GET request to 

http://<host>:6050/ping 

to verify that it's alive. 

#### GIT REPOSITORY

#### SCHEDULED JOBS

#### DATA RECONCILIATION

#### RESOURCES & API DOCUMENTATION

****

Amazon Mobile API documentation can be found here: https://developer.amazon.com/docs/reports-promo/reporting-API.html

