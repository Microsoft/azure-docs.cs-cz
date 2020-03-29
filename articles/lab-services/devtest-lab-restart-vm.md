---
title: Restartování virtuálního počítače v testovacím prostředí v laboratořích Azure DevTest Labs | Dokumenty společnosti Microsoft
description: Tento článek obsahuje kroky k rychlému a snadnému restartování virtuálních počítačů (VM) v azure devtest labs.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759990"
---
# <a name="restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Restartování virtuálního počítače v testovacím prostředí v laboratořích Azure DevTest Labs
Můžete rychle a snadno restartovat virtuální počítač v DevTest Labs podle kroků v tomto článku. Před restartováním virtuálního virtuálního montovana zvažte následující:

- Virtuální počítač musí být spuštěn, aby byla povolena funkce restartování.
- Pokud je uživatel při restartování připojený ke spuštěnému virtuálnímu počítači, musí se po spuštění znovu připojit k virtuálnímu počítači.
- Pokud artefakt se používá při restartování virtuálního počítače, zobrazí se upozornění, že artefakt nemusí být použita.

    ![Upozornění při restartování při použití artefaktů](./media/devtest-lab-restart-vm/devtest-lab-restart-vm-apply-artifacts.png)


   > [!NOTE]
   > Pokud se virtuální počítač zastavil při použití artefaktu, můžete použít funkci restartování virtuálního počítače jako potenciální způsob řešení problému.
   >
   >

## <a name="steps-to-restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Postup restartování virtuálního počítače v testovacím prostředí v laboratořích Azure DevTest Labs
1. Přihlaste se k [portálu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Vyberte **Všechny služby**a ze seznamu vyberte **DevTest Labs.**
1. Ze seznamu testovacích prostředí vyberte testovací prostředí, které obsahuje virtuální počítač, který chcete restartovat.
1. V levém panelu vyberte **Moje virtuální počítače**.
1. Ze seznamu virtuálních měn vyberte spuštěný virtuální ms.
1. V horní části podokna správy virtuálních počítačových společností vyberte **Restartovat**.

    ![Tlačítko Restartovat virtuální počítač](./media/devtest-lab-restart-vm/devtest-lab-restart-vm.png)

1. Sledování stavu restartování výběrem ikony **Oznámení** v pravém horním rohu okna.

    ![Zobrazení stavu restartování virtuálního počítače](./media/devtest-lab-restart-vm/devtest-lab-restart-notification.png)

Spuštěný virtuální počítač můžete také restartovat výběrem jeho tři tečky (...) v seznamu **Moje virtuální počítače**.

![Restartování virtuálního počítače prostřednictvím elips](./media/devtest-lab-restart-vm/devtest-lab-restart-elipses.png)

## <a name="next-steps"></a>Další kroky
* Po restartování se můžete znovu připojit k virtuálnímu počítači výběrem **možnosti Připojit** v podokně správy.
* Prozkoumejte [galerii šablon Rychléspuštění nástroje DevTest Labs Azure](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)
