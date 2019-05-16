---
title: Kurz – poskytnutí uživatel přístup k prostředkům Azure pomocí šablony Resource Manageru a RBAC | Dokumentace Microsoftu
description: Zjistěte, jak udělit přístup uživatelů k prostředkům Azure pomocí řízení přístupu na základě role (RBAC) pomocí šablony Azure Resource Manageru.
services: role-based-access-control,azure-resource-manager
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control,azure-resource-manager
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 05/15/2019
ms.author: rolyon
ms.openlocfilehash: e99a9d2cfa38c9b2ea74f9075b18f81006b34881
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65791523"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-rbac-and-resource-manager-template"></a>Kurz: Udělení přístupu uživatelů k prostředkům Azure pomocí šablony Resource Manageru a RBAC

[Řízení přístupu na základě role (RBAC)](overview.md) je způsob, která můžete spravovat přístup k prostředkům Azure. V tomto kurzu vytvořte skupinu prostředků a udělit přístup uživatelům vytvářet a spravovat virtuální počítače ve skupině prostředků. Tento kurz se zaměřuje na postup nasazení šablony Resource Manageru k udělení přístupu. Další informace o vývoji šablon Resource Manageru najdete v tématu [dokumentace ke službě Správce prostředků](/azure/azure-resource-manager/) a [referenčními informacemi k šablonám](/azure/templates/microsoft.authorization/allversions
).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Udělení přístupu pro uživatele v oboru skupiny prostředků
> * Ověření nasazení
> * Vyčištění

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Přidávat a odebírat přiřazení rolí, musíte mít:

* `Microsoft.Authorization/roleAssignments/write` a `Microsoft.Authorization/roleAssignments/delete` oprávnění, jako například [správce uživatelských přístupů](built-in-roles.md#user-access-administrator) nebo [vlastníka](built-in-roles.md#owner)

## <a name="grant-access"></a>Udělit přístup

Šablona použitá v tomto rychlém startu je z [šablony rychlý start Azure](https://azure.microsoft.com/resources/templates/101-rbac-builtinrole-resourcegroup/). Povolení Azure související šablony lze nalézt [tady](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization).

Pokud chcete nasadit šablonu, vyberte **vyzkoušet** otevřete Azure Cloud shell, a vložte následující skript prostředí PowerShell do okna prostředí. Vložte kód, pravým tlačítkem myši na okno prostředí a potom vyberte **vložte**.

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

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Otevřete skupinu prostředků, vytvořili v posledním postupu. Výchozí název je název projektu s **rg** připojí.
1. Z nabídky vlevo vyberte **Řízení přístupu (IAM)**.
1. Vyberte **přiřazení rolí**. 
1. V **název**, zadejte e-mailovou adresu, které jste zadali v posledním postupu. Zobrazí uživatele s e-mailovou adresu má **Přispěvatel virtuálních počítačů** role.

## <a name="clean-up"></a>Vyčištění

K odebrání skupiny prostředků vytvořené v posledním postupu, vyberte **vyzkoušet** otevřete Azure Cloud shell, a vložte následující skript prostředí PowerShell do okna prostředí.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz: Udělení přístupu uživatelů k prostředkům Azure pomocí RBAC a Azure Powershellu](tutorial-role-assignments-user-powershell.md)