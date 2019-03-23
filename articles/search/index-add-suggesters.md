---
title: Přidání typeahead dotazů na index – Azure Search
description: Povolení akcí našeptávání dotazů ve službě Azure Search vytvořením moduly pro návrhy a formulování požadavků, které vyvolat automatické dokončování nebo autosuggested termíny dotazu.
ms.date: 03/22/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 877294e80d655ab75be78a5aa57854a03a5f267a
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370648"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-search"></a>Přidat moduly pro návrhy na index pro typeahead ve službě Azure Search

A **modulu pro návrhy** je konstrukce v [index Azure Search](search-what-is-an-index.md) , který podporuje prostředí "search jako vám type". Obsahuje seznam polí, pro které chcete povolit typeahead dotazu vstupy. Existují dvě varianty typeahead: [ *automatické dokončování* ](search-autocomplete-tutorial.md) dokončení termín nebo frázi při psaní se [ *pro automatické návrhy* ](search-autosuggest-example.md) poskytuje stručný seznam podmínek nebo frázích, které můžete vybrat jako vstup dotazu. Viděli jste nepochybně těchto projevů před v komerční vyhledávací weby.

![Vizuální porovnání automatického dokončování a pro automatické návrhy](./media/index-add-suggesters/visual-comparison-suggest-complete.png "vizuální porovnání automatického dokončování a pro automatické návrhy")

K implementaci těchto chování ve službě Azure Search, je jako součást index a dotazu. 

+ V indexu přidejte modulu pro návrhy. Můžete na portálu, rozhraní REST API nebo .NET SDK k vytvoření modulu pro návrhy. 

+ V dotazu zadejte buď pro automatické návrhy nebo automatické dokončování akci. 

> [!Important]
> Automatické dokončování je aktuálně ve verzi preview dostupná ve verzi preview rozhraní REST API a sady .NET SDK a nejsou podporovány pro aplikace v produkčním prostředí. 

Podpora Typeahead je povolena na základě na pole. Pokud chcete prostředí podobné je uvedeno na snímku obrazovky můžete implementovat obě typeahead chování ve stejném řešení hledání. Cíl i požadavky *dokumenty* kolekce konkrétního indexu a odpovědi se vrátí po uživatel má k dispozici alespoň tři vstupní řetězec znaků.

## <a name="create-a-suggester"></a>Vytvoření modulu pro návrhy

I když se modulu pro návrhy má několik vlastností, je primárně kolekce pole, pro které chcete povolit typeahead prostředí. Cestovní aplikaci může být například vhodné typeahead hledání na cíle, města a atrakce. Všechny tři pole by jako takové, přejděte v kolekci polí.

Pokud chcete vytvořit modulu pro návrhy, přidejte ho do schématu indexu. V indexu může mít jeden modul pro návrhy (konkrétně jeden modul pro návrhy v kolekci moduly pro návrhy). 

