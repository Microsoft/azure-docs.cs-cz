---
title: Přidání automatického dokončování a návrhů do vyhledávacího pole
titleSuffix: Azure Cognitive Search
description: Povolte akce dotazů jako typ vyhledávání v Azure Cognitive Search vytvořením návrhy a formulováním požadavků, které automaticky doplní vyhledávací pole s hotovými výrazy nebo frázemi. Můžete také vrátit navrhované zápasy.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 1d8085c6056cb0d2541999c3e9c249cde3da8834
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641258"
---
# <a name="add-autocomplete-and-suggestions-to-client-apps"></a>Přidání automatického dokončování a návrhů do klientských aplikací

Hledání podle typu je běžná technika pro zlepšení produktivity dotazů iniciovaných uživatelem. V Azure Cognitive Search je toto prostředí podporováno prostřednictvím *automatického dokončování*, které dokončí termín nebo frázi na základě částečného vstupu (dokončení "micro" s "Microsoft"). Dalším formulářem jsou *návrhy*: krátký seznam odpovídajících dokumentů (vrácení titulů knihy s ID, abyste mohli vytvořit odkaz na stránku podrobností). Automatické dokončování i návrhy jsou založeny na shodě v indexu. Služba nebude nabízet dotazy, které vracejí nulové výsledky.

K implementaci těchto prostředí v Azure Cognitive Search, budete potřebovat:

+ *Návrhovač* na zadní straně.
+ *Dotaz* určující automatické dokončování nebo rozhraní API návrhů v žádosti.
+ *Ovládací prvek ui* pro zpracování interakcí jako typ vyhledávání v klientské aplikaci. Pro tento účel doporučujeme použít existující knihovnu JavaScriptu.

V Azure Cognitive Search se automaticky vyplněné dotazy a navrhované výsledky načítají z indexu vyhledávání z vybraných polí, která jste zaregistrovali u návrhovače. Návrhovač je součástí indexu a určuje, která pole budou poskytovat obsah, který buď dokončí dotaz, navrhne výsledek, nebo provede obojí. Při vytvoření a načtení indexu je vytvořena datová struktura sugesteru interně pro uložení předpon používaných pro párování s částečnými dotazy. Pro návrhy, výběr vhodných polí, které jsou jedinečné, nebo alespoň ne opakující se, je nezbytné pro zážitek. Další informace naleznete [v tématu Vytvoření návrhu](index-add-suggesters.md).

Zbývající část tohoto článku je zaměřena na dotazy a kód klienta. Používá JavaScript a C# pro ilustraci klíčových bodů. Rest API příklady se používají k stručné prezentovat každou operaci. Odkazy na ukázky kódu od konce na konec naleznete [v tématu Další kroky](#next-steps).

## <a name="set-up-a-request"></a>Nastavení požadavku

