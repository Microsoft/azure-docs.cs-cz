---
title: Přehled | Mapy Microsoft Azure
description: Přečtěte si o službách a možnostech v Mapách Microsoft Azure a o tom, jak je používat ve svých aplikacích.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: fdac12350db785e6194cd3d057f4d2adfefa5969
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80335733"
---
# <a name="what-is-azure-maps"></a>Co je služba Azure Maps?

Azure Maps je kolekce geoprostorových služeb, které využívají čerstvá data mapování k poskytování geografického kontextu webovým a mobilním aplikacím. Azure Maps poskytuje:

* ROZHRANÍ REST API pro vykreslení map ve více stylech a na satelitních snímkech.
* Vyhledejte služby a vyhledejte adresy, místa a zajímavá místa po celém světě.
* Různé možnosti směrování; jako je point-to-point, multipoint, multipoint optimization, isochrone, užitková vozidla, provoz ovlivněný a směrování matice.
* Zobrazení toku provozu a zobrazení incidentů pro aplikace, které vyžadují dopravní informace.
* Služba mobility pro vyžádání logistiky veřejné dopravy, plánování tras v reálném čase a vyžádání informací o alternativních způsobech dopravy.
* Služby časového pásma a geolokace a převod umístění na časová pásma.
* Geofencing služba a mapování úložiště dat, s informacemi o poloze hostované v Azure. 
* Zjišťování polohy prostřednictvím geoprostorové analýzy. 

Služby Azure Maps jsou navíc dostupné prostřednictvím sady Web SDK nebo sady Android SDK. Tyto nástroje pomáhají vývojářům rychle vyvíjet a škálovat řešení, která integrují informace o poloze do řešení Azure. 

Můžete si zaregistrovat bezplatný [účet Azure Maps](https://azure.microsoft.com/services/azure-maps/) a začít s vývojem.

Následující video vysvětluje Azure Maps do hloubky:

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>Ovládací prvky mapy

### <a name="web-sdk"></a>Sada Web SDK

Sada Azure Maps Web SDK umožňuje přizpůsobit interaktivní mapy pomocí vlastního obsahu a obrázků. Tuto interaktivní mapu můžete použít jak pro webové, tak pro mobilní aplikace. Ovládací prvek mapy využívá WebGL, takže můžete vykreslit velké datové sady s vysokým výkonem. Vývoj s sadou SDK pomocí JavaScriptu nebo TypeScriptu.

![Příklad mapy změny obyvatelstva](media/about-azure-maps/intro_web_map_control.png)

### <a name="android-sdk"></a>Android SDK

Pomocí sady Azure Maps SDK pro Android můžete vytvářet mobilní mapové aplikace. 

![Mapování příkladů na mobilním zařízení](media/about-azure-maps/android_sdk.png)

## <a name="services-in-azure-maps"></a>Služby v rámci Azure Maps

Azure Maps se skládá z následujících devíti služeb, které můžou poskytovat geografický kontext vašim aplikacím Azure.

### <a name="data-service"></a>Služba Data

Data jsou pro mapy nezbytná. Pomocí služby Data Service můžete nahrávat a ukládat geoprostorová data pro použití s prostorovými operacemi nebo kompozicí obrazu.  Přibližování zákaznických dat ke službě Azure Maps sníží latenci, zvýší produktivitu a vytvoří te nové scénáře ve vašich aplikacích. Podrobnosti o této službě naleznete v [dokumentaci k rozhraní API datové služby](https://docs.microsoft.com/rest/api/maps/data).

### <a name="mobility-service"></a>Služba Mobility

Služba Azure Maps Mobility umožňuje plánování cest v reálném čase. Vrací nejlepší možné možnosti trasy a poskytuje různé druhy cestovních režimů. Pro metro (město) oblasti, tyto režimy mohou zahrnovat pěší turistiku, cykloturistiku a veřejnou dopravu. Můžete požádat o itinerář tranzitu, geometrii linky, seznamy zastávek, plánované příjezdy, příjezdy v reálném čase a upozornění na služby.

Služba také umožňuje vyhledávání konkrétnítypy objektů v okolí umístění. Uživatelé mohou vyhledávat sdílená kola, skútry nebo auta v okolí místa. Uživatelé si mohou vyžádat počet dostupných kol v nejbližším doku a vyhledávat dostupná vozidla pro sdílení jízd. A uživatelé mohou najít podrobnosti, jako je budoucí dostupnost vozidel a aktuální úroveň paliva.

Další informace o službě najdete v [dokumentaci k rozhraní API pro mobilitu](https://docs.microsoft.com/rest/api/maps/mobility).

### <a name="render-service"></a>Služba Render

Služba Render pomáhá vývojářům vytvářet webové a mobilní aplikace s funkcemi mapování. Služba nabízí buď vysoce kvalitní rastrové podklady v 19 úrovních přiblížení, nebo plně přizpůsobitelné vektorové obrazy.

![Příklad mapy ze služby Vykreslení](media/about-azure-maps/intro_map.png)

Služba Render teď nabízí rozhraní API ve verzi Preview, která umožňují vývojářům pracovat se satelitními snímky. Další podrobnosti naleznete v [dokumentaci k rozhraní API render .](https://docs.microsoft.com/rest/api/maps/render)

### <a name="route-service"></a>Služba Route

Služba Route obsahuje robustní výpočty geometrie na základě reálné infrastruktury a trasy pro různé způsoby dopravy. Služba umožňuje vývojářům vypočítat směry v celé řadě cestovních režimů, jako je auto, nákladní automobil, jízdní kolo nebo chůze. Služba také zvažuje vstupy, jako jsou dopravní podmínky, omezení hmotnosti nebo přeprava nebezpečných materiálů.

![Příklad mapy ze služby Route](media/about-azure-maps/intro_route.png)

Služba Route nabízí náhled pokročilých funkcí, jako jsou: 

* Dávkové zpracování více požadavků na postup.
* Matice doby jízdy a vzdálenosti mezi sadou počátků a destinací.
* Hledání tras nebo vzdáleností, které mohou uživatelé cestovat na základě požadavků na čas nebo palivo. 

Podrobnosti o možnostech směrování naleznete v [dokumentaci rozhraní ROUTE API](https://docs.microsoft.com/rest/api/maps/route).

### <a name="search-service"></a>Služba Search

Služba Hledat pomáhá vývojářům vyhledávat adresy, místa, firemní zápisy podle názvu nebo kategorie a další geografické informace. Vyhledávací služba může [obrátit adresy geokódu](https://en.wikipedia.org/wiki/Reverse_geocoding) a procházet ulicemi na základě zeměpisných šířků a délky.

![Příklad hledání na mapě](media/about-azure-maps/intro_search.png)

Vyhledávací služba také poskytuje pokročilé funkce, jako jsou:

* Hledat podél trasy.
* Hledejte v širší oblasti.
* Dávková skupina požadavků na vyhledávání.
* Vyhledejte větší oblast místo bodu umístění. 

Rozhraní API pro dávkové hledání a hledání oblastí jsou aktuálně ve verzi Preview. Další podrobnosti o možnostech vyhledávání naleznete v [dokumentaci k rozhraní SEARCH API](https://docs.microsoft.com/rest/api/maps/search).

### <a name="spatial-operations-service"></a>Služba prostorového provozu

Služba Azure Maps Spatial Operations přebírá informace o poloze. Rychle analyzuje informace o poloze, aby pomohl informovat zákazníky o probíhajících událostech probíhajících v čase a prostoru. Umožňuje téměř analýzu událostí v reálném čase a prediktivní modelování událostí. 

Služba umožňuje zákazníkům vylepšit jejich lokalizační inteligenci pomocí knihovny běžných geoprostorových matematických výpočtů. Běžné výpočty zahrnují nejbližší bod, velkou kružnici a vyrovnávací paměti. Další informace o službě a různých funkcích naleznete v [dokumentaci k rozhraní API prostorových operací](https://docs.microsoft.com/rest/api/maps/spatial).

### <a name="time-zone-service"></a>Služba Time Zone

Služba Časové pásmo umožňuje dotazovat aktuální, historické a budoucí informace o časovém pásmu. Můžete použít buď páry zeměpisné šířky a délky, nebo [ID ID IANA](https://www.iana.org/). Služba Časové pásmo také umožňuje:

* Převod ID časového pásma systému Microsoft Windows na časová pásma IANA.
* Načítání posunu časového pásma do času UTC.
* Získání aktuálního času ve zvoleném časovém pásmu. 

Typická odpověď JSON pro dotaz na službu časového pásma vypadá jako následující ukázka:

```JSON
{
    "Version": "2017c",
    "ReferenceUtcTimestamp": "2017-11-20T23:09:48.686173Z",
    "TimeZones": [{
        "Id": "America/Los_Angeles",
        "ReferenceTime": {
            "Tag": "PST",
            "StandardOffset": "-08:00:00",
            "DaylightSavings": "00:00:00",
            "WallTime": "2017-11-20T15:09:48.686173-08:00",
            "PosixTzValidYear": 2017,
            "PosixTz": "PST+8PDT,M3.2.0,M11.1.0"
        }
    }]
}
```

Podrobnosti o této službě naleznete v dokumentaci rozhraní [API časového pásma](https://docs.microsoft.com/rest/api/maps/timezone).

### <a name="traffic-service"></a>Služba Traffic

Služba Traffic je sada webových služeb, které mohou vývojáři používat pro webové nebo mobilní aplikace, které vyžadují dopravní informace. Služba nabízí dva datové typy:

* Dopravní tok: V reálném čase pozorované rychlosti a doby jízdy pro všechny klíčové silnice v síti.
* Dopravní nehody: Aktuální pohled na dopravní zácpy a incidenty v okolí silniční sítě.

![Příklad mapy s dopravními informacemi](media/about-azure-maps/intro_traffic.png)

Další informace naleznete v [dokumentaci k rozhraní Traffic API](https://docs.microsoft.com/rest/api/maps/traffic).

### <a name="ip-to-location-service"></a>IP až lokační služba

Pomocí služby IP to Location zobrazíte náhled načteného dvoupísmenný kód země pro ADRESU IP. Tato služba vám může pomoci zlepšit uživatelské prostředí tím, že poskytuje přizpůsobený obsah aplikace na základě geografické polohy.

Další podrobnosti o službě IP to Location v rozhraní REST API najdete v [dokumentaci k rozhraní API Azure Maps Geolocation API](https://docs.microsoft.com/rest/api/maps/geolocation).

## <a name="programming-model"></a>Programovací model

Azure Maps jsou vytvořené pro mobilitu a můžou vám pomoct vyvíjet aplikace napříč platformami. Používá programovací model, který je jazyk agnostik a podporuje výstup JSON prostřednictvím [REST API](https://docs.microsoft.com/rest/api/maps/).

Azure Maps také nabízí pohodlné [ovládání map javascriptu](https://docs.microsoft.com/javascript/api/azure-maps-control) s jednoduchým programovacím modelem. Vývoj je rychlý a snadný pro webové i mobilní aplikace.

## <a name="usage"></a>Využití

Přístup ke službám Azure Maps je otázkou přejdete na [portál Azure](https://portal.azure.com) a vytvoříte účet Azure Maps.

V Azure Maps se používá schéma ověřování založeného na klíčích. Váš účet je dodáván se dvěma klíči, které jsou již pro vás vygenerovány, použijte buď klíče. Začněte integrovat tyto možnosti umístění ve vaší aplikaci a podejte požadavky na služby Azure Maps.

Poznámka – Azure Maps sdílí dotazy na adresu/umístění poskytnuté zákazníkem ("dotazy") s třetí stranou TomTom pro účely mapování funkcí. Dotazy nejsou při sdílení se společností TomTom propojeny s žádným zákazníkem ani koncovým uživatelem a nelze je použít k identifikaci jednotlivců. Společnost Microsoft v současné době přidává společnost TomTom do seznamu subdodavatelů služeb online. Všimněte si, že mobilita a meteorologické služby, které zahrnují integraci s Moovit a AccuWeather jsou v současné době v [NÁHLED](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="supported-regions"></a>Podporované oblasti

Rozhraní API Map Azure jsou momentálně dostupná ve všech zemích a oblastech s výjimkou těchto:

* Čína
* Jižní Korea

Ověřte, zda je umístění vaší aktuální IP adresy v podporované zemi.

## <a name="next-steps"></a>Další kroky

Vyzkoušejte ukázkovou aplikaci, která zobrazuje Azure Maps:

> [!div class="nextstepaction"]
> [Úvodní příručka: Vytvoření webové aplikace](quick-demo-map-app.md)

Buďte v obraze v Azure Maps: 

> [!div class="nextstepaction"]
> [Blog o Mapách Azure](https://azure.microsoft.com/blog/topics/azure-maps/)
