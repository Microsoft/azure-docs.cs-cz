---
title: 'Rychlý Start: přidání přiřazení role Azure pomocí šablony Azure Resource Manager – Azure RBAC'
description: Naučte se, jak udělit přístup k prostředkům Azure pro uživatele v oboru skupiny prostředků pomocí šablon Azure Resource Manager a řízení přístupu na základě role v Azure (Azure RBAC).
services: role-based-access-control,azure-resource-manager
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: quickstart
ms.custom: subject-armqs
ms.workload: identity
ms.date: 05/21/2020
ms.author: rolyon
ms.openlocfilehash: 56d2647c3da84e9534220e7b237ea24b2b45e8b3
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/26/2020
ms.locfileid: "83854574"
---
# <a name="quickstart-add-an-azure-role-assignment-using-an-azure-resource-manager-template"></a>Rychlý Start: přidání přiřazení role Azure pomocí šablony Azure Resource Manager

[Řízení přístupu na základě role Azure (Azure RBAC)](overview.md) je způsob, jakým můžete spravovat přístup k prostředkům Azure. V tomto rychlém startu vytvoříte skupinu prostředků a udělíte uživateli přístup k vytváření a správě virtuálních počítačů ve skupině prostředků. Tento rychlý Start používá pro udělení přístupu šablonu Správce prostředků.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li přidat přiřazení rolí, je nutné mít následující:

* `Microsoft.Authorization/roleAssignments/write`a `Microsoft.Authorization/roleAssignments/delete` oprávnění, jako je například správce nebo [vlastník](built-in-roles.md#owner) [přístupu uživatele](built-in-roles.md#user-access-administrator)

## <a name="create-a-role-assignment"></a>Vytvoření přiřazení role

Chcete-li přidat přiřazení role, je nutné zadat tři prvky: objekt zabezpečení, definice role a obor. V tomto rychlém startu je objekt zabezpečení vy nebo jiný uživatel ve vašem adresáři, definice role je [Přispěvatel virtuálních počítačů](built-in-roles.md#virtual-machine-contributor)a obor je skupina prostředků, kterou určíte.

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/101-rbac-builtinrole-resourcegroup/). Šablona má tři parametry a oddíl Resources. V části Resources (prostředky) si všimněte, že má tři prvky přiřazení role: objekt zabezpečení, definice role a obor. 

:::code language="json" source="~/quickstart-templates/101-rbac-builtinrole-resourcegroup/azuredeploy.json" highlight="30-32":::

### <a name="deploy-the-template"></a>Nasazení šablony

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

1. Určete svou e-mailovou adresu, která je přidružená k vašemu předplatnému Azure. Nebo určete e-mailovou adresu jiného uživatele ve vašem adresáři.

1. Otevřete Azure Cloud Shell pro PowerShell.

1. Zkopírujte následující skript a vložte ho do Cloud Shell.

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter a resource group name (i.e. ExampleGrouprg)"
    $emailAddress = Read-Host -Prompt "Enter an email address for a user in your directory"
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"
    
    $roleAssignmentName = New-Guid
    $principalId = (Get-AzAdUser -Mail $emailAddress).id
    $roleDefinitionId = (Get-AzRoleDefinition -name "Virtual Machine Contributor").id
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-rbac-builtinrole-resourcegroup/azuredeploy.json"
    
    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -roleAssignmentName $roleAssignmentName -roleDefinitionID $roleDefinitionId -principalId $principalId
    ```

1. Zadejte název skupiny prostředků, například ExampleGrouprg.

1. Zadejte e-mailovou adresu pro sebe nebo jiného uživatele ve vašem adresáři.

1. Zadejte umístění pro skupinu prostředků, například centralus.

1. V případě potřeby stiskněte klávesu ENTER a spusťte příkaz New-AzResourceGroupDeployment.

    Příkaz [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) vytvoří novou skupinu prostředků a příkaz [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) nasadí šablonu pro přidání přiřazení role.

    Zobrazený výstup by měl vypadat přibližně takto:

    ```azurepowershell
    PS> New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -roleAssignmentName $roleAssignmentName -roleDefinitionID $roleDefinitionId -principalId $principalId
    
    DeploymentName          : azuredeploy
    ResourceGroupName       : ExampleGrouprg
    ProvisioningState       : Succeeded
    Timestamp               : 5/22/2020 9:01:30 PM
    Mode                    : Incremental
    TemplateLink            :
                              Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-rbac-builtinrole-resourcegroup/azuredeploy.json
                              ContentVersion : 1.0.0.0
    
    Parameters              :
                              Name                  Type                       Value
                              ====================  =========================  ==========
                              roleAssignmentName    String                     {roleAssignmentName}
                              roleDefinitionID      String                     9980e02c-c2be-4d73-94e8-173b1dc7cf3c
                              principalId           String                     {principalId}
    
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

1. V Azure Portal otevřete skupinu prostředků, kterou jste vytvořili.

1. V nabídce vlevo klikněte na **řízení přístupu (IAM)**.

1. Klikněte na kartu **Přiřazení rolí**.

1. Ověřte, jestli je role **Přispěvatel virtuálního počítače** přiřazená k zadanému uživateli.

   ![Přiřazení nové role](./media/quickstart-role-assignments-template/role-assignment-portal.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat přiřazení role a skupinu prostředků, kterou jste vytvořili, postupujte podle těchto kroků.

1. Zkopírujte následující skript a vložte ho do Cloud Shell.

    ```azurepowershell
    $emailAddress = Read-Host -Prompt "Enter the email address of the user with the role assignment to remove"
    $resourceGroupName = Read-Host -Prompt "Enter the resource group name to remove (i.e. ExampleGrouprg)"
    
    $principalId = (Get-AzAdUser -Mail $emailAddress).id
    
    Remove-AzRoleAssignment -ObjectId $principalId -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName $resourceGroupName
    Remove-AzResourceGroup -Name $resourceGroupName
    ```
    
1. Zadejte e-mailovou adresu uživatele s přiřazením role, kterou chcete odebrat.

1. Zadejte název skupiny prostředků, který chcete odebrat, například ExampleGrouprg.

1. V případě potřeby stiskněte klávesu ENTER a spusťte příkaz Remove-AzResourceGroup.

1. Zadáním **Y** potvrďte, že chcete odebrat skupinu prostředků.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: udělení přístupu uživatele k prostředkům Azure pomocí Azure PowerShell](tutorial-role-assignments-user-powershell.md)