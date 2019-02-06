---
title: Monitorování využití a dotaz metriky prostředků pro službu search – Azure Search
description: Povolení protokolování, získat metriky aktivity dotaz, využití prostředků a další systémové data ze služby Azure Search.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: e76c8ae671333bcbf50995c4bd9345f8434fbea2
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55745958"
---
# <a name="monitor-resource-consumption-and-query-activity-in-azure-search"></a>Monitorování prostředků spotřeby a dotaz aktivity ve službě Azure Search

Na stránce Přehled služby Azure Search můžete zobrazit systémová data o využití prostředků, dotaz metriky a kolik kvóty je možné vytvořit více indexů, indexerů a data zdrojů. Na portálu můžete také použít ke konfiguraci log analytics nebo jiný prostředek, který používá trvalá data kolekce. 

Nastavení protokolů je užitečné pro samoobslužné diagnostiky a zachování provozní historie. Interně existují protokoly na back-endu na krátkou dobu čas dostatečná pro zkoumání a analýzy Pokud založte lístek podpory. Pokud chcete kontrolu a přístup k protokolování informací, měli byste nastavit jedno řešení popisovaných v tomto článku.

V tomto článku najdete informace o monitorování možnosti, jak povolit protokolování a protokolování úložiště a jak zobrazit obsah protokolu.

## <a name="metrics-at-a-glance"></a>Metriky na první pohled

**Využití** a **monitorování** oddíly integrovaná v přehledu stránky sestavy na využití prostředků a dotazování metrik spouštění. Tyto informace k dispozici, jakmile začnete používat službu, bez nezbytné konfigurace. Na této stránce se aktualizují každých několik minut. Pokud jste na dokončení rozhodnutí o [jaké úroveň určený pro produkční úlohy](search-sku-tier.md), nebo jestli se má [upravit počet aktivní repliky a oddíly, které](search-capacity-planning.md), tyto metriky vám můžou pomoct se těchto rozhodnutí Zobrazí, jak rychle se spotřebovávají prostředky a jak dobře aktuální konfigurace zpracuje existující zatížení.

**Využití** kartě se zobrazí dostupnost prostředků relativně vzhledem k aktuální [omezení](search-limits-quotas-capacity.md). Na následujícím obrázku je bezplatná služba, která je omezené na 3 objekty každého typu a přes 50 MB úložiště. Service Basic nebo Standard má vyšší limity, a pokud zvýšíte počet oddílů, maximální velikost úložiště přejde proporcionálně.

![Stav využití vzhledem k omezení účinný](./media/search-monitor-usage/usage-tab.png
 "stav využití vzhledem k omezení účinný")

## <a name="queries-per-second-qps-and-other-metrics"></a>Dotazů za sekundu (QPS) a jiné metriky

**Monitorování** kartě ukazuje klouzavé průměry pro metriky, jako je hledání *dotazy za sekundu* agregovat (QPS), za minutu. 
*Latence hledání* je množství času potřebného vyhledávací službu ke zpracování vyhledávacích dotazů, agregují za minutu. *Omezených vyhledávacích dotazů procento* (není vidět) je procento vyhledávacích dotazů, které byly omezené také agregovaná za minutu.

Tato čísla jsou jen přibližné a jsou určené k poskytují obecnou představu o tom, jak dobře váš systém obsluhuje požadavky. Skutečné QPS může být vyšší nebo nižší než čísla oznámeného na portálu.

![Dotazy na druhou aktivitu](./media/search-monitor-usage/monitoring-tab.png "dotazů za druhou aktivitu")

## <a name="activity-logs"></a>Protokoly aktivit

**Protokolu aktivit** shromažďuje informace z Azure Resource Manageru. Příklady informace nacházející se v protokolu aktivit: vytvoření nebo odstranění služby, aktualizuje skupinu prostředků, kontrola dostupnosti názvu nebo získat přístupový klíč služby pro zpracování požadavku. 

Můžete přistupovat **protokolu aktivit** z levého navigačního podokna, nebo z oznámení v hlavní okno příkazového řádku nebo z **diagnostikovat a řešit problémy** stránky.

Pro provozní úlohy, jako je vytvoření indexu nebo odstranění zdroje dat zobrazí se vám obecné oznámení jako "Získat klíč správce" pro každý požadavek, ale ne konkrétní vlastní akci. Tato úroveň informací je nutné povolit monitorování řešení doplňku.

## <a name="add-on-monitoring-solutions"></a>Monitorování řešení doplňků

Služba Azure Search neukládá data nad rámec objekty, které spravuje, což znamená, že protokol, který musí být uložená externě. Pokud chcete zachovat data protokolu nakonfigurujete prostředcích níže. 

Následující tabulka porovnává možnosti pro ukládání protokolů a přidání podrobné monitorování operací služby a úlohy dotazů pomocí Application Insights.

