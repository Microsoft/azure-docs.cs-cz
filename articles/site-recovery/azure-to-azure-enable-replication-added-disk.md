---
title: Povolení replikace pro přidaný disk Azure VM v Azure Site Recovery
description: Tento článek popisuje, jak povolit replikaci pro disk přidaný do virtuálního počítače Azure, který je povolený pro zotavení po havárii pomocí Azure Site Recovery
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/29/2019
ms.openlocfilehash: 6cbbe63d7968816de78256f5a8408517bb8da278
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "75973798"
---
# <a name="enable-replication-for-a-disk-added-to-an-azure-vm"></a>Povolení replikace pro disk přidaný do virtuálního počítače Azure


Tento článek popisuje, jak povolit replikaci datových disků přidaných do virtuálního počítače Azure, který je už povolený pro zotavení po havárii do jiné oblasti Azure, pomocí [Azure Site Recovery](site-recovery-overview.md).

Povolení replikace pro disk, který přidáte do virtuálního počítače, se podporuje pro virtuální počítače Azure se spravovanými disky.

Když přidáte nový disk do virtuálního počítače Azure, který se replikuje do jiné oblasti Azure, dojde k následujícímu:

-   Stav replikace pro virtuální počítač zobrazuje upozornění a Poznámka na portálu informuje o tom, že je k dispozici jeden nebo více disků pro ochranu.
-   Pokud povolíte ochranu pro přidané disky, bude upozornění po počáteční replikaci disku zmizí.
-   Pokud se rozhodnete Nepovolit replikaci pro disk, můžete vybrat možnost Zavřít upozornění.

![Byl přidán nový disk.](./media/azure-to-azure-enable-replication-added-disk/newdisk.png)



## <a name="before-you-start"></a>Než začnete

V tomto článku se předpokládá, že jste už nastavili zotavení po havárii pro virtuální počítač, do kterého disk přidáváte. Pokud jste to neudělali, postupujte podle [kurzu Azure to Azure pro zotavení po havárii](azure-to-azure-tutorial-enable-replication.md).

## <a name="enable-replication-for-an-added-disk"></a>Povolení replikace pro přidaný disk

Pokud chcete povolit replikaci pro přidaný disk, udělejte toto:

1. V trezoru > **replikované položky** klikněte na virtuální počítač, ke kterému jste přidali disk.
2. Klikněte na **disky** a potom vyberte datový disk, pro který chcete povolit replikaci (tyto disky mají stav **Nechráněno** ).
3.  V **podrobnostech o disku** klikněte na **Povolit replikaci**.

    ![Povolit replikaci pro přidaný disk](./media/azure-to-azure-enable-replication-added-disk/enabled-added.png)

Po spuštění úlohy povolit replikaci a dokončení počáteční replikace dojde k odebrání upozornění na stav replikace pro problém disku.



## <a name="next-steps"></a>Další kroky

[Přečtěte si další informace](site-recovery-test-failover-to-azure.md) o spuštění testovacího převzetí služeb při selhání.
