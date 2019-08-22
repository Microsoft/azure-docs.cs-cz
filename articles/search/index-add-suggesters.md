---
title: Přidání dotazů typeahead do indexu – Azure Search
description: Umožňuje v Azure Search povolit akce dotazování typu dopředu vytvořením modulu pro návrhy a formulací požadavků, které vyvolávají automatické dokončování nebo návrhy dotazů.
ms.date: 05/02/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
manager: nitinme
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
ms.openlocfilehash: 73cfdb6a4185689a6485f55a4f6bdd1e7e3b14be
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648840"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-search"></a>Přidání návrhů do indexu pro typeahead v Azure Search

Modul pro **návrhy** je konstrukcí v [Azure Search indexu](search-what-is-an-index.md) , který podporuje možnosti hledání typu "vyhledávání jako". Obsahuje seznam polí, pro které chcete povolit vstupy dotazů typeahead. Stejný modul pro návrhy podporuje v rámci indexu buď jednu z těchto dvou typeahead variant, nebo obojí: *Automatické dokončování* dokončuje výraz nebo frázi, kterou píšete, *návrhy* poskytují krátký seznam výsledků. 

Následující snímek obrazovky ilustruje obě funkce typeahead. Na této stránce vyhledávání Xbox se položky automatického dokončování převezmou na novou stránku s výsledky hledání pro daný dotaz, zatímco návrhy jsou skutečné výsledky, které vás zajímají na stránku této konkrétní hry. Automatické dokončování můžete omezit na jednu položku na panelu hledání nebo uvést seznam, jako je zde zobrazený. Pro návrhy můžete vytvořit plochu dokumentu, který nejlépe popisuje výsledek.

![Vizuální porovnání automatického dokončování a navrhovaných dotazů](./media/index-add-suggesters/visual-comparison-suggest-complete.png "Vizuální porovnání automatického dokončování a navrhovaných dotazů")

K implementaci tohoto chování v Azure Search existuje index a komponenta pro dotazy. 

+ Součást indexu je modul pro návrhy. K vytvoření nástroje pro vytváření návrhů můžete použít portál, REST API nebo .NET SDK. 

+ Součást dotazu je akce zadaná u žádosti o dotaz (návrh nebo akce automatického dokončování). 

Podpora vyhledávání podle typu je povolena pro každé pole. Typeahead chování můžete implementovat v rámci stejného řešení hledání, pokud chcete podobné prostředí, jaké je uvedené na snímku obrazovky. Oba požadavky cílí na to, že kolekce *dokumentů* určitého indexu a odpovědí se vrátí poté, co uživatel zadá alespoň tři vstupní řetězce znaků.

## <a name="create-a-suggester"></a>Vytvoření navrhovatele

I když má modul pro návrhy několik vlastností, je primárně kolekcí polí, pro které povolujete typeahead prostředí. Například cestovní aplikace může chtít povolit typeahead vyhledávání na cílech, městech a attractions. V takovém případě by všechna tři pole přešla do kolekce Fields.

Pokud chcete vytvořit návrh, přidejte ho do schématu indexu. V indexu můžete mít jeden modul pro návrh (konkrétně jeden modul pro návrhy v kolekci modul pro návrhy). 

### <a name="use-the-rest-api"></a>Použití rozhraní REST API

