---
title: Opětovné nasazení virtuálního počítače v testovacím prostředí ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Zjistěte, jak znovu nasadit virtuální počítač (přesunout z jednoho uzlu Azure do jiné) ve službě Azure DevTest Labs.
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
ms.openlocfilehash: 273b0f1105d8b71b90a06e2627e201b97f12a754
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35643610"
---
# <a name="redeploy-a-vm-in-a-lab-in-azure-devtest-labs"></a>Opětovné nasazení virtuálního počítače v testovacím prostředí ve službě Azure DevTest Labs
Pokud se nemůžete připojit k virtuálnímu počítači (VM) v testovacím prostředí pomocí připojení ke vzdálené ploše, znovu nasadit virtuální počítač a opakujte conencting k němu. Při opětovném nasazování virtuálního počítače s DevTest Labs přesune virtuální počítač z uzlu, na kterém běží do nového uzlu v rámci infrastruktury Azure. Pak spustí virtuální počítač při zachování všech možností konfigurace a přidružených prostředků. Tato funkce může ušetřit vám čas trvání řešení potíží s připojení ke vzdálené ploše nebo přístup k aplikaci do virtuálních počítačů s Windows v testovacím prostředí. 

## <a name="steps-to-redeploy-a-vm-in-a-lab"></a>Postup opětovné nasazení virtuálního počítače v testovacím prostředí 
Opětovné nasazení virtuálního počítače v testovacím prostředí ve službě Azure DevTest Labs, proveďte následující kroky: 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby**a pak vyberte **DevTest Labs** ze seznamu.
3. V seznamu testovacích prostředí vyberte testovací prostředí, která obsahuje virtuální počítač, který chcete znovu nasadit.  
4. Na levém panelu, vyberte **Moje Virtual Machines**. 
5. Ze seznamu virtuálních počítačů vyberte virtuální počítač.
6. Na stránce virtuální počítač pro virtuální počítač vyberte **znovu nasadit** pod **operace** v levé nabídce.

    ![Opětovné nasazení](media/devtest-lab-redeploy-vm/redeploy.png)
7. Přečtěte si informace na stránce a vyberte **znovu nasadit** tlačítko. 9. Kontrola stavu operace opětovného nasazení **oznámení** okna.

    ![Stav opětovné nasazení](media/devtest-lab-redeploy-vm/redeploy-status.png)

## <a name="next-steps"></a>Další postup
Zjistěte, jak změnit velikost virtuálního počítače ve službě Azure DevTest Labs, naleznete v tématu [změnit velikost virtuálního počítače](devtest-lab-resize-vm.md).


