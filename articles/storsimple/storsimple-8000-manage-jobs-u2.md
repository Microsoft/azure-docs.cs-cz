---
title: Zobrazení a Správa úloh pro řadu StorSimple 8000 | Dokumentace Microsoftu
description: Popisuje úlohy okně služby Správce zařízení StorSimple a použít ke sledování poslední, aktuální a naplánované úlohy zálohování.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64720690"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-jobs-update-3-and-later"></a>Pomocí služby Správce zařízení StorSimple můžete zobrazit a spravovat úlohy (aktualizace 3 a novější)

## <a name="overview"></a>Přehled
**Úlohy** okno poskytuje jednu centrální portál pro zobrazení a správu úloh, které byly spuštěny na zařízení připojená ke službě Správce zařízení StorSimple. Můžete zobrazit naplánované, spuštěný, dokončené, zrušené a neúspěšné úlohy pro více zařízení. Výsledky jsou uvedené ve formátu tabulky.

![Okno úlohy](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

Můžete rychle najít úloh, které mají zájem pomocí filtrování pole, jako:

* **Stav** – úlohy můžou být v průběhu, bylo úspěšné, zrušené, se nezdařilo, zrušení nebo úspěchu s chybami.
* **Časový rozsah** – úlohy je možné filtrovat podle rozsahu datum a čas. Tyto rozsahy následují za poslední hodinu, posledních 24 hodin, posledních 7 dní, posledních 30 dnů, minulého roku nebo vlastní data.
* **Typ** – typ úlohy může být naplánované zálohování, ručního zálohování, obnovení zálohu klonovat svazek, převzetí služeb při selhání kontejnerů svazků, vytvořit místně připojené svazky, změnu svazku, instalovat aktualizace, shromažďování protokolů podpory a vytvoření cloudového zařízení.
* **Zařízení** – zahájí úlohy na určitých zařízení připojená ke službě.
  
Filtrované úlohy jsou pak uvedených na základě následující atributy:
  
* **Název** – naplánované zálohování, ručního zálohování, obnovení zálohu, klonování svazku, převzetí služeb při selhání kontejnerů svazků, vytvořit místně připojený svazek, změnu svazku, instalovat aktualizace, shromažďování protokolů podpory nebo vytvořit cloudové zařízení.
* **Stav** – spuštěné, dokončené, zrušené, se nezdařilo, zrušení nebo dokončené s chybami.
* **Entity** – úlohy, které mohou být spojeny s svazku, zásady zálohování nebo zařízení. Například úloha klonování je přidruženo ke svazku, že naplánované úlohy zálohování je přidružené k zásadám zálohování. V důsledku operaci obnovení nebo zotavení po havárii (DR) se vytvoří úloha zařízení.
* **Zařízení** – název zařízení, na kterém byla spuštěna úloha.
* **Spustit na** – čas zahájení úlohy.
* **Doba trvání** – čas potřebný k dokončení úlohy.

Seznam úloh se aktualizuje každých 30 sekund.

Provedením následujících akcí souvisejících s úlohami na této stránce:

* Zobrazení podrobností o úloze
* Zrušení úlohy

## <a name="view-job-details"></a>Zobrazení podrobností o úloze
Proveďte následující kroky, chcete-li zobrazit podrobnosti libovolné úlohy.

#### <a name="to-view-job-details"></a>Chcete-li zobrazit podrobnosti o úloze
1. Přejděte do služby Správce zařízení StorSimple a potom klikněte na tlačítko **úlohy**.

2. V **úlohy** okně zobrazení úlohy mají zájem spuštěním dotazu s vhodné filtry. Můžete hledat dokončení spuštěný, nebo zrušit úlohy.

    ![Okno úlohy](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

2. Vyberte a klikněte na úlohu.

    ![Okno úlohy](./media/storsimple-8000-manage-jobs-u2/jobs3.png)

3. V okně podrobností úlohy můžete zobrazit stav, podrobností, statistiku časových údajů a dat statistiky.
   
    ![Podrobnosti o úloze](./media/storsimple-8000-manage-jobs-u2/jobs4.png)

## <a name="cancel-a-job"></a>Zrušení úlohy
Proveďte následující kroky a zrušit běžící úlohu.

> [!NOTE]
> Některé úlohy, například kvůli úpravě svazku pro změnu typu svazku nebo rozšíření svazku nejde zrušit.


### <a name="to-cancel-a-job"></a>Zrušit úlohu
1. Na **úlohy** stránce, zobrazit spuštěné úlohy, které chcete zrušit tak, že spouštění dotazů s vhodné filtry. Vyberte úlohu.

2. Klikněte pravým tlačítkem na vybrané úlohy vyvolejte místní nabídku a klikněte na tlačítko **zrušit**.

    ![Podrobnosti o úloze](./media/storsimple-8000-manage-jobs-u2/jobs2.png)

3. Po zobrazení výzvy k potvrzení klikněte na **Ano**. Tato úloha je teď zrušena.

## <a name="next-steps"></a>Další postup
* Zjistěte, jak [spravovat své zásady zálohování StorSimple](storsimple-8000-manage-backup-policies-u2.md).
* Zjistěte, jak [použití služby Správce zařízení StorSimple ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

