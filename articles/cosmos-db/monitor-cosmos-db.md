---
title: Azure Cosmos DB monitorování | Microsoft Docs
description: Naučte se monitorovat výkon a dostupnost Azure Cosmos DB.
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/24/2020
ms.author: bwren
ms.custom: subject-monitoring
ms.openlocfilehash: c04d61c6381304382261219f23e99401465590ca
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93099381"
---
# <a name="monitoring-azure-cosmos-db"></a>Azure Cosmos DB monitorování
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Pokud máte důležité aplikace a obchodní procesy spoléhající se na prostředky Azure, budete chtít tyto prostředky sledovat pro jejich dostupnost, výkon a provoz. Tento článek popisuje data monitorování generovaná databázemi Azure Cosmos a o tom, jak můžete pomocí funkcí Azure Monitor analyzovat tato data a upozorňovat na ně.

Data můžete monitorovat pomocí metrik na straně klienta a serveru. Při použití metrik na straně serveru můžete monitorovat data uložená v Azure Cosmos DB pomocí následujících možností:

* **Monitorování z portálu Azure Cosmos DB:** Můžete monitorovat metriky dostupné v rámci účtu Azure Cosmos na kartě **metriky** . Metriky na této kartě zahrnují metriky propustnosti, úložiště, dostupnosti, latence, konzistence a úrovně systému. Ve výchozím nastavení mají tyto metriky dobu uchovávání 7 dní. Další informace najdete v části [monitorování dat shromážděných z Azure Cosmos DB](#monitoring-from-azure-cosmos-db) v tomto článku.

* **Monitorování pomocí metrik ve službě Azure monitor:** Můžete monitorovat metriky svého účtu Azure Cosmos a vytvářet řídicí panely z Azure Monitor. Azure Monitor shromažďuje metriky Azure Cosmos DB ve výchozím nastavení, nemusíte nic konfigurovat explicitně. Tyto metriky se shromažďují s různou úrovní podrobností, členitost se může lišit v závislosti na zvolené metrikě. Ve výchozím nastavení mají tyto metriky dobu uchovávání 30 dnů. Většina metrik, které jsou k dispozici z předchozích možností, je také k dispozici v těchto metrikách. Hodnoty dimenze pro metriky, jako je například název kontejneru, nerozlišují velká a malá písmena. Proto je nutné použít porovnávání bez rozlišování velkých a malých písmen při porovnávání řetězců u těchto hodnot dimenzí. Další informace najdete v části [Analýza dat metrik](#analyze-metric-data) v tomto článku.

* **Monitorování pomocí diagnostických protokolů v Azure monitor:** Můžete monitorovat protokoly svého účtu Azure Cosmos a vytvářet řídicí panely z Azure Monitor. Telemetrie, jako jsou události a trasování, ke kterým dochází při druhé členitosti, se ukládají jako protokoly. Například pokud se propustnost kontejneru změní, vlastnosti účtu Cosmos se změní tyto události jsou zachyceny v rámci protokolů. Tyto protokoly můžete analyzovat spuštěním dotazů na shromážděná data. Další informace najdete v části [Analýza dat protokolu](#analyze-log-data) v tomto článku.

* **Monitorování prostřednictvím kódu programu pomocí sad SDK:** Účet Azure Cosmos můžete sledovat programově pomocí rozhraní .NET, Java, Pythonu Node.js SDK a hlaviček v REST API. Další informace najdete v části [monitorování Azure Cosmos DB programově](#monitor-cosmosdb-programmatically) v tomto článku.

Následující obrázek ukazuje různé možnosti, které jsou k dispozici pro monitorování Azure Cosmos DB účtu prostřednictvím Azure Portal:

:::image type="content" source="media/monitor-cosmos-db/monitoring-options-portal.png" alt-text="Možnosti monitorování dostupné v Azure Portal" border="false":::

Při použití Azure Cosmos DB můžete na straně klienta shromažďovat podrobnosti o požadavcích na požadavky, ID aktivity, informace o výjimce/zásobníku, stav HTTP/dílčí stavový kód, diagnostický řetězec pro ladění všech problémů, ke kterým může dojít. Tyto informace se vyžadují i v případě, že se potřebujete obrátit na tým podpory Azure Cosmos DB.  

## <a name="what-is-azure-monitor"></a>Co je Azure Monitor?

Azure Cosmos DB vytváří data monitorování pomocí [Azure monitor](../azure-monitor/overview.md) , což je plná služba monitorování zásobníku v Azure, která poskytuje kompletní sadu funkcí pro monitorování prostředků Azure kromě prostředků v jiných cloudech a místních prostředích.

Pokud ještě nejste obeznámeni s monitorováním služeb Azure, začněte s článkem [monitorování prostředků Azure pomocí Azure monitor](../azure-monitor/insights/monitor-azure-resource.md) , který popisuje následující informace:

* Co je Azure Monitor?
* Náklady spojené s monitorováním
* Monitorování dat shromážděných v Azure
* Konfigurace shromažďování dat
* Standardní nástroje v Azure pro analýzu a upozorňování na data monitorování

Následující části jsou uvedené v tomto článku, které popisují konkrétní data získaná z Azure Cosmos DB a poskytují příklady pro konfiguraci shromažďování dat a analýzu těchto dat pomocí nástrojů Azure.

## <a name="azure-monitor-for-azure-cosmos-db"></a>Azure Monitor pro Azure Cosmos DB

Azure Monitor pro Azure Cosmos DB je založen na [funkci sešitů Azure monitor](../azure-monitor/platform/workbooks-overview.md) a používá stejná data monitorování shromážděná pro Cosmos DB popsané v následujících částech. Pomocí Azure Monitor můžete zobrazit celkový výkon, selhání, kapacitu a provozní stav všech vašich prostředků Azure Cosmos DB ve sjednoceném interaktivním prostředí a využít další funkce Azure Monitor pro účely podrobné analýzy a upozorňování. Další informace najdete v článku [prozkoumání Azure Monitor Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md) .

> [!NOTE]
> Při vytváření kontejnerů se ujistěte, že nevytvoříte dva kontejnery se stejným názvem, ale s různou velikostí písmen. Důvodem je to, že některé části platformy Azure nerozlišují velká a malá písmena a to může vést k nejasnostem/kolizi telemetrie a akcí na kontejnerech s takovými názvy.

## <a name="monitor-data-collected-from-azure-cosmos-db-portal"></a><a id="monitoring-from-azure-cosmos-db"></a> Monitorování dat shromážděných z portálu Azure Cosmos DB

Azure Cosmos DB shromažďuje stejné typy dat monitorování jako jiné prostředky Azure, které jsou popsány v tématu [monitorování dat z prostředků Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). Podrobné informace o protokolech a metrikách vytvořených pomocí Azure Cosmos DB najdete v tématu [referenční informace k datům monitorování Azure Cosmos DB](monitor-cosmos-db-reference.md) .

Stránka **Přehled** v Azure Portal pro každou databázi Azure Cosmos obsahuje stručný přehled využití databáze, včetně jejich žádosti a hodinového využití fakturace. Tyto informace jsou užitečné, ale k dispozici je jen malé množství dat monitorování. Některá z těchto dat jsou shromažďována automaticky a k dispozici pro analýzu, jakmile vytvoříte databázi, zatímco můžete povolit další shromažďování dat s určitou konfigurací.

:::image type="content" source="media/monitor-cosmos-db/overview-page.png" alt-text="Možnosti monitorování dostupné v Azure Portal":::

## <a name="analyzing-metric-data"></a><a id="analyze-metric-data"></a> Analýza dat metriky

Azure Cosmos DB poskytuje vlastní prostředí pro práci s metrikami.

Metriky pro Azure Cosmos DB můžete analyzovat pomocí metrik z jiných služeb Azure pomocí Průzkumníka metrik, a to tak, že v nabídce **Azure monitor** otevřete **metriky** . Podrobnosti o použití tohoto nástroje najdete v tématu [Začínáme s Azure Průzkumník metrik](../azure-monitor/platform/metrics-getting-started.md) . Všechny metriky pro Azure Cosmos DB jsou v oboru názvů **Cosmos DB Standardní metriky** . Při přidávání filtru do grafu můžete použít následující dimenze s těmito metrikami:

* Název kolekce
* DatabaseName
* Typ operace
* Oblast
* StatusCode

### <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Zobrazit metriky na úrovni operace pro Azure Cosmos DB

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. V levém navigačním panelu vyberte **monitor** a vyberte **metriky** .

   :::image type="content" source="./media/monitor-cosmos-db/monitor-metrics-blade.png" alt-text="Možnosti monitorování dostupné v Azure Portal":::

1. V podokně **metriky** > **Vyberte prostředek** > zvolte požadované **předplatné** a **skupinu prostředků** . Jako **typ prostředku** vyberte **Azure Cosmos DB účty** , zvolte jeden ze stávajících účtů Azure Cosmos a pak vyberte **použít** .

   :::image type="content" source="./media/monitor-cosmos-db/select-cosmosdb-account.png" alt-text="Možnosti monitorování dostupné v Azure Portal":::

1. Dále můžete vybrat metriku ze seznamu dostupných metrik. Můžete vybrat metriky specifické pro jednotky žádosti, úložiště, latenci, dostupnost, Cassandra a další. Podrobné informace o všech dostupných metrikách v tomto seznamu najdete v článku [metriky podle kategorií](monitor-cosmos-db-reference.md) . V tomto příkladu vybereme **jednotky žádosti** a jako hodnotu agregace určíte **průměr** .

   Kromě těchto podrobností můžete také vybrat **časový rozsah** a **časovou členitost** metrik. V poli Max (maximum) si můžete zobrazit metriky za posledních 30 dní.  Po použití filtru se v závislosti na vašem filtru zobrazí graf. Pro vybrané období můžete zobrazit průměrný počet spotřebovaných jednotek žádostí za minutu.  

   :::image type="content" source="./media/monitor-cosmos-db/metric-types.png" alt-text="Možnosti monitorování dostupné v Azure Portal":::

### <a name="add-filters-to-metrics"></a>Přidání filtrů do metrik

Můžete také filtrovat metriky a graf zobrazený podle konkrétního typu **CollectionName** , **DatabaseName** , **typem operace OperationType** , **region** a **StatusCode** . Chcete-li filtrovat metriky, vyberte možnost **Přidat filtr** a zvolte požadovanou vlastnost, například **typem operace OperationType** , a vyberte hodnotu, jako je například **dotaz** . V grafu se pak zobrazí jednotky žádosti spotřebované pro operaci dotazování pro vybrané období. Operace provedené prostřednictvím uložené procedury nejsou protokolovány, takže nejsou k dispozici v rámci metriky typem operace OperationType.

:::image type="content" source="./media/monitor-cosmos-db/add-metrics-filter.png" alt-text="Možnosti monitorování dostupné v Azure Portal":::

Metriky můžete seskupit pomocí možnosti **použít rozdělení** . Například můžete seskupit jednotky žádostí na typ operace a zobrazit graf pro všechny operace najednou, jak je znázorněno na následujícím obrázku:

:::image type="content" source="./media/monitor-cosmos-db/apply-metrics-splitting.png" alt-text="Možnosti monitorování dostupné v Azure Portal":::

## <a name="analyzing-log-data"></a><a id="analyze-log-data"></a> Analýza dat protokolu

Data v Azure Monitor protokoly se ukládají v tabulkách, ve kterých každá tabulka má vlastní sadu jedinečných vlastností. Azure Cosmos DB ukládá data v následujících tabulkách.

| Tabulka | Popis |
|:---|:---|
| AzureDiagnostics | Společná tabulka používaná více službami k ukládání protokolů prostředků. Protokoly prostředků z Azure Cosmos DB lze identifikovat pomocí `MICROSOFT.DOCUMENTDB` .   |
| AzureActivity    | Společná tabulka, která ukládá všechny záznamy z protokolu aktivit.

> [!IMPORTANT]
> Když v nabídce Azure Cosmos DB vyberete **protokoly** , Log Analytics se otevře s oborem dotazu nastaveným na aktuální databázi Azure Cosmos. To znamená, že dotazy protokolu budou zahrnovat pouze data z tohoto prostředku. Pokud chcete spustit dotaz, který zahrnuje data z jiných databází nebo dat z jiných služeb Azure, vyberte z nabídky **Azure monitor** **protokoly** . Podrobnosti najdete [v tématu Rozsah dotazů protokolu a časový rozsah v Azure Monitor Log Analytics](../azure-monitor/log-query/scope.md) .

### <a name="azure-cosmos-db-log-analytics-queries-in-azure-monitor"></a>Azure Cosmos DB Log Analytics dotazy v Azure Monitor

Tady jsou některé dotazy, které můžete zadat do panelu hledání v **protokolu** , abyste mohli monitorovat kontejnery Cosmos Azure. Tyto dotazy fungují s [novým jazykem](../azure-monitor/log-query/log-query-overview.md).

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

## <a name="monitor-azure-cosmos-db-programmatically"></a><a id="monitor-cosmosdb-programmatically"></a> Monitorování Azure Cosmos DB programově

Metriky na úrovni účtu dostupné na portálu, jako je například využití úložiště účtů a celkový počet požadavků, nejsou k dispozici prostřednictvím rozhraní SQL API. Data o využití na úrovni kolekce ale můžete načíst pomocí rozhraní SQL API. Chcete-li načíst data na úrovni kolekce, postupujte následovně:

* Chcete-li použít REST API, [proveďte operaci get pro kolekci](/rest/api/cosmos-db/get-a-collection). Kvóta a informace o využití pro kolekci se vrátí v hlavičce x-MS-Resource-quote a x-MS-Resource-Usage v odpovědi.

* Chcete-li použít sadu .NET SDK, použijte metodu [DocumentClient. ReadDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync) , která vrátí [ResourceResponse](/dotnet/api/microsoft.azure.documents.client.resourceresponse-1) obsahující řadu vlastností použití jako **CollectionSizeUsage** , **DatabaseUsage** , **DocumentUsage** a další.

Pro přístup k dalším metrikám použijte [sadu Azure monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights). Dostupné definice metriky lze načíst voláním:

```http
https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/providers/microsoft.insights/metricDefinitions?api-version=2018-01-01
```

Chcete-li načíst jednotlivé metriky, použijte následující formát:

```http
https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/providers/microsoft.insights/metrics?timespan={StartTime}/{EndTime}&interval={AggregationInterval}&metricnames={MetricName}&aggregation={AggregationType}&`$filter={Filter}&api-version=2018-01-01
```

Další informace najdete v článku [REST API monitorování Azure](../azure-monitor/platform/rest-api-walkthrough.md) .

## <a name="next-steps"></a>Další kroky

* Odkaz na protokoly a metriky vytvořené pomocí Azure Cosmos DB najdete v tématu informace o [monitorování Azure Cosmos DB dat](monitor-cosmos-db-reference.md) .
* Podrobnosti o monitorování prostředků Azure najdete v tématu [monitorování prostředků Azure pomocí Azure monitor](../azure-monitor/insights/monitor-azure-resource.md) .