---
title: C# výuka o stránkování výsledků hledání
titleSuffix: Azure Cognitive Search
description: Tento kurz ukazuje stránkování výsledků hledání. Staví na existujícím projektu hotelů, s stránkováním podle prvních, dalších, předchozích, posledních a číslovaných tlačítek. Druhý stránkovací systém používá nekonečné posouvání, které se aktivuje přesunutím svislého posuvníku na jeho dolní hranici.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: 9abfeb54be6e22885b8e973034a6d89df8272146
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77121520"
---
# <a name="c-tutorial-search-results-pagination---azure-cognitive-search"></a>Kurz C#: Stránkování výsledků hledání – Azure Cognitive Search

Naučte se implementovat dva různé stránkovací systémy, první na základě čísel stránek a druhý na nekonečném posouvání. Oba systémy stránkování jsou široce používány a výběr správného závisí na uživatelském prostředí, které byste chtěli s výsledky. Tento kurz staví stránkovací systémy do projektu vytvořeného v [kurzu C#: Vytvořte první aplikaci – kurz azure kognitivního vyhledávání.](tutorial-csharp-create-first-app.md)

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Rozšíření aplikace o číslované stránkování
> * Rozšíření aplikace s nekonečným posouváním

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

Mít [c# kurz: Vytvořte si první aplikaci – Azure Cognitive Search](tutorial-csharp-create-first-app.md) projektu v provozu. Tento projekt může být buď vaše vlastní verze, nebo jej nainstalovat z GitHub: [Vytvořit první aplikaci](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="extend-your-app-with-numbered-paging"></a>Rozšíření aplikace o číslované stránkování

Číslované stránkování je stránkovací systém výběru hlavních internetových vyhledávačů a většiny ostatních vyhledávacích webových stránek. Číslované stránkování obvykle zahrnuje možnost "další" a "předchozí" kromě rozsahu skutečných čísel stránek. K dispozici může být také možnost "první stránka" a "poslední stránka". Tyto možnosti jistě poskytují uživateli kontrolu nad procházením výsledků založených na stránce.

Přidáme systém, který obsahuje první, předchozí, další a poslední možnosti, spolu s čísly stránek, které nezačínají od 1, ale místo toho obklopují aktuální stránku, na které se uživatel nachází (takže například pokud se uživatel dívá na stránku 10, možná se zobrazí čísla stránek 8, 9, 10, 11 a 12).

Systém bude dostatečně flexibilní, aby umožnil nastavit počet viditelných čísel stránek v globální proměnné.

Systém bude považovat tlačítka číslo stránky nejvíce vlevo a vpravo jako zvláštní, což znamená, že spustí změnu rozsahu zobrazených čísel stránek. Pokud jsou například zobrazena čísla stránek 8, 9, 10, 11 a 12 a uživatel klikne na číslo 8, rozsah zobrazených čísel stránek se změní na 6, 7, 8, 9 a 10. A tam je podobný posun doprava, pokud vybrali 12.

### <a name="add-paging-fields-to-the-model"></a>Přidání stránkovacích polí do modelu

Otevřete základní řešení vyhledávací stránky.

1. Otevřete soubor modelu SearchData.cs.

2. Nejprve přidejte některé globální proměnné. V MVC globální proměnné jsou deklarovány ve své vlastní statické třídy. **ResultsPerPage** nastaví počet výsledků na stránku. **MaxPageRange** určuje počet viditelných čísel stránek v zobrazení. **PageRangeDelta** určuje, kolik stránek vlevo nebo vpravo rozsah stránky by měla být posunuta, když je vybrána číslo stránky nejvíce vlevo nebo vpravo. Obvykle toto druhé číslo je přibližně polovina **MaxPageRange**. Přidejte následující kód do oboru názvů.

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
    >Pokud používáte tento projekt na zařízení s menší obrazovkou, jako je například přenosný počítač, zvažte změnu **ResultsPerPage** na 2.

3. Přidejte vlastnosti stránkování do třídy **SearchData,** řekněme za vlastnost **searchText.**

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

1. Otevřete soubor index.cshtml a přidejte následující &lt;kód&gt; těsně před uzavírací značku /body. Tento nový kód představuje tabulku možností stránkování: první, předchozí, 1, 2, 3, 4, 5, další, poslední.

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

    Používáme tabulku HTML k úhlednému zarovnání věcí. Všechny akce však @Html.ActionLink pochází z příkazů, z nichž každý volá řadič s **novým** modelem vytvořeným s různými položkami do **vlastnosti stránkování,** kterou jsme přidali dříve.

    První a poslední stránka možnosti neposílají řetězce jako "první" a "poslední", ale místo toho odeslat správná čísla stránek.

2. Přidejte některé třídy stránkování do seznamu stylů HTML v souboru hotels.css. Třída **pageSelected** je k dispozici k identifikaci stránky, kterou uživatel právě prohlíží (otočením tučného čísla) v seznamu čísel stránek.

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

### <a name="add-a-page-action-to-the-controller"></a>Přidání akce Stránka do ovladače

1. Otevřete soubor HomeController.cs a přidejte akci **Stránka.** Tato akce reaguje na některou z vybraných možností stránky.

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

    Metoda **RunQueryAsync** nyní zobrazí syntaktickou chybu, protože třetí parametr, ke kterému se trochu dostaneme.

    > [!Note]
    > **Volání TempData** uložit hodnotu **(objekt)** v dočasném úložišti, i když toto úložiště přetrvává _pouze_ pro jedno volání. Pokud uložíme něco v dočasných datech, bude k dispozici pro další výzvu k akci správce, ale bude určitě pryč volání po tom! Z důvodu této krátké životnosti ukládáme vyhledávací text a vlastnosti stránkování zpět do dočasného úložiště při každém volání na **stránku**.

2. Akce **Index(model)** potřebuje aktualizovat pro uložení dočasných proměnných a přidat parametr stránky zcela vlevo do volání **RunQueryAsync.**

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

3. Metoda **RunQueryAsync** potřebuje výrazně aktualizovat. Používáme **přeskočit**, **Top**, a **IncludeTotalResultCount** pole **SearchParameters třídy** požadovat pouze jednu stránku v hodnotě výsledků, počínaje **Přeskočit** nastavení. Musíme také vypočítat stránkovací proměnné pro náš názor. Nahraďte celou metodu následujícím kódem.

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

4. A konečně musíme provést malou změnu v pohledu. Proměnná **resultsList.Results.Count** bude nyní obsahovat počet výsledků vrácených na jedné stránce (3 v našem příkladu), nikoli celkový počet. Protože jsme nastavili **IncludeTotalResultCount** na true, proměnná **resultsList.Count** nyní obsahuje celkový počet výsledků. Takže vyhledejte, kde je zobrazen počet výsledků v zobrazení, a změňte jej na následující kód.

    ```cs
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Count) Results
            </p>
    ```

    > [!Note]
    > Je dosažení výkonu, i když ne obvykle hodně z jednoho, nastavením **IncludeTotalResultCount** na true, protože tento součet musí být vypočtena Azure Cognitive Search. U komplexních datových sad je upozornění, že vrácená hodnota je _aproximace_. Pro naše hotelové údaje budou přesné.

### <a name="compile-and-run-the-app"></a>Kompilace a spuštění aplikace

Nyní vyberte **spustit bez ladění** (nebo stiskněte klávesu F5).

1. Hledat na nějaký text, který bude dávat spoustu výsledků (například "wifi"). Můžete stránku úhledně přes výsledky?

    ![Číslované stránkování prostřednictvím výsledků fondu](./media/tutorial-csharp-create-first-app/azure-search-numbered-paging.png)

2. Zkuste kliknout na čísla stránek zcela vpravo a později na většinu vlevo. Upraví se čísla stránek odpovídajícím způsobem tak, aby vystředila stránku, na které se nacházejíte?

3. Jsou užitečné možnosti "první" a "poslední"? Některá oblíbená webová vyhledávání používají tyto možnosti a jiná nikoli.

4. Přejděte na poslední stránku výsledků. Poslední stránka je jediná stránka, která může obsahovat méně než **výsledky ResultsPerPage.**

    ![Zkoumání poslední stránky "wifi"](./media/tutorial-csharp-create-first-app/azure-search-pool-last-page.png)

5. Zadejte "město" a klikněte na hledat. Pokud jsou výsledky v hodnotě menší než jedna stránka, nebudou zobrazeny žádné možnosti stránkování.

    ![Hledání "města"](./media/tutorial-csharp-create-first-app/azure-search-town.png)

Nyní uložte tento projekt a zkusme alternativu k této formě stránkování.

## <a name="extend-your-app-with-infinite-scrolling"></a>Rozšíření aplikace s nekonečným posouváním

Nekonečné posouvání se aktivuje, když uživatel posouvá svislý posuvník na poslední z zobrazených výsledků. V takovém případě je provedeno volání na server pro další stránku výsledků. Pokud nejsou k dispozici žádné další výsledky, nic se vrátí a svislý posuvník se nezmění. Pokud jsou k dispozici další výsledky, jsou připojeny k aktuální stránce a posuvník se změní tak, aby zobrazoval, že jsou k dispozici další výsledky.

Důležitým bodem je, že zobrazená stránka není nahrazena, ale je připojena k novým výsledkům. Uživatel může vždy posunout zpět na první výsledky hledání.

Chcete-li implementovat nekonečné posouvání, začněme s projektem před přidáním některého z prvků posouvání čísla stránky. Takže, pokud potřebujete, vytvořte další kopii základní vyhledávací stránky z GitHubu: [Vytvořte první aplikaci](https://github.com/Azure-Samples/azure-search-dotnet-samples).

### <a name="add-paging-fields-to-the-model"></a>Přidání stránkovacích polí do modelu

1. Nejprve přidejte vlastnost **stránkování** do třídy **SearchData** (v souboru SearchData.cs modelu).

    ```cs
        // Record if the next page is requested.
        public string paging { get; set; }
    ```

    Tato proměnná je řetězec, který obsahuje "další", pokud by měla být odeslána další stránka výsledků nebo má být null pro první stránku hledání.

2. Ve stejném souboru a v oboru názvů přidejte globální třídu proměnných s jednou vlastností. V MVC globální proměnné jsou deklarovány ve své vlastní statické třídy. **ResultsPerPage** nastaví počet výsledků na stránku. 

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

### <a name="add-a-vertical-scroll-bar-to-the-view"></a>Přidání svislého posuvníku do zobrazení

1. Vyhledejte část souboru index.cshtml, která zobrazuje výsledky (začíná ** @if (Model != null)**).

2. Nahraďte oddíl níže uvedeným kódem. Nová ** &lt;&gt; ** sekce div je kolem oblasti, která by měla být rolovací, a přidá atribut **přetečení y** a volání funkce **onscroll** s názvem "scrolled()", tak to.

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

3. Přímo pod smyčku, &lt;&gt; za /div tag, přidejte **rolované** funkce.

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

    Příkaz **if** ve výše uvedeném skriptu testuje, zda se uživatel posunul do dolní části svislého posuvníku. Pokud ano, je provedeno volání na **domácí** ovladač na akci s názvem **Další**. Správce nepotřebuje žádné další informace, vrátí další stránku dat. Tato data jsou pak formátována pomocí identických stylů HTML jako původní stránka. Pokud nejsou vráceny žádné výsledky, nic není připojeno a věci zůstanou tak, jak jsou.

### <a name="handle-the-next-action"></a>Zpracování akce Další

Existují pouze tři akce, které je třeba odeslat do řadiče: první spuštění aplikace, která volá **Index()**, první vyhledávání uživatelem, který volá **Index(model)** a následné volání pro další výsledky přes **Next(model)**.

1. Otevřete soubor domovského řadiče a odstraňte metodu **RunQueryAsync** z původního kurzu.

2. Nahraďte akci **Index(model)** následujícím kódem. Nyní zpracovává **stránkovací** pole, pokud je null nebo nastaveno na "další" a zpracovává volání Azure Cognitive Search.

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

    Podobně jako v případě číslované metody stránkování používáme nastavení **přeskočit** a **horní** vyhledávání, abychom požadovali pouze data, která potřebujeme, je vrácena.

3. Přidejte **Next** další akci do domácího ovladače. Všimněte si, jak se vrátí seznam, každý hotel přidá do seznamu dva prvky: název hotelu a popis hotelu. Tento formát je nastaven tak, aby odpovídal použití vrácených dat v zobrazení **posouvanou** funkcí.

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

4. Pokud se zobrazuje chyba syntaxe v **&lt;&gt;řetězci Seznam**, přidejte do hlavy souboru řadiče následující **direktivu pomocí** směrnice.

    ```cs
    using System.Collections.Generic;
    ```

### <a name="compile-and-run-your-project"></a>Kompilace a spuštění projektu

Nyní vyberte **spustit bez ladění** (nebo stiskněte klávesu F5).

1. Zadejte termín, který poskytne spoustu výsledků (například "fond") a pak otestujte svislý posuvník. Má to vyvolat novou stránku výsledků?

    ![Nekonečné procházení výsledků "fondu"](./media/tutorial-csharp-create-first-app/azure-search-infinite-scroll.png)

    > [!Tip]
    > Chcete-li zajistit, aby se na první stránce objevil posuvník, musí první stránka výsledků mírně překročit výšku oblasti, ve které jsou zobrazeny. V našem příkladu **.box1** má výšku 30 pixelů, **.box2** má výšku 100 pixelů _a_ dolní okraj 24 pixelů. Takže každý záznam používá 154 pixelů. Tři položky budou trvat až 3 x 154 = 462 pixelů. Chcete-li zajistit, aby se zobrazil svislý posuvník, musí být nastavena výška oblasti zobrazení, která je menší než 462 pixelů, i 461 funguje. K tomuto problému dochází pouze na první stránce, po tomto posuvník je určitě zobrazí. Řádek k aktualizaci je: ** &lt;div id = "myDiv" style ="šířka: 800px; výška: 450px; přetečení-y: scroll;" onscroll&gt;="scrolled()"**.

2. Posuňte se dolů až do dolní části výsledků. Všimněte si, jak jsou nyní všechny informace na jedné stránce zobrazení. Můžete posunout celou cestu zpět na vrchol bez spuštění volání serveru.

Sofistikovanější nekonečné rolovací systémy mohou používat kolečko myši nebo podobný jiný mechanismus ke spuštění načítání nové stránky výsledků. Nebudeme užívat nekonečné rolování žádné další v těchto výukových programů, ale má určité kouzlo na to, jak se vyhýbá další kliknutí myší, a možná budete chtít prozkoumat další možnosti dále!

## <a name="takeaways"></a>Shrnutí

Vezměme si následující stánek s jídlem z tohoto projektu:

* Číslované stránkování je dobré pro vyhledávání, kde je pořadí výsledků poněkud libovolné, což znamená, že může být něco zajímavého pro vaše uživatele na pozdějších stránkách.
* Nekonečné posouvání je dobré, když je pořadí výsledků obzvláště důležité. Například pokud jsou výsledky seřazeny na vzdálenost od centra cílového města.
* Číslované stránkování umožňuje lepší navigaci. Například uživatel si může vzpomenout, že zajímavý výsledek byl na stránce 6, zatímco žádný takový snadný odkaz existuje v nekonečném posouvání.
* Nekonečné rolování má snadné odvolání, rolování nahoru a dolů bez úzkostlivý čísla stránek kliknout na.
* Klíčovým rysem nekonečného posouvání je, že výsledky jsou připojeny k existující stránce, nikoli nahrazení této stránky, která je efektivní.
* Dočasné úložiště přetrvává pouze pro jedno volání a je třeba obnovit, aby přežilo další volání.


## <a name="next-steps"></a>Další kroky

Stránkování je zásadní pro vyhledávání na internetu. Při dobře zakrytém stránkování je dalším krokem další zlepšení uživatelského prostředí přidáním vyhledávání typu dopředu.

> [!div class="nextstepaction"]
> [Kurz jazyka C#: Přidání automatického dokončování a návrhů – Azure Cognitive Search](tutorial-csharp-type-ahead-and-suggestions.md)
