---
title: Úvod do kognitivního hledání Azure
titleSuffix: Azure Cognitive Search
description: Azure Kognitivní hledání je plně spravovaná hostovaná cloudová vyhledávací služba od Microsoftu. Přečtěte si popisy funkcí, vývojový pracovní postup, porovnání s ostatními produkty Microsoftu pro hledání a jak začít.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 01/06/2020
ms.openlocfilehash: 87295278f39f7e7097b1f4d1ce1c729a71599c9f
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2020
ms.locfileid: "84466217"
---
# <a name="what-is-azure-cognitive-search"></a>Co je kognitivní hledání Azure?

Azure Kognitivní hledání ([dřív označované jako "Azure Search"](whats-new.md#new-service-name)) je cloudové řešení hledání jako služby, které vývojářům poskytuje rozhraní API a nástroje pro přidání bohatých možností vyhledávání přes soukromý, heterogenní obsah ve webových, mobilních a podnikových aplikacích. Váš kód nebo nástroj vyvolá přijímání dat (indexování) pro vytvoření a načtení indexu. Volitelně můžete přidat dovednosti rozpoznávání pro použití procesů AI během indexování. Díky tomu můžete přidat nové informace a struktury užitečné pro hledání a další scénáře.

Na druhé straně služby váš kód aplikace vystavuje požadavky na dotazy a zpracovává odpovědi. Možnosti vyhledávání jsou ve vašem klientovi definované pomocí funkcí z Azure Kognitivní hledání. provádění dotazů se provádí prostřednictvím trvalého indexu, který vytvoříte, vlastníte a ukládáte do své služby.

![Architektura Azure Kognitivní hledání](media/search-what-is-azure-search/azure-search-diagram.svg "Architektura Azure Kognitivní hledání")

Funkce se zveřejňuje prostřednictvím jednoduchého rozhraní [REST API](/rest/api/searchservice/) nebo [.NET SDK](search-howto-dotnet-sdk.md), které zakrývá zákonitou složitost načítání informací. Kromě rozhraní API portál Azure poskytuje podporu správy a správy obsahu a nástroje pro vytváření prototypů a dotazů na indexy. Služba běží v cloudu, proto infrastrukturu a dostupnost spravuje Microsoft.

## <a name="when-to-use-azure-cognitive-search"></a>Kdy použít Azure Kognitivní hledání

Azure Kognitivní hledání je vhodné pro následující scénáře aplikací:

+ Konsolidace heterogenních typů obsahu do privátního, jednoduchého indexu s možností prohledávání. Dotazy jsou vždycky na indexu, který vytvoříte a načtete pomocí dokumentů, a index se vždycky nachází v cloudu ve službě Azure Kognitivní hledání. Index můžete naplnit pomocí datových proudů dokumentů JSON z libovolného zdroje nebo platformy. Pro obsah ve zdroji v Azure můžete také použít *indexer* , který načte data do indexu. Klíčovým důvodem pro používání služby Azure Kognitivní hledání je definice indexu a Správa/vlastnictví.

+ Nezpracovaný obsah je velký nerozlišený text, soubory obrázků nebo soubory aplikace, jako jsou typy obsahu Office ve zdroji dat Azure, jako je Azure Blob Storage nebo Cosmos DB. Můžete použít odbornosti rozpoznávání během indexování pro přidání struktury nebo extrakce z obrázků a souborů aplikace.

+ Snadná implementace funkcí souvisejících s vyhledáváním Rozhraní API pro Azure Kognitivní hledání zjednodušují vytváření dotazů, omezující navigaci, filtry (včetně geografického hledání), mapování synonym, dotazy typeahead a optimalizaci závažnosti. Pomocí integrovaných funkcí můžete vyhovět očekáváním koncových uživatelů při hledání podobného komerčním vyhledávacím webům.

+ Indexování nestrukturovaného textu nebo extrakce textu a informací ze souborů obrázků. Funkce [rozšíření AI](cognitive-search-concept-intro.md) v Azure kognitivní hledání přidává do kanálu INDEXOVÁNÍ zpracování AI. Některé běžné případy použití zahrnují rozpoznávání OCR přes naskenovaný dokument, rozpoznávání entit a extrakci klíčových frází pro velké dokumenty, rozpoznávání jazyka a překlad textu a analýzu mínění.

+ Jazykové požadavky jsou splněné pomocí vlastních a jazykových analyzátorů Azure Kognitivní hledání. Pokud máte obsah, který není v angličtině, Azure Kognitivní hledání podporuje analyzátory Lucene i procesory přirozeného jazyka společnosti Microsoft. Můžete také nakonfigurovat analyzátory, aby byly zajištěny specializované zpracování nezpracovaného obsahu, například filtrování diakritických znamének.

<a name="feature-drilldown"></a>

## <a name="feature-descriptions"></a>Popis funkcí

| Základní &nbsp; vyhledávání&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Funkce |
|-------------------|----------|
|Hledání v textu volného tvaru | [**Fulltextové vyhledávání**](search-lucene-query-architecture.md) je primární případ použití pro většinu aplikací založených na vyhledávání. Dotazy se dají formulovat pomocí podporované syntaxe. <br/><br/>[**Jednoduchá syntaxe dotazů**](query-simple-syntax.md) poskytuje logické operátory, operátory pro hledání slovních spojení, operátory pro přípony a operátory priority.<br/><br/>[**Syntaxe dotazů Lucene**](query-lucene-syntax.md) zahrnuje všechny operace v jednoduché syntaxi s rozšířeními pro přibližné vyhledávání, vyhledávání blízkých výrazů, zvyšování skóre termínu a regulární výrazy.|
| Relevance | [**Jednoduché bodování**](index-add-scoring-profiles.md) je klíčovou výhodou pro Azure kognitivní hledání. Profily vyhodnocování (bodovací profily) se používají k modelování relevance jako funkce hodnot v samotných dokumentech. Můžete například chtít, aby se novější produkty nebo produkty se slevou zobrazovaly ve výsledcích hledání na vyšších pozicích. Můžete také vytvořit profily vyhodnocování pomocí značek pro individuální vyhodnocování podle preferencí vyhledávání zákazníků, které sledujete a ukládáte odděleně. |
| Geografické vyhledávání | Azure Kognitivní hledání zpracovává, filtruje a zobrazuje geografické polohy. Umožňuje uživatelům zkoumat data podle blízkosti výsledku hledání k fyzické poloze. Pokud o tom chcete vědět víc, [pusťte si toto video](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) nebo [se podívejte na tuto ukázku](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). |
| Filtry a omezující vlastnosti | [**Fasetová navigace**](search-faceted-navigation.md) se aktivuje jedním parametrem dotazu. Azure Kognitivní hledání vrátí vlastnost s omezující navigační strukturou, kterou můžete použít jako kód za seznamem kategorií, pro filtrování samy sebe (například pro filtrování položek katalogu podle cenového rozsahu nebo značky). <br/><br/> [**Filtry**](query-odata-filter-orderby-syntax.md) se dají použít k začlenění fasetové navigace do uživatelského rozhraní aplikace, rozšíření možností formulování dotazu a filtrování na základě kritérií zadaných uživatelem nebo vývojářem. Filtry se vytvářejí pomocí syntaxe OData. |
| Funkce a možnosti pro uživatele | Na panelu hledání lze povolit [**Automatické dokončování**](search-autocomplete-tutorial.md) dotazů typu dotazování. <br/><br/>[**Návrhy hledání**](https://docs.microsoft.com/rest/api/searchservice/suggesters) fungují také s částečnými textovými vstupy na panelu hledání, ale výsledky jsou skutečné dokumenty ve vašem indexu, a ne termíny dotazu. <br/><br/>[**Synonyma**](search-synonyms.md) přidružují ekvivalentní termíny, které implicitně rozšiřují rozsah dotazu, aniž by uživatel musel zadávat alternativní termíny. <br/><br/>[**Zvýrazňování nalezených položek**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) použije formátování textu na odpovídající klíčové slovo ve výsledcích hledání. Můžete si zvolit pole, která budou vracet zvýrazněné fragmenty.<br/><br/>[**Řazení**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) se poskytuje pro několik polí prostřednictvím schématu indexu. Zapíná se pak v době dotazu jedním parametrem vyhledávání.<br/><br/> [**Stránkování**](search-pagination-page-layout.md) a omezení výsledků hledání je jednoduché s jemně vyladěným ovládacím prvkem, který Azure kognitivní hledání nabízí nad výsledky hledání.  <br/><br/>|

| &nbsp;Obohacení AI&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;       | Funkce |
|-------------------|----------|
|Zpracování AI při indexování | [**Rozšíření AI**](cognitive-search-concept-intro.md) pro analýzu obrázků a textu lze použít pro kanál indexování k extrakci textových informací z nezpracovaného obsahu. Mezi příklady [integrovaných dovedností](cognitive-search-predefined-skills.md) patří optické rozpoznávání znaků (umožňuje prohledávání naskenovaných obrázků JPEG), rozpoznávání entit (identifikuje organizaci, název nebo umístění) a rozpoznávání klíčových frází. Můžete také [naprogramovat vlastní dovednosti](cognitive-search-create-custom-skill-example.md), které připojíte ke kanálu. |
| Ukládání obohaceného obsahu pro analýzu a spotřebu ve scénářích bez vyhledávání | [**Znalostní báze Knowledge Store (Preview)**](knowledge-store-concept-intro.md) je rozšíření indexování založené na AI. Díky službě Azure Storage jako back-endu můžete ukládat rozšíření vytvořená během indexování. Tyto artefakty lze použít k návrhu lepšího dovednostií nebo k vytváření tvarů a strukturování z Amorphous nebo nejednoznačných dat. Můžete vytvořit projekce těchto struktur, které cílí na konkrétní úlohy nebo uživatele. Můžete také přímo analyzovat extrahovaná data nebo je načíst do jiných aplikací.<br/><br/> |
| Obsah uložený v mezipaměti | [**Přírůstkové obohacení (verze Preview)**](cognitive-search-incremental-indexing-conceptual.md) omezuje zpracování na pouze dokumenty, které byly změněny konkrétní úpravou v kanálu, a to pomocí obsahu uloženého v mezipaměti pro části kanálu, které se nemění. |

| &nbsp;Import/indexování dat | Funkce |
|----------------------------------|----------|
| Zdroje dat | Indexy Azure Kognitivní hledání přijímají data z libovolného zdroje za předpokladu, že se odešlou jako datová struktura JSON. <br/><br/> [**Indexery**](search-indexer-overview.md) automatizují přijímání dat pro podporované zdroje dat Azure a zpracování serializace JSON. Připojte se k [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-cosmosdb.md)nebo [úložišti objektů BLOB v Azure](search-howto-indexing-azure-blob-storage.md) a extrahujte prohledávatelný obsah v primárních úložištích dat. Indexery Azure Blob můžou *pronikat do dokumentů* za účelem [extrahování textu z hlavních formátů souborů](search-howto-indexing-azure-blob-storage.md), včetně dokumentů Microsoft Office, PDF a HTML. |
| Hierarchické a vnořené datové struktury | [**Komplexní typy**](search-howto-complex-data-types.md) a kolekce umožňují modelovat prakticky jakýkoli typ struktury JSON jako index služby Azure kognitivní hledání. Mohutnost 1: n se dá vyjádřit nativně prostřednictvím kolekcí, komplexních typů a kolekcí komplexních typů.|
| Lingvistická analýza | Analyzátory jsou komponenty, které slouží ke zpracování textu během operací indexování a vyhledávání. Existují dva typy. <br/><br/>[**Vlastní lexikální analyzátory**](index-add-custom-analyzers.md) se používají pro komplexní vyhledávací dotazy s využitím porovnávání výslovnosti a regulárních výrazů. <br/><br/>[**Jazykové analyzátory**](index-add-language-analyzers.md) od Lucene nebo Microsoftu se používají k inteligentnímu zpracování lingvistiky pro konkrétní jazyk včetně časů sloves, rodu, nepravidelného množného čísla podstatných jmen (například anglické „mouse“ oproti „mice“), rozkladu slov, dělení slov (pro jazyky bez mezer) a dalších možností. <br/><br/>|


| &nbsp;Úroveň platformy&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| Funkce |
|-------------------|----------|
| Nástroje pro vytváření prototypů a kontrolu | Na portálu můžete pomocí [**Průvodce importem dat**](search-import-data-portal.md) nakonfigurovat indexery, návrháře indexů, aby sestavil index, a [**Průzkumníka služby Search**](search-explorer.md), abyste mohli testovat dotazy a upřesňovat profily vyhodnocování. Můžete také otevřít index a zobrazit jeho schéma. |
| Monitorování a diagnostika | [**Umožněte funkcím monitorování**](search-monitor-usage.md) , abyste mohli přesáhnout metriky, které jsou vždy viditelné na portálu. Metriky pro dotazy za sekundu, latenci a omezování se zachycují a uvádějí na stránkách portálu bez další nezbytné konfigurace.|
| Šifrování na straně serveru | [**Šifrování spravované Microsoftem v klidovém režimu**](search-security-overview.md#encrypted-transmissions-and-storage) je integrované do interní vrstvy úložiště a je neodvolatelné. Volitelně můžete výchozí šifrování doplnit pomocí [**šifrovacích klíčů spravovaných zákazníkem**](search-security-manage-encryption-keys.md). Klíče, které vytvoříte a spravujete v Azure Key Vault slouží k šifrování indexů a mapování synonym v Azure Kognitivní hledání. |
| Infrastruktura | Díky **vysoce dostupné platformě** je prostředí vyhledávací služby nadmíru spolehlivé. V případě správné škály [Azure kognitivní hledání nabízí smlouvu SLA 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).<br/><br/> Azure Kognitivní hledání **plně spravované a škálovatelné** jako ucelené řešení, ale vyžaduje naprostou správu infrastruktury. Služba se dá přizpůsobit na míru vašim potřebám pomocí nastavení kapacity ve dvou dimenzích, aby mohla pracovat s větším úložištěm dokumentů, vyšším objemem dotazů nebo obojím.<br/><br/>|

## <a name="how-to-use-azure-cognitive-search"></a>Jak používat Azure Kognitivní hledání
### <a name="step-1-provision-service"></a>1. krok: Zřízení služby
Službu Azure Kognitivní hledání můžete zřídit v [Azure Portal](https://portal.azure.com/) nebo prostřednictvím [rozhraní API pro správu prostředků Azure](/rest/api/searchmanagement/). Můžete si zvolit bezplatnou službu sdílenou s jinými odběrateli nebo [placenou úroveň](https://azure.microsoft.com/pricing/details/search/), která vyhrazuje prostředky používané jenom vaší službou. U placených úrovní je možné službu škálovat ve dvou dimenzích: 

- Přidat repliky, které zvětší kapacitu pro zpracování velkých objemů dotazů   
- Přidat oddíly, které zvětší úložiště pro více dokumentů 

Díky oddělenému řízení úložiště dokumentů a propustnosti dotazů můžete kalibrovat prostředky podle produkčních požadavků.

### <a name="step-2-create-index"></a>2. krok: Vytvoření indexu
Než budete moct nahrát prohledávatelný obsah, musíte nejdřív definovat index služby Azure Kognitivní hledání. Index je jako databázová tabulka, která obsahuje vaše data a přijímá vyhledávací dotazy. Definujete schéma indexu do mapování tak, aby odráželo strukturu dokumentů, ve kterých chcete hledat, podobně jako pole v databázi.

Schéma se dá vytvořit na portálu Azure nebo programově pomocí [.NET SDK](search-howto-dotnet-sdk.md) nebo [REST API](/rest/api/searchservice/).

### <a name="step-3-load-data"></a>Krok 3: Načtení dat
Po definování indexu jste připravení nahrát obsah. Můžete použít model nabízení nebo vyžádání.

Model vyžádání načítá data z externích zdrojů dat. Podporují ho *indexery*, které zjednodušují a automatizují aspekty příjmu dat, jako je například připojení k datům a jejich čtení a serializace. [Indexery](/rest/api/searchservice/Indexer-operations) jsou k dispozici pro Azure Cosmos DB, Azure SQL Database, Azure Blob Storage a SQL Server hostované ve virtuálním počítači Azure. V indexeru můžete nakonfigurovat aktualizaci dat na vyžádání nebo podle plánu.

Model nabízení se zajišťuje prostřednictvím sady SDK nebo rozhraní REST API, která slouží k posílání aktualizovaných dokumentů do indexu. Data můžete nabízet prakticky z libovolné datové sady ve formátu JSON. Pokyny k načítání dat najdete v článcích [Přidávání, aktualizace a odstraňování dokumentů](/rest/api/searchservice/addupdate-or-delete-documents) nebo [Jak používat sadu .NET SDK](search-howto-dotnet-sdk.md).

### <a name="step-4-search"></a>4. krok: Vyhledávání
Po naplnění indexu můžete [vydávat vyhledávací dotazy](search-query-overview.md) pro koncový bod služby pomocí jednoduchých požadavků HTTP s [REST API](/rest/api/searchservice/Search-Documents) nebo [sadou .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations).

Projděte si [vytvořením první vyhledávací aplikace](tutorial-csharp-create-first-app.md) , kterou vytvoříte a pak rozšíříte webovou stránku, která shromažďuje uživatelský vstup a zpracovává výsledky. K dotazování existujícího indexu můžete použít také [metodu post pro interaktivní volání REST](search-get-started-postman.md) nebo integrované [Průzkumník služby Search](search-explorer.md) v nástroji Azure Portal.

## <a name="how-it-compares"></a>Srovnání s ostatními řešeními

Zákazníci se často dotazují, jak Azure Kognitivní hledání porovnává s dalšími řešeními souvisejícími s vyhledáváním. Následující tabulka shrnuje klíčové rozdíly.

| Porovnaná alternativa | Klíčové rozdíly |
|-------------|-----------------|
|Bing | [Rozhraní API Bingu pro vyhledávání na webu](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/) hledá odpovídající zadané termíny v indexech na Bing.com. Indexy jsou vytvořené z webového obsahu HTML, XML a dalších typů na veřejných webech. [Vlastní vyhledávání Bingu](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/) staví na stejných základech a nabízí stejnou technologii prohledávacího modulu pro typy webového obsahu vymezeného na jednotlivé weby.<br/><br/>Azure Kognitivní hledání vyhledá index, který definujete, který je vyplněný daty a dokumenty, které vlastníte, často z různých zdrojů. Azure Kognitivní hledání má k dispozici možnosti prohledávacího modulu pro některé zdroje dat prostřednictvím [indexerů](search-indexer-overview.md), můžete ale do jediného, konsolidovaného vyhledávaného prostředku, vložit libovolný dokument JSON, který odpovídá schématu indexu. |
|Databázové hledání | Mnoho databázových platforem obsahuje integrované prostředí pro vyhledávání. SQL Server má [fulltextové vyhledávání](https://docs.microsoft.com/sql/relational-databases/search/full-text-search). Cosmos DB a podobné technologie mají dotazovatelné indexy. Při vyhodnocování produktů, které kombinují vyhledávání a úložiště, může být obtížné určit, kterým směrem se vydat. Mnoho řešení používá: DBMS pro úložiště a Azure Kognitivní hledání pro specializované funkce hledání.<br/><br/>V porovnání s DBMS hledáním Azure Kognitivní hledání ukládá obsah z heterogenních zdrojů a nabízí specializované funkce pro zpracování textu, jako je například zpracování textu s jazykovou podporou (lemmatizátor, lemmatizátor nebo předzpracování, Word Forms) v [jazycích 56](https://docs.microsoft.com/rest/api/searchservice/language-support). Podporuje také automatické opravy slov s pravopisnými chybami, [synonyma](https://docs.microsoft.com/rest/api/searchservice/synonym-map-operations), [návrhy](https://docs.microsoft.com/rest/api/searchservice/suggestions), [ovládací prvky pro určování skóre](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [omezující vlastnosti](https://docs.microsoft.com/azure/search/search-filters-facets) a [vlastní tokenizaci](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search). [Fulltextový vyhledávací](search-lucene-query-architecture.md) web ve službě Azure kognitivní hledání je postaven na Apache Lucene, což je oborová norma při načítání informací. Zatímco Azure Kognitivní hledání uchovává data ve formě převráceného indexu, je zřídka náhrada za skutečná úložiště dat. Další informace najdete v tomto [příspěvku fóra](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data). <br/><br/>Dalším důležitým bodem v této kategorii je využití prostředků. Indexování a určité operace dotazování jsou často velmi náročné na výpočetní operace. Přenesení zátěže vyhledávání z DBMS na vyhrazené řešení v cloudu zachová systémové prostředky pro zpracování transakcí. Díky přenesení vyhledávání na externí prostředky můžete také snadno upravit kapacitu tak, aby odpovídala množství dotazů.|
|Vyhrazené řešení pro vyhledávání | Za předpokladu, že jste se rozhodli pro vyhrazené vyhledávání s komplexními funkcemi, poslední kategorií je srovnání místních řešení a cloudové služby. Mnoho vyhledávacích technologií nabízí kontrolu nad indexováním a kanály dotazů, přístup k širší syntaxi dotazů a filtrování, řízení podle priority a relevance a funkce pro samořízené a inteligentní vyhledávání. <br/><br/>Cloudová služba je správnou volbou, pokud chcete řešení na klíč s minimální režií a údržbou a upravitelnou kapacitou. <br/><br/>V rámci cloudu nabízí několik poskytovatelů srovnatelné základní funkce s fulltextovým vyhledáváním, geografickým vyhledáváním a schopností zvládat určitou úroveň nejednoznačnosti v zadáních hledání. Obvykle jde o [specializovanou funkci](#feature-drilldown) nebo snadnost a celkovou jednoduchost rozhraní API, nástrojů a správy, která určuje, co je nejvhodnější. |

Mezi poskytovateli cloudu je Azure Kognitivní hledání nejsilnější pro úlohy fulltextového vyhledávání v úložištích obsahu a databázích v Azure, a to pro aplikace, které jsou primárně závislé na hledání jak pro načítání informací, tak pro navigaci v obsahu. 

Mezi hlavní silné stránky patří:

+ Integrace dat Azure (prohledávací moduly) ve vrstvě indexování
+ Portál Azure Portal pro centrální správu
+ Škálování kapacity, spolehlivost a špičková dostupnost Azure
+ Zpracování souborů AI z nezpracovaných dat, aby bylo možné je prohledávat, včetně textu z obrázků nebo hledání vzorců v nestrukturovaném obsahu.
+ Jazykové a vlastní analýzy s analyzátory pro výkonné fulltextové vyhledávání v 56 jazycích
+ [Základní funkce společné pro aplikace zaměřené na vyhledávání](#feature-drilldown): vyhodnocování (bodování), používání faset, návrhy, synonyma, geografické vyhledávání a další.

> [!Note]
> Zdroje dat mimo Azure jsou plně podporované, ale závisí na metodice nabízení s vyššími nároky na kód než indexery. Pomocí rozhraní API můžete přesměrovat všechny kolekce dokumentů JSON do indexu služby Azure Kognitivní hledání.

Mezi naše zákazníky dokázali využít nejširší škálu funkcí v Azure Kognitivní hledání zahrnuje online katalogy, obchodní programy a aplikace pro zjišťování dokumentů.

## <a name="rest-api--net-sdk"></a>REST API | .NET SDK

I když je možné na portálu provádět mnoho úloh, Azure Kognitivní hledání je určený pro vývojáře, kteří chtějí integrovat funkce vyhledávání do stávajících aplikací. Dostupná jsou následující programovací rozhraní.

|Platforma |Description |
|-----|------------|
|[REST](/rest/api/searchservice/) | Příkazy HTTP podporované každou programovací platformou a jazykem, včetně Xamarinu, Javy a JavaScriptu.|
|[.NET SDK](search-howto-dotnet-sdk.md) | Obálka .NET pro REST API nabízí efektivní kódování v jazyce C# a dalších jazycích spravovaného kódu určených pro rozhraní .NET Framework. |

## <a name="free-trial"></a>Bezplatná zkušební verze
Předplatitelé Azure můžou [zřídit službu na úrovni Free](search-create-service-portal.md).

Pokud nejste předplatitel, můžete si [zdarma otevřít účet Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). Budete dostávat kredity za vyzkoušení placených služeb Azure. Po jejich vyčerpání si můžete účet ponechat a používat [bezplatné služby Azure](https://azure.microsoft.com/free/). Nikdy vám nebudeme účtovat žádné poplatky, pokud si sami nezměníte nastavení a nezačnete používat placené služby.

Jako druhou možnost si můžete [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): Vaše předplatné MSDN vám každý měsíc dává kredity, které můžete použít k placení za služby Azure. 

## <a name="how-to-get-started"></a>Jak začít

1. Vytvořte si [bezplatnou službu](search-create-service-portal.md). Všechny rychlé starty a kurzy je možné absolvovat s bezplatnou službou.

2. Projděte si [kurz použití integrovaných nástrojů pro indexování a dotazy](search-get-started-portal.md). Seznamte se s důležitými koncepty a informacemi, které portál nabízí.

3. Pokračujte s kódem s využitím .NET nebo rozhraní REST API:

   + [Jak používat sadu .NET SDK](search-howto-dotnet-sdk.md) představuje hlavní pracovní postup ve spravovaném kódu.  
   + [Začínáme s rozhraním API REST](https://github.com/Azure-Samples/search-rest-api-getting-started) ukazuje stejný postup pomocí rozhraní REST API. Tento rychlý Start můžete použít také k volání rozhraní REST API z post nebo Fiddler: [Prozkoumejte rozhraní REST API pro Azure kognitivní hledání](search-get-started-postman.md).

## <a name="watch-this-video"></a>Podívejte se na toto video

Vyhledávací moduly jsou běžnými prostředky načítání informací v mobilních aplikacích, na webu a ve firemních úložištích dat. Azure Kognitivní hledání poskytuje nástroje pro vytváření vyhledávacího prostředí podobného těm na velkých komerčních webech.

V tomto 15 minutovém videu Luis program Správce programů Cabrera zavádí Azure Kognitivní hledání. 

>[!VIDEO https://www.youtube.com/embed/kOJU0YZodVk?version=3]