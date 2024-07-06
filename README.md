# Data_cleaning_with_SQL

In this project I built an automated data cleaner uisng the #USHOUSEHOLDINCOME# csv file. 

The first step taekn was to clean my data uisng the following code. 

                        -- Remove Duplicates
                        DELETE FROM us_household_income_clean 
                        WHERE 
                        	row_id IN (
                        	SELECT row_id
                        FROM (
                        	SELECT row_id, id,
                        		ROW_NUMBER() OVER (
                        			PARTITION BY id
                        			ORDER BY id) AS row_num
                        	FROM 
                        		us_household_income_clean
                        ) duplicates
                        WHERE 
                        	row_num > 1
                        );
                        
                        -- Fixing some data quality issues by fixing typos and general standardization
                        UPDATE us_household_income_clean
                        SET State_Name = 'Georgia'
                        WHERE State_Name = 'georia';
                        
                        UPDATE us_household_income_clean
                        SET County = UPPER(County);
                        
                        UPDATE us_household_income_clean
                        SET City = UPPER(City);
                        
                        UPDATE us_household_income_clean
                        SET Place = UPPER(Place);
                        
                        UPDATE us_household_income_clean
                        SET State_Name = UPPER(State_Name);
                        
                        UPDATE us_household_income_clean
                        SET `Type` = 'CDP'
                        WHERE `Type` = 'CPD';
                        
                        UPDATE us_household_income_clean
                        SET `Type` = 'Borough'
                        WHERE `Type` = 'Boroughs';


