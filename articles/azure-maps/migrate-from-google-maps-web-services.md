---
title: Migrace webových služeb z Google Maps | Mapy Microsoft Azure
description: Migrace webových služeb z Map Google na mapy Microsoft Azure
author: rbrundritt
ms.author: richbrun
ms.date: 08/19/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 57d71d517cb953a2a2c84b7e003fd08541416539
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319567"
---
# <a name="migrate-web-service-from-google-maps"></a>Migrace webové služby z Google Maps

Mapy Azure i Google poskytují přístup k prostorovým rozhraním API prostřednictvím webových služeb REST. Rozhraní API těchto platforem fungují podobně jako funkce. Ale každý z nich používá různé konvence pojmenování a objekty odpovědí.

V tabulce jsou uvedena rozhraní API služby Azure Maps, která mají podobnou funkci v uvedených rozhraních API služby Google Maps.

| Rozhraní API služby Google Maps | Rozhraní API služby Azure Maps                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| Pokyny              | [Trasa](https://docs.microsoft.com/rest/api/maps/route)                                     |
| Matice vzdáleností         | [Matice směrování](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)       |
| Geokódování               | [Hledání](https://docs.microsoft.com/rest/api/maps/search)                                   |
| Hledání míst           | [Hledání](https://docs.microsoft.com/rest/api/maps/search)                                   |
| Umístit automatické dokončování      | [Hledání](https://docs.microsoft.com/rest/api/maps/search)                                   |
| Přichycení k cestám            | Viz část [Výpočet tras a pokynů](#calculate-routes-and-directions) .            |
| Omezení rychlosti            | Přečtěte si oddíl [Reverse INCODE a souřadnice](#reverse-geocode-a-coordinate) .                  |
| Statická mapa              | [Vykreslování](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                       |
| Časové pásmo               | [Časové pásmo](https://docs.microsoft.com/rest/api/maps/timezone)                              |

Následující rozhraní API služby nejsou v tuto chvíli k dispozici v Azure Maps:

- Zvýšení oprávnění
- Geografická poloha
- Podrobnosti o místech a fotky – k dispozici jsou informace o telefonních číslech a adrese URL webu v rozhraní API pro hledání Azure Maps.
- Mapování adres URL
- Nejbližší cesty – to se dá dosáhnout pomocí webové sady SDK, jak je znázorněno [zde](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Basic%20snap%20to%20road%20logic
), ale momentálně není k dispozici jako služba.
- Statické zobrazení ulice

Azure Maps má několik dalších webových služeb REST, které mohou být zajímavé:

- [Prostorové operace](https://docs.microsoft.com/rest/api/maps/spatial): přesměrování složitých prostorových výpočtů a operací, jako je monitorování geografických zón, do služby.
- [Provoz](https://docs.microsoft.com/rest/api/maps/traffic): přístup k datům o toku provozu a incidentu v reálném čase.

## <a name="geocoding-addresses"></a>Adresy geografického kódování

Geografické kódování je proces převodu adresy na souřadnici. Například "1 Microsoft Way, Redmond, WA" se převede na zeměpisnou délku:-122,1298, zeměpisná šířka: 47,64005. Pak lze souřadnice použít pro různé druhy účelů, jako je například umístění středu značky na mapě.

Azure Maps poskytuje několik metod pro adresy geografického kódování:

- [**Geografické kódování adresy volného formátu**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress): zadejte jednu adresu řetězce a okamžitě zpracuje požadavek. "1 Microsoft Way, Redmond, WA" je příklad jednoho adresového řetězce. Toto rozhraní API se doporučuje v případě, že potřebujete jednotlivé adresy snadno kódovat.
- [**Geografické označování strukturovaných adres**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured): Určete části jedné adresy, jako je název ulice, město, země/oblast a poštovní směrovací číslo a okamžitě zpracujte požadavek. Toto rozhraní API se doporučuje, pokud potřebujete rychle identifikovat jednotlivé adresy a data už se analyzují na jednotlivé části adres.
- [**Geografické kódování adresy Batch**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview): vytvořte žádost obsahující až 10 000 adres a požádejte je o zpracování v časovém intervalu. Všechny adresy budou na serveru paralelně zavedeny a po dokončení bude možné stáhnout úplnou sadu výsledků. Tento postup je doporučený pro geografické sady velkých datových sad.
- [**Hledání přibližných**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)hodnot: Toto rozhraní API kombinuje geografické kódování s bodem hledání z hlediska zájmu. Toto rozhraní API přebírá řetězec ve volném formátu. Tento řetězec může být adresa, místo, orientační bod, bod zájmu nebo kategorie zájmu. Toto rozhraní API zpracovává žádost téměř v reálném čase. Toto rozhraní API se doporučuje pro aplikace, kde uživatelé hledají adresy nebo body zájmu ve stejném textovém poli.
- [**Nepřibližné dávkové vyhledávání**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): vytvoření žádosti obsahující až 10 000 adres, míst, orientačních bodů nebo bodů zájmu a jejich zpracování v časovém intervalu. Všechna data budou zpracována paralelně na serveru a po dokončení bude možné stáhnout úplnou sadu výsledků.

Následující tabulka odkazuje na parametry rozhraní API Google Maps pomocí srovnatelných parametrů rozhraní API v Azure Maps.

| Parametr rozhraní API pro Google Maps | Srovnatelný parametr Azure Maps rozhraní API  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` a `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality` – Město/město<br/>`municipalitySubdivision` – okolí, sub/super City<br/>`countrySubdivision` – stát nebo provincie<br/>`countrySecondarySubdivision` – Okres<br/>`countryTertiarySubdivision` – Okres<br/>`countryCode` – dvě číslice kód země/oblasti |
| `key`                       | `subscription-key` – Viz také [ověřování pomocí Azure Maps](azure-maps-authentication.md) dokumentaci. |
| `language`                  | `language` – Viz dokumentace k [podporovaným jazykům](supported-languages.md) .  |
| `region`                    | `countrySet`                       |

Příklad použití vyhledávací služby je popsán [zde](how-to-search-for-address.md). Nezapomeňte si projít [osvědčené postupy pro hledání](how-to-use-best-practices-for-search.md).

> [!TIP]
> Rozhraní API pro geografické kódování a přibližné vyhledávání lze použít v režimu automatického dokončování přidáním `&typeahead=true` na adresu URL požadavku. Tím se server upozorní na to, že je vstupní text nejspíš částečný, a hledání se vrátí do prediktivního režimu.

## <a name="reverse-geocode-a-coordinate"></a>Obrátit souřadnici pro INCODE

Opačné geografické kódování je proces převodu geografických souřadnic na přibližnou adresu. Koordinuje se "Zeměpisná délka:-122,1298, zeměpisná šířka: 47,64005" převést na "1 Microsoft Way, Redmond, WA".

Azure Maps poskytuje několik metod reverzního geografického kódování:

- [**Adresa reverzního INCODE pro zpětný**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)přístup: zadejte jednu geografickou souřadnici, která bude mít přibližnou adresu odpovídající této souřadnici. Zpracuje požadavek téměř v reálném čase.
- Informující o [**zpětném vyhledávání meziulici**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet): Určete jednu geografickou souřadnici, která bude dostávat informace o sousedních oblastech, a okamžitě zpracovat požadavek. Můžete například obdržet následující meziulic 1. a Main St.
- [**Adresa dávkového reverzního příznaku zpětného vyhledávání**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview): vytvořte žádost obsahující až 10 000 souřadnic a požádejte je o zpracování v časovém intervalu. Všechna data budou zpracována paralelně na serveru. Po dokončení žádosti si můžete stáhnout celou sadu výsledků.

Tato tabulka křížově odkazuje na parametry rozhraní API služby Google Maps s podobnými parametry rozhraní API v Azure Maps.

| Parametr rozhraní API pro Google Maps   | Srovnatelný parametr Azure Maps rozhraní API   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key` – Viz také [ověřování pomocí Azure Maps](azure-maps-authentication.md) dokumentaci. |
| `language`                  | `language` – Viz dokumentace k [podporovaným jazykům](supported-languages.md) .  |
| `latlng`                    | `query`  |
| `location_type`             | *NENÍ K DISPOZICI*     |
| `result_type`               | `entityType`    |

Projděte si [osvědčené postupy pro hledání](how-to-use-best-practices-for-search.md).

Rozhraní API pro invertování geografického kódování Azure Maps obsahuje některé další funkce, které nejsou k dispozici ve službě Google Maps. Tyto funkce můžou být užitečné při integraci s vaší aplikací při migraci vaší aplikace:

- Načte data omezení rychlosti.
- Načíst informace o využití provozu: místní silnice, Arterial, omezený přístup, rampa atd.
- Načíst stranu ulice, na které se nachází souřadnice

## <a name="search-for-points-of-interest"></a>Hledání bodů zájmu

Data z bodu zájmu můžete vyhledávat ve službě Google Maps pomocí rozhraní API pro vyhledávání míst. Toto rozhraní API nabízí tři různé způsoby hledání bodů zájmu:

- **Najít místo z textu:** Vyhledá bod zájmu na základě jeho názvu, adresy nebo telefonního čísla.
- **Nejbližší hledání**: vyhledá body zájmů, které spadají do určité vzdálenosti.
- **Hledání textu:** Vyhledá místa pomocí bezplatného textu, který obsahuje bod zájmu a informace o poloze. Například "pizza in Praha" nebo "restaurace poblíž Main St".

Azure Maps poskytuje několik rozhraní API pro hledání bodů zájmu:

- [**Hledání POI**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi): vyhledejte body zájmů podle jména. Například "Starbucks".
- [**Hledání kategorií POI**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory): vyhledejte body zájmu podle kategorie. Například "restaurace".
- [**Nejbližší hledání**](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby): vyhledá body zájmů, které spadají do určité vzdálenosti.
- [**Hledání přibližných**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)hodnot: Toto rozhraní API kombinuje geografické kódování s bodem hledání z hlediska zájmu. Toto rozhraní API přebírá řetězec ve volném formátu, který může být adresami, místem, orientačními body, bodem zájmu nebo kategorií zájmu. Tento požadavek zpracovává téměř v reálném čase. Toto rozhraní API se doporučuje pro aplikace, kde uživatelé hledají adresy nebo body zájmu ve stejném textovém poli.
- [**Hledání v geometrii**](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry): hledání bodů zájmů v zadané geometrii. Můžete například vyhledat bod zájmu v rámci mnohoúhelníku.
- [**Hledání v cestě**](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute): vyhledejte body, které jsou podél zadané cesty trasy.
- [**Nepřibližné dávkové vyhledávání**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): vytvořte žádost obsahující až 10 000 adres, míst, orientačních bodů nebo bodů zájmu. Žádost se zpracovala v časovém intervalu. Všechna data budou zpracována paralelně na serveru. Po dokončení zpracování žádosti si můžete stáhnout celou sadu výsledků.

V současné době Azure Maps nemá srovnatelné rozhraní API pro rozhraní API pro vyhledávání textu ve službě Google Maps.

> [!TIP]
> Poi vyhledávání, vyhledávání kategorií POI a rozhraní API pro hledání přibližné vyhledávání se dají použít v režimu automatického dokončování přidáním `&typeahead=true` na adresu URL požadavku. Tím se serveru říká, že je vstupní text nejspíš částečný. Rozhraní API provede hledání v prediktivním režimu.

Projděte si [osvědčené postupy pro dokumentaci pro hledání](how-to-use-best-practices-for-search.md) .

### <a name="find-place-from-text"></a>Najít místo z textu

Pomocí Azure Maps [POI hledání](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) a [přibližného vyhledávání](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) můžete hledat body zájmů podle jména nebo adresy.

Tabulka křížově odkazuje na parametry rozhraní API Google Maps pomocí srovnatelných parametrů Azure Maps rozhraní API.

| Parametr rozhraní API pro Google Maps | Srovnatelný parametr Azure Maps rozhraní API |
|---------------------------|-------------------------------------|
| `fields`                  | *NENÍ K DISPOZICI*                               |
| `input`                   | `query`                             |
| `inputtype`               | *NENÍ K DISPOZICI*                               |
| `key`                     | `subscription-key` – Viz také [ověřování pomocí Azure Maps](azure-maps-authentication.md) dokumentaci. |
| `language`                | `language` – Viz dokumentace k [podporovaným jazykům](supported-languages.md) .  |
| `locationbias`            | `lat``lon`a`radius`<br/>`topLeft` a `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>Okolní hledání

Pomocí rozhraní API pro [hledání v okolí](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) načtěte v Azure Maps okolní body zájmů.

V tabulce jsou uvedeny parametry rozhraní API služby Google Maps s podobnými parametry Azure Maps rozhraní API.

| Parametr rozhraní API pro Google Maps | Srovnatelný parametr Azure Maps rozhraní API  |
|---------------------------|--------------------------------------|
| `key`                       | `subscription-key` – Viz také [ověřování pomocí Azure Maps](azure-maps-authentication.md) dokumentaci. |
| `keyword`                   | `categorySet` a `brandSet`        |
| `language`                  | `language` – Viz dokumentace k [podporovaným jazykům](supported-languages.md) .  |
| `location`                  | `lat` a `lon`                     |
| `maxprice`                  | *NENÍ K DISPOZICI*                               |
| `minprice`                  | *NENÍ K DISPOZICI*                               |
| `name`                      | `categorySet` a `brandSet`        |
| `opennow`                   | *NENÍ K DISPOZICI*                               |
| `pagetoken`                 | `ofs` a `limit`                   |
| `radius`                    | `radius`                            |
| `rankby`                    | *NENÍ K DISPOZICI*                               |
| `type`                      | `categorySet –` Viz dokumentace k [kategoriím hledání podporované](supported-search-categories.md) .   |

## <a name="calculate-routes-and-directions"></a>Vypočítat trasy a směry

Vypočítejte trasy a pokyny pomocí Azure Maps. Azure Maps má mnoho stejných funkcí jako směrovací služba Google Maps, například:

- Časy doručení a odchodu.
- Trasy provozu v reálném čase a prediktivní provoz.
- Různé režimy dopravy. Například řízení, procházení, cyklistice.

> [!NOTE]
> Azure Maps vyžaduje, aby všechny Waypoints byly souřadnice. Adresy musí být nejprve v INCODE.

Služba směrování Azure Maps poskytuje následující rozhraní API pro výpočet tras:

- [**Vypočítat trasu**](https://docs.microsoft.com/rest/api/maps/route/getroutedirections): Vypočítejte trasu a ihned zpracuje požadavek. Toto rozhraní API podporuje žádosti GET i POST. Žádosti POST se doporučují při zadání velkého počtu Waypoints nebo při použití spousty možností směrování, abyste zajistili, že se požadavek na adresu URL nestane příliš dlouhý a způsobuje problémy. Směr následné trasy v Azure Maps má možnost přebírat tisíce [pomocných bodů](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#supportingpoints) a bude je používat k opětovnému vytvoření cesty logické trasy mezi nimi (přichycení k cestě). 
- [**Dávková trasa**](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview): vytvořte žádost obsahující až 1 000 žádosti o trasu a požádejte ji o zpracování v časovém intervalu. Všechna data budou zpracována paralelně na serveru a po dokončení bude možné stáhnout úplnou sadu výsledků.
- [**Služby mobility**](https://docs.microsoft.com/rest/api/maps/mobility): Vypočítejte trasy a směry pomocí veřejného přenosu.

Tabulka křížově odkazuje na parametry rozhraní API Google Maps s podobnými parametry rozhraní API v Azure Maps.

| Parametr rozhraní API pro Google Maps    | Srovnatelný parametr Azure Maps rozhraní API  |
|------------------------------|--------------------------------------|
| `alternatives`                 | `maxAlternatives`                  |
| `arrival_time`                | `arriveAt`                          |
| `avoid`                        | `avoid`                            |
| `departure_time`              | `departAt`                          |
| `destination`                  | `query` – souřadnice ve formátu `"lat0,lon0:lat1,lon1…."`  |
| `key`                          | `subscription-key` – Viz také [ověřování pomocí Azure Maps](azure-maps-authentication.md) dokumentaci. |
| `language`                     | `language` – Viz dokumentace k [podporovaným jazykům](supported-languages.md) .   |
| `mode`                         | `travelMode`                       |
| `optimize`                     | `computeBestOrder`                 |
| `origin`                       | `query`                            |
| `region`                       | Není *k dispozici* – Tato funkce se týká geografického kódování. Při použití Azure Maps rozhraní API pro geografické kódování použijte parametr *countrySet* .  |
| `traffic_model`               | Není *k dispozici* – dá se zadat jenom v případě, že se mají použít data přenosů s parametrem *provozu* . |
| `transit_mode`                | Viz [dokumentace ke službě mobility](https://docs.microsoft.com/rest/api/maps/mobility) |
| `transit_routing_preference` | Viz [dokumentace ke službě mobility](https://docs.microsoft.com/rest/api/maps/mobility) |
| `units`                        | Není *k dispozici* – Azure Maps používá pouze systém metrik.  |
| `waypoints`                    | `query`                            |

> [!TIP]
> Ve výchozím nastavení vrací rozhraní API trasy Azure Maps pouze souhrn. Vrátí vzdálenost a časy a souřadnice pro cestu trasy. Pomocí `instructionsType` parametru načtěte pokyny pro zapínání. A použijte `routeRepresentation` parametr pro odfiltrování cesty souhrnu a trasy.

Rozhraní API pro směrování Azure Maps má další funkce, které nejsou dostupné ve službě Google Maps. Při migraci vaší aplikace zvažte použití těchto funkcí, které můžete najít užitečné.

- Podpora pro typ směrování: nejkratší, nejrychlejší, Trilling a většina pohonných hmot.
- Podpora dalších způsobů cestovního režimu: sběrnice, motocykl, taxislužby, nákladní vůz a van.
- Podpora 150 Waypoints.
- Výpočet více dob cestování v rámci jediné žádosti; historické přenosy, živý provoz bez provozu.
- Vyhněte se dalším typům cest: carpool silnice, Unpaved silnice, již využité silnice.
- Zadejte vlastní oblasti, abyste se vyhnuli.
- Omezte zvýšení oprávnění, které trasou může být Ascend.
- Směrování na základě specifikací stroje. Vypočítat trasy pro spalovací nebo elektrické vozidla na základě specifikací motoru a zbývajícího paliva nebo poplatku.
- Podporuje parametry směrování komerčních vozidel. Jako jsou například rozměry vozidla, váhy, počet Axels a typ nákladu.
- Zadejte maximální rychlost vozidla.

Kromě toho služba Směrování v Azure Maps podporuje [výpočet rozsahů směrování](https://docs.microsoft.com/rest/api/maps/route/getrouterange). Výpočet rozsahů směrování se označuje také jako izochronů. Zahrnuje generování mnohoúhelníku, který pokrývá oblast, na kterou se dá cestovat v libovolném směru od počátečního bodu. V rámci stanoveného množství času nebo objemu paliva nebo poplatků.

Projděte si [osvědčené postupy pro](how-to-use-best-practices-for-routing.md) dokumentaci ke směrování.

## <a name="retrieve-a-map-image"></a>Načtení obrázku mapy

Azure Maps poskytuje rozhraní API pro vykreslování statických imagí map s překrývajícími se daty. Rozhraní API pro [vykreslování obrázků map](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) v Azure Maps je SROVNATELNÉ s rozhraním API statické mapy v Mapách Google Maps.

> [!NOTE]
> Azure Maps vyžaduje, aby bylo uprostřed, všechna značka a umístění cesty souřadnice ve formátu "Zeměpisná šířka". Vzhledem k tomu, že Google Maps používá formát Zeměpisná šířka, zeměpisná délka. Adresy musí být nejprve v INCODE.

Tabulka křížově odkazuje na parametry rozhraní API Google Maps s podobnými parametry rozhraní API v Azure Maps.

| Parametr rozhraní API pro Google Maps | Srovnatelný parametr Azure Maps rozhraní API  |
|---------------------------|--------------------------------------|
| `center`                    | `center`                           |
| `format`                    | `format` – zadáno jako součást cesty URL. V současné době je podporována pouze PNG. |
| `key`                       | `subscription-key` – Viz také [ověřování pomocí Azure Maps](azure-maps-authentication.md) dokumentaci. |
| `language`                  | `language` – Viz dokumentace k [podporovaným jazykům](supported-languages.md) .  |
| `maptype`                   | `layer` a `style` – Viz dokumentace k [podporovaným stylům mapy](supported-map-styles.md) . |
| `markers`                   | `pins`                             |
| `path`                      | `path`                             |
| `region`                    | Není *k dispozici* – jedná se o funkci související s geografické kódování. Použijte `countrySet` parametr při použití Azure Maps rozhraní API pro geografické kódování.  |
| `scale`                     | *NENÍ K DISPOZICI*                              |
| `size`                      | `width` a `height` – může mít velikost až 8192x8192. |
| `style`                     | *NENÍ K DISPOZICI*                              |
| `visible`                   | *NENÍ K DISPOZICI*                              |
| `zoom`                      | `zoom`                             |

> [!NOTE]
> V systému Azure Maps dlaždice jsou dlaždice dvakrát velikosti dlaždic rozvržení používaných v Google Maps. Vzhledem k tom, že hodnota úrovně přiblížení v Azure Maps, se v porovnání se službou Google Maps zobrazí jedna úroveň přiblížení v Azure Maps. Chcete-li tento rozdíl kompenzovat, zmenšete úroveň přiblížení v rámci požadavků, které migrujete.

Další informace najdete v příručce k [rozhraní API pro vykreslování obrázků mapy](how-to-render-custom-data.md).

Kromě toho, že je možné vygenerovat statický obrázek mapy, služba Azure Maps rendering poskytuje možnost přímý přístup k dlaždicím mapy v rastrovém formátu (PNG) a ve vektorovém formátu:

- [**Dlaždice mapy**](https://docs.microsoft.com/rest/api/maps/render/getmaptile): načíst rastrový obrázek (PNG) a vektorové dlaždice pro základní mapy (cesty, hranice, pozadí).
- [**Dlaždice map snímků**](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)– obrázek: načtení dlaždic satelitních a satelitních snímků

> [!TIP]
> Řada aplikací pro Google Maps, kde se před několika lety přepne z interaktivního mapového prostředí do statických imagí. To bylo provedeno jako metoda úspory nákladů. V Azure Maps je obvykle cenově výhodnější používat ovládací prvek interaktivní mapování v sadě web SDK. Interaktivní ovládací prvky mapy se účtují podle počtu načtených dlaždic. Mapové dlaždice v Azure Maps jsou velké. Často trvá pouze několik dlaždic pro opětovné vytvoření stejného zobrazení mapy jako statické mapy. Mapové dlaždice jsou automaticky ukládány v prohlížeči. V takovém případě interaktivní ovládací prvek mapování často generuje zlomek transakce při reprodukci statického zobrazení mapy. Posouvání a přiblížení načte další dlaždice. Nicméně existují možnosti v mapovém ovládacím prvku pro zakázání tohoto chování. Interaktivní ovládací prvek mapy také nabízí mnohem více možností vizualizace než statické mapové služby.

### <a name="marker-url-parameter-format-comparison"></a>Porovnání formátu parametru adresy URL značky

**Před: Google Maps**

Přidejte značky pomocí `markers` parametru v adrese URL. `markers`Parametr přebírá ve stylu a seznam umístění, které mají být vykresleny na mapě s tímto stylem, jak je znázorněno níže:

```
&markers=markerStyles|markerLocation1|markerLocation2|...
```

Chcete-li přidat další styly, použijte `markers` Parametry adresy URL s jiným stylem a sadou umístění.

Zadejte umístění značek s formátem Zeměpisná šířka, zeměpisná délka.

Přidejte styly značek ve `optionName:value` formátu s několika styly oddělenými znaky svislé čáry ( \| ), jako je například "optionName1: hodnota1 \| optionName2: hodnota2". Všimněte si, že názvy možností a hodnoty jsou oddělené dvojtečkou (:). Pro značky stylu v Google Maps použijte následující názvy možností stylu:

- `color` – Barva výchozí ikony značky Může být 24bitové hexadecimální barva ( `0xrrggbb` ) nebo jedna z následujících hodnot: `black` , `brown` , `green` , `purple` , `yellow` , `blue` , `gray` , `orange` , `red` , `white` .
- `label` – Jedním velkým alfanumerickým znakem, který se zobrazí nad ikonu.
- `size` – Velikost značky. Může být `tiny` , `mid` , nebo `small` .

Pro vlastní ikony v Google Maps použijte následující názvy možností stylu:

- `anchor` – Určuje způsob zarovnání obrázku ikony na souřadnici. Může to být hodnota pixel (x, y) nebo jedna z následujících hodnot: `top`, `bottom` , `left` , `right` , `center` , `topleft` , `topright` , `bottomleft` nebo `bottomright` .
- `icon` – Adresa URL ukazující na obrázek ikony.

Řekněme například, že na mapu přidáte červenou značku střední velikosti:-110, zeměpisná šířka: 45:

```
&markers=color:red|size:mid|45,-110
```


![Značka Google Maps](media/migrate-google-maps-web-services/google-maps-marker.png)

**Po: Azure Maps**

Přidejte značky do statického obrázku mapy zadáním `pins` parametru v adrese URL. Podobně jako Google Maps Určete styl a seznam umístění v parametru. `pins`Parametr lze zadat vícekrát pro podporu značek s různými styly.

```
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

Chcete-li použít další styly, přidejte `pins` do adresy URL další parametry s jiným stylem a sadou umístění.

V Azure Maps musí být umístění kódu PIN ve formátu "Zeměpisná šířka". Google Maps používá formát "Zeměpisná délka". Mezera, nejedná se o čárku, která odděluje zeměpisnou a zeměpisnou šířku ve formátu Azure Maps.

`iconType`Určuje typ PIN kódu, který se má vytvořit. Může mít následující hodnoty:

- `default` – Výchozí ikona PIN
- `none` – Není zobrazena žádná ikona, budou vykresleny pouze popisky.
- `custom` – Určuje vlastní ikonu, která se má použít. Adresa URL ukazující na obrázek ikony může být přidána na konec `pins` parametru za informace o umístění kódu PIN.
- `{udid}` – Jedinečné ID dat (UDID) pro ikonu uloženou v Azure Maps platformě úložiště dat.

Přidejte styly kódu PIN ve `optionNameValue` formátu. Oddělte více stylů znaky svislé čáry ( \| ). Například: `iconType|optionName1Value1|optionName2Value2`. Hodnoty a názvy možností nejsou oddělené. Pro značky stylu použijte následující názvy možností stylu:

- `al` – Určuje neprůhlednost (alfa) značky. Vyberte číslo mezi 0 a 1.
- `an` – Určuje kotvu PIN. Zadejte hodnoty X a y pixelů ve formátu "X y".
- `co` – Barva kódu PIN. Zadejte 24bitové hexadecimální barvu: `000000` na `FFFFFF` .
- `la` – Určuje kotvu popisku. Zadejte hodnoty X a y pixelů ve formátu "X y".
- `lc` – Barva popisku. Zadejte 24bitové hexadecimální barvu: `000000` na `FFFFFF` .
- `ls` – Velikost popisku v pixelech. Vyberte číslo větší než 0.
- `ro` – Hodnota ve stupních pro otočení ikony. Vyberte číslo v rozmezí od-360 do 360.
- `sc` – Hodnota měřítka pro ikonu připnutí. Vyberte číslo větší než 0.

Zadejte hodnoty popisků pro každé umístění kódu PIN. Tento přístup je efektivnější než použití jedné hodnoty popisku na všechny značky v seznamu umístění. Hodnota popisku může být řetězec s více znaky. Zabalte řetězec s jednoduchými uvozovkami, aby se zajistilo, že není možné ho nahradit jako styl nebo hodnotu umístění.

Pojďme přidat výchozí ikonu Red ( `FF0000` ) s popiskem "místo ručičky", která je umístěná níže (15 50). Ikona má délku:-122,349300, zeměpisná šířka: 47,620180:

```
&pins=default|coFF0000|la15 50||'Space Needle' -122.349300 47.620180
```


![Značka Azure Maps](media/migrate-google-maps-web-services/azure-maps-marker.png)

Přidejte tři PIN kódy s hodnotami popisku "1", "2" a "3":

```
&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12
```



![Azure Maps více značek](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)

### <a name="path-url-parameter-format-comparison"></a>Porovnání formátu parametru adresy URL cesty

**Před: Google Maps**

Přidejte čáry a mnohoúhelník na statický obrázek mapy pomocí `path` parametru v adrese URL. `path`Parametr přebírá ve stylu a seznam umístění, které mají být vykresleny na mapě, jak je znázorněno níže:

```
&path=pathStyles|pathLocation1|pathLocation2|...
```

Další styly můžete přidat přidáním dalších `path` parametrů k adrese URL s jiným stylem a sadou umístění.

Umístění cesty jsou zadána ve `latitude1,longitude1|latitude2,longitude2|…` formátu. Cesty můžou být kódované nebo obsahují adresy pro body.

Přidejte styly cest ve `optionName:value` formátu a oddělte více stylů znaky kanálu ( \| ). Názvy a hodnoty možností oddělujte dvojtečkou (:). Takto: `optionName1:value1|optionName2:value2` . Následující názvy možností stylu lze použít ke stylování cest v Google Maps:

- `color` – Barva obrysu cesty nebo mnohoúhelníku. Může být 24bitové hexadecimální barva ( `0xrrggbb` ), 32 šestnáctková barva ( `0xrrggbbbaa` ) nebo jedna z následujících hodnot: černá, hnědá, zelená, fialová, žlutá, modrá, šedá, oranžová, červená a bílá.
- `fillColor` – Barva, kterou chcete vyplnit oblast cesty (mnohoúhelník). Může být 24bitové hexadecimální barva ( `0xrrggbb` ), 32 šestnáctková barva ( `0xrrggbbbaa` ) nebo jedna z následujících hodnot: černá, hnědá, zelená, fialová, žlutá, modrá, šedá, oranžová, červená a bílá.
- `geodesic` – Určuje, zda by měla být cesta čára, která následuje zakřivení země.
- `weight` – Tloušťka čáry cesty v pixelech

Přidejte neprůhlednost červené čáry a tloušťku pixelů na mapu mezi souřadnicemi v parametru URL. V příkladu níže má čára neprůhlednost 50% a tloušťku čtyř pixelů. Souřadnice jsou zeměpisná délka:-110, zeměpisná šířka: 45 a zeměpisná délka:-100, zeměpisná šířka: 50.

```
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

![Lomená čáry Google Maps](media/migrate-google-maps-web-services/google-maps-polyline.png)

**Po: Azure Maps**

Přidejte čáry a mnohoúhelníky do statického obrázku mapy zadáním `path` parametru v adrese URL. Podobně jako u Map Google Určete styl a seznam umístění v tomto parametru. Zadejte `path` parametr vícekrát pro vykreslení více kruhů, čar a mnohoúhelníků s různými styly.

```
&path=pathStyles||pathLocation1|pathLocation2|...
```

Když přichází do umístění cest, Azure Maps vyžaduje, aby souřadnice byly ve formátu "Zeměpisná šířka". Google Maps používá formát "Zeměpisná délka". Mezera, nejedná se o čárku, která odděluje zeměpisnou a zeměpisnou šířku ve formátu Azure Maps. Azure Maps nepodporuje kódované cesty nebo adresy pro body. Nahrajte větší sady dat jako soubor s příponou. JSON do rozhraní Azure Maps API pro úložiště dat, jak je popsáno [zde](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage).

Přidejte styly cest ve `optionNameValue` formátu. Rozdělte více stylů podle svislých ( \| ) znaků, jako je to `optionName1Value1|optionName2Value2` . Hodnoty a názvy možností nejsou oddělené. Pro styly cest v Azure Maps použijte následující názvy možností stylu:

- `fa` – Neprůhlednost barvy výplně (alfa), která se používá při vykreslování mnohoúhelníků. Vyberte číslo mezi 0 a 1.
- `fc` – Barva výplně použitá k vykreslení oblasti mnohoúhelníku
- `la` – Barva čáry neprůhlednosti (alfa), která se používá při vykreslování čar a obrysu mnohoúhelníků. Vyberte číslo mezi 0 a 1.
- `lc` – Barva čáry použitá k vykreslení čar a obrysu mnohoúhelníků.
- `lw` – Šířka čáry v pixelech.
- `ra` – Určuje poloměr kroužků v měřičích.

V parametru URL přidejte neprůhlednost červené čáry a tloušťku pixelů mezi souřadnicemi. Pro příklad níže má čára 50% krytí a tloušťku čtyř pixelů. Souřadnice mají následující hodnoty: Zeměpisná délka:-110, zeměpisná šířka: 45 a zeměpisná délka:-100, zeměpisná šířka: 50.

```
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

![Azure Maps lomená čáry](media/migrate-google-maps-web-services/azure-maps-polyline.png)

## <a name="calculate-a-distance-matrix"></a>Vypočítat matici vzdálenosti

Azure Maps poskytuje rozhraní API pro matici Distance. Pomocí tohoto rozhraní API můžete vypočítat dobu trvání cesty a vzdálenosti mezi sadou umístění s maticí s vzdáleností. Je srovnatelná s rozhraním API služby Distance v Google Maps.

- [**Matice směrování**](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview): asynchronně vypočítá dobu trvání cesty a vzdálenosti pro sadu míst původu a místa. Podporuje až 700 buněk na žádost. To je počet původních vynásobený počtem cílů. S tímto omezením je třeba mít příklady možných dimenzí matice: 700x1, 50x10, 10x10, 28x25, 10x70.

> [!NOTE]
> Požadavek na rozhraní API na dálku se dá vytvořit jenom pomocí žádosti POST s informacemi o původu a cíli v těle žádosti. Azure Maps navíc vyžaduje, aby všechny zdroje a cíle byly souřadnice. Adresy musí být nejprve v INCODE.

Tato tabulka křížově odkazuje na parametry rozhraní API služby Google Maps pomocí srovnatelných parametrů Azure Maps rozhraní API.

| Parametr rozhraní API pro Google Maps      | Srovnatelný parametr Azure Maps rozhraní API  |
|--------------------------------|--------------------------------------|
| `arrivial_time`                | `arriveAt`                           |
| `avoid`                        | `avoid`                              |
| `depature_time`                | `departAt`                           |
| `destinations`                 | `destination` – Zadejte v textu požadavku POST jako text typu injson. |
| `key`                          | `subscription-key` – Viz také [ověřování pomocí Azure Maps](azure-maps-authentication.md) dokumentaci. |
| `language`                     | `language` – Viz dokumentace k [podporovaným jazykům](supported-languages.md) .  |
| `mode`                         | `travelMode`                         |
| `origins`                      | `origins` – Zadejte v textu požadavku POST jako text typu injson.  |
| `region`                       | Není *k dispozici* – Tato funkce se týká geografického kódování. Použijte `countrySet` parametr při použití Azure Maps rozhraní API pro geografické kódování. |
| `traffic_model`                | Není *k dispozici* – lze určit, zda mají být v parametru použity data přenosů `traffic` . |
| `transit_mode`                 | V současné době *se* nepodporují meziměstské matice na bázi přenosu.  |
| `transit_routing_preference`   | V současné době *se* nepodporují meziměstské matice na bázi přenosu.  |
| `units`                        | Není *k dispozici* – Azure Maps používá pouze systém metrik. |

> [!TIP]
> Všechny možnosti pokročilého směrování, které jsou dostupné v rozhraní API směrování Azure Maps, jsou podporované v rozhraní API Azure Maps Distance. Mezi pokročilé možnosti směrování patří: směrování nákladní automobil, specifikace stroje a tak dále.

Projděte si [osvědčené postupy pro](how-to-use-best-practices-for-routing.md) dokumentaci ke směrování.

## <a name="get-a-time-zone"></a>Získat časové pásmo

Azure Maps poskytuje rozhraní API pro načtení časového pásma souřadnic. Rozhraní API pro časové pásmo Azure Maps je srovnatelné s rozhraním API pro časové pásmo v Google Maps:

- [**Časové pásmo podle souřadnic**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates): Určete souřadnici a přijímají podrobnosti o časovém pásmu pro souřadnici.

Tato tabulka křížově odkazuje na parametry rozhraní API služby Google Maps s podobnými parametry rozhraní API v Azure Maps.

| Parametr rozhraní API pro Google Maps | Srovnatelný parametr Azure Maps rozhraní API   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key` – Viz také [ověřování pomocí Azure Maps](azure-maps-authentication.md) dokumentaci.       |
| `language`                  | `language` – Viz dokumentace k [podporovaným jazykům](supported-languages.md) .    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

Kromě tohoto rozhraní API Azure Maps poskytuje řadu rozhraní API pro časové pásmo. Tato rozhraní API převádějí čas na základě názvů nebo ID časového pásma:

- [**Časové pásmo podle ID**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebyid): vrátí aktuální, historické a budoucí informace o časovém pásmu pro zadané ID časového pásma IANA.
- Identifikátor [**úřadu pro vyčíslení časového pásma**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumiana): vrátí úplný seznam ID časových pásem IANA. Aktualizace služby IANA se v systému projeví v rámci jednoho dne.
- [**Okna výčtu časových pásem**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumwindows): vrátí úplný seznam ID časových pásem systému Windows.
- [**Verze IANA časového pásma**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneianaversion): vrátí aktuální číslo verze iana používané Azure Maps.
- [**Časová pásma Windows to IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonewindowstoiana): vrátí odpovídající ID IANA, které má přidělené platné ID časového pásma systému Windows. Pro jedno ID systému Windows může být vráceno více ID IANA.

## <a name="client-libraries"></a>Klientské knihovny

Azure Maps poskytuje klientské knihovny pro následující programovací jazyky:

- JavaScript, TypeScript, Node.js – [documentation](how-to-use-services-module.md) \| [balíček dokumentace npm](https://www.npmjs.com/package/azure-maps-rest)

Tyto open source klientské knihovny jsou pro jiné programovací jazyky:

- .NET Standard 2,0 – [GitHub project](https://github.com/perfahlen/AzureMapsRestServices) \| [balíček NuGet](https://www.nuget.org/packages/AzureMapsRestToolkit/) pro projekt GitHubu

## <a name="additional-resources"></a>Další zdroje

Níže najdete další dokumentaci a prostředky pro služby Azure Maps REST.

- [Osvědčené postupy pro hledání](how-to-use-best-practices-for-search.md)
- [Hledání adresy](how-to-search-for-address.md)
- [Osvědčené postupy pro směrování](how-to-use-best-practices-for-routing.md)
- [Referenční dokumentace k rozhraní API služby Azure Maps REST](https://docs.microsoft.com/rest/api/maps/)
- [Ukázky kódu](https://docs.microsoft.com/samples/browse/?products=azure-maps)
- [Jak používat modul služby (Web SDK)](how-to-use-best-practices-for-routing.md)