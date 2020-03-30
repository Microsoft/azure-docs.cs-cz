---
title: Poskytování zpětné vazby od rizik v programu Azure Active Directory Identity Protection
description: Jak a proč byste měli poskytnout zpětnou vazbu k detekci rizik ochrany identity.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4be8290f12d64f0c556100c63ec159bd414c6fcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382092"
---
# <a name="how-to-give-risk-feedback-in-azure-ad-identity-protection"></a>Postup: Poskytnutí zpětné vazby od rizik v azure ad identity protection

Azure AD Identity Protection umožňuje poskytnout zpětnou vazbu na jeho posouzení rizik. V následujícím dokumentu jsou uvedeny scénáře, kde chcete poskytnout zpětnou vazbu na posouzení rizik Azure AD Identity Protection a jak ho začleňujeme.

## <a name="what-is-a-detection"></a>Co je detekce?

Detekce identity Protection je indikátorem podezřelé aktivity z hlediska rizika identity. Tyto podezřelé aktivity se nazývají detekce rizik. Tyto detekce založené na identitě mohou být založeny na heuristiky, strojové učení nebo mohou pocházet z partnerských produktů. Tyto detekce se používají k určení rizika přihlášení a rizika pro uživatele,

* Uživatelské riziko představuje pravděpodobnost ohrožení identity.
* Riziko přihlášení představuje pravděpodobnost, že přihlášení je ohrožena (například přihlášení není povoleno vlastníkem identity).

## <a name="why-should-i-give-risk-feedback-to-azure-ads-risk-assessments"></a>Proč bych měl poskytnout zpětnou vazbu rizik k hodnocení rizik Azure AD? 

Existuje několik důvodů, proč byste měli poskytnout zpětnou vazbu o riziku Azure AD:

- **Zjistili jste, že uživatel azure ad nebo přihlášení posouzení rizik nesprávné**. Například přihlášení uvedené v sestavě Rizikové přihlášení bylo neškodné a všechna zjištění tohoto přihlášení byla falešně pozitivní.
- **Ověřili jste, že azure ad uživatele nebo přihlášení posouzení rizika byla správná**. Například přihlášení zobrazené v sestavě rizikové přihlášení bylo skutečně škodlivé a chcete, aby služba Azure AD věděla, že všechna zjišťování v tomto přihlášení byla true positives.
- **Bylo opraveno riziko pro tohoto uživatele mimo Azure AD Identity Protection** a chcete, aby byla aktualizována úroveň rizika uživatele.

## <a name="how-does-azure-ad-use-my-risk-feedback"></a>Jak Azure AD používá moji zpětnou vazbu o riziku?

Azure AD používá vaši zpětnou vazbu k aktualizaci rizika základního uživatele nebo přihlášení a přesnosti těchto událostí. Tato zpětná vazba pomáhá zabezpečit koncového uživatele. Například po potvrzení ohrožení zabezpečení přihlášení, Azure AD okamžitě zvyšuje riziko uživatele a přihlášení celkové riziko (není riziko v reálném čase) na vysoké. Pokud je tento uživatel zahrnut do zásad rizika uživatele, aby vynutil uživatelům s vysokým rizikem, aby bezpečně resetovali svá hesla, uživatel se při příštím přihlášení automaticky napraví.

## <a name="how-should-i-give-risk-feedback-and-what-happens-under-the-hood"></a>Jak mám poskytnout zpětnou vazbu od rizika a co se děje pod kapotou?

Tady jsou scénáře a mechanismy, které poskytují zpětnou vazbu rizik azure ad.

