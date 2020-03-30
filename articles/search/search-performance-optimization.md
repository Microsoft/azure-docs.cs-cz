---
title: Škálování pro zajištění výkonu
titleSuffix: Azure Cognitive Search
description: Naučte se techniky a osvědčené postupy pro ladění výkonu Azure Cognitive Search a konfiguraci optimálního škálování.
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 7c2857de0613be400f83544e1dabe079b7497bbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77212390"
---
# <a name="scale-for-performance-on-azure-cognitive-search"></a>Škálování výkonu v Azure Cognitive Search

Tento článek popisuje osvědčené postupy pro pokročilé scénáře se sofistikovanými požadavky na škálovatelnost a dostupnost.

## <a name="start-with-baseline-numbers"></a>Začít s čísly směrného plánu

Před provedením větší nasazení úsilí, ujistěte se, že víte, co typické zatížení dotazu vypadá. Následující pokyny vám mohou pomoci dospět k číslům dotazů podle směrného plánu.

1. Vyberte cílovou latenci (nebo maximální dobu), kterou by měl požadavek na typické hledání trvat.

1. Vytvořte a otestujte skutečné zatížení proti vaší vyhledávací službě s realistickou sadou dat pro měření těchto sazeb latence.

1. Začněte s nízkým počtem dotazů za sekundu (QPS) a postupně zvyšujte počet spuštěný v testu, dokud latence dotazu neklesne pod předdefinovaný cíl. Toto je důležité měřítko, které vám pomůže naplánovat škálování, jak vaše aplikace roste v používání.

1. Pokud je to možné, znovu použijte připojení HTTP. Pokud používáte azure cognitive search .NET SDK, to znamená, že byste měli znovu použít instanci nebo [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) instance a pokud používáte rozhraní REST API, měli byste znovu použít jeden httpclient.

1. Měnit podstatu požadavků dotazu tak, aby hledání dochází v různých částech indexu. Varianta je důležité, protože pokud budete průběžně provádět stejné požadavky na vyhledávání, ukládání dat do mezipaměti začne, aby výkon vypadat lépe, než by se s více různorodé dotazu set.

1. Měnit strukturu požadavků dotazů tak, aby se zoíkané různé typy dotazů. Ne každý vyhledávací dotaz provádí na stejné úrovni. Například vyhledávání dokumentu nebo návrh hledání je obvykle rychlejší než dotaz s významným počtem omezujících vlastností a filtrů. Složení testu by měla obsahovat různé dotazy, zhruba ve stejných poměrech, jakou byste očekávali v produkčním prostředí.  

Při vytváření těchto testovacích úloh, existují některé charakteristiky Azure Cognitive Search mít na paměti:

+ Je možné přetížení služby tím, že tlačí příliš mnoho vyhledávacích dotazů najednou. V takovém případě se zobrazí kódy odezvy HTTP 503. Chcete-li se vyhnout 503 během testování, začněte s různými rozsahy požadavků na vyhledávání zobrazíte rozdíly v latenci sazby při přidávání dalších požadavků na vyhledávání.

+ Azure Cognitive Search nespouštějící úlohy indexování na pozadí. Pokud vaše služba zpracovává úlohy dotazů a indexování souběžně, vezměte to v úvahu buď zavedením úloh indexování do testů dotazů, nebo zkoumáním možností pro spuštění úloh indexování během pracovní doby mimo špičku.

