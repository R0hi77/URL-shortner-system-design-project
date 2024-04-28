# SYSTEM DESIGN OF A URL SHORTNER
In this project i explore the design of URL shortner service show casing my proficiency in the basics system design.
## Functional requirements:
    Given a long URL the system should generate a short url and unique equivalent
    
    Short URL should redirect to long url when hit

    Short URL link should expire after 1 week

## Non Functinal requirements:
    System should be highly available

    Real-time redirection

    Shortened links should not be predictable

## High level archtecture 
![highlevel architecture](/images/Screenshot%20from%202024-04-27%2015-14-27.png)

In this all functions are coupled into a single API using a layered architecture mainly because it is not very complex

### Below is a breakdown of the various functions
 1. URL shortening 
 ![url shortner](/images/Screenshot%20from%202024-04-27%2015-40-34.png)

 - Shortened URL should have a maximum of 7 characters 
 - Shortened URL should be unique for each entry
 #### To meet the above requirements i opt for B62 encoding because of the following:
 - Bi-directional: String <=> Hash
 - uses a characters a-z A-Z 0-9. This makes generated hashes very readable and human friendlly
 - 3521614606208 different unique combinations can be formed


 User posts a long url to be shortened ``` POST  api/shorturl``` 
 with payload `{ url: https://testsite.com/somerandomlongurl }` API cross checks the database to ensure short version exist. If a short version exists it is returned to the user else a short url is generated and stored in the database `{ https://testsite.com/randomlongurl : 2rcbOiq}` and returned  to the user


 2. url redirection
 ![url redirection](/images/Screenshot%20from%202024-04-27%2017-42-42.png)
 System redirection happens when user makes a `GET api/2rcb0iq` API call, The system fetches the long equivalent and makes a request to the original link `{https: //testsite.com/randomlongurl}` and makes a permanent redirection.


 3. Cleaning up old links

To achieve this a script that performs the clean up function can be executed periodically as a cron job.
- pseudo code for script:
```
establish connection to the db
fetch entries in the db
for every entry in db:
if timestamp >= 7 days 
remove entry from db 

```
