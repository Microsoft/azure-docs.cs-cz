---
title: Vytvořit vazbu pomocí Secure LDAP (LDAPS) k spravované doméně služby Azure AD Domain Services | Dokumentace Microsoftu
description: Vytvořit vazbu k spravované doméně služby Azure AD Domain Services pomocí zabezpečeného protokolu LDAP (LDAPS)
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 6871374a-0300-4275-9a45-a39a52c65ae4
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: maheshu
ms.openlocfilehash: 9728d42710ce44226363ea4954d83fcc3efbfb75
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39502948"
---
# <a name="bind-to-an-azure-ad-domain-services-managed-domain-using-secure-ldap-ldaps"></a>Vytvořit vazbu k spravované doméně služby Azure AD Domain Services pomocí zabezpečeného protokolu LDAP (LDAPS)

## <a name="before-you-begin"></a>Než začnete
Kompletní [úloha 4 – konfigurace služby DNS pro přístup ke spravované doméně z Internetu](active-directory-ds-ldaps-configure-dns.md).


## <a name="task-5-bind-to-the-managed-domain-over-ldap-using-ldpexe"></a>Úkol 5: Vytvoření vazby ke spravované doméně přes LDAP použití LDP.exe
Můžete používat nástroje LDP.exe, který je součástí balíčku nástroje pro vzdálenou správu serveru pro svázání a vyhledávání protokolu LDAP.

Nejprve spustit nástroj LDP a připojte se k spravované doméně. Klikněte na tlačítko **připojení** a klikněte na tlačítko **připojit...**  v nabídce. Zadejte název domény DNS pro spravovanou doménu. Zadejte port pro připojení používat. Pro připojení LDAP použijte portu 389. Pro připojení protokolu LDAPS musí používejte port 636. Klikněte na tlačítko **OK** tlačítko pro připojení ke spravované doméně.

V dalším kroku vytvoření vazby ke spravované doméně. Klikněte na tlačítko **připojení** a klikněte na tlačítko **vazby...**  v nabídce. Zadejte přihlašovací údaje uživatelského účtu, které patří do skupiny "Správci AAD DC".

Vyberte **zobrazení**a pak vyberte **stromu** v nabídce. Základní rozlišující název pole ponechte prázdné a klikněte na tlačítko OK. Přejděte do kontejneru, který chcete vyhledat, klikněte pravým tlačítkem na kontejner a vyberte Hledat.

> [!TIP]
> - Uživatelé a skupiny synchronizované se službou Azure AD jsou uložené v **uživatelé AADDC** kontejneru. Vyhledávací cesta pro tento kontejner bude vypadat jako ```CN=AADDC\ Users,DC=CONTOSO100,DC=COM```.
> - Účty počítačů k počítačům připojeným k spravované doméně jsou uloženy v **kontejnery počítače AADDC** kontejneru. Vyhledávací cesta pro tento kontejner bude vypadat jako ```CN=AADDC\ Computers,DC=CONTOSO100,DC=COM```.
>
>

Další informace – [základy dotazů protokolu LDAP](https://technet.microsoft.com/library/aa996205.aspx)


## <a name="task-6-lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet"></a>Krok 6: Uzamčení přístupu k vaší spravované doméně přes internet secure LDAP
> [!NOTE]
> Pokud jste ještě nepovolili přístup protokolu LDAPS ke spravované doméně přes internet, přeskočte tuto úlohu konfigurace.
>
>

Před zahájením této úlohy, dokončení podle kroků popsaných v [úloha 3](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md).

Když povolíte přístup protokolu LDAPS přes internet k vaší spravované doméně, vytvoří ohrožení zabezpečení. Spravovaná doména je dosažitelný z Internetu na port používaný pro protokol secure LDAP (to znamená, že port 636). Můžete omezit přístup ke spravované doméně na konkrétní známé IP adresy. Vytvořit skupinu zabezpečení sítě (NSG) a přidružte jej k podsíti, kde jste povolili službu Azure AD Domain Services.

Ukázka skupiny zabezpečení sítě v následující tabulce uzamkne protokolu Secure LDAP přes internet. Pravidla NSG povolit příchozí přístup protokolu secure LDAP přes TCP port 636 pouze ze zadané sady IP adres. Výchozí pravidlo "DenyAll" platí pro všechny další příchozí provoz z Internetu. Pravidla NSG pro povolení protokolu LDAPS přístup přes internet ze zadaných IP adres má vyšší prioritu než pravidla DenyAll NSG.

![Ukázka skupiny NSG k zabezpečení přístupu k protokolu LDAPS přes internet](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**Další informace o** - [skupiny zabezpečení sítě](../virtual-network/security-overview.md).


## <a name="related-content"></a>Související obsah
* [Azure AD Domain Services – Příručka Začínáme](active-directory-ds-getting-started.md)
* [Správa spravované domény služby Azure AD Domain Services](active-directory-ds-admin-guide-administer-domain.md)
* [Základy dotazů protokolu LDAP](https://technet.microsoft.com/library/aa996205.aspx)
* [Správa zásad skupiny ve spravované doméně služby Azure AD Domain Services](active-directory-ds-admin-guide-administer-group-policy.md)
* [Skupiny zabezpečení sítě](../virtual-network/security-overview.md)
* [Vytvořte skupinu zabezpečení sítě](../virtual-network/tutorial-filter-network-traffic.md)


## <a name="next-step"></a>Další krok
[Řešení potíží s protokol secure LDAP ve spravované doméně](active-directory-ds-ldaps-troubleshoot.md)
