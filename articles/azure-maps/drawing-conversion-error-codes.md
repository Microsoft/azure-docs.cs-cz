---
title: Azure Maps chyb a upozornění převodu vykreslování
description: Přečtěte si o chybách a upozorněních při převodu, které se mohou vyhovět při používání služby Azure Maps Conversion. Přečtěte si doporučení, jak vyřešit chyby a upozornění, s příklady.
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philMea
ms.openlocfilehash: 14cf5238d29ede1ea229604316eee875b417e50e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361530"
---
# <a name="drawing-conversion-errors-and-warnings"></a>Upozornění a chyby převodu kreslení

[Služba Azure Maps Conversion](https://docs.microsoft.com/rest/api/maps/conversion) umožňuje převést nahrané balíčky výkresu na data mapy. Balíčky pro kreslení musí splňovat [požadavky balíčku pro vykreslování](drawing-requirements.md). Pokud nejsou splněny některé požadavky, služba konverze vrátí chyby nebo upozornění. V tomto článku jsou uvedeny kódy chyb a varování při převodu s doporučeními, jak je vyřešit. Poskytuje také některé příklady kreseb, které mohou způsobit, že služba převodu vrátí tyto kódy.

Služba konverze bude úspěšná, pokud dojde k nějakým upozorněním na převod. Doporučuje se ale zkontrolovat a vyřešit všechna upozornění. Upozornění znamená, že část převodu byla ignorována nebo automaticky opravena. Selhání při řešení problémů by mohlo způsobit chyby v těchto procesech.

## <a name="general-warnings"></a>Obecná upozornění

### <a name="geometrywarning"></a>**geometryWarning**

#### <a name="description-for-geometrywarning"></a>*Popis pro geometryWarning*

K **geometryWarning** dojde v případě, že výkres obsahuje neplatnou entitu. Neplatná entita je entita, která nedodržuje geometrická omezení. Příklady neplatných entit jsou samostatně protínající se mnohoúhelníky nebo neuzavřená lomená čárka ve vrstvě, která podporuje pouze uzavřenou geometrii.

Služba konverze nemůže vytvořit funkci mapy z neplatné entity a místo toho ji ignoruje.

#### <a name="examples-for-geometrywarning"></a>*Příklady pro geometryWarning*

* Následující dva obrázky ukazují příklady mnohoúhelníků s sebou samým.

     ![Příkladem křížového mnohoúhelníku, který je v sobě příkladem.](./media/drawing-conversion-error-codes/geometry-warning-1.png)

     ![Příkladem křížového mnohoúhelníku, který je v sobě příkladem.](./media/drawing-conversion-error-codes/geometry-warning-2.png)

* Níže je obrázek, který zobrazuje neuzavřenou lomenou čáru. Předpokládat, že vrstva podporuje pouze uzavřenou geometrii.

    ![Příklad neuzavřené lomené čáry](./media/drawing-conversion-error-codes/geometry-warning-3.png)

#### <a name="how-to-fix-geometrywarning"></a>*Jak opravit geometryWarning*

Zkontrolujte **geometryWarning** pro každou entitu a ověřte, že se řídí geometrickými omezeními.

### <a name="unexpectedgeometryinlayer"></a>**unexpectedGeometryInLayer**

#### <a name="description-for-unexpectedgeometryinlayer"></a>*Popis pro unexpectedGeometryInLayer*

K **unexpectedGeometryInLayer** upozornění dojde, Pokud kresba obsahuje geometrii, která není kompatibilní s očekávaným typem geometrie pro danou vrstvu. Když převodní služba vrátí upozornění **unexpectedGeometryInLayer** , bude tuto geometrii ignorovat.

#### <a name="example-for-unexpectedgeometryinlayer"></a>*Příklad pro unexpectedGeometryInLayer*

Následující obrázek ukazuje neuzavřenou lomenou čáru. Předpokládat, že vrstva podporuje pouze uzavřenou geometrii.

![Příklad neuzavřené lomené čáry](./media/drawing-conversion-error-codes/geometry-warning-3.png)

#### <a name="how-to-fix-unexpectedgeometryinlayer"></a>*Jak opravit unexpectedGeometryInLayer*

Zkontrolujte všechna upozornění **unexpectedGeometryInLayer** a přesuňte nekompatibilní geometrii do kompatibilní vrstvy. Pokud není kompatibilní s žádnou jinou vrstvou, měla by být odebrána.

### <a name="unsupportedfeaturerepresentation"></a>**unsupportedFeatureRepresentation**

#### <a name="description-for-unsupportedfeaturerepresentation"></a>*Popis pro unsupportedFeatureRepresentation*

K **unsupportedFeatureRepresentation** upozornění dojde, Pokud kresba obsahuje nepodporovaný typ entity.

#### <a name="example-for-unsupportedfeaturerepresentation"></a>*Příklad pro unsupportedFeatureRepresentation*

Následující obrázek ukazuje nepodporovaný typ entity jako víceřádkový textový objekt na vrstvě popisku.
  
![Příklad víceřádkového textového objektu na vrstvě popisku](./media/drawing-conversion-error-codes/multi-line.png)

#### <a name="how-to-fix-unsupportedfeaturerepresentation"></a>*Jak opravit unsupportedFeatureRepresentation*

Ujistěte se, že soubory DWG obsahují pouze podporované typy entit. Podporované typy jsou uvedené v [části požadavky na soubory výkresu v článku požadavky na balíček pro vykreslování](drawing-requirements.md#drawing-package-requirements).

### <a name="automaticrepairperformed"></a>**automaticRepairPerformed**

#### <a name="description-for-automaticrepairperformed"></a>*Popis pro automaticRepairPerformed*

K **automaticRepairPerformed** upozornění dojde, když služba konverze automaticky opraví neplatnou geometrii.

#### <a name="examples-for-automaticrepairperformed"></a>*Příklady pro automaticRepairPerformed*

* Následující obrázek ukazuje, jak služba převodu opravila v platné geometrii mnohoúhelník s sebou samým.

    ![Příklad opraveného mnohoúhelníku, který se přeprotínají](./media/drawing-conversion-error-codes/automatic-repair-1.png)

* Následující obrázek ukazuje, jak převodní služba přichycena k prvnímu a poslednímu vrcholu neuzavřené lomené čáry, aby vytvořila uzavřenou lomenou čáru, kde první a poslední vrchol byl méně než 1 mm od sebe.  

    ![Příklad přichycené lomené čáry](./media/drawing-conversion-error-codes/automatic-repair-2.png)

* Následující obrázek ukazuje, jak ve vrstvě, která podporuje pouze uzavřené lomené čáry, opravila několik neuzavřených linií. Aby nedošlo k zahození neuzavřených linií, služba je kombinuje do jedné uzavřené lomené čáry.

    ![Příklad neuzavřených lomených čar na jednu uzavřenou lomenou čáru](./media/drawing-conversion-error-codes/automatic-repair-3.png)

#### <a name="how-to-fix-automaticrepairperformed"></a>*Jak opravit automaticRepairPerformed*

Chcete-li vyřešit **automaticRepairPerformed** upozornění, proveďte následující akce:

1. Zkontrolujte geometrii a konkrétní text upozornění.
2. Zjistěte, jestli je automatizovaná oprava správná.
3. Pokud je oprava správná, pokračujte. V opačném případě přejdete do souboru návrhu a vyřešte upozornění ručně.

>[!TIP]
>Chcete-li v budoucnu potlačit upozornění, proveďte změny v původním vykreslování tak, aby původní výkres odpovídal opravenému výkresu.

## <a name="manifest-warnings"></a>Upozornění manifestu

### <a name="redundantattribution"></a>**redundantAttribution**

#### <a name="description-for-redundantattribution"></a>*Popis pro redundantAttribution*

K **redundantAttribution** upozornění dojde, pokud manifest obsahuje redundantní nebo konfliktní vlastnosti objektu.

#### <a name="examples-for-redundantattribution"></a>*Příklady pro redundantAttribution*

* Následující fragment kódu JSON obsahuje dva nebo více `unitProperties` objektů se stejným `name` .

    ```json
    "unitProperties": [
        {
            "unitName": "L1-100",
            "categoryName": "room.office"
        },
        {
            "unitName": "L1-101",
            "categoryName": "room.office"
        },
        {
            "unitName": "L1-101",
            "categoryName": "room.office"
        }
    ]
    ```

* V níže uvedeném fragmentu kódu JSON mají dva nebo více `zoneProperties` objektů stejnou `name` .

    ```json
     "zoneProperties": [
        {
            "zoneName": "Assembly Area 1",
            "categoryName": "zone.assembly"
        },
        {
            "zoneName": "Assembly Area 2",
            "categoryName": "zone.assembly"
        },
        {
            "zoneName": "Assembly Area 2",
            "categoryName": "zone.assembly"
        }
    ```

#### <a name="how-to-fix-redundantattribution"></a>*Jak opravit redundantAttribution*

Chcete-li opravit **redundantAttribution* upozornění, odeberte nadbytečné nebo konfliktní vlastnosti objektu.

### <a name="manifestwarning"></a>**manifestWarning**

#### <a name="description-for-manifestwarning"></a>*Popis pro manifestWarning*

K **manifestWarning** dojde v případě, že manifest obsahuje objekty UnitProperties nebo zoneProperties, které se při převodu nepoužívá.

#### <a name="examples-for-manifestwarning"></a>*Příklady pro manifestWarning*

* Manifest obsahuje `unitProperties` objekt s `unitName` , který nemá odpovídající popisek ve `unitLabel` vrstvě.

* Manifest obsahuje `zoneProperties` objekt s `zoneName` , který nemá odpovídající popisek ve `zoneLabel` vrstvě.

#### <a name="how-to-fix-manifestwarning"></a>*Jak opravit manifestWarning*

Chcete-li opravit **manifestWarning**, odeberte nepoužitý `unitProperties` `zoneProperties` objekt nebo z manifestu nebo přidejte popisek jednotky nebo zóny k vykreslování tak, aby objekt Properties byl použit během převodu.

## <a name="wall-warnings"></a>Upozornění na zeď

### <a name="walloutsidelevel"></a>**wallOutsideLevel**

#### <a name="description-for-walloutsidelevel"></a>*Popis pro wallOutsideLevel*

K **wallOutsideLevel** upozornění dojde, Pokud kresba obsahuje geometrii zdi mimo hranice obrysu úrovně.

#### <a name="example-for-walloutsidelevel"></a>*Příklad pro wallOutsideLevel*

* Následující obrázek znázorňuje vnitřní zeď, červenou, mimo hranice žluté úrovně.

    ![Příklad vnitřní stěny mimo hranici úrovně](./media/drawing-conversion-error-codes/wall-outside-level.png)

* Následující obrázek ukazuje vnější zeď, červenou, mimo hranice žluté úrovně.

    ![Příklad vnější stěny mimo hranici úrovně](./media/drawing-conversion-error-codes/wall-outside-level-exterior.png)

#### <a name="how-to-fix-walloutsidelevel"></a>*Jak opravit wallOutsideLevel*

Chcete-li opravit **wallOutsideLevel** upozornění, rozbalte geometrii úrovně tak, aby zahrnovala všechny zdi. Nebo upravte hranice stěn tak, aby se vešly do hranice úrovně.

## <a name="unit-warnings"></a>Upozornění na jednotky

### <a name="unitoutsidelevel"></a>**unitOutsideLevel**

#### <a name="description-for-unitoutsidelevel"></a>*Popis pro unitOutsideLevel*

K **unitOutsideLevel** upozornění dojde, Pokud kresba obsahuje geometrii jednotky mimo hranice obrysu úrovně.

#### <a name="example-for-unitoutsidelevel"></a>*Příklad pro unitOutsideLevel*

 Na následujícím obrázku je geometrie jednotky červená, je větší než hranice žluté úrovně.

 ![Příklad jednotky překračující hranici úrovně](./media/drawing-conversion-error-codes/unit-outside-level.png)

#### <a name="how-to-fix-unitoutsidelevel"></a>*Jak opravit unitOutsideLevel*

Chcete-li opravit **unitOutsideLevel** upozornění, rozbalte hranici úrovně tak, aby zahrnovala všechny jednotky. Nebo upravte geometrii jednotky tak, aby se vešla do hranice úrovně.

### <a name="partiallyoverlappingunit"></a>**partiallyOverlappingUnit**

#### <a name="description-for-partiallyoverlappingunit"></a>*Popis pro partiallyOverlappingUnit*

K **partiallyOverlappingUnit** upozornění dojde v případě, že výkres obsahuje geometrii jednotky částečně překrývající jinou geometrii jednotky. Služba převodu zahodí překrývající se jednotky.

#### <a name="example-scenarios-partiallyoverlappingunit"></a>*Příklady scénářů partiallyOverlappingUnit*

Na následujícím obrázku je překrývající se jednotka zvýrazněna červeně. `UNIT110` a `HALLWAY` jsou zahozeny.

![Příklad překrývajících se jednotek](./media/drawing-conversion-error-codes/partially-overlapping-unit.png)

#### <a name="how-to-fix-partiallyoverlappingunit"></a>*Jak opravit partiallyOverlappingUnit*

Chcete-li opravit **partiallyOverlappingUnit** upozornění, překreslete všechny částečně překrývající se jednotky, aby nepřekrývaly žádné jiné jednotky.

## <a name="door-warnings"></a>Upozornění na dvířka

### <a name="dooroutsidelevel"></a>**doorOutsideLevel**

#### <a name="description-for-dooroutsidelevel"></a>*Popis pro doorOutsideLevel*

K **doorOutsideLevel** upozornění dojde, Pokud kresba obsahuje geometrii dvířek mimo hranice geometrie úrovně.

#### <a name="example-for-dooroutsidelevel"></a>*Příklad pro doorOutsideLevel*

Na následujícím obrázku je geometrie dvířek zvýrazněna červenou a překrývá hranice žluté úrovně.

![Příklad dvířek překrývajících hranice úrovně](./media/drawing-conversion-error-codes/door-outside-level.png)

#### <a name="how-to-fix-dooroutsidelevel"></a>*Jak opravit doorOutsideLevel*

Pokud chcete opravit **doorOutsideLevel** upozornění, překreslete geometrii dvířek tak, aby byla uvnitř hranic úrovně.

## <a name="zone-warnings"></a>Upozornění zóny

### <a name="zonewarning"></a>**zoneWarning**

#### <a name="description-for-zonewarning"></a>*Popis pro zoneWarning*

K **zoneWarning** dojde, když zóna neobsahuje popisek. Služba převodu zahodí zónu, která není typu Label. l.

#### <a name="example-for-zonewarning"></a>*Příklad pro zoneWarning*

Následující obrázek ukazuje zónu, která neobsahuje popisek.

![Příklad zóny neobsahuje popisek.](./media/drawing-conversion-error-codes/zone-warning.png)

#### <a name="how-to-fix-zonewarning"></a>*Jak opravit zoneWarning*

Chcete-li opravit **zoneWarning**, ověřte, zda má každá zóna jeden popisek.

## <a name="label-warnings"></a>Upozornění popisku

### <a name="labelwarning"></a>*labelWarning*

#### <a name="description-for-labelwarning"></a>*Popis pro labelWarning*

K **labelWarning** dojde, když kresba obsahuje nejednoznačnou nebo protichůdnou funkci labels.

K **labelWarning** dojde z jednoho nebo více následujících důvodů:

* Označení jednotky není v žádném z jednotek.
* Popisek zóny není v žádné zóně.
* Popisek zóny se nachází ve dvou nebo více zónách.

#### <a name="example-for-labelwarning"></a>*Příklad pro labelWarning*

Následující obrázek ukazuje popisek, který se nachází uvnitř dvou zón.

![Příklad popisku ve dvou zónách ](./media/drawing-conversion-error-codes/label-warning.png)

#### <a name="how-to-fix-labelwarning"></a>*Jak opravit labelWarning*

Pokud chcete opravit **labelWarning**, ujistěte se, že:

* Všechny popisky jednotek jsou uvnitř jednotek.
* Všechny popisky zóny jsou uvnitř zón.
* Všechny popisky zóny jsou v jedné a jenom jedné zóně.

## <a name="drawing-package-errors"></a>Chyby při vykreslování balíčku

### <a name="invalidarchiveformat"></a>**invalidArchiveFormat**

#### <a name="description-for-invalidarchiveformat"></a>*Popis pro invalidArchiveFormat*

K chybě **invalidArchiveFormat** dojde v případě, že je balíček pro kreslení v neplatném formátu archivu, například gzip nebo 7-zip. Podporován je pouze formát archivu ZIP.

K chybě **invalidArchiveFormat** dojde také v případě, že je archiv zip prázdný.

#### <a name="how-to-fix-invalidarchiveformat"></a>*Jak opravit invalidArchiveFormat*

Pokud chcete opravit chybu **invalidArchiveFormat** , ověřte, že:

* Název souboru archivu končí na _. zip_.
* Váš archiv ZIP obsahuje data.
* Můžete otevřít archiv ZIP.

### <a name="invaliduserdata"></a>**invalidUserData**

#### <a name="description-for-invaliduserdata"></a>*Popis pro invalidUserData*

K chybě **invalidUserData** dojde, pokud služba převodu nemůže načíst datový objekt uživatele z úložiště.

#### <a name="example-scenario-for-invaliduserdata"></a>*Příklad scénáře pro invalidUserData*

Pokusili jste se nahrát balíček pro kreslení s nesprávným `udid` parametrem.

#### <a name="how-to-fix-invaliduserdata"></a>*Jak opravit invalidUserData*

Pokud chcete opravit chybu **invalidUserData** , ověřte, že:

* Zadali jste správný `udid` pro nahraný balíček.
* Pro účet Azure Maps, který jste použili k nahrání balíčku pro kreslení, je povolený Azure Maps tvůrce.
* Požadavek rozhraní API na službu převodu obsahuje klíč předplatného pro Azure Maps účet, který jste použili k nahrání balíčku pro kreslení.

### <a name="dwgerror"></a>**dwgError**

#### <a name="description-for-dwgerror"></a>*Popis pro dwgError*

**DwgError** v případě, že balíček pro kreslení obsahuje problém s jedním nebo více soubory DWG v nahraném archivu zip.

K **dwgError** dojde v případě, že balíček pro kreslení obsahuje soubor DWG, který nelze otevřít, protože je neplatný nebo poškozený.

* Soubor DWG není platným kresbou formátu souborů DWG AutoCADu.
* Soubor DWG je poškozený.
* Soubor DWG je uvedený v _manifest.jsv_ souboru, ale v archivu zip chybí.

#### <a name="how-to-fix-dwgerror"></a>*Jak opravit dwgError*

Pokud chcete opravit **dwgError**, zkontrolujte _manifest.jsv_ souboru a ověřte, že:

* Všechny soubory DWG v archivu ZIP jsou platné kresby formátu DWG AutoCADu, v AutoCADu je otevřete. Odeberte nebo opravte všechny neplatné výkresy.
* Seznam souborů DWG v _manifest.js_  se shoduje se soubory DWG v archivu zip.

## <a name="manifest-errors"></a>Chyby manifestu

### <a name="invalidjsonformat"></a>**invalidJsonFormat**

#### <a name="description-for-invalidjsonformat"></a>Popis pro invalidJsonFormat

Pokud _manifest.js_ pro soubor nelze přečíst, dojde k chybě **invalidJsonFormat** .

On_file _manifest.jsnelze načíst z důvodu chyb formátování nebo syntaxe JSON. Další informace o tom, jak formátovat a syntax formátu JSON, najdete [ve formátu výměny dat JavaScript Object Notation (JSON)](https://tools.ietf.org/html/rfc7159) .

#### <a name="how-to-fix-invalidjsonformat"></a>*Jak opravit invalidJsonFormat*

K opravě chyby **invalidJsonFormat** použijte linter JSON ke zjištění a vyřešení všech chyb JSON.

### <a name="missingrequiredfield"></a>**missingRequiredField**

#### <a name="description-for-missingrequiredfield"></a>*Popis pro missingRequiredField*

Pokud _manifest.jsv_ souboru chybí požadovaná data, dojde k chybě **missingRequiredField** .

#### <a name="how-to-fix-missingrequiredfield"></a>*Jak opravit missingRequiredField*

Chcete-li opravit chybu **missingRequiredField** , ověřte, zda manifest obsahuje všechny požadované vlastnosti. Úplný seznam požadovaného objektu manifestu naleznete v [části manifest v tématu požadavky na balíček pro kreslení](drawing-requirements.md#manifest-file-requirements) .  

### <a name="missingmanifest"></a>**missingManifest**

#### <a name="description-for-missingmanifest"></a>*Popis pro missingManifest*

K chybě **missingManifest** dojde, když v archivu ZIP chybí _manifest.jsv_ souboru.

K chybě **missingManifest** dojde z jednoho nebo více následujících důvodů:

* Soubor _manifest.jsv_ souboru je špatně napsaný.
* _manifest.js_ chybí.
* _manifest.jsv_ adresáři není v kořenovém adresáři archivu zip.

#### <a name="how-to-fix-missingmanifest"></a>*Jak opravit missingManifest*

Pokud chcete opravit chybu **missingManifest** , zkontrolujte, že archiv obsahuje soubor s názvem _manifest.jsna_ kořenové úrovni archivu zip.

### <a name="conflict"></a>**Konflikt**

#### <a name="description-for-conflict"></a>*Popis pro konflikt*

K chybě **konfliktu** dojde, když _manifest.jsv_ souboru obsahuje konfliktní informace.

#### <a name="example-scenario-for-conflict"></a>*Ukázkový scénář pro konflikt*

Služba konverze vrátí chybu **konfliktu** , pokud je definována více než jedna úroveň se stejnou úrovní pořadí. Následující fragment kódu JSON ukazuje dvě úrovně definované se stejným pořadovým číslem.

```JSON
"buildingLevels":
{
    "levels": [
        {
            "levelName": "Ground",
            "ordinal": 0,
            "filename": "./Level_0.dwg"
        },
        {
            "levelName": "Parking",
            "ordinal": 0,
            "filename": "./Level_P.dwg"
        }
    ]
}
```

#### <a name="how-to-fix-conflict"></a>*Jak opravit konflikt*

Pokud chcete opravit chybu **konfliktu** , zkontrolujte _manifest.js_ a odeberte všechny konfliktní informace.

### <a name="invalidgeoreference"></a>**invalidGeoreference**

#### <a name="description-for-invalidgeoreference"></a>*Popis pro invalidGeoreference*

K chybě **invalidGeoreference** dojde, když _manifest.jsv_ souboru obsahuje neplatný ukazatel.

K chybě **invalidGeoreference** dojde z jednoho nebo více následujících důvodů:

* Uživatel je zeměpisně odkazující na zeměpisnou šířku nebo zeměpisnou hodnotu, která je mimo rozsah.
* Uživatel má obsměrované hodnoty rotace, která je mimo rozsah.

#### <a name="example-scenario-for-invalidgeoreference"></a>*Příklad scénáře pro invalidGeoreference*

V níže uvedeném fragmentu kódu JSON je zeměpisná šířka větší než horní limit.

```json
"georeference"
{
    "lat": 88.0,
    "lon": -122.132600,
    "angle": 0
},
```

#### <a name="how-to-fix-invalidgeoreference"></a>*Jak opravit invalidGeoreference*

Chcete-li opravit chybu **invalidGeoreference** , ověřte, zda jsou hodnoty v rozsahu.

>[!IMPORTANT]
>V zeměpisném formátu JSON je pořadí souřadnic Zeměpisná šířka a zeměpisná šířka. Pokud nepoužíváte správné pořadí, možná nebudete náhodně označovat zeměpisnou šířku nebo zeměpisnou hodnotu, která je mimo rozsah.

## <a name="wall-errors"></a>Chyby stěn

### <a name="wallerror"></a>**wallError**

#### <a name="description-for-wallerror"></a>*Popis pro wallError*

K **wallError** dojde, když výkres obsahuje chybu při pokusu o vytvoření funkce zdi.

#### <a name="example-scenario-for-wallerror"></a>*Příklad scénáře pro wallError*

Na následujícím obrázku je zobrazená funkce zdi, která nepřekrývá žádné jednotky.

![Příklad funkce zeď, která nepřekrývá žádné jednotky](./media/drawing-conversion-error-codes/wall-error.png)

#### <a name="how-to-fix-wallerror"></a>*Jak opravit wallError*

Pokud chcete opravit chybu **wallError** , překreslete stěnu tak, aby se překrývala aspoň jedna jednotka. Nebo vytvořte novou jednotku, která překryje zeď.

## <a name="vertical-penetration-errors"></a>Chyby vertikálního průniku

### <a name="verticalpenetrationerror"></a>**verticalPenetrationError**

#### <a name="description-for-verticalpenetrationerror"></a>*Popis pro verticalPenetrationError*

K **verticalPenetrationError** dojde, Pokud kresba obsahuje nejednoznačnou funkci průniku ve svislém směru.

K **verticalPenetrationError** dojde z jednoho nebo více následujících důvodů:

* Kresba obsahuje svislou oblast průniku, která nemá žádné překrývající se oblasti svislého průniku na jakékoli úrovni nad nebo pod ní.
* Balíček pro kreslení obsahuje úroveň se dvěma nebo více funkcemi překrytí, které se překrývají s jednou funkcí svislého průniku na jiné úrovni přímo nad nebo pod ní.

#### <a name="example-scenario-for-verticalpenetrationerror"></a>*Příklad scénáře pro verticalPenetrationError*

Následující obrázek ukazuje oblast svislého průniku bez překrývajících se oblastí svislého průniku na úrovních nad nebo pod ním.

![Příklad svislého průniku 1](./media/drawing-conversion-error-codes/vrt-2.png)

Následující obrázek znázorňuje oblast svislého průniku, která se překrývá s více než jednou oblastí svislého průniku na sousední úrovni.

![Příklad svislého průniku 2](./media/drawing-conversion-error-codes/vrt-1.png)

#### <a name="how-to-fix-verticalpenetrationerror"></a>Jak opravit verticalPenetrationError

Pokud chcete opravit chybu **verticalPenetrationError** , přečtěte si informace o tom, jak používat funkci vertikálního průniku v článku [požadavky na balíček pro vykreslování](drawing-requirements.md) .

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Jak používat Azure Maps pro vykreslování – Vizualizér chyb](drawing-error-visualizer.md)

> [!div class="nextstepaction"]
> [Tvůrce pro mapování vnitřníchy](creator-indoor-maps.md)