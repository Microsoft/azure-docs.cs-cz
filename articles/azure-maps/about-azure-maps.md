---
title: Přehled Azure Maps | Microsoft Docs
description: Seznámení s Azure Maps
author: dsk-2015
ms.author: dkshir
ms.date: 07/12/2018
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 803e82a294b64452ffd788880097b9d86ac1065b
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2018
ms.locfileid: "42745286"
---
# <a name="what-is-azure-maps"></a>Co je služba Azure Maps?
Azure Maps je kolekce geoprostorových služeb založená na čerstvých mapových datech, takže můžete pro své webové a mobilní aplikace poskytovat přesný geografický kontext. Obsahuje rozhraní REST API pro vykreslování map, hledání bodů zájmu a tras k nim, dopravní informace, časová pásma a služby převodu IP adresy na polohu. Tato rozhraní API můžete používat se známými nástroji k rychlému vývoji a škálování řešení, která integrují informace o poloze do vašich řešení v Azure. Spolu s rozhraními REST API poskytuje webové ovládání v JavaScriptu pro zajištění snadného, flexibilního a přenositelného vývoje napříč různými médii. 

Následující video vysvětluje Azure Maps do hloubky:

<iframe src="https://channel9.msdn.com/Shows/Azure-Friday/Azure-Location-Based-Services/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

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

Služba Time Zone umožňuje získávat aktuální, historické i budoucí informace související s časovými pásmy buď podle zeměpisné šířky a délky, nebo [ID organizace IANA](http://www.iana.org/). Služba časových pásem také umožňuje převod ID časových pásem Microsoft Windows na ID organizace IANA, zjištění rozdílu časového pásma od UTC a získání aktuálního času v zadaném časovém pásmu. Typická odpověď JSON na dotaz na službu Time Zone Service vypadá jako v následující ukázce:

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

Služba IP to Location ve verzi Preview umožňuje získat dvoupísmenný kód země pro danou IP adresu. Tato služba pomáhá přizpůsobit aplikaci speciálním geopolitickým omezením a zlepšit prostředí pro uživatele úpravou obsahu aplikace na základě geografické polohy. 

Informace o rozhraních REST API pro službu IP to Location najdete na stránce s [rozhraními Geolocation API služby Azure Maps](https://docs.microsoft.com/rest/api/maps/geolocation).

## <a name="programming-model"></a>Programovací model

Azure Maps podporuje mobilitu a může se využívat aplikacemi na různých platformách. Používá programovací model, který není závislý na jazyku, a podporuje výstup JSON prostřednictvím [rozhraní REST API](https://docs.microsoft.com/rest/api/maps/). 

Kromě toho Azure Maps nabízí pohodlný [mapový ovládací prvek v JavaScriptu](https://docs.microsoft.com/javascript/api/azure-maps-control/models?view=azure-iot-typescript-latest) s jednoduchým programovacím modelem pro rychlý a snadný vývoj webových i mobilních aplikací. 


## <a name="usage"></a>Využití

Přístup ke službám Maps je otázkou přechodu na web [Azure Portal](http://portal.azure.com) a vytvoření účtu Azure Maps. 

V Azure Maps se používá schéma ověřování založeného na klíčích. V účtu budou pro vás předem vygenerované dva klíče. Při zahájení integrace těchto polohových služeb do vaší aplikace můžete v požadavcích na službu Azure Maps použít kterýkoli z obou klíčů.

## <a name="supported-regions"></a>Podporované oblasti
Rozhraní Azure Maps API jsou v současné době k dispozici ve všech zemích s následujícími výjimkami: 

* Argentina
* Čína
* Indie
* Maroko
* Pákistán
* Jižní Korea

Zkontrolujte aktuální IP adresu a ověřte, že umístění této IP adresy není v jedné z uvedených nepodporovaných zemí.

## <a name="next-steps"></a>Další kroky

- Další informace o nových funkcích Azure Maps: 
    - [Matice tras, izochrony, vyhledávání IP adresy a další](https://azure.microsoft.com/blog/route-matrix-isochrones-ip-lookup-and-more-added-to-azure-maps/). 
- Pokračujte vyzkoušením ukázkové aplikace, která službu předvádí.
    - [Spuštění ukázky interaktivního hledání v mapách](quick-demo-map-app.md)
