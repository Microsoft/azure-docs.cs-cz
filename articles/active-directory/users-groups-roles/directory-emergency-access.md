---
title: Správa účtů pro nouzový přístup ve službě Azure AD | Dokumentace Microsoftu
description: Tento článek popisuje, jak pomocí účtů pro nouzový přístup můžete zabránit neúmyslnému zamknutí mimo vašeho tenanta Azure Active Directory (Azure AD).
services: active-directory
author: markwahl-msft
ms.author: curtand
ms.date: 12/21/2018
ms.topic: article-type-from-white-list
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 974e00ce877dcf3b15dc7ce6d73f7d1331e20bb5
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429117"
---
# <a name="manage-emergency-access-accounts-in-azure-ad"></a>Správa účtů pro nouzový přístup ve službě Azure AD

Je důležité, abyste zabránili se neúmyslně zamknutí mimo vašeho tenanta Azure Active Directory (Azure AD), protože nemůže přihlásit nebo aktivovat účet existující konkrétního uživatele jako správce. Můžete zmírnit dopad zvyšuje ochranu před nechtěnými chybějící přístup pro správu tak, že vytvoříte dva nebo více *účtů pro nouzový přístup* ve vašem tenantovi.

Jsou vysoce privilegované účty pro nouzový přístup a nejsou přiřazeny k jednotlivým uživatelům. Účty pro nouzový přístup jsou omezené na nouzové nebo "pohotovostní" scénáře, kdy nejde použít normální účty pro správu. Organizace, musíte mít cíl omezení využití nouzový účtu pouze situace, kdy je nezbytně nutné.

Tento článek obsahuje pokyny pro správu účtů pro nouzový přístup ve službě Azure AD.

## <a name="when-would-you-use-an-emergency-access-account"></a>Když použijete účet pro nouzový přístup?

Organizace může být nutné použít účet pro nouzový přístup v následujících situacích:

- Uživatelské účty jsou federované a federace je momentálně není k dispozici z důvodu přerušení sítě buňky nebo kvůli výpadku zprostředkovatele identity. Například pokud má výpadek hostitele zprostředkovatele identity ve vašem prostředí, uživatelé pravděpodobně nejde se přihlásit, pokud Azure AD přesměruje na jeho poskytovatele identity.
- Správci, které jsou registrovány prostřednictvím ověřování Azure Multi-Factor Authentication a jejich jednotlivých zařízení nejsou k dispozici nebo je tato služba není k dispozici. Uživatelé nemusí být schopen provést ověřování službou Multi-Factor Authentication pro aktivaci role. Například výpadek sítě buňky je jim zabránit v odpovídání na telefonní hovory nebo přijímat textové zprávy, pouze dvě ověřovací mechanismy, které jsou registrovány pro svoje zařízení.
- Osoba s přístupem nejnovější globální správce opustil organizaci. Azure AD brání odstranit poslední účet globálního správce, ale nezabrání účet odstranit nebo zablokuje v místním. Buď situaci by mohlo způsobit nepoužitelnost nejde obnovit účet organizace.
- Nepředvídatelné okolnosti – třeba přírodní katastrofě nouzový, během kterých může být mobilní telefon nebo jiné sítě není k dispozici. 

## <a name="create-two-cloud-based-emergency-access-accounts"></a>Vytvořit dva účty pro nouzový přístup založené na cloudu

Vytvoření dvou nebo více účtů pro nouzový přístup. Tyto účty by měly být jen cloudové účty, které používají \*. onmicrosoft.com domény a že nejsou federované nebo synchronizované z místního prostředí.

Při konfiguraci těchto účtů, musí být splněny následující požadavky:

