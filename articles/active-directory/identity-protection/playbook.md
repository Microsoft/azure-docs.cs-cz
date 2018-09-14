---
title: Azure Active Directory Identity Protection playbook | Dokumentace Microsoftu
description: Zjistěte, jak Azure AD Identity Protection umožňuje omezit schopnost útočníka zneužít ohrožení zabezpečení identity nebo zařízení a zabezpečit identitu nebo zařízení, který byl dříve podezřelý nebo známé u něho ohrožena bezpečnost.
services: active-directory
keywords: cloud discovery, Správa aplikací, zabezpečení, rizika, úroveň rizika, ohrožení zabezpečení, zásady zabezpečení služby Azure active directory identity protection
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 60836abf-f0e9-459d-b344-8e06b8341d25
ms.service: active-directory
ms.component: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 5c2a37f3753292c6db847a4a9152bd4506f8fa5e
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45580482"
---
# <a name="azure-active-directory-identity-protection-playbook"></a>Playbook Azure Active Directory Identity Protection

Playbook vám umožní:

* Naplnění dat v rámci prostředí Identity Protection tak, že simulace rizikových událostí a ohrožení zabezpečení
* Nastavení zásad podmíněného přístupu na základě rizik a otestujte dopad těchto zásad


## <a name="simulating-risk-events"></a>Simulace rizikových událostí

Tato část obsahuje kroky pro simulaci následující typy rizikových událostí:

* Přihlášení z anonymních IP adres (jednoduchý)
* Přihlášení z neznámých míst (střední)
* Nemožná cesta do netypických míst (obtížné)

Další rizikové události nemůže být Simulovaná bezpečným způsobem.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Přihlášení z anonymních IP adres

