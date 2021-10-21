****## Belajar POSTGRE SQL

### CREATE DATABASE
```sql
CREATE DATABASE my_database;
```

### CREATE TABLE 
```sql
CREATE TABLE person(
    id BIGSERIAL NOT NULL PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50),
    email VARCHAR(100),
    date_of_birth DATE NOT NULL DEFAULT CURRENT_TIMESTAMP
);

```

### INSERT 
```sql
INSERT INTO person(first_name,last_name,date_of_birth,email) 
VALUES ('Anne', 'Smith', '2021-02-01', 'anne@slashdot.com')
VALUES('Jhon', 'Dhoe', '2021-01-02')
```

### SELECT
#### SELECT ALL
```sql
SELECT * FROM person;
```

#### SELECT SPECIFIC COLUMN
```sql
SELECT first_name,last_name FROM person;
```

#### SELECT WITH WHERE CLAUSE
```sql
SELECT first_name,last_name, FROM person WHERE last_name = 'Anne'
```

### AND OPERATOR
```sql
SELECT first_name,last_name FROM person WHERE last_name = 'Smith' AND birth_of_date = '2021-01-02'
```

### OR OPERATOR
```sql
SELECT first_name, last_name FROM person WHERE last_name = 'Smith' OR last_name = 'Dhoe';
```

### BETWEEN
```sql
SELECT * FROM person WHERE date_of_birth BETWEEN '2021-02-01' AND '2021-02-10';
```

### IN 
```sql
SELECT * FROM person WHERE date_of_birth IN ('2021-01-02','2021-01-02','2021-01-03')
```

### LIMIT, OFFSET, FETCH
#### LIMIT WITHOUT OFFSET
```sql
SELECT * FROM person LIMIT 10;
```

#### LIMIT WITH PERSON
```sql
SELECT * FROM person OFFSET 10 LIMIT 10;
```

#### FETCH WITHOUT OFFET
```sql
SELECT * FROM person FETCH FIRST 10 ROW ONLY;
```

#### FETCH WITH OFFSET
```sql
SELECT * FROM person OFFSET 10 FETCH FIRST 10 ROW ONLY
```

### LIKE
```sql
SELECT * FROM person WHERE email LIKE '%rlife%'
```

```sql
SELECT * FROM person WHERE email LIKE '___@%'
```

### COALESCE
ini akan memberikan nilai default jika nilai column berupa null
```sql
SELECT first_name,COALESCE(email,'Email Not Provided') FROM person;
```
output dari query diatas adalah menggantikan email yang berupa nilai null,menjadi 'Email Not Provided'

### NULLIF
ini akan menghasilkan output berupa parameter pertama jika parameter pertama dan kedua tidak sama, dan akan menghasilkan null, jika parameter pertama dan parameter kedua sama
```sql
-- ini akan menghasilkan null
SELECT NULLIF(10,10); 

-- ini akan menghasilkan 10
SELECT NULLIF(10,5);
```

### COALESCE X NULLIF
```sql
-- ini akan menghasilkan null
SELECT 10 / NULL;

SELECT COALESCE(10 / NULLIF(0,0),0)
```

### Adding and Subtracting with date and timestamp
```sql
    SELECT NOW() + INTERVAL '1 YEAR';
    SELECT NOW() - INTERVAL '1 YEAR';
    SELECT NOW() + INTERVAL '1 MONTH';
    SELECT NOW() - INTERVAL '1 MONTH';
    SELECT NOW() + INTERVAL '1 DAYS';
    SELECT NOW() - INTERVAL '1 DAYS';

    -- CASTING
    SELECT (NOW() - INTERVAL '1 YEAR')::DATE;
```

### EXTRACT FROM TIMESTAMP
```sql
    SELECT EXTRACT('year' FROM NOW());
    SELECT EXTRACT('month' FROM NOW());
    SELECT EXTRACT('day' FROM NOW());
    SELECT EXTRACT('hour' FROM NOW());
    SELECT EXTRACT('minute' FROM NOW());
    SELECT EXTRACT('second' FROM NOW());
```

### GET AGE / TIME DIFFERENCE (selisih waktu)
```sql
    SELECT AGE(NOW(),birth_of_date) FROM personl
    -- Output
    -- 11 mons 26 days 08:47:10.39097
```

