---
title: Spravovat účty pro správu přístupu nouze ve službě Azure AD | Microsoft Docs
description: Tento článek popisuje, jak použít účty pro nouzový přístup umožňující organizacím omezit privilegovaný přístup v rámci stávajícího prostředí Azure Active Directory.
services: active-directory
author: markwahl-msft
ms.author: billmath
ms.date: 12/13/2017
ms.topic: article-type-from-white-list
ms.service: active-directory
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.openlocfilehash: 5c256befba4c73527b529c953f7b9cd4d266450c
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="manage-emergency-access-administrative-accounts-in-azure-ad"></a>Spravovat účty pro správu přístupu nouze ve službě Azure AD 

Pro většinu každodenní aktivit *globálního správce* vaši uživatelé nejsou potřebné oprávnění. Uživatelé by neměl trvale přiřadit role, protože se může nechtěně provést úlohu, která vyžaduje vyšší oprávnění, než by měly mít. Když uživatelé nepotřebují tak, aby fungoval jako globální správce, jejich by měl pomocí Azure Active Directory (Azure AD) Privileged Identity Management (PIM), na svůj vlastní účet nebo účet správce alternativní aktivovat přiřazení role.

Kromě uživatelů s ohledem na přístupová práva pro správu sami, je třeba zabránit, aby se nechtěně uzamčen mimo správy klienta služby Azure AD, protože nelze přihlásit ani aktivovat účet existující konkrétního uživatele jako správce. Můžete zmírnit dopad nechtěnému nedostatečná přístup pro správu uložením dvou nebo více *účty pro nouzový přístup* ve vašem klientovi.

Účty pro nouzový přístup pomáhá organizacím omezit privilegovaný přístup v rámci stávajícího prostředí Azure Active Directory. Tyto účty jsou vysoce privilegované a nejsou přiřazeny k konkrétní osoby. Účty pro nouzový přístup je omezen na nouzové nebo 'pohotovostní' scénářů situacích, kdy nelze použít normální účty pro správu. Organizace musí zachovat cílem omezit používání nouzový účtů pouze tento čas, během které je nezbytné.

Organizace může být nutné použít účet nouzový přístup v následujících situacích:

 - Uživatelské účty jsou federovaný a federation není momentálně k dispozici z důvodu zalomení buňky sítě nebo výpadek zprostředkovatele identity. Například pokud hostitel poskytovatele identity ve vašem prostředí je mimo provoz, uživatelé možná nejde se přihlásit, pokud Azure AD přesměruje na jejich zprostředkovatele identity. 
 - Správci, které jsou registrovány prostřednictvím Azure Multi-Factor Authentication a všechny jejich jednotlivá zařízení nejsou k dispozici. Uživatelé nemusí být schopen dokončit vícefaktorového ověřování pro aktivaci role. Například výpadku sítě buňky brání je v odpovědi na telefonní hovory nebo přijímat textové zprávy, jenom dva ověřovací mechanismy, které jsou zaregistrované pro jejich zařízení. 
 - Uživatel s nejnovější globální správce přístup opustil organizaci. Azure AD znemožňuje poslední *globálního správce* účet odstranit, ale nezabrání účet před odstraněním nebo zablokuje v místním. Buď situaci provést nemůže provést zotavení účet organizace.

## <a name="initial-configuration"></a>Počáteční konfigurace

Vytvořte dva nebo více účtů nouzový přístup. Tady by měly být jen cloudové účty, které používají \*. onmicrosoft.com domény a že nejsou federovaný nebo synchronizováni z místního prostředí. 

Účty by neměl být přidružen jednotlivé uživatele v organizaci. Organizace potřebují k zajištění přihlašovací údaje pro tyto účty jsou zabezpečené a známé pouze pro jednotlivce, kteří jsou autorizovaní k jejich použití. 

> [!NOTE]
> Heslo k účtu pro účet nouzový přístup je obvykle rozdělené na dvě nebo tři části, zapisují na samostatné části dokumentu a uložené v zabezpečené, chráněné trezory, které jsou v zabezpečené, samostatné umístění. 
>
> Ujistěte se, že vaše účty pro nouzový přístup nejsou propojená s všechny zadané zaměstnanec mobilní telefony, hardwarové tokeny, že cesta se jednotlivé zaměstnanci nebo jiné přihlašovací údaje specifické pro zaměstnance. Toto opatření popisuje případech, kdy není zaměstnanec jednotlivých nedostupný potřeby přihlašovací údaje. 

### <a name="initial-configuration-with-permanent-assignments"></a>Počáteční konfigurace s trvalé přiřazení

Jednou z možností je, aby uživatele členy *globálního správce* role. Tato možnost může být vhodné pro organizace, které nemají Azure AD Premium P2 odběry.

Aby se snížilo riziko útoku vyplývající z ohroženého heslo, Azure AD doporučuje vyžadovat Vícefaktorové ověřování pro všechny jednotlivé uživatele. Tato skupina by měla obsahovat správce a všechny ostatní uživatele (například finanční osoby) jejichž ohrožení bezpečnosti účtu by mít významný dopad. 

