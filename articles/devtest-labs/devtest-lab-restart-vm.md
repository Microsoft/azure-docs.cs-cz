---
title: Restartování virtuálního počítače v testovacím prostředí v Azure DevTest Labs | Microsoft Docs
description: Tento článek popisuje kroky pro rychlé a snadné restartování virtuálních počítačů v Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d2bcbcbea613aa84da91789fb9f118afd7023fab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85481984"
---
# <a name="restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Restartování virtuálního počítače v testovacím prostředí v Azure DevTest Labs
Pomocí kroků v tomto článku můžete rychle a snadno restartovat virtuální počítač v DevTest Labs. Před restartováním virtuálního počítače zvažte následující:

- Aby bylo možné funkci restart povolit, musí být spuštěný virtuální počítač.
- Pokud je uživatel připojen ke spuštěnému virtuálnímu počítači, když provádí restart, musí se po jeho spuštění znovu připojit k virtuálnímu počítači.
- Pokud se při restartování virtuálního počítače použije artefakt, zobrazí se upozornění, že artefakt nemůžete použít.

    ![Upozornění při restartování při aplikování artefaktů](./media/devtest-lab-restart-vm/devtest-lab-restart-vm-apply-artifacts.png)


   > [!NOTE]
   > Pokud při použití artefaktu byl virtuální počítač zastavený, můžete ho použít jako potenciální způsob řešení problému.
   >
   >

## <a name="steps-to-restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Postup restartování virtuálního počítače v testovacím prostředí v Azure DevTest Labs
1. Přihlaste se na [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Vyberte **všechny služby** a v seznamu vyberte **DevTest Labs** .
1. V seznamu Labs vyberte testovací prostředí, které obsahuje virtuální počítač, který chcete restartovat.
1. Na levém panelu vyberte **moje Virtual Machines**.
1. V seznamu virtuálních počítačů vyberte spuštěný virtuální počítač.
1. V horní části podokna Správa virtuálních počítačů vyberte **restartovat**.

    ![Tlačítko restartovat virtuální počítač](./media/devtest-lab-restart-vm/devtest-lab-restart-vm.png)

1. Monitorujte stav restartování výběrem ikony **oznámení** v pravém horním rohu okna.

    ![Zobrazení stavu restartování virtuálního počítače](./media/devtest-lab-restart-vm/devtest-lab-restart-notification.png)

Běžící virtuální počítač můžete také restartovat tak, že v seznamu **mých Virtual Machines** vyberete jeho tři tečky (...).

![Restartování virtuálního počítače přes tři tečky](./media/devtest-lab-restart-vm/devtest-lab-restart-elipses.png)

## <a name="next-steps"></a>Další kroky
* Po restartování se můžete k virtuálnímu počítači připojit tak, že v podokně Správa vyberete **připojit** .
* Prozkoumejte [galerii šablon rychlého startu Azure Resource Manager DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)
