---
title: Spravovat účty pro správu nouzovou přístup ve službě Azure AD | Dokumentace Microsoftu
description: Tento článek popisuje, jak pomáhají organizacím omezit privilegovaný přístup v rámci stávajícího prostředí Azure Active Directory pomocí účtů pro nouzový přístup.
services: active-directory
author: markwahl-msft
ms.author: billmath
ms.date: 12/13/2017
ms.topic: article-type-from-white-list
ms.service: active-directory
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.openlocfilehash: 4f3772abc1cdbd3b35b8b1f16e7a47c0f1a17783
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38595650"
---
# <a name="manage-emergency-access-administrative-accounts-in-azure-ad"></a>Spravovat účty pro správu nouzovou přístup ve službě Azure AD 

Pro většinu každodenních aktivit *globálního správce* oprávnění nejsou potřeba vašich uživatelů. Uživatelé by neměla být přiřazená trvale roli, protože můžou neúmyslně provádět úlohy, která vyžaduje vyšší oprávnění, než by měl mít. Když uživatelé nemusejí fungovat jako globální správce, jejich aktivovat přiřazení role pomocí Azure Active Directory (Azure AD) Privileged Identity Management (PIM), na svůj vlastní účet nebo alternativního účtu správce.

Kromě uživatelů s ohledem na přístupová práva pro správu sami, je nutné zabránit neúmyslnému uzamknuty správu vašeho tenanta Azure AD vzhledem k tomu, že jste přihlášení ani aktivace existující konkrétního uživatele účtu jako správce. Můžete zmírnit dopad zvyšuje ochranu před nechtěnými chybějící přístup pro správu uložením nejmíň dva *účtů pro nouzový přístup* ve vašem tenantovi.

Účty pro nouzový přístup může pomoct organizacím omezit privilegovaný přístup v rámci stávajícího prostředí Azure Active Directory. Tyto účty jsou vysoce privilegovaní a nejsou přiřazeny k jednotlivým uživatelům. Účty pro nouzový přístup jsou omezené na nouzové nebo "pohotovostní" scénáře situacích, kdy nejde použít normální účty pro správu. Organizace, musíte mít cíl omezení využití nouzový účtu jenom tento čas, během které je nezbytné.

Organizace může být nutné použít účet pro nouzový přístup v následujících situacích:

 - Uživatelské účty jsou federované a federace je momentálně není k dispozici z důvodu přerušení sítě buňky nebo kvůli výpadku zprostředkovatele identity. Například pokud má výpadek hostitele zprostředkovatele identity ve vašem prostředí, uživatelé pravděpodobně nejde se přihlásit, pokud Azure AD přesměruje na jeho poskytovatele identity. 
 - Správci, které jsou registrovány prostřednictvím ověřování Azure Multi-Factor Authentication, a jejich jednotlivých zařízení nejsou k dispozici. Uživatelé nemusí být schopen provést ověřování službou Multi-Factor Authentication pro aktivaci role. Například výpadek sítě buňky je jim zabránit v odpovídání na telefonní hovory nebo přijímat textové zprávy, pouze dvě ověřovací mechanismy, které jsou registrovány pro svoje zařízení. 
 - Osoba s nejnovější globálního přístupu pro správu opustil organizaci. Azure AD znemožňuje poslední *globálního správce* účet odstranit, ale nezabrání účet odstranit nebo zablokuje v místním. Buď situaci by mohlo způsobit nepoužitelnost nejde obnovit účet organizace.

## <a name="initial-configuration"></a>Počáteční konfigurace

Vytvoření dvou nebo více účtů pro nouzový přístup. Tady by měly být jen cloudové účty, které používají \*. onmicrosoft.com domény a že nejsou federované nebo synchronizované z místního prostředí. 

Účty by neměl být přidružen jednotlivé uživatele v organizaci. Organizace potřebují k zajištění, že přihlašovací údaje pro tyto účty jsou zabezpečené a známé pouze uživatelům, kteří mají oprávnění k jejich použití. 

> [!NOTE]
> Heslo účtu pro účet pro nouzový přístup je obvykle rozdělené na dvě nebo tři části, zapisují na samostatné části dokumentu a uložená v zabezpečené a chráněné sejfy, které jsou v zabezpečené a samostatné umístění. 
>
> Ujistěte se, že se všechny zadané zaměstnanec mobilní telefony nejsou připojené účty pro nouzový přístup, hardwarové tokeny této cesty pomocí jednotlivých zaměstnanců nebo jiné přihlašovací údaje specifické pro zaměstnance. Toto opatření pokrývá situace, kdy jednotlivé zaměstnance nedostupný když přihlašovací údaje, které je potřeba. 

### <a name="initial-configuration-with-permanent-assignments"></a>Počáteční konfigurace s trvalé přiřazení

Jednou z možností je, aby uživatele trvalé členy *globálního správce* role. Tato možnost může být vhodné pro organizace, které nemají předplatné Azure AD Premium P2.

