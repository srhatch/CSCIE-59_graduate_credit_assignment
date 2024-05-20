[Video presentation](https://youtu.be/awp3cwPBlv4)


INTRODUCTION AND SETUP:
The dataset I’m working with captures nutritional analysis of fruit food items. I originally had planned to use a dataset consisting of several thousand food items across major food groups, but after initial attempts to work with that dataset, I realized it was too much to realistically work with given the limits of the machine I’m using as well as time constraints. Due to the large amount of data generated by the structure of the database itself I decided to narrow the dataset down to examine only fruits. This made the data easier to work with and still retained the purpose and structure of the database allowing the same queries and interactions as with the full dataset. The structure of the database contains separate tables for food items and nutrient types. An associative table captures the many-to-many relationship between these two entities. For each food item there are 46 possible nutrient types and with thousands of food items this associative table quickly becomes heavily populated. 

I retained the structure of this database using MySQL on an Amazon EC2 instance. The main reason I chose to use a relational database has to do with scaling. This database is unlikely to need to be scaled quickly. New foods or nutrient types are not discovered at high rates and so the database will not need to be able to accommodate the level of growth that would justify the use of a NoSQL database. The relational nature of this database also allows for individual nutrients to be entered for a specified fruit into the associative table without disturbing the other nutrient values for that fruit.

As a result of only using fruit, I no longer needed a food category table. To fully preserve the original structure, I created a fruit category table and categorized each fruit item into one of these categories. Botanically, fruits are categorized using three broad labels: simple, aggregate, and multiple. Briefly, these categories have to do with how the edible fruit (the ovary) grows on the plant. I created this table to mimic the types of queries that might have been performed using the original food category table (e.g. filtering foods by category to perform more detailed queries).

The data was available for download in csv format, so I cleaned it up in excel before feeding it into my tables on MySQL workbench. I had to enter the tables in a specific order so I could enter in the foreign key IDs properly, that order being: serving_measurements, fruit_category, fruit_list, nutrient_types, and nutrient_values.

For example, this is a screenshot of the csv file (in excel) that was loaded into the fruit_list table:

![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/ac7ec7c0-56ef-464d-a80e-9b5d09d4dd4c)


The dataset I ultimately used contains 166 fruit items, 38 nutrient types, 270 weight descriptions, and 3 fruit categories. I reduced the number of nutrient types by eliminating some that were repetitive, like using different units of measurement (for example vitamin D was measured in both international units and micrograms) The associative table contains 6270 nutrient values. Nutrient values are given per 100g of the fruit item. I used the data import wizard in MySQL workbench to load the csv files into pre-existing tables. 

The following commands are used to create the tables:

CREATE TABLE `fruit_list` (
  `fruit_id` INT NOT NULL AUTO_INCREMENT,
  `description` VARCHAR(255) DEFAULT NULL,
  `gram_weight_1` INT DEFAULT NULL,
  `gram_weight_2` INT DEFAULT NULL,
  `refuse_percentage` INT DEFAULT NULL,
  `category_id` INT DEFAULT NULL,
  PRIMARY KEY (`fruit_id`),
  KEY `gram_weight_1_fk ` (`gram_weight_1`),
  KEY `gram_weight_2_fk` (`gram_weight_2`),
  KEY `category_id_fk` (`category_id`),
  CONSTRAINT `category_id_fk` FOREIGN KEY (`category_id`) REFERENCES `fruit_category` (`category_id`) ON DELETE CASCADE ON UPDATE CASCADE,
  CONSTRAINT `gram_weight_1_fk` FOREIGN KEY (`gram_weight_1`) REFERENCES `serving_measurements` (`serving_size_id`) ON DELETE CASCADE ON UPDATE CASCADE,
  CONSTRAINT `gram_weight_2_fk` FOREIGN KEY (`gram_weight_2`) REFERENCES `serving_measurements` (`serving_size_id`) ON DELETE CASCADE ON UPDATE CASCADE
);

CREATE TABLE `fruit_category` (
  `category_id` INT NOT NULL AUTO_INCREMENT,
  `description` VARCHAR(45) DEFAULT NULL,
  PRIMARY KEY (`category_id`)
);

CREATE TABLE `nutrient_types` (
  `nutrient_id` INT NOT NULL AUTO_INCREMENT,
  `name` VARCHAR(45) DEFAULT NULL,
  `measurement` VARCHAR(45) DEFAULT NULL,
  PRIMARY KEY (`nutrient_id`)
);

CREATE TABLE `nutrient_values` (
  `nutrient_values_id` INT NOT NULL AUTO_INCREMENT,
  `fruit_id` INT DEFAULT NULL,
  `nutrient_id` INT DEFAULT NULL,
  `amount` FLOAT DEFAULT NULL,
  PRIMARY KEY (`nutrient_values_id`),
  KEY `fruit_list_1_fk` (`fruit_id`),
  KEY `nutrient_type_1_fk` (`nutrient_id`),
  CONSTRAINT `fruit_list_1_fk` FOREIGN KEY (`fruit_id`) REFERENCES `fruit_list` (`fruit_id`) ON DELETE CASCADE ON UPDATE CASCADE,
  CONSTRAINT `nutrient_type_1_fk` FOREIGN KEY (`nutrient_id`) REFERENCES `nutrient_types` (`nutrient_id`) ON DELETE CASCADE ON UPDATE CASCADE
);

CREATE TABLE `serving_measurements` (
  `serving_size_id` INT NOT NULL AUTO_INCREMENT,
  `gram_weight` FLOAT DEFAULT NULL,
  `measurement_desc` VARCHAR(255) DEFAULT NULL,
  PRIMARY KEY (`serving_size_id`)
);


The data import process is demonstrated below. This shows importing of the fruit list into the fruit_list table:

![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/162d8f5c-a4a4-4768-8c31-a4b6ef005fb4)


Select the needed pre-existing table.

![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/7f355d37-8a90-4328-9710-1e8193b6d442)


Field separator is changed to a comma.

![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/e7a562dc-ccb6-43c7-9c35-7986fa4d4c8f)


The values in the csv columns are lined up to the columns in the table:

![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/e50c4323-aa27-4040-8e38-307b9c319cc1)


Clicking Next imports the data:

![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/6d1ebf6a-32d2-4bca-9380-9184bf1c0712)


Here are some statements to show that the data was entered properly:


SELECT * FROM fruit_composition.fruit_list;

![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/9d15077c-28b7-440a-b622-500b076297f9)


SELECT COUNT(*) FROM fruit_composition.fruit_list;

![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/6c894655-530f-4ca6-8581-1878f0ae27c4)


SELECT * FROM fruit_composition.fruit_category;

![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/9d5f814d-0e27-40cf-9f42-09c5dc1db77a)


SELECT * FROM fruit_composition.nutrient_types;

![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/2dd480a5-e355-4448-ae9a-fa02ca5a855c)


SELECT * FROM fruit_composition.nutrient_values;

![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/0d959292-2d95-4d3d-a6b7-d9beaa4e5469)


SELECT COUNT(*) FROM fruit_composition.nutrient_values;

![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/7f96a1cc-93f2-4c53-b0c3-6cb61d4cda61)


SELECT * FROM fruit_composition.serving_measurements;

![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/52b47dd8-b5ad-4c81-a256-4e1572d4d47f)



ERD:
The nutrient_values table captures the many-to-many relationship between fruit_list and nutrient_types, and stores the actual data about the nutrient composition of fruit items. For each fruit item there are two gram measurements given as a reference for what a typical serving size might look like.

![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/5eae332f-c2ec-416e-b58e-38c81f61f036)



QUERIES:

This basic query retrieves the specified nutrient value (in this case potassium) for a specified fruit item (raw nectarines). The fruit search is done using LIKE, which is an important tool for this dataset given the string descriptions of fruit names and the possible multiple entries for one fruit item. 

USE fruit_composition;
SELECT apricot.description, nutrient.name, nutrient_value.amount, nutrient.measurement
FROM (SELECT * FROM fruit_list WHERE description LIKE nectarine%raw') AS apricot
INNER JOIN (SELECT * FROM nutrient_values) AS nutrient_value
USING (fruit_id)
INNER JOIN (SELECT * FROM nutrient_types WHERE name='potassium') AS nutrient
USING (nutrient_id);

![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/80ed45df-0bab-40a4-8e01-9027aa449dbf)

 
This kind of query could be useful for studies that are looking for effect sizes. The query filters nutrient values to display values above 1000 in this case, so the results give a list of fruits with values of beta-carotene above 1000mcg.

USE fruit_composition;
SELECT fruits.description, beta_carotene.name, nutrient_value.amount, beta_carotene.measurement
FROM (SELECT * FROM nutrient_types WHERE name='beta_carotene') AS beta_carotene
INNER JOIN (SELECT * FROM nutrient_values WHERE amount > 1000) AS nutrient_value
USING (nutrient_id)
INNER JOIN (SELECT * FROM fruit_list) AS fruits
USING (fruit_id)
ORDER BY amount DESC;

![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/d84bbb1f-84f7-4e0f-8b54-386c6a43c88e)
 

This query displays two different nutrient types, and their values, in the same fruit (in this case looking at potassium and calcium in raw limes)

USE fruit_composition;
SELECT limes.description, nutrient.name, nutrient_value.amount, nutrient.measurement
FROM (SELECT * FROM fruit_list WHERE description LIKE 'limes%raw') AS limes
INNER JOIN (SELECT * FROM nutrient_values) AS nutrient_value
USING (fruit_id)
INNER JOIN (SELECT * FROM nutrient_types WHERE name='potassium') AS nutrient
USING (nutrient_id)
UNION
SELECT limes.description, nutrient.name, nutrient_value.amount, nutrient.measurement
FROM (SELECT * FROM fruit_list WHERE description LIKE 'limes%raw') AS limes
INNER JOIN (SELECT * FROM nutrient_values) AS nutrient_value
USING (fruit_id)
INNER JOIN (SELECT * FROM nutrient_types WHERE name='calcium') AS nutrient
USING (nutrient_id);

![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/f1d91c83-85c4-4d43-8d31-7497c6909e24)


An important step in the scientific process is establishing an area of research. This query displays nutrients that have a NULL value for Japanese persimmons, showing a potential knowledge gap.

USE fruit_composition;
SELECT fruit.description, nutrient.name, nutrient.measurement
FROM (SELECT * FROM fruit_list WHERE description LIKE 'persimmons%japanese%') AS fruit
INNER JOIN (SELECT * FROM nutrient_values WHERE amount IS NULL) AS nutrient_value
USING (fruit_id)
INNER JOIN (SELECT * FROM nutrient_types) as nutrient
USING (nutrient_id);

![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/9263081c-79f5-4c68-bfd7-18baeacd2e3c)


This query compares a nutrient type for two different fruits.

USE fruit_composition;
SELECT fruit.description, nutrient.name, nutrient_value.amount, nutrient.measurement
FROM (SELECT * FROM fruit_list WHERE fruit_id=103) AS fruit
INNER JOIN (SELECT * FROM nutrient_values) AS nutrient_value
USING (fruit_id)
INNER JOIN (SELECT * FROM nutrient_types WHERE nutrient_id=18) AS nutrient
USING (nutrient_id)
UNION
SELECT fruit.description, nutrient.name, nutrient_value.amount, nutrient.measurement
FROM (SELECT * FROM fruit_list WHERE fruit_id=108) AS fruit
INNER JOIN (SELECT * FROM nutrient_values) AS nutrient_value
USING (fruit_id)
INNER JOIN (SELECT * FROM nutrient_types WHERE nutrient_id=18) AS nutrient
USING (nutrient_id)
ORDER BY amount DESC;

![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/8548c727-0820-4839-ac68-9364cd45c1e4)


This query uses the fruit_category table to filter fruit items allowing examination of a nutrient type (in this case pantothenic acid) for that category of fruit (aggregate fruits)

USE fruit_composition;
SELECT category.description, fruit.description, nutrients.name, nutrient_values.amount, nutrients.measurement, fruit.gram_weight_1, fruit.gram_weight_2
FROM (SELECT * FROM fruit_category WHERE description="Aggregate") AS category
INNER JOIN (SELECT * FROM fruit_list) AS fruit
USING (category_id)
INNER JOIN (SELECT * FROM nutrient_values) AS nutrient_values
USING (fruit_id)
INNER JOIN (SELECT * FROM nutrient_types WHERE name="pantothenic_acid") as nutrients
USING (nutrient_id);

![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/d9b6e344-ca26-4b2d-99fe-d5b89f7d49c1)


STORED PROCEDURES:

This creates a new fruit and a simultaneous new nutrient value:

DELIMITER //
CREATE PROCEDURE `add_fruit`(
	IN description VARCHAR(255),
    IN gram_weight_1 INT,
    IN gram_weight_2 INT,
    IN refuse_percentage INT,
    IN nutrient_id INT,
    IN category INT,
    IN nutrient_amount FLOAT
)
BEGIN
	DECLARE fruit_id INT;
	INSERT INTO fruit_list (description, gram_weight_1, gram_weight_2, refuse_percentage, cateogry_id) VALUES (description, gram_weight_1, gram_weight_2, refuse_percentage, category);
    SELECT LAST_INSERT_ID() INTO fruit_id;
    INSERT INTO nutrient_values (fruit_id, nutrient_id, amount) VALUES (fruit_id, nutrient_id, nutrient_amount);
    SELECT fruit_id;
END //
DELIMITER ;


This creates a new fruit item:

DELIMITER //
CREATE PROCEDURE `new_fruit_item`(
	IN description VARCHAR(255),
    IN gram_weight_1 INT,
    IN gram_weight_2 INT,
    IN refuse_percentage INT,
    IN category INT
)
BEGIN
	INSERT INTO fruit_list (description, gram_weight_1, gram_weight_2, refuse_percentage, cateogry_id) VALUES (description, gram_weight_1, gram_weight_2, refuse_percentage, category);
    SELECT LAST_INSERT_ID();
END //
DELIMITER ;


This creates a new nutrient item:

DELIMITER //
CREATE PROCEDURE `new_nutrient`(
	IN name VARCHAR(45),
    IN measurement VARCHAR(45)
)
BEGIN
	INSERT INTO nutrient_types (name, measurement) VALUES (name, measurement);
    SELECT LAST_INSERT_ID();
END //
DELIMITER ;


This enters a new record into the nutrient_values table:

DELIMITER //
CREATE PROCEDURE `new_nutrient_value`(
	IN fruit_id INT,
    IN nutrient_id INT,
    IN new_amount FLOAT
)
BEGIN
	UPDATE nutrient_values SET amount = new_amount WHERE fruit_id = fruit_id AND nutrient_id = nutrient_id;
END //
DELIMITER ;

Demonstrating the above queries by adding in carambola, or starfruit:

![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/447ab452-6c9b-4a0f-a51b-ab72eddb0d78)

 
This is the return value, which is the ID of the newly created fruit item:

![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/7d78d364-aae0-4f78-9bf5-b2a438a8d266)


A query to show the newly entered nutrient value:
USE fruit_composition;
SELECT starfruit.description, nutrient.name, nutrient_value.amount, nutrient.measurement
FROM (SELECT * FROM fruit_list WHERE description LIKE 'carambola%') AS starfruit
INNER JOIN (SELECT * FROM nutrient_values) AS nutrient_value
USING (fruit_id)
INNER JOIN (SELECT * FROM nutrient_types WHERE nutrient_id=15) AS nutrient
USING (nutrient_id);

![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/d3a971e4-e9a7-43d8-b169-ad2e56b78c1e)



SCALING:
This database would likely need to scale to accommodate increasing read operations. New fruits are not discovered and studied at high rates. The same goes for individual nutrients. 
The nature of how the data is generated is a slow process so there’s little risk of single row contention. 
	Optimizing the database structure by indexing would 
Expanding the scope of the database to include foods from all food groups, like the full original database I got the fruit data from, would quickly lead to the the nutrient_values table containing possibly hundreds of thousands of records. Indexing on this table would be important, particularly on the fruit_id and nutrient_id columns.
Adding in a nutrient type table (e.g. vitamins, minerals) could also be useful to further filter queries and could avoid searching through the whole nutrient_type table.

The commands for adding indexes to the nutrient_values table are:

USE fruit_composition;
ALTER TABLE nutrient_values ADD INDEX (nutrient_id);

USE fruit_composition;
ALTER TABLE nutrient_values ADD INDEX (fruit_id);


Below is a walk-through of how to set up an RDS (relational database service) instance so that read-replicas can be created on that instance. I populated the read-replicas using mysqldump just to demonstrate that they were operational, though in practice it would be better to set up a database migration service to automatically update the RDS instance with any changes to the MySQL server on the EC2 instance.

In the RDS dashboard click on Databases and click Create database.

![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/d6b11ca4-12b8-4581-9b72-e76a81cdff5f)

![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/dc9cb517-47e2-4e3d-80db-4272c0c5cd3b)

![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/7eda278e-e916-4d19-a5fd-ff60db1324d9)

![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/58943489-fb6a-4fdc-bc46-790334bb0966)

![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/9b0418c2-7d35-470e-83d7-3972796f5808)


Importantly, connect the new database to the EC2 instance the MySQL server is running on.

![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/d1cc6b67-e623-4494-a2aa-cb06dee1fa10)


If all goes well.

![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/fc22e07c-9398-4003-a150-10c2b5909e0e)


Now a replica can be created.
 
![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/1d228cb0-820f-47ff-bde0-66de5c9204f3)

![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/464af298-ef3d-4be8-9ccb-d86996b3e3e9)


Now this database has a read replica.

![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/f5248dc8-4bee-4d40-b004-e46dc234b092)


Both this database and its replica can be accessed through the EC2 instance with this command (replacing the name after -h with the name of the desired instance):

![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/80c363ad-467b-4d2e-8ece-14e1ff14ac95)


Since the database I’m using isn’t enormous, I used mysqldump to transfer the data to the RDS database.

I created a database called fruit_composition in the RDS database, then used mysqldump to transfer the data into that database.

![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/fe2202d4-c591-4b80-80fc-1c4c5ef6a3c0)

![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/2c587bea-74a8-4b8e-9110-00735ee31b15)


Connect to the replica of the RDS database to see if the data came through:

![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/fcf58fd1-60d4-4a98-8a0e-1107cccba982)


Test queries to show that the replica database is populated:
 
![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/137d31eb-34ec-4245-9b5d-6e99b69d05ad)

![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/7002291e-ca48-4c2a-8026-86a680d1e1f9)

![alt text](https://github.com/s-hatch/CSCIE-59_graduate_credit_assignment/assets/113044909/8e16a41e-3b73-4b64-9938-b2553bf6b135)

 


[Data source](https://data.nal.usda.gov/dataset/composition-foods-raw-processed-prepared-usda-national-nutrient-database-standard-reference-release-27)
