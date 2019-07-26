---
title: Jak nakonfigurovat zásady rizik uživatelů v Azure Active Directory Identity Protection | Microsoft Docs
description: Přečtěte si, jak nakonfigurovat Azure AD Identity Protection zásady rizik uživatelů.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc7ea05497d69a7ca833cc783e7a2bc6bf1a8b07
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335440"
---
# <a name="how-to-configure-the-user-risk-policy"></a>Jak: Konfigurace zásad rizik uživatelů

V případě rizika uživatele služba Azure AD zjistí pravděpodobnost, že došlo k ohrožení zabezpečení uživatelského účtu. Jako správce můžete nakonfigurovat zásadu podmíněného přístupu pro uživatele, která bude automaticky reagovat na konkrétní úroveň rizika uživatele.
 
Tento článek poskytuje informace, které potřebujete ke konfiguraci zásad rizik uživatelů.

## <a name="what-is-a-user-risk-policy"></a>Co je to zásady rizik uživatelů?

Azure AD analyzuje každé přihlášení uživatele. Cílem analýzy je rozpoznat podezřelé akce, které jsou k disjetí společně s přihlášením. V Azure AD se podezřelé akce, které může systém detekovat, označují také jako rizikové události. I když lze v reálném čase zjistit některé rizikové události, existují také rizikové události vyžadující více času. Například pro zjištění nemožného cestování do neobvyklých umístění systém vyžaduje počáteční období učení 14 dní, kde se dozvíte o běžném chování uživatele. Existuje několik možností, jak vyřešit zjištěné rizikové události. Jednotlivé rizikové události můžete například vyřešit ručně, nebo je můžete vyřešit pomocí rizika přihlašování nebo zásad podmíněného přístupu uživatele.

Všechny rizikové události, které byly zjištěny pro uživatele a nebyly vyřešeny, se označují jako aktivní rizikové události. Aktivní rizikové události, které jsou přidruženy k uživateli, se označují jako riziko pro uživatele. V závislosti na riziku uživatele Azure AD vypočítá pravděpodobnost (nízká, střední, vysoká), že došlo k ohrožení uživatele. Pravděpodobnost se nazývá úroveň rizika uživatele.

![Rizika uživatele](./media/howto-user-risk-policy/1031.png)

Zásady rizik uživatelů je automatizovaná odpověď, kterou můžete nakonfigurovat pro konkrétní úroveň rizika uživatele. Pomocí zásad rizika pro uživatele můžete zablokovat přístup k vašim prostředkům nebo vyžadovat změnu hesla, abyste získali uživatelský účet zpátky do čistého stavu.

## <a name="how-do-i-access-the-user-risk-policy"></a>Návody získat přístup k zásadám rizika pro uživatele?
   
Zásady rizik přihlašování jsou v části **Konfigurace** na [stránce Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Zásady rizik uživatelů](./media/howto-user-risk-policy/1014.png)

## <a name="policy-settings"></a>Nastavení zásad

Když konfigurujete zásady pro rizikové přihlašování, musíte nastavit:

- Uživatelé a skupiny, na které se zásady vztahují:

    ![Uživatelé a skupiny](./media/howto-user-risk-policy/11.png)

- Úroveň rizika přihlašování, která spouští zásady:

    ![Úroveň rizika uživatele](./media/howto-user-risk-policy/12.png)

- Typ přístupu, který chcete vyhovět při splnění úrovně rizika přihlašování:  

    ![Access](./media/howto-user-risk-policy/13.png)

- Stav zásady:

    ![Vyhovět zásadám](./media/howto-user-risk-policy/14.png)

Dialogové okno Konfigurace zásad poskytuje možnost odhadnout dopad vaší konfigurace.

![Odhadovaný dopad](./media/howto-user-risk-policy/15.png)

## <a name="what-you-should-know"></a>Co byste měli vědět

Můžete nastavit zásady zabezpečení rizik uživatelů pro blokování uživatelů při přihlášení v závislosti na úrovni rizika.

![Blokování](./media/howto-user-risk-policy/16.png)

Blokování přihlášení:

* Brání generování nových rizikových událostí uživatele pro ovlivněného uživatele.
* Umožňuje správcům ručně opravit rizikové události, které mají vliv na identitu uživatele a obnoví je na zabezpečený stav.

## <a name="best-practices"></a>Osvědčené postupy

Výběr **vysoké** prahové hodnoty snižuje počet aktivovaných zásad a minimalizuje dopad na uživatele.
Nezahrnuje ale **malé** a **středně velké** uživatele označené příznakem rizika ze zásad, což nemusí zabezpečit identity nebo zařízení, která byla dříve podezřelá nebo známá k ohrožení.

Při nastavování zásad

* Vyloučení uživatelů, kteří mají nejspíš vygenerovat spoustu falešně pozitivních hodnot (vývojářům, analytikům zabezpečení)
* Vylučte uživatele v místních prostředích, kde povolení zásad není praktické (například bez přístupu k helpdesku).
* Při počátečním zavedení zásad použijte **vysokou** prahovou hodnotu nebo pokud potřebujete minimalizovat výzvy, které vidí koncoví uživatelé.
* Pokud vaše organizace vyžaduje vyšší úroveň zabezpečení, použijte **nízkou** prahovou hodnotu. Výběr **nízké** prahové hodnoty zavádí další výzvy k přihlašování uživatelů, ale zvýšené zabezpečení.

Doporučená výchozí hodnota pro většinu organizací je nakonfigurovat pravidlo pro **střední** prahovou hodnotu, aby se vyrovnalo rovnováhu mezi použitelností a zabezpečením.

Přehled souvisejícího uživatelského prostředí najdete v těchto tématech:

* [Narušený tok obnovení účtu](flows.md#compromised-account-recovery).  
* [Napadený tok blokovaných účtů](flows.md#compromised-account-blocked).  

**Otevření dialogového okna související konfigurace**:

- V okně **Azure AD Identity Protection** klikněte v části **Konfigurovat** na **zásady rizik uživatelů**.

    ![Zásady rizik uživatelů](./media/howto-user-risk-policy/1009.png "Zásady rizik uživatelů")

## <a name="next-steps"></a>Další postup

Přehled Azure AD Identity Protection najdete v tématu [Azure AD Identity Protection Overview](overview.md).
