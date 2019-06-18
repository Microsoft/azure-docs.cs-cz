---
title: C#Kurz předvádějící automatického doplňování a návrhy – Azure Search
description: Tento kurz vychází projektu "Výsledky hledání stránkování – Azure Search" Přidání automatického doplňování a návrhy. Cílem je pohodlnější a pestřejší prostředí uživatele. Zjistěte, jak kombinovat rozevírací seznam návrhů pomocí vložených automatického doplňování.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 05/01/2019
ms.openlocfilehash: d1722d98b594c8a317fa782eab223a754fc578fe
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67166803"
---
# <a name="c-tutorial-add-autocompletion-and-suggestions---azure-search"></a>C#Kurz: Přidat automatického doplňování a návrhy – Azure Search

Zjistěte, jak implementovat automatického doplňování (našeptávání a návrhy) a uživatel spustí při psaní do vyhledávacího pole. V tomto kurzu jsme se zobrazit našeptávání výsledky a výsledky návrh samostatně a pak si ukážeme způsob kombinování vytvářet pohodlnější a pestřejší prostředí uživatele. Uživatel může mít pouze na typ dvě nebo tři klíče pro vyhledání všech výsledků, které jsou k dispozici. V tomto kurzu se vytvoří na stránkování projekt vytvořený v [ C# kurzu: Výsledky stránkování – Azure Search](tutorial-csharp-paging.md) kurzu.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Přidat návrhy
> * Přidání zvýrazňování návrhy
> * Přidat automatického doplňování
> * Kombinace automatického doplňování a návrhy

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

Máte [ C# kurzu: Výsledky stránkování – Azure Search](tutorial-csharp-paging.md) projektu rychle zprovoznit. Tento projekt může mít vlastní verzi, dokončení v předchozím kurzu, nebo ji nainstalovat z Githubu: [Vytvoření první aplikace](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="add-suggestions"></a>Přidat návrhy

Začněme nejjednodušší případ této nabídky si alternativy uživateli: rozevírací seznam návrhů.

1. V souboru index.cshtml změnit **TextBoxFor** příkazu následujícímu.

```cs
     @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautosuggest" }) <input value="" class="searchBoxSubmit" type="submit">
```

Klíč zde je, že jsme nastavili ID do vyhledávacího pole **azureautosuggest**.

2. Po tomto příkazu za uzavírací  **&lt;/div&gt;** , zadejte tento skript.

```cs
    <script>
        $("#azureautosuggest").autocomplete({
            source: "/Home/Suggest?highlights=false&fuzzy=false",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
```

Připojili jsme tento skript do vyhledávacího pole prostřednictvím stejné ID. Navíc minimálně dva znaky, je potřeba aktivovat hledání a říkáme **navrhnout** akce v kontroleru domácí se dvěma parametry dotazu: **zvýrazní** a **přibližných shod**, obě nastaveny na hodnotu false v této instanci.

### <a name="add-references-to-jquery-scripts-to-the-view"></a>Přidat odkazy na skripty jquery do zobrazení

Ve výše uvedené skriptu volaná funkce automatického dokončování není něco musíme napsat sami je k dispozici v knihovně jquery. 

1. Chcete-li získat přístup k knihovny jquery, změňte &lt;head&gt; část zobrazení souboru v následujícím kódu.

```cs
<head>
    <meta charset="utf-8">
    <title>Autocomplete demo</title>
    <link href="https://code.jquery.com/ui/1.10.4/themes/ui-lightness/jquery-ui.css"
          rel="stylesheet">
    <script src="https://code.jquery.com/jquery-1.10.2.js"></script>
    <script src="https://code.jquery.com/ui/1.10.4/jquery-ui.js"></script>
    <link rel="stylesheet" href="~/css/hotels.css" />
</head>
```

2. Musíme také odstranit, nebo okomentovat, řádek odkazující na jquery v souboru _Layout.cshtml (v **zobrazení/Shared** složky). Vyhledejte následující řádky a okomentujte první řádek skriptu, jak je znázorněno. Tato změna zabraňuje kolidující odkazy na jquery.

```cs
    <environment include="Development">
        <!-- <script src="~/lib/jquery/dist/jquery.js"></script> -->
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
```

Teď můžeme použít funkce jquery předdefinované funkce automatického dokončování.

### <a name="add-the-suggest-action-to-the-controller"></a>Přidat navrhovanou akci kontroleru

1. V domácí kontroleru, přidejte **navrhnout** akce (například po **stránky** akci).

```cs
        public async Task<ActionResult> Suggest(bool highlights, bool fuzzy, string term)
        {
            InitSearch();

            // Setup the suggest parameters.
            var parameters = new SuggestParameters()
            {
                UseFuzzyMatching = fuzzy,
                Top = 8,
            };

            if (highlights)
            {
                parameters.HighlightPreTag = "<b>";
                parameters.HighlightPostTag = "</b>";
            }

            // Only one suggester can be specified per index. The name of the suggester is set when the suggester is specified by other API calls.
            // The suggester for the hotel database is called "sg", and simply searches the hotel name.
            DocumentSuggestResult<Hotel> suggestResult = await _indexClient.Documents.SuggestAsync<Hotel>(term, "sg", parameters);

            // Convert the suggest query results to a list that can be displayed in the client.
            List<string> suggestions = suggestResult.Results.Select(x => x.Text).ToList();

            // Return the list of suggestions.
            return new JsonResult(suggestions);
        }
```

**Horní** parametr určuje, kolik výsledků k vrácení (Pokud tento parametr zadán, výchozí hodnota je 5). A _modulu pro návrhy_ je zadán v Azure indexu, které se provádí při dat je nastaveno a ne pomocí klientské aplikace, jako je například tento kurz. V takovém případě modul pro návrhy se nazývá "zabezpečení" a hledání **HotelName** pole - nic dalšího. 

Přibližné shody umožňuje "near výpadky" mají být zahrnuty ve výstupu. Pokud **zvýrazní** parametr je nastaven na hodnotu true, pak tučné značky HTML jsou přidány do výstupu. Tyto dva parametry nastavíme na hodnotu true v další části.

2. Může se zobrazit některé chyby syntaxe. Pokud ano, přidejte následující dva **pomocí** příkazy do horní části souboru.

```cs
using System.Collections.Generic;
using System.Linq;
```

3. Spusťte aplikaci. Že když například zadáte "po", získáte širokou paletu možností? Teď zkuste "pa".

    ![Zadáním "po" odhalí dvě návrhy](./media/tutorial-csharp-create-first-app/azure-search-suggest-po.png)

Všimněte si, že písmen zadáte _musí_ začátku slova a nikoli pouze být součástí slova.

4. V zobrazení skriptu, nastavte **& přibližných shod** na hodnotu true a znovu spusťte aplikaci. Teď zadejte "po". Všimněte si, že hledání předpokládá, že jste získali jedno písmeno nesprávné!
 
    ![Zadáním "pa" s fuzzy logikou nastavenou na hodnotu true](./media/tutorial-csharp-create-first-app/azure-search-suggest-fuzzy.png)

Pokud vás zajímá, [syntaxe dotazů Lucene ve službě Azure Search](https://docs.microsoft.com/azure/search/query-lucene-syntax) logikou používanou v přibližné vyhledávání podrobně popisuje.

## <a name="add-highlighting-to-the-suggestions"></a>Přidání zvýrazňování návrhy

Můžeme vylepšit vzhled návrhy uživateli o něco tak, že nastavíte **zvýrazní** parametr na hodnotu true. Ale nejprve potřebujeme přidat kód k zobrazení tučný text.

1. V zobrazení (index.cshtml), přidejte následující skript po **azureautosuggest** skript, který jste zadali výše.

```cs
    <script>
        var updateTextbox = function (event, ui) {
            var result = ui.item.value.replace(/<\/?[^>]+(>|$)/g, "");
            $("#azuresuggesthighlights").val(result);
            return false;
        };

        $("#azuresuggesthighlights").autocomplete({
            html: true,
            source: "/home/suggest?highlights=true&fuzzy=false&",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            },
            select: updateTextbox,
            focus: updateTextbox
        }).data("ui-autocomplete")._renderItem = function (ul, item) {
            return $("<li></li>")
                .data("item.autocomplete", item)
                .append("<a>" + item.label + "</a>")
                .appendTo(ul);
        };
    </script>
```

2. Nyní změníte ID do textového pole, aby vypadal takto.

```cs
@Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azuresuggesthighlights" }) <input value="" class="searchBoxSubmit" type="submit">
```

3. Znovu spusťte aplikaci a měli byste vidět vaše zadaný text tučné v návrzích. Zkuste například zadat "pa".
 
    ![Zadáním "pa" s zvýraznění](./media/tutorial-csharp-create-first-app/azure-search-suggest-highlight.png)

4. Logikou používanou ve výše uvedené zvýraznění skriptu není spolehlivá. Pokud zadáte termín, který se zobrazí dvakrát se stejným názvem, nejsou výsledky tučné poměrně co je vhodné. Zkuste zadat "měsíc".

Jedna z otázek, které vývojář musí odpovědět je, když je skript práce "dobře dostatek", a kdy se má jeho adaptivní řešit. Jsme nebude trvá zvýraznění žádné dále v tomto kurzu ale hledání přesné algoritmus je něco, co můžete zvážit v případě, že spustíte další zvýraznění.

## <a name="add-autocompletion"></a>Přidat automatického doplňování

Je další variantou, se mírně liší od návrhů, automatického doplňování (někdy nazývané "našeptávání"). Znovu Začneme s nejjednodušší implementace před přesunutím do komfort uživatelů.

1. Zadejte následující skript do zobrazení po předchozí skripty.

```cs
    <script>
        $("#azureautocompletebasic").autocomplete({
            source: "/Home/Autocomplete",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
```

2. Nyní změníte ID do textového pole, aby vypadal takto.

```cs
@Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocompletebasic" }) <input value="" class="searchBoxSubmit" type="submit">
```

3. V domácí kontroleru budeme muset zadat **automatické dokončování** akce, například níže **navrhnout** akce.

```cs
        public async Task<ActionResult> AutoComplete(string term)
        {
            InitSearch();

            // Setup the autocomplete parameters.
            var ap = new AutocompleteParameters()
            {
                AutocompleteMode = AutocompleteMode.OneTermWithContext,
                Top = 6
            };
            AutocompleteResult autocompleteResult = await _indexClient.Documents.AutocompleteAsync(term, "sg", ap);

            // Convert the results to a list that can be displayed in the client.
            List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();

            // Return the list.
            return new JsonResult(autocomplete);
        }
```

Všimněte si, že používáme stejné *modulu pro návrhy* funkci nazvanou "zabezpečení" ve službě search automatické dokončování, jako jsme to udělali pro návrhy (takže se pouze pokoušíme automatické dokončování názvů hotelu).

Existuje řada **vlastnost AutocompleteMode** nastavení a ještě používáte **OneTermWithContext**. Odkazovat na [automatické dokončování Azure](https://docs.microsoft.com/rest/api/searchservice/autocomplete) popis toho, množství možností.

4. Spusťte aplikaci. Všimněte si, jak škálu možnosti zobrazené v rozevíracím seznamu jsou jednotlivá slova. Zkuste zadat slovům začínajícím na "znovu". Všimněte si, jak snižuje počet možností, které jsou zadávání více písmen.

    ![Zadání základního automatického doplňování.](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocompletebasic.png)

Jak se návrhy skriptu, který jste dříve spustili je pravděpodobně užitečnější než tento skript automatického doplňování. Chcete-li automatického doplňování přívětivější, nejlépe přidá se do hledání návrhů.

## <a name="combine-autocompletion-and-suggestions"></a>Kombinace automatického doplňování a návrhy

Kombinace automatického doplňování a návrhy je nejsložitější naše možnosti a pravděpodobně poskytuje nejlepší uživatelské prostředí. Rádi bychom je zobrazení, vložené textem, který uživatel píše, první volbu služby Azure Search pro automatického dokončení text. Kromě toho chceme, aby celou řadu návrhů jako rozevíracího seznamu.

Existují knihovny, které nabízí tyto funkce – často označované jako "vložené automatického doplňování" nebo podobným názvem. Ale budeme nativně provádět tuto funkci, abyste si mohli zobrazit, co se děje. Budeme v tomto příkladu nejdříve spustit práci na kontroleru.

1. Potřebujeme přidat akci pro kontroler, který vrátí pouze jeden výsledek automatického doplňování, spolu s zadaný počet návrhů. Budeme volat tuto akci **AutocompleteAndSuggest**. Domácí kontroleru přidejte následující akci po další nové akce.

```cs
        public async Task<ActionResult> AutocompleteAndSuggest(string term)
        {
            InitSearch();

            // Setup the type-ahead search parameters.
            var ap = new AutocompleteParameters()
            {
                AutocompleteMode = AutocompleteMode.OneTermWithContext,
                Top = 1,
            };
            AutocompleteResult autocompleteResult = await _indexClient.Documents.AutocompleteAsync(term, "sg", ap);

            // Setup the suggest search parameters.
            var sp = new SuggestParameters()
            {
                Top = 8,
            };

            // Only one suggester can be specified per index. The name of the suggester is set when the suggester is specified by other API calls.
            // The suggester for the hotel database is called "sg", and it searches only the hotel name.
            DocumentSuggestResult<Hotel> suggestResult = await _indexClient.Documents.SuggestAsync<Hotel>(term, "sg", sp);

            // Create an empty list.
            var results = new List<string>();

            if (autocompleteResult.Results.Count > 0)
            {
                // Add the top result for type-ahead.
                results.Add(autocompleteResult.Results[0].Text);
            }
            else
            {
                // There were no type-ahead suggestions, so add an empty string.
                results.Add("");
            }
            for (int n = 0; n < suggestResult.Results.Count; n++)
            {
                // Now add the suggestions.
                results.Add(suggestResult.Results[n].Text);
            }

            // Return the list.
            return new JsonResult(results);
        }
```

Jednou z možností automatického doplňování se vrátí v horní části **výsledky** list, za nímž následuje všechny návrhy.

2. V zobrazení nejprve Implementujeme zdvih tak, aby světle šedá automatického doplňování slov je vykreslen v rámci bolder textového zadání uživatelem. HTML obsahuje relativní umístění pro tento účel. Změnit **TextBoxFor** – příkaz (a jeho kolem &lt;div&gt; příkazy) následujícím konstatujme, že druhý vyhledávacího pole identifikované jako **pod** je přímo v rámci naší Normální vyhledávacího pole přidáním tohoto hledání pixelů 39 pole z výchozího umístění!

```cs
    <div id="underneath" class="searchBox" style="position: relative; left: 0; top: 0">
    </div>

    <div id="searchinput" class="searchBoxForm" style="position: relative; left: 0; top: -39px">
        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocomplete" }) <input value="" class="searchBoxSubmit" type="submit">
    </div>
```

Poznámka: měníme ID znovu, **azureautocomplete** v tomto případě.

3. Také v zobrazení, zadejte následující skript, všechny skripty, které jste zatím zadali. Je poměrně hodně to.

```cs
    <script>
        $('#azureautocomplete').autocomplete({
            delay: 500,
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            },

            // Use Ajax to set up a "success" function.
            source: function (request, response) {
                var controllerUrl = "/Home/AutoCompleteAndSuggest?term=" + $("#azureautocomplete").val();
                $.ajax({
                    url: controllerUrl,
                    dataType: "json",
                    success: function (data) {
                        if (data && data.length > 0) {

                            // Show the autocomplete suggestion.
                            document.getElementById("underneath").innerHTML = data[0];

                            // Remove the top suggestion as it is used for inline autocomplete.
                            var array = new Array();
                            for (var n = 1; n < data.length; n++) {
                                array[n - 1] = data[n];
                            }

                            // Show the drop-down list of suggestions.
                            response(array);
                        } else {

                            // Nothing is returned, so clear the autocomplete suggestion.
                            document.getElementById("underneath").innerHTML = "";
                        }
                    }
                });
            }
        });

        // Complete on TAB.
        // Clear on ESC.
        // Clear if backspace to less than 2 characters.
        // Clear if any arrow key hit as user is navigating the suggestions.
        $("#azureautocomplete").keydown(function (evt) {

            var suggestedText = document.getElementById("underneath").innerHTML;
            if (evt.keyCode === 9 /* TAB */ && suggestedText.length > 0) {
                $("#azureautocomplete").val(suggestedText);
                return false;
            } else if (evt.keyCode === 27 /* ESC */) {
                document.getElementById("underneath").innerHTML = "";
                $("#azureautocomplete").val("");
            } else if (evt.keyCode === 8 /* Backspace */) {
                if ($("#azureautocomplete").val().length < 2) {
                    document.getElementById("underneath").innerHTML = "";
                }
            } else if (evt.keyCode >= 37 && evt.keyCode <= 40 /* Any arrow key */) {
                document.getElementById("underneath").innerHTML = "";
            }
        });

        // Character replace function.
        function setCharAt(str, index, chr) {
            if (index > str.length - 1) return str;
            return str.substr(0, index) + chr + str.substr(index + 1);
        }

        // This function is needed to clear the "underneath" text when the user clicks on a suggestion, and to
        // correct the case of the autocomplete option when it does not match the case of the user input.
        // The interval function is activated with the input, blur, change, or focus events.
        $("#azureautocomplete").on("input blur change focus", function (e) {

            // Set a 2 second interval duration.
            var intervalDuration = 2000, 
                interval = setInterval(function () {

                    // Compare the autocorrect suggestion with the actual typed string.
                    var inputText = document.getElementById("azureautocomplete").value;
                    var autoText = document.getElementById("underneath").innerHTML;

                    // If the typed string is longer than the suggestion, then clear the suggestion.
                    if (inputText.length > autoText.length) {
                        document.getElementById("underneath").innerHTML = "";
                    } else {

                        // If the strings match, change the case of the suggestion to match the case of the typed input.
                        if (autoText.toLowerCase().startsWith(inputText.toLowerCase())) {
                            for (var n = 0; n < inputText.length; n++) {
                                autoText = setCharAt(autoText, n, inputText[n]);
                            }
                            document.getElementById("underneath").innerHTML = autoText;

                        } else {
                            // The strings do not match, so clear the suggestion.
                            document.getElementById("underneath").innerHTML = "";
                        }
                    }

                    // If the element loses focus, stop the interval checking.
                    if (!$input.is(':focus')) clearInterval(interval);

                }, intervalDuration);
        });
    </script>
```

Všimněte si, že dokonalá použití **interval** funkce k oběma smazat základní text, když už neodpovídá zadání uživatele, a také nastavit malých písmen (horní nebo dolní) jako uživatel je zadání (jako "pa" odpovídá "PA", "pA", "Pa" při hledání), tak, aby byla úhledné překryté text.

Přečtěte si komentáře ve skriptu, chcete-li získat podrobnější vysvětlení.

4. A konečně musíme provést menší úpravu na dvou tříd HTML, aby byly transparentní. Přidejte následující řádek, který **searchBoxForm** a **searchBox** třídy v souboru hotels.css.

```cs
        background: rgba(0,0,0,0);
```

5. Nyní spusťte aplikaci. Do vyhledávacího pole zadejte "pa". Zobrazila se vám "palace" jako návrhy automatického dokončování, spolu s dvěma hotely, které obsahují "pa"?

    ![Zadání vloženého automatické dokončování a návrhy](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocomplete.png)

6. Procházení tabulátorem přijměte nabídnuté a zkuste vybrat návrhy pomocí šipkové klávesy a stisknutím klávesy tab a akci opakujte pomocí myši a jedním kliknutím. Ověřte, že skript elegantně zpracovává všech těchto situacích.

Můžete se rozhodnout, že je načíst do knihovny, který nabízí tato funkce pro vás jednodušší, ale teď znáte alespoň jeden způsob, jak automatického doplňování inline se pusťte do práce!

## <a name="takeaways"></a>Shrnutí

Vezměte v úvahu následující takeaways z tohoto projektu:

* Automatického doplňování (označované také jako "našeptávání") a návrhy můžete povolit uživateli zadat jenom pár klíčů najít přesně to, co chtějí.
* Automatického doplňování a návrhy spolupracují můžete poskytují bohaté uživatelské prostředí.
* Vždy testujte funkce automatického doplňování se všechny formy vstup.
* Použití **setInterval** funkce může být užitečné při ověřením a opravením prvky uživatelského rozhraní.

## <a name="next-steps"></a>Další postup

Dokončili jste tuto sérii C# výukových kurzů – které by měl získali cenné informace o rozhraní API služby Azure Search.

Další referenční dokumentace a kurzy, zvažte možnost procházení [Microsoft Learn](https://docs.microsoft.com/learn/browse/?products=azure), nebo další kurzy v [dokumentace ke službě Azure Search](https://docs.microsoft.com/azure/search/).
