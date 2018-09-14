---
title: Jak nakonfigurovat zásady rizik přihlašování v Azure Active Directory Identity Protection | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat zásady rizik přihlašování Azure AD Identity Protection.
services: active-directory
keywords: Azure active directory identity protection, zjišťování cloudových aplikací, Správa aplikací, zabezpečení, rizika, úroveň rizika, ohrožení zabezpečení, zásady zabezpečení
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.component: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: 7350cbd3e8aed6098f24d0edaa5807d241890287
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45581472"
---
# <a name="how-to-configure-the-sign-in-risk-policy"></a>Postupy: Konfigurace zásad rizika přihlašování

Azure Active Directory detekuje [typech rizikových událostí](../reports-monitoring/concept-risk-events.md#risk-event-types) v reálném čase a v režimu offline. Každý rizikovou událost, která byla zjištěna přihlásit uživatele přispívá k logický pojem volána rizikových přihlášení. Riziková přihlášení je indikátorem pokusu přihlásit, který se nemusí provést legitimním vlastníkem uživatelského účtu.


## <a name="sign-in-risk-level"></a>Úroveň rizika přihlášení

Úroveň rizika přihlášení je označení (vysoká, střední nebo nízká) pravděpodobnost, že pokus o přihlášení nebyl prováděné legitimním vlastníkem uživatelského účtu.

## <a name="mitigating-sign-in-risk-events"></a>Zmírnění rizika přihlašování události

Omezení rizik je akci, kterou chcete omezit schopnost útočníka zneužít ohrožení zabezpečení identity nebo zařízení bez obnovení identity nebo zařízení do bezpečného stavu. Omezení rizik nevyřeší předchozí přihlášení rizikové události spojené s identity nebo zařízení.

Zmírnění rizikových přihlášení automaticky, můžete nakonfigurovat zásady zabezpečení rizik přihlašování. Pomocí těchto zásad, zvažte úroveň rizika uživatele nebo přihlášení k blokování rizikových přihlášení nebo vyžadovat, aby uživatel k provedení ověřování Multi-Factor Authentication. Tato akce může zabránit útočníkům ve využívajícím krádež identity způsobit poškození a může dát nějaký čas zabezpečit identitu.

## <a name="sign-in-risk-security-policy"></a>Zásady zabezpečení rizik přihlašování
Zásady rizik přihlašování se zásady podmíněného přístupu, která vyhodnotí riziko pro konkrétní přihlášení a použije omezení rizik na základě předem definované podmínky a pravidla.

![Zásady rizik přihlašování](./media/howto-sign-in-risk-policy/1014.png "zásady rizik přihlašování")

Služba Azure AD Identity Protection umožňuje správu zmírnění rizikových přihlášení tím, že vám umožňuje:

* Nastavte uživatele a skupiny, které zásady platí pro:

    ![Zásady rizik přihlašování](./media/howto-sign-in-risk-policy/1015.png "zásady rizik přihlašování")
* Nastavte rizika přihlašování úrovně prahovou hodnotu (nízká, střední nebo vysoké), která aktivuje zásady:

    ![Zásady rizik přihlašování](./media/howto-sign-in-risk-policy/1016.png "zásady rizik přihlašování")
* Můžete nastavte řízení vynucení při aktivaci zásady:  

    ![Zásady rizik přihlašování](./media/howto-sign-in-risk-policy/1017.png "zásady rizik přihlašování")
* Přepnutí stavu zásad:

    ![Registrace MFA](./media/howto-sign-in-risk-policy/403.png "registrace MFA")
* Kontrola a vyhodnocení dopadu změn před aktivací ho:

    ![Zásady rizik přihlašování](./media/howto-sign-in-risk-policy/1018.png "zásady rizik přihlašování")

## <a name="what-you-need-to-know"></a>Co je potřeba vědět
Můžete nakonfigurovat zásady zabezpečení rizik přihlašování vyžadovat vícefaktorové ověřování:

![Zásady rizik přihlašování](./media/howto-sign-in-risk-policy/1017.png "zásady rizik přihlašování")

Ale z bezpečnostních důvodů se toto nastavení funguje jenom pro uživatele, kteří jsou již zaregistrovány ověřování službou Multi-Factor Authentication. Tento uživatel je zablokovaný, pokud je splněna podmínka tak, aby vyžadovala vícefaktorové ověřování pro uživatele, který ještě není zaregistrovaný k vícefaktorovému ověřování.

Jako osvědčený postup Pokud chcete, aby ověřování službou Multi-Factor Authentication pro rizikových přihlášení, měli byste:

1. Povolit [zásady registrace pro vícefaktorové ověřování](#multi-factor-authentication-registration-policy) pro ovlivnění uživatelé.
2. Vyžadovat ovlivněných uživatelů na přihlášení v relaci rizikové k provedení registrace MFA

Dokončení těchto kroků se zajistí, že ověřování službou Multi-Factor Authentication je vyžadováno pro rizikové přihlášení.

## <a name="best-practices"></a>Osvědčené postupy
Výběr **vysokou** prahová hodnota snižuje počet, kolikrát zásada se aktivuje a minimalizuje dopad na uživatele.  

Ale nezahrnuje **nízká** a **střední** přihlášení označených jako rizikoví ze zásad, které nemusí blokovat útočník ze zneužití ohrožení zabezpečení identity.

Při nastavování zásad,

* Vyloučit uživatele, kteří nejsou / nemůže mít ověřování službou Multi-Factor Authentication
* Vyloučit uživatele v národních prostředí, kde není praktické povolení zásad (například žádný přístup k technické podpory)
* Vyloučit uživatele, kteří mohou generovat velké množství pozitivní hodnotu false (vývojáři, analytikům zabezpečení)
* Použití **vysokou** prahové hodnoty během počáteční zásad uvádění, nebo pokud musíte minimalizovat problémy, koncový uživatel neuvidí.
* Použití **nízká** prahovou hodnotu, pokud vaše organizace vyžaduje vyšší úroveň zabezpečení. Výběr **nízká** prahová hodnota představuje další uživatele přihlásit výzvy, ale zvýšené zabezpečení.

Doporučené výchozí nastavení pro většinu organizací je a nakonfigurujte pravidlo pro **střední** prahová hodnota hledají rovnováhu mezi použitelnost a zabezpečení.

Zásady rizik přihlašování je:

* Použít pro všechny prohlížeče provoz a přihlášení pomocí moderního ověřování.
* Bez použití aplikací s použitím starší protokoly zabezpečení tím, že zakážete koncového bodu WS-Trust ve federovaných zprostředkovatele identity, jako jsou služby AD FS.

**Rizikových událostí** stránky v konzole Identity Protection obsahuje seznam všech událostí:

* Tato zásada byla použita k
* Můžete zkontrolovat aktivity a zjistit, jestli byla akce odpovídající

Přehled související uživatelské prostředí naleznete v tématu:

* [Obnovení riziková přihlášení](flows.md#risky-sign-in-recovery)
* [Riziková přihlášení blokováno](flows.md#risky-sign-in-blocked)  
* [Možnosti přihlašování s Azure AD Identity Protection](flows.md)  

**Chcete-li otevřít dialogové okno Konfigurace související**:

- Na **Azure AD Identity Protection** okno v **konfigurovat** klikněte na tlačítko **zásady rizik přihlašování**.

    ![Zásady rizik uživatelů](./media/howto-sign-in-risk-policy/1014.png "zásady rizik uživatelů")




## <a name="next-steps"></a>Další postup

Získejte přehled o Azure AD Identity Protection, najdete v článku [Přehled služby Azure AD Identity Protection](overview.md).
