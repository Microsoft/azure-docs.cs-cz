---
title: Výměna hardwarové součásti řady StorSimple 8000 | Microsoft Docs
description: Popisuje, jak bezpečně nahradit PCMs, baterii, moduly kontroleru, řadiče EBOD, diskové jednotky a skříň zařízení StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: 12ab5a9598cc0222f5a3e64985be2e2ea9e7e2fd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85564282"
---
# <a name="replace-a-hardware-component-on-your-storsimple-8000-series-device"></a>Výměna hardwarové součásti na zařízení řady StorSimple 8000

## <a name="overview"></a>Přehled
Kurzy k výměně hardwarových součástí popisují hardwarové součásti zařízení Microsoft Azure StorSimple 8000 Series a kroky nutné k jejich odebrání a nahrazení. Tento článek popisuje bezpečnostní ikony, poskytuje odkazy na podrobné kurzy a uvádí součásti, které lze nahradit.

> [!IMPORTANT]
> Před pokusem o odebrání nebo nahrazení jakékoli součásti StorSimple se ujistěte, že jste provedli kontrolu [bezpečnostních ikon](#safety-icon-conventions) a další [bezpečnostní opatření](storsimple-8000-safety.md).

### <a name="safety-icon-conventions"></a>Bezpečnostní konvence ikon

V následující tabulce jsou popsány bezpečnostní ikony používané v těchto kurzech. Pokud provedete kroky pro odebrání a nahrazení součástí zařízení, Věnujte jim pozor na tyto bezpečnostní ikony.

| Ikona | Text | Další informace |
|:--- |:--- |:--- |
| ![Ikona upozornění](./media/storsimple-hardware-component-replacement/Warning.png) |**BEZPEČNÁ!** |Označuje nebezpečnou situaci, která, pokud se nevyhne, má za následek smrt nebo vážnou škodu. Toto slovo signálu je omezené na extrémní situace. |
| ![Ikona upozornění](./media/storsimple-hardware-component-replacement/Warning.png) |**Upozornění!** |Označuje nebezpečnou situaci, že pokud se nevyhnete, může dojít k úmrtí nebo závažné újmě. |
| ![Ikona upozornění](./media/storsimple-hardware-component-replacement/Caution.png) |**Upozornění!** |Označuje nebezpečnou situaci, že pokud se nevyhnete, může dojít k menší nebo střední újmě. |
| ![Ikona oznámení](./media/storsimple-hardware-component-replacement/NoticeIcon.png) |**ŠESTIMĚSÍČNÍ** |Indikuje informace považované za důležité, ale nesouvisí s rizikem. |
| ![Ikona elektrického úrazu](./media/storsimple-hardware-component-replacement/Electric.png) |**Nebezpečí elektrického úrazu** |Označuje vysoké napětí. |
| ![Ikona velké váhy](./media/storsimple-hardware-component-replacement/Weight.png) |**Silná váha** | |
| ![Ikona žádné části uživatelských služeb](./media/storsimple-hardware-component-replacement/NoUserServiceableParts.png) |**Žádné části s uživatelskými službami** |Nepoužívejte přístup, pokud není řádně vyškolená. |
| ![Ikona pro čtení pokynů](./media/storsimple-hardware-component-replacement/ReadInstructions.png) |**Nejprve si přečtěte všechny pokyny.** | |
| ![Ikona nebezpečí tipu](./media/storsimple-hardware-component-replacement/TipHazard.png) |**Nebezpečí špičky** | |

### <a name="before-you-begin"></a>Než začnete

Seznamte se s bezpečnostními informacemi o zařízení a bezpečnostních ikonách, které se používají v tomto kurzu. Pro úplné informace použijte pro [bezpečnou instalaci a provoz zařízení StorSimple](storsimple-8000-safety.md) . Nezapomeňte si projít [bezpečnostní opatření](storsimple-8000-safety.md#handling-precautions) před zpracováním zařízení StorSimple.

Před pokusem o nahrazení komponenty Vezměte v úvahu následující informace.

![Ikona upozornění ](./media/storsimple-hardware-component-replacement/Warning.png) ![ ikona elektrického úrazu – ](./media/storsimple-hardware-component-replacement/Electric.png) **Upozornění**

* Při manipulaci s moduly a komponentami zařízení StorSimple se dobře doplní pomocí elektrostatického výboje nebo nestatického materiálu.
* Nemusíte se dotknout žádného dovody. Použijte dodané popisovače a vodítka při zpracovávání komponent, které mohou mít vystavené dodávání.

![Ikona upozornění ikona oznámení ](./media/storsimple-hardware-component-replacement/Warning.png) ![ ](./media/storsimple-hardware-component-replacement/NoticeIcon.png) **:**

Když nahradíte modul, **nikdy nenechávejte prázdné místo na zadní části skříně**. Před odebráním části problému Získejte náhradní nebo prázdný modul.

## <a name="hardware-component-replacement-procedures"></a>Postup nahrazení hardwarových součástí

Zařízení řady StorSimple 8000 se skládá z několika modulů plug-in v primárních nebo EBOD skříních. 8100 má jedinou primární skříň, zatímco 8600 je zařízení se dvěma skříněmi s primární skříní a EBOD skříňkou.

Hlavní součásti hardwaru v zařízení jsou shrnuté v následujících tabulkách. Kliknutím na odkaz ve sloupci **postup nahrazení** přejdete k přidruženému kurzu.

| Komponenty | Počet přítomných | Modul plug-in? | Postup nahrazení |
|:--- |:--- |:--- |:--- |
| Skříň |1 |No |[Výměna skříně na zařízení StorSimple](storsimple-8000-chassis-replacement.md) |
| Primární řadiče |2 |Yes |[Výměna modulu Controller na zařízení StorSimple](storsimple-8000-controller-replacement.md) |
| 764W moduly napájení a chlazení (PCMs) |2 |Yes |[Výměna napájecího a chladicího modulu na zařízení StorSimple](storsimple-8000-power-cooling-module-replacement.md) |
| Záložní baterie |2 |Yes |[Výměna modulu záložní baterie na zařízení StorSimple](storsimple-8000-battery-replacement.md) |
| Diskové jednotky |12 |Yes |[Výměna diskové jednotky na zařízení StorSimple](storsimple-8000-disk-drive-replacement.md) |

**Tabulka 1** Hardwarové součásti v primární skříni

Primární skříň a skříň EBOD se ve svých vstupně-výstupních modulech liší. Kromě toho má PCMs jiný příkon. PCMs v primární skříni jsou 764 W, zatímco ta ve skříni EBOD jsou 580 W. PCMs v primární skříni obsahuje také modul záložní baterie.

| Komponenty | Počet přítomných | Modul plug-in? | Postup nahrazení |
|:--- |:--- |:--- |:--- |
| Skříň |1 |No |[Výměna skříně na zařízení StorSimple](storsimple-8000-chassis-replacement.md) |
| EBOD řadiče |2 |Yes |[Výměna EBOD kontroleru na zařízení StorSimple](storsimple-8000-ebod-controller-replacement.md) |
| 580W moduly napájení a chlazení (PCMs) |2 |Yes |[Výměna napájecího a chladicího modulu na zařízení StorSimple](storsimple-8000-power-cooling-module-replacement.md) |
| Diskové jednotky |12 |Yes |[Výměna diskové jednotky na zařízení StorSimple](storsimple-8000-disk-drive-replacement.md) |

**Tabulka 2** Hardwarové součásti v skříni EBOD

Moduly plug-in v zařízení jsou zvýrazněné v následujících front a zadních diagramech. Tyto diagramy můžete použít k určení umístění různých modulů plug-in, pokud je potřeba náhradní. Přední diagram zobrazuje diskové jednotky a zadní diagramy skříně EBOD a primární skříň zobrazuje moduly modulů plug-in.

![Přední panel zařízení s diskovou jednotkou](./media/storsimple-hardware-component-replacement/IC741028.png)

**Obrázek 1** Před zařízením

| Popisek | Description |
|:--- |:--- |
| 0 - 11 |Diskové jednotky (celkem 12) |

Primární skříň i skříň EBOD mají moduly nosných jednotek. Diskové jednotky na skříni 4 3,5 jsou uspořádány ve formátu 3 podle 4.

![Naplánování modulů primární skříně zařízení](./media/storsimple-hardware-component-replacement/IC740994.png)

**Obrázek 2** Zpět primární skříň

| Popisek | Description |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |Kontroler 0 |
| 4 |Kontroler 1 |

![Naplánování modulů plug-in EBOD ve skříni zařízení](./media/storsimple-hardware-component-replacement/IC769599.png)

**Obrázek 3** Zpátky skříň EBOD

| Popisek | Description |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |EBOD Controller 0 |
| 4 |EBOD řadič 1 |

## <a name="field-replaceable-units"></a>Nahraditelné jednotky pole

Pro vaše zařízení StorSimple jsou k dispozici následující volné jednotky (FRUs):

* Skříň (včetně panelu integrovaných operací)
* 764 W AC PCM
* 580 W AC PCM
* Pevný disk s modulem hnacího dopravce
* Modul kontroleru
* Modul EBOD Controller
* Modul záložní baterie
* Montážní skříň – držák

[Kontaktujte prosím podpora Microsoftu](storsimple-8000-contact-microsoft-support.md) pro objednání kterékoli z těchto náhradních jednotek.

## <a name="next-steps"></a>Další kroky

Před pokusem o nahrazení hardwarové komponenty StorSimple zkontrolujte všechny [bezpečnostní údaje](storsimple-8000-safety.md) .
