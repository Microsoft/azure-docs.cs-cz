---
title: Azure Maps Glosář | Dokumentace Microsoftu
description: Glosář související s Azure Maps, Location Based Services a GIS běžně používaných výrazů.
author: rbrundritt
ms.author: richbrun
ms.date: 09/18/2018
ms.topic: resource
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: d220fea64f860ebe5b660f7ebe5ad7db7aec4534
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46368603"
---
# <a name="glossary"></a>Glosář

Následuje seznam běžná slova používat s Azure Maps.

## <a name="a"></a>A

<a name="address-validation"></a> **Adresa ověření**: proces ověřování existence adresu.

<a name="advanced-routing"></a> **Pokročilé směrování**: kolekce služeb, které provádějí operace zálohy pomocí směrování dat cestách, jako je výpočet dostupné rozsahy (izochronů), velikost matice a batch směrování požadavků.

<a name="aerial-imagery"></a> **Leteckých snímků**: viz [satelitní trénováním](#satellite-imagery). 

<a name="along-a-route-search"></a> **Podél hledání trasy**: prostorový dotaz, který vyhledá data, která jsou v rámci zadané náhradní proces, čas nebo vzdálenost od cesta trasy.

<a name="altitude"></a> **Výška**: výšku nebo vertikální zvýšení bod nad odkaz na povrchu. Výška měření vycházejí z daného odkazu datum, jako je střední stopách. Další informace naleznete v tématu ke zvýšení úrovně oprávnění.

<a name="ambiguous"></a> **Nejednoznačný**: ve stavu nejistoty klasifikaci dat, které existuje, když objekt odpovídajícím způsobem možné přiřadit dva nebo více hodnot pro daný atribut. Například při geokódování "CA" dvě dvojznačné výsledky jsou vráceny; "Kanada" a "Kalifornie" jako "CA" je kód země a stavu pro každý v uvedeném pořadí. 

<a name="annotation"></a> **Poznámka**: Text nebo grafiky zobrazená na mapě poskytují informace o uživateli. Poznámka může identifikovat popisují konkrétní mapování entity, zadání obecných informací o oblasti na mapě nebo zadat informace o mapování samotný.

<a name="antimeridian"></a> **Antimeridian**: označované také jako 180<sup>th</sup> poledníků je se setkávají-180 stupňů a 180stupňový rozsah s orientací délky. Což je opačné poledníku, který na celém světě.

<a name="application-programming-interface-api"></a> **Rozhraní API (Application Programming)**: specifikace, která umožňuje vývojářům vytvářet aplikace.

<a name="api-key"></a> **Klíč rozhraní API**: viz klíč map Azure.

<a name="area-of-interest-aoi"></a> **Oblast zájmu (AOI)**: rozsah sloužících k definování zaměření pro produkční prostředí na mapě nebo databáze.

<a name="asset-tracking"></a> **Sledování prostředků**: proces umístění prostředek, jako osoba, vozidel nebo některý objekt pro sledování.

<a name="asynchronous-request"></a> **Asynchronní požadavek**: požadavek HTTP, který otevře připojení a odešle požadavek na server, který vrátí asynchronního požadavku, identifikátor ukončí připojení. Server pokračuje ve zpracování žádosti a uživatele můžete zkontrolovat stav pomocí identifikátoru. Žádost po dokončení zpracování, uživatel může potom stahování odpovědi. Tento typ požadavku se obvykle používá pro dlouho spuštěných procesů.

<a name="autocomplete"></a> **Automatické dokončování**: funkce v aplikaci předpovídá zbývající slova zadání uživatele. 

<a name="autosuggest"></a> **Pro automatické návrhy**: funkce v aplikaci předpovídá logické možností pro zadání uživatele.

<a name="azure-location-based-services-lbs"></a> **Azure umístění Based Services (LBS)**: původní název Azure Maps, že je ve verzi preview.

<a name="azure-maps-key"></a> **Klíč Azure Maps**: Azure Maps Key je jedinečný řetězec, který se používá k ověření uživatele Azure Maps aplikace nebo žádosti o službu. 

## <a name="b"></a>B

<a name="base-map"></a> **Základní mapa**: součástí mapy aplikace, která se zobrazuje na pozadí referenční informace, jako jsou cesty, orientačních bodů a politické hranice.

<a name="batch-request"></a> **Žádost o dávku**: Proces kombinování více požadavků do jednoho požadavku.

<a name="bearing"></a> **Opatřené**: vodorovném směru bodu ve vztahu k jinému bodu. Vyjadřuje se jako úhel vzhledem k severu ze stupňů 0 do 360 stupňů ve směru hodinových ručiček. 

<a name="boundary"></a> **Hranice**: řádek nebo oddělení sousední politické entit, jako je například země, oblasti a vlastnosti mnohoúhelníku. Hranice je řádek, který může nebo nemusí odpovídat fyzické funkce, jako je řek, hory nebo stěny.

<a name="bounds"></a> **Hranice**: viz [Bounding pole](#bounding-box).

<a name="bounding-box"></a> **Ohraničující rámeček**: Sada souřadnic používá k reprezentování obdélníkovou oblast na mapě. 

## <a name="c"></a>C

<a name="cadastre"></a> **Katastru**: záznam o registrovaný pozemního a vlastnosti. Viz také [balení](#parcel).

<a name="camera"></a> **Fotoaparát**: V kontextu ovládacího prvku interaktivní mapu, kamera definuje mapování pole zobrazení. Zobrazení fotoaparátu/kamery je určen na základě několika parametrů mapy; System Center, úroveň přiblížení, výšku, vliv. 

<a name="centroid"></a> **Těžiště**: geometrické center funkce. Těžiště řádku by středního těžiště mnohoúhelníku by se už jeho střed oblasti.

<a name="choropleth-map"></a> **Mapa Choropleth**: tematických mapy, ve které oblasti jsou šedé výkonový zisk měření statistické proměnné se zobrazí na mapě. Například obarvení hranice jednotlivých státní na základě jeho relativní naplnění pro všechny ostatní stavy.

<a name="concave-hull"></a> **Konkávní trupu**: obrazce, která představuje možné konkávní geometrii, která obklopuje všechny obrazce v zadané datové sadě. Vygenerovaný tvar je podobný obtékání data s plastů zalamování řádků a potom vytápění, způsobující velké zahrnuje mezi body cave směrem k dalších datových bodů.

<a name="consumption-model"></a> **Využití modelu**: informace, které definují spotřebu jakou vozidla posílit nebo k elektrické energie. Viz také [dokumentaci model spotřeby](consumption-model.md).

<a name="control"></a> **Ovládací prvek**: samostatná nebo opakovaně použitelné komponenty skládající se z grafického uživatelského rozhraní, který definuje sadu chování pro rozhraní. Například mapový ovládací prvek je obvykle část uživatelského rozhraní, který načte interaktivní mapu.

<a name="convex-hull"></a> **Konvexní trupu**: konvexní trupu je tvar, který představuje minimální konvexní geometrii, který obklopuje všechny obrazce v zadané datové sadě. Vygenerovaný tvar je podobný obtékání elastický kolem datové sady.

<a name="coordinate"></a> **Koordinovat**: sestává z hodnot zeměpisné šířky a délky, používá k reprezentování umístění na mapě.

<a name="coordinate-system"></a> **Systém souřadnic**: referenční architektura používá k definování polohy bodů v prostoru v dvě nebo tři dimenze.

<a name="country-code"></a> **Kód země**: Jedinečný identifikátor pro zemi. podle standardu ISO. ISO2 je dvoumístný kód země, (například USA), která představuje ISO3 tři znaky kódu (například USA).

<a name="country-subdivision"></a> **Dělení země**: dělení první úrovně země, obvykle označuje jako stát nebo kraj.

<a name="country-secondary-subdivision"></a> **Sekundární dělení země**: dělení druhé úrovně země, obvykle označuje jako okres.

<a name="country-tertiary-subdivision"></a> **Terciární dělení země**: dělení třetí úrovně země, obvykle pojmenované oblasti, jako je například dál.

<a name="cross-street"></a> **Ulice napříč**: bod, kde dva nebo více ulice intersect.

<a name="cylindrical-projection"></a> **Válcové**: projekci, která transformuje body z spheroid nebo oblasti na tangens nebo secant – válce. Válce je pak průřezem shora dolů a sloučí do roviny.

## <a name="d"></a>D

<a name="datum"></a> **Datum**: Specifikace referencí měřicího systému, systém souřadnic umístí na ploše (vodorovné datum) nebo výšky nad nebo pod surface (svislé datum).

<a name="dbf-file"></a> **Soubor DBF není Podporován**: formát souboru databáze, který se používá v kombinaci s soubory ve formátu Shapefile (SHP).

<a name="degree-minutes-seconds-dms"></a> **Stupeň minut sekund (DMS)**: jednotka měření pro popis zeměpisné šířky a délky. Takovou úroveň důvěryhodnosti je 1/360<sup>th</sup> kruhu. Takovou úroveň důvěryhodnosti se dál dělí na 60 minut, a chvíli je rozdělen do 60 sekund.

<a name="delaunay-triangulation"></a> **Triangulační Delaunay**: postup pro vytvoření sítě souvislé, nepřekrývající trojúhelníky z datové sady bodů. Každý trojúhelník vnější kruh neobsahuje žádné hodnoty z datové sady v jeho vnitřku.

<a name="demographics"></a> **Demografické údaje**: statistické vlastnosti (například věk, projekcemi a příjmy) lidské základního souboru.

<a name="destination"></a> **Určení**: koncový bod nebo umístění, ve kterém, někdo je na cestách.

<a name="digital-elevation-model-dem"></a> **Digitální zvýšení oprávnění modelu (DEM)**: datovou sadu zvýšení hodnoty související se surface, zachytávat po určitou oblast v pravidelných intervalech pomocí běžných datum. DEMs se obvykle používají znázornit humanitární terénu.

<a name="dijkstra's-algorithm"></a> **Algoritmus pro Dijkstra**: algoritmus, který zkontroluje připojení sítě k nalezení nejkratší cesty mezi dvěma body.

<a name="distance-matrix"></a> **Matice vzdáleností**: matice, který obsahuje informace vzdálenost a doba trvání cesty mezi sadu zdrojů a cílů. 

## <a name="e"></a>E

<a name="elevation"></a> **Zvýšení oprávnění**: svislou vzdálenost bodu nebo objekt nad nebo pod odkaz na ploše nebo datum (obecně Střední mořská úroveň). Zvýšení oprávnění obecně označuje výšky pozemního.

<a name="envelope"></a> **Obálka**: viz [Bounding pole](#bounding-box).

<a name="extended-postal-code"></a> **Rozšířené PSČ**: poštovní směrovací číslo, které mohou obsahovat další informace. Například v USA, PSČ mít pět číslic ale rozšířeného PSČ, označované jako zip + 4, bude obsahovat čtyři další číslice. Tyto další číslice se používají k identifikaci geografické segment v oblasti doručování pět číslic, jako je například blok Město, skupina objekty apartment nebo pole office příspěvku, který pomáhá při řazení efektivní e-mailu a doručování.

<a name="extent"></a> **Rozsah**: viz [Bounding pole](#bounding-box).

## <a name="f"></a>F

<a name="federated-authentication"></a> **Federované ověřování**: metodu ověřování, která umožňuje jednotné přihlašování a ověřování mechanismus použít v rámci více webových a mobilních aplikací. 

<a name="feature"></a> **Funkce**: objekt, který kombinuje geometrii s informacemi dalších metadat. 

<a name="feature-collection"></a> **Kolekce funkcí**: kolekce objektů funkce.

<a name="find-along-route"></a> **Najít trase**: prostorový dotaz, který vyhledá data, která jsou v rámci zadané náhradní proces, čas nebo vzdálenost od cesta trasy.

<a name="find-nearby"></a> **Vyhledejte blízké**: prostorový dotaz, který vyhledá pevná lineární vzdálenost (jak vzdušnou čarou) z bodu.

<a name="fleet-management"></a> **Loďstva správu**: správu komerční vozidla auta, vozů, dodává nebo plochy. Správa vozového parku může zahrnovat celou řadu funkcí, jako je například financování vozidla, údržby, telematika (sledování a diagnostiku) stejně jako ovladač, rychlost, palivo a stav a bezpečnostní správu. Správa vozového parku je proces využívaný sadou společnosti, které využívají dopravy ve firmě. Chcete-li minimalizovat rizika a snižovat jejich celkové doprava a pracovníci náklady, a současně zajistit dodržování předpisů pro státní správu.

<a name="free-flow-speed"></a> **Bezplatné tok rychlosti**: volného toku rychlost očekává ideální podmínek. Obvykle omezení rychlosti.

<a name="free-form-address"></a> **Adresu volného formátu**: úplná adresa, která je reprezentována jako jeden řádek textu.

<a name="fuzzy-search"></a> **Vyhledávání přibližných shod**: vyhledávání, které přebírá volným tvarem textový řetězec, který může obsahovat adresu bodu zájmu. 

## <a name="g"></a>G

<a name="geocode"></a> **Geokód**: adresy nebo umístění, ke kterému byla převedena na souřadnice, který slouží k zobrazení tohoto umístění na mapě. 

<a name="geocoding"></a> **Geokódování**: taky říká dopředné geokódování, je proces převodu adresa umístění dat na souřadnice. 

<a name="geodesic-path"></a> **Cesta k geodetických**: nejkratší cesty mezi dvěma body na zakřivené povrchu. Při vykreslování v Azure Maps tato cesta se zobrazí jako křivka kvůli projekci Mercator.

<a name="geofence"></a> **Monitorové geografické zóny**: A definované geografické oblasti, které můžete použít k aktivaci události, kdy existuje oblast nebo přejde do zařízení.

<a name="geojson"></a> **GeoJSON**: je běžný formát souborů JSON se používá k ukládání zeměpisné vektorová data, například body, řádky a mnohoúhelníku. **Poznámka:**: Azure Maps používá rozšířenou verzi GeoJSON jako [zdokumentované tady](extend-geojson.md).

<a name="geometry"></a> **Geometrie**: představuje prostorový například bod, řádek nebo mnohoúhelníku.

<a name="geometrycollection"></a> **GeometryCollection**: kolekce geometrické objekty.

<a name="geopol"></a> **GeoPol**: odkazuje na geopoliticky citlivá data, jako je například sporné ohraničení a místních jmen.

<a name="georeference"></a> **Vytváření**: proces zarovnání zeměpisných dat nebo dokumentů známé systém souřadnic. Tento proces může obsahovat posunutí, otočení, změna měřítka nebo zkosení data.

<a name="georss"></a> **GeoRSS**: rozšíření jazyka XML pro přidávání prostorová data k informačním kanálům RSS.

<a name="gis"></a> **GIS**: zkratka pro "Systém geografické informace". Běžné pojem používaný pro mapování oboru.

<a name="gml"></a> **GML**: označuje se taky jako Geography Markup Language. Rozšíření souboru XML pro ukládání prostorová data.

<a name="gps"></a> **GPS**: označují jako globální umístění systému, je systém satelity sloužící k určení umístění zařízení na světě. Orbiting satelity odesílání signálů, které umožňují GPS příjemce kdekoli na světě pro výpočet vlastní umístění prostřednictvím trilateration.

<a name="gpx"></a> **GPX**: také známé jako formát výměny GPS, je často vytvořena z GPS zařízení formát souboru XML.  

<a name="great-circle-distance"></a> **Delšími**: nejkratší vzdáleností mezi dvěma body na povrchu koule.

<a name="greenwich-mean-time-gmt"></a> **Střední čas (GMT)**: čas poledníku, který se spouští pomocí Royal stanice v Greenwiche v Anglii.

<a name="guid"></a> **Identifikátor GUID**: globálně jedinečný identifikátor. Řetězec sloužící k jednoznačné identifikaci rozhraní, třídy, knihovnu typů, kategorie komponent nebo záznam.

## <a name="h"></a>H

<a name="haversine-formula"></a> **Vzorec Haversine**: common rovnice sloužící k výpočtu kruh velké vzdálenosti mezi dvěma body na kouli.

<a name="hd-maps"></a> **HD, High Density mapy**: také známé jako vysokou mapuje definice se skládá ze věrného silniční síti informace, jako je označení lane, materiálů a směr světla, které jsou nezbytné pro autonomního řízení.

<a name="heading"></a> **Nadpis**: směr něco odkazuje nebo připojena. Viz také [opatřené](#heading).

<a name="heatmap"></a> **Heatmapu**: vizualizace dat, ve které představují širokou paletu barev hustota body v konkrétní oblasti. Viz také [tematických mapy](#thermatic-map).

<a name="hybrid-imagery"></a> **Hybridní snímky**: satelitní nebo leteckých snímků, která má data cestách a popisky překrývající dojde k jeho zvýraznění.

## <a name="i"></a>I

<a name="iana"></a> **Organizace IANA**: zkratka pro Internet Assigned Numbers Authority. Neziskovou organizací složenou ze skupiny, která dohlíží globální přidělování IP adres.

<a name="isochrone"></a> **Isochrone**: isochrone definuje oblast, ve kterém někdo můžete projít v zadaném čase pro režimu přepravy vede libovolným směrem od daného umístění. Viz také [dostupný rozsah](#reachable-range).

<a name="isodistance"></a> **Isodistance**: zadané umístění, isochrone definuje oblast, ve které někdo můžete projít v určité vzdálenosti pro režimu přepravy vede libovolným směrem. Viz také [dostupný rozsah](#reachable-range).

## <a name="k"></a>tis.

<a name="kml"></a> **KML**: taky říká klíčové dírky Markup Language je běžný formát souboru XML pro ukládání geografické vektorová data, například body, řádky a mnohoúhelníky. 

## <a name="l"></a>L

<a name="landsat"></a> **Landsat**: Multispectral, earth orbiting satelity vypracovanou organizací cccppf NASA, shromážděte trénováním pozemek, který se používá v mnoha oborech, třeba zemědělství, lesnictví a postupy.

<a name="latitude"></a> **Zeměpisná šířka**: úhlová vzdálenost měřenou ve stupních od rovníku v Severní a Jižní směru.

<a name="level-of-detail"></a> **Úroveň podrobností**: viz [úroveň zvětšení](#zoom-level).

<a name="lidar"></a> **Lidar**: zkratka pro světlý zjišťování a rozsahu. Vzdálené průzkumu technika, která používá k měření vzdálenosti k Odrazivý povrch lasers.

<a name="linear-interpolation"></a> **Lineární interpolace**: odhad neznámou hodnotu pomocí lineárního vzdálenost mezi známé hodnoty.

<a name="linestring"></a> **LineString**: geometrii, která slouží k reprezentaci řádku. Označované také jako lomené čáry. 

<a name="localization"></a> **Lokalizace**: podpora pro různé jazyky a jazykové verze.

<a name="logistics"></a> **Logistika**: proces přechodu lidí, vozidel, dodávek nebo prostředky koordinovaným způsobem.

<a name="longitude"></a> **Zeměpisná délka**: úhlová vzdálenost měřenou ve stupních od poledníku, který ve východní a západní směru.

## <a name="m"></a>M

<a name="map-tile"></a> **Mapování dlaždice**: obdélníkové image, která představuje rozdělení plátna mapy. Další informace najdete v tématu [úrovně přiblížení a mřížka dokumentaci dlaždici](zoom-levels-and-tile-grid.md).

<a name="marker"></a> **Značky**: označované také jako PIN kód nebo připínáček je ikona, která představuje umístění bodů na mapě.

<a name="mercator-projection"></a> **Projekci mercator**: projekce válcových mapy, který začal být projekce standardní mapy pro účely námořních z důvodu jeho schopnost představují řádky konstantní kurz, označované jako řádky přesně jako přímo segmenty, které šetří úhly s poledníky. Všechny projekce bez stromové struktury mapy zkreslují tvarů nebo velikosti mapy ve srovnání s true rozložení povrchu země. Projekci Mercator zvýrazní oblasti daleko od rovníku, takže menší oblasti se zobrazí na mapě větší jak můžete přistupovat hole. 

<a name="multilinestring"></a> **MultiLineString**: geometrii, která představuje kolekci objektů LineString. 

<a name="multipoint"></a> **Systém multiPoint**: geometrii, která představuje kolekci objektů bodu.

<a name="multipolygon"></a> **MultiPolygon**: geometrii, která představuje kolekci objektů mnohoúhelníku. Například zobrazíte hranici Havajské ostrovy by každý ostrov uvedených s mnohoúhelníku a hranice Havajské ostrovy by tedy MultiPolygon.

<a name="municipality"></a> **Magistrát**: Město nebo obec. 

<a name="municipality-subdivision"></a> **Dělení magistrát**: součást magistrát, jako je například Okolní počítače nebo název místní oblasti, jako je například "centru".

## <a name="n"></a>Ne

<a name="navigation-bar"></a> **Navigační panel**: Sada ovládacích prvků na mapě používá pro upravit úroveň zvětšení, sklonu, otočení a přepínání vrstvu základní mapy.

<a name="nearby-search"></a> **Poblíž hledání**: prostorový dotaz, který vyhledá pevná lineární vzdálenost (jak vzdušnou čarou) z bodu.

<a name="neutral-ground-truth"></a> **Neutrální země pravdy**: mapu, která vykreslí popisky v oficiální jazyce, který představuje oblasti a v místní skripty, pokud je k dispozici.

## <a name="o"></a>O

<a name="origin"></a> **Počátek**: počáteční bod nebo umístění, ve kterém se uživatel.

## <a name="p"></a>P

<a name="panning"></a> **Posouvání**: proces přechodu na mapě v libovolném směru při zachování konstantní zvětšení.

<a name="parcel"></a> **Balení**: vykreslení hranice pozemního nebo vlastnost.

<a name="pitch"></a> **Rozteč**: množství náklon mapa obsahuje vzhledem k svisle, kde 0 je prohlížení přímo dolů na mapě.

<a name="point"></a> **Bod**: geometrii, která představuje jednu pozici na mapě. 

<a name="points-of-interest-poi"></a> **Body zájmu (POI)**: firmy, orientačních bodů nebo obvyklé místo, které vás zajímají.

<a name="polygon"></a> **Mnohoúhelník**: ucelený geometrie, který představuje oblasti na mapě. 

<a name="polyline"></a> **Lomené čáry**: geometrii, která slouží k reprezentaci řádku. Označované také jako typ LineString. 

<a name="position"></a> **Pozice**: zeměpisná délka, zeměpisná šířka a výška (x, y, z souřadnice) bodu.

<a name="post-code"></a> **PSČ**: viz [Postal code](#postal-code).

<a name="postal-code"></a> **Poštovní směrovací číslo**: řadu písmena nebo čísla nebo obojí, v určitém formátu, používá služba poštovní země na geografické oblasti rozdělení na zóny pro zjednodušení doručování e-mailu.

<a name="prime-meridian"></a> **Poledníku**: řádku zeměpisnou délku, která představuje zeměpisnou délku 0 stupňů. Obecně platí, hodnoty délky snížit cestách západním směrem do 180 stupňů a zvýšit při cestování východním směry -180-stupňů. 

<a name="prj"></a> **PRJ**: textový soubor, který doprovází často soubor soubor, který obsahuje informace o předpokládaných systém souřadnic datové sady je v.

<a name="projection"></a> **Projekce**: předpokládané souřadnicový systém podle projekce mapy, jako je propojeními Mercator, Albers rovnat oblasti a Robinson. Ty umožňují projektu mapy ke kulovému povrchu země na plochu dvojrozměrné souřadnice kartézský. Předpokládané souřadnicových systémů jsou někdy označovány jako projekce mapy.

## <a name="q"></a>Q

<a name="quadkey"></a> **Quadkey**: index 4 základní adresa pro dlaždici v rámci systému quadtree dělení do bloků. Další informace najdete v tématu [úrovně přiblížení a mřížka dlaždic](zoom-levels-and-tile-grid.md) Další informace naleznete v dokumentaci.

<a name="quadtree"></a> **Quadtree**: datová struktura, ve kterém má každý uzel přesně čtyři podřízené položky. Dělení do bloků systém používá ve službě Azure Maps používá strukturu quadtree tak, aby jako uživatel přiblíží jednu úroveň, každý mapovou dlaždici se rozdělí do dílčích čtyři dlaždice.  Další informace najdete v tématu [úrovně přiblížení a mřížka dlaždic](zoom-levels-and-tile-grid.md) Další informace naleznete v dokumentaci.

<a name="queries-per-second-qps"></a> **Dotazy na druhý (QPS)**: počet dotazy nebo požadavky, které je třeba služba nebo platforma v rámci jedné sekundy. 

## <a name="r"></a>R

<a name="radial-search"></a> **Paprskové hledání**: prostorový dotaz, který vyhledá pevná lineární vzdálenost (jak vzdušnou čarou) z bodu. 

<a name="raster-data"></a> **Rastrová data**: matice buněk (nebo v pixelech) uspořádány do řádků a sloupců (neboli mřížky) kde každá buňka obsahuje hodnotu představující informace, jako je například teploty. Rastrové společnosti zahrnují digitální leteckých snímků, obrázek z satelity, digitálních obrázků a naskenované mapy.

<a name="raster-layer"></a> **Rastrová vrstva**: Dlaždicovou vrstvu, která se skládá z rastrové obrázky.

<a name="reachable-range"></a> **Dostupné oblasti**: dostupný rozsah definuje oblast, ve které někdo můžete projít v rámci zvoleného časového nebo vzdálenosti použitá pro režimu přepravy projít vede libovolným směrem z umístění. Viz také [Isochrone](#isochrone) a [Isodistance](#isodistance).

<a name="remote-sensing"></a> **Vzdálené průzkumu**: proces shromažďování a interpretaci dat snímačů ze vzdálenosti.

<a name="rest-service"></a> **Služba REST s**: zkratka REST, které jsou zahrnovaného Representational State Transfer. Služba REST je založené na adrese URL webové služby, která závisí na základní webové technologii ke komunikaci, nejběžnější metody se požadavky HTTP GET a POST. Tyto typy služeb mají tendenci mně mnohem rychlejší a menší než tradiční služby založené na protokolu SOAP.

<a name="reverse-geocode"></a> **Geokódovat**: proces trvá souřadnice a určující adresu, ve kterém je představuje na mapě.

<a name="reproject"></a> **Reproject**: viz [transformace](#transformation).

<a name="rest-service"></a> **Služba REST s**: zkratka Representational State Transfer. Architektura pro výměnu informací mezi rovnocennými počítači v decentralizovaný, distribuované prostředí. REST umožňuje programům na různých počítačích komunikovat bez ohledu na jejich operačního systému nebo platformy podle odesílání požadavku protokolu HTTP (Hypertext Transfer) do uniform resource locator (URL) a získat zpět data.

<a name="route"></a> **Trasa**: cesta mezi dva nebo více umístění, které mohou zahrnovat také další informace, jako jsou pokyny, jak waypoints na trase.

<a name="requests-per-second-rps"></a> **Počet požadavků za sekundu (předávajících stran)**: viz [dotazů za sekundu (QPS)](#queries-per-second-qps). 

<a name="rss"></a> **RSS**: zkratka pro Really Simple Syndication, Souhrn webu Framework popis prostředku (RDF) nebo službou Rich Site Summary, v závislosti na zdroji. Jednoduché, strukturované XML formátu pro sdílení obsahu mezi různé weby. RSS dokumenty obsahují prvky klíčových metadat – například autor, datum, název, stručný popis a hypertextový odkaz. Tato informace objasňují uživatele (nebo službě vydavatele RSS) rozhodnout materiály stojí za další šetření.

## <a name="s"></a>S

<a name="satellite-imagery"></a> **Satelitní trénováním**: obrázek, který zachytí rovin a satelity směřující přímo dolů.

<a name="software-development-kit-sdk"></a> **Software development kit (SDK)**: kolekce dokumentaci, ukázky kódu a ukázkové aplikace umožňující vývojáři pomocí rozhraní API můžete vytvářet aplikace.

<a name="shapefile-shp"></a> **Soubor (SHP)**: označují jako ve formátu ESRI Shapefile, je formátu vektoru dat úložiště pro ukládání umístění, tvar a atributy geografické funkcí. Soubor je uložen v nastavení sady souvisejících souborů.

<a name="spherical-mercator-projection"></a> **Ke kulovému projekci Mercator**: viz [webové Mercator](#web-mercator). 

<a name="spatial-query"></a> **Prostorový dotaz**: žádost službě, která provádí prostorových operace. Například jako paprskového hledání nebo podél hledání trasy.

<a name="spatial-reference"></a> **Prostorové reference**: souřadnice místní, místní nebo globální systémem používaná k nalezení přesně zeměpisné entity. Definuje souřadnicový systém používá k propojení souřadnicích mapy místa v reálném světě. Prostorové reference Ujistěte se, že je možné integrovat prostorová data z různých vrstev nebo zdrojů pro přesné zobrazení nebo analýza. Použití Azure Maps [EPSG:3857](https://epsg.io/3857) koordinovat referenčního systému a WGS 84 pro geometrii vstupní data. 

<a name="sql-spatial"></a> **SQL prostorových**: odkazuje na prostorové funkce integrované do SQL Azure a SQL Server 2008 a novějších. Tato prostorové funkce je také k dispozici jako knihovna .NET, který je možné nezávisle na serveru SQL Server. Další informace najdete v tématu [prostorových dat (SQL Server) dokumentaci](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) Další informace.

<a name="synchronous-request"></a> **Synchronní žádosti**: požadavek HTTP otevře připojení a čeká na odpověď. Prohlížeče omezit počet souběžných požadavků HTTP, které mohou být provedeny ze stránky. Pokud dlouho trvající synchronní žádosti více probíhají ve stejnou dobu, může být dosažení tohoto limitu a požadavky se pozastavilo, dokud jeden z ostatních požadavků byla dokončena.

## <a name="t"></a>T

<a name="telematics"></a> **Telematika**: odesílání, příjem a ukládání informací o prostřednictvím zařízení telekomunikační ve spojení s několikrát ovládací prvek na vzdálených objektů. 

<a name="temporal-data"></a> **Dočasná data**: Data, konkrétně odkazující na dobu nebo kalendářní data. Dočasná data mohou odkazovat na diskrétní události, jako je například bleskově katastrof; Přesun objektů, jako je například železniční; nebo opakované pozorování, jako jsou počty senzorových přenosy.

<a name="terrain"></a> **Terénu**: oblast pozemek mají určité charakteristiky, jako je například písčitých terénu nebo povrchu.

<a name="thematic-maps"></a> **Tematických mapy**: tematických mapa je jednoduchý mapy provedené tak, aby odrážely motiv o geografické oblasti. Běžný scénář pro tento typ mapy je barva pro správu oblasti jako je například země na základě některé metriky data.

<a name="tile-layer"></a> **Dlaždicová vrstva**: vrstvě zobrazuje sestavení do průběžné vrstvy dlaždic map (obdélníkové části). Dlaždice jsou buď rastrových obrázků dlaždic nebo vektorových dlaždic. Rastrové dlaždice vrstvy jsou obvykle vykreslen předem domluvili a uložené jako bitové kopie na serveru. To může trvat až velké množství prostoru úložiště. Vrstvy dlaždic vektorových vykreslení v reálném čase v rámci klientské aplikace, proto jsou menší požadavky na úložiště na straně serveru.

<a name="time-zone"></a> **Časové pásmo**: oblasti světě, která dodržuje jednotné standardní čas pro účely právních, obchodních a sociálních sítí. Časová pásma mívají sklony dodržovat hranice země a jejich třídění.

<a name="transaction"></a> **Transakce**: Azure Maps používá transakční licenčního modelu kde;

- Jedna transakce vytvoří pro každých 15 dlaždicích mapy nebo provoz požadovaný.
- Jedna transakce vytvoří pro každé volání rozhraní API do jedné služby ve službě Azure Maps, například vyhledávání, nebo směrují.

<a name="transformation"></a> **Transformace**: proces převodu dat mezi různými systémy zeměpisné souřadnice. Například může mít data, která byla zachycena ve Spojeném království a založený na systému OSGB 1936 zeměpisné souřadnice. Použití Azure Maps [EPSG:3857](https://epsg.io/3857) variantu systém souřadnic odkaz WGS84. V důsledku správně zobrazit data, ji budou muset mít jeho souřadnice transformuje z jednoho systému do druhého.

<a name="traveling-salesmen-problem-tsp"></a> **Traveling obchodníků problém (TSP)**: Hamiltonian okruh problém ve kterém musí najít prodejce nejúčinnější způsob přejdete řadu zarážky, pak se vraťte do počáteční umístění.  

<a name="trilateration"></a> **Trilateration**: procesu, který určuje umístění bodu na povrchu země s ohledem na dva další body na základě měření vzdálenosti mezi všechny tři body.

<a name="turn-by-turn-navigation"></a> **Zapnout vypnout navigační**: aplikace, která obsahuje trasy pokyny pro každého kroku postupu jako uživatelé přiblíží další manévrů.

## <a name="v"></a>V

<a name="vector-data"></a> **Vektorová data**: souřadnice na základě dat, která je reprezentována jako body, řádky nebo mnohoúhelníky.

<a name="vector-tile"></a> **Vektor, dlaždice**: specifikaci dat pro ukládání geoprostorové vektorová data pomocí stejné systém dlaždic jako mapového ovládacího prvku. Viz také [Dlaždicovou vrstvu](#tile-layer).

<a name="vehicle-routing-problem-vrp"></a> **Vehicle směrování problém (VRP)**: Třída problémů, ve kterých se počítá sadu seřazený trasy za flotilu vozidel při přihlédnutím jako sadu omezujících podmínek. Tato omezení může obsahovat věci, jako je doručování časových oken, více kapacit trasy a cestovní omezení doby trvání.

<a name="voronoi-diagram"></a> **Voronoi diagram**: rozdělení prostoru do oblastí nebo buňky, obklopené sadu geometrické objekty (obvykle funkce bodu). Tyto buňky nebo mnohoúhelníky, musí splňovat kritéria pro Delaunay trojúhelníky. Všechna umístění v rámci oblasti jsou podrobněji na objekt, který ji obklopuje než druhý objekt v sadě. Diagramy Voronoi se často používají od sebe odděluje oblasti vliv kolem geografické funkce. 

## <a name="w"></a>W

<a name="waypoint"></a> **Bod na trase**: bod na trase je zadaný zeměpisné umístění určené zeměpisnou délku a šířku, která se používá pro účely navigace. Často se používá k reprezentování bod, ve kterém uživatel přejde směrovat přes.

<a name="waypoint-optimization"></a> **Bod na trase optimalizace**: proces sadu waypoints minimalizovat dobu trvání cesty nebo vzdálenost vyžaduje, aby procházel všechny zadané waypoints změnu uspořádání. Označovaný také jako [Traveling obchodníků problém](#traveling-salesmen-problem-tsp) nebo [vozidla směrování problém](#vehicle-routing-problem-vrp) záleží na složitosti optimalizace.

<a name="web-map-service-wms"></a> **Webovou službu mapy (WMS)**: WMS je standard Open geografické W3c (OGC), který definuje bitové mapy služeb. WMS služby poskytují obrázky mapy pro konkrétní oblasti v mapě na vyžádání. Image zahrnout předem vykreslená Symbologie a může být vykreslen v jednom z několika pojmenované styly definovány službou.

<a name="web-mercator"></a> **Web Mercator**: označované také jako ke Kulovému projekci Mercator je mírné varianta projekci Mercator, jeden používá primárně v aplikacích založených na webu mapování. Používá stejné vzorce jako standardní projekci Mercator jako používané pro rozsáhlé mapy. Ale Web Mercator používá ke kulovému vzorce vůbec škáluje, zatímco ve velkém měřítku Mercator obvykle mapuje formulář elipsoidním projekce. Rozdíl je nepostřehnutelný v globálním měřítku, ale způsobí, že mapování z místní oblasti, které mírně liší od hodnotu true, elipsoidním Mercator mapuje na stejném měřítku.

<a name="wgs84"></a> **WGS84**: sadu konstanty používané k umístění na ploše na mapě se týkají prostorových souřadnice. WGS84 datum je standardní používají nejvíce online mapování poskytovatele a GPS zařízení. Použití Azure Maps [EPSG:3857](https://epsg.io/3857) variantu systém souřadnic odkaz WGS84.

## <a name="z"></a>Z

<a name="z-coordinate"></a> **Souřadnice Z**: viz [výška](#altitude). 

<a name="zip-code"></a> **PSČ**: viz [Postal code](#postal-code).

<a name="Zoom level"></a> **Úroveň zvětšení**: Určuje úroveň podrobností a jak velká část mapy je viditelný. Při oddálení úplně úroveň 0, mapu světa úplné často se bude v zobrazení, ale budou vidět omezenou podrobnosti, jako jsou názvy země a ohraničení a jeho jména na oceánských. Při přiblížení blíže na úroveň 17, zobrazí na mapě oblasti několik bloků město silniční podrobné informace. Další informace najdete v tématu [úrovně přiblížení a mřížka dlaždic](zoom-levels-and-tile-grid.md) dokumentaci.

