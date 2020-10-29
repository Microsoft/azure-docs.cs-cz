---
title: Monitorování Azure Cosmos DB dat pomocí nastavení diagnostiky Azure
description: Naučte se používat nastavení diagnostiky Azure ke sledování výkonu a dostupnosti dat uložených v Azure Cosmos DB
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/28/2020
ms.author: sngun
ms.openlocfilehash: 319713cd631b87d9f97af0db3d4a7b3af1c580ec
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "92926117"
---
# <a name="monitor-azure-cosmos-db-data-by-using-diagnostic-settings-in-azure"></a>Monitorování Azure Cosmos DB dat pomocí nastavení diagnostiky v Azure

Nastavení diagnostiky v Azure slouží ke shromažďování protokolů prostředků. Protokoly prostředků Azure se generují prostředkem a poskytují bohatou a častou data o provozu daného prostředku. Tyto protokoly jsou zachyceny na žádost a jsou také označovány jako "protokoly roviny dat". Mezi Příklady operací s rovinou dat patří odstranění, vložení a readFeed. Obsah těchto protokolů se liší podle typu prostředku.

Metriky platforem a protokoly aktivit jsou shromažďovány automaticky, zatímco je nutné vytvořit nastavení diagnostiky pro shromáždění protokolů prostředků nebo jejich přeposílání mimo Azure Monitor. Nastavení diagnostiky pro účty Azure Cosmos můžete zapnout pomocí následujících kroků:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

1. Přejděte k účtu Azure Cosmos. Otevřete podokno **nastavení diagnostiky** a pak vyberte **Přidat možnost nastavení diagnostiky** .

1. V podokně **nastavení diagnostiky** vyplňte formulář následujícími podrobnostmi: 

    * **Název** : zadejte název protokolů, které chcete vytvořit.

    * Protokoly můžete ukládat k **archivaci do účtu úložiště** , **streamovat do centra událostí** nebo je **Odeslat do Log Analytics**

1. Při vytváření nastavení diagnostiky určíte, kterou kategorii protokolů se mají shromažďovat. Kategorie protokolů, které podporuje Azure Cosmos DB, jsou uvedené dál spolu s ukázkovým protokolem, který jsou shromážděny:

 * **DataPlaneRequests** : tuto možnost vyberte, pokud chcete protokolovat požadavky back-endu do všech rozhraní API, mezi které patří účty SQL, Graph, MongoDB, Cassandra a rozhraní API pro tabulky v Azure Cosmos DB. Všimněte si klíčových vlastností, které jsou: `Requestcharge` , `statusCode` , `clientIPaddress` , `partitionID` , a `resourceTokenPermissionId` `resourceTokenPermissionMode` .

    ```json
    { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372", "resourceTokenPermissionId": "perm-prescriber-app","resourceTokenPermissionMode": "all", "resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
    ```

* **MongoRequests** : tuto možnost vyberte, pokud chcete protokolovat požadavky iniciované uživateli z front-endu, aby sloužily požadavky na rozhraní API služby Azure Cosmos DB pro MongoDB. Tento typ protokolu není k dispozici pro jiné účty rozhraní API. Klíčové vlastnosti, které se mají poznamenat: `Requestcharge` , `opCode` . Pokud povolíte MongoRequests v diagnostických protokolech, nezapomeňte vypnout DataPlaneRequests. V rozhraní API se zobrazí jeden protokol pro každý požadavek.

    ```json
    { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
    ```

* **CassandraRequests** : tuto možnost vyberte, pokud chcete protokolovat požadavky iniciované uživateli z front-endu, aby sloužily požadavky na rozhraní API služby Azure Cosmos DB pro Cassandra. Tento typ protokolu není k dispozici pro jiné účty rozhraní API. Klíčové vlastnosti, které se mají poznamenat, jsou, `operationName` `requestCharge` , `piiCommandText` . Pokud povolíte CassandraRequests v diagnostických protokolech, nezapomeňte vypnout DataPlaneRequests. V rozhraní API se zobrazí jeden protokol pro každý požadavek.

   ```json
   { "time": "2020-03-30T23:55:10.9579593Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "CassandraRequests", "operationName": "QuerySelect", "properties": {"activityId": "6b33771c-baec-408a-b305-3127c17465b6","opCode": "<empty>","errorCode": "-1","duration": "0.311900","requestCharge": "1.589237","databaseName": "system","collectionName": "local","retryCount": "<empty>","authorizationTokenType": "PrimaryMasterKey","address": "104.42.195.92","piiCommandText": "{"request":"SELECT key from system.local"}","userAgent": """"}}
   ```

* **QueryRuntimeStatistics** : tuto možnost vyberte, pokud chcete protokolovat text dotazu, který se spustil. Tento typ protokolu je k dispozici pouze pro účty rozhraní SQL API.

    ```json
    { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
    ```

