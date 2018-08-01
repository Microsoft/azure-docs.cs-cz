---
title: Jak vytvořit, vypsat a odstranit uživatele přiřazeny (MSI) pomocí Azure Powershellu
description: Podrobné pokyny o tom, jak vytvořit, vypsat a odstranit uživatele přiřadit identita spravované služby pomocí Azure Powershellu.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: a9f684eccefab3e43d9b2b7a364b245a53519f76
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2018
ms.locfileid: "39389675"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-azure-powershell"></a>Vytvoření seznamu nebo odstranění identity přiřazené uživateli, pomocí Azure Powershellu

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Identita spravované služby poskytuje služby Azure s využitím spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření služby, které podporují ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak vytvářet, seznamu a odstraňovat identity přiřazené uživateli, pomocí Azure Powershellu.

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s identita spravované služby, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi přiřazenou systémem a identity přiřazené uživateli](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Nainstalujte [nejnovější verzi Azure Powershellu](https://www.powershellgallery.com/packages/AzureRM) Pokud jste tak již neučinili.
- Pokud se rozhodnete nainstalovat a používat PowerShell místně, tento kurz vyžaduje Azure PowerShell verze modulu 5.7.0 nebo novější. Verzi zjistíte spuštěním příkazu ` Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzureRmAccount` pro vytvoření připojení k Azure.
- Váš účet k provádění operací správy v tomto článku, potřebuje následující přiřazení rolí:
    - [Spravovaná identita Přispěvatel](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) role k vytvoření, čtení (list), aktualizace a odstranění identity přiřazené uživateli.
    - [Operátor Identity spravované](/azure/role-based-access-control/built-in-roles#managed-identity-operator) role ke čtení vlastnosti identity přiřazené uživateli (seznam).

> [!NOTE]
> Když uživatel přiřazenou identity jsou stále ve verzi preview, musíte nejprve ručně nainstalovat AzureRM.ManagedServiceIdentity modul pomocí následujícího příkazu. 
```azurepowershell-interactive
Install-Module -Name AzureRM.ManagedServiceIdentity -AllowPrerelease
```

## <a name="create-a-user-assigned-identity"></a>Vytvoření identity přiřazené uživatelem

K vytvoření identity přiřazené uživateli, použijte [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity) příkazu. `ResourceGroupName` Parametr určuje skupinu prostředků, kde k vytvoření identity přiřazené uživateli a `-Name` parametr určuje její název. Nahradit `<RESOURCE GROUP>` a `<USER ASSIGNED IDENTITY NAME>` parametr hodnoty vlastními hodnotami:

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-identities"></a>Uživatel seznamu identit přiřazených

Seznam uživatelsky přiřazených identit, použijte [Get-AzureRmUserAssigned](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity) příkazu.  `-ResourceGroupName` Parametr určuje skupinu prostředků, ve kterém byla vytvořena identity přiřazené uživateli. Nahraďte `<RESOURCE GROUP>` s vlastní hodnotou:

```azurepowershell-interactive
Get-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
V odpovědi, identity uživatelů mají `"Microsoft.ManagedIdentity/userAssignedIdentities"` hodnotu pro klíč, `Type`.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-identity"></a>Odstranit identity přiřazené uživateli

Chcete-li odstranit identitu uživatele, použijte [odebrat AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/remove-azurermuserassignedidentity) příkazu.  `-ResourceGroupName` Parametr určuje skupinu prostředků, ve kterém byla vytvořena identity přiřazené uživateli a `-Name` parametr určuje její název. Nahradit `<RESOURCE GROUP>` a `<USER ASSIGNED IDENTITY NAME>` parametry hodnoty vlastními hodnotami:

 ```azurecli-interactive
Remove-AzurRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> Odstraňování identity přiřazené uživateli nedojde k odebrání odkazu, ze všech prostředků, který byl přiřazen. Přiřazení identity třeba je odebrat samostatně.

## <a name="related-content"></a>Související obsah

Úplný seznam a podrobnosti o příkazy Azure Powershellu MSI najdete v tématu [AzureRM.ManagedServiceIdentity](/powershell/module/azurerm.managedserviceidentity#managed_service_identity).


 
