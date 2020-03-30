---
title: Výměna řadiče StorSimple 8600 EBOD | Dokumenty společnosti Microsoft
description: Vysvětluje, jak odebrat a nahradit jeden nebo oba řadiče EBOD na zařízení StorSimple 8600.
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
ms.openlocfilehash: b05d1f36d1e74b3d915e216676859654fbcbacf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254882"
---
# <a name="replace-an-ebod-controller-on-your-storsimple-device"></a>Výměna ovladače EBOD na zařízení StorSimple

## <a name="overview"></a>Přehled
Tento kurz vysvětluje, jak nahradit vadný modul řadiče EBOD na zařízení Microsoft Azure StorSimple. Chcete-li nahradit modul řadiče EBOD, musíte:

* Vyjměte vadný řadič EBOD
* Instalace nového řadiče EBOD

Než začnete, zvažte následující informace:

* Do všech nepoužívaných slotů musí být vloženy prázdné moduly EBOD. Pokud je slot ponechán otevřený, kryt se pořádně neochladí.
* Regulátor EBOD je vyměnitelný za provozu a lze jej vyjmout nebo vyměnit. Neodstraňujte vadný modul, dokud nebudete mít náhradní. Při zahájení procesu výměny je nutné jej dokončit do 10 minut.

> [!IMPORTANT]
> Než se pokusíte odebrat nebo vyměnit jakoukoli součást StorSimple, zkontrolujte [konvence bezpečnostních ikon](storsimple-safety.md#safety-icon-conventions) a další bezpečnostní [opatření](storsimple-safety.md).

## <a name="remove-an-ebod-controller"></a>Odebrání řadiče EBOD
Před výměnou selhání modulu řadiče EBOD v zařízení StorSimple se ujistěte, že druhý modul řadiče EBOD je aktivní a spuštěný. Následující postup a tabulka vysvětlují, jak odebrat modul řadiče EBOD.

#### <a name="to-remove-an-ebod-module"></a>Odebrání modulu EBOD
1. Otevřete web Azure Portal.
2. Přejděte do zařízení a přejděte na **nastavení** > **Stav hardwaru**a ověřte, zda je stav LED diody pro aktivní modul řadiče EBOD zelený a led dioda pro vadný modul řadiče EBOD je červená.
3. Vyhledejte vadný modul řadiče EBOD na zadní straně zařízení.
4. Před vyjmutím modulu EBOD ze systému odstraňte kabely, které připojují modul regulátoru EBOD k regulátoru.
5. Poznamenejte si přesný port SAS modulu řadiče EBOD, který byl připojen k řadiči. Po výměně modulu EBOD budete muset systém obnovit do této konfigurace.
   
   > [!NOTE]
   > Obvykle se jedná o port A, který je v následujícím diagramu označen jako **hostitel.**
   
    ![Zadní rovina regulátoru EBOD](./media/storsimple-ebod-controller-replacement/IC741049.png)
   
     **Obrázek 1** Zadní část modulu EBOD
   
   | Popisek | Popis |
   |:--- |:--- |
   | 1 |Led dioda poruchy |
   | 2 |Led dioda napájení |
   | 3 |Konektory SAS |
   | 4 |SAS LED diody |
   | 5 |Sériové porty pouze pro použití z výroby |
   | 6 |Port A (host v) |
   | 7 |Port B (hostitel ven) |
   | 8 |Port C (pouze použití z výroby) |

## <a name="install-a-new-ebod-controller"></a>Instalace nového řadiče EBOD
Následující postup a tabulka vysvětlují, jak nainstalovat modul řadiče EBOD do zařízení StorSimple.

#### <a name="to-install-an-ebod-controller"></a>Instalace řadiče EBOD
1. Zkontrolujte, zda zařízení EBOD není poškozeno, zejména na konektoru rozhraní. Neinstalujte nový regulátor EBOD, pokud jsou některé kolíky ohnuté.
2. Se západky v otevřené poloze zasuňte modul do krytu, dokud se západky nezachytí.
   
    ![Instalace řadiče EBOD](./media/storsimple-ebod-controller-replacement/IC741050.png)
   
    **Obrázek 2**  Instalace modulu řadiče EBOD
3. Zavřete západku. Měli byste slyšet cvaknutí, jak západka zapadne.
   
    ![Uvolnění zámku EBOD](./media/storsimple-ebod-controller-replacement/IC741047.png)
   
    **Obrázek 3**  Zavření západky modulu EBOD
4. Znovu připojte kabely. Použijte přesnou konfiguraci, která byla přítomna před nahrazením. Podrobnosti o připojení kabelů naleznete v následujícím diagramu a tabulce.
   
    ![Kabel zařízení 4U pro napájení](./media/storsimple-ebod-controller-replacement/IC770723.png)
   
    **Obrázek 4**. Opětovné připojení kabelů
   
   | Popisek | Popis |
   |:--- |:--- |
   | 1 |Primární skříň |
   | 2 |PCM 0 |
   | 3 |PCM 1 |
   | 4 |Kontroler 0 |
   | 5 |Kontroler 1 |
   | 6 |Řadič EBOD 0 |
   | 7 |Řadič EBOD 1 |
   | 8 |Skříň EBOD |
   | 9 |Napájecí rozvody |

## <a name="next-steps"></a>Další kroky
Další informace o [výměně hardwarových komponent StorSimple](storsimple-8000-hardware-component-replacement.md).

