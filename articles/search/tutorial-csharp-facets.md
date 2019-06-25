---
title: C#kurz použití omezujících vlastností efektivitu navigace a sítě – Azure Search
description: Tento kurz vychází projektu "Výsledky hledání stránkování – Azure Search" Přidání omezující vlastnosti hledání. Přečtěte si, že omezující vlastnosti lze použít v navigaci a automatického doplňování.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 06/20/2019
ms.openlocfilehash: a81042869564533050fef42a983f2f8fb9bc7b23
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304668"
---
# <a name="c-tutorial-use-facets-for-navigation-and-network-efficiency---azure-search"></a>C#kurz: Použití omezujících vlastností efektivitu navigace a sítě – Azure Search

Omezující vlastnosti mají dva odlišné použití ve službě Azure Search. Omezující vlastnosti umožňuje podporu navigace, tím, že uživatel poskytuje sadu zaškrtněte políčka používat a zaměřit své hledání. Navíc můžete používají ke zlepšení efektivity sítě při použití automatického doplňování. Omezující vlastnost vyhledávání jsou efektivní, protože se provádí jenom s jednou registrací u každé načtení stránky, nikoli s jednou registrací u každé stisknutí kláves. 

Omezující vlastnosti jsou atributy dat (třeba kategorie hotelu v našich ukázkových dat) a obraze životnost vyhledávání.

Tento kurz vytvoří dva projekty: jedno pro omezující vlastnost navigace a druhou pro omezující vlastnost automatického doplňování. Sestavení na stránkování projekt vytvořený v obou projektů [ C# kurzu: Výsledky stránkování – Azure Search](tutorial-csharp-paging.md) kurzu.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Nastavit vlastnosti modelu jako _IsFacetable_
> * Přidání omezující vlastnost navigace do vaší aplikace
> * Přidání automatického doplňování omezující vlastnost do vaší aplikace
> * Při rozhodování, kdy použití automatického doplňování omezující vlastnost

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

Máte [ C# kurzu: Výsledky stránkování – Azure Search](tutorial-csharp-paging.md) projektu rychle zprovoznit. To být vlastní verzi, nebo ji nainstalovat z Githubu: [Vytvoření první aplikace](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="set-model-fields-as-isfacetable"></a>Sada polí modelu jako IsFacetable

V pořadí pro vlastnosti modelu nacházely ve omezující vlastnosti hledání (navigační nebo automatického doplňování), musí být označená pomocí **IsFacetable**.

1. Zkontrolujte **hotelu** třídy. **Kategorie** a **značky**, například jsou označené jako **IsFacetable**, ale **HotelName** a **popis** nejsou. 

    ```cs
    public partial class Hotel
    {
        [System.ComponentModel.DataAnnotations.Key]
        [IsFilterable]
        public string HotelId { get; set; }

        [IsSearchable, IsSortable]
        public string HotelName { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnLucene)]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrLucene)]
        [JsonProperty("Description_fr")]
        public string DescriptionFr { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string Category { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string[] Tags { get; set; }

        [IsFilterable, IsSortable, IsFacetable]
        public bool? ParkingIncluded { get; set; }

        [IsFilterable, IsSortable, IsFacetable]
        public DateTimeOffset? LastRenovationDate { get; set; }

        [IsFilterable, IsSortable, IsFacetable]
        public double? Rating { get; set; }

        public Address Address { get; set; }

        [IsFilterable, IsSortable]
        public GeographyPoint Location { get; set; }

        public Room[] Rooms { get; set; }

    }
    ```

2. Společnost Microsoft nebude měnit všechny značky v tomto kurzu. Omezující vlastnost vyhledávání vyvolá chybu, pokud není požadováno do vyhledávacího pole označené odpovídajícím způsobem.


## <a name="add-facet-navigation-to-your-app"></a>Přidání omezující vlastnost navigace do vaší aplikace

V tomto příkladu budeme umožňující uživateli vybrat jednu nebo více kategorií hotelu, v seznamu uvedeno nalevo od výsledky. Potřebujeme řadič znát seznam kategorií při prvním spuštění aplikace a předat tento seznam zobrazení, který se má zobrazit při vykreslení první obrazovky. Jak se zobrazí jednotlivé stránky potřebujeme Ujistěte se, že budeme mít udržuje seznam omezující vlastnosti a aktuální výběr uživatele, mají být předány na následujících stránkách. Znovu použijeme jako mechanismus pro zachování dat dočasné úložiště.

![Chcete-li zúžit hledání "fondu" pomocí omezující vlastnost navigace](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="modify-the-searchdata-model"></a>Upravit SearchData model

1. Otevřete soubor SearchData.cs a přidejte tuto další **pomocí** příkazu. Musíme povolit **seznamu&lt;řetězec&gt;**  vytvořit.

    ```cs
    using System.Collections.Generic;
    ```

2. Ve stejném souboru přidejte následující řádky do **SearchData** třídy. Neodstraňujte žádný existující vlastnosti třídy, ale přidejte následující konstruktor metody a pole vlastností.

    ```cs
        public SearchData()
        {
        }

        // Constructor to initialize the list of facets sent from the controller.
        public SearchData(List<string> facets)
        {
            facetText = new string[facets.Count];

            for (int i = 0; i < facets.Count; i++)
            {
                facetText[i] = facets[i];
            }
        }

        // Array to hold the text for each facet.
        public string[] facetText { get; set; }

        // Array to hold the check box setting.
        public bool[] facetOn { get; set; }
    ```


### <a name="search-for-facets-on-the-first-index-call"></a>Vyhledejte omezující vlastnosti při prvním volání indexu

Domácí kontrolér musí významné změny. První volání **Index()** by již nevracelo zobrazení s žádné další zpracování. Chceme poskytnout úplný seznam omezující vlastnosti zobrazení a prvním volání je ten správný pro tento účel.

1. Otevřete soubor domácí řadiče a přidejte dva **pomocí** příkazy.

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

2. Nyní nahraďte několika řádků aktuální **Index()** metodu s metodou, která provádí omezující vlastnosti hledání pro kategorie hotelu. Při hledání se provádí asynchronně, musí deklarovat jsme **Index** metody jako **asynchronní**.

    ```cs
        public async Task<ActionResult> Index()
        {
            InitSearch();

            // Set up the facets call in the search parameters.
            SearchParameters sp = new SearchParameters()
            {
                // Search for up to 20 categories.
                // Field names specified here must be marked as "IsFacetable" in the model, or the search call will throw an exception.
                Facets = new List<string> { "Category,count:20" },
            };

            DocumentSearchResult<Hotel> searchResult = await _indexClient.Documents.SearchAsync<Hotel>("*", sp);

            // Convert the results to a list that can be displayed in the client.
            List<string> categories = searchResult.Facets["Category"].Select(x => x.Value.ToString()).ToList();

            // Initiate a model with a list of facets for the first view.
            SearchData model = new SearchData(categories);

            // Save the facet text for the next view.
            SaveFacets(model);

            // Render the view including the facets.
            return View(model);
        }
    ```

    Několik bodů si zde. Můžeme převést výsledky hledání volání do seznam řetězců, pak tato omezující vlastnost řetězce jsou přidány do **SearchData** model pro komunikaci na zobrazení. Také jsme uložit tyto řetězce do dočasného úložiště před nakonec vykreslení zobrazení. Tato uložení se provádí tak, aby tento seznam je k dispozici pro další volání akce kontroleru.

3. Teď přidejte dvě soukromé metody k uložení a obnovení omezujících vlastností vzoru a do dočasného úložiště.

    ```cs
        // Save the facet text to temporary storage, optionally saving the state of the check boxes.
        private void SaveFacets(SearchData model, bool saveChecks = false)
        {
            for (int i = 0; i < model.facetText.Length; i++)
            {
                TempData["facet" + i.ToString()] = model.facetText[i];
                if (saveChecks)
                {
                    TempData["faceton" + i.ToString()] = model.facetOn[i];
                }
            }
            TempData["facetcount"] = model.facetText.Length;
        }

        // Recover the facet text to a model, optionally recoving the state of the check boxes.
        private void RecoverFacets(SearchData model, bool recoverChecks = false)
        {
            // Create arrays of the appropriate length.
            model.facetText = new string[(int)TempData["facetcount"]];
            if (recoverChecks)
            {
                model.facetOn = new bool[(int)TempData["facetcount"]];
            }

            for (int i = 0; i < (int)TempData["facetcount"]; i++)
            {
                model.facetText[i] = TempData["facet" + i.ToString()].ToString();
                if (recoverChecks)
                {
                    model.facetOn[i] = (bool)TempData["faceton" + i.ToString()];
                }
            }
        }
    ```

### <a name="save-and-restore-facet-text-on-all-calls"></a>Uložení a obnovení omezující vlastnost text na všechna volání

1. Dvou dalších akcí Domů řadiče **indexu (SearchData model)** a **stránky (SearchData model)** , obě muset obnovit omezující vlastnosti než volání hledání a uložit je znovu po volání hledání. Změnit **indexu (SearchData model)** pro tyto dvě volání.

    ```cs
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                // Ensure the search string is valid.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }

                // Recover the facet text.
                RecoverFacets(model);

                // Make the search call for the first page.
                await RunQueryAsync(model, 0, 0);

                // Ensure temporary data is stored for the next call.
                TempData["page"] = 0;
                TempData["leftMostPage"] = 0;
                TempData["searchfor"] = model.searchText;

                // Facets
                SaveFacets(model, true);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

2. Teď to samé proveďte **stránky (SearchData model)** metody. Vypsali jsme pouze relevantní kód uvedený níže. Přidat **RecoverFacets** a **SaveFacets** volá kolem **RunQueryAsync** volání.

    ```cs
                // Recover facet text and check marks.
                RecoverFacets(model, true);

                await RunQueryAsync(model, page, leftMostPage);

                // Save facets and check marks.
                SaveFacets(model, true);
    ```

### <a name="set-up-a-search-filter"></a>Nastavit filtr pro hledání

Když uživatel vybere určité charakteristiky, například Dejme tomu, že kliknou na **rozpočtu** a **možnost a Spa** kategorie a pak pouze hotely, které jsou definované jako jednu z těchto dvou kategorií by měla být vrácena v výsledky. K optimalizaci hledání tímto způsobem, musíme nastavit _filtr_.

1. V **RunQueryAsync** metodu, přidejte kód pro cyklický průchod nastavení daného modelu omezující vlastnost, pro vytvoření řetězce filtru. A přidejte filtr, aby se **parametrech vyhledávání**, jak je znázorněno v následujícím kódu.

    ```cs
            // Create a filter for selected facets.
            string selectedFacets = "";

            for (int f = 0; f < model.facetText.Length; f++)
            {
                if (model.facetOn[f])
                {
                    if (selectedFacets.Length > 0)
                    {
                        // If there is more than one selected facet, logically OR them together.
                        selectedFacets += " or ";
                    }
                    selectedFacets += "(Category eq \'" + model.facetText[f] + "\')";
                }
            }

            var parameters = new SearchParameters
            {
                // Facets: add the filter.
                Filter = selectedFacets,

                // Enter Hotel property names into this list so only these values will be returned.
                // If Select is empty, all values will be returned, which can be inefficient.
                Select = new[] { "HotelName", "Description", "Category" },
                SearchMode = SearchMode.All,

                // Skip past results that have already been returned.
                Skip = page * GlobalVariables.ResultsPerPage,

                // Take only the next page worth of results.
                Top = GlobalVariables.ResultsPerPage,

                // Include the total number of results.
                IncludeTotalResultCount = true,
            };
    ```

    Přidali jsme **kategorie** vlastnosti do seznamu **vyberte** položek k vrácení. Přidání této vlastnosti není povinné, ale tímto způsobem můžeme ověřit jsme správně filtrování.

### <a name="define-a-few-additional-html-styles"></a>Definujte pár dalších stylů jazyka HTML

Zobrazení se bude vyžadovat významné změny. 

1. Začněte tím, že soubor hotels.css (ve složce wwwroot/css) a přidejte následující třídy.

    ```html
    .facetlist {
        list-style: none;
    }

    .facetchecks {
        width: 200px;
        background-color: lightgoldenrodyellow;
        display: normal;
        color: #666;
        margin: 10px;
    }
    ```

### <a name="add-a-list-of-facet-checkboxes-to-the-view"></a>Přidání seznamu zaškrtávacích políček omezující vlastnosti k zobrazení

Pro zobrazení jsme uspořádání výstup do tabulky, elegantně zarovnat omezující vlastnosti na levé straně a výsledky na pravé straně. Otevřete soubor index.cshtml.

1. Nahradí celý obsah HTML &lt;tělo&gt; značky s následujícím kódem.

    ```cs
    <body>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        <table>
            <tr>
                <td></td>
                <td>
                    <h1 class="sampleTitle">
                        <img src="~/images/azure-logo.png" width="80" />
                        Hotels Search - Facet Navigation
                    </h1>
                </td>
            </tr>

            <tr>
                <td></td>
                <td>
                    <!-- Display the search text box, with the search icon to the right of it.-->
                    <div class="searchBoxForm">
                        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input value="" class="searchBoxSubmit" type="submit">
                    </div>
                </td>
            </tr>

            <tr>
                <td valign="top">
                    <div id="facetplace" class="facetchecks">
                        <h5>Filter by Category:</h5>
                        <ul class="facetlist">
                            @for (var i = 0; i < Model.facetText.Length; i++)
                            {
                                <li> @Html.CheckBoxFor(m => m.facetOn[i], new { @id = "check" + i.ToString() }) @Model.facetText[i] </li>
                            }
                        </ul>
                    </div>
                </td>
                <td valign="top">
                    <div id="resultsplace">
                        @if (Model != null && Model.resultList != null)
                        {
                            // Show the result count.
                            <p class="sampleText">
                                @Html.DisplayFor(m => m.resultList.Count) Results
                            </p>

                            @for (var i = 0; i < Model.resultList.Results.Count; i++)
                            {
                                // Display the hotel name and description.
                                @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                                @Html.TextArea("desc", Model.resultList.Results[i].Document.Description + "\nCategory:  " +  Model.resultList.Results[i].Document.Category, new { @class = "box2" })
                            }
                        }
                    </div>
                </td>
            </tr>

            <tr>
                <td></td>
                <td valign="top">
                    @if (Model != null && Model.pageCount > 1)
                    {
                        // If there is more than one page of results, show the paging buttons.
                        <table>
                            <tr>
                                <td class="tdPage">
                                    @if (Model.currentPage > 0)
                                    {
                                        <p class="pageButton">
                                            @Html.ActionLink("|<", "Page", "Home", new { paging = "0" }, null)
                                        </p>
                                    }
                                    else
                                    {
                                        <p class="pageButtonDisabled">|&lt;</p>
                                    }
                                </td>

                                <td class="tdPage">
                                    @if (Model.currentPage > 0)
                                    {
                                        <p class="pageButton">
                                            @Html.ActionLink("<", "Page", "Home", new { paging = "prev" }, null)
                                        </p>
                                    }
                                    else
                                    {
                                        <p class="pageButtonDisabled">&lt;</p>
                                    }
                                </td>

                                @for (var pn = Model.leftMostPage; pn < Model.leftMostPage + Model.pageRange; pn++)
                                {
                                    <td class="tdPage">
                                        @if (Model.currentPage == pn)
                                        {
                                            // Convert displayed page numbers to 1-based and not 0-based.
                                            <p class="pageSelected">@(pn + 1)</p>
                                        }
                                        else
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink((pn + 1).ToString(), "Page", "Home", new { paging = @pn }, null)
                                            </p>
                                        }
                                    </td>
                                }

                                <td class="tdPage">
                                    @if (Model.currentPage < Model.pageCount - 1)
                                    {
                                        <p class="pageButton">
                                            @Html.ActionLink(">", "Page", "Home", new { paging = "next" }, null)
                                        </p>
                                    }
                                    else
                                    {
                                        <p class="pageButtonDisabled">&gt;</p>
                                    }
                                </td>

                                <td class="tdPage">
                                    @if (Model.currentPage < Model.pageCount - 1)
                                    {
                                        <p class="pageButton">
                                            @Html.ActionLink(">|", "Page", "Home", new { paging = Model.pageCount - 1 }, null)
                                        </p>
                                    }
                                    else
                                    {
                                        <p class="pageButtonDisabled">&gt;|</p>
                                    }
                                </td>
                            </tr>
                        </table>
                    }
                </td>
            </tr>
        </table>
    }
    </body>
    ```

    Všimněte si použití **CheckBoxFor** volání k naplnění **facetOn** pole s výběry uživatele. Také jsme přidali kategorie hotelu konec popis hotelu. Tento text je jednoduše potvrďte, že naše vyhledávání funguje správně. Není moc jiný změnil z předchozích kurzů, s tím rozdílem, že budeme mít uspořádané výstup do tabulky.

### <a name="run-and-test-the-app"></a>Spuštění a testování aplikace

1. Spusťte aplikaci a ověřte, že seznam omezující vlastnosti elegantně zobrazuje na levé straně.

2. Zkuste vybrat jednu, dvě, tři nebo více políček a ověřte výsledky.

    ![Chcete-li zúžit hledání "Wi-Fi" pomocí omezující vlastnost navigace](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

3. Není lehké komplikací s omezující vlastnost navigace. Co se stane, pokud uživatel změní výběr omezující vlastnosti (zaškrtnutím nebo zrušením výběru zaškrtávací políčka), ale pak na jednu z možností stránkování a ne na vyhledávacím panelu? Změna výběru v důsledku toho by mělo zahájit nové hledání podle aktuální stránky již nebudou správná. Alternativně může být ignorovány změny provedené uživatelem, a další stránky výsledků uvedený, na základě výběrů původní omezující vlastnost. Jsme zvolili v tomto příkladu druhá možnost řešení, ale možná zvažte, jak může implementovat bývalé řešení. Pokud nejnovější výběr zvoleného omezující vlastnosti přesně neodpovídá výběru v dočasné úložiště možná spustí nové hledání?

Tím končí naše příklad omezující vlastnost navigace. Ale možná také zvažte jak může prodloužit tuto aplikaci. Seznam omezující vlastnost může rozšíří na další možnost omezující vlastnost pole (Řekněme, že **značky**), takže uživatel může vybrat celou řadu možností, jako je například fondu, Wi-Fi, pruhový, parkovací bezplatné a tak dále. 

Výhodou omezující vlastnost navigace uživateli je, že není potřeba zadávat stejný text, zachovají své volby omezující vlastnost životnost aktuální relace se aplikace. Může vybrat kategorie a případně také další atributy, jedním kliknutím, vyhledejte na jiné určitý text.

Teď se podíváme jiné užívání omezujících vlastností.

## <a name="add-facet-autocompletion-to-your-app"></a>Přidání automatického doplňování omezující vlastnost do vaší aplikace

Omezující vlastnost automatického doplňování funguje tak, že počáteční vyhledávání při prvním spuštění aplikace. Toto vyhledávání shromažďuje seznam omezující vlastnosti pro použití jako návrhy při zadání uživatele.

![Psaní "re" zobrazí tři omezující vlastnosti](./media/tutorial-csharp-create-first-app/azure-search-facet-type-re.png)

Použijeme číselného stránkování aplikace, které může dokončení v druhé části kurzu jako základ pro tuto ukázku.

K implementaci automatického doplňování omezující vlastnost, jsme není potřeba změnit některý z modely (data třídy). Potřebujeme přidat skript k zobrazení a akce pro kontroler.

### <a name="add-an-autocomplete-script-to-the-view"></a>Přidání skriptu automatické dokončování pro zobrazení

Aby bylo možné zahájit hledání omezující vlastnost, musíme odeslat dotaz. Přidat do souboru index.cshtml následující jazyka JavaScript poskytuje logiku dotazu a prezentaci, kterou potřebujeme.

1. Vyhledejte **@Html.TextBoxFor(m = > m.searchText,...)** příkaz a přidejte jedinečné ID, podobně jako následující.

    ```cs
    <div class="searchBoxForm">
        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azuresearchfacets" }) <input value="" class="searchBoxSubmit" type="submit">
    </div>
    ```

2. Teď přidejte následující JavaScript (za uzavírací **&lt;/div&gt;** výše uvedené funguje bez problémů).

    ```JavaScript
     <script>
            $(function () {
                $.getJSON("/Home/Facets", function (data) {

                    $("#azuresearchfacets").autocomplete({
                        source: data,
                        minLength: 2,
                        position: {
                            my: "left top",
                            at: "left-23 bottom+10"
                        }
                    });
                });
            });
        </script>
    ```

    Všimněte si, že skript volá **omezující vlastnosti** akce v kontroleru home, bez dalších parametrů, když je dosaženo o minimální délce dvě zadané znaky.

### <a name="add-references-to-jquery-scripts-to-the-view"></a>Přidat odkazy na skripty jquery do zobrazení

Ve výše uvedené skriptu volaná funkce automatického dokončování není něco musíme napsat sami je k dispozici v knihovně jquery. 

1. Chcete-li získat přístup k knihovny jquery, nahraďte &lt;head&gt; část zobrazení souboru následujícím kódem.

    ```cs
    <head>
        <meta charset="utf-8">
        <title>Facets demo</title>
        <link href="https://code.jquery.com/ui/1.10.4/themes/ui-lightness/jquery-ui.css"
              rel="stylesheet">
        <script src="https://code.jquery.com/jquery-1.10.2.js"></script>
        <script src="https://code.jquery.com/ui/1.10.4/jquery-ui.js"></script>

        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

2. Musíme také odstranit, nebo okomentovat, řádek odkazující na jquery v souboru _Layout.cshtml (v **zobrazení/Shared** složky). Vyhledejte následující řádky a okomentujte první řádek skriptu, jak je znázorněno. Odstraněním tohoto řádku můžeme vyhnout nejednoznačných odkazů na jquery.

    ```html
     <environment include="Development">
            <!-- <script src="~/lib/jquery/dist/jquery.js"></script> -->
            <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
            <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
    ```

Teď můžeme použít funkce jquery předdefinované funkce automatického dokončování.

### <a name="add-a-facet-action-to-the-controller"></a>Přidání akce omezující vlastnost ke kontroleru

1. Otevřete domovskou kontroleru a přidejte následující dva **pomocí** příkazy na jednoho souboru.

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

2. JavaScript v zobrazení aktivační události **omezující vlastnosti** akce v kontroleru, přidejme tedy tuto akci pro kontroler home (například níže **stránky** akce).

    ```cs
        public async Task<ActionResult> Facets()
        {
            InitSearch();

            // Set up the facets call in the search parameters.
            SearchParameters sp = new SearchParameters()
            {
                // Search all Tags, but limit the total number to 100, and add up to 20 categories.
                // Field names specified here must be marked as "IsFacetable" in the model, or the search call will throw an exception.
                Facets = new List<string> { "Tags,count:100", "Category,count:20" },
            };

            DocumentSearchResult<Hotel> searchResult = await _indexClient.Documents.SearchAsync<Hotel>("*", sp);

            // Convert the results to two lists that can be displayed in the client.
            List<string> facets = searchResult.Facets["Tags"].Select(x => x.Value.ToString()).ToList();
            List<string> categories = searchResult.Facets["Category"].Select(x => x.Value.ToString()).ToList();

            // Combine and return the lists.
            facets.AddRange(categories);
            return new JsonResult(facets);
        }
    ```

    Všimněte si, že požaduje až 100 omezující vlastnosti z **značky** polí a až z 20 **kategorie** pole. **Počet** položky jsou volitelné, pokud žádný počet nastavená výchozí hodnota je 10.

    Potřebujeme dva seznamy, které jsou následně se spojí dohromady do jedné, protože jsme pro dvě pole pro hledání vyzváni (**značky** a **kategorie**). Pokud jsme měli požádán o tři pole pro hledání, máme by zkombinovat do jedné tři seznamy a tak dále.

    > [!NOTE]
    > Je možné nastavit jeden nebo více z následujících parametrů pro každé pole v omezující vlastnosti hledání: **počet**, **řazení**, **interval**, a **hodnoty**. Další informace najdete v tématu [jak ve službě Azure Search implementovat fasetovou navigaci](https://docs.microsoft.com/azure/search/search-faceted-navigation).

### <a name="compile-and-run-your-project"></a>Kompilace a spuštění projektu

Teď otestujte aplikaci.

1. Zkuste zadat "fr" do vyhledávacího pole, které by se zobrazit několik výsledky.

    ![Zadáním "fr" zobrazí tři omezující vlastnosti](./media/tutorial-csharp-create-first-app/azure-search-facet-type-fr.png)

2. Nyní přidejte "o" do "z" Ujistěte se a Všimněte si, že škálu možností je omezená na jednu.

3. Zadejte jiné kombinace dvě písmena a zjistěte, co se zobrazuje. Všimněte si, že když zadáte server *není* volána. Omezující vlastnosti jsou uložené v mezipaměti místně při spuštění aplikace a nyní je jenom k volání serveru když uživatel požádá o hledání.

## <a name="decide-when-to-use-a-facet-autocompletion-search"></a>Při rozhodování, kdy použití search automatického doplňování omezující vlastnost

Vymazat rozdíl mezi omezující vlastnosti hledání a další hledání, jako jsou návrhy a automatického doplňování, je, že omezující vlastnost vyhledávání _navržené_ provádět pouze jednou při načtení stránky. Další automatického doplňování hledání jsou _navržené_ která bude volána po zadání každého znaku. Použití omezujících vlastností tímto způsobem potenciálně uloží mnoho volání na server. 

Nicméně, kdy je vhodné omezující vlastnost automatického doplňování používat?

Omezující vlastnost automatického doplňování je nejvhodnější použít, pokud:
* Primárním důvodem, proč je, že výkon dalších hledání, které volají každé stisknutí klávesy na serveru je problém.
* Vrátí omezující vlastnosti pro uživatele se seznamem možností přiměřené při zadání několik znaků.
* Vrátí omezující vlastnosti poskytují rychlý způsob, jak přistupovat k nejvíce, nebo v ideálním případě všechny, aby byla data dostupná.
* Maximální počet povolit nejvíce omezující vlastnosti mají být zahrnuty. V našem kódu nastavíme maximálně 100 omezující vlastnosti pro **značky** a 20 omezující vlastnosti pro **kategorie**. Sada maximální hodnoty musí fungovat s velikost datové sady. Pokud jsou nebyly příliš mnoho možných omezujících vlastností, pak možná hledání není užitečný by měl být.

> [!NOTE]
> Ačkoli omezující vlastnosti hledání jsou navržené tak, která se má volat po za načtení stránky, můžete samozřejmě volat mnohem častěji, závisí na vaší jazyka JavaScript. Mají stejnou hodnotu true je, že hledání automatického doplňování a návrhu lze provádět méně často než jednou za stisk klávesy. Znovu se určuje podle JavaScript, ne Azure Search. Omezující vlastnost vyhledávání je však navrženy k lze volat pouze jednou na jedné stránce jako omezující vlastnosti se službou Azure Search vytvářejí na základě hledaných dokumenty s tímto v úvahu. Je vhodné vzít v úvahu prohledávání automatického doplňování omezující vlastnost jako formu uživatelská asistence méně flexibilní ale mnohem efektivnější pro sítě.

## <a name="takeaways"></a>Shrnutí

Vezměte v úvahu následující takeaways z tohoto projektu:

* Je nutné k označení jednotlivá pole jako **IsFacetable**, pokud mají být součástí omezující vlastnost navigace nebo automatického doplňování.
* Omezující vlastnost navigace poskytuje uživatele s jednoduchou a intuitivní, způsob zúžení vyhledávání.
* Omezující vlastnost navigace je nejlepší rozdělený do částí (kategorie hotelu), funkce hotelu, cena rozsahy, atd., každá část s odpovídajícím názvem.
* Omezující vlastnost automatického doplňování je účinný způsob získání užitečných uživatelské prostředí bez volání opakované serveru jiné prohledávání automatického doplňování.
* Automatického doplňování omezující vlastnost je _alternativní_ pro automatické dokončování a návrhy, ne doplněk.

## <a name="next-steps"></a>Další postup

Dokončili jste tuto sérii C# výukových kurzů – které by měl získali cenné informace o rozhraní API služby Azure Search.

Další referenční dokumentace a kurzy, zvažte možnost procházení [Microsoft Learn](https://docs.microsoft.com/learn/browse/?products=azure), nebo další kurzy v [dokumentace ke službě Azure Search](https://docs.microsoft.com/azure/search/).
