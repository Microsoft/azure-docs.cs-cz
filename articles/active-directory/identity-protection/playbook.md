---
title: Azure Active Directory Identity Protection PlayBook | Microsoft Docs
description: Přečtěte si, jak Azure AD Identity Protection umožňuje omezit schopnost útočníka zneužít ohroženou identitu nebo zařízení a zabezpečit identitu nebo zařízení, které bylo dříve podezřelé nebo které bylo známo, že by mohlo dojít k ohrožení zabezpečení.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 273a6aca2050676650b955ec078b47b2ffcfe319
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333923"
---
# <a name="azure-active-directory-identity-protection-playbook"></a>Azure Active Directory Identity Protection PlayBook

Tato PlayBook vám pomůže:

* Naplnit data v prostředí Identity Protection simulací rizikových událostí a ohrožení zabezpečení
* Nastavte zásady podmíněného přístupu na základě rizika a otestujte dopad těchto zásad.

## <a name="simulating-risk-events"></a>Simulace rizikových událostí

V této části najdete postup pro simulaci následujících typů rizikových událostí:

* Přihlášení z anonymních IP adres (snadné)
* Přihlášení z neznámých umístění (střední)
* Nemožná cesta do netypických míst (obtížné)

Jiné rizikové události nelze simulovat zabezpečeným způsobem.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Přihlášení z anonymních IP adres

