---
title: Přidání automatického dokončování do vyhledávacího pole
titleSuffix: Azure Cognitive Search
description: Povolte v Azure Kognitivní hledání akce dotazování typu hledání jako, které umožňují navrhovat a formulují žádosti, které automaticky vyplní vyhledávací pole s dokončenými podmínkami nebo frázemi. Můžete také vrátit navrhované shody.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/24/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 6bc45bb6eec9dbf46e039dd1e2c32197820bb09d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626703"
---
# <a name="add-autocomplete-and-suggestions-to-client-apps-using-azure-cognitive-search"></a>Přidání automatického dokončování a návrhů do klientských aplikací s využitím Azure Kognitivní hledání

Hledání jako typ je běžná technika pro zlepšení produktivity dotazů. V Azure Kognitivní hledání se toto prostředí podporuje prostřednictvím *automatického dokončování*, které dokončuje termín nebo frázi na základě částečného vstupu ("mikro" s "Microsoft"). Druhý uživatel je *návrhem* nebo krátký seznam odpovídajících dokumentů (s ID, který vrací názvy knih s ID, takže můžete propojit stránku s podrobnostmi o dané knize). Automatické dokončování i návrhy jsou u shody v indexu predikátem. Služba nebude nabízet dotazy, které vracejí žádné výsledky.

K implementaci těchto prostředí v Azure Kognitivní hledání budete potřebovat:

+ Definice *návrhového* nástroje, která je vložena do schématu indexu.
+ *Dotaz* určující rozhraní API pro [Automatické dokončování](/rest/api/searchservice/autocomplete) nebo [návrhy](/rest/api/searchservice/suggestions) v žádosti.
+ *Ovládací prvek uživatelského rozhraní* , který v klientské aplikaci zpracovává interakce typu hledání podle vás. Pro tento účel doporučujeme použít existující knihovnu JavaScriptu.

V Azure Kognitivní hledání jsou automatické dokončováné dotazy a navrhované výsledky načteny z indexu hledání z vybraných polí, která jste zaregistrovali v modulu pro návrhy. Modul pro návrhy je součástí indexu a určuje, která pole budou poskytovat obsah, který buď dokončí dotaz, navrhne výsledek nebo provede obojí. Když se index vytvoří a načte, vytvoří se interně struktura dat modulu pro ukládání předpon používaných pro porovnání částečných dotazů. V případě návrhů zvolte vhodná pole, která jsou jedinečná nebo alespoň neopakující, je základem pro prostředí. Další informace najdete v tématu [Vytvoření](index-add-suggesters.md)modulu pro návrhy.

Zbývající část tohoto článku se zaměřuje na dotazy a kód klienta. K ilustraci klíčových bodů používá JavaScript a C#. K stručnému zobrazení jednotlivých operací se používají příklady REST API. Odkazy na komplexní ukázky kódu najdete v části [Další kroky](#next-steps).

## <a name="set-up-a-request"></a>Nastavení žádosti

Mezi prvky požadavku patří jedno z rozhraní API pro hledání podle typu, částečný dotaz a modul pro návrhy. Následující skript ilustruje komponenty požadavku pomocí automatického dokončování REST API jako příklad.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2020-06-30
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

"SuggesterName" poskytuje pole, která používají modul pro navrhování, která slouží k dokončení podmínek nebo návrhů. Pro konkrétní návrhy se seznam polí musí skládat z těch, které nabízí jasné možnosti výběru mezi odpovídajícími výsledky. V lokalitě, která prodává počítačové hry, může být polem název hry.

Parametr Search poskytuje částečný dotaz, kde jsou znaky podávány do žádosti dotazu prostřednictvím ovládacího prvku jQuery AutoComplete. Ve výše uvedeném příkladu je "minecraf" statická ilustrace toho, co ovládací prvek mohl předávat.

Rozhraní API neukládají požadavky na minimální délku na částečný dotaz. může to být jen jeden znak. Automatické dokončování jQuery ale poskytuje minimální délku. Typický je minimálně dva nebo tři znaky.

