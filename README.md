# Hybris

## Installation of Hybris
* Download the software from https://support.sap.com/software.html 
* Unzip it to in any srive.
* After unzipping it will have bin and licences folders
* Under bin we have some default extensions
* Bin contains the extension, where as licences folder contains the software licence.
* After all these go to bin folder, and run below command to build the application.
  * setantenv.bat
  * ant clean all  -- It checks the existing folder structure. If any folder structures are missing then it creates them, else it will skip. If there is no build, it will create it from scratch, else it will delete it and re-creates it.
  * ant all -- It assumes that the folder structure already exists. If there is no build it will create it from scratch, else it will modify the build.
* Starting the server
  * hybrisserver.bat
* Default port will be 9001, so to access the application you need to click on http://localhost:9001
* By default hybris will connect to HSQL database, If we want to chane we can add the new database configuration under local.properties file.
* Stopping the server : We need to press the Ctrl+C to stop the server(graceful shutdown)
## Initialization:
  One of the goals of the initialization process is to prepare the data to your store.
  
we have two initialization hooks provided by accelarator.
1) core data hook -- CoreSystemSetup in core extension -- basic data : cockpit definations, email templates, CMS layout etc.
2) sample data hook -- InitialDataSystemSetup in initialdata extension -- catalogs, solr configurations, etc..
This uses import services CoreDataImportService and SampleDataImportService to import the data.

### CoreDataImportService: 
import all essential data for a new store. The service is used to import basic structure
of store(e.g catalog defination, email templates, site defination).

### SampleDataImportService: 
import all store specific data like products, images, cms content etc..

-- we need to modify the InitialDataSystemSetup class to create our new store data every time while we are performing
initialization/update of system.
for that we need to update the createProjectData method in the above class to add the product, content catalogs and site.

@SystemSetup() -- will take 3 arguments 
1) type -- project, essential, all
2) process -- init, update, all
3) extension -- name of extension

## Dynamic Attribute:
 <persistence type="dynamic" attributeHandler="dynamicHybrisCustomerAttributeBean" />
Means this value will not be persisted in the database. We can handle this attribute by using DynamicAttribute handler.
According to the requirement we can add modifiers like read/write etc.

we need to cretae a java class which will implements the DynamicAttributeHandler interface, we need to provide the defination
for get(returntype, model) method. And we need to register this as spring bean in *core-spring.xml file.

## Impex for Basic Store:

Store.impex -- Contains the BaseStore id, BaseStore2DeliveryModelRel and PromotionGroup
store_en.impex -- add the english localized values to the base store. BaseStroe name.

BaseStore2DeliveryModelRel - Is a relation type in commerceservices-items.xml file which have the relation between
store and deliverymode, which is many-many, so we need to maintain a separate table to use the many-many relation.

site.impex -- contaisn the CMSSite, cartRemovalCronJob, SiteMapPage
site_en.impex -- add the english localization to CMSSite

solr.impex -- From 5.7 embedded server is deprecated, so we need to use it as standalone and we need to define
endpoint url for solr server. and cretae the index related details in this file.
solr_en.impex -- add the english localization.
solrtrigger.impex -- when index needs to run, that configuration we need to mention here.

### Content Catalog :

catalog.impex -- used to create the content catalogs in staged and online version. We will give the permissions
like writePrinciples and readPrinciples to users by using this impex file.
catalog_en.impex -- for localization

cms-content.impex -- which defines all of the content slots for the templates, naming the templates
cms-content_en.impex -- localization

email-content.impex -- updates the emailnotification layout.
email-content_en.impex

mobile-cms-content.impex -- related to mobile site
mobile-cms-content_en.impex

### Product catalog:

catalog.impex -- used to create product catalogs
catalog_en.impex

### Impex for Create products: 

categories.impex -- to add category and subcategories. Category and CategoryCategoryRelation items.
categories_en.impex -- localization

products.impex -- to create the products, Product item where we can provide the supercategories of the product.
products-media.impex -- used to provide the images to products by using Media, MediaContainer and Product items
products_en.impex -- localization
products-prices.impex -- used to mention the prices of products. Product item
products-relations.impex -- used to mention the relations like cross selling and upselling
products-stocklevels.impex -- used to mention the vendor name and the no of stocks available for that product and
we will provide the relation between warehouse, vendor and product and stocklevels

warehouses.impex -- used to link warehouse and store.

### Content Catalog:

cms-content.impex: 
cms-content_en.impex
email-content.impex
email-content_en.impex
cmscockpit-users.impex

We need to do the System Update and restart the server then you will find the Merchandise Storefront.

## Hot Folder:

   Hybris supports hot folders. Hot folders are folders from which data can be automatically imported into hybris by
simply placing the data inside the folder.
  Hybris accelaratorservices extension template comes with a batch package that enables automated importing of data from
hot folder.

### Steps: 
1) We need to scan for the files, by using inbound channel adapter tag.
2) move the files for processing and setup header, by using outbound gateway and service activator(which wil take the
request messages from input-channel and sends reply to output-channel by using service activator)

add above two steps in hot-folder-store-{storename}-spring.xml file
3) mention the above hot folder spring.xml file location in *core-spring.xml file using import tag.

then restart the server to apply the spring related changes.

Then place the csv file in the hot folder then if file processed then it will move to acrhive directory.

How PK will be generated in hybris?
-- It is the combination of
1) typeCode of that type
2) getClusterId()
3) creationTime()

then modelService().get(PK) - extracts the typeCode of that Item and it will search in that particular table.
so that is the reason typecode should be unique over the hybris system.

Improve the performance ImpEx:
https://wiki.hybris.com/display/~brendan.dobbs@hybris.com/Improving+ImpEx+Performance
