---
title: Funkce šablon Azure Resource Manageru – prostředky | Dokumentace Microsoftu
description: Popisuje funkce pro použití v šabloně Azure Resource Manageru k načtení hodnoty o prostředcích.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2019
ms.author: tomfitz
ms.openlocfilehash: 73c5e6d1d5078040fee23a2c94bb4fbb649fada4
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2019
ms.locfileid: "55082159"
---
# <a name="resource-functions-for-azure-resource-manager-templates"></a>Prostředek funkce pro šablony Azure Resource Manageru

Resource Manager poskytuje následující funkce pro načtení prostředků hodnot:

* [seznam *](#list)
* [Zprostředkovatelé](#providers)
* [Referenční dokumentace](#reference)
* [resourceGroup](#resourcegroup)
* [ID prostředku](#resourceid)
* [předplatné](#subscription)

Chcete-li získat hodnoty z parametrů, proměnných nebo aktuální nasazení, přečtěte si téma [funkce hodnotu nasazení](resource-group-template-functions-deployment.md).

<a id="listkeys" />
<a id="list" />

## <a name="list"></a>seznam *

`list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)`

Syntaxe pro tuto funkci se liší podle názvu operace výpisu. Každá implementace vrátí hodnoty pro typ prostředku, který podporuje operaci seznamu. Název operace musí začínat znakem `list`. Jsou některé běžné použití `listKeys` a `listSecrets`. 

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| resourceName nebo resourceIdentifier |Ano |řetězec |Jedinečný identifikátor prostředku. |
| apiVersion |Ano |řetězec |Verze rozhraní API prostředku běhový stav. Obvykle ve formátu **rrrr mm-dd**. |
| functionValues |Ne |objekt | Objekt, který obsahuje hodnoty pro funkci. Tento objekt poskytují pouze pro funkce, které podporují přijímá objekt s hodnotami parametrů, jako například **listAccountSas** v účtu úložiště. | 

### <a name="implementations"></a>Implementace

V následující tabulce jsou uvedeny možné použití seznamu *.

| Typ prostředku | Název funkce |
| ------------- | ------------- |
| Microsoft.Addons/supportProviders | listsupportplaninfo |
| Microsoft.AnalysisServices/servers | listGatewayStatus |
| Microsoft.Automation/automationAccounts | klíče Listkey |
| Microsoft.AzureStack/registrations/products | listDetails |
| Microsoft.Batch/batchAccounts | klíče listkey |
| Microsoft.BatchAI/workspaces/experiments/jobs | listoutputfiles |
| Microsoft.BingMaps/mapApis | listSecrets |
| Microsoft.BingMaps/mapApis | listSingleSignOnToken |
| Microsoft.Cache/redis | klíče Listkey |
| Microsoft.Cache/redis | listUpgradeNotifications |
| Microsoft.CognitiveServices/accounts | klíče Listkey |
| Microsoft.ContainerRegistry/registries/buildTasks | listSourceRepositoryProperties |
| Microsoft.ContainerRegistry/registries/buildTasks/steps | listBuildArguments |
| Microsoft.ContainerRegistry/registries | listBuildSourceUploadUrl |
| Microsoft.ContainerRegistry/registries | listCredentials |
| Microsoft.ContainerRegistry/registries | Funkce listPolicies |
| Microsoft.ContainerRegistry/registries | listUsages |
| Microsoft.ContainerRegistry/registries/runs | listLogSasUrl |
| Microsoft.ContainerRegistry/registries/tasks | listDetails |
| Microsoft.ContainerRegistry/registries/webhooks | listEvents |
| Microsoft.ContainerService/managedClusters/accessProfiles | listCredential |
| Microsoft.ContainerService/managedClusters | listClusterAdminCredential |
| Microsoft.ContainerService/managedClusters | listClusterUserCredential |
| Microsoft.ContentModerator/applications | listSecrets |
| Microsoft.ContentModerator/applications | listSingleSignOnToken |
| Microsoft.ContentModerator | listCommunicationPreference |
| Microsoft.DataBox/jobs | listCredentials |
| Microsoft.DataFactory/datafactories/gateways | listauthkeys |
| Microsoft.DataFactory/factories/integrationruntimes | listauthkeys |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers | listSasTokens |
| Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys | klíče listkey |
| Microsoft.Devices/elasticPools/iotHubTenants | klíče Listkey |
| Microsoft.Devices/iotHubs/iotHubKeys | klíče listkey |
| Microsoft.Devices/iotHubs | klíče listkey |
| Microsoft.Devices/provisioningServices/keys | klíče listkey |
| Microsoft.Devices/provisioningServices | klíče listkey |
| Microsoft.DevSpaces/controllers | listConnectionDetails |
| Microsoft.DevTestLab/labs | ListVhds |
| Microsoft.DevTestLab/labs/schedules | ListApplicable |
| Microsoft.DevTestLab/labs/users/serviceFabrics | ListApplicableSchedules |
| Microsoft.DevTestLab/labs/virtualMachines | ListApplicableSchedules |
| Microsoft.DocumentDB/databaseAccounts | listConnectionStrings |
| Microsoft.DocumentDB/databaseAccounts | klíče Listkey |
| Microsoft.DomainRegistration | listDomainRecommendations |
| Microsoft.DomainRegistration/topLevelDomains | listAgreements |
| Microsoft.EventGrid/topics | klíče Listkey |
| Microsoft.EventHub/namespaces/authorizationRules | klíče listkey |
| Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules | klíče listkey |
| Microsoft.EventHub/namespaces/eventhubs/authorizationRules | klíče listkey |
| Microsoft.ImportExport/jobs | listBitLockerKeys |
| Microsoft.Insights | ListMigrationDate |
| Microsoft.LabServices/users | ListEnvironments |
| Microsoft.LabServices/users | ListLabs |
| Microsoft.LocationBasedServices/accounts | klíče Listkey |
| Microsoft.LocationServices/accounts | klíče Listkey |
| Microsoft.Logic/integrationAccounts/agreements | listContentCallbackUrl |
| Microsoft.Logic/integrationAccounts/assemblies | listContentCallbackUrl |
| Microsoft.Logic/integrationAccounts | listCallbackUrl |
| Microsoft.Logic/integrationAccounts | listKeyVaultKeys |
| Microsoft.Logic/integrationAccounts/maps | listContentCallbackUrl |
| Microsoft.Logic/integrationAccounts/partners | listContentCallbackUrl |
| Microsoft.Logic/integrationAccounts/schemas | listContentCallbackUrl |
| Microsoft.Logic/workflows/accessKeys | list |
| Microsoft.Logic/workflows | listCallbackUrl |
| Microsoft.Logic/workflows | listSwagger |
| Microsoft.Logic/workflows/runs/actions | listExpressionTraces |
| Microsoft.Logic/workflows/runs/actions/repetitions | listExpressionTraces |
| Microsoft.Logic/workflows/triggers | listCallbackUrl |
| Microsoft.Logic/workflows/versions/triggers | listCallbackUrl |
| Microsoft.MachineLearning/webServices | klíče listkey |
| Microsoft.MachineLearning/Workspaces | listworkspacekeys |
| Microsoft.MachineLearningCompute/operationalizationClusters | klíče Listkey |
| Microsoft.MachineLearningServices/workspaces/computes | klíče Listkey |
| Microsoft.MachineLearningServices/workspaces | klíče Listkey |
| Microsoft.Maps/accounts | klíče Listkey |
| Microsoft.MarketplaceApps/ClassicDevServices | listSecrets |
| Microsoft.MarketplaceApps/ClassicDevServices | listSingleSignOnToken |
| Microsoft.Media/mediaservices/assets | listContainerSas |
| Microsoft.Media/mediaservices/assets | listStreamingLocators |
| Microsoft.Media/mediaservices/streamingLocators | listContentKeys |
| Microsoft.Media/mediaservices/streamingLocators | listPaths |
| Microsoft.Network/applicationSecurityGroups | listIpConfigurations |
| microsoft.network/vpngateways | listvpnconnectionshealth |
| Microsoft.NotificationHubs/Namespaces/authorizationRules | klíče listkey |
| Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules | klíče listkey |
| Microsoft.OperationalInsights/workspaces | klíče Listkey |
| Microsoft.OperationalInsights/workspaces | klíče Listkey |
| Microsoft.PolicyInsights/remediations | listDeployments |
| Microsoft.Relay/namespaces/authorizationRules | klíče listkey |
| Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules | klíče listkey |
| Microsoft.Relay/namespaces/HybridConnections/authorizationRules | klíče listkey |
| Microsoft.Relay/namespaces/WcfRelays/authorizationRules | klíče listkey |
| Microsoft.SaaS/saasresources | listaccesstoken |
| Microsoft.Search/searchServices | listAdminKeys |
| Microsoft.Search/searchServices | listQueryKeys |
| Microsoft.ServiceBus/namespaces/authorizationRules | klíče listkey |
| Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules | klíče listkey |
| Microsoft.ServiceBus/namespaces/queues/authorizationRules | klíče listkey |
| Microsoft.ServiceBus/namespaces/topics/authorizationRules | klíče listkey |
| Microsoft.SignalRService/SignalR | listFeatures |
| Microsoft.SignalRService/SignalR | klíče listkey |
| Microsoft.Storage/storageAccounts | listAccountSas |
| Microsoft.Storage/storageAccounts | klíče listkey |
| Microsoft.Storage/storageAccounts | listServiceSas |
| Microsoft.StorSimple/managers/devices | listFailoverSets |
| Microsoft.StorSimple/managers/devices | listFailoverTargets |
| Microsoft.StorSimple/managers | listActivationKey |
| Microsoft.StorSimple/managers | listPublicEncryptionKey |
| microsoft.web/apimanagementaccounts/apis/connections | listconnectionkeys |
| microsoft.web/apimanagementaccounts/apis/connections | listsecrets |
| Microsoft.Web/connectionGateways | ListStatus |
| microsoft.web/connections | listconsentlinks |
| Microsoft.Web/customApis | listWsdlInterfaces |
| microsoft.web/locations | listwsdlinterfaces |
| microsoft.web/sites/backups | list |
| Microsoft.Web/sites/config | list |
| microsoft.web/sites/functions | listsecrets |
| microsoft.web/sites/hybridconnectionnamespaces/relays | klíče listkey |
| microsoft.web/sites | listsyncfunctiontriggerstatus |
| microsoft.web/sites/slots/backups | list |
| Microsoft.Web/sites/slots/config | list |
| microsoft.web/sites/slots/functions | listsecrets |


### <a name="return-value"></a>Návratová hodnota

Seznam funkcí, které používáte, se liší vráceného objektu. Například klíče Listkey pro účet služby storage vrátí v následujícím formátu:

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

Další seznam funkce mají různé formáty návratový. Pokud chcete zobrazit formát funkce, zahrňte do část Outputs následujícím jak je znázorněno v příkladu šablony. 

### <a name="remarks"></a>Poznámky

[SAS účtu seznamu](/rest/api/storagerp/storageaccounts#StorageAccounts_ListAccountSAS) operace vyžaduje parametrů těla zprávy požadavku, jako jsou *signedExpiry*. Chcete-li použít tuto funkci v šabloně, poskytnout objekt spolu s textem hodnoty parametrů.

Pokud chcete zjistit, které typy prostředků máte seznam operace, máte následující možnosti:

* Zobrazení [operace REST API](/rest/api/) pro poskytovatele prostředků a najít seznamu operací. Například účty úložiště mají [klíče Listkey operace](/rest/api/storagerp/storageaccounts#StorageAccounts_ListKeys).
* Použití [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) rutiny Powershellu. Následující příklad získá všechny seznam operací pro účty úložiště:

  ```powershell
  Get-AzProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* Použijte následující příkaz rozhraní příkazového řádku Azure k filtrování pouze operace výpisu:

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

Zadat zdroj podle použití názvu prostředku nebo [funkce resourceId](#resourceid). Při použití této funkce do stejné šablony, který se nasazuje odkazovaných prostředků, použijte název prostředku.

### <a name="example"></a>Příklad:

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/listkeys.json) ukazuje, jak vrátit primární a sekundární klíče z účtu úložiště v části výstupů. Také vrátí token SAS pro účet úložiště. Chcete-li získat tento token, předá objekt listAccountSas funkce. Tento příklad je určen pro použití funkce seznamu. Obvykle můžete by pomocí tokenu SAS v hodnotě prostředků místo vrátit jako výstupní hodnoty. Výstupní hodnoty jsou uloženy v historii nasazení a nejsou zabezpečené. Je nutné zadat vypršení platnosti čas v budoucnosti pro na úspěšné nasazení.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagename": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "southcentralus"
        },
        "accountSasProperties": {
            "type": "object",
            "defaultValue": {
                "signedServices": "b",
                "signedPermission": "r",
                "signedExpiry": "2018-08-20T11:00:00Z",
                "signedResourceTypes": "s"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2018-02-01",
            "name": "[parameters('storagename')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Storage/storageAccounts",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "properties": {
                "supportsHttpsTrafficOnly": false,
                "accessTier": "Hot",
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        },
                        "file": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                }
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "keys": {
            "type": "object",
            "value": "[listKeys(parameters('storagename'), '2018-02-01')]"
        },
        "accountSAS": {
            "type": "object",
            "value": "[listAccountSas(parameters('storagename'), '2018-02-01', parameters('accountSasProperties'))]"
        }
    }
}
``` 

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/listkeys.json --parameters storagename=<your-storage-account>
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/listkeys.json -storagename <your-storage-account>
```

<a id="providers" />

## <a name="providers"></a>Zprostředkovatelé
`providers(providerNamespace, [resourceType])`

Vrátí informace o zprostředkovateli prostředků a její podporované typy prostředků. Pokud nezadáte typ prostředku, funkce vrátí všechny podporované typy pro poskytovatele prostředků.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| providerNamespace |Ano |řetězec |Namespace poskytovatele |
| Typ prostředku |Ne |řetězec |Typ prostředku v rámci zadaného oboru názvů. |

### <a name="return-value"></a>Návratová hodnota

Každý podporovaný typ se vrátí v následujícím formátu: 

```json
{
    "resourceType": "{name of resource type}",
    "locations": [ all supported locations ],
    "apiVersions": [ all supported API versions ]
}
```

Pole řazení vrácené hodnoty není zaručena.

### <a name="example"></a>Příklad:

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json) ukazuje, jak použít funkci zprostředkovatele:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "providerNamespace": {
            "type": "string"
        },
        "resourceType": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "providerOutput": {
            "value": "[providers(parameters('providerNamespace'), parameters('resourceType'))]",
            "type" : "object"
        }
    }
}
```

Pro **Microsoft.Web** poskytovatele prostředků a **lokality** typ prostředku v předchozím příkladu vrátí objekt v následujícím formátu:

```json
{
  "resourceType": "sites",
  "locations": [
    "South Central US",
    "North Europe",
    "West Europe",
    "Southeast Asia",
    ...
  ],
  "apiVersions": [
    "2016-08-01",
    "2016-03-01",
    "2015-08-01-preview",
    "2015-08-01",
    ...
  ]
}
```

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/providers.json --parameters providerNamespace=Microsoft.Web resourceType=sites
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/providers.json -providerNamespace Microsoft.Web -resourceType sites
```

<a id="reference" />

## <a name="reference"></a>Referenční dokumentace
`reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])`

Vrátí objekt představující stav prostředků modulu runtime.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| resourceName nebo resourceIdentifier |Ano |řetězec |Název nebo identifikátor prostředku. |
| apiVersion |Ne |řetězec |Verze rozhraní API zadaný prostředek. Zahrnout tento parametr, pokud prostředek není zřízený v rámci stejné šablony. Obvykle ve formátu **rrrr mm-dd**. |
| "Úplné" |Ne |řetězec |Hodnota, která určuje, jestli se má vrátit objekt úplné prostředku. Pokud nezadáte `'Full'`, je vrácen pouze objekt vlastnosti prostředku. Úplný objekt obsahuje hodnoty, jako je ID prostředku a umístění. |

### <a name="return-value"></a>Návratová hodnota

Každý typ prostředku vrátí různé vlastnosti pro odkaz na funkci. Funkce nevrací předdefinovaný formát. Vrácené hodnoty liší také podle toho, jestli zadaná úplný objekt. Pokud chcete zobrazit vlastnosti pro typ prostředku, vrátí objekt v část Outputs následujícím, jak je znázorněno v příkladu.

### <a name="remarks"></a>Poznámky

Odkaz na funkci načte běhový stav již nasazený prostředek nebo prostředek nasazený v aktuální šablony. Tento článek ukazuje příklady pro oba scénáře. Při odkazování na prostředek v aktuální šablony, zadejte pouze název prostředku jako parametr. Při odkazování na prostředek už nasazenou, zadejte ID prostředku a verze rozhraní API pro prostředek. Můžete určit platná verze rozhraní API pro prostředek v [referenčními informacemi k šablonám](/azure/templates/).

Odkaz na funkci jde použít jenom ve vlastnosti definice prostředku a část Outputs následujícím šablony nebo nasazení.

Pomocí funkce odkaz na implicitně deklarujete, jeden prostředek závisí na jiný prostředek, pokud je oba odkazované prostředky poskytnutém v rámci stejné šablony a reference na prostředek má název (není ID prostředku). Není nutné použít také vlastnost dependsOn. Funkce není vyhodnocen, dokud odkazované prostředky dokončení nasazení.

Pokud chcete zobrazit názvy a hodnoty pro typ prostředku, vytvořte šablonu, která vrátí objekt v část outputs. Pokud máte existující prostředek tohoto typu, šablony vrátí objekt bez nutnosti nasazovat žádné nové prostředky. 

Obvykle se používají **odkaz** funkci vrátíte konkrétní hodnoty z objektu, jako je identifikátor URI koncového bodu objektu blob nebo plně kvalifikovaný název domény.

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
        "type" : "string"
    },
    "FQDN": {
        "value": "[reference(concat('Microsoft.Network/publicIPAddresses/', parameters('ipAddressName')), '2016-03-30').dnsSettings.fqdn]",
        "type" : "string"
    }
}
```

Použití `'Full'` Pokud potřebujete hodnoty prostředků, které nejsou součástí vlastnosti schématu. Například pokud chcete nastavit zásady přístupu trezoru klíčů, získáte vlastnosti identity pro virtuální počítač.

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "properties": {
    "tenantId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.tenantId]",
    "accessPolicies": [
      {
        "tenantId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.tenantId]",
        "objectId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.principalId]",
        "permissions": {
          "keys": [
            "all"
          ],
          "secrets": [
            "all"
          ]
        }
      }
    ],
    ...
```

Úplný příklad předchozí šablonu postupem, naleznete v tématu [Windows do služby Key Vault](https://github.com/rjmax/AzureSaturday/blob/master/Demo02.ManagedServiceIdentity/demo08.msiWindowsToKeyvault.json). Podobný příklad je k dispozici pro [Linux](https://github.com/rjmax/AzureSaturday/blob/master/Demo02.ManagedServiceIdentity/demo07.msiLinuxToArm.json).

### <a name="example"></a>Příklad:

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json) nasadí prostředku a odkazuje na tento prostředek.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageAccountName": { 
          "type": "string"
      }
  },
  "resources": [
    {
      "name": "[parameters('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {
      "referenceOutput": {
          "type": "object",
          "value": "[reference(parameters('storageAccountName'))]"
      },
      "fullReferenceOutput": {
        "type": "object",
        "value": "[reference(parameters('storageAccountName'), '2016-12-01', 'Full')]"
      }
    }
}
``` 

V předchozím příkladu vrátí dva objekty. Objekt vlastnosti je v následujícím formátu:

```json
{
   "creationTime": "2017-10-09T18:55:40.5863736Z",
   "primaryEndpoints": {
     "blob": "https://examplestorage.blob.core.windows.net/",
     "file": "https://examplestorage.file.core.windows.net/",
     "queue": "https://examplestorage.queue.core.windows.net/",
     "table": "https://examplestorage.table.core.windows.net/"
   },
   "primaryLocation": "southcentralus",
   "provisioningState": "Succeeded",
   "statusOfPrimary": "available",
   "supportsHttpsTrafficOnly": false
}
```

Úplný objekt je v následujícím formátu:

```json
{
  "apiVersion":"2016-12-01",
  "location":"southcentralus",
  "sku": {
    "name":"Standard_LRS",
    "tier":"Standard"
  },
  "tags":{},
  "kind":"Storage",
  "properties": {
    "creationTime":"2017-10-09T18:55:40.5863736Z",
    "primaryEndpoints": {
      "blob":"https://examplestorage.blob.core.windows.net/",
      "file":"https://examplestorage.file.core.windows.net/",
      "queue":"https://examplestorage.queue.core.windows.net/",
      "table":"https://examplestorage.table.core.windows.net/"
    },
    "primaryLocation":"southcentralus",
    "provisioningState":"Succeeded",
    "statusOfPrimary":"available",
    "supportsHttpsTrafficOnly":false
  },
  "subscriptionId":"<subscription-id>",
  "resourceGroupName":"functionexamplegroup",
  "resourceId":"Microsoft.Storage/storageAccounts/examplestorage",
  "referenceApiVersion":"2016-12-01",
  "condition":true,
  "isConditionTrue":true,
  "isTemplateResource":false,
  "isAction":false,
  "provisioningOperation":"Read"
}
```

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/referencewithstorage.json --parameters storageAccountName=<your-storage-account>
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/referencewithstorage.json -storageAccountName <your-storage-account>
```

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/reference.json) odkazuje na účet úložiště, který není nasazený v této šabloně. Účet úložiště už existuje v rámci stejného předplatného.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageResourceGroup": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "ExistingStorage": {
            "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]",
            "type": "object"
        }
    }
}
```

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/reference.json --parameters storageResourceGroup=<rg-for-storage> storageAccountName=<your-storage-account>
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/reference.json -storageResourceGroup <rg-for-storage> -storageAccountName <your-storage-account>
```

<a id="resourcegroup" />

## <a name="resourcegroup"></a>Skupina prostředků
`resourceGroup()`

Vrátí objekt představující aktuální skupinu prostředků. 

### <a name="return-value"></a>Návratová hodnota

Vrácený objekt je v následujícím formátu:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

### <a name="remarks"></a>Poznámky

`resourceGroup()` Funkci nelze použít v šabloně, která je [nasazené na úrovni předplatného](deploy-to-subscription.md). To jde použít jenom v šablonách, které jsou nasazeny do skupiny prostředků.

K vytváření prostředků ve stejném umístění jako skupina prostředků se běžně používá funkci skupina prostředků. Následující příklad používá umístění skupiny prostředků přiřadit umístění pro webový server.

```json
"resources": [
   {
      "apiVersion": "2016-08-01",
      "type": "Microsoft.Web/sites",
      "name": "[parameters('siteName')]",
      "location": "[resourceGroup().location]",
      ...
   }
]
```

### <a name="example"></a>Příklad:

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json) vrátí vlastnosti skupiny prostředků.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "resourceGroupOutput": {
            "value": "[resourceGroup()]",
            "type" : "object"
        }
    }
}
```

V předchozím příkladu vrátí objekt v následujícím formátu:

```json
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/examplegroup",
  "name": "examplegroup",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/resourcegroup.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/resourcegroup.json 
```

<a id="resourceid" />

## <a name="resourceid"></a>resourceId
`resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2]...)`

Vrací jedinečný identifikátor prostředku. Tuto funkci použít, když název prostředku je nejednoznačný nebo není zřízené v rámci stejné šablony. 

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| subscriptionId |Ne |řetězec (ve formátu identifikátoru GUID) |Výchozí hodnota je aktuálním předplatném. Tuto hodnotu zadejte, když budete chtít načíst prostředek v jiném předplatném. |
| resourceGroupName |Ne |řetězec |Výchozí hodnota je aktuální skupinu prostředků. Tuto hodnotu zadejte, když budete chtít načíst prostředek v jiné skupině prostředků. |
| Typ prostředku |Ano |řetězec |Typ prostředku včetně obor názvů zprostředkovatele prostředků. |
| resourceName1 |Ano |řetězec |Název prostředku. |
| resourceName2 |Ne |řetězec |Další zdroje segment názvu Pokud vnořených prostředků. |

### <a name="return-value"></a>Návratová hodnota

Tento identifikátor se vrátí v následujícím formátu:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Poznámky

Při použití s [nasazení na úrovni předplatného](deploy-to-subscription.md), `resourceId()` funkce se dá načíst jenom ID prostředků, které jsou nasazené na této úrovni. Například můžete získat ID definice zásady nebo definice role, ale ne ID účtu úložiště. Nasazení do skupiny prostředků opak má hodnotu true. Nelze získat ID prostředku prostředky nasazené na úrovni předplatného.

Hodnoty parametrů, které jste zadali závisí na tom, jestli prostředek je ve stejného předplatného a skupiny prostředků jako aktuální nasazení. Pokud chcete získat ID prostředku účtu úložiště ve stejném předplatném a skupině prostředků, použijte:

```json
"[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]"
```

Chcete-li získat ID prostředku účtu úložiště ve stejném předplatném, ale jinou skupinu prostředků, použijte:

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

Pokud chcete získat ID prostředku účtu úložiště v jiném předplatném a skupině prostředků, použijte:

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

Chcete-li získat ID prostředku pro databázi v jiné skupině prostředků, použijte:

```json
"[resourceId('otherResourceGroup', 'Microsoft.SQL/servers/databases', parameters('serverName'), parameters('databaseName'))]"
```

Při nasazení v oboru předplatného, získejte ID prostředků prostředků na úrovni předplatného, použijte:

```json
"[resourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
```

Často je potřeba tuto funkci použít, pokud používáte účet úložiště nebo virtuální sítě ve skupině prostředků alternativní. Následující příklad ukazuje, jak lze prostředek ze skupiny pro externí zdroj jednoduše použít:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualNetworkName": {
          "type": "string"
      },
      "virtualNetworkResourceGroup": {
          "type": "string"
      },
      "subnet1Name": {
          "type": "string"
      },
      "nicName": {
          "type": "string"
      }
  },
  "variables": {
      "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
      "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
  },
  "resources": [
  {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
          "ipConfigurations": [{
              "name": "ipconfig1",
              "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "subnet": {
                      "id": "[variables('subnet1Ref')]"
                  }
              }
          }]
       }
  }]
}
```

### <a name="example"></a>Příklad:

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json) vrátí ID prostředku účtu úložiště ve skupině prostředků:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "sameRGOutput": {
            "value": "[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentRGOutput": {
            "value": "[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentSubOutput": {
            "value": "[resourceId('11111111-1111-1111-1111-111111111111', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "nestedResourceOutput": {
            "value": "[resourceId('Microsoft.SQL/servers/databases', 'serverName', 'databaseName')]",
            "type" : "string"
        }
    }
}
```

Výstup z předchozího příkladu s výchozími hodnotami je:

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| sameRGOutput | Řetězec | /subscriptions/{Current-Sub-ID}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | Řetězec | /subscriptions/{Current-Sub-ID}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | Řetězec | /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | Řetězec | /subscriptions/{Current-Sub-ID}/resourceGroups/examplegroup/providers/Microsoft.SQL/Servers/servername/Databases/databaseName |

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/resourceid.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/resourceid.json 
```

<a id="subscription" />

## <a name="subscription"></a>předplatné
`subscription()`

Vrátí podrobnosti o předplatném pro aktuální nasazení. 

### <a name="return-value"></a>Návratová hodnota

Funkce vrátí v následujícím formátu:

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="example"></a>Příklad:

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json) ukazuje volal funkci předplatného v část outputs. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[subscription()]",
            "type" : "object"
        }
    }
}
```

Pokud chcete nasadit šablonu tento příklad pomocí Azure CLI, použijte:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/subscription.json
```

Pokud chcete nasadit tento příklad šablony pomocí prostředí PowerShell, použijte:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/subscription.json 
```

## <a name="next-steps"></a>Další postup
* Popis části šablony Azure Resource Manageru najdete v tématu [šablon pro vytváření Azure Resource Manageru](resource-group-authoring-templates.md).
* Chcete-li sloučit několik šablon, přečtěte si téma [použití propojených šablon s Azure Resource Managerem](resource-group-linked-templates.md).
* K iteraci zadaného počtu opakování při vytváření konkrétní typ prostředku, naleznete v tématu [vytvořit více instancí prostředku v Azure Resource Manageru](resource-group-create-multiple.md).
* Postup nasazení šablony, které jste vytvořili, najdete v sekci [nasazení aplikace pomocí šablony Azure Resource Manageru](resource-group-template-deploy.md).

