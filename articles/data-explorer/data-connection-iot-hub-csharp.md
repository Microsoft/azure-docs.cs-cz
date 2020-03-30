---
title: 'Vytvoření datového připojení služby IoT Hub pro Azure Data Explorer pomocí C #'
description: V tomto článku se dozvíte, jak vytvořit datové připojení služby IoT Hub pro Azure Data Explorer pomocí C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 0cac03e50bf46910f8430b745803107b60905769
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74667389"
---
# <a name="create-an-iot-hub-data-connection-for-azure-data-explorer-by-using-c-preview"></a>Vytvoření datového připojení služby IoT Hub pro Azure Data Explorer pomocí C# (Preview)

> [!div class="op_single_selector"]
> * [Portál](ingest-data-iot-hub.md)
> * [C #](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Šablona Azure Resource Manageru](data-connection-iot-hub-resource-manager.md)

Průzkumník dat Azure je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. Azure Data Explorer nabízí ingestování (načítání dat) z centra událostí, ioT hubů a objektů BLOB zapsané do kontejnerů objektů blob. V tomto článku vytvoříte datové připojení služby IoT Hub pro Průzkumníka dat Azure pomocí jazyka C#.

## <a name="prerequisites"></a>Požadavky

* Pokud nemáte nainstalovaný Visual Studio 2019, můžete si stáhnout a použít **bezplatnou** [visual studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit možnost **Azure Development**.
* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* [Vytvoření clusteru a databáze](create-cluster-database-csharp.md)
* Vytvoření [mapování tabulek a sloupců](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)
* Nastavení [zásad databáze a tabulky](database-table-policies-csharp.md) (volitelné)
* Vytvořte [centrum IoT Hub s nakonfigurovanou zásadou sdíleného přístupu](ingest-data-iot-hub.md#create-an-iot-hub).

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-iot-hub-data-connection"></a>Přidání datového připojení centra IoT Hub 

Následující příklad ukazuje, jak přidat datové připojení centra IoT programově. Na [jdete připojit tabulku Průzkumníka dat Azure do Služby IoT Hub,](ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub) kde najdete přidání datového připojení služby Iot Hub pomocí portálu Azure.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
var dataConnectionName = "myeventhubconnect";
//The IoT hub that is created as part of the Prerequisites
var iotHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Devices/IotHubs/xxxxxx";
var sharedAccessPolicyName = "iothubforread";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;

await kustoManagementClient.DataConnections.CreateOrUpdate(resourceGroupName, clusterName, databaseName, dataConnectionName,
            new IotHubDataConnection(iotHubResourceId, consumerGroup, sharedAccessPolicyName, tableName: tableName, location: location, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
```

|**Nastavení** | **Navrhovaná hodnota** | **Popis pole**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Vaše tenantské ID. Označuje se také jako ID adresáře.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID předplatného, které používáte pro vytvoření prostředků.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID klienta aplikace, která má přístup k prostředkům ve vašem tenantovi.|
| clientSecret | *xxxxxxxxxxxxxx* | Tajný klíč klienta aplikace, která může přistupovat k prostředkům ve vašem tenantovi. |
| resourceGroupName | *testrg* | Název skupiny prostředků obsahující váš cluster.|
| clusterName | *mykustocluster* | Název clusteru.|
| Databasename | *mykustodatabáze* | Název cílové databáze v clusteru.|
| dataConnectionName | *myeventhubconnect* | Požadovaný název datového připojení.|
| tableName | *Bouřkové akce* | Název cílové tabulky v cílové databázi.|
| název_pravidla mapování | *StormEvents_CSV_Mapping* | Název mapování sloupců související s cílovou tabulkou.|
| Dataformat | *Csv* | Formát dat zprávy.|
| iotHubResourceId | *ID prostředku* | ID prostředků vašeho centra IoT hub, který obsahuje data pro ingestování. |
| sharedAccessPolicyName | *iothubforread* | Název zásady sdíleného přístupu, který definuje oprávnění pro zařízení a služby pro připojení k služby IoT Hub. |
| consumerGroup | *$Default* | Skupina spotřebitelů centra událostí.|
| location | *USA – střed* | Umístění prostředku datového připojení.|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
