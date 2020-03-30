---
title: 'Vytvoření datového připojení Centra událostí pro Azure Data Explorer pomocí C #'
description: V tomto článku se dozvíte, jak vytvořit datové připojení Centra událostí pro Azure Data Explorer pomocí C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: cf2a274b4f48b31739d6abba5cf87fa2a10d4ca1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74667682"
---
# <a name="create-an-event-hub-data-connection-for-azure-data-explorer-by-using-c"></a>Vytvoření datového připojení Centra událostí pro Azure Data Explorer pomocí C #

> [!div class="op_single_selector"]
> * [Portál](ingest-data-event-hub.md)
> * [C #](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)
> * [Šablona Azure Resource Manageru](data-connection-event-hub-resource-manager.md)

Průzkumník dat Azure je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. Azure Data Explorer nabízí ingestování (načítání dat) z centra událostí, ioT hubů a objektů BLOB zapsané do kontejnerů objektů blob. V tomto článku vytvoříte datové připojení Centra událostí pro Azure Data Explorer pomocí C#.

## <a name="prerequisites"></a>Požadavky

* Pokud nemáte nainstalovaný Visual Studio 2019, můžete si stáhnout a použít **bezplatnou** [visual studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit možnost **Azure Development**.
* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* [Vytvoření clusteru a databáze](create-cluster-database-csharp.md)
* Vytvoření [mapování tabulek a sloupců](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)
* Nastavení [zásad databáze a tabulky](database-table-policies-csharp.md) (volitelné)
* Vytvořte [Centrum událostí s daty pro ingestování](ingest-data-event-hub.md#create-an-event-hub). 

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>Přidání datového připojení Centra událostí

Následující příklad ukazuje, jak programově přidat datové připojení Centra událostí. Podívejte se [na připojení k centru událostí,](ingest-data-event-hub.md#connect-to-the-event-hub) kde najdete přidání datového připojení Centra událostí pomocí portálu Azure.

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
//The event hub that is created as part of the Prerequisites
var eventHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;
await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, dataConnectionName, 
    new EventHubDataConnection(eventHubResourceId, consumerGroup, location: location, tableName: tableName, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
```

|**Nastavení** | **Navrhovaná hodnota** | **Popis pole**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Vaše tenantské ID. Označuje se také jako ID adresáře.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID předplatného, které používáte pro vytvoření prostředků.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID klienta aplikace, která má přístup k prostředkům ve vašem tenantovi.|
| clientSecret | *xxxxxxxxxxxxxx* | Tajný klíč klienta aplikace, která může přistupovat k prostředkům ve vašem tenantovi.|
| resourceGroupName | *testrg* | Název skupiny prostředků obsahující váš cluster.|
| clusterName | *mykustocluster* | Název clusteru.|
| Databasename | *mykustodatabáze* | Název cílové databáze v clusteru.|
| dataConnectionName | *myeventhubconnect* | Požadovaný název datového připojení.|
| tableName | *Bouřkové akce* | Název cílové tabulky v cílové databázi.|
| název_pravidla mapování | *StormEvents_CSV_Mapping* | Název mapování sloupců související s cílovou tabulkou.|
| Dataformat | *Csv* | Formát dat zprávy.|
| eventHubResourceId | *ID prostředku* | ID prostředku centra událostí, které obsahuje data pro ingestování. |
| consumerGroup | *$Default* | Skupina spotřebitelů centra událostí.|
| location | *USA – střed* | Umístění prostředku datového připojení.|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
