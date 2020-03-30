---
title: Přehled zabezpečení Azure Active Directory Identity Protection
description: Zjistěte, jak vám přehled zabezpečení poskytuje přehled o stavu zabezpečení vaší organizace.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9550684ad154f28a02ee347fd0a79c1ec286beb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382188"
---
# <a name="azure-active-directory-identity-protection---security-overview"></a>Azure Active Directory Identity Protection – přehled zabezpečení

[Přehled zabezpečení](https://aka.ms/IdentityProtectionRefresh) na webu Azure Portal vám poskytne přehled o stavu zabezpečení vaší organizace. Pomáhá identifikovat potenciální útoky a pochopit účinnost vašich zásad.

Přehled zabezpečení je široce rozdělen do dvou částí:

- Trendy na levé straně poskytují časovou osu rizik ve vaší organizaci.
- Dlaždice vpravo zvýrazňují klíčové probíhající problémy ve vaší organizaci a navrhují, jak rychle provést akci.

![Přehled zabezpečení](./media/concept-identity-protection-security-overview/01.png)
  
## <a name="trends"></a>Trendy

### <a name="new-risky-users-detected"></a>Byli zjištěni noví rizikoví uživatelé

Tento graf zobrazuje počet nových rizikových uživatelů, kteří byli zjištěni během zvoleného časového období. Zobrazení tohoto grafu můžete filtrovat podle úrovně rizika uživatele (nízká, střední, vysoká). Najeďte nad přírůstky data UTC, abyste viděli počet rizikových uživatelů zjištěných pro tento den. Kliknutím na tento graf se dostanete do přehledu "Rizikoví uživatelé". Chcete-li napravit ohrožené uživatele, zvažte změnu hesla.

### <a name="new-risky-sign-ins-detected"></a>Byla zjištěna nová riziková přihlášení

Tento graf zobrazuje počet rizikových přihlášení zjištěných za zvolené časové období. Zobrazení tohoto grafu můžete filtrovat podle typu rizika přihlášení (v reálném čase nebo agregace) a úrovně rizika přihlášení (nízká, střední, vysoká). Nechráněné přihlášení jsou úspěšné přihlášení rizika v reálném čase, které nebyly zpochybněny vícefaktorové. (Poznámka: Přihlášení, která jsou riskantní z důvodu offline detekce nelze chránit v reálném čase pomocí zásad rizikpřihlášení). Najeďte nad přírůstky data UTC, abyste viděli počet přihlášení zjištěných v ohrožení pro tento den. Kliknutím na tento graf se dostanete do sestavy "Riskantní přihlášení".

## <a name="tiles"></a>Dlaždice
 
### <a name="high-risk-users"></a>Vysoce riziková uživatelé

Dlaždice Vysoce rizikových uživatelů zobrazuje nejnovější počet uživatelů s vysokou pravděpodobností ohrožení identity. Ty by měly být nejvyšší prioritou pro vyšetřování. Kliknutím na dlaždici "Vysoce riziková uživatelé" se přesměruje na filtrované zobrazení sestavy "Rizikoví uživatelé", která zobrazuje pouze uživatele s vysokou mírou rizika. Pomocí této sestavy se můžete dozvědět více a napravit tyto uživatele pomocí resetování hesla.

![Přehled zabezpečení](./media/concept-identity-protection-security-overview/02.png)

### <a name="medium-risk-users"></a>Středně rizikovění uživatelé
Dlaždice Středně rizikových uživatelů zobrazuje nejnovější počet uživatelů se střední pravděpodobností ohrožení identity. Kliknutím na dlaždici "Středně rizikovější uživatelé" se přesměruje na filtrované zobrazení sestavy "Rizikoví uživatelé", která zobrazuje pouze uživatele s úrovní rizika média. Pomocí této sestavy můžete tyto uživatele dále prozkoumat a napravit.

### <a name="unprotected-risky-sign-ins"></a>Nechráněná riskantní přihlášení

Dlaždice Nechráněné rizikové přihlášení zobrazuje počet úspěšných rizikových přihlášení v reálném čase za poslední týden, které nebyly blokovány nebo mfa zpochybněny zásadami podmíněného přístupu, zásadami rizika ochrany identity nebo vícefaktorové ověřování na uživatele. Jedná se o potenciálně ohrožené přihlášení, které byly úspěšné a nejsou zpochybněny vícefaktorové. Chcete-li tyto přihlašovací opatření v budoucnu chránit, použijte zásady rizika přihlášení. Kliknutím na dlaždici "Nechráněné rizikové přihlášení" přesměrujete na okno konfigurace zásad rizikpřihlášení, kde můžete nakonfigurovat zásady rizika přihlášení tak, aby vyžadovaly vícefaktorové zabezpečení při přihlášení se zadanou úrovní rizika.

### <a name="legacy-authentication"></a>Starší verze ověřování

Na dlaždici Starší verze ověřování se zobrazuje počet starších ověření ve vaší organizaci za poslední týden. Starší ověřovací protokoly nepodporují moderní metody zabezpečení, jako je například vícefaktorové ověřování. Chcete-li zabránit staršímu ověřování, můžete použít zásady podmíněného přístupu. Kliknutím na dlaždici "Starší verze ověřování" se přesměrujete na "Skóre zabezpečení identity".

### <a name="identity-secure-score"></a>Skóre zabezpečení identity

Skóre identity měří a porovnává stav zabezpečení s oborovými vzory. Pokud kliknete na dlaždici "Skóre identity (náhled)", přesměruje se na okno "Skóre identity", kde se můžete dozvědět více o zlepšení stavu zabezpečení.

## <a name="next-steps"></a>Další kroky

- [Co je riziko](concept-identity-protection-risks.md)

- [Dostupné zásady ke zmírnění rizik](concept-identity-protection-policies.md)
