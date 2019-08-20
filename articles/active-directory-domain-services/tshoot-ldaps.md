---
title: Řešení potíží s protokol Secure LDAP (LDAPs) v Azure AD Domain Services | Microsoft Docs
description: Řešení potíží s protokol Secure LDAP (LDAPs) pro Azure AD Domain Services spravovanou doménu
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 285af0e5e5d5ab03027fc29064a5f3623ed10e2f
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617047"
---
# <a name="troubleshoot-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Řešení potíží s protokol Secure LDAP (LDAPs) pro Azure AD Domain Services spravovanou doménu

## <a name="connection-issues"></a>Problémy s připojením
Pokud máte potíže s připojením ke spravované doméně pomocí protokolu Secure LDAP:

* Řetěz vystavitele certifikátu zabezpečeného protokolu LDAP musí být v klientovi důvěryhodný. Můžete se rozhodnout přidat kořenovou certifikační autoritu do úložiště důvěryhodných kořenových certifikátů na klientovi pro vytvoření vztahu důvěryhodnosti.
* Ověřte, jestli se klient LDAP (například LDP. exe) připojí ke koncovému bodu zabezpečeného LDAP pomocí názvu DNS, ne IP adresy.
* Ověřte název DNS, ke kterému se klient LDAP připojuje. Musí se překládat na veřejnou IP adresu pro zabezpečený protokol LDAP ve spravované doméně.
* Ověřte, že certifikát zabezpečeného protokolu LDAP pro spravovanou doménu má název DNS v předmětu nebo v atributu alternativní názvy subjektu.
* Nastavení NSG pro virtuální síť musí umožňovat přenos z Internetu na port 636. Tento krok platí jenom v případě, že jste povolili zabezpečený přístup pomocí protokolu LDAP přes Internet.


## <a name="need-help"></a>Potřebujete pomoct?
Pokud stále dochází k potížím s připojením ke spravované doméně pomocí protokolu Secure LDAP, požádejte o technickou podporu [produktového týmu](contact-us.md) . Zahrňte následující informace, které vám pomůžou lépe diagnostikovat problém:
* Snímek obrazovky nástroje Ldp. exe, který provádí připojení a selhává.
* Vaše ID tenanta Azure AD a název domény DNS vaší spravované domény.
* Přesné uživatelské jméno, se kterým se snažíte vytvořit vazby.


## <a name="related-content"></a>Související obsah
* [Azure AD Domain Services – Průvodce Začínáme](tutorial-create-instance.md)
* [Správa domény Azure AD Domain Services](tutorial-create-management-vm.md)
* [Základy dotazů LDAP](https://technet.microsoft.com/library/aa996205.aspx)
* [Spravovat Zásady skupiny pro Azure AD Domain Services](manage-group-policy.md)
* [Skupiny zabezpečení sítě](../virtual-network/security-overview.md)
* [Vytvoření skupiny zabezpečení sítě](../virtual-network/tutorial-filter-network-traffic.md)
