---
title: Zobrazení a Správa úloh StorSimple Virtual Array | Dokumentace Microsoftu
description: Popisuje úlohy stránce služby Správce zařízení StorSimple a použít ho ke sledování nedávných a aktuálních úloh pro StorSimple Virtual Array.
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
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52333560"
---
# <a name="use-the-storsimple-device-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>Slouží k zobrazení úloh pro StorSimple Virtual Array do služby Správce zařízení StorSimple
## <a name="overview"></a>Přehled
**Úlohy** okno poskytuje jednu centrální portál pro zobrazování a správu úloh, které jsou spuštěny na virtuální pole, které jsou připojené ke službě Správce zařízení StorSimple. Můžete zobrazit spuštěné, dokončenou a neúspěšné úlohy pro více virtuálních zařízení. Výsledky jsou uvedené ve formátu tabulky.

![Okno úlohy](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)

Můžete rychle najít úloh, které mají zájem pomocí filtrování pole, jako:

* **Časový rozsah** – úlohy je možné filtrovat podle rozsahu datum a čas.
* **Zařízení** – úlohy se zahájí na konkrétní zařízení připojené k vaší službě. Filtrované úlohy jsou pak uvedených na základě následujících atributů:
  
  * **Název** – název úlohy může být **všechny**, **zálohování**, **klonování**, **převzetí služeb při selhání**, **stahování aktualizací**, nebo **instalovat aktualizace**.
  * **Stav** – úlohy můžou být **všechny**, **probíhá**, **Succeeded**, nebo **neúspěšné**, nebo **zrušeno**.
  * **Entity** – úlohy, které mohou být spojeny s svazek, sdílená složka nebo zařízení.
  * **Zařízení** – název zařízení, na kterém byla spuštěna úloha.
  * **Spustit na** – čas zahájení úlohy.
  * **Doba trvání** – doba trvání podle kterého se spouští úloha.
* **Stav** – můžete vyhledat všechny spuštěné, Dokončeno nebo neúspěšné úlohy.
* **Typ úlohy** – typ úlohy být vše, zálohování, obnovení, převzetí služeb při selhání, stažení aktualizací nebo instalace aktualizací.

Seznam úloh se aktualizuje každých 30 sekund.

## <a name="view-job-details"></a>Zobrazení podrobností o úloze
Proveďte následující kroky, chcete-li zobrazit podrobnosti libovolné úlohy.

#### <a name="to-view-job-details"></a>Chcete-li zobrazit podrobnosti o úloze
1. Na **úlohy** okně zobrazení úlohy mají zájem spuštěním dotazu s vhodné filtry. Můžete vyhledat spuštěné nebo dokončené úlohy.
2. Vyberte úlohu z tabulkového seznamu úloh.
   
    ![Okno úlohy](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)
3. V dolní části stránky klikněte na tlačítko **podrobnosti**.
4. V **podrobnosti** dialogovém okně můžete zobrazit stav, podrobnosti a statistiku časových údajů. Následující obrázek znázorňuje příklad **podrobnosti úlohy zálohování** dialogové okno.
   
    ![Podrobnosti úlohy](./media/storsimple-virtual-array-manage-jobs/ova-jobs-details.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>Selhání úlohy, když se virtuální počítač pozastaví v hypervisoru
Když úloha je ve průběh StorSimple Virtual Array a zařízení (virtuální počítač zřízený v hypervisoru) je pozastaven na více než 15 minut, úloha se nezdaří. To je z důvodu času StorSimple Virtual Array nejsou synchronizovány s časem služby Microsoft Azure. 

Zobrazí se následující chyba: "čas zařízení není synchronizovaný s časem služby Microsoft Azure o víc než 15 minut. Ujistěte se, že hypervisoru a zařízení, které časy jsou synchronizovány pomocí serveru NTP. Ověřte, že zde nejsou žádné problémy s připojením. K řešení problémů s připojením, spusťte diagnostické testy z místního webového uživatelského rozhraní vašeho virtuálního zařízení."

Tyto chyby se vztahují na úlohy zálohování, obnovení, aktualizace a převzetí služeb při selhání. Pokud zřízení virtuálního počítače Hyper-v na počítači nakonec synchronizuje čas s vaší hypervisoru. Jakmile se to stane, můžete restartovat úlohy.

## <a name="next-steps"></a>Další postup
[Další informace o použití místního webového uživatelského rozhraní pro správu StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

