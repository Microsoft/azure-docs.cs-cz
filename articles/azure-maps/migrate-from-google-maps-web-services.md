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
ms.openlocfilehash: 51c00524c781d9af58f60b36aa3baeb079c6eafa
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910759"
---
# <a name="migrate-web-service-from-google-maps"></a>Migrace webové služby z Google Maps

Mapy Azure i Google poskytují přístup k prostorovým rozhraním API prostřednictvím webových služeb REST. Rozhraní API pro tyto platformy provádějí podobné funkce, ale používají různé konvence pojmenování a objekty odpovědí.

Následující tabulka poskytuje rozhraní API služby Azure Maps, která poskytují podobné funkce pro uvedená rozhraní API služby Google Maps.

| Rozhraní API služby Google Maps | Rozhraní API služby Azure Maps                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| Navigační pokyny              | [Cestě](https://docs.microsoft.com/rest/api/maps/route)                               |
| Matice vzdáleností         | [Matice směrování](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview) |
| Geokódování               | [Search](https://docs.microsoft.com/rest/api/maps/search)                             |
| Hledání míst           | [Search](https://docs.microsoft.com/rest/api/maps/search)                             |
| Umístit automatické dokončování      | [Search](https://docs.microsoft.com/rest/api/maps/search)                             |
| Statická mapa              | [Vykreslení](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                 |
| Časové pásmo               | [Časové pásmo](https://docs.microsoft.com/rest/api/maps/timezone)                        |

Následující rozhraní API služby nejsou v tuto chvíli k dispozici v Azure Maps:

- Zvýšení oprávnění
- Geografická poloha
- Místo podrobností a fotografií. K dispozici jsou telefonní čísla a adresa URL webu v rozhraní API pro hledání Azure Maps.
- Mapování adres URL
- Cesty – omezení rychlosti jsou k dispozici prostřednictvím trasy a reverzní rozhraní API pro geografické kódování v Azure Maps.
- Statické zobrazení ulice

Azure Maps má několik dalších webových služeb REST, které mohou být zajímavé:

- [Prostorové operace](https://docs.microsoft.com/rest/api/maps/spatial): přesměrování složitých prostorových výpočtů a operací, jako je monitorování geografických zón, do služby.
- [Provoz](https://docs.microsoft.com/rest/api/maps/traffic): přístup k datům o toku provozu a incidentu v reálném čase.

## <a name="geocoding-addresses"></a>Adresy geografického kódování

Geografické kódování je proces převodu adresy (například "1 Microsoft Way, Redmond, WA") na souřadnici (například zeměpisná délka:-122,1298, zeměpisná šířka: 47,64005). Souřadnice se pak často používají k umístění značky na mapě nebo středu mapy.

Azure Maps poskytuje několik metod pro adresy geografického kódování:

- [**Geografické kódování adresy volného tvaru**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress): zadejte jeden adresní řetězec (například "1 Microsoft Way, Redmond, WA") a okamžitě zpracujte požadavek. To se doporučuje v případě, že potřebujete jednotlivé adresy snadno kódovat.
- [**Geografické označování strukturovaných adres**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured): Určete části jedné adresy, jako je název ulice, město, země a poštovní směrovací číslo a okamžitě zpracujte požadavek. To se doporučuje v případě, že potřebujete rychle identifikovat jednotlivé adresy a data jsou již analyzována na jednotlivé části adres.
- [**Geografické kódování adresy Batch**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview): vytvořte žádost obsahující až 10 000 adres a požádejte je o zpracování v časovém intervalu. Všechny adresy budou na serveru paralelně zavedeny a po dokončení bude možné stáhnout úplnou sadu výsledků. Tento postup je doporučený pro geografické sady velkých datových sad.
- [**Hledání přibližných**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)hodnot: Toto rozhraní API kombinuje geografické kódování s bodem hledání z hlediska zájmu. Toto rozhraní API přebírá řetězec ve volném formátu, který může být adresami, místem, orientačními body, bodem zájmu nebo kategorií zájmu a okamžitě zpracovat požadavek. Toto rozhraní API se doporučuje u aplikací, kde můžou uživatelé vyhledat adresy nebo body zájmu ze stejného textového pole.
- [**Nepřibližné dávkové vyhledávání**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): vytvoření žádosti obsahující až 10 000 adres, míst, orientačních bodů nebo bodů zájmu a jejich zpracování v časovém intervalu. Všechna data budou zpracována paralelně na serveru a po dokončení bude možné stáhnout úplnou sadu výsledků.

Následující tabulka odkazuje na parametry rozhraní API Google Maps pomocí srovnatelných parametrů rozhraní API v Azure Maps.

| Parametr rozhraní API pro Google Maps | Srovnatelný parametr Azure Maps rozhraní API  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` a `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality` – City/město<br/>`municipalitySubdivision` – okolí, sub/super City<br/>`countrySubdivision` – kraj<br/>`countrySecondarySubdivision` – okres<br/>`countryTertiarySubdivision` – oblast<br/>`countryCode` – kód země pro dva číslice |
| `key`                       | `subscription-key` – viz také dokumentace k [ověřování pomocí Azure Maps](azure-maps-authentication.md) . |
| `language`                  | `language` – viz dokumentace k [podporovaným jazykům](supported-languages.md) .  |
| `region`                    | `countrySet`                       |

Příklad použití vyhledávací služby je popsán [zde](how-to-search-for-address.md). Nezapomeňte si projít [osvědčené postupy pro vyhledávání](how-to-use-best-practices-for-search.md) v dokumentaci.

> [!TIP]
> V režimu automatického dokončování je možné použít rozhraní API pro geografické kódování a přibližné vyhledávání, a to přidáním `&amp;typeahead=true` k adrese URL požadavku. Tím se server upozorní na to, že je vstupní text nejspíš částečný, a přejde do prediktivního režimu.

## <a name="reverse-geocode-a-coordinate"></a>Obrátit souřadnici pro INCODE

Inverzní geografické kódování je proces převodu geografických souřadnic (jako Zeměpisná délka:-122,1298, zeměpisná šířka: 47,64005) na jeho přibližnou adresu (například "1 Microsoft Way, Redmond, WA").

Azure Maps poskytuje několik metod reverzního geografického kódování:

- [**Adresa reverzního INCODE**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse): Určete jednu geografickou souřadnici, která získá přibližnou adresu a okamžitě zpracuje požadavek.
- [**INCODE Reverse pro meziulici**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet): Určete jednu geografickou souřadnici, která má být v blízkosti informací o meziulici (například 1 & Main), a okamžitě zpracovat požadavek.
- [**Adresa dávkového reverzního příznaku zpětného vyhledávání**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview): vytvořte žádost obsahující až 10 000 souřadnic a požádejte je o zpracování v časovém intervalu. Všechna data budou zpracována paralelně na serveru a po dokončení bude možné stáhnout úplnou sadu výsledků.

Následující tabulka odkazuje na parametry rozhraní API Google Maps pomocí srovnatelných parametrů rozhraní API v Azure Maps.

| Parametr rozhraní API pro Google Maps   | Srovnatelný parametr Azure Maps rozhraní API   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key` – viz také dokumentace k [ověřování pomocí Azure Maps](azure-maps-authentication.md) . |
| `language`                  | `language` – viz dokumentace k [podporovaným jazykům](supported-languages.md) .  |
| `latlng`                    | `query`  |
| `location_type`             | *–*     |
| `result_type`               | `entityType`    |

Nezapomeňte si projít [osvědčené postupy pro vyhledávání](how-to-use-best-practices-for-search.md) v dokumentaci.

Rozhraní API pro invertování Azure Maps obsahuje některé další funkce, které nejsou k dispozici ve službě Google Maps, které by mohly být užitečné při migraci aplikace:

- Načte data omezení rychlosti.
- Načíst informace o využití provozu: místní silnice, Arterial, omezený přístup, rampa atd.
- Strana ulice, na kterou souřadnice spadá.

## <a name="search-for-points-of-interest"></a>Hledání bodů zájmu

Data z bodu zájmu je možné prohledávat ve službě Google Maps pomocí rozhraní API pro vyhledávání umístění. Toto rozhraní API nabízí tři různé způsoby hledání bodů zájmu:

- **Najít místo z textu:** Vyhledá bod zájmu na základě jeho názvu, adresy nebo telefonního čísla.
- **Nejbližší hledání**: vyhledá body zájmů, které spadají do určité vzdálenosti.
- **Hledání textu:** Vyhledá místa pomocí volného formátu textu, který obsahuje bod zájmu a informace o poloze. Například "pizza in Praha" nebo "restaurace poblíž Main St".

Azure Maps poskytuje několik rozhraní API pro hledání bodů zájmu:

- [**Hledání POI**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi): vyhledejte body zájmů podle jména. Například "Starbucks".
- [**Hledání kategorií POI**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory): vyhledejte body zájmu podle kategorie. Například "restaurace".
- [**Nejbližší hledání**](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby): vyhledá body zájmů, které spadají do určité vzdálenosti.
- [**Hledání přibližných**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)hodnot: Toto rozhraní API kombinuje geografické kódování s bodem hledání z hlediska zájmu. Toto rozhraní API přebírá řetězec ve volném formátu, který může být adresami, místem, orientačními body, bodem zájmu nebo kategorií zájmu a okamžitě zpracovat požadavek. Toto rozhraní API se doporučuje u aplikací, kde můžou uživatelé vyhledat adresy nebo body zájmu ze stejného textového pole.
- [**Hledání v geometrii**](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry): hledání bodů zájmů v rámci zadaného geometrie (mnohoúhelník).
- [**Hledání v cestě**](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute): vyhledejte body, které jsou podél zadané cesty trasy.
- [**Nepřibližné dávkové vyhledávání**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): vytvoření žádosti obsahující až 10 000 adres, míst, orientačních bodů nebo bodů zájmu a jejich zpracování v časovém intervalu. Všechna data budou zpracována paralelně na serveru a po dokončení bude možné stáhnout úplnou sadu výsledků.

V současné době Azure Maps nemá srovnatelné rozhraní API s rozhraním API pro vyhledávání textu v Google Maps.

> [!TIP]
> Poi vyhledávání, vyhledávání kategorií POI a rozhraní API pro hledání přibližné vyhledávání se dají použít v režimu automatického dokončování přidáním `&amp;typeahead=true` k adrese URL požadavku. Tím se server upozorní na to, že je vstupní text nejspíš částečný, a přejde do prediktivního režimu.

Nezapomeňte si projít [osvědčené postupy pro vyhledávání](how-to-use-best-practices-for-search.md) v dokumentaci.

### <a name="find-place-from-text"></a>Najít místo z textu

Pokud chcete hledat body zájmů podle jména nebo adresy, je možné použít rozhraní API Azure Maps [POI Search](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) a [přibližného vyhledávání](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) .

Následující tabulka odkazuje na parametry rozhraní API Google Maps pomocí srovnatelných parametrů rozhraní API v Azure Maps.

| Parametr rozhraní API pro Google Maps | Srovnatelný parametr Azure Maps rozhraní API |
|---------------------------|-------------------------------------|
| `fields`                  | *–*                               |
| `input`                   | `query`                             |
| `inputtype`               | *–*                               |
| `key`                     | `subscription-key` – viz také dokumentace k [ověřování pomocí Azure Maps](azure-maps-authentication.md) . |
| `language`                | `language` – viz dokumentace k [podporovaným jazykům](supported-languages.md) .  |
| `locationbias`            | `lat`, `lon` a `radius`<br/>`topLeft` a `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>Okolní hledání

Okolní body zájmů se dají načíst v Azure Maps pomocí rozhraní API pro [hledání v okolí](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) .

Následující tabulka odkazuje na parametry rozhraní API Google Maps pomocí srovnatelných parametrů rozhraní API v Azure Maps.

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

Azure Maps lze použít k výpočtu tras a směrů. Azure Maps má mnoho stejných funkcí jako směrovací služba Google Maps, například:

- časy doručení a odchodu.
- trasy provozu v reálném čase a prediktivní provoz.
- různé režimy dopravy; řízení, procházení, cyklistice.

> [!NOTE]
> Azure Maps vyžaduje, aby všechny Waypoints byly souřadnice. Adresy musí být nejprve v INCODE.

Služba směrování Azure Maps poskytuje následující rozhraní API pro výpočet tras:

- [**Vypočítat trasu**](https://docs.microsoft.com/rest/api/maps/route/getroutedirections): Vypočítejte trasu a ihned zpracuje požadavek. Toto rozhraní API podporuje žádosti GET i POST. Žádosti POST se doporučují při zadání velkého počtu Waypoints nebo při použití spousty možností směrování, abyste zajistili, že se požadavek na adresu URL nestane příliš dlouhý a způsobuje problémy.
- [**Dávková trasa**](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview): vytvořte žádost obsahující až 1 000 žádosti o trasu a požádejte ji o zpracování v časovém intervalu. Všechna data budou zpracována paralelně na serveru a po dokončení bude možné stáhnout úplnou sadu výsledků.
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
| `region`                       | Není *k dispozici* – jedná se o funkci související s geografické kódování. Při použití Azure Maps rozhraní API pro geografické kódování použijte parametr *countrySet* .  |
| `traffic_model`               | Není *k dispozici* – dá se zadat jenom v případě, že se mají použít data přenosů s parametrem *provozu* . |
| `transit_mode`                | Viz [dokumentace ke službě mobility](https://docs.microsoft.com/rest/api/maps/mobility) |
| `transit_routing_preference` | Viz [dokumentace ke službě mobility](https://docs.microsoft.com/rest/api/maps/mobility) |
| `units`                        | Není *k dispozici* – Azure Maps používá pouze systém metrik.  |
| `waypoints`                    | `query`                            |

> [!TIP]
> Ve výchozím nastavení vrací rozhraní API trasy Azure Maps pouze souhrn (vzdálenost a časy) a souřadnice pro cestu trasy. Pomocí parametru `instructionsType` načtěte pokyny pro zapínání. Parametr `routeRepresentation` lze použít k odfiltrování cesty souhrnu a trasy.

Rozhraní API pro směrování Azure Maps má mnoho dalších funkcí, které nejsou dostupné ve službě Google Maps, které by mohly být užitečné při migraci vaší aplikace:

- Podpora pro typ směrování: nejkratší, nejrychlejší, Trilling a většina pohonných hmot.
- Podpora dalších způsobů cestovního režimu: sběrnice, motocykl, taxislužby, nákladní vůz a van.
- Podpora 150 Waypoints.
- Výpočet více dob cestování v rámci jediné žádosti; historické přenosy, živý provoz bez provozu.
- Vyhněte se dalším typům cest: carpool silnice, Unpaved silnice, již využité silnice.
- Zadejte vlastní oblasti, abyste se vyhnuli.
- Omezte zvýšení oprávnění v tom, že trasa může být Ascend.
- Směrování na základě specifikace stroje. Vypočítat trasy pro spalovací nebo elektrická vozidla založená na jejich zbývajících specifikacích pro palivo/poplatek a stroj.
- Podpora parametrů směrování komerčních vozidel; rozměry, váhy, počty Axels a nákladové typy vozidel.
- Zadejte maximální rychlost vozidla.

Kromě toho služba Směrování v Azure Maps také podporuje [Výpočet rozsahu směrování](https://docs.microsoft.com/rest/api/maps/route/getrouterange), označovaného také jako izochronů, který vygeneruje mnohoúhelník zahrnující oblast, na kterou se dá cestovat v libovolném směru od počátečního bodu v zadaném množství času nebo množství paliva a poplatků.

## <a name="retrieve-a-map-image"></a>Načtení obrázku mapy

Azure Maps poskytuje rozhraní API pro vykreslování statických imagí map s překrývajícími se daty. Rozhraní API pro [vykreslování obrázku mapy](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) Azure Maps je SROVNATELNÉ s rozhraním API statické mapy v Google Maps.

> [!NOTE]
> Azure Maps vyžaduje střed, všechna označení a umístění cest mají být souřadnice ve formátu "Zeměpisná délka" Zeměpisná šířka, zatímco Google Maps používá formát "Zeměpisná délka". Adresy musí být nejprve v INCODE.

Následující tabulka odkazuje na parametry rozhraní API Google Maps pomocí srovnatelných parametrů rozhraní API v Azure Maps.

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
> Azure Maps používá systém dlaždic s dlaždicemi, které jsou dvojnásobnou velikostí dlaždic rozvržení používaných ve službě Google Maps. Vzhledem k tom, že hodnota úrovně přiblížení v Azure Maps, se v porovnání se službou Google Maps zobrazí jedna úroveň přiblížení v Azure Maps. Snižte úroveň přiblížení v požadavcích, které migrujete, o jednu, abyste to vyrovnali.

Další informace najdete v příručce k [rozhraní API pro vykreslování obrázků mapy](how-to-render-custom-data.md).

Kromě toho, že je možné vygenerovat statický obrázek mapy, služba Azure Maps rendering také poskytuje možnost přímý přístup k dlaždicím mapy v rastrovém formátu (PNG) a ve vektorovém formátu:

- [**Dlaždice mapy**](https://docs.microsoft.com/rest/api/maps/render/getmaptile): načíst rastrový obrázek (PNG) a vektorové dlaždice pro základní mapy (cesty, hranice, pozadí).
- [**Dlaždice map snímků**](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)– obrázek: načtení dlaždic satelitních a satelitních snímků

> [!TIP]
> Mnoho aplikací mapy Google, kde se přepnuly z interaktivního mapového prostředí do statických map, je před několika lety jako metoda úspory nákladů. V Azure Maps je často mnohem efektivnější použít ovládací prvek interaktivní mapování v sadě web SDK, protože se účtuje za poplatky na základě načítání dlaždic mapy. Mapové dlaždice v Azure Maps jsou velké a často se k opětovnému vytvoření stejného zobrazení mapy jako statické mapy a dlaždic mapování ukládají automaticky v prohlížeči. V takovém případě interaktivní ovládací prvek mapy často generuje zlomek transakce při reprodukci statického zobrazení mapy. Posouvání a přiblížení načte další dlaždice, ale v mapovém ovládacím prvku jsou k dispozici možnosti, které toto chování v případě potřeby zakáže. Interaktivní ovládací prvek mapy také nabízí mnohem více možností vizualizace než statické služby mapy.

### <a name="marker-url-parameter-format-comparison"></a>Porovnání formátu parametru adresy URL značky

**Před: Google Maps**

Ve značkách Google Maps se dají do obrázku statické mapy přidat pomocí parametru `markers` v adrese URL. Parametr `markers` přebírá ve stylu a seznam umístění, která se mají na mapě vykreslovat, jak je znázorněno níže:

```
&markers=markerStyles|markerLocation1|markerLocation2|...
```

Další styly lze použít přidáním dalších parametrů `markers` k adrese URL s jiným stylem a sadou umístění.

Umístění značek jsou zadána ve formátu "Zeměpisná délka, zeměpisná délka".

Styly značek ve službě Google Maps jsou přidány s `optionName:value`formátu s více znaky oddělenými svislou čárou (\|), jako je například optionName1: hodnota1\|optionName2: hodnota2. Všimněte si, že názvy možností a hodnoty jsou oddělené dvojtečkou (:). Následující názvy možností stylu lze použít k označení stylu ve službě Google Maps:

- `color` – barva výchozí ikony značky Může to být 24bitové hexadecimální barva (`0xrrggbb`) nebo jedna z následujících hodnot: `black`, `brown`, `green`, `purple`, `yellow`, `blue`, `gray`, `orange`, `red`, `white`.
- `label` – jeden malý alfanumerický znak, který se má zobrazit nad ikonou.
- `size` – velikost značky Může být `tiny`, `mid`nebo `small`.

Vlastní ikony lze také použít v Mapách Google pomocí následujících názvů možností stylu:

- `anchor` – určuje způsob zarovnání obrázku ikony na souřadnici. Může to být hodnota pixel (x, y) nebo jedna z následujících hodnot: `top`, `bottom`, `left`, `right`, `center`, `topleft`, `topright`, `bottomleft`nebo `bottomright`.
- `icon` – adresa URL ukazující na obrázek ikony.

Například v Google Maps může být na mapě přidána červená značka střední velikosti v souřadnicích (zeměpisná délka:-110, zeměpisná šířka: 45) s následujícím parametrem URL:

```
&markers=color:red|size:mid|45,-110
```

<center>

](media/migrate-google-maps-web-services/google-maps-marker.png)</center> značka ![mapy Google

**Po: Azure Maps**

Ve Azure Maps značky lze také přidat do statického obrázku mapy zadáním parametru `pins` v adrese URL. Podobně jako u služby Google Maps lze v tomto parametru zadat styl a seznam umístění a parametr `pins` lze zadat vícekrát pro podporu značek s různými styly.

```
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

Další styly lze použít přidáním dalších parametrů `pins` k adrese URL s jiným stylem a sadou umístění.

Když přichází do připnutí umístění, Azure Maps vyžaduje, aby byly souřadnice ve formátu "Zeměpisná šířka", zatímco Google Maps používá formát "Zeměpisná délka". Všimněte si také, že je mezera, a ne čárka, která odděluje zeměpisnou délku a zeměpisnou šířku v Azure Maps.

Hodnota `iconType` určuje typ pinu, který se má vytvořit, a může mít následující hodnoty:

- `default` – ikona výchozí PIN
- `none` – není zobrazena žádná ikona, budou vykresleny pouze popisky.
- `custom` – určuje vlastní ikonu, která se má použít. Adresa URL ukazující na obrázek ikony může být přidána na konec parametru `pins` za informace o umístění kódu PIN.
- `{udid}` – jedinečné ID dat (UDID) pro ikonu uloženou v Azure Maps datové platformě úložiště.

Styly připnutí v Azure Maps jsou přidány s `optionNameValue`formátu s více znaky oddělenými svislým symbolem (\|), jako je tento `iconType|optionName1Value1|optionName2Value2`. Všimněte si, že názvy možností a hodnoty nejsou oddělené. Následující názvy možností stylu lze použít k označení stylu v Azure Maps:

- `al` – určuje neprůhlednost (alfa) značky. Může se jednat o číslo mezi 0 a 1.
- `an` – určuje kotvu PIN. Hodnoty x a y v pixelech, které jsou zadány ve formátu "x y".
- `co` – barva kódu PIN Musí být 24bitové hexadecimální Barva: `000000` pro `FFFFFF`.
- `la` – určuje kotvu popisku. Hodnoty x a y v pixelech, které jsou zadány ve formátu "x y".
- `lc` – Barva popisku Musí být 24, ale hexadecimální Barva: `000000` pro `FFFFFF`.
- `ls` – velikost popisku v pixelech Může být číslo větší než 0.
- `ro` – hodnota ve stupních pro otočení ikony. Může to být číslo mezi-360 a 360.
- `sc` – hodnota měřítka pro ikonu připnutí Může být číslo větší než 0.

Hodnoty popisků jsou zadány pro každé místo kódu PIN, nikoli jako jediná hodnota popisku, která se vztahuje na všechny značky v seznamu umístění. Hodnota popisku může být řetězec více znaků a zabalená pomocí jednoduchých uvozovek, aby se zajistilo, že není možné je označit jako styl nebo hodnotu umístění.

Například v Azure Maps přidání červené (`FF0000`) výchozí ikony s popiskem "ručička místa" umístěného níže (15 50) ikona na souřadnicích (zeměpisná délka:-122,349300, zeměpisná šířka: 47,620180) se dá provést pomocí následujícího parametru URL:

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

V mapě Google jsou čáry a mnohoúhelníky přidány do statického obrázku mapy pomocí parametru `path` v adrese URL. Parametr `path` přebírá ve stylu a seznam umístění, která se mají na mapě vykreslovat, jak je znázorněno níže:

```
&path=pathStyles|pathLocation1|pathLocation2|...
```

Další styly lze použít přidáním dalších parametrů `path` k adrese URL s jiným stylem a sadou umístění.

Umístění cest v Google Maps jsou zadaná ve formátu `latitude1,longitude1|latitude2,longitude2|…`. Cesty můžou být kódované nebo obsahují adresy pro body.

Styly cest ve službě Google Maps se přidávají s `optionName:value`formátu s více znaky oddělenými svislou čárou (\|), jako je tato `optionName1:value1|optionName2:value2`. Všimněte si, že názvy možností a hodnoty jsou oddělené dvojtečkou (:). Následující názvy možností stylu lze použít ke stylování cest v Google Maps:

- `color` – barva obrysu cesty nebo mnohoúhelníku. Může být 24bitové hexadecimální barva (`0xrrggbb`) 32, bitová hexadecimální barva (`0xrrggbbbaa`) nebo jedna z následujících hodnot: černá, hnědá, zelená, fialová, žlutá, modrá, šedá, oranžová, červená a bílá.
- `fillColor` – barva, kterou chcete vyplnit oblast cesty (mnohoúhelník). Může být 24bitové hexadecimální barva (`0xrrggbb`) 32, bitová hexadecimální barva (`0xrrggbbbaa`) nebo jedna z následujících hodnot: černá, hnědá, zelená, fialová, žlutá, modrá, šedá, oranžová, červená a bílá.
- `geodesic` – určuje, zda by měla být cesta čára, která následuje zakřivení země.
- `weight` – tloušťka čáry cesty v pixelech

Například ve službě Google Maps se k mapě mezi souřadnicemi (zeměpisná délka:-110, zeměpisná šířka: 45 a zeměpisná délka:-100, zeměpisná šířka: 50) s následujícím parametrem URL může přidat červená čára s 50% krytím a tloušťka 4 pixelů:

```
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

<center>

![](media/migrate-google-maps-web-services/google-maps-polyline.png)</center> na lomenou čáru mapy Google

**Po: Azure Maps**

V Azure Maps čáry a mnohoúhelníky lze také přidat do statického obrázku mapy zadáním parametru `path` v adrese URL. Podobně jako u služby Google Maps lze v tomto parametru zadat styl a seznam umístění a parametr `path` lze zadat vícekrát pro vykreslení více kruhů, čar a mnohoúhelníků s různými styly.

```
&path=pathStyles||pathLocation1|pathLocation2|...
```

Když přichází do umístění cest, Azure Maps vyžaduje, aby byly souřadnice ve formátu "Zeměpisná šířka", zatímco Google Maps používá formát "Zeměpisná délka". Všimněte si také, že je mezera, a ne čárka, která odděluje zeměpisnou délku a zeměpisnou šířku v Azure Maps. Azure Maps nepodporuje kódované cesty ani adresy pro body. Větší sady dat je možné nahrát do rozhraní Azure Maps API úložiště dat, jak je popsáno [zde](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage).

Styly cest v Azure Maps jsou přidány s `optionNameValue`formátu s více znaky oddělenými svislými písmeny (\|), jako je tento `optionName1Value1|optionName2Value2`. Všimněte si, že názvy možností a hodnoty nejsou oddělené. Následující názvy možností stylu lze použít k použití stylu cest v Azure Maps:

- `fa` – neprůhlednost barvy výplně (alfa), která se používá při vykreslování mnohoúhelníků. Může se jednat o číslo mezi 0 a 1.
- `fc` – Barva výplně použitá k vykreslení oblasti mnohoúhelníku
- `la` – neprůhlednost barvy čáry (alfa), která se používá při vykreslování čar a obrysu mnohoúhelníků. Může se jednat o číslo mezi 0 a 1.
- `lc` – Barva čáry použitá k vykreslení čar a obrysu mnohoúhelníků.
- `lw` – šířka čáry v pixelech
- `ra` – určuje poloměr kroužků v měřičích.

Například v Azure Maps může být k mapě mezi souřadnicemi (zeměpisná délka:-110, zeměpisná šířka: 45 a zeměpisná délka:-100, zeměpisná šířka: 50) s následujícím parametrem adresy URL přidána červená čára s 50% krytím a tloušťka 4 pixelů:

```
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

<center>

![Azure Maps lomená čáry](media/migrate-google-maps-web-services/azure-maps-polyline.png)</center>

## <a name="calculate-a-distance-matrix"></a>Vypočítat matici vzdálenosti

Azure Maps poskytuje rozhraní API pro výpočet doby trvání cest a vzdáleností mezi sadou umístění jako s maticí na dálku. Rozhraní API služby Azure Maps Distance je srovnatelné s rozhraním API služby Distance v Google Maps;

- [**Matice směrování**](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview): asynchronně vypočítá dobu trvání cesty a vzdálenosti pro sadu míst původu a místa. Je podporováno až 700 buněk na žádost (počet počátek vynásobený počtem cílů). S tímto omezením je třeba mít příklady možných dimenzí matice: 700x1, 50x10, 10x10, 28x25, 10x70.

> [!NOTE]
> Požadavek na rozhraní API na dálku se dá vytvořit jenom pomocí žádosti POST s informacemi o původu a cíli v těle žádosti. Azure Maps navíc vyžaduje, aby všechny zdroje a cíle byly souřadnice. Adresy musí být nejprve v INCODE.

Následující tabulka odkazuje na parametry rozhraní API Google Maps pomocí srovnatelných parametrů rozhraní API v Azure Maps.

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
| `region`                       | Není *k dispozici* – jedná se o funkci související s geografické kódování. Při použití Azure Maps geografického kódování rozhraní API použijte parametr `countrySet`. |
| `traffic_model`                | Není *k dispozici* – může určovat, jestli se mají používat data přenosů s parametrem `traffic`. |
| `transit_mode`                 | Nepodporují se *v* současné době přenosové matice na bázi neaktivních vzdáleností.  |
| `transit_routing_preference`   | Nepodporují se *v* současné době přenosové matice na bázi neaktivních vzdáleností.  |
| `units`                        | Není *k dispozici* – Azure Maps používá pouze systém metrik. |

> [!TIP]
> Všechny možnosti pokročilého směrování, které jsou k dispozici v rozhraní API směrování Azure Maps (směrování nákladní automobil, specifikace modulu, vyhněte se...) podporují rozhraní API Azure Maps Distance.

## <a name="get-a-time-zone"></a>Získat časové pásmo

Azure Maps poskytuje rozhraní API pro načtení časového pásma, ve kterém je souřadnice. Rozhraní API pro časové pásmo Azure Maps je srovnatelné s rozhraním API pro časové pásmo v Google Maps:

- [**Časové pásmo podle souřadnic**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates): Určete souřadnici a Získejte podrobnosti o časovém pásmu, ve kterém spadá.

Následující tabulka odkazuje na parametry rozhraní API Google Maps pomocí srovnatelných parametrů rozhraní API v Azure Maps.

| Parametr rozhraní API pro Google Maps | Srovnatelný parametr Azure Maps rozhraní API   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key` – viz také dokumentace k [ověřování pomocí Azure Maps](azure-maps-authentication.md) .       |
| `language`                  | `language` – viz dokumentace k [podporovaným jazykům](supported-languages.md) .    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

Kromě této Azure Maps platforma poskytuje také řadu dalších rozhraní API pro časové pásmo, které vám pomůžou s převody pomocí názvů a ID časových pásem:

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

Níže najdete další dokumentaci a prostředky služby Azure Maps REST.

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