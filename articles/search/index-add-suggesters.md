---
title: Přidání typeahead dotazů na index – Azure Search
description: Povolení akcí našeptávání dotazů ve službě Azure Search vytvořením moduly pro návrhy a formulování požadavků, které vyvolat automatické dokončování nebo autosuggested termíny dotazu.
ms.date: 05/02/2019
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
ms.openlocfilehash: eb6667a1429382ed566826de64ad7ffbe83183cf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65521886"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-search"></a>Přidat moduly pro návrhy na index pro typeahead ve službě Azure Search

A **modulu pro návrhy** je konstrukce v [index Azure Search](search-what-is-an-index.md) , který podporuje prostředí "search jako vám type". Obsahuje seznam polí, pro které chcete povolit typeahead dotazu vstupy. V rámci indexu, stejného modulu pro návrhy podporuje jednu nebo obě tyto dvě varianty typeahead: *automatické dokončování* dokončení termín nebo frázi při psaní se *návrhy* poskytuje krátký seznam výsledků. 

Následující snímek obrazovky ukazuje obě typeahead funkce. Na této stránce hledání Xbox automatické dokončování ve více položek můžete přejít na nové stránce výsledků hledání pro daný dotaz, že návrhy jsou skutečné výsledky, které se dostanete na stránku pro tuto konkrétní hru. Můžete omezit automatické dokončování pro jednu položku na panelu hledání nebo zadat seznam podobný znázorněno zde. Máte nějaké návrhy můžete zařízení surface libovolnou část dokumentu, která nejlépe popisuje výsledek.

![Vizuální porovnání automatického dokončování a navrhované dotazy](./media/index-add-suggesters/visual-comparison-suggest-complete.png "vizuální porovnání automatického dokončování a navrhované dotazy")

K implementaci těchto chování ve službě Azure Search, je jako součást index a dotazu. 

+ Součást index je modulu pro návrhy. Portálu, rozhraní REST API nebo .NET SDK můžete použít k vytvoření modulu pro návrhy. 

+ Komponentu dotazu je akce zadaný v požadavku dotazu (akce návrh nebo automatické dokončování). 

Na základě na pole je povolena podpora vyhledávání jako vám type. Pokud chcete prostředí podobné je uvedeno na snímku obrazovky můžete implementovat obě typeahead chování ve stejném řešení hledání. Cíl i požadavky *dokumenty* kolekce konkrétního indexu a odpovědi se vrátí po uživatel má k dispozici alespoň tři vstupní řetězec znaků.

## <a name="create-a-suggester"></a>Vytvoření navrhovatele

I když se modulu pro návrhy má několik vlastností, je primárně kolekce pole, pro které chcete povolit typeahead prostředí. Cestovní aplikaci může být například vhodné typeahead hledání na cíle, města a atrakce. Všechny tři pole by jako takové, přejděte v kolekci polí.

Pokud chcete vytvořit modulu pro návrhy, přidejte ho do schématu indexu. V indexu může mít jeden modul pro návrhy (konkrétně jeden modul pro návrhy v kolekci moduly pro návrhy). 

### <a name="use-the-rest-api"></a>Použití rozhraní REST API

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
Po vytvoření modulu pro návrhy, přidejte [návrhy API](https://docs.microsoft.com/rest/api/searchservice/suggestions) nebo [rozhraní API pro automatické dokončování](https://docs.microsoft.com/rest/api/searchservice/autocomplete) v logice dotaz, vyvolat funkci.

### <a name="use-the-net-sdk"></a>Použití sady .NET SDK

V C#, definovat [objektu modulu pro návrhy](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). `Suggesters` je kolekce, ale jde převzít jenom jednu položku. 

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

## <a name="property-reference"></a>Odkaz na vlastnost

Klíčové body k upozornění ohledně moduly pro návrhy je, že je název (moduly pro návrhy jsou odkazovány podle názvu na vyžádání), searchMode (aktuálně pouze jeden "analyzingInfixMatching") a seznam polí, u kterých je povolená typeahead. 

Vlastnosti, které definují modulu pro návrhy, patří:

|Vlastnost      |Popis      |
|--------------|-----------------|
|`name`        |Název modulu pro návrhy. Použijte název modulu pro návrhy při volání [rozhraní REST API pro návrhy](https://docs.microsoft.com/rest/api/searchservice/suggestions) nebo [rozhraní REST API pro automatické dokončování](https://docs.microsoft.com/rest/api/searchservice/autocomplete).|
|`searchMode`  |Strategie použitá k vyhledání kandidátských frází. Jediný momentálně podporovaný režim je `analyzingInfixMatching`, který provádí flexibilní porovnávání frází na začátku nebo uprostřed vět.|
|`sourceFields`|Seznam jednoho nebo více polí, které jsou zdrojem obsahu pro návrhy. Pouze pole typu `Edm.String` a `Collection(Edm.String)` může být zdroje pro návrhy. Lze použít pouze pole, které nemají vlastní analyzátor jazyka nastavit.<p/>Zadejte jenom pole samo očekávaným způsobem a odpovídající odpověď, ať už jde o řetězec dokončené v panel hledání nebo v rozevíracím seznamu.<p/>Název hotelu je vhodným kandidátem, protože má přesnosti. Podrobné pole, jako jsou popisů a komentářů jsou příliš dense. Obdobně opakované pole, jako je například kategorie a značky, jsou méně účinné. V příkladech jsme zahrnuli "kategorie" i přesto prokázat, že může obsahovat více polí. |

## <a name="when-to-create-a-suggester"></a>Kdy vytvořit modulu pro návrhy

Aby se zabránilo opětovné sestavení indexu, modulu pro návrhy a pole určená v `sourceFields` musí být vytvořeny ve stejnou dobu.

Pokud chcete přidat do existujícího indexu, ve kterém jsou existující pole součástí modulu pro návrhy `sourceFields`zásadním způsobem mění definici pole a vyžaduje se opětovné sestavení. Další informace najdete v tématu [postup nového sestavení indexu Azure Search](search-howto-reindex.md).

## <a name="how-to-use-a-suggester"></a>Jak používat modulu pro návrhy

Jak již bylo uvedeno výše můžete modulu pro návrhy pro navrhované dotazy a automatické dokončování. 

Modulu pro návrhy se odkazuje na žádost spolu s operaci. Například při volání GET REST, zadejte buď `suggest` nebo `autocomplete` v kolekci dokumentů. REST, po vytvoření modulu pro návrhy použijte [návrhy API](https://docs.microsoft.com/rest/api/searchservice/suggestions) nebo [rozhraní API pro automatické dokončování](https://docs.microsoft.com/rest/api/searchservice/autocomplete) v logice dotazu.

Pro platformu .NET, použijte [SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet) nebo [AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet).

Příklad demonstrace obou požadavků, najdete v části [příklad pro přidání automatické dokončování a návrhy ve službě Azure Search](search-autocomplete-tutorial.md).

## <a name="sample-code"></a>Ukázka kódu

[DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) obsahuje ukázkové C# a kód v Javě a ukazuje vytváření modulu pro návrhy, navrhované dotazy, automatické dokončování a omezující vlastnost navigace. 

Používá izolovaného prostoru služby Azure Search a předem načtené index tak všechny nemusíte je stisknutím klávesy F5 spusťte ho. Žádné předplatné nebo si zaregistrovat nezbytné.

## <a name="next-steps"></a>Další postup

Doporučujeme, abyste podle následujícího příkladu lze naleznete v tématu Jak formulovali požadavky.

> [!div class="nextstepaction"]
> [Návrhy a automatické dokončování ve více příkladů](search-autocomplete-tutorial.md) 
