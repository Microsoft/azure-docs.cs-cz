---
title: Řešení potíží se sdílenými bitovými kopiemi v Azure
description: Naučte se řešit problémy s galeriemi sdílených imagí.
author: olayemio
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: troubleshooting
ms.workload: infrastructure
ms.date: 10/27/2020
ms.author: olayemio
ms.reviewer: cynthn
ms.openlocfilehash: 015fa201fe1c31dde2e30c2fe689ac13452b1b01
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105607588"
---
# <a name="troubleshoot-shared-image-galleries-in-azure"></a>Řešení potíží s galeriemi sdílených imagí v Azure

Pokud máte problémy s prováděním jakýchkoli operací v galeriích sdílených imagí, definicích imagí a verzích obrázků, spusťte příkaz po chybě znovu v režimu ladění. Režim ladění aktivujete tak, že předáte `--debug` přepínač s rozhraním příkazového řádku Azure a `-Debug` přepnete do PowerShellu. Po vyhledání chyby použijte tento článek k vyřešení potíží.


## <a name="creating-or-modifying-a-gallery"></a>Vytvoření nebo úprava galerie ##

**Zpráva**: *název galerie je neplatný. Povolené znaky jsou anglické alfanumerické znaky, s podtržítky a tečkami povolenými uprostřed, maximálně 80 znaků. Všechny ostatní speciální znaky, včetně pomlček, nejsou povoleny.*  
**Příčina**: název galerie nesplňuje požadavky na pojmenování.  
**Alternativní řešení**: vyberte název, který splňuje tyto podmínky: 
- Má limit 80 znaků
- Obsahuje jenom anglická písmena, číslice, podtržítka a tečky.
- Začíná a končí anglicky písmeny nebo číslicemi.

**Zpráva**: *název entity ' název galerie ' je podle jeho ověřovacího pravidla neplatný: ^ [^ \_ \w] [\w-. \_ ] {0,79} (? <! [-.]) $.*  
**Příčina**: název galerie nesplňuje požadavky na pojmenování.  
**Alternativní řešení**: vyberte název galerie, který splňuje následující podmínky: 
- Má limit 80 znaků
- Obsahuje jenom anglická písmena, číslice, podtržítka a tečky.
- Začíná a končí anglicky písmeny nebo číslicemi.

**Zpráva**: *zadaný název prostředku <název galerie \> má neplatné koncové znaky: <znak \> . Název nemůže končit znaky \> : <znak.*  
**Příčina**: název galerie končí tečkou nebo podtržítkem.  
**Alternativní řešení**: vyberte název galerie, který splňuje následující podmínky: 
- Má limit 80 znaků
- Obsahuje jenom anglická písmena, číslice, podtržítka a tečky.
- Začíná a končí anglicky písmeny nebo číslicemi.

**Zpráva**: *zadané umístění <oblasti není \> k dispozici pro typ prostředku Microsoft. COMPUTE/galerie. Seznam oblastí, které jsou k dispozici pro typ prostředku, je...*  
**Příčina**: oblast zadaná pro galerii je nesprávná nebo vyžaduje žádost o přístup.  
**Alternativní řešení**: Ověřte, že je název oblasti správný. Pokud je název oblasti správný, odešlete [žádost o přístup](/troubleshoot/azure/general/region-access-request-process) k dané oblasti.

**Zpráva**: *prostředek nelze odstranit, dokud nebudou odstraněny vnořené prostředky.*  
**Příčina**: Pokusili jste se odstranit galerii, která obsahuje alespoň jednu existující definici obrázku. Galerie musí být prázdná, aby ji bylo možné odstranit.  
**Alternativní řešení**: Odstraňte všechny definice obrázků v galerii a pak pokračujte v odstraňování galerie. Pokud definice image obsahuje verze imagí, je nutné před odstraněním definic imagí odstranit verze imagí.

**Zpráva**: *název galerie ' <název galerie \> ' není jedinečný v rámci předplatného ' <subscriptionID> '. Vyberte prosím jiný název galerie.*  
**Příčina**: máte stávající galerii se stejným názvem a pokusili jste se vytvořit další galerii se stejným názvem.  
**Alternativní řešení**: pro galerii vyberte jiný název.

**Zpráva**: *prostředek <Gallery \> již existuje v umístění <oblasti \_ 1 \> ve skupině prostředků <zdroj \> . Prostředek se stejným názvem se nedá vytvořit v umístění <oblasti \_ 2 \> . Vyberte prosím nový název prostředku.*  
**Příčina**: máte stávající galerii se stejným názvem a pokusili jste se vytvořit další galerii se stejným názvem.  
**Alternativní řešení**: pro galerii vyberte jiný název.

## <a name="creating-or-modifying-image-definitions"></a>Vytvoření nebo úprava definicí obrázků ##

