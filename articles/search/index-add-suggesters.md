---
title: Vytvoření navrhovatele
titleSuffix: Azure Cognitive Search
description: Umožňuje v Azure Kognitivní hledání povolit akce dotazování typu dopředu tím, že vytvoří moduly pro návrhy a formuluje požadavky, které vyvolávají automatické dokončování nebo návrhy dotazů.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/24/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 748ad9fdab781ba03135f026ab846099fe50c51f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604402"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggested-results-in-a-query"></a>Vytvoření modulu pro návrhy umožňující automatické dokončování a navrhované výsledky v dotazu

V Azure Kognitivní hledání je vyhledávání "Search-as-Type" povolené prostřednictvím nástroje pro *návrhy*. Modul pro návrhy je interní datová struktura, která se skládá z kolekce polí. Pole se dotýkají dodatečného tokenizace, generování posloupnosti předpon pro podporu shody na částečných výrazech.

Pokud například modul pro návrhy obsahuje pole City, budou pro termín "Seattle" vytvořena Výsledná kombinace předpony "moře", "sedadlo", "pracovní stanice" a "seattl". Předpony jsou uloženy v obrácených indexech, jedno pro každé pole zadané v kolekci polí modulu pro návrhy.

## <a name="typeahead-experiences-in-cognitive-search"></a>Typeahead prostředí v Kognitivní hledání

Modul pro návrhy podporuje dvě prostředí: *Automatické dokončování*, které dokončuje částečný vstup pro celý dotaz na určitý termín, a *návrhy* , které pozvaní na kliknutí do konkrétní shody. Automatické dokončování vytvoří dotaz. Návrhy vytvoří vyhovující dokument.

