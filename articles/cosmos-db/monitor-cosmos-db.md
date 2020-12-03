---
title: Azure Cosmos DB monitorování | Microsoft Docs
description: Naučte se monitorovat výkon a dostupnost Azure Cosmos DB.
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/01/2020
ms.author: sngun
ms.custom: subject-monitoring
ms.openlocfilehash: f7cc9d4062ad9a49ff84db6ac035ff53934ae0a0
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532053"
---
# <a name="monitor-azure-cosmos-db"></a>Monitorování služby Azure Cosmos DB
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

## <a name="monitor-overview"></a>Přehled monitorování

Stránka **Přehled** v Azure Portal pro každý účet Azure Cosmos DB obsahuje stručný přehled využití prostředků, jako je třeba celkový počet požadavků, požadavky, které vedly k určitému kódu stavu HTTP, a hodinové fakturaci. Tyto informace jsou užitečné, ale v tomto podokně jsou k dispozici pouze malé množství dat monitorování. Některá z těchto dat jsou shromažďována automaticky a jsou k dispozici pro analýzu ihned po vytvoření prostředku. Můžete povolit další typy shromažďování dat s určitou konfigurací.

## <a name="what-is-azure-monitor"></a>Co je Azure Monitor?

Azure Cosmos DB vytváří data monitorování pomocí [Azure monitor](../azure-monitor/overview.md) , což je plná služba monitorování zásobníku v Azure, která poskytuje kompletní sadu funkcí pro monitorování prostředků Azure kromě prostředků v jiných cloudech a místních prostředích.

Pokud ještě nejste obeznámeni s monitorováním služeb Azure, začněte s článkem [monitorování prostředků Azure pomocí Azure monitor](../azure-monitor/insights/monitor-azure-resource.md) , který popisuje následující koncepty:

* Co je Azure Monitor?
* Náklady spojené s monitorováním
* Monitorování dat shromážděných v Azure
* Konfigurace shromažďování dat
* Standardní nástroje v Azure pro analýzu a upozorňování na data monitorování

Následující části jsou uvedené v tomto článku, které popisují konkrétní data získaná z Azure Cosmos DB a poskytují příklady pro konfiguraci shromažďování dat a analýzu těchto dat pomocí nástrojů Azure.

## <a name="azure-monitor-for-azure-cosmos-db"></a>Azure Monitor pro Azure Cosmos DB

Azure Monitor pro Azure Cosmos DB je založen na [funkci sešitů Azure monitor](../azure-monitor/platform/workbooks-overview.md) a používá stejná data monitorování shromážděná pro Azure Cosmos DB popsané v následujících částech. Pomocí Azure Monitor můžete zobrazit celkový výkon, selhání, kapacitu a provozní stav všech vašich prostředků Azure Cosmos DB ve sjednoceném interaktivním prostředí a využít další funkce Azure Monitor pro účely podrobné analýzy a upozorňování. Další informace najdete v článku [prozkoumání Azure Monitor Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md) .

> [!NOTE]
> Při vytváření kontejnerů se ujistěte, že nevytvoříte dva kontejnery se stejným názvem, ale s různou velikostí písmen. Důvodem je to, že některé části platformy Azure nerozlišují velká a malá písmena a to může vést k nejasnostem/kolizi telemetrie a akcí na kontejnerech s takovými názvy.

## <a name="monitoring-data"></a><a id="monitoring-from-azure-cosmos-db"></a> Data monitorování 

Azure Cosmos DB shromažďuje stejné typy dat monitorování jako jiné prostředky Azure, které jsou popsány v tématu [monitorování dat z prostředků Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). Podrobné informace o protokolech a metrikách vytvořených pomocí Azure Cosmos DB najdete v tématu [referenční informace k datům monitorování Azure Cosmos DB](monitor-cosmos-db-reference.md) .

Stránka **Přehled** v Azure Portal pro každou databázi Azure Cosmos obsahuje stručný přehled využití databáze, včetně jejich žádosti a hodinového využití fakturace. Tyto informace jsou užitečné, ale k dispozici je jen malé množství dat monitorování. Některá z těchto dat jsou shromažďována automaticky a k dispozici pro analýzu, jakmile vytvoříte databázi, zatímco můžete povolit další shromažďování dat s určitou konfigurací.

:::image type="content" source="media/monitor-cosmos-db/overview-page.png" alt-text="Stránka Přehled":::

## <a name="collection-and-routing"></a>Shromažďování a směrování

Metriky platforem a protokol aktivit se shromažďují a ukládají automaticky, ale můžete je směrovat do jiných umístění pomocí diagnostického nastavení.  

Protokoly prostředků se neshromažďují a ukládají, dokud nevytvoříte nastavení diagnostiky a nebudete je směrovat do jednoho nebo více umístění.

Podrobný postup pro vytvoření nastavení diagnostiky pomocí Azure Portal a některých příkladů diagnostických dotazů najdete v tématu [Vytvoření nastavení diagnostiky pro shromažďování protokolů a metrik platforem v Azure](cosmosdb-monitor-resource-logs.md) . Při vytváření nastavení diagnostiky určíte, které kategorie protokolů se mají shromáždit.

Metriky a protokoly, které můžete shromažďovat, jsou popsány v následujících částech.

## <a name="analyzing-metrics"></a><a id="analyze-metric-data"></a> Analýza metrik

Azure Cosmos DB poskytuje vlastní prostředí pro práci s metrikami. Metriky pro Azure Cosmos DB můžete analyzovat pomocí metrik z jiných služeb Azure pomocí Průzkumníka metrik, a to tak, že v nabídce **Azure monitor** otevřete **metriky** . Podrobnosti o použití tohoto nástroje najdete v tématu [Začínáme s Azure Průzkumník metrik](../azure-monitor/platform/metrics-getting-started.md) . Můžete také rezervovat, jak monitorovat [latenci na straně serveru](monitor-server-side-latency.md), [využití jednotky požadavku](monitor-request-unit-usage.md)a [normalizované využití jednotek požadavků](monitor-normalized-request-units.md) pro prostředky Azure Cosmos DB.

Seznam metrik platforem shromážděných pro Azure Cosmos DB najdete v článku [monitorování Azure Cosmos DB dat metriky](monitor-cosmos-db-reference.md#metrics) .

Všechny metriky pro Azure Cosmos DB jsou v oboru názvů **Cosmos DB Standardní metriky**. Při přidávání filtru do grafu můžete použít následující dimenze s těmito metrikami:

* Název kolekce
* DatabaseName
* Typ operace
* Oblast
* StatusCode

Pro referenci se zobrazí seznam [všech metrik prostředků, které jsou podporované v Azure monitor](../azure-monitor/platform/metrics-supported.md).

### <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Zobrazit metriky na úrovni operace pro Azure Cosmos DB

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. V levém navigačním panelu vyberte **monitor** a vyberte **metriky**.

   :::image type="content" source="./media/monitor-cosmos-db/monitor-metrics-blade.png" alt-text="Podokno metrik v Azure Monitor":::

1. V podokně **metriky** > **Vyberte prostředek** > zvolte požadované **předplatné** a **skupinu prostředků**. Jako **typ prostředku** vyberte **Azure Cosmos DB účty**, zvolte jeden ze stávajících účtů Azure Cosmos a pak vyberte **použít**.

   :::image type="content" source="./media/monitor-cosmos-db/select-cosmosdb-account.png" alt-text="Vyberte účet Cosmos DB, pro který chcete zobrazit metriky.":::

1. Dále můžete vybrat metriku ze seznamu dostupných metrik. Můžete vybrat metriky specifické pro jednotky žádosti, úložiště, latenci, dostupnost, Cassandra a další. Podrobné informace o všech dostupných metrikách v tomto seznamu najdete v článku [metriky podle kategorií](monitor-cosmos-db-reference.md) . V tomto příkladu vybereme **jednotky žádosti** a jako hodnotu agregace určíte **průměr** .

   Kromě těchto podrobností můžete také vybrat **časový rozsah** a **časovou členitost** metrik. V poli Max (maximum) si můžete zobrazit metriky za posledních 30 dní.  Po použití filtru se v závislosti na vašem filtru zobrazí graf. Pro vybrané období můžete zobrazit průměrný počet spotřebovaných jednotek žádostí za minutu.  

   :::image type="content" source="./media/monitor-cosmos-db/metric-types.png" alt-text="Vyberte metriku z Azure Portal":::

### <a name="add-filters-to-metrics"></a>Přidání filtrů do metrik

Můžete také filtrovat metriky a graf zobrazený podle konkrétního typu **CollectionName**, **DatabaseName**, **typem operace OperationType**, **region** a **StatusCode**. Chcete-li filtrovat metriky, vyberte možnost **Přidat filtr** a zvolte požadovanou vlastnost, například **typem operace OperationType** , a vyberte hodnotu, jako je například **dotaz**. V grafu se pak zobrazí jednotky žádosti spotřebované pro operaci dotazování pro vybrané období. Operace provedené prostřednictvím uložené procedury nejsou protokolovány, takže nejsou k dispozici v rámci metriky typem operace OperationType.

:::image type="content" source="./media/monitor-cosmos-db/add-metrics-filter.png" alt-text="Přidejte filtr pro výběr členitosti metriky.":::

Metriky můžete seskupit pomocí možnosti **použít rozdělení** . Například můžete seskupit jednotky žádostí na typ operace a zobrazit graf pro všechny operace najednou, jak je znázorněno na následujícím obrázku:

:::image type="content" source="./media/monitor-cosmos-db/apply-metrics-splitting.png" alt-text="Přidat použít dělicí filtr":::

## <a name="analyzing-logs"></a><a id="analyze-log-data"></a> Analýza protokolů

Data v Azure Monitor protokoly se ukládají v tabulkách, ve kterých každá tabulka má vlastní sadu jedinečných vlastností.

Všechny protokoly prostředků v Azure Monitor mají stejná pole následovaná poli pro konkrétní služby. Společné schéma je popsáno v [Azure monitor schéma protokolu prostředků](../azure-monitor/platform/resource-logs-schema.md#top-level-common-schema). Seznam typů protokolů prostředků shromážděných pro Azure Cosmos DB naleznete v tématu [Monitoring Azure Cosmos DB data reference](monitor-cosmos-db-reference.md#resource-logs).

[Protokol aktivit](/azure/azure-monitor/platform/activity-log) je přihlášení k platformě Azure, které poskytuje přehled o událostech na úrovni předplatného. Můžete ji zobrazit nezávisle nebo ji směrovat do Azure Monitor protokolů, kde můžete provádět mnohem složitější dotazy pomocí Log Analytics.  

Azure Cosmos DB ukládá data v následujících tabulkách.

| Tabulka | Popis |
|:---|:---|
| AzureDiagnostics | Společná tabulka používaná více službami k ukládání protokolů prostředků. Protokoly prostředků z Azure Cosmos DB lze identifikovat pomocí `MICROSOFT.DOCUMENTDB` .   |
| AzureActivity    | Společná tabulka, která ukládá všechny záznamy z protokolu aktivit.

### <a name="sample-kusto-queries"></a>Ukázkové dotazy Kusto

> [!IMPORTANT]
> Když v nabídce Azure Cosmos DB vyberete **protokoly** , Log Analytics se otevře s oborem dotazu nastaveným na aktuální účet Azure Cosmos DB. To znamená, že dotazy protokolu budou zahrnovat pouze data z tohoto prostředku. Pokud chcete spustit dotaz, který obsahuje data z jiných účtů nebo dat z jiných služeb Azure, vyberte z nabídky **Azure monitor** **protokoly** . Podrobnosti najdete [v tématu Rozsah dotazů protokolu a časový rozsah v Azure Monitor Log Analytics](../azure-monitor/log-query/scope.md) .

Tady jsou některé dotazy, které můžete zadat do panelu hledání v **protokolu** , abyste mohli monitorovat prostředky Azure Cosmos. Tyto dotazy fungují s [novým jazykem](../azure-monitor/log-query/log-query-overview.md).

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

## <a name="alerts"></a>Výstrahy

Azure Monitor výstrahy proaktivně upozorní na to, že se ve vašich datech monitorování nacházejí důležité podmínky. Umožňují identifikovat a řešit problémy v systému před tím, než si je vaši zákazníci všimnete. Můžete nastavit výstrahy na [metrikách](../azure-monitor/platform/alerts-metric-overview.md), [protokolech](../azure-monitor/platform/alerts-unified-log.md)a [protokolu aktivit](../azure-monitor/platform/activity-log-alerts.md). Různé typy výstrah mají výhody a nevýhody.

V následující tabulce jsou například uvedena několik pravidel upozornění pro vaše prostředky. Podrobný seznam pravidel upozornění najdete v Azure Portal. Další informace najdete v článku [Postup konfigurace výstrah](create-alerts.md) .  

| Typ upozornění | Podmínka | Popis  |
|:---|:---|:---|
|Omezení rychlosti na jednotkách žádosti (výstraha metriky) |Název dimenze: StatusCode, operátor: Equals, hodnoty dimenze: 429  | Upozorní, pokud kontejner nebo databáze překročila zřízenou mez propustnosti. |
|Převzetí služeb při selhání oblasti |Operator: větší než, typ agregace: počet, prahová hodnota: 1 | Při převzetí služeb při selhání jedné oblasti. Tato výstraha je užitečná, pokud jste nepovolili automatické převzetí služeb při selhání. |
| Otočit klíče (výstraha protokolu aktivit)| Úroveň události: informativní, stav: spuštěno| Upozorní na otočení klíčů účtu. Svou aplikaci můžete aktualizovat pomocí nových klíčů. |

## <a name="monitor-azure-cosmos-db-programmatically"></a><a id="monitor-cosmosdb-programmatically"></a> Monitorování Azure Cosmos DB programově

Metriky na úrovni účtu dostupné na portálu, jako je například využití úložiště účtů a celkový počet požadavků, nejsou k dispozici prostřednictvím rozhraní SQL API. Data o využití na úrovni kolekce ale můžete načíst pomocí rozhraní SQL API. Chcete-li načíst data na úrovni kolekce, postupujte následovně:

* Chcete-li použít REST API, [proveďte operaci get pro kolekci](/rest/api/cosmos-db/get-a-collection). Kvóta a informace o využití pro kolekci se vrátí v hlavičce x-MS-Resource-quote a x-MS-Resource-Usage v odpovědi.

* Chcete-li použít sadu .NET SDK, použijte metodu [DocumentClient. ReadDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync) , která vrátí [ResourceResponse](/dotnet/api/microsoft.azure.documents.client.resourceresponse-1) obsahující řadu vlastností použití jako **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage** a další.

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
