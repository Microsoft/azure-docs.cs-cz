---
title: Výměna baterie na zařízení řady Microsoft Azure StorSimple 8000
description: Popisuje, jak odebrat, vyměnit a udržovat záložní bateriový modul na zařízení StorSimple.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: f21bbf4777aa74e84ffb8c1af903f90608d5551f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255012"
---
# <a name="replace-the-backup-battery-module-on-your-storsimple-device"></a>Výměna modulu záložní baterie na zařízení StorSimple

## <a name="overview"></a>Přehled
Primární skříň Power and Cooling Module (PCM) na vašem zařízení Microsoft Azure StorSimple má další sadu baterií. Tato sada poskytuje napájení tak, aby zařízení StorSimple mohlo ukládat data, pokud dojde ke ztrátě střídavého proudu do primární skříně. Tato sada baterií se označuje jako *záložní bateriový modul*. Modul záložní baterie existuje pouze pro primární skříň ve vašem zařízení StorSimple (skříň EBOD neobsahuje záložní bateriový modul).

Tento kurz vysvětluje následující postupy:

* Vyjmutí záložního bateriového modulu
* Instalace nového záložního bateriového modulu
* Udržovat záložní bateriový modul

> [!IMPORTANT]
> Před vyjmutím a výměnou záložního bateriového modulu si přečtěte bezpečnostní informace v [části Úvod do výměny hardwarové součásti StorSimple](storsimple-8000-hardware-component-replacement.md).


## <a name="remove-the-backup-battery-module"></a>Vyjmutí záložního bateriového modulu
Záložní bateriový modul pro vaše zařízení StorSimple je jednotka vyměnitelná v terénu. Před instalací do PCM by měl být bateriový modul uložen v původním obalu. Chcete-li záložní baterii vyjmout, proveďte následující kroky.

#### <a name="to-remove-the-backup-battery-module"></a>Odebrání záložního modulu baterie
1. Na webu Azure Portal přejděte do okna služby Správce zařízení StorSimple. Přejděte do **části Zařízení** a vyberte zařízení ze seznamu zařízení. Přejděte na **monitor** > **stavu hardwaru**. V části **Sdílené součásti**se podívejte na stav baterie.
2. Identifikujte pcm, ve kterém selhala baterie. Obrázek 1 znázorňuje zadní část zařízení StorSimple.
   
    ![Backplane primárních modulů skříně zařízení](./media/storsimple-battery-replacement/IC740994.png)
   
    **Obrázek 1** Zadní část primárního zařízení s PCM a řídicími moduly
   
   | Popisek | Popis |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Kontroler 0 |
   | 4 |Kontroler 1 |
   
    Jak ukazuje číslo 3 na obrázku 2, indikátor LED na PCM 0, který odpovídá **poruše baterie,** by měl být rozsvícen.
   
    ![Zadní rovina zařízení PCM indikátor indikátor led diody](./media/storsimple-battery-replacement/IC740992.png)
   
    **Obrázek 2** Zadní část PCM zobrazující indikátor led diody monitorování
   
   | Popisek | Popis |
   |:--- |:--- |
   | 1 |Výpadku napájení střídavého proudu |
   | 2 |Selhání ventilátoru |
   | 3 |Porucha baterie |
   | 4 |PCM je v pořádku |
   | 5 |Selhání napájení stejnosměrného proudu |
   | 6 |Baterie zdravá |
3. Chcete-li odebrat počítač PCM s neúspěšnou baterií, postupujte takto v části [Odebrání počítače PCM](storsimple-8000-power-cooling-module-replacement.md#remove-a-pcm).
4. Po vyjmutí pcm zvedněte a otočte rukojeť bateriového modulu směrem nahoru, jak je uvedeno na následujícím obrázku, a vytáhněte jej nahoru, abyste baterii vyjmuli.
   
    ![Vyjmutí baterie z PCM](./media/storsimple-battery-replacement/IC741019.png)
   
    **Obrázek 3** Vyjmutí baterie z PCM
5. Umístěte modul do obalu jednotky vyměnitelného polem.
6. Vrátit vadný celek společnosti Microsoft pro správnou údržbu a manipulaci.

## <a name="install-a-new-backup-battery-module"></a>Instalace nového záložního bateriového modulu
Provedením následujících kroků nainstalujte náhradní bateriový modul do počítače PCM v primární skříni zařízení StorSimple.

#### <a name="to-install-the-battery-module"></a>Instalace bateriového modulu
1. Umístěte záložní bateriový modul do správné orientace v PCM.
2. Zatlačte na rukojeť bateriového modulu úplně, abyste usadit konektor.
3. Vyměňte PCM v primární skříni podle pokynů v [části Výměna napájecího a chladicího modulu na zařízení StorSimple](storsimple-8000-power-cooling-module-replacement.md).
4. Po dokončení výměny přejděte do zařízení a přejděte na **monitor** > **u stavu hardwaru** na webu Azure Portal. Ověřte stav baterie a ujistěte se, že instalace byla úspěšná. Zelený stav označuje, že baterie je v pořádku.

## <a name="maintain-the-backup-battery-module"></a>Udržovat záložní bateriový modul
V zařízení StorSimple poskytuje záložní bateriový modul napájení řadiče během události výpadku napájení. Umožňuje zařízení StorSimple ukládat důležitá data před vypnutím řízeným způsobem. Se dvěma plně nabitými bateriemi v PCM může systém zvládnout dvě po sobě jdoucí ztrátové události.

Na webu Azure Portal **stav hardwaru** pod okno **monitor** označuje, zda baterie nefunguje nebo se blíží konec životnosti. Stav baterie je indikován **baterií v PCM 0** nebo baterií v **PCM 1 v** části Sdílené **součásti**. Tento nůž zobrazí **degradovaný** stav pro blížící se konec životnosti a **FAILED** pro dosažení konce životnosti.

> [!NOTE]
> Baterie může hlásit **selhání,** když je prostě potřeba nabít.


Pokud se zobrazí stav **DEGRADOVANÝ,** doporučujeme následující postup:

* Systém mohl v poslední době dojít ke ztrátě napájení nebo baterie mohou být podstupují pravidelnou údržbu. Než budete pokračovat, dodržujte systém 12 hodin.
  
  * Pokud je stav stále **degradován** po 12 hodinách nepřetržitého připojení ke střídavému napájení s běžícími regulátory a PCM, je třeba baterii vyměnit. Požádejte o náhradní záložní bateriový modul [od podpory společnosti Microsoft.](storsimple-8000-contact-microsoft-support.md)
  * Pokud se stav po 12 hodinách stane v pořádku, baterie je v provozu a potřebuje pouze nabití údržby.
* Pokud nedošlo k související ztrátě střídavého proudu a pcm je zapnuto a připojeno ke střídavé síti, je třeba vyměnit baterii. [Chcete-li](storsimple-8000-contact-microsoft-support.md) objednat náhradní záložní bateriový modul, obraťte se na podporu společnosti Microsoft.

> [!IMPORTANT]
> Likvidujte neúspěšnou baterii v souladu s národními a regionálními předpisy.

## <a name="next-steps"></a>Další kroky
Další informace o [výměně hardwarových komponent StorSimple](storsimple-8000-hardware-component-replacement.md).

