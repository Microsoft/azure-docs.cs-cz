---
title: Funkce šablony - zdroje
description: Popisuje funkce, které se mají použít v šabloně Azure Resource Manager k načtení hodnot o prostředcích.
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 641602218aa19b790eb6e7feabdb7b46a520b590
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478270"
---
# <a name="resource-functions-for-arm-templates"></a>Funkce prostředků pro šablony ARM

Správce prostředků poskytuje následující funkce pro získávání hodnot prostředků v šabloně Správce prostředků Azure(ARM):

* [rozšířeníResourceId](#extensionresourceid)
* [seznam*](#list)
* [Poskytovatelů](#providers)
* [Odkaz](#reference)
* [skupina prostředků](#resourcegroup)
* [Resourceid](#resourceid)
* [Předplatné](#subscription)
* [subscriptionResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

Chcete-li získat hodnoty z parametrů, proměnných nebo aktuálního nasazení, [přečtěte](template-functions-deployment.md)si téma Funkce hodnoty nasazení .

## <a name="extensionresourceid"></a>rozšířeníResourceId

```json
extensionResourceId(resourceId, resourceType, resourceName1, [resourceName2], ...)
```

Vrátí ID prostředku pro [prostředek rozšíření](../management/extension-resource-types.md), což je typ prostředku, který je použit pro jiný prostředek přidat do svých možností.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| resourceId |Ano |řetězec |ID prostředku pro prostředek, na který je prostředek rozšíření použit. |
| resourceType |Ano |řetězec |Typ prostředku včetně oboru názvů poskytovatele prostředků. |
| název_prostředku1 |Ano |řetězec |Název prostředku. |
| název_prostředku2 |Ne |řetězec |Další segment názvu prostředku, v případě potřeby. |

Pokračujte v přidávání názvů prostředků jako parametrů, pokud typ prostředku obsahuje více segmentů.

### <a name="return-value"></a>Návratová hodnota

Základní formát ID prostředku vrácené touto funkcí je:

```json
{scope}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Segment oboru se liší podle zdroje, který je rozšiřován.

Při použití prostředku rozšíření **prostředku**je ID prostředku vráceno v následujícím formátu:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{baseResourceProviderNamespace}/{baseResourceType}/{baseResourceName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Pokud je prostředek rozšíření použit pro **skupinu prostředků**, formát je:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Pokud je prostředek rozšíření použit pro **odběr**, formát je:

```json
/subscriptions/{subscriptionId}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Pokud je prostředek rozšíření použit pro **skupinu pro správu**, formát je:

```json
/providers/Microsoft.Management/managementGroups/{managementGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

### <a name="extensionresourceid-example"></a>extensionResourceId příklad

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

## <a name="list"></a>seznam*

```json
list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)
```

Syntaxe této funkce se liší podle názvu operací seznamu. Každá implementace vrátí hodnoty pro typ prostředku, který podporuje operaci seznamu. Název operace musí `list`začínat písmenem . Některé běžné použití `listKeys` `listSecrets`jsou a .

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| resourceName nebo resourceIdentifier |Ano |řetězec |Jedinečný identifikátor prostředku |
| apiVersion |Ano |řetězec |Verze rozhraní API stavu za běhu prostředků. Obvykle ve formátu **yyyy-mm-dd**. |
| functionValues |Ne |objekt | Objekt, který má hodnoty pro funkci. Tento objekt zadejte pouze pro funkce, které podporují příjem objektu s hodnotami parametrů, jako je například **listAccountSas** na účtu úložiště. Příklad předávání hodnot funkcí je uveden v tomto článku. |

### <a name="valid-uses"></a>Platná použití

Funkce seznamu lze použít pouze ve vlastnostech definice prostředku a v části výstupy šablony nebo nasazení. Při použití s [iterací vlastností](copy-properties.md) `input` můžete použít funkce seznamu, protože výraz je přiřazen vlastnosti prostředku. Nelze je použít s, `count` protože počet musí být určen před vyřešením funkce seznamu.

### <a name="implementations"></a>Implementace

Možná použití seznamu* jsou uvedena v následující tabulce.

| Typ prostředku | Název funkce |
| ------------- | ------------- |
| Microsoft.AnalysisServices/servery | [listGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft.AppKonfigurace/configurationStores | Seznamy kláves |
| Microsoft.Automation/automationÚčty | [seznam kláves](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.Batch/batchAccounts | [seznamové klávesy](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft.BatchAI/pracovní prostory/experimenty/úlohy | [listoutputfiles](/rest/api/batchai/jobs/listoutputfiles) |
| Microsoft.Blockchain/blockchainČlenové | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Microsoft.Blockchain/blockchainČlenové/transakceNodes | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Microsoft.Cache/redis | [seznam kláves](/rest/api/redis/redis/listkeys) |
| Microsoft.CognitiveServices/účty | [seznam kláves](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Microsoft.ContainerRegistry/registry | [listBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/getbuildsourceuploadurl) |
| Microsoft.ContainerRegistry/registry | [listCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Microsoft.ContainerRegistry/registry | [seznamPoužití](/rest/api/containerregistry/registries/listusages) |
| Microsoft.ContainerRegistry/registry/webhooky | [listUdálosti](/rest/api/containerregistry/webhooks/listevents) |
| Microsoft.ContainerRegistry/registry/runs | [listLogSasUrl](/rest/api/containerregistry/runs/getlogsasurl) |
| Microsoft.ContainerRegistry/registry/úkoly | [listPodrobnosti](/rest/api/containerregistry/tasks/getdetails) |
| Microsoft.ContainerService/spravované clustery | [listClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft.ContainerService/spravované clustery | [listClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Microsoft.ContainerService/managedClusters/accessProfiles | [listCredential](/rest/api/aks/managedclusters/getaccessprofile) |
| Microsoft.DataBox/úlohy | listCredentials |
| Microsoft.DataFactory/datafactories/gateways Microsoft.DataFactory/datafactories/gateways Microsoft.DataFactory/datafactories/gateways Microsoft. | listauthkeys |
| Microsoft.DataFactory/továrny/integrationruntimes | [listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft.DataLakeAnalytics/účty/storageAccounts/Containers | [seznamSasTokeny](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Microsoft.DataShare/účty/sdílené položky | [listSynchronizace](/rest/api/datashare/shares/listsynchronizations) |
| Microsoft.DataShare/účty/sharePředplatné | [listSourceShareSynchronizationSettings](/rest/api/datashare/sharesubscriptions/listsourcesharesynchronizationsettings) |
| Microsoft.DataShare/účty/sharePředplatné | [listSynchronizacePodrobnosti](/rest/api/datashare/sharesubscriptions/listsynchronizationdetails) |
| Microsoft.DataShare/účty/sharePředplatné | [listSynchronizace](/rest/api/datashare/sharesubscriptions/listsynchronizations) |
| Microsoft.Devices/iotHubs | [seznamové klávesy](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft.Devices/iotHubs/iotHubKeys | [seznamové klávesy](/rest/api/iothub/iothubresource/getkeysforkeyname) |
| Microsoft.Devices/provisioningServices/keys | [seznamové klávesy](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft.Devices/provisioningServices | [seznamové klávesy](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft.DevTestLab/labs | [SeznamVhds](/rest/api/dtl/labs/listvhds) |
| Microsoft.DevTestLab/labs/plány | [SeznamPoužitelné](/rest/api/dtl/schedules/listapplicable) |
| Microsoft.DevTestLab/labs/users/serviceFabrics | [ListApplicableSchedules](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft.DevTestLab/labs/virtualMachines | [ListApplicableSchedules](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft.DocumentDB/databaseAccounts | [listConnectionStrings](/rest/api/cosmos-db-resource-provider/databaseaccounts/listconnectionstrings) |
| Microsoft.DocumentDB/databaseAccounts | [seznam kláves](/rest/api/cosmos-db-resource-provider/databaseaccounts/listkeys) |
| Microsoft.DomainRegistration | [listDomainRecommendations](/rest/api/appservice/domains/listrecommendations) |
| Microsoft.DomainRegistration/topLevelDomains | [listDohody](/rest/api/appservice/topleveldomains/listagreements) |
| Microsoft.EventGrid/domény | [seznam kláves](/rest/api/eventgrid/version2019-06-01/domains/listsharedaccesskeys) |
| Microsoft.EventGrid/témata | [seznam kláves](/rest/api/eventgrid/version2019-06-01/topics/listsharedaccesskeys) |
| Microsoft.EventHub/obory názvů/autorizační pravidla | [seznamové klávesy](/rest/api/eventhub/namespaces/listkeys) |
| Microsoft.EventHub/obory názvů/disasterRecoveryConfigs/authorizationRules | [seznamové klávesy](/rest/api/eventhub/disasterrecoveryconfigs/listkeys) |
| Microsoft.EventHub/obory názvů/eventhubs/authorizationRules | [seznamové klávesy](/rest/api/eventhub/eventhubs/listkeys) |
| Microsoft.ImportExport/úlohy | [listBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Microsoft.Kusto/Clusters/Databases | [Principals](/rest/api/azurerekusto/databases/listprincipals) |
| Microsoft.LabServices/uživatelé | [SepsAt prostředí](/rest/api/labservices/globalusers/listenvironments) |
| Microsoft.LabServices/uživatelé | [Seznamlabs](/rest/api/labservices/globalusers/listlabs) |
| Microsoft.Logic/integrationAccounts/agreements | [listContentCallbackUrl](/rest/api/logic/agreements/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/assemblies | [listContentCallbackUrl](/rest/api/logic/integrationaccountassemblies/listcontentcallbackurl) |
| Microsoft.Logic/integrationÚčty | [listCallbackUrl](/rest/api/logic/integrationaccounts/getcallbackurl) |
| Microsoft.Logic/integrationÚčty | [listKeyVaultKeys](/rest/api/logic/integrationaccounts/listkeyvaultkeys) |
| Microsoft.Logic/integrationAccounts/maps | [listContentCallbackUrl](/rest/api/logic/maps/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/partners | [listContentCallbackUrl](/rest/api/logic/partners/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/schémata | [listContentCallbackUrl](/rest/api/logic/schemas/listcontentcallbackurl) |
| Microsoft.Logic/pracovní postupy | [listCallbackUrl](/rest/api/logic/workflows/listcallbackurl) |
| Microsoft.Logic/pracovní postupy | [seznamSwagger](/rest/api/logic/workflows/listswagger) |
| Microsoft.Logic/workflows/runs/actions | [listExpressionTraces](/rest/api/logic/workflowrunactions/listexpressiontraces) |
| Microsoft.Logic/workflows/runs/actions/opakování | [listExpressionTraces](/rest/api/logic/workflowrunactionrepetitions/listexpressiontraces) |
| Microsoft.Logic/workflows/triggers | [listCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Microsoft.Logic/workflows/verze/aktivační události | [listCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Microsoft.MachineLearning/webServices | [seznamové klávesy](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft.MachineLearning/Pracovní prostory | listworkspacekeys |
| Microsoft.MachineLearningServices/pracovní prostory/výpočetní prostředky | [seznam kláves](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listkeys) |
| Microsoft.MachineLearningServices/pracovní prostory/výpočetní prostředky | [listNodes](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listnodes) |
| Microsoft.MachineLearningServices/pracovní prostory | [seznam kláves](/rest/api/azureml/workspacesandcomputes/workspaces/listkeys) |
| Microsoft.Maps/účty | [seznam kláves](/rest/api/maps-management/accounts/listkeys) |
| Microsoft.Media/mediaservices/assets | [listContainerSas](/rest/api/media/assets/listcontainersas) |
| Microsoft.Media/mediaservices/assets | [listStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Microsoft.Media/mediaservices/streamingLokátory | [seznamklávesobsahu](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft.Media/mediaservices/streamingLokátory | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| Microsoft.Network/applicationSecurityGroups | listIpKonfigurace |
| Microsoft.NotificationHubs/Obory názvů/autorizační pravidla | [seznamové klávesy](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft.NotificationHubs/Obory názvů/NotificationHubs/authorizationRules | [seznamové klávesy](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft.OperationalInsights/pracovní prostory | [seznam kláves](/rest/api/loganalytics/workspaces%202015-03-20/listkeys) |
| Microsoft.PolicyInsights/náprava | [listDeployments](/rest/api/policy-insights/remediations/listdeploymentsatresourcegroup) |
| Microsoft.Relay/namespaces/authorizationRules | [seznamové klávesy](/rest/api/relay/namespaces/listkeys) |
| Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules | seznamové klávesy |
| Microsoft.Relay/namespaces/HybridConnections/authorizationRules | [seznamové klávesy](/rest/api/relay/hybridconnections/listkeys) |
| Microsoft.Relay/namespaces/WcfRelays/authorizationRules | [seznamové klávesy](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft.Search/searchServices | [seznam kláves správce](/rest/api/searchmanagement/adminkeys/get) |
| Microsoft.Search/searchServices | [listQueryKeys](/rest/api/searchmanagement/querykeys/listbysearchservice) |
| Microsoft.ServiceBus/obory názvů/autorizační pravidla | [seznamové klávesy](/rest/api/servicebus/namespaces/listkeys) |
| Microsoft.ServiceBus/obory názvů/disasterRecoveryConfigs/authorizationRules | [seznamové klávesy](/rest/api/servicebus/disasterrecoveryconfigs/listkeys) |
| Microsoft.ServiceBus/obory názvů/fronty/autorizacePravidla | [seznamové klávesy](/rest/api/servicebus/queues/listkeys) |
| Microsoft.ServiceBus/obory názvů/témata/autorizační pravidla | [seznamové klávesy](/rest/api/servicebus/topics/listkeys) |
| Microsoft.SignalRService/SignalR | [seznamové klávesy](/rest/api/signalr/signalr/listkeys) |
| Microsoft.Storage/storageAccounts | [listAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft.Storage/storageAccounts | [seznamové klávesy](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft.Storage/storageAccounts | [listServiceSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Microsoft.StorSimple/manažeři/zařízení | [sady selhat](/rest/api/storsimple/devices/listfailoversets) |
| Microsoft.StorSimple/manažeři/zařízení | [listFailoverTargets listFailoverTargets listFailoverTargets listFail](/rest/api/storsimple/devices/listfailovertargets) |
| Microsoft.StorSimple/manažeři | [seznamAktivační klíč](/rest/api/storsimple/managers/getactivationkey) |
| Microsoft.StorSimple/manažeři | [listPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Microsoft.Web/connectionGateways | Stav seznamu |
| Microsoft.web/připojení | seznamsouhlasodkazy |
| Microsoft.Web/customApis | listWsdlInterfaces |
| microsoft.web/locations | listwsdlinterfaces |
| Microsoft.web/apimanagementAccounts/apis/connections | listconnectionkeys |
| Microsoft.web/apimanagementAccounts/apis/connections | listsecrets |
| Microsoft.web/sites/backups | [list](/rest/api/appservice/webapps/listbackups) |
| Microsoft.Web/weby/config | [list](/rest/api/appservice/webapps/listconfigurations) |
| Microsoft.web/weby/funkce | [seznamové klávesy](/rest/api/appservice/webapps/listfunctionkeys)
| Microsoft.web/weby/funkce | [listsecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| Microsoft.web/sites/hybridconnectionnamespaces/relays Microsoft.web/sites/hybridconnectionnamespaces/relays Microsoft.web/sites/hybridconnectionnamespaces/relays Microsoft. | [seznamové klávesy](/rest/api/appservice/appserviceplans/listhybridconnectionkeys) |
| Microsoft.web/weby | [stav triggeru funkce synchronizace seznamu](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| microsoft.web/sites/sloty/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |
| microsoft.web/sites/sloty/zálohy | [list](/rest/api/appservice/webapps/listbackupsslot) |
| Microsoft.Web/sites/slots/config | [list](/rest/api/appservice/webapps/listconfigurationsslot) |
| microsoft.web/sites/sloty/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |

Chcete-li zjistit, které typy prostředků mají operaci seznamu, máte následující možnosti:

* Zobrazte [operace rozhraní REST API](/rest/api/) pro poskytovatele prostředků a vyhledejte operace seznamu. Například účty úložiště mají [seznamKeys operace](/rest/api/storagerp/storageaccounts).
* Použijte rutinu prostředí [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) PowerShell. Následující příklad získá všechny operace seznamu pro účty úložiště:

  ```powershell
  Get-AzProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* Pomocí následujícího příkazu Azure CLI můžete filtrovat jenom operace seznamu:

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

### <a name="return-value"></a>Návratová hodnota

Vrácený objekt se liší podle funkce seznamu, kterou používáte. Například listKeys pro účet úložiště vrátí následující formát:

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

Ostatní funkce seznamu mají různé návratové formáty. Chcete-li zobrazit formát funkce, zahrňte ji do oddílu výstupy, jak je znázorněno v vzorové šabloně.

### <a name="remarks"></a>Poznámky

Zadejte prostředek pomocí názvu prostředku nebo [funkce resourceId](#resourceid). Při použití funkce seznamu ve stejné šabloně, která nasazuje odkazovaný prostředek, použijte název prostředku.

Pokud použijete funkci **seznamu** v prostředku, který je podmíněně nasazen, funkce se vyhodnotí i v případě, že prostředek není nasazen. Pokud funkce **seznamu** odkazuje na prostředek, který neexistuje, zobrazí se chyba. Pomocí funkce **if** se ujistěte, že je funkce vyhodnocena pouze při nasazení prostředku. Podívejte se na [funkci if](template-functions-logical.md#if) pro ukázkovou šablonu, která používá if a seznam s podmíněně nasazeným prostředkem.

### <a name="list-example"></a>Příklad seznamu

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/listkeys.json) ukazuje, jak vrátit primární a sekundární klíče z účtu úložiště v části výstupy. Vrátí také token SAS pro účet úložiště.

Chcete-li získat token SAS, předajte objekt pro čas vypršení platnosti. Doba vypršení platnosti musí být v budoucnu. Tento příklad je určen k zobrazení způsobu použití funkcí seznamu. Obvykle byste použít token SAS v hodnotě prostředku, nikoli vrátit jako výstupní hodnotu. Výstupní hodnoty jsou uloženy v historii nasazení a nejsou zabezpečené.

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

## <a name="providers"></a>Poskytovatelů

```json
providers(providerNamespace, [resourceType])
```

Vrátí informace o poskytovateli prostředků a jeho podporovaných typech prostředků. Pokud nezadáte typ prostředku, funkce vrátí všechny podporované typy pro poskytovatele prostředků.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| obor providerNamespace |Ano |řetězec |Obor názvů zprostředkovatele |
| resourceType |Ne |řetězec |Typ prostředku v zadaném oboru názvů. |

### <a name="return-value"></a>Návratová hodnota

Každý podporovaný typ je vrácen v následujícím formátu:

```json
{
    "resourceType": "{name of resource type}",
    "locations": [ all supported locations ],
    "apiVersions": [ all supported API versions ]
}
```

Pořadí polí vrácených hodnot není zaručeno.

### <a name="providers-example"></a>Příklad zprostředkovatelů

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json) ukazuje, jak používat funkci zprostředkovatele:

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

U poskytovatele prostředků **Microsoft.Web** a typu prostředků **webů** vrátí předchozí příklad objekt v následujícím formátu:

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

## <a name="reference"></a>reference

```json
reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])
```

Vrátí objekt představující stav běhu prostředku.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| resourceName nebo resourceIdentifier |Ano |řetězec |Název nebo jedinečný identifikátor prostředku. Při odkazování na prostředek v aktuální šabloně zadejte jako parametr pouze název prostředku. Při odkazování na dříve nasazený prostředek nebo při nejednoznačném názvu prostředku zadejte ID prostředku. |
| apiVersion |Ne |řetězec |Verze rozhraní API zadaného prostředku. Tento parametr zahrňte, když prostředek není zřízen v rámci stejné šablony. Obvykle ve formátu **yyyy-mm-dd**. Platné verze rozhraní API pro váš prostředek naleznete v [tématu odkaz na šablonu](/azure/templates/). |
| "Plná" |Ne |řetězec |Hodnota, která určuje, zda má být vrácen celý objekt prostředku. Pokud nezadáte `'Full'`, je vrácen pouze objekt vlastností prostředku. Úplný objekt obsahuje hodnoty, jako je například ID prostředku a umístění. |

### <a name="return-value"></a>Návratová hodnota

Každý typ prostředku vrátí různé vlastnosti pro referenční funkci. Funkce nevrátí jeden předdefinovaný formát. Vrácená hodnota se také liší v závislosti na tom, zda jste zadali úplný objekt. Chcete-li zobrazit vlastnosti typu prostředku, vraťte objekt v části výstupy, jak je znázorněno v příkladu.

### <a name="remarks"></a>Poznámky

Referenční funkce načte stav runtime dříve nasazeného prostředku nebo prostředku nasazeného v aktuální šabloně. Tento článek ukazuje příklady pro oba scénáře.

Obvykle použijete **referenční** funkci k vrácení určité hodnoty z objektu, jako je například identifikátor URI koncového bodu objektu blob nebo plně kvalifikovaný název domény.

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))).primaryEndpoints.blob]",
        "type" : "string"
    },
    "FQDN": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName'))).dnsSettings.fqdn]",
        "type" : "string"
    }
}
```

Použijte, `'Full'` když potřebujete hodnoty prostředků, které nejsou součástí schématu vlastností. Chcete-li například nastavit zásady přístupu trezoru klíčů, získejte vlastnosti identity pro virtuální počítač.

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "properties": {
    "tenantId": "[subscription().tenantId]",
    "accessPolicies": [
      {
        "tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.tenantId]",
        "objectId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.principalId]",
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

Referenční funkci lze použít pouze ve vlastnostech definice prostředku a v části výstupy šablony nebo nasazení. Při použití s [iterací vlastností](copy-properties.md) `input` můžete použít referenční funkci, protože výraz je přiřazen vlastnosti prostředku. Nelze jej použít s, `count` protože počet musí být určen před referenční funkce je vyřešen.

Referenční funkci ve výstupech [vnořené šablony](linked-templates.md#nested-template) nelze použít k vrácení prostředku, který jste nasadili v vnořené šabloně. Místo toho použijte [propojenou šablonu](linked-templates.md#linked-template).

Pokud použijete **referenční** funkci v prostředku, který je podmíněně nasazen, funkce se vyhodnotí i v případě, že prostředek není nasazen.  Zobrazí se chyba, pokud **referenční** funkce odkazuje na prostředek, který neexistuje. Pomocí funkce **if** se ujistěte, že je funkce vyhodnocena pouze při nasazení prostředku. Podívejte se na [funkci if](template-functions-logical.md#if) pro ukázkovou šablonu, která používá if a odkaz s podmíněně nasazeným prostředkem.

### <a name="implicit-dependency"></a>Implicitní závislost

Pomocí referenční funkce implicitně deklarujete, že jeden prostředek závisí na jiném prostředku, pokud je odkazovaný prostředek zřízen v rámci stejné šablony a odkazujete na prostředek podle jeho názvu (nikoli ID prostředku). Není nutné také použít dependsOn vlastnost. Funkce není vyhodnocena, dokud odkazovaný prostředek nedokončí nasazení.

### <a name="resource-name-or-identifier"></a>Název prostředku nebo identifikátor

Při odkazování na prostředek, který je nasazen ve stejné šabloně, zadejte název prostředku.

```json
"value": "[reference(parameters('storageAccountName'))]"
```

Při odkazování na prostředek, který není nasazen ve stejné šabloně, zadejte ID prostředku.

```json
"value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

Chcete-li se vyhnout nejednoznačnosti ohledně toho, na který prostředek odkazujete, můžete zadat plně kvalifikovaný identifikátor prostředku.

```json
"value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName')))]"
```

Při vytváření plně kvalifikovaný odkaz na prostředek, pořadí kombinovat segmenty z typu a názvu není pouze zřetězení obou. Místo toho za obor názvů použijte posloupnost párů *typu/názvu* od nejméně specifických po nejkonkrétnější:

**{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]**

Například:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt`je `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` správné, není správné

Chcete-li zjednodušit vytváření libovolného `resourceId()` ID prostředku, použijte funkce `concat()` popsané v tomto dokumentu namísto funkce.

### <a name="get-managed-identity"></a>Získání spravované identity

[Spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md) jsou [typy prostředků rozšíření,](../management/extension-resource-types.md) které jsou vytvořeny implicitně pro některé prostředky. Vzhledem k tomu, že spravovaná identita není v šabloně explicitně definována, musíte odkazovat na prostředek, na který je identita použita. Slouží `Full` k získání všech vlastností, včetně implicitně vytvořené identity.

Chcete-li například získat ID klienta pro spravovanou identitu, která se použije pro škálovací sadu virtuálních strojů, použijte:

```json
"tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), '2019-03-01', 'Full').Identity.tenantId]"
```

### <a name="reference-example"></a>Referenční příklad

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json) nasazuje prostředek a odkazuje na tento prostředek.

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

Předchozí příklad vrátí dva objekty. Objekt vlastností je v následujícím formátu:

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

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/reference.json) odkazuje na účet úložiště, který není v této šabloně nasazený. Účet úložiště již existuje v rámci stejného předplatného.

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

Vlastnost **managedBy** je vrácena pouze pro skupiny prostředků, které obsahují prostředky spravované jinou službou. Pro spravované aplikace, Databricks a AKS je hodnota vlastnosti ID prostředku spravovaného prostředku.

### <a name="remarks"></a>Poznámky

Funkci `resourceGroup()` nelze použít v šabloně, která je [nasazena na úrovni předplatného](deploy-to-subscription.md). Lze jej použít pouze v šablonách, které jsou nasazeny do skupiny prostředků. Funkci můžete použít v [propojené nebo vnořené šabloně (s vnitřním rozsahem),](linked-templates.md) která cílí na skupinu prostředků, i když je nadřazená šablona nasazená do předplatného. `resourceGroup()` V tomto scénáři je propojená nebo vnořená šablona nasazena na úrovni skupiny prostředků. Další informace o cílení na skupinu prostředků v nasazení na úrovni předplatného najdete v [tématu Nasazení prostředků Azure do více než jednoho předplatného nebo skupiny prostředků](cross-resource-group-deployment.md).

Funkce resourceGroup se běžně používá k vytváření prostředků ve stejném umístění jako skupina prostředků. Následující příklad používá umístění skupiny prostředků pro výchozí hodnotu parametru.

```json
"parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
}
```

Pomocí funkce resourceGroup můžete také použít značky ze skupiny prostředků na prostředek. Další informace naleznete v [tématu Použití značek ze skupiny prostředků](../management/tag-resources.md#apply-tags-from-resource-group).

Při použití vnořených šablon k nasazení do více skupin prostředků můžete určit obor pro vyhodnocení funkce resourceGroup. Další informace najdete [v tématu Nasazení prostředků Azure do více než jednoho předplatného nebo skupiny prostředků](cross-resource-group-deployment.md).

### <a name="resource-group-example"></a>Příklad skupiny prostředků

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json) vrátí vlastnosti skupiny prostředků.

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

Vrátí jedinečný identifikátor prostředku. Tuto funkci použijete, pokud je název prostředku nejednoznačný nebo není zřízen v rámci stejné šablony. Formát vráceného identifikátoru se liší v závislosti na tom, zda k nasazení dochází v oboru skupiny prostředků, předplatného, skupiny pro správu nebo klienta.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| subscriptionId |Ne |řetězec (ve formátu GUID) |Výchozí hodnota je aktuální předplatné. Tuto hodnotu zadejte, když potřebujete načíst prostředek v jiném předplatném. Tuto hodnotu zadejte pouze při nasazování v oboru skupiny prostředků nebo předplatného. |
| resourceGroupName |Ne |řetězec |Výchozí hodnota je aktuální skupina prostředků. Tuto hodnotu zadejte, když potřebujete načíst prostředek v jiné skupině prostředků. Tuto hodnotu zadejte pouze při nasazování v oboru skupiny prostředků. |
| resourceType |Ano |řetězec |Typ prostředku včetně oboru názvů poskytovatele prostředků. |
| název_prostředku1 |Ano |řetězec |Název prostředku. |
| název_prostředku2 |Ne |řetězec |Další segment názvu prostředku, v případě potřeby. |

Pokračujte v přidávání názvů prostředků jako parametrů, pokud typ prostředku obsahuje více segmentů.

### <a name="return-value"></a>Návratová hodnota

Při nasazení šablony v oboru skupiny prostředků je ID prostředku vráceno v následujícím formátu:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Při použití v [nasazení na úrovni předplatného](deploy-to-subscription.md)je ID prostředku vráceno v následujícím formátu:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Při použití v [nasazení na úrovni skupiny pro správu](deploy-to-management-group.md) nebo nasazení na úrovni tenanta je ID prostředku vráceno v následujícím formátu:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Pokud chcete ID získat v jiných formátech, přečtěte si následující:

* [rozšířeníResourceId](#extensionresourceid)
* [subscriptionResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

### <a name="remarks"></a>Poznámky

Počet zadaných parametrů se liší v závislosti na tom, zda je prostředek nadřazený nebo podřízený prostředek a zda je prostředek ve stejném předplatném nebo skupině prostředků.

Chcete-li získat ID prostředku pro nadřazený prostředek ve stejném předplatném a skupině prostředků, zadejte typ a název prostředku.

```json
"[resourceId('Microsoft.ServiceBus/namespaces', 'namespace1')]"
```

Chcete-li získat ID prostředku pro podřízený prostředek, věnujte pozornost počtu segmentů v typu prostředku. Zadejte název prostředku pro každý segment typu prostředku. Název segmentu odpovídá prostředku, který existuje pro tuto část hierarchie.

```json
"[resourceId('Microsoft.ServiceBus/namespaces/queues/authorizationRules', 'namespace1', 'queue1', 'auth1')]"
```

Chcete-li získat ID prostředku pro prostředek ve stejném předplatném, ale v jiné skupině prostředků, zadejte název skupiny prostředků.

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts', 'examplestorage')]"
```

Chcete-li získat ID prostředku pro prostředek v jiném předplatném a skupině prostředků, zadejte ID předplatného a název skupiny prostředků.

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

Často je potřeba použít tuto funkci při použití účtu úložiště nebo virtuální sítě v alternativní skupině prostředků. Následující příklad ukazuje, jak lze prostředek ze skupiny externích prostředků snadno použít:

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

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json) vrátí ID prostředku pro účet úložiště ve skupině prostředků:

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
| differentSubOutput | Řetězec | /subscriptions/11111111-1111-1111-1111-11111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| vnořený resourceoutput | Řetězec | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/serverName/databases/databases/databaseName |

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

### <a name="remarks"></a>Poznámky

Při použití vnořené šablony k nasazení do více odběrů, můžete určit obor pro vyhodnocení funkce předplatného. Další informace najdete [v tématu Nasazení prostředků Azure do více než jednoho předplatného nebo skupiny prostředků](cross-resource-group-deployment.md).

### <a name="subscription-example"></a>Příklad předplatného

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json) zobrazuje funkci odběru volanou v části výstupy.

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

Vrátí jedinečný identifikátor pro prostředek nasazený na úrovni předplatného.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| subscriptionId |Ne |řetězec (ve formátu GUID) |Výchozí hodnota je aktuální předplatné. Tuto hodnotu zadejte, když potřebujete načíst prostředek v jiném předplatném. |
| resourceType |Ano |řetězec |Typ prostředku včetně oboru názvů poskytovatele prostředků. |
| název_prostředku1 |Ano |řetězec |Název prostředku. |
| název_prostředku2 |Ne |řetězec |Další segment názvu prostředku, v případě potřeby. |

