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
ms.openlocfilehash: 189fa12b1fc11e79ab64231a7ecd453113b8771a
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93336006"
---
# <a name="troubleshooting-shared-image-galleries-in-azure"></a>Řešení potíží s galeriemi sdílených imagí v Azure

Pokud při provádění jakýchkoli operací se sdílenými galeriemi imagí, definicemi imagí a verzemi imagí narazíte na problémy, spusťte příkaz, který selhal, znovu v režimu ladění. Režim ladění je aktivován předáním přepínače pomocí rozhraní příkazového `--debug` řádku a `-Debug` přepínače pomocí prostředí PowerShell. Po vyhledání chyby použijte tento dokument k odstranění chyb.


## <a name="issues-with-creating-or-modifying-a-gallery"></a>Problémy s vytvořením nebo úpravou galerie ##

*Název galerie je neplatný. Povolené znaky jsou anglické alfanumerické znaky, s podtržítky a tečkami povolenými uprostřed, maximálně 80 znaků. Všechny ostatní speciální znaky, včetně pomlček, nejsou povoleny.*  
**Příčina** : daný název galerie nesplňuje požadavky na pojmenování.  
**Alternativní řešení** : vyberte název, který splňuje následující podmínky: 1) 80-limit znaků, 2) obsahuje jenom anglická písmena, číslice, podtržítka a tečky, 3) začíná a končí na anglických písmenech nebo čísly.

*Název entity ' název galerie ' je podle jeho ověřovacího pravidla neplatný: ^ [^ \_ \w] [\w-. \_ ] {0,79} (? <! [-.]) $.*  
**Příčina** : název galerie nesplňuje požadavky na pojmenování.  
**Alternativní řešení** : vyberte název pro galerii, který splňuje následující podmínky: 1) 80-limit znaků, 2) obsahuje jenom anglická písmena, číslice, podtržítka a tečky, 3) začíná a končí na anglické abecedy nebo čísly.

*Zadaný název prostředku <název galerie \> má neplatné koncové znaky: <znak \> . Název nemůže končit znaky: <znak.\>*  
**Příčina** : název galerie končí tečkou nebo podtržítkem.  
**Alternativní řešení** : vyberte název pro galerii, který splňuje následující podmínky: 1) 80-limit znaků, 2) obsahuje jenom anglická písmena, číslice, podtržítka a tečky, 3) začíná a končí na anglické abecedy nebo čísly.

*Zadané umístění <oblasti \> není k dispozici pro typ prostředku Microsoft. COMPUTE/galerie. Seznam oblastí, které jsou k dispozici pro typ prostředku, je...*  
**Příčina** : oblast zadaná pro galerii je nesprávná nebo vyžaduje žádost o přístup.  
**Alternativní řešení** : Ověřte, zda je název oblasti zadán správně. K zobrazení oblastí, ke kterým máte přístup, můžete spustit tento příkaz. Pokud oblast není uvedená v seznamu, odešlete [žádost o přístup](/troubleshoot/azure/general/region-access-request-process).

*Prostředek nelze odstranit před odstraněním vnořených prostředků.*  
**Příčina** : Pokusili jste se odstranit galerii, která obsahuje alespoň jednu existující definici obrázku. Galerie musí být prázdná, aby ji bylo možné odstranit.  
**Alternativní řešení** : Odstraňte všechny definice obrázků v galerii a pak pokračujte v odstraňování galerie. Pokud definice image obsahuje verze imagí, je nutné před odstraněním definic imagí odstranit verze imagí.

*Prostředek <Gallery \> již existuje v umístění <oblasti \_ 1 \> ve skupině prostředků <skupina prostředků \> . Prostředek se stejným názvem se nedá vytvořit v umístění <oblasti \_ 2 \> . Vyberte prosím nový název prostředku.*  
**Příčina** : již máte existující galerii ve skupině prostředků se stejným názvem a pokusili jste se vytvořit další galerii se stejným názvem, ale v jiné oblasti.  
**Alternativní řešení** : buď použijte jinou galerii nebo jinou skupinu prostředků.

## <a name="issues-with-creating-or-modifying-image-definitions"></a>Problémy s vytvářením nebo úpravou definicí obrázků ##

*Změna vlastnosti ' galleryImage. Properties. <Property \> ' není povolena.*  
**Příčina** : Pokusili jste se změnit typ operačního systému, stav operačního systému, generaci technologie Hyper V, nabídka, Vydavatel a SKU. Změna kterékoli z těchto vlastností není povolená.  
**Alternativní řešení** : místo toho vytvořte definici nové image.