Následující snímek obrazovky z části [Vytvoření první aplikace v jazyce C#](tutorial-csharp-type-ahead-and-suggestions.md) ilustruje obě. Automatické dokončování předpokládá potenciální termín a dokončuje "TW" s "in". Návrhy jsou zkrácené výsledky hledání, kde pole jako název hotelu představuje odpovídající dokument hledání hotelu z indexu. V případě návrhů můžete Surface libovolného pole, které poskytuje popisné informace.

![Vizuální porovnání automatického dokončování a navrhovaných dotazů](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Vizuální porovnání automatického dokončování a navrhovaných dotazů")

Tyto funkce můžete použít samostatně nebo dohromady. K implementaci těchto chování ve službě Azure Kognitivní hledání je k dispozici komponenta index a dotaz. 

+ Přidejte do definice indexu hledání modul pro návrh. Zbývající část tohoto článku se zaměřuje na vytvoření modulu pro návrhy.

+ Pomocí některého z [rozhraní API uvedených níže](#how-to-use-a-suggester)zavolejte na dotaz s povoleným návrhem, ve formě žádosti o návrh nebo žádosti o automatické dokončování.

Podpora vyhledávání podle typu je povolena pro každé pole pro pole řetězců. Typeahead chování můžete implementovat v rámci stejného řešení hledání, pokud chcete podobné prostředí, jaké je uvedené na snímku obrazovky. Oba požadavky cílí na to, že kolekce *dokumentů* určitého indexu a odpovědí se vrátí poté, co uživatel zadá alespoň tři vstupní řetězce znaků.

## <a name="how-to-create-a-suggester"></a>Jak vytvořit modul pro návrhy

Pokud chcete vytvořit návrh, přidejte ho do [definice indexu](/rest/api/searchservice/create-index). Modul pro návrhy Získá název a kolekci polí, přes které je povoleno prostředí typeahead. a [nastavte každou vlastnost](#property-reference). Nejlepším časem, jak vytvořit modul pro navrhování, je, že definujete pole, které ho bude používat.

+ Použijte pouze pole řetězců.

+ Pokud je pole řetězce součástí komplexního typu (například pole město v rámci adresy), zahrňte nadřazený prvek do pole: `"Address/City"` (Rest a C# a Python) nebo `["Address"]["City"]` (JavaScript).

+ Použijte výchozí standardní analyzátor Lucene ( `"analyzer": null` ) nebo [analyzátor jazyka](index-add-language-analyzers.md) (například `"analyzer": "en.Microsoft"` ) v poli.

Pokud se pokusíte vytvořit modul pro návrh pomocí již existujících polí, rozhraní API ho zakáže. Při indexování jsou generovány předpony, pokud jsou částečné výrazy ve dvou nebo více kombinacích znaků spolu s celými čísly. Vzhledem k tomu, že existující pole již mají tokeny, bude nutné index znovu sestavit, pokud je chcete přidat do nástroje pro návrhy. Další informace najdete v tématu [Postup opětovného sestavení indexu služby Azure kognitivní hledání](search-howto-reindex.md).

### <a name="choose-fields"></a>Zvolit pole

I když má modul pro návrhy několik vlastností, je primárně kolekce polí řetězců, pro které povolujete vyhledávání s možností vyhledávání. Pro každý index je k dispozici jeden modul pro návrhy, takže seznam modulu pro návrhy musí zahrnovat všechna pole, která přispívají k obsahu pro návrhy i automatické dokončování.

Automatické dokončování přináší výhody většího fondu polí, ze kterých se má vykreslit, protože další obsah má větší potenciál na dokončení.

Na druhé straně návrhy poskytují lepší výsledky, pokud je volba pole vybraná. Mějte na paměti, že návrh je proxy pro dokument hledání, takže budete chtít, aby pole, která nejlépe reprezentují jeden výsledek. Názvy, názvy nebo jiná jedinečná pole, která rozlišují mezi více shod, fungují nejlépe. Pokud se pole skládají z opakujících se hodnot, návrhy se skládají z identických výsledků a uživatel nebude znát, který z nich se má kliknout.

Aby bylo možné vyhovět vyhledávání výsledků hledání, přidejte všechna pole, která potřebujete pro automatické dokončování, ale pak použijte **$Select**, **$Top**, **$Filter** a **searchFields** k řízení výsledků návrhů.

### <a name="choose-analyzers"></a>Zvolit analyzátory

Volba analyzátoru určuje, jak jsou pole s tokeny a následně předem opravena. Například u rozděleného řetězce, jako je "kontextově závislé", bude použití analyzátoru jazyka mít za následek tyto kombinace tokenů: "Context", "citlivé", "kontextově závislé". Používali jste standardní nástroj Lucene Analyzer, řetězec s pomlčkou neexistoval. 

Při vyhodnocování analyzátorů zvažte použití [rozhraní analyzovat text API](/rest/api/searchservice/test-analyzer) , kde najdete informace o tom, jak jsou výrazy zpracovávány. Po sestavení indexu můžete vyzkoušet různé analyzátory na řetězci a zobrazit tak výstup tokenu.

Pole, která používají [vlastní analyzátory](index-add-custom-analyzers.md) nebo [předdefinované analyzátory](index-add-custom-analyzers.md#built-in-analyzers) (s výjimkou standardního Lucene), jsou výslovně zakázána, aby nedocházelo k špatným výsledkům.

> [!NOTE]
> Pokud potřebujete obejít omezení analyzátoru, například pokud potřebujete klíčové slovo nebo ngram Analyzer pro určité scénáře dotazování, měli byste pro stejný obsah použít dvě samostatná pole. Tím umožníte, aby jedno z polí mělo možnost navrhovat, zatímco druhá je možné nastavit pomocí vlastní konfigurace analyzátoru.

## <a name="create-using-the-portal"></a>Vytvoření pomocí portálu

Při použití možnosti **Přidat index** nebo průvodce **importem dat** k vytvoření indexu máte možnost povolit modul pro návrhy:

1. Do definice indexu zadejte název modulu pro návrhy.

1. V každé definici pole pro nová pole zaškrtněte políčko ve sloupci navrhnout. Zaškrtávací políčko je k dispozici pouze v polích řetězců. 

Jak bylo uvedeno dříve, volba analyzátoru ovlivňuje tokenizace a předpony. Při povolování návrhů Vezměte v úvahu celou definici polí. 

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

V jazyce C# definujte [objekt SearchSuggester](/dotnet/api/azure.search.documents.indexes.models.searchsuggester). `Suggesters` je kolekce na objektu SearchIndex, ale může mít pouze jednu položku. Přidejte do definice indexu modul pro návrhy.

```csharp
private static void CreateIndex(string indexName, SearchIndexClient indexClient)
{
    FieldBuilder fieldBuilder = new FieldBuilder();
    var searchFields = fieldBuilder.Build(typeof(Hotel));

    var definition = new SearchIndex(indexName, searchFields);

    var suggester = new SearchSuggester("sg", new[] { "HotelName", "Category", "Address/City", "Address/StateProvince" });
    definition.Suggesters.Add(suggester);

    indexClient.CreateOrUpdateIndex(definition);
}
```

## <a name="property-reference"></a>Odkaz na vlastnost

|Vlastnost      |Popis      |
|--------------|-----------------|
|`name`        | Zadáno v definici modulu pro návrhy, ale také voláno na žádost o automatické dokončování nebo návrhy. |
|`sourceFields`| Zadáno v definici modulu pro návrhy. Je to seznam jednoho nebo více polí v indexu, který je zdrojem obsahu pro návrhy. Pole musí být typu `Edm.String` a `Collection(Edm.String)` . Je-li v poli analyzátor určen, musí se jednat o pojmenovaný lexikální analyzátor z [tohoto seznamu](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzername) (nikoli vlastního analyzátoru).<p/> Osvědčeným postupem je zadat pouze ta pole, která samy zapůjčuje očekávanou a odpovídající odpověď, ať už se jedná o dokončený řetězec na panelu hledání nebo v rozevíracím seznamu.<p/>Název hotelu je dobrý kandidát, protože má přesnost. Podrobná pole, jako jsou popisy a komentáře, jsou moc zhuštěná. Podobně opakující se pole, jako jsou kategorie a značky, jsou méně efektivní. V příkladech obsahuje "Category", abyste ukázali, že můžete zahrnout více polí. |
|`searchMode`  | Parametr pouze pro REST, ale také viditelný na portálu. Tento parametr není v sadě .NET SDK k dispozici. Označuje strategii, pomocí které se hledají fráze kandidáta. Jediným aktuálně podporovaným režimem je `analyzingInfixMatching` , který aktuálně odpovídá začátku období.|

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester"></a>Použití modulu pro návrhy

V dotazu se používá modul pro návrhy. Po vytvoření modulu pro vytváření výsledků volejte jedno z následujících rozhraní API pro vyhledávání, jako je například:

+ [REST API návrhů](/rest/api/searchservice/suggestions)
+ [REST API automatického dokončování](/rest/api/searchservice/autocomplete)
+ [Metoda SuggestAsync](/dotnet/api/azure.search.documents.searchclient.suggestasync)
+ [Metoda AutocompleteAsync](/dotnet/api/azure.search.documents.searchclient.autocompleteasync)

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

+ [Vytvoření první aplikace v jazyce C# (lekce 3 – přidání výsledků hledání podle vašeho typu)](tutorial-csharp-type-ahead-and-suggestions.md) ukázka navrhovaných dotazů, automatického dokončování a vymezené navigace. Tato ukázka kódu běží na službě izolovaného prostoru (sandbox) Azure Kognitivní hledání a používá předem načtený index hotelů s již vytvořeným nástrojem pro navrhování, takže stačí stisknout klávesu F5 ke spuštění aplikace. Není nutné žádné předplatné ani přihlášení.

## <a name="next-steps"></a>Další kroky

Pro další informace o způsobu formulování požadavků doporučujeme následující článek.

> [!div class="nextstepaction"]
> [Přidání automatického dokončování a návrhů do klientského kódu](search-autocomplete-tutorial.md)