---
title: Zobrazit a spravovat úlohy řady StorSimple 8000 | Dokumenty společnosti Microsoft
description: Popisuje okno Úlohy služby StorSimple Device Manager a způsob, jak ji použít ke sledování nedávných, aktuálních a plánovaných úloh zálohování.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 462f8dafdffa7ee01e6ccf7945a1abfdff90db42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254869"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-jobs-update-3-and-later"></a>Použití služby StorSimple Device Manager k zobrazení a správě úloh (aktualizace 3 a novější)

## <a name="overview"></a>Přehled
Okno **Úlohy** poskytuje jeden centrální portál pro zobrazení a správu úloh, které byly spuštěny na zařízeních připojených ke službě StorSimple Device Manager. Můžete zobrazit naplánované, spuštěné, dokončené, zrušené a neúspěšné úlohy pro více zařízení. Výsledky jsou prezentovány v tabulkovém formátu.

![Kotouč pracovních míst](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

Úlohy, které vás zajímají, můžete rychle najít filtrováním polí, jako jsou:

* **Stav** – Úlohy mohou být v průběhu, úspěšné, zrušené, neúspěšné, zrušené nebo úspěšné s chybami.
* **Časový rozsah** – Úlohy lze filtrovat na základě data a časového rozsahu. Rozsahy jsou za 1 hodinu, posledních 24 hodin, posledních 7 dní, posledních 30 dní, minulý rok nebo vlastní datum.
* **Typ** – Typ úlohy lze naplánovat zálohování, ruční zálohování, obnovení zálohování, klonování svazku, převzetí služeb při selhání kontejnery svazku, vytvářet místně vázaný svazek, upravovat svazek, instalovat aktualizace, shromažďovat protokoly podpory a vytvářet cloudové zařízení.
* **Zařízení** – úlohy jsou inicializovány na určitém zařízení připojeném k vaší službě.
  
Filtrované úlohy jsou pak tabulkovány na základě následujících atributů:
  
* **Název** – plánované zálohování, ruční zálohování, obnovení zálohování, klonování svazku, převzetí služeb při selhání kontejnery svazku, vytvoření místně vázaný svazek, upravit svazek, instalovat aktualizace, shromažďovat protokoly podpory, nebo vytvořit cloud zařízení.
* **Stav** – spuštěno, dokončeno, zrušeno, nezdařilo se, zrušilo nebo bylo dokončeno s chybami.
* **Entita** – úlohy mohou být přidruženy ke svazku, zásadám zálohování nebo zařízení. Například klonovací úloha je přidružena ke svazku, zatímco naplánovaná úloha zálohování je přidružena k zásadám zálohování. Úloha zařízení je vytvořena v důsledku zotavení po havárii (DR) nebo operace obnovení.
* **Zařízení** – název zařízení, na kterém byla úloha spuštěna.
* **Začalo** to – čas, kdy byla úloha spuštěna.
* **Doba trvání** – doba potřebná k dokončení úlohy.

Seznam úloh se aktualizuje každých 30 sekund.

Na této stránce můžete provádět následující akce související s úlohou:

* Zobrazení podrobností o úloze
* Zrušení úlohy

## <a name="view-job-details"></a>Zobrazení podrobností o úloze
Chcete-li zobrazit podrobnosti o libovolné úloze, proveďte následující kroky.

#### <a name="to-view-job-details"></a>Zobrazení podrobností o úloze
1. Přejděte na službu Správce zařízení StorSimple a klepněte na tlačítko **Úlohy**.

2. V okně **Úlohy** zobrazte úlohy, které vás zajímají, spuštěním dotazu s příslušnými filtry. Můžete vyhledat dokončené, spuštěné nebo zrušené úlohy.

    ![Pracovní čepel](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

2. Vyberte úlohu a klikněte na ni.

    ![Pracovní čepel](./media/storsimple-8000-manage-jobs-u2/jobs3.png)

3. V okně podrobností o úloze můžete zobrazit stav, podrobnosti, statistiky času a statistiky dat.
   
    ![Podrobnosti o úloze](./media/storsimple-8000-manage-jobs-u2/jobs4.png)

## <a name="cancel-a-job"></a>Zrušení úlohy
Chcete-li zrušit spuštěnou úlohu, proveďte následující kroky.

> [!NOTE]
> Některé úlohy, například úpravu svazku za účelem změny typu svazku nebo rozšíření svazku, nelze zrušit.


### <a name="to-cancel-a-job"></a>Zrušení úlohy
1. Na stránce **Úlohy** zobrazte spuštěné úlohy, které chcete zrušit, spuštěním dotazu s příslušnými filtry. Vyberte úlohu.

2. Kliknutím pravým tlačítkem myši na vybranou úlohu vyvoláte místní nabídku a klepněte na příkaz **Storno**.

    ![Podrobnosti o úloze](./media/storsimple-8000-manage-jobs-u2/jobs2.png)

3. Po zobrazení výzvy k potvrzení klikněte na **Ano**. Tato úloha je nyní zrušena.

## <a name="next-steps"></a>Další kroky
* Přečtěte si, jak [spravovat zásady zálohování StorSimple](storsimple-8000-manage-backup-policies-u2.md).
* Přečtěte si, jak [používat službu StorSimple Device Manager ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

