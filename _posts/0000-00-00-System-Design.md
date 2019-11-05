---
layout: post
title:  System Design
date:   2019-11-05 01:00:00 +0800
categories: 不周山
tag: OOD
---


* content
{:toc}

## Design a URL Shortener ( TinyURL ) System
### Problem
Design a service like TinyURL, a URL shortening service, a web service that provides short aliases for redirection of long URLs.

### Solution
If you don't know about TinyURL, just check it. Basically we need a one to one mapping to get shorten URL which can retrieve original URL later. This will involve saving such data into database.

We should check the following things:

1. What's the traffic volume / length of the shortened URL?

2. What's the mapping function?

3. Single machine or multiple machines?

### Traffic
Let's assume we want to serve more than 1000 billion URLs. If we can use 62 characters [A-Z, a-z, 0-9] for the short URLs having length n, then we can have total 62^n URLs. So, we should keep our URLs as short as possible given that it should fulfill the requirement. For our requirement, we should use n=7 i.e the length of short URLs will be 7 and we can serve 62^7 ~= 3500 billion URLs.

### Basic solution
To make things easier, we can assume the alias is something like http://tinyurl.com/<alias_hash> and alias_hash is a fixed length string.
To begin with, let’s store all the mappings in a single database. A straightforward approach is using alias_hash as the ID of each mapping, which can be generated as a random string of length 7.

Therefore, we can first just store <ID, URL>. When a user inputs a long URL “http://www.google.com”, the system creates a random 7-character string like “abcd123” as ID and inserts entry <“abcd123”, “http://www.google.com”> into the database.

In the run time, when someone visits http://tinyurl.com/abcd123, we look up by ID “abcd123” and redirect to the corresponding URL “http://www.google.com”.

### Problem with this solution
We can't generate unique hash values for the given long URL. In hashing, there may be collisions (2 long urls map to same short url) and we need a unique short url for every long url so that we can access long url back but hash is one way function.

### Better Solution:

One of the most simple but also effective one, is to have a database table set up this way:
```
Table Tiny_Url(
ID : int PRIMARY_KEY AUTO_INC,
Original_url : varchar,
Short_url : varchar
)
```
Then the auto-incremental primary key ID is used to do the conversion: (ID, 10) <==> (short_url, BASE). Whenever you insert a new original_url, the query can return the new inserted ID, and use it to derive the short_url, save this short_url and send it to cilent.

Code for methods (that are used to convert ID to short_url and short_url to ID):
```java
string idToShortURL(long int n)
{
    // Map to store 62 possible characters
    char map[] = "abcdefghijklmnopqrstuvwxyzABCDEF"
                 "GHIJKLMNOPQRSTUVWXYZ0123456789";

    string shorturl;

    // Convert given integer id to a base 62 number
    while (n)
    {
        shorturl.push_back(map[n%62]);
        n = n/62;
    }

    // Reverse shortURL to complete base conversion
    reverse(shorturl.begin(), shorturl.end());

    return shorturl;
}

// Function to get integer ID back from a short url
long int shortURLtoID(string shortURL)
{
    long int id = 0; // initialize result

    // A simple base conversion logic
    for (int i=0; i < shortURL.length(); i++)
    {
        if ('a' <= shortURL[i] && shortURL[i] <= 'z')
          id = id*62 + shortURL[i] - 'a';
        if ('A' <= shortURL[i] && shortURL[i] <= 'Z')
          id = id*62 + shortURL[i] - 'A' + 26;
        if ('0' <= shortURL[i] && shortURL[i] <= '9')
          id = id*62 + shortURL[i] - '0' + 52;
    }
    return id;
}
```
### Multiple machines:

If we are dealing with massive data of our service, distributed storage can increase our capacity. The idea is simple, get a hash code from original URL and go to corresponding machine then use the same process as a single machine. For routing to the correct node in cluster, Consistent Hashing is commonly used.

Following is the pseudo code for example,

Get shortened URL

hash original URL string to 2 digits as hashed value hash_val

use hash_val to locate machine on the ring

insert original URL into the database and use getShortURL function to get shortened URL short_url

Combine hash_val and short_url as our final_short_url (length=8) and return to the user

Retrieve original from short URL

get first two chars in final_short_url as hash_val

use hash_val to locate the machine

find the row in the table by rest of 6 chars in final_short_url as short_url

return original_url to the user

### Other factors

One thing I’d like to further discuss here is that by using GUID (Globally Unique Identifier) as the entry ID, what would be pros/cons versus incremental ID in this problem?

If you dig into the insert/query process, you will notice that using random string as IDs may sacrifice performance a little bit. More specifically, when you already have millions of records, insertion can be costly. Since IDs are not sequential, so every time a new record is inserted, the database needs to go look at the correct page for this ID. However, when using incremental IDs, insertion can be much easier – just go to the last page.

You can connect with me here: https://www.linkedin.com/in/shashi-bhushan-kumar-709a05b5/
References: http://blog.gainlo.co/index.php/2016/03/08/system-design-interview-question-create-tinyurl-system/
https://www.geeksforgeeks.org/how-to-design-a-tiny-url-or-url-shortener/


## Count large file (MapReduce)
### Problem
Given a text file of size say 100GB? Task is to find first non repeating word in this file?
constraints: You can traverse the file only once.

### Solution
Lets assume 100GBs cannot fit into a single server.

So there will be a need for multiple servers. Also, there is a scenario where you can have all 100GBs to be unique words, so a single server cannot store all those words to find the best answer. Let's avoid the 100GB long word scenario for now.

This is a good solution for map reduce. Say I have 100 servers, I break up my 100GB file into 100 lines evenly by first counting how many words are in the document, then dividing that by the amount of servers available to determine how many words are in each line.

However, reading 100GB just to count the words might be slow. We can instead send every 100 words as a line in a round robin fashion to each server evenly.

Each server will be given one line and a line number.

They will use a hash table, key is the word, value is the count, you will also need the line # and position.

You can also use a trie, up to you. After processing each line, we need to tally up the same words into one server.

 For the sake of this solution, say we have an additional 26 servers. Each of these 26 servers is responsible for a letter in the alphabet, A-Z. If the word starts with that alphabet the count will be sent to that server.

 This is good because each of the first 100 servers can keep a small lookup table of the range of words that match a specific machine ID.
Something like:
```
machine_id = get_machine_id_from(word)
server = connect_to_server_with(machine_id)
server.send(word, count, line_num, pos_in_line)
```
After the map and shuffle phase, we now enter the reduce phase.

The reduce phase can have multiple steps. Since each of these 26 servers contain the counts of words bucketed by their alphabet. We can now go through all the words in the each server and just look at the words that only have a count of 1.

Each of the 26 servers will keep their own "local best" non-repeating word using the line number and the position in the line to determine the result. After that, all 26 servers will send their "local result" to one last server to do the last comparison.

Two reduce phases required.

## Customers who bought this item also bought
### Problem
Design amazon's "Customers who bought this item also bought" recommendation system.
### Solution
#### FUNCTIONALITY
Qn: When are the items shown? Assumption: When the page of an item is viewed.
Qn: How many items are shown? Assumption: Maximum of 5.
Qn: Did the other customer buy the items at the same time? Assumption: Not necessarily, but if it was many years between purchases of A and B then we would not recommend B with A.
Qn: What is the scale of the system? Assumption: Start with a small solution and extent to the scale of Amazon.

#### INITIAL HIGH-LEVEL DESIGN
Relational database table ALSO_BOUGHT with columns item_A_id, item_B_id, count.
Primary key is (item_A_id, item_B_id).
count is the number of times these items have been bought by the same customer (within 1 year of the purchases).
Enforce ordering so that item_A_id < item_B_id, so we don't duplicate everything.
Do not store multiple purchases where item_A_id == item_B_id since they are not good recommendations.

#### WORKFLOW
When a customer makes a new purchase of A then retrieve all items purchased in the last year (B, C, D, E ...)
Increment the count for each pair (A, B), (A, C), ...
When a customer views item X, select all rows in the table where item_A_id == X or item_B_id == X.
Sort by count and return the top 5.

#### OPTIMIZATIONS AND SCALING
Updating ALSO_BOUGHT can be done asynchronously. It is not necessary to include all purchases to the latest millisecond.
Use a cache to store purchases temporarily, and periodically update the database.
Or write purchases to a log, which are processed in parallel similar to MapReduce.
Or write to a queue, which are consumed by workers (a dedicated microservice).
If data is lost due to hardware failure, we can recreate it by querying the main purchases database.

Finding the recommentaions to show with an item must have low latency.
The scheme above will be slow, if Amazon has 10^8 products and each product has been bought with 1000 others the table has ~10^11 rows.
Calculate the top 5 recommendations for each item asynchronously and store in a RECOMMENDATIONS table.
For the most popular items, store their top 5 recommendations in a cache.

Split ALSO_BOUGHT by department (electronics, clothes, toys). Reduces the size and "long tail" but miss some cross-department sales.
Improve recommendations from the above count-based method with machine learning accounting for individual characteristics.
