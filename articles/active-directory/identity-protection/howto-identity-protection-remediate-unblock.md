---
title: Náprava rizik a odblokování uživatelů v azure ad identity protection
description: Seznamte se s možnostmi, které máte k dispozici pro detekci aktivních rizik.
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
ms.openlocfilehash: 350e7b37d36be70cea345db52cdfb639b2f1c1a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382098"
---
# <a name="remediate-risks-and-unblock-users"></a>Náprava rizik a odblokování uživatelů

Po dokončení [šetření](howto-identity-protection-investigate-risk.md)budete chtít podniknout kroky k nápravě rizika nebo odblokování uživatelů. Organizace mají také možnost povolit automatickou nápravu pomocí svých [rizikových zásad](howto-identity-protection-configure-risk-policies.md). Organizace by se měly pokusit ukončit všechna zjišťování rizik, která jsou prezentována v časovém období, ve které je vaše organizace v pořádku. Společnost Microsoft doporučuje uzavírat události co nejdříve, protože při práci s rizikem záleží na čase.

## <a name="remediation"></a>Odstranění rizika

Všechna aktivní detekce rizik přispívají k výpočtu hodnoty nazývané úroveň rizika uživatele. Úroveň rizika uživatele je indikátor (nízká, střední, vysoká) pro pravděpodobnost, že účet byl ohrožen. Jako správce chcete uzavřet všechna zjišťování rizik, aby ovlivnění uživatelé již nejsou ohroženi.

Některá zjištění rizik mohou být označena ochranou identity jako "Uzavřeno (systém)", protože události již nebyly určeny jako rizikové.

Správci mají následující možnosti nápravy:

- Sebenáprava s politikou rizik
- Ruční resetování hesla
- Odmítnutí rizika uživatele
- Ruční uzavření jednotlivých detekcí rizik

### <a name="self-remediation-with-risk-policy"></a>Sebenáprava s politikou rizik

Pokud uživatelům povolíte vlastní nápravu pomocí Azure Multi-Factor Authentication (MFA) a samoobslužného resetování hesla (SSPR) ve vašich zásadách rizik, můžou se odblokovat, když je zjištěno riziko. Tyto detekce jsou pak považovány za uzavřené. Uživatelé musí mít dříve registrované pro Azure MFA a SSPR, aby bylo možné použít při zjištění rizika.

Některá zjišťování nemusí zvýšit riziko na úroveň, kde by bylo vyžadováno samoobslužné nápravy uživatele, ale správci by měli tato zjišťování stále vyhodnocovat. Správci mohou určit, že jsou nezbytná další opatření, jako je [blokování přístupu z umístění](../conditional-access/howto-conditional-access-policy-location.md) nebo snížení přijatelného rizika ve svých zásadách.

### <a name="manual-password-reset"></a>Ruční resetování hesla

Pokud není možné vyžadovat resetování hesla pomocí zásad rizika uživatele, mohou správci ukončit všechny detekce rizik pro uživatele ručním resetováním hesla.

Správci mají při resetování hesla pro své uživatele dvě možnosti:

- **Generovat dočasné heslo** – Generováním dočasného hesla můžete okamžitě vrátit identitu zpět do bezpečného stavu. Tato metoda vyžaduje kontaktování postižených uživatelů, protože potřebují vědět, co je dočasné heslo. Vzhledem k tomu, že heslo je dočasné, je uživatel vyzván ke změně hesla na něco nového během dalšího přihlášení.

- **Vyžadovat, aby uživatel resetoval heslo** – Vyžadování uživatelů k resetování hesel umožňuje samoobnovení bez kontaktování technické podpory nebo správce. Tato metoda platí pouze pro uživatele, kteří jsou registrováni pro Azure MFA a SSPR. Pro uživatele, kteří nebyli zaregistrováni, tato možnost není k dispozici.

### <a name="dismiss-user-risk"></a>Odmítnutí rizika uživatele

Pokud pro vás není možné obnovit heslo, protože například uživatel byl odstraněn, můžete zrušit detekci rizik uživatelů.

