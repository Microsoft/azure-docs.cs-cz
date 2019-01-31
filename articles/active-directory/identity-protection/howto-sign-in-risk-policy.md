---
title: Jak nakonfigurovat zásady rizik přihlašování v Azure Active Directory Identity Protection | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat zásady rizik přihlašování Azure AD Identity Protection.
services: active-directory
keywords: Azure active directory identity protection, zjišťování cloudových aplikací, Správa aplikací, zabezpečení, rizika, úroveň rizika, ohrožení zabezpečení, zásady zabezpečení
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2019
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: ff346f9c842672b426a75eadaa278ca91de5d9c6
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55297485"
---
# <a name="how-to-configure-the-sign-in-risk-policy"></a>Jak: Konfigurace zásad rizik přihlášení

Azure Active Directory detekuje [typech rizikových událostí](../reports-monitoring/concept-risk-events.md#risk-event-types) v reálném čase a v režimu offline. Každý rizikovou událost, která byla zjištěna přihlásit uživatele přispívá k logický pojem volána rizikových přihlášení. Riziková přihlášení je indikátorem pokusu přihlásit, který se nemusí provést legitimním vlastníkem uživatelského účtu.


## <a name="what-is-the-sign-in-risk-policy"></a>Co jsou zásady rizik přihlašování?

Azure AD analyzuje každé přihlášení uživatele. Cílem analýzy je ke zjištění podezřelé akce, které společně přihlášení. Například je přihlášení provést pomocí anonymní IP adresy, nebo je přihlášení zahájené z neznámého umístění? Ve službě Azure AD jsou podezřelé akce, které systém může zjistit, označované také jako rizikové události. Podle rizikové události, které byly zjištěny při přihlašování, Azure AD, vypočítá hodnotu. Hodnota představuje pravděpodobnost, že přihlášení není provést legitimní uživatel, (nízká, střední, vysoká). Pravděpodobnost se nazývá **úroveň rizika přihlášení**.

Zásady rizik přihlašování je automatické reakce, které můžete konfigurovat pro úroveň rizika konkrétních přihlášení. V odpovědi může blokovat přístup k vašim prostředkům nebo vyžadují předávání výzvu ověřování službou Multi-Factor Authentication (MFA) k získání přístupu.

   
## <a name="how-do-i-access-the-sign-in-risk-policy"></a>Jak získám přístup do zásady rizik přihlašování?
   
Zásady rizik přihlašování **konfigurovat** části na [stránku služby Azure AD Identity Protection](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy).
   
![Zásady rizik přihlašování](./media/howto-sign-in-risk-policy/1014.png "zásady rizik přihlašování")


## <a name="policy-settings"></a>Nastavení zásad

Když konfigurujete zásady rizik přihlašování, budete muset nastavit:

- Uživatelé a skupiny, které zásady platí pro:

    ![Uživatelé a skupiny](./media/howto-sign-in-risk-policy/11.png)

- Úroveň rizika přihlášení, která aktivuje zásady:

    ![Úroveň rizika přihlášení](./media/howto-sign-in-risk-policy/12.png)

- Typ přístupu, kterou chcete vynutit, pokud byly splněny úroveň rizika přihlášení:  

    ![Access](./media/howto-sign-in-risk-policy/13.png)

- Stav zásad:

    ![Vynucení zásad](./media/howto-sign-in-risk-policy/14.png)


Dialogové okno Konfigurace zásad vám poskytne možnost zhodnotit dopad změny konfigurace.

![Odhadovaný dopad](./media/howto-sign-in-risk-policy/15.png)

## <a name="what-you-should-know"></a>Co byste měli vědět

Můžete nakonfigurovat zásady zabezpečení rizik přihlašování vyžadovat vícefaktorové ověřování:

![Vyžadování MFA](./media/howto-sign-in-risk-policy/16.png)

Ale z bezpečnostních důvodů se toto nastavení funguje jenom pro uživatele, kteří jsou již zaregistrovány pro vícefaktorové ověřování. Ochrana identity blokuje uživatelů pomocí požadavku na vícefaktorové ověřování, pokud, nejsou ještě zaregistrovaný pro MFA.

Pokud chcete vyžadovat vícefaktorové ověřování pro rizikových přihlášení, měli byste:

1. Povolit [zásady registrace pro vícefaktorové ověřování](howto-mfa-policy.md) pro ovlivnění uživatelé.

2. Vyžadovat ovlivněných uživatelů pro přihlášení k – rizikové relace k provedení registrace MFA.

Dokončení těchto kroků se zajistí, že ověřování službou Multi-Factor Authentication je vyžadováno pro rizikové přihlášení.

Zásady rizik přihlašování je:

- Použít pro všechny prohlížeče provoz a přihlášení pomocí moderního ověřování.

- Bez použití aplikací s použitím starší protokoly zabezpečení tím, že zakážete koncového bodu WS-Trust ve federovaných zprostředkovatele identity, jako jsou služby AD FS.


Přehled související uživatelské prostředí naleznete v tématu:

* [Obnovení riziková přihlášení](flows.md#risky-sign-in-recovery)
* [Riziková přihlášení blokováno](flows.md#risky-sign-in-blocked)  
* [Možnosti přihlašování s Azure AD Identity Protection](flows.md)  

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

Získejte přehled o Azure AD Identity Protection, najdete v článku [Přehled služby Azure AD Identity Protection](overview.md).
