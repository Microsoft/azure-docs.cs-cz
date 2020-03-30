---
title: Odstranění testovacího prostředí nebo virtuálního počítače v testovacím prostředí v laboratořích Azure DevTest Labs
description: Tento článek ukazuje, jak odstranit testovací prostředí nebo odstranit virtuální počítač v testovacím prostředí pomocí portálu Azure (Azure DevTest Labs).
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 28fb464714f464a4c0a8f5eaf304dcdd5d603c90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270807"
---
# <a name="delete-a-lab-or-vm-in-a-lab-in-azure-devtest-labs"></a>Odstranění testovacího prostředí nebo virtuálního počítače v testovacím prostředí v laboratořích Azure DevTest Labs
Tento článek ukazuje, jak odstranit testovací prostředí nebo virtuální ho virtuálního min. v testovacím prostředí.

## <a name="delete-a-lab"></a>Odstranění testovacího prostředí
Když odstraníte instanci DevTest Labs ze skupiny prostředků, služba DevTest Labs provede následující akce: 

- Všechny prostředky, které byly automaticky vytvořeny v době vytvoření testovacího prostředí, budou automaticky odstraněny. Samotná skupina prostředků není odstraněna. Pokud jste ručně vytvořili všechny prostředky této skupiny prostředků, služba je neodstraní. 
- Všechny virtuální uživatele v testovacím prostředí a skupiny prostředků přidružené k těmto virtuálním účtům se automaticky odstraní. Když vytvoříte virtuální hod v testovacím prostředí, služba vytvoří prostředky (disk, síťové rozhraní, veřejnou IP adresu atd.) pro virtuální hod v samostatné skupině prostředků. Pokud však ručně vytvoříte další prostředky v těchto skupinách prostředků, služba DevTest Labs tyto prostředky a skupinu prostředků neodstraní. 

Chcete-li odstranit testovací prostředí, proveďte následující akce: 

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Vyberte **všechny zdroje** z nabídky na levé straně, vyberte **DevTest Labs** pro typ služby a vyberte testovací prostředí.

    ![Vyberte si testovací prostředí](media/devtest-lab-delete-lab-vm/select-lab.png)
3. Na stránce **DevTest Lab** klikněte na panelu nástrojů na **Odstranit.** 

    ![Tlačítko Odstranit](media/devtest-lab-delete-lab-vm/delete-button.png)
4. Na stránce **Potvrzení** zadejte **název** testovacího prostředí a vyberte **Odstranit**. 

    ![Confirm](media/devtest-lab-delete-lab-vm/confirm-delete.png)
5. Chcete-li zobrazit stav operace, vyberte ikonu **Oznámení (Zvonek).** 

    ![Oznámení](media/devtest-lab-delete-lab-vm/delete-status.png)

 
## <a name="delete-a-vm-in-a-lab"></a>Odstranění virtuálního virtuálního ms v testovacím prostředí
Pokud odstraním virtuální ho v testovacím prostředí, odstraní se některé prostředky (ne všechny), které byly vytvořeny v době vytvoření testovacího prostředí. Následující prostředky nejsou odstraněny: 

-   Trezor klíčů v hlavní skupině prostředků
-   Skupina dostupnosti, vyrovnávání zatížení, veřejná IP adresa ve skupině prostředků virtuálního počítačů. Tyto prostředky jsou sdíleny více virtuálními počítači ve skupině prostředků. 

Virtuální počítač, síťové rozhraní a disk přidružený k virtuálnímu počítači se odstraní. 

Pokud chcete odstranit virtuální hospova v testovacím prostředí, proveďte následující akce: 

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Vyberte **všechny zdroje** z nabídky na levé straně, vyberte **DevTest Labs** pro typ služby a vyberte testovací prostředí.

    ![Vyberte si testovací prostředí](media/devtest-lab-delete-lab-vm/select-lab.png)
3. Vyberte **... (tři tečky)** pro virtuální ho virtuálního soudu v seznamu virtuálních discích a vyberte **Odstranit**. 

    ![Odstranit virtuální virtuální ms v nabídce](media/devtest-lab-delete-lab-vm/delete-vm-menu-in-list.png)
4. V **potvrzovacím** dialogovém okně vyberte **ok**. 
5. Chcete-li zobrazit stav operace, vyberte ikonu **Oznámení (Zvonek).** 

Pokud chcete odstranit virtuální počítač ze **stránky Virtuální počítač**, vyberte **Odstranit** z panelu nástrojů, jak je znázorněno na následujícím obrázku:

![Odstranění virtuálního virtuálního mísy ze stránky virtuálního aplikace](media/devtest-lab-delete-lab-vm/delete-from-vm-page.png) 


## <a name="next-steps"></a>Další kroky
Pokud chcete vytvořit testovací prostředí, podívejte se na následující články: 

- [Vytvoření testovacího prostředí](devtest-lab-create-lab.md)
- [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md)