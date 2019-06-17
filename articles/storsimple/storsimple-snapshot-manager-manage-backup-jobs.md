---
title: Úlohy zálohování StorSimple Snapshot Manageru | Dokumentace Microsoftu
description: Popisuje, jak pomocí modulu snap-in konzoly MMC StorSimple Snapshot Manager umožňuje zobrazit a spravovat plánované, aktuálně běžících a dokončených úloh zálohování.
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
ms.openlocfilehash: c34ff487f03d90b16b6660fbad77c3a16699e165
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64719869"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>K zobrazení a Správa úloh zálohování pomocí StorSimple Snapshot Manageru

## <a name="overview"></a>Přehled
**Úlohy** uzlu v **oboru** podokně ukazuje **naplánované**, **posledních 24 hodin**, a **systémem** úlohy zálohování, které iniciováno interaktivně nebo pomocí nakonfigurovaných zásad. 

Tento kurz vysvětluje, jak můžete použít **úlohy** uzel k zobrazení informací o naplánované, poslední a aktuálně spuštěné úlohy zálohování. (Se zobrazí v seznamu úloh a odpovídající informace **výsledky** podokně.) Kromě toho můžete klikněte pravým tlačítkem na uvedené úlohy a zobrazit místní nabídku, která obsahuje seznam dostupných akcí.

## <a name="view-scheduled-jobs"></a>Zobrazení naplánovaných úloh
Následujícím postupem zobrazíte naplánovaných úloh zálohování.

#### <a name="to-view-scheduled-jobs"></a>Chcete-li zobrazit naplánované úlohy
1. Klepněte na ikonu klasické pracovní plochy spusťte StorSimple Snapshot Manageru. 
2. V **oboru** podokně rozbalte **úlohy** uzel a klikněte na **naplánované**. Tyto informace zobrazí **výsledky** podokna:
   
   * **Název** – název plánovaný snímek
   * **Následně spusťte** – datum a čas na další plánovaný snímek
   * **Poslední spuštění** – datum a čas posledního plánovaný snímek
     
     > [!NOTE]
     > Pro jednorázové pouze snímky **další spuštění** a **poslední spuštění** budou stejné.
     
     ![Naplánované úlohy zálohování](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
3. K provedení další akce pro konkrétní úlohu, klikněte pravým tlačítkem na název úlohy v **výsledky** podokně a vyberte z možností v nabídce.

## <a name="view-recent-jobs"></a>Zobrazit nejnovější úlohy
Pomocí následujícího postupu na zobrazení zálohování a obnovení úloh, které byly dokončeny za posledních 24 hodin.

#### <a name="to-view-recent-jobs"></a>Chcete-li zobrazit poslední úlohy
1. Klepněte na ikonu klasické pracovní plochy spusťte StorSimple Snapshot Manageru.
2. V **oboru** podokně rozbalte **úlohy** uzel a klikněte na tlačítko **posledních 24 hodin**. **Výsledky** podokně se zobrazí úlohy zálohování za posledních 24 hodin (na maximálně 64 úlohy). Tyto informace se zobrazí v **výsledky** podokně, v závislosti na tom **zobrazení** zadaných parametrů:
   
   * **Název** – název plánovaný snímek.
   * **Spuštění** – datum a čas, kdy začal snímku.
   * **Zastavit** – datum a čas, kdy bylo dokončeno snímku, nebo byl ukončen.
   * **Uplynulý** – množství času mezi **spuštěno** a **Zastaveno** časy.
   * **Stav** – stav nedávno dokončené úlohy. **Úspěch** označuje, že zálohování byla úspěšně vytvořena. **Nepovedlo** označuje, že úlohy nebyl úspěšně spuštěn.
   * **Informace o** – důvod selhání.
   * **Bajty zpracovaných (MB)** – množství dat ze skupiny svazku, který byl zpracován (v MB). 
     
     ![Úlohy, které byly spuštěny za posledních 24 hodin](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 
3. K provedení další akce pro konkrétní úlohu, klikněte pravým tlačítkem na název úlohy v **výsledky** podokně a vyberte z možností v nabídce.
   
    ![Odstranit úlohu](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png)

## <a name="view-currently-running-jobs"></a>Zobrazit aktuálně spuštěné úlohy
Pomocí následujícího postupu do zobrazení úloh, které jsou aktuálně spuštěny.

#### <a name="to-view-currently-running-jobs"></a>Chcete-li zobrazit aktuálně spuštěné úlohy
1. Klepněte na ikonu klasické pracovní plochy spusťte StorSimple Snapshot Manageru.
2. V **oboru** podokně rozbalte **úlohy** uzel a klikněte na **systémem**. V závislosti na tom **zobrazení** možnosti můžete zadat následující informace se zobrazí v **výsledky** podokna:
   
   * **Název** – název plánovaný snímek.
   * **Spuštění** – datum a čas, kdy začal snímku.
   * **Kontrolní bod** – aktuální akce zálohování.
   * **Stav** – o procentuální hodnotě dokončení.
   * **Uplynulý** – množství času, který prošel od začátku zálohování. 
   * **Průměrná propustnost (MB)** – poměr celkový počet bajtů dat zpracovaných celkový čas potřebný ke zpracování (MB).
   * **Bajty zpracovaných (MB)** – celkový počet bajtů dat zpracovaných (v MB).
   * **(MB) zapsané bajty** – celkový počet bajtů dat (v MB). Obsahuje data, jakož i metadata a proto je obvykle větší než bajty zpracovaných.
     
     ![Aktuálně spuštěné úlohy](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)
3. K provedení další akce pro konkrétní úlohu, klikněte pravým tlačítkem na název úlohy v **výsledky** podokně a vyberte z možností v nabídce.

## <a name="next-steps"></a>Další postup
* Zjistěte, jak [použít ke správě vašeho řešení StorSimple Snapshot Manageru zařízení StorSimple](storsimple-snapshot-manager-admin.md).
* Zjistěte, jak [Správa katalogu záloh pomocí StorSimple Snapshot Manageru](storsimple-snapshot-manager-manage-backup-catalog.md).

