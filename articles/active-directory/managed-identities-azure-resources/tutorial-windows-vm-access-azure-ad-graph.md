---
title: Použití spravované identity přiřazené systémem na virtuálním počítači s Windows pro přístup k rozhraní Azure AD Graph API
description: Tento kurz vás provede použitím spravované identity přiřazené systémem na virtuálním počítači s Windows pro přístup k rozhraní Azure AD Graph API.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/20/2018
ms.author: priyamo
ms.openlocfilehash: 7be1d98ef98e6e859f7ca530869cf9a097b3b986
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54885284"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-ad-graph-api"></a>Kurz: Použití spravované identity přiřazené systémem na virtuálním počítači s Windows pro přístup k rozhraní Azure AD Graph API

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice.md)]

V tomto kurzu se dozvíte, jak použít spravovanou identitu přiřazenou systémem na virtuálním počítači s Windows pro přístup k rozhraní Microsoft Graph API za účelem načtení jeho členství ve skupinách. Spravované identity pro prostředky Azure se spravují automaticky v Azure a umožňují vám ověřovat přístup ke službám podporujícím ověřování Azure AD bez nutnosti vložení přihlašovacích údajů do kódu.  V tomto kurzu se budete dotazovat na členství identity virtuálního počítače ve skupinách služby Azure AD. Informace o skupinách se často používají například při rozhodování o autorizaci. Spravovaná identita virtuálního počítače je uvnitř služby Azure AD reprezentovaná **instančním objektem**. Před dotazem na skupiny přidejte do některé skupiny ve službě Azure AD instanční objekt reprezentující identitu virtuálního počítače. Můžete to udělat přes Azure PowerShell, Azure AD PowerShell nebo Azure CLI.

> [!div class="checklist"]
> * Připojení k Azure AD
> * Přidání identity virtuálního počítače do skupiny ve službě Azure AD 
> * Udělení přístupu k Azure AD Graphu identitě virtuálního počítače 
> * Získání přístupového tokenu pomocí identity virtuálního počítače a jeho použití k volání Azure AD Graphu

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- Abyste identitě virtuálního počítače mohli udělit přístup k Azure AD Graphu, musí být ve službě Azure AD váš účet přiřazený k roli **globálního správce**.

## <a name="connect-to-azure-ad"></a>Připojení k Azure AD

Kvůli tomu, abyste virtuální počítač přiřadili do skupiny a udělili mu oprávnění k načtení jeho členství ve skupinách, se musíte připojit ke službě Azure AD.

```powershell
Connect-AzureAD
```

## <a name="add-your-vm-identity-to-a-group-in-azure-ad"></a>Přidání identity virtuálního počítače do skupiny ve službě Azure AD

Když jste u virtuálního počítače s Windows povolili spravovanou identitu přiřazenou systémem, vytvořil se ve službě Azure AD instanční objekt.  Teď potřebujete přidat tento virtuální počítač do nějaké skupiny.  Následující příklad vytvoří novou skupinu ve službě Azure AD a přidá instanční objekt virtuálního počítače do této skupiny:

```powershell
New-AzureADGroup -DisplayName "myGroup" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
$AzureADGroup = Get-AzureADGroup -Filter "displayName eq 'myGroup'"
$ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq 'myVM'"
Add-AzureADGroupMember -ObjectId $AzureADGroup.ObjectID -RefObjectId $ManagedIdentitiesServicePrincipal.ObjectId
```
## <a name="grant-your-vm-access-to-the-azure-ad-graph-api"></a>Udělení přístupu k rozhraní Azure AD Graph API virtuálnímu počítači

Když použijete spravované identity pro prostředky Azure, může kód získat přístupové tokeny sloužící k ověření přístupu k prostředkům, které podporují ověřování Azure AD. Rozhraní Microsoft Azure AD Graph API podporuje ověřování Azure AD. V tomto kroku udělíte instančnímu objektu identity virtuálního počítače přístup k Azure AD Graphu, aby se mohl dotazovat na členství ve skupinách. Instanční objekty mají udělený přístup k Microsoft nebo Azure AD Graphu prostřednictvím **oprávnění aplikace**. Typ oprávnění aplikace, který potřebujete udělit, závisí na entitě, ke které chcete v MS nebo Azure AD Graphu získat přístup.

V tomto kurzu udělíte identitě virtuálního počítače schopnost dotazování členství ve skupinách pomocí oprávnění aplikace ```Directory.Read.All```. K udělení tohoto oprávnění budete potřebovat uživatelský účet, který má ve službě Azure AD přiřazenou roli globálního správce. Normálně byste oprávnění aplikace udělili tak, že byste přešli na registraci vaší aplikace na webu Azure Portal a přidali oprávnění tam. Spravované identity prostředků Azure ale ve službě Azure AD neregistrují objekty aplikací, pouze instanční objekty. K registraci oprávnění aplikace použijete nástroj pro příkazový řádek Azure AD PowerShell. 

Azure AD Graph:
- ID aplikace instančního objektu (používá se při udělování oprávnění aplikace): 00000002-0000-0000-c000-000000000000
- ID prostředku (používá se při žádosti o přístupový token ze spravovaných identit prostředků Azure): https://graph.windows.net
- Odkaz na obor oprávnění: [Referenční dokumentace k Azure AD Graph oprávněním](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)

### <a name="grant-application-permissions-using-azure-ad-powershell"></a>Udělení oprávnění aplikace přes Azure AD PowerShell

K použití této možnosti budete potřebovat Azure AD PowerShell. Pokud ho nemáte nainstalovaný, před pokračováním [si stáhněte nejnovější verzi](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2).

