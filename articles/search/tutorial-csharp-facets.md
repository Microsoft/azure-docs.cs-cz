---
title: C# návod na použití omezující chod pro navigaci
titleSuffix: Azure Cognitive Search
description: Tento kurz staví na projektu "Stránkování výsledků hledání – Azure Cognitive Search" projektu přidat omezující schopnost navigace. Zjistěte, jak lze omezující aspekty snadno zúžit hledání.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: d88a9d7efdabd493fd31b961748bb6ad3bd8d738
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77121568"
---
# <a name="c-tutorial-use-facets-to-aid-navigation---azure-cognitive-search"></a>C# výuka: Použití omezující chod funkce pro navigaci – Azure Cognitive Search

Omezující jsou používány k podpoře navigace tím, že uživateli sadu odkazů použít k zaměření jejich vyhledávání. Omezující vlastnosti jsou atributy dat (například kategorie nebo konkrétní funkce hotelu v našich ukázkových datech).

Tento kurz navazuje na stránkovací projekt vytvořený v [kurzu C#: Stránkování výsledků hledání – kurz azure kognitivního vyhledávání.](tutorial-csharp-paging.md)

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Nastavit vlastnosti modelu jako _IsFacetable_
> * Přidání omezující ho satónové navigace do aplikace

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

Mít [c# kurz: stránkování výsledků hledání – projekt Azure Cognitive Search](tutorial-csharp-paging.md) je spuštěn. Tento projekt může být buď vaše vlastní verze, nebo jej nainstalovat z GitHub: [Vytvořit první aplikaci](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="set-model-properties-as-isfacetable"></a>Nastavit vlastnosti modelu jako IsFacetable

Aby byla vlastnost modelu umístěna v hledání omezujícívlastnosti, musí být označena **isfacetable**.

1. Prohlédněte si **třídu hotelu.** **Kategorie** a **značky**, například jsou označeny jako **IsFacetable**, ale **Název_hotel a** **popis** nejsou. 

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

2. Nebudeme měnit žádné značky jako součást tohoto kurzu, takže zavřete hotel.cs soubor beze změny.

    > [!Note]
    > Hledání omezujících vlivů vyvolá chybu, pokud pole požadované při hledání není odpovídajícím způsobem označeno.


## <a name="add-facet-navigation-to-your-app"></a>Přidání omezující ho satónové navigace do aplikace

V tomto příkladu umožníme uživateli vybrat jednu kategorii hotelu nebo jednu vybavenost ze seznamů odkazů zobrazených nalevo od výsledků. Uživatel začíná zadáním nějakého hledaného textu, pak může zúžit výsledky hledání výběrem kategorie a může výsledky dále zúžit výběrem amenity, nebo může nejprve vybrat amenity (pořadí není důležité).

Potřebujeme, aby správce předal seznamy omezujících položek do zobrazení. Musíme zachovat výběr uživatelů v průběhu vyhledávání a znovu používáme dočasné úložiště jako mechanismus pro uchovávání dat.

![Použití fazety navigace zúžit hledání "fondu"](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="add-filter-strings-to-the-searchdata-model"></a>Přidání řetězců filtrů do modelu SearchData

1. Otevřete soubor SearchData.cs a přidejte vlastnosti řetězce do třídy **SearchData,** abyste podrželi řetězce filtrů omezujících vlastností.

    ```cs
        public string categoryFilter { get; set; }
        public string amenityFilter { get; set; }
    ```

### <a name="add-the-facet-action-method"></a>Přidání metody akce omezující ho výrazu

Domácí řadič potřebuje jednu novou akci, **omezující tvář**a aktualizace stávajících akcí **indexu** a **stránky,** stejně jako aktualizace metody **RunQueryAsync.**

1. Otevřete soubor domovského řadiče a přidejte příkaz **using,** abyste povolili konstrukci **&lt;&gt; řetězce seznamu.**

    ```cs
    using System.Collections.Generic;
    ```

2. Nahraďte metodu akce **Index (Model SearchData).**

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

                // Make the search call for the first page.
                await RunQueryAsync(model, 0, 0, "", "");
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

3. Nahraďte metodu akce **Page(Model SearchData).**

    ```cs
        public async Task<ActionResult> Page(SearchData model)
        {
            try
            {
                int page;

                // Calculate the page that should be displayed.
                switch (model.paging)
                {
                    case "prev":
                        page = (int)TempData["page"] - 1;
                        break;

                    case "next":
                        page = (int)TempData["page"] + 1;
                        break;

                    default:
                        page = int.Parse(model.paging);
                        break;
                }

                // Recover the leftMostPage.
                int leftMostPage = (int)TempData["leftMostPage"];

                // Recover the filters.
                string catFilter = TempData["categoryFilter"].ToString();
                string ameFilter = TempData["amenityFilter"].ToString();

                // Recover the search text.
                model.searchText = TempData["searchfor"].ToString();

                // Search for the new page.
                await RunQueryAsync(model, page, leftMostPage, catFilter, ameFilter);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "2" });
            }
            return View("Index", model);
        }
    ```

4. Přidejte metodu akce **Omezující stav (model SearchData),** která se aktivuje, když uživatel klikne na odkaz omezující sazby. Model bude obsahovat filtr vyhledávání kategorií nebo filtr vyhledávání podle vlastností. Možná ji přidat po akci **Stránka.**

    ```cs
        public async Task<ActionResult> Facet(SearchData model)
        {
            try
            {
                // Filters set by the model override those stored in temporary data.
                string catFilter;
                string ameFilter;
                if (model.categoryFilter != null)
                {
                    catFilter = model.categoryFilter;
                } else
                {
                    catFilter = TempData["categoryFilter"].ToString();
                }

                if (model.amenityFilter != null)
                {
                    ameFilter = model.amenityFilter;
                } else
                {
                    ameFilter = TempData["amenityFilter"].ToString();
                }

                // Recover the search text.
                model.searchText = TempData["searchfor"].ToString();

                // Initiate a new search.
                await RunQueryAsync(model, 0, 0, catFilter, ameFilter);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "2" });
            }
            return View("Index", model);
        }
    ```

### <a name="set-up-the-search-filter"></a>Nastavení filtru hledání

Když uživatel vybere například určitou omezující položku, klikne na kategorii **Resort and Spa,** ve výsledcích by měly být vráceny pouze hotely, které jsou určeny jako tato kategorie. Chcete-li zúžit hledání tímto způsobem, musíme nastavit _filtr_.

1. Nahraďte metodu **RunQueryAsync** následujícím kódem. Primárně trvá řetězec filtru kategorie a řetězec filtru amenity filtraci a nastaví **parametr Filter** **SearchParameters**.

    ```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage, string catFilter, string ameFilter)
        {
            InitSearch();

            string facetFilter = "";

            if (catFilter.Length > 0 && ameFilter.Length > 0)
            {
                // Both facets apply.
                facetFilter = $"{catFilter} and {ameFilter}"; 
            } else
            {
                // One, or zero, facets apply.
                facetFilter = $"{catFilter}{ameFilter}";
            }

            var parameters = new SearchParameters
            {
                Filter = facetFilter,

                // Return information on the text, and number, of facets in the data.
                Facets = new List<string> { "Category,count:20", "Tags,count:20" },

                // Enter Hotel property names into this list, so only these values will be returned.
                Select = new[] { "HotelName", "Description", "Category", "Tags" },

                SearchMode = SearchMode.All,

                // Skip past results that have already been returned.
                Skip = page * GlobalVariables.ResultsPerPage,

                // Take only the next page worth of results.
                Top = GlobalVariables.ResultsPerPage,

                // Include the total number of results.
                IncludeTotalResultCount = true,
            };

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

            // This variable communicates the total number of pages to the view.
            model.pageCount = ((int)model.resultList.Count + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

            // This variable communicates the page number being displayed to the view.
            model.currentPage = page;

            // Calculate the range of page numbers to display.
            if (page == 0)
            {
                leftMostPage = 0;
            }
            else
               if (page <= leftMostPage)
            {
                // Trigger a switch to a lower page range.
                leftMostPage = Math.Max(page - GlobalVariables.PageRangeDelta, 0);
            }
            else
            if (page >= leftMostPage + GlobalVariables.MaxPageRange - 1)
            {
                // Trigger a switch to a higher page range.
                leftMostPage = Math.Min(page - GlobalVariables.PageRangeDelta, model.pageCount - GlobalVariables.MaxPageRange);
            }
            model.leftMostPage = leftMostPage;

            // Calculate the number of page numbers to display.
            model.pageRange = Math.Min(model.pageCount - leftMostPage, GlobalVariables.MaxPageRange);

            // Ensure Temp data is stored for the next call.
            TempData["page"] = page;
            TempData["leftMostPage"] = model.leftMostPage;
            TempData["searchfor"] = model.searchText;
            TempData["categoryFilter"] = catFilter;
            TempData["amenityFilter"] = ameFilter;

            // Return the new view.
            return View("Index", model);
        }
    ```

    Do seznamu **Vybrat** **položky,** které chcete vrátit, jsme přidali vlastnosti Kategorie a **tagy.** Toto přidání není požadavek pro fazet navigace do práce, ale používáme tyto informace k ověření, že jsme filtrování správně.

### <a name="add-lists-of-facet-links-to-the-view"></a>Přidání seznamů omezujících položek odkazů do zobrazení

Pohled bude vyžadovat některé významné změny. 

1. Začněte otevřením souboru hotels.css (ve složce wwwroot/css) a přidejte následující třídy.

    ```html
    .facetlist {
        list-style: none;
    }

    .facetchecks {
        width: 250px;
        display: normal;
        color: #666;
        margin: 10px;
        padding: 5px;
    }

    .facetheader {
        font-size: 10pt;
        font-weight: bold;
        color: darkgreen;    
    }
    ```

2. Pro zobrazení uspořádáme výstup do tabulky, abychom úhledně zarovnali seznamy omezujících položek vlevo a výsledky vpravo. Otevřete soubor index.cshtml. Nahraďte celý obsah &lt;&gt; tagů textu HTML následujícím kódem.

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

                        @if (Model != null && Model.resultList != null)
                        {
                            List<string> categories = Model.resultList.Facets["Category"].Select(x => x.Value.ToString()).ToList();

                            if (categories.Count > 0)
                            {
                                <h5 class="facetheader">Category:</h5>
                                <ul class="facetlist">
                                    @for (var c = 0; c < categories.Count; c++)
                                    {
                                        var facetLink = $"{categories[c]} ({Model.resultList.Facets["Category"][c].Count})";
                                        <li>
                                            @Html.ActionLink(facetLink, "Facet", "Home", new { categoryFilter = $"Category eq '{categories[c]}'" }, null)
                                        </li>
                                    }
                                </ul>
                            }

                            List<string> tags = Model.resultList.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

                            if (tags.Count > 0)
                            {
                                <h5 class="facetheader">Amenities:</h5>
                                <ul class="facetlist">
                                    @for (var c = 0; c < tags.Count; c++)
                                    {
                                        var facetLink = $"{tags[c]} ({Model.resultList.Facets["Tags"][c].Count})";
                                        <li>
                                            @Html.ActionLink(facetLink, "Facet", "Home", new { amenityFilter = $"Tags/any(t: t eq '{tags[c]}')" }, null)
                                        </li>
                                    }
                                </ul>
                            }
                        }
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
                                string amenities = string.Join(", ", Model.resultList.Results[i].Document.Tags);

                                string fullDescription = Model.resultList.Results[i].Document.Description;
                                fullDescription += $"\nCategory: {Model.resultList.Results[i].Document.Category}";
                                fullDescription += $"\nAmenities: {amenities}";

                                // Display the hotel name and description.
                                @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                                @Html.TextArea($"desc{i}", fullDescription, new { @class = "box2" })
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

    Všimněte si použití volání **Html.ActionLink.** Toto volání sděluje řadiči platné řetězce filtrů, když uživatel klepne na odkaz omezující vlastnost. 

### <a name="run-and-test-the-app"></a>Spuštění a testování aplikace

Výhodou aspekt navigace k uživateli je, že mohou zúžit vyhledávání s jedním kliknutím, které můžeme zobrazit v následujícím pořadí.

1. Spusťte aplikaci, zadejte "letiště" jako hledaný text. Ověřte, zda se seznam omezujících položek zobrazuje úhledně doleva. Tyto aspekty jsou všechny, které se vztahují na hotely, které mají "letiště" v jejich textových dat, s počtem, jak často se vyskytují.

    ![Použití fazetnavigace zúžit hledání "letiště"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport.png)

2. Klikněte na kategorii **Resort and Spa.** Ověřte, zda jsou všechny výsledky v této kategorii.

    ![Zužující se hledání na "Resort and Spa"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras.png)

3. Klikněte na **kontinentální snídani.** Ověřte, zda jsou všechny výsledky stále v kategorii "Resort and Spa" s vybranou vybaveností.

    ![Zužující se hledání na "kontinentální snídani"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras-cb.png)

4. Zkuste vybrat jinou kategorii, pak jednu amenity a zobrazit zužující se výsledky. Pak zkuste naopak, jedna amenity, pak jedna kategorie.

    >[!Note]
    > Když je jeden výběr proveden v seznamu omezujících položek (například kategorie), přepíše jakýkoli předchozí výběr v seznamu kategorií.

## <a name="takeaways"></a>Shrnutí

Vezměme si následující stánek s jídlem z tohoto projektu:

* Je nutné označit každou vlastnost jako **IsFacetable**, pokud mají být zahrnuty do fazetnavigace.
* Fazet navigace poskytuje uživateli snadný a intuitivní způsob, jak zúžit vyhledávání.
* Fasetová navigace je nejlépe rozdělena do sekcí (kategorie hotelu, vybavení hotelu, cenové rozpětí, rozsahy hodnocení atd.), každá sekce s příslušným nadpisem.

## <a name="next-steps"></a>Další kroky

V dalším kurzu se podíváme na objednávání výsledků. Až do tohoto okamžiku jsou výsledky seřazeny jednoduše v pořadí, v jakém jsou umístěny v databázi.

> [!div class="nextstepaction"]
> [C# kurz: Pořadí výsledků- Azure Kognitivní vyhledávání](tutorial-csharp-orders.md)
