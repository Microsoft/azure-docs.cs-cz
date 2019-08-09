---
title: Zabezpečte svou Azure Active Directory Domain Services spravovanou doménu | Microsoft Docs
description: Zabezpečení spravované domény
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2019
ms.author: iainfou
ms.openlocfilehash: 923ecae9dc649b8f5cdcfd447b78fdec0805927a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879150"
---
# <a name="secure-your-azure-ad-domain-services-managed-domain"></a>Zabezpečení Azure AD Domain Services spravované domény
Tento článek vám pomůže zabezpečit spravovanou doménu. Můžete vypnout používání slabých šifrovacích sad a zakázat synchronizaci hodnot hash přihlašovacích údajů NTLM.

## <a name="install-the-required-powershell-modules"></a>Instalace požadovaných modulů prostředí PowerShell

### <a name="install-and-configure-azure-ad-powershell"></a>Instalace a konfigurace Azure AD PowerShellu
Podle pokynů v článku [nainstalujte modul Azure AD PowerShell a připojte se ke službě Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Instalace a konfigurace Azure Powershellu
Podle pokynů v článku [nainstalujte modul Azure PowerShell a připojte se k předplatnému Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="disable-weak-cipher-suites-and-ntlm-credential-hash-synchronization"></a>Zakázat slabé šifrovací sady a synchronizaci hodnot hash přihlašovacích údajů NTLM
Následující skript prostředí PowerShell použijte k těmto akcím:

1. Ve spravované doméně deaktivujte podporu NTLM v1.
2. Zakažte synchronizaci hodnot hash hesel protokolu NTLM z místní služby AD.
3. Ve spravované doméně deaktivujte protokol TLS v1.

Pokud se zobrazí chyba s `Get-AzResource` příkazem, který prostředek *Microsoft. aad/DomainServices* neexistuje, zvyšte [svůj přístup na správu všech předplatných Azure a skupin pro správu](../role-based-access-control/elevate-access-global-admin.md).

```powershell
// Login to your Azure AD tenant
Login-AzAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// 1. Disable NTLM v1 support on the managed domain.
// 2. Disable the synchronization of NTLM password hashes from
//    on-premises AD to Azure AD and Azure AD Domain Services
// 3. Disable TLS v1 on the managed domain.
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}

// Apply the settings to the managed domain.
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

> [!IMPORTANT]
> Uživatelé (a účty služeb) nemůžou provádět jednoduché vazby LDAP, pokud jste na instanci Azure AD Domain Services zakázali synchronizaci hodnot hash hesel protokolu NTLM.  Další informace o zakázání synchronizace hodnot hash hesel protokolu NTLM najdete v článku [zabezpečení spravované domény služby Azure AD DOmain Services](secure-your-domain.md).
>
>

## <a name="next-steps"></a>Další postup
* [Pochopení synchronizace v Azure AD Domain Services](synchronization.md)
