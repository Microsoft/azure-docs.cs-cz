---
title: Zapnutí nebo vypnutí zařízení řady StorSimple 8000
description: Vysvětluje, jak zapnout nové zařízení StorSimple, zapnout zařízení, které bylo vypnuto nebo ztratilo napájení, a vypnout běžící zařízení.
author: alkohli
ms.assetid: 8e9c6e6c-965c-4a81-81bd-e1c523a14c82
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1319583569a8abc619ad902a87ee551b476f88eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254622"
---
# <a name="turn-on-or-turn-off-your-storsimple-8000-series-device"></a>Zapnutí nebo vypnutí zařízení řady StorSimple 8000

## <a name="overview"></a>Přehled
Vypnutí zařízení Microsoft Azure StorSimple není vyžadováno jako součást normálního provozu systému. Možná však budete muset zapnout nové zařízení nebo zařízení, které bylo nutné vypnout. Obecně platí, že vypnutí je vyžadováno v případech, kdy je nutné vyměnit vadný hardware, fyzicky přesunout jednotku nebo zařízení z provozu. Tento kurz popisuje požadovaný postup pro zapnutí a vypnutí zařízení StorSimple v různých scénářích.

## <a name="turn-on-a-new-device"></a>Zapnutí nového zařízení
Kroky pro zapnutí zařízení StorSimple poprvé se liší v závislosti na tom, zda je zařízení 8100 nebo 8600 model. 8100 má jednu primární skříň, zatímco 8600 je zařízení se dvěma skříněmi s primární skříní a skříní EBOD. Podrobné kroky pro oba modely jsou uvedeny v následujících částech.

* [Nové zařízení pouze s primárním krytem](#new-device-with-primary-enclosure-only)
* [Nové zařízení s krytem EBOD](#new-device-with-ebod-enclosure)

### <a name="new-device-with-primary-enclosure-only"></a>Nové zařízení pouze s primárním krytem
Model StorSimple 8100 je jedno skříňové zařízení. Zařízení obsahuje redundantní napájecí a chladicí moduly (PCM). Aby byla zajištěna vysoká dostupnost, musí být oba pcmy nainstalovány a připojeny k různým zdrojům napájení.

Proveďte následující kroky k připojení zařízení k napájení.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

> [!NOTE]
> Kompletní pokyny k nastavení zařízení a kabeláži naleznete v [části Instalace zařízení StorSimple 8100](storsimple-8100-hardware-installation.md). Ujistěte se, že budete postupovat podle pokynů přesně.
> 
> 

### <a name="new-device-with-ebod-enclosure"></a>Nové zařízení s krytem EBOD
Model StorSimple 8600 má primární skříň i skříň EBOD. To vyžaduje, aby jednotky byly spojeny dohromady pro připojení a napájení Serial Attached SCSI (SAS).

Při prvním nastavení tohoto zařízení proveďte nejprve kroky pro kabeláž SAS a poté proveďte kroky pro napájení kabeláže.

[!INCLUDE [storsimple-sas-cable-8600](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

> [!NOTE]
> Kompletní pokyny k nastavení zařízení a kabeláži naleznete v [části Instalace zařízení StorSimple 8600](storsimple-8600-hardware-installation.md). Ujistěte se, že budete postupovat podle pokynů přesně.

## <a name="turn-on-a-device-after-shutdown"></a>Zapnutí zařízení po vypnutí
Kroky pro zapnutí zařízení StorSimple po jeho vypnutí se liší v závislosti na tom, zda je zařízení 8100 nebo 8600 model. 8100 má jednu primární skříň, zatímco 8600 je zařízení se dvěma skříněmi s primární skříní a skříní EBOD.

* [Zařízení pouze s primárním krytem](#device-with-primary-enclosure-only)
* [Zařízení s krytem EBOD](#device-with-ebod-enclosure)

### <a name="device-with-primary-enclosure-only"></a>Zařízení pouze s primárním krytem
Po vypnutí použijte následující postup k zapnutí zařízení StorSimple s primární skříní a bez skříně EBOD.

#### <a name="to-turn-on-a-device-with-a-primary-enclosure-only"></a>Zapnutí zařízení pouze s primárním krytem
1. Ujistěte se, že vypínače na napájecích i chladicích modulech (PCM) jsou v poloze OFF. Pokud spínače nejsou v poloze OFF, otočte je do polohy OFF a počkejte, až světla zhasnou.
2. Zapněte zařízení přepnutím vypínačů napájení na obou PCM do polohy ON. Zařízení by se mělo zapnout.
3. Zkontrolujte následující, abyste ověřili, zda je zařízení plně zapnuté:
   
   1. LED diody OK na obou modulech PCM jsou zelené.
   2. Stavové LED diody na obou ovladačích jsou plné zeleně.
   3. Modrá LED dioda na jednom z ovladačů bliká, což znamená, že ovladač je aktivní.
      
      Pokud některá z těchto podmínek není splněna, vaše zařízení není v pořádku. [Obraťte se na podporu společnosti Microsoft](storsimple-8000-contact-microsoft-support.md).

### <a name="device-with-ebod-enclosure"></a>Zařízení s krytem EBOD
Po vypnutí použijte následující postup k zapnutí zařízení StorSimple s primární skříní a skříní EBOD. Proveďte každý krok v pořadí přesně tak, jak je popsáno. Pokud tak neučiníte, může dojít ke ztrátě dat.

#### <a name="to-turn-on-a-device-with-a-primary-and-an-ebod-enclosure"></a>Zapnutí zařízení s primárním a skříní EBOD
1. Ujistěte se, že skříň EBOD je připojena k primární skříni. Další informace naleznete v [tématu Instalace zařízení StorSimple 8600](storsimple-8600-hardware-installation.md).
2. Ujistěte se, že napájecí a chladicí moduly (PCM) na EBOD i primárních skříních jsou v poloze OFF. Pokud spínače nejsou v poloze OFF, otočte je do polohy OFF a počkejte, až světla zhasnou.
3. Nejprve zapněte skříň EBOD přepnutím vypínačů na obou PCM do polohy ON. LED diody PCM by měly být zelené. Zelená LED dioda regulátoru EBOD na této jednotce indikuje, že je zapnutá skříň EBOD.
4. Zapněte primární kryt přepnutím vypínačů napájení na obou PCM do polohy ON. Celý systém by měl být nyní zapnutý.
5. Ověřte, zda jsou LED diody SAS zelené, což zajišťuje, že spojení mezi skříní EBOD a primární skříní je dobré.

## <a name="turn-on-a-device-after-a-power-loss"></a>Zapnutí zařízení po výpadku napájení
Výpadek nebo přerušení napájení může vypnout zařízení StorSimple. K výpadku napájení může dojít na jednom napájecím zdroje nebo na obou napájecích zdrojích. Kroky obnovení se liší v závislosti na tom, zda je zařízení model 8100 nebo 8600. 8100 má jednu primární skříň, zatímco 8600 je zařízení se dvěma skříněmi s primární skříní a skříní EBOD. Tato část popisuje postup obnovení pro každý scénář.

* [Zařízení pouze s primárním krytem](#8100)
* [Zařízení s krytem EBOD](#8600)

### <a name="device-with-primary-enclosure-only-a-name8100"></a>Zařízení pouze s primárním krytem<a name="8100">
Systém může pokračovat v normálním provozu, pokud dojde ke ztrátě napájení jednoho ze svých napájecích zdrojů. Chcete-li však zajistit vysokou dostupnost zařízení, obnovte napájení napájecího zdroje co nejdříve.

Dojde-li k výpadku napájení nebo přerušení napájení na obou napájecích zdrojích, systém se vypne řádným a kontrolovaným způsobem. Po obnovení napájení se systém automaticky zapne.

### <a name="device-with-ebod-enclosure-a-name8600"></a>Zařízení s krytem EBOD<a name="8600">
#### <a name="power-loss-on-one-power-supply"></a>Ztráta energie na jednom napájecím zdroji
Systém může pokračovat v normálním provozu, pokud dojde ke ztrátě napájení jednoho ze svých napájecích zdrojů na primárním krytu nebo skříni EBOD. Chcete-li však zajistit vysokou dostupnost zařízení, obnovte napájení napájecího zdroje co nejdříve.

#### <a name="power-loss-on-both-power-supplies-on-primary-and-ebod-enclosures"></a>Výpadky napájení na napájecích zdrojích na primárních skříních i v skříních EBOD
Pokud dojde k výpadku napájení nebo přerušení napájení na obou napájecích zdrojích, skříň EBOD se okamžitě vypne a primární skříň se vypne řádným a kontrolovaným způsobem. Po obnovení napájení se přístroj automaticky spustí.

Pokud je napájení vypnuto ručně, proveďte následující kroky k obnovení napájení systému.

1. Zapněte kryt EBOD.
2. Po zapnutí skříně EBOD zapněte primární kryt.

### <a name="power-loss-on-both-power-supplies-on-ebod-enclosure"></a>Ztráta energie na obou napájecích zdrojích na skříni EBOD
Při nastavujete kabely, musíte zajistit, aby EBOD nikdy připojen sám k samostatnému PDU. Pokud EBOD a primární skříň nezdaří ve stejnou dobu, systém se obnoví.

Pokud se nezdaří pouze skříň EBOD na obou napájecích zdrojích, systém se automaticky neobnoví. Chcete-li systém zapnout a obnovit do stavu v pořádku, postupujte takto:

1. Pokud je primární skříň zapnutá, vypněte moduly napájení i chlazení (PCM).
2. Počkejte několik minut, než se systém vypne.
3. Zapněte kryt EBOD.
4. Po zapnutí skříně EBOD zapněte primární kryt.

## <a name="turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost"></a>Zapnutí zařízení po ztrátě primárního připojení skříně a připojení skříně EBOD
Pokud dojde ke ztrátě spojení mezi pohotovostním řadičem a odpovídajícím řadičem EBOD, zařízení bude nadále fungovat. Pokud dojde ke ztrátě spojení mezi aktivním řadičem systému a odpovídajícím řadičem EBOD, mělo by dojít k převzetí služeb při selhání a zařízení by mělo nadále fungovat jako obvykle.

Po odstranění obou sériových připojených kabelů SCSI (SAS) nebo odpojení spojení mezi skříní EBOD a primární skříní přestane zařízení fungovat. V tomto okamžiku proveďte následující kroky.

### <a name="to-turn-on-the-device-after-connection-is-lost"></a>Zapnutí zařízení po ztrátě připojení
1. Přístup na zadní straně zařízení.
2. Pokud je kabelové spojení SAS mezi skříní EBOD a primární skříní přerušeno, všechny diody jízdních pruhů SAS na skříni EBOD budou vypnuty.
3. Vypněte moduly napájení i chlazení (PCM) ve skříni EBOD a primární skříni.
4. Počkejte, až se všechna světla na zadní straně obou skříní zhasnou.
5. Znovu vložte kabely SAS a ujistěte se, že existuje dobré spojení mezi skříní EBOD a primární skříní.
6. Nejprve zapněte skříň EBOD přepnutím obou přepínačů PCM do polohy ON.
7. Zkontrolujte, zda je zelená LED dioda zapnutá.
8. Zapněte primární kryt.
9. Zkontrolujte, zda je zapnutá zelená LED dioda regulátoru.
10. Ověřte, zda je připojení skříně EBOD k primární skříni dobré kontrolou, zda jsou diody jízdních pruhů SAS (čtyři na řadič EBOD) zapnuty.

> [!IMPORTANT]
> Pokud jsou kabely SAS vadné nebo spojení mezi skříní EBOD a primární skříní není dobré, při zapnutí systému přejde do režimu obnovy. Pokud k tomu dojde, obraťte se [na podporu společnosti Microsoft.](storsimple-8000-contact-microsoft-support.md)


## <a name="turn-off-a-running-device"></a>Vypnutí běžícího zařízení
Spuštěné zařízení StorSimple může být nutné vypnout, pokud je přesouváno, vyřazeno z provozu nebo má nefunkční součást, kterou je třeba vyměnit. Kroky se liší v závislosti na tom, zda zařízení StorSimple je model 8100 nebo 8600. 8100 má jednu primární skříň, zatímco 8600 je zařízení se dvěma skříněmi s primární skříní a skříní EBOD. Tato část podrobně popisuje kroky k vypnutí spuštěného zařízení.

* [Zařízení s primárním krytem](#8100a)
* [Zařízení s krytem EBOD](#8600a)

### <a name="device-with-primary-enclosure-a-name8100a"></a>Zařízení s primárním krytem<a name="8100a">
Chcete-li vypnout zařízení řádným a řízeným způsobem, můžete to udělat prostřednictvím portálu Azure nebo prostřednictvím prostředí Windows PowerShell pro StorSimple. 

> [!IMPORTANT]
> Nevypínejte běžící zařízení pomocí tlačítka napájení na zadní straně zařízení.
> 
> Před vypnutím zařízení zkontrolujte, zda jsou všechny součásti zařízení v pořádku. Na webu Azure Portal přejděte na > **stav hardwaru****monitoru** **zařízení** > a ověřte, zda je stav všech součástí zelený. To platí pouze pro zdravý systém. Pokud se systém vypíná, aby nahradil nefunkční součást, zobrazí se pro příslušnou součást **stavu hardwaru**stav selhání (červený) nebo snížený (žlutý) stav.
> 
> 

Po přístupu k prostředí Windows PowerShell pro StorSimple nebo portálazure postupujte podle pokynů v [vypnutí zařízení StorSimple](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device). 

### <a name="device-with-ebod-enclosure-a-name8600a"></a>Zařízení s krytem EBOD<a name="8600a">
> [!IMPORTANT]
> Před vypnutím primární skříně a skříně EBOD se ujistěte, že jsou všechny součásti zařízení v pořádku. Na webu Azure Portal přejděte na > **stav hardwaru****monitoru** **zařízení** > a ověřte, zda jsou všechny součásti v pořádku.


#### <a name="to-shut-down-a-running-device-with-ebod-enclosure"></a>Vypnutí běžícího zařízení s skříní EBOD
1. Postupujte podle všech kroků uvedených v [vypnutí zařízení StorSimple](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device) pro primární skříň.
2. Po vypnutí primárního krytu vypněte EBOD vypnutím přepínačů Power and Cooling Module (PCM).
3. Chcete-li ověřit, zda byl EBOD vypnutý, zkontrolujte, zda jsou všechna světla na zadní straně skříně EBOD vypnuta.

> [!NOTE]
> Kabely SAS, které se používají k připojení skříně EBOD k primární skříni, by neměly být odstraněny, dokud není systém vypnut.

## <a name="next-steps"></a>Další kroky
Pokud při zapnutí nebo vypínání zařízení StorSimple narazíte na [podporu společnosti Microsoft, obraťte](storsimple-8000-contact-microsoft-support.md) se na podporu společnosti Microsoft.

