---
title: Řešení potíží se sdílenými bitovými kopiemi v Azure
description: Naučte se řešit problémy s galeriemi sdílených imagí.
author: olayemio
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: troubleshooting
ms.workload: infrastructure
ms.date: 10/27/2020
ms.author: olayemio
ms.reviewer: cynthn
ms.openlocfilehash: 74e1dc12be9bfec57d76357a335b46a55912f6df
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500270"
---
# <a name="troubleshoot-shared-image-galleries-in-azure"></a>Řešení potíží s galeriemi sdílených imagí v Azure

Pokud máte problémy s prováděním jakýchkoli operací v galeriích sdílených imagí, definicích imagí a verzích obrázků, spusťte příkaz po chybě znovu v režimu ladění. Režim ladění aktivujete tak, že předáte `--debug` přepínač s rozhraním příkazového řádku Azure a `-Debug` přepnete do PowerShellu. Po vyhledání chyby použijte tento článek k vyřešení potíží.


## <a name="creating-or-modifying-a-gallery"></a>Vytvoření nebo úprava galerie ##

*Název galerie je neplatný. Povolené znaky jsou anglické alfanumerické znaky, s podtržítky a tečkami povolenými uprostřed, maximálně 80 znaků. Všechny ostatní speciální znaky, včetně pomlček, nejsou povoleny.*  
**Příčina**: název galerie nesplňuje požadavky na pojmenování.  
**Alternativní řešení**: vyberte název, který splňuje tyto podmínky: 
- Má limit 80 znaků
- Obsahuje jenom anglická písmena, číslice, podtržítka a tečky.
- Začíná a končí anglicky písmeny nebo číslicemi.

*Název entity ' název galerie ' je podle jeho ověřovacího pravidla neplatný: ^ [^ \_ \w] [\w-. \_ ] {0,79} (? <! [-.]) $.*  
**Příčina**: název galerie nesplňuje požadavky na pojmenování.  
**Alternativní řešení**: vyberte název galerie, který splňuje následující podmínky: 
- Má limit 80 znaků
- Obsahuje jenom anglická písmena, číslice, podtržítka a tečky.
- Začíná a končí anglicky písmeny nebo číslicemi.

*Zadaný název prostředku <název galerie \> má neplatné koncové znaky: <znak \> . Název nemůže končit znaky: <znak.\>*  
**Příčina**: název galerie končí tečkou nebo podtržítkem.  
**Alternativní řešení**: vyberte název galerie, který splňuje následující podmínky: 
- Má limit 80 znaků
- Obsahuje jenom anglická písmena, číslice, podtržítka a tečky.
- Začíná a končí anglicky písmeny nebo číslicemi.

*Zadané umístění <oblasti \> není k dispozici pro typ prostředku Microsoft. COMPUTE/galerie. Seznam oblastí, které jsou k dispozici pro typ prostředku, je...*  
**Příčina**: oblast zadaná pro galerii je nesprávná nebo vyžaduje žádost o přístup.  
**Alternativní řešení**: Ověřte, zda je název oblasti zadán správně. K zobrazení oblastí, ke kterým máte přístup, můžete spustit tento příkaz. Pokud oblast není v seznamu, odešlete [žádost o přístup](/troubleshoot/azure/general/region-access-request-process).

*Prostředek nelze odstranit před odstraněním vnořených prostředků.*  
**Příčina**: Pokusili jste se odstranit galerii, která obsahuje alespoň jednu existující definici obrázku. Galerie musí být prázdná, aby ji bylo možné odstranit.  
**Alternativní řešení**: Odstraňte všechny definice obrázků v galerii a pak pokračujte v odstraňování galerie. Pokud definice image obsahuje verze imagí, je nutné před odstraněním definic imagí odstranit verze imagí.

*Prostředek <Gallery \> již existuje v umístění <oblasti \_ 1 \> ve skupině prostředků <skupina prostředků \> . Prostředek se stejným názvem se nedá vytvořit v umístění <oblasti \_ 2 \> . Vyberte prosím nový název prostředku.*  
**Příčina**: ve skupině prostředků máte existující galerii se stejným názvem a pokusili jste se vytvořit další galerii se stejným názvem, ale v jiné oblasti.  
**Alternativní řešení**: buď použijte jinou galerii, nebo použijte jinou skupinu prostředků.

## <a name="creating-or-modifying-image-definitions"></a>Vytvoření nebo úprava definicí obrázků ##

