---
title: Zapnout nebo vypnout zařízení řady StorSimple 8000
description: Vysvětluje, jak zapnout nové zařízení StorSimple, zapnout zařízení, které se ukončilo nebo ztratilo napájení, a vypnutí spuštěného zařízení.
author: alkohli
ms.assetid: 8e9c6e6c-965c-4a81-81bd-e1c523a14c82
ms.service: storsimple
ms.topic: how-to
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c8f4269235c494ff9dd8d1bf8e0ef940562f8927
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85515271"
---
# <a name="turn-on-or-turn-off-your-storsimple-8000-series-device"></a>Zapnutí nebo vypnutí zařízení řady StorSimple 8000

## <a name="overview"></a>Přehled
Vypínání Microsoft Azure StorSimple zařízení není vyžadováno jako součást běžné systémové operace. Může se ale stát, že budete muset zapnout nové zařízení nebo zařízení, které se musí vypnout. Obecně se vyžaduje vypnutí v případech, kdy je třeba vyměnit neúspěšný hardware, fyzicky přesunout jednotku nebo převzít zařízení ze služby. Tento kurz popisuje požadovaný postup pro zapnutí a vypnutí zařízení StorSimple v různých scénářích.

## <a name="turn-on-a-new-device"></a>Zapnout nové zařízení
Postup při prvním zapnutí zařízení StorSimple se liší v závislosti na tom, jestli je zařízení model 8100 nebo 8600. 8100 má jedinou primární skříň, zatímco 8600 je zařízení se dvěma plášťy s primární skříní a EBOD skříňkou. Podrobný postup pro oba modely je popsaný v následujících částech.

