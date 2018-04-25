---
title: Co je Azure Search | Microsoft Docs
description: Azure Search je plně spravovaná hostovaná cloudová vyhledávací služba. Další informace najdete v tomto přehledu funkce.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: overview
ms.date: 11/10/2017
ms.author: heidist
ms.openlocfilehash: ad5c60c246c2946e4dd3a5bb6b4d6e8d21d2b03d
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="what-is-azure-search"></a>Co je Azure Search?
Azure Search je cloudové řešení pro vyhledávání v podobě služby, které poskytuje vývojářům rozhraní API a nástroje, aby mohli přidat výkonné vyhledávání v obsahu ve webových, mobilních a firemních aplikacích.

Funkce se zveřejňuje prostřednictvím jednoduchého rozhraní [REST API](/rest/api/searchservice/) nebo [.NET SDK](search-howto-dotnet-sdk.md), které zakrývá zákonitou složitost načítání informací. Kromě rozhraní API portál Azure poskytuje podporu správy a správy obsahu a nástroje pro vytváření prototypů a dotazů na indexy. Služba běží v cloudu, proto infrastrukturu a dostupnost spravuje Microsoft.

<a name="feature-drilldown"></a>

## <a name="feature-summary"></a>Shrnutí funkce

| Kategorie | Funkce |
|----------|----------|
|Fulltextové vyhledávání a analýza textu | [Fulltextové vyhledávání](search-lucene-query-architecture.md) je primární případ použití pro většinu vyhledávacích aplikací. Dotazy se dají formulovat pomocí podporované syntaxe. <br/><br/>[**Jednoduchá syntaxe dotazů**](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) poskytuje logické operátory, operátory pro hledání slovních spojení, operátory pro přípony a operátory priority.<br/><br/>[**Syntaxe dotazů Lucene**](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) zahrnuje všechny operace v jednoduché syntaxi s rozšířeními pro přibližné vyhledávání, vyhledávání blízkých výrazů, zvyšování skóre termínu a regulární výrazy.| 
| Integrace dat | Indexy Azure Search přijímají data z jakéhokoliv zdroje, pokud je odeslaný jako datová struktura JSON. <br/><br/> Volitelně můžete pro podporované zdroje dat v Azure použít [**indexery**](search-indexer-overview.md), které automaticky projdou [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-cosmosdb.md) nebo [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) a synchronizují obsah vašeho indexu vyhledávání s vaším primárním úložištěm dat. Indexery Azure Blob můžou *pronikat do dokumentů* za účelem [indexování hlavních formátů souborů](search-howto-indexing-azure-blob-storage.md), včetně dokumentů Microsoft Office, PDF a HTML. |
| Lingvistická analýza | Analyzátory jsou komponenty, které slouží ke zpracování textu během operací indexování a vyhledávání. Existují dva typy. <br/><br/>[**Vlastní lexikální analyzátory**](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) se používají pro komplexní vyhledávací dotazy s využitím porovnávání výslovnosti a regulárních výrazů. <br/><br/>[**Jazykové analyzátory**](https://docs.microsoft.com/rest/api/searchservice/language-support) od Lucene nebo Microsoftu se používají k inteligentnímu zpracování lingvistiky pro konkrétní jazyk včetně časů sloves, rodu, nepravidelného množného čísla podstatných jmen (například anglické „mouse“ oproti „mice“), rozkladu slov, dělení slov (pro jazyky bez mezer) a dalších možností. |
| Geografické vyhledávání | Azure Search zpracovává, filtruje a zobrazuje zeměpisné polohy. Umožňuje uživatelům zkoumat data podle blízkosti výsledku hledání k fyzické poloze. Pokud o tom chcete vědět víc, [pusťte si toto video](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) nebo [se podívejte na tuto ukázku](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). |
| Funkce a možnosti pro uživatele | Je možné povolit [**návrhy hledání**](https://docs.microsoft.com/rest/api/searchservice/suggesters) k našeptávání dotazů na panelu hledání. Když uživatelé postupně zadávají hledaný text, navrhují se aktuální dokumenty ve vašem indexu. <br/><br/>[**Fasetová navigace**](https://docs.microsoft.com/azure/search/search-faceted-navigation) se aktivuje jedním parametrem dotazu. Azure Search vrací fasetovou strukturu navigace, kterou můžete použít jako kód pro seznam kategorií pro účely samořízeného filtrování (například k filtrování položek katalogu podle cenového rozsahu nebo značky). <br/><br/> [**Filtry**](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) se dají použít k začlenění fasetové navigace do uživatelského rozhraní aplikace, rozšíření možností formulování dotazu a filtrování na základě kritérií zadaných uživatelem nebo vývojářem. Filtry se vytvářejí pomocí syntaxe OData.<br/><br/> [**Zvýrazňování nalezených položek**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) použije formátování textu na odpovídající klíčové slovo ve výsledcích hledání. Můžete si zvolit pole, která budou vracet zvýrazněné fragmenty.<br/><br/>[**Řazení**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) se poskytuje pro několik polí prostřednictvím schématu indexu. Zapíná se pak v době dotazu jedním parametrem vyhledávání.<br/><br/> [**Stránkování**](search-pagination-page-layout.md) a omezování výsledků hledání je jednoduché s jemným laděním, které Azure Search nabízí ve vašich výsledcích hledání.  
| Relevance | [**Jednoduché vyhodnocování**](/rest/api/searchservice/add-scoring-profiles-to-a-search-index) (bodování) je klíčovou výhodou Azure Search. Profily vyhodnocování (bodovací profily) se používají k modelování relevance jako funkce hodnot v samotných dokumentech. Můžete například chtít, aby se novější produkty nebo produkty se slevou zobrazovaly ve výsledcích hledání na vyšších pozicích. Můžete také vytvořit profily vyhodnocování pomocí značek pro individuální vyhodnocování podle preferencí vyhledávání zákazníků, které sledujete a ukládáte odděleně. |
| Monitorování a vytváření sestav | [**Analytické informace o provozu vyhledávání**](search-traffic-analytics.md) se shromažďují a analyzují, aby se pomocí nich mohly dělat přehledy z toho, co uživatelé zadávají do vyhledávacího pole. <br/><br/>Metriky pro dotazy za sekundu, latenci a omezování se zachycují a uvádějí na stránkách portálu bez další nezbytné konfigurace. Můžete také snadno monitorovat index a počty dokumentů, abyste podle potřeby mohli upravit kapacitu. Další informace najdete v článku [Správa služby](search-manage.md). |
| Nástroje pro vytváření prototypů a kontrolu | Na portálu můžete pomocí [**Průvodce importem dat**](search-import-data-portal.md) nakonfigurovat indexery, návrháře indexů, aby sestavil index, a [**Průzkumníka služby Search**](search-explorer.md), abyste mohli testovat dotazy a upřesňovat profily vyhodnocování. Můžete také otevřít index a zobrazit jeho schéma. |
| Infrastruktura | Díky **vysoce dostupné platformě** je prostředí vyhledávací služby nadmíru spolehlivé. Při správném nastavení kapacity [Azure Search nabízí SLA 99,9 %](https://azure.microsoft.com/support/legal/sla/search/v1_0/).<br/><br/> Azure Search představuje **plně spravované a škálovatelné** řešení typu klient-server a nevyžaduje vůbec žádnou správu infrastruktury. Služba se dá přizpůsobit na míru vašim potřebám pomocí nastavení kapacity ve dvou dimenzích, aby mohla pracovat s větším úložištěm dokumentů, vyšším objemem dotazů nebo obojím.

## <a name="how-to-use-azure-search"></a>Použití Azure Search
### <a name="step-1-provision-service"></a>1. krok: Zřízení služby
Službu Azure Search si můžete nastavit na [portálu Azure](https://portal.azure.com/) nebo pomocí [rozhraní API pro správu prostředků Azure](/rest/api/searchmanagement/). Můžete si zvolit bezplatnou službu sdílenou s jinými odběrateli nebo [placenou úroveň](https://azure.microsoft.com/pricing/details/search/), která vyhrazuje prostředky používané jenom vaší službou. U placených úrovní je možné službu škálovat ve dvou dimenzích: 

- Přidat repliky, které zvětší kapacitu pro zpracování velkých objemů dotazů   
- Přidat oddíly, které zvětší úložiště pro více dokumentů 

Díky oddělenému řízení úložiště dokumentů a propustnosti dotazů můžete kalibrovat prostředky podle produkčních požadavků.

### <a name="step-2-create-index"></a>2. krok: Vytvoření indexu
Předtím, než budete moct nahrát prohledávatelný obsah, je nutné definovat index Azure Search. Index je jako databázová tabulka, která obsahuje vaše data a přijímá vyhledávací dotazy. Definujete schéma indexu do mapování tak, aby odráželo strukturu dokumentů, ve kterých chcete hledat, podobně jako pole v databázi.

Schéma se dá vytvořit na portálu Azure nebo programově pomocí [.NET SDK](search-howto-dotnet-sdk.md) nebo [REST API](/rest/api/searchservice/).

### <a name="step-3-index-data"></a>3. krok: Indexování dat
Po definování indexu jste připravení nahrát obsah. Můžete použít model nabízení nebo vyžádání.

Model vyžádání načítá data z externích zdrojů dat. Podporují ho *indexery*, které zjednodušují a automatizují aspekty příjmu dat, jako je například připojení k datům a jejich čtení a serializace. [Indexery](/rest/api/searchservice/Indexer-operations) jsou k dispozici pro Azure Cosmos DB, Azure SQL Database, Azure Blob Storage a SQL Server hostované ve virtuálním počítači Azure. V indexeru můžete nakonfigurovat aktualizaci dat na vyžádání nebo podle plánu.

Model nabízení se zajišťuje prostřednictvím sady SDK nebo rozhraní REST API, která slouží k posílání aktualizovaných dokumentů do indexu. Data můžete nabízet prakticky z libovolné datové sady ve formátu JSON. Pokyny k načítání dat najdete v článcích [Přidávání, aktualizace a odstraňování dokumentů](/rest/api/searchservice/addupdate-or-delete-documents) nebo [Jak používat sadu .NET SDK](search-howto-dotnet-sdk.md).

### <a name="step-4-search"></a>4. krok: Vyhledávání
Po naplnění indexu můžete [vydávat vyhledávací dotazy](/rest/api/searchservice/Search-Documents) do vašeho koncového bodu služby pomocí jednoduchých žádostí HTTP přes rozhraní REST API nebo .NET SDK.

## <a name="how-azure-search-compares"></a>Jak si stojí Azure Search

Zákazníci se často ptají, jak je na tom Azure Search v porovnání s jinými řešeními pro vyhledávání. Následující tabulka shrnuje klíčové rozdíly.

| Porovnaná alternativa | Klíčové rozdíly |
|--|--|
|Bing | [Rozhraní API Bingu pro vyhledávání na webu](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/) hledá odpovídající zadané termíny v indexech na Bing.com. Indexy jsou vytvořené z webového obsahu HTML, XML a dalších typů na veřejných webech. [Vlastní vyhledávání Bingu](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/) nabízí stejnou technologii prohledávacího modulu pro typy webového obsahu vymezeného na jednotlivé weby.<br/><br/>Azure Search prohledá index, který definujete, vyplněný daty a dokumenty, které vlastníte, často z různých zdrojů. Azure Search má schopnosti prohledávacího modulu pro některé zdroje dat prostřednictvím [indexerů](search-indexer-overview.md), můžete ale nabídnout jakýkoliv dokument JSON, který vyhovuje schématu indexu, do jediného konsolidovaného prohledávatelného prostředku. |
|Databázové hledání | [Fulltextové vyhledávání SQL Serveru](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) je pro interní obsah správce databáze v tabulkách SQL. <br/><br/>Azure Search ukládá obsah z heterogenních zdrojů a nabízí specializované funkce pro zpracování textu, jako je například jazyková a vlastní analýza. [Fulltextový vyhledávací modul](search-lucene-query-architecture.md) ve službě Azure Search je založený na oborovém standardu v načítání informací Apache Lucene. <br/><br/>Využívání prostředků je další důležitý bod. Vyhledávání v přirozeném jazyce je často výpočetně náročné. Přenesení zátěže vyhledávání na vyhrazené řešení zachová prostředky pro zpracování transakcí. Díky přenesení vyhledávání na externí prostředky můžete snadno upravit kapacitu tak, aby odpovídala množství dotazů.|
|Vyhrazené řešení pro vyhledávání | Místní nebo cloudová řešení jsou vyhrazená řešení pro vyhledávání plně vybavená funkcemi. Vyhledávací technologie obvykle nabízejí kontrolu nad indexováním a kanály dotazů, přístup k širší syntaxi dotazů a filtrování, řízení podle priority a relevance a funkce pro samořízené a inteligentní vyhledávání. <br/><br/>Vyhrazená řešení pro vyhledávání najdete v nabídkách jako cloudovou službu nebo jako samostatný server hostovaný místně nebo ve virtuálním počítači. Cloudová služba je správnou volbou, pokud chcete [řešení na klíč s minimální režií a údržbou a upravitelnou kapacitou](#cloud-service-advantage). <br/><br/>V rámci cloudu nabízí několik poskytovatelů srovnatelné základní funkce s fulltextovým vyhledáváním, geografickým vyhledáváním a schopností zvládat určitou úroveň nejednoznačnosti v zadáních hledání. Obvykle jde o [specializovanou funkci](#feature-drilldown) nebo snadnost a celkovou jednoduchost rozhraní API, nástrojů a správy, která určuje, co je nejvhodnější. |

Mezi cloudovými poskytovateli je Azure Search nejsilnější z hlediska fulltextového vyhledávání v úložištích obsahu a databázích na Azure a z hlediska aplikací, které závisí primárně na hledání při načítání informací i navigaci v obsahu. Mezi hlavní silné stránky patří:

+ Integrace dat Azure (prohledávací moduly) ve vrstvě indexování
+ Portál Azure Portal pro centrální správu
+ Škálování kapacity, spolehlivost a špičková dostupnost Azure
+ Jazykové a vlastní analýzy s analyzátory pro výkonné fulltextové vyhledávání v 56 jazycích
+ [Základní funkce společné pro aplikace zaměřené na vyhledávání](#feature-drilldown): vyhodnocování (bodování), používání faset, návrhy, synonyma, geografické vyhledávání a další.

> [!Note]
> Zdroje dat mimo Azure jsou plně podporované, ale závisí na metodice nabízení s vyššími nároky na kód než indexery. Pomocí rozhraní API můžete předat jakoukoliv kolekci dokumentů JSON do indexu Azure Search.

Mezi naše zákazníky, kteří umí využívat širokou škálu funkcí ve službě Azure Search, patří online katalogy, podnikové programy a aplikace pro zjišťování dokumentů.

## <a name="rest-api--net-sdk"></a>REST API | .Net SDK

Na portálu jde provádět celá řada úloh, služba Azure Search je ale určená pro vývojáře, kteří chtějí integrovat funkce vyhledávání do stávajících aplikací. Dostupná jsou následující programovací rozhraní.

|Platforma |Popis |
|-----|------------|
|[REST](/rest/api/searchservice/) | Příkazy HTTP podporované každou programovací platformou a jazykem, včetně Xamarinu, Javy a JavaScriptu.|
|[.NET SDK](search-howto-dotnet-sdk.md) | Obálka .NET pro REST API nabízí efektivní kódování v jazyce C# a dalších jazycích spravovaného kódu určených pro rozhraní .NET Framework. |

## <a name="free-trial"></a>Bezplatná zkušební verze
Předplatitelé Azure můžou [zřídit službu na úrovni Free](search-create-service-portal.md).

Pokud nejste předplatitel, můžete si [zdarma otevřít účet Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). Budete dostávat kredity za vyzkoušení placených služeb Azure. Po jejich vyčerpání si můžete účet ponechat a používat [bezplatné služby Azure](https://azure.microsoft.com/free/). Nikdy vám nebudeme účtovat žádné poplatky, pokud si sami nezměníte nastavení a nezačnete používat placené služby.

Jako druhou možnost si můžete [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): Vaše předplatné MSDN vám každý měsíc dává kredity, které můžete použít k placení za služby Azure. 

## <a name="how-to-get-started"></a>Jak začít

1. Vytvořte si službu na [úrovni Free](search-create-service-portal.md).

2. Projděte si jeden nebo více z následujících kurzů. 

  + [Jak používat sadu .NET SDK](search-howto-dotnet-sdk.md) představuje hlavní kroky ve spravovaném kódu.  
  + [Začínáme s rozhraním API REST](https://github.com/Azure-Samples/search-rest-api-getting-started) ukazuje stejný postup pomocí rozhraní REST API.  
  + [Vytvořte si první index v portálu](search-get-started-portal.md) pomocí integrovaných funkcí indexování a vytváření prototypů.   

Vyhledávací moduly jsou běžnými prostředky načítání informací v mobilních aplikacích, na webu a ve firemních úložištích dat. Azure Search nabízí nástroje pro vytváření vyhledávacího prostředí podobného těm na velkých komerčních webech.

V tomto 9minutovém videu od manažera programu Liama Cavanagha se dozvíte, jak může být integrace vyhledávacího modulu přínosná pro vaši aplikaci. Krátké ukázky ukazují klíčové funkce v Azure Search a jak vypadá obvyklý pracovní postup. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ První 3 minuty pojednávají o klíčových funkcích a případech použití.
+ 3. a 4. minuta popisuje zřizování služby. 
+ 4. až 6. minuta popisuje průvodce importem dat, který se použije k vytvoření indexu pomocí předdefinované datové sady nemovitostí.
+ 6. až 9. minuta popisuje Průzkumníka služby Search a různé dotazy.


