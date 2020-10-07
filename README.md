# Azure Digital Twins

Azure Digital Twins is a platform as a service (PaaS) offering that enables the creation of knowledge graphs based on digital models of entire environments. These environments could be buildings, factories, farms, energy networks, railways, stadiums, and more—even entire cities. These digital models can be used to gain insights that drive better products, optimized operations, reduced costs, and breakthrough customer experiences.

More info: https://azure.microsoft.com/es-es/services/digital-twins/

***
**This repository is intended to be a guide to start using the service:**
***

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
az dt role-assignment create --dt-name YourtDTname --assignee "ObjectID" --role "Azure Digital Twins Owner (Preview)"
~~~

We will create a simple model for our Azure Digital Twins

~~~
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n YourtDTname
~~~

We will add a digital model initialized to 0 to generate the first context:
~~~
az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{"Temperature": 0.0,}' --dt-name YourtDTname
~~~

Si necesitamos un simulador, debido a que no tenemos un dispositivo real:

~~~
az iot hub device-identity create --device-id thermostat67 --hub-name <your-IoT-hub-name> -g <your-resource-group>
~~~

To get the information about our new device (simulator):

~~~
az iot hub show-connection-string -n <your-IoT-hub-name>
~~~

~~~
az iot hub device-identity show-connection-string --device-id thermostat67 --hub-name <your-IoT-hub-name>
~~~

To assign permissions to the Function on our ADT instance we must use the following commands:

~~~
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=<your-Azure-Digital-Twins-instance-URL>"
~~~

~~~
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>
~~~

~~~
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Owner (Preview)"
~~~

~~~
az dt twin query -q "select * from digitaltwins" -n YourtDTname
~~~


Pending: 
**Crear manager identity -> sobre el grupo de recuros le das al Azure Digital Twins y de esta forma asignamos la manager identity