> [!Tip]
> Můžete simulovat realistické zatížení dotazu pomocí nástrojů pro testování zatížení. Zkuste [zátěžové testování s Azure DevOps](https://docs.microsoft.com/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=azure-devops) nebo použijte některou z těchto [alternativ](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops#alternatives).

## <a name="scale-for-high-query-volume"></a>Měřítko pro velký objem dotazu

Služba je přetížena, když dotazy trvat příliš dlouho nebo když služba začne uvolnění požadavků. Pokud k tomu dojde, můžete problém vyřešit jedním ze dvou způsobů:

+ **Přidání replik**  

  Každá replika je kopií dat, což umožňuje službě požadavky na vyrovnávání zatížení proti více kopiím.  Všechny vyrovnávání zatížení a replikace dat spravuje Azure Cognitive Search a můžete kdykoli změnit počet replik přidělených pro vaši službu. Můžete přidělit až 12 replik ve službě standardní vyhledávání a 3 repliky ve službě základní vyhledávání. Repliky lze upravit buď z [portálu Azure](search-create-service-portal.md) nebo [PowerShell](search-manage-powershell.md).

+ **Vytvoření nové služby na vyšší úrovni**  

  Azure Cognitive Search se k dispozici v [několika úrovních](https://azure.microsoft.com/pricing/details/search/) a každý z nich nabízí různé úrovně výkonu. V některých případech může mít tolik dotazů, že úroveň, na které se nacházejí, nemůže poskytnout dostatečný obrat, i když jsou repliky vyčerpány. V takovém případě zvažte přechod na vyšší úroveň, jako je například úroveň Standard S3, určená pro scénáře s velkým počtem dokumentů a extrémně vysokými úlohami dotazů.

## <a name="scale-for-slow-individual-queries"></a>Škálování pro pomalé jednotlivé dotazy

Dalším důvodem pro vysokou latenci sazby je jeden dotaz trvá příliš dlouho na dokončení. V tomto případě přidání replik nepomůže. Dvě možné možnosti, které mohou pomoci, zahrnují následující:

+ **Zvětšit oddíly**

  Oddíl rozděluje data mezi další výpočetní prostředky. Dva oddíly rozdělit data na polovinu, třetí oddíl rozdělí na třetiny a tak dále. Jeden pozitivní vedlejší efekt je, že pomalejší dotazy někdy provádět rychleji z důvodu paralelní výpočetní. Zaznamenali jsme paralelizaci na dotazy s nízkou selektivitou, jako jsou dotazy, které odpovídají mnoha dokumentům, nebo omezující schopnosti poskytující počty v celém velkém počtu dokumentů. Vzhledem k tomu, že je nutné provést významné výpočty, aby bylo možné zvýšit relevanci dokumentů nebo spočítat počet dokumentů, přidání dalších oddílů pomáhá dotazům dokončit se rychleji.  
   
  Ve službě Standardní vyhledávání může být maximálně 12 oddílů a ve službě Základní vyhledávací služba 1 oddíl. Oddíly lze upravit buď z [portálu Azure](search-create-service-portal.md) nebo [PowerShell](search-manage-powershell.md).

+ **Omezit pole vysoké mohutnosti**

  Pole s vysokou mohutností se skládá z pole s hodnotou líhně nebo filtrovatelné pole, které má významný počet jedinečných hodnot a v důsledku toho spotřebovává významné prostředky při výpočtu výsledků. Například nastavení ID produktu nebo popis pole jako facetable/filterable by se počítat jako vysoká mohutnost, protože většina hodnot z dokumentu do dokumentu jsou jedinečné. Pokud je to možné, omezte počet polí s vysokou mohutností.

+ **Zvýšení úrovně vyhledávání**  

  Přechod na vyšší úroveň Azure Cognitive Search může být další způsob, jak zlepšit výkon pomalých dotazů. Každá vyšší vrstva poskytuje rychlejší procesory a více paměti, které mají pozitivní vliv na výkon dotazu.

## <a name="scale-for-availability"></a>Škálování pro dostupnost

Repliky nejen pomoci snížit latenci dotazu, ale může také umožnit vysokou dostupnost. U jedné repliky byste měli očekávat pravidelné prostoje z důvodu restartování serveru po aktualizacích softwaru nebo u jiných událostí údržby, ke kterým dojde. V důsledku toho je důležité zvážit, pokud vaše aplikace vyžaduje vysokou dostupnost vyhledávání (dotazy) stejně jako zápisy (indexování událostí). Azure Cognitive Search nabízí možnosti s la na všechny nabídky placeného vyhledávání s následujícími atributy:

+ Dvě repliky pro vysokou dostupnost úloh jen pro čtení (dotazy)

+ Tři nebo více replik pro vysokou dostupnost úloh pro čtení a zápis (dotazy a indexování)

Další podrobnosti o tomto, navštivte [Azure Cognitive Search Service Level Agreement](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Vzhledem k tomu, že repliky jsou kopie vašich dat, s více replikumožňuje Azure Cognitive Search provést restartování počítače a údržbu proti jedné repliky, zatímco provádění dotazů pokračuje na jiné repliky. Naopak pokud budete mít repliky pryč, dojde ke snížení výkonu dotazu, za předpokladu, že tyto repliky byly nedostatečně využité prostředek.

## <a name="scale-for-geo-distributed-workloads-and-geo-redundancy"></a>Škálování pro geograficky distribuované úlohy a geografickou redundanci

U geograficky distribuovaných úloh budou mít uživatelé, kteří se nacházejí daleko od hostitelského datového centra, vyšší latenci. Jedním z opatření ke zmírnění rizik je zřídit více vyhledávacích služeb v oblastech s bližší blízkostí k těmto uživatelům.

Azure Cognitive Search aktuálně neposkytuje automatizovanou metodu geografické replikace indexů Azure Cognitive Search napříč oblastmi, ale existují některé techniky, které lze použít, které mohou tento proces usnadnit implementaci a správu. Ty jsou popsány v několika následujících částech.

Cílem geograficky distribuované sady vyhledávacích služeb je mít k dispozici dva nebo více indexů ve dvou nebo více oblastech, kde je uživatel směrován do služby Azure Cognitive Search, která poskytuje nejnižší latenci, jak je vidět v tomto příkladu:

   ![Křížová karta služeb podle regionů][1]

### <a name="keep-data-synchronized-across-multiple-services"></a>Zachování synchronizace dat ve více službách

Existují dvě možnosti pro synchronizaci distribuovaných vyhledávacích služeb, které se skládají buď pomocí [Azure Cognitive Search Indexer](search-indexer-overview.md) nebo Rozhraní API push (označované také jako [rozhraní REST API Azure Cognitive Search).](https://docs.microsoft.com/rest/api/searchservice/)  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>Použití indexerů pro aktualizaci obsahu ve více službách

Pokud již používáte indexer v jedné službě, můžete nakonfigurovat druhý indexer na druhé službě použít stejný objekt zdroje dat, tahání dat ze stejného umístění. Každá služba v každé oblasti má svůj vlastní indexer a cílový index (index vyhledávání není sdílen, což znamená, že data jsou duplikována), ale každý indexer odkazuje na stejný zdroj dat.

Zde je na vysoké úrovni vizuální, co tato architektura bude vypadat.

   ![Jediný zdroj dat s distribuovaným indexerem a kombinacemi služeb][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>Použití rest API pro odesílání aktualizací obsahu na více služeb

Pokud používáte rozhraní AZURE Cognitive Search REST API k [nabízení obsahu v indexu Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice/update-index), můžete udržovat různé vyhledávací služby synchronizované tím, že budete tlačit změny do všech vyhledávacích služeb, kdykoli je vyžadována aktualizace. V kódu nezapomeňte zpracovat případy, kdy se nezdaří aktualizace jedné vyhledávací služby, ale u jiných vyhledávacích služeb se nezdaří.

## <a name="leverage-azure-traffic-manager"></a>Využití Azure Traffic Manageru

[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) umožňuje směrovat požadavky na více geograficky umístěných webů, které jsou pak zálohovány více vyhledávacích služeb. Jednou z výhod Traffic Manager je, že může sondovat Azure Cognitive Search zajistit, že je k dispozici a směrovat uživatele alternativní vyhledávací služby v případě prostojů. Kromě toho pokud směrujete požadavky na vyhledávání prostřednictvím webů Azure, Azure Traffic Manager vám umožní vyvažovat případy, kdy je web nahoře, ale ne Azure Cognitive Search. Zde je příklad toho, co architektura, která využívá Traffic Manager.

   ![Křížová karta služeb podle regionů, s centrálním traffic managerem][3]

## <a name="next-steps"></a>Další kroky

Další informace o cenových úrovních a limitech služeb pro každou z nich najdete v [tématu Limity služeb](search-limits-quotas-capacity.md). Další informace o kombinacích oddílů a replik najdete v [tématu Plánování kapacity.](search-capacity-planning.md)

Pro diskusi o výkonu a ukázky technik diskutovaných v tomto článku, podívejte se na následující video:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
