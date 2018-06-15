---
title: Aspekty výkonu a optimalizace Azure Search | Microsoft Docs
description: Vyladění výkonu Azure Search a nakonfigurujte optimálního škálování
author: LiamCavanagh
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/01/2017
ms.author: liamca
ms.openlocfilehash: 89c0352723f1ed00784250b566902028af853d10
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
ms.locfileid: "31797758"
---
# <a name="azure-search-performance-and-optimization-considerations"></a>Aspekty výkonu a optimalizace Azure Search
Skvělé vyhledáváním je klíčem k úspěchu pro mnoho mobilních a webových aplikací. Z nemovitosti používat car tržišť online katalogů, rychlé vyhledávání a relevantní výsledky ovlivní zkušeností zákazníků. Tento dokument je určený pro pomoc zjistíte, že osvědčené postupy pro postup co nejlépe využít Azure Search, zejména pro pokročilé scénáře s sofistikované požadavky pro škálovatelnost, vícejazyčné podpory, nebo vlastní hodnocení.  Kromě toho tento dokument popisuje internals a popisuje přístupy, které efektivně fungoval v aplikacích reálného zákazníka.

## <a name="performance-and-scale-tuning-for-search-services"></a>Výkonu a škálování optimalizace pro vyhledávací služby
Nemůžeme se používají pro vyhledávací weby, jako je Bing a Google a vysoký výkon, které nabízejí.  Výsledkem je když zákazníci používají webovým hledání nebo mobilních aplikací, bude očekávané podobné výkonové charakteristiky.  Optimalizace výkonu vyhledávání, jeden z přístupů nejlepší je soustředit na latenci, což je doby trvání se dokončí a vrátí výsledky dotazu.  Při optimalizaci pro vyhledávací latence je důležité pro:

1. Vybrat cílovou latencí (nebo maximální množství času), typické vyhledávací žádosti by měla být provedena.
2. Vytvoření a otestování skutečné pracovní zátěže proti služby vyhledávání prostřednictvím realistické datová sada k měření tyto rychlosti latence.
3. Spustit s nízkou počet dotazů za sekundu (QPS) a pokračovat tak, abyste provést v testu, dokud latence dotazu klesne pod definované cílovou latencí.  Jde důležité srovnávacího testu při plánování pro škálování růstem vaší aplikace využití.
4. Pokud je to možné, opakovaně používat připojení prostřednictvím protokolu HTTP.  Pokud používáte .NET SDK služby Azure Search, znamená to, by měl znovu použít instanci nebo [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) instance, a pokud používáte rozhraní API REST, jeden HttpClient by měl použít znovu.

Při vytváření těchto testovací úlohy, nejsou některé vlastnosti Azure Search mějte na paměti:

1. Je možné nabízené tak mnoho vyhledávání dotazuje najednou, že bude přetížena prostředky k dispozici ve službě Azure Search.  Pokud k tomu dojde, zobrazí se kódy odpovědí HTTP 503.  Z tohoto důvodu je vhodné začít pomocí různých rozsahů vyhledávání žádostí o informace o rozdílech v sazby latence, jak přidat další požadavky search.
2. Odesílání obsahu do služby Azure Search ovlivní celkový výkon a latence služby Azure Search.  Pokud očekáváte, že k odesílání dat, zatímco uživatelé fungují hledání, je důležité přihlédnout tímto zatížením v úvahu v testy.
3. Ne každý vyhledávacího dotazu se provede na stejné úrovni výkonu.  Například dokument vyhledávání nebo vyhledávání návrhu bude obvykle rychlejší než dotaz s velký počet omezující vlastnosti a filtry.  Je vhodné provést různé dotazy, které byste měli vidět v úvahu při vytváření testů.  
4. Varianta požadavky search je důležité, protože pokud průběžně spustit stejné požadavky search, ukládání dat do mezipaměti se spustí aby výkonu vypadat lépe než ji může s dotazem více různorodých nastaveno.

> [!NOTE]
> [Visual Studio zátěžové testování](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) je skutečně vhodný způsob k provedení vaší srovnávacího testu testuje jako umožňuje provést požadavků HTTP, jako byste potřebovali pro spuštění dotazy pro Azure Search a umožňuje paralelního zpracování požadavků.
> 
> 

## <a name="scaling-azure-search-for-high-query-rates-and-throttled-requests"></a>Škálování Azure Search sazby vysoké dotazu a omezení požadavků
Když obdrželi příliš mnoho požadavků omezenému nebo překročit sazby latence cílový z dotazu zvýšené zatížení, můžou se podívat snížení latence sazby v jednom ze dvou způsobů:

