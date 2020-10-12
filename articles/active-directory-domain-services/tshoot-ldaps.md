---
title: Řešení potíží se zabezpečením LDAP v Azure AD Domain Services | Microsoft Docs
description: Přečtěte si, jak řešit potíže se zabezpečením LDAP (LDAPs) pro Azure Active Directory Domain Services spravovanou doménu.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: iainfou
ms.openlocfilehash: c339893c12af74b5ecadb182b9aecb8000df65b9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86222951"
---
# <a name="troubleshoot-secure-ldap-connectivity-issues-to-an-azure-active-directory-domain-services-managed-domain"></a>Řešení potíží s zabezpečeným připojením LDAP do spravované domény Azure Active Directory Domain Services

Aplikace a služby, které používají protokol LDAP (Lightweight Directory Access Protocol) ke komunikaci s Azure Active Directory Domain Services (Azure služba AD DS), je možné [nakonfigurovat tak, aby používaly zabezpečený protokol LDAP](tutorial-configure-ldaps.md). Aby zabezpečený protokol LDAP správně fungoval, musí být otevřený vhodný certifikát a požadované síťové porty.

Tento článek vám pomůže řešit problémy s zabezpečeným přístupem LDAP v Azure služba AD DS.

## <a name="common-connection-issues"></a>Běžné problémy s připojením

Pokud máte potíže s připojením k spravované doméně Azure služba AD DS pomocí protokolu Secure LDAP, přečtěte si následující postup pro řešení potíží. Po každém kroku řešení potíží se znovu pokuste připojit ke spravované doméně:

* Řetěz vystavitele certifikátu zabezpečeného protokolu LDAP musí být v klientovi důvěryhodný. Kořenovou certifikační autoritu (CA) můžete přidat do úložiště důvěryhodných kořenových certifikátů na klientovi a vytvořit tak vztah důvěryhodnosti.
    * Nezapomeňte [exportovat a použít certifikát pro klientské počítače][client-cert].
* Ověřte, že certifikát zabezpečeného protokolu LDAP pro spravovanou doménu má název DNS v *předmětu* nebo v atributu *alternativní názvy subjektu* .
    * Zkontrolujte [požadavky na certifikát zabezpečení LDAP][certs-prereqs] a v případě potřeby vytvořte náhradní certifikát.
* Ověřte, jestli se klient LDAP, například *ldp.exe* , připojí ke koncovému bodu zabezpečeného LDAP pomocí názvu DNS, ne IP adresy.
    * Certifikát používaný pro spravovanou doménu neobsahuje IP adresy služby, jenom názvy DNS.
* Ověřte název DNS, ke kterému se klient LDAP připojuje. Musí se překládat na veřejnou IP adresu pro zabezpečený protokol LDAP ve spravované doméně.
    * Pokud se název DNS přeloží na interní IP adresu, aktualizujte záznam DNS tak, aby se přeložil na externí IP adresu.
* V případě externího připojení musí skupina zabezpečení sítě obsahovat pravidlo, které umožňuje přenos z Internetu na port TCP 636.
    * Pokud se ke spravované doméně můžete připojit pomocí zabezpečeného LDAP z prostředků přímo připojených k virtuální síti, ale ne externích připojení, ujistěte se, že jste [vytvořili pravidlo skupiny zabezpečení sítě, které povoluje zabezpečený provoz protokolu LDAP][ldaps-nsg].

## <a name="next-steps"></a>Další kroky

Pokud stále máte problémy, [otevřete žádost o podporu Azure][azure-support] , kde najdete další pomoc při řešení potíží.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
[certs-prereqs]: tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap
[client-cert]: tutorial-configure-ldaps.md#export-a-certificate-for-client-computers
[ldaps-nsg]: tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet
