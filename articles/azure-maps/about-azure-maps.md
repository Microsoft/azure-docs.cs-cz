---
title: Přehled | Mapy Microsoft Azure
description: V tomto článku se dozvíte o službách a funkcích v Microsoft Azure Maps a o tom, jak je používat ve svých aplikacích.
author: walsehgal
ms.author: v-musehg
ms.date: 02/04/2019
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 353850cacb06dcc8a0db1b957114db314d7002a3
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911836"
---
# <a name="what-is-azure-maps"></a>Co je služba Azure Maps?

Azure Maps je kolekce geoprostorových služeb, které používají čerstvá data mapování k zajištění přesného geografického kontextu pro webové a mobilní aplikace. Azure Maps poskytuje:

* Rozhraní REST API pro vykreslování map v několika stylech a v satelitních proobrázcích.
* Vyhledá adresy, místa a body zájmu po celém světě.
* Směrování Point-to-Point, MultiPoint, optimalizace systému MultiPoint, isochrone, komerční vozidlo, ovlivnění provozu a směrování matrice; zobrazení toku a incidentů provozu.
* Služby mobility pro požadavky na veřejný tranzit a alternativní režimy přepravy (například sdílení kol, Scooter sdílená složka a podíl automobilu) a naplánujte trasy v reálném čase. 
* Stanovení umístění uživatelů prostřednictvím geografického umístění a převod umístění na časová pásma. 
* Služby pro geografickou a mapovací úložiště dat s informacemi o poloze hostované v Azure. 
* Informace o poloze prostřednictvím geoprostorové analýzy. 

Kromě rozhraní REST API jsou Azure Maps služby k dispozici prostřednictvím webové sady SDK nebo Android SDK. Tyto nástroje umožňují vývojářům rychle vyvíjet a škálovat řešení, která integrují informace o poloze do řešení Azure. 

Můžete si zaregistrovat bezplatný [Azure Maps účet](https://azure.microsoft.com/services/azure-maps/) a začít vyvíjet.

Následující video vysvětluje Azure Maps do hloubky:

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>Ovládací prvky mapy

### <a name="web-sdk"></a>Sada Web SDK

Sada Azure Maps Web SDK umožňuje přizpůsobit interaktivní mapy s vlastním obsahem a pomocí obrázků pro zobrazení ve vašich webových nebo mobilních aplikacích. Tento ovládací prvek využívá WebGL, takže můžete vykreslit velké sady dat s vysokým výkonem. Vývoj pomocí sady SDK pomocí JavaScriptu nebo TypeScript

![Příklad mapy změny populace](media/about-azure-maps/Introduction_WebMapControl.png)

### <a name="android-sdk"></a>Android SDK

K vytváření aplikací pro mobilní mapování použijte Azure Maps Android SDK. 

![Příklady map na mobilním zařízení](media/about-azure-maps/AndroidSDK.png)

## <a name="services-in-azure-maps"></a>Služby v rámci Azure Maps

Azure Maps se skládá z následujících devíti služeb, které poskytují geografickou souvislost aplikacím Azure.

### <a name="data-service"></a>Datová služba

Data jsou pro mapy imperativní. Pomocí datové služby můžete nahrávat a ukládat geoprostorové data pro použití s prostorovou operací nebo kompozicí imagí.  Uvedení zákaznických dat blíže ke službě Azure Maps omezí latenci, zvýší produktivitu a vytvoří nové scénáře ve vašich aplikacích. Podrobnosti o této službě najdete v dokumentaci k [rozhraní API datové služby](https://docs.microsoft.com/rest/api/maps/data).

### <a name="mobility-service"></a>Služba Mobility

Služba mobility Azure Maps umožňuje plánování cest v reálném čase. Vrátí nejlepší možné možnosti směrování a poskytuje celou řadu cestovních režimů. Pro oblasti metro (City) můžou tyto režimy zahrnovat procházení, cyklistice a veřejný tranzit. Vývojáři mohou požádat o přenosové údaje itineráře, jako je geometrie řádku, seznamy zastavení, plánované doručení a oznámení v reálném čase a výstrahy služby.

Služba také umožňuje vyhledávat konkrétní typy objektů, jako jsou například sdílená kola, Scooters nebo automobily kolem umístění. Uživatelé si můžou vyžádat, kolik dostupných sdílených kol zůstane v nejbližším Docku. Můžou vyhledat dostupná vozidla pro sdílení auta a najít podrobnosti, jako je budoucí dostupnost a současná úroveň paliva.

Další informace o této službě najdete v [dokumentaci k rozhraní API mobility](https://docs.microsoft.com/rest/api/maps/mobility).

### <a name="render-service"></a>Služba Render

Služba vykreslování pomáhá vývojářům vytvářet webové a mobilní aplikace kolem mapování. Služba nabízí buď vysoce kvalitní rastrové podklady v 19 úrovních přiblížení, nebo plně přizpůsobitelné vektorové obrazy.

![Příklad mapy ze služby vykreslování](media/about-azure-maps/Introduction_Map.png)

Služba Render teď nabízí rozhraní API ve verzi Preview, která umožňují vývojářům pracovat se satelitními snímky. Další podrobnosti najdete v [dokumentaci k rozhraní API pro vykreslování](https://docs.microsoft.com/rest/api/maps/render).

### <a name="route-service"></a>Služba Route

Služba Route obsahuje robustní výpočty geometrie na základě reálné infrastruktury a trasy pro různé způsoby dopravy. Služba umožňuje vývojářům vypočítat trasy v několika různých režimech, např. auto, nákladní vůz, kolo nebo chůze. Služba také dokáže zohlednit zadání, jako je aktuální provoz, omezení hmotnosti nebo doprava nebezpečných látek.

![Příklad mapy ze služby směrování](media/about-azure-maps/Introduction_Route.png)

Služba směrování nabízí verzi Preview pokročilých funkcí, jako například: 

* Dávkové zpracování více požadavků na směrování.
* Matice doby cesty a vzdálenosti mezi sadou zdrojů a míst určení.
* Hledání cest nebo vzdáleností, které mohou uživatelé cestovat na základě požadavků na čas nebo pohon. 

Podrobnosti o možnostech směrování najdete v [dokumentaci k rozhraní API pro směrování](https://docs.microsoft.com/rest/api/maps/route).

### <a name="search-service"></a>Služba Search

Vyhledávací služba pomáhá vývojářům vyhledávat adresy, místa, obchodní výpisy podle názvu nebo kategorie a dalších geografických informací. Služba vyhledávání může [přesměrovat](https://en.wikipedia.org/wiki/Reverse_geocoding) adresy zeměpisných a meziulic v závislosti na Latitudes a zeměpisných délkách.

![Příklad hledání na mapě](media/about-azure-maps/Introduction_Search.png)

Vyhledávací služba také poskytuje pokročilé funkce, jako například:

* Hledání v trase.
* Hledejte v širší oblasti.
* Dávkovat skupinu žádostí o hledání.
* Místo bodu umístění vyhledejte větší oblast. 

Rozhraní API pro dávkové hledání a hledání oblastí jsou aktuálně ve verzi Preview. Další podrobnosti o možnostech vyhledávání najdete v [dokumentaci rozhraní API pro hledání](https://docs.microsoft.com/rest/api/maps/search).

### <a name="spatial-operations-service"></a>Služba prostorových operací

Služba Azure Maps prostorových operací používá informace o poloze a analyzuje je, aby pomohla informovat zákazníky o probíhajících událostech v čase a prostoru. Umožňuje analýzu téměř v reálném čase a prediktivní modelování událostí. 

Služba umožňuje zákazníkům vylepšit své lokátory umístění pomocí knihovny běžných geoprostorovéch matematických výpočtů, včetně nejbližšího bodu, skvělého kruhu a vyrovnávací paměti. Další informace o službě a různých funkcích najdete v [dokumentaci rozhraní API pro prostorové operace](https://docs.microsoft.com/rest/api/maps/spatial).

### <a name="time-zone-service"></a>Služba Time Zone

Služba časového pásma vám umožní dotazovat se na aktuální, historické a budoucí informace o časovém pásmu pomocí párů Zeměpisná šířka a délka nebo [ID IANA](https://www.iana.org/). Služba časového pásma také umožňuje:

* Převod ID časových pásem Microsoft Windows na časová pásma IANA.
* Načítají se posunutí časového pásma na čas UTC.
* Získání aktuálního času v časovém pásmu. 

Typická odpověď JSON pro dotaz na službu časového pásma vypadá jako v následující ukázce:

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

Podrobnosti o této službě najdete v [dokumentaci k rozhraní API pro časové pásmo](https://docs.microsoft.com/rest/api/maps/timezone).

### <a name="traffic-service"></a>Služba Traffic

Přenosová služba je sada webových služeb, které můžou vývojáři použít k vytváření webových a mobilních aplikací, které vyžadují informace o provozu. Služba nabízí dva datové typy:

* Tok přenosů: zaznamenané rychlosti v reálném čase a doby provozu pro všechny klíčové cesty v síti.
* Incidenty provozu: aktuální přehled o zaseknutí a incidentech provozu kolem sítě v provozu.

![Příklad mapy s informacemi o provozu](media/about-azure-maps/Introduction_Traffic.png)

Další informace najdete v [dokumentaci rozhraní API pro přenos](https://docs.microsoft.com/rest/api/maps/traffic).

### <a name="ip-to-location-service"></a>IP adresa – služba zjišťování polohy

Pomocí služby IP to Location Service můžete zobrazit náhled načteného kódu země pro IP adresu. Tato služba vám může přispět k přizpůsobení a vylepšení uživatelského prostředí tím, že poskytuje přizpůsobený obsah aplikace v závislosti na geografickém umístění.

Další podrobnosti o rozhraních REST API pro službu IP na umístění najdete v dokumentaci k [rozhraní API](https://docs.microsoft.com/rest/api/maps/geolocation)geografického umístění v Azure Maps.

## <a name="programming-model"></a>Programovací model

Azure Maps je postavená na mobilitu a může vám pomáhat vyvíjet aplikace pro různé platformy. Používá programovací model, který je jazyk nezávislá a podporuje výstup JSON prostřednictvím [rozhraní REST API](https://docs.microsoft.com/rest/api/maps/).

Kromě toho Azure Maps nabízí pohodlný [mapový ovládací prvek v JavaScriptu](https://docs.microsoft.com/javascript/api/azure-maps-control) s jednoduchým programovacím modelem pro rychlý a snadný vývoj webových i mobilních aplikací.

## <a name="usage"></a>Využití

Přístup ke službám Azure Maps Services je podstatou [Azure Portal](https://portal.azure.com) a vytvořením účtu Azure Maps.

V Azure Maps se používá schéma ověřování založeného na klíčích. Váš účet obsahuje dva klíče, které jsou už vygenerované za vás. Při zahájení integrace těchto polohových služeb do vaší aplikace můžete v požadavku na službu Azure Maps použít kterýkoli z obou klíčů.

## <a name="supported-regions"></a>Podporované oblasti

Rozhraní API pro Azure Maps jsou aktuálně k dispozici ve všech zemích nebo oblastech s výjimkou těchto:

* Čína
* Jižní Korea

Zkontrolujte, že umístění aktuální IP adresy není v jedné z uvedených nepodporovaných zemí.

## <a name="next-steps"></a>Další kroky

Vyzkoušejte si ukázkovou aplikaci, která prezentuje Azure Maps:

> [!div class="nextstepaction"]
> [Rychlý Start: Vytvoření webové aplikace](quick-demo-map-app.md)

Udržujte si přehled o Azure Maps: 

> [!div class="nextstepaction"]
> [Blog Azure Maps](https://azure.microsoft.com/blog/topics/azure-maps/)
