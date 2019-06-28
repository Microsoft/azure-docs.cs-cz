---
title: C#kurz k vytvoření první aplikace – Azure Search
description: Tento kurz obsahuje podrobný návod, jak vytvářet svoji první aplikaci pro službu Azure Search. Tento kurz poskytuje i odkaz na pracovní aplikaci na Githubu a dokončit proces, jak vytvořit aplikaci úplně od začátku. Další informace o základní součásti služby Azure Search.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 05/01/2019
ms.openlocfilehash: d569437a3e6f6f05ddb9c6fa85f62c77ac51f72b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443804"
---
# <a name="c-tutorial-create-your-first-app---azure-search"></a>C#kurz: Vytvoření první aplikace – Azure Search

Zjistěte, jak vytvořit webové rozhraní pro dotaz a k dispozici výsledky hledání z indexu pomocí Azure Search. Tento kurz pracuje s indexem existující, prostředí tak, aby se mohli soustředit na vytváření stránku vyhledávání. Index obsahuje fiktivní hotelu data. Jakmile budete mít základní stránku, ji můžete vylepšit v následujících lekcích stránkování, omezujících vlastností a s automatickým dokončováním prostředí.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Nastavení vývojového prostředí
> * Model datové struktury
> * Vytvoření webové stránky
> * Definování metody
> * Testování aplikace

Dozvíte se taky, jak jednoduché je volání hledání. Klíče příkazů v kódu, budete vyvíjet jsou zapouzdřeny v následujícím po zadání několika řádků.

```cs
var parameters = new SearchParameters
{
    // Enter Hotel property names into this list, so only these values will be returned.
    Select = new[] { "HotelName", "Description" }
};

DocumentSearchResult<Hotel> results  = await _indexClient.Documents.SearchAsync<Hotel>("search text", parameters);
```

Toto volání zahájí prohledávání dat Azure a vrátí výsledky.

![Hledání "fond"](./media/tutorial-csharp-create-first-app/azure-search-pool.png)


## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

