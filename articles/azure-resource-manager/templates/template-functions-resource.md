---
title: Funkce šablon – prostředky
description: Popisuje funkce, které se použijí v šabloně Azure Resource Manager k načtení hodnot o prostředcích.
ms.topic: conceptual
ms.date: 09/03/2020
ms.openlocfilehash: dd040715cc8fb1339c6054c53007dbcd08e2cbdb
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91816809"
---
# <a name="resource-functions-for-arm-templates"></a>Funkce prostředků pro šablony ARM

Správce prostředků poskytuje následující funkce pro získání hodnot prostředků v šabloně Azure Resource Manager (ARM):

* [extensionResourceId](#extensionresourceid)
* [seznamu](#list)
* [pickZones](#pickzones)
* [dodavateli](#providers)
* [odkaz](#reference)
* [resourceGroup](#resourcegroup)
* [Prostředku](#resourceid)
* [formě](#subscription)
* [subscriptionResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

Chcete-li získat hodnoty z parametrů, proměnných nebo aktuálního nasazení, přečtěte si téma [funkce hodnot nasazení](template-functions-deployment.md).

## <a name="extensionresourceid"></a>extensionResourceId

`extensionResourceId(resourceId, resourceType, resourceName1, [resourceName2], ...)`

Vrátí ID prostředku pro [prostředek rozšíření](../management/extension-resource-types.md), což je typ prostředku, který se použije na jiný prostředek, aby se mohl přidat k jeho funkcím.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| resourceId |Ano |řetězec |ID prostředku prostředku, na který se prostředek rozšíření používá |
| resourceType |Ano |řetězec |Typ prostředku včetně oboru názvů poskytovatele prostředků |
| resourceName1 |Ano |řetězec |Název prostředku |
| resourceName2 |No |řetězec |Další segment názvu prostředku (v případě potřeby). |

Pokračujte v přidávání názvů prostředků jako parametrů, pokud typ prostředku obsahuje více segmentů.

### <a name="return-value"></a>Vrácená hodnota

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
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

Vlastní definice zásad nasazená do skupiny pro správu je implementována jako prostředek rozšíření. Chcete-li vytvořit a přiřadit zásadu, nasaďte následující šablonu do skupiny pro správu.

:::code language="json" source="~/quickstart-templates/managementgroup-deployments/mg-policy/azuredeploy.json":::

Předdefinované definice zásad jsou prostředky na úrovni tenanta. Příklad nasazení předdefinované definice zásad najdete v tématu [tenantResourceId](#tenantresourceid).

<a id="listkeys"></a>
<a id="list"></a>

## <a name="list"></a>seznamu

`list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)`

Syntaxe této funkce se liší podle názvu operací seznamu. Každá implementace vrací hodnoty pro typ prostředku, který podporuje operaci list. Název operace musí začínat na `list` . Některé běžné použití jsou `listKeys` , `listKeyValue` a `listSecrets` .

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| resourceName nebo resourceIdentifier |Ano |řetězec |Jedinečný identifikátor prostředku |
| apiVersion |Ano |řetězec |Verze rozhraní API stavu modulu runtime prostředku Obvykle ve formátu **yyyy-MM-DD**. |
| functionValues |No |object | Objekt, který má hodnoty pro funkci. Tento objekt Poskytněte jenom pro funkce, které podporují příjem objektu s hodnotami parametrů, jako je například **listAccountSas** v účtu úložiště. Příklad předávání hodnot funkcí je uveden v tomto článku. |

### <a name="valid-uses"></a>Platná použití

Funkce seznamu se dá použít ve vlastnostech definice prostředků. Nepoužívejte funkci seznamu, která zveřejňuje citlivé informace v části výstupy šablony. Výstupní hodnoty jsou uložené v historii nasazení a můžou je načíst uživatel se zlými úmysly.

Při použití s [iterací vlastnosti](copy-properties.md)můžete použít funkce seznamu, `input` protože výraz je přiřazen vlastnosti prostředku. Nelze je použít s, `count` protože počet musí být určen před vyřešením funkce seznamu.

### <a name="implementations"></a>Implementace

Možná použití seznamu * jsou uvedena v následující tabulce.

| Typ prostředku | Název funkce |
| ------------- | ------------- |
| Microsoft. addons/supportProviders | listsupportplaninfo |
| Microsoft. AnalysisServices/servery | [listGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft. ApiManagement/Service/authorizationServers | [listSecrets](/rest/api/apimanagement/2019-12-01/authorizationserver/listsecrets) |
| Microsoft. ApiManagement/Service/gateways | [Klíče listkey](/rest/api/apimanagement/2019-12-01/gateway/listkeys) |
| Microsoft. ApiManagement/Service/identityProviders | [listSecrets](/rest/api/apimanagement/2019-12-01/identityprovider/listsecrets) |
| Microsoft. ApiManagement/Service/namedValues | [listValue](/rest/api/apimanagement/2019-12-01/namedvalue/listvalue) |
| Microsoft. ApiManagement/Service/openidConnectProviders | [listSecrets](/rest/api/apimanagement/2019-12-01/openidconnectprovider/listsecrets) |
| Microsoft. AppConfiguration/configurationStores | [Klíče listkey](/rest/api/appconfiguration/configurationstores/listkeys) |
| Microsoft. AppPlatform/pružina | [listTestKeys](/rest/api/azurespringclould/services/listtestkeys) |
| Microsoft. Automation/automationAccounts | [Klíče listkey](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.Batch/batchAccounts | [klíče listkey](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft.BatchAI/pracovní prostory/experimenty/úlohy | [listoutputfiles](/rest/api/batchai/jobs/listoutputfiles) |
| Microsoft. blockchain/blockchainMembers | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Microsoft. blockchain/blockchainMembers/transactionNodes | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Microsoft. BotService/botServices/kanály | [listChannelWithKeys](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/botservice/resource-manager/Microsoft.BotService/stable/2020-06-02/botservice.json#L553) |
| Microsoft. cache/Redis | [Klíče listkey](/rest/api/redis/redis/listkeys) |
| Microsoft. Cognitiveservices Account/účty | [Klíče listkey](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Microsoft. ContainerRegistry/Registry | [listBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/getbuildsourceuploadurl) |
| Microsoft. ContainerRegistry/Registry | [listCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Microsoft. ContainerRegistry/Registry | [listUsages](/rest/api/containerregistry/registries/listusages) |
| Microsoft. ContainerRegistry/Registry/agentpools | listQueueStatus |
| Microsoft. ContainerRegistry/Registry/buildTasks | listSourceRepositoryProperties |
| Microsoft. ContainerRegistry/Registry/buildTasks/kroky | listBuildArguments |
| Microsoft. ContainerRegistry/Registry/taskruns | listDetails |
| Microsoft. ContainerRegistry/Registry/Webhooky | [listEvents](/rest/api/containerregistry/webhooks/listevents) |
| Microsoft. ContainerRegistry/Registry/běhy | [listLogSasUrl](/rest/api/containerregistry/runs/getlogsasurl) |
| Microsoft. ContainerRegistry/Registry/úkoly | [listDetails](/rest/api/containerregistry/tasks/getdetails) |
| Microsoft. ContainerService/managedClusters | [listClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft. ContainerService/managedClusters | [listClusterMonitoringUserCredential](/rest/api/aks/managedclusters/listclustermonitoringusercredentials) |
| Microsoft. ContainerService/managedClusters | [listClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Microsoft. ContainerService/managedClusters/accessProfiles | [listCredential](/rest/api/aks/managedclusters/getaccessprofile) |
| Microsoft. DataBox/Jobs | listCredentials |
| Microsoft. DataFactory/DataFactory/gateways | listauthkeys |
| Microsoft. DataFactory/Factory/integrationruntimes | [listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft. DataLakeAnalytics/Accounts/storageAccounts/Containers | [listSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Microsoft. datashare/Accounts/shares | [listSynchronizations](/rest/api/datashare/shares/listsynchronizations) |
| Microsoft. datashare/Accounts/shareSubscriptions | [listSourceShareSynchronizationSettings](/rest/api/datashare/sharesubscriptions/listsourcesharesynchronizationsettings) |
| Microsoft. datashare/Accounts/shareSubscriptions | [listSynchronizationDetails](/rest/api/datashare/sharesubscriptions/listsynchronizationdetails) |
| Microsoft. datashare/Accounts/shareSubscriptions | [listSynchronizations](/rest/api/datashare/sharesubscriptions/listsynchronizations) |
| Microsoft. Devices/iotHubs | [klíče listkey](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft. Devices/iotHubs/iotHubKeys | [klíče listkey](/rest/api/iothub/iothubresource/getkeysforkeyname) |
| Microsoft. Devices/provisioningServices/Keys | [klíče listkey](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft. Devices/provisioningServices | [klíče listkey](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft. DevTestLab/Labs | [ListVhds](/rest/api/dtl/labs/listvhds) |
| Microsoft. DevTestLab/Labs/plány | [ListApplicable](/rest/api/dtl/schedules/listapplicable) |
| Microsoft. DevTestLab/Labs/Users/serviceFabrics | [ListApplicableSchedules](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft. DevTestLab/Labs/virtualMachines | [ListApplicableSchedules](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft.DocumentDB/databaseAccounts | [listConnectionStrings](/rest/api/cosmos-db-resource-provider/2020-06-01-preview/databaseaccounts/listconnectionstrings) |
| Microsoft.DocumentDB/databaseAccounts | [Klíče listkey](/rest/api/cosmos-db-resource-provider/2020-06-01-preview/databaseaccounts/listkeys) |
| Microsoft.DocumentDB/databaseAccounts/notebookWorkspaces | [listConnectionInfo](/rest/api/cosmos-db-resource-provider/2020-04-01/notebookworkspaces/listconnectioninfo) |
| Microsoft. DomainRegistration | [listDomainRecommendations](/rest/api/appservice/domains/listrecommendations) |
| Microsoft. DomainRegistration/topLevelDomains | [listAgreements](/rest/api/appservice/topleveldomains/listagreements) |
| Microsoft. EventGrid/domény | [Klíče listkey](/rest/api/eventgrid/version2020-06-01/domains/listsharedaccesskeys) |
| Microsoft. EventGrid/témata | [Klíče listkey](/rest/api/eventgrid/version2020-06-01/topics/listsharedaccesskeys) |
| Microsoft. EventHub/obory názvů/autorizačních pravidel | [klíče listkey](/rest/api/eventhub) |
| Microsoft. EventHub/obory názvů/disasterRecoveryConfigs/autorizačních pravidel | [klíče listkey](/rest/api/eventhub) |
| Microsoft. EventHub/obory názvů/eventhubs/autorizačních pravidel | [klíče listkey](/rest/api/eventhub) |
| Microsoft. ImportExport/Jobs | [listBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Microsoft. Kusto/clustery/databáze | [ListPrincipals](/rest/api/azurerekusto/databases/listprincipals) |
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
| Microsoft. Logic/Workflows/běhy/Actions | [listExpressionTraces](/rest/api/logic/workflowrunactions/listexpressiontraces) |
| Microsoft. Logic/Workflows/běhy/Actions/opakování | [listExpressionTraces](/rest/api/logic/workflowrunactionrepetitions/listexpressiontraces) |
| Microsoft. Logic/Workflows/Triggers | [listCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Microsoft. Logic/Workflows/Versions/Triggers | [listCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Microsoft. MachineLearning/WebServices | [klíče listkey](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft. MachineLearning/pracovní prostory | listworkspacekeys |
| Microsoft. MachineLearningServices/pracovní prostory/výpočetní služby | [Klíče listkey](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listkeys) |
| Microsoft. MachineLearningServices/pracovní prostory/výpočetní služby | [listNodes](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listnodes) |
| Microsoft. MachineLearningServices/pracovní prostory | [Klíče listkey](/rest/api/azureml/workspacesandcomputes/workspaces/listkeys) |
| Microsoft. Maps/Accounts | [Klíče listkey](/rest/api/maps-management/accounts/listkeys) |
| Microsoft. Media/MediaServices/Assets | [listContainerSas](/rest/api/media/assets/listcontainersas) |
| Microsoft. Media/MediaServices/Assets | [listStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Microsoft. Media/MediaServices/streamingLocators | [listContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft. Media/MediaServices/streamingLocators | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| Microsoft. Network/applicationSecurityGroups | listIpConfigurations |
| Microsoft. NotificationHubs/obory názvů/autorizačních pravidel | [klíče listkey](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft. NotificationHubs/obory názvů/NotificationHubs/autorizačních pravidel | [klíče listkey](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft. OperationalInsights/pracovní prostory | [list](/rest/api/loganalytics/workspaces/list) |
| Microsoft. OperationalInsights/pracovní prostory | Klíče listkey |
| Microsoft. PolicyInsights/nápravy | [listDeployments](/rest/api/policy-insights/remediations/listdeploymentsatresourcegroup) |
| Microsoft. RedHatOpenShift/openShiftClusters | [listCredentials](/rest/api/openshift/openshiftclusters/listcredentials) |
| Microsoft. Relay/obory názvů/autorizačních pravidel | [klíče listkey](/rest/api/relay/namespaces/listkeys) |
| Microsoft. Relay/obory názvů/disasterRecoveryConfigs/autorizačních pravidel | klíče listkey |
| Microsoft. Relay/obory názvů/HybridConnections/autorizačních pravidel | [klíče listkey](/rest/api/relay/hybridconnections/listkeys) |
| Microsoft. Relay/obory názvů/WcfRelays/autorizačních pravidel | [klíče listkey](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft. Search/searchServices | [listAdminKeys](/rest/api/searchmanagement/adminkeys/get) |
| Microsoft. Search/searchServices | [listQueryKeys](/rest/api/searchmanagement/querykeys/listbysearchservice) |
| Microsoft. ServiceBus/obory názvů/autorizačních pravidel | [klíče listkey](/rest/api/servicebus/stable/namespaces%20-%20authorization%20rules/listkeys) |
| Microsoft. ServiceBus/obory názvů/disasterRecoveryConfigs/autorizačních pravidel | [klíče listkey](/rest/api/servicebus/stable/disasterrecoveryconfigs/listkeys) |
| Microsoft. ServiceBus/obory názvů/Queues/autorizačních pravidel | [klíče listkey](/rest/api/servicebus/stable/queues%20-%20authorization%20rules/listkeys) |
| Microsoft. ServiceBus/obory názvů/témata/autorizačních pravidel | [klíče listkey](/rest/api/servicebus/stable/topics%20–%20authorization%20rules/listkeys) |
| Microsoft. SignalRService/Signaler | [klíče listkey](/rest/api/signalr/signalr/listkeys) |
| Microsoft. Storage/storageAccounts | [listAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft. Storage/storageAccounts | [klíče listkey](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft. Storage/storageAccounts | [listServiceSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Microsoft. StorSimple/manažeři/zařízení | [listFailoverSets](/rest/api/storsimple/devices/listfailoversets) |
| Microsoft. StorSimple/manažeři/zařízení | [listFailoverTargets](/rest/api/storsimple/devices/listfailovertargets) |
| Microsoft. StorSimple/– manažeři | [listActivationKey](/rest/api/storsimple/managers/getactivationkey) |
| Microsoft. StorSimple/– manažeři | [listPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Microsoft. synapse/pracovní prostory/integrationRuntimes | [listAuthKeys](/rest/api/synapse/integrationruntimeauthkeys/list) |
| Microsoft. Web/connectionGateways | ListStatus |
| Microsoft. Web/připojení | listconsentlinks |
| Microsoft. Web/customApis | listWsdlInterfaces |
| Microsoft. Web/umístění | listwsdlinterfaces |
| Microsoft. Web/apimanagementaccounts/API/Connections | listconnectionkeys |
| Microsoft. Web/apimanagementaccounts/API/Connections | listsecrets |
| Microsoft. Web/weby/zálohy | [list](/rest/api/appservice/webapps/listbackups) |
| Microsoft. Web/weby/konfigurace | [list](/rest/api/appservice/webapps/listconfigurations) |
| Microsoft. Web/weby/funkce | [klíče listkey](/rest/api/appservice/webapps/listfunctionkeys)
| Microsoft. Web/weby/funkce | [listsecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| Microsoft. Web/weby/hybridconnectionnamespaces/Relay | [klíče listkey](/rest/api/appservice/appserviceplans/listhybridconnectionkeys) |
| Microsoft. Web/weby | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| Microsoft. Web/weby/sloty/funkce | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |
| Microsoft. Web/weby/sloty/zálohy | [list](/rest/api/appservice/webapps/listbackupsslot) |
| Microsoft. Web/weby/sloty/config | [list](/rest/api/appservice/webapps/listconfigurationsslot) |
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

### <a name="return-value"></a>Vrácená hodnota

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

Použijete-li funkci **list** v prostředku, který je podmíněně nasazen, je funkce vyhodnocena i v případě, že prostředek není nasazen. Pokud funkce **seznamu** odkazuje na prostředek, který neexistuje, zobrazí se chyba. Použijte funkci **if** a ujistěte se, že je funkce vyhodnocena pouze při nasazení prostředku. Podívejte se na [funkci IF](template-functions-logical.md#if) pro ukázkovou šablonu, která používá if a list s podmíněně nasazeným prostředkem.

### <a name="list-example"></a>Příklad seznamu

Následující příklad používá klíče listkey při nastavování hodnoty pro [skripty nasazení](deployment-script-template.md).

```json
"storageAccountSettings": {
    "storageAccountName": "[variables('storageAccountName')]",
    "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
}
```

Následující příklad ukazuje funkci seznamu, která přebírá parametr. V tomto případě je funkce **listAccountSas**. Předejte objekt pro čas vypršení platnosti. Čas vypršení platnosti musí být v budoucnosti.

```json
"parameters": {
    "accountSasProperties": {
        "type": "object",
        "defaultValue": {
            "signedServices": "b",
            "signedPermission": "r",
            "signedExpiry": "2020-08-20T11:00:00Z",
            "signedResourceTypes": "s"
        }
    }
},
...
"sasToken": "[listAccountSas(parameters('storagename'), '2018-02-01', parameters('accountSasProperties')).accountSasToken]"
```

## <a name="pickzones"></a>pickZones

`pickZones(providerNamespace, resourceType, location, [numberOfZones], [offset])`

Určuje, zda typ prostředku podporuje zóny pro oblast.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| providerNamespace | Ano | řetězec | Obor názvů poskytovatele prostředků pro typ prostředku, který má kontrolovat podporu zóny. |
| resourceType | Ano | řetězec | Typ prostředku pro kontrolu podpory zón. |
| location | Ano | řetězec | Oblast pro kontrolu podpory zón. |
| numberOfZones | No | integer | Počet logických zón, které mají být vráceny. Výchozí hodnota je 1. Číslo musí být kladné celé číslo od 1 do 3.  U prostředků s jednou zónou použijte 1. U prostředků s více zónami musí být hodnota menší nebo rovna počtu podporovaných zón. |
| posun | No | integer | Posun od počáteční logické zóny. Funkce vrátí chybu, pokud posun plus numberOfZones překračuje počet podporovaných zón. |

### <a name="return-value"></a>Vrácená hodnota

Pole s podporovanými zónami. Při použití výchozích hodnot pro posun a numberOfZones typ prostředku a oblast, která podporuje zóny, vrátí následující pole:

```json
[
    "1"
]
```

Pokud `numberOfZones` je parametr nastaven na hodnotu 3, vrátí:

```json
[
    "1",
    "2",
    "3"
]
```

Pokud typ prostředku nebo oblast nepodporuje zóny, bude vráceno prázdné pole.

```json
[
]
```

### <a name="pickzones-example"></a>Příklad pickZones

Následující šablona ukazuje tři výsledky pro použití funkce pickZones.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "functions": [],
    "variables": {},
    "resources": [],
    "outputs": {
        "supported": {
            "type": "array",
            "value": "[pickZones('Microsoft.Compute', 'virtualMachines', 'westus2')]"
        },
        "notSupportedRegion": {
            "type": "array",
            "value": "[pickZones('Microsoft.Compute', 'virtualMachines', 'northcentralus')]"
        },
        "notSupportedType": {
            "type": "array",
            "value": "[pickZones('Microsoft.Cdn', 'profiles', 'westus2')]"
        }
    }
}
```

Výstup z předchozích příkladů vrátí tři pole.

| Název | Typ | Hodnota |
| ---- | ---- | ----- |
| Podporuje se | array | ["1"] |
| notSupportedRegion | array | [] |
| notSupportedType | array | [] |

Pomocí odpovědi z pickZones můžete určit, jestli se má pro zóny zadat hodnota null, nebo přiřadit virtuální počítače k různým zónám. Následující příklad nastaví hodnotu pro zónu na základě dostupnosti zón.

```json
"zones": {
    "value": "[if(not(empty(pickZones('Microsoft.Compute', 'virtualMachines', 'westus2'))), string(add(mod(copyIndex(),3),1)), json('null'))]"
},
```

## <a name="providers"></a>dodavateli

`providers(providerNamespace, [resourceType])`

Vrátí informace o poskytovateli prostředků a jeho podporovaných typech prostředků. Pokud nezadáte typ prostředku, funkce vrátí všechny podporované typy pro poskytovatele prostředků.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| providerNamespace |Ano |řetězec |Obor názvů poskytovatele |
| resourceType |No |řetězec |Typ prostředku v rámci zadaného oboru názvů. |

### <a name="return-value"></a>Vrácená hodnota

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
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

## <a name="reference"></a>reference

`reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])`

Vrátí objekt představující běhový stav prostředku.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| resourceName nebo resourceIdentifier |Ano |řetězec |Název nebo jedinečný identifikátor prostředku Při odkazování na prostředek v aktuální šabloně zadejte jako parametr jenom název prostředku. Když odkazujete na dříve nasazený prostředek nebo pokud je název prostředku dvojznačný, zadejte ID prostředku. |
| apiVersion |No |řetězec |Verze rozhraní API zadaného prostředku **Tento parametr je vyžadován, pokud prostředek není zřízen v rámci stejné šablony.** Obvykle ve formátu **yyyy-MM-DD**. Platné verze rozhraní API pro váš prostředek naleznete v tématu [Reference k šabloně](/azure/templates/). |
| Kompletní |No |řetězec |Hodnota, která určuje, zda má být vrácen úplný objekt prostředku. Pokud nezadáte `'Full'` , vrátí se pouze objekt vlastnosti prostředku. Úplný objekt obsahuje hodnoty, jako je ID a umístění prostředku. |

### <a name="return-value"></a>Vrácená hodnota

Každý typ prostředku vrátí různé vlastnosti referenční funkce. Funkce nevrací jeden, předdefinovaný formát. Vrácená hodnota se také liší v závislosti na hodnotě `'Full'` argumentu. Chcete-li zobrazit vlastnosti pro typ prostředku, vraťte objekt v části výstupy, jak je znázorněno v příkladu.

### <a name="remarks"></a>Poznámky

Odkazovaná funkce načte běhový stav buď dříve nasazeného prostředku, nebo prostředku nasazeného v aktuální šabloně. Tento článek ukazuje příklady pro oba scénáře.

Obvykle použijete **odkazovou** funkci k vrácení konkrétní hodnoty z objektu, jako je například identifikátor URI koncového bodu objektu BLOB nebo plně kvalifikovaný název domény.

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

Použijte `'Full'` v případě, že potřebujete hodnoty prostředků, které nejsou součástí schématu vlastností. Pokud třeba chcete nastavit zásady přístupu trezoru klíčů, Získejte vlastnosti identity pro virtuální počítač.

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

Odkazovaná funkce se dá použít jenom ve vlastnostech definice prostředků a v části výstupy šablony nebo nasazení. Při použití s [iterací vlastnosti](copy-properties.md)můžete použít referenční funkci pro, `input` protože výraz je přiřazen vlastnosti prostředku.

Pomocí referenční funkce nelze nastavit hodnotu `count` vlastnosti ve smyčce kopírování. Můžete použít k nastavení dalších vlastností ve smyčce. Odkaz je blokován pro vlastnost Count, protože tato vlastnost musí být určena před vyřešením Referenční funkce.

Chcete-li použít funkci reference Function nebo Any list * v části výstupy vnořené šablony, je nutné nastavit,  ```expressionEvaluationOptions``` aby používalo [vnitřní vyhodnocení oboru](linked-templates.md#expression-evaluation-scope-in-nested-templates) , nebo použít propojený namísto vnořené šablony.

Použijete-li **referenční** funkci v prostředku, který je podmíněně nasazen, je funkce vyhodnocena i v případě, že prostředek není nasazen.  Pokud **odkazovaná** funkce odkazuje na prostředek, který neexistuje, zobrazí se chyba. Použijte funkci **if** a ujistěte se, že je funkce vyhodnocena pouze při nasazení prostředku. Podívejte se na [funkci IF](template-functions-logical.md#if) pro ukázkovou šablonu, která používá if a odkaz s podmíněně nasazeným prostředkem.

### <a name="implicit-dependency"></a>Implicitní závislost

Pomocí referenční funkce implicitně deklarujete, že jeden prostředek závisí na jiném prostředku, je-li odkazovaný prostředek zřízen ve stejné šabloně a odkazujete na prostředek podle jeho názvu (nikoli ID prostředku). Nemusíte také používat vlastnost dependsOn. Funkce není vyhodnocena, dokud není dokončeno nasazení odkazovaného prostředku.

### <a name="resource-name-or-identifier"></a>Název nebo identifikátor prostředku

Když odkazujete na prostředek, který je nasazený ve stejné šabloně, zadejte název prostředku.

```json
"value": "[reference(parameters('storageAccountName'))]"
```

Když odkazujete na prostředek, který není nasazený ve stejné šabloně, zadejte ID prostředku a `apiVersion` .

```json
"value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

Aby nedocházelo k nejednoznačnosti prostředků, na které odkazujete, můžete zadat plně kvalifikovaný identifikátor prostředku.

```json
"value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName')))]"
```

Při sestavování plně kvalifikovaného odkazu na prostředek, pořadí pro kombinování segmentů z typu a název není pouhým zřetězením obou. Místo toho je nutné po oboru názvů použít sekvenci dvojic *typů a názvů* z nejméně specifických na nejvíc:

**{Resource-Provider-Namespace}/{Parent-Resource-Type}/{Parent-Resource-Name} [/{Child-Resource-Type}/{Child-Resource-Name}]**

Příklad:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` je správné, není `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` správné.

Pro zjednodušení vytváření ID prostředku použijte `resourceId()` funkce popsané v tomto dokumentu namísto `concat()` funkce.

### <a name="get-managed-identity"></a>Získat spravovanou identitu

[Spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md) jsou [typy prostředků rozšíření](../management/extension-resource-types.md) , které se pro některé prostředky vytvoří implicitně. Vzhledem k tomu, že spravovaná identita není explicitně definována v šabloně, musíte odkazovat na prostředek, na který je identita použita. Použijte `Full` k získání všech vlastností, včetně implicitně vytvořené identity.

Vzor je:

`"[reference(resourceId(<resource-provider-namespace>, <resource-name>, <API-version>, 'Full').Identity.propertyName]"`

Pokud například chcete získat ID objektu zabezpečení pro spravovanou identitu, která se používá pro virtuální počítač, použijte:

```json
"[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')),'2019-12-01', 'Full').identity.principalId]",
```

Nebo pokud chcete získat ID tenanta pro spravovanou identitu, která se aplikuje na sadu škálování virtuálního počítače, použijte:

```json
"[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), 2019-12-01, 'Full').Identity.tenantId]"
```

### <a name="reference-example"></a>Příklad odkazu

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json) nasadí prostředek a odkazuje na tento prostředek.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

Vrátí objekt, který představuje aktuální skupinu prostředků.

### <a name="return-value"></a>Vrácená hodnota

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

`resourceGroup()`Funkci nelze použít v šabloně, která je [nasazena na úrovni předplatného](deploy-to-subscription.md). Dá se použít jenom v šablonách, které se nasazují do skupiny prostředků. Funkci můžete použít `resourceGroup()` v [propojené nebo vnořené šabloně (s vnitřním rozsahem)](linked-templates.md) , která cílí na skupinu prostředků, i když je nadřazená šablona nasazena do předplatného. V takovém případě je propojená nebo vnořená šablona nasazena na úrovni skupiny prostředků. Další informace o cílení skupiny prostředků v nasazení na úrovni předplatného najdete v tématu [nasazení prostředků Azure do více než jednoho předplatného nebo skupiny prostředků](cross-scope-deployment.md).

Běžné použití funkce Resource je vytvořit prostředky ve stejném umístění jako skupina prostředků. Následující příklad používá umístění skupiny prostředků pro výchozí hodnotu parametru.

```json
"parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
}
```

K použití značek ze skupiny prostředků na prostředek můžete také použít funkci Resource. Další informace najdete v tématu [použití značek ze skupiny prostředků](../management/tag-resources.md#apply-tags-from-resource-group).

Při použití vnořených šablon k nasazení do více skupin prostředků můžete zadat obor pro vyhodnocení funkce Resource. Další informace najdete v tématu [nasazení prostředků Azure do více než jednoho předplatného nebo skupiny prostředků](cross-scope-deployment.md).

### <a name="resource-group-example"></a>Příklad skupiny prostředků

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json) vrátí vlastnosti skupiny prostředků.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

`resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2], ...)`

Vrátí jedinečný identifikátor prostředku. Tuto funkci použijete, pokud je název prostředku dvojznačný nebo není zřízený v rámci stejné šablony. Formát vráceného identifikátoru se liší v závislosti na tom, jestli se nasazení stane v oboru skupiny prostředků, předplatného, skupiny pro správu nebo tenanta.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| subscriptionId |No |řetězec (ve formátu GUID) |Výchozí hodnota je aktuální předplatné. Tuto hodnotu zadejte, když potřebujete načíst prostředek v jiném předplatném. Tuto hodnotu Poskytněte jenom při nasazení v oboru skupiny prostředků nebo předplatného. |
| resourceGroupName |No |řetězec |Výchozí hodnota je aktuální skupina prostředků. Tuto hodnotu zadejte, když potřebujete načíst prostředek v jiné skupině prostředků. Tuto hodnotu Poskytněte jenom při nasazení v oboru skupiny prostředků. |
| resourceType |Ano |řetězec |Typ prostředku včetně oboru názvů poskytovatele prostředků |
| resourceName1 |Ano |řetězec |Název prostředku |
| resourceName2 |No |řetězec |Další segment názvu prostředku (v případě potřeby). |

Pokračujte v přidávání názvů prostředků jako parametrů, pokud typ prostředku obsahuje více segmentů.

### <a name="return-value"></a>Vrácená hodnota

Když je šablona nasazená v oboru skupiny prostředků, vrátí se ID prostředku v následujícím formátu:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Můžete použít funkci resourceId pro jiné obory nasazení, ale formát ID se změní.

Pokud během nasazování do předplatného použijete resourceId, ID prostředku se vrátí v následujícím formátu:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Pokud použijete resourceId při nasazení do skupiny pro správu nebo tenanta, ID prostředku se vrátí v následujícím formátu:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Chcete-li předejít nejasnostem, doporučujeme, abyste při práci s prostředky nasazenými do předplatného, skupiny pro správu nebo tenanta nepoužívali resourceId. Místo toho použijte funkci ID, která je navržena pro obor.

U [prostředků na úrovni předplatného](deploy-to-subscription.md)použijte funkci [subscriptionResourceId](#subscriptionresourceid) .

U [prostředků na úrovni skupiny pro správu](deploy-to-management-group.md)použijte funkci [extensionResourceId](#extensionresourceid) , která odkazuje na prostředek, který je implementovaný jako rozšíření skupiny pro správu. Například vlastní definice zásad, které jsou nasazeny do skupiny pro správu, jsou rozšířeními skupiny pro správu. Pomocí funkce [tenantResourceId](#tenantresourceid) můžete odkazovat na prostředky, které jsou nasazeny do tenanta, ale jsou k dispozici ve vaší skupině pro správu. Například předdefinované definice zásad jsou implementovány jako prostředky na úrovni tenanta.

U [prostředků na úrovni tenanta](deploy-to-tenant.md)použijte funkci [tenantResourceId](#tenantresourceid) . Použijte tenantResourceId pro předdefinované definice zásad, protože jsou implementované na úrovni tenanta.

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
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
| sameRGOutput | Řetězec | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | Řetězec | /subscriptions/{current-sub-id}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | Řetězec | /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | Řetězec | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/serverName/databases/databaseName |

## <a name="subscription"></a>předplatné

`subscription()`

Vrátí podrobnosti o předplatném pro aktuální nasazení.

### <a name="return-value"></a>Vrácená hodnota

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

Při použití vnořených šablon k nasazení do více předplatných můžete zadat obor pro vyhodnocení funkce předplatného. Další informace najdete v tématu [nasazení prostředků Azure do více než jednoho předplatného nebo skupiny prostředků](cross-scope-deployment.md).

### <a name="subscription-example"></a>Příklad předplatného

Následující [příklad šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json) ukazuje funkci předplatného nazvanou v části výstupy.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

`subscriptionResourceId([subscriptionId], resourceType, resourceName1, [resourceName2], ...)`

Vrátí jedinečný identifikátor prostředku nasazeného na úrovni předplatného.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| subscriptionId |No |řetězec (ve formátu GUID) |Výchozí hodnota je aktuální předplatné. Tuto hodnotu zadejte, když potřebujete načíst prostředek v jiném předplatném. |
| resourceType |Ano |řetězec |Typ prostředku včetně oboru názvů poskytovatele prostředků |
| resourceName1 |Ano |řetězec |Název prostředku |
| resourceName2 |No |řetězec |Další segment názvu prostředku (v případě potřeby). |

Pokračujte v přidávání názvů prostředků jako parametrů, pokud typ prostředku obsahuje více segmentů.

### <a name="return-value"></a>Vrácená hodnota

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
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

`tenantResourceId(resourceType, resourceName1, [resourceName2], ...)`

Vrátí jedinečný identifikátor prostředku nasazeného na úrovni tenanta.

### <a name="parameters"></a>Parametry

| Parametr | Požaduje se | Typ | Popis |
|:--- |:--- |:--- |:--- |
| resourceType |Ano |řetězec |Typ prostředku včetně oboru názvů poskytovatele prostředků |
| resourceName1 |Ano |řetězec |Název prostředku |
| resourceName2 |No |řetězec |Další segment názvu prostředku (v případě potřeby). |

Pokračujte v přidávání názvů prostředků jako parametrů, pokud typ prostředku obsahuje více segmentů.

### <a name="return-value"></a>Vrácená hodnota

Identifikátor se vrátí v následujícím formátu:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Poznámky

Pomocí této funkce získáte ID prostředku pro prostředek, který je nasazený do tenanta. Vrácené ID se liší od hodnot vrácených jinými funkcemi ID prostředků bez zahrnutí skupin prostředků nebo hodnot předplatného.

### <a name="tenantresourceid-example"></a>Příklad tenantResourceId

Předdefinované definice zásad jsou prostředky na úrovni tenanta. Pokud chcete nasadit přiřazení zásady, které odkazuje na definici předdefinované zásady, použijte funkci tenantResourceId.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "policyAssignmentName": {
      "type": "string",
      "defaultValue": "[guid(parameters('policyDefinitionID'), resourceGroup().name)]",
      "metadata": {
        "description": "Specifies the name of the policy assignment, can be used defined or an idempotent name as the defaultValue provides."
      }
    },
    "policyDefinitionID": {
      "type": "string",
      "defaultValue": "0a914e76-4921-4c19-b460-a2d36003525a",
      "metadata": {
        "description": "Specifies the ID of the policy definition or policy set definition being assigned."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "name": "[parameters('policyAssignmentName')]",
      "apiVersion": "2019-09-01",
      "properties": {
        "scope": "[subscriptionResourceId('Microsoft.Resources/resourceGroups', resourceGroup().name)]",
        "policyDefinitionId": "[tenantResourceId('Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Další kroky

* Popis sekcí v šabloně Azure Resource Manager najdete v tématu [vytváření šablon Azure Resource Manager](template-syntax.md).
* Chcete-li sloučit více šablon, přečtěte si téma [použití propojených šablon s Azure Resource Manager](linked-templates.md).
* Informace o iteraci zadaného počtu výskytů při vytváření typu prostředku najdete v tématu [vytvoření více instancí prostředků v Azure Resource Manager](copy-resources.md).
* Pokud chcete zjistit, jak nasadit šablonu, kterou jste vytvořili, přečtěte si téma [nasazení aplikace pomocí šablony Azure Resource Manager](deploy-powershell.md).

