---
title: Výměna hardwarových komponent řady StorSimple 8000 | Dokumenty společnosti Microsoft
description: Popisuje, jak bezpečně nahradit PCM, baterie, moduly řadiče, řadiče EBOD, diskové jednotky a šasi zařízení StorSimple.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60321677"
---
# <a name="replace-a-hardware-component-on-your-storsimple-8000-series-device"></a>Výměna hardwarové součásti zařízení řady StorSimple 8000

## <a name="overview"></a>Přehled
Kurzy na výměnu hardwarové součásti popisují hardwarové součásti zařízení řady Microsoft Azure StorSimple 8000 a kroky potřebné k jejich odebrání a nahrazení. Tento článek popisuje ikony zabezpečení, poskytuje odkazy na podrobné kurzy a uvádí součásti, které jsou nahraditelné.

> [!IMPORTANT]
> Než se pokusíte odebrat nebo vyměnit jakoukoli součást StorSimple, zkontrolujte [konvence bezpečnostních ikon](#safety-icon-conventions) a další bezpečnostní [opatření](storsimple-safety.md).


### <a name="safety-icon-conventions"></a>Konvence bezpečnostních ikon
Následující tabulka popisuje ikony zabezpečení použité v těchto kurzech. Při procházení kroků k odebrání a výměně součástí zařízení věnujte pozornost těmto bezpečnostním ikonám.

| Ikona | Text | Další informace |
|:--- |:--- |:--- |
| ![Ikona upozornění](./media/storsimple-hardware-component-replacement/Warning.png) |**Nebezpečí!** |Označuje nebezpečnou situaci, která, pokud se jí nevyhnete, bude mít za následek smrt nebo vážné zranění. Toto signální slovo je omezeno na nejextrémnější situace. |
| ![Ikona upozornění](./media/storsimple-hardware-component-replacement/Warning.png) |**Upozornění!** |Označuje nebezpečnou situaci, která, pokud se jí nevyhnete, může mít za následek smrt nebo vážné zranění. |
| ![Ikona upozornění](./media/storsimple-hardware-component-replacement/Caution.png) |**Pozor!** |Označuje nebezpečnou situaci, která, pokud se jí nevyhnete, může vést k menšímu nebo střednímu zranění. |
| ![Ikona Oznámení](./media/storsimple-hardware-component-replacement/NoticeIcon.png) |**Oznámení:** |Označuje informace považované za důležité, ale ne související s nebezpečím. |
| ![Ikona elektrického šoku](./media/storsimple-hardware-component-replacement/Electric.png) |**Nebezpečí úrazu elektrickým proudem** |Označuje vysoké napětí. |
| ![Ikona aplikace Heavy Weight](./media/storsimple-hardware-component-replacement/Weight.png) |**Těžká váha** | |
| ![Ikona žádné houževnaté díly uživatelem](./media/storsimple-hardware-component-replacement/NoUserServiceableParts.png) |**Žádné uživatelem opravitelné díly** |Nevstupte do funkce, pokud není řádně vyškolen. |
| ![Ikona Číst pokyny](./media/storsimple-hardware-component-replacement/ReadInstructions.png) |**Nejprve si přečtěte všechny pokyny** | |
| ![Ikona nebezpečí špičky](./media/storsimple-hardware-component-replacement/TipHazard.png) |**Nebezpečí špičky** | |

### <a name="before-you-begin"></a>Než začnete
Seznamte se s bezpečnostními informacemi o vašem zařízení a bezpečnostními ikonami použitými v tomto kurzu. Přejděte na [bezpečně nainstalovat a provozovat zařízení StorSimple](storsimple-safety.md) pro úplné informace. Než se s přístrojem StorSimple vypořádáte, zkontrolujte [bezpečnostní opatření.](storsimple-safety.md#handling-precautions)

Než se pokusíte nahradit součást, zvažte následující informace.

![Výstražná](./media/storsimple-hardware-component-replacement/Warning.png) ![ikona Ikona](./media/storsimple-hardware-component-replacement/Electric.png) elektrického šoku **VAROVÁNÍ!**

* Při manipulaci s moduly a součástmi zařízení StorSimple se řádně umisťte pomocí elektrostatického výboje nebo antistatické rohože.
* Nedotýkejte se žádného obvodu. Při manipulaci s součástmi, které mohou mít exponované obvody, používejte dodané rukojeti a vodítka.

![Upozornění](./media/storsimple-hardware-component-replacement/Warning.png) ![na](./media/storsimple-hardware-component-replacement/NoticeIcon.png) ikonu výstražné **ikony:**

Při výměně modulu **nikdy nenechávejte prázdnou pozici v zadní části krytu**. Před odebráním problémové části získejte náhradní nebo prázdný modul.

## <a name="hardware-component-replacement-procedures"></a>Postupy výměny hardwarových součástí
Zařízení řady StorSimple 8000 se skládá z několika zásuvných modulů v primárních skříních nebo skříních EBOD. 8100 má jednu primární skříň, zatímco 8600 je zařízení s duální skříní s primární skříní a skříní EBOD.

Hlavní hardwarové součásti v zařízení jsou shrnuty v následujících tabulkách. Kliknutím na odkaz ve sloupci **Náhradní procedura** přejdete do přidruženého kurzu.

| Komponenty | # Současnost | Zásuvný modul? | Postup výměny |
|:--- |:--- |:--- |:--- |
| Skříň |1 |Ne |[Výměna šasi na zařízení StorSimple](storsimple-8000-chassis-replacement.md) |
| Primární řadiče |2 |Ano |[Výměna modulu ovladače na zařízení StorSimple](storsimple-8000-controller-replacement.md) |
| 764W napájecí a chladicí moduly (PCM) |2 |Ano |[Výměna napájecího a chladicího modulu na zařízení StorSimple](storsimple-8000-power-cooling-module-replacement.md) |
| Záložní baterie |2 |Ano |[Výměna modulu záložní baterie na zařízení StorSimple](storsimple-8000-battery-replacement.md) |
| Diskové jednotky |12 |Ano |[Výměna diskové jednotky v zařízení StorSimple](storsimple-8000-disk-drive-replacement.md) |

**Tabulka 1** Hardwarové komponenty v primární skříni

Primární skříň a skříň EBOD se liší v jejich V/O moduly. Kromě toho mají PCM různé příkony. PCM v primární skříni jsou 764 W, zatímco ve skříni EBOD jsou 580 W. PCM v primární skříni také obsahují záložní bateriový modul.

| Komponenty | # Současnost | Zásuvný modul? | Postup výměny |
|:--- |:--- |:--- |:--- |
| Skříň |1 |Ne |[Výměna šasi na zařízení StorSimple](storsimple-8000-chassis-replacement.md) |
| Řadiče EBOD |2 |Ano |[Výměna ovladače EBOD na zařízení StorSimple](storsimple-8000-ebod-controller-replacement.md) |
| 580W napájecí a chladicí moduly (PCM) |2 |Ano |[Výměna napájecího a chladicího modulu na zařízení StorSimple](storsimple-8000-power-cooling-module-replacement.md) |
| Diskové jednotky |12 |Ano |[Výměna diskové jednotky v zařízení StorSimple](storsimple-8000-disk-drive-replacement.md) |

**Tabulka 2** Hardwarové komponenty ve skříni EBOD

Zásuvné moduly na zařízení jsou zvýrazněny v následujících předních a zadních schématech. Tyto diagramy můžete použít k určení umístění různých modulů plug-in, pokud je požadováno nahrazení. Přední diagram znázorňuje diskové jednotky a zadní diagramy skříně EBOD a primární skříň zobrazují zásuvné moduly.

![Přední panel zařízení s diskovými jednotkami](./media/storsimple-hardware-component-replacement/IC741028.png)

**Obrázek 1** Přední strana zařízení

| Popisek | Popis |
|:--- |:--- |
| 0 - 11 |Diskové jednotky (celkem 12) |

Primární skříň i skříň EBOD mají moduly nosiče pohonu. Podvozek obsahuje dvanáct 3,5" diskových jednotek uspořádaných ve formátu 3: 4.

![Zadní rovina primárních modulů skříně zařízení](./media/storsimple-hardware-component-replacement/IC740994.png)

**Obrázek 2** Zadní část primárního krytu

| Popisek | Popis |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |Kontroler 0 |
| 4 |Kontroler 1 |

![Backplane zásuvných modulů skříně EBOD](./media/storsimple-hardware-component-replacement/IC769599.png)

**Obrázek 3** Zadní část krytu EBOD

| Popisek | Popis |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |Řadič EBOD 0 |
| 4 |Řadič EBOD 1 |

## <a name="field-replaceable-units"></a>Vyměnitelné jednotky pole
Pro vaše zařízení StorSimple jsou k dispozici následující vyměnitelné jednotky polí:

* Podvozek (včetně integrovaného provozního panelu)
* 764 W AC PCM
* 580 W AC PCM
* Pevný disk s modulem nosiče disků
* Modul regulátoru
* Modul regulátoru EBOD
* Záložní bateriový modul
* Sada montážní chodníček racku

Chcete-li objednat některou z těchto náhradních jednotek, obraťte se na [podporu společnosti Microsoft.](storsimple-8000-contact-microsoft-support.md)

## <a name="next-steps"></a>Další kroky
Před pokusem o výměnu hardwarové součásti StorSimple zkontrolujte všechny [bezpečnostní informace.](storsimple-safety.md)

