---
title: Funkce šablon – prostředky
description: Popisuje funkce pro použití v šabloně Azure Resource Manageru k načtení hodnoty o prostředcích.
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: 1b860876b0d8967a6a3f90c7bb68f20d6c442109
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513860"
---
# <a name="resource-functions-for-azure-resource-manager-templates"></a>Prostředek funkce pro šablony Azure Resource Manageru

Resource Manager poskytuje následující funkce pro načtení prostředků hodnot:

* [extensionResourceId](#extensionresourceid)
* [seznam *](#list)
* [Zprostředkovatelé](#providers)
* [Referenční dokumentace](#reference)
* [resourceGroup](#resourcegroup)
* [ID prostředku](#resourceid)
* [předplatné](#subscription)
* [subscriptionResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

Chcete-li získat hodnoty z parametrů, proměnných nebo aktuální nasazení, přečtěte si téma [funkce hodnotu nasazení](template-functions-deployment.md).

## <a name="extensionresourceid"></a>extensionResourceId

```json
extensionResourceId(resourceId, resourceType, resourceName1, [resourceName2], ...)
```

Vrátí ID prostředku pro [prostředek rozšíření](../management/extension-resource-types.md), což je typ prostředku, který se použije na jiný prostředek, aby se mohl přidat k jeho funkcím.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| resourceId |Ano |string |ID prostředku prostředku, na který se prostředek rozšíření používá |
| resourceType |Ano |string |Typ prostředku včetně obor názvů zprostředkovatele prostředků. |
| resourceName1 |Ano |string |Název prostředku. |
| resourceName2 |Ne |string |Další segment názvu prostředku (v případě potřeby). |

Pokračujte v přidávání názvů prostředků jako parametrů, pokud typ prostředku obsahuje více segmentů.

### <a name="return-value"></a>Návratová hodnota

Základní formát ID prostředku vrácený touto funkcí je:

```json
{scope}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Segment oboru se liší podle prostředku, který se rozšiřuje.

Pokud se prostředek rozšíření použije u **prostředku**, ID prostředku se vrátí v následujícím formátu:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{baseResourceProviderNamespace}/{baseResourceType}/{baseResourceName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Pokud se prostředek rozšíření použije pro **skupinu prostředků**, formát je:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Pokud se prostředek rozšíření použije u **předplatného**, jedná se o tento formát:

```json
/subscriptions/{subscriptionId}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Pokud je prostředek rozšíření použit pro skupinu pro **správu**, formát je:

```json
/providers/Microsoft.Management/managementGroups/{managementGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

### <a name="extensionresourceid-example"></a>Příklad extensionResourceId

Následující příklad vrátí ID prostředku pro zámek skupiny prostředků.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "lockName":{
            "type": "string"
        }
    },
    "variables": {},
    "resources": [],
    "outputs": {
        "lockResourceId": {
            "type": "string",
            "value": "[extensionResourceId(resourceGroup().Id , 'Microsoft.Authorization/locks', parameters('lockName'))]"
        }
    }
}
```

<a id="listkeys" />
<a id="list" />

## <a name="list"></a>seznamu

```json
list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)
```

Syntaxe této funkce se liší podle názvu operací seznamu. Každá implementace vrací hodnoty pro typ prostředku, který podporuje operaci list. Název operace musí začínat na `list`. Některé běžné použití jsou `listKeys` a `listSecrets`. 

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| resourceName nebo resourceIdentifier |Ano |string |Jedinečný identifikátor prostředku. |
| apiVersion |Ano |string |Verze rozhraní API prostředku běhový stav. Obvykle ve formátu **rrrr mm-dd**. |
| functionValues |Ne |object | Objekt, který obsahuje hodnoty pro funkci. Tento objekt poskytují pouze pro funkce, které podporují přijímá objekt s hodnotami parametrů, jako například **listAccountSas** v účtu úložiště. Příklad předávání hodnot funkcí je uveden v tomto článku. | 

### <a name="valid-uses"></a>Platná použití

Funkce seznamu se dá použít jenom ve vlastnostech definice prostředků a v části výstupy šablony nebo nasazení. Při použití s [iterací vlastnosti](create-multiple-instances.md#property-iteration)můžete použít funkce seznamu pro `input`, protože je výraz přiřazen vlastnosti prostředku. Nelze je použít s `count`, protože počet musí být určen před vyřešením funkce seznamu.

### <a name="implementations"></a>Implementace

Možná použití seznamu * jsou uvedena v následující tabulce.

| Typ prostředku | Název funkce |
| ------------- | ------------- |
| Microsoft.AnalysisServices/servers | [listGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft.AppConfiguration/configurationStores | Klíče listkey |
| Microsoft. Automation/automationAccounts | [klíče Listkey](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.Batch/batchAccounts | [klíče listkey](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft. BatchAI/pracovní prostory/experimenty/úlohy | [listoutputfiles](/rest/api/batchai/jobs/listoutputfiles) |
| Microsoft. blockchain/blockchainMembers | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Microsoft. blockchain/blockchainMembers/transactionNodes | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Microsoft.Cache/redis | [klíče Listkey](/rest/api/redis/redis/listkeys) |
| Microsoft.CognitiveServices/accounts | [klíče Listkey](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Microsoft.ContainerRegistry/registries | [listBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/getbuildsourceuploadurl) |
| Microsoft.ContainerRegistry/registries | [listCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Microsoft.ContainerRegistry/registries | [listUsages](/rest/api/containerregistry/registries/listusages) |
| Microsoft.ContainerRegistry/registries/webhooks | [listEvents](/rest/api/containerregistry/webhooks/listevents) |
| Microsoft.ContainerRegistry/registries/runs | [listLogSasUrl](/rest/api/containerregistry/runs/getlogsasurl) |
| Microsoft.ContainerRegistry/registries/tasks | [listDetails](/rest/api/containerregistry/tasks/getdetails) |
| Microsoft.ContainerService/managedClusters | [listClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft.ContainerService/managedClusters | [listClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Microsoft.ContainerService/managedClusters/accessProfiles | [listCredential](/rest/api/aks/managedclusters/getaccessprofile) |
| Microsoft.DataBox/jobs | listCredentials |
| Microsoft.DataFactory/datafactories/gateways | listauthkeys |
| Microsoft. DataFactory/Factory/integrationruntimes | [listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft. DataLakeAnalytics/Accounts/storageAccounts/Containers | [listSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Microsoft. datashare/Accounts/shares | [listSynchronizations](/rest/api/datashare/shares/listsynchronizations) | 
| Microsoft. datashare/Accounts/shareSubscriptions | [listSourceShareSynchronizationSettings](/rest/api/datashare/sharesubscriptions/listsourcesharesynchronizationsettings) |
| Microsoft. datashare/Accounts/shareSubscriptions | [listSynchronizationDetails](/rest/api/datashare/sharesubscriptions/listsynchronizationdetails) |
| Microsoft. datashare/Accounts/shareSubscriptions | [listSynchronizations](/rest/api/datashare/sharesubscriptions/listsynchronizations) |
| Microsoft. Devices/iotHubs | [klíče listkey](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft. Devices/iotHubs/iotHubKeys | [klíče listkey](/rest/api/iothub/iothubresource/getkeysforkeyname) |
| Microsoft. Devices/provisioningServices/Keys | [klíče listkey](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft.Devices/provisioningServices | [klíče listkey](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft.DevTestLab/labs | [ListVhds](/rest/api/dtl/labs/listvhds) |
| Microsoft.DevTestLab/labs/schedules | [ListApplicable](/rest/api/dtl/schedules/listapplicable) |
| Microsoft.DevTestLab/labs/users/serviceFabrics | [ListApplicableSchedules](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft.DevTestLab/labs/virtualMachines | [ListApplicableSchedules](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft. DocumentDB/databaseAccounts | [listConnectionStrings](/rest/api/cosmos-db-resource-provider/databaseaccounts/listconnectionstrings) |
| Microsoft. DocumentDB/databaseAccounts | [klíče Listkey](/rest/api/cosmos-db-resource-provider/databaseaccounts/listkeys) |
| Microsoft.DomainRegistration | [listDomainRecommendations](/rest/api/appservice/domains/listrecommendations) |
| Microsoft. DomainRegistration/topLevelDomains | [listAgreements](/rest/api/appservice/topleveldomains/listagreements) |
| Microsoft. EventGrid/domény | [klíče Listkey](/rest/api/eventgrid/domains/listsharedaccesskeys) |
| Microsoft.EventGrid/topics | [klíče Listkey](/rest/api/eventgrid/topics/listsharedaccesskeys) |
| Microsoft. EventHub/obory názvů/autorizačních pravidel | [klíče listkey](/rest/api/eventhub/namespaces/listkeys) |
| Microsoft. EventHub/obory názvů/disasterRecoveryConfigs/autorizačních pravidel | [klíče listkey](/rest/api/eventhub/disasterrecoveryconfigs/listkeys) |
| Microsoft. EventHub/obory názvů/eventhubs/autorizačních pravidel | [klíče listkey](/rest/api/eventhub/eventhubs/listkeys) |
| Microsoft. ImportExport/Jobs | [listBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Microsoft. Kusto/clustery/databáze | [ListPrincipals](/rest/api/azurerekusto/databases/listprincipals) |
| Microsoft. LabServices/uživatelé | [ListEnvironments](/rest/api/labservices/globalusers/listenvironments) |
| Microsoft. LabServices/uživatelé | [ListLabs](/rest/api/labservices/globalusers/listlabs) |
| Microsoft. Logic/integrationAccounts/Agreements | [listContentCallbackUrl](/rest/api/logic/agreements/listcontentcallbackurl) |
| Microsoft. Logic/integrationAccounts/Assemblies | [listContentCallbackUrl](/rest/api/logic/integrationaccountassemblies/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listCallbackUrl](/rest/api/logic/integrationaccounts/getcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listKeyVaultKeys](/rest/api/logic/integrationaccounts/listkeyvaultkeys) |
| Microsoft. Logic/integrationAccounts/Maps | [listContentCallbackUrl](/rest/api/logic/maps/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/partners | [listContentCallbackUrl](/rest/api/logic/partners/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/schemas | [listContentCallbackUrl](/rest/api/logic/schemas/listcontentcallbackurl) |
| Microsoft.Logic/workflows | [listCallbackUrl](/rest/api/logic/workflows/listcallbackurl) |
| Microsoft.Logic/workflows | [listSwagger](/rest/api/logic/workflows/listswagger) |
| Microsoft. Logic/Workflows/běhy/Actions | [listExpressionTraces](/rest/api/logic/workflowrunactions/listexpressiontraces) |
| Microsoft. Logic/Workflows/běhy/Actions/opakování | [listExpressionTraces](/rest/api/logic/workflowrunactionrepetitions/listexpressiontraces) |
| Microsoft. Logic/Workflows/Triggers | [listCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Microsoft. Logic/Workflows/Versions/Triggers | [listCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Microsoft. MachineLearning/WebServices | [klíče listkey](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft. MachineLearning/pracovní prostory | listworkspacekeys |
| Microsoft. MachineLearningServices/pracovní prostory/výpočetní služby | [klíče Listkey](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listkeys) |
| Microsoft. MachineLearningServices/pracovní prostory/výpočetní služby | [listNodes](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listnodes) |
| Microsoft.MachineLearningServices/workspaces | [klíče Listkey](/rest/api/azureml/workspacesandcomputes/workspaces/listkeys) |
| Microsoft. Maps/Accounts | [klíče Listkey](/rest/api/maps-management/accounts/listkeys) |
| Microsoft. Media/MediaServices/Assets | [listContainerSas](/rest/api/media/assets/listcontainersas) |
| Microsoft. Media/MediaServices/Assets | [listStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Microsoft. Media/MediaServices/streamingLocators | [listContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft. Media/MediaServices/streamingLocators | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| Microsoft.Network/applicationSecurityGroups | listIpConfigurations |
| Microsoft. NotificationHubs/obory názvů/autorizačních pravidel | [klíče listkey](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft. NotificationHubs/obory názvů/NotificationHubs/autorizačních pravidel | [klíče listkey](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft.OperationalInsights/workspaces | [klíče Listkey](/rest/api/loganalytics/workspaces%202015-03-20/listkeys) |
| Microsoft.PolicyInsights/remediations | [listDeployments](/rest/api/policy-insights/remediations/listdeploymentsatresourcegroup) |
| Microsoft. Relay/obory názvů/autorizačních pravidel | [klíče listkey](/rest/api/relay/namespaces/listkeys) |
| Microsoft. Relay/obory názvů/disasterRecoveryConfigs/autorizačních pravidel | klíče listkey |
| Microsoft. Relay/obory názvů/HybridConnections/autorizačních pravidel | [klíče listkey](/rest/api/relay/hybridconnections/listkeys) |
| Microsoft. Relay/obory názvů/WcfRelays/autorizačních pravidel | [klíče listkey](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft.Search/searchServices | [listAdminKeys](/rest/api/searchmanagement/adminkeys/get) |
| Microsoft.Search/searchServices | [listQueryKeys](/rest/api/searchmanagement/querykeys/listbysearchservice) |
| Microsoft. ServiceBus/obory názvů/autorizačních pravidel | [klíče listkey](/rest/api/servicebus/namespaces/listkeys) |
| Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules | [klíče listkey](/rest/api/servicebus/disasterrecoveryconfigs/listkeys) |
| Microsoft. ServiceBus/obory názvů/Queues/autorizačních pravidel | [klíče listkey](/rest/api/servicebus/queues/listkeys) |
| Microsoft. ServiceBus/obory názvů/témata/autorizačních pravidel | [klíče listkey](/rest/api/servicebus/topics/listkeys) |
| Microsoft. SignalRService/Signaler | [klíče listkey](/rest/api/signalr/signalr/listkeys) |
| Microsoft.Storage/storageAccounts | [listAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft.Storage/storageAccounts | [klíče listkey](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft.Storage/storageAccounts | [listServiceSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Microsoft. StorSimple/manažeři/zařízení | [listFailoverSets](/rest/api/storsimple/devices/listfailoversets) |
| Microsoft. StorSimple/manažeři/zařízení | [listFailoverTargets](/rest/api/storsimple/devices/listfailovertargets) |
| Microsoft. StorSimple/– manažeři | [listActivationKey](/rest/api/storsimple/managers/getactivationkey) |
| Microsoft. StorSimple/– manažeři | [listPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Microsoft. Web/connectionGateways | ListStatus |
| microsoft.web/connections | listconsentlinks |
| Microsoft.Web/customApis | listWsdlInterfaces |
| microsoft.web/locations | listwsdlinterfaces |
| Microsoft. Web/apimanagementaccounts/API/Connections | listconnectionkeys |
| Microsoft. Web/apimanagementaccounts/API/Connections | listsecrets |
| microsoft.web/sites/backups | [list](/rest/api/appservice/webapps/listbackups) |
| Microsoft.Web/sites/config | [list](/rest/api/appservice/webapps/listconfigurations) |
| microsoft.web/sites/functions | [klíče listkey](/rest/api/appservice/webapps/listfunctionkeys)
| microsoft.web/sites/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| microsoft.web/sites/hybridconnectionnamespaces/relays | [klíče listkey](/rest/api/appservice/appserviceplans/listhybridconnectionkeys) |
| microsoft.web/sites | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| microsoft.web/sites/slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |
| microsoft.web/sites/slots/backups | [list](/rest/api/appservice/webapps/listbackupsslot) |
| Microsoft.Web/sites/slots/config | [list](/rest/api/appservice/webapps/listconfigurationsslot) |
| microsoft.web/sites/slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |

Pokud chcete zjistit, které typy prostředků máte seznam operace, máte následující možnosti:

* Zobrazení [operace REST API](/rest/api/) pro poskytovatele prostředků a najít seznamu operací. Například účty úložiště mají [klíče Listkey operace](/rest/api/storagerp/storageaccounts).
* Použijte rutinu PowerShellu [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) . Následující příklad získá všechny seznam operací pro účty úložiště:

  ```powershell
  Get-AzProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* Použijte následující příkaz rozhraní příkazového řádku Azure k filtrování pouze operace výpisu:

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

### <a name="return-value"></a>Návratová hodnota

Vrácený objekt se liší podle funkce seznamu, kterou používáte. Například klíče listkey pro účet úložiště vrátí následující formát:

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

Zadat zdroj podle použití názvu prostředku nebo [funkce resourceId](#resourceid). Při použití funkce list ve stejné šabloně, která nasazuje odkazovaný prostředek, použijte název prostředku.

Použijete-li funkci **list** v prostředku, který je podmíněně nasazen, je funkce vyhodnocena i v případě, že prostředek není nasazen. Pokud funkce **seznamu** odkazuje na prostředek, který neexistuje, zobrazí se chyba. Použijte funkci **if** a ujistěte se, že je funkce vyhodnocena pouze při nasazení prostředku. Podívejte se na [funkci IF](template-functions-logical.md#if) pro ukázkovou šablonu, která používá if a list s podmíněně nasazeným prostředkem.

### <a name="list-example"></a>Příklad seznamu

Následující [Ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/listkeys.json) ukazuje, jak vrátit primární a sekundární klíče z účtu úložiště v části výstupů. Také vrátí token SAS pro účet úložiště. 

Chcete-li získat token SAS, předejte objekt pro čas vypršení platnosti. Čas vypršení platnosti musí být v budoucnosti. Tento příklad je určen pro použití funkce seznamu. Obvykle můžete by pomocí tokenu SAS v hodnotě prostředků místo vrátit jako výstupní hodnoty. Výstupní hodnoty jsou uloženy v historii nasazení a nejsou zabezpečené.

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

## <a name="providers"></a>Zprostředkovatelé

```json
providers(providerNamespace, [resourceType])
```

Vrátí informace o zprostředkovateli prostředků a její podporované typy prostředků. Pokud nezadáte typ prostředku, funkce vrátí všechny podporované typy pro poskytovatele prostředků.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| providerNamespace |Ano |string |Namespace poskytovatele |
| resourceType |Ne |string |Typ prostředku v rámci zadaného oboru názvů. |

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

### <a name="providers-example"></a>Příklad poskytovatelů

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

## <a name="reference"></a>Referenční dokumentace

```json
reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])
```

Vrátí objekt představující stav prostředků modulu runtime.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| resourceName nebo resourceIdentifier |Ano |string |Název nebo identifikátor prostředku. Při odkazování na prostředek v aktuální šablony, zadejte pouze název prostředku jako parametr. Při odkazování na dříve nasazený prostředek zadejte ID prostředku. |
| apiVersion |Ne |string |Verze rozhraní API zadaný prostředek. Zahrnout tento parametr, pokud prostředek není zřízený v rámci stejné šablony. Obvykle ve formátu **rrrr mm-dd**. Platné verze rozhraní API pro váš prostředek naleznete v tématu [Reference k šabloně](/azure/templates/). |
| "Úplné" |Ne |string |Hodnota, která určuje, jestli se má vrátit objekt úplné prostředku. Pokud nezadáte `'Full'`, je vrácen pouze objekt vlastnosti prostředku. Úplný objekt obsahuje hodnoty, jako je ID prostředku a umístění. |

### <a name="return-value"></a>Návratová hodnota

Každý typ prostředku vrátí různé vlastnosti pro odkaz na funkci. Funkce nevrací předdefinovaný formát. Vrácené hodnoty liší také podle toho, jestli zadaná úplný objekt. Pokud chcete zobrazit vlastnosti pro typ prostředku, vrátí objekt v část Outputs následujícím, jak je znázorněno v příkladu.

### <a name="remarks"></a>Poznámky

Odkaz na funkci načte běhový stav již nasazený prostředek nebo prostředek nasazený v aktuální šablony. Tento článek ukazuje příklady pro oba scénáře.

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

### <a name="valid-uses"></a>Platná použití

Odkaz na funkci jde použít jenom ve vlastnosti definice prostředku a část Outputs následujícím šablony nebo nasazení. Při použití s [iterací vlastnosti](create-multiple-instances.md#property-iteration)můžete použít referenční funkci pro `input`, protože výraz je přiřazen vlastnosti prostředku. Nelze jej použít s `count`, protože počet musí být určen před vyřešením Referenční funkce.

V výstupech [vnořené šablony](linked-templates.md#nested-template) nelze použít referenční funkci pro vrácení prostředku, který jste nasadili ve vnořené šabloně. Místo toho použijte [propojenou šablonu](linked-templates.md#linked-template).

Použijete-li **referenční** funkci v prostředku, který je podmíněně nasazen, je funkce vyhodnocena i v případě, že prostředek není nasazen.  Pokud **odkazovaná** funkce odkazuje na prostředek, který neexistuje, zobrazí se chyba. Použijte funkci **if** a ujistěte se, že je funkce vyhodnocena pouze při nasazení prostředku. Podívejte se na [funkci IF](template-functions-logical.md#if) pro ukázkovou šablonu, která používá if a odkaz s podmíněně nasazeným prostředkem.

### <a name="implicit-dependency"></a>Implicitní závislost

Pomocí funkce odkaz na implicitně deklarujete, jeden prostředek závisí na jiný prostředek, pokud je oba odkazované prostředky poskytnutém v rámci stejné šablony a reference na prostředek má název (není ID prostředku). Není nutné použít také vlastnost dependsOn. Funkce není vyhodnocen, dokud odkazované prostředky dokončení nasazení.

### <a name="resource-name-or-identifier"></a>Název nebo identifikátor prostředku

Když odkazujete na prostředek, který je nasazený ve stejné šabloně, zadejte název prostředku.

```json
"value": "[reference(parameters('storageAccountName'))]"
```

Když odkazujete na prostředek, který není nasazený ve stejné šabloně, zadejte ID prostředku.

```json
"value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

Aby nedocházelo k nejednoznačnosti prostředků, na které odkazujete, můžete zadat plně kvalifikovaný název prostředku.

```json
"value": "[reference(concat('Microsoft.Network/publicIPAddresses/', parameters('ipAddressName')))]"
```

Při sestavování plně kvalifikovaného odkazu na prostředek, pořadí pro kombinování segmentů z typu a název není pouhým zřetězením obou. Místo toho je nutné po oboru názvů použít sekvenci dvojic *typů a názvů* z nejméně specifických na nejvíc:

**{Resource-Provider-Namespace}/{Parent-Resource-Type}/{Parent-Resource-Name} [/{Child-Resource-Type}/{Child-Resource-Name}]**

Například:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` je správné `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` není správné.

### <a name="get-managed-identity"></a>Získat spravovanou identitu

[Spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md) jsou [typy prostředků rozšíření](../management/extension-resource-types.md) , které se pro některé prostředky vytvoří implicitně. Vzhledem k tomu, že spravovaná identita není explicitně definována v šabloně, musíte odkazovat na prostředek, na který je identita použita. Pomocí `Full` můžete získat všechny vlastnosti, včetně implicitně vytvořené identity.

Pokud například chcete získat ID tenanta pro spravovanou identitu, která se používá pro sadu škálování virtuálního počítače, použijte:

```json
"tenantId": "[reference(concat('Microsoft.Compute/virtualMachineScaleSets/',  variables('vmNodeType0Name')), variables('vmssApiVersion'), 'Full').Identity.tenantId]"
```

### <a name="reference-example"></a>Příklad odkazu

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

## <a name="resourcegroup"></a>resourceGroup

```json
resourceGroup()
```

Vrátí objekt představující aktuální skupinu prostředků. 

### <a name="return-value"></a>Návratová hodnota

Vrácený objekt je v následujícím formátu:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "{resourceGroupLocation}",
  "managedBy": "{identifier-of-managing-resource}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

Vlastnost **ManagedBy** je vrácena pouze pro skupiny prostředků, které obsahují prostředky, které jsou spravovány jinou službou. Pro spravované aplikace, datacihly a AKS je hodnota vlastnosti ID prostředku správy prostředku.

### <a name="remarks"></a>Poznámky

Funkci `resourceGroup()` nelze použít v šabloně, která je [nasazena na úrovni předplatného](deploy-to-subscription.md). Dá se použít jenom v šablonách, které se nasazují do skupiny prostředků. V [propojené nebo vnořené šabloně (s vnitřním rozsahem)](linked-templates.md) , která cílí na skupinu prostředků, můžete použít funkci `resourceGroup()`, a to i v případě, že je nadřazená šablona nasazena do předplatného. V takovém případě je propojená nebo vnořená šablona nasazena na úrovni skupiny prostředků. Další informace o cílení skupiny prostředků v nasazení na úrovni předplatného najdete v tématu [nasazení prostředků Azure do více než jednoho předplatného nebo skupiny prostředků](cross-resource-group-deployment.md).

K vytváření prostředků ve stejném umístění jako skupina prostředků se běžně používá funkci skupina prostředků. Následující příklad používá umístění skupiny prostředků pro výchozí hodnotu parametru.

```json
"parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
}
```

K použití značek ze skupiny prostředků na prostředek můžete také použít funkci Resource. Další informace najdete v tématu [použití značek ze skupiny prostředků](../management/tag-resources.md#apply-tags-from-resource-group).

Při použití vnořených šablon k nasazení do více skupin prostředků můžete zadat obor pro vyhodnocení funkce Resource. Další informace najdete v tématu [nasazení prostředků Azure do více než jednoho předplatného nebo skupiny prostředků](cross-resource-group-deployment.md).

### <a name="resource-group-example"></a>Příklad skupiny prostředků

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
  "type":"Microsoft.Resources/resourceGroups",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

## <a name="resourceid"></a>resourceId

```json
resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2], ...)
```

Vrací jedinečný identifikátor prostředku. Tuto funkci použít, když název prostředku je nejednoznačný nebo není zřízené v rámci stejné šablony. 

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| subscriptionId |Ne |řetězec (ve formátu identifikátoru GUID) |Výchozí hodnota je aktuálním předplatném. Tuto hodnotu zadejte, když budete chtít načíst prostředek v jiném předplatném. |
| resourceGroupName |Ne |string |Výchozí hodnota je aktuální skupinu prostředků. Tuto hodnotu zadejte, když budete chtít načíst prostředek v jiné skupině prostředků. |
| resourceType |Ano |string |Typ prostředku včetně obor názvů zprostředkovatele prostředků. |
| resourceName1 |Ano |string |Název prostředku. |
| resourceName2 |Ne |string |Další segment názvu prostředku (v případě potřeby). |

Pokračujte v přidávání názvů prostředků jako parametrů, pokud typ prostředku obsahuje více segmentů.

### <a name="return-value"></a>Návratová hodnota

ID prostředku se vrátí v následujícím formátu:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Při použití v [nasazení na úrovni předplatného](deploy-to-subscription.md)se ID prostředku vrátí v následujícím formátu:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Chcete-li získat ID v jiných formátech, přečtěte si:

* [extensionResourceId](#extensionresourceid)
* [subscriptionResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

### <a name="remarks"></a>Poznámky

Počet parametrů, které zadáte, se liší v závislosti na tom, zda se jedná o prostředek nadřazeného nebo podřízeného prostředku a zda je prostředek ve stejném předplatném nebo skupině prostředků.

Pokud chcete získat ID prostředku nadřazeného prostředku ve stejném předplatném a skupině prostředků, zadejte typ a název prostředku.

```json
"[resourceId('Microsoft.ServiceBus/namespaces', 'namespace1')]"
```

Pokud chcete získat ID prostředku pro podřízený prostředek, věnujte pozornost počtu segmentů v typu prostředku. Zadejte název prostředku pro každý segment typu prostředku. Název segmentu odpovídá prostředku, který existuje pro danou část hierarchie.

```json
"[resourceId('Microsoft.ServiceBus/namespaces/queues/authorizationRules', 'namespace1', 'queue1', 'auth1')]"
```

Pokud chcete získat ID prostředku ve stejném předplatném, ale v jiné skupině prostředků, zadejte název skupiny prostředků.

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts', 'examplestorage')]"
```

Pokud chcete získat ID prostředku v jiném předplatném a skupině prostředků, zadejte ID předplatného a název skupiny prostředků.

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
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
      "subnet1Ref": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnet1Name'))]"
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

### <a name="resource-id-example"></a>Příklad ID prostředku

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

| Name (Název) | Typ | Hodnota |
| ---- | ---- | ----- |
| sameRGOutput | Řetězec | /subscriptions/{Current-Sub-ID}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | Řetězec | /subscriptions/{Current-Sub-ID}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | Řetězec | /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | Řetězec | /subscriptions/{Current-Sub-ID}/resourceGroups/examplegroup/providers/Microsoft.SQL/Servers/servername/Databases/databaseName |

## <a name="subscription"></a>předplatné

```json
subscription()
```

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

### <a name="remarks"></a>Poznámky

Při použití vnořených šablon k nasazení do více předplatných můžete zadat obor pro vyhodnocení funkce předplatného. Další informace najdete v tématu [nasazení prostředků Azure do více než jednoho předplatného nebo skupiny prostředků](cross-resource-group-deployment.md).

### <a name="subscription-example"></a>Příklad předplatného

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

## <a name="subscriptionresourceid"></a>subscriptionResourceId

```json
subscriptionResourceId([subscriptionId], resourceType, resourceName1, [resourceName2], ...)
```

Vrátí jedinečný identifikátor prostředku nasazeného na úrovni předplatného.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| subscriptionId |Ne |řetězec (ve formátu GUID) |Výchozí hodnota je aktuálním předplatném. Tuto hodnotu zadejte, když budete chtít načíst prostředek v jiném předplatném. |
| resourceType |Ano |string |Typ prostředku včetně obor názvů zprostředkovatele prostředků. |
| resourceName1 |Ano |string |Název prostředku. |
| resourceName2 |Ne |string |Další segment názvu prostředku (v případě potřeby). |

Pokračujte v přidávání názvů prostředků jako parametrů, pokud typ prostředku obsahuje více segmentů.

### <a name="return-value"></a>Návratová hodnota

Tento identifikátor se vrátí v následujícím formátu:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Poznámky

Pomocí této funkce lze získat ID prostředku pro prostředky, které jsou [nasazeny do předplatného](deploy-to-subscription.md) , nikoli jako skupinu prostředků. Vrácené ID se liší od hodnoty vrácené funkcí [ResourceID](#resourceid) , protože nezahrnuje hodnotu skupiny prostředků.

### <a name="subscriptionresourceid-example"></a>Příklad subscriptionResourceID

Následující šablona přiřadí vestavěnou roli. Můžete ho nasadit buď do skupiny prostředků, nebo do předplatného. Pomocí funkce subscriptionResourceId získá ID prostředku pro předdefinované role.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "Owner": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

## <a name="tenantresourceid"></a>tenantResourceId

```json
tenantResourceId(resourceType, resourceName1, [resourceName2], ...)
```

Vrátí jedinečný identifikátor prostředku nasazeného na úrovni tenanta.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| resourceType |Ano |string |Typ prostředku včetně obor názvů zprostředkovatele prostředků. |
| resourceName1 |Ano |string |Název prostředku. |
| resourceName2 |Ne |string |Další segment názvu prostředku (v případě potřeby). |

Pokračujte v přidávání názvů prostředků jako parametrů, pokud typ prostředku obsahuje více segmentů.

### <a name="return-value"></a>Návratová hodnota

Tento identifikátor se vrátí v následujícím formátu:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Poznámky

Pomocí této funkce získáte ID prostředku pro prostředek, který je nasazený do tenanta. Vrácené ID se liší od hodnot vrácených jinými funkcemi ID prostředků bez zahrnutí skupin prostředků nebo hodnot předplatného.

## <a name="next-steps"></a>Další kroky

* Popis části šablony Azure Resource Manageru najdete v tématu [šablon pro vytváření Azure Resource Manageru](template-syntax.md).
* Chcete-li sloučit několik šablon, přečtěte si téma [použití propojených šablon s Azure Resource Managerem](linked-templates.md).
* K iteraci zadaného počtu opakování při vytváření konkrétní typ prostředku, naleznete v tématu [vytvořit více instancí prostředku v Azure Resource Manageru](create-multiple-instances.md).
* Postup nasazení šablony, které jste vytvořili, najdete v sekci [nasazení aplikace pomocí šablony Azure Resource Manageru](deploy-powershell.md).

