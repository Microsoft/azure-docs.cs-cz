---
title: Virtuální počítač s Windows uživatel přiřazený MSI používat pro přístup k Azure Resource Manager
description: Kurz vás provede procesem pomocí uživatele přiřazené spravované služby Identity (MSI) na virtuální počítač s Windows, pro přístup k Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2018
ms.author: arluca
ms.openlocfilehash: bdd7966721b22d75023c593593e69ab651b3aaca
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-windows-vm-to-access-azure-resource-manager"></a>Použít uživatele přiřazené spravované služby Identity (MSI) na virtuální počítač s Windows, pro přístup k Azure Resource Manager

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Tento kurz vysvětluje, jak vytvořit uživatele přiřazené identity, přiřaďte k systému Windows virtuálního počítače (VM) a pak tuto identitu používat pro přístup k rozhraní API služby Azure Resource Manager. Identita spravované služby je automaticky prováděna nástrojem Azure. Umožňují ověřování pro služby, které podporují ověřování Azure AD, aniž by museli přihlašovací údaje pro vložení do vašeho kódu. 

Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření virtuálního počítače s Windows 
> * Vytvoření uživatele přiřazené identity
> * Přiřazení uživatelů přiřazené identity virtuálního počítače Windows
> * Udělení přístupu identity přiřazeného uživatele do skupiny prostředků ve službě Správce prostředků Azure 
> * Získání přístupového tokenu pomocí přiřazeného identity uživatele a použít jej k vyvolání Azure Resource Manager 
> * Číst vlastnosti služby skupiny prostředků

## <a name="prerequisites"></a>Požadavky

- Pokud vaše jsou obeznámeni s identita spravované služby, podívejte se [přehled](overview.md) části. **Nezapomeňte si přečíst [rozdíly mezi systémem a uživatel přiřazené identity](overview.md#how-does-it-work)**.
- Pokud ještě nemáte účet Azure [si zaregistrovat bezplatný účet](https://azure.microsoft.com/free/) než budete pokračovat.
- Při vytváření požadovaný prostředek a kroky správy role v tomto kurzu, musí váš účet oprávnění "Vlastník" v příslušné oboru (skupina vaše předplatné nebo prostředek). Pokud potřebujete pomoc s přiřazení role, přečtěte si [Use Role-Based řízení přístupu ke správě přístupu k prostředkům předplatného Azure](/azure/role-based-access-control/role-assignments-portal).

Pokud si zvolíte instalaci a použití prostředí PowerShell místně, tento kurz vyžaduje prostředí Azure PowerShell verze modulu 5.7 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzureRmAccount` pro vytvoření připojení k Azure.

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

V následujícím příkladu s názvem skupiny prostředků, *myResourceGroupVM* je vytvořen v *EastUS* oblast.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName "myResourceGroupVM" -Location "EastUS"
```

## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

Po vytvoření skupiny prostředků, vytvoření virtuálního počítače s Windows.

Uživatelské jméno a heslo potřebné pro účet správce na virtuálním počítači můžete nastavit pomocí příkazu [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```
Vytvořte virtuální počítač pomocí příkazu [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```

## <a name="create-a-user-assigned-identity"></a>Vytvoření uživatele přiřazené identity

Uživatel přiřazený identity je vytvořen jako samostatný prostředků Azure. Pomocí [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity), Azure vytvoří identity v klientovi služby Azure AD, které lze přiřadit k jedné nebo více instancí služby Azure.

> [!IMPORTANT]
> Vytvoření identity uživatele přiřazené podporuje pouze alfanumerické znaky a spojovníky (0 – 9 nebo a-z nebo A-Z nebo -) znaků. Kromě toho název by měl být omezený na 24 znaků pro přiřazení virtuálního počítače nebo VMSS správně fungovat. Vraťte se zpět pro aktualizace. Další informace najdete v části [nejčastější dotazy a známé problémy](known-issues.md)

```azurepowershell-interactive
Get-AzureRmUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1
```

Odpověď obsahuje podrobnosti o uživatel s přiřazenou identity vytvořený, podobně jako v následujícím příkladu. Poznámka: `Id` hodnoty pro své uživatele přiřazené identity, jak se použije v dalším kroku:

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

## <a name="assign-the-user-assigned-identity-to-a-windows-vm"></a>Přiřaďte uživatele přiřazené identity virtuální počítač s Windows

Uživatel přiřazený identity lze klienty na několik prostředků Azure. Použijte následující příkazy identity uživatele přiřazené přiřadit jeden virtuální počítač. Použití `Id` vrácena vlastnost v předchozím kroku `-IdentityID` parametr.

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzureRmVM -ResourceGroupName TestRG -VM $vm -IdentityType "UserAssigned" -IdentityID "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
```

## <a name="grant-your-user-assigned-msi-access-to-a-resource-group-in-azure-resource-manager"></a>Udělte že uživatele přiřazené MSI přístup do skupiny prostředků ve službě Správce prostředků Azure 

Identita spravované služby (MSI) poskytuje identity, které váš kód můžete vyžádat přístupové tokeny k ověřování na prostředek rozhraní API, ověření podpory Azure AD. V tomto kurzu bude váš kód přístup k rozhraní API služby Azure Resource Manager. 

Váš kód mohli dostat k rozhraní API, musíte přidělit identitě přístup k prostředku ve službě Správce prostředků Azure. V tomto případě skupině prostředků, ve kterém se nachází virtuální počítač. Aktualizujte hodnotu pro `<SUBSCRIPTION ID>` jako vhodné pro vaše prostředí.

```azurepowershell-interactive
$spID = (Get-AzureRmUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1).principalid
New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/"
```

Odpověď obsahuje podrobné informace o přiřazení role vytvořený, podobně jako v následujícím příkladu:

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

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Získání přístupového tokenu pomocí identity Virtuálního počítače a použít jej k vyvolání Resource Manager 

Pro zbývající část tohoto kurzu budete pracovat z virtuálního počítače, které jsme vytvořili předtím.

1. Přihlaste se k portálu Azure v [https://portal.azure.com](https://portal.azure.com)

2. Na portálu, přejděte na **virtuální počítače** a přejděte do virtuálního počítače s Windows a v **přehled**, klikněte na tlačítko **Connect**.

3. Zadejte **uživatelské jméno** a **heslo** jste použili při vytvoření virtuálního počítače Windows.

4. Teď, když jste vytvořili **připojení ke vzdálené ploše** s virtuálním počítačem, otevřete **prostředí PowerShell** ve vzdálené relaci.

5. Pomocí prostředí PowerShell na `Invoke-WebRequest`, vytvořte žádost na místní koncový bod MSI se získat přístupový token pro Azure Resource Manager.

    ```azurepowershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=73444643-8088-4d70-9532-c3a0fdc190fz&resource=https://management.azure.com' -Method GET -Headers @{Metadata="true"}
    $content = $response.Content | ConvertFrom-Json
    $ArmToken = $content.access_token
    ```

## <a name="read-the-properties-of-a-resource-group"></a>Číst vlastnosti služby skupiny prostředků

Použití tokenu přístupu získali v předchozím kroku pro přístup k Azure Resource Manager a číst vlastnosti prostředku skupiny, kterou jste uděleno přístup identity přiřazeného uživatele. Nahraďte <SUBSCRIPTION ID> s id předplatného vašeho prostředí.

```azurepowershell
(Invoke-WebRequest -Uri https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/myResourceGroupVM?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{Authorization ="Bearer $ArmToken"}).content
```
Odpověď obsahuje konkrétní informace skupinu prostředků, podobně jako v následujícím příkladu:

```json
{"id":"/subscriptions/<SUBSCRIPTIONID>/resourceGroups/TestRG","name":"myResourceGroupVM","location":"eastus","properties":{"provisioningState":"Succeeded"}}
```

## <a name="next-steps"></a>Další postup

- Přehled MSI najdete v tématu [identita spravované služby přehled](overview.md).
