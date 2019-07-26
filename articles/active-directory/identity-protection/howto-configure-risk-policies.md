---
title: Postup konfigurace zásad rizik v Azure Active Directory Identity Protection (Aktualizováno) | Microsoft Docs
description: Postup konfigurace zásad rizik v Azure Active Directory Identity Protection (Aktualizováno).
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ce4e2958978de9339f4340755e3740730025a5f
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334024"
---
# <a name="how-to-configure-risk-policies-in-azure-active-directory-identity-protection-refreshed"></a>Jak: Konfigurace zásad rizik v Azure Active Directory Identity Protection (Aktualizováno)

Azure AD detekuje rizikové události, které jsou indikátory pro potenciálně ohrožené identity. Konfigurací zásad rizik můžete definovat automatizované odpovědi na výsledky detekce:

- Pomocí zásad rizik přihlašování můžete nakonfigurovat odpověď na rizikové události v reálném čase, které byly zjištěny během přihlašování uživatele. 
- Pomocí zásad rizika pro uživatele můžete nakonfigurovat odpověď na všechna aktivní rizika uživatelů zjištěná uživatelem v průběhu času.  

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="what-is-the-sign-in-risk-policy"></a>Jaké jsou zásady rizik přihlašování?

Azure AD analyzuje každé přihlášení uživatele. Cílem analýzy je rozpoznat podezřelé akce, které jsou k disjetí společně s přihlášením. Například pokud je přihlášení provedeno pomocí anonymní IP adresy nebo je přihlášení zahájeno z neznámého umístění? V Azure AD se podezřelé akce, které může systém detekovat, označují také jako rizikové události. Na základě rizikových událostí, které byly zjištěny během přihlašování, služba Azure AD vypočítá hodnotu. Hodnota představuje pravděpodobnost (nízká, střední, vysoká), že se přihlášení legitimního uživatele neprovádí. Pravděpodobnost se nazývá **úroveň rizika přihlašování**.

Zásada pro rizikové přihlašování je automatizovaná odpověď, kterou můžete nakonfigurovat pro konkrétní úroveň rizika přihlašování. V odpovědi můžete zablokovat přístup k vašim prostředkům nebo vyžadovat přístup ke službě Multi-Factor Authentication (MFA), abyste získali přístup.

Když uživatel úspěšně dokončí výzvu MFA aktivované zásadou pro přihlašování, poskytne nám zpětnou vazbu k identitě, kterou přihlásilo od legitimního uživatele. Proto se riziková událost přihlašování, která aktivovala výzvu MFA, automaticky uzavře a ochrana identity zabrání v přispívání na zvýšení rizika uživatele. Povolení zásad rizik přihlašování může snížit noisiness v zobrazení rizikových přihlášení tím, že uživatelům umožníte, aby při zobrazení výzvy pro MFA a následně automaticky uzavřeli související rizikové přihlášení.

## <a name="how-do-i-access-the-sign-in-risk-policy"></a>Návody získat přístup k zásadám rizik přihlašování?
   
Zásady rizik přihlašování jsou v části **Konfigurace** na [stránce Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Zásady rizik přihlašování](./media/howto-configure-risk-policies/1014.png "Zásady rizik přihlašování")

## <a name="sign-in-risk-policy-settings"></a>Nastavení zásad rizik přihlašování

Když konfigurujete zásady pro rizikové přihlašování, musíte nastavit:

- Uživatelé a skupiny, na které se zásady vztahují:

   ![Uživatelé a skupiny](./media/howto-configure-risk-policies/11.png)

- Úroveň rizika přihlašování, která spouští zásady:

   ![Úroveň rizika přihlášení](./media/howto-configure-risk-policies/12.png)

- Typ přístupu, který chcete vyhovět při splnění úrovně rizika přihlašování:  

   ![Access](./media/howto-configure-risk-policies/13.png)

- Stav zásady:

   ![Vyhovět zásadám](./media/howto-configure-risk-policies/14.png)

Dialogové okno Konfigurace zásad poskytuje možnost odhadnout dopad rekonfigurace.

![Odhadovaný dopad](./media/howto-configure-risk-policies/15.png)

## <a name="what-you-should-know-about-sign-in-risk-policies"></a>Co byste měli znát o zásadách rizik přihlašování

Zásady zabezpečení rizik přihlašování můžete nakonfigurovat tak, aby vyžadovaly MFA:

![Vyžadování MFA](./media/howto-configure-risk-policies/16.png)

Z bezpečnostních důvodů ale toto nastavení funguje jenom pro uživatele, kteří už byli zaregistrovaní pro MFA. Identity Protection blokuje uživatele s požadavkem MFA, pokud ještě nejsou zaregistrované pro MFA.

Pokud chcete vyžadovat MFA pro rizikové přihlášení, měli byste:

1. Povolte zásady registrace služby Multi-Factor Authentication pro ovlivněné uživatele.
2. Vyžaduje, aby se zasažení uživatelé přihlásili v nerizikové relaci a prováděli registraci MFA.

Provedením těchto kroků zajistíte, aby se pro rizikové přihlášení vyžadovalo vícefaktorové ověřování.

Zásady rizik přihlašování jsou tyto:

- Používá se u všech přenosů a přihlášení v prohlížeči pomocí moderního ověřování.
- Neaplikuje se na aplikace, které používají starší protokoly zabezpečení, zakázáním koncového bodu WS-Trust v federovaném IDP, například ADFS.

Přehled souvisejícího uživatelského prostředí najdete v těchto tématech:

* [Obnovení rizik při přihlášení](flows.md#risky-sign-in-recovery)
* [Zakázané rizikové přihlášení](flows.md#risky-sign-in-blocked)  
* [Prostředí pro přihlašování pomocí Azure AD Identity Protection](flows.md)  

## <a name="what-is-a-user-risk-policy"></a>Co je to zásady rizik uživatelů?

Azure AD analyzuje každé přihlášení uživatele. Cílem analýzy je rozpoznat podezřelé akce, které jsou k disjetí společně s přihlášením. V Azure AD se podezřelé akce, které může systém detekovat, označují také jako rizikové události. I když lze v reálném čase zjistit některé rizikové události, existují také rizikové události vyžadující více času. Například pro zjištění nemožného cestování do neobvyklých umístění systém vyžaduje počáteční období učení 14 dní, kde se dozvíte o běžném chování uživatele. Existuje několik možností, jak vyřešit zjištěné rizikové události. Jednotlivé rizikové události můžete například vyřešit ručně, nebo je můžete vyřešit pomocí rizika přihlašování nebo zásad podmíněného přístupu uživatele.

Všechny rizikové události, které byly zjištěny pro uživatele a nebyly vyřešeny, se označují jako aktivní rizikové události. Aktivní rizikové události, které jsou přidruženy k uživateli, se označují jako riziko pro uživatele. V závislosti na riziku uživatele Azure AD vypočítá pravděpodobnost (nízká, střední, vysoká), že došlo k ohrožení uživatele. Pravděpodobnost se nazývá úroveň rizika uživatele.

![Rizika uživatele](./media/howto-configure-risk-policies/11031.png)

Zásady rizik uživatelů je automatizovaná odpověď, kterou můžete nakonfigurovat pro konkrétní úroveň rizika uživatele. Pomocí zásad rizika pro uživatele můžete zablokovat přístup k vašim prostředkům nebo vyžadovat změnu hesla, abyste získali uživatelský účet zpátky do čistého stavu.

## <a name="how-do-i-access-the-user-risk-policy"></a>Návody získat přístup k zásadám rizika pro uživatele?
   
Zásady rizik uživatelů jsou v části **Konfigurace** na [stránce Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Zásady rizik uživatelů](./media/howto-configure-risk-policies/11014.png)

## <a name="user-risk-policy-settings"></a>Nastavení zásad rizik uživatelů

Když konfigurujete zásady rizik uživatelů, budete muset nastavit:

- Uživatelé a skupiny, na které se zásady vztahují:

   ![Uživatelé a skupiny](./media/howto-configure-risk-policies/111.png)

- Úroveň rizika přihlašování, která spouští zásady:

   ![Úroveň rizika uživatele](./media/howto-configure-risk-policies/112.png)

- Typ přístupu, který chcete vyhovět při splnění úrovně rizika přihlašování:  

   ![Access](./media/howto-configure-risk-policies/113.png)

- Stav zásady:

   ![Vyhovět zásadám](./media/howto-configure-risk-policies/114.png)

Dialogové okno Konfigurace zásad poskytuje možnost odhadnout dopad vaší konfigurace.

![Odhadovaný dopad](./media/howto-configure-risk-policies/115.png)

## <a name="what-you-should-know-about-user-risk-polices"></a>Co byste měli znát pro zásady rizik uživatelů

Můžete nastavit zásady zabezpečení rizik uživatelů pro blokování uživatelů při přihlášení v závislosti na úrovni rizika.

![Blokování](./media/howto-configure-risk-policies/116.png)

Blokování přihlášení:

* Brání generování nových rizikových událostí uživatele pro ovlivněného uživatele.
* Umožňuje správcům ručně opravit rizikové události, které mají vliv na identitu uživatele a obnoví je na zabezpečený stav.

## <a name="best-practices"></a>Osvědčené postupy

Výběr **vysoké** prahové hodnoty snižuje počet aktivovaných zásad a minimalizuje dopad na uživatele.  

Ale nezahrnuje **malá** a **střední** přihlášení označená příznakem pro riziko ze zásad, což nemusí útočníkovi zabránit v zneužití ohrožené identity.

Při nastavování zásad

- Vyloučit uživatele, kteří nemají přístup k Multi-Factor Authentication
- Vylučte uživatele v místních prostředích, kde povolení zásad není praktické (například bez přístupu k helpdesku).
- Vyloučení uživatelů, kteří mají nejspíš generovat mnoho falešných (vývojářů, analytiků zabezpečení)
- Při počátečním zavedení zásad použijte **vysokou** prahovou hodnotu nebo pokud potřebujete minimalizovat výzvy, které vidí koncoví uživatelé.
- Pokud vaše organizace vyžaduje vyšší úroveň zabezpečení, použijte **nízkou** prahovou hodnotu. Výběr **nízké** prahové hodnoty zavádí další výzvy k přihlašování uživatelů, ale zvýšené zabezpečení.

Doporučená výchozí hodnota pro většinu organizací je nakonfigurovat pravidlo pro **střední** prahovou hodnotu, aby se vyrovnalo rovnováhu mezi použitelností a zabezpečením.

## <a name="next-steps"></a>Další postup

 [Kanál 9: Azure AD a zobrazení identity: Identity Protection ve verzi Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
