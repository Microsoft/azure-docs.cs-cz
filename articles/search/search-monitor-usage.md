---
title: Monitorování využití a dotaz statistiky prostředků pro službu search – Azure Search
description: Získáte metriky aktivity dotaz, spotřeby prostředků a další data systému ze služby Azure Search.
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
ms.openlocfilehash: 5f8a4e7dcaa1bc2df71246f67d06fc63ae4fcd06
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54883496"
---
# <a name="monitor-an-azure-search-service-in-azure-portal"></a>Monitorování služby Azure Search na webu Azure portal

Na stránce Přehled služby Azure Search můžete zobrazit systémová data o využití prostředků a navíc dotaz metriky, jako jsou dotazy na druhý (QPS), latence dotazu a procento žádostí, které byly omezené. Kromě toho můžete na portálu využít škálu možnosti na platformě Azure pro hlubší shromažďování dat monitorování. 

Tento článek identifikuje a porovnává dostupné možnosti protokolování operace Azure Search. Obsahuje pokyny pro povolení protokolování a protokolů a způsob, jak rozbalit na informacích, které se shromažďují.

Pokud jsou vyplníte lístek podpory, neexistují žádné konkrétní úlohy nebo informace, které je potřeba zadat. Techniky podpory mají potřebné informace k prošetření specifické problémy.  

## <a name="metrics-at-a-glance"></a>Metriky na první pohled

**Využití** a **monitorování** oddíly, které jsou součástí přehled spotřebu úložiště vizualizaci a dotazování metrik spouštění. Tyto informace k dispozici, jakmile začnete používat službu, bez nezbytné konfigurace. Na této stránce se aktualizují každých několik minut. Pokud jste na dokončení rozhodnutí o [jaké úroveň určený pro produkční úlohy](search-sku-tier.md), nebo jestli se má [upravit počet aktivní repliky a oddíly, které](search-capacity-planning.md), tyto metriky vám můžou pomoct se těchto rozhodnutí Zobrazí, jak rychle se spotřebovávají prostředky a jak dobře aktuální konfigurace zpracuje existující zatížení.

**Využití** kartě se zobrazí dostupnost prostředků relativně vzhledem k aktuální [omezení](search-limits-quotas-capacity.md). Na následujícím obrázku je bezplatná služba, která je omezené na 3 objekty každého typu a přes 50 MB úložiště. Service Basic nebo Standard má vyšší limity, a pokud zvýšíte počet oddílů, maximální velikost úložiště přejde proporcionálně.

![Stav využití vzhledem k omezení účinný](./media/search-monitor-usage/usage-tab.png
 "stav využití vzhledem k omezení účinný")

## <a name="queries-per-second-qps-and-other-metrics"></a>Dotazů za sekundu (QPS) a jiné metriky