Po klepnutí na tlačítko **Zavřít riziko uživatele**budou všechny události uzavřeny a postižený uživatel již není ohrožen. Však vzhledem k tomu, že tato metoda nemá vliv na existující heslo, nepřináší související identitu zpět do bezpečného stavu. 

### <a name="close-individual-risk-detections-manually"></a>Ruční uzavření jednotlivých detekcí rizik

Jednotlivé detekce rizik můžete uzavřít ručně. Ručním zavřením detekce rizik můžete snížit úroveň rizika uživatele. Detekce rizik jsou obvykle uzavřeny ručně v reakci na související šetření. Například při rozhovoru s uživatelem odhalí, že aktivní detekce rizik a už není vyžadována. 
 
Při ručním zavírání detekcí rizik můžete provést některou z následujících akcí ke změně stavu detekce rizik:

- Potvrdit ohrožení zabezpečení uživatele
- Odmítnutí rizika uživatele
- Potvrzení bezpečného přihlášení
- Potvrzení ohrožení zabezpečení přihlášení

## <a name="unblocking-users"></a>Odblokování uživatelů

Správce se může rozhodnout zablokovat přihlášení na základě svých zásad rizik nebo šetření. Blok může dojít na základě rizika přihlášení nebo uživatele.

### <a name="unblocking-based-on-user-risk"></a>Odblokování na základě rizika uživatele

Chcete-li odblokovat účet blokovaný z důvodu rizika uživatele, mají správci následující možnosti:

1. **Resetovat heslo** – můžete obnovit heslo uživatele.
1. **Odmítnout riziko uživatele** – Zásady rizika uživatele blokují uživatele, pokud bylo dosaženo nakonfigurované úrovně rizika uživatele pro blokování přístupu. Úroveň rizika uživatele můžete snížit zrušením rizika uživatele nebo ručním zavřením hlášených detekcí rizik.
1. **Vyloučit uživatele ze zásad** – Pokud se domníváte, že aktuální konfigurace zásad přihlášení způsobuje problémy konkrétním uživatelům, můžete z něj vyloučit uživatele. Další informace naleznete v části Vyloučení v [článku: Konfigurace a povolení zásad rizik](howto-identity-protection-configure-risk-policies.md#exclusions).
1. **Zakázat zásady** – Pokud se domníváte, že konfigurace zásad způsobuje problémy všem uživatelům, můžete zásadu zakázat. Další informace naleznete v [článku: Konfigurace a povolení zásad rizik](howto-identity-protection-configure-risk-policies.md).

### <a name="unblocking-based-on-sign-in-risk"></a>Odblokování na základě rizika přihlášení

Chcete-li odblokovat účet na základě rizika přihlášení, mají správci následující možnosti:

1. **Přihlásit se ze známého umístění nebo zařízení** – Běžným důvodem blokovaných podezřelých přihlášení jsou pokusy o přihlášení z neznámých míst nebo zařízení. Uživatelé mohou rychle zjistit, zda je tento důvod příčinou blokování, a to tak, že se pokusí přihlásit ze známého umístění nebo zařízení.
1. **Vyloučit uživatele ze zásad** – Pokud se domníváte, že aktuální konfigurace zásad přihlášení způsobuje problémy konkrétním uživatelům, můžete z něj vyloučit uživatele. Další informace naleznete v části Vyloučení v [článku: Konfigurace a povolení zásad rizik](howto-identity-protection-configure-risk-policies.md#exclusions).
1. **Zakázat zásady** – Pokud se domníváte, že konfigurace zásad způsobuje problémy všem uživatelům, můžete zásadu zakázat. Další informace naleznete v [článku: Konfigurace a povolení zásad rizik](howto-identity-protection-configure-risk-policies.md).

## <a name="next-steps"></a>Další kroky

Pokud chcete získat přehled o azure ad identity ochrany, najdete v tématu [Azure AD Identity Protection přehled](overview-identity-protection.md).
