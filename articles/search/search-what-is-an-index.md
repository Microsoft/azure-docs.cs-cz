---
title: Vytvoření indexu
titleSuffix: Azure Cognitive Search
description: Zavádí koncepty a nástroje indexování v Azure Kognitivní hledání, včetně definic schémat a fyzické struktury dat.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: d9f4ba48a7dc6cdcf6d60e4e9da5f68fcc6b1f28
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509329"
---
# <a name="creating-search-indexes-in-azure-cognitive-search"></a>Vytváření indexů vyhledávání v Azure Kognitivní hledání

Vyhledávací index ukládá prohledávatelný obsah, který se používá pro fulltextové a filtrované dotazy. Index je definován schématem a uložený do služby, přičemž import dat je uvedený jako druhý krok. 

Indexy obsahují *dokumenty*. V koncepčním dokumentu je dokument jediná jednotka prohledávatelných dat v indexu. Prodejce může mít dokument pro každý produkt, organizace zpráv může mít dokument pro každý článek a tak dále. Mapování těchto konceptů na podrobnější ekvivalenty databáze: *Index vyhledávání* se rovná *tabulce* a *dokumenty* zhruba odpovídají *řádkům* v tabulce.

## <a name="whats-an-index-schema"></a>Co je schéma indexu?

Fyzická struktura indexu je určena schématem. Kolekce Fields je obvykle největší částí indexu, kde každé pole má název, přiřazený [datový typ](/rest/api/searchservice/Supported-data-types)a má atribut s povoleným chováním, které určují, jak se používají.

```json
{
  "name": "name_of_index, unique across the service",
  "fields": [
    {
      "name": "name_of_field",
      "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
      "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
      "filterable": true (default) | false,
      "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
      "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
      "key": true | false (default, only Edm.String fields can be keys),
      "retrievable": true (default) | false,
      "analyzer": "name_of_analyzer_for_search_and_indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
      "searchAnalyzer": "name_of_search_analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
      "indexAnalyzer": "name_of_indexing_analyzer", (only if 'searchAnalyzer' is set and 'analyzer' is not set)
      "synonymMaps": [ "name_of_synonym_map" ] (optional, only one synonym map per field is currently supported)
    }
  ],
  "suggesters": [ ],
  "scoringProfiles": [ ],
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) { },
  "encryptionKey":(optional){ }
  }
}
```