Další informace o této rizikové události najdete v tématu [přihlášení z anonymních IP adres](../reports-monitoring/concept-risk-events.md#sign-ins-from-anonymous-ip-addresses). 

Dokončení následujícího postupu vyžaduje, abyste použili:

- [Prohlížeč mandátu](https://www.torproject.org/projects/torbrowser.html.en) pro simulaci anonymních IP adres. Je možné, že budete muset virtuální počítač použít, pokud vaše organizace omezuje používání prohlížeče pro prostředí.
- Testovací účet, který ještě není zaregistrovaný pro službu Multi-Factor Authentication.

**Chcete-li simulovat přihlášení z anonymní IP adresy, proveďte následující kroky**:

1. V [prohlížeči](https://www.torproject.org/projects/torbrowser.html.en)systému pro práci přejděte na [https://myapps.microsoft.com](https://myapps.microsoft.com)adresu.   
2. Zadejte přihlašovací údaje účtu, který se má zobrazit v sestavě **přihlášení z anonymních IP adres** .

Přihlašování se zobrazuje na řídicím panelu Identity Protection během 10-15 minut. 

### <a name="sign-ins-from-unfamiliar-locations"></a>Přihlášení z neznámých míst

Další informace o této rizikové události najdete v tématu [přihlášení z neznámých umístění](../reports-monitoring/concept-risk-events.md#sign-in-from-unfamiliar-locations). 

Abyste mohli simulovat neznámou polohu, musíte se přihlásit z umístění a zařízení, ke kterému váš zkušební účet ještě nebyl přihlášený.

Následující postup používá nově vytvořenou:

- Připojení k síti VPN pro simulaci nového umístění.
- Virtuální počítač, abyste mohli simulovat nové zařízení.

Provedení následujícího postupu vyžaduje, abyste použili uživatelský účet, který má:

- Aspoň 30 dní na historii přihlášení.
- Ověřování Multi-Factor Authentication je povolené.

**Chcete-li simulovat přihlášení z neznámého umístění, proveďte následující kroky**:

1. Když se přihlašujete pomocí testovacího účtu, selže dotaz MFA, protože neprojde výzvou MFA.
2. Pomocí nové sítě VPN přejděte na [https://myapps.microsoft.com](https://myapps.microsoft.com) adresu a zadejte přihlašovací údaje svého testovacího účtu.

Přihlašování se zobrazuje na řídicím panelu Identity Protection během 10-15 minut.

### <a name="impossible-travel-to-atypical-location"></a>Nemožná cesta do neobvyklých míst

Další informace o této rizikové události najdete v tématu [nemožná cesta do neobvyklých umístění](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations). 

Simulace nemožného cestovního stavu je obtížné, protože algoritmus využívá Machine Learning k pleveli falešně pozitivních hodnot, jako je nemožná cesta ze známých zařízení, nebo přihlášení z VPN, které používají jiní uživatelé v adresáři. Kromě toho algoritmus vyžaduje před tím, než začne generovat rizikové události, historii přihlášení 14 dní a 10 přihlášení uživatele. Z důvodu komplexních modelů strojového učení a výše uvedených pravidel existuje možnost, že následující kroky nevedou k rizikové události. Je možné, že budete chtít tyto kroky replikovat pro několik účtů Azure AD, aby se tato riziková událost publikovala.

**Chcete-li simulovat nemožná cestu k neobvyklým umístěním, proveďte následující kroky**:

1. V prohlížeči použijte standardní prohlížeč a přejděte [https://myapps.microsoft.com](https://myapps.microsoft.com)na.  
2. Zadejte přihlašovací údaje účtu, pro který chcete vygenerovat nemožnou událost pro služební riziko.
3. Změňte svého uživatelského agenta. Můžete změnit uživatelského agenta v aplikaci Internet Explorer z Vývojářské nástroje nebo změnit uživatelského agenta v prohlížeči Firefox nebo Chrome pomocí doplňku User-Agent přepínač.
4. Změňte IP adresu. IP adresu můžete změnit pomocí sítě VPN, samoobslužného doplňku nebo nového počítače v Azure v jiném datovém centru.
5. Přihlaste se [https://myapps.microsoft.com](https://myapps.microsoft.com) pomocí stejných přihlašovacích údajů jako před a během několika minut po předchozím přihlášení.

Přihlašování se zobrazuje na řídicím panelu Identity Protection během 2-4 hodin.

## <a name="simulating-vulnerabilities"></a>Simulace chyb zabezpečení
Ohrožení zabezpečení představují slabé stránky v prostředí Azure AD, které může zneužít špatný objekt actor. V současné době se 3 typy ohrožení zabezpečení nacházejí v Azure AD Identity Protection, které využívají jiné funkce služby Azure AD. Po nastavení těchto funkcí se tyto chyby zabezpečení zobrazí na řídicím panelu Identity Protection automaticky.

* [Multi-Factor Authentication pro](../authentication/multi-factor-authentication.md) Azure AD
* [Cloud Discovery](https://docs.microsoft.com/cloud-app-security/)Azure AD.
* [Privileged Identity Management](../privileged-identity-management/pim-configure.md)Azure AD. 

## <a name="testing-security-policies"></a>Testování zásad zabezpečení

V této části najdete postup testování rizika uživatele a zásady zabezpečení rizik přihlašování.

### <a name="user-risk-security-policy"></a>Zásady zabezpečení rizik uživatelů

Další informace najdete v článku, který se zabývá [způsobem konfigurace zásad rizik uživatelů](howto-user-risk-policy.md).

![Riziko uživatele](./media/playbook/02.png "PlayBook")

**Chcete-li otestovat zásady zabezpečení rizik uživatelů, proveďte následující kroky**:

1. Přihlaste [https://portal.azure.com](https://portal.azure.com) se pomocí přihlašovacích údajů globálního správce pro vašeho tenanta.
2. Přejděte na **Identity Protection**. 
3. Na stránce **Azure AD Identity Protection** klikněte na **zásady rizik uživatelů**.
4. V části **přiřazení** vyberte požadované uživatele (a skupiny) a úroveň rizika uživatele.

    ![Riziko uživatele](./media/playbook/03.png "PlayBook")

5. V části ovládací prvky vyberte požadovaný ovládací prvek přístupu (například vyžadovat změnu hesla).
5. Jako **Vynutilit zásady**vyberte **vypnuto**.
6. Zvyšte riziko pro uživatele testovacího účtu, a to například simulací jedné z rizikových událostí.
7. Počkejte pár minut a pak ověřte, že je úroveň uživatele pro uživatele střední. Pokud ne, simulovat pro uživatele více rizikových událostí.
8. Jako **Vynutilit zásady**vyberte **zapnuto**.
9. Můžete teď otestovat podmíněný přístup na základě rizik uživatelů přihlášením pomocí uživatele se zvýšenou úrovní rizika.

### <a name="sign-in-risk-security-policy"></a>Zásady zabezpečení rizik přihlašování

Další informace najdete v článku, který se zabývá [způsobem konfigurace zásad rizik přihlášení](howto-sign-in-risk-policy.md).

![Riziko přihlášení](./media/playbook/01.png "PlayBook")

**Chcete-li otestovat rizikové zásady přihlašování, proveďte následující kroky:**

1. Přihlaste [https://portal.azure.com](https://portal.azure.com) se pomocí přihlašovacích údajů globálního správce pro vašeho tenanta.
2. Přejděte na **Azure AD Identity Protection**.
3. Na hlavní **Azure AD Identity Protection** stránce klikněte na **zásady rizik přihlašování**. 
4. V části **přiřazení** vyberte požadované uživatele (a skupiny) a úroveň rizika přihlašování.

    ![Riziko přihlášení](./media/playbook/04.png "PlayBook")

5. V části **ovládací prvky** vyberte požadovaný ovládací prvek přístupu (například **vyžadovat službu Multi-Factor Authentication**). 
6. Jako **Vynutilit zásady**vyberte **zapnuto**.
7. Klikněte na **Uložit**.
8. Nyní můžete otestovat podmíněný přístup na základě rizik přihlašování pomocí rizikové relace (například pomocí prohlížeče služby 

## <a name="see-also"></a>Viz také:

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
