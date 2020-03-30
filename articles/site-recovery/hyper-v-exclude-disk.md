---
title: Vyloučení disků virtuálních počítačů Hyper-V z obnovy po havárii do Azure pomocí Azure Site Recovery
description: Jak vyloučit disky virtuálních počítačů Hyper-V z replikace do Azure pomocí Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.topic: conceptual
ms.author: mayg
ms.date: 11/12/2019
ms.openlocfilehash: 50fb6da2905b2ae27547f25cce3d7a76ca7976b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75498133"
---
# <a name="exclude-disks-from-replication"></a>Vyloučení disků z replikace

Tento článek popisuje, jak vyloučit disky při replikaci virtuálních počítačů Hyper-V do Azure. Disky můžete z replikace vyloučit z několika důvodů:

- Ujistěte se, že nedůležitá data na vyloučeném disku nebudou replikována.
- Optimalizujte spotřebovanou šířku pásma replikace nebo prostředky na straně cíle vyloučením disků, které není nutné replikovat.
- Ušetřete úložiště a síťové prostředky tím, že nebudete replikovat data, která nepotřebujete.

Před vyloučením disků z replikace:

- [Přečtěte si další informace](exclude-disks-replication.md) o vyloučení disků.
- Zkontrolujte [typické scénáře](exclude-disks-replication.md#typical-scenarios) vyloučení a [příklady,](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) které ukazují, jak vyloučení disku ovlivňuje replikaci, převzetí služeb při selhání a navrácení služeb po selhání.

## <a name="before-you-start"></a>Než začnete

Než začnete, poznamenejte si následující:

- **Replikace**: Ve výchozím nastavení jsou replikovány všechny disky v počítači.
- **Typ disku**:
    - Z replikace můžete vyloučit základní disky.
    - Nemůžete vyloučit disky operačního systému.
    - Doporučujeme, abyste nevylučovali dynamické disky. Site Recovery nemůže určit, který virtuální pevný disk je základní nebo dynamický ve virtuálním virtuálním virtuálním ms hosta.  Pokud nevyloučíte všechny závislé disky dynamického svazku, stane se chráněný dynamický disk neúspěšným diskem na neúspěšném virtuálním počítači a data na tomto disku nebudou přístupná.
- **Přidat/odebrat/vyloučit disky**: Po povolení replikace nelze přidat/odebrat/vyloučit disky pro replikaci. Pokud chcete přidat nebo odebrat nebo vyloučit disk, je třeba zakázat ochranu virtuálního počítače a pak ji znovu povolit.
- **Převzetí služeb při selhání**: Po převzetí služeb při selhání, pokud převzetí služeb při selhání přes aplikace je nutné vyloučit disky, aby fungovaly, je třeba vytvořit tyto disky ručně. Případně můžete integrovat automatizaci Azure do plánu obnovení a vytvořit disk během převzetí služeb při selhání počítače.
- **Navrácení služeb po službě 14:** Při navrácení služeb po selhání na místní web po převzetí služeb při selhání, disky, které jste vytvořili ručně v Azure se nezdaří zpět. Například pokud převzetí služeb při selhání přes tři disky a vytvořit dva disky přímo na virtuálním počítači Azure, pouze tři disky, které byly převzetí služeb při selhání se pak nezdaří zpět. Nelze zahrnout disky, které byly vytvořeny ručně v navrácení služeb po selhání nebo v obrácené replikaci virtuálních počítačů.

## <a name="exclude-disks"></a>Vyloučení disků

1. Pokud chcete vyloučit disky, když [povolíte replikaci](site-recovery-hyper-v-site-to-azure.md) pro virtuální hod Hyper-V, zkontrolujte po výběru virtuálních počítačů, které chcete replikovat, na stránce Povolit**vlastnosti vlastností vlastností vlastností vlastností vlastností vlastností vlastností vlastností vlastností možnosti** >  **replikace,** > zkontrolujte sloupec **Disky k replikaci.** **Configure properties** Ve výchozím nastavení jsou pro replikaci vybrány všechny disky.
2. Pokud nechcete replikovat konkrétní disk, v **části Disky vymažte** výběr všech disků, které chcete vyloučit. 

    ![Vyloučení disků z replikace](./media/hyper-v-exclude-disk/enable-replication6-with-exclude-disk.png)


## <a name="next-steps"></a>Další kroky
Po nasazení a zprovoznění nasazení si můžete přečíst [další informace](failover-failback-overview.md) o různých typech převzetí služeb při selhání.
