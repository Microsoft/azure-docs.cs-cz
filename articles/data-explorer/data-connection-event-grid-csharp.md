---
title: 'Vytvoření datového připojení Grid událostí pro Azure Data Explorer pomocí C #'
description: V tomto článku se dozvíte, jak vytvořit datové připojení Event Grid pro Azure Data Explorer pomocí C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 03963f60cc364dd36ad55c0a28e92e3b585bb38d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255078"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-c"></a>Vytvoření datového připojení Grid událostí pro Azure Data Explorer pomocí C #

> [!div class="op_single_selector"]
> * [Portál](ingest-data-event-grid.md)
> * [C #](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Šablona Azure Resource Manageru](data-connection-event-grid-resource-manager.md)


Průzkumník dat Azure je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. Azure Data Explorer nabízí ingestování (načítání dat) z centra událostí, ioT hubů a objektů BLOB zapsané do kontejnerů objektů blob. V tomto článku vytvoříte datové připojení Event Grid pro Azure Data Explorer pomocí C#.

## <a name="prerequisites"></a>Požadavky

* Pokud nemáte nainstalovaný Visual Studio 2019, můžete si stáhnout a použít **bezplatnou** [visual studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit možnost **Azure Development**.
* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* [Vytvoření clusteru a databáze](create-cluster-database-csharp.md)
* Vytvoření [mapování tabulek a sloupců](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)
* Nastavení [zásad databáze a tabulky](database-table-policies-csharp.md) (volitelné)
* Vytvořte [účet úložiště s předplatným Služby event grid](ingest-data-event-grid.md#create-an-event-grid-subscription-in-your-storage-account).

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-grid-data-connection"></a>Přidání datového připojení Mřížky událostí

Následující příklad ukazuje, jak přidat datové připojení Event Grid programově. V Průzkumníkovi dat Azure pro přidání datového připojení Event Grid pomocí portálu Azure najdete v tématu [vytvoření datového připojení Event Grid.](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer)

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
| eventHubResourceId | *ID prostředku* | ID prostředku centra událostí, kde je mřížka událostí nakonfigurována pro odesílání událostí. |
| storageAccountResourceId | *ID prostředku* | ID prostředku vašeho účtu úložiště, který uchovává data pro ingestování. |
| consumerGroup | *$Default* | Skupina spotřebitelů centra událostí.|
| location | *USA – střed* | Umístění prostředku datového připojení.|

## <a name="generate-sample-data"></a>Generování ukázkových dat

Teď, když jsou Azure Data Explorer a účet úložiště připojené, můžete vytvořit ukázková data a nahrát je do úložiště objektů blob.

Tento skript vytvoří nový kontejner ve vašem účtu úložiště, nahraje existující soubor (jako objekt blob) do tohoto kontejneru a pak zobrazí seznam objektů BLOB v kontejneru.

```csharp
var azureStorageAccountConnectionString=<storage_account_connection_string>;

var containerName=<container_name>;
var blobName=<blob_name>;
var localFileName=<file_to_upload>;

// Creating the container
var azureStorageAccount = CloudStorageAccount.Parse(azureStorageAccountConnectionString);
var blobClient = azureStorageAccount.CreateCloudBlobClient();
var container = blobClient.GetContainerReference(containerName);
container.CreateIfNotExists();

// Set metadata and upload file to blob
var blob = container.GetBlockBlobReference(blobName);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoIngestionMappingReference", "mapping_v2‬");
blob.UploadFromFile(localFileName);

// List blobs
var blobs = container.ListBlobs();
```

> [!NOTE]
> Azure Data Explorer neodstraní objekty BLOB po požití. Uchvátíte objekty BLOB po dobu tří až pěti dnů pomocí [životního cyklu úložiště objektů Blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) ke správě odstranění objektu blob.

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
