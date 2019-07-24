---
title: Funkce šablon Azure Resource Manageru – prostředky | Dokumentace Microsoftu
description: Popisuje funkce pro použití v šabloně Azure Resource Manageru k načtení hodnoty o prostředcích.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 07/11/2019
ms.author: tomfitz
ms.openlocfilehash: 7d967f0bb0b7a811d4db7836cdbffdad91088a2c
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311687"
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

## <a name="list"></a>seznamu

`list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)`

Syntaxe této funkce se liší podle názvu operací seznamu. Každá implementace vrací hodnoty pro typ prostředku, který podporuje operaci list. Název operace musí začínat `list`na. Některé běžné použití jsou `listKeys` a. `listSecrets` 

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| resourceName nebo resourceIdentifier |Ano |řetězec |Jedinečný identifikátor prostředku. |
| apiVersion |Ano |řetězec |Verze rozhraní API prostředku běhový stav. Obvykle ve formátu **rrrr mm-dd**. |
| functionValues |Ne |objekt | Objekt, který obsahuje hodnoty pro funkci. Tento objekt poskytují pouze pro funkce, které podporují přijímá objekt s hodnotami parametrů, jako například **listAccountSas** v účtu úložiště. Příklad předávání hodnot funkcí je uveden v tomto článku. | 

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
| Microsoft. BotService/botServices/kanály | listChannelWithKeys |
| Microsoft.Cache/redis | [klíče Listkey](/rest/api/redis/redis/listkeys) |
| Microsoft.CognitiveServices/accounts | [klíče Listkey](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Microsoft.ContainerRegistry/registries | [listBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/getbuildsourceuploadurl) |
| Microsoft.ContainerRegistry/registries | [listCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Microsoft.ContainerRegistry/registries | [listPolicies](/rest/api/containerregistry/registries/listpolicies) |
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
| Microsoft. Devices/iotHubs | [klíče listkey](/rest/api/iothub/iothubresource/listkeys) |
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
| Microsoft. Logic/Workflows/Triggers | [listCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Microsoft. Logic/Workflows/Versions/Triggers | [listCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Microsoft. MachineLearning/WebServices | [klíče listkey](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft. MachineLearning/pracovní prostory | listworkspacekeys |
| Microsoft. MachineLearningServices/pracovní prostory/výpočetní služby | Klíče listkey |
| Microsoft.MachineLearningServices/workspaces | Klíče listkey |
| Microsoft. Maps/Accounts | [klíče Listkey](/rest/api/maps-management/accounts/listkeys) |
| Microsoft. Media/MediaServices/Assets | [listContainerSas](/rest/api/media/assets/listcontainersas) |
| Microsoft. Media/MediaServices/Assets | [listStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Microsoft. Media/MediaServices/streamingLocators | [listContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft. Media/MediaServices/streamingLocators | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| Microsoft.Network/applicationSecurityGroups | listIpConfigurations |
| Microsoft. NotificationHubs/obory názvů/autorizačních pravidel | [klíče listkey](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft. NotificationHubs/obory názvů/NotificationHubs/autorizačních pravidel | [klíče listkey](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft.OperationalInsights/workspaces | [klíče Listkey](/rest/api/loganalytics/workspaces%202015-03-20/listkeys) |
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
| Microsoft.Storage/storageAccounts. | [listAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft.Storage/storageAccounts. | [klíče listkey](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft.Storage/storageAccounts. | [listServiceSas](/rest/api/storagerp/storageaccounts/listservicesas) |
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
| microsoft.web/sites/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| microsoft.web/sites/hybridconnectionnamespaces/relays | [klíče listkey](/rest/api/appservice/webapps/listhybridconnectionkeys) |
| microsoft.web/sites | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
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

Použijete-li funkci **list** v prostředku, který je podmíněně nasazen, je funkce vyhodnocena i v případě, že prostředek není nasazen. Pokud funkce **seznamu** odkazuje na prostředek, který neexistuje, zobrazí se chyba. Použijte funkci **if** a ujistěte se, že je funkce vyhodnocena pouze při nasazení prostředku. Podívejte se na [funkci IF](resource-group-template-functions-logical.md#if) pro ukázkovou šablonu, která používá if a list s podmíněně nasazeným prostředkem.

### <a name="example"></a>Příklad

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

Odkaz na funkci jde použít jenom ve vlastnosti definice prostředku a část Outputs následujícím šablony nebo nasazení. Při použití s [iterací vlastnosti](resource-group-create-multiple.md#property-iteration)můžete použít referenční funkci pro `input` , protože výraz je přiřazen vlastnosti prostředku. Nelze jej použít s `count` , protože počet musí být určen před vyřešením Referenční funkce.

V výstupech [vnořené šablony](resource-group-linked-templates.md#nested-template) nelze použít referenční funkci pro vrácení prostředku, který jste nasadili ve vnořené šabloně. Místo toho použijte [propojenou šablonu](resource-group-linked-templates.md#external-template-and-external-parameters).

Pomocí funkce odkaz na implicitně deklarujete, jeden prostředek závisí na jiný prostředek, pokud je oba odkazované prostředky poskytnutém v rámci stejné šablony a reference na prostředek má název (není ID prostředku). Není nutné použít také vlastnost dependsOn. Funkce není vyhodnocen, dokud odkazované prostředky dokončení nasazení.

Použijete-li **referenční** funkci v prostředku, který je podmíněně nasazen, je funkce vyhodnocena i v případě, že prostředek není nasazen.  Pokud odkazovaná funkce odkazuje na  prostředek, který neexistuje, zobrazí se chyba. Použijte funkci **if** a ujistěte se, že je funkce vyhodnocena pouze při nasazení prostředku. Podívejte se na [funkci IF](resource-group-template-functions-logical.md#if) pro ukázkovou šablonu, která používá if a odkaz s podmíněně nasazeným prostředkem.

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

Funkci nelze použít v šabloně, která je nasazena [na úrovni](deploy-to-subscription.md)předplatného. `resourceGroup()` Dá se použít jenom v šablonách, které se nasazují do skupiny prostředků.

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

K použití značek ze skupiny prostředků na prostředek můžete také použít funkci Resource. Další informace najdete v tématu [použití značek ze skupiny prostředků](resource-group-using-tags.md#apply-tags-from-resource-group).

### <a name="example"></a>Příklad

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

Při použití s nasazením `resourceId()` na [úrovni](deploy-to-subscription.md)předplatného může funkce načíst jenom ID prostředků nasazených na dané úrovni. Můžete například získat ID definice zásady nebo definice role, ale ne ID účtu úložiště. U nasazení do skupiny prostředků má opak hodnotu true. Nemůžete získat ID prostředku nasazeného na úrovni předplatného.

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

Pokud chcete získat ID prostředku na úrovni předplatného při nasazení v oboru předplatného, použijte:

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

## <a name="next-steps"></a>Další postup

* Popis části šablony Azure Resource Manageru najdete v tématu [šablon pro vytváření Azure Resource Manageru](resource-group-authoring-templates.md).
* Chcete-li sloučit několik šablon, přečtěte si téma [použití propojených šablon s Azure Resource Managerem](resource-group-linked-templates.md).
* K iteraci zadaného počtu opakování při vytváření konkrétní typ prostředku, naleznete v tématu [vytvořit více instancí prostředku v Azure Resource Manageru](resource-group-create-multiple.md).
* Postup nasazení šablony, které jste vytvořili, najdete v sekci [nasazení aplikace pomocí šablony Azure Resource Manageru](resource-group-template-deploy.md).

