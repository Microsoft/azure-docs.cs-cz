---
title: Azure Resource Manager funkce šablon – prostředky | Microsoft Docs
description: Popisuje funkce, které se použijí v šabloně Azure Resource Manager k načtení hodnot o prostředcích.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: tomfitz
ms.openlocfilehash: cf791bd262849cd93a155a19ade8f8fc377f8da6
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72894201"
---
# <a name="resource-functions-for-azure-resource-manager-templates"></a>Funkce prostředků pro šablony Azure Resource Manager

Správce prostředků poskytuje následující funkce pro získání hodnot prostředků:

* [extensionResourceId](#extensionresourceid)
* [seznamu](#list)
* [dodavateli](#providers)
* [odkaz](#reference)
* [resourceGroup](#resourcegroup)
* [Prostředku](#resourceid)
* [předplatné](#subscription)
* [subscriptionResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

Chcete-li získat hodnoty z parametrů, proměnných nebo aktuálního nasazení, přečtěte si téma [funkce hodnot nasazení](resource-group-template-functions-deployment.md).

## <a name="extensionresourceid"></a>extensionResourceId

```json
extensionResourceId(resourceId, resourceType, resourceName1, [resourceName2], ...)
```

Vrátí ID prostředku pro [prostředek rozšíření](extension-resource-types.md), což je typ prostředku, který se použije na jiný prostředek, aby se mohl přidat k jeho funkcím.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| resourceId |Ano |string |ID prostředku prostředku, na který se prostředek rozšíření používá |
| resourceType |Ano |string |Typ prostředku včetně oboru názvů poskytovatele prostředků |
| resourceName1 |Ano |string |Název prostředku |
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
| resourceName nebo resourceIdentifier |Ano |string |Jedinečný identifikátor prostředku |
| apiVersion |Ano |string |Verze rozhraní API stavu modulu runtime prostředku Obvykle ve formátu **yyyy-MM-DD**. |
| functionValues |Ne |object | Objekt, který má hodnoty pro funkci. Tento objekt Poskytněte jenom pro funkce, které podporují příjem objektu s hodnotami parametrů, jako je například **listAccountSas** v účtu úložiště. Příklad předávání hodnot funkcí je uveden v tomto článku. | 

### <a name="valid-uses"></a>Platná použití

Funkce seznamu se dá použít jenom ve vlastnostech definice prostředků a v části výstupy šablony nebo nasazení. Při použití s [iterací vlastnosti](resource-group-create-multiple.md#property-iteration)můžete použít funkce seznamu pro `input`, protože je výraz přiřazen vlastnosti prostředku. Nelze je použít s `count`, protože počet musí být určen před vyřešením funkce seznamu.

### <a name="implementations"></a>Implementace

Možná použití seznamu * jsou uvedena v následující tabulce.

| Typ prostředku | Název funkce |
| ------------- | ------------- |
| Microsoft. AnalysisServices/servery | [listGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft. AppConfiguration/configurationStores | Klíče listkey |
| Microsoft. Automation/automationAccounts | [Klíče listkey](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft. Batch/batchAccounts | [klíče listkey](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft. BatchAI/pracovní prostory/experimenty/úlohy | [listoutputfiles](/rest/api/batchai/jobs/listoutputfiles) |
| Microsoft. blockchain/blockchainMembers | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Microsoft. blockchain/blockchainMembers/transactionNodes | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Microsoft. BotService/botServices/kanály | listChannelWithKeys |
| Microsoft. cache/Redis | [Klíče listkey](/rest/api/redis/redis/listkeys) |
| Microsoft. Cognitiveservices Account/účty | [Klíče listkey](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Microsoft. ContainerRegistry/Registry | [listBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/getbuildsourceuploadurl) |
| Microsoft. ContainerRegistry/Registry | [listCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Microsoft. ContainerRegistry/Registry | [listUsages](/rest/api/containerregistry/registries/listusages) |
| Microsoft. ContainerRegistry/Registry/Webhooky | [listEvents](/rest/api/containerregistry/webhooks/listevents) |
| Microsoft. ContainerRegistry/Registry/běhy | [listLogSasUrl](/rest/api/containerregistry/runs/getlogsasurl) |
| Microsoft. ContainerRegistry/Registry/úkoly | [listDetails](/rest/api/containerregistry/tasks/getdetails) |
| Microsoft. ContainerService/managedClusters | [listClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft. ContainerService/managedClusters | [listClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Microsoft. ContainerService/managedClusters/accessProfiles | [listCredential](/rest/api/aks/managedclusters/getaccessprofile) |
| Microsoft. DataBox/Jobs | listCredentials |
| Microsoft. DataFactory/DataFactory/gateways | listauthkeys |
| Microsoft. DataFactory/Factory/integrationruntimes | [listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft. DataLakeAnalytics/Accounts/storageAccounts/Containers | [listSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Microsoft. Devices/iotHubs | [klíče listkey](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft. Devices/provisioningServices/Keys | [klíče listkey](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft. Devices/provisioningServices | [klíče listkey](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft. DevTestLab/Labs | [ListVhds](/rest/api/dtl/labs/listvhds) |
| Microsoft. DevTestLab/Labs/plány | [ListApplicable](/rest/api/dtl/schedules/listapplicable) |
| Microsoft. DevTestLab/Labs/Users/serviceFabrics | [ListApplicableSchedules](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft. DevTestLab/Labs/virtualMachines | [ListApplicableSchedules](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft. DocumentDB/databaseAccounts | [listConnectionStrings](/rest/api/cosmos-db-resource-provider/databaseaccounts/listconnectionstrings) |
| Microsoft. DocumentDB/databaseAccounts | [Klíče listkey](/rest/api/cosmos-db-resource-provider/databaseaccounts/listkeys) |
| Microsoft. DomainRegistration | [listDomainRecommendations](/rest/api/appservice/domains/listrecommendations) |
| Microsoft. DomainRegistration/topLevelDomains | [listAgreements](/rest/api/appservice/topleveldomains/listagreements) |
| Microsoft. EventGrid/domény | [Klíče listkey](/rest/api/eventgrid/domains/listsharedaccesskeys) |
| Microsoft. EventGrid/témata | [Klíče listkey](/rest/api/eventgrid/topics/listsharedaccesskeys) |
| Microsoft. EventHub/obory názvů/autorizačních pravidel | [klíče listkey](/rest/api/eventhub/namespaces/listkeys) |
| Microsoft. EventHub/obory názvů/disasterRecoveryConfigs/autorizačních pravidel | [klíče listkey](/rest/api/eventhub/disasterrecoveryconfigs/listkeys) |
| Microsoft. EventHub/obory názvů/eventhubs/autorizačních pravidel | [klíče listkey](/rest/api/eventhub/eventhubs/listkeys) |
| Microsoft. ImportExport/Jobs | [listBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Microsoft. LabServices/uživatelé | [ListEnvironments](/rest/api/labservices/globalusers/listenvironments) |
| Microsoft. LabServices/uživatelé | [ListLabs](/rest/api/labservices/globalusers/listlabs) |
| Microsoft. Logic/integrationAccounts/Agreements | [listContentCallbackUrl](/rest/api/logic/agreements/listcontentcallbackurl) |
| Microsoft. Logic/integrationAccounts/Assemblies | [listContentCallbackUrl](/rest/api/logic/integrationaccountassemblies/listcontentcallbackurl) |
| Microsoft. Logic/integrationAccounts | [listCallbackUrl](/rest/api/logic/integrationaccounts/getcallbackurl) |
| Microsoft. Logic/integrationAccounts | [listKeyVaultKeys](/rest/api/logic/integrationaccounts/listkeyvaultkeys) |
| Microsoft. Logic/integrationAccounts/Maps | [listContentCallbackUrl](/rest/api/logic/maps/listcontentcallbackurl) |
| Microsoft. Logic/integrationAccounts/partneři | [listContentCallbackUrl](/rest/api/logic/partners/listcontentcallbackurl) |
| Microsoft. Logic/integrationAccounts/schemas | [listContentCallbackUrl](/rest/api/logic/schemas/listcontentcallbackurl) |
| Microsoft. Logic/Workflows | [listCallbackUrl](/rest/api/logic/workflows/listcallbackurl) |
| Microsoft. Logic/Workflows | [listSwagger](/rest/api/logic/workflows/listswagger) |
| Microsoft. Logic/Workflows/Triggers | [listCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Microsoft. Logic/Workflows/Versions/Triggers | [listCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Microsoft. MachineLearning/WebServices | [klíče listkey](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft. MachineLearning/pracovní prostory | listworkspacekeys |
| Microsoft. MachineLearningServices/pracovní prostory/výpočetní služby | Klíče listkey |
| Microsoft. MachineLearningServices/pracovní prostory | Klíče listkey |
| Microsoft. Maps/Accounts | [Klíče listkey](/rest/api/maps-management/accounts/listkeys) |
| Microsoft. Media/MediaServices/Assets | [listContainerSas](/rest/api/media/assets/listcontainersas) |
| Microsoft. Media/MediaServices/Assets | [listStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Microsoft. Media/MediaServices/streamingLocators | [listContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft. Media/MediaServices/streamingLocators | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| Microsoft. Network/applicationSecurityGroups | listIpConfigurations |
| Microsoft. NotificationHubs/obory názvů/autorizačních pravidel | [klíče listkey](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft. NotificationHubs/obory názvů/NotificationHubs/autorizačních pravidel | [klíče listkey](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft. OperationalInsights/pracovní prostory | [Klíče listkey](/rest/api/loganalytics/workspaces%202015-03-20/listkeys) |
| Microsoft. Relay/obory názvů/autorizačních pravidel | [klíče listkey](/rest/api/relay/namespaces/listkeys) |
| Microsoft. Relay/obory názvů/disasterRecoveryConfigs/autorizačních pravidel | klíče listkey |
| Microsoft. Relay/obory názvů/HybridConnections/autorizačních pravidel | [klíče listkey](/rest/api/relay/hybridconnections/listkeys) |
| Microsoft. Relay/obory názvů/WcfRelays/autorizačních pravidel | [klíče listkey](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft. Search/searchServices | [listAdminKeys](/rest/api/searchmanagement/adminkeys/get) |
| Microsoft. Search/searchServices | [listQueryKeys](/rest/api/searchmanagement/querykeys/listbysearchservice) |
| Microsoft. ServiceBus/obory názvů/autorizačních pravidel | [klíče listkey](/rest/api/servicebus/namespaces/listkeys) |
| Microsoft. ServiceBus/obory názvů/disasterRecoveryConfigs/autorizačních pravidel | [klíče listkey](/rest/api/servicebus/disasterrecoveryconfigs/listkeys) |
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
| Microsoft. Web/připojení | listconsentlinks |
| Microsoft. Web/customApis | listWsdlInterfaces |
| Microsoft. Web/umístění | listwsdlinterfaces |
| Microsoft. Web/apimanagementaccounts/API/Connections | listconnectionkeys |
| Microsoft. Web/apimanagementaccounts/API/Connections | listsecrets |
| Microsoft. Web/weby/funkce | [listsecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| Microsoft. Web/weby/hybridconnectionnamespaces/Relay | [klíče listkey](/rest/api/appservice/webapps/listhybridconnectionkeys) |
| Microsoft. Web/weby | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| Microsoft. Web/weby/sloty/funkce | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |

Chcete-li zjistit, které typy prostředků mají operaci seznamu, máte následující možnosti:

* Prohlédněte si [operace REST API](/rest/api/) pro poskytovatele prostředků a hledejte operace se seznamem. Například účty úložiště mají [operaci klíče listkey](/rest/api/storagerp/storageaccounts).
* Použijte rutinu PowerShellu [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) . Následující příklad získá všechny operace se seznamem pro účty úložiště:

  ```powershell
  Get-AzProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* Pomocí následujícího příkazu rozhraní příkazového řádku Azure můžete filtrovat jenom operace se seznamem:

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

Jiné funkce seznamu mají různé návratové formáty. Chcete-li zobrazit formát funkce, zahrňte ji do části výstupy, jak je znázorněno v příkladu šablony.

### <a name="remarks"></a>Poznámky

Zadejte prostředek buď pomocí názvu prostředku, nebo pomocí [funkce ResourceID](#resourceid). Při použití funkce list ve stejné šabloně, která nasazuje odkazovaný prostředek, použijte název prostředku.

Použijete-li funkci **list** v prostředku, který je podmíněně nasazen, je funkce vyhodnocena i v případě, že prostředek není nasazen. Pokud funkce **seznamu** odkazuje na prostředek, který neexistuje, zobrazí se chyba. Použijte funkci **if** a ujistěte se, že je funkce vyhodnocena pouze při nasazení prostředku. Podívejte se na [funkci IF](resource-group-template-functions-logical.md#if) pro ukázkovou šablonu, která používá if a list s podmíněně nasazeným prostředkem.

### <a name="list-example"></a>Příklad seznamu

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/listkeys.json) ukazuje, jak vrátit primární a sekundární klíče z účtu úložiště v části výstupy. Také vrátí token SAS pro účet úložiště. 

Chcete-li získat token SAS, předejte objekt pro čas vypršení platnosti. Čas vypršení platnosti musí být v budoucnosti. Tento příklad je určený k zobrazení způsobu použití funkcí seznamu. Obvykle byste token SAS používali v hodnotě prostředku, ale nebudete ho vracet jako výstupní hodnotu. Výstupní hodnoty jsou uložené v historii nasazení a nejsou zabezpečené.

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

## <a name="providers"></a>Dodavateli

```json
providers(providerNamespace, [resourceType])
```

Vrátí informace o poskytovateli prostředků a jeho podporovaných typech prostředků. Pokud nezadáte typ prostředku, funkce vrátí všechny podporované typy pro poskytovatele prostředků.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| providerNamespace |Ano |string |Obor názvů poskytovatele |
| resourceType |Ne |string |Typ prostředku v rámci zadaného oboru názvů. |

### <a name="return-value"></a>Návratová hodnota

Každý podporovaný typ je vrácen v následujícím formátu: 

```json
{
    "resourceType": "{name of resource type}",
    "locations": [ all supported locations ],
    "apiVersions": [ all supported API versions ]
}
```

Řazení pole vrácených hodnot není zaručeno.

### <a name="providers-example"></a>Příklad poskytovatelů

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json) ukazuje, jak používat funkci poskytovatele:

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

U poskytovatele prostředků **Microsoft. Web** **a typu** prostředku vrátí předchozí příklad objekt v následujícím formátu:

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

## <a name="reference"></a>Odkaz

```json
reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])
```

Vrátí objekt představující běhový stav prostředku.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| resourceName nebo resourceIdentifier |Ano |string |Název nebo jedinečný identifikátor prostředku Při odkazování na prostředek v aktuální šabloně zadejte jako parametr jenom název prostředku. Při odkazování na dříve nasazený prostředek zadejte ID prostředku. |
| apiVersion |Ne |string |Verze rozhraní API zadaného prostředku Zahrňte tento parametr, pokud prostředek není zřízený v rámci stejné šablony. Obvykle ve formátu **yyyy-MM-DD**. Platné verze rozhraní API pro váš prostředek naleznete v tématu [Reference k šabloně](/azure/templates/). |
| Kompletní |Ne |string |Hodnota, která určuje, zda má být vrácen úplný objekt prostředku. Pokud nezadáte `'Full'`, vrátí se pouze objekt vlastnosti prostředku. Úplný objekt obsahuje hodnoty, jako je ID a umístění prostředku. |

### <a name="return-value"></a>Návratová hodnota

Každý typ prostředku vrátí různé vlastnosti referenční funkce. Funkce nevrací jeden, předdefinovaný formát. Vrácená hodnota se také liší v závislosti na tom, zda jste zadali úplný objekt. Chcete-li zobrazit vlastnosti pro typ prostředku, vraťte objekt v části výstupy, jak je znázorněno v příkladu.

### <a name="remarks"></a>Poznámky

Odkazovaná funkce načte běhový stav buď dříve nasazeného prostředku, nebo prostředku nasazeného v aktuální šabloně. Tento článek ukazuje příklady pro oba scénáře.

Obvykle použijete **odkazovou** funkci k vrácení konkrétní hodnoty z objektu, jako je například identifikátor URI koncového bodu objektu BLOB nebo plně kvalifikovaný název domény.

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

Použijte `'Full'`, pokud potřebujete hodnoty prostředků, které nejsou součástí schématu vlastností. Pokud třeba chcete nastavit zásady přístupu trezoru klíčů, Získejte vlastnosti identity pro virtuální počítač.

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

Odkazovaná funkce se dá použít jenom ve vlastnostech definice prostředků a v části výstupy šablony nebo nasazení. Při použití s [iterací vlastnosti](resource-group-create-multiple.md#property-iteration)můžete použít referenční funkci pro `input`, protože výraz je přiřazen vlastnosti prostředku. Nelze jej použít s `count`, protože počet musí být určen před vyřešením Referenční funkce.

V výstupech [vnořené šablony](resource-group-linked-templates.md#nested-template) nelze použít referenční funkci pro vrácení prostředku, který jste nasadili ve vnořené šabloně. Místo toho použijte [propojenou šablonu](resource-group-linked-templates.md#external-template).

Použijete-li **referenční** funkci v prostředku, který je podmíněně nasazen, je funkce vyhodnocena i v případě, že prostředek není nasazen.  Pokud **odkazovaná** funkce odkazuje na prostředek, který neexistuje, zobrazí se chyba. Použijte funkci **if** a ujistěte se, že je funkce vyhodnocena pouze při nasazení prostředku. Podívejte se na [funkci IF](resource-group-template-functions-logical.md#if) pro ukázkovou šablonu, která používá if a odkaz s podmíněně nasazeným prostředkem.

### <a name="implicit-dependency"></a>Implicitní závislost

Pomocí referenční funkce implicitně deklarujete, že jeden prostředek závisí na jiném prostředku, je-li odkazovaný prostředek zřízen ve stejné šabloně a odkazujete na prostředek podle jeho názvu (nikoli ID prostředku). Nemusíte také používat vlastnost dependsOn. Funkce není vyhodnocena, dokud není dokončeno nasazení odkazovaného prostředku.

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

### <a name="reference-example"></a>Příklad odkazu

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json) nasadí prostředek a odkazuje na tento prostředek.

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

Předchozí příklad vrátí dva objekty. Objekt vlastnosti je v následujícím formátu:

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

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/reference.json) odkazuje na účet úložiště, který není v této šabloně nasazený. Účet úložiště již existuje v rámci stejného předplatného.

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

Vrátí objekt, který představuje aktuální skupinu prostředků. 

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

Funkci `resourceGroup()` nelze použít v šabloně, která je [nasazena na úrovni předplatného](deploy-to-subscription.md). Dá se použít jenom v šablonách, které se nasazují do skupiny prostředků.

Běžné použití funkce Resource je vytvořit prostředky ve stejném umístění jako skupina prostředků. Následující příklad používá umístění skupiny prostředků k přiřazení umístění webu.

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

K použití značek ze skupiny prostředků na prostředek můžete také použít funkci Resource. Další informace najdete v tématu [použití značek ze skupiny prostředků](resource-group-using-tags.md#apply-tags-from-resource-group).

### <a name="resource-group-example"></a>Příklad skupiny prostředků

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json) vrátí vlastnosti skupiny prostředků.

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

Předchozí příklad vrátí objekt v následujícím formátu:

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

Vrátí jedinečný identifikátor prostředku. Tuto funkci použijete, pokud je název prostředku dvojznačný nebo není zřízený v rámci stejné šablony. 

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| subscriptionId |Ne |řetězec (ve formátu GUID) |Výchozí hodnota je aktuální předplatné. Tuto hodnotu zadejte, když potřebujete načíst prostředek v jiném předplatném. |
| resourceGroupName |Ne |string |Výchozí hodnota je aktuální skupina prostředků. Tuto hodnotu zadejte, když potřebujete načíst prostředek v jiné skupině prostředků. |
| resourceType |Ano |string |Typ prostředku včetně oboru názvů poskytovatele prostředků |
| resourceName1 |Ano |string |Název prostředku |
| resourceName2 |Ne |string |Další segment názvu prostředku (v případě potřeby). |

Pokračujte v přidávání názvů prostředků jako parametrů, pokud typ prostředku obsahuje více segmentů.

### <a name="return-value"></a>Návratová hodnota

ID prostředku se vrátí v následujícím formátu:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
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

Tuto funkci je často potřeba použít při použití účtu úložiště nebo virtuální sítě v alternativní skupině prostředků. Následující příklad ukazuje, jak lze snadno použít prostředek z externí skupiny prostředků:

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

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json) vrátí ID prostředku pro účet úložiště ve skupině prostředků:

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
| sameRGOutput | Řetězec | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | Řetězec | /subscriptions/{current-sub-id}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | Řetězec | /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | Řetězec | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/serverName/databases/databaseName |

## <a name="subscription"></a>předplatné

```json
subscription()
```

Vrátí podrobnosti o předplatném pro aktuální nasazení. 

### <a name="return-value"></a>Návratová hodnota

Funkce vrátí následující formát:

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="subscription-example"></a>Příklad předplatného

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json) ukazuje funkci předplatného nazvanou v části výstupy. 

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
| subscriptionId |Ne |řetězec (ve formátu GUID) |Výchozí hodnota je aktuální předplatné. Tuto hodnotu zadejte, když potřebujete načíst prostředek v jiném předplatném. |
| resourceType |Ano |string |Typ prostředku včetně oboru názvů poskytovatele prostředků |
| resourceName1 |Ano |string |Název prostředku |
| resourceName2 |Ne |string |Další segment názvu prostředku (v případě potřeby). |

Pokračujte v přidávání názvů prostředků jako parametrů, pokud typ prostředku obsahuje více segmentů.

### <a name="return-value"></a>Návratová hodnota

Identifikátor se vrátí v následujícím formátu:

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
| resourceType |Ano |string |Typ prostředku včetně oboru názvů poskytovatele prostředků |
| resourceName1 |Ano |string |Název prostředku |
| resourceName2 |Ne |string |Další segment názvu prostředku (v případě potřeby). |

Pokračujte v přidávání názvů prostředků jako parametrů, pokud typ prostředku obsahuje více segmentů.

### <a name="return-value"></a>Návratová hodnota

Identifikátor se vrátí v následujícím formátu:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Poznámky

Pomocí této funkce získáte ID prostředku pro prostředek, který je nasazený do tenanta. Vrácené ID se liší od hodnot vrácených jinými funkcemi ID prostředků bez zahrnutí skupin prostředků nebo hodnot předplatného.

## <a name="next-steps"></a>Další kroky

* Popis sekcí v šabloně Azure Resource Manager najdete v tématu [vytváření šablon Azure Resource Manager](resource-group-authoring-templates.md).
* Chcete-li sloučit více šablon, přečtěte si téma [použití propojených šablon s Azure Resource Manager](resource-group-linked-templates.md).
* Informace o iteraci zadaného počtu výskytů při vytváření typu prostředku najdete v tématu [vytvoření více instancí prostředků v Azure Resource Manager](resource-group-create-multiple.md).
* Pokud chcete zjistit, jak nasadit šablonu, kterou jste vytvořili, přečtěte si téma [nasazení aplikace pomocí šablony Azure Resource Manager](resource-group-template-deploy.md).

