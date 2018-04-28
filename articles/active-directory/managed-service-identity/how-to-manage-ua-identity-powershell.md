---
title: Postup vytvoření, seznamu a odstranění uživatele přiřazené (MSI) pomocí prostředí Azure PowerShell
description: Podrobné pokyny, jak vytvářet, seznamu a odstraňovat uživatele přiřazené identita spravované služby pomocí Azure PowerShell.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: 0f4041bd34a0b4978d820b64b45afd1f155cd6ab
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-azure-powershell"></a>Vytvoření seznamu nebo odstranění identitu uživatele přiřazené pomocí Azure PowerShell

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Identita spravované služby poskytuje Azure služby spravovanou identitu ve službě Azure Active Directory. Tuto identitu můžete použít k ověření služby, které podporují ověřování Azure AD, bez nutnosti přihlašovací údaje ve vašem kódu. 

V tomto článku zjistěte, jak vytvářet, seznamu a odstraňovat identitu uživatele přiřazené pomocí Azure PowerShell.

## <a name="prerequisites"></a>Požadavky

- Pokud jste obeznámeni s identita spravované služby, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi systémem přiřazený a uživatel s přiřazenou identity](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure [si zaregistrovat bezplatný účet](https://azure.microsoft.com/free/) než budete pokračovat.
- Nainstalujte [nejnovější verzi prostředí Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) Pokud jste tak ještě neučinili.
- Pokud si zvolíte instalaci a použití prostředí PowerShell místně, tento kurz vyžaduje prostředí Azure PowerShell verze modulu 5.7.0 nebo novější. Verzi zjistíte spuštěním příkazu ` Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzureRmAccount` pro vytvoření připojení k Azure.

## <a name="create-a-user-assigned-identity"></a>Vytvoření uživatele přiřazené identity

K vytvoření identity uživatele, které jsou přiřazeny, použijte [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity) příkaz. `ResourceGroupName` Parametr určuje skupinu prostředků, kde k vytvoření identity uživatele přiřazené a `-Name` parametr určuje její název. Nahraďte `<RESOURCE GROUP>` a `<USER ASSIGNED IDENTITY NAME>` hodnoty parametrů s vlastními hodnotami:

> [!IMPORTANT]
> Vytvoření uživatele přiřazené identity s speciální znaky (tj. podtržítko) v názvu není aktuálně podporován. Použijte alfanumerické znaky. Vraťte se zpět pro aktualizace.  Další informace najdete v části [nejčastější dotazy a známé problémy](known-issues.md).

 ```azurepowershell-interactive
New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-identities"></a>Seznam přiřazeného identity uživatele

Chcete-li seznam identit uživatelů, které jsou přiřazeny, použijte [Get-AzureRmUserAssigned](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity) příkaz.  `-ResourceGroupName` Parametr určuje skupinu prostředků, které bylo vytvořeno přiřazeného identity uživatele.  Nahraďte `<RESOURCE GROUP>` vlastní hodnotou:

```azurepowershell-interactive
Get-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
V odpovědi identit uživatelů mají `"Microsoft.ManagedIdentity/userAssignedIdentities"` hodnota vrácená pro klíč `Type`.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-identity"></a>Odstranění uživatele přiřazené identity

Pokud chcete odstranit identitu uživatele, použijte [odebrat AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/remove-azurermuserassignedidentity) příkaz.  `-ResourceGroupName` Parametr určuje skupinu prostředků, které bylo vytvořeno přiřazeného identity uživatele a `-Name` parametr určuje její název.  Nahraďte `<RESOURCE GROUP>` a `<USER ASSIGNED IDENTITY NAME>` hodnot parametrů s vlastními hodnotami:

 ```azurecli-interactive
Remove-AzurRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> Odstranění uživatele přiřazené identity neodebere odkaz, z libovolného zdroje, kterému byla přiřazena. Přiřazení identity třeba je odebrat samostatně.

## <a name="related-content"></a>Související obsah

Úplný seznam a další podrobnosti o příkazy prostředí PowerShell MSI Azure najdete v tématu [AzureRM.ManagedServiceIdentity](/powershell/module/azurerm.managedserviceidentity#managed_service_identity).


 
