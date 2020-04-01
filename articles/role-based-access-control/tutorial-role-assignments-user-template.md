---
title: 'Kurz: Udělení přístupu uživatelů k prostředkům Azure pomocí šablony RBAC a Správce prostředků'
description: Zjistěte, jak udělit uživateli přístup k prostředkům Azure pomocí řízení přístupu na základě rolí (RBAC) pomocí šablony Azure Resource Manager v tomto kurzu.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77138205"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-rbac-and-resource-manager-template"></a>Kurz: Udělení přístupu uživateli k prostředkům Azure pomocí šablony RBAC a Resource Manager

[Řízení přístupu na základě rolí (RBAC)](overview.md) je způsob, jakým spravujete přístup k prostředkům Azure. V tomto kurzu vytvoříte skupinu prostředků a udělíte uživateli přístup k vytváření a správě virtuálních počítačů ve skupině prostředků. Tento kurz se zaměřuje na proces nasazování šablony Správce prostředků k udělení přístupu. Další informace o vývoji šablon Správce prostředků naleznete v [dokumentaci ke Správci prostředků](/azure/azure-resource-manager/) a [v odkazu na šablonu](/azure/templates/microsoft.authorization/allversions
).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Udělení přístupu uživateli v oboru skupiny prostředků
> * Ověření nasazení
> * Vyčištění

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li přidat a odebrat přiřazení rolí, musíte mít:

* `Microsoft.Authorization/roleAssignments/write`a `Microsoft.Authorization/roleAssignments/delete` oprávnění, například [Správce přístupu uživatelů](built-in-roles.md#user-access-administrator) nebo [Vlastník](built-in-roles.md#owner)

## <a name="grant-access"></a>Udělení přístupu

Šablona použitá v tomto rychlém startu je ze [šablon rychlého startu Azure](https://azure.microsoft.com/resources/templates/101-rbac-builtinrole-resourcegroup/). Další šablony související s autorizací Azure najdete [zde](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization).

Pokud chcete šablonu nasadit, vyberte **Vyzkoušet,** chcete otevřít prostředí Azure Cloud a pak do okna prostředí vložte následující skript Prostředí PowerShell. Pokud chcete kód vložit, klikněte pravým tlačítkem myši na okno prostředí a pak vyberte **Vložit**.

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

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Otevřete skupinu prostředků vytvořenou v posledním postupu. Výchozí název je název projektu s **rg** připojen.
1. Z nabídky vlevo vyberte **Řízení přístupu (IAM)**.
1. Vyberte **Přiřazení rolí**. 
1. V **části Název**zadejte e-mailovou adresu, kterou jste zadali v posledním postupu. Uvidíte, že uživatel s e-mailovou adresou má roli **přispěvatele virtuálního počítače.**

## <a name="clean-up"></a>Vyčištění

Pokud chcete odebrat skupinu prostředků vytvořenou v posledním postupu, vyberte **Try it** to open the Azure Cloud shell a potom vložte následující skript Prostředí PowerShell do okna prostředí.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: Udělení přístupu uživateli k prostředkům Azure pomocí RBAC a Azure PowerShellu](tutorial-role-assignments-user-powershell.md)