1. **Zvýšení replik:** replika je jako kopii dat umožňuje Azure Search načíst vyrovnávat požadavky na více kopií.  Všechny Vyrovnávání zatížení a replikace dat mezi replik spravuje Azure Search a můžete změnit počet replik přidělené pro vaši službu kdykoli.  Můžete přidělit až 12 replik ve standardní službu vyhledávání a 3 replik v základní vyhledávací službu. Repliky může být buď z upravena [portál Azure](search-create-service-portal.md) nebo [prostředí PowerShell](search-manage-powershell.md).
2. **Zvýšení úrovně vyhledávání:** vyhledávání systému Azure se dodává [počet vrstev](https://azure.microsoft.com/pricing/details/search/) a každý z těchto úrovní nabízí různé úrovně výkonu.  V některých případech může mít mnoho dotazy vrstvy, které jsou na neposkytuje počty dostatečně nízkou latencí, i v případě, že repliky podle toho se limit.  Chcete v tomto případě zvažte využití jeden vyšší vyhledávání vrstvami, jako je Azure Search S3 vrstvy, která dobře hodí pro scénáře s velkým počtem dokumentů a velmi vysoké dotazu úlohy.

## <a name="scaling-azure-search-for-slow-individual-queries"></a>Škálování Azure Search pomalé jednotlivých dotazů
Z jednoho dotazu trvá příliš dlouho dokončení je další důvod, proč může být pomalé latence sazby.  V takovém případě přidání repliky nebude zlepšit latenci sazby.  Pro tento případ existují dvě možnosti k dispozici:

1. **Zvýšit oddíly** oddíl je mechanismus pro rozdělení dat mezi další prostředky.  Z tohoto důvodu při přidat druhý oddíl získá vaše data rozdělit na dvě.  Třetí oddíl rozdělí indexu na tři atd.  To také má za následek, že v některých případech pomalé dotazy bude rychlejší z důvodu paralelizace výpočtu.  Existuje několik příkladů kde jsme viděli tento paralelizace velmi dobře fungují s dotazy, které mají nízkou selektivitu dotazy.  Tento postup se skládá dotazů odpovídající mnoho dokumenty nebo při používání omezujících vlastností je třeba počty přes velké počty dokumentů.  Vzhledem k tomu, že existuje mnoho výpočtu skóre důležitosti dokumentů nebo počítat počty dokumentů potřeba, přidání další oddíly může pomoci zajistit další výpočty.  
   
   Může být maximálně 12 oddílů ve standardní službu vyhledávání a 1 oddíl v základní vyhledávací službu.  Oddíly mohou být upravena buď z [portál Azure](search-create-service-portal.md) nebo [prostředí PowerShell](search-manage-powershell.md).
2. **Limit vysokou kardinalitou pole:** vysokou kardinalitou pole se skládá z facetable (kategorizovatelné) nebo filtrování pole, které má velký počet jedinečných hodnot a v důsledku toho trvá velké množství prostředků k výpočtu výsledky přes.   Zkontrolujte například, nastavení produktu ID nebo popisu pole jako facetable (kategorizovatelné) nebo filtrování by pro vysokou kardinalitou protože většina hodnot z dokumentu do dokumentu jsou jedinečné. Je to možné, omezte počet polí vysokou kardinalitou.
3. **Zvýšení úrovně vyhledávání:** přesun až vyšší úroveň Azure Search může být jiný způsob, jak zlepšit výkon dotazů pomalé.  Každý vyšší úroveň také poskytuje rychlejší procesoru a víc paměti, což může mít kladný dopad na výkon dotazů.

## <a name="scaling-for-availability"></a>Škálování dostupnosti
Repliky nejen snížit latenci dotazu, ale můžete povolit také pro vysokou dostupnost.  S jednu repliku byste měli očekávat pravidelné výpadky v důsledku restartování serveru po aktualizace softwaru nebo pro další události údržby, které se vyskytnou.  V důsledku toho je důležité vzít v úvahu, pokud vaše aplikace vyžaduje vysokou dostupnost hledání (dotazy) a také zápisy (indexování událostí).  Služba Azure Search nabízí možnosti SLA na všech nabídek placeného vyhledávání s následujícími atributy:

* 2 replik pro vysokou dostupnost úloh jen pro čtení (dotazy)
* 3 nebo více replik pro vysokou dostupnost úloh pro čtení a zápis (dotazy a indexování)

Další informace o to, najdete [smlouvy o úrovni služeb Azure Search](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Vzhledem k tomu, že repliky jsou kopií vašich dat, s více replik umožňuje Azure Search na počítač restartování a údržba pro jednu repliku současně současně dotazy pokračujte mají být provedeny u jiných replik.  Z tohoto důvodu bude také nutné vzít v úvahu, jak tento výpadek může mít vliv na dotazy, které teď mají mají být provedeny u jednoho menší kopii dat.

## <a name="scaling-geo-distributed-workloads-and-provide-geo-redundancy"></a>Škálování zeměpisné polohy úloh a zadejte geografická redundance
Pro úlohy, zeměpisné polohy zjistíte, že uživatelé nacházející se daleko od datového centra je hostitelem služby Azure Search, budou mít vyšší latence sazby.  Z tohoto důvodu je často důležité, aby byly víc služby vyhledávání v oblastech, které se nacházejí v těsné blízkosti pro tyto uživatele.  Služba Azure Search v oblastech aktuálně neposkytuje metoda automatické geografická replikace indexů Azure Search, ale existují některé techniky, které lze použít které může být tento proces snadno implementovat a spravovat. Tyto jsou uvedeny v následujících částech několik.

Cílem sady zeměpisné polohy služby vyhledávání je mít dvě nebo více indexů, které jsou k dispozici ve dvou nebo více oblastech, kde bude uživatel směrován na službu Azure Search, která poskytuje nejnižší latenci, jak je vidět v tomto příkladu:

   ![Mezi – karta služeb podle oblasti][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>Udržování synchronizace dat mezi několika služby vyhledávání systému Azure
Existují dvě možnosti pro zachování vaší distribuované služby vyhledávání synchronizované které se skládají z buď pomocí [indexeru pro hledání Azure](search-indexer-overview.md) nebo rozhraní API Push (také označuje jako [REST API služby Azure Search](https://docs.microsoft.com/rest/api/searchservice/)).  

### <a name="azure-search-indexers"></a>Indexery vyhledávání systému Azure
Pokud používáte Azure indexeru pro hledání, jsou již import změny dat z centrální úložiště dat jako databázi SQL Azure nebo Azure Cosmos DB. Při vytváření nového hledání služby, můžete jednoduše taky vytvořit nové Indexer vyhledávání Azure pro tuto službu, která odkazuje na tato stejné úložiště. Tímto způsobem, vždy, když se nové změny do úložiště dat, budou se pak indexovat pomocí různých indexery.  

Tady je příklad, jak by vypadat této architektuře.

   ![Single – datový zdroj s kombinací služby a distribuované indexeru][2]

### <a name="push-api"></a>Push rozhraní API
Pokud používáte API služby Azure Search Push na [aktualizovat obsah v indexu Azure Search](https://docs.microsoft.com/rest/api/searchservice/update-index), můžete ponechat vaší různé služby vyhledávání synchronizované vynucením změny všech vyhledávacích služeb vždy, když je nutná aktualizace.  Pokud to je důležité nezapomeňte zpracování případy, kdy se nezdaří aktualizace jeden vyhledávací služby a jednu nebo více aktualizací úspěšné.

## <a name="leveraging-azure-traffic-manager"></a>Využívání Azure Traffic Manager
[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) umožňuje směrování požadavků na více geograficky umístěné weby, které jsou pak zajišťované více služby vyhledávání systému Azure.  Jednou z výhod Traffic Manager je, že ho probe Azure Search a ujistěte se, že je k dispozici směrovat uživatelé služby alternativní vyhledávání v případě výpadku.  Kromě toho pokud jsou směrování požadavky search prostřednictvím webů Azure, Azure Traffic Manager umožňuje můžete načíst vyrovnávání případech, kdy web si ale není Azure Search.  Tady je příklad co na architektuře, která využívá Traffic Manager.

   ![Mezi – karta služeb podle oblasti, s centrální Traffic Manager][3]

## <a name="monitoring-performance"></a>Sledování výkonu
Služba Azure Search nabízí možnost analyzovat a monitorovat výkon služby prostřednictvím [Analýza provozu vyhledávání (STA)](search-traffic-analytics.md). Prostřednictvím STA můžete volitelně protokolovat operace jednotlivých hledání, jakož i agregovaných metrik k účtu Azure Storage, který lze potom zpracována pro analýzu nebo vizualizována ve službě Power BI.  STA metriky můžete zkontrolovat statistiku výkonu tak, jako je například průměrný počet dotazů nebo dobu odezvy na dotazy.  Kromě toho operaci protokolování umožňuje podrobnostem operace konkrétní hledání.

STA je cenným nástrojem pochopit latence sazby z tohoto hlediska Azure Search.  Vzhledem k tomu, že metriky výkonu dotazu protokolována jsou založená na dobu, kterou dotazem vyžádá mají být plně zpracovány ve službě Azure Search (od okamžiku, kdy je požadavek na odeslání), budete moci použít k určení, pokud jsou problémy s latencí z výpisů na straně služby Azure Search IDE služby, například kvůli latence sítě.  

## <a name="next-steps"></a>Další postup
Další informace o omezení cenové úrovně a služby pro každé z nich najdete v tématu [omezení ve službě Azure Search služby](search-limits-quotas-capacity.md).

Navštivte [plánování kapacity](search-capacity-planning.md) Další informace o oddílu a repliky kombinace.

Další podrobnosti o výkonu a zobrazit některé předvádění o tom, jak implementovat optimalizace popsané v tomto článku v následujícím videu:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