| Prostředek | Používá pro |
|----------|----------|
| [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) | Protokolované události a metriky dotazu, podle schémat níže, korelují s událostí uživatele ve vaší aplikaci. Toto je jediné řešení, která zohledňuje signály nebo akce uživatelů, událostí mapování z uživatelem iniciované hledání, na rozdíl od filtrovat žádosti odeslané kódem aplikace. Pokud chcete použít tento přístup, kopírování a vkládání kód instrumentace do zdrojových souborů pro informace o postupu žádosti do služby Application Insights. Další informace najdete v tématu [Analýza provozu vyhledávání](search-traffic-analytics.md). |
| [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) | Protokolované události a metriky dotazu, podle schémat níže. Události jsou protokolovány do pracovního prostoru v Log Analytics. Spusťte dotazy na pracovní prostor ke vrací podrobné informace z protokolu. Další informace najdete v tématu [Začínáme se službou Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) |
| [Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Protokolované události a metriky dotazu, podle schémat níže. Události jsou protokolovány do kontejneru objektů Blob a uloženy v souborech JSON. Chcete-li zobrazit obsah souboru pomocí editoru JSON.|
| [Centrum událostí](https://docs.microsoft.com/azure/event-hubs/) | Protokolované události a metriky dotazu, podle schémat uvedeno v tomto článku. Tuto možnost zvolte jako služba alternativní data kolekce pro velmi objemné protokoly. |

Log Analytics a úložištěm objektů Blob jsou dostupné jako bezplatná služba sdílené tak, aby vám ho můžou vyzkoušet zdarma po dobu životnosti vašeho předplatného Azure. Application Insights je zdarma pro registraci a použít, pokud je velikost dat aplikace v rámci určitá omezení (viz [stránce s cenami](https://azure.microsoft.com/pricing/details/monitor/) podrobnosti).

Následující části vás provede kroky pro povolení a používání úložiště objektů Blob v Azure ke shromáždění a přístup k datům protokolů vytvořené operací Azure Search.

## <a name="enable-logging"></a>Povolit protokolování

Protokolování pro úlohy indexování a dotazování je vypnuto ve výchozím nastavení a závisí na doplněk řešení pro protokolování infrastruktury a dlouhodobé externího úložiště. Samostatně jsou jen trvalá data ve službě Azure Search objekty vytváří a spravuje, takže protokoly musí být uloženy jinde.

V této části se dozvíte, jak používat úložiště objektů Blob k ukládání dat protokolu událostí a metrik.

1. [Vytvoření účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) pokud ho ještě nemáte. Je možné je umístit ve stejné skupině prostředků jako Azure Search pro zjednodušení vyčistit později, pokud chcete odstranit všechny prostředky používané v tomto cvičení.

2. Otevřete stránku přehled vaší vyhledávací služby. V levém navigačním podokně přejděte dolů k položce **monitorování** a klikněte na tlačítko **povolit monitorování**.

   ![Povolit monitorování](./media/search-monitor-usage/enable-monitoring.png "povolit monitorování")

3. Vyberte data, která chcete exportovat: Protokoly, metriky nebo obojí. Můžete zkopírovat do účtu úložiště, odesílat do centra událostí nebo exportovat je do Log Analytics.

   Pro archivaci do úložiště objektů Blob, musí existovat jenom účet úložiště. Kontejnery a objekty BLOB se vytvoří při exportu dat protokolu.

   ![Archivní úložiště objektů blob konfigurace](./media/search-monitor-usage/configure-blob-storage-archive.png "konfigurace objektu blob úložiště archivu")

4. Uložte profil.

5. Test protokolování vytváření nebo odstraňování objektů (vytvoří protokol událostí) a odesíláním dotazů (generuje metriky). 

Po uložení profilu je povoleno protokolování. Kontejnery jsou vytvořeny pouze po aktivitu protokolu nebo měr. Když jsou data zkopírována do účtu úložiště, se data naformátovaná jako JSON a umístí do dvou kontejnerů:

* insights – protokoly operationlogs: pro protokoly přenosů služby search
* insights-metrics-pt1m: pro metriky

Bude trvat hodinu, než se zobrazí kontejnery ve službě Blob storage. Existuje jeden objekt blob, za hodinu a kontejner. 

Můžete použít [Visual Studio Code](#Download-and-open-in-Visual-Studio-Code) nebo jiný editor JSON, chcete-li zobrazit soubory. 

### <a name="example-path"></a>Příklad cesty

```
resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2018/m=12/d=25/h=01/m=00/name=PT1H.json
```

## <a name="log-schema"></a>Schéma protokolu
Objekty BLOB obsahující protokoly přenosů služby vyhledávání jsou strukturované, jak je popsáno v této části. Každý objekt blob má jeden kořenový objekt volána **záznamy** obsahující pole objektů protokolu. Každý objekt blob obsahuje záznamy pro všechny operace, které došlo během jedné hodiny.

| Název | Typ | Příklad: | Poznámky |
| --- | --- | --- | --- |
| time |datetime |"2018-12-07T00:00:43.6872559Z" |Časové razítko operace |
| resourceId |řetězec |"/ SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>VÝCHOZÍ/RESOURCEGROUPS/POSKYTOVATELE /<br/> SPOLEČNOSTI MICROSOFT. HLEDÁNÍ/SEARCHSERVICES/SEARCHSERVICE" |Vaše ID prostředku |
| operationName |řetězec |"Query.Search" |Název operace |
| operationVersion |řetězec |"2017-11-11" |Používá rozhraní api-version |
| category |řetězec |"OperationLogs" |Konstanty |
| resultType |řetězec |"Success" |Možné hodnoty: Úspěch nebo neúspěch |
| resultSignature |int |200 |Kód výsledku protokolu HTTP |
| doby trvání v MS |int |50 |Doba trvání operace v milisekundách |
| properties |objekt |v následující tabulce najdete |Objekt obsahující data specifická pro operace |

**Vlastnosti schématu**

| Název | Typ | Příklad: | Poznámky |
| --- | --- | --- | --- |
| Popis |řetězec |"GET /indexes('content')/docs" |Operace koncového bodu |
| Dotaz |řetězec |"?search=AzureSearch&$count=true&api-version=2017-11-11" |Parametry dotazu |
| Dokumenty |int |42 |Počet zpracovaných dokumentů |
| indexName |řetězec |"testindex" |Název přidružený k operaci indexu |

## <a name="metrics-schema"></a>Schématu metrik

Metriky se vám budou zaznamenávat požadavků na dotazy.

| Název | Typ | Příklad: | Poznámky |
| --- | --- | --- | --- |
| resourceId |řetězec |"/ SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111 /<br/>VÝCHOZÍ/RESOURCEGROUPS/POSKYTOVATELE /<br/>SPOLEČNOSTI MICROSOFT. HLEDÁNÍ/SEARCHSERVICES/SEARCHSERVICE" |vaše id prostředku |
| metricName |řetězec |"Latence" |Název metriky |
| time |datetime |"2018-12-07T00:00:43.6872559Z" |časové razítko operace |
| průměr |int |64 |Průměrná hodnota nezpracovanou ukázky metriky časový interval |
| minimum |int |37 |Minimální hodnota nezpracovanou ukázky metriky časový interval |
| maximum |int |78 |Maximální hodnota nezpracovanou ukázky metriky časový interval |
| celkem |int |258 |Celková hodnota nezpracovanou ukázky metriky časový interval |
| count |int |4 |Počet nezpracovaných vzorků sloužící ke generování metriku |
| timegrain |řetězec |"PT1M" |Časový interval metrika ve formátu ISO 8601 |

Všechny metriky se vykazují v minutových intervalech. Každý metrika uvádí minimální, maximální a průměrné hodnoty za minutu.

Metriky SearchQueriesPerSecond minimum je nejnižší hodnota vyhledávací dotazy za sekundu, která byla zaregistrována tohoto minutě. Totéž platí i pro maximální hodnotu. Průměr je agregace napříč celou minutu.
Představte si, že o tomto scénáři během jedné minuty: jedné sekundy vysoké zatížení, který je maximální pro SearchQueriesPerSecond, za nímž následuje 58 sekund průměrné zatížení a nakonec jedné sekundy se jenom jeden dotaz, což je minimální.

Pro ThrottledSearchQueriesPercentage, minimální, maximální, průměrné a celkový počet, všechny mají stejnou hodnotu: procento vyhledávacích dotazů, které byly omezené z celkového počtu vyhledávací dotazy za jednu minutu.

## <a name="download-and-open-in-visual-studio-code"></a>Stáhnout a otevřít v aplikaci Visual Studio Code

Chcete-li zobrazit soubor protokolu můžete použít libovolný editor JSON. Pokud nemáte, doporučujeme [Visual Studio Code](https://code.visualstudio.com/download).

1. Na webu Azure portal otevřete svůj účet úložiště. 

2. V levém navigačním podokně klikněte na tlačítko **objekty BLOB**. Měli byste vidět **přehledy. protokoly operationlogs** a **insights-metrics-pt1m**. Tyto kontejnery vytvořené službou Azure Search při exportu dat protokolu do úložiště objektů Blob.

3. Dokud se nedostanete soubor .json, klikněte na tlačítko dolů hierarchii složek.  Použijte v místní nabídce ke stažení souboru.

Když se soubor stáhne, otevře se v editoru JSON k zobrazení obsahu.

## <a name="use-system-apis"></a>Použití rozhraní API systému
Rozhraní REST API Azure Search a sady .NET SDK poskytují programový přístup k informace o službě metriky, indexu a indexeru a počty dokumentů.

* [Získání statistiky služby](/rest/api/searchservice/get-service-statistics)
* [Získání statistik indexu](/rest/api/searchservice/get-index-statistics)
* [Počet dokumentů](/rest/api/searchservice/count-documents)
* [Získat stav indexeru](/rest/api/searchservice/get-indexer-status)

Pokud chcete povolit pomocí Powershellu nebo rozhraní příkazového řádku Azure, najdete v dokumentaci [tady](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs#how-to-enable-collection-of-diagnostic-logs).

## <a name="next-steps"></a>Další postup

[Správa služby Search v Microsoft Azure](search-manage.md) pro další informace o správě služby a [výkon a optimalizace](search-performance-optimization.md) pro Průvodce laděním.