---
title: C# kurz k vytvoření první aplikace
titleSuffix: Azure Cognitive Search
description: Přečtěte si, jak vytvořit první vyhledávací aplikaci C# krok za krokem. Kurz poskytuje jak odkaz na pracovní aplikaci na GitHubu, tak celý proces sestavení aplikace od začátku. Seznamte se se základními součástmi Azure Cognitive Search.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: 2b4f67fc448d98239947fd764d4926f1d590c5e2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77121588"
---
# <a name="c-tutorial-create-your-first-app---azure-cognitive-search"></a>Kurz C#: Vytvořte si první aplikaci – Azure Cognitive Search

Zjistěte, jak vytvořit webové rozhraní pro dotazování a prezentaci výsledků hledání z indexu pomocí Azure Cognitive Search. Tento kurz začíná existujícím hostovaným indexem, takže se můžete soustředit na vytváření vyhledávací stránky. Index obsahuje fiktivní údaje o hotelu. Jakmile budete mít základní stránku, můžete ji vylepšit v následujících lekcích tak, aby zahrnovala stránkování, omezující stránky a prostředí typu dopředu.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Nastavení vývojového prostředí
> * Modelové datové struktury
> * Vytvoření webové stránky
> * Definovat metody
> * Otestování aplikace

Dozvíte se také, jak přímočaré je volání do vyhledávání. Klíčové příkazy v kódu, který budete vyvíjet, jsou zapouzdřeny v následujících několika řádcích.

```cs
var parameters = new SearchParameters
{
    // Enter Hotel property names into this list, so only these values will be returned.
    Select = new[] { "HotelName", "Description" }
};

DocumentSearchResult<Hotel> results  = await _indexClient.Documents.SearchAsync<Hotel>("search text", parameters);
```

Toto jedno volání zahájí hledání dat Azure a vrátí výsledky.

![Hledání "bazénu"](./media/tutorial-csharp-create-first-app/azure-search-pool.png)


## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

