---
title: Indexování ve více jazycích pro jiné než anglické vyhledávací dotazy
titleSuffix: Azure Cognitive Search
description: Vytvořte index, který podporuje vícejazyčný obsah, a pak vytvořte dotazy vymezené tomuto obsahu.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 627ec77af4e492b4f22404972729cecdb1c40f06
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801600"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-cognitive-search"></a>Vytvoření indexu pro více jazyků v Azure Kognitivní hledání

Klíčovým požadavkem v aplikaci pro vyhledávání ve více jazycích je schopnost vyhledávat a načítat výsledky v jazyce uživatele. V Azure Kognitivní hledání jeden ze způsobů, jak splňovat jazykové požadavky vícejazyčné aplikace, je vytvořit vyhrazená pole pro ukládání řetězců do konkrétního jazyka a pak omezit fulltextové vyhledávání jenom na ta pole v době dotazu.

+ V části definice polí nastavte analyzátor jazyka, který vyvolá jazyková pravidla cílového jazyka. Úplný seznam podporovaných analyzátorů najdete v tématu [Přidání analyzátorů jazyka](index-add-language-analyzers.md).

+ V požadavku na dotaz nastavte parametry pro určení rozsahu fulltextového vyhledávání na konkrétní pole a pak ořízněte výsledky všech polí, která neposkytují obsah kompatibilní s vyhledávacím prostředím, které chcete poskytnout.

Úspěch této techniky se přestavuje na základě integrity obsahu polí. Azure Kognitivní hledání nepřevádí řetězce ani neprovádí detekci jazyka jako součást provádění dotazu. Ujistěte se, že pole obsahují řetězce, které očekáváte.

## <a name="define-fields-for-content-in-different-languages"></a>Definovat pole pro obsah v různých jazycích

V Azure Kognitivní hledání dotazy cílí na jeden index. Vývojáři, kteří chtějí poskytnout řetězce pro konkrétní jazyk v rámci jednoho vyhledávacího prostředí, obvykle definují vyhrazená pole pro ukládání hodnot: jedno pole pro anglické řetězce, jeden pro francouzštinu a tak dále.

Vlastnost analyzátoru definice pole se používá k nastavení [analyzátoru jazyka](index-add-language-analyzers.md). Použije se při indexování i provádění dotazů.

```JSON
{
  "name": "hotels-sample-index",
  "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "en.microsoft"
    },
    {
      "name": "Description_fr",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "fr.microsoft"
    },
```

## <a name="build-and-load-an-index"></a>Sestavení a načtení indexu

Mezilehlého (a možná zjevné) kroku je, že před [vytvořením dotazu musíte sestavit a naplnit index](search-get-started-dotnet.md) . Tento krok uvádíme pro úplnost. Jedním ze způsobů, jak určit dostupnost indexu, je kontrola seznamu indexů na [portálu](https://portal.azure.com).

> [!TIP]
> Rozpoznání jazyka a překlad textu se podporují během příjmu dat prostřednictvím [rozšíření AI](cognitive-search-concept-intro.md) a [dovednosti](cognitive-search-working-with-skillsets.md). Pokud máte zdroj dat Azure se smíšeným jazykem a obsahem, můžete vyzkoušet funkce pro detekci a překlad jazyka pomocí [Průvodce importem dat](cognitive-search-quickstart-blob.md).

## <a name="constrain-the-query-and-trim-results"></a>Omezení výsledků dotazu a oříznutí

Parametry dotazu slouží k omezení vyhledávání na konkrétní pole a následnému oříznutí výsledků všech polí, která nejsou pro váš scénář vhodná. 

| Parametry | Účel |
|-----------|--------------|
| **searchFields** | Omezí úplné hledání textu na seznam pojmenovaných polí. |
| **$select** | Ořízne odpověď tak, aby zahrnovala pouze pole, která zadáte. Ve výchozím nastavení jsou vrácena všechna pole, která lze načíst. Parametr **$Select** vám umožní zvolit, která z nich se má vrátit. |

Vzhledem k tomu, že je výsledkem omezení vyhledávání pole obsahující francouzsky řetězce, byste použili **searchFields** k zacílení dotazu na pole obsahující řetězce v daném jazyce.

Určení analyzátoru pro požadavek na dotaz není nutné. Analyzátor jazyka na definici pole bude vždy použit při zpracování dotazu. U dotazů, které určují více polí vyvolaných různými analyzátory jazyka, se výrazy nebo fráze zpracují nezávisle přiřazenými analyzátory pro každé pole.

Ve výchozím nastavení vrátí hledání všechna pole, která jsou označena jako získatelné. V takovém případě můžete chtít vyloučit pole, která neodpovídají jazykově specifickému hledanému prostředí, které chcete poskytnout. Konkrétně, pokud jste omezili hledání na pole se francouzskými řetězci, pravděpodobně budete chtít vyloučit pole s anglickým řetězcem z vašich výsledků. Použití parametru dotazu **$Select** vám poskytne kontrolu nad tím, která pole se vrátí do volající aplikace.

#### <a name="example-in-rest"></a>Příklad v REST

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "animaux acceptés",
    "searchFields": "Tags, Description_fr",
    "select": "HotelName, Description_fr, Address/City, Address/StateProvince, Tags",
    "count": "true"
}
```

#### <a name="example-in-c"></a>Příklad v jazyce C #

```csharp
private static void RunQueries(SearchClient srchclient)
{
    SearchOptions options;
    SearchResults<Hotel> response;

    options = new SearchOptions()
    {
        IncludeTotalCount = true,
        Filter = "",
        OrderBy = { "" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Description_fr");
    options.SearchFields.Add("Tags");
    options.SearchFields.Add("Description_fr");

    response = srchclient.Search<Hotel>("*", options);
    WriteDocuments(response);
}
```

## <a name="boost-language-specific-fields"></a>Posílit pole specifická pro konkrétní jazyk

Někdy není známý jazyk agenta, který vydává dotaz, a v takovém případě lze dotaz vydat pro všechna pole současně. Preference IA pro výsledky v určitém jazyce může být definovaná pomocí [profilů vyhodnocování](index-add-scoring-profiles.md). V následujícím příkladu se shody zjištěné v popisu v angličtině budou větší vzhledem k odpovídajícím hodnotám v jiných jazycích:

```JSON
  "scoringProfiles": [
    {
      "name": "englishFirst",
      "text": {
        "weights": { "description": 2 }
      }
    }
  ]
```

Do žádosti o vyhledávání byste pak zahrnuli profil vyhodnocování:

```http
POST /indexes/hotels/docs/search?api-version=2020-06-30
{
  "search": "pets allowed",
  "searchFields": "Tags, Description",
  "select": "HotelName, Tags, Description",
  "scoringProfile": "englishFirst",
  "count": "true"
}
```

## <a name="next-steps"></a>Další kroky

+ [Analyzátory jazyka](index-add-language-analyzers.md)
+ [Jak funguje fulltextové vyhledávání ve službě Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Rozhraní API pro vyhledávání v dokumentech](/rest/api/searchservice/search-documents)
+ [Přehled rozšíření AI](cognitive-search-concept-intro.md)
+ [Dovednosti – přehled](cognitive-search-working-with-skillsets.md)