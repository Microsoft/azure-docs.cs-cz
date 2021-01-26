---
title: Kurz C# použití omezujících vlastností k usnadnění navigace
titleSuffix: Azure Cognitive Search
description: Pokračujte z "výsledků stránkování", chcete-li přidat omezující navigaci. Zjistěte, jak lze pomocí omezující vlastnosti snadno zúžit hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/26/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 44a05ed5b4386f2787a1c84dfeb61a5d539c7cb5
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98789799"
---
# <a name="tutorial-add-faceted-navigation-using-the-net-sdk"></a>Kurz: Přidání naomezující navigace pomocí sady .NET SDK

Omezující vlastnosti umožňují navigaci s sebou samým, protože poskytují sadu odkazů pro filtrování výsledků. V tomto kurzu je napravovaná navigační struktura umístěná na levé straně stránky s popisky a kliknutím na text pro oříznutí výsledků.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Nastavení vlastností modelu jako _tváře_
> * Přidání navigační vlastnosti do aplikace

## <a name="overview"></a>Přehled

Omezující vlastnosti jsou založené na polích indexu vyhledávání. Požadavek dotazu, který obsahuje omezující vlastnost = [String], poskytuje pole omezující vlastnosti. Je běžné zahrnout více omezujících vlastností, například `&facet=category&facet=amenities` každou jednu, oddělenou znakem ampersand (&). Implementace omezující navigační struktury vyžaduje, abyste určili omezující vlastnosti i filtry. Filtr se používá pro událost Click k zúžení výsledků. Například kliknutím na "rozpočet" vyfiltrujete výsledky na základě těchto kritérií.

Tento kurz rozšiřuje projekt stránkování vytvořený v kurzu [Přidat stránkování do výsledků hledání](tutorial-csharp-paging.md) .

Hotovou verzi kódu v tomto kurzu najdete v následujícím projektu:

* [4 – Přidání vlastností – navigace (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/4-add-facet-navigation)

## <a name="prerequisites"></a>Požadavky

* [2a – řešení pro přidání stránkování (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2a-add-paging) . Tento projekt může být vlastní verze sestavená z předchozího kurzu nebo kopie z GitHubu.

Tento kurz byl aktualizován tak, aby používal balíček [Azure.Search.Documents (verze 11)](https://www.nuget.org/packages/Azure.Search.Documents/) . Starší verzi sady .NET SDK najdete v tématu [Ukázka kódu Microsoft. Azure. Search (verze 10)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10).

## <a name="set-model-properties-as-isfacetable"></a>Nastavení vlastností modelu jako tváře

Aby se vlastnost modelu nacházela v hledání omezující vlastnosti, musí být označená pomocí třídy **Face**.

1. Prověřte třídu **hotelu** . **Kategorie** a **značky** jsou například označeny jako " **Faced**", ale **hotely** a **Description** nejsou. 

    ```cs
    public partial class Hotel
    {
        [SimpleField(IsFilterable = true, IsKey = true)]
        public string HotelId { get; set; }

        [SearchableField(IsSortable = true)]
        public string HotelName { get; set; }

        [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
        public string Description { get; set; }

        [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrLucene)]
        [JsonPropertyName("Description_fr")]
        public string DescriptionFr { get; set; }

        [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
        public string Category { get; set; }

        [SearchableField(IsFilterable = true, IsFacetable = true)]
        public string[] Tags { get; set; }

        [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
        public bool? ParkingIncluded { get; set; }

        [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
        public DateTimeOffset? LastRenovationDate { get; set; }

        [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
        public double? Rating { get; set; }

        public Address Address { get; set; }

        [SimpleField(IsFilterable = true, IsSortable = true)]
        public GeographyPoint Location { get; set; }

        public Room[] Rooms { get; set; }
    }
    ```

1. V rámci tohoto kurzu nebudeme měnit žádné značky, takže zavřete soubor hotel.cs beze změny.

    > [!Note]
    > Při hledání omezující vlastnosti dojde k chybě, pokud pole požadované ve vyhledávání není správně označeno.

## <a name="add-facet-navigation-to-your-app"></a>Přidání navigační vlastnosti do aplikace

V tomto příkladu umožníme uživateli vybrat jednu kategorii hotelu nebo jednu Amenity ze seznamu odkazů zobrazených nalevo od výsledků. Uživatel začne zadáním nějakého hledaného textu a pak postupně zúžit výsledky hledání tak, že vybere kategorii nebo Amenity.

Je to úloha kontroleru, která předáte seznam omezujících vlastností do zobrazení. Pokud chcete zachovat výběry uživatelů při hledání, používáme jako mechanismus pro zachování stavu dočasné úložiště.

![Použití navigace omezující vlastnosti k zúžení hledání "fondu"](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="add-filter-strings-to-the-searchdata-model"></a>Přidání řetězců filtru do modelu SearchData

1. Otevřete soubor SearchData.cs a přidejte do třídy **SearchData** vlastnosti řetězce pro uložení řetězců filtru omezující vlastnosti.

    ```cs
    public string categoryFilter { get; set; }
    public string amenityFilter { get; set; }
    ```

### <a name="add-the-facet-action-method"></a>Přidání metody akce omezující vlastnosti

Ovladač Home vyžaduje jednu novou akci, **omezující vlastnost** a aktualizace svého existujícího **indexu** a akcí **stránky** a metody **RunQueryAsync** .

<!-- 1. Open the home controller file, and add the **using** statement, to enable the **List&lt;string&gt;** construct.

    ```cs
    using System.Collections.Generic; 
    ```-->

1. Nahraďte metodu akce **index (model SearchData)** .

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
            await RunQueryAsync(model, 0, 0, "", "").ConfigureAwait(false);
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "1" });
        }

        return View(model);
    }
    ```

1. Nahraďte metodu akce **PageAsync (model SearchData)** .

    ```cs
    public async Task<ActionResult> PageAsync(SearchData model)
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

