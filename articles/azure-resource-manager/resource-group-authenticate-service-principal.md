---
title: "Vytvoření identity pro aplikaci Azure pomocí prostředí PowerShell | Microsoft Docs"
description: "Popisuje, jak používat prostředí Azure PowerShell k vytvoření aplikace Azure Active Directory a objektu zabezpečení a jí udělit přístup k prostředkům prostřednictvím řízení přístupu na základě rolí. Ukazuje, jak k ověření aplikace s certifikátem."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: d2caf121-9fbe-4f00-bf9d-8f3d1f00a6ff
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/12/2018
ms.author: tomfitz
ms.openlocfilehash: 175d95c16484b90b13936c3be39b67749f0c3238
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="use-azure-powershell-to-create-a-service-principal-with-a-certificate"></a>Pomocí prostředí Azure PowerShell k vytvoření objektu služby pomocí certifikátu

Pokud máte aplikace nebo skriptu, která potřebuje přístup k prostředkům, můžete nastavit identity aplikace a ověřit aplikaci s svoje vlastní přihlašovací údaje. Tato identita se označuje jako hlavní název služby. Tento přístup umožňuje:

* Přiřadíte oprávnění k identitě aplikace, která se liší od vlastní oprávnění. Tato oprávnění jsou obvykle omezené na přesně co aplikaci je třeba provést.
* Při provádění bezobslužného skriptu, použijte certifikát pro ověřování.

> [!IMPORTANT]
> Místo vytvoření instančního objektu, zvažte použití identit Azure AD spravované služby pro vaši identitu aplikace. Azure AD MSI je funkce ve verzi public preview služby Azure Active Directory, který zjednodušuje vytváření identity pro kód. Pokud váš kód běží na službu, která podporuje Azure AD MSI a přistupuje k prostředkům, které podporují ověřování Azure Active Directory, Azure AD MSI je lepší volbou pro vás. Další informace o Azure AD MSI, včetně služby, které aktuálně podporují, najdete v části [spravované identita služby pro prostředky Azure](../active-directory/managed-service-identity/overview.md).

Tento článek ukazuje, jak vytvořit objekt služby, který ověřuje pomocí certifikátu. Objekt služby s heslem, naleznete v tématu [vytvořit objekt služby Azure pomocí prostředí Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).

## <a name="required-permissions"></a>Požadovaná oprávnění

K dokončení tohoto článku, musíte mít dostatečná oprávnění v Azure Active Directory a předplatné Azure. Konkrétně musí být schopná vytvořit aplikaci ve službě Azure Active Directory a přiřazení objektu služby roli.

