---
title: Úlohy zálohování Správce snímků StorSimple | Dokumenty společnosti Microsoft
description: Popisuje použití modulu snap-in Správce snímků StorSimple MMC k zobrazení a správě naplánovaných, aktuálně spuštěných a dokončených úloh zálohování.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: bf4dcff6-c819-4766-b9d9-9922831cb200
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: 094b996cd3227903995c7a74ef14ed8c0561f59d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933338"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-backup-jobs"></a>Použití Správce snímků StorSimple k zobrazení a správě úloh zálohování

## <a name="overview"></a>Přehled
Uzel **Úlohy** v podokně **Obor** zobrazuje **naplánované**, **posledních 24 hodin**a **spuštění** úloh zálohování, které jste inicializovat lichválně nebo nakonfigurovanou zásadou. 

Tento kurz vysvětluje, jak můžete použít uzel **Úlohy** k zobrazení informací o naplánovaných, posledních a aktuálně spuštěných úlohách zálohování. (Seznam úloh a odpovídající informace se zobrazí v podokně **Výsledky.)** Kromě toho můžete klepnout pravým tlačítkem myši na uvedenou úlohu a zobrazit místní nabídku s dostupnými akcemi.

## <a name="view-scheduled-jobs"></a>Zobrazit naplánované úlohy
K zobrazení naplánovaných úloh zálohování použijte následující postup.

#### <a name="to-view-scheduled-jobs"></a>Zobrazení naplánovaných úloh
1. Kliknutím na ikonu na ploše spusťte Správce snímků StorSimple. 
2. V podokně **Obor** rozbalte uzel **Úlohy** a klepněte na **položku Naplánované**. V podokně **Výsledky** se zobrazí následující informace:
   
   * **Název** – název naplánovaného snímku
   * **Další spuštění** – datum a čas dalšího naplánovaného snímku
   * **Poslední spuštění** – datum a čas posledního naplánovaného snímku
     
     > [!NOTE]
     > Pro jednorázové pouze snímky **bude další spuštění** a poslední **spuštění** stejné.
     
     ![Plánované úlohy zálohování](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png) 
3. Chcete-li provést další akce u určité úlohy, klepněte pravým tlačítkem myši na název **úlohy** v podokně Výsledky a vyberte z možností nabídky.

## <a name="view-recent-jobs"></a>Zobrazit poslední úlohy
Pomocí následujícího postupu můžete zobrazit úlohy zálohování a obnovení, které byly dokončeny za posledních 24 hodin.

#### <a name="to-view-recent-jobs"></a>Zobrazení posledních úloh
1. Kliknutím na ikonu na ploše spusťte Správce snímků StorSimple.
2. V podokně **Obor** rozbalte uzel **Úlohy** a klepněte na **tlačítko Posledních 24 hodin**. Podokno **Výsledky** zobrazuje úlohy zálohování za posledních 24 hodin (maximálně 64 úloh). V podokně Výsledky se v závislosti na zadaných možnostech **zobrazení** zobrazí v podokně **Výsledky** následující informace:
   
   * **Název** – název naplánovaného snímku.
   * **Spuštěno** – datum a čas, kdy snímek začal.
   * **Zastaveno** – datum a čas, kdy byl snímek dokončen nebo byl ukončen.
   * **Uplynulá** – doba mezi **časy Spuštěno** a **Zastaveno.**
   * **Stav** – stav nedávno dokončené úlohy. **Úspěch** označuje, že záloha byla úspěšně vytvořena. **Se nezdařilo,** že úloha nebyla úspěšně spuštěna.
   * **Informace** – důvod selhání.
   * **Počet zpracovaných bajtů (MB)** – množství dat ze skupiny svazků, která byla zpracována (v MB). 
     
     ![Úlohy, které běžely za posledních 24 hodin](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png) 
3. Chcete-li provést další akce u určité úlohy, klepněte pravým tlačítkem myši na název **úlohy** v podokně Výsledky a vyberte z možností nabídky.
   
    ![Odstranění úlohy](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png)

## <a name="view-currently-running-jobs"></a>Zobrazit aktuálně spuštěné úlohy
Následující postup slouží k zobrazení úloh, které jsou aktuálně spuštěny.

#### <a name="to-view-currently-running-jobs"></a>Zobrazení aktuálně spuštěné úlohy
1. Kliknutím na ikonu na ploše spusťte Správce snímků StorSimple.
2. V podokně **Obor** rozbalte uzel **Úlohy** a klepněte na **tlačítko Spuštěno**. V závislosti na zadaných možnostech **zobrazení** se v podokně **Výsledky** zobrazí následující informace:
   
   * **Název** – název naplánovaného snímku.
   * **Spuštěno** – datum a čas, kdy snímek začal.
   * **Kontrolní bod** – aktuální akce zálohy.
   * **Stav** – procento dokončení.
   * **Uplynulý** – množství času, který uplynul od začátku zálohování. 
   * **Průměrná propustnost (MB)** – poměr celkových bajtů zpracovávaných dat k celkovédobě času zpracování (MB).
   * **Zpracovali jste (MB)** ( celkový počet bajtů zpracovávaných dat (v MB).
   * **Zápis bajtů (MB)** – celkový počet zapsaných dat (v MB). Obsahuje data, stejně jako metadata a proto je obvykle větší než bytes zpracované.
     
     ![Aktuálně spuštěné úlohy](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)
3. Chcete-li provést další akce u určité úlohy, klepněte pravým tlačítkem myši na název **úlohy** v podokně Výsledky a vyberte z možností nabídky.

## <a name="next-steps"></a>Další kroky
* Naučte se [používat Správce snímků StorSimple ke správě řešení StorSimple](storsimple-snapshot-manager-admin.md).
* Naučte se [používat Správce snímků StorSimple ke správě katalogu záloh](storsimple-snapshot-manager-manage-backup-catalog.md).

