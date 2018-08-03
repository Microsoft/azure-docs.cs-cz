---
title: Odstranění testovacího prostředí nebo virtuálních počítačů v testovacím prostředí ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Tento článek ukazuje, jak odstranění testovacího prostředí nebo virtuálních počítačů v testovacím prostředí.
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
ms.date: 07/30/2018
ms.author: spelluru
ms.openlocfilehash: 99caf04698226de8daa9cfb8f60662e5cb0f8b49
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449824"
---
# <a name="delete-a-lab-or-vm-in-a-lab-in-azure-devtest-labs"></a>Odstranění testovacího prostředí nebo virtuálních počítačů v testovacím prostředí ve službě Azure DevTest Labs
Tento článek ukazuje, jak odstranění testovacího prostředí nebo virtuálních počítačů v testovacím prostředí.

## <a name="delete-a-lab"></a>Odstranění testovacího prostředí
Když odstraníte instanci DevTest Labs ze skupiny prostředků, služby DevTest Labs provede následující akce: 

- Všechny prostředky, které byly vytvořeny automaticky v době vytvoření testovacího prostředí se automaticky odstraní. Není odstraní samotnou skupinu prostředků. Pokud jste ručně vytvořili všechny prostředky tuto skupinu prostředků, nedojde k odstranění jejich službu. 
- Všechny virtuální počítače ve skupinách testovacího prostředí a zdroje přidružené k těmto virtuálním počítačům se automaticky odstraní. Při vytváření virtuálního počítače v testovacím prostředí, vytvoří službu prostředků (disku, síťové rozhraní, veřejné IP adresy, atd.) pro virtuální počítač v samostatné skupiny prostředků. Nicméně pokud ručně vytvořit další prostředky v těchto skupinách prostředků, služby DevTest Labs nedojde k odstranění těchto prostředků a skupinu prostředků. 

Pokud chcete odstranit testovací prostředí, proveďte následující akce: 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všech prostředků** v nabídce na levé straně vyberte **DevTest Labs** pro typ služby a vyberte testovací prostředí.

    ![Vyberte testovací prostředí](media\devtest-lab-delete-lab-vm\select-lab.png)
3. Na **DevTest Lab** klikněte na **odstranit** na panelu nástrojů. 

    ![Tlačítko Odstranit](media\devtest-lab-delete-lab-vm\delete-button.png)
4. Na **potvrzení** stránky, zadejte **název** testovacího prostředí a vyberte **odstranit**. 

    ![Potvrdit](media\devtest-lab-delete-lab-vm\confirm-delete.png)
5. Pokud chcete zobrazit stav operace, vyberte **oznámení** ikonu (zvonek). 

    ![Oznámení](media\devtest-lab-delete-lab-vm\delete-status.png)

 
## <a name="delete-a-vm-in-a-lab"></a>Odstranění virtuálního počítače v testovacím prostředí
Když odstraním virtuálního počítače v testovacím prostředí, jsou odstraněny některé z prostředků (ne všechny), které byly vytvořeny v době vytvoření testovacího prostředí. Nebudou odstraněny následující zdroje: 

-   Ve skupině prostředků hlavní trezor klíčů
-   Dostupnost nastavení, pro load balancer, veřejnou IP adresu ve skupině prostředků virtuálního počítače. Tyto prostředky se sdílí několik virtuálních počítačů ve skupině prostředků. 

Virtuální počítač, síťové rozhraní a disk přidružený k virtuálnímu počítači se odstraní. 

Pokud chcete odstranit virtuální počítač v testovacím prostředí, proveďte následující akce: 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všech prostředků** v nabídce na levé straně vyberte **DevTest Labs** pro typ služby a vyberte testovací prostředí.

    ![Vyberte testovací prostředí](media\devtest-lab-delete-lab-vm\select-lab.png)
3. Vyberte **... (tři tečky)**  pro virtuální počítač v seznamu virtuálních počítačů a vyberte **odstranit**. 

    ![Odstranění virtuálního počítače v nabídce](media\devtest-lab-delete-lab-vm\delete-vm-menu-in-list.png)
4. Na **potvrzení** dialogu **Ok**. 
5. Pokud chcete zobrazit stav operace, vyberte **oznámení** ikonu (zvonek). 

Odstranit z virtuálního počítače **stránce virtuálního počítače**vyberte **odstranit** z panelu nástrojů, jak je znázorněno na následujícím obrázku:

![Odstranění virtuálního počítače z virtuálního počítače stránky](media\devtest-lab-delete-lab-vm\delete-from-vm-page.png) 


## <a name="next-steps"></a>Další postup
Pokud chcete vytvořit testovací prostředí, najdete v následujících článcích: 

- [Vytvoření testovacího prostředí](devtest-lab-create-lab.md)
- [Přidání virtuálního počítače v testovacím prostředí](devtest-lab-add-vm.md)