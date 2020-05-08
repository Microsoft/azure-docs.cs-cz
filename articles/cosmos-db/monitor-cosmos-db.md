---
title: Azure Cosmos DB monitorování | Microsoft Docs
description: Naučte se monitorovat výkon a dostupnost Azure Cosmos DB.
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: bwren
ms.custom: subject-monitoring
ms.openlocfilehash: eef6ece115afc41fd30d77747eb3e368cf95719c
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780177"
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

Azure Monitor pro Azure Cosmos DB je založen na [funkci sešitů Azure monitor](../azure-monitor/app/usage-workbooks.md) a používá stejná data monitorování shromážděná pro Cosmos DB popsané v následujících částech. Pomocí Azure Monitor můžete zobrazit celkový výkon, selhání, kapacitu a provozní stav všech vašich prostředků Azure Cosmos DB ve sjednoceném interaktivním prostředí a využít další funkce Azure Monitor pro účely podrobné analýzy a upozorňování. Další informace najdete v článku [prozkoumání Azure Monitor Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md) .

> [!NOTE]
> Při vytváření kontejnerů se ujistěte, že nevytvoříte dva kontejnery se stejným názvem, ale s různou velikostí písmen. Důvodem je to, že některé části platformy Azure nerozlišují velká a malá písmena a to může vést k nejasnostem/kolizi telemetrie a akcí na kontejnerech s takovými názvy.

## <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Zobrazit metriky na úrovni operace pro Azure Cosmos DB

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. V levém navigačním panelu vyberte **monitor** a vyberte **metriky**.

   ![Podokno metrik v Azure Monitor](./media/monitor-cosmos-db/monitor-metrics-blade.png)

1. V podokně **metriky** > **Vyberte prostředek** > zvolte požadované **předplatné**a **skupinu prostředků**. Jako **typ prostředku**vyberte **Azure Cosmos DB účty**, zvolte jeden ze stávajících účtů Azure Cosmos a pak vyberte **použít**.

   ![Vyberte účet Cosmos DB, pro který chcete zobrazit metriky.](./media/monitor-cosmos-db/select-cosmosdb-account.png)

1. Dále můžete vybrat metriku ze seznamu dostupných metrik. Můžete vybrat metriky specifické pro jednotky žádosti, úložiště, latenci, dostupnost, Cassandra a další. Podrobné informace o všech dostupných metrikách v tomto seznamu najdete v článku [metriky podle kategorií](monitor-cosmos-db-reference.md) . V tomto příkladu vybereme **jednotky žádosti** a jako hodnotu agregace určíte **průměr** .

   Kromě těchto podrobností můžete také vybrat **časový rozsah** a **časovou členitost** metrik. V poli Max (maximum) si můžete zobrazit metriky za posledních 30 dní.  Po použití filtru se v závislosti na vašem filtru zobrazí graf. Pro vybrané období můžete zobrazit průměrný počet spotřebovaných jednotek žádostí za minutu.  

   ![Vyberte metriku z Azure Portal](./media/monitor-cosmos-db/metric-types.png)

### <a name="add-filters-to-metrics"></a>Přidání filtrů do metrik

Můžete také filtrovat metriky a graf zobrazený podle konkrétního typu **CollectionName**, **DatabaseName**, **typem operace OperationType**, **region**a **StatusCode**. Chcete-li filtrovat metriky, vyberte možnost **Přidat filtr** a zvolte požadovanou vlastnost, například **typem operace OperationType** , a vyberte hodnotu, jako je například **dotaz**. V grafu se pak zobrazí jednotky žádosti spotřebované pro operaci dotazování pro vybrané období. Operace provedené prostřednictvím uložené procedury nejsou protokolovány, takže nejsou k dispozici v rámci metriky typem operace OperationType.

![Přidejte filtr pro výběr členitosti metriky.](./media/monitor-cosmos-db/add-metrics-filter.png)

Metriky můžete seskupit pomocí možnosti **použít rozdělení** . Například můžete seskupit jednotky žádostí na typ operace a zobrazit graf pro všechny operace najednou, jak je znázorněno na následujícím obrázku:

![Přidat použít dělicí filtr](./media/monitor-cosmos-db/apply-metrics-splitting.png)

## <a name="monitoring-data-collected-from-azure-cosmos-db"></a>Monitorování dat shromážděných z Azure Cosmos DB

Azure Cosmos DB shromažďuje stejné typy dat monitorování jako jiné prostředky Azure, které jsou popsány v tématu [monitorování dat z prostředků Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). Podrobné informace o protokolech a metrikách vytvořených pomocí Azure Cosmos DB najdete v tématu [referenční informace k datům monitorování Azure Cosmos DB](monitor-cosmos-db-reference.md) .

Stránka **Přehled** v Azure Portal pro každou databázi Azure Cosmos obsahuje stručný přehled využití databáze, včetně jejich žádosti a hodinového využití fakturace. Tyto informace jsou užitečné, ale k dispozici je jen malé množství dat monitorování. Některá z těchto dat jsou shromažďována automaticky a k dispozici pro analýzu, jakmile vytvoříte databázi, zatímco můžete povolit další shromažďování dat s určitou konfigurací.

