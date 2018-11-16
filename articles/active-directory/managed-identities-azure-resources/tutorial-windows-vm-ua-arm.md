---
title: Použití spravované identity přiřazené uživatelem virtuálnímu počítači s Windows pro přístup k Azure Resource Manageru
description: Tento kurz vás provede procesem použití spravované identity přiřazené uživatelem na virtuálním počítači s Windows pro přístup k Azure Resource Manageru.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2018
ms.author: daveba
ms.openlocfilehash: 4b84876db86f5930acd95a2e1a45055282fa37fa
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51622104"
---
# <a name="tutorial-use-a-user-assigned-managed-identity-on-a-windows-vm-to-access-azure-resource-manager"></a>Kurz: Použití spravované identity přiřazené uživatelem na virtuálním počítači s Windows pro přístup k Azure Resource Manageru

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Tento kurz vysvětluje, jak vytvořit identitu přiřazenou uživatelem, přiřadit ji k virtuálnímu počítači s Windows a pak ji použít pro přístup k rozhraní API Azure Resource Manageru. Identity spravovaných služeb se spravují automaticky v Azure. Umožňují ověřování ve službách, které podporují ověřování Azure AD, bez nutnosti vložení přihlašovacích údajů do kódu. 

Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření spravované identity přiřazené uživatelem
> * Přiřazení identity přiřazené uživatelem k virtuálnímu počítači s Windows
> * Udělení přístupu identitě přiřazené uživatelem ke skupině prostředků v Azure Resource Manageru 
> * Získání přístupového tokenu pomocí identity přiřazené uživatelem a jeho použití k volání Azure Resource Manageru 
> * Čtení vlastností skupiny prostředků

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

