---
title: Jak vytvořit, vypsat a odstranit uživatelsky přiřazené spravovanou identitu pomocí Azure Powershellu
description: Podrobné pokyny o tom, jak vytvořit, vypsat a odstranit uživatelsky přiřazené spravovaných identit pomocí Azure Powershellu.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: priyamo
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0d1fee9526c308f025abaf1ded1f02ee0617b2f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56202570"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Vytvoření seznamu nebo odstranit uživatelem přidělenou spravovanou identitu pomocí Azure Powershellu

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Spravované identity pro prostředky Azure poskytuje služby Azure s využitím spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření služby, které podporují ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak vytvářet, vypsat a odstranit uživatelsky přiřazené spravovanou identitu pomocí Azure Powershellu.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s spravovaných identit pro prostředky Azure, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi systém přiřadil a uživatelsky přiřazené identity spravované](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Nainstalujte [nejnovější verzi Azure Powershellu](/powershell/azure/install-az-ps) Pokud jste tak již neučinili.
- Pokud používáte PowerShell místně, je potřeba provést také následující: 
    - Spuštěním příkazu `Connect-AzAccount` vytvořte připojení k Azure.
    - Nainstalujte [nejnovější verzi modulu PowerShellGet](/powershell/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
    - Spuštěním rutiny `Install-Module -Name PowerShellGet -AllowPrerelease` získejte předběžnou verzi modulu `PowerShellGet` (po spuštění tohoto příkazu možná budete muset pomocí příkazu `Exit` ukončit aktuální relaci PowerShellu, aby se modul `Az.ManagedServiceIdentity` nainstaloval).
    - Spustit `Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease` nainstalovat zkušební verzi `Az.ManagedServiceIdentity` modulu provádět uživatel přiřazenou spravované identity operace v tomto článku.

## <a name="create-a-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem

Pro vytvoření uživatelsky přiřazené identity spravované, musí váš účet [Přispěvatel spravovaných identit](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) přiřazení role.

Pro vytvoření uživatelsky přiřazené identity spravované, použijte `New-AzUserAssignedIdentity` příkazu. `ResourceGroupName` Parametr určuje skupinu prostředků, kde k vytvoření uživatelsky přiřazené spravovanou identitu a `-Name` parametr určuje její název. Nahradit `<RESOURCE GROUP>` a `<USER ASSIGNED IDENTITY NAME>` parametr hodnoty vlastními hodnotami:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Seznam uživatelsky přiřazené spravované identity

Do seznamu/čtení uživatelsky přiřazené spravovanou identitu, musí váš účet [operátor spravovaných identit](/azure/role-based-access-control/built-in-roles#managed-identity-operator) nebo [Přispěvatel spravovaných identit](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) přiřazení role.

Seznam uživatelsky přiřazené spravovaných identit, použijte [Get-AzUserAssigned] příkaz.  `-ResourceGroupName` Parametr určuje skupinu prostředků, kde byl vytvořen uživatelem přidělenou spravovaná identita. Nahraďte `<RESOURCE GROUP>` s vlastní hodnotou:

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
V odpovědi, mají uživatelsky přiřazené identity spravované `"Microsoft.ManagedIdentity/userAssignedIdentities"` hodnotu pro klíč, `Type`.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Odstranění spravované identity přiřazené uživateli

Pokud chcete odstranit spravovanou identitu uživatele přiřazeny, musí váš účet [Přispěvatel spravovaných identit](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) přiřazení role.

Chcete-li odstranit spravovanou identitu uživatele přiřazeny, použijte `Remove-AzUserAssignedIdentity` příkazu.  `-ResourceGroupName` Parametr určuje skupinu prostředků, ve kterém byla vytvořena uživatelsky přiřazené identity a `-Name` parametr určuje její název. Nahradit `<RESOURCE GROUP>` a `<USER ASSIGNED IDENTITY NAME>` parametry hodnoty vlastními hodnotami:

 ```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> Odstraňuje se uživatel přiřazenou spravovanou identitu nedojde k odebrání odkazu, ze všech prostředků, který byl přiřazen. Přiřazení identity třeba je odebrat samostatně.

## <a name="next-steps"></a>Další postup

Úplný seznam a podrobnosti o prostředí Azure PowerShell spravovaných identit pro příkazy Azure prostředky, najdete v části [Az.ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity).
