Solution for Northwestern University Knight Lab SQL Murder Mystery

--crime_scene_report 
--drivers_license
--person
--facebook_event_checkin
--interview
--get_fit_now_member
--get_fit_now_check_in
--income
--solution


CREATE TABLE crime_scene_report ( date Integer, type Text, description Text, city Text )

SELECT * FROM crime_scene_report 
WHERE date = 20180115 AND City = 'SQL City' AND type = 'murder';

--Output:
--date	type	description	city
--20180115	murder	Security footage shows that there were 2 witnesses. The first witness lives at the last house on "Northwestern Dr". The second witness, named Annabel, lives somewhere on "Franklin Ave".	SQL City

—Security footage shows that there were 2 witnesses. The first witness lives at the last house on "Northwestern Dr". The second witness, named Annabel, lives somewhere on "Franklin Ave”.—


CREATE TABLE person (id Integer PRIMARY KEY, name Text, license_id Integer, address_number Integer, address_street_name Text, ssn Integer, FOREIGN KEY (license_id) REFERENCES drivers_license(id))

SELECT *, MAX(address_number)  FROM Person 
WHERE address_street_name = 'Northwestern Dr';

--Output:
--id	name	license_id	address_number	address_street_name	ssn	Address
--14887	Morty Schapiro	118009	4919	Northwestern Dr	111564949	4919


SELECT  *  FROM Person 
WHERE address_street_name = 'Franklin Ave' AND Name LIKE '%Annabel%';

--Output:
--id	name	license_id	address_number	address_street_name	ssn
--16371	Annabel Miller	490173	103	Franklin Ave	318771143


CREATE TABLE interview (FOREIGN KEY (person_id) Integer REFERENCES person(id), transcript Text)

SELECT * FROM interview
WHERE person_id IN (14887, 16371)

Output:
person_id	transcript
14887	I heard a gunshot and then saw a man run out. He had a "Get Fit Now Gym" bag. The membership number on the bag started with "48Z". Only gold members have those bags. The man got into a car with a plate that included "H42W".
16371	I saw the murder happen, and I recognized the killer from my gym when I was working out last week on January the 9th.


Select

	get_fit_now_member.person_id, 
	get_fit_now_member.id,
	get_fit_now_member.name,
	get_fit_now_member.membership_start_date,
	get_fit_now_check_in.membership_id,  
	get_fit_now_check_in.check_in_date,
	get_fit_now_check_in.check_in_time,
	get_fit_now_check_in.check_out_time
	
From get_fit_now_member

Join get_fit_now_check_in ON get_fit_now_member.id = get_fit_now_check_in.membership_id

Where get_fit_now_member.person_id IN (14887, 16371)

Output: —Corroborate Annabel Miller Story—
person_id	id	name	membership_start_date	membership_id	check_in_date	check_in_time	check_out_time
16371	90081	Annabel Miller	20160208	90081	20180109	1600	1700


CREATE TABLE facebook_event_checkin (person_id Integer FOREIGN KEY, event_id Integer, event_name Text, date Integer)

SELECT * FROM facebook_event_checkin
WHERE person_id IN (14887, 16371)

Output: —Both were at an event the day of the incident—
person_id	event_id	event_name	date
14887	4719	The Funky Grooves Tour	20180115
16371	4719	The Funky Grooves Tour	20180115


CREATE TABLE get_fit_now_member (id Integer PRIMARY KEY, person_id Integer FOREIGN KEY, name Text, membership_start_date Integer, membership_status Text)

SELECT
 
  get_fit_now_check_in.membership_id,
  get_fit_now_check_in.check_in_date,  
  get_fit_now_member.id,
  get_fit_now_member.person_id,
 get_fit_now_member.membership_status,
  get_fit_now_member.name,
  person.id,
  person.license_id,
  drivers_license.id,
  drivers_license.plate_number,
  drivers_license.car_make,
  drivers_license.car_model
  
FROM 
	person
 
JOIN get_fit_now_check_in ON get_fit_now_member.id = get_fit_now_check_in.membership_id
  JOIN get_fit_now_member ON person.id = get_fit_now_member.person_id
  JOIN drivers_license ON person.license_id = drivers_license.id
where membership_status = 'gold' AND get_fit_now_member.id LIKE '%48z%'
AND check_in_date = 20180109 AND plate_number LIKE '%H42W%'

Output:
membership_id	check_in_date	id	person_id	membership_status	name	id	license_id	id	plate_number	car_make	car_model
48Z55	20180109	48Z55	67318	gold	Jeremy Bowers	67318	423327	423327	0H42W2	Chevrolet	Spark LS

Select * From Interview 
Where person_id = 67318

Output:
person_id	transcript
67318	I was hired by a woman with a lot of money. I don't know her name but I know she's around 5'5" (65") or 5'7" (67"). She has red hair and she drives a Tesla Model S. I know that she attended the SQL Symphony Concert 3 times in December 2017.


SELECT

	person.license_id, 
	person.id, 
	person.name,
	drivers_license.id,
	income.annual_income
	
FROM person

JOIN drivers_license ON person.license_id = drivers_license.id
JOIN income ON person.ssn = income.ssn

WHERE 
	car_make = 'Tesla' 
AND car_model = 'Model S' 
AND hair_color = 'red' 
AND height BETWEEN 65 AND 67 

Output:
license_id	id	name	id	annual_income
918773	78881	Red Korb	918773	278000
202298	99716	Miranda Priestly	202298	310000


SELECT 

	facebook_event_checkin.person_id, 
	facebook_event_checkin.event_id,
	facebook_event_checkin.event_name,
	facebook_event_checkin.date,  
	person.name	
	
FROM facebook_event_checkin

JOIN person ON facebook_event_checkin.person_id = person.id

WHERE event_name = 'SQL Symphony Concert' 
AND facebook_event_checkin.date 
LIKE '%201712%' 
AND person.id IN (99716, 78881)

Output:
person_id	event_id	event_name	date	name
99716	1143	SQL Symphony Concert	20171206	Miranda Priestly
99716	1143	SQL Symphony Concert	20171212	Miranda Priestly
99716	1143	SQL Symphony Concert	20171229	Miranda Priestly
