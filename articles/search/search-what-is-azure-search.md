---
title: Úvod do služby Azure Cognitive Search
titleSuffix: Azure Cognitive Search
description: Azure Kognitivní hledání je plně spravovaná cloudová vyhledávací služba od Microsoftu. Přečtěte si o případech použití, vývojovém pracovním postupu, porovnání s ostatními produkty Microsoftu pro vyhledávání a o tom, jak začít.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 11/24/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: 01f61eac31c388cd0bf8a857beb7a10944187c6b
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97027896"
---
# <a name="what-is-azure-cognitive-search"></a>Co je Azure Cognitive Search?

Azure Kognitivní hledání ([dříve označované jako "Azure Search"](whats-new.md#new-service-name)) je cloudová vyhledávací služba, která vývojářům poskytuje rozhraní API a nástroje pro vytváření bohatých funkcí vyhledávání přes soukromý, heterogenní obsah ve webových, mobilních a podnikových aplikacích. 

Když vytváříte službu Kognitivní hledání, získáte:

+ Vyhledávací modul, který provádí indexování a provádění dotazů
+ Trvalé úložiště vyhledávacích indexů, které vytvoříte a spravujete
+ Dotazovací jazyk pro vytváření jednoduchých a složitých dotazů
+ Analýzy na základě AI, vytváření prohledávatelných obsahu z imagí, nezpracovaný text, soubory aplikací
+ Integrace se službou Azure data prostřednictvím indexerů hledání, automatizace importu a aktualizace dat

Služba vyhledávání v architektuře je mezi externími úložišti dat, která obsahují vaše neindexovaná data, a klientskou aplikací, která odesílá dotazy do indexu vyhledávání a zpracovává odpověď.

![Architektura Azure Kognitivní hledání](media/search-what-is-azure-search/azure-search-diagram.svg "Architektura Azure Kognitivní hledání")

Kromě toho se služba vyhledávání integruje s dalšími službami Azure ve formě *indexerů* , které automatizují přijímání a načítání dat ze zdrojů dat Azure, a *dovednosti* , které zahrnují spotřební AI z Cognitive Services, jako je například analýza obrázků a textu nebo vlastní AI, které vytvoříte v Azure Machine Learning nebo zabalíte do Azure Functions.

V samotné službě Search se tyto dvě primární úlohy *indexují* a *dotazují*. 

+ Indexování přináší do vyhledávací služby text a umožňuje prohledávat. Interně se příchozí text zpracovává do tokenů a ukládá se do obrácených indexů pro rychlé prověřování. 

  V rámci indexování máte možnost Přidat *rozšíření AI* prostřednictvím [dovedností rozpoznávání](cognitive-search-working-with-skillsets.md), a to buď z předdefinovaných společností Microsoft, nebo z vlastních dovedností, které vytvoříte. Následná analýza a transformace můžou mít za následek nové informace a struktury, které dříve neexistovaly, a poskytují tak vysoký výkon pro mnoho scénářů vyhledávání a vědomostí o dolování.

+ Po naplnění indexu pomocí prohledávatelných dat pošle klientská aplikace dotazy požadavky na vyhledávací službu a zpracuje odpovědi. Všechny provádění dotazů se provádí pomocí vyhledávacího indexu, který vytvoříte, vlastníte a uložíte v rámci služby. V klientské aplikaci jsou možnosti vyhledávání definované pomocí rozhraní API z Azure Kognitivní hledání a můžou zahrnovat vyladění relevance, automatické dokončování, porovnávání synonym, přibližné porovnávání, porovnávání vzorů, filtrování a řazení.

Funkce se zveřejňuje prostřednictvím jednoduchého rozhraní [REST API](/rest/api/searchservice/) nebo [.NET SDK](search-howto-dotnet-sdk.md), které zakrývá zákonitou složitost načítání informací. Můžete také použít Azure Portal pro správu služeb a správu obsahu, a to pomocí nástrojů pro vytváření prototypů a dotazování na indexy a dovednosti. Služba běží v cloudu, proto infrastrukturu a dostupnost spravuje Microsoft.

## <a name="why-use-cognitive-search"></a>Proč použít Kognitivní hledání

Azure Kognitivní hledání je vhodným způsobem pro následující scénáře aplikací:

+ Konsoliduje heterogenní obsah do privátního uživatelsky definovaného vyhledávacího indexu. Index vyhledávání můžete naplnit pomocí datových proudů dokumentů JSON z libovolného zdroje. Pro podporované zdroje v Azure použijte *indexer* k automatizaci indexování. Hlavním důvodem pro použití Kognitivní hledání je kontrola schématu indexu a aktualizace plánu.

+ Snadná implementace funkcí souvisejících s vyhledáváním Rozhraní API pro vyhledávání zjednodušují vytváření dotazů, omezující navigaci, filtry (včetně geografického hledání), mapování synonym, automatické dokončování a optimalizaci závažnosti. Pomocí integrovaných funkcí můžete vyhovět očekáváním koncových uživatelů při hledání podobného komerčním vyhledávacím webům.

+ Nezpracovaný obsah je velký nerozlišený textový soubor nebo soubory obrázků nebo soubory aplikace uložené ve službě Azure Blob Storage nebo Cosmos DB. K identifikaci a extrakci textu, vytvoření struktury nebo vytváření nových informací, jako je například přeložený text nebo entity, můžete použít [odbornosti rozpoznávání](cognitive-search-concept-intro.md) během indexování.

+ Obsah vyžaduje jazykovou nebo vlastní analýzu textu. Pokud máte obsah, který není v angličtině, Azure Kognitivní hledání podporuje analyzátory Lucene i procesory přirozeného jazyka společnosti Microsoft. Můžete také nakonfigurovat analyzátory, abyste dosáhli specializovaného zpracování nezpracovaného obsahu, jako je filtrování diakritických znamének nebo rozpoznávání a zachování vzorů v řetězcích.

Další informace o konkrétních funkcích najdete v tématu [funkce služby Azure kognitivní hledání](search-features-list.md)

## <a name="how-to-get-started"></a>Jak začít

Ucelený průzkum základních funkcí vyhledávání se dá dosáhnout ve čtyřech krocích:

1. [**Vytvořte vyhledávací službu**](search-create-service-portal.md) na úrovni Free sdílené s ostatními předplatiteli nebo [placená úroveň](https://azure.microsoft.com/pricing/details/search/) vyhrazených prostředků, které používá jenom vaše služba. Všechny rychlé starty a kurzy je možné absolvovat s bezplatnou službou.

1. Pomocí portálu [**vytvořte index vyhledávání**](search-what-is-an-index.md) [REST API](/rest/api/searchservice/create-index). [Sada .NET SDK](search-howto-dotnet-sdk.md)nebo jiná sada SDK. Schéma indexu definuje strukturu prohledávatelných obsahu.

1. [**Nahrajte obsah**](search-what-is-data-import.md) do indexu. Použijte [model "push"](tutorial-optimize-indexing-push-api.md) pro vložení dokumentů JSON z libovolného zdroje nebo použijte [model "pull" (indexery)](search-indexer-overview.md) , pokud jsou zdrojová data v Azure.

1. [**Dotazování indexu**](search-query-overview.md) pomocí [Průzkumníka služby Search](search-explorer.md) na portálu, [REST API](search-get-started-rest.md), [.NET SDK](/dotnet/api/azure.search.documents.searchclient.search)nebo jiné sadě SDK.

> [!TIP]
> Minimalizujte kroky tím, že začnete s [**Průvodcem importem dat**](search-get-started-portal.md) a zdrojem dat Azure pro vytvoření, načtení a dotazování indexu v řádu minut.

## <a name="how-it-compares"></a>Srovnání s ostatními řešeními

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
+ Portál Azure Portal pro centrální správu
+ Škálování kapacity, spolehlivost a špičková dostupnost Azure
+ Zpracování souborů AI z nezpracovaných dat, aby bylo možné je prohledávat, včetně textu z obrázků nebo hledání vzorců v nestrukturovaném obsahu.
+ Jazykové a vlastní analýzy s analyzátory pro výkonné fulltextové vyhledávání v 56 jazycích
+ [Základní funkce společné pro aplikace zaměřené na vyhledávání](search-features-list.md): vyhodnocování (bodování), používání faset, návrhy, synonyma, geografické vyhledávání a další.

Mezi naše zákazníky dokázali využít nejširší škálu funkcí v Azure Kognitivní hledání zahrnuje online katalogy, obchodní programy a aplikace pro zjišťování dokumentů.

## <a name="watch-this-video"></a>Podívejte se na toto video

V tomto 15 minutovém videu Luis program Správce programů Cabrera zavádí Azure Kognitivní hledání.

>[!VIDEO https://www.youtube.com/embed/kOJU0YZodVk?version=3]