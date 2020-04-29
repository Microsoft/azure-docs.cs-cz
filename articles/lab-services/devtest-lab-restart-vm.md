---
title: Restartování virtuálního počítače v testovacím prostředí v Azure DevTest Labs | Microsoft Docs
description: Tento článek popisuje kroky pro rychlé a snadné restartování virtuálních počítačů v Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 52d3b92909483a99eb82c86b727261bbeb5f8d46
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "76759990"
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
1. Přihlaste se k webu [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Vyberte **všechny služby**a v seznamu vyberte **DevTest Labs** .
1. V seznamu Labs vyberte testovací prostředí, které obsahuje virtuální počítač, který chcete restartovat.
1. Na levém panelu vyberte **moje Virtual Machines**.
1. V seznamu virtuálních počítačů vyberte spuštěný virtuální počítač.
1. V horní části podokna Správa virtuálních počítačů vyberte **restartovat**.

    ![Tlačítko restartovat virtuální počítač](./media/devtest-lab-restart-vm/devtest-lab-restart-vm.png)

1. Monitorujte stav restartování výběrem ikony **oznámení** v pravém horním rohu okna.

    ![Zobrazení stavu restartování virtuálního počítače](./media/devtest-lab-restart-vm/devtest-lab-restart-notification.png)

Běžící virtuální počítač můžete také restartovat tak, že v seznamu **mých Virtual Machines**vyberete jeho tři tečky (...).

![Restartování virtuálního počítače přes tři tečky](./media/devtest-lab-restart-vm/devtest-lab-restart-elipses.png)

## <a name="next-steps"></a>Další kroky
* Po restartování se můžete k virtuálnímu počítači připojit tak, že v podokně Správa vyberete **připojit** .
* Prozkoumejte [galerii šablon rychlého startu Azure Resource Manager DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)
