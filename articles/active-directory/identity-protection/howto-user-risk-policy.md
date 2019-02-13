---
title: Jak nakonfigurovat zásady rizik uživatelů v Azure Active Directory Identity Protection | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat zásady rizik uživatelů Azure AD Identity Protection.
services: active-directory
keywords: Azure active directory identity protection, zjišťování cloudových aplikací, Správa aplikací, zabezpečení, rizika, úroveň rizika, ohrožení zabezpečení, zásady zabezpečení
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: markvi
ms.reviewer: raluthra
ms.collection: M365-identity-device-management
ms.openlocfilehash: 409c86556d8132cb913508518be7d5b9f9d35321
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56180194"
---
# <a name="how-to-configure-the-user-risk-policy"></a>Jak: Konfigurace zásad rizik uživatelů

S riziko uživatele Azure AD detekuje pravděpodobnost, že není uživatelský účet v bezpečí. Jako správce můžete nakonfigurovat zásady podmíněného přístupu rizik uživatelů, automaticky reagovat na úroveň rizika konkrétního uživatele.
 
Tento článek obsahuje informace, na kterých je nutné nakonfigurovat zásady rizik uživatelů.


## <a name="what-is-a-user-risk-policy"></a>Co je zásady rizik uživatelů?

Azure AD analyzuje každé přihlášení uživatele. Cílem analýzy je ke zjištění podezřelé akce, které společně přihlášení. Ve službě Azure AD jsou podezřelé akce, které systém může zjistit, označované také jako rizikové události. Zatímco některé rizikové události lze zjistit v reálném čase, jsou také rizikové události, které vyžadují delší dobu. Systém vyžaduje ke zjištění nemožná cesta do netypických míst, období učení 14 dnů se dozvíte o běžné chování uživatele. Existuje několik možností, jak vyřešit zjištěné rizikové události. Například můžete ručně vyřešit jednotlivé rizikových událostí nebo můžete získat jejich duplicita se vyřešila pomocí rizika přihlášení nebo zásady rizik podmíněného přístupu uživatelů.

Všechny rizikové události, které byly zjištěny pro uživatele a nepovedlo se vyřešila jsou označovány jako aktivní rizikové události. Aktivní rizikové události, které jsou spojeny s uživatelem, jsou označovány jako uživatelského rizika. Azure AD podle rizika uživatele, vypočítá pravděpodobnost (nízká, střední, vysoká) ohrožené uživatele. Pravděpodobnost se nazývá úroveň rizika uživatele.

![Rizika uživatele](./media/howto-user-risk-policy/1031.png)

Zásady rizik uživatelů je automatické reakce, které můžete konfigurovat pro úroveň rizika konkrétního uživatele. Pomocí zásady rizik uživatelů můžete zablokovat přístup k vašim prostředkům nebo vyžadovat změnu hesla pro uživatelský účet návrat do čistého stavu.


## <a name="how-do-i-access-the-user-risk-policy"></a>Jak získám přístup do zásady rizik uživatelů?
   
Zásady rizik přihlašování **konfigurovat** části na [stránku služby Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Zásady rizik uživatelů](./media/howto-user-risk-policy/1014.png)



## <a name="policy-settings"></a>Nastavení zásad

Když konfigurujete zásady rizik přihlašování, budete muset nastavit:

- Uživatelé a skupiny, které zásady platí pro:

    ![Uživatelé a skupiny](./media/howto-user-risk-policy/11.png)

- Úroveň rizika přihlášení, která aktivuje zásady:

    ![Úroveň rizika uživatele](./media/howto-user-risk-policy/12.png)

- Typ přístupu, kterou chcete vynutit, pokud byly splněny úroveň rizika přihlášení:  

    ![Access](./media/howto-user-risk-policy/13.png)

- Stav zásad:

    ![Vynucení zásad](./media/howto-user-risk-policy/14.png)

Dialogové okno Konfigurace zásad vám poskytne možnost odhad dopadu vaší konfigurace.

![Odhadovaný dopad](./media/howto-user-risk-policy/15.png)

## <a name="what-you-should-know"></a>Co byste měli vědět

Můžete nastavit zásady zabezpečení rizik uživatelů k blokování uživatelů při přihlášení v závislosti na úrovni rizika.

![Blokování](./media/howto-user-risk-policy/16.png)


Blokování přihlašování se změnami:

* Brání generování nového uživatele rizikové události pro ovlivněného uživatele
* Správcům umožňuje ručně odstranit rizikové události by to ovlivnilo identitu uživatele a jeho obnovení stavu zabezpečení

## <a name="best-practices"></a>Osvědčené postupy

Výběr **vysokou** prahová hodnota snižuje počet, kolikrát zásada se aktivuje a minimalizuje dopad na uživatele.
Ale nezahrnuje **nízká** a **střední** uživatelů označených příznakem rizika ze zásad, které nemusí zabezpečit identity nebo zařízení, které byly dříve vzbuzovat podezření na nebo známé u něho ohrožena bezpečnost.

Při nastavování zásad,

* Vyloučit uživatele, kteří mohou generovat velké množství pozitivní hodnotu false (vývojáři, analytikům zabezpečení)
* Vyloučit uživatele v národních prostředí, kde není praktické povolení zásad (například žádný přístup k technické podpory)
* Použití **vysokou** prahové hodnoty během počáteční zásad uvádění, nebo pokud musíte minimalizovat problémy, koncový uživatel neuvidí.
* Použití **nízká** prahovou hodnotu, pokud vaše organizace vyžaduje vyšší úroveň zabezpečení. Výběr **nízká** prahová hodnota představuje další uživatele přihlásit výzvy, ale zvýšené zabezpečení.

Doporučené výchozí nastavení pro většinu organizací je a nakonfigurujte pravidlo pro **střední** prahová hodnota hledají rovnováhu mezi použitelnost a zabezpečení.

Přehled související uživatelské prostředí naleznete v tématu:

* [Dojde k ohrožení bezpečnosti účtu obnovení toku](flows.md#compromised-account-recovery).  
* [Dojde k ohrožení bezpečnosti účtu blokované tok](flows.md#compromised-account-blocked).  

**Chcete-li otevřít dialogové okno Konfigurace související**:

- Na **Azure AD Identity Protection** okno v **konfigurovat** klikněte na tlačítko **zásady rizik uživatelů**.

    ![Zásady rizik uživatelů](./media/howto-user-risk-policy/1009.png "zásady rizik uživatelů")




## <a name="next-steps"></a>Další postup

Získejte přehled o Azure AD Identity Protection, najdete v článku [Přehled služby Azure AD Identity Protection](overview.md).
