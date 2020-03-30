---
title: Zabezpečené služby Azure AD Domain Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak zakázat slabé šifry, staré protokoly a synchronizaci hash hesel NTLM pro spravovanou doménu služby Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: 8eee516beaaf26ed25bd20f9689d26fdb1eb9b40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74546220"
---
# <a name="disable-weak-ciphers-and-password-hash-synchronization-to-secure-an-azure-ad-domain-services-managed-domain"></a>Zakázání slabých šifrach a synchronizace hash hesel za účelem zabezpečení spravované domény služby Azure AD Domain Services

Ve výchozím nastavení služba Azure Active Directory Domain Services (Azure AD DS) umožňuje použití šifer, jako jsou NTLM v1 a TLS v1. Tyto šifry mohou být vyžadovány pro některé starší aplikace, ale jsou považovány za slabé a mohou být zakázány, pokud je nepotřebujete. Pokud máte místní hybridní připojení pomocí služby Azure AD Connect, můžete také zakázat synchronizaci hodnot hashe hesel NTLM.

Tento článek ukazuje, jak zakázat šifrování NTLM v1 a TLS v1 a zakázat synchronizaci hash hesel NTLM.

## <a name="prerequisites"></a>Požadavky

Chcete-li tento článek dokončit, potřebujete následující zdroje:

* Aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte si účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, synchronizovaný s místním adresářem nebo s adresářem pouze pro cloud.
    * V případě potřeby [vytvořte klienta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure ke svému účtu][associate-azure-ad-tenant].
* Spravovaná doména Služby Azure Active Directory Domain Services povolená a nakonfigurovaná ve vašem tenantovi Azure AD.
    * V případě potřeby [vytvořte a nakonfigurujte instanci služby Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Instalace a konfigurace Azure Powershellu.
    * V případě potřeby postupujte podle pokynů k [instalaci modulu Azure PowerShell a připojte se k předplatnému Azure](/powershell/azure/install-az-ps).
    * Ujistěte se, že se přihlásíte k předplatnému Azure pomocí rutiny [Connect-AzAccount.][Connect-AzAccount]
* Nainstalujte a nakonfigurujte Azure AD PowerShell.
    * V případě potřeby postupujte podle pokynů k [instalaci modulu Azure AD PowerShell a připojte se k Azure AD](/powershell/azure/active-directory/install-adv2).
    * Ujistěte se, že se přihlásíte ke svému tenantovi Azure AD pomocí rutiny [Connect-AzureAD.][Connect-AzureAD]

## <a name="disable-weak-ciphers-and-ntlm-password-hash-sync"></a>Zakázání slabých šifr a synchronizace hash hesla NTLM

Chcete-li zakázat slabé šifrovací sady a synchronizaci hash pověření NTLM, přihlaste se ke svému účtu Azure a pak získejte prostředek Azure AD DS pomocí rutiny [Get-AzResource:][Get-AzResource]

> [!TIP]
> Pokud se zobrazí chyba pomocí příkazu [Get-AzResource,][Get-AzResource] že prostředek *Microsoft.AAD/DomainServices* neexistuje, [zvyšte přístup ke správě všech předplatných azure a skupin pro správu][global-admin].

```powershell
Login-AzAccount

$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"
```

Dále definujte *DomainSecuritySettings* a nakonfigurujte následující možnosti zabezpečení:

1. Zakažte podporu NTLM v1.
2. Zakažte synchronizaci hodnot hashe hesel NTLM z místního služby AD.
3. Zakažte TLS v1.

> [!IMPORTANT]
> Uživatelé a účty služeb nelze provádět LDAP jednoduché vazby, pokud zakážete ntlm synchronizace hash hesla ve spravované doméně Azure AD DS. Pokud potřebujete provést jednoduché vazby LDAP, nenastavujte *možnost "SyncNtlmPasswords"="Zakázáno";* možnost konfigurace zabezpečení v následujícím příkazu.

```powershell
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}
```

Nakonec použijte definované nastavení zabezpečení pro spravovanou doménu Azure AD DS pomocí rutiny [Set-AzResource.][Set-AzResource] Zadejte prostředek Azure AD DS od prvního kroku a nastavení zabezpečení z předchozího kroku.

```powershell
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

Trvá několik okamžiků pro nastavení zabezpečení, které mají být použity pro spravovanou doménu Azure AD DS.

## <a name="next-steps"></a>Další kroky

Další informace o procesu synchronizace najdete v tématu [Jak se objekty a přihlašovací údaje synchronizují ve spravované doméně Azure AD DS][synchronization].

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