| Scénář | Jak poskytnout zpětnou vazbu? | Co se děje pod kapotou? | Poznámky |
| --- | --- | --- | --- |
| **Přihlášení není ohroženo (falešně pozitivní)** <br> Zpráva "Risky sign-in" zobrazuje rizikové přihlášení [Rizikový stav = V riziku], ale toto přihlášení nebylo ohroženo. | Vyberte přihlášení a klikněte na "Potvrdit přihlašovací trezor". | Azure AD přesune celkové riziko přihlášení na žádný [Stav rizika = Potvrzeno bezpečné; Úroveň rizika (Agregát) = -] a zvrátí jeho dopad na riziko uživatele. | V současné době je možnost Potvrdit bezpečné přihlášení k dispozici pouze v sestavě Rizikové přihlášení. |
| **Ohrožení zabezpečení přihlášení (True positive)** <br> Zpráva "Risky sign-in" zobrazuje rizikové přihlášení [Rizikový stav = Rizikový stav] s nízkým rizikem [Úroveň rizika (Agregát) = Nízká] a toto přihlášení bylo skutečně ohroženo. | Vyberte přihlášení a klikněte na "Potvrdit ohrožení zabezpečení přihlášení". | Azure AD přesune celkové riziko přihlášení a riziko uživatele na vysoké [stav rizika = Potvrzeno ohroženo; Úroveň rizika = vysoká]. | V současné době je možnost Potvrdit ohrožení zabezpečení přihlášení je k dispozici pouze v sestavě Rizikové přihlášení. |
| **Ohrožení zabezpečení uživatelem (True positive)** <br> "Zpráva rizikových uživatelů" ukazuje rizikového uživatele [Rizikový stav = Rizikový stav] s nízkým rizikem [Úroveň rizika = Nízká] a tento uživatel byl skutečně ohrožen. | Vyberte uživatele a klikněte na "Potvrdit ohrožení zabezpečení uživatele". | Azure AD přesune riziko uživatele na vysoké [stav rizika = Potvrzeno ohroženo; Úroveň rizika = Vysoká] a přidá novou detekci "Admin potvrdil uživatele ohrožena". | V současné době je možnost Potvrdit ohrožení zabezpečení uživatele matná pouze v sestavě Rizikových uživatelů. <br> Detekce "Správce potvrdil uživatele ohrožena" se zobrazí na kartě "Detekce rizik, které nejsou spojeny s přihlášením" v sestavě "Rizikoví uživatelé". |
| **Uživatelem opraveným mimo azure ad identity protection (True positive + Remediated)** <br> Sestava "Rizikových uživatelů" zobrazuje ohroženého uživatele a následně jsem uživatele nachtěl mimo azure ad identity protection. | 1. Vyberte uživatele a klikněte na tlačítko "Potvrdit ohrožení zabezpečení uživatele". (Tento proces potvrzuje Azure AD, že uživatel byl skutečně ohrožena.) <br> 2. Počkejte, až se úroveň rizika uživatele přejde na vysokou. (Tento čas poskytuje Azure AD potřebný čas, aby výše uvedené zpětné vazby na řízení rizik.) <br> 3. Vyberte uživatele a klikněte na tlačítko "Odmítnout riziko uživatele". (Tento proces potvrzuje azure ad, že uživatel již není ohrožena.) |  Azure AD přesune riziko uživatele na žádný [Stav rizika = Zamítnuto; Úroveň rizika = -] a uzavírá riziko u všech existujících přihlášení s aktivním rizikem. | Kliknutím na tlačítko Odmítnout riziko uživatele uzavřete všechna rizika u uživatele a minulých přihlášení. Tuto akci nelze vrátit zpět. |
| **Uživatel nebyl ohrožen (falešně pozitivní)** <br> Sestava "Rizikových uživatelů" se zobrazuje u rizikového uživatele, ale uživatel není ohrožen. | Vyberte uživatele a klikněte na tlačítko "Odmítnout riziko uživatele". (Tento proces potvrzuje azure ad, že uživatel není ohrožena.) | Azure AD přesune riziko uživatele na žádný [Stav rizika = Zamítnuto; Úroveň rizika = -]. | Kliknutím na tlačítko Odmítnout riziko uživatele uzavřete všechna rizika u uživatele a minulých přihlášení. Tuto akci nelze vrátit zpět. |
| Chci uzavřít riziko uživatele, ale nejsem si jistý, zda je uživatel ohrožen / bezpečný. | Vyberte uživatele a klikněte na tlačítko "Odmítnout riziko uživatele". (Tento proces potvrzuje azure ad, že uživatel již není ohrožena.) | Azure AD přesune riziko uživatele na žádný [Stav rizika = Zamítnuto; Úroveň rizika = -]. | Kliknutím na tlačítko Odmítnout riziko uživatele uzavřete všechna rizika u uživatele a minulých přihlášení. Tuto akci nelze vrátit zpět. Doporučujeme, abyste uživatele okamžitě nalézali kliknutím na "Resetheslo" nebo požádali jej, aby bezpečně resetoval/změnil své přihlašovací údaje. |

Zpětná vazba na detekci rizik uživatelů v aplikaci Identity Protection je zpracována offline a může nějakou dobu trvat jejich aktualizace. Sloupec stavu zpracování rizika bude poskytovat aktuální stav zpracování zpětné vazby.

![Stav zpracování rizikpro zprávu o rizikovém uživateli](./media/howto-identity-protection-risk-feedback/risky-users-provide-feedback.png)

## <a name="next-steps"></a>Další kroky

- [Odkaz na zjišťování rizik ochrany identitslužby Azure Active Directory](risk-events-reference.md)
