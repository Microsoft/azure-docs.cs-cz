---
title: Co je režim pouze pro sestavy podmíněného přístupu? -Azure Active Directory
description: Jak může pomáhat režim pouze sestav s nasazením zásad podmíněného přístupu
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6f4e4851d406b14ed38665274401b96446ac6a0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579072"
---
# <a name="what-is-conditional-access-report-only-mode"></a>Co je režim pouze pro sestavy podmíněného přístupu?

Podmíněný přístup se v mnoha případech používá pro naše zákazníky k zajištění zabezpečení díky použití správných řízení přístupu ve správných situacích. Některá z potíží s nasazením zásad podmíněného přístupu ve vaší organizaci ale určuje dopad na koncové uživatele. Může být obtížné předvídat počet a jména uživatelů ovlivněných běžnými iniciativami nasazení, jako je blokování ověřování starší verze, vyžadování vícefaktorového ověřování pro populace uživatelů nebo implementace zásad rizik přihlašování. 

Režim pouze pro sestavy je nový stav zásad podmíněného přístupu, který umožňuje správcům vyhodnotit dopad zásad podmíněného přístupu předtím, než je povolí ve svém prostředí.  S vydáním režimu pouze pro sestavy:

- V režimu pouze pro sestavy lze povolit zásady podmíněného přístupu.
- Během přihlašování jsou zásady v režimu pouze sestavy vyhodnocovány, ale nejsou vyhodnoceny.
- Výsledky jsou protokolovány na kartách **podmíněný přístup** a **pouze sestavy** v podrobnostech protokolu přihlášení.
- Zákazníci s předplatným Azure Monitor můžou monitorovat dopad zásad podmíněného přístupu pomocí sešitu s přehledem podmíněného přístupu.

> [!VIDEO https://www.youtube.com/embed/NZbPYfhb5Kc]

> [!WARNING]
> Zásady v režimu pouze pro sestavy, které vyžadují vyhovující zařízení, mohou uživatele v systému Mac, iOS a Android vyzvat k výběru certifikátu zařízení během vyhodnocování zásad, a to i v případě, že není vynuceno dodržování předpisů zařízením. Tyto výzvy se můžou opakovat, dokud zařízení nedodržuje předpisy. Pokud chcete koncovým uživatelům zabránit v přijímání výzev během přihlašování, vylučte platformy Mac, iOS a Android ze zásad pouze pro sestavy, které provádějí kontroly dodržování předpisů zařízením. Režim pouze pro sestavy nelze použít pro zásady podmíněného přístupu s oborem "akce uživatele".

![Karta pouze pro sestavy v protokolu přihlášení ke službě Azure AD](./media/concept-conditional-access-report-only/report-only-detail-in-sign-in-log.png)

## <a name="policy-results"></a>Výsledky zásad

Pokud je pro dané přihlášení vyhodnocena zásada v režimu pouze sestavy, existují čtyři nové možné hodnoty výsledků:

| Výsledek | Description |
| --- | --- |
| Pouze sestava: úspěch | Všechny nakonfigurované podmínky zásad, vyžadované neinteraktivní ovládací prvky grantu a ovládací prvky relace byly splněné. Například požadavek služby Multi-Factor Authentication je spokojen deklarací MFA, která už je v tokenu přítomná, nebo se na zařízení, které dodržuje předpisy, vyhovět zásadám zařízení, které dodržuje předpisy. |
| Pouze sestava: Chyba | Všechny nakonfigurované podmínky zásad byly splněny, ale nebyly splněny všechny požadované ovládací prvky pro udělení neinteraktivního udělení nebo řízení relace. Například zásada platí pro uživatele, kde je nakonfigurovaný ovládací prvek blokování, nebo zařízení nesplňuje požadavky zásad zařízení. |
| Pouze sestava: vyžaduje se akce uživatele. | Byly splněny všechny nakonfigurované podmínky zásad, ale akce uživatele by vyžadovala, aby splňovala požadované ovládací prvky udělení nebo relace. V režimu pouze sestavy není uživatel vyzván k splnění požadovaných ovládacích prvků. Například uživatelé nejsou dotazováni na výzvy služby Multi-Factor Authentication nebo podmínek použití.   |
| Pouze sestava: Nepoužito | Nebyly splněny všechny nakonfigurované podmínky zásad. Uživatel je například vyloučen ze zásady nebo zásada se vztahuje pouze na určitá důvěryhodná umístění. |

## <a name="conditional-access-insights-workbook"></a>Sešit pro podmíněný přístup – přehled

Správci mají možnost vytvořit více zásad v režimu pouze sestavy, takže je nutné pochopit individuální dopad obou zásad a kombinovaný dopad více zásad, které jsou vyhodnocovány dohromady. Nový sešit podmíněného přístupu s přehledem umožňuje správcům vizualizovat dotazy podmíněného přístupu a monitorovat dopad zásad pro daný časový rozsah, sadu aplikací a uživatele. 
 
## <a name="next-steps"></a>Další kroky

[Konfigurace režimu pouze pro sestavy na základě zásad podmíněného přístupu](howto-conditional-access-insights-reporting.md)
