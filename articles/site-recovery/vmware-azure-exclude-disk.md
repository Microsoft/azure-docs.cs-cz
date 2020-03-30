---
title: Vyloučení disků virtuálních počítačů VMware z obnovy po havárii do Azure pomocí Azure Site Recovery
description: Jak vyloučit virtuální počítače VMware disky z replikace do Azure pomocí Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.date: 12/10/2019
ms.author: mayg
ms.topic: conceptual
ms.openlocfilehash: cd54da5ee01206e576157435135065189bfb8035
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495369"
---
# <a name="exclude-disks-from-vmware-vm-replication-to-azure"></a>Vyloučení disků z replikace virtuálních počítačů VMware do Azure

Tento článek popisuje, jak vyloučit disky při replikaci virtuálních počítačů VMware do Azure pro zotavení po havárii. Disky můžete z replikace vyloučit z několika důvodů:

- Ujistěte se, že nedůležitá data na vyloučeném disku nebudou replikována.
- Optimalizujte spotřebovanou šířku pásma replikace nebo prostředky na straně cíle vyloučením disků, které není nutné replikovat.
- Ušetřete úložiště a síťové prostředky tím, že nebudete replikovat data, která nepotřebujete.

Před vyloučením disků z replikace:

- [Přečtěte si další informace](exclude-disks-replication.md) o vyloučení disků.
- Zkontrolujte [typické scénáře](exclude-disks-replication.md#typical-scenarios) vyloučení a [příklady,](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) které ukazují, jak vyloučení disku ovlivňuje replikaci, převzetí služeb při selhání a navrácení služeb po selhání.

## <a name="before-you-start"></a>Než začnete

 Než začnete, poznamenejte si následující:

- **Replikace**: Ve výchozím nastavení jsou replikovány všechny disky v počítači.
- **Typ disku**: Z replikace lze vyloučit pouze základní disky. Nemůžete vyloučit disk operačního systému ani dynamické disky.
- **Služba mobility**: Chcete-li vyloučit disk z replikace, je nutné ručně nainstalovat službu Mobility do počítače před povolením replikace. Nabízenou instalaci nelze použít, protože tato metoda nainstaluje službu Mobility na virtuální počítač až po povolení replikace.  
- **Přidat/odebrat/vyloučit disky**: Po povolení replikace nelze přidat/odebrat/vyloučit disky pro replikaci. Chcete-li přidat nebo odebrat nebo vyloučit disky, je třeba zakázat ochranu počítače a poté ji znovu povolit.
- **Převzetí služeb při selhání**: Po převzetí služeb při selhání, pokud převzetí služeb při selhání přes aplikace potřebují vyloučené disky, aby fungovaly, je třeba vytvořit tyto disky ručně. Případně můžete integrovat automatizaci Azure do plánu obnovení a vytvořit disk během převzetí služeb při selhání počítače.
- **Obnovení služeb při selhání Windows**: Když po převzetí služeb při selhání zpět na místní web po převzetí služeb při selhání, disky se systémem Windows, které vytvoříte ručně v Azure se nezdaří zpět. Pokud například převezmete služby při selhání tři disky a vytvoříte dva disky přímo na virtuálních počítačích Azure, budou se vrátit zpět pouze tři disky, které byly převzetí služeb při selhání.
- **Fail-Linux**: Pro navrácení služeb po obnovení počítačů s Linuxem disky, které vytvoříte ručně v Azure se nezdařilo zpět. Například pokud převzetí služeb při selhání přes tři disky a vytvořit dva disky přímo na virtuálních počítačích Azure, všech pět se nezdaří zpět. Nelze vyloučit disky, které byly vytvořeny ručně v navrácení služeb po obnovení nebo v opětovné ochraně virtuálních počítačů.



## <a name="exclude-disks-from-replication"></a>Vyloučení disků z replikace

1. Když [povolíte replikaci](site-recovery-hyper-v-site-to-azure.md) pro virtuální hospodařící vsystému VMware, zkontrolujte po výběru virtuálních počítačů, které chcete replikovat, na stránce Povolit**vlastnosti vlastností vlastností vlastností vlastností vlastností vlastností vlastností vlastností** > **Configure properties** **replikace,** > zkontrolujte sloupec **Disky k replikaci.** Ve výchozím nastavení jsou pro replikaci vybrány všechny disky.
2. Pokud nechcete replikovat konkrétní disk, v **části Disky vymažte** výběr všech disků, které chcete vyloučit. 

    ![Vyloučení disků z replikace](./media/vmware-azure-exclude-disk/enable-replication-exclude-disk1.png)



## <a name="next-steps"></a>Další kroky
Po nasazení a zprovoznění nasazení si můžete přečíst [další informace](failover-failback-overview.md) o různých typech převzetí služeb při selhání.
