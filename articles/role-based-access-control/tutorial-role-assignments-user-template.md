---
title: 'Kurz: udělení přístupu uživatelů k prostředkům Azure pomocí šablony RBAC a Správce prostředků'
description: Naučte se, jak udělit uživateli přístup k prostředkům Azure pomocí řízení přístupu na základě role (RBAC) pomocí šablony Azure Resource Manager v tomto kurzu.
services: role-based-access-control,azure-resource-manager
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 05/15/2019
ms.author: rolyon
ms.openlocfilehash: 96ca4f65d2def5f5004388c533410f09cc2a71fa
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138205"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-rbac-and-resource-manager-template"></a>Kurz: udělení přístupu uživatele k prostředkům Azure pomocí šablony RBAC a Správce prostředků

[Řízení přístupu na základě role (RBAC)](overview.md) je způsob, jakým můžete spravovat přístup k prostředkům Azure. V tomto kurzu vytvoříte skupinu prostředků a udělíte uživatelům přístup k vytváření a správě virtuálních počítačů ve skupině prostředků. Tento kurz se zaměřuje na proces nasazení šablony Správce prostředků pro udělení přístupu. Další informace o vývoji šablon Správce prostředků naleznete v [dokumentaci správce prostředků](/azure/azure-resource-manager/) a odkazu na [šablonu](/azure/templates/microsoft.authorization/allversions
).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Udělení přístupu pro uživatele v oboru skupiny prostředků
> * Ověření nasazení
> * Vyčištění

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

Chcete-li přidat a odebrat přiřazení rolí, je nutné mít následující:

* oprávnění `Microsoft.Authorization/roleAssignments/write` a `Microsoft.Authorization/roleAssignments/delete`, jako je například správce nebo [vlastník](built-in-roles.md#owner) [přístupu uživatele](built-in-roles.md#user-access-administrator)

## <a name="grant-access"></a>Udělení přístupu

Šablona použitá v tomto rychlém startu je ze [šablon Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/101-rbac-builtinrole-resourcegroup/). Další šablony související s autorizací Azure najdete [tady](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization).

Pokud chcete šablonu nasadit, vyberte **zkusit znovu** a otevřete službu Azure Cloud Shell a vložte do okna prostředí následující skript PowerShellu. Kód vložíte tak, že kliknete pravým tlačítkem myši na okno prostředí a pak vyberete **Vložit**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$emailAddress = Read-Host -Prompt "Enter your email address that is associated with your Azure subscription (used to find the principal ID)"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

$resourceGroupName = "${projectName}rg"
$roleAssignmentName = New-Guid
$principalId = (Get-AzAdUser -Mail $emailAddress).id
$roleDefinitionId = (Get-AzRoleDefinition -name "Virtual Machine Contributor").id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-rbac-builtinrole-resourcegroup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -roleAssignmentName $roleAssignmentName -roleDefinitionID $roleDefinitionId -principalId $principalId
```

## <a name="validate-the-deployment"></a>Ověření nasazení

1. Přihlaste se k webu [Portál Azure](https://portal.azure.com).
1. Otevřete skupinu prostředků vytvořenou v posledním postupu. Výchozí název je název projektu s připojeným **RG** .
1. Z nabídky vlevo vyberte **Řízení přístupu (IAM)** .
1. Vyberte **Přiřazení rolí**. 
1. Do pole **název**zadejte e-mailovou adresu, kterou jste zadali v posledním postupu. Zobrazí se uživatel s e-mailovou adresou role **Přispěvatel virtuálních počítačů** .

## <a name="clean-up"></a>Vyčištění

Pokud chcete odebrat skupinu prostředků vytvořenou v posledním postupu, vyberte **zkusit** , aby se otevřelo Azure Cloud Shell, a pak do okna prostředí vložte následující skript PowerShellu.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: udělení přístupu uživatele k prostředkům Azure pomocí RBAC a Azure PowerShell](tutorial-role-assignments-user-powershell.md)