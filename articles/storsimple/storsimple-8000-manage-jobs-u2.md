---
title: Zobrazit a spravovat úlohy pro řady StorSimple 8000 | Microsoft Docs
description: Popisuje, v okně úlohy služby StorSimple Manager zařízení a způsobu jeho použití ke sledování poslední, aktuální a naplánované úlohy zálohování.
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
ms.openlocfilehash: bf8038b7171053b75eeb9aed88bff4246e65a8a8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874812"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-jobs-update-3-and-later"></a>Pomocí služby StorSimple Manager zařízení můžete zobrazit a spravovat úlohy (Update 3 a novější)

## <a name="overview"></a>Přehled
**Úlohy** okno poskytuje jednu centrální portál pro zobrazení a Správa úloh, které byly zahájeny v zařízeních připojení ke službě StorSimple Manager zařízení. Můžete zobrazit naplánované, spuštěná, dokončené, zrušené a k selhání úlohy pro více zařízení. Výsledky se zobrazí v tabulkovém formátu.

![Okno úlohy](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

Můžete rychle najít úlohy, které zajímá pomocí filtrování na pole, jako například:

* **Stav** – úloh může být v průběhu, byly úspěšné, zrušené, se nezdařilo, ruší nebo úspěšně dokončeno s chybami.
* **Čas rozsah** – úlohy je možné filtrovat podle rozsahu datum a čas. Tyto rozsahy následují za hodiny za posledních 24 hodin, posledních 7 dnů, za 30 dní, minulém roce nebo vlastní hodnota data.
* **Typ** – typ úlohy můžou být plánované zálohování, ruční zálohy, obnovení zálohu, klonování svazku, převzít kontejnery svazků, vytváření místně vázaný svazek, úpravě svazku, instalaci aktualizací, shromažďovat protokoly podpory a vytvoření cloudu zařízení.
* **Zařízení** – úloh se spouští na určité zařízení připojena k službě.
  
Filtrované úlohy jsou pak v tabulce na základě následující atributy:
  
* **Název** – naplánované zálohování, ruční zálohování, obnovení zálohu, klonování svazku, převzetí služeb při selhání kontejnery svazků, vytvořte místně vázaný svazek, upravit svazek, instalaci aktualizací, shromažďovat protokoly podpory nebo vytvořte cloudu zařízení.
* **Stav** – spuštěný, dokončené, zrušené, se nezdařila, ruší nebo dokončena s chybami.
* **Entity** – úloh může být přidružen svazku, zásady zálohování nebo zařízení. Například úlohu klonování je přidružen svazku, zatímco je přidružený k zásadě zálohování naplánované úlohy zálohování. V důsledku operaci obnovení nebo zotavení po havárii (DR), se vytvoří úloha zařízení.
* **Zařízení** – název zařízení, na kterém byla úloha spuštěna.
* **Bylo zahájeno** – čas zahájení úlohy.
* **Doba trvání** – čas potřebný k dokončení úlohy.

Seznam úloh se aktualizují každých 30 sekund.

Na této stránce můžete provádět následující úlohy související akce:

* Zobrazení podrobností o úloze
* Zrušení úlohy

## <a name="view-job-details"></a>Zobrazení podrobností o úloze
Proveďte následující kroky, chcete-li zobrazit podrobnosti libovolné úlohy.

#### <a name="to-view-job-details"></a>Chcete-li zobrazit podrobnosti úlohy
1. Přejděte do služby StorSimple Manager zařízení a pak klikněte na tlačítko **úlohy**.

2. V **úlohy** okně zobrazení úloh se zajímá spuštěním dotazu s odpovídající filtry. Pro dokončení může hledat spuštěný, nebo došlo ke zrušení úlohy.

    ![Okno úlohy](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

2. Vyberte a klikněte na úlohu.

    ![Okno úlohy](./media/storsimple-8000-manage-jobs-u2/jobs3.png)

3. V okně podrobností úlohy můžete zobrazit stav, podrobnosti, čas statistiky a data statistiky.
   
    ![Podrobnosti úlohy](./media/storsimple-8000-manage-jobs-u2/jobs4.png)

## <a name="cancel-a-job"></a>Zrušení úlohy
Proveďte následující kroky, chcete-li zrušit probíhající úloze.

> [!NOTE]
> Některé úlohy, jako jsou úpravy svazek, chcete-li změnit typ svazku nebo rozšíření svazku, nelze zrušit.


### <a name="to-cancel-a-job"></a>Zrušení úlohy
1. Na **úlohy** stránky, zobrazit spuštěné úlohy, kterou chcete zrušit spuštěním dotazu s odpovídající filtry. Vyberte úlohu.

2. Klikněte pravým tlačítkem na vybrané úlohy vyvolání místní nabídky a klikněte na tlačítko **zrušit**.

    ![Podrobnosti úlohy](./media/storsimple-8000-manage-jobs-u2/jobs2.png)

3. Po zobrazení výzvy k potvrzení klikněte na **Ano**. Tato úloha je nyní zrušena.

## <a name="next-steps"></a>Další kroky
* Zjistěte, jak [spravovat vaše zásady zálohování StorSimple](storsimple-8000-manage-backup-policies-u2.md).
* Zjistěte, jak [použít službu StorSimple Manager zařízení ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

