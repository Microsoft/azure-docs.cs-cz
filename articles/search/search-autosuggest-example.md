---
title: Pro automatické návrhy příklad pro přidání rozevíracího seznamu podmínek do vyhledávacího pole – Azure Search
description: Přidáte vstupy navrhované dotazu vytvořením moduly pro návrhy a formulování požadavků, které vyvolají navrhované dotazy ve službě Azure Search.
manager: cgronlun
author: heidisteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: heidist
ms.openlocfilehash: 1db085d61c60d303aaff5c3b0d16998805fcda90
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2019
ms.locfileid: "58373072"
---
# <a name="example-add-autosuggest-for-dropdown-query-selections"></a>Příklad: Přidat pro automatické návrhy pro rozevírací seznam výběru dotazu

Rozevírací seznam navrhovaných dotazů podmínek může obsahovat zadáních hledání termín. Seznámili jste se tato možnost v komerční vyhledávací weby a fungují na podobném principu můžete implementovat v Azure Search pomocí [modulu pro návrhy konstrukce](index-add-suggesters.md) a operace návrhy na žádost o dotazu. V tomto článku se používají příklady k předvedení formulování dotazu pro automatické návrhy pomocí modulu pro návrhy, který už máte definované. 

## <a name="rest-api"></a>REST API

Můžete použít [Postman](search-fiddler.md) nebo [PowerShell](search-create-index-rest-api.md) a [rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/) vyzkoušet si v tomto příkladu, ale výsledky vrátí se jako dokumenty JSON. Reálné a vizuální prostředí lze nalézt pomocí [ukázkový kód pro automatické dokončování](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete).

### <a name="1---index-with-a-suggester-construct"></a>1 - index pomocí konstruktoru modulu pro návrhy

Pro automatické návrhy začíná [modulu pro návrhy konstrukce](index-add-suggesters.md) přidat do indexu, skládající se z polí, které přispívají rozevírací seznam hodnot. Daný následující schéma, navrhované dotazy se formulovat pomocí hodnot z polí hotelName a kategorie.

Za předpokladu, že minimální ukázkových datových sad v rychlých startech, hotelu názvy patří "Nápadité zůstat" a "Roach motel" a kategorie patří "Luxusní" a "Budget".

Pokud už máte indexu hotelů, měli byste vyřadit a znovu vytvořit pomocí následujícího schématu. Toto schéma přidá modulu pro návrhy. Nezapomeňte znovu načíst data při selhání.

```json
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ],
  "suggesters": [
    {
      "name": "sg",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": ["hotelName", "category"]
    }
  ]
}

```

### <a name="2---query-with-suggestions-operator"></a>2 - dotazovat pomocí operátoru návrhy

K použití modulu pro návrhy a vyvolávání pro automatické návrhy, je nutné odeslat [návrhy API](https://docs.microsoft.com/rest/api/searchservice/suggestions) požadovat pomocí GET nebo POST. Na vyžádání vyhledávací služba hledá potenciální shody po přijetí první tři znaky. 

V hlavičce požadavku, nastavte **klíč api-key** do klíče správce nebo dotaz, a **Content-Type** na application/json. 

```http
GET https://mydemo.search.windows.net/indexes/hotels/docs/suggest?search=fan&&suggesterName=sg
```

Požadavek kontroly všechna pole, které jsou součástí modulu pro návrhy (hotelName a kategorie), vrátí následující odpověď:

```
{
    "@odata.context": "https://mydemo.search.windows.net/indexes('hotels')/$metadata#docs(*)",
    "value": [
        {
            "@search.text": "Fancy Stay",
            "hotelId": "1"
        }
    ]
}
```

K zajištění očekávaný výsledek, váš klientský kód vykreslení výsledky jako rozevíracího seznamu v části panelu hledání.

## <a name="net-sdk-c"></a>.NET SDK (C#)

### <a name="1---index-with-a-suggester-construct"></a>1 - index pomocí konstruktoru modulu pro návrhy

V sadě .NET SDK, pomocí [třídy modulu pro návrhy](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). Modul pro návrhy je kolekce, ale jde převzít jenom jednu položku.

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels",
        Fields = FieldBuilder.BuildForType<Hotel>(),
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelId", "Category" }
            }}
    };

    serviceClient.Indexes.Create(definition);

}
```

### <a name="2---query-with-suggest-method"></a>2 - dotazu s navrhnout – metoda

[DocumentsOperationsExtensions.Suggest metoda](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet) se používá k vrácení řetězce navrhovaných dotazů.

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        UseFuzzyMatching = fuzzy,
        Top = 5
    };

    if (highlights)
    {
        sp.HighlightPreTag = "<b>";
        sp.HighlightPostTag = "</b>";
    }

    DocumentSuggestResult suggestResult = _indexClient.Documents.Suggest(term, "sg",sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = suggestResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

## <a name="see-also"></a>Další informace najdete v tématech

+ [Prozkoumejte rozhraní REST API pomocí nástroje Postman](search-fiddler.md)
+ [Příklad: Automatické dokončování](search-autocomplete-tutorial.md)
+ [Přidat moduly pro návrhy do indexu](index-add-suggesters.md)
+ [Přidejte třídu moduly pro návrhy pomocí .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)
+ [Volání návrhy pomocí GET nebo POST (REST API)](https://docs.microsoft.com/rest/api/searchservice/suggestions)
+ [Volání návrhy pomocí SuggestWithHttpMessagesAsync (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet-preview) nebo 