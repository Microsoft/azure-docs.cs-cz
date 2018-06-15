---
title: Úlohy zálohování Snapshot Manager zařízení StorSimple | Microsoft Docs
description: Popisuje způsob použití modulu snap-in konzoly MMC StorSimple Snapshot Manager můžete zobrazit a spravovat naplánované, aktuálně spuštěné a dokončené úlohy zálohování.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: bf4dcff6-c819-4766-b9d9-9922831cb200
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: 03e306b62250f2bb033cc14e856a59760b5406c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875813"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>Pomocí StorSimple Snapshot Manager můžete zobrazit a spravovat úlohy zálohování

## <a name="overview"></a>Přehled
**Úlohy** uzel v **oboru** podokně zobrazí **naplánovaná**, **posledních 24 hodin**, a **systémem** úlohy zálohování, které jste spustili interaktivně nebo nakonfigurované zásady. 

Tento kurz popisuje, jak můžete použít **úlohy** uzel k zobrazení informací o naplánované, poslední a aktuálně spuštěné úlohy zálohování. (Se zobrazí v seznamu úloh a odpovídající informace **výsledky** podokně.) Kromě toho můžete klikněte pravým tlačítkem na úlohu uvedené a najdete v části z kontextové nabídky, které jsou uvedeny dostupné akce.

## <a name="view-scheduled-jobs"></a>Zobrazit naplánované úlohy
Následujícím postupem zobrazíte naplánované úlohy zálohování.

#### <a name="to-view-scheduled-jobs"></a>Chcete-li zobrazit naplánované úlohy
1. Klikněte na ikonu plochy spusťte StorSimple Snapshot Manager. 
2. V **oboru** podokně rozbalte **úlohy** uzel a klikněte na tlačítko **naplánovaná**. Tyto informace se zobrazí v **výsledky** podokně:
   
   * **Název** – název plánovaný snímek
   * **Následně spusťte** – datum a čas na další plánovaný snímek
   * **Poslední spuštění** – datum a čas poslední plánovaný snímek
     
     > [!NOTE]
     > Pro jednorázové pouze snímky **další spuštění** a **poslední spuštění** budou stejné.
     
     ![Naplánované úlohy zálohování](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
3. Chcete-li provádět další akce pro konkrétní úlohu, klikněte pravým tlačítkem na název úlohy v **výsledky** panelu a vyberte jednu z možností v nabídce.

## <a name="view-recent-jobs"></a>Zobrazit nejnovější úlohy
Následující postup použijte k zobrazení zálohování a obnovení úlohy, které byly dokončeny za posledních 24 hodin.

#### <a name="to-view-recent-jobs"></a>Chcete-li zobrazit nejnovější úlohy
1. Klikněte na ikonu plochy spusťte StorSimple Snapshot Manager.
2. V **oboru** podokně rozbalte **úlohy** uzel a klikněte na tlačítko **posledních 24 hodin**. **Výsledky** podokně se zobrazují úloh zálohování za posledních 24 hodin (na maximálně 64 úlohy). Tyto informace se zobrazí v **výsledky** podokně, v závislosti na **zobrazení** možnosti můžete zadat:
   
   * **Název** – název plánovaný snímek.
   * **Spuštění** – datum a čas zahájení snímku.
   * **Zastavit** – datum a čas dokončení nebo byl ukončen snímku.
   * **Uplynulý čas** – časového intervalu mezi **Začínáme** a **Zastaveno** časy.
   * **Stav** – stav nedávno dokončené úlohy. **Úspěch** označuje, že záloha byla úspěšně vytvořena. **Se nezdařilo** označuje, že úloha nebyla úspěšně spuštěna.
   * **Informace o** – příčinu selhání.
   * **Zpracování bajtů (MB)** – množství dat ze skupiny svazek, který byl zpracován (v MB). 
     
     ![Úlohy, které byly spuštěny za posledních 24 hodin](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 
3. Chcete-li provádět další akce pro konkrétní úlohu, klikněte pravým tlačítkem na název úlohy v **výsledky** panelu a vyberte jednu z možností v nabídce.
   
    ![Odstranit úlohu](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png)

## <a name="view-currently-running-jobs"></a>Zobrazit aktuálně spuštěné úlohy
Použijte následující postup zobrazení úloh, které jsou aktuálně spuštěné.

#### <a name="to-view-currently-running-jobs"></a>Chcete-li zobrazit aktuálně spuštěné úlohy
1. Klikněte na ikonu plochy spusťte StorSimple Snapshot Manager.
2. V **oboru** podokně rozbalte **úlohy** uzel a klikněte na tlačítko **systémem**. V závislosti na tom **zobrazení** určíte, následující informace se zobrazí v možnosti **výsledky** podokně:
   
   * **Název** – název plánovaný snímek.
   * **Spuštění** – datum a čas zahájení snímku.
   * **Kontrolní bod** – zálohy aktuální akce.
   * **Stav** – o procentuální hodnotě dokončení.
   * **Uplynulý čas** – množství času, který prošel od začátku zálohování. 
   * **Průměrná propustnost (MB)** – poměr celkový počet bajtů dat, které zpracoval, celkový čas potřebný pro zpracování (MB).
   * **Zpracování bajtů (MB)** – celkový počet bajtů dat, zpracování (v MB).
   * **(MB) zapsaných bajtů** – celkový počet bajtů zapsaných (v MB). Zahrnuje data, jakož i metadata a proto je obvykle větší než zpracovat bajtů.
     
     ![Aktuálně spuštěné úlohy](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)
3. Chcete-li provádět další akce pro konkrétní úlohu, klikněte pravým tlačítkem na název úlohy v **výsledky** panelu a vyberte jednu z možností v nabídce.

## <a name="next-steps"></a>Další kroky
* Zjistěte, jak [použít ke správě vašeho řešení StorSimple Snapshot Manager zařízení StorSimple](storsimple-snapshot-manager-admin.md).
* Zjistěte, jak [StorSimple Snapshot Manager použít ke správě katalogu zálohování](storsimple-snapshot-manager-manage-backup-catalog.md).

