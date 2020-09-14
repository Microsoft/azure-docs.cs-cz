---
title: Požadavky na balíček pro vykreslování v Azure Maps Creator
description: Informace o požadavcích na balíček pro vykreslování k převedení souborů návrhu zařízení na mapování dat
author: anastasia-ms
ms.author: v-stharr
ms.date: 6/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philMea
ms.openlocfilehash: 1f25aadf716b7768b6122a4fb165466aef7f8a16
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2020
ms.locfileid: "90053388"
---
# <a name="drawing-package-requirements"></a>Požadavky balíčku pro kreslení

Nahrané balíčky výkresu můžete převést na data mapy pomocí [služby Azure Maps Conversion Service](https://docs.microsoft.com/rest/api/maps/conversion). Tento článek popisuje požadavky balíčku pro vykreslování pro rozhraní API pro převod. Pokud chcete zobrazit ukázkový balíček, můžete si stáhnout vzorový [balíček pro kreslení](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

## <a name="prerequisites"></a>Požadavky

Balíček pro kreslení obsahuje kresby uložené ve formátu DWG, což je formát nativního souboru pro software Autodesk pro® pro AutoCAD.

Můžete vybrat libovolný software CAD a vykreslit kresby v balíčku pro kreslení.  

[Služba konverze Azure Maps](https://docs.microsoft.com/rest/api/maps/conversion) převede balíček pro vykreslování na data mapy. Služba převodu funguje ve formátu souboru DWG AutoCADu. `AC1032` je interní formát verze souborů DWG a je vhodné zvolit `AC1032` interní verzi formátu souboru DWG.  

## <a name="glossary-of-terms"></a>Glosář termínů

Tady jsou některé pojmy a definice, které jsou důležité při čtení tohoto článku.

| Označení  | Definice |
|:-------|:------------|
| Vrstva | Vrstva DWG AutoCADu.|
| Úroveň | Oblast budovy se zvýšenou úrovní oprávnění. Například podlaha budovy. |
| Odkazy XREF  |Soubor ve formátu DWG AutoCADu (. dwg), připojený k primárnímu výkresu jako externí odkaz.  |
| Příznak | Objekt, který kombinuje geometrii s dalšími informacemi o metadatech. |
| Třídy funkcí | Běžný podrobný plán pro funkce. Například *jednotka* je třída funkce a *kancelář* je funkce. |

## <a name="drawing-package-structure"></a>Vykreslování struktury balíčku

Balíček pro kreslení je archiv zip, který obsahuje následující soubory:

* Soubory DWG ve formátu souborů DWG AutoCADu
* _manifest.jsv_ souboru pro jedno zařízení.

Soubory DWG můžete uspořádat jakýmkoli způsobem uvnitř složky, ale soubor manifestu musí být v kořenovém adresáři složky. Složku je nutné zálohovat v jednom souboru archivu s příponou. zip. Následující části podrobně popisují požadavky na soubory DWG, soubor manifestu a obsah těchto souborů.  

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
* Mnohoúhelníky s automatickým průnikem jsou automaticky opraveny a [služba Azure Maps Conversion](https://docs.microsoft.com/rest/api/maps/conversion) vyvolá upozornění. Opravené výsledky byste měli zkontrolovat ručně, protože nemusí odpovídat očekávaným výsledkům.

Všechny entity vrstvy musí být jedním z následujících typů: line, lomená, mnohoúhelník, kruhový oblouk, Circle nebo text (jeden řádek). Všechny ostatní typy entit jsou ignorovány.

Následující tabulka popisuje podporované typy entit a podporované funkce pro každou vrstvu. Pokud vrstva obsahuje nepodporované typy entit, [Služba konverze Azure Maps](https://docs.microsoft.com/rest/api/maps/conversion) ignoruje tyto entity.  

| Vrstva | Typy entit | Funkce |
| :----- | :-------------------| :-------
| [Zpětný](#exterior-layer) | Mnohoúhelník, lomená (uzavřená), kruh | Úrovně
| [Jednotka](#unit-layer) |  Mnohoúhelník, lomená (uzavřená), kruh | Svislé průniky, jednotky
| [Zásuvky](#wall-layer)  | Mnohoúhelník, lomená (uzavřená), kruh | Neužívá se. Další informace najdete v tématu [vrstva zdi](#wall-layer).
| [Dveře](#door-layer) | Mnohoúhelník, Lomená čára, čára, CircularArc, kruh | Volná
| [Zóna](#zone-layer) | Mnohoúhelník, lomená (uzavřená), kruh | Zóna
| [UnitLabel](#unitlabel-layer) | Text (jednořádkový řádek) | Neužívá se. Tato vrstva může přidat vlastnosti pouze do funkcí jednotek z vrstvy jednotek. Další informace naleznete na [UnitLabel vrstvě](#unitlabel-layer).
| [ZoneLabel](#zonelabel-layer) | Text (jednořádkový řádek) | Neužívá se. Tato vrstva může do funkcí zón přidávat pouze vlastnosti z ZonesLayer. Další informace naleznete na [ZoneLabel vrstvě](#zonelabel-layer).

Další části obsahují podrobnosti o požadavcích na jednotlivé vrstvy.

### <a name="exterior-layer"></a>Vnější vrstva

Soubor DWG pro každou úroveň musí obsahovat vrstvu pro definování hraničního obvodu úrovně. Tato vrstva je označována jako *vnější* vrstva. Pokud například zařízení obsahuje dvě úrovně, musí mít dva soubory DWG s vnější vrstvou pro každý soubor.

Bez ohledu na to, kolik kreseb entit je v vnější vrstvě, bude [Výsledná datová sada](tutorial-creator-indoor-maps.md#create-a-feature-stateset) pro každý soubor DWG obsahovat jenom jednu funkci úrovně. Dále:

* Vnější musí být vykresleny jako mnohoúhelník, lomená (uzavřená) nebo KRUHOVÁ.
* Vnější lze překrývat, ale jsou rozdálené na jednu geometrii.

Pokud vrstva obsahuje více překrývajících se čar, jsou lomené řádky rozstupné na funkci s jednou úrovní. Případně, pokud vrstva obsahuje více překrývajících se čar, funkce Výsledná úroveň má více mnohoúhelníkových reprezentací.

Jako vrstvu obrysu v [ukázkovém balíčku kreslení](https://github.com/Azure-Samples/am-creator-indoor-data-examples)vidíte příklad vnější vrstvy.

### <a name="unit-layer"></a>Vrstva jednotek

Soubor DWG pro každou úroveň definuje vrstvu obsahující jednotky. Jednotky jsou naviguje mezery v budově, například pobočky, předsálí, schodiště a výtahy. Vrstva jednotek by měla splňovat následující požadavky:

* Jednotky musí být vykresleny jako mnohoúhelník, lomená (uzavřená) nebo KRUHOVÁ.
* Jednotky musí klesnout do hranic vnějšího hraničního zařízení.
* Jednotky nesmí být částečně překryty.
* Jednotky nesmí obsahovat žádnou neprotínající geometrii sebe.

Pojmenujte jednotku tak, že vytvoříte textový objekt ve vrstvě UnitLabel a umístíte objekt do hranic jednotky. Další informace naleznete na [UnitLabel vrstvě](#unitlabel-layer).

Můžete vidět příklad vrstvy jednotky v [ukázkovém balíčku pro kreslení](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="wall-layer"></a>Zeď – vrstva

Soubor DWG pro každou úroveň může obsahovat vrstvu, která definuje fyzické rozsahy zdí, sloupců a dalších stavebních struktur.

* Zdi musí být vykreslené jako mnohoúhelník, lomená (uzavřená) nebo KRUHOVÁ.
* Vrstva nebo vrstvy zdi by měly obsahovat pouze geometrii, která je interpretována jako sestavování struktury.

V [ukázkovém balíčku kreslení](https://github.com/Azure-Samples/am-creator-indoor-data-examples)vidíte příklad vrstvy zdí.

### <a name="door-layer"></a>Vrstva dveří

Můžete zahrnout vrstvu DWG, která obsahuje dveře. Každá dvířka musí překrývat okraje jednotky z vrstvy jednotky.

Otevírající se dvířka v datové sadě Azure Maps jsou reprezentována jako jednořádkový segment, který překrývá více hranic jednotek. Následující obrázky ukazují, jak převést geometrii ve vrstvě dveří na otevírání funkcí v datové sadě.

![Čtyři grafické prvky, které znázorňují kroky pro generování otevřených objektů](./media/drawing-requirements/opening-steps.png)

### <a name="zone-layer"></a>Vrstva zóny

Soubor DWG pro každou úroveň může obsahovat vrstvu zóny, která definuje fyzické rozsahy zón. Zónou může být vnitřní prázdný prostor nebo zadní loděnice.

* Zóny musí být vykresleny jako mnohoúhelník, lomená (uzavřená) nebo KRUHOVÁ.
* Zóny se můžou překrývat.
* Zóny můžou náležet do vnějšího hraničního zařízení nebo mimo něj.

Pojmenujte zónu vytvořením textového objektu ve vrstvě ZoneLabel a umístěte textový objekt do hranic zóny. Další informace naleznete v tématu [ZoneLabel Layer](#zonelabel-layer).

V [ukázkovém balíčku kreslení](https://github.com/Azure-Samples/am-creator-indoor-data-examples)vidíte příklad vrstvy zóny.

### <a name="unitlabel-layer"></a>UnitLabel vrstva

Soubor DWG pro každou úroveň může obsahovat UnitLabel vrstvu. Vrstva UnitLabel přidá vlastnost Name na jednotky extrahované z vrstvy jednotky. Jednotky s vlastností name mohou mít další podrobnosti zadané v souboru manifestu.

* Popisky jednotek musí být textové entity s jedním řádkem.
* Štítky jednotek musí být uvnitř hranic jejich jednotky.
* Jednotky nesmí obsahovat více textových entit ve vrstvě UnitLabel.

V [ukázkovém balíčku kreslení](https://github.com/Azure-Samples/am-creator-indoor-data-examples)vidíte příklad vrstvy UnitLabel.

### <a name="zonelabel-layer"></a>ZoneLabel vrstva

Soubor DWG pro každou úroveň může obsahovat ZoneLabel vrstvu. Tato vrstva přidá vlastnost Name do zón extrahovaných z vrstvy zóny. Zóny s vlastností name mohou mít další podrobnosti, které jsou uvedeny v souboru manifestu.

* Popisky zón musí být textové entity s jedním řádkem.
* Popisky zón se musí nacházet uvnitř hranic jejich zóny.
* Zóny nesmí obsahovat více textových entit ve vrstvě ZoneLabel.

V [ukázkovém balíčku kreslení](https://github.com/Azure-Samples/am-creator-indoor-data-examples)vidíte příklad vrstvy ZoneLabel.

## <a name="manifest-file-requirements"></a>Požadavky souboru manifestu

Složka zip musí obsahovat soubor manifestu na kořenové úrovni adresáře a soubor musí mít název **manifest.js**. Popisuje soubory DWG, aby [Služba konverze Azure Maps](https://docs.microsoft.com/rest/api/maps/conversion) mohla analyzovat svůj obsah. Ingestují se jenom soubory identifikované manifestem. Soubory, které jsou ve složce zip, ale nejsou správně uvedeny v manifestu, jsou ignorovány.

Cesty k souborům v `buildingLevels` objektu souboru manifestu musí být relativní ke kořenu složky zip. Název souboru DWG se musí přesně shodovat s názvem úrovně zařízení. Například soubor DWG pro úroveň "Basement" je "Basement. DWG". Soubor DWG pro úroveň 2 se jmenuje "level_2. DWG". Použijte podtržítko, pokud má název úrovně mezeru.

I když jsou k dispozici požadavky při použití objektů manifestu, nejsou všechny objekty požadovány. V následující tabulce jsou uvedeny povinné a volitelné objekty pro verzi 1,1 [služby Azure Maps Conversion](https://docs.microsoft.com/rest/api/maps/conversion).

| Objekt | Povinné | Popis |
| :----- | :------- | :------- |
| `version` | true |Verze schématu manifestu V současné době je podporována pouze verze 1,1.|
| `directoryInfo` | true | Popisuje geografické a kontaktní informace o zařízení. Dá se použít i k vytvoření osnovy geografických a kontaktních údajů pro cestující. |
| `buildingLevels` | true | Určuje úrovně budov a soubory, které obsahují návrh úrovní. |
| `georeference` | true | Obsahuje číselné geografické informace pro kreslení zařízení. |
| `dwgLayers` | true | Obsahuje seznam názvů vrstev a každá vrstva obsahuje názvy svých vlastních funkcí. |
| `unitProperties` | false (nepravda) | Dá se použít k vložení dalších metadat pro funkce jednotek. |
| `zoneProperties` | false (nepravda) | Dá se použít k vložení dalších metadat pro funkce zóny. |

Další části obsahují podrobnosti o požadavcích na jednotlivé objekty.

### `directoryInfo`

| Vlastnost  | Typ | Vyžadováno | Popis |
|-----------|------|----------|-------------|
| `name`      | řetězec | true   |  Název budovy |
| `streetAddress`|    řetězec |    false (nepravda)    | Adresa sestavení. |
|`unit`     | řetězec    |  false (nepravda)    |  Jednotka v budově. |
| `locality` |    řetězec |    false (nepravda) |    Název oblasti, okolí nebo oblasti. Například "přejezera" nebo "střední oblast". Nejedná se o část poštovní adresy. |
| `adminDivisions` |    Pole JSON řetězců |    false (nepravda)     | Pole, které obsahuje označení adresy (země, stát, město) nebo (země, Prefektura, City, město). Použijte kódy zemí ISO 3166 a kódy států ISO 3166-2/oblasti. |
| `postalCode` |    řetězec    | false (nepravda)    | Kód pro řazení pošty |
| `hoursOfOperation` |    řetězec |     false (nepravda) | Dodržuje formát [osm otevírací doba](https://wiki.openstreetmap.org/wiki/Key:opening_hours/specification) . |
| `phone`    | řetězec |    false (nepravda) |    Telefonní číslo přidružené k budově Musí zahrnovat kód země. |
| `website`    | řetězec |    false (nepravda)    | Web přidružený k budově Musí začínat http nebo HTTPS. |
| `nonPublic` |    bool    | false (nepravda) | Příznak určující, zda je sestavení otevřeno veřejnému. |
| `anchorLatitude` | numerické |    false (nepravda) | Zeměpisná šířka kotvy zařízení (připínáček). |
| `anchorLongitude` | numerické |    false (nepravda) | Zeměpisná délka kotvy zařízení (připínáček). |
| `anchorHeightAboveSeaLevel`  | numerické | false (nepravda) | Výška uzemněného podlahového zařízení nad úrovní moře v metrech |
| `defaultLevelVerticalExtent` | numerické | false (nepravda) | Výchozí výška (tloušťka) úrovně tohoto zařízení, která se má použít, když `verticalExtent` je úroveň nedefinovaná. |

### `buildingLevels`

`buildingLevels`Objekt obsahuje pole s JSON úrovněmi budov.

| Vlastnost  | Typ | Vyžadováno | Popis |
|-----------|------|----------|-------------|
|`levelName`    |řetězec    |true |    Název popisné úrovně Například: Floor 1, předsálí, Blue parkování nebo Basement.|
|`ordinal` | celé číslo |    true | Určuje svislé pořadí úrovní. Každé zařízení musí mít úroveň s pořadovým číslem 0. |
|`heightAboveFacilityAnchor` | numerické | false (nepravda) |    Výška úrovně nad kotvou měřičů |
| `verticalExtent` | numerické | false (nepravda) | Výška podlahy na strop (tloušťka) úrovně v metrech. |
|`filename` |    řetězec |    true |    Cesta systému souborů výkresu CAD pro úroveň budovy Musí být relativní ke kořeni souboru ZIP stavebního souboru. |

### `georeference`

| Vlastnost  | Typ | Vyžadováno | Popis |
|-----------|------|----------|-------------|
|`lat`    | numerické |    true |    Desítková reprezentace stupně zeměpisné šířky v počátku kreslení zařízení. Souřadnice zdroje musí být v WGS84 web Mercator ( `EPSG:3857` ).|
|`lon`    |numerické|    true|    Desítková reprezentace ve stupních délky v počátku kreslení zařízení Souřadnice zdroje musí být v WGS84 web Mercator ( `EPSG:3857` ). |
|`angle`|    numerické|    true|   Úhel po směru hodinových ručiček ve stupních mezi skutečnou a svislou osou výkresu (Y).   |

### `dwgLayers`

| Vlastnost  | Typ | Vyžadováno | Popis |
|-----------|------|----------|-------------|
|`exterior`    |pole řetězců|    true|    Názvy vrstev, které definují vnější profil budovy.|
|`unit`|    pole řetězců|    true|    Názvy vrstev, které definují jednotky.|
|`wall`|    pole řetězců    |false (nepravda)|    Názvy vrstev definujících zdi.|
|`door`    |pole řetězců|    false (nepravda)   | Názvy vrstev, které definují dvířka|
|`unitLabel`    |pole řetězců|    false (nepravda)    |Názvy vrstev, které definují názvy jednotek.|
|`zone` | pole řetězců    | false (nepravda)    | Názvy vrstev definujících zóny.|
|`zoneLabel` | pole řetězců |     false (nepravda) |    Názvy vrstev, které definují názvy zón.|

### `unitProperties`

`unitProperties`Objekt obsahuje pole s vlastnostmi jednotky ve formátu JSON.

| Vlastnost  | Typ | Vyžadováno | Popis |
|-----------|------|----------|-------------|
|`unitName`    |řetězec    |true    |Název jednotky, která se má přidružit k tomuto `unitProperty` záznamu. Tento záznam je platný pouze v případě, že `unitName` se v vrstvách nachází shodný popisek `unitLabel` . |
|`categoryName`|    řetězec|    false (nepravda)    |Název kategorie Úplný seznam kategorií najdete v tématu [kategorie](https://aka.ms/pa-indoor-spacecategories). |
|`navigableBy`| pole řetězců |    false (nepravda)    |Určuje typy navigace v agentech, které mohou procházet jednotky. Tato vlastnost informuje o schopnostech Wayfinding. Povolené hodnoty jsou: `pedestrian` , `wheelchair` , `machine` , `bicycle` , `automobile` , `hiredAuto` , `bus` , `railcar` , `emergency` , `ferry` , `boat` , a `disallowed` .|
|`routeThroughBehavior`|    řetězec|    false (nepravda)    |Chování trasy v rámci jednotky. Povolené hodnoty jsou `disallowed` , `allowed` a `preferred` . Výchozí hodnota je `allowed`.|
|`occupants`    |pole objektů directoryInfo |false (nepravda)    |Seznam osob pro jednotku. |
|`nameAlt`|    řetězec|    false (nepravda)|    Alternativní název jednotky |
|`nameSubtitle`|    řetězec    |false (nepravda)|    Podnadpis jednotky |
|`addressRoomNumber`|    řetězec|    false (nepravda)|    Číslo místnosti, jednotky, bytu nebo sady jednotek.|
|`verticalPenetrationCategory`|    řetězec|    false (nepravda)| Když je tato vlastnost definovaná, výsledná funkce je vertikální průnik (VRT) místo jednotky. VRTs můžete použít k přechodu na jiné funkce VRT na úrovních výše nebo pod ní. Svislé průniky je název [kategorie](https://aka.ms/pa-indoor-spacecategories) . Je-li tato vlastnost definována, `categoryName` je vlastnost přepsána pomocí `verticalPenetrationCategory` . |
|`verticalPenetrationDirection`|    řetězec|    false (nepravda)    |Pokud `verticalPenetrationCategory` je definován, Volitelně definujte platný směr cesty. Povolené hodnoty jsou: `lowToHigh` , `highToLow` , a `both` `closed` . Výchozí hodnota je `both`.|
| `nonPublic` | bool | false (nepravda) | Určuje, zda je jednotka otevřena veřejnému. |
| `isRoutable` | bool | false (nepravda) | Pokud je tato vlastnost nastavená na `false` , nemůžete přejít na jednotku ani do ní. Výchozí hodnota je `true`. |
| `isOpenArea` | bool | false (nepravda) | Umožňuje navigaci v agentovi zadat jednotku bez nutnosti otevření připojeného k jednotce. Ve výchozím nastavení je tato hodnota nastavena na hodnotu `true` pro jednotky bez otevřených a `false` pro jednotky s otevřenými. Ručním nastavením na `isOpenArea` `false` hodnotu u jednotky bez jakýchkoli otevřených výsledků se zobrazí upozornění. Důvodem je to, že výsledná jednotka nebude dosažitelná pomocí navigace v agentovi.|

### `zoneProperties`

`zoneProperties`Objekt obsahuje pole JSON vlastností zóny.

| Vlastnost  | Typ | Vyžadováno | Popis |
|-----------|------|----------|-------------|
|Název_zóny        |řetězec    |true    |Název zóny, která se má přidružit k `zoneProperty` záznamu Tento záznam je platný pouze v případě, že `zoneName` se v vrstvě zóny nachází shodný popisek `zoneLabel` .  |
|categoryName|    řetězec|    false (nepravda)    |Název kategorie Úplný seznam kategorií najdete v tématu [kategorie](https://aka.ms/pa-indoor-spacecategories). |
|zoneNameAlt|    řetězec|    false (nepravda)    |Alternativní název zóny  |
|zoneNameSubtitle|    řetězec |    false (nepravda)    |Podnadpis zóny |
|zoneSetId|    řetězec |    false (nepravda)    | Nastavte ID, aby se navázal vztah mezi několika zónami, aby se mohly dotazovat nebo vybrat jako skupinu. Například zóny, které přesahují více úrovní. |

### <a name="sample-drawing-package-manifest"></a>Ukázkový manifest balíčku vykreslování

Následuje ukázkový soubor manifestu ukázkového balíčku pro kreslení. Pokud si chcete stáhnout celý balíček, přečtěte si téma [vzorový balíček pro vykreslování](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

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

Když váš balíček vykreslování splňuje požadavky, můžete použít [službu Azure Maps Conversion](https://docs.microsoft.com/rest/api/maps/conversion) k převedení balíčku na datovou mapu. Pak můžete použít datovou sadu k vygenerování vnitřních map pomocí modulu mapy vnitřníchy.

> [!div class="nextstepaction"]
>[Autor pro mapy vnitřníchy](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Kurz: vytvoření mapy interiéru autora](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Dynamické stylování map v interiéru](indoor-map-dynamic-styling.md)
