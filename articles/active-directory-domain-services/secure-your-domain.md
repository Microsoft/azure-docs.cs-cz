---
title: Zabezpečený Azure AD Domain Services | Microsoft Docs
description: Přečtěte si, jak zakázat slabé šifry, staré protokoly a synchronizaci hodnot hash hesel protokolu NTLM pro Azure Active Directory Domain Services spravovanou doménu.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: a89c898e150facc9860d86e18a7acc42f5e0f441
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618854"
---
# <a name="disable-weak-ciphers-and-password-hash-synchronization-to-secure-an-azure-active-directory-domain-services-managed-domain"></a>Zakázání slabých šifr a synchronizace hodnot hash hesel k zabezpečení Azure Active Directory Domain Services spravované domény

Ve výchozím nastavení umožňuje Azure Active Directory Domain Services (Azure služba AD DS) použití šifr, jako je NTLM V1 a TLS v1. Tato šifra může být pro některé starší aplikace povinná, ale je pokládána za slabá a může být zakázaná, pokud je nepotřebujete. Pokud máte místní hybridní připojení pomocí Azure AD Connect, můžete také zakázat synchronizaci hodnot hash hesel protokolu NTLM.

V tomto článku se dozvíte, jak zakázat šifry protokolu NTLM V1 a TLS V1 a vypnout synchronizaci hodnot hash hesel protokolu NTLM.

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto článku potřebujete tyto prostředky:

* Musíte mít aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, buď synchronizovaný s místním adresářem, nebo jenom s cloudovým adresářem.
    * V případě potřeby [vytvořte tenanta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure k vašemu účtu][associate-azure-ad-tenant].
* Ve vašem tenantovi Azure AD je povolená a nakonfigurovaná spravovaná doména Azure Active Directory Domain Services.
    * V případě potřeby [vytvořte a nakonfigurujte Azure Active Directory Domain Services spravovanou doménu][create-azure-ad-ds-instance].
* Instalace a konfigurace Azure Powershellu.
    * V případě potřeby postupujte podle pokynů k [instalaci modulu Azure PowerShell a připojte se k předplatnému Azure](/powershell/azure/install-az-ps).
    * Ujistěte se, že se ke svému předplatnému Azure přihlašujete pomocí rutiny [Connect-AzAccount][Connect-AzAccount] .
* Nainstalujte a nakonfigurujte Azure AD PowerShell.
    * V případě potřeby postupujte podle pokynů k [instalaci modulu Azure AD PowerShell a připojte se ke službě Azure AD](/powershell/azure/active-directory/install-adv2).
    * Pomocí rutiny [Connect-AzureAD][Connect-AzureAD] se ujistěte, že se přihlašujete k TENANTOVI Azure AD.

## <a name="disable-weak-ciphers-and-ntlm-password-hash-sync"></a>Zakázání slabých šifr a synchronizace hodnot hash hesel NTLM

Pokud chcete zakázat slabé šifrovací sady a synchronizaci hodnot hash přihlašovacích údajů NTLM, přihlaste se ke svému účtu Azure a pak pomocí rutiny [Get-AzResource][Get-AzResource] Získejte prostředek Azure služba AD DS:

> [!TIP]
> Pokud se zobrazí chyba pomocí příkazu [Get-AzResource][Get-AzResource] , že prostředek *Microsoft. aad/DomainServices* neexistuje, [Zvyšte svůj přístup na správu všech předplatných Azure a skupin pro správu][global-admin].

```powershell
Login-AzAccount

$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"
```

Dále definujte *DomainSecuritySettings* a nakonfigurujte následující možnosti zabezpečení:

1. Zakažte podporu protokolu NTLM v1.
2. Zakažte synchronizaci hodnot hash hesel protokolu NTLM z místní služby AD.
3. Zakažte protokol TLS v1.

> [!IMPORTANT]
> Pokud zakážete synchronizaci hodnoty hash hesla protokolu NTLM ve spravované doméně Azure služba AD DS, uživatelé a účty služeb nemůžou provádět jednoduché vazby LDAP. Pokud potřebujete provést jednoduché vazby LDAP, v následujícím příkazu nenastavte možnost *"SyncNtlmPasswords" = "Disabled";* nastavení zabezpečení.

```powershell
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}
```

Nakonec pomocí rutiny [set-AzResource][Set-AzResource] použijte definované nastavení zabezpečení pro spravovanou doménu. V prvním kroku zadejte prostředek služby Azure služba AD DS a nastavení zabezpečení z předchozího kroku.

```powershell
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

Použití nastavení zabezpečení pro spravovanou doménu chvíli trvá.

> [!IMPORTANT]
> Po zakázání protokolu NTLM proveďte úplnou synchronizaci hodnot hash hesel v Azure AD Connect, abyste ze spravované domény odebrali všechny hodnoty hash hesel. Pokud zakážete protokol NTLM, ale nevynutíte synchronizaci hodnoty hash hesla, budou hodnoty hash hesla protokolu NTLM pro uživatelský účet odebrány pouze při další změně hesla. Toto chování může uživateli dovolit, aby se mohli i nadále přihlašovat, pokud mají přihlašovací údaje uložené v mezipaměti v systému, ve kterém se jako metoda ověřování používá protokol NTLM.
>
> Jakmile je hodnota hash hesla protokolu NTLM odlišná od hodnoty hash hesla protokolu Kerberos, záložní hodnota NTLM nebude fungovat. Přihlašovací údaje uložené v mezipaměti také nebudou fungovat, pokud má virtuální počítač připojení ke spravovanému řadiči domény.  

## <a name="next-steps"></a>Další kroky

Další informace o procesu synchronizace najdete v tématu [jak se objekty a přihlašovací údaje synchronizují ve spravované doméně][synchronization].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[global-admin]: ../role-based-access-control/elevate-access-global-admin.md
[synchronization]: synchronization.md

<!-- EXTERNAL LINKS -->
[Get-AzResource]: /powershell/module/az.resources/Get-AzResource
[Set-AzResource]: /powershell/module/Az.Resources/Set-AzResource
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
