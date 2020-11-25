---
title: Co je nového? Poznámky k verzi – Azure Active Directory | Microsoft Docs
description: Zjistěte, co je nového v Azure Active Directory; například nejnovější poznámky k verzi, známé problémy, opravy chyb, zastaralé funkce a nadcházející změny.
services: active-directory
author: ajburnle
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71726724e7c018f34b1175f323d0c8e55b604931
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95973628"
---
# <a name="whats-new-in-azure-active-directory"></a>Co je nového v Azure Active Directory?

>Přečtěte si informace o tom, kdy se má tato stránka na aktualizace znovu navštívit zkopírováním a vložením této adresy URL: `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` do čtečky informačního kanálu ![ RSS ](./media/whats-new/feed-icon-16x16.png) .

Služba Azure AD průběžně přijímá vylepšení. V tomto článku najdete informace o tom, jak se můžete seznámit s nejnovějším vývojem.

- Nejnovější verze
- Známé problémy
- Opravy chyb
- Zastaralé funkce
- Plánuje změny

Tato stránka se aktualizuje měsíčně, takže ji můžete pravidelně znovu navštěvovat. Pokud hledáte položky starší než šest měsíců, najdete je v archivu, abyste mohli [novinky v Azure Active Directory](whats-new-archive.md).

---
## <a name="october-2020"></a>Říjen 2020

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-tls-certificate-changes"></a>Místní hybridní agenti Azure AD ovlivněné změnami certifikátu Azure TLS

**Zadejte:** Plánování změn  
**Kategorie služby:** NENÍ K DISPOZICI  
**Schopnost produktu:** Platformy

Microsoft aktualizuje služby Azure tak, aby používaly certifikáty TLS z jiné sady kořenových certifikačních autorit (CAs). Tato aktualizace je způsobena aktuálními certifikáty certifikační autority, které nejsou v souladu s jedním z požadavků na základní hodnoty fóra pro certifikační autoritu nebo v prohlížeči. Tato změna ovlivní hybridní agenty Azure AD nainstalovanou místně, které mají posílená prostředí s pevným seznamem kořenových certifikátů a budou se muset aktualizovat, aby důvěřovala novým vystavitelům certifikátů.

Tato změna způsobí přerušení služby, Pokud neprovedete okamžitou akci. Mezi tyto agenty patří [konektory proxy aplikací](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) pro vzdálený přístup do místního prostředí, předávací agenty pro [ověřování](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) , které umožňují vašim uživatelům přihlašovat se k aplikacím pomocí stejných hesel a agentům [verze Preview pro zřizování cloudu](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) , kteří provádějí AD synchronizaci služby Azure AD. 

Pokud máte prostředí s pravidly brány firewall nastavené tak, aby umožňovalo odchozí volání jenom na určitý seznam odvolaných certifikátů (CRL), budete muset použít následující seznam CRL a adresy URL protokolu OCSP. Úplné podrobnosti o změně a adresách zabezpečení seznamu CRL a protokolu OCSP pro povolení přístupu k nástroji najdete v tématu  [změny certifikátu Azure TLS](../../security/fundamentals/tls-certificate-changes.md).

---

### <a name="provisioning-events-will-be-removed-from-audit-logs-and-published-solely-to-provisioning-logs"></a>Události zřizování se z protokolů auditu odeberou a publikují se výhradně do protokolů zřizování.

**Zadejte:** Plánování změn  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav
 
Aktivita [služby SCIM Provisioning](../app-provisioning/user-provisioning.md) je zaznamenána v protokolech auditu i v protokolech zřizování. Patří sem aktivity, jako je vytvoření uživatele v ServiceNow, skupina v GSuite nebo import role z AWS. V budoucnu budou tyto události publikovány pouze v protokolech zřizování. Tato změna se provádí, aby nedocházelo k duplicitním událostem v protokolech a dodatečné náklady vzniklé zákazníky, které zabírají protokoly v Log Analytics. 

Až se datum dokončí, poskytneme aktualizaci. Tato zastaralost není plánována pro kalendářní rok 2020. 

> [!NOTE]
> To nemá vliv na žádné události v protokolech auditu mimo události synchronizace emitované službou zřizování. Události, jako je vytvoření aplikace, zásada podmíněného přístupu, uživatel v adresáři atd. budou nadále vydávány v protokolech auditu. [Přečtěte si další informace](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs?context=azure/active-directory/app-provisioning/context/app-provisioning-context).
 

---

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-transport-layer-security-tls-certificate-changes"></a>Místní hybridní agenti Azure AD ovlivněné změnami certifikátu TLS (Azure Transport Layer Security)

**Zadejte:** Plánování změn  
**Kategorie služby:** NENÍ K DISPOZICI  
**Schopnost produktu:** Platformy
 
Microsoft aktualizuje služby Azure tak, aby používaly certifikáty TLS z jiné sady kořenových certifikačních autorit (CAs). Aktualizace se projeví v důsledku aktuálních certifikátů certifikační autority, které nenásledují po jednom z požadavků na základní hodnoty fóra pro certifikační autoritu nebo v prohlížeči. Tato změna ovlivní hybridní agenty Azure AD nainstalovanou místně, které mají posílená prostředí s pevným seznamem kořenových certifikátů. Tyto agenty bude nutné aktualizovat, aby důvěřovali novým vystavitelům certifikátů.

Tato změna způsobí přerušení služby, Pokud neprovedete okamžitou akci. Mezi tyto agenty patří: 
- [Konektory proxy aplikací](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) pro vzdálený přístup k místnímu přístupu 
- [Předávací](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) agenti pro ověřování umožňující uživatelům přihlašovat se k aplikacím pomocí stejných hesel
- Agenti ve [verzi Preview pro zřizování cloudu](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) , kteří synchronizují služby Azure AD. 

Pokud máte prostředí s pravidly brány firewall nastavené tak, aby umožňovalo odchozí volání jenom na konkrétní stažení seznamu odvolaných certifikátů (CRL), budete muset použít seznam CRL a adresy URL protokolu OCSP. Úplné podrobnosti o změně a adresách zabezpečení seznamu CRL a protokolu OCSP pro povolení přístupu k nástroji najdete v tématu  [změny certifikátu Azure TLS](../../security/fundamentals/tls-certificate-changes.md).
 
---

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud"></a>Azure Active Directory TLS 1,0, TLS 1,1 a šifrování 3DES v US Gov cloudu

**Zadejte:** Plánování změn  
**Kategorie služby:** NENÍ K DISPOZICI  
**Schopnost produktu:** Zvyšování
 
