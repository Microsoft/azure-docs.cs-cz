---
title: Vytvoření navrhovatele
titleSuffix: Azure Cognitive Search
description: Umožňuje v Azure Kognitivní hledání povolit akce dotazování typu dopředu tím, že vytvoří moduly pro návrhy a formuluje požadavky, které vyvolávají automatické dokončování nebo návrhy dotazů.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/21/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: e439f7d2b0232a2e1c36517f24723e4e16f7e6bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91537595"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggested-results-in-a-query"></a>Vytvoření modulu pro návrhy umožňující automatické dokončování a navrhované výsledky v dotazu

V Azure Kognitivní hledání je vyhledávání "Search-as-Type" povolené prostřednictvím konstrukce **návrhového** modulu přidaného do [indexu vyhledávání](search-what-is-an-index.md). Modul pro návrhy podporuje dvě prostředí: *Automatické dokončování*, které dokončuje částečný vstup pro celý dotaz, a *návrhy* , které vás přejdou na konkrétní shodu. Automatické dokončování vytvoří dotaz. Návrhy vytvoří vyhovující dokument.

Následující snímek obrazovky z části [Vytvoření první aplikace v jazyce C#](tutorial-csharp-type-ahead-and-suggestions.md) ilustruje obě. Automatické dokončování předpokládá potenciální termín a dokončuje "TW" s "in". Návrhy jsou zkrácené výsledky hledání, kde pole jako název hotelu představuje odpovídající dokument hledání hotelu z indexu. V případě návrhů můžete Surface libovolného pole, které poskytuje popisné informace.

![Vizuální porovnání automatického dokončování a navrhovaných dotazů](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Vizuální porovnání automatického dokončování a navrhovaných dotazů")

Tyto funkce můžete použít samostatně nebo dohromady. K implementaci těchto chování ve službě Azure Kognitivní hledání je k dispozici komponenta index a dotaz. 

+ V indexu přidejte k indexu modul pro návrhy. Můžete použít portál, [REST API](/rest/api/searchservice/create-index)nebo [.NET SDK](/dotnet/api/microsoft.azure.search.models.suggester). Zbývající část tohoto článku se zaměřuje na vytvoření modulu pro návrhy.

+ V požadavku na dotaz volejte jedno z [rozhraní API uvedených níže](#how-to-use-a-suggester).

Podpora vyhledávání podle typu je povolena pro každé pole pro pole řetězců. Typeahead chování můžete implementovat v rámci stejného řešení hledání, pokud chcete podobné prostředí, jaké je uvedené na snímku obrazovky. Oba požadavky cílí na to, že kolekce *dokumentů* určitého indexu a odpovědí se vrátí poté, co uživatel zadá alespoň tři vstupní řetězce znaků.

## <a name="what-is-a-suggester"></a>Co je to modul pro návrhy?

Modul pro návrhy je interní datová struktura, která podporuje chování vyhledávání podle typu, protože ukládá předpony pro porovnání u částečných dotazů. Stejně jako u výrazů s tokeny jsou předpony uloženy v obrácených indexech, jedno pro každé pole zadané v kolekci polí pro návrhy.

## <a name="define-a-suggester"></a>Definování modulu pro návrhy

Chcete-li vytvořit modul pro návrh, přidejte ho do [schématu indexu](/rest/api/searchservice/create-index) a [nastavte jednotlivé vlastnosti](#property-reference). Nejlepším časem, jak vytvořit modul pro navrhování, je, že definujete pole, které ho bude používat.

+ Použít pouze pole řetězců

+ Použijte výchozí standardní analyzátor Lucene ( `"analyzer": null` ) nebo [analyzátor jazyka](index-add-language-analyzers.md) (například `"analyzer": "en.Microsoft"` ) v poli.

### <a name="choose-fields"></a>Zvolit pole

I když má modul pro návrhy několik vlastností, je primárně kolekce polí řetězců, pro které povolujete vyhledávání s možností vyhledávání. Pro každý index je k dispozici jeden modul pro návrhy, takže seznam modulu pro návrhy musí zahrnovat všechna pole, která přispívají k obsahu pro návrhy i automatické dokončování.

Automatické dokončování přináší výhody většího fondu polí, ze kterých se má vykreslit, protože další obsah má větší potenciál na dokončení.

Na druhé straně návrhy poskytují lepší výsledky, pokud je volba pole vybraná. Mějte na paměti, že návrh je proxy pro dokument hledání, takže budete chtít, aby pole, která nejlépe reprezentují jeden výsledek. Názvy, názvy nebo jiná jedinečná pole, která rozlišují mezi více shod, fungují nejlépe. Pokud se pole skládají z opakujících se hodnot, návrhy se skládají z identických výsledků a uživatel nebude znát, který z nich se má kliknout.

Aby bylo možné vyhovět vyhledávání výsledků hledání, přidejte všechna pole, která potřebujete pro automatické dokončování, ale pak použijte **$Select**, **$Top**, **$Filter**a **searchFields** k řízení výsledků návrhů.

### <a name="choose-analyzers"></a>Zvolit analyzátory

Volba analyzátoru určuje, jak jsou pole s tokeny a následně předem opravena. Například u rozděleného řetězce, jako je "kontextově závislé", bude použití analyzátoru jazyka mít za následek tyto kombinace tokenů: "Context", "citlivé", "kontextově závislé". Používali jste standardní nástroj Lucene Analyzer, řetězec s pomlčkou neexistoval. 

Při vyhodnocování analyzátorů zvažte použití [rozhraní analyzovat text API](/rest/api/searchservice/test-analyzer) pro přehled o tom, jak jsou výrazy vyhodnoceny a následně předem opraveny. Po sestavení indexu můžete vyzkoušet různé analyzátory na řetězci a zobrazit tak výstup tokenu.

Pole, která používají [vlastní analyzátory](index-add-custom-analyzers.md) nebo [předdefinované analyzátory](index-add-custom-analyzers.md#predefined-analyzers-reference) (s výjimkou standardního Lucene), jsou výslovně zakázána, aby nedocházelo k špatným výsledkům.

> [!NOTE]
> Pokud potřebujete obejít omezení analyzátoru, například pokud potřebujete klíčové slovo nebo ngram Analyzer pro určité scénáře dotazování, měli byste pro stejný obsah použít dvě samostatná pole. Tím umožníte, aby jedno z polí mělo možnost navrhovat, zatímco druhá je možné nastavit pomocí vlastní konfigurace analyzátoru.

### <a name="when-to-create-a-suggester"></a>Kdy vytvořit modul pro návrhy

Nejlepším časem, jak vytvořit modul pro navrhování, je, že vytváříte také vlastní definici pole.

Pokud se pokusíte vytvořit modul pro návrh pomocí již existujících polí, rozhraní API ho zakáže. Při indexování jsou generovány předpony, pokud jsou částečné výrazy ve dvou nebo více kombinacích znaků spolu s celými čísly. Vzhledem k tomu, že existující pole již mají tokeny, bude nutné index znovu sestavit, pokud je chcete přidat do nástroje pro návrhy. Další informace najdete v tématu [Postup opětovného sestavení indexu služby Azure kognitivní hledání](search-howto-reindex.md).

## <a name="create-using-rest"></a>Vytvoření pomocí REST

V REST API přidejte moduly pro návrhy prostřednictvím [Create index](/rest/api/searchservice/create-index) nebo [Update index](/rest/api/searchservice/update-index). 

  ```json
  {
    "name": "hotels-sample-index",
    "fields": [
      . . .
          {
              "name": "HotelName",
              "type": "Edm.String",
              "facetable": false,
              "filterable": false,
              "key": false,
              "retrievable": true,
              "searchable": true,
              "sortable": false,
              "analyzer": "en.microsoft",
              "indexAnalyzer": null,
              "searchAnalyzer": null,
              "synonymMaps": [],
              "fields": []
          },
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["HotelName"]
      }
    ],
    "scoringProfiles": [
      . . .
    ]
  }
  ```

## <a name="create-using-net"></a>Vytvoření pomocí .NET

V jazyce C# Definujte objekt nástroje pro [návrhy](/dotnet/api/microsoft.azure.search.models.suggester). `Suggesters` je kolekce, ale může mít pouze jednu položku. 

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels-sample-index",
        Fields = FieldBuilder.BuildForType<Hotel>(),
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelName", "Category" }
            }}
    };

    serviceClient.Indexes.Create(definition);

}
```

## <a name="property-reference"></a>Odkaz na vlastnost

|Vlastnost      |Popis      |
|--------------|-----------------|
|`name`        |Název modulu pro návrhy.|
|`searchMode`  |Strategie použitá pro hledání kandidátských frází. Jediným aktuálně podporovaným režimem je `analyzingInfixMatching` , který aktuálně odpovídá začátku období.|
|`sourceFields`|Seznam jednoho nebo více polí, která jsou zdrojem obsahu pro návrhy. Pole musí být typu `Edm.String` a `Collection(Edm.String)` . Je-li v poli analyzátor určen, musí se jednat o pojmenovaný analyzátor z [tohoto seznamu](/dotnet/api/microsoft.azure.search.models.analyzername) (nikoli vlastní analyzátor).<p/> Osvědčeným postupem je zadat pouze ta pole, která samy zapůjčuje očekávanou a odpovídající odpověď, ať už se jedná o dokončený řetězec na panelu hledání nebo v rozevíracím seznamu.<p/>Název hotelu je dobrý kandidát, protože má přesnost. Podrobná pole, jako jsou popisy a komentáře, jsou moc zhuštěná. Podobně opakující se pole, jako jsou kategorie a značky, jsou méně efektivní. V příkladech obsahuje "Category", abyste ukázali, že můžete zahrnout více polí. |

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester"></a>Použití modulu pro návrhy

V dotazu se používá modul pro návrhy. Po vytvoření modulu pro vytváření výsledků volejte jedno z následujících rozhraní API pro vyhledávání, jako je například:

+ [REST API návrhů](/rest/api/searchservice/suggestions) 
+ [REST API automatického dokončování](/rest/api/searchservice/autocomplete) 
+ [Metoda SuggestWithHttpMessagesAsync] (/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?
+ [Metoda AutocompleteWithHttpMessagesAsync](/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync)

V aplikaci vyhledávání by měl klientský kód využít knihovnu, jako je například [Automatické dokončování uživatelského rozhraní jQuery](https://jqueryui.com/autocomplete/) , ke shromáždění částečného dotazu a zadání shody. Další informace o této úloze najdete v tématu [Přidání automatického dokončování nebo navrhovaných výsledků do klientského kódu](search-autocomplete-tutorial.md).

Použití rozhraní API je znázorněno v následujícím volání REST API automatického dokončování. Existují dva poznatky z tohoto příkladu. Jako první, stejně jako u všech dotazů, se operace týká kolekce dokumentů indexu a dotaz obsahuje parametr **hledání** , který v tomto případě poskytuje částečný dotaz. Za druhé musíte do žádosti přidat **suggesterName** . Pokud není v indexu definován modul pro návrh, volání automatického dokončování nebo návrhů se nezdaří.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2020-06-30
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

## <a name="sample-code"></a>Ukázka kódu

+ [Vytvoření první aplikace v jazyce C# (lekce 3 – přidání výsledků hledání podle vašeho typu)](tutorial-csharp-type-ahead-and-suggestions.md) ukázka konstrukce, navrhovaných dotazů, automatického dokončování a vymezené navigace pro návrhy. Tato ukázka kódu běží na službě izolovaného prostoru Azure Kognitivní hledání a používá předem načtený index hotelů, takže ke spuštění aplikace stačí stisknout klávesu F5. Není nutné žádné předplatné ani přihlášení.

+ [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) je starší ukázka obsahující kód C# i Java. Ukazuje také vytváření návrhů, navrhované dotazy, automatické dokončování a omezující navigaci. Tato ukázka kódu používá ukázková data hostovaného [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) . 

## <a name="next-steps"></a>Další kroky

Pro další informace o způsobu formulování požadavků doporučujeme následující článek.

> [!div class="nextstepaction"]
> [Přidání automatického dokončování a návrhů do klientského kódu](search-autocomplete-tutorial.md)