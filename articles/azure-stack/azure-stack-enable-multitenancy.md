---
title: Víceklientská architektura v Azure stacku
description: Zjistěte, jak podporovat více adresářů Azure Active Directory ve službě Azure Stack
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: patricka
ms.openlocfilehash: e61b4457cd88c236145ce7595ee7db4340538465
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39330800"
---
# <a name="multi-tenancy-in-azure-stack"></a>Víceklientská architektura v Azure stacku

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Můžete nakonfigurovat služby Azure Stack pro podporu uživatelů z více tenantů Azure Active Directory (Azure AD) pro použití služeb ve službě Azure Stack. Představte si třeba následující scénář:

 - Jste správcem služby contoso.onmicrosoft.com, kde je nainstalovaný Azure Stack.
 - Jan je správcem adresáře fabrikam.onmicrosoft.com, kde se nachází uživatelé typu Host. 
 - Její společnosti přijme služby IaaS a PaaS ve vaší společnosti a je potřeba povolit uživatelům z adresáře hosta (fabrikam.onmicrosoft.com) přihlásit a používat prostředky služby Azure Stack v contoso.onmicrosoft.com.

Tato příručka obsahuje kroky potřebné v rámci tohoto scénáře, chcete-li nakonfigurovat více tenantů ve službě Azure Stack. V tomto scénáři jste a Mary musíte dokončit postup povolení uživatelům k využívání služeb v nasazení Azure Stack ve společnosti Contoso a Fabrikam.  

## <a name="enable-multi-tenancy"></a>Povolení víceklientské architektury

Existuje několik požadavků pro účet předtím, než nakonfigurujete více tenantů ve službě Azure Stack:
  
 - Jste a Mary musí administrativní kroky koordinaci mezi adresáři, který je nainstalovaný Azure Stack (Contoso) a adresář hostů (Fabrikam).  
 - Ujistěte se, že jste [nainstalované](azure-stack-powershell-install.md) a [nakonfigurované](azure-stack-powershell-configure-admin.md) prostředí PowerShell pro Azure Stack.
 - [Stáhnout nástroje Azure Stack](azure-stack-powershell-download.md)a importovat moduly připojit a Identity:

    ````PowerShell  
    Import-Module .\Connect\AzureStack.Connect.psm1
    Import-Module .\Identity\AzureStack.Identity.psm1
    ````

 - Bude vyžadovat Mary [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) přístup ke službě Azure Stack. 

### <a name="configure-azure-stack-directory"></a>Konfigurace adresáře služby Azure Stack

V této části nakonfigurujete tak, aby přihlášení ze společnosti Fabrikam Azure AD directory tenantů Azure Stack.

Připojení ke službě Azure Stack konfigurací Azure Resource Manageru a akceptovat uživatele objekty zabezpečení v tenantu Active directory hostované služby hosta Tenantu Active Directory (Fabrikam).

````PowerShell  
## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantToBeOnboarded = "fabrikam.onmicrosoft.com"

## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
$ResourceGroupName = "system.local"

## Replace the value below with the region location of the resource group. 
$location = "local"

Register-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
 -DirectoryTenantName $azureStackDirectoryTenant `
 -GuestDirectoryTenantName $guestDirectoryTenantToBeOnboarded `
 -Location $location `
 -ResourceGroupName $ResourceGroupName
````

### <a name="configure-guest-directory"></a>Nakonfigurujte adresář hostů

Po dokončení kroků v adresáři služby Azure Stack Mary musí poskytnout souhlas adresář hostů přístup ke službě Azure Stack a Azure Stack zaregistrovat adresář hostů. 

#### <a name="registering-azure-stack-with-the-guest-directory"></a>Registrace Azure Stack s adresář hostů

Jakmile správce adresáře hosta poskytl souhlas pro Azure Stack pro přístup do adresáře společnosti Fabrikam, Mary zaregistrujte Azure Stack s tenantem adresář společnosti Fabrikam.

````PowerShell
## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantName = "fabrikam.onmicrosoft.com"

Register-AzSWithMyDirectoryTenant `
 -TenantResourceManagerEndpoint $tenantARMEndpoint `
 -DirectoryTenantName $guestDirectoryTenantName `
 -Verbose 
````

> [!IMPORTANT]
> Pokud správce služby Azure Stack v budoucnu nainstaluje nové služby nebo aktualizace, budete muset znovu spusťte tento skript.
>
> Tento skript spusťte znovu kdykoli zkontrolovat stav služby Azure Stack aplikace ve vašem adresáři.

### <a name="direct-users-to-sign-in"></a>Přímé přihlášení uživatele

Teď, když jste a Mary jste dokončili kroky pro začlenění Mary adresáře, může směrovat Mary Fabrikam uživatelům umožní přihlásit.  Fabrikam uživatele (to znamená, že uživatelé s příponou fabrikam.onmicrosoft.com) přihlaste návštěvou https://portal.local.azurestack.external.  

Marie bude směrovat všechny [cizích objektů zabezpečení](../role-based-access-control/rbac-and-directory-admin-roles.md) v adresáři společnosti Fabrikam (to znamená, že uživatelé v adresáři společnosti Fabrikam bez přípony fabrikam.onmicrosoft.com) k přihlášení pomocí https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.  Pokud se nepoužívají tuto adresu URL, se odesílají do jejich výchozí adresář (Fabrikam) a zobrazí se chyba s upozorněním, že jejich správu. nevyjádřil.

## <a name="disable-multi-tenancy"></a>Zakázat víceklientské architektury

Pokud už nechcete více tenantů ve službě Azure Stack, je víceklientská zakázat provedením následujících kroků v pořadí:

1. Jako správce adresáře hosta (v tomto scénáři Marie) spusťte *Unregister-AzsWithMyDirectoryTenant*. Rutina odinstaluje všechny aplikace služby Azure Stack z nového adresáře.

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
    $tenantARMEndpoint = "https://management.local.azurestack.external"
        
    ## Replace the value below with the guest tenant directory. 
    $guestDirectoryTenantName = "fabrikam.onmicrosoft.com"
    
    Unregister-AzsWithMyDirectoryTenant `
     -TenantResourceManagerEndpoint $tenantARMEndpoint `
     -DirectoryTenantName $guestDirectoryTenantName `
     -Verbose 
    ```

2. Jako správce služeb Azure stacku (je v tomto scénáři), spusťte *Unregister-AzSGuestDirectoryTenant*. 

    ``` PowerShell  
    ## The following Azure Resource Manaager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
    $adminARMEndpoint = "https://adminmanagement.local.azurestack.external"
    
    ## Replace the value below with the Azure Stack directory
    $azureStackDirectoryTenant = "contoso.onmicrosoft.com"
    
    ## Replace the value below with the guest tenant directory. 
    $guestDirectoryTenantToBeDecommissioned = "fabrikam.onmicrosoft.com"
    
    ## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
    $ResourceGroupName = "system.local"
    
    Unregister-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
     -DirectoryTenantName $azureStackDirectoryTenant `
     -GuestDirectoryTenantName $guestDirectoryTenantToBeDecommissioned `
     -ResourceGroupName $ResourceGroupName
    ```

    > [!WARNING]
    > Kroky zakázat víceklientská architektura je potřeba provést v pořadí. Krok #1 selže, pokud nejprve dokončení kroku #2.

## <a name="next-steps"></a>Další postup

- [Spravovat delegované poskytovatele.](azure-stack-delegated-provider.md)
- [Klíčové koncepty služby Azure Stack](azure-stack-key-features.md)