Shody jsou na začátku termínu kdekoli ve vstupním řetězci. V případě "rychlé hnědého Fox" se obě funkce automatického dokončování i návrhy shodují na částečných verzích "The", "Quick", "Brown" nebo "Fox", ale ne na částečných vponych výrazech, jako je například "řádku" nebo "Ox". Kromě toho každá shoda nastaví obor pro rozšíření pro příjem dat. Částečný dotaz "Rychlá BR" se bude shodovat s "rychlým hnědá" nebo "rychlým chlebem", ale ani "hnědá" nebo "chléb" by se shodovali s tím, že před nimi "Rychlá".

### <a name="apis-for-search-as-you-type"></a>Rozhraní API pro hledání podle vašeho typu

Pro referenční stránky REST a .NET SDK použijte tyto odkazy:

+ [REST API návrhů](/rest/api/searchservice/suggestions) 
+ [REST API automatického dokončování](/rest/api/searchservice/autocomplete) 
+ [Metoda SuggestAsync](/dotnet/api/azure.search.documents.searchclient.suggestasync)
+ [Metoda AutocompleteAsync](/dotnet/api/azure.search.documents.searchclient.autocompleteasync)

## <a name="structure-a-response"></a>Strukturování odpovědi

Odpovědi na automatické dokončování a návrhy jsou to, co byste pro vzor mohli očekávat: [funkce automatického dokončování](/rest/api/searchservice/autocomplete#response) vrátí seznam podmínek, [návrhy](/rest/api/searchservice/suggestions#response) vrátí výrazy a ID dokumentu, abyste mohli načíst dokument (pomocí rozhraní API pro [vyhledání dokumentu](/rest/api/searchservice/lookup-document) načíst konkrétní dokument pro stránku s podrobnostmi).

Odpovědi jsou ve tvaru podle parametrů na žádosti:

+ Pro automatické dokončování nastavte [autocompleteMode](/rest/api/searchservice/autocomplete#query-parameters) , abyste zjistili, jestli se při dokončování textu vyskytuje jedna nebo dvě slova. 

+ V případě návrhů nastavte [$Select](/rest/api/searchservice/suggestionse#query-parameters) , aby vracela pole obsahující jedinečné nebo rozdílné hodnoty, jako jsou názvy a popis. Vyhněte se polím, která obsahují duplicitní hodnoty (například kategorie nebo City).

Následující další parametry se vztahují na automatické dokončování i návrhy, ale mohou být vhodnější pro návrhy, zejména v případě, že modul pro návrh obsahuje více polí.

| Parametr | Využití |
|-----------|-------|
| searchFields | Omezí dotaz na konkrétní pole. |
| $filter | Použijte kritéria shody pro sadu výsledků dotazu ( `$filter=Category eq 'ActionAdventure'` ). |
| $top | Omezí výsledky na konkrétní číslo ( `$top=5` ).|

## <a name="add-user-interaction-code"></a>Přidat kód interakce uživatele

Automatické vyplňování dotazovacího termínu nebo vyřazení seznamu vyhovujících odkazů vyžaduje kód interakce s uživatelem, obvykle JavaScript, který může spotřebovávat žádosti z externích zdrojů, jako jsou například automatické dokončování nebo návrhy dotazů na Azure Searchho indexu rozpoznávání.

I když byste tento kód mohli nativně psát, je mnohem jednodušší použít funkce z existující knihovny JavaScriptu, například z následujících. 

+ Ve fragmentu kódu návrhu se zobrazí [widget automatického dokončování (rozhraní jQuery)](https://jqueryui.com/autocomplete/) . Můžete vytvořit vyhledávací pole a pak na něj odkazovat ve funkci JavaScriptu, která používá widget automatického dokončování. Vlastnosti pomůcky nastaví zdroj (funkci automatického dokončování nebo návrhů), minimální délku vstupních znaků před provedením akce a umístění.

+ Ve fragmentu kódu automatického dokončování se zobrazí [modul plug-in XDSoft AutoComplete](https://xdsoft.net/jqplugins/autocomplete/) .

+ [návrhy](https://www.npmjs.com/package/suggestions) se zobrazí v [kurzu JavaScript](tutorial-javascript-overview.md) a v ukázce kódu.

Pomocí těchto knihoven v klientovi můžete vytvořit vyhledávací pole, které podporuje návrhy i automatické dokončování. Vstupy shromážděné do vyhledávacího pole se pak dají spárovat s návrhy a akcemi automatického dokončování v rámci vyhledávací služby.

## <a name="suggestions"></a>Návrhy

V této části se seznámíte s implementací navrhovaných výsledků od definice vyhledávacího pole. Ukazuje také, jak a skript vyvolá první knihovnu automatického dokončování JavaScriptu, na kterou odkazuje tento článek.

### <a name="create-a-search-box"></a>Vytvoření vyhledávacího pole

Za předpokladu, že [Knihovna automatického dokončování uživatelského rozhraní jQuery](https://jqueryui.com/autocomplete/) a projekt MVC v jazyce C#, můžete definovat vyhledávací pole pomocí JavaScriptu v souboru **index. cshtml** . Knihovna přidá do vyhledávacího pole interakci prohledat jako typ tak, že provede asynchronní volání kontroleru MVC k načtení návrhů.

V **indexu. cshtml** ve složce \Views\Home může být čára pro vytvoření vyhledávacího pole následující:

```html
<input class="searchBox" type="text" id="searchbox1" placeholder="search">
```

V tomto příkladu je jednoduché vstupní textové pole s třídou pro stylování, na ID, na které se odkazuje pomocí JavaScriptu, a zástupný text.  

V rámci stejného souboru vložte JavaScript, který odkazuje na vyhledávací pole. Následující funkce volá rozhraní API pro návrhy, které vyžádá navrhovaný dokument podle dílčích vstupů:

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

Obsahuje `source` informace o funkci automatického dokončování uživatelského rozhraní jQuery, kde získat seznam položek, které se mají zobrazit v poli hledání. Vzhledem k tomu, že tento projekt je projekt MVC, volá funkci **navrhnout** v **HomeController. cs** , která obsahuje logiku pro vracení návrhů dotazů. Tato funkce také předá několik parametrů pro řízení světel, přibližné spárování a termínu. Rozhraní JavaScript API pro automatické dokončování přidá parametr term.

Tím `minLength: 3` zajistíte, aby se doporučení zobrazovala pouze v případě, že pole hledání obsahuje alespoň tři znaky.

### <a name="enable-fuzzy-matching"></a>Povolit přibližné porovnání

Vyhledávání přibližných shod umožňuje získat výsledky na základě blízké shody i v případě, že uživatel ve vyhledávacím poli udělá chybu ve slově. Vzdálenost úprav je 1, což znamená, že mezi uživatelským vstupem a shodou může být maximálně jeden znak. 

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

### <a name="enable-highlighting"></a>Povolit zvýrazňování

Zvýrazňování aplikuje styl písma na znaky ve výsledku, které odpovídají vstupu. Pokud je například částečný vstup "mikro", výsledek by se **zobrazil jako** mikroměkký, **mikroskop Scope a** tak dále. Zvýrazňování je založeno na parametrech HighlightPreTag a HighlightPostTag, který je definovaný jako vložený s funkcí návrh.

```javascript
source: "/home/suggest?highlights=true&fuzzy=true&",
```

### <a name="suggest-function"></a>Navrhnout funkci

Pokud používáte jazyk C# a aplikaci MVC, v adresáři Controllers se nachází soubor **HomeController. cs** , kde můžete vytvořit třídu pro navrhované výsledky. V rozhraní .NET je funkce navrhovat založená na [metodě SuggestAsync](/dotnet/api/azure.search.documents.searchclient.suggestasync). Další informace o sadě .NET SDK najdete v tématu [Jak používat Azure kognitivní hledání z aplikace .NET](search-howto-dotnet-sdk.md).

`InitSearch`Metoda vytvoří ověřeného klienta http indexu pro službu Azure kognitivní hledání. Vlastnosti třídy [SuggestOptions](/dotnet/api/azure.search.documents.suggestoptions) určují, která pole se prohledávají a vrátí ve výsledcích, počet shod a zda se používá přibližná shoda. 

Pro automatické dokončování je přibližné porovnání omezené na jednu vzdálenost úprav (jeden vynechán nebo nesprávně umístěn znak). Všimněte si, že Přibližná shoda v dotazech automatického dokončování může někdy způsobit neočekávané výsledky v závislosti na velikosti indexu a způsobu jeho horizontálně dělené. Další informace najdete v tématu [dělení a horizontálního dělení koncepty](search-capacity-planning.md#concepts-search-units-replicas-partitions-shards).

```csharp
public async Task<ActionResult> SuggestAsync(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    var options = new SuggestOptions()
    {
        UseFuzzyMatching = fuzzy,
        Size = 8,
    };

    if (highlights)
    {
        options.HighlightPreTag = "<b>";
        options.HighlightPostTag = "</b>";
    }

    // Only one suggester can be specified per index.
    // The suggester for the Hotels index enables autocomplete/suggestions on the HotelName field only.
    // During indexing, HotelNames are indexed in patterns that support autocomplete and suggested results.
    var suggestResult = await _searchClient.SuggestAsync<Hotel>(term, "sg", options).ConfigureAwait(false);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = suggestResult.Value.Results.Select(x => x.Text).ToList();

    // Return the list of suggestions.
    return new JsonResult(suggestions);
}
```

Funkce SuggestAsync přebírá dva parametry, které určují, zda jsou vraceny nejzajímavější výsledky nebo se neshoduje s tím, že se kromě hledaného výrazu používá přibližná shoda. Do navrhovaných výsledků lze zahrnout až osm shod. Metoda vytvoří [objekt SuggestOptions](/dotnet/api/azure.search.documents.suggestoptions), který je poté předán rozhraní API navrhnout. Výsledek se pak převede do formátu JSON, aby ho bylo možné zobrazit v klientovi.

## <a name="autocomplete"></a>Automatické dokončování

V tomto případě byl kód uživatelského rozhraní hledání na základě návrhů na střed. Další blok kódu zobrazuje automatické dokončování pomocí funkce automatického dokončování uživatelského rozhraní XDSoft jQuery, která předá požadavek na automatické dokončování Azure Kognitivní hledání. Stejně jako u návrhů v aplikaci jazyka C#, kód, který podporuje interakci s uživatelem, přechází do **indexu. cshtml**.

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.8 https://xdsoft.net/jqplugins/autocomplete/
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

### <a name="autocomplete-function"></a>Funkce AutoComplete

Automatické dokončování je založené na [metodě AutocompleteAsync](/dotnet/api/azure.search.documents.searchclient.autocompleteasync). Stejně jako u návrhů by tento blok kódu měl přejít do souboru **HomeController. cs** .

```csharp
public async Task<ActionResult> AutoCompleteAsync(string term)
{
    InitSearch();

    // Setup the autocomplete parameters.
    var ap = new AutocompleteOptions()
    {
        Mode = AutocompleteMode.OneTermWithContext,
        Size = 6
    };
    var autocompleteResult = await _searchClient.AutocompleteAsync(term, "sg", ap).ConfigureAwait(false);

    // Convert the autocompleteResult results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Value.Results.Select(x => x.Text).ToList();

    return new JsonResult(autocomplete);
}
```

Funkce automatického dokončování přijímá vstup hledaného termínu. Metoda vytvoří [objekt AutoCompleteParameters](/rest/api/searchservice/autocomplete). Výsledek se pak převede do formátu JSON, aby ho bylo možné zobrazit v klientovi.

## <a name="next-steps"></a>Další kroky

Pomocí těchto odkazů můžete najít ucelené pokyny nebo kód, který demonstruje prostředí hledání s možností vyhledávání. Ukázka demonstruje hybridní implementaci návrhů a automatického dokončování.

+ [Přidání vyhledávání na web (JavaScript):](tutorial-javascript-search-query-integration.md#azure-function-suggestions-from-the-catalog) používá otevřený zdrojový balíček návrhů pro částečné dokončování v klientské aplikaci.
+ [Kurz: Vytvoření první aplikace v jazyce c# (lekce 3)](tutorial-csharp-type-ahead-and-suggestions.md) s přiřazenou  [ukázkou kódu c#: Azure-Search-dotnet-Samples/Create-First-App/3-Add-typeahead/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10/3-add-typeahead) předvedení nativní podpory pro typeahead.