1. Přidejte metodu akce **FacetAsync (model SearchData)** , která se aktivuje, když uživatel klikne na odkaz na omezující vlastnost. Model bude obsahovat buď kategorii, nebo vyhledávací filtr Amenity. Přidejte ho za akci **PageAsync** .

    ```cs
    public async Task<ActionResult> FacetAsync(SearchData model)
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
            await RunQueryAsync(model, 0, 0, catFilter, ameFilter).ConfigureAwait(false);
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

1. Metodu **RunQueryAsync** nahraďte následujícím kódem. Primárně převezme řetězec filtru kategorie a řetězec filtru Amenity a nastaví parametr **filtru** pro **příznacích searchOptions jsou**.

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

        var options = new SearchOptions
        {
            Filter = facetFilter,

            SearchMode = SearchMode.All,

            // Skip past results that have already been returned.
            Skip = page * GlobalVariables.ResultsPerPage,

            // Take only the next page worth of results.
            Size = GlobalVariables.ResultsPerPage,

            // Include the total number of results.
            IncludeTotalCount = true,
        };

        // Return information on the text, and number, of facets in the data.
        options.Facets.Add("Category,count:20");
        options.Facets.Add("Tags,count:20");

        // Enter Hotel property names into this list, so only these values will be returned.
        options.Select.Add("HotelName");
        options.Select.Add("Description");
        options.Select.Add("Category");
        options.Select.Add("Tags");

        // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
        model.resultList = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);

        // This variable communicates the total number of pages to the view.
        model.pageCount = ((int)model.resultList.TotalCount + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

        // This variable communicates the page number being displayed to the view.
        model.currentPage = page;

        // Calculate the range of page numbers to display.
        if (page == 0)
        {
            leftMostPage = 0;
        }
        else if (page <= leftMostPage)
        {
            // Trigger a switch to a lower page range.
            leftMostPage = Math.Max(page - GlobalVariables.PageRangeDelta, 0);
        }
        else if (page >= leftMostPage + GlobalVariables.MaxPageRange - 1)
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

    Všimněte si, že vlastnosti **kategorie** a **značky** jsou přidány do seznamu **vybraných** položek, které mají být vráceny. Tento dodatek není požadavkem na fungování navigace omezujícími vlastnostmi, ale tyto informace používáme k ověření, že filtry fungují správně.

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

1. Pro zobrazení uspořádejte výstup do tabulky, aby bylo možné v levé horní části seznamu omezující vlastnosti, a výsledky na pravé straně. Otevřete soubor index. cshtml. Nahraďte celý obsah &lt; značek těla textu HTML &gt; následujícím kódem.

    ```html
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
                                                @Html.ActionLink(facetLink, "FacetAsync", "Home", new { categoryFilter = $"Category eq '{categories[c]}'" }, null)
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
                                                @Html.ActionLink(facetLink, "FacetAsync", "Home", new { amenityFilter = $"Tags/any(t: t eq '{tags[c]}')" }, null)
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
                                    @Model.resultList.TotalCount Results
                                </p>

                                var results = Model.resultList.GetResults().ToList();

                                @for (var i = 0; i < results.Count; i++)
                                {
                                    string amenities = string.Join(", ", results[i].Document.Tags);

                                    string fullDescription = results[i].Document.Description;
                                    fullDescription += $"\nCategory: {results[i].Document.Category}";
                                    fullDescription += $"\nAmenities: {amenities}";


                                    // Display the hotel name and description.
                                    @Html.TextAreaFor(m => results[i].Document.HotelName, new { @class = "box1" })
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
                                                @Html.ActionLink("|<", "PageAsync", "Home", new { paging = "0" }, null)
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
                                                @Html.ActionLink("<", "PageAsync", "Home", new { paging = "prev" }, null)
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
                                                    @Html.ActionLink((pn + 1).ToString(), "PageAsync", "Home", new { paging = @pn }, null)
                                                </p>
                                            }
                                        </td>
                                    }

                                    <td class="tdPage">
                                        @if (Model.currentPage < Model.pageCount - 1)
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink(">", "PageAsync", "Home", new { paging = "next" }, null)
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
                                                @Html.ActionLink(">|", "PageAsync", "Home", new { paging = Model.pageCount - 1 }, null)
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