* **PartitionKeyStatistics** : tuto možnost vyberte, pokud chcete protokolovat statistiky klíčů oddílů. V současné době se jedná o velikost úložiště klíčů oddílů (KB). V části [potíže s řešením potíží pomocí diagnostických dotazů Azure](#diagnostic-queries) v tomto článku. Například dotazy, které používají "PartitionKeyStatistics". Protokol je vygenerován proti prvním třem klíčům oddílu, které zabírají většinu úložiště dat. Tento protokol obsahuje data, jako je ID předplatného, název oblasti, název databáze, název kolekce, klíč oddílu a velikost úložiště v KB.

    ```json
    { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
    ```

* **PartitionKeyRUConsumption** : Tento protokol oznamuje agregovanou spotřebu klíčů oddílů za sekundu ru/s. V současné době Azure Cosmos DB sestavovat klíče oddílů pouze pro účty rozhraní SQL API a operace čtení, zápisu a uložených procedur v bodu. jiné typy rozhraní API a operací se nepodporují. V případě jiných rozhraní API bude sloupec klíče oddílu v tabulce diagnostického protokolu prázdný. Tento protokol obsahuje data, jako je ID předplatného, název oblasti, název databáze, název kolekce, klíč oddílu, typ operace a poplatek za požadavek. V části [potíže s řešením potíží pomocí diagnostických dotazů Azure](#diagnostic-queries) v tomto článku. Například dotazy, které používají "PartitionKeyRUConsumption". 

* **ControlPlaneRequests** : Tento protokol obsahuje podrobnosti o operacích roviny ovládacího prvku, jako je vytvoření účtu, přidání nebo odebrání oblasti, aktualizace nastavení replikace účtů atd. Tento typ protokolu je k dispozici pro všechny typy rozhraní API, které obsahují SQL (Core), MongoDB, Gremlin, Cassandra, rozhraní API pro tabulky.

* **Požadavky** : tuto možnost vyberte, pokud chcete shromažďovat data metrik z Azure Cosmos DB do cílových umístění v nastavení diagnostiky. Jedná se o stejná data shromažďovaná automaticky v metrikách Azure. Shromažďovat data metriky pomocí protokolů zdrojů k analýze obou druhů dat a k odesílání dat metriky mimo Azure Monitor.

Podrobné informace o tom, jak vytvořit nastavení diagnostiky pomocí Azure Portal, CLI nebo PowerShellu, najdete v tématu [Vytvoření nastavení diagnostiky pro shromáždění protokolů platforem a metrik v článku Azure](../azure-monitor/platform/diagnostic-settings.md) .


## <a name="troubleshoot-issues-with-diagnostics-queries"></a><a id="diagnostic-queries"></a> Řešení potíží s diagnostickými dotazy

1. Postup dotazování na operace, které trvá déle než 3 MS pro spuštění:

   ```Kusto
   AzureDiagnostics 
   | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | summarize count() by clientIpAddress_s, TimeGenerated
   ```

1. Postup dotazování na uživatelského agenta, na kterém běží operace:

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | summarize count() by OperationName, userAgent_s
   ```

1. Dotaz na dlouhodobě běžící operace:

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | project TimeGenerated , duration_s 
   | summarize count() by bin(TimeGenerated, 5s)
   | render timechart
   ```
    
1. Jak získat statistiku klíče oddílu pro vyhodnocení zešikmení v horních 3 oddílech pro databázový účet:

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
   | project SubscriptionId, regionName_s, databaseName_s, collectionName_s, partitionKey_s, sizeKb_d, ResourceId 
   ```

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

1. Jak získat všechny dotazy, které spotřebovávají více než 100 RU/s s daty z **DataPlaneRequests** a **QueryRunTimeStatistics** .

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 100.0
   | project activityId_g, requestCharge_s
   | join kind= inner (
           AzureDiagnostics
           | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
           | project activityId_g, querytext_s
   ) on $left.activityId_g == $right.activityId_g
   | order by requestCharge_s desc
   | limit 100
   ```

1. Jak získat poplatky za požadavek a dobu provádění dotazu?

   ```kusto
   AzureDiagnostics
   | where TimeGenerated >= ago(24hr)
   | where Category == "QueryRuntimeStatistics"
   | join (
   AzureDiagnostics
   | where TimeGenerated >= ago(24hr)
   | where Category == "DataPlaneRequests"
   ) on $left.activityId_g == $right.activityId_g
   | project databasename_s, collectionname_s, OperationName1 , querytext_s,requestCharge_s1, duration_s1, bin(TimeGenerated, 1min)
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

1. Jak získat latenci při replikaci p99 nebo P50 pro operace, poplatek za požadavek nebo délku odpovědi?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2d)
   | summarize
   percentile(todouble(responseLength_s), 50), percentile(todouble(responseLength_s), 99), max(responseLength_s),
   percentile(todouble(requestCharge_s), 50), percentile(todouble(requestCharge_s), 99), max(requestCharge_s),
   percentile(todouble(duration_s), 50), percentile(todouble(duration_s), 99), max(duration_s),
   count()
   by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```
 
1. Jak získat protokoly ControlPlane?
 
   Nezapomeňte zapnout příznak, jak je popsáno v článku [Zakázání přístupu k zápisu metadat na základě klíčů](audit-control-plane-logs.md#disable-key-based-metadata-write-access) , a provedení operací pomocí Azure PowerShell, rozhraní příkazového řádku Azure nebo Azure Resource Manager.
 
   ```Kusto  
   AzureDiagnostics 
   | where Category =="ControlPlaneRequests"
   | summarize by OperationName 
   ```

## <a name="next-steps"></a>Další kroky

* [Azure Monitor pro Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json)
* [Monitorování a ladění pomocí metrik v Azure Cosmos DB](use-metrics.md)
