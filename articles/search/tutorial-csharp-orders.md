---
title: C# návod na objednávání výsledků
titleSuffix: Azure Cognitive Search
description: Tento kurz ukazuje, jak objednat výsledky hledání. Staví na předchozím projektu hotelů, objednávání podle primárního majetku, sekundární vlastnosti a obsahuje profil hodnocení pro přidání kritérií pro zvýšení.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: 812085a5a4b3e8d1233f19c947d2fd5e433f6ab7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77121547"
---
# <a name="c-tutorial-order-the-results---azure-cognitive-search"></a>Kurz C#: Seruje výsledky – Azure Cognitive Search

Až do tohoto okamžiku v naší sérii výukových programů jsou výsledky vráceny a zobrazeny ve výchozím pořadí. Může se stát pořadí, ve kterém jsou data umístěna, nebo případně byl definován výchozí _profil hodnocení,_ který bude použit, pokud nejsou zadány žádné parametry řazení. V tomto kurzu se podíváme na to, jak objednat výsledky na základě primární vlastnosti a potom pro výsledky, které mají stejnou primární vlastnost, jak objednat tento výběr na sekundární vlastnosti. Jako alternativu k řazení na základě číselných hodnot, poslední příklad ukazuje, jak pořadí na základě vlastního profilu hodnocení. Půjdeme také trochu hlouběji do zobrazení _složitých typů_.

