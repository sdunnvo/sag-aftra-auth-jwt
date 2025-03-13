SQL answers:

```
/*
Person Table
Person_ID, Name, Actor Type (Actor,Voice,), Birth Date
 
Address Table
Address_ID, Addr_Line 1, Line2, State, City, State, Zip Code, Effective_Start_date , Effective_end_date
 
Person_Address_link
Person_ID, Address_ID
 
Earnings Table
Earnings_ID, Wage_amount, Employee_ID, Person_id

Employee Table
Employee_Id, Employer_Name
*/

1 
List all the persons with active address
::
SELECT p.Name 
FROM Person as p, Person_Address_link as pal, Address as addy 
WHERE p.Person_ID = pal.Person_ID and
      addy.Address_ID = pal.Address_ID and 
      (addy.Effective_end_date = NULL or addy.Effective_end_date > DUAL.now()) 
      

2
Find the person with no active address
::
SELECT per.name
FROM  Person as per
WHERE per.Person_ID not in (
    SELECT p.Person_ID 
    FROM Person as p, Person_Address_link as pal, Address as addy 
    WHERE p.Person_ID = pal.Person_ID and
          addy.Address_ID = pal.Address_ID and 
          (addy.Effective_end_date = NULL or addy.Effective_end_date > DUAL.now()) 
)


3
Find persons with duplicate address for the given start date and end date
::
SELECT per3.name
FROM Person as per3
WHERE per3.Person_ID in (
	SELECT p3.Person_ID, 
		addy3.Addr_Line1, addy3.Line2, addy3.State, addy3.City, addy3.Zip Code, 
		addy3.Effective_Start_date, addy3.Effective_end_date, count(*) as dupeAddyDates
	FROM Person as p3, Address as addy3, Person_Address_link pal3
	GROUP BY p3.Person_ID,
		addy3.Addr_Line1, addy3.Line2, addy3.State, addy3.City, addy3.Zip Code, 
		addy3.Effective_Start_date, addy3.Effective_end_date
	WHERE p3.Person_ID = pal3.Person_ID and
	      addy3.Address_ID = pal3.Address_ID and
	      dupeAddyDates > 1
)


4
Provide the summary for a given person – Summary of earnings by each employer for a given person
::
SELECT p4.name, earn4.Employee_ID, empl4.Employer_Name, SUM(earn4.Wage_amount) as wageSummary_PerEmployer_PerEmployeeIDPersonName
FROM Person as p4, Earnings as earn4, Employee as empl4
GROUP BY earn4.Employee_ID, empl4.Employer_Name
WHERE p4.Person_ID = earn4.Person_ID and
	earn4.Employee_ID = empl4.Employee_ID


5
Provide the summary for a given employer – Summary of earnings by employer for a given employer with more than million $ in earnings
::
SELECT empl5.Employer_Name, SUM(earn5.Wage_amount) as millionaireEmployersOnly
FROM Earnings as earn5, Employee as empl5
GROUP BY empl5.Employer_Name
WHERE empl5.Employee_ID = earn5.Employee_ID and
	millionaireEmployersOnly > 1000000
```

=========================================
User Authentication and Authorization
=========================================

Task: Build a simple user authentication system using Spring Boot and JWT.

Requirements:
Implement user registration and login endpoints.

Store user details securely using Spring Security and password hashing.

Generate and validate JWT tokens for authentication.

Restrict access to a secured endpoint (e.g., /profile) so that only authenticated users can access it.

Use an in-memory database (H2) for storing users.

<>

4 endpoints have been tested successfully. JWT Filtering for authenticated users has yet to be tested (will have completed test results in the next 24 hours)

```
unsecured
|GET|:7007/authuser/bemvindo
|POST|:7007/authuser/addNewUser
|POST|:7007/authuser/login
|POST|:7007/authuser/generateToken
```

/addNewUser _ data samples:
```
{
    "name": "Holden.Caufield"
    "email": "h.cauf@catcher.inthe.rye",
    "username": "caufinated",
    "password": "dontp@$$theB9”,
    "roles": "ROLE_BASEUSER,ROLE_ADMIN"
}

{
    "name": "Michael.Knight",
    "email": "kitt@glen.a.larson",
    "username": "hasslehauffen",
    "password": "dontp@$$theV8,
    "roles": “ROLE_BASEUSER"
}

{
    "name": "Gal.Gadot",
    "email": "ww@dc.comics",
    "username": "gadottie",
    "password": "dontp@$$theN84",
    "roles": "ROLE_ADMIN"
}

{
    "name": "Ebenezer Scrooge",
    "email": "h.cauf@catcher.inthe.rye",
    "username": "ebenezzled",
    "password": "dontp@$$theB9",
    "roles": "ROLE_BASEUSER,ROLE_ADMIN"
}
```

/generateToken | /login _ data samples:
```
{
    "username": "caufinated",
    "password": "dontp@$$theB9"
}

{
    "username": "gadottie",
    "password": "dontp@$$theN84"
}

{
    "username": "ebenezzled",
    "password": "dontp@$$theB9"
}
```
