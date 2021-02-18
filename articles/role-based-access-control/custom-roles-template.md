---
title: Vytvoření nebo aktualizace vlastních rolí Azure pomocí šablony Azure Resource Manager – Azure RBAC
description: Naučte se vytvářet nebo aktualizovat vlastní role Azure pomocí šablony Azure Resource Manager (šablony ARM) a řízení přístupu na základě role v Azure (Azure RBAC).
services: role-based-access-control,azure-resource-manager
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 12/16/2020
ms.author: rolyon
ms.openlocfilehash: 0626a9e36d05ac9cb51f62652dbe6f3133bbc6d7
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095898"
---
# <a name="create-or-update-azure-custom-roles-using-an-arm-template"></a>Vytvoření nebo aktualizace vlastních rolí Azure pomocí šablony ARM

Pokud [předdefinované role Azure](built-in-roles.md) nevyhovují konkrétním potřebám vaší organizace, můžete vytvořit vlastní [role](custom-roles.md). Tento článek popisuje, jak vytvořit nebo aktualizovat vlastní roli pomocí šablony Azure Resource Manager (šablona ARM).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud chcete vytvořit vlastní roli, zadejte název role, oprávnění a místo, kde se dá role použít. V tomto článku vytvoříte roli s názvem _Custom role – Reader RG_ s oprávněními k prostředkům, která se dají přiřadit v oboru předplatného nebo v nižším rozsahu.

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsubscription-deployments%2Fcreate-role-def%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Pokud chcete vytvořit vlastní roli, musíte mít:

- Oprávnění k vytváření vlastních rolí, jako je [vlastník](built-in-roles.md#owner) nebo [Správce přístupu uživatelů](built-in-roles.md#user-access-administrator).

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto článku je ze [šablon Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/create-role-def). Šablona má čtyři parametry a oddíl Resources. Čtyři parametry jsou:

- Pole akcí s výchozí hodnotou `["Microsoft.Resources/subscriptions/resourceGroups/read"]` .
- Pole `notActions` s prázdnou výchozí hodnotou.
- Název role s výchozí hodnotou `Custom Role - RG Reader` .
- Popis role s výchozí hodnotou `Subscription Level Deployment of a Role Definition` .

Rozsah, ve kterém se dá tato vlastní role přiřadit, se nastaví na aktuální předplatné.

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-role-def/azuredeploy.json":::

Prostředek definovaný v šabloně je:

- [Microsoft. Authorization/roleDefinitions](/azure/templates/Microsoft.Authorization/roleDefinitions)

## <a name="deploy-the-template"></a>Nasazení šablony

Pomocí těchto kroků nasaďte předchozí šablonu.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Otevřete Azure Cloud Shell pro PowerShell.

1. Zkopírujte následující skript a vložte ho do Cloud Shell.

    ```azurepowershell-interactive
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"
    [string[]]$actions = Read-Host -Prompt "Enter actions as a comma-separated list (i.e. action1,action2)"
    $actions = $actions.Split(',')
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json"
    New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions
    ```

1. Zadejte umístění pro nasazení, jako je například `centralus` .

1. Zadejte seznam akcí pro vlastní roli jako seznam oddělený čárkami, například `Microsoft.Resources/resources/read,Microsoft.Resources/subscriptions/resourceGroups/read` .

1. V případě potřeby stiskněte klávesu ENTER a spusťte `New-AzDeployment` příkaz.

    Příkaz [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) nasadí šablonu pro vytvoření vlastní role.

    Zobrazený výstup by měl vypadat přibližně takto:

    ```azurepowershell-interactive
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

    ```azurepowershell-interactive
    Get-AzRoleDefinition "Custom Role - RG Reader" | ConvertTo-Json
    ```

    Zobrazený výstup by měl vypadat přibližně takto:

    ```azurepowershell-interactive
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

1. V nabídce vlevo vyberte **řízení přístupu (IAM)**.

1. Vyberte kartu **role** .

1. V seznamu **typ** nastavte **CustomRole**.

1. Ověřte, že je uvedená role **RG Reader vlastní role** .

   ![Nová vlastní role v Azure Portal](./media/custom-roles-template/custom-role-template-portal.png)

## <a name="update-a-custom-role"></a>Aktualizace vlastní role

Podobně jako při vytváření vlastní role můžete aktualizovat existující vlastní roli pomocí šablony. Chcete-li aktualizovat vlastní roli, je nutné zadat roli, kterou chcete aktualizovat.

Tady jsou změny, které byste museli udělat v předchozí šabloně pro rychlý Start, abyste mohli aktualizovat vlastní roli.

- Zahrňte ID role jako parametr.
    ```json
        ...
        "roleDefName": {
          "type": "string",
          "metadata": {
            "description": "ID of the role definition"
          }
        ...
    ```

- Do definice role přidejte parametr ID role.

    ```json
      ...
      "resources": [
        {
          "type": "Microsoft.Authorization/roleDefinitions",
          "apiVersion": "2018-07-01",
          "name": "[parameters('roleDefName')]",
          "properties": {
            ...
    ```

Tady je příklad, jak šablonu nasadit.

```azurepowershell
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"
[string[]]$actions = Read-Host -Prompt "Enter actions as a comma-separated list (i.e. action1,action2)"
$actions = $actions.Split(',')
$roleDefName = Read-Host -Prompt "Enter the role ID to update"
$templateFile = "rg-reader-update.json"
New-AzDeployment -Location $location -TemplateFile $templateFile -actions $actions -roleDefName $roleDefName
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li odebrat vlastní roli, postupujte podle těchto kroků.

1. Spuštěním následujícího příkazu odeberte vlastní roli.

    ```azurepowershell-interactive
    Get-AzRoleDefinition -Name "Custom Role - RG Reader" | Remove-AzRoleDefinition
    ```

1. Zadáním **Y** potvrďte, že chcete odebrat vlastní roli.

## <a name="next-steps"></a>Další kroky

- [Vysvětlení definic rolí Azure](role-definitions.md)
- [Rychlý Start: přiřazení role Azure pomocí šablony Azure Resource Manager](quickstart-role-assignments-template.md)
- [Dokumentace k šablonám ARM](../azure-resource-manager/templates/index.yml)
