---
title: Plány nasazení – Azure Active Directory | Microsoft Docs
description: Kompletní pokyny, jak nasadit mnoho možností Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 069cf348bb6d0432d02a8350bc0847c2b155f90a
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498077"
---
# <a name="azure-active-directory-deployment-plans"></a>Plány nasazení Azure Active Directory
Hledáte kompletní pokyny k nasazení funkcí Azure Active Directory (Azure AD)? Plány nasazení Azure AD vás seznámí s obchodní hodnotou, požadavky na plánování a provozními postupy potřebnými k úspěšnému nasazení běžných funkcí služby Azure AD.

Na kterékoli stránce plánu použijte možnost Tisk v prohlížeči do formátu PDF a vytvořte aktuální offline verzi dokumentace.


## <a name="deploy-authentication"></a>Nasazení ověřování

| Schopnost | Description|
| -| -|
| [Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)| Azure AD Multi-Factor Authentication (MFA) je řešení ověřování dvou kroků od Microsoftu. Pomocí metod ověřování schválených správcem Azure AD MFA pomáhá chránit přístup k vašim datům a aplikacím a současně splňuje požadavky na jednoduchý proces přihlašování. |
| [Podmíněný přístup](../conditional-access/plan-conditional-access.md)| Pomocí podmíněného přístupu můžete implementovat automatizované rozhodnutí řízení přístupu, která budou mít přístup k vašim cloudovým aplikacím, a to na základě podmínek. |
| [Samoobslužné resetování hesla](../authentication/howto-sspr-deployment.md)| Samoobslužné resetování hesla pomáhá uživatelům resetovat hesla bez zásahu správce, kdy a kde potřebují. |
| [Bez hesla](../authentication/howto-authentication-passwordless-deployment.md) | Implementace ověřování pomocí hesla pomocí Microsoft Authenticator aplikace nebo klíčů zabezpečení FIDO2 ve vaší organizaci |

## <a name="deploy-application-and-device-management"></a>Nasazení správy aplikací a zařízení

| Schopnost | Description|
| -| - |
| [Jednotné přihlašování](../manage-apps/plan-sso-deployment.md)| Jednotné přihlašování pomáhá uživatelům přistupovat k aplikacím a prostředkům, které potřebují k podnikání, a současně se přihlašuje. Až se přihlásí, můžou přejít z systém Microsoft Office do služby SalesForce do interních aplikací, aniž by se museli zadávat přihlašovací údaje podruhé. |
| [Přístupový panel](../manage-apps/access-panel-deployment-plan.md)| Poskytněte uživatelům jednoduché centrum pro zjišťování a přístup ke všem jejich aplikacím. Umožněte jejich zvýšení produktivity díky funkcím samoobslužné služby, jako je vyžadování přístupu k aplikacím a skupinám nebo Správa přístupu k prostředkům jménem jiných uživatelů. |
| [Zařízení](../devices/plan-device-deployment.md) | Tento článek vám pomůže vyhodnotit metody pro integraci zařízení s Azure AD, zvolit plán implementace a poskytuje klíčové odkazy na podporované nástroje pro správu zařízení. |


## <a name="deploy-hybrid-scenarios"></a>Nasazení hybridních scénářů

| Schopnost | Description|
| -| -|
| [ADFS pro synchronizaci hodnot hash hesel](../hybrid/plan-migrate-adfs-password-hash-sync.md)| Díky synchronizaci hodnot hash hesel jsou hash hesla uživatelů synchronizovaná z místní služby Active Directory do Azure AD a umožňují tak uživatelům ověřování Azure AD bez interakce s místní službou Active Directory. |
| [ADFS pro předávací ověřování](../hybrid/plan-migrate-adfs-pass-through-authentication.md)| Předávací ověřování Azure AD pomáhá uživatelům přihlašovat se pomocí stejných hesel k místním i cloudovým aplikacím. Tato funkce poskytuje uživatelům lepší možnosti – jedno méně než jedno heslo, které si zapamatuje a snižuje náklady na helpdesk, protože uživatelé mají méně pravděpodobný způsob, jak se přihlásit. Když se uživatelé přihlásí pomocí Azure AD, tato funkce ověří jejich hesla přímo v místní službě Active Directory. |
| [Proxy aplikací služby Azure AD](../manage-apps/application-proxy-deployment-plan.md) |Dnešní zaměstnanci chtějí být produktivní, ať jsou kdekoli, a to neustále a na jakémkoli zařízení. Potřebují přístup k SaaS aplikacím v cloudu a místních aplikacích. Proxy aplikace služby Azure AD umožňuje tento robustní přístup bez nákladných a složitých virtuálních privátních sítí (zóny DMZ) nebo zón demilitarizovaná (). |
| [Bezproblémové jednotné přihlašování](../hybrid/how-to-connect-sso-quick-start.md)| Bezproblémové jednotné přihlašování Azure Active Directory (bezproblémové jednotné přihlašování Azure AD) automaticky přihlašuje uživatele, kteří zrovna používají svá podniková zařízení připojená k vaší podnikové síti. S touto funkcí uživatelé nebudou muset zadávat hesla pro přihlášení ke službě Azure AD a obvykle nemusejí zadávat svá uživatelská jména. Tato funkce poskytuje autorizovaným uživatelům, kteří mají snadný přístup k vašim cloudovým aplikacím bez nutnosti dalších místních komponent. |

## <a name="deploy-user-provisioning"></a>Nasazení zřizování uživatelů

| Schopnost | Description|
| -| -|
| [Zřizování uživatelů](../app-provisioning/plan-auto-user-provisioning.md)| Azure AD pomáhá automatizovat vytváření, údržbu a odebírání identit uživatelů v cloudových aplikacích (SaaS), jako jsou Dropbox, Salesforce, ServiceNow a další. |
| [Zřizování uživatelů pro cloudový personál](../app-provisioning/plan-cloud-hr-provision.md)| Zřizování uživatelů v cloudu pro službu Active Directory vytváří základ pro průběžné řízení identit a vylepšuje kvalitu obchodních procesů, které spoléhají na data autoritativní identity. Pomocí této funkce s vaším produktem cloudového HR, jako je například Workday nebo SuccessFactors, můžete bez problémů spravovat životní cyklus identit zaměstnanců a podmíněných pracovníků tím, že nakonfigurujete pravidla, která mapují procesy připojujících se stěhovací společnosti (například nové přijetí, ukončení a přenos) na akce zřizování IT (například vytvořit, povolit, zakázat). |

## <a name="deploy-governance-and-reporting"></a>Nasazení zásad správného řízení a vytváření sestav

| Schopnost | Description|
| -| -|
| [Privileged Identity Management](../privileged-identity-management/pim-deployment-plan.md)| Azure AD Privileged Identity Management (PIM) pomáhá spravovat privilegované administrativní role napříč službami Azure AD, prostředky Azure a dalšími online službami Microsoftu. PIM poskytuje řešení, jako je přístup za běhu, pracovní postupy schvalování žádostí a plně integrované recenze přístupu, abyste mohli identifikovat, odhalit a zabránit škodlivým aktivitám privilegovaných rolí v reálném čase. |
| [Vytváření sestav a monitorování](../reports-monitoring/plan-monitoring-and-reporting.md)| Návrh řešení pro vytváření sestav a monitorování Azure AD závisí na vašich právních a provozních požadavcích a na vašich stávajících prostředích a procesech. Tento článek nabízí různé možnosti návrhu a provede vás se správnou strategií nasazení. |
| [Kontroly přístupu](../governance/deploy-access-reviews.md) | Kontroly přístupu jsou důležitou součástí vaší strategie zásad správného řízení, která vám umožní znát a spravovat, kdo má přístup, a k čemu mají přístup. Tento článek vám pomůže s plánováním a nasazením kontrol přístupu, abyste dosáhli požadovaného postures zabezpečení a spolupráce. |

## <a name="include-the-right-stakeholders"></a>Zahrnutí správných zúčastněných stran

Při zahájení plánování nasazení nové funkce je důležité zahrnout klíčové účastníky v rámci vaší organizace. Doporučujeme identifikovat a zdokumentovat osobu nebo lidi, kteří splňují jednotlivé role, a pracovat s nimi k určení jejich zapojení do projektu.  

Role můžou zahrnovat následující: 

|Role |Popis |
|-|-|
|Koncový uživatel|Zástupce skupiny uživatelů, pro které bude tato funkce implementována. Aplikace často zobrazuje náhled změn v pilotním programu.
|Správce podpory IT|Podporuje zástupce organizace, který poskytuje vstup o podpoře této změny z perspektivy technické podpory.  
|Architekt identity nebo globální správce Azure|Zástupce týmu správy identit se účtuje podle toho, jak se tato změna rovná s infrastrukturou správy základní identity ve vaší organizaci.|
|Vlastník firemních aplikací |Celkový obchodní vlastník ovlivněných aplikací, které můžou zahrnovat správu přístupu.Může také poskytnout vstup na uživatelské prostředí a užitečnost této změny z perspektivy koncového uživatele.
|Vlastník zabezpečení|Zástupce z bezpečnostního týmu, který se může odhlásit, že plán bude splňovat požadavky na zabezpečení vaší organizace.|
|Správce dodržování předpisů|Osoba v rámci vaší organizace odpovědná za zajištění souladu s podnikovými, průmyslovými nebo vládními požadavky.|

**Úrovně zapojení můžou zahrnovat:**

- Esponsible **R** pro implementaci plánu a výsledku projektu 

- **Pproval plánu** a výsledku projektu 

- Ontributor plánování a výsledek projektu **C** 

- **Nformed plán** a výsledek projektu


## <a name="best-practices-for-a-pilot"></a>Osvědčené postupy pro pilotní nasazení
Pilotní nasazení vám umožní testovat malou skupinu před zapnutím funkce pro každého. Ujistěte se, že jako součást testování je každý případ použití v rámci vaší organizace důkladně testován. Je vhodné cílit na konkrétní skupinu pilotních uživatelů, než to zavedete do vaší organizace jako celek.

V první vlny, cílení na IT, použitelnost a další vhodné uživatele, kteří můžou testovat a poskytovat zpětnou vazbu. Tato zpětná vazba by se měla použít k dalšímu vývoji komunikace a pokynů, které odesíláte uživatelům, a k získání přehledu o typech problémů, které mohou pracovníci podpory vidět. 

Rozšiřování zavedení na větší skupiny uživatelů by mělo být provedeno zvýšením rozsahu cílových skupin. To se dá udělat prostřednictvím [členství v dynamické skupině](../enterprise-users/groups-dynamic-membership.md)nebo ručním přidáním uživatelů do cílových skupin.
