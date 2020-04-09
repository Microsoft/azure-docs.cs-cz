---
title: Vytvoření identity aplikace Azure (PowerShell) | Azure
titleSuffix: Microsoft identity platform
description: Popisuje, jak používat prostředí Azure PowerShell k vytvoření aplikace Azure Active Directory a instančního objektu a udělení přístupu k prostředkům prostřednictvím řízení přístupu na základě role. Ukazuje, jak ověřit aplikaci certifikátem.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.date: 10/10/2019
ms.author: ryanwi
ms.reviewer: tomfitz
ms.openlocfilehash: 7bd8c3b25c23ba8586e38ec8eb7d1baefaa21633
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884166"
---
# <a name="how-to-use-azure-powershell-to-create-a-service-principal-with-a-certificate"></a>Postup: Vytvoření instančního objektu s certifikátem pomocí Azure PowerShellu

Pokud máte aplikaci nebo skript, který potřebuje přístup k prostředkům, můžete pro aplikaci nastavit identitu a ověřit tuto aplikaci jejími vlastními přihlašovacími údaji. Tato identita se označuje jako instanční objekt. Tento přístup vám umožní:

* Přiřadit identitě aplikace oprávnění, která se budou lišit od vašich vlastních oprávnění. Tato oprávnění jsou obvykle omezená přesně na to, co aplikace potřebuje dělat.
* Při provádění bezobslužného skriptu použít k ověření certifikát.

> [!IMPORTANT]
> Místo vytváření instančního objektu zvažte použití spravovaných identit pro prostředky Azure pro vaši identitu aplikace. Pokud váš kód běží na službě, která podporuje spravované identity a přistupuje k prostředkům, které podporují ověřování Azure Active Directory (Azure AD), spravované identity jsou lepší volbou pro vás. Další informace o spravovaných identitách pro prostředky Azure, včetně služeb, které je aktuálně podporují, najdete v [tématu Co je spravované identity pro prostředky Azure?](../managed-identities-azure-resources/overview.md).

Tento článek vám ukazuje, jak vytvořit instanční objekt, který se ověřuje certifikátem. Pokud chcete nastavit instanční objekt s heslem, podívejte se na článek věnovaný [vytvoření instančního objektu Azure s použitím prostředí Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).

K tomuto článku musíte mít [nejnovější verzi](/powershell/azure/install-az-ps) PowerShellu.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="required-permissions"></a>Požadovaná oprávnění

Chcete-li dokončit tento článek, musíte mít dostatečná oprávnění v azure a předplatné Azure. Konkrétně musíte být schopni vytvořit aplikaci ve službě Azure AD a přiřadit instanční objekt k roli.

