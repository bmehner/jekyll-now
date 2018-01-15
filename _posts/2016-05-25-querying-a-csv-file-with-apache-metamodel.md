---
layout: post
title: Querying A CSV File With Apache Meta Model
---

Recently I had the requirement to search a CSV file like it was a single 
database table. Up to now I had used CSV for data import and stuff where the 
data in the file is read or written line by line. I never had to search for a 
specific entry in these kind of files.

After some research online I stumbled over 
[Apache Meta Model](http://metamodel.apache.org/). It claims to 
> Providing a common interface for discovery, exploration of metadata and querying of different types of data sources. With MetaModel you get a uniform connector and query API for:
> * Relational databases
> * PostgreSQL
> * MySQL
> * Oracle DB
> * SQL Server
> * Apache Hive
> * ...
> * **CSV files**
> * ...
>
> MetaModel isn't a data mapping framework. Instead we emphasize abstraction of metadata and ability to add data sources at runtime, making MetaModel great for generic data processing applications, less so for applications modeled around a particular domain. 

## Requirements

I have a CSV file generated from OSM Data. It contains all the post codes, the cities, the longitudes and latitudes in Germany in this case. The format is like this:

````
city;countryCode;lat;lon;postcode
;DE;51.06019;13.71117;01067
Dresden;DE;51.06019;13.71117;01067
;DE;51.03964;13.7303;01069
Dresden;DE;51.03964;13.7303;01069
;DE;51.06941;13.7377;01097
Dresden;DE;51.06941;13.7377;01097
;DE;51.09272;13.82842;01099
Dresden;DE;51.09272;13.82842;01099
;DE;51.1518;13.79227;01108
Dresden;DE;51.1518;13.79227;01108
Marsdorf;DE;51.16307;13.76819;01108
;DE;51.12232;13.75621;01109
Dresden;DE;51.12232;13.75621;01109
;DE;51.07897;13.72638;01127
Dresden;DE;51.07897;13.72638;01127
;DE;51.09777;13.73114;01129
Dresden;DE;51.09777;13.73114;01129
;DE;51.08002;13.69169;01139
...
````

Following usecases needed to be covered:
* Querying for the coordinates of a certain post code
* SAYT for post codes
* Showing all postcodes for a city name

## Implementation

Apache Meta Model reads the CSV file and provides an abstraction in a way that it treats the file like a database with one table.

First you have to build a CsvDataContext:

````Java
InputStream resourceAsStream = this.getClass().getResourceAsStream("OSM_PLZ.csv");
CsvDataContext csvDataContext = new CsvDataContext(resourceAsStream, new CsvConfiguration(1, "utf-8", ';', '"', '\\'));
Schema defaultSchema = csvDataContext.getDefaultSchema();
Table[] tables = defaultSchema.getTables();
// There is only one table
Table table = tables[0];
Column[] columns = table.getColumns();
````
Then you can start to build queries:

````Java
Query query = new Query();
query.select(columns);
query.from(table);

query.where(columns[0], EQUALS_TO, "Heilbronn");
query.where(columns[4], LIKE, "7407%");
````
Executing the query and reading the results:

````Java
DataSet ds = csvDataContext.executeQuery(query);

List<GeoLocation> result = new ArrayList<>();

while (ds.next()) {
  Row row = ds.getRow();
  final String valueOfCity = StringUtils.trimToNull((String) row.getValue(0));
  final String valueOfCountryCode = StringUtils.trimToNull((String) row.getValue(1));
  final Double latitude = Double.valueOf(StringUtils.trimToNull((String) row.getValue(2)));
  final Double longitude = Double.valueOf(StringUtils.trimToNull((String) row.getValue(3)));
  final String postCode = StringUtils.trimToNull((String) row.getValue(4));
}
````
The columns are all Strings in CSV context regardless of the actual content. So I need to convert the value from String to Double. Would be a nice touch if a read to Double was possible. But it is not that bad.

This allows for a flexible querying of the data in the CSV-table without to build own indices for each attribute we want to query.
