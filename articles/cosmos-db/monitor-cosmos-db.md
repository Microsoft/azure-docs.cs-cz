---
title: Azure Cosmos DB monitorování | Microsoft Docs
description: Naučte se monitorovat výkon a dostupnost Azure Cosmos DB.
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: bwren
ms.openlocfilehash: acba365ac5cd33b6fbba5910e5e8b469bf111aac
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186848"
---
# <a name="monitoring-azure-cosmos-db"></a>Azure Cosmos DB monitorování
Pokud máte důležité aplikace a obchodní procesy spoléhající se na prostředky Azure, budete chtít tyto prostředky sledovat pro jejich dostupnost, výkon a provoz. Tento článek popisuje data monitorování generovaná databázemi Azure Cosmos a o tom, jak můžete pomocí funkcí Azure Monitor analyzovat tato data a upozorňovat na ně.

## <a name="what-is-azure-monitor"></a>Co je Azure Monitor?
Azure Cosmos DB vytváří data monitorování pomocí [Azure monitor](../azure-monitor/overview.md) , což je plná služba monitorování zásobníku v Azure, která poskytuje kompletní sadu funkcí pro monitorování prostředků Azure kromě prostředků v jiných cloudech a místních prostředích. 

Pokud ještě nejste obeznámeni s monitorováním služeb Azure, začněte s článkem [monitorování prostředků Azure pomocí Azure monitor](../azure-monitor/insights/monitor-azure-resource.md) , který popisuje následující informace:

- Co je Azure Monitor?
- Náklady spojené s monitorováním
- Monitorování dat shromážděných v Azure
- Konfigurace shromažďování dat
- Standardní nástroje v Azure pro analýzu a upozorňování na data monitorování

Následující části jsou uvedené v tomto článku, které popisují konkrétní data získaná z Azure Cosmos DB a poskytují příklady pro konfiguraci shromažďování dat a analýzu těchto dat pomocí nástrojů Azure.

## <a name="azure-monitor-for-cosmos-db-preview"></a>Azure Monitor pro Cosmos DB (Preview)
[Azure monitor pro Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md) je založen na [funkci sešitů Azure monitor](../azure-monitor/app/usage-workbooks.md) a používá stejná data monitorování shromážděná pro Cosmos DB popsané v následujících částech. Tento nástroj vám umožňuje zobrazit celkový výkon, chyby, kapacitu a provozní stav všech vašich Azure Cosmos DBch prostředků ve sjednoceném interaktivním prostředí a využít další funkce Azure Monitor pro podrobnou analýzu a upozorňování. 

![Azure Monitor pro Cosmos DB](media/monitor-cosmos-db/azure-monitor-cosmos-db.png)

## <a name="monitoring-data-collected-from-azure-cosmos-db"></a>Monitorování dat shromážděných z Azure Cosmos DB
Azure Cosmos DB shromažďuje stejné typy dat monitorování jako jiné prostředky Azure, které jsou popsány v tématu [monitorování dat z prostředků Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). Podrobné informace o protokolech a metrikách vytvořených pomocí Azure Cosmos DB najdete v tématu [referenční informace k datům monitorování Azure Cosmos DB](monitor-cosmos-db-reference.md) .

Stránka **Přehled** v Azure Portal pro každou databázi Azure Cosmos obsahuje stručný přehled využití databáze, včetně jejich žádosti a hodinového využití fakturace. Tyto informace jsou užitečné, ale k dispozici je jen malé množství dat monitorování. Některá z těchto dat jsou shromažďována automaticky a k dispozici pro analýzu, jakmile vytvoříte databázi, zatímco můžete povolit další shromažďování dat s určitou konfigurací.

![Stránka Přehled](media/monitor-cosmos-db/overview-page.png)



