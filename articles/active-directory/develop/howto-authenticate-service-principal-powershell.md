---
title: Vytvoření identity pro aplikaci Azure pomocí PowerShellu | Microsoft Docs
description: Popisuje, jak používat prostředí Azure PowerShell k vytvoření aplikace Azure Active Directory a instančního objektu a udělení přístupu k prostředkům prostřednictvím řízení přístupu na základě role. Ukazuje, jak ověřit aplikaci certifikátem.
services: active-directory
documentationcenter: na
author: CelesteDG
manager: mtillman
ms.assetid: d2caf121-9fbe-4f00-bf9d-8f3d1f00a6ff
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/24/2018
ms.author: celested
ms.reviewer: tomfitz
ms.openlocfilehash: c768d5a35016b0725d4dbca2a418c8e780812d62
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55091642"
---
# <a name="how-to-use-azure-powershell-to-create-a-service-principal-with-a-certificate"></a>Postup: Vytvoření instančního objektu s certifikátem pomocí Azure PowerShellu

Pokud máte aplikaci nebo skript, který potřebuje přístup k prostředkům, můžete pro aplikaci nastavit identitu a ověřit tuto aplikaci jejími vlastními přihlašovacími údaji. Tato identita se označuje jako instanční objekt. Tento přístup vám umožní:

* Přiřadit identitě aplikace oprávnění, která se budou lišit od vašich vlastních oprávnění. Tato oprávnění jsou obvykle omezená přesně na to, co aplikace potřebuje dělat.
* Při provádění bezobslužného skriptu použít k ověření certifikát.

> [!IMPORTANT]
> Místo vytvoření instančního objektu, zvažte použití spravované identity pro prostředky Azure pro vaši identitu aplikace. Pokud váš kód běží na službu, která podporuje spravované identity a přístupy prostředky, které podporují ověřování pomocí Azure Active Directory (Azure AD), spravované identity jsou vhodnější pro vás. Další informace o spravovaných identit pro prostředky Azure, včetně služby, které aktuálně podporují, najdete v článku [co je spravované identity pro prostředky Azure?](../managed-identities-azure-resources/overview.md).

Tento článek vám ukazuje, jak vytvořit instanční objekt, který se ověřuje certifikátem. Pokud chcete nastavit instanční objekt s heslem, podívejte se na článek věnovaný [vytvoření instančního objektu Azure s použitím prostředí Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).

K tomuto článku musíte mít [nejnovější verzi](/powershell/azure/install-az-ps) PowerShellu.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="required-permissions"></a>Požadovaná oprávnění

K dokončení tohoto článku, musíte mít dostatečná oprávnění ve službě Azure AD a předplatném Azure. Konkrétně musí být schopni vytvářet aplikace ve službě Azure AD a přiřazení instančního objektu k roli.

