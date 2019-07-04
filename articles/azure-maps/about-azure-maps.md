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
ms.openlocfilehash: 5f41263113568cf9f3771119135be8db37119181
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442946"
---
# <a name="what-is-azure-maps"></a>Co je služba Azure Maps?

Azure Maps je kolekce geoprostorových služeb, které data čerstvými používat k poskytování přesných geografického kontextu pro webové a mobilní aplikace. Služba Azure Maps poskytuje:

* Rozhraní REST API pro vykreslení mapy v několika styly a v satelitních snímků.
* Vyhledá adresy, místa a body zájmu po celém světě.
* Směrování typu point-to-point, multipoint, multipoint optimalizace, isochrone, komerční vozidla, provoz k nim a matice směrování. zobrazení toku provozu a incidentů.
* Služby mobility pro vyžádání veřejné přenosu tak alternativní režimy dopravy (jako je sdílení kol, scooter sdílené složky a sdílení auta) a plánování tras v reálném čase. 
* Vytvoření polohu uživatele přes informace o zeměpisné poloze a převod umístění na časová pásma. 
* Služby pro monitorování geografických zón a mapování úložiště dat, se informace o poloze, které jsou hostované v Azure. 
* Informací o umístění prostřednictvím geoprostorové analýzy. 

Kromě rozhraní REST API jsou k dispozici prostřednictvím Android SDK nebo sady SDK webové služby Azure Maps. Tyto nástroje pomáhají vývojářům rychle vyvíjet a škálovat řešení schopná integrovat informace o poloze do řešení Azure. 

