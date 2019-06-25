---
title: Restartování virtuálního počítače v testovacím prostředí ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Zjistěte, jak restartování virtuálního počítače ve službě Azure DevTest Labs
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 34c08a79abf6acb5ae8582ecd0743a890d850fc8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60864400"
---
# <a name="restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Restartování virtuálního počítače v testovacím prostředí ve službě Azure DevTest Labs
Můžete rychle a snadno restartování virtuálního počítače ve službě DevTest Labs pomocí následujících kroků v tomto článku. Vezměte v úvahu následující před restartováním virtuálního počítače:

- Virtuální počítač musí být spuštěn restartování funkce povolit.
- Pokud je uživatel připojen do spuštěného virtuálního počítače při vykonávání restartování, se musí znovu připojit k virtuálnímu počítači po jeho spuštění zálohování.
- Pokud artefakt se právě používá, když restartujte virtuální počítač, zobrazí se upozornění, že nejde použít artefakt.

    ![Upozornění při restartování při použití artefaktů](./media/devtest-lab-restart-vm/devtest-lab-restart-vm-apply-artifacts.png)


   > [!NOTE]
   > Pokud při použití artefakt zastaví virtuální počítač, můžete použít funkci restartování virtuálního počítače jako potenciální způsob, jak problém vyřešit.
   >
   >

## <a name="steps-to-restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Postup restartování virtuálního počítače v testovacím prostředí ve službě Azure DevTest Labs
1. Přihlaste se k webu [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Vyberte **všechny služby**a pak vyberte **DevTest Labs** ze seznamu.
1. V seznamu testovacích prostředí vyberte testovací prostředí, která obsahuje virtuální počítač, který chcete restartovat.
1. Na levém panelu, vyberte **Moje Virtual Machines**.
1. Ze seznamu virtuálních počítačů vyberte spuštěného virtuálního počítače.
1. V horní části podokna Správa virtuálního počítače, vyberte **restartovat**.

    ![Restartujte virtuální počítač tlačítko](./media/devtest-lab-restart-vm/devtest-lab-restart-vm.png)

1. Monitorování stavu restartování tak, že vyberete **oznámení** ikonu v horní části napravo od okna.

    ![Zobrazení stavu restartování virtuálního počítače](./media/devtest-lab-restart-vm/devtest-lab-restart-notification.png)

Můžete také restartovat spuštěného virtuálního počítače tak, že vyberete jeho ikonu tří teček (...) v seznamu **Moje Virtual Machines**.

![Restartujte virtuální počítač prostřednictvím symbol tří teček](./media/devtest-lab-restart-vm/devtest-lab-restart-elipses.png)

## <a name="next-steps"></a>Další postup
* Po restartování, je možné znovu připojit k virtuálnímu počítači tak, že vyberete **připojit** na panel pro správu.
* Prozkoumejte [Galerie šablon rychlý start DevTest Labs Azure Resource Manageru](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)