**Zpráva**: *Změna vlastnosti ' galleryImage. Properties. <Property \> ' není povolena.*  
**Příčina**: Pokusili jste se změnit typ operačního systému, stav operačního systému, generaci technologie Hyper-V, nabídka, Vydavatel nebo SKU. Změna kterékoli z těchto vlastností není povolená.  
**Alternativní řešení**: místo toho vytvořte definici nové image.

**Zpráva**: *prostředek <Gallery/imageDefinitionName \> již existuje v umístění <oblasti \_ 1 \> ve skupině prostředků <zdroj \> . Prostředek se stejným názvem se nedá vytvořit v umístění <oblasti \_ 2 \> . Vyberte prosím nový název prostředku.*  
**Příčina**: máte existující definici image ve stejné galerii a skupině prostředků se stejným názvem. Pokusili jste se vytvořit další definici obrázku se stejným názvem a ve stejné galerii, ale v jiné oblasti.  
**Alternativní řešení**: použijte jiný název definice obrázku nebo vložte definici image do jiné galerie nebo skupiny prostředků.

**Zpráva**: *zadaný název prostředku <Gallery \> /<imageDefinitionName \> obsahuje tyto neplatné koncové znaky: <znak \> . Název nemůže končit znaky \> : <znak.*  
**Příčina**: <název imageDefinitionName \> končí tečkou nebo podtržítkem.  
**Alternativní řešení**: vyberte název definice obrázku, který splňuje tyto podmínky: 
- Má limit 80 znaků
- Obsahuje jenom anglická písmena, číslice, podtržítka, spojovníky a tečky.
- Začíná a končí anglicky písmeny nebo čísly.

**Zpráva**: *název entity <imageDefinitionName \> je podle jeho ověřovacího pravidla neplatný: ^ [^ \_ \\ W] [ \\ W-. \_ ] {0,79} (? <! [-.]) $"*  
**Příčina**: <název imageDefinitionName \> končí tečkou nebo podtržítkem.  
**Alternativní řešení**: vyberte název definice obrázku, který splňuje tyto podmínky: 
- Má limit 80 znaků
- Obsahuje jenom anglická písmena, číslice, podtržítka, spojovníky a tečky.
- Začíná a končí anglicky písmeny nebo číslicemi.

**Zpráva**: *vlastnost název assetu galleryImage. Properties. Identifier. <není \> platná. Nemůže být prázdný. Povolené znaky jsou velká a malá písmena, číslice, spojovník (-), tečka (.), podtržítko ( \_ ). Názvům nesmí končit tečkou (.). Délka názvu nesmí překročit <é číselné \> znaky.*  
**Příčina**: hodnota vydavatele, nabídky nebo SKU nesplňuje požadavky na pojmenování.  
**Alternativní řešení**: vyberte hodnotu, která splňuje tyto podmínky: 
- Má limit 128 znaků pro vydavatele nebo 64 omezení pro nabídku a SKU
- Obsahuje jenom anglická písmena, číslice, spojovníky, podtržítka a tečky.
- Nekončí tečkou

**Zpráva**: *u vnořeného prostředku nelze provést požadovanou operaci. Nepovedlo \> se najít nadřazený <prostředku.*  
**Příčina**: \> v aktuálním předplatném a skupině prostředků neexistuje žádná galerie s názvem <název galerie.  
**Alternativní řešení**: Ověřte správnost názvů galerie, předplatného a skupiny prostředků. V opačném případě vytvořte novou galerii s názvem <název galerie \> .

**Zpráva**: *zadané umístění <oblasti není \> k dispozici pro typ prostředku Microsoft. COMPUTE/galerie. Seznam oblastí, které jsou k dispozici pro typ prostředku, je...*  
**Příčina**: název <oblasti \> je nesprávný nebo vyžaduje žádost o přístup.  
**Alternativní řešení**: Ověřte, zda je název oblasti zadán správně. K zobrazení oblastí, ke kterým máte přístup, můžete spustit tento příkaz. Pokud oblast není v seznamu, odešlete [žádost o přístup](/troubleshoot/azure/general/region-access-request-process).

