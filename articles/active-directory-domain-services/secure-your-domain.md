---
title: Zabezpečení vaší spravované domény Azure Active Directory Domain Services | Dokumentace Microsoftu
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
ms.openlocfilehash: e94cd9ca049cfdfd2321ce046714506ed1f23390
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67483282"
---
# <a name="secure-your-azure-ad-domain-services-managed-domain"></a>Zabezpečení vaší spravované domény služby Azure AD Domain Services
Tento článek pomáhá vám zabezpečit vaši spravovanou doménu. Můžete vypnout používání slabé šifrovacích sad a zakázat synchronizaci hodnot hash přihlašovacích údajů protokolů NTLM.

## <a name="install-the-required-powershell-modules"></a>Nainstalujte požadované moduly prostředí PowerShell

### <a name="install-and-configure-azure-ad-powershell"></a>Instalace a konfigurace Azure AD Powershellu
Postupujte podle pokynů v článku [instalace modulu Azure AD PowerShell a připojte se k Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Instalace a konfigurace Azure Powershellu
Postupujte podle pokynů v článku [instalace modulu Azure PowerShell a připojte se ke svému předplatnému Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="disable-weak-cipher-suites-and-ntlm-credential-hash-synchronization"></a>Zakázat slabý šifrovacích sad a synchronizaci hodnot hash přihlašovacích údajů protokolů NTLM
Pomocí následujícího skriptu prostředí PowerShell:
1. Ve spravované doméně deaktivujte podporu NTLM v1.
2. Zakázat synchronizaci hodnot hash hesel protokolů NTLM z místní AD.
3. Ve spravované doméně deaktivujte protokol TLS v1.

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
> Uživatelé (a účty služby) nelze provést jednoduché vazby LDAP, pokud jste zakázali synchronizaci hodnot hash hesel protokolů NTLM ve vaší instanci Azure AD Domain Services.  Další informace o zakázání synchronizace hodnot hash hesel protokolů NTLM, najdete v článku [zabezpečení vaší spravované domény služby Azure AD DOmain Services](secure-your-domain.md).
>
>

## <a name="next-steps"></a>Další postup
* [Principy synchronizace ve službě Azure AD Domain Services](synchronization.md)
