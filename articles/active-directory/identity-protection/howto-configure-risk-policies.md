---
title: Jak nakonfigurovat zásady rizik ve službě Azure Active Directory identity protection (Aktualizovat) | Dokumentace Microsoftu
description: Jak nakonfigurovat zásady rizik ve službě Azure Active Directory identity protection (Aktualizovat).
services: active-directory
keywords: Azure active directory identity protection, zjišťování cloudových aplikací, Správa aplikací, zabezpečení, rizika, úroveň rizika, ohrožení zabezpečení, zásady zabezpečení
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: f9ffbebee06ab7b9e010c7e6c84fee533611fb38
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55567651"
---
# <a name="how-to-configure-risk-policies-in-azure-active-directory-identity-protection-refreshed"></a>Jak: Konfigurace zásady rizik ve službě Azure Active Directory identity protection (Aktualizovat)


Azure AD detekuje rizikových událostí, které jsou indikátory potenciálně ohroženými identit. Tím, že nakonfigurujete zásady rizik, můžete definovat automatické odpovědi pro výsledky vyhledávání:

- Pomocí zásady rizik přihlašování můžete nakonfigurovat odpověď v reálném čase rizikových událostí, které byly zjištěny během přihlášení uživatele. 
- Pomocí zásady rizik uživatelů můžete nakonfigurovat odpovědi na všechny aktivní uživatel riziko, jaké byly zjištěny pro uživatele v čase.  


## <a name="what-is-the-sign-in-risk-policy"></a>Co jsou zásady rizik přihlašování?

Azure AD analyzuje každé přihlášení uživatele. Cílem analýzy je ke zjištění podezřelé akce, které společně přihlášení. Například je přihlášení provést pomocí anonymní IP adresy, nebo je přihlášení zahájené z neznámého umístění? Ve službě Azure AD jsou podezřelé akce, které systém může zjistit, označované také jako rizikové události. Podle rizikové události, které byly zjištěny při přihlašování, Azure AD, vypočítá hodnotu. Hodnota představuje pravděpodobnost, že přihlášení není provést legitimní uživatel, (nízká, střední, vysoká). Pravděpodobnost se nazývá **úroveň rizika přihlášení**.

Zásady rizik přihlašování je automatické reakce, které můžete konfigurovat pro úroveň rizika konkrétních přihlášení. V odpovědi může blokovat přístup k vašim prostředkům nebo vyžadují předávání výzvu ověřování službou Multi-Factor Authentication (MFA) k získání přístupu.

   
## <a name="how-do-i-access-the-sign-in-risk-policy"></a>Jak získám přístup do zásady rizik přihlašování?
   
Zásady rizik přihlašování **konfigurovat** části na [stránku služby Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Zásady rizik přihlašování](./media/howto-configure-risk-policies/1014.png "zásady rizik přihlašování")


## <a name="sign-in-risk-policy-settings"></a>Nastavení zásad rizika přihlašování

Když konfigurujete zásady rizik přihlašování, budete muset nastavit:

- Uživatelé a skupiny, které zásady platí pro:

    ![Uživatelé a skupiny](./media/howto-configure-risk-policies/11.png)

- Úroveň rizika přihlášení, která aktivuje zásady:

    ![Úroveň rizika přihlášení](./media/howto-configure-risk-policies/12.png)

- Typ přístupu, kterou chcete vynutit, pokud byly splněny úroveň rizika přihlášení:  

    ![Access](./media/howto-configure-risk-policies/13.png)

- Stav zásad:

    ![Vynucení zásad](./media/howto-configure-risk-policies/14.png)


Dialogové okno Konfigurace zásad vám poskytne možnost zhodnotit dopad změny konfigurace.

![Odhadovaný dopad](./media/howto-configure-risk-policies/15.png)

## <a name="what-you-should-know-about-sign-in-risk-policies"></a>Co byste měli vědět o zásady rizik přihlašování

Můžete nakonfigurovat zásady zabezpečení rizik přihlašování vyžadovat vícefaktorové ověřování:

![Vyžadování MFA](./media/howto-configure-risk-policies/16.png)

Ale z bezpečnostních důvodů se toto nastavení funguje jenom pro uživatele, kteří jsou již zaregistrovány pro vícefaktorové ověřování. Ochrana identity blokuje uživatelů pomocí požadavku na vícefaktorové ověřování, pokud, nejsou ještě zaregistrovaný pro MFA.

Pokud chcete vyžadovat vícefaktorové ověřování pro rizikových přihlášení, měli byste:

1. Povolit [zásady registrace pro vícefaktorové ověřování](#multi-factor-authentication-registration-policy) pro ovlivnění uživatelé.

2. V relaci rizikové k provedení registrace MFA vyžadují ovlivněných uživatelů na přihlášení.

Dokončení těchto kroků se zajistí, že ověřování službou Multi-Factor Authentication je vyžadováno pro rizikové přihlášení.

Zásady rizik přihlašování je:

- Použít pro všechny prohlížeče provoz a přihlášení pomocí moderního ověřování.

- Bez použití aplikací s použitím starší protokoly zabezpečení tím, že zakážete koncového bodu WS-Trust ve federovaných zprostředkovatele identity, jako jsou služby AD FS.


Přehled související uživatelské prostředí naleznete v tématu:

* [Obnovení riziková přihlášení](flows.md#risky-sign-in-recovery)
* [Riziková přihlášení blokováno](flows.md#risky-sign-in-blocked)  
* [Možnosti přihlašování s Azure AD Identity Protection](flows.md)  









## <a name="what-is-a-user-risk-policy"></a>Co je zásady rizik uživatelů?

Azure AD analyzuje každé přihlášení uživatele. Cílem analýzy je ke zjištění podezřelé akce, které společně přihlášení. Ve službě Azure AD jsou podezřelé akce, které systém může zjistit, označované také jako rizikové události. Zatímco některé rizikové události lze zjistit v reálném čase, jsou také rizikové události, které vyžadují delší dobu. Systém vyžaduje ke zjištění nemožná cesta do netypických míst, období učení 14 dnů se dozvíte o běžné chování uživatele. Existuje několik možností, jak vyřešit zjištěné rizikové události. Například můžete ručně vyřešit jednotlivé rizikových událostí nebo můžete získat jejich duplicita se vyřešila pomocí rizika přihlášení nebo zásady rizik podmíněného přístupu uživatelů.

Všechny rizikové události, které byly zjištěny pro uživatele a nepovedlo se vyřešila jsou označovány jako aktivní rizikové události. Aktivní rizikové události, které jsou spojeny s uživatelem, jsou označovány jako uživatelského rizika. Azure AD podle rizika uživatele, vypočítá pravděpodobnost (nízká, střední, vysoká) ohrožené uživatele. Pravděpodobnost se nazývá úroveň rizika uživatele.

![Rizika uživatele](./media/howto-configure-risk-policies/11031.png)

Zásady rizik uživatelů je automatické reakce, které můžete konfigurovat pro úroveň rizika konkrétního uživatele. Pomocí zásady rizik uživatelů můžete zablokovat přístup k vašim prostředkům nebo vyžadovat změnu hesla pro uživatelský účet návrat do čistého stavu.


## <a name="how-do-i-access-the-user-risk-policy"></a>Jak získám přístup do zásady rizik uživatelů?
   
Zásady rizik uživatelů je v **konfigurovat** části na [stránku služby Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Zásady rizik uživatelů](./media/howto-configure-risk-policies/11014.png)



## <a name="user-risk-policy-settings"></a>Nastavení zásad rizika uživatele

Když konfigurujete zásady rizik uživatelů, je nutné nastavit:

- Uživatelé a skupiny, které zásady platí pro:

    ![Uživatelé a skupiny](./media/howto-configure-risk-policies/111.png)

- Úroveň rizika přihlášení, která aktivuje zásady:

    ![Úroveň rizika uživatele](./media/howto-configure-risk-policies/112.png)

- Typ přístupu, kterou chcete vynutit, pokud byly splněny úroveň rizika přihlášení:  

    ![Access](./media/howto-configure-risk-policies/113.png)

- Stav zásad:

    ![Vynucení zásad](./media/howto-configure-risk-policies/114.png)

Dialogové okno Konfigurace zásad vám poskytne možnost odhad dopadu vaší konfigurace.

![Odhadovaný dopad](./media/howto-configure-risk-policies/115.png)

## <a name="what-you-should-know-about-user-risk-polices"></a>Co byste měli vědět o riziko uživatele zásad

Můžete nastavit zásady zabezpečení rizik uživatelů k blokování uživatelů při přihlášení v závislosti na úrovni rizika.

![Blokování](./media/howto-configure-risk-policies/116.png)


Blokování přihlašování se změnami:

* Brání generování nového uživatele rizikové události pro ovlivněného uživatele
* Správcům umožňuje ručně odstranit rizikové události by to ovlivnilo identitu uživatele a jeho obnovení stavu zabezpečení


























## <a name="best-practices"></a>Osvědčené postupy

Výběr **vysokou** prahová hodnota snižuje počet, kolikrát zásada se aktivuje a minimalizuje dopad na uživatele.  

Ale nezahrnuje **nízká** a **střední** přihlášení označených jako rizikoví ze zásad, které nemusí blokovat útočník ze zneužití ohrožení zabezpečení identity.

Při nastavování zásad,

- Vyloučit uživatele, kteří nejsou / nemůže mít ověřování službou Multi-Factor Authentication

- Vyloučit uživatele v národních prostředí, kde není praktické povolení zásad (například žádný přístup k technické podpory)

- Vyloučit uživatele, kteří jsou pravděpodobně vygeneruje mnoho false poplachů (vývojáři, analytikům zabezpečení)

- Použití **vysokou** prahové hodnoty během zavádění počáteční zásad, nebo pokud musíte minimalizovat problémy, koncový uživatel neuvidí.

- Použití **nízká** prahovou hodnotu, pokud vaše organizace vyžaduje vyšší úroveň zabezpečení. Výběr **nízká** prahová hodnota představuje další uživatele přihlásit výzvy, ale zvýšené zabezpečení.

Doporučené výchozí nastavení pro většinu organizací je a nakonfigurujte pravidlo pro **střední** prahová hodnota hledají rovnováhu mezi použitelnost a zabezpečení.





## <a name="next-steps"></a>Další postup

 [Kanál 9: Azure AD a Identity zobrazit: Identity Protection ve verzi Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

