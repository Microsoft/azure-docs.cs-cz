---
title: Výměna součást hardwaru řady StorSimple 8000 | Microsoft Docs
description: Popisuje, jak bezpečně PCMs, baterie, moduly řadiče, EBOD řadiče, diskové jednotky a nahradíte skříň zařízení StorSimple.
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
ms.openlocfilehash: 6de50c5031db59176bdf17ecc69b934559220f6a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23927604"
---
# <a name="replace-a-hardware-component-on-your-storsimple-8000-series-device"></a>Nahraďte hardwarová komponenta na vašem zařízení řady StorSimple 8000

## <a name="overview"></a>Přehled
Kurzy hardwarové součásti nahrazení popisují hardwarové součásti zařízení řady Microsoft Azure StorSimple 8000 a kroky potřebné k odebrání a nahradíte je. Tento článek popisuje ikony zabezpečení, obsahuje odkazy na podrobné kurzy a uvádí součásti, které jsou nahraditelné.

> [!IMPORTANT]
> Před pokusem o odeberte nebo nahraďte všechny součásti, StorSimple, ujistěte se, abyste si prošli [zabezpečení ikonu konvence](#safety-icon-conventions) a dalších [bezpečnostní opatření](storsimple-safety.md).


### <a name="safety-icon-conventions"></a>Konvence ikona zabezpečení
Následující tabulka popisuje zabezpečení ikony, které používá v těchto kurzech. Zaměřte na tyto ikony zabezpečení při procházení postup odeberete a nahradíte komponenty zařízení.

| Ikona | Text | Další informace |
|:--- |:--- |:--- |
| ![Ikona upozornění](./media/storsimple-hardware-component-replacement/Warning.png) |**NEBEZPEČÍ!** |Označuje nebezpečné situace, která, pokud není vyhnout, bude výsledkem smrt nebo vážné škody. Signál slovo je omezený na nejvíce extrémních situacích. |
| ![Ikona upozornění](./media/storsimple-hardware-component-replacement/Warning.png) |**UPOZORNĚNÍ!** |Označuje nebezpečné situace, která, pokud není vyhnout, může mít za následek smrt nebo vážné škody. |
| ![Ikona upozornění](./media/storsimple-hardware-component-replacement/Caution.png) |**UPOZORNĚNÍ!** |Označuje nebezpečné situace, která, pokud není vyhnout, může mít za následek menší nebo střední škody. |
| ![Ikona upozornění](./media/storsimple-hardware-component-replacement/NoticeIcon.png) |**UPOZORNĚNÍ:** |Označuje informace, které jsou považovány za důležité, ale není nebezpečí související. |
| ![Ikona elektrický rázu](./media/storsimple-hardware-component-replacement/Electric.png) |**Elektrický rázu nebezpečí** |Označuje napětí vysoké. |
| ![Ikona velkou váhy](./media/storsimple-hardware-component-replacement/Weight.png) |**Velkou váhy** | |
| ![Žádná obsluhovatelná částí ikona uživatele](./media/storsimple-hardware-component-replacement/NoUserServiceableParts.png) |**Žádná Obsluhovatelná části uživatele** |K přístup, pokud správně cvičení. |
| ![Ikona pokyny pro čtení](./media/storsimple-hardware-component-replacement/ReadInstructions.png) |**Číst všechny pokyny nejprve** | |
| ![Nebezpečí ikona tipu](./media/storsimple-hardware-component-replacement/TipHazard.png) |**Tip nebezpečí** | |

### <a name="before-you-begin"></a>Než začnete
Seznamte se s bezpečnostní informace o vašem zařízení a bezpečnost ikony používané v tomto kurzu. Přejděte na [bezpečně instalaci a provoz zařízení StorSimple](storsimple-safety.md) úplné informace. Nezapomeňte si přečíst [bezpečnostní opatření](storsimple-safety.md#handling-precautions) před zpracovat zařízení StorSimple.

Před pokusem o nahrazení komponenty, vezměte v úvahu následující informace.

![Ikona upozornění](./media/storsimple-hardware-component-replacement/Warning.png) ![elektrický rázu ikonu](./media/storsimple-hardware-component-replacement/Electric.png) **upozornění!**

* Pozadí sami správně pomocí elektrostatické vyřízení nebo antistatická mat při zpracování moduly a součástí zařízení StorSimple.
* Není touch žádné zapojení. Při zpracování součásti, které mohou být zpřístupněny zapojení používejte zadaný obslužné rutiny a příručky.

![Ikona upozornění](./media/storsimple-hardware-component-replacement/Warning.png) ![Všimněte si, ikona](./media/storsimple-hardware-component-replacement/NoticeIcon.png) **oznámení:**

Když nahradíte modul, **nikdy neopustí prázdný bay vzadu skříni**. Před odebráním část problém získejte náhrada nebo prázdné modulu.

## <a name="hardware-component-replacement-procedures"></a>Hardwarové součásti náhradní postupy
Vaše zařízení řady StorSimple 8000 se skládá z několika moduly plug-in do primární nebo EBOD skříně. 8100 má jeden primární skříň, kdežto 8600 zařízení duální skříň s primární skříně a EBOD skříň.

Hlavní hardwarové součásti v zařízení jsou shrnuté v následujících tabulkách. Klikněte na odkaz **postup nahrazení** sloupec přejít na související kurzu.

| Komponenty | # Přítomen | Modul plug-in? | Postup při nahrazení |
|:--- |:--- |:--- |:--- |
| Skříň |1 |Ne |[Nahraďte skříň zařízení StorSimple](storsimple-8000-chassis-replacement.md) |
| Primární řadiče |2 |Ano |[Nahraďte modul řadiče zařízení StorSimple](storsimple-8000-controller-replacement.md) |
| 764W napájení a chlazení moduly (PCMs) |2 |Ano |[Nahrazení energii a chlazení modulu zařízení StorSimple](storsimple-8000-power-cooling-module-replacement.md) |
| Zálohování baterie |2 |Ano |[Nahraďte modul zálohování baterie zařízení StorSimple](storsimple-8000-battery-replacement.md) |
| Diskové jednotky |12 |Ano |[Místo disku v zařízení StorSimple](storsimple-8000-disk-drive-replacement.md) |

**Tabulka 1** hardwarové součásti v primární skříň

Primární skříně a skříň EBOD se liší v jejich vstupně-výstupní moduly. Navíc PCMs mají různé příkon. PCMs ve skříni primární jsou 764 W, zatímco těmi ve skříni EBOD 580 dokončeno PCMs ve skříni primární taky obsahovat záložní baterie modulu.

| Komponenty | # Přítomen | Modul plug-in? | Postup při nahrazení |
|:--- |:--- |:--- |:--- |
| Skříň |1 |Ne |[Nahraďte skříň zařízení StorSimple](storsimple-8000-chassis-replacement.md) |
| EBOD řadiče |2 |Ano |[Nahraďte řadič EBOD zařízení StorSimple](storsimple-8000-ebod-controller-replacement.md) |
| 580W napájení a chlazení moduly (PCMs) |2 |Ano |[Nahrazení energii a chlazení modulu zařízení StorSimple](storsimple-8000-power-cooling-module-replacement.md) |
| Diskové jednotky |12 |Ano |[Místo disku v zařízení StorSimple](storsimple-8000-disk-drive-replacement.md) |

**Tabulka 2** hardwarové součásti v EBOD skříň

Moduly plug-in na zařízení jsou vyznačené na následující přední a zadní diagramů. Tyto diagramy můžete použít k určení umístění různých modulů plug-in, pokud je potřeba nahrazení. Front diagram znázorňuje diskové jednotky a zadní diagramy skříni EBOD a zobrazit primární skříň, moduly plug-in.

![Frontplane zařízení s diskové jednotky](./media/storsimple-hardware-component-replacement/IC741028.png)

**Obrázek 1** Front zařízení

| Štítek | Popis |
|:--- |:--- |
| 0 - 11 |Diskové jednotky (celkem 12) |

Primární skříň i skříni EBOD mají moduly, poskytovatel jednotky. Skříň ve uložený dvanáct 3,5" diskové jednotky uspořádané ve formátu 3 ve 4.

![Propojovací rozhraní systému modulů skříň primární zařízení](./media/storsimple-hardware-component-replacement/IC740994.png)

**Obrázek 2** zadní primární skříň

| Štítek | Popis |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |Řadič 0 |
| 4 |Řadič 1 |

![Propojovací rozhraní systému zařízení EBOD skříň moduly plug-in](./media/storsimple-hardware-component-replacement/IC769599.png)

**Obrázek 3** zadní EBOD skříň

| Štítek | Popis |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |EBOD řadič 0 |
| 4 |EBOD řadiči 1 |

## <a name="field-replaceable-units"></a>Nahraditelné jednotky pole
Následující jednotky pole replaceable (FRU) jsou k dispozici pro zařízení StorSimple:

* Skříň (včetně panelu integrované operace)
* 764 W AC PCM
* 580 W AC PCM
* Pevný disk s modulem poskytovatel jednotky
* Modul řadiče
* Modul EBOD řadiče
* Modul zálohování baterie
* Rack připojení liště kit

Prosím [kontaktovat Microsoft Support](storsimple-8000-contact-microsoft-support.md) pořadí některé z těchto jednotek nahrazení.

## <a name="next-steps"></a>Další kroky
Zkontrolujte všechny [bezpečnostní informace](storsimple-safety.md) před dalším pokusem o výměně hardwarové součásti StorSimple.

