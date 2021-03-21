---
title: Glosář Azure Maps | Microsoft Docs
description: Glosář běžně používaných termínů spojených s Azure Maps, Location-Based službami a GIS.
author: rbrundritt
ms.author: richbrun
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 05071be4fc1705787a649d331613e8d9399f983f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92897173"
---
# <a name="glossary"></a>Glosář

Následující seznam popisuje běžná slova používaná s Azure Maps službami.

## <a name="a"></a>A

<a name="address-validation"></a>**Ověřování adres**: proces ověřování existence adresy.

<a name="advanced-routing"></a>**Rozšířené směrování**: kolekce služeb, které provádějí předběžné operace s využitím dat o směrování na cestách; například výpočet dosažitelných rozsahů (izochronů), zástupných tříd a požadavků dávkové trasy.

<a name="aerial-imagery"></a>**Letecké satelitní** obraz: viz [satelitní](#satellite-imagery)obrazové. 

<a name="along-a-route-search"></a>**Podél hledání tras**: prostorový dotaz, který vyhledává data v zadaném čase nebo vzdálenosti od cesty trasy.

<a name="altitude"></a>**Nadmořská** Výška: výška nebo svislé zvýšení úrovně bodu nad referenční plochu. Měření nadmořského nadlimitního množství vychází z konkrétního referenčního údaje, jako je například střední úroveň moře. Viz také zvýšení úrovně oprávnění.

<a name="ambiguous"></a>**Dvojznačný**: stav nejistoty v klasifikaci dat, která existuje v případě, že může být objekt vhodně přiřazen dvěma nebo více hodnotám pro daný atribut. Například při geografickém kódování "CA" jsou vraceny dva dvojznačné výsledky: "Kanada" a "Kalifornie". "CA" je země nebo oblast a kód stavu, v uvedeném pořadí pro "Kanada" a "Kalifornie". 

<a name="annotation"></a>**Anotace**: text nebo grafika zobrazená na mapě k poskytnutí informací uživateli. Poznámka může identifikovat nebo popsat konkrétní mapovou entitu, zadat obecné informace o oblasti na mapě nebo zadat informace o samotné mapě.

<a name="antimeridian"></a>**Antipoledníku**: označuje se také jako 180.<sup>tý</sup> . To je bod, kde-180 stupňů a 180 stupňů délky. Což je opak prvotního poledníku na světě.

<a name="application-programming-interface-api"></a>**Rozhraní API (Application Programming Interface)**: specifikace, která vývojářům umožňuje vytvářet aplikace.

<a name="api-key"></a>**Klíč rozhraní API**: viz [ověřování sdíleného klíče](#shared-key-authentication).

<a name="area-of-interest-aoi"></a>**Oblast zájmu (AOI)**: rozsah, který slouží k definování oblasti pro fokus pro mapu nebo pro produkci databáze.

<a name="asset-tracking"></a>**Sledování prostředků**: proces sledování umístění assetu, jako je osoba, vozidlo nebo nějaký jiný objekt.

<a name="asynchronous-request"></a>**Asynchronní požadavek**: požadavek HTTP, který otevře připojení, a odešle požadavek na server, který vrátí identifikátor asynchronního požadavku, a pak ukončí připojení. Server pokračuje ve zpracování žádosti a uživatel může stav ověřit pomocí identifikátoru. Po dokončení zpracování žádosti může uživatel stáhnout odpověď. Tento typ požadavku se používá pro dlouho běžící procesy.

<a name="autocomplete"></a>**Automatické dokončování**: funkce v aplikaci, která předpovídá zbývající část slova, které uživatel zadává. 

<a name="autosuggest"></a>Automatické **návrhy**: funkce v aplikaci, která předpovídá logické možnosti pro to, co uživatel zadá.

<a name="azure-location-based-services-lbs"></a>**Azure Location Based Services (kg)**: dřívější název Azure Maps, pokud byl ve verzi Preview.

<a name="azure-active-directory"></a>**Azure Active Directory (Azure AD)**: Azure AD je cloudová služba pro správu identit a přístupu od Microsoftu. Azure Maps integrace služby Azure AD je aktuálně dostupná ve verzi Preview pro všechna Azure Maps API. Azure AD podporuje řízení přístupu na základě role Azure (Azure RBAC), které umožňuje jemně odstupňovaný přístup k Azure Maps prostředkům. Další informace o Azure Maps integraci služby Azure AD najdete v tématu [Azure Maps a Azure AD](azure-maps-authentication.md) a [správa ověřování v Azure Maps](how-to-manage-authentication.md).

<a name="azure-maps-key"></a>**Klíč Azure Maps**: viz [ověřování sdíleného klíče](#shared-key-authentication).

## <a name="b"></a>B

<a name="base-map"></a>**Základní mapa**: část aplikace mapy, která zobrazuje referenční informace na pozadí, jako jsou cesty, orientačních bodů a politické hranice.

<a name="batch-request"></a>**Požadavek Batch**: Proces kombinování více požadavků do jediného požadavku.

<a name="bearing"></a>**Nesoucí**: horizontální směr bodu ve vztahu k jinému bodu. Tato hodnota se vyjadřuje jako úhel relativní vůči severním, od 0 do 360 stupňů po směru hodinových ručiček. 

<a name="boundary"></a>**Hranice**: čára nebo mnohoúhelník oddělení sousedících politických entit, jako jsou země/oblasti, oblasti a vlastnosti. Hranice je řádek, který může nebo nemusí splňovat fyzické funkce, jako jsou řeky, Mountains nebo zdi.

<a name="bounds"></a>**Meze**: viz [ohraničující rámeček](#bounding-box).

<a name="bounding-box"></a>**Ohraničující rámeček**: Sada souřadnic, které slouží k reprezentaci obdélníkové oblasti na mapě. 

## <a name="c"></a>C

<a name="cadastre"></a>**Cadastre**: záznam registrované půdy a vlastností. Viz také [parcela](#parcel).

<a name="camera"></a>**Kamera**: v kontextu interaktivního ovládacího prvku mapy fotoaparát definuje pole mapy zobrazení. Zobrazení kamery se určuje na základě několika parametrů mapování: Center, úroveň přiblížení, rozteč, s sebou. 

<a name="centroid"></a>**Těžiště**: geometrické centrum funkce. Těžiště řádku by byl střední bod, zatímco těžiště mnohoúhelníku by byl jeho středem oblasti.

<a name="choropleth-map"></a>**Mapa kartogramy**: tematické mapování, ve kterém jsou oblasti vybarvené v poměru k měření statistické proměnné. Tato statistická proměnná se zobrazí na mapě. Například vybarvení hranice každého stavu USA na základě jeho relativního naplnění na všechny ostatní stavy.

<a name="concave-hull"></a>**Konkávní trup**: tvar, který představuje možná konkávní geometrii, která obklopuje všechny tvary v zadané datové sadě. Vygenerovaný tvar je podobný obtékání dat s plastovým zabalením a pak je zahřívání, což způsobí velké rozpětí mezi body až konkávní v dalších datových bodech.

<a name="consumption-model"></a>**Model spotřeby**: informace, které definují rychlost, s jakou vozidlo spotřebovává palivo nebo elektřinu. Viz také [dokumentace k modelu spotřeby](consumption-model.md).

<a name="control"></a>**Control**: samostatně obsažená nebo opakovaně použitelná komponenta skládající se z grafického uživatelského rozhraní, které definuje sadu chování pro rozhraní. Například mapový ovládací prvek je obvykle část uživatelského rozhraní, která načte interaktivní mapu.

<a name="convex-hull"></a>**Vypuklý trup**: vypuklý trup je tvar, který představuje minimální konvexní geometrii, která obklopuje všechny tvary v zadané datové sadě. Vygenerovaný tvar je podobný jako při obtékání elastického pásma kolem datové sady.

<a name="coordinate"></a>**Souřadnice**: skládá se z hodnoty Zeměpisná délka a zeměpisná šířka využité k reprezentaci umístění na mapě.

<a name="coordinate-system"></a>**Souřadnicový systém**: referenční rozhraní, pomocí něhož lze definovat pozice bodů v prostoru ve dvou nebo třech dimenzích.

<a name="country-code"></a>**Kód země**: jedinečný identifikátor země nebo oblasti na základě standardu ISO. ISO2 je kód o dvou znacích pro zemi nebo oblast (například US), která ISO3 představuje kód se třemi znaky (například USA).

<a name="country-subdivision"></a>**Dělení země**: rozdělení země nebo oblasti první úrovně, obvykle označované jako stát nebo provincie.

<a name="country-secondary-subdivision"></a>**Sekundární dělení země**: rozdělení země nebo oblasti druhé úrovně, obvykle označované jako okres.

<a name="country-tertiary-subdivision"></a>Třetí **Pododdíl země**/oblasti: třetí úroveň rozdělení země nebo oblasti, obvykle pojmenované oblasti, jako je například.

<a name="cross-street"></a>**Příčná ulice**: bod, ve kterém se protínají dvě nebo více ulic.

<a name="cylindrical-projection"></a>**Válcová projekce**: projekce, která transformuje body z spheroid nebo koule na válcový nebo sekans. Válec se pak rozdělí shora dolů a sloučí se do roviny.

## <a name="d"></a>D

<a name="datum"></a>**Datum**: Referenční specifikace systému měření, systém souřadnicových poloh na povrchu (horizontální datum) nebo výšky nad nebo pod povrchem (vertikální datum).

<a name="dbf-file"></a>**Soubor DBF**: formát databázového souboru, který se používá v kombinaci s SHAPEFILE (SHP).

<a name="degree-minutes-seconds-dms"></a>Doba **minuty v sekundách (DMS)**: Měrná jednotka pro popis zeměpisné šířky a délky. Stupeň je 1/360<sup>th</sup> kružnice. Úroveň je dále rozdělena na 60 minut a minuta je rozdělena do 60 sekund.

<a name="delaunay-triangulation"></a>**Delaunay triangulace**: technika pro vytvoření mřížky souvislých a nepřekrývajících se trojúhelníků z datové sady bodů. Circumscribing kruh každého trojúhelníku neobsahuje žádné body z datové sady v jeho vnitřku.

<a name="demographics"></a>**Demografické údaje**: statistické charakteristiky (například věk, sazba narození a příjem) lidského obyvatelstva.

<a name="destination"></a>**Cíl**: koncový bod nebo umístění, do kterého má někdo na služební cestě.

<a name="digital-elevation-model-dem"></a>**Model digitálního zvýšení úrovně (DEM)**: datovou sadu hodnot zvýšení oprávnění souvisejících s povrchem zachyceným v oblasti v pravidelných intervalech pomocí společného pole datum. DEMs se obvykle používají k vyjádření osvobození od terénu.

<a name="dijkstra's-algorithm"></a>**Dijkstra algoritmus**: algoritmus, který prověřuje připojení k síti, aby bylo možné najít nejkratší cestu mezi dvěma body.

<a name="distance-matrix"></a>**Matice o vzdálenosti**: matice, která obsahuje informace o čase a vzdálenosti mezi sadou zdrojů a místy určení. 

## <a name="e"></a>E

<a name="elevation"></a>**Zvýšení oprávnění**: svislá vzdálenost bodu nebo objektu nad nebo pod odkazem na referenční plochu nebo datum. Obecně platí, že referenční plocha je střední úroveň moře. Zvýšení úrovně obecně odkazuje na vertikální výšku půdy.

<a name="envelope"></a>**Obálka**: viz [ohraničovací rámeček](#bounding-box).

<a name="extended-postal-code"></a>**Rozšířené poštovní** směrovací číslo: PSČ, který může obsahovat další informace. Například v USA mají kódy PSČ pět číslic. Ale rozšířené PSČ, označované jako zip + 4, obsahuje čtyři další číslice. Tyto další číslice slouží k identifikaci geografického segmentu v rámci oblasti pro doručování s pěti číslicemi, jako je například blok města, skupina poboček nebo poštovní kancelář. Znalost geografického segmentu v oblasti efektivního řazení a doručování pošty.

<a name="extent"></a>**Rozsah**: viz [ohraničovací rámeček](#bounding-box).

## <a name="f"></a>F

<a name="federated-authentication"></a>**Federované ověřování**: metoda ověřování umožňující použití jednotného mechanismu přihlášení a ověřování v několika webových a mobilních aplikacích. 

<a name="feature"></a>**Funkce**: objekt, který kombinuje geometrii s dalšími informacemi o metadatech. 

<a name="feature-collection"></a>**Kolekce funkcí**: kolekce objektů funkcí.

<a name="find-along-route"></a>**Hledání podél trasy**: prostorový dotaz, který hledá data, která jsou v rámci zadaného času nebo vzdálenosti v cestě trasy.

<a name="find-nearby"></a>**Najít okolní**: prostorový dotaz, který vyhledává pevnou vzdálenost rovnou řádku (jako rozvětvené rozvětvené) z bodu.

<a name="fleet-management"></a>**Řízení loďstva**: Správa komerčních vozidel, jako jsou automobily, automobily, lodě a roviny. Správa loďstva může zahrnovat řadu funkcí, jako je financování vozidel, údržba, telematika (sledování a Diagnostika) a také ovladače, rychlost, palivo a Správa stavu a zabezpečení. Správa loďstva je proces používaný společnostmi, které se spoléhají na přepravu ve své firmě. Společnosti chtějí minimalizovat rizika a snížit celkové náklady na dopravu a pracovníky a přitom zajistit soulad s právními předpisy pro státní správu.

<a name="free-flow-speed"></a>**Rychlost toku zdarma**: za ideálních podmínek se očekává rychlost bezplatného toku. Obvykle je to omezení rychlosti.

<a name="free-form-address"></a>**Adresa volného formátu**: úplná adresa, která je vyjádřena jako jeden řádek textu.

<a name="fuzzy-search"></a>**Přibližné vyhledávání**: hledání, které odpovídá prázdnému řetězci textu, který může být adresa nebo bod zájmu. 

## <a name="g"></a>G

<a name="geocode"></a>Zeměpisná **poloha: adresa** nebo umístění, které se převedlo na souřadnici, která se dá použít k zobrazení tohoto umístění na mapě. 

<a name="geocoding"></a>**Geografické kódování**: označuje se také jako dopředné geografické kódování, je proces převodu adresy dat umístění na souřadnice. 

<a name="geodesic-path"></a>**Geodesic cesta**: nejkratší cesta mezi dvěma body na zakřivené ploše. Při vykreslování v Azure Maps tato cesta se zobrazuje jako Zakřivená čára z důvodu projekce Mercator.

<a name="geofence"></a>Geografické **plot**: definovaná geografická oblast, která se dá použít k aktivaci událostí, když zařízení zadá nebo doplní oblast.

<a name="geojson"></a>Geografické **JSON**: Jedná se o společný formát souborů založený na formátu JSON, který slouží k ukládání geografických vektorových dat, jako jsou body, čáry a mnohoúhelníky. **Poznámka**: Azure Maps používá rozšířenou verzi služby injson, jak je [uvedeno zde](extend-geojson.md).

<a name="geometry"></a>**Geometry**: představuje prostorový objekt, jako je například Point, line nebo mnohoúhelník.

<a name="geometrycollection"></a>**GeometryCollection**: kolekce objektů geometrie

<a name="geopol"></a>**GeoPol**: odkazuje na geopoliticky citlivá data, jako jsou sporné hranice a názvy.

<a name="georeference"></a>Geografické **reference**: proces zarovnání geografických dat nebo souborů dat do známého systému souřadnic. Tento proces se může skládat z posunu, otáčení, škálování nebo zkosení dat.

<a name="georss"></a>**GeoRSS**: rozšíření XML pro přidání prostorových dat do kanálů RSS.

<a name="gis"></a>**GIS**: zkratka pro "geograficky Information System". Běžný termín, který se používá k popisu odvětví mapování.

<a name="gml"></a>**GML**: označuje se také jako geografické označení jazyka. Přípona souboru XML pro ukládání prostorových dat.

<a name="gps"></a>**GPS**: taky označované jako Global Positioning System je systém satelitních míst, který se používá k určení polohy zařízení na zemi. Satelity v rámci přenáší signály, které umožní přijímači GPS kdekoli na zemi, aby vypočítala své místo přes trilateration.

<a name="gpx"></a>**GPX**: také známý jako formát GPS Exchange je formát souboru XML obvykle vytvořený ze zařízení GPS.  

<a name="great-circle-distance"></a>**Skvělé – vzdálenost**: nejkratší vzdálenost mezi dvěma body na povrchu koule.

<a name="greenwich-mean-time-gmt"></a>**Greenwichský střední čas (GMT)**: čas v primárním poledníku, který se spouští přes královská Observatory v Greenwiche, Anglie.

<a name="guid"></a>**GUID**: globálně jedinečný identifikátor. Řetězec, který slouží k jednoznačné identifikaci rozhraní, třídy, knihovny typů, kategorie součásti nebo záznamu.

## <a name="h"></a>H

<a name="haversine-formula"></a>**Vzorec Haversine**: běžná rovnice použitá pro výpočet vzdálenosti skvělého kruhu mezi dvěma body v oblasti.

<a name="hd-maps"></a>**Mapy HD**: taky označované jako mapy s vysokým rozlišením, obsahují informace o silniční síti s vysokou věrností, jako jsou označení dráhy, znaménka a směrové svítilny vyžadované pro autonomní řízení.

<a name="heading"></a>**Nadpis**: směr, na který něco odkazuje, nebo na protější. Viz také [.](#heading)

<a name="heatmap"></a>**Heatmapu**: vizualizace dat, ve které rozsah barev představuje hustotu bodů v určité oblasti. Viz také tematická mapa.

<a name="hybrid-imagery"></a>**Hybridních snímků**: satelitní nebo mikrosnímků, které obsahují data a popisky, které jsou na ní překryté.

## <a name="i"></a>I

<a name="iana"></a>**IANA**: zkratka pro autoritu s přiřazenými čísly pro Internet. Nezisková skupina, která se přehlíží na globální přidělování IP adres.

<a name="isochrone"></a>**Isochrone**: Isochrone definuje oblast, ve které se může někdo v zadaném čase přenášet na určitý směr přepravy v libovolném směru z daného umístění. Viz také [dosažitelný rozsah](#reachable-range).

<a name="isodistance"></a>**Isodistance**: isochrone definuje oblast, ve které může někdo cestovat v zadané vzdálenosti pro určitý směr přepravy. Viz také [dosažitelný rozsah](#reachable-range).

## <a name="k"></a>K

<a name="kml"></a>**KML**: označuje se také jako jazyk s označením díry, je běžný formát souboru XML pro ukládání geografických vektorových dat, jako jsou body, čáry a mnohoúhelníky. 

## <a name="l"></a>L

<a name="landsat"></a>**Landsat**: družicové a uzemněné satelity, které vyvinula NASA, které shromažďují na pozemky. Tento obrázek se používá v mnoha odvětvích, jako jsou zemědělství, lesnictví a cartography.

<a name="latitude"></a>**Zeměpisná šířka**: úhlová vzdálenost měřená ve stupních od nárazu v severním nebo jižním směru.

<a name="level-of-detail"></a>**Úroveň podrobností**: viz úroveň přiblížení.

<a name="lidar"></a>**LIDAR**: zkratka pro světlou detekci a rozsah. Technika vzdáleného průzkumu, která používá Lasers k měření vzdálenosti na reflektované plochy.

<a name="linear-interpolation"></a>**Lineární interpolace**: odhad neznámé hodnoty pomocí lineární vzdálenosti mezi známými hodnotami.

<a name="linestring"></a>**LineString**: geometrie, která se používá k reprezentaci řádku. Označuje se také jako lomená čáry. 

<a name="localization"></a>**Lokalizace**: Podpora různých jazyků a kultur.

<a name="logistics"></a>**Logistika**: proces přesunu lidí, vozidel, dodávek nebo prostředků koordinovaným způsobem.

<a name="longitude"></a>**Zeměpisná délka**: úhlová vzdálenost měřená ve stupních od primárního poledníku po východním nebo západním směru.

## <a name="m"></a>M

<a name="map-tile"></a>**Dlaždice mapy**: obdélníkový obrázek, který představuje oddíl mapového plátna. Další informace naleznete v [dokumentaci úrovně přiblížení a mřížka dlaždic](zoom-levels-and-tile-grid.md).

<a name="marker"></a>**Značka**: označuje se také jako PIN nebo připínáček, což představuje ikonu umístění bodu na mapě.

<a name="mercator-projection"></a>**Projekce Mercator**: projekce válcového mapování, která se stala standardní projekcí mapy pro námořní účely z důvodu jeho schopnosti vyjádřit řádky konstantního kurzu, označované jako loxodromy čáry, jako přímé segmenty, které šetří úhly s poledníky. Všechny projekce ploché mapy deformují tvary nebo velikosti mapy při porovnání s skutečným rozložením povrchu země. Mercator promítání exaggerates oblastí daleko od sebe, aby se menší oblasti zobrazovaly na mapě větší, jako při přístupu k POLES. 

<a name="multilinestring"></a>**MultiLineString**: geometrie reprezentující kolekci objektů LineString. 

<a name="multipoint"></a>**MultiPoint**: geometrie, která představuje kolekci objektů Point.

<a name="multipolygon"></a>**Promnohoúhelník**: geometrie, která představuje kolekci objektů mnohoúhelníku. Například chcete-li zobrazit hranici Havajské ostrovy, každý ostrov by byl obrysem mnohoúhelníku. Proto by měla hranice Havajské ostrovy tedy být pro více mnohoúhelníků.

<a name="municipality"></a>**Obec**: město nebo město. 

<a name="municipality-subdivision"></a>**Dílčí dělení**: rozčlenění obce, jako je například okolní nebo místní název oblasti, například "centru".

## <a name="n"></a>N

<a name="navigation-bar"></a>**Navigační panel**: sada ovládacích prvků na mapě, která se používá pro úpravu úrovně přiblížení, sklonu, rotace a přepínání základní vrstvy mapy.

<a name="nearby-search"></a>**Blízké hledání**: prostorový dotaz, který vyhledává pevnou vzdálenost rovnou řádku (jako rozvětvené rozvětvené) z bodu.

<a name="neutral-ground-truth"></a>**Neutrální uzemnění**: mapa, která vykreslí popisky v oficiálním jazyce oblasti, který představuje, a v místních skriptech, pokud jsou k dispozici.

## <a name="o"></a>O

<a name="origin"></a>**Počátek**: počáteční bod nebo umístění, ve kterém je uživatel.

## <a name="p"></a>P

<a name="panning"></a>**Posouvání**: proces přesunutí mapy v jakémkoli směru a udržování stálé úrovně přiblížení.

<a name="parcel"></a>**Parcela**: vykreslení hranice půdy nebo vlastnosti.

<a name="pitch"></a>**Rozteč**: velikost náklonu mapy vzhledem ke svislému, kde 0 na mapě vypadá rovnou.

<a name="point"></a>**Point**: geometrie, která představuje jednu pozici na mapě. 

<a name="points-of-interest-poi"></a>**Body zájmu (POI)**: obchodní, orientační nebo běžné místo, které vás zajímají.

<a name="polygon"></a>**Mnohoúhelník**: plná geometrie, která představuje oblast na mapě. 

<a name="polyline"></a>**Lomená** čára: geometrie použitá pro reprezentaci řádku. Označuje se také jako LineString. 

<a name="position"></a>**Pozice**: Zeměpisná délka, zeměpisná šířka a výška (souřadnice x, y, z) bodu.

<a name="post-code"></a>**PSČ: viz** [PSČ](#postal-code).

<a name="postal-code"></a>**Poštovní směrovací číslo**: Řada písmen nebo číslic nebo obojí, v určitém formátu. PSČ používá poštovní služba země nebo oblasti k rozdělení geografických oblastí do zón, aby bylo možné zjednodušit doručování e-mailů.

<a name="primary-key"></a>**Primární klíč**: první ze dvou klíčů předplatného, které jsou k dispozici pro ověřování Azure Mapsho sdíleného klíče. Viz [ověřování sdíleného klíče](#shared-key-authentication).

<a name="prime-meridian"></a>**Prvotní poledníku**: čára délky, která představuje 0 – stupně zeměpisné délky. Obecně se hodnoty délky nezmenšují při cestování v západním směru až do 180 stupňů a zvyšují se při cestování v východním směrech do-180-stupňů. 

<a name="prj"></a>**PRJ**: textový soubor, který často provází soubor shapefile, který obsahuje informace o prodaném systému souřadnicového systému, ve kterém je datová sada.

<a name="projection"></a>**Projekce**: Projektový systém souřadnic založený na projekci mapy, jako je příčná Mercator, Albers EQUAL Area a Robinson. Ty poskytují možnost projektovat mapy kulového povrchu země na dvojrozměrné rovině kartézském souřadnice. Předpokládané systémy souřadnic se někdy označují jako projekce map.

## <a name="q"></a>Q

<a name="quadkey"></a>**Quadkey**: index adresy Base-4 pro dlaždici v systému quadtree dláždění. Další informace najdete v části [úrovně přiblížení a](zoom-levels-and-tile-grid.md) v dokumentaci k mřížce dlaždice pro další informace.

<a name="quadtree"></a>**Quadtree**: datová struktura, ve které má každý uzel přesně čtyři podřízené položky. Systém dlaždic používaný v Azure Maps používá strukturu quadtree, například když uživatel přiblíží jednu úroveň, každá dlaždice mapy se rozdělí do čtyř dlaždic.  Další informace najdete v části [úrovně přiblížení a](zoom-levels-and-tile-grid.md) v dokumentaci k mřížce dlaždice pro další informace.

<a name="queries-per-second-qps"></a>**Dotazy za sekundu (QPS)**: počet dotazů nebo požadavků, které lze provést na službu nebo platformu během jedné sekundy. 

## <a name="r"></a>R

<a name="radial-search"></a>**Paprskové vyhledávání**: prostorový dotaz, který vyhledává pevnou vzdálenost rovnou řádku (jako rozvětvené rozvětvené) z bodu. 

<a name="raster-data"></a>**Rastrová data**: matice buněk (nebo pixelů) uspořádaná do řádků a sloupců (neboli mřížky), kde každá buňka obsahuje hodnotu, která představuje informace, jako je například teplota. Rastrový obrázek zahrnuje digitální antény, snímky z satelitních, digitálních obrázků a naskenovaných map.

<a name="raster-layer"></a>**Rastrová vrstva**: vrstva dlaždice, která se skládá z rastrových obrázků.

<a name="reachable-range"></a>**Dosažitelný rozsah**: dostupný rozsah definuje oblast, ve které se může někdo pohybovat v zadaném čase nebo na dálku, a to v jakémkoli směru přenosu na cestování. Viz také [Isochrone](#isochrone) a [Isodistance](#isodistance).

<a name="remote-sensing"></a>**Vzdálené průzkumy**: proces shromažďování a interpretace dat ze senzorů od určité vzdálenosti.

<a name="rest-service"></a>**Služba REST**: zkratka zůstane pro přenos po převádění stavu. Služba REST je webová služba založená na adrese URL, která spoléhá na základní webovou technologii ke komunikaci, nejběžnější metody, které požadavky HTTP GET a POST. Tyto typy služeb mají v úmyslu mnohem rychlejší a menší než tradiční služby založené na protokolu SOAP.

<a name="reverse-geocode"></a>**Reverse INCODE**: proces pořizování souřadnic a určení adresy, na které se nachází na mapě.

<a name="reproject"></a>**Reprojektovat**: viz [transformace](#transformation).

<a name="rest-service"></a>**Služba REST**: zkratka pro přenos stavu pro reprezentaci. Architektura pro výměnu informací mezi partnerskými uzly v decentralizovaném a distribuovaném prostředí. REST umožňuje programům na různých počítačích komunikovat nezávisle na operačním systému nebo platformě. Služba může poslat požadavek HTTP (Hypertext Transfer Protocol) na adresu URL (Uniform Resource Locator) a získat zpět data.

<a name="route"></a>**Route**: cesta mezi dvěma nebo více umístěními, která může obsahovat taky další informace, třeba pokyny pro Waypoints trasy.

<a name="requests-per-second-rps"></a>**Počet požadavků za sekundu (RPS)**: Další informace najdete v [dotazech za sekundu (QPS)](#queries-per-second-qps). 

<a name="rss"></a>**RSS**: zkratka pro skutečně jednoduchou syndikaci, souhrn lokality RDF (Resource Description Framework) nebo obsáhlý Souhrn webu v závislosti na zdroji. Jednoduchý strukturovaný formát XML pro sdílení obsahu mezi různými weby. Dokumenty RSS obsahují klíčové prvky metadat, jako je autor, datum, název, stručný popis a hypertextový odkaz. Tyto informace pomáhají uživateli (nebo službě vydavatelů informačních kanálů RSS) rozhodnout, jaké materiály jsou k dalšímu šetření.

## <a name="s"></a>S

<a name="satellite-imagery"></a>**Satelitních snímků**: s použitím snímků, které jsou zachycené rovinami a satelity ukazujícími dolů.

<a name="secondary-key"></a>**Sekundární klíč**: druhý ze dvou klíčů předplatného, které jsou k dispozici pro ověřování Azure Mapsho sdíleného klíče. Viz [ověřování sdíleného klíče](#shared-key-authentication).

<a name="shapefile-shp"></a>**Shapefile (SHP)**: označuje se také jako ESRI shapefile, je formát úložiště vektorových dat pro ukládání umístění, tvaru a atributů geografických funkcí. Shapefile je uložen v sadě souvisejících souborů.

<a name="shared-key-authentication"></a>**Ověřování pomocí sdíleného klíče**: ověřování pomocí sdíleného klíče spoléhá na předávání klíčů generovaných Azure Mapsm účtu s každým požadavkem Azure Maps. Tyto klíče se často označují jako klíče předplatného. Doporučuje se, aby se klíče pravidelně znovu vygenerovaly z důvodu zabezpečení. K dispozici jsou dva klíče, abyste mohli při opětovném generování druhé klávesy spravovat připojení pomocí jednoho klíče. Když znovu vygenerujete klíče, musíte aktualizovat všechny aplikace, které přistupují k tomuto účtu, aby používaly nové klíče. Další informace o ověřování Azure Maps najdete v tématech [Azure Maps a Azure AD](azure-maps-authentication.md) a [správa ověřování v Azure Maps](how-to-manage-authentication.md).

<a name="software-development-kit-sdk"></a>**Sada SDK (Software Development Kit)**: kolekce dokumentace, ukázkový kód a ukázkové aplikace, které vývojářům pomůžou při sestavování aplikací použít rozhraní API.

<a name="spherical-mercator-projection"></a>**Kulový Mercator projekce**: viz [Web Mercator](#web-mercator). 

<a name="spatial-query"></a>**Prostorový dotaz**: požadavek provedený na službu, která provádí prostorovou operaci. Například kruhové hledání nebo vyhledávání tras.

<a name="spatial-reference"></a>**Prostorová reference**: místní, regionální nebo globální systém založený na souřadnicích, který se používá k přesnému nalezení geografických entit. Definuje souřadnicový systém, který se používá k přidružení souřadnic mapy k umístěním v reálném světě. Prostorové odkazy zajišťují, že prostorová data z různých vrstev nebo zdrojů můžou být integrovaná pro přesné zobrazení nebo analýzu. Azure Maps používá referenční systém souřadnic [EPSG: 3857](https://epsg.io/3857) a WGS 84 pro vstupní data geometrie.

<a name="sql-spatial"></a>**Prostor SQL**: odkazuje na prostorové funkce integrované do SQL Azure a SQL Server 2008 a vyšší. Tato prostorová funkce je také k dispozici jako knihovna .NET, která se dá použít nezávisle na SQL Server. Další informace najdete v dokumentaci k [prostorovým datům (SQL Server)](/sql/relational-databases/spatial/spatial-data-sql-server) , kde najdete další informace.

<a name="subscription-key"></a>**Klíč předplatného**: viz [ověřování sdíleného klíče](#shared-key-authentication).

<a name="synchronous-request"></a>**Synchronní požadavek**: požadavek HTTP otevře připojení a počká na odpověď. Prohlížeče omezují počet souběžných požadavků HTTP, které lze vytvořit ze stránky. Je-li více dlouho spuštěných synchronních požadavků provedeno současně, je možné dosáhnout tohoto omezení. Žádosti budou zpožděny, dokud se jedna z ostatních požadavků nedokončí.

## <a name="t"></a>T

<a name="telematics"></a>**Telematické**: posílání, přijímání a ukládání informací prostřednictvím komunikačních zařízení spolu s tím, jak ovlivňuje řízení vzdálených objektů. 

<a name="temporal-data"></a>**Dočasná data**: data, která konkrétně odkazují na časy nebo kalendářní data. Dočasná data se můžou týkat diskrétních událostí, jako jsou přeškrtnutí v podobě blesku; Přesun objektů, jako jsou vlaky; nebo opakovaná pozorování, například počty ze senzorů provozu.

<a name="terrain"></a>**Terén**: oblast pozemku se specifickou charakteristikou, například oranžovohnědá terén nebo horského terénu.

<a name="thematic-maps"></a>**Tematické mapy**: tematická mapa je jednoduchá Mapa vytvořená tak, aby odrážela motiv geografické oblasti. Běžným scénářem tohoto typu mapy je vybarvit oblasti správy, jako jsou země nebo oblasti, na základě některé metriky dat.

<a name="tile-layer"></a>**Vrstva dlaždice**: vrstva zobrazená pomocí navýšení dlaždic mapy (pravoúhlé části) do souvislé vrstvy. Dlaždice jsou buď dlaždice rastrového obrázku, nebo vektorové dlaždice. Vrstvy rastrových dlaždic jsou obvykle vykresleny předem a jsou uloženy jako obrázky na serveru. Vrstvy rastrových dlaždic můžou používat velký prostor úložiště. Vrstvy vektorové dlaždice jsou vykreslovány téměř v reálném čase v rámci klientské aplikace. Proto jsou požadavky na úložiště na straně serveru pro vrstvy vektorové dlaždice menší.

<a name="time-zone"></a>**Časové pásmo**: oblast zeměkoule, která sleduje jednotnou standardní dobu pro právní, komerční a sociální účely. Časová pásma mají za následek sledovat hranice zemí nebo oblastí a jejich dílčí dělení.

<a name="transaction"></a>**Transakce**: Azure Maps používá model transakčních licencí, kde;

- Jedna transakce se vytvoří pro každých 15 požadavků na dlaždice mapy nebo přenosů.
- Jedna transakce se vytvoří pro každé volání rozhraní API k jedné ze služeb v Azure Maps. Příklady Azure Maps služby jsou hledání a směrování.

<a name="transformation"></a>**Transformace**: proces převodu dat mezi různými geografickými systémy souřadnic. Můžete například mít data zaznamenaná ve Spojeném království a na základě geografického systému souřadnic OSGB 1936. Azure Maps používá souřadnici [EPSG: 3857](https://epsg.io/3857) referenční systém variant WGS84. Vzhledem k tomu, že se data zobrazují správně, bude nutné, aby jejich souřadnice byly transformované z jednoho systému do jiného.

<a name="traveling-salesmen-problem-tsp"></a>**Salesmen problém na cestách (TSP)**: problém okruhu Hamiltonian, ve kterém prodejce musí najít nejúčinnější způsob, jak navštívit řadu zastavení, a pak se vrátit do počátečního umístění.  

<a name="trilateration"></a>**Trilateration**: proces stanovení pozice bodu na povrchu země, s ohledem na dva další body měřením vzdálenosti mezi všemi třemi body.

<a name="turn-by-turn-navigation"></a>**Zapnutí navigace**: aplikace, která poskytuje pokyny k směrování pro každý krok trasy, se uživatelům blíží následující manévrů.

## <a name="v"></a>V

<a name="vector-data"></a>**Vektorová data**: data založená na souřadnicích, která jsou reprezentována jako body, čáry nebo mnohoúhelníky.

<a name="vector-tile"></a>**Vector dlaždice**: otevřená specifikace dat pro ukládání geoprostorových vektorových dat pomocí stejného systému dlaždic jako mapového ovládacího prvku. Viz také [Dlaždicová vrstva](#tile-layer).

<a name="vehicle-routing-problem-vrp"></a>**Problém s směrováním na vozidlo (VRP)**: třída problémů, při které se počítá sada seřazených tras pro loďstva vozidel, přičemž se bere v úvahu sada omezení. Tato omezení můžou zahrnovat časová období doručení, několik kapacit tras a omezení doby trvání cesty.

<a name="voronoi-diagram"></a>**Voronoi diagram**: oddíl prostoru v oblastech nebo buňkách, které obklopují sadu geometrických objektů, obvykle funkce Point. Tyto buňky nebo mnohoúhelníky musí splňovat kritéria pro Delaunay trojúhelníky. Všechna místa v oblasti jsou blíž k objektu, který obklopuje, než na jiný objekt v sadě. Diagramy Voronoi se často používají k vymezení oblastí ovlivňujících geografické funkce. 

## <a name="w"></a>W

<a name="waypoint"></a>**Bod na trase**: bod na trase je zadané zeměpisné umístění definované zeměpisnou délkou a zeměpisnou šířkou, které se používá pro navigační účely. Často se používá k reprezentaci bodu, ve kterém někdo prochází trasu.

<a name="waypoint-optimization"></a>**Optimalizace bod na trase**: proces přeuspořádání sady Waypoints pro minimalizaci doby trvání cesty nebo vzdálenosti, která je potřeba k předávání všech poskytovaných Waypoints. V závislosti na složitosti optimalizace se tato optimalizace často označuje jako problém [Salesmen na cestování](#traveling-salesmen-problem-tsp) nebo [problémy s směrováním na vozidlo](#vehicle-routing-problem-vrp).

<a name="web-map-service-wms"></a>**Služba webového mapování (WMS)**: WMS je OGC (Open geografický Consortium), který definuje mapové služby založené na bitových kopiích. Služba WMS nabízí mapy obrázků pro konkrétní oblasti v rámci mapy na vyžádání. Obrázky zahrnují předem vykreslené Symbology a mohou být vykresleny v jednom z několika pojmenovaných stylů, pokud jsou definovány službou.

<a name="web-mercator"></a>**Web Mercator**: označuje se také jako kulové Mercator projekce. Jedná se o mírnou variantu projekce Mercator, která se používá hlavně ve webových aplikacích pro mapování. Používá stejné vzorce jako standardní projekce Mercator, která se používá pro mapy malého rozsahu. Web Mercator ale používá pro všechna měřítka kulové vzorce, ale mapy Mercator ve velkém měřítku obvykle používají ellipsoidal formu projekce. Nesoulad je nepravdivý v globálním měřítku, ale způsobuje, že mapy místních oblastí se mírně odchylují od skutečných ellipsoidal Mercator map ve stejném měřítku.

<a name="wgs84"></a>**WGS84**: sada konstant sloužící k propojení prostorových souřadnic do umístění na povrchu mapy. WGS84 datum je standardní verze, kterou používá většina poskytovatelů online mapování a zařízení GPS. Azure Maps používá souřadnici [EPSG: 3857](https://epsg.io/3857) referenční systém variant WGS84.

## <a name="z"></a>Z

<a name="z-coordinate"></a>**Souřadnice Z**: viz [nadmořská](#altitude)plocha. 

<a name="zip-code"></a>**PSČ**: viz [poštovní směrovací](#postal-code)číslo.

<a name="Zoom level"></a>**Úroveň přiblížení**: Určuje úroveň podrobností a velikost viditelného rozvržení. Při přiblížení všech možností na úroveň 0 bude celá světová mapa často viditelná. Mapa ale zobrazí omezené podrobnosti, jako jsou názvy zemí nebo oblastí, ohraničení a názvy oceánů. Při přiblížení do úrovně 17 se v mapě zobrazí oblast několika bloků města s podrobnými informacemi o cestách. V Azure Maps je nejvyšší úroveň přiblížení 22. Další informace naleznete v dokumentaci [úrovně přiblížení a mřížka dlaždic](zoom-levels-and-tile-grid.md) .