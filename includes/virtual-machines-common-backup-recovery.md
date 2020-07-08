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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "67175001"
---
## <a name="azure-backup"></a>Azure Backup

Pro zálohování virtuálních počítačů Azure s provozními úlohami použijte Azure Backup. Azure Backup podporuje zálohování konzistentní vzhledem k aplikacím pro virtuální počítače s Windows i Linux. Azure Backup vytváří body obnovení, které se ukládají v geograficky redundantních trezorech obnovení. Při obnovení z bodu obnovení můžete obnovit celý virtuální počítač nebo jenom určité soubory. 

Jednoduché praktické seznámení s Azure Backup pro virtuální počítače Azure najdete v kurzu zálohování virtuálních počítačů Azure pro [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) nebo [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md).

Další informace o tom, jak Azure Backup funguje, najdete v tématu [plánování infrastruktury zálohování virtuálních počítačů v Azure](../articles/backup/backup-azure-vms-introduction.md) .


## <a name="azure-site-recovery"></a>Azure Site Recovery

Azure Site Recovery chrání vaše virtuální počítače před velkým případem havárie, když dojde k výpadku v celé oblasti z důvodu významné přírodní havárie nebo většího přerušení služeb. Pro své virtuální počítače můžete nakonfigurovat Azure Site Recovery, abyste mohli aplikaci obnovit jediným kliknutím v několika minutách. Můžete replikovat do libovolné oblasti Azure podle vašeho výběru, není omezena na spárované oblasti. 

Můžete spouštět postupy zotavení po havárii s využitím testovacích převzetí služeb při selhání na vyžádání, aniž by to ovlivnilo vaše produkční úlohy nebo průběžnou replikaci. Vytvořte plány obnovení pro orchestraci převzetí služeb při selhání a navrácení služeb po obnovení celé aplikace spuštěné na více virtuálních počítačích. Funkce plánu obnovení je integrovaná s Runbooky Azure Automation.

Můžete začít [replikací virtuálních počítačů](https://aka.ms/a2a-getting-started). 

## <a name="managed-snapshots"></a>Spravované snímky 

Ve vývojových a testovacích prostředích nabízí snímky rychlou a jednoduchou možnost pro zálohování virtuálních počítačů, které používají Managed Disks. Spravovaný snímek je úplná kopie spravovaného disku, která je jen pro čtení. Snímky existují nezávisle na zdrojovém disku a lze je použít k vytvoření nových spravovaných disků pro opětovné sestavení virtuálního počítače. Účtují se na základě využité části disku. Pokud třeba vytvoříte snímek spravovaného disku s zřízenou kapacitou 64 GB a skutečná velikost využitých dat je 10 GB, bude se snímek účtovat jenom pro velikost využitých dat o velikosti 10 GB.  

Další informace o vytváření snímků najdete v těchto tématech:

* [Vytvoření kopie VHD uložené jako spravovaný disk pomocí snímků ve Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Vytvoření kopie VHD uložené jako spravovaný disk pomocí snímků v Linuxu](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)



## <a name="next-steps"></a>Další kroky
Můžete si vyzkoušet Azure Backup pomocí kurzu zálohování virtuálních počítačů s Windows pro [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) nebo [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md).
