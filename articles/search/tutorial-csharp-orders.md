---
title: C#kurz předvádějící řazení výsledků – Azure Search
description: Tento kurz vychází projektu "Výsledky hledání stránkování – Azure Search" Přidání řazení výsledků hledání. Další způsob řazení výsledků na vlastnost primárního a pro výsledky, které mají stejné vlastnosti primární způsob řazení výsledků u sekundární vlastnosti. Nakonec další způsob řazení výsledků podle bodovací profil.
services: search
ms.service: search
ms.topic: tutorial
ms.author: v-pettur
author: PeterTurcan
ms.date: 06/21/2019
ms.openlocfilehash: 32e253b4e131d753ab6937d0aa2a49bda471e091
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466574"
---
# <a name="c-tutorial-order-the-results---azure-search"></a>C#kurz: Řazení výsledků – Azure Search

Až tento bod v našich sérii kurzů se výsledky vrátí a zobrazí ve výchozím nastavení pořadí. To může být pořadí, ve kterém se nachází data, případně výchozí _bodovací profil_ byla definována, který se používá, pokud nejsou zadány žádné parametry pořadí. V tomto kurzu jsme začnou způsob řazení výsledků na základě primární vlastnost a pak pro výsledky, které mají stejné vlastnosti primární způsob řazení výběr u sekundární vlastnosti. Jako alternativu k řazení na základě číselné hodnoty, poslední příklad ukazuje způsob řazení podle vlastní bodovací profil. Budeme se také podrobněji trochu zobrazení _komplexní typy_.

