---
title: Vytvoření identity aplikace Azure (PowerShell) | Azure
titleSuffix: Microsoft identity platform
description: Popisuje, jak používat prostředí Azure PowerShell k vytvoření aplikace Azure Active Directory a instančního objektu a udělení přístupu k prostředkům prostřednictvím řízení přístupu na základě role. Ukazuje, jak ověřit aplikaci certifikátem.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev , devx-track-azurepowershell
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.date: 06/26/2020
ms.author: ryanwi
ms.reviewer: tomfitz
ms.openlocfilehash: b07679c6dd5e03131fde1439213783893971a088
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581818"
---
# <a name="how-to-use-azure-powershell-to-create-a-service-principal-with-a-certificate"></a>Postupy: použití Azure PowerShell k vytvoření instančního objektu s certifikátem

Pokud máte aplikaci nebo skript, který potřebuje přístup k prostředkům, můžete pro aplikaci nastavit identitu a ověřit tuto aplikaci jejími vlastními přihlašovacími údaji. Tato identita se označuje jako instanční objekt. Tento přístup vám umožní:

* Přiřadit identitě aplikace oprávnění, která se budou lišit od vašich vlastních oprávnění. Tato oprávnění jsou obvykle omezená přesně na to, co aplikace potřebuje dělat.
* Při provádění bezobslužného skriptu použít k ověření certifikát.

> [!IMPORTANT]
> Místo Vytvoření instančního objektu zvažte použití spravovaných identit pro prostředky Azure pro vaši identitu aplikace. Pokud je váš kód spuštěný na službě, která podporuje spravované identity, a přistupuje k prostředkům, které podporují ověřování Azure Active Directory (Azure AD), jsou spravované identity lepší volbou pro vás. Další informace o spravovaných identitách pro prostředky Azure, včetně toho, které služby v současnosti podporují, najdete v tématu [co jsou spravované identity pro prostředky Azure?](../managed-identities-azure-resources/overview.md).

Tento článek vám ukazuje, jak vytvořit instanční objekt, který se ověřuje certifikátem. Pokud chcete nastavit instanční objekt s heslem, podívejte se na článek věnovaný [vytvoření instančního objektu Azure s použitím prostředí Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).

K tomuto článku musíte mít [nejnovější verzi](/powershell/azure/install-az-ps) PowerShellu.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="required-permissions"></a>Požadovaná oprávnění

K dokončení tohoto článku musíte mít dostatečná oprávnění v rámci vašeho předplatného Azure AD i Azure. Konkrétně musíte být schopni vytvořit aplikaci v Azure AD a přiřadit instanční objekt roli.

