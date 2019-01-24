---
title: Zabezpečení vaší spravované domény Azure Active Directory Domain Services | Dokumentace Microsoftu
description: Zabezpečení vaší spravované domény
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: ergreenl
ms.openlocfilehash: 1a9a8ee13145e6326c234a4eda053ed49faf33aa
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856668"
---
# <a name="secure-your-azure-ad-domain-services-managed-domain"></a>Zabezpečení vaší spravované domény služby Azure AD Domain Services
Tento článek pomáhá vám zabezpečit vaši spravovanou doménu. Můžete vypnout používání slabé šifrovacích sad a zakázat synchronizaci hodnot hash přihlašovacích údajů protokolů NTLM.

## <a name="install-the-required-powershell-modules"></a>Nainstalujte požadované moduly prostředí PowerShell

### <a name="install-and-configure-azure-ad-powershell"></a>Instalace a konfigurace Azure AD Powershellu
Postupujte podle pokynů v článku [instalace modulu Azure AD PowerShell a připojte se k Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Instalace a konfigurace Azure Powershellu
Postupujte podle pokynů v článku [instalace modulu Azure PowerShell a připojte se ke svému předplatnému Azure](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="disable-weak-cipher-suites-and-ntlm-credential-hash-synchronization"></a>Zakázat slabý šifrovacích sad a synchronizaci hodnot hash přihlašovacích údajů protokolů NTLM
Pomocí následujícího skriptu prostředí PowerShell:
1. Ve spravované doméně deaktivujte podporu NTLM v1.
2. Zakázat synchronizaci hodnot hash hesel protokolů NTLM z místní AD.
3. Ve spravované doméně deaktivujte protokol TLS v1.

```powershell
// Login to your Azure AD tenant
Login-AzureRmAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzureRmResource -ResourceType "Microsoft.AAD/DomainServices"

// 1. Disable NTLM v1 support on the managed domain.
// 2. Disable the synchronization of NTLM password hashes from
//    on-premises AD to Azure AD and Azure AD Domain Services
// 3. Disable TLS v1 on the managed domain.
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}

// Apply the settings to the managed domain.
Set-AzureRmResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

## <a name="next-steps"></a>Další postup
* [Principy synchronizace ve službě Azure AD Domain Services](active-directory-ds-synchronization.md)