1. Otevřete okno PowerShellu a připojte se ke službě Azure AD:

   ```powershell
   Connect-AzureAD
   ```

2. Spuštěním následujících příkazů PowerShellu přiřaďte oprávnění aplikace ``Directory.Read.All`` k instančnímu objektu, který reprezentuje identitu virtuálního počítače.

   ```powershell
   $ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq 'myVM'"
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   $PermissionName = "Directory.Read.All"
   $AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}
   New-AzureAdServiceAppRoleAssignment -ObjectId $ManagedIdentitiesServicePrincipal.ObjectId -PrincipalId $ManagedIdentitiesServicePrincipal.ObjectId -ResourceId $GraphServicePrincipal.ObjectId -Id $AppRole.Id
   ``` 

   Výstup posledního příkazu vracejícího ID přiřazení by měl vypadat takto:
        
   `ObjectId`:`gzR5KyLAiUOTiqFhNeWZWBtK7ZKqNJxAiWYXYVHlgMs`

   `ResourceDisplayName`:`Windows Azure Active Directory`

   `PrincipalDisplayName`:`myVM` 

   Pokud volání `New-AzureAdServiceAppRoleAssignment` selže s chybou `bad request, one or more properties are invalid`, může už být oprávnění aplikace k instančnímu objektu identity virtuálního počítače přiřazené. Ke kontrole, jestli mezi identitou virtuálního počítače a Azure AD Graphem už existuje oprávnění aplikace, můžete použít následující příkazy PowerShellu:

   ```powershell
   $ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq '<VM-NAME>'"
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   $PermissionName = "Directory.Read.All"
   $AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}
   Get-AzureADServiceAppRoleAssignment -ObjectId $GraphServicePrincipal.ObjectId | Where-Object {$_.Id -eq $AppRole.Id -and $_.PrincipalId -eq $ManagedIdentitiesServicePrincipal.ObjectId}
   ```

   K výpisu všech oprávnění aplikace, která byla udělena Azure AD Graphu, můžete použít následující příkazy PowerShellu:

   ```powershell
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   Get-AzureADServiceAppRoleAssignment -ObjectId $GraphServicePrincipal.ObjectId
   ``` 

   K odebrání oprávnění aplikace, která byla udělena identitě virtuálního počítače pro Azure AD Graph, můžete použít následující příkazy PowerShellu:

   ```powershell
   $ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq '<VM-NAME>'"
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   $PermissionName = "Directory.Read.All"
   $AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}   
   $ServiceAppRoleAssignment = Get-AzureADServiceAppRoleAssignment -ObjectId $GraphServicePrincipal.ObjectId | Where-Object {$_.Id -eq $AppRole.Id -and $_.PrincipalId -eq $ManagedIdentitiesServicePrincipal.ObjectId}
   Remove-AzureADServiceAppRoleAssignment -AppRoleAssignmentId $ServiceAppRoleAssignment.ObjectId -ObjectId $ManagedIdentitiesServicePrincipal.ObjectId
   ```
 
## <a name="get-an-access-token-using-the-vms-identity-to-call-azure-ad-graph"></a>Získání přístupového tokenu pomocí identity virtuálního počítače k volání služby Azure AD Graph 

Pokud chcete k ověření vůči Azure AD Graphu použít spravovanou identitu virtuálního počítače přiřazenou systémem, musíte žádosti učinit z tohoto virtuálního počítače.

1. Na portálu přejděte na **Virtuální počítače**, přejděte ke svému virtuálnímu počítači s Windows a v okně **Přehled** klikněte na **Připojit**.  
2. Zadejte **Uživatelské jméno** a **Heslo**, které jste použili při vytváření virtuálního počítače s Windows.
3. Teď, když jste vytvořili připojení ke vzdálené ploše s virtuálním počítačem, otevřete ve vzdálené relaci PowerShell.  
4. Příkazem Invoke-WebRequest v PowerShellu požádejte místní spravované identity o koncový bod prostředků Azure k získání přístupového tokenu pro Azure AD Graph.

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://graph.windows.net' -Method GET -Headers @{Metadata="true"}
   ```

   Převeďte odpověď z objektu JSON na objekt PowerShellu.

   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   Extrahujte z odpovědi přístupový token.

   ```powershell
   $AccessToken = $content.access_token
   ```

5. Pomocí ID objektu instančního objektu identity virtuálního počítače (tuto hodnotu můžete načíst z proměnné deklarované v předchozích krocích: ``$ManagedIdentitiesServicePrincipal.ObjectId``) můžete dotazovat rozhraní Azure AD Graph API a načíst jeho členství ve skupinách. <OBJECT ID> nahraďte ID objektu z předchozího kroku a <ACCESS-TOKEN> dříve získaným přístupovým tokenem:

   ```powershell
   Invoke-WebRequest 'https://graph.windows.net/<Tenant ID>/servicePrincipals/<VM Object ID>/getMemberGroups?api-version=1.6' -Method POST -Body '{"securityEnabledOnly":"false"}' -Headers @{Authorization="Bearer $AccessToken"} -ContentType "application/json"
   ```
   
   Odpověď obsahuje `Object ID` skupiny, do které jste instanční objekt virtuálního počítače přidali v předchozích krocích.

   Odpověď:

   ```powershell   
   Content : {"odata.metadata":"https://graph.windows.net/<Tenant ID>/$metadata#Collection(Edm.String)","value":["<ObjectID of VM's group membership>"]}
   ```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak využít spravovanou identitu přiřazenou systémem na virtuálním počítači s Windows pro přístup k Azure AD Graphu.  Další informace o Azure AD Graphu najdete zde:

>[!div class="nextstepaction"]
>[Azure AD Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api)
