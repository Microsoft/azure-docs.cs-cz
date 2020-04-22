---
title: Vytvoření navrhovatele
titleSuffix: Azure Cognitive Search
description: Povolte akce dotazů s dopředem v Azure Cognitive Search vytvořením návrhy a formulováním požadavků, které vyvolávají automatické dokončování nebo automaticky navrhované termíny dotazů.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 7eb2988628d60fa72c7d83b81a58a1e0fae5de33
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770099"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggested-results-in-a-query"></a>Vytvoření návrhu umožňujícího automatické dokončování a navrhované výsledky v dotazu

V Azure Cognitive Search je "hledání podle typu" povoleno prostřednictvím **návrhu** konstrukce přidané do [indexu vyhledávání](search-what-is-an-index.md). Návrhovač podporuje dvě možnosti: *automatické dokončování*, které dokončí částečný vstup pro celý termín dotazu a *návrhy,* které vyzve proklikat na konkrétní shodu. Automatické dokončování vytvoří dotaz. Návrhy vytvářejí odpovídající dokument.

Následující snímek obrazovky z [vytvoření první aplikace v C#](tutorial-csharp-type-ahead-and-suggestions.md) ilustruje obojí. Automatické dokončování předvídá potenciální termín, dokončení "tw" s "in". Návrhy jsou mini výsledky vyhledávání, kde pole, jako je název hotelu, představuje odpovídající dokument pro vyhledávání hotelů z indexu. U návrhů můžete pole, které poskytuje popisné informace, pole.

![Vizuální porovnání automatických dokončování a navrhovaných dotazů](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Vizuální porovnání automatických dokončování a navrhovaných dotazů")

Tyto funkce můžete používat samostatně nebo společně. Chcete-li implementovat tato chování v Azure Cognitive Search, je index a dotaz součást. 

+ V indexu přidejte návrhového objektu do indexu. Můžete použít portál, [rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)nebo [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). Zbývající část tohoto článku je zaměřena na vytvoření návrhu.

+ V žádosti o dotaz volejte jeden z [níže uvedených api](#how-to-use-a-suggester).

Podpora typu Hledání podle počtu můžete povolit pro pole pro pole typu. Můžete implementovat obě chování typeahead v rámci stejného řešení vyhledávání, pokud chcete prostředí podobné tomu, které je uvedeno na snímku obrazovky. Oba požadavky cílí na *kolekci dokumentů* konkrétní ho indexu a odpovědi jsou vráceny poté, co uživatel poskytl alespoň vstupní řetězec o třech znacích.

## <a name="what-is-a-suggester"></a>Co je to sugester?

Návrhovač je interní datová struktura, která podporuje chování typu hledání podle chování uložením předpon pro párování s částečnými dotazy. Stejně jako u tokenizovaných termínů jsou předpony uloženy v invertovaných indexech, jedno pro každé pole zadané v kolekci pole návrhu.

## <a name="define-a-suggester"></a>Definování návrhu

Chcete-li vytvořit návrhovač, přidejte jeden do schématu [indexu](https://docs.microsoft.com/rest/api/searchservice/create-index) a [nastavte každou vlastnost](#property-reference). Nejlepší čas k vytvoření návrhu je, když také definujete pole, které jej bude používat.

+ Použít pouze pole řetězce

+ Použijte výchozí standardní analyzátor Lucene (`"analyzer": null`) nebo [analyzátor jazyka](index-add-language-analyzers.md) `"analyzer": "en.Microsoft"`(například) v poli

### <a name="choose-fields"></a>Výběr polí

Přestože návrhmá používá několik vlastností, je to především kolekce řetězcových polí, pro které povolujete prostředí typu hledání podle typu. Pro každý index existuje jeden návrhovač, takže seznam návrhovačů musí obsahovat všechna pole, která přispívají obsahem pro návrhy i automatické dokončování.

Automatické dokončování těží z většího fondu polí, ze kterých lze čerpat, protože další obsah má větší potenciál dokončení termínu.

Návrhy, na druhé straně, produkují lepší výsledky, když vaše pole volba je selektivní. Nezapomeňte, že návrh je proxy pro vyhledávací dokument, takže budete chtít pole, která nejlépe představují jeden výsledek. Názvy, názvy nebo jiná jedinečná pole, která rozlišují mezi více shodami, fungují nejlépe. Pokud se pole skládají z opakujících se hodnot, návrhy se skládají ze stejných výsledků a uživatel nebude vědět, na který z nich kliknout.

Chcete-li uspokojit obě prostředí hledání podle typu, přidejte všechna pole, která potřebujete pro automatické dokončování, ale potom použijte **$select**, **$top**, **$filter**a **searchFields** k řízení výsledků návrhů.

### <a name="choose-analyzers"></a>Výběr analyzátorů

Volba analyzátoru určuje, jak jsou pole tokenizována a následně předpona. Například pro řetězec s pomlčkou, jako je "kontextově citlivý", použití analyzátoru jazyka bude mít za následek tyto kombinace tokenů: "kontext", "citlivé", "kontextově citlivé". Pokud byste použili standardní analyzátor Lucene, řetězec s pomlčkou by neexistoval. 

Při vyhodnocování analyzátorů zvažte použití [rozhraní Analyzovat textové rozhraní API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) pro přehled o tom, jak jsou termíny tokenizovány a následně předponou. Po vytvoření indexu můžete vyzkoušet různé analyzátory na řetězci pro zobrazení výstupu tokenu.

Pole, která používají [vlastní analyzátory](index-add-custom-analyzers.md) nebo [předdefinované analyzátory](index-add-custom-analyzers.md#predefined-analyzers-reference) (s výjimkou standardnílucene) jsou explicitně zakázány, aby se zabránilo špatné výsledky.

> [!NOTE]
> Pokud potřebujete obejít omezení analyzátoru, například pokud potřebujete klíčové slovo nebo ngram analyzátor pro určité scénáře dotazu, měli byste použít dvě samostatná pole pro stejný obsah. To umožní jedno z polí mít návrh, zatímco druhé lze nastavit s vlastní konfigurací analyzátoru.

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

Doporučujeme následující článek se dozvíte více o tom, jak žádosti formulace.

> [!div class="nextstepaction"]
> [Přidání automatického dokončování a návrhů do kódu klienta](search-autocomplete-tutorial.md) 