Nejjednodušším způsobem, jak zkontrolovat, jestli má váš účet dostatečná oprávnění, je použít k tomu portál. V tématu [zkontrolujte požadované oprávnění](resource-group-create-service-principal-portal.md#required-permissions).

## <a name="create-service-principal-with-self-signed-certificate"></a>Vytvoření instančního objektu se certifikát podepsaný svým držitelem

Následující příklad popisuje jednoduchého scénáře. Používá [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) vytvoření objektu služby se certifikát podepsaný svým držitelem a používá [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) přiřadit [Přispěvatel](../active-directory/role-based-access-built-in-roles.md#contributor)role instanční objekt. Přiřazení role je vymezen na aktuálně vybrané předplatné. Chcete-li vybrat jiného předplatného, použijte [Set-AzureRmContext](/powershell/module/azurerm.profile/set-azurermcontext).

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" `
  -Subject "CN=exampleappScriptCert" `
  -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

$sp = New-AzureRMADServicePrincipal -DisplayName exampleapp `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

V příkladu v režimu spánku 20 sekund umožňující chvíli pro novou službu hlavní rozšíří v rámci Azure Active Directory. Pokud váš skript není dostatečně dlouho čekat, zobrazí se chybová zpráva: "Hlavní {ID} neexistuje v adresáři {DIR-ID}." Pokud chcete tuto chybu vyřešit, počkejte spusťte **New-AzureRmRoleAssignment** příkaz znovu.

V dalším příkladu je složitější, protože umožňuje nastavit obor pro přiřazení rolí, které se liší od vaším aktuálním předplatným Azure. Zadejte parametr ResourceGroup pouze v případě, že chcete omezit rozsah přiřazení role do skupiny prostředků. Pokud dojde k chybě při přiřazení role, se pokusí o přiřazení. Pokud nemáte Azure PowerShell 2.0 na Windows 10 nebo Windows Server 2016.

```powershell
Param (

 # Use to set scope to resource group. If no value is provided, scope is set to subscription.
 [Parameter(Mandatory=$false)]
 [String] $ResourceGroup,

 # Use to set subscription. If no value is provided, default subscription is used. 
 [Parameter(Mandatory=$false)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName
 )

 Login-AzureRmAccount
 Import-Module AzureRM.Resources

 if ($SubscriptionId -eq "") 
 {
    $SubscriptionId = (Get-AzureRmContext).Subscription.Id
 }
 else
 {
    Set-AzureRmContext -Subscription $SubscriptionId
 }

 if ($ResourceGroup -eq "")
 {
    $Scope = "/subscriptions/" + $SubscriptionId
 }
 else
 {
    $Scope = (Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop).ResourceId
 }

 $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleappScriptCert" -KeySpec KeyExchange
 $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
```

Pokud jste **nemají Windows 10 nebo Windows Server 2016**, budete muset stáhnout [certifikát podepsaný svým držitelem generátor](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) z webu Microsoft Script Center. Rozbalte obsah a importovat rutiny, které potřebujete.

```powershell
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```

Ve skriptu nahraďte následující dva řádky, které se vygeneruje certifikát.

```powershell
New-SelfSignedCertificateEx -StoreLocation CurrentUser `
  -StoreName My `
  -Subject "CN=exampleapp" `
  -KeySpec "Exchange" `
  -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Zadejte certifikát pomocí automatizované skript prostředí PowerShell

Vždy, když se přihlásíte jako hlavní název služby, je třeba zadat ID klienta adresáře pro vaši aplikaci AD. Klient je instance služby Azure Active Directory.

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertSubject,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -match $CertSubject }).Thumbprint
 Login-AzureRmAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

ID aplikace a ID klienta nejsou písmena, takže je můžete vložit přímo ve vašem skriptu. Pokud potřebujete zjistit ID klienta, použijte:

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

Pokud potřebujete zjistit ID aplikace, použijte:

```powershell
(Get-AzureRmADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>Vytvoření instančního objektu pomocí certifikátu od certifikační autority

Následující příklad používá k vytvoření instančního objektu certifikátu vydaného certifikační autority. Přiřazení je vymezen na zadané předplatné Azure. Přidá objekt služby k [Přispěvatel](../active-directory/role-based-access-built-in-roles.md#contributor) role. Pokud dojde k chybě při přiřazení role, se pokusí o přiřazení.

```powershell
Param (
 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword
 )

 Login-AzureRmAccount
 Import-Module AzureRM.Resources
 Set-AzureRmContext -Subscription $SubscriptionId
 
 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force

 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName
 New-AzureRmADSpCredential -ObjectId $ServicePrincipal.Id -CertValue $KeyValue -StartDate $PFXCert.NotBefore -EndDate $PFXCert.NotAfter
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
 
 $NewRole
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Zadejte certifikát pomocí automatizované skript prostředí PowerShell
Vždy, když se přihlásíte jako hlavní název služby, je třeba zadat ID klienta adresáře pro vaši aplikaci AD. Klient je instance služby Azure Active Directory.

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
 $PFXCert = New-Object `
  -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 `
  -ArgumentList @($CertPath, $CertPassword)
 $Thumbprint = $PFXCert.Thumbprint

 Login-AzureRmAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

ID aplikace a ID klienta nejsou písmena, takže je můžete vložit přímo ve vašem skriptu. Pokud potřebujete zjistit ID klienta, použijte:

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

Pokud potřebujete zjistit ID aplikace, použijte:

```powershell
(Get-AzureRmADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="change-credentials"></a>Změnit pověření

Můžete změnit pověření pro aplikaci AD, buď kvůli ohrožení zabezpečení nebo vypršení platnosti pověření [odebrat AzureRmADAppCredential](/powershell/resourcemanager/azurerm.resources/v3.3.0/remove-azurermadappcredential) a [New-AzureRmADAppCredential](/powershell/module/azurerm.resources/new-azurermadappcredential) rutiny.

Chcete-li odebrat všechny přihlašovací údaje pro aplikaci, použijte:

```powershell
Remove-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -All
```

Přidání certifikátu hodnoty, vytvořte certifikát podepsaný svým držitelem, jak je znázorněno v tomto článku. Pak použijte:

```powershell
New-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
```

## <a name="debug"></a>Ladění

Při vytváření objektu služby, může dojít k následujícím chybám:

* **"Authentication_Unauthorized"** nebo **"žádné předplatné nalezena v kontextu."** -Se zobrazí tato chyba, pokud váš účet nemá [požadovaná oprávnění](#required-permissions) v Azure Active Directory pro registraci aplikace. Obvykle se zobrazí tato chyba při jenom správci ve vašem Azure Active Directory můžete zaregistrovat aplikace a váš účet není správce. Požádejte správce buď můžete přiřadit role správce nebo umožňuje uživatelům registrovat aplikace.

* Váš účet **"nemá oprávnění k provedení akce 'Microsoft.Authorization/roleAssignments/write' u rozsahu: /subscriptions/ {guid}'."**  -Zobrazí tato chyba, pokud se váš účet nemá dostatečná oprávnění k přiřazení role identity. Požádejte správce předplatného můžete přidat do role správce přístupu uživatelů.

## <a name="next-steps"></a>Další postup
* Objekt služby s heslem, naleznete v tématu [vytvořit objekt služby Azure pomocí prostředí Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).
* Podrobné pokyny k integraci aplikace do Azure pro správu prostředků najdete v tématu [Příručka pro vývojáře k autorizaci s rozhraním API pro Azure Resource Manager](resource-manager-api-authentication.md).
* Podrobnější vysvětlení aplikací a objekty služby najdete v tématu [objekty aplikací a hlavní objekty služeb](../active-directory/active-directory-application-objects.md). 
* Další informace o ověřování Azure Active Directory najdete v tématu [scénáře ověřování pro Azure AD](../active-directory/active-directory-authentication-scenarios.md).
