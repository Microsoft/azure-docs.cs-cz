---
title: Monitorování služby Azure Cosmos DB | Dokumenty společnosti Microsoft
description: Zjistěte, jak sledovat výkon a dostupnost Azure Cosmos DB.
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.openlocfilehash: db9e86706ecd4e5b2526e1d801dda45ed6b345c6
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887244"
---
# <a name="monitoring-azure-cosmos-db"></a>Monitorování Služby Azure Cosmos DB
Pokud máte důležité aplikace a obchodní procesy, které jsou závislé na prostředcích Azure, chcete tyto prostředky sledovat z důvodu jejich dostupnosti, výkonu a provozu. Tento článek popisuje data monitorování generovaná databázemi Azure Cosmos a jak můžete pomocí funkcí Azure Monitoru analyzovat a upozorňovat na tato data.

## <a name="what-is-azure-monitor"></a>Co je Azure Monitor?
Azure Cosmos DB vytváří data monitorování pomocí [Azure Monitoru,](../azure-monitor/overview.md) což je služba monitorování celého zásobníku v Azure, která poskytuje kompletní sadu funkcí pro monitorování vašich prostředků Azure kromě prostředků v jiných cloudech a místních. 

Pokud ještě nejste obeznámeni s monitorováním služeb Azure, začněte s článkem [Sledování prostředků Azure pomocí Azure Monitoru,](../azure-monitor/insights/monitor-azure-resource.md) který popisuje následující:

- Co je Azure Monitor?
- Náklady spojené s monitorováním
- Data monitorování shromážděná v Azure
- Konfigurace shromažďování dat
- Standardní nástroje v Azure pro analýzu a upozorňování na data monitorování

Následující části vycházejí z tohoto článku tím, že popisují konkrétní data shromážděná z Azure Cosmos DB a poskytují příklady pro konfiguraci shromažďování dat a analýzu těchto dat pomocí nástrojů Azure.

## <a name="azure-monitor-for-cosmos-db-preview"></a>Azure Monitor pro Cosmos DB (preview)
[Azure Monitor pro Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md) je založený na [funkci sešitů azure monitoru](../azure-monitor/app/usage-workbooks.md) a používá stejná data monitorování shromážděná pro Cosmos DB popsané v následujících částech. Tento nástroj slouží k zobrazení celkového výkonu, selhání, kapacity a provozního stavu všech prostředků služby Azure Cosmos DB v jednotném interaktivním prostředí a využijte další funkce Azure Monitoru pro podrobnou analýzu a výstrahy. 

![Azure Monitor pro Cosmos DB](media/monitor-cosmos-db/azure-monitor-cosmos-db.png)

## <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Zobrazení metrik úrovně operace pro Azure Cosmos DB

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Na levém navigačním panelu vyberte **Sledovat** a vyberte **Metriky**.

   ![Podokno Metriky ve službě Azure Monitor](./media/monitor-cosmos-db/monitor-metrics-blade.png)

1. V podokně **Metriky** > **Vyberte zdroj** > zvolte požadované **předplatné**a **skupinu prostředků**. Pro **typ prostředku**vyberte **účty Azure Cosmos DB**, vyberte jeden z vašich existujících účtů Azure Cosmos a vyberte **Použít**.

   ![Výběr účtu Cosmos DB pro zobrazení metrik](./media/monitor-cosmos-db/select-cosmosdb-account.png)

1. Dále můžete vybrat metriku ze seznamu dostupných metrik. Můžete vybrat metriky specifické pro vyžádání jednotek, úložiště, latence, dostupnosti, Cassandra a další. Podrobné informace o všech dostupných metrikách v tomto seznamu najdete v článku [Metriky podle kategorií.](monitor-cosmos-db-reference.md) V tomto příkladu vyberte **Request units** a **Avg** jako hodnotu agregace.

   Kromě těchto podrobností můžete také vybrat **časový rozsah** a **rozlišovací** schopnost metrik. Při maximální mlze můžete zobrazit metriky za posledních 30 dní.  Po použití filtru se na základě filtru zobrazí graf. Můžete zobrazit průměrný počet jednotek požadavku spotřebovaných za minutu pro vybrané období.  

   ![Výběr metriky z webu Azure Portal](./media/monitor-cosmos-db/metric-types.png)

