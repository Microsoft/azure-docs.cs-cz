---
title: Běžné scénáře správy aplikací pro Azure Active Directory | Microsoft Docs
description: Centralizovaná správa aplikací pomocí Azure AD
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/02/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ce3819ff1f9b0c61f7738f90ff17c2798fe888b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "88642108"
---
# <a name="centralize-application-management-with-azure-ad"></a>Centralizovaná správa aplikací pomocí Azure AD

Hesla, jak Nightmare, tak i bolesti zaměstnanců po celém světě. To je důvod, proč čím více společností Azure Active Directory, tak řešení pro správu identit a přístupu od Microsoftu pro Cloud a všechny další prostředky. Přejít z aplikace do aplikace bez nutnosti zadávat heslo pro každý z nich. Přeskočte z Outlooku na Workday, abyste mohli co nejrychleji otevřít, a to rychle a bezpečně. Pak můžete spolupracovat s partnery a dokonce i ostatními mimo vaši organizaci, aniž byste je museli volat. Díky tomu Azure AD pomáhá spravovat rizika tím, že zajišťuje zabezpečení aplikací, které používáte, jako je Multi-Factor Authentication, a umožňuje tak pomocí nepřetržitě adaptivního strojového učení a zabezpečení zjišťovat podezřelá přihlášení, která vám umožní zabezpečený přístup k aplikacím, které potřebujete, bez ohledu na to, kde jste. Pro uživatele je to nejen Skvělé, ale také pro ně. Díky kontrolám přístupu za běhu a kompletní sadě zásad správného škálování vám Azure AD pomůže zůstat v dodržování předpisů a vymáhat zásady. Díky tomu můžete dokonce automatizovat zřizování uživatelských účtů a spravovat přístup k Breeze. Podívejte se na některé z běžných scénářů, které zákazník používá Azure Active Directory možností správy aplikací.

**Typické scénáře**


> [!div class="checklist"]
> * Jednotné přihlašování pro všechny vaše aplikace
> * Automatizace zřizování a rušení zřizování 
> * Zabezpečte své aplikace
> * Řízení přístupu k vašim aplikacím
> * Hybridní zabezpečený přístup

## <a name="scenario-1-set-up-sso-for-all-your-applications"></a>Scénář 1: nastavení jednotného přihlašování pro všechny vaše aplikace

Žádné další Správa hesla Zabezpečený přístup ke všem prostředkům, které potřebujete, pomocí podnikových přihlašovacích údajů. 

