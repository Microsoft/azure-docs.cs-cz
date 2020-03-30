---
title: Co je režim pouze pro sestavu podmíněného přístupu? - Služba Azure Active Directory
description: Jak může pomoc režimu sestavy pomocí nasazení zásad podmíněného přístupu
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd41e79a1e08c57e806f6ada32faccfa5fdf5792
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295285"
---
# <a name="what-is-conditional-access-report-only-mode"></a>Co je režim pouze pro sestavu podmíněného přístupu?

Podmíněný přístup je široce používán našimi zákazníky, aby zůstali v bezpečí použitím správných ovládacích prvků přístupu za správných okolností. Jednou z výzev při nasazování zásad podmíněného přístupu ve vaší organizaci je však určení dopadu na koncové uživatele. Může být obtížné předvídat počet a názvy uživatelů ovlivněných běžnými iniciativami nasazení, jako je blokování starších verzí ověřování, vyžadování vícefaktorového ověřování pro populaci uživatelů nebo implementace zásad rizik přihlášení. 

Režim pouze pro sestavu je nový stav zásad podmíněného přístupu, který umožňuje správcům vyhodnotit dopad zásad podmíněného přístupu před povolením v jejich prostředí.  S vydáním režimu pouze pro hlášení:

- Zásady podmíněného přístupu lze povolit v režimu pouze pro sestavu.
- Během přihlášení zásady v režimu pouze sestavy jsou vyhodnocovány, ale nejsou vynuceny.
- Výsledky jsou zaznamenány v **podmíněném přístupu** a **sestavy pouze (Preview)** karty přihlašovací protokol podrobnosti.
- Zákazníci s předplatným Azure Monitoru můžou sledovat dopad svých zásad podmíněného přístupu pomocí sešitu Přehledy podmíněného přístupu.

> [!WARNING]
> Zásady v režimu pouze pro sestavu, které vyžadují kompatibilní zařízení, mohou uživatele na Macu, iOS a Androidu vyzvat k výběru certifikátu zařízení během vyhodnocení zásad, i když dodržování předpisů zařízení není vynuceno. Tyto výzvy se mohou opakovat, dokud nebude zařízení kompatibilní. Chcete-li koncovým uživatelům zabránit v přijímání výzev během přihlašování, vylučte platformy zařízení Mac, iOS a Android ze zásad pouze pro sestavy, které provádějí kontroly dodržování předpisů zařízení.

![Karta Pouze sestava v přihlašovacím protokolu Azure AD](./media/concept-conditional-access-report-only/report-only-detail-in-sign-in-log.png)

## <a name="policy-results"></a>Výsledky zásad

Pokud je zásada v režimu pouze pro sestavu vyhodnocena pro dané přihlášení, existují čtyři nové možné hodnoty výsledků:

| Výsledek | Popis |
| --- | --- |
| Pouze zpráva: Úspěch | Byly splněny všechny nakonfigurované podmínky zásad, požadované neinteraktivní ovládací prvky grantů a ovládací prvky relace. Například požadavek vícefaktorového ověřování je splněn deklarací mfa, která je již v tokenu k dispozici, nebo je splněna kompatibilní zásada zařízení provedením kontroly zařízení na kompatibilním zařízení. |
| Pouze sestava: Selhání | Všechny nakonfigurované podmínky zásad byly splněny, ale nebyly splněny všechny požadované neinteraktivní ovládací prvky grantu nebo ovládací prvky relace. Zásada se například vztahuje na uživatele, u kterého je nakonfigurován ovládací prvek bloku nebo pokud zařízení selže v zásadách kompatibilního zařízení. |
| Pouze sestava: Je vyžadována akce uživatele | Všechny nakonfigurované podmínky zásad byly splněny, ale akce uživatele by byla nutná ke splnění požadovaných ovládacích prvků udělení nebo relací. V režimu pouze sestavy není uživatel vyzván ke splnění požadovaných ovládacích prvků. Uživatelé například nejsou vyzváni k vícefaktorové ověřování výzvy nebo podmínky použití.   |
| Pouze sestava: Nepoužito | Nebyly splněny všechny nakonfigurované podmínky zásad. Například uživatel je vyloučen ze zásady nebo se zásada vztahuje pouze na určitá důvěryhodná místa s názvem. |

## <a name="conditional-access-insights-workbook"></a>Sešit Přehledy podmíněného přístupu

Správci mají možnost vytvářet více zásad v režimu pouze sestavy, takže je nutné pochopit individuální dopad jednotlivých zásad a kombinovaný dopad více zásad vyhodnocených společně. Nový sešit Přehledy podmíněného přístupu umožňuje správcům vizualizovat dotazy podmíněného přístupu a sledovat dopad zásad pro daný časový rozsah, sadu aplikací a uživatelů. 
 
## <a name="next-steps"></a>Další kroky

[Konfigurace režimu pouze pro sestavu v zásadách podmíněného přístupu](howto-conditional-access-report-only.md)