Aby se snížilo riziko útoků výsledkem ohrožení zabezpečení hesla, Azure AD doporučuje vyžadovat Vícefaktorové ověřování pro všechny jednotlivé uživatele. Tato skupina by měla obsahovat správce a všechny ostatní uživatele (třeba finanční vedoucí pracovníci pověření ochranou) jejichž ohrožení bezpečnosti účtu by mít významný dopad. 

Nicméně pokud vaše organizace nemá žádné sdílené zařízení, ověřování službou Multi-Factor Authentication nemusí být možné pro tyto účty pro nouzový přístup. Pokud konfigurujete zásady podmíněného přístupu tak, aby vyžadovala [registracích vícefaktorového ověřování pro každý správce](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states) pro službu Azure AD a druhým připojeným software jako služba (SaaS) aplikací, možná budete muset nakonfigurovat zásady vyloučení vyloučit účtů pro nouzový přístup z tohoto požadavku.

### <a name="initial-configuration-with-approvals"></a>Počáteční konfigurace s schválení

Další možností je nakonfigurovat uživatele jako oprávněných a schvalovatele, které chcete aktivovat *globálního správce* role. Tato možnost vyžaduje vaše organizace má předplatné Azure AD Premium P2. Bude také vyžadovat možnost ověřování službou Multi-Factor Authentication, která je vhodná pro sdílené používání mezi více osob a síťové prostředí. Tyto požadavky nejsou, protože aktivace *globálního správce* rolí vyžaduje, aby uživatelé jste provedli dříve ověřování službou Multi-Factor Authentication. Další informace najdete v tématu [jak vyžadovat Vícefaktorové ověřování ve službě Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-how-to-require-mfa).

Nedoporučujeme používání ověřování Multi-Factor Authentication, který je spojen s osobní zařízení pro účty pro nouzový přístup. Ve stavu nouze skutečná osoba, která potřebuje přístup k zařízení zaregistrovaný u služby Multi-Factor Authentication nemusí být ten, který má vlastní zařízení. 

Zvažte také světě hrozeb. Například mohou nastat nepředvídatelné okolnosti jako jsou přírodní katastrofě nouzový, během které mobilním telefonu nebo jiné sítě možná nebude k dispozici. Je důležité zajistit, že všechna registrovaná zařízení zůstanou ve známých a zabezpečené umístění, které má více prostředky pro komunikaci s Azure AD.

## <a name="ongoing-monitoring"></a>Průběžné monitorování

Monitorování [přihlášení Azure AD a auditování protokoluje](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins) pro jakékoli přihlášení a auditovat jejich aktivitu z nouzovou přístupové účty. Obvykle tyto účty by neměly být přihlášení a by neměl být provádění změn, takže z nich je pravděpodobně neobvyklé a bude vyžadovat bezpečnostní šetření.

## <a name="account-check-validation-must-occur-at-regular-intervals"></a>Ověření kontrolu účtu se musí vyskytovat v pravidelných intervalech

Pokud chcete ověřit účet, proveďte následující kroky minimálně:
- Každých 90 dní.
- Pokud došlo ke změnám v pracovníky, jako je například změna úlohy, výjimka nebo nové zařazení.
- Pokud předplatné služby Azure AD v organizaci změnily.

K trénování zaměstnanci používat účty pro nouzový přístup, postupujte takto:

* Zajistěte, aby byly uvědomit, že je aktivita kontrolu účtu průběžné sledování zabezpečení pracovníků.
* Ověřte, že cloudové uživatelské účty můžete přihlásit a aktivovaly jejich role a, že uživatelé, kteří můžou potřebovat provést tyto kroky během nouze, jsou trénované na proces.
* Ujistěte se, že ještě nezaregistrovali ověřování službou Multi-Factor Authentication nebo samoobslužné resetování hesla (SSPR) na všech jednotlivých uživatelských zařízení nebo osobní údaje. 
* Pokud účty jsou registrované pro ověřování službou Multi-Factor Authentication na zařízení, pro použití při aktivaci role, ujistěte se, že zařízení je dostupná pro všechny správce, kteří možná muset použít během nouze. Dál ověřte, že zařízení je zaregistrované prostřednictvím alespoň dva mechanismy, které nesdílí společným režimem pro selhání. Například zařízení může komunikovat na Internetu prostřednictvím bezdrátové sítě zařízení a sítě poskytovatele buňky.
* Aktualizace přihlašovacích údajů účtu.

## <a name="next-steps"></a>Další postup
- [Přidání cloudových uživatelů](../fundamentals/add-users-azure-active-directory.md) a [přiřadit novému uživateli roli globálního správce](../fundamentals/active-directory-users-assign-role-azure-portal.md).
- [Registrace pro Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md), pokud jste ještě už zaregistrovali.
- [Vyžadovat ověřování Azure Multi-Factor Authentication pro jednotlivé uživatele přiřadit jako správce](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states).
- [Konfigurace dodatečnou ochranu pro globální správce v Office 365](https://support.office.com/article/Protect-your-Office-365-global-administrator-accounts-6b4ded77-ac8d-42ed-8606-c014fd947560), pokud používáte Office 365.
- [Provádění kontroly přístupu globální správci](../privileged-identity-management/pim-how-to-start-security-review.md) a [přechod existující globální správci pro více konkrétní role správce](directory-assign-admin-roles.md).


