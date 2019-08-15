---
title: Vazba pomocí protokol Secure LDAP (LDAPs) do spravované domény Azure AD Domain Services | Microsoft Docs
description: Vytvoření vazby k Azure AD Domain Services spravované doméně pomocí protokolu Secure LDAP (LDAPs)
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 6871374a-0300-4275-9a45-a39a52c65ae4
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: iainfou
ms.openlocfilehash: cbc5bee0f4cc59f59af6e3f57219279cd8fcb030
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68988586"
---
# <a name="bind-to-an-azure-ad-domain-services-managed-domain-using-secure-ldap-ldaps"></a>Vytvoření vazby k Azure AD Domain Services spravované doméně pomocí protokolu Secure LDAP (LDAPs)

## <a name="before-you-begin"></a>Před zahájením
Dokončení [úlohy 4 – konfigurace DNS pro přístup ke spravované doméně z Internetu](active-directory-ds-ldaps-configure-dns.md)


## <a name="task-5-bind-to-the-managed-domain-over-ldap-using-ldpexe"></a>Úloha 5: Vytvoření vazby k spravované doméně přes LDAP pomocí nástroje LDP. exe
Nástroj LDP. exe, který je součástí balíčku nástrojů pro vzdálenou správu serveru, můžete použít k vytvoření vazby a hledání přes LDAP.

Nejdřív otevřete nástroj LDP a připojte se ke spravované doméně. Klikněte na **připojení** a v nabídce klikněte na **připojit...** . Zadejte název domény DNS spravované domény. Zadejte port, který se má použít pro připojení. Pro připojení LDAP použijte port 389. V případě připojení LDAPs použijte port 636. Kliknutím na tlačítko **OK** se připojte ke spravované doméně.

V dalším kroku se připojte ke spravované doméně. Klikněte na **připojení** a v nabídce klikněte na **BIND...** . Zadejte přihlašovací údaje uživatelského účtu patřícího do skupiny Správci AAD DC.

> [!IMPORTANT]
> Uživatelé (a účty služeb) nemůžou provádět jednoduché vazby LDAP, pokud jste na instanci Azure AD Domain Services zakázali synchronizaci hodnot hash hesel protokolu NTLM.  Další informace o zakázání synchronizace hodnot hash hesel protokolu NTLM najdete v článku [zabezpečení Azure AD Domain Services spravované domény](secure-your-domain.md).
>
>

Vyberte **zobrazení**a potom v nabídce vyberte možnost **strom** . Pole Základní rozlišující název nechte prázdné a klikněte na OK. Přejděte do kontejneru, který chcete vyhledat, klikněte pravým tlačítkem na kontejner a vyberte Hledat.

> [!TIP]
> - Uživatelé a skupiny synchronizované z Azure AD jsou uložené v organizační jednotce **AADDC Users** . Cesta pro hledání této organizační jednotky vypadá takto ```OU=AADDC Users,DC=CONTOSO100,DC=COM```:.
> - Účty počítačů pro počítače připojené ke spravované doméně jsou uloženy v organizační jednotce **AADDC Computers** . Cesta pro hledání této organizační jednotky vypadá takto ```OU=AADDC Computers,DC=CONTOSO100,DC=COM```:.
>
>

Další informace – [základy dotazů LDAP](https://docs.microsoft.com/windows/desktop/ad/creating-a-query-filter)


## <a name="task-6-lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet"></a>Úloha 6: Uzamčení zabezpečeného přístupu LDAP ke svojí spravované doméně prostřednictvím Internetu
> [!NOTE]
> Pokud jste nepovolili přístup LDAP ke spravované doméně přes Internet, přeskočte tuto úlohu konfigurace.
>
>

Než začnete s tímto úkolem, proveďte kroky popsané v [úloze 3](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md).

Když povolíte přístup přes Internet k spravované doméně prostřednictvím Internetu, vytvoří se bezpečnostní hrozba. Spravovaná doména je dosažitelná z Internetu na portu, který se používá pro zabezpečený protokol LDAP (tj. port 636). Můžete zvolit omezení přístupu ke spravované doméně na konkrétní známé IP adresy. Vytvořte skupinu zabezpečení sítě (NSG) a přidružte ji k podsíti, ve které jste povolili Azure AD Domain Services.

Vzorový NSG v následující tabulce zamkne zabezpečený přístup LDAP přes Internet. Pravidla v NSG umožňují příchozí zabezpečený přístup LDAP přes port TCP 636 jenom ze zadané sady IP adres. Výchozí pravidlo DenyAll platí pro všechny ostatní příchozí přenosy z Internetu. Pravidlo NSG, které povoluje přístup LDAP přes Internet ze zadaných IP adres, má vyšší prioritu než pravidlo DenyAll NSG.

![Ukázka NSG pro zabezpečení přístupu LDAP přes Internet](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**Další informace o** - [skupinách zabezpečení sítě](../virtual-network/security-overview.md).


## <a name="related-content"></a>Související obsah
* [Azure AD Domain Services – Průvodce Začínáme](create-instance.md)
* [Správa domény Azure AD Domain Services](manage-domain.md)
* [Základy dotazů LDAP](https://docs.microsoft.com/windows/desktop/ad/creating-a-query-filter)
* [Spravovat Zásady skupiny pro Azure AD Domain Services](manage-group-policy.md)
* [Skupiny zabezpečení sítě](../virtual-network/security-overview.md)
* [Vytvoření skupiny zabezpečení sítě](../virtual-network/tutorial-filter-network-traffic.md)


## <a name="next-step"></a>Další krok
[Řešení potíží se zabezpečeným protokolem LDAP ve spravované doméně](tshoot-ldaps.md)
