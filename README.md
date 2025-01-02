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

### Step 4: Checking Police Interviews
```sql
select * 
from Interview
where person_id in (14887, 16371);
```
| person_id | transcript                                                                                                  |
|-----------|------------------------------------------------------------------------------------------------------------|
| 14887     | I heard a gunshot and then saw a man run out. He had a "Get Fit Now Gym" bag. The membership number started with "48Z". Only gold members have those bags. The man got into a car with a plate that included "H42W". |
| 16371     | I saw the murder happen, and I recognized the killer from my gym when I was working out last week on January the 9th. |

### Step 5: Finding the Murderer
Using clues from the interview:
* The murderer has a "Get Fit Now Gym" gold membership.
* Their gym membership ID starts with "48Z".
* They drive a car with a license plate containing "H42W".
* They were at the gym on Jan. 9, 2018.

```sql
select gym.id, gym.person_id, p.license_id, p.name, plate_number
from get_fit_now_member gym
inner join person p on gym.person_id = p.id
inner join drivers_license dl on p.license_id = dl.id
where gym.id like("48z%") and gym.membership_status = "gold" and plate_number like("%H42W%");
```
| id     | person_id | license_id | name           | plate_number |
|--------|-----------|------------|----------------|--------------|
| 48Z55  | 67318     | 423327     | Jeremy Bowers  | 0H42W2       |

### Step 6: Verifying the Gym Check-In on Jan. 9, 2018
```sql
select * 
from get_fit_now_check_in
where membership_id = "48Z55" and check_in_date = 20180109;
```
| membership_id | check_in_date | check_in_time | check_out_time |
|---------------|---------------|---------------|----------------|
| 48Z55         | 20180109      | 1530          | 1700           |

### Step 7: Final Verification of the Murderer
```sql
INSERT INTO solution VALUES (1, 'Jeremy Bowers');
SELECT value FROM solution;
```
| value           |
|-----------------|
| Jeremy Bowers   |

🔍 Congrats, you found the murderer! But wait, there's more... If you think you're up for a challenge, try querying the interview transcript of the murderer to find the real villain behind this crime. If you feel especially confident in your SQL skills, try to complete this final step with no more than 2 queries. Use this same INSERT statement with your new suspect to check your answer.

### Step 8: Finding the Real Villain
```sql
select * from interview where person_id = 67318;
```
Through the interview, the murderer claims that they were hired by a woman with red hair, around 5'5" to 5'7" tall, who drives a Tesla Model S. She attended the SQL Symphony Concert 3 times in December 2017.

```sql
select * from drivers_license 
where height >= 65 AND height <= 67 and hair_color = 'red' 
and gender='female' and car_make = 'Tesla' and car_model like ('Model S');
```
| id     | age | height | eye_color | hair_color | gender | plate_number | car_make | car_model |
|--------|-----|--------|-----------|------------|--------|--------------|----------|-----------|
| 202298 | 68  | 66     | green     | red        | female | 500123       | Tesla    | Model S  |
| 291182 | 65  | 66     | blue      | red        | female | 08CM64       | Tesla    | Model S  |
| 918773 | 48  | 65     | black     | red        | female | 917UU3       | Tesla    | Model S  |

### Step 9: Finalizing the Villain
```sql
select p.id, p.name
from person p
inner join(
    select * from drivers_license 
    where height >= 65 AND height <= 67 and hair_color = 'red' 
    and gender='female' and car_make = 'Tesla' and car_model like ('Model S')) dl
on p.license_id = dl.id;
```
| id    | name             |
|-------|------------------|
| 78881 | Red Korb |
| 90700 | Regina George |
| 99716 | Miranda Priestly |

### Step 10: Verifying the SQL Symphony Concert Attendance
```sql
select p.id, p.name
from person p
inner join(
    select * from drivers_license 
    where height >= 65 AND height <= 67 and hair_color = 'red' and 
          gender='female' and car_make = 'Tesla' and car_model like ('Model S')) dl
on p.license_id = dl.id
inner join (
    select person_id from facebook_event_checkin
    where event_name = 'SQL Symphony Concert' and date like('201712__')
    group by person_id
    having count(person_id) = 3) fec
on p.id = fec.person_id;
```
| id    | name             |
|-------|------------------|
| 99716 | Miranda Priestly |

### Step 11: Final Answer
```sql
INSERT INTO solution VALUES (1, 'Miranda Priestly');  
SELECT value FROM solution;
```
🕵️‍♂️🚀 Congrats, you found the brains behind the murder! Everyone in SQL City hails you as the greatest SQL detective of all time. Time to break out the champagne!
