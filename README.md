# sqlclues
SQL queries to collect clues in a hidden database.

# Task: Keep a log of any SQL queries you execute as you solve the mystery.

# Find the description in the crime scene report from Day = 28, month = 07, year =2021

SELECT description FROM crime_scene_reports WHERE year = 2021 AND month = 07 AND day = 28;

# Find the names and transcripts of the three witnesses where the transcript contains the phrase "bakery"

SELECT name, transcript FROM interviews WHERE month = 07 AND day = 28 AND transcript LIKE '%bakery%';

# Find Eugene's account # to look at prior transactions
SELECT account_number FROM bank_accounts WHERE person_id =(SELECT id, name FROM people WHERE name LIKE 'Eugene%');
# Info from database: Eugene's acct #: 285790309
# info from database ID 280744

# Find a list of acct #s and withdrawls from the ATM on Leggett Street before Eugene's transaction per witness transcript
# Use the ATM acct #s to look up the peoples ID's and find their names

SELECT people.name, atm_transactions.transaction_type FROM people
JOIN bank_accounts ON bank_accounts.person_id = people.id
JOIN atm_transactions ON atm_transactions.account_number = bank_accounts.account_number
WHERE atm_transactions.day = 28
AND atm_transactions.month = 7
AND atm_transactions.year = 2021
AND atm_location = "Leggett Street"
AND atm_transactions.transaction_type = "withdraw";

# Info from database: Names: Bruce, Diana, Brooke, Kenny, Iman, Luca, Taylor, Benista

# Find the names of the callers on a phone call for less than a minute per witness transcript

SELECT caller, receiver FROM phone_calls WHERE year = 2021 AND month = 07 AND day = 28 AND duration <= 60;

# Run each query once, then remove
ALTER TABLE phone_calls
ADD caller_name text;

ALTER TABLE phone_calls
ADD receiver_name text;

# Now, add in the colums to the previous query
SELECT caller, caller_name, receiver, receiver_name FROM phone_calls WHERE year = 2021 AND month = 07 AND day = 28 AND duration <= 60;

# Run two commands once
# When ran the second time, copy and paste the origional query down below the commands
UPDATE phone_calls
SET caller_name = people.name
FROM people
WHERE phone_calls.caller = people.phone_number;


UPDATE phone_calls
SET receiver_name = people.name
FROM people
WHERE phone_calls.receiver = people.phone_number;

SELECT caller, caller_name, receiver, receiver_name FROM phone_calls WHERE year = 2021 AND month = 07 AND day = 28 AND duration <= 60;

# info from databse: Bruce called Robin


# Check the security cameras from the parking lot to see if any of the three left the scene between 10:15-10:25 am

SELECT bakery_security_logs.activity, bakery_security_logs.license_plate, people.name
FROM people JOIN bakery_security_logs ON bakery_security_logs.license_plate = people.license_plate
WHERE bakery_security_logs.day = 28
AND bakery_security_logs.month = 07
AND bakery_security_logs.year = 2021
AND bakery_security_logs.hour = 10
AND bakery_security_logs.minute >= 14
AND bakery_security_logs.MINUTE <= 25;

# Possible drivers: Vanessa, Bruce, Iman, Sofia, Luca, Diana, Kelsey

# Find the name of the airport and its ID in Fiftyville Regional Airport
SELECT full_name, abbreviation, id FROM airports WHERE city = 'Fiftyville';

# Look up early morning flights departure times from CSF (id = 8)on 07/29/2021 and thier destination
SELECT destination_airport_id, hour, minute FROM flights
WHERE origin_airport_id = '8' AND year = 2021 AND month = 07 AND day = 29 AND hour < 12;

# FIND the names of the destination airport
SELECT full_name, abbreviation FROM airports WHERE id IN
(SELECT destination_airport_id FROM flights
WHERE origin_airport_id = '8' AND year = 2021 AND month = 07 AND day = 29 AND hour < 12);

# Confirm the airport id to the flight
SELECT city FROM airports WHERE id = 4;
# is LaGuardia
SELECT city FROM airports WHERE id = 1;
# is O'Hare

# Look up the names of the passengers on the flight to LaGuardia
SELECT name FROM people WHERE passport_number IN
(SELECT passport_number FROM passengers WHERE flight_id =
(SELECT id FROM flights WHERE destination_airport_id = '4' AND year = 2021 AND month = 07 AND day = 29 AND hour = '8' ));
# Passengers: Kenny, Sofia, Taylor, Luca, Kelsey, Edward, Bruce, Doris

# Look up the names of the passengers on the flight to O'Hare
SELECT name FROM people WHERE passport_number IN
(SELECT passport_number FROM passengers WHERE flight_id =
(SELECT id FROM flights WHERE destination_airport_id = '1' AND year = 2021 AND month = 07 AND day = 29 AND hour = '9'));
# Heather, Rebecca, Marilyn, Carol, Sophia, Daniel

# CONCLUSIONS FROM QUERIES
# The ealry morning flights were to 9:30 to Chicago O'Hare and  8:20 to NYC LaGuardia
# Members in our investigation were on the flight to LaGuardia, so the suspect must've been involved in that flight.
# Only Bruce is the common character that was on the flight, at the atm, left the parking lot
# around the time of the crime, and had a phone call for less than a minute in which he called his accomplice, Robin
# Bruce is the suspect and Robin is the accomplice in this fictional senario.


