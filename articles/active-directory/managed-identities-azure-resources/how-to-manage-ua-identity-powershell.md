---
title: Jak vytvořit, vypsat a odstranit uživatelsky přiřazené spravovanou identitu pomocí Azure Powershellu
description: Podrobné pokyny o tom, jak vytvořit, vypsat a odstranit uživatelsky přiřazené spravovaných identit pomocí Azure Powershellu.
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
ms.openlocfilehash: c7191f60b8780e8ccee9b330aa21d8174f0f0148
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47106779"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Vytvoření seznamu nebo odstranit uživatelem přidělenou spravovanou identitu pomocí Azure Powershellu

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Spravované identity pro prostředky Azure poskytuje služby Azure s využitím spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření služby, které podporují ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak vytvářet, vypsat a odstranit uživatelsky přiřazené spravovanou identitu pomocí Azure Powershellu.

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s spravovaných identit pro prostředky Azure, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi systém přiřadil a uživatelsky přiřazené identity spravované](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Nainstalujte [nejnovější verzi Azure Powershellu](https://www.powershellgallery.com/packages/AzureRM) Pokud jste tak již neučinili.
- Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul Azure PowerShell verze 5.7.0 nebo novější. Verzi zjistíte spuštěním příkazu ` Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). 
- Pokud používáte PowerShell místně, je potřeba provést také následující: 
    - Spuštěním příkazu `Login-AzureRmAccount` vytvořte připojení k Azure.
    - Nainstalujte [nejnovější verzi modulu PowerShellGet](/powershell/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
    - Spuštěním rutiny `Install-Module -Name PowerShellGet -AllowPrerelease` získejte předběžnou verzi modulu `PowerShellGet` (po spuštění tohoto příkazu možná budete muset pomocí příkazu `Exit` ukončit aktuální relaci PowerShellu, aby se modul `AzureRM.ManagedServiceIdentity` nainstaloval).
    - Spustit `Install-Module -Name AzureRM.ManagedServiceIdentity -AllowPrerelease` nainstalovat zkušební verzi `AzureRM.ManagedServiceIdentity` modulu provádět uživatel přiřazenou spravované identity operace v tomto článku.
- Váš účet k provádění operací správy v tomto článku, potřebuje následující přiřazení rolí:
    - [Spravovaná identita Přispěvatel](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) role k vytvoření, čtení (list), aktualizovat a odstranit spravované identity přiřazené uživateli.
    - [Operátor Identity spravované](/azure/role-based-access-control/built-in-roles#managed-identity-operator) role ke čtení vlastnosti uživatelsky přiřazené spravovanou identitu (seznam).

## <a name="create-a-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem

Pro vytvoření uživatelsky přiřazené identity spravované, použijte [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity) příkazu. `ResourceGroupName` Parametr určuje skupinu prostředků, kde k vytvoření uživatelsky přiřazené spravovanou identitu a `-Name` parametr určuje její název. Nahradit `<RESOURCE GROUP>` a `<USER ASSIGNED IDENTITY NAME>` parametr hodnoty vlastními hodnotami:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Seznam uživatelsky přiřazené spravované identity

Seznam uživatelsky přiřazené identity spravované, použijte [Get-AzureRmUserAssigned](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity) příkazu.  `-ResourceGroupName` Parametr určuje skupinu prostředků, kde byl vytvořen uživatelem přidělenou spravovaná identita. Nahraďte `<RESOURCE GROUP>` s vlastní hodnotou:

```azurepowershell-interactive
Get-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
V odpovědi, mají uživatelsky přiřazené identity spravované `"Microsoft.ManagedIdentity/userAssignedIdentities"` hodnotu pro klíč, `Type`.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Odstranění spravované identity přiřazené uživateli

Chcete-li odstranit spravovanou identitu uživatele přiřazeny, použijte [odebrat AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/remove-azurermuserassignedidentity) příkazu.  `-ResourceGroupName` Parametr určuje skupinu prostředků, ve kterém byla vytvořena uživatelsky přiřazené identity a `-Name` parametr určuje její název. Nahradit `<RESOURCE GROUP>` a `<USER ASSIGNED IDENTITY NAME>` parametry hodnoty vlastními hodnotami:

 ```azurepowershell-interactive
Remove-AzurRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> Odstraňuje se uživatel přiřazenou spravovanou identitu nedojde k odebrání odkazu, ze všech prostředků, který byl přiřazen. Přiřazení identity třeba je odebrat samostatně.

## <a name="next-steps"></a>Další postup

Úplný seznam a podrobnosti o prostředí Azure PowerShell spravovaných identit pro příkazy Azure prostředky, najdete v části [AzureRM.ManagedServiceIdentity](/powershell/module/azurerm.managedserviceidentity#managed_service_identity).