Abyste mohli snadno porovnat vrácené výsledky, tento projekt sestavení do nekonečné posuvné projekt vytvořený v [ C# kurzu: Výsledky stránkování – Azure Search](tutorial-csharp-paging.md) kurzu.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Řazení výsledků podle jedné vlastnosti
> * Řazení výsledků na základě více vlastností
> * Filtrovat výsledky podle vzdálenost od zeměpisné bodu
> * Řazení výsledků podle bodovací profil

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu je potřeba provést následující:

Nekonečné posuvné verze [ C# kurzu: Výsledky stránkování – Azure Search](tutorial-csharp-paging.md) projektu rychle zprovoznit. Tento projekt může být vlastní verzi nebo nainstalovat z Githubu: [Vytvoření první aplikace](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="order-results-based-on-one-property"></a>Řazení výsledků podle jedné vlastnosti

Když jsme pořadí výsledky podle jedné vlastnosti, Dejme tomu, že hotelu hodnocení, chceme nejenom seřazených výsledků, chceme potvrzení, že pořadí je správná. Jinými slovy Pokud jsme uspořádat na hodnocení, můžeme zobrazit hodnocení v zobrazení.

V tomto kurzu jsme taky se přidá trochu více pro zobrazení výsledků, nejlevnější místnosti rychlost a nejdražší rychlost místnosti, pro každý hotelu. Jak jsme delve do řazení, budeme také přidávat hodnoty Ujistěte se, co jsme se na řazení se také zobrazí v zobrazení.

Není třeba upravovat žádné modely umožňující řazení. Zobrazení a kontroler vyžadují aktualizovaná. Začněte tak, že otevřete domovskou kontroleru.

### <a name="add-the-orderby-property-to-the-search-parameters"></a>Přidat vlastnost OrderBy parametry hledání

1. Vše, která je potřebná pro řazení výsledků podle jednu číselnou vlastnost je nastavena **OrderBy** parametr pro název vlastnosti. V **indexu (SearchData model)** metodu, přidejte následující řádek do parametrů hledání.

    ```cs
        OrderBy = new[] { "Rating desc" },
    ```

    >[!Note]
    > Výchozí pořadí je ascending, i když přidáte **asc** vlastnosti to jasně. Sestupném pořadí tak, že přidáte určena **desc**.

2. Nyní spusťte aplikaci a zadejte všechny běžné hledaný termín. Výsledky může nebo nemusí být ve správném pořadí, ani jako vývojář, nikoli uživatele, má všechny snadný způsob ověřování výsledky!

3. Provedeme to jasné výsledky jsou seřazeny na hodnocení. Nejprve nahraďte **pole1** a **pole2** třídy v souboru hotels.css s následující třídy (tyto třídy jsou všechny nové potřebujeme pro účely tohoto kurzu).

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
    >Prohlížeče obvykle mezipaměti souborů css, a to může vést k používá starý soubor šablony stylů css a úpravy ignorovány. Dobrým způsobem zaokrouhlí Toto je přidat řetězec dotazu s parametrem verze na odkaz. Příklad:
    >
    >```html
    >   <link rel="stylesheet" href="~/css/hotels.css?v1.1" />
    >```
    >
    >Aktualizujte číslo verze, pokud se domníváte, že váš prohlížeč používá starý soubor šablony stylů css.

4. Přidat **hodnocení** vlastnost **vyberte** parametr v **indexu (SearchData model)** metody.

    ```cs
    Select = new[] { "HotelName", "Description", "Rating"},
    ```

5. Otevřete zobrazení (index.cshtml) a nahraďte smyčky vykreslování ( **&lt;! – zobrazit data hotelu.--&gt;** ) následujícím kódem.

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

6. Hodnocení musí být k dispozici na první zobrazené stránce i na následujících stránkách, které jsou volány prostřednictvím nekonečné posuvníku. K tomu z těchto dvou případů, musíme aktualizovat i **Další** akce v kontroleru a **Posunul** funkce v zobrazení. Spouští se kontroler, změnit **Další** metodu v následujícím kódu. Tento kód vytvoří a komunikuje text hodnocení.

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

7. Teď aktualizovat **Posunul** funkce v zobrazení, aby zobrazil text hodnocení.

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

8. Nyní spusťte aplikaci znovu. Vyhledat všechny běžné pojmem, jako například "Wi-Fi" a ověřte, že výsledky jsou řazeny podle sestupném pořadí podle hodnocení hotelu.

    ![Řazení podle hodnocení](./media/tutorial-csharp-create-first-app/azure-search-orders-rating.png)

    Uvidíte, že několik hotels mít identické hodnocení a tak jejich výskytu v zobrazení opět pořadí, ve kterém se nachází data, která je volitelný.

    Předtím, než se podíváme do přidává druhou úroveň řazení, přidejme nějaký kód k zobrazení rozsahu místnosti sazby. Tento kód přidáváme do obou zobrazit extrahování dat z _komplexní typ_, a také tak, že můžeme probrat řazení výsledků na základě ceny (nejlevnější první možná).

### <a name="add-the-range-of-room-rates-to-the-view"></a>Přidat řadu kurzů místnosti do zobrazení

1. Přidání vlastnosti obsahující míry nejlevnější a nejnákladnější místnosti Hotel.cs modelu.

    ```cs
        // Room rate range
        public double cheapest { get; set; }
        public double expensive { get; set; }
    ```

2. Výpočet plateb místnosti na konci **indexu (SearchData model)** akce v kontroleru home. Po uložení dočasných dat přidáte výpočty.

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

3. Přidat **místnosti** vlastnost **vyberte** parametr **indexu (SearchData model)** metody akce kontroleru.

    ```cs
     Select = new[] { "HotelName", "Description", "Rating", "Rooms" },
    ```

4. Změňte smyčky vykreslování v zobrazení tak, aby frekvence rozsahu pro první stránka výsledků.

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

5. Změnit **Další** metoda v domácí adaptér komunikaci rozsah rychlostí, pro další stránky výsledků.

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

6. Aktualizace **Posunul** funkce v zobrazení pro zpracování místnosti sazby za text.

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

7. Spusťte aplikaci a ověřte, že se zobrazují rozsahy frekvence místnosti.

    ![Zobrazení rozsahů míra místnosti](./media/tutorial-csharp-create-first-app/azure-search-orders-rooms.png)

**OrderBy** vlastnost parametry hledání nebude přijímat položku například **Rooms.BaseRate** poskytnout nejlevnější rychlost místnosti, i v případě, že místnosti bylo již seřazená podle frekvence (které nejsou). Zobrazíte hotely v ukázkové datové sadě, Objednáno míra místnosti, je třeba seřadit výsledky v domácí kontroleru a odesílat tyto výsledky k zobrazení do požadovaného pořadí.

## <a name="order-results-based-on-multiple-values"></a>Řazení výsledků na základě několika hodnot

Nyní otázkou je postup rozlišovat mezi hotels pomocí stejné hodnocení. Jeden vhodný způsob může být pořadí na základě posledního renovovanou byl hotelu. Jinými slovy Čím nedávno hotelu byl renovovanou, tím větší hotelu se zobrazí ve výsledcích.

1. Chcete-li přidat druhou úroveň řazení, změňte **OrderBy** a **vyberte** vlastnosti v **indexu (SearchData model)** tak, aby zahrnoval  **Části LastRenovationDate** vlastnost.

    ```cs
    OrderBy = new[] { "Rating desc", "LastRenovationDate desc" },
    Select = new[] { "HotelName", "Description", "Rating", "Rooms", "LastRenovationDate" },
    ```

    >[!Tip]
    >Je možné zadat libovolný počet vlastností v **OrderBy** seznamu. Pokud hotels stejné hodnocení a renovace datum, může je třeba zadat třetí vlastnost k jejich rozlišení.

2. Znovu musíme naleznete v tématu renovace data v zobrazení, stačí je potřeba mít jistotu, že pořadí je správný. Pro taková věc jako renovace pravděpodobně právě rok je povinný. Smyčka vykreslování v zobrazení změňte na následující kód.

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

3. Změnit **Další** metoda v domácí kontroleru, aby předával složku roku datum posledního renovace.

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

4. Změnit **Posunul** funkce v zobrazení pro zobrazení potřebného textu renovace.

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

5. Spusťte aplikaci. Hledat na běžný pojem, jako je například "fond" nebo "Zobrazit" a ověřte, že hotels pomocí stejné hodnocení se teď zobrazují v sestupném pořadí podle renovace datum.

    ![Řazení renovace Date](./media/tutorial-csharp-create-first-app/azure-search-orders-renovation.png)

## <a name="filter-results-based-on-a-distance-from-a-geographical-point"></a>Filtrovat výsledky podle vzdálenost od zeměpisné bodu

Hodnocení, data a času renovace jsou příkladem vlastnosti, které se nejlíp zobrazí v sestupném pořadí. Abecední by příklad vhodné využít vzestupné pořadí (např. Pokud byl jen jeden **OrderBy** vlastnost a byla nastavena na **HotelName** pak bude zobrazen abecedním pořadí ). Pro ukázková data, by být vhodnější vzdálenost od zeměpisné bodu.

Zobrazit výsledky podle zeměpisné vzdálenost, několik kroky jsou povinné.

1. Vyfiltrovat všechny hotely, které jsou mimo zadanou radius od dané chvíle zadáním filtru s longitude, latitude a parametry protokolu radius. Zeměpisná délka dostane první funkci bodu. RADIUS je v kilometrech.

    ```cs
        // "Location" must match the field name in the Hotel class.
        // Distance (the radius) is in kilometers.
        // Point order is Longitude then Latitude.
        Filter = $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') le {model.radius}",
    ```

2. Výše uvedený filtr nemá _není_ řazení výsledků podle vzdálenost, stačí odebere odlehlé hodnoty. Řazení výsledků, zadejte **OrderBy** nastavení, která určuje metodu geoDistance.

    ```cs
    OrderBy = new[] { $"geo.distance(Location, geography'POINT({model.lon} {model.lat})') asc" },
    ```

3. Přestože výsledků vrácených rutinou Azure Search pomocí filtru vzdálenost, počítaných vzdálenost mezi daty a zadaným bodem je _není_ vrátila. Přepočítejte tuto hodnotu v zobrazení a kontroler, pokud chcete zobrazit ve výsledcích.

    Následující kód vypočítá vzdálenost mezi dvěma body lat/fyzický pevný disk.

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

4. Teď máte tyto souvislostí. Tyto fragmenty kódu jsou však jde v našem kurzu proběhne, sestavování, že aplikace na základě map se ponechané jako cvičení pro čtečku. Abyste mohli dál v tomto příkladu, vezměte v úvahu buď zadání názvu města s protokolem radius, nebo vyhledání bod na mapě a vyberete poloměr. Chcete-li prozkoumat tyto možnosti dále, naleznete v následujících zdrojích:

* [Dokumentace k Azure Maps](https://docs.microsoft.com/azure/azure-maps/)
* [Najít adresu pomocí služby vyhledávání Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)

## <a name="order-results-based-on-a-scoring-profile"></a>Řazení výsledků podle bodovací profil

Uvedené příklady v tomto kurzu zatím ukazují, jak uspořádat na numerické hodnoty (hodnocení, renovace datum, geografické vzdálenosti), poskytování _přesné_ zpracovat řazení. Nicméně některé prohledávání a některá data neumožňují na tyto jednoduché porovnávání mezi dva datové prvky. Služba Azure Search zahrnuje koncept _vyhodnocování_. _Profily skórování_ je možné zadat pro sadu dat, který slouží k poskytování více kvalitativní a komplexní porovnání, které by měly být nejvíc hodí v situaci, kdy například porovnávání textová data se rozhodnout, který by měl být zobrazí první.

Profily vyhodnocování nejsou definované uživatelem, ale obvykle správci datových sad. Několik profilů vyhodnocování již byly vytvořeny na datech hotels. Pojďme podívat, jak je definované bodovací profil a potom zkuste psaní kódu pro vyhledávání na ně.

### <a name="how-scoring-profiles-are-defined"></a>Jak bodovací profily jsou definovány.

Pojďme podívat se na tři příklady bodovací profily a zvažte, jak se každý _by měl_ vliv na výsledky pořadí. Jako vývojář aplikací nenapíšete těchto profilů, jsou zapsány pomocí Správce dat, ale jsou užitečné podívat se na syntaxi.

1. Toto je výchozí pro datovou sadu hotels použít, pokud nezadáte žádné bodovací profil **OrderBy** nebo **ScoringProfile** parametru. Tento profil zvyšuje _skóre_ pro hotelu Pokud hledaný text je k dispozici v hotelu název, popis a seznam značek (zařízení). Všimněte si, jak váhy hodnocení upřednostnit určitá pole. Pokud hledaný text se zobrazí v jiném poli, není uveden níže, bude mít lepší zvolit váhu 1. Samozřejmě Čím skóre, dříve výsledek se zobrazí v zobrazení.

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

2. Následující bodovací profil posiluje skóre výrazně, pokud zadaný parametr obsahuje nejméně jeden seznam značek (které voláme "zařízení"). Klíčovým bodem tento profil, který je parametr _musí_ zadat, která obsahuje text. Pokud tento parametr je prázdný nebo není zadán, bude vyvolána k chybě.
 
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

3. V tomto příkladu třetí hodnocení poskytuje výrazné zvýšení výkonu na skóre. Datum posledního renovovanou bude také zvýšení skóre, ale pouze pokud tato data se vrátí do 730 dnů (2 roky) aktuálního data.

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

    Nyní dejte nám podívejte se, pokud se tyto profily fungovat podle myslíme si, že by měly.

### <a name="add-code-to-the-view-to-compare-profiles"></a>Přidejte kód do zobrazení a porovnání profily

1. Otevřete soubor index.cshtml a nahraďte &lt;tělo&gt; oddíl s následujícím kódem.

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

2. Otevřete soubor SearchData.cs a nahraďte **SearchData** třídy následujícím kódem.

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

### <a name="add-code-to-the-controller-to-specify-a-scoring-profile"></a>Přidejte kód do kontroleru k určení bodovací profil

1. Otevřete soubor domácí kontroleru. Přidejte následující **pomocí** – příkaz (pro usnadnění vytváření seznamů).

    ```cs
    using System.Linq;
    ```

2.  V tomto příkladu budeme potřebovat počáteční volání **Index** trochu více než jen vrátit počáteční zobrazení. Metoda teď hledá až 20 vymoženosti zobrazíte v zobrazení.

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

3. Potřebujeme dva privátní metody omezující vlastnosti uložit do dočasného úložiště nebo můžete obnovit z dočasného úložiště a naplnit modelu.

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

4. Musíme nastavit **OrderBy** a **ScoringProfile** parametry podle potřeby. Nahraďte existující **indexu (SearchData model)** metoda následujícím kódem.

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

    Přečtěte si poznámky pro každou z **přepnout** výběry.

5. Jsme není nutné provádět žádné změny **Další** akce, pokud jste dokončili další kód pro předchozí části na pořadí na základě více vlastností.

### <a name="run-and-test-the-app"></a>Spuštění a testování aplikace

1. Spusťte aplikaci. Měli byste vidět kompletní sadu vymoženosti v zobrazení.

2. Pro řazení, vybrat "číselné hodnocení" vám poskytne číselné řazení, které jste implementovali již v tomto kurzu s datem renovace rozhodování mezi hotels rovná hodnocení.

![Řazení "beach" na základě hodnocení](./media/tutorial-csharp-create-first-app/azure-search-orders-beach.png)

3. Teď zkuste profil "podle vymoženosti". Různé dle vymoženosti a ověřte, že jsou povýšeny hotely se tyto vymoženosti nahoru v seznamu výsledků.

![Řazení "beach" na základě profilu](./media/tutorial-csharp-create-first-app/azure-search-orders-beach-profile.png)

4. Zkuste "Podle Renovated datum a hodnocení profilu" Pokud chcete zobrazit, pokud se zobrazí, co očekáváte. Pouze nedávno renovovanou hotels poslán _aktuálnosti_ boost.

### <a name="resources"></a>Zdroje a prostředky

Další informace naleznete na následujícím [přidání profilů vyhodnocování do indexu Azure Search](https://docs.microsoft.com/azure/search/index-add-scoring-profiles).

## <a name="takeaways"></a>Shrnutí

Vezměte v úvahu následující takeaways z tohoto projektu:

* Uživatelé se očekávají, že výsledky hledání povolujeme, relevantní nejprve.
* Data, třeba strukturována tak, aby pořadí je snadné. Nepovedlo se nám se budou řadit "nejlevnější" nejprve snadno, jak se data strukturovaná, aby mohl řazení provést bez dalšího kódu.
* Může existovat mnoho úrovní rozlišovat mezi výsledky, které mají stejnou hodnotu na vyšší úrovni pořadí řazení.
* Jsou přirozené pro některé výsledky povolujeme ve vzestupném pořadí (například vzdálenost od bodu) a některých v sestupném pořadí (například Host hodnocení).
* Profily skórování lze definovat, když číselné porovnání nejsou k dispozici nebo není dostatečně inteligentní pro datovou sadu. Vyhodnocování každého výsledku pomoct pořadí a monetizace zobrazit výsledky.

## <a name="next-steps"></a>Další postup

Dokončili jste tuto sérii C# výukových kurzů – které by měl získali cenné informace o rozhraní API služby Azure Search.

Další referenční dokumentace a kurzy, zvažte možnost procházení [Microsoft Learn](https://docs.microsoft.com/learn/browse/?products=azure), nebo další kurzy v [dokumentace ke službě Azure Search](https://docs.microsoft.com/azure/search/).
