---
title: Kurz `:` použití spravované identity pro přístup k Azure Resource Manager-Windows-Azure AD
description: Tento kurz vás provede procesem použití spravované identity přiřazené uživatelem na virtuálním počítači s Windows pro přístup k Azure Resource Manageru.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43f926f400bf8ddd522bbb5f62509519fa09903d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101096593"
---
# <a name="tutorial-use-a-user-assigned-managed-identity-on-a-windows-vm-to-access-azure-resource-manager"></a>Kurz: použití spravované identity přiřazené uživatelem na virtuálním počítači s Windows pro přístup k Azure Resource Manager

Tento kurz vysvětluje, jak vytvořit identitu přiřazenou uživatelem, přiřadit ji k virtuálnímu počítači s Windows a pak ji použít pro přístup k rozhraní API Azure Resource Manageru. Identity spravovaných služeb se spravují automaticky v Azure. Umožňují ověřování ve službách, které podporují ověřování Azure AD, bez nutnosti vložení přihlašovacích údajů do kódu. 

Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření spravované identity přiřazené uživatelem
> * Přiřazení identity přiřazené uživatelem k virtuálnímu počítači s Windows
> * Udělení přístupu identitě přiřazené uživatelem ke skupině prostředků v Azure Resource Manageru 
> * Získání přístupového tokenu pomocí identity přiřazené uživatelem a jeho použití k volání Azure Resource Manageru 
> * Čtení vlastností skupiny prostředků

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

