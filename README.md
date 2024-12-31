Solve the SQL Murder Mystery using SQL queries! Follow clues, analyze data, and uncover the mastermind behind the crime. 🕵️‍♂️🚀

# 🔍 SQL Murder Mystery 🎯

A crime has taken place, and the detective needs your help. The detective gave you the crime scene report, but you lost it. The only details you remember are:  
- The crime was a **murder**.  
- It occurred on **January 15, 2018**.  
- The location was **SQL City**.

## 🚶 Steps to Solve the Mystery  

### Step 1: Understanding the `Crime_scene_report` Table  
```sql
SELECT *  FROM Crime_scene_report LIMIT 5;
```

### Step 2: Following the Initial Clue
The crime occurred in SQL City on January 15, 2018.
```sql
SELECT * 
FROM Crime_scene_report
WHERE date = '20180115' AND city = 'SQL City';
```
### Step 3: Searching for Witnesses
🔍 The security footage shows:
* A witness lives at the last house on Northwestern Dr.
* Another witness, named Annabel, lives somewhere on Franklin Ave.
```sql
SELECT a.* 
FROM (
    SELECT * 
    FROM person
    WHERE address_street_name = "Northwestern Dr"
    ORDER BY address_number DESC
    LIMIT 1 ) AS a
UNION
SELECT * 
FROM person
WHERE address_street_name = "Franklin Ave" AND name LIKE("Annabel%");
```
Output:
| id    | name            | license_id | address_number | address_street_name | ssn        |
|-------|-----------------|------------|----------------|----------------------|------------|
| 14887 | Morty Schapiro  | 118009     | 4919           | Northwestern Dr      | 111564949  |
| 16371 | Annabel Miller  | 490173     | 103            | Franklin Ave         | 318771143  |




