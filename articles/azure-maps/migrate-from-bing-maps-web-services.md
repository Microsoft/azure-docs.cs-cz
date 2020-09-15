---
title: 'Kurz: Migrace webových služeb z map Bingu | Mapy Microsoft Azure'
description: Jak migrovat webové služby z map Bing na Microsoft Azure Maps
author: rbrundritt
ms.author: richbrun
ms.date: 9/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 4520332fbc5040aff682ce52e819fa4a940999cc
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90108685"
---
# <a name="migrate-web-service-from-bing-maps"></a>Migrace webové služby z map Bing

Azure i mapy Bing poskytují přístup k prostorovým rozhraním API prostřednictvím webových služeb REST. Rozhraní API pro tyto platformy provádějí podobné funkce, ale používají různé konvence pojmenování a objekty odpovědí.

Následující tabulka poskytuje rozhraní API služby Azure Maps, která poskytují podobné funkce pro uvedená rozhraní API služby Bing Maps.

| Rozhraní API služby mapy Bing                 | Rozhraní API služby Azure Maps      |
|---------------------------------------|-----------------------------|
| Automatické návrhy                           | [Hledání](https://docs.microsoft.com/rest/api/maps/search)     |
| Pokyny (včetně nákladní auto)          | [Směr směrování](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)                          |
| Matice vzdáleností                       | [Matice směrování](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)                          |
| Obrázek – statická mapa                  | [Vykreslování](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                                   |
| Izochronů                            | [Rozsah trasy](https://docs.microsoft.com/rest/api/maps/route/getrouterange)                                    |
| Místní přehledy                        | [Hledání](https://docs.microsoft.com/rest/api/maps/search)  +  [Rozsah trasy](https://docs.microsoft.com/rest/api/maps/route/getrouterange)    |
| Místní hledání                          | [Hledání](https://docs.microsoft.com/rest/api/maps/search)     |
| Rozpoznávání polohy (zájmu)           | [Hledání](https://docs.microsoft.com/rest/api/maps/search)     |
| Umístění (dopředného nebo zpětného geografického kódování) | [Hledání](https://docs.microsoft.com/rest/api/maps/search)                                               |
| Přichycení k cestám                          | [Pokyny pro odeslání trasy](https://docs.microsoft.com/rest/api/maps/route/postroutedirections)                         |
| Prostorové Data Services (SDS)           | [Hledání](https://docs.microsoft.com/rest/api/maps/search)  +  [Směrování](https://docs.microsoft.com/rest/api/maps/route) a další služby Azure |
| Časové pásmo                             | [Časové pásmo](https://docs.microsoft.com/rest/api/maps/timezone)  |
| Incidenty provozu                     | [Podrobnosti o incidentu provozu](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficincidentdetail)                     |

Následující rozhraní API služby nejsou v tuto chvíli k dispozici v Azure Maps:

-   Zvýšení oprávnění – plánováno
-   Optimalizované trasy trasy – plánováno. Rozhraní API pro Azure Maps tras podporuje salesmen optimalizaci pro jedno vozidlo.
-   Metadata z snímků – primárně se používají pro získání adres URL dlaždic v mapách Bing. Azure Maps má samostatnou službu pro přímý přístup k dlaždicím mapy.

Azure Maps má několik dalších webových služeb REST, které mohou být zajímavé;

-   [Azure Maps Creator](https://docs.microsoft.com/azure/azure-maps/creator-indoor-maps) – vytvořte si vlastní privátní digitální vlákna pro budovy a prostory.
-   [Prostorové operace](https://docs.microsoft.com/rest/api/maps/spatial) – přesměrovat složité prostorové výpočty a operace, jako je monitorování geografických zón, do služby.
-   [Dlaždice mapy](https://docs.microsoft.com/rest/api/maps/render/getmaptile) – přístup k dlaždicím cest a snímků z Azure Maps jako rastrové a vektorové dlaždice.
-   [Směrování Batch](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview) – umožňuje v jedné dávce provést až 1 000 požadavků na směrování za určitou dobu. Trasy jsou při rychlejším zpracování vypočítány paralelně na serveru.
-   [Přenos dat](https://docs.microsoft.com/rest/api/maps/traffic) Flow – přístup k datům toku provozu v reálném čase jako rastrové i vektorové dlaždice.
-   [Rozhraní API pro geografickou polohu](https://docs.microsoft.com/rest/api/maps/geolocation/getiptolocationpreview) – Získá umístění IP adresy.
-   [Počasí Services](https://docs.microsoft.com/rest/api/maps/weather) – získáte přístup k datům počasí v reálném čase a předpovědi.

Nezapomeňte si také projít následující Příručky k osvědčeným postupům:

-   [Osvědčené postupy pro hledání](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search)
-   [Osvědčené postupy pro směrování](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-routing)

## <a name="geocoding-addresses"></a>Adresy geografického kódování

Geografické kódování je proces převodu adresy (jako `"1 Microsoft way, Redmond, WA"` ) na souřadnici (například zeměpisná délka:-122,1298, zeměpisná šířka: 47,64005). Souřadnice se pak často používají k umístění připínáčku na mapě nebo na střed mapy.

Azure Maps poskytuje několik metod pro adresy geografického kódování;

-   [Geografické kódování adresy volného formátu](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress): zadejte jeden adresní řetězec (například `"1 Microsoft way, Redmond, WA"` ) a okamžitě zpracovat požadavek. Tato služba se doporučuje v případě, že potřebujete jednotlivé adresy snadno kódovat.
-   [Geografické označování strukturovaných adres](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured): Určete části jedné adresy, jako je název ulice, město, země a poštovní směrovací číslo a okamžitě zpracujte požadavek. Tato služba je doporučena v případě, že je nutné rychle kódovat jednotlivé adresy a data jsou již analyzována do jednotlivých částí adres.
-   [Geografické kódování adresy Batch](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview): vytvořte žádost obsahující až 10 000 adres a požádejte je o zpracování v časovém intervalu. Všechny adresy budou na serveru paralelně zavedeny a po dokončení bude možné stáhnout úplnou sadu výsledků. Tato služba se doporučuje pro geografické datové sady s velkými kódováními.
-   [Hledání přibližných](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)hodnot: Toto rozhraní API kombinuje geografické kódování s bodem hledání z hlediska zájmu. Toto rozhraní API přebírá řetězec ve volném formátu, který může být adresami, místem, orientačními body, bodem zájmu nebo kategorií zájmu a okamžitě zpracovat požadavek. Toto rozhraní API se doporučuje u aplikací, kde můžou uživatelé vyhledat adresy nebo body zájmu ze stejného textového pole.
-   [Nepřibližné dávkové vyhledávání](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): vytvoření žádosti obsahující až 10 000 adres, míst, orientačních bodů nebo bodů zájmu a jejich zpracování v časovém intervalu. Všechna data budou zpracována paralelně na serveru a po dokončení bude možné stáhnout úplnou sadu výsledků.

Následující tabulky křížově odkazují na parametry rozhraní API služby mapy Bing s podobnými parametry rozhraní API v Azure Maps pro strukturované a volné adresní označení.

**Umístění podle adresy (strukturovaná adresa)**

| Parametr rozhraní API pro mapy Bing              | Srovnatelný parametr Azure Maps rozhraní API                 |
|--------------------------------------|-----------------------------------------------------|
| `addressLine`                      | `streetNumber`, `streetName` nebo `crossStreet` |
| `adminDistrict`                    | `countrySubdivision`                              |
| `countryRegion`                    | `country` a `countryCode`                     |
| `locality`                         | `municipality` nebo `municipalitySubdivision`     |
| `postalCode`                       | `postalCode`                                      |
| `maxResults` (`maxRes`)          | `limit`                                           |
| `includeNeighborhood` (`inclnb`) | Není k dispozici – vždy vracené Azure Maps, pokud je k dispozici.   |
| `include` (`incl`)               | N/A – ISO2 kód země se vždycky vrátí Azure Maps. |
| `key`                              | `subscription-key` – Viz také [ověřování pomocí Azure Maps](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication) dokumentaci. |
| `culture` (`c`)                  | `language` – Viz dokumentace k [podporovaným jazykům](https://docs.microsoft.com/azure/azure-maps/supported-languages) . |
| `userRegion` (`ur`)              | `view` – Viz dokumentace k [podporovaným zobrazením](https://aka.ms/AzureMapsLocalizationViews) . |

Azure Maps také podporuje;

-   `countrySecondarySubdivision` – Okres, oblasti
-   `countryTertiarySubdivision` -Pojmenované oblasti; boroughs, kantony, obce
-   `ofs` – Stránkou výsledků v kombinaci s `maxResults` parametrem.

**Umístění podle dotazu (řetězec adresy ve volném formátu)**

| Parametr rozhraní API pro mapy Bing              | Srovnatelný parametr Azure Maps rozhraní API      |
|--------------------------------------|------------------------------------------|
| `query`                            | `query`                                |
| `maxResults` (`maxRes`)          | `limit`                                |
| `includeNeighborhood` (`inclnb`) | Není k dispozici – vždy vracené Azure Maps, pokud je k dispozici.  |
| `include` (`incl`)               | N/A – ISO2 kód země se vždycky vrátí Azure Maps.  |
| `key`                              | `subscription-key` – Viz také [ověřování pomocí Azure Maps](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication) dokumentaci. |
| `culture` (`c`)                  | `language` – Viz dokumentace k [podporovaným jazykům](https://docs.microsoft.com/azure/azure-maps/supported-languages) .  |
| `userRegion` (`ur`)              | `view` – Viz dokumentace k [podporovaným zobrazením](https://aka.ms/AzureMapsLocalizationViews) . |

Azure Maps také podporuje;

-   `typeahead` -Druh, pokud se dotaz interpretuje jako částečný vstup, a hledání se zahájí v prediktivním režimu (Automatický návrh/automatické dokončování).
-   `countrySet` – Čárkami oddělený seznam ISO2 zemí, ve kterých se má omezit hledání na.
-   `lat`/`lon`, `topLeft` / `btmRight` ,, `radius` – Zadání umístění uživatele a oblasti, aby se výsledky podrobnější.
-   `ofs` – Stránkou výsledků v kombinaci s `maxResults` parametrem.

Příklad použití vyhledávací služby je popsán [zde](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address). Nezapomeňte si projít [osvědčené postupy pro vyhledávání](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search) v dokumentaci.

## <a name="reverse-geocode-a-coordinate-find-a-location-by-point"></a>Obrátit zeměpisnou souřadnici souřadnice (najít umístění podle bodu)

Inverzní geografické kódování je proces převodu geografických souřadnic (jako Zeměpisná délka:-122,1298, zeměpisná šířka: 47,64005) na jeho přibližnou adresu (například `"1 Microsoft way, Redmond, WA"` ).

Azure Maps poskytuje několik metod reverzního geografického kódování;

-   [Adresa reverzního INCODE](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse): Určete jednu geografickou souřadnici, která získá přibližnou adresu a okamžitě zpracuje požadavek.
-   [INCODE Reverse pro meziulici](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet): Určete jednu geografickou souřadnici, která má být v blízkosti informací o meziulici (například 1 & Main), a okamžitě zpracovat požadavek.
-   [Adresa dávkového reverzního příznaku zpětného vyhledávání](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview): vytvořte žádost obsahující až 10 000 souřadnic a požádejte je o zpracování v časovém intervalu. Všechna data budou zpracována paralelně na serveru a po dokončení bude možné stáhnout úplnou sadu výsledků.

Následující tabulka odkazuje na parametry rozhraní API služby Bing Maps pomocí srovnatelných parametrů rozhraní API v Azure Maps.

| Parametr rozhraní API pro mapy Bing              | Srovnatelný parametr Azure Maps rozhraní API       |
|--------------------------------------|-------------------------------------------|
| `point`                              | `query`                                   |
| `includeEntityTypes`                 | `entityType` – Viz tabulka porovnání typů entit níže.    |
| `includeNeighborhood` (`inclnb`)     | Není k dispozici – vždy vracené Azure Maps, pokud je k dispozici.         |
| `include` (`incl`)                   | N/A – ISO2 kód země se vždycky vrátí Azure Maps.    |
| `key`                                | `subscription-key` – Viz také [ověřování pomocí Azure Maps](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication) dokumentaci. |
| `culture` (`c`)                      | `language` – Viz dokumentace k [podporovaným jazykům](https://docs.microsoft.com/azure/azure-maps/supported-languages) .   |
| `userRegion` (`ur`)                  | `view` – Viz dokumentace k [podporovaným zobrazením](https://aka.ms/AzureMapsLocalizationViews) . |

Nezapomeňte si projít [osvědčené postupy pro vyhledávání](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search) v dokumentaci.

Rozhraní API pro invertování Azure Maps obsahuje některé další funkce, které nejsou k dispozici ve službě Bing Maps, které by mohly být užitečné při migraci aplikace:

-   Načte data omezení rychlosti.
-   Načíst informace o využití cest; místní silnice, Arterial, omezený přístup, rampa atd.
-   Strana ulice, na kterou souřadnice spadá.

**Srovnávací tabulka typů entit**

Následující tabulka odkazuje na hodnoty typu entity mapy Bing na ekvivalentní názvy vlastností v Azure Maps.

| Typ entity mapy Bing | Srovnatelný Azure Maps typ entity               | Popis                                |
|-----------------------|-------------------------------------------------|--------------------------------------------|
| `Address`             |                                                 | *Adresa*                                  |
| `Neighborhood`        | `Neighbourhood`                                 | *Včetně*                             |
| `PopulatedPlace`      | `Municipality` nebo `MunicipalitySubdivision`     | *Město*, města *nebo sub*, nebo *super City*     |
| `Postcode1`           | `PostalCodeArea`                                | *Poštovní* *směrovací číslo nebo PSČ*                |
| `AdminDivision1`      | `CountrySubdivision`                            | *Stát* nebo *provincie*                      |
| `AdminDivision2`      | `CountrySecondarySubdivison`                    | *Okresy* nebo *oblasti*                    |
| `CountryRegion`       | `Country`                                       | *Název země*                             |
|                       | `CountryTertiarySubdivision`                    | *Boroughs*, *kantony*, *obce*          |

## <a name="get-location-suggestions-autosuggest"></a>Získat návrhy umístění (automatické návrhy)

Některé z prediktivního režimu podpory rozhraní API pro hledání Azure Maps, které je možné použít pro scénáře automatických návrhů. Rozhraní API pro automatické návrhy map Bingu je podobně jako rozhraní API pro automatické návrhy Azure Maps pro [hledání přibližných](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) funkcí. Následující rozhraní API podporuje také prediktivní režim, přidávání `&typeahead=true` do dotazu;

-   [Geografické kódování adresy volného formátu](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress): zadejte jeden adresní řetězec (například `"1 Microsoft way, Redmond, WA"` ) a okamžitě zpracovat požadavek. Tato služba se doporučuje v případě, že potřebujete jednotlivé adresy snadno kódovat.
-   [Hledání přibližných](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)hodnot: Toto rozhraní API kombinuje geografické kódování s bodem hledání z hlediska zájmu. Toto rozhraní API přebírá řetězec ve volném formátu, který může být adresami, místem, orientačními body, bodem zájmu nebo kategorií zájmu a okamžitě zpracovat požadavek. Toto rozhraní API se doporučuje u aplikací, kde můžou uživatelé vyhledat adresy nebo body zájmu ze stejného textového pole.
-   [Hledání POI](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi): vyhledejte body zájmů podle jména. Například; `"starbucks"`.
-   [Hledání kategorií POI](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory): vyhledejte body zájmu podle kategorie. Například; "restaurace".

## <a name="calculate-routes-and-directions"></a>Vypočítat trasy a směry

Azure Maps lze použít k výpočtu tras a směrů. Azure Maps má mnoho stejných funkcí jako směrovací službu mapy Bing, jako je například;

-   časy doručení a odchodu
-   trasy provozu v reálném čase a prediktivní provoz na bázi
-   různé režimy dopravy; jízda, navýšení, nákladní vůz
-   Optimalizace pořadí bod na trase (cestovní salesmen)

> [!NOTE]
> Azure Maps vyžaduje, aby všechny Waypoints byly souřadnice. Adresy musí být nejprve v INCODE.

Směrovací služba Azure Maps poskytuje následující rozhraní API pro výpočet tras.

-   [Vypočítat trasu](https://docs.microsoft.com/rest/api/maps/route/getroutedirections): Vypočítejte trasu a ihned zpracuje požadavek. Toto rozhraní API podporuje žádosti GET i POST. Žádosti POST se doporučují při zadání velkého počtu Waypoints nebo při použití spousty možností směrování, abyste zajistili, že se požadavek na adresu URL nestane příliš dlouhý a způsobuje problémy.
-   [Dávková trasa](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview): vytvořte žádost obsahující až 1 000 žádosti o trasu a požádejte ji o zpracování v časovém intervalu. Všechna data budou zpracována paralelně na serveru a po dokončení bude možné stáhnout úplnou sadu výsledků.
-   [Služby mobility](https://docs.microsoft.com/rest/api/maps/mobility): Vypočítejte trasy a směry pomocí veřejného přenosu.

Následující tabulka odkazuje na parametry rozhraní API služby Bing Maps pomocí srovnatelných parametrů rozhraní API v Azure Maps.

| Parametr rozhraní API pro mapy Bing                                    | Srovnatelný parametr Azure Maps rozhraní API               |
|------------------------------------------------------------|---------------------------------------------------|
| `avoid`                                                    | `avoid`                                           |
| `dateTime` (`dt`)                                          | `departAt` nebo `arriveAt`                          |
| `distanceBeforeFirstTurn` (`dbft`)                         | –                                               |
| `distanceUnit` (`du`)                                      | Není k dispozici – Azure Maps používá pouze systém metrik.     |
| `heading` (`hd`)                                           | `vehicleHeading`                                  |
| `maxSolutions` (`maxSolns`)                                | `maxAlternatives`, `alternativeType` , `minDeviationDistance` a `minDeviationTime`  |
| `optimize` (`optwz`)                                       | `routeType` a `traffic`                         |
| `optimizeWaypoints` (`optWp`)                              | `computeBestOrder`                                |
| `routeAttributes` (`ra`)                                   | `instructionsType`                                |
| `routePathOutput` (`rpo`)                                  | `routeRepresentation`                             |
| `timeType` (`tt`)                                          | `departAt` nebo `arriveAt`                          |
| `tolerances` (`tl`)                                        | –                                               |
| `travelMode`                                               | `travelMode`                                      |
| `waypoint.n` ( `wp.n` ) nebo `viaWaypoint.n` (`vwp.n`)         | `query` – souřadnice ve formátu `lat0,lon0:lat1,lon1….`   |
| `key`                                                      | `subscription-key` – Viz také [ověřování pomocí Azure Maps](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication) dokumentaci. |
| `culture` (`c`)                                            | `language` – Viz dokumentace k [podporovaným jazykům](https://docs.microsoft.com/azure/azure-maps/supported-languages) . |
| `userRegion` (`ur`)                                        | `view` – Viz dokumentace k [podporovaným zobrazením](https://aka.ms/AzureMapsLocalizationViews) . |

Rozhraní API pro směrování Azure Maps podporuje také směrování nákladní v rámci stejného rozhraní API. Následující tabulka ukazuje křížové odkazy na další parametry směrování v mapě Bingu s podobnými parametry rozhraní API v Azure Maps.

| Parametr rozhraní API pro mapy Bing                  | Srovnatelný parametr Azure Maps rozhraní API        |
|------------------------------------------|--------------------------------------------|
| `dimensionUnit` (`dims`)                 | Není k dispozici – dimenze v měřičích nejsou podporovány. |
| `weightUnit` (`wu`)                      | Není k dispozici – váhy se podporují jenom v kilogramech. |
| `vehicleHeight` (`height`)               | `vehicleHeight`                            |
| `vehicleWidth` (`width`)                 | `vehicleWidth`                             |
| `vehicleLength` (`vl`)                   | `vehicleLength`                            |
| `vehicleWeight` (`weight`)               | `vehicleWeight`                            |
| `vehicleAxles` (`axles`)                 | `vehicleAxelWeight`                        |
| `vehicleTrailers` (`vt`)                 | **–**                                    |
| `vehicleSemi` (`semi`)                   | `vehicleCommercial`                        |
| `vehicleMaxGradient` (`vmg`)             | **–**                                    |
| `vehicleMinTurnRadius` (`vmtr`)          | **–**                                    |
| `vehicleAvoidCrossWind` (`vacw`)         | **–**                                    |
| `vehicleAvoidGroundingRisk` (`vagr`)     | **–**                                    |
| `vehicleHazardousMaterials` (`vhm`)      | `vehicleLoadType`                          |
| `vehicleHazardousPermits` (`vhp`)        | `vehicleLoadType`                          |

> [!TIP]
> Ve výchozím nastavení vrací rozhraní API trasy Azure Maps pouze souhrn (vzdálenost a časy) a souřadnice pro cestu trasy. Pomocí `instructionsType` parametru načtěte pokyny pro zapínání. `routeRepresentation`Parametr lze použít k odfiltrování cesty souhrnu a trasy.

Nezapomeňte si také projít [osvědčené postupy pro](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-routing) dokumentaci ke směrování.

Rozhraní API pro směrování Azure Maps má mnoho dalších funkcí, které nejsou v mapách Bingu k dispozici, které by mohly být užitečné při migraci vaší aplikace:

-   Podpora pro typ směrování: nejkratší, nejrychlejší, Trilling a většina pohonných hmot.
-   Podpora dalších způsobů cestovních cest: jízdní kolo, sběrnice, motocykl, taxislužby, nákladní vůz a van.
-   Podpora 150 Waypoints.
-   Výpočet více dob cestování v rámci jediné žádosti; historické přenosy, živý provoz bez provozu.
-   Vyhněte se dalším typům cest: carpool silnice, Unpaved silnice, již využité silnice.
-   Směrování na základě specifikace stroje. Vypočítat trasy pro spalovací nebo elektrická vozidla založená na jejich zbývajících specifikacích pro palivo/poplatek a stroj.
-   Zadejte maximální rychlost vozidla.

## <a name="snap-coordinates-to-road"></a>Souřadnice přichycení k silnici

K dispozici je několik způsobů, jak přitahovat souřadnice na silnice v Azure Maps.

-   K přichycení souřadnic a z logické trasy podél síťové sítě použijte rozhraní API pro směrování tras.
-   Pomocí Azure Maps webové sady SDK můžete přitahovat jednotlivé souřadnice k nejbližší cestě na vektorové dlaždice.
-   Použijte vektorové dlaždice Azure Maps přímo k přichycení jednotlivých souřadnic.

**Použití rozhraní API pro směr směrování k přichycení souřadnic**

Azure Maps může přitahovat souřadnice na cesty pomocí rozhraní API pro [trasy tras](https://docs.microsoft.com/rest/api/maps/route/postroutedirections) . Tato služba se dá použít k rekonstrukci logické trasy mezi sadou souřadnic a je srovnatelná s rozhraním API pro mapování Bingu na cestu.

Existují dva různé způsoby, jak použít rozhraní API trasy směrování k přitahování souřadnic do cest.

-   Pokud jsou 150 nebo méně souřadnice, dají se předat jako Waypoints v rozhraní API pro získání tras. Pomocí tohoto přístupu se dají načíst dva různé typy přichycených dat. pokyny k trase budou obsahovat jednotlivé přichycené waypoints, zatímco cesta trasy bude obsahovat interpolované sady souřadnic, které plní úplnou cestu mezi souřadnicemi.
-   Je-li k dispozici více než 150 souřadnic, lze použít rozhraní API pro přesměrování tras po směru. Souřadnice počátečního a koncového souřadnic musí být předány do parametru dotazu, ale všechny souřadnice lze předat do `supportingPoints` parametru v těle požadavku POST a naformátovat kolekci geometrických geometrických bodů. Jediná přichycená data, která jsou k dispozici pomocí tohoto přístupu, budou cestou trasy, která je interpolovaná Sada souřadnic, které plní úplnou cestu mezi souřadnicemi. [Tady je příklad](https://azuremapscodesamples.azurewebsites.net/?sample=Snap%20points%20to%20logical%20route%20path) tohoto přístupu pomocí modulu služby v sadě Azure Maps Web SDK.

Následující tabulka odkazuje na parametry rozhraní API služby Bing Maps pomocí srovnatelných parametrů rozhraní API v Azure Maps.

| Parametr rozhraní API pro mapy Bing    | Srovnatelný parametr Azure Maps rozhraní API                                 |
|----------------------------|---------------------------------------------------------------------|
| `points`                   | `supportingPoints` – předejte tyto body do těla žádosti post.  |
| `interpolate`              | N/A                                                                 |
| `includeSpeedLimit`        | N/A                                                                 |
| `includeTruckSpeedLimit`   | N/A                                                                 |
| `speedUnit`                | N/A                                                                 |
| `travelMode`               | `travelMode`                                                        |
| `key`                      | `subscription-key` – Viz také [ověřování pomocí Azure Maps](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication) dokumentaci. |
| `culture` (`c`)            | `language` – Viz dokumentace k [podporovaným jazykům](https://docs.microsoft.com/azure/azure-maps/supported-languages) .   |
| `userRegion` (`ur`)        | `view` – Viz dokumentace k [podporovaným zobrazením](https://aka.ms/AzureMapsLocalizationViews) .   |

Rozhraní API směrování Azure Maps také podporuje parametr směrování nákladní ruky v rámci stejného rozhraní API, aby se vypočítaly logické cesty. Následující tabulka ukazuje křížové odkazy na další parametry směrování v mapě Bingu s podobnými parametry rozhraní API v Azure Maps.

| Parametr rozhraní API pro mapy Bing                 | Srovnatelný parametr Azure Maps rozhraní API        |
|-----------------------------------------|--------------------------------------------|
| `dimensionUnit` (`dims`)                | Není k dispozici – dimenze v měřičích nejsou podporovány. |
| `weightUnit` (`wu`)                     | Není k dispozici – váhy se podporují jenom v kilogramech. |
| `vehicleHeight` (`height`)              | `vehicleHeight`                            |
| `vehicleWidth` (`width`)                | `vehicleWidth`                             |
| `vehicleLength` (`vl`)                  | `vehicleLength`                            |
| `vehicleWeight` (`weight`)              | `vehicleWeight`                            |
| `vehicleAxles` (`axles`)                | `vehicleAxelWeight`                        |
| `vehicleTrailers` (`vt`)                | **–**                                    |
| `vehicleSemi` (`semi`)                  | `vehicleCommercial`                        |
| `vehicleMaxGradient` (`vmg`)            | **–**                                    |
| `vehicleMinTurnRadius` (`vmtr`)         | **–**                                    |
| `vehicleAvoidCrossWind` (`vacw`)        | **–**                                    |
| `vehicleAvoidGroundingRisk` (`vagr`)    | **–**                                    |
| `vehicleHazardousMaterials` (`vhm`)     | `vehicleLoadType`                          |
| `vehicleHazardousPermits` (`vhp`)       | `vehicleLoadType`                          |

Vzhledem k tomu, že tento přístup používá rozhraní API s pokyny pro směrování, je možné použít úplnou sadu možností v této službě k přizpůsobení logiky použité pro přitahování souřadnice na cesty. Například zadání času odchodu by způsobilo, že budou vzata v úvahu historická data o provozu.

Rozhraní API pro Azure Maps tras v současné době nevrací data s omezením rychlosti, ale lze je načíst pomocí rozhraní API Azure Maps reverzního geografického kódování.

**Použití webové sady SDK k přichycení souřadnic**

Sada Azure Maps Web SDK používá ke vykreslování map vektorové dlaždice. Tyto vektorové dlaždice obsahují nezpracované informace o geometrii a lze je použít k výpočtu nejbližší cesty k souřadnici pro jednoduché uchycení jednotlivých souřadnic. To je užitečné, pokud chcete, aby se souřadnice vizuálně zobrazovaly v cestách a abyste již používali Azure Maps webové sady SDK k vizualizaci dat.

Tento přístup se ale bude přitahovat jenom na segmenty cest, které jsou načtené v zobrazení mapy. Při přiblížení na úrovni země nemusí existovat žádná data o provozu, takže přichycení není možné, ale v této úrovni přiblížení může jeden pixel představovat oblast několika bloků měst, takže přichycení není potřeba. Pro vyřešení této logiky lze logiku přichycení použít při každém přesunutí mapy. [Zde je příklad kódu](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Basic%20snap%20to%20road%20logic) , který ukazuje to.

**Použití vektorových dlaždic Azure Maps přímo k přichycení souřadnic**

Azure Maps vektorové dlaždice obsahují nezpracovaná data geometrie cest, která se dají použít k výpočtu nejbližšího bodu na cestách na souřadnici, aby se základní přichycení k jednotlivým souřadnicím. Všechny silniční segmenty se zobrazují v sektorech na úrovni přiblížení 15, takže budete chtít z nich načíst dlaždice. Pak můžete použít [plovoucí matematiku dlaždice quadtree](https://docs.microsoft.com/azure/azure-maps/zoom-levels-and-tile-grid) a určit, jestli jsou dlaždice potřebné, a převést dlaždice na geometrií. Z prostorové matematické knihovny, jako je například [Turf js](http://turfjs.org/) nebo [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) , lze použít k výpočtu nejbližších segmentů čáry.

## <a name="retrieve-a-map-image-static-map"></a>Načtení obrázku mapy (statická mapa)

Azure Maps poskytuje rozhraní API pro vykreslování statických imagí map s překrývajícími se daty. Rozhraní API pro [vykreslování obrázku mapy](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) Azure Maps je SROVNATELNÉ s rozhraním API statické mapy v mapách Bing.

> [!NOTE]
> Azure Maps vyžaduje, aby byly všechny špendlíky a umístění cest ve `longitude,latitude` formátu, zatímco mapy Bing používají `latitude,longitude` formát.</p>
<p>Adresy musí být nejprve v INCODE.

Následující tabulka odkazuje na parametry rozhraní API služby Bing Maps pomocí srovnatelných parametrů rozhraní API v Azure Maps.

| Parametr rozhraní API pro mapy Bing  | Srovnatelný parametr Azure Maps rozhraní API            |
|--------------------------|------------------------------------------------|
| `centerPoint`            | `center`                                       |
| `format`                 | `format` – zadáno jako součást cesty URL. V současné době je podporována pouze PNG.  |
| `heading`                | Není k dispozici – Streetside se nepodporuje.                |
| `imagerySet`             | `layer` a `style` – Viz dokumentace k [podporovaným stylům mapy](https://docs.microsoft.com/azure/azure-maps/supported-map-styles) .   |
| `mapArea` (`ma`)         | `bbox`                                         |
| `mapLayer` (`ml`)        | –                                            |
| `mapSize` (`ms`)         | `width` a `height` – může mít velikost až 8192x8192. |
| `declutterPins` (`dcl`)  | N/A                                            |
| `dpi`                    | N/A                                            |
| `drawCurve`              | `path`                                         |
| `mapMetadata`            | N/A                                            |
| `pitch`                  | Není k dispozici – Streetside se nepodporuje.                |
| `pushpin` (`pp`)         | `pins`                                         |
| `zoomLevel`              | `zoom`                                         |
| `query`                  | Je třeba použít na střed nebo ohraničovací rámeček.     |
| `highlightEntity` (`he`) | N/A                                            |
| `style`                  | N/A                                            |
| parametry směrování         | –                                            |
| `key`                    | `subscription-key` – Viz také [ověřování pomocí Azure Maps](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication) dokumentaci. |
| `culture` (`c`)          | `language` – Viz dokumentace k [podporovaným jazykům](https://docs.microsoft.com/azure/azure-maps/supported-languages) .   |
| `userRegion` (`ur`)      | `view` – Viz dokumentace k [podporovaným zobrazením](https://aka.ms/AzureMapsLocalizationViews) . |

> [!NOTE]
> Azure Maps používá systém dlaždic s dlaždicemi, které mají dvojnásobnou velikost dlaždic rozvržení používaných ve službě Mapy Bing. V takovém případě se hodnota úrovně přiblížení v Azure Maps zobrazí v porovnání s mapami bingem o jednu úroveň přiblížení v Azure Maps. Snižte úroveň přiblížení požadavků, které migrujete, o 1, abyste to vyrovnali.

Další informace najdete v [příručce k rozhraní API pro vykreslování obrázků mapy](https://docs.microsoft.com/azure/azure-maps/how-to-render-custom-data) .

Kromě toho, že je možné vygenerovat statický obrázek mapy, služba Azure Maps rendering také poskytuje možnost přímý přístup k dlaždicím mapy v rastrovém formátu (PNG) a ve vektorovém formátu.

-   [Dlaždice mapy](https://docs.microsoft.com/rest/api/maps/render/getmaptile) – načte rastrový (PNG) a vektorové dlaždice pro základní mapy (cesty, hranice, pozadí).
-   [Mapa dlaždice s rozvržením obrazů](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) – načtení dlaždic satelitních a satelitních snímků

### <a name="pushpin-url-parameter-format-comparison"></a>Porovnání formátu parametru URL pro připínáček

**Před: mapy Bing**

Ve službě Mapy Bing lze přidat špendlíky na statický obrázek mapy pomocí `pushpin` parametru v adrese URL. `pushpin`Parametr přebírá ve `latitude,longitude` formátu umístění, styl ikony a textový popisek (až tři znaky), jak je znázorněno níže:

> `&pushpin=latitude,longitude;iconStyle;label`

Další špendlíky lze přidat přidáním dalších `pushpin` parametrů k adrese URL s jinou sadou hodnot. Styly ikon připínáčku jsou omezeny na jeden z předdefinovaných stylů dostupných v rozhraní API služby mapy Bing.

Například v mapách Bingu se k mapě dá přidat červená připínáček s popiskem "AB" (zeměpisná délka:-110, zeměpisná šířka: 45) s následujícím parametrem URL:

> `&pushpin=45,-110;7;AB`

<center>

![Statický mapový kód mapy Bing Maps](media/migrate-bing-maps-web-service/bing-maps-static-map-pin.jpg)</center>

**Po: Azure Maps**

V Azure Maps lze také přidat špendlíky do statického obrázku mapy zadáním `pins` parametru v adrese URL. Špendlíky v Azure Maps jsou definovány zadáním stylu ikony a seznamu umístění, která používají tento styl ikony. Tyto informace se pak předají do `pins` parametru zadat víckrát, aby se podporovaly špendlíky s různými styly.

> `&pins=iconType|pinStyles||pinLocation1|pinLocation2|...`

Další styly lze použít přidáním dalších `pins` parametrů k adrese URL s jiným stylem a sadou umístění.

Když přichází do připnutí umístění, Azure Maps vyžaduje, aby byly souřadnice ve `longitude latitude` formátu, zatímco mapy Bing používají `latitude,longitude` formát. Všimněte si také, že **je mezera, a ne čárka,** která odděluje zeměpisnou délku a zeměpisnou šířku v Azure Maps.

`iconType`Hodnota určuje typ pinu, který se má vytvořit, a může mít následující hodnoty:

-   `default` – Výchozí ikona PIN
-   `none` – Není zobrazena žádná ikona, budou vykresleny pouze popisky.
-   `custom` – Určuje vlastní ikonu, která se má použít. Adresa URL ukazující na obrázek ikony může být přidána na konec `pins` parametru za informace o umístění kódu PIN.
-   `{udid}` – Jedinečné ID dat (UDID) pro ikonu uloženou v Azure Maps platformě úložiště dat.

Styly připnutí v Azure Maps jsou přidány ve formátu `optionNameValue` s více znaky oddělenými svislou čárou ( `|` ), jako je to `iconType|optionName1Value1|optionName2Value2` . Všimněte si, že názvy možností a hodnoty nejsou oddělené. Následující názvy možností stylu lze použít pro styl připínáček v Azure Maps:

-   `al` – Určuje neprůhlednost (alfa) špendlíků. Může se jednat o číslo mezi 0 a 1.
-   `an` – Určuje kotvu PIN. Hodnoty X a y v pixelech, které jsou zadány ve formátu `x y` .
-   `co` – Barva kódu PIN. Musí se jednat o 24bitové hexadecimální barvu: `000000` na `FFFFFF` .
-   `la` – Určuje kotvu popisku. Hodnoty X a y v pixelech, které jsou zadány ve formátu `x y` .
-   `lc` – Barva popisku. Musí být 24, ale hexadecimální Barva: `000000` na `FFFFFF` .
-   `ls` – Velikost popisku v pixelech. Může být číslo větší než 0.
-   `ro` – Hodnota ve stupních pro otočení ikony. Může to být číslo mezi-360 a 360.
-   `sc` – Hodnota měřítka pro ikonu připnutí. Může být číslo větší než 0.

Hodnoty popisků jsou určené pro každé místo kódu PIN, a ne jako jediná hodnota popisku, která se vztahuje na všechna připínáček v seznamu umístění. Hodnota popisku může být řetězec více znaků a zabalená pomocí jednoduchých uvozovek, aby se zajistilo, že není možné je označit jako styl nebo hodnotu umístění.

Například v Azure Maps přidání červené ( `FF0000` ) výchozí ikony s popiskem "ručička místa" umístěného níže (15 50) ikona na souřadnicích (zeměpisná délka:-122,349300, zeměpisná šířka: 47,620180) se dá provést pomocí následujícího parametru URL:

> `&pins=default|coFF0000|la15 50||'Space Needle'-122.349300 47.620180`

<center>

![Azure Maps kód PIN statické mapy](media/migrate-bing-maps-web-service/azure-maps-static-map-pin.jpg)</center>

Následující příklad přidá tři PIN kódy s hodnotami popisku "1", "2" a "3":

> `&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12`

<center>

![Azure Maps více kódů PIN statických map](media/migrate-bing-maps-web-service/azure-maps-static-map-multiple-pins.jpg)</center>

### <a name="draw-curve-url-parameter-format-comparison"></a>Nakreslit porovnání formátu parametru URL křivky

**Před: mapy Bing**

V rámci mapy Bingu, čáry a mnohoúhelníky lze přidat do statického obrázku mapy pomocí `drawCurve` parametru v adrese URL. `drawCurve`Parametr přebírá typ tvaru, typ stylu a seznam umístění, které mají být vykresleny na mapě, jak je znázorněno níže:

> `&drawCurve=shapeType,styleType,location1,location2...`

Další styly lze použít přidáním dalších `drawCurve` parametrů k adrese URL s jiným stylem a sadou umístění.

Umístění ve službě Mapy Bing jsou zadaná ve formátu `latitude1,longitude1_latitude2,longitude2_…` . Umístění lze také kódovat.

Mezi typy obrazců v mapách Bing patří čáry, mnohoúhelníky, kolečka a křivka. Mezi typy stylů patří Barva čáry, Tloušťka čáry, barva obrysu, barva výplně, tloušťka obrysu a kruhový poloměr.

Například v mapách Bing je modrá čára s 50% neprůhledností a tloušťka čtyř pixelů může být přidána k mapě mezi souřadnicemi (zeměpisná délka:-110, zeměpisná šířka: 45 a zeměpisná délka:-100, zeměpisná šířka: 50) s následujícím parametrem URL:

`&drawCurve=l,FF000088,4;45,-110_50,-100`

<center>

![Statická mapová čára mapy Bing](media/migrate-bing-maps-web-service/bing-maps-static-map-line.jpg)</center>

**Po: Azure Maps**

V Azure Maps lze k obrázku statické mapy přidat také řádky a mnohoúhelníky zadáním parametru *path* v adrese URL. Podobně jako mapy Bing mohou být v tomto parametru zadány styly a seznam umístění a parametr *cesty* lze zadat vícekrát pro vykreslení více kruhů, čar a mnohoúhelníků s různými styly.

> `&path=pathStyles||pathLocation1|pathLocation2|...`

Pokud se nachází do umístění cest, Azure Maps vyžaduje, aby byly souřadnice ve `longitude latitude` formátu, zatímco mapy Bing používají `latitude,longitude` formát. Všimněte si také, že **je mezera, a ne čárka, která odděluje** zeměpisnou délku a zeměpisnou šířku v Azure Maps. Azure Maps aktuálně nepodporuje kódované cesty. Větší sady dat je možné nahrát do rozhraní Azure Maps API úložiště dat, jak je popsáno [zde](https://docs.microsoft.com/azure/azure-maps/how-to-render-custom-data#get-data-from-azure-maps-data-storage).

Styly cest v Azure Maps jsou přidány ve formátu `optionNameValue` s více znaky oddělenými znaky svislé čáry ( `|` ), jako je to `optionName1Value1|optionName2Value2` . Všimněte si, že názvy možností a hodnoty nejsou oddělené. Následující názvy možností stylu lze použít k použití stylu cest v Azure Maps:

-   `fa` – Barva výplně pozadí (alfa) použitá při vykreslování mnohoúhelníků. Může se jednat o číslo mezi 0 a 1.
-   `fc` – Barva výplně použitá k vykreslení oblasti mnohoúhelníku
-   `la` – Barva čáry neprůhlednosti (alfa), která se používá při vykreslování čar a obrysu mnohoúhelníků. Může se jednat o číslo mezi 0 a 1.
-   `lc` – Barva čáry použitá k vykreslení čar a obrysu mnohoúhelníků.
-   `lw` – Šířka čáry v pixelech.
-   `ra` – Určuje poloměr kroužků v měřičích.

Například v Azure Maps může být k mapě mezi souřadnicemi (zeměpisná délka:-110, zeměpisná šířka: 45 a zeměpisná délka:-100, zeměpisná šířka: 50) s následujícím parametrem adresy URL přidána modrá čára s 50% krytím:

> `&path=lc0000FF|la.5|lw4||-110 45|-100 50`

<center>

![Azure Maps statická čára mapy](media/migrate-bing-maps-web-service/azure-maps-static-map-line.jpg)</center>

## <a name="calculate-a-distance-matrix"></a>Vypočítat matici vzdálenosti

Azure Maps poskytuje rozhraní API pro výpočet doby trvání cest a vzdáleností mezi sadou umístění jako s maticí na dálku. Rozhraní API služby Azure Maps Distance je srovnatelné s rozhraním API služby Distance v mapách Bingu.

-   [Matice směrování](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview): asynchronně vypočítá dobu trvání cesty a vzdálenosti pro sadu míst původu a místa. Je podporováno až 700 buněk na žádost (počet počátek vynásobený počtem cílů). S tímto omezením je třeba mít příklady možných dimenzí matice: `700x1` , `50x10` , `10x10` , `28x25` , `10x70` .

> [!NOTE]
> Požadavek na rozhraní API na dálku se dá vytvořit jenom pomocí žádosti POST s informacemi o původu a cíli v těle žádosti.</p>
<p>Azure Maps navíc vyžaduje, aby všechny zdroje a cíle byly souřadnice. Adresy musí být nejprve v INCODE.

Následující tabulka odkazuje na parametry rozhraní API služby Bing Maps pomocí srovnatelných parametrů rozhraní API v Azure Maps.

| Parametr rozhraní API pro mapy Bing | Srovnatelný parametr Azure Maps rozhraní API                         |
|-------------------------|-------------------------------------------------------------|
| `origins`               | `origins` – Zadejte v textu požadavku POST jako text typu injson.    |
| `destinations`          | `destination` – Zadejte v textu požadavku POST jako text typu injson. |
| `endTime`               | `arriveAt`                                                  |
| `startTime`             | `departAt`                                                  |
| `travelMode`            | `travelMode`                                                |
| `resolution`            | –                                                         |
| `distanceUnit`          | Není k dispozici – všechny vzdálenosti v metrech.                              |
| `timeUnit`              | Není k dispozici – všechny časy v sekundách.                                 |
| `key`                   | `subscription-key` – Viz také [ověřování pomocí Azure Maps](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication) dokumentaci. |
| `culture` (`c`)         | `language` – Viz dokumentace k [podporovaným jazykům](https://docs.microsoft.com/azure/azure-maps/supported-languages) .  |
| `userRegion` (`ur`)     | `view` – Viz dokumentace k [podporovaným zobrazením](https://aka.ms/AzureMapsLocalizationViews) .     |

> [!TIP]
> Všechny možnosti pokročilého směrování, které jsou k dispozici v rozhraní API směrování Azure Maps (směrování nákladní automobil, specifikace modulu, vyhněte se...) podporují rozhraní API Azure Maps Distance.

## <a name="calculate-an-isochrone"></a>Vypočítat isochrone

Azure Maps poskytuje rozhraní API pro výpočet isochrone, mnohoúhelník pokrývající oblast, na kterou se dá cestovat v jakémkoli směru od počátečního bodu v zadaném množství času nebo množství paliva a poplatků. Rozhraní API rozsahu Azure Maps tras je srovnatelné s rozhraním API isochrone ve službě Bing Maps;

-   [Směrování](https://docs.microsoft.com/rest/api/maps/route/getrouterange) Rozsah * *: Vypočítejte mnohoúhelník pokrývající oblast, na kterou se dá cestovat v jakémkoli směru od počátečního místa v zadaném čase, vzdálenosti nebo množství dostupného paliva nebo nabití.

> [!NOTE]
> Azure Maps vyžaduje, aby měl počátek dotazu souřadnici. Adresy musí být nejprve v INCODE.</p>
<p>Mapy Bing také mohou vypočítat izochronů na základě času nebo vzdálenosti, zatímco Azure Maps mohou vypočítat izochronů na základě času, vzdálenosti nebo množství dostupného paliva nebo nabití.

Následující tabulka odkazuje na parametry rozhraní API služby Bing Maps pomocí srovnatelných parametrů rozhraní API v Azure Maps.

| Parametr rozhraní API pro mapy Bing      | Srovnatelný parametr Azure Maps rozhraní API            |
|------------------------------|------------------------------------------------|
| `waypoint` (`wp`)            | `query`                                        |
| `dateTime` (`dt`)            | `departAt`                                     |
| `maxTime`                    | `timeBudgetInSec`                              |
| `timeUnit` (`tu`)            | Není k dispozici – všechny časy v sekundách.                    |
| `travelMode` (`mode`)        | `travelMode`                                   |
| `maxDistance` (`maxDis`)     | `distanceBudgetInMeters`                       |
| `distanceUnit` (`du`)        | Není k dispozici – všechny vzdálenosti v metrech.                 |
| `optimize` (`optmz`)         | `routeType`                                    |
| `key`                        | `subscription-key` – Viz také [ověřování pomocí Azure Maps](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication) dokumentaci. |
| `culture` (`c`)              | `language` – Viz dokumentace k [podporovaným jazykům](https://docs.microsoft.com/azure/azure-maps/supported-languages) .  |
| `userRegion` (`ur`)          | `view` – Viz dokumentace k [podporovaným zobrazením](https://aka.ms/AzureMapsLocalizationViews) . |

> [!TIP]
> Všechny možnosti pokročilého směrování, které jsou k dispozici v rozhraní API směrování pro Azure Maps (směrování nákladní automobil, specifikace modulu, vyhněte se...) podporují rozhraní API služby Azure Maps isochrone.

## <a name="search-for-points-of-interest"></a>Hledání bodů zájmu

Data z bodu zájmu je možné prohledávat pomocí následujících rozhraní API služby Bing Maps:

-   **Místní hledání:** Vyhledá zajímavé body, které jsou v blízkosti (kruhové hledání), podle názvu nebo podle typu entity (kategorie). Rozhraní API pro vyhledávání Azure Maps [POI](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) a [POI v kategorii hledání](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) se nejvíce podobá tomuto rozhraní API.
-   **Rozpoznávání umístění**: vyhledá body zájmů, které spadají do určité vzdálenosti. Rozhraní API pro [hledání v blízkosti](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) Azure Maps je nejvíce podobné tomuto rozhraní API.
-   **Místní přehledy:** Vyhledá body zájmů, které jsou v rámci určeného maximální doby jízdy nebo vzdálenosti od konkrétní souřadnice. To je dosažitelné pomocí Azure Maps, a to tak, že nejprve vypočítá isochrone a pak je předáte do [vyhledávání v rámci](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) rozhraní API geometrie.

Azure Maps poskytuje několik rozhraní API pro hledání bodů zájmu:

-   [Hledání POI](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi): vyhledejte body zájmů podle jména. Například; `"starbucks"`.
-   [Hledání kategorií POI](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory): vyhledejte body zájmu podle kategorie. Například; "restaurace".
-   [Nejbližší hledání](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby): vyhledá body zájmů, které spadají do určité vzdálenosti.
-   [Hledání přibližných](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)hodnot: Toto rozhraní API kombinuje geografické kódování s bodem hledání z hlediska zájmu. Toto rozhraní API přebírá řetězec ve volném formátu, který může být adresami, místem, orientačními body, bodem zájmu nebo kategorií zájmu a okamžitě zpracovat požadavek. Toto rozhraní API se doporučuje u aplikací, kde můžou uživatelé vyhledat adresy nebo body zájmu ze stejného textového pole.
-   [Hledání v geometrii](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry): hledání bodů zájmů v rámci zadaného geometrie (mnohoúhelník).
-   [Hledání v cestě](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute): vyhledejte body, které jsou podél zadané cesty trasy.
-   [Nepřibližné dávkové vyhledávání](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): vytvoření žádosti obsahující až 10 000 adres, míst, orientačních bodů nebo bodů zájmu a jejich zpracování v časovém intervalu. Všechna data budou zpracována paralelně na serveru a po dokončení bude možné stáhnout úplnou sadu výsledků.

Nezapomeňte si projít [osvědčené postupy pro vyhledávání](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search) v dokumentaci.

## <a name="get-traffic-incidents"></a>Získat incidenty provozu

Azure Maps poskytuje několik rozhraní API pro načítání dat o provozu. K dispozici jsou dva typy dat provozu;

-   **Data toku** – poskytuje metriky toku provozu v částech cest. To se často používá k barvování cest kódu. Tato data se aktualizují každé 2 minuty.
-   **Data incidentu** – poskytuje data týkající se konstrukce, uzavření provozu, havárií a dalších incidentů, které mohou mít vliv na provoz. Tato data se aktualizují každou minutu.

Služba mapy Bing poskytuje data toku a dat incidentu v interaktivních ovládacích prvcích mapy a také zpřístupňuje data incidentu jako službu.

Data přenosů jsou také integrovaná do Azure Maps interaktivní ovládací prvky mapy. Služba Azure Maps také poskytuje následující rozhraní API služeb přenosu dat;

-   [Segmenty toku provozu](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficflowsegment): poskytuje informace o rychlostech a cestách cest ke fragmentu cesty, které jsou nejblíže daným souřadnicím.
-   [Dlaždice toku provozu](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficflowtile): poskytuje rastrové a vektorové dlaždice obsahující data toku provozu. Ty lze použít s ovládacími prvky Azure Maps nebo v mapových ovládacích prvcích třetích stran, jako je například leták. Vektorové dlaždice lze také použít pro pokročilou analýzu dat.
-   [Podrobnosti o incidentu provozu](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficincidentdetail): poskytuje podrobnosti o incidentech provozu, které se nacházejí v rámci ohraničujícího pole, úrovně přiblížení a modelu provozu.
-   [Dlaždice incidentů provozu](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficincidenttile): poskytuje rastrové a vektorové dlaždice obsahující data o incidentech provozu.
-   [Zobrazení incidentu provozu](https://docs.microsoft.com/rest/api/maps/traffic/gettrafficincidentviewport): načte právní a technické informace pro zobrazení popsané v žádosti, jako je například ID modelu provozu.

Následující tabulka odkazuje na parametry rozhraní API služby mapy Bing s podobnými parametry rozhraní API pro podrobnosti o incidentu provozu v Azure Maps.

| Parametr rozhraní API pro mapy Bing  | Srovnatelný parametr Azure Maps rozhraní API   |
|--------------------------|---------------------------------------|
| `mapArea`                | `boundingBox` a `boundingZoom`      |
| `includeLocationCodes`   | –                                   |
| `severity` (`s`)         | Není k dispozici – všechna vrácená data               |
| `type` (`t`)             | Není k dispozici – všechna vrácená data               |
| `key`                    | `subscription-key` – Viz také [ověřování pomocí Azure Maps](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication) dokumentaci. |
| `culture` (`c`)          | `language` – Viz dokumentace k [podporovaným jazykům](https://docs.microsoft.com/azure/azure-maps/supported-languages) . |
| `userRegion` (`ur`)      | `view` – Viz dokumentace k [podporovaným zobrazením](https://aka.ms/AzureMapsLocalizationViews) . |

## <a name="get-a-time-zone"></a>Získat časové pásmo

Azure Maps poskytuje rozhraní API pro načtení časového pásma, ve kterém je souřadnice. Rozhraní API pro časové pásmo Azure Maps je srovnatelné s rozhraním API pro časové pásmo ve službě Bing Maps;

-   [Časové pásmo podle souřadnic](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates): Určete souřadnici a Získejte podrobnosti o časovém pásmu, ve kterém spadá.

Následující tabulka odkazuje na parametry rozhraní API služby Bing Maps pomocí srovnatelných parametrů rozhraní API v Azure Maps.

| Parametr rozhraní API pro mapy Bing | Srovnatelný parametr Azure Maps rozhraní API          |
|-------------------------|----------------------------------------------|
| `point`                 | `query`                                      |
| `query`                 | Není k dispozici, umístění je třeba nejprve geograficky nakódovat.      |
| `dateTime`              | `timeStamp`                                  |
| `includeDstRules`       | Není k dispozici – vždy je součástí odpovědi Azure Maps. |
| `key`                   | `subscription-key` – Viz také [ověřování pomocí Azure Maps](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication) dokumentaci. |
| `culture` (`c`)         | `language` – Viz dokumentace k [podporovaným jazykům](https://docs.microsoft.com/azure/azure-maps/supported-languages) .  |
| `userRegion` (`ur`)     | `view` – Viz dokumentace k [podporovaným zobrazením](https://aka.ms/AzureMapsLocalizationViews) .  |

Kromě této Azure Maps platforma poskytuje také řadu dalších rozhraní API pro časové pásmo, které vám pomůžou s převody s názvy a ID časových pásem.

-   [Časové pásmo podle ID](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebyid): vrátí aktuální, historické a budoucí informace o časovém pásmu pro zadané ID časového pásma IANA.
-   Identifikátor [úřadu pro vyčíslení časového pásma](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumiana): vrátí úplný seznam ID časových pásem IANA. Aktualizace služby IANA se v systému projeví v rámci jednoho dne. 
-   [Okna výčtu časových pásem](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumwindows): vrátí úplný seznam ID časových pásem systému Windows.
-   [Verze IANA časového pásma](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneianaversion): vrátí aktuální číslo verze iana používané Azure Maps. 
-   [Časová pásma Windows to IANA](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonewindowstoiana): vrátí odpovídající ID IANA, které má přidělené platné ID časového pásma systému Windows. Pro jedno ID systému Windows může být vráceno více ID IANA.

## <a name="spatial-data-services-sds"></a>Prostorové Data Services (SDS)

Služby prostorových dat v mapách Bing poskytují tři klíčové funkce:

-   Geografické kódování dávky – zpracování velké dávky adresování geografického kódu s jediným požadavkem.
-   Načíst data hranic pro správu – použijte souřadnici a získejte hranici přeprotínající se pro zadaný typ entity.
-   Hostování a dotazování prostorových podnikových dat – nahrajte jednoduchou 2D tabulku dat a získejte k ní přístup pomocí několika jednoduchých prostorových dotazů.

### <a name="batch-geocode-data"></a>Data o subkódu Batch

Geografické kódování dávky je proces přebírání velkého počtu adres nebo míst, jejich předání v rámci jediné žádosti do služby a všechny jejich geografické označení paralelně a výsledky vracené v jediné odpovědi.

Mapy Bing umožňují předávat až 200 000 adres do jediné žádosti o kód v rámci služby Batch. Tento požadavek přechází do fronty a obvykle zpracovává v časovém intervalu, a to v závislosti na velikosti datové sady a zatížení služby, a to během několika minut až po několik hodin. Každá adresa v žádosti vygenerovala transakci.

Azure Maps má službu pro geografické kódování, ale umožňuje předávat až 10 000 adres v rámci jediné žádosti a v závislosti na velikosti datové sady a zatížení služby se zpracovává během několika sekund. Každá adresa v žádosti vygenerovala transakci. V Azure Maps je služba geografické kódování v dávce dostupná jenom pro úroveň S1.

Další možností pro geografické kódování velkých adres s Azure Maps je udělat paralelní požadavky na standardní rozhraní API pro vyhledávání. Tyto služby přijímají jenom jednu adresu na žádost, ale dají se použít s úrovní S0, která také poskytuje bezplatné limity využití. Úroveň S0 umožňuje až 50 požadavků za sekundu na Azure Maps platformu z jednoho účtu. Takže pokud omezíte THES tak, aby se v rámci tohoto limitu udržely, je možné, že se vám bude po hodinách nacházet až 180 000 adres. Úroveň S1 nemá dokumentovaný limit počtu dotazů za sekundu, které je možné vytvořit z účtu, takže při použití této cenové úrovně se dá rychleji zpracovat spousta dat, což vám ale pomůže snížit celkový objem přenášených dat a významně snížit zatížení sítě.

-   [Geografické kódování adresy volného formátu](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress): zadejte jeden adresní řetězec (například `"1 Microsoft way, Redmond, WA"` ) a okamžitě zpracovat požadavek. Tato služba se doporučuje v případě, že potřebujete jednotlivé adresy snadno kódovat.
-   [Geografické označování strukturovaných adres](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured): Určete části jedné adresy, jako je název ulice, město, země a poštovní směrovací číslo a okamžitě zpracujte požadavek. Tato služba je doporučena v případě, že je nutné rychle kódovat jednotlivé adresy a data jsou již analyzována do jednotlivých částí adres.
-   [Geografické kódování adresy Batch](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview): vytvořte žádost obsahující až 10 000 adres a požádejte je o zpracování v časovém intervalu. Všechny adresy budou na serveru paralelně zavedeny a po dokončení bude možné stáhnout úplnou sadu výsledků. Tato služba se doporučuje pro geografické datové sady s velkými kódováními.
-   [Hledání přibližných](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)hodnot: Toto rozhraní API kombinuje geografické kódování s bodem hledání z hlediska zájmu. Toto rozhraní API přebírá řetězec ve volném formátu, který může být adresami, místem, orientačními body, bodem zájmu nebo kategorií zájmu a okamžitě zpracovat požadavek. Toto rozhraní API se doporučuje u aplikací, kde můžou uživatelé vyhledat adresy nebo body zájmu ze stejného textového pole.
-   [Nepřibližné dávkové vyhledávání](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): vytvoření žádosti obsahující až 10 000 adres, míst, orientačních bodů nebo bodů zájmu a jejich zpracování v časovém intervalu. Všechna data budou zpracována paralelně na serveru a po dokončení bude možné stáhnout úplnou sadu výsledků.

### <a name="get-administrative-boundary-data"></a>Získat data hranice správy

V mapách Bingu jsou hranice správy pro země, stavy, okresy, města a poštovní kódy dostupné prostřednictvím rozhraní API pro zjištění dat. Toto rozhraní API přebírá buď souřadnici, nebo dotaz na INCODE. Pokud je předán dotaz, je určen jako kód a jsou použity souřadnice z prvního výsledku. Toto rozhraní API přebírá souřadnice a načítá hranici zadaného typu entity, která protíná souřadnici. Všimněte si, že toto rozhraní API nevrátilo nutně hranici pro dotaz, který byl předán. Pokud `"Seattle, WA"` je předán dotaz pro, ale hodnota typu entity je nastavená na oblast země, vrátí se hranice pro USA.

Azure Maps také poskytuje přístup k hranicím pro správu (země, státy, okresy, města a poštovní kódy). Pokud chcete načíst hranici, musíte zadat dotaz na jednu z rozhraní API pro hledání požadované hranice (tj. `Seattle, WA` ). Pokud má výsledek hledání přidruženou hranici, poskytne se v odpovědi výsledek ID geometrie. Rozhraní API pro vyhledávání pomocí mnohoúhelníku se pak dá použít k načtení přesných hranic pro jedno nebo více ID geometrie. Toto je jiný bit než mapy Bing, protože Azure Maps vrací hranici pro hledání, zatímco služba Bing Maps vrací hranici pro zadaný typ entity na zadané souřadnici. Kromě toho jsou data hranice vrácená funkcí Azure Mapsa ve formátu injson.

Do Rekapitulace:

1.  Předejte dotaz na hranici, kterou chcete přijmout, do jedné z následujících rozhraní API pro hledání.
    -   [Geografické kódování adresy volného formátu](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)
    -   [Geografické kódování strukturovaných adres](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured)
    -   [Geografické kódování adres dávky](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview)
    -   [Vyhledávání přibližných shod](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
    -   [Nepřibližné dávkové vyhledávání](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview)
2.  Pokud mají požadované výsledky ID geometrie, předejte je do [rozhraní API pro hledání mnohoúhelníku](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon).

### <a name="host-and-query-spatial-business-data"></a>Hostování a dotazování prostorových firemních dat

Služby prostorových dat v mapách Bing poskytují jednoduché řešení úložiště prostorových dat pro hostování obchodních dat a jeho zveřejnění jako služby prostorového REST. Tato služba poskytuje čtyři hlavní dotazy; Najít podle vlastnosti, najít okolní, najít v ohraničujícím poli a vyhledat s 1 km trasy. Mnoho společností, které tuto službu používají, často již mají své obchodní údaje uloženy v databázi někam a do této služby odeslali malou podmnožinu dat do aplikací, jako jsou Lokátory úložiště. Vzhledem k tomu, že ověřování založené na klíčích poskytuje základní zabezpečení, doporučujeme, abyste tuto službu používali jenom s veřejnými daty.

V databázi se spustí většina dat umístění firmy. V takovém případě se doporučuje používat stávající řešení Azure Storage, jako je Azure SQL nebo Azure PostgreSQL (s modulem plug-in PostGIS). Obě tato řešení úložiště podporují prostorová data a poskytují bohatou sadu možností dotazování na prostor. Jakmile jsou data v vhodném řešení úložiště, je možné je integrovat do vaší aplikace vytvořením vlastní webové služby nebo pomocí architektury, jako je ASP.NET nebo Entity Framework. Použití tohoto přístupu nabízí další možnosti dotazování a také mnohem větší možnosti zabezpečení.

Azure Cosmos DB také nabízí omezené množství prostorových možností, které v závislosti na vašem scénáři můžou být dostatečné.

Tady je několik užitečných prostředků pro hostování a dotazování prostorových dat v Azure.

-   [Přehled prostorových datových typů SQL Azure](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-types-overview)
-   [Prostor v Azure SQL – dotaz s nejbližším sousedem](https://docs.microsoft.com/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor)
-   [Přehled Azure Cosmos DB geoprostorové možnosti](https://docs.microsoft.com/azure/cosmos-db/geospatial)

## <a name="client-libraries"></a>Klientské knihovny

Azure Maps poskytuje klientské knihovny pro následující programovací jazyky;

-   JavaScript, TypeScript, Node.js – [documentation](https://docs.microsoft.com/azure/azure-maps/how-to-use-services-module) \| [balíček dokumentace npm](https://www.npmjs.com/package/azure-maps-rest)

Open Source klientské knihovny pro jiné programovací jazyky;

-   .NET Standard 2,0 – [GitHub project](https://github.com/perfahlen/AzureMapsRestServices) \| [balíček NuGet](https://www.nuget.org/packages/AzureMapsRestToolkit/) pro projekt GitHubu

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o službě Azure Maps REST.

> [!div class="nextstepaction"]
> [Osvědčené postupy pro používání vyhledávací služby](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [Osvědčené postupy pro používání směrovací služby](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [Jak používat modul služby (Web SDK)](how-to-use-best-practices-for-routing.md)

> [!div class="nextstepaction"]
> [Referenční dokumentace k rozhraní API služby Azure Maps REST](https://docs.microsoft.com/rest/api/maps/)

> [!div class="nextstepaction"]
> [Ukázky kódu](https://docs.microsoft.com/samples/browse/?products=azure-maps)