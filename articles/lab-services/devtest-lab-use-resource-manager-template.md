---
title: Zobrazení a použití šablony Správce prostředků Azure virtuálního počítače
description: Zjistěte, jak pomocí šablony Azure Resource Manageru z virtuálního počítače vytvořit další virtuální počítače.
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
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 7064fdeec04f4dc5ae2c73c1a3896cf2d10dd01d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169114"
---
# <a name="create-virtual-machines-using-an-azure-resource-manager-template"></a>Vytváření virtuálních počítačů pomocí šablony Azure Resource Manageru 

Když vytváříte virtuální počítač (VM) v DevTest Labs prostřednictvím [portálu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040), můžete zobrazit šablonu Azure Resource Manager před uložením virtuálního počítače. Šablonu pak můžete použít jako základ pro vytvoření dalších virtuálních počítače testovacího prostředí se stejným nastavením.

Tento článek popisuje šablony Správce prostředků více virtuálních počítače vs. jednoho virtuálního počítače a ukazuje, jak zobrazit a uložit šablonu při vytváření virtuálního počítače.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>Šablony Správce prostředků s více virtuálními počítačemi vs.
Existují dva způsoby, jak vytvořit virtuální počítače v DevTest Labs pomocí šablony Správce prostředků: zřízení Microsoft.DevTestLab/labs/virtualmachines prostředku nebo zřízení Microsoft.Compute/virtualmachines prostředku. Každý se používá v různých scénářích a vyžaduje různá oprávnění.

- Šablony Správce prostředků, které používají typ prostředku Microsoft.DevTestLab/labs/virtualmachines (jak je uvedeno ve vlastnosti "prostředek" v šabloně) mohou zřídit jednotlivé virtuální počítače testovacího prostředí. Každý virtuální počítač se pak zobrazí jako jedna položka v seznamu virtuálních počítačů DevTest Labs:

   ![Seznam virtuálních počítačů jako jednotlivé položky v seznamu virtuálních počítačů DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   Tento typ šablony Správce prostředků lze zřídit pomocí příkazu Azure PowerShell **New-AzResourceGroupDeployment** nebo prostřednictvím **vytvoření nasazení skupiny AZ příkazu**Azure. Vyžaduje oprávnění správce, takže uživatelé, kteří jsou přiřazeni k roli uživatele DevTest Labs, nemohou nasazení provést. 

- Šablony Správce prostředků, které používají typ prostředku Microsoft.Compute/virtualmachines můžete zřídit více virtuálních počítačů jako jedno prostředí v devTest Labs virtuálnípočítače seznamu:

   ![Seznam virtuálních počítačů jako jednotlivé položky v seznamu virtuálních počítačů DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   Virtuální aplikace ve stejném prostředí lze spravovat společně a sdílet stejný životní cyklus. Uživatelé, kteří jsou přiřazeni k roli uživatele DevTest Labs můžete vytvořit prostředí pomocí těchto šablon tak dlouho, dokud správce nakonfiguroval testovací prostředí tímto způsobem.

Zbývající část tohoto článku popisuje šablony Správce prostředků, které používají Microsoft.DevTestLab/labs/virtualmachines. Používají je správci testovacího prostředí k automatizaci vytváření virtuálních virtuálních zařízení testovacího prostředí (například nárokovatelné virtuální ch odvodů) nebo generování zlatých bitových obrázků (například factory bitových obrázků).

[Doporučené postupy pro vytváření šablon Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) nabízí mnoho pokynů a návrhů, které vám pomohou vytvořit šablony Azure Resource Manager, které jsou spolehlivé a snadno použitelné.

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>Zobrazení a uložení šablony Správce prostředků virtuálního počítače
1. Postupujte podle kroků v [části Vytvoření prvního virtuálního počítače v testovacím prostředí](tutorial-create-custom-lab.md#add-a-vm-to-the-lab) a začněte vytvářet virtuální počítač.
1. Zadejte požadované informace pro váš virtuální počítač a přidejte všechny artefakty, které chcete pro tento virtuální počítač.
1. Swtich na kartu **Upřesnit nastavení.** 
1. V dolní části okna Konfigurovat nastavení zvolte **Zobrazit šablonu ARM**.
1. Zkopírujte a uložte šablonu Správce prostředků, abyste ji později použili k vytvoření jiného virtuálního počítače.

   ![Šablona Správce prostředků, která se má uložit pro pozdější použití](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

Po uložení šablony Správce prostředků je nutné před použitím aktualizovat část parametrů šablony. Můžete vytvořit soubor parameter.json, který přizpůsobí pouze parametry mimo skutečnou šablonu Správce prostředků. 

![Přizpůsobení parametrů pomocí souboru JSON](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

Šablona Správce prostředků je teď připravená k [vytvoření virtuálního počítače](devtest-lab-create-environment-from-arm.md).

## <a name="set-expiration-date"></a>Nastavit datum vypršení platnosti
Ve scénářích, jako je školení, ukázky a zkušební verze, můžete chtít vytvořit virtuální počítače a odstranit je automaticky po pevné době trvání, takže vám nevzniknou zbytečné náklady. Virtuální ho virtuálního zařízení testovacího prostředí s datem vypršení platnosti můžete zadat vlastnost **expirationDate** pro virtuální ho. Podívejte se na stejnou šablonu Správce prostředků v [našem úložišti GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-customimage-with-expiration).



### <a name="next-steps"></a>Další kroky
* Přečtěte [si, jak vytvořit prostředí s více virtuálními počítačemi pomocí šablon Správce prostředků](devtest-lab-create-environment-from-arm.md).
* [Nasazení šablony Správce prostředků k vytvoření virtuálního počítače](devtest-lab-create-environment-from-arm.md#automate-deployment-of-environments)
* Prozkoumejte další šablony správce prostředků pro rychlý start pro automatizaci DevTest Labs z [veřejného úložiště DevTest Labs GitHub](https://github.com/Azure/azure-quickstart-templates).
