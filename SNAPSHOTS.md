```sql
-- Extraction along with data cleaning and quality check: Count missing values in patient health and diet table --
SELECT COUNT(*) AS MissingValues
FROM healthdata.`patient health and diet`
WHERE Heart_Disease IS NULL
   OR Skin_Cancer IS NULL
   OR Other_Cancer IS NULL
   OR Depression IS NULL
   OR Diabetes IS NULL
   OR Arthritis IS NULL
   OR Smoking_History IS NULL
   OR Alcohol_Consumption IS NULL
   OR Fruit_Consumption IS NULL
   OR Green_Vegetables_Consumption IS NULL
   OR FriedPotato_Consumption IS NULL;
```
OUTPUT
![image](https://github.com/VinithaVarghese/DATA-1202-FINAL-ASSIGNMENT/assets/138626409/9fb4e705-5920-4e84-a390-c941b5eea305)

```sql
-- Extraction along with data cleaning and quality check: Count missing values in patient profile table --
SELECT COUNT(*) AS MissingValues
FROM healthdata.`patient profile`
WHERE General_Health IS NULL
   OR Checkup IS NULL
   OR Exercise IS NULL
   OR Sex IS NULL
   OR Age_Category IS NULL
   OR `Height_(cm)` IS NULL
   OR `Weight_(kg)` IS NULL
   OR BMI IS NULL;
```
OUTPUT
![image](https://github.com/VinithaVarghese/DATA-1202-FINAL-ASSIGNMENT/assets/138626409/7f922dab-29d4-4ea0-949d-0752a8726119)

```sql
-- Transformation: Heart Disease Distribution and Exercise Habits Analysis --
SELECT pp.Exercise, phd.Heart_Disease, COUNT(*) AS Count
FROM healthdata.`patient health and diet` phd
JOIN healthdata.`patient profile` pp ON phd.ID = pp.ID
GROUP BY pp.Exercise, phd.Heart_Disease
ORDER BY pp.Exercise, phd.Heart_Disease;
```
OUTPUT
![image](https://github.com/VinithaVarghese/DATA-1202-FINAL-ASSIGNMENT/assets/138626409/d5e2c3a7-0543-431f-b15a-ad2b2497330f)
```sql
-- Transformation: Average BMI by Age Category --
SELECT
    p.Age_Category,
    AVG(p.BMI) AS Average_BMI
FROM
    healthdata.`patient profile` p
GROUP BY
    p.Age_Category
ORDER BY
    p.Age_Category;
```
OUTPUT
![image](https://github.com/VinithaVarghese/DATA-1202-FINAL-ASSIGNMENT/assets/138626409/8c81d9fa-b497-4bb4-854a-f03f804a0779)

```sql
-- Transformation: Gender-based Health Patterns --
SELECT
    p.Sex,
    AVG(CASE WHEN hd.Heart_Disease = 'Yes' THEN 1 ELSE 0 END) AS Heart_Disease_Rate,
    AVG(CASE WHEN hd.Skin_Cancer = 'Yes' THEN 1 ELSE 0 END) AS Skin_Cancer_Rate,
    AVG(CASE WHEN hd.Other_Cancer = 'Yes' THEN 1 ELSE 0 END) AS Other_Cancer_Rate,
    AVG(CASE WHEN hd.Depression = 'Yes' THEN 1 ELSE 0 END) AS Depression_Rate,
    AVG(CASE WHEN hd.Diabetes = 'Yes' THEN 1 ELSE 0 END) AS Diabetes_Rate,
    AVG(CASE WHEN hd.Arthritis = 'Yes' THEN 1 ELSE 0 END) AS Arthritis_Rate
FROM
    healthdata.`patient health and diet` hd
JOIN
    healthdata.`patient profile` p ON hd.ID = p.ID
GROUP BY
    p.Sex
ORDER BY
    p.Sex;
```
OUTPUT
![image](https://github.com/VinithaVarghese/DATA-1202-FINAL-ASSIGNMENT/assets/138626409/a1d0cbf4-0083-4186-966c-d32a71d2fad0)

```sql
-- Transformation: Nutrition Habits and Health Conditions --
SELECT
    AVG(CASE WHEN hd.Heart_Disease = 'Yes' THEN 1 ELSE 0 END) AS Heart_Disease_Rate,
    AVG(CASE WHEN hd.Skin_Cancer = 'Yes' THEN 1 ELSE 0 END) AS Skin_Cancer_Rate,
    AVG(CASE WHEN hd.Other_Cancer = 'Yes' THEN 1 ELSE 0 END) AS Other_Cancer_Rate,
    AVG(CASE WHEN hd.Depression = 'Yes' THEN 1 ELSE 0 END) AS Depression_Rate,
    AVG(CASE WHEN hd.Diabetes = 'Yes' THEN 1 ELSE 0 END) AS Diabetes_Rate,
    AVG(CASE WHEN hd.Arthritis = 'Yes' THEN 1 ELSE 0 END) AS Arthritis_Rate,
    AVG(CASE WHEN hd.Fruit_Consumption = 'Yes' THEN 1 ELSE 0 END) AS High_Fruit_Consumption_Rate,
    AVG(CASE WHEN hd.Green_Vegetables_Consumption = 'Yes' THEN 1 ELSE 0 END) AS High_Green_Vegetables_Consumption_Rate,
    AVG(CASE WHEN hd.FriedPotato_Consumption = 'No' THEN 1 ELSE 0 END) AS Low_FriedPotato_Consumption_Rate
FROM
    healthdata.`patient health and diet` hd
JOIN
    healthdata.`patient profile` p ON hd.ID = p.ID;
```
OUTPUT
![image](https://github.com/VinithaVarghese/DATA-1202-FINAL-ASSIGNMENT/assets/138626409/997d428e-a686-49f9-8157-ddee40c6a096)

```sql
-- Transformation: Average BMI by General Health Status --
SELECT
    p.General_Health,
    ROUND(AVG(p.BMI)) AS Average_BMI
FROM
    healthdata.`patient profile` p
GROUP BY
    p.General_Health;
```
OUTPUT
![image](https://github.com/VinithaVarghese/DATA-1202-FINAL-ASSIGNMENT/assets/138626409/d32816d5-47e7-4ed4-99bc-ab122fb40e02)

```sql
-- Transformation: CHECK DATA TYPES --
SELECT COLUMN_NAME, DATA_TYPE
FROM INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_SCHEMA = 'healthdata'
  AND TABLE_NAME IN ('patient health and diet', 'patient profile');    
```
OUTPUT
![image](https://github.com/VinithaVarghese/DATA-1202-FINAL-ASSIGNMENT/assets/138626409/6b82e51c-0de3-4aee-9dff-0ad609bbfba3)
![image](https://github.com/VinithaVarghese/DATA-1202-FINAL-ASSIGNMENT/assets/138626409/1dbf0df4-d585-4bc4-bf2d-be1fe89d074a)

```sql
-- Loading along with data cleaning and quality check: GENERAL AGE_GENDER_HEALTH_INSIGHTS -- 
CREATE TABLE AGE_GENDER_HEALTH_INSIGHTS AS (
    SELECT
        p.Age_Category,
        p.Sex,
        MAX(p.General_Health) AS General_Health,
        MAX(p.Checkup) AS General_Checkup_Status,
        MAX(pp.Exercise) AS General_Exercise_Rate,
        ROUND(AVG(CASE WHEN p.BMI REGEXP '^[0-9]*\\.?[0-9]+$' THEN CAST(p.BMI AS DOUBLE) ELSE NULL END), 2) AS Avg_BMI,
        AVG(CASE WHEN hd.Heart_Disease = 'Yes' THEN 1 ELSE 0 END) AS Heart_Disease_Rate,
        AVG(CASE WHEN hd.Skin_Cancer = 'Yes' THEN 1 ELSE 0 END) AS Skin_Cancer_Rate,
        AVG(CASE WHEN hd.Other_Cancer = 'Yes' THEN 1 ELSE 0 END) AS Other_Cancer_Rate,
        AVG(CASE WHEN hd.Depression = 'Yes' THEN 1 ELSE 0 END) AS Depression_Rate,
        AVG(CASE WHEN hd.Diabetes = 'Yes' THEN 1 ELSE 0 END) AS Diabetes_Rate,
        AVG(CASE WHEN hd.Arthritis = 'Yes' THEN 1 ELSE 0 END) AS Arthritis_Rate,
        AVG(CASE WHEN hd.Smoking_History = 'Yes' THEN 1 ELSE 0 END) AS Smoking_History_Rate,
        AVG(hd.Alcohol_Consumption) AS Average_Alcohol_Consumption,
        AVG(hd.Fruit_Consumption) AS Average_Fruit_Consumption,
        AVG(hd.Green_Vegetables_Consumption) AS Average_Green_Vegetables_Consumption,
        AVG(hd.FriedPotato_Consumption) AS Average_FriedPotato_Consumption
    FROM healthdata.`patient profile` p
    LEFT JOIN healthdata.`patient health and diet` hd ON p.ID = hd.ID
    LEFT JOIN healthdata.`patient profile` pp ON p.Age_Category = pp.Age_Category AND p.Sex = pp.Sex
    GROUP BY
        p.Age_Category,
        p.Sex
    ORDER BY
        CASE
            WHEN p.Age_Category = '18-24' THEN 1
            WHEN p.Age_Category = '25-29' THEN 2
            WHEN p.Age_Category = '30-34' THEN 3
            WHEN p.Age_Category = '35-39' THEN 4
            WHEN p.Age_Category = '40-44' THEN 5
            WHEN p.Age_Category = '45-49' THEN 6
            WHEN p.Age_Category = '50-54' THEN 7
            WHEN p.Age_Category = '55-59' THEN 8
            WHEN p.Age_Category = '60-64' THEN 9
            WHEN p.Age_Category = '65-69' THEN 10
            WHEN p.Age_Category = '70-74' THEN 11
            WHEN p.Age_Category = '75-79' THEN 12
            WHEN p.Age_Category = '80+' THEN 13
        END,
        p.Sex
);
```
OUTPUT
![image](https://github.com/VinithaVarghese/DATA-1202-FINAL-ASSIGNMENT/assets/138626409/a3d69ff4-c9c1-441a-94ca-1e6c1a6930b9)

```sql
-- Loading: View to Get Average BMI by Age Category --
CREATE VIEW Avg_BMI_By_Age AS
SELECT Age_Category, Avg_BMI
FROM AGE_GENDER_HEALTH_INSIGHTS
GROUP BY Age_Category, Avg_BMI;
```
OUTPUT
![image](https://github.com/VinithaVarghese/DATA-1202-FINAL-ASSIGNMENT/assets/138626409/10260ebf-37bd-46fd-825e-81443d66a010)

```sql
-- Loading: View to Get Age-based Depression Rates --
CREATE VIEW Age_Depression_Rates AS
SELECT Age_Category, Depression_Rate
FROM AGE_GENDER_HEALTH_INSIGHTS
GROUP BY Age_Category, Depression_Rate;
```
OUTPUT
![image](https://github.com/VinithaVarghese/DATA-1202-FINAL-ASSIGNMENT/assets/138626409/b00ab384-3ee3-4407-8cab-18f2b923f496)

```sql
-- Loading: View to Get Smoking and Alcohol Consumption Patterns --
CREATE VIEW smoking_alcohol_patterns AS
SELECT
    Sex,
    AVG(Smoking_History_Rate) AS Avg_Smoking_History_Rate,
    AVG(Average_Alcohol_Consumption) AS Avg_Average_Alcohol_Consumption
FROM AGE_GENDER_HEALTH_INSIGHTS
GROUP BY Sex;
```
OUTPUT
![image](https://github.com/VinithaVarghese/DATA-1202-FINAL-ASSIGNMENT/assets/138626409/dceb704e-530f-4503-9d2a-a5bf914ee301)

```sql
-- Loading: View to Get Nutritional Habits and Health Conditions --
CREATE VIEW Nutrition_Health_Habits AS
SELECT Age_Category, Average_Fruit_Consumption, Average_Green_Vegetables_Consumption,
       Average_FriedPotato_Consumption, Heart_Disease_Rate, Diabetes_Rate, Arthritis_Rate
FROM AGE_GENDER_HEALTH_INSIGHTS
ORDER BY Heart_Disease_Rate DESC, Diabetes_Rate DESC, Arthritis_Rate DESC;
```
OUTPUT
![image](https://github.com/VinithaVarghese/DATA-1202-FINAL-ASSIGNMENT/assets/138626409/622a0256-1be3-4b00-aaa7-947a0e56b1a9)

