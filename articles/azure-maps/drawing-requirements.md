---
title: Požadavky na balíček pro vykreslování v Azure Maps Creator
description: Přečtěte si o požadavcích na balíček pro vykreslování k převedení souborů návrhu zařízení na mapování dat pomocí služby konverze Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 6/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philMea
ms.openlocfilehash: af7238ca4229bac678061c742f13953299a96ba4
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89290017"
---
# <a name="drawing-package-requirements"></a>Požadavky balíčku pro kreslení

[Služba Azure Maps Conversion](https://docs.microsoft.com/rest/api/maps/conversion) umožňuje převést nahrané balíčky výkresu na data mapy. Tento článek popisuje požadavky balíčku pro vykreslování pro rozhraní API pro převod. Pokud chcete zobrazit ukázkový balíček, můžete si stáhnout vzorový [balíček pro kreslení](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

## <a name="prerequisites"></a>Požadavky

Balíček pro kreslení obsahuje kresby uložené ve formátu DWG, což je nativní formát souboru pro software Autodesk® pro AutoCAD, a [ochranná známka společnosti Autodesk, Inc](https://www.autodesk.com/company/legal-notices-trademarks/trademarks/guidelines-for-use#section12).

Můžete zvolit libovolný software CAD pro vytváření kreseb v balíčku pro kreslení.  

[Služba konverze Azure Maps](https://docs.microsoft.com/rest/api/maps/conversion) převede balíček pro vykreslování na data mapy.  Převodní služba byla vyvinuta a testována pomocí formátu souboru DWG AutoCADu. `AC1032` je interní formát verze souborů DWG. Doporučujeme, abyste vybrali `AC1032` interní verzi formátu souboru DWG.  

Glosář termínů používaných v tomto dokumentu.

| Označení  | Definice |
|:-------|:------------|
| Vrstva | Vrstva DWG AutoCADu.|
| Úroveň | Oblast budovy se zvýšenou úrovní oprávnění. Například podlaha budovy. |
| Odkazy XREF  |Soubor ve formátu DWG AutoCADu (. dwg) připojený k primárnímu výkresu jako externí odkaz.  |
| Příznak | Objekt, který kombinuje geometrii s dalšími informacemi o metadatech. |
| Třídy funkcí | Běžný podrobný plán pro funkce. Například jednotka je třída funkce a kancelář je funkce. |

## <a name="drawing-package-structure"></a>Vykreslování struktury balíčku

Balíček pro kreslení je archiv zip, který obsahuje následující soubory:

* Soubory DWG ve formátu souborů DWG AutoCADu
* _manifest.jsv_ souboru pro jedno zařízení.

Soubory DWG mohou být v rámci složky uspořádány jakýmkoli způsobem, ale soubor manifestu musí být v kořenovém adresáři složky. Složka musí být zip v jednom souboru archivu s příponou. zip. Následující části podrobně popisují požadavky na soubory DWG, soubor manifestu a obsah těchto souborů.  

## <a name="dwg-files-requirements"></a>Požadavky na soubory DWG

Pro každou úroveň zařízení je vyžadován jeden soubor DWG. Data úrovně musí být obsažena v jednom souboru DWG. Všechny externí odkazy (_xrefs_) musí být svázané s nadřazeným vykreslováním. Navíc každý soubor DWG:

* Je nutné definovat vrstvu na _vnějších_ a _jednotkách_ . Volitelně může definovat následující volitelné vrstvy: _zeď_, _dveře_, _UnitLabel_, _zóna_a _ZoneLabel_.
* Nesmí obsahovat funkce z více úrovní.
* Nesmí obsahovat funkce z více zařízení.

[Služba Azure Maps Conversion](https://docs.microsoft.com/rest/api/maps/conversion) může extrahovat následující třídy funkcí ze souboru DWG:

* Úrovně
* Jednotky
* Zóny
* Volná
* Wall
* Svislé průniky

Všechny úlohy převodu mají za následek minimální sadu výchozích kategorií: místnost, struktura. zeď, otevření. dveře, zóna a zařízení. Další kategorie jsou pro každý název kategorie, na který odkazují objekty.  

Vrstva DWG musí obsahovat funkce jedné třídy. Třídy nesmí sdílet vrstvu. Například jednotky a zdi nemůžou sdílet vrstvu.

Vrstvy DWG musí také splňovat následující kritéria:

* Počátek kreseb pro všechny soubory DWG se musí Zarovnat ke stejné zeměpisné šířce a délce.
* Každá úroveň musí být ve stejné orientaci jako ostatní úrovně.
* Mnohoúhelníky s automatickým průnikem budou automaticky opraveny a [služba Azure Maps Conversion Service](https://docs.microsoft.com/rest/api/maps/conversion) vyvolá upozornění. Doporučujeme ručně zkontrolovat opravené výsledky, protože neodpovídají očekávaným výsledkům.

Všechny entity vrstvy musí být jedním z následujících typů: line, lomená, mnohoúhelník, kruhový oblouk, Circle, text (single line). Všechny ostatní typy entit budou ignorovány.

Následující tabulka popisuje podporované typy entit a podporované funkce pro každou vrstvu. Pokud vrstva obsahuje nepodporované typy entit, bude [služba Azure Maps Conversion](https://docs.microsoft.com/rest/api/maps/conversion) tyto entity ignorovat.  

| Vrstva | Typy entit | Funkce |
| :----- | :-------------------| :-------
| [Zpětný](#exterior-layer) | Mnohoúhelník, lomená (uzavřená), kruh | Úrovně
| [Jednotka](#unit-layer) |  Mnohoúhelník, lomená (uzavřená), kruh | Svislé průniky, jednotky
| [Zásuvky](#wall-layer)  | Mnohoúhelník, lomená (uzavřená), kruh | Neužívá se. Další informace najdete v tématu [vrstva zdi](#wall-layer).
| [Dveře](#door-layer) | Mnohoúhelník, Lomená čára, čára, CircularArc, kruh | Volná
| [Zóna](#zone-layer) | Mnohoúhelník, lomená (uzavřená), kruh | Zóna
| [UnitLabel](#unitlabel-layer) | Text (jednořádkový řádek) | Neužívá se. Tato vrstva může přidat vlastnosti pouze do funkcí jednotek z vrstvy jednotek. Další informace naleznete na [UnitLabel vrstvě](#unitlabel-layer).
| [ZoneLabel](#zonelabel-layer) | Text (jednořádkový řádek) | Neužívá se. Tato vrstva může do funkcí zón přidávat pouze vlastnosti z ZonesLayer. Další informace najdete v tématu [ZoneLabel vrstva](#zonelabel-layer) .

Další části obsahují podrobnosti o požadavcích na jednotlivé vrstvy.

### <a name="exterior-layer"></a>Vnější vrstva

Soubor DWG pro každou úroveň musí obsahovat vrstvu pro definování hraničního obvodu úrovně. Tato vrstva je označována jako vnější vrstva. Pokud například zařízení obsahuje dvě úrovně, musí mít dva soubory DWG s vnější vrstvou pro každý soubor.

V případě, že je mnoho kreseb entit v vnější vrstvě, bude [Výsledná datová sada](tutorial-creator-indoor-maps.md#create-a-feature-stateset) pro jednotlivé soubory DWG obsahovat pouze **jednu** funkci úrovně. Dále:

* Vnější musí být vykresleny jako mnohoúhelník, lomená (uzavřená), kruhová.

* Vnější mohou překrývat, ale budou překryty na jednu geometrii.

Pokud vrstva obsahuje více překrývajících se lomených čar, pak budou lomené čáry přeřešeny na funkci s jednou úrovní. Případně, pokud vrstva obsahuje více non_overlappingch lomených čar, bude výsledná funkce vystupovat s více mnohoúhelníky.

Příkladem vnější vrstvy lze zobrazit jako vrstvu OBRYSu v [ukázkovém balíčku pro kreslení](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="unit-layer"></a>Vrstva jednotek

Soubor DWG pro každou úroveň by měl definovat vrstvu obsahující jednotky.  Jednotky jsou naviguje mezery v budově, například pobočky, předsálí, schodiště a výtahy. Vrstva jednotek by měla splňovat následující požadavky:

* Jednotky musí být vykresleny jako mnohoúhelník, lomená (uzavřená), kruhová.
* Jednotky musí klesnout do hranic vnějšího hraničního zařízení.
* Jednotky nesmí být částečně překryty.
* Jednotky nesmí obsahovat žádnou neprotínající geometrii sebe.

 Pojmenujte jednotku tak, že vytvoříte textový objekt ve vrstvě _unitLabel_ a pak umístíte objekt dovnitř hranic jednotky. Další informace naleznete na [UnitLabel vrstvě](#unitlabel-layer).

Příkladem vrstvy jednotky lze zobrazit jako vrstvu jednotky v [ukázkovém balíčku pro kreslení](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="wall-layer"></a>Zeď – vrstva

Soubor DWG pro každou úroveň může obsahovat vrstvu, která definuje fyzické rozsahy zdí, sloupců a dalších stavebních struktur.

* Zdi musí být vykreslené jako mnohoúhelník, lomená (uzavřená), kruhová.
* Vrstvy zdí by měly obsahovat pouze geometrii, která je interpretována jako sestavování struktury.

Jako vrstvu ZDÍ v [ukázkovém balíčku kreslení](https://github.com/Azure-Samples/am-creator-indoor-data-examples)se může zobrazit příklad vrstvy zdí.

### <a name="door-layer"></a>Vrstva dveří

Můžete zahrnout vrstvu DWG obsahující dveře. Každá dvířka musí překrývat okraje jednotky z vrstvy jednotky.

Otvory ve Azure Maps datové sadě jsou reprezentovány jako jednořádkový segment, který překrývá více hranic jednotek. Následující kroky jsou provedeny pro převod geometrie ve vrstvě dveří na otevírání funkcí v datové sadě.

![Postup generování otvírání](./media/drawing-requirements/opening-steps.png)

### <a name="zone-layer"></a>Vrstva zóny

Soubor DWG pro každou úroveň může obsahovat vrstvu zóny, která definuje fyzické rozsahy zón. Zónou může být vnitřní prázdný prostor nebo zadní loděnice.

* Zóny musí být vykresleny jako mnohoúhelník, lomená (uzavřená), kruhová.
* Zóny se mohou překrývat.
* Zóny můžou být uvnitř vnějšího hraničního zařízení nebo mimo něj.

Pojmenujte zónu vytvořením textového objektu ve vrstvě _zoneLabel_ a umístěte textový objekt do hranic zóny. Další informace naleznete v tématu [ZoneLabel Layer](#zonelabel-layer).

Příkladem vrstvy zón se dá v [ukázkovém balíčku pro vykreslování](https://github.com/Azure-Samples/am-creator-indoor-data-examples)zobrazit jako vrstva zón.

### <a name="unitlabel-layer"></a>UnitLabel vrstva

Soubor DWG pro každou úroveň může obsahovat vrstvu popisku jednotky. Vrstva popisku jednotky přidá vlastnost název k jednotkám extrahovaných z vrstvy jednotky. Jednotky s vlastností name mohou mít další podrobnosti zadané v souboru manifestu.

* Popisky jednotek musí být textové entity s jedním řádkem.
* Štítky jednotek musí být uvnitř hranic jejich jednotky.
* Jednotky nesmí obsahovat více textových entit ve vrstvě popisků jednotek.

Příkladem vrstvy UnitLabel lze zobrazit jako vrstvu UNITLABELS v [ukázkovém balíčku pro kreslení](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="zonelabel-layer"></a>ZoneLabel vrstva

Soubor DWG pro každou úroveň může obsahovat vrstvu popisku zóny. Tato vrstva přidá vlastnost Name do zón extrahovaných z vrstvy zóny. Zóny s vlastností name mohou mít další podrobnosti, které jsou uvedeny v souboru manifestu.

* Popisky zón musí být textové entity s jedním řádkem.
* Popisky zón se musí nacházet uvnitř hranic jejich zóny.
* Zóny nesmí obsahovat více textových entit ve vrstvě popisků zóny.

Příkladem vrstvy Zonelabel lze zobrazit jako vrstvu ZONELABELS v [ukázkovém balíčku pro kreslení](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

## <a name="manifest-file-requirements"></a>Požadavky souboru manifestu

Složka zip musí obsahovat soubor manifestu na kořenové úrovni adresáře a soubor musí mít název **manifest.js**. Popisuje soubory DWG, aby [Služba konverze Azure Maps](https://docs.microsoft.com/rest/api/maps/conversion) mohla analyzovat svůj obsah. Budou ingestovat pouze soubory identifikované manifestem. Soubory, které jsou ve složce zip, ale nejsou správně uvedeny v manifestu, budou ignorovány.

Cesty k souborům v objektu **buildingLevels** souboru manifestu musí být relativní ke kořenu složky zip. Název souboru DWG se musí přesně shodovat s názvem úrovně zařízení. Například soubor DWG pro úroveň "Basement" by byl "Basement. DWG". Soubor DWG pro úroveň 2 bude pojmenován jako "level_2. DWG". Použijte podtržítko, pokud má název úrovně mezeru.

I když jsou k dispozici požadavky při použití objektů manifestu, nejsou všechny objekty požadovány. V následující tabulce jsou uvedeny povinné a volitelné objekty pro verzi 1,1 [služby Azure Maps Conversion](https://docs.microsoft.com/rest/api/maps/conversion).

| Objekt | Povinné | Popis |
| :----- | :------- | :------- |
| verze | true |Verze schématu manifestu V současné době je podporována pouze verze 1,1.|
| directoryInfo | true | Popisuje geografické a kontaktní informace o zařízení. Dá se použít i k vytvoření osnovy geografických a kontaktních údajů pro cestující. |
| buildingLevels | true | Určuje úrovně budov a soubory, které obsahují návrh úrovní. |
| informace o mikroodkazech | true | Obsahuje číselné geografické informace pro kreslení zařízení. |
| dwgLayers | true | Obsahuje seznam názvů vrstev a každá vrstva obsahuje názvy svých vlastních funkcí. |
| unitProperties | false (nepravda) | Dá se použít k vložení dalších metadat pro funkce jednotek. |
| zoneProperties | false (nepravda) | Dá se použít k vložení dalších metadat pro funkce zóny. |

Další části obsahují podrobnosti o požadavcích na jednotlivé objekty.

### <a name="directoryinfo"></a>directoryInfo

| Vlastnost  | typ | Povinné | Popis |
|-----------|------|----------|-------------|
| name      | řetězec | true   |  Název budovy |
| streetAddress|    řetězec |    false (nepravda)    | Adresa sestavení. |
|unit     | řetězec    |  false (nepravda)    |  Jednotka v budově. |
| polohu |    řetězec |    false (nepravda) |    Název oblasti, okolí nebo oblasti. Například "přejezera" nebo "střední oblast". Nejedná se o část poštovní adresy. |
| adminDivisions |    Pole JSON řetězců |    false (nepravda)     | Pole, které obsahuje označení adresy (země, stát, město) nebo (země, Prefektura, City, město). Použijte kódy zemí ISO 3166 a kódy států ISO 3166-2/oblasti. |
| Ovládacím |    řetězec    | false (nepravda)    | Kód pro řazení pošty |
| hoursOfOperation |    řetězec |     false (nepravda) | Dodržuje formát [osm otevírací doba](https://wiki.openstreetmap.org/wiki/Key:opening_hours/specification) . |
| Android    | řetězec |    false (nepravda) |    Telefonní číslo přidružené k budově Musí zahrnovat kód země. |
| webu    | řetězec |    false (nepravda)    | Web přidružený k budově M zahajte protokolem HTTP nebo HTTPS. |
| nonPublic |    bool    | false (nepravda) | Příznak určující, zda je sestavení otevřeno veřejnému. |
| anchorLatitude | numerické |    false (nepravda) | Zeměpisná šířka kotvy zařízení (připínáček). |
| anchorLongitude | numerické |    false (nepravda) | Zeměpisná délka kotvy zařízení (připínáček). |
| anchorHeightAboveSeaLevel  | numerické | false (nepravda) | Výška uzemněného podlahového zařízení nad úrovní moře v metrech |
| defaultLevelVerticalExtent | numerické | false (nepravda) | Výchozí výška (tloušťka) úrovně tohoto zařízení, která se má použít, když `verticalExtent` je úroveň nedefinovaná. |

### <a name="buildinglevels"></a>buildingLevels

`buildingLevels`Objekt obsahuje pole s JSON úrovněmi budov.

| Vlastnost  | Typ | Vyžadováno | Popis |
|-----------|------|----------|-------------|
|levelName    |řetězec    |true |    Název popisné úrovně Například: patra 1, předsálí, modré zaparkování, Basement a tak dále.|
|řadový | celé číslo |    true | Pořadové číslo se používá k určení svislého pořadí úrovní. Každé zařízení musí mít úroveň s pořadovým číslem 0. |
|heightAboveFacilityAnchor | numerické | false (nepravda) |    Výška úrovně nad kotvou měřičů |
| verticalExtent | numerické | false (nepravda) | Patra na výšku (tloušťka) úrovně v měřičích. |
|filename |    řetězec |    true |    Cesta systému souborů výkresu CAD pro úroveň budovy Musí být relativní ke kořeni souboru ZIP stavebního souboru. |

### <a name="georeference"></a>informace o mikroodkazech

| Vlastnost  | Typ | Vyžadováno | Popis |
|-----------|------|----------|-------------|
|připojí    | numerické |    true |    Desítková reprezentace stupně zeměpisné šířky v počátku kreslení zařízení. Souřadnice zdroje musí být v WGS84 web Mercator ( `EPSG:3857` ).|
|lon    |numerické|    true|    Desítková reprezentace ve stupních délky v počátku kreslení zařízení Souřadnice zdroje musí být v WGS84 web Mercator ( `EPSG:3857` ). |
|angle|    numerické|    true|   Úhel po směru hodinových ručiček ve stupních mezi skutečnou a svislou osou výkresu (Y).   |

### <a name="dwglayers"></a>dwgLayers

| Vlastnost  | Typ | Vyžadováno | Popis |
|-----------|------|----------|-------------|
|zpětný    |Pole řetězců|    true|    Názvy vrstev, které definují vnější profil budovy.|
|unit|    Pole řetězců|    true|    Názvy vrstev, které definují jednotky.|
|zásuvky|    Pole řetězců    |false (nepravda)|    Názvy vrstev, které definují zdi.|
|dveře    |Pole řetězců|    false (nepravda)   | Názvy vrstev, které definují dvířka|
|unitLabel    |Pole řetězců|    false (nepravda)    |Názvy vrstev, které definují názvy jednotek.|
|zóna | Pole řetězců    | false (nepravda)    | Názvy vrstev, které definují zóny.|
|zoneLabel | Pole řetězců |     false (nepravda) |    Názvy vrstev, které definují názvy zón.|

### <a name="unitproperties"></a>unitProperties

`unitProperties`Objekt obsahuje pole s vlastnostmi jednotky ve formátu JSON.

| Vlastnost  | Typ | Vyžadováno | Popis |
|-----------|------|----------|-------------|
|jednotka    |řetězec    |true    |Název jednotky, která se má přidružit k tomuto `unitProperty` záznamu. Tento záznam je platný pouze v případě, že `unitName` se v vrstvách nachází shodný popisek `unitLabel` . |
|categoryName|    řetězec|    false (nepravda)    |Název kategorie Úplný seznam kategorií najdete v tématu [kategorie](https://aka.ms/pa-indoor-spacecategories). |
|navigableBy| Pole řetězců |    false (nepravda)    |Určuje typy navigace v agentech, které mohou procházet jednotky. Například "chod". Tato vlastnost bude informovat o schopnostech Wayfinding.  Povolené hodnoty jsou `pedestrian` , `wheelchair` ,, `machine` `bicycle` , `automobile` , `hiredAuto` , `bus` , `railcar` , `emergency` ,, a `ferry` `boat` `disallowed` .|
|routeThroughBehavior|    řetězec|    false (nepravda)    |Chování trasy v rámci jednotky. Povolené hodnoty jsou `disallowed` , `allowed` a `preferred` . Výchozí hodnota je `allowed` .|
|cestující    |Pole objektů directoryInfo |false (nepravda)    |Seznam osob pro jednotku. |
|nameAlt|    řetězec|    false (nepravda)|    Alternativní název jednotky |
|nameSubtitle|    řetězec    |false (nepravda)|    Podnadpis jednotky |
|addressRoomNumber|    řetězec|    false (nepravda)|    Číslo místnosti/jednotky/bytu/sady jednotky.|
|verticalPenetrationCategory|    řetězec|    false (nepravda)| Když je tato vlastnost definovaná, výsledná funkce bude vertikální průnik (VRT) místo jednotky. VRTs se dá použít k přechodu na jiné funkce VRT na úrovních výše nebo pod ní. Svislé průniky je název [kategorie](https://aka.ms/pa-indoor-spacecategories) . Je-li tato vlastnost definována, je vlastnost categoryName přepsána pomocí verticalPenetrationCategory. |
|verticalPenetrationDirection|    řetězec|    false (nepravda)    |Pokud `verticalPenetrationCategory` je definován, Volitelně definujte platný směr cesty. Povolené hodnoty jsou `lowToHigh` , `highToLow` , `both` a `closed` . Výchozí hodnota je `both` .|
| nonPublic | bool | false (nepravda) | Určuje, zda je jednotka otevřena veřejnému. |
| isRoutable | bool | false (nepravda) | Když je nastavena na `false` , jednotka nemůže přejít na nebo prostřednictvím. Výchozí hodnota je `true` . |
| isOpenArea | bool | false (nepravda) | Umožňuje navigaci v agentovi zadat jednotku bez nutnosti otevření připojeného k jednotce. Ve výchozím nastavení je tato hodnota nastavena na hodnotu `true` pro jednotky bez otevřených `false` . pro jednotky s otevřenými.  Ručním nastavením na `isOpenArea` `false` hodnotu u jednotky bez jakýchkoli otevřených výsledků se zobrazí upozornění. Důvodem je to, že výsledná jednotka nebude dosažitelná pomocí navigace v agentovi.|

### <a name="the-zoneproperties-object"></a>Objekt zoneProperties

`zoneProperties`Objekt obsahuje pole JSON vlastností zóny.

| Vlastnost  | Typ | Vyžadováno | Popis |
|-----------|------|----------|-------------|
|Název_zóny        |řetězec    |true    |Název zóny, která se má přidružit k `zoneProperty` záznamu Tento záznam je platný pouze v případě, že `zoneName` se v vrstvě zóny nachází shodný popisek `zoneLabel` .  |
|categoryName|    řetězec|    false (nepravda)    |Název kategorie Úplný seznam kategorií najdete v tématu [kategorie](https://aka.ms/pa-indoor-spacecategories). |
|zoneNameAlt|    řetězec|    false (nepravda)    |Alternativní název zóny  |
|zoneNameSubtitle|    řetězec |    false (nepravda)    |Podnadpis zóny |
|zoneSetId|    řetězec |    false (nepravda)    | Nastavte ID pro vytvoření vztahu mezi více zónami, aby se mohly dotazovat nebo vybrat jako skupinu. Například zóny, které přesahují více úrovní. |

### <a name="sample-drawing-package-manifest"></a>Ukázkový manifest balíčku vykreslování

Níže je ukázkový soubor manifestu ukázkového balíčku pro kreslení. Pokud si chcete stáhnout celý balíček, klikněte na [ukázkový balíček pro kreslení](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

#### <a name="manifest-file"></a>Soubor manifestu

```JSON
{
    "version": "1.1", 
    "directoryInfo": { 
        "name": "Contoso Building", 
        "streetAddress": "Contoso Way", 
        "unit": "1", 
        "locality": "Contoso eastside", 
        "postalCode": "98052", 
        "adminDivisions": [ 
            "Contoso city", 
            "Contoso state", 
            "Contoso country" 
        ], 
        "hoursOfOperation": "Mo-Fr 08:00-17:00 open", 
        "phone": "1 (425) 555-1234", 
        "website": "www.contoso.com", 
        "nonPublic": false, 
        "anchorLatitude": 47.636152, 
        "anchorLongitude": -122.132600, 
        "anchorHeightAboveSeaLevel": 1000, 
        "defaultLevelVerticalExtent": 3  
    }, 
    "buildingLevels": { 
        "levels": [ 
            { 
                "levelName": "Basement", 
                "ordinal": -1, 
                "filename": "./Basement.dwg" 
            }, { 
                "levelName": "Ground", 
                "ordinal": 0, 
                "verticalExtent": 5, 
                "filename": "./Ground.dwg" 
            }, { 
                "levelName": "Level 2", 
                "ordinal": 1, 
                "heightAboveFacilityAnchor": 3.5, 
                "filename": "./Level_2.dwg" 
            } 
        ] 
    }, 
    "georeference": { 
        "lat": 47.636152, 
        "lon": -122.132600, 
        "angle": 0 
    }, 
    "dwgLayers": { 
        "exterior": [ 
            "OUTLINE", "WINDOWS" 
        ], 
        "unit": [ 
            "UNITS" 
        ], 
        "wall": [ 
            "WALLS" 
        ], 
        "door": [ 
            "DOORS" 
        ], 
        "unitLabel": [ 
            "UNITLABELS" 
        ], 
        "zone": [ 
            "ZONES" 
        ], 
        "zoneLabel": [ 
            "ZONELABELS" 
        ] 
    }, 
    "unitProperties": [ 
        { 
            "unitName": "B01", 
            "categoryName": "room.office", 
            "navigableBy": ["pedestrian", "wheelchair", "machine"], 
            "routeThroughBehavior": "disallowed", 
            "occupants": [ 
                { 
                    "name": "Joe's Office", 
                    "phone": "1 (425) 555-1234" 
                } 
            ], 
            "nameAlt": "Basement01", 
            "nameSubtitle": "01", 
            "addressRoomNumber": "B01", 
            "nonPublic": true, 
            "isRoutable": true, 
            "isOpenArea": true 
        }, 
        { 
            "unitName": "B02" 
        }, 
        { 
            "unitName": "B05", 
            "categoryName": "room.office" 
        }, 
        { 
            "unitName": "STRB01", 
            "verticalPenetrationCategory": "verticalPenetration.stairs", 
            "verticalPenetrationDirection": "both" 
        }, 
        { 
            "unitName": "ELVB01", 
            "verticalPenetrationCategory": "verticalPenetration.elevator", 
            "verticalPenetrationDirection": "high_to_low" 
        } 
    ], 
    "zoneProperties": 
    [ 
        { 
            "zoneName": "WifiB01", 
            "categoryName": "Zone", 
            "zoneNameAlt": "MyZone", 
            "zoneNameSubtitle": "Wifi", 
            "zoneSetId": "1234" 
        }, 
        { 
            "zoneName": "Wifi101",
            "categoryName": "Zone",
            "zoneNameAlt": "MyZone",
            "zoneNameSubtitle": "Wifi",
            "zoneSetId": "1234"
        }
    ]
}
```

## <a name="next-steps"></a>Další kroky

Jakmile balíček pro vykreslování splňuje požadavky, můžete použít [službu Azure Maps Conversion](https://docs.microsoft.com/rest/api/maps/conversion) k převedení balíčku na datovou sadu mapy. Pak můžete použít datovou sadu k vygenerování mapy interiéru pomocí modulu mapy Vnitřníchy. Další informace o používání modulu mapy Vnitřníchy najdete v následujících článcích:

> [!div class="nextstepaction"]
>[Autor pro mapy vnitřníchy](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Kurz: vytvoření mapy interiéru autora](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Dynamické stylování map v interiéru](indoor-map-dynamic-styling.md)
