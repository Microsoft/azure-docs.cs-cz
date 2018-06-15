---
title: Vaše zařízení řady StorSimple 8000 vypnutí a zapnutí | Microsoft Docs
description: Vysvětluje, jak zapnout nové zařízení StorSimple, zapněte na zařízení, které byl vypnut, nebo došlo ke ztrátě napájení a vypnout spuštěné zařízení.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 8e9c6e6c-965c-4a81-81bd-e1c523a14c82
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 95fd00608be9cfafb4c703c32ec3ed4713855ca5
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2018
ms.locfileid: "27779444"
---
# <a name="turn-on-or-turn-off-your-storsimple-8000-series-device"></a>Zapněte nebo vypněte zařízení řady StorSimple 8000

## <a name="overview"></a>Přehled
Vypínání Microsoft Azure StorSimple zařízení není vyžadován jako součást operace normální systému. Potřebujete však zapnout nového zařízení nebo zařízení, které musí být vypnuté. Obecně platí vypnutí se vyžaduje v případech, ve kterých je musí nahradit selhání hardwaru, fyzicky přesunout jednotku nebo trvat zařízení mimo provoz. Tento kurz popisuje požadované postup pro zapnutí a vypnutí zařízení StorSimple v různých scénářích.

## <a name="turn-on-a-new-device"></a>Zapnout nové zařízení
Postup pro zapnutí zařízení StorSimple poprvé lišit v závislosti na tom, zda je zařízení 8100 nebo 8600 model. 8100 má jeden primární skříň, kdežto 8600 zařízení duální skříň s primární skříně a EBOD skříň. Podrobné kroky pro oba modely jsou popsané v následujících částech.

