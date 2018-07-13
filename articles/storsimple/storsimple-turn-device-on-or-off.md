---
title: Zapněte nebo vypněte zařízení řady StorSimple 8000 | Dokumentace Microsoftu
description: Vysvětluje, jak zapnout nové zařízení StorSimple, zapnutí zařízení, které vypnul nebo došlo ke ztrátě napájení a vypněte běžící zařízení.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38670962"
---
# <a name="turn-on-or-turn-off-your-storsimple-8000-series-device"></a>Zapněte nebo vypněte zařízení řady StorSimple 8000

## <a name="overview"></a>Přehled
Vypínání zařízení s Microsoft Azure StorSimple se nevyžaduje jako součást operace normální systému. Ale budete muset zapnout nové zařízení nebo zařízení, které musí být vypnut. Obecně platí je potřeba vypnout v případech, kdy musíte nahradit selhání hardwaru, fyzicky přesunout jednotka nebo trvat zařízení mimo provoz. Tento kurz popisuje požadovaný postup pro zapnutí a vypnutí zařízení StorSimple v různých scénářích.

## <a name="turn-on-a-new-device"></a>Zapnout nové zařízení
Postup pro zapnutí zařízení StorSimple poprvé lišit v závislosti na tom, jestli je zařízení 8100 nebo 8600 model. 8100 má jeden primární skříň, vzhledem k tomu je duální skříň zařízení s primární skříň a skříně EBOD 8600. Podrobné kroky pro oba modely jsou popsané v následujících částech.