Další informace o tuto rizikovou událost, naleznete v tématu [přihlášení z anonymních IP adres](../reports-monitoring/concept-risk-events.md#sign-ins-from-anonymous-ip-addresses). 

Dokončuje se následující postup vyžaduje použití:

- [Tor prohlížeče](https://www.torproject.org/projects/torbrowser.html.en) pro simulaci anonymní IP adresy. Můžete potřebovat použít virtuální počítač, pokud vaše organizace omezí pomocí prohlížeče sítě Tor.
- Testovací účet, který ještě není zaregistrovaný k vícefaktorovému ověřování.

**Pro simulaci u přihlášení z anonymní IP adresy, proveďte následující kroky**:

1. Použití [Tor prohlížeče](https://www.torproject.org/projects/torbrowser.html.en), přejděte na [ https://myapps.microsoft.com ](https://myapps.microsoft.com).   
2. Zadejte přihlašovací údaje účtu, které se mají zobrazit v **přihlášení z anonymních IP adres** sestavy.

Přihlášení se zobrazí na řídicím panelu služby Identity Protection během 10 až 15 minut. 

### <a name="sign-ins-from-unfamiliar-locations"></a>Přihlášení z neznámých míst

Další informace o tuto rizikovou událost, naleznete v tématu [přihlášení z neznámých míst](../reports-monitoring/concept-risk-events.md#sign-in-from-unfamiliar-locations). 

Pro simulaci neznámých míst, budete muset přihlásit z umístění a zařízení, které testovací účet nebyl přihlásil z před.

Následující postup používá nově vytvořený:

- Připojení VPN k simulaci nové umístění.

- Virtuální počítač, jak simulovat nové zařízení.

Dokončení následující postup, musíte použít uživatelský účet, který má:

- Minimálně 30 dní přihlášení historie.
- Povolené vícefaktorové ověřování.


**Pro simulaci u přihlášení z neznámého umístění, proveďte následující kroky**:

1. Při přihlašování pomocí účtu test, nezdaří ověřovacím testem MFA není předáním ověřovacím testem MFA.
2. Pomocí nové síť VPN, přejděte do [ https://myapps.microsoft.com ](https://myapps.microsoft.com) a zadejte přihlašovací údaje účtu testu.
   

Přihlášení se zobrazí na řídicím panelu služby Identity Protection během 10 až 15 minut.

### <a name="impossible-travel-to-atypical-location"></a>Nemožná cesta do netypických umístění

Další informace o tuto rizikovou událost, naleznete v tématu [neuskutečnitelné cesty na neobvyklé místo](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations). 

Budete jen Simulovat podmínky neuskutečnitelné cesty je obtížné, protože používá algoritmus strojového učení při odstraňování plevele na false – pozitivní například neuskutečnitelné cesty ze známé zařízení nebo přihlášení z VPN, které se používají jinými uživateli v adresáři. Algoritmus vyžaduje kromě toho historie přihlašování 14 dnů a 10 přihlašovacích údajů uživatele, před jeho zahájením generování rizikové události. Z důvodu složité modely strojového učení a výše uvedených pravidel je pravděpodobné, že následující kroky nevedou k rizikové události. Můžete chtít replikovat postupu pro několik účtů služby Azure AD k publikování tuto rizikovou událost.


**Pro simulaci nemožná cesta do netypických umístění, proveďte následující kroky**:

1. Pomocí standardního prohlížeče, přejděte do [ https://myapps.microsoft.com ](https://myapps.microsoft.com).  
2. Zadejte přihlašovací údaje účtu, který chcete generovat události rizika neuskutečnitelné cesty pro.
3. Změna uživatelského agenta. Můžete změnit uživatelského agenta v aplikaci Internet Explorer z nástroje pro vývojáře, nebo změna vašeho uživatelského agenta v aplikaci Firefox nebo Chrome pomocí doplňku přepínání uživatelského agenta.
4. Změna IP adresy. IP adresu můžete změnit pomocí sítě VPN, doplněk Tor, nebo opravíte vytvořením nového počítače v Azure v různých datových center.
5. Přihlaste se do [ https://myapps.microsoft.com ](https://myapps.microsoft.com) pomocí stejných přihlašovacích údajů, jako předtím a během pár minut po předchozí přihlášení.

Přihlášení se zobrazí na řídicím panelu služby Identity Protection během 2 – 4 hodiny.

## <a name="simulating-vulnerabilities"></a>Simulace chyby
Ohrožení zabezpečení jsou slabá místa v prostředí Azure AD, které může zneužít chybný actor. Aktuálně jsou prezentované 3 typy ohrožení zabezpečení v Azure AD Identity Protection, které využívají dalších funkcích služby Azure AD. Tyto chyby se zobrazí na řídicím panelu služby Identity Protection automaticky po těchto funkcí jsou nastavené.

* Azure AD [ověřování službou Multi-Factor Authentication](../authentication/multi-factor-authentication.md)
* Azure AD [Cloud Discovery](https://docs.microsoft.com/cloud-app-security/).
* Azure AD [Privileged Identity Management](../privileged-identity-management/pim-configure.md). 


## <a name="testing-security-policies"></a>Testování zásad zabezpečení

Tato část obsahuje kroky pro testování uživatelského rizika a zásady zabezpečení rizik přihlašování.


### <a name="user-risk-security-policy"></a>Zásady zabezpečení rizik uživatelů

Další informace najdete v tématu [jak nakonfigurovat zásady rizik uživatelů](howto-user-risk-policy.md).

![Riziko uživatele](./media/playbook/02.png "Playbook")


**Pokud chcete otestovat zásady zabezpečení rizik uživatelů, proveďte následující kroky**:

1. Přihlaste se do [ https://portal.azure.com ](https://portal.azure.com) pomocí přihlašovacích údajů globálního správce pro vašeho tenanta.
2. Přejděte do **Identity Protection**. 
3. Na **Azure AD Identity Protection** klikněte na **zásady rizik uživatelů**.
4. V **přiřazení** vyberte požadované uživatele (a skupiny) a úroveň rizika uživatele.

    ![Riziko uživatele](./media/playbook/03.png "Playbook")

5. V části ovládací prvky, vyberte požadované řízení přístupu (například vyžaduje změnu hesla).
5. Jako **vynucení zásad**vyberte **vypnout**.
6. Zvýšení úrovně rizika uživatele testovacího účtu, například simulace rizikových událostí jednoho několikrát.
7. Počkejte pár minut a potom ověřte, že uživatelské úrovni pro vaše uživatele je střední. V opačném případě simulace další rizikové události pro uživatele.
8. Jako **vynucení zásad**vyberte **na**.
9. Podmíněný přístup na základě rizik uživatelů teď můžete otestovat přihlášením pomocí uživatele s úrovní rizika se zvýšenými oprávněními.
    
    

### <a name="sign-in-risk-security-policy"></a>Zásady zabezpečení rizik přihlašování

Další informace najdete v tématu [jak nakonfigurovat zásady rizik přihlašování](howto-sign-in-risk-policy.md).

![Riziko přihlášení](./media/playbook/01.png "Playbook")


**K otestování znaménko zásadám rizik, postupujte následovně:**

1. Přihlaste se do [ https://portal.azure.com ](https://portal.azure.com) pomocí přihlašovacích údajů globálního správce pro vašeho tenanta.

2. Přejděte do **Azure AD Identity Protection**.

3. V hlavním **Azure AD Identity Protection** klikněte na **zásady rizik přihlašování**. 

4. V **přiřazení** části, vyberte požadované uživatele (a skupiny) a přihlaste se úroveň rizika.

    ![Riziko přihlášení](./media/playbook/04.png "Playbook")


5. V **ovládací prvky** vyberte požadované řízení přístupu (například **vyžadovat vícefaktorové ověřování**). 

6. Jako **vynucení zásad**vyberte **na**.

7. Klikněte na **Uložit**.

8. Podmíněný přístup podle rizika přihlašování se teď můžete otestovat přihlášením pomocí rizikové relace (například pomocí prohlížeče Tor). 

 




## <a name="see-also"></a>Další informace najdete v tématech

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

