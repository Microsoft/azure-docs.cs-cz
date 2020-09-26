---
title: Kurz C# o stránkování výsledků hledání
titleSuffix: Azure Cognitive Search
description: Přidejte do výsledků hledání stránkování a navigační tlačítka a sestavte stávající projekt hotelů a přidejte tak tlačítka First, Next, Previous, Last a číslovaných tlačítek. Druhý stránkovací systém používá nekonečné posouvání, aktivované přesunutím svislého posuvníku na jeho dolní limit.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 06/20/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: edae1edc076c99c025ff70c9b2493bc15e44102b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91280739"
---
# <a name="tutorial-add-paging-to-search-results-using-the-net-sdk"></a>Kurz: Přidání stránkování do výsledků hledání pomocí sady .NET SDK

Naučte se implementovat dva různé systémy stránkování, první v závislosti na číslech stránek a druhá při nekonečné posouvání. Často se používají oba systémy stránkování a výběr pravého tlačítka závisí na uživatelském prostředí, které chcete s výsledky. Tento kurz sestaví stránkovací systémy do projektu vytvořeného v [C# kurzu: Vytvoření první aplikace – Azure kognitivní hledání](tutorial-csharp-create-first-app.md) kurzu.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Rozšiřování aplikace pomocí číslovaného stránkování
> * Rozšiřování aplikace pomocí nekonečné posouvání

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

Kurz pro [C#: Vytvoření první aplikace – Azure kognitivní hledání](tutorial-csharp-create-first-app.md) Project a jejich spuštění. Tento projekt může být buď vlastní verze, nebo ho můžete nainstalovat z GitHubu: [vytvořit první aplikaci](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="extend-your-app-with-numbered-paging"></a>Rozšiřování aplikace pomocí číslovaného stránkování

Číslované stránkování je stránkovací systém, ve kterém se volí hlavní vyhledávací weby pro Internet a většina ostatních vyhledávacích webů. Očíslované stránkování obvykle zahrnuje možnost "Další" a "předchozí" kromě rozsahu skutečných čísel stránek. Také může být k dispozici možnost "první stránka" a "Poslední stránka". Tyto možnosti jistě poskytují uživatelský ovládací prvek pro procházení výsledků na základě stránky.

Přidáme systém, který obsahuje možnosti First, Previous, Next a Last, spolu s čísly stránek, která nezačínají od 1, ale místo toho se uživatel bude začínat (například pokud se na stránce zobrazí stránka 10, třeba číslo stránky 8, 9, 10, 11 a 12).

Systém bude dostatečně flexibilní, aby bylo možné nastavit počet zobrazených čísel stránek v globální proměnné.

Systém bude zacházet s tlačítky na nejvyšší úrovni vlevo a vpravo (jako speciální), což znamená, že budou aktivovat změnu rozsahu zobrazených čísel stránek. Například pokud se zobrazí čísla stránky 8, 9, 10, 11 a 12 a uživatel klikne na 8, pak se rozsah zobrazených čísel stránek změní na 6, 7, 8, 9 a 10. A v případě, že je vybrána možnost 12, existuje podobný posun doprava.

### <a name="add-paging-fields-to-the-model"></a>Přidání polí stránkování do modelu

Otevřete základní řešení pro stránku vyhledávání.

1. Otevřete soubor modelu SearchData.cs.

2. Nejdřív přidejte některé globální proměnné. V MVC jsou globální proměnné deklarovány ve vlastní statické třídě. **ResultsPerPage** nastaví počet výsledků na stránce. **MaxPageRange** určuje počet viditelných čísel stránek v zobrazení. **PageRangeDelta** určuje, kolik stran doleva nebo dolů má být rozsahem stránky posunuto, pokud je vybráno číslo stránky nejvíce vlevo nebo vpravo. Toto druhé číslo je obvykle kolem poloviny **MaxPageRange**. Do oboru názvů přidejte následující kód.

    ```cs
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

3. Přidejte vlastnosti stránkování do třídy **SearchData** , říkáme za vlastnost **prohledávanýtext** .

    ```cs
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

    ```cs
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
                <td>
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

            <td>
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

            <td>
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
    ```

    K zarovnání věcí používáme tabulku HTML. Nicméně všechny akce pocházejí z @Html.ActionLink příkazů, každé volání kontroleru s **novým** modelem vytvořeným s různými položkami do vlastnosti **stránkování** , kterou jsme přidali dříve.

    První a poslední stránka neodesílají řetězce, například "First" a "Last", ale místo toho odesílají správná čísla stránek.

2. Přidejte některé třídy stránkování do seznamu stylů HTML v souboru hotely. CSS. Třída **pageSelected** je k identifikaci stránky, kterou uživatel aktuálně zobrazuje (zapnutím čísla tučně) v seznamu čísel stránek.

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

1. Otevřete soubor HomeController.cs a přidejte akci **stránky** . Tato akce odpoví na kteroukoli z vybraných možností stránky.

    ```cs
        public async Task<ActionResult> Page(SearchData model)
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

                // Ensure Temp data is stored for next call, as TempData only stored for one call.
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
    > **TempData** volá do dočasného úložiště hodnotu ( **objekt**), i když toto úložiště přetrvává _jenom_ pro jedno volání. Pokud ukládáme něco do dočasných dat, bude k dispozici pro další volání akce kontroleru, ale po této akci bude toto volání nejvíce neomezené. V důsledku tohoto krátkého intervalu ukládáme do dočasného úložiště vlastnosti hledání textu a stránkování a každé volání **stránky**se vrátí do dočasného úložiště.

2. Akce **index (model)** vyžaduje aktualizaci, aby ukládala dočasné proměnné a přidala do volání **RunQueryAsync** parametr stránky vlevo.

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

3. Metoda **RunQueryAsync** se musí významně aktualizovat. Pomocí polí **Skip**, **Top**a **IncludeTotalResultCount** třídy **SearchParameters** si vyžádáme jenom jednu stránku s výsledky, počínaje nastavením **Skip** . Pro naše zobrazení je také potřeba vypočítat stránkovací proměnné. Nahraďte celou metodu následujícím kódem.

    ```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage)
        {
            InitSearch();

            var parameters = new SearchParameters
            {
                   // Enter Hotel property names into this list so only these values will be returned.
                   // If Select is empty, all values will be returned, which can be inefficient.
                   Select = new[] { "HotelName", "Description" },
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
            model.pageCount = ( (int)model.resultList.Count + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

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

            return View("Index", model);
        }
    ```

4. Nakonec musíme udělat malou změnu zobrazení. Proměnná **resultsList. Results. Count** nyní bude obsahovat počet výsledků vrácených na jedné stránce (3 v našem příkladu), ne celkové číslo. Vzhledem k tomu, že **IncludeTotalResultCount** nastavíme na hodnotu true, proměnná **resultsList. Count** nyní obsahuje celkový počet výsledků. Proto Najděte, kde se v zobrazení zobrazí počet výsledků, a změňte ji na následující kód.

    ```cs
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Count) Results
            </p>
    ```

    > [!Note]
    > Výkon se může dosáhnout, i když to není obvykle mnoho, nastavením **IncludeTotalResultCount** na hodnotu true, protože tento součet je potřeba vypočítat pomocí Azure kognitivní hledání. U komplexních datových sad se zobrazí upozornění, že vrácená hodnota je _aproximace_. Pro naše data hotelu bude přesné.

### <a name="compile-and-run-the-app"></a>Zkompilovat a spustit aplikaci

Nyní vyberte možnost **Spustit bez ladění** (nebo stiskněte klávesu F5).

1. Vyhledejte nějaký text, který poskytne dostatek výsledků (například "WiFi"). Můžete stránku na základě výsledků zamezit.

    ![Očíslované stránkování prostřednictvím "fondu" výsledků](./media/tutorial-csharp-create-first-app/azure-search-numbered-paging.png)

2. Zkuste kliknout na nejvíc vpravo a později na všechna čísla stránek, která jsou nejvíce vlevo. Přizpůsobte si čísla stránek odpovídajícím způsobem centrování stránky, na které jste připojeni?

3. Jsou možnosti "první" a "poslední" užitečné? Některá Oblíbená hledání na webu využívají tyto možnosti a jiné ne.

4. Přejít na poslední stránku výsledků. Poslední stránka je jediná stránka, která může obsahovat méně než **ResultsPerPage** výsledků.

    ![Prozkoumání poslední stránky "WiFi"](./media/tutorial-csharp-create-first-app/azure-search-pool-last-page.png)

5. Zadejte "město" a klikněte na Hledat. Pokud je k dispozici méně než jedna stránka s výsledky, nejsou zobrazeny žádné možnosti stránkování.

    ![Hledání "města"](./media/tutorial-csharp-create-first-app/azure-search-town.png)

Nyní tento projekt uložte a pojďme se pokusit o alternativu k této formě stránkování.

## <a name="extend-your-app-with-infinite-scrolling"></a>Rozšiřování aplikace pomocí nekonečné posouvání

Nekonečná posouvání se aktivuje, když uživatel posune svislý posuvník na poslední zobrazené výsledky. V tomto případě se pro další stránku výsledků vyvolá volání serveru. Pokud neexistují žádné další výsledky, není nic vráceno a svislý posuvník se nemění. Pokud je k dispozici více výsledků, připojí se k aktuální stránce a posuvník se změní, aby bylo vidět, že jsou k dispozici další výsledky.

Důležitým bodem je, že zobrazená stránka není nahrazena, ale je připojená k novým výsledkům. Uživatel může vždycky přejít zpět na první výsledky hledání.

Chcete-li implementovat nekonečné posouvání, začněte s projektem před přidáním prvků pro posouvání čísla stránky. Takže pokud potřebujete, udělejte další kopii základní vyhledávací stránky z GitHubu: [vytvořit první aplikaci](https://github.com/Azure-Samples/azure-search-dotnet-samples).

### <a name="add-paging-fields-to-the-model"></a>Přidání polí stránkování do modelu

1. Nejprve přidejte vlastnost **stránkování** do třídy **SearchData** (v souboru modelu SearchData.cs).

    ```cs
        // Record if the next page is requested.
        public string paging { get; set; }
    ```

    Tato proměnná je řetězec, který obsahuje "Next", pokud by se měla odeslat další stránka výsledků, nebo pro první stránku hledání mít hodnotu null.

2. Do stejného souboru a v rámci oboru názvů přidejte globální proměnnou třídy s jednou vlastností. V MVC jsou globální proměnné deklarovány ve vlastní statické třídě. **ResultsPerPage** nastaví počet výsledků na stránce. 

    ```cs
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

2. Nahraďte oddíl následujícím kódem. Nový oddíl ** &lt; div &gt; ** je okolo oblasti, která by se měla procházet, a přidá jak atribut **přetečení** , tak i **volání funkce pro posouvání s** názvem "scrolled ()", například.

    ```cs
        @if (Model != null)
        {
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Count) Results
            </p>

            <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    // Display the hotel name and description.
                    @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box2" })
                }
            </div>
        }
    ```

3. Přímo pod smyčkou, za &lt; &gt; značkou/div přidejte **posunutou** funkci.

    ```javascript
        <script>
                function scrolled() {
                    if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                        $.getJSON("/Home/Next", function (data) {
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

    Příkaz **if** ve skriptu výše testuje, zda se uživatel přesunul do dolní části svislého posuvníku. Pokud mají, volání do **domovského** kontroleru se provede na akci s názvem Next ( **Další**). Kontroler nevyžaduje žádné další informace, vrátí další stránku dat. Tato data se pak naformátují pomocí identických stylů HTML jako původní stránky. Pokud se nevrátí žádné výsledky, nic se nepřipojí a co se stane.

### <a name="handle-the-next-action"></a>Zpracování další akce

Existují pouze tři akce, které je třeba odeslat do kontroleru: první spuštění aplikace, která volá **index ()**, první hledání uživatelem, který volá **index (model)**, a následné volání pro další výsledky prostřednictvím **Next (model)**.

1. Otevřete soubor domovského kontroleru a odstraňte metodu **RunQueryAsync** z původního kurzu.

2. Nahraďte akci **indexu (modelu)** následujícím kódem. Nyní zpracovává pole **stránkování** , pokud je null nebo nastaveno na "Další" a zpracovává volání do Azure kognitivní hledání.

    ```cs
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
                var parameters = new SearchParameters
                {
                    // Enter Hotel property names into this list so only these values will be returned.
                    // If Select is empty, all values will be returned, which can be inefficient.
                    Select = new[] { "HotelName", "Description" },
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

    Podobně jako u očíslované metody stránkování používáme nastavení pro **přeskočení** a **začátek** vyhledávání k vyžádání pouze těch dat, která potřebujeme.

3. Přidejte **Další** akci do domovského kontroleru. Všimněte si, jak vrátí seznam a každý Hotel přidávají dva prvky do seznamu: název hotelu a popis hotelu. Tento formát je nastavený tak, aby se **shodoval s použitím** vrácených dat v zobrazení.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel name, then description, to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

4. Pokud se vám v ** &lt; řetězci &gt; seznamu**zobrazuje chyba syntaxe, přidejte do hlavičky souboru kontroleru následující direktivu **using** .

    ```cs
    using System.Collections.Generic;
    ```

### <a name="compile-and-run-your-project"></a>Zkompilovat a spustit projekt

Nyní vyberte možnost **Spustit bez ladění** (nebo stiskněte klávesu F5).

1. Zadejte termín, který poskytne dostatek výsledků (například "fond") a potom otestuje svislý posuvník. Aktivuje se nová stránka s výsledky?

    ![Nekonečná posouvání prostřednictvím "fondu" výsledků](./media/tutorial-csharp-create-first-app/azure-search-infinite-scroll.png)

    > [!Tip]
    > Aby se zajistilo, že se na první stránce zobrazí posuvník, první stránka výsledků musí mírně překročit výšku oblasti, ve které se zobrazují. V našem příkladu má **box1** výšku 30 pixelů **. box2** má výšku 100 pixelů _a_ spodní okraj 24 pixelů. Každá položka tedy používá 154 pixelů. Tři položky budou zabírat 3 x 154 = 462 pixelů. Aby se zajistilo, že se zobrazí svislý posuvník, musí být výška na zobrazované oblasti nastavená tak, aby byla menší než 462 pixelů, i když 461 funguje. K tomuto problému dochází pouze na první stránce, poté, co se zobrazí posuvník. Řádek, který se má aktualizovat: ** &lt; div ID = "myDiv" Style = "width: 800px; Height: 450px; přetečení-y: Scroll;" proscroll = "scrolled ( &gt; )"**.

2. Posuňte se dolů k dolnímu okraji výsledků. Všimněte si, že všechny informace jsou nyní na jedné stránce zobrazení. Můžete se přesměrovat zpět na začátek, aniž by se aktivovala žádná volání serveru.

Sofistikované nekonečné systémy posouvání můžou použít kolečko myši nebo podobný jiný mechanismus, aby se aktivovalo načítání nové stránky výsledků. V těchto kurzech ještě nebudeme nic přepínat, ale k tomu má určitý ovládací tlačítko, protože se vyhnete dalším klikáním myší a možná budete chtít prozkoumat další možnosti.

## <a name="takeaways"></a>Shrnutí

Vezměte v úvahu následující poznatky z tohoto projektu:

* Očíslované stránkování je dobré pro hledání, kde pořadí výsledků je trochu libovolné, což znamená, že uživatelé můžou na pozdějších stránkách něco zajímat.
* Nekonečné posouvání je vhodné, pokud je pořadí výsledků obzvláště důležité. Například pokud jsou výsledky seřazené na vzdálenost od středu cílového města.
* Číslované stránkování umožňuje určitou lepší navigaci. Uživatel si například může pamatovat, že na stránce 6 byl zajímavý výsledek, protože žádný takový jednoduchý odkaz neexistuje v nekonečném posouvání.
* Nekonečné posouvání má snadnou přitažlivost, posun nahoru a dolů bez Fussy čísel stránek, na které kliknete.
* Klíčovou funkcí nekonečné posouvání je, že výsledky se připojí k existující stránce a nenahrazují tuto stránku, což je efektivní.
* Dočasné úložiště uchovává pouze jedno volání a musí být obnoveno, aby se zadrželo další volání.


## <a name="next-steps"></a>Další kroky

Stránkování je zásadní pro hledání v Internetu. V případě dobře krytého stránkování je dalším krokem lepší zlepšení uživatelského prostředí tím, že se přidají hledání typu dopředu.

> [!div class="nextstepaction"]
> [Kurz C#: přidání automatického dokončování a návrhů – Azure Kognitivní hledání](tutorial-csharp-type-ahead-and-suggestions.md)
