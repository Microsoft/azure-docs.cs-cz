---
title: Úvod do kognitivního hledání Azure
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search je plně spravovaná hostovaná cloudová vyhledávací služba od Microsoftu. Přečtěte si popisy funkcí, pracovní postup vývoje, porovnání s jinými vyhledávacími produkty společnosti Microsoft a postup, jak začít.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 01/06/2020
ms.openlocfilehash: fee7c8eb73fe0bb7c9fd0bd9de9aa57bd8c40215
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77580647"
---
# <a name="what-is-azure-cognitive-search"></a>Co je kognitivní hledání Azure?

Azure Cognitive Search[(dříve známé jako "Azure Search"](whats-new.md#new-service-name)) je cloudové řešení pro vyhledávání jako služba, které vývojářům poskytuje rozhraní API a nástroje pro přidání bohatého prostředí pro vyhledávání přes soukromý, heterogenní obsah ve webových, mobilních a podnikových aplikacích. Váš kód nebo nástroj vyvolá požití dat (indexování) k vytvoření a načtení indexu. Volitelně můžete přidat kognitivní dovednosti pro použití procesů AI během indexování. Tím můžete přidat nové informace a struktury užitečné pro hledání a další scénáře.

Na druhé straně služby kód aplikace vydává požadavky na dotazy a zpracovává odpovědi. Prostředí vyhledávání je definováno ve vašem klientovi pomocí funkcí z Azure Cognitive Search, s prováděním dotazu přes trvalý index, který vytvoříte, vlastníte a uložíte ve vaší službě.

![Architektura Azure cognitive search](media/search-what-is-azure-search/azure-search-diagram.svg "Architektura Azure cognitive search")

Funkce se zveřejňuje prostřednictvím jednoduchého rozhraní [REST API](/rest/api/searchservice/) nebo [.NET SDK](search-howto-dotnet-sdk.md), které zakrývá zákonitou složitost načítání informací. Kromě rozhraní API portál Azure poskytuje podporu správy a správy obsahu a nástroje pro vytváření prototypů a dotazů na indexy. Služba běží v cloudu, proto infrastrukturu a dostupnost spravuje Microsoft.

## <a name="when-to-use-azure-cognitive-search"></a>Kdy použít Azure Cognitive Search

Azure Cognitive Search je vhodný pro následující scénáře aplikací:

+ Konsolidace heterogenních typů obsahu do soukromého, jediného indexu, který lze prohledávat. Dotazy jsou vždy přes index, který vytvoříte a načtete s dokumenty a index je vždy umístěn v cloudu ve vaší službě Azure Cognitive Search. Index můžete naplnit datovými proudy dokumentů JSON z libovolného zdroje nebo platformy. Případně pro obsah pocházející z Azure, můžete použít *indexer* utkat data do indexu. Definice a správa a vlastnictví indexu je klíčovým důvodem pro použití Azure Cognitive Search.

+ Nezpracovaný obsah je velký nediferencovaný text, soubory bitové kopie nebo soubory aplikací, jako jsou typy obsahu Office na zdroji dat Azure, jako je úložiště objektů blob Azure nebo Cosmos DB. Můžete použít kognitivní dovednosti během indexování přidat strukturu nebo extrahovat význam z obrazu a aplikačních souborů.

+ Snadná implementace funkcí souvisejících s vyhledáváním. Azure Cognitive Search API zjednodušit návrhy dotazů, fazetované navigace, filtry (včetně geografického prostorového vyhledávání), mapování synonym, dotazy dopředného psaní a optimalizace relevance. Pomocí vestavěných funkcí můžete uspokojit očekávání koncových uživatelů pro vyhledávání podobné komerčním webovým vyhledávačům.

+ Indexování nestrukturovaného textu nebo extrahování textu a informací z obrazových souborů. Funkce [obohacení ai](cognitive-search-concept-intro.md) Azure Cognitive Search přidá zpracování AI do kanálu indexování. Mezi běžné případy použití patří rozpoznávání OCR nad naskenovaným dokumentem, rozpoznávání mů e se konstitucí entit a extrakce klíčových frází nad velkými dokumenty, detekce jazyka a překlad textu a analýza mínění.

+ Jazykové požadavky splněny pomocí vlastnía jazykové analyzátory Azure Cognitive Search. Pokud máte neanglický obsah, Azure Cognitive Search podporuje analyzátory Lucene i procesory přirozeného jazyka Microsoftu. Analyzátory můžete také nakonfigurovat tak, aby bylo dosaženo specializovaného zpracování nezpracovaného obsahu, například filtrování diakritiky.

<a name="feature-drilldown"></a>

## <a name="feature-descriptions"></a>Popis funkcí

| Základní&nbsp;vyhledávání&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Funkce |
|-------------------|----------|
|Hledání v textu volného tvaru | [**Fulltextové vyhledávání**](search-lucene-query-architecture.md) je primární případ použití pro většinu aplikací založených na vyhledávání. Dotazy se dají formulovat pomocí podporované syntaxe. <br/><br/>[**Jednoduchá syntaxe dotazů**](query-simple-syntax.md) poskytuje logické operátory, operátory pro hledání slovních spojení, operátory pro přípony a operátory priority.<br/><br/>[**Syntaxe dotazů Lucene**](query-lucene-syntax.md) zahrnuje všechny operace v jednoduché syntaxi s rozšířeními pro přibližné vyhledávání, vyhledávání blízkých výrazů, zvyšování skóre termínu a regulární výrazy.|
| Relevance | [**Jednoduché bodování**](index-add-scoring-profiles.md) je klíčovou výhodou Azure Cognitive Search. Profily vyhodnocování (bodovací profily) se používají k modelování relevance jako funkce hodnot v samotných dokumentech. Můžete například chtít, aby se novější produkty nebo produkty se slevou zobrazovaly ve výsledcích hledání na vyšších pozicích. Můžete také vytvořit profily vyhodnocování pomocí značek pro individuální vyhodnocování podle preferencí vyhledávání zákazníků, které sledujete a ukládáte odděleně. |
| Geografické vyhledávání | Azure Cognitive Search zpracovává, filtruje a zobrazuje geografická umístění. Umožňuje uživatelům zkoumat data podle blízkosti výsledku hledání k fyzické poloze. Pokud o tom chcete vědět víc, [pusťte si toto video](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) nebo [se podívejte na tuto ukázku](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). |
| Filtry a omezující aspekty | [**Fasetová navigace**](search-faceted-navigation.md) se aktivuje jedním parametrem dotazu. Azure Cognitive Search vrátí fazetovaný navigační struktura, kterou můžete použít jako kód za seznam kategorií, pro filtrování s vlastním řízením (například pro filtrování položek katalogu podle cenového rozsahu nebo značky). <br/><br/> [**Filtry**](query-odata-filter-orderby-syntax.md) se dají použít k začlenění fasetové navigace do uživatelského rozhraní aplikace, rozšíření možností formulování dotazu a filtrování na základě kritérií zadaných uživatelem nebo vývojářem. Filtry se vytvářejí pomocí syntaxe OData. |
| Funkce a možnosti pro uživatele | [**Automatické dokončování**](search-autocomplete-tutorial.md) lze povolit pro dotazy typu dopředu na vyhledávacím panelu. <br/><br/>[**Návrhy hledání**](https://docs.microsoft.com/rest/api/searchservice/suggesters) fungují také s částečnými textovými vstupy na panelu hledání, ale výsledky jsou skutečné dokumenty ve vašem indexu, a ne termíny dotazu. <br/><br/>[**Synonyma**](search-synonyms.md) přidružují ekvivalentní termíny, které implicitně rozšiřují rozsah dotazu, aniž by uživatel musel zadávat alternativní termíny. <br/><br/>[**Zvýrazňování nalezených položek**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) použije formátování textu na odpovídající klíčové slovo ve výsledcích hledání. Můžete si zvolit pole, která budou vracet zvýrazněné fragmenty.<br/><br/>[**Řazení**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) se poskytuje pro několik polí prostřednictvím schématu indexu. Zapíná se pak v době dotazu jedním parametrem vyhledávání.<br/><br/> [**Stránkování**](search-pagination-page-layout.md) a omezování výsledků hledání je jednoduché díky jemně vyladěné ovládací mu, které azure cognitive search nabízí přes výsledky hledání.  <br/><br/>|

| Obohacení&nbsp;umělou a umělou a indič&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;       | Funkce |
|-------------------|----------|
|Zpracování umělou ai během indexování | [**Obohacení ai**](cognitive-search-concept-intro.md) pro analýzu obrazu a textu lze použít na indexovací kanál k extrahování textových informací z nezpracovaného obsahu. Mezi příklady [integrovaných dovedností](cognitive-search-predefined-skills.md) patří optické rozpoznávání znaků (umožňuje prohledávání naskenovaných obrázků JPEG), rozpoznávání entit (identifikuje organizaci, název nebo umístění) a rozpoznávání klíčových frází. Můžete také [naprogramovat vlastní dovednosti](cognitive-search-create-custom-skill-example.md), které připojíte ke kanálu. |
| Ukládání obohaceného obsahu pro analýzu a spotřebu ve scénářích bez vyhledávání | [**Úložiště znalostí (preview)**](knowledge-store-concept-intro.md) je rozšíření indexování založené na AI. S úložiště Azure jako back-end, můžete uložit obohacení vytvořené během indexování. Tyto artefakty lze použít k návrhu lepší dovednosti nebo vytvořit tvar a strukturu z amorfní nebo nejednoznačné data. Můžete vytvořit projekce těchto struktur, které cílí na konkrétní úlohy nebo uživatele. Extrahovaná data můžete také přímo analyzovat nebo je načíst do jiných aplikací.<br/><br/> |
| Obsah uložený v mezipaměti | [**Přírůstkové obohacení (náhled)**](cognitive-search-incremental-indexing-conceptual.md) omezuje zpracování pouze na dokumenty, které jsou změněny konkrétní úpravy kanálu, pomocí obsahu uloženého v mezipaměti pro části kanálu, které se nemění. |

| Import/indexování dat&nbsp; | Funkce |
|----------------------------------|----------|
| Zdroje dat | Azure Cognitive Search indexy přijímat data z libovolného zdroje, za předpokladu, že je odeslána jako struktura dat JSON. <br/><br/> [**Indexery**](search-indexer-overview.md) automatizují ingestování dat pro podporované zdroje dat Azure a zpracovávají serializaci JSON. Připojte se k [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), Azure [Cosmos DB](search-howto-index-cosmosdb.md)nebo azure [blob úložiště](search-howto-indexing-azure-blob-storage.md) extrahovat prohledávatelný obsah v úložišti primárních dat. Indexery Azure Blob můžou *pronikat do dokumentů* za účelem [extrahování textu z hlavních formátů souborů](search-howto-indexing-azure-blob-storage.md), včetně dokumentů Microsoft Office, PDF a HTML. |
| Hierarchické a vnořené datové struktury | [**Komplexní typy**](search-howto-complex-data-types.md) a kolekce umožňují modelovat prakticky jakýkoli typ struktury JSON jako index Azure Cognitive Search. Mohutnost 1:N a N může být nativně vyjádřena prostřednictvím kolekcí, složitých typů a kolekcí složitých typů.|
| Lingvistická analýza | Analyzátory jsou komponenty, které slouží ke zpracování textu během operací indexování a vyhledávání. Existují dva typy. <br/><br/>[**Vlastní lexikální analyzátory**](index-add-custom-analyzers.md) se používají pro komplexní vyhledávací dotazy s využitím porovnávání výslovnosti a regulárních výrazů. <br/><br/>[**Jazykové analyzátory**](index-add-language-analyzers.md) od Lucene nebo Microsoftu se používají k inteligentnímu zpracování lingvistiky pro konkrétní jazyk včetně časů sloves, rodu, nepravidelného množného čísla podstatných jmen (například anglické „mouse“ oproti „mice“), rozkladu slov, dělení slov (pro jazyky bez mezer) a dalších možností. <br/><br/>|


| Úroveň&nbsp;platformy&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| Funkce |
|-------------------|----------|
| Nástroje pro vytváření prototypů a kontrolu | Na portálu můžete pomocí [**Průvodce importem dat**](search-import-data-portal.md) nakonfigurovat indexery, návrháře indexů, aby sestavil index, a [**Průzkumníka služby Search**](search-explorer.md), abyste mohli testovat dotazy a upřesňovat profily vyhodnocování. Můžete také otevřít index a zobrazit jeho schéma. |
| Monitorování a diagnostika | [**Umožněte funkcím monitorování,**](search-monitor-usage.md) abyste překonali metriky, které jsou vždy viditelné na portálu. Metriky pro dotazy za sekundu, latenci a omezování se zachycují a uvádějí na stránkách portálu bez další nezbytné konfigurace.|
| Šifrování na straně serveru | [**Šifrování v klidovém stavu spravované společností Microsoft**](search-security-overview.md#encrypted-transmission-and-storage) je integrováno do vrstvy vnitřního úložiště a je neodvolatelné. Volitelně můžete doplnit výchozí šifrování pomocí [**šifrovacích klíčů spravovaných zákazníkem**](search-security-manage-encryption-keys.md). Klíče, které vytvoříte a spravujete v Azure Key Vault se používají k šifrování indexů a synonym mapy v Azure Cognitive Search. |
| Infrastruktura | Díky **vysoce dostupné platformě** je prostředí vyhledávací služby nadmíru spolehlivé. Při správném škálování [azure cognitive search nabízí 99,9% sla](https://azure.microsoft.com/support/legal/sla/search/v1_0/).<br/><br/> Azure Cognitive **Search, plně spravované a škálovatelné** jako komplexní řešení, nevyžaduje absolutně žádnou správu infrastruktury. Služba se dá přizpůsobit na míru vašim potřebám pomocí nastavení kapacity ve dvou dimenzích, aby mohla pracovat s větším úložištěm dokumentů, vyšším objemem dotazů nebo obojím.<br/><br/>|

## <a name="how-to-use-azure-cognitive-search"></a>Jak používat Azure Cognitive Search
### <a name="step-1-provision-service"></a>1. krok: Zřízení služby
Službu Azure Cognitive Search můžete zřídit na [webu Azure Portal](https://portal.azure.com/) nebo prostřednictvím rozhraní Azure Resource Management [API](/rest/api/searchmanagement/). Můžete si zvolit bezplatnou službu sdílenou s jinými odběrateli nebo [placenou úroveň](https://azure.microsoft.com/pricing/details/search/), která vyhrazuje prostředky používané jenom vaší službou. U placených úrovní je možné službu škálovat ve dvou dimenzích: 

- Přidat repliky, které zvětší kapacitu pro zpracování velkých objemů dotazů   
- Přidat oddíly, které zvětší úložiště pro více dokumentů 

Díky oddělenému řízení úložiště dokumentů a propustnosti dotazů můžete kalibrovat prostředky podle produkčních požadavků.

### <a name="step-2-create-index"></a>2. krok: Vytvoření indexu
Než budete moct nahrát prohledávatelný obsah, musíte nejdřív definovat index Azure Cognitive Search. Index je jako databázová tabulka, která obsahuje vaše data a přijímá vyhledávací dotazy. Definujete schéma indexu do mapování tak, aby odráželo strukturu dokumentů, ve kterých chcete hledat, podobně jako pole v databázi.

Schéma se dá vytvořit na portálu Azure nebo programově pomocí [.NET SDK](search-howto-dotnet-sdk.md) nebo [REST API](/rest/api/searchservice/).

### <a name="step-3-load-data"></a>Krok 3: Načtení dat
Po definování indexu jste připravení nahrát obsah. Můžete použít model nabízení nebo vyžádání.

Model vyžádání načítá data z externích zdrojů dat. Podporují ho *indexery*, které zjednodušují a automatizují aspekty příjmu dat, jako je například připojení k datům a jejich čtení a serializace. [Indexery](/rest/api/searchservice/Indexer-operations) jsou k dispozici pro Azure Cosmos DB, Azure SQL Database, Azure Blob Storage a SQL Server hostované ve virtuálním počítači Azure. V indexeru můžete nakonfigurovat aktualizaci dat na vyžádání nebo podle plánu.

Model nabízení se zajišťuje prostřednictvím sady SDK nebo rozhraní REST API, která slouží k posílání aktualizovaných dokumentů do indexu. Data můžete nabízet prakticky z libovolné datové sady ve formátu JSON. Pokyny k načítání dat najdete v článcích [Přidávání, aktualizace a odstraňování dokumentů](/rest/api/searchservice/addupdate-or-delete-documents) nebo [Jak používat sadu .NET SDK](search-howto-dotnet-sdk.md).

### <a name="step-4-search"></a>4. krok: Vyhledávání
Po vyplnění indexu můžete vydávat vyhledávací dotazy do koncového bodu [služby](search-query-overview.md) pomocí jednoduchých požadavků HTTP s [rozhraním REST API](/rest/api/searchservice/Search-Documents) nebo [sadou .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations).

Krok [ujte: Vytvořte první vyhledávací aplikaci,](tutorial-csharp-create-first-app.md) kterou chcete vytvořit, a pak rozšiřte webovou stránku, která shromažďuje vstup uživatele a zpracovává výsledky. [Postman](search-get-started-postman.md) můžete použít také pro interaktivní volání REST nebo integrovaný [Průzkumník vyhledávání](search-explorer.md) na webu Azure Portal k dotazování na existující index.

## <a name="how-it-compares"></a>Srovnání s ostatními řešeními

Zákazníci se často ptají, jak azure kognitivní vyhledávání porovnává s jinými řešeními souvisejícími s vyhledáváním. Následující tabulka shrnuje klíčové rozdíly.

| Porovnaná alternativa | Klíčové rozdíly |
|-------------|-----------------|
|Bing | [Rozhraní API Bingu pro vyhledávání na webu](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/) hledá odpovídající zadané termíny v indexech na Bing.com. Indexy jsou vytvořené z webového obsahu HTML, XML a dalších typů na veřejných webech. [Vlastní vyhledávání Bingu](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/) staví na stejných základech a nabízí stejnou technologii prohledávacího modulu pro typy webového obsahu vymezeného na jednotlivé weby.<br/><br/>Azure Cognitive Search prohledává index, který definujete, naplněný daty a dokumenty, které vlastníte, často z různých zdrojů. Azure Cognitive Search má prohledávací funkce pro některé zdroje dat prostřednictvím [indexerů](search-indexer-overview.md), ale můžete tlačit jakýkoli dokument JSON, který odpovídá schématu indexu do jednoho konsolidovaného prostředku s možností vyhledávání. |
|Databázové hledání | Mnoho databázových platforem obsahuje integrované prostředí pro vyhledávání. SQL Server má [fulltextové vyhledávání](https://docs.microsoft.com/sql/relational-databases/search/full-text-search). Cosmos DB a podobné technologie mají dotazovatelné indexy. Při vyhodnocování produktů, které kombinují vyhledávání a úložiště, může být obtížné určit, kterým směrem se vydat. Mnoho řešení používá obojí: DBMS pro úložiště a Azure Cognitive Search pro specializované funkce vyhledávání.<br/><br/>Ve srovnání s vyhledáváním DBMS ukládá Azure Cognitive Search obsah z heterogenních zdrojů a nabízí specializované funkce zpracování textu, jako je zpracování textu podporující jazykovou zprávu (vyplývající, lemmatizace, textové formy) v [56 jazycích](https://docs.microsoft.com/rest/api/searchservice/language-support). Podporuje také automatické opravy slov s pravopisnými chybami, [synonyma](https://docs.microsoft.com/rest/api/searchservice/synonym-map-operations), [návrhy](https://docs.microsoft.com/rest/api/searchservice/suggestions), [ovládací prvky pro určování skóre](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [omezující vlastnosti](https://docs.microsoft.com/azure/search/search-filters-facets) a [vlastní tokenizaci](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search). [Fulltextový vyhledávač](search-lucene-query-architecture.md) v Azure Cognitive Search je založený na Apache Lucene, což je průmyslový standard při načítání informací. Zatímco Azure Cognitive Search uchovává data ve formě obráceného indexu, je zřídka náhradou za skutečné úložiště dat. Další informace najdete v tomto [příspěvku fóra](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data). <br/><br/>Dalším důležitým bodem v této kategorii je využití prostředků. Indexování a určité operace dotazování jsou často velmi náročné na výpočetní operace. Přenesení zátěže vyhledávání z DBMS na vyhrazené řešení v cloudu zachová systémové prostředky pro zpracování transakcí. Díky přenesení vyhledávání na externí prostředky můžete také snadno upravit kapacitu tak, aby odpovídala množství dotazů.|
|Vyhrazené řešení pro vyhledávání | Za předpokladu, že jste se rozhodli pro vyhrazené vyhledávání s komplexními funkcemi, poslední kategorií je srovnání místních řešení a cloudové služby. Mnoho vyhledávacích technologií nabízí kontrolu nad indexováním a kanály dotazů, přístup k širší syntaxi dotazů a filtrování, řízení podle priority a relevance a funkce pro samořízené a inteligentní vyhledávání. <br/><br/>Cloudová služba je správnou volbou, pokud chcete řešení na klíč s minimální režií a údržbou a upravitelnou kapacitou. <br/><br/>V rámci cloudu nabízí několik poskytovatelů srovnatelné základní funkce s fulltextovým vyhledáváním, geografickým vyhledáváním a schopností zvládat určitou úroveň nejednoznačnosti v zadáních hledání. Obvykle jde o [specializovanou funkci](#feature-drilldown) nebo snadnost a celkovou jednoduchost rozhraní API, nástrojů a správy, která určuje, co je nejvhodnější. |

Mezi poskytovateli cloudu je Azure Cognitive Search nejsilnější pro úlohy fulltextového vyhledávání v úložištích obsahu a databázích v Azure, pro aplikace, které se spoléhají především na vyhledávání načítání informací a navigace v obsahu. 

Mezi hlavní silné stránky patří:

+ Integrace dat Azure (prohledávací moduly) ve vrstvě indexování
+ Portál Azure Portal pro centrální správu
+ Škálování kapacity, spolehlivost a špičková dostupnost Azure
+ AI zpracování nezpracovaných dat, aby bylo možné prohledávat, včetně textu z obrázků, nebo hledání vzorů v nestrukturovaném obsahu.
+ Jazykové a vlastní analýzy s analyzátory pro výkonné fulltextové vyhledávání v 56 jazycích
+ [Základní funkce společné pro aplikace zaměřené na vyhledávání](#feature-drilldown): vyhodnocování (bodování), používání faset, návrhy, synonyma, geografické vyhledávání a další.

> [!Note]
> Zdroje dat mimo Azure jsou plně podporované, ale závisí na metodice nabízení s vyššími nároky na kód než indexery. Pomocí api můžete kanál libovolné kolekce dokumentů JSON do indexu Azure Cognitive Search.

Mezi naše zákazníky, které mohou využívat nejširší škálu funkcí v Azure Cognitive Search patří online katalogy, obchodní programy a aplikace pro vyhledávání dokumentů.

## <a name="rest-api--net-sdk"></a>REST API | .NET SDK

Zatímco mnoho úloh lze provádět na portálu, Azure Cognitive Search je určen pro vývojáře, kteří chtějí integrovat funkce vyhledávání do existujících aplikací. Dostupná jsou následující programovací rozhraní.

|Platforma |Popis |
|-----|------------|
|[Odpočinku](/rest/api/searchservice/) | Příkazy HTTP podporované každou programovací platformou a jazykem, včetně Xamarinu, Javy a JavaScriptu.|
|[Sada SDK rozhraní .NET](search-howto-dotnet-sdk.md) | Obálka .NET pro REST API nabízí efektivní kódování v jazyce C# a dalších jazycích spravovaného kódu určených pro rozhraní .NET Framework. |

## <a name="free-trial"></a>Bezplatná zkušební verze
Předplatitelé Azure můžou [zřídit službu na úrovni Free](search-create-service-portal.md).

Pokud nejste předplatitel, můžete si [zdarma otevřít účet Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). Budete dostávat kredity za vyzkoušení placených služeb Azure. Po jejich vyčerpání si můžete účet ponechat a používat [bezplatné služby Azure](https://azure.microsoft.com/free/). Nikdy vám nebudeme účtovat žádné poplatky, pokud si sami nezměníte nastavení a nezačnete používat placené služby.

Jako druhou možnost si můžete [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): Vaše předplatné MSDN vám každý měsíc dává kredity, které můžete použít k placení za služby Azure. 

## <a name="how-to-get-started"></a>Jak začít

1. Vytvořte si [bezplatnou službu](search-create-service-portal.md). Všechny rychlé starty a kurzy je možné absolvovat s bezplatnou službou.

2. Projděte si [kurz použití integrovaných nástrojů pro indexování a dotazy](search-get-started-portal.md). Seznamte se s důležitými koncepty a informacemi, které portál nabízí.

3. Pokračujte s kódem s využitím .NET nebo rozhraní REST API:

   + [Jak používat sadu .NET SDK](search-howto-dotnet-sdk.md) představuje hlavní pracovní postup ve spravovaném kódu.  
   + [Začínáme s rozhraním API REST](https://github.com/Azure-Samples/search-rest-api-getting-started) ukazuje stejný postup pomocí rozhraní REST API. Tento rychlý start můžete také použít k volání REST API z Postman nebo Fiddler: [Prozkoumejte Azure Cognitive Search REST API](search-get-started-postman.md).

## <a name="watch-this-video"></a>Podívejte se na toto video

Vyhledávací moduly jsou běžnými prostředky načítání informací v mobilních aplikacích, na webu a ve firemních úložištích dat. Azure Cognitive Search poskytuje nástroje pro vytváření prostředí pro vyhledávání podobné těm na velkých komerčních webech.

V tomto 9minutovém videu od manažera programu Liama Cavanagha se dozvíte, jak může být integrace vyhledávacího modulu přínosná pro vaši aplikaci. Krátké ukázky pokrývají klíčové funkce azure kognitivního vyhledávání a jak vypadá typický pracovní postup. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ První 3 minuty pojednávají o klíčových funkcích a případech použití.
+ 3 a 4 minuta popisuje zřizování služby. 
+ 4 až 6 minuta popisuje průvodce importem dat, který se použije k vytvoření indexu pomocí předdefinované datové sady nemovitostí.
+ 6 až 9 minuta popisuje Průzkumníka služby Search a různé dotazy.
