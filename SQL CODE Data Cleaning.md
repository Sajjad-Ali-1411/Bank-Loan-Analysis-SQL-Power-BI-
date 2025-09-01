# SQL CODE for Data Cleaning



      CREATE TABLE financial\_loan (
      
            id BIGINT PRIMARY KEY,
        
            address\_state VARCHAR(5),
        
            application\_type VARCHAR(50),
        
            emp\_length VARCHAR(20),
        
            emp\_title VARCHAR(255),
        
            grade CHAR(1),
        
            home\_ownership VARCHAR(20),
        
            issue\_date DATE,
        
            last\_credit\_pull\_date DATE,
        
            last\_payment\_date DATE,
        
            loan\_status VARCHAR(50),
        
            next\_payment\_date DATE,
        
            member\_id BIGINT,
        
            purpose VARCHAR(100),
        
            sub\_grade VARCHAR(5),
        
            term VARCHAR(20),
        
            verification\_status VARCHAR(50),
        
            annual\_income NUMERIC(15,2),
        
            dti NUMERIC(10,4),
        
            installment NUMERIC(12,2),
        
            int\_rate NUMERIC(6,4),
        
            loan\_amount BIGINT,
        
            total\_acc INT,
        
            total\_payment BIGINT
      
      );

---


# DATA CLEANING STEPS: 



1\. Convert Date Columns (issue\_date, last\_credit\_pull\_date, last\_payment\_date, next\_payment\_date)



Currently, they are strings (DD-MM-YYYY). Converting them to proper DATE:



-- Convert issue\_date

        ALTER TABLE financial_loan
      
        ALTER COLUMN issue_date TYPE DATE
      
        USING TO_DATE(issue_date, 'DD-MM-YYYY');


-- Convert last\_credit\_pull\_date

      ALTER TABLE financial_loan
      
      ALTER COLUMN last_credit_pull_date TYPE DATE
      
      USING TO\_DATE(last_credit_pull_date, 'DD-MM-YYYY');


-- Convert last\_payment\_date

      ALTER TABLE financial_loan
      
      ALTER COLUMN last_payment_date TYPE DATE
      
      USING TO_DATE(last_payment_date, 'DD-MM-YYYY');
      

-- Convert next\_payment\_date (if empty values exist, they’ll become NULL)

      ALTER TABLE financial_loan
      
      ALTER COLUMN next_payment_date TYPE DATE
      
      USING TO_DATE(next_payment_date, 'DD-MM-YYYY');


---


2\. Clean Employment Length (emp\_length)



Values are like "< 1 year", "10+ years", "9 years". Converting to numeric years of experience:



ALTER TABLE financial\_loan ADD COLUMN emp\_length\_years INT;



UPDATE financial_loan

      SET emp_length_years =
      
          CASE
      
              WHEN emp_length = '< 1 year' THEN 0
      
              WHEN emp_length = '10+ years' THEN 10
      
              WHEN emp_length = '[0-9]+ years$' THEN CAST(split_part(emp_length, ' ', 1) AS INT)
      
              ELSE NULL
      
          END;

---

3\. Convert Interest Rate (int\_rate)

ALTER TABLE financial\_loan ADD COLUMN int\_rate\_percent NUMERIC(5,2);



      UPDATE financial_loan
      
      SET int_rate_percent = ROUND(int_rate \* 100, 2);

---


4\. Handle Missing or Inconsistent Data

Checking for missing values:

-- Counting NULLs per column

      SELECT column_name,
      
             COUNT(*) FILTER (WHERE column_name IS NULL) AS null_count
      
      FROM financial_loan
      
      GROUP BY column_name;

---


5\. Standardize Categorical Fields



Example: home\_ownership have inconsistent values (MORTGAGE, Mortgage, mortgaged).



    UPDATE financial_loan
    
    SET home_ownership = UPPER(home_ownership);







