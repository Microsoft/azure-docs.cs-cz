---
title: Přehled Azure Maps | Microsoft Docs
description: Seznámení s Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 02/04/2019
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: ce55185effc67709157ce6219c405bb8e32dd5db
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55768575"
---
# <a name="what-is-azure-maps"></a>Co je služba Azure Maps?

Azure Maps je kolekce geoprostorových služeb, nepřipnutých nejčerstvější mapování daty dostupnými poskytování přesných geografického kontextu pro webové a mobilní aplikace. Azure Maps se skládá z rozhraní REST API pro vykreslování **mapy** v několika – styly a satelitních snímků, **hledání** pro adresy, místa a body zájmu po celém světě; **Směrování** point-to-point, multipoint, multipoint optimalizace, isochrone, komerční vozidla, provoz k nim a matice směrování; zobrazení to nejlepší z vyvíjet toku provozu a incidenty; navazování polohu uživatele přes **Informace o zeměpisné poloze**; a umístění pro převod **časových pásem**, jakož i načítání čas do umístění. Kromě toho Azure Maps nabízí služby pro **monitorování geografických zón**, mapy **Data** úložiště – hostování informace o poloze v Azure; a **prostorových operace** umístění Chcete-li zobrazit Intelligence prostřednictvím geoprostorové analýzy. Služby Azure Maps jsou k dispozici přímo jako rozhraní REST API nebo prostřednictvím naší robustní **Web SDK** nebo **sady Android SDK**. Tyto nástroje umožňují vývojářům rychle vyvíjet a škálovat řešení schopná integrovat informace o poloze do řešení Azure z v rámci cloudu Azure. Zaregistrujte vaši bezplatnou [účet Azure Maps](https://azure.microsoft.com/services/azure-maps/) dnes a vývoj start!

Následující video vysvětluje Azure Maps do hloubky:

<iframe src="https://channel9.msdn.com/Shows/Azure-Friday/Azure-Location-Based-Services/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>Mapové ovládací prvky

### <a name="web-control"></a>Webový ovládací prvek

Webový ovládací prvek Azure Maps umožňuje přizpůsobit interaktivní mapy s využitím vlastního obsahu a obrázků, které chcete zobrazit ve svých webových nebo mobilních aplikacích. Tento ovládací prvek využívá WebGL a umožňuje vykreslovat rozsáhlé datové sady s vysokým výkonem. Ovládací prvky můžete vyvíjet pomocí JavaScriptu nebo TypeScriptu.

![Webový ovládací prvek Azure Maps](media/about-azure-maps/Introduction_WebMapControl.png)

## <a name="services-in-azure-maps"></a>Služby v rámci Azure Maps

Azure Maps se skládá z následujících šesti služeb, které můžou poskytovat geografický kontext pro vaše aplikace Azure.

### <a name="render-service"></a>Služba Render

Služba Render je určená vývojářům, kteří potřebují vytvářet webové a mobilní mapové aplikace. Služba nabízí buď vysoce kvalitní rastrové podklady v 19 úrovních přiblížení, nebo plně přizpůsobitelné vektorové obrazy.

![Azure Maps Map.png](media/about-azure-maps/Introduction_Map.png)

Služba Render teď nabízí rozhraní API ve verzi Preview, která umožňují vývojářům pracovat se satelitními snímky. Další podrobnosti najdete v popisu [rozhraní API služby Azure Maps Render](https://docs.microsoft.com/rest/api/maps/render).

### <a name="route-service"></a>Služba Route

Služba Route obsahuje robustní výpočty geometrie na základě reálné infrastruktury a trasy pro různé způsoby dopravy. Služba umožňuje vývojářům vypočítat trasy v několika různých režimech, např. auto, nákladní vůz, kolo nebo chůze. Služba také dokáže zohlednit zadání, jako je aktuální provoz, omezení hmotnosti nebo doprava nebezpečných látek.

![Azure Maps Route.png](media/about-azure-maps/Introduction_Route.png)

Služba Route teď nabízí pokročilé funkce ve verzi Preview, jako je dávkové zpracování požadavků na více tras, matice doby trvání cesty a vzdálenosti mezi sadou počátečních a cílových destinací a hledání tras nebo vzdáleností, které můžete ujet na základě vašich požadavků na dobu nebo spotřebu paliva. Podrobnosti o možnostech hledání tras najdete v popisu [rozhraní API služby Azure Maps Route](https://docs.microsoft.com/rest/api/maps/route).

### <a name="search-service"></a>Služba Search

Služba Search umožňuje vývojářům vyhledávat adresy, místa a výpisy firem podle názvu nebo kategorie a jiných zeměpisných údajů. Služba Search může také [reverzně geokódovat](https://en.wikipedia.org/wiki/Reverse_geocoding) adresy a křižovatky na základě zeměpisných souřadnic.

![Azure Maps Search.png](media/about-azure-maps/Introduction_Search.png)

Služba Search poskytuje také pokročilé funkce, jako je hledání podél trasy, hledání v širší oblasti, dávkové zpracování více požadavků na hledání a také hledání větší oblasti namísto pozičního bodu. Rozhraní API pro dávkové hledání a hledání oblastí jsou aktuálně ve verzi Preview. Další informace o možnostech hledání najdete v popisu [rozhraní API služby Azure Maps Search](https://docs.microsoft.com/rest/api/maps/search).

### <a name="time-zone-service"></a>Služba Time Zone

Služba Time Zone umožňuje získávat aktuální, historické i budoucí informace související s časovými pásmy buď podle zeměpisné šířky a délky, nebo [ID organizace IANA](https://www.iana.org/). Služba časových pásem také umožňuje převod ID časových pásem Microsoft Windows na ID organizace IANA, zjištění rozdílu časového pásma od UTC a získání aktuálního času v zadaném časovém pásmu. Typická odpověď JSON na dotaz na službu Time Zone Service vypadá jako v následující ukázce:

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

Podrobnosti o této službě najdete v popisu [rozhraní API služby Azure Maps Timezone](https://docs.microsoft.com/rest/api/maps/timezone).

### <a name="traffic-service"></a>Služba Traffic

Služba Traffic je sada webových služeb určená pro vývojáře, kteří ve svých webových nebo mobilních aplikacích potřebují informace o provozu. Služba nabízí dva datové typy:

* Aktuální provoz – v reálném čase zjištěné rychlosti a doby průjezdu pro všechny klíčové silnice sítě.
* Dopravní nehody – přesné informace o zablokované dopravě a o nehodách na silniční síti.

![Provoz v Azure Maps](media/about-azure-maps/Introduction_Traffic.png)

Další informace najdete na stránce [rozhraní API služby Azure Maps Traffic](https://docs.microsoft.com/rest/api/maps/traffic).

### <a name="ip-to-location"></a>IP to Location

Služba IP to Location umožňuje získat náhled načteného dvoupísmenného kódu země pro danou IP adresu. Tato služba vám pomůže přizpůsobit a vylepšit uživatelské prostředí tím, že využívá přizpůsobený obsah aplikace na základě zeměpisné polohy.

Informace o rozhraních REST API pro službu IP to Location najdete na stránce s [rozhraními Geolocation API služby Azure Maps](https://docs.microsoft.com/rest/api/maps/geolocation).

## <a name="programming-model"></a>Programovací model

Azure Maps podporuje mobilitu a může se využívat aplikacemi na různých platformách. Používá programovací model, který není závislý na jazyku, a podporuje výstup JSON prostřednictvím [rozhraní REST API](https://docs.microsoft.com/rest/api/maps/).

Kromě toho Azure Maps nabízí pohodlný [mapový ovládací prvek v JavaScriptu](https://docs.microsoft.com/javascript/api/azure-maps-control) s jednoduchým programovacím modelem pro rychlý a snadný vývoj webových i mobilních aplikací.

## <a name="usage"></a>Využití

Přístup ke službám Maps je otázkou přechodu na web [Azure Portal](https://portal.azure.com) a vytvoření účtu Azure Maps.

V Azure Maps se používá schéma ověřování založeného na klíčích. V účtu budou pro vás předem vygenerované dva klíče. Při zahájení integrace těchto polohových služeb do vaší aplikace můžete v požadavku na službu Azure Maps použít kterýkoli z obou klíčů.

## <a name="supported-regions"></a>Podporované oblasti

Rozhraní Azure Maps API jsou v současné době k dispozici ve všech zemích s následujícími výjimkami:

* Argentina
* Čína
* Indie
* Maroko
* Pákistán
* Jižní Korea

Zkontrolujte, že umístění aktuální IP adresy není v jedné z uvedených nepodporovaných zemí.

## <a name="next-steps"></a>Další postup

Další informace o nových funkcích Azure Maps:

> [!div class="nextstepaction"]
> [Matice tras, izochrony, vyhledávání IP adresy a další](https://azure.microsoft.com/blog/route-matrix-isochrones-ip-lookup-and-more-added-to-azure-maps/).

Pokračujte vyzkoušením ukázkové aplikace, která službu předvádí:

> [!div class="nextstepaction"]
> [Spuštění ukázky interaktivního hledání v mapách](quick-demo-map-app.md)