- [Přihlášení k Azure Portal](https://portal.azure.com)

- [Vytvoření virtuálního počítače s Windows](../../virtual-machines/windows/quick-create-portal.md)

- K provedení kroků v tomto kurzu potřebných k vytvoření prostředku a správě rolí potřebuje váš účet oprávnění vlastníka v odpovídajícím oboru (vaše předplatné nebo skupina prostředků). Pokud potřebujete pomoc s přiřazením role, přečtěte si téma [přiřazení rolí Azure ke správě přístupu k prostředkům předplatného Azure](../../role-based-access-control/role-assignments-portal.md).

- Chcete-li spustit ukázkové skripty, máte dvě možnosti:
    - Použijte [Azure Cloud Shell](../../cloud-shell/overview.md), který můžete otevřít pomocí tlačítka **vyzkoušet** v pravém horním rohu bloků kódu.
    - Spouštějte skripty místně pomocí Azure PowerShell, jak je popsáno v následující části.

### <a name="configure-azure-powershell-locally"></a>Konfigurace Azure PowerShell místně

Pokud chcete použít Azure PowerShell lokálně pro tento článek (místo použití Cloud Shell), proveďte následující kroky:

1. Pokud jste to ještě neudělali, nainstalujte [nejnovější verzi Azure PowerShell](/powershell/azure/install-az-ps) .

1. Přihlaste se k Azure:

    ```azurepowershell
    Connect-AzAccount
    ```

1. Nainstalujte [nejnovější verzi modulu PowerShellGet](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).

    ```azurepowershell
    Install-Module -Name PowerShellGet -AllowPrerelease
    ```

    `Exit`Po spuštění tohoto příkazu pro další krok možná budete muset odhlásit aktuální relaci PowerShellu.

1. Nainstalujte předběžnou verzi `Az.ManagedServiceIdentity` modulu, abyste mohli provádět operace spravované identity přiřazené uživatelem v tomto článku:

    ```azurepowershell
    Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease
    ```

## <a name="enable"></a>Povolit

V případě scénáře, který je založen na identitě přiřazené uživatelem, je třeba provést následující kroky:

- Vytvoření identity
- Přiřaďte nově vytvořenou identitu.

### <a name="create-identity"></a>Vytvořit identitu

V této části se dozvíte, jak vytvořit uživatelem přiřazenou identitu. Identita přiřazená uživatelem se vytváří jako samostatný prostředek Azure. Pomocí [New-AzUserAssignedIdentity](/powershell/module/az.managedserviceidentity/get-azuserassignedidentity)vytvoří Azure v TENANTOVI Azure AD identitu, kterou je možné přiřadit k jedné nebo více instancím služby Azure.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1
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

### <a name="assign-identity"></a>Přiřadit identitu

V této části se dozvíte, jak přiřadit uživatelem přiřazenou identitu k virtuálnímu počítači s Windows. Klienti můžou identitu přiřazenou uživatelem používat pro několik prostředků Azure. Pomocí následujících příkazů přiřaďte identitu přiřazenou uživatelem k jednomu virtuálnímu počítači. Jako hodnotu parametru `-IdentityID` použijte vlastnost `Id` vrácenou v předchozím kroku.

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVM -ResourceGroupName TestRG -VM $vm -IdentityType "UserAssigned" -IdentityID "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
```

## <a name="grant-access"></a>Udělení přístupu 

V této části se dozvíte, jak udělit uživatelem přiřazenou identitu přístupu ke skupině prostředků v Azure Resource Manager. Spravované identity pro prostředky Azure poskytují identity, které můžete v kódu použít k odesílání požadavků na přístupové tokeny pro ověření v rozhraních API prostředků, která podporují ověřování Azure AD. V tomto kurzu budete v kódu přistupovat k rozhraní API Azure Resource Manageru. 

Než bude mít kód přístup k rozhraní API, je potřeba udělit identitě přístup k prostředku v Azure Resource Manageru. V tomto případě ke skupině prostředků, která obsahuje virtuální počítač. Aktualizujte hodnotu `<SUBSCRIPTION ID>` odpovídajícím způsobem pro vaše prostředí.

```azurepowershell-interactive
$spID = (Get-AzUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1).principalid
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/"
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

## <a name="access-data"></a>Přístup k datům

### <a name="get-an-access-token"></a>Získání přístupového tokenu 

Ve zbývající části kurzu použijete k práci dříve vytvořený virtuální počítač.

1. Přihlaste se k Azure Portal v [https://portal.azure.com](https://portal.azure.com)

2. Na portálu přejděte na **Virtuální počítače**, přejděte ke svému virtuálnímu počítači s Windows a v části **Přehled** klikněte na **Připojit**.

3. Zadejte **Uživatelské jméno** a **Heslo**, které jste použili při vytváření virtuálního počítače s Windows.

4. Teď, když jste vytvořili **připojení ke vzdálené ploše** s virtuálním počítačem, otevřete **PowerShell** ve vzdálené relaci.

5. Pomocí příkazu `Invoke-WebRequest` v PowerShellu požádejte místní spravované identity o koncový bod prostředků Azure k získání přístupového tokenu pro Azure Resource Manager.  `client_id`Hodnota je hodnota vrácená při vytvoření spravované identity přiřazené uživatelem.

    ```azurepowershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=af825a31-b0e0-471f-baea-96de555632f9&resource=https://management.azure.com/' -Method GET -Headers @{Metadata="true"}
    $content = $response.Content | ConvertFrom-Json
    $ArmToken = $content.access_token
    ```

### <a name="read-properties"></a>Číst vlastnosti

Použijte přístupový token načtený v předchozím kroku k přístupu k Azure Resource Manageru a čtení vlastností skupiny prostředků, ke které jste identitě spravované uživatelem udělili přístup. Místo `<SUBSCRIPTION ID>` použijte ID předplatného pro vaše prostředí.

```azurepowershell
(Invoke-WebRequest -Uri https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/myResourceGroupVM?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{Authorization ="Bearer $ArmToken"}).content
```
Odpověď bude obsahovat informace o konkrétní skupině prostředků podobně jako v následujícím příkladu:

```json
{"id":"/subscriptions/<SUBSCRIPTIONID>/resourceGroups/myResourceGroupVM","name":"myResourceGroupVM","location":"eastus","properties":{"provisioningState":"Succeeded"}}
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak vytvořit uživatelem přiřazenou identitu a připojit ji k virtuálnímu počítači Azure pro přístup k rozhraní Azure Resource Manager API.  Další informace o Azure Resource Manageru:

> [!div class="nextstepaction"]
>[Azure Resource Manager](../../azure-resource-manager/management/overview.md)
