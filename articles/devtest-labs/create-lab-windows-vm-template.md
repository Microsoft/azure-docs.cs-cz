---
title: Vytvoření testovacího prostředí pomocí Azure DevTest Labs a šablony Azure Resource Manager
description: V tomto rychlém startu vytvoříte testovací prostředí v Azure DevTest Labs pomocí šablony Azure Resource Manager (šablona ARM). Správce testovacího prostředí nastaví testovací prostředí, vytvoří virtuální počítače v testovacím prostředí a nakonfiguruje zásady.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/26/2020
ms.openlocfilehash: 2b825b4d4485f401199556b6faaef0017f583cc1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91461185"
---
# <a name="quickstart-set-up-a-lab-by-using-azure-devtest-labs-arm-template"></a>Rychlý Start: nastavení testovacího prostředí pomocí šablony Azure DevTest Labs ARM
V tomto rychlém startu vytvoříte testovací prostředí pomocí virtuálního počítače s Windows serverem 2019 Datacenter pomocí šablony Azure Resource Manager (šablona ARM). 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

V tomto rychlém startu provedete následující akce:

> [!div class="checklist"]
> * Kontrola šablony 
> * Nasazení šablony
> * Ověření šablony
> * Vyčištění prostředků

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-dtl-create-lab-windows-vm%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-dtl-create-lab-windows-vm/).

:::code language="json" source="~/quickstart-templates/101-dtl-create-lab-windows-vm/azuredeploy.json":::

Mezi prostředky, které jsou definované v šabloně, patří:

- [**Microsoft. DevTestLab/Labs**](/azure/templates/microsoft.devtestlab/labs)
- [**Microsoft. DevTestLab Labs/virtualnetworks**](/azure/templates/microsoft.devtestlab/labs/virtualnetworks)
- [**Microsoft. DevTestLab Labs/VirtualMachines**](/azure/templates/microsoft.devtestlab/labs/virtualmachines)

Další ukázky šablon najdete v tématu [šablony rychlý Start pro Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devtestlab).

## <a name="deploy-the-template"></a>Nasazení šablony
Chcete-li spustit nasazení automaticky, klikněte na následující tlačítko. 

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-dtl-create-lab-windows-vm%2Fazuredeploy.json)

1. Vytvořte **novou skupinu prostředků** , aby ji bylo možné snadno vyčistit později.
1. Vyberte **umístění** pro skupinu prostředků. 
1. Zadejte **název testovacího prostředí**. 
1. Zadejte **název virtuálního počítače**. 
1. Zadejte **uživatelské jméno** , které má přístup k virtuálnímu počítači. 
1. Zadejte **heslo** pro tohoto uživatele. 
1. Vyberte Souhlasím **s podmínkami a ujednáními uvedenými nahoře**. 
1. Pak vyberte **koupit**.

    :::image type="content" source="./media/create-lab-windows-vm-template/deploy-template-page.png" alt-text="Nasadit stránku šablony":::

## <a name="validate-the-deployment"></a>Ověření nasazení
1. Výběrem **oznámení** v horní části zobrazíte stav nasazení a kliknete na odkaz **probíhá nasazení** .

    :::image type="content" source="./media/create-lab-windows-vm-template/deployment-notification.png" alt-text="Oznámení o nasazení":::
2. Na stránce **nasazení – přehled** počkejte, až se nasazení dokončí. Dokončení této operace (zejména vytváření virtuálního počítače) trvá nějakou dobu. Pak vyberte svou položku **Přejít na skupinu prostředků** nebo **název skupiny prostředků** , jak je znázorněno na následujícím obrázku: 

    :::image type="content" source="./media/create-lab-windows-vm-template/navigate-resource-group.png" alt-text="Přejít do skupiny prostředků":::
3. Na stránce **Skupina prostředků** se zobrazí seznam prostředků ve skupině prostředků. Ověřte, že se v prostředcích zobrazuje testovací prostředí typu: `DevTest Lab` . Závislé prostředky, jako je třeba virtuální síť a virtuální počítač, se zobrazí také ve skupině prostředků. 

    :::image type="content" source="./media/create-lab-windows-vm-template/resource-group-home-page.png" alt-text="Domovská stránka skupiny prostředků":::
4. Vyberte testovací prostředí ze seznamu prostředků, abyste viděli domovskou stránku vašeho testovacího prostředí. Ověřte, že je v seznamu **moje virtuální počítače** ZOBRAZENÝ virtuální počítač datacenter Windows serveru 2019. Na následujícím obrázku je minimalizován oddíl **základy** . 

    :::image type="content" source="./media/create-lab-windows-vm-template/lab-home-page.png" alt-text="Domovská stránka pro testovací prostředí":::

    > [!IMPORTANT] 
    > Tuto stránku nechte otevřenou a postupujte podle pokynů v další části, abyste vyčistili prostředky, abyste předešli nákladům na spuštění testovacího prostředí a virtuálního počítače v Azure. Pokud si chcete projít další kurz, abyste otestovali přístup k virtuálnímu počítači v testovacím prostředí, vyčistěte po absolvování tohoto kurzu prostředky. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

1. Nejprve odstraňte testovací prostředí, abyste mohli odstranit skupinu prostředků. Nebudete moct odstranit skupinu prostředků s testovacím prostředím. Testovací prostředí odstraníte tak, že na panelu nástrojů vyberete **Odstranit** . 

    :::image type="content" source="./media/create-lab-windows-vm-template/delete-lab-button.png" alt-text="Tlačítko Odstranit testovací prostředí":::
 2. Na stránce potvrzení zadejte **název testovacího prostředí** a vyberte **Odstranit**. 
 3. Počkejte, než se testovací prostředí odstraní. Výběrem ikony **zvonku** zobrazíte oznámení od operace odstranění. Tento proces trvá nějakou dobu. Potvrďte odstranění testovacího prostředí a pak vyberte **skupinu prostředků** v nabídce s popisem cesty. 
 
    :::image type="content" source="./media/create-lab-windows-vm-template/confirm-lab-deletion.png" alt-text="Potvrďte odstranění virtuálního počítače v oznámeních":::
 1. Na stránce **Skupina prostředků** vyberte z panelu nástrojů **Odstranit skupinu prostředků** . Na stránce potvrzení zadejte **název skupiny prostředků** a vyberte **Odstranit**. Zkontrolujte oznámení a potvrďte, že se skupina prostředků odstranila.
 
    :::image type="content" source="./media/create-lab-windows-vm-template/delete-resource-group-button.png" alt-text="Tlačítko Odstranit skupinu prostředků":::

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste vytvořili testovací prostředí s virtuálním počítačem. Další informace o tom, jak se dostat do testovacího prostředí, najdete v dalším kurzu:

> [!div class="nextstepaction"]
> [Kurz: Přístup k testovacímu prostředí](tutorial-use-custom-lab.md)