* [Nové zařízení s pouze primární skříň](#new-device-with-primary-enclosure-only)
* [Nové zařízení s EBOD skříň](#new-device-with-ebod-enclosure)

### <a name="new-device-with-primary-enclosure-only"></a>Nové zařízení s pouze primární skříň
Model StorSimple 8100 je jeden skříň zařízení. Zařízení obsahuje redundantní napájení a chlazení moduly (PCMs). Jak PCMs musí být nainstalovaný a připojený k jiné power zdroje k zajištění vysoké dostupnosti.

Proveďte následující kroky a zapojení kabeláže zařízení pro napájení.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

> [!NOTE]
> Pro dokončení zařízení nastavení a kabeláž pokyny, přejděte na [instalace zařízení StorSimple 8100](storsimple-8100-hardware-installation.md). Zajistěte, aby přesně podle pokynů.
> 
> 

### <a name="new-device-with-ebod-enclosure"></a>Nové zařízení s EBOD skříň
Model zařízení StorSimple 8600 má skříni primární i EBOD skříň. To vyžaduje jednotky k být společně kabelové připojení Serial Attached (SCSI SAS) a napájení.

Při nastavování toto zařízení poprvé, postupujte podle kroků pro SAS kabelů nejprve a potom postupujte podle pokynů pro kabeláž napájení.

[!INCLUDE [storsimple-sas-cable-8600](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

> [!NOTE]
> Pro dokončení zařízení nastavení a kabeláž pokyny, přejděte na [instalace zařízení StorSimple 8600](storsimple-8600-hardware-installation.md). Zajistěte, aby přesně podle pokynů.

## <a name="turn-on-a-device-after-shutdown"></a>Zapnout zařízení po vypnutí
Postup pro zapnutí zařízení StorSimple, poté, co byl vypnut se liší v závislosti na tom, zda je zařízení 8100 nebo 8600 model. 8100 má jeden primární skříň, kdežto 8600 zařízení duální skříň s primární skříně a EBOD skříň.

* [Zařízení s pouze primární skříň](#device-with-primary-enclosure-only)
* [Zařízení s EBOD skříň](#device-with-ebod-enclosure)

### <a name="device-with-primary-enclosure-only"></a>Zařízení s pouze primární skříň
Po vypnutí použijte následující postup zapnutí zařízení StorSimple s skříni primární a žádné EBOD skříň.

#### <a name="to-turn-on-a-device-with-a-primary-enclosure-only"></a>Chcete-li v zařízení s pouze primární skříň
1. Ujistěte se, že napájení přepínače na obou napájení a chlazení moduly (PCMs) jsou v pozici OFF. Pokud přepínače nejsou v pozici OFF, překlopit je na pozici OFF a počkejte indikátory vypnutí.
2. Zapněte zařízení překlopení hlavní přepínače na obou PCMs na pozici ON. Zařízení musí zapnout.
3. Zkontrolujte následující příkaz a ověřte, že zařízení je plně na:
   
   1. OK LED na obou PCM moduly jsou zelená.
   2. Stav LED na oba řadiče jsou plnou zeleně.
   3. Modré DIODU na jednom z řadičů bliká, což naznačuje, že kontroleru je aktivní.
      
      Pokud nejsou splněny některé z těchto podmínek, pak zařízení není v pořádku. Prosím [kontaktovat Microsoft Support](storsimple-8000-contact-microsoft-support.md).

### <a name="device-with-ebod-enclosure"></a>Zařízení s EBOD skříň
Po vypnutí použijte následující postup zapnutí zařízení StorSimple s skříni primární a EBOD skříň. Každý krok proveďte přesně tak, jak je popsáno v pořadí. Selhání k tomu může dojít ke ztrátě dat.

#### <a name="to-turn-on-a-device-with-a-primary-and-an-ebod-enclosure"></a>Chcete-li v zařízení s primární a EBOD skříň
1. Ujistěte se, že skříň EBOD je připojen k primární skříň. Další informace najdete v tématu [instalace zařízení StorSimple 8600](storsimple-8600-hardware-installation.md).
2. Ujistěte se, že napájení a chlazení moduly (PCMs) na EBOD i primární skříně jsou pozice OFF. Pokud přepínače nejsou v pozici OFF, překlopit je na pozici OFF a počkejte indikátory vypnutí.
3. Zapněte skříni EBOD první překlopení hlavní přepínače na obou PCMs na pozici ON. By měly být LED PCM. Zelená EBOD řadič DIODU na tuto jednotku znamená, že skříň EBOD na.
4. Zapněte primární skříň překlopení hlavní přepínače na obou PCMs na pozici ON. Na by teď měly být celý systém.
5. Ověřte, že LED SAS budou zelené, což zajišťuje, že je spojení mezi skříni EBOD a primární skříň dobrý.

## <a name="turn-on-a-device-after-a-power-loss"></a>Zapnout zařízení po výpadku napájení
Vypnout výpadku napájení nebo přerušení zařízení StorSimple. Výpadku napájení může dojít na jednom ze zdroje napájení nebo oba zdroje napájení. Postup obnovení se liší v závislosti na tom, jestli je zařízení 8100 nebo 8600 model. 8100 má jeden primární skříň, kdežto 8600 zařízení duální skříň s primární skříně a EBOD skříň. Tato část popisuje postup obnovení pro jednotlivé scénáře.

* [Zařízení s pouze primární skříň](#8100)
* [Zařízení s EBOD skříň](#8600)

### <a name="device-with-primary-enclosure-only-a-name8100"></a>Zařízení s pouze primární skříň<a name="8100">
Systém můžete pokračovat v jeho normální provoz při výpadku napájení do jednoho z jeho napájení. Ale k zajištění vysoké dostupnosti zařízení, obnovte power napájení. co nejdříve.

Pokud dojde k výpadku napájení nebo napájení přerušení na oba zdroje napájení, systém se vypne uspořádání a řízené způsobem. Po obnovení napájení, systém bude automaticky zapnout.

### <a name="device-with-ebod-enclosure-a-name8600"></a>Zařízení s EBOD skříň<a name="8600">
#### <a name="power-loss-on-one-power-supply"></a>Zadejte výpadku napájení na jednu napájení
Při výpadku napájení do jednoho z jeho napájení na skříni primární nebo EBOD skříň, můžete pokračovat systému jeho normální provoz. Ale k zajištění vysoké dostupnosti zařízení, obnovte power na napájení. co nejdříve.

#### <a name="power-loss-on-both-power-supplies-on-primary-and-ebod-enclosures"></a>Výpadku napájení na obou napájení na primární server a EBOD skříně
Pokud dojde k výpadku napájení nebo napájení přerušení na oba zdroje napájení, skříni EBOD ihned vypne a primární skříň vypne uspořádání a řízené způsobem. Po obnovení napájení, automaticky se spustí zařízení.

Napájení vypnut ručně, proveďte následující kroky k obnovení napájení systému.

1. Zapněte EBOD skříň.
2. Po na skříni EBOD zapněte primární skříň.

### <a name="power-loss-on-both-power-supplies-on-ebod-enclosure"></a>Výpadku napájení na obou napájení na EBOD skříň
Při nastavování kabely, ujistěte se, že EBOD je ještě nikdy nepřipojili samostatně k samostatné jednotky PDU. Pokud EBOD a primární skříň selhat ve stejnou dobu, bude systém obnovit.

Pokud jenom skříni EBOD selže na oba zdroje napájení, systém automaticky neobnoví. Proveďte následující kroky, chcete-li na serveru a obnovte ji do stavu v pořádku:

1. Pokud je zapnutá primární skříň, vypněte napájení a chlazení moduly (PCMs).
2. Počkejte několik minut, než systém a ukončí se.
3. Zapněte EBOD skříň.
4. Po na skříni EBOD zapněte primární skříň.

## <a name="turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost"></a>Zapnout v zařízení po primární a dojde ke ztrátě připojení EBOD skříň
Pokud připojení dojde ke ztrátě mezi řadičem pohotovostní a odpovídající EBOD řadiče, zařízení nadále fungovat. Pokud dojde ke ztrátě připojení mezi řadičem active systému a odpovídající EBOD řadiče, mělo dojít k převzetí služeb při selhání a zařízení by měly být nadále fungovat normálně.

Pokud jsou odebrány i kabely Serial Attached (SCSI SAS) nebo je porušeno připojení mezi skříni EBOD a primární skříň, zařízení přestane fungovat. V tomto okamžiku proveďte následující kroky.

### <a name="to-turn-on-the-device-after-connection-is-lost"></a>Chcete-li v zařízení po ztrátě připojení
1. Přístup na zadní straně zařízení.
2. Pokud SAS kabel mezi skříni EBOD a primární skříň se přeruší, SAS dráhy všechny LED na skříni EBOD bude vypnuto.
3. Vypnutí napájení a chlazení moduly (PCMs) na skříni EBOD a primární skříň.
4. Počkejte, dokud všechny indikátory na zadní straně obou skříních vypnout.
5. Vložte SAS kabely a zkontrolujte, zda je funkční připojení mezi skříni EBOD a primární skříň.
6. Zapněte skříni EBOD první překlopení obou PCM přepínače na pozici ON.
7. Zajistěte, aby byl skříni EBOD na kontrolou, jestli zelená DIODU je ON.
8. Zapněte primární skříň.
9. Zajistěte, aby byl primární skříň na kontrolou, že řadiče zelená DIODU je ON.
10. Ověřte, že skříň EBOD připojení ke skříni primární dobrý kontrolou, jestli dráhy SAS LED (čtyři na jeden kontroler EBOD) jsou všechny ON.

> [!IMPORTANT]
> Pokud SAS kabely poškozený nebo připojení mezi skříni EBOD a primární skříň není funkční, když zapnete systému, protože budou přenášeny do režimu obnovení. Prosím [kontaktovat Microsoft Support](storsimple-8000-contact-microsoft-support.md) v takovém případě.


## <a name="turn-off-a-running-device"></a>Vypnout spuštěné zařízení
Vypnout, pokud se během přesunu, vyřazeno z provozu, nebo má chybně fungující komponenty, která je nutné vyměnit potřebovat spuštěné zařízení StorSimple. Postup se liší v závislosti na tom, jestli zařízení StorSimple je 8100 nebo 8600 model. 8100 má jeden primární skříň, kdežto 8600 zařízení duální skříň s primární skříně a EBOD skříň. V této části najdete postup vypnutí spuštěné zařízení.

* [Zařízení s primární skříň](#8100a)
* [Zařízení s EBOD skříň](#8600a)

### <a name="device-with-primary-enclosure-a-name8100a"></a>Zařízení s primární skříň<a name="8100a">
Uspořádání a řízené způsobem vypnout zařízení, můžete provést prostřednictvím portálu Azure nebo prostřednictvím Windows Powershellu pro StorSimple. 

> [!IMPORTANT]
> Nelze vypnout spuštěné zařízení pomocí tlačítka napájení na zadní straně zařízení.
> 
> Před ukončením zařízení, ujistěte se, že všechny součásti zařízení jsou v pořádku. Na portálu Azure přejděte do **zařízení** > **monitorování** > **stavu hardwaru**a ověřte, zda je stav všech součástí zelená. To platí pouze pro systém v pořádku. Pokud systému je vypínán dolů k nahrazení chybně fungující komponenty, zobrazí se nezdařila (červený) nebo snížení (žlutý) stav příslušných součástí **stavu hardwaru**.
> 
> 

Poté, co otevřete prostředí Windows PowerShell pro zařízení StorSimple nebo portálu Azure, postupujte podle kroků v [vypnout zařízení StorSimple](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device). 

### <a name="device-with-ebod-enclosure-a-name8600a"></a>Zařízení s EBOD skříň<a name="8600a">
> [!IMPORTANT]
> Před ukončením skříni primární a EBOD skříň, zajistěte, aby byly všechny součásti zařízení v pořádku. Na portálu Azure přejděte do **zařízení** > **monitorování** > **stavu hardwaru**a ověřte, zda jsou všechny součásti v pořádku.


#### <a name="to-shut-down-a-running-device-with-ebod-enclosure"></a>Vypnutí spuštěné zařízení s EBOD skříň
1. Proveďte všechny kroky uvedené v [vypnout zařízení StorSimple](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device) pro primární skříň.
2. Po ukončení primární skříň, ukončení EBOD překlopení vypnutí napájení a chlazení modulu (PCM) přepínače.
3. Pokud chcete ověřit, má EBOD vypnutý, zkontrolujte, zda všechny indikátory na zadní straně skříni EBOD jsou vypnutí.

> [!NOTE]
> SAS kabely, které slouží k připojení ke skříni primární skříni EBOD by se neměly odebírat až po systém je vypnutý.

## <a name="next-steps"></a>Další postup
[Kontaktujte Microsoft Support](storsimple-8000-contact-microsoft-support.md) Pokud narazíte na problémy při zapnutí nebo vypnutí zařízení StorSimple.

