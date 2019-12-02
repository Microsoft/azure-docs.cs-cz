---
title: Vytvoření datového připojení IoT Hub pro Azure Průzkumník dat pomocíC#
description: V tomto článku se dozvíte, jak vytvořit datové připojení IoT Hub pro Azure Průzkumník dat pomocí C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 0cac03e50bf46910f8430b745803107b60905769
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74667389"
---
# <a name="create-an-iot-hub-data-connection-for-azure-data-explorer-by-using-c-preview"></a>Vytvoření datového připojení IoT Hub pro Azure Průzkumník dat pomocí C# (Preview)

> [!div class="op_single_selector"]
> * [Azure Portal](ingest-data-iot-hub.md)
> * [C#](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Šablona Azure Resource Manageru](data-connection-iot-hub-resource-manager.md)

Azure Data Explorer je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. Azure Průzkumník dat nabízí ingestování (načítání dat) z Event Hubs, rozbočovačů IoT a objektů BLOB zapsaných do kontejnerů objektů BLOB. V tomto článku vytvoříte datové připojení IoT Hub pro Azure Průzkumník dat pomocí C#.

## <a name="prerequisites"></a>Předpoklady

* Pokud nemáte nainstalovanou aplikaci Visual Studio 2019, můžete si stáhnout a použít **bezplatnou** [edici Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit možnost **Azure Development**.
* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* Vytvoření [clusteru a databáze](create-cluster-database-csharp.md)
* Vytvořit [mapování tabulek a sloupců](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)
* Nastavení [zásad databáze a tabulek](database-table-policies-csharp.md) (volitelné)
* Create an [IoT Hub with a shared access policy configured](ingest-data-iot-hub.md#create-an-iot-hub).

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-iot-hub-data-connection"></a>Add an IoT Hub data connection 

The following example shows you how to add an IoT Hub data connection programmatically. See [connect Azure Data Explorer table to IoT Hub](ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub) for adding an Iot Hub data connection using the Azure portal.

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
| TenantId | *XXXXXXXX-xxxxx-xxxx-xxxx-XXXXXXXXX* | Vaše ID tenanta. Označuje se také jako ID adresáře.|
| subscriptionId | *XXXXXXXX-xxxxx-xxxx-xxxx-XXXXXXXXX* | ID předplatného, které používáte pro vytváření prostředků.|
| clientId | *XXXXXXXX-xxxxx-xxxx-xxxx-XXXXXXXXX* | ID klienta aplikace, která má přístup k prostředkům ve vašem tenantovi.|
| clientSecret | *xxxxxxxxxxxxxx* | Tajný klíč klienta aplikace, který má přístup k prostředkům ve vašem tenantovi. |
| resourceGroupName | *testrg* | Název skupiny prostředků, která obsahuje váš cluster.|
| clusterName | *mykustocluster* | Název vašeho clusteru.|
| Databáze | *mykustodatabase* | Název cílové databáze v clusteru.|
| Připojení k dataconnectionname | *myeventhubconnect* | Požadovaný název datového připojení.|
| tableName | *StormEvents* | Název cílové tabulky v cílové databázi.|
| mappingRuleName | *StormEvents_CSV_Mapping* | Název mapování sloupce souvisejícího s cílovou tabulkou.|
| Formát DataFormat | *Formát* | Formát dat zprávy|
| iotHubResourceId | *ID prostředku* | The resource ID of your IoT hub that holds the data for ingestion. |
| sharedAccessPolicyName | *iothubforread* | The name of the shared access policy that defines the permissions for devices and services to connect to IoT Hub. |
| Klientská organizace | *$Default* | The consumer group of your event hub.|
| location | *Střed USA* | Umístění prostředku datového připojení.|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
