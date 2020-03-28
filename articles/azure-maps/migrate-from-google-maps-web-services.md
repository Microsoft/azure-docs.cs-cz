---
title: 'Kurz: Migrace webových služeb z Map Google | Mapy Microsoft Azure'
description: Jak migrovat webové služby z Map Google do Map Microsoft Azure.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: d2f25f2b786686b8af9bad4ea8ce3c8aea9b589f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371460"
---
# <a name="migrate-web-service-from-google-maps"></a>Migrace webové služby z Map Google

Azure i Mapy Google poskytují přístup k prostorovým rozhraním API prostřednictvím webových služeb REST. Rozhraní rozhraní ROZHRANÍ API těchto platforem provádět podobné funkce. Ale každý z nich používá různé konvence pojmenování a objekty odpovědi.

V tabulce jsou uvedena rozhraní API služby Azure Maps, která mají podobné funkce jako uvedená rozhraní API služby Mapy Google.

| Rozhraní API služby Mapy Google | Rozhraní API služby Azure Maps                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| Směrech              | [Postupu](https://docs.microsoft.com/rest/api/maps/route)                                     |
| Matice vzdálenosti         | [Matice trasy](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)       |
| Geokódování               | [Hledat](https://docs.microsoft.com/rest/api/maps/search)                                   |
| Hledání míst           | [Hledat](https://docs.microsoft.com/rest/api/maps/search)                                   |
| Umístit automatické dokončování      | [Hledat](https://docs.microsoft.com/rest/api/maps/search)                                   |
| Přichytit k silnici            | Viz [Výpočet tras a tras oddílu.](#calculate-routes-and-directions)            |
| Rychlostní limity            | Viz [Reverzní geokód souřadnice](#reverse-geocode-a-coordinate) sekce.                  |
| Statická mapa              | [Vykreslování](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                       |
| Časové pásmo               | [Časové pásmo](https://docs.microsoft.com/rest/api/maps/timezone)                              |

V Azure Maps momentálně nejsou dostupná následující rozhraní API služby:

- Zvýšení
- Geografická poloha
- Podrobnosti o místech a fotky – Telefonní čísla a adresa URL webu jsou dostupné v rozhraní API pro vyhledávání V Azure Maps.
- Adresy URL map
- Nejbližší silnice - To je dosažitelné pomocí sady Web SDK, jak je znázorněno [zde](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Basic%20snap%20to%20road%20logic
), ale není k dispozici jako služba v současné době.
- Statické zobrazení ulice

Azure Maps má několik dalších webových služeb REST, které by mohly být zajímavé:

- [Prostorové operace](https://docs.microsoft.com/rest/api/maps/spatial): Přetížit složité prostorové výpočty a operace, jako je například geofencing, na službu.
- [Provoz](https://docs.microsoft.com/rest/api/maps/traffic): Přístup k datům o toku provozu v reálném čase a o incidentech.

## <a name="geocoding-addresses"></a>Geokódovací adresy

Geokódování je proces převodu adresy na souřadnici. Například "1 Microsoft way, Redmond, WA" převede na zeměpisnou délku: -122.1298, zeměpisná šířka: 47.64005. Souřadnice pak mohou být použity pro různé druhy účelů, jako je například umístění středové značky na mapě.

Azure Maps poskytuje několik metod pro geokódování adres:

- [**Geokódování volné adresy**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress): Zadejte jeden řetězec adresy a požadavek okamžitě zpracujte. "1 Microsoft way, Redmond, WA" je příkladem jednoho řetězce adresy. Toto rozhraní API se doporučuje, pokud potřebujete rychle geokódovat jednotlivé adresy.
- [**Geografické kódování strukturované adresy**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured): Zadejte části jedné adresy, například název ulice, město, zemi a PSČ, a požadavek okamžitě zpracujte. Toto rozhraní API se doporučuje, pokud potřebujete rychle geokódovat jednotlivé adresy a data jsou již analyzována do jednotlivých částí adresy.
- [**Geokódování dávkové adresy**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview): Vytvořte požadavek obsahující až 10 000 adres a nechte je zpracovat po určitou dobu. Všechny adresy budou geokódovány paralelně na serveru a po dokončení lze stáhnout celou sadu výsledků. To se doporučuje pro geokódování velkých datových sad.
- [**Přibližné vyhledávání**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): Toto rozhraní API kombinuje geokódování adresy s vyhledávaným bodem zájmu. Toto rozhraní API přebírá řetězec volného tvaru. Tento řetězec může být adresa, místo, orientační bod, bod zájmu nebo zájmová kategorie. Toto rozhraní API zpracovává požadavek téměř v reálném čase. Toto rozhraní API je doporučeno pro aplikace, kde uživatelé vyhledávají adresy nebo body zájmu ve stejném textovém poli.
- [**Přibližné dávkové vyhledávání**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): Vytvořte požadavek obsahující až 10 000 adres, míst, orientačních bodů nebo bodů zájmu a nechte je zpracovat po určitou dobu. Všechna data budou zpracována paralelně na serveru a po dokončení lze stáhnout úplnou sadu výsledků.

Následující tabulka křížově odkazuje na parametry rozhraní GOOGLE Maps API se srovnatelnými parametry rozhraní API v Azure Maps.

| Parametr ROZHRANÍ API Map Google | Srovnatelný parametr rozhraní API Azure Maps  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` a `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality`- město / město<br/>`municipalitySubdivision`– sousedství, sub / super město<br/>`countrySubdivision`- stát nebo provincie<br/>`countrySecondarySubdivision`- kraj<br/>`countryTertiarySubdivision`- okres<br/>`countryCode`- dvoupísmenný kód země |
| `key`                       | `subscription-key`– Podívejte se také na [dokumentaci ověřování pomocí Map Azure.](azure-maps-authentication.md) |
| `language`                  | `language`– Viz dokumentace [k podporovaným jazykům.](supported-languages.md)  |
| `region`                    | `countrySet`                       |

Příklad použití vyhledávací služby je popsán [zde](how-to-search-for-address.md). Přečtěte si [doporučené postupy pro vyhledávání](how-to-use-best-practices-for-search.md).

> [!TIP]
> Pole geokódování volné adresy a přibližná vyhledávací api `&typeahead=true` lze použít v režimu automatického dokončování přidáním do adresy URL požadavku. To bude serveru sdělit, že vstupní text je pravděpodobně částečný a hledání přejde do prediktivního režimu.

## <a name="reverse-geocode-a-coordinate"></a>Obrácení geokódu souřadnice

Reverzní geokódování je proces převodu zeměpisných souřadnic na přibližnou adresu. Souřadnice s "zeměpisnou délkou: -122.1298, zeměpisná šířka: 47.64005" převést na "1 Microsoft way, Redmond, WA".

Azure Maps poskytuje několik metod reverzního geokódování:

- [**Adresa reverzní geokodér**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse): Zadejte jednu zeměpisnou souřadnici získat přibližnou adresu odpovídající této souřadnice. Zpracuje požadavek téměř v reálném čase.
- [**Cross street reverse geocoder**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet): Zadejte jednu zeměpisnou souřadnici, abyste získali informace z ulice v okolí a okamžitě zpracovali požadavek. Můžete například obdržet následující křížové ulice 1st Ave a Main St.
- [**Reverzní geokodér s dávkovou adresou**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview): Vytvořte požadavek obsahující až 10 000 souřadnic a nechte je zpracovat po určitou dobu. Všechna data budou zpracována paralelně na serveru. Po dokončení požadavku si můžete stáhnout úplnou sadu výsledků.

Tato tabulka křížově odkazuje na parametry rozhraní GOOGLE Maps API se srovnatelnými parametry rozhraní API v Azure Maps.

| Parametr ROZHRANÍ API Map Google   | Srovnatelný parametr rozhraní API Azure Maps   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key`– Podívejte se také na [dokumentaci ověřování pomocí Map Azure.](azure-maps-authentication.md) |
| `language`                  | `language`– Viz dokumentace [k podporovaným jazykům.](supported-languages.md)  |
| `latlng`                    | `query`  |
| `location_type`             | *N/a*     |
| `result_type`               | `entityType`    |

Projděte si [doporučené postupy pro vyhledávání](how-to-use-best-practices-for-search.md).

Rozhraní Azure Maps reverzní geocoding API obsahuje některé další funkce, které nejsou k dispozici v Mapách Google. Tyto funkce mohou být užitečné pro integraci s vaší aplikací při migraci aplikace:

- Načtení dat omezení rychlosti
- Získejte informace o používání silnic: místní silnice, arteriální, omezený přístup, rampa a tak dále
- Načtení strany ulice, ve které je umístěna souřadnice

## <a name="search-for-points-of-interest"></a>Hledání bodů zájmu

Údaje o místě zájmu lze vyhledávat v Mapách Google pomocí rozhraní API pro vyhledávání míst. Toto rozhraní API nabízí tři různé způsoby vyhledávání bodů zájmu:

- **Najít místo z textu:** Vyhledá bod zájmu na základě jeho jména, adresy nebo telefonního čísla.
- **Hledání v okolí**: Vyhledá body zájmu, které jsou v určité vzdálenosti od místa.
- **Vyhledávání textu:** Vyhledá místa pomocí volného textu, který obsahuje informace o místě zájmu a umístění. Například "pizza v New Yorku" nebo "restaurace v blízkosti hlavníulice".

Azure Maps poskytuje několik vyhledávacích rozhraní API pro body zájmu:

- [**Vyhledávání bodů zájmu**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi): Hledání bodů zájmů podle názvu. Například "Starbucks".
- [**Vyhledávání v kategorii bzučů**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory): Hledání bodů zájmů podle kategorií. Například "restaurace".
- [**Hledání v okolí**](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby): Vyhledá zajímavosti, které jsou v určité vzdálenosti od místa.
- [**Přibližné vyhledávání**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): Toto rozhraní API kombinuje geokódování adresy s vyhledávaným bodem zájmu. Toto rozhraní API přebírá řetězec volného tvaru, který může být adresa, místo, orientační bod, bod zájmu nebo kategorie bodu zájmu. Zpracovává požadavek téměř v reálném čase. Toto rozhraní API je doporučeno pro aplikace, kde uživatelé vyhledávají adresy nebo body zájmu ve stejném textovém poli.
- [**Hledat v rámci geometrie**](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry): Hledání bodů zájmů, které jsou v zadané geometrii. Například vyhledejte bod zájmu v polygonu.
- [**Hledat podél trasy**](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute): Hledání bodů zájmů, které se nacházejí na zadané trase trasy.
- [**Přibližné dávkové vyhledávání**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): Vytvořte požadavek obsahující až 10 000 adres, míst, orientačních bodů nebo bodů zájmu. Zpracoval požadavek po určitou dobu. Všechna data budou zpracována paralelně na serveru. Po dokončení zpracování požadavku si můžete stáhnout úplnou sadu výsledků.

V současné době Azure Maps nemá srovnatelné rozhraní API pro vyhledávání textu v Mapách Google.

> [!TIP]
> Vyhledávání bodů po zem, vyhledávání v kategoriích bodů přehrávání a přibližná hledání lze použít v režimu automatického dokončování přidáním `&typeahead=true` do adresy URL požadavku. To bude serveru sdělit, že vstupní text je pravděpodobně částečný. Rozhraní API provede vyhledávání v prediktivním režimu.

Projděte si [doporučené postupy pro dokumentaci k vyhledávání.](how-to-use-best-practices-for-search.md)

### <a name="find-place-from-text"></a>Hledání místa z textu

Pomocí vyhledávání [Bodů zájmu](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) v Azure Maps a [přibližné hledání](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) můžete vyhledávat body zájmu podle názvu nebo adresy.

Tabulka křížově odkazuje na parametry rozhraní GOOGLE Maps API se srovnatelnými parametry rozhraní AZURE Maps API.

| Parametr ROZHRANÍ API Map Google | Srovnatelný parametr rozhraní API Azure Maps |
|---------------------------|-------------------------------------|
| `fields`                  | *N/a*                               |
| `input`                   | `query`                             |
| `inputtype`               | *N/a*                               |
| `key`                     | `subscription-key`– Podívejte se také na [dokumentaci ověřování pomocí Map Azure.](azure-maps-authentication.md) |
| `language`                | `language`– Viz dokumentace [k podporovaným jazykům.](supported-languages.md)  |
| `locationbias`            | `lat`a `lon` ) a`radius`<br/>`topLeft` a `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>Vyhledávání v okolí

Pomocí rozhraní [API pro vyhledávání v okolí](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) můžete v Azure Maps načíst blízká místa zájmů.

Tabulka zobrazuje parametry rozhraní GOOGLE Maps API se srovnatelnými parametry rozhraní API Azure Maps API.

| Parametr ROZHRANÍ API Map Google | Srovnatelný parametr rozhraní API Azure Maps  |
|---------------------------|--------------------------------------|
| `key`                       | `subscription-key`– Podívejte se také na [dokumentaci ověřování pomocí Map Azure.](azure-maps-authentication.md) |
| `keyword`                   | `categorySet` a `brandSet`        |
| `language`                  | `language`– Viz dokumentace [k podporovaným jazykům.](supported-languages.md)  |
| `location`                  | `lat` a `lon`                     |
| `maxprice`                  | *N/a*                               |
| `minprice`                  | *N/a*                               |
| `name`                      | `categorySet` a `brandSet`        |
| `opennow`                   | *N/a*                               |
| `pagetoken`                 | `ofs` a `limit`                   |
| `radius`                    | `radius`                            |
| `rankby`                    | *N/a*                               |
| `type`                      | `categorySet –`Podívejte se na [dokumentaci k podporovaným kategoriím vyhledávání.](supported-search-categories.md)   |

## <a name="calculate-routes-and-directions"></a>Výpočet tras a tras

Vypočítejte trasy a trasy pomocí Azure Maps. Azure Maps má mnoho stejných funkcí jako služba směrování Google Maps, například:

- Časy příjezdu a odjezdu.
- Dopravní trasy v reálném čase a na základě prediktivních tras.
- Různé druhy dopravy. Jako, řízení, chůze, jízda na kole.

> [!NOTE]
> Azure Maps vyžaduje, aby všechny trasové body byly souřadnicemi. Adresy musí být nejprve geokódovány.

Služba směrování Azure Maps poskytuje následující rozhraní API pro výpočet tras:

- [**Vypočítat postup**](https://docs.microsoft.com/rest/api/maps/route/getroutedirections): Vypočítejte trasu a požadavek okamžitě zpracujte. Toto rozhraní API podporuje požadavky GET i POST. Požadavky POST se doporučují při zadávání velkého počtu trasových bodů nebo při použití velkého počtu možností trasy, aby se zajistilo, že požadavek na adresu URL nebude příliš dlouhý a způsobí problémy. Směr trasy POST v Azure Maps má možnost, která trvá tisíce [podpůrných bodů](https://docs.microsoft.com/rest/api/maps/route/postroutedirections#supportingpoints) a použije je k opětovnému vytvoření logické trasy mezi nimi (přichycení k silnici). 
- [**Dávkový postup**](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview): Vytvořte požadavek obsahující až 1 000 požadavků na trasu a nechte je zpracovat po určitou dobu. Všechna data budou zpracována paralelně na serveru a po dokončení lze stáhnout úplnou sadu výsledků.
- [**Služby mobility**](https://docs.microsoft.com/rest/api/maps/mobility): Výpočet tras a tras pomocí veřejné dopravy.

Tabulka křížově odkazuje na parametry rozhraní GOOGLE Maps API se srovnatelnými parametry rozhraní API v Azure Maps.

| Parametr ROZHRANÍ API Map Google    | Srovnatelný parametr rozhraní API Azure Maps  |
|------------------------------|--------------------------------------|
| `alternatives`                 | `maxAlternatives`                  |
| `arrival_time`                | `arriveAt`                          |
| `avoid`                        | `avoid`                            |
| `departure_time`              | `departAt`                          |
| `destination`                  | `query`– souřadnice ve formátu`"lat0,lon0:lat1,lon1…."`  |
| `key`                          | `subscription-key`– Podívejte se také na [dokumentaci ověřování pomocí Map Azure.](azure-maps-authentication.md) |
| `language`                     | `language`– Viz dokumentace [k podporovaným jazykům.](supported-languages.md)   |
| `mode`                         | `travelMode`                       |
| `optimize`                     | `computeBestOrder`                 |
| `origin`                       | `query`                            |
| `region`                       | *Není v dispozici* – Tato funkce souvisí s geokódováním. Při použití rozhraní API pro geokódování Azure Maps použijte parametr *countrySet.*  |
| `traffic_model`               | *Není k zapisovat* – lze určit pouze, pokud mají být s parametrem *provozu* použity provozní údaje. |
| `transit_mode`                | Zobrazit [dokumentaci služeb mobility](https://docs.microsoft.com/rest/api/maps/mobility) |
| `transit_routing_preference` | Zobrazit [dokumentaci služeb mobility](https://docs.microsoft.com/rest/api/maps/mobility) |
| `units`                        | *Není k mna* – Azure Maps používá jenom metrický systém.  |
| `waypoints`                    | `query`                            |

> [!TIP]
> Ve výchozím nastavení rozhraní API trasy Azure Maps vrátí pouze souhrn. Vrátí vzdálenost a časy a souřadnice trasy. Pomocí `instructionsType` parametru načtěte pokyny k řadovému pořadí. A pomocí `routeRepresentation` parametru odfiltrujte souhrn a trasu trasy.

Rozhraní API pro směrování Azure Maps obsahuje další funkce, které nejsou v Mapách Google dostupné. Při migraci aplikace zvažte použití těchto funkcí, které by mohly být užitečné.

- Podpora pro typ trasy: nejkratší, nejrychlejší, trilling, a nejvíce úsporné.
- Podpora pro další cestovní režimy: autobus, motocykl, taxi, nákladní automobil a dodávka.
- Podpora pro 150 trasových bodů.
- Výpočet více časů cesty v jednom požadavku; historický provoz, živý provoz, žádný provoz.
- Vyhněte se dalším typům silnic: spolujízda, nezpevněné silnice, již použité silnice.
- Zadejte vlastní oblasti, kterým se chcete vyhnout.
- Omezte výšku, kterou může trasa vystoupat.
- Trasa na základě specifikací motoru. Vypočítejte trasy pro spalovací nebo elektrická vozidla na základě specifikací motoru a zbývající palivo nebo náboj.
- Podpořte parametry trasy užitkových vozidel. Například rozměry vozidla, hmotnost, počet axelů a typ nákladu.
- Určete maximální rychlost vozidla.

Kromě toho služba směrování v Azure Maps podporuje [výpočet směrovatelných rozsahů](https://docs.microsoft.com/rest/api/maps/route/getrouterange). Výpočet směrovatelných rozsahů se také označuje jako isochrony. To znamená vytvoření polygonu pokrývající oblast, do které lze cestovat v libovolném směru od výchozího bodu. Vše pod stanovenou dobu nebo množství paliva nebo poplatku.

Projděte si [doporučené postupy pro dokumentaci směrování.](how-to-use-best-practices-for-routing.md)

## <a name="retrieve-a-map-image"></a>Načtení obrázku mapy

Azure Maps poskytuje rozhraní API pro vykreslování statických mapových bitových kopií s překrytými daty. Rozhraní [API pro vykreslení obrázků map](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) v Mapách Azure je srovnatelné s rozhraním API statické mapy v Mapách Google.

> [!NOTE]
> Azure Maps vyžaduje, aby centrum, všechny značky a umístění cesty byly souřadnice ve formátu "zeměpisná šířka". Vzhledem k tomu, Google Maps používá "zeměpisnou šířku, délka" formátu. Adresy budou muset být nejprve geokódovány.

Tabulka křížově odkazuje na parametry rozhraní GOOGLE Maps API se srovnatelnými parametry rozhraní API v Azure Maps.

| Parametr ROZHRANÍ API Map Google | Srovnatelný parametr rozhraní API Azure Maps  |
|---------------------------|--------------------------------------|
| `center`                    | `center`                           |
| `format`                    | `format`– zadáno jako součást cesty URL. V současné době je podporován pouze png. |
| `key`                       | `subscription-key`– Podívejte se také na [dokumentaci ověřování pomocí Map Azure.](azure-maps-authentication.md) |
| `language`                  | `language`– Viz dokumentace [k podporovaným jazykům.](supported-languages.md)  |
| `maptype`                   | `layer`a `style` – viz [Dokumentace k podporovaným mapovým stylům.](supported-map-styles.md) |
| `markers`                   | `pins`                             |
| `path`                      | `path`                             |
| `region`                    | *Není k dispozici* – Jedná se o funkci související s geokódováním. Parametr `countrySet` použijte při použití geokódovacího rozhraní API Azure Maps.  |
| `scale`                     | *N/a*                              |
| `size`                      | `width`a `height` – může být až 8192x8192 ve velikosti. |
| `style`                     | *N/a*                              |
| `visible`                   | *N/a*                              |
| `zoom`                      | `zoom`                             |

> [!NOTE]
> V systému dlaždic Azure Maps jsou dlaždice dvakrát větší než dlaždice map používané v Mapách Google. Jako taková se hodnota úrovně zvětšení v Azure Maps zobrazí o jednu úroveň přiblížení blíže v Azure Maps ve srovnání s Google Maps. Chcete-li tento rozdíl kompenzovat, zmenšete úroveň přiblížení v požadavcích, které migrujete.

Další informace naleznete v [návodu k zobrazení rozhraní API pro vykreslení obrázku mapy](how-to-render-custom-data.md).

Kromě možnosti generovat statický obrázek mapy poskytuje vykreslovací služba Azure Maps možnost přímého přístupu k dlaždicím mapy v rastrovém (PNG) a vektorovém formátu:

- [**Dlaždice mapy**](https://docs.microsoft.com/rest/api/maps/render/getmaptile): Načíst rastrové (PNG) a vektorové dlaždice pro základní mapy (silnice, hranice, pozadí).
- [**Dlaždice mapových snímků**](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile): Načtení dlaždic leteckých a satelitních snímků.

> [!TIP]
> Mnoho aplikací Map Google, kde před několika lety přešlo z interaktivních mapových zážitků na statické mapové obrázky. To bylo provedeno jako metoda úspory nákladů. V Azure Maps je obvykle nákladově efektivnější použít interaktivní ovládací prvek mapy ve webové sdk. Interaktivní ovládací prvky mapy účtují počet načtech dlaždic. Dlaždice map v Azure Maps jsou velké. Často trvá pouze několik dlaždic, aby se znovu vytvořilo stejné zobrazení mapy jako statická mapa. Dlaždice mapy jsou automaticky ukládány do mezipaměti prohlížečem. Jako takový ovládací prvek interaktivní mapy často generuje zlomek transakce při reprodukci statické zobrazení mapy. Posouvání a zvětšování načte více dlaždic; však existují možnosti v ovládacím prvku mapy zakázat toto chování. Interaktivní ovládací prvek mapy také poskytuje mnohem více možností vizualizace než služby statické mapy.

### <a name="marker-url-parameter-format-comparison"></a>Porovnání parametrů adresy URL značky

**Před: Mapy Google**

Přidejte značky `markers` pomocí parametru v adrese URL. Parametr `markers` přebírá styl a seznam míst, která mají být vykreslena na mapě s tímto stylem, jak je znázorněno níže:

```
&markers=markerStyles|markerLocation1|markerLocation2|...
```

Chcete-li přidat další `markers` styly, použijte parametry adresy URL s jiným stylem a sadou umístění.

Určete umístění značek ve formátu "zeměpisná šířka,zeměpisná délka".

Přidejte styly `optionName:value` značek s formátem,\|s více styly oddělenými trubcemi ( ) znaky, jako je tato "optionName1:value1\|optionName2:value2". Všimněte si, že názvy a hodnoty možností jsou odděleny dvojtečkou (:). Značky stylu v Mapách Google slouží k následujícím názvům možností stylu:

- `color`– Barva výchozí ikony značky. Může se jedná o 24bitovou šestnáctkovou barvu (`0xrrggbb`) nebo jednu z následujících hodnot; `black`, `brown`, `green`, `purple`, `yellow`, `blue`, `gray`, `orange`, `red`, `white`.
- `label`– Jeden velký alfanumerický znak, který se zobrazí v horní části ikony.
- `size`- Velikost značky. Může `tiny`být `mid`, `small`, nebo .

Pro vlastní ikony v Mapách Google použijte následující názvy možností stylu:

- `anchor`– Určuje, jak zarovnat obraz ikony ke souřadninici. Může se na nich vyvěšovat hodnota obrazového bodu (x,y) nebo jedna z následujících hodnot; `top`, `bottom` `left`, `right` `center`, `topleft` `topright`, `bottomleft`, `bottomright`, , , nebo .
- `icon`– Adresa URL ukazující na obrázek ikony.

Například přidáme červenou značku střední velikosti na mapu při zeměpisné délky: -110, zeměpisná šířka: 45:

```
&markers=color:red|size:mid|45,-110
```

<center>

![Značka Map Google](media/migrate-google-maps-web-services/google-maps-marker.png)</center>

**Po: Mapy Azure**

Přidejte značky do statického obrázku mapy zadáním parametru `pins` v adrese URL. Podobně jako Mapy Google zadejte styl a seznam míst v parametru. Parametr `pins` lze zadat vícekrát pro podporu značek s různými styly.

```
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

Chcete-li použít další `pins` styly, přidejte do adresy URL další parametry s jiným stylem a sadou umístění.

V Azure Maps musí být umístění pinu ve formátu "zeměpisná šířka". Mapy Google používají formát "zeměpisná šířka,zeměpisná délka". Mezera, nikoli čárka, odděluje zeměpisnou délku a šířku ve formátu Azure Maps.

Určuje `iconType` typ špendlíku, který chcete vytvořit. Může mít následující hodnoty:

- `default`– Výchozí ikona pinu.
- `none`– Nezobrazí se žádná ikona, budou vykresleny pouze popisky.
- `custom`– Určuje, že má být použita vlastní ikona. Adresu URL ukazující na obrázek ikony lze `pins` přidat na konec parametru za informace o umístění špendlíku.
- `{udid}`– jedinečné ID dat (UDID) pro ikonu uloženou v platformě Úložiště dat Azure Maps.

Přidejte styly `optionNameValue` špendlíků s formátem. Oddělte více stylů\|pomocí znaků kanálu ( ). Například: `iconType|optionName1Value1|optionName2Value2`. Názvy a hodnoty možností nejsou odděleny. Ke značkám stylů použijte následující názvy možností stylu:

- `al`– Určuje krytí (alfa) značky. Zvolte číslo mezi 0 a 1.
- `an`– Určuje kotvu kolíku. Zadejte hodnoty obrazových bodů X a y ve formátu "x y".
- `co`– Barva kolíku. Určete 24bitovou šestnáctkovou barvu: `000000` do `FFFFFF`.
- `la`– Určuje kotvu popisku. Zadejte hodnoty obrazových bodů X a y ve formátu "x y".
- `lc`– Barva štítku. Určete 24bitovou šestnáctkovou barvu: `000000` do `FFFFFF`.
- `ls`– Velikost popisku v pixelech. Zvolte číslo větší než 0.
- `ro`– Hodnota ve stupních otočit ikonu. Zvolte číslo mezi -360 a 360.
- `sc`– Hodnota měřítka pro ikonu špendlíku. Zvolte číslo větší než 0.

Zadejte hodnoty popisků pro každé umístění kolíku. Tento přístup je efektivnější než použití hodnoty jednoho popisku na všechny značky v seznamu umístění. Hodnota popisku může být řetězec více znaků. Zabalte řetězec s jednoduchými uvozovkami, abyste zajistili, že není chybován jako hodnota stylu nebo umístění.

Přidáme červenou (`FF0000`) výchozí ikonu s popiskem "Space Needle", umístěným níže (15 50). Ikona je v zeměpisné době: -122.349300, zeměpisná šířka: 47.620180:

```
&pins=default|coFF0000|la15 50||'Space Needle' -122.349300 47.620180
```

<center>

![Značka Azure Maps](media/migrate-google-maps-web-services/azure-maps-marker.png)</center>

Přidejte tři kolíky s hodnotami štítku "1", "2" a "3":

```
&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12
```

<center>

![Azure Maps více značek](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)</center>

### <a name="path-url-parameter-format-comparison"></a>Porovnání parametrů adresy URL cesty

**Před: Mapy Google**

Přidejte čáry a polygon do `path` statického obrázku mapy pomocí parametru v adrese URL. Parametr `path` přebírá styl a seznam míst, která mají být vykreslena na mapě, jak je znázorněno níže:

```
&path=pathStyles|pathLocation1|pathLocation2|...
```

Přidáním dalších `path` parametrů do adresy URL s jiným stylem a sadou umístění použijte další styly.

Umístění cest jsou `latitude1,longitude1|latitude2,longitude2|…` určena formátem. Cesty mohou být kódovány nebo obsahují adresy pro body.

Přidejte styly `optionName:value` cest s formátem,\|oddělte více stylů pomocí znaků kanálu ( ). A samostatné názvy možností a hodnoty s dvojtečkou (:). Takhle: `optionName1:value1|optionName2:value2`. Následující názvy možností stylu lze použít ke stylu cesty v Mapách Google:

- `color`– Barva obrysu cesty nebo polygonu. Může to být 24bitová`0xrrggbb`šestnáctková barva ( ),`0xrrggbbbaa`32bitová šestnáctková barva ( ) nebo jedna z následujících hodnot: černá, hnědá, zelená, fialová, žlutá, modrá, šedá, oranžová, červená, bílá.
- `fillColor`– Barva, kterou chcete vyplnit oblast cesty (polygon). Může to být 24bitová`0xrrggbb`šestnáctková barva ( ),`0xrrggbbbaa`32bitová šestnáctková barva ( ) nebo jedna z následujících hodnot: černá, hnědá, zelená, fialová, žlutá, modrá, šedá, oranžová, červená, bílá.
- `geodesic`– Označuje, zda by cesta měla být čárou, která následuje po zakřivení země.
- `weight`– Tloušťka čáry cesty v obrazových bodech.

Přidejte do mapy mezi souřadnicemi v parametru URL krytí červené čáry a tloušťku obrazových bodů. V níže uvedeném příkladu má čára krytí 50 % a tloušťku čtyř obrazových bodů. Souřadnice jsou zeměpisné délky: -110, zeměpisná šířka: 45 a zeměpisná délka: -100, zeměpisná šířka: 50.

```
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

<center>

![Pointa Map Google](media/migrate-google-maps-web-services/google-maps-polyline.png)</center>

**Po: Mapy Azure**

Přidejte čáry a polygony do statického obrázku mapy zadáním parametru `path` v adrese URL. Podobně jako Mapy Google zadejte v tomto parametru styl a seznam míst. Zadejte `path` parametr vícekrát, chcete-li vykreslit více kružnic, čar a mnohonoly s různými styly.

```
&path=pathStyles||pathLocation1|pathLocation2|...
```

Pokud jde o umístění cest, Azure Maps vyžaduje, aby souřadnice byly ve formátu "zeměpisné šířky zeměpisné délky". Mapy Google používají formát "zeměpisná šířka,zeměpisná délka". Mezera, nikoli čárka, odděluje zeměpisnou délku a šířku ve formátu Azure Maps. Azure Maps nepodporuje kódované cesty nebo adresy pro body. Nahrajte větší sady dat jako soubor GeoJSON do rozhraní API úložiště dat Azure Maps, jak je zdokumentováno [zde](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage).

Přidejte styly `optionNameValue` cest s formátem. Oddělte více\|stylů podle znaků `optionName1Value1|optionName2Value2`kanálu ( ), jako je tento . Názvy a hodnoty možností nejsou odděleny. K stylům cest v Azure Maps použijte následující názvy možností stylu:

- `fa`- Krytí barvy výplně (alfa) použité při vykreslování polygonů. Zvolte číslo mezi 0 a 1.
- `fc`- Barva výplně použitá k vykreslení oblasti polygonu.
- `la`– Krytí barvy čáry (alfa) použité při vykreslování čar a obrysu polygonů. Zvolte číslo mezi 0 a 1.
- `lc`– Barva čáry použitá k vykreslení čar a obrys uponíven.
- `lw`– Šířka čáry v pixelech.
- `ra`– Určuje poloměr kružnic v metrech.

V parametru URL přidejte krytí červené čáry a tloušťku obrazových bodů mezi souřadnice. V níže uvedeném příkladu má čára 50 % krytí a tloušťku čtyř obrazových bodů. Souřadnice mají následující hodnoty: zeměpisná délka: -110, zeměpisná šířka: 45 a zeměpisná délka: -100, zeměpisná šířka: 50.

```
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

<center>

![Předlavená čára Azure Maps](media/migrate-google-maps-web-services/azure-maps-polyline.png)</center>

## <a name="calculate-a-distance-matrix"></a>Výpočet matice vzdálenosti

Azure Maps poskytuje rozhraní API matice vzdálenosti. Toto rozhraní API slouží k výpočtu doby jízdy a vzdáleností mezi sadou umístění s maticí vzdálenosti. Je to srovnatelné s rozhraním API pro matici vzdáleností v Mapách Google.

- [**Matice trasy**](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview): Asynchronně vypočítá doby a vzdálenosti jízdy pro sadu počátků a cílů. Podporuje až 700 buněk na požadavek. To je počet původů vynásobený počtem destinací. S ohledem na toto omezení jsou příklady možných dimenzí matice: 700x1, 50x10, 10x10, 28x25, 10x70.

> [!NOTE]
> Požadavek na rozhraní API matice vzdálenosti lze provést pouze pomocí požadavku POST s informacemi o původu a cíli v těle požadavku. Azure Maps navíc vyžaduje, aby všechny původy a cíle byly souřadnicemi. Adresy budou muset být nejprve geokódovány.

Tato tabulka křížově odkazuje na parametry rozhraní GOOGLE Maps API se srovnatelnými parametry rozhraní AZURE Maps API.

| Parametr ROZHRANÍ API Map Google      | Srovnatelný parametr rozhraní API Azure Maps  |
|--------------------------------|--------------------------------------|
| `arrivial_time`                | `arriveAt`                           |
| `avoid`                        | `avoid`                              |
| `depature_time`                | `departAt`                           |
| `destinations`                 | `destination`– v orgánu požadavku POST uveďte jako GeoJSON. |
| `key`                          | `subscription-key`– Podívejte se také na [dokumentaci ověřování pomocí Map Azure.](azure-maps-authentication.md) |
| `language`                     | `language`– Viz dokumentace [k podporovaným jazykům.](supported-languages.md)  |
| `mode`                         | `travelMode`                         |
| `origins`                      | `origins`– v orgánu požadavku POST uveďte jako GeoJSON.  |
| `region`                       | *Není v dispozici* – Tato funkce souvisí s geokódováním. Parametr `countrySet` použijte při použití geokódovacího rozhraní API Azure Maps. |
| `traffic_model`                | *Není k zapisovat* – lze určit `traffic` pouze, pokud mají být s parametrem použity provozní údaje. |
| `transit_mode`                 | *Není podporována* – matice vzdálenosti založené na tranzitu nejsou aktuálně podporovány.  |
| `transit_routing_preference`   | *Není podporována* – matice vzdálenosti založené na tranzitu nejsou aktuálně podporovány.  |
| `units`                        | *Není k mna* – Azure Maps používá jenom metrický systém. |

> [!TIP]
> Všechny pokročilé možnosti směrování, které jsou k dispozici v rozhraní API směrování Azure Maps, jsou podporované v rozhraní API matice vzdálenosti Azure Maps. Mezi pokročilé možnosti směrování patří: směrování nákladních vozidel, specifikace motoru a tak dále.

Projděte si [doporučené postupy pro dokumentaci směrování.](how-to-use-best-practices-for-routing.md)

## <a name="get-a-time-zone"></a>Získání časového pásma

Azure Maps poskytuje rozhraní API pro načítání časové pásmo souřadnice. Rozhraní API časového pásma Azure Maps je srovnatelné s rozhraním API časového pásma v Mapách Google:

- [**Časové pásmo podle souřadnic**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates): Zadejte souřadnici a přijměte podrobnosti časového pásma souřadnice.

Tato tabulka křížově odkazuje na parametry rozhraní GOOGLE Maps API se srovnatelnými parametry rozhraní API v Azure Maps.

| Parametr ROZHRANÍ API Map Google | Srovnatelný parametr rozhraní API Azure Maps   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key`– Podívejte se také na [dokumentaci ověřování pomocí Map Azure.](azure-maps-authentication.md)       |
| `language`                  | `language`– Viz dokumentace [k podporovaným jazykům.](supported-languages.md)    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

Kromě tohoto rozhraní API Azure Maps poskytuje řadu rozhraní API časové pásma. Tato řešení API převádějí čas na základě názvů nebo ID časového pásma:

- [**Časové pásmo podle ID**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebyid): Vrátí aktuální, historické a budoucí informace o časovém pásmu pro zadané ID časového pásma IANA.
- [**Časové pásmo Výčtu IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumiana): Vrátí úplný seznam ID časového pásma IANA. Aktualizace služby IANA se projeví v systému během jednoho dne.
- [**Časové pásmo Výčet Windows**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumwindows): Vrátí úplný seznam ID časového pásma systému Windows.
- [**Verze Časového pásma IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneianaversion): Vrátí aktuální číslo verze IANA používané v Azure Maps.
- [**Časové pásmo Windows na IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonewindowstoiana): Vrátí odpovídající ID IANA, dané platné ID časového pásma systému Windows. Pro jedno ID systému Windows může být vráceno více ID ID ID ID.

## <a name="client-libraries"></a>Klientské knihovny

Azure Maps poskytuje klientské knihovny pro následující programovací jazyky:

- JavaScript, TypeScript, Node.js – [dokumentace](how-to-use-services-module.md) \| [NPM balíček](https://www.npmjs.com/package/azure-maps-rest)

Tyto open-source klientské knihovny jsou určeny pro jiné programovací jazyky:

- .NET Standard 2.0 – [GitHub projekt](https://github.com/perfahlen/AzureMapsRestServices) \| [NuGet balíček](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="additional-resources"></a>Další zdroje

Následuje další dokumentace a prostředky pro služby Azure Maps REST.

- [Osvědčené postupy pro hledání](how-to-use-best-practices-for-search.md)
- [Hledání adresy](how-to-search-for-address.md)
- [Doporučené postupy pro směrování](how-to-use-best-practices-for-routing.md)
- [Referenční dokumentace rozhraní API služby Azure Maps REST](https://docs.microsoft.com/rest/api/maps/)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o službách Azure Maps REST.

> [!div class="nextstepaction"]
> [Doporučené postupy pro používání vyhledávací služby](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [Doporučené postupy pro používání služby směrování](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [Použití modulu služeb (Web SDK)](how-to-use-best-practices-for-routing.md)

> [!div class="nextstepaction"]
> [Ukázky kódu](https://docs.microsoft.com/samples/browse/?products=azure-maps)
