---
title: Opětovné nasazení virtuálního počítače v testovacím prostředí v Azure DevTest Labs | Microsoft Docs
description: Naučte se znovu nasadit virtuální počítač (přesunout z jednoho uzlu Azure do jiného) v Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a38b112165b893d877733b967c21bb62b20ca2f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "90530314"
---
# <a name="redeploy-a-vm-in-a-lab-in-azure-devtest-labs"></a>Opětovné nasazení virtuálního počítače v testovacím prostředí v Azure DevTest Labs
Pokud se nemůžete připojit k virtuálnímu počítači v testovacím prostředí prostřednictvím připojení ke vzdálené ploše, znovu nasaďte virtuální počítač a zkuste se k němu připojit znovu. Když znovu nasadíte virtuální počítač, DevTest Labs přesune virtuální počítač z uzlu, na kterém je spuštěný, do nového uzlu v infrastruktuře Azure. Pak spustí virtuální počítač a zachová všechny možnosti konfigurace a přidružené prostředky. Tato funkce šetří čas strávený při řešení potíží s připojením ke vzdálené ploše nebo aplikací k virtuálním počítačům se systémem Windows v testovacím prostředí. 

## <a name="steps-to-redeploy-a-vm-in-a-lab"></a>Postup opětovného nasazení virtuálního počítače v testovacím prostředí 
Pokud chcete znovu nasadit virtuální počítač v testovacím prostředí v Azure DevTest Labs, proveďte následující kroky: 

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby** a v seznamu vyberte **DevTest Labs** .
3. V seznamu cvičení vyberte testovací prostředí, které obsahuje virtuální počítač, který chcete znovu nasadit.  
4. Na levém panelu vyberte **moje Virtual Machines**. 
5. V seznamu virtuálních počítačů vyberte virtuální počítač.
6. Na stránce virtuálního počítače pro váš virtuální počítač vyberte v části **operace** v levé nabídce možnost **znovu nasadit** .

    ![Snímek obrazovky ukazuje stránku virtuálního počítače s vybraným opětovným nasazením.](media/devtest-lab-redeploy-vm/redeploy.png)
7. Přečtěte si informace na stránce a vyberte tlačítko **znovu nasadit** . 9. Ověřte stav operace opětovného nasazení v okně **oznámení** .

    ![Stav opětovného nasazení](media/devtest-lab-redeploy-vm/redeploy-status.png)

## <a name="next-steps"></a>Další kroky
Informace o tom, jak změnit velikost virtuálního počítače v Azure DevTest Labs, najdete v tématu [Změna velikosti virtuálního počítače](devtest-lab-resize-vm.md).


