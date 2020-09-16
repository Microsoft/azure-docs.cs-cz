---
title: Úvod do služby Azure Cognitive Search
titleSuffix: Azure Cognitive Search
description: Azure Kognitivní hledání je plně spravovaná cloudová vyhledávací služba od Microsoftu. Přečtěte si o případech použití, vývojovém pracovním postupu, porovnání s ostatními produkty Microsoftu pro vyhledávání a o tom, jak začít.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 09/15/2020
ms.openlocfilehash: e4cee699bf18b340d0bb1cbe783bdedcca537db6
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2020
ms.locfileid: "90602944"
---
# <a name="what-is-azure-cognitive-search"></a>Co je kognitivní hledání Azure?

Azure Kognitivní hledání ([dříve označované jako "Azure Search"](whats-new.md#new-service-name)) je cloudová vyhledávací služba, která vývojářům poskytuje rozhraní API a nástroje pro vytváření bohatých funkcí vyhledávání přes soukromý, heterogenní obsah ve webových, mobilních a podnikových aplikacích.

Když vytváříte službu Kognitivní hledání, získáte indexování a dotazovací modul, trvalé úložiště vyhledávacích indexů, které vytvoříte a spravujete, a dotazovací jazyk pro vytváření jednoduchých a složitých dotazů. Vyhledávací služba se integruje s dalšími službami Azure ve formě *indexerů* , které automatizují přijímání a načítání dat ze zdrojů dat Azure, a *dovednosti* , které přidávají zpracování AI z Cognitive Services, jako je například analýza obrázků a textu.

![Architektura Azure Kognitivní hledání](media/search-what-is-azure-search/azure-search-diagram.svg "Architektura Azure Kognitivní hledání")

Služba vyhledávání v architektuře je mezi externími úložišti dat, která obsahují vaše data, a klientskou aplikací, která odesílá požadavky na dotazy a zpracovává odpovědi. Tyto dvě primární úlohy vyhledávací služby se *indexují* a *dotazují*.

Při indexování se přidá obsah do vaší vyhledávací služby a bude možné ho prohledávat. Interně se příchozí text zpracovává do tokenů a ukládá se do obrácených indexů pro rychlé porovnání. Schéma indexu určuje strukturu prohledávatelných obsahu. Při indexování máte možnost Přidat *dovednosti v rozpoznávání*, buď předdefinované od společnosti Microsoft, nebo vlastní dovednosti, které vytvoříte. Výsledná analýza a transformace mohou vytvořit nové informace a struktury, které dříve neexistovaly, a poskytnout tak vysoký výkon pro mnoho scénářů vyhledávání a vědomostí o dolování.

Po naplnění indexu pomocí prohledávatelných dat pošle klientská aplikace dotazy požadavky na vyhledávací službu a zpracuje odpovědi. Možnosti vyhledávání jsou definované ve vašem klientovi pomocí rozhraní API z Azure Kognitivní hledání a můžou zahrnovat vyladění relevance, automatické dokončování, porovnávání synonym, přibližné porovnávání, porovnávání vzorů, filtrování a řazení.

Funkce se zveřejňuje prostřednictvím jednoduchého rozhraní [REST API](/rest/api/searchservice/) nebo [.NET SDK](search-howto-dotnet-sdk.md), které zakrývá zákonitou složitost načítání informací. Můžete také použít Azure Portal pro správu služeb a správu obsahu, a to pomocí nástrojů pro vytváření prototypů a dotazování na indexy a dovednosti. Služba běží v cloudu, proto infrastrukturu a dostupnost spravuje Microsoft.

## <a name="when-to-use-cognitive-search"></a>Kdy použít Kognitivní hledání

Azure Kognitivní hledání je vhodné pro následující scénáře aplikací:

+ Konsolidace heterogenních typů obsahu do privátního uživatelsky definovaného vyhledávacího indexu. Index vyhledávání můžete naplnit pomocí datových proudů dokumentů JSON z libovolného zdroje. Pro podporované zdroje v Azure použijte *indexer* k automatizaci indexování. Hlavním důvodem pro použití Kognitivní hledání je kontrola schématu indexu a aktualizace plánu.

+ Snadná implementace funkcí souvisejících s vyhledáváním Rozhraní API pro vyhledávání zjednodušují vytváření dotazů, omezující navigaci, filtry (včetně geografického hledání), mapování synonym, automatické dokončování a optimalizaci závažnosti. Pomocí integrovaných funkcí můžete vyhovět očekáváním koncových uživatelů při hledání podobného komerčním vyhledávacím webům.

+ Nezpracovaný obsah je velký nerozlišený textový soubor nebo soubory obrázků nebo soubory aplikace uložené ve službě Azure Blob Storage nebo Cosmos DB. K identifikaci a extrakci textu, vytvoření struktury nebo vytváření nových informací, jako je například přeložený text nebo entity, můžete použít [odbornosti rozpoznávání](cognitive-search-concept-intro.md) během indexování.

+ Obsah vyžaduje jazykovou nebo vlastní analýzu textu. Pokud máte obsah, který není v angličtině, Azure Kognitivní hledání podporuje analyzátory Lucene i procesory přirozeného jazyka společnosti Microsoft. Můžete také nakonfigurovat analyzátory, abyste dosáhli specializovaného zpracování nezpracovaného obsahu, jako je filtrování diakritických znamének nebo rozpoznávání a zachování vzorů v řetězcích.

Další informace o konkrétních funkcích najdete v tématu [funkce služby Azure kognitivní hledání](search-features-list.md)

## <a name="how-to-use-cognitive-search"></a>Jak používat Kognitivní hledání

### <a name="step-1-provision-service"></a>1. krok: Zřízení služby

Můžete [vytvořit bezplatnou službu](search-create-service-portal.md) sdílenou s ostatními předplatiteli nebo [placenou úroveň](https://azure.microsoft.com/pricing/details/search/) , která vyhradí prostředky používané pouze vaší službou. Všechny rychlé starty a kurzy je možné absolvovat s bezplatnou službou.

U placených úrovní můžete škálovat službu ve dvou dimenzích k kalibraci reprodukce na základě produkčních požadavků:

+ Přidejte repliky, které rozšiřují kapacitu pro zpracování silných zátěží dotazů.
+ Přidání oddílů pro zvětšení úložiště pro další dokumenty

### <a name="step-2-create-an-index"></a>Krok 2: Vytvoření indexu

Definujte schéma indexu pro mapování tak, aby odráželo strukturu dokumentů, které chcete hledat, podobně jako pole v databázi. Index vyhledávání je specializovaná datová struktura, která je optimalizována pro rychlé provádění dotazů.

Je běžné [vytvořit schéma indexu v Azure Portal](search-what-is-an-index.md)nebo programově pomocí [sady .net SDK](search-howto-dotnet-sdk.md) nebo [REST API](/rest/api/searchservice/).

### <a name="step-3-load-data"></a>Krok 3: Načtení dat

Po definování indexu jste připravení nahrát obsah. Můžete použít model nabízení nebo vyžádání.

Model nabízených oznámení "odešle" Dokumenty JSON do indexu pomocí rozhraní API ze [sady SDK](search-howto-dotnet-sdk.md) nebo [REST](/rest/api/searchservice/addupdate-or-delete-documents). Externí datová sada může být prakticky libovolný zdroj dat, pokud jsou dokumenty ve formátu JSON.

Model vyžádané replikace načte data ze zdrojů v Azure a pošle je do indexu vyhledávání. Model pro vyžádání obsahu je implementován prostřednictvím [*indexerů*](/rest/api/searchservice/Indexer-operations) , které zjednodušují a automatizují aspekty příjmu dat, například připojení, čtení a serializaci dat. Mezi podporované zdroje dat patří Azure Cosmos DB, Azure SQL a Azure Storage.

### <a name="step-4-send-queries-and-handle-responses"></a>Krok 4: odeslání dotazů a zpracování odpovědí

Po naplnění indexu můžete [vydávat vyhledávací dotazy](search-query-overview.md) pro koncový bod služby pomocí jednoduchých požadavků HTTP s [REST API](/rest/api/searchservice/Search-Documents) nebo [sadou .NET SDK](/dotnet/api/microsoft.azure.search.idocumentsoperations).

Projděte si [vytvořením první vyhledávací aplikace](tutorial-csharp-create-first-app.md) , kterou vytvoříte a pak rozšíříte webovou stránku, která shromažďuje uživatelský vstup a zpracovává výsledky. K dotazování existujícího indexu můžete použít také [metodu post pro interaktivní volání REST](search-get-started-postman.md) nebo integrované [Průzkumník služby Search](search-explorer.md) v nástroji Azure Portal.

## <a name="how-it-compares"></a>Srovnání s ostatními řešeními

Zákazníci se často dotazují, jak Azure Kognitivní hledání porovnává s dalšími řešeními souvisejícími s vyhledáváním. Následující tabulka shrnuje klíčové rozdíly.

| Porovnaná alternativa | Klíčové rozdíly |
|-------------|-----------------|
|Bing | [Rozhraní API Bingu pro vyhledávání na webu](../cognitive-services/bing-web-search/index.yml) hledá odpovídající zadané termíny v indexech na Bing.com. Indexy jsou vytvořené z webového obsahu HTML, XML a dalších typů na veřejných webech. [Vlastní vyhledávání Bingu](/azure/cognitive-services/bing-custom-search/) staví na stejných základech a nabízí stejnou technologii prohledávacího modulu pro typy webového obsahu vymezeného na jednotlivé weby.<br/><br/>Azure Kognitivní hledání vyhledá index, který definujete, který je vyplněný daty a dokumenty, které vlastníte, často z různých zdrojů. Azure Kognitivní hledání má k dispozici možnosti prohledávacího modulu pro některé zdroje dat prostřednictvím [indexerů](search-indexer-overview.md), můžete ale do jediného, konsolidovaného vyhledávaného prostředku, vložit libovolný dokument JSON, který odpovídá schématu indexu. |
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