---
title: Strategie nasazení a osvědčené postupy pro optimalizaci výkonu – Azure Search
description: Zjistěte, techniky a osvědčené postupy pro optimalizaci výkonu Azure Search a konfigurace optimálního škálování.
author: LiamCavanagh
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/02/2019
ms.author: liamca
ms.custom: seodec2018
ms.openlocfilehash: 8a07657d04cee57cb69c9f5f7862fed3e7965716
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61283552"
---
# <a name="deployment-strategies-and-best-practices-for-optimizing-performance-on-azure-search"></a>Strategie nasazení a osvědčené postupy pro optimalizaci výkonu v Azure Search

Tento článek popisuje osvědčené postupy pro pokročilé scénáře se složité požadavky pro zajištění škálovatelnosti a dostupnosti. 

## <a name="develop-baseline-numbers"></a>Vývoj čísla směrného plánu
Při optimalizaci výkonu vyhledávání, měli byste se zaměřit na zkrácení doby provádění dotazu. K tomu je potřeba vědět, vypadá typické dotazové zatížení. Podle následujících pokynů můžete přejít na čísla směrného plánu dotazu.

1. Výběr cílovou latencí (nebo maximální množství času), který žádost o typickém hledání by měla být provedena.
2. Vytvořte a otestujte skutečná zátěž proti vaší vyhledávací služby s datovou sadou reálného měření latence sazby.
3. Začněte v malém počtu dotazů za sekundu (QPS) a postupně zvyšujte počet provedených v testu, dokud latence dotazu klesne pod definovanou cílovou latencí. To je důležité srovnávací test vám pomohou při plánování pro škálování využití růstem vaší aplikace.
4. Kdykoli je to možné, opakovaně používat připojení prostřednictvím protokolu HTTP. Pokud používáte Azure Search .NET SDK, to znamená, že by měl znovu použít instanci nebo [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) instance, a pokud používáte rozhraní REST API, by měl znovu použít jeden HttpClient.
5. Tak, že hledání probíhá přes různé části indexu se liší látku požadavků na dotazy. Varianta je důležité, protože pokud neustále spustí stejný požadavků hledání, ukládání dat do mezipaměti se začátkem výkonu vypadat lépe, než ji může s více různorodé dotazy nastavit.
6. Struktura požadavků na dotazy liší, takže budete mít různé typy dotazů. Ne každá vyhledávací dotaz provádí na stejné úrovni. Například návrh vyhledávání nebo vyhledejte dokument je obvykle rychlejší než dotaz s velký počet omezujících vlastností a filtry. Složení test by měl obsahovat různých dotazů v přibližně stejnou poměry dle očekávání v produkčním prostředí.  

Při vytváření tyto testovací úlohy, existují některé vlastnosti služby Azure Search brát v úvahu:

+ Je možné přetížit služby v jednom okamžiku formou moc velký počet vyhledávacích dotazů. Pokud k tomu dojde, zobrazí se kódy odpovědí protokolu HTTP 503. Aby se zabránilo 503 během testování, spusťte s různými rozsahy žádostí o hledání zjistit rozdíly v rychlosti latence při přidávání více žádostí o hledání.

+ Služba Azure Search nejde spustit indexování úloh na pozadí. Jestliže vaše služba zpracovává dotazů a indexování úloh současně, vzít v úvahu zavedením buď indexování úloh do testování dotazu nebo při zkoumání možností pro spouštění indexování úloh během mimo špičku.

> [!NOTE]
> [Visual Studio zátěžové testování](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) je opravdu dobrým způsobem, jak provádět vaše srovnávacího testu testy jako umožňuje provádění požadavků protokolu HTTP, jako je třeba pro provádění dotazů u Azure Search a umožňuje paralelního zpracování požadavků.
> 
> 

## <a name="scaling-for-high-query-volume-and-throttled-requests"></a>Škálování pro svazek vysokou dotazu a omezené požadavky
Když obdrželi příliš mnoho omezené požadavky nebo překračují sazby latence cíl z zvýšenou dotazové zatížení, můžete se podívat na snížení latence plateb v jednom ze dvou způsobů:

1. **Zvýšení replik:**  Replika je jako kopie vašich dat umožňuje Azure Search k vyrovnávání zatížení způsobeného požadavky na více kopií.  Všechny Vyrovnávání zatížení a replikace dat mezi replik spravuje Azure Search a je možné změnit počet replik přidělena pro vaši službu kdykoli.  Můžete přidělit až 12 replik ve standardní službu vyhledávání a 3 repliky ve službě základní hledání. Repliky mohou být upraveny buď z [webu Azure portal](search-create-service-portal.md) nebo [Powershellu](search-manage-powershell.md).
2. **Zvýšit úroveň hledání:**  Služba Azure Search je k dispozici ve [počet úrovní](https://azure.microsoft.com/pricing/details/search/) a každá z těchto úrovní nabízí různé úrovně výkonu.  V některých případech může mít mnoho dotazů na úrovni, ke kterému jste na neposkytuje sazby za dostatečně s nízkou latencí, i v případě, že repliky jsou maxed navýšení kapacity. V takovém případě můžete chtít zvážit využití jeden z vyšší úrovně vyhledávání, jako je Azure Search S3 vrstvu, je vhodné pro scénáře s velkým počtem dokumentů a velmi vysoké dotazu úlohy.

## <a name="scaling-for-slow-individual-queries"></a>Škálování pomalé jednotlivé dotazy
Dalším důvodem pro sazby vysokou latencí je pomocí jediného dotazu trvá moc dlouho. V takovém případě přidání repliky nepomůže. Dvě možnosti, které můžou pomoct následující možnosti:

1. **Zvýšit oddíly** oddíl je mechanismus pro rozdělení dat na další prostředky. Přidání druhý oddíl na dva rozdělí data, třetí oddíl rozdělí do tří a tak dále. Jednu pozitivní vedlejší efekt je, že pomalejší dotazy někdy provádět rychlejší z důvodu paralelní výpočty. Jsme jste si poznamenali paralelizace dotazů s nízkou selektivitu, jako jsou dotazy, které odpovídají mnoha dokumenty nebo omezující vlastnosti poskytuje počty přes velký počet dokumentů. Protože významné výpočtu se vyžaduje ke stanovení skóre relevanci dokumenty nebo mají spočítat počet dokumentů, přidávání dalších oddílů pomáhá dotazy rychleji.  
   
   Může být maximálně 12 oddílů ve standardní službu vyhledávání a 1 oddíl ve službě základní hledání.  Oddíly mohou být upraveny buď z [webu Azure portal](search-create-service-portal.md) nebo [Powershellu](search-manage-powershell.md).

2. **Limit vysokou kardinalitou pole:** Pole vysokou kardinalitou se skládá z facetable nebo filtrovatelná pole, která má velký počet jedinečných hodnot a proto spotřebovávají značné množství prostředků při výpočtu výsledků. Například nastavení pole ID produktu nebo popis, jako filtrovatelné a kategorizovatelné by byl započítán jako vysokou kardinalitou protože většina hodnot z dokumentu do dokumentu jsou jedinečné. Kdykoli je to možné, omezte počet polí velkou mohutností.

3. **Zvýšit úroveň hledání:**  Přesun do vyšší úrovně Azure Search může být jiný způsob, jak zlepšit výkon pomalých dotazů. Každá vyšší úroveň poskytuje rychlejší procesory a více paměti, které mají pozitivní dopad na výkon dotazů.

## <a name="scaling-for-availability"></a>Škálování pro dostupnost
Repliky nejen pomoct snížit latenci dotazů, ale můžete také povolit pro zajištění vysoké dostupnosti. S jednou replikou měli byste očekávat pravidelné výpadku v důsledku restartování serveru po aktualizacích softwaru nebo pro jiné události údržby, ke kterým dojde.  V důsledku toho je důležité vzít v úvahu, pokud vaše aplikace vyžaduje vysokou dostupnost služby vyhledávání (dotazy) a také zápisy (indexování události). Služba Azure Search nabízí SLA možnosti na všech placených hledání nabídek s následujícími atributy:

* 2 repliky pro zajištění vysoké dostupnosti úloh jen pro čtení (dotazy)
* 3 nebo více replik pro zajištění vysoké dostupnosti pro čtení i zápis úlohy (dotazů a indexování)

Další podrobnosti o to, najdete [smlouvu o úrovni služeb Azure Search](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Protože repliky jsou kopie vašich dat, s několika replikami umožňuje Azure Search k sadě machine restartování nebo Údržba na jednu repliku zároveň umožní provádění dotazů, pokračujte na další repliky. Naopak pokud nepodniknete repliky jinam, bude, vznikne vám snížení výkonu dotazů, za předpokladu, že tyto repliky se prostředek nevyužitých.

## <a name="scaling-for-geo-distributed-workloads-and-geo-redundancy"></a>Geograficky distribuované úlohy a geografické redundanci škálování
Geograficky distribuované úlohy a bude mít uživatele, kteří se nacházejí daleko od datového centra, hostování Azure Search vyšší míru latence. Jeden omezení rizik je zřídit několik vyhledávací služby v oblasti se blíže na tyto uživatele. Služba Azure Search napříč oblastmi aktuálně neposkytuje metodu automatizované geografické replikaci indexů Azure Search, ale existují některé techniky, které je možné, které můžete provést tento postup snadno implementovat a spravovat. Tyto jsou popsány v následujících částech.

Cílem sady geograficky distribuované služby vyhledávání je mít dva nebo více indexů, které jsou k dispozici ve dvou nebo více oblastech, kde se směruje uživatele do služby Azure Search, která poskytuje nejnižší latenci, jak je vidět v tomto příkladu:

   ![Karta různé služby v jednotlivých oblastech][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>Udržování synchronizace dat napříč několika službami Azure Search
Existují dvě možnosti pro zachování vašich distribuovaných vyhledávací služby synchronizace které tvoří buď pomocí [indexeru Azure Search](search-indexer-overview.md) nebo rozhraní Push API (také nazývané [REST API služby Azure Search](https://docs.microsoft.com/rest/api/searchservice/)).  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>Použití indexerů pro aktualizaci obsahu na několika službách

Pokud už používáte indexer na jednu službu, můžete nakonfigurovat druhý indexer na druhou službu, která používá stejný objekt zdroje dat, načítání dat ze stejného umístění. Každá služba v každé z nich má vlastní indexeru a cílový index (ve vyhledávacím korpusu služby není sdílená, což znamená, že data jsou duplikovaná), ale každý indexer odkazuje stejného datového zdroje.

Tady je podrobný vizuálu této architektuře by vypadat.

   ![Jeden zdroj dat s kombinací služby a distribuované indexeru][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>Pomocí rozhraní REST API pro vkládání obsahu aktualizací na několika službách
Pokud používáte rozhraní REST API pro Azure Search [push obsah v indexu Azure Search](https://docs.microsoft.com/rest/api/searchservice/update-index), abyste mohli různé vyhledávací služby synchronizace podle doručením (push) změny do všech vyhledávacích služeb pokaždé, když se vyžaduje se aktualizace. Ve vašem kódu Ujistěte se, že pro zpracování v případech, kdy aktualizace jeden vyhledávací služby nezdaří, ale pro ostatní služby search se nezdaří.

## <a name="leverage-azure-traffic-manager"></a>Využijte Azure Traffic Manageru
[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) umožňuje směrování požadavků na několika geograficky rozmístěná weby, které jsou potom se opírá o několik služeb Azure Search. Jednou z výhod Traffic Manager je, že můžete sběru dat Azure Search k zajištění, že je k dispozici a směrování uživatelů do alternativní hledání služeb v případě výpadku. Kromě toho pokud jsou směrování žádostí o hledání prostřednictvím webů Azure, Azure Traffic Manager umožňuje načíst zůstatek případech, kdy webu nahoru, ale ne Azure Search. Tady je příklad jaké architektury, které využívá Traffic Manageru.

   ![Mezi – karta služeb podle oblastí, s využitím centrální Traffic Manageru][3]

## <a name="monitor-performance"></a>Monitorování výkonu
Azure Search nabízí možnost analyzovat a sledovat výkon vaší služby prostřednictvím [Analýza provozu vyhledávání](search-traffic-analytics.md). Když tuto funkci povolit a přidat instrumentaci vaší klientské aplikace, můžete volitelně protokolovat operace jednotlivé hledání, jakož i agregovaná metrika k účtu služby Azure Storage, který může pak zpracovány pro analýzu nebo vizualizují v Power BI. Metriky zachycení tímto způsobem poskytují výkon statistiky, jako je průměrný počet dotazů nebo pomalejší doby odezvy. Kromě toho operaci protokolování umožňuje přejít k podrobnostem konkrétní vyhledávací operace.

Analýza provozu je užitečné pro pochopení latence kurzy z této perspektivě Azure Search. Protože metriky výkonu dotazů protokoluje jsou založeny na čas, který dotaz na úplné zpracování ve službě Azure Search (od okamžiku, kdy je požádali o odeslání navýšení kapacity), je možné využit k určí, jestli problémy s latencí je z výpisů nebo na straně služby Azure Search integrované vývojové prostředí služby, jako třeba latence sítě.  

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