* [Nové zařízení jenom s primární skříňkou](#new-device-with-primary-enclosure-only)
* [Nové zařízení s EBOD skříní](#new-device-with-ebod-enclosure)

### <a name="new-device-with-primary-enclosure-only"></a>Nové zařízení jenom s primární skříňkou
Model StorSimple 8100 je zařízení s jednou skříní. Vaše zařízení obsahuje redundantní moduly napájení a chlazení (PCMs). Aby se zajistila vysoká dostupnost, musí být nainstalovaná a připojená PCMs k různým zdrojům napájení.

Pomocí následujících kroků Zapojte zařízení do elektrického napájení.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

> [!NOTE]
> Kompletní nastavení zařízení a pokyny k kabeláži získáte, když přejdete na [nainstalovat zařízení StorSimple 8100](storsimple-8100-hardware-installation.md). Ujistěte se, že přesně budete postupovat podle pokynů.
> 
> 

### <a name="new-device-with-ebod-enclosure"></a>Nové zařízení s EBOD skříní
Model StorSimple 8600 má primární skříň i skříň EBOD. To vyžaduje, aby jednotky byly zapojeny pro připojení a napájení přes SAS (Serial Attached SCSI).

Při prvním nastavování tohoto zařízení proveďte kroky pro kabely SAS a pak dokončete postup pro kabely napájení.

[!INCLUDE [storsimple-sas-cable-8600](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

> [!NOTE]
> Kompletní nastavení zařízení a pokyny k kabeláži získáte, když přejdete na [nainstalovat zařízení StorSimple 8600](storsimple-8600-hardware-installation.md). Ujistěte se, že přesně budete postupovat podle pokynů.

## <a name="turn-on-a-device-after-shutdown"></a>Zapnutí zařízení po vypnutí
Postup zapnutí zařízení StorSimple po jeho vypnutí se liší v závislosti na tom, jestli je zařízení model 8100 nebo 8600. 8100 má jedinou primární skříň, zatímco 8600 je zařízení se dvěma plášťy s primární skříní a EBOD skříňkou.

* [Jenom zařízení s primární skříňkou](#device-with-primary-enclosure-only)
* [Zařízení s EBOD skříní](#device-with-ebod-enclosure)

### <a name="device-with-primary-enclosure-only"></a>Jenom zařízení s primární skříňkou
Po vypnutí použijte následující postup, chcete-li zapnout zařízení StorSimple s primární skříní a bez EBOD skříně.

#### <a name="to-turn-on-a-device-with-a-primary-enclosure-only"></a>Zapnutí zařízení jenom s primární skříňkou
1. Ujistěte se, že přepínače napájení v modulech napájení a chladicích modulech (PCMs) jsou v VYPNUTé pozici. Pokud přepínače nejsou mimo místo, překlopte je na VOLNou a počkejte, než se indikátory vrátí.
2. Zapněte zařízení překlopením přepínačů napájení na PCMs na pozici. Zařízení by se mělo zapnout.
3. Zkontrolujte následující postup a ověřte, zda je zařízení plně zapnuté:
   
   1. Diody OK u obou modulů PCM jsou zelené.
   2. Stavová dioda v obou řadičích je plná zelená.
   3. Modrý indikátor LED na jednom z řadičů je blikající, což indikuje, že je kontroler aktivní.
      
      Pokud některá z těchto podmínek není splněná, zařízení není v pořádku. [Kontaktujte prosím podpora Microsoftu](storsimple-8000-contact-microsoft-support.md).

### <a name="device-with-ebod-enclosure"></a>Zařízení s EBOD skříní
Po vypnutí použijte následující postup, kterým zapnete zařízení StorSimple s primární skříní a skříní EBOD. Proveďte všechny kroky v sekvenci přesně tak, jak je popsáno. V takovém případě by mohlo dojít ke ztrátě dat.

#### <a name="to-turn-on-a-device-with-a-primary-and-an-ebod-enclosure"></a>Zapnutí zařízení s primární a EBODou skříňkou
1. Ujistěte se, že skříň EBOD je připojená k primární skříni. Další informace najdete v tématu [instalace zařízení StorSimple 8600](storsimple-8600-hardware-installation.md).
2. Ujistěte se, že moduly napájení a chlazení (PCMs) v EBOD i v primárních skříních jsou v VYPNUTé pozici. Pokud přepínače nejsou mimo místo, překlopte je na VOLNou a počkejte, než se indikátory vrátí.
3. Nejprve zapněte EBOD skříň tím, že překlopením přepínače napájení v obou PCMs na pozici na pozici. Indikátory LED pro PCM by měly být zelené. Zelený kontroler EBOD LED na této jednotce indikuje, že skříň EBOD je zapnutá.
4. Zapněte primární skříň tím, že překlopete přepínače napájení na PCMs na pozici. Teď by měl být zapnutý celý systém.
5. Ověřte, že jsou indikátory LED SAS zelené, což zajistí, že spojení mezi skříní EBOD a primární skříňkou je dobré.

## <a name="turn-on-a-device-after-a-power-loss"></a>Zapnutí zařízení po výpadku napájení
Výpadek napájení nebo přerušení může vypnout zařízení StorSimple. Výpadek napájení se může vyskytnout na jednom ze zdrojů napájení nebo na zdroji napájení. Postup obnovení se liší v závislosti na tom, jestli je zařízení model 8100 nebo 8600. 8100 má jedinou primární skříň, zatímco 8600 je zařízení se dvěma plášťy s primární skříní a EBOD skříňkou. Tato část popisuje postup obnovení pro jednotlivé scénáře.

* [Jenom zařízení s primární skříňkou](#8100)
* [Zařízení s EBOD skříní](#8600)

### <a name="device-with-primary-enclosure-only"></a>Jenom zařízení s primární skříňkou <a name="8100"></a>
Systém může pokračovat v běžném provozu, pokud dojde ke snížení výkonu některého z jeho zdrojů napájení. Chcete-li však zajistit vysokou dostupnost zařízení, obnovte napájení podle potřeby co nejdříve.

Pokud dojde k výpadku napájení nebo přerušení napájení u napájení, systém se vypne přesně podle pořadí. Po obnovení napájení se systém automaticky zapne.

### <a name="device-with-ebod-enclosure"></a>Zařízení s EBOD skříní <a name="8600"></a>
#### <a name="power-loss-on-one-power-supply"></a>Výpadek napájení u jednoho zdroje napájení
Systém může pokračovat v běžném provozu, pokud dojde ke snížení výkonu některého z jeho napájení v primární skříni nebo skříni EBOD. Chcete-li však zajistit vysokou dostupnost zařízení, obnovte napájení podle potřeby co nejdříve.

#### <a name="power-loss-on-both-power-supplies-on-primary-and-ebod-enclosures"></a>Výpadek napájení u napájení u primárních i EBOD skříní
Pokud dojde k výpadku napájení nebo přerušení napájení u napájení, skříň EBOD se okamžitě vypne a primární skříň se vypne přesně podle pořadí. Po obnovení napájení se zařízení automaticky spustí.

Pokud je napájení vypnuto ručně, proveďte následující kroky k obnovení napájení systému.

1. Zapněte EBOD skříň.
2. Jakmile je skříň EBOD zapnutá, zapněte primární skříň.

### <a name="power-loss-on-both-power-supplies-on-ebod-enclosure"></a>Výpadek napájení u napájení v EBOD skříni
Při nastavování kabelů je nutné zajistit, aby se EBOD nikdy nepřipojovaly samostatně k samostatné jednotce PDU. Pokud EBOD a primární skříň selže ve stejnou dobu, systém se obnoví.

Pokud se v obou zdrojích napájení selže jenom skříň EBOD, systém se automaticky neobnoví. Proveďte následující kroky, abyste systém zapnuli a obnovili ho do stavu v pořádku:

1. Pokud je primární skříň zapnutá, vypínejte moduly napájení a chlazení (PCMs).
2. Počkejte několik minut, než se systém vypíná.
3. Zapněte EBOD skříň.
4. Jakmile je skříň EBOD zapnutá, zapněte primární skříň.

## <a name="turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost"></a>Zapnout zařízení po ztrátě primárního a EBOD připojení
Pokud dojde ke ztrátě spojení mezi pohotovostním a odpovídajícím řadičem EBOD, zařízení bude i nadále fungovat. Pokud dojde ke ztrátě připojení mezi systémovým aktivním řadičem a odpovídajícím řadičem EBOD, mělo by dojít k převzetí služeb při selhání a zařízení by mělo dál fungovat jako normálně.

Když dojde k odebrání kabelů SAS (Serial Attached SCSI) nebo připojení mezi skříní EBOD a primární skříní je závažné, přestane zařízení fungovat. V tomto okamžiku proveďte následující kroky.

### <a name="to-turn-on-the-device-after-connection-is-lost"></a>Zapnutí zařízení po ztrátě připojení
1. Přístup k zadní části zařízení.
2. Pokud je připojení kabelem SAS mezi skříňkou EBOD a primární skříňkou přerušeno, všechny indikátory SAS na skříni EBOD budou vypnuty.
3. Vypněte moduly napájení a chlazení (PCMs) na skříni EBOD a v primární skříni.
4. Počkejte, než se všechna světla v zadní části obou skříní vypnou.
5. Znovu vložte kabely SAS a zajistěte, aby existovalo dobré spojení mezi skříní EBOD a primární skříňkou.
6. Nejprve zapněte EBOD skříň tím, že překlopete přepínače PCM na pozici.
7. Ujistěte se, že je EBOD skříň zapnutá, a zkontrolujte, že je ZAPNUTý zelený indikátor LED.
8. Zapněte primární skříň.
9. Ujistěte se, že je primární skříň zapnutá pomocí kontroly, jestli je ZAPNUTý kontroler zelený indikátor.
10. Ověřte, jestli je připojení skříně EBOD k primární skříni dobré, a to tak, že zkontrolujete, jestli jsou všechny indikátory LED SAS Lane (čtyři na řadič EBOD).

> [!IMPORTANT]
> Pokud jsou kabely SAS vadné nebo spojení mezi skříňkou EBOD a primární skříňkou není dobré, při zapnutí systému se přejdou do režimu obnovení. Pokud k tomu dojde, [kontaktujte prosím podpora Microsoftu](storsimple-8000-contact-microsoft-support.md) .


## <a name="turn-off-a-running-device"></a>Vypnutí běžícího zařízení
Běžící zařízení StorSimple může být potřeba vypnout, pokud se právě přesouvá, přijímá služby nebo má nefunkční komponentu, kterou je potřeba vyměnit. Postup se liší v závislosti na tom, jestli je zařízení StorSimple model 8100 nebo 8600. 8100 má jedinou primární skříň, zatímco 8600 je zařízení se dvěma plášťy s primární skříní a EBOD skříňkou. Tato část podrobně popisuje postup vypnutí spuštěného zařízení.

* [Zařízení s primární skříňkou](#8100a)
* [Zařízení s EBOD skříní](#8600a)

### <a name="device-with-primary-enclosure"></a>Zařízení s primární skříňkou <a name="8100a"></a>
Pokud chcete zařízení vypnout podle pořadí a řízeného způsobu, můžete to provést prostřednictvím Azure Portal nebo přes Windows PowerShell pro StorSimple. 

> [!IMPORTANT]
> Neukončí spuštěné zařízení pomocí tlačítka napájení na zadní straně zařízení.
> 
> Před vypnutím zařízení se ujistěte, že jsou všechny součásti zařízení v pořádku. V Azure Portal přejděte na **zařízení**  >  **monitorovat**stav  >  **hardwaru**a ověřte, že stav všech komponent je zelený. To platí jenom pro dobrý systém. Pokud se systém vypíná, aby nahradil nefunkční komponentu, zobrazí se u příslušné součásti ve **stavu hardwaru**stav neúspěšné (červené) nebo zhoršený (žlutý).
> 
> 

Po přístupu k Windows PowerShell pro StorSimple nebo Azure Portal postupujte podle kroků v části [vypnutí zařízení StorSimple](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device). 

### <a name="device-with-ebod-enclosure"></a>Zařízení s EBOD skříní <a name="8600a"></a>
> [!IMPORTANT]
> Před vypnutím primární skříně a skříně EBOD zajistěte, aby byly všechny součásti zařízení v dobrém stavu. V Azure Portal přejděte na **zařízení**  >  **monitor**  >  **stav hardwaru**a ověřte, zda jsou všechny součásti v pořádku.


#### <a name="to-shut-down-a-running-device-with-ebod-enclosure"></a>Vypnutí běžícího zařízení pomocí skříně EBOD
1. Postupujte podle všech kroků uvedených v části [vypnutí zařízení StorSimple](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device) pro primární skříň.
2. Po vypnutí primární skříně vypněte EBOD překlopením přepínačů PCM (Power and chladicí modul).
3. Pokud chcete ověřit, že se EBOD vypnul, zkontrolujte, že jsou vypnutá všechna světla zadní části skříně EBOD.

> [!NOTE]
> Kabely SAS používané pro připojení skříně EBOD k primární skříni by se neměly odebírat až po vypnutí systému.

## <a name="next-steps"></a>Další kroky
Pokud při zapnutí nebo vypnutí zařízení StorSimple dojde k problémům, [kontaktujte podpora Microsoftu](storsimple-8000-contact-microsoft-support.md) .

