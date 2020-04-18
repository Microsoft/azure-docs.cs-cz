---
title: Automatické dokončování a návrhy
titleSuffix: Azure Cognitive Search
description: Tento kurz ukazuje automatické dokončování a návrhy jako způsob, jak shromažďovat zadávání vyhledávacích termínů od uživatelů pomocí rozevíracího seznamu. Navazuje na stávající hotelový projekt.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: 6b74c3bbb811c122950fd969a8797e87f8f77f86
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641079"
---
# <a name="c-tutorial-add-autocomplete-and-suggestions---azure-cognitive-search"></a>Kurz C#: Přidání automatického dokončování a návrhů – Azure Cognitive Search

Přečtěte si, jak implementovat automatické dokončování (dotazy typu dopředné a navrhované dokumenty), když uživatel začne psát do vyhledávacího pole. V tomto kurzu se zobrazí automaticky dokončené dotazy a návrhy výsledky samostatně a pak společně. Uživatel může mít pouze zadat dva nebo tři znaky najít všechny výsledky, které jsou k dispozici.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Přidání návrhů
> * Přidání zvýraznění k návrhům
> * Přidání automatického dokončování
> * Zkombinujte automatické dokončování a návrhy

## <a name="prerequisites"></a>Požadavky

Tento kurz je součástí řady a staví na projektu stránkování vytvořeném v [kurzu C#: Stránkování výsledků hledání – Azure Cognitive Search](tutorial-csharp-paging.md).

Případně si můžete stáhnout a spustit řešení pro tento konkrétní kurz: [3-add-typeahead](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/3-add-typeahead).

## <a name="add-suggestions"></a>Přidání návrhů

Začněme s nejjednodušším případem, kdy nabízíte alternativy uživateli: rozevírací seznam návrhů.

1. V souboru index.cshtml `@id` změna **příkazu TextBoxFor** na **azureautosuggest**.

    ```cs
     @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautosuggest" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

2. Po tomto příkazu zadejte po uzávěrce ** &lt;/div&gt;** tento skript. Tento skript využívá [widget automatického dokončování](https://api.jqueryui.com/autocomplete/) z open-source knihovny jQuery UI k prezentaci rozevíracího seznamu navrhovaných výsledků. 

    ```javascript
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

    ID "azureautosuggest" spojuje výše uvedený skript do vyhledávacího pole. Zdrojová možnost widgetu je nastavena na metodu Suggest, která volá rozhraní Suggest API se dvěma parametry dotazu: **zvýraznění** a **přibližné**, obě nastavené na hodnotu false v této instanci. K aktivaci hledání jsou také zapotřebí minimálně dva znaky.

### <a name="add-references-to-jquery-scripts-to-the-view"></a>Přidání odkazů na skripty jQuery do zobrazení

1. Chcete-li získat přístup ke &lt;&gt; knihovně jQuery, změňte hlavní část souboru zobrazení na následující kód:

    ```cs
    <head>
        <meta charset="utf-8">
        <title>Typeahead</title>
        <link href="https://code.jquery.com/ui/1.12.1/themes/start/jquery-ui.css"
              rel="stylesheet">
        <script src="https://code.jquery.com/jquery-1.10.2.js"></script>
        <script src="https://code.jquery.com/ui/1.12.1/jquery-ui.js"></script>

        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

2. Protože zavádíme nový odkaz jQuery, musíme také odstranit nebo zakomentovat výchozí odkaz jQuery v souboru _Layout.cshtml (ve složce **Zobrazení / Sdílené).** Vyhledejte následující řádky a zakomentujte první řádek skriptu, jak je znázorněno na obrázku. Tato změna zabraňuje kolidování odkazy na jQuery.

    ```html
    <environment include="Development">
        <!-- <script src="~/lib/jquery/dist/jquery.js"></script> -->
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
    ```

    Nyní můžeme použít předdefinované funkce automatického dokončování jQuery.

### <a name="add-the-suggest-action-to-the-controller"></a>Přidání akce Navrhnout do ovladače

1. V domácím ovladači přidejte akci **Navrhnout** (třeba za akci **Stránka).**

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

            // Only one suggester can be specified per index. It is defined in the index schema.
            // The name of the suggester is set when the suggester is specified by other API calls.
            // The suggester for the hotel database is called "sg", and simply searches the hotel name.
            DocumentSuggestResult<Hotel> suggestResult = await _indexClient.Documents.SuggestAsync<Hotel>(term, "sg", parameters);

            // Convert the suggest query results to a list that can be displayed in the client.
            List<string> suggestions = suggestResult.Results.Select(x => x.Text).ToList();

            // Return the list of suggestions.
            return new JsonResult(suggestions);
        }
    ```

    Parametr **Top** určuje, kolik výsledků má být vráceno (pokud není zadáno, výchozí hodnota je 5). _Návrhovač_ je určen v indexu Azure, který se provádí při nastavení dat, a nikoli klientskou aplikací, jako je tento kurz. V tomto případě se návrhový člen nazývá "sg" a prohledá pole **Název_** 

    Přibližné shody umožňuje "téměř mine", které mají být zahrnuty do výstupu, až do jedné vzdálenosti úprav. Pokud je parametr **highlights** nastaven na hodnotu true, budou do výstupu přidány tučné značky HTML. Tyto dva parametry nastavíme na hodnotu true v další části.

2. Můžete získat některé syntaktické chyby. Pokud ano, přidejte následující dva **příkazy using** do horní části souboru.

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

3. Spusťte aplikaci. Máte řadu možností, když zadáte "po", například? Teď zkus "pa".

    ![Psaní "po" odhaluje dva návrhy](./media/tutorial-csharp-create-first-app/azure-search-suggest-po.png)

    Všimněte si, že písmena, která zadáte, _musí_ začínat slovo a nesmí být jednoduše zahrnuta do slova.

4. Ve skriptu zobrazení nastavte **&nepřímazaného** na true a spusťte aplikaci znovu. Nyní zadejte "po". Všimněte si, že hledání předpokládá, že máš jeden dopis špatně!
 
    ![Psaní "pa" s fuzzy nastaveným na hodnotu true](./media/tutorial-csharp-create-first-app/azure-search-suggest-fuzzy.png)

    Pokud vás [zajímá, syntaxe dotazu Lucene v Azure Cognitive Search](https://docs.microsoft.com/azure/search/query-lucene-syntax) popisuje logiku použitou v přibližné vyhledávání podrobně.

## <a name="add-highlighting-to-the-suggestions"></a>Přidání zvýraznění k návrhům

Můžeme zlepšit vzhled návrhů pro uživatele nastavením parametru **highlights** na true. Nejprve však musíme přidat nějaký kód do zobrazení pro zobrazení tučného textu.

1. V zobrazení (index.cshtml) přidejte následující skript za **skript azureautosuggest,** který jste zadali výše.

    ```javascript
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

2. Nyní změňte ID textového pole tak, aby se přečte následujícím způsobem.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azuresuggesthighlights" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. Spusťte aplikaci znovu a v návrzích byste měli vidět zadaný text s tučným písmem. Zkus zadat "pa".
 
    ![Psaní "pa" se zvýrazněním](./media/tutorial-csharp-create-first-app/azure-search-suggest-highlight.png)

4. Logika použitá ve výše uvedeném skriptu pro zvýraznění není spolehlivá. Pokud zadáte termín, který se zobrazí dvakrát ve stejném názvu, tučně výsledky nejsou úplně to, co byste chtěli. Zkuste zadat "mo".

    Jednou z otázek, vývojář musí odpovědět, je, když je skript pracuje "dost dobře", a kdy by měl y jeho vtípky být řešeny. Nebudeme brát zvýraznění žádné další v tomto tutoriálu, ale najít přesný algoritmus je něco, co je třeba zvážit, pokud zvýraznění není efektivní pro vaše data. Další informace naleznete v tématu [Zvýraznění zásahu](search-pagination-page-layout.md#hit-highlighting).

## <a name="add-autocomplete"></a>Přidání automatického dokončování

Další variantou, která se mírně liší od návrhů, je automatické dokončování (někdy nazývané "typ dopředu"), který dokončí termín dotazu. Opět začneme s nejjednodušší implementací, než zlepšíme uživatelské prostředí.

1. Podle předchozích skriptů zadejte do zobrazení následující skript.

    ```javascript
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

2. Nyní změňte ID textového pole tak, aby se přečte následujícím způsobem.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocompletebasic" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. V domácím ovladači musíme zadat akci **automatického dokončování,** řekněme pod akci **Navrhnout.**

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

    Všimněte si, že používáme stejnou funkci *návrhu,* nazvanou "sg", při hledání automatického dokončování, jak jsme to udělali pro návrhy (takže se snažíme pouze automaticky dokončovat názvy hotelů).

    Existuje řada nastavení **režimu automatického dokončování** a používáme **OneTermWithContext**. Popis dalších možností naleznete v [rozhraní API pro automatické dokončování.](https://docs.microsoft.com/rest/api/searchservice/autocomplete)

4. Spusťte aplikaci. Všimněte si, jak rozsah možností zobrazených v rozevíracím seznamu jsou jednotlivá slova. Zkuste psát slova začínající na "re". Všimněte si, jak se počet možností snižuje, jak jsou zadána další písmena.

    ![Psaní se základním automatickým dokončováním](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocompletebasic.png)

    Jak to stojí, návrhy skript, který jste běželi dříve, je pravděpodobně užitečnější než tento skript automatického dokončování. Chcete-li automatické dokončování uživatelsky přívětivější, je nejlepší přidat do hledání návrhů.

## <a name="combine-autocompletion-and-suggestions"></a>Zkombinujte automatické dokončování a návrhy

Kombinace automatického dokončování a návrhů je nejsložitější z našich možností a pravděpodobně poskytuje nejlepší uživatelský zážitek. Co chceme, je zobrazit, v souladu s textem, který je zadáván, je první volbou Azure Cognitive Search pro automatické dokončení textu. Také chceme řadu návrhů jako rozevírací seznam.

Existují knihovny, které nabízejí tuto funkci - často volal "inline automatické dokončování" nebo podobný název. Tuto funkci však nativně implementujeme, abyste viděli, co se děje. Chystáme se začít pracovat na řadiči nejprve v tomto příkladu.

1. Musíme přidat akci řadiče, který vrátí pouze jeden výsledek automatického dokončení, spolu se zadaným počtem návrhů. Tuto akci nazveme **automatickým dokončováníananananasutím**. Do domácího ovladače přidejte následující akci, která následuje další nové akce.

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

    Jedna možnost automatického dokončování je vrácena v horní části seznamu **výsledků,** následuje všechny návrhy.

2. V zobrazení nejprve implementujeme trik tak, aby bylo jasně šedé slovo automatického dokončování vykresleno přímo pod odvážnějším textem zadávaným uživatelem. HTML zahrnuje relativní umístění pro tento účel. Změňte **TextBoxFor** prohlášení (a jeho okolní &lt;div&gt; prohlášení) na následující, s tím, že druhý vyhledávací pole označené jako **pod** je přímo pod naší normální vyhledávací pole, tahem toto vyhledávací pole 39 pixelů z jeho výchozí umístění!

    ```cs
    <div id="underneath" class="searchBox" style="position: relative; left: 0; top: 0">
    </div>

    <div id="searchinput" class="searchBoxForm" style="position: relative; left: 0; top: -39px">
        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocomplete" }) <input value="" class="searchBoxSubmit" type="submit">
    </div>
    ```

    Všimněte si, že v tomto případě měníme ID znovu na **azureautocomplete.**

3. Také v zobrazení, zadejte následující skript, po všech skriptů, které jste zadali tak daleko. Je toho docela dost.

    ```javascript
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

    Všimněte si chytrého použití funkce **intervalu** k vymazání podkladového textu, když již neodpovídá tomu, co uživatel píše, a také k nastavení stejného případu (horní nebo dolní) jako uživatel zadává (jako "pa" odpovídá "PA", "pA", "Pa" při hledání), takže překrytý text je čistý.

    Přečtěte si komentáře ve skriptu získat úplnější pochopení.

4. Nakonec musíme provést menší úpravu dvou tříd HTML, aby byly průhledné. Přidejte následující řádek do tříd **searchBoxForm** a **searchBox** v souboru hotels.css.

    ```html
        background: rgba(0,0,0,0);
    ```

5. Nyní spusťte aplikaci. Do vyhledávacího pole zadejte "pa". Máte "palác" jako návrh automatického dokončování, spolu se dvěma hotely, které obsahují "pa"?

    ![Psaní s vřádkovým automatickým dokončováním a návrhy](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocomplete.png)

6. Zkuste přijmout návrh automatického dokončování tabulátorem a vybrat návrhy pomocí kláves se šipkami a klávesy tabulátoru a zkusit to znovu pomocí myši a jediného kliknutí. Ověřte, zda skript zpracovává všechny tyto situace úhledně.

    Můžete se rozhodnout, že je jednodušší načíst do knihovny, která nabízí tuto funkci pro vás, ale teď víte, alespoň jeden způsob, jak získat inline automatické dokončování do práce!

## <a name="takeaways"></a>Shrnutí

Vezměme si následující stánek s jídlem z tohoto projektu:

* Automatické dokončování (označované také jako "typ dopředu") a návrhy mohou uživateli umožnit zadat pouze několik klíčů, aby vyšel přesně to, co chtějí.
* Automatické dokončování a návrhy, které spolupracují, mohou poskytnout bohaté uživatelské prostředí.
* Vždy otestujte funkce automatického dokončování se všemi formami vstupu.
* Použití funkce **setInterval** může být užitečné při ověřování a opravě prvků uživatelského rozhraní.

## <a name="next-steps"></a>Další kroky

V dalším kurzu se podíváme na jiný způsob, jak zlepšit uživatelské prostředí, pomocí omezujících aspekty zúžit vyhledávání s jedním kliknutím.

> [!div class="nextstepaction"]
> [C# Výuka: Použití omezujících fasejí pro navigaci – Azure Cognitive Search](tutorial-csharp-facets.md)


