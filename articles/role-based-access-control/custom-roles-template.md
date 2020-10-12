---
title: Vytvoření vlastní role Azure pomocí šablony Azure Resource Manager – Azure RBAC
description: Naučte se, jak vytvořit vlastní roli Azure pomocí šablon Azure Resource Manager a řízení přístupu na základě role v Azure (Azure RBAC).
services: role-based-access-control,azure-resource-manager
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.custom: subject-armqs
ms.workload: identity
ms.date: 06/25/2020
ms.author: rolyon
ms.openlocfilehash: bcf1966ffc326291448cb611d99390fe0d652151
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85392899"
---
# <a name="create-an-azure-custom-role-using-an-azure-resource-manager-template"></a>Vytvoření vlastní role Azure pomocí šablony Azure Resource Manager

Pokud [předdefinované role Azure](built-in-roles.md) nevyhovují konkrétním potřebám vaší organizace, můžete vytvořit vlastní [role](custom-roles.md). Tento článek popisuje, jak vytvořit vlastní roli pomocí šablony Azure Resource Manager.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Požadavky

Pokud chcete vytvořit vlastní roli, musíte mít:

- Oprávnění k vytváření vlastních rolí, například [Vlastník](built-in-roles.md#owner) nebo [Správce přístupu uživatelů](built-in-roles.md#user-access-administrator)

## <a name="create-a-custom-role"></a>Vytvoření vlastní role

Pokud chcete vytvořit vlastní roli, zadejte název role, oprávnění a místo, kde se dá role použít. V tomto článku vytvoříte roli s názvem "Custom role – RG Reader" s oprávněními k prostředkům, která se dají přiřadit v oboru předplatného nebo na nižší úrovni.

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto článku je ze [šablon Azure pro rychlý Start](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments/create-role-def). Šablona má čtyři parametry a oddíl Resources. Čtyři parametry jsou:

- Pole akcí s výchozí hodnotou ["Microsoft. Resources/Subscriptions/resourceGroups/Read"]
- Pole notActions s prázdnou výchozí hodnotou
- Název role s výchozí hodnotou "Custom role – RG Reader"
- Popis role s výchozí hodnotou "nasazení na úrovni předplatného definice role"

Prostředek definovaný v šabloně je:

- [Microsoft. Authorization/roleDefinitions](/azure/templates/Microsoft.Authorization/roleDefinitions)

Rozsah, ve kterém se dá tato vlastní role přiřadit, se nastaví na aktuální předplatné.

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-role-def/azuredeploy.json":::

### <a name="deploy-the-template"></a>Nasazení šablony

Pomocí těchto kroků nasaďte předchozí šablonu.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

1. Otevřete Azure Cloud Shell pro PowerShell.

1. Zkopírujte následující skript a vložte ho do Cloud Shell.

    ```azurepowershell
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"
    [string[]]$actions = Read-Host -Prompt "Enter actions as a comma-separated list (i.e. action1,action2)"
    $actions = $actions.Split(',')

    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json"

    New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions
    ```

1. Zadejte umístění pro nasazení, například *centralus*.

1. Zadejte seznam akcí pro vlastní roli jako seznam oddělený čárkami, jako je Microsoft. Resources */Resources/Read, Microsoft. Resources/Subscriptions/resourceGroups/Read*.

1. V případě potřeby stiskněte klávesu ENTER a spusťte příkaz New-AzDeployment.

    Příkaz [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) nasadí šablonu pro vytvoření vlastní role.

    Zobrazený výstup by měl vypadat přibližně takto:

    ```azurepowershell
    PS> New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions
    
    Id                      : /subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/azuredeploy
    DeploymentName          : azuredeploy
    Location                : centralus
    ProvisioningState       : Succeeded
    Timestamp               : 6/25/2020 8:08:32 PM
    Mode                    : Incremental
    TemplateLink            :
                              Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json
                              ContentVersion : 1.0.0.0
    
    Parameters              :
                              Name               Type                       Value
                              =================  =========================  ==========
                              actions            Array                      [
                                "Microsoft.Resources/resources/read",
                                "Microsoft.Resources/subscriptions/resourceGroups/read"
                              ]
                              notActions         Array                      []
                              roleName           String                     Custom Role - RG Reader
                              roleDescription    String                     Subscription Level Deployment of a Role Definition
    
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Pomocí těchto kroků ověříte, že se vlastní role vytvořila.

1. Pro zobrazení seznamu vlastní role spusťte příkaz [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) .

    ```azurepowershell
    Get-AzRoleDefinition "Custom Role - RG Reader" | ConvertTo-Json
    ```

    Zobrazený výstup by měl vypadat přibližně takto:

    ```azurepowershell
    {
      "Name": "Custom Role - RG Reader",
      "Id": "11111111-1111-1111-1111-111111111111",
      "IsCustom": true,
      "Description": "Subscription Level Deployment of a Role Definition",
      "Actions": [
        "Microsoft.Resources/resources/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/{subscriptionId}"
      ]
    }
    ```

1. V Azure Portal otevřete své předplatné.

1. V nabídce vlevo klikněte na **řízení přístupu (IAM)**.

1. Klikněte na kartu **role** .

1. V seznamu **typ** nastavte **CustomRole**.

1. Ověřte, že je uvedená role **RG Reader vlastní role** .

   ![Nová vlastní role v Azure Portal](./media/custom-roles-template/custom-role-template-portal.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li odebrat vlastní roli, postupujte podle těchto kroků.

1. Spuštěním následujícího příkazu odeberte vlastní roli.

    ```azurepowershell
    Get-AzRoleDefinition -Name "Custom Role - RG Reader" | Remove-AzRoleDefinition
    ```

1. Zadáním **Y** potvrďte, že chcete odebrat vlastní roli.

## <a name="next-steps"></a>Další kroky

- [Vysvětlení definic rolí Azure](role-definitions.md)
- [Rychlý Start: přidání přiřazení role Azure pomocí šablony Azure Resource Manager](quickstart-role-assignments-template.md)
- [Dokumentace k šablonám ARM](../azure-resource-manager/templates/index.yml)
