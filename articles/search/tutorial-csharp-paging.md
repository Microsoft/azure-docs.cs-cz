---
title: C#kurz týkající se stránkování výsledků vyhledávání – Azure Search
description: Tento kurz vychází projektu "Vytvoření první aplikace – Azure Search", s možností dva druhy stránkování. První používá celou řadu tlačítek číslo stránky, stejně jako první, potom předchozí a poslední stránky, tlačítka. Druhý systém stránkování používá nekonečné posouvání, aktivuje přesunutím svislý posuvník na jeho dolní mez.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 05/01/2019
ms.openlocfilehash: 7e6c433168b73c6b58d13d4698bed55d7c18ec58
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67434625"
---
# <a name="c-tutorial-search-results-pagination---azure-search"></a>C#kurz: Stránkování výsledků vyhledávání – Azure Search

Zjistěte, jak implementovat dva různé systémy stránkování, první na základě čísla stránek a podruhé v nekonečné posouvání. Oba systémy stránkování hojně používají, a ten správný výběr závisí na uživatelské prostředí, které chcete s výsledky. V tomto kurzu sestavení do projektu vytvořeného v systémech stránkování [ C# kurzu: Vytvoření první aplikace – Azure Search](tutorial-csharp-create-first-app.md) kurzu.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Rozšiřte svou aplikaci pomocí číselného stránkování
> * Rozšiřte svou aplikaci pomocí nekonečné posouvání

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

Máte [ C# kurzu: Vytvoření první aplikace – Azure Search](tutorial-csharp-create-first-app.md) projektu rychle zprovoznit. Tento projekt může být vlastní verzi nebo nainstalovat z Githubu: [Vytvoření první aplikace](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="extend-your-app-with-numbered-paging"></a>Rozšiřte svou aplikaci pomocí číselného stránkování

Číselného stránkování je systém stránkování hlavní vyhledávací weby internet a většina jiných webových stránek hledání podle výběru. Číselného stránkování obvykle nabízí možnost "následující" a "předchozí" kromě rozsah čísel skutečné stránky. Také "první stránka" a "poslední stránka" možnost může být také k dispozici. Tyto možnosti jistě poskytnout uživatelský ovládací prvek v navigaci na stránce výsledky.

Přidáme systému, který obsahuje další první, předchozí a poslední možnosti, spolu s čísly, která nebudou spuštěny z 1, ale místo toho je obklopit stránce aktuálního uživatele na (tedy například, pokud se uživatel dívá na stránku s 10, třeba čísla stránek 8 9, 10, 11 a 12 se zobrazí).

Systém bude dostatečně flexibilní, aby počet čísel viditelné stránky v globální proměnné.

Systém bude považovat stránka úplně vlevo a úplně vpravo číslo tlačítka jako speciální, což znamená, že aktivují změnit rozsah čísel stránky zobrazí. Například pokud se zobrazí stránka čísla 8, 9, 10, 11 a 12 a uživatel klikne na 8, pak rozsah čísel stránky zobrazí změny 6, 7, 8, 9 a 10. A pokud vybral 12 je podobné posunutí doprava.

### <a name="add-paging-fields-to-the-model"></a>Do modelu přidat pole stránkování

Máte základní hledání stránky řešení otevřené.

1. Otevřete soubor modelu SearchData.cs.

2. Nejprve přidáte některých globálních proměnných. V aplikaci MVC globální proměnné jsou deklarovány ve své vlastní statické třídě. **ResultsPerPage** nastaví počet výsledků na stránku. **MaxPageRange** určuje počet hodnot viditelné stránky v zobrazení. **PageRangeDelta** určuje počet stránek levého nebo pravého rozsah stránek by měl posunuta, pokud je vybrána číslo levým nebo krajním stránky. Toto druhé číslo je obvykle kolem polovinu **MaxPageRange**. Přidejte následující kód do oboru názvů.

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
    >Pokud používáte tento projekt na zařízení s menší obrazovku, například notebook, pak zvažte změnu **ResultsPerPage** na 2.

3. Přidání vlastností stránkování **SearchData** třídy, například po **Prohledávanýtext** vlastnost.

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

### <a name="add-a-table-of-paging-options-to-the-view"></a>Přidat tabulku možností stránkování na zobrazení

1. Otevřete soubor index.cshtml a přidejte následující kód bezprostředně před uzavírací &lt;/body&gt; značky. Tento nový kód představuje tabulku možnosti stránkování: první, předchozí, 1, 2, 3, 4, 5, v dalším kroku naposledy.

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

    Používáme tabulku HTML elegantně zarovnat věci. Ale všechny akce pochází z @Html.ActionLink příkazy, každá volá kontroler s **nové** model vytvořený pomocí jiné položky **stránkování** vlastnost jsme přidali dříve.

    První a poslední stránky možnosti Neodesílat řetězce, jako jsou "first" a "last", ale místo toho odesílat čísla správná stránka.

2. Přidejte do seznamu stylů HTML v souboru hotels.css některé třídy stránkování. **PageSelected** třídy existuje a identifikovat tak stránku uživatel je momentálně zobrazení (počet aktivací bold) v seznamu čísel stránek.

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

### <a name="add-a-page-action-to-the-controller"></a>Přidat stránku akce kontroleru

1. Otevřete soubor HomeController.cs a přidejte **stránky** akce. Tato akce jsou reaguje na některý z vybrané možnosti stránky.

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

    **RunQueryAsync** metoda kvůli třetí parametr, který bude přejdeme k ve chvíli nyní zobrazí chybu syntaxe.

    > [!Note]
    > **TempData** volání uložení hodnoty ( **objekt**) v dočasné úložiště, i když toto úložiště potrvají _pouze_ jedno volání. Pokud něco uložíme v dočasných dat, bude k dispozici pro další volání akce kontroleru, ale bude nejvíc jednoznačně znovu voláním po tomto! Z důvodu této krátké životnost, uložíme hledaný text a zpět vlastnosti stránkování v dočasné úložiště každého volání **stránky**.

2. **Index(model)** akce potřeby aktualizovali k ukládání dočasných proměnných a přidat parametr stránka úplně vlevo **RunQueryAsync** volání.

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

3. **RunQueryAsync** metoda potřeby výrazně aktualizovat. Používáme **přeskočit**, **horní**, a **IncludeTotalResultCount** pole **parametrech vyhledávání** třídy požádat o jednu stránku za výsledky, počínaje **přeskočit** nastavení. Potřebujeme také k výpočtu proměnné stránkování pro naše zobrazení. Nahraďte celou metodu s následujícím kódem.

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

4. Nakonec musíme proveďte malou změnu do zobrazení. Proměnná **resultsList.Results.Count** bude teď obsahovat počet výsledků vrácených na jedné stránce (3 v našem příkladu), není celkový počet. Protože jsme nastavili **IncludeTotalResultCount** na hodnotu true, je proměnná **resultsList.Count** nyní obsahuje celkový počet výsledků. Proto vyhledejte, kde počet výsledků, které se zobrazí v zobrazení a změnit na následující kód.

    ```cs
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Count) Results
            </p>
    ```

    > [!Note]
    > Je dosaženo výkonu, i když obvykle mnohem jednoho nastavením **IncludeTotalResultCount** na hodnotu true, protože tento součet je potřeba vypočítat služba Azure Search. Komplexní datové sady existuje upozornění. Vrácená hodnota je _aproximace_. Pro naše data hotelu je přesné.

### <a name="compile-and-run-the-app"></a>Kompilace a spuštění aplikace

Teď vyberte **spustit bez ladění** (nebo stiskněte klávesu F5).

1. Hledat na nějaký text, který vám poskytne dostatek výsledků (například "Wi-Fi"). Lze při procházení elegantně výsledky?

    ![Číselného stránkování prostřednictvím "fond" výsledky](./media/tutorial-csharp-create-first-app/azure-search-numbered-paging.png)

2. Zkuste klepnout na úplně vpravo a později, čísla stránek nejvíce vlevo. Číslo stránky odpovídajícím způsobem nastavit střed stránku, kterou používáte?

3. Možnosti "first" a "last" jsou užitečné? Tyto možnosti použít některé oblíbené webové vyhledávání a jiné zase ne.

4. Přejdete na poslední stránku výsledků. Poslední stránka je jedinou stránkou, která mohou obsahovat méně než **ResultsPerPage** výsledky.

    ![Zkoumání poslední stránky "Wi-Fi"](./media/tutorial-csharp-create-first-app/azure-search-pool-last-page.png)

5. Zadejte "Město" a klikněte na tlačítko Hledat. Pokud jsou menší než za stránku výsledků, zobrazí se žádné možnosti stránkování.

    ![Hledání "Město"](./media/tutorial-csharp-create-first-app/azure-search-town.png)

Vypnout tento projekt nyní uložit a teď si vyzkoušíme alternativu k tomuto formuláři stránkování.

## <a name="extend-your-app-with-infinite-scrolling"></a>Rozšiřte svou aplikaci pomocí nekonečné posouvání

Nekonečné posouvání se aktivuje, když bude uživatel posouvat poslední výsledků se zobrazí svislý posuvník. V takovém případě je uskutečněn hovor na server pro další stránky výsledků. Pokud neexistují žádné další výsledky, nevrátí a nemění svislý posuvník. Pokud existují další výsledky, jsou připojeny k aktuální stránce a změny posuvníku panelu zobrazíte, že jsou k dispozici více výsledků.

Důležitý bod je, že na stránce se zobrazí není nahrazena, ale připojené k nové výsledky. Uživatele můžete vždy přejděte zpět do první výsledky hledání.

K implementaci nekonečné posouvání, začneme projektu předtím, než byly přidané číslo posouvání prvky stránky. Ano Pokud je potřeba vytvořte další kopii základní hledání stránky z Githubu: [Vytvoření první aplikace](https://github.com/Azure-Samples/azure-search-dotnet-samples).

### <a name="add-paging-fields-to-the-model"></a>Do modelu přidat pole stránkování

1. Nejprve přidejte **stránkování** vlastnost **SearchData** třídy (v souboru modelu SearchData.cs).

    ```cs
        // Record if the next page is requested.
        public string paging { get; set; }
    ```

    Tato proměnná je řetězec, který obsahuje tlačítko Další"Pokud by měly být odeslány další stránky výsledků, nebo mít hodnotu null pro první stránka hledání.

2. Ve stejném souboru a v rámci oboru názvů přidejte třídu globální proměnné s jednou vlastností. V aplikaci MVC globální proměnné jsou deklarovány ve své vlastní statické třídě. **ResultsPerPage** nastaví počet výsledků na stránku. 

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

### <a name="add-a-vertical-scroll-bar-to-the-view"></a>Přidat do zobrazení svislý posuvník

1. Vyhledejte část souboru index.cshtml, která zobrazuje výsledky (začíná  **@if (Model! = null)** ).

2. V části nahraďte následujícím kódem. Nové **&lt;div&gt;** části spočívá v oblasti, která by měla být posuvný a přidá obě **overflow-y** atribut a volání **onscroll**funkci nazvanou "scrolled()" tak, jako je.

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

3. Přímo pod smyčky po &lt;/div&gt; značky, přidejte **Posunul** funkce.

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

    **Pokud** příkaz ve skriptu vyšší než testy, které chcete zobrazit, pokud se uživatel posunul k dolnímu okraji svislý posuvník. Pokud ano, volání **Domů** řadič se provádí akci volá **Další**. Je potřeba žádné další informace o kontroleru, vrátí data na další stránku. Tato data pak formátována pomocí stejné stylů HTML jako původní stránku. Pokud se nezobrazí žádné výsledky, nic se připojí a věcí, zůstanou jsou.

### <a name="handle-the-next-action"></a>Popisovač akce dalšího

Existují pouze tří akcí, které se mají odeslat na řadič: první spuštění aplikace, která volá **Index()** , první hledat podle uživatelů, která volá **Index(model)** a pak následné volání pro více výsledků prostřednictvím **Next(model)** .

1. Otevřete soubor domácí řadiče a odstraňte **RunQueryAsync** metoda z původní kurzu.

2. Nahradit **Index(model)** akce s následujícím kódem. Nyní zpracovává **stránkování** pole, pokud je hodnota null, nebo nastavte na "next" a zpracovává volání do služby Azure Search.

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

    Podobně jako metodu číselného stránkování, můžeme použít **přeskočit** a **horní** vrátil nastavení vyhledávání o jen data, budeme potřebovat.

3. Přidat **Další** akci pro kontroler home. Všimněte si, jak vrátí seznam, každý hotelu přidáním dvou prvků do seznamu: hotelu název a popis hotelu. Tento formát je nastaven tak, aby odpovídaly **Posunul** použití funkce vrácená data v zobrazení.

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

4. Pokud se zobrazuje chyba syntaxe **seznamu&lt;řetězec&gt;** , pak přidejte následující **pomocí** na začátku souboru řadiče.

    ```cs
    using System.Collections.Generic;
    ```

### <a name="compile-and-run-your-project"></a>Kompilace a spuštění projektu

Teď vyberte **spustit bez ladění** (nebo stiskněte klávesu F5).

1. Zadejte termín, který bude dát spoustu výsledky (například "fond") a poté otestujte svislý posuvník. To aktivovat novou stránku výsledků?

    ![Nekonečné "fond" výsledků procházení](./media/tutorial-csharp-create-first-app/azure-search-infinite-scroll.png)

    > [!Tip]
    > Pokud chcete mít jistotu, že posuvník se zobrazí na stránce první, první stránka výsledků mírně překročit výšku, které se zobrazí v oblasti. V našem příkladu **.box1** má výšku 30 pixelů, **.box2** má 100 pixelů výška _a_ dolní okraj 24 pixelů. Takže každý záznam používá 154 pixelů. Tři položky bude trvat až 3 x 154 = 462 pixelů. Chcete-li zajistit, aby se zobrazí svislý posuvník, musí být nastavena výšku do oblasti zobrazení, který je menší než 462 pixelů, dokonce i 461 funguje. K tomuto problému dochází pouze na první stránce po, který je potřeba se zobrazí posuvník. Řádek k aktualizaci:  **&lt;div id = "myDiv" style = "width: 800px; Height: 450px; přetečení y: posuvníku;"onscroll="scrolled() "&gt;** .

2. Přejděte dolů, až na konec výsledky. Všimněte si, že způsob, jakým je všechny informace na stránce jedno zobrazení. Můžete procházet všechny způsob, jakým zpět na začátek bez aktivace všechna volání serveru.

Složitější nekonečné posuvné systémy použít kolečko myši nebo podobné jiný mechanismus pro aktivaci načtení nové stránky výsledků. Jsme nebude trvá nekonečné žádné další v těchto kurzech posouvání, ale má určitá ovládacího tlačítka na ni a zabraňuje navíc myší, můžete chtít prozkoumat další možnosti Další!

## <a name="takeaways"></a>Shrnutí

Vezměte v úvahu následující takeaways z tohoto projektu:

* Číselného stránkování je dobré pro hledání pořadí výsledky se mohou poněkud libovolného, to znamená i něco, abyste uživatelům na novější stránkách.
* Nekonečné posouvání je dobré, když je zvláště důležité pořadí výsledky. Pokud například výsledky jsou seřazeny na vzdálenost v Centru pro určení města.
* Číselného stránkování umožňuje některé lepší navigace. Například uživatel může mějte na paměti, že byl zajímavé výsledky na stránce 6, že neexistuje žádný takový snadno odkaz v nekonečné posouvání.
* Snadné odvolání, posouvání navýšení nebo snížení kapacity se žádná čísla není přeplněna nepoužitelnými stránky na nekonečnou posouvání má.
* Klíčovou funkcí nekonečné posouvání je, že výsledky jsou připojeny k existující stránky, ne nahrazení tuto stránku, což je efektivnější.
* Dočasné úložiště potrvají pouze jedno volání a je potřeba resetovat nezbytné k překonání další volání.


## <a name="next-steps"></a>Další postup

Stránkování je zásadní pro hledání na Internetu. S stránkování také zahrnuté, dalším krokem je vylepšit uživatelské prostředí dále přidáním našeptávání hledání.

> [!div class="nextstepaction"]
> [C#Kurz: Přidat automatického doplňování a návrhy – Azure Search](tutorial-csharp-type-ahead-and-suggestions.md)
