---
author: v-amallick
ms.service: Azure Backup
ms.topic: include
ms.date: 04/16/2021
ms.author: v-amallick
ms.openlocfilehash: e7af0819a94661a1008d96b7d0738c30a4a80c18
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107716737"
---
1. Pro virtuální počítače Azure, které jsou vybrané pro zálohování, Azure Backup spustí úlohu zálohování podle zadaného plánu zálohování.
1. Při prvním zálohování se na virtuálním počítači nainstaluje rozšíření zálohování, pokud je virtuální počítač spuštěný.
    - Pro virtuální počítače s Windows se nainstaluje [rozšíření VMSnapshot](../articles/virtual-machines/extensions/vmsnapshot-windows.md) .
    - Pro virtuální počítače se systémem Linux se nainstaluje [rozšíření VMSnapshotLinux](../articles/virtual-machines/extensions/vmsnapshot-linux.md) .
1. Pro virtuální počítače s Windows, na kterých běží, se zaregistrují služby Windows služba Stínová kopie svazku (VSS), aby se snímek virtuálního počítače konzistentní vzhledem k aplikacím vybral.
    - Ve výchozím nastavení provádí zálohování úplné zálohy VSS.
    - Pokud zálohování nemůže pořídit snímek konzistentní vzhledem k aplikacím, pak bude mít snímek konzistentní se souborem základního úložiště (protože při zastavení virtuálního počítače nedochází k žádným zápisům aplikací).
1. Pro virtuální počítače se systémem Linux aplikace Backup provede zálohu konzistentní se souborem. U snímků konzistentních vzhledem k aplikacím je nutné ručně přizpůsobit skripty před/po.
1. Po zálohování získá snímek data do trezoru.
    - Zálohování se optimalizuje tak, že se všechny disky virtuálních počítačů zálohují paralelně.
    - Služba Azure Backup přečte u každého zálohovaného disku bloky na disku a identifikuje a přenese pouze bloky dat, které se od předchozího zálohování změnily (rozdíl).
    - Data snímku se nemusí do trezoru zkopírovat okamžitě. Může to trvat několik hodin v době špičky. Celková doba zálohování virtuálního počítače bude u zásad denního zálohování menší než 24 hodin.
1. Změny provedené na virtuálním počítači s Windows po Azure Backup jsou zapnuté:
    - Microsoft Visual C++ 2013 Redistributable (x64) – na virtuálním počítači je nainstalovaná 12.0.40660.
    - Typ spuštění služby Stínová kopie svazku (VSS) se změnil na automatické z ručního.
    - Přidaná služba systému Windows IaaSVmProvider

1. Po dokončení přenosu dat se snímek odebere a vytvoří se bod obnovení.

![Architektura zálohování virtuálních počítačů Azure](../articles/backup/media/backup-azure-vms-introduction/vmbackup-architecture.png)
