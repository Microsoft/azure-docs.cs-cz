---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: be71b269e618d13a126e4005754b307e9c6517d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175001"
---
## <a name="azure-backup"></a>Azure Backup

Pro zálohování virtuálních počítačích Azure s produkčními úlohami použijte Azure Backup. Azure Backup podporuje zálohy konzistentní s aplikacemi pro virtuální počítače s Windows i Linux. Azure Backup vytváří body obnovení, které se ukládají v geograficky redundantních trezorech obnovení. Při obnovení z bodu obnovení můžete obnovit celý virtuální počítač nebo jenom určité soubory. 

Jednoduchý praktický úvod k virtuálním počítačům Azure pro Azure najdete v kurzu "Zálohování virtuálních počítačů Azure" pro [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) nebo [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md).

Další informace o tom, jak funguje Azure Backup, najdete [v tématu Plánování infrastruktury zálohování virtuálních počítačích v Azure.](../articles/backup/backup-azure-vms-introduction.md)


## <a name="azure-site-recovery"></a>Azure Site Recovery

Azure Site Recovery chrání vaše virtuální počítače před scénářem závažné katastrofy, když celá oblast dojde k výpadku z důvodu velké přírodní katastrofy nebo rozsáhlé přerušení služby. Azure Site Recovery můžete nakonfigurovat pro vaše virtuální počítače, takže můžete obnovit aplikaci jediným kliknutím během několika minut. Můžete replikovat do oblasti Azure podle vašeho výběru, není omezena na spárované oblasti. 

Můžete spustit cvičení pro zotavení po havárii s převzetím služeb při selhání na vyžádání, aniž by to ovlivnilo vaše produkční úlohy nebo probíhající replikaci. Vytvořte plány obnovení pro orchestraci převzetí služeb při selhání a navrácení služeb po obnovení celé aplikace spuštěné na více virtuálních počítačích. Funkce plánu obnovení je integrovaná s runbooky azure automatizace.

Můžete začít [replikací virtuálních počítačů](https://aka.ms/a2a-getting-started). 

## <a name="managed-snapshots"></a>Spravované snímky 

Ve vývojových a testovacích prostředích poskytují snímky rychlou a jednoduchou možnost zálohování virtuálních počítačů, které používají spravované disky. Spravovaný snímek je úplná kopie spravovaného disku jen pro čtení. Snímky existují nezávisle na zdrojovém disku a lze je použít k vytvoření nových spravovaných disků pro opětovné sestavení virtuálního počítače. Fakturují se na základě použité části disku. Pokud například vytvoříte snímek spravovaného disku se zřízenou kapacitou 64 GB a skutečně použitou velikostí dat 10 GB, bude snímek účtován pouze pro použitou velikost dat 10 GB.  

Další informace o vytváření snímků naleznete v tématu:

* [Vytvoření kopie VHD uložené jako spravovaný disk pomocí snímků ve Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Vytvoření kopie VHD uložené jako spravovaný disk pomocí snímků v Linuxu](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)



## <a name="next-steps"></a>Další kroky
Azure Backup můžete vyzkoušet podle pokynů k kurzu "Zálohování virtuálních počítačů windows" pro [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) nebo [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md).
