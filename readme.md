# Tyler Fraking
# SQL Practice - store.sqlite3

## EXPLORER MODE

How many users are there?

select count(\*) from users;

**50**

What are the 5 most expensive items?
select title from items order by price desc limit 5;
Small Cotton Gloves
Small Wooden Computer
Awesome Granite Pants
Sleek Wooden Hat
Ergonomic Steel Car

What's the cheapest book? (Does that change for "category is exactly 'book'" versus "category contains 'book'"?)
select title from items where category like "%book%" order by price asc limit 1;
Ergonomic Granite Chair
select title from items where category like "Books" order by price asc limit 1;
Ergonomic Granite Chair
The answer is the same, but it could have been different if the data was different.

Who lives at "6439 Zetta Hills, Willmouth, WY"? Do they have another address?
select first_name, last_name from users inner join addresses on addresses.user_id == users.id where street == "6439 Zetta Hills" and city == "Willmouth" and state == "WY";
Corrine|Little
select count(street) from users inner join addresses on addresses.user_id == users.id where first_name == "Corrine" and last_name == "Little";
2
Yes, Corrine Little has two addresses.

^(I worked through this one for a while seeing if I could solve it with only one sql command, but no dice.)

Correct Virginie Mitchell's address to "New York, NY, 10108".
select addresses.id, city, state, zip from users inner join addresses on addresses.user_id == users.id where first_name == "Virginie" and last_name == "Mitchell";
41|Roxanehaven|NY|34705
42|Bahringerland|WY|24028
update addresses set city = "New York", state = "NY", zip = "10108" where id == 41;
select addresses.id, city, state, zip from users inner join addresses on addresses.user_id == users.id where first_name == "Virginie" and last_name == "Mitchell";
41|New York|NY|10108
42|Bahringerland|WY|24028

How much would it cost to buy one of each tool?
select sum(price) from items where category like "%tool%";
46477

How many total items did we sell?
select sum(quantity) from orders;
2125

How much was spent on books?
select sum(quantity * price) from orders inner join items where item_id == items.id and category like "%book%";
1081352

Simulate buying an item by inserting a User for yourself and an Order for that User.
insert into users (first_name, last_name, email) values ("Tyler", "Fraking", "tfraking@boomtownroi.com");
insert into orders (user_id, item_id, quantity, created_at) values (51, 42, 9001, date('now'));

^(Putting date('now') did not include the time, only the date.)

## ADVENTURER MODE

What item was ordered most often?
select title, quantity from orders inner join items where item_id == items.id order by quantity desc limit 2;
Gorgeous Cotton Pants|9001
Gorgeous Granite Car|10

Grossed the most money?
select title, quantity * price from orders inner join items where item_id == items.id order by (quantity * price) desc limit 2;
Gorgeous Cotton Pants|25373819
Awesome Granite Pants|97900

What user spent the most?
select first_name, last_name, sum(price*quantity) from users inner join orders on users.id == orders.user_id inner join items on items.id == orders.item_id group by users.id order by sum(price*quantity) desc limit 3;
Tyler|Fraking|25373819
Hassan|Runte|639386
Mekhi|Lakin|454343

^(I included a limit higher than 1 because adding myself as a user changed results.)

What were the top 3 highest grossing categories?
select category from items join orders where items.id == orders.item_id group by category order by sum(price)*sum(quantity) desc limit 3;
Computers
Sports
Books
