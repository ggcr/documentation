# Data catalogue API with OData interface

## Query structure

As a general note, OData query consistst of elements which in this documentation are called "options". Interface supports the following search options:

* filter
* orderby
* top
* skip
* count
* expand

Search options should always be preceded with *$* and consecutive options should be separated with *&*.

Consecutive filters within *filter* option should be separated with *and* or *or*. *Not* operator can also be used e.g.:

[https://catalogue.dataspace.copernicus.eu/odata/v1/Products?$filter=not contains(Name,'S2') and ContentDate/Start gt 2022-05-03T00:00:00.000Z and ContentDate/Start lt 2022-05-03T00:10:00.000Z&$orderby=ContentDate/Start&$top=100](https://catalogue.dataspace.copernicus.eu/odata/v1/Products?$filter=not%20contains(Name,%27S2%27)%20and%20ContentDate/Start%20gt%202022-05-03T00:00:00.000Z%20and%20ContentDate/Start%20lt%202022-05-03T00:10:00.000Z&$orderby=ContentDate/Start&$top=100)

<u>Performance note</u>:

To accelerate the query performance it is recommended to limit the query by acquisition dates e.g.:
```
ContentDate/Start gt 2022-05-03T00:00:00.000Z and ContentDate/Start lt 2022-05-21T00:00:00.000Z
```
## Filter option

### Query by name

To search for a specific product by its exact name:

[https://catalogue.dataspace.copernicus.eu/odata/v1/Products?$filter=Name eq 'S1A_IW_GRDH_1SDV_20141031T161924_20141031T161949_003076_003856_634E.SAFE'](https://catalogue.dataspace.copernicus.eu/odata/v1/Products?$filter=Name%20eq%20%27S1A_IW_GRDH_1SDV_20141031T161924_20141031T161949_003076_003856_634E.SAFE')

To search for products containing "S1A" in their names:

[https://catalogue.dataspace.copernicus.eu/odata/v1/Products?$filter=contains(Name,'S1A') and ContentDate/Start gt 2022-05-03T00:00:00.000Z and ContentDate/Start lt 2022-05-21T00:00:00.000Z](https://catalogue.dataspace.copernicus.eu/odata/v1/Products?$filter=contains(Name,%27S1A%27)%20and%20ContentDate/Start%20gt%202022-05-03T00:00:00.000Z%20and%20ContentDate/Start%20lt%202022-05-21T00:00:00.000Z)

Alternatively to *contains*, *endswith* and *startswith* can be used, to search for products ending or starting with provided string.

### Query by list

In case a user desires to search for multiple products by name in one query, POST method can be used:

**POST**

[https://catalogue.dataspace.copernicus.eu/odata/v1/Products/OData.CSC.FilterList](https://catalogue.dataspace.copernicus.eu/odata/v1/Products/OData.CSC.FilterList)

**Request body**:
```Json
{
  "FilterProducts":
    [
     {"Name": "S1A_IW_GRDH_1SDV_20141031T161924_20141031T161949_003076_003856_634E.SAFE"},
     {"Name": "S3B_SL_1_RBT____20190116T050535_20190116T050835_20190117T125958_0179_021_048_0000_LN2_O_NT_003.SEN3"},
     {"Name": "xxxxxxxx.06.tar"}
    ]
 }
```
Two results are returned, as there is no product named xxxxxxxx.06.tar.

### Query Collection of Products

To search for products within a specific collection:

[https://catalogue.dataspace.copernicus.eu/odata/v1/Products?$filter=Collection/Name eq 'SENTINEL-2' and ContentDate/Start gt 2022-05-03T00:00:00.000Z and ContentDate/Start lt 2022-05-03T00:11:00.000Z](https://catalogue.dataspace.copernicus.eu/odata/v1/Products?$filter=Collection/Name%20eq%20%27SENTINEL-2%27%20and%20ContentDate/Start%20gt%202022-05-03T00:00:00.000Z%20and%20ContentDate/Start%20lt%202022-05-03T00:11:00.000Z)

The following collections are currently available:

* SENTINEL-1
* SENTINEL-2
* SENTINEL-3
* SENTINEL-5P

### Query by Publication Date

To search for products published between two dates:

[https://catalogue.dataspace.copernicus.eu/odata/v1/Products?$filter=PublicationDate gt 2019-05-15T00:00:00.000Z and PublicationDate lt 2019-05-16T00:00:00.000Z](https://catalogue.dataspace.copernicus.eu/odata/v1/Products?$filter=PublicationDate%20gt%202019-05-15T00:00:00.000Z%20and%20PublicationDate%20lt%202019-05-16T00:00:00.000Z)

To define inclusive interval *ge* and *le* parameters can be used:

[https://catalogue.dataspace.copernicus.eu/odata/v1/Products?$filter=PublicationDate ge 2019-05-15T00:00:00.000Z and PublicationDate le 2019-05-16T00:00:00.000Z](https://catalogue.dataspace.copernicus.eu/odata/v1/Products?$filter=PublicationDate%20ge%202019-05-15T00:00:00.000Z%20and%20PublicationDate%20le%202019-05-16T00:00:00.000Z)

### Query by Sensing Date

To search for products acquired between two dates:

[https://catalogue.dataspace.copernicus.eu/odata/v1/Products?$filter=ContentDate/Start gt 2019-05-15T00:00:00.000Z and ContentDate/Start lt 2019-05-16T00:00:00.000Z](https://catalogue.dataspace.copernicus.eu/odata/v1/Products?$filter=ContentDate/Start%20gt%202019-05-15T00:00:00.000Z%20and%20ContentDate/Start%20lt%202019-05-16T00:00:00.000Z)

Usually, there are two parameters describing the ContentDate (Acquisition Dates) for a product - Start and End. Depending on what the user is looking for, these parameters can be mixed, e.g.:

[https://catalogue.dataspace.copernicus.eu/odata/v1/Products?$filter=ContentDate/Start gt 2019-05-15T00:00:00.000Z and ContentDate/End lt 2019-05-15T00:05:00.000Z](https://catalogue.dataspace.copernicus.eu/odata/v1/Products?$filter=ContentDate/Start%20gt%202019-05-15T00:00:00.000Z%20and%20ContentDate/End%20lt%202019-05-15T00:05:00.000Z)

### Query by Geographic Criteria

To search for products intersecting the specified polygon:

[https://catalogue.dataspace.copernicus.eu/odata/v1/Products?$filter=OData.CSC.Intersects(area=geography'SRID=4326;POLYGON((12.655118166047592 47.44667197521409,21.39065656328509 48.347694733853245,28.334291357162826 41.877123516783655,17.47086198383573 40.35854475076158,12.655118166047592 47.44667197521409))') and ContentDate/Start gt 2022-05-20T00:00:00.000Z and ContentDate/Start lt 2022-05-21T00:00:00.000Z](https://catalogue.dataspace.copernicus.eu/odata/v1/Products?$filter=OData.CSC.Intersects(area=geography%27SRID=4326;POLYGON((12.655118166047592%2047.44667197521409,21.39065656328509%2048.347694733853245,28.334291357162826%2041.877123516783655,17.47086198383573%2040.35854475076158,12.655118166047592%2047.44667197521409))%27)%20and%20ContentDate/Start%20gt%202022-05-20T00:00:00.000Z%20and%20ContentDate/Start%20lt%202022-05-21T00:00:00.000Z)

To search for products intersecting the specified point:

[https://catalogue.dataspace.copernicus.eu/odata/v1/Products?$filter=OData.CSC.Intersects(area=geography%27SRID=4326;POINT(-0.5319577002158441%2028.65487836189358)%27)](https://catalogue.dataspace.copernicus.eu/odata/v1/Products?$filter=OData.CSC.Intersects(area=geography%27SRID=4326;POINT(-0.5319577002158441%2028.65487836189358)%27))

<u>Disclaimers</u>:

1. MULTIPOLYGON is currently not supported.
2. Polygon must start and end with the same point.
3. Coordinates must be given in EPSG 4326

### Query by attributes

To search for products by attributes it is necessary to build a filter with the following structure:
```
Attributes/OData.CSC.ValueTypeAttribute/any(att:att/Name eq '[Attribute.Name]' and att/OData.CSC.ValueTypeAttribute/Value eq '[Attribute.Value]')
```
where

- *ValueTypeAttribute* can take the following values:
  - *StringAttribute*
  - *DoubleAttribute*
  - *IntegerAttribute*
  - *DateTimeOffsetAttribute*
- *[Attribute.Name]* is the attribute name which can take multiple values, depending on collection (Attachment 1 - Coming soon)
- *eq* before *[Attribute.Value]* can be substituted with le, lt, ge, gt in case of *Integer, Double* or *DateTimeOffset* Attributes
- *[Attribute.Value]* is the specific value that the user is searching for

To get products with CloudCover\<40% between two dates:

[https://catalogue.dataspace.copernicus.eu/odata/v1/Products?$filter=Attributes/OData.CSC.DoubleAttribute/any(att:att/Name eq 'cloudCover' and att/OData.CSC.DoubleAttribute/Value le 40.00) and ContentDate/Start gt 2022-01-01T00:00:00.000Z and ContentDate/Start lt 2022-01-03T00:00:00.000Z&$top=10](https://catalogue.dataspace.copernicus.eu/odata/v1/Products?$filter=Attributes/OData.CSC.DoubleAttribute/any(att:att/Name%20eq%20%27cloudCover%27%20and%20att/OData.CSC.DoubleAttribute/Value%20le%2040.00)%20and%20ContentDate/Start%20gt%202022-01-01T00:00:00.000Z%20and%20ContentDate/Start%20lt%202022-01-03T00:00:00.000Z&$top=10)

To get products with cloudCover\< 10% and productType=S2MSI2A and ASCENDING orbitDirection between two dates:

[https://catalogue.dataspace.copernicus.eu/odata/v1/Products?$filter=Attributes/OData.CSC.DoubleAttribute/any(att:att/Name eq 'cloudCover' and att/OData.CSC.DoubleAttribute/Value lt 10.00) and Attributes/OData.CSC.StringAttribute/any(att:att/Name eq 'productType' and att/OData.CSC.StringAttribute/Value eq 'S2MSI2A') and Attributes/OData.CSC.StringAttribute/any(att:att/Name eq 'orbitDirection' and att/OData.CSC.StringAttribute/Value eq 'ASCENDING') and ContentDate/Start gt 2022-05-03T00:00:00.000Z and ContentDate/Start lt 2022-05-03T04:00:00.000Z&$top=10](https://catalogue.dataspace.copernicus.eu/odata/v1/Products?$filter=Attributes/OData.CSC.DoubleAttribute/any(att:att/Name%20eq%20%27cloudCover%27%20and%20att/OData.CSC.DoubleAttribute/Value%20lt%2010.00)%20and%20Attributes/OData.CSC.StringAttribute/any(att:att/Name%20eq%20%27productType%27%20and%20att/OData.CSC.StringAttribute/Value%20eq%20%27S2MSI2A%27)%20and%20Attributes/OData.CSC.StringAttribute/any(att:att/Name%20eq%20%27orbitDirection%27%20and%20att/OData.CSC.StringAttribute/Value%20eq%20%27ASCENDING%27)%20and%20ContentDate/Start%20gt%202022-05-03T00:00:00.000Z%20and%20ContentDate/Start%20lt%202022-05-03T04:00:00.000Z&$top=10)


### Orderby option

Orderby option can be used to order the products in an ascending (asc) or descending (desc) direction. If asc or desc not specified, then the resources will be ordered in ascending order.

To order products by ContentDate/Start in a descending direction:

[https://catalogue.dataspace.copernicus.eu/odata/v1/Products?$filter=contains(Name,'S1A_EW_GRD') and ContentDate/Start gt 2022-05-03T00:00:00.000Z and ContentDate/Start lt 2022-05-03T03:00:00.000Z&$orderby=ContentDate/Start desc](https://catalogue.dataspace.copernicus.eu/odata/v1/Products?$filter=contains(Name,%27S1A_EW_GRD%27)%20and%20ContentDate/Start%20gt%202022-05-03T00:00:00.000Z%20and%20ContentDate/Start%20lt%202022-05-03T03:00:00.000Z&$orderby=ContentDate/Start%20desc)

By default, if orderby option is not used, the results are not ordered. If orderby option is used, additional orderby by id is also used, so that the results are fully ordered and no products are lost while paginating through the results.

The acceptable arguments for this option: *ContentDate/Start*, *ContentDate/End, PublicationDate, ModificationDate*, in directions: *asc, desc*.

### Top option

Top option specifies the maximum number of items returned from a query.

To limit the number of results:

[https://catalogue.dataspace.copernicus.eu/odata/v1/Products?$filter=contains(Name,%27S1A_EW_GRD%27)%20and%20ContentDate/Start%20gt%202022-05-03T00:00:00.000Z%20and%20ContentDate/Start%20lt%202022-05-03T12:00:00.000Z&$top=100](https://catalogue.dataspace.copernicus.eu/odata/v1/Products?$filter=contains(Name,%27S1A_EW_GRD%27)%20and%20ContentDate/Start%20gt%202022-05-03T00:00:00.000Z%20and%20ContentDate/Start%20lt%202022-05-03T12:00:00.000Z&$top=100)

 The default value is set to 20.

The acceptable arguments for this option: _Integer \<0,1000\>_

## Skip option

Skip option can be used to skip a specific number of results. Exemplary application of this option would be paginating through the results, however for performance reasons, we recommend limiting queries with small time intervals as a substitute of using skip in a more generic query.

To skip a specific number of results:

[https://catalogue.dataspace.copernicus.eu/odata/v1/Products?$filter=contains(Name,%27S1A_EW_GRD%27)%20and%20ContentDate/Start%20gt%202022-05-03T00:00:00.000Z%20and%20ContentDate/Start%20lt%202022-05-03T12:00:00.000Z&$skip=23](https://catalogue.dataspace.copernicus.eu/odata/v1/Products?$filter=contains(Name,%27S1A_EW_GRD%27)%20and%20ContentDate/Start%20gt%202022-05-03T00:00:00.000Z%20and%20ContentDate/Start%20lt%202022-05-03T12:00:00.000Z&$skip=23)

 The default value is set to 0.

Whenever a query results in more products than 20 (default top value), the API provides a nextLink at the bottom of the page:
```
"@OData.nextLink":
```
[http://catalogue.dataspace.copernicus.eu/odata/v1/Products?$filter=contains(Name,'S1A_EW_GRD')+and+ContentDate/Start+gt+2022-05-03T00:00:00.000Z+and+ContentDate/Start+lt+2022-05-03T12:00:00.000Z&$skip=20](https://catalogue.dataspace.copernicus.eu/odata/v1/Products?$filter=contains(Name,%27S1A_EW_GRD%27)+and+ContentDate/Start+gt+2022-05-03T00:00:00.000Z+and+ContentDate/Start+lt+2022-05-03T12:00:00.000Z&$skip=20)

The acceptable arguments for this option: *Integer \<0,10000\>*

## Count option

Count option enables users to get the exact number of products matching the query. This option is disabled by default to accelerate the query performance.

To get the exact number of products for a given query:

[https://catalogue.dataspace.copernicus.eu/odata/v1/Products?$filter=contains(Name,%27S1A_EW_GRD%27)%20and%20ContentDate/Start%20gt%202022-05-03T00:00:00.000Z%20and%20ContentDate/Start%20lt%202022-05-03T12:00:00.000Z&$count=True](https://catalogue.dataspace.copernicus.eu/odata/v1/Products?$filter=contains(Name,%27S1A_EW_GRD%27)%20and%20ContentDate/Start%20gt%202022-05-03T00:00:00.000Z%20and%20ContentDate/Start%20lt%202022-05-03T12:00:00.000Z&$count=True)

 The acceptable arguments for this option: *True, true, 1, False, false, 0*.

## Expand option

Expand option enables users to see full metadata of each returned result.

To see the metadata of the results:

[https://catalogue.dataspace.copernicus.eu/odata/v1/Products?$filter=contains(Name,%27S1A_EW_GRD%27)%20and%20ContentDate/Start%20gt%202022-05-03T00:00:00.000Z%20and%20ContentDate/Start%20lt%202022-05-03T12:00:00.000Z&$expand=Attributes](https://catalogue.dataspace.copernicus.eu/odata/v1/Products?$filter=contains(Name,%27S1A_EW_GRD%27)%20and%20ContentDate/Start%20gt%202022-05-03T00:00:00.000Z%20and%20ContentDate/Start%20lt%202022-05-03T12:00:00.000Z&$expand=Attributes)

 The acceptable arguments for this option: *Attributes*

## Product Download

For downloading products you need an authorization token as only authorized users are allowed to download data products.


To get the token you can use the following scripts:

```bash
curl --location --request POST 'https://identity.dataspace.copernicus.eu/auth/realms/CDSE/protocol/openid-connect/token' \
  --header 'Content-Type: application/x-www-form-urlencoded' \
  --data-urlencode 'grant_type=password' \
  --data-urlencode 'username=<LOGIN>' \
  --data-urlencode 'password=<PASSWORD>' \
  --data-urlencode 'client_id=cdse-public'
```

or
```bash
'-d 'password=' -d 'grant_type=password' 'https://identity.dataspace.copernicus.eu/auth/realms/CDSE/protocol/openid-connect/token' | python -m json.tool | grep "access_token" | awk -F\" '{print $4}')]]>
```

Along with the Access Token you will be returned a Refresh Token, the latter is used to generate a new Access Token without the need to specify Username or Password, this helps to make requests less vulnerable to your credentials being exposed.

To re-generate the Access Token from the Refresh Token it can be done with the following request:

```bash
curl --location --request POST 'https://identity.dataspace.copernicus.eu/auth/realms/CDSE/protocol/openid-connect/token' \
  --header 'Content-Type: application/x-www-form-urlencoded' \
  --data-urlencode 'grant_type=refresh_token' \
  --data-urlencode 'refresh_token=<REFRESH_TOKEN>' \
  --data-urlencode 'client_id=cdse-public'
```


<!--Where USER and PASSWORD are credentials to Your CloudFerro account in specific BRAND. Brand names are listed below with API from which You can get your token.

| **Brand names** | **API** |
| --- | --- |
| dias | [https://identity.dataspace.copernicus.eu/auth/realms/dias/protocol/openid-connect/token](https://identity.cloudferro.com/auth/realms/dias) |
| Creodias-new | [https://identity.dataspace.copernicus.eu/auth/realms/Creodias-new/protocol/openid-connect/token](https://identity.cloudferro.com/auth/realms/Creodias-new) |
| CODE-DE-EL | [https://identity.dataspace.copernicus.eu/auth/realms/CODE-DE-EL/protocol/openid-connect/token](https://identity.cloudferro.com/auth/realms/CODE-DE-EL) |
| wekeo-elasticity | [https://identity.dataspace.copernicus.eu/auth/realms/wekeo-elasticity/protocol/openid-connect/token](https://identity.cloudferro.com/auth/realms/wekeo-elasticity) |
| Eumetsat-elasticity | [https://identity.dataspace.copernicus.eu/auth/realms/Eumetsat-elasticity/protocol/openid-connect/token](https://identity.cloudferro.com/auth/realms/Eumetsat-elasticity) | -->


<br>

Once you have your token, you require a product Id which can be found in the response of the products search: [https://catalogue.dataspace.copernicus.eu/odata/v1/Products](https://catalogue.dataspace.copernicus.eu/odata/v1/Products)


Finally, you can download the product using this script:
```bash
curl -H "Authorization: Bearer $KEYCLOAK_TOKEN" 'https://catalogue.dataspace.copernicus.eu/odata/v1/Products(060882f4-0a34-5f14-8e25-6876e4470b0d)/$value' --output /tmp/product.zip
```
or
```bash
wget  --header "Authorization: Bearer $KEYCLOAK_TOKEN" 'http://catalogue.dataspace.copernicus.eu/odata/v1/Products(db0c8ef3-8ec0-5185-a537-812dad3c58f8)/$value' -O example_odata.zip
```