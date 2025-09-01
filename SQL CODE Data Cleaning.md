SQL CODE for Data Cleaning



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





**DATA CLEANING STEPS: (Optional if you want you can do)**



1\. Convert Date Columns (issue\_date, last\_credit\_pull\_date, last\_payment\_date, next\_payment\_date)



Currently, they are strings (DD-MM-YYYY). Converting them to proper DATE:



-- Convert issue\_date

ALTER TABLE financial\_loan

ALTER COLUMN issue\_date TYPE DATE

USING TO\_DATE(issue\_date, 'DD-MM-YYYY');



-- Convert last\_credit\_pull\_date

ALTER TABLE financial\_loan

ALTER COLUMN last\_credit\_pull\_date TYPE DATE

USING TO\_DATE(last\_credit\_pull\_date, 'DD-MM-YYYY');



-- Convert last\_payment\_date

ALTER TABLE financial\_loan

ALTER COLUMN last\_payment\_date TYPE DATE

USING TO\_DATE(last\_payment\_date, 'DD-MM-YYYY');



-- Convert next\_payment\_date (if empty values exist, they’ll become NULL)

ALTER TABLE financial\_loan

ALTER COLUMN next\_payment\_date TYPE DATE

USING TO\_DATE(next\_payment\_date, 'DD-MM-YYYY');





2\. Clean Employment Length (emp\_length)



Values are like "< 1 year", "10+ years", "9 years". Converting to numeric years of experience:



ALTER TABLE financial\_loan ADD COLUMN emp\_length\_years INT;



UPDATE financial\_loan

SET emp\_length\_years =

    CASE

        WHEN emp\_length = '< 1 year' THEN 0

        WHEN emp\_length = '10+ years' THEN 10

        WHEN emp\_length ~ '^\[0-9]+ years$' THEN CAST(split\_part(emp\_length, ' ', 1) AS INT)

        ELSE NULL

    END;







3\. Convert Interest Rate (int\_rate)





ALTER TABLE financial\_loan ADD COLUMN int\_rate\_percent NUMERIC(5,2);



UPDATE financial\_loan

SET int\_rate\_percent = ROUND(int\_rate \* 100, 2);







4\. Handle Missing or Inconsistent Data



Checking for missing values:



-- Count NULLs per column

SELECT column\_name,

       COUNT(\*) FILTER (WHERE column\_name IS NULL) AS null\_count

FROM financial\_loan

GROUP BY column\_name;







5\. Standardize Categorical Fields



Example: home\_ownership have inconsistent values (MORTGAGE, Mortgage, mortgaged).



UPDATE financial\_loan

SET home\_ownership = UPPER(home\_ownership);

