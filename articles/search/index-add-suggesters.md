---
title: Přidání dotazů typeahead do indexu
titleSuffix: Azure Cognitive Search
description: Umožňuje v Azure Kognitivní hledání povolit akce dotazování typu dopředu tím, že vytvoří moduly pro návrhy a formuluje požadavky, které vyvolávají automatické dokončování nebo návrhy dotazů.
manager: nitinme
author: Brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
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
ms.openlocfilehash: a312068d5c8c574e7b069263cf37e3b855810e4b
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790106"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-cognitive-search"></a>Přidání návrhů k indexu pro typeahead v Azure Kognitivní hledání

V Azure Kognitivní hledání jsou funkce hledání typu Search-as-Type nebo typeahead založené na konstruktu **návrhu** , který přidáte do [indexu vyhledávání](search-what-is-an-index.md). Je to seznam jednoho nebo více polí, pro které chcete povolit typeahead.

Modul pro návrhy podporuje dvě typeahead varianty: *Automatické dokončování*, které dokončuje výraz nebo frázi, kterou píšete, a *návrhy* , které vracejí krátký seznam vyhovujících dokumentů.  

Následující snímek obrazovky, z části [Vytvoření první aplikace v C# ](tutorial-csharp-type-ahead-and-suggestions.md) ukázce, ilustruje typeahead. Funkce automatického dokončování předpokládá, co uživatel může zadat do vyhledávacího pole. Skutečný vstup je "TW", který dokončí automatické dokončování s "in", přičemž se jako termín hledání potenciálního vyhledávání překládá jako "". Návrhy jsou vizuálně v rozevíracím seznamu. Pro návrhy můžete vytvořit plochu dokumentu, který nejlépe popisuje výsledek. V tomto příkladu jsou návrhy názvy hotelů. 

![Vizuální porovnání automatického dokončování a navrhovaných dotazů](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Vizuální porovnání automatického dokončování a navrhovaných dotazů")

K implementaci těchto chování ve službě Azure Kognitivní hledání je k dispozici komponenta index a dotaz. 

+ V indexu přidejte k indexu modul pro návrhy. Můžete použít portál, [REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)nebo [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). Zbývající část tohoto článku se zaměřuje na vytvoření modulu pro návrhy. 

+ V požadavku na dotaz volejte jedno z [rozhraní API uvedených níže](#how-to-use-a-suggester).

Podpora vyhledávání podle typu je povolena pro každé pole. Typeahead chování můžete implementovat v rámci stejného řešení hledání, pokud chcete podobné prostředí, jaké je uvedené na snímku obrazovky. Oba požadavky cílí na to, že kolekce *dokumentů* určitého indexu a odpovědí se vrátí poté, co uživatel zadá alespoň tři vstupní řetězce znaků.

## <a name="create-a-suggester"></a>Vytvoření navrhovatele

I když má modul pro návrhy několik vlastností, je primárně kolekcí polí, pro které povolujete typeahead prostředí. Například cestovní aplikace může chtít povolit typeahead vyhledávání na cílech, městech a attractions. V takovém případě by všechna tři pole přešla do kolekce Fields.

Pokud chcete vytvořit návrh, přidejte ho do schématu indexu. V indexu můžete mít jeden modul pro návrh (konkrétně jeden modul pro návrhy v kolekci modul pro návrhy). 

### <a name="when-to-create-a-suggester"></a>Kdy vytvořit modul pro návrhy

Nejlepším časem, jak vytvořit modul pro navrhování, je, že vytváříte také vlastní definici pole.

Pokud se pokusíte vytvořit modul pro návrh pomocí již existujících polí, rozhraní API ho zakáže. Typeahead text se vytvoří během indexování, pokud jsou částečné výrazy ve dvou nebo více kombinacích znaků spolu s celými čísly. Vzhledem k tomu, že existující pole již mají tokeny, bude nutné index znovu sestavit, pokud je chcete přidat do nástroje pro návrhy. Další informace o reindexaci najdete v tématu [Postup opětovného sestavení indexu služby Azure kognitivní hledání](search-howto-reindex.md).

### <a name="create-using-the-rest-api"></a>Vytvoření pomocí REST API

V REST API přidejte moduly pro návrhy prostřednictvím [Create index](https://docs.microsoft.com/rest/api/searchservice/create-index) nebo [Update index](https://docs.microsoft.com/rest/api/searchservice/update-index). 

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


### <a name="create-using-the-net-sdk"></a>Vytvoření pomocí sady .NET SDK

V C#Definujte objekt nástroje pro [návrhy](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). `Suggesters` je kolekce, ale může provést pouze jednu položku. 

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

### <a name="property-reference"></a>Odkaz na vlastnost

|Vlastnost      |Popis      |
|--------------|-----------------|
|`name`        |Název modulu pro návrhy.|
|`searchMode`  |Strategie použitá pro hledání kandidátských frází. Jediným aktuálně podporovaným režimem je `analyzingInfixMatching`, který provádí flexibilní spárování frází na začátku nebo uprostřed vět.|
|`sourceFields`|Seznam jednoho nebo více polí, která jsou zdrojem obsahu pro návrhy. Pole musí být typu `Edm.String` a `Collection(Edm.String)`. Je-li v poli analyzátor určen, musí se jednat o pojmenovaný analyzátor z [tohoto seznamu](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) (nikoli vlastní analyzátor).<p/>Osvědčeným postupem je zadat pouze ta pole, která samy zapůjčuje očekávanou a odpovídající odpověď, ať už se jedná o dokončený řetězec na panelu hledání nebo v rozevíracím seznamu.<p/>Název hotelu je dobrý kandidát, protože má přesnost. Podrobná pole, jako jsou popisy a komentáře, jsou moc zhuštěná. Podobně opakující se pole, jako jsou kategorie a značky, jsou méně efektivní. V příkladech obsahuje "Category", abyste ukázali, že můžete zahrnout více polí. |

### <a name="analyzer-restrictions-for-sourcefields-in-a-suggester"></a>Omezení analyzátoru pro sourceFields v modulu pro návrh

Azure Kognitivní hledání analyzuje obsah pole a povoluje dotazování na jednotlivé výrazy. Moduly pro návrhy vyžadují, aby byly předpony indexovány Kromě úplných podmínek, které vyžadují další analýzu nad zdrojovými poli. Vlastní konfigurace analyzátoru můžou kombinovat kterýkoli z různých tokenizátory musíte nejdřív a filtrů, často způsobem, který by vytvořil předpony vyžadované pro návrhy, které neumožňují. Z tohoto důvodu Azure Kognitivní hledání zabraňuje zahrnutí polí s vlastními analyzátory do modulu pro návrhy.

> [!NOTE] 
>  Pokud potřebujete obejít výše uvedené omezení, použijte pro stejný obsah dvě samostatná pole. Tím umožníte, aby jedno z polí mělo možnost navrhovat, zatímco druhá je možné nastavit pomocí vlastní konfigurace analyzátoru.

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester-in-a-query"></a>Použití modulu pro návrhy v dotazu

Po vytvoření nástroje pro vytváření návrhů zavolejte příslušné rozhraní API v logice dotazu pro vyvolání funkce. 

+ [REST API návrhů](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [REST API automatického dokončování](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [Metoda SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [Metoda AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

Použití rozhraní API je znázorněno v následujícím volání REST API automatického dokončování. Existují dva poznatky z tohoto příkladu. Jako první se jako u všech dotazů jedná o operaci proti kolekci dokumentů indexu. Za druhé můžete přidat parametry dotazu. I když je mnoho parametrů dotazu společné pro obě rozhraní API, seznam se u každého z nich liší.

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs/autocomplete?[query parameters]  
api-key: [admin or query key]
```

Pokud není v indexu definován modul pro návrh, volání automatického dokončování nebo návrhů se nezdaří.

## <a name="sample-code"></a>Ukázka kódu

+ [Vytvoření první aplikace v C# ](tutorial-csharp-type-ahead-and-suggestions.md) ukázce znázorňuje konstrukci návrhů, navrhované dotazy, automatické dokončování a omezující navigaci. Tato ukázka kódu běží na službě izolovaného prostoru Azure Kognitivní hledání a používá předem načtený index hotelů, takže ke spuštění aplikace stačí stisknout klávesu F5. Není nutné žádné předplatné ani přihlášení.

+ [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) je starší vzorek, který obsahuje C# i kód Java. Ukazuje také vytváření návrhů, navrhované dotazy, automatické dokončování a omezující navigaci. Tato ukázka kódu používá ukázková data hostovaného [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) . 


## <a name="next-steps"></a>Další kroky

Pro zobrazení způsobu formulace požadavků doporučujeme následující příklad.

> [!div class="nextstepaction"]
> [Příklady návrhů a automatického dokončování](search-autocomplete-tutorial.md) 
