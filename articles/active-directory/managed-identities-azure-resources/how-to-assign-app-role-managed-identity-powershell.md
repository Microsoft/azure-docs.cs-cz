---
title: Přiřazení spravované identity k aplikační roli pomocí PowerShellu – Azure AD
description: Podrobné pokyny pro přiřazení přístupu spravované identity k roli jiné aplikace pomocí PowerShellu
services: active-directory
documentationcenter: ''
author: johndowns
manager: ''
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2020
ms.author: jodowns
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2d844054e3744aafa01f43ded354a5b5d9f8b4b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98732060"
---
# <a name="assign-a-managed-identity-access-to-an-application-role-using-powershell"></a>Přiřazení spravované identity přístup k roli aplikace pomocí PowerShellu

Spravované identity pro prostředky Azure poskytují služby Azure s identitou v Azure Active Directory. Pracují bez nutnosti přihlašovacích údajů ve vašem kódu. Služby Azure používají tuto identitu k ověřování pro služby, které podporují ověřování Azure AD. Aplikační role poskytují formulář řízení přístupu na základě role a umožňují službě implementovat autorizační pravidla.

V tomto článku se dozvíte, jak přiřadit spravovanou identitu k aplikační roli, která je vystavena jinou aplikací pomocí Azure AD PowerShellu.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Předpoklady

