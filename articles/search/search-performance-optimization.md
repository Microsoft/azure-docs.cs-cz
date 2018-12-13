---
title: Azure Search výkon a optimalizace aspekty – Azure Search
description: Zjistěte, techniky a osvědčené postupy pro optimalizaci výkonu Azure Search a konfigurace optimálního škálování.
author: LiamCavanagh
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/01/2017
ms.author: liamca
ms.custom: seodec2018
ms.openlocfilehash: 0a98e7f05e766d47a5ea9293409a74a6fafbf837
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310207"
---
# <a name="azure-search-performance-and-optimization-considerations"></a>Azure Search výkon a optimalizace důležité informace
Ideální vyhledávací prostředí je klíčem k úspěchu pro mnoho mobilních a webových aplikací. Z nemovitosti používat tržišť automobilu na online katalogy, rychlé hledání a relevantní výsledky bude mít vliv na prostředí pro zákazníky. Tento dokument je určený pro pomoc zjistíte, že nejlepší postupy jejich naplno využít Azure Search, zejména pro pokročilé scénáře se službou sofistikované požadavky na škálovatelnost, vícejazyčnou podporu, nebo vlastní pořadí.  Kromě toho tento dokument popisuje interní informace a zahrnuje přístupy, které efektivně pracovat v aplikacích skutečných zákazníků.

## <a name="performance-and-scale-tuning-for-search-services"></a>Výkon a škálování optimalizace pro vyhledávací služby
Můžeme se používají pro vyhledávací weby, jako je Bing nebo Google a vysoký výkon, které nabízejí.  Proto když zákazníci používají povoleno vyhledávání webových nebo mobilních aplikací, se očekávají podobným výkonem.  Optimalizace výkonu vyhledávání, jeden z osvědčených postupů při a zaměřte se na latenci, což je doba trvání dotazu dokončí, a vrátí výsledky.  Při optimalizaci pro vyhledávací latence je potřeba:

1. Výběr cílovou latencí (nebo maximální množství času), který žádost o typickém hledání by měla být provedena.
2. Vytvořte a otestujte skutečná zátěž proti vaší vyhledávací služby s datovou sadou reálného měření latence sazby.
3. Začněte v malém počtu dotazů za sekundu (QPS) a dál větší počet provedených v testu, dokud latence dotazu klesne pod definovanou cílovou latencí.  To je důležité srovnávací test vám pomohou při plánování pro škálování využití růstem vaší aplikace.
4. Kdykoli je to možné, opakovaně používat připojení prostřednictvím protokolu HTTP.  Pokud používáte Azure Search .NET SDK, to znamená, že by měl znovu použít instanci nebo [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) instance, a pokud používáte rozhraní REST API, by měl znovu použít jeden HttpClient.

Při vytváření tyto testovací úlohy, existují některé vlastnosti služby Azure Search brát v úvahu:

1. Jde o nasdílení změn tak mnoho vyhledávací dotazy v jednom okamžiku cítili zahlcení prostředkům dostupným ve službě Azure Search.  Pokud k tomu dojde, zobrazí se kódy odpovědí protokolu HTTP 503.  Z tohoto důvodu je vhodné začít s různými rozsahy žádostí o hledání zjistit rozdíly v rychlosti latence při přidávání více žádostí o hledání.
2. Nahrávání obsahu do služby Azure Search bude mít vliv na celkový výkon a latenci služby Azure Search.  Pokud plánujete odeslat data, zatímco uživatelé provádíte hledání, je důležité vzít v úvahu tuto úlohu v testech.
3. Ne každá vyhledávací dotaz provede na stejné úrovni výkonu.  Například návrh vyhledávání nebo vyhledejte dokument bude obvykle rychlejší než dotaz s velký počet omezujících vlastností a filtry.  Doporučujeme provést různé dotazy, které byste měli vidět v úvahu při vytváření testů.  
4. Varianta žádostí o hledání je důležité, protože pokud neustále spustí stejný požadavků hledání, ukládání dat do mezipaměti se začátkem výkonu vypadat lépe, než ji může s více různorodé dotazy nastavit.

> [!NOTE]
> [Visual Studio zátěžové testování](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) je opravdu dobrým způsobem, jak provádět vaše srovnávacího testu testy jako umožňuje provádění požadavků protokolu HTTP, jako je třeba pro provádění dotazů u Azure Search a umožňuje paralelního zpracování požadavků.
> 
> 

## <a name="scaling-azure-search-for-high-query-rates-and-throttled-requests"></a>Škálování Azure Search pro dotaz vysoké sazby a omezuje požadavky
Když obdrželi příliš mnoho omezené požadavky nebo překračují sazby latence cíl z zvýšenou dotazové zatížení, můžete se podívat na snížení latence plateb v jednom ze dvou způsobů:

1. **Zvýšení replik:**  Replika je jako kopie vašich dat umožňuje Azure Search k vyrovnávání zatížení způsobeného požadavky na více kopií.  Všechny Vyrovnávání zatížení a replikace dat mezi replik spravuje Azure Search a je možné změnit počet replik přidělena pro vaši službu kdykoli.  Můžete přidělit až 12 replik ve standardní službu vyhledávání a 3 repliky ve službě základní hledání. Repliky mohou být upraveny buď z [webu Azure portal](search-create-service-portal.md) nebo [Powershellu](search-manage-powershell.md).
2. **Zvýšit úroveň hledání:**  Služba Azure Search je k dispozici ve [počet úrovní](https://azure.microsoft.com/pricing/details/search/) a každá z těchto úrovní nabízí různé úrovně výkonu.  V některých případech může mít mnoho dotazů na úrovni, ke kterému jste na neposkytuje sazby za dostatečně s nízkou latencí, i v případě, že repliky jsou maxed navýšení kapacity.  V takovém případě můžete chtít zvážit využití jeden z vyšší úrovně vyhledávání, jako je Azure Search S3 vrstvu, je velmi vhodná pro scénáře s velkým počtem dokumentů a velmi vysoké dotazu úlohy.

## <a name="scaling-azure-search-for-slow-individual-queries"></a>Škálování Azure Search pro jednotlivé pomalých dotazů
Dalším důvodem, proč může být pomalé. sazby latence je z jednoho dotazu trvá moc dlouho.  V takovém případě přidání repliky nebude zlepšit latenci sazby.  Pro tento případ existují dvě možnosti k dispozici:

1. **Zvýšit oddíly** oddíl je mechanismus pro rozdělení dat na další prostředky.  Z tohoto důvodu když přidáte druhý oddíl získá vaše data rozdělení na dva.  Třetí oddíl indexu rozdělí tři atd.  To také má za následek, že v některých případech pomalé dotazy bude rychlejší z důvodu paralelizace výpočtu.  Existuje několik příkladů, kde jsme viděli toto paralelizace velmi dobře fungují s dotazy, které mají nízké selektivitu dotazy.  To se skládá z dotazů, které odpovídají počet dokumentů nebo když je potřeba poskytnout počty přes velký počet dokumentů "faceting".  Protože velké množství výpočtů potřebných ke stanovení skóre relevanci dokumenty nebo mají spočítat počet dokumentů, přidávání dalších oddílů usnadňuje poskytování dalších výpočtu.  
   
   Může být maximálně 12 oddílů ve standardní službu vyhledávání a 1 oddíl ve službě základní hledání.  Oddíly mohou být upraveny buď z [webu Azure portal](search-create-service-portal.md) nebo [Powershellu](search-manage-powershell.md).
2. **Limit vysokou kardinalitou pole:** Pole vysokou kardinalitou se skládá z facetable nebo filtrovatelná pole, který má velký počet jedinečných hodnot a proto má velké množství prostředků k výpočtu výsledků přes.   Například nastavení pole ID produktu nebo popis, jako filtrovatelné a kategorizovatelné s žádným pro velkou mohutností protože většina hodnot z dokumentu do dokumentu jsou jedinečné. Kdykoli je to možné, omezte počet polí velkou mohutností.
3. **Zvýšit úroveň hledání:**  Přesun do vyšší úrovně Azure Search může být jiný způsob, jak zlepšit výkon pomalých dotazů.  Každá vyšší úroveň také poskytuje rychlejší procesory a větší množství paměti, který může mít kladný dopad na výkon dotazů.

## <a name="scaling-for-availability"></a>Škálování pro dostupnost
Repliky nejen pomoct snížit latenci dotazů, ale můžete také povolit pro zajištění vysoké dostupnosti.  S jednou replikou měli byste očekávat pravidelné výpadku v důsledku restartování serveru po aktualizacích softwaru nebo pro jiné události údržby, ke kterým dojde.  V důsledku toho je důležité vzít v úvahu, pokud vaše aplikace vyžaduje vysokou dostupnost služby vyhledávání (dotazy) a také zápisy (indexování události).  Služba Azure Search nabízí SLA možnosti na všech placených hledání nabídek s následujícími atributy:

* 2 repliky pro zajištění vysoké dostupnosti úloh jen pro čtení (dotazy)
* 3 nebo více replik pro zajištění vysoké dostupnosti pro čtení i zápis úlohy (dotazů a indexování)

Další podrobnosti o to, najdete [smlouvu o úrovni služeb Azure Search](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Protože repliky jsou kopie vašich dat, s několika replikami umožňuje Azure Search k sadě machine restartování nebo Údržba na jednu repliku v době zároveň umožní dotazy pokračujte mají být provedeny na jiné repliky.  Z tohoto důvodu bude také nutné vzít v úvahu, jak tento výpadek může mít vliv na dotazy, které mají být provedeny na jeden menší kopii dat teď mají.

## <a name="scaling-geo-distributed-workloads-and-provide-geo-redundancy"></a>Škálování geograficky distribuované úlohy a poskytují geografickou redundanci
Geograficky distribuované úlohy a zjistíte, že budou mít uživatelé, které jsou umístění daleko od datového centra, který je hostitelem služby Azure Search vyšší míru latence.  Z tohoto důvodu je často potřeba mít více vyhledávací služby v oblasti, které jsou v těsné blízkosti pro tyto uživatele.  Služba Azure Search napříč oblastmi aktuálně neposkytuje metodu automatizované geografické replikaci indexů Azure Search, ale existují některé techniky, které je možné, které můžete provést tento postup snadno implementovat a spravovat. Tyto jsou popsány v následujících částech.

Cílem sady geograficky distribuované služby vyhledávání je mít dva nebo více indexů, které jsou k dispozici ve dvou nebo více oblastech, kde se budou směrovat uživatele do služby Azure Search, která poskytuje nejnižší latenci, jak je vidět v tomto příkladu:

   ![Karta různé služby v jednotlivých oblastech][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>Udržování synchronizace dat napříč několika službami Azure Search
Existují dvě možnosti pro zachování vašich distribuovaných vyhledávací služby synchronizace které tvoří buď pomocí [indexeru Azure Search](search-indexer-overview.md) nebo rozhraní Push API (také nazývané [REST API služby Azure Search](https://docs.microsoft.com/rest/api/searchservice/)).  

### <a name="azure-search-indexers"></a>Indexery Azure Search
Pokud používáte Azure Search indexeru, již importujete změny dat z centrální úložiště dat, jako je například Azure SQL Database nebo Azure Cosmos DB. Při vytváření nového hledání služby, můžete jednoduše taky vytvořit nový Indexer Azure Search pro tuto službu, která odkazuje na tento stejné úložiště. Tak pokaždé, když se nové změny přijít do úložiště dat, se pak indexovat pomocí různých indexery.  

Tady je příklad toho, co by vypadat této architektury.

   ![Jeden zdroj dat s kombinací služby a distribuované indexeru][2]

### <a name="push-api"></a>Push API
Pokud používáte Push API služby Azure Search k [aktualizovat obsah v indexu Azure Search](https://docs.microsoft.com/rest/api/searchservice/update-index), abyste mohli různé vyhledávací služby synchronizace podle doručením (push) změny do všech vyhledávacích služeb pokaždé, když se vyžaduje se aktualizace.  Při tomto postupu je třeba Ujistěte se, že pro zpracování v případech, kdy aktualizace jeden vyhledávací služby selže a jednu nebo více aktualizací úspěšné.

## <a name="leveraging-azure-traffic-manager"></a>Využití Azure Traffic Manageru
[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) umožňuje směrování požadavků na několika geograficky rozmístěná weby, které jsou potom se opírá o několik služeb Azure Search.  Jednou z výhod Traffic Manager je, že můžete sběru dat Azure Search k zajištění, že je k dispozici a směrování uživatelů do alternativní hledání služeb v případě výpadku.  Kromě toho pokud jsou směrování žádostí o hledání prostřednictvím webů Azure, Azure Traffic Manager umožňuje načíst zůstatek případech, kdy webu nahoru, ale ne Azure Search.  Tady je příklad jaké architektury, které využívá Traffic Manageru.

   ![Mezi – karta služeb podle oblastí, s využitím centrální Traffic Manageru][3]

## <a name="monitoring-performance"></a>Sledování výkonu
Azure Search nabízí možnost analyzovat a sledovat výkon vaší služby prostřednictvím [analýzy provozu vyhledávání (STA)](search-traffic-analytics.md). Prostřednictvím STA můžete volitelně protokolovat operace jednotlivé hledání, jakož i agregovaná metrika k účtu služby Azure Storage, který může pak zpracovány pro analýzu nebo vizualizují v Power BI.  Pomocí metrik STA, můžete zkontrolovat výkon statistiky, jako je průměrný počet dotazů nebo pomalejší doby odezvy.  Kromě toho operaci protokolování umožňuje přejít k podrobnostem konkrétní vyhledávací operace.

STA je cenným nástrojem pro pochopení latence kurzy z této perspektivě Azure Search.  Protože metriky výkonu dotazů protokoluje jsou založeny na čas, který dotaz na úplné zpracování ve službě Azure Search (od okamžiku, kdy je požádali o odeslání navýšení kapacity), je možné využit k určí, jestli problémy s latencí je z výpisů nebo na straně služby Azure Search integrované vývojové prostředí služby, jako třeba latence sítě.  

## <a name="next-steps"></a>Další postup
Další informace o omezeních cenové úrovně a služby pro každé z nich najdete v tématu [omezení služby Azure Search](search-limits-quotas-capacity.md).

Navštivte [plánování kapacity](search-capacity-planning.md) získat další informace o kombinaci oddílů a replik.

Další podrobnosti o výkonu a zobrazíte některé ukázky implementace optimalizace popisovaných v tomto článku z následujícího videa:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
