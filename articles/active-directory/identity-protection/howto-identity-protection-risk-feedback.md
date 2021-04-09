---
title: Poskytněte zpětnou vazbu rizika v Azure Active Directory Identity Protection
description: Jak a proč byste měli poskytovat zpětnou vazbu k detekcím rizik ochrany identity
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 939ffc9e2a8fb8ce3a25dc212e3df34dc6bb2ec1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88950353"
---
# <a name="how-to-give-risk-feedback-in-azure-ad-identity-protection"></a>Postupy: poskytnutí zpětné vazby o riziku v Azure AD Identity Protection

Azure AD Identity Protection vám umožní sdělit svůj názor na posouzení rizik. Následující dokument obsahuje seznam scénářů, ve kterých byste chtěli sdělit svůj názor na posouzení rizika Azure AD Identity Protection a způsob, jakým ho zachováme.

## <a name="what-is-a-detection"></a>Co je zjišťování?

Detekce Identity Protection je indikátorem podezřelé aktivity z perspektivy rizika identity. Tyto podezřelé aktivity se nazývají detekce rizik. Tyto detekce na základě identity můžou být založené na heuristikách, strojovém učení nebo může pocházet z partnerských produktů. Tyto detekce slouží k určení rizika přihlašování a rizika pro uživatele.

* Riziko uživatele představuje pravděpodobnost ohrožení identity.
* Riziko přihlášení představuje pravděpodobnost, že je přihlášení ohroženo (například přihlášení není autorizováno vlastníkem identity).

## <a name="why-should-i-give-risk-feedback-to-azure-ads-risk-assessments"></a>Proč bych měl poskytnout rizikovou zpětnou vazbu k posouzení rizik Azure AD? 

Existuje několik důvodů, proč byste měli poskytnout zpětnou vazbu k riziku Azure AD:

- **Našli jste nesprávné vyhodnocení rizika uživatele nebo přihlášení služby Azure AD**. Například přihlášení zobrazené v sestavě rizikové přihlášení bylo neškodné a všechny detekce tohoto přihlášení byly falešně pozitivní.
- **Ověřili jste správnost vyhodnocení rizika uživatele nebo přihlašování služby Azure AD**. Například přihlášení zobrazené v sestavě rizikové přihlášení bylo opravdu škodlivé a chcete, aby služba Azure AD věděla, že všechna zjištění tohoto přihlášení byla pravdivá.
- Napravili **jste riziko pro daného uživatele mimo Azure AD Identity Protection** a chcete aktualizovat úroveň rizika uživatele.

## <a name="how-does-azure-ad-use-my-risk-feedback"></a>Jak Azure AD využívá můj názor na rizika?

Azure AD používá vaši zpětnou vazbu k aktualizaci rizika podkladového uživatele nebo přihlašování a přesnosti těchto událostí. Tato zpětná vazba pomáhá zabezpečit koncového uživatele. Když například ověříte, že je přihlášení ohrožené, Azure AD okamžitě zvyšuje riziko uživatele a agregované riziko přihlášení (ne riziko v reálném čase) na maximum. Pokud je tento uživatel zahrnutý v zásadách rizik uživatelů, aby mohli uživatelé s vysokým rizikem bezpečně resetovat hesla, uživatel se při příštím přihlášení automaticky opraví.

## <a name="how-should-i-give-risk-feedback-and-what-happens-under-the-hood"></a>Jak mám sdělit svůj názor na riziko a co se stane pod digestoří?

Tady jsou scénáře a mechanismy, které vám poskytnou zpětnou vazbu na rizika pro Azure AD.