* [Nové zařízení s pouze primární skříň](#new-device-with-primary-enclosure-only)
* [Nové zařízení s podporou rozpoznávání skříní EBOD](#new-device-with-ebod-enclosure)

### <a name="new-device-with-primary-enclosure-only"></a>Nové zařízení s pouze primární skříň
StorSimple 8100 model je jeden skříň zařízení. Zařízení obsahuje redundantní napájení a chlazení moduly (PCMs). Jak PCMs musí být nainstalované a připojený k různým zdrojům napájení k zajištění vysoké dostupnosti.

Proveďte následující kroky a zapojení kabeláže zařízení za výkon.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

> [!NOTE]
> Nastavení kompletní zařízení a kabeláž pokyny v části [instalaci zařízení StorSimple 8100](storsimple-8100-hardware-installation.md). Ujistěte se, že přesně postupujte podle pokynů.
> 
> 

### <a name="new-device-with-ebod-enclosure"></a>Nové zařízení s podporou rozpoznávání skříní EBOD
StorSimple 8600 modelu má primární skříň a EBOD skříň. To vyžaduje jednotky být zapojené dohromady pro připojení k rozhraní SCSI SAS (Serial Attached) a napájení.

Při nastavování zařízení poprvé, postupujte podle kroků pro SAS kabelů nejprve a potom postupujte podle pokynů pro kabeláž napájení.

[!INCLUDE [storsimple-sas-cable-8600](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

> [!NOTE]
> Nastavení kompletní zařízení a kabeláž pokyny v části [instalaci zařízení StorSimple 8600](storsimple-8600-hardware-installation.md). Ujistěte se, že přesně postupujte podle pokynů.

## <a name="turn-on-a-device-after-shutdown"></a>Zapněte zařízení po vypnutí
Postup pro zapnutí zařízení StorSimple, poté, co byl vypnut se liší v závislosti na tom, jestli je zařízení 8100 nebo 8600 model. 8100 má jeden primární skříň, vzhledem k tomu je duální skříň zařízení s primární skříň a skříně EBOD 8600.

* [Zařízení s pouze primární skříň](#device-with-primary-enclosure-only)
* [Zařízení s podporou rozpoznávání skříní EBOD](#device-with-ebod-enclosure)

### <a name="device-with-primary-enclosure-only"></a>Zařízení s pouze primární skříň
Po vypnutí pomocí následujícího postupu zapnutí zařízení StorSimple s primární skříň a žádné EBOD skříň.

#### <a name="to-turn-on-a-device-with-a-primary-enclosure-only"></a>Chcete-li na zařízení s pouze primární skříň
1. Ujistěte se, že výkon přepínače na obou napájení a chlazení moduly (PCMs) jsou na jinou pozici. Není-li přepínače na pozici, vypnuto, překlopit na pozici vypnuto a počkejte, světla vypnutí.
2. Zapněte zařízení podle překlopení napájení přepínače na obou PCMs do polohy zapnuto. Zařízení by měl zapnout.
3. Zaškrtnutím následujícího ověřte, že zařízení je plně na:
   
   1. OK LED na oba moduly PCM jsou zelená.
   2. Stav LED na obou kontrolerech jsou plné zelená.
   3. Modré Indikátor na jeden z řadičů je bliká vám kontrolka, což znamená, že je aktivní kontroler.
      
      Pokud se nesplní žádné z těchto podmínek, pak vaše zařízení není v pořádku. Prosím [obraťte se na Microsoft Support](storsimple-8000-contact-microsoft-support.md).

### <a name="device-with-ebod-enclosure"></a>Zařízení s podporou rozpoznávání skříní EBOD
Po vypnutí použijte následující postup zapnutí zařízení StorSimple s primární skříň a skříně EBOD. Každý krok proveďte přesně tak, jak je popsáno v pořadí. Selhání k tomu může dojít ke ztrátě.

#### <a name="to-turn-on-a-device-with-a-primary-and-an-ebod-enclosure"></a>Chcete-li na zařízení s primární a skříně EBOD
1. Ujistěte se, že skříň EBOD je připojen k primární skříň. Další informace najdete v tématu [instalaci zařízení StorSimple 8600](storsimple-8600-hardware-installation.md).
2. Ujistěte se, že napájení a chlazení moduly (PCMs) na EBOD i primární skříních jsou na jinou pozici. Není-li přepínače na pozici, vypnuto, překlopit na pozici vypnuto a počkejte, světla vypnutí.
3. Zapněte skříň EBOD první překlopení napájení přepínače na obou PCMs do polohy zapnuto. LED PCM by měl být zelená. Zelená kontroleru EBOD LED na této jednotce znamená, že skříň EBOD na.
4. Zapněte primární skříň překlopení napájení přepínače na obou PCMs do polohy zapnuto. Celý systém by měl nyní obsahovat.
5. Ověřte, že LED SAS zelené, které zajišťuje, že je dobré připojení mezi EBOD skříň a primární zařízení.

## <a name="turn-on-a-device-after-a-power-loss"></a>Zapněte zařízení po výpadku napájení
Zařízení StorSimple můžete vypnout výpadku napájení nebo přerušení. Výpadku napájení může dojít na jednom zdroji napájení nebo oba napájení. Postup obnovení se liší v závislosti na tom, jestli je zařízení 8100 nebo 8600 modelu. 8100 má jeden primární skříň, vzhledem k tomu je duální skříň zařízení s primární skříň a skříně EBOD 8600. Tato část popisuje postup obnovení pro jednotlivé scénáře.

* [Zařízení s pouze primární skříň](#8100)
* [Zařízení s podporou rozpoznávání skříní EBOD](#8600)

### <a name="device-with-primary-enclosure-only-a-name8100"></a>Zařízení s pouze primární skříň <a name="8100">
Systém můžete pokračovat v normálním provozu při výpadku napájení na jeden z jeho napájení. Ale k zajištění vysoké dostupnosti zařízení obnovíte power napájení. co nejdříve.

Pokud dojde k výpadku napájení nebo přerušení napájení na obou napájení, systém se vypne v řádné a systematicky. Po obnovení výkon systému bude automaticky zapnout.

### <a name="device-with-ebod-enclosure-a-name8600"></a>Zařízení s podporou rozpoznávání skříní EBOD <a name="8600">
#### <a name="power-loss-on-one-power-supply"></a>Zadejte jeden výkonu při výpadku napájení
Systém můžete pokračovat v normálním provozu při výpadku napájení na jeden z jeho napájení na primární zařízení nebo EBOD skříň. Ale k zajištění vysoké dostupnosti zařízení, obnovte prosím power do napájení. co nejdříve.

#### <a name="power-loss-on-both-power-supplies-on-primary-and-ebod-enclosures"></a>Při výpadku napájení na obou napájení na primárním serverem a skříně EBOD
Pokud dojde k výpadku napájení nebo přerušení napájení na obou napájení, skříň EBOD ihned vypne a primární zařízení vypne v řádné a systematicky. Když se obnoví power zařízení se spustí automaticky.

Pokud výkon je ručně vypnout, pak proveďte následující kroky k obnovení napájení systému.

1. Zapněte EBOD skříň.
2. Po na skříň EBOD zapněte primární skříň.

### <a name="power-loss-on-both-power-supplies-on-ebod-enclosure"></a>Při výpadku napájení na obou napájení na skříň EBOD
Když nastavíte kabely, musíte zajistit, že EBOD se ještě nikdy nepřipojili samostatně na samostatné jednotky PDU. Pokud EBOD a primární skříň selhat ve stejnou dobu, systém se obnoví.

Pokud pouze skříň EBOD selže v obou napájení, systém automaticky neobnoví. Proveďte následující kroky, chcete-li v systému a jeho obnovení stavu v pořádku:

1. Pokud je zapnutá primární skříň, vypněte napájení a chlazení moduly (PCMs).
2. Počkejte několik minut, než systém vypnout.
3. Zapněte EBOD skříň.
4. Po na skříň EBOD zapněte primární skříň.

## <a name="turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost"></a>Zapněte zařízení po primární a dojde ke ztrátě připojení skříň EBOD
Pokud je mezi kontroler v pohotovostním režimu a odpovídající kontroleru EBOD ke ztrátě připojení, bude zařízení dál fungovat. Pokud dojde ke ztrátě připojení mezi aktivním řadiči systému a odpovídající kontroleru EBOD, by mělo dojít k převzetí služeb při selhání a zařízení by ml fungovat normálním způsobem.

Když se odeberou i kabely Serial Attached (SCSI SAS) nebo porušeno připojení mezi EBOD skříň a primární zařízení, zařízení přestane fungovat. V tomto okamžiku proveďte následující kroky.

### <a name="to-turn-on-the-device-after-connection-is-lost"></a>Chcete-li na zařízení, jakmile dojde ke ztrátě připojení
1. Přístup na zadní straně zařízení.
2. Pokud připojení SAS kabel mezi EBOD skříň a primární zařízení bylo přerušeno, SAS lane LED na skříň EBOD bude vypnuto.
3. Vypněte napájení a chlazení moduly (PCMs) na skříň EBOD a primární zařízení.
4. Počkejte, dokud všechny indikátory na zadní straně obě skříních vypnout.
5. Vložte SAS kabely a ujistěte se, že je dobré připojení mezi EBOD skříň a primární zařízení.
6. Zapněte skříň EBOD první překlopení oba přepínače PCM do polohy zapnuto.
7. Zkontrolujte, že skříň EBOD na tak, že zkontrolujete, že zelený Indikátor je ON.
8. Zapněte primární skříň.
9. Zajistěte, aby byl primární zařízení na tak, že zkontrolujete, že řadič zelené Indikátor je ON.
10. Ověřte, že připojení skříň EBOD s primární podporou rozpoznávání skříní dobré tak, že zkontrolujete, že lane SAS LED (čtyři za kontroleru EBOD) jsou všechny ON.

> [!IMPORTANT]
> Pokud je SAS kabely vadných nebo připojení mezi EBOD skříň a primární zařízení nevhodní, když zapnete systému, přejde do režimu obnovení. Prosím [obraťte se na Microsoft Support](storsimple-8000-contact-microsoft-support.md) v této situaci.


## <a name="turn-off-a-running-device"></a>Vypnout spuštěné zařízení
Spuštěné zařízení StorSimple může být potřeba vypnout, pokud se přesune, vyřazen ze služby, nebo je nefunkční komponentu, která potřebuje vyměnit. Postup se liší v závislosti na tom, jestli je zařízení StorSimple 8100 nebo 8600 model. 8100 má jeden primární skříň, vzhledem k tomu je duální skříň zařízení s primární skříň a skříně EBOD 8600. Tato část podrobně popisuje kroky pro vypnutí spuštěné zařízení.

* [Zařízení s podporou rozpoznávání skříní primární](#8100a)
* [Zařízení s podporou rozpoznávání skříní EBOD](#8600a)

### <a name="device-with-primary-enclosure-a-name8100a"></a>Zařízení s podporou rozpoznávání skříní primární <a name="8100a">
Vypnutí zařízení za řádné a systematicky, vám pomůžou ho na webu Azure portal nebo pomocí Windows Powershellu pro StorSimple. 

> [!IMPORTANT]
> Nevypínejte spuštěné zařízení pomocí tlačítka napájení na zadní straně zařízení.
> 
> Před ukončením zařízení, ujistěte se, že všechny komponenty zařízení jsou v pořádku. Na webu Azure Portal, přejděte na **zařízení** > **monitorování** > **stav hardwaru**a ověřte, zda je stav všech součástí zeleně. To platí pouze pro stavu systému. Pokud systém je vypínán dolů k nahrazení nefunkční komponenty, se zobrazí selhání (červená) nebo degradovaný stav (žlutý) pro příslušné součásti v **stav hardwaru**.
> 
> 

Poté, co můžete získat přístup k prostředí Windows PowerShell pro StorSimple nebo na webu Azure portal, postupujte podle kroků v [vypněte zařízení StorSimple](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device). 

### <a name="device-with-ebod-enclosure-a-name8600a"></a>Zařízení s podporou rozpoznávání skříní EBOD <a name="8600a">
> [!IMPORTANT]
> Před ukončením primární skříň a EBOD skříň, ujistěte se, že všechny komponenty zařízení jsou v pořádku. Na webu Azure Portal, přejděte na **zařízení** > **monitorování** > **stav hardwaru**a ověřte, že všechny komponenty jsou v pořádku.


#### <a name="to-shut-down-a-running-device-with-ebod-enclosure"></a>Pro vypnutí spuštěné zařízení s podporou rozpoznávání skříní EBOD
1. Postupujte podle všech kroků uvedených v [vypněte zařízení StorSimple](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device) pro primární skříň.
2. Poté, co je vypnut primární skříň, vypněte EBOD kliknutím překlopení vypnout napájení a chlazení modulu (PCM) přepínače.
3. Pokud chcete ověřit, že byl vypnut EBOD, zkontrolujte, zda jsou všechny indikátory na zadní straně skříň EBOD vypnout.

> [!NOTE]
> SAS kabely, které se používají k připojení skříň EBOD na primární zařízení by se neměly odebírat až po systém je vypnutý.

## <a name="next-steps"></a>Další postup
[Obraťte se na Microsoft Support](storsimple-8000-contact-microsoft-support.md) Pokud narazíte na problémy při zapnutí nebo vypnutí zařízení StorSimple.

