---
title: Osvědčené postupy pro Azure Maps Route Service v mapách služby Microsoft Azure
description: Naučte se směrovat vozidla pomocí Route Service ze Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8174529def5e3924086e49f36c225f07a4da2648
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99051647"
---
# <a name="best-practices-for-azure-maps-route-service"></a>Osvědčené postupy pro službu Azure Maps Route

Rozhraní API pro trasy tras a směrovací matice v Azure Maps [Route Service](/rest/api/maps/route) lze použít k výpočtu předpokládaných časů doručení (ETAs) pro každou požadovanou trasu. Rozhraní API pro směrování uvažují o faktorech, jako jsou informace o přenosech v reálném čase a historické údaje o provozu, jako je obvyklá rychlost provozu v požadovaném dni v týdnu a denní doba. Rozhraní API vrací nejkratší nebo nejrychlejší trasy, které jsou k dispozici více cílům v čase v pořadí nebo v optimalizovaném pořadí, na základě času nebo vzdálenosti. Uživatelé si také můžou vyžádat specializované trasy a podrobnosti pro cyklisty a komerční vozidla, jako je nákladní automobily. V tomto článku budeme sdílet osvědčené postupy pro volání Azure Maps [Route Service](/rest/api/maps/route)a naučíte se, jak:

 * Volba mezi rozhraními API pro trasy tras a směrovacím rozhraním API pro matici
 * Vyžádat si historické a predikované dojezdové časy na základě aktuálních i historických dat o provozu
 * Podrobnosti o trasách, jako je čas a vzdálenost, pro celou trasu a všechny fáze trasy
 * Vyžádat trasu pro komerční vozidlo, jako je nákladní vůz
 * Požadovat informace o přenosech podél trasy, jako jsou informace o zaseknutí a záplatcích
 * Vyžádat trasu, která se skládá z jedné nebo více zarážek (Waypoints)
 * Optimalizace trasy jednoho nebo více zastavení za účelem získání nejlepšího příkazu k návštěvě každého zastavení (bod na trase)
 * Optimalizujte alternativní trasy pomocí pomocných bodů. Můžete například nabídnout alternativní trasy, které předají elektricky zpoplatněné trakční vozidlo.
 * Použití [Route Service](/rest/api/maps/route) s Azure Maps Web SDK

## <a name="prerequisites"></a>Předpoklady