|Funkce  | Popis | Doporučení |
|---------|---------|---------|
|Jednotné přihlašování|Federované jednotné přihlašování založené na standardech pomocí důvěryhodných oborových standardů.|Vždy použijte [SAML/OIDC](https://docs.microsoft.com/azure/active-directory/manage-apps/isv-choose-multi-tenant-federation) k povolení jednotného přihlašování, když ji vaše aplikace podporuje.|
|Moje aplikace|Poskytněte uživatelům jednoduché centrum pro zjišťování a přístup ke všem jejich aplikacím. Umožněte jejich zvýšení produktivity díky funkcím samoobslužné služby, jako je vyžadování přístupu k aplikacím a skupinám nebo Správa přístupu k prostředkům jménem jiných uživatelů.| Po integraci vašich aplikací se službou Azure AD pro jednotné přihlašování můžete nasadit [Moje aplikace](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-deployment-plan) ve vaší organizaci.|

## <a name="scenario-2-automate-provisioning-and-deprovisioning"></a>Scénář 2: automatizace zřizování a rušení zřizování 


Většina aplikací vyžaduje, aby se uživatel před přístupem k potřebným prostředkům zřídil do aplikace. Správa pomocí souborů CSV nebo složitých skriptů může být náročná a těžká. Zákazníci navíc musí zajistit, aby se účty odebraly, pokud by k nim neměl přístup někdo. Pomocí níže uvedených nástrojů můžete automatizovat zřizování a rušení zřizování. 


|Funkce  |Popis|Doporučení |
|---------|---------|---------|
|Zřizování SCIM|[SCIM](https://aka.ms/SCIMOverview) je osvědčeným postupem automatizace zřizování uživatelů. Do Azure AD se dá integrovat jakákoli aplikace kompatibilní s SCIM. Automatické vytváření, aktualizace a odstraňování uživatelských účtů bez nutnosti uchovávat soubory CSV, vlastní skripty nebo Prem řešení.|Podívejte se na rostoucí seznam [předem integrovaných](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) aplikací v galerii aplikací Azure AD.|
|Microsoft Graph|Využijte Breath a hloubku dat, která Azure AD potřebuje k rozšíření vaší aplikace s daty, která potřebují.|Využijte [Microsoft Graph](https://developer.microsoft.com/graph/) k získávání dat z celého ekosystému Microsoftu. |


## <a name="scenario-3-secure-your-applications"></a>Scénář 3: zabezpečení aplikací
Identita je linchpin pro zabezpečení. Pokud dojde k ohrožení identity, neuvěřitelně se obtížně zastaví dominový efekt předtím, než bude příliš pozdě. V průměru po 100 dnech předá organizace, že došlo k ohrožení zabezpečení. Pomocí nástrojů poskytovaných službou Azure AD můžete zlepšit stav zabezpečení vašich aplikací. 

|Funkce  |Popis| Doporučení |
|---------|---------| ---------|
|Azure MFA|Azure Multi-Factor Authentication (MFA) je řešení dvoustupňového ověřování od Microsoftu. Pomocí metod ověřování schválených správcem Azure MFA pomáhá chránit přístup k vašim datům a aplikacím a současně splňuje požadavky na jednoduchý proces přihlašování.| [Povolte vícefaktorové ověřování](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124) pro vaše uživatele.  |
|Podmíněný přístup|Pomocí podmíněného přístupu můžete implementovat automatizované rozhodnutí řízení přístupu, která budou mít přístup k vašim cloudovým aplikacím, a to na základě podmínek.| Projděte si [výchozí hodnoty zabezpečení](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) a [běžné zásady](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common) používané zákazníky. | 
|Identity Protection|Služba Identity Protection používá studijní materiály, které Microsoft získal od svého umístění v organizacích s Azure AD, uživatelským prostorem s účty Microsoft a na hraní her s Xbox k ochraně vašich uživatelů. Microsoft analyzuje 6 500 000 000 000 signálů za den k identifikaci a ochraně zákazníků před hrozbami.|Povolte [výchozí zásady ochrany identit](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-policies) poskytované naší službou. | 

## <a name="scenario-4-govern-access-to-your-applications"></a>Scénář 4: řízení přístupu k vašim aplikacím
Řízení identit pomáhá organizacím dosáhnout rovnováhy mezi produktivitou – jak rychle může osoba získat přístup k aplikacím, které potřebují, například když se připojí k mojí organizaci? A zabezpečení – jak se má v průběhu času měnit přístup, například kvůli změnám stavu zaměstnanosti této osoby? 

|Funkce  |Popis|Doporučení |
|---------|---------| ---------|
|ELM|Správa nároků Azure AD může uživatelům pomáhat i mimo organizaci efektivněji spravovat přístup ke svým aplikacím.| Umožněte nesprávcům spravovat přístup k jejich aplikacím pomocí [přístupových balíčků](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-first).|
|Kontroly přístupu|Přístup uživatelů k aplikacím se může pravidelně kontrolovat, aby se zajistilo, že budou mít přístup jenom přípravní lidé.| [Zkontrolujte přístup](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) k vašim citlivým aplikacím. |
|Log Analytics|Generování sestav o tom, kdo přistupují k aplikacím a ukládají je do nástroje SIEM, podle vlastního výběru, aby korelují data mezi zdroji dat a v průběhu času.| Povolte [Log Analytics](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics) a nastavte výstrahy pro kritické události, které souvisí s vašimi aplikacemi. |


## <a name="scenario-5-hybrid-secure-access"></a>Scénář 5: hybridní zabezpečený přístup
Identita může být pouze vaší řídicí rovinou, pokud může propojit vše napříč cloudem a místními aplikacemi. Využijte nástroje poskytované službou Azure AD a jejími partnery k zabezpečení přístupu k aplikacím založeným na starší verzi ověřování.

|Funkce  |Popis|Doporučení |
|---------|---------|---------|
|Proxy aplikací|Dnešní zaměstnanci chtějí být produktivní, ať jsou kdekoli, a to neustále a na jakémkoli zařízení. Potřebují přístup k SaaS aplikacím v cloudu a místních aplikacích. Proxy aplikace služby Azure AD umožňuje tento robustní přístup bez nákladných a složitých virtuálních privátních sítí (zóny DMZ) nebo zón demilitarizovaná ().|Nastavte [vzdálený přístup](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) pro aplikace Prem. |
|F5, Akamai, Zscaler|Pomocí stávajícího síťového adaptéru a řadiče pro doručování můžete snadno chránit starší verze aplikací, které jsou pro vaše obchodní procesy pořád důležité, ale nemůžete je chránit před službou Azure AD. Je možné, že už máte všechno, co potřebujete, abyste mohli začít chránit tyto aplikace.| Používáte Akamai, Citrix, F5 nebo Zscaler? Projděte si naše [předem připravená řešení](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access). | 

## <a name="related-articles"></a>Související články

- [Správa aplikací](https://docs.microsoft.com/azure/active-directory/manage-apps/index)
- [Zřizování aplikací](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)
- [Hybridní zabezpečený přístup](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access)
- [Zásady správného řízení identity](https://docs.microsoft.com/azure/active-directory/governance/identity-governance-overview)
- [Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)
- [Zabezpečení identity](https://docs.microsoft.com/azure/active-directory/conditional-access/index)
