---
title: Vytvoření navrhovatele
titleSuffix: Azure Cognitive Search
description: Povolte akce dotazů s dopředem v Azure Cognitive Search vytvořením návrhy a formulováním požadavků, které vyvolávají automatické dokončování nebo automaticky navrhované termíny dotazů.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 1e2a837acef976b6b872c2d4002ee49d662ad594
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641330"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggested-results-in-a-query"></a>Vytvoření návrhu umožňujícího automatické dokončování a navrhované výsledky v dotazu

V Azure Cognitive Search je "hledání podle typu" povoleno prostřednictvím **návrhu** konstrukce přidané do [indexu vyhledávání](search-what-is-an-index.md). Návrhovač podporuje dvě možnosti: *automatické dokončování*, které doplňuje termín nebo *frázi, a návrhy,* které vracejí krátký seznam odpovídajících dokumentů.  

Následující snímek obrazovky z [vytvoření první aplikace v C#](tutorial-csharp-type-ahead-and-suggestions.md) ilustruje obojí. Automatické dokončování předvídá potenciální termín, dokončení "tw" s "in". Návrhy jsou mini výsledky vyhledávání, kde pole, jako je název hotelu, představuje odpovídající dokument pro vyhledávání hotelů z indexu. U návrhů můžete pole, které poskytuje popisné informace, pole.

![Vizuální porovnání automatických dokončování a navrhovaných dotazů](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Vizuální porovnání automatických dokončování a navrhovaných dotazů")

Tyto funkce můžete používat samostatně nebo společně. Chcete-li implementovat tato chování v Azure Cognitive Search, je index a dotaz součást. 

+ V indexu přidejte návrhového objektu do indexu. Můžete použít portál, [rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)nebo [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). Zbývající část tohoto článku je zaměřena na vytvoření návrhu.

+ V žádosti o dotaz volejte jeden z [níže uvedených api](#how-to-use-a-suggester).

Podpora typu Hledání podle počtu můžete povolit pro pole pro pole typu. Můžete implementovat obě chování typeahead v rámci stejného řešení vyhledávání, pokud chcete prostředí podobné tomu, které je uvedeno na snímku obrazovky. Oba požadavky cílí na *kolekci dokumentů* konkrétní ho indexu a odpovědi jsou vráceny poté, co uživatel poskytl alespoň vstupní řetězec o třech znacích.

## <a name="what-is-a-suggester"></a>Co je to sugester?

Návrhovač je datová struktura, která podporuje chování typu hledání podle chování uložením předpon pro párování s částečnými dotazy. Podobně jako tokenizované termíny jsou předpony uloženy v invertovaných indexech, jedno pro každé pole zadané v kolekci pole návrhu.

Při vytváření předpon má návrhovač svůj vlastní řetězec analýzy, podobný řetězci používanému pro fulltextové vyhledávání. Na rozdíl od analýzy v fulltextovém vyhledávání však může návrhátor pracovat pouze přes pole, která používají standardní analyzátor Lucene (výchozí) nebo [analyzátor jazyka](index-add-language-analyzers.md). Pole, která používají [vlastní analyzátory](index-add-custom-analyzers.md) nebo [předdefinované analyzátory](index-add-custom-analyzers.md#predefined-analyzers-reference) (s výjimkou standardnílucene) jsou explicitně zakázány, aby se zabránilo špatné výsledky.

> [!NOTE]
> Pokud potřebujete obejít omezení analyzátoru, použijte pro stejný obsah dvě samostatná pole. To umožní jedno z polí mít návrh, zatímco druhé lze nastavit s vlastní konfigurací analyzátoru.

## <a name="define-a-suggester"></a>Definování návrhu

Chcete-li vytvořit návrhovač, přidejte jeden do schématu [indexu](https://docs.microsoft.com/rest/api/searchservice/create-index) a [nastavte každou vlastnost](#property-reference). V indexu můžete mít jeden návrhový (konkrétně jeden návrhový v kolekci suggesterů). Nejlepší čas k vytvoření návrhu je, když také definujete pole, které jej bude používat.

### <a name="choose-fields"></a>Výběr polí

Přestože návrhmác má několik vlastností, je především kolekce polí, pro které povolujete vyhledávání jako typ prostředí. Pro návrhy zejména zvolte pole, která nejlépe představují jeden výsledek. Názvy, názvy nebo jiná jedinečná pole, která rozlišují mezi více shodami, fungují nejlépe. Pokud se pole skládají z opakujících se hodnot, návrhy se skládají ze stejných výsledků a uživatel nebude vědět, na který z nich kliknout.

Ujistěte se, že každé pole používá analyzátor, který provádí lexikální analýzu během indexování. Můžete použít buď výchozí standardní analyzátor`"analyzer": null`Lucene ( ) nebo `"analyzer": "en.Microsoft"` [analyzátor jazyka](index-add-language-analyzers.md) (například). 

Volba analyzátoru určuje, jak jsou pole tokenizována a následně předpona. Například pro řetězec s pomlčkou, jako je "kontextově citlivý", použití analyzátoru jazyka bude mít za následek tyto kombinace tokenů: "kontext", "citlivé", "kontextově citlivé". Pokud byste použili standardní analyzátor Lucene, řetězec s pomlčkou by neexistoval.

> [!TIP]
> Zvažte použití [analyzovat textové rozhraní API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) pro přehled o tom, jak jsou termíny tokenizovány a následně předponou. Jakmile vytvoříte index, můžete vyzkoušet různé analyzátory na řetězci a zobrazit tokeny, které vydává.

### <a name="when-to-create-a-suggester"></a>Kdy vytvořit návrhovače

Nejlepší čas k vytvoření návrhu je, když také vytváříte samotnou definici pole.

Pokud se pokusíte vytvořit návrhovačpomocí již existujících polí, rozhraní API jej zakáže. Předpony jsou generovány během indexování, když částečné termíny ve dvou nebo více kombinací znaků jsou tokenizovány vedle celé termíny. Vzhledem k tomu, že existující pole jsou již tokenizována, budete muset znovu vytvořit index, pokud je chcete přidat do návrhu. Další informace najdete v [tématu Jak znovu sestavit index Azure cognitive search](search-howto-reindex.md).

## <a name="create-using-rest"></a>Vytvořit pomocí REST

V rozhraní REST API přidejte návrhy pomocí [vytvořit index](https://docs.microsoft.com/rest/api/searchservice/create-index) nebo [aktualizovat index](https://docs.microsoft.com/rest/api/searchservice/update-index). 

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

## <a name="create-using-net"></a>Vytvořit pomocí rozhraní .NET

V c# definujte [objekt Susič](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). `Suggesters`je kolekce, ale může trvat pouze jednu položku. 

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
|`name`        |Jméno návrhače.|
|`searchMode`  |Strategie používaná k vyhledávání frází kandidátů. Jediný aktuálně podporovaný režim `analyzingInfixMatching`je , který se aktuálně shoduje na začátku termínu.|
|`sourceFields`|Seznam jednoho nebo více polí, která jsou zdrojem obsahu návrhů. Pole musí být `Edm.String` `Collection(Edm.String)`typu a . Pokud je v poli zadán analyzátor, musí se jednat o pojmenovaný analyzátor z [tohoto seznamu](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) (nikoli vlastní analyzátor).<p/> Jako osvědčený postup zadejte pouze pole, která se hodí k očekávané a vhodné odpovědi, ať už se jedná o dokončený řetězec v řádku hledání nebo v rozevíracím seznamu.<p/>Název hotelu je dobrým kandidátem, protože má přesnost. Podrobná pole, jako jsou popisy a komentáře, jsou příliš hustá. Podobně jsou méně účinná opakující se pole, například kategorie a značky. V příkladech uvádíme "kategorie", abychom ukázali, že můžete zahrnout více polí. |

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester"></a>Použití návrhu

Návrhový dotaz se používá v dotazu. Po vytvoření návrhu volejte jeden z následujících api pro vyhledávání jako typ prostředí:

+ [Návrhy rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [Automatické dokončování rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [Metoda SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [Metoda automatického dokončováníWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

Ve vyhledávací aplikaci by měl klientský kód využít knihovnu, jako je [automatické dokončování jQuery UI,](https://jqueryui.com/autocomplete/) ke shromažďování částečného dotazu a poskytování shody. Další informace o této úloze naleznete v [tématu Přidání automatického dokončování nebo doporučených výsledků do kódu klienta](search-autocomplete-tutorial.md).

Využití rozhraní API je znázorněno v následujícím volání rozhraní REST API automatického dokončování. Existují dva stánek s jídlem z tohoto příkladu. Za prvé, stejně jako u všech dotazů, operace je proti shromažďování dokumentů indexu a dotaz obsahuje parametr **hledání,** který v tomto případě poskytuje částečný dotaz. Za druhé je nutné přidat **suggesterName** k požadavku. Pokud návrhnení definován v indexu, volání automatického dokončování nebo návrhy se nezdaří.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2019-05-06
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

## <a name="sample-code"></a>Ukázka kódu

+ [Vytvořte si první aplikaci v C# (lekce 3 – Přidání hledání jako typ)](tutorial-csharp-type-ahead-and-suggestions.md) ukázka ukazuje návrhkonstrukce, navrhované dotazy, automatické dokončování a fazetované navigace. Tato ukázka kódu běží na izolovaném prostoru Azure Cognitive Search služby a používá předem načtené Hotels index, takže vše, co musíte udělat, je stiskněte klávesu F5 ke spuštění aplikace. Není nutné žádné předplatné ani přihlášení.

+ [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) je starší ukázka obsahující kód Jazyka C# i Java. Ukazuje také návrhkonstrukce, navrhované dotazy, automatické dokončování a fazetované navigace. Tato ukázka kódu používá hostovaná ukázková data [NYCJobs.](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) 

## <a name="next-steps"></a>Další kroky

Doporučujeme následující příklad, abyste zjistili, jak jsou formulovány požadavky.

> [!div class="nextstepaction"]
> [Přidání automatického dokončování a návrhů do kódu klienta](search-autocomplete-tutorial.md) 
