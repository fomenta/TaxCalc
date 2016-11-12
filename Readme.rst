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

Proposed model can be seen on image below. Also, sample classes are under the **php-model** subfolder on this project.


Class Model
-----------

.. image:: Images/TaxCalculatorDiagram.png


Tax Calculatation
-----------------

The Tax Factory will take care of receiving order information and making the correct tax calculation based on the different variables
such as vendor/buyer location and/or product classification.

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


About Scalability
-----------------

The usual approach on scalability for this type of situations where tax information can be added 
at any time for additional countries is to create "resolvers". This way additional code is added
and loaded dynamically as it seems necessary.

For example:

	Markup :  [A Tax Library](https://github.com/commerceguys/tax)

However, I am going to take a different path. The Scalability is based on a data-driven approach.

A repository (which can be a database or, simply, a set of json/xml files) will hold detailed information on tax calculation 
for each country or a combination of variables.

Classes on the table below represent the repository.

+---------------------+--------------------------------------------+
| Entity              | Description                                |
+=====================+============================================+
| TaxRepository       | Main class to access tax data repository   |
+---------------------+--------------------------------------------+
| TaxRate             | Holds tax rate information by:             |
|                     |  * Vendor country                          |
|                     |  * Vendor/buyer country                    |
|                     |  * Vendor/buyer country, product tax type  |
+---------------------+--------------------------------------------+
| ProductTaxType      | Product tax classification (Lookup table). |
|                     | Example: Food, Drugs, Alcohol              |
+---------------------+--------------------------------------------+
| Country             | List of countries (Lookup table)           |
+---------------------+--------------------------------------------+

### Additional Complexity

Complex calculations can be extended by simply adding rows with additional information 
about tax rates based on vendor location, buyer location, product classification.

Also, additional variables can be added to the formula. For instance, if calculations must be extended 
at the State level (not only the Country) additional columns can be added to include State for 
Vendor and Buyer (as optional fields)

Now the model would hold tax rate information by:
 * Vendor country                         
 * Vendor country, buyer country
 * Vendor country, vendor state, buyer country
 * Vendor country, buyer country, buyer state
 * Vendor country, vendor state, buyer country, buyer state
 * Vendor/buyer country/state, product classification

 
### Even More Complex

Just to prove the point, The model above introduced extra variables to the tax calculation formula. 
I haven't talked about it just yet.


Certain countries support a different rate on special days of the year. For example, a Super Reduced tax rate on January 1st. If the system doesn't provide this functionality, somebody at the support team should be waiting to update tax information at midnight on New Year's Eve.

Looking at the class model above, the TaxRate class contains two extra fields:
 * ValidFromDate
 * ValidToDate

This will allow having a different tax rate for certain dates of the year without having support personnel around for it to happen.
