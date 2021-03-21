---
title: Úlohy zálohování StorSimple Snapshot Manager | Microsoft Docs
description: Popisuje, jak použít modul snap-in StorSimple Snapshot Manager konzoly MMC k zobrazení a správě naplánovaných, aktuálně spuštěných a dokončených úloh zálohování.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: bf4dcff6-c819-4766-b9d9-9922831cb200
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 3c26a84e32a17cba83b5ca895f146e561072fa62
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95998188"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>Zobrazení a správa úloh zálohování pomocí Snapshot Manager StorSimple

## <a name="overview"></a>Přehled
Uzel **úlohy** v podokně **oboru** zobrazuje **naplánované**, **posledních 24 hodin** a **spuštěné** úlohy zálohování, které jste spustili interaktivně, nebo nakonfigurovanou zásadou. 

V tomto kurzu se dozvíte, jak můžete pomocí uzlu **Jobs (úlohy** ) zobrazit informace o plánovaných, nedávných a aktuálně spuštěných úlohách zálohování. (Seznam úloh a odpovídající informace se zobrazí v podokně **výsledků** .) Kromě toho můžete kliknout pravým tlačítkem myši na uvedenou úlohu a zobrazit kontextovou nabídku, která obsahuje seznam dostupných akcí.

## <a name="view-scheduled-jobs"></a>Zobrazit naplánované úlohy
Pomocí následujícího postupu můžete zobrazit naplánované úlohy zálohování.

#### <a name="to-view-scheduled-jobs"></a>Zobrazení plánovaných úloh
1. Kliknutím na ikonu na ploše spusťte StorSimple Snapshot Manager. 
2. V podokně **obor** rozbalte uzel **úlohy** a klikněte na **naplánované**. V podokně **výsledků** se zobrazí následující informace:
   
   * **Název** – název naplánovaného snímku
   * **Další spuštění** – datum a čas dalšího naplánovaného snímku
   * **Poslední spuštění** – datum a čas posledního naplánovaného snímku
     
     > [!NOTE]
     > Pouze pro jednorázové snímky bude **příští spuštění** a **Poslední spuštění** stejné.
     
     ![Naplánované úlohy zálohování](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
3. Chcete-li provést další akce na konkrétní úlohu, klikněte pravým tlačítkem myši na název úlohy v podokně **výsledků** a vyberte možnost z možností nabídky.

## <a name="view-recent-jobs"></a>Zobrazit nedávné úlohy
Pomocí následujícího postupu můžete zobrazit úlohy zálohování a obnovení, které byly dokončeny za posledních 24 hodin.

#### <a name="to-view-recent-jobs"></a>Zobrazení posledních úloh
1. Kliknutím na ikonu na ploše spusťte StorSimple Snapshot Manager.
2. V podokně **obor** rozbalte uzel **úlohy** a klikněte na **Poslední 24 hodin**. V podokně **výsledků** se zobrazují úlohy zálohování za posledních 24 hodin (maximálně 64 úloh). V podokně **výsledků** se zobrazí následující informace v závislosti na zadaných možnostech **zobrazení** :
   
   * **Název** – název naplánovaného snímku.
   * **Začalo** – datum a čas zahájení snímku.
   * **Stopped** – datum a čas dokončení snímku nebo ukončení.
   * **Uplynulo** – doba mezi časem **zahájení** a **zastavením** .
   * **Stav** – stav poslední dokončené úlohy. **Úspěch** indikuje, že se zálohování úspěšně vytvořilo. **Selhání** znamená, že úloha nebyla úspěšně spuštěna.
   * **Informace** – důvod selhání.
   * **Zpracované bajty (MB)** – množství dat ze zpracované skupiny svazků (v MB). 
     
     ![Úlohy, které byly spuštěny za posledních 24 hodin](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 
3. Chcete-li provést další akce na konkrétní úlohu, klikněte pravým tlačítkem myši na název úlohy v podokně **výsledků** a vyberte možnost z možností nabídky.
   
    ![Odstranění úlohy](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png)

## <a name="view-currently-running-jobs"></a>Zobrazit aktuálně spuštěné úlohy
K zobrazení aktuálně spuštěných úloh použijte následující postup.

#### <a name="to-view-currently-running-jobs"></a>Zobrazení aktuálně spuštěných úloh
1. Kliknutím na ikonu na ploše spusťte StorSimple Snapshot Manager.
2. V podokně **obor** rozbalte uzel **úlohy** a klikněte na možnost **spuštěno**. V závislosti na zadaných možnostech **zobrazení** se v podokně **výsledků** zobrazí následující informace:
   
   * **Název** – název naplánovaného snímku.
   * **Začalo** – datum a čas zahájení snímku.
   * **Kontrolní bod** – aktuální akce zálohy.
   * **Stav** – procentuální podíl dokončení.
   * **Uplynulo** – množství času, které bylo od zahájení zálohování úspěšné. 
   * **Průměrná propustnost (MB)** – poměr celkového objemu zpracovaných dat do celkového času pro zpracování (MB).
   * **Zpracované bajty (MB)** – celkový počet zpracovaných dat (v MB).
   * **Zapsané bajty (MB)** – celkový počet zapsaných dat (v MB). Zahrnuje data i metadata, a proto jsou obvykle větší než zpracované bajty.
     
     ![Aktuálně spuštěné úlohy](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)
3. Chcete-li provést další akce na konkrétní úlohu, klikněte pravým tlačítkem myši na název úlohy v podokně **výsledků** a vyberte možnost z možností nabídky.

## <a name="next-steps"></a>Další kroky
* Naučte se [používat Snapshot Manager StorSimple ke správě řešení StorSimple](storsimple-snapshot-manager-admin.md).
* Naučte se [používat Snapshot Manager StorSimple ke správě katalogu záloh](storsimple-snapshot-manager-manage-backup-catalog.md).