Mezi prvky požadavku patří rozhraní API ([Automatické dokončování REST](https://docs.microsoft.com/rest/api/searchservice/autocomplete) nebo [Návrh REST](https://docs.microsoft.com/rest/api/searchservice/suggestions)), částečný dotaz a návrh.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2019-05-06
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

Název **suggesteru** poskytuje pole s informacemi o návrhu, která slouží k dokončení termínů nebo návrhů. U návrhů by se měl seznam polí skládat z těch, které nabízejí jasnou volbu mezi odpovídajícími výsledky. Na webu, který prodává počítačové hry, může být pole název hry.

Parametr **search** poskytuje částečný dotaz, kde jsou znaky vloženy do požadavku na dotaz prostřednictvím ovládacího prvku automatického dokončování jQuery. Ve výše uvedeném příkladu "minecraf" je statický ilustrací co ovládací prvek může mít předány.

Api neukládají požadavky na minimální délku částečného dotazu; to může být tak malý jako jeden znak. Automatické dokončování jQuery však poskytuje minimální délku. Typické jsou minimálně dva nebo tři znaky.

Shody jsou na začátku termínu kdekoli ve vstupním řetězci. Vzhledem k tomu, "rychlé hnědé lišky", jak automatické dokončování a návrhy budou odpovídat na částečné verze "", "rychlé", "hnědé", nebo "liška", ale ne na částečné infix termíny jako "rown" nebo "vůl". Kromě toho každá shoda nastavuje prostor pro následné rozšíření. Částečný dotaz "quick br" se bude shodovat s "rychlým hnědým" nebo "rychlým chlebem", ale ani "hnědý" nebo "chléb" by se sám o sobě neshodoval, pokud jim nepředchází "rychlý".

### <a name="apis"></a>Rozhraní API

Postupujte podle těchto odkazů pro referenční stránky sady REST a .NET SDK:

+ [Návrhy rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [Automatické dokončování rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [Metoda SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [Metoda automatického dokončováníWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

## <a name="structure-a-response"></a>Strukturujte odpověď

Odpovědi na automatické dokončování a návrhy jsou to, co můžete očekávat pro vzorek: [Automatické dokončování](https://docs.microsoft.com/rest/api/searchservice/autocomplete#response) vrátí seznam termínů, [Návrhy](https://docs.microsoft.com/rest/api/searchservice/suggestions#response) vrátí termíny plus ID dokumentu, takže můžete načíst dokument (pomocí rozhraní API [vyhledávacího dokumentu](https://docs.microsoft.com/rest/api/searchservice/lookup-document) načíst konkrétní dokument pro stránku podrobností).

Odpovědi jsou utvářeny parametry na požadavku. V případě automatického [**dokončování nastavte režim automatického dokončování,**](https://docs.microsoft.com/rest/api/searchservice/autocomplete#autocomplete-modes) abyste zjistili, zda k dokončování textu dojde v jednom nebo dvou termínech. U návrhů určuje pole, které zvolíte, obsah odpovědi.

Chcete-li dále upřesnit odpověď, zahrnout další parametry na požadavek. Následující parametry platí pro automatické dokončování i návrhy.

| Parametr | Využití |
|-----------|-------|
| **$select** | Pokud máte více **zdrojových polí**, použijte **$select** `$select=GameTitle`a zvolte, které pole přispívá hodnotami ( ). |
| **$filter** | Použít kritéria shody na`$filter=ActionAdventure`sadu výsledků ( ). |
| **$top** | Omezte výsledky na`$top=5`konkrétní číslo ( ).|

## <a name="add-user-interaction-code"></a>Přidání kódu interakce uživatele

Automatické vyplnění termínu dotazu nebo zrušení seznamu odpovídajících odkazů vyžaduje kód interakce uživatele, obvykle JavaScript, který může využívat požadavky z externích zdrojů, jako jsou automatické dokončování nebo dotazy na návrhy podle indexu Azure Search Cognitive.

I když můžete napsat tento kód nativně, je mnohem jednodušší používat funkce z existující knihovny JavaScript. Tento článek ukazuje dva, jeden pro návrhy a další pro automatické dokončování. 

+ [Widget automatického dokončování (jQuery UI)](https://jqueryui.com/autocomplete/) se používá v příkladu návrhu. Můžete vytvořit vyhledávací pole a pak na něj odkazovat ve funkci JavaScriptu, která používá widget Automatické dokončování. Vlastnosti widgetu nastavují zdroj (funkce automatického dokončování nebo návrhy), minimální délku vstupních znaků před provedením akce a umístění.

+ [Modul plug-in automatického dokončování XDSoft](https://xdsoft.net/jqplugins/autocomplete/) se používá v příkladu automatického dokončování.

Tyto knihovny používáme k vytvoření vyhledávacího pole podporujícího návrhy i automatické dokončování. Vstupy shromážděné ve vyhledávacím poli jsou spárovány s návrhy a akcemi automatického dokončování.

## <a name="suggestions"></a>Návrhy

Tato část vás provede implementací navržených výsledků, počínaje definicí vyhledávacího pole. To také ukazuje, jak a skript, který vyvolá první JavaScript automatické dokončování knihovny odkazuje v tomto článku.

### <a name="create-a-search-box"></a>Vytvoření vyhledávacího pole

Za předpokladu, že [knihovna automatického dokončování jQuery UI](https://jqueryui.com/autocomplete/) a projekt MVC v jazyce C#, můžete definovat vyhledávací pole pomocí JavaScriptu v souboru **Index.cshtml.** Knihovna přidá do vyhledávacího pole interakci typu hledání asynchronním voláním řadiče MVC pro načtení návrhů.

V **souboru Index.cshtml** ve složce \Views\Home může být řádek pro vytvoření vyhledávacího pole následující:

```html
<input class="searchBox" type="text" id="searchbox1" placeholder="search">
```

Tento příklad je jednoduché vstupní textové pole s třídou pro styling, ID, na které odkazuje JavaScript, a zástupný text.  

Do stejného souboru vemte JavaScript, který odkazuje na vyhledávací pole. Následující funkce volá rozhraní Suggest API, které požaduje odpovídající dokumenty na základě částečných termínových vstupů:

```javascript
$(function () {
    $("#searchbox1").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

Říká `source` jQuery UI funkce automatického dokončování, kde získat seznam položek, které mají být uvedeny pod vyhledávacím polem. Vzhledem k tomu, že tento projekt je projekt MVC, volá **funkce Suggest** v **HomeController.cs,** která obsahuje logiku pro vrácení návrhů dotazů. Tato funkce také předá několik parametrů pro řízení zvýraznění, přibližné shody a termínu. Automatické dokončování JavaScript API přidá parametr termínu.

Zajišťuje, `minLength: 3` že doporučení budou zobrazena pouze v případě, že jsou ve vyhledávacím poli alespoň tři znaky.

### <a name="enable-fuzzy-matching"></a>Povolit přibližné shody

Vyhledávání přibližných shod umožňuje získat výsledky na základě blízké shody i v případě, že uživatel ve vyhledávacím poli udělá chybu ve slově. Vzdálenost úprav je 1, což znamená, že mezi vstupem uživatele a shodou může být maximální rozdíl jednoho znaku. 

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

### <a name="enable-highlighting"></a>Povolit zvýraznění

Zvýraznění aplikuje styl písma na znaky ve výsledku, které odpovídají vstupu. Například pokud částečný vstup je "micro", výsledek se zobrazí jako **mikro**soft, **mikro**rozsah a tak dále. Zvýraznění je založeno na parametrech HighlightPreTag a HighlightPostTag definovaných vložených s funkcí Návrh.

```javascript
source: "/home/suggest?highlights=true&fuzzy=true&",
```

### <a name="suggest-function"></a>Navrhnout funkci

Pokud používáte C# a mvc aplikace, **HomeController.cs** soubor v adresáři řadiče je místo, kde můžete vytvořit třídu pro navrhované výsledky. V rozhraní .NET je funkce Suggest založena na [metodě DocumentsOperationsExtensions.Suggest](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet).

Metoda `InitSearch` vytvoří ověřený http index klienta služby Azure Cognitive Search. Další informace o sdk .NET najdete v tématu [Jak používat Azure Cognitive Search z aplikace .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

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

    DocumentSuggestResult resp = _indexClient.Documents.Suggest(term, "sg", sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = resp.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

Funkce Suggest přebírá dva parametry, které určují, jestli se mají vracet zvýrazněné shody nebo jestli se má kromě vstupního hledaného výrazu použít i přibližná shoda. Metoda vytvoří [Objekt SuggestParameters](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet), který je pak předán suggest API. Výsledek se pak převede do formátu JSON, aby ho bylo možné zobrazit v klientovi.

## <a name="autocomplete"></a>Automatické dokončování

Zatím byl vyhledávací kód Uživatelského ux zaměřen na návrhy. Další blok kódu zobrazuje automatické dokončování pomocí funkce automatického dokončování uživatelského režimu XDSoft jQuery a předá žádost o automatické dokončování Azure Cognitive Search. Stejně jako u návrhů, v aplikaci C# kód, který podporuje interakci s uživatelem jde do **index.cshtml**.

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 https://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#searchbox1").autocompleteInline({
        appendMethod: "replace",
        source: [
            function (text, add) {
                if (!text) {
                    return;
                }

                $.getJSON("/home/autocomplete?term=" + text, function (data) {
                    if (data && data.length > 0) {
                        currentSuggestion2 = data[0];
                        add(data);
                    }
                });
            }
        ]
    });

    // complete on TAB and clear on ESC
    $("#searchbox1").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#searchbox1").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#searchbox1").val("");
        }
    });
});
```

### <a name="autocomplete-function"></a>Funkce automatického dokončování

Automatické dokončování je založeno na [metodě DocumentsOperationsExtensions.Autocomplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet). Stejně jako u návrhů by tento blok kódu šel do **HomeController.cs** souboru.

```csharp
public ActionResult AutoComplete(string term)
{
    InitSearch();
    //Call autocomplete API and return results
    AutocompleteParameters ap = new AutocompleteParameters()
    {
        AutocompleteMode = AutocompleteMode.OneTermWithContext,
        UseFuzzyMatching = false,
        Top = 5
    };
    AutocompleteResult autocompleteResult = _indexClient.Documents.Autocomplete(term, "sg", ap);

    // Convert the Suggest results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = autocomplete
    };
}
```

Funkce automatického dokončování přebírá vstup hledaného výrazu. Metoda vytvoří [objekt AutoCompleteParameters](https://docs.microsoft.com/rest/api/searchservice/autocomplete). Výsledek se pak převede do formátu JSON, aby ho bylo možné zobrazit v klientovi.

## <a name="next-steps"></a>Další kroky

Postupujte podle těchto odkazů pro koncové pokyny nebo kód, který ukazuje obě prostředí typu vyhledávání. Oba příklady kódu zahrnují hybridní implementace návrhů a automatické dokončování společně.

+ [Kurz: Vytvořte si první aplikaci v C# (lekce 3)](tutorial-csharp-type-ahead-and-suggestions.md)
+ [Ukázka kódu Jazyka C#: azure-search-dotnet-samples/create-first-app/3-add-typeahead/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/3-add-typeahead)
+ [C# a JavaScript s ukázkou kódu REST vedle sebe](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)