## <a name="diagnostic-settings"></a>Nastavení diagnostiky
Metriky platforem a protokol aktivit jsou shromažďovány automaticky, ale je nutné vytvořit nastavení diagnostiky pro shromáždění protokolů prostředků nebo jejich přeposílání mimo Azure Monitor. Podrobný postup pro vytvoření nastavení diagnostiky pomocí Azure Portal, CLI nebo PowerShellu najdete v tématu [Vytvoření nastavení diagnostiky pro shromažďování protokolů a metrik platforem v Azure](../azure-monitor/platform/diagnostic-settings.md) .

Při vytváření nastavení diagnostiky určíte, které kategorie protokolů se mají shromáždit. Kategorie pro Azure Cosmos DB jsou uvedené níže spolu s ukázkovými daty.

 * **DataPlaneRequests**: tuto možnost vyberte, pokud chcete protokolovat požadavky back-endu na všechna rozhraní API, která zahrnují účty SQL, Graph, MongoDB, Cassandra a rozhraní API pro tabulky v Azure Cosmos DB. Všimněte si klíčových vlastností: Requestcharge, statusCode, clientIPaddress a partitionID.

    ```
    { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
    ```

* **MongoRequests**: tuto možnost vyberte, pokud chcete protokolovat požadavky iniciované uživateli z front-endu, aby sloužily požadavky na rozhraní API služby Azure Cosmos DB pro MongoDB. Žádosti MongoDB se zobrazí v MongoRequests a také v DataPlaneRequests. Všimněte si klíčových vlastností: Requestcharge, opCode.

    ```
    { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
    ```

* **QueryRuntimeStatistics**: tuto možnost vyberte, pokud chcete protokolovat text dotazu, který se spustil. 

    ```
    { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
    ```

* **PartitionKeyStatistics**: tuto možnost vyberte, pokud chcete protokolovat statistiky klíčů oddílů. V současné době se jedná o velikost úložiště klíčů oddílů (KB). Protokol je vygenerován proti prvním třem klíčům oddílu, které zabírají většinu úložiště dat.

    ```
    { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
    ```

* **Požadavky na metriky**: tuto možnost vyberte, pokud chcete shromažďovat data metrik z Azure Cosmos DB do cílových umístění v nastavení diagnostiky. Jedná se o stejná data shromažďovaná automaticky v metrikách Azure. Shromažďovat data metriky pomocí protokolů zdrojů k analýze obou druhů dat a k odesílání dat metriky mimo Azure Monitor.

## <a name="analyzing-metric-data"></a>Analýza dat metriky
Azure Cosmos DB poskytuje vlastní prostředí pro práci s metrikami. Podrobnosti o používání tohoto prostředí a analýze různých Azure Cosmos DBch scénářů najdete v tématu [monitorování a ladění Azure Cosmos DB metriky z Azure monitor](cosmos-db-azure-monitor-metrics.md) .

Metriky pro Azure Cosmos DB můžete analyzovat pomocí metrik z jiných služeb Azure pomocí Průzkumníka metrik, a to tak, že v nabídce **Azure monitor** otevřete **metriky** . Podrobnosti o použití tohoto nástroje najdete v tématu [Začínáme s Azure Průzkumník metrik](../azure-monitor/platform/metrics-getting-started.md) . Všechny metriky pro Azure Cosmos DB jsou v oboru názvů **Cosmos DB Standardní metriky**. Při přidávání filtru do grafu můžete použít následující dimenze s těmito metrikami:

- collectionName
- DatabaseName
- OperationType
- Oblast
- statusCode


## <a name="analyzing-log-data"></a>Analýza dat protokolu
Data v Azure Monitor protokoly se ukládají v tabulkách, ve kterých každá tabulka má vlastní sadu jedinečných vlastností. Azure Cosmos DB ukládá data v následujících tabulkách.

| Table | Popis |
|:---|:---|
| AzureDiagnostics | Společná tabulka používaná více službami k ukládání protokolů prostředků. Protokoly prostředků z Azure Cosmos DB lze identifikovat pomocí `MICROSOFT.DOCUMENTDB`.   |
| AzureActivity    | Společná tabulka, která ukládá všechny záznamy z protokolu aktivit. 


> [!IMPORTANT]
> Když v nabídce Azure Cosmos DB vyberete **protokoly** , Log Analytics se otevře s oborem dotazu nastaveným na aktuální databázi Azure Cosmos. To znamená, že dotazy protokolu budou zahrnovat pouze data z tohoto prostředku. Pokud chcete spustit dotaz, který zahrnuje data z jiných databází nebo dat z jiných služeb Azure, vyberte z nabídky **Azure monitor** **protokoly** . Podrobnosti najdete [v tématu Rozsah dotazů protokolu a časový rozsah v Azure Monitor Log Analytics](../azure-monitor/log-query/scope.md) .

### <a name="sample-queries"></a>Ukázkové dotazy
Níže jsou uvedené dotazy, které vám pomůžou monitorovat databáze Azure Cosmos.

* Dotaz pro všechny diagnostických protokolů ze služby Azure Cosmos DB pro zadané časové období:

    ```Kusto
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
    ```

* Dotaz na 10 nejvíce nedávno protokolovaných událostí:

    ```Kusto
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | take 10
    ```

* Dotaz pro všechny operace, seskupené podle typu operace:

    ```Kusto
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName
    ```

* Dotaz pro všechny operace, seskupené podle **prostředků**:

    ```Kusto
    AzureActivity | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* Dotaz pro všechny aktivity uživatelů, seskupené podle prostředků:

    ```Kusto
    AzureActivity | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```
* Pro získání všech dotazů větších než 100 ru spojených s daty z **DataPlaneRequests** a **QueryRunTimeStatistics**.

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

* Dotaz, pro kterou trvá déle než 3 milisekund operace:

    ```Kusto
    AzureDiagnostics | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Dotaz, který Agent běží operace:

    ```Kusto
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName, userAgent_s
    ```

* Dotaz pro, pokud byly provedeny dlouho běžící operace:

    ```Kusto
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | project TimeGenerated , duration_s | render timechart
    ```
    
* Získání statistik o klíčích oddílů pro vyhodnocení zešikmení v horních 3 oddílech pro databázový účet:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId
    ```

## <a name="monitor-azure-cosmos-db-programmatically"></a>Programové monitorování služby Azure Cosmos DB
Účtu metriky na úrovni k dispozici na portálu, jako je například využití a celkový počet požadavků na účet úložiště, nejsou k dispozici prostřednictvím rozhraní SQL API. Však můžete načíst data o využití na úrovni kolekce pomocí rozhraní SQL API. K načtení dat na úrovni kolekce, postupujte takto:

* Použití rozhraní REST API [provádět GET na kolekci](https://msdn.microsoft.com/library/mt489073.aspx). V záhlaví x-ms-resource-quota a x-ms-resource využití v odpovědi se vrátí informace kvótu a využití pro kolekci.
* Použití sady .NET SDK, použijte [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) metoda, která vrátí [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) , která obsahuje počet použití vlastnosti jako  **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage**a provádění dalších akcí.

Chcete-li přistupovat k dalším metrikám, použijte [SDK služby Azure Monitor](https://www.nuget.org/packages/Microsoft.Azure.Insights). Dostupné definice metrik může být načten voláním:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

Dotazy k načtení jednotlivých metrik použijte následující formát:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z



## <a name="next-steps"></a>Další kroky

- Odkaz na protokoly a metriky vytvořené pomocí Azure Cosmos DB najdete v tématu informace o [monitorování Azure Cosmos DB dat](monitor-cosmos-db-reference.md) .
- Podrobnosti o monitorování prostředků Azure najdete v tématu [monitorování prostředků Azure pomocí Azure monitor](../azure-monitor/insights/monitor-azure-resource.md) .
