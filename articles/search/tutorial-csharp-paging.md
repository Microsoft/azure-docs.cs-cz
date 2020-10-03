---
title: Kurz C# o stránkování výsledků hledání
titleSuffix: Azure Cognitive Search
description: Přidejte do výsledků hledání stránkování a navigační tlačítka a sestavte stávající projekt hotelů a přidejte tak tlačítka First, Next, Previous, Last a číslovaných tlačítek. Druhý stránkovací systém používá nekonečné posouvání, aktivované přesunutím svislého posuvníku na jeho dolní limit.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 09/30/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 8dfc69bf251a811363426a3aeca7379d18458b47
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91667227"
---
# <a name="tutorial-add-paging-to-search-results-using-the-net-sdk"></a>Kurz: Přidání stránkování do výsledků hledání pomocí sady .NET SDK

Naučte se implementovat dva různé systémy stránkování, první v závislosti na číslech stránek a druhá při nekonečné posouvání. Často se používají oba systémy stránkování a výběr pravého tlačítka závisí na uživatelském prostředí, které chcete s výsledky. 

V tomto kurzu získáte informace o těchto tématech:
> [!div class="checklist"]
> * Rozšiřování aplikace pomocí číslovaného stránkování
> * Rozšiřování aplikace pomocí nekonečné posouvání

## <a name="overview"></a>Přehled

V tomto kurzu se překrývá stránkovací systém do dříve vytvořeného projektu popsaného v kurzu [Vytvoření prvního vyhledávacího aplikace](tutorial-csharp-create-first-app.md) .

Verze kódu, který budete vyvíjet v tomto kurzu, najdete v následujících projektech:

