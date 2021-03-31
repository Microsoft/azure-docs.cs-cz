---
title: Změna velikosti virtuálního počítače v testovacím prostředí v Azure DevTest Labs
description: Naučte se, jak změnit velikost virtuálního počítače v Azure DevTest Labs na základě vašich požadavků na výkon procesoru, sítě nebo disku.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 592be0862bc33ead86fe43d37753fb72fde120c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85482016"
---
# <a name="resize-a-vm-in-a-lab-in-azure-devtest-labs"></a>Změna velikosti virtuálního počítače v testovacím prostředí v Azure DevTest Labs
Jednou z důležitých funkcí virtuálních počítačů Azure je to, že umožňuje změnit velikost virtuálního počítače na základě vašich potřeb pro výkon procesoru, sítě nebo disku. Azure DevTest Labs podporuje tuto funkci pro virtuální počítače v testovacím prostředí. Funkce změny velikosti dodržuje zásady testovacího prostředí pro povolené velikosti virtuálních počítačů v testovacím prostředí. To znamená, že můžete změnit velikost virtuálního počítače jenom na povolené velikosti v testovacím prostředí. 


## <a name="steps-to-resize-a-vm-in-a-lab"></a>Postup změny velikosti virtuálního počítače v testovacím prostředí 
Pokud chcete změnit velikost virtuálního počítače v testovacím prostředí v Azure DevTest Labs, proveďte následující kroky: 

> [!NOTE]
> Pokud jste připojeni k virtuálnímu počítači přes relaci vzdálené plochy (RDP), uložte svou práci a odpojte se od virtuálního počítače před změnou velikosti.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby** a v seznamu vyberte **DevTest Labs** .
3. V seznamu Labs vyberte testovací prostředí, které obsahuje virtuální počítač, který chcete změnit.  
4. Na levém panelu vyberte **moje Virtual Machines**. 
5. V seznamu virtuálních počítačů vyberte virtuální počítač.
6. Pokud je virtuální počítač spuštěný, vyberte **zastavit** na panelu nástrojů. Stav operace ověřte v okně **oznámení** . Počkejte, až se virtuální počítač zastaví, a pak okno **oznámení** zavřete. 

    ![Zastavení virtuálního počítače](media/devtest-lab-resize-vm/stop-vm.png)
1. Na stránce virtuální počítač pro virtuální počítač vyberte **Velikost** v části **Nastavení** v nabídce vlevo.

    ![Nabídka velikost](media/devtest-lab-resize-vm/size-menu.png)
1. V okně **Zvolte velikost** vyhledejte a vyberte velikost virtuálního počítače a klikněte na **Vybrat**.     
1. Ověřte stav operace změny velikosti v okně **oznámení** .

    ![Změnit velikost stavu](media/devtest-lab-resize-vm/resize-status.png)
10. Po úspěšném dokončení operace změny velikosti zavřete okno **oznámení** . 
11. V nabídce vlevo vyberte **Přehled** a na panelu nástrojů vyberte **restartovat** a restartujte virtuální počítač. 

## <a name="next-steps"></a>Další kroky
Podrobné informace o funkci změny velikosti podporované virtuálními počítači Azure najdete v tématu [Změna velikosti virtuálních počítačů](https://azure.microsoft.com/blog/resize-virtual-machines/).