[Nainstalujte Visual Studio](https://visualstudio.microsoft.com/) pro použití jako ide.

### <a name="install-and-run-the-project-from-github"></a>Instalace a spuštění projektu z GitHubu

1. Vyhledejte ukázku na [GitHubu: Vytvořte první aplikaci](https://github.com/Azure-Samples/azure-search-dotnet-samples).
1. Vyberte **Klonovat nebo stáhnout** a vytvořit si vlastní místní kopii projektu.
1. Pomocí sady Visual Studio přejděte na a otevřete řešení pro základní vyhledávací stránku a vyberte **spustit bez ladění** (nebo stiskněte klávesu F5).
1. Zadejte některá slova (například "wifi", "view", "bar", "parkování") a prohlédněte si výsledky!

    ![Hledání "wifi"](./media/tutorial-csharp-create-first-app/azure-search-wifi.png)

Doufejme, že tento projekt poběží hladce a máte spuštěnou aplikaci Azure. Mnoho základních součástí pro sofistikovanější vyhledávání je součástí této aplikace, takže je dobré ji projít a znovu ji vytvořit krok za krokem.

Chcete-li vytvořit tento projekt od začátku, a tím pomoci posílit součásti Azure Cognitive Search ve vaší mysli, projděte si následující kroky.

## <a name="set-up-a-development-environment"></a>Nastavení vývojového prostředí

1. V Sadě Visual Studio 2017 nebo novější vyberte **Nový/Project** a **pak ASP.NET základní webovou aplikaci**. Pojmenujte projekt například "FirstAzureSearchApp".

    ![Vytvoření cloudového projektu](./media/tutorial-csharp-create-first-app/azure-search-project1.png)

2. Po klepnutí na tlačítko **OK** pro tento typ projektu obdržíte druhou sadu možností, které se vztahují k tomuto projektu. Vyberte **webovou aplikaci (Model-View-Controller).**

    ![Vytvoření projektu MVC](./media/tutorial-csharp-create-first-app/azure-search-project2.png)

3. Dále v nabídce **Nástroje** vyberte **Správce balíčků NuGet** a pak spravovat **balíčky NuGet pro řešení...**. Je tu jeden balíček, který musíme nainstalovat. Vyberte kartu **Procházet** a do vyhledávacího pole zadejte "Azure Cognitive Search". Nainstalujte **Microsoft.Azure.Search,** když se zobrazí v seznamu (verze 9.0.1 nebo novější). Budete muset proklikat několik dalších dialogových oken k dokončení instalace.

    ![Přidání knihoven Azure pomocí NuGetu](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

### <a name="initialize-azure-cognitive-search"></a>Inicializovat Azure Cognitive Search

Pro tuto ukázku používáme veřejně dostupná data o hotelu. Tato data jsou libovolnou sbírkou 50 fiktivních názvů a popisů hotelů, vytvořených výhradně za účelem poskytování demo dat. Chcete-li získat přístup k těmto datům, musíte zadat název a klíč pro ně.

1. Otevřete soubor appsettings.json v novém projektu a nahraďte výchozí řádky následujícím názvem a klíčem. Zde uvedený klíč ROZHRANÍ API není příkladem klíče, je to _přesně_ ten klíč, který potřebujete pro přístup k datům hotelu. Váš soubor appsettings.json by teď měl vypadat takto.

    ```cs
    {
        "SearchServiceName": "azs-playground",
        "SearchServiceQueryApiKey": "EA4510A6219E14888741FCFC19BFBB82"
    }
    ```

2. S tímto souborem jsme ještě neskončili, vyberte vlastnosti tohoto souboru a změňte nastavení **Kopírovat do výstupního adresáře** na **Kopírovat, pokud je novější**.

    ![Kopírování nastavení aplikace do výstupu](./media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png)

## <a name="model-data-structures"></a>Modelové datové struktury

Modely (třídy C#) se používají ke komunikaci dat mezi klientem (zobrazením), serverem (řadičem) a také cloudem Azure pomocí architektury MVC (model, zobrazení, řadič). Tyto modely obvykle budou odrážet strukturu dat, která je přístupná. Také potřebujeme model pro zpracování komunikace pohledu/kontroloru.

1. Otevřete složku **Modely** projektu pomocí Průzkumníka řešení a uvidíte tam jeden výchozí model: **ErrorViewModel.cs**.

2. Klikněte pravým tlačítkem myši na složku **Modely** a vyberte **přidat** **položku**. Potom v dialogovém okně, které se zobrazí, vyberte **ASP.NET Jádro** a pak první možnost **Class**. Přejmenujte soubor CS na Hotel.cs a klepněte na **tlačítko Přidat**. Nahraďte veškerý obsah Hotel.cs následujícím kódem. Všimněte **si, adresa** a **pokoj** členové třídy, tato pole jsou třídy sami, takže budeme potřebovat modely pro ně taky.

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

3. Postupujte stejným způsobem při vytváření modelu pro třídu **Adresa,** s výjimkou názvu souboru Address.cs. Nahraďte obsah následujícím.

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

4. A opět postupujte stejným způsobem a vytvořte třídu **Room** a pojmenujte soubor Room.cs. Opět nahraďte obsah následujícím.

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

5. Sada **tříd Hotel**, **Address**a **Room** je to, co se v Azure označuje jako [_složité typy_](search-howto-complex-data-types.md), což je důležitá funkce Azure Cognitive Search. Komplexní typy mohou být mnoho úrovní hluboké třídy a podtřídy a povolit mnohem složitější datové struktury, které mají být reprezentovány než pomocí _jednoduchých typů_ (třída obsahující pouze primitivní členy). Potřebujeme ještě jeden model, takže projít procesem vytvoření nové třídy modelu znovu, i když tentokrát volejte třídu SearchData.cs a nahradit výchozí kód s následujícím.

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

    Tato třída obsahuje vstup uživatele (**searchText**) a výstup hledání (**resultList**). Typ výstupu je kritický, **&lt;DocumentSearchResult&gt;Hotel**, protože tento typ přesně odpovídá výsledkům z hledání a musíme předat tento odkaz do zobrazení.



## <a name="create-a-web-page"></a>Vytvoření webové stránky

Projekt, který jste vytvořili, ve výchozím nastavení vytvoří několik zobrazení klienta. Přesné zobrazení závisí na verzi core .NET, kterou používáte (v této ukázce používáme verzi 2.1). Všechny jsou ve složce **Zobrazení** projektu. Budete muset pouze upravit soubor Index.cshtml (ve složce **Zobrazení/Domov).**

Odstraňte obsah index.cshtml v plném rozsahu a znovu vytvořit soubor v následujících krocích.

1. V zobrazení používáme dva malé obrázky. Můžete použít vlastní nebo zkopírovat přes obrázky z projektu GitHub: azure-logo.png a search.png. Tyto dva obrázky by měly být umístěny ve složce **wwwroot/images.**

2. První řádek Index.cshtml by měl odkazovat na model, který budeme používat ke komunikaci dat mezi klientem (zobrazení) a serverem (řadič), což je model **SearchData,** který jsme vytvořili. Přidejte tento řádek do souboru Index.cshtml.

    ```cs
    @model FirstAzureSearchApp.Models.SearchData
    ```

3. Je běžnou praxí zadat název zobrazení, takže další řádky by měly být:

    ```cs
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

4. Za názvem zadejte odkaz na html styl, který brzy vytvoříme.

    ```cs
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

5. Nyní k masu pohledu. Klíčovou věcí k zapamatování je, že pohled musí zvládnout dvě situace. Za prvé, musí zpracovat displej při prvním spuštění aplikace a uživatel dosud nezadal žádný vyhledávací text. Za druhé, musí zpracovávat zobrazení výsledků, kromě vyhledávacího textového pole, pro opakované použití uživatelem. Chcete-li zpracovat tyto dvě situace, musíme zkontrolovat, zda model k dispozici zobrazení je null nebo ne. Null model označuje, že jsme v první ze dvou situací (počáteční spuštění aplikace). Přidejte následující do souboru Index.cshtml a přečtěte si komentáře.

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

6. Nakonec přidáme šablonu stylů. V sadě Visual Studio vyberte v nabídce **Soubor** **možnost Nový/Soubor** a potom **Šablona stylů** (se zvýrazněnou **položkou Obecné).** Nahraďte výchozí kód následujícím kódem. Nebudeme jít do tohoto souboru v žádném detailu, styly jsou standardní HTML.

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

7. Soubor stylů uložte jako hotels.css do složky wwwroot/css spolu s výchozím souborem site.css.

To dokončuje náš názor. Děláme velký pokrok. Modely a pohledy jsou dokončeny, pouze řadič je ponecháno svázat vše dohromady.

## <a name="define-methods"></a>Definovat metody

Musíme upravit obsah jednoho řadiče **(Home Controller),** který je vytvořen ve výchozím nastavení.

1. Otevřete soubor HomeController.cs a nahraďte **příkazy using** následujícím.

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

Potřebujeme dvě metody **indexu,** jednu bez parametrů (pro případ, kdy je aplikace poprvé otevřena), a jednu s modelem jako parametrem (pro když uživatel zadal hledaný text). První z těchto metod je vytvořen ve výchozím nastavení. 

1. Za výchozí metodu **Index()** přidejte následující metodu.

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

    Všimněte si **asynchronní** deklarace metody a **await** volání **RunQueryAsync**. Tato klíčová slova se starají o to, aby naše volání byla asynchronní, a proto se vyhněte blokování vláken na serveru.

    Blok **catch** používá model chyby, který byl vytvořen pro nás ve výchozím nastavení.

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>Všimněte si zpracování chyb a dalších výchozích zobrazení a metod

V závislosti na tom, kterou verzi rozhraní .NET Core používáte, je ve výchozím nastavení vytvořena mírně odlišná sada výchozích zobrazení. Pro rozhraní .NET Core 2.1 jsou výchozími zobrazeními rejstřík, informace, kontakt, ochrana osobních údajů a chyba. Pro rozhraní .NET Core 2.2 jsou například výchozí zobrazení Index, Ochrana osobních údajů a Chyba. V obou případech můžete zobrazit tyto výchozí stránky při spuštění aplikace a zkontrolujte, jak jsou zpracovány v řadiči.

Budeme testování zobrazení chyby později v tomto kurzu.

V ukázce GitHub jsme odstranili nevyužité zobrazení a jejich přidružené akce.

### <a name="add-the-runqueryasync-method"></a>Přidání metody RunQueryAsync

Volání Azure Cognitive Search je zapouzdřeno v naší metodě **RunQueryAsync.**

1. Nejprve přidejte některé statické proměnné k nastavení služby Azure a volání k jejich zahájení.

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

2. Nyní přidejte samotnou metodu **RunQueryAsync.**

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

    V této metodě nejprve zajistíme, že je spuštěna naše konfigurace Azure, a pak nastavíme některé parametry vyhledávání. Názvy polí v parametru **Select** přesně odpovídají názvům vlastností ve třídě **hotelu.** Je možné vynechat **Select** parametr, v takovém případě jsou vráceny všechny vlastnosti. Nastavení žádné **parametry Select** je však neefektivní, pokud nás zajímá pouze podmnožina dat. Zadáním vlastností, které nás zajímají, jsou vráceny pouze tyto vlastnosti.

    Asynchronní volání vyhledávání (**model.resultList = await _indexClient.Documents.SearchAsync&lt;Hotel&gt;(model.searchText, parametry);**) je to, co tento návod a aplikace jsou všechny kolem. **DocumentSearchResult** Třída je zajímavá a dobrý nápad (při spuštění aplikace) je nastavit zarážku zde a použít ladicí program prozkoumat obsah **model.resultList**. Měli byste zjistit, že je intuitivní, poskytuje vám data, která jste žádali, a nic jiného.

Nyní pro okamžik pravdy.

### <a name="test-the-app"></a>Otestování aplikace

Nyní zkontrolujeme, zda aplikace běží správně.

1. Vyberte **Ladění/start bez ladění** nebo stiskněte klávesu F5. Pokud jste zakódovali věci správně, získáte počáteční zobrazení indexu.

     ![Otevření aplikace](./media/tutorial-csharp-create-first-app/azure-search-index.png)

2. Zadejte text, například "pláž" (nebo jakýkoli text, který přijde na mysl), a klikněte na ikonu hledání. Měl bys dostat nějaké výsledky.

     ![Hledání "pláže"](./media/tutorial-csharp-create-first-app/azure-search-beach.png)

3. Zkuste zadat "pět hvězdiček". Všimněte si, jak získat žádné výsledky. Sofistikovanější hledání by považovalo "pět hvězdiček" za synonymum pro "luxus" a vrátilo by tyto výsledky. Použití synonym je k dispozici v Azure Cognitive Search, i když nebudeme pokrývající v prvních kurzech.
 
4. Zkuste zadat "hot" jako hledaný text. _Nevrací_ položky se slovem "hotel" v nich. Naše hledání je pouze umístění celých slov, i když několik výsledků jsou vráceny.

5. Zkuste jiná slova: "bazén", "sluníčko", "pohled" a cokoliv jiného. Uvidíte Azure Cognitive Search pracuje na jeho nejjednodušší, ale stále přesvědčivé úrovni.

## <a name="test-edge-conditions-and-errors"></a>Podmínky a chyby zkušební hrany

Je důležité ověřit, zda naše funkce zpracování chyb fungují tak, jak by měly, i když věci fungují perfektně. 

1. V **Metodě Index** po volání **try {** zadejte řádek Throw **new Exception()**. Tato výjimka vynutí chybu při hledání textu.

2. Spusťte aplikaci, zadejte "bar" jako hledaný text a klikněte na ikonu hledání. Výjimka by měla mít za následek zobrazení chyb.

     ![Vynucení chyby](./media/tutorial-csharp-create-first-app/azure-search-error.png)

    > [!Important]
    > Za bezpečnostní riziko se vrací interní čísla chyb na chybových stránkách. Pokud je vaše aplikace určena pro obecné použití, proveďte některé šetření v oblasti bezpečné a osvědčené postupy, co se má vrátit, když dojde k chybě.

3. Odebrat **Throw new Exception()** pokud jste spokojeni zpracování chyb funguje tak, jak by mělo.

## <a name="takeaways"></a>Shrnutí

Vezměme si následující stánek s jídlem z tohoto projektu:

* Azure Cognitive Search volání je stručné a je snadné interpretovat výsledky.
* Asynchronní volání přidat malé množství složitosti řadiče, ale jsou osvědčeným postupem, pokud máte v úmyslu vyvíjet kvalitní aplikace.
* Tato aplikace provedla jednoduché vyhledávání textu, definované tím, co je nastaveno ve **vyhledávacích parametrech**. Tato jedna třída však může být naplněna mnoha členy, které přidávají sofistikovanost do hledání. Není mnoho další práce je potřeba, aby se tato aplikace podstatně silnější.

## <a name="next-steps"></a>Další kroky

Abychom mohli poskytovat co nejlepší uživatelské prostředí pomocí Azure Cognitive Search, musíme přidat další funkce, zejména stránkování (buď pomocí čísel stránek nebo nekonečné posouvání) a automatické dokončování/návrhy. Měli bychom také zvážit sofistikovanější vyhledávací parametry (například geografické vyhledávání hotelů v rámci určeného okruhu daného bodu a pořadí výsledků vyhledávání).

Tyto další kroky jsou řešeny v řadě kurzů. Začněme stránkováním.

> [!div class="nextstepaction"]
> [Kurz jazyka C#: Stránkování výsledků hledání – Azure Cognitive Search](tutorial-csharp-paging.md)