![Stránka Přehled](media/monitor-cosmos-db/overview-page.png)

## <a name="analyzing-metric-data"></a>Analýza dat metriky

Azure Cosmos DB poskytuje vlastní prostředí pro práci s metrikami. Podrobnosti o používání tohoto prostředí a analýze různých Azure Cosmos DBch scénářů najdete v tématu [monitorování a ladění Azure Cosmos DB metriky z Azure monitor](cosmos-db-azure-monitor-metrics.md) .

Metriky pro Azure Cosmos DB můžete analyzovat pomocí metrik z jiných služeb Azure pomocí Průzkumníka metrik, a to tak, že v nabídce **Azure monitor** otevřete **metriky** . Podrobnosti o použití tohoto nástroje najdete v tématu [Začínáme s Azure Průzkumník metrik](../azure-monitor/platform/metrics-getting-started.md) . Všechny metriky pro Azure Cosmos DB jsou v oboru názvů **Cosmos DB Standardní metriky**. Při přidávání filtru do grafu můžete použít následující dimenze s těmito metrikami:

- Název kolekce
- DatabaseName
- Typem operace OperationType
- Oblast
- StatusCode

## <a name="analyzing-log-data"></a>Analýza dat protokolu
Data v Azure Monitor protokoly se ukládají v tabulkách, ve kterých každá tabulka má vlastní sadu jedinečných vlastností. Azure Cosmos DB ukládá data v následujících tabulkách.

| Table | Popis |
|:---|:---|
| AzureDiagnostics | Společná tabulka používaná více službami k ukládání protokolů prostředků. Protokoly prostředků z Azure Cosmos DB lze identifikovat pomocí `MICROSOFT.DOCUMENTDB`.   |
| AzureActivity    | Společná tabulka, která ukládá všechny záznamy z protokolu aktivit. 


> [!IMPORTANT]
> Když v nabídce Azure Cosmos DB vyberete **protokoly** , Log Analytics se otevře s oborem dotazu nastaveným na aktuální databázi Azure Cosmos. To znamená, že dotazy protokolu budou zahrnovat pouze data z tohoto prostředku. Pokud chcete spustit dotaz, který zahrnuje data z jiných databází nebo dat z jiných služeb Azure, vyberte z nabídky **Azure monitor** **protokoly** . Podrobnosti najdete [v tématu Rozsah dotazů protokolu a časový rozsah v Azure Monitor Log Analytics](../azure-monitor/log-query/scope.md) .

### <a name="azure-cosmos-db-log-analytics-queries-in-azure-monitor"></a>Azure Cosmos DB Log Analytics dotazy v Azure Monitor

Tady jsou některé dotazy, které můžete zadat do panelu hledání v **protokolu** , abyste mohli monitorovat kontejnery Cosmos Azure. Tyto dotazy fungují s [novým jazykem](../log-analytics/log-analytics-log-search-upgrade.md).

Níže jsou uvedené dotazy, které vám pomůžou monitorovat databáze Azure Cosmos.

* Dotaz na všechny diagnostické protokoly z Azure Cosmos DB v zadaném časovém období:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests"

    ```

* Dotaz na všechny operace seskupené podle prostředků:

    ```Kusto
    AzureActivity 
    | where ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests" 
    | summarize count() by Resource

    ```

* Dotaz na všechny aktivity uživatelů seskupené podle prostředků:

    ```Kusto
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```

## <a name="monitor-azure-cosmos-db-programmatically"></a>Monitorování Azure Cosmos DB programově

Metriky na úrovni účtu dostupné na portálu, jako je například využití úložiště účtů a celkový počet požadavků, nejsou k dispozici prostřednictvím rozhraní SQL API. Data o využití na úrovni kolekce ale můžete načíst pomocí rozhraní SQL API. Chcete-li načíst data na úrovni kolekce, postupujte následovně:

* Chcete-li použít REST API, [proveďte operaci get pro kolekci](https://msdn.microsoft.com/library/mt489073.aspx). Kvóta a informace o využití pro kolekci se vrátí v hlavičce x-MS-Resource-quote a x-MS-Resource-Usage v odpovědi.

* Chcete-li použít sadu .NET SDK, použijte metodu [DocumentClient. ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) , která vrátí [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) obsahující řadu vlastností použití jako **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage**a další.

Pro přístup k dalším metrikám použijte [sadu Azure monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights). Dostupné definice metriky lze načíst voláním:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

Dotazy k načtení jednotlivých metrik používají následující formát:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z



## <a name="next-steps"></a>Další kroky

- Odkaz na protokoly a metriky vytvořené pomocí Azure Cosmos DB najdete v tématu informace o [monitorování Azure Cosmos DB dat](monitor-cosmos-db-reference.md) .
- Podrobnosti o monitorování prostředků Azure najdete v tématu [monitorování prostředků Azure pomocí Azure monitor](../azure-monitor/insights/monitor-azure-resource.md) .
