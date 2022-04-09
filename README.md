# WestJet API Management Overview

Westjet operates 4 API environments: Development, QA, Staging and Production, each has its own API Management instance. The Development, QA, and Staging environment exist in West US 2. The Production environment is deployment to US West 2 and US East. 

API developers have access to the Development instance and can use it for developing and testing their APIs. Staging and production instances are managed by a designated team.

API developers can fork the publisher repository to a developer repository and work on the changes for their APIs. Developers can focus on the API templates for their APIs and do not need to change the shared or service templates.

Once API developers have finished developing and testing an API, and have generated the API template, they can submit a pull request to merge the changes to the main branch of this repository. The API management team will validate the pull request to make sure the changes are safe and compliant.

# Creating API Definitions

There are two methods to create API definitions. 

[**Creator**](./src/README.md#Creator)

* API developers who work with [OpenAPI Specification](https://github.com/OAI/OpenAPI-Specification) can use a utility tool called Creator to automate the creation of API templates based on an Open API Specification file. Developers can also supply API Management policies for an API in XML format. With this tool, API developers can continue focusing on the formats and artifacts they are familiar with.

 [**Extractor**](./src/README.md#extractor)

* For customers who have already been using API Management or do not use the Open API Specifications we have created another tool called Extractor to generate templates by extracting configurations from their exisitng API Management instances. 

# Creator
<a name="creator1"></a>
This utility creates API definitions for APIs based on the [OpenAPI Specification](https://github.com/OAI/OpenAPI-Specification) of the API. API policies can optionally be provided. The utility requires one argument, --configFile, which points to a yaml file that controls the templates generated by the Creator tool. The file contains a Creator Configuration object whose schema and related schemas are listed below:

#### Creator Configuration

| Property              | Type                  | Required              | Value                                            |
|-----------------------|-----------------------|-----------------------|--------------------------------------------------|
| version               | string                | Yes                   | Configuration version.                            |
| apimServiceName       | string                | Yes                   | Name of the APIM service to deploy resources into.    |
| policy                | string                | No                    | Location of the global service policy XML file. Can be url or local file.           |
| apiVersionSets         | Array<[APIVersionSetConfiguration](#APIVersionSetConfiguration)> | No               | List of API Version Set configurations.                        |
| apis                   | Array<[APIConfiguration](#APIConfiguration)>      | Yes                   | List of API configurations.                                |
| products                   | Array<[ProductConfiguration](#ProductConfiguration)>      | No                   | List of Product configurations.                                |
| namedValues               | Array<[PropertyConfiguration](#PropertyConfiguration)>     | No                   | List of Named Values
| loggers                   | Array<[LoggerConfiguration](#LoggerConfiguration)>      | No                   | List of Logger configurations.                                |
| authorizationServers                   | Array<[AuthorizationServerContractProperties](#https://docs.microsoft.com/en-us/azure/templates/microsoft.apimanagement/2019-01-01/service/authorizationservers#AuthorizationServerContractProperties)>      | No                   | List of Authorization Server configurations.                                |
| backends                   | Array<[BackendContractProperties](#https://docs.microsoft.com/en-us/azure/templates/microsoft.apimanagement/2019-01-01/service/backends#BackendContractProperties)>      | No                   | List of Backend configurations.                                |
| outputLocation        | string                | Yes                   | Local folder the utility will write templates to. |
| linked                | boolean               | No                    | Determines whether the utility should create a master template that links to all generated templates. |
| linkedTemplatesBaseUrl| string                | No                    | Location that stores linked templates. Required if 'linked' is set to true. |
| linkedTemplatesUrlQueryString| string                | No                    | Query string appended to linked templates uris that enables retrieval from private storage. |
| tags                   | Array<[TagConfiguration](#tagConfiguration)>      | No                   | List of Tags configurations.                                |
| subscriptionKeyParameterNames | APITemplateSubscriptionKeyParameterNames      | No                   | subscription key parameter name.                    |
| baseFileName | string      | No                   | base file name for the templates file               |
| serviceUrlParameters | Array<[ServiceUrlProperty](#ServiceUrlProperty)> | No                   | List of parameterized ServiceUrl.                    |

#### APIConfiguration

| Property              | Type                  | Required              | Value                                            |
|-----------------------|-----------------------|-----------------------|--------------------------------------------------|
| name                  | string                | Yes                   | API identifier. Must be unique in the current API Management service instance.                                 |
| displayName           | string                | No                    | User-friendly name for the API.           |
| description           | string                | No                    | Description of the API.                          |
| serviceUrl            | string                | No                    | Absolute URL of the backend service implementing this API.                                 |
| type                  | enum                  | No                    | Type of API. - http or soap                      |
| openApiSpec           | string                | Yes                   | Location of the Open API Spec file. Can be url or local file.                          |
| openApiSpecFormat           | string                | No                   | Format of the API definition. When the `openApiSpec` property refers to a local file, the program will infer the format if this property is omitted. If the `openApiSpec` property refers to a url, you can prevent downloading the API definition by specifying this property. Valid values are `Swagger` (JSON), `Swagger_Json`, `OpenApi20` (YAML), `OpenApi20_Yaml`, `OpenApi20_Json`, `OpenApi30` (YAML), `OpenApi30_Yaml`, or `OpenApi30_Json`.
| policy                | string                | No                    | Location of the API policy XML file. Can be url or local file.                          |
| suffix                | string                | Yes                    | Relative URL uniquely identifying this API and all of its resource paths within the API Management service instance. It is appended to the API endpoint base URL specified during the service instance creation to form a public URL for this API.                       |
| subscriptionRequired  | boolean               | No                    | Specifies whether an API or Product subscription is required for accessing the API.                         |
| isCurrent             | boolean               | No                    | Indicates if API revision is current api revision.    |
| apiVersion            | string                | No                    | Indicates the Version identifier of the API if the API is versioned.                         |
| apiVersionDescription | string                | No                    | Description of the API Version.                   |
| apiRevision           | string                | No                    | Describes the Revision of the API. If no value is provided, default revision 1 is created.                  |
| apiRevisionDescription| string                | No                    | Description of the Api Revision.                 |
| apiVersionSetId       | string                | No                    | A resource identifier for the related ApiVersionSet. Value must match the resource id on an existing version set and is irrelevant if the apiVersionSet property is supplied.       |
| operations            | Dictionary<string, [APIOperationPolicyConfiguration](#APIOperationPolicyConfiguration)> | No    | XML policies that will be applied to operations within the API. Keys must match the operationId property of one of the API's operations.                 |
| authenticationSettings| [AuthenticationSettingsContract](https://docs.microsoft.com/en-us/azure/templates/microsoft.apimanagement/2018-06-01-preview/service/apis#AuthenticationSettingsContract)                | No                    | Collection of authentication settings included into this API.                         |
| products              | string                | No                    | Comma separated list of existing products to associate the API with.                   |
| protocols             | string                | No                    | Comma separated list of protocols used between client and APIM service.                   |
| diagnostic            | [APIDiagnosticConfiguration](#APIDiagnosticConfiguration) | No | Diagnostic configuration. |
| tags                  | string                | No                    | Comma separated list of tags to associate the API with. Tags can be existing or nonexisting. For nonexisting tags, it will automatically generate new tags on the API instance        |

#### APIOperationPolicyConfiguration

| Property              | Type                  | Required              | Value                                            |
|-----------------------|-----------------------|-----------------------|--------------------------------------------------|
| policy                | string                | Yes                    | Location of the operation policy XML file. Can be url or local file.      |

#### APIDiagnosticConfiguration

| Property              | Type                  | Required              | Value                                            |
|-----------------------|-----------------------|-----------------------|--------------------------------------------------|
| name                  | enum                | No                    | Name of API Diagnostic - azureEventHub or applicationInsights       |

_Additional properties found in [DiagnosticContractProperties](https://docs.microsoft.com/en-us/azure/templates/microsoft.apimanagement/2019-01-01/service/apis/diagnostics#DiagnosticContractProperties)_

#### APIVersionSetConfiguration

| Property              | Type                  | Required              | Value                                            |
|-----------------------|-----------------------|-----------------------|--------------------------------------------------|
| id                    | string                | No                    | ID of the API Version Set.                        |

_Additional properties found in [ApiVersionSetContractProperties](https://docs.microsoft.com/en-us/azure/templates/microsoft.apimanagement/2019-01-01/service/apiversionsets#ApiVersionSetContractProperties)_

#### ProductConfiguration

| Property              | Type                  | Required              | Value                                            |
|-----------------------|-----------------------|-----------------------|--------------------------------------------------|
| name                | string                | No                    | Name of the product resource. If omitted, the display name is used.                          |
| policy                | string                | No                    | Location of the Product policy XML file. Can be url or local file.                          
| subscriptions                | Array<[SubscriptionConfiguration](#SubscriptionConfiguration)>                | No                    | List of Subscriptions

_Additional properties found in [ProductContractProperties](https://docs.microsoft.com/en-us/azure/templates/microsoft.apimanagement/2019-01-01/service/products#ProductContractProperties)_

#### SubscriptionConfiguration

| Property              | Type                  | Required              | Value                                            |
|-----------------------|-----------------------|-----------------------|--------------------------------------------------|
| name                | string                | No                    | Name of the subscription resource. If omitted, the display name is used.                          |

_Additional properties found in [ProductContractProperties](https://docs.microsoft.com/en-us/azure/templates/microsoft.apimanagement/2019-01-01/service/products#ProductContractProperties)_

#### PropertyConfiguration

| Property              | Type                  | Required              | Value                                            |
|-----------------------|-----------------------|-----------------------|--------------------------------------------------|
| tags                | array                | No                    | Optional tags that when provided can be used to filter the property list. - string
| secret                | boolean                | No                    | Determines whether the value is a secret and should be encrypted or not. Default value is false.
| displayName                | string                | Yes                    | Unique name of Property. It may contain only letters, digits, period, dash, and underscore characters.                          |
| value                | string                | No                    | Value of the property. Can contain policy expressions. It can be empty or consist only of whitespace only if the keyvault parameter is set.                          |
| keyvault                | [PropertyKeyVaultConfiguration](#PropertyKeyVaultConfiguration)                 | No                    | The keyvault settings for the property.                          |

_Additional properties found in [PropertyContractProperties](https://docs.microsoft.com/en-us/azure/templates/microsoft.apimanagement/2019-01-01/service/properties#propertycontractproperties-object)_

#### PropertyKeyVaultConfiguration

| Property              | Type                  | Required              | Value                                            |
|-----------------------|-----------------------|-----------------------|--------------------------------------------------|
| secretIdentifier                | string                | Yes                    | KeyVault secret id which will map to the property.   

#### LoggerConfiguration

| Property              | Type                  | Required              | Value                                            |
|-----------------------|-----------------------|-----------------------|--------------------------------------------------|
| name                  | string                | Yes                   | Name of the Logger                         |

_Additional properties found in [LoggerContractProperties](https://docs.microsoft.com/en-us/azure/templates/microsoft.apimanagement/2019-01-01/service/loggers#LoggerContractProperties)_

#### TagConfiguration

| Property              | Type                  | Required              | Value                                            |
|-----------------------|-----------------------|-----------------------|--------------------------------------------------|
| displayName           | string                | Yes                   | DisplayName and name of the tag                  |

_Additional properties found in [TagContractProperties](https://docs.microsoft.com/en-us/azure/templates/microsoft.apimanagement/2019-01-01/service/tags)_

#### APITemplateSubscriptionKeyParameterNames

| Property              | Type                  | Required              | Value                                            |
|-----------------------|-----------------------|-----------------------|--------------------------------------------------|
| header                | string                | Yes                   | header name of the subscription.                 |
| query                 | string                | Yes                   | query parameter name of the subscription.        |

_Additional properties found in [APITemplateSubscriptionKeyParameterNames](https://docs.microsoft.com/en-us/azure/templates/microsoft.apimanagement/2019-01-01/service/subscriptions)_

#### ServiceUrlProperty

| Property              | Type                  | Required              | Value                                            |
|-----------------------|-----------------------|-----------------------|--------------------------------------------------|
| apiName               | string                | Yes                   | Name of API.                 |
| serviceUrl            | string                | Yes                   | API ServiceUrl parameter.        |



### Sample Config File

The following is a full config.yml file with each property listed:

```
version: 0.0.1
apimServiceName: myAPIMService
policy: C:\Users\myUsername\Projects\azure-api-management-devops-example\src\ArmTemplates\Creator\ExampleFiles\XMLPolicies\globalServicePolicy.xml
apiVersionSets:
    - id: myAPIVersionSetID
      displayName: swaggerPetstoreVersionSetLinked
      description: a description
      versioningScheme: Query
      versionQueryName: versionQuery
      versionHeaderName: versionHeader
    - id: secondAPIVersionSetID
      displayName: secondSet
      description: another description
      versioningScheme: Header
      versionQueryName: versionQuery
      versionHeaderName: versionHeader
apis:
    - name: myAPI
      type: http
      displayName: My API
      description: myFirstAPI
      serviceUrl: http://myApiBackendUrl.com
      openApiSpec: C:\Users\myUsername\Projects\azure-api-management-devops-example\src\ArmTemplates\Creator\ExampleFiles\OpenApiSpecs\swaggerPetstore.json
      openApiSpecFormat: swagger
      policy: C:\Users\myUsername\Projects\azure-api-management-devops-example\src\ArmTemplates\Creator\ExampleFiles\XMLPolicies\apiPolicyHeaders.xml
      suffix: conf
      subscriptionRequired: true
      isCurrent: true
      apiVersion: v1
      apiVersionDescription: My first version
      apiVersionSetId: myAPIVersionSetID
      apiRevision: 1
      apiRevisionDescription: My first revision 
      products: myProduct   
      tags: Universe, myTag
      operations:
        addPet:
          policy: C:\Users\myUsername\Projects\azure-api-management-devops-example\src\ArmTemplates\Creator\ExampleFiles\XMLPolicies\operationRateLimit.xml
        deletePet:
          policy: C:\Users\myUsername\Projects\azure-api-management-devops-example\src\ArmTemplates\Creator\ExampleFiles\XMLPolicies\operationRateLimit.xml
      products: starter, platinum
      authenticationSettings:
        oAuth2:
            authorizationServerId: myAuthServer
            scope: myScope
      diagnostic:
        name: applicationinsights
        alwaysLog: allErrors
        loggerId: myAppInsights
        sampling:
          samplingType: fixed
          percentage: 50
        frontend: 
          request:
            headers:
            body: 
              bytes: 512
          response: 
            headers:
            body: 
              bytes: 512
        backend: 
          request:
            headers:
            body: 
              bytes: 512
          response: 
            headers:
            body: 
              bytes: 512
        enableHttpCorrelationHeaders: true
products:
    - name: platinum
      displayName: Platinum
      description: a test product
      terms: some terms
      subscriptionRequired: true
      approvalRequired: true
      subscriptionsLimit: 1
      state: notPublished
      policy: C:\Users\myUsername\Projects\azure-api-management-devops-example\src\ArmTemplates\Creator\ExampleFiles\XMLPolicies\productSetBodyBasic.xml
      subscriptions:
          - name: platinum
            primaryKey: a240691f-03fd-4557-a5cb-6e0f65cd976a
            secondaryKey: 032338aa-0076-4379-910c-32ddd42f38a1
            state: active
            allowTracing: true 
tags:
    - displayName: Universe
loggers:
    - name: myAppInsights
      loggerType: applicationInsights
      description: a test app insights
      credentials:
        instrumentationKey: 45d4v88-fdfs-4b35-9232-731d82d4d1c6
      isBuffered: true
authorizationServers:
    - displayName: myAuthServer
      description: test server
      clientRegistrationEndpoint: https://www.contoso.com/apps
      authorizationEndpoint: https://www.contoso.com/oauth2/auth
      authorizationMethods:
        - GET
      tokenEndpoint: https://www.contoso.com/oauth2/token
      supportState: true
      defaultScope: read write
      grantTypes:
        - authorizationCode
        - implicit
      bearerTokenSendingMethods:
        - authorizationHeader
      clientId: 1
      clientSecret: 2
      resourceOwnerUsername: un
      resourceOwnerPassword: pwd
backends:
    - title: myBackend
      description: description5308
      url: https://backendname2644/
      protocol: http
      credentials:
        query: 
          sv: 
            - xx
            - bb
        header: 
          x-my-1:
            - val1
            - val2
        authorization: 
          scheme: Basic
          parameter: opensesma
      proxy:
        url: http://192.168.1.1:8080
        username: Contoso\admin
        password: opensesame
      tls:
        validateCertificateChain: false
        validateCertificateName: false
outputLocation: C:\Users\myUsername\GeneratedTemplates
linked: false
linkedTemplatesBaseUrl : https://mystorageaccount.blob.core.windows.net/mycontainer
linkedTemplatesUrlQueryString : ?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-12-22T23:12:53Z&st=2019-09-09T14:12:53Z&spr=https&sig=uFTldJEYPH888QVzKb7q7eLq0Xt%2Bu35UTqpFGUYo6uc%3D
baseFileName: baseName
serviceUrlParameters: 
  - apiName: myAPI
    serviceUrl: httpbin.com/myAPI
```

<a name="creator2"></a>

## Running the Creator
Below are the steps to run the Creator from the source code:

- Clone this repository and restore its packages using ```dotnet restore```
- Navigate to {repo root}/src/ARMTemplates directory
- Run the following command:
```dotnet run create --configFile CONFIG_YAML_FILE_LOCATION ```
- Run the following command to pass apim Name as a parameter:
```dotnet run create --configFile CONFIG_YAML_FILE_LOCATION --apimNameValue apimname1```
- Run the following command to pass api name to generate ARM templates only for this specified APIs semicolon separated where the api1 (api name) will be same as name used in valid.yml file for that api(here it is myBackend):
```dotnet run create --configFile CONFIG_YAML_FILE_LOCATION --preferredAPIsForDeployment myBackend;api2;api3;```
- Run the following command to pass BackendUrls as an json input file into the parameter(sample file available in the same path as below in this repository):
```dotnet run create --configFile CONFIG_YAML_FILE_LOCATION --backendurlconfigFile .\apimtemplate\Creator\ExampleFiles\BackendUrlParameter\BackendUrlParameters.json```
- Run the following command to pass AppinsightsName and Appinsights InstrumentationKey as an parameter:
```dotnet run create --configFile CONFIG_YAML_FILE_LOCATION --appInsightsInstrumentationKey 45d4v88-fdfs-4b35-9232-731d82d4d1c6 --appInsightsName  myAppInsights ```
- Run the following command to pass namedValueKeys as an parameter to provide environment specific named values with key name and value like following:
- Add new key to namedValues section in valid yaml file then use the same key name here in this cli parameter
    **Here namedvalue displayname can not have | or ; in their name.**
```dotnet run create --configFile CONFIG_YAML_FILE_LOCATION --namedValues namedvalue1|namevaluevalue1;namedvalue2|namevaluevalue2 ```
Add new key to namedValues section in valid yaml file then use the same key name here in this cli parameter
    **Here displayName can not have | or ; in their name.**
- Run the following command `dotnet run create --configFile CONFIG_YAML_FILE_LOCATION --parameterizeNamedValuesAndSecrets " "` to parameterize namedValues and KeyVault Secrets and allow to use single ARM template for multiple environments

You can also run it directly from the [releases](https://github.com/Azure/azure-api-management-devops-resource-kit/releases).

Additionaly, the Creator can also be made available as a global [dotnet CLI tool](https://docs.microsoft.com/en-us/dotnet/core/tools/global-tools) in your Azure DevOps artifacts or private NuGet repository. Build the Creator, and run the following commands to package the Creator as a dotnet tool:

```
dotnet pack -c Release
dotnet tool install -g --add-source .\bin\Release ArmTemplates
```

The Creator tool is now available anywhere on the command-line:

```apim-templates create --configFile CONFIG_YAML_FILE_LOCATION ```

# Extractor

This utility generates [Resource Manager templates](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authoring-templates) by extracting existing configurations of one or more APIs in an API Management instance. 

<a name="prerequisite"></a>

## Prerequisite

To be able to run the Extractor, you would first need to [install the Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).

<a name="extractor1"></a>

## Running the Extractor
Below are the steps to run the Extractor from the source code:
- Clone this repository and navigate to {repo root}/src/ARMTemplates
- Restore its packages using ```dotnet restore```
- Make sure you have signed in using Azure CLI and have switched to the subscription containing the API Management instance from which the configurations will be extracted. 
```
az login
az account set --subscription <subscription_id>
```

#### Extractor Arguments

You have two choices when specifying your settings:
1. By using a json file with key-values where the keys matches the table below. Use the `extractorConfig` argument:
`extract --extractorConfig c:/temp/extractSettings.json`. [See more examples.](#extractorParameterFileExamples)
2. Pass the arguments on the command line. For instance `extract --sourceApimName my-feature-apim --destinationApimName company-stable-apim --resourceGroup my-feature-rg --fileFolder c:\temp\apim-extract --apiName MyFeatureV1Api`.

| Property              | Required              | Value                                             |
|-----------------------|-----------------------|---------------------------------------------------|
| sourceApimName        | Yes                   | Name of the source APIM instance.                 |
| destinationApimName   | Yes                   | Name of the destination APIM instance.            |
| resourceGroup         | Yes                   | Name of the resource group.                       |
| fileFolder            | Yes                   | Path to output folder                             |
| apiName               | No                    | Name of API. If provided, Extractor executes single API extraction. Otherwise, Extractor executes full extraction.  Note:  This is the "Name" value as seen in the API settings, not "Display Name" and is case sensitive.     |
| linkedTemplatesBaseUrl| No                    | Linked templates remote location. If provided, Extractor generates master template and requires linked templates pushed to remote location.                                   |
| linkedTemplatesUrlQueryString | No            | Query string appended to linked templates uris that enables retrieval from private storage. |
| linkedTemplatesSasToken | No                  | String appended to end of the linked templates uris that enables adding a SAS token or other query parameters. |
| policyXMLBaseUrl      | No                    | Policy XML files remote location. If provided, Extractor generates policies folder with xml files, and requires they be pushed to remote location.                              |
| splitAPIs     | No                    | If set to "true", then generate multiple api folders, each api will have a seperate folder, with a separate master template to deploy this api. If this single api has a version set, then a version set folder will generate instead, then all apis that belongs to this version set will be included in the version set folder, apis in this version set can be deployed separately using every api's master template, or they can be deployed together using the master template in "VersionSetMasterFolder" folder                        |
| apiVersionSetName  | No                    | Name of the APIVersionSet.  If provided, extract all apis within this apiversionset. It will generate seperate folder for each api and also a master folder to link all apis in this apiversionset      |
| multipleAPIs  | No                    | Specify multiple APIs to extract. Generate templates for each API, also generate an aggregated templates folder to deploy these APIs together at a time      |
| includeAllRevisions  | No                    |  Set to "true" will extract all revisions for the single API. Will work only with "apiName" paramter, where you specify which API's revisions to extract. Generate templates for each revision, also generate an aggregated master folder to deploy these revisions together at one time. Note: there are many complicated issues with deploying revisions, make sure your deployment won't overwrite or break the existing ones      |
| baseFileName  | No                    | Specify base file name of the template files      |
|  policyXMLSasToken | No                    | Specify sasToken for fetching policy files    |
|  linkedTemplatesSasToken | No                    | Specify sasToken for fetching linkedTemplate files    |
| serviceUrlParameters  | No                    | Parameterize service url in advance (you can replace serviceUrl afterwards as well, you can refer example for more information).  |
|  paramServiceUrl | No                    |  Set to "true" will parameterize all serviceUrl for each api and generate serviceUrl parameter to api template/parameter template/master template files |
|  paramNamedValue | No                    |  Set to "true" will parameterize all named values and add named values parameter to property template/parameter template/mastert emplate files |
|  paramApiLoggerId | No                    |  Set to "true" will parameterize all logger ids in all apis (within api templates), Also includes the "All API" monitoring configuration |
|  paramLogResourceId | No                    |  Set to "true" will parameterize all loggers' resource ids (within logger template)|
| serviceBaseUrl | No                    | Specify the base url where you want to run your extractor |
| notIncludeNamedValue | No                    | Set to "true" will not generate Named Value Templates|
| paramNamedValuesKeyVaultSecrets | No | Set to true will parameterize all named values where the value is from a key vault secret |
| paramBackend | No | Set to true will parameterize sepcific backend values (limited to resourceId, url and protocol) |
| extractGateways | No | Set to true will attempt to extract the Self Hosted Gateways. |

#### Note
* Can not use "splitAPIs" and "apiName" at the same time, since using "apiName" only extract one API
* Can not use "apiName" and "multipleAPIs" at the same time
* Can only "includeAllRevisions" with "apiName"

<a name="extractorParameterFileExamples"></a>
### Extractor Parameter File Example
 
Executing **a single API extraction with linked templates and policy file** generation, use the following parameters: 
```
{
    "sourceApimName": "<source-apim-name>",
    "destinationApimName": "<destination-apim-name>",
    "resourceGroup": "<resource-group>",
    "fileFolder": "<destination-file-folder>",
    "apiName": "<api_name>",
    "linkedTemplatesBaseUrl": "<linked_templates_remote_location>",
    "policyXMLBaseUrl": "<policies_remote_location>"
}
```
Extract **all APIs with linked templates linking all apis and policy file**, use the following parameters: 
```
{
    "sourceApimName": "<source-apim-name>",
    "destinationApimName": "<destination-apim-name>",
    "resourceGroup": "<resource-group>",
    "fileFolder": "<destination-file-folder>",
    "linkedTemplatesBaseUrl": "<linked_templates_remote_location>",
    "policyXMLBaseUrl": "<policies_remote_location>"
}
```
Extract **all APIs with seperated api folders**, use the following parameters: 
```
{
    "sourceApimName": "<source-apim-name>",
    "destinationApimName": "<destination-apim-name>",
    "resourceGroup": "<resource-group>",
    "fileFolder": "<destination-file-folder>",
    "linkedTemplatesBaseUrl": "<linked_templates_remote_location>",
    "policyXMLBaseUrl": "<policies_remote_location>",
    "splitAPIs": "true"
}
```
Extract **all APIs within an apiversionset**, use the following parameters: 
```
{
    "sourceApimName": "<source-apim-name>",
    "destinationApimName": "<destination-apim-name>",
    "resourceGroup": "<resource-group>",
    "fileFolder": "<destination-file-folder>",
    "linkedTemplatesBaseUrl": "<linked_templates_remote_location>",
    "policyXMLBaseUrl": "<policies_remote_location>",
    "apiVersionSetName": "<api-version-set-name>"
}
```
Extract **single API with all revisions**, use the following parameters: 
```
{
    "sourceApimName": "<source-apim-name>",
    "destinationApimName": "<destination-apim-name>",
    "resourceGroup": "<resource-group>",
    "fileFolder": "<destination-file-folder>",
    "linkedTemplatesBaseUrl": "<linked_templates_remote_location>",
    "policyXMLBaseUrl": "<policies_remote_location>",
    "apiName": "<api_name>",
    "includeAllRevisions": "true"
}
```
Extract **multiple APIs**, use the following parameters: 
```
{
    "sourceApimName": "<source-apim-name>",
    "destinationApimName": "<destination-apim-name>",
    "resourceGroup": "<resource-group>",
    "fileFolder": "<destination-file-folder>",
    "linkedTemplatesBaseUrl": "<linked_templates_remote_location>",
    "policyXMLBaseUrl": "<policies_remote_location>",
    "multipleAPIs": "api1, api2, api3"
}
```
Extract **single API with baseFileName**, use the following parameters: 
```
{
    "sourceApimName": "<source-apim-name>",
    "destinationApimName": "<destination-apim-name>",
    "resourceGroup": "<resource-group>",
    "fileFolder": "<destination-file-folder>",
    "linkedTemplatesBaseUrl": "<linked_templates_remote_location>",
    "policyXMLBaseUrl": "<policies_remote_location>",
    "apiName": "<api_name>",
    "baseFileName": "<base_file_name>"
}
```
Extract **all APIs with serviceUrlParameters**, use the following parameters: 
```
{
    "sourceApimName": "<source-apim-name>",
    "destinationApimName": "<destination-apim-name>",
    "resourceGroup": "<resource-group>",
    "fileFolder": "<destination-file-folder>",
    "linkedTemplatesBaseUrl": "<linked_templates_remote_location>",
    "policyXMLBaseUrl": "<policies_remote_location>",
    "serviceUrlParameters": [
      {
         "apiName": "test",
         "serviceUrl": "http://url.com"
      },
      {  
         "apiName": "api2",
         "serviceUrl": "http://url2.com"
      }
    ]
}
```
Extract **all APIs within parameterServiceUrl**, use the following parameters: 
```
{
    "sourceApimName": "<source-apim-name>",
    "destinationApimName": "<destination-apim-name>",
    "resourceGroup": "<resource-group>",
    "fileFolder": "<destination-file-folder>",
    "linkedTemplatesBaseUrl": "<linked_templates_remote_location>",
    "policyXMLBaseUrl": "<policies_remote_location>",
    "paramServiceUrl": "true"
}
```
Extract **all APIs within parameterServiceUrl**, use the following parameters: 
```
{
    "sourceApimName": "<source-apim-name>",
    "destinationApimName": "<destination-apim-name>",
    "resourceGroup": "<resource-group>",
    "fileFolder": "<destination-file-folder>",
    "linkedTemplatesBaseUrl": "<linked_templates_remote_location>",
    "policyXMLBaseUrl": "<policies_remote_location>",
    "paramNamedValue": "true"
}
```

#### Run the extractor
```
dotnet run extract
```


You can also run it directly from the [releases](https://github.com/Azure/azure-api-management-devops-resource-kit/releases).

Likewise, if you [package the Extractor as a dotnet CLI tool](#creator2), you can run it from anywhere on the command-line:

```
apim-templates extract
```
