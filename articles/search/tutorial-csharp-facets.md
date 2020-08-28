---
title: Kurz C# použití omezujících vlastností k usnadnění navigace
titleSuffix: Azure Cognitive Search
description: Pokračujte z "výsledků stránkování", chcete-li přidat omezující navigaci. Zjistěte, jak lze pomocí omezující vlastnosti snadno zúžit hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 06/20/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 0b51960418b311ed6170d6d060f3674a9a825808
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2020
ms.locfileid: "88998505"
---
# <a name="tutorial-add-faceted-navigation-using-the-net-sdk"></a>Kurz: Přidání naomezující navigace pomocí sady .NET SDK

Omezující vlastnosti se používají k podpoře navigace. poskytuje uživateli sadu odkazů, které se mají použít k zaměření hledání. Omezující vlastnosti jsou atributy dat (například kategorie nebo konkrétní funkce hotelu v našich ukázkových datech).

Tento kurz sestaví projekt stránkování vytvořený v [kurzu C#: stránkování výsledků hledání – kurz k Azure kognitivní hledání](tutorial-csharp-paging.md) .

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Nastavení vlastností modelu jako _tváře_
> * Přidání navigační vlastnosti do aplikace

## <a name="prerequisites"></a>Předpoklady

Pro absolvování tohoto kurzu je potřeba provést následující:

Kurz pro [C#: stránkování výsledků hledání – Azure kognitivní hledání](tutorial-csharp-paging.md) Project je spuštěný. Tento projekt může být buď vlastní verze, nebo ho můžete nainstalovat z GitHubu: [vytvořit první aplikaci](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="set-model-properties-as-isfacetable"></a>Nastavení vlastností modelu jako tváře

Aby se vlastnost modelu nacházela v hledání omezující vlastnosti, musí být označená pomocí třídy **Face**.

1. Prověřte třídu **hotelu** . **Kategorie** a **značky**jsou například označeny jako " **Faced**", ale **hotely** a **Description** nejsou. 

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

2. V rámci tohoto kurzu nebudeme měnit žádné značky, takže zavřete soubor hotel.cs beze změny.

    > [!Note]
    > Při hledání omezující vlastnosti dojde k chybě, pokud pole požadované ve vyhledávání není správně označeno.


## <a name="add-facet-navigation-to-your-app"></a>Přidání navigační vlastnosti do aplikace

V tomto příkladu umožníme uživateli vybrat jednu kategorii hotelu nebo jednu Amenity ze seznamu odkazů zobrazených nalevo od výsledků. Uživatel začne zadáním nějakého hledaného textu a pak může zúžit výsledky hledání tak, že vyberete kategorii a výsledky hledání můžete zúžit tak, že vyberete Amenity, nebo si můžou nejdřív vybrat Amenity (pořadí není důležité).

Potřebujeme, aby kontroler předal seznam omezujících vlastností do zobrazení. Musíme při hledání průběžně udržovat výběry uživatelů a znovu použít dočasné úložiště jako mechanismus pro zachování dat.

![Použití navigace omezující vlastnosti k zúžení hledání "fondu"](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="add-filter-strings-to-the-searchdata-model"></a>Přidání řetězců filtru do modelu SearchData

1. Otevřete soubor SearchData.cs a přidejte do třídy **SearchData** vlastnosti řetězce pro uložení řetězců filtru omezující vlastnosti.

    ```cs
        public string categoryFilter { get; set; }
        public string amenityFilter { get; set; }
    ```

### <a name="add-the-facet-action-method"></a>Přidání metody akce omezující vlastnosti

Ovladač Home vyžaduje jednu novou akci, **omezující vlastnost**a aktualizace svého existujícího **indexu** a akcí **stránky** a také aktualizace metody **RunQueryAsync** .

1. Otevřete soubor domovského kontroleru a přidejte příkaz **using** , chcete-li povolit konstrukci ** &lt; &gt; řetězce seznamu** .

    ```cs
    using System.Collections.Generic;
    ```

2. Nahraďte metodu akce **index (model SearchData)** .

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

3. Nahraďte metodu akce **Page (model SearchData)** .

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

4. Přidejte metodu akce **(model SearchData)** , která se aktivuje, když uživatel klikne na odkaz na omezující vlastnost. Model bude obsahovat buď filtr hledání kategorií, nebo filtr Amenity Search. Možná ji přidáte po akci **stránky** .

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

### <a name="set-up-the-search-filter"></a>Nastavení vyhledávacího filtru

Když uživatel vybere určitou omezující vlastnost, například klikne na kategorii kategorie **a Spa** , pak se do výsledků vrátí jenom hotely zadané jako tato kategorie. Abychom mohli hledání zúžit tímto způsobem, je potřeba nastavit _Filtr_.

1. Metodu **RunQueryAsync** nahraďte následujícím kódem. Primárně převezme řetězec filtru kategorie a řetězec filtru Amenity a nastaví parametr **filtru** pro **SearchParameters**.

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

    Přidali jsme vlastnosti **Category** a **Tags** do seznamu **vybraných** položek, které se mají vrátit. Tento dodatek není požadavkem na fungování navigace omezujícími vlastnostmi, ale tyto informace používáme k ověření, že je správně filtrované.

### <a name="add-lists-of-facet-links-to-the-view"></a>Přidání seznamů odkazů omezujících vlastností do zobrazení

Zobrazení bude vyžadovat některé významné změny. 

1. Začněte otevřením souboru hotely. CSS (ve složce Wwwroot/CSS) a přidejte následující třídy.

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

2. Pro zobrazení uspořádáme výstup do tabulky, aby se seznam omezujících vlastností na levé straně vyrovnal a výsledky na pravé straně. Otevřete soubor index. cshtml. Nahraďte celý obsah &lt; značek těla textu HTML &gt; následujícím kódem.

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

    Všimněte si použití volání **HTML. ActionLink** . Tento hovor komunikuje s platnými řetězci filtru, když uživatel klikne na odkaz na omezující vlastnost. 

### <a name="run-and-test-the-app"></a>Spuštění a otestování aplikace

Výhodou navigace omezujícími vlastnostmi uživatele je, že může zúžit hledání jediným kliknutím, které můžeme zobrazit v následujícím pořadí.

1. Spusťte aplikaci a jako text hledání zadejte "letiště". Ověřte, že se seznam omezujících vlastností zobrazuje na levé straně. Všechny tyto omezující vlastnosti platí pro hotely, které mají v textových datech "letiště", a to s počtem četnosti výskytů.

    ![Použití navigace omezující vlastnosti k zúžení hledání "letiště"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport.png)

2. Klikněte na kategorii **středisko a Spa** . Ověřte, že všechny výsledky jsou v této kategorii.

    ![Zúžení hledání na "možnosti a Spa"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras.png)

3. Klikněte na **kontinentální Amenity snídaně** . Ověřte, že všechny výsledky jsou stále v kategorii "kategorie" a "Spa" a vybrané Amenity.

    ![Zúžení hledání na "kontinentální snídaně"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras-cb.png)

4. Zkuste vybrat jinou kategorii, jednu Amenity a zobrazit zúžené výsledky. Pak zkuste jiný způsob, jak o jeden Amenity, tak o jednu kategorii.

    >[!Note]
    > Když se jeden výběr vytvoří v seznamu omezujících vlastností (například kategorie), přepíše jakýkoli předchozí výběr v seznamu kategorií.

## <a name="takeaways"></a>Shrnutí

Vezměte v úvahu následující poznatky z tohoto projektu:

* Je nutné označit jednotlivé vlastnosti jako vlastnost- **Face**, pokud mají být zahrnuty v navigaci omezující vlastnosti.
* Navigace omezující vlastnosti poskytuje uživateli jednoduchý a intuitivní způsob zúžení hledání.
* Navigace omezující vlastnosti je nejlépe rozdělená na oddíly (kategorie hotelů, rekreačního hotelu, cenové rozsahy, rozsahy hodnocení atd.), každý oddíl s odpovídajícím nadpisem.

## <a name="next-steps"></a>Další kroky

V dalším kurzu se podíváme na výsledky řazení. Do tohoto okamžiku jsou výsledky seřazené jednoduše v pořadí, ve kterém se nacházejí v databázi.

> [!div class="nextstepaction"]
> [Kurz C#: seřazení výsledků – Azure Kognitivní hledání](tutorial-csharp-orders.md)
