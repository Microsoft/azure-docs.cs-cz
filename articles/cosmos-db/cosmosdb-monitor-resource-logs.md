---
title: Monitorování Azure Cosmos DB dat pomocí nastavení diagnostiky Azure
description: Naučte se používat nastavení diagnostiky Azure ke sledování výkonu a dostupnosti dat uložených v Azure Cosmos DB
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: sngun
ms.openlocfilehash: 184fc65dae57292243be9abdca71a129512b3d0b
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252057"
---
# <a name="monitor-azure-cosmos-db-data-by-using-diagnostic-settings-in-azure"></a>Monitorování Azure Cosmos DB dat pomocí nastavení diagnostiky v Azure

Nastavení diagnostiky v Azure slouží ke shromažďování protokolů prostředků. Protokoly prostředků Azure se generují prostředkem a poskytují bohatou a častou data o provozu daného prostředku. Tyto protokoly jsou zachyceny na žádost a jsou také označovány jako "protokoly roviny dat". Mezi Příklady operací s rovinou dat patří odstranění, vložení a readFeed. Obsah tyto protokoly se liší podle typu prostředku.

Metriky platforem a protokoly aktivit jsou shromažďovány automaticky, zatímco je nutné vytvořit nastavení diagnostiky pro shromáždění protokolů prostředků nebo jejich přeposílání mimo Azure Monitor. Nastavení diagnostiky pro účty Azure Cosmos můžete zapnout pomocí následujících kroků:

1. Přihlaste se k [Azure Portal](https://portal.azure.com).

1. Přejděte k účtu Azure Cosmos. Otevřete podokno **nastavení diagnostiky** a pak vyberte **Přidat možnost nastavení diagnostiky** .

1. V podokně **nastavení diagnostiky** vyplňte formulář následujícími podrobnostmi: 

    * **Název**: zadejte název protokolů, které chcete vytvořit.

    * Protokoly můžete ukládat k **archivaci do účtu úložiště**, **streamovat do centra událostí** nebo je **Odeslat do Log Analytics**

1. Při vytváření nastavení diagnostiky určíte, kterou kategorii protokolů se mají shromažďovat. Kategorie protokolů, které podporuje Azure Cosmos DB, jsou uvedené dál spolu s ukázkovým protokolem, který jsou shromážděny:

 * **DataPlaneRequests**: tuto možnost vyberte, pokud chcete protokolovat požadavky back-endu do všech rozhraní API, mezi které patří účty SQL, Graph, MongoDB, Cassandra a rozhraní API pro tabulky v Azure Cosmos DB. Všimněte si klíčových vlastností: `Requestcharge`, `statusCode`, `clientIPaddress`a `partitionID`.

    ```
    { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
    ```

* **MongoRequests**: tuto možnost vyberte, pokud chcete protokolovat požadavky iniciované uživateli z front-endu, aby sloužily požadavky na rozhraní api služby Azure Cosmos DB pro MongoDB. Tento typ protokolu není k dispozici pro jiné účty rozhraní API. Žádosti MongoDB se zobrazí v MongoRequests a také v DataPlaneRequests. Všimněte si klíčových vlastností: `Requestcharge`, `opCode`.

    ```
    { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
    ```

* **QueryRuntimeStatistics**: tuto možnost vyberte, pokud chcete protokolovat text dotazu, který se spustil. Tento typ protokolu je k dispozici pouze pro účty rozhraní SQL API.

    ```
    { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
    ```

* **PartitionKeyStatistics**: tuto možnost vyberte, pokud chcete protokolovat statistiky klíčů oddílů. V současné době se jedná o velikost úložiště klíčů oddílů (KB). V části [potíže s řešením potíží pomocí diagnostických dotazů Azure](#diagnostic-queries) v tomto článku. Například dotazy, které používají "PartitionKeyStatistics". Protokol je vygenerován proti prvním třem klíčům oddílu, které zabírají většinu úložiště dat. Tento protokol obsahuje data, jako je ID předplatného, název oblasti, název databáze, název kolekce, klíč oddílu a velikost úložiště v KB.

    ```
    { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
    ```

* **PartitionKeyRUConsumption**: Tento protokol oznamuje agregovanou spotřebu klíčů oddílů za sekundu ru/s. V současné době Azure Cosmos DB sestavovat klíče oddílů pouze pro účty rozhraní SQL API a operace čtení, zápisu a uložených procedur v bodu. jiné typy rozhraní API a operací se nepodporují. V případě jiných rozhraní API bude sloupec klíče oddílu v tabulce diagnostického protokolu prázdný. Tento protokol obsahuje data, jako je ID předplatného, název oblasti, název databáze, název kolekce, klíč oddílu, typ operace a poplatek za požadavek. V části [potíže s řešením potíží pomocí diagnostických dotazů Azure](#diagnostic-queries) v tomto článku. Například dotazy, které používají "PartitionKeyRUConsumption". 

* **ControlPlaneRequests**: Tento protokol obsahuje podrobnosti o operacích roviny ovládacího prvku, jako je vytvoření účtu, přidání nebo odebrání oblasti, aktualizace nastavení replikace účtů atd. Tento typ protokolu je k dispozici pro všechny typy rozhraní API, které obsahují SQL (Core), MongoDB, Gremlin, Cassandra, rozhraní API pro tabulky.

* **Požadavky**: tuto možnost vyberte, pokud chcete shromažďovat data metrik z Azure Cosmos DB do cílových umístění v nastavení diagnostiky. Jedná se o stejná data shromažďovaná automaticky v metrikách Azure. Shromažďovat data metriky pomocí protokolů zdrojů k analýze obou druhů dat a k odesílání dat metriky mimo Azure Monitor.

Podrobné informace o tom, jak vytvořit nastavení diagnostiky pomocí Azure Portal, CLI nebo PowerShellu, najdete v tématu [Vytvoření nastavení diagnostiky pro shromáždění protokolů platforem a metrik v článku Azure](../azure-monitor/platform/diagnostic-settings.md) .


## <a id="diagnostic-queries"></a>Řešení potíží s diagnostickými dotazy

1. Jak získat poplatky za žádosti pro nákladné dotazy?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 10.0
   | project activityId_g, requestCharge_s
   | join kind= inner (
   AzureDiagnostics
   | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
   | project activityId_g, querytext_s
   ) on $left.activityId_g == $right.activityId_g
   | order by requestCharge_s desc
   | limit 100
   ```

1. Jak zjistit, které operace přebírají maximum RU/s?

    ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(responseLength_s), max(requestLength_s), max(requestCharge_s), count = count() by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```
1. Jak získat distribuci pro různé operace?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize count = count()  by OperationName, requestResourceType_s, bin(TimeGenerated, 1h) 
   ```

1. Jaká je maximální propustnost, kterou oddíl spotřeboval?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(requestCharge_s) by bin(TimeGenerated, 1h), partitionId_g
   ```

1. Jak získat informace o spotřebě klíčů oddílů RU/s za sekundu?

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s, TimeGenerated 
   | order by TimeGenerated asc 
   ```

1. Jak získat poplatek za požadavek na konkrétní klíč oddílu

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where parse_json(partitionKey_s)[0] == "2" 
   ```

1. Jak získat nejdůležitější klíče oddílů s největším RU/s spotřebovaným v určitém období? 

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where TimeGenerated >= datetime("11/26/2019, 11:20:00.000 PM") and TimeGenerated <= datetime("11/26/2019, 11:30:00.000 PM") 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s 
   | order by total desc
    ```

1. Jak získat protokoly pro klíče oddílů, jejichž velikost úložiště je větší než 8 GB?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics"
   | where todouble(sizeKb_d) > 800000
   ```

1. Jak získat statistiku klíče oddílu k vyhodnocení zkosení v rámci tří hlavních oddílů pro databázový účet?

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    ```

## <a name="next-steps"></a>Další kroky

* [Azure Monitor pro Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json)
* [Monitorování a ladění pomocí metrik v Azure Cosmos DB](use-metrics.md)