#### EXTRACT FROM AGE
```sql
    SELECT EXTRACT('year' FROM AGE(NOW(),birth_of_date)) FROM personl
    SELECT EXTRACT('month' FROM AGE(NOW(),birth_of_date)) FROM personl
```

### PRIMARY KEY
fungsi dari primary key adalah memberikan identifier / tanda pengenal pada masing - masing row data sebagai tanda pengenal unique
```sql
 -- add primary when init new table
 CREATE TABLE person(
     id BIGSERIAL NOT NULL PRIMARY KEY
 );

 -- add primary key to existing table (user defined constraint name)
    ALTER TABLE person ADD CONSTRAINT person_pkey PRIMARY KEY (id)

-- add primary key to existing table (automatic name)
    ALTER TABLE person ADD PRIMARY KEY (id)

-- remove primary key
    ALTER TABLE person REMOVE CONSTRAINT person_pkey;
```

### UNIQUE CONSTRAINT
fungsi dari UNIQUE CONSTRAINT adalah untuk memastikan bahwa nilai dalam sebuah kolom unique (tidak terdapat pada kolom lain)
```sql
    -- add unique constraint when init new table
    CREATE TABLE person(
        email varchar(100) NOT NULL UNIQUE
    );

    -- add unique constraint to existing table (automatic constraint name)
    ALTER TABLE person ADD UNIQUE(email)

    -- add unique constraint to existing table (user defined name)
    ALTER TABLE person ADD CONSTRAINT person_uemail UNIQUE(email)

    -- remove unique constraint
    ALTER TABLE person REMOVE CONSTRAINT person_uemail
```

### CHECK CONSTRAINT
fungsi dari CHECK CONSTRAINT adalah untuk memastikan bahwa data yang diinputkan sesuai dengan ketentuan
```sql
 -- add check constraint when init new table
    CREATE TABLE person(
        gender varchar(100) NOT NULL CHECK(gender = 'male' OR gender 'female')
    )
-- add check constraint to existing table(automatic name)
    ALTER TABLE person ADD CHECK(gender = 'male' OR gender = 'female')
-- add check constraint to existing table(user defined constraint name)
    ALTER TABLE person ADD CONSTRAINT check_gender CHECK(gender = 'male' OR gender = 'female')
-- remove constraint 
    ALTER TABLE person DROP CONSTRAINT check_gender;
```

### DELETE DATA
```sql
    -- delete all data
    DELETE FROM person;

    -- delete specific data
    DELETE FROM person WHERE id = 1;

    -- delete with condition
    DELETE FROM person WHERE email = 'x@gmail.com' AND country = 'Indonesia'
```
### UPDATE DATA
```sql
UPDATE person SET first_name = 'jamal', last_name = 'udin', email = 'riz@gmail.com', gender = 'Male', country_of_date = 'Indonesia' WHERE id = 2006;
```

### ON CONFLICT DO NOTHING
ini berguna untuk melakukan error handling jika terjadi duplikasi saat insert data pada column atau row yang seharusnya unique,
sehingga query tidak mengembalikan pesan error, dan akan mengabaikan query tersebut, fungsi ini hanya bekerja pada kolom yang
memiliki UNIQUE CONSTRAINT atau kolom yang merupakan PRIMARY KEY
```sql
 INSERT INTO person(id,first_name, last_name, email, gender, birth_of_date, country_of_date) VALUES(2047,'A','B','emattam15@addtoany.com','Male','1992-01-02', 'Indonesia') ON CONFLICT(id) DO NOTHING
```

### UPSERT
ini berguna sama seperti ON CONFLICT DO NOTHING, tapi fungsi ini bukan mengabaikan query yang dikirim, tapi melakukan ekseskusi pada query tersebut
dengan cara melakukan update terhadap data yang dituju
```sql
INSERT INTO person(id,first_name, last_name, email, gender, birth_of_date, country_of_date) VALUES(2047,'A','B','anjay@gmail.com','Male','1992-01-02', 'Indonesia') ON CONFLICT(id) DO UPDATE SET email = EXCLUDED.email;
```
query diatas akan dieksekusi, tapi tidak untuk insert data, melainkan untuk update data dengan id 2047.