*Změna vlastnosti ' galleryImage. Properties. <Property \> ' není povolena.*  
**Příčina**: Pokusili jste se změnit typ operačního systému, stav operačního systému, generaci technologie Hyper-V, nabídka, Vydavatel nebo SKU. Změna kterékoli z těchto vlastností není povolená.  
**Alternativní řešení**: místo toho vytvořte definici nové image.

*Prostředek <Gallery/imageDefinitionName \> již existuje v umístění <oblasti \_ 1 \> ve skupině prostředků <zdroj \> . Prostředek se stejným názvem se nedá vytvořit v umístění <oblasti \_ 2 \> . Vyberte prosím nový název prostředku.*  
**Příčina**: máte existující definici image ve stejné galerii a skupině prostředků se stejným názvem. Pokusili jste se vytvořit další definici obrázku se stejným názvem a ve stejné galerii, ale v jiné oblasti.  
**Alternativní řešení**: použijte jiný název definice obrázku nebo vložte definici image do jiné galerie nebo skupiny prostředků.

*Zadaný název prostředku <Gallery \> /<imageDefinitionName \> obsahuje tyto neplatné koncové znaky: <znak \> . Název nemůže končit znaky: <znak.\>*  
**Příčina**: <název imageDefinitionName \> končí tečkou nebo podtržítkem.  
**Alternativní řešení**: vyberte název definice obrázku, který splňuje tyto podmínky: 
- Má limit 80 znaků
- Obsahuje jenom anglická písmena, číslice, podtržítka, spojovníky a tečky.
- Začíná a končí anglicky písmeny nebo čísly.

*Název entity <imageDefinitionName \> je podle jeho ověřovacího pravidla neplatný: ^ [^ \_ \\ W] [ \\ W-. \_ ] {0,79} (? <! [-.]) $"*  
**Příčina**: <název imageDefinitionName \> končí tečkou nebo podtržítkem.  
**Alternativní řešení**: vyberte název definice obrázku, který splňuje tyto podmínky: 
- Má limit 80 znaků
- Obsahuje jenom anglická písmena, číslice, podtržítka, spojovníky a tečky.
- Začíná a končí anglicky písmeny nebo číslicemi.

*Vlastnost názvu assetu galleryImage. Properties. Identifier. <není \> platná. Nemůže být prázdný. Povolené znaky jsou velká a malá písmena, číslice, spojovník (-), tečka (.), podtržítko ( \_ ). Názvům nesmí končit tečkou (.). Délka názvu nesmí překročit <é číselné \> znaky.*  
**Příčina**: hodnota vydavatele, nabídky nebo SKU nesplňuje požadavky na pojmenování.  
**Alternativní řešení**: vyberte hodnotu, která splňuje tyto podmínky: 
- Má limit 128 znaků pro vydavatele nebo 64 omezení pro nabídku a SKU
- Obsahuje jenom anglická písmena, číslice, spojovníky, podtržítka a tečky.
- Nekončí tečkou

*U vnořeného prostředku nelze provést požadovanou operaci. Nepovedlo se najít nadřazený <prostředku \> .*  
**Příčina**: \> v aktuálním předplatném a skupině prostředků neexistuje žádná galerie s názvem <název galerie.  
**Alternativní řešení**: Ověřte správnost názvů galerie, předplatného a skupiny prostředků. V opačném případě vytvořte novou galerii s názvem <název galerie \> .

*Zadané umístění <oblasti \> není k dispozici pro typ prostředku Microsoft. COMPUTE/galerie. Seznam oblastí, které jsou k dispozici pro typ prostředku, je...*  
**Příčina**: název <oblasti \> je nesprávný nebo vyžaduje žádost o přístup.  
**Alternativní řešení**: Ověřte, zda je název oblasti zadán správně. K zobrazení oblastí, ke kterým máte přístup, můžete spustit tento příkaz. Pokud oblast není v seznamu, odešlete [žádost o přístup](/troubleshoot/azure/general/region-access-request-process).

