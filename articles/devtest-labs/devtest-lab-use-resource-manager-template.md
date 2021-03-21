---
title: Zobrazení a použití šablony Azure Resource Manager virtuálního počítače
description: Naučte se používat Azure Resource Manager šablonu z virtuálního počítače k vytvoření dalších virtuálních počítačů.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: fb7cb38dfb2c1664017f0e342fefecdd464df45f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97696012"
---
# <a name="create-virtual-machines-using-an-azure-resource-manager-template"></a>Vytváření virtuálních počítačů pomocí šablony Azure Resource Manager 

Když vytváříte virtuální počítač v DevTest Labs prostřednictvím [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040), můžete před ULOŽENÍM virtuálního počítače zobrazit Azure Resource Manager šablonu. Šablonu je pak možné použít jako základ pro vytvoření více virtuálních počítačů testovacího prostředí se stejným nastavením.

Tento článek popisuje více virtuálních počítačů a Správce prostředků šablon pro jeden virtuální počítač a ukazuje, jak zobrazit a uložit šablonu při vytváření virtuálního počítače.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>Více virtuálních počítačů a Správce prostředků šablon pro jeden virtuální počítač
Existují dva způsoby, jak vytvořit virtuální počítače v DevTest Labs pomocí šablony Správce prostředků: zřízení prostředku Microsoft. DevTestLab/Labs/VirtualMachines nebo zřízení prostředku Microsoft. COMPUTE/VirtualMachines. Každý se používá v různých scénářích a vyžaduje jiná oprávnění.

- Správce prostředků šablony, které používají typ prostředku Microsoft. DevTestLab/Labs/VirtualMachines (jak je deklarované v šabloně "prostředku") mohou zřídit jednotlivé virtuální počítače v testovacím prostředí. Každý virtuální počítač se pak v seznamu virtuálních počítačů DevTest Labs zobrazí jako jedna položka:

   ![Snímek obrazovky, který zobrazuje seznam virtuálních počítačů jako jednu položku v seznamu virtuálních počítačů DevTest Labs.](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   Tento typ šablony Správce prostředků lze zřídit pomocí příkazu Azure PowerShell **New-AzResourceGroupDeployment** nebo pomocí příkazu Azure CLI **AZ Deployment Group Create**. Vyžaduje oprávnění správce, takže uživatelé, kteří jsou přiřazeni pomocí role uživatele DevTest Labs, nemůžou nasazení provést. 

- Správce prostředků šablony, které používají typ prostředku Microsoft. COMPUTE/VirtualMachines, mohou zřídit několik virtuálních počítačů jako jedno prostředí v seznamu virtuálních počítačů DevTest Labs:

   ![Seznam virtuálních počítačů jako jedné položky v seznamu virtuálních počítačů DevTest Labs](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   Virtuální počítače ve stejném prostředí je možné spravovat společně a sdílet stejný životní cyklus. Uživatelé, kteří jsou přiřazeni pomocí role uživatele DevTest Labs, mohou vytvářet prostředí pomocí těchto šablon, pokud správce nakonfigurovali testovací prostředí tímto způsobem.

Zbývající část tohoto článku se zabývá Správce prostředkůmi šablonami, které používají Microsoft. DevTestLab/Labs/VirtualMachines. Používají je správci testovacího prostředí k automatizaci vytváření virtuálních počítačů testovacího prostředí (například s nárokem na virtuální počítače) nebo při generování zlatých imagí (například objekt pro vytváření imagí).

[Osvědčené postupy pro vytváření šablon Azure Resource Manager](../azure-resource-manager/templates/template-best-practices.md) nabízí mnoho pokynů a návrhů, které vám pomůžou vytvořit Azure Resource Manager šablony, které jsou spolehlivé a snadno použitelné.

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>Zobrazení a uložení šablony Správce prostředků virtuálního počítače
1. Pokud chcete začít vytvářet virtuální počítač, postupujte podle kroků v části [Vytvoření prvního virtuálního počítače v testovacím prostředí](tutorial-create-custom-lab.md#add-a-vm-to-the-lab) .
1. Zadejte požadované informace pro virtuální počítač a přidejte všechny artefakty, které chcete pro tento virtuální počítač.
1. Přepněte na kartu **Upřesnit nastavení** . 
1. V dolní části okna Konfigurovat nastavení vyberte možnost **Zobrazit šablonu ARM**.
1. Zkopírujte a uložte šablonu Správce prostředků pro pozdější použití k vytvoření dalšího virtuálního počítače.

   ![Správce prostředků šablonu, která se má uložit pro pozdější použití](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

Po uložení šablony Správce prostředků musíte aktualizovat oddíl Parameters (parametry), abyste mohli šablonu použít. Můžete vytvořit parameter.jsv, který přizpůsobuje pouze parametry, mimo skutečnou šablonu Správce prostředků. 

![Přizpůsobení parametrů pomocí souboru JSON](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

Šablona Správce prostředků je teď připravená k použití k [Vytvoření virtuálního počítače](devtest-lab-create-environment-from-arm.md).

## <a name="set-expiration-date"></a>Nastavit datum vypršení platnosti
Ve scénářích, jako jsou například školení, ukázky a zkušební verze, můžete chtít vytvořit virtuální počítače a odstranit je automaticky po pevné době trvání, takže nebudete mít zbytečné náklady. Virtuální počítač testovacího prostředí s datem vypršení platnosti můžete vytvořit zadáním vlastnosti **expirationDate** pro virtuální počítač. Podívejte se na stejnou šablonu Správce prostředků v [našem úložišti GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-customimage-with-expiration).



### <a name="next-steps"></a>Další kroky
* Naučte [se vytvářet prostředí s několika virtuálními počítači pomocí šablon Správce prostředků](devtest-lab-create-environment-from-arm.md).
* [Nasazení šablony Správce prostředků k vytvoření virtuálního počítače](devtest-lab-create-environment-from-arm.md#automate-deployment-of-environments)
* Prozkoumejte další šablony Správce prostředků pro rychlý Start pro automatizaci DevTest Labs z [úložiště GitHub veřejné DevTest Labs](https://github.com/Azure/azure-quickstart-templates).