Azure Active Directory zařadí následující protokoly do 31. března 2021:
- TLS 1.0
- TLS 1.1
- šifrovací sada 3DES (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Všechny kombinace klient-server a prohlížeč-Server by měly používat protokol TLS 1,2 a moderní šifrovací sady k údržbě zabezpečeného připojení k Azure Active Directory pro Azure, Office 365 a služby Microsoft 365.

Ovlivněná prostředí jsou:
- US Gov Azure
- [Office 365 RSZ High & DoD](https://docs.microsoft.com/microsoft-365/compliance/tls-1-2-in-office-365-gcc?view=o365-worldwide)
 
---

### <a name="assign-applications-to-roles-on-au-and-object-scope"></a>Přiřazení aplikací k rolím v rozsahu AU a Object

**Zadejte:** Nová funkce  
**Kategorie služby:** RBAC  
**Schopnost produktu:** Access Control
 
Tato funkce umožňuje přiřadit aplikaci (SPN) k roli správce v oboru jednotky pro správu. Další informace najdete v tématu [přiřazení vymezených rolí k jednotce pro správu](../roles/admin-units-assign-roles.md).

---

### <a name="now-you-can-disable-and-delete-guest-users-when-theyre-denied-access-to-a-resource"></a>Nyní můžete zakázat a odstranit uživatele typu Host, pokud jim dojde k odepření přístupu k prostředku.

**Zadejte:** Nová funkce  
**Kategorie služby:** Kontroly přístupu  
**Schopnost produktu:** Zásady správného řízení identity
 
Zakázat a odstranit je pokročilý ovládací prvek kontrol přístupu Azure AD, který organizacím umožňuje lépe spravovat externí hosty ve skupinách a aplikacích. Pokud dojde k odepření hostů při kontrole přístupu, **zakážete a odstraníte** automaticky blokování těchto přihlášení po dobu 30 dnů. Po 30 dnech se úplně odeberou z tenanta.

Další informace o této funkci najdete v tématu [zakázání a odstranění externích identit pomocí kontrol přístupu Azure AD (Preview)](../governance/access-reviews-external-users.md#disable-and-delete-external-identities-with-azure-ad-access-reviews-preview).
 
---

### <a name="access-review-creators-can-add-custom-messages-in-emails-to-reviewers"></a>Tvůrci kontroly přístupu můžou přidat vlastní zprávy e-mailů k kontrolorům.

**Zadejte:** Nová funkce  
**Kategorie služby:** Kontroly přístupu  
**Schopnost produktu:** Zásady správného řízení identity
 
V rámci kontrol přístupu ke službě Azure AD teď správci vytvářející recenze můžou napsat vlastní zprávu kontrolorům. Revidující uvidí zprávu v e-mailu, které obdrží, a vyzve k dokončení kontroly. Další informace o použití této funkce najdete v části Krok 6 oddílu [Pokročilá nastavení](../governance/create-access-review.md#advanced-settings) .

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---october-2020"></a>Nové zřizovací konektory v galerii aplikací Azure AD – říjen 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** integrace třetích stran
 
Nyní můžete automatizovat vytváření, aktualizaci a odstraňování uživatelských účtů pro tyto nově integrované aplikace:

- [Apple Business Manager](../saas-apps/apple-business-manager-provision-tutorial.md)
- [Apple School Manager](../saas-apps/apple-school-manager-provision-tutorial.md)
- [Code42](../saas-apps/code42-provisioning-tutorial.md)
- [AlertMedia](../saas-apps/alertmedia-provisioning-tutorial.md)
- [OpenText Directory Services](../saas-apps/open-text-directory-services-provisioning-tutorial.md)
- [Cinode](../saas-apps/cinode-provisioning-tutorial.md)
- [Global Relay Identity Sync](../saas-apps/global-relay-identity-sync-provisioning-tutorial.md)

Další informace o tom, jak lépe zabezpečit vaši organizaci pomocí automatizovaného zřizování uživatelských účtů, najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](../app-provisioning/user-provisioning.md).
 
---

### <a name="integration-assistant-for-azure-ad-b2c"></a>Pomocník pro integraci pro Azure AD B2C

**Zadejte:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C
 
Prostředí Pomocník pro integraci (Preview) je teď k dispozici pro Azure AD B2C Registrace aplikací. Toto prostředí vám pomůže s konfigurací aplikace pro běžné scénáře. Přečtěte si další informace o [osvědčených postupech a doporučeních platformy Microsoft Identity Platform](../develop/identity-platform-integration-checklist.md).
 
---

### <a name="view-role-template-id-in-azure-portal-ui"></a>Zobrazení ID šablony role v Azure Portal uživatelském rozhraní

**Zadejte:** Nová funkce  
**Kategorie služby:** Role Azure  
**Schopnost produktu:** Access Control
 

Teď si můžete zobrazit ID šablony každé role Azure AD v Azure Portal. V Azure AD vyberte  **Popis** vybrané role. 

Doporučujeme, aby zákazníci používali ID šablon rolí ve svém skriptu PowerShellu a kódu místo zobrazovaného názvu. ID šablony role je podporované pro použití pro objekty [directoryRoles](https://docs.microsoft.com/graph/api/resources/directoryrole?view=graph-rest-1.0) a [rutiny roledefinition](https://docs.microsoft.com/graph/api/resources/unifiedroledefinition?view=graph-rest-beta) . Další informace o ID šablon rolí najdete v tématu [ID šablon rolí](../roles/permissions-reference.md#role-template-ids).

---

### <a name="api-connectors-for-azure-ad-b2c-sign-up-user-flows-is-now-in-public-preview"></a>Konektory rozhraní API pro Azure AD B2C uživatelské toky pro registraci jsou teď ve verzi Public Preview.

**Zadejte:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C
 

Konektory API jsou teď k dispozici pro použití s Azure Active Directory B2C. Konektory API umožňují používat webová rozhraní API k přizpůsobení uživatelských toků registrace a integraci s externími cloudových systémů. Konektory rozhraní API můžete použít k těmto akcím:

- Integrace s vlastními pracovními postupy schvalování
- Ověřit data vstupu uživatele
- Přepsat atributy uživatele 
- Spustit vlastní obchodní logiku 

 Další informace najdete v části [Použití konektorů rozhraní API k přizpůsobení a rozšiřování registrační](../../active-directory-b2c/api-connectors-overview.md) dokumentace.

---

### <a name="state-property-for-connected-organizations-in-entitlement-management"></a>Vlastnost State pro připojené organizace ve správě nároků

**Zadejte:** Nová funkce  
**Kategorie služby:** **Schopnost produktu** Správa adresářů: Správa nároků
 

 Všechny připojené organizace teď budou mít další vlastnost s názvem "State". Stav bude řídit způsob, jakým se připojená organizace použije v zásadách, které odkazují na "všechny nakonfigurované propojené organizace". Hodnota bude buď "nakonfigurovaná" (což znamená, že organizace je v oboru zásad, které používají klauzuli All) nebo "navržené" (což znamená, že organizace není v oboru).  

Ručně vytvořené propojené organizace budou mít nastavené výchozí nastavení. Mezitím budou automaticky vytvořená (vytvořená prostřednictvím zásad, které umožňují jakémukoli uživateli z Internetu vyžadovat přístup) výchozí nastavení "navrženo".  Všechny připojené organizace vytvořené před září 9 2020 budou nastavené na nakonfigurováno. Správci mohou tuto vlastnost podle potřeby aktualizovat. [Přečtěte si další informace](../governance/entitlement-management-organization.md#managing-a-connected-organization-programmatically).
 

---

### <a name="azure-active-directory-external-identities-now-has-premium-advanced-security-settings-for-b2c"></a>Azure Active Directory externích identit teď má pro B2C nastavení Premium Advanced Security.

**Zadejte:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C
 
K dispozici jsou nově podmíněný přístup na základě rizik a funkce detekce rizik ochrany Identity Protection v [Azure AD B2C](../..//active-directory-b2c/conditional-access-identity-protection-overview.md). Díky těmto pokročilým funkcím zabezpečení můžou zákazníci teď:
- Využijte inteligentní přehledy k vyhodnocení rizik s B2C aplikacemi a koncovými uživatelskými účty. Mezi detekce patří neobvyklá cesta, anonymní IP adresy, IP adresy propojené malware a Azure AD Threat Intelligence. K dispozici jsou také sestavy založené na portálu a rozhraní API.
- Automaticky řeší rizika konfigurací zásad adaptivního ověřování pro uživatele B2C. Vývojáři a správci aplikací můžou zmírnit riziko v reálném čase tím, že požadují vícefaktorové ověřování (MFA) nebo blokují přístup v závislosti na zjištěné úrovni rizik uživatelů, s dalšími dostupnými ovládacími prvky na základě umístění, skupiny a aplikace.
- Integrujte s Azure AD B2C toky uživatelů a vlastní zásady. Podmínky se můžou aktivovat z vestavěných toků uživatelů v Azure AD B2C nebo je můžete začlenit do vlastních zásad B2C. Stejně jako u dalších aspektů toku uživatele B2C je možné přizpůsobit zasílání zpráv o činnostech koncového uživatele. Přizpůsobení je na základě možností hlasu, značky a zmírnění rizik v organizaci.
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---october-2020"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – říjen 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
V říjnu 2020 jsme do Galerie aplikací přidali následující 27 nových aplikací s podporou federace:

[Sentry](../saas-apps/sentry-tutorial.md), [Bumblebee – produktivita superapp](https://app.yellowmessenger.com/user/login), [cloudová integrace společnosti ABBYY](../saas-apps/abbyy-flexicapture-cloud-tutorial.md), [EAComposer](../saas-apps/eacomposer-tutorial.md), [Genesys Integration Cloud pro Azure](https://apps.mypurecloud.com/msteams-integration/), [portál pro zóny](https://portail.zonetechnologie.com/signin), [Beautiful.AI](../saas-apps/beautiful.ai-tutorial.md), [Datawiza Access Broker](https://console.datawiza.com/), [ZOKRI](https://app.zokri.com/), [CheckProof](../saas-apps/checkproof-tutorial.md), [ecoChallenge.org](https://events.ecochallenge.org/users/login), [atSpoke](http://atspoke.com/login), [připomenutí schůzky](https://app.appointmentreminder.co.nz/account/login), [Cloud. trh](https://cloud.market/), [TravelPerk](../saas-apps/travelperk-tutorial.md), [Greetings](https://app.greetly.com/), [OrgVitality SSO} (.. /SaaS-Apps/orgvitality-SSO-tutorial.MD), [leteckého provozu na webu](../saas-apps/web-cargo-air-tutorial.md), [Flow Flow CRM](../saas-apps/loop-flow-crm-tutorial.md), [Starmind](../saas-apps/starmind-tutorial.md), [Workstem](https://hrm.workstem.com/login), [maloobchodní Zipline](../saas-apps/retail-zipline-tutorial.md), [Hoxhunt](../saas-apps/hoxhunt-tutorial.md), [Samsara](../saas-apps/samsara-tutorial.md)MEVISIO, [Samsara,](../saas-apps/mevisio-tutorial.md) [Nimbus](../saas-apps/nimbus-tutorial.md), [Pulse Secure Virtual Traffic Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-virtual-traffic-manager-tutorial)

Dokumentaci pro všechny aplikace můžete také najít tady. https://aka.ms/AppsTutorial

Pokud chcete zobrazit seznam aplikací v galerii aplikací Azure AD, přečtěte si podrobnosti tady. https://aka.ms/AzureADAppRequest

---

### <a name="provisioning-logs-can-now-be-streamed-to-log-analytics"></a>Protokoly zřizování se teď dají streamovat do Log Analytics.

**Zadejte:** Nová funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav
 

Publikujte protokoly zřizování do Log Analytics za účelem:
- Ukládat protokoly zřizování po dobu delší než 30 dnů
- Definování vlastních výstrah a oznámení
- Sestavování řídicích panelů pro vizualizaci protokolů
- Provádění složitých dotazů k analýze protokolů 

Informace o použití této funkce najdete v tématu [Vysvětlení způsobu, jakým se zřizování integruje s protokoly Azure monitor](../app-provisioning/application-provisioning-log-analytics.md).
 
---

### <a name="provisioning-logs-can-now-be-viewed-by-application-owners"></a>Pro vlastníky aplikací se teď můžou zobrazit protokoly zřizování.

**Zadejte:** Změněná funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav
 
Nyní můžete vlastníkům aplikace dovolit monitorovat aktivity pomocí služby zřizování a řešit problémy bez poskytnutí privilegované role nebo zrušení kritického bodu. [Přečtěte si další informace](../reports-monitoring/concept-provisioning-logs.md).
 
---

### <a name="renaming-10-azure-active-directory-roles"></a>Přejmenování 10 Azure Active Directory rolí

**Zadejte:** Změněná funkce  
**Kategorie služby:** Role Azure  
**Schopnost produktu:** Access Control
 
Některé předdefinované role Azure Active Directory (AD) mají názvy, které se liší od těch, které se zobrazují v centru pro správu Microsoft 365, na portálu Azure AD a na Microsoft Graph. Tato nekonzistence může způsobit problémy v automatizovaných procesech. V této aktualizaci přejmenováváme 10 názvů rolí, aby se zajistila konzistence. Nové názvy rolí mají následující tabulka:

![Tabulka nových názvů rolí](media/whats-new/azure-role.png)

---

### <a name="azure-ad-b2c-support-for-auth-code-flow-for-spas-using-msal-js-2x"></a>Azure AD B2C podpora toku kódu ověřování pro jednostránkové pomocí MSAL JS 2. x

**Zadejte:** Změněná funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C
 
MSAL.js verze 2. x teď zahrnuje podporu toku autorizačního kódu pro jednostránkové webové aplikace (jednostránkové). Azure AD B2C teď bude podporovat použití typu aplikace SPA pro Azure Portal a používání MSAL.jsho toku autorizačního kódu s PKCE pro jednostránkové aplikace. Díky tomu bude jednostránkové používat Azure AD B2C k udržování jednotného přihlašování s novějšími prohlížeči a respektování novějších doporučení protokolu ověřování. Začněte s [registrací jednostránkové aplikace (Spa) v Azure Active Directory B2C](../../active-directory-b2c/tutorial-register-spa.md) kurzu.

---

### <a name="updates-to-remember-multi-factor-authentication-mfa-on-a-trusted-device-setting"></a>Aktualizace zapamatování Multi-Factor Authentication (MFA) na nastavení důvěryhodného zařízení

**Zadejte:** Změněná funkce  
**Kategorie služby:** VÍCEFAKTOROVÉHO  
**Schopnost produktu:** Zabezpečení identity & ochrana
 

Nedávno jsme aktualizovali [zapamatovatelné Multi-Factor Authentication (MFA)](../authentication/howto-mfa-mfasettings.md#remember-multi-factor-authentication) na funkci důvěryhodného zařízení a rozšířili jsme ověřování po dobu až 365 dní. Licence pro Azure Active Directory (Azure AD) Premium můžou používat taky [zásady četnosti přihlašování](../conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency) , které poskytují větší flexibilitu pro nastavení opětovného ověřování.

Pro optimální činnost koncového uživatele doporučujeme použít četnost přihlášení k podmíněnému přístupu pro prodloužení životnosti relací v důvěryhodných zařízeních, umístěních nebo relacích s nízkým rizikem jako alternativu k možnosti Zapamatovat MFA v nastavení důvěryhodného zařízení. Pokud chcete začít, přečtěte si naše [nejnovější pokyny k optimalizaci prostředí pro opakované ověření](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

---

## <a name="september-2020"></a>Září 2020

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---september-2020"></a>Nové zřizovací konektory v galerii aplikací Azure AD – září 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** integrace třetích stran
 
Nyní můžete automatizovat vytváření, aktualizaci a odstraňování uživatelských účtů pro tyto nově integrované aplikace:

- [Coda](../saas-apps/coda-provisioning-tutorial.md)
- [Cofense Recipient Sync](../saas-apps/cofense-provision-tutorial.md)
- [InVision](../saas-apps/invision-provisioning-tutorial.md)
- [myday](../saas-apps/myday-provision-tutorial.md)
- [SAP Analytics Cloud](../saas-apps/sap-analytics-cloud-provisioning-tutorial.md)
- [Povědomí o zabezpečení Webroot](../saas-apps/webroot-security-awareness-training-provisioning-tutorial.md)

Další informace o tom, jak lépe zabezpečit vaši organizaci pomocí automatizovaného zřizování uživatelských účtů, najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](../app-provisioning/user-provisioning.md).
 
---
### <a name="cloud-provisioning-public-preview-refresh"></a>Zřizování cloudu se Public Preview aktualizovat.

**Zadejte:** Nová funkce  
**Kategorie služby:** **Schopnost produktu** Azure AD zřizování cloudu: Správa životního cyklu identit
 
Azure AD Connect aktualizace cloudového zřizování ve verzi Public Preview dvě hlavní vylepšení vyvinutá na základě zpětné vazby od zákazníků: 

- Možnosti mapování atributů prostřednictvím Azure Portal

    Díky této funkci můžou správci IT mapovat atributy uživatelů, skupin nebo kontaktů ze služby AD na Azure AD pomocí různých typů mapování, které jsou dnes přítomné. Mapování atributů je funkce používaná ke standardizaci hodnot atributů, které se přenášejí ze služby Active Directory na Azure Active Directory. Jedna z nich může určit, jestli má být hodnota atributu přímo namapována z AD do služby Azure AD, nebo použít výrazy k transformaci hodnot atributů při zřizování uživatelů. [Další informace](../cloud-provisioning/how-to-attribute-mapping.md)

- Zřizování na vyžádání nebo testování uživatelského prostředí

    Po nastavení konfigurace můžete chtít ověřit, zda transformace uživatele pracuje podle očekávání, a teprve potom ji použít pro všechny uživatele v oboru. Díky zřizování na vyžádání můžou správci IT zadat rozlišující název (DN) uživatele AD a zjistit, jestli se synchronizují podle očekávání. Zřizování na vyžádání poskytuje skvělý způsob, jak zajistit, že mapování atributů, které jste dříve pracovali, podle očekávání. [Další informace](../cloud-provisioning/how-to-on-demand-provision.md)
 
---

### <a name="audited-bitlocker-recovery-in-azure-ad---public-preview"></a>Auditované obnovení BitLockeru v Azure AD – Public Preview

**Zadejte:** Nová funkce  
**Kategorie služby:** Správa přístupu k zařízením  
**Schopnost produktu:** Správa životního cyklu zařízení
 
Když správci IT nebo koncoví uživatelé čtou klíče pro obnovení nástroje BitLocker, ke kterým mají přístup, Azure Active Directory nyní vygeneruje protokol auditu, který zachycuje přístup k obnovovacímu klíči. Stejný audit poskytuje podrobnosti o zařízení, ke kterému byl klíč BitLocker přidružený.

Koncoví uživatelé mají [přístup ke svým klíčům pro obnovení prostřednictvím svého účtu](../user-help/my-account-portal-devices-page.md#view-a-bitlocker-key). Správci IT můžou získat přístup k klíčům pro obnovení prostřednictvím [rozhraní API pro obnovení pomocí obnovovacího klíče BitLockeru ve verzi beta](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta) nebo prostřednictvím portálu Azure AD. Další informace najdete v tématu [zobrazení nebo kopírování klíčů nástroje BitLocker na portálu Azure AD](../devices/device-management-azure-portal.md#view-or-copy-bitlocker-keys).

---

### <a name="teams-devices-administrator-built-in-role"></a>Integrovaná role Správce zařízení Teams

**Zadejte:** Nová funkce  
**Kategorie služby:** RBAC  
**Schopnost produktu:** Access Control
 
Uživatelé s rolí [Správce zařízení týmů](../roles/permissions-reference.md#teams-devices-administrator) můžou spravovat [zařízení certifikovaná](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) v centru pro správu týmů. 

Tato role umožňuje uživateli zobrazit všechna zařízení v jednoduchém přehledu s možností vyhledávání a filtrování zařízení. Uživatel může také zkontrolovat podrobnosti každého zařízení, včetně přihlášeného účtu a značka a modelu zařízení. Uživatel může změnit nastavení na zařízení a aktualizovat verze softwaru. Tato role neuděluje oprávnění kontrolovat aktivity týmů a zavolá kvalitu zařízení.
 
---

### <a name="advanced-query-capabilities-for-directory-objects"></a>Pokročilé možnosti dotazů pro objekty adresáře

**Zadejte:** Nová funkce  
**Kategorie služby:** MS Graph  
**Schopnost produktu:** Vývojářské prostředí
 
Všechny nové možnosti dotazů, které jsou představené pro objekty adresáře v rozhraních API Azure AD, jsou teď dostupné v koncovém bodu verze 1.0 a připravené k výrobě. Vývojáři mohou pomocí standardních operátorů OData spočítat, Hledat, filtrovat a řadit objekty adresáře a související odkazy.

Další informace najdete v dokumentaci [zde](https://aka.ms/BlogPostMezzoGA)a můžete také poslat názor na tento [stručný průzkum](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_yN8EPoGo5OpR1hgmCp1XxUMENJRkNQTk5RQkpWTE44NEk2U0RIV0VZRy4u).
 
---

### <a name="public-preview-continuous-access-evaluation-for-tenants-who-configured-conditional-access-policies"></a>Public Preview: vyhodnocení průběžného přístupu pro klienty, kteří konfigurují zásady podmíněného přístupu

**Zadejte:** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
Vyhodnocování průběžného přístupu (CAE) je teď k dispozici ve verzi Public Preview pro klienty Azure AD se zásadami podmíněného přístupu. V CAE se kritické události a zásady zabezpečení vyhodnocují v reálném čase. To zahrnuje zakázání účtu, resetování hesla a změnu umístění. Další informace najdete v tématu [vyhodnocení průběžného přístupu](../conditional-access/concept-continuous-access-evaluation.md).

---

### <a name="public-preview-ask-users-requesting-an-access-package-additional-questions-to-improve-approval-decisions"></a>Verze Public Preview: vyžádání dalších otázek pro přístup uživatelů k balíčku pro zlepšení rozhodnutí o schválení

**Zadejte:** Nová funkce  
**Kategorie služby:** Správa přístupu uživatelů  
**Schopnost produktu:** Správa nároků
 
Správci teď můžou vyžadovat, aby si uživatelé, kteří žádají o přístup k balíčku, odpověděli na další otázky mimo obchodní odůvodnění na portálu pro správu nároků služby Azure AD. Odpovědi uživatelů se pak zobrazí schvalovatelům, kteří jim pomohou zajistit přesnější rozhodnutí o schválení přístupu. Další informace najdete v tématu [shromáždění dalších informací o žadatelích ke schválení (Preview)](../governance/entitlement-management-access-package-approval-policy.md#collect-additional-requestor-information-for-approval-preview).
 
---

### <a name="public-preview-enhanced-user-management"></a>Verze Public Preview: Rozšířená správa uživatelů

**Zadejte:** Nová funkce  
**Kategorie služby:** Správa uživatelů  
**Schopnost produktu:** Správa uživatelů
 

Portál Azure AD je aktualizovaný, aby bylo snazší najít uživatele na stránkách všichni uživatelé a odstranění uživatelé. Změny ve verzi Preview zahrnují: 
- Více viditelných vlastností uživatele včetně ID objektu, stavu synchronizace adresáře, typu vytvoření a vystavitele identity.
- Hledání teď umožňuje kombinované hledání názvů, e-mailů a ID objektů.
- Rozšířené filtrování podle typu uživatele (Member, Guest a None), stav synchronizace adresářů, typ vytvoření, název společnosti a název domény.
- Nové možnosti řazení ve vlastnostech, jako je název, hlavní název uživatele a datum odstranění.
- Nový celkový počet uživatelů, kteří se aktualizují pomocí všech hledání nebo filtrů.

Další informace najdete [v tématu vylepšení správy uživatelů (Preview) v Azure Active Directory](../enterprise-users/users-search-enhanced.md).

---

### <a name="new-notes-field-for-enterprise-applications"></a>Nové pole poznámky pro podnikové aplikace

**Zadejte:** Nová funkce  
**Kategorie služby:** **Funkce produktu** podnikové aplikace: jednotné přihlašování

Můžete přidat volné textové poznámky k podnikovým aplikacím. Můžete přidat všechny relevantní informace, které vám pomohou aplikace manažera v rámci podnikových aplikací. Další informace najdete v tématu [rychlý Start: Konfigurace vlastností aplikace ve vašem tenantovi Azure Active Directory (Azure AD)](../manage-apps/add-application-portal-configure.md). 

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---september-2020"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – září 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran

V září 2020 jsme do Galerie aplikací přidali následující 34 nových aplikací s podporou federace:

[VMware Horizon – Unified Access Gateway](), [Pulse Secure PC](../saas-apps/vmware-horizon-unified-access-gateway-tutorial.md), [Inventory360](../saas-apps/pulse-secure-pcs-tutorial.md), [Frontitude](https://services.enteksystems.de/sso/microsoft/signup), [BookWidgets](https://www.bookwidgets.com/sso/office365), [ZVD_SERVER](https://zaas.zenmutech.com/user/signin), [HashData for Business](https://hashdata.app/login.xhtml), [SecureLogin](https://securelogin.securelogin.nu/sso/azure/login), [CyberSolutions MAILBASEΣ/CMS](../saas-apps/cybersolutions-mailbase-tutorial.md), [CyberSolutions CYBERMAILΣ](../saas-apps/cybersolutions-cybermail-tutorial.md), [LimbleCMMS](https://auth.limblecmms.com/), [Glint Inc](../saas-apps/glint-inc-tutorial.md), [zeroheight](../saas-apps/zeroheight-tutorial.md), [vhodnosti pro pohlaví](https://app.genderfitness.com/), [Coeo portál](https://my.coeo.com/), [gramaticky](../saas-apps/grammarly-tutorial.md), [Fivetran](../saas-apps/fivetran-tutorial.md), [Kumolus](../saas-apps/kumolus-tutorial.md), [RSA Archer Suite](../saas-apps/rsa-archer-suite-tutorial.md), [TeamzSkill](../saas-apps/teamzskill-tutorial.md), [raumfürraum](../saas-apps/raumfurraum-tutorial.md), [Saviynt](../saas-apps/saviynt-tutorial.md), [BizMerlinHR](https://marketplace.bizmerlin.net/bmone/signup), [Mobile Locker](../saas-apps/mobile-locker-tutorial.md), [Zengine](../saas-apps/zengine-tutorial.md), [CloudCADI](https://app.cloudcadi.com/login), [Simfoni Analytics](https://simfonianalytics.com/accounts/microsoft/login/), [Secure a](../saas-apps/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial.md) [identity & Access Management](https://my.priva.com/), [nitro pro](https://www.gonitro.com/nps/product-details/downloads), [Eventfinity](../saas-apps/eventfinity-tutorial.md), [Fexa](../saas-apps/fexa-tutorial.md) [,](https://wisteconline.com/auth/oidc) [zabezpečené podepisování Enterprise Portal](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), zabezpečené podepisování [Enterprise Portal](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal)

Dokumentaci ke všem aplikacím můžete najít také zde: https://aka.ms/AppsTutorial .

Pokud chcete zobrazit seznam aplikací v galerii aplikací Azure AD, přečtěte si podrobnosti tady: https://aka.ms/AzureADAppRequest .

---

### <a name="new-delegation-role-in-azure-ad-entitlement-management-access-package-assignment-manager"></a>Nová role delegování ve správě nároků v Azure AD: přístup k balíčku přiřazení balíčků

**Zadejte:** Nová funkce  
**Kategorie služby:** Správa přístupu uživatelů  
**Schopnost produktu:** Správa nároků
 
Do správy opravňujících k Azure AD se přidala nová role správce přiřazení balíčku přístupu, která poskytuje přesnější oprávnění ke správě přiřazení. Nyní můžete delegovat úkoly na uživatele v této roli, kteří mohou delegovat správu pro přístup balíčku k vlastníkovi firmy. Správce přiřazení balíčku pro přístup ale nemůže změnit zásady balíčků přístupu ani jiné vlastnosti, které jsou nastavené správci. 

S touto novou rolí budete mít k výhodu nejmenších oprávnění potřebných k delegování správy přiřazení a udržování administrativního řízení pro všechny ostatní konfigurace balíčků přístupu. Další informace najdete v tématu [role správy oprávnění](../governance/entitlement-management-delegate.md#entitlement-management-roles).
 
---

### <a name="changes-to-privileged-identity-managements-onboarding-flow"></a>Změny v toku připojování Privileged Identity Management

**Zadejte:** Změněná funkce  
**Kategorie služby:** Privileged Identity Management  
**Schopnost produktu:** Privileged Identity Management
 
V minulosti byl vyžadován souhlas uživatele s připojováním k Privileged Identity Management (PIM) a postup připojování v okně PIM, které zahrnovalo registraci v Azure AD MFA. Díky nedávné integraci PIM v okně role a správci služby Azure AD odebíráme toto prostředí. Každý tenant s platnou licencí P2 se automaticky připojí k PIM.

Připojování k PIM nemá žádný přímý negativní dopad na vašeho tenanta. Můžete očekávat následující změny:
- Další možnosti přiřazení, jako je aktivní vs. s nárokem v podobě počátečního a koncového času, když provedete přiřazení v okně role PIM nebo Azure AD a správci. 
- Další mechanismy vytváření oborů, jako jsou jednotky pro správu a vlastní role, se zavedly přímo do prostředí pro přiřazení. 
- Pokud jste správcem globálního správce nebo privilegovaného správce role, můžete začít s několika dalšími e-maily, jako je týdenní přehled o PIM. 
- V protokolu auditu souvisejícím s přiřazením role můžete také vidět instanční objekt MS-PIM. Tato očekávaná změna by neměla mít vliv na běžný pracovní postup.

 Další informace najdete v tématu [Začínáme používat Privileged Identity Management](../privileged-identity-management/pim-getting-started.md).

---

### <a name="azure-ad-entitlement-management-the-select-pane-of-access-package-resources-now-shows-by-default-the-resources-currently-in-the-selected-catalog"></a>Správa nároků na Azure AD: ve výchozím nastavení se nyní zobrazuje podokno vybrat prostředky balíčku přístupu. aktuálně se jedná o prostředky, které jsou aktuálně ve vybraném katalogu.

**Zadejte:** Změněná funkce  
**Kategorie služby:** Správa přístupu uživatelů  
**Schopnost produktu:** Správa nároků
 

V toku vytváření balíčku přístupu se změna chování podokna vybrat na kartě role prostředků. V současné době je výchozím chováním zobrazení všech prostředků vlastněných uživatelem a prostředky přidanými do vybraného katalogu. 

Toto prostředí se změní tak, aby se ve výchozím nastavení zobrazovaly jenom prostředky aktuálně přidané do katalogu, aby uživatelé mohli snadno vybírat prostředky z katalogu. Tato aktualizace vám pomůže s zjistitelnými prostředky, které se mají přidat do balíčků pro přístup, a snižuje riziko neúmyslného přidání prostředků vlastněných uživatelem, kteří nejsou součástí katalogu. Další informace najdete v tématu [Vytvoření nového balíčku pro přístup ve správě nároků ve službě Azure AD](../governance/entitlement-management-access-package-create.md#resource-roles).
 
---

## <a name="august-2020"></a>Srpen 2020 
 
### <a name="updates-to-azure-multi-factor-authentication-server-firewall-requirements"></a>Aktualizace požadavků na bránu firewall pro Azure Multi-Factor Authentication Server

**Zadejte:** Plánování změn  
**Kategorie služby:** VÍCEFAKTOROVÉHO  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
Od 1. října 2020 budou požadavky na bránu firewall pro Azure MFA Server vyžadovat další rozsahy IP adres.

Pokud máte odchozí pravidla brány firewall ve vaší organizaci, aktualizujte pravidla tak, aby servery MFA mohly komunikovat se všemi potřebnými rozsahy IP adres. Rozsahy IP adres najdete v části [požadavky na bránu firewall pro Azure Multi-Factor Authentication Server](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements).

---

### <a name="upcoming-changes-to-user-experience-in-identity-secure-score"></a>Nadcházející změny uživatelského prostředí v hodnocení identity Secure

**Zadejte:** Plánování změn  
**Kategorie služby:** **Schopnost produktu** Identity Protection: zabezpečení identity & Protection

Aktualizujeme portál pro zabezpečený skóre identit, který bude zarovnaný se změnami zavedenými v [nové verzi](/microsoft-365/security/mtp/microsoft-secure-score-whats-new?view=o365-worldwide)Microsoft Secure skore. 

Verze Preview se změnami bude k dispozici na začátku září. Změny ve verzi Preview zahrnují:
- "Bezpečné skóre identity" přejmenováno na "zabezpečené skóre pro identitu" pro přizpůsobení značky s Microsoft Secure skore
- Body jsou normalizovány na standardní měřítko a nahlášené v procentech namísto bodů.

V této verzi Preview můžou zákazníci přepínat mezi stávajícím prostředím a novým prostředím. Tato verze Preview bude poslední až do konce listopadu 2020. Po verzi Preview budou zákazníci automaticky přesměrováni na nové prostředí pro uživatelské prostředí.

---

### <a name="new-restricted-guest-access-permissions-in-azure-ad---public-preview"></a>Nová omezená oprávnění k přístupu hosta v Azure AD – Public Preview

**Zadejte:** Nová funkce  
**Kategorie služby:** Access Control   
**Schopnost produktu:** Správa uživatelů

Aktualizovali jsme oprávnění na úrovni adresáře pro uživatele typu Host. Tato oprávnění umožňují správcům vyžadovat další omezení a ovládací prvky při přístupu k externímu uživateli typu Host. Správci teď můžou přidat další omezení přístupu externích hostů k informacím o profilu a členství uživatelů a skupin. Díky této funkci veřejné verze Preview můžou zákazníci spravovat přístup externích uživatelů ve velkém měřítku tím, že zakazují členství ve skupině, včetně omezení uživatelů typu Host, aby viděli členství skupin, ve kterých jsou.

Další informace najdete v tématu [omezená oprávnění k přístupu hosta](../enterprise-users/users-restrict-guest-permissions.md) a [výchozí oprávnění uživatelů](./users-default-permissions.md).
 
---

### <a name="general-availability-of-delta-queries-for-service-principals"></a>Obecná dostupnost rozdílových dotazů u instančních objektů

**Zadejte:** Nová funkce  
**Kategorie služby:** MS Graph  
**Schopnost produktu:** Vývojářské prostředí
 
Microsoft Graph rozdílový dotaz teď podporuje typ prostředku v v 1.0:
- Instanční objekt

Klienti teď můžou sledovat změny těchto prostředků efektivně a poskytovat nejlepší řešení pro synchronizaci změn těchto prostředků s místním úložištěm dat. Informace o tom, jak nakonfigurovat tyto prostředky v dotazu, najdete v tématu [použití rozdílového dotazu ke sledování změn v Microsoft Graphch datech](/graph/delta-query-overview).
 
---

### <a name="general-availability-of-delta-queries-for-oauth2permissiongrant"></a>Obecná dostupnost rozdílových dotazů pro oAuth2PermissionGrant

**Zadejte:** Nová funkce  
**Kategorie služby:** MS Graph  
**Schopnost produktu:** Vývojářské prostředí

Microsoft Graph rozdílový dotaz teď podporuje typ prostředku v v 1.0:
- OAuth2PermissionGrant

Klienti teď můžou sledovat změny těchto prostředků efektivně a poskytovat nejlepší řešení pro synchronizaci změn těchto prostředků s místním úložištěm dat. Informace o tom, jak nakonfigurovat tyto prostředky v dotazu, najdete v tématu [použití rozdílového dotazu ke sledování změn v Microsoft Graphch datech](/graph/delta-query-overview).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---august-2020"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – srpen 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran

V srpnu 2020 jsme do Galerie aplikací přidali následující 25 nových aplikací s podporou federace:

[Backup365](https://portal.backup365.io/login), [SOAPBOX](https://app.soapboxhq.com/create?step=auth&provider=azure-ad2-oauth2), [Alma SIS](https://almau.getalma.com/), [konektor Enlyft Dynamics 365](http://enlyft.com/), [Serraview místo pro využívání softwarových řešení](../saas-apps/serraview-space-utilization-software-solutions-tutorial.md), [uniq](https://web.uniq.app/), [viditelně](../saas-apps/visibly-tutorial.md), [Zylo](../saas-apps/zylo-tutorial.md), [Edmentum – hodnocení přesná cesta](https://auth.edmentum.com/elf/login), [CyberLAB](https://cyberlab.evolvesecurity.com/#/welcome), [Altamira HRM](../saas-apps/altamira-hrm-tutorial.md), WireWheel, [ZIX dodržování předpisů a zachytávání](https://sminstall.zixcorp.com/teams/teams.php?install_request=true&tenant_id=common), [GreenLight podnikových ovládacích prvků](../saas-apps/greenlight-enterprise-business-controls-platform-tutorial.md) [,](https://goto.bpanda.com/login)Genetec, [volný prostor](https://www.clearance.network/) [,](../saas-apps/isams-tutorial.md) [VeraSMART, Amiko](../saas-apps/wirewheel-tutorial.md), [Funnel Leasing](https://nestiolistings.com/sso/oidc/azure/authorize/) [Twingate,](../saas-apps/verasmart-tutorial.md) [Scalefusion](https://amiko.web.rivero.app/) [,](../saas-apps/fortigate-ssl-vpn-tutorial.md) [Bpanda,](https://auth.twingate.com/signup) [Vivun Calendar Connect](https://app.vivun.com/dashboard/calendar/connect)pro [koncové uživatele](https://www.wandera.com/) [, Vivun](https://scalefusion.com/users/sign_in/)

Dokumentaci pro všechny aplikace můžete také najít tady. https://aka.ms/AppsTutorial

Pokud chcete zobrazit seznam aplikací v galerii aplikací Azure AD, přečtěte si podrobnosti tady. https://aka.ms/AzureADAppRequest

---

### <a name="resource-forests-now-available-for-azure-ad-ds"></a>Doménové struktury prostředků jsou teď dostupné pro Azure služba AD DS 

**Zadejte:** Nová **Kategorie služby funkcí:** Azure AD Domain Services   
**Schopnost produktu:** Azure AD Domain Services
 
Schopnost doménových struktur prostředků v Azure AD Domain Services je teď všeobecně dostupná. Nyní můžete povolit autorizaci bez synchronizace hodnot hash hesel, aby bylo možné použít Azure AD Domain Services, včetně autorizace pomocí čipové karty. Další informace najdete v tématu [Koncepty a funkce sady replik pro Azure Active Directory Domain Services (Preview)](../../active-directory-domain-services/concepts-replica-sets.md).
 
---

### <a name="regional-replica-support-for-azure-ad-ds-managed-domains-now-available"></a>Podpora místní repliky pro Azure služba AD DS spravované domény je teď k dispozici.

**Zadejte:** Nová funkce   
**Kategorie služby:** Azure AD Domain Services  
**Schopnost produktu:** Azure AD Domain Services
 
Spravovanou doménu můžete rozšířit tak, aby měla více než jednu sadu replik na jeden tenant služby Azure AD. Sady replik se dají přidat do všech partnerských virtuálních sítí v libovolné oblasti Azure, která podporuje Azure AD Domain Services. Další sady replik v různých oblastech Azure poskytují geografickou obnovu po havárii pro starší aplikace, pokud oblast Azure přejde do režimu offline. Další informace najdete v tématu [Koncepty a funkce sady replik pro Azure Active Directory Domain Services (Preview)](../../active-directory-domain-services/concepts-replica-sets.md).

---

### <a name="general-availability-of-azure-ad-my-sign-ins"></a>Obecná dostupnost služby Azure AD moje Sign-Ins

**Zadejte:** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Prostředí koncového uživatele
 
Azure AD moje Sign-Ins je nová funkce, která umožňuje podnikovým uživatelům zkontrolovat historii přihlášení, aby zkontrolovala jakoukoli neobvyklou aktivitu. Kromě toho tato funkce umožňuje koncovým uživatelům hlásit zprávu "This jsem já" nebo "to jsem já" na podezřelých aktivitách. Další informace o použití této funkce najdete v tématu [zobrazení a hledání poslední přihlašovací aktivity na stránce Sign-Ins](../user-help/my-account-portal-sign-ins-page.md#confirm-unusual-activity).
 
---

### <a name="sap-successfactors-hr-driven-user-provisioning-to-azure-ad-is-now-generally-available"></a>Zřizování uživatelů SAP SuccessFactors na základě lidských zdrojů v Azure AD je teď všeobecně dostupné.

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** Správa životního cyklu identit
 
Službu SAP SuccessFactors teď můžete integrovat jako autoritativní zdroj identity s Azure AD a automatizovat kompletní životní cyklus identity pomocí událostí souvisejících s hodinami, jako jsou noví zaměstnanci a ukončení, při zřizování a rušení zřizování účtů v Azure AD. 

Další informace o tom, jak nakonfigurovat příchozí zřizování SAP SuccessFactors do služby Azure AD, najdete v kurzu [konfigurace SAP SuccessFactors pro zřizování uživatelů služby Active Directory](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md).
 
---

### <a name="custom-open-id-connect-ms-graph-api-support-for-azure-ad-b2c"></a>Vlastní otevřené ID připojit podporu MS Graph API pro Azure AD B2C

**Zadejte:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C
 
Dřív se vlastní Open ID Connect Provider mohl přidat nebo spravovat jenom prostřednictvím Azure Portal. Nyní mohou zákazníci Azure AD B2C přidávat a spravovat prostřednictvím rozhraní API beta verze Microsoft Graph. Informace o tom, jak nakonfigurovat tento prostředek pomocí rozhraní API, najdete v tématu [identityProvider Resource Type](/graph/api/resources/identityprovider?view=graph-rest-beta).
 
---

### <a name="assign-azure-ad-built-in-roles-to-cloud-groups"></a>Přiřazení předdefinovaných rolí Azure AD ke skupinám cloudu

**Zadejte:** Nová funkce  
**Kategorie služby:** Role Azure AD  
**Schopnost produktu:** Access Control

Pomocí této nové funkce teď můžete přiřadit předdefinované role Azure AD ke skupinám cloudu. Můžete například přiřadit roli správce služby SharePoint k Contoso_SharePoint_Admins skupině. Správce osobních informací můžete použít také k seskupení oprávněných členů role místo udělení přístupu. Informace o tom, jak nakonfigurovat tuto funkci, najdete v tématu [použití skupin cloudu ke správě přiřazení rolí v Azure Active Directory (Preview)](../roles/groups-concept.md).
 
---

### <a name="insights-business-leader-built-in-role-now-available"></a>Předdefinovaná role Insights Business vedoucího společnosti je teď dostupná.

**Zadejte:** Nová funkce  
**Kategorie služby:** Role Azure AD  
**Schopnost produktu:** Access Control
 
Uživatelé v roli vedoucí firmy Insights mají přístup k sadě řídicích panelů a přehledů prostřednictvím [aplikace M365 Insights](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). To zahrnuje plný přístup ke všem řídicím panelům a prezentovaným přehledům a funkcím průzkumu dat. Uživatelé v této roli ale nemají přístup k nastavení konfigurace produktu, což je zodpovědností role správce Insights. Další informace o této roli najdete v tématu [oprávnění role správce v Azure Active Directory](../roles/permissions-reference.md#insights-business-leader)
 
---

### <a name="insights-administrator-built-in-role-now-available"></a>Integrovaná role správce Insights je teď dostupná.

**Zadejte:** Nová funkce  
**Kategorie služby:** Role Azure AD  
**Schopnost produktu:** Access Control
 
Uživatelé v roli správce Insights mají přístup k plné sadě možností správy v [aplikaci M365 Insights](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). Uživatel v této roli může číst informace o adresáři, monitorovat stav služby, lístky podpory souborů a přistupovat k aspektům nastavení pro správce Insights. Další informace o této roli najdete v tématu [oprávnění role správce v Azure Active Directory](../roles/permissions-reference.md#insights-administrator)
 
--- 

### <a name="application-admin-and-cloud-application-admin-can-manage-extension-properties-of-applications"></a>Správce aplikace a správce cloudových aplikací můžou spravovat vlastnosti rozšíření aplikací.

**Zadejte:** Změněná funkce  
**Kategorie služby:** Role Azure AD  
**Schopnost produktu:** Access Control
 
Dříve mohl [vlastnost Extension](/graph/api/application-post-extensionproperty?view=graph-rest-beta&tabs=http)spravovat jenom globální správce. Nyní tuto funkci povolujeme také pro Správce aplikací a správce cloudových aplikací.
 
---

### <a name="mim-2016-sp2-hotfix-462630-and-connectors-1113010"></a>1.1.1301.0 4.6.263.0 a konektory MIM 2016 SP2

**Zadejte:** Změněná funkce  
**Kategorie služby:** Microsoft Identity Manager  
**Schopnost produktu:** Správa životního cyklu identit

[Kumulativní balíček oprav hotfix (Build 4.6.263.0)](https://support.microsoft.com/help/4576473/hotfix-rollup-package-build-4-6-263-0-is-available-for-microsoft-ident) je k dispozici pro Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Tento balíček kumulativní aktualizace obsahuje aktualizace pro MIM CM, Správce synchronizace MIM a komponenty PAM. Kromě toho sestavování obecných konektorů MIM 1.1.1301.0 zahrnuje aktualizace konektoru grafu.

---
 
## <a name="july-2020"></a>Červenec 2020

### <a name="as-an-it-admin-i-want-to-target-client-apps-using-conditional-access"></a>Jako správce IT chci cílit na klientské aplikace pomocí podmíněného přístupu.

**Zadejte:** Plánování změn   
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
V případě, že je v podmíněném přístupu dostupná verze GA klientských aplikací, nové zásady se teď ve výchozím nastavení použijí pro všechny klientské aplikace. Patří sem starší klienti ověřování. Existující zásady zůstanou beze změny, ale přepínač *Konfigurovat ano/ne* se odebere z existujících zásad, aby bylo možné snadno zjistit, které klientské aplikace jsou zásadami aplikovány. 

Při vytváření nové zásady se ujistěte, že vyloučíte uživatele a účty služeb, které pořád používají starší verze ověřování. Pokud to neuděláte, budou zablokovány. [Přečtěte si další informace](../conditional-access/concept-conditional-access-conditions.md).
 
---

### <a name="upcoming-scim-compliance-fixes"></a>Nadcházející opravy dodržování předpisů SCIM

**Zadejte:** Plánování změn  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** Správa životního cyklu identit
 
Služba zřizování Azure AD využívá standard SCIM pro integraci s aplikacemi. Naše implementace standardu SCIM se vyvíjí a očekáváme, že provedeme změny v našem chování, jak provedeme operace opravy, a také nastavit vlastnost "aktivní" na prostředku. [Přečtěte si další informace](../app-provisioning/application-provisioning-config-problem-scim-compatibility.md).
 
---

### <a name="group-owner-setting-on-azure-admin-portal-will-be-changed"></a>Nastavení vlastník skupiny na portálu pro správu Azure se změní.

**Zadejte:** Plánování změn  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Prostřednictvím

Na portálu pro správu Azure a přístupovém panelu můžete nakonfigurovat nastavení vlastníka na stránce Obecné nastavení skupiny, aby se omezila oprávnění přiřazování vlastníků k omezené skupině uživatelů. Brzy bude možné přiřazovat oprávnění vlastníka skupiny nejen na těchto dvou portálech UX, ale taky vymáhat zásady na back-endu, aby poskytovaly konzistentní chování v rámci koncových bodů, jako je například PowerShell a Microsoft Graph. 

Začneme deaktivovat aktuální nastavení pro zákazníky, kteří ho nepoužívají, a nabídne vám možnost určit v následujících několika měsících možnost oboru uživatelů pro oprávnění vlastníka skupiny. Pokyny k aktualizaci nastavení skupiny najdete v tématu Úprava informací o skupině pomocí [Azure Active Directory](./active-directory-groups-settings-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context).

---

### <a name="azure-active-directory-registration-service-is-ending-support-for-tls-10-and-11"></a>Služba Azure Active Directory Registration Service končí na podporu TLS 1,0 a 1,1

**Zadejte:** Plánování změn  
**Kategorie služby:** Registrace a Správa zařízení  
**Schopnost produktu:** Platformy

Protokol TLS (Transport Layer Security) 1,2 a servery aktualizací a klienti budou brzy komunikovat se službou Azure Active Directory Device Registration. Podpora TLS 1,0 a 1,1 pro komunikaci se službou Device Registration Service pro Azure AD vyřadí:
- 31. srpna 2020 ve všech cloudech v svrchovaném moři (na úrovni RSZ, DoD atd.)
- 30. října 2020 ve všech komerčních cloudech

[Přečtěte si další informace](../devices/reference-device-registration-tls-1-2.md) o TLS 1,2 pro službu Azure AD Registration Service.

---

### <a name="windows-hello-for-business-sign-ins-visible-in-azure-ad-sign-in-logs"></a>Přihlašování Windows Hello pro firmy zobrazené v protokolech přihlášení Azure AD

**Zadejte:** Určí  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav
 
Windows Hello pro firmy umožňuje koncovým uživatelům přihlašovat se k počítačům s Windows pomocí gesta (například PIN nebo biometriky). Správci Azure AD můžou chtít odlišit přihlášení Windows Hello pro firmy od jiných přihlášení k Windows v rámci cesty organizace k ověřování pomocí hesla. 

Správci teď můžou zjistit, jestli ověřování Windows používalo Windows Hello pro firmy, a to tak, že na kartě Podrobnosti ověření pro přihlašovací událost Windows v okně Azure AD Sign-Ins v Azure Portal. Ověřování Windows Hello pro firmy bude v poli Metoda ověřování zahrnovat "WindowsHelloForBusiness". Další informace o interpretaci Sign-Inch protokolů najdete v [dokumentaci k přihlašovacím protokolům](../reports-monitoring/concept-sign-ins.md).
 
---

### <a name="fixes-to-group-deletion-behavior-and-performance-improvements"></a>Opravy chování při odstraňování skupin a zlepšení výkonu

**Zadejte:** Určí  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** Správa životního cyklu identit
 
Když jste dřív změnili skupinu z "in-Scope" na "out-of-Scope" a správce klikl na restart před dokončením změny, objekt skupiny se neodstraní. Objekt skupiny se teď odstraní z cílové aplikace, když se dostane mimo rozsah (zakázaný, odstraněný, nepřiřazený nebo neprojde filtr oboru). [Přečtěte si další informace](../app-provisioning/how-provisioning-works.md#incremental-cycles).
 
---

### <a name="public-preview-admins-can-now-add-custom-content-in-the-email-to-reviewers-when-creating-an-access-review"></a>Public Preview: Správci teď můžou do e-mailu přidat vlastní obsah při vytváření kontroly přístupu.

**Zadejte:** Nová funkce  
**Kategorie služby:** Kontroly přístupu  
**Schopnost produktu:** Zásady správného řízení identity
 
Při vytvoření nové kontroly přístupu obdrží kontrolor e-mail s žádostí o dokončení kontroly přístupu. Řada našich zákazníků požádala o možnost přidat vlastní obsah do e-mailu, jako jsou kontaktní údaje nebo jiný další podpůrný obsah, který vás provede kontrolorem. 

V rámci veřejné verze Preview můžou správci zadat vlastní obsah e-mailu odeslanému revidujícím přidáním obsahu v části Upřesnit v tématu kontroly přístupu Azure AD. Pokyny k vytváření kontrol přístupu najdete v tématu [Vytvoření kontroly přístupu skupin a aplikací v prohlídekch Azure AD Access](../governance/create-access-review.md).
 
---

### <a name="authorization-code-flow-for-single-page-apps-available"></a>Tok autorizačního kódu pro jednostránkové aplikace k dispozici

**Zadejte:** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Vývojářské prostředí
 
Vzhledem k tomu, že se v moderních prohlížečích používají omezení souborů cookie třetích stran, jako je Safari ITP, bude muset jednostránkové používat tok autorizačního kódu místo implicitního toku pro udržování jednotného přihlašování a MSAL.js v 2. x teď bude podporovat tok autorizačního kódu. 

Existují odpovídající aktualizace Azure Portal, takže můžete zabezpečené ověřování hesla aktualizovat tak, aby bylo typu "Spa", a používat tok kódu ověřování. Další pokyny najdete v tématech [přihlášení uživatelů a získání přístupového tokenu v zabezpečeném kódu v JavaScriptu pomocí toku kódu ověřování](../develop/quickstart-v2-javascript-auth-code.md) .
 
---

### <a name="azure-ad-application-proxy-now-supports-the-remote-desktop-services-web-client"></a>Služba Azure Proxy aplikací služby AD nyní podporuje webového klienta vzdálené plochy.

**Zadejte:** Nová funkce  
**Kategorie služby:** Proxy aplikace  
**Schopnost produktu:** Access Control

Služba Azure Proxy aplikací služby AD nyní podporuje webového klienta vzdálené plochy (RDS). Webový klient RDS umožňuje uživatelům přístup k infrastruktuře vzdálené plochy prostřednictvím libovolného prohlížeče podporujícího HTLM5, jako je Microsoft Edge, Internet Explorer 11, Google Chrome atd. Uživatelé můžou komunikovat se vzdálenými aplikacemi nebo počítači, jako by se k nim měli místní zařízení odkudkoli. Pomocí Azure Proxy aplikací služby AD můžete zvýšit zabezpečení nasazení RDS tím, že vynucujete zásady podmíněného ověřování a podmíněného přístupu pro všechny typy bohatých klientských aplikací. Pokyny najdete v tématu [publikování vzdálené plochy pomocí Azure proxy aplikací služby AD](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md).
 
---

### <a name="next-generation-azure-ad-b2c-user-flows-in-public-preview"></a>Nové generace Azure AD B2C toky uživatelů ve verzi Public Preview

**Zadejte:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C
 
Zjednodušené uživatelské prostředí nabízí paritu funkcí s funkcemi verze Preview a je domovkou pro všechny nové funkce. Uživatelé budou moci povolit nové funkce v rámci stejného toku uživatelů a snížit tak nutnost vytvářet více verzí s každou novou verzí funkce. Nakonec nově uživatelsky přívětivé uživatelské prostředí zjednodušuje výběr a vytváření toků uživatelů. Vyzkoušejte si to hned [vytvořením toku uživatele](../../active-directory-b2c/tutorial-create-user-flows.md). 

Další informace o tokůch uživatelů najdete [v tématu verze toku uživatele v Azure Active Directory B2C](../../active-directory-b2c/user-flow-versions.md#:~:text=    User flow  ,account. Usi ...  1 more rows ).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---july-2020"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – červenec 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
V červenci 2020 jsme do Galerie aplikací přidali následující 55 nových aplikací s podporou federace:

[CLAP své ruce](http://www.rmit.com.ar/), [Appreiz](https://microsoftteams.appreiz.com/), [Inextor trezor](https://inexto.com/inexto-suite/inextor), [Beekast](https://my.beekast.com/), [Templafy OpenID Connect](https://app.templafy.com/), [PeterConnects recepční](https://msteams.peterconnects.com/), [AlohaCloud](https://appfusions.alohacloud.com/auth), [Control Tower](https://bpm.tnxcorp.com/sso/microsoft), [Cocoom](https://start.cocoom.com/), [mince konstrukcí Cloud](https://sso.coinsconstructioncloud.com/#login/), [Medxnote MT](https://task.teamsmain.medx.im/authorization), [Reflekt](https://reflekt.konsolute.com/login), [rever](https://app.reverscore.net/access), [MyCompanyArchive](https://login.mycompanyarchive.com/), [GReminders](https://app.greminders.com/o365-oauth), [Titanfile, Wootric](../saas-apps/titanfile-tutorial.md) [, SolarWinds](../saas-apps/wootric-tutorial.md) [Orion](https://support.solarwinds.com/SuccessCenter/s/orion-platform?language=en_US),  [OpenText adresářových služeb](../saas-apps/opentext-directory-services-tutorial.md), [DataSite](../saas-apps/datasite-tutorial.md) [, BlogIn, IntSights](../saas-apps/blogin-tutorial.md) [, KPIFIRE](../saas-apps/intsights-tutorial.md) [, TextLine](../saas-apps/kpifire-tutorial.md) [,](../saas-apps/textline-tutorial.md) [Cloud Academy – SSO](../saas-apps/cloud-academy-sso-tutorial.md), [Community Spark](../saas-apps/community-spark-tutorial.md), [ChatWork](../saas-apps/chatwork-tutorial.md) [, CloudSign,](../saas-apps/cloudsign-tutorial.md) [C3M Cloud Control](../saas-apps/c3m-cloud-control-tutorial.md), [SmartHR](https://smarthr.jp/), [NumlyEngage™](../saas-apps/numlyengage-tutorial.md), [Michigan jednotné přihlašování ke službě data hub](../saas-apps/michigan-data-hub-single-sign-on-tutorial.md), [výstup](../saas-apps/egress-tutorial.md), [SendSafely](../saas-apps/sendsafely-tutorial.md), [Eletive](https://app.eletive.com/), [pravá ruka kyberbezpečnosti ADI](https://right-hand.ai/), [Fyde Enterprise Authentication](https://enterprise.fyde.com/), [verme](../saas-apps/verme-tutorial.md), [lenses.IO](../saas-apps/lensesio-tutorial.md), [chvilka](../saas-apps/momenta-tutorial.md), [zvýšení](https://app.uprise.co/sign-in), [Q](https://q.moduleq.com/login), [CloudCords](../saas-apps/cloudcords-tutorial.md), [Tellme robot](https://tellme365liteweb.azurewebsites.net/), [inspirovat](https://app.inspiresoftware.com/), [Maverics identity Orchestrator – konektor SAML](https://www.strata.io/identity-fabric/), [Smartschool (systém pro správu školy)](https://smartschoolz.com/login), [Zepto – inteligentní Timekeeping](https://user.zepto-ai.com/signin), [Studi.ly](https://studi.ly/), [Trackplan](http://www.trackplanfm.com/), [Skedda](../saas-apps/skedda-tutorial.md), [WhosOnLocation](../saas-apps/whos-on-location-tutorial.md), [Coggle](../saas-apps/coggle-tutorial.md), [kemp LoadMaster](https://kemptechnologies.com/cloud-load-balancer/), [jednotné přihlašování browserstackem](../saas-apps/browserstack-single-sign-on-tutorial.md)

Dokumentaci pro všechny aplikace můžete také najít tady. https://aka.ms/AppsTutorial

Pokud chcete zobrazit seznam aplikací v galerii aplikací Azure AD, přečtěte si prosím podrobnosti. https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---july-2020"></a>Nové zřizovací konektory v galerii aplikací Azure AD – červenec 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** integrace třetích stran

Nyní můžete automatizovat vytváření, aktualizaci a odstraňování uživatelských účtů pro nově integrovanou [výuku LinkedInu](../saas-apps/linkedin-learning-provisioning-tutorial.md)aplikace.

Další informace o tom, jak lépe zabezpečit vaši organizaci pomocí automatizovaného zřizování uživatelských účtů, najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="view-role-assignments-across-all-scopes-and-ability-to-download-them-to-a-csv-file"></a>Zobrazení přiřazení rolí napříč všemi obory a možnost je stáhnout do souboru CSV

**Zadejte:** Změněná funkce  
**Kategorie služby:** Role Azure AD  
**Schopnost produktu:** Access Control
 
Nyní můžete zobrazit přiřazení rolí napříč všemi obory role na kartě role a správci na portálu Azure AD. Tato přiřazení rolí můžete také stáhnout pro každou roli do souboru CSV. Pokyny k zobrazení a přidání přiřazení rolí najdete v tématu [zobrazení a přiřazení rolí správce v Azure Active Directory](../roles/manage-roles-portal.md).
 
---

### <a name="azure-multi-factor-authentication-software-development-azure-mfa-sdk-deprecation"></a>Azure Multi-Factor Authentication Software Development (Azure MFA SDK) – vyřazení

**Zadejte:** Zastaralé  
**Kategorie služby:** VÍCEFAKTOROVÉHO  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
Vývoj softwaru Azure Multi-Factor Authentication (Azure MFA SDK) dosáhl konce životnosti v listopadu 14 2018, jak je uvedeno poprvé v listopadu 2017. Microsoft vypíná platnost služby SDK až do 30. září 2020. Všechna volání, která sada SDK provede, selžou.

Pokud vaše organizace používá sadu Azure MFA SDK, je nutné migrovat do 30. září 2020:
- Azure MFA SDK pro MIM: Pokud používáte sadu SDK s MIM, měli byste migrovat na Azure MFA Server a aktivovat Privileged Access Management (PAM) podle těchto [pokynů](/microsoft-identity-manager/working-with-mfaserver-for-mim).   
- Azure MFA SDK pro vlastní aplikace: Zvažte integraci vaší aplikace do služby Azure AD a použití podmíněného přístupu k prosazování MFA. Pokud chcete začít, podívejte se na tuto [stránku](../manage-apps/plan-an-application-integration.md). 

---

## <a name="june-2020"></a>Červen 2020 

### <a name="user-risk-condition-in-conditional-access-policy"></a>Podmínka rizika uživatele v zásadách podmíněného přístupu

**Zadejte:** Plánování změn  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity & ochrana
 

Podpora rizik uživatelů v zásadách podmíněného přístupu Azure AD umožňuje vytvořit několik uživatelských zásad založených na rizicích. Pro různé uživatele a aplikace se můžou vyžadovat jiné minimální úrovně rizika uživatele. Na základě rizika uživatele můžete vytvořit zásady pro blokování přístupu, vyžadovat vícefaktorové ověřování, zabezpečenou změnu hesla nebo přesměrovat na Microsoft Cloud App Security k vynucení zásad relace, jako je například další auditování.

Podmínka rizika uživatele vyžaduje Azure AD Premium P2, protože používá Azure Identity Protection, což je nabídka P2. Další informace o podmíněném přístupu najdete v [dokumentaci k podmíněnému přístupu Azure AD](../conditional-access/index.yml).

---

### <a name="saml-sso-now-supports-apps-that-require-spnamequalifier-to-be-set-when-requested"></a>Jednotné přihlašování SAML teď podporuje aplikace, které vyžadují, aby se v případě vyžádání nastavil SPNameQualifier

**Zadejte:** Určí  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ
 
Některé aplikace SAML vyžadují, aby se v předmětu kontrolního výrazu vrátilo SPNameQualifier, pokud je to požadováno. Služba Azure AD teď odpoví správně, když se v zásadách NameID žádosti vyžádá SPNameQualifier. To funguje taky pro přihlášení iniciované pomocí SP a zahájí se přihlášení IdP.  Další informace o protokolu SAML v Azure Active Directory najdete v tématu [Single Sign-On SAML Protocol](../develop/single-sign-on-saml-protocol.md).

---

### <a name="azure-ad-b2b-collaboration-supports-inviting-msa-and-google-users-in-azure-government-tenants"></a>Spolupráce B2B Azure AD podporuje pozvání uživatelů MSA a Google v Azure Government tenantů.

**Zadejte:** Nová funkce  
**Kategorie služby:** B2B  
**Schopnost produktu:** B2B/B2C
 

Klienti Azure Government pomocí funkcí spolupráce B2B teď můžou pozvat uživatele, kteří mají účet Microsoft nebo Google. Pokud chcete zjistit, jestli váš tenant může tyto možnosti využít, postupujte podle pokynů v tématu [Jak poznám, jestli je spolupráce B2B dostupná ve svém Tenantovi Azure USA?](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)

 
---
 
### <a name="user-object-in-ms-graph-v1-now-includes-externaluserstate-and-externaluserstatechangeddatetime-properties"></a>Objekt uživatele v MS Graph v1 nyní zahrnuje vlastnosti externalUserState a externalUserStateChangedDateTime.

**Zadejte:** Nová funkce  
**Kategorie služby:** B2B  
**Schopnost produktu:** B2B/B2C
 

Vlastnosti externalUserState a externalUserStateChangedDateTime lze použít k vyhledání pozvaných hostů B2B, kteří ještě nepřijali své pozvánky, a také automatizaci sestavení, jako je odstranění uživatelů, kteří nepřijali své pozvánky po určitém počtu dnů. Tyto vlastnosti jsou teď dostupné v MS Graph v1. Pokyny k používání těchto vlastností najdete v tématu [typ prostředku uživatele](/graph/api/resources/user?view=graph-rest-1.0).
 
---

### <a name="manage-authentication-sessions-in-azure-ad-conditional-access-is-now-generally-available"></a>Správa relací ověřování ve službě Azure AD podmíněný přístup je teď všeobecně dostupná.

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
Možnosti správy relace ověřování vám umožňují nakonfigurovat, jak často uživatelé potřebují zadat přihlašovací údaje a jestli potřebují zadat přihlašovací údaje po zavření a opětovném otevření prohlížečů, aby ve vašem prostředí nabízely větší zabezpečení a flexibilitu.
 
Kromě toho se Správa relace ověřování používá jenom pro první ověření v připojené službě Azure AD, připojené k hybridní službě Azure AD a k zařízením registrovaným v Azure AD. Nyní se Správa relace ověřování uplatní i na MFA. Další informace najdete v tématu [Konfigurace správy relace ověřování pomocí podmíněného přístupu](../conditional-access/howto-conditional-access-session-lifetime.md).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2020"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – červen 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
V červnu 2020 jsme do Galerie aplikací přidali následující 29 nových aplikací s podporou federace:

[Shopify plus](../saas-apps/shopify-plus-tutorial.md), [Ekarda](../saas-apps/ekarda-tutorial.md), [MailGates](../saas-apps/mailgates-tutorial.md), [BullseyeTDP](../saas-apps/bullseyetdp-tutorial.md), [raketa](../saas-apps/raketa-tutorial.md), [segment](../saas-apps/segment-tutorial.md), [AI auditor](https://www.mindbridge.ai/products/ai-auditor/), [Pobuca Connect](https://app.pobu.ca/), [proto.IO](../saas-apps/proto.io-tutorial.md), [Gatekeeper](https://www.gatekeeperhq.com/)Webmarketo, [Plánovač centra](../saas-apps/hub-planner-tutorial.md), Ansira, možnosti [pro uvedení na trh](https://ansira.com/technology/channel-engagement), [IBM Digital Business Automation v cloudu](../saas-apps/ibm-digital-business-automation-on-cloud-tutorial.md), [Kisi fyzické zabezpečení](../saas-apps/kisi-physical-security-tutorial.md), [ViewpointOne](https://team.viewpoint.com/), [IntelligenceBank](../saas-apps/intelligencebank-tutorial.md), [pymetrics](../saas-apps/pymetrics-tutorial.md), [nula](https://www.teamzero.com/) [Curricula SAML](../saas-apps/curricula-saml-tutorial.md), [instanice](https://instation.invillia.com/), [EDX for Business SAML 2,0 Integration](../saas-apps/edx-for-business-saml-integration-tutorial.md), mooc SmartKargo, PKIsigning [Perforce Helix Core - Helix Authentication Service](../saas-apps/perforce-helix-core-tutorial.md) [, SiteIntel](../saas-apps/smartkargo-tutorial.md), [Perforce, Helix](../saas-apps/siteintel-tutorial.md) [, Cloud](../saas-apps/field-id-tutorial.md) [,](https://cloud.metacompliance.com/) [MOOC Office 365](https://mooc.office365-training.com/en/) [PKIsigning platform](https://platform.pkisigning.nl/) [Helix 365](https://smallstep.com/sso-ssh/)  

Dokumentaci ke všem aplikacím si můžete také najít tady https://aka.ms/AppsTutorial . Pokud chcete zobrazit seznam aplikací v galerii aplikací Azure AD, přečtěte si prosím podrobnosti tady: https://aka.ms/AzureADAppRequest .

---

### <a name="api-connectors-for-external-identities-self-service-sign-up-are-now-in-public-preview"></a>Konektory rozhraní API pro samoobslužné registrace externích identit jsou teď ve verzi Public Preview.

**Zadejte:** Nová funkce  
**Kategorie služby:** B2B  
**Schopnost produktu:** B2B/B2C
 
Externí identity API konektory umožňují využívat webová rozhraní API pro integraci samoobslužné registrace do externích cloudových systémů. To znamená, že teď můžete vyvolat webová rozhraní API jako konkrétní kroky v toku registrace a aktivovat tak cloudové vlastní pracovní postupy. Konektory rozhraní API můžete například použít k těmto akcím:

- Proveďte integraci s vlastními pracovními postupy schvalování.
- Provést kontrolu identity
- Ověřit data vstupu uživatele
- Přepsat atributy uživatele
- Spustit vlastní obchodní logiku

Další informace o všech dostupných prostředích pomocí konektorů rozhraní API najdete v tématu [Použití konektorů rozhraní API k přizpůsobení a rozšiřování samoobslužné registrace](../external-identities/api-connectors-overview.md)nebo [přizpůsobení samoobslužné registrace externích identit pomocí integrace webového rozhraní API](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-external-identities-self-service-sign-up-with-web-api/ba-p/1257364#.XvNz2fImuQg.linkedin).
 
---

### <a name="provision-on-demand-and-get-users-into-your-apps-in-seconds"></a>Zřizování na vyžádání a získání uživatelů do vašich aplikací během několika sekund

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** Správa životního cyklu identit
 
Služba zřizování Azure AD v současné době pracuje cyklicky. Služba se spouští každých 40 minut. [Funkce zřizování na vyžádání](https://aka.ms/provisionondemand) umožňuje vybrat uživatele a zřídit je během několika sekund. Díky této možnosti můžete rychle řešit problémy s zřizováním, aniž byste museli provést restart, aby se cyklus zřizování znovu spustil. 
 
---

### <a name="new-permission-for-using-azure-ad-entitlement-management-in-graph"></a>Nové oprávnění pro používání správy nároků Azure AD v grafu

**Zadejte:** Nová funkce  
**Kategorie služby:** Jiná  
**Schopnost produktu:** Správa nároků
 
Nové delegované oprávnění EntitlementManagement. Read. All je teď k dispozici pro použití s rozhraním API pro správu oprávnění ve službě Microsoft Graph beta. Další informace o dostupných rozhraních API najdete v tématu [práce s rozhraním API pro správu opravňujících k Azure AD](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta).

---

### <a name="identity-protection-apis-available-in-v10"></a>Rozhraní API pro ochranu identit dostupné v 1.0

**Zadejte:** Nová funkce  
**Kategorie služby:** Ochrana identity  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
Rozhraní API pro Microsoft Graph riskyUsers a riskDetections jsou teď všeobecně dostupná. Teď, když jsou k dispozici na koncovém bodu v 1.0, vás pozvaní, abyste je mohli používat v produkčním prostředí. Další informace najdete v [dokumentaci Microsoft Graph](/graph/api/resources/identityprotectionroot?view=graph-rest-1.0).
 
---

### <a name="sensitivity-labels-to-apply-policies-to-microsoft-365-groups-is-now-generally-available"></a>Popisky citlivosti na uplatnění zásad na Microsoft 365 skupiny jsou teď všeobecně dostupné.

**Zadejte:** Nová funkce  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Prostřednictvím
 

Nyní můžete vytvořit popisky citlivosti a použít nastavení popisku k uplatnění zásad pro Microsoft 365 skupiny, včetně ochrany osobních údajů (veřejných nebo privátních) a zásad přístupu k externím uživatelům. Můžete vytvořit popisek se zásadami ochrany osobních údajů jako privátní a zásady přístupu externích uživatelů, aby nepovolovaly přidávání uživatelů typu Host. Když uživatel použije tento popisek na skupinu, skupina bude soukromá a do skupiny nebude povoleno přidávat žádné uživatele typu Host. 

Popisky citlivosti jsou důležité pro ochranu důležitých podnikových dat a umožňují vám spravovat skupiny ve velkém měřítku, a to v souladu a zabezpečeném způsobem. Pokyny k používání popisků citlivosti najdete [v tématu Přiřazení popisků citlivosti Microsoft 365 skupinám v Azure Active Directory (Preview)](../enterprise-users/groups-assign-sensitivity-labels.md).
 
---

### <a name="updates-to-support-for-microsoft-identity-manager-for-azure-ad-premium-customers"></a>Aktualizace pro podporu Microsoft Identity Manager pro zákazníky Azure AD Premium

**Zadejte:** Změněná funkce  
**Kategorie služby:** Microsoft Identity Manager  
**Schopnost produktu:** Správa životního cyklu identit
 
Podpora Azure je teď k dispozici pro komponenty integrace služby Azure AD Microsoft Identity Manager 2016 na konci rozšířené podpory pro Microsoft Identity Manager 2016. Další informace najdete v článku [o podpoře aktualizace pro Azure AD Premium zákazníky pomocí Microsoft Identity Manager](/microsoft-identity-manager/support-update-for-azure-active-directory-premium-customers).

---

### <a name="the-use-of-group-membership-conditions-in-sso-claims-configuration-is-increased"></a>Bylo zvýšeno použití podmínek členství ve skupině v konfiguraci deklarací SSO.

**Zadejte:** Změněná funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ
 
Dříve počet skupin, které můžete použít při podmíněné změně deklarací identity na základě členství ve skupině v rámci každé konfigurace jedné aplikace, byl omezený na 10. Použití podmínek členství ve skupině v konfiguraci deklarací SSO se teď zvýšilo na maximálně 50 skupin. Další informace o tom, jak nakonfigurovat deklarace identity, najdete v tématu [Konfigurace deklarací pro jednotné přihlašování k podnikovým aplikacím](../develop/active-directory-saml-claims-customization.md#emitting-claims-based-on-conditions). 

---

### <a name="enabling-basic-formatting-on-the-sign-in-page-text-component-in-company-branding"></a>Povolení základního formátování na součásti textu přihlašovací stránky v brandingu společnosti.

**Zadejte:** Změněná funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů
 
Funkce brandingu společnosti na přihlašovacím jméně Azure AD/Microsoft 365 se aktualizovala tak, aby zákazník mohl přidávat hypertextové odkazy a jednoduché formátování, včetně tučného písma, podtržení a kurzívy. Pokyny k použití této funkce najdete v tématu [Přidání brandingu na přihlašovací stránku Azure Active Directory vaší organizace](./customize-branding.md).

---

### <a name="provisioning-performance-improvements"></a>Vylepšení výkonu zřizování

**Zadejte:** Změněná funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** Správa životního cyklu identit
 
Služba zřizování se aktualizovala tak, aby snižovala čas dokončení [přírůstkového cyklu](../app-provisioning/how-provisioning-works.md#incremental-cycles) . To znamená, že uživatelé a skupiny budou k aplikacím zřízeni rychleji než dříve. Všem novým zřizovacím úlohám vytvořeným po 6/10/2020 budou automaticky těžit z vylepšení výkonu. Všechny aplikace, které jsou nakonfigurované pro zřizování před 6/10/2020, se budou muset restartovat jednou po 6/10/2020, aby se využilo zvýšení výkonu. 

---

### <a name="announcing-the-deprecation-of-adal-and-ms-graph-parity"></a>Oznamujeme vyřazení ADAL a parity MS graphu.

**Zadejte:** Zastaralé  
**Kategorie služby:** NENÍ K DISPOZICI  
**Schopnost produktu:** Správa životního cyklu zařízení

Teď, když jsou k dispozici knihovny Microsoft Authentication Library (MSAL), už nepřidáme Azure Active Directory do Authentication Library (ADAL) nové funkce a budou ukončeny opravy zabezpečení na 30. června 2022. Další informace o tom, jak migrovat na MSAL, najdete v článku [migrace aplikací do knihovny Microsoft Authentication Library (MSAL)](../develop/msal-migration.md).

Kromě toho jsme dokončili práci, aby všechny funkce grafu služby Azure AD byly dostupné prostřednictvím MS graphu. Rozhraní Azure AD Graph API tak budou přijímat jenom opravu chyb a opravy zabezpečení až do 30. června 2022. Další informace najdete v tématu [aktualizace aplikací pro použití knihovny Microsoft Authentication Library a rozhraní Microsoft Graph API](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363) .
 
---
 
## <a name="may-2020"></a>Květen 2020

### <a name="retirement-of-properties-in-signins-riskyusers-and-riskdetections-apis"></a>Vyřazení vlastností v rozhraních API nenašla, riskyUsers a riskDetections

**Zadejte:** Plánování změn  
**Kategorie služby:** Ochrana identity  
**Schopnost produktu:** Zabezpečení identity & ochrana

V současné době výčtové typy slouží k reprezentaci vlastnosti riskType v rozhraní riskDetections API i v riskyUserHistoryItem (ve verzi Preview). Výčtové typy se používají také pro vlastnost riskEventTypes v rozhraní nenašla API. Po přeposlání budeme tyto vlastnosti zastupovat jako řetězce. 

Zákazníci by měli přejít na vlastnost riskEventType v rozhraní beta riskDetections a riskyUserHistoryItem API a na vlastnost riskEventTypes_v2 v rozhraní beta nenašla API do 9. září 2020. V tomto datu bude vycházet z aktuálních vlastností riskType a riskEventTypes. Další informace najdete [v tématu změny vlastností rizikových událostí a rozhraní API ochrany identity na Microsoft Graph](https://developer.microsoft.com/graph/blogs/changes-to-risk-event-properties-and-identity-protection-apis-on-microsoft-graph/).

--- 

### <a name="deprecation-of-riskeventtypes-property-in-signins-v10-api-on-microsoft-graph"></a>Vyřazení vlastnosti riskEventTypes v rozhraní API nenašla v 1.0 v Microsoft Graph

**Zadejte:** Plánování změn  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Zabezpečení identity & ochrana

Výčtové typy budou přepnuty na typy řetězců při reprezentaci vlastností rizikové události Microsoft Graph září 2020. Kromě ovlivnění rozhraní API verze Preview Tato změna ovlivní také nenašla rozhraní API v produkčním prostředí.

Zavedli jsme novou vlastnost riskEventsTypes_v2 (String) na rozhraní API pro nenašla v 1.0. Aktuální vlastnost riskEventTypes (Enum) vyřadíme 11. června 2022 v souladu s našimi zásadami pro vyřazení z provozu Microsoft Graph. Zákazníci by měli přejít na vlastnost riskEventTypes_v2 v rozhraní nenašla API v 1.0 od 11. června 2022. Další informace najdete v tématu vyřazení [vlastnosti riskEventTypes v rozhraní API nenašla v 1.0 na Microsoft Graph](https://developer.microsoft.com/graph/blogs/deprecation-of-riskeventtypes-property-in-signins-v1-0-api-on-microsoft-graph//).

--- 

### <a name="upcoming-changes-to-mfa-email-notifications"></a>Nadcházející změny e-mailových oznámení MFA

**Zadejte:** Plánování změn  
**Kategorie služby:** VÍCEFAKTOROVÉHO  
**Schopnost produktu:** Zabezpečení identity & ochrana
 

U e-mailových oznámení pro Cloud MFA provádíme tyto změny:

E-mailová oznámení budou odesílána z následující adresy: azure-noreply@microsoft.com a msonlineservicesteam@microsoftonline.com . Aktualizujeme obsah e-mailů s výstrahou týkající se podvodů, aby lépe označovaly požadované kroky odblokování použití.

---

### <a name="new-self-service-sign-up-for-users-in-federated-domains-who-cant-access-microsoft-teams-because-they-arent-synced-to-azure-active-directory"></a>Nové samoobslužné registrace pro uživatele ve federovaných doménách, kteří nemají přístup k Microsoft teams, protože nejsou synchronizované s Azure Active Directory.

**Zadejte:** Plánování změn  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů
 

V současné době jsou uživatelé, kteří jsou v doméně federované v Azure AD, ale nesynchronizované do tenanta, nemůžou získat přístup k týmům. Od konce června Tato nová funkce jim umožní učinit rozšířením stávající funkce pro registraci e-mailu. To umožní uživatelům, kteří se budou přihlašovat k federovaným IdP, ale kteří ještě nemají v Azure ID objekt uživatele, aby se objekt uživatele automaticky vytvořil a ověřil pro týmy. Objekt uživatele bude označen jako "samoobslužná registrace". Jedná se o rozšíření stávající možnosti ověřování pomocí e-mailu, které můžou uživatelé ve spravovaných doménách dělat a můžou se řídit pomocí stejného příznaku. Tato změna se dokončí během následujících dvou měsíců. Podívejte se na aktualizace dokumentace [zde](../enterprise-users/directory-self-service-signup.md).
 
---

### <a name="upcoming-fix-the-oidc-discovery-document-for-the-azure-government-cloud-is-being-updated-to-reference-the-correct-graph-endpoints"></a>Nadcházející Oprava: dokument zjišťování OIDC pro cloud Azure Government se aktualizuje tak, aby odkazoval na správné koncové body grafu.

**Zadejte:** Plánování změn  
**Kategorie služby:** Cloudy svrchovan  
**Schopnost produktu:** Ověřování uživatelů
 
Od června začne dokument zjišťování OIDC [Microsoft Identity Platform a OpenID Connect](../develop/v2-protocols-oidc.md) na koncovém bodu [cloudu Azure Government](../develop/authentication-national-cloud.md) (login.microsoftonline.us) vracet správný národní koncový bod [cloudového grafu](/graph/deployments) ( https://graph.microsoft.us nebo na https://dod-graph.microsoft.us) základě poskytnutého tenanta).  V současné době poskytuje nesprávný msgraph_host pole koncového bodu grafu (graph.microsoft.com).  

Oprava této chyby se postupně provede přibližně 2 měsíce.  

---

### <a name="azure-government-users-will-no-longer-be-able-to-sign-in-on-loginmicrosoftonlinecom"></a>Azure Government se uživatelé už nebudou moct přihlašovat v login.microsoftonline.com.

**Zadejte:** Plánování změn  
**Kategorie služby:** Cloudy svrchovan  
**Schopnost produktu:** Ověřování uživatelů
 
Od 1. června 2018 se oficiální autorita Azure Active Directory (Azure AD) pro Azure Government změnila z https://login-us.microsoftonline.com na https://login.microsoftonline.us . Pokud vlastníte aplikaci v rámci klienta Azure Government, musíte aplikaci aktualizovat, aby se uživatelé mohli podepisovat na koncovém bodu. us.

Od 5. května Azure AD zahájí vynucování změny koncového bodu, blokuje Azure Government uživatelům přihlášení k aplikacím hostovaným v Azure Government klienty pomocí veřejného koncového bodu (microsoftonline.com). Ovlivněné aplikace začnou zobrazovat chybu AADSTS900439-USGClientNotSupportedOnPublicEndpoint. 

Dojde k postupnému zavedení této změny s tím, že se očekává, že bude dokončeno v rámci všech aplikací od června 2020. Další podrobnosti najdete v [příspěvku blogu Azure Government](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/).

---

### <a name="saml-single-logout-request-now-sends-nameid-in-the-correct-format"></a>Požadavek na jednotné odhlášení SAML teď pošle NameID ve správném formátu.

**Zadejte:** Určí  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů
 
Když uživatel klikne na odhlášení (například na portálu Mojeapl), pošle služba Azure AD zprávu o jednotném odhlášení SAML do každé aplikace, která je v uživatelské relaci aktivní a má nakonfigurovanou adresu URL pro odhlášení. Tyto zprávy obsahují NameID v trvalém formátu.

Pokud původní token pro přihlášení SAML používá jiný formát pro NameID (např. e-mail nebo hlavní název uživatele (UPN)), pak aplikace SAML nemůže korelovat NameID zprávy ve zprávě pro odhlášení do existující relace (protože NameIDs použitá v obou zprávách je odlišná), což způsobilo, že se zpráva o odhlášení zahodila aplikací SAML a uživatel zůstane přihlášený. Tato oprava vytvoří zprávu pro odhlášení konzistentní s NameID nakonfigurovaným pro aplikaci.

---

### <a name="hybrid-identity-administrator-role-is-now-available-with-cloud-provisioning"></a>Role správce hybridní identity je teď k dispozici s zřizováním cloudu.

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování cloudu Azure AD  
**Schopnost produktu:** Správa životního cyklu identit
 
Správci IT můžou začít používat novou roli Hybrid admin jako nejnižší privilegovanou roli pro nastavení cloudového zřizování Azure ADConnect. U této nové role už nemusíte k instalaci a konfiguraci zřizování cloudu používat roli globálního správce. [Přečtěte si další informace](../roles/delegate-by-task.md#connect).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2020"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – květen 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
V květnu 2020 jsme do Galerie aplikací přidali následující 36 nové aplikace s podporou federace:

[Moula](https://moula.com.au/pay/merchants), [Surveypal](https://www.surveypal.com/app), [Kbot365](https://www.konverso.ai/virtual-assistant-digital-workplace/), [TackleBox](http://www.tacklebox.app/), [Powell Teams](https://powell-software.com/en/powell-teams-en/), [Talentsoft Assistant](https://msteams.talent-soft.com/), [přehledy](https://teams.asc-recording.app/product)o prostředcích ASC, [Humanage](../saas-apps/humanage-tutorial.md) [GO1](https://www.go1.com/), [B](https://b-engaged.se/), [COMPETELLA kontaktní centra](http://www.competella.com/) [, ASITE,](http://www.asite.com/) [ImageSoft identity](https://identity.imagesoftinc.com/), [My IBISWorld](https://identity.imagesoftinc.com/), [unsuite](../saas-apps/insuite-tutorial.md), [Change Process Management](../saas-apps/change-process-management-tutorial.md), [Cyara CX](../saas-apps/cyara-cx-assurance-platform-tutorial.md), [Smart Global vládnutí](../saas-apps/smart-global-governance-tutorial.md), [Prezi](../saas-apps/prezi-tutorial.md), [Mapbox](../saas-apps/mapbox-tutorial.md), [Datava Enterprise Service Platform](../saas-apps/datava-enterprise-service-platform-tutorial.md), [Whimsical](../saas-apps/whimsical-tutorial.md), [Trelica](../saas-apps/trelica-tutorial.md), [EasySSO pro Confluence](../saas-apps/easysso-for-confluence-tutorial.md), [EasySSO](../saas-apps/easysso-for-bitbucket-tutorial.md), Bitbucket [, EasySSO](../saas-apps/easysso-for-bamboo-tutorial.md), Bamboo [, Torii,](../saas-apps/torii-tutorial.md)Axiad, ColorTokens, ZTNA [ColorTokens ZTNA](../saas-apps/colortokens-ztna-tutorial.md) [ShareVault](../saas-apps/sharevault-tutorial.md), [Všichni domů CRM](../saas-apps/anyone-home-crm-tutorial.md) [, CCH,](../saas-apps/cch-tagetik-tutorial.md) [Vyond](../saas-apps/vyond-tutorial.md) [Contact Center](../saas-apps/ice-contact-center-tutorial.md) [Axiad Cloud](../saas-apps/axiad-cloud-tutorial.md) [TextExpander](../saas-apps/textexpander-tutorial.md) [askSpoke](../saas-apps/askspoke-tutorial.md)

Dokumentaci ke všem aplikacím si můžete také najít tady https://aka.ms/AppsTutorial .

Pokud chcete zobrazit seznam aplikací v galerii aplikací Azure AD, přečtěte si prosím podrobnosti tady https://aka.ms/AzureADAppRequest .

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>Režim jenom pro sestavy pro podmíněný přístup je teď všeobecně dostupný.

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity & ochrana

[Režim pouze sestav pro podmíněný přístup Azure AD](../conditional-access/concept-conditional-access-report-only.md) umožňuje vyhodnotit výsledek zásady bez vynucení řízení přístupu. V rámci vaší organizace můžete testovat zásady jenom pro sestavy a porozumět jejich dopadu, než je povolíte, aby bylo nasazení bezpečnější a jednodušší. Během posledních několika měsíců jsme se seznámili se silným přijetím režimu jenom pro sestavy – přes 26M uživatelé už jsou v oboru zásady jenom pro sestavy. V dnešní době se nové zásady podmíněného přístupu Azure AD vytvoří ve výchozím nastavení v režimu pouze sestavy. To znamená, že můžete sledovat dopad vašich zásad od okamžiku, kdy byly vytvořeny. A pro ty z vás, kteří používají rozhraní API MS graphu, můžete také [programově spravovat zásady jenom pro sestavy](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta) . 

---

### <a name="self-service-sign-up-for-guest-users"></a>Samoobslužná registrace pro uživatele typu Host

**Zadejte:** Nová funkce  
**Kategorie služby:** B2B  
**Schopnost produktu:** B2B/B2C
 
S externími identitami v Azure AD můžete uživatelům mimo vaši organizaci umožnit přístup k vašim aplikacím a prostředkům a zároveň jim umožnit přihlašovat se pomocí libovolné identity, které preferuje. Když sdílíte aplikaci s externími uživateli, možná nebudete vždy znát předem, který bude potřebovat přístup k aplikaci. Díky [samoobslužné registraci](../external-identities/self-service-sign-up-overview.md)můžete uživatelům typu Host povolit registraci a získat účet hosta pro vaše obchodní aplikace (LOB). Registrační tok se dá vytvořit a přizpůsobit tak, aby podporoval Azure AD a sociální identity. Během registrace můžete také shromažďovat další informace o uživateli.

---

 ### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>Přehledy podmíněného přístupu a sešit vytváření sestav jsou všeobecně dostupné

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity & ochrana

[Sešit přehledů a sestav](../conditional-access/howto-conditional-access-insights-reporting.md) poskytuje správcům souhrnné zobrazení podmíněného přístupu Azure AD ve svém tenantovi. Díky možnosti výběru jednotlivých zásad můžou správci lépe pochopit, co jednotlivé zásady dělá, a monitorovat všechny změny v reálném čase. Sešit streamuje data uložená v Azure Monitor, která můžete nastavit během několika minut [po těchto pokynech](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md). Aby byl řídicí panel lépe zjistitelný, přesunuli jsme ho na kartu nové přehledy a sestavy v nabídce podmíněný přístup Azure AD.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>Okno podrobností zásad pro podmíněný přístup je ve verzi Public Preview.

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity & ochrana

V okně [Podrobnosti o nové zásadě](../conditional-access/troubleshoot-conditional-access.md) se zobrazí přiřazení, podmínky a ovládací prvky splněné během hodnocení zásad podmíněného přístupu. Přístup k oknu můžete zobrazit tak, že na kartách podmíněného přístupu nebo pouze sestavy v podrobnostech pro přihlášení vyberete řádek.

---

### <a name="new-query-capabilities-for-directory-objects-in-microsoft-graph-are-in-public-preview"></a>Nové funkce dotazů pro objekty adresáře v Microsoft Graph jsou v Public Preview

**Zadejte:** Nová funkce  
**Kategorie služby:** **Funkce produktu** MS Graph: vývojářské prostředí

Zavádí se nové možnosti pro Microsoft Graph rozhraní API objektů adresáře, což povoluje operace počet, vyhledávání, filtrování a řazení. To vývojářům umožní rychle se dotazovat na naše objekty adresáře bez řešení, jako je filtrování a řazení v paměti. Další informace najdete v tomto [blogovém příspěvku](https://aka.ms/CountFilterMSGraphAAD).

V současné době je Public Preview a hledáme zpětnou vazbu. Pošlete prosím své komentáře s tímto [stručným průzkumem](https://aka.ms/MsGraphAADSurveyDocs).

---

### <a name="configure-saml-based-single-sign-on-using-microsoft-graph-api-beta"></a>Konfigurace jednotného přihlašování založeného na SAML pomocí rozhraní Microsoft Graph API (beta verze)

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ
 
K dispozici je teď podpora pro vytváření a konfiguraci aplikace z Galerie Azure AD pomocí rozhraní API MS graphu ve verzi beta. Pokud pro více instancí aplikace potřebujete nastavit jednotné přihlašování založené na SAML, Ušetřete čas pomocí rozhraní Microsoft Graph API pro [automatizaci konfigurace jednotného přihlašování založeného na SAML](/graph/application-saml-sso-configure-api).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---may-2020"></a>Nové zřizovací konektory v galerii aplikací Azure AD – květen 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** integrace třetích stran
 
Nyní můžete automatizovat vytváření, aktualizaci a odstraňování uživatelských účtů pro tyto nově integrované aplikace:

* [8x8](../saas-apps/8x8-provisioning-tutorial.md)
* [Juno Journey](../saas-apps/juno-journey-provisioning-tutorial.md)
* [MediusFlow](../saas-apps/mediusflow-provisioning-tutorial.md)
* [New Relic podle organizace](../saas-apps/new-relic-by-organization-provisioning-tutorial.md)
* [Oracle Cloud Infrastructure Console](../saas-apps/oracle-cloud-infratstructure-console-provisioning-tutorial.md)

Další informace o tom, jak lépe zabezpečit vaši organizaci pomocí automatizovaného zřizování uživatelských účtů, najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="saml-token-encryption-is-generally-available"></a>Šifrování tokenu SAML je všeobecně dostupné.

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ
 
[Šifrování tokenu SAML](../manage-apps/howto-saml-token-encryption.md) umožňuje aplikacím nakonfigurovat, aby přijímaly šifrované kontrolní výrazy SAML. Tato funkce je teď obecně dostupná ve všech cloudech.
 
---

### <a name="group-name-claims-in-application-tokens-is-generally-available"></a>Deklarace identity názvu skupiny v tokenech aplikace jsou všeobecně dostupné.

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ
 
Deklarace skupin vystavené v tokenu se teď dají omezit jenom na tyto skupiny přiřazené k aplikaci.  To je obzvláště důležité, když jsou uživatelé členy velkého počtu skupin a hrozí riziko překročení omezení velikosti tokenu. Díky této nové funkci je všeobecně dostupná možnost [přidávat názvy skupin do tokenů](../hybrid/how-to-connect-fed-group-claims.md) .
 
---

### <a name="workday-writeback-now-supports-setting-work-phone-number-attributes"></a>Zpětný zápis do Workday teď podporuje nastavení atributů pracovní telefonní číslo.

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** Správa životního cyklu identit
 
Vylepšili jsme aplikaci pro zajištění zpětného zápisu do pracovních verzí, která teď podporuje zpětný zápis telefonních čísel a atributů mobilního čísla. Kromě e-mailu a uživatelského jména můžete teď v rámci služby Azure AD a Workday nakonfigurovat aplikaci pro zajištění zpětného zápisu do Workday k flowu hodnot telefonního čísla. Další podrobnosti o tom, jak nakonfigurovat zpětný zápis pro telefonní číslo, najdete v kurzu aplikace pro [zpětný zápis do Workday](../saas-apps/workday-writeback-tutorial.md) . 

---

### <a name="publisher-verification-preview"></a>Ověřování vydavatele (Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Jiná  
**Schopnost produktu:** Vývojářské prostředí
 
Ověřování vydavatele (Preview) pomáhá správcům a koncovým uživatelům pochopit pravost vývojářů aplikací, které jsou integrovány s platformou Microsoft identity. Podrobnosti najdete v tématu [ověření vydavatele (Preview)](../develop/publisher-verification-overview.md).
 
---

### <a name="authorization-code-flow-for-single-page-apps"></a>Tok autorizačního kódu pro jednostránkové aplikace

**Zadejte:** Kategorie změněné **služby funkcí:** ověřování **– schopnost produktu:** vývojové prostředí

Vzhledem k tomu, že se v moderních prohlížečích používají [omezení souborů cookie třetích stran](../develop/reference-third-party-cookies-spas.md), jako je Safari ITP, bude muset jednostránkové používat tok autorizačního kódu místo implicitního toku pro zachování jednotného přihlašování. MSAL.js v 2. x teď bude podporovat tok autorizačního kódu. V takovém případě odpovídají aktualizace Azure Portal, takže můžete zabezpečené ověřování pomocí hesla zadat jako "Spa" a použít tok kódu ověřování. Pokyny najdete [v tématu rychlý Start: přihlášení uživatelů a získání přístupového tokenu v zabezpečeném kódu v jazyce JavaScript pomocí toku kódu ověřování](../develop/quickstart-v2-javascript-auth-code.md).

---

### <a name="improved-filtering-for-devices-is-in-public-preview"></a>Vylepšené filtrování pro zařízení je v Public Preview

**Zadejte:** Změněná funkce   
**Kategorie služby:** **Schopnost produktu** správy zařízení: Správa životního cyklu zařízení
 
Dřív jste mohli použít jenom filtry "povoleno" a "datum aktivity". Teď můžete [seznam zařízení filtrovat podle dalších vlastností](../devices/device-management-azure-portal.md#device-list-filtering-preview), včetně typu operačního systému, typu spojení, dodržování předpisů a dalších. Tyto doplňky by měly zjednodušit vyhledání konkrétního zařízení.

---

### <a name="the-new-app-registrations-experience-for-azure-ad-b2c-is-now-generally-available"></a>Nové prostředí Registrace aplikací pro Azure AD B2C je teď všeobecně dostupné

**Zadejte:** Změněná funkce   
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** Správa životního cyklu identit
 
Nové prostředí Registrace aplikací pro Azure AD B2C je teď všeobecně dostupné. 

Dřív jste museli spravovat aplikace B2C pro spotřebitele odděleně od ostatních aplikací, které využívají starší možnosti aplikací. Který na různých místech v Azure znamenal jiné prostředí pro vytváření aplikací.

Nové prostředí zobrazuje všechny registrace aplikací B2C a registrace aplikací Azure AD na jednom místě a poskytuje konzistentní způsob jejich správy. Bez ohledu na to, jestli potřebujete spravovat zákaznickou aplikaci nebo aplikaci, která má přístup k Microsoft Graph pro programovou správu prostředků Azure AD B2C, stačí se naučit jenom jeden ze způsobů, jak to udělat.

Nové prostředí můžete oslovit tak, že přejdete na službu Azure AD B2C a vyberete okno Registrace aplikací. Prostředí je dostupné taky z Azure Active Directory služby.

Prostředí Azure AD B2C Registrace aplikací je založené na obecném [prostředí pro registraci aplikací](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) pro klienty Azure AD, ale je přizpůsobené pro Azure AD B2C. Starší verze aplikací budou v budoucnu zastaralé.

Další informace najdete [v novém prostředí pro registraci aplikací pro Azure AD B2C](../../active-directory-b2c/app-registrations-training-guide.md).

---