* [2a – přidání-stránkování (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2a-add-paging)

* [2b – přidat-nekonečné – Scroll (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2b-add-infinite-scroll)

## <a name="prerequisites"></a>Požadované součásti

* [1 – Basic-Search – projekt stránky (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/1-basic-search-page) . Tento projekt může být vlastní verze sestavená z předchozího kurzu nebo kopie z GitHubu.

Tento kurz byl aktualizován tak, aby používal balíček [Azure.Search.Documents (verze 11)](https://www.nuget.org/packages/Azure.Search.Documents/) . Starší verzi sady .NET SDK najdete v tématu [Ukázka kódu Microsoft. Azure. Search (verze 10)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10).

## <a name="extend-your-app-with-numbered-paging"></a>Rozšiřování aplikace pomocí číslovaného stránkování

Číslované stránkování je stránkovací systém, který je vhodný pro hlavní obchodní vyhledávací weby a mnoho dalších webů hledání. Očíslované stránkování obvykle zahrnuje možnost "Další" a "předchozí" kromě rozsahu skutečných čísel stránek. Také může být k dispozici možnost "první stránka" a "Poslední stránka". Tyto možnosti jistě poskytují uživatelský ovládací prvek pro procházení výsledků na základě stránky.

V tomto kurzu přidáte systém, který obsahuje možnosti první, předchozí, další a poslední, spolu s čísly stránek, která nezačínají od 1, ale místo toho se uživatel bude začínat (například pokud se na stránce zobrazuje stránka 10, možná číslo stránky 8, 9, 10, 11 a 12).

Systém bude dostatečně flexibilní, aby bylo možné nastavit počet zobrazených čísel stránek v globální proměnné.

Systém bude zacházet s tlačítky na nejvyšší úrovni vlevo a vpravo (jako speciální), což znamená, že budou aktivovat změnu rozsahu zobrazených čísel stránek. Například pokud se zobrazí čísla stránky 8, 9, 10, 11 a 12 a uživatel klikne na 8, pak se rozsah zobrazených čísel stránek změní na 6, 7, 8, 9 a 10. A v případě, že je vybrána možnost 12, existuje podobný posun doprava.

### <a name="add-paging-fields-to-the-model"></a>Přidání polí stránkování do modelu

Otevřete základní řešení pro stránku vyhledávání.

1. Otevřete soubor modelu SearchData.cs.

1. Přidejte globální proměnné pro podporu stránkování. V MVC jsou globální proměnné deklarovány ve vlastní statické třídě. **ResultsPerPage** nastaví počet výsledků na stránce. **MaxPageRange** určuje počet viditelných čísel stránek v zobrazení. **PageRangeDelta** určuje, kolik stránek by se mělo posunout doleva nebo doprava, když se vybere číslo stránky nejvíce vlevo nebo vpravo. Toto druhé číslo je obvykle kolem poloviny **MaxPageRange**. Do oboru názvů přidejte následující kód.

    ```csharp
    public static class GlobalVariables
    {
        public static int ResultsPerPage
        {
            get
            {
                return 3;
            }
        }
        public static int MaxPageRange
        {
            get
            {
                return 5;
            }
        }

        public static int PageRangeDelta
        {
            get
            {
                return 2;
            }
        }
    }
    ```

    >[!Tip]
    >Pokud tento projekt spouštíte na zařízení, které má menší obrazovku, jako je třeba přenosný počítač, zvažte změnu **ResultsPerPage** na 2.

1. Přidejte vlastnosti stránkování do třídy **SearchData** po vlastnosti **prohledávanýtext** .

    ```csharp
    // The current page being displayed.
    public int currentPage { get; set; }

    // The total number of pages of results.
    public int pageCount { get; set; }

    // The left-most page number to display.
    public int leftMostPage { get; set; }

    // The number of page numbers to display - which can be less than MaxPageRange towards the end of the results.
    public int pageRange { get; set; }

    // Used when page numbers, or next or prev buttons, have been selected.
    public string paging { get; set; }
    ```

### <a name="add-a-table-of-paging-options-to-the-view"></a>Přidání tabulky možností stránkování do zobrazení

1. Otevřete soubor index. cshtml a přidejte následující kód přímo před uzavírací &lt; &gt; značku/body. Tento nový kód představuje tabulku možností stránkování: první, předchozí, 1, 2, 3, 4, 5, další, poslední.

    ```html
    @if (Model != null && Model.pageCount > 1)
    {
    // If there is more than one page of results, show the paging buttons.
    <table>
        <tr>
            <td>
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

            <td>
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
                <td>
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

            <td>
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

            <td>
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
    ```

    K zarovnání věcí používáme tabulku HTML. Nicméně všechny akce pocházejí z @Html.ActionLink příkazů, každé volání kontroleru s **novým** modelem vytvořeným s různými položkami do vlastnosti **stránkování** , kterou jsme přidali dříve.

    První a poslední stránka neodesílají řetězce, například "First" a "Last", ale místo toho odesílají správná čísla stránek.

1. Přidejte třídy stránkování do seznamu stylů HTML v souboru hotely. CSS. Třída **pageSelected** je zde k identifikaci aktuální stránky (pomocí tučného formátu na číslo stránky) v seznamu čísel stránek.

    ```html
    .pageButton {
        border: none;
        color: darkblue;
        font-weight: normal;
        width: 50px;
    }

    .pageSelected {
        border: none;
        color: black;
        font-weight: bold;
        width: 50px;
    }

    .pageButtonDisabled {
        border: none;
        color: lightgray;
        font-weight: bold;
        width: 50px;
    }
    ```

### <a name="add-a-page-action-to-the-controller"></a>Přidat akci stránky do kontroleru

1. Otevřete soubor HomeController.cs a přidejte akci **PageAsync** . Tato akce odpoví na kteroukoli z vybraných možností stránky.

    ```csharp
    public async Task<ActionResult> PageAsync(SearchData model)
    {
        try
        {
            int page;

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

            // Recover the search text and search for the data for the new page.
            model.searchText = TempData["searchfor"].ToString();

            await RunQueryAsync(model, page, leftMostPage);

            // Ensure Temp data is stored for next call, as TempData only stores for one call.
            TempData["page"] = (object)page;
            TempData["searchfor"] = model.searchText;
            TempData["leftMostPage"] = model.leftMostPage;
        }

        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "2" });
        }
        return View("Index", model);
    }
    ```

    Metoda **RunQueryAsync** nyní zobrazí syntaktickou chybu, protože se jedná o třetí parametr, ke kterému budeme přijít v bitu.

    > [!Note]
    > Volání do **TempData** ukládají hodnotu ( **objekt**) v dočasném úložišti, i když toto úložiště přetrvává _pouze_ pro jedno volání. Pokud ukládáme něco do dočasných dat, bude k dispozici pro další volání akce kontroleru, ale po této akci bude toto volání nejvíce neomezené. V důsledku tohoto krátkého intervalu ukládáme do dočasného úložiště vlastnosti hledání textu a stránkování a každé volání **PageAsync**.

1. Aktualizujte akci **index (model)** na ukládání dočasných proměnných a přidejte do volání **RunQueryAsync** parametr stránky na levé straně.

    ```csharp
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
            await RunQueryAsync(model, 0, 0);

            // Ensure temporary data is stored for the next call.
            TempData["page"] = 0;
            TempData["leftMostPage"] = 0;
            TempData["searchfor"] = model.searchText;
        }

        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "1" });
        }
        return View(model);
    }
    ```

1. Metoda **RunQueryAsync** , představená v předchozí lekci, potřebuje úpravu k vyřešení chyby syntaxe. Pomocí polí **Skip**, **Size**a **IncludeTotalCount** třídy [**příznacích searchOptions jsou**](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchoptions) si vyžádáme jenom jednu stránku s výsledky, počínaje nastavením **Skip** . Pro naše zobrazení je také potřeba vypočítat stránkovací proměnné. Nahraďte celou metodu následujícím kódem.

    ```csharp
    private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage)
    {
        InitSearch();

        var options = new SearchOptions
        {
            // Skip past results that have already been returned.
            Skip = page * GlobalVariables.ResultsPerPage,

            // Take only the next page worth of results.
            Size = GlobalVariables.ResultsPerPage,

            // Include the total number of results.
            IncludeTotalCount = true
        };

        // Add fields to include in the search results.
        options.Select.Add("HotelName");
        options.Select.Add("Description");

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

        return View("Index", model);
    }
    ```

1. Nakonec udělejte v zobrazení malou změnu. Proměnná **resultList. Results. TotalCount** nyní bude obsahovat počet výsledků vrácených na jedné stránce (3 v našem příkladu), ne celkové číslo. Vzhledem k tomu, že **IncludeTotalCount** nastavíme na hodnotu true, proměnná **resultList. TotalCount** nyní obsahuje celkový počet výsledků. Proto Najděte, kde se v zobrazení zobrazí počet výsledků, a změňte ji na následující kód.

    ```csharp
    // Show the result count.
    <p class="sampleText">
        @Model.resultList.TotalCount Results
    </p>

    var results = Model.resultList.GetResults().ToList();

    @for (var i = 0; i < results.Count; i++)
    {
        // Display the hotel name and description.
        @Html.TextAreaFor(m => results[i].Document.HotelName, new { @class = "box1" })
        @Html.TextArea($"desc{1}", results[i].Document.Description, new { @class = "box2" })
    }
    ```

    > [!Note]
    > Při nastavování **IncludeTotalCount** na hodnotu true se dosáhlo menšího výkonu, protože tento součet je potřeba vypočítat pomocí kognitivní hledání Azure. U složitých datových sad se zobrazí upozornění, že vrácená hodnota je _aproximace_. Vzhledem k tomu, že hledání hotelů corpus je malé, bude přesné.

### <a name="compile-and-run-the-app"></a>Zkompilovat a spustit aplikaci

Nyní vyberte možnost **Spustit bez ladění** (nebo stiskněte klávesu F5).

1. Vyhledejte řetězec, který vrátí mnoho výsledků (například "WiFi"). Můžete stránku na základě výsledků zamezit.

    ![Očíslované stránkování prostřednictvím "fondu" výsledků](./media/tutorial-csharp-create-first-app/azure-search-numbered-paging.png)

1. Zkuste kliknout na nejvíc vpravo a později na všechna čísla stránek, která jsou nejvíce vlevo. Přizpůsobte si čísla stránek odpovídajícím způsobem centrování stránky, na které jste připojeni?

1. Jsou možnosti "první" a "poslední" užitečné? Některé komerční vyhledávací weby tyto možnosti používají a jiné ne.

1. Přejít na poslední stránku výsledků. Poslední stránka je jediná stránka, která může obsahovat méně než **ResultsPerPage** výsledků.

    ![Prozkoumání poslední stránky "WiFi"](./media/tutorial-csharp-create-first-app/azure-search-pool-last-page.png)

1. Zadejte "město" a klikněte na Hledat. Pokud jsou výsledky menší než jedna stránka, nezobrazí se žádné možnosti stránkování.

    ![Hledání "města"](./media/tutorial-csharp-create-first-app/azure-search-town.png)

Uložte tento projekt a pokračujte do další části pro alternativní forma stránkování.

## <a name="extend-your-app-with-infinite-scrolling"></a>Rozšiřování aplikace pomocí nekonečné posouvání

Nekonečná posouvání se aktivuje, když uživatel posune svislý posuvník na poslední zobrazené výsledky. V tomto případě je pro další stránku výsledků volání služby vyhledávání provedeno. Pokud neexistují žádné další výsledky, není nic vráceno a svislý posuvník se nemění. Pokud je k dispozici více výsledků, připojí se k aktuální stránce a posuvník se změní, aby bylo vidět, že jsou k dispozici další výsledky.

Důležité je Ukázat, že aktuální stránka není nahrazena, ale spíše rozšířená, aby se zobrazily další výsledky. Uživatel může vždycky přejít zpět na první výsledky hledání.

Chcete-li implementovat nekonečné posouvání, začněte s projektem před přidáním prvků pro posouvání čísla stránky. Na GitHubu je to řešení [FirstAzureSearchApp](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/1-basic-search-page) .

### <a name="add-paging-fields-to-the-model"></a>Přidání polí stránkování do modelu

1. Nejprve přidejte vlastnost **stránkování** do třídy **SearchData** (v souboru modelu SearchData.cs).

    ```csharp
    // Record if the next page is requested.
    public string paging { get; set; }
    ```

    Tato proměnná je řetězec, který obsahuje "Next", pokud by se měla odeslat další stránka výsledků, nebo pro první stránku hledání mít hodnotu null.

1. Do stejného souboru a v rámci oboru názvů přidejte globální proměnnou třídy s jednou vlastností. V MVC jsou globální proměnné deklarovány ve vlastní statické třídě. **ResultsPerPage** nastaví počet výsledků na stránce. 

    ```csharp
    public static class GlobalVariables
    {
        public static int ResultsPerPage
        {
            get
            {
                return 3;
            }
        }
    }
    ```

### <a name="add-a-vertical-scroll-bar-to-the-view"></a>Přidat svislý posuvník do zobrazení

1. Vyhledejte část souboru index. cshtml, který zobrazuje výsledky (začíná na ** @if (model! = null)**).

1. Nahraďte oddíl následujícím kódem. Nový oddíl ** &lt; div &gt; ** je okolo oblasti, která by se měla procházet, a přidá jak atribut **přetečení** , tak i **volání funkce pro posouvání s** názvem "scrolled ()", například.

    ```csharp
    @if (Model != null)
    {
        // Show the result count.
        <p class="sampleText">
            @Model.resultList.TotalCount Results
        </p>

        var results = Model.resultList.GetResults().ToList();

        <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

            <!-- Show the hotel data. -->
            @for (var i = 0; i < results.Count; i++)
            {
                // Display the hotel name and description.
                @Html.TextAreaFor(m => results[i].Document.HotelName, new { @class = "box1" })
                @Html.TextArea($"desc{i}", results[i].Document.Description, new { @class = "box2" })
            }
    ```

1. Přímo pod smyčkou, za &lt; &gt; značkou/div přidejte **posunutou** funkci.

    ```javascript
    <script>
        function scrolled() {
            if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                $.getJSON("/Home/NextAsync", function (data) {
                    var div = document.getElementById('myDiv');

                    // Append the returned data to the current list of hotels.
                    for (var i = 0; i < data.length; i += 2) {
                        div.innerHTML += '\n<textarea class="box1">' + data[i] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box2">' + data[i + 1] + '</textarea>';
                    }
                });
            }
        }
    </script>
    ```

    Příkaz **if** ve skriptu výše testuje, zda se uživatel přesunul do dolní části svislého posuvníku. Pokud mají, volání do **domovského** kontroleru se provede v akci s názvem **NextAsync**. Kontroler nevyžaduje žádné další informace, vrátí další stránku dat. Tato data se pak naformátují pomocí identických stylů HTML jako původní stránky. Pokud se nevrátí žádné výsledky, nic se nepřipojí a co se stane.

### <a name="handle-the-next-action"></a>Zpracování další akce

Existují pouze tři akce, které je třeba odeslat do kontroleru: první spuštění aplikace, která volá **index ()**, první hledání uživatelem, který volá **index (model)**, a následné volání pro další výsledky prostřednictvím **Next (model)**.

1. Otevřete soubor domovského kontroleru a odstraňte metodu **RunQueryAsync** z původního kurzu.

1. Nahraďte akci **indexu (modelu)** následujícím kódem. Nyní zpracovává pole **stránkování** , pokud je null nebo nastaveno na "Další" a zpracovává volání do Azure kognitivní hledání.

    ```csharp
    public async Task<ActionResult> Index(SearchData model)
    {
        try
        {
            InitSearch();

            int page;

            if (model.paging != null && model.paging == "next")
            {
                // Increment the page.
                page = (int)TempData["page"] + 1;

                // Recover the search text.
                model.searchText = TempData["searchfor"].ToString();
            }
            else
            {
                // First call. Check for valid text input.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }
                page = 0;
            }

            // Setup the search parameters.
            var options = new SearchOptions
            {
                SearchMode = SearchMode.All,

                // Skip past results that have already been returned.
                Skip = page * GlobalVariables.ResultsPerPage,

                // Take only the next page worth of results.
                Size = GlobalVariables.ResultsPerPage,

                // Include the total number of results.
                IncludeTotalCount = true
            };

            // Specify which fields to include in results.
            options.Select.Add("HotelName");
            options.Select.Add("Description");

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);               

            // Ensure TempData is stored for the next call.
            TempData["page"] = page;
            TempData["searchfor"] = model.searchText;
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "1" });
        }

        return View("Index", model);
    }
    ```

    Podobně jako u očíslované metody stránkování používáme nastavení vyhledávání **Skip** a **Size** k vyžádání pouze těch dat, která potřebujeme.

1. Přidejte akci **NextAsync** do domovského kontroleru. Všimněte si, jak vrátí seznam a každý Hotel přidávají dva prvky do seznamu: název hotelu a popis hotelu. Tento formát je nastavený tak, aby se **shodoval s použitím** vrácených dat v zobrazení.

    ```csharp
    public async Task<ActionResult> NextAsync(SearchData model)
    {
        // Set the next page setting, and call the Index(model) action.
        model.paging = "next";
        await Index(model).ConfigureAwait(false);

        // Create an empty list.
        var nextHotels = new List<string>();

        // Add a hotel name, then description, to the list.
        await foreach (var searchResult in model.resultList.GetResultsAsync())
        {
            nextHotels.Add(searchResult.Document.HotelName);
            nextHotels.Add(searchResult.Document.Description);
        }

        // Rather than return a view, return the list of data.
        return new JsonResult(nextHotels);
    }
    ```

1. Pokud se zobrazí chyba syntaxe v ** &lt; řetězci &gt; seznamu**, přidejte následující direktivu **using** do hlavičky souboru kontroleru.

    ```csharp
    using System.Collections.Generic;
    ```

### <a name="compile-and-run-your-project"></a>Zkompilovat a spustit projekt

Nyní vyberte možnost **Spustit bez ladění** (nebo stiskněte klávesu F5).

1. Zadejte termín, který poskytne dostatek výsledků (například "fond") a potom otestuje svislý posuvník. Aktivuje se nová stránka s výsledky?

    ![Nekonečná posouvání prostřednictvím "fondu" výsledků](./media/tutorial-csharp-create-first-app/azure-search-infinite-scroll.png)

    > [!Tip]
    > Aby se zajistilo, že se na první stránce zobrazí posuvník, první stránka výsledků musí mírně překročit výšku oblasti, ve které se zobrazují. V našem příkladu má **box1** výšku 30 pixelů **. box2** má výšku 100 pixelů _a_ spodní okraj 24 pixelů. Každá položka tedy používá 154 pixelů. Tři položky budou zabírat 3 x 154 = 462 pixelů. Aby se zajistilo, že se zobrazí svislý posuvník, musí být výška na zobrazované oblasti nastavená tak, aby byla menší než 462 pixelů, i když 461 funguje. K tomuto problému dochází pouze na první stránce, poté, co se zobrazí posuvník. Řádek, který se má aktualizovat: ** &lt; div ID = "myDiv" Style = "width: 800px; Height: 450px; přetečení-y: Scroll;" proscroll = "scrolled ( &gt; )"**.

1. Posuňte se dolů k dolnímu okraji výsledků. Všimněte si, že všechny informace jsou nyní na jedné stránce zobrazení. Můžete se přesměrovat zpět na začátek, aniž by se aktivovala žádná volání serveru.

Sofistikované nekonečné systémy posouvání můžou použít kolečko myši nebo podobný jiný mechanismus, aby se aktivovalo načítání nové stránky výsledků. V těchto kurzech ještě nebudeme nic přepínat, ale k tomu má určitý ovládací tlačítko, protože se vyhnete dalším klikáním myší a možná budete chtít další možnosti prozkoumat.

## <a name="takeaways"></a>Shrnutí

Vezměte v úvahu následující poznatky z tohoto projektu:

* Číslované stránkování je užitečné pro hledání, kde pořadí výsledků je trochu libovolné, což znamená, že uživatelé můžou na pozdějších stránkách něco zajímat.
* Nekonečné posouvání je užitečné v případě, že je pořadí výsledků obzvláště důležité. Například pokud jsou výsledky seřazené na vzdálenost od středu cílového města.
* Číslované stránkování umožňuje lepší navigaci. Uživatel si například může pamatovat, že na stránce 6 byl zajímavý výsledek, protože žádný takový jednoduchý odkaz neexistuje v nekonečném posouvání.
* Nekonečné posouvání má jednoduchý způsob odvolání, posun nahoru a dolů bez čísel stránek pro kliknutí.
* Klíčovou funkcí nekonečné posouvání je, že výsledky se připojí k existující stránce a nenahrazují tuto stránku, což je efektivní.
* Dočasné úložiště uchovává pouze jedno volání a musí být obnoveno, aby se zadrželo další volání.

## <a name="next-steps"></a>Další kroky

Stránkování je základem pro vyhledávání. V případě dobře krytého stránkování je dalším krokem lepší zlepšení uživatelského prostředí tím, že se přidají hledání typu dopředu.

> [!div class="nextstepaction"]
> [Kurz C#: přidání automatického dokončování a návrhů – Azure Kognitivní hledání](tutorial-csharp-type-ahead-and-suggestions.md)
