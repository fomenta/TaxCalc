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

The purpose of each entity is shown on the table below.

+---------------------+--------------------------------------------+
| Entity              | Description                                |
+=====================+============================================+
| TaxFactory          | Main class. Calculates tax information     |
+---------------------+--------------------------------------------+
| TaxRequest          | Holds the order information needed to      |
|                     | calculate taxes.                           |
+---------------------+--------------------------------------------+
| TaxRequestAddress   | Full address information (e.g. from        |
|                     | a vendor or a buyer)                       |
+---------------------+--------------------------------------------+
| TaxRequestOrderItem | Information about an order item            |
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
| ProductTaxType      | Product tax classification (Lookup table). |
|                     | Example: Food, Drugs, Alcohol              |
+---------------------+--------------------------------------------+
| Country             | List of countries (Lookup table)           |
+---------------------+--------------------------------------------+


PHP Sample Request
--------------------
::

	TaxFactory.calculate({
	  OrderNumber: '123',
	  OrderAmount: 345.8,
	  OrderDate: '2016-11-11',
	  VendorAddress: {
		  Line1: '2340 5th Avenue',
		  City: 'New York',
		  State: 'NY',
		  PostalCode: '10118',
		  Country: 'US'
		  },
	  BuyerAddress: {
		  line1: '300S Orange Ave',
		  city: 'Orlando',
		  state: 'NY',
		  postalCode: '32800',
		  country: 'US'
		  },
	  OrderItems: [
		{
		  Id: 1,
		  Quantity: 1,
		  UnitPrice: 45.2,
		  ProductTaxType: 'Food'
		},
		{
		  Id: 2,
		  Quantity: 1,
		  UnitPrice: 300.6,
		  ProductTaxType: 'Drugs'
		}
	  ]
	});

PHP Sample Response (Simple Calculatation)
------------------------------------------
::

	{
	  OrderNumber: '123',
	  OrderAmount: 345.8,
	  TaxFixedRate: 0.075,
	  TaxAmount: 25.935
	}

PHP Sample Response (Complex Calculatation)
------------------------------------------
::

	{
	  OrderNumber: '123',
	  OrderAmount: 345.8,
	  TaxFixedRate: 0,
	  TaxAmount: 26.308,
	  OrderItems: [
		{
		  Id: 1,
		  ProductTaxType: 'Food'
		  TaxRate: 0.05,
		  TaxAmount: 2.26
		},
		{
		  Id: 2,
		  ProductTaxType: 'Drugs'
		  TaxRate: 0.08,
		  TaxAmount: 24.048
		}
	  ]
	}

