---
title: Vyloučení disků virtuálních počítačů Hyper-V z zotavení po havárii do Azure pomocí Azure Site Recovery
description: Jak vyloučit disky virtuálních počítačů Hyper-V z replikace do Azure pomocí Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.topic: conceptual
ms.author: mayg
ms.date: 11/12/2019
ms.openlocfilehash: 373cffe96119af4a2fc0d74e2090e6cc24dcaf8f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86131246"
---
# <a name="exclude-disks-from-replication"></a>Vyloučení disků z replikace

Tento článek popisuje, jak vyloučit disky při replikaci virtuálních počítačů Hyper-V do Azure. Disky můžete chtít z replikace vyloučit z několika důvodů:

- Ujistěte se, že se nereplikují neimportovaná data na vyloučeném disku.
- Optimalizujte spotřebované šířky pásma replikace nebo prostředky na cílové straně vyloučením disků, které nepotřebujete replikovat.
- Uložte úložiště a síťové prostředky tím, že se nereplikují data, která nepotřebujete.

Než vyloučíte disky z replikace:

- [Přečtěte si další informace](exclude-disks-replication.md) o vyloučení disků.
- Přečtěte si o [typických scénářích vyloučení](exclude-disks-replication.md#typical-scenarios) a [Příklady](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) , které ukazují, jak disk vylučuje replikaci, převzetí služeb při selhání a obnovení

## <a name="before-you-start"></a>Než začnete

Než začnete, vezměte na vědomí následující:

- **Replikace**: ve výchozím nastavení se replikují všechny disky na počítači.
- **Typ disku**:
    - Z replikace můžete vyloučit základní disky.
    - Nemůžete vyloučit disky operačního systému.
    - Doporučujeme, abyste nevylučovali dynamické disky. Site Recovery nemůže zjistit, který virtuální pevný disk je na virtuálním počítači hosta základní nebo dynamický.  Pokud neodeberete všechny závislé disky s dynamickými svazky, bude chráněný dynamický disk na virtuálním počítači, u kterého došlo k převzetí služeb při selhání, a data na tomto disku nebudou přístupná.
- **Přidat/odebrat/vyloučit disky**: po povolení replikace nemůžete přidat/odebrat/vyloučit disky pro replikaci. Pokud chcete přidat nebo odebrat nebo vyloučit disk, budete muset zakázat ochranu virtuálního počítače a pak ho znovu povolit.
- **Převzetí služeb při** selhání: Pokud aplikace po převzetí služeb při selhání v aplikaci potřebují vyloučit disky, musíte tyto disky vytvořit ručně. Případně můžete integrovat Azure Automation do plánu obnovení a vytvořit disk během převzetí služeb při selhání počítače.
- **Navrácení služeb po obnovení**: po navrácení služeb po obnovení do místní lokality po převzetí služeb při selhání se nezdařilo navrátit disky, které jste vytvořili ručně v Azure. Pokud například při selhání převezmete tři disky a vytvoříte dva disky přímo na virtuálním počítači Azure, navrátí se po obnovení jenom tři disky, u kterých došlo k převzetí služeb při selhání. Ručně vytvořené disky nemůžete zahrnout do navrácení služeb po obnovení ani do reverzní replikace virtuálních počítačů.

## <a name="exclude-disks"></a>Vyloučení disků

1. Pokud chcete vyloučit disky při [Povolení replikace](./hyper-v-azure-tutorial.md) pro virtuální počítač Hyper-V, pak po výběru virtuálních počítačů, které chcete replikovat, na stránce **Povolit**  >  **vlastnosti** replikace  >  **Konfigurovat vlastnosti** zkontrolujte sloupec **disky, které chcete replikovat** . Ve výchozím nastavení jsou pro replikaci vybrány všechny disky.
2. Pokud nechcete replikovat určitý disk, na **discích pro replikaci** zrušte výběr pro všechny disky, které chcete vyloučit. 

    ![Vyloučení disků z replikace](./media/hyper-v-exclude-disk/enable-replication6-with-exclude-disk.png)


## <a name="next-steps"></a>Další kroky
Po nasazení a zprovoznění nasazení si můžete přečíst [další informace](failover-failback-overview.md) o různých typech převzetí služeb při selhání.
