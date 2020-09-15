---
title: Přehled funkcí
titleSuffix: Azure Cognitive Search
description: Prozkoumejte kategorie funkcí služby Azure Kognitivní hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/15/2020
ms.openlocfilehash: 540936ac190e981ff786ea90a91516161346d65c
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90533054"
---
# <a name="features-of-azure-cognitive-search"></a>Funkce Azure Kognitivní hledání

Azure Kognitivní hledání poskytuje fulltextový vyhledávací modul, trvalé úložiště indexů vyhledávání, integrované AI používané při indexování k extrakci více textových a struktur a rozhraní API a nástrojů. Následující tabulka shrnuje funkce podle kategorie. Další informace o tom, jak Kognitivní hledání porovnává s dalšími technologiemi pro hledání, najdete v tématu [co je Azure kognitivní hledání?](search-what-is-azure-search.md).

## <a name="indexing-features"></a>Funkce indexování

| Kategorií&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Funkce |
|-------------------|----------|
| Zdroje dat | Indexy Azure Kognitivní hledání přijímají data z libovolného zdroje za předpokladu, že se odešlou jako datová struktura JSON. <br/><br/> [**Indexery**](search-indexer-overview.md) automatizují přijímání dat pro podporované zdroje dat Azure a zpracování serializace JSON. Připojte se k [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-cosmosdb.md)nebo [úložišti objektů BLOB v Azure](search-howto-indexing-azure-blob-storage.md) a extrahujte prohledávatelný obsah v primárních úložištích dat. Indexery Azure Blob můžou *pronikat do dokumentů* za účelem [extrahování textu z hlavních formátů souborů](search-howto-indexing-azure-blob-storage.md), včetně dokumentů Microsoft Office, PDF a HTML. |
| Hierarchické a vnořené datové struktury | [**Komplexní typy**](search-howto-complex-data-types.md) a kolekce umožňují modelovat prakticky jakýkoli typ struktury JSON jako index služby Azure kognitivní hledání. Mohutnost 1: n se dá vyjádřit nativně prostřednictvím kolekcí, komplexních typů a kolekcí komplexních typů.|
| Lingvistická analýza | Analyzátory jsou komponenty, které slouží ke zpracování textu během operací indexování a vyhledávání. Existují dva typy. <br/><br/>[**Vlastní lexikální analyzátory**](index-add-custom-analyzers.md) se používají pro komplexní vyhledávací dotazy s využitím porovnávání výslovnosti a regulárních výrazů. <br/><br/>[**Jazykové analyzátory**](index-add-language-analyzers.md) od Lucene nebo Microsoftu se používají k inteligentnímu zpracování lingvistiky pro konkrétní jazyk včetně časů sloves, rodu, nepravidelného množného čísla podstatných jmen (například anglické „mouse“ oproti „mice“), rozkladu slov, dělení slov (pro jazyky bez mezer) a dalších možností. <br/><br/>|

## <a name="ai-enrichment-and-knowledge-mining"></a>Rozšíření AI a dolování znalostí

| Kategorií&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Funkce |
|-------------------|----------|
|Zpracování AI při indexování | [**Rozšíření AI**](cognitive-search-concept-intro.md) pro analýzu obrázků a textu lze použít pro kanál indexování k extrakci textových informací z nezpracovaného obsahu. Mezi příklady [integrovaných dovedností](cognitive-search-predefined-skills.md) patří optické rozpoznávání znaků (umožňuje prohledávání naskenovaných obrázků JPEG), rozpoznávání entit (identifikuje organizaci, název nebo umístění) a rozpoznávání klíčových frází. Můžete také [naprogramovat vlastní dovednosti](cognitive-search-create-custom-skill-example.md), které připojíte ke kanálu. Můžete také [integrovat Azure Machine Learning vytvořené dovednosti](./cognitive-search-tutorial-aml-custom-skill.md). |
| Ukládání obohaceného obsahu pro analýzu a spotřebu ve scénářích bez vyhledávání | [**Znalostní báze**](knowledge-store-concept-intro.md) je rozšíření indexování založené na AI. Při Azure Storage jako back-endu můžete ukládat obohacení vytvořené během indexování. Tyto artefakty lze použít k návrhu lepšího dovednostií nebo k vytváření tvarů a strukturování z Amorphous nebo nejednoznačných dat. Můžete vytvořit projekce těchto struktur, které cílí na konkrétní úlohy nebo uživatele. Můžete také přímo analyzovat extrahovaná data nebo je načíst do jiných aplikací.<br/><br/> |
| Obsah uložený v mezipaměti | [**Přírůstkové obohacení (verze Preview)**](cognitive-search-incremental-indexing-conceptual.md) omezuje zpracování na pouze dokumenty, které byly změněny konkrétní úpravou v kanálu, a to pomocí obsahu uloženého v mezipaměti pro části kanálu, které se nemění. |

## <a name="query-and-user-experience"></a>Dotazování a činnost koncového uživatele