- [Přihlášení k webu Azure Portal](https://portal.azure.com)

- [Vytvoření virtuálního počítače s Windows](/azure/virtual-machines/windows/quick-create-portal)

- K provedení kroků v tomto kurzu potřebných k vytvoření prostředku a správě rolí potřebuje váš účet oprávnění vlastníka v odpovídajícím oboru (vaše předplatné nebo skupina prostředků). Pokud potřebujete pomoc s přiřazením role, přečtěte si téma [Použití řízení přístupu na základě role ke správě přístupu k prostředkům předplatného Azure](/azure/role-based-access-control/role-assignments-portal).
- Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul Azure PowerShell verze 5.7.0 nebo novější. Verzi zjistíte spuštěním příkazu ` Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). 
- Pokud používáte PowerShell místně, je potřeba provést také následující: 
    - Spuštěním příkazu `Login-AzureRmAccount` vytvořte připojení k Azure.
    - Nainstalujte [nejnovější verzi modulu PowerShellGet](/powershell/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
    - Spuštěním rutiny `Install-Module -Name PowerShellGet -AllowPrerelease` získejte předběžnou verzi modulu `PowerShellGet` (po spuštění tohoto příkazu možná budete muset pomocí příkazu `Exit` ukončit aktuální relaci PowerShellu, aby se modul `AzureRM.ManagedServiceIdentity` nainstaloval).
    - Spuštěním rutiny `Install-Module -Name AzureRM.ManagedServiceIdentity -AllowPrerelease` nainstalujte předběžnou verzi modulu `AzureRM.ManagedServiceIdentity`, který umožňuje provádět operace s identitou přiřazenou uživatelem v tomto článku.

## <a name="create-a-user-assigned-identity"></a>Vytvoření identity přiřazené uživatelem

Identita přiřazená uživatelem se vytváří jako samostatný prostředek Azure. Když použijete příkaz [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity), Azure ve vašem tenantovi Azure AD vytvoří identitu, kterou je možné přiřadit k jedné nebo několika instancím služeb Azure.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurepowershell-interactive
New-AzureRmUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1
```

Odpověď bude obsahovat podrobnosti o vytvořené identitě přiřazené uživatelem podobně jako v následujícím příkladu. Poznamenejte si hodnoty `Id` a `ClientId` pro vaši identitu přiřazenou uživatelem, protože je budete potřebovat v dalších krocích:

```azurepowershell
{
Id: /subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1
ResourceGroupName : myResourceGroupVM
Name: ID1
Location: westus
TenantId: 733a8f0e-ec41-4e69-8ad8-971fc4b533f8
PrincipalId: e591178e-b785-43c8-95d2-1397559b2fb9
ClientId: af825a31-b0e0-471f-baea-96de555632f9
ClientSecretUrl: https://control-westus.identity.azure.net/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1/credentials?tid=733a8f0e-ec41-4e69-8ad8-971fc4b533f8&oid=e591178e-b785-43c8-95d2-1397559b2fb9&aid=af825a31-b0e0-471f-baea-96de555632f9
Type: Microsoft.ManagedIdentity/userAssignedIdentities
}
```

## <a name="assign-the-user-assigned-identity-to-a-windows-vm"></a>Přiřazení identity přiřazené uživatelem k virtuálnímu počítači s Windows

Klienti můžou identitu přiřazenou uživatelem používat pro několik prostředků Azure. Pomocí následujících příkazů přiřaďte identitu přiřazenou uživatelem k jednomu virtuálnímu počítači. Jako hodnotu parametru `-IdentityID` použijte vlastnost `Id` vrácenou v předchozím kroku.

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzureRmVM -ResourceGroupName TestRG -VM $vm -IdentityType "UserAssigned" -IdentityID "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
```

## <a name="grant-your-user-assigned-identity-access-to-a-resource-group-in-azure-resource-manager"></a>Udělení přístupu identitě přiřazené uživatelem ke skupině prostředků v Azure Resource Manageru 

Spravované identity pro prostředky Azure poskytují identity, které můžete v kódu použít k odesílání požadavků na přístupové tokeny pro ověření v rozhraních API prostředků, která podporují ověřování Azure AD. V tomto kurzu budete v kódu přistupovat k rozhraní API Azure Resource Manageru. 

Než bude mít kód přístup k rozhraní API, je potřeba udělit identitě přístup k prostředku v Azure Resource Manageru. V tomto případě ke skupině prostředků, která obsahuje virtuální počítač. Aktualizujte hodnotu `<SUBSCRIPTION ID>` odpovídajícím způsobem pro vaše prostředí.

```azurepowershell-interactive
$spID = (Get-AzureRmUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1).principalid
New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/"
```

Odpověď bude obsahovat podrobnosti o vytvořeném přiřazení role podobně jako v následujícím příkladu:

```azurepowershell
RoleAssignmentId: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM/providers/Microsoft.Authorization/roleAssignments/f9cc753d-265e-4434-ae19-0c3e2ead62ac
Scope: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM
DisplayName: ID1
SignInName:
RoleDefinitionName: Reader
RoleDefinitionId: acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId: e591178e-b785-43c8-95d2-1397559b2fb9
ObjectType: ServicePrincipal
CanDelegate: False
```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Získání přístupového tokenu pomocí identity virtuálního počítače a jeho použití k volání Resource Manageru 

Ve zbývající části kurzu použijete k práci dříve vytvořený virtuální počítač.

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

2. Na portálu přejděte na **Virtuální počítače**, přejděte ke svému virtuálnímu počítači s Windows a v části **Přehled** klikněte na **Připojit**.

3. Zadejte **Uživatelské jméno** a **Heslo**, které jste použili při vytváření virtuálního počítače s Windows.

4. Teď, když jste vytvořili **připojení ke vzdálené ploše** s virtuálním počítačem, otevřete ve vzdálené relaci **PowerShell**.

5. Pomocí příkazu `Invoke-WebRequest` v PowerShellu požádejte místní spravované identity o koncový bod prostředků Azure k získání přístupového tokenu pro Azure Resource Manager.  Hodnota `client_id` je hodnota, která se vrátila při [vytvoření spravované identity přiřazené uživatelem](#create-a-user-assigned-identity).

    ```azurepowershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=af825a31-b0e0-471f-baea-96de555632f9&resource=https://management.azure.com/' -Method GET -Headers @{Metadata="true"}
    $content = $response.Content | ConvertFrom-Json
    $ArmToken = $content.access_token
    ```

## <a name="read-the-properties-of-a-resource-group"></a>Čtení vlastností skupiny prostředků

Použijte přístupový token načtený v předchozím kroku k přístupu k Azure Resource Manageru a čtení vlastností skupiny prostředků, ke které jste identitě spravované uživatelem udělili přístup. Místo <SUBSCRIPTION ID> použijte ID předplatného pro vaše prostředí.

```azurepowershell
(Invoke-WebRequest -Uri https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/myResourceGroupVM?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{Authorization ="Bearer $ArmToken"}).content
```
Odpověď bude obsahovat informace o konkrétní skupině prostředků podobně jako v následujícím příkladu:

```json
{"id":"/subscriptions/<SUBSCRIPTIONID>/resourceGroups/myResourceGroupVM","name":"myResourceGroupVM","location":"eastus","properties":{"provisioningState":"Succeeded"}}
```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli, jak vytvořit identitu přiřazenou uživateli a připojit ji k virtuálnímu počítači Azure kvůli přístupu k rozhraní API Azure Resource Manageru.  Další informace o Azure Resource Manageru:

> [!div class="nextstepaction"]
>[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)