**Monitorování** kartě ukazuje klouzavé průměry pro metriky, jako je hledání *dotazy za sekundu* agregovat (QPS), za minutu. 
*Latence hledání* je množství času potřebného vyhledávací službu ke zpracování vyhledávacích dotazů, agregují za minutu. *Omezených vyhledávacích dotazů procento* (není vidět) je procento vyhledávacích dotazů, které byly omezené také agregovaná za minutu.

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
| [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) | [Analýza provozu vyhledávání](search-traffic-analytics.md). Toto je jediné řešení, která zachycuje další informace týkající se požadavků, využití nad rámec hodnoty uvedené v následující schémata metriky a protokolování. S tímto přístupem budete kopírování a vkládání kód instrumentace do zdrojových souborů pro směrování informace o žádosti odeslané do služby Application Insights pro analýzu na vstupech termín dotazu, dotazy s nulovou shody a tak dále. Doporučujeme, abyste Power BI jako front-endu analytics k datům uloženým ve službě Application Insights.  |
| [Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | Požadavky a metriky, podle schémat níže. Události jsou protokolovány do kontejneru objektů Blob. Doporučujeme aplikace Excel nebo Power BI jako front-endu analýzy uložených dat v úložišti objektů Blob v Azure.|
| [Centrum událostí](https://docs.microsoft.com/azure/event-hubs/) | Požadavky a metriky, podle schémat uvedeno v tomto článku. Tuto možnost zvolte jako služba alternativní data kolekce pro velmi objemné protokoly. |

Azure search poskytuje monitorování [balíček obsahu Power BI](https://app.powerbi.com/getdata/services/azure-search) tak, aby můžete analyzovat data protokolů. Balíček obsahu se skládá ze sestav nakonfigurovat tak, aby automaticky se připojovat k vašim datům a nabízejí vizuální přehled o vaší vyhledávací služby. Další informace najdete v tématu [stránce nápovědy balíčku obsahu](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/).

Možnost úložiště objektů Blob je k dispozici jako bezplatná sdílených služeb tak, aby vám ho můžou vyzkoušet zdarma po dobu životnosti vašeho předplatného Azure. Následující části vás provede kroky pro povolení a používání úložiště objektů Blob v Azure ke shromáždění a přístup k datům protokolů vytvořené operací Azure Search.

## <a name="enable-logging"></a>Povolit protokolování

Protokolování pro úlohy indexování a dotazování je vypnuto ve výchozím nastavení a závisí na doplněk řešení pro protokolování infrastruktury a externí úložiště. Samostatně je jen trvalá data ve službě Azure Search indexy, takže protokoly musí být uloženy jinde.

V této části se dozvíte, jak používat úložiště objektů Blob tak, aby obsahovala data protokolu událostí a metrik.

1. [Vytvoření účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) pokud ho ještě nemáte. Je možné je umístit ve stejné skupině prostředků jako Azure Search pro zjednodušení vyčistit později, pokud chcete odstranit všechny prostředky používané v tomto cvičení.

2. Otevřete stránku přehled vaší vyhledávací služby. V levém navigačním podokně přejděte dolů k položce **monitorování** a klikněte na tlačítko **povolit monitorování**.

   ![Povolit monitorování](./media/search-monitor-usage/enable-monitoring.png "povolit monitorování")

3. Vyberte data, která chcete exportovat: Protokoly, metriky nebo obojí. Můžete zkopírovat do účtu úložiště, odesílat do centra událostí nebo exportovat je do Log Analytics.

   Pro archivaci do úložiště objektů Blob, musí existovat jenom účet úložiště. Kontejnery a objekty BLOB se vytvoří při exportu dat protokolu.

   ![Archivní úložiště objektů blob konfigurace](./media/search-monitor-usage/configure-blob-storage-archive.png "konfigurace objektu blob úložiště archivu")

4. Uložte profil.

5. Test protokolování vytváření nebo odstraňování objektů (generuje operační protokol) a odesíláním dotazů (generuje metriky). 

Po uložení profilu je povoleno protokolování, kontejnery jsou vytvořeny pouze po události do protokolu nebo měr. Může trvat několik minut, než kontejnery se zobrazí. Je možné [vizualizace dat v Power BI](#analyze-with-power-bi) až bude k dispozici.

Když jsou data zkopírována do účtu úložiště, se data naformátovaná jako JSON a umístí do dvou kontejnerů:

* insights – protokoly operationlogs: pro protokoly přenosů služby search
* insights-metrics-pt1m: pro metriky

Existuje jeden objekt blob, za hodinu a kontejner.

Příklad cesty: `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2018/m=12/d=25/h=01/m=00/name=PT1H.json`

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

## <a name="analyze-with-power-bi"></a>Analyzovat pomocí Power BI

Doporučujeme používat [Power BI](https://powerbi.microsoft.com) pro zkoumání a vizualizace dat, zejména v případě, že jste povolili [Analýza provozu vyhledávání](search-traffic-analytics.md). Další informace najdete v tématu [stránce nápovědy balíčku obsahu](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/).

Připojení vyžadují klíč účtu úložiště název a přístup, který můžete získat z Azure stránek portálu na **přístupové klíče** stránky řídicího panelu vašeho účtu úložiště.

1. Nainstalujte [Power BI Content Pack](https://app.powerbi.com/getdata/services/azure-search). Balíček obsahu přidá předdefinované grafů a tabulek, které jsou užitečné pro analýzu další data zachycená pro prohledání analýzy provozu. 

   Pokud používáte účet Blob storage nebo jiný mechanismus úložiště a instrumentace nebyl přidán do vašeho kódu, můžete přeskočit tento balíček obsahu a použít integrované vizualizace Power BI.

2. Otevřít **Power BI**, klikněte na tlačítko **získat Data** > **služby** > **Azure Search**.

3. Zadejte název účtu úložiště vyberte **klíč** pro ověřování a pak vložte přístupový klíč.

4. Import dat a pak klikněte na tlačítko **zobrazení dat**.

Následující snímek obrazovky ukazuje předdefinovaných sestav a grafů pro analýzu hledání analýzu provozu.

![Řídicí panel Power BI pro službu Azure Search](./media/search-monitor-usage/AzureSearch-PowerBI-Dashboard.png "řídicí panel Power BI pro službu Azure Search")

## <a name="get-sys-info-apis"></a>Získat sys informace o rozhraní API
Rozhraní REST API Azure Search a sady .NET SDK poskytují programový přístup k informace o službě metriky, indexu a indexeru a počty dokumentů.

* [Získání statistiky služby](/rest/api/searchservice/get-service-statistics)
* [Získání statistik indexu](/rest/api/searchservice/get-index-statistics)
* [Počet dokumentů](/rest/api/searchservice/count-documents)
* [Získat stav indexeru](/rest/api/searchservice/get-indexer-status)

Pokud chcete povolit pomocí Powershellu nebo rozhraní příkazového řádku Azure, najdete v dokumentaci [tady](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs#how-to-enable-collection-of-diagnostic-logs).

## <a name="next-steps"></a>Další postup

[Správa služby Search v Microsoft Azure](search-manage.md) pro další informace o správě služby a [výkon a optimalizace](search-performance-optimization.md) pro Průvodce laděním.

Další informace o vytváření úžasných sestav. Zobrazit [Začínáme s Power BI Desktopu](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) podrobnosti.

