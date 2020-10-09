---
title: 'Kurz: Vytvoření vlastní role Azure pomocí Azure PowerShell – Azure RBAC'
description: Seznámení s vytvářením vlastní role Azure pomocí Azure PowerShell a řízení přístupu na základě role Azure (Azure RBAC) v tomto kurzu.
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
ms.openlocfilehash: 8aedc59fc34278bf84983d78bf0e9a31fe38ee93
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "82735585"
---
# <a name="tutorial-create-an-azure-custom-role-using-azure-powershell"></a>Kurz: Vytvoření vlastní role Azure pomocí Azure PowerShell

Pokud [předdefinované role Azure](built-in-roles.md) nevyhovují konkrétním potřebám vaší organizace, můžete vytvořit vlastní role. V tomto kurzu pomocí Azure PowerShellu vytvoříte vlastní roli Čtenář lístků podpory. Vlastní role uživateli umožňuje zobrazit vše v rovině správy předplatného a také otevřít lístky podpory.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření vlastní role
> * Výpis vlastních rolí
> * Aktualizace vlastní role
> * Odstranění vlastní role

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- Oprávnění k vytváření vlastních rolí, například [Vlastník](built-in-roles.md#owner) nebo [Správce přístupu uživatelů](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) nebo [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="sign-in-to-azure-powershell"></a>Přihlášení k Azure PowerShellu

Přihlaste se k [Azure PowerShell](/powershell/azure/authenticate-azureps).

## <a name="create-a-custom-role"></a>Vytvoření vlastní role

Nejjednodušší způsob, jak vytvořit vlastní roli, je začít s předdefinovanou rolí, upravit ji a pak vytvořit novou roli.

1. V PowerShellu použijte k získání seznamu operací pro poskytovatele prostředků Microsoft. support příkaz [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) . Je užitečné znát operace, které máte k dispozici k vytváření oprávnění. Seznam všech operací najdete také v tématu [Operace poskytovatele prostředků Azure Resource Manageru](resource-provider-operations.md#microsoftsupport).

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

1. Použijte příkaz [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) pro výstup role [Čtenář](built-in-roles.md#reader) ve formátu JSON.

    ```azurepowershell
    Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole.json
    ```

1. V editoru otevřete soubor **ReaderSupportRole.json**.

    Následuje ukázka výstupu JSON. Informace o různých vlastnostech najdete v tématu [vlastní role Azure](custom-roles.md).

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

1. Pomocí příkazu [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) Získejte ID vašeho předplatného.

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
    
1. Chcete-li vytvořit novou vlastní roli, použijte příkaz [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) a zadejte soubor definice role JSON.

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

- Pokud chcete zobrazit seznam všech vlastních rolí, použijte příkaz [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) .

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

1. Pokud chcete soubor JSON aktualizovat, použijte k výstupu vlastní role ve formátu JSON příkaz [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) .

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
        
1. K aktualizaci vlastní role použijte příkaz [set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) a zadejte aktualizovaný soubor JSON.

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

1. Chcete-li použít `PSRoleDefintion` objekt k aktualizaci vlastní role, nejprve použijte příkaz [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) , abyste získali roli.

    ```azurepowershell
    $role = Get-AzRoleDefinition "Reader Support Tickets"
    ```
    
1. Zavoláním metody `Add` přidejte operaci pro čtení nastavení diagnostiky.

    ```azurepowershell
    $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*/read")
    ```

1. K aktualizaci role použijte [příkaz set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) .

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

1. K získání ID vlastní role použijte příkaz [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) .

    ```azurepowershell
    Get-AzRoleDefinition "Reader Support Tickets"
    ```

1. K odstranění vlastní role použijte příkaz [Remove-AzRoleDefinition](/powershell/module/az.resources/remove-azroledefinition) a zadejte ID role.

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
> [Vytvoření nebo aktualizace vlastních rolí Azure pomocí Azure PowerShell](custom-roles-powershell.md)
