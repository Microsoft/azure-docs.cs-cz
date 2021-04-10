---
title: Kurz jazyka C# pro řazení výsledků
titleSuffix: Azure Cognitive Search
description: Tento kurz C# ukazuje, jak seřadit výsledky hledání. Sestavuje se v předchozím projektu hotelů, řadí se podle primární vlastnosti, sekundární vlastnosti a zahrnuje profil vyhodnocování pro přidání kritérií zvyšování.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/26/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 1f8100dd6340383eadec5d10b7f23db59ba0ebdf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98786381"
---
# <a name="tutorial-order-search-results-using-the-net-sdk"></a>Kurz: seřazení výsledků hledání pomocí sady .NET SDK

V celé této sérii kurzů byly výsledky vráceny a zobrazeny ve [výchozím pořadí](index-add-scoring-profiles.md#what-is-default-scoring). V tomto kurzu přidáte primární a sekundární kritéria řazení. Jako alternativu k řazení na základě numerických hodnot ukazuje konečný příklad řazení výsledků na základě vlastního profilu vyhodnocování. Trochu provedeme také hlubší zobrazení _komplexních typů_.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Seřazení výsledků na základě jedné vlastnosti
> * Seřazení výsledků na základě více vlastností
> * Seřazení výsledků na základě profilu vyhodnocování

## <a name="overview"></a>Přehled

Tento kurz rozšiřuje nekonečný posun projektu vytvořený v kurzu [Přidání stránkování do výsledků hledání](tutorial-csharp-paging.md) .

Hotovou verzi kódu v tomto kurzu najdete v následujícím projektu:

* [5-Order-Results (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/5-order-results)

## <a name="prerequisites"></a>Požadavky

* [2b – přidat – nenekonečné – Scroll (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2b-add-infinite-scroll) – řešení. Tento projekt může být vlastní verze sestavená z předchozího kurzu nebo kopie z GitHubu.

Tento kurz byl aktualizován tak, aby používal balíček [Azure.Search.Documents (verze 11)](https://www.nuget.org/packages/Azure.Search.Documents/) . Starší verzi sady .NET SDK najdete v tématu [Ukázka kódu Microsoft. Azure. Search (verze 10)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10).

## <a name="order-results-based-on-one-property"></a>Seřazení výsledků na základě jedné vlastnosti

Při řazení výsledků na základě jedné vlastnosti, jako je například hodnocení hotelu, nepotřebujeme pro seřazené výsledky také potvrzení, že je pořadí správné. Když do výsledků přidáte pole hodnocení, umožníme vám potvrdit správné řazení výsledků.

V tomto cvičení také přidáme další informace k zobrazení výsledků: tempo nejlevnější místností a nejdražších pokojových kurzů pro každý Hotel.

Není nutné upravovat žádné modely, aby bylo možné řazení povolit. Jenom zobrazení a kontroler vyžadují aktualizace. Začněte otevřením domovského kontroleru.

### <a name="add-the-orderby-property-to-the-search-parameters"></a>Přidání vlastnosti OrderBy do parametrů hledání

1. V HomeController. cs přidejte možnost **OrderBy** a zahrňte vlastnost hodnocení s sestupným pořadím řazení. V metodě **index (model SearchData)** přidejte do parametrů hledání následující řádek.

    ```cs
    options.OrderBy.Add("Rating desc");
    ```

    >[!Note]
    > Výchozí pořadí je vzestupné, i když můžete přidat `asc` do vlastnosti, která tuto vlastnost zruší. Sestupné pořadí je určeno přidáním `desc` .

1. Nyní spusťte aplikaci a zadejte libovolný běžný hledaný termín. Výsledky mohou nebo nemusí být ve správném pořadí, stejně jako vývojář, nikoli uživatel, ale mají snadný způsob ověření výsledků.

1. Pojďme to vymazat výsledky jsou seřazené na hodnocení. Nejdřív nahraďte třídy **box1** a **Box2** v souboru hotelů. CSS následujícími třídami (tyto třídy jsou všechny nové, které pro tento kurz potřebujeme).

    ```html
    textarea.box1A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box1B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box2A {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        padding-left: 5px;
        text-align: left;
    }

    textarea.box2B {
        width: 324px;
        height: 32px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        color: blue;
        text-align: right;
        padding-right: 5px;
    }

    textarea.box3 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }
    ```

    > [!Tip]
    > Prohlížeče obvykle ukládají soubory CSS do mezipaměti a to může vést k použití starého souboru CSS a vaše úpravy se ignorují. Dobrým způsobem, jak to zaokrouhlit, je přidat řetězec dotazu s parametrem verze na odkaz. Například:
    >
    >```html
    >   <link rel="stylesheet" href="~/css/hotels.css?v1.1" />
    >```
    >
    > Pokud si myslíte, že váš prohlížeč používá starý soubor CSS, aktualizujte číslo verze.

1. Přidejte vlastnost **hodnocení** do parametru **Select** v metodě **index (SearchData model)** tak, aby výsledky zahrnovaly následující tři pole:

    ```cs
    options.Select.Add("HotelName");
    options.Select.Add("Description");
    options.Select.Add("Rating");
    ```

1. Otevřete zobrazení (index. cshtml) a nahraďte smyčku vykreslování (**&lt; !--Zobrazit data hotelu.-- &gt;**) pomocí následujícího kódu.

    ```cs
    <!-- Show the hotel data. -->
    @for (var i = 0; i < result.Count; i++)
    {
        var ratingText = $"Rating: {result[i].Document.Rating}";

        // Display the hotel details.
        @Html.TextArea($"name{i}", result[i].Document.HotelName, new { @class = "box1A" })
        @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
        @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
    }
    ```

1. Hodnocení musí být k dispozici na první zobrazené stránce a na dalších stránkách, které jsou volány pomocí nekonečné posouvání. Pro druhé z těchto dvou případů musíme aktualizovat **Další** akci v kontroleru a v zobrazení se zobrazí **posouvající** funkce. Od kontroleru změňte **Další** metodu na následující kód. Tento kód vytvoří a komunikuje text hodnocení.

    ```cs
    public async Task<ActionResult> Next(SearchData model)
    {
        // Set the next page setting, and call the Index(model) action.
        model.paging = "next";
        await Index(model);

        // Create an empty list.
        var nextHotels = new List<string>();

        // Add a hotel details to the list.
        await foreach (var result in model.resultList.GetResultsAsync())
        {
            var ratingText = $"Rating: {result.Document.Rating}";
            var rateText = $"Rates from ${result.Document.cheapest} to ${result.Document.expensive}";
    
            string fullDescription = result.Document.Description;
    
            // Add strings to the list.
            nextHotels.Add(result.Document.HotelName);
            nextHotels.Add(ratingText);
            nextHotels.Add(fullDescription);
        }

        // Rather than return a view, return the list of data.
        return new JsonResult(nextHotels);
    }
    ```

1. Teď aktualizujte **posunutou** funkci v zobrazení, aby se zobrazil text hodnocení.

    ```javascript
    <script>
        function scrolled() {
            if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                $.getJSON("/Home/Next", function (data) {
                    var div = document.getElementById('myDiv');

                    // Append the returned data to the current list of hotels.
                    for (var i = 0; i < data.length; i += 3) {
                        div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box3">' + data[i + 2] + '</textarea>';
                    }
                });
            }
        }
    </script>
    ```

1. Nyní spusťte aplikaci znovu. Vyhledejte všechny běžné podmínky, například "WiFi", a ověřte, že jsou výsledky seřazené podle sestupného pořadí podle hodnocení hotelu.

    ![Řazení podle hodnocení](./media/tutorial-csharp-create-first-app/azure-search-orders-rating.png)

    Všimnete si, že několik hotelů má stejné hodnocení, takže jejich vzhled v zobrazení je znovu pořadí, ve kterém jsou data nalezena, což je libovolné.

    Než se podíváme na přidání druhé úrovně řazení, přidáme kód, který zobrazí rozsah sazeb za místnost. Tento kód přidáváme do obou jak zobrazit extrakci dat ze _komplexního typu_, a proto můžeme diskutovat o výsledcích řazení na základě ceny (nejlevnější je třeba).

### <a name="add-the-range-of-room-rates-to-the-view"></a>Přidat rozsah místnostních sazeb do zobrazení

1. Přidejte do modelu hotelu. cs vlastnosti obsahující nejlevnější a nejdražší pokojovou sazbu.

    ```cs
    // Room rate range
    public double cheapest { get; set; }
    public double expensive { get; set; }
    ```

1. V domovském kontroleru Vypočítejte sazby za místnost na konci akce **index (SearchData model)** . Po uložení dočasných dat přidejte výpočty.

    ```cs
    // Ensure TempData is stored for the next call.
    TempData["page"] = page;
    TempData["searchfor"] = model.searchText;

    // Calculate the room rate ranges.
    await foreach (var result in model.resultList.GetResultsAsync())
    {
        var cheapest = 0d;
        var expensive = 0d;

        foreach (var room in result.Document.Rooms)
        {
            var rate = room.BaseRate;
            if (rate < cheapest || cheapest == 0)
            {
                cheapest = (double)rate;
            }
            if (rate > expensive)
            {
                expensive = (double)rate;
            }
        }
        model.resultList.Results[n].Document.cheapest = cheapest;
        model.resultList.Results[n].Document.expensive = expensive;
    }
    ```

1. Přidejte vlastnost **místnosti** do parametru **Select** v metodě **index (SearchData model)** akce kontroleru.

    ```cs
    options.Select.Add("Rooms");
    ```

1. Změňte cyklus vykreslování v zobrazení tak, aby se zobrazil rozsah přenosů první stránky výsledků.

    ```cs
    <!-- Show the hotel data. -->
    @for (var i = 0; i < result.Count; i++)
    {
        var rateText = $"Rates from ${result[i].Document.cheapest} to ${result[i].Document.expensive}";
        var ratingText = $"Rating: {result[i].Document.Rating}";

        string fullDescription = result[i].Document.Description;

        // Display the hotel details.
        @Html.TextArea($"name{i}", result[i].Document.HotelName, new { @class = "box1A" })
        @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
        @Html.TextArea($"rates{i}", rateText, new { @class = "box2A" })
        @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
    }
    ```

1. Změňte **Další** metodu v domovském řadiči, aby komunikovala rozsah přenosů pro následné stránky výsledků.

    ```cs
    public async Task<ActionResult> Next(SearchData model)
    {
        // Set the next page setting, and call the Index(model) action.
        model.paging = "next";
        await Index(model);

        // Create an empty list.
        var nextHotels = new List<string>();

        // Add a hotel details to the list.
        await foreach (var result in model.resultList.GetResultsAsync())
        {
            var ratingText = $"Rating: {result.Document.Rating}";
            var rateText = $"Rates from ${result.Document.cheapest} to ${result.Document.expensive}";

            string fullDescription = result.Document.Description;

            // Add strings to the list.
            nextHotels.Add(result.Document.HotelName);
            nextHotels.Add(ratingText);
            nextHotels.Add(rateText);
            nextHotels.Add(fullDescription);
        }

        // Rather than return a view, return the list of data.
        return new JsonResult(nextHotels);
    }
    ```

1. Umožňuje aktualizovat **posunutou** funkci v zobrazení, aby se zpracoval text sazeb místností.

    ```javascript
    <script>
        function scrolled() {
            if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                $.getJSON("/Home/Next", function (data) {
                    var div = document.getElementById('myDiv');

                    // Append the returned data to the current list of hotels.
                    for (var i = 0; i < data.length; i += 4) {
                        div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                    }
                });
            }
        }
    </script>
    ```

1. Spusťte aplikaci a ověřte, že se zobrazují rozsahy kurzů místností.

    ![Zobrazení rozsahů kurzů místností](./media/tutorial-csharp-create-first-app/azure-search-orders-rooms.png)

Vlastnost **OrderBy** parametrů hledání nepřijímá položku, jako je například **místnosts. BaseRate** , aby poskytovala tempo nejlevnější místností, a to i v případě, že místnosti již byly seřazené podle sazeb. V tomto případě nejsou místnosti seřazené podle sazeb. Aby bylo možné zobrazit hotely v ukázkové sadě dat seřazené podle sazby za místnost, je třeba seřadit výsledky v rámci svého domovského kontroleru a odeslat tyto výsledky do zobrazení v požadovaném pořadí.

## <a name="order-results-based-on-multiple-values"></a>Seřazení výsledků na základě více hodnot

Otázka teď rozlišuje mezi hotely a stejným hodnocením. Jedním z přístupů může být sekundární řazení založené na posledním okamžiku, kdy byl Hotel renovated, aby se ve výsledcích zobrazovaly rychlejší renovated hotely.

1. Chcete-li přidat druhou úroveň řazení, přidejte **části lastrenovationdate** do výsledků hledání a **OrderBy** v metodě **index (SearchData model)** .

    ```cs
    options.Select.Add("LastRenovationDate");

    options.OrderBy.Add("LastRenovationDate desc");
    ```

    >[!Tip]
    > V seznamu **OrderBy** lze zadat libovolný počet vlastností. Pokud má hotely stejné datum hodnocení a renovace, bylo možné zadat třetí vlastnost, která rozlišuje mezi nimi.

1. V zobrazení musíme znovu zobrazit datum renovace, ale stačí, abyste si vyjisti, že je řazení správné. Pro takové věci, jako je například renovace, je pravděpodobné, že stačí jenom rok. Změňte smyčku vykreslování v zobrazení na následující kód.

    ```cs
    <!-- Show the hotel data. -->
    @for (var i = 0; i < result.Count; i++)
    {
        var rateText = $"Rates from ${result[i].Document.cheapest} to ${result[i].Document.expensive}";
        var lastRenovatedText = $"Last renovated: { result[i].Document.LastRenovationDate.Value.Year}";
        var ratingText = $"Rating: {result[i].Document.Rating}";

        string fullDescription = result[i].Document.Description;

        // Display the hotel details.
        @Html.TextArea($"name{i}", result[i].Document.HotelName, new { @class = "box1A" })
        @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
        @Html.TextArea($"rates{i}", rateText, new { @class = "box2A" })
        @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
        @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
    }
    ```

1. Změňte **Další** metodu v rámci domovského kontroleru, aby se přeložila součást roku poslední datum renovace.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            await foreach (var result in model.resultList.GetResultsAsync())
            {
                var ratingText = $"Rating: {result.Document.Rating}";
                var rateText = $"Rates from ${result.Document.cheapest} to ${result.Document.expensive}";
                var lastRenovatedText = $"Last renovated: {result.Document.LastRenovationDate.Value.Year}";

                string fullDescription = result.Document.Description;

                // Add strings to the list.
                nextHotels.Add(result.Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(lastRenovatedText);
                nextHotels.Add(fullDescription);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

1. Změnou **posunuté** funkce v zobrazení zobrazíte text renovace.

    ```javascript
    <script>
        function scrolled() {
            if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                $.getJSON("/Home/Next", function (data) {
                    var div = document.getElementById('myDiv');

                    // Append the returned data to the current list of hotels.
                    for (var i = 0; i < data.length; i += 5) {
                        div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box1B">' + data[i + 1] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box2B">' + data[i + 3] + '</textarea>';
                        div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                    }
                });
            }
        }
    </script>
    ```

1. Spusťte aplikaci. Vyhledejte běžný termín, například "fond" nebo "zobrazení", a ověřte, že se nyní zobrazují hotely se stejným hodnocením v sestupném pořadí podle data renovace.

    ![Řazení pro datum renovace](./media/tutorial-csharp-create-first-app/azure-search-orders-renovation.png)

## <a name="order-results-based-on-a-scoring-profile"></a>Seřazení výsledků na základě profilu vyhodnocování

V příkladech uvedených v tomto kurzu se dozvíte, jak seřadit číselné hodnoty (datum hodnocení a renovace) a poskytnout _přesné_ pořadí řazení. Některá hledání a některá data ale neposkytují jednoduché porovnání mezi dvěma datovými prvky. Pro fulltextové vyhledávací dotazy Kognitivní hledání zahrnuje koncept _hodnocení_. Je možné určit _profily vyhodnocování_ , které mají vliv na řazení výsledků, což vám umožní složitější a kvalitativní porovnání.

[Profily vyhodnocování](index-add-scoring-profiles.md) jsou definovány ve schématu indexu. V datech hotelů bylo nastaveno několik profilů vyhodnocování. Pojďme se podívat, jak je definovaný profil vyhodnocování, a pak zkuste napsat kód, který je bude hledat.

### <a name="how-scoring-profiles-are-defined"></a>Jak jsou definovány profily vyhodnocování

Profily vyhodnocování se definují v indexu vyhledávání v době návrhu. Index hotelů jen pro čtení, který hostuje Microsoft, má tři profily bodování. V této části se prozkoumá profily vyhodnocování a dozvíte se, jak používat kód v kódu.

1. Níže je uvedený výchozí profil hodnocení pro sadu dat hotelů, který se používá, když nezadáte parametr **OrderBy** nebo **ScoringProfile** . Tento profil zvyšuje _skóre_ pro Hotel, pokud se hledaný text nachází v názvu hotelu, v popisu nebo v seznamu značek (rekreační). Všimněte si, jak váhy bodování upřednostní určitá pole. Pokud se hledaný text objeví v jiném poli, které není uvedené níže, bude mít váhu 1. Čím vyšší je skóre, tím vyšší výsledek se zobrazí v zobrazení.

     ```cs
    {
        "name": "boostByField",
        "text": {
            "weights": {
                "Tags": 3,
                "HotelName": 2,
                "Description": 1.5,
                "Description_fr": 1.5,
            }
        }
    }
    ```

1. Následující alternativní profil vyhodnocování zvyšuje skóre významně, pokud zadaný parametr obsahuje jeden nebo více seznamů značek (které zavoláme "" "). Klíčovým bodem tohoto profilu je, že je _nutné_ zadat parametr, který obsahuje text. Pokud je parametr prázdný nebo není zadán, bude vyvolána chyba.

    ```cs
    {
        "name":"boostAmenities",
        "functions":[
            {
            "fieldName":"Tags",
            "freshness":null,
            "interpolation":"linear",
            "magnitude":null,
            "distance":null,
            "tag":{
                "tagsParameter":"amenities"
            },
            "type":"tag",
            "boost":5
            }
        ],
        "functionAggregation":0
    },
    ```

1. V tomto třetím profilu poskytuje hodnocení Hotel významné zvýšení skóre. Datum poslední renovated také zvýší skóre, ale pouze v případě, že tato data spadají do 730 dnů (2 let) aktuálního data.

    ```cs
    {
        "name":"renovatedAndHighlyRated",
        "functions":[
            {
            "fieldName":"Rating",
            "freshness":null,
            "interpolation":"linear",
            "magnitude":{
                "boostingRangeStart":0,
                "boostingRangeEnd":5,
                "constantBoostBeyondRange":false
            },
            "distance":null,
            "tag":null,
            "type":"magnitude",
            "boost":20
            },
            {
            "fieldName":"LastRenovationDate",
            "freshness":{
                "boostingDuration":"P730D"
            },
            "interpolation":"quadratic",
            "magnitude":null,
            "distance":null,
            "tag":null,
            "type":"freshness",
            "boost":10
            }
        ],
        "functionAggregation":0
    }
    ```

    Teď se podíváme, jestli tyto profily fungují, jak by se měly považovat za vhodné.

### <a name="add-code-to-the-view-to-compare-profiles"></a>Přidání kódu do zobrazení pro porovnání profilů

1. Otevřete soubor index. cshtml a oddíl Text nahraďte &lt; &gt; následujícím kódem.

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
                        Hotels Search - Order Results
                    </h1>
                </td>
            </tr>
            <tr>
                <td></td>
                <td>
                    <!-- Display the search text box, with the search icon to the right of it. -->
                    <div class="searchBoxForm">
                        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
                    </div>

                    <div class="searchBoxForm">
                        <b>&nbsp;Order:&nbsp;</b>
                        @Html.RadioButtonFor(m => m.scoring, "Default") Default&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "RatingRenovation") By numerical Rating&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "boostAmenities") By Amenities&nbsp;&nbsp;
                        @Html.RadioButtonFor(m => m.scoring, "renovatedAndHighlyRated") By Renovated date/Rating profile&nbsp;&nbsp;
                    </div>
                </td>
            </tr>

            <tr>
                <td valign="top">
                    <div id="facetplace" class="facetchecks">

                        @if (Model != null && Model.facetText != null)
                        {
                            <h5 class="facetheader">Amenities:</h5>
                            <ul class="facetlist">
                                @for (var c = 0; c < Model.facetText.Length; c++)
                                {
                                    <li> @Html.CheckBoxFor(m => m.facetOn[c], new { @id = "check" + c.ToString() }) @Model.facetText[c] </li>
                                }

                            </ul>
                        }
                    </div>
                </td>
                <td>
                    @if (Model != null && Model.resultList != null)
                    {
                        // Show the total result count.
                        <p class="sampleText">
                            @Html.DisplayFor(m => m.resultList.Count) Results <br />
                        </p>

                        <div id="myDiv" style="width: 800px; height: 450px; overflow-y: scroll;" onscroll="scrolled()">

                            <!-- Show the hotel data. -->
                            @for (var i = 0; i < Model.resultList.Results.Count; i++)
                            {
                                var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                                var lastRenovatedText = $"Last renovated: { Model.resultList.Results[i].Document.LastRenovationDate.Value.Year}";
                                var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                                string amenities = string.Join(", ", Model.resultList.Results[i].Document.Tags);
                                string fullDescription = Model.resultList.Results[i].Document.Description;
                                fullDescription += $"\nAmenities: {amenities}";

                                // Display the hotel details.
                                @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                                @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                                @Html.TextArea($"rates{i}", rateText, new { @class = "box2A" })
                                @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
                                @Html.TextArea($"desc{i}", fullDescription, new { @class = "box3" })
                            }
                        </div>

                        <script>
                            function scrolled() {
                                if (myDiv.offsetHeight + myDiv.scrollTop >= myDiv.scrollHeight) {
                                    $.getJSON("/Home/Next", function (data) {
                                        var div = document.getElementById('myDiv');

                                        // Append the returned data to the current list of hotels.
                                        for (var i = 0; i < data.length; i += 5) {
                                            div.innerHTML += '\n<textarea class="box1A">' + data[i] + '</textarea>';
                                            div.innerHTML += '<textarea class="box1B">' + data[i + 1] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box2A">' + data[i + 2] + '</textarea>';
                                            div.innerHTML += '<textarea class="box2B">' + data[i + 3] + '</textarea>';
                                            div.innerHTML += '\n<textarea class="box3">' + data[i + 4] + '</textarea>';
                                        }
                                    });
                                }
                            }
                        </script>
                    }
                </td>
            </tr>
        </table>
    }
    </body>
    ```

1. Otevřete soubor SearchData. cs a nahraďte třídu **SearchData** následujícím kódem.

    ```cs
    public class SearchData
    {
        public SearchData()
        {
        }

        // Constructor to initialize the list of facets sent from the controller.
        public SearchData(List<string> facets)
        {
            facetText = new string[facets.Count];

            for (int i = 0; i < facets.Count; i++)
            {
                facetText[i] = facets[i];
            }
        }

        // Array to hold the text for each amenity.
        public string[] facetText { get; set; }

        // Array to hold the setting for each amenitity.
        public bool[] facetOn { get; set; }

        // The text to search for.
        public string searchText { get; set; }

        // Record if the next page is requested.
        public string paging { get; set; }

        // The list of results.
        public DocumentSearchResult<Hotel> resultList;

        public string scoring { get; set; }       
    }
    ```

1. Otevřete soubor hotely. CSS a přidejte následující třídy HTML.

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

### <a name="add-code-to-the-controller-to-specify-a-scoring-profile"></a>Přidejte do kontroleru kód pro určení profilu vyhodnocování.

1. Otevřete soubor domovského kontroleru. Přidejte následující příkaz **using** (pro pomoc s vytvářením seznamů).

    ```cs
    using System.Linq;
    ```

1. V tomto příkladu potřebujeme počáteční volání **indexu** , aby se provedlo trochu víc, než stačí vrátit počáteční zobrazení. Metoda teď vyhledá až 20 možností pro zobrazení v zobrazení.

    ```cs
        public async Task<ActionResult> Index()
        {
            InitSearch();

            // Set up the facets call in the search parameters.
            SearchOptions options = new SearchOptions();
            // Search for up to 20 amenities.
            options.Facets.Add("Tags,count:20");

            SearchResults<Hotel> searchResult = await _searchClient.SearchAsync<Hotel>("*", options);

            // Convert the results to a list that can be displayed in the client.
            List<string> facets = searchResult.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

            // Initiate a model with a list of facets for the first view.
            SearchData model = new SearchData(facets);

            // Save the facet text for the next view.
            SaveFacets(model, false);

            // Render the view including the facets.
            return View(model);
        }
    ```

1. Potřebujeme dvě privátní metody pro uložení omezujících vlastností do dočasného úložiště a jejich obnovení z dočasného úložiště a naplnění modelu.

    ```cs
        // Save the facet text to temporary storage, optionally saving the state of the check boxes.
        private void SaveFacets(SearchData model, bool saveChecks = false)
        {
            for (int i = 0; i < model.facetText.Length; i++)
            {
                TempData["facet" + i.ToString()] = model.facetText[i];
                if (saveChecks)
                {
                    TempData["faceton" + i.ToString()] = model.facetOn[i];
                }
            }
            TempData["facetcount"] = model.facetText.Length;
        }

        // Recover the facet text to a model, optionally recoving the state of the check boxes.
        private void RecoverFacets(SearchData model, bool recoverChecks = false)
        {
            // Create arrays of the appropriate length.
            model.facetText = new string[(int)TempData["facetcount"]];
            if (recoverChecks)
            {
                model.facetOn = new bool[(int)TempData["facetcount"]];
            }

            for (int i = 0; i < (int)TempData["facetcount"]; i++)
            {
                model.facetText[i] = TempData["facet" + i.ToString()].ToString();
                if (recoverChecks)
                {
                    model.facetOn[i] = (bool)TempData["faceton" + i.ToString()];
                }
            }
        }
    ```

1. V případě potřeby je potřeba nastavit parametry **OrderBy** a **ScoringProfile** . Nahraďte existující metodu **indexu (SearchData model)** následujícím způsobem.

    ```cs
    public async Task<ActionResult> Index(SearchData model)
    {
        try
        {
            InitSearch();

            int page;

            if (model.paging != null && model.paging == "next")
            {
                // Recover the facet text, and the facet check box settings.
                RecoverFacets(model, true);

                // Increment the page.
                page = (int)TempData["page"] + 1;

                // Recover the search text.
                model.searchText = TempData["searchfor"].ToString();
            }
            else
            {
                // First search with text. 
                // Recover the facet text, but ignore the check box settings, and use the current model settings.
                RecoverFacets(model, false);

                // First call. Check for valid text input, and valid scoring profile.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }
                if (model.scoring == null)
                {
                    model.scoring = "Default";
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
                IncludeTotalCount = true,
            };
            // Select the data properties to be returned.
            options.Select.Add("HotelName");
            options.Select.Add("Description");
            options.Select.Add("Tags");
            options.Select.Add("Rooms");
            options.Select.Add("Rating");
            options.Select.Add("LastRenovationDate");

            List<string> parameters = new List<string>();
            // Set the ordering based on the user's radio button selection.
            switch (model.scoring)
            {
                case "RatingRenovation":
                    // Set the ordering/scoring parameters.
                    options.OrderBy.Add("Rating desc");
                    options.OrderBy.Add("LastRenovationDate desc");
                    break;

                case "boostAmenities":
                    {
                        options.ScoringProfile = model.scoring;

                        // Create a string list of amenities that have been clicked.
                        for (int a = 0; a < model.facetOn.Length; a++)
                        {
                            if (model.facetOn[a])
                            {
                                parameters.Add(model.facetText[a]);
                            }
                        }

                        if (parameters.Count > 0)
                        {
                            options.ScoringParameters.Add($"amenities-{ string.Join(',', parameters)}");
                        }
                        else
                        {
                            // No amenities selected, so set profile back to default.
                            options.ScoringProfile = "";
                        }
                    }
                    break;

                case "renovatedAndHighlyRated":
                    options.ScoringProfile = model.scoring;
                    break;

                default:
                    break;
            }

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _searchClient.SearchAsync<Hotel>(model.searchText, options);

            // Ensure TempData is stored for the next call.
            TempData["page"] = page;
            TempData["searchfor"] = model.searchText;
            TempData["scoring"] = model.scoring;
            SaveFacets(model, true);

            // Calculate the room rate ranges.
            await foreach (var result in model.resultList.GetResultsAsync())
            {
                var cheapest = 0d;
                var expensive = 0d;

                foreach (var room in result.Document.Rooms)
                {
                    var rate = room.BaseRate;
                    if (rate < cheapest || cheapest == 0)
                    {
                        cheapest = (double)rate;
                    }
                    if (rate > expensive)
                    {
                        expensive = (double)rate;
                    }
                }

                result.Document.cheapest = cheapest;
                result.Document.expensive = expensive;
            }
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "1" });
        }

        return View("Index", model);
    }
    ```

    Přečtěte si komentáře pro jednotlivé výběry **přepínačů** .

1. Pokud jste dokončili další kód pro předchozí oddíl řazení na základě více vlastností, nemusíme dělat žádné změny **Další** akce.

### <a name="run-and-test-the-app"></a>Spuštění a otestování aplikace

1. Spusťte aplikaci. V zobrazení by se měla zobrazit celá sada rekreačních součástí.

1. Pro řazení, výběr "podle číselného hodnocení" vám poskytne číselné řazení, které jste už v tomto kurzu implementovali, s datem renovace, který se při rozhodování mezi hotely stejného hodnocení používá.

   ![Řazení "pláž" na základě hodnocení](./media/tutorial-csharp-create-first-app/azure-search-orders-beach.png)

1. Teď Vyzkoušejte profil "podle vašeho rekreačního". Provedete nejrůznější výběry a ověřte, že Hotely s těmito ubytováními jsou povýšeny do seznamu výsledků.

   ![Řazení "pláž" na základě profilu](./media/tutorial-csharp-create-first-app/azure-search-orders-beach-profile.png)

1. Vyzkoušejte si profil "podle renovated data/hodnocení", abyste viděli, jestli se vám neočekáváte. Pouze nedávno renovated hotely by měli zvýšit zvýšení _aktuálnosti_ .

### <a name="resources"></a>Zdroje informací

Další informace najdete v následujících tématech [přidání profilů vyhodnocování do indexu služby Azure kognitivní hledání](./index-add-scoring-profiles.md).

## <a name="takeaways"></a>Shrnutí

Vezměte v úvahu následující poznatky z tohoto projektu:

* Uživatelé budou očekávat, že výsledky hledání budou seřazené, nejdůležitější jako první.
* Data musí být strukturovaná, aby bylo řazení snadné. Nedokázali jsme nejdřív seřadit "nejlevnější", protože data nejsou strukturovaná, aby bylo možné provádět řazení bez dalšího kódu.
* Pro objednání může být k dispozici mnoho úrovní, aby bylo možné odlišit výsledky, které mají stejnou hodnotu na vyšší úrovni řazení.
* U některých výsledků je přirozené řazení ve vzestupném pořadí (například vzdálenost od bodu) a některé v sestupném pořadí (například hodnocení hosta).
* Profily vyhodnocování lze definovat, pokud nejsou k dispozici numerická porovnání nebo nejsou pro datovou sadu dostatečně inteligentní. Bodování každého výsledku vám pomůže s inteligentním seřazením a zobrazením výsledků.

## <a name="next-steps"></a>Další kroky

Dokončili jste tuto sérii kurzů pro C# – měli byste mít k dispozici užitečné znalosti rozhraní API Azure Kognitivní hledání.

Další referenční materiály a kurzy najdete v části procházení [Microsoft Learn](/learn/browse/?products=azure)nebo v dalších kurzech v [dokumentaci k Azure kognitivní hledání](./index.yml).