Pokračujte v přidávání názvů prostředků jako parametrů, pokud typ prostředku obsahuje více segmentů.

### <a name="return-value"></a>Návratová hodnota

Identifikátor je vrácen v následujícím formátu:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Poznámky

Tuto funkci použijete k získání ID prostředku pro prostředky, které jsou [nasazeny do předplatného,](deploy-to-subscription.md) nikoli do skupiny prostředků. Vrácené ID se liší od hodnoty vrácené funkcí [resourceId](#resourceid) tím, že nezahrnuje hodnotu skupiny prostředků.

### <a name="subscriptionresourceid-example"></a>příklad subscriptionResourceID

Následující šablona přiřazuje předdefinovanou roli. Můžete ji nasadit do skupiny prostředků nebo předplatného. Používá funkci subscriptionResourceId k získání ID prostředku pro předdefinované role.

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

Vrátí jedinečný identifikátor pro prostředek nasazený na úrovni klienta.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| resourceType |Ano |řetězec |Typ prostředku včetně oboru názvů poskytovatele prostředků. |
| název_prostředku1 |Ano |řetězec |Název prostředku. |
| název_prostředku2 |Ne |řetězec |Další segment názvu prostředku, v případě potřeby. |

Pokračujte v přidávání názvů prostředků jako parametrů, pokud typ prostředku obsahuje více segmentů.

### <a name="return-value"></a>Návratová hodnota

Identifikátor je vrácen v následujícím formátu:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Poznámky

Tuto funkci použijete k získání ID prostředku pro prostředek, který je nasazen do klienta. Vrácené ID se liší od hodnot vrácených jinými funkcemi ID prostředků tím, že nezahrnuje hodnoty skupiny prostředků nebo předplatného.

## <a name="next-steps"></a>Další kroky

* Popis oddílů v šabloně Azure Resource Manager u najdete v tématu [Vytváření šablon Azure Resource Manageru](template-syntax.md).
* Pokud chcete sloučit víc šablon, přečtěte si informace [o použití propojených šablon ve Správci prostředků Azure](linked-templates.md).
* Chcete-li itrerate zadaný počet časů při vytváření typu prostředku, najdete v [tématu vytvoření více instancí prostředků ve Správci prostředků Azure](copy-resources.md).
* Informace o tom, jak nasadit vytvořenou šablonu, najdete [v tématu Nasazení aplikace pomocí šablony Azure Resource Manageru](deploy-powershell.md).

