---
title: Doporučené postupy pro službu Route Služby Azure Maps | Mapy Microsoft Azure
description: Zjistěte, jak efektivně směrovat pomocí služby Route Service z Map Microsoft Azure.
author: philmea
ms.author: philmea
ms.date: 03/11/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 85ce29d088b8fbd110988db67776d89346215e5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335415"
---
# <a name="best-practices-for-azure-maps-route-service"></a>Doporučené postupy pro službu Azure Maps Route

Směry trasy a rozhraní API matice tras ve službě Route [Služby](https://docs.microsoft.com/rest/api/maps/route) Azure Maps lze použít k výpočtu odhadovaných časů příjezdu (ETA) pro každou požadovanou trasu. Lana trasy zohlední faktory, jako jsou dopravní informace v reálném čase a historické dopravní údaje, jako jsou typické rychlosti na silnici v požadovaný den v týdnu a denní dobu. Rozhraní API vrátí nejkratší nebo nejrychlejší trasy, které jsou k dispozici pro více cílů najednou v pořadí nebo v optimalizovaném pořadí na základě času nebo vzdálenosti. Uživatelé mohou také požadovat specializované trasy a podrobnosti pro chodce, cyklisty a užitková vozidla, jako jsou nákladní automobily. V tomto článku budeme sdílet osvědčené postupy pro volání [služby](https://docs.microsoft.com/rest/api/maps/route)Azure Maps Route Service a dozvíte se, jak:

* Vyberte si mezi rozhraními API směrů trasy a rozhraním MATRIX Routing API
* Vyžádejte si historické a předpokládané doby jízdy na základě údajů o provozu v reálném čase a historických dopravních informacích
* Vyžádejte si podrobnosti trasy, jako je čas a vzdálenost, pro celou trasu a každou část trasy
* Žádost o trasu pro užitkové vozidlo, jako je nákladní automobil
* Vyžádejte si dopravní informace na trase, jako jsou uvíznutá a informace o mýtném
* Vyžádejte si trasu, která se skládá z jedné nebo více zastávek (trasové body)
* Optimalizace trasy jedné nebo více zastávek pro dosažení nejlepšíobjednávky k návštěvě každé zastávky (trasový bod)
* Optimalizujte alternativní trasy pomocí podpůrných bodů. Nabídněte například alternativní trasy, které projdou nabíjecí stanicí pro elektromobily.
* Použití [služby Route service](https://docs.microsoft.com/rest/api/maps/route) s webovou sadou Azure Maps SDK

## <a name="prerequisites"></a>Požadavky

K volání na rozhraní API Azure Maps potřebujete účet Azure Maps a klíč. Další informace naleznete [v tématu Vytvoření účtu](quick-demo-map-app.md#create-an-account-with-azure-maps) a Získání [primárního klíče](quick-demo-map-app.md#get-the-primary-key-for-your-account). Primární klíč se také označuje jako primární klíč předplatného nebo klíč předplatného.

Informace o ověřování v Mapách Azure najdete [v tématu Správa ověřování v Mapách Azure](./how-to-manage-authentication.md). Další informace o pokrytí služby route naleznete v tématu [Pokrytí směrování](routing-coverage.md).

Tento článek používá [postman aplikace](https://www.postman.com/downloads/) k sestavení volání REST, ale můžete zvolit libovolné prostředí pro vývoj rozhraní API.

## <a name="choose-between-route-directions-and-matrix-routing"></a>Možnost volby mezi směry trasy a směrováním matice

Směry trasy API vrátit pokyny včetně doby jízdy a souřadnice pro trasu. Rozhraní API Matice trasy umožňuje vypočítat dobu jízdy a vzdálenosti pro sadu tras, které jsou definovány podle původu a cílového umístění. Pro každý daný původ rozhraní API matice vypočítá náklady (cestovní čas a vzdálenost) směrování z tohoto počátku do každého místa určení. Všechna tato rozhraní API umožňují zadat parametry, jako je požadovaný čas odjezdu, časy příjezdu a typ vozidla, jako je auto nebo nákladní automobil. Všechny používají údaje o provozu v reálném čase nebo prediktivní dopravní údaje, aby vrátily nejoptimálnější trasy.

Zvažte volání trassměrovacích api, pokud váš scénář je:

* Vyžádejte si nejkratší nebo nejrychlejší trasu mezi dvěma nebo více známými místy, abyste získali přesné časy příjezdu vašich doručovacích vozidel.
* Vyžádejte si podrobné navádění k trase, včetně geometrie trasy, pro vizualizaci tras na mapě
* Vzhledem k tomu, seznam míst zákazníků, vypočítat nejkratší možnou trasu k návštěvě každého místa zákazníka a vrátit se k původu. Tento scénář je běžně známý jako problém cestujícího prodejce. V jedné žádosti můžete předat až 150 trasových bodů (zastávek).
* Odesílat dávky dotazů do rozhraní API rozhraní Route Directions Batch api pouze pomocí jednoho volání rozhraní API.

Zvažte volání rozhraní Matrix Routing API, pokud váš scénář je:

* Vypočítejte dobu jízdy nebo vzdálenost mezi sadou počátků a cílů. Například máte 12 řidičů a musíte najít nejbližšího dostupného řidiče, který si vyzvedne dodávku jídla z restaurace.
* Seřaďte potenciální trasy podle jejich skutečné cestovní vzdálenosti nebo času. Rozhraní MATRIX API vrátí pouze cestovní časy a vzdálenosti pro každou kombinaci původu a cíle.
* Data clusteru založená na době jízdy nebo vzdálenostech. Například vaše společnost má 50 zaměstnanců, najít všechny zaměstnance, kteří žijí do 20 minut Doba jízdy z vaší kanceláře.

Zde je porovnání pro zobrazení některých možností tras a maticových api:

| Azure Maps API | Maximální počet dotazů v požadavku | Vyhněte se oblastem | Směrování nákladních automobilů a elektrických vozidel | trasové body a optimalizace pojízdního prodejce | Podpůrné body |
| :--------------: |  :--------------: |  :--------------: | :--------------: | :--------------: | :--------------: |
| Získat trasu trasy | 1 | | × | × | |
| Zaúčtovat směr trasy | 1 | × | × | × | × |
| Zaúčtovat dávku tras trasy | 700 | | × | × | |
| Matice postupu příspěvku | 700 | | × | | |

Další informace o možnostech směrování elektrických vozidel najdete v našem kurzu o [směrování elektromobilů pomocí notebooků Azure s Pythonem](tutorial-ev-routing.md).

## <a name="request-historic-and-real-time-data"></a>Vyžádání historických dat a dat v reálném čase

Ve výchozím nastavení služba Route předpokládá, že cestovní režim je auto a čas odjezdu je nyní. Vrátí trasu na základě provozních podmínek v reálném čase, pokud požadavek na výpočet trasy neurčí jinak. Jsou zachycena pevná časově závislá dopravní omezení, například "Levé zatáčky nejsou povoleny mezi 16:00 a 18:00" a budou zváženy motorem směrování. Uzavírky silnic, jako jsou práce na silnici, budou zváženy, pokud výslovně nepožádáte o trasu, která ignoruje aktuální živý provoz. Chcete-li ignorovat aktuální `traffic` `false` provoz, nastavte v požadavku rozhraní API.

Hodnota **travelTimeInSeconds** výpočtu trasy zahrnuje zpoždění způsobené provozem. Generuje se využitím aktuálních a historických údajů o době jízdy, kdy je čas odjezdu nastaven na tuto chvíli. Pokud je čas odjezdu nastaven v budoucnu, api vrátí předpokládané doby cesty na základě historických dat.

Pokud do požadavku zahrnete **parametr computeTravelTimeFor=all,** bude mít souhrnný prvek v odpovědi následující další pole včetně historických provozních podmínek:

| Element | Popis|
| :--- | :--- |
| noTrafficTravelTimeInSeconds | Odhadovaná doba jízdy vypočtená jako v případě, že na trase nejsou žádná zpoždění z důvodu dopravního stavu, například z důvodu přetížení |
| historickéTrafficTravelTimeInSeconds | Odhadovaná doba jízdy vypočtená pomocí historických provozních údajů závislých na čase |
| liveTrafficIncidentsTravelTimeInSeconds | Odhadovaná doba jízdy vypočtená pomocí dat rychlosti v reálném čase |

V dalších částech ukazují, jak volat rozhraní API trasy pomocí popisovaných parametrů.

### <a name="sample-query"></a>Ukázkový dotaz

V prvním příkladu níže je čas odjezdu nastaven do budoucnosti, v době psaní.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=51.368752,-0.118332:51.385426,-0.128929&travelMode=car&traffic=true&departAt=2025-03-29T08:00:20&computeTravelTimeFor=all
```

Odpověď obsahuje souhrnný prvek, například následující. Vzhledem k tomu, že čas odjezdu je nastaven a do budoucna, hodnota **trafficDelayInSeconds** je nulová. Hodnota **travelTimeInSeconds** se vypočítá pomocí historických provozních dat závislých na čase. Takže v tomto případě **travelTimeInSeconds** hodnota se rovná **historicTrafficTravelTimeInSeconds** hodnotu.

```json
"summary": {
    "lengthInMeters": 2131,
    "travelTimeInSeconds": 248,
    "trafficDelayInSeconds": 0,
    "departureTime": "2025-03-29T08:00:20Z",
    "arrivalTime": "2025-03-29T08:04:28Z",
    "noTrafficTravelTimeInSeconds": 225,
    "historicTrafficTravelTimeInSeconds": 248,
    "liveTrafficIncidentsTravelTimeInSeconds": 248
},
```

### <a name="sample-query"></a>Ukázkový dotaz

V druhém příkladu níže máme požadavek na směrování v reálném čase, kde je nyní čas odjezdu. Není explicitně zadána v adrese URL, protože se jedná o výchozí hodnotu.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=47.6422356,-122.1389797:47.6641142,-122.3011268&travelMode=car&traffic=true&computeTravelTimeFor=all
```

Odpověď obsahuje souhrn, jak je uvedeno níže. Z důvodu přetížení **trafficDelaysInSeconds** hodnota je větší než nula. Je to také větší než **historicTrafficTravelTimeInSeconds**.

```json
"summary": {
    "lengthInMeters": 16637, 
    "travelTimeInSeconds": 2905, 
    "trafficDelayInSeconds": 1604, 
    "departureTime": "2020-02-28T01:00:20+00:00",
    "arrivalTime": "2020-02-28T01:48:45+00:00", 
    "noTrafficTravelTimeInSeconds": 872, 
    "historicTrafficTravelTimeInSeconds": 1976, 
    "liveTrafficIncidentsTravelTimeInSeconds": 2905 
},
```

## <a name="request-route-and-leg-details"></a>Vyžádat si podrobnosti o trase a noze

Ve výchozím nastavení služba Route vrátí pole souřadnic. Odpověď bude obsahovat souřadnice, které tvoří `points`cestu v seznamu s názvem . Odezva trasy zahrnuje také vzdálenost od začátku trasy a odhadovaný uplynulý čas. Tyto hodnoty lze použít k výpočtu průměrné rychlosti pro celou trasu.

Následující obrázek `points` znázorňuje prvek.

<center>

![bodový seznam](media/how-to-use-best-practices-for-routing/points-list-is-hidden-img.png)

</center>

Rozbalte `point` prvek, chcete-li zobrazit seznam souřadnic pro cestu:

<center>

![bodový seznam](media/how-to-use-best-practices-for-routing/points-list-img.png)

</center>

Rozhraní API směry trasy podporují různé formáty pokynů, které lze použít zadáním **parametru instructionsType.** Chcete-li formátovat pokyny pro snadné zpracování počítače, použijte **instructionsType=coded**. Pomocí **instructionsType=tagagged** zobrazíte pokyny jako text pro uživatele. Také instrukce mohou být formátovány jako text, kde jsou označeny některé prvky pokynů a instrukce je prezentována se speciálním formátováním. Další informace naleznete v [seznamu podporovaných typů instrukcí](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#routeinstructionstype).

Pokud jsou požadovány pokyny, odpověď `guidance`vrátí nový prvek s názvem . Prvek `guidance` obsahuje dvě informace: turn-by-turn pokyny a souhrnné pokyny.

<center>

![Typ pokynů](media/how-to-use-best-practices-for-routing/instructions-type-img.png)

</center>

Prvek `instructions` drží turn-by-turn pokyny pro cestu, `instructionGroups` a má shrnuty pokyny. Každý souhrn pokynů pokrývá část cesty, která by mohla pokrývat více silnic. Api můžete vrátit podrobnosti pro úseky trasy. například rozsah souřadnic dopravní zácpy nebo aktuální rychlost provozu.

<center>

![Turn by turn pokyny](media/how-to-use-best-practices-for-routing/instructions-turn-by-turn-img.png)

</center>

<center>

![Souhrnné pokyny](media/how-to-use-best-practices-for-routing/instructions-summary-img.png)

</center>

## <a name="request-a-route-for-a-commercial-vehicle"></a>Žádost o trasu pro užitkové vozidlo

Rozhraní API směrování map Azure podporují směrování užitkových vozidel, které zahrnuje směrování užitkových nákladních vozidel. Api zvážit zadané limity. Například výška a hmotnost vozidla a v případě, že vozidlo přepravuje nebezpečný náklad. Pokud je například vozidlo převažující s hořlavinou, vyhněte se frézovacímu motoru určitým tunelům, které se nacházejí v blízkosti obytných oblastí.

### <a name="sample-query"></a>Ukázkový dotaz

Níže uvedená ukázková žádost se dotazuje na trasu pro nákladní automobil. Nákladní vozidlo převáží nebezpečný odpad třídy 1.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass1&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

Rozhraní ROUTE API vrací směry, které se přizpůsobí rozměrům nákladního vozidla a nebezpečnému odpadu. Pokyny k postupu si můžete `guidance` přečíst rozbalením prvku.

<center>

![Nákladní automobil s hazodpadem třídy 1](media/how-to-use-best-practices-for-routing/truck-with-hazwaste-img.png)

</center>

### <a name="sample-query"></a>Ukázkový dotaz

Změna třídy Us Hazmat z výše uvedeného dotazu bude mít za následek jinou trasu, aby se přizpůsobila této změně.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass9&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

Níže uvedená odpověď je pro nákladní vozidlo přepravující nebezpečný materiál třídy 9, který je méně nebezpečný než nebezpečný materiál třídy 1. Když rozbalíte `guidance` prvek číst trasy, zjistíte, že pokyny nejsou stejné. Existuje více pokynů pro trasu pro nákladní vozidlo přepravující nebezpečný materiál třídy 1.

<center>

![Nákladní automobil s hazodpadem třídy 9](media/how-to-use-best-practices-for-routing/truck-with-hazwaste9-img.png)

</center>

## <a name="request-traffic-information-along-a-route"></a>Vyžádání dopravních informací na trase

Pomocí rozhraní API pro směrování tras Azure Maps mohou vývojáři `sectionType` požadovat podrobnosti pro každý typ oddílu zahrnutím parametru do požadavku. Můžete například požádat o informace o rychlosti pro každý segment dopravní zácpy. Naleznete v [seznamu hodnot pro sectionType klíč](https://docs.microsoft.com/rest/api/maps/route/getroutedirections#sectiontype) se dozvíte o různých podrobnostech, které můžete požadovat.

### <a name="sample-query"></a>Ukázkový dotaz

Následující dotaz `sectionType` nastaví `traffic`na . Požaduje oddíly, které obsahují dopravní informace ze Seattlu do San Diega.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&sectionType=traffic&query=47.6062,-122.3321:32.7157,-117.1611
```

Odpověď obsahuje oddíly, které jsou vhodné pro provoz podél daných souřadnic.

<center>

![dopravní úseky](media/how-to-use-best-practices-for-routing/traffic-section-type-img.png)

</center>

Tuto volbu lze použít k obarvení sekcí při vykreslování mapy, jako na obrázku níže: 

<center>

![dopravní úseky](media/how-to-use-best-practices-for-routing/show-traffic-sections-img.png)

</center>

## <a name="calculate-and-optimize-a-multi-stop-route"></a>Výpočet a optimalizace vícestopé trasy

Azure Maps aktuálně poskytuje dvě formy optimalizace tras:

* Optimalizace na základě požadovaného typu trasy, aniž by se změnilo pořadí trasových bodů. Podporované typy [tras](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#routetype) naleznete zde

* Optimalizace obchodního cestujícího, která mění pořadí trasových bodů, aby získala nejlepší objednávku k návštěvě každé zastávky

Pro vícestopé trasy může být v jednom požadavku na trasu určeno až 150 trasových bodů. Počáteční a koncová umístění souřadnic mohou být stejná, jako by tomu bylo v případě zpáteční cesty. Pro výpočet trasy je však třeba zadat alespoň jeden další trasový bod. Trasové body lze přidat do dotazu mezi souřadnice počátku a cíle.

Pokud chcete optimalizovat nejlepší pořadí pro návštěvu daného trasového bodu, musíte zadat **computeBestOrder=true**. Tento scénář je také známý jako problém optimalizace pojízdní prodejce.

### <a name="sample-query"></a>Ukázkový dotaz

Následující dotaz požaduje cestu pro šest trasových `computeBestOrder` bodů `false`s parametrem nastaveným na . Je to také výchozí hodnota `computeBestOrder` parametru.

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=false&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

Odpověď popisuje délku cesty na 140,851 metrů, a že to bude trvat 9,991 sekundy cestovat touto cestou.

<center>

![Neoptimalizovaná odezva](media/how-to-use-best-practices-for-routing/non-optimized-response-img.png)

</center>

Obrázek níže znázorňuje cestu vyplývající z tohoto dotazu. Tato cesta je jednou z možných cest. Není to optimální cesta na základě času nebo vzdálenosti.

<center>

![Neoptimalizovaný obraz](media/how-to-use-best-practices-for-routing/non-optimized-image-img.png)

</center>

Tato trasa trasa trasa trasa pořadí je: 0, 1, 2, 3, 4, 5 a 6.

### <a name="sample-query"></a>Ukázkový dotaz

Následující dotaz požaduje cestu pro stejných šest trasových bodů, jako ve výše uvedeném příkladu. Tentokrát `computeBestOrder` parametr nastaven na `true` (optimalizace prodejce cestujících).

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=true&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

Odpověď popisuje délku cesty na 91,814 metrů, a že to bude trvat 7,797 sekundy cestovat touto cestou. Cestovní vzdálenost a doba jízdy jsou zde nižší, protože rozhraní API vrátilo optimalizovanou trasu.

<center>

![Neoptimalizovaná odezva](media/how-to-use-best-practices-for-routing/optimized-response-img.png)

</center>

Obrázek níže znázorňuje cestu vyplývající z tohoto dotazu.

<center>

![Neoptimalizovaný obraz](media/how-to-use-best-practices-for-routing/optimized-image-img.png)

</center>

Optimální trasa má následující pořadí trasových bodů: 0, 5, 1, 2, 4, 3 a 6.

>[!TIP]
> Optimalizované informace o pořadí trasových bodů ze služby Směrování poskytují sadu indexů. Ty vylučují původ a cílové indexy. Tyto hodnoty je třeba počitat o 1, aby se zohlednil původ. Potom přidejte cíl na konec a získejte kompletní seřazený seznam bodů cesty.

## <a name="calculate-and-bias-alternative-routes-using-supporting-points"></a>Výpočet a zkreslení alternativních tras pomocí podpůrných bodů

Můžete mít situace, kdy chcete rekonstruovat trasu pro výpočet nula nebo více alternativních tras pro referenční trasu. Můžete například zobrazit zákazníkům alternativní trasy, které projdou vaší maloobchodní prodejnou. V takovém případě je třeba zkreslení umístění pomocí podpůrných bodů. Zde jsou kroky k zkreslení umístění:

1. Výpočet trasy tak, jak je, a získat trasu z odezvy trasy
2. Pomocí trasy vyhledejte požadovaná místa podél trasy nebo v její blízkosti. Můžete například použít rozhraní AZURE Maps [Point of Interest API](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) nebo zadat dotaz na vlastní data v databázi.  
3. Objednejte umístění na základě vzdálenosti od začátku trasy
4. Přidejte tato umístění jako podpůrné body v novém požadavku na trasu do [rozhraní API pro zaúčtování trasy](https://docs.microsoft.com/rest/api/maps/route/postroutedirections). Další informace o podpůrných bodech najdete v [dokumentaci k rozhraní API pro směrování .](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#supportingpoints) 

Při volání [rozhraní API pro zaúčtování tras](https://docs.microsoft.com/rest/api/maps/route/postroutedirections)můžete nastavit minimální čas odchylky nebo omezení vzdálenosti spolu s podpůrnými body. Tyto parametry použijte, pokud chcete nabídnout alternativní trasy, ale také chcete omezit dobu jízdy. Při použití těchto omezení budou alternativní trasy sledovat referenční trasu od počátečního bodu pro daný čas nebo vzdálenost. Jinými slovy, ostatní trasy se liší od referenční trasy podle daných omezení.

Obrázek níže je příkladem vykreslení alternativních tras se zadanými mezními odchylkami pro čas a vzdálenost.

<center>

![Alternativní trasy](media/how-to-use-best-practices-for-routing/alternative-routes-img.png)

</center>

## <a name="use-the-routing-service-in-a-web-app"></a>Použití služby Směrování ve webové aplikaci

Sada Azure Maps Web SDK poskytuje [modul služby](https://docs.microsoft.com/javascript/api/azure-maps-rest/?view=azure-maps-typescript-latest). Tento modul je pomocná knihovna, která usnadňuje použití rozhraní API AZURE Maps REST ve webových aplikacích nebo aplikacích Node.js pomocí JavaScriptu nebo TypeScriptu. Modul Service lze použít k vykreslení vrácených tras na mapě. Modul automaticky určuje, které rozhraní API se má použít s požadavky GET a POST.

## <a name="next-steps"></a>Další kroky

Další informace naleznete na adrese:

> [!div class="nextstepaction"]
> [Služba Azure Maps Route](https://docs.microsoft.com/rest/api/maps/route)

> [!div class="nextstepaction"]
> [Použití modulu Servis](https://docs.microsoft.com/azure/azure-maps/how-to-use-services-module)

> [!div class="nextstepaction"]
> [Zobrazit trasu na mapě](https://docs.microsoft.com/azure/azure-maps/map-route)

> [!div class="nextstepaction"]
> [Balíček NPM pro mapy Azure](https://www.npmjs.com/package/azure-maps-rest  )
