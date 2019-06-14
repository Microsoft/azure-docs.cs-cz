---
title: Poskytnout zpětnou vazbu ohledně rizikových událostí ve službě Azure AD Identity Protection – Azure Active Directory
description: Jak a proč by měl poskytnete zpětnou vazbu v události rizika Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66d53590e89afb1a903b22ff60e32871a1502ada
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65827902"
---
# <a name="how-to-give-risk-feedback-in-azure-ad-identity-protection"></a>Jak: Váš názor rizika v Azure AD Identity Protection

Azure AD Identity Protection můžete poskytnout zpětnou vazbu na posouzení rizika. Následující dokument obsahuje seznam scénářů, kde chcete poskytnout zpětnou vazbu na posouzení rizika služby Azure AD Identity Protection a jak jsme začlení jej.

## <a name="what-is-a-detection"></a>Co je detekce?

Zjišťování Identity Protection je indikátorem podezřelou aktivitu z hlediska rizika identity. Tyto podezřelé aktivity se nazývají rizikové události. Tyto detekce založené na identitě může být založen na heuristickými metodami, strojové učení nebo může pocházet z partnerským produktům. Tyto detekce slouží k určení rizika přihlášení a uživatelského rizika

* Riziko uživatele představuje pravděpodobnost, že dojde k ohrožení identity.
* Pravděpodobnost, že přihlášení dojde k narušení představuje riziko přihlášení (například přihlášení nemá oprávnění od vlastníka identity).

## <a name="why-should-i-give-risk-feedback-to-azure-ads-risk-assessments"></a>Proč by měla poskytnout zpětnou vazbu rizika na posouzení rizika služby Azure AD? 

Tady je několik důvodů, proč by měl poskytnete zpětnou vazbu riziko Azure AD:

1. **Nalezena nesprávná posouzení rizika uživatele nebo přihlášení Azure AD**. Například u přihlášení zobrazí v sestavě 'Rizikových přihlášení' je neškodné a všechna nalezení na tomto přihlášení počet falešně pozitivních výsledků.
1. **Ověření uživatele této Azure AD nebo posouzení rizika přihlašování byla správná**. Například zobrazí v sestavě "Rizikových přihlášení" úspěšně jste skutečně škodlivý a chcete vědět, že všechna nalezení na tomto přihlášení byla pravdivě pozitivní Azure AD.
1. **Opravené riziko na uživatele mimo Azure AD Identity Protection** a chcete, aby úroveň rizika uživatele aktualizovat.

## <a name="how-does-azure-ad-use-my-risk-feedback"></a>Jak používat Azure AD mého názoru rizika?

Azure AD používá vaše zpětná vazba k aktualizaci riziko základní uživatele a/nebo přihlášení. Tato zpětná vazba pomáhá je zabezpečit koncového uživatele. Například Jakmile se ujistíte, že přihlášení dojde k ohrožení, Azure AD okamžitě zvyšuje riziko uživatele a na agregační rizika přihlášení (ne v reálném čase riziko) na hodnotu Vysoká. Pokud vaše zásady rizik uživatelů vynutit bezpečně resetování hesel uživatelů s vysokým rizikem je součástí tohoto uživatele, uživatel automaticky napraví samotné příště jejich přihlášení.

## <a name="how-should-i-give-risk-feedback-and-what-happens-under-the-hood"></a>Jak by měla poskytnout riziko zpětnou vazbu a co se stane pod pokličkou?

Tady jsou scénáře a mechanismy pro váš názor rizik do služby Azure AD.

