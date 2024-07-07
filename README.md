# Automated_data_cleaning_with_SQL

In this project I built an automated data cleaner using Stored Procedures and Events on MySQL.
The data used is the #Us_household_income# which contained over 18,000 rows.

Here are the steps I followed to clean my data and build an automated data cleaning procedure.

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


 Step 3: The third step I created a store procedure and inserted the the queries used for duplicated removal and data standardization. This is important so we call the data cleaning function. In addition, making our 
         schedule EVENT perform it duty.

    DELIMITER $$
    DROP PROCEDURE IF EXISTS copy_and_clean_data;
    CREATE PROCEDURE copy_and_clean_data()
    BEGIN 
      	CREATE TABLE IF NOT EXISTS `us_household_income_cleaned` (
      	  `row_id` int DEFAULT NULL,
      	  `id` int DEFAULT NULL,
      	  `State_Code` int DEFAULT NULL,
      	  `State_Name` text,
      	  `State_ab` text,
      	  `County` text,
      	  `City` text,
      	  `Place` text,
      	  `Type` text,
      	  `Primary` text,
      	  `Zip_Code` int DEFAULT NULL,
      	  `Area_Code` int DEFAULT NULL,
      	  `ALand` int DEFAULT NULL,
      	  `AWater` int DEFAULT NULL,
      	  `Lat` double DEFAULT NULL,
      	  `Lon` double DEFAULT NULL,
      	  `TimeStamp` TIMESTAMP DEFAULT NULL
      	) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;
      
        INSERT INTO `us_household_income_cleaned` 
        SELECT *, CURRENT_TIMESTAMP
        FROM us_household_income;
          
        DELETE FROM us_household_income_cleaned
        WHERE row_id IN (SELECT row_id
                             FROM
                               (SELECT row_id, 
                               ROW_NUMBER() OVER (PARTITION BY row_id, `TimeStamp` ORDER BY row_id, `TimeStamp`) as row_num
                               FROM us_household_income_cleaned) as duplicates
                             WHERE row_num > 1); 
      	
         UPDATE us_household_income_cleaned
         SET State_Name = 'Georgia'
         WHERE State_Name = 'georia';
          
         UPDATE us_household_income_cleaned
         SET County = UPPER(County);
          
         UPDATE us_household_income_cleaned
         SET City = UPPER(City);
          
         UPDATE us_household_income_cleaned
         SET Place = UPPER(Place);
          
         UPDATE us_household_income_cleaned
         SET State_Name = UPPER(State_Name);
          
         UPDATE us_household_income_cleaned
         SET `Type` = 'CDP'
         WHERE `Type` = 'CPD';
          
         UPDATE us_household_income_cleaned
         SET `Type` = 'Borough'
         WHERE `Type` = 'Boroughs';
      
      END $$
      DELIMITER ; 

Step 4: I created an EVENT that runs the data cleaning based on my time of choice. In this project I used 1 day. 

      CREATE EVENT run_data_cleaning
      ON SCHEDULE EVERY 1 DAY
      DO CALL copy_and_clean_data();


By following these steps, I not only cleaned my data but also developed an automated data cleaning process that cleans my table at the end of every day. This has made my life easier and my job faster.


      
         
