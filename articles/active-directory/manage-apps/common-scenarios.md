---
title: Běžné scénáře správy aplikací pro Službu Azure Active Directory | Dokumenty společnosti Microsoft
description: Centralizace správy aplikací pomocí Azure AD
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/02/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: d21ff820470765b82e397e56a2458603b8e5a7c7
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657946"
---
# <a name="centralize-application-management-with-azure-ad"></a>Centralizace správy aplikací pomocí Azure AD

Hesla, jak it noční můra, tak bolest pro zaměstnance po celém světě. To je důvod, proč se stále více společností obrací na Azure Active Directory, řešení Microsoft pro správu identit a přístupu pro cloud a všechny vaše další prostředky. Přejděte z aplikace do aplikace, aniž byste museli zadávat heslo pro každou z nich. Přejděte z Outlooku do Workday do ADP tak rychle, jak je můžete rychle a bezpečně otevřít. Pak spolupracujte s partnery a dokonce i s dalšími uživateli mimo vaši organizaci, aniž byste museli volat it. Azure AD navíc pomáhá řídit rizika tím, že zabezpečuje aplikace, které používáte s věcmi, jako je vícefaktorové ověřování, k ověření, kdo jste, pomocí neustále adaptivního strojového učení a inteligentního zabezpečení k detekci podezřelých přihlášení, která vám poskytují zabezpečený přístup k aplikacím, které potřebujete, ať jste kdekoli. Je to nejen skvělé pro uživatele, ale i pro IT. Díky kontrolám přístupu just-in-time a sadě zásad správného řízení v plném rozsahu vám Azure AD pomůže zůstat v souladu s předpisy a vynucovat zásady. A získat to, můžete dokonce automatizovat zřizování uživatelských účtů, takže správa přístupu hračkou. Podívejte se na některé běžné scénáře, pro které zákazník používá funkce správy aplikací služby Azure Active Directory.

**Obvyklé scénáře**


> [!div class="checklist"]
> * Přihlašování k vlastnímu nastavení pro všechny vaše aplikace
> * Automatizace zřizování a zrušení zřizování 
> * Zabezpečení aplikací
> * Řídit přístup k vašim aplikacím
> * Hybridní zabezpečený přístup

## <a name="scenario-1-set-up-sso-for-all-your-applications"></a>Scénář 1: Nastavení přihlašování k vlastnímu nastavení pro všechny aplikace

Už žádné správu hesla. Díky firemním přihlašovacím údajům můžete bezpečně přistupovat ke všem potřebným prostředkům. 

