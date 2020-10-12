---
title: Škálování pro zajištění výkonu
titleSuffix: Azure Cognitive Search
description: Naučte se techniky a osvědčené postupy pro optimalizaci výkonu Azure Kognitivní hledání a konfigurace optimálního škálování.
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 5fd949466978714fe1dc0c4ccc67a3cb8f993314
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88934952"
---
# <a name="scale-for-performance-on-azure-cognitive-search"></a>Škálování pro výkon v Azure Kognitivní hledání

Tento článek popisuje osvědčené postupy pro pokročilé scénáře se sofistikovanými požadavky na škálovatelnost a dostupnost.

## <a name="start-with-baseline-numbers"></a>Začínáme s čísly směrného plánu

Než budete mít větší úsilí na nasazení, ujistěte se, že víte, jak vypadá typické načítání dotazů. Následující pokyny vám pomůžou s příchodem na čísla dotazů na základní hodnoty.

1. Vyberte cílovou latenci (nebo maximální dobu), po kterou by měla být dokončena typická žádost o hledání.

1. Vytvoření a otestování reálné úlohy pro vaši vyhledávací službu pomocí realistické sady dat pro měření těchto latencí.

1. Začněte s nízkým počtem dotazů za sekundu (QPS) a pak postupně zvyšujte počet provedených v testu, dokud latence dotazu neklesne pod předdefinovaný cíl. Toto je důležitý srovnávací test, který vám pomůže při plánování škálování, protože vaše aplikace roste v používání.

1. Pokud je to možné, znovu použijte připojení HTTP. Pokud používáte sadu Azure Kognitivní hledání .NET SDK, znamená to, že byste měli znovu použít instanci nebo instanci [SearchIndexClient](/dotnet/api/microsoft.azure.search.searchindexclient) a pokud používáte REST API, měli byste znovu použít jeden HttpClient.

1. Lišící se v závislosti na požadavcích na dotazy, takže vyhledávání probíhá přes různé části indexu. Variace je důležitá, protože pokud průběžně spouštíte stejné požadavky na hledání, zahájí ukládání dat do mezipaměti lepší výkon, než může s více různými množinami dotazů.

1. Můžete měnit strukturu požadavků na dotazy, abyste získali různé typy dotazů. Ne každý vyhledávací dotaz provádí na stejné úrovni. Například vyhledávání dokumentů nebo návrh hledání je obvykle rychlejší než dotaz s významným počtem omezujících vlastností a filtrů. Složení testu by mělo zahrnovat různé dotazy, přibližně stejné poměry, jako byste očekávali v produkčním prostředí.  

Při vytváření těchto testovacích úloh jsou k dispozici některé charakteristiky Kognitivní hledání Azure, které je potřeba vzít v úvahu:

+ Je možné přetížit vaši službu tím, že najednou zadáváme příliš mnoho vyhledávacích dotazů. Pokud k tomu dojde, zobrazí se kódy odpovědí HTTP 503. Abyste se vyhnuli 503 během testování, začněte s různými rozsahy žádostí o hledání, abyste viděli rozdíly v sazbách latence při přidávání dalších požadavků hledání.

+ Azure Kognitivní hledání nespouští úlohy indexování na pozadí. Pokud vaše služba současně zpracovává úlohy dotazů a indexování, vezměte tuto možnost v úvahu tím, že zavedete indexování úloh do testů dotazů nebo prozkoumáte možnosti spouštění úloh indexování v době mimo špičku.