[Instalace sady Visual Studio](https://visualstudio.microsoft.com/) pro použití jako rozhraní IDE.

### <a name="install-and-run-the-project-from-github"></a>Instalace a spuštění projektu z Githubu

1. Najděte ukázky na Githubu: [Vytvoření první aplikace](https://github.com/Azure-Samples/azure-search-dotnet-samples).
1. Vyberte **klonovat nebo stáhnout** a vytvořit místní privátní kopii projektu.
1. Pomocí sady Visual Studio, přejděte do umístění a otevřete řešení základní hledání stránky a vyberte **spustit bez ladění** (nebo stiskněte klávesu F5).
1. Zadejte některá slova ("například"Wi-Fi", Zobrazit", "bar", "parkovací") a podívejte se na výsledky!

    ![Hledání "Wi-Fi"](./media/tutorial-csharp-create-first-app/azure-search-wifi.png)

Snad tento projekt poběží naprosto hladce a běžet na platformě Azure. Mnoho základních komponent pro sofistikovanější hledání jsou součástí jedné aplikace, proto je vhodné ji projít a jejímu krok za krokem.

K vytvoření tohoto projektu od začátku a proto pomohli posílit komponenty služby Azure Search ve své rozhodnutí, projděte si následující kroky.

## <a name="set-up-a-development-environment"></a>Nastavení vývojového prostředí

1. V sadě Visual Studio 2017 nebo později, vyberte **New/projekt** pak **webové aplikace ASP.NET Core**. Pojmenujte projekt jako je například "FirstAzureSearchApp".

    ![Vytvoření projektu cloudu](./media/tutorial-csharp-create-first-app/azure-search-project1.png)

2. Po klepnutí **OK** pro tento typ projektu, budete mít druhou sadu možností, které se vztahují k tomuto projektu. Vyberte **webová aplikace (Model-View-Controller)** .

    ![Vytvoření projektu aplikace MVC](./media/tutorial-csharp-create-first-app/azure-search-project2.png)

3. Vedle **nástroje** nabídce vyberte možnost **Správce balíčků NuGet** a potom **spravovat balíčky NuGet pro řešení...** . Existuje jeden balíček, který potřebujeme k instalaci. Vyberte **Procházet** kartě pak do vyhledávacího pole zadejte "Azure Search". Nainstalujte **Microsoft.Azure.Search** až se zobrazí v seznamu (verze 9.0.1, nebo novější). Budete muset klikněte na tlačítko prostřednictvím několika dalších dialogových oken pro dokončení instalace.

    ![Pomocí nástroje NuGet pro přidání knihoven Azure](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

### <a name="initialize-azure-search"></a>Inicializovat Azure Search

K této ukázce používáme veřejně dostupné hotelu data. Tato data jsou libovolná kolekce 50 fiktivní hotelu názvy a popisy, vytvořených výhradně za účelem poskytování ukázková data. Pokud chcete přístup k těmto datům, musíte zadat název a klíč pro něj.

1. Otevřete soubor appsettings.json v novém projektu a nahradit výchozí řádky s následujícím názvem a klíčem. Klíč rozhraní API je znázorněno zde není příklad klíče, je _přesně_ klíč budete potřebovat pro přístup k datům hotelu. Soubor appsettings.json by měl nyní vypadat takto.

    ```cs
    {
        "SearchServiceName": "azs-playground",
        "SearchServiceQueryApiKey": "EA4510A6219E14888741FCFC19BFBB82"
    }
    ```

2. Můžeme se provádí s tímto souborem ještě, vyberte vlastnosti pro tento soubor a změňte **kopírovat do výstupního adresáře** nastavení **kopírovat, pokud je novější**.

    ![Kopírování nastavení aplikace do výstupu](./media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png)

## <a name="model-data-structures"></a>Model datové struktury

Modely (C# třídy) se používají k přenosu dat mezi klientem (view), serveru (řadič) a také Azure cloudu s využitím architektury MVC (model, zobrazení, řadič). Tyto modely obvykle bude odrážet strukturu dat, která se právě využívají. Navíc potřebujeme model pro zpracování kontroler zobrazení/komunikace.

1. Otevřete **modely** složky vašeho projektu pomocí Průzkumníka řešení kde se zobrazí jeden výchozí model tady: **ErrorViewModel.cs**.

2. Klikněte pravým tlačítkem myši **modely** a pak zvolte položku **přidat** pak **nová položka**. Pak v zobrazeném dialogu vyberte **ASP.NET Core** první možnost **třídy**. Přejmenovat soubor .cs Hotel.cs a klikněte na tlačítko **přidat**. Veškerý obsah Hotel.cs nahraďte následujícím kódem. Všimněte si, že **adresu** a **místnosti** členy třídy, tato pole jsou samotné třídy, potřebujeme modely pro ně příliš.

    ```cs
    using System;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Spatial;
    using Newtonsoft.Json;

    namespace FirstAzureSearchApp.Models
    {
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
    }
    ```

3. Opakujte stejný postup vytváření model, který **adresu** třídy, s tím rozdílem, název souboru Address.cs. Nahraďte obsah následujícím kódem.

    ```cs
    using Microsoft.Azure.Search;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Address
        {
            [IsSearchable]
            public string StreetAddress { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string City { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string StateProvince { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string PostalCode { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Country { get; set; }
        }
    }
    ```

4. A znovu opakujte stejný postup k vytvoření **místnosti** třídy pojmenování souboru Room.cs. Znovu nahraďte obsah následujícím kódem.

    ```cs
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Newtonsoft.Json;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Room
        {
            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnMicrosoft)]

            public string Description { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.FrMicrosoft)]
            [JsonProperty("Description_fr")]
            public string DescriptionFr { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string Type { get; set; }

            [IsFilterable, IsFacetable]
            public double? BaseRate { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string BedOptions { get; set; }

            [IsFilterable, IsFacetable]

            public int SleepsCount { get; set; }

            [IsFilterable, IsFacetable]
            public bool? SmokingAllowed { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string[] Tags { get; set; }
        }
    }
    ```

5. Sada **hotelu**, **adresu**, a **místnosti** jsou třídy, která se označuje v Azure jako [ _komplexní typy_ ](search-howto-complex-data-types.md), důležitou funkcí služby Azure Search. Komplexní typy lze mnoho úrovní do hloubky tříd a podtříd a povolit mnohem složitější datové struktury zastoupen než použití _jednoduché typy_ (obsahuje pouze členy základní třídy). Potřebujeme jeden další model, proto projít procesem vytvoření nové třídy modelu znovu, ale tentokrát volat třídu SearchData.cs a nahraďte kód následujícím.

    ```cs
    using Microsoft.Azure.Search.Models;

    namespace FirstAzureSearchApp.Models
    {
        public class SearchData
        {
            // The text to search for.
            public string searchText { get; set; }

            // The list of results.
            public DocumentSearchResult<Hotel> resultList;
        }
    }
    ```

    Tato třída obsahuje uživatelský vstup (**Prohledávanýtext**), a výstupu hledání (**resultList**). Typ výstupu je velmi důležité, **DocumentSearchResult&lt;hotelu&gt;** , jak přesně odpovídá výsledků hledání podle tohoto typu, a My potřebujeme předat tento odkaz k zobrazení.



## <a name="create-a-web-page"></a>Vytvoření webové stránky

Projekt, který jste vytvořili ve výchozím nastavení vytvoří klient několik zobrazení. Přesné zobrazení závisí na verzi .NET Core použijete (používáme 2.1 v této ukázce). Všechny jsou v **zobrazení** složky projektu. Je potřeba jenom změnit souboru Index.cshtml (v **zobrazení Domů** složky).

Odstraňte obsah Index.cshtml v celém rozsahu a znovu soubor sestavit do následujících kroků.

1. Používáme dva malé obrázky v zobrazení. Můžete použít vlastní nebo kopírovat na bitové kopie z projektu z Githubu: azure-logo.png a search.png. Tyto dvě bitové kopie musí být umístěné ve **wwwroot/imagí** složky.

2. První řádek Index.cshtml by měly odkazovat modelu použijeme k přenosu dat mezi klientem (zobrazení) a serveru (řadič), který je **SearchData** modelu jsme vytvořili. Přidejte následující řádek do souboru Index.cshtml.

    ```cs
    @model FirstAzureSearchApp.Models.SearchData
    ```

3. Je obvyklé, zadejte název zobrazení, by tak měly být následující řádky:

    ```cs
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

4. Následující název zadejte odkaz na šablonu stylů HTML, který vytvoříme za chvíli.

    ```cs
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

5. Nyní na maso zobrazení. Nejdůležitější pamatovat si je, že zobrazení má pro zpracování dvě situace. Za prvé je musí zpracovat zobrazení při prvním spuštění aplikace a uživatel nebyl dosud zadány žádné hledaný text. Za druhé musí umět zpracovat zobrazení výsledků vedle textového pole hledání pro opakované použití uživatelem. Pro zpracování těchto dvou případů, musíme zkontrolujte, zda model, který poskytuje zobrazení má hodnotu null, nebo ne. Modelu null znamená, že se v první dvě situace (počátečním spuštění aplikace). Přidejte následující k souboru Index.cshtml a přečtěte si komentáře.

    ```cs
    <body>
    <h1 class="sampleTitle">
        <img src="~/images/azure-logo.png" width="80" />
        Hotels Search
    </h1>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        // Display the search text box, with the search icon to the right of it.
        <div class="searchBoxForm">
            @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
        </div>

        @if (Model != null)
        {
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Results.Count) Results
            </p>

            @for (var i = 0; i < Model.resultList.Results.Count; i++)
            {
                // Display the hotel name and description.
                @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box2" })
            }
        }
    }
    </body>
    ```

6. Nakonec přidáme šablony stylů. V sadě Visual Studio v **souboru** nabídky vyberte možnost **nový/soubor** pak **stylů** (s **Obecné** zvýrazněné). Nahraďte kód následujícím. Nesmí být budeme do tohoto souboru v některém podrobněji, styly jsou standardní HTML.

    ```html
    textarea.box1 {
        width: 648px;
        height: 30px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
    }

    textarea.box2 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }

    .sampleTitle {
        font: 32px/normal 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
        margin: 20px 0;
        font-size: 32px;
        text-align: left;
    }

    .sampleText {
        font: 16px/bold 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
        margin: 20px 0;
        font-size: 14px;
        text-align: left;
        height: 30px;
    }

    .searchBoxForm {
        width: 648px;
        box-shadow: 0 0 0 1px rgba(0,0,0,.1), 0 2px 4px 0 rgba(0,0,0,.16);
        background-color: #fff;
        display: inline-block;
        border-collapse: collapse;
        border-spacing: 0;
        list-style: none;
        color: #666;
    }

    .searchBox {
        width: 568px;
        font-size: 16px;
        margin: 5px 0 1px 20px;
        padding: 0 10px 0 0;
        border: 0;
        max-height: 30px;
        outline: none;
        box-sizing: content-box;
        height: 35px;
        vertical-align: top;
    }

    .searchBoxSubmit {
        background-color: #fff;
        border-color: #fff;
        background-image: url(/images/search.png);
        background-repeat: no-repeat;
        height: 20px;
        width: 20px;
        text-indent: -99em;
        border-width: 0;
        border-style: solid;
        margin: 10px;
        outline: 0;
    }
    ```

7. Uložte soubor šablony stylů jako hotels.css, do složky wwwroot/css, spolu s výchozí soubor site.css.

Tím končí naše zobrazení. Provádíme velký pokrok. Modely a zobrazení jsou dokončeny, pouze kontroleru je ponecháno na vše spojí dohromady.

## <a name="define-methods"></a>Definování metody

Potřebujeme upravit obsah jednoho řadiče (**kontroler Home**), které se vytvořily ve výchozím nastavení.

1. Otevřete soubor HomeController.cs a nahraďte **pomocí** příkazy následujícím kódem.

    ```cs
    using System;
    using System.Diagnostics;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using FirstAzureSearchApp.Models;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    ```

### <a name="add-index-methods"></a>Přidejte metody indexu

Potřebujeme dva **Index** metody, jeden pro provádění bez parametrů (pro případ, při prvním otevření aplikace) a jeden trvá modelu jako parametr (Pokud uživatel zadal hledaný text). První z těchto metod se vytvoří ve výchozím nastavení. 

1. Přidejte následující metodu po výchozí **Index()** metody.

    ```cs
        [HttpPost]
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                // Ensure the search string is valid.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }

                // Make the Azure Search call.
                await RunQueryAsync(model);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

    Všimněte si, že **asynchronní** deklarace metody a **await** volání **RunQueryAsync**. Tato klíčová slova postará o našich volání asynchronní a vyhněte se blokování vlákna na serveru.

    **Catch** blok používá model chyb, který byl vytvořen pro nás ve výchozím nastavení.

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>Poznámka: zpracování chyb a další výchozí zobrazení a metody

V závislosti na tom, kterou verzi .NET Core použijete, mírně odlišnou sadu výchozí zobrazení vytvořené ve výchozím nastavení. Výchozí zobrazení pro .NET Core 2.1 se Index, About, Contact, ochrany osobních údajů a chyba. Pro .NET Core 2.2 například výchozí zobrazení jsou Index, ochrany osobních údajů a chyba. V obou případech se může zobrazit tyto výchozí stránky při spuštění aplikace a prozkoumejte, jak se zpracovává v kontroleru.

Jsme budete testovat zobrazení chyb později v tomto kurzu.

V ukázce na Githubu jsme odstranili nevyužité zobrazení a jejich přidružených akcí.

### <a name="add-the-runqueryasync-method"></a>Přidejte metodu RunQueryAsync

Azure Search volání, je zapouzdřena v našich **RunQueryAsync** metody.

1. Nejprve přidáte některé statické proměnné, jak nastavit službu Azure a volání k zahájení je.

    ```cs
        private static SearchServiceClient _serviceClient;
        private static ISearchIndexClient _indexClient;
        private static IConfigurationBuilder _builder;
        private static IConfigurationRoot _configuration;

        private void InitSearch()
        {
            // Create a configuration using the appsettings file.
            _builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            _configuration = _builder.Build();

            // Pull the values from the appsettings.json file.
            string searchServiceName = _configuration["SearchServiceName"];
            string queryApiKey = _configuration["SearchServiceQueryApiKey"];

            // Create a service and index client.
            _serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(queryApiKey));
            _indexClient = _serviceClient.Indexes.GetClient("hotels");
        }
    ```

2. Teď přidejte **RunQueryAsync** metoda sama.

    ```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model)
        {
            InitSearch();

            var parameters = new SearchParameters
            {
                // Enter Hotel property names into this list so only these values will be returned.
                // If Select is empty, all values will be returned, which can be inefficient.
                Select = new[] { "HotelName", "Description" }
            };

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

            // Display the results.
            return View("Index", model);
        }
    ```

    V této metodě zajišťujeme nejdřív naše konfigurace Azure iniciované a pak nastavit některé parametry vyhledávání. Názvy polí v **vyberte** parametr odpovídat přesně v názvech vlastností **hotelu** třídy. Je možné nechat **vyberte** parametr, v takovém případě budou vráceny všechny vlastnosti. Nastavení, ale ne **vyberte** je neefektivní, pokud nás zajímají pouze podmnožinu dat parametry. Zadáním vlastnosti, které nás zajímají, jsou vráceny pouze tyto vlastnosti.

    Asynchronní volání pro vyhledávání (**model.resultList = await _indexClient.Documents.SearchAsync&lt;hotelu&gt;(model.searchText, parametry);** ) je tento kurz a aplikace jsou všechno. **DocumentSearchResult** třída je některý z zajímavé a vhodné (když je aplikace spuštěna) je zde nastavit zarážky a zkontrolovat obsah pomocí ladicího programu **model.resultList**. Měli byste najít, že je intuitivní, vám poskytnou data, která se zobrazí dotaz a mnohem else.

Nyní pravdivost chvíli of.

### <a name="test-the-app"></a>Testování aplikace

Teď Pojďme se podívat na spuštění aplikace správně.

1. Vyberte **ladění/spuštění bez ladění** nebo stiskněte klávesu F5. Pokud jste nakódovali věci správně, získáte počáteční Index zobrazení.

     ![Otevřete aplikaci](./media/tutorial-csharp-create-first-app/azure-search-index.png)

2. Zadejte text, například "beach" (nebo jakýkoli text, který vás napadne) a klikněte na ikonu hledání. Byste získali nějaké výsledky.

     ![Hledání "beach"](./media/tutorial-csharp-create-first-app/azure-search-beach.png)

3. Zkuste zadat "five star". Všimněte si, jak získat žádné výsledky. Složitější vyhledávání by "five star" považovat za synonymum pro "luxusní" a vrátí výsledky. Použití synonym je k dispozici ve službě Azure Search, i když jsme nesmí být pokrývající ho v první kurzech.
 
4. Zkuste zadat "horkými" jako hledaný text. Provádí _není_ vrátí položky obsahující slovo "hotel" v nich. Naše vyhledávání pouze hledá celá slova, i když několik výsledky jsou vráceny.

5. Vyzkoušejte jiná slova: "fond", "sluníčko a mrak", "Zobrazit" a cokoli, co. Zobrazí se Azure Search funguje v nejjednodušším, ale stále přesvědčivé úroveň.

## <a name="test-edge-conditions-and-errors"></a>Edge podmínky testu a chyby

Je důležité ověřit, že naše funkce zpracování chyb fungovat jak by měly, i v případě, že věci pracují něco nedobrého. 

1. V **Index** metoda, po **zkuste {** volání, zadejte řádek, který **Throw nové Exception()** . Tato výjimka vynutí chybu při vyhledávání na text.

2. Spuštění aplikace, zadejte "bar", stejně jako hledaný text a klikněte na ikonu hledání. Výjimky by měly mít za následek zobrazení chyb.

     ![Platnost chybu](./media/tutorial-csharp-create-first-app/azure-search-error.png)

    > [!Important]
    > Bude považován za bezpečnostní riziko pro vrácení čísla došlo k vnitřní chybě v chybové stránky. Pokud vaše aplikace je určena pro obecné použití, udělejte některé vyšetřování zabezpečení a osvědčené postupy toho, jak vrátit, pokud dojde k chybě.

3. Odebrat **Throw nové Exception()** až budete spokojení zpracování funguje, jak by mělo chyb.

## <a name="takeaways"></a>Shrnutí

Vezměte v úvahu následující takeaways z tohoto projektu:

* Volání rozhraní Azure Search je stručné a snadno interpretovat výsledky.
* Byla zahájena asynchronní volání malé množství složitost přidejte k řadiči, ale jsou osvědčený postup, pokud máte v úmyslu k vývoji kvalitních aplikací.
* Tato aplikace provádí jednoduché textové vyhledávání, definované jaké je nastavení **parametrech vyhledávání**. Tato jedna třída, ale je možné naplnit mnoho členů, které aplikacím dodávají sofistikovanější vyhledávání. Nevěnuje další práce je třeba vytvořit tuto aplikaci výrazně výkonnější.

## <a name="next-steps"></a>Další postup

Aby bylo možné poskytovat nejlepší uživatelské prostředí pomocí služby Azure Search, je potřeba přidat další funkce, zejména stránkování (buď pomocí čísla stránek nebo nekonečné posouvání) a automatické dokončování a návrhy. Jsme měli také zvážit sofistikovanější parametry vyhledávání (například geografické vyhledávání na hotely v rámci zadaného úhlu časovém okamžiku a výsledky hledání řazení).

Tyto další kroky jsou popsány v sérii kurzů. Začněme stránkování.

> [!div class="nextstepaction"]
> [C#Kurz: Stránkování výsledků vyhledávání – Azure Search](tutorial-csharp-paging.md)


