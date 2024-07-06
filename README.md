# Automated_data_cleaning_with_SQL

In this project I built an automated data cleaner using Stored Procedures, Triggers, and Events on MySQL.
The data used is the #Us_household_income# which contained over 31,000 rows.

The first steps I took was to actually clean the data using the following queries:

  Step 1: Removing duplicate OR checking for duplicates

      DELETE FROM us_household_income
      WHERE row_id IN (SELECT row_id
                       FROM
                         (SELECT row_id, 
                         ROW_NUMBER() OVER (PARTITION BY row_id ORDER BY row_id) as row_num
                         FROM us_household_income) as duplicates
                       WHERE row_num > 1); 
                  

  In this case, there are no duplicates in this data.

  Step 2: I fixed some data quality issues by correcting typos and general standardization.
    
    UPDATE us_household_income
    SET State_Name = 'Georgia'
    WHERE State_Name = 'georia';
    
    UPDATE us_household_income
    SET County = UPPER(County);
    
    UPDATE us_household_income
    SET City = UPPER(City);
    
    UPDATE us_household_income
    SET Place = UPPER(Place);
    
    UPDATE us_household_income
    SET State_Name = UPPER(State_Name);
    
    UPDATE us_household_income
    SET `Type` = 'CDP'
    WHERE `Type` = 'CPD';
    
    UPDATE us_household_income
    SET `Type` = 'Borough'
    WHERE `Type` = 'Boroughs';


 Step 3: 
