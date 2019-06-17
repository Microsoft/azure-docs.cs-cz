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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66160050"
---
## <a name="azure-backup"></a>Azure Backup

Pro zálohování virtuálních počítačů Azure, spouštění úloh v produkčním prostředí, použijte Azure Backup. Azure Backup podporuje zálohy konzistentní s aplikací pro Windows a virtuální počítače s Linuxem. Azure Backup vytváří body obnovení, které se ukládají v geograficky redundantních trezorech obnovení. Při obnovení z bodu obnovení můžete obnovit celý virtuální počítač nebo jenom určité soubory. 

Jednoduché, praktické Úvod do služby Azure Backup pro virtuální počítače Azure, najdete v kurzu "zálohování Azure virtual machines" pro [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) nebo [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md).

Další informace o tom, jak Azure Backup funguje, najdete v části [plánování infrastruktury zálohování virtuálních počítačů v Azure](../articles/backup/backup-azure-vms-introduction.md)


## <a name="azure-site-recovery"></a>Azure Site Recovery

Azure Site Recovery chrání vaše virtuální počítače v případě větší havárie, pokud celé oblasti dojde k výpadku z důvodu větší přírodní havárie nebo přerušení rozšířených služby. Azure Site Recovery pro virtuální počítače můžete nakonfigurovat tak, aby mohli obnovit aplikaci s jedním kliknutím v řádu minut. Můžete replikovat do oblasti Azure podle vašeho výběru, není omezen na spárovaných oblastí. 

Nácvik zotavení po havárii můžete spustit s převzetí služeb při selhání testů na vyžádání, aniž to ovlivní vaše produkční úlohy nebo probíhající replikaci. Vytvořte plány obnovení pro orchestraci převzetí služeb při selhání a navrácení služeb po obnovení z celé aplikace na několika virtuálních počítačích. Funkce plánu obnovení je integrovaná s runbooky služby Azure automation.

Můžete začít tak [replikaci virtuálních počítačů](https://aka.ms/a2a-getting-started). 

## <a name="managed-snapshots"></a>Spravované snímky 

Snímky v vývojová a testovací prostředí poskytují rychlé a jednoduché možnosti pro zálohování virtuálních počítačů, které používají spravované disky. Spravovaný snímek je jen pro čtení úplná kopie spravovaného disku. Snímky existují nezávisle na zdrojový disk a slouží k vytvoření nové spravované disky pro virtuální počítač znovu sestavit. Se účtují na základě využité části disku. Pokud například vytvoříte snímek spravovaného disku se zřízenou kapacitou 64 GB a aktuální využitou velikostí dat 10 GB, za tento snímek se bude účtovat jenom využitá velikost dat, tedy 10 GB.  

Další informace o vytváření snímků najdete v článku:

* [Vytvoření kopie VHD uložené jako spravovaný disk pomocí snímků ve Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Vytvoření kopie VHD uložené jako spravovaný disk pomocí snímků v Linuxu](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)



## <a name="next-steps"></a>Další postup
Budete moct vyzkoušet Azure Backup pomocí následujících "Zálohování Windows virtual machines kurz" pro [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) nebo [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md).