- Pokud neznáte spravované identity prostředků Azure, přečtěte si [část přehled](overview.md). **Nezapomeňte si projít [rozdíl mezi spravovanou identitou přiřazenou systémem a uživatelem](overview.md#managed-identity-types)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Chcete-li spustit ukázkové skripty, máte dvě možnosti:
    - Použijte [Azure Cloud Shell](../../cloud-shell/overview.md), který můžete otevřít pomocí tlačítka **vyzkoušet** v pravém horním rohu bloků kódu.
    - Spouštět skripty místně pomocí instalace nejnovější verze [Azure AD PowerShellu](/powershell/azure/active-directory/install-adv2).

## <a name="assign-a-managed-identity-access-to-another-applications-app-role"></a>Přiřaďte přístup spravovaných identit k aplikační roli jiné aplikace.

1. Povolte spravovanou identitu na prostředku Azure, [jako je třeba virtuální počítač Azure](qs-configure-powershell-windows-vm.md).

1. Vyhledejte ID objektu instančního objektu spravované identity.

   **Pro spravovanou identitu přiřazenou systémem** můžete najít ID objektu na Azure Portal na stránce **Identita** prostředku. K vyhledání ID objektu můžete také použít následující skript prostředí PowerShell. Budete potřebovat ID prostředku prostředku, který jste vytvořili v kroku 1, který je k dispozici ve Azure Portal na stránce **vlastností** prostředku.

    ```powershell
    $resourceIdWithManagedIdentity = '/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.Compute/virtualMachines/{my virtual machine name}'
    (Get-AzResource -ResourceId $resourceIdWithManagedIdentity).Identity.PrincipalId
    ```

    V **případě uživatelsky přiřazené spravované identity** najdete ID objektu spravované identity na Azure Portal na stránce **Přehled** prostředku. K vyhledání ID objektu můžete také použít následující skript prostředí PowerShell. Budete potřebovat ID prostředku spravované identity přiřazené uživatelem.

    ```powershell
    $userManagedIdentityResourceId = '/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{my managed identity name}'
    (Get-AzResource -ResourceId $userManagedIdentityResourceId).Properties.PrincipalId
    ```

1. Vytvořte novou registraci aplikace, která bude představovat službu, do které vaše spravovaná identita odešle požadavek. Pokud rozhraní API nebo služba, které zveřejňují roli aplikace, mají ve vašem tenantovi služby Azure AD již instanční objekt, tento krok přeskočte. Pokud například chcete udělit přístup spravovaných identit k rozhraní Microsoft Graph API, můžete tento krok přeskočit.

1. Vyhledejte ID objektu instančního objektu aplikace služby. To můžete najít pomocí Azure Portal. Přejít na Azure Active Directory a otevřít stránku **podnikové aplikace** , najít aplikaci a vyhledat **ID objektu**. ID objektu instančního objektu můžete také najít pomocí jeho zobrazovaného názvu pomocí následujícího skriptu prostředí PowerShell:

    ```powershell
    $serverServicePrincipalObjectId = (Get-AzureADServicePrincipal -Filter "DisplayName eq '$applicationName'").ObjectId
    ```

    > [!NOTE]
    > Zobrazované názvy aplikací nejsou jedinečné, proto byste měli ověřit, že získáte správný instanční objekt aplikace.

1. Přidejte [roli aplikace](../develop/howto-add-app-roles-in-azure-ad-apps.md) do aplikace, kterou jste vytvořili v kroku 3. Roli můžete vytvořit pomocí Azure Portal nebo pomocí Microsoft Graph. Například můžete přidat roli aplikace takto:

    ```json
    {
        "allowedMemberTypes": [
            "Application"
        ],
        "displayName": "Read data from MyApi",
        "id": "0566419e-bb95-4d9d-a4f8-ed9a0f147fa6",
        "isEnabled": true,
        "description": "Allow the application to read data as itself.",
        "value": "MyApi.Read.All"
    }
    ```

1. Přiřaďte roli aplikace spravované identitě. K přiřazení role aplikace budete potřebovat následující informace:
    * `managedIdentityObjectId`: ID objektu instančního objektu spravované identity, který jste našli v kroku 2.
    * `serverServicePrincipalObjectId`: ID objektu instančního objektu serverové aplikace, který jste našli v kroku 4.
    * `appRoleId`: ID aplikační role vystavené serverovou aplikací, kterou jste vytvořili v kroku 5 – v příkladu je ID aplikační role `0566419e-bb95-4d9d-a4f8-ed9a0f147fa6` .
   
   Přidejte přiřazení role spuštěním následujícího skriptu prostředí PowerShell:

    ```powershell
    New-AzureADServiceAppRoleAssignment -ObjectId $managedIdentityObjectId -Id $appRoleId -PrincipalId $managedIdentityObjectId -ResourceId $serverServicePrincipalObjectId
    ```

## <a name="complete-script"></a>Celý skript

Tento ukázkový skript ukazuje, jak přiřadit roli aplikace spravovanou identitu webové aplikace Azure.

```powershell
# Install the module. (You need admin on the machine.)
# Install-Module AzureAD

# Your tenant ID (in the Azure portal, under Azure Active Directory > Overview).
$tenantID = '<tenant-id>'

# The name of your web app, which has a managed identity that should be assigned to the server app's app role.
$webAppName = '<web-app-name>'
$resourceGroupName = '<resource-group-name-containing-web-app>'

# The name of the server app that exposes the app role.
$serverApplicationName = '<server-application-name>' # For example, MyApi

# The name of the app role that the managed identity should be assigned to.
$appRoleName = '<app-role-name>' # For example, MyApi.Read.All

# Look up the web app's managed identity's object ID.
$managedIdentityObjectId = (Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $webAppName).identity.principalid

Connect-AzureAD -TenantId $tenantID

# Look up the details about the server app's service principal and app role.
$serverServicePrincipal = (Get-AzureADServicePrincipal -Filter "DisplayName eq '$serverApplicationName'")
$serverServicePrincipalObjectId = $serverServicePrincipal.ObjectId
$appRoleId = ($serverServicePrincipal.AppRoles | Where-Object {$_.Value -eq $appRoleName }).Id

# Assign the managed identity access to the app role.
New-AzureADServiceAppRoleAssignment `
    -ObjectId $managedIdentityObjectId `
    -Id $appRoleId `
    -PrincipalId $managedIdentityObjectId `
    -ResourceId $serverServicePrincipalObjectId
```

## <a name="next-steps"></a>Další kroky

- [Přehled spravované identity pro prostředky Azure](overview.md)
- Pokud chcete povolit spravovanou identitu na virtuálním počítači Azure, přečtěte si téma [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí PowerShellu](qs-configure-powershell-windows-vm.md).