V REST API můžete přidat moduly pro návrhy prostřednictvím [Create index](https://docs.microsoft.com/rest/api/searchservice/create-index) nebo [Update index](https://docs.microsoft.com/rest/api/searchservice/update-index). 

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
Po vytvoření modulu pro návrhy přidejte rozhraní [API návrhů](https://docs.microsoft.com/rest/api/searchservice/suggestions) nebo [rozhraní API pro automatické dokončování](https://docs.microsoft.com/rest/api/searchservice/autocomplete) do logiky dotazu pro vyvolání funkce.

### <a name="use-the-net-sdk"></a>Použití sady .NET SDK

V C#Definujte objekt nástroje pro [návrhy](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). `Suggesters`je kolekce, ale může mít pouze jednu položku. 

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

Klíčovými body, které se týkají modulu pro návrhy, je, že existuje název (pro návrhy se odkazuje podle názvu na žádosti), searchMode (aktuálně jen jedna, "analyzingInfixMatching") a seznam polí, pro která je povolená typeahead. 

Mezi vlastnosti definující modul pro návrhy patří následující:

|Vlastnost      |Popis      |
|--------------|-----------------|
|`name`        |Název modulu pro návrhy. Při volání [návrhů REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) nebo [automatického dokončování REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete)použijete název modulu pro návrhy.|
|`searchMode`  |Strategie použitá pro hledání kandidátských frází. Jediným aktuálně podporovaným režimem `analyzingInfixMatching`je, který provádí flexibilní spárování frází na začátku nebo uprostřed vět.|
|`sourceFields`|Seznam jednoho nebo více polí, která jsou zdrojem obsahu pro návrhy. Pouze pole typu `Edm.String` a `Collection(Edm.String)` mohou být zdrojem návrhů. Použít lze pouze pole, která nemají sadu vlastní jazykové analyzátory.<p/>Zadejte jenom ta pole, která se sami zahodí k očekávané a příslušné reakci, ať už se jedná o dokončený řetězec na panelu hledání nebo v rozevíracím seznamu.<p/>Název hotelu je dobrý kandidát, protože má přesnost. Podrobná pole, jako jsou popisy a komentáře, jsou moc zhuštěná. Podobně opakující se pole, jako jsou kategorie a značky, jsou méně efektivní. V příkladech obsahuje "Category", abyste ukázali, že můžete zahrnout více polí. |

#### <a name="analysis-of-sourcefields-in-a-suggester"></a>Analýza SourceFields v modulu pro návrhy

Azure Search analyzuje obsah pole a povoluje dotazování na jednotlivé výrazy. Moduly pro návrhy vyžadují, aby byly předpony indexovány Kromě úplných podmínek, které vyžadují další analýzu nad zdrojovými poli. Vlastní konfigurace analyzátoru můžou kombinovat kterýkoli z různých tokenizátory musíte nejdřív a filtrů, často způsobem, který by vytvořil předpony vyžadované pro návrhy, které neumožňují. Z tohoto důvodu **Azure Search zabránit zahrnutí polí s vlastními analyzátory do**modulu pro návrhy.

> [!NOTE] 
>  Doporučený postup pro vyřešení výše uvedeného omezení je použití dvou samostatných polí pro stejný obsah. Tím umožníte, aby jedno z polí měl moduly pro návrhy a druhý bylo možné nastavit s vlastní konfigurací analyzátoru.

## <a name="when-to-create-a-suggester"></a>Kdy vytvořit modul pro návrhy

Chcete-li se vyhnout opakovanému sestavení indexu, je třeba vytvořit modul pro `sourceFields` návrhy a pole určená v nástroji.

Pokud přidáte modul pro návrhy do existujícího indexu, kde jsou obsažena existující pole, definice `sourceFields`pole se v podstatě změní a vyžaduje se opětovné sestavení. Další informace najdete v tématu [Postup opětovného sestavení indexu Azure Search](search-howto-reindex.md).

## <a name="how-to-use-a-suggester"></a>Jak používat modul pro návrhy

Jak bylo uvedeno dříve, můžete použít modul pro návrhy dotazů, automatického dokončování nebo obojího. 

Na žádost je odkazováno v žádosti spolu s operací. Například v případě volání Get REST zadejte buď `suggest` nebo `autocomplete` v kolekci dokumentů. V případě, že je vytvořen modul pro REST, použijte rozhraní [API návrhů](https://docs.microsoft.com/rest/api/searchservice/suggestions) nebo [rozhraní API pro automatické dokončování](https://docs.microsoft.com/rest/api/searchservice/autocomplete) ve vaší logice dotazu.

V případě .NET použijte [SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet) nebo [AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet).

Příklad demonstrující obě požadavky najdete v tématu [Příklad přidání automatického dokončování a návrhů v Azure Search](search-autocomplete-tutorial.md).

## <a name="sample-code"></a>Ukázka kódu

Ukázka [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) obsahuje kód v C# jazyce Java a předvádí vytváření návrhů, navrhované dotazy, automatické dokončování a navigaci omezující vlastnosti. 

Používá službu Azure Search sandboxu a předem načtený index, takže ke spuštění stačí stisknout klávesu F5. Není nutné žádné předplatné ani přihlášení.

## <a name="next-steps"></a>Další postup

Pro zobrazení způsobu formulace požadavků doporučujeme následující příklad.

> [!div class="nextstepaction"]
> [Příklady návrhů a automatického dokončování](search-autocomplete-tutorial.md) 
