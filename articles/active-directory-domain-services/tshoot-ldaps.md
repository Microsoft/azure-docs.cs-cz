---
title: Poradce při potížích se zabezpečeným protokolem LDAP ve službě Azure AD Domain Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak řešit potíže se zabezpečenou službou LDAP (LDAPS) pro spravovanou doménu služby Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: 22d1b6e2344256b52cfdbc48720a680a770a4216
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132173"
---
# <a name="troubleshoot-secure-ldap-connectivity-issues-to-an-azure-active-directory-domain-services-managed-domain"></a>Poradce při potížích se zabezpečeným připojením LDAP ke spravované doméně služby Azure Active Directory Domain Services

Aplikace a služby, které ke komunikaci se službou LdAP (LdAP) používají protokol LDAP (LIGHTWEIGHT Directory Access Services), lze [nakonfigurovat tak, aby používaly zabezpečené protokol LDAP](tutorial-configure-ldaps.md). Aby zabezpečené protokol LDAP fungovaly správně, musí být otevřen příslušný certifikát a požadované síťové porty.

Tento článek vám pomůže vyřešit problémy se zabezpečeným přístupem LDAP ve službě Azure AD DS.

## <a name="common-connection-issues"></a>Běžné problémy s připojením

Pokud máte potíže s připojením ke spravované doméně Azure AD DS pomocí zabezpečeného protokolu LDAP, přečtěte si následující kroky řešení potíží. Po každém kroku řešení potíží zkuste znovu připojit ke spravované doméně Azure AD DS:

* Řetězec vystavitele zabezpečeného certifikátu LDAP musí být v klientovi důvěryhodný. Můžete přidat kořenový certifikační úřad (CA) do důvěryhodného kořenového úložiště certifikátů v klientovi a vytvořit tak vztah důvěryhodnosti.
    * Ujistěte se, že [exportujete a použijete certifikát pro klientské počítače][client-cert].
* Ověřte, zda má zabezpečený certifikát LDAP pro spravovanou doménu název DNS v atributu *Předmět* nebo *Alternativní názvy předmětu.*
    * Zkontrolujte [požadavky na certifikát protokolu LDAP][certs-prereqs] a v případě potřeby vytvořte náhradní certifikát.
* Ověřte, zda se klient LDAP, například *program ldp.exe,* připojuje k zabezpečenému koncovému bodu LDAP pomocí názvu DNS, nikoli adresy IP.
    * Certifikát použitý pro spravovanou doménu Azure AD DS neobsahuje IP adresy služby, ale pouze názvy DNS.
* Zkontrolujte název DNS, ke kterému se klient LDAP připojuje. Musí vyřešit na veřejnou IP adresu pro zabezpečené LDAP na spravované doméně Azure AD DS.
    * Pokud se název DNS překládá na interní adresu IP, aktualizujte záznam DNS tak, aby přetísloval na externí adresu IP.
* Pro externí připojení musí skupina zabezpečení sítě obsahovat pravidlo, které umožňuje přenos na port TCP 636 z Internetu.
    * Pokud se můžete připojit ke spravované doméně Azure AD DS pomocí zabezpečené ldap z prostředků přímo připojených k virtuální síti, ale ne externí připojení, ujistěte se, že [vytvoříte pravidlo skupiny zabezpečení sítě povolit zabezpečené přenosy LDAP][ldaps-nsg].

## <a name="next-steps"></a>Další kroky

Pokud máte stále problémy, [otevřete žádost o podporu Azure][azure-support] pro další pomoc při řešení potíží.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
[certs-prereqs]: tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap
[client-cert]: tutorial-configure-ldaps.md#export-a-certificate-for-client-computers
[ldaps-nsg]: tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet
