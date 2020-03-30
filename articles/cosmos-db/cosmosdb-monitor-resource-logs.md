---
title: Monitorování dat Azure Cosmos DB pomocí nastavení Diagnostika Azure
description: Zjistěte, jak pomocí nastavení Diagnostika Azure sledovat výkon a dostupnost dat uložených v Azure Cosmos DB
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: sngun
ms.openlocfilehash: 184fc65dae57292243be9abdca71a129512b3d0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252057"
---
# <a name="monitor-azure-cosmos-db-data-by-using-diagnostic-settings-in-azure"></a>Monitorování dat Azure Cosmos DB pomocí diagnostických nastavení v Azure

Diagnostická nastavení v Azure se používají ke shromažďování protokolů prostředků. Protokoly prostředků Azure jsou emitovány prostředek a poskytují bohaté, časté data o provozu tohoto prostředku. Tyto protokoly jsou zachyceny na požadavek a jsou také označovány jako "protokoly roviny dat". Mezi příklady operací s rovinou dat patří odstranění, vložení a čtení kanálu ReadFeed. Obsah těchto protokolů se liší podle typu prostředku.

Metriky platformy a protokoly aktivit jsou shromažďovány automaticky, zatímco je nutné vytvořit diagnostické nastavení pro shromažďování protokolů prostředků nebo jejich předávání mimo Azure Monitor. Diagnostické nastavení pro účty Azure Cosmos můžete zapnout pomocí následujících kroků:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. Přejděte na svůj účet Azure Cosmos. Otevřete podokno **Nastavení diagnostiky** a pak vyberte Přidat možnost **nastavení diagnostiky.**

1. V podokně **Nastavení diagnostiky** vyplňte formulář následujícími podrobnostmi: 

    * **Název**: Zadejte název pro protokoly, které chcete vytvořit.

    * Protokoly můžete uložit do **archivu do účtu úložiště**, **Streamovat do centra událostí** nebo **odeslat do analýzy protokolů.**

1. Při vytváření diagnostickénastavení určíte, které kategorie protokolů shromažďovat. Kategorie protokolů podporovaných Službou Azure Cosmos DB jsou uvedeny níže spolu s ukázkovým protokolem, který shromažďují:

 * **DataPlaneRequests**: Tuto možnost vyberte, chcete-li protokolovat back-endové požadavky ke všem rozhraním API, která zahrnují účty SQL, Graph, MongoDB, Cassandra a Table API v Azure Cosmos DB. Klíčové vlastnosti, `Requestcharge`které `statusCode` `clientIPaddress`je `partitionID`třeba poznamenat, jsou: , , a .

    ```
    { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
    ```

* **MongoRequests**: Tuto možnost vyberte, chcete-li protokolovat požadavky iniciované uživatelem z front-endu, aby se zolíky zobrazoval požadavky na rozhraní API služby Azure Cosmos DB pro MongoDB, tento typ protokolu není k dispozici pro jiné účty rozhraní API. Požadavky MongoDB se zobrazí v MongoRequests, stejně jako DataPlaneRequests. Klíčové vlastnosti, `Requestcharge`které `opCode`je třeba poznamenat, jsou: , .

    ```
    { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
    ```

* **QueryRuntimeStatistics**: Tuto možnost vyberte, chcete-li protokolovat text dotazu, který byl spuštěn. Tento typ protokolu je k dispozici pouze pro účty rozhraní SQL API.

    ```
    { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
    ```

* **PartitionKeyStatistics**: Tuto možnost vyberte, chcete-li protokolovat statistiky klíčů oddílů. To je aktuálně reprezentován o velikosti úložiště (KB) klíče oddílu. Podívejte se na [řešení problémů pomocí](#diagnostic-queries) diagnostické dotazy Azure části tohoto článku. Například dotazy, které používají "PartitionKeyStatistics". Protokol je vydáván proti první tři klíče oddílu, které zabírají většinu úložiště dat. Tento protokol obsahuje data, jako je například ID předplatného, název oblasti, název databáze, název kolekce, klíč oddílu a velikost úložiště v KB.

    ```
    { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
    ```

* **PartitionKeyRUConsumption**: Tento protokol hlásí agregovně za sekundu RU/s spotřeby klíče oddílů. V současné době Azure Cosmos DB hlásí klíče oddílů pouze pro účty rozhraní SQL API a pro operace čtení a zápisu bodu a uložené procedury. jiná řešení API a typy operací nejsou podporovány. U ostatních api bude sloupec klíče oddílu v tabulce diagnostického protokolu prázdný. Tento protokol obsahuje data, jako je například ID předplatného, název oblasti, název databáze, název kolekce, klíč oddílu, typ operace a poplatek požadavku požadavku. Podívejte se na [řešení problémů pomocí](#diagnostic-queries) diagnostické dotazy Azure části tohoto článku. Například dotazy, které používají "PartitionKeyRUConsumption". 

* **ControlPlaneRequests**: Tento protokol obsahuje podrobnosti o operacích roviny ovládacího prvku, jako je vytvoření účtu, přidání nebo odebrání oblasti, aktualizace nastavení replikace účtu atd. Tento typ protokolu je k dispozici pro všechny typy rozhraní API, které zahrnují SQL (Core), MongoDB, Gremlin, Cassandra, Table API.

* **Požadavky**: Tuto možnost vyberte, chcete-li shromažďovat metrická data z Azure Cosmos DB do cílů v diagnostickém nastavení. Jedná se o stejná data shromážděná automaticky v Azure Metrics. Shromažďujte metrická data pomocí protokolů prostředků, abyste společně analyzovali oba druhy dat a odesílli metrická data mimo Azure Monitor.

Podrobné informace o tom, jak vytvořit diagnostické nastavení pomocí portálu Azure, CLI nebo PowerShellu, najdete v [tématu Vytvoření diagnostického nastavení pro shromažďování protokolů platformy a metrik v](../azure-monitor/platform/diagnostic-settings.md) článku Azure.


## <a name="troubleshoot-issues-with-diagnostics-queries"></a><a id="diagnostic-queries"></a>Poradce při potížích s diagnostickými dotazy

1. Jak získat poplatky za požadavky na nákladné dotazy?

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

1. Jak zjistit, které operace využívají většinu Ru/s?

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

1. Jaká je maximální propustnost, kterou oddíl spotřebovává?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(requestCharge_s) by bin(TimeGenerated, 1h), partitionId_g
   ```

1. Jak získat informace o spotřebě klíče ru/s oddílů za sekundu?

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s, TimeGenerated 
   | order by TimeGenerated asc 
   ```

1. Jak získat poplatek za požadavek pro konkrétní klíč oddílu

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where parse_json(partitionKey_s)[0] == "2" 
   ```

1. Jak získat horní klíče oddílu s většinou RU / s spotřebované v určitém období? 

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

1. Jak získat oddíl Key statistiky vyhodnotit zkosení přes horní tři oddíly pro databázový účet?

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    ```

## <a name="next-steps"></a>Další kroky

* [Azure Monitor pro Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json)
* [Monitorování a ladění metrik v Azure Cosmos DB](use-metrics.md)