Nejjednodušším způsobem, jak zkontrolovat, jestli má váš účet dostatečná oprávnění, je použít k tomu portál. Informace najdete v článku [Kontrola požadovaných oprávnění](howto-create-service-principal-portal.md#required-permissions).

## <a name="create-service-principal-with-self-signed-certificate"></a>Vytvoření instančního objektu s certifikátem podepsaným svým držitelem

Následující příklad popisuje jednoduchou situaci. Používá [New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal) k vytvoření instančního objektu pomocí certifikátu podepsaného svým držitelem a používá [New-AzureRmRoleAssignment](/powershell/module/az.resources/new-azroleassignment) přiřadit [Přispěvatel](../../role-based-access-control/built-in-roles.md#contributor) roli instančnímu objektu služby. Přiřazení role je vymezené vaším aktuálně vybraným předplatným Azure. Chcete-li vybrat jiného předplatného, použijte [Set-AzContext](/powershell/module/az.profile/set-azcontext).

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" `
  -Subject "CN=exampleappScriptCert" `
  -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

$sp = New-AzADServicePrincipal -DisplayName exampleapp `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
Sleep 20
New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

V příkladu uspí 20 sekund nějakou dobu pro novou službu objektu zabezpečení na dokončení propagace v rámci Azure AD. Pokud váš skript není dostatečně dlouho čekat, zobrazí se vám chybová zpráva: "Objekt zabezpečení {ID} neexistuje v adresáři {DIR-ID}." Chcete-li vyřešit tuto chybu, počkejte chvíli, než spusťte **New-AzRoleAssignment** příkaz znovu.

Přiřazení role můžete vymezit na konkrétní skupinu prostředků pomocí parametru **ResourceGroupName**. Vymezit konkrétní rozsah můžete také pomocí parametrů **ResourceType** a **ResourceName**. 

Pokud **nemáte Windows 10 nebo Windows Server 2016**, musíte si z webu Microsoft Script Center stáhnout [generátor certifikátů podepsaných svým držitelem](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/). Rozbalte jeho obsah a importujte potřebnou rutinu.

```powershell
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```

Ve skriptu nahraďte následující dva řádky, aby se vygeneroval certifikát.

```powershell
New-SelfSignedCertificateEx -StoreLocation CurrentUser `
  -Subject "CN=exampleapp" `
  -KeySpec "Exchange" `
  -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Poskytnutí certifikátu pomocí automatizovaného skriptu PowerShellu

Vždy, když se přihlásíte jako instanční objekt, musíte pro svoji aplikaci AD zadat ID tenanta adresáře. Tenant je instance služby Azure AD.

```powershell
$TenantId = (Get-AzSubscription -SubscriptionName "Contoso Default").TenantId
$ApplicationId = (Get-AzADApplication -DisplayNameStartWith exampleapp).ApplicationId

 $Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -match "CN=exampleappScriptCert" }).Thumbprint
 Connect-AzAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>Vytvoření instančního objektu pomocí certifikátu z certifikační autority

Následující příklad používá k vytvoření instančního objektu certifikát vydaný certifikační autoritou. Přiřazení je vymezené na zadané předplatné Azure. Přidá instanční objekt k roli [Přispěvatel](../../role-based-access-control/built-in-roles.md#contributor). Pokud dojde k chybě při přiřazení role, dojde k dalšímu pokusu o přiřazení.

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

 Connect-AzAccount
 Import-Module Az.Resources
 Set-AzContext -Subscription $SubscriptionId
 
 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force

 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

 $ServicePrincipal = New-AzADServicePrincipal -DisplayName $ApplicationDisplayName
 New-AzADSpCredential -ObjectId $ServicePrincipal.Id -CertValue $KeyValue -StartDate $PFXCert.NotBefore -EndDate $PFXCert.NotAfter
 Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
 
 $NewRole
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Poskytnutí certifikátu pomocí automatizovaného skriptu PowerShellu
Vždy, když se přihlásíte jako instanční objekt, musíte pro svoji aplikaci AD zadat ID tenanta adresáře. Tenant je instance služby Azure AD.

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

 Connect-AzAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

ID aplikace a ID tenanta nejsou důvěrná, takže je můžete vložit přímo do skriptu. Pokud potřebujete načíst ID tenanta, použijte tento kód:

```powershell
(Get-AzSubscription -SubscriptionName "Contoso Default").TenantId
```

Pokud potřebujete načíst ID aplikace, použijte tento kód:

```powershell
(Get-AzADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="change-credentials"></a>Změna přihlašovacích údajů

Chcete-li změnit přihlašovací údaje pro aplikaci AD, a to buď z důvodu ohrožení zabezpečení nebo vypršení platnosti přihlašovacích údajů, použijte [odebrat AzADAppCredential](/powershell/module/az.resources/remove-azadappcredential) a [New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential) rutiny.

Pokud chcete odebrat všechny přihlašovací údaje pro aplikaci, použijte tento kód:

```powershell
Get-AzADApplication -DisplayName exampleapp | Remove-AzADAppCredential
```

Pokud chcete přidat hodnotu certifikátu, vytvořte certifikát podepsaný svým držitelem, jak je znázorněno v tomto článku. Potom použijte:

```powershell
Get-AzADApplication -DisplayName exampleapp | New-AzADAppCredential `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
```

## <a name="debug"></a>Ladění

Při vytváření instančního objektu může dojít k následujícím chybám:

* **Ověřování_neautorizované** nebo **V kontextu se nenašlo žádné předplatné.** -Se zobrazí tato chyba, pokud váš účet nemá [požadovaná oprávnění](#required-permissions) v Azure AD pro registraci aplikace. Obvykle se zobrazí tato chyba při jenom správci ve službě Azure Active Directory můžou registrovat aplikace, které váš účet není správce. Požádejte svého správce, aby vás přiřadil k roli správce nebo aby uživatelům umožnil registrovat aplikace.

* Váš účet **"nemá oprávnění k provedení akce"Microsoft.Authorization/roleAssignments/write"rozsahu"/subscriptions/ {guid}"."**  – Se zobrazí tato chyba, pokud váš účet nemá dostatečná oprávnění k přiřazení role na identitu. Požádejte správce předplatného, aby vás přidal do role Správce přístupu uživatelů.

## <a name="next-steps"></a>Další postup

* Pokud chcete nastavit instanční objekt s heslem, podívejte se na článek věnovaný [vytvoření instančního objektu Azure s použitím prostředí Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).
* Podrobné pokyny k integraci aplikace do Azure za účelem správy prostředků najdete v článku [Průvodce vývojáře k ověřování pomocí rozhraní API Azure Resource Manageru](../../azure-resource-manager/resource-manager-api-authentication.md).
* Podrobnější vysvětlení aplikací a instančních objektů najdete v článku [Objekty aplikací a instanční objekty](app-objects-and-service-principals.md).
* Další informace o ověřování Azure AD najdete v tématu [scénáře ověřování pro službu Azure AD](authentication-scenarios.md).