Ale pokud vaše organizace nemá sdílená zařízení, Multi-Factor Authentication nemusí být možné pro tyto účty pro nouzový přístup. Pokud konfigurujete zásady podmíněného přístupu tak, aby vyžadovala [registrace služby Multi-Factor Authentication pro každé správce](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states) pro Azure AD a druhý software jako služba (SaaS) aplikace, možná budete muset nakonfigurovat zásady vyloučení vyloučit účty pro nouzový přístup z tohoto požadavku.

### <a name="initial-configuration-with-approvals"></a>Počáteční konfigurace s schválení

Další možností je nakonfigurovat uživatele jako způsobilých a schvalovatele aktivovat *globálního správce* role. Tato možnost by vyžadovaly vaše organizace má Azure AD Premium P2 odběry. Bude vyžadovat také možnost vícefaktorového ověřování, který je vhodný pro použití sdílené mezi více osob a síťové prostředí. Tyto požadavky jsou, protože aktivace *globálního správce* role vyžaduje, aby uživatelé provedli dříve Multi-Factor Authentication. Další informace najdete v tématu [jak vyžadovat Vícefaktorové ověřování v Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-how-to-require-mfa).

Nedoporučujeme používat službu Multi-Factor Authentication, který je spojen s osobní zařízení pro účty pro nouzový přístup. V případě nouze skutečná osoba, která potřebuje přístup k zařízení registrované služby Multi-Factor Authentication nemusí být k tomu, který má osobní zařízení. 

Zvažte také povahu hrozeb. Například může dojít nepředvídatelné okolnosti, například přírodní katastrofě nouzový, během které mobilního telefonu nebo jiné sítě může být k dispozici. Je důležité zajistit, že jsou všechny registrované zařízení nacházet v známé a bezpečné umístění, které má více prostředky pro komunikaci se službou Azure AD.

## <a name="ongoing-monitoring"></a>Průběžné monitorování

Monitorování [přihlášení k Azure AD a auditu v protokolech](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins) pro všechny přihlášení a audit aktivity z nouzové přístupové účty. Tyto účty za normálních okolností by neměl být přihlášení a nesmí být provádění změn, tak z nich je pravděpodobně být neobvyklé a vyžadují zabezpečení šetření.

## <a name="account-check-validation-must-occur-at-regular-intervals"></a>Ověření kontrolu účtu se musí vyskytovat v pravidelných intervalech

K ověření účtu, proveďte následující kroky minimálně:
- Každých 90 dní.
- Pokud došlo ke změnám v pracovníky IT, jako je například změna úlohy, odeslání nebo nové přijetím.
- Pokud se změnily předplatná Azure AD v organizaci.

Při školení zaměstnance, aby použít účty pro nouzový přístup, postupujte takto:

* Zajistěte, aby pracovníci sledování zabezpečení uvědomit, že je probíhající aktivity kontrolu účtu.
* Ověření, cloudu uživatelské účty můžete přihlásit a aktivace jejich rolí a že uživatelé, kteří může být nutné provést tyto kroky nouzová probíhá trénink na procesu.
* Zajistěte, aby nebyly zaregistrovány ověřování Multi-Factor Authentication nebo samoobslužné resetování hesla (SSPR) na všechny jednotlivé uživatele zařízení nebo osobní údaje. 
* Pokud tyto účty jsou registrované pro službu Multi-Factor Authentication na zařízení, pro použití při aktivaci role, zajistěte, aby byl zařízení přístupné pro všechny správce, kteří může být nutné k použití při nouze. Také ověřte, že je zařízení zaregistrované prostřednictvím alespoň dva mechanismů, které nesdílí běžné režimu selhání. Zařízení může například komunikovat k Internetu prostřednictvím bezdrátové sítě do zařízení a síť buňky zprostředkovatele.
* Aktualizujte přihlašovací údaje účtu.

## <a name="next-steps"></a>Další postup
- [Přidání cloudových uživatelů](add-users-azure-active-directory.md) a [nového uživatele přiřadit roli globálního správce](active-directory-users-assign-role-azure-portal.md).
- [Registrace pro Azure Active Directory Premium](active-directory-get-started-premium.md), pokud nemáte již registraci.
- [Vyžadovat ověřování Azure Multi-Factor Authentication pro jednotlivé uživatele přiřazené jako správci](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states).
- [Konfiguraci lepší ochranu globálních správců v Office 365](https://support.office.com/article/Protect-your-Office-365-global-administrator-accounts-6b4ded77-ac8d-42ed-8606-c014fd947560), pokud používáte Office 365.
- [Provést kontrolu přístupu globální správci](active-directory-privileged-identity-management-how-to-start-security-review.md) a [přechod existující globální správci pro více konkrétní role správce](active-directory-assign-admin-roles-azure-portal.md).


