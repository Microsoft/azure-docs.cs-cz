---
title: Vytvoření navrhovatele
titleSuffix: Azure Cognitive Search
description: Povolte akce dotazů s dopředem v Azure Cognitive Search vytvořením návrhy a formulováním požadavků, které vyvolávají automatické dokončování nebo automaticky navrhované termíny dotazů.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: a6c4051a5b3d557f9ac2927a62492425e7636c0d
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113470"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggestions-in-azure-cognitive-search"></a>Vytvoření návrhu umožňujícího automatické dokončování a návrhy v Azure Cognitive Search

V Azure Cognitive Search je funkce "hledání podle typu" nebo dopředného psaní založena na konstrukci **návrhu,** kterou přidáte do [indexu vyhledávání](search-what-is-an-index.md). Návrhovač podporuje dvě varianty vyhledávání podle typu: *automatické dokončování*, které doplní zadávaný termín nebo frázi, a *návrhy,* které vracejí krátký seznam odpovídajících dokumentů.  

Následující snímek obrazovky z ukázky [Vytvořit první aplikaci v c#](tutorial-csharp-type-ahead-and-suggestions.md) ilustruje obě prostředí. Automatické dokončování předvídá, co může uživatel zadat, a dokončuje "tw" s "in" jako potenciální hledaný výraz. Návrhy jsou skutečné výsledky hledání, z nichž každý představuje odpovídající dokument. U návrhů můžete vysušovat libovolnou část dokumentu, která nejlépe vystichne výsledek. V tomto příkladu jsou návrhy reprezentovány polem název hotelu.

![Vizuální porovnání automatických dokončování a navrhovaných dotazů](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Vizuální porovnání automatických dokončování a navrhovaných dotazů")

Chcete-li implementovat tato chování v Azure Cognitive Search, je index a dotaz součást. 

+ V indexu přidejte návrhového objektu do indexu. Můžete použít portál, [rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)nebo [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). Zbývající část tohoto článku je zaměřena na vytvoření návrhu.

+ V žádosti o dotaz volejte jeden z [níže uvedených api](#how-to-use-a-suggester).

Podpora vyhledávání podle typu je povolena pro dané pole. Můžete implementovat obě chování typeahead v rámci stejného řešení vyhledávání, pokud chcete prostředí podobné tomu, které je uvedeno na snímku obrazovky. Oba požadavky cílí na *kolekci dokumentů* konkrétní ho indexu a odpovědi jsou vráceny poté, co uživatel poskytl alespoň vstupní řetězec o třech znacích.

## <a name="what-is-a-suggester"></a>Co je to sugester?

Návrhovač je datová struktura, která podporuje chování typu hledání podle chování uložením předpon pro párování s částečnými dotazy. Podobně jako tokenizované termíny jsou předpony uloženy v invertovaných indexech, jedno pro každé pole zadané v kolekci pole návrhu.

Při vytváření předpon má návrhovač svůj vlastní řetězec analýzy, podobný řetězci používanému pro fulltextové vyhledávání. Na rozdíl od analýzy v fulltextovém vyhledávání však může návrhař používat pouze předdefinované analyzátory (standardní analyzátory Lucene, [analyzátory jazyků](index-add-language-analyzers.md)nebo jiné analyzátory v [seznamu předdefinovaných analyzátorů](index-add-custom-analyzers.md#predefined-analyzers-reference). [Vlastní analyzátory](index-add-custom-analyzers.md) a konfigurace jsou výslovně zakázány, aby se zabránilo náhodné konfigurace, které poskytují špatné výsledky.

> [!NOTE]
> Pokud potřebujete obejít omezení analyzátoru, použijte pro stejný obsah dvě samostatná pole. To umožní jedno z polí mít návrh, zatímco druhé lze nastavit s vlastní konfigurací analyzátoru.

## <a name="create-a-suggester"></a>Vytvoření navrhovatele

Přestože návrhmá několik vlastností, je především kolekce polí, pro které povolujete prostředí dopředného psaní. Cestovatelská aplikace může například chtít povolit vyhledávání typu dopředného v destinacích, městech a atrakcích. Jako takový by všechna tři pole jít do kolekce polí.

Chcete-li vytvořit návrh, přidejte jeden do schématu indexu. Můžete mít jeden návrhový v indexu (konkrétně jeden suggester v suggesters kolekce).

### <a name="when-to-create-a-suggester"></a>Kdy vytvořit návrhovače

Nejlepší čas k vytvoření návrhu je, když také vytváříte samotnou definici pole.

Pokud se pokusíte vytvořit návrhovačpomocí již existujících polí, rozhraní API jej zakáže. Text dopředného textu je vytvořen během indexování, když jsou částečné termíny ve dvou nebo více kombinacích znaků tokenizovány vedle celých termínů. Vzhledem k tomu, že existující pole jsou již tokenizována, budete muset znovu vytvořit index, pokud je chcete přidat do návrhu. Další informace o reindexingu najdete v [tématu Jak znovu sestavit index Azure Cognitive Search](search-howto-reindex.md).

### <a name="create-using-the-rest-api"></a>Vytvoření pomocí rozhraní REST API

V rozhraní REST API přidejte návrhy pomocí [vytvořit index](https://docs.microsoft.com/rest/api/searchservice/create-index) nebo [aktualizovat index](https://docs.microsoft.com/rest/api/searchservice/update-index). 

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

V c# definujte [objekt Susič](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). `Suggesters`je kolekce, ale může trvat pouze jednu položku. 

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
|`name`        |Jméno návrhače.|
|`searchMode`  |Strategie používaná k vyhledávání frází kandidátů. Jediný aktuálně podporovaný režim `analyzingInfixMatching`je , který provádí flexibilní párování frází na začátku nebo uprostřed vět.|
|`sourceFields`|Seznam jednoho nebo více polí, která jsou zdrojem obsahu návrhů. Pole musí být `Edm.String` `Collection(Edm.String)`typu a . Pokud je v poli zadán analyzátor, musí se jednat o pojmenovaný analyzátor z [tohoto seznamu](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) (nikoli vlastní analyzátor).<p/>Jako osvědčený postup zadejte pouze pole, která se hodí k očekávané a vhodné odpovědi, ať už se jedná o dokončený řetězec v řádku hledání nebo v rozevíracím seznamu.<p/>Název hotelu je dobrým kandidátem, protože má přesnost. Podrobná pole, jako jsou popisy a komentáře, jsou příliš hustá. Podobně jsou méně účinná opakující se pole, například kategorie a značky. V příkladech uvádíme "kategorie", abychom ukázali, že můžete zahrnout více polí. |

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester-in-a-query"></a>Použití návrhu v dotazu

Po vytvoření návrhu volání příslušné rozhraní API v logice dotazu vyvolat funkci. 

+ [Návrhy rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [Automatické dokončování rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [Metoda SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [Metoda automatického dokončováníWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

Využití rozhraní API je znázorněno v následujícím volání rozhraní REST API automatického dokončování. Existují dva stánek s jídlem z tohoto příkladu. Za prvé, stejně jako u všech dotazů, operace je proti shromažďování dokumentů indexu. Za druhé můžete přidat parametry dotazu. Zatímco mnoho parametrů dotazu jsou společné pro obě rozhraní API, seznam se liší pro každý z nich.

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs/autocomplete?[query parameters]  
api-key: [admin or query key]
```

Pokud návrhnení definován v indexu, volání automatického dokončování nebo návrhy se nezdaří.

## <a name="sample-code"></a>Ukázka kódu

+ [Vytvořte si první aplikaci v c#](tutorial-csharp-type-ahead-and-suggestions.md) ukázce ukazuje návrh konstrukce, navrhované dotazy, automatické dokončování a fazetované navigace. Tato ukázka kódu běží na izolovaném prostoru Azure Cognitive Search služby a používá předem načtené Hotels index, takže vše, co musíte udělat, je stiskněte klávesu F5 ke spuštění aplikace. Není nutné žádné předplatné ani přihlášení.

+ [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) je starší ukázka obsahující kód Jazyka C# i Java. Ukazuje také návrhkonstrukce, navrhované dotazy, automatické dokončování a fazetované navigace. Tato ukázka kódu používá hostovaná ukázková data [NYCJobs.](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) 


## <a name="next-steps"></a>Další kroky

Doporučujeme následující příklad, abyste zjistili, jak jsou formulovány požadavky.

> [!div class="nextstepaction"]
> [Příklady návrhů a automatického dokončování](search-autocomplete-tutorial.md) 
