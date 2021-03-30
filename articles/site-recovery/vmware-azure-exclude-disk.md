---
title: Vyloučení disků virtuálních počítačů VMware z zotavení po havárii do Azure pomocí Azure Site Recovery
description: Postup vyloučení disků virtuálních počítačů VMware z replikace do Azure pomocí Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.date: 12/10/2019
ms.author: mayg
ms.topic: conceptual
ms.openlocfilehash: c4842172ff181b5cdbe7f6fecf69da8755ae43fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86129874"
---
# <a name="exclude-disks-from-vmware-vm-replication-to-azure"></a>Vyloučení disků z replikace virtuálních počítačů VMware do Azure

Tento článek popisuje, jak vyloučit disky při replikaci virtuálních počítačů VMware do Azure pro zotavení po havárii. Disky můžete chtít z replikace vyloučit z několika důvodů:

- Ujistěte se, že se nereplikují neimportovaná data na vyloučeném disku.
- Optimalizujte spotřebované šířky pásma replikace nebo prostředky na cílové straně vyloučením disků, které nepotřebujete replikovat.
- Uložte úložiště a síťové prostředky tím, že se nereplikují data, která nepotřebujete.

Než vyloučíte disky z replikace:

- [Přečtěte si další informace](exclude-disks-replication.md) o vyloučení disků.
- Přečtěte si o [typických scénářích vyloučení](exclude-disks-replication.md#typical-scenarios) a [Příklady](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) , které ukazují, jak disk vylučuje replikaci, převzetí služeb při selhání a obnovení

## <a name="before-you-start"></a>Než začnete

 Než začnete, vezměte na vědomí následující:

- **Replikace**: ve výchozím nastavení se replikují všechny disky na počítači.
- **Typ disku**: z replikace je možné vyloučit jenom základní disky. Nemůžete vyloučit disk operačního systému ani dynamické disky.
- **Služba mobility**: Pokud chcete vyloučit disk z replikace, musíte na počítač ručně nainstalovat službu mobility, než povolíte replikaci. Nabízenou instalaci nemůžete použít, protože tato metoda nainstaluje službu mobility na virtuální počítač až po povolení replikace.  
- **Přidat/odebrat/vyloučit disky**: po povolení replikace nemůžete přidat/odebrat/vyloučit disky pro replikaci. Pokud chcete přidat nebo odebrat nebo vyloučit disky, musíte zakázat ochranu počítače a pak ho znovu povolit.
- **Převzetí služeb při** selhání: Pokud aplikace po převzetí služeb při selhání potřebují k práci, je potřeba tyto disky vytvořit ručně. Případně můžete integrovat Azure Automation do plánu obnovení a vytvořit disk během převzetí služeb při selhání počítače.
- **Navrácení služeb po obnovení – Windows**: po navrácení služeb po obnovení do místní lokality po převzetí služeb při selhání se nezdařily disky se systémem Windows, které vytvoříte ručně v Azure. Pokud například při selhání převezmete tři disky a vytvoříte dva disky přímo na virtuálních počítačích Azure, navrátí se zpět pouze ty tři disky, u kterých došlo k převzetí služeb při selhání.
- **Navrácení služeb po obnovení – Linux**: pro navrácení služeb po obnovení z počítačů se systémem Linux se vrátí ručně vytvořené disky v Azure. Pokud například při selhání převezmete tři disky a vytvoříte dva disky přímo na virtuálních počítačích Azure, dojde k selhání všech pěti. Ručně vytvořené disky nemůžete vyloučit v navrácení služeb po obnovení nebo v rámci ochrany virtuálních počítačů.



## <a name="exclude-disks-from-replication"></a>Vyloučení disků z replikace

1. Pokud [povolíte replikaci](./hyper-v-azure-tutorial.md) pro virtuální počítač VMware, po výběru virtuálních počítačů, které chcete replikovat, na stránce **Povolit**  >  **vlastnosti** replikace  >  **Konfigurovat vlastnosti** zkontrolujte sloupec disky, které chcete **replikovat** . Ve výchozím nastavení jsou pro replikaci vybrány všechny disky.
2. Pokud nechcete replikovat určitý disk, na **discích pro replikaci** zrušte výběr pro všechny disky, které chcete vyloučit. 

    ![Vyloučení disků z replikace](./media/vmware-azure-exclude-disk/enable-replication-exclude-disk1.png)



## <a name="next-steps"></a>Další kroky
Po nasazení a zprovoznění nasazení si můžete přečíst [další informace](failover-failback-overview.md) o různých typech převzetí služeb při selhání.
