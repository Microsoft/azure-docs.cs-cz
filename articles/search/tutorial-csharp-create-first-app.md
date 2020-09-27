---
title: Kurz jazyka C# k vytvoření první aplikace
titleSuffix: Azure Cognitive Search
description: Naučte se, jak vytvořit první aplikaci pro vyhledávání v C# – krok za krokem. Kurz poskytuje odkaz na funkční aplikaci na GitHubu a kompletní proces sestavení zcela nové aplikace. Přečtěte si o základních součástech Azure Kognitivní hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 09/25/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 49dc551039e0fd82ddb4374713c59fca2f493b62
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2020
ms.locfileid: "91397942"
---
# <a name="tutorial-create-your-first-search-app-using-the-net-sdk"></a>Kurz: Vytvoření první aplikace pro vyhledávání pomocí sady .NET SDK

Naučte se vytvářet webové rozhraní pro dotazování a prezentaci výsledků hledání z indexu pomocí Azure Kognitivní hledání. Tento kurz začíná s existujícím hostovaným indexem, abyste se mohli soustředit na vytváření vyhledávací stránky. Index obsahuje fiktivní data o hotelu. Jakmile budete mít základní stránku, můžete ji v dalších lekcích rozšířit tak, aby zahrnovala stránkování, omezující vlastnosti a prostředí typu před zahájením.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Nastavení vývojového prostředí
> * Modelování datových struktur
> * Vytvoření webové stránky
> * Definovat metody
> * Otestování aplikace

Naučíte se také, jak jednoduché je vyhledávací volání. Klíčové příkazy v kódu, který budete vyvíjet, se zapouzdřují na následujících pár řádcích.

```cs
var parameters = new SearchParameters
{
    // Enter Hotel property names into this list, so only these values will be returned.
    Select = new[] { "HotelName", "Description" }
};

DocumentSearchResult<Hotel> results  = await _indexClient.Documents.SearchAsync<Hotel>("search text", parameters);
```

Toto jedno volání iniciuje hledání dat Azure a vrátí výsledky.

:::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-pool.png" alt-text="Hledání * fondu *" border="false":::


## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

[Nainstalujte Visual Studio](https://visualstudio.microsoft.com/) pro použití jako rozhraní IDE.

### <a name="install-and-run-the-project-from-github"></a>Instalace a spuštění projektu z GitHubu

1. Vyhledání ukázky na GitHubu: [Vytvoření první aplikace](https://github.com/Azure-Samples/azure-search-dotnet-samples)
1. Vyberte **klonovat nebo stáhnout** a vytvořte soukromou místní kopii projektu.
1. Pomocí sady Visual Studio přejděte na a otevřete řešení pro stránku základní vyhledávání a vyberte **Spustit bez ladění** (nebo stiskněte klávesu F5).
1. Zadejte některá slova (například "WiFi", "View", "bar", "parkování") a Prohlédněte si výsledky.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-wifi.png" alt-text="Hledání * Wi-Fi *" border="false":::

Snad tento projekt bude pracovat hladce a máte spuštěnou aplikaci Azure. Tato jedna aplikace obsahuje mnoho základních komponent pro složitější hledání, takže je vhodné ji projít a znovu vytvořit krok za krokem.

Pokud chcete tento projekt vytvořit úplně od začátku, a proto vám pomůžeme posílit součásti Azure Kognitivní hledání na svém mysli, Projděte si následující kroky.

## <a name="set-up-a-development-environment"></a>Nastavení vývojového prostředí

1. V aplikaci Visual Studio 2017 nebo novější vyberte možnost **Nový/projekt** a pak **ASP.NET Core webová aplikace**. Dejte projektu název, jako je například "FirstAzureSearchApp".

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-project1.png" alt-text="Vytvoření cloudového projektu" border="false":::

2. Po kliknutí na **OK** pro tento typ projektu se vám nabídne druhá sada možností, které se vztahují na tento projekt. Vyberte možnost **Webová aplikace (model-zobrazení-kontroler)**.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-project2.png" alt-text="Vytvoření projektu MVC":::

3. Potom v nabídce **nástroje** vyberte **Správce balíčků NuGet** a pak **spravujte balíčky NuGet pro řešení...**. Je potřeba nainstalovat jeden balíček. Vyberte kartu **Procházet** a do vyhledávacího pole zadejte "Azure kognitivní hledání". Nainstalujte **Microsoft. Azure. Search** , když se objeví v seznamu (verze 9.0.1 nebo novější). Abyste mohli instalaci dokončit, budete muset kliknout na několik dalších dialogových oken.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png" alt-text="Přidání knihoven Azure pomocí NuGet" border="false":::

### <a name="initialize-azure-cognitive-search"></a>Inicializovat Azure Kognitivní hledání

V této ukázce používáme veřejně dostupná data o hotelu. Tato data jsou libovolná kolekce 50 názvů a popisů fiktivního hotelu vytvořená výhradně pro účely poskytování ukázkových dat. Aby bylo možné získat přístup k těmto datům, je nutné zadat název a klíč pro něj.

1. Otevřete appsettings.jsv souboru v novém projektu a nahraďte výchozí řádky následujícím názvem a klíčem. Zde uvedený klíč rozhraní API není příkladem klíče, je to _přesně_ klíč, který potřebujete pro přístup k datům hotelu. Váš appsettings.jsv souboru by teď měl vypadat takto.

    ```cs
    {
        "SearchServiceName": "azs-playground",
        "SearchServiceQueryApiKey": "EA4510A6219E14888741FCFC19BFBB82"
    }
    ```

2. Tento soubor ještě není hotový, vyberte vlastnosti pro tento soubor a změňte nastavení **Kopírovat do výstupního adresáře** na kopírovat, pokud je **novější**.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png" alt-text="Kopírování nastavení aplikace do výstupu" border="false":::

## <a name="model-data-structures"></a>Modelování datových struktur

Modely (třídy jazyka C#) se používají ke sdělování dat mezi klientem (zobrazení), serverem (řadičem) a také cloudem Azure pomocí architektury MVC (model, zobrazení, kontroler). Obvykle tyto modely odrážejí strukturu dat, ke kterým se přistupoval. Potřebujeme také model pro zpracování komunikace zobrazení/kontroleru.

1. Otevřete složku **modely** vašeho projektu pomocí Průzkumník řešení a v části se zobrazí jeden výchozí model: **ErrorViewModel.cs**.

2. Klikněte pravým tlačítkem na složku **modely** a vyberte **Přidat** a **Nová položka**. Pak v dialogovém okně, které se zobrazí, vyberte **ASP.NET Core** pak první **třídu**možností. Přejmenujte soubor. cs na Hotel.cs a klikněte na **Přidat**. Nahraďte veškerý obsah Hotel.cs následujícím kódem. Všimněte si, že **adresa** a **konverzační** členové třídy, tato pole jsou samotné třídy, takže pro ně budeme potřebovat modely.

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

3. Použijte stejný postup vytvoření modelu pro třídu **adres** , s výjimkou názvu souboru Address.cs. Nahraďte obsah následujícím.

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

4. A znovu použijte stejný postup k vytvoření třídy **Room** a pojmenujte soubor Room.cs. Znovu nahraďte obsah následujícím.

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

5. Sada tříd **hotelů**, **adres**a **místností** je v Azure známá jako [_komplexní typy_](search-howto-complex-data-types.md), což je důležitou funkcí služby Azure kognitivní hledání. Komplexní typy mohou představovat mnoho úrovní hluboko tříd a podtříd a povolit mnohem složitější datové struktury, které mají být reprezentovány, než použití _jednoduchých typů_ (třída obsahující pouze primitivní členy). Potřebujeme ještě jeden další model, takže provedete to tak, že znovu vytvoříte novou třídu modelu, ale tentokrát zavoláte třídu SearchData.cs a nahradíte výchozí kód následujícím kódem.

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

    Tato třída obsahuje vstup uživatele (**prohledávanýtext**) a výstup hledání (**resultList**). Typ výstupu je kritický, **DocumentSearchResult &lt; Hotel &gt; **, protože tento typ přesně odpovídá výsledkům hledání a musíme tento odkaz předat do zobrazení.



## <a name="create-a-web-page"></a>Vytvoření webové stránky

Projekt, který jste vytvořili, bude ve výchozím nastavení vytvářet množství zobrazení klienta. Přesná zobrazení závisí na verzi základního rozhraní .NET, kterou používáte (v této ukázce používáme 2,1). Jsou všechny ve složce **zobrazení** projektu. Budete muset upravit pouze soubor index. cshtml (v **zobrazeních/domovské** složce).

Odstraňte obsah souboru index. cshtml v celém rozsahu a znovu sestavte soubor v následujících krocích.

1. V zobrazení používáme dvě malé obrázky. Můžete použít vlastní nebo zkopírovat image z projektu GitHub: azure-logo.png a search.png. Tyto dva obrázky by měly být umístěny ve složce **wwwroot/images** .

2. První řádek indexu. cshtml by měl odkazovat na model, který budeme používat ke sdělování dat mezi klientem (zobrazení) a serverem (kontroler), což je **SearchData** model, který jsme vytvořili. Přidejte tento řádek do souboru index. cshtml.

    ```cs
    @model FirstAzureSearchApp.Models.SearchData
    ```

3. Je standardní postup, jak zadat název zobrazení, takže další řádky by měly být:

    ```cs
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

4. Za nadpisem zadejte odkaz na šablonu stylů HTML, kterou vytvoříme za chvíli.

    ```cs
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

5. Nyní k mase zobrazení. Klíčovým aspektem je pamatovat si, že zobrazení musí zpracovávat dvě situace. Za prvé musí při prvním spuštění aplikace zpracovat zobrazení a uživatel ještě nezadal žádný hledaný text. Za druhé musí zpracovávat zobrazení výsledků, a to i v textovém poli hledání pro opakované použití uživatelem. Pro zpracování těchto dvou situací musíme ověřit, zda je model zadaný pro zobrazení null nebo ne. Model s hodnotou null označuje, že jsme v prvním ze dvou případů (počáteční spuštění aplikace). Přidejte následující do souboru index. cshtml a přečtěte si komentáře.

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

6. Nakonec přidáme šablonu stylů. V aplikaci Visual Studio v nabídce **soubor** vyberte možnost **nový/soubor** a potom na položku **Šablona stylů** (se zvýrazněnou možností **Obecné** ). Výchozí kód nahraďte následujícím kódem. Do tohoto souboru nebudeme mít žádné další podrobnosti. Tyto styly jsou standardní HTML.

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

7. Uložte soubor šablony stylů jako hotely. CSS do složky wwwroot/CSS společně s výchozím souborem Web. CSS.

Tím se dokončí naše zobrazení. Máme dobrý pokrok. Modely a zobrazení jsou dokončeny, pouze kontroler je ponechán k propojení všeho.

## <a name="define-methods"></a>Definovat metody

Musíme upravit obsah jednoho kontroleru (**domovského kontroleru**), který je ve výchozím nastavení vytvořený.

1. Otevřete soubor HomeController.cs a nahraďte příkazy **using** následujícím příkazem.

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

### <a name="add-index-methods"></a>Přidat metody indexu

Potřebujeme dvě metody **indexu** , jednu bez parametrů (pro případ, kdy je aplikace poprvé otevřená), a jeden z nich jako parametr (Pokud uživatel zadal hledaný text). První z těchto metod je vytvořena ve výchozím nastavení. 

1. Po výchozí metodě **index ()** přidejte následující metodu.

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

                // Make the Azure Cognitive Search call.
                await RunQueryAsync(model);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

    Všimněte si **asynchronní** deklarace metody a volání **await** pro **RunQueryAsync**. Tato klíčová slova se postará o asynchronní volání a vyhnout se přitom blokování vláken na serveru.

    Blok **catch** používá model chyb, který se ve výchozím nastavení vytvořil pro nás.

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>Poznamenejte si zpracování chyb a další výchozí zobrazení a metody.

V závislosti na verzi .NET Core, kterou používáte, se ve výchozím nastavení vytvoří trochu odlišná sada výchozích zobrazení. Pro .NET Core 2,1 jsou výchozí zobrazení index, o, kontakt, ochrana osobních údajů a chyba. Pro .NET Core 2,2 jsou například výchozí zobrazení index, soukromí a chyba. V obou případech můžete při spuštění aplikace zobrazit tyto výchozí stránky a zjistit, jak jsou zpracovávány v řadiči.

V tomto kurzu budeme testovat zobrazení chyb později.

V ukázce GitHub jsme odstranili nepoužívaná zobrazení a jejich přidružené akce.

### <a name="add-the-runqueryasync-method"></a>Přidat metodu RunQueryAsync

Volání služby Azure Kognitivní hledání je zapouzdřeno v naší metodě **RunQueryAsync** .

1. Nejdřív přidejte některé statické proměnné, které nastaví službu Azure, a zavolejte k jejich zahájení.

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

2. Nyní přidejte samotnou metodu **RunQueryAsync** .

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

    V této metodě Nejdřív zajistěte zahájení naší konfigurace Azure a pak nastavíme některé parametry hledání. Názvy polí v parametru **Select** odpovídají přesně názvům vlastností v třídě **hotelu** . Je možné ponechat parametr **Select** a v takovém případě jsou vráceny všechny vlastnosti. Nastavení žádného parametru **Select** je neefektivní, pokud se zajímá pouze podmnožina dat. Když zadáte vlastnosti, které vás zajímají, vrátí se jenom tyto vlastnosti.

    Asynchronní volání vyhledávání (**model. resultList = await _indexClient.Documents. SearchAsync &lt; Hotel &gt; (model. prohledávanýtext, Parameters);**) je to, co se týká tohoto kurzu a aplikace. Třída **DocumentSearchResult** je zajímavá a dobrý nápad (Pokud je aplikace spuštěná), je nastavit zarážku a použít ladicí program k prohlédnutí obsahu **model. resultList**. Měli byste zjistit, že je intuitivní a poskytuje vám data, která jste si vyžádali, a ne mnohem jinak.

Teď přichází okamžik pravdy.

### <a name="test-the-app"></a>Otestování aplikace

Teď zkontrolujeme, jestli je aplikace správně spuštěná.

1. Vyberte **ladit/spustit bez ladění** nebo stiskněte klávesu F5. Pokud máte správně kódované věci, zobrazí se počáteční zobrazení indexu.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-index.png" alt-text="Otevírání aplikace" border="false":::

2. Zadejte text, například "plážový" (nebo libovolný text, který je na mysli), a klikněte na ikonu hledání. Měli byste získat nějaké výsledky.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-beach.png" alt-text="Hledání * pláž *" border="false":::

3. Zkuste zadat "pět hvězdiček". Všimněte si, že nezískáte žádné výsledky. Propracovanější hledání má za následek "pět hvězdiček" jako synonymum "luxus" a vrátí tyto výsledky. Použití synonym je k dispozici v Azure Kognitivní hledání, ale nebudeme je pokrýt v prvním výukovém programu.
 
4. Zkuste zadat text "Hot" jako text hledání. Nevrací _položky_ se slovem "Hotel". Hledání je prohledáno pouze celá slova, i když je vráceno několik výsledků.

5. Zkuste použít jiná slova: "fond", "slunečno", "View" a libovolná. Uvidíte, že Azure Kognitivní hledání pracuje na nejjednodušším, ale stále přesvědčit úroveň.

## <a name="test-edge-conditions-and-errors"></a>Podmínky a chyby hraničního testu

Je důležité ověřit, že naše funkce zpracování chyb fungují tak, jak by měly, a to i v případě, že všechno funguje dokonale. 

1. V metodě **index** po volání **Try {** zavolejte řádek **throw New Exception ()**. Tato výjimka vynutí chybu při hledání textu.

2. Spusťte aplikaci, jako hledaný text zadejte "bar" a klikněte na ikonu hledání. Výjimka by měla být výsledkem zobrazení chyby.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-error.png" alt-text="Vynutit chybu" border="false":::

    > [!Important]
    > Je považováno za bezpečnostní riziko, které vrátí čísla interních chyb na chybové stránce. Pokud je vaše aplikace určena pro obecné použití, proveďte některé šetření na zabezpečené a osvědčené postupy, které se vrátí, když dojde k chybě.

3. Odeberte **vyvolat novou výjimku ()** , pokud jste přesvědčeni, že zpracování chyb funguje, jak by mělo.

## <a name="takeaways"></a>Shrnutí

Vezměte v úvahu následující poznatky z tohoto projektu:

* Volání služby Azure Kognitivní hledání je stručné a je snadné interpretovat výsledky.
* Asynchronní volání přidávají k řadiči malé množství složitosti, ale osvědčeným postupem je, že máte v úmyslu vyvíjet aplikace kvality.
* Tato aplikace provedla jasné hledání textu definované podle toho, co je nastaveno v **searchParameters**. Tuto jednu třídu však lze naplnit mnoha členy, kteří přidávají sofistikovanější k hledání. K tomu, aby tato aplikace byla výrazně výkonnější, není potřeba žádná další práce.

## <a name="next-steps"></a>Další kroky

Abychom zajistili nejlepší uživatelské prostředí pomocí Azure Kognitivní hledání, musíme přidat další funkce, zejména stránkování (buď pomocí čísel stránek, nebo nekonečné posouvání), a automatické dokončování a návrhy. Měli byste taky zvážit složitější vyhledávací parametry (například geografická prohledávání v pohostincích v rámci zadaného poloměru daného bodu a řazení výsledků hledání).

Tyto další kroky jsou řešeny v řadě kurzů. Pojďme začít používat stránkování.

> [!div class="nextstepaction"]
> [Kurz C#: stránkování výsledků hledání – Azure Kognitivní hledání](tutorial-csharp-paging.md)


