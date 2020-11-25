---
title: Zobrazení a správa úloh virtuálních polí StorSimple | Microsoft Docs
description: Popisuje stránku úlohy služby StorSimple Device Manager a jejich použití ke sledování nejnovějších a aktuálních úloh pro virtuální pole StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 31879821-b599-4609-a7f4-d4b0f658a933
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/11/2016
ms.author: alkohli
ms.openlocfilehash: d806d8a04dc1bd8547808d20c77bfec310f7dd06
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95992984"
---
# <a name="use-the-storsimple-device-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>Použijte službu StorSimple Device Manager k zobrazení úloh pro virtuální pole StorSimple.
## <a name="overview"></a>Přehled
Okno **úlohy** nabízí jeden centrální portál pro zobrazení a správu úloh, které jsou spuštěné ve virtuálních polích, která jsou připojená ke službě StorSimple Device Manager. Můžete zobrazit spuštěné, dokončené a neúspěšné úlohy pro více virtuálních zařízení. Výsledky jsou uvedeny v tabulkovém formátu.

![Okno úlohy](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)

Můžete rychle najít úlohy, které vás zajímají, filtrováním polí, jako jsou:

* **Časový rozsah** – úlohy je možné filtrovat podle rozsahu data a času.
* **Zařízení** – úlohy se iniciují na konkrétním zařízení, které je připojené k vaší službě. Filtrované úlohy jsou pak na základě těchto atributů:
  
  * **Název** – název úlohy může být **vše**, **zálohování**, **klonování**, **převzetí služeb při selhání**, **stahovat aktualizace** nebo **instalovat aktualizace**.
  * **Stav** – úlohy mohou být **všechny**, **probíhající**, **úspěšné** nebo **neúspěšné** nebo **zrušené**.
  * **Entita** – úlohy je možné přidružit ke svazku, sdílené složce nebo zařízení.
  * **Zařízení** – název zařízení, na kterém se úloha spustila.
  * **Zahájeno** – čas spuštění úlohy.
  * **Doba trvání** – doba, po kterou byla úloha spuštěna.
* **Stav** – můžete vyhledat všechny, spuštěné, dokončené nebo neúspěšné úlohy.
* **Typ úlohy** – typ úlohy může být vše, zálohování, obnovení, převzetí služeb při selhání, stahovat aktualizace nebo instalovat aktualizace.

Seznam úloh se aktualizuje každých 30 sekund.

## <a name="view-job-details"></a>Zobrazení podrobností o úloze
Chcete-li zobrazit podrobnosti o jakékoli úloze, proveďte následující kroky.

#### <a name="to-view-job-details"></a>Zobrazení podrobností o úloze
1. V okně **úlohy** Zobrazte úlohy, které vás zajímají, spuštěním dotazu s příslušnými filtry. Můžete vyhledat dokončené nebo spuštěné úlohy.
2. Vyberte úlohu z tabulkového seznamu úloh.
   
    ![Okno úlohy](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)
3. V dolní části stránky klikněte na tlačítko **Podrobnosti**.
4. V dialogovém okně **Podrobnosti** můžete zobrazit statistiku stavů, podrobností a času. Následující ilustrace znázorňuje příklad dialogového okna **Podrobnosti úlohy zálohování** .
   
    ![Podrobnosti o úloze](./media/storsimple-virtual-array-manage-jobs/ova-jobs-details.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>Selhání úlohy, když se virtuální počítač pozastaví v hypervisoru
Když ve virtuálním poli StorSimple probíhá úloha a zařízení (virtuální počítač zřízené v hypervisoru) se pozastaví na víc než 15 minut, úloha se nezdařila. Důvodem je to, že váš StorSimple virtuální pole není synchronizovaný s časem Microsoft Azure. 

Zobrazí se následující chyba: "doba vašeho zařízení není synchronizovaná s Microsoft Azureým časem o více než 15 minut. Ujistěte se, že se hypervisor a časy zařízení synchronizují se serverem NTP. Ověřte, že nedochází k žádným problémům s připojením. Pokud chcete řešit problémy s připojením, spusťte diagnostické testy z místního webového uživatelského rozhraní vašeho virtuálního zařízení.

Tyto chyby se vztahují na úlohy zálohování, obnovení, aktualizace a převzetí služeb při selhání. Pokud je váš virtuální počítač zřízený v Hyper-V, počítač nakonec synchronizuje čas s vaším hypervisorem. Jakmile k tomu dojde, můžete úlohu spustit znovu.

## <a name="next-steps"></a>Další kroky
Přečtěte si, [jak pomocí místního webového uživatelského rozhraní spravovat virtuální pole StorSimple](storsimple-ova-web-ui-admin.md).

