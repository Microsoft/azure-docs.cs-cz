---
title: Zobrazit a spravovat úlohy pole virtuální zařízení StorSimple | Microsoft Docs
description: Popisuje stránku úlohy služby StorSimple Manager zařízení a způsobu jeho použití ke sledování poslední a aktuální úlohy pro pole virtuální zařízení StorSimple.
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
ms.openlocfilehash: 3fd1c262a8ce94d8e98f2b066a8028d974b15b1d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
ms.locfileid: "23927716"
---
# <a name="use-the-storsimple-device-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>Zobrazit úlohy pro pole virtuální zařízení StorSimple pomocí služby StorSimple Manager zařízení
## <a name="overview"></a>Přehled
**Úlohy** okno poskytuje jednu centrální portál pro zobrazování a správu úloh, které jsou spuštěny na virtuální pole, které jsou připojené k vaší službě StorSimple Manager zařízení. Můžete zobrazit spuštěné, dokončené a k selhání úlohy pro více virtuální zařízení. Výsledky se zobrazí v tabulkovém formátu.

![Okno úlohy](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)

Můžete rychle najít úlohy, které zajímá pomocí filtrování na pole, jako například:

* **Čas rozsah** – úlohy je možné filtrovat podle rozsahu datum a čas.
* **Zařízení** – úloh se spouští na konkrétní zařízení připojena k službě. Filtrované úlohy jsou pak poskytovalo na základě následujících atributů:
  
  * **Název** – název úlohy může být **všechny**, **zálohování**, **klon**, **převzetí služeb při selhání**, **stahování aktualizací**, nebo **nainstalovat aktualizace**.
  * **Stav** – úloh může být **všechny**, **v průběhu**, **úspěšné**, nebo **se nezdařilo**, nebo **zrušeno**.
  * **Entity** – úloh může být přidružen svazek, sdílená složka nebo zařízení.
  * **Zařízení** – název zařízení, na kterém byla úloha spuštěna.
  * **Bylo zahájeno** – čas zahájení úlohy.
  * **Doba trvání** – dobu trvání pro na úloha byla spuštěna.
* **Stav** – můžete vyhledat všechny, se nezdařila, dokončené nebo spuštěné úlohy.
* **Typ úlohy** – typ úlohy může být vše, zálohování, obnovení, převzetí služeb při selhání, stáhnout aktualizace nebo instalace aktualizací.

Seznam úloh se aktualizují každých 30 sekund.

## <a name="view-job-details"></a>Zobrazení podrobností o úloze
Proveďte následující kroky, chcete-li zobrazit podrobnosti libovolné úlohy.

#### <a name="to-view-job-details"></a>Chcete-li zobrazit podrobnosti úlohy
1. Na **úlohy** okně zobrazení úloh se zajímá spuštěním dotazu s příslušné filtry. Můžete vyhledat dokončené nebo spuštěné úlohy.
2. Vyberte úlohu z tabulkového seznamu úloh.
   
    ![Okno úlohy](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)
3. V dolní části stránky klikněte na tlačítko **podrobnosti**.
4. V **podrobnosti** dialogové okno, můžete zobrazit stav, podrobnosti a statistiku časových údajů. Následující obrázek ukazuje příklad **podrobnosti úlohy zálohování** dialogové okno.
   
    ![Podrobnosti úlohy](./media/storsimple-virtual-array-manage-jobs/ova-jobs-details.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>Selhání úlohy při virtuálního počítače je pozastaven v hypervisoru
Když je úloha v průběhu na pole virtuální zařízení StorSimple a zařízení (virtuálního počítače v hypervisoru zřídit) je pozastavena pro více než 15 minut, úloha selže. To je z důvodu doby pole virtuální zařízení StorSimple nejsou synchronizovány s časem Microsoft Azure. 

Zobrazí se následující chyba: "váš čas zařízení není synchronizován s časem Microsoft Azure ve víc než 15 minut. Zajistěte, aby hypervisoru a zařízení, které jsou synchronizovány časy se server NTP. Ověřte, že neexistují žádné problémy s připojením. Chcete-li vyřešit problémy s připojením, spustit diagnostické testy z místního webového uživatelského rozhraní vašeho virtuální zařízení."

Tyto chyby se vztahují na úlohy zálohování, obnovení, aktualizace a převzetí služeb při selhání. Pokud zřízení virtuálního počítače technologie Hyper-V na počítač nakonec synchronizuje čas s vaší hypervisoru. Jakmile k tomu dojde, můžete ji znovu úlohu.

## <a name="next-steps"></a>Další postup
[Další informace o použití místního webového uživatelského rozhraní pro správu pole virtuální zařízení StorSimple](storsimple-ova-web-ui-admin.md).

