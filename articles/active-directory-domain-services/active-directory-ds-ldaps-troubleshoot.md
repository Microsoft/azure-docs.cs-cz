---
title: Řešení potíží s protokol Secure LDAP (LDAPS) ve službě Azure AD Domain Services | Dokumentace Microsoftu
description: Řešení potíží s Secure LDAP (LDAPS) pro spravované domény služby Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: maheshu
ms.openlocfilehash: 9713a06bbf6a61b316e061cb851721a3554cd632
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39503668"
---
# <a name="troubleshoot-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Řešení potíží s Secure LDAP (LDAPS) pro spravované domény služby Azure AD Domain Services

## <a name="connection-issues"></a>Problémy s připojením
Pokud máte potíže s připojením ke spravované doméně pomocí protokolu secure LDAP:

* Na straně klienta musí důvěřovat řetězu vystavitele certifikát protokolu secure LDAP. Můžete přidat kořenové certifikační autority do úložiště důvěryhodných kořenových certifikátů na straně klienta k navázání vztahu důvěryhodnosti.
* Ověřte, že klienta protokolu LDAP (například ldp.exe) připojí k zabezpečení koncového bodu protokolu LDAP pomocí názvu DNS není IP adresa.
* Zkontrolujte název DNS, který klient LDAP se připojí k. Musí se přeložit na veřejnou IP adresu pro protokol secure LDAP ve spravované doméně.
* Ověřte, že certifikát protokolu secure LDAP pro spravovanou doménu má v předmětu nebo alternativní názvy subjektů atribut názvu DNS.
* Nastavení skupiny zabezpečení sítě pro virtuální síť musí umožňovat provoz na portu 636 z Internetu. Tento krok platí jenom v případě, že jste povolili přístup protokolu secure LDAP přes internet.


## <a name="need-help"></a>Potřebujete pomoct?
Pokud stále máte potíže s připojením ke spravované doméně pomocí protokolu secure LDAP [kontaktováním produktového týmu](active-directory-ds-contact-us.md) nápovědu. Uveďte následující informace umožňující provádět lepší diagnostiku problému:
* Snímek obrazovky s ldp.exe vytváření připojení a selhání.
* Vaše ID tenanta Azure AD a název domény DNS vaší spravované domény.
* Přesné uživatelské jméno, které se snažíte vytvořit vazbu jako.


## <a name="related-content"></a>Související obsah
* [Azure AD Domain Services – Příručka Začínáme](active-directory-ds-getting-started.md)
* [Správa spravované domény služby Azure AD Domain Services](active-directory-ds-admin-guide-administer-domain.md)
* [Základy dotazů protokolu LDAP](https://technet.microsoft.com/library/aa996205.aspx)
* [Správa zásad skupiny ve spravované doméně služby Azure AD Domain Services](active-directory-ds-admin-guide-administer-group-policy.md)
* [Skupiny zabezpečení sítě](../virtual-network/security-overview.md)
* [Vytvořte skupinu zabezpečení sítě](../virtual-network/tutorial-filter-network-traffic.md)
