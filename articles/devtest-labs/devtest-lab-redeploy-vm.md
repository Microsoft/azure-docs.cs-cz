---
title: Opětovné nasazení virtuálního počítače v testovacím prostředí v Azure DevTest Labs | Microsoft Docs
description: Naučte se znovu nasadit virtuální počítač (přesunout z jednoho uzlu Azure do jiného) v Azure DevTest Labs.
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
ms.date: 06/11/2018
ms.author: spelluru
ms.openlocfilehash: 4500fcfa6fbfb346a6e5c7fd045ba0046a901b91
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2020
ms.locfileid: "84898721"
---
# <a name="redeploy-a-vm-in-a-lab-in-azure-devtest-labs"></a>Opětovné nasazení virtuálního počítače v testovacím prostředí v Azure DevTest Labs
Pokud se nemůžete připojit k virtuálnímu počítači v testovacím prostředí prostřednictvím připojení ke vzdálené ploše, znovu nasaďte virtuální počítač a zkuste se k němu připojit znovu. Když znovu nasadíte virtuální počítač, DevTest Labs přesune virtuální počítač z uzlu, na kterém je spuštěný, do nového uzlu v infrastruktuře Azure. Pak spustí virtuální počítač a zachová všechny možnosti konfigurace a přidružené prostředky. Tato funkce šetří čas strávený při řešení potíží s připojením ke vzdálené ploše nebo aplikací k virtuálním počítačům se systémem Windows v testovacím prostředí. 

## <a name="steps-to-redeploy-a-vm-in-a-lab"></a>Postup opětovného nasazení virtuálního počítače v testovacím prostředí 
Pokud chcete znovu nasadit virtuální počítač v testovacím prostředí v Azure DevTest Labs, proveďte následující kroky: 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby**a v seznamu vyberte **DevTest Labs** .
3. V seznamu cvičení vyberte testovací prostředí, které obsahuje virtuální počítač, který chcete znovu nasadit.  
4. Na levém panelu vyberte **moje Virtual Machines**. 
5. V seznamu virtuálních počítačů vyberte virtuální počítač.
6. Na stránce virtuálního počítače pro váš virtuální počítač vyberte v části **operace** v levé nabídce možnost **znovu nasadit** .

    ![Opětovné nasazení](media/devtest-lab-redeploy-vm/redeploy.png)
7. Přečtěte si informace na stránce a vyberte tlačítko **znovu nasadit** . 9. Ověřte stav operace opětovného nasazení v okně **oznámení** .

    ![Stav opětovného nasazení](media/devtest-lab-redeploy-vm/redeploy-status.png)

## <a name="next-steps"></a>Další kroky
Informace o tom, jak změnit velikost virtuálního počítače v Azure DevTest Labs, najdete v tématu [Změna velikosti virtuálního počítače](devtest-lab-resize-vm.md).