*Nelze serializovat hodnotu: hodnota <\> jako typ: ' ISO-8601 '. ISO8601Error: iso 8601 doba označení t ' chybí. Nedá se analyzovat hodnota <řetězců DateTime.\>*  
**Příčina**: hodnota poskytnutá vlastnosti není správně formátována jako datum.  
**Alternativní řešení**: Zadejte datum ve formátu RRRR-MM-DD, rrrr-mm-dd'T'HH: mm: Sszzz nebo [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-platný formát.

*Nelze převést řetězec na hodnotu DateTimeOffset: <Value \> . Cesta ' Properties. <vlastnost \> '*  
**Příčina**: hodnota poskytnutá vlastnosti není správně formátována jako datum.  
**Alternativní řešení**: Zadejte datum ve formátu RRRR-MM-DD, rrrr-mm-dd'T'HH: mm: Sszzz nebo [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-platný formát.

*EndOfLifeDate musí být nastavené na budoucí datum.*  
**Příčina**: vlastnost datum ukončení životnosti není správně formátována jako datum, které následuje po dnešním datu.  
**Alternativní řešení**: Zadejte datum ve formátu RRRR-MM-DD, rrrr-mm-dd'T'HH: mm: Sszzz nebo [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-platný formát.

*Argument--<vlastnost \> : Neplatná hodnota typu int: <hodnota\>*  
**Příčina**: <vlastnost \> přijímá pouze celočíselné hodnoty a hodnota <\> není celé číslo.  
**Alternativní řešení**: vyberte celočíselnou hodnotu.

*Minimální hodnota vlastnosti <\> nesmí být větší než maximální hodnota vlastnosti <\> .*  
**Příčina**: minimální hodnota zadaná pro vlastnost <\> je vyšší než maximální hodnota poskytnutá pro vlastnost <\> .  
**Alternativní řešení**: Změňte hodnoty tak, aby minimum bylo menší nebo rovno maximální hodnotě.

*Obrázek Galerie: <imageDefinitionName \> identifikoval (Vydavatel: <Publisher \> , nabídka: <nabídka \> , SKU: <SKU \> ) již existuje. Vyberte jiného vydavatele, nabídku, kombinaci SKU.*  
**Příčina**: Pokusili jste se vytvořit novou definici image stejného vydavatele, nabídky a SKU s trojicí jako existující definici obrázku ve stejné galerii.  
**Alternativní řešení**: v rámci Galerie musí mít všechny definice obrázků jedinečnou kombinaci položek Vydavatel, nabídka a SKU. Vyberte jedinečnou kombinaci nebo vyberte novou galerii a znovu vytvořte definici image.

*Prostředek nelze odstranit před odstraněním vnořených prostředků.*  
**Příčina**: Pokusili jste se odstranit definici image, která obsahuje verze imagí. Definice obrázku musí být prázdná, aby ji bylo možné odstranit.  
**Alternativní řešení**: Odstraňte všechny verze imagí v definici image a pak pokračujte v odstranění definice image.

*Nelze vytvořit vazby vlastnosti <parametru \> . Hodnotu <hodnoty nelze převést \> na typ <PropertyType \> . Název identifikátoru <hodnoty nelze porovnat \> s platným názvem enumerátoru. Zadejte jeden z následujících názvů enumerátorů a akci opakujte: <choice1 \> , <choice2 \> ,...*  
**Příčina**: vlastnost má omezený seznam možných hodnot a <hodnota není \> jednou z nich.  
**Alternativní řešení**: zvolte jednu z možných <\> hodnoty volby.

*Nelze vytvořit vazby vlastnosti <parametru \> . Hodnotu <hodnoty nelze převést \> na typ &quot; System. DateTime.&quot;*  
**Příčina**: hodnota poskytnutá vlastnosti není správně formátována jako datum.  
**Alternativní řešení**: Zadejte datum ve formátu RRRR-MM-DD, rrrr-mm-dd'T'HH: mm: Sszzz nebo [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-platný formát.

*Nelze vytvořit vazby vlastnosti <parametru \> . Hodnotu <hodnoty nelze převést \> na typ &quot; System. Int32.&quot;*  
**Příčina**: <vlastnost \> přijímá pouze celočíselné hodnoty a hodnota <\> není celé číslo.  
**Alternativní řešení**: vyberte celočíselnou hodnotu.

*Typ účtu úložiště ZRS se v této oblasti nepodporuje.*  
**Příčina**: v oblasti, která ji ještě nepodporuje, jste zvolili standardní úložiště ZRS (zóna – redundantní úložiště).  
**Alternativní řešení**: Změňte typ účtu úložiště na **Premium \_ LRS** nebo **standard \_ LRS**. V naší dokumentaci najdete nejnovější [seznam oblastí](../storage/common/storage-redundancy.md#zone-redundant-storage) s povoleným ZRS Preview.

## <a name="creating-or-updating-image-versions"></a>Vytváření a aktualizace verzí imagí ##

*Zadané umístění <oblasti \> není k dispozici pro typ prostředku Microsoft. COMPUTE/galerie. Seznam oblastí, které jsou k dispozici pro typ prostředku, je...*  
**Příčina**: název <oblasti \> je nesprávný nebo vyžaduje žádost o přístup.  
**Alternativní řešení**: Ověřte, zda je název oblasti zadán správně. K zobrazení oblastí, ke kterým máte přístup, můžete spustit tento příkaz. Pokud oblast není v seznamu, odešlete [žádost o přístup](/troubleshoot/azure/general/region-access-request-process).

*U vnořeného prostředku nelze provést požadovanou operaci. Nepovedlo se najít nadřazený prostředek <imageDefinitionName (Gallery/) \> .*  
**Příčina**: \> v aktuálním předplatném a skupině prostředků neexistuje žádná galerie s názvem <název galerie/imageDefinitionName.  
**Alternativní řešení**: Ověřte správnost názvů galerie, předplatného a skupiny prostředků. V opačném případě vytvořte novou galerii s názvem <název galerie \> nebo definice obrázku s názvem <imageDefinitionName v zadané \> skupině prostředků.

*Nelze vytvořit vazby vlastnosti <parametru \> . Hodnotu <hodnoty nelze převést \> na typ &quot; System. DateTime.&quot;*  
**Příčina**: hodnota poskytnutá vlastnosti není správně formátována jako datum.  
**Alternativní řešení**: Zadejte datum ve formátu RRRR-MM-DD, rrrr-mm-dd'T'HH: mm: Sszzz nebo [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-platný formát.

*Nelze vytvořit vazby vlastnosti <parametru \> . Hodnotu <hodnoty nelze převést \> na typ &quot; System. Int32.&quot;*  
**Příčina**: <vlastnost \> přijímá pouze celočíselné hodnoty a hodnota <\> není celé číslo.  
**Alternativní řešení**: vyberte celočíselnou hodnotu.

*Verze image v galerii – oblasti profil publikování <publishingRegions \> musí obsahovat umístění verze image <sourceRegion\>*  
**Příčina**: umístění zdrojového obrázku (<sourceRegion \> ) musí být zahrnuté v seznamu <publishingRegions \> .  
**Alternativní řešení**: \> v seznamu <PublishingRegions uveďte <sourceRegion \> .

*Hodnota <hodnota \> vlastnosti <parametru \> je mimo rozsah. Hodnota musí být mezi <minValue \> a <MaxValue \> , včetně.*  
**Příčina**: hodnota <\> je mimo rozsah možných hodnot pro vlastnost <\> .  
**Alternativní řešení**: vyberte hodnotu, která je v rozsahu <minValue \> a <MaxValue \> (včetně).

*Zdroj <resourceID nebyl \> nalezen. Zkontrolujte prosím, že zdroj existuje a je ve stejné oblasti jako vytvořená verze image galerie.*  
**Příčina**: není k dispozici žádný zdroj umístěný v <ResourceID \> , nebo zdroj v <ResourceID \> není ve stejné oblasti, ve které je vytvářena image galerie.  
**Alternativní řešení**: Ověřte, že hodnota <ResourceID \> je správná a že zdrojová oblast verze image galerie je shodná s oblastí <ResourceID \> .

*Změna vlastnosti ' galleryImageVersion. Properties. storageProfile. <diskImage \> . Source.ID ' není povolena.*  
**Příčina**: ID zdroje verze image galerie se po vytvoření nedá změnit.  
**Alternativní řešení**: Zajistěte, aby ID zdroje bylo stejné jako ID existujícího zdroje, změňte číslo verze Image nebo odstraňte aktuální verzi image a zkuste to znovu.

*V discích vstupních dat se zjistila duplicitní čísla logických jednotek (LUN). Číslo logické jednotky (LUN) musí být pro každý datový disk jedinečné.*  
**Příčina**: Když vytváříte verzi Image pomocí seznamu disků nebo disků, dva nebo více disků nebo snímky disků mají stejnou logickou jednotku (LUN).  
**Alternativní řešení**: odeberte nebo změňte jakékoli duplicitní logické jednotky (LUN).

*Ve vstupních discích se nacházejí duplicitní ID zdroje. ID zdroje by mělo být pro každý disk jedinečné.*  
**Příčina**: Když vytváříte verzi Image pomocí seznamu disků nebo snímků disku, dva nebo víc disků nebo snímků disku mají stejné ID prostředku.  
**Alternativní řešení**: odeberte nebo změňte všechna duplicitní ID zdroje disku.

*ID vlastnosti <resourceID \> v cestě ' Properties. storageProfile. <diskImages \> . Source.ID ' je neplatné. Očekávalo se plně kvalifikované ID prostředku, které začíná na '/subscriptions/{subscriptionId} ' nebo '/providers/{resourceProviderNamespace}/'.*  
**Příčina**: hodnota <ResourceID \> je nesprávně naformátovaná.  
**Alternativní řešení**: Ověřte, jestli je ID prostředku správné.

*ID zdroje: <resourceID \> musí být buď spravovaná image, virtuální počítač nebo jiná verze image galerie.*  
**Příčina**: hodnota <ResourceID \> je nesprávně naformátovaná.  
**Alternativní řešení**: Pokud jako zdrojovou bitovou kopii používáte virtuální počítač, spravovanou bitovou kopii nebo verzi image galerie, ověřte, jestli je ID prostředku virtuálního počítače, spravované Image nebo Image Galerie správné.

*ID zdroje: <resourceID \> musí být spravovaný disk nebo snímek.*  
**Příčina**: hodnota <ResourceID \> je nesprávně naformátovaná.  
**Alternativní řešení**: Pokud používáte disky a/nebo snímky disků jako zdroje pro verzi image, ověřte, jestli jsou ID prostředků disků nebo snímky disků správné.

*Nejde vytvořit verzi image galerie z: <resourceID \> , protože stav operačního systému v nadřazené imagi galerie (<OsState \_ 1 \> ) není <OsState \_ 2 \> .*  
**Příčina**: stav operačního systému (zobecněné nebo specializované) se neshoduje s stavem operačního systému zadaným v definici obrázku.  
**Alternativní řešení**: buď vyberte zdroj založený na virtuálním počítači se stavem operačního systému <OsState \_ 1 \> , nebo vytvořte novou definici image pro virtuální počítače na základě <OsState \_ 2 \> .

*Prostředek s ID <resourceID \> má jinou generaci hypervisoru [' <V # \_ 1 \> ] než generace hypervisoru image nadřazené galerie [' <V # \_ 2 \> '].*  
**Příčina**: generace hypervisoru verze image neodpovídá generaci hypervisoru určenému v definici image. Operační systém definice Image je <V č. \_ 1 \> a verze image operačního systému je <V č \_ \> . 2.  
**Alternativní řešení**: buď vyberte zdroj se stejnou generací hypervisoru jako definici obrázku, nebo vytvořte nebo vyberte novou definici image, která má stejnou generaci hypervisoru jako verze image.

*Prostředek s ID <resourceID \> má jiný typ operačního systému [' <OsType \_ 1 \> '] než typ operačního systému image nadřazené galerie [' <OsType \_ 2 \> '].*  
**Příčina**: generace hypervisoru verze image neodpovídá generaci hypervisoru určenému v definici image. Operační systém definice Image je <OsType \_ 1 \> a verze image operačního systému je <OsType \_ 2 \> .  
**Alternativní řešení**: buď jako definici bitové kopie vyberte zdroj se stejným operačním systémem (Linux/Windows), nebo vytvořte nebo vyberte novou definici image, která má stejnou generaci operačního systému jako verze image.

*Zdrojový virtuální počítač <resourceID \> nemůže obsahovat dočasný disk s operačním systémem.*  
**Příčina**: zdroj na <ResourceID \> obsahuje dočasný disk s operačním systémem. Galerie sdílených imagí v současné době nepodporuje dočasné disky s operačním systémem.  
**Alternativní řešení**: vyberte jiný zdroj založený na virtuálním počítači, který nepoužívá dočasný disk s operačním systémem.

*Zdrojový virtuální počítač <resourceID \> nemůže obsahovat disk [' <diskID \> '] uložený v typu účtu UltraSSD.*  
**Příčina**: disk <diskID \> je SSD úrovně Ultra disk. Galerie sdílených imagí v současné době nepodporuje SSD úrovně Ultra disky.  
**Alternativní řešení**: použijte zdroj, který obsahuje jenom SSD úrovně Premium spravované disky, SSD úrovně Standard nebo HDD úrovně Standard.

*Zdrojový virtuální počítač <resourceID \> je nutné vytvořit z Managed disks.*  
**Příčina**: virtuální počítač v <ResourceID \> používá nespravované disky.  
**Alternativní řešení**: použijte zdroj založený na virtuálním počítači, který obsahuje jenom SSD úrovně Premium spravované disky, SSD úrovně Standard nebo HDD úrovně Standard.

*Ve zdroji <resourceID je moc velký počet požadavků \> . Snižte počet požadavků na zdroj nebo počkejte nějakou dobu, než se zopakuje.*  
**Příčina**: zdroj pro tuto verzi image se v tuto chvíli omezuje kvůli příliš velkému počtu požadavků.  
**Alternativní řešení**: Pokuste se vytvořit verzi image později.

*Sada šifrování disku <diskEncryptionSetID \> musí být ve stejném předplatném "<subscriptionID \> " jako prostředek galerie.*  
**Příčina**: sady Disk Encryption lze použít pouze ve stejném předplatném a oblasti, ve kterých byly vytvořeny.  
**Alternativní řešení**: Vytvořte nebo použijte šifrovací sadu ve stejném předplatném a oblasti jako verzi image.

*Zašifrovaný zdroj: <resourceID \> má jiné ID předplatného než aktuální předplatné verze image galerie <subscriptionID \_ 1 \> . Zkuste to prosím znovu s nešifrovanými zdroji nebo použijte předplatné zdroje <subcriptionID \_ 2 \> k vytvoření verze image galerie.*  
**Příčina**: Galerie sdílených imagí v současné době nepodporuje vytváření verzí imagí v jiném předplatném z jiné zdrojové image, pokud je zdrojový obraz zašifrovaný.  
**Alternativní řešení**: použijte nešifrovaný zdroj nebo vytvořte verzi image ve stejném předplatném jako zdroj.

*Sada šifrování disku <diskEncryptionSetID nebyla \> nalezena.*  
**Příčina**: šifrování disku může být nesprávné.  
**Alternativní řešení**: Ověřte, jestli je ID prostředku sady pro šifrování disků správné.

*Název verze Image je neplatný. Název verze Image by měl následovat po hlavní (int). Vedlejší (int). Oprava (int), například: 1.0.0, 2018.12.1 atd.*  
**Příčina**: platný formát pro verzi obrázku je tři celá čísla oddělená tečkou. Název verze image nevyhověl platnému formátu.  
**Alternativní řešení**: použijte název verze image, který následuje po formátu hlavní (int). Vedlejší (int). Patch (int). Například: 1.0.0. nebo 2018.12.1.

*Hodnota parametru galleryArtifactVersion. Properties. publishingProfile. targetRegions. Encryption. dataDiskImages. diskEncryptionSetId není platná.*  
**Příčina**: ID prostředku sady šifrování disku používaného v imagi datového disku používá neplatný formát.  
**Řešení**: Zajistěte, aby ID prostředku sady šifrování disku bylo/Subscriptions/<subscriptionID \> /ResourceGroups/<ResourceGroupName \> /providers/Microsoft.COMPUTE/<diskEncryptionSetName \> .

*Hodnota parametru galleryArtifactVersion. Properties. publishingProfile. targetRegions. Encryption. osDiskImage. diskEncryptionSetId není platná.*  
**Příčina**: ID prostředku sady šifrování disku použité v imagi disku operačního systému používá neplatný formát.  
**Řešení**: Zajistěte, aby ID prostředku sady šifrování disku bylo/Subscriptions/<subscriptionID \> /ResourceGroups/<ResourceGroupName \> /providers/Microsoft.COMPUTE/<diskEncryptionSetName \> .

*Nelze zadat novou logickou jednotku (LUN) pro šifrování dat na disku [<číslo \> ] se sadou Disk Encryption nastavenou v oblasti [<oblast \> ] pro požadavek verze Image Galerie aktualizace. Chcete-li aktualizovat tuto verzi, odeberte novou logickou jednotku (LUN). Pokud potřebujete změnit nastavení šifrování bitové kopie dat, musíte vytvořit novou verzi image galerie se správnými nastaveními.*  
**Příčina**: Přidali jste šifrování na datový disk existující verze image. Nemůžete přidat šifrování do existující verze image.  
**Alternativní řešení**: Vytvořte novou verzi Image Galerie nebo odeberte přidané nastavení šifrování.

*Zdroj verze artefaktu galerie lze zadat pouze přímo v rámci storageProfile nebo v rámci jednotlivého operačního systému nebo datových disků. Je možné zadat pouze jeden typ zdroje (bitová kopie uživatele, snímek, disk, virtuální počítač).*  
**Příčina**: chybí ID zdroje.  
**Alternativní řešení**: Zajistěte, aby bylo ID zdroje k dispozici.

*Zdroj nebyl nalezen: <resourceID \> . Ujistěte se prosím, že zdroj existuje.*  
**Příčina**: ID prostředku zdroje může být nesprávné.  
**Alternativní řešení**: Ujistěte se, že ID prostředku zdroje je správné.

*Pro disk "galleryArtifactVersion. Properties. publishingProfile. targetRegions. Encryption. osDiskImage. diskEncryptionSetId" v cílové oblasti <oblasti 1 se vyžaduje sada šifrování disku, \_ \> protože sada disk Encryption <diskEncryptionSetId \> se používá pro odpovídající disk v oblasti <region \_ 2 \> .*  
**Příčina**: šifrování se použilo na disku s operačním systémem v <oblasti \_ 2 \> , ale ne v <oblasti \_ 1 \> .  
**Alternativní řešení**: Pokud používáte šifrování na disku s operačním systémem, použijte šifrování ve všech oblastech.

*Pro diskovou jednotku LUN <číslo \> v cílové oblasti <oblasti 1 se vyžaduje sada šifrování disku, \_ \> protože \> pro odpovídající disk v oblasti <oblasti 2 se používá šifrovací sada disků <diskEncryptionSetID. \_ \>*  
**Příčina**: šifrování se použilo na datovém disku na logické jednotce <číslo \> v <oblasti \_ 2 \> , ale ne v <oblasti \_ 1 \> .  
**Alternativní řešení**: Pokud používáte šifrování na datovém disku, použijte šifrování ve všech oblastech.

*V šifrování. dataDiskImages byla zadána neplatná logická jednotka (LUN) [číslo <\> ]. Logická jednotka (LUN) musí být jedna z následujících hodnot [' 0, 9 '].*  
**Příčina**: logická jednotka zadaná pro šifrování neodpovídá žádné z logických jednotek (LUN) pro disky připojené k virtuálnímu počítači.  
**Alternativní řešení**: v šifrování změňte logickou jednotku (LUN) na logickou jednotku datového disku přítomného ve virtuálním počítači.

*\>V cílovém regionu ' <oblasti \> ' Encryption. dataDiskImages ' byly zadány duplicitní logické jednotky lun ' <číslo '.*  
**Příčina**: nastavení šifrování používané v <oblasti \> zadal alespoň dvakrát více logických jednotek.  
**Alternativní řešení**: Změňte logickou jednotku v oblasti <a ujistěte se \> , že jsou všechny logické jednotky (lun) v <oblasti jedinečné \> .

*OSDiskImage a DataDiskImage nemohou odkazovat na stejný objekt BLOB <sourceID.\>*  
**Příčina**: zdroje pro disk s operačním systémem a aspoň jeden datový disk nejsou jedinečné.  
**Alternativní řešení**: změňte zdroj pro disk s operačním systémem a datové disky, abyste měli jistotu, že disk s operačním systémem i každý datový disk je jedinečný.

*V cílových oblastech publikování nejsou povoleny duplicitní oblasti.*  
**Příčina**: oblast je uvedena mezi oblastmi publikování více než jednou.  
**Alternativní řešení**: odstraňte duplicitní oblast.

*Přidávání nových datových disků nebo změna logické jednotky (LUN) datového disku v existující imagi není povoleno.*  
**Příčina**: volání aktualizace verze image buď obsahuje nový datový disk, nebo má pro disk novou logickou jednotku (LUN).  
**Alternativní řešení**: použijte logické jednotky (LUN) a datové disky existující verze image.

*Sada šifrování disku <diskEncryptionSetID \> musí být ve stejném předplatném <subscriptionID \> jako prostředek galerie.*  
**Příčina**: Galerie sdílených imagí v současné době nepodporuje používání šifrovacího disku nastaveného v jiném předplatném.  
**Alternativní řešení**: v rámci stejného předplatného vytvořte verzi image a sadu Encryption disk.

## <a name="creating-or-updating-a-vm-or-scale-sets-from-an-image-version"></a>Vytvoření nebo aktualizace sady VM nebo sady škálování z verze image ##

*Klient má oprávnění k provedení akce "Microsoft. COMPUTE/galeriích/imagí/verze/číst" v oboru <resourceID \> , ale aktuální tenant <tenantId1 \> nemá autorizaci pro přístup k propojenému předplatnému <subscriptionId2 \> .*  
**Příčina**: virtuální počítač nebo sada škálování se vytvořily prostřednictvím image SIG v jiném tenantovi. Pokusili jste se provést změnu virtuálního počítače nebo sady škálování, ale nemáte přístup k předplatnému, které vlastní image.  
**Alternativní řešení**: Pokud chcete verzi image udělit přístup pro čtení, obraťte se na vlastníka předplatného verze image.

*Obrázek Galerie <resourceID není \> k dispozici v oblasti <oblasti \> . Pokud chcete replikovat do této oblasti, obraťte se prosím na vlastníka image, nebo změňte požadovanou oblast.*  
**Příčina**: virtuální počítač se vytváří v oblasti, která není mezi seznamem publikovaných oblastí pro image galerie.  
**Alternativní řešení**: buď replikujte image do oblasti, nebo vytvořte virtuální počítač v jedné z oblastí v oblastech publikování obrázku galerie.

*Parametr osProfile není povolený.*  
**Příčina**: pro virtuální počítač, který byl vytvořen z specializované verze bitové kopie, byly poskytnuty uživatelské jméno a heslo správce nebo klíče SSH.  
**Alternativní řešení**: Pokud máte v úmyslu vytvořit virtuální počítač z této image, nezahrnujte uživatelské jméno a heslo správce ani klíče SSH. V opačném případě použijte zobecněnou verzi image a zadejte uživatelské jméno, heslo nebo klíče SSH pro správce.

*Povinný parametr osProfile chybí (null).*  
**Příčina**: virtuální počítač se vytvoří z generalizované image a chybí uživatelské jméno správce, heslo nebo klíče SSH. Vzhledem k tomu, že generalizované image neuchovávají uživatelské jméno správce, heslo nebo klíče SSH, musí být tato pole zadaná při vytváření virtuálního počítače nebo sady škálování.  
**Alternativní řešení**: zadejte uživatelské jméno správce, heslo nebo klíče SSH nebo použijte specializovanou verzi image.

*Nejde vytvořit verzi image galerie z: <resourceID, \> protože stav operačního systému v nadřazené imagi galerie ("specializované") není zobecněný.*  
**Příčina**: verze obrázku je vytvořena z zobecněného zdroje, ale jeho Nadřazená definice je specializovaná.  
**Alternativní řešení**: buď vytvořte verzi Image pomocí specializovaného zdroje, nebo použijte nadřazenou definici, která je zobecněná.

*Nejde aktualizovat sadu škálování virtuálního počítače <vmssName \> , protože aktuální stav operačního systému sady škálování virtuálního počítače je zobecněný, který se liší od aktualizovaného stavu operačního systému image galerie, který je specializovaný.*  
**Příčina**: aktuální zdrojová image pro sadu škálování je zobecněná zdrojová image, ale aktualizuje se zdrojovou imagí, která je specializovaná. Aktuální zdrojová image a nová zdrojová image pro sadu škálování musí být ve stejném stavu.  
**Alternativní řešení**: Pokud chcete aktualizovat sadu škálování, použijte zobecněnou verzi image.

*Sada šifrování disku <diskEncryptionSetId \> v galerii sdílených imagí <versionId \> patří do předplatného <subscriptionId1 \> a nedá se použít s prostředkem v předplatném <subscriptionId2\>*  
**Příčina**: sada šifrování disků, která se používá k šifrování verze image, se nachází v jiném předplatném, než je předplatné pro hostování verze image.  
**Alternativní řešení**: použijte stejné předplatné pro verzi image a sadu šifrování disku.

*Vytvoření virtuálního počítače nebo sady škálování virtuálního počítače trvá dlouhou dobu.*  
**Alternativní řešení**: Ověřte, že **OSType** verze image, ze které se pokoušíte vytvořit virtuální počítač nebo sadu škálování virtuálního počítače, z nástroje má stejný **OSType** zdroje, který jste použili k vytvoření verze image. 

## <a name="creating-a-disk-from-an-image-version"></a>Vytvoření disku z verze image ##

*Hodnota parametru element imagereference je neplatná.*  
**Příčina**: Pokusili jste se exportovat z verze image SIG na disk, ale použili jste pozici logické jednotky (LUN), která v imagi neexistuje.    
**Alternativní řešení**: Podívejte se na verzi obrázku a podívejte se, jaké pozice LUN se používají.

## <a name="sharing-resources"></a>Sdílení prostředků

Sdílení prostředků Galerie imagí, definice obrázku a verzí obrázků v rámci předplatných je povolené pomocí [řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/rbac-and-directory-admin-roles.md). 

## <a name="replication-speed"></a>Rychlost replikace

Pomocí příznaku **--expand ReplicationStatus** můžete ověřit, jestli se dokončila replikace do všech zadaných cílových oblastí. Pokud ne, počkejte až šest hodin, než se úloha dokončí. Pokud dojde k chybě, znovu spusťte příkaz pro vytvoření a replikaci verze image. Pokud existuje mnoho cílových oblastí, na které se replikuje verze bitové kopie, zvažte provedení replikace ve fázích.

## <a name="azure-limits-and-quotas"></a>Omezení a kvóty Azure 

[Omezení a kvóty Azure](../azure-resource-manager/management/azure-subscription-service-limits.md) se vztahují na všechny prostředky pro galerii sdílených imagí, definice obrázků a verze imagí. Ujistěte se, že jste v omezeních pro vaše předplatná. 


## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [galeriích sdílených imagí](./linux/shared-image-galleries.md).