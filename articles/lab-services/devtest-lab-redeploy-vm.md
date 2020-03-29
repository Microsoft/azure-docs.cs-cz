---
title: Opětovné nasazení virtuálního počítače v testovacím prostředí v laboratořích Azure DevTest Labs | Dokumenty společnosti Microsoft
description: Zjistěte, jak znovu nasadit virtuální počítač (přesun z jednoho uzlu Azure do druhého) v Azure DevTest Labs.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60561595"
---
# <a name="redeploy-a-vm-in-a-lab-in-azure-devtest-labs"></a>Opětovné nasazení virtuálního počítače v testovacím prostředí v laboratořích Azure DevTest Labs
Pokud se nemůžete připojit k virtuálnímu počítači (VM) v testovacím prostředí prostřednictvím připojení ke vzdálené ploše, znovu nasadit virtuální počítač a zkuste se k němu znovu připojit. Když znovu nasadíte virtuální počítač, DevTest Labs přesune virtuální počítač z uzlu, na kterém běží na nový uzel v rámci infrastruktury Azure. Potom spustí virtuální počítač při zachování všech možností konfigurace a přidružené prostředky. Tato funkce vám ušetří čas strávený při řešení potíží s připojením ke vzdálené ploše nebo přístupem aplikací k virtuálním počítačům se systémem Windows v testovacím prostředí. 

## <a name="steps-to-redeploy-a-vm-in-a-lab"></a>Postup opětovného nasazení virtuálního virtuálního montovavky v testovacím prostředí 
Pokud chcete znovu nasadit virtuální počítač v testovacím prostředí v Azure DevTest Labs, postupujte takto: 

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Vyberte **Všechny služby**a ze seznamu vyberte **DevTest Labs.**
3. Ze seznamu testovacích prostředí vyberte testovací prostředí, které obsahuje virtuální hod, který chcete znovu nasadit.  
4. V levém panelu vyberte **Moje virtuální počítače**. 
5. Ze seznamu virtuálních měn vyberte virtuální ho.
6. Na stránce Virtuální počítač pro virtuální počítač vyberte v levém menu **možnost Znovu nasadit** v části **OPERACE.**

    ![Opětovné nasazení](media/devtest-lab-redeploy-vm/redeploy.png)
7. Přečtěte si informace na stránce a vyberte tlačítko **Znovu nasadit.** 9. Zkontrolujte stav operace opětovného nasazení v okně **Oznámení.**

    ![Stav opětovného nasazení](media/devtest-lab-redeploy-vm/redeploy-status.png)

## <a name="next-steps"></a>Další kroky
Přečtěte si, jak změnit velikost virtuálního počítače v Azure DevTest Labs, najdete v [tématu Změna velikosti virtuálního počítače](devtest-lab-resize-vm.md).


