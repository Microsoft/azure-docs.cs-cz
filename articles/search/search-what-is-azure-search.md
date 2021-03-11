---
title: Úvod do služby Azure Cognitive Search
titleSuffix: Azure Cognitive Search
description: Azure Kognitivní hledání je plně spravovaná cloudová vyhledávací služba od Microsoftu. Přečtěte si o případech použití, vývojovém pracovním postupu, porovnání s ostatními produkty Microsoftu pro vyhledávání a o tom, jak začít.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 03/09/2021
ms.custom: contperf-fy21q1
ms.openlocfilehash: e17d08d09814c135af3e0b4fc299b6e6f42326d4
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102549885"
---
# <a name="what-is-azure-cognitive-search"></a>Co je Azure Cognitive Search?

Azure Kognitivní hledání ([dříve označované jako "Azure Search"](whats-new.md#new-service-name)) je cloudová vyhledávací služba, která vývojářům poskytuje rozhraní API a nástroje pro vytváření bohatých funkcí vyhledávání přes soukromý, heterogenní obsah ve webových, mobilních a podnikových aplikacích. 

Hledání je základem jakékoli aplikace, která umožňuje obsah uživatelům, a to s využitím běžných scénářů, jako je katalog nebo hledání dokumentů, hledání webů elektronického obchodování nebo dolování znalostí pro datové vědy. Rozhraní API a architektura Kognitivní hledání zjednodušují přidávání sofistikovaného načítání informací do libovolného řešení.

Vyhledávací služba má následující součásti:

+ Vyhledávací modul pro fulltextové vyhledávání
+ Trvalé úložiště indexovaných obsahu vlastněných uživatelem
+ Rozhraní API pro indexování a dotazování na obsah
+ Volitelná [rozšíření založená na AI](cognitive-search-concept-intro.md), vytváření prohledávatelných obsahu je mimo obrázky, nezpracovaný nestrukturovaný text, soubory aplikací
+ Volitelná integrace s dalšími službami Azure pro data, strojové učení/AI, monitorování a zabezpečení
+ Volitelná implementace [sémantického vyhledávání (Preview)](semantic-search-overview.md) pro vylepšení důležitosti

Vyhledávací služba se nachází mezi externím úložištěm dat, která obsahují vaše neindexovaná data, a klientskou aplikací odesílající požadavky na dotaz do indexu vyhledávání a zpracovává odpověď.

![Architektura Azure Kognitivní hledání](media/search-what-is-azure-search/azure-search-diagram.svg "Architektura Azure Kognitivní hledání")

Hledání můžete externě integrovat s dalšími službami Azure ve formě *indexerů* , které automatizují přijímání a načítání dat ze zdrojů dat Azure, a *dovednosti* , které zahrnují spotřební text AI z Cognitive Services, jako je například analýza obrázků a textu nebo vlastní AI, který vytvoříte v Azure Machine Learning nebo zabalíte do Azure Functions.

## <a name="inside-a-search-service"></a>V rámci vyhledávací služby

V samotné službě Search se tyto dvě primární úlohy *indexují* a *dotazují*. 

+ [Indexování](search-what-is-an-index.md) je proces sání, který načte obsah do vyhledávací služby a umožňuje ho prohledávat. Interně se příchozí text zpracovává do tokenů a ukládá se do obrácených indexů pro rychlé prověřování. Můžete nahrát libovolný text, který je ve formátu dokumentů JSON.

  Kromě toho, pokud obsah zahrnuje smíšené soubory, máte možnost Přidat *rozšíření AI* pomocí [dovedností rozpoznávání](cognitive-search-working-with-skillsets.md). Rozšíření AI může extrahovat text vložený do souborů aplikace a také odvodit text a strukturu z netextových souborů analýzou obsahu. 

  Dovednosti, které tuto analýzu poskytují, jsou předdefinovány od Microsoftu nebo vlastními dovednostmi, které vytvoříte. Následná analýza a transformace můžou mít za následek nové informace a struktury, které dříve neexistovaly, a poskytují tak vysoký výkon pro mnoho scénářů vyhledávání a vědomostí o dolování.

+ [Dotazování](search-query-overview.md) může nastat po naplnění indexu pomocí prohledávatelných textů, když klientská aplikace odesílá požadavky na dotazy službě vyhledávání a zpracovává odpovědi. Všechny provádění dotazů se provádí pomocí vyhledávacího indexu, který vytvoříte, vlastníte a uložíte v rámci služby. V klientské aplikaci jsou možnosti vyhledávání definované pomocí rozhraní API z Azure Kognitivní hledání a můžou zahrnovat vyladění relevance, automatické dokončování, porovnávání synonym, přibližné porovnávání, porovnávání vzorů, filtrování a řazení.

Funkce se zveřejňuje prostřednictvím jednoduchého rozhraní [REST API](/rest/api/searchservice/) nebo [.NET SDK](search-howto-dotnet-sdk.md), které zakrývá zákonitou složitost načítání informací. Můžete také použít Azure Portal pro správu služeb a správu obsahu, a to pomocí nástrojů pro vytváření prototypů a dotazování na indexy a dovednosti. Služba běží v cloudu, proto infrastrukturu a dostupnost spravuje Microsoft.

## <a name="why-use-cognitive-search"></a>Proč použít Kognitivní hledání

Azure Kognitivní hledání je vhodné pro následující scénáře aplikací:

+ Konsoliduje heterogenní obsah do privátního uživatelsky definovaného vyhledávacího indexu.

+ Snadná implementace funkcí souvisejících s vyhledáváním: optimalizace závažnosti, omezující navigace, filtry (včetně hledání geografického prostoru), mapování synonym a automatické dokončování.

+ Transformujte velké nerozlišené textové nebo obrázkové soubory nebo soubory aplikace uložené ve službě Azure Blob Storage nebo Cosmos DB do prohledávatelných dokumentů JSON. To se dosáhne při indexování prostřednictvím [dovedností rozpoznávání](cognitive-search-concept-intro.md) , které přidávají externí zpracování.

+ Přidejte jazykovou nebo vlastní analýzu textu. Pokud máte obsah, který není v angličtině, Azure Kognitivní hledání podporuje analyzátory Lucene i procesory přirozeného jazyka společnosti Microsoft. Můžete také nakonfigurovat analyzátory, abyste dosáhli specializovaného zpracování nezpracovaného obsahu, jako je filtrování diakritických znamének nebo rozpoznávání a zachování vzorů v řetězcích.

Další informace o konkrétních funkcích najdete v tématu [funkce služby Azure kognitivní hledání](search-features-list.md)

## <a name="how-to-get-started"></a>Jak začít

Ucelený průzkum základních funkcí vyhledávání se dá dosáhnout ve čtyřech krocích:

1. [**Vytvořte vyhledávací službu**](search-create-service-portal.md) ve sdílené bezplatné úrovni nebo na [Fakturovatelné úrovni](https://azure.microsoft.com/pricing/details/search/) pro vyhrazené prostředky používané pouze vaší službou. Všechny rychlé starty a kurzy se dají dokončit na sdílené službě.

1. Pomocí portálu, [REST API](/rest/api/searchservice/create-index), [sady .NET SDK](search-howto-dotnet-sdk.md)nebo jiné sady SDK [**vytvořte index vyhledávání**](search-what-is-an-index.md) . Schéma indexu definuje strukturu prohledávatelných obsahu.

1. [**Nahrajte obsah**](search-what-is-data-import.md) pomocí [modelu push](tutorial-optimize-indexing-push-api.md) pro vložení dokumentů JSON z libovolného zdroje nebo použijte [model "pull" (indexery)](search-indexer-overview.md) , pokud jsou zdrojová data v Azure.

1. [**Dotazování indexu**](search-query-overview.md) pomocí [Průzkumníka služby Search](search-explorer.md) na portálu, [REST API](search-get-started-rest.md), [.NET SDK](/dotnet/api/azure.search.documents.searchclient.search)nebo jiné sadě SDK.

V případě prvotního průzkumu začněte s [**Průvodcem importem dat**](search-get-started-portal.md) a integrovaným zdrojem dat Azure pro vytvoření, načtení a dotazování indexu v řádu minut.

Pokud chcete získat pomoc s komplexními nebo vlastními řešeními, [**obraťte se na partnera**](resource-partners-knowledge-mining.md) s hlubokými odbornými znalostmi kognitivní hledání technologie.

## <a name="compare-search-options"></a>Porovnání možností hledání

Zákazníci se často dotazují, jak Azure Kognitivní hledání porovnává s dalšími řešeními souvisejícími s vyhledáváním. Následující tabulka shrnuje klíčové rozdíly.

| Porovnaná alternativa | Klíčové rozdíly |
|-------------|-----------------|
| Microsoft Search | [Služba Microsoft Search](/microsoftsearch/overview-microsoft-search) je určena pro Microsoft 365 ověřených uživatelů, kteří potřebují zadávat dotazy na obsah ve službě SharePoint. Nabízí se jako předpřipravené vyhledávání, které povoluje a nakonfigurovali správci, a umožňuje tak přijímat externí obsah prostřednictvím konektorů od Microsoftu a jiných zdrojů. Pokud to popisuje váš scénář, pak Microsoft Search with Microsoft 365 je zajímavou možností, jak prozkoumat.<br/><br/>Azure Kognitivní hledání naproti tomu spouští dotazy přes index, který definujete, a to s daty a dokumenty, které vlastníte, často z různých zdrojů. Azure Kognitivní hledání obsahuje možnosti prohledávacího modulu pro některé zdroje dat Azure prostřednictvím [indexerů](search-indexer-overview.md), můžete ale do jediného, konsolidovaného prohledávatelnýho prostředku, vložit libovolný dokument JSON, který odpovídá schématu indexu. Kanál indexování můžete také přizpůsobit tak, aby zahrnoval strojové učení a lexikální analyzátory. Vzhledem k tomu, že Kognitivní hledání je sestaven jako součást modulu plug-in ve větších řešeních, můžete na libovolné platformě integrovat hledání do skoro libovolné aplikace.|
|Bing | [Rozhraní API Bingu pro vyhledávání na webu](../cognitive-services/bing-web-search/index.yml) hledá odpovídající zadané termíny v indexech na Bing.com. Indexy jsou vytvořené z webového obsahu HTML, XML a dalších typů na veřejných webech. [Vlastní vyhledávání Bingu](/azure/cognitive-services/bing-custom-search/) staví na stejných základech a nabízí stejnou technologii prohledávacího modulu pro typy webového obsahu vymezeného na jednotlivé weby.<br/><br/>V Kognitivní hledání můžete index definovat a naplnit. [Indexery](search-indexer-overview.md) můžete použít k procházení dat ve zdrojích dat Azure nebo k odeslání dokumentu JSON, který odpovídá indexu, do vaší vyhledávací služby. |
|Databázové hledání | Mnoho databázových platforem obsahuje integrované prostředí pro vyhledávání. SQL Server má [fulltextové vyhledávání](/sql/relational-databases/search/full-text-search). Cosmos DB a podobné technologie mají dotazovatelné indexy. Při vyhodnocování produktů, které kombinují vyhledávání a úložiště, může být obtížné určit, kterým směrem se vydat. Mnoho řešení používá: DBMS pro úložiště a Azure Kognitivní hledání pro specializované funkce hledání.<br/><br/>V porovnání s DBMS hledáním Azure Kognitivní hledání ukládá obsah z heterogenních zdrojů a nabízí specializované funkce pro zpracování textu, jako je například zpracování textu s jazykovou podporou (lemmatizátor, lemmatizátor nebo předzpracování, Word Forms) v [jazycích 56](/rest/api/searchservice/language-support). Podporuje také automatické opravy slov s pravopisnými chybami, [synonyma](/rest/api/searchservice/synonym-map-operations), [návrhy](/rest/api/searchservice/suggestions), [ovládací prvky pro určování skóre](/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [omezující vlastnosti](./search-filters-facets.md) a [vlastní tokenizaci](/rest/api/searchservice/custom-analyzers-in-azure-search). [Fulltextový vyhledávací](search-lucene-query-architecture.md) web ve službě Azure kognitivní hledání je postaven na Apache Lucene, což je oborová norma při načítání informací. Nicméně když Azure Kognitivní hledání uchovává data ve formě převráceného indexu, nejedná se o náhradu za skutečná úložiště dat a nedoporučujeme ji v této kapacitě použít. Další informace najdete v tomto [příspěvku fóra](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data). <br/><br/>Dalším důležitým bodem v této kategorii je využití prostředků. Indexování a určité operace dotazování jsou často velmi náročné na výpočetní operace. Přenesení zátěže vyhledávání z DBMS na vyhrazené řešení v cloudu zachová systémové prostředky pro zpracování transakcí. Díky přenesení vyhledávání na externí prostředky můžete také snadno upravit kapacitu tak, aby odpovídala množství dotazů.|
|Vyhrazené řešení pro vyhledávání | Za předpokladu, že jste se rozhodli pro vyhrazené vyhledávání s komplexními funkcemi, poslední kategorií je srovnání místních řešení a cloudové služby. Mnoho vyhledávacích technologií nabízí kontrolu nad indexováním a kanály dotazů, přístup k širší syntaxi dotazů a filtrování, řízení podle priority a relevance a funkce pro samořízené a inteligentní vyhledávání. <br/><br/>Cloudová služba je správnou volbou, pokud chcete řešení na klíč s minimální režií a údržbou a upravitelnou kapacitou. <br/><br/>V rámci cloudu nabízí několik poskytovatelů srovnatelné základní funkce s fulltextovým vyhledáváním, geografickým vyhledáváním a schopností zvládat určitou úroveň nejednoznačnosti v zadáních hledání. Obvykle jde o [specializovanou funkci](search-features-list.md) nebo snadnost a celkovou jednoduchost rozhraní API, nástrojů a správy, která určuje, co je nejvhodnější. |

Mezi poskytovateli cloudu je Azure Kognitivní hledání nejsilnější pro úlohy fulltextového vyhledávání v úložištích obsahu a databázích v Azure, a to pro aplikace, které jsou primárně závislé na hledání jak pro načítání informací, tak pro navigaci v obsahu. 

Mezi hlavní silné stránky patří:

+ Integrace dat Azure (prohledávací moduly) ve vrstvě indexování
+ Integrace privátních odkazů Azure pro podporu požadavků na zabezpečení z Internetu
+ Integrace se zpracováním AI, aby bylo možné v neprohledávatelných typech obsahu prohledávat text.
+ Jazykové a vlastní analýzy s analyzátory pro výkonné fulltextové vyhledávání v 56 jazycích
+ [Důležité funkce](search-features-list.md): bohatý dotazovací jazyk, optimalizace závažnosti, charakteristiky, automatické dokončování, synonyma, geografické vyhledávání a kompozice výsledků.
+ Škálování kapacity, spolehlivost a špičková dostupnost Azure

Mezi naše zákazníky dokázali využít nejširší škálu funkcí v Azure Kognitivní hledání zahrnuje online katalogy, obchodní programy a aplikace pro zjišťování dokumentů.

## <a name="watch-this-video"></a>Podívejte se na toto video

V tomto 15 minutovém videu Luis program Správce programů Cabrera zavádí Azure Kognitivní hledání.

>[!VIDEO https://www.youtube.com/embed/kOJU0YZodVk?version=3]