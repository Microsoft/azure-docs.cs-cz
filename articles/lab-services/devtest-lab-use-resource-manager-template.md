---
title: Zobrazení a použití šablony virtuálního počítače Azure Resource Manageru | Dokumentace Microsoftu
description: Zjistěte, jak pomocí šablony Azure Resource Manageru z virtuálního počítače k vytvoření dalších virtuálních počítačů
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
ms.openlocfilehash: 4fd9f76223adba20028288b46037586f8f54fd1a
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51686377"
---
# <a name="create-virtual-machines-using-an-azure-resource-manager-template"></a>Vytvoření virtuálních počítačů pomocí šablony Azure Resource Manageru 

Při vytváření virtuálního počítače (VM) ve službě DevTest Labs prostřednictvím [webu Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040), před uložením virtuálního počítače můžete zobrazit šablonu Azure Resource Manageru. Šablonu pak slouží jako základ k vytvoření testovacího prostředí více virtuálních počítačů se stejným nastavením.

Tento článek popisuje více virtuálních počítačů a šablon Resource Manageru jednoho virtuálního počítače a ukazuje, jak zobrazit a uložit šablonu při vytváření virtuálního počítače.

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>Více virtuálních počítačů a šablon Resource Manageru jednoho virtuálního počítače
Existují dva způsoby, jak vytvořit virtuální počítače ve službě DevTest Labs pomocí šablony Resource Manageru: zřízení prostředků Microsoft.DevTestLab/labs/virtualmachines nebo zřídit Microsoft.Commpute/virtualmachines prostředků. Každá se používá v různých scénářích a vyžaduje jiný oprávnění.

- Šablony Resource Manageru, které používají typ prostředku Microsoft.DevTestLab/labs/virtualmachines (jak je deklarovaný ve vlastnosti v šabloně "prostředků") můžete zřizovat virtuální počítače jednotlivých testovacího prostředí. Každý virtuální počítač se pak zobrazí jako jedna položka v seznamu virtuálních počítačů DevTest Labs:

   ![Seznam virtuálních počítačů jako jednotlivé položky v seznamu virtuálních počítačů DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   Tento typ šablony Resource Manageru dají zřídit prostřednictvím Azure Powershellu příkaz **New-AzureRmResourceGroupDeployment** nebo prostřednictvím příkazového řádku Azure **vytvořit nasazení skupiny pro az**. Vyžaduje oprávnění správce, aby uživatelé, kteří jsou přiřazeni k roli uživatele DevTest Labs nelze provádět nasazení. 

- Šablony Resource Manageru, které používají typ prostředku Microsoft.Compute/virtualmachines můžete zřídit několik virtuálních počítačů jako jednotné prostředí v DevTest Labs seznam virtuálních počítačů:

   ![Seznam virtuálních počítačů jako jednotlivé položky v seznamu virtuálních počítačů DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   Virtuální počítače ve stejném prostředí můžete spravovat společně a sdílet stejný životní cyklus. Uživatelé, kteří jsou přiřazeni k roli uživatele DevTest Labs můžete vytvořit prostředí pomocí těchto šablon, tak dlouho, dokud správce nakonfiguroval testovacího prostředí tímto způsobem.

Zbývající část tohoto článku popisuje šablon Resource Manageru, které používají Microsoft.DevTestLab/labs/virtualmachines. Správce testovacího prostředí tyto jsou používány k automatizaci vytvoření testovacího prostředí virtuálního počítače (například nárokovatelných virtuálních počítačů) nebo generování zlaté image (například obrázek objekt pro vytváření).

[Osvědčené postupy pro vytváření šablon Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) nabízí mnoho pokyny a návrhy k vytváření šablon Azure Resource Manageru, které je spolehlivé a snadné použití.

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>Zobrazení a uložení virtuálního počítače šablonu Resource Manageru
1. Postupujte podle kroků uvedených v [vytvoření vašeho prvního virtuálního počítače v testovacím prostředí](devtest-lab-create-first-vm.md) zahajte proces vytváření virtuálního počítače.
1. Zadejte požadované informace pro váš virtuální počítač a přidat všechny artefakty, které chcete pro tento virtuální počítač.
1. V dolní části okna Konfigurovat nastavení, zvolte **šablony ARM zobrazení**.

   ![Zobrazit tlačítko šablony ARM](./media/devtest-lab-use-arm-template/devtestlab-lab-view-rm-template.png)
1. Zkopírujte a uložte šablonu Resource Manageru pro pozdější použití k vytvoření jiného virtuálního počítače.

   ![Šablony Resource Manageru uložit pro pozdější použití.](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

Po uložení šablony Resource Manageru, je nutné aktualizovat sekci parametrů šablony, než budete moct použít. Můžete vytvořit parameter.json, který upravuje pouze parametry, mimo skutečnou šablonu Resource Manageru. 

![Přizpůsobení parametrů pomocí souboru JSON](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

Šablony Resource Manageru je teď připravený k použití na [vytvoření virtuálního počítače](devtest-lab-create-environment-from-arm.md).

### <a name="next-steps"></a>Další postup
* Zjistěte, jak [vytvoření prostředí více virtuálních počítačů pomocí šablon Resource Manageru](devtest-lab-create-environment-from-arm.md).
* [Nasazení šablony Resource Manageru k vytvoření virtuálního počítače](devtest-lab-create-environment-from-arm.md#deploy-a-resource-manager-template-to-create-a-vm)
* Prozkoumejte další šablony Resource Manageru rychlý start pro DevTest Labs automatizace z [veřejného úložiště DevTest Labs GitHub](https://github.com/Azure/azure-quickstart-templates).