Rozhraní REST API, můžete přidat moduly pro návrhy prostřednictvím [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) nebo [aktualizaci indexu](https://docs.microsoft.com/rest/api/searchservice/update-index). 

  ```json
  {
    "name": "hotels",
    "fields": [
      . . .
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["hotelName", "category"]
      }
    ],
    "scoringProfiles": [
      . . .
    ]
  }
  ```

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

Klíčové body k upozornění ohledně moduly pro návrhy je, že je název (moduly pro návrhy jsou odkazovány podle názvu na vyžádání), searchMode (aktuálně pouze jeden "analyzingInfixMatching") a seznam polí, u kterých je povolená typeahead. 

Po vytvoření modulu pro návrhy, přidejte [návrhy API](https://docs.microsoft.com/rest/api/searchservice/suggestions) v logice dotaz, vyvolat funkci.

### <a name="property-reference"></a>Odkaz na vlastnost

Vlastnosti, které definují modulu pro návrhy, patří:

|Vlastnost      |Popis      |
|--------------|-----------------|
|`name`        |Název modulu pro návrhy. Použijte název modulu pro návrhy při volání [rozhraní REST API pro návrhy](https://docs.microsoft.com/rest/api/searchservice/suggestions) nebo [rozhraní REST API (preview) pro automatické dokončování](https://docs.microsoft.com/rest/api/searchservice/autocomplete).|
|`searchMode`  |Strategie použitá k vyhledání kandidátských frází. Jediný momentálně podporovaný režim je `analyzingInfixMatching`, který provádí flexibilní porovnávání frází na začátku nebo uprostřed vět.|
|`sourceFields`|Seznam jednoho nebo více polí, které jsou zdrojem obsahu pro návrhy. Pouze pole typu `Edm.String` a `Collection(Edm.String)` může být zdroje pro návrhy. Lze použít pouze pole, které nemají vlastní analyzátor jazyka nastavit.<p/>Zadejte jenom pole samo očekávaným způsobem a odpovídající odpověď, ať už jde o řetězec dokončené v panel hledání nebo v rozevíracím seznamu.<p/>Název hotelu je vhodným kandidátem, protože má přesnosti. Podrobné pole, jako jsou popisů a komentářů jsou příliš dense. Obdobně opakované pole, jako je například kategorie a značky, jsou méně účinné. V příkladech jsme zahrnuli "kategorie" i přesto prokázat, že může obsahovat více polí. |

### <a name="index-rebuilds-for-existing-fields"></a>Znovu sestaví index pro existující pole

Moduly pro návrhy obsahují pole, a pokud přidáváte modulu pro návrhy do existujícího indexu nebo změna jeho pole složení, budete pravděpodobně muset znovu sestavte index.

| Akce | Dopad |
|--------|--------|
| Vytvořit nové pole a současně vytvořit nového modulu pro návrhy v rámci jedné aktualizace | Nejmenší dopad. Obsahuje-li index dříve přidaná pole, přidávání nových polí a nového modulu pro návrhy nemá žádný vliv na existující pole. |
| Přidat existující pole modulu pro návrhy | Vysoký dopad. Přidání pole mění definici pole, proto je nutné [úplné opětovné sestavení](search-howto-reindex.md).|

## <a name="use-a-suggester"></a>Použít modulu pro návrhy

Jak již bylo uvedeno výše můžete modulu pro návrhy pro automatické návrhy a automatické dokončování. 

Modulu pro návrhy se odkazuje na žádost spolu s operaci. Například při volání GET REST, zadejte buď `suggest` nebo `autocomplete` v kolekci dokumentů. REST, po vytvoření modulu pro návrhy použijte [návrhy API](https://docs.microsoft.com/rest/api/searchservice/suggestions) nebo [rozhraní API pro automatické dokončování (preview)](https://docs.microsoft.com/rest/api/searchservice/autocomplete) v logice dotazu.

Pro platformu .NET, použijte [SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet-preview) nebo [AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet-preview&viewFallbackFrom=azure-dotnet).

Příklady demonstrující každého požadavku:

+ [Přidat pro automatické návrhy pro rozevírací seznam výběru dotazu](search-autosuggest-example.md)

+ [Přidání automatické dokončování ve více vstupů dílčí výraz ve službě Azure Search](search-autocomplete-tutorial.md) (ve verzi preview) 

## <a name="sample-code"></a>Ukázka kódu

[DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) obsahuje ukázkové C# a kód v Javě a ukazuje vytváření modulu pro návrhy, automatické návrhy automatického dokončování a omezující vlastnost navigace. 

Používá izolovaného prostoru služby Azure Search a předem načtené index tak všechny nemusíte je stisknutím klávesy F5 spusťte ho. Žádné předplatné nebo si zaregistrovat nezbytné.

## <a name="next-steps"></a>Další postup

Projděte si následující příklady naleznete v tématu Jak formulovali požadavky:

+ [Příklad autosuggested dotazu](search-autosuggest-example.md) 
+ [Příklad dotazu Autocompleted (preview)](search-autocomplete-tutorial.md) 
