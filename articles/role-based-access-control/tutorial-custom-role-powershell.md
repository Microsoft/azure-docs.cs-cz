---
title: Tutorial - Create a custom role for Azure resources using Azure PowerShell
description: Get started creating a custom role for Azure resources using Azure PowerShell in this tutorial.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/20/2019
ms.author: rolyon
ms.openlocfilehash: 176e465163d92156308eda64c4187467cc10ee15
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2019
ms.locfileid: "74419746"
---
# <a name="tutorial-create-a-custom-role-for-azure-resources-using-azure-powershell"></a>Tutorial: Create a custom role for Azure resources using Azure PowerShell

If the [built-in roles for Azure resources](built-in-roles.md) don't meet the specific needs of your organization, you can create your own custom roles. V tomto kurzu pomocí Azure PowerShellu vytvoříte vlastní roli Čtenář lístků podpory. The custom role allows the user to view everything in the management plane of a subscription and also open support tickets.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření vlastní role
> * Výpis vlastních rolí
> * Aktualizace vlastní role
> * Odstranění vlastní role

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Předpoklady

Pro absolvování tohoto kurzu potřebujete:

- Oprávnění k vytváření vlastních rolí, například [Vlastník](built-in-roles.md#owner) nebo [Správce přístupu uživatelů](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) or [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="sign-in-to-azure-powershell"></a>Přihlášení k Azure PowerShellu

Přihlaste se k [Azure PowerShellu](/powershell/azure/authenticate-azureps).

## <a name="create-a-custom-role"></a>Vytvoření vlastní role

Nejjednodušší způsob, jak vytvořit vlastní roli, je začít s předdefinovanou rolí, upravit ji a pak vytvořit novou roli.

1. In PowerShell, use the [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) command to get the list of operations for the Microsoft.Support resource provider. Je užitečné znát operace, které máte k dispozici k vytváření oprávnění. Seznam všech operací najdete také v tématu [Operace poskytovatele prostředků Azure Resource Manageru](resource-provider-operations.md#microsoftsupport).

    ```azurepowershell
    Get-AzProviderOperation "Microsoft.Support/*" | FT Operation, Description -AutoSize
    ```
    
    ```Output
    Operation                              Description
    ---------                              -----------
    Microsoft.Support/register/action      Registers to Support Resource Provider
    Microsoft.Support/supportTickets/read  Gets Support Ticket details (including status, severity, contact ...
    Microsoft.Support/supportTickets/write Creates or Updates a Support Ticket. You can create a Support Tic...
    ```

1. Use the [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) command to output the [Reader](built-in-roles.md#reader) role in JSON format.

    ```azurepowershell
    Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole.json
    ```

1. V editoru otevřete soubor **ReaderSupportRole.json**.

    Následuje ukázka výstupu JSON. Informace o různých vlastnostech najdete v tématu [Vlastní role](custom-roles.md).

    ```json
    {
      "Name": "Reader",
      "Id": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
      "IsCustom": false,
      "Description": "Lets you view everything, but not make any changes.",
      "Actions": [
        "*/read"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/"
      ]
    }
    ```
    
1. Upravte soubor JSON a do vlastnosti `Actions` přidejte operaci `"Microsoft.Support/*"`. Nezapomeňte vložit čárku za operaci čtení. Tato akce umožní uživateli vytvářet lístky podpory.

1. Get the ID of your subscription using the [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) command.

    ```azurepowershell
    Get-AzSubscription
    ```

1. Do vlastnosti `AssignableScopes` přidejte ID vašeho předplatného v následujícím formátu: `"/subscriptions/00000000-0000-0000-0000-000000000000"`.

    Musíte přidat explicitní ID předplatných, jinak tuto roli nebudete moct importovat do svého předplatného.

1. Odstraňte řádek vlastnosti `Id` a změňte vlastnost `IsCustom` na hodnotu `true`.

1. Změňte vlastnost `Name` na Čtenář lístků podpory a vlastnost `Description` na Zobrazení všeho v předplatném a také otevírání lístků podpory.

    Váš soubor JSON by měl vypadat následovně:

    ```json
    {
      "Name": "Reader Support Tickets",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    
1. To create the new custom role, use the [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) command and specify the JSON role definition file.

    ```azurepowershell
    New-AzRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
        
    Nová vlastní role je teď k dispozici na webu Azure Portal a můžete ji přiřadit uživatelům, skupinám nebo instančním objektům stejně jako předdefinované role.

## <a name="list-custom-roles"></a>Výpis vlastních rolí

- To list all your custom roles, use the [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) command.

    ```azurepowershell
    Get-AzRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
    ```

    ```Output
    Name                   IsCustom
    ----                   --------
    Reader Support Tickets     True
    ```
    
    Vlastní roli můžete zobrazit také na webu Azure Portal.

    ![snímek obrazovky webu Azure Portal s importovanou vlastní rolí](./media/tutorial-custom-role-powershell/custom-role-reader-support-tickets.png)

## <a name="update-a-custom-role"></a>Aktualizace vlastní role

Pokud chcete aktualizovat vlastní roli, můžete aktualizovat soubor JSON nebo použít objekt `PSRoleDefinition`.

1. To update the JSON file, use the [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) command to output the custom role in JSON format.

    ```azurepowershell
    Get-AzRoleDefinition -Name "Reader Support Tickets" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole2.json
    ```

1. Otevřete soubor v editoru.

1. Do vlastnosti `Actions` přidejte operaci `"Microsoft.Resources/deployments/*"` umožňující vytváření a správu nasazení skupiny prostředků.

    Váš aktualizovaný soubor JSON by měl vypadat následovně:

    ```json
    {
      "Name": "Reader Support Tickets",
      "Id": "22222222-2222-2222-2222-222222222222",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*",
        "Microsoft.Resources/deployments/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
        
1. To update the custom role, use the [Set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) command and specify the updated JSON file.

    ```azurepowershell
    Set-AzRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole2.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```

1. To use the `PSRoleDefintion` object to update your custom role, first use the [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) command to get the role.

    ```azurepowershell
    $role = Get-AzRoleDefinition "Reader Support Tickets"
    ```
    
1. Zavoláním metody `Add` přidejte operaci pro čtení nastavení diagnostiky.

    ```azurepowershell
    $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*/read")
    ```

1. Use the [Set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) to update the role.

    ```azurepowershell
    Set-AzRoleDefinition -Role $role
    ```
    
    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*,
                       Microsoft.Insights/diagnosticSettings/*/read}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
    
## <a name="delete-a-custom-role"></a>Odstranění vlastní role

1. Use the [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) command to get the ID of the custom role.

    ```azurepowershell
    Get-AzRoleDefinition "Reader Support Tickets"
    ```

1. Use the [Remove-AzRoleDefinition](/powershell/module/az.resources/remove-azroledefinition) command and specify the role ID to delete the custom role.

    ```azurepowershell
    Remove-AzRoleDefinition -Id "22222222-2222-2222-2222-222222222222"
    ```

    ```Output
    Confirm
    Are you sure you want to remove role definition with id '22222222-2222-2222-2222-222222222222'.
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```

1. Když se zobrazí výzva k potvrzení, zadejte **Y**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytváření vlastních rolí pro prostředky Azure pomocí Azure PowerShellu](custom-roles-powershell.md)
