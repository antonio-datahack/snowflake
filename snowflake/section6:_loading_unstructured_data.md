<h3>38. Creating stage & raw file</h3>

```sql

// First step: Load Raw JSON

CREATE OR REPLACE stage MANAGE_DB.EXTERNAL_STAGES.JSONSTAGE
     url='s3://bucketsnowflake-jsondemo';

CREATE OR REPLACE file format MANAGE_DB.FILE_FORMATS.JSONFORMAT
    TYPE = JSON;
    
    
CREATE OR REPLACE table OUR_FIRST_DB.PUBLIC.JSON_RAW (
    raw_file variant);
    
COPY INTO OUR_FIRST_DB.PUBLIC.JSON_RAW
    FROM @MANAGE_DB.EXTERNAL_STAGES.JSONSTAGE
    file_format= MANAGE_DB.FILE_FORMATS.JSONFORMAT
    files = ('HR_data.json');
    
   
SELECT * FROM OUR_FIRST_DB.PUBLIC.JSON_RAW;

```

<h3>Assignment 7: Load raw JSON</h3>

 If you have not created the database EXERCISE_DB then you can do so - otherwise use this database for this exercise.


1. Create a stage object that is pointing to 's3://snowflake-assignments-mc/unstructureddata/'

2. Create a file format object that is using TYPE = JSON

3. Create a table called JSON_RAW with one column

Column name: Raw
Column type: Variant

4. Copy the raw data in the JSON_RAW table using the file format object and stage object

Questions for this assignment
What is the last name of the person in the first row (id=1)?

```sql     

 --  Create database (only if not already created in previous assignment)
create database EXERCISE_DB;
 
USE EXERCISE_DB;


 // First step: Load Raw JSON
CREATE OR REPLACE stage JSONSTAGE
     url='s3://snowflake-assignments-mc/unstructureddata/';

CREATE OR REPLACE file format JSONFORMAT
    TYPE = JSON;
    
    
CREATE OR REPLACE table JSON_RAW (
    raw_file variant);
    
COPY INTO JSON_RAW
    FROM @JSONSTAGE
    file_format= JSONFORMAT
    
SELECT * FROM JSON_RAW;


```

<h3>39. Parsing JSON</h3>

```sql  

// Second step: Parse & Analyse Raw JSON 

   // Selecting attribute/column

SELECT RAW_FILE:city FROM OUR_FIRST_DB.PUBLIC.JSON_RAW;


// en ves de escribir RAW_FILE puedo escribir la posicion de la columna con el signo $
SELECT $1:city FROM OUR_FIRST_DB.PUBLIC.JSON_RAW;

SELECT $1:first_name FROM OUR_FIRST_DB.PUBLIC.JSON_RAW;


   // Selecting attribute/column - formattted

SELECT RAW_FILE:first_name::string as first_name  FROM OUR_FIRST_DB.PUBLIC.JSON_RAW;

SELECT RAW_FILE:id::int as id  FROM OUR_FIRST_DB.PUBLIC.JSON_RAW;

SELECT 
    RAW_FILE:id::int as id,  
    RAW_FILE:first_name::STRING as first_name,
    RAW_FILE:last_name::STRING as last_name,
    RAW_FILE:gender::STRING as gender

FROM OUR_FIRST_DB.PUBLIC.JSON_RAW;

```

<h3>40. Handling nested data</h3>


```sql  
   
SELECT RAW_FILE:job as job  FROM OUR_FIRST_DB.PUBLIC.JSON_RAW;


SELECT 
      RAW_FILE:job.salary::INT as salary
FROM OUR_FIRST_DB.PUBLIC.JSON_RAW;



SELECT 
    RAW_FILE:first_name::STRING as first_name,
    RAW_FILE:job.salary::INT as salary,
    RAW_FILE:job.title::STRING as title
FROM OUR_FIRST_DB.PUBLIC.JSON_RAW;


// Handling arreys

SELECT
    RAW_FILE:prev_company as prev_company
FROM OUR_FIRST_DB.PUBLIC.JSON_RAW;

SELECT
    RAW_FILE:prev_company[1]::STRING as prev_company
FROM OUR_FIRST_DB.PUBLIC.JSON_RAW;


SELECT
    ARRAY_SIZE(RAW_FILE:prev_company) as prev_company
FROM OUR_FIRST_DB.PUBLIC.JSON_RAW;




SELECT 
    RAW_FILE:id::int as id,  
    RAW_FILE:first_name::STRING as first_name,
    RAW_FILE:prev_company[0]::STRING as prev_company
FROM OUR_FIRST_DB.PUBLIC.JSON_RAW
UNION ALL 
SELECT 
    RAW_FILE:id::int as id,  
    RAW_FILE:first_name::STRING as first_name,
    RAW_FILE:prev_company[1]::STRING as prev_company
FROM OUR_FIRST_DB.PUBLIC.JSON_RAW
ORDER BY id;


```
