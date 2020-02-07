---
title: 'Kurz: Migrace webových služeb z Google Maps | Mapy Microsoft Azure'
description: Migrace webových služeb z Map Google na Microsoft Azure Maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 50bdc0722328f857279b2cbd9a6e4cee740b9df8
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048929"
---
# <a name="migrate-web-service-from-google-maps"></a>Migrace webové služby z Google Maps

Azure Maps a Google Maps poskytují přístup k prostorovým rozhraním API prostřednictvím webových služeb REST. Rozhraní API těchto dvou platforem provádějí podobné funkce, ale každý z nich používá různé zásady vytváření názvů a objekty odpovědí.

Následující tabulka ukazuje rozhraní API služby Azure Maps, které poskytuje podobnou funkci rozhraní API služby Google Maps.

| Rozhraní API služby Google Maps | Rozhraní API služby Azure Maps                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| Značení              | [Cestě](https://docs.microsoft.com/rest/api/maps/route)                               |
| Matice vzdáleností         | [Matice směrování](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview) |
| Geokódování               | [Search](https://docs.microsoft.com/rest/api/maps/search)                             |
| Hledání míst           | [Search](https://docs.microsoft.com/rest/api/maps/search)                             |
| Umístit automatické dokončování      | [Search](https://docs.microsoft.com/rest/api/maps/search)                             |
| Statická mapa              | [Činit](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                 |
| Časové pásmo               | [Časové pásmo](https://docs.microsoft.com/rest/api/maps/timezone)                        |

Následující rozhraní API služby nejsou v tuto chvíli k dispozici v Azure Maps:

- Zvýšení oprávnění
- Zeměpisná poloha
- Umístěte podrobnosti a umístěte fotografie. Telefonní čísla a adresa URL webu jsou k dispozici v rozhraní API pro hledání Azure Maps.
- Mapování adres URL
- Cestách. Data omezení rychlosti jsou k dispozici prostřednictvím trasy a reverzní rozhraní API pro geografické kódování v Azure Maps.
- Statické zobrazení ulice

Azure Maps má několik dalších webových služeb REST, které mohou být zajímavé:

- [Prostorové operace](https://docs.microsoft.com/rest/api/maps/spatial): přesměrování složitých prostorových výpočtů a operací, jako je monitorování geografických zón, do služby.
- [Provoz](https://docs.microsoft.com/rest/api/maps/traffic): přístup k datům o toku provozu a incidentu v reálném čase.

## <a name="geocoding-addresses"></a>Adresy geografického kódování

Geografické kódování je proces převodu adresy na souřadnici. Například "1 Microsoft Way, Redmond, WA" se převede na "Zeměpisná délka:-122,1298, zeměpisná šířka: 47,64005". Souřadnice jsou nezbytné k umístění značky na mapě nebo středu mapy.

Azure Maps poskytuje několik metod pro adresy geografického kódování:

- [**Geografické kódování adresy volného formátu**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress): zadejte jednu adresu řetězce a okamžitě zpracuje požadavek. Jedna adresa řetězce je "1 Microsoft Way, Redmond, WA". Tato metoda je doporučena v případě, že je nutné rychle kódovat jednotlivé adresy.
- [**Geografické kódování strukturovaných adres**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured): Určete části jedné adresy a Zpracujte žádost v reálném čase. Mezi části adresy patří název ulice, město, země a poštovní směrovací číslo. Tato metoda se doporučuje pro dva hlavní scénáře. Data jsou již analyzována jako části jednotlivých adres. Nebo musíte jednotlivé adresy snadno kódovat.
- [**Geografické kódování adresy Batch**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview): vytvořte žádost obsahující až 10 000 adres a Zpracujte žádost v časovém intervalu. Všechny adresy budou na serveru paralelně zakódovatelné. Po dokončení geografického kódování se celá sada výsledků bude stahovat. Tato metoda je doporučena pro geografické sady velkých datových sad.
- [**Hledání přibližných**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)hodnot: Toto rozhraní API kombinuje geografické kódování s bodem hledání v rámci zájmu. Toto rozhraní API přebírá řetězec ve volném formátu a zpracovává žádost prakticky v reálném čase. Řetězec volného formátu může být adresa, místo, orientační bod, bod zájmu nebo kategorie zájmu. Toto rozhraní API se doporučuje, když se pro dotazování adres a bodů zájmu používá stejné textové pole.
- [**Nepřibližné dávkové vyhledávání**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): vytvoření žádosti obsahující až 10 000 adres a zpracování žádosti v časovém intervalu. Můžete požadovat místa, orientační body nebo zájmy. Všechna data budou zpracována na serveru a paralelně. Po dokončení se celá sada výsledků bude stahovat.

Následující tabulka odkazuje na parametry rozhraní API Google Maps pomocí srovnatelných parametrů rozhraní API v Azure Maps.

| Parametr rozhraní API pro Google Maps | Srovnatelný parametr Azure Maps rozhraní API  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` a `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality` – City/město<br/>`municipalitySubdivision` – okolí, sub/super City<br/>`countrySubdivision` – kraj<br/>`countrySecondarySubdivision` – okres<br/>`countryTertiarySubdivision` – oblast<br/>`countryCode` – kód země pro dva číslice |
| `key`                       | `subscription-key` – viz také dokumentace k [ověřování pomocí Azure Maps](azure-maps-authentication.md) . |
| `language`                  | `language` – viz dokumentace k [podporovaným jazykům](supported-languages.md) .  |
| `region`                    | `countrySet`                       |

Příklad použití vyhledávací služby je popsán [zde](how-to-search-for-address.md). Nezapomeňte si projít [osvědčené postupy pro hledání](how-to-use-best-practices-for-search.md).

> [!TIP]
> V režimu automatického dokončování je možné použít rozhraní API pro geografické kódování a přibližné vyhledávání, a to přidáním `&amp;typeahead=true` k adrese URL požadavku. Tím se server upozorní na to, že je vstupní text nejspíš částečný, a hledání se vrátí do prediktivního režimu.

## <a name="reverse-geocode-a-coordinate"></a>Obrátit souřadnici pro INCODE

Opačné geografické kódování je proces převodu geografických souřadnic na přibližnou adresu. Koordinuje se "Zeměpisná délka:-122,1298, zeměpisná šířka: 47,64005" převést na "1 Microsoft Way, Redmond, WA".

Azure Maps poskytuje několik metod reverzního geografického kódování:

- [**Adresa reverzního INCODE pro zpětný**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)přístup: zadejte jednu geografickou souřadnici, která má získat přibližnou adresu odpovídající této souřadnici. Zpracujte žádost v blízkosti reálného času.
- [**INCODE Reverted pro meziulici**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet): Určete jednotlivou geografickou souřadnici a načtěte informace o okolí z okolního meziulici. Zpracujte žádost v blízkosti reálného času.
- [**Adresa dávkového reverzního příznaku zpětného vyhledávání**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview): vytvořte žádost obsahující až 10 000 souřadnic a v časovém intervalu zpracovala požadavek. Všechna data budou zpracována paralelně na serveru. Po dokončení se celá sada výsledků bude stahovat.

Následující tabulka odkazuje na parametry rozhraní API Google Maps pomocí srovnatelných parametrů rozhraní API v Azure Maps.

| Parametr rozhraní API pro Google Maps   | Srovnatelný parametr Azure Maps rozhraní API   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key` – viz také dokumentace k [ověřování pomocí Azure Maps](azure-maps-authentication.md) . |
| `language`                  | `language` – viz dokumentace k [podporovaným jazykům](supported-languages.md) .  |
| `latlng`                    | `query`  |
| `location_type`             | *–*     |
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
- [**Hledání přibližných**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)hodnot: Toto rozhraní API kombinuje geografické kódování s bodem hledání z hlediska zájmu. Toto rozhraní API přebírá řetězec ve volném formátu. Adresa, místo, orientační bod, bod zájmu, kategorie zájmu a tak dále. Toto rozhraní API může zpracovat žádost prakticky v reálném čase. Toto rozhraní API se doporučuje, když uživatelé hledají adresy nebo body zájmu pomocí stejného textového pole.
- [**Hledání v geometrii**](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry): hledání bodů zájmů v rámci zadaného geometrie (mnohoúhelník).
- [**Hledání v cestě**](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute): vyhledejte body, které jsou podél zadané cesty trasy.
- [**Nepřibližné dávkové vyhledávání**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): vytvoření žádosti obsahující až 10 000 adres, míst, orientačních bodů nebo bodů zájmu a jejich zpracování v časovém intervalu. Všechna data budou zpracována paralelně na serveru. Po dokončení zpracování žádosti si stáhněte úplnou sadu výsledků.

V současné době Azure Maps nemá srovnatelné rozhraní API pro rozhraní API pro vyhledávání textu ve službě Google Maps.

> [!TIP]
> Rozhraní API pro vyhledávání POI, rozhraní API pro vyhledávání POI kategorií a Přibližná vyhledávací rozhraní API se dají použít v režimu automatického dokončování. Přidejte `&amp;typeahead=true` k adrese URL požadavku. Tím se server upozorní na to, že je vstupní text pravděpodobně částečný, a hledání bude dokončeno v prediktivním režimu.

Projděte si [osvědčené postupy pro hledání](how-to-use-best-practices-for-search.md).

### <a name="find-place-from-text"></a>Najít místo z textu

Použijte Azure Maps rozhraní API pro [hledání POI](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) nebo [přibližné vyhledávací](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) rozhraní API pro hledání bodů zájmu podle jména nebo adresy.

V následující tabulce jsou uvedeny parametry rozhraní API služby Google Maps s příslušnými parametry Azure Maps API.

| Parametr rozhraní API pro Google Maps | Srovnatelný parametr Azure Maps rozhraní API |
|---------------------------|-------------------------------------|
| `fields`                  | *–*                               |
| `input`                   | `query`                             |
| `inputtype`               | *–*                               |
| `key`                     | `subscription-key` – viz také dokumentace k [ověřování pomocí Azure Maps](azure-maps-authentication.md) . |
| `language`                | `language` – viz dokumentace k [podporovaným jazykům](supported-languages.md) .  |
| `locationbias`            | `lat`, `lon` a `radius`<br/>`topLeft` a `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>Okolní hledání

V Azure Maps načtěte okolní body zájmů pomocí rozhraní API pro [hledání v okolí](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) .

Následující tabulka odkazuje na parametry rozhraní API Google Maps pomocí parametrů Azure Maps API.

| Parametr rozhraní API pro Google Maps | Srovnatelný parametr Azure Maps rozhraní API  |
|---------------------------|--------------------------------------|
| `key`                       | `subscription-key` – viz také dokumentace k [ověřování pomocí Azure Maps](azure-maps-authentication.md) . |
| `keyword`                   | `categorySet` a `brandSet`        |
| `language`                  | `language` – viz dokumentace k [podporovaným jazykům](supported-languages.md) .  |
| `location`                  | `lat` a `lon`                     |
| `maxprice`                  | *–*                               |
| `minprice`                  | *–*                               |
| `name`                      | `categorySet` a `brandSet`        |
| `opennow`                   | *–*                               |
| `pagetoken`                 | `ofs` a `limit`                   |
| `radius`                    | `radius`                            |
| `rankby`                    | *–*                               |
| `type`                      | `categorySet –` najdete v dokumentaci [podporované kategorie hledání](supported-search-categories.md) .   |

## <a name="calculate-routes-and-directions"></a>Vypočítat trasy a směry

Vypočítejte trasy a pokyny pomocí Azure Maps. Azure Maps má mnoho stejných funkcí jako směrovací služba Google Maps, například:

- časy doručení a odchodu.
- trasy provozu v reálném čase a prediktivní provoz.
- Různé režimy dopravy. Například řízení, procházení, cyklistice.

> [!NOTE]
> Azure Maps vyžaduje, aby všechny Waypoints byly souřadnice. Adresy musí být nejprve v INCODE.

Služba směrování Azure Maps poskytuje následující rozhraní API pro výpočet tras:

- [**Vypočítat trasu**](https://docs.microsoft.com/rest/api/maps/route/getroutedirections): vypočítat trasu a zpracovat žádost blízko v reálném čase. Toto rozhraní API podporuje žádosti GET i POST. Žádosti POST se doporučují pro zadání velkého počtu Waypoints nebo použití mnoha možností směrování. Při použití POST se zaručí, že žádost o adresu URL nebude moc dlouhá a způsobuje problémy.
- [**Dávková trasa**](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview): vytvořte žádost obsahující až 1 000 žádosti o trasu. Požadavek zpracovává procesy v časovém intervalu. Všechny procesy dat paralelně na serveru. Stáhněte si sadu hned po dokončení žádosti a výsledky budou připravené.
- [**Služby mobility**](https://docs.microsoft.com/rest/api/maps/mobility): Vypočítejte trasy a směry pomocí veřejného přenosu.

Následující tabulka odkazuje na parametry rozhraní API Google Maps pomocí srovnatelných parametrů rozhraní API v Azure Maps.

| Parametr rozhraní API pro Google Maps    | Srovnatelný parametr Azure Maps rozhraní API  |
|------------------------------|--------------------------------------|
| `alternatives`                 | `maxAlternatives`                  |
| `arrival_time`                | `arriveAt`                          |
| `avoid`                        | `avoid`                            |
| `departure_time`              | `departAt`                          |
| `destination`                  | `query` – souřadnice ve formátu `"lat0,lon0:lat1,lon1…."`  |
| `key`                          | `subscription-key` – viz také dokumentace k [ověřování pomocí Azure Maps](azure-maps-authentication.md) . |
| `language`                     | `language` – viz dokumentace k [podporovaným jazykům](supported-languages.md) .   |
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
> Ve výchozím nastavení vrací rozhraní API trasy Azure Maps pouze souhrn (vzdálenost a časy) a souřadnice pro cestu trasy. Pomocí parametru `instructionsType` načtěte pokyny pro zapínání. Pomocí parametru `routeRepresentation` vyfiltrujte cestu k souhrnným údajům a trasám.

Rozhraní API pro směrování Azure Maps má mnoho dalších funkcí, které nejsou dostupné ve službě Google Maps. Při migraci aplikace může být užitečné tyto funkce integrovat:

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

Kromě těchto funkcí služba Směrování v Azure Maps podporuje [výpočet rozsahů směrování](https://docs.microsoft.com/rest/api/maps/route/getrouterange). Výpočet rozsahů směrování se označuje také jako izochronů. Zahrnuje generování oblasti pokryté mnohoúhelníkem. Pak se vypočítává zdvih v libovolném směru od počátečního bodu. Vše při zvažování stanovené doby a množství paliva nebo poplatků.

## <a name="retrieve-a-map-image"></a>Načtení obrázku mapy

Azure Maps poskytuje rozhraní API pro vykreslování statických imagí map s překrývajícími se daty. Rozhraní API pro [vykreslování obrázků map](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) v Azure Maps je SROVNATELNÉ s rozhraním API statické mapy v Mapách Google Maps.

> [!NOTE]
> Azure Maps vyžaduje, aby bylo uprostřed, všechna značka a umístění cest souřadnicé ve formátu Zeměpisná délka, zeměpisná šířka. Na druhé straně mapy Google používá formát Zeměpisná šířka, zeměpisná délka. Adresy musí být nejprve v INCODE.

Následující tabulka ukazuje parametry rozhraní API pro Google Maps s podobnými parametry Azure Maps rozhraní API.

| Parametr rozhraní API pro Google Maps | Srovnatelný parametr Azure Maps rozhraní API  |
|---------------------------|--------------------------------------|
| `center`                    | `center`                           |
| `format`                    | `format` – zadáno jako součást cesty URL. V současné době je podporována pouze PNG. |
| `key`                       | `subscription-key` – viz také dokumentace k [ověřování pomocí Azure Maps](azure-maps-authentication.md) . |
| `language`                  | `language` – viz dokumentace k [podporovaným jazykům](supported-languages.md) .  |
| `maptype`                   | `layer` a `style` – viz dokumentace k [podporovaným stylům mapy](supported-map-styles.md) . |
| `markers`                   | `pins`                             |
| `path`                      | `path`                             |
| `region`                    | Není *k dispozici* – jedná se o funkci související s geografické kódování. Při použití Azure Maps geografického kódování rozhraní API použijte parametr `countrySet`.  |
| `scale`                     | *–*                              |
| `size`                      | `width` a `height` – může mít velikost až 8192x8192. |
| `style`                     | *–*                              |
| `visible`                   | *–*                              |
| `zoom`                      | `zoom`                             |

> [!NOTE]
> Azure Maps používá systém dlaždic s dlaždicemi, které jsou dvojnásobnou velikostí dlaždic rozvržení používaných ve službě Google Maps. Tato hodnota úrovně přiblížení se v porovnání se službou Google Maps jeví jako jedna úroveň přiblížení v Azure Maps. Snížit úroveň přiblížení o jednu v rámci požadavků, které migrujete. Snížení hodnoty úrovně přiblížení kompenzuje variace v systémech dlaždic.

Další informace najdete v příručce k [rozhraní API pro vykreslování obrázků mapy](how-to-render-custom-data.md).

Kromě vygenerování statického obrázku mapy, služba Azure Maps rendering poskytuje možnost přímého přístupu k dlaždicím mapy v rastrovém formátu (PNG) a vektorovém formátu:

- [**Dlaždice mapy**](https://docs.microsoft.com/rest/api/maps/render/getmaptile): načíst rastrový obrázek (PNG) a vektorové dlaždice pro základní mapy (cesty, hranice, pozadí).
- [**Dlaždice map snímků**](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)– obrázek: načtení dlaždic satelitních a satelitních snímků

> [!TIP]
> Před několika lety se celá řada aplikací Google Maps přepnula z interaktivního mapového prostředí do statických imagí mapy jako metoda úspory nákladů. V Azure Maps je často mnohem efektivnější použít ovládací prvek interaktivní mapování v sadě web SDK. Poplatek za službu vychází z počtu dlaždic mapy, které aplikace načítá. Mapové dlaždice v Azure Maps jsou velké. Často trvá pouze několik dlaždic pro opětovné vytvoření stejného zobrazení mapy jako statické mapy. Mapové dlaždice jsou automaticky ukládány v prohlížeči. V takovém případě interaktivní ovládací prvek mapování často generuje zlomek transakce při reprodukci statického zobrazení mapy. Posouvání a přiblížení načte další dlaždice, ale v mapovém ovládacím prvku jsou k dispozici možnosti, které toto chování zakáže. Interaktivní ovládací prvek mapy také nabízí mnohem více možností vizualizace než statické služby mapy.

### <a name="marker-url-parameter-format-comparison"></a>Porovnání formátu parametru adresy URL značky

**Před: Google Maps**

Ve službě Google Maps můžete do obrázku statické mapy přidat značky pomocí parametru `markers` v adrese URL. Parametr `markers` přebírá ve stylu a seznam umístění, která se mají na mapě vykreslovat, jak je znázorněno níže:

```
&markers=markerStyles|markerLocation1|markerLocation2|...
```

Další styly lze použít přidáním dalších parametrů `markers` k adrese URL s jiným stylem a sadou umístění.

Umístění značek jsou zadána ve formátu "Zeměpisná délka, zeměpisná délka".

Styly značek v Google Maps se přidávají s formátováním `optionName:value`s více znaky oddělenými svislými písmeny (\|). Takto: "optionName1: hodnota1\|optionName2: hodnota2". Všimněte si, že názvy možností a hodnoty jsou oddělené dvojtečkou (:). Následující názvy možností stylu lze použít k označení stylu ve službě Google Maps:

- `color` – barva výchozí ikony značky Může to být 24bitové hexadecimální barva (`0xrrggbb`) nebo jedna z následujících hodnot: `black`, `brown`, `green`, `purple`, `yellow`, `blue`, `gray`, `orange`, `red`, `white`.
- `label` – jeden malý alfanumerický znak, který se má zobrazit nad ikonou.
- `size` – velikost značky Může být `tiny`, `mid`nebo `small`.

Vlastní ikony lze přidat do služby Google Maps pomocí následujících názvů možností stylu:

- `anchor` – určuje způsob zarovnání obrázku ikony na souřadnici. Může to být hodnota pixel (x, y) nebo jedna z následujících hodnot: `top`, `bottom`, `left`, `right`, `center`, `topleft`, `topright`, `bottomleft`nebo `bottomright`.
- `icon` – adresa URL ukazující na obrázek ikony.

Například v Google Maps může být na mapě přidána červená značka střední velikosti v souřadnicích (zeměpisná délka:-110, zeměpisná šířka: 45) s následujícím parametrem URL:

```
&markers=color:red|size:mid|45,-110
```

<center>

](media/migrate-google-maps-web-services/google-maps-marker.png)</center> značka ![mapy Google

**Po: Azure Maps**

V Azure Maps přidejte značky do statického obrázku mapy zadáním parametru `pins` v adrese URL. Podobně jako u Map Google Určete styl a seznam umístění v tomto parametru. Zadejte parametr `pins` vícekrát pro podporu značek s různými styly.

```
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

Chcete-li použít další styly, přidejte do adresy URL další parametry `pins` s jiným stylem a sadou umístění.

Pro umístění kódu PIN Azure Maps vyžaduje, aby souřadnice byly ve formátu "Zeměpisná šířka". Google Maps používá formát "Zeměpisná délka". Mezera, nejedná se o čárku, která odděluje zeměpisnou a zeměpisnou šířku ve formátu Azure Maps.

`iconType` určuje typ pinu, který se má vytvořit. Může mít následující hodnoty:

- `default` – ikona výchozí PIN
- `none` – není zobrazena žádná ikona, budou vykresleny pouze popisky.
- `custom` – určuje vlastní ikonu, která se má použít. Adresa URL ukazující na obrázek ikony může být přidána na konec parametru `pins` za informace o umístění kódu PIN.
- `{udid}` – jedinečné ID dat (UDID) pro ikonu uloženou v Azure Maps datové platformě úložiště.

Přidejte styly připnutí v Azure Maps ve formátu `optionNameValue`. Rozdělte více stylů pomocí svislých (\|) znaků. Například: `iconType|optionName1Value1|optionName2Value2`. Hodnoty a názvy možností nejsou oddělené. Pro značky stylu v Azure Maps použijte následující názvy možností stylu:

- `al` – určuje neprůhlednost (alfa) značky. Vyberte číslo mezi 0 a 1.
- `an` – určuje kotvu PIN. Hodnoty x a y v pixelech, které jsou zadány ve formátu "x y".
- `co` – barva kódu PIN Musí být 24bitové hexadecimální Barva: `000000` pro `FFFFFF`.
- `la` – určuje kotvu popisku. Hodnoty x a y v pixelech, které jsou zadány ve formátu "x y".
- `lc` – Barva popisku Musí být 24bitové hexadecimální Barva: `000000` pro `FFFFFF`.
- `ls` – velikost popisku v pixelech Vyberte číslo větší než 0.
- `ro` – hodnota ve stupních pro otočení ikony. Vyberte číslo v rozmezí od-360 do 360.
- `sc` – hodnota měřítka pro ikonu připnutí Vyberte číslo větší než 0.

Hodnoty popisků jsou určeny pro každé umístění kódu PIN. Tento přístup je efektivnější než použití jedné hodnoty popisku na všechny značky v seznamu umístění. Hodnota popisku může být řetězec s více znaky. Zabalte řetězec s jednoduchými uvozovkami, aby se zajistilo, že není možné ho nahradit jako styl nebo hodnotu umístění.

Například v Azure Maps přidání červené (`FF0000`) výchozí ikony s popiskem "místo ručičky", umístěné níže (15 50), s ikonou na souřadnicích (zeměpisná délka:-122,349300, zeměpisná šířka: 47,620180), se zobrazí následující parametr URL:

```
&pins=default|coFF0000|la15 50||'Space Needle' -122.349300 47.620180
```

<center>

Značka Azure Maps ![](media/migrate-google-maps-web-services/azure-maps-marker.png)</center>

Následující příklad přidá tři PIN kódy s hodnotami popisku "1", "2" a "3":

```
&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12
```

<center>

![Azure Maps více značek](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)</center>

### <a name="path-url-parameter-format-comparison"></a>Porovnání formátu parametru adresy URL cesty

**Před: Google Maps**

Ve službě Google Maps je možné do obrázku statické mapy přidat čáry a mnohoúhelníky pomocí parametru `path` v adrese URL. Parametr `path` přebírá ve stylu a seznam umístění, která se mají na mapě vykreslovat, jak je znázorněno níže:

```
&path=pathStyles|pathLocation1|pathLocation2|...
```

Další styly lze použít přidáním dalších parametrů `path` k adrese URL s jiným stylem a sadou umístění.

Umístění cest v Google Maps jsou zadaná ve formátu `latitude1,longitude1|latitude2,longitude2|…`. Cesty můžou být kódované nebo obsahují adresy pro body.

Styly cest ve službě Google Maps se přidávají s `optionName:value`formátu s více znaky oddělenými svislou čárou (\|). Takto: `optionName1:value1|optionName2:value2`. Všimněte si, že názvy možností a hodnoty jsou oddělené dvojtečkou (:). Následující názvy možností stylu lze použít ke stylování cest v Google Maps:

- `color` – barva obrysu cesty nebo mnohoúhelníku. Může se jednat o 24bitové hexadecimální barvu (`0xrrggbb`), 32 šestnáctková barva (`0xrrggbbbaa`) nebo jedna z následujících hodnot: černá, hnědá, zelená, fialová, žlutá, modrá, šedá, oranžová, červená a bílá.
- `fillColor` – barva, kterou chcete vyplnit oblast cesty (mnohoúhelník). Může se jednat o 24bitové hexadecimální barvu (`0xrrggbb`), 32 šestnáctková barva (`0xrrggbbbaa`) nebo jedna z následujících hodnot: černá, hnědá, zelená, fialová, žlutá, modrá, šedá, oranžová, červená a bílá.
- `geodesic` – určuje, zda by měla být cesta čára, která následuje zakřivení země.
- `weight` – tloušťka čáry cesty v pixelech

Ve službě Google Maps se na mapu mezi souřadnicemi v parametru URL dá přidat neprůhlednost červené čáry a tloušťka pixelů. V příkladu níže má čára neprůhlednost 50% a tloušťku čtyř pixelů. Souřadnice jsou zeměpisná délka:-110, zeměpisná šířka: 45 a zeměpisná délka:-100, zeměpisná šířka: 50.

```
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

<center>

![](media/migrate-google-maps-web-services/google-maps-polyline.png)</center> na lomenou čáru mapy Google

**Po: Azure Maps**

V Azure Maps přidejte čáry a mnohoúhelníky do statického obrázku mapy zadáním parametru `path` v adrese URL. Podobně jako u Map Google Určete styl a seznam umístění v tomto parametru. Určete `path` parametr několikrát pro vykreslení více kruhů, čar a mnohoúhelníků s různými styly.

```
&path=pathStyles||pathLocation1|pathLocation2|...
```

Když přichází do umístění cest, Azure Maps vyžaduje, aby souřadnice byly ve formátu "Zeměpisná šířka". Google Maps používá formát "Zeměpisná délka". Mezera, nejedná se o čárku, která odděluje zeměpisnou a zeměpisnou šířku ve formátu Azure Maps. Azure Maps nepodporuje kódované cesty nebo adresy pro body. Do Azure Maps rozhraní API pro úložiště dat, jak je uvedeno [tady](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage), nahrajte větší datové sady jako soubory ve formátu celé JSON.

V Azure Maps přidejte styly cest s formátem `optionNameValue`. Rozdělte více stylů podle kanálu (\|), jako je tento `optionName1Value1|optionName2Value2`. Hodnoty a názvy možností nejsou oddělené. Pro styly cest v Azure Maps použijte následující názvy možností stylu:

- `fa` – neprůhlednost barvy výplně (alfa), která se používá při vykreslování mnohoúhelníků. Vyberte číslo mezi 0 a 1.
- `fc` – Barva výplně použitá k vykreslení oblasti mnohoúhelníku
- `la` – neprůhlednost barvy čáry (alfa), která se používá při vykreslování čar a obrysu mnohoúhelníků. Vyberte číslo mezi 0 a 1.
- `lc` – Barva čáry použitá k vykreslení čar a obrysu mnohoúhelníků.
- `lw` – šířka čáry v pixelech
- `ra` – určuje poloměr kroužků v měřičích.

V Azure Maps přidejte v parametru URL neprůhlednost červené čáry a tloušťku písmen mezi souřadnicemi. Pro příklad níže má čára 50% krytí a tloušťku čtyř pixelů. Souřadnice mají následující hodnoty: Zeměpisná délka:-110, zeměpisná šířka: 45 a zeměpisná délka:-100, zeměpisná šířka: 50.

```
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

<center>

![Azure Maps lomená čáry](media/migrate-google-maps-web-services/azure-maps-polyline.png)</center>

## <a name="calculate-a-distance-matrix"></a>Vypočítat matici vzdálenosti

Azure Maps poskytuje rozhraní API pro matici Distance. Pomocí tohoto rozhraní API můžete vypočítat dobu trvání cesty a vzdálenosti mezi sadou umístění s maticí s vzdáleností. Je srovnatelná s rozhraním API služby Distance v Google Maps.

- [**Matice směrování**](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview): asynchronně vypočítá dobu trvání cesty a vzdálenosti pro sadu míst původu a místa. Podporuje až 700 buněk na žádost (počet počátek vynásobený počtem cílů. S tímto omezením je třeba mít příklady možných dimenzí matice: 700x1, 50x10, 10x10, 28x25, 10x70.

> [!NOTE]
> Požadavek na rozhraní API na dálku se dá vytvořit jenom pomocí žádosti POST s informacemi o původu a cíli v těle žádosti. Azure Maps navíc vyžaduje, aby všechny zdroje a cíle byly souřadnice. Adresy musí být nejprve v INCODE.

Následující tabulka odkazuje na parametry rozhraní API Google Maps pomocí srovnatelných parametrů Azure Maps rozhraní API.

| Parametr rozhraní API pro Google Maps      | Srovnatelný parametr Azure Maps rozhraní API  |
|--------------------------------|--------------------------------------|
| `arrivial_time`                | `arriveAt`                           |
| `avoid`                        | `avoid`                              |
| `depature_time`                | `departAt`                           |
| `destinations`                 | `destination` – zadejte v textu požadavku POST jako text typu injson. |
| `key`                          | `subscription-key` – viz také dokumentace k [ověřování pomocí Azure Maps](azure-maps-authentication.md) . |
| `language`                     | `language` – viz dokumentace k [podporovaným jazykům](supported-languages.md) .  |
| `mode`                         | `travelMode`                         |
| `origins`                      | `origins` – zadejte v textu požadavku POST jako text typu injson.  |
| `region`                       | Není *k dispozici* – Tato funkce se týká geografického kódování. Při použití Azure Maps geografického kódování rozhraní API použijte parametr `countrySet`. |
| `traffic_model`                | Není *k dispozici* – může určovat, jestli se mají používat data přenosů s parametrem `traffic`. |
| `transit_mode`                 | V současné době *se* nepodporují meziměstské matice na bázi přenosu.  |
| `transit_routing_preference`   | V současné době *se* nepodporují meziměstské matice na bázi přenosu.  |
| `units`                        | Není *k dispozici* – Azure Maps používá pouze systém metrik. |

> [!TIP]
> Všechny možnosti pokročilého směrování, které jsou dostupné v rozhraní API směrování Azure Maps, jsou podporované v rozhraní API Azure Maps Distance. Mezi pokročilé možnosti směrování patří: směrování Ruck, specifikace modulu atd.

## <a name="get-a-time-zone"></a>Získat časové pásmo

Azure Maps poskytuje rozhraní API pro načtení časového pásma souřadnic. Rozhraní API pro časové pásmo Azure Maps je srovnatelné s rozhraním API pro časové pásmo v Google Maps:

- [**Časové pásmo podle souřadnic**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates): Určete souřadnici a přijímají podrobnosti o časovém pásmu pro souřadnici.

Následující tabulka odkazuje na parametry rozhraní API Google Maps pomocí srovnatelných parametrů rozhraní API v Azure Maps.

| Parametr rozhraní API pro Google Maps | Srovnatelný parametr Azure Maps rozhraní API   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key` – viz také dokumentace k [ověřování pomocí Azure Maps](azure-maps-authentication.md) .       |
| `language`                  | `language` – viz dokumentace k [podporovaným jazykům](supported-languages.md) .    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

Kromě tohoto rozhraní API poskytuje Azure Maps platforma řadu rozhraní API pro časové pásmo. Tato rozhraní API převádějí čas na základě názvů nebo ID časového pásma:

- [**Časové pásmo podle ID**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebyid): vrátí aktuální, historické a budoucí informace o časovém pásmu pro zadané ID časového pásma IANA.
- Identifikátor [**úřadu pro vyčíslení časového pásma**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumiana): vrátí úplný seznam ID časových pásem IANA. Aktualizace služby IANA se v systému projeví v rámci jednoho dne.
- [**Okna výčtu časových pásem**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumwindows): vrátí úplný seznam ID časových pásem systému Windows.
- [**Verze IANA časového pásma**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneianaversion): vrátí aktuální číslo verze iana používané Azure Maps.
- [**Časová pásma Windows to IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonewindowstoiana): vrátí odpovídající ID IANA, které má přidělené platné ID časového pásma systému Windows. Pro jedno ID systému Windows může být vráceno více ID IANA.

## <a name="client-libraries"></a>Klientské knihovny

Azure Maps poskytuje klientské knihovny pro následující programovací jazyky:

- JavaScript, TypeScript, Node. js – [dokumentace](how-to-use-services-module.md) \| [balíčku npm](https://www.npmjs.com/package/azure-maps-rest)

Open Source klientské knihovny pro jiné programovací jazyky:

- .NET Standard 2,0 – [projekt githubu](https://github.com/perfahlen/AzureMapsRestServices) \| [balíček NuGet](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="additional-resources"></a>Další zdroje informací:

Níže najdete další dokumentaci a prostředky pro služby Azure Maps REST.

- [Osvědčené postupy pro hledání](how-to-use-best-practices-for-search.md)
- [Hledání adresy](how-to-search-for-address.md)
- [Referenční dokumentace k rozhraní API služby Azure Maps REST](https://docs.microsoft.com/rest/api/maps/)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o službě Azure Maps REST.

> [!div class="nextstepaction"]
> [Osvědčené postupy pro používání vyhledávací služby](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [Jak používat modul služby (Web SDK)](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Ukázky kódu](https://docs.microsoft.com/samples/browse/?products=azure-maps)