**Zpráva**: *hodnotu nelze serializovat: <hodnota \> jako typ: ISO-8601., ISO8601Error: ISO 8601 Time devalueer ' chybí. Nedá se analyzovat hodnota \> <řetězců DateTime* .  
**Příčina**: hodnota poskytnutá vlastnosti není správně formátována jako datum.  
**Alternativní řešení**: Zadejte datum ve formátu RRRR-MM-DD, rrrr-mm-dd'T'HH: mm: Sszzz nebo [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-platný formát.

**Zpráva**: *nelze převést řetězec na hodnotu DateTimeOffset: <Value \> . Cesta ' Properties. <vlastnost \> '*  
**Příčina**: hodnota poskytnutá vlastnosti není správně formátována jako datum.  
**Alternativní řešení**: Zadejte datum ve formátu RRRR-MM-DD, rrrr-mm-dd'T'HH: mm: Sszzz nebo [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-platný formát.

**Zpráva**: *EndOfLifeDate musí být nastavené na budoucí datum.*  
**Příčina**: vlastnost datum ukončení životnosti není správně formátována jako datum, které následuje po dnešním datu.  
**Alternativní řešení**: Zadejte datum ve formátu RRRR-MM-DD, rrrr-mm-dd'T'HH: mm: Sszzz nebo [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-platný formát.

**Message**: *argument--<vlastnost \> : Neplatná hodnota typu int: <\> hodnota*  
**Příčina**: <vlastnost \> přijímá pouze celočíselné hodnoty a hodnota <\> není celé číslo.  
**Alternativní řešení**: vyberte celočíselnou hodnotu.

**Zpráva**: *vlastnost minimální hodnoty <\> nesmí být větší než maximální hodnota vlastnosti <\> .*  
**Příčina**: minimální hodnota zadaná pro vlastnost <\> je vyšší než maximální hodnota poskytnutá pro vlastnost <\> .  
**Alternativní řešení**: Změňte hodnoty tak, aby minimum bylo menší nebo rovno maximální hodnotě.

**Zpráva**: *obrázek Galerie: <imageDefinitionName \> identifikoval (Vydavatel: <Publisher \> , nabídka: <nabídka \> , SKU: <SKU \> ) již existuje. Vyberte jiného vydavatele, nabídku, kombinaci SKU.*  
**Příčina**: Pokusili jste se vytvořit novou definici image stejného vydavatele, nabídky a SKU s trojicí jako existující definici obrázku ve stejné galerii.  
**Alternativní řešení**: v rámci Galerie musí mít všechny definice obrázků jedinečnou kombinaci položek Vydavatel, nabídka a SKU. Vyberte jedinečnou kombinaci nebo vyberte novou galerii a znovu vytvořte definici image.

**Zpráva**: *prostředek nelze odstranit, dokud nebudou odstraněny vnořené prostředky.*  
**Příčina**: Pokusili jste se odstranit definici image, která obsahuje verze imagí. Definice obrázku musí být prázdná, aby ji bylo možné odstranit.  
**Alternativní řešení**: Odstraňte všechny verze imagí v definici image a pak pokračujte v odstranění definice image.

**Zpráva**: *vlastnost <parametru nelze vytvořit \> . Hodnotu <hodnoty nelze převést \> na typ <PropertyType \> . Název identifikátoru <hodnoty nelze porovnat \> s platným názvem enumerátoru. Zadejte jeden z následujících názvů enumerátorů a zkuste to znovu: <volba \_ 1 \> , <volba \_ 2 \> ,...*  
**Příčina**: vlastnost má omezený seznam možných hodnot a <hodnota není \> jednou z nich.  
**Alternativní řešení**: zvolte jednu z možných <\> hodnoty volby.

**Zpráva**: *vlastnost <parametru nelze vytvořit \> . Hodnotu <hodnoty nelze převést \> na typ &quot; System. DateTime &quot; .*  
**Příčina**: hodnota poskytnutá vlastnosti není správně formátována jako datum.  
**Alternativní řešení**: Zadejte datum ve formátu RRRR-MM-DD, rrrr-mm-dd'T'HH: mm: Sszzz nebo [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-platný formát.

**Zpráva**: *vlastnost <parametru nelze vytvořit \> . Hodnotu <hodnoty nelze převést \> na typ &quot; System. Int32 &quot; .*  
**Příčina**: <vlastnost \> přijímá pouze celočíselné hodnoty a hodnota <\> není celé číslo.  
**Alternativní řešení**: vyberte celočíselnou hodnotu.

**Zpráva**: *typ účtu úložiště ZRS se v této oblasti nepodporuje.*  
**Příčina**: v oblasti, která ji ještě nepodporuje, jste zvolili standardní úložiště ZRS (zóna – redundantní úložiště).  
**Alternativní řešení**: Změňte typ účtu úložiště na **Premium \_ LRS** nebo **standard \_ LRS**. V naší dokumentaci najdete nejnovější [seznam oblastí](../storage/common/storage-redundancy.md#zone-redundant-storage) s povoleným ZRS Preview.

## <a name="creating-or-updating-image-versions"></a>Vytváření a aktualizace verzí imagí ##

**Zpráva**: *zadané umístění <oblasti není \> k dispozici pro typ prostředku Microsoft. COMPUTE/galerie. Seznam oblastí, které jsou k dispozici pro typ prostředku, je...*  
**Příčina**: název <oblasti \> je nesprávný nebo vyžaduje žádost o přístup.  
**Alternativní řešení**: Ověřte, zda je název oblasti zadán správně. K zobrazení oblastí, ke kterým máte přístup, můžete spustit tento příkaz. Pokud oblast není v seznamu, odešlete [žádost o přístup](/troubleshoot/azure/general/region-access-request-process).

**Zpráva**: *u vnořeného prostředku nelze provést požadovanou operaci. Nepovedlo \> se najít nadřazený prostředek <imageDefinitionName (Gallery/).*  
**Příčina**: \> v aktuálním předplatném a skupině prostředků neexistuje žádná galerie s názvem <název galerie/imageDefinitionName.  
**Alternativní řešení**: Ověřte správnost názvů galerie, předplatného a skupiny prostředků. V opačném případě vytvořte novou galerii s názvem <název galerie \> nebo definice obrázku s názvem <imageDefinitionName v zadané \> skupině prostředků.

**Zpráva**: *vlastnost <parametru nelze vytvořit \> . Hodnotu <hodnoty nelze převést \> na typ &quot; System. DateTime &quot; .*  
**Příčina**: hodnota poskytnutá vlastnosti není správně formátována jako datum.  
**Alternativní řešení**: Zadejte datum ve formátu RRRR-MM-DD, rrrr-mm-dd'T'HH: mm: Sszzz nebo [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-platný formát.

**Zpráva**: *vlastnost <parametru nelze vytvořit \> . Hodnotu <hodnoty nelze převést \> na typ &quot; System. Int32 &quot; .*  
**Příčina**: <vlastnost \> přijímá pouze celočíselné hodnoty a hodnota <\> není celé číslo.  
**Alternativní řešení**: vyberte celočíselnou hodnotu.

**Zpráva**: *oblast profil publikování verze Image Galerie <publishingRegions \> musí obsahovat umístění Image verze <sourceRegion \>*  
**Příčina**: umístění zdrojového obrázku (<sourceRegion \> ) musí být zahrnuté v seznamu <publishingRegions \> .  
**Alternativní řešení**: \> v seznamu <PublishingRegions uveďte <sourceRegion \> .

**Zpráva**: *hodnota <hodnota \> <vlastnosti \> je mimo rozsah. Hodnota musí být mezi <minValue \> a <MaxValue \> , včetně.*  
**Příčina**: hodnota <\> je mimo rozsah možných hodnot pro vlastnost <\> .  
**Alternativní řešení**: vyberte hodnotu, která je v rozsahu <minValue \> a <MaxValue \> (včetně).

**Zpráva**: *zdroj <ResourceID nebyl \> nalezen. Zkontrolujte prosím, že zdroj existuje a je ve stejné oblasti jako vytvořená verze image galerie.*  
**Příčina**: není k dispozici žádný zdroj umístěný v <ResourceID \> , nebo zdroj v <ResourceID \> není ve stejné oblasti, ve které je vytvářena image galerie.  
**Alternativní řešení**: Ověřte, že hodnota <ResourceID \> je správná a že zdrojová oblast verze image galerie je shodná s oblastí <ResourceID \> .

**Zpráva**: *Změna vlastnosti ' galleryImageVersion. Properties. StorageProfile. <diskImage \> . Source.ID ' není povolena.*  
**Příčina**: ID zdroje verze image galerie se po vytvoření nedá změnit.  
**Alternativní řešení**: Zajistěte, aby ID zdroje bylo stejné jako ID existujícího zdroje, změňte číslo verze Image nebo odstraňte aktuální verzi image a zkuste to znovu.

**Zpráva**: *na discích vstupních dat se zjistila duplicitní čísla logických jednotek (LUN). Číslo logické jednotky (LUN) musí být pro každý datový disk jedinečné.*  
**Příčina**: Když vytváříte verzi Image pomocí seznamu disků nebo disků, dva nebo více disků nebo snímky disků mají stejnou logickou jednotku (LUN).  
**Alternativní řešení**: odeberte nebo změňte jakékoli duplicitní logické jednotky (LUN).

**Zpráva**: *duplicitní ID zdroje se nacházejí ve vstupních discích. ID zdroje by mělo být pro každý disk jedinečné.*  
**Příčina**: Když vytváříte verzi Image pomocí seznamu disků nebo snímků disku, dva nebo víc disků nebo snímků disku mají stejné ID prostředku.  
**Alternativní řešení**: odeberte nebo změňte všechna duplicitní ID zdroje disku.

**Zpráva**: *ID vlastnosti <ResourceID \> v cestě ' properties. storageProfile. <diskImages \> . Source.ID ' je neplatné. Očekává se plně kvalifikované ID prostředku, které začíná na '/Subscriptions/ <subscriptionID> ' nebo '/Providers/ <resourceProviderNamespace> /'.*  
**Příčina**: hodnota <ResourceID \> je nesprávně naformátovaná.  
**Alternativní řešení**: Ověřte, jestli je ID prostředku správné.

**Zpráva**: *id zdroje: <ResourceID \> musí být buď spravovaná image, virtuální počítač nebo jiná verze image galerie.*  
**Příčina**: hodnota <ResourceID \> je nesprávně naformátovaná.  
**Alternativní řešení**: Pokud jako zdrojovou bitovou kopii používáte virtuální počítač, spravovanou bitovou kopii nebo verzi image galerie, ověřte, jestli je ID prostředku virtuálního počítače, spravované Image nebo Image Galerie správné.

**Zpráva**: *id zdroje: <ResourceID \> musí být spravovaný disk nebo snímek.*  
**Příčina**: hodnota <ResourceID \> je nesprávně naformátovaná.  
**Alternativní řešení**: Pokud používáte disky a/nebo snímky disků jako zdroje pro verzi image, ověřte, jestli jsou ID prostředků disků nebo snímky disků správné.

**Zpráva**: *nejde vytvořit verzi image galerie z: <ResourceID \> , protože stav operačního systému v nadřazené imagi galerie (<OsState \_ 1 \> ) není <OsState \_ 2 \> .*  
**Příčina**: stav operačního systému (zobecněné nebo specializované) se neshoduje s stavem operačního systému zadaným v definici obrázku.  
**Alternativní řešení**: buď vyberte zdroj založený na virtuálním počítači se stavem operačního systému <OsState \_ 1 \> , nebo vytvořte novou definici image pro virtuální počítače na základě <OsState \_ 2 \> .

**Zpráva**: *prostředek s ID <ResourceID \> má jinou generaci hypervisoru [' <V # \_ 1 \> ] než generace hypervisoru image nadřazené Galerie [' <V # \_ 2 \> '].*  
**Příčina**: generace hypervisoru verze image neodpovídá generaci hypervisoru určenému v definici image. Operační systém definice Image je <V č. \_ 1 \> a verze image operačního systému je <V č \_ \> . 2.  
**Alternativní řešení**: buď vyberte zdroj se stejnou generací hypervisoru jako definici obrázku, nebo vytvořte nebo vyberte novou definici image, která má stejnou generaci hypervisoru jako verze image.

**Zpráva**: *prostředek s ID <ResourceID \> má jiný typ operačního systému [' <OsType \_ 1 \> '] než typ operačního systému image nadřazené Galerie [' <OsType \_ 2 \> '].*  
**Příčina**: generace hypervisoru verze image neodpovídá generaci hypervisoru určenému v definici image. Operační systém definice Image je <OsType \_ 1 \> a verze image operačního systému je <OsType \_ 2 \> .  
**Alternativní řešení**: buď jako definici bitové kopie vyberte zdroj se stejným operačním systémem (Linux/Windows), nebo vytvořte nebo vyberte novou definici image, která má stejnou generaci operačního systému jako verze image.

**Zpráva**: *zdrojový virtuální počítač <ResourceID \> nemůže obsahovat dočasný disk s operačním systémem.*  
**Příčina**: zdroj na <ResourceID \> obsahuje dočasný disk s operačním systémem. Galerie sdílených imagí v současné době nepodporuje dočasné disky s operačním systémem.  
**Alternativní řešení**: vyberte jiný zdroj založený na virtuálním počítači, který nepoužívá dočasný disk s operačním systémem.

**Zpráva**: *zdrojový virtuální počítač <ResourceID \> nemůže obsahovat disk [' <diskID \> '] uložený v typu účtu UltraSSD.*  
**Příčina**: disk <diskID \> je SSD úrovně Ultra disk. Galerie sdílených imagí v současné době nepodporuje SSD úrovně Ultra disky.  
**Alternativní řešení**: použijte zdroj, který obsahuje jenom SSD úrovně Premium spravované disky, SSD úrovně Standard nebo HDD úrovně Standard.

**Zpráva**: *zdrojový virtuální počítač <ResourceID \> je nutné vytvořit z Managed disks.*  
**Příčina**: virtuální počítač v <ResourceID \> používá nespravované disky.  
**Alternativní řešení**: použijte zdroj založený na virtuálním počítači, který obsahuje jenom SSD úrovně Premium spravované disky, SSD úrovně Standard nebo HDD úrovně Standard.

**Zpráva**: *na zdroji <ResourceID je moc velký počet požadavků \> . Snižte počet požadavků na zdroj nebo počkejte nějakou dobu, než se zopakuje.*  
**Příčina**: zdroj pro tuto verzi image se v tuto chvíli omezuje kvůli příliš velkému počtu požadavků.  
**Alternativní řešení**: Pokuste se vytvořit verzi image později.

**Zpráva**: *sada šifrování disku <diskEncryptionSetID \> musí být ve stejném předplatném "<subscriptionID \> " jako prostředek galerie.*  
**Příčina**: sady Disk Encryption lze použít pouze ve stejném předplatném a oblasti, ve kterých byly vytvořeny.  
**Alternativní řešení**: Vytvořte nebo použijte šifrovací sadu ve stejném předplatném a oblasti jako verzi image.

**Zpráva**: *zašifrovaný zdroj: <ResourceID \> má jiné ID předplatného než aktuální předplatné verze Image Galerie <subscriptionID \_ 1 \> . Zkuste to prosím znovu s nešifrovanými zdroji nebo použijte předplatné zdroje <subcriptionID \_ 2 \> k vytvoření verze image galerie.*  
**Příčina**: Galerie sdílených imagí v současné době nepodporuje vytváření verzí imagí v jiném předplatném z jiné zdrojové image, pokud je zdrojový obraz zašifrovaný.  
**Alternativní řešení**: použijte nešifrovaný zdroj nebo vytvořte verzi image ve stejném předplatném jako zdroj.

**Zpráva**: *nastavení šifrování disku <diskEncryptionSetID nebylo \> nalezeno.*  
**Příčina**: šifrování disku může být nesprávné.  
**Alternativní řešení**: Ověřte, jestli je ID prostředku sady pro šifrování disků správné.

**Zpráva**: *název verze Image je neplatný. Název verze Image by měl následovat po hlavní (int). Vedlejší (int). Oprava (int), například: 1.0.0, 2018.12.1 atd.*  
**Příčina**: platný formát pro verzi obrázku je tři celá čísla oddělená tečkou. Název verze image nevyhověl platnému formátu.  
**Alternativní řešení**: použijte název verze image, který následuje po formátu hlavní (int). Vedlejší (int). Patch (int). Například: 1.0.0. nebo 2018.12.1.

**Zpráva**: *hodnota parametru galleryArtifactVersion. Properties. publishingProfile. targetRegions. Encryption. dataDiskImages. diskEncryptionSetId není platná.*  
**Příčina**: ID prostředku sady šifrování disku používaného v imagi datového disku používá neplatný formát.  
**Řešení**: Zajistěte, aby ID prostředku sady šifrování disku bylo/Subscriptions/<subscriptionID \> /ResourceGroups/<ResourceGroupName \> /providers/Microsoft.COMPUTE/<diskEncryptionSetName \> .

**Zpráva**: *hodnota parametru galleryArtifactVersion. Properties. publishingProfile. targetRegions. Encryption. osDiskImage. diskEncryptionSetId není platná.*  
**Příčina**: ID prostředku sady šifrování disku použité v imagi disku operačního systému používá neplatný formát.  
**Řešení**: Zajistěte, aby ID prostředku sady šifrování disku bylo/Subscriptions/<subscriptionID \> /ResourceGroups/<ResourceGroupName \> /providers/Microsoft.COMPUTE/<diskEncryptionSetName \> .

**Zpráva**: *pro požadavek verze Image Galerie aktualizací nelze zadat novou logickou jednotku (LUN) pro šifrování dat na disku [<číslo \> ] se sadou Disk Encryption nastavenou v oblasti [<oblast \> ]. Chcete-li aktualizovat tuto verzi, odeberte novou logickou jednotku (LUN). Pokud potřebujete změnit nastavení šifrování bitové kopie dat, musíte vytvořit novou verzi image galerie se správnými nastaveními.*  
**Příčina**: Přidali jste šifrování na datový disk existující verze image. Nemůžete přidat šifrování do existující verze image.  
**Alternativní řešení**: Vytvořte novou verzi Image Galerie nebo odeberte přidané nastavení šifrování.

**Zpráva**: *zdroj verze artefaktu galerie lze zadat pouze přímo v rámci storageProfile nebo v rámci jednotlivého operačního systému nebo datových disků. Je možné zadat pouze jeden typ zdroje (bitová kopie uživatele, snímek, disk, virtuální počítač).*  
**Příčina**: chybí ID zdroje.  
**Alternativní řešení**: Zajistěte, aby bylo ID zdroje k dispozici.

**Zpráva**: *zdroj nebyl nalezen: <ResourceID \> . Ujistěte se prosím, že zdroj existuje.*  
**Příčina**: ID prostředku zdroje může být nesprávné.  
**Alternativní řešení**: Ujistěte se, že ID prostředku zdroje je správné.

**Zpráva**: *pro disk "galleryArtifactVersion. Properties. publishingProfile. targetRegions. Encryption. osDiskImage. diskEncryptionSetId" v cílové oblasti <oblasti 1 se vyžaduje sada šifrování disku, \_ \> protože \> pro odpovídající disk v oblasti <oblast \_ 2 \> se používá sada Encryption <diskEncryptionSetId* .  
**Příčina**: šifrování se použilo na disku s operačním systémem v <oblasti \_ 2 \> , ale ne v <oblasti \_ 1 \> .  
**Alternativní řešení**: Pokud používáte šifrování na disku s operačním systémem, použijte šifrování ve všech oblastech.

**Zpráva**: *pro \> \_ \> \> příslušný disk v oblasti <oblast \_ \> 2 se vyžaduje sada šifrování disku pro diskovou jednotku LUN <číslo v cílové oblasti <oblasti 1 <.*  
**Příčina**: šifrování se použilo na datovém disku na logické jednotce <číslo \> v <oblasti \_ 2 \> , ale ne v <oblasti \_ 1 \> .  
**Alternativní řešení**: Pokud používáte šifrování na datovém disku, použijte šifrování ve všech oblastech.

**Zpráva**: *\> v šifrování. dataDiskImages byla zadána neplatná logická jednotka (LUN) [<číslo]. Logická jednotka (LUN) musí být jedna z následujících hodnot [' 0, 9 '].*  
**Příčina**: logická jednotka zadaná pro šifrování neodpovídá žádné z logických jednotek (LUN) pro disky připojené k virtuálnímu počítači.  
**Alternativní řešení**: v šifrování změňte logickou jednotku (LUN) na logickou jednotku datového disku přítomného ve virtuálním počítači.

**Zpráva**: *\> v cílovém regionu ' <oblasti \> ' Encryption. dataDiskImages ' byly zadány duplicitní logické jednotky LUN ' <číslo '.*  
**Příčina**: nastavení šifrování používané v <oblasti \> zadal alespoň dvakrát více logických jednotek.  
**Alternativní řešení**: Změňte logickou jednotku v oblasti <a ujistěte se \> , že jsou všechny logické jednotky (lun) v <oblasti jedinečné \> .

**Zpráva**: *OSDiskImage a DataDiskImage nemůže ukazovat na stejný objekt BLOB <\> SourceId* .  
**Příčina**: zdroje pro disk s operačním systémem a aspoň jeden datový disk nejsou jedinečné.  
**Alternativní řešení**: změňte zdroj pro disk s operačním systémem a datové disky, abyste měli jistotu, že disk s operačním systémem i každý datový disk je jedinečný.

**Zpráva**: *v cílových oblastech publikování nejsou povoleny duplicitní oblasti.*  
**Příčina**: oblast je uvedena mezi oblastmi publikování více než jednou.  
**Alternativní řešení**: odstraňte duplicitní oblast.

**Zpráva**: *Přidání nových datových disků nebo změna logické jednotky (LUN) datového disku v existující imagi není povoleno.*  
**Příčina**: volání aktualizace verze image buď obsahuje nový datový disk, nebo má pro disk novou logickou jednotku (LUN).  
**Alternativní řešení**: použijte logické jednotky (LUN) a datové disky existující verze image.

**Zpráva**: *sada šifrování disku <diskEncryptionSetID \> musí být ve stejném předplatném <subscriptionID \> jako prostředek galerie.*  
**Příčina**: Galerie sdílených imagí v současné době nepodporuje používání šifrovacího disku nastaveného v jiném předplatném.  
**Alternativní řešení**: v rámci stejného předplatného vytvořte verzi image a sadu Encryption disk.

**Zpráva**: *replikace se v této oblasti nezdařila z důvodu, že velikost zdrojového prostředku GalleryImageVersion 2048 překračuje maximální podporovanou velikost 1024.*  
**Příčina**: datový disk ve zdroji je větší než 1 TB.  
**Alternativní řešení**: Změňte velikost datového disku na 1 TB.

## <a name="creating-or-updating-a-vm-or-scale-sets-from-an-image-version"></a>Vytvoření nebo aktualizace sady VM nebo sady škálování z verze image ##

**Zpráva**: *pro "<imageDefinitionResourceID \> " neexistuje žádná nejnovější verze bitové kopie* .  
**Příčina**: definice bitové kopie, kterou jste použili k nasazení virtuálního počítače, neobsahuje žádné verze bitové kopie, které jsou zahrnuty v nejnovější verzi.  
**Alternativní řešení**: Zajistěte, aby existovala alespoň jedna verze image, která má možnost vyloučit z nejnovější hodnoty nastavenou na hodnotu false. 

**Zpráva**: *klient má oprávnění k provedení akce "Microsoft. COMPUTE/Galerie/images/verze/čtení" v oboru <ResourceID \> , ale aktuální tenant <tenantID \> nemá autorizaci pro přístup k propojenému předplatnému <subscriptionID \> .*  
**Příčina**: virtuální počítač nebo sada škálování se vytvořily prostřednictvím image SIG v jiném tenantovi. Pokusili jste se provést změnu virtuálního počítače nebo sady škálování, ale nemáte přístup k předplatnému, které vlastní image.  
**Alternativní řešení**: Pokud chcete verzi image udělit přístup pro čtení, obraťte se na vlastníka předplatného verze image.

**Zpráva**: *Obrázek Galerie <ResourceID \> není k dispozici v oblasti <oblasti \> . Pokud chcete replikovat do této oblasti, obraťte se prosím na vlastníka image, nebo změňte požadovanou oblast.*  
**Příčina**: virtuální počítač se vytváří v oblasti, která není mezi seznamem publikovaných oblastí pro image galerie.  
**Alternativní řešení**: buď replikujte image do oblasti, nebo vytvořte virtuální počítač v jedné z oblastí v oblastech publikování obrázku galerie.

**Zpráva**: *parametr osProfile není povolen.*  
**Příčina**: pro virtuální počítač, který byl vytvořen z specializované verze bitové kopie, byly poskytnuty uživatelské jméno a heslo správce nebo klíče SSH.  
**Alternativní řešení**: Pokud máte v úmyslu vytvořit virtuální počítač z této image, nezahrnujte uživatelské jméno a heslo správce ani klíče SSH. V opačném případě použijte zobecněnou verzi image a zadejte uživatelské jméno, heslo nebo klíče SSH pro správce.

**Zpráva**: *povinný parametr osProfile chybí (null).*  
**Příčina**: virtuální počítač se vytvoří z generalizované image a chybí uživatelské jméno správce, heslo nebo klíče SSH. Vzhledem k tomu, že generalizované image neuchovávají uživatelské jméno správce, heslo nebo klíče SSH, musí být tato pole zadaná při vytváření virtuálního počítače nebo sady škálování.  
**Alternativní řešení**: zadejte uživatelské jméno správce, heslo nebo klíče SSH nebo použijte specializovanou verzi image.

**Zpráva**: *nejde vytvořit verzi image galerie z: <ResourceID \> , protože stav operačního systému v nadřazené imagi galerie ("specializované") není zobecněný.*  
**Příčina**: verze obrázku je vytvořena z zobecněného zdroje, ale jeho Nadřazená definice je specializovaná.  
**Alternativní řešení**: buď vytvořte verzi Image pomocí specializovaného zdroje, nebo použijte nadřazenou definici, která je zobecněná.

**Zpráva**: *nejde aktualizovat sadu škálování virtuálního počítače <vmssName \> , protože aktuální stav operačního systému sady škálování virtuálního počítače je zobecněný, který se liší od aktualizovaného stavu operačního systému image galerie, který je specializovaný.*  
**Příčina**: aktuální zdrojová image pro sadu škálování je zobecněná zdrojová image, ale aktualizuje se zdrojovou imagí, která je specializovaná. Aktuální zdrojová image a nová zdrojová image pro sadu škálování musí být ve stejném stavu.  
**Alternativní řešení**: Pokud chcete aktualizovat sadu škálování, použijte zobecněnou verzi image.

**Zpráva**: *sada šifrování disků <diskEncryptionSetID \> v galerii sdílených imagí <versionID \> patří k předplatnému <SubscriptionId \_ 1 \> a nelze ji použít s prostředkem v předplatném <SubscriptionId \_ 2 \>* .  
**Příčina**: sada šifrování disků, která se používá k šifrování verze image, se nachází v jiném předplatném, než je předplatné pro hostování verze image.  
**Alternativní řešení**: použijte stejné předplatné pro verzi image a sadu šifrování disku.

**Zpráva**: *Vytvoření virtuálního počítače nebo sady škálování virtuálního počítače trvá dlouhou dobu.*  
**Alternativní řešení**: Ověřte, že **OSType** verze image, ze které se pokoušíte vytvořit virtuální počítač nebo sadu škálování virtuálního počítače, z nástroje má stejný **OSType** zdroje, který jste použili k vytvoření verze image. 

**Zpráva**: *prostředek s ID <vmID \> má jiný plán [' { \" name \" : \" <name> \" , \" Publisher \" : \" <publisher> \" , \" Product \" : \" <product> \" , \" promotionCode \" : \" <promotionCode> \" } '] než plán obrázku nadřazené Galerie [' null '].*  
**Příčina**: definice nadřazené image pro nasazenou verzi image neobsahuje informace o plánu nákupu.  
**Alternativní řešení**: vytvořte definici image se stejnými podrobnostmi plánu nákupu z chybové zprávy a vytvořte verzi image v rámci definice image.


## <a name="creating-a-disk-from-an-image-version"></a>Vytvoření disku z verze image ##

**Zpráva**: *hodnota parametru element imagereference je neplatná.*  
**Příčina**: Pokusili jste se exportovat z verze image SIG na disk, ale použili jste pozici logické jednotky (LUN), která v imagi neexistuje.    
**Alternativní řešení**: Podívejte se na verzi obrázku a podívejte se, jaké pozice LUN se používají.

## <a name="sharing-resources"></a>Sdílení prostředků

Sdílení prostředků Galerie imagí, definice obrázku a verzí obrázků v rámci předplatných je povolené pomocí [řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/rbac-and-directory-admin-roles.md). 

## <a name="replication-speed"></a>Rychlost replikace

Pomocí příznaku **--expand ReplicationStatus** můžete ověřit, jestli se dokončila replikace do všech zadaných cílových oblastí. Pokud ne, počkejte až šest hodin, než se úloha dokončí. Pokud dojde k chybě, znovu spusťte příkaz pro vytvoření a replikaci verze image. Pokud existuje mnoho cílových oblastí, na které se replikuje verze bitové kopie, zvažte provedení replikace ve fázích.

## <a name="azure-limits-and-quotas"></a>Omezení a kvóty Azure 

[Omezení a kvóty Azure](../azure-resource-manager/management/azure-subscription-service-limits.md) se vztahují na všechny prostředky pro galerii sdílených imagí, definice obrázků a verze imagí. Ujistěte se, že jste v omezeních pro vaše předplatná. 


## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [galeriích sdílených imagí](./shared-image-galleries.md).