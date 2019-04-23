---
title: StorSimple 8000 series hardwarové komponenty nahrazení | Dokumentace Microsoftu
description: Popisuje, jak bezpečně nahraďte PCMs, baterie, moduly řadič, EBOD řadiče, diskové jednotky a skříně zařízení StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: e05a37122647d4979089f0ba00b1fc15f9b84b0f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60321677"
---
# <a name="replace-a-hardware-component-on-your-storsimple-8000-series-device"></a>Vyměňovat hardwarové součásti na vašem zařízení řady StorSimple 8000

## <a name="overview"></a>Přehled
Kurzy hardwarové komponenty nahrazení popisují hardwarové komponenty Microsoft Azure StorSimple 8000 series zařízení a kroky potřebné k odebrání a nahradíte je. Tento článek popisuje ikony bezpečnost, poskytuje odkazy na podrobné kurzy a komponenty, které jsou replaceable obsahuje.

> [!IMPORTANT]
> Než se pokusíte odebrat nebo nahradit jakoukoli komponentu StorSimple, ujistěte se, abyste si [bezpečnosti ikonu konvence](#safety-icon-conventions) a dalších [bezpečnostní opatření](storsimple-safety.md).


### <a name="safety-icon-conventions"></a>Bezpečný přístup z více ikonu konvence
Následující tabulka popisuje bezpečnosti ikony používané v těchto kurzech. Pozornosti tyto ikony bezpečný přístup z více jak projít kroky k odebrání a nahrazení komponenty zařízení.

| Ikona | Text | Další informace |
|:--- |:--- |:--- |
| ![Ikona upozornění](./media/storsimple-hardware-component-replacement/Warning.png) |**NEBEZPEČÍ!** |Označuje nebezpečné situace, která, pokud není vyhnout, bude výsledkem úmrtí nebo vážným. Toto slovo signálu je omezená na v nejextrémnějších případech. |
| ![Ikona upozornění](./media/storsimple-hardware-component-replacement/Warning.png) |**UPOZORNĚNÍ!** |Označuje nebezpečné situace, která, pokud není vyhnout, může způsobit úmrtí nebo vážným. |
| ![Ikona upozornění](./media/storsimple-hardware-component-replacement/Caution.png) |**UPOZORNĚNÍ!** |Označuje nebezpečné situace, která, pokud není vyhnout, by mohlo způsobit vedlejší nebo střední škody. |
| ![Všimněte si, že ikona](./media/storsimple-hardware-component-replacement/NoticeIcon.png) |**VŠIMNĚTE SI, ŽE:** |Označuje informace, které jsou považovány za důležité, ale ne související nebezpečí. |
| ![Ikona k elektrické otřást](./media/storsimple-hardware-component-replacement/Electric.png) |**Nebezpečí k elektrické Otřást** |Označuje napětí vysoké. |
| ![Tučná tloušťka ikonu](./media/storsimple-hardware-component-replacement/Weight.png) |**Tučná tloušťka** | |
| ![Žádná možnost změny části ikona uživatele](./media/storsimple-hardware-component-replacement/NoUserServiceableParts.png) |**Žádné části možnost změny uživatele** |K přístup, pokud správně školení. |
| ![Ikona pokyny pro čtení](./media/storsimple-hardware-component-replacement/ReadInstructions.png) |**Nejprve přečíst všechny pokyny** | |
| ![Nebezpečí ikona tipu](./media/storsimple-hardware-component-replacement/TipHazard.png) |**Tip ohrožení** | |

### <a name="before-you-begin"></a>Než začnete
Seznamte se s informacemi o zařízení a bezpečný přístup z více ikony v tomto kurzu používá bezpečnosti. Přejděte na [bezpečně instalaci a provozování zařízení StorSimple](storsimple-safety.md) podrobnější informace. Nezapomeňte si přečíst [bezpečnostní opatření](storsimple-safety.md#handling-precautions) před zpracovat zařízení StorSimple.

Před pokusem o nahrazení komponenty, zvažte následující informace.

![Ikona upozornění](./media/storsimple-hardware-component-replacement/Warning.png) ![ikonu k elektrické Otřást](./media/storsimple-hardware-component-replacement/Electric.png) **upozornění!**

* Světlá sami správně pomocí Elektrostatický výboj nebo antistatická mat při zpracování moduly a komponenty zařízení StorSimple.
* Žádné zapojení nic neděláte. Pomocí zadaného popisovače a průvodců při zpracování součásti, které může zpřístupnit zapojení.

![Ikona upozornění](./media/storsimple-hardware-component-replacement/Warning.png) ![Všimněte si ikony](./media/storsimple-hardware-component-replacement/NoticeIcon.png) **oznámení:**

Po nahrazení modulu, **nikdy neopustí prázdná pozice vzadu přílohu**. Před odebráním části problém získáte modul nahrazení nebo prázdné.

## <a name="hardware-component-replacement-procedures"></a>Hardwarové komponenty náhradní postupy
Vaše zařízení řady StorSimple 8000 se skládá z několika moduly plug-in v primární a/nebo EBOD skříně. 8100 má jeden primární skříň, vzhledem k tomu, 8600 je duální skříň zařízení s primární skříň a skříně EBOD.

Hlavní hardwarové komponenty v zařízení jsou shrnuté v následujících tabulkách. Klikněte na odkaz v **postup nahrazení** sloupec, který chcete přejít na související kurz.

| Komponenty | # K dispozici | Modul plug-in? | Postup při nahrazení |
|:--- |:--- |:--- |:--- |
| Chassis |1 |Ne |[Výměna skříně zařízení StorSimple](storsimple-8000-chassis-replacement.md) |
| Primární řadiče |2 |Ano |[Nahraďte modul řadiče na zařízení StorSimple](storsimple-8000-controller-replacement.md) |
| 764W napájení, chlazení moduly (PCMs) |2 |Ano |[Výměna napájecího a chladicího modulu na zařízení StorSimple](storsimple-8000-power-cooling-module-replacement.md) |
| Záložní baterie |2 |Ano |[Výměna modulu záložní baterie na zařízení StorSimple](storsimple-8000-battery-replacement.md) |
| Diskové jednotky |12 |Ano |[Výměna diskové jednotky na zařízení StorSimple](storsimple-8000-disk-drive-replacement.md) |

**Tabulka 1** hardwarové komponenty v primární skříň

Primární skříň a skříně EBOD se liší v jejich vstupně-výstupní moduly. Kromě toho PCMs mají různé příkon. PCMs ve primárního skříni jsou 764 W, zatímco ty ve skříni EBOD 580 W. PCMs ve primárního skříni taky obsahovat záložní baterie modulu.

| Komponenty | # K dispozici | Modul plug-in? | Postup při nahrazení |
|:--- |:--- |:--- |:--- |
| Chassis |1 |Ne |[Výměna skříně zařízení StorSimple](storsimple-8000-chassis-replacement.md) |
| Řadiče EBOD |2 |Ano |[Výměna kontroleru EBOD, zařízení StorSimple](storsimple-8000-ebod-controller-replacement.md) |
| 580W napájení, chlazení moduly (PCMs) |2 |Ano |[Výměna napájecího a chladicího modulu na zařízení StorSimple](storsimple-8000-power-cooling-module-replacement.md) |
| Diskové jednotky |12 |Ano |[Výměna diskové jednotky na zařízení StorSimple](storsimple-8000-disk-drive-replacement.md) |

**Tabulka 2** hardwarové komponenty ve skříni EBOD

Moduly plug-in na zařízení jsou vyznačené na následující přední a zadní diagramů. Tyto diagramy můžete použít k určení umístění různých modulů plug-in, zda můžou nahradit aktuální soubor je povinný. Front-diagram znázorňuje diskové jednotky a zadní diagramy EBOD skříň a zobrazit primární skříň, moduly plug-in.

![Přední panel zařízení s diskové jednotky](./media/storsimple-hardware-component-replacement/IC741028.png)

**Obrázek 1** front-zařízení

| Štítek | Popis |
|:--- |:--- |
| 0 - 11 |Diskové jednotky (celkem 12) |

Primární skříň a skříně EBOD mají moduly dopravce jednotky. Skříň jsou uloženy dvanáct 3,5" diskové jednotky uspořádané do formátu 3 ve 4.

![Propojovací rozhraní modulů primární skříň zařízení](./media/storsimple-hardware-component-replacement/IC740994.png)

**Obrázek 2** zadní primární skříň

| Štítek | Popis |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |Řadič 0 |
| 4 |Řadič 1 |

![Základní moduly plug-in skříň zařízení EBOD](./media/storsimple-hardware-component-replacement/IC769599.png)

**Obrázek 3** zadní skříň EBOD

| Štítek | Popis |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |Kontroleru EBOD 0 |
| 4 |Kontroleru EBOD 1 |

## <a name="field-replaceable-units"></a>Pole replaceable jednotky
Následující jednotky pole replaceable (FRU) jsou k dispozici pro zařízení StorSimple:

* Skříň (včetně panelu integrované operace)
* 764 W AC PCM
* 580 W AC PCM
* Pevný disk s modulem dopravce jednotky
* Modul řadiče
* Modul řadiče EBOD
* Modul záložní baterie
* Stojan připojení kit lišty

Prosím [obraťte se na Microsoft Support](storsimple-8000-contact-microsoft-support.md) řazení některý z těchto jednotek nahrazení.

## <a name="next-steps"></a>Další postup
Zkontrolujte všechny [bezpečnostní informace](storsimple-safety.md) před pokusem o vyměňovat hardwarové součásti StorSimple.

