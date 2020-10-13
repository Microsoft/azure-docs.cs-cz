---
title: Kurz jazyka C# k vytvoření první aplikace
titleSuffix: Azure Cognitive Search
description: Naučte se, jak vytvořit první krok v jazyce C# pro vyhledávání. Tento kurz poskytuje jak odkaz ke stažení na pracovní aplikaci na GitHubu, tak i úplný proces sestavení zcela nové aplikace.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 09/30/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ec98762ac5918437e8fdb8426b54b79b1fb5b222
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2020
ms.locfileid: "91939717"
---
# <a name="tutorial-create-your-first-search-app-using-the-net-sdk"></a>Kurz: Vytvoření první aplikace pro vyhledávání pomocí sady .NET SDK

V tomto kurzu se dozvíte, jak vytvořit webovou aplikaci, která se dotazuje a vrátí výsledky indexu hledání pomocí Azure Kognitivní hledání a sady Visual Studio.

V tomto kurzu se naučíte, jak:

> [!div class="checklist"]
> * Nastavení vývojového prostředí
> * Modelování datových struktur
> * Vytvoření webové stránky pro shromáždění vstupů dotazů a výsledků zobrazení
> * Definování vyhledávací metody
> * Otestování aplikace

Naučíte se také, jak jednoduché je vyhledávací volání. Klíčové příkazy v kódu, který budete vyvíjet, se zapouzdřují na následujících pár řádcích.

```csharp
var options = new SearchOptions()
{
    // The Select option specifies fields for the result set
    options.Select.Add("HotelName");
    options.Select.Add("Description");
};

var searchResult = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);
model.resultList = searchResult.Value.GetResults().ToList();
```

Pouze jedno volání vyhledá index a vrátí výsledky.

:::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-pool.png" alt-text="Hledání * fondu *" border="true":::

## <a name="overview"></a>Přehled

V tomto kurzu se používá existující hostovaný ukázkový index, abyste se mohli soustředit na vytvoření stránky pro hledání, která bude shromažďovat řetězec dotazu pro požadavek a vrátí výsledky. Index obsahuje fiktivní data o hotelu. Jakmile budete mít základní stránku, můžete ji v dalších lekcích rozšířit tak, aby zahrnovala stránkování, omezující vlastnosti a prostředí typu před zahájením.

Hotovou verzi kódu v tomto kurzu najdete v následujícím projektu:

* [1. Basic – vyhledávání – stránka (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/1-basic-search-page)

Tento kurz byl aktualizován tak, aby používal balíček Azure.Search.Documents (verze 11). Starší verzi sady .NET SDK najdete v tématu [Ukázka kódu Microsoft. Azure. Search (verze 10)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10).

## <a name="prerequisites"></a>Požadavky

Vzhledem k tomu, že používáte veřejný index vyhledávání, který je hostovaný Microsoftem, nepotřebujete pro tento kurz službu vyhledávání ani účet Azure.

* [Visual Studio](https://visualstudio.microsoft.com/)

* [Klientská knihovna pro Azure Kognitivní hledání (verze 11)](https://www.nuget.org/packages/Azure.Search.Documents/)

### <a name="install-and-run-the-project-from-github"></a>Instalace a spuštění projektu z GitHubu

Pokud chcete přejít na pracovní aplikaci, postupujte podle následujících kroků a stáhněte a spusťte dokončený kód.

1. Vyhledání ukázky na GitHubu: [Vytvoření první aplikace](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11)

1. V [kořenové složce](https://github.com/Azure-Samples/azure-search-dotnet-samples)vyberte **kód**, následovaný **klonem** nebo **stažením zip** , abyste si mohli vytvořit soukromou místní kopii projektu.

1. Pomocí sady Visual Studio přejděte na a otevřete řešení pro stránku Basic Search ("1 – Basic-Search-Page") a vyberte možnost **Spustit bez ladění** (nebo stiskněte klávesu F5) a sestavte a spusťte program.

1. Toto je index hotelů, takže zadejte slova, která můžete použít k hledání hotelů (například "WiFi", "View", "bar", "parkování") a Prohlédněte si výsledky.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-wifi.png" alt-text="Hledání * fondu *" border="true":::

Snad tento projekt bude pracovat hladce a webová aplikace je spuštěná. Tato jedna aplikace obsahuje mnoho základních komponent pro složitější hledání, takže je vhodné ji projít a znovu vytvořit krok za krokem. Následující části obsahují tyto kroky.

## <a name="set-up-a-development-environment"></a>Nastavení vývojového prostředí

Pokud chcete tento projekt vytvořit úplně od začátku, a tak posílit koncepty Azure Kognitivní hledání na svém mysli, začněte s projektem sady Visual Studio.

1. V aplikaci Visual Studio vyberte **Nový**  >  **projekt**a pak **ASP.NET Core webová aplikace**.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-project1.png" alt-text="Hledání * fondu *" border="true":::

1. Dejte projektu název, jako je například "FirstSearchApp" a nastavte umístění. Vyberte **Vytvořit**.

1. Vyberte šablonu projektu **Webová aplikace (model-zobrazení-kontroler)** .

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-project2.png" alt-text="Hledání * fondu *" border="true":::

1. Nainstalujte klientskou knihovnu. V části **nástroje**  >  **Správce balíčků NuGet**  >  **Spravovat balíčky NuGet pro řešení...**, vyberte **Procházet** a vyhledejte "azure.search.documents". Nainstalujte **Azure.Search.Documents** (verze 11 nebo novější) a přijměte licenční smlouvy a závislosti.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png" alt-text="Hledání * fondu *" border="true":::

### <a name="initialize-azure-cognitive-search"></a>Inicializovat Azure Kognitivní hledání

V této ukázce používáte veřejně dostupná data hotelu. Tato data jsou libovolná kolekce 50 názvů a popisů fiktivního hotelu vytvořená výhradně pro účely poskytování ukázkových dat. Pro přístup k těmto datům zadejte název a klíč rozhraní API.

1. Otevřete **appsettings.js** a nahraďte výchozí řádky následujícím názvem a klíčem. Zde uvedený klíč rozhraní API není příkladem klíče, je to *přesně* klíč, který potřebujete pro přístup k datům hotelu. Váš soubor by teď měl vypadat nějak takto.

    ```csharp
    {
        "SearchServiceName": "azs-playground",
        "SearchServiceQueryApiKey": "EA4510A6219E14888741FCFC19BFBB82"
    }
    ```

1. V Průzkumník řešení vyberte soubor a v části Vlastnosti změňte nastavení **Kopírovat do výstupního adresáře** na **Kopírovat, pokud je novější**.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png" alt-text="Hledání * fondu *" border="true":::

## <a name="model-data-structures"></a>Modelování datových struktur

Modely (třídy jazyka C#) se používají ke sdělování dat mezi klientem (zobrazení), serverem (řadičem) a také cloudem Azure pomocí architektury MVC (model, zobrazení, kontroler). Obvykle tyto modely odrážejí strukturu dat, ke kterým se přistupoval.

V tomto kroku napíšete datové struktury indexu vyhledávání a také hledaný řetězec, který se používá v zobrazení nebo komunikaci s řadičem. V indexu hotelů má každý Hotel mnoho místností a každý Hotel má adresu s více částmi. Zcela celá reprezentace hotelu je hierarchická a vnořená struktura dat. Pro vytvoření jednotlivých komponent budete potřebovat tři třídy.

Sada tříd **hotelů**, **Address**a **Room** je známá jako [*komplexní typy*](search-howto-complex-data-types.md), což je důležitou funkcí služby Azure kognitivní hledání. Komplexní typy mohou představovat mnoho úrovní hluboko tříd a podtříd a povolit mnohem složitější datové struktury, které mají být reprezentovány, než použití *jednoduchých typů* (třída obsahující pouze primitivní členy).

1. V Průzkumník řešení klikněte pravým tlačítkem na **modely**  >  **Přidat**  >  **novou položku**.

1. Vyberte**Třída** a pojmenujte ji Hotel.cs. Nahraďte veškerý obsah Hotel.cs následujícím kódem. Všimněte si, že **adresa** a členové **místnosti** třídy, tato pole jsou samotné třídy, takže budete potřebovat také modely pro ně.

    ```csharp
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;
    using Microsoft.Spatial;
    using System;
    using System.Text.Json.Serialization;

    namespace FirstAzureSearchApp.Models
    {
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
    }
    ```

1. Opakujte stejný postup vytvoření modelu pro třídu **adres** a pojmenujte soubor Address.cs. Nahraďte obsah následujícím.

    ```csharp
    using Azure.Search.Documents.Indexes;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Address
        {
            [SearchableField]
            public string StreetAddress { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string City { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string StateProvince { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string PostalCode { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Country { get; set; }
        }
    }
    ```

1. A znovu použijte stejný postup k vytvoření třídy **Room** a pojmenujte soubor Room.cs.

    ```csharp
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;
    using System.Text.Json.Serialization;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Room
        {
            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnMicrosoft)]
            public string Description { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrMicrosoft)]
            [JsonPropertyName("Description_fr")]
            public string DescriptionFr { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string Type { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public double? BaseRate { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string BedOptions { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public int SleepsCount { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public bool? SmokingAllowed { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string[] Tags { get; set; }
        }
    }
    ```

1. Poslední model, který vytvoříte v tomto kurzu, je třída s názvem **SearchData** , která představuje vstup uživatele (**prohledávanýtext**) a výstup hledání (**resultList**). Typ výstupu je kritický, **searchResults &lt; Hotel &gt; **, protože tento typ přesně odpovídá výsledkům hledání a Vy musíte tento odkaz předat do zobrazení. Nahraďte výchozí šablonu následujícím kódem.

    ```csharp
    using Azure.Search.Documents.Models;

    namespace FirstAzureSearchApp.Models
    {
        public class SearchData
        {
            // The text to search for.
            public string searchText { get; set; }

            // The list of results.
            public SearchResults<Hotel> resultList;
        }
    }
    ```

## <a name="create-a-web-page"></a>Vytvoření webové stránky

Šablony projektů obsahují řadu zobrazení klienta umístěných ve složce **views** . Přesná zobrazení závisí na verzi základního rozhraní .NET, kterou používáte (3,1 se v této ukázce používá). Pro účely tohoto kurzu upravíte **index. cshtml** , aby zahrnoval prvky vyhledávací stránky.

Odstraňte obsah souboru index. cshtml v celém rozsahu a znovu sestavte soubor v následujících krocích.

1. V tomto kurzu se v zobrazení používá dva malé obrázky: logo Azure a ikona hledání (azure-logo.png a search.png). Zkopírujte napříč obrázky z projektu GitHub do složky **wwwroot/images** v projektu.

1. První řádek indexu. cshtml by měl odkazovat na model používaný ke sdělování dat mezi klientem (zobrazení) a serverem (kontroler), který je dříve vytvořeným modelem **SearchData** . Přidejte tento řádek do souboru index. cshtml.

    ```csharp
    @model FirstAzureSearchApp.Models.SearchData
    ```

1. Je standardní postup, jak zadat název zobrazení, takže další řádky by měly být:

    ```csharp
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

1. Za nadpisem zadejte odkaz na šablonu stylů HTML, kterou vytvoříte za chvíli.

    ```csharp
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

1. Tělo zobrazení zpracovává dva případy použití. Nejdřív musí před zadáním libovolného vyhledávaného textu zadat prázdnou stránku při prvním použití. Za druhé musí kromě textového pole Hledat pro opakované dotazy zpracovávat výsledky.

   Chcete-li zpracovat oba případy, je nutné ověřit, zda je model poskytnutý zobrazení prázdný. Model s hodnotou null označuje první případ použití (počáteční spuštění aplikace). Přidejte následující do souboru index. cshtml a přečtěte si komentáře.

    ```csharp
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
                @Model.resultList.Count Results
            </p>

            @for (var i = 0; i < Model.resultList.Count; i++)
            {
                // Display the hotel name and description.
                @Html.TextAreaFor(m => m.resultList[i].Document.HotelName, new { @class = "box1" })
                @Html.TextArea($"desc{i}", Model.resultList[i].Document.Description, new { @class = "box2" })
            }
        }
    }
    </body>
    ```

1. Přidejte šablonu stylů. **V aplikaci Visual Studio v souboru** >  **Nový**  >  **soubor**vyberte možnost **Šablona stylů** (se zvýrazněnou možností **Obecné** ).

   Výchozí kód nahraďte následujícím kódem. Do tohoto souboru nebudeme mít žádné další podrobnosti. Tyto styly jsou standardní HTML.

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

1. Uložte soubor šablony stylů jako hotely. CSS do složky **wwwroot/CSS** společně s výchozím souborem Web. CSS.

Tím se dokončí naše zobrazení. V tomto okamžiku jsou oba modely i zobrazení dokončené. Pouze kontroler je ponechán k propojení všeho.

## <a name="define-methods"></a>Definovat metody

V tomto kroku změňte obsah **domovského kontroleru**.

1. Otevřete soubor HomeController.cs a nahraďte příkazy **using** následujícím příkazem.

    ```csharp
    using Azure;
    using Azure.Search.Documents;
    using Azure.Search.Documents.Indexes;
    using FirstAzureSearchApp.Models;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Configuration;
    using System;
    using System.Diagnostics;
    using System.Linq;
    using System.Threading.Tasks;
    ```

### <a name="add-index-methods"></a>Přidat metody indexu

V aplikaci MVC je metoda **index ()** metodou výchozí akce pro libovolný kontroler. Otevře se stránka s indexem HTML. Výchozí metoda, která nepřijímá žádné parametry, se používá v tomto kurzu pro případ spuštění aplikace: vykreslení prázdné vyhledávací stránky.

V této části rozšiřujeme metodu, která podporuje druhý případ použití: vykreslování stránky, když uživatel zadal hledaný text. Pro podporu tohoto případu je metoda indexu rozšířena tak, aby jako parametr převzala model.

1. Po výchozí metodě **index ()** přidejte následující metodu.

    ```csharp
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

    Všimněte si **asynchronní** deklarace metody a volání **await** pro **RunQueryAsync**. Tato klíčová slova se postará o provádění asynchronních volání, čímž se vyhnete blokování vláken na serveru.

    Blok **catch** používá model chyb, který byl vytvořen ve výchozím nastavení.

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>Poznamenejte si zpracování chyb a další výchozí zobrazení a metody.

V závislosti na verzi .NET Core, kterou používáte, se ve výchozím nastavení vytvoří trochu odlišná sada výchozích zobrazení. Pro .NET Core 3,1 jsou výchozí zobrazení index, soukromí a chyba. Tyto výchozí stránky můžete zobrazit při spuštění aplikace a zkontrolujte, jak jsou zpracovávány v řadiči.

V tomto kurzu budete testovat zobrazení chyb později.

V ukázce GitHubu se odstraní nepoužívaná zobrazení a jejich přidružené akce.

### <a name="add-the-runqueryasync-method"></a>Přidat metodu RunQueryAsync

Volání služby Azure Kognitivní hledání je zapouzdřeno v naší metodě **RunQueryAsync** .

1. Nejdřív přidejte některé statické proměnné, které nastaví službu Azure, a zavolejte k jejich zahájení.

    ```csharp
        private static SearchClient _searchClient;
        private static SearchIndexClient _indexClient;
        private static IConfigurationBuilder _builder;
        private static IConfigurationRoot _configuration;

        private void InitSearch()
        {
            // Create a configuration using appsettings.json
            _builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            _configuration = _builder.Build();

            // Read the values from appsettings.json
            string searchServiceUri = _configuration["SearchServiceUri"];
            string queryApiKey = _configuration["SearchServiceQueryApiKey"];

            // Create a service and index client.
            _indexClient = new SearchIndexClient(new Uri(searchServiceUri), new AzureKeyCredential(queryApiKey));
            _searchClient = _indexClient.GetSearchClient("hotels");
        }
    ```

2. Nyní přidejte samotnou metodu **RunQueryAsync** .

    ```csharp
    private async Task<ActionResult> RunQueryAsync(SearchData model)
    {
        InitSearch();

        var options = new SearchOptions() { };

        // Enter Hotel property names into this list so only these values will be returned.
        // If Select is empty, all values will be returned, which can be inefficient.
        options.Select.Add("HotelName");
        options.Select.Add("Description");

        // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
        var searchResult = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);
        model.resultList = searchResult.Value.GetResults().ToList();

        // Display the results.
        return View("Index", model);
    }
    ```

    V této metodě Nejdřív zajistěte zahájení naší konfigurace Azure a pak nastavte nějaké možnosti hledání. Možnost **Select** určuje, která pole se mají vracet ve výsledcích, a tak odpovídá názvům vlastností ve třídě **hotelu** . Pokud vynecháte **možnost Select**, vrátí se všechna neskrytá pole, což může být neefektivní, pokud zajímáte pouze podmnožinu všech možných polí.

    Asynchronní volání metody Search formuluje požadavek (modelované jako **prohledávanýtext**) a odpověď (modelované jako **SearchResult**). Pokud ladíte tento kód, třída **SearchResult** je vhodným kandidátem pro nastavení bodu přerušení, pokud potřebujete prošetřit obsah **modelu. resultList**. Měli byste zjistit, že je intuitivní a poskytuje vám data, která jste si vyžádali, a ne mnohem jinak.

### <a name="test-the-app"></a>Otestování aplikace

Teď zkontrolujeme, jestli aplikace funguje správně.

1. Vyberte **ladit**  >  **Spustit bez ladění** nebo stiskněte klávesu **F5**. Pokud aplikace funguje podle očekávání, měli byste získat počáteční zobrazení indexu.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-index.png" alt-text="Hledání * fondu *" border="true":::

1. Zadejte řetězec dotazu, například "plážový" (nebo libovolný text, který je na mysli), a kliknutím na ikonu hledání odešlete žádost.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-beach.png" alt-text="Hledání * fondu *" border="true":::

1. Zkuste zadat "pět hvězdiček". Všimněte si, že tento dotaz nevrací žádné výsledky. Propracovanější hledání má za následek "pět hvězdiček" jako synonymum "luxus" a vrátí tyto výsledky. Podpora [synonym](search-synonyms.md) je dostupná v Azure kognitivní hledání, ale není zahrnutá v této sérii kurzů.

1. Zkuste zadat text "Hot" jako text hledání. Nevrací _položky_ se slovem "Hotel". Hledání je prohledáno pouze celá slova, i když je vráceno několik výsledků.

1. Zkuste použít jiná slova: "fond", "slunečno", "View" a libovolná. Uvidíte, že Azure Kognitivní hledání pracuje na nejjednodušším, ale stále přesvědčit úroveň.

## <a name="test-edge-conditions-and-errors"></a>Podmínky a chyby hraničního testu

Je důležité ověřit, že naše funkce zpracování chyb fungují tak, jak by měly, a to i v případě, že všechno funguje dokonale. 

1. V metodě **index** po volání **Try {** zavolejte řádek **throw New Exception ()**. Tato výjimka vynutí při hledání textu chybu.

2. Spusťte aplikaci, jako hledaný text zadejte "bar" a klikněte na ikonu hledání. Výjimka by měla být výsledkem zobrazení chyby.

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-error.png" alt-text="Hledání * fondu *" border="true":::

    > [!Important]
    > Je považováno za bezpečnostní riziko, které vrátí čísla interních chyb na chybové stránce. Pokud je vaše aplikace určena pro obecné použití, proveďte některé šetření na zabezpečené a osvědčené postupy, které se vrátí, když dojde k chybě.

3. Odeberte **vyvolat novou výjimku ()** , pokud jste přesvědčeni, že zpracování chyb funguje, jak by mělo.

## <a name="takeaways"></a>Shrnutí

Vezměte v úvahu následující poznatky z tohoto projektu:

* Volání služby Azure Kognitivní hledání je stručné a je snadné interpretovat výsledky.
* Asynchronní volání přidávají k řadiči malé množství složitosti, ale osvědčeným postupem je, že máte v úmyslu vyvíjet aplikace kvality.
* Tato aplikace provedla jasné hledání textu definované podle toho, co je nastaveno v **příznacích searchOptions jsou**. Tuto jednu třídu však lze naplnit mnoha členy, kteří přidávají sofistikovanější k hledání. K tomu, aby tato aplikace byla výrazně výkonnější, není potřeba žádná další práce.

## <a name="next-steps"></a>Další kroky

Chcete-li zlepšit činnost koncového uživatele, přidejte další funkce, zejména stránkování (buď pomocí čísel stránek, nebo nekonečné posouvání), a funkci automatického dokončování a návrhů. Můžete také zvážit propracovanější možnosti hledání (například geografická prohledávání v pohostincích v rámci zadaného poloměru daného bodu a řazení výsledků hledání).

Tyto další kroky jsou řešeny ve zbývajících kurzech. Pojďme začít používat stránkování.

> [!div class="nextstepaction"]
> [Kurz C#: stránkování výsledků hledání – Azure Kognitivní hledání](tutorial-csharp-paging.md)