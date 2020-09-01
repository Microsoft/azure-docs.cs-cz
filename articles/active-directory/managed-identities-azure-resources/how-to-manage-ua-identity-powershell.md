---
title: Vytvoření, vypsání & odstranění spravované identity přiřazené uživatelem pomocí Azure PowerShell-Azure AD
description: Podrobné pokyny, jak vytvořit, zobrazit seznam a odstranit spravovanou identitu přiřazenou uživatelem pomocí Azure PowerShell.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1211245786bbb734e0338be1b79030f5f9552793
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2020
ms.locfileid: "89266370"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Vytvoření, vypsání nebo odstranění spravované identity přiřazené uživatelem pomocí Azure PowerShell

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Spravované identity pro prostředky Azure poskytují služby Azure se spravovanou identitou v Azure Active Directory. Tuto identitu můžete použít k ověření pro služby, které podporují ověřování Azure AD, a to bez nutnosti přihlašovacích údajů ve vašem kódu. 

V tomto článku se dozvíte, jak pomocí Azure PowerShell vytvořit, vypsat a odstranit spravovanou identitu přiřazenou uživatelem.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

- Pokud neznáte spravované identity prostředků Azure, přečtěte si [část přehled](overview.md). **Nezapomeňte si projít [rozdíl mezi spravovanou identitou přiřazenou systémem a uživatelem](overview.md#managed-identity-types)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Pokud jste to ještě neudělali, nainstalujte [nejnovější verzi Azure PowerShell](/powershell/azure/install-az-ps) .
- Pokud používáte PowerShell místně, je potřeba provést také následující: 
    - Spuštěním příkazu `Connect-AzAccount` vytvořte připojení k Azure.
    - Nainstalujte [nejnovější verzi modulu PowerShellGet](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
    - Spuštěním rutiny `Install-Module -Name PowerShellGet -AllowPrerelease` získejte předběžnou verzi modulu `PowerShellGet` (po spuštění tohoto příkazu možná budete muset pomocí příkazu `Exit` ukončit aktuální relaci PowerShellu, aby se modul `Az.ManagedServiceIdentity` nainstaloval).
    - Spusťte `Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease` instalaci předprodejní verze `Az.ManagedServiceIdentity` modulu, abyste mohli provádět operace spravované identity přiřazené uživatelem v tomto článku.

## <a name="create-a-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem

K vytvoření spravované identity přiřazené uživatelem potřebuje váš účet přiřazení role [Přispěvatel spravované identity](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

K vytvoření spravované identity přiřazené uživatelem použijte `New-AzUserAssignedIdentity` příkaz. `ResourceGroupName`Parametr určuje skupinu prostředků, ve které se má vytvořit spravovaná identita přiřazená uživateli, a `-Name` parametr určuje jeho název. Nahraďte `<RESOURCE GROUP>` `<USER ASSIGNED IDENTITY NAME>` hodnoty parametrů a vlastními hodnotami:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Výpis spravovaných identit přiřazených uživatelem

K vypsání nebo načtení spravované identity přiřazené uživatelem vyžaduje váš účet [spravovaný operátor identity](../../role-based-access-control/built-in-roles.md#managed-identity-operator) nebo přiřazení role [Přispěvatel spravované identity](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

Pokud chcete zobrazit seznam spravovaných identit přiřazených uživateli, použijte příkaz [Get-AzUserAssigned].  `-ResourceGroupName`Parametr určuje skupinu prostředků, ve které se vytvořila spravovaná identita přiřazená uživatelem. Nahraďte `<RESOURCE GROUP>` vlastní hodnotou:

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
V odpovědi měla uživatelem přiřazené spravované identity `"Microsoft.ManagedIdentity/userAssignedIdentities"` hodnotu vrácenou pro klíč, `Type` .

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Odstranění spravované identity přiřazené uživatelem

Pokud chcete odstranit spravovanou identitu přiřazenou uživatelem, váš účet potřebuje přiřazení role [Přispěvatel spravované identity](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

Pokud chcete odstranit spravovanou identitu přiřazenou uživatelem, použijte `Remove-AzUserAssignedIdentity` příkaz.  `-ResourceGroupName`Parametr určuje skupinu prostředků, ve které byla uživatelem přiřazená identita vytvořena, a `-Name` parametr určuje její název. Nahraďte `<RESOURCE GROUP>` hodnoty a `<USER ASSIGNED IDENTITY NAME>` parametry vlastními hodnotami:

 ```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> Odstraněním spravované identity přiřazené uživatelem se neodebere odkaz z libovolného prostředku, ke kterému byl přiřazen. Přiřazení identit je nutné odebrat samostatně.

## <a name="next-steps"></a>Další kroky

Úplný seznam a další podrobnosti o Azure PowerShell spravovaných identit pro prostředky Azure najdete v tématu [AZ. ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity).