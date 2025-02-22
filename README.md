# Azure Purview CLI

This package provides a command line interface to Azure Purview's REST API.  
![purviewcli](https://raw.githubusercontent.com/tayganr/purviewcli/master/doc/image/purviewcli_example.png)

## Requirements

The purviewcli package works on Python versions:

* Python 3.6+
* Python 3.7+
* Python 3.8+
* Python 3.9+

## Local Installation

```
pip install purviewcli
```

## Run container on Docker Desktop

Alternatively, you can run Purview CLI inside a self-enclosed Docker Container.

Clone this repo - then to run the container locally on Docker Desktop, run:

```powershell

# Change into "docker" directory
cd docker

# Build container image locally from included Dockerfile
docker build -t purview-cli .

# Start container by injecting environment variables
docker run --name purview-cli-docker -d `
  -e "PURVIEW_NAME=<your--purview--account--name>" `
  -e "AZURE_CLIENT_ID=<your--client--id>" `
  -e "AZURE_CLIENT_SECRET=<your--client--secret>" `
  -e "AZURE_TENANT_ID=<your--azure--tenant--id>" `
  purview-cli
```
This will spin up a container in Docker Desktop with the environment variables injected in - for example:
![Spin up container](/doc/image/purviewcli_runcontainer.png)

And we can shell into the running container using Docker Desktop:
![Shell into container](/doc/image/purviewcli_runcontainershell.png)

Execute a command, e.g. Get all classifications: `pv types readTypeDefs --type "CLASSIFICATION"`:
![Run sample command](/doc/image/purviewcli_runcmdcontainershell.png)

## Getting Started

[![YouTube](https://raw.githubusercontent.com/tayganr/purviewcli/master/doc/image/purviewcli_youtube.png)](https://www.youtube.com/watch?v=ycr1G5iMM6U)

1. Install purviewcli (e.g. `pip install purviewcli`).
2. [OPTIONAL] Set environment variable(s).
    *  `PURVIEW_NAME`
    * `AZURE_CLIENT_ID`
    * `AZURE_TENANT_ID`
    * `AZURE_CLIENT_SECRET`

   Note #1: The environment variables related to authentication are optional as there are several methods in which we can pass credentials to purviewcli in order to authenticate with an instance of Azure Purview. See [Authentication](#authentication) for more details. 

   Note #2: While an Azure Purview account name ***must*** be specified, you can provide this value within the command itself (as opposed to via an environment variable). Simply add `--purviewName=<val>` at the end of any command.

3. Execute a command (e.g. `pv glossary read --purviewName PURVIEW_ACCOUNT_NAME`).

Snippet of an example Python-based notebook below.  
Note: If you are executing purviewcli commands within a Python notebook, you will need to prefix the command with an exclamation mark `!`. This will ensure the command is passed to the shell (not the Python interpreter).

![purviewcli](https://raw.githubusercontent.com/tayganr/purviewcli/master/doc/image/purviewcli_notebook.png)

## Authentication

The purviewcli package leverages the `DefaultAzureCredential` method from [azure-identity](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/identity/azure-identity#defaultazurecredential). This provides purviewcli a variety of credential sources it can use to attempt authentication (e.g. Environment Variables, Managed Identity, Visual Studio Code, Azure CLI, Interactive). For example, if you are signed into Azure within Visual Studio Code, purviewcli will leverage those existing credentials when executing a command. This negates the need to store and manage credentials specific to the purviewcli package by leveraging what exists already. Read the [azure-identity](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/identity/azure-identity#defaultazurecredential) documentation to understand the authentication hierarchy.

## Authorization

The identity executing Azure Purview CLI commands will need the following role assignments:  

* `Data Curator` (read/write on catalog objects)
* `Data Source Admin` (read/write on sources/scans)
* `Collection Admin` (assign roles/manage collections)

For more information, check out [Access control in Azure Purview](https://docs.microsoft.com/en-us/azure/purview/catalog-permissions).

## Usage

```
pv command sub-command --parameter1='value' --parameter2='value'
```

## Parameter Types

* All parameters are required by default.
* Parameters enclosed with square brackets "**[ ]**" are optional.
* Mutually exclusive parameters are enclosed with parens "**( )**" and separated with a pipe "**|**".
* The "=&lt;val&gt;" indicates parameters which require an input (e.g. --parameter=&lt;val&gt;). Input can be specified after a space (e.g. --parameter 'value') or equal "**=**" sign (e.g. --parameter='value').
* Parameters that do not require an input are **False** by default and **True** if present (e.g. --ignoreRelationships).
* The ellipsis "**...**" indicates parameters that are allowed to repeat (e.g. --guid='12345' --guid='23451' --guid='34512')

## Commands

### Search

```
pv search query [--keywords=<val> --limit=<val> --offset=<val> --filter-file=<val> --facets-file=<val>]
```

### Entity

```
pv entity create --payload-file=<val>
pv entity createBulk --payload-file=<val>
pv entity createBulkClassification --payload-file=<val>
pv entity createBulkSetClassifications --payload-file=<val>
pv entity createClassifications --guid=<val> --payload-file=<val>
pv entity createUniqueAttributeClassifications --typeName=<val> --payload-file=<val>
pv entity delete --guid=<val>
pv entity deleteBulk --guid=<val>...
pv entity deleteClassification --guid=<val> --classificationName=<val>
pv entity deleteUniqueAttribute --typeName=<val>
pv entity deleteUniqueAttributeClassification --typeName=<val> --classificationName=<val>
pv entity put --guid=<val> --name=<val> --payload-file=<val>
pv entity putClassifications --guid=<val> --payload-file=<val>
pv entity putUniqueAttribute --typeName=<val> --payload-file=<val>
pv entity putUniqueAttributeClassifications --typeName=<val> --payload-file=<val>
pv entity read --guid=<val> [--ignoreRelationships --minExtInfo]
pv entity readBulk --guid=<val>... [--ignoreRelationships --minExtInfo]
pv entity readBulkUniqueAttribute --typeName=<val> [--ignoreRelationships --minExtInfo]
pv entity readClassification --guid=<val> --classificationName=<val>
pv entity readClassifications --guid=<val>
pv entity readHeader --guid=<val>
pv entity readUniqueAttribute --typeName=<val> --qualifiedName=<val> [--ignoreRelationships --minExtInfo]
```

### Glossary

```
pv glossary create --payload-file=<val>
pv glossary createCategories --payload-file=<val>
pv glossary createCategory --payload-file=<val>
pv glossary createTerm --payload-file=<val>
pv glossary createTerms --payload-file=<val>
pv glossary createTermsAssignedEntities --termGuid=<val> --payload-file=<val>
pv glossary createTermsExport --glossaryGuid=<val> --termGuid=<val>...
pv glossary createTermsImport (--glossaryGuid=<val> | --glossaryName=<val>)
pv glossary delete --glossaryGuid=<val>
pv glossary deleteCategory --categoryGuid=<val>
pv glossary deleteTerm --termGuid=<val>
pv glossary deleteTermsAssignedEntities --termGuid=<val> --payload-file=<val>
pv glossary put --glossaryGuid=<val> --payload-file=<val>
pv glossary putCategory --categoryGuid=<val> --payload-file=<val>
pv glossary putCategoryPartial --categoryGuid=<val> --payload-file=<val>
pv glossary putPartial --glossaryGuid=<val> --payload-file=<val>
pv glossary putTerm --termGuid=<val> --payload-file=<val>
pv glossary putTermPartial --termGuid=<val> --payload-file=<val>
pv glossary putTermsAssignedEntities --termGuid=<val> --payload-file=<val>
pv glossary read [--glossaryGuid=<val> --limit=<val> --offset=<val> --sort=<val>]
pv glossary readCategories --glossaryGuid=<val> [--limit=<val> --offset=<val> --sort=<val>]
pv glossary readCategoriesHeaders --glossaryGuid=<val> [--limit=<val> --offset=<val> --sort=<val>]
pv glossary readCategory --categoryGuid=<val> [--limit=<val> --offset=<val> --sort=<val>]
pv glossary readCategoryRelated --categoryGuid=<val>
pv glossary readCategoryTerms --categoryGuid=<val> [--limit=<val> --offset=<val> --sort=<val>]
pv glossary readDetailed --glossaryGuid=<val>
pv glossary readTerm --termGuid=<val>
pv glossary readTerms [--glossaryGuid=<val> --limit=<val> --offset=<val> --sort=<val> --extInfo --includeTermHierarchy]
pv glossary readTermsAssignedEntities --termGuid=<val> [--limit=<val> --offset=<val> --sort=<val>]
pv glossary readTermsHeaders --glossaryGuid=<val> [--limit=<val> --offset=<val> --sort=<val>]
pv glossary readTermsImport --operationGuid=<val>
pv glossary readTermsRelated --termGuid=<val> [--limit=<val> --offset=<val> --sort=<val>]
```

### Lineage

```
pv lineage read --guid=<val> [--depth=<val> --width=<val> --direction=<val>]
pv lineage readNext --guid=<val> [--direction<val> --offset=<val> --limit=<val>]
```

### Relationship

```
pv relationship create --payload-file=<val>
pv relationship delete --guid=<val>
pv relationship put --payload-file=<val>
pv relationship read --guid=<val> [--extendedInfo]
```

### Types

```
pv types createTypeDefs --payload-file=<val>
pv types deleteTypeDef --name=<val>
pv types deleteTypeDefs --payload-file=<val>
pv types putTypeDefs --payload-file=<val>
pv types readClassificationDef (--guid=<val> | --name=<val>)
pv types readEntityDef (--guid=<val> | --name=<val>)
pv types readEnumDef (--guid=<val> | --name=<val>)
pv types readRelationshipDef (--guid=<val> | --name=<val>)
pv types readStatistics
pv types readStructDef (--guid=<val> | --name=<val>)
pv types readTermTemplateDef (--guid=<val> | --name=<val>)
pv types readTypeDef (--guid=<val> | --name=<val>)
pv types readTypeDefs [--includeTermTemplate --type=<val>]
pv types readTypeDefsHeaders [--includeTermTemplate --type=<val>]
```

### Scan

```
pv scan cancelScan --dataSourceName=<val> --scanName=<val> --runId=<val>
pv scan deleteClassificationRule --classificationRuleName=<val>
pv scan deleteDataSource --dataSourceName=<val>
pv scan deleteKeyVault --keyVaultName=<val>
pv scan deleteScan --dataSourceName=<val> --scanName=<val>
pv scan deleteScanRuleset --scanRulesetName=<val>
pv scan deleteTrigger --dataSourceName=<val> --scanName=<val>
pv scan putClassificationRule --classificationRuleName=<val> --payload-file=<val>
pv scan putDataSource --dataSourceName=<val> --payload-file=<val>
pv scan putFilter --dataSourceName=<val> --scanName=<val> --payload-file=<val>
pv scan putKeyVault --keyVaultName=<val> --payload-file=<val>
pv scan putScan --dataSourceName=<val> --scanName=<val> --payload-file=<val>
pv scan putScanRuleset --scanRulesetName=<val> --payload-file=<val>
pv scan putTrigger --dataSourceName=<val> --scanName=<val> --payload-file=<val>
pv scan readClassificationRule --classificationRuleName=<val>
pv scan readClassificationRuleVersions --classificationRuleName=<val>
pv scan readClassificationRules
pv scan readDatasource --dataSourceName=<val>
pv scan readDatasources
pv scan readFilters --dataSourceName=<val> --scanName=<val>
pv scan readKeyVault --keyVaultName=<val>
pv scan readKeyVaults
pv scan readScan --dataSourceName=<val> --scanName=<val>
pv scan readScanHistory --dataSourceName=<val> --scanName=<val>
pv scan readScanRuleset --scanRulesetName=<val>
pv scan readScanRulesets
pv scan readScans --dataSourceName=<val>
pv scan readSystemScanRuleset --dataSourceType=<val>
pv scan readSystemScanRulesetLatest --dataSourceType=<val>
pv scan readSystemScanRulesetVersion --version=<val> --dataSourceType=<val>
pv scan readSystemScanRulesetVersions --dataSourceType=<val>
pv scan readSystemScanRulesets
pv scan readTrigger --dataSourceName=<val> --scanName=<val>
pv scan runScan --dataSourceName=<val> --scanName=<val>
```

### Credential

```
pv credential delete --credentialName=<val>
pv credential put --credentialName=<val> --payload-file=<val>
pv credential read [--credentialName=<val>]
```

### Management

```
pv management checkNameAvailability --subscriptionId=<val> --accountName=<val>
pv management createAccount --subscriptionId=<val> --resourceGroupName=<val> --accountName=<val> --payload-file=<val>
pv management defaultAccount --scopeTenantId=<val> --scopeType=<val> --scope=<val>
pv management deleteAccount --subscriptionId=<val> --resourceGroupName=<val> --accountName=<val>
pv management deletePrivateEndpoint --subscriptionId=<val> --resourceGroupName=<val> --accountName=<val> --privateEndpointConnectionName=<val>
pv management listKeys --subscriptionId=<val> --resourceGroupName=<val> --accountName=<val>
pv management listOperations
pv management listPrivateLinkResources --subscriptionId=<val> --resourceGroupName=<val> --accountName=<val> [--groupId=<val>]
pv management putPrivateEndpoint --subscriptionId=<val> --resourceGroupName=<val> --accountName=<val> --privateEndpointConnectionName=<val> --payload-file=<val>
pv management readAccount --subscriptionId=<val> --resourceGroupName=<val> --accountName=<val>
pv management readAccounts --subscriptionId=<val> [--resourceGroupName=<val>]
pv management readPrivateEndpoint --subscriptionId=<val> --resourceGroupName=<val> --accountName=<val> --privateEndpointConnectionName=<val>
pv management readPrivateEndpoints --subscriptionId=<val> --resourceGroupName=<val> --accountName=<val>
pv management removeDefaultAccount --scopeTenantId=<val> --scopeType=<val> --scope=<val>
pv management setDefaultAccount --subscriptionId=<val> --resourceGroupName=<val> --accountName=<val> --scopeTenantId=<val> --scopeType=<val> --scope=<val>
pv management updateAccount --subscriptionId=<val> --resourceGroupName=<val> --accountName=<val> --payload-file=<val>
```

### Insight (Guardian)

```
pv insight assetDataSources
pv insight assetDistribution
pv insight fileExtensions [--numberOfDays=<val> --takeTopCount=<val>]
pv insight fileTypeSizeTimeSeries [--numberOfDays=<val> --fileType=<val> --dataSource=<val>]
pv insight filesWithoutResourceSet
pv insight graphql --payload-file=<val>
pv insight scanStatusSummaries [--numberOfDays=<val>]
pv insight scanStatusSummariesByTs [--numberOfDays=<val>]
pv insight topFileTypesBySize
```

### Policystore

```
pv policystore readMetadataRoles
pv policystore readMetadataPolicy (--collectionName=<val> | --policyId=<val>)
pv policystore readMetadataPolicies
pv policystore putMetadataPolicy --policyId=<val> --payload-file=<val>
```

### Account

```
pv account deleteCollection --collectionName=<val>
pv account getChildCollectionNames --collectionName=<val>
pv account getCollection --collectionName=<val>
pv account getCollectionPath --collectionName=<val>
pv account getCollections
pv account putCollection --friendlyName=<val> --parentCollection=<val>
```