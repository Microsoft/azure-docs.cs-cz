---
title: Vytvoření datového připojení Event Grid pro Azure Průzkumník dat pomocíC#
description: V tomto článku se dozvíte, jak vytvořit datové připojení Event Grid pro Azure Průzkumník dat pomocí C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 0accf502df3616a686a34fc6c96cb2cfc47e6db1
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74667826"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-c"></a>Vytvoření datového připojení Event Grid pro Azure Průzkumník dat pomocíC#

> [!div class="op_single_selector"]
> * [Azure Portal](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Šablona Azure Resource Manageru](data-connection-event-grid-resource-manager.md)


Azure Data Explorer je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. Azure Průzkumník dat nabízí ingestování (načítání dat) z Event Hubs, rozbočovačů IoT a objektů BLOB zapsaných do kontejnerů objektů BLOB. V tomto článku vytvoříte datové připojení Event Grid pro Azure Průzkumník dat pomocí C#.

## <a name="prerequisites"></a>Předpoklady

* Pokud nemáte nainstalovanou aplikaci Visual Studio 2019, můžete si stáhnout a použít **bezplatnou** [edici Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit možnost **Azure Development**.
* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* Vytvoření [clusteru a databáze](create-cluster-database-csharp.md)
* Vytvořit [mapování tabulek a sloupců](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)
* Nastavení [zásad databáze a tabulek](database-table-policies-csharp.md) (volitelné)
* Vytvořte [účet úložiště s předplatným Event Grid](ingest-data-event-grid.md#create-an-event-grid-subscription-in-your-storage-account).

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-grid-data-connection"></a>Přidání datového připojení Event Grid

Následující příklad ukazuje, jak přidat Event Grid datové připojení prostřednictvím kódu programu. Další informace najdete [v tématu Vytvoření datového připojení Event Grid v Azure Průzkumník dat](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer) pro přidání Event Gridho datového připojení pomocí Azure Portal.

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
//The event hub and storage account that are created as part of the Prerequisites
var eventHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
var storageAccountResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Storage/storageAccounts/xxxxxx";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;

await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, dataConnectionName,
    new EventGridDataConnection(storageAccountResourceId, eventHubResourceId, consumerGroup, tableName: tableName, location: location, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
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
| eventHubResourceId | *ID prostředku* | ID prostředku centra událostí, kde je Event Grid nakonfigurované pro odesílání událostí. |
| storageAccountResourceId | *ID prostředku* | ID prostředku účtu úložiště, který obsahuje data pro ingestování. |
| Klientská organizace | *$Default* | Skupina uživatelů centra událostí.|
| location | *Střed USA* | Umístění prostředku datového připojení.|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