*Prostředek <Gallery/imageDefinitionName \> již existuje v umístění <oblasti \_ 1 \> ve skupině prostředků <zdroj \> . Prostředek se stejným názvem se nedá vytvořit v umístění <oblasti \_ 2 \> . Vyberte prosím nový název prostředku.*  
**Příčina** : existující definici image už máte ve stejné galerii a skupině prostředků se stejným názvem a pokusili jste se vytvořit další definici image se stejným názvem a ve stejné galerii, ale v jiné oblasti.  
**Alternativní řešení** : použijte jiný název definice obrázku nebo vložte definici image do jiné galerie nebo skupiny prostředků.

*Zadaný název prostředku <Gallery \> /<imageDefinitionName \> obsahuje tyto neplatné koncové znaky: <znak \> . Název nemůže končit znaky: <znak.\>*  
**Příčina** : zadaný <imageDefinitionName \> končí tečkou nebo podtržítkem.  
**Alternativní řešení** : vyberte název definice obrázku, který splňuje následující podmínky: 1) 80-limit znaků, 2) obsahuje jenom anglická písmena, číslice, pomlčky, podtržítka a tečky, 3) začíná a končí v anglických písmenech nebo číslech.

*Název entity <imageDefinitionName \> je podle jeho ověřovacího pravidla neplatný: ^ [^ \_ \\ W] [ \\ W-. \_ ] {0,79} (? <! [-.]) $"*  
**Příčina** : zadaný <imageDefinitionName \> končí tečkou nebo podtržítkem.  
**Alternativní řešení** : vyberte název definice obrázku, který splňuje následující podmínky: 1) 80-limit znaků, 2) obsahuje jenom anglická písmena, číslice, pomlčky, podtržítka a tečky, 3) začíná a končí v anglických písmenech nebo číslech.

*Vlastnost názvu assetu galleryImage. Properties. Identifier. <není \> platná. Nemůže být prázdný. Povolené znaky jsou velká a malá písmena, číslice, spojovník (-), tečka (.), podtržítko ( \_ ). Názvům nesmí končit tečkou (.). Délka názvu nesmí překročit <é číselné \> znaky.*  
**Příčina** : zadaný Vydavatel, nabídka nebo hodnota SKU nesplňuje požadavky na pojmenování.  
**Alternativní řešení** : vyberte hodnotu, která splňuje tyto podmínky: 1) 128 – limit počtu znaků 64 pro nabídku a SKU, 2) obsahuje jenom anglická písmena, číslice, pomlčky, podtržítka a tečky a 3) nekončí tečkou.

*U vnořeného prostředku nelze provést požadovanou operaci. Nepovedlo se najít nadřazený <prostředku \> .*  
**Příčina** : \> v aktuálním předplatném a skupině prostředků neexistuje žádná galerie s názvem <název galerie.  
**Alternativní řešení** : Ověřte správnost názvu galerie, předplatného a skupiny prostředků. V opačném případě vytvořte novou galerii s názvem <název galerie \> .

*Zadané umístění <oblasti \> není k dispozici pro typ prostředku Microsoft. COMPUTE/galerie. Seznam oblastí, které jsou k dispozici pro typ prostředku, je...*  
**Příčina** : <oblast \> je nesprávná nebo vyžaduje žádost o přístup.  
**Alternativní řešení** : Ověřte, zda je název oblasti zadán správně. K zobrazení oblastí, ke kterým máte přístup, můžete spustit tento příkaz. Pokud oblast není uvedená v seznamu, odešlete [žádost o přístup](/troubleshoot/azure/general/region-access-request-process).