- Účty pro nouzový přístup by neměl být přidružen jednotlivé uživatele v organizaci. Ujistěte se, že vaše účty nejsou spojeny s všechny zadané zaměstnanec mobilní telefony, hardwarové tokeny této cesty pomocí jednotlivých zaměstnanců nebo jiné přihlašovací údaje specifické pro zaměstnance. Toto opatření pokrývá situace, kdy jednotlivé zaměstnance nedostupný když přihlašovací údaje, které je potřeba. Je důležité zajistit, že všechna registrovaná zařízení zůstanou ve známých a zabezpečené umístění, které má více prostředky pro komunikaci s Azure AD.
- Mechanismus ověřování, který se používá pro účet pro nouzový přístup by měl být odlišný od, který používá vaše ostatní účty pro správu, včetně jiných účtů pro nouzový přístup.  Například pokud vaše přihlášení normální správce je prostřednictvím MFA v místním, pak Azure MFA by jiným způsobem.  Nicméně pokud Azure MFA je vaše primární součástí ověřování pro vaše účty pro správu, zvažte jiný přístup pro tyto prvky, jako je třeba použití podmíněného přístupu u poskytovatele MFA třetích stran.
- Zařízení nebo přihlašovacích údajů nesmí vypršení platnosti nebo být v rozsahu automatické vyčištění z důvodu nedostatku použití.  
- Přiřazení role globálního správce by měl trvalé pro účty pro nouzový přístup. 


### <a name="exclude-at-least-one-account-from-phone-based-multi-factor-authentication"></a>Vyloučit alespoň jeden účet ověřování aplikací Multi-Factor Authentication založené na telefonu

Aby se snížilo riziko útoků výsledkem ohrožení zabezpečení hesla, Azure AD doporučuje vyžadovat vícefaktorové ověřování pro všechny jednotlivé uživatele. Tato skupina obsahuje správce a všechny ostatní uživatele (třeba finanční vedoucí pracovníci pověření ochranou) jejichž ohrožení bezpečnosti účtu by mít významný dopad.

Ale alespoň jeden z vašich účtů pro nouzový přístup by neměl mít stejný mechanismus ověřování službou Multi-Factor Authentication jako další účty bez nouze. To zahrnuje řešení třetí strany služby Multi-Factor authentication. Pokud máte zásadu podmíněného přístupu, která vyžaduje [ověřování službou Multi-Factor Authentication pro každý správce](../authentication/howto-mfa-userstates.md) pro službu Azure AD a jiných připojených software jako služba (SaaS) aplikací, měli byste vyloučit účtů pro nouzový přístup z tohoto požadavek a místo něj nakonfigurovat jiným způsobem. Kromě toho je dobré mít že účty nemají zásadu na uživatele služby Multi-Factor authentication.

### <a name="exclude-at-least-one-account-from-conditional-access-policies"></a>Alespoň jeden účet vyloučit ze zásad podmíněného přístupu

Během nouze které nechcete zásadu, která by mohly blokovat přístup k vyřešení problému. Účet pro nouzový přístup aspoň jeden by se měly vyloučit ze všech zásad podmíněného přístupu. Pokud jste povolili [základní zásady](../conditional-access/baseline-protection.md), má být vyloučena účtů pro nouzový přístup.

## <a name="additional-guidance-for-hybrid-customers"></a>Další pokyny pro zákazníky využívající hybridní řešení

Další možnost pro organizace, které používají AD Domain Services a služby AD FS nebo podobně jako zprostředkovatele identity pro vytvoření federace služby Azure AD, je nakonfigurovat účet pro nouzový přístup, jejíž deklaraci identity MFA může třeba dodat ze zprostředkovatele identity.  Například účet pro nouzový přístup by byl zálohovaný dvojici certifikátu a klíče jako jsou například uložené na čipovou kartu.  Při ověření tohoto uživatele do AD služby AD FS může poskytovat deklarace identity do služby Azure AD označující, že uživatel splňuje požadavky na vícefaktorové ověřování.  Dokonce i s tímto přístupem organizace však musí mít účtů pro nouzový přístup založené na cloudu v případě, že federační nelze navázat. 

## <a name="store-devices-and-credentials-in-a-safe-location"></a>Store zařízení a přihlašovacích údajů na bezpečném místě