| Kategorií&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Funkce |
|-------------------|----------|
|Hledání v textu volného tvaru | [**Fulltextové vyhledávání**](search-lucene-query-architecture.md) je primární případ použití pro většinu aplikací založených na vyhledávání. Dotazy se dají formulovat pomocí podporované syntaxe. <br/><br/>[**Jednoduchá syntaxe dotazů**](query-simple-syntax.md) poskytuje logické operátory, operátory pro hledání slovních spojení, operátory pro přípony a operátory priority.<br/><br/>[**Syntaxe dotazů Lucene**](query-lucene-syntax.md) zahrnuje všechny operace v jednoduché syntaxi s rozšířeními pro přibližné vyhledávání, vyhledávání blízkých výrazů, zvyšování skóre termínu a regulární výrazy.|
| Relevance | [**Jednoduché bodování**](index-add-scoring-profiles.md) je klíčovou výhodou pro Azure kognitivní hledání. Profily vyhodnocování (bodovací profily) se používají k modelování relevance jako funkce hodnot v samotných dokumentech. Můžete například chtít, aby se novější produkty nebo produkty se slevou zobrazovaly ve výsledcích hledání na vyšších pozicích. Můžete také vytvořit profily vyhodnocování pomocí značek pro individuální vyhodnocování podle preferencí vyhledávání zákazníků, které sledujete a ukládáte odděleně. |
| Geografické vyhledávání | Azure Kognitivní hledání zpracovává, filtruje a zobrazuje geografické polohy. Umožňuje uživatelům zkoumat data podle blízkosti výsledku hledání k fyzické poloze. Pokud o tom chcete vědět víc, [pusťte si toto video](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) nebo [se podívejte na tuto ukázku](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). |
| Filtry a omezující vlastnosti | [**Fasetová navigace**](search-faceted-navigation.md) se aktivuje jedním parametrem dotazu. Azure Kognitivní hledání vrátí vlastnost s omezující navigační strukturou, kterou můžete použít jako kód za seznamem kategorií, pro filtrování samy sebe (například pro filtrování položek katalogu podle cenového rozsahu nebo značky). <br/><br/> [**Filtry**](query-odata-filter-orderby-syntax.md) se dají použít k začlenění fasetové navigace do uživatelského rozhraní aplikace, rozšíření možností formulování dotazu a filtrování na základě kritérií zadaných uživatelem nebo vývojářem. Filtry se vytvářejí pomocí syntaxe OData. |
| Uživatelské prostředí | Na panelu hledání lze povolit [**Automatické dokončování**](search-autocomplete-tutorial.md) dotazů typu dotazování. <br/><br/>[**Návrhy hledání**](/rest/api/searchservice/suggesters) fungují také s částečnými textovými vstupy na panelu hledání, ale výsledky jsou skutečné dokumenty ve vašem indexu, a ne termíny dotazu. <br/><br/>[**Synonyma**](search-synonyms.md) přidružují ekvivalentní termíny, které implicitně rozšiřují rozsah dotazu, aniž by uživatel musel zadávat alternativní termíny. <br/><br/>[**Zvýrazňování nalezených položek**](/rest/api/searchservice/Search-Documents) použije formátování textu na odpovídající klíčové slovo ve výsledcích hledání. Můžete si zvolit pole, která budou vracet zvýrazněné fragmenty.<br/><br/>[**Řazení**](/rest/api/searchservice/Search-Documents) se poskytuje pro několik polí prostřednictvím schématu indexu. Zapíná se pak v době dotazu jedním parametrem vyhledávání.<br/><br/> [**Stránkování**](search-pagination-page-layout.md) a omezení výsledků hledání je jednoduché s jemně vyladěným ovládacím prvkem, který Azure kognitivní hledání nabízí nad výsledky hledání.  <br/><br/>|

## <a name="security-features"></a>Funkce zabezpečení

