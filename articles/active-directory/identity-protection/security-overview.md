---
title: Přehled zabezpečení služby Azure Active Directory (Azure AD) Identity Protection | Dokumentace Microsoftu
description: Zjistěte, jak Přehled zabezpečení poskytuje přehled o stavu zabezpečení vaší organizace.
services: active-directory
keywords: Azure active directory identity protection, zjišťování cloudových aplikací, Správa aplikací, zabezpečení, rizika, úroveň rizika, ohrožení zabezpečení, zásady zabezpečení
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.component: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: a16c1cbc4549568cfdeb2312511fa4cb3564dceb
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54913762"
---
# <a name="azure-active-directory-identity-protection-security-overview"></a>Přehled zabezpečení služby Azure Active Directory Identity Protection

Přehled zabezpečení poskytuje přehled o stavu zabezpečení vaší organizace. Pomáhá identifikovat potenciální útoky a pochopit účinnost zásad.

Přehled zabezpečení je široce rozdělený do dvou částí:

- Trendy, na levé straně, zadejte časovou osu rizik ve vaší organizaci.

- Dlaždice na pravé straně, zvýrazněte klíčové aktuální problémy ve vaší organizaci a navrhnout jak rychle reagovat.


![Přehled zabezpečení](./media/security-overview/01.png)
  
## <a name="trends"></a>Trendy

### <a name="new-risky-users-detected"></a>Zjistili se noví rizikoví uživatelé

Tento graf zobrazuje počet nové rizikové uživatele, které byly zjištěny v průběhu zvoleném časovém období. Můžete filtrovat zobrazení tohoto grafu podle uživatele úrovně rizika (nízké, střední, vysoká). Najeďte myší přírůstky datum UTC zobrazíte počet rizikových uživatelů pro daný den zjištěna. Klikněte na tento graf přejdete na sestavu "Rizikový uživatel". Oprava uživatelů, kteří jsou na riziko, zvažte možnost změnit své heslo.

### <a name="new-risky-sign-ins-detected"></a>Zjistila se nová riziková přihlášení

Tento graf zobrazuje počet rizikových přihlášení zjištěnými za zvoleném časovém období. Můžete filtrovat zobrazení tohoto grafu podle rizika přihlašování typu (v reálném čase nebo agregace) a úroveň rizika přihlášení (nízká, střední, vysoká). Nechráněné přihlášení jsou úspěšné v reálném čase rizika přihlášení, které nebylo probíhá ověřovací test MFA. (Poznámka: Sign-INS, které bývají offline detekce za riziková nedá chránit v: zásady rizik přihlašování v reálném čase). Najeďte myší přírůstky datum UTC zobrazíte počet přihlášení zjistil riziko pro daný den. Klikněte na tento graf přejdete na sestavu "Rizikových přihlášení".

## <a name="tiles"></a>Dlaždice
 
### <a name="high-risk-users"></a>Uživatelé s vysokým rizikem

Dlaždice 'uživatelů s vysokým rizikem' zobrazuje nejnovější počet uživatelů s velkou pravděpodobností identita ohrozit zabezpečení. Tady by měly být nejvyšší prioritu pro šetření. Kliknutím na dlaždici 'uživatelů s vysokým rizikem' bude přesměrována na filtrované zobrazení "Rizikoví uživatelé" sestavu zobrazující pouze uživatelé s vysokou úroveň rizika. Pomocí této sestavy, můžete další informace a oprava těchto uživatelů s resetováním hesla.

![Přehled zabezpečení](./media/security-overview/02.png)


### <a name="medium-risk-users"></a>Uživatelé se středním rizikem
Na dlaždici 'střední riziko uživatele' zobrazují nejnovější počtu uživatelů s střední pravděpodobnosti ohrožení identity. Kliknutím na dlaždici 'střední riziko uživatele' bude přesměrována na filtrované zobrazení "Rizikoví uživatelé" sestavu zobrazující pouze uživatelé s úrovní rizika média. Pomocí této sestavy můžete dále prozkoumat a oprava těchto uživatelů.

### <a name="unprotected-risky-sign-ins"></a>Nechráněná riziková přihlášení

Dlaždice 'Nechráněné rizikových přihlášení' zobrazuje počet za poslední týden úspěšné, v reálném čase rizikových přihlášení, které byly ani blokované ani MFA vystaven zásady podmíněného přístupu, Identity Protection rizikem zásad nebo na uživatele MFA. Jedná se o potenciálně ohroženými přihlašovací údaje, které proběhly úspěšně a probíhá ověřovací test MFA není. K ochraně těchto přihlášení v budoucnosti, používejte zásady rizik přihlašování. Kliknutím na dlaždici "Nechráněné rizikových přihlášení" přesměruje do okna Konfigurace zásady rizik přihlašování ve kterém můžete nakonfigurovat zásady rizik přihlašování k vynucení vícefaktorového ověřování na Přihlaste se pomocí úroveň rizika zadané.


### <a name="legacy-authentication"></a>Starší verze ověřování

Dlaždice 'Starší verze ověřování' Zobrazí počet za poslední týden starší verze ověřování ve vaší organizaci. Protokoly pro ověřování na starší verze nepodporují metody moderní zabezpečení, jako jsou MFA. Pokud chcete zabránit starší verze ověřování, můžete použít zásady podmíněného přístupu. Kliknutím na dlaždici "Starší verze ověřování" verze vás přesměrují na "Identity zabezpečení skóre".


### <a name="identity-secure-score"></a>Skóre zabezpečení identit

Skóre zabezpečení Identity opatření a porovnává lepšímu zabezpečení vzorů odvětví. Pokud kliknete na dlaždici "Identita zabezpečení skóre (Preview)", přesměruje se do okna "Identity zabezpečení skóre (Preview)' Pokud podrobnější informace o stavu zabezpečení.


## <a name="next-steps"></a>Další postup

- [Kanál 9: Azure AD a Identity zobrazit: Identity Protection ve verzi Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

- [Povolení služby Azure Active Directory Identity Protection](enable.md)