Organizace potřebují k zajištění, zda jsou pověření pro účty pro nouzový přístup uchovány zabezpečené a známé pouze uživatelům, kteří mají oprávnění k jejich použití. Zákazníci, kteří použít čipovou kartu a ostatní používat hesla. Heslo pro účet pro nouzový přístup je obvykle rozdělené na dvě nebo tři části, zapisují na samostatné části dokumentu a uložená v zabezpečené a chráněné sejfy, které jsou v zabezpečené a samostatné umístění.

Pokud pomocí hesla, ujistěte se, že účty mít silná hesla, které nevyprší platnost hesla. Hesla v ideálním případě by měl být alespoň 16 znaků dlouhé a náhodně generované.


## <a name="monitor-sign-in-and-audit-logs"></a>Monitorování přihlášení a protokoly auditu

Monitorování [přihlášení Azure AD a auditování protokoluje](../reports-monitoring/concept-sign-ins.md) pro jakékoli přihlášení a auditovat jejich aktivitu z účtů pro nouzový přístup. Za normálních okolností tyto účty by neměly být přihlášení a by neměl být provádění změn, takže z nich je pravděpodobně neobvyklé a bude vyžadovat bezpečnostní šetření.

## <a name="validate-accounts-at-regular-intervals"></a>Ověření účtů v pravidelných intervalech

K trénování zaměstnanci používat účty pro nouzový přístup a ověření účtů pro nouzový přístup, proveďte následující kroky minimální v pravidelných intervalech:

- Zajistěte, aby byly uvědomit, že je aktivita kontrolu účtu průběžné sledování zabezpečení pracovníků.
- Zajistěte, aby byl proces skla nouzový přerušení tyto účty používat zdokumentované a aktuální.
- Ujistěte se, že správci a zabezpečení pracovníci, kteří můžou potřebovat provést tyto kroky během nouze, jsou trénované na proces.
- Aktualizujte přihlašovací údaje účtu, zejména všechna hesla pro účty pro nouzový přístup a ověřte, že účty pro nouzový přístup můžou přihlásit a provádět úlohy správy.
- Ujistěte se, že uživatelé se ještě nezaregistrovali ověřování službou Multi-Factor Authentication nebo samoobslužné resetování hesla (SSPR) na všech jednotlivých uživatelských zařízení nebo osobní údaje. 
- Pokud účty jsou registrované pro ověřování službou Multi-Factor Authentication na zařízení, pro použití při aktivaci přihlášení nebo role, ujistěte se, že zařízení je dostupná pro všechny správce, kteří možná muset použít během nouze. Dál ověřte, jestli zařízení může komunikovat prostřednictvím alespoň dva síťové cesty, které nesdílí společným režimem pro selhání. Například zařízení může komunikovat na Internetu prostřednictvím bezdrátové sítě zařízení a sítě poskytovatele buňky.

Tyto kroky musí provést v pravidelných intervalech a klíče změny:

- Nejméně každých 90 dní
- Pokud došlo ke změnám v pracovníky, jako je například změna úlohy, výjimka nebo nové zařazení
- Pokud jste změnili předplatná Azure AD v organizaci

## <a name="next-steps"></a>Další postup

- [Zabezpečení privilegovaného přístupu pro hybridní a cloudová nasazení v Azure AD](directory-admin-roles-secure.md)
- [Přidat uživatele, kteří používají Azure AD](../fundamentals/add-users-azure-active-directory.md) a [přiřadit novému uživateli roli globálního správce](../fundamentals/active-directory-users-assign-role-azure-portal.md)
- [Registrace Azure AD Premium](../fundamentals/active-directory-get-started-premium.md), pokud jste se ještě nezaregistrovali již
- [Jak vyžadovat dvoustupňové ověřování pro uživatele](../authentication/howto-mfa-userstates.md)
- [Konfigurace dodatečnou ochranu pro globální správce v Office 365](https://docs.microsoft.com/office365/enterprise/protect-your-global-administrator-accounts), pokud používáte Office 365
- [Zahájení kontroly přístupu globální správci](../privileged-identity-management/pim-how-to-start-security-review.md) a [přechod existující globálními správci, konkrétnější rolí správce](directory-assign-admin-roles.md)