Další prvky jsou sbaleny pro zkrácení, ale následující odkazy mohou poskytnout podrobnosti: [návrhy](index-add-suggesters.md), [profily vyhodnocování](index-add-scoring-profiles.md), [analyzátory](search-analyzers.md) používané ke zpracování řetězců do tokenů podle jazykových pravidel nebo dalších vlastností podporovaných analyzátorem a nastavení [vzdáleného skriptování (CORS) mezi zdroji](#corsoptions) .

## <a name="choose-a-client"></a>Zvolit klienta

Existuje několik metodik pro vytvoření indexu vyhledávání. Pro prvotní vývoj a testování testování konceptů doporučujeme Azure Portal nebo rozhraní REST API.

Během vývoje si naplánujte časté časté sestavování. Vzhledem k tomu, že se ve službě vytvářejí fyzické struktury, je nutné vyřadit [a znovu vytvořit indexy](search-howto-reindex.md) pro většinu úprav existující definice pole. Můžete uvažovat o práci s podmnožinou vašich dat, aby bylo možné znovu sestavit nové sestavení.

### <a name="permissions"></a>Oprávnění

Všechny operace související s indexem vyhledávání, včetně požadavků GET, vyžadují svou definici, vyžadují v žádosti [klíč rozhraní API pro správu](search-security-api-keys.md) .

### <a name="limits"></a>Omezení

Všechny [úrovně služeb omezují](search-limits-quotas-capacity.md#index-limits) počet objektů, které můžete vytvořit. Pokud experimentování na bezplatné úrovni, můžete mít v jednom okamžiku pouze 3 indexy.

### <a name="use-azure-portal-to-create-a-search-index"></a>Vytvoření vyhledávacího indexu pomocí Azure Portal

Portál nabízí dvě možnosti pro vytvoření indexu vyhledávání: [**Průvodce importem dat**](search-import-data-portal.md) a **Přidání indexu** , který poskytuje pole pro zadání schématu indexu. Balíčky průvodce v dalších operacích vytvoří také indexer, zdroj dat a načtení dat. Pokud je to víc, než co potřebujete, měli byste použít jenom **Přidat index** nebo jiný přístup.

Na následujícím snímku obrazovky vidíte, kde můžete na portálu najít **index přidat** . **Importovat data** jsou pravá další dvířka.

  :::image type="content" source="media/search-what-is-an-index/add-index.png" alt-text="Přidat index – příkaz" border="true":::

> [!Tip]
> Návrh indexu prostřednictvím portálu vynutil požadavky a pravidla schématu pro konkrétní datové typy, jako je například zákaz funkcí fulltextového vyhledávání u číselných polí. Jakmile budete mít index s příheslem, můžete kód JSON zkopírovat z portálu a přidat ho do svého řešení.

### <a name="use-a-rest-client"></a>Použití klienta REST

Post i Visual Studio Code (s rozšířením pro Azure Kognitivní hledání) můžou fungovat jako klient vyhledávacího indexu. Pomocí kteréhokoli nástroje se můžete připojit k vaší vyhledávací službě a odeslat požadavky na [vytvoření indexu (REST)](/rest/api/searchservice/create-index) . K dispozici je řada kurzů a příkladů, které předvádějí klientům REST pro vytváření objektů. 

Pokud chcete získat informace o jednotlivých klientech, začněte jedním z těchto článků:

+ [Vytvoření indexu vyhledávání pomocí REST a předzálohovacího](search-get-started-rest.md)
+ [Začínáme s Visual Studio Code a Azure Kognitivní hledání](search-get-started-vs-code.md)

Nápovědu k formulování požadavků na index najdete v tématu [operace indexu (REST)](/rest/api/searchservice/index-operations) .

### <a name="use-an-sdk"></a>Použití sady SDK

Pro Kognitivní hledání sady SDK Azure implementují všeobecně dostupné funkce. V takovém případě můžete použít kteroukoli sadu SDK k vytvoření indexu vyhledávání. Všechny z nich poskytují **SearchIndexClient** , který obsahuje metody pro vytváření a aktualizaci indexů.

| Azure SDK | Klient | Příklady |
|-----------|--------|----------|
| .NET | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) | [Azure-Search-dotnet-Samples/rychlý Start/v11/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11) |
| Java | [SearchIndexClient](/java/api/com.azure.search.documents.indexes.searchindexclient) | [CreateIndexExample. Java](https://github.com/Azure/azure-sdk-for-java/blob/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) |
| JavaScript | [SearchIndexClient](/javascript/api/@azure/search-documents/searchindexclient) | [Indexy](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/indexes) |
| Python | [SearchIndexClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexclient) | [sample_index_crud_operations. py](https://github.com/Azure/azure-sdk-for-python/blob/7cd31ac01fed9c790cec71de438af9c45cb45821/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) |

## <a name="defining-fields"></a>Definování polí

Jedno pole typu EDM. String musí být určeno jako klíč dokumentu. Slouží k jednoznačné identifikaci každého dokumentu hledání. K naplnění stránky podrobností můžete načíst dokument pomocí jeho klíče.  

Pokud jsou příchozí data hierarchicky v podstatě, přiřaďte datový typ [komplexní typ](search-howto-complex-data-types.md) , který bude představovat vnořené struktury. Integrovaná Ukázková sada dat, hotely, znázorňuje komplexní typy pomocí adresy (obsahuje několik dílčích polí), které mají vztah 1:1 s každým hotelem, a prostorově komplexní kolekci, kde je k jednotlivým hotelům přidruženo více místností. 

Před vytvořením indexu přiřaďte všechny analyzátory k polím řetězců. To samé proveďte pro moduly pro návrhy, pokud chcete povolit automatické dokončování u konkrétních polí.

<a name="index-attributes"></a>

### <a name="attributes"></a>Atributy

Atributy polí určují, jak se pole používá, například jestli se používá ve fulltextovém vyhledávání, fasetové navigaci, operacích řazení a tak dále. 

Pole řetězců jsou často označena jako "prohledávatelné" a "získatelné". Mezi pole, která se používají k zúžení výsledků hledání, patří "seřaditelné", "filtrovatelné" a "FACET".

|Atribut|Popis|  
|---------------|-----------------|  
|prohledávatelná |Fulltextově prohledávatelné, lze provést lexikální analýzu, jako je dělení slov během indexování. Pokud nastavíte prohledávatelné pole na hodnotu jako „slunečný den“, interně se rozdělí na jednotlivé tokeny „slunečný“ a „den“. Podrobnosti najdete v článku [Jak funguje fulltextové vyhledávání](search-lucene-query-architecture.md).|  
|Filterable |Odkazované v dotazech $filter. Ve filtrovatelných polích typu `Edm.String` nebo `Collection(Edm.String)` nejdou dělit slova, takže se dají porovnávat jenom na přesné shody. Pokud například nastavíte takové pole f na „sunny day“, `$filter=f eq 'sunny'` nenajde žádné shody, ale `$filter=f eq 'sunny day'` ano. |  
|seřaditelné |Ve výchozím nastavení systém řadí výsledky podle skóre (bodů), můžete ale nakonfigurovat řazení na základě polí v dokumentech. Pole typu `Collection(Edm.String)` nelze seřadit. |  
|kategorizovatelné |Obvykle se používá v prezentaci výsledků hledání, která obsahuje počet nalezených položek podle kategorie (například hotely v konkrétním městě). Tuto možnost nejde použít s poli typu `Edm.GeographyPoint`. Pole typu `Edm.String` , která jsou filtrovatelné, "seřaditelné" nebo "FACET" mohou mít délku maximálně 32 kilobajtů. Podrobnosti najdete v článku [Vytvoření indexu (REST API)](/rest/api/searchservice/create-index).|  
|zkrat |Jedinečný identifikátor pro dokumenty v indexu. Jako pole key se musí zvolit právě jedno pole a musí být typu `Edm.String`.|  
|Retrievable |Určuje, jestli může být pole vrácené ve výsledku hledání. To je užitečné, když chcete použít pole (například *zisková marže*) jako filtrovací, řadicí a bodovací mechanismus, ale nechcete, aby pole bylo viditelné pro koncového uživatele. Tento atribut musí být `true` pro pole typu `key`.|  

I když můžete nová pole přidat kdykoliv, jsou existující definice polí zamknuté v indexu po dobu jeho existence. Z tohoto důvodu vývojáři obvykle používají portál k vytváření jednoduchých indexů, testování nápadů nebo k vyhledání nastavení pomocí stránek portálu. Časté změny návrhu indexu jsou efektivnější, pokud budete postupovat pomocí kódu, aby bylo možné index snadno znovu sestavit.

> [!NOTE]
> Rozhraní API, která použijete k vytvoření indexu, mají proměnlivé výchozí chování. Pro [rozhraní REST API](/rest/api/searchservice/Create-Index)je ve výchozím nastavení povolená většina atributů (například "prohledávatelné" a "získatelné" jsou pro řetězcová pole pravdivá) a často je stačí nastavit, jenom pokud je chcete vypnout. Pro sadu .NET SDK má opak hodnotu true. U jakékoli vlastnosti, kterou explicitně nenastavíte, je ve výchozím nastavení zakázáno odpovídající chování hledání, pokud ho výslovně nepovolíte.

<a name="index-size"></a>

## <a name="attributes-and-index-size-storage-implications"></a>Atributy a velikost indexu (důsledky úložiště)

Velikost indexu je určena velikostí dokumentů, které nahráváte, a konfigurací indexu, například bez ohledu na to, zda jste zahrnuli moduly pro návrhy, a jak nastavíte atributy na jednotlivá pole. 

Následující snímek obrazovky znázorňuje vzory úložiště indexů, které jsou výsledkem různých kombinací atributů. Index je založený na **indexu ukázek reálného majetku**, který můžete snadno vytvořit pomocí Průvodce importem dat. I když nejsou uvedena schémata indexu, můžete odvodit atributy na základě názvu indexu. Index *s možností prohledávání realestate* má například vybraný atribut "prohledávatelné" a nic jiného, index *realestate* má vybraný atribut "získatelné" a nic jiného a tak dále.

![Velikost indexu na základě výběru atributu](./media/search-what-is-an-index/realestate-index-size.png "Velikost indexu na základě výběru atributu")

I když jsou tyto varianty indexu umělé, můžeme na ně odkazovat, aby bylo možné využít široké porovnání atributů úložiště. Nastavím možnost "získatelné" zvýšit velikost indexu? No. Přidávají se pole do **přizpůsobitelné** velikosti indexu? Ano.

Indexy, které podporují filtrování a řazení, jsou proporcionálně větší než indexy podporující pouze fulltextové vyhledávání. Důvodem je to, že operace filtrování a řazení prohledají přesné shody a vyžadují přítomnost doslovnéch textových řetězců. Naopak vyhledávací pole podporující fulltextové dotazy používají obrácené indexy, které jsou vyplněny pomocí tokenů, které spotřebovávají méně místa než celé dokumenty. 

> [!Note]
> Architektura úložiště se považuje za podrobné informace o implementaci Azure Kognitivní hledání a může se změnit bez předchozího upozornění. V budoucnu není zaručeno, že aktuální chování bude zachováno.

<a name="corsoptions"></a>

## <a name="about-corsoptions"></a>Upozorňován `corsOptions`

Schémata indexů obsahují oddíl pro nastavení `corsOptions` . JavaScript na straně klienta nemůže ve výchozím nastavení volat žádná rozhraní API, protože v prohlížeči se zabrání všechny požadavky mezi zdroji. Pokud chcete pro svůj index povolit dotazy mezi zdroji, povolte CORS (sdílení prostředků mezi zdroji) nastavením atributu **corsOptions** . Z bezpečnostních důvodů podporují CORS jenom rozhraní API pro dotazy. 

Pro CORS se dají nastavit tyto možnosti:

+ **allowedOrigins** (povinné): Jedná se o seznam původních zdrojů, kterým bude udělen přístup k vašemu indexu. To znamená, že jakýkoli JavaScriptový kód, který je obsluhován z těchto původu, bude moci dotazovat váš index (za předpokladu, že poskytuje správný klíč rozhraní API). Každý původ má obvykle tvar, `protocol://<fully-qualified-domain-name>:<port>` i když `<port>` je často vynechán. Další podrobnosti najdete v tématu [sdílení prostředků mezi zdroji (Wikipedii)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) .

  Pokud chcete povolený přístup ke všem počátkům, zahrňte do `*` pole **allowedOrigins** jako jednu položku. *Toto není doporučený postup pro služby vyhledávání v provozu* , ale je často užitečný pro vývoj a ladění.

+ **maxAgeInSeconds** (volitelné): v prohlížečích se tato hodnota používá k určení doby (v sekundách), po kterou se budou odpovědi na kontrolu mezipaměti CORS ukládat do mezipaměti. Toto musí být nezáporné celé číslo. Čím větší je tato hodnota, tím vyšší bude výkon, tím déle bude trvat, než se změny zásad CORS projeví. Pokud není nastavená, použije se výchozí doba trvání 5 minut.

## <a name="next-steps"></a>Další kroky

Můžete získat praktické zkušenosti s vytvářením indexu pomocí skoro jakékoli ukázky nebo návodu pro Kognitivní hledání. Můžete zvolit libovolné rychlé starty z obsahu, abyste mohli začít.

Budete také chtít seznámit s metodologiemi pro načítání indexu s daty. Definice indexu a plnění se provádí společně. Následující články poskytují další informace.

+ [Přehled importu dat](search-what-is-data-import.md)

+ [Přidávání, aktualizace a odstraňování dokumentů (REST)](/rest/api/searchservice/addupdate-or-delete-documents) 