Nejjednodušším způsobem, jak zkontrolovat, jestli má váš účet dostatečná oprávnění, je použít k tomu portál. Informace najdete v článku [Kontrola požadovaných oprávnění](howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

## <a name="assign-the-application-to-a-role"></a>Přiřazení aplikace k roli
Pokud chcete získat přístup k prostředkům ve vašem předplatném, musíte aplikaci přiřadit k roli. Rozhodněte, která role nabízí správná oprávnění pro aplikaci. Další informace o dostupných rolích najdete v tématu [předdefinované role Azure](../../role-based-access-control/built-in-roles.md).

Rozsah můžete nastavit na úrovni předplatného, skupiny prostředků nebo prostředku. Oprávnění jsou zděděna na nižší úrovně rozsahu. Například přidání aplikace do role *Čtenář* pro skupinu prostředků znamená, že může číst skupinu prostředků a všechny prostředky, které obsahuje. Pokud chcete aplikaci dovolit, aby prováděla akce, jako je restartování, spuštění a zastavení instancí, vyberte roli *Přispěvatel* .

## <a name="create-service-principal-with-self-signed-certificate"></a>Vytvoření instančního objektu s certifikátem podepsaným svým držitelem

Následující příklad popisuje jednoduchou situaci. Používá [New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal) k vytvoření instančního objektu s certifikátem podepsaným svým držitelem a pomocí [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) přiřadí roli [čtenáře](../../role-based-access-control/built-in-roles.md#reader) k instančnímu objektu. Přiřazení role je vymezené vaším aktuálně vybraným předplatným Azure. Pokud chcete vybrat jiné předplatné, použijte [set-AzContext](/powershell/module/Az.Accounts/Set-AzContext).

> [!NOTE]
> Rutina New-SelfSignedCertificate a modul PKI se v prostředí PowerShell Core momentálně nepodporují. 

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

Příkladem je režim spánku, který po dobu 20 sekund umožní určitému novému instančnímu objektu rozšíření v rámci služby Azure AD určitou dobu. Pokud tato čekací doba skriptu není dostatečně dlouhá, zobrazí se vám chybová zpráva: Objekt zabezpečení {ID} není v adresáři {DIR-ID}." Pokud chcete tuto chybu vyřešit, chvíli počkejte a pak znovu spusťte příkaz **New-AzRoleAssignment** .

Přiřazení role můžete vymezit na konkrétní skupinu prostředků pomocí parametru **ResourceGroupName**. Vymezit konkrétní rozsah můžete také pomocí parametrů **ResourceType** a **ResourceName**. 

Pokud nemáte **Windows 10 nebo Windows Server 2016**, Stáhněte si ze služby Microsoft Script Center [generátor certifikátů podepsaný svým držitelem](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) . Rozbalte jeho obsah a importujte potřebnou rutinu.

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

Pokaždé, když se přihlásíte jako instanční objekt, zadejte ID tenanta adresáře vaší aplikace AD. Tenant je instance služby Azure AD.

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

Následující příklad používá k vytvoření instančního objektu certifikát vydaný certifikační autoritou. Přiřazení je vymezené na zadané předplatné Azure. Přidá instanční objekt do role [čtenáře](../../role-based-access-control/built-in-roles.md#reader) . Pokud dojde k chybě při přiřazení role, dojde k dalšímu pokusu o přiřazení.

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
Pokaždé, když se přihlásíte jako instanční objekt, zadejte ID tenanta adresáře vaší aplikace AD. Tenant je instance služby Azure AD.

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

Pokud chcete změnit přihlašovací údaje pro aplikaci AD z důvodu bezpečnostního ohrožení nebo vypršení platnosti přihlašovacích údajů, použijte rutiny [Remove-AzADAppCredential](/powershell/module/az.resources/remove-azadappcredential) a [New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential) .

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

* **Ověřování_neautorizované** nebo **V kontextu se nenašlo žádné předplatné.** – Tato chyba se zobrazí, pokud váš účet nemá [požadovaná oprávnění](#required-permissions) k registraci aplikace v Azure AD. Tato chyba se obvykle zobrazuje, když můžou registrovat aplikace jenom uživatelé Azure Active Directory s právy pro správu, a váš účet není správce. Požádejte správce, aby vám přidělil roli správce nebo aby uživatelům umožnil registraci aplikací.

* Váš účet nemá **autorizaci k provedení akce "Microsoft. Authorization/roleAssignments/Write" nad oborem "/Subscriptions/{GUID}". "** – Tato chyba se zobrazí, pokud váš účet nemá dostatečná oprávnění k přiřazení role k identitě. Požádejte správce předplatného, aby vás přidal do role Správce přístupu uživatelů.

## <a name="next-steps"></a>Další kroky

* Pokud chcete nastavit instanční objekt s heslem, podívejte se na článek věnovaný [vytvoření instančního objektu Azure s použitím prostředí Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).
* Podrobnější vysvětlení aplikací a instančních objektů najdete v článku [Objekty aplikací a instanční objekty](app-objects-and-service-principals.md).
* Další informace o ověřování Azure AD najdete v tématu [scénáře ověřování pro Azure AD](./authentication-vs-authorization.md).
* Informace o práci s registracemi aplikací pomocí **Microsoft Graph** najdete v referenčních informacích k rozhraní API pro [aplikace](/graph/api/resources/application) .
