---
title: Přehled možností zálohování virtuálních počítačů
description: Přehled možností zálohování virtuálních počítačů Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: recovery
ms.topic: conceptual
ms.date: 8/03/2020
ms.author: cynthn
ms.openlocfilehash: c4116ef8d02bd47d6375371be9381553f8c22eda
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102565440"
---
# <a name="backup-and-restore-options-for-virtual-machines-in-azure"></a>Možnosti zálohování a obnovení pro virtuální počítače v Azure

Svá data můžete chránit prováděním záloh v pravidelných intervalech. V závislosti na vašem případu použití je pro virtuální počítače k dispozici několik možností zálohování.

## <a name="azure-backup"></a>Azure Backup

Pro zálohování virtuálních počítačů Azure s provozními úlohami použijte Azure Backup. Azure Backup podporuje zálohování konzistentní vzhledem k aplikacím pro virtuální počítače s Windows i Linux. Azure Backup vytváří body obnovení, které se ukládají v geograficky redundantních trezorech obnovení. Při obnovení z bodu obnovení můžete obnovit celý virtuální počítač nebo jenom určité soubory. 

Jednoduché praktické seznámení s Azure Backup pro virtuální počítače Azure najdete v části [rychlý start Azure Backup](../backup/quick-backup-vm-portal.md).

Další informace o tom, jak Azure Backup funguje, najdete v tématu [plánování infrastruktury zálohování virtuálních počítačů v Azure](../backup/backup-azure-vms-introduction.md) .


## <a name="azure-site-recovery"></a>Azure Site Recovery

Azure Site Recovery chrání vaše virtuální počítače před velkým případem havárie, když dojde k výpadku v celé oblasti z důvodu významné přírodní havárie nebo většího přerušení služeb. Pro své virtuální počítače můžete nakonfigurovat Azure Site Recovery, abyste mohli aplikaci obnovit jediným kliknutím v několika minutách. Můžete replikovat do libovolné oblasti Azure podle vašeho výběru, není omezena na spárované oblasti. 

Můžete spouštět postupy zotavení po havárii s využitím testovacích převzetí služeb při selhání na vyžádání, aniž by to ovlivnilo vaše produkční úlohy nebo průběžnou replikaci. Vytvořte plány obnovení pro orchestraci převzetí služeb při selhání a navrácení služeb po obnovení celé aplikace spuštěné na více virtuálních počítačích. Funkce plánu obnovení je integrovaná s Runbooky Azure Automation.

Můžete začít [replikací virtuálních počítačů](../site-recovery/azure-to-azure-quickstart.md). 

## <a name="managed-snapshots"></a>Spravované snímky 

Ve vývojových a testovacích prostředích nabízí snímky rychlou a jednoduchou možnost pro zálohování virtuálních počítačů, které používají Managed Disks. Spravovaný snímek je úplná kopie spravovaného disku, která je jen pro čtení. Snímky existují nezávisle na zdrojovém disku a lze je použít k vytvoření nových spravovaných disků pro opětovné sestavení virtuálního počítače. Účtují se na základě využité části disku. Pokud třeba vytvoříte snímek spravovaného disku s zřízenou kapacitou 64 GB a skutečná velikost využitých dat je 10 GB, bude se snímek účtovat jenom pro velikost využitých dat o velikosti 10 GB.  

Další informace o vytváření snímků najdete v těchto tématech:

* [Vytvoření kopie VHD uložené jako spravovaný disk pomocí snímků ve Windows](./windows/snapshot-copy-managed-disk.md)
* [Vytvoření kopie VHD uložené jako spravovaný disk pomocí snímků v Linuxu](./linux/snapshot-copy-managed-disk.md)



## <a name="next-steps"></a>Další kroky
Azure Backup můžete vyzkoušet pomocí [Azure Backup rychlý Start](../backup/quick-backup-vm-portal.md).