|Funkce  | Popis | Doporučení |
|---------|---------|---------|
|Jednotné přihlašování|Federované sondované služby SSO založené na standardech s využitím důvěryhodných oborových standardů.|Vždy použijte [SAML / OIDC](https://docs.microsoft.com/azure/active-directory/manage-apps/isv-choose-multi-tenant-federation) povolit přihlašovat, když vaše aplikace podporuje.|
|Přístupový panel|Nabídněte svým uživatelům jednoduché centrum pro zjišťování a přístup ke všem jejich aplikacím. Umožněte jim zvýšit produktivitu díky samoobslužným funkcím, jako je požadování přístupu k aplikacím a skupinám nebo správa přístupu k prostředkům jménem jiných uživatelů.| Nasaďte [přístupový panel](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-deployment-plan) ve vaší organizaci, jakmile integrujete své aplikace s Azure AD pro služby SSO.|

## <a name="scenario-2-automate-provisioning-and-deprovisioning"></a>Scénář 2: Automatizace zřizování a zrušení zřízení 


Většina aplikací vyžaduje, aby uživatel zřídit do aplikace před přístupem k prostředkům, které potřebují. Použití souborů CSV nebo složitých skriptů může být nákladné a těžko spravovatelné. Zákazníci navíc musí zajistit, aby byly účty odebrány, když už někdo neměl mít přístup. Využijte níže uvedené nástroje k automatizaci zřizování a zrušení zřizování. 


|Funkce  |Popis|Doporučení |
|---------|---------|---------|
|Zřizování SCIM|[SCIM](https://aka.ms/SICMOverview) je osvědčený postup pro automatizaci zřizování uživatelů. Všechny aplikace kompatibilní s SCIM lze integrovat s Azure AD. Automaticky vytvářejte, aktualizujte a odstraňujte uživatelské účty, aniž byste museli udržovat soubory CSV, vlastní skripty nebo řešení na před.|Podívejte se na rostoucí seznam [předem integrovaných](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) aplikací v galerii aplikací Azure AD|
|Microsoft Graph|Využijte dech a hloubku dat, které azure ad má obohatit vaši aplikaci s daty, která potřebuje.|Využijte [graf Microsoftu](https://developer.microsoft.com/graph/) k získání dat z celého ekosystému Microsoftu. |


## <a name="scenario-3-secure-your-applications"></a>Scénář 3: Zabezpečení aplikací
Identita je základní mačkání pro zabezpečení. Pokud bude identita kompromitována, je neuvěřitelně obtížné zastavit dominový efekt, než bude příliš pozdě. V průměru více než 100 dní, než organizace zjistí, že došlo ke kompromisu. Pomocí nástrojů poskytovaných službou Azure AD můžete zlepšit stav zabezpečení vašich aplikací. 

|Funkce  |Popis| Doporučení |
|---------|---------| ---------|
|Azure MFA|Azure Multi-Factor Authentication (MFA) je řešení dvoustupňového ověřování od Microsoftu. Pomocí metod ověřování schválených správcem azure mfa pomáhá chránit přístup k vašim datům a aplikacím a zároveň splňuje požadavky na jednoduchý proces přihlášení.| [Povolte vícefaktorové povolení](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124) pro uživatele.  |
|Podmíněný přístup|Pomocí podmíněného přístupu můžete implementovat automatická rozhodnutí o řízení přístupu pro to, kdo má přístup k vašim cloudovým aplikacím, a to na základě podmínek.| Zkontrolujte [výchozí hodnoty zabezpečení](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) a běžné [zásady,](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common) které zákazníci používají. | 
|Identity Protection|Ochrana identity využívá poznatky, které Microsoft získal ze své pozice v organizacích s Azure AD, spotřebitelského prostoru s účty Microsoft a hraní her s konzolí Xbox k ochraně vašich uživatelů. Společnost Microsoft analyzuje 6,5 bilionu signálů denně, aby identifikovala a ochránila zákazníky před hrozbami.|Povolte [výchozí zásady ochrany identity](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-policies) poskytované naší službou. | 

## <a name="scenario-4-govern-access-to-your-applications"></a>Scénář 4: Řídí přístup k vašim aplikacím
Zásady správného řízení identit pomáhá organizacím dosáhnout rovnováhy mezi produktivitou – Jak rychle může mít osoba přístup k aplikacím, které potřebuje, například když se připojí k mé organizaci? A bezpečnost - Jak by se měl jejich přístup v průběhu času měnit, například kvůli změnám v zaměstnaneckém statusu této osoby? 

|Funkce  |Popis|Doporučení |
|---------|---------| ---------|
|Elm|Správa nároků Azure AD může uživatelům ve vaší organizaci i mimo ni pomoci efektivněji spravovat přístup k jejich aplikacím.| Povolte správcům, kteří nejsou správci, spravovat přístup ke svým aplikacím pomocí [přístupových balíčků](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-first).|
|Kontroly přístupu|Přístup uživatelů k aplikacím lze pravidelně kontrolovat, aby bylo zajištěno, že budou mít i nadále přístup pouze ti praví lidé.| [Zkontrolujte přístup](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) k nejcitlivějším aplikacím. |
|Log Analytics|Vygenerujte sestavy o tom, kdo přistupuje ke kterým aplikacím, a uložte je do vybraného nástroje SIEM, abyste korelovali data mezi zdroji dat a v průběhu času.| Povolte [analýzu protokolů](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics) a nastavte výstrahy pro důležité události související s vašimi aplikacemi. |


## <a name="scenario-5-hybrid-secure-access"></a>Scénář 5: Hybridní zabezpečený přístup
Identita může být pouze vaše řídicí rovina, pokud může připojit vše v cloudu a místních aplikacích. Využijte nástroje poskytované Azure AD a jeho partnery k zabezpečení přístupu k aplikacím založeným na staršíverzi.

|Funkce  |Popis|Doporučení |
|---------|---------|---------|
|Proxy aplikací|Dnešní zaměstnanci chtějí být produktivní, ať jsou kdekoli, a to neustále a na jakémkoli zařízení. Potřebují přístup k aplikacím SaaS v cloudu a podnikových aplikacích v místním prostředí. Proxy aplikace Azure AD umožňuje tento robustní přístup bez nákladné a složité virtuální privátní sítě (VN) nebo demilitarizované zóny (DMZs).|Nastavte [vzdálený přístup](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) pro vaše aplikace před školním vysíláním. |
|F5, Akamai, Zscaler|Pomocí stávajícího síťového a doručovacího řadiče můžete snadno chránit starší aplikace, které jsou stále důležité pro vaše obchodní procesy, ale které jste dříve nemohli chránit pomocí Služby Azure AD. Je pravděpodobné, že již máte vše, co potřebujete k zahájení ochrany těchto aplikací.| Používáte Akamai, Citrix, F5 nebo Zscaler? Podívejte se na naše [předem vyrobená řešení](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access). | 

## <a name="related-articles"></a>Související články

- [Správa aplikací](https://docs.microsoft.com/azure/active-directory/manage-apps/index)
- [Zřizování aplikací](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)
- [Hybridní zabezpečený přístup]()
- [Zásady správného řízení identit](https://docs.microsoft.com/azure/active-directory/governance/identity-governance-overview)
- [Microsoft identity platform](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)
- [Zabezpečení identity](https://docs.microsoft.com/azure/active-directory/conditional-access/index)