| Kategorií&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Funkce |
|-------------------|----------|
| Šifrování dat | [**Šifrování spravované Microsoftem v klidovém režimu**](search-security-overview.md#encrypted-transmissions-and-storage) je integrované do interní vrstvy úložiště a je neodvolatelné. <br/><br/>[**Šifrovací klíče spravované zákazníkem**](search-security-manage-encryption-keys.md) , které vytvoříte a spravujete v Azure Key Vault lze použít k dodatečnému šifrování indexů a mapování synonym. Pro služby vytvořené po 1 2020. srpna se šifrování CMK rozšíří na data na dočasných discích, aby bylo úplné šifrování indexovaného obsahu velmi dvojité.|
| Ochrana koncového bodu | [**Pravidla protokolu IP pro podporu příchozích bran firewall**](service-configure-firewall.md) umožňují nastavit rozsahy IP adres, přes které bude služba vyhledávání přijímat požadavky.<br/><br/>[**Vytvoření privátního koncového bodu**](service-create-private-endpoint.md) pomocí privátního propojení Azure k vynucení všech požadavků přes virtuální síť. |
| Odchozí zabezpečení (indexery) | [**Přístup k datům prostřednictvím soukromých koncových bodů**](search-indexer-howto-access-private.md) umožňuje indexeru připojit se k prostředkům Azure chráněným pomocí privátního propojení Azure.<br/><br/>[**Přístup k datům pomocí důvěryhodné identity**](search-howto-managed-identities-data-sources.md) znamená, že připojovací řetězce k externím zdrojům dat můžou vynechat uživatelská jména a hesla. Když indexer připojí ke zdroji dat, prostředek umožňuje připojení, pokud byla vyhledávací služba předtím zaregistrována jako důvěryhodná služba. |

## <a name="portal-features"></a>Funkce portálu

| Kategorií&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Funkce |
|-------------------|----------|
| Nástroje pro vytváření prototypů a kontrolu | [**Průvodce importem dat**](search-import-data-portal.md) vytvoří indexy, indexery, dovednosti a definice zdrojů dat. <br/><br/>[**Průzkumník služby Search**](search-explorer.md) slouží k testování dotazů a k upřesnění profilů vyhodnocování.<br/><br/>Možnost [**vytvořit ukázkovou aplikaci**](search-create-app-portal.md) slouží k vygenerování stránky HTML, která se dá použít k otestování vyhledávacího prostředí.  |
| Monitorování a diagnostika | [**Umožněte funkcím monitorování**](search-monitor-usage.md) , abyste mohli přesáhnout metriky, které jsou vždy viditelné na portálu. Metriky pro dotazy za sekundu, latenci a omezování se zachycují a uvádějí na stránkách portálu bez další nezbytné konfigurace.|

## <a name="programmability"></a>Programovatelnost

| Kategorií&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Funkce |
|-------------------|----------|
| REST | [**REST API služby**](https://docs.microsoft.com/rest/api/searchservice/) je pro operace roviny dat, včetně všech operací souvisejících s indexováním, dotazy a obohacením AI. Pomocí této klientské knihovny můžete také načíst systémové informace a statistiky. <br/><br/>[**REST API správy**](https://docs.microsoft.com/rest/api/searchmanagement/) je pro vytvoření služby a vyčištění prostřednictvím Azure Resource Manager. Toto rozhraní API můžete použít také ke správě klíčů a zřízení služby.|
| Sada Azure SDK pro .NET | [**Azure.Search.Documents**](https://docs.microsoft.com/dotnet/api/overview/azure/search.documents-readme) slouží k operacím roviny dat, včetně všech operací souvisejících s indexováním, dotazy a rozšířením AI. Pomocí této klientské knihovny můžete také načíst systémové informace a statistiky. <br/><br/>[**Microsoft. Azure. Management. Search**](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search) je pro vytvoření služby a vyčištění prostřednictvím Azure Resource Manager. Toto rozhraní API můžete použít také ke správě klíčů a zřízení služby.|
| Azure SDK pro Javu | [**com.azure.search.documents**](https://docs.microsoft.com/java/api/com.azure.search.documents) slouží k operacím roviny dat, včetně všech operací souvisejících s indexováním, dotazy a rozšířením AI. Pomocí této klientské knihovny můžete také načíst systémové informace a statistiky. <br/><br/>[**com. Microsoft. Azure. Management. Search**](https://docs.microsoft.com/java/api/overview/azure/search/management) je pro vytvoření služby a vyčištění prostřednictvím Azure Resource Manager. Toto rozhraní API můžete použít také ke správě klíčů a zřízení služby.|
| Azure SDK pro Python | [**Azure-Search-Documents**](https://docs.microsoft.com/python/api/overview/azure/search-documents-readme) je pro operace roviny dat, včetně všech operací souvisejících s indexováním, dotazy a obohacením AI. Pomocí této klientské knihovny můžete také načíst systémové informace a statistiky. <br/><br/>[**Azure-správa-Search**](https://docs.microsoft.com/python/api/overview/azure/search/management) je pro vytvoření služby a vyčištění prostřednictvím Azure Resource Manager. Toto rozhraní API můžete použít také ke správě klíčů a zřízení služby. |
| Sada Azure SDK pro JavaScript a TypeScript | [**Azure/Search – dokumenty**](https://docs.microsoft.com/javascript/api/@azure/search-documents/) jsou pro operace roviny dat, včetně všech operací souvisejících s indexováním, dotazy a rozšířením AI. Pomocí této klientské knihovny můžete také načíst systémové informace a statistiky. <br/><br/>[**Azure/ARM – vyhledávání**](https://docs.microsoft.com/javascript/api/@azure/arm-search/) je pro vytvoření služby a vyčištění prostřednictvím Azure Resource Manager. Toto rozhraní API můžete použít také ke správě klíčů a zřízení služby. |

## <a name="see-also"></a>Viz také

+ [Co je nového v Kognitivní hledání](whats-new.md)

+ [Funkce ve verzi Preview v Kognitivní hledání](search-api-preview.md)