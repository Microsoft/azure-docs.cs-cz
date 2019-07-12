---
title: Povolení replikace pro disk přidat do virtuálního počítače Azure replikované pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek popisuje, jak povolit replikaci pro disk přidat do virtuálního počítače Azure, který je povolený pro zotavení po havárii pomocí Azure Site Recovery
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: asgang
ms.openlocfilehash: 4a262a3a0c32516988890a6afc6eef34d8655c89
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671876"
---
# <a name="enable-replication-for-a-disk-added-to-an-azure-vm"></a>Povolení replikace pro disk přidat do virtuálního počítače Azure


Tento článek popisuje, jak povolit replikaci pro datové disky, které jsou přidány do virtuálního počítače Azure, který je již povolen pro zotavení po havárii do jiné oblasti Azure, pomocí [Azure Site Recovery](site-recovery-overview.md).

Povolení replikace pro disk, které přidáte do virtuálního počítače je podporováno pro virtuální počítače Azure se spravovanými disky.

Když přidáte nový disk k virtuálnímu počítači Azure, které je replikovat do jiné oblasti Azure, dojde k následujícímu:

-   Stav replikace pro virtuální počítač se zobrazí upozornění a poznámky na portálu pro vás informuje, že jedna nebo více disků jsou k dispozici pro ochranu.
-   Pokud povolíte ochranu pro přidané disky, upozornění zmizí po počáteční replikaci disku.
-   Pokud se rozhodnete povolit replikaci disku, můžete vybrat zavřete upozornění.

![Přidat nový disk](./media/azure-to-azure-enable-replication-added-disk/newdisk.png)



## <a name="before-you-start"></a>Než začnete

Tento článek předpokládá, že jste již nastavili zotavení po havárii pro virtuální počítač, na který přidáváte na disku. Pokud jste tak dosud, postupujte [kurz pro zotavení po havárii Azure do Azure](azure-to-azure-tutorial-enable-replication.md). 

## <a name="enable-replication-for-an-added-disk"></a>Povolení replikace pro přidaný disk 

Povolíte replikaci pro přidání disku, postupujte takto:

1. V trezoru > **replikované položky**, klikněte na virtuální počítač, ke které jste přidali na disku.
2. Klikněte na tlačítko **disky**a pak vyberte datový disk, pro které chcete povolit replikaci (tyto disky mají **nechrání** stav).
3.  V **podrobných informací o discích**, klikněte na tlačítko **povolit replikaci**.

    ![Povolení replikace pro přidání disku](./media/azure-to-azure-enable-replication-added-disk/enabled-added.png)

Po spuštění úlohy povolit replikaci a dokončení počáteční replikace, upozornění stavu replikace pro problém, disk se odebere.



# <a name="next-steps"></a>Další postup

[Další informace](site-recovery-test-failover-to-azure.md) o spuštění testovací převzetí služeb při selhání.