1. Klikněte na kategorii **středisko a Spa** . Ověřte, že všechny výsledky jsou v této kategorii.

    ![Zúžení hledání na "možnosti a Spa"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras.png)

1. Klikněte na **kontinentální Amenity snídaně** . Ověřte, že všechny výsledky jsou stále v kategorii "kategorie" a "Spa" a vybrané Amenity.

    ![Zúžení hledání na "kontinentální snídaně"](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras-cb.png)

1. Zkuste vybrat jinou kategorii, jednu Amenity a zobrazit zúžené výsledky. Pak zkuste jiný způsob, jak o jeden Amenity, tak o jednu kategorii. Chcete-li obnovit stránku, odešlete prázdné vyhledávání.

    >[!Note]
    > Když se jeden výběr vytvoří v seznamu omezujících vlastností (například kategorie), přepíše jakýkoli předchozí výběr v seznamu kategorií.

## <a name="takeaways"></a>Shrnutí

Vezměte v úvahu následující poznatky z tohoto projektu:

* Je naprosto nutné označit každé pole plošky **vlastnostmi Face pro** zahrnutí v navigaci omezující vlastnosti.
* Omezující vlastnosti jsou kombinovány s filtry, aby se snížily výsledky.
* Omezující vlastnosti jsou kumulativní a jednotlivé výběry se sestavují v předchozím případě k lepším úzkým výsledkům.

## <a name="next-steps"></a>Další kroky

V dalším kurzu se podíváme na výsledky řazení. Do tohoto okamžiku jsou výsledky seřazené jednoduše v pořadí, ve kterém se nacházejí v databázi.

> [!div class="nextstepaction"]
> [Kurz C#: seřazení výsledků – Azure Kognitivní hledání](tutorial-csharp-orders.md)