| Scenario | Jak můžete sdělit svůj názor? | Co se stane pod digestoří? | Poznámky |
| --- | --- | --- | --- |
| **Neohrožené přihlášení (falešně pozitivní)** <br> Sestava rizikových přihlášení zobrazuje nerizikové přihlašování [rizikový stav = ohroženo], ale toto přihlášení nebylo ohroženo. | Vyberte přihlášení a klikněte na potvrdit bezpečné přihlášení. | Azure AD přesune agregované riziko přihlášení do žádného [stav rizika = potvrzené zabezpečení; Úroveň rizika (agregace) =-] a vrátí dopad na riziko uživatele. | V současné době je možnost potvrdit přihlášení bezpečně dostupná jenom v sestavě rizikové přihlašovacích. |
| **Narušeno přihlášení (true pozitivní)** <br> Sestava rizikové přihlašovací údaje zobrazuje nerizikové přihlašování [rizikový stav = ohroženo] s nízkým rizikem [úroveň rizika (agregace) = nízká] a toto přihlášení bylo skutečně ohroženo. | Vyberte přihlášení a klikněte na potvrdit narušené přihlášení. | Azure AD přesune agregované riziko přihlášení a riziko pro uživatele do vysoké úrovně [rizikový stav = potvrzené ohrožení zabezpečení; Úroveň rizika = vysoká]. | V současné době je možnost potvrdit narušení přihlášení dostupná jenom v sestavě rizikové přihlašovacích. |
| **Ohrožení zabezpečení uživatele (true pozitivní)** <br> Sestava rizikové uživatele zobrazuje nerizikové uživatele [rizikový stav = ohroženo] s nízkým rizikem [úroveň rizika = nízká] a tímto uživatelem došlo k ohrožení zabezpečení. | Vyberte uživatele a klikněte na potvrdit ohrožení zabezpečení uživatele. | Azure AD přesune riziko uživatele do vysoké úrovně [rizikový stav = potvrzené ohrožení zabezpečení; Úroveň rizika = vysoká] a přidá nový detekční účet správce potvrzující ohrožení uživatele. | V současné době je možnost potvrdit ohrožení uživatele dostupná jenom v sestavě rizikové uživatele. <br> Detekce "správce potvrzuje, že došlo k ohrožení uživatele", se zobrazí v části detekce rizik, která není propojena s přihlášením v sestavě rizikové uživatele. |
| **Napravení uživatele mimo Azure AD Identity Protection (true kladné + opraveno)** <br> Sestava rizikové uživatele zobrazuje nerizikového uživatele a uživatel ho následně napravoval mimo Azure AD Identity Protection. | 1. Vyberte uživatele a klikněte na potvrdit napadení uživatelem. (Tento proces potvrzuje službě Azure AD, že uživatel byl skutečně ohrožen.) <br> 2. Počkejte, až bude úroveň rizika uživatele přejít na hodnotu Vysoká. (Tentokrát poskytuje Azure AD potřebnou dobu k tomu, aby se výše uvedená zpětná vazba vybrala na rizikový modul.) <br> 3. Vyberte uživatele a klikněte na tlačítko Zavřít riziko uživatele. (Tento proces potvrzuje službě Azure AD, že uživatel už není ohrožen.) |  Azure AD přesune riziko pro uživatele na žádné [stav rizika = zrušeno; Úroveň rizika =-] a uzavře riziko pro všechna existující přihlášení, která mají aktivní riziko. | Kliknutím na Zavřít riziko uživatele zavřete všechna rizika pro přihlášení a uživatele v minulosti. Tuto akci nelze vrátit zpět. |
| **Uživatel není napadený (falešně pozitivní).** <br> Sestava rizikové uživatele zobrazuje uživatele v rizikovém uživateli, ale uživatel není ohrožen. | Vyberte uživatele a klikněte na tlačítko Zavřít riziko uživatele. (Tento proces potvrdí službě Azure AD, že uživatel není ohrožen.) | Azure AD přesune riziko pro uživatele na žádné [stav rizika = zrušeno; Úroveň rizika =-]. | Kliknutím na Zavřít riziko uživatele zavřete všechna rizika pro přihlášení a uživatele v minulosti. Tuto akci nelze vrátit zpět. |
| Chci zavřít riziko uživatele, ale nejste si jistí, jestli je uživatel napadený/bezpečný. | Vyberte uživatele a klikněte na tlačítko Zavřít riziko uživatele. (Tento proces potvrzuje službě Azure AD, že uživatel už není ohrožen.) | Azure AD přesune riziko pro uživatele na žádné [stav rizika = zrušeno; Úroveň rizika =-]. | Kliknutím na Zavřít riziko uživatele zavřete všechna rizika pro přihlášení a uživatele v minulosti. Tuto akci nelze vrátit zpět. Doporučujeme, abyste uživatele opravili kliknutím na resetovat heslo nebo požádat uživatele, aby bezpečně obnovil nebo změnil své přihlašovací údaje. |

Zpětná vazba na zjišťování rizik uživatelů v ochraně identity je zpracována offline a aktualizace může nějakou dobu trvat. Sloupec stav zpracování rizika vám poskytne aktuální stav zpracování zpětné vazby.

![Stav zpracování rizik pro sestavu rizikového uživatele](./media/howto-identity-protection-risk-feedback/risky-users-provide-feedback.png)

## <a name="next-steps"></a>Další kroky

- [Referenční informace o Azure Active Directory Identity Protection detekci rizik](./concept-identity-protection-risks.md)