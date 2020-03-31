---
title: Zobrazit a spravovat úlohy StorSimple Virtual Array | Dokumenty společnosti Microsoft
description: Popisuje stránku Úlohy služby StorSimple Device Manager a způsob, jak ji použít ke sledování posledních a aktuálních úloh pro virtuální pole StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 31879821-b599-4609-a7f4-d4b0f658a933
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/11/2016
ms.author: alkohli
ms.openlocfilehash: dbab2aaab2c12bef07748f54e5864d042f1c982a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60302476"
---
# <a name="use-the-storsimple-device-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>Použití služby StorSimple Device Manager k zobrazení úloh pro virtuální pole StorSimple
## <a name="overview"></a>Přehled
Okno **Úlohy** poskytuje jeden centrální portál pro zobrazení a správu úloh, které jsou spuštěny na virtuálních polích, které jsou připojeny k vaší službě StorSimple Device Manager. Můžete zobrazit spuštěné, dokončené a neúspěšné úlohy pro více virtuálních zařízení. Výsledky jsou prezentovány v tabulkovém formátu.

![Kotouč pracovních míst](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)

Úlohy, které vás zajímají, můžete rychle najít filtrováním polí, jako jsou:

* **Časový rozsah** – Úlohy lze filtrovat na základě data a časového rozsahu.
* **Zařízení** – úlohy se inicializuje na konkrétním zařízení připojeném k vaší službě. Filtrované úlohy jsou pak tabulkovány podle následujících atributů:
  
  * **Název** – Název úlohy může být **Vše**, **Zálohování**, **Klonování**, **Převzetí služeb při selhání**, Stažení **aktualizací**nebo **Instalace aktualizací**.
  * **Stav** – Úlohy mohou být **Všechny**, **Probíhá**, **Úspěšné**nebo **Neúspěšné**nebo **Zrušené**.
  * **Entita** – úlohy lze přidružit ke svazku, sdílené složce nebo zařízení.
  * **Zařízení** – název zařízení, na kterém byla úloha spuštěna.
  * **Začalo** to – čas, kdy byla úloha spuštěna.
  * **Doba trvání** – doba trvání, po kterou byla úloha spuštěna.
* **Stav** – Můžete vyhledat všechny, spuštěné, dokončené nebo neúspěšné úlohy.
* **Typ úlohy** – Typ úlohy může být všechny, zálohování, obnovení, převzetí služeb při selhání, stahování aktualizací nebo instalace aktualizací.

Seznam úloh se aktualizuje každých 30 sekund.

## <a name="view-job-details"></a>Zobrazení podrobností o úloze
Chcete-li zobrazit podrobnosti o libovolné úloze, proveďte následující kroky.

#### <a name="to-view-job-details"></a>Zobrazení podrobností o úloze
1. V okně **Úlohy** zobrazte úlohy, které vás zajímají, spuštěním dotazu s příslušnými filtry. Můžete vyhledat dokončené nebo spuštěné úlohy.
2. Vyberte úlohu z tabulkového seznamu úloh.
   
    ![Pracovní čepel](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)
3. V dolní části stránky klikněte na **Podrobnosti**.
4. V dialogovém okně **Podrobnosti** můžete zobrazit stav, podrobnosti a statistiky času. Následující obrázek znázorňuje příklad dialogového okna **Podrobnosti zálohovací úlohy.**
   
    ![Podrobnosti o úloze](./media/storsimple-virtual-array-manage-jobs/ova-jobs-details.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>Selhání úlohy při pozastavení virtuálního počítače v hypervisoru
Pokud probíhá úloha na virtuálním poli StorSimple a zařízení (virtuální počítač zřízený v hypervisoru) je pozastaveno na více než 15 minut, úloha se nezdaří. Důvodem je, že váš čas virtuálního pole StorSimple není synchronizován s časem Microsoft Azure. 

Zobrazí se následující chyba: "Čas vašeho zařízení není synchronizován s časem Microsoft Azure o více než 15 minut. Ujistěte se, že hypervisor a časy zařízení jsou synchronizovány se serverem NTP. Ověřte, zda nejsou žádné problémy s připojením. Chcete-li vyřešit problémy s připojením, spusťte diagnostické testy z místního webového uživatelského rozhraní virtuálního zařízení."

Tato selhání platí pro úlohy zálohování, obnovení, aktualizace a převzetí služeb při selhání. Pokud váš virtuální počítač je zřízena v Hyper-V, počítač nakonec synchronizuje čas s hypervisorem. Jakmile se to stane, můžete restartovat svou úlohu.

## <a name="next-steps"></a>Další kroky
[Přečtěte si, jak používat místní webové uživatelské prostředí ke správě virtuálního pole StorSimple](storsimple-ova-web-ui-admin.md).