1. [Vytvořit účet Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Získejte primární klíč předplatného](quick-demo-map-app.md#get-the-primary-key-for-your-account), označovaný také jako primární klíč nebo klíč předplatného.

Další informace o pokrytí Route Service najdete v [pokrytí směrování](routing-coverage.md).

V tomto článku se k sestavení volání REST používá [aplikace pro odesílání](https://www.postman.com/downloads/) , ale můžete zvolit prostředí pro vývoj rozhraní API.

## <a name="choose-between-route-directions-and-matrix-routing"></a>Volba mezi trasami směrování a směrováním mezi tabulkami

Rozhraní API pro trasy tras vrátí instrukce, včetně doby trvání cesty a souřadnic pro cestu trasy. Rozhraní API trasy pro směrování umožňuje vypočítat dobu cesty a vzdálenosti pro sadu tras, které jsou definovány podle původu a cílových umístění. Pro každý počátek vypočítává rozhraní API pro vydaný cíl náklady (doba jízdy a vzdálenost) od tohoto zdroje do každého daného cíle. Všechna tato rozhraní API umožňují zadat parametry, jako je třeba požadovaná doba odchodu, časy příchodu a typ vozidla, jako je automobil nebo nákladní auto. Všechna používají data v reálném čase nebo prediktivní přenos dat, a to proto, aby vracely optimální trasy.

Zvažte volání rozhraní API pro trasy směrování, pokud je vaším scénářem:

* Vyžádejte nejkratší nebo nejrychlejší jízdnou trasu mezi dvěma nebo více známými umístěními, aby se dosáhlo přesné doby doručení vašich doručovacích vozidel.
* Požádejte o podrobné pokyny k trasám, včetně geometrie tras, k vizualizaci tras na mapě.
* Podle seznamu umístění zákazníků Vypočítejte nejkratší možnou trasu, která navštíví každé umístění zákazníka a vrátí se k původnímu zdroji. Tento scénář se běžně označuje jako problém prodejce na cestách. V jedné žádosti můžete předat až 150 Waypoints (zastavení).
* Odeslat dávky dotazů do rozhraní API pro směrování tras pomocí jediného volání rozhraní API.

Zvažte volání rozhraní API směrování matrice, pokud je vaším scénářem:

* Vypočítat dobu trvání cesty nebo vzdálenost mezi sadou zdrojů a místy určení. Máte například 12 ovladačů a potřebujete najít nejbližší dostupný ovladač pro výběr dodávky z jídla z restaurace.
* Seřadit potenciální trasy podle skutečné dráhy nebo času. Rozhraní API matice vrátí pouze časy a vzdálenosti pro každou kombinaci počátek a cíl.
* Data clusteru na základě doby cesty nebo vzdálenosti. Například vaše společnost má 50 zaměstnanců, vyhledá všechny zaměstnance, kteří žijí během 20 minut jednotek od kanceláře.

Tady je porovnání, ve kterém se zobrazují některé možnosti směrových cest a rozhraní API pro matic:

| Rozhraní API pro Azure Maps | Maximální počet dotazů v žádosti | Nepoužívat oblasti | Směrování nákladní a elektrické vozidlo | Optimalizace prodejců Waypoints a cestování | Podpůrné body |
| :--------------: |  :--------------: |  :--------------: | :--------------: | :--------------: | :--------------: |
| Získat směr směrování | 1 | | ✔ | ✔ | |
| Pokyny pro odeslání trasy | 1 | ✔ | ✔ | ✔ | ✔ |
| Batch – itinerář trasy | 700 | | ✔ | ✔ | |
| Vyjednaná matice směrování | 700 | | ✔ | | |

Další informace o možnostech směrování u elektrických vozidel najdete v našem kurzu o [Směrování elektrických vozidel pomocí Azure Notebooks s Pythonem](tutorial-ev-routing.md).

## <a name="request-historic-and-real-time-data"></a>Žádost o historické údaje a data v reálném čase

Ve výchozím nastavení předpokládá služba směrování, že režim cestování je automobil a čas odchodu je nyní. Vrátí směrování na základě podmínek provozu v reálném čase, pokud požadavek na výpočet trasy neurčí jinak. Pevná časová omezení pro provoz závislá na čase, například "levá klávesa nepovolená mezi 4:00 PM až 6:00 PM", jsou zachycena a budou posouzena modulem směrování. Uzavírání cest, jako je roadworks, se bude brát v úvahu, pokud výslovně nepožadujete trasu, která ignoruje aktuální živý provoz. Pokud chcete aktuální provoz ignorovat, nastavte `traffic` `false` v žádosti o rozhraní API.

Hodnota **travelTimeInSeconds** výpočtu trasy zahrnuje zpoždění v důsledku provozu. Vygeneruje se pomocí aktuálního a historických dat o době jízdy, pokud je čas odchodu nastavený na hodnotu aktuálně. Pokud je čas odchodu nastavený v budoucnosti, vrátí rozhraní API předpokládanou dobu trvání cesty na základě historických dat.

Pokud do žádosti zahrnete parametr **computeTravelTimeFor = All** , bude mít element Summary v odpovědi následující další pole, včetně historických podmínek provozu:

| Element | Popis|
| :--- | :--- |
| noTrafficTravelTimeInSeconds | Odhadovaná doba trvání cesty, která se počítá jako v případě, že na trase nedochází k žádným zpožděním kvůli přenosovým podmínkám, například kvůli zahlcení |
| historicTrafficTravelTimeInSeconds | Odhadovaná doba trvání cesty vypočtená pomocí historických dat o přenosech závislých na čase |
| liveTrafficIncidentsTravelTimeInSeconds | Odhadovaná doba trvání cesty vypočtená pomocí dat rychlosti v reálném čase |

Další části ukazují, jak volat rozhraní API směrování pomocí popisovaných parametrů.

### <a name="sample-query"></a>Ukázkový dotaz

V prvním příkladu níže je doba odchodu v době psaní nastavena na budoucnost.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=51.368752,-0.118332:51.385426,-0.128929&travelMode=car&traffic=true&departAt=2025-03-29T08:00:20&computeTravelTimeFor=all
```

Odpověď obsahuje prvek souhrnu, podobně jako následující příklad. Vzhledem k tomu, že doba odchodu je nastavená na budoucnost, hodnota **trafficDelayInSeconds** je nula. Hodnota **travelTimeInSeconds** se počítá pomocí historických dat o přenosech závislých na čase. Takže v tomto případě je hodnota **travelTimeInSeconds** rovna hodnotě **historicTrafficTravelTimeInSeconds** .

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

V druhém příkladu níže máme požadavek na směrování v reálném čase, kdy čas odchodu je teď. Není výslovně zadáno v adrese URL, protože je to výchozí hodnota.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=47.6422356,-122.1389797:47.6641142,-122.3011268&travelMode=car&traffic=true&computeTravelTimeFor=all
```

Odpověď obsahuje souhrn, jak je znázorněno níže. Z důvodu zahlcení je hodnota **trafficDelaysInSeconds** větší než nula. Je také větší než **historicTrafficTravelTimeInSeconds**.

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

## <a name="request-route-and-leg-details"></a>Podrobnosti o trasách a odnože

Ve výchozím nastavení bude služba Směrování vracet pole souřadnic. Odpověď bude obsahovat souřadnice, které tvoří cestu v seznamu s názvem `points` . Reakce na trase také zahrnuje vzdálenost od začátku trasy a odhadovaný uplynulý čas. Pomocí těchto hodnot lze vypočítat průměrnou rychlost pro celou trasu.

Následující obrázek znázorňuje `points` element.

![Body – element](media/how-to-use-best-practices-for-routing/points-list-is-hidden-img.png)

Rozbalením `point` prvku zobrazíte seznam souřadnic pro cestu:

![Element Expanded Points](media/how-to-use-best-practices-for-routing/points-list-img.png)

Rozhraní API pro itinerář trasy podporují různé formáty instrukcí, které lze použít zadáním parametru **instructionsType** . Chcete-li naformátovat pokyny pro snadné zpracování počítačů, použijte **instructionsType = Code**. Použijte **instructionsType = Tagged** k zobrazení instrukcí jako textu pro uživatele. Pokyny je také možné formátovat jako text, kde jsou označeny některé prvky instrukcí a instrukce se zobrazí se speciálním formátováním. Další informace najdete v [seznamu podporovaných typů instrukcí](/rest/api/maps/route/postroutedirections#routeinstructionstype).

Po vyžádání pokynů vrátí odpověď nový element s názvem `guidance` . `guidance`Element obsahuje dvě informace: pokyny pro zapnutí a shrnutí.

![Typ instrukcí](media/how-to-use-best-practices-for-routing/instructions-type-img.png)

`instructions`Element obsahuje přepínat směr pro cestu a `instructionGroups` obsahuje shrnuté pokyny. Každý souhrn instrukcí pokrývá segment cesty, který může pokrývat více cest. Rozhraní API mohou vracet podrobnosti o oddílech trasy. například souřadnice rozsahu zaseknutých přenosů nebo aktuální rychlost provozu.

![Zapnout podle pokynů](media/how-to-use-best-practices-for-routing/instructions-turn-by-turn-img.png)

![Shrnuté pokyny](media/how-to-use-best-practices-for-routing/instructions-summary-img.png)

## <a name="request-a-route-for-a-commercial-vehicle"></a>Žádost o trasu pro komerční vozidlo

Rozhraní API pro směrování Azure Maps podporují směrování komerčních vozidel, které pokrývá komerční směrování nákladní auta. Rozhraní API považují zadaná omezení. Například výška a hmotnost vozidla a v případě, že vozidlo uskutečňuje nebezpečný náklad. Například pokud vozidlo prochází hořlavým, modul směrování vyloučí určité tunely, které jsou blízko domácích oblastí.

### <a name="sample-query"></a>Ukázkový dotaz

Následující ukázkový požadavek se dotazuje na trasu pro komerční nákladní vůz. Nákladní vůz přepravuje nebezpečné odpadní materiály třídy 1.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass1&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

Rozhraní API trasy vrací směry, které přizpůsobují rozměry nákladní automobilu a nebezpečných odpadů. Pokyny k trase si můžete přečíst rozbalením `guidance` elementu.

![Nákladní vůz s třídou 1 hazwaste](media/how-to-use-best-practices-for-routing/truck-with-hazwaste-img.png)

### <a name="sample-query"></a>Ukázkový dotaz

Změna třídy US HAZMAT z výše uvedeného dotazu má za následek jinou trasu, která tuto změnu přizpůsobí.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass9&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

Níže uvedená odpověď je určena pro nákladní automobil, který přechází z nebezpečného materiálu třídy 9, který je méně bezpečný než nebezpečný materiál třídy 1. Když rozbalíte `guidance` prvek pro přečtení pokynů, Všimněte si, že směry nejsou stejné. K dispozici jsou další pokyny k trasám pro nákladní automobil, který je držitelem kategorie 1 nebezpečných materiálů.



![Nákladní vůz s třídou 9 hazwaste](media/how-to-use-best-practices-for-routing/truck-with-hazwaste9-img.png)



## <a name="request-traffic-information-along-a-route"></a>Vyžádat informace o přenosech podél trasy

S rozhraními API směr Azure Mapsho směrování můžou vývojáři požádat o podrobnosti pro každý typ oddílu zahrnutím `sectionType` parametru do žádosti. Můžete například požadovat informace o rychlosti pro každý segment zaseknutí provozu. Informace o různých podrobnostech, které si můžete vyžádat, najdete v [seznamu hodnot pro klíč sectionType](/rest/api/maps/route/getroutedirections#sectiontype) .

### <a name="sample-query"></a>Ukázkový dotaz

Následující dotaz nastaví na `sectionType` `traffic` . Vyžádá si oddíly, které obsahují informace o provozu z Seattlu do San Diegu.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&sectionType=traffic&query=47.6062,-122.3321:32.7157,-117.1611
```

Odpověď obsahuje oddíly, které jsou vhodné pro přenos podél daných souřadnic.

![Části přenosů](media/how-to-use-best-practices-for-routing/traffic-section-type-img.png)

Tuto možnost lze použít k obarvení oddílů při vykreslování mapy, jako na obrázku níže: 

![Barevné oddíly vykreslené na mapě](media/how-to-use-best-practices-for-routing/show-traffic-sections-img.png)

## <a name="calculate-and-optimize-a-multi-stop-route"></a>Výpočet a optimalizace trasy s několika zastávkami

Azure Maps v současné době poskytuje dvě formy optimalizace tras:

* Optimalizace na základě požadovaného typu trasy beze změny pořadí Waypoints. [Podporované typy tras](/rest/api/maps/route/postroutedirections#routetype) můžete najít tady.

* Optimalizace pro prodejny, která mění pořadí waypoints, aby se získalo co nejlepší pro návštěvě každého zastavení

V případě vícenásobného zastavení směrování může být v rámci jedné žádosti o trase zadán až 150 Waypoints. Spouštěcí a koncová umístění souřadnic můžou být stejná, jako by to byl případ s kulatou cestou. K provedení výpočtu trasy ale musíte zadat alespoň jeden další bod na trase. Waypoints lze přidat do dotazu v rámci souřadnic počátek a cíl.

Pokud chcete optimalizovat nejlepší pořadí pro návštěvě daného waypoints, musíte zadat **computeBestOrder = true**. Tento scénář je taky známý jako problém optimalizace na cestování – prodej.

### <a name="sample-query"></a>Ukázkový dotaz

Následující dotaz požádá o cestu pro šest Waypoints s `computeBestOrder` parametrem nastaveným na `false` . Je to také výchozí hodnota `computeBestOrder` parametru.

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=false&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

Odpověď popisuje délku cesty, která bude 140 851 měřičů, a to tak, aby bylo přetrvat 9 991 sekund pro cestu k této cestě.

![Neoptimalizovaná odpověď](media/how-to-use-best-practices-for-routing/non-optimized-response-img.png)

Následující obrázek znázorňuje cestu, která je výsledkem tohoto dotazu. Tato cesta je jedním z možných tras. Nejedná se o optimální cestu na základě času nebo vzdálenosti.

![Neoptimalizovaná image](media/how-to-use-best-practices-for-routing/non-optimized-image-img.png)



Toto pořadí bod na trase trasy je: 0, 1, 2, 3, 4, 5 a 6.

### <a name="sample-query"></a>Ukázkový dotaz

Následující dotaz požádá o cestu ke stejnému šesti waypoints, jako ve výše uvedené ukázce. V tuto chvíli je `computeBestOrder` Parametr nastavený na `true` (optimalizace na cestách pro prodej).

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=true&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

Odpověď popisuje délku cesty, která bude 91 814 měřičů, a to tak, aby bylo přetrvat 7 797 sekund pro cestu k této cestě. Vzdálenost cestovného a doba trvání cesty jsou níže, protože rozhraní API vrátilo optimalizovanou trasu.

![Optimalizovaná odpověď](media/how-to-use-best-practices-for-routing/optimized-response-img.png)

Následující obrázek znázorňuje cestu, která je výsledkem tohoto dotazu.

![Optimalizovaná bitová kopie](media/how-to-use-best-practices-for-routing/optimized-image-img.png)

Optimální trasa má následující bod na trase pořadí: 0, 5, 1, 2, 4, 3 a 6.

>[!TIP]
> Informace o optimalizovaném pořadí bod na trase ze služby směrování poskytují sadu indexů. Ty vylučují původ a cílové indexy. Je potřeba zvýšit hodnoty o 1 na účet pro počátek. Pak přidejte své cílové umístění do konce, abyste získali úplný uspořádaný seznam bod na trase.

## <a name="calculate-and-bias-alternative-routes-using-supporting-points"></a>Výpočet a posunutí alternativních tras pomocí pomocných bodů

Může se stát, že budete chtít vytvořit novou situaci, abyste mohli vypočítat nula nebo více alternativních tras pro referenční trasu. Například můžete chtít zobrazit alternativní trasy zákazníků, které předají vaše maloobchodní úložiště. V takovém případě je třeba vymezit umístění pomocí pomocných bodů. Tady je postup pro posunutí umístění:

1. Vypočítat trasu jako a získat cestu z odpovědi na trase
2. Použijte cestu trasy k vyhledání požadovaných umístění podél nebo blízko cesty k trase. Můžete například použít Azure Maps [Point rozhraní API pro důležité](/rest/api/maps/search/getsearchpoi) dotazy nebo dotazovat vlastní data v databázi.  
3. Pořadí umístění na základě vzdálenosti od začátku trasy
4. Přidejte tato umístění jako doprovodné body do nové žádosti o trasu do [rozhraní API pro přesměrování tras](/rest/api/maps/route/postroutedirections). Další informace o pomocných bodech najdete v [dokumentaci k rozhraní API pro pokyny pro post Route](/rest/api/maps/route/postroutedirections#supportingpoints). 

Při volání [rozhraní API pro směrování po trasách](/rest/api/maps/route/postroutedirections)můžete nastavit minimální čas odchylky nebo omezení vzdálenosti spolu s podpůrnými body. Tyto parametry použijte, pokud chcete nabízet alternativní trasy, ale také chcete omezit dobu trvání cesty. Když se použijí tato omezení, alternativní trasy budou postupovat podle trasy odkazů z počátečního bodu pro daný čas nebo vzdálenost. Jinými slovy se ostatní trasy odchýlit od referenční trasy za dané omezení.

Obrázek níže je příklad vykreslování alternativních tras se zadanými limity odchylek pro čas a vzdálenost.

![Alternativní trasy](media/how-to-use-best-practices-for-routing/alternative-routes-img.png)

## <a name="use-the-routing-service-in-a-web-app"></a>Použití směrovací služby ve webové aplikaci

Sada Azure Maps Web SDK poskytuje [modul služby](/javascript/api/azure-maps-rest/). Tento modul je pomocná knihovna usnadňující použití Azure Maps rozhraní REST API ve webových nebo Node.jsch aplikacích pomocí JavaScriptu nebo TypeScript. Modul služby lze použít k vykreslení vrácených tras na mapě. Modul automaticky určuje, které rozhraní API se má použít s požadavky GET a POST.

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět víc, přečtěte si:

> [!div class="nextstepaction"]
> [Služba Azure Maps Route](/rest/api/maps/route)

> [!div class="nextstepaction"]
> [Jak používat modul služby](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Zobrazit trasu na mapě](./map-route.md)

> [!div class="nextstepaction"]
> [Balíček NPM Azure Maps](https://www.npmjs.com/package/azure-maps-rest  )
