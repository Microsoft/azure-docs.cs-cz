---
title: Plány nasazení – Služba Azure Active Directory | Dokumenty společnosti Microsoft
description: Komplexní pokyny o nasazení mnoha funkcí služby Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4538afcef85c4a6eaef4213133963ecab9987e1f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876213"
---
# <a name="azure-active-directory-deployment-plans"></a>Plány nasazení Azure Active Directory
Hledáte komplexní pokyny k nasazení funkcí Služby Azure Active Directory (Azure AD)? Plány nasazení Azure AD vás provedou obchodní hodnotou, aspekty plánování a provozními postupy potřebnými k úspěšnému nasazení společných funkcí Azure AD.

Na kterékoli stránce plánu můžete pomocí funkce Tisk do PDF vytvořit aktuální offline verzi dokumentace.
## <a name="include-the-right-stakeholders"></a>Zahrňte správné zúčastněné strany

Při zahájení plánování nasazení pro nové funkce je důležité zahrnout klíčové zúčastněné strany v celé organizaci. Doporučujeme identifikovat a zdokumentovat osobu nebo osoby, které plní každou z následujících rolí, a pracovat s nimi na určení jejich zapojení do projektu.  

Role mohou zahrnovat následující 

|Role |Popis |
|-|-|
|Koncových uživatelů|Reprezentativní skupina uživatelů, pro které bude schopnost implementována. Často zobrazuje náhled změn v pilotním programu.
|Správce it podpory|Zástupce organizace podpory IT, který může poskytnout informace o podpoře této změny z hlediska helpdesku.  
|Architekt identity nebo globální správce Azure|Zástupce týmu pro správu identit, který má na starosti definování způsobu, jakým je tato změna zarovnána s hlavní infrastrukturou správy identit ve vaší organizaci.|
|Vlastník aplikace |Celkový vlastník podniku dotčených aplikací, které mohou zahrnovat správu přístupu.Může také poskytnout vstup na uživatelské zkušenosti a užitečnost této změny z pohledu koncového uživatele.
|Vlastník zabezpečení|Zástupce týmu zabezpečení, který může podepsat, že plán bude splňovat požadavky na zabezpečení vaší organizace.|
|Správce dodržování předpisů|Osoba ve vaší organizaci odpovědná za zajištění souladu s podnikovými, průmyslovými nebo vládními požadavky.|

**Míra zapojení může zahrnovat:**

- **R**esponsible pro realizaci plánu projektu a výsledku 

- **Pproval**plánu projektu a výsledku 

- **C**ontributor k plánu projektu a výsledku 

- **I**nformed plánu projektu a výsledek


## <a name="best-practices-for-a-pilot"></a>Osvědčené postupy pro pilota
Pilot vám umožní testovat s malou skupinou před zapnutím schopnosti pro každého. Ujistěte se, že v rámci testování je každý případ použití v rámci vaší organizace důkladně testován. Před zavedením této skupiny do vaší organizace jako celku je nejlepší cílit na určitou skupinu pilotních uživatelů.

V první vlně zacilte na IT, použitelnost a další vhodné uživatele, kteří mohou testovat a poskytovat zpětnou vazbu. Tato zpětná vazba by měla být použita k dalšímu rozvoji komunikace a pokynů, které odesíláte uživatelům, a k získání přehledu o typech problémů, které mohou vaši pracovníci podpory vidět. 

Rozšíření zavádění na větší skupiny uživatelů by mělo být provedeno zvýšením rozsahu cílové skupiny (skupiny). To lze provést prostřednictvím [dynamického členství ve skupině](../users-groups-roles/groups-dynamic-membership.md)nebo ručním přidáním uživatelů do cílových skupin.


## <a name="deploy-authentication"></a>Nasazení ověřování

| Schopnost | Popis|
| -| -|
| [Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)| Azure Multi-Factor Authentication (MFA) je řešení dvoustupňového ověřování od Microsoftu. Pomocí metod ověřování schválených správcem azure mfa pomáhá chránit přístup k vašim datům a aplikacím a zároveň splňuje požadavky na jednoduchý proces přihlášení. |
| [Podmíněný přístup](../conditional-access/plan-conditional-access.md)| Pomocí podmíněného přístupu můžete implementovat automatická rozhodnutí o řízení přístupu pro to, kdo má přístup k vašim cloudovým aplikacím, a to na základě podmínek. |
| [Samoobslužné resetování hesla](../authentication/howto-sspr-deployment.md)| Samoobslužné resetování hesla pomáhá uživatelům resetovat hesla bez zásahu správce, kdy a kde potřebují. |
| [Bez hesla](../authentication/howto-authentication-passwordless-deployment.md) | Implementace ověřování bez hesla pomocí aplikace Microsoft Authenticator nebo bezpečnostních klíčů FIDO2 ve vaší organizaci |

## <a name="deploy-application-management"></a>Nasazení správy aplikací

| Schopnost | Popis|
| -| - |
| [Jednotné přihlašování](../manage-apps/plan-sso-deployment.md)| Jednotné přihlašování pomáhá uživatelům získat přístup k aplikacím a prostředkům, které potřebují k podnikání, a to pouze jednou. Po přihlášení mohou přejít z Microsoft Office na SalesForce do boxu do interních aplikací, aniž by museli zadávat přihlašovací údaje podruhé. |
| [Přístupový panel](../manage-apps/access-panel-deployment-plan.md)| Nabídněte svým uživatelům jednoduché centrum pro zjišťování a přístup ke všem jejich aplikacím. Umožněte jim zvýšit produktivitu díky samoobslužným funkcím, jako je požadování přístupu k aplikacím a skupinám nebo správa přístupu k prostředkům jménem jiných uživatelů. |


## <a name="deploy-hybrid-scenarios"></a>Nasazení hybridních scénářů

| Schopnost | Popis|
| -| -|
| [ADFS pro synchronizaci hodnot hash hesel](../hybrid/plan-migrate-adfs-password-hash-sync.md)| Díky synchronizaci hodnot hash hesel se hashy uživatelských hesel synchronizují z místní služby Active Directory do služby Azure AD a umožňují ověřování uživatelů služby Azure AD bez interakce s místní službou Active Directory. |
| [ADFS pro předávací ověřování](../hybrid/plan-migrate-adfs-pass-through-authentication.md)| Předávací ověřování Azure AD pomáhá uživatelům přihlásit se k místním i cloudovým aplikacím pomocí stejných hesel. Tato funkce poskytuje uživatelům lepší prostředí – o jedno heslo méně k zapamatování – a snižuje náklady na it helpdesk, protože u uživatelů je méně pravděpodobné, že zapomenou, jak se přihlásit. Když se uživatelé přihlásí pomocí Azure AD, tato funkce ověří jejich hesla přímo v místní službě Active Directory. |
| [Proxy aplikací služby Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-deployment-plan.md) |Dnešní zaměstnanci chtějí být produktivní, ať jsou kdekoli, a to neustále a na jakémkoli zařízení. Potřebují přístup k aplikacím SaaS v cloudu a podnikových aplikacích v místním prostředí. Proxy aplikace Azure AD umožňuje tento robustní přístup bez nákladné a složité virtuální privátní sítě (VN) nebo demilitarizované zóny (DMZs). |
| [Bezproblémové jednotné přihlašování](../hybrid/how-to-connect-sso-quick-start.md)| Bezproblémové jednotné přihlašování Azure Active Directory (bezproblémové jednotné přihlašování Azure AD) automaticky přihlašuje uživatele, kteří zrovna používají svá podniková zařízení připojená k vaší podnikové síti. Pomocí této funkce uživatelé nebudou muset zadávat svá hesla, aby se přihlásili ke službě Azure AD a obvykle nebudou muset zadávat svá uživatelská jména. Tato funkce poskytuje oprávněným uživatelům snadný přístup k vašim cloudovým aplikacím bez nutnosti dalších místních komponent. |

## <a name="deploy-user-provisioning"></a>Nasazení zřizování uživatelů

| Schopnost | Popis|
| -| -|
| [Zřizování uživatelů](../app-provisioning/plan-auto-user-provisioning.md)| Azure AD pomáhá automatizovat vytváření, údržbu a odebírání identit uživatelů v cloudových aplikacích (SaaS), jako jsou Dropbox, Salesforce, ServiceNow a další. |
| [Zřizování uživatelů lidských zdrojů v cloudu](../app-provisioning/plan-cloud-hr-provision.md)| Zřizování uživatelů lidských zdrojů v cloudu do služby Active Directory vytváří základ pro průběžné zásady správného řízení identit a zvyšuje kvalitu podnikových procesů, které jsou závislé na autoritativních datech identity. Pomocí této funkce s vaším cloudovým hr produktem, jako je Workday nebo Successfactors, můžete bezproblémově spravovat životní cyklus identity zaměstnanců a podmíněných pracovníků konfigurací pravidel, která mapují procesy Joiner-Mover-Leaver (například New Hire, Terminate, Transfer) na akce zřizování IT (například Vytvořit, Povolit, Zakázat) |

## <a name="deploy-governance-and-reporting"></a>Nasazení zásad správného řízení a vytváření sestav

| Schopnost | Popis|
| -| -|
| [Privileged Identity Management](../privileged-identity-management/pim-deployment-plan.md)| Azure AD Privileged Identity Management (PIM) vám pomůže spravovat privilegované administrativní role napříč Azure AD, prostředky Azure a dalšími službami Microsoft Online Services. PIM poskytuje řešení, jako je přístup just-in-time, pracovní postupy schvalování žádostí a plně integrované kontroly přístupu, takže můžete identifikovat, odhalit a zabránit škodlivým aktivitám privilegovaných rolí v reálném čase. |
| [Podávání zpráv a monitorování](../reports-monitoring/plan-monitoring-and-reporting.md)| Návrh vašeho řešení vytváření sestav a monitorování Azure AD závisí na vašich právních, bezpečnostních a provozních požadavcích, stejně jako na vašem stávajícím prostředí a procesech. Tento článek představuje různé možnosti návrhu a provede vás správnou strategií nasazení. |