*Nelze serializovat hodnotu: hodnota <\> jako typ: ' ISO-8601 '. ISO8601Error: iso 8601 doba označení t ' chybí. Nedá se analyzovat hodnota <řetězců DateTime.\>*  
**Příčina** : hodnota poskytnutá vlastnosti není správně formátována jako datum.  
**Alternativní řešení** : Zadejte datum ve formátu RRRR-MM-DD, rrrr-mm-dd'T'HH: mm: Sszzz nebo [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-platný formát.

*Nelze převést řetězec na hodnotu DateTimeOffset: <Value \> . Cesta ' Properties. <vlastnost \> '*  
**Příčina** : hodnota poskytnutá vlastnosti není správně formátována jako datum.  
**Alternativní řešení** : Zadejte datum ve formátu RRRR-MM-DD, rrrr-mm-dd'T'HH: mm: Sszzz nebo [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-platný formát.

*EndOfLifeDate musí být nastavené na budoucí datum.*  
**Příčina** : vlastnost datum ukončení životnosti není správně naformátována jako datum, které následuje po dnešním datu.  
**Alternativní řešení** : Zadejte datum ve formátu RRRR-MM-DD, rrrr-mm-dd'T'HH: mm: Sszzz nebo [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-platný formát.

*Argument--<vlastnost \> : Neplatná hodnota typu int: <hodnota\>*  
**Příčina** : <vlastnost \> přijímá pouze celočíselné hodnoty a hodnota <\> není celé číslo.  
**Alternativní řešení** : vyberte celočíselnou hodnotu.

*Minimální hodnota vlastnosti <\> nesmí být větší než maximální hodnota vlastnosti <\> .*  
**Příčina** : minimální hodnota zadaná pro vlastnost <\> je vyšší než maximální hodnota poskytnutá pro vlastnost <\> .  
**Alternativní řešení** : Změňte hodnoty tak, aby minimum bylo menší nebo rovno maximální hodnotě.

*Obrázek Galerie: <imageDefinitionName \> identifikoval (Vydavatel: <Publisher \> , nabídka: <nabídka \> , SKU: <SKU \> ) již existuje. Vyberte jiného vydavatele, nabídku, kombinaci SKU.*  
**Příčina** : Pokusili jste se vytvořit novou definici obrázku stejného vydavatele, nabídky, SKU s trojicí jako existující definice obrázku ve stejné galerii.  
**Alternativní řešení** : všechny definice imagí musí mít jedinečnou kombinaci vydavatele, nabídky, skladové položky (SKU). Vyberte jedinečnou kombinaci nebo vyberte novou galerii a znovu vytvořte definici image.

*Prostředek nelze odstranit před odstraněním vnořených prostředků.*  
**Příčina** : Pokusili jste se odstranit definici image, která obsahuje verze imagí. Definice obrázku musí být prázdná, aby ji bylo možné odstranit.  
**Alternativní řešení** : Odstraňte všechny verze imagí v definici image a pak pokračujte v odstranění definice image.

*Nelze vytvořit vazby vlastnosti <parametru \> . Hodnotu <hodnoty nelze převést \> na typ <PropertyType \> . Název identifikátoru <hodnoty nelze porovnat \> s platným názvem enumerátoru. Zadejte jeden z následujících názvů enumerátorů a akci opakujte: <choice1 \> , <choice2 \> ,...*  
**Příčina** : vlastnost má omezený seznam možných hodnot a <hodnota není \> jednou z nich.  
**Alternativní řešení** : zvolte jednu z možných <\> hodnoty volby.

*Nelze vytvořit vazby vlastnosti <parametru \> . Hodnotu <hodnoty nelze převést \> na typ &quot; System. DateTime.&quot;*  
**Příčina** : hodnota poskytnutá vlastnosti není správně formátována jako datum.  
**Alternativní řešení** : Zadejte datum ve formátu RRRR-MM-DD, rrrr-mm-dd'T'HH: mm: Sszzz nebo [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-platný formát.

*Nelze vytvořit vazby vlastnosti <parametru \> . Hodnotu <hodnoty nelze převést \> na typ &quot; System. Int32.&quot;*  
**Příčina** : <vlastnost \> přijímá pouze celočíselné hodnoty a hodnota <\> není celé číslo.  
**Alternativní řešení** : vyberte celočíselnou hodnotu.

*Typ účtu úložiště ZRS se v této oblasti nepodporuje.*  
**Příčina** : zvolili jste standardní ZRS v oblasti, která ho ještě nepodporují.  
**Alternativní řešení** : Změňte typ účtu úložiště na ' Premium \_ LRS ' nebo ' standard \_ LRS '. V naší dokumentaci najdete nejnovější [seznam oblastí](/azure/storage/common/storage-redundancy#zone-redundant-storage) s povoleným ZRS Preview.

## <a name="issues-with-creating-or-updating-image-versions"></a>Problémy s vytvářením nebo aktualizací verzí imagí ##

*Zadané umístění <oblasti \> není k dispozici pro typ prostředku Microsoft. COMPUTE/galerie. Seznam oblastí, které jsou k dispozici pro typ prostředku, je...*  
**Příčina** : <oblast \> je nesprávná nebo vyžaduje žádost o přístup.  
**Alternativní řešení** : Ověřte, zda je název oblasti zadán správně. K zobrazení oblastí, ke kterým máte přístup, můžete spustit tento příkaz. Pokud oblast není uvedená v seznamu, odešlete [žádost o přístup](/troubleshoot/azure/general/region-access-request-process).

*U vnořeného prostředku nelze provést požadovanou operaci. Nepovedlo se najít nadřazený prostředek <imageDefinitionName (Gallery/) \> .*  
**Příčina** : \> v aktuálním předplatném a skupině prostředků neexistuje žádná galerie s názvem <název galerie/imageDefinitionName.  
**Alternativní řešení** : Ověřte správnost názvu galerie, předplatného a skupiny prostředků. V opačném případě vytvořte novou galerii s názvem <název galerie \> nebo definice obrázku s názvem <imageDefinitionName \> ve zmíněné skupině prostředků.

*Nelze vytvořit vazby vlastnosti <parametru \> . Hodnotu <hodnoty nelze převést \> na typ &quot; System. DateTime.&quot;*  
**Příčina** : hodnota poskytnutá vlastnosti není správně formátována jako datum.  
**Alternativní řešení** : Zadejte datum ve formátu RRRR-MM-DD, rrrr-mm-dd'T'HH: mm: Sszzz nebo [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-platný formát.

*Nelze vytvořit vazby vlastnosti <parametru \> . Hodnotu <hodnoty nelze převést \> na typ &quot; System. Int32.&quot;*  
**Příčina** : <vlastnost \> přijímá pouze celočíselné hodnoty a hodnota <\> není celé číslo.  
**Alternativní řešení** : vyberte celočíselnou hodnotu.

*Verze image v galerii – oblasti profil publikování <publishingRegions \> musí obsahovat umístění verze image <sourceRegion\>*  
**Příčina** : umístění zdrojového obrázku (<sourceRegion \> ) musí být zahrnuté do <\> seznamu publishingRegions  
**Alternativní řešení** : \> v seznamu <PublishingRegions uveďte < sourceRegion \> .

*Hodnota <hodnota \> vlastnosti <parametru \> je mimo rozsah. Hodnota musí být mezi <minValue \> a <MaxValue \> , včetně.*  
**Příčina** : hodnota <\> je mimo rozsah možných hodnot pro vlastnost <\> .  
**Alternativní řešení** : vyberte hodnotu, která spadá do rozsahu <minValue \> a <MaxValue \> včetně.

*Zdroj <resourceID nebyl \> nalezen. Zkontrolujte prosím, že zdroj existuje a je ve stejné oblasti jako vytvořená verze image galerie.*  
**Příčina** : není k dispozici žádný zdroj umístěný v <ResourceID \> nebo zdroj v <ResourceID \> není ve stejné oblasti, ve které je vytvářena image galerie.  
**Alternativní řešení** : Ověřte, že je <ResourceID \> správné a že zdrojová oblast verze image galerie je shodná s oblastí <ResourceID.\>

*Změna vlastnosti ' galleryImageVersion. Properties. storageProfile. <diskImage \> . Source.ID ' není povolena.*  
**Příčina** : ID zdroje verze Image Galerie nelze po vytvoření změnit.  
**Alternativní řešení** : Zajistěte, aby ID zdroje bylo stejné jako již existující ID zdroje, nebo změňte číslo verze image.

*V discích vstupních dat se zjistila duplicitní čísla logických jednotek (LUN). Číslo logické jednotky (LUN) musí být pro každý datový disk jedinečné.*  
**Příčina** : při vytváření verze Image pomocí seznamu disků nebo snímků disku mají dva nebo víc disků nebo snímků na disku stejná čísla logické jednotky (LUN).  
**Alternativní řešení** : odeberte nebo změňte duplicitní čísla logické jednotky (LUN).

*Ve vstupních discích se nacházejí duplicitní ID zdroje. ID zdroje by mělo být pro každý disk jedinečné.*  
**Příčina** : Když vytváříte verzi Image pomocí seznamu disků nebo disků, mají dva nebo víc disků nebo snímků na disku stejné ID prostředku.  
**Alternativní řešení** : odeberte nebo změňte všechna duplicitní ID zdroje disku.

*ID vlastnosti <resourceID \> v cestě ' Properties. storageProfile. <diskImages \> . Source.ID ' je neplatné. Očekávalo se plně kvalifikované ID prostředku, které začíná na '/subscriptions/{subscriptionId} ' nebo '/providers/{resourceProviderNamespace}/'.*  
**Příčina** : <ResourceID \> je nesprávně naformátované.  
**Alternativní řešení** : Ověřte, jestli je ResourceID správné.

*ID zdroje: <resourceID \> musí být buď spravovaná image, virtuální počítač nebo jiná verze image galerie.*  
**Příčina** : <ResourceID \> je nesprávně naformátované.  
**Alternativní řešení** : Pokud jako zdrojovou bitovou kopii používáte virtuální počítač, spravovanou Image nebo Image Galerie, ověřte, jestli je ID prostředku virtuálního počítače, spravované Image nebo Image Galerie správné.

*ID zdroje: <resourceID \> musí být spravovaný disk nebo snímek.*  
**Příčina** : <ResourceID \> je nesprávně naformátované.  
**Alternativní řešení** : Pokud používáte disky nebo snímky disků jako zdroje pro verzi image, ověřte, jestli jsou ID prostředků disků nebo snímky disků správné.

*Nejde vytvořit verzi image galerie z: <resourceID \> , protože stav operačního systému v nadřazené imagi galerie (<OsState \_ 1 \> ) není <OsState \_ 2 \> .*  
**Příčina** : stav operačního systému (zobecněné nebo specializované) se neshoduje s stavem operačního systému zadaným v definici obrázku.  
**Alternativní řešení** : buď vyberte zdroj založený na virtuálním počítači se stavem operačního systému <OsState \_ 1 \> , nebo vytvořte novou definici image pro virtuální počítače na základě <OsState \_ 2 \> .

*Prostředek s ID <resourceID \> má jinou generaci hypervisoru [' <V # \_ 1 \> ] než generace hypervisoru image nadřazené galerie [' <V # \_ 2 \> '].*  
**Příčina** : generace hypervisoru verze image neodpovídá generaci hypervisoru určenému v definici image. Operační systém definice Image je <V č. \_ 1 \> a verze image operačního systému je <V č \_ . 2 \> .  
**Alternativní řešení** : buď vyberte zdroj se stejnou generací hypervisoru jako definici obrázku, nebo vytvořte nebo vyberte novou definici image, která má stejnou generaci hypervisoru jako verze image.

*Prostředek s ID <resourceID \> má jiný typ operačního systému [' <OsType \_ 1 \> '] než typ operačního systému image nadřazené galerie [' <OsType \_ 2 \> '].*  
**Příčina** : generace hypervisoru verze image neodpovídá generaci hypervisoru určenému v definici image. Operační systém definice Image je <OsType \_ 1 \> a verze image operačního systému je <OsType \_ 2 \> .  
**Alternativní řešení** : buď jako definici bitové kopie vyberte zdroj se stejným operačním systémem (Linux/Windows), nebo vytvořte nebo vyberte novou definici image, která má stejnou generaci operačního systému jako verze image.

*Zdrojový virtuální počítač <resourceID \> nemůže obsahovat dočasný disk s operačním systémem.*  
**Příčina** : zdroj v ' <ResourceID \> ' obsahuje dočasný disk s operačním systémem. Galerie sdílených imagí v současné době nepodporuje dočasný disk s operačním systémem.  
**Alternativní řešení** : vyberte jiný zdroj založený na virtuálním počítači, který nepoužívá dočasný disk s operačním systémem.

*Zdrojový virtuální počítač <resourceID \> nemůže obsahovat disk [' <diskID \> '] uložený v typu účtu UltraSSD.*  
**Příčina** : disk ' <diskID \> je disk UltraSSD. Galerie sdílených imagí v současné době nepodporuje SSD úrovně Ultra disky.  
**Alternativní řešení** : použijte zdroj, který obsahuje jenom SSD úrovně Premium, SSD úrovně Standard nebo disky spravované HDD úrovně Standard.

*Zdrojový virtuální počítač <resourceID \> je nutné vytvořit z Managed disks.*  
**Příčina** : virtuální počítač v <ResourceID \> používá nespravované disky.  
**Alternativní řešení** : použijte zdroj založený na virtuálním počítači, který obsahuje jenom SSD úrovně Premium disky spravované SSD úrovně Standard nebo HDD úrovně Standard.

*Ve zdroji <resourceID je moc velký počet požadavků \> . Snižte počet požadavků na zdroj nebo počkejte nějakou dobu, než se zopakuje.*  
**Příčina** : zdroj této verze image se v současné době omezuje z důvodu příliš velkého počtu požadavků.  
**Alternativní řešení** : Pokuste se vytvořit verzi image později.

*Sada šifrování disku <diskEncryptionSetID \> musí být ve stejném předplatném "<subscriptionID \> " jako prostředek galerie.*  
**Příčina** : sady šifrování disků lze použít pouze ve stejném předplatném a oblasti, ve kterých byly vytvořeny.  
**Alternativní řešení** : Vytvořte nebo použijte šifrovací sadu ve stejném předplatném a oblasti jako verzi image.

*Zašifrovaný zdroj: <resourceID \> má jiné ID předplatného než aktuální předplatné verze image galerie <subscriptionID \_ 1 \> . Zkuste to prosím znovu s nešifrovanými zdroji nebo použijte předplatné zdroje <subcriptionID \_ 2 \> k vytvoření verze image galerie.*  
**Příčina** : Galerie sdílených imagí v současné době nepodporuje vytváření verzí imagí v jiném předplatném z jiné zdrojové image, pokud je zdrojový obraz zašifrovaný.  
**Alternativní řešení** : použijte nešifrovaný zdroj nebo vytvořte verzi image ve stejném předplatném jako zdroj.

*Sada šifrování disku <diskEncryptionSetID nebyla \> nalezena.*  
**Příčina** : šifrování disku může být nesprávné.  
**Alternativní řešení** : Ověřte, jestli je ID prostředku sady pro šifrování disků správné.

*Název verze Image je neplatný. Název verze Image by měl následovat po hlavní (int). Vedlejší (int). Oprava (int), například: 1.0.0, 2018.12.1 atd.*  
**Příčina** : platný formát pro verzi obrázku je 3 celá čísla oddělená tečkou. Název verze image nevyhověl platnému formátu.  
**Alternativní řešení** : použijte název verze image, který následuje po formátu hlavní (int). Vedlejší (int). Patch (int), například: 1.0.0. nebo 2018.12.1.

*Hodnota parametru galleryArtifactVersion. Properties. publishingProfile. targetRegions. Encryption. dataDiskImages. diskEncryptionSetId není platná.*  
**Příčina** : ID prostředku sady šifrování disku používaného v imagi datového disku používá neplatný formát.  
**Řešení** : Zajistěte, aby ID prostředku sady šifrování disku bylo/Subscriptions/<subscriptionID \> /ResourceGroups/<ResourceGroupName \> /providers/Microsoft.COMPUTE/<diskEncryptionSetName \> .

*Hodnota parametru galleryArtifactVersion. Properties. publishingProfile. targetRegions. Encryption. osDiskImage. diskEncryptionSetId není platná.* 
 **Příčina** : ID prostředku sady šifrování disku použité v imagi disku operačního systému používá neplatný formát.  
**Řešení** : Zajistěte, aby ID prostředku sady šifrování disku bylo/Subscriptions/<subscriptionID \> /ResourceGroups/<ResourceGroupName \> /providers/Microsoft.COMPUTE/<diskEncryptionSetName \> .

*Nelze zadat novou logickou jednotku (LUN) pro šifrování dat na disku [<číslo \> ] se sadou Disk Encryption nastavenou v oblasti [<oblast \> ] pro požadavek verze Image Galerie aktualizace. Chcete-li aktualizovat tuto verzi, odeberte novou logickou jednotku (LUN). Pokud potřebujete změnit nastavení šifrování bitové kopie dat, musíte vytvořit novou verzi image galerie se správnými nastaveními.*  
**Příčina** : bylo přidáno šifrování na datový disk existující verze bitové kopie. Nelze přidat šifrování do existující verze bitové kopie.  
**Alternativní řešení** : Vytvořte novou verzi Image Galerie nebo odeberte přidané nastavení šifrování.

*Zdroj verze artefaktu galerie lze zadat pouze přímo v rámci storageProfile nebo v rámci jednotlivého operačního systému nebo datových disků. Je možné zadat pouze jeden typ zdroje (bitová kopie uživatele, snímek, disk, virtuální počítač).*  
**Příčina** : chybí ID zdroje.  
**Alternativní řešení** : Zajistěte, aby bylo ID zdroje k dispozici.

*Zdroj nebyl nalezen: <resourceID \> . Ujistěte se prosím, že zdroj existuje.*  
**Příčina** : zdroj ResourceID zdroje může být nesprávný.  
**Alternativní řešení** : Ujistěte se, že je zdroj ResourceID správný.

*Pro disk "galleryArtifactVersion. Properties. publishingProfile. targetRegions. Encryption. osDiskImage. diskEncryptionSetId" v cílové oblasti <oblasti 1 se vyžaduje sada šifrování disku, \_ \> protože sada disk Encryption <diskEncryptionSetId \> se používá pro odpovídající disk v oblasti <region \_ 2 \> .*  
**Příčina** : šifrování se použilo na disku s operačním systémem v <oblasti \_ 2 \> , ale ne v <oblasti \_ 1 \> .  
**Alternativní řešení** : Pokud používáte šifrování na disku s operačním systémem, použijte šifrování ve všech oblastech.

*Pro diskovou jednotku LUN <číslo \> v cílové oblasti <oblasti 1 se vyžaduje sada šifrování disku, \_ \> protože \> pro odpovídající disk v oblasti <oblasti 2 se používá šifrovací sada disků <diskEncryptionSetID. \_ \>*  
**Příčina** : šifrování se použilo na datovém disku na logické jednotce <číslo \> v <oblasti \_ 2 \> , ale ne v <oblasti \_ 1 \> .  
**Alternativní řešení** : Pokud používáte šifrování na datový disk, použijte šifrování ve všech oblastech.

*V šifrování. dataDiskImages byla zadána neplatná logická jednotka (LUN) [číslo <\> ]. Logická jednotka (LUN) musí být jedna z následujících hodnot [' 0, 9 '].*  
**Příčina** : číslo logické jednotky zadané pro šifrování neodpovídá žádnému z čísel LUN pro disky připojené k virtuálnímu počítači.  
**Alternativní řešení** : v šifrování změňte číslo logické jednotky (LUN) na číslo logické jednotky (LUN) datového disku přítomného ve virtuálním počítači.

*\>V cílovém regionu ' <oblasti \> ' Encryption. dataDiskImages ' byly zadány duplicitní logické jednotky lun ' <číslo '.*  
**Příčina** : nastavení šifrování používané v <oblasti \> určilo alespoň dvě číslo logické jednotky.  
**Alternativní řešení** : v oblasti <změňte číslo logické jednotky a ujistěte se \> , že všechna čísla logických jednotek jsou v <oblasti jedinečná \> .

*OSDiskImage a DataDiskImage nemohou odkazovat na stejný objekt BLOB <sourceID.\>*  
**Příčina** : zdroj disku s operačním systémem a aspoň jeden datový disk není jedinečný.  
**Alternativní řešení** : změňte zdroj pro disk s operačním systémem a datové disky, abyste měli jistotu, že disk s operačním systémem i každý datový disk je jedinečný.

*V cílových oblastech publikování nejsou povoleny duplicitní oblasti.*  
**Příčina** : oblast je uvedena mezi oblastmi publikování více než jednou.  
**Alternativní řešení** : odstraňte duplicitní oblast.

*Přidávání nových datových disků nebo změna logické jednotky (LUN) datového disku v existující imagi není povoleno.*  
**Příčina** : volání aktualizace verze image buď obsahuje nový datový disk, nebo má nové číslo logické jednotky pro disk.  
**Alternativní řešení** : použijte čísla logických jednotek a datové disky existující verze image.

*Sada šifrování disku <diskEncryptionSetID \> musí být ve stejném předplatném <subscriptionID \> jako prostředek galerie.*  
**Příčina** : Galerie sdílených imagí v současné době nepodporuje používání sady šifrování disků v jiném předplatném.  
**Alternativní řešení** : v rámci stejného předplatného vytvořte verzi image a sadu Encryption disk.

## <a name="issues-creating-or-updating-a-vm-or-scale-sets-from-image-version"></a>Problémy při vytváření nebo aktualizaci virtuálního počítače nebo sady škálování z verze image ##

*Klient má oprávnění k provedení akce "Microsoft. COMPUTE/galeriích/imagí/verze/číst" v oboru <resourceID \> , ale aktuální tenant <tenantId1 \> nemá autorizaci pro přístup k propojenému předplatnému <subscriptionId2 \> .*  
**Příčina** : virtuální počítač nebo sada škálování se vytvořily pomocí Image SIG v jiném tenantovi. Pokusili jste se provést změnu virtuálního počítače nebo sady škálování, ale nemáte přístup k předplatnému, které vlastní image.  
**Alternativní řešení** : Pokud chcete verzi image udělit přístup pro čtení, obraťte se na vlastníka předplatného verze image.

*Obrázek Galerie <resourceID není \> k dispozici v oblasti <oblasti \> . Pokud chcete replikovat do této oblasti, obraťte se prosím na vlastníka image, nebo změňte požadovanou oblast.*  
**Příčina** : virtuální počítač se vytváří v oblasti, která není mezi seznamem publikovaných oblastí pro image galerie.  
**Alternativní řešení** : buď replikujte image do oblasti, nebo vytvořte virtuální počítač v jedné z oblastí v oblastech publikování obrázku galerie.

*Parametr osProfile není povolený.*  
**Příčina** : pro virtuální počítač, který byl vytvořen z specializované verze bitové kopie, byly poskytnuty uživatelské jméno a heslo správce nebo klíče SSH.  
**Alternativní řešení** : Pokud máte v úmyslu vytvořit virtuální počítač z této image, nezahrnujte uživatelské jméno správce, heslo ani klíče SSH. V opačném případě použijte zobecněnou verzi image a zadejte uživatelské jméno, heslo nebo klíče SSH pro správce.

*Povinný parametr osProfile chybí (null).*  
**Příčina** : virtuální počítač se vytvoří z generalizované image a chybí uživatelské jméno, heslo nebo klíče SSH pro správce. Vzhledem k tomu, že generalizované image neuchovávají uživatelské jméno, heslo ani klíče SSH, musí být tato pole zadaná při vytváření virtuálního počítače nebo škálovací sady.  
**Alternativní řešení** : zadejte uživatelské jméno správce, heslo nebo klíče SSH nebo použijte specializovanou verzi image.

*Nejde vytvořit verzi image galerie z: <resourceID, \> protože stav operačního systému v nadřazené imagi galerie ("specializované") není zobecněný.*  
**Příčina** : verze obrázku je vytvořena z zobecněného zdroje, ale jeho Nadřazená definice je specializovaná.  
**Alternativní řešení** : buď vytvořte verzi Image pomocí specializovaného zdroje, nebo použijte nadřazenou definici, která je zobecněná.

*Nejde aktualizovat sadu škálování virtuálního počítače <vmssName \> , protože aktuální stav operačního systému sady škálování virtuálního počítače je zobecněný, který se liší od aktualizovaného stavu operačního systému image galerie, který je specializovaný.*  
**Příčina** : aktuální zdrojová image pro škálovací sady je zobecněná zdrojová image, ale je aktualizována pomocí zdrojové image, která je specializovaná. Aktuální zdrojová image a nová zdrojová image pro škálovací sady musí být ve stejném stavu.  
**Alternativní řešení** : Pokud chcete aktualizovat škálovací sady, použijte zobecněnou verzi image.

*Sada šifrování disku <diskEncryptionSetId \> v galerii sdílených imagí <versionId \> patří do předplatného <subscriptionId1 \> a nedá se použít s prostředkem v předplatném <subscriptionId2\>*  
**Příčina** : sada šifrování disků, která se používá k šifrování verze image, se nachází v jiném předplatném, než je předplatné pro hostování verze image.  
**Alternativní řešení** : použijte stejné předplatné pro verzi image a sadu šifrování disku.

*Vytvoření virtuálního počítače nebo sady škálování virtuálního počítače trvá dlouhou dobu.*  
**Alternativní řešení** : Ověřte, že **OSType** verze image, ze které se pokoušíte vytvořit virtuální počítač nebo sadu škálování virtuálního počítače z nástroje, má stejný **OSType** zdroje, který jste použili k vytvoření verze image. 

## <a name="issues-creating-a-disk-from-an-image-version"></a>Problémy při vytváření disku z verze image ##

*Hodnota parametru element imagereference je neplatná.*  
**Příčina** : Pokusili jste se exportovat z verze image SIG na disk, ale použili jste pozici logické jednotky (LUN), která v imagi neexistuje.    
**Alternativní řešení** : Podívejte se na verzi obrázku a podívejte se, jaké pozice LUN se používají.

## <a name="unable-to-share-resources"></a>Nejde sdílet prostředky

Sdílení Galerie sdílených imagí, definice obrázku a verze imagí v rámci předplatných je povolené pomocí [řízení přístupu na základě role Azure (RBAC)](../role-based-access-control/rbac-and-directory-admin-roles.md). 

## <a name="replication-is-slow"></a>Replikace je pomalá.

Pomocí příznaku **--expand ReplicationStatus** můžete ověřit, jestli se dokončila replikace do všech zadaných cílových oblastí. V takovém případě počkejte po dobu až 6 hodin, než se úloha dokončí. Pokud dojde k chybě, znovu spusťte příkaz pro vytvoření a replikaci verze image. Pokud existuje mnoho cílových oblastí, na které se replikuje verze bitové kopie, zvažte provedení replikace ve fázích.

## <a name="azure-limits-and-quotas"></a>Omezení a kvóty Azure 

[Omezení a kvóty Azure](../azure-resource-manager/management/azure-subscription-service-limits.md) se vztahují na všechny prostředky pro galerii sdílených imagí, definice obrázků a verze imagí. Ujistěte se, že jste v omezeních pro vaše předplatná. 


## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [galeriích sdílených imagí](./linux/shared-image-galleries.md).