---
title: Vytvoření datového připojení IoT Hub pro Azure Průzkumník dat pomocíC#
description: V tomto článku se dozvíte, jak vytvořit datové připojení IoT Hub pro Azure Průzkumník dat pomocí C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: bcc80000be5e061a37601f05a2a245aac031fc15
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72031653"
---
# <a name="create-an-iot-hub-data-connection-for-azure-data-explorer-by-using-c-preview"></a>Vytvoření datového připojení IoT Hub pro Azure Průzkumník dat pomocí C# (Preview)

> [!div class="op_single_selector"]
> * [Azure Portal](ingest-data-iot-hub.md)
> * [C#](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)

Azure Data Explorer je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. Azure Průzkumník dat nabízí ingestování (načítání dat) z Event Hubs, rozbočovačů IoT a objektů BLOB zapsaných do kontejnerů objektů BLOB. V tomto článku vytvoříte datové připojení IoT Hub pro Azure Průzkumník dat pomocí C#.

## <a name="prerequisites"></a>Předpoklady

* Pokud nemáte nainstalovanou aplikaci Visual Studio 2019, můžete si stáhnout a použít **bezplatnou** [edici Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit možnost **Azure Development**.

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

* Vytvoření [clusteru a databáze](create-cluster-database-csharp.md)

* Vytvořit [mapování tabulek a sloupců](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)

* Nastavení [zásad databáze a tabulek](database-table-policies-csharp.md) (volitelné)

* Vytvoření služby [IoT Hub se nakonfigurovanými zásadami sdíleného přístupu](ingest-data-iot-hub.md#create-an-iot-hub)

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-iot-hub-data-connection"></a>Přidání datového připojení IoT Hub 

Následující příklad ukazuje, jak přidat IoT Hub datové připojení prostřednictvím kódu programu. Pokud chcete přidat datové připojení ke službě IoT Hub pomocí Azure Portal, přečtěte si téma [připojení tabulky Azure Průzkumník dat k IoT Hub](ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub) .

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
| iotHubResourceId | *ID prostředku* | ID prostředku vašeho centra IoT, které obsahuje data pro ingestování. |
| sharedAccessPolicyName | *iothubforread* | Název zásad sdíleného přístupu, který definuje oprávnění pro zařízení a služby pro připojení k IoT Hub. |
| Klientská organizace | *$Default* | Skupina uživatelů centra událostí.|
| location | *Střed USA* | Umístění prostředku datového připojení.|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