Nejjednodušším způsobem, jak zkontrolovat, jestli má váš účet dostatečná oprávnění, je použít k tomu portál. Informace najdete v článku [Kontrola požadovaných oprávnění](howto-create-service-principal-portal.md#required-permissions).

## <a name="assign-the-application-to-a-role"></a>Přiřazení aplikace k roli
Chcete-li získat přístup k prostředkům v rámci předplatného, musíte aplikaci přiřadit k roli. Rozhodněte, která role nabízí správná oprávnění pro aplikaci. Informace o dostupných rolích najdete v tématu [RBAC: Built in Roles](/azure/role-based-access-control/built-in-roles).

Obor můžete nastavit na úrovni předplatného, skupiny prostředků nebo prostředku. Oprávnění jsou zděděna do nižších úrovní oboru. Například přidání aplikace do role *Čtečka* pro skupinu prostředků znamená, že může číst skupinu prostředků a všechny prostředky, které obsahuje. Chcete-li aplikaci povolit provádění akcí, jako je restartování, spuštění a zastavení instancí, vyberte roli *přispěvatele.*

## <a name="create-service-principal-with-self-signed-certificate"></a>Vytvoření instančního objektu s certifikátem podepsaným svým držitelem

Následující příklad popisuje jednoduchou situaci. Používá [New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal) k vytvoření instančního objektu s certifikátem podepsaným svým držitelem a používá [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) k přiřazení role [čtenáře](/azure/role-based-access-control/built-in-roles#reader) k instančnímu objektu. Přiřazení role je vymezené vaším aktuálně vybraným předplatným Azure. Chcete-li vybrat jiné předplatné, použijte [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext).

> [!NOTE]
> Rutina New-SelfSignedCertificateCertificate a modul PKI nejsou aktuálně v jádru prostředí PowerShell podporovány. 

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
New-AzRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $sp.ApplicationId
```

Příklad přepne 20 sekund, aby nějaký čas pro nový instanční objekt pro šíření v celém Azure AD. Pokud tato čekací doba skriptu není dostatečně dlouhá, zobrazí se vám chybová zpráva: Objekt zabezpečení {ID} není v adresáři {DIR-ID}." Chcete-li tuto chybu vyřešit, počkejte chvíli a spusťte příkaz **New-AzRoleAssignment** znovu.

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

Vždy, když se přihlásíte jako instanční objekt, musíte pro svoji aplikaci AD zadat ID tenanta adresáře. Tenant je instancí služby Azure AD.

```powershell
$TenantId = (Get-AzSubscription -SubscriptionName "Contoso Default").TenantId
$ApplicationId = (Get-AzADApplication -DisplayNameStartWith exampleapp).ApplicationId

 $Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -eq "CN=exampleappScriptCert" }).Thumbprint
 Connect-AzAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>Vytvoření instančního objektu pomocí certifikátu z certifikační autority

Následující příklad používá k vytvoření instančního objektu certifikát vydaný certifikační autoritou. Přiřazení je vymezené na zadané předplatné Azure. Přidá instanční objekt do role [čtečky.](../../role-based-access-control/built-in-roles.md#reader) Pokud dojde k chybě při přiřazení role, dojde k dalšímu pokusu o přiřazení.

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
    New-AzRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $ServicePrincipal.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
 
 $NewRole
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Poskytnutí certifikátu pomocí automatizovaného skriptu PowerShellu
Vždy, když se přihlásíte jako instanční objekt, musíte pro svoji aplikaci AD zadat ID tenanta adresáře. Tenant je instancí služby Azure AD.

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

Chcete-li změnit přihlašovací údaje pro aplikaci AD, ať už z důvodu ohrožení zabezpečení nebo vypršení platnosti pověření, použijte rutiny [Remove-AzADAppCredential](/powershell/module/az.resources/remove-azadappcredential) a [New-AzADAppCredential.](/powershell/module/az.resources/new-azadappcredential)

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

## <a name="debug"></a>Ladit

Při vytváření instančního objektu může dojít k následujícím chybám:

* **Ověřování_neautorizované** nebo **V kontextu se nenašlo žádné předplatné.** - Tato chyba se zobrazí, když váš účet nemá [požadovaná oprávnění](#required-permissions) na Azure AD k registraci aplikace. Tato chyba se obvykle zobrazuje, když aplikace mohou zaregistrovat jenom uživatelé správce ve službě Azure Active Directory a váš účet není správcem. Požádejte správce, aby vás buď přiřadil k roli správce, nebo aby uživatelům umožnil zaregistrovat aplikace.

* Váš účet **"nemá oprávnění k provedení akce 'Microsoft.Authorization/roleAssignments/write' nad rozsahem '/subscriptions/{guid}'."** - Tato chyba se zobrazí, pokud váš účet nemá dostatečná oprávnění k přiřazení role k identitě. Požádejte správce předplatného, aby vás přidal do role Správce přístupu uživatelů.

## <a name="next-steps"></a>Další kroky

* Pokud chcete nastavit instanční objekt s heslem, podívejte se na článek věnovaný [vytvoření instančního objektu Azure s použitím prostředí Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).
* Podrobnější vysvětlení aplikací a instančních objektů najdete v článku [Objekty aplikací a instanční objekty](app-objects-and-service-principals.md).
* Další informace o ověřování Azure AD najdete v [tématu scénáře ověřování pro Azure AD](authentication-scenarios.md).
