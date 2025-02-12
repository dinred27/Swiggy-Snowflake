


-- use accountadmin role.
use role accountadmin;



create role swiggy_de;
create role swiggy_da;
grant role swiggy_de to user ravuladr27;
grant role swiggy_de to role sysadmin;

-- create a warehouse if not exist 
create warehouse if not exists swiggy_wh
     comment = 'This is the swiggy-wh'
     warehouse_size = 'x-small' 
     auto_resume = true 
     auto_suspend = 60 
     enable_query_acceleration = false 
     warehouse_type = 'standard' 
     min_cluster_count = 1 
     max_cluster_count = 5
     scaling_policy = 'standard'
     initially_suspended = true;

-- create development database/schema  if does not exist
create database if not exists swiggy;
use database swiggy;
create schema if not exists stage_sch;
create schema if not exists clean_sch;
create schema if not exists consumption_sch;
create schema if not exists common;


grant usage on warehouse swiggy_wh to role swiggy_de;
grant usage on database swiggy to role swiggy_de;

-- Allow referencing the schema
grant usage on all schemas in database swiggy to role swiggy_de;
grant select on all schemas in database swiggy to role swiggy_de;

-- Allow creating tables, views, and other objects in all schemas
GRANT CREATE TABLE, CREATE VIEW, CREATE STAGE, CREATE FUNCTION 
ON ALL SCHEMAS IN DATABASE swiggy TO ROLE swiggy_de;

-- Allow performing DML operations on all existing tables
GRANT SELECT, INSERT, UPDATE, DELETE 
ON ALL TABLES IN DATABASE swiggy TO ROLE swiggy_de;

-- Grant privileges on any future tables
GRANT SELECT, INSERT, UPDATE, DELETE 
ON FUTURE TABLES IN DATABASE swiggy TO ROLE swiggy_de;

-- Grant USAGE on the stage
GRANT Read, write ON STAGE SWIGGY.STAGE_SCH.CSV_STG TO ROLE swiggy_de;


-- Grant CREATE STREAM privilege on all schemas to allow stream creation
GRANT CREATE STREAM ON ALL SCHEMAS IN DATABASE SWIGGY TO ROLE swiggy_de;

-- Grant DML and monitoring privileges on all streams in the database
GRANT SELECT ON ALL STREAMS IN DATABASE SWIGGY TO ROLE swiggy_de;

-- Grant OWNERSHIP on all streams in the database (full control: modify, drop, etc.)
GRANT OWNERSHIP ON ALL STREAMS IN DATABASE SWIGGY TO ROLE swiggy_de;


use schema stage_sch;

 -- create file format to process the CSV file
  create file format if not exists stage_sch.csv_file_format 
        type = 'csv' 
        compression = 'auto' 
        field_delimiter = ',' 
        record_delimiter = '\n' 
        skip_header = 1 
        field_optionally_enclosed_by = '\042' 
        null_if = ('\\N');

-- Grant USAGE to allow the role to use the file format
GRANT USAGE ON FILE FORMAT SWIGGY.STAGE_SCH.CSV_FILE_FORMAT TO ROLE swiggy_de;

-- (Optional) Grant OWNERSHIP if the role needs full control (modify, drop, transfer ownership)
GRANT OWNERSHIP ON FILE FORMAT SWIGGY.STAGE_SCH.CSV_FILE_FORMAT TO ROLE swiggy_de;

create stage stage_sch.csv_stg
    directory = ( enable = true )
    comment = 'this is the snowflake internal stage';


create or replace tag 
    common.pii_policy_tag 
    allowed_values 'PII','PRICE','SENSITIVE','EMAIL'
    comment = 'This is PII policy tag object';

create or replace masking policy 
    common.pii_masking_policy as (pii_text string)
    returns string -> 
    to_varchar('** PII **');

create or replace masking policy 
    common.email_masking_policy as (email_text string)
    returns string -> 
    to_varchar('** EMAIL **');

create or replace masking policy 
    common.phone_masking_policy as (phone string)
    returns string -> 
    to_varchar('** Phone **');



grant usage on tag common.pii_policy_tag to role swiggy_de;

-- Grant USAGE to allow referencing the tag
GRANT USAGE ON TAG PII_POLICY_TAG TO ROLE swiggy_de;

-- Grant APPLY to allow applying the tag to objects
GRANT APPLY ON TAG PII_POLICY_TAG TO ROLE swiggy_de;

use role swiggy_de;
use warehouse swiggy_wh;
show grants to role swiggy_de;
list @stage_sch.csv_stg;