> [!Tip]
> Můžete simulovat reálné zatížení dotazů pomocí nástrojů pro zátěžové testování. Vyzkoušejte [zátěžové testování pomocí Azure DevOps](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=azure-devops) nebo použijte některou z těchto [alternativ](/azure/devops/test/load-test/overview?view=azure-devops#alternatives).

## <a name="scale-for-high-query-volume"></a>Škálování pro velký objem dotazů

Služba je přetížena v případě, že dotazy trvají příliš dlouho nebo když služba zahajuje žádosti. Pokud k tomu dojde, můžete problém vyřešit jedním ze dvou způsobů:

+ **Přidat repliky**  

  Každá replika je kopií vašich dat, což umožňuje službě vyrovnávat zatížení žádostí proti více kopiím.  Veškeré vyrovnávání zatížení a replikace dat spravuje Azure Kognitivní hledání a kdykoli můžete změnit počet replik, které jsou pro vaši službu vyhrazené. V rámci standardní vyhledávací služby můžete přidělit až 12 replik a 3 repliky ve službě Basic Search. Repliky je možné upravit buď z [Azure Portal](search-create-service-portal.md) , nebo pomocí [PowerShellu](search-manage-powershell.md).

+ **Vytvoření nové služby na vyšší úrovni**  

  Azure Kognitivní hledání je součástí [řady vrstev](https://azure.microsoft.com/pricing/details/search/) a každá z nich nabízí různé úrovně výkonu. V některých případech můžete mít tolik dotazů, že úroveň, na kterou se nacházíte, nemůže poskytovat dostatečné vyřízení, a to i v případě, že se repliky vyčerpáním. V takovém případě zvažte přesunutí do vyšší úrovně, například úrovně Standard S3, navržené pro scénáře s velkým počtem dokumentů a velmi vysokým zatížením dotazů.

## <a name="scale-for-slow-individual-queries"></a>Škálování pro pomalé jednotlivé dotazy

Dalším důvodem pro vysokou latenci je, že dokončení jednoho dotazu trvá příliš dlouho. V takovém případě vám nebudou přidány repliky. K dispozici jsou dvě možné možnosti, které mohou obsahovat následující informace:

+ **Zvětšit oddíly**

  Oddíl rozdělí data mezi další výpočetní prostředky. Dva oddíly rozdělí data na polovinu, třetí oddíl je rozdělí na třetinu a tak dále. Jedním z pozitivních vedlejších účinků je to, že pomalejší dotazy někdy provádějí rychleji v důsledku paralelního zpracování. Poznamenali jsme paralelní zpracování dotazů s nízkou selektivitou, jako jsou dotazy, které odpovídají mnoha dokumentům, nebo omezující vlastnosti, které poskytují počty přes velký počet dokumentů. Vzhledem k tomu, že je potřeba významné výpočty, aby bylo možné určit skóre relevanci dokumentů nebo počet dokumentů, přidání dalších oddílů pomáhá rychleji provádět dotazy.  
   
  Ve standardní službě vyhledávání může být maximálně 12 oddílů a 1 oddíl služby Basic Search. Oddíly lze upravit buď z [Azure Portal](search-create-service-portal.md) , nebo z [prostředí PowerShell](search-manage-powershell.md).

+ **Omezit pole vysoké mohutnosti**

  Pole s vysokou mohutnou sestává z pole s vysokou hodnotou, které má velký počet jedinečných hodnot, a výsledkem je, že při výpočtu výsledků spotřebovává významné prostředky. Například když nastavíte pole ID produktu nebo popis jako plošku nebo filtr, bude se počítat jako vysoká mohutnost, protože většina hodnot z dokumentu do dokumentu je jedinečná. Pokud je to možné, omezte počet polí vysoké mohutnosti.

+ **Zvýšit úroveň vyhledávání**  

  Přechod na vyšší úroveň Kognitivní hledání Azure může být jiný způsob, jak zlepšit výkon pomalých dotazů. Každá vyšší úroveň poskytuje rychlejší procesory a více paměti, které mají pozitivní dopad na výkon dotazů.

## <a name="scale-for-availability"></a>Škálování pro dostupnost

Repliky neumožňují nejen snížit latenci dotazů, ale můžou taky umožňovat vysokou dostupnost. U jedné repliky byste měli očekávat pravidelné výpadky, protože se server restartuje po aktualizacích softwaru nebo pro jiné události údržby, ke kterým dojde. V důsledku toho je důležité vzít v úvahu, jestli vaše aplikace vyžaduje vysokou dostupnost hledání (dotazů) a také zápisy (indexování událostí). Azure Kognitivní hledání nabízí možnosti smlouvy SLA pro všechny placené nabídky vyhledávání s následujícími atributy:

+ Dvě repliky pro vysokou dostupnost úloh jen pro čtení (dotazů)

+ Tři nebo více replik pro vysokou dostupnost úloh pro čtení a zápis (dotazy a indexování)

Další podrobnosti najdete na webu [Azure Kognitivní hledání smlouva SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Vzhledem k tomu, že repliky jsou kopiemi vašich dat, může Azure Kognitivní hledání provádět restartování počítače a údržbu proti jedné replice, zatímco provádění dotazů pokračuje na dalších replikách. Naopak pokud ponecháte repliky, budete mít k disgradaci výkonu dotazů. za předpokladu, že tyto repliky byly prostředkem, který se používá.

## <a name="scale-for-geo-distributed-workloads-and-geo-redundancy"></a>Škálování pro geograficky distribuované úlohy a geografickou redundanci

Pro geograficky distribuovaná zatížení budou mít uživatelé, kteří se nacházejí daleko od datového centra hostitele, vyšší míru latence. Jedním z rizik je zřídit několik vyhledávacích služeb v oblastech s užším okolím pro tyto uživatele.

Azure Kognitivní hledání v současné době neposkytuje automatizovanou metodu geografické replikace indexů Azure Kognitivní hledání napříč různými oblastmi, ale existují některé techniky, které mohou tento proces snadno implementovat a spravovat. Ty jsou popsány v následujících částech.

Cílem geografické distribuované sady vyhledávacích služeb je mít dva nebo více indexů dostupných ve dvou nebo více oblastech, kde je uživatel směrován do služby Azure Kognitivní hledání, která poskytuje nejnižší latenci, jak je vidět v tomto příkladu:

   ![Mezi kartami služeb podle oblasti][1]

### <a name="keep-data-synchronized-across-multiple-services"></a>Udržování dat synchronizovaných napříč více službami

Existují dvě možnosti, jak udržovat služby distribuované služby Search v synchronizaci, které se skládají buď pomocí služby [azure kognitivní hledání indexer](search-indexer-overview.md) , nebo rozhraní API push (také označovaného jako [kognitivní hledání Azure REST API](/rest/api/searchservice/)).  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>Použití indexerů pro aktualizaci obsahu ve více službách

Pokud již indexer používáte v jedné službě, můžete u druhé služby nakonfigurovat druhý indexer tak, aby používal stejný objekt zdroje dat, a nastavovat data ze stejného umístění. Každá služba v každé oblasti má svůj vlastní indexer a cílový index (váš index vyhledávání není sdílený, což znamená, že data jsou duplicitní), ale každý indexer odkazuje na stejný zdroj dat.

Tady je přehled toho, co by architektura vypadala jako.

   ![Jeden zdroj dat s kombinacemi distribuovaných indexerů a služeb][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>Použití rozhraní REST API pro doručování aktualizací obsahu ve více službách

Pokud používáte službu Azure Kognitivní hledání REST API k [nabízení obsahu v indexu služby azure kognitivní hledání](/rest/api/searchservice/update-index), můžete uchovávat různé služby vyhledávání v synchronizaci tím, že zadáte změny ve všech vyhledávacích službách pokaždé, když se vyžaduje aktualizace. V kódu se ujistěte, že se nezdařily případy, kdy aktualizace jedné služby vyhledávání selže, ale u jiných vyhledávacích služeb je úspěšná.

## <a name="leverage-azure-traffic-manager"></a>Využití Azure Traffic Manager

[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) umožňuje směrovat požadavky na více geograficky umístěných webů, které jsou následně zajištěny několika vyhledávacími službami. Jednou z výhod Traffic Manager je to, že se dá vyzkoušet Azure Kognitivní hledání a zajistit tak, že bude k dispozici a směrovat uživatele na alternativní vyhledávací služby v případě výpadku. Kromě toho, pokud odesíláte žádosti o vyhledávání prostřednictvím webů Azure, vám Azure Traffic Manager umožňuje vyrovnávat zatížení případů, kde web není, ale není Azure Kognitivní hledání. Tady je příklad, jak architektura, která využívá Traffic Manager.

   ![Křížové karty služeb podle oblasti s centrálním Traffic Manager][3]

## <a name="next-steps"></a>Další kroky

Další informace o cenových úrovních a omezeních služeb pro každé z nich najdete v tématu [omezení služby](search-limits-quotas-capacity.md). Další informace o kombinacích oddílů a replik najdete v tématu [plánování kapacity](search-capacity-planning.md) .

Diskuzi o výkonu a demonstračních technikách popsaných v tomto článku najdete v následujícím videu:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png