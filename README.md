
# Template: SAP to Salesforce Customer Migration

This template moves a large set of customers or accounts from SAP to Salesforce. Trigger with an HTTP call either manually or programmatically.

Accounts are upserted so that the migration can be run multiple times without worrying about creating duplicates. This template uses the Mule batch module to make moving a large set of data easier and more transparent.

![44f6f4a7-7c53-4dce-91ab-f63a38af27ff-image.png](https://exchange2-file-upload-service-kprod.s3.us-east-1.amazonaws.com:443/44f6f4a7-7c53-4dce-91ab-f63a38af27ff-image.png)

[//]: # (![]\(https://www.youtube.com/embed/aXFAwMMAGkU?wmode=transparent\)
[![YouTube Video](http://img.youtube.com/vi/aXFAwMMAGkU/0.jpg)](https://www.youtube.com/watch?v=aXFAwMMAGkU)

# License Agreement
This template is subject to the conditions of the <a href="https://s3.amazonaws.com/templates-examples/AnypointTemplateLicense.pdf">MuleSoft License Agreement</a>. Review the terms of the license before downloading and using this template. You can use this template for free with the Mule Enterprise Edition, CloudHub, or as a trial in Anypoint Studio.

# Use Case
This template should serve as a foundation for setting an offline migration of customers from SAP to Salesforce accounts.

Requirements have been set not only to be used as examples, but also to establish a starting point to adapt your integration to your requirements.

The integration is triggered by an HTTP Listener Connector that receives the migration request. Then it retrieves all customers from SAP using the standard BAPI **BAPI_CUSTOMER_GETLIST**, transforms them into Salesforce accounts and passes them to the Batch process.

As implemented, this template leverages the Batch Module. The batch job is divided in *Process* and *On Complete* stages.

During the *Process* stage, in the first Step the template goes to Salesforce and query all the existing accounts matching the Name for each SAP customer. In the following step, the account is upserted to Salesforce.

Finally during the *On Complete* stage the template both outputs statistics data into the console and sends a notification email with the results of the batch execution.

# Considerations

To make this template run, there are certain preconditions that must be considered. All of them deal with the preparations in both source (SAP) and destination (SFDC) systems, that must be made for your template to run smoothly. Failing to do so could lead to unexpected behavior of the template.

Before continue with the use of this template, you may want to check out this [Documentation Page](https://docs.mulesoft.com/connectors/sap/sap-connector), that teaches you how to work with SAP and Anypoint Studio.

## Disclaimer

This Anypoint template uses a few private Maven dependencies from Mulesoft in order to work. If you intend to run this template with Maven support, you need to add three extra dependencies for SAP to the pom.xml file.

## SAP Considerations

Here's what you need to know to get this template to work with SAP.

### As a Data Source

The SAP backend system is used as a source of data. The SAP connector is used to send and receive the data from the SAP backend. The connector can either use RFC calls of BAPI functions and/or IDoc messages for data exchange, and needs to be properly customized per the "Properties to Configure" section.

## Salesforce Considerations

Here's what you need to know about Salesforce to get this template to work:

- Where can I check that the field configuration for my Salesforce instance is the right one? See: <a href="https://help.salesforce.com/HTViewHelpDoc?id=checking_field_accessibility_for_a_particular_field.htm&language=en_US">Salesforce: Checking Field Accessibility for a Particular Field</a>.
- Can I modify the Field Access Settings? How? See: <a href="https://help.salesforce.com/HTViewHelpDoc?id=modifying_field_access_settings.htm&language=en_US">Salesforce: Modifying Field Access Settings</a>.

### As a Data Destination

There are no considerations with using Salesforce as a data destination.

# Run it!
Simple steps to get SAP to Salesforce customer Migration running.


## Running On Premises
In this section we help you run your template on your computer.

### Where to Download Anypoint Studio and the Mule Runtime
If you are a newcomer to Mule, here is where to get the tools.

+ [Download Anypoint Studio](https://www.mulesoft.com/platform/studio)
+ [Download Mule runtime](https://www.mulesoft.com/lp/dl/mule-esb-enterprise)

### Importing a Template into Studio
In Studio, click the Exchange X icon in the upper left of the taskbar, log in with your Anypoint Platform credentials, search for the template, and click **Open**.


### Running on Studio
After you import your template into Anypoint Studio, follow these steps to run it:

+ Locate the properties file `mule.dev.properties`, in src/main/resources.
+ Complete all the properties required as per the examples in the "Properties to Configure" section.
+ Right click the template project folder.
+ Hover your mouse over `Run as`.
+ Click `Mule Application (configure)`.
+ Inside the dialog, select Environment and set the variable `mule.env` to the value `dev`.
+ Click `Run`. To make this template run on Studio there are a few extra steps that needs to be made. You can check this documentation page [Enabling Your Studio Project for SAP](https://docs.mulesoft.com/connectors/sap-connector#configuring-the-connector-in-studio-7)

### Running on Mule Standalone
Complete all properties in one of the property files, for example in mule.prod.properties and run your app with the corresponding environment variable. To follow the example, this is `mule.env=prod`.


## Running on CloudHub
While creating your application on CloudHub (or you can do it later as a next step), go to Runtime Manager > Manage Application > Properties to set the environment variables listed in "Properties to Configure" as well as the **mule.env**.


### Deploying Your Template on CloudHub
In Studio, right click your project name in Package Explorer and select Anypoint Platform > Deploy on CloudHub.


## Properties to Configure
To use this template, configure properties (credentials, configurations, etc.) in the properties file or in CloudHub from Runtime Manager > Manage Application > Properties. The sections that follow list example values.

### Application Configuration
**HTTP Connector Configuration**
+ http.port `9090`

**Batch Aggregator Configuration**
+ page.size `200`

**SAP Connector Configuration**
+ sap.jco.ashost `your.sap.address.com`
+ sap.jco.user `SAP_USER`
+ sap.jco.passwd `SAP_PASS`
+ sap.jco.sysnr `14`
+ sap.jco.client `800`
+ sap.jco.lang `EN`

**Maximal number of customers from SAP**
+ sap.maxrows `20`

**SalesForce Connector Configuration**
+ sfdc.username `bob.dylan@sfdc`
+ sfdc.password `DylanPassword123`
+ sfdc.securityToken `avsfwCUl7apQs56Xq2AKi3X`

**SMTP Services Configuration**
+ smtp.host `smtp.gmail.com`
+ smtp.port `587`
+ smtp.user `gmailuser`
+ smtp.password `gmailpassword`

**EMail Details**
+ mail.from `your.email@gmail.com`
+ mail.to `your.email@gmail.com`
+ mail.subject `Mail subject`

# API Calls
SalesForce imposes limits on the number of API Calls that can be made. Therefore calculating this amount may be an important factor to consider. customer migration template calls to the API can be calculated using the formula:

**X + (X / ${page.size})**

X is the number of customers to be synchronized on each run.

Divide by ${page.size} because, by default, customers are gathered in groups of ${page.size} for each Upsert API Call in the commit step. For instance if 10 records are fetched from origin instance, then 11 API calls to SFDC is made ( 1).


# Customize It!
This brief guide intends to give a high level idea of how this template is built and how you can change it according to your needs. As Mule applications are based on XML files, this page describes the XML files used with this template.

More files are available such as test classes and Mule application files, but to keep it simple, we focus on these XML files:

* config.xml
* businessLogic.xml
* endpoints.xml
* errorHandling.xml

## config.xml
Configuration for connectors and configuration properties are set in this file. Even change the configuration here, all parameters that can be modified are in properties file, which is the recommended place to make your changes. However if you want to do core changes to the logic, you need to modify this file.

In the Studio visual editor, the properties are on the *Global Element* tab.

## businessLogic.xml
Functional aspect of the template is implemented on this XML, directed by a batch job that is responsible for creations/updates. The several message processors constitute four high level actions that fully implement the logic of this template:

1. Job execution is invoked from triggerFlow (endpoints.xml).
2. During the *Process* stage, each customer is filtered depending on, if it has an existing matching account in the Salesforce instance. The matching is performed by querying a Salesforce instance for an entry with the given Name.
3. The next step inserts a new record into the Salesforce instance if there was none found in the previous step or update the existing one.

Finally during the *On Complete* stage the template logs output statistics data into the console and send a notification email with the results.

## endpoints.xml
This file provides the inbound and outbound sides of your integration app. This template has a HTTP Listener Connector as the way to trigger the use case.

### Trigger Flow
**HTTP Listener Connector** - Start Report Generation

+ `${http.port}` is set as a property to be defined either on a property file or in CloudHub environment variables.
+ The path configured by default is `migrateaccounts` and you are free to change for the one you prefer.
+ The host name for all endpoints in your CloudHub configuration should be defined as `localhost`. CloudHub then routes requests from your application domain URL to the endpoint.

## errorHandling.xml
This file specifies how your integration reacts depending on the different exceptions.  This file provides error handling that is referenced by the main flow in the business logic.
