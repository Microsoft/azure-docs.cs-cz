---
title: Zobrazení a použití šablony virtuálního počítače Azure Resource Manageru | Microsoft Docs
description: Zjistěte, jak vytvořit ostatních virtuálních počítačů pomocí šablony Azure Resource Manageru z virtuálního počítače
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: a759d9ce-655c-4ac6-8834-cb29dd7d30dd
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 1b96c801e3c5a43f23d4c63c13a611c24dd6705b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="create-virtual-machines-using-an-azure-resource-manager-template"></a>Vytváření virtuálních počítačů pomocí šablony Azure Resource Manager 

Když vytváříte virtuální počítač (VM) v DevTest Labs prostřednictvím [portál Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), můžete zobrazit šablonu Azure Resource Manager před uložením virtuálního počítače. Šablony pak slouží jako základ pro vytvoření testovacího prostředí více virtuálních počítačů se stejným nastavením.

Tento článek popisuje více virtuálních počítačů vs. šablony Resource Manageru single-VM a ukazuje, jak zobrazit a uložit šablonu při vytváření virtuálního počítače.

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>Více virtuálních počítačů vs. šablony Resource Manageru single-VM
Existují dva způsoby, jak vytvořit virtuální počítače v DevTest Labs pomocí šablony Resource Manageru: zřizování prostředků Microsoft.DevTestLab/labs/virtualmachines nebo zřídit Microsoft.Commpute/virtualmachines prostředků. Každý se používá v různých scénářích a vyžaduje jiný oprávnění.

- Šablony Resource Manageru, které používají typ prostředku Microsoft.DevTestLab/labs/virtualmachines (podle údajů v vlastnost "prostředku" v šabloně) můžou zřizovat virtuální počítače jednotlivých testovacího prostředí. Každý virtuální počítač se pak objeví jako jedna položka v seznamu DevTest Labs virtuální počítače:

   ![Seznam virtuálních počítačů jako jednotlivé položky v seznamu DevTest Labs virtuální počítače](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   Tento typ šablony Resource Manageru můžete zřídit pomocí příkazu prostředí Azure PowerShell **New-AzureRmResourceGroupDeployment** nebo pomocí příkazu příkazového řádku Azure CLI **vytvořit nasazení skupiny az**. Vyžaduje oprávnění správce, aby uživatelé, kteří jsou přiřazeny k roli uživatele DevTest Labs nelze provést nasazení. 

- Šablony Resource Manageru, které používají typ prostředku Microsoft.Compute/virtualmachines můžete zřídit několik virtuálních počítačů jako jeden prostředí v DevTest Labs seznamu virtuálních počítačů:

   ![Seznam virtuálních počítačů jako jednotlivé položky v seznamu DevTest Labs virtuální počítače](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   Virtuální počítače ve stejném prostředí lze spravovat společně a sdílet stejný životní cyklus. Uživatelé, kteří jsou přiřazeny k roli uživatele DevTest Labs můžete vytvořit prostředí pomocí těchto šablon, dokud správce nakonfiguroval testovací prostředí tímto způsobem.

Zbývající část tohoto článku popisuje šablony Resource Manageru, které používají Mirosoft.DevTestLab/labs/virtualmachines. Ty se používají správci testovacího prostředí automatizovat vytvoření testovacího prostředí virtuálních počítačů (například vymahatelných virtuální počítače) nebo zlaté image generace (například obrázek factory).

[Osvědčené postupy pro vytváření šablon Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) nabízí mnoho pokyny a doporučení k vytváření šablon Azure Resource Manageru, která jsou spolehlivé a snadno se používá.

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>Zobrazení a uložení šablony Resource Manageru virtuálního počítače
1. Postupujte podle kroků v [vytvoření vaší první virtuální počítač v testovacím prostředí](devtest-lab-create-first-vm.md) zahajte proces vytváření virtuálního počítače.
1. Zadejte požadované informace pro virtuální počítač a přidejte všechny artefakty, které chcete pro tento virtuální počítač.
1. V dolní části okna nastavení konfigurace, zvolte **šablony ARM zobrazení**.

   ![Tlačítko šablony ARM zobrazení](./media/devtest-lab-use-arm-template/devtestlab-lab-view-rm-template.png)
1. Zkopírujte a uložte šablony Resource Manageru pro pozdější použití vytvořit jiný virtuální počítač.

   ![Šablony Resource Manageru uložit pro pozdější použití](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

Po uložení šablony Resource Manageru, musíte aktualizovat sekci parametrů šablony, než budete moci použít. Můžete vytvořit parameter.json, který upravuje jenom parametry, mimo skutečné šablony Resource Manageru. 

![Přizpůsobit parametry pomocí souboru JSON](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

Šablona Resource Manager je nyní připravena k [vytvoření virtuálního počítače](devtest-lab-create-environment-from-arm.md).

### <a name="next-steps"></a>Další postup
* Zjistěte, jak [vytvoření prostředí více virtuálních počítačů pomocí šablony Resource Manageru](devtest-lab-create-environment-from-arm.md).
* [Nasazení šablony Resource Manageru k vytvoření virtuálního počítače](devtest-lab-create-environment-from-arm.md#deploy-a-resource-manager-template-to-create-a-vm)
* Prozkoumejte další šablony Resource Manageru rychlý start pro automatizaci DevTest Labs z [veřejného úložiště DevTest Labs GitHub](https://github.com/Azure/azure-quickstart-templates).
