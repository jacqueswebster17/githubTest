# **Amazon Mobile DIDP Runbook**

[TOC]

#### CHANGE LOG

****

| Version |   Date   |     Editor/Reviewer     |          Comments           |
| :-----: | :------: | :---------------------: | :-------------------------: |
|   1.0   | 3/9/2021 | Kristoffer Buenaventura | First draft for DIDP Amazon |
|         |          |                         |                             |



#### PREREQUISITE UTILITIES & INSTALLATION:

****

- Python 3.5 or higher
- Unix command-line utilities: 
- make 
- curl
- curl command is a tool to download or transfer files/data from or to a server using FTP, HTTP, HTTPS, SCP, SFTP, SMB and other supported protocols on Linux or Unix-like system.
- Install via: $sudo apt-get install curl
- jq
- Unix filter utility for JSON data
- Install via: $sudo apt-get install jq
- pipenv
- dos2unix
- postgresql-client
- postgresql-contrib
- libpq-dev


To set up the tooling environment:

issue "pipenv install" to install the Python dependencies
issue "pipenv shell" to start the virtual environment
(or, if you are not using a virtual env, issue "pip install -r requirements.txt")

```yaml
globals:
    project_home: $TDX_HOME
    service_module: tdx_services
    processor_module: tdx_processors
    macro_module: tdx_macros

service_objects:

macros:

targets:

    2k_token:
        url: "https://api.amazon.com/auth/o2/token"
        method: POST
        headers:                        
            Content-Type: application/x-www-form-urlencoded

        request_params:
          - name: grant_type
            value: client_credentials

          - name: client_id
            value: amzn1.application-oa2-client.1a9d0e0d8a824b2fa2b4d6aa918d5c38
              

          - name: client_secret
            value: $_2K_CLIENT_SECRET
            
          - name: scope
            value: 'adx_reporting::appstore:marketer'

    2k_data:
      url: "https://developer.amazon.com/api/appstore/download/report/sales/{sales_year}/{sales_month}"
      method: GET
      headers:
        Authorization: ~template[Bearer $(_2K_SESSION_TOKEN)]
      
      template_params:
        sales_year: '2021'
        sales_month: '03'


    rockstar_token:
        url: "https://api.amazon.com/auth/o2/token"
        method: POST
        headers:                        
            Content-Type: application/x-www-form-urlencoded

        request_params:
          - name: grant_type
            value: client_credentials

          - name: client_id
            value: amzn1.application-oa2-client.95ee38b2793b4b2db800f48872e8d6de

          - name: client_secret
            value: $ROCKSTAR_CLIENT_SECRET
            
          - name: scope
            value: 'adx_reporting::appstore:marketer'


    rockstar_data:
      url: "https://developer.amazon.com/api/appstore/download/report/sales/{sales_year}/{sales_month}"
      method: GET
      headers:
        Authorization: ~template[Bearer $(ROCKSTAR_SESSION_TOKEN)]
      
      template_params:
        sales_year: '2021'
        sales_month: '03'


    rockstar_download:
      url: https://appstore-adx-reporting-sales-reports-prod.s3.amazonaws.com/monthly/20915/90-0202/sales-2020-09.zip
      method: GET
      headers:

      request_params:
        - name: X-Amz-Algorithm
          value: "AWS4-HMAC-SHA256"

        - name: X-Amz-Date
          value: "20210129T184133Z"
          
        - name: X-Amz-SignedHeaders
          value: host

        - name: X-Amz-Expires
          value: 299

        - name: X-Amz-Credential
          value: "AKIARUHABELWDF4JDXNQ%2F20210129%2Fus-east-1%2Fs3%2Faws4_request"
  
        - name: X-Amz-Signature
          value: 6001e04ca8838d8ec6e6288d90359b908a682453d23bc3346ed3bef885bc2578


    socialpoint_token:
        url: "https://api.amazon.com/auth/o2/token"
        method: POST
        headers:                        
            Content-Type: application/x-www-form-urlencoded

        request_params:
          - name: grant_type
            value: client_credentials

          - name: client_id
            value: amzn1.application-oa2-client.c2b36f7be248493e809803172a8bd8f9

          - name: client_secret
            value: $SOCIALPOINT_CLIENT_SECRET
            
          - name: scope
            value: 'adx_reporting::appstore:marketer'


    socialpoint_data:
      url: "https://developer.amazon.com/api/appstore/download/report/sales/{sales_year}/{sales_month}"
      method: GET
      headers:
        Authorization: ~template[Bearer $(SOCIALPOINT_SESSION_TOKEN)]
      
      template_params:
        sales_year: '2021'
        sales_month: '03'
```



#### ENVIRONMENTAL VARIABLE SETUP

****

- EPIC_API_VERSION (this should be "v2")
- TDX_HOME (this should be set to the repo root directory)
- TUBULAR_API_KEY 
- S3_BUCKET_NAME -- this is the name of the bucket to which pipeline targets will upload files. It should not include the s3:// prefix OR any subfolders.
- SNS_NOTIFICATION_TOPIC -- set this to the ARN of the SNS topic you plan to use for pipeline notifications
- SONY_ACCOUNT_ID
- SONY_ACCOUNT_TOKEN
- SONY_SESSION_TOKEN -- set this to the output of the following command:
  curl -v --user $SONY_ACCOUNT_ID:$SONY_ACCOUNT_TOKEN "https://api.domo.com/oauth/token?grant_type=client_credentials&scope=data" |jq -r .access_token

(there is a script for this: get_sony_token.sh.)

(For Snowflake-aware data processing, set the following environment vars:

SNOWFLAKE_WAREHOUSENAME
SNOWFLAKE_ROLE
SNOWFLAKE_PASSWORD
SNOWFLAKE_USERNAME
SNOWFLAKE_ACCOUNT
SNOWFLAKE_DATABASE

)


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

#### MAKE TARGETS

#### YAML, CONFIG, & URL INFO

#### RESOURCES & API DOCUMENTATION

****

Amazon Mobile API documentation can be found here: https://developer.amazon.com/docs/reports-promo/reporting-API.html

