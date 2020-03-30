---
title: Vytvoření, seznam & odstranění spravované identity přiřazené uživatelem pomocí Azure PowerShellu – Azure AD
description: Krok za krokem pokyny, jak vytvořit, seznam a odstranit uživatelem přiřazenou spravovanou identitu pomocí Azure PowerShellu.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: c512a867685b4480c7b31ac582e2cee069ee2447
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74547405"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Vytvoření, vypsat nebo odstranit spravovanou identitu přiřazenou uživatelem pomocí Azure PowerShellu

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Spravované identity pro prostředky Azure poskytují služby Azure se spravovanou identitou ve službě Azure Active Directory. Tuto identitu můžete použít k ověření služeb, které podporují ověřování Azure AD, bez nutnosti pověření ve vašem kódu. 

V tomto článku se dozvíte, jak vytvořit, vypsat a odstranit uživatelem přiřazenou spravovanou identitu pomocí Azure PowerShellu.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni se spravovanými identitami pro prostředky Azure, podívejte se na [část s přehledem](overview.md). **Zkontrolujte rozdíl [mezi systémem přiřazenou a uživatelem přiřazenou spravovanou identitou](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Pokud jste to ještě neudělali, nainstalujte [nejnovější verzi Azure PowerShellu.](/powershell/azure/install-az-ps)
- Pokud používáte PowerShell místně, je potřeba provést také následující: 
    - Spuštěním příkazu `Connect-AzAccount` vytvořte připojení k Azure.
    - Nainstalujte [nejnovější verzi modulu PowerShellGet](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
    - Spuštěním rutiny `Install-Module -Name PowerShellGet -AllowPrerelease` získejte předběžnou verzi modulu `PowerShellGet` (po spuštění tohoto příkazu možná budete muset pomocí příkazu `Exit` ukončit aktuální relaci PowerShellu, aby se modul `Az.ManagedServiceIdentity` nainstaloval).
    - Spuštěním `Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease` nainstalujete předběžnou `Az.ManagedServiceIdentity` verzi modulu a proveďte uživatelem přiřazené operace spravované identity v tomto článku.

## <a name="create-a-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem

Chcete-li vytvořit spravovanou identitu přiřazenou uživateli, potřebuje váš účet přiřazení role [Přispěvatel spravované identity.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

Chcete-li vytvořit spravovanou identitu `New-AzUserAssignedIdentity` přiřazenou uživateli, použijte příkaz. Parametr `ResourceGroupName` určuje skupinu prostředků, kde má být vytvořit spravovanou identitu `-Name` přiřazenou uživateli, a parametr určuje její název. Nahraďte hodnoty parametrů `<RESOURCE GROUP>` a `<USER ASSIGNED IDENTITY NAME>` vlastními hodnotami:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Seznam spravovaných identit přiřazených uživatelem

Chcete-li vypsat/přečíst spravovanou identitu přiřazenou uživatelem, potřebuje váš účet přiřazení role [Operátor spravované identity](/azure/role-based-access-control/built-in-roles#managed-identity-operator) nebo [Přiřazení přispěvatele spravované identity.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

Chcete-li vypsat spravované identity přiřazené uživatelem, použijte příkaz [Get-AzUserAssigned].  Parametr `-ResourceGroupName` určuje skupinu prostředků, ve které byla vytvořena spravovaná identita přiřazená uživatelem. `<RESOURCE GROUP>` Nahraďte jej vlastní hodnotou:

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
V odpovědi mají uživatelem přiřazené `"Microsoft.ManagedIdentity/userAssignedIdentities"` spravované identity `Type`vrácenou hodnotu pro klíč .

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Odstranění spravované identity přiřazené uživateli

Chcete-li odstranit spravovanou identitu přiřazenou uživatelem, potřebuje váš účet přiřazení role [Přispěvatel spravované identity.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

Chcete-li odstranit spravovanou identitu `Remove-AzUserAssignedIdentity` přiřazenou uživatelem, použijte příkaz.  Parametr `-ResourceGroupName` určuje skupinu prostředků, kde byla vytvořena identita `-Name` přiřazená uživateli, a parametr určuje její název. Nahraďte `<USER ASSIGNED IDENTITY NAME>` hodnoty `<RESOURCE GROUP>` parametrů a vlastními hodnotami:

 ```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> Odstraněním spravované identity přiřazené uživateli neodeberete odkaz z prostředků, ke kterým byl přiřazen. Přiřazení identity je třeba odebrat samostatně.

## <a name="next-steps"></a>Další kroky

Úplný seznam a další podrobnosti o spravovaných identitách Azure PowerShellu pro prostředky Azure najdete v tématu [Az.ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity).
