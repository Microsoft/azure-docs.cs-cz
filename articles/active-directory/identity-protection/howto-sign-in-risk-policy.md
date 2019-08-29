---
title: Jak nakonfigurovat zásady pro rizikové přihlašování v Azure Active Directory Identity Protection | Microsoft Docs
description: Přečtěte si, jak nakonfigurovat zásady rizik Azure AD Identity Protection přihlašování.
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
ms.openlocfilehash: d00376c6689b6be773f24e8acd09c3697fb6a799
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126297"
---
# <a name="how-to-configure-the-sign-in-risk-policy"></a>Jak: Konfigurace zásad rizik přihlášení

Azure Active Directory detekuje [typy detekce rizik](../reports-monitoring/concept-risk-events.md#risk-detection-types) v reálném čase i v režimu offline. Každé zjištění rizika, které bylo zjištěno pro přihlášení uživatele, přispívá k logickému konceptu s názvem rizikové přihlášení. Rizikové přihlášení je indikátorem pokusu o přihlášení, který nemusí být proveden oprávněným vlastníkem uživatelského účtu.

## <a name="what-is-the-sign-in-risk-policy"></a>Jaké jsou zásady rizik přihlašování?

Azure AD analyzuje každé přihlášení uživatele. Cílem analýzy je rozpoznat podezřelé akce, které jsou k disjetí společně s přihlášením. Například pokud je přihlášení provedeno pomocí anonymní IP adresy nebo je přihlášení zahájeno z neznámého umístění? V Azure AD se podezřelé akce, které může systém detekovat, označují také jako detekce rizik. V závislosti na detekcích rizik zjištěných během přihlašování Azure AD vypočítá hodnotu. Hodnota představuje pravděpodobnost (nízká, střední, vysoká), že se přihlášení legitimního uživatele neprovádí. Pravděpodobnost se nazývá **úroveň rizika přihlašování**.

Zásada pro rizikové přihlašování je automatizovaná odpověď, kterou můžete nakonfigurovat pro konkrétní úroveň rizika přihlašování. V odpovědi můžete zablokovat přístup k vašim prostředkům nebo vyžadovat přístup ke službě Multi-Factor Authentication (MFA), abyste získali přístup.
   
## <a name="how-do-i-access-the-sign-in-risk-policy"></a>Návody získat přístup k zásadám rizik přihlašování?
   
Zásady rizik přihlašování jsou v části **Konfigurace** na [stránce Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Zásady rizik přihlašování](./media/howto-sign-in-risk-policy/1014.png "Zásady rizik přihlašování")

## <a name="policy-settings"></a>Nastavení zásad

Když konfigurujete zásady pro rizikové přihlašování, musíte nastavit:

- Uživatelé a skupiny, na které se zásady vztahují:

    ![Uživatelé a skupiny](./media/howto-sign-in-risk-policy/11.png)

- Úroveň rizika přihlašování, která spouští zásady:

    ![Úroveň rizika přihlašování](./media/howto-sign-in-risk-policy/12.png)

- Typ přístupu, který chcete vyhovět při splnění úrovně rizika přihlašování:  

    ![Access](./media/howto-sign-in-risk-policy/13.png)

- Stav zásady:

    ![Vyhovět zásadám](./media/howto-sign-in-risk-policy/14.png)

Dialogové okno Konfigurace zásad poskytuje možnost odhadnout dopad rekonfigurace.

![Odhadovaný dopad](./media/howto-sign-in-risk-policy/15.png)

## <a name="what-you-should-know"></a>Co byste měli vědět

Zásady zabezpečení rizik přihlašování můžete nakonfigurovat tak, aby vyžadovaly MFA:

![Vyžadování MFA](./media/howto-sign-in-risk-policy/16.png)

Z bezpečnostních důvodů ale toto nastavení funguje jenom pro uživatele, kteří už byli zaregistrovaní pro MFA. Identity Protection blokuje uživatele s požadavkem MFA, pokud ještě nejsou zaregistrované pro MFA.

Pokud chcete vyžadovat MFA pro rizikové přihlášení, měli byste:

1. Povolte [zásady registrace služby Multi-Factor Authentication](howto-mfa-policy.md) pro ovlivněné uživatele.
2. Vyžaduje, aby se zasaženým uživatelům přihlásili k nerizikové relaci a prováděli registraci MFA.

Provedením těchto kroků zajistíte, aby se pro rizikové přihlášení vyžadovalo vícefaktorové ověřování.

Zásady rizik přihlašování jsou tyto:

- Používá se u všech přenosů a přihlášení v prohlížeči pomocí moderního ověřování.
- Neaplikuje se na aplikace, které používají starší protokoly zabezpečení, zakázáním koncového bodu WS-Trust v federovaném IDP, například ADFS.

Přehled souvisejícího uživatelského prostředí najdete v těchto tématech:

* [Obnovení rizik při přihlášení](flows.md#risky-sign-in-recovery)
* [Zakázané rizikové přihlášení](flows.md#risky-sign-in-blocked)  
* [Prostředí pro přihlašování pomocí Azure AD Identity Protection](flows.md)  

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

Přehled Azure AD Identity Protection najdete v tématu [Azure AD Identity Protection Overview](overview.md).
