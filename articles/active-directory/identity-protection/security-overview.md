---
title: Přehled zabezpečení služby Azure Active Directory (Azure AD) Identity Protection | Microsoft Docs
description: Přečtěte si, jak Přehled zabezpečení poskytuje přehled o stav zabezpečení vaší organizace.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 02/14/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: b894f7020083dd6ca46c394ec2930a3da36bc76d
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335203"
---
# <a name="azure-active-directory-identity-protection---security-overview"></a>Azure Active Directory Identity Protection – Přehled zabezpečení

[Přehled zabezpečení](https://aka.ms/IdentityProtectionRefresh) vám poskytne přehled o stav zabezpečení vaší organizace. Pomáhá identifikovat možné útoky a pochopit efektivitu vašich zásad.

Přehled zabezpečení je široce rozdělen do dvou částí:

- Trendy na levé straně poskytují časovou osu rizika ve vaší organizaci.
- Dlaždice na pravé straně, zvýrazněte klíčové problémy ve vaší organizaci a navrhněte, jak rychle provést akci.

![Přehled zabezpečení](./media/security-overview/01.png)
  
## <a name="trends"></a>Trendy

### <a name="new-risky-users-detected"></a>Zjistili se noví rizikoví uživatelé

Tento graf znázorňuje počet nových rizikových uživatelů zjištěných ve zvoleném časovém období. Můžete filtrovat zobrazení tohoto grafu podle úrovně rizika uživatele (nízká, střední, vysoká). Najeďte ukazatelem myši na přírůstek data UTC, abyste viděli počet rizikových uživatelů zjištěných pro daný den. Kliknutím na tento graf vás přinesete do sestavy rizikové uživatele. Chcete-li opravit uživatele, kteří jsou ohroženi, zvažte možnost změnit heslo.

### <a name="new-risky-sign-ins-detected"></a>Zjistila se nová riziková přihlášení

Tento graf znázorňuje počet rizikových přihlášení zjištěných ve zvoleném časovém období. Zobrazení tohoto grafu můžete filtrovat podle typu rizika přihlašování (v reálném čase nebo agregace) a úrovně rizika přihlašování (nízká, střední, vysoká). Nechráněná přihlášení jsou úspěšná přihlášení rizik v reálném čase, která neproběhla proti ověřování MFA. (Poznámka: Přihlášení, která jsou riziková, protože v reálném čase není možné v reálném čase chránit pomocí zásad pro rizikové přihlašování. Najeďte ukazatelem myši na přírůstek data UTC, abyste viděli počet přihlášení, která se v daném dni objevila v nebezpečí. Kliknutím na tento graf vás přinesete do sestavy rizikové přihlášení.

## <a name="tiles"></a>Dlaždice
 
### <a name="high-risk-users"></a>Uživatelé s vysokým rizikem

Na dlaždici uživatelé s vysokým rizikem se zobrazuje nejnovější počet uživatelů s vysokou pravděpodobností ohrožení identity. Ty by měly být nejvyšší prioritou pro šetření. Kliknutím na dlaždici s vysokým rizikovým uživatelům se přesměruje na filtrované zobrazení sestavy rizikové uživatele, která zobrazuje jenom uživatele s úrovní rizika vysoká. Pomocí této sestavy můžete získat další informace a opravit tyto uživatele s resetováním hesla.

![Přehled zabezpečení](./media/security-overview/02.png)

### <a name="medium-risk-users"></a>Uživatelé se středním rizikem
Dlaždice středně rizikové uživatele zobrazuje nejnovější počet uživatelů se střední pravděpodobností ohrožení identity. Kliknutím na dlaždici středně rizikové uživatele se přesměruje na filtrované zobrazení sestavy rizikové uživatele, která zobrazuje jenom uživatele, kteří mají úroveň rizika střední. Pomocí této sestavy můžete tyto uživatele dále prozkoumat a opravit.

### <a name="unprotected-risky-sign-ins"></a>Nechráněná riziková přihlášení

Dlaždice "nechráněná riziková přihlášení" zobrazuje počet úspěšných rizikových přihlášení v reálném čase, které se neblokovaly ani MFA nezdařily pomocí zásad podmíněného přístupu, zásad rizika ochrany identity nebo MFA pro uživatele. To může mít za následek ohrožení přihlášení, která byla úspěšná, a ne MFA. K ochraně takových přihlášení v budoucnu použijte zásady pro rizikové přihlašování. Kliknutím na dlaždici nechráněná riziková přihlášení se přesměruje na okno Konfigurace zásad rizik přihlašování, kde můžete nakonfigurovat zásady rizik přihlašování a vyžadovat MFA na přihlášení se zadanou úrovní rizika.

### <a name="legacy-authentication"></a>Starší verze ověřování

Na dlaždici starší verze ověřování se zobrazuje počet starších verzí ověřování ve vaší organizaci v posledním týdnu. Starší protokoly ověřování nepodporují moderní metody zabezpečení, jako je MFA. Pokud chcete zabránit staršímu ověřování, můžete použít zásady podmíněného přístupu. Kliknutím na dlaždici starší verze ověřování vás přesměruje na "bezpečné skóre identity".

### <a name="identity-secure-score"></a>Identity Secure Score

Skóre identity Secure měří a porovnává stav zabezpečení s oborovými vzory. Pokud kliknete na dlaždici "identity Security reskore (Preview)", přesměruje se na okno "bezpečné skóre identity (Preview)", kde si můžete přečíst další informace o vylepšení stav zabezpečení.

## <a name="next-steps"></a>Další postup

- [Kanál 9: Azure AD a zobrazení identity: Identity Protection ve verzi Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
- [Povolení Azure Active Directory Identity Protection](enable.md)
