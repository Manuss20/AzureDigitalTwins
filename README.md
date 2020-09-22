# Azure Digital Twins

To deploy the solution we must take into account the following steps:

To start working we must authenticate and select the subscription that we are going to use:
~~~
az login
az account list --output table
az account set --subscription "Your ID subscription"
~~~

We will add the namespaces to be able to provision:

~~~
az provider register --namespace 'Microsoft.DigitalTwins'
~~~

Assign permissions (you must assign the user's Object ID in Azure AD):

~~~
az dt role-assignment create --dt-name StarkIndustries --assignee "ObjectID" --role "Azure Digital Twins Owner (Preview)"
~~~

We will create a simple model for our Azure Digital Twins

~~~
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n StarkIndustries
~~~

We will add a digital model initialized to 0 to generate the first context:
~~~
az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{"Temperature": 0.0,}' --dt-name StarkIndustries
~~~