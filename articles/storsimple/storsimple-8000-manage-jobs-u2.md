---
title: Zobrazení a správa úloh pro řady StorSimple 8000 | Microsoft Docs
description: Popisuje okno úlohy služby StorSimple Správce zařízení a jeho použití ke sledování posledních, aktuálních a naplánovaných úloh zálohování.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: dc630021cbf93074119d4feedcb3a09fae1453e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91949424"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-jobs-update-3-and-later"></a>Použití služby StorSimple Správce zařízení k zobrazení a správě úloh (aktualizace 3 a novější)

## <a name="overview"></a>Přehled
Okno **úlohy** nabízí jeden centrální portál pro zobrazení a správu úloh, které byly spuštěny na zařízeních připojených ke službě StorSimple Správce zařízení. Můžete zobrazit naplánované, spuštěné, dokončené, zrušené a neúspěšné úlohy pro více zařízení. Výsledky jsou uvedeny v tabulkovém formátu.

![Okno úlohy](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

Můžete rychle najít úlohy, které vás zajímají, filtrováním polí, jako jsou:

* **Stav** – úlohy mohou být probíhající, úspěšné, zrušené, neúspěšné, zrušené nebo úspěšné s chybami.
* **Časový rozsah** – úlohy je možné filtrovat podle rozsahu data a času. Rozsahy jsou za 1 hodinu, posledních 24 hodin, posledních 7 dní, posledních 30 dní, minulý rok nebo vlastní datum.
* **Typ** – typ úlohy může být plánované zálohování, ruční zálohování, obnovení zálohy, klonování svazku, převzetí služeb při selhání kontejnerů svazků, vytváření místně připnutého svazku, změna svazku, instalace aktualizací, shromažďování protokolů podpory a vytváření cloudových zařízení.
* **Zařízení** – úlohy jsou spuštěné na určitém zařízení, které je připojené k vaší službě.
  
Filtrované úlohy se pak ukládají na základě následujících atributů:
  
* **Název** – plánované zálohování, ruční zálohování, obnovení zálohy, klonování svazků, převzetí služeb při selhání, vytvoření místně připojeného svazku, úprava svazku, instalace aktualizací, shromáždění protokolů podpory nebo vytvoření cloudového zařízení.
* **Stav** – spuštěno, dokončeno, zrušeno, selhalo, zrušení nebo dokončení s chybami.
* **Entita** – úlohy je možné přidružit ke svazku, zásadě zálohování nebo zařízení. Například úloha klonování je přidružená ke svazku, zatímco je naplánovaná úloha zálohování přidružená k zásadám zálohování. Úloha zařízení je vytvořena v důsledku zotavení po havárii (DR) nebo operace obnovení.
* **Zařízení** – název zařízení, na kterém se úloha spustila.
* **Zahájeno** – čas spuštění úlohy.
* **Doba trvání** – čas potřebný k dokončení úlohy.

Seznam úloh se aktualizuje každých 30 sekund.

Na této stránce můžete provádět následující akce související s úlohou:

* Zobrazení podrobností o úloze
* Zrušení úlohy

## <a name="view-job-details"></a>Zobrazení podrobností o úloze
Chcete-li zobrazit podrobnosti o jakékoli úloze, proveďte následující kroky.

#### <a name="to-view-job-details"></a>Zobrazení podrobností o úloze
1. Přejděte do služby StorSimple Správce zařízení a klikněte na **úlohy**.

2. V okně **úlohy** Zobrazte úlohy, které vás zajímají, spuštěním dotazu s příslušnými filtry. Můžete hledat dokončené, spuštěné nebo zrušené úlohy.

    ![Okno úlohy 2](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

2. Vyberte úlohu a klikněte na ni.

    ![Okno úlohy 3](./media/storsimple-8000-manage-jobs-u2/jobs3.png)

3. V okně podrobností úlohy můžete zobrazit informace o stavu, podrobnostech, časové statistice a statistice dat.
   
    ![Podrobnosti o úloze](./media/storsimple-8000-manage-jobs-u2/jobs4.png)

## <a name="cancel-a-job"></a>Zrušení úlohy
Provedením následujících kroků zrušíte spuštěnou úlohu.

> [!NOTE]
> Některé úlohy, jako je například změna svazku pro změnu typu svazku nebo rozšíření svazku, nelze zrušit.


### <a name="to-cancel-a-job"></a>Zrušení úlohy
1. Na stránce **úlohy** Zobrazte spuštěné úlohy, které chcete zrušit spuštěním dotazu s příslušnými filtry. Vyberte úlohu.

2. Kliknutím pravým tlačítkem myši na vybranou úlohu vyvolejte kontextovou nabídku a klikněte na tlačítko **Storno**.

    ![Podrobnosti úlohy 2](./media/storsimple-8000-manage-jobs-u2/jobs2.png)

3. Po zobrazení výzvy k potvrzení klikněte na **Ano**. Tato úloha je teď zrušená.

## <a name="next-steps"></a>Další kroky
* Naučte se [Spravovat zásady zálohování StorSimple](storsimple-8000-manage-backup-policies-u2.md).
* Naučte se [používat službu StorSimple Správce zařízení ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