Chcete-li snadno porovnat vrácené výsledky, tento projekt navazuje na nekonečný projekt posouvání vytvořený v [kurzu C#: Stránkování výsledků hledání – kurz azure kognitivního vyhledávání.](tutorial-csharp-paging.md)

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Výsledky objednávky na základě jedné vlastnosti
> * Pořadí výsledků na základě více vlastností
> * Filtrování výsledků na základě vzdálenosti od zeměpisného bodu
> * Pořadí výsledků na základě profilu hodnocení

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

Mít nekonečné posouvání verze [C# Tutorial: Výsledky hledání stránkování – Azure Cognitive Search](tutorial-csharp-paging.md) projektu v provozu. Tento projekt může být buď vaše vlastní verze, nebo jej nainstalovat z GitHub: [Vytvořit první aplikaci](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="order-results-based-on-one-property"></a>Výsledky objednávky na základě jedné vlastnosti

Když objednáváme výsledky na základě jedné nemovitosti, řekněme hodnocení hotelu, chceme nejen objednané výsledky, ale také potvrzení, že objednávka je správná. Jinými slovy, pokud objednáme hodnocení, měli bychom hodnocení zobrazit v zobrazení.

V tomto tutoriálu také přidáme trochu více k zobrazení výsledků, nejlevnější ceny pokoje a nejdražší ceny pokoje pro každý hotel. Jak jsme se ponořit do objednávání, budeme také přidávat hodnoty, aby se ujistil, co jsme objednávání na je také zobrazen v zobrazení.

Není nutné upravovat žádný z modelů povolit řazení. Zobrazení a řadič je třeba aktualizovat. Začněte otevřením domácího ovladače.

### <a name="add-the-orderby-property-to-the-search-parameters"></a>Přidání vlastnosti OrderBy do parametrů vyhledávání

1. Vše, co je potřeba k pořadí výsledků na základě jedné číselné vlastnosti, je nastavit **OrderBy** parametr na název vlastnosti. V metodě **Index(Model SearchData)** přidejte následující řádek do parametrů hledání.

    ```cs
        OrderBy = new[] { "Rating desc" },
    ```

    >[!Note]
    > Výchozí pořadí je vzestupně, i když můžete přidat **asc** do vlastnosti, aby to bylo jasné. Sestupné pořadí je určeno přidáním **desc**.

2. Nyní spusťte aplikaci a zadejte jakýkoli běžný hledaný výraz. Výsledky mohou nebo nemusí být ve správném pořadí, protože ani vy jako vývojář, ne uživatel, nemá snadný způsob, jak ověřit výsledky!

3. Ujasněme si, že výsledky jsou seřazeny podle hodnocení. Nejprve nahraďte třídy **box1** a **box2** v souboru hotels.css následujícími třídami (tyto třídy jsou všechny nové, které potřebujeme pro tento kurz).

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

    >[!Tip]
    >Prohlížeče obvykle mezipaměti css soubory, a to může vést k staré css soubor používá, a vaše úpravy ignorovány. Dobrým způsobem, jak to toto zaokrouhlit, je přidat řetězec dotazu s parametrem verze k odkazu. Například:
    >
    >```html
    >   <link rel="stylesheet" href="~/css/hotels.css?v1.1" />
    >```
    >
    >Aktualizujte číslo verze, pokud si myslíte, že váš prohlížeč používá starý soubor css.

4. Přidejte vlastnost **Hodnocení** do parametru **Select** v metodě **Index(Model SearchData).**

    ```cs
    Select = new[] { "HotelName", "Description", "Rating"},
    ```

5. Otevřete zobrazení (index.cshtml) a nahraďte vykreslovací smyčku**&lt;(!-- Zobrazit data hotelu. --&gt;**) následujícím kódem.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

6. Hodnocení musí být k dispozici jak na první zobrazené stránce, tak na následujících stránkách, které jsou volány prostřednictvím nekonečného posouvání. Pro druhé z těchto dvou situací, musíme aktualizovat další **akce** v řadiči a **rolované** funkce v zobrazení. Počínaje řadičem změňte metodu **Next** na následující kód. Tento kód vytvoří a sdělí text hodnocení.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";

                // Add three strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

7. Nyní aktualizujte **rolovací** funkci v zobrazení, abyste zobrazili text hodnocení.

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

8. Nyní spusťte aplikaci znovu. Vyhledejte libovolný běžný termín, například "wifi", a ověřte, zda jsou výsledky seřazeny podle sestupného pořadí hodnocení hotelu.

    ![Objednávání na základě hodnocení](./media/tutorial-csharp-create-first-app/azure-search-orders-rating.png)

    Všimnete si, že několik hotelů má stejné hodnocení, a tak jejich vzhled na displeji je opět pořadí, ve kterém jsou data nalezena, což je libovolné.

    Než se podíváme na přidání druhé úrovně objednávání, přidáme nějaký kód pro zobrazení rozsahu sazeb za pokoj. Přidáváme tento kód jak zobrazit extrahování dat z _komplexního typu_, a také tak můžeme diskutovat o objednávání výsledky na základě ceny (nejlevnější první možná).

### <a name="add-the-range-of-room-rates-to-the-view"></a>Přidání nabídky sazeb pokojů do zobrazení

1. Přidejte vlastnosti obsahující nejlevnější a nejdražší cenu pokoje do modelu Hotel.cs.

    ```cs
        // Room rate range
        public double cheapest { get; set; }
        public double expensive { get; set; }
    ```

2. Vypočítejte sazby za pokoj na konci akce **Index (Model SearchData)** v domácím řadiči. Přidejte výpočty po uložení dočasných dat.

    ```cs
                // Ensure TempData is stored for the next call.
                TempData["page"] = page;
                TempData["searchfor"] = model.searchText;

                // Calculate the room rate ranges.
                for (int n = 0; n < model.resultList.Results.Count; n++)
                {
                    // Calculate room rates.
                    var cheapest = 0d;
                    var expensive = 0d;

                    for (var r = 0; r < model.resultList.Results[n].Document.Rooms.Length; r++)
                    {
                        var rate = model.resultList.Results[n].Document.Rooms[r].BaseRate;
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

3. Přidejte vlastnost **Rooms** do metody akce **Select** **(Model SearchData)** řadiče.

    ```cs
     Select = new[] { "HotelName", "Description", "Rating", "Rooms" },
    ```

4. Změňte smyčku vykreslování v zobrazení tak, aby se zobrazil rozsah rychlosti pro první stránku výsledků.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"rates{i}" , rateText, new { @class = "box2A" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

5. Změňte **další** metodu v domácím řadiči pro komunikaci rozsahu rychlosti pro následující stránky výsledků.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";
                var rateText = $"Rates from ${model.resultList.Results[n].Document.cheapest} to ${model.resultList.Results[n].Document.expensive}";

                // Add strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

6. Aktualizujte **rolovací** funkci v zobrazení, abyste zpracovat text sazeb místnosti.

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

7. Spusťte aplikaci a ověřte, zda se zobrazí rozsahy sazeb pokojů.

    ![Zobrazení rozsahů sazeb pokojů](./media/tutorial-csharp-create-first-app/azure-search-orders-rooms.png)

Vlastnost **OrderBy** parametrů vyhledávání nepřijme položku, jako je **Rooms.BaseRate,** aby poskytla nejlevnější cenu pokoje, i když pokoje byly již seřazeny podle sazby. V tomto případě nejsou pokoje seřazeny podle sazby. Chcete-li zobrazit hotely v sadě ukázkových dat seřazené podle ceny pokoje, budete muset výsledky seřadit v domovském řadiči a odeslat tyto výsledky do zobrazení v požadovaném pořadí.

## <a name="order-results-based-on-multiple-values"></a>Pořadí výsledků na základě více hodnot

Otázkou nyní je, jak rozlišovat mezi hotely se stejným hodnocením. Jedním z dobrých způsobů, jak by bylo objednat na základě posledního zrekonstruována hotel. Jinými slovy, čím nedávno byl hotel zrekonstruován, tím vyšší je hotel ve výsledcích.

1. Chcete-li přidat druhou úroveň řazení, změňte **OrderBy** a **Select** vlastnosti v **Index (SearchData model)** metoda zahrnout **LastRenovationDate** vlastnost.

    ```cs
    OrderBy = new[] { "Rating desc", "LastRenovationDate desc" },
    Select = new[] { "HotelName", "Description", "Rating", "Rooms", "LastRenovationDate" },
    ```

    >[!Tip]
    >Libovolný počet vlastností lze zadat do seznamu **OrderBy.** Pokud by hotely měly stejné hodnocení a datum renovace, mohla by být zadána třetí nemovitost, která by mezi nimi rozlišovala.

2. Opět musíme vidět datum renovace v pohledu, jen abychom si byli jisti, že objednávka je správná. Pro takovou věc, jako je renovace, je pravděpodobně zapotřebí jen rok. Změňte vykreslování smyčky v zobrazení na následující kód.

    ```cs
                <!-- Show the hotel data. -->
                @for (var i = 0; i < Model.resultList.Results.Count; i++)
                {
                    var rateText = $"Rates from ${Model.resultList.Results[i].Document.cheapest} to ${Model.resultList.Results[i].Document.expensive}";
                    var lastRenovatedText = $"Last renovated: { Model.resultList.Results[i].Document.LastRenovationDate.Value.Year}";
                    var ratingText = $"Rating: {Model.resultList.Results[i].Document.Rating}";

                    // Display the hotel details.
                    @Html.TextArea($"name{i}", Model.resultList.Results[i].Document.HotelName, new { @class = "box1A" })
                    @Html.TextArea($"rating{i}", ratingText, new { @class = "box1B" })
                    @Html.TextArea($"rates{i}" , rateText, new { @class = "box2A" })
                    @Html.TextArea($"renovation{i}", lastRenovatedText, new { @class = "box2B" })
                    @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box3" })
                }
    ```

3. Změňte metodu **Další** v domácím řadiči, abyste přenesli roční složku posledního data renovace.

    ```cs
        public async Task<ActionResult> Next(SearchData model)
        {
            // Set the next page setting, and call the Index(model) action.
            model.paging = "next";
            await Index(model);

            // Create an empty list.
            var nextHotels = new List<string>();

            // Add a hotel details to the list.
            for (int n = 0; n < model.resultList.Results.Count; n++)
            {
                var ratingText = $"Rating: {model.resultList.Results[n].Document.Rating}";
                var rateText = $"Rates from ${model.resultList.Results[n].Document.cheapest} to ${model.resultList.Results[n].Document.expensive}";
                var lastRenovatedText = $"Last renovated: {model.resultList.Results[n].Document.LastRenovationDate.Value.Year}";

                // Add strings to the list.
                nextHotels.Add(model.resultList.Results[n].Document.HotelName);
                nextHotels.Add(ratingText);
                nextHotels.Add(rateText);
                nextHotels.Add(lastRenovatedText);
                nextHotels.Add(model.resultList.Results[n].Document.Description);
            }

            // Rather than return a view, return the list of data.
            return new JsonResult(nextHotels);
        }
    ```

4. Změňte **rolovací** funkci v zobrazení tak, aby zobrazovala text renovace.

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

5. Spusťte aplikaci. Hledejte běžný termín, například "bazén" nebo "zobrazit", a ověřte, zda jsou hotely se stejným hodnocením nyní zobrazeny v sestupném pořadí podle data renovace.

    ![Objednání v den renovace](./media/tutorial-csharp-create-first-app/azure-search-orders-renovation.png)

## <a name="filter-results-based-on-a-distance-from-a-geographical-point"></a>Filtrování výsledků na základě vzdálenosti od zeměpisného bodu

Hodnocení a datum renovace jsou příklady vlastností, které jsou nejlépe zobrazeny v sestupném pořadí. Abecední výpis by byl příkladem dobrého použití vzestupného pořadí (například pokud existovala pouze jedna vlastnost **OrderBy** a byla nastavena na **HotelName,** pak by se zobrazilo abecední pořadí). Pro naše vzorové údaje by však byla vhodnější vzdálenost od zeměpisného bodu.

Chcete-li zobrazit výsledky na základě zeměpisné vzdálenosti, je vyžadováno několik kroků.

1. Odfiltrujte všechny hotely, které jsou mimo zadaný poloměr od daného bodu, zadáním filtru s parametry zeměpisné délky, zeměpisné šířky a poloměru. Zeměpisná propustka je dána nejprve funkci POINT. Poloměr je v kilometrech.

    ```cs
        // "Location" must match the field name in the Hotel class.
        // Distance (the radius) is in kilometers.
        // Point order is Longitude then Latitude.
        Filter = $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') le {model.radius}",
    ```

2. Výše uvedený _not_ filtr nesesokuje výsledky na základě vzdálenosti, jen odstraní odlehlé hodnoty. Chcete-li sestavovat výsledky, zadejte **OrderBy** nastavení, které určuje metodu geoDistance.

    ```cs
    OrderBy = new[] { $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') asc" },
    ```

3. Přestože výsledky byly vráceny Azure Cognitive Search pomocí filtru vzdálenosti, vypočtená vzdálenost mezi daty a zadaný bod _není_ vrácena. Přepočítejte tuto hodnotu v zobrazení nebo kontroleru, pokud ji chcete zobrazit ve výsledcích.

    Následující kód vypočítá vzdálenost mezi dvěma lat/lon body.

    ```cs
        const double EarthRadius = 6371;

        public static double Degrees2Radians(double deg)
        {
            return deg * Math.PI / 180;
        }

        public static double DistanceInKm( double lat1,  double lon1, double lat2, double lon2)
        {
            double dlon = Degrees2Radians(lon2 - lon1);
            double dlat = Degrees2Radians(lat2 - lat1);

            double a = (Math.Sin(dlat / 2) * Math.Sin(dlat / 2)) + Math.Cos(Degrees2Radians(lat1)) * Math.Cos(Degrees2Radians(lat2)) * (Math.Sin(dlon / 2) * Math.Sin(dlon / 2));
            double angle = 2 * Math.Atan2(Math.Sqrt(a), Math.Sqrt(1 - a));
            return angle * EarthRadius;
        }
    ```

4. Nyní musíte tyto pojmy spojit dohromady. Tyto fragmenty kódu jsou však tak daleko, jak náš kurz jde, budování aplikace založené na mapě je ponecháno jako cvičení pro čtenáře. Chcete-li tento příklad vzít dále, zvažte zadání názvu města s poloměrem nebo umístění bodu na mapě a výběr poloměru. Chcete-li tyto možnosti dále prozkoumat, podívejte se na následující zdroje:

* [Dokumentace k Azure Maps](https://docs.microsoft.com/azure/azure-maps/)
* [Vyhledání adresy pomocí vyhledávací služby Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)

## <a name="order-results-based-on-a-scoring-profile"></a>Pořadí výsledků na základě profilu hodnocení

Příklady uvedené v tutoriálu zatím ukazují, jak objednat na číselné hodnoty (hodnocení, datum renovace, zeměpisná vzdálenost), poskytující _přesný_ proces objednávání. Některá vyhledávání a některá data však nepropůjčují takové snadné porovnání mezi dvěma datovými prvky. Azure Cognitive Search zahrnuje koncept _bodování_. _Profily hodnocení_ lze zadat pro sadu dat, která lze použít k poskytování složitějších a kvalitativních porovnání, která by měla být nejcennější, když například porovnáte textová data, aby se rozhodlo, které by měly být zobrazeny jako první.

Profily hodnocení nejsou definovány uživateli, ale obvykle správci sady dat. Na datech hotelů bylo nastaveno několik profilů bodování. Podívejme se na to, jak je definován profil hodnocení, a pak zkuste napsat kód pro vyhledávání na nich.

### <a name="how-scoring-profiles-are-defined"></a>Jak jsou definovány profily hodnocení

Podívejme se na tři příklady profilů hodnocení a zvažte, jak _by měl_ každý z nich ovlivnit pořadí výsledků. Jako vývojář aplikace nepíšete tyto profily, jsou napsány správcem dat, je však užitečné podívat se na syntaxi.

1. Toto je výchozí profil hodnocení pro sadu dat hotelů, který se používá, pokud nezadáte žádný parametr **OrderBy** nebo **ScoringProfile.** Tento profil zvyšuje _skóre_ hotelu, pokud je hledaný text v názvu hotelu, popisu nebo seznamu značek (vybavení). Všimněte si, jak váhy bodování upřednostňují určitá pole. Pokud se hledaný text zobrazí v jiném poli, které není uvedeno níže, bude mít váhu 1. Je zřejmé, že čím vyšší je skóre, tím dříve se výsledek zobrazí v zobrazení.

     ```cs
    {
            "name": "boostByField",
            "text": {
                "weights": {
                    "HotelName": 2,
                    "Description": 1.5,
                    "Description_fr": 1.5,
                    "Tags": 3
                }
            }
        }

    ```

2. Následující bodovací profil výrazně zvyšuje skóre, pokud dodaný parametr obsahuje jeden nebo více ze seznamu značek (které nazýváme "vybavení"). Klíčovým bodem tohoto profilu je, že _musí_ být zadán parametr obsahující text. Pokud je parametr prázdný nebo není zadán, bude vyvolána chyba.
 
    ```cs
            {
            "name": "boostAmenities",
            "functions": [
                {
                    "type": "tag",
                    "fieldName": "Tags",
                    "boost": 5,
                    "tag": {
                        "tagsParameter": "amenities"
                    }
                }
            ]
        }
    ```

3. V tomto třetím příkladu hodnocení výrazně zvyšuje skóre. Poslední renovované datum také zvýší skóre, ale pouze v případě, že tyto údaje spadají do 730 dnů (2 let) od aktuálního data.

    ```cs
            {
            "name": "renovatedAndHighlyRated",
            "functions": [
                {
                    "type": "magnitude",
                    "fieldName": "Rating",
                    "boost": 20,
                    "interpolation": "linear",
                    "magnitude": {
                        "boostingRangeStart": 0,
                        "boostingRangeEnd": 5,
                        "constantBoostBeyondRange": false
                    }
                },
                {
                    "type": "freshness",
                    "fieldName": "LastRenovationDate",
                    "boost": 10,
                    "interpolation": "quadratic",
                    "freshness": {
                        "boostingDuration": "P730D"
                    }
                }
            ]
        }

    ```

    Nyní uvidíme, jestli tyto profily fungují tak, jak si myslíme, že by měly!

### <a name="add-code-to-the-view-to-compare-profiles"></a>Přidání kódu do zobrazení pro porovnání profilů

1. Otevřete soubor index.cshtml a &lt;&gt; nahraďte část textu následujícím kódem.

    ```cs
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

2. Otevřete soubor SearchData.cs a nahraďte třídu **SearchData** následujícím kódem.

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

3. Otevřete soubor hotels.css a přidejte následující třídy HTML.

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

### <a name="add-code-to-the-controller-to-specify-a-scoring-profile"></a>Přidání kódu do kontrolora pro určení profilu hodnocení

1. Otevřete soubor domovského řadiče. Přidejte následující **příkaz using** (pro pomoc při vytváření seznamů).

    ```cs
    using System.Linq;
    ```

2.  V tomto příkladu potřebujeme počáteční volání **index** udělat trochu víc než jen vrátit počáteční zobrazení. Metoda nyní vyhledá až 20 vybavení, které se zobrazí v zobrazení.

    ```cs
        public async Task<ActionResult> Index()
        {
            InitSearch();

            // Set up the facets call in the search parameters.
            SearchParameters sp = new SearchParameters()
            {
                // Search for up to 20 amenities.
                Facets = new List<string> { "Tags,count:20" },
            };

            DocumentSearchResult<Hotel> searchResult = await _indexClient.Documents.SearchAsync<Hotel>("*", sp);

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

3. Potřebujeme dvě soukromé metody pro uložení omezujících okolností do dočasného úložiště a jejich obnovení z dočasného úložiště a naplnění modelu.

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

4. Musíme nastavit **OrderBy** a **ScoringProfile** parametry podle potřeby. Nahraďte existující metodu **Index(Model SearchData)** následujícím způsobem.

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
                    RecoverFacets(model,false);

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

                // Set empty defaults for ordering and scoring parameters.
                var orderby = new List<string>();
                string profile = "";
                var scoringParams = new List<ScoringParameter>();

                // Set the ordering based on the user's radio button selection.
                switch (model.scoring)
                {
                    case "RatingRenovation":
                        orderby.Add("Rating desc");
                        orderby.Add("LastRenovationDate desc");
                        break;

                    case "boostAmenities":
                        {
                            profile = model.scoring;
                            var setAmenities = new List<string>();

                            // Create a string list of amenities that have been clicked.
                            for (int a = 0; a < model.facetOn.Length; a++)
                            {
                                if (model.facetOn[a])
                                {
                                    setAmenities.Add(model.facetText[a]);
                                }
                            }
                            if (setAmenities.Count > 0)
                            {
                                // Only set scoring parameters if there are any.
                                var sp = new ScoringParameter("amenities", setAmenities);
                                scoringParams.Add(sp);
                            }
                            else
                            {
                                // No amenities selected, so set profile back to default.
                                profile = "";
                            }
                        }
                        break;

                    case "renovatedAndHighlyRated":
                        profile = model.scoring;
                        break;

                    default:
                        break;
                }

                // Setup the search parameters.
                var parameters = new SearchParameters
                {
                    // Set the ordering/scoring parameters.
                    OrderBy = orderby,
                    ScoringProfile = profile,
                    ScoringParameters = scoringParams,

                    // Select the data properties to be returned.
                    Select = new[] { "HotelName", "Description", "Tags", "Rooms", "Rating", "LastRenovationDate" },
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
                TempData["scoring"] = model.scoring;
                SaveFacets(model,true);

                // Calculate the room rate ranges.
                for (int n = 0; n < model.resultList.Results.Count; n++)
                {
                    var cheapest = 0d;
                    var expensive = 0d;

                    for (var r = 0; r < model.resultList.Results[n].Document.Rooms.Length; r++)
                    {
                        var rate = model.resultList.Results[n].Document.Rooms[r].BaseRate;
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
            }
            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View("Index", model);
        }
    ```

    Přečtěte si komentáře pro každý z **přepínačů** výběrů.

5. Nepotřebujeme provádět žádné změny **další** akce, pokud jste dokončili další kód pro předchozí část o objednávání na základě více vlastností.

### <a name="run-and-test-the-app"></a>Spuštění a testování aplikace

1. Spusťte aplikaci. Měli byste vidět kompletní sadu vybavení v pohledu.

2. Pro objednání, výběr "Podle číselného hodnocení" vám číselné pořadí, které jste již implementovali v tomto tutoriálu, s datem renovace rozhodování mezi hotely stejného hodnocení.

![Objednání "pláže" na základě hodnocení](./media/tutorial-csharp-create-first-app/azure-search-orders-beach.png)

3. Nyní zkuste profil "Podle vybavení". Proveďte různé výběry vybavení a ověřte, zda jsou hotely s tímto vybavením propagovány v seznamu výsledků.

![Objednání "pláže" na základě profilu](./media/tutorial-csharp-create-first-app/azure-search-orders-beach-profile.png)

4. Zkuste "Podle renovovaného data / hodnocení profilu", abyste zjistili, zda dostanete to, co očekáváte. Teprve nedávno zrekonstruované hotely by měly získat _podporu čerstvosti._

### <a name="resources"></a>Prostředky

Další informace najdete v následujících tématech [Přidání profilů hodnocení do indexu Azure Cognitive Search](https://docs.microsoft.com/azure/search/index-add-scoring-profiles).

## <a name="takeaways"></a>Shrnutí

Vezměme si následující stánek s jídlem z tohoto projektu:

* Uživatelé budou očekávat, že výsledky vyhledávání budou objednány, nejrelevantnější jako první.
* Data potřebují strukturované tak, aby řazení bylo snadné. Nebyli jsme schopni třídit na "nejlevnější" první snadno, protože data nejsou strukturována tak, aby řazení bylo provedeno bez dalšího kódu.
* Může existovat mnoho úrovní řazení, rozlišovat mezi výsledky, které mají stejnou hodnotu na vyšší úrovni řazení.
* Je přirozené, že některé výsledky mají být objednány ve vzestupném pořadí (řekněme, vzdálenost od bodu) a některé v sestupném pořadí (řekněme hodnocení hosta).
* Profily hodnocení lze definovat, pokud číselné porovnání nejsou k dispozici nebo nejsou dostatečně inteligentní pro sadu dat. Bodování každý výsledek pomůže objednat a zobrazit výsledky inteligentně.

## <a name="next-steps"></a>Další kroky

Dokončili jste tuto řadu c# kurzy – měli jste získali cenné znalosti azure kognitivní vyhledávání API.

Další informace a kurzy naleznete v microsoft [learnu](https://docs.microsoft.com/learn/browse/?products=azure)nebo v jiných kurzech v [dokumentaci k azure kognitivnímu vyhledávání](https://docs.microsoft.com/azure/search/).
