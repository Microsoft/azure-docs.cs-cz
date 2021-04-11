---
title: Vytvoření řídicího panelu Azure Portal pomocí šablony Azure Resource Manager
description: Naučte se vytvořit řídicí panel Azure Portal pomocí šablony Azure Resource Manager.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/15/2021
ms.openlocfilehash: a3ab8767e09256ed8235dbd980ea3336a6f0fb1d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104658317"
---
# <a name="quickstart-create-a-dashboard-in-the-azure-portal-by-using-an-arm-template"></a>Rychlý Start: Vytvoření řídicího panelu v Azure Portal pomocí šablony ARM

Řídicí panel v Azure Portal je cílené a organizované zobrazení vašich cloudových prostředků. Tento rychlý Start se zaměřuje na proces nasazení šablony Azure Resource Manager (šablona ARM) k vytvoření řídicího panelu. Řídicí panel zobrazuje výkon virtuálního počítače a také některé statické informace a odkazy.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-portal-dashboard%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
- Existující virtuální počítač.

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Řídicí panel, který vytvoříte v další části tohoto rychlého startu, vyžaduje existující virtuální počítač. Pomocí následujících kroků vytvořte virtuální počítač.

1. V Azure Portal vyberte **Cloud Shell**.

    ![Výběr Cloud shellu na pásu Azure Portal](media/quick-create-template/cloud-shell.png)

1. V okně **Cloud Shell** vyberte **PowerShell**.

    ![V okně terminálu vyberte PowerShell.](media/quick-create-template/powershell.png)

1. Zkopírujte následující příkaz a zadejte ho do příkazového řádku, abyste mohli vytvořit skupinu prostředků.

    ```powershell
    New-AzResourceGroup -Name SimpleWinVmResourceGroup -Location EastUS
    ```

    ![Zkopírování příkazu do příkazového řádku](media/quick-create-template/command-prompt.png)

1. Zkopírujte následující příkaz a zadejte ho do příkazového řádku a vytvořte virtuální počítač ve skupině prostředků.

    ```powershell
    New-AzVm `
        -ResourceGroupName "SimpleWinVmResourceGroup" `
        -Name "SimpleWinVm" `
        -Location "East US" 
    ```

1. Zadejte uživatelské jméno a heslo pro virtuální počítač. Toto je nové uživatelské jméno a heslo. Nejedná se například o účet, který používáte k přihlášení do Azure. Další informace najdete v tématu [požadavky na uživatelské jméno](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm) a [požadavky na heslo](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

    Nasazení virtuálního počítače se teď spustí a obvykle trvá několik minut, než se dokončí. Po dokončení nasazení přejděte k další části.

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-azure-portal-dashboard/). Šablona pro tento článek je moc dlouhá, takže se tady nedá zobrazit. Chcete-li zobrazit šablonu, přečtěte si téma [azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-portal-dashboard/azuredeploy.json). Jeden prostředek Azure je definovaný v šabloně, [Microsoft. Portal/řídicích panelech](/azure/templates/microsoft.portal/dashboards) – vytvoří řídicí panel v Azure Portal.

## <a name="deploy-the-template"></a>Nasazení šablony

1. Vyberte následující obrázek a přihlaste se k Azure a otevřete šablonu.

    [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-portal-dashboard%2Fazuredeploy.json)

1. Vyberte nebo zadejte následující hodnoty a pak vyberte **zkontrolovat + vytvořit**.

    ![Šablona ARM, vytvoření řídicího panelu, nasazení portálu](media/quick-create-template/create-dashboard-using-template-portal.png)

    Pokud není zadaný, použijte k vytvoření řídicího panelu výchozí hodnoty.

    * **Předplatné:** Vyberte předplatné Azure.
    * **Skupina prostředků**: vyberte **SimpleWinVmResourceGroup**.
    * **Umístění**: vyberte **východní USA**.
    * **Název virtuálního počítače**: zadejte **SimpleWinVm**.
    * **Skupina prostředků virtuálního počítače**: zadejte **SimpleWinVmResourceGroup**.

1. Vyberte **vytvořit** nebo **koupit**. Po úspěšném nasazení řídicího panelu se zobrazí oznámení:

    ![Šablona ARM, vytvoření řídicího panelu, nasazení oznámení na portálu](media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

Azure Portal se použila k nasazení šablony. Kromě webu Azure Portal můžete také použít také Azure PowerShell, Azure CLI a REST API. Další informace o dalších metodách nasazení najdete v tématu [Nasazení šablon](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

[!INCLUDE [azure-portal-review-deployed-resources](../../includes/azure-portal-review-deployed-resources.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat virtuální počítač a přidružený řídicí panel, odstraňte skupinu prostředků, která je obsahuje.

1. V Azure Portal vyhledejte **SimpleWinVmResourceGroup** a potom ho vyberte ve výsledcích hledání.

1. Na stránce **SimpleWinVmResourceGroup** vyberte **Odstranit skupinu prostředků**, zadejte název skupiny prostředků, který chcete potvrdit, a pak vyberte **Odstranit**.

    ![Odstranění skupiny prostředků](media/quick-create-template/delete-resource-group.png)

## <a name="next-steps"></a>Další kroky

Další informace o řídicích panelech v Azure Portal najdete v tématech:

> [!div class="nextstepaction"]
> [Vytváření a sdílení řídicích panelů na webu Azure Portal](azure-portal-dashboards.md)