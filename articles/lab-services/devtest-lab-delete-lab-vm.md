---
title: Odstranění testovacího prostředí nebo virtuálního počítače v testovacím prostředí v Azure DevTest Labs
description: V tomto článku se dozvíte, jak odstranit testovací prostředí nebo odstranit virtuální počítač v testovacím prostředí pomocí Azure Portal (Azure DevTest Labs).
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79270807"
---
# <a name="delete-a-lab-or-vm-in-a-lab-in-azure-devtest-labs"></a>Odstranění testovacího prostředí nebo virtuálního počítače v testovacím prostředí v Azure DevTest Labs
V tomto článku se dozvíte, jak odstranit testovací prostředí nebo virtuální počítač v testovacím prostředí.

## <a name="delete-a-lab"></a>Odstranění testovacího prostředí
Když odstraníte instanci DevTest Labs ze skupiny prostředků, služba DevTest Labs provede tyto akce: 

- Automaticky se odstraní všechny prostředky, které se automaticky vytvořily v době vytváření testovacího prostředí. Vlastní skupina prostředků se neodstraní. Pokud jste ručně vytvořili nějaké prostředky v této skupině prostředků, služba je neodstraní. 
- Všechny virtuální počítače v testovacím prostředí a skupinách prostředků přidružené k těmto virtuálním počítačům se automaticky odstraní. Když vytvoříte virtuální počítač v testovacím prostředí, služba vytvoří prostředky (disk, síťové rozhraní, veřejná IP adresa atd.) pro virtuální počítač v samostatné skupině prostředků. Pokud ale v těchto skupinách prostředků ručně vytvoříte další prostředky, služba DevTest Labs tyto prostředky a skupinu prostředků neodstraní. 

Chcete-li odstranit testovací prostředí, proveďte následující akce: 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce vlevo vyberte **všechny prostředky** , vyberte **DevTest Labs** pro typ služby a vyberte testovací prostředí.

    ![Výběr testovacího prostředí](media/devtest-lab-delete-lab-vm/select-lab.png)
3. Na stránce **testovací prostředí DevTest** klikněte na tlačítko **Odstranit** na panelu nástrojů. 

    ![Tlačítko Odstranit](media/devtest-lab-delete-lab-vm/delete-button.png)
4. Na stránce **potvrzení** zadejte **název** testovacího prostředí a vyberte **Odstranit**. 

    ![Confirm](media/devtest-lab-delete-lab-vm/confirm-delete.png)
5. Chcete-li zobrazit stav operace, vyberte ikonu **oznámení** (zvonek). 

    ![Oznámení](media/devtest-lab-delete-lab-vm/delete-status.png)

 
## <a name="delete-a-vm-in-a-lab"></a>Odstranění virtuálního počítače v testovacím prostředí
Když odstraním virtuální počítač v testovacím prostředí, odstraní se některé prostředky (ne všechny), které byly vytvořeny v době vytváření testovacího prostředí. Následující prostředky nejsou odstraněny: 

-   Trezor klíčů v hlavní skupině prostředků
-   Skupina dostupnosti, nástroj pro vyrovnávání zatížení, veřejná IP adresa ve skupině prostředků virtuálních počítačů. Tyto prostředky sdílí více virtuálních počítačů ve skupině prostředků. 

Odstraní se virtuální počítač, síťové rozhraní a disk přidružený k VIRTUÁLNÍmu počítači. 

Pokud chcete odstranit virtuální počítač v testovacím prostředí, proveďte následující akce: 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce vlevo vyberte **všechny prostředky** , vyberte **DevTest Labs** pro typ služby a vyberte testovací prostředí.

    ![Výběr testovacího prostředí](media/devtest-lab-delete-lab-vm/select-lab.png)
3. Vybrat **... (tři tečky)** pro virtuální počítač v seznamu virtuálních počítačů vyberte **Odstranit**. 

    ![Odstranit virtuální počítač v nabídce](media/devtest-lab-delete-lab-vm/delete-vm-menu-in-list.png)
4. V **potvrzovacím** dialogovém okně vyberte **OK**. 
5. Chcete-li zobrazit stav operace, vyberte ikonu **oznámení** (zvonek). 

Pokud chcete odstranit virtuální počítač ze **stránky virtuálního počítače**, vyberte **Odstranit** z panelu nástrojů, jak je znázorněno na následujícím obrázku:

![Odstranit virtuální počítač ze stránky virtuálního počítače](media/devtest-lab-delete-lab-vm/delete-from-vm-page.png) 


## <a name="next-steps"></a>Další kroky
Pokud chcete vytvořit testovací prostředí, přečtěte si následující články: 

- [Vytvoření testovacího prostředí](devtest-lab-create-lab.md)
- [Přidání virtuálního počítače do testovacího prostředí](devtest-lab-add-vm.md)