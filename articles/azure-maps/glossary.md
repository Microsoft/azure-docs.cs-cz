---
title: Glosář map Azure | Dokumenty společnosti Microsoft
description: Glosář běžně používaných termínů přidružených k Mapám Azure, službám založeným na poloze a GIS.
author: rbrundritt
ms.author: richbrun
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: c9d5c2efc14dec5beee0eaf81a4f628421ef3d49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657040"
---
# <a name="glossary"></a>Slovníček

Následující seznam popisuje běžná slova používaná ve službách Azure Maps.

## <a name="a"></a>A

<a name="address-validation"></a>**Ověření adresy**: Proces ověření existence adresy.

<a name="advanced-routing"></a>**Rozšířené směrování**: Kolekce služeb, které provádějí předběžné operace pomocí dat silničního směrování; například výpočet dosažitelných rozsahů (izochronů), matric vzdálenosti a požadavků na dávkové trasy.

<a name="aerial-imagery"></a>**Letecké snímky**: Viz [Satelitní snímky](#satellite-imagery). 

<a name="along-a-route-search"></a>**Vyhledávání na trase**: Prostorový dotaz, který vyhledádata v určeném čase objížďky nebo vzdálenosti od trasy.

<a name="altitude"></a>**Nadmořská výška**: Výška nebo svislá výška bodu nad referenčním povrchem. Měření nadmořské výšky jsou založena na daném referenčním údaji, jako je průměrná hladina moře. Viz také nadmořská výška.

<a name="ambiguous"></a>**Nejednoznačný**: Stav nejistoty v klasifikaci dat, který existuje, když může být objektu pro daný atribut odpovídajícím způsobem přiřazeny dvě nebo více hodnot. Například při geokódování "CA", jsou vráceny dva nejednoznačné výsledky: "Kanada" a "Kalifornie". "CA" je země a státní kód pro "Kanada" a "Kalifornie". 

<a name="annotation"></a>**Anotace**: Text nebo grafika zobrazená na mapě za účelem poskytnutí informací uživateli. Anotace může identifikovat nebo popsat konkrétní entitu mapy, poskytnout obecné informace o oblasti na mapě nebo poskytnout informace o samotné mapě.

<a name="antimeridian"></a>**Antimeridian**: Také známý jako 180<sup>th</sup> Meridian. To je bod, kde se setkávají -180 stupňů a 180 stupňů. Což je opak hlavního poledníku na světě.

<a name="application-programming-interface-api"></a>**Aplikační programovací rozhraní (API)**: Specifikace, která umožňuje vývojářům vytvářet aplikace.

<a name="api-key"></a>**Klíč rozhraní API**: Viz [Ověřování sdíleného klíče](#shared-key-authentication).

<a name="area-of-interest-aoi"></a>**Oblast zájmu (AOI)**: Rozsah použitý k definování zaostřovací oblasti pro mapu nebo databázovou produkci.

<a name="asset-tracking"></a>**Sledování majetku**: Proces sledování polohy majetku, například osoby, vozidla nebo jiného objektu.

<a name="asynchronous-request"></a>**Asynchronní požadavek**: Požadavek HTTP, který otevře připojení a provede požadavek na server, který vrátí identifikátor pro asynchronní požadavek, pak ukončí připojení. Server pokračuje ve zpracování požadavku a uživatel může zkontrolovat stav pomocí identifikátoru. Po dokončení zpracování požadavku může uživatel stáhnout odpověď. Tento typ požadavku se používá pro dlouhotrvající procesy.

<a name="autocomplete"></a>**Automatické dokončování**: Funkce v aplikaci, která předpovídá zbytek slova, které uživatel zadává. 

<a name="autosuggest"></a>**Autosuggest**: Funkce v aplikaci, která předpovídá logické možnosti pro to, co uživatel zadává.

<a name="azure-location-based-services-lbs"></a>**Služby Azure založené na poloze (LBS):** Dřívější název Azure Maps, když byl ve verzi preview.

<a name="azure-active-directory"></a>**Azure Active Directory (Azure AD):** Azure AD je cloudová služba správy identit a přístupu microsoftu. Integrace Azure Maps Azure AD je momentálně dostupná ve verzi Preview pro všechna rozhraní API Azure Maps. Azure AD podporuje řízení přístupu na základě rolí (RBAC) povolit jemně odstupňovaný přístup k prostředkům Azure Maps. Další informace o integraci Azure Maps Azure AD najdete v [tématu Azure Maps a Azure AD](azure-maps-authentication.md) a [Správa ověřování v Azure Maps](how-to-manage-authentication.md).

<a name="azure-maps-key"></a>**Klíč Mapy Azure:** Viz [Ověřování pomocí sdíleného klíče](#shared-key-authentication).

## <a name="b"></a>B

<a name="base-map"></a>**Základní mapa**: Část mapové aplikace, která zobrazuje referenční informace na pozadí, jako jsou silnice, orientační body a politické hranice.

<a name="batch-request"></a>**Dávkový požadavek**: Proces kombinování více požadavků do jednoho požadavku.

<a name="bearing"></a>**Ložisko**: Vodorovný směr bodu vzhledem k jinému bodu. To je vyjádřeno jako úhel vzhledem k severu, od 0 stupňů do 360 stupňů ve směru hodinových ručiček. 

<a name="boundary"></a>**Hranice**: Čára nebo mnohostranný objekt oddělující sousední politické entity, například země nebo oblasti, okresy a vlastnosti. Hranice je čára, která může nebo nemusí sledovat fyzické rysy, jako jsou řeky, hory nebo stěny.

<a name="bounds"></a>**Hranice**: Viz [Ohraničovací rámeček](#bounding-box).

<a name="bounding-box"></a>**Ohraničovací rámeček**: Sada souřadnic používaných k reprezentaci obdélníkové oblasti na mapě. 

## <a name="c"></a>C

<a name="cadastre"></a>**Katastr nemovitostí**: Evidence zapsaných pozemků a nemovitostí. Viz také [Parcela](#parcel).

<a name="camera"></a>**Kamera**: V kontextu interaktivního ovládání mapy definuje kamera zorné pole map. Výřez kamery je určen na základě několika parametrů mapy: střed, úroveň přiblížení, rozteč, směr. 

<a name="centroid"></a>**Centroid**: Geometrický střed prvku. Středčáry by byl středem, zatímco centroid polygonu by byl jeho středem oblasti.

<a name="choropleth-map"></a>**Choropleth mapa**: Tematická mapa, ve které jsou oblasti stínovány v poměru k měření statistické proměnné. Tato statistická proměnná je zobrazena na mapě. Například vybarvení hranice každého amerického státu na základě jeho relativního počtu obyvatel do všech ostatních států.

<a name="concave-hull"></a>**Konkávní korba**: Obrazec, který představuje možnou konkávní geometrii, která uzavře všechny tvary do zadané datové sady. Generovaný tvar je podobný balení dat plastovým obalem a poté je zahřívá, což způsobuje, že se velké rozpětí mezi body prosakuje směrem k jiným datovým bodům.

<a name="consumption-model"></a>**Model spotřeby**: Informace, které definují rychlost, s jakou vozidlo spotřebovává palivo nebo elektřinu. Viz také [dokumentace modelu spotřeby](consumption-model.md).

<a name="control"></a>**Ovládání**: Samostatná nebo opakovaně použitelná součást skládající se z grafického uživatelského rozhraní, které definuje sadu chování pro rozhraní. Například ovládací prvek mapy je obecně část uživatelského rozhraní, která načte interaktivní mapu.

<a name="convex-hull"></a>**Konvexní trup**: Konvexní trup je tvar, který představuje minimální konvexní geometrii, která obklopuje všechny tvary v zadané datové sadě. Generovaný tvar je podobný obtékání elastického pásku kolem datové sady.

<a name="coordinate"></a>**Souřadnice**: Skládá se z hodnot zeměpisné délky a šířky použitých k reprezentaci umístění na mapě.

<a name="coordinate-system"></a>**Souřadný systém**: Referenční rámec používaný k definování poloh bodů v prostoru ve dvou nebo třech rozměrech.

<a name="country-code"></a>**Kód země**: Jedinečný identifikátor země/oblasti založený na normě ISO. ISO2 je dvouznakový kód pro zemi (například USA), který ISO3 představuje tříznakový kód (například USA).

<a name="country-subdivision"></a>**Pododdíl země**: První úroveň pododdílu země nebo oblasti, obecně známé jako stát nebo provincie.

<a name="country-secondary-subdivision"></a>**Sekundární dílčí členění země**: Druhé pododdělení země nebo oblasti, obecně známé jako okres.

<a name="country-tertiary-subdivision"></a>**Terciární členění země**: Členění třetí úrovně země nebo oblasti, obvykle pojmenovaná oblast, například oddělení.

<a name="cross-street"></a>**Cross Street**: Bod, kde se protínají dvě nebo více ulic.

<a name="cylindrical-projection"></a>**Válcová projekce**: Projekce, která transformuje body ze sféroidu nebo koule na tekutový nebo secantový válec. Válec je pak nakrájen shora dolů a zploštělý do roviny.

## <a name="d"></a>D

<a name="datum"></a>**Datum**: Referenční specifikace měřicího systému, systém souřadnicových poloh na povrchu (vodorovné vztažné zařízení) nebo výšky nad nebo pod povrchem (svislé vztažné).

<a name="dbf-file"></a>**DBF soubor**: Formát souboru databáze, který se používá v kombinaci s Shapefiles (SHP).

<a name="degree-minutes-seconds-dms"></a>**Stupeň minut sekund (DMS)**: Měrná jednotka pro popis zeměpisné šířky a délky. Stupeň je 1/360<sup>th</sup> kruhu. Stupeň je dále rozdělen na 60 minut a minuta je rozdělena na 60 sekund.

<a name="delaunay-triangulation"></a>**Delaunaytriangulace**: Technika pro vytvoření sítě souvislé, nepřekrývající se trojúhelníky z datové sady bodů. Každý trojúhelník je kruh opsané neobsahuje žádné body z datové sady v jeho interiéru.

<a name="demographics"></a>Demografické údaje : Statistické **charakteristiky**(jako je věk, porodnost a příjem) lidské populace.

<a name="destination"></a>**Cíl**: Koncový bod nebo místo, do kterého někdo cestuje.

<a name="digital-elevation-model-dem"></a>**Digitální výškový model (DEM):** Datová sada hodnot nadmořské výšky souvisejících s povrchem zachycených v oblasti v pravidelných intervalech pomocí společného základny. DEM symse se obvykle používají k reprezentaci terénního reliéfu.

<a name="dijkstra's-algorithm"></a>**Dijkstrův algoritmus**: Algoritmus, který zkoumá připojení sítě, aby našel nejkratší cestu mezi dvěma body.

<a name="distance-matrix"></a>**Matice vzdálenosti**: Matice, která obsahuje informace o době jízdy a vzdálenosti mezi sadou počátků a cílů. 

## <a name="e"></a>E

<a name="elevation"></a>**Výška**: Svislá vzdálenost bodu nebo objektu nad nebo pod referenčním povrchem nebo základnou. Obecně platí, že referenční povrch je střední hladina moře. Nadmořská výška obecně odkazuje na svislou výšku země.

<a name="envelope"></a>**Obálka**: Viz [Ohraničovací rámeček](#bounding-box).

<a name="extended-postal-code"></a>**Rozšířené PSČ**: PSČ, které může obsahovat další informace. Například v USA mají PSČ pět číslic. Rozšířené PSČ, známé jako zip+4, však obsahuje čtyři další číslice. Tyto další číslice se používají k identifikaci geografického segmentu v rámci pětimístné oblasti doručení, jako je městský blok, skupina bytů nebo poštovní schránka. Znalost geografického segmentu pomáhá při efektivním třídění a doručování pošty.

<a name="extent"></a>**Rozsah**: Viz [ohraničovací rámeček](#bounding-box).

## <a name="f"></a>F

<a name="federated-authentication"></a>**Federované ověřování:** Metoda ověřování, která umožňuje použití jednoho mechanismu přihlášení/ověřování ve více webových a mobilních aplikacích. 

<a name="feature"></a>**Funkce**: Objekt, který kombinuje geometrii s dalšími informacemi o metadatech. 

<a name="feature-collection"></a>**Kolekce funkcí**: Kolekce objektů funkcí.

<a name="find-along-route"></a>**Najít na trase**: Prostorový dotaz, který vyhledá data, která jsou v zadaném čase objížďky nebo vzdálenosti od trasy.

<a name="find-nearby"></a>**Najít v okolí**: Prostorový dotaz, který prohledává pevnou přímou vzdálenost (jako vzdušnou čarou) od bodu.

<a name="fleet-management"></a>**Správa vozového parku**: Správa užitkových vozidel, jako jsou osobní automobily, nákladní automobily, lodě a letadla. Správa vozového parku může zahrnovat celou řadu funkcí, jako je financování vozidel, údržba, telematika (sledování a diagnostika), jakož i řízení řidičů, rychlosti, paliva a řízení bezpečnosti a ochrany zdraví. Správa vozového parku je proces používaný společnostmi, které se spoléhají na dopravu ve svém podnikání. Společnosti chtějí minimalizovat rizika a snížit své celkové náklady na dopravu a zaměstnance a zároveň zajistit soulad s vládními právními předpisy.

<a name="free-flow-speed"></a>**Rychlost volného průtoku**: Otáčky volného průtoku očekávané za ideálních podmínek. Obvykle je to rychlostní limit.

<a name="free-form-address"></a>**Adresa volného tvaru**: Úplná adresa, která je reprezentována jako jeden řádek textu.

<a name="fuzzy-search"></a>**Přibližné vyhledávání**: Hledání, které přijímá řetězec textu ve volné formě, který může být adresou nebo bodem zájmu. 

## <a name="g"></a>G

<a name="geocode"></a>**Geokód**: Adresa nebo umístění, které bylo převedeno na souřadnici, kterou lze použít k zobrazení tohoto umístění na mapě. 

<a name="geocoding"></a>**Geocoding**: Také známý jako forward geocoding, je proces převodu adresy lokalizačních dat na souřadnice. 

<a name="geodesic-path"></a>**Geodetická cesta**: Nejkratší cesta mezi dvěma body na zakřiveném povrchu. Při vykreslení v Azure Maps se tato cesta zobrazí jako zakřivená čára kvůli projekci Mercator.

<a name="geofence"></a>**Geofence**: Definovaná zeměpisná oblast, kterou lze použít k aktivaci událostí, když zařízení vstoupí do oblasti nebo ji existuje.

<a name="geojson"></a>**GeoJSON**: Je běžný formát souboru založený na JSON, který se používá pro ukládání geografických vektorových dat, jako jsou body, čáry a polygony. **Poznámka:** Azure Maps používá rozšířenou verzi GeoJSON, jak [je zde zdokumentováno](extend-geojson.md).

<a name="geometry"></a>**Geometrie**: Představuje prostorový objekt, například bod, čáru nebo polygon.

<a name="geometrycollection"></a>**GeometryCollection**: Kolekce objektů geometrie.

<a name="geopol"></a>**GeoPol**: Odkazuje na geopoliticky citlivá data, jako jsou sporné hranice a názvy míst.

<a name="georeference"></a>**Georeference**: Proces zarovnání geografických dat nebo snímků se známým souřadnicovým systémem. Tento proces může spočívat v posunu, otočení, změně velikosti nebo zkosení dat.

<a name="georss"></a>**GeoRSS**: Rozšíření XML pro přidávání prostorových dat do informačních kanálů RSS.

<a name="gis"></a>**GIS**: Zkratka pro "Geografický informační systém". Běžný termín používaný k popisu odvětví mapování.

<a name="gml"></a>**GML**: Také známý jako zeměpis značkovací jazyk. Přípona souboru XML pro ukládání prostorových dat.

<a name="gps"></a>**GPS**: Také známý jako globální polohovací systém, je systém satelitů používaných pro určení polohy zařízení na Zemi. Obíhající satelity přenášejí signály, které umožňují přijímači GPS kdekoli na Zemi vypočítat svou vlastní polohu prostřednictvím trilaterace.

<a name="gpx"></a>**GPX**: Také známý jako GPS eXchange formát, je formát XML souboru běžně vytvořené z GPS zařízení.  

<a name="great-circle-distance"></a>**Vzdálenost velkého kruhu**: Nejkratší vzdálenost mezi dvěma body na povrchu koule.

<a name="greenwich-mean-time-gmt"></a>**Greenwichský střední čas (GMT)**: Čas na hlavním poledníku, který prochází Královskou observatoří v anglickém Greenwichi.

<a name="guid"></a>**GUID**: Globálně jedinečný identifikátor. Řetězec používaný k jednoznačné identifikaci rozhraní, třídy, knihovny typů, kategorie komponent nebo záznamu.

## <a name="h"></a>H

<a name="haversine-formula"></a>**Haversineho vzorec**: Společná rovnice používaná pro výpočet vzdálenosti velkého kruhu mezi dvěma body na kouli.

<a name="hd-maps"></a>**HD mapy**: Také známý jako High Definition Maps, se skládá z vysoce věrných informací o silniční síti, jako je značení jízdních pruhů, značení a směrová světla potřebná pro autonomní jízdu.

<a name="heading"></a>**Nadpis**: Směr, kterým něco směřuje nebo směřuje. Viz také [Ložisko](#heading).

<a name="heatmap"></a>**Heatmap**: Vizualizace dat, ve které rozsah barev představuje hustotu bodů v určité oblasti. Viz také Tematická mapa.

<a name="hybrid-imagery"></a>**Hybridní snímky**: Satelitní nebo letecké snímky, na kterých jsou překryty údaje o silnici a štítky.

## <a name="i"></a>I

<a name="iana"></a>**IANA**: Zkratka pro Internet Přidělená čísla úřadu. Nezisková skupina, která dohlíží na globální přidělování IP adres.

<a name="isochrone"></a>**Isochrone**: Isochrone definuje oblast, ve které může někdo cestovat v určeném čase pro způsob dopravy v libovolném směru z daného místa. Viz také [Dosažitelný rozsah](#reachable-range).

<a name="isodistance"></a>**Isodistance**: Isochrone, dané místo, definuje oblast, ve které může někdo cestovat v určité vzdálenosti pro způsob dopravy v libovolném směru. Viz také [Dosažitelný rozsah](#reachable-range).

## <a name="k"></a>K

<a name="kml"></a>**KML**: Také známý jako Keyhole Markup Language, je společný formát souboru XML pro ukládání geografických vektorových dat, jako jsou body, čáry a polygony. 

## <a name="l"></a>L

<a name="landsat"></a>**Landsat**: Multispectral, Země-obíhající satelity vyvinuté NASA, které shromažďují snímky země. Tyto snímky se používají v mnoha průmyslových odvětvích, jako je zemědělství, lesnictví a kartografie.

<a name="latitude"></a>**Zeměpisná šířka**: Úhlová vzdálenost měřená ve stupních od rovníku v severním nebo jižním směru.

<a name="level-of-detail"></a>**Úroveň podrobností**: Viz Úroveň přiblížení.

<a name="lidar"></a>**Lidar**: Zkratka pro detekci světla a rozsah. Technika dálkového snímání, která používá lasery k měření vzdáleností k reflexním povrchům.

<a name="linear-interpolation"></a>**Lineární interpolace**: Odhad neznámé hodnoty s použitím lineární vzdálenosti mezi známými hodnotami.

<a name="linestring"></a>**LineString**: Geometrie používaná k reprezentaci čáry. Také známý jako polyna. 

<a name="localization"></a>**Lokalizace**: Podpora pro různé jazyky a jazykové verze.

<a name="logistics"></a>**Logistika**: Proces koordinovaného přesunu osob, vozidel, dodávek nebo majetku.

<a name="longitude"></a>**Zeměpisná šířka**: Úhlová vzdálenost měřená ve stupních od hlavního poledníku ve východním nebo západním směru.

## <a name="m"></a>M

<a name="map-tile"></a>**Dlaždice mapy**: Obdélníkový obraz, který představuje oddíl plátna mapy. Další informace naleznete v [dokumentaci k úrovním lupy a k dlaždicové mřížce](zoom-levels-and-tile-grid.md).

<a name="marker"></a>**Značka**: Také známý jako pin nebo pinpin, je ikona, která představuje bod umístění na mapě.

<a name="mercator-projection"></a>**Mercatorova projekce**: Válcová projekce mapy, která se stala standardní mapovou projekcí pro námořní účely díky své schopnosti reprezentovat čáry konstantního průběhu, známé jako loxodinové čáry, jako přímé segmenty, které zachovávají úhly s meridiány. Všechny ploché mapové projekce deformují tvary nebo velikosti mapy ve srovnání se skutečným rozložením zemského povrchu. Mercatorova projekce zveličuje oblasti daleko od rovníku, takže menší plochy se na mapě objevují větší, když se blížíte k pólům. 

<a name="multilinestring"></a>**MultiLineString**: Geometrie, která představuje kolekci linestringových objektů. 

<a name="multipoint"></a>**MultiPoint**: Geometrie, která představuje kolekci Point objekty.

<a name="multipolygon"></a>**MultiPolygon**: Geometrie, která představuje kolekci mnohonožkových objektů. Chcete-li například zobrazit hranice Havaje, každý ostrov by byl nastíněn polygem. Hranice Havaje by tak byla MultiPolygon.

<a name="municipality"></a>**Obec**: Město nebo město. 

<a name="municipality-subdivision"></a>**Pododdíl obce**: Členění obce, například název čtvrti nebo místní oblasti, například "centrum města".

## <a name="n"></a>Ne

<a name="navigation-bar"></a>**Navigační panel**: Sada ovládacích prvků na mapě používaná k úpravě úrovně přiblížení, rozteče, otočení a přepnutí základní vrstvy mapy.

<a name="nearby-search"></a>**Hledání v okolí**: Prostorový dotaz, který prohledává pevnou přímou vzdálenost (jako vzdušnou čarou) od bodu.

<a name="neutral-ground-truth"></a>**Neutrální pozemní pravda**: Mapa, která vykresluje štítky v úředním jazyce regionu, který představuje, a v místních skriptech, pokud je k dispozici.

## <a name="o"></a>O

<a name="origin"></a>**Původ**: Počáteční bod nebo místo, ve kterém se uživatel nachází.

## <a name="p"></a>P

<a name="panning"></a>**Posouvání**: Proces pohybu mapy v libovolném směru při zachování konstantní úrovně přiblížení.

<a name="parcel"></a>**Pozemek**: Pozemek nebo hranice pozemku.

<a name="pitch"></a>**Rozteč**: Velikost náklonu mapy má vzhledem k svislé, kde 0 se dívá přímo dolů na mapě.

<a name="point"></a>**Bod**: Geometrie, která představuje jednu pozici na mapě. 

<a name="points-of-interest-poi"></a>**Body zájmu (POI)**: Obchodní, orientační bod nebo společné místo zájmu.

<a name="polygon"></a>**Polygon**: Objemová geometrie, která představuje oblast na mapě. 

<a name="polyline"></a>**Polyna :** Geometrie použitá k reprezentaci čáry. Označuje se také jako LineString. 

<a name="position"></a>**Poloha**: Zeměpisná délka, zeměpisná šířka a nadmořská výška (souřadnice x,y,z) bodu.

<a name="post-code"></a>**PSČ**: viz [PSČ](#postal-code).

<a name="postal-code"></a>**PSČ**: Řada písmen nebo čísel nebo obojího v určitém formátu. Poštovní směrovací číslo používá poštovní služba země/oblasti k rozdělení zeměpisných oblastí do zón za účelem zjednodušení doručování pošty.

<a name="primary-key"></a>**Primární klíč**: První ze dvou klíčů předplatných, které jsou k dispozici pro ověřování sdíleného klíče Azure Maps. Viz [Ověřování pomocí sdíleného klíče](#shared-key-authentication).

<a name="prime-meridian"></a>**Hlavní poledník**: Čára podély, která představuje zeměpisnou šířku. Obecně platí, že hodnoty podélu se snižují při cestování západním směrem až do 180 stupňů a zvyšují se při cestování východnímsměremm na -180 stupňů. 

<a name="prj"></a>**PRJ**: Textový soubor, který často doprovází soubor Shapefile, který obsahuje informace o promítnutém souřadnicovém systému, ve kterém se datová sada nachází.

<a name="projection"></a>**Projekce**: Promítaný souřadný systém založený na mapové projekci, jako je příčný Mercator, Albersrova rovná plocha a Robinson. Ty poskytují schopnost promítat mapy zemského sférického povrchu na dvourozměrnou kartézskou souřadnicovou rovinu. Promítané souřadné systémy jsou někdy označovány jako mapové projekce.

## <a name="q"></a>Q

<a name="quadkey"></a>**Quadkey**: Základní-4 adresa index pro dlaždice v rámci quadtree obkladové soustavy. Další informace naleznete v tématu Úrovně zvětšení a dokumentace [k dlaždicové mřížce.](zoom-levels-and-tile-grid.md)

<a name="quadtree"></a>**Quadtree**: Datová struktura, ve které má každý uzel přesně čtyři podřízené objekty. Systém dlaždic používaný v Azure Maps používá quadtree strukturu tak, že jako uživatel přiblíží v jedné úrovni, každá mapa dlaždice rozdělí do čtyř podtile.  Další informace naleznete v tématu Úrovně zvětšení a dokumentace [k dlaždicové mřížce.](zoom-levels-and-tile-grid.md)

<a name="queries-per-second-qps"></a>**Dotazy za sekundu (QPS)**: Počet dotazů nebo požadavků, které lze provést na službu nebo platformu během jedné sekundy. 

## <a name="r"></a>R

<a name="radial-search"></a>**Radiální vyhledávání**: Prostorový dotaz, který prohledává pevnou lineární vzdálenost (jako vzdušnou čarou) od bodu. 

<a name="raster-data"></a>**Rastrová data**: Matice buněk (nebo obrazových bodů) uspořádaná do řádků a sloupců (nebo mřížky), kde každá buňka obsahuje hodnotu představující informace, například teplotu. Raster je patří digitální letecké snímky, snímky ze satelitů, digitální obrázky, a naskenované mapy.

<a name="raster-layer"></a>**Rastrová vrstva**: Vrstva dlaždic, která se skládá z rastrových obrazů.

<a name="reachable-range"></a>**Dosažitelný rozsah**: Dosažitelný rozsah definuje oblast, ve které může někdo cestovat v určeném čase nebo vzdálenosti, aby způsob dopravy mohl cestovat v libovolném směru z místa. Viz také [Isochrone](#isochrone) a [Isodistance](#isodistance).

<a name="remote-sensing"></a>**Dálkový průzkum**země : Proces sběru a interpretace dat ze senzorů z dálky.

<a name="rest-service"></a>**REST service**: Zkratka REST je zkratka reprezentačního přenosu stavu. Služba REST je webová služba založená na adrese URL, která ke komunikaci spoléhá na základní webovou technologii, přičemž nejběžnějšími metodami jsou požadavky HTTP GET a POST. Tyto typy služeb mají tendenci ke mně mnohem rychlejší a menší než tradiční služby založené na SOAP.

<a name="reverse-geocode"></a>**Reverzní geokód**: Proces souřadnice a určení adresy, ve které je na mapě.

<a name="reproject"></a>**Přeprojekt :** Viz [Transformace](#transformation).

<a name="rest-service"></a>**REST service**: Zkratka pro reprezentační přenos stavu. Architektura pro výměnu informací mezi partnery v decentralizovaném, distribuovaném prostředí. REST umožňuje programům v různých počítačích komunikovat nezávisle na operačním systému nebo platformě. Služba může odeslat požadavek http (Hypertext Transfer Protocol) do jednotného lokátoru prostředků (URL) a získat zpět data.

<a name="route"></a>**Trasa**: Trasa mezi dvěma nebo více místy, která může také obsahovat další informace, jako jsou pokyny pro trasové body na trase.

<a name="requests-per-second-rps"></a>**Požadavky za sekundu (RPS)**: Viz [dotazy za sekundu (QPS)](#queries-per-second-qps). 

<a name="rss"></a>**RSS**: Zkratka pro opravdu jednoduché syndication, popis zdrojů Framework (RDF) Site Summary, nebo Rich Site Summary, v závislosti na zdroji. Jednoduchý strukturovaný formát XML pro sdílení obsahu mezi různými weby. Dokumenty RSS obsahují klíčové prvky metadat, jako je autor, datum, název, stručný popis a hypertextový odkaz. Tyto informace pomáhají uživateli (nebo službě vydavatele RSS) rozhodnout, jaké materiály stojí za další prošetření.

## <a name="s"></a>S

<a name="satellite-imagery"></a>**Satelitní snímky**: Snímky, které byly zachyceny letadly a satelity směřujícími přímo dolů.

<a name="secondary-key"></a>**Sekundární klíč**: Druhý ze dvou klíčů předplatných, které jsou k dispozici pro ověřování sdíleného klíče Azure Maps. Viz [Ověřování pomocí sdíleného klíče](#shared-key-authentication).

<a name="shapefile-shp"></a>**Shapefile (SHP)**: Také známý jako ESRI Shapefile, je formát úložiště vektorových dat pro ukládání umístění, tvaru a atributů geografických prvků. Soubor shapefile je uložen v sadě souvisejících souborů.

<a name="shared-key-authentication"></a>**Ověřování pomocí sdíleného klíče**: Ověřování pomocí sdíleného klíče závisí na předání klíčů generovaných účtem Azure Maps s každým požadavkem na Azure Maps. Tyto klíče jsou často označovány jako klíče předplatného. Doporučuje se, aby klíče byly pravidelně regenerovány z důvodu zabezpečení. Dva klíče jsou k dispozici tak, aby bylo možné udržovat připojení pomocí jednoho klíče při obnově druhého. Při opětovné mnoství klíče, je nutné aktualizovat všechny aplikace, které přistupují k tomuto účtu používat nové klíče. Další informace o ověřování na Mapách Azure najdete v [tématu Azure Maps a Azure AD](azure-maps-authentication.md) and [Manage authentication in Azure Maps](how-to-manage-authentication.md).

<a name="software-development-kit-sdk"></a>**Sada pro vývoj softwaru (SDK):** Kolekce dokumentace, ukázkový kód a ukázkové aplikace, které vývojářům pomohou používat rozhraní API k vytváření aplikací.

<a name="spherical-mercator-projection"></a>**Sférická mercatorová projekce**: Viz [Web Mercator](#web-mercator). 

<a name="spatial-query"></a>**Prostorový dotaz**: Požadavek na službu, která provádí prostorovou operaci. Například radiální vyhledávání nebo podél vyhledávání trasy.

<a name="spatial-reference"></a>**Prostorový odkaz**: Místní, regionální nebo globální systém založený na souřadnicích, který se používá k přesnému vyhledání geografických entit. Definuje souřadnicový systém používaný k propojení souřadnic mapy s místy v reálném světě. Prostorové odkazy zajišťují, že prostorová data z různých vrstev nebo zdrojů mohou být integrována pro přesné zobrazení nebo analýzu. Azure Maps používá souřadnicový referenční systém [EPSG:3857](https://epsg.io/3857) a WGS 84 pro vstupní geometrii dat.

<a name="sql-spatial"></a>**Prostorové SQL**: Odkazuje na prostorové funkce integrované do SQL Azure a SQL Server 2008 a vyšší. Tato prostorová funkce je také k dispozici jako knihovna .NET, kterou lze použít nezávisle na serveru SQL Server. Další informace naleznete v [dokumentaci k prostorovým datům (SQL Server)](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) další informace.

<a name="subscription-key"></a>**Klíč předplatného**: Viz [Ověřování sdíleného klíče](#shared-key-authentication).

<a name="synchronous-request"></a>**Synchronní požadavek**: Požadavek HTTP otevře připojení a čeká na odpověď. Prohlížeče omezují počet souběžných požadavků HTTP, které lze vytvořit ze stránky. Pokud je současně provedeno více dlouhotrvajících synchronních požadavků, lze tohoto limitu dosáhnout. Žádosti budou odloženy, dokud nebude dokončenjeden z ostatních požadavků.

## <a name="t"></a>T

<a name="telematics"></a>**Telematika**: Odesílání, přijímání a ukládání informací prostřednictvím telekomunikačních zařízení ve spojení s ovládáním na vzdálených objektech. 

<a name="temporal-data"></a>**Časová data**: Data, která konkrétně odkazují na časy nebo data. Časová data mohou odkazovat na diskrétní události, jako jsou údery blesku; pohybující se předměty, jako jsou vlaky; nebo opakovaná pozorování, například počty z dopravních senzorů.

<a name="terrain"></a>**Terén**: Plocha země, která má zvláštní charakteristiku, jako je písčitý terén nebo hornatý terén.

<a name="thematic-maps"></a>**Tematické mapy**: Tematická mapa je jednoduchá mapa, která odráží téma o zeměpisné oblasti. Běžným scénářem pro tento typ mapy je vybarvení oblastí správy, jako jsou země nebo oblasti, na základě některých metrik dat.

<a name="tile-layer"></a>**Vrstva dlaždic**: Vrstva zobrazená sestavením dlaždic mapy (obdélníkových řezů) do souvislé vrstvy. Dlaždice jsou buď rastrové dlaždice obrázků nebo vektorové dlaždice. Rastrové vrstvy dlaždic se obvykle vykreslují dopředu a jsou uloženy jako obrazy na serveru. Vrstvy rastrových dlaždic mohou využívat velký úložný prostor. Vrstvy vektorových dlaždic jsou vykresleny téměř v reálném čase v rámci klientské aplikace. Požadavky na úložiště na straně serveru jsou tedy menší pro vrstvy vektorových dlaždic.

<a name="time-zone"></a>**Časové pásmo**: Oblast zeměkoule, která dodržuje jednotný standardní čas pro právní, obchodní a sociální účely. Časová pásma mají tendenci sledovat hranice zemí nebo oblastí a jejich subdivizí.

<a name="transaction"></a>**Transakce**: Azure Maps používá transakční licenční model, kde;

- Pro každých 15 požadovaných map nebo dlaždic dopravy je vytvořena jedna transakce.
- Pro každé volání rozhraní API pro jednu ze služeb v Azure Maps se vytvoří jedna transakce. Vyhledávání a směrování jsou příklady služby Azure Maps.

<a name="transformation"></a>**Transformace**: Proces převodu dat mezi různými geografickými souřadnicovými systémy. Můžete mít například některá data, která byla zachycena ve Spojeném království a založena na geografickém souřadnicovém systému OSGB 1936. Azure Maps používá variantu souřadnicového systému [EPSG:3857](https://epsg.io/3857) WGS84. Jako takové zobrazit data správně, bude muset mít své souřadnice transformovány z jednoho systému do druhého.

<a name="traveling-salesmen-problem-tsp"></a>**Problém cestujícího prodejce (TSP):** Problém hamiltonského okruhu, při kterém musí prodejce najít nejúčinnější způsob, jak navštívit řadu zastávek, a pak se vrátit do výchozího místa.  

<a name="trilateration"></a>**Trilatelace**: Proces určení polohy bodu na zemském povrchu, vzhledem ke dvěma dalším bodům, měřením vzdáleností mezi všemi třemi body.

<a name="turn-by-turn-navigation"></a>**Turn-by-turn navigace**: Aplikace, která poskytuje pokyny k trase pro každý krok trasy, jak se uživatelé blíží k dalšímu manévru.

## <a name="v"></a>V

<a name="vector-data"></a>**Vektorová data**: Data založená na souřadnicích, která jsou reprezentována jako body, čáry nebo polygony.

<a name="vector-tile"></a>**Vektorová dlaždice**: Specifikace otevřených dat pro ukládání geoprostorových vektorových dat pomocí stejného systému dlaždic jako ovládací prvek mapy. Viz také [vrstva dlaždice](#tile-layer).

<a name="vehicle-routing-problem-vrp"></a>**Problém směrování vozidel (VRP)**: Třída problémů, ve které se vypočítá sada objednaných tras pro vozový park vozidel, přičemž se bere v úvahu jako soubor omezení. Tato omezení mohou zahrnovat okna doby doručení, více kapacit trasy a omezení doby trvání cesty.

<a name="voronoi-diagram"></a>**Voronoiův diagram**: Oddíl prostoru do oblastí nebo buněk, které obklopují sadu geometrických objektů, obvykle bodových prvků. Tyto buňky, nebo polygony, musí splňovat kritéria pro Delaunay trojúhelníky. Všechna umístění v oblasti jsou blíže k objektu, který obklopuje, než k jakémukoli jinému objektu v sadě. Voronoiovy diagramy se často používají k vymezení oblastí vlivu kolem zeměpisných rysů. 

## <a name="w"></a>W

<a name="waypoint"></a>**Waypoint**: Waypoint je určená zeměpisná poloha definovaná zeměpisnou délkou a šířkou, která se používá pro navigační účely. Často se používá k reprezentaci bodu, ve kterém někdo prochází trasou.

<a name="waypoint-optimization"></a>**Optimalizace trasových bodů**: Proces přeuspořádání sady trasových bodů, aby se minimalizovala doba jízdy nebo vzdálenost potřebná k průchodu všemi poskytnutými trasové body. V závislosti na složitosti optimalizace se tato optimalizace často označuje jako [problém s pojízdnou prodejnou](#traveling-salesmen-problem-tsp) nebo [problém směrování vozidel](#vehicle-routing-problem-vrp).

<a name="web-map-service-wms"></a>**Web Map Service (WMS)**: WMS je standard Open Geographic Consortium (OGC), který definuje mapové služby založené na obrázcích. Služby WMS poskytují mapové obrázky pro konkrétní oblasti v rámci mapy na vyžádání. Obrázky obsahují předem vykreslenou symboliku a mohou být vykresleny v jednom z několika pojmenovaných stylů, pokud jsou definovány službou.

<a name="web-mercator"></a>**Web Mercator**: Také známý jako sférický Mercator projekce. Je to nepatrná varianta projekce Mercator, která se používá především ve webových mapových programech. Používá stejné vzorce jako standardní projekce Mercator, jako je tomu u malých map. Web Mercator však používá sférické vzorce ve všech měřítcích, ale rozsáhlé mapy Mercator obvykle používají elipsoidní formu projekce. Tento rozdíl je nepostřehnutelný v globálním měřítku, ale způsobuje, že mapy místních oblastí se mírně odchylují od skutečných elipsoidních mercatorových map ve stejném měřítku.

<a name="wgs84"></a>**WGS84**: Sada konstant používaných k propojení prostorových souřadnic s umístěními na povrchu mapy. Základna WGS84 je standardní, kterou používá většina poskytovatelů online mapování a zařízení GPS. Azure Maps používá variantu souřadnicového systému [EPSG:3857](https://epsg.io/3857) WGS84.

## <a name="z"></a>Z

<a name="z-coordinate"></a>**Souřadnice Z**: Viz [Nadmořská výška](#altitude). 

<a name="zip-code"></a>**PSČ**: Viz [PSČ](#postal-code).

<a name="Zoom level"></a>**Úroveň zvětšení**: Určuje úroveň podrobností a počet viditelných map. Při přiblížení až na úroveň 0 bude často viditelná úplná mapa světa. Mapa však zobrazí omezené podrobnosti, jako jsou názvy zemí nebo oblastí, hranice a názvy oceánů. Při přiblížení blíže k úrovni 17 se na mapě zobrazí oblast několika městských bloků s podrobnými informacemi o silnici. V mapách Azure je nejvyšší úroveň přiblížení 22. Další informace naleznete v [dokumentaci k úrovním lupy a k dlaždicové mřížce.](zoom-levels-and-tile-grid.md)