### <a name="add-filters-to-metrics"></a>Přidání filtrů do metrik

Můžete také filtrovat metriky a graf zobrazený určitým **názvem CollectionName**, **Názvem_ DatabaseName**, **OperationType**, **Region**a **StatusCode**. Chcete-li metriky filtrovat, vyberte **možnost Přidat filtr** a zvolte požadovanou vlastnost, například **OperationType,** a vyberte hodnotu, například **Dotaz**. Graf pak zobrazí jednotky požadavku spotřebované pro operaci dotazu pro vybrané období. Operace prováděné prostřednictvím uložené procedury nejsou protokolovány, takže nejsou k dispozici v rámci metriky OperationType.

![Přidání filtru pro výběr rozlišovací schopnost metriky](./media/monitor-cosmos-db/add-metrics-filter.png)

Metriky můžete seskupit pomocí možnosti **Použít rozdělení.** Můžete například seskupit jednotky požadavků podle typu operace a zobrazit graf pro všechny operace najednou, jak je znázorněno na následujícím obrázku:

![Přidat filtr použít rozdělení](./media/monitor-cosmos-db/apply-metrics-splitting.png)

## <a name="monitoring-data-collected-from-azure-cosmos-db"></a>Data monitorování shromážděná z Azure Cosmos DB

Azure Cosmos DB shromažďuje stejné druhy dat monitorování jako ostatní prostředky Azure, které jsou popsané v [části Monitorování dat z prostředků Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). Podrobný odkaz na protokoly a metriky vytvořené službou Azure Cosmos DB najdete [v tématu Odkaz na data monitorování Azure](monitor-cosmos-db-reference.md) Cosmos DB.

Stránka **Přehled** na portálu Azure pro každou databázi Azure Cosmos obsahuje stručné zobrazení využití databáze včetně jejího požadavku a využití hodinové fakturace. To je užitečné informace, ale pouze malé množství dostupných dat monitorování. Některá z těchto dat jsou shromažďována automaticky a jsou k dispozici pro analýzu, jakmile vytvoříte databázi, zatímco můžete povolit další shromažďování dat s určitou konfigurací.

![Stránka s přehledem](media/monitor-cosmos-db/overview-page.png)

## <a name="analyzing-metric-data"></a>Analýza dat metriky

Azure Cosmos DB poskytuje vlastní prostředí pro práci s metrikami. Podrobnosti o používání tohoto prostředí a analýze různých scénářů Azure Cosmos DB najdete v tématu [Monitorování a ladění metrik Azure Cosmos DB z Azure Monitoru.](cosmos-db-azure-monitor-metrics.md)

Metriky pro Azure Cosmos DB můžete analyzovat pomocí metrik z jiných služeb Azure pomocí průzkumníka metrik otevřením **metrik z** nabídky **Azure Monitor.** Podrobnosti o používání tohoto nástroje najdete v tématu [Začínáme s Průzkumníkem metrik Azure.](../azure-monitor/platform/metrics-getting-started.md) Všechny metriky pro Azure Cosmos DB jsou ve **standardních metrikách**oboru názvů Cosmos DB . Při přidávání filtru do grafu můžete s těmito metrikami použít následující dimenze:

- CollectionName
- DatabaseName
- Operationtype
- Region (Oblast)
- Statuscode


## <a name="analyzing-log-data"></a>Analýza dat protokolu
Data v protokolech monitorování Azure se ukládají v tabulkách, které každá tabulka má vlastní sadu jedinečných vlastností. Azure Cosmos DB ukládá data v následujících tabulkách.

| Table | Popis |
|:---|:---|
| AzureDiagnostics | Běžná tabulka používaná více službami k ukládání protokolů prostředků. Protokoly prostředků z Azure Cosmos `MICROSOFT.DOCUMENTDB`DB lze identifikovat pomocí .   |
| AzureActivity    | Běžná tabulka, ve které jsou uloženy všechny záznamy z protokolu aktivit. 


> [!IMPORTANT]
> Když vyberete **protokoly** z nabídky Azure Cosmos DB, log analytics se otevře s oborem dotazu nastaveným na aktuální databázi Azure Cosmos. To znamená, že dotazy protokolu budou obsahovat pouze data z tohoto prostředku. Pokud chcete spustit dotaz, který obsahuje data z jiných databází nebo data z jiných služeb Azure, vyberte **protokoly** z nabídky **Azure Monitor.** Podrobnosti najdete [v tématu Rozsah dotazu protokolu a časový rozsah v Azure Monitor Log Analytics.](../azure-monitor/log-query/scope.md)

### <a name="azure-cosmos-db-log-analytics-queries-in-azure-monitor"></a>Dotazy Azure Cosmos DB Log Analytics v Azure Monitoru

Tady jsou některé dotazy, které můžete zadat do vyhledávacího panelu **protokolu,** které vám pomůžou sledovat vaše kontejnery Azure Cosmos. Tyto dotazy pracují s [novým jazykem](../log-analytics/log-analytics-log-search-upgrade.md).

Následují dotazy, které můžete použít ke sledování databází Azure Cosmos.

* Dotaz na všechny diagnostické protokoly z Azure Cosmos DB za zadané časové období:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"

    ```

* Dotaz na 10 naposledy naposledy protokolovaných událostí:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | limit 10
    ```

* Chcete-li dotazovat na všechny operace seskupené podle typu operace:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName
    ```

* Chcete-li dotazovat na všechny operace seskupené podle prostředku:

    ```Kusto
    AzureActivity 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource

    ```

* Chcete-li dotazovat na všechny aktivity uživatele seskupené podle prostředku:

    ```Kusto
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```
* Chcete-li získat všechny dotazy větší než 100 ru spojené s daty z **DataPlaneRequests** a **QueryRunTimeStatistics**.

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

* Dotaz, pro které operace trvá déle než 3 milisekundy:

    ```Kusto
    AzureDiagnostics 
    | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Dotaz, pro kterého agenta je spuštěna operace:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName, userAgent_s
    ```

* Dotaz, kdy byly provedeny dlouho běžící operace:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | project TimeGenerated , duration_s 
    | summarize count() by bin(TimeGenerated, 5s)
    | render timechart
    ```
    
* Chcete-li získat statistiky klíče oddílu k vyhodnocení zkosení přes top 3 oddíly pro databázový účet:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    ```

## <a name="monitor-azure-cosmos-db-programmatically"></a>Programové monitorování Služby Azure Cosmos DB
Metriky na úrovni účtu dostupné na portálu, jako je například využití úložiště účtu a celkové požadavky, nejsou k dispozici prostřednictvím rozhraní API SQL. Data o využití však můžete načíst na úrovni kolekce pomocí sql api. Chcete-li načíst data na úrovni kolekce, postupujte takto:

* Chcete-li použít rozhraní REST API, [proveďte GET na kolekci](https://msdn.microsoft.com/library/mt489073.aspx). Informace o kvótě a použití pro kolekci jsou vráceny v hlavičách x-ms-resource-quota a x-ms-resource-usage v odpovědi.
* Chcete-li použít sdk rozhraní .NET, použijte metodu [DocumentClient.ReadDocumentCollectionAsync,](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) která vrací metodu [ResourceResponse,](https://msdn.microsoft.com/library/dn799209.aspx) která obsahuje řadu vlastností použití, jako je **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage**a další.

Chcete-li získat přístup k dalším metrikám, použijte sadku [Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights). Dostupné definice metrik lze načíst voláním:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

Dotazy k načtení jednotlivých metrik používají následující formát:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z



## <a name="next-steps"></a>Další kroky

- Viz [odkaz na data monitorování Azure Cosmos DB](monitor-cosmos-db-reference.md) pro odkaz na protokoly a metriky vytvořené Službou Azure Cosmos DB.
- Podrobnosti o monitorování prostředků Azure najdete v tématu [Sledování prostředků Azure pomocí Azure Monitoru.](../azure-monitor/insights/monitor-azure-resource.md)