Můžete se zaregistrovat pro bezplatnou [účet Azure Maps](https://azure.microsoft.com/services/azure-maps/) a začít s vývojem.

Následující video vysvětluje Azure Maps do hloubky:

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>Ovládací prvky mapy

### <a name="web-sdk"></a>Sada Web SDK

Azure Maps Web SDK umožňuje přizpůsobit interaktivní mapy s vlastním obsahem a obrázek pro zobrazení ve vašich webových nebo mobilních aplikací. Tento ovládací prvek využívá WebGL, takže je může mít za následek velké datové sady s vysokým výkonem. Vývoj pomocí sady SDK pomocí jazyka JavaScript nebo TypeScript.

![Ukázková mapa plnění změnit](media/about-azure-maps/Introduction_WebMapControl.png)

### <a name="android-sdk"></a>Android SDK

Použití Azure Maps Android SDK k vytváření aplikací pro mobilní mapování. 

![Příklady mapování na mobilním zařízení](media/about-azure-maps/AndroidSDK.png)

## <a name="services-in-azure-maps"></a>Služby v rámci Azure Maps

Azure Maps se skládá z následujících devět služeb, které můžete k zajištění geografického kontextu pro aplikace Azure.

### <a name="data-service"></a>Datová služba

Data jsou dnešní pro mapy. Pomocí služby Data nahrávat a ukládat geoprostorových dat pro použití s prostorových operace nebo bitové kopie sestavení.  Přenesení zákaznická data blíž ke službě Azure Maps bude snížit latenci, zvýšit produktivitu a vytvořit nové scénáře ve svých aplikacích. Podrobnosti o této službě najdete v článku [dokumentace k rozhraní API datové služby](https://docs.microsoft.com/rest/api/maps/data).

### <a name="mobility-service"></a>Služba Mobility

Služba Azure Maps Mobility umožňuje plánování cest v reálném čase. Vrátí nejlepší možnosti možné trasy a poskytuje celou řadu režimech. Pro oblasti metro (Město) můžete zahrnout tyto režimy walking kole a veřejné přenosu. Vývojáři můžou požádat o přenosu itineráře podrobnosti jako řádek geometrie, seznamy zarážek, naplánovat a v reálném čase doručení a upozornění služby.

Služba také umožňuje vyhledávání pro konkrétní objekt typy, jako jsou sdílené kola, skútry nebo auta kolem umístění. Uživatelé mohou požadovat, kolik k dispozici sdílené kol jsou ponechána na nejbližší ukotvení. Mohou vyhledávání k dispozici Auto share vozidel a podrobnosti jako budoucí dostupnosti a aktuální úroveň posílit najít.

Další informace o službě, najdete v článku [dokumentace k rozhraní API Mobility](https://docs.microsoft.com/rest/api/maps/mobility).

### <a name="render-service"></a>Služba Render

Render service pomáhá vývojářům vytvářet webové a mobilní mapové aplikace. Služba nabízí buď vysoce kvalitní rastrové podklady v 19 úrovních přiblížení, nebo plně přizpůsobitelné vektorové obrazy.

![Příklad mapování z Render service](media/about-azure-maps/Introduction_Map.png)

Služba Render teď nabízí rozhraní API ve verzi Preview, která umožňují vývojářům pracovat se satelitními snímky. Další podrobnosti najdete v článku [Render API dokumentaci](https://docs.microsoft.com/rest/api/maps/render).

### <a name="route-service"></a>Služba Route

Služba Route obsahuje robustní výpočty geometrie na základě reálné infrastruktury a trasy pro různé způsoby dopravy. Služba umožňuje vývojářům vypočítat trasy v několika různých režimech, např. auto, nákladní vůz, kolo nebo chůze. Služba také dokáže zohlednit zadání, jako je aktuální provoz, omezení hmotnosti nebo doprava nebezpečných látek.

![Příklad objektu map z rozhraní API Route service](media/about-azure-maps/Introduction_Route.png)

Rozhraní API Route service nabízí náhled pokročilé funkce, jako například: 

* Dávkové zpracování více směrování požadavků.
* Matice z přenosu čas a vzdálenost mezi sadu zdrojů a cílů.
* Hledání trasy nebo vzdálenosti, které uživatelé mohou projít podle času nebo posílit požadavky. 

Podrobnosti o možnosti přesměrování, najdete v článku [dokumentace k rozhraní API Route](https://docs.microsoft.com/rest/api/maps/route).

### <a name="search-service"></a>Služba Search

Vyhledávací služba pomáhá vývojářům vyhledávat adresy, místa, výpisy firem podle názvu nebo kategorie a jiných zeměpisných údajů. Vyhledávací služba může [geokódovat](https://en.wikipedia.org/wiki/Reverse_geocoding) adresy a křižovatky podle zeměpisná šířka a délka.

![Příklad hledání na mapě](media/about-azure-maps/Introduction_Search.png)

Služba vyhledávání poskytuje také pokročilé funkce, jako například:

* Hledat trase.
* Hledat v širší oblasti.
* Batch skupiny žádostí o hledání.
* Hledání větší oblast namísto umístění bodu. 

Rozhraní API pro dávkové hledání a hledání oblastí jsou aktuálně ve verzi Preview. Další informace o možnosti vyhledávání, najdete v článku [dokumentace k rozhraní API pro vyhledávání](https://docs.microsoft.com/rest/api/maps/search).

### <a name="spatial-operations-service"></a>Prostorový operací služby

Služba Azure Maps prostorových operace přebírá informace o umístění a analyzuje v reálném čase pomáhají informovat zákazníky probíhající události v čase a místo. Umožňuje téměř v reálném čase analýzy a prediktivnímu modelování událostí. 

Tato služba umožňuje zákazníky k vylepšení svých informací o umístění s knihovnou běžné geoprostorové matematických výpočtů, včetně co nejblíž koncovým bodem, delšími a vyrovnávací paměti. Další informace o službě a různé funkce, přečtěte si [dokumentace k rozhraní API prostorových operace](https://docs.microsoft.com/rest/api/maps/spatial).

### <a name="time-zone-service"></a>Služba Time Zone

Služba časových pásem vám umožní zadat dotaz na aktuální, historické i budoucí informace s použitím obou páry zeměpisné šířky a délky nebo [ID organizace IANA](https://www.iana.org/). Služba časových pásem také umožňuje:

* Převod ID časových pásem Microsoft Windows na IANA časová pásma.
* Načítání posun časového pásma UTC.
* Získávání aktuální čas v časovém pásmu. 

Typická odpověď JSON pro dotaz na službu časového pásma vypadá takto:

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

Podrobnosti o této službě najdete [dokumentace k rozhraní API časové pásmo](https://docs.microsoft.com/rest/api/maps/timezone).

### <a name="traffic-service"></a>Služba Traffic

Traffic service je sada webových služeb, které mohou vývojáři vytvářet webové a mobilní aplikace, které vyžadují informace o přenosech. Služba nabízí dva datové typy:

* Tok provozu: V reálném čase dodržovat rychlosti a doby trvání cesty pro všechny klíčové silnice sítě.
* Incidenty provozu: Aktuální přehled o nehodách a incidentů na silniční síti.

![Příklad objektu map s informacemi o provozu](media/about-azure-maps/Introduction_Traffic.png)

Další informace najdete v tématu [dokumentace k rozhraní API provoz](https://docs.microsoft.com/rest/api/maps/traffic).

### <a name="ip-to-location-service"></a>IP adresy k umístění služby

Použijte IP adresu pro umístění služby pro náhled kód načtený dvoupísmenné země pro IP adresu. Tuto službu můžete přizpůsobit a zlepšit uživatelské prostředí tím, že poskytuje obsahu vlastní aplikace na základě geografického umístění.

Další podrobnosti o rozhraní REST API pro IP adresy k umístění služby najdete v článku [dokumentace k rozhraní API služby Azure Maps informace o zeměpisné poloze](https://docs.microsoft.com/rest/api/maps/geolocation).

## <a name="programming-model"></a>Programovací model

Azure Maps je navržené pro mobilní a pomáhají při vývoji multiplatformních aplikací. Používá programovací model, který je nezávislý na jazyce a podporuje výstup JSON prostřednictvím [rozhraní REST API](https://docs.microsoft.com/rest/api/maps/).

Kromě toho Azure Maps nabízí pohodlný [mapový ovládací prvek v JavaScriptu](https://docs.microsoft.com/javascript/api/azure-maps-control) s jednoduchým programovacím modelem pro rychlý a snadný vývoj webových i mobilních aplikací.

## <a name="usage"></a>Využití

Přístup ke službám Azure Maps je otázkou přechodu do [webu Azure portal](https://portal.azure.com) a vytvoření účtu Azure Maps.

V Azure Maps se používá schéma ověřování založeného na klíčích. Váš účet součástí dva klíče, který již vygenerován. Při zahájení integrace těchto polohových služeb do vaší aplikace můžete v požadavku na službu Azure Maps použít kterýkoli z obou klíčů.

## <a name="supported-regions"></a>Podporované oblasti

Rozhraní API služby Azure Maps jsou aktuálně k dispozici ve všech zemích nebo oblastech, kromě těchto:

* Argentina
* Čína
* Indie
* Maroko
* Pákistán
* Jižní Korea

Ověřte, že umístění vaši aktuální IP adresu není v jedné z nepodporovaných zemích nebo oblastech.

## <a name="next-steps"></a>Další postup

Vyzkoušejte ukázkovou aplikaci, která představuje sadu Azure Maps:

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: Vytvoření webové aplikace](quick-demo-map-app.md)

Aktuální informace o Azure Maps: 

> [!div class="nextstepaction"]
> [Blog o Azure Maps](https://azure.microsoft.com/blog/topics/azure-maps/)
