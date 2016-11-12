Multi-Country Tax Calculator
============================

Statement
---------

* A company processes orders that are placed in multiple countries.
	* Calculate country taxes.
* Some may use:
	* A simple calculation
	* A fixed percentage
* others:
	* Use a sophisticated formula 
	* Requires additional information
		* Product classification
		* Vendor location
		* Buyer location 
		* Etc.

Problem
-------

Propose a model (class model) that:

* Can be used to calculate taxes for multiple countries 
* Can be escalated easily. 

**NOTICE**: made up objects may be included.

Solution
========

Proposed model is shown below.


Class Model
-----------

.. image:: Images/TaxCalculatorDiagram.png


Entities
--------

Below is a short description for the purpose of each entity.

+---------------------+--------------------------------------------+
| Entity              | Description                                |
+=====================+============================================+
| TaxFactory          | Main class. Calculates tax information     |
+---------------------+--------------------------------------------+
| TaxRequest          | Holds the order information needed to      |
|                     | calculate taxes.                           |
+---------------------+--------------------------------------------+
| TaxRequestAddress   | information about full address (e.g. from  |
|                     | a vendor or a buyer)                       |
+---------------------+--------------------------------------------+
| TaxRequestOrderItem | information about an order item            |
+---------------------+--------------------------------------------+
| TaxResponse         | Holds tax calculation details              |
+---------------------+--------------------------------------------+
| TaxResponseItem     | Information about calculations when taxes  |
|                     | go to the product level.                   |
+---------------------+--------------------------------------------+
| TaxRepository       | Main class to access tax data repository   |
+---------------------+--------------------------------------------+
| TaxRate             | May hold tax rate information by:          |
|                     |  * Vendor country                          |
|                     |  * Vendor/buyer country                    |
|                     |  * Vendor/buyer country, product tax type  |
+---------------------+--------------------------------------------+
| ProductTaxType      | Product tax classification (Lookup table)  |
|                     | Example: Food, Drugs, Alcohol              |
+---------------------+--------------------------------------------+
| Country             | List of countries (Lookup table)           |
+---------------------+--------------------------------------------+

