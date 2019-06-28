---
title: C#kurz použití omezující vlastnosti na podporu navigace – Azure Search
description: Tento kurz vychází projektu "Výsledky hledání stránkování – Azure Search" Přidání omezující vlastnost navigace. Přečtěte si, že omezující vlastnosti je možné snadno Zúží vyhledávání.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 06/20/2019
ms.openlocfilehash: 62326ad3bc5f2d740ce744819df559bce8658eb7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443782"
---
# <a name="c-tutorial-use-facets-to-aid-navigation---azure-search"></a>C#kurz: Podpora navigace – Azure Search pomocí omezující vlastnosti

Omezující vlastnosti umožňují podporu navigace, tím, že uživatel poskytuje sadu odkazy na používat a zaměřit své hledání. Omezující vlastnosti jsou atributy dat (třeba kategorie, nebo konkrétní funkce, hotelu v našich ukázkových dat).

V tomto kurzu se vytvoří na stránkování projekt vytvořený v [ C# kurzu: Výsledky stránkování – Azure Search](tutorial-csharp-paging.md) kurzu.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Nastavit vlastnosti modelu jako _IsFacetable_
> * Přidání omezující vlastnost navigace do vaší aplikace

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

Máte [ C# kurzu: Výsledky stránkování – Azure Search](tutorial-csharp-paging.md) projektu rychle zprovoznit. Tento projekt může být vlastní verzi nebo nainstalovat z Githubu: [Vytvoření první aplikace](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="set-model-properties-as-isfacetable"></a>Nastavení vlastností modelu jako IsFacetable

V pořadí pro vlastnosti modelu nacházely ve vyhledávání omezující vlastnost, musí být označená pomocí **IsFacetable**.

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

2. Jsme nebudou měnit všechny značky v rámci tohoto kurzu, takže zavřete soubor hotel.cs beze změny.

    > [!Note]
    > Omezující vlastnost vyhledávání vyvolá chybu, pokud není požadováno do vyhledávacího pole označené odpovídajícím způsobem.


## <a name="add-facet-navigation-to-your-app"></a>Přidání omezující vlastnost navigace do vaší aplikace

V tomto příkladu budeme umožňující uživateli vybrat jednu kategorii hotelu nebo jeden rekreačním ze seznamů odkazy uvedené nalevo od výsledky. Uživatel spustí tak, že některé zadáte hledaný text, pak můžete zúžit výsledky hledání tak, že vyberete kategorii a můžete zúžit výsledky dál tak, že vyberete rekreačním nebo mohou vybrat rekreačním první (pořadí není důležité).

Potřebujeme kontroleru k předání seznam omezující vlastnosti k zobrazení. Musíme udržovat volby uživatele v průběhu vyhledávání a znovu jako mechanismus pro zachování dat používáme dočasné úložiště.

![Chcete-li zúžit hledání "fondu" pomocí omezující vlastnost navigace](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="add-filter-strings-to-the-searchdata-model"></a>Přidat filtr řetězce do modelu SearchData

1. Otevřete soubor SearchData.cs a přidejte vlastnosti řetězce **SearchData** třídy pro uložení řetězce filtru omezující vlastnost.

    ```cs
        public string categoryFilter { get; set; }
        public string amenityFilter { get; set; }
    ```

### <a name="add-the-facet-action-method"></a>Přidejte metodu akce omezující vlastnost

Domácí kontrolér musí jednu novou akci, **omezující vlastnost**a aktualizace existujících **Index** a **stránky** akce, stejně jako aktualizace **RunQueryAsync**  metody.

1. Otevřete soubor domácí řadiče a přidejte **pomocí** příkaz, aby **seznamu&lt;řetězec&gt;**  vytvořit.

    ```cs
    using System.Collections.Generic;
    ```

2. Nahradit **indexu (SearchData model)** metody akce.

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

3. Nahradit **stránky (SearchData model)** metody akce.

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

4. Přidat **omezující vlastnosti (SearchData model)** metodě akce, chcete-li aktivovat, když uživatel klikne na odkaz omezující vlastnost. Model bude obsahovat vyhledávací filtr kategorií nebo rekreačním vyhledávací filtr. Třeba přidat ji po **stránky** akce.

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

### <a name="set-up-the-search-filter"></a>Nastavení vyhledávací filtr

Když uživatel vybere určité omezující vlastnost, například kliknutí na **možnost a Spa** kategorie a pak pouze hotely, které jsou definované jako tato kategorie má být vrácen ve výsledcích. Chcete-li zúžit hledání tak, musíme nastavit _filtr_.

1. Nahradit **RunQueryAsync** metodu s následujícím kódem. Především, přebírá řetězec filtru kategorie a řetězce filtru rekreačním a nastaví **filtr** parametr **parametrech vyhledávání**.

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

    Přidali jsme **kategorie** a **značky** vlastnosti do seznamu **vyberte** položek k vrácení. Toto přidání není vyžadována pro omezující vlastnost navigace pro práci, ale tyto informace používáme k ověření, že jsme se správně filtrování.

### <a name="add-lists-of-facet-links-to-the-view"></a>Přidat seznam omezující vlastnost odkazy na zobrazení

Zobrazení se bude vyžadovat významné změny. 

1. Začněte tím, že soubor hotels.css (ve složce wwwroot/css) a přidejte následující třídy.

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

2. Pro zobrazení uspořádání jsme výstup do tabulky, elegantně zarovnat omezující vlastnost seznamů na levé straně a výsledky na pravé straně. Otevřete soubor index.cshtml. Nahradí celý obsah HTML &lt;tělo&gt; značky s následujícím kódem.

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

    Všimněte si použití **Html.ActionLink** volání. Toto volání komunikuje platný filtr řetězce kontroleru, když uživatel klikne na odkaz omezující vlastnost. 

### <a name="run-and-test-the-app"></a>Spuštění a testování aplikace

Výhodou omezující vlastnost navigace uživateli je, že se můžete zúžit hledání jedním kliknutím, který jsme můžete zobrazit v tomto pořadí.

1. Spusťte aplikaci, zadejte "letiště" jako hledaný text. Ověřte, zda seznam omezující vlastnosti elegantně objeví na levé straně. Těchto omezujících vlastností jsou vše, co platí pro hotely, které mají "letiště" v textových dat, s počtem jak často k nim dojde.

    ![Chcete-li zúžit hledání "letiště" pomocí omezující vlastnost navigace](./media/tutorial-csharp-create-first-app/azure-search-facet-airport.png)

2. Klikněte na tlačítko **možnost a Spa** kategorie. Ověřte, že všechny výsledky jsou v této kategorii.

    ![Zúžení hledání "A možnost Spa"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras.png)

3. Klikněte na tlačítko **kontinentální části snídani** rekreačním. Ověřte, že všechny výsledky jsou stále v kategorii "A možnost Spa" s vybranou rekreačním.

    ![Zúžení hledání "kontinentální části snídani"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras-cb.png)

4. Zkuste vybrat jiné kategorie, pak jeden rekreačním a zobrazení zúžení výsledků. Opakujte naopak, jeden rekreačním a pak jednu kategorii.

    >[!Note]
    > Když se provádí jeden výběr v seznamu omezující vlastnosti (například kategorie) přepíše jakékoli předchozí výběr v seznamu kategorií.

## <a name="takeaways"></a>Shrnutí

Vezměte v úvahu následující takeaways z tohoto projektu:

* Je nutné pro každou vlastnost jako označení **IsFacetable**, pokud mají být součástí omezující vlastnost navigace.
* Omezující vlastnost navigace poskytuje uživatele s jednoduchou a intuitivní, způsob zúžení vyhledávání.
* Omezující vlastnost navigace je nejlepší rozdělený do částí (kategorie hotelu), vymoženosti hotelu, cena rozsahy adres, rozsahy hodnocení, atd., každá část s odpovídajícím názvem.

## <a name="next-steps"></a>Další postup

V dalším kurzu se podíváme na řazení výsledků. Do této chvíle jsou výsledky seřazeny jednoduše v pořadí, že se nacházejí v databázi.

> [!div class="nextstepaction"]
> [C#kurz: Azure Search výsledky - ORDER](tutorial-csharp-orders.md)
