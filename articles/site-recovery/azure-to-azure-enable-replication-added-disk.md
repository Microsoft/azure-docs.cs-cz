---
title: Povolení replikace pro přidaný disk virtuálního počítače Azure v azure site recovery
description: Tento článek popisuje, jak povolit replikaci pro disk přidaný k virtuálnímu počítači Azure, který je povolený pro zotavení po havárii pomocí azure site recovery
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/29/2019
ms.openlocfilehash: 6cbbe63d7968816de78256f5a8408517bb8da278
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973798"
---
# <a name="enable-replication-for-a-disk-added-to-an-azure-vm"></a>Povolení replikace pro disk přidaný k virtuálnímu počítači Azure


Tento článek popisuje, jak povolit replikaci pro datové disky, které jsou přidány do virtuálního počítače Azure, který už je povolený pro zotavení po havárii do jiné oblasti Azure pomocí [Azure Site Recovery](site-recovery-overview.md).

Povolení replikace pro disk, který přidáte do virtuálního počítače, je podporované pro virtuální počítače Azure se spravovanými disky.

Když přidáte nový disk do virtuálního počítače Azure, který se replikuje do jiné oblasti Azure, dojde k následujícímu:

-   Stav replikace pro virtuální hod zobrazuje upozornění a poznámka na portálu vás informuje, že jeden nebo více disků jsou k dispozici pro ochranu.
-   Pokud povolíte ochranu přidaných disků, upozornění zmizí po počáteční replikaci disku.
-   Pokud se rozhodnete nepovolit replikaci disku, můžete upozornění zavřít.

![Byl přidán nový disk.](./media/azure-to-azure-enable-replication-added-disk/newdisk.png)



## <a name="before-you-start"></a>Než začnete

Tento článek předpokládá, že jste již nastavili zotavení po havárii pro virtuální počítače, do kterého přidáváte disk. Pokud jste tak neučinili, postupujte podle [kurzu azure zotavení po havárii](azure-to-azure-tutorial-enable-replication.md).

## <a name="enable-replication-for-an-added-disk"></a>Povolení replikace pro přidaný disk

Chcete-li povolit replikaci pro přidaný disk, postupujte takto:

1. V úschovně > **replikované položky**klikněte na virtuální ms, do kterého jste disk přidali.
2. Klepněte na **položku Disky**a vyberte datový disk, pro který chcete povolit replikaci (tyto disky mají **stav Není chráněn).**
3.  V **části Podrobnosti disku**klepněte na tlačítko **Povolit replikaci**.

    ![Povolení replikace pro přidaný disk](./media/azure-to-azure-enable-replication-added-disk/enabled-added.png)

Po spuštění úlohy povolit replikaci a dokončení počáteční replikace se odebere upozornění stavu replikace pro problém s diskem.



## <a name="next-steps"></a>Další kroky

[Další informace](site-recovery-test-failover-to-azure.md) o spuštění testu převzetí služeb při selhání.