| Scénář | Jak poskytnout zpětnou vazbu? | Co se stane pod pokličkou? | Poznámky |
| --- | --- | --- | --- |
| **Přihlášení není dojde k ohrožení bezpečnosti (falešně pozitivní)** <br> 'Rizikových přihlášení' sestava se zobrazí rizikové přihlášení [riziko, stav = ohrožených], ale, že nebyla ohrožena přihlášení. | Vyberte přihlášení a klikněte na "Potvrdit přihlášení bezpečných". | Azure AD se přesune vaší agregační rizika přihlašování na hodnotu none [riziko, stav = Potvrzeno bezpečné; Úroveň (agregace) rizika =-] a vrátíte zpět jeho dopad na riziko uživatele. | V současné době "Potvrdit přihlášení bezpečných" možnost je k dispozici pouze v sestavě "Rizikových přihlášení". |
| **Přihlašování dojde k ohrožení bezpečnosti (skutečně pozitivní)** <br> 'Rizikových přihlášení' sestava se zobrazí rizikové přihlášení [riziko, stav = ohrožených] s nízkým rizikem [(agregace) úroveň rizika = nízká] a že přihlášení se skutečně dojde k ohrožení bezpečnosti. | Vyberte přihlášení a klikněte na "Potvrdit přihlašování dojde k ohrožení bezpečnosti". | Azure AD se přesune přihlášení na agregační rizika a uživatelského rizika na hodnotu Vysoká [riziko, stav = Potvrzeno dojde k ohrožení bezpečnosti; Úroveň rizika = vysoká]. | V současné době možnost 'Potvrdit přihlašování dojde k ohrožení bezpečnosti' je k dispozici pouze v sestavě "Rizikových přihlášení". |
| **Uživatel, dojde k ohrožení bezpečnosti (skutečně pozitivní)** <br> 'Rizikový uživatel' sestava zobrazí rizikové uživatele [rizika stav = ohrožených] s nízkým rizikem [úrovně rizika = nízká] a tomuto uživateli se skutečně dojde k ohrožení bezpečnosti. | Vyberte uživatele a klikněte na "Potvrzení uživatelem dojde k ohrožení bezpečnosti". | Azure AD se přesunou na vysoké riziko uživatele [riziko, stav = Potvrzeno dojde k ohrožení bezpečnosti; Úroveň rizika = vysoká] a přidá nové detekce "Správce potvrzen, dojde k ohrožení bezpečnosti uživatele". | V současné době 'Dojde k ohrožení bezpečnosti potvrzení uživatelem' možnost je k dispozici pouze v sestavě "Rizikový uživatel". <br> Detekce "Správce potvrzen, dojde k ohrožení bezpečnosti uživatelského" se zobrazí na kartě "Rizikových událostí není propojená se u přihlášení" v sestavě "Rizikový uživatel". |
| **Uživatel napravených mimo Azure AD Identity Protection (pravdivě pozitivní upozornění a Remediated)** <br> 'Rizikový uživatel' sestava zobrazí rizikové uživatele a následně můžu mít napravených uživatele mimo Azure AD Identity Protection. | 1. Vyberte uživatele a klikněte na tlačítko "Potvrzení uživatelem dojde k ohrožení bezpečnosti". (Tento proces potvrdí do služby Azure AD, aby uživatel byl skutečně dojde k ohrožení bezpečnosti.) <br> 2. Počkejte uživatelské úrovni rizika"Přejít na hodnotu Vysoká. (Tento čas poskytuje Azure AD potřebný čas provést výše zpětnou vazbu k modulu riziko.) <br> 3. Vyberte uživatele a klikněte na tlačítko "Zrušit riziko pro uživatele". (Tento proces potvrdí do služby Azure AD, že uživatel je už dojde k ohrožení bezpečnosti.) |  Azure AD riziko uživatele přesune na hodnotu none [riziko, stav = Zamítnuto; Úroveň rizika =-] a zavře všechna existující přihlášení s aktivní riziko rizika. | Kliknutím na 'Zavřít uživatelského rizika' se zavře všechna rizika na uživatele a po přihlášení. Tuto akci nejde vrátit zpět. |
| **Uživatel není dojde k ohrožení bezpečnosti (falešně pozitivní)** <br> Sestava 'Rizikoví uživatelé' ukazuje na rizikové uživatele, ale uživatel nedošlo k ohrožení bezpečnosti. | Vyberte uživatele a klikněte na tlačítko "Zrušit riziko pro uživatele". (Tento proces potvrdí do služby Azure AD, že uživatel není ohrožena.) | Azure AD riziko uživatele přesune na hodnotu none [riziko, stav = Zamítnuto; Úroveň rizika =-]. | Kliknutím na 'Zavřít uživatelského rizika' se zavře všechna rizika na uživatele a po přihlášení. Tuto akci nejde vrátit zpět. |
| Chcete zavřít uživatelského rizika, ale nejste si jisti, zda je uživatel ohrožení zabezpečení / bezpečné. | Vyberte uživatele a klikněte na tlačítko "Zrušit riziko pro uživatele". (Tento proces potvrdí do služby Azure AD, že uživatel je už dojde k ohrožení bezpečnosti.) | Azure AD riziko uživatele přesune na hodnotu none [riziko, stav = Zamítnuto; Úroveň rizika =-]. | Kliknutím na 'Zavřít uživatelského rizika' se zavře všechna rizika na uživatele a po přihlášení. Tuto akci nejde vrátit zpět. Doporučujeme, abyste opravit uživatel kliknutím na "Resetovat heslo", nebo požádat o uživateli bezpečně resetovat nebo změnit jejich přihlašovací údaje. |

Zpětná vazba na uživatele rizikových událostí ve službě Identity Protection se zpracovávají offline a může trvat nějakou dobu aktualizace. Riziko zpracování sloupce stavu bude poskytovat aktuální stav zpracování zpětné vazby.

![Stav zpracování sestavy rizikový uživatel rizika](./media/howto-provide-risk-event-feedback/risky-users-provide-feedback.png)

## <a name="next-steps"></a>Další postup

[Azure Active Directory Identity Protection rizikem události – referenční informace](risk-events-reference.md)