---
title: Co je nového? Poznámky k verzi – Azure Active Directory | Microsoft Docs
description: Přečtěte si, co je nového v Azure Active Directory, jako jsou například nejnovější poznámky k verzi, známé problémy, opravy chyb, zastaralé funkce a nadcházející změny.
services: active-directory
author: msaburnley
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ac87bda4251a8414ef659ad486c989c0c1cd42e1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87034716"
---
# <a name="whats-new-in-azure-active-directory"></a>Co je nového v Azure Active Directory?

>Přečtěte si informace o tom, kdy se má tato stránka na aktualizace znovu navštívit zkopírováním a vložením této adresy URL: `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` do čtečky informačního kanálu ![ RSS ](./media/whats-new/feed-icon-16x16.png) .

Služba Azure AD průběžně přijímá vylepšení. V tomto článku najdete informace o tom, jak se můžete seznámit s nejnovějším vývojem.

- Nejnovější verze
- Známé problémy
- Opravy chyb
- Zastaralé funkce
- Plánuje změny

Tato stránka se aktualizuje měsíčně, takže ji můžete pravidelně znovu navštěvovat. Pokud hledáte položky, které jsou starší než šest měsíců, najdete je v archivu, abyste mohli [novinky v Azure Active Directory](whats-new-archive.md).

---

## <a name="june-2020"></a>Červen 2020 

### <a name="user-risk-condition-in-conditional-access-policy"></a>Podmínka rizika uživatele v zásadách podmíněného přístupu

**Zadejte:** Plánování změn  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity & ochrana
 

Podpora rizik uživatelů v zásadách podmíněného přístupu Azure AD umožňuje vytvořit několik uživatelských zásad založených na rizicích. Pro různé uživatele a aplikace se můžou vyžadovat jiné minimální úrovně rizika uživatele. Na základě rizika uživatele můžete vytvořit zásady pro blokování přístupu, vyžadovat vícefaktorové ověřování, zabezpečenou změnu hesla nebo přesměrovat na Microsoft Cloud App Security k vynucení zásad relace, jako je například další auditování.

Podmínka rizika uživatele vyžaduje Azure AD Premium P2, protože používá Azure Identity Protection, což je nabídka P2. Další informace o podmíněném přístupu najdete v [dokumentaci k podmíněnému přístupu Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/).

---

### <a name="saml-sso-now-supports-apps-that-require-spnamequalifier-to-be-set-when-requested"></a>Jednotné přihlašování SAML teď podporuje aplikace, které vyžadují, aby se v případě vyžádání nastavil SPNameQualifier

**Zadejte:** Určí  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ
 
Některé aplikace SAML vyžadují, aby se v předmětu kontrolního výrazu vrátilo SPNameQualifier, pokud je to požadováno. Služba Azure AD teď odpoví správně, když se v zásadách NameID žádosti vyžádá SPNameQualifier. To funguje taky pro přihlášení iniciované pomocí SP a zahájí se přihlášení IdP.  Další informace o protokolu SAML v Azure Active Directory najdete v tématu [protokol SAML jednotného přihlašování](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol).

---

### <a name="azure-ad-b2b-collaboration-supports-inviting-msa-and-google-users-in-azure-government-tenants"></a>Spolupráce B2B Azure AD podporuje pozvání uživatelů MSA a Google v Azure Government tenantů.

**Zadejte:** Nová funkce  
**Kategorie služby:** B2B  
**Schopnost produktu:** B2B/B2C
 

Klienti Azure Government pomocí funkcí spolupráce B2B teď můžou pozvat uživatele, kteří mají účet Microsoft nebo Google. Pokud chcete zjistit, jestli váš tenant může tyto možnosti využít, postupujte podle pokynů v tématu [Jak poznám, jestli je spolupráce B2B dostupná ve svém Tenantovi Azure USA?](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)

 
---
 
### <a name="user-object-in-ms-graph-v1-now-includes-externaluserstate-and-externaluserstatechangeddatetime-properties"></a>Objekt uživatele v MS Graph v1 nyní zahrnuje vlastnosti externalUserState a externalUserStateChangedDateTime.

**Zadejte:** Nová funkce  
**Kategorie služby:** B2B  
**Schopnost produktu:** B2B/B2C
 

Vlastnosti externalUserState a externalUserStateChangedDateTime lze použít k vyhledání pozvaných hostů B2B, kteří ještě nepřijali své pozvánky, a také automatizaci sestavení, jako je odstranění uživatelů, kteří nepřijali své pozvánky po určitém počtu dnů. Tyto vlastnosti jsou teď dostupné v MS Graph v1. Pokyny k používání těchto vlastností najdete v tématu [typ prostředku uživatele](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0).
 
---

### <a name="manage-authentication-sessions-in-azure-ad-conditional-access-is-now-generally-available"></a>Správa relací ověřování ve službě Azure AD podmíněný přístup je teď všeobecně dostupná.

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
Možnosti správy relace ověřování vám umožňují nakonfigurovat, jak často uživatelé potřebují zadat přihlašovací údaje a jestli potřebují zadat přihlašovací údaje po zavření a opětovném otevření prohlížečů, aby ve vašem prostředí nabízely větší zabezpečení a flexibilitu.
 
Kromě toho se Správa relace ověřování používá jenom pro první ověření v připojené službě Azure AD, připojené k hybridní službě Azure AD a k zařízením registrovaným v Azure AD. Nyní se Správa relace ověřování uplatní i na MFA. Další informace najdete v tématu [Konfigurace správy relace ověřování pomocí podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2020"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – červen 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
V červnu 2020 jsme do Galerie aplikací přidali následující 29 nových aplikací s podporou federace:

[Shopify plus](https://docs.microsoft.com/azure/active-directory/saas-apps/shopify-plus-tutorial), [Ekarda](https://docs.microsoft.com/azure/active-directory/saas-apps/ekarda-tutorial), [MailGates](https://docs.microsoft.com/azure/active-directory/saas-apps/mailgates-tutorial), [BullseyeTDP](https://docs.microsoft.com/azure/active-directory/saas-apps/bullseyetdp-tutorial), [raketa](https://docs.microsoft.com/azure/active-directory/saas-apps/raketa-tutorial), [segment](https://docs.microsoft.com/azure/active-directory/saas-apps/segment-tutorial), [AI auditor](https://www.mindbridge.ai/products/ai-auditor/), [Pobuca Connect](https://app.pobu.ca/), [proto.IO](https://docs.microsoft.com/azure/active-directory/saas-apps/proto.io-tutorial), [Gatekeeper](https://www.gatekeeperhq.com/)Webmarketo, [Plánovač centra](https://docs.microsoft.com/azure/active-directory/saas-apps/hub-planner-tutorial), Ansira, možnosti [pro uvedení na trh](https://ansira.com/technology/channel-engagement), [IBM Digital Business Automation v cloudu](https://docs.microsoft.com/azure/active-directory/saas-apps/ibm-digital-business-automation-on-cloud-tutorial), [Kisi fyzické zabezpečení](https://docs.microsoft.com/azure/active-directory/saas-apps/kisi-physical-security-tutorial), [ViewpointOne](https://team.viewpoint.com/), [IntelligenceBank](https://docs.microsoft.com/azure/active-directory/saas-apps/intelligencebank-tutorial), [pymetrics](https://docs.microsoft.com/azure/active-directory/saas-apps/pymetrics-tutorial), [nula](https://www.teamzero.com/) [Curricula SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/curricula-saml-tutorial), [instanice](https://instation.invillia.com/), [EDX for Business SAML 2,0 Integration](https://docs.microsoft.com/azure/active-directory/saas-apps/edx-for-business-saml-integration-tutorial), mooc SmartKargo, PKIsigning [Perforce Helix Core - Helix Authentication Service](https://docs.microsoft.com/azure/active-directory/saas-apps/perforce-helix-core-tutorial) [, SiteIntel](https://docs.microsoft.com/azure/active-directory/saas-apps/smartkargo-tutorial), [Perforce, Helix](https://docs.microsoft.com/azure/active-directory/saas-apps/siteintel-tutorial) [, Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/field-id-tutorial) [,](https://cloud.metacompliance.com/) [MOOC Office 365](https://mooc.office365-training.com/en/) [PKIsigning platform](https://platform.pkisigning.nl/) [Helix 365](https://smallstep.com/sso-ssh/)  

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

Další informace o všech dostupných prostředích pomocí konektorů rozhraní API najdete v tématu [Použití konektorů rozhraní API k přizpůsobení a rozšiřování samoobslužné registrace](https://docs.microsoft.com/azure/active-directory/b2b/api-connectors-overview)nebo [přizpůsobení samoobslužné registrace externích identit pomocí integrace webového rozhraní API](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-external-identities-self-service-sign-up-with-web-api/ba-p/1257364#.XvNz2fImuQg.linkedin).
 
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
 
Nové delegované oprávnění EntitlementManagement. Read. All je teď k dispozici pro použití s rozhraním API pro správu oprávnění ve službě Microsoft Graph beta. Další informace o dostupných rozhraních API najdete v tématu [práce s rozhraním API pro správu opravňujících k Azure AD](https://docs.microsoft.com/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta).

---

### <a name="identity-protection-apis-available-in-v10"></a>Rozhraní API pro ochranu identit dostupné v 1.0

**Zadejte:** Nová funkce  
**Kategorie služby:** Ochrana identity  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
Rozhraní API pro Microsoft Graph riskyUsers a riskDetections jsou teď všeobecně dostupná. Teď, když jsou k dispozici na koncovém bodu v 1.0, vás pozvaní, abyste je mohli používat v produkčním prostředí. Další informace najdete v [dokumentaci Microsoft Graph](https://docs.microsoft.com/graph/api/resources/identityprotectionroot?view=graph-rest-1.0).
 
---

### <a name="sensitivity-labels-to-apply-policies-to-microsoft-365-groups-is-now-generally-available"></a>Popisky citlivosti na uplatnění zásad na Microsoft 365 skupiny jsou teď všeobecně dostupné.

**Zadejte:** Nová funkce  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Prostřednictvím
 

Nyní můžete vytvořit popisky citlivosti a použít nastavení popisku k uplatnění zásad pro Microsoft 365 skupiny, včetně ochrany osobních údajů (veřejných nebo privátních) a zásad přístupu k externím uživatelům. Můžete vytvořit popisek se zásadami ochrany osobních údajů jako privátní a zásady přístupu externích uživatelů, aby nepovolovaly přidávání uživatelů typu Host. Když uživatel použije tento popisek na skupinu, skupina bude soukromá a do skupiny nebude povoleno přidávat žádné uživatele typu Host. 

Popisky citlivosti jsou důležité pro ochranu důležitých podnikových dat a umožňují vám spravovat skupiny ve velkém měřítku, a to v souladu a zabezpečeném způsobem. Pokyny k používání popisků citlivosti najdete [v tématu Přiřazení popisků citlivosti skupinám Office 365 ve Azure Active Directory (Preview)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-assign-sensitivity-labels).
 
---

### <a name="updates-to-support-for-microsoft-identity-manager-for-azure-ad-premium-customers"></a>Aktualizace pro podporu Microsoft Identity Manager pro zákazníky Azure AD Premium

**Zadejte:** Změněná funkce  
**Kategorie služby:** Microsoft Identity Manager  
**Schopnost produktu:** Správa životního cyklu identit
 
Podpora Azure je teď k dispozici pro komponenty integrace služby Azure AD Microsoft Identity Manager 2016 na konci rozšířené podpory pro Microsoft Identity Manager 2016. Další informace najdete v článku [o podpoře aktualizace pro Azure AD Premium zákazníky pomocí Microsoft Identity Manager](https://docs.microsoft.com/microsoft-identity-manager/support-update-for-azure-active-directory-premium-customers).

---

### <a name="the-use-of-group-membership-conditions-in-sso-claims-configuration-is-increased"></a>Bylo zvýšeno použití podmínek členství ve skupině v konfiguraci deklarací SSO.

**Zadejte:** Změněná funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ
 
Dříve počet skupin, které můžete použít při podmíněné změně deklarací identity na základě členství ve skupině v rámci každé konfigurace jedné aplikace, byl omezený na 10. Použití podmínek členství ve skupině v konfiguraci deklarací SSO se teď zvýšilo na maximálně 50 skupin. Další informace o tom, jak nakonfigurovat deklarace identity, najdete v tématu [Konfigurace deklarací pro jednotné přihlašování k podnikovým aplikacím](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization#emitting-claims-based-on-conditions). 

---

### <a name="enabling-basic-formatting-on-the-sign-in-page-text-component-in-company-branding"></a>Povolení základního formátování na součásti textu přihlašovací stránky v brandingu společnosti.

**Zadejte:** Změněná funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů
 
Funkce brandingu společnosti na přihlašovacím jméně Azure AD/Microsoft 365 se aktualizovala tak, aby zákazník mohl přidávat hypertextové odkazy a jednoduché formátování, včetně tučného písma, podtržení a kurzívy. Pokyny k použití této funkce najdete v tématu [Přidání brandingu na přihlašovací stránku Azure Active Directory vaší organizace](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding).

---

### <a name="provisioning-performance-improvements"></a>Vylepšení výkonu zřizování

**Zadejte:** Změněná funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** Správa životního cyklu identit
 
Služba zřizování se aktualizovala tak, aby snižovala čas dokončení [přírůstkového cyklu](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#incremental-cycles) . To znamená, že uživatelé a skupiny budou k aplikacím zřízeni rychleji než dříve. Všem novým zřizovacím úlohám vytvořeným po 6/10/2020 budou automaticky těžit z vylepšení výkonu. Všechny aplikace, které jsou nakonfigurované pro zřizování před 6/10/2020, se budou muset restartovat jednou po 6/10/2020, aby se využilo zvýšení výkonu. 

---

### <a name="announcing-the-deprecation-of-adal-and-ms-graph-parity"></a>Oznamujeme vyřazení ADAL a parity MS graphu.

**Zadejte:** Zastaralé  
**Kategorie služby:** NENÍ K DISPOZICI  
**Schopnost produktu:** Správa životního cyklu zařízení

Teď, když jsou k dispozici knihovny Microsoft Authentication Library (MSAL), už nepřidáme Azure Active Directory do Authentication Library (ADAL) nové funkce a budou ukončeny opravy zabezpečení na 30. června 2022. Další informace o tom, jak migrovat na MSAL, najdete v článku [migrace aplikací do knihovny Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-migration).

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
 

V současné době jsou uživatelé, kteří jsou v doméně federované v Azure AD, ale nesynchronizované do tenanta, nemůžou získat přístup k týmům. Od konce června Tato nová funkce jim umožní učinit rozšířením stávající funkce pro registraci e-mailu. To umožní uživatelům, kteří se budou přihlašovat k federovaným IdP, ale kteří ještě nemají v Azure ID objekt uživatele, aby se objekt uživatele automaticky vytvořil a ověřil pro týmy. Objekt uživatele bude označen jako "samoobslužná registrace". Jedná se o rozšíření stávající možnosti ověřování pomocí e-mailu, které můžou uživatelé ve spravovaných doménách dělat a můžou se řídit pomocí stejného příznaku. Tato změna se dokončí během následujících dvou měsíců. Podívejte se na aktualizace dokumentace [zde](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup).
 
---

### <a name="upcoming-fix-the-oidc-discovery-document-for-the-azure-government-cloud-is-being-updated-to-reference-the-correct-graph-endpoints"></a>Nadcházející Oprava: dokument zjišťování OIDC pro cloud Azure Government se aktualizuje tak, aby odkazoval na správné koncové body grafu.

**Zadejte:** Plánování změn  
**Kategorie služby:** Cloudy svrchovan  
**Schopnost produktu:** Ověřování uživatelů
 
Od června začne dokument zjišťování OIDC [Microsoft Identity Platform a OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc) na koncovém bodu [cloudu Azure Government](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud) (login.microsoftonline.us) vracet správný národní koncový bod [cloudového grafu](https://docs.microsoft.com/graph/deployments) ( https://graph.microsoft.us nebo na https://dod-graph.microsoft.us) základě poskytnutého tenanta).  V současné době poskytuje nesprávný msgraph_host pole koncového bodu grafu (graph.microsoft.com).  

Oprava této chyby se postupně provede přibližně 2 měsíce.  

---

### <a name="azure-government-users-will-no-longer-be-able-to-sign-in-on-loginmicrosoftonlinecom"></a>Azure Government se uživatelé už nebudou moct přihlašovat v login.microsoftonline.com.

**Zadejte:** Plánování změn  
**Kategorie služby:** Cloudy svrchovan  
**Schopnost produktu:** Ověřování uživatelů
 
Od 1. června 2018 se oficiální autorita Azure Active Directory (AAD) pro Azure Government změnila z https://login-us.microsoftonline.com na https://login.microsoftonline.us . Pokud vlastníte aplikaci v rámci klienta Azure Government, musíte aplikaci aktualizovat, aby se uživatelé mohli podepisovat na koncovém bodu. us.

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
 
Správci IT můžou začít používat novou roli Hybrid admin jako nejnižší privilegovanou roli pro nastavení cloudového zřizování Azure ADConnect. U této nové role už nemusíte k instalaci a konfiguraci zřizování cloudu používat roli globálního správce. [Přečtěte si další informace](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-delegate-by-task#connect).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2020"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – květen 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
V květnu 2020 jsme do Galerie aplikací přidali následující 36 nové aplikace s podporou federace:

[Moula](https://moula.com.au/pay/merchants), [Surveypal](https://www.surveypal.com/app), [Kbot365](https://www.konverso.ai/virtual-assistant-digital-workplace/), [TackleBox](http://www.tacklebox.app/), [Powell Teams](https://powell-software.com/en/powell-teams-en/), [Talentsoft Assistant](https://msteams.talent-soft.com/), [přehledy](https://teams.asc-recording.app/product)o prostředcích ASC, [Humanage](https://docs.microsoft.com/azure/active-directory/saas-apps/humanage-tutorial) [GO1](https://www.go1.com/), [B](https://b-engaged.se/), [COMPETELLA kontaktní centra](http://www.competella.com/) [, ASITE,](http://www.asite.com/) [ImageSoft identity](https://identity.imagesoftinc.com/), [My IBISWorld](https://identity.imagesoftinc.com/), [unsuite](https://docs.microsoft.com/azure/active-directory/saas-apps/insuite-tutorial), [Change Process Management](https://docs.microsoft.com/azure/active-directory/saas-apps/change-process-management-tutorial), [Cyara CX](https://docs.microsoft.com/azure/active-directory/saas-apps/cyara-cx-assurance-platform-tutorial), [Smart Global vládnutí](https://docs.microsoft.com/azure/active-directory/saas-apps/smart-global-governance-tutorial), [Prezi](https://docs.microsoft.com/azure/active-directory/saas-apps/prezi-tutorial), [Mapbox](https://docs.microsoft.com/azure/active-directory/saas-apps/mapbox-tutorial), [Datava Enterprise Service Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/datava-enterprise-service-platform-tutorial), [Whimsical](https://docs.microsoft.com/azure/active-directory/saas-apps/whimsical-tutorial), [Trelica](https://docs.microsoft.com/azure/active-directory/saas-apps/trelica-tutorial), [EasySSO pro Confluence](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-confluence-tutorial), [EasySSO](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-bitbucket-tutorial), Bitbucket [, EasySSO](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-bamboo-tutorial), Bamboo [, Torii,](https://docs.microsoft.com/azure/active-directory/saas-apps/torii-tutorial)Axiad, ColorTokens, ZTNA [ColorTokens ZTNA](https://docs.microsoft.com/azure/active-directory/saas-apps/colortokens-ztna-tutorial) [ShareVault](https://docs.microsoft.com/azure/active-directory/saas-apps/sharevault-tutorial), [Všichni domů CRM](https://docs.microsoft.com/azure/active-directory/saas-apps/anyone-home-crm-tutorial) [, CCH,](https://docs.microsoft.com/azure/active-directory/saas-apps/cch-tagetik-tutorial) [Vyond](https://docs.microsoft.com/azure/active-directory/saas-apps/vyond-tutorial) [Contact Center](https://docs.microsoft.com/azure/active-directory/saas-apps/ice-contact-center-tutorial) [Axiad Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/axiad-cloud-tutorial) [TextExpander](https://docs.microsoft.com/azure/active-directory/saas-apps/textexpander-tutorial) [askSpoke](https://docs.microsoft.com/azure/active-directory/saas-apps/askspoke-tutorial)

Dokumentaci ke všem aplikacím si můžete také najít tady https://aka.ms/AppsTutorial .

Pokud chcete zobrazit seznam aplikací v galerii aplikací Azure AD, přečtěte si prosím podrobnosti tady https://aka.ms/AzureADAppRequest .

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>Režim jenom pro sestavy pro podmíněný přístup je teď všeobecně dostupný.

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity & ochrana

[Režim pouze sestav pro podmíněný přístup Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-report-only) umožňuje vyhodnotit výsledek zásady bez vynucení řízení přístupu. V rámci vaší organizace můžete testovat zásady jenom pro sestavy a porozumět jejich dopadu, než je povolíte, aby bylo nasazení bezpečnější a jednodušší. Během posledních několika měsíců jsme se seznámili se silným přijetím režimu jenom pro sestavy – přes 26M uživatelé už jsou v oboru zásady jenom pro sestavy. V dnešní době se nové zásady podmíněného přístupu Azure AD vytvoří ve výchozím nastavení v režimu pouze sestavy. To znamená, že můžete sledovat dopad vašich zásad od okamžiku, kdy byly vytvořeny. A pro ty z vás, kteří používají rozhraní API MS graphu, můžete také [programově spravovat zásady jenom pro sestavy](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta) . 

---

### <a name="self-service-sign-up-for-guest-users"></a>Samoobslužná registrace pro uživatele typu Host

**Zadejte:** Nová funkce  
**Kategorie služby:** B2B  
**Schopnost produktu:** B2B/B2C
 
S externími identitami v Azure AD můžete uživatelům mimo vaši organizaci umožnit přístup k vašim aplikacím a prostředkům a zároveň jim umožnit přihlašovat se pomocí libovolné identity, které preferuje. Když sdílíte aplikaci s externími uživateli, možná nebudete vždy znát předem, který bude potřebovat přístup k aplikaci. Díky [samoobslužné registraci](https://docs.microsoft.com/azure/active-directory/b2b/self-service-sign-up-overview)můžete uživatelům typu Host povolit registraci a získat účet hosta pro vaše obchodní aplikace (LOB). Registrační tok se dá vytvořit a přizpůsobit tak, aby podporoval Azure AD a sociální identity. Během registrace můžete také shromažďovat další informace o uživateli.

---

 ### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>Přehledy podmíněného přístupu a sešit vytváření sestav jsou všeobecně dostupné

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity & ochrana

[Sešit přehledů a sestav](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-insights-reporting) poskytuje správcům souhrnné zobrazení podmíněného přístupu Azure AD ve svém tenantovi. Díky možnosti výběru jednotlivých zásad můžou správci lépe pochopit, co jednotlivé zásady dělá, a monitorovat všechny změny v reálném čase. Sešit streamuje data uložená v Azure Monitor, která můžete nastavit během několika minut [po těchto pokynech](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics). Aby byl řídicí panel lépe zjistitelný, přesunuli jsme ho na kartu nové přehledy a sestavy v nabídce podmíněný přístup Azure AD.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>Okno podrobností zásad pro podmíněný přístup je ve verzi Public Preview.

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity & ochrana

V okně [Podrobnosti o nové zásadě](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access) se zobrazí přiřazení, podmínky a ovládací prvky splněné během hodnocení zásad podmíněného přístupu. Přístup k oknu můžete zobrazit tak, že na kartách podmíněného přístupu nebo pouze sestavy v podrobnostech pro přihlášení vyberete řádek.

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
 
K dispozici je teď podpora pro vytváření a konfiguraci aplikace z Galerie Azure AD pomocí rozhraní API MS graphu ve verzi beta. Pokud pro více instancí aplikace potřebujete nastavit jednotné přihlašování založené na SAML, Ušetřete čas pomocí rozhraní Microsoft Graph API pro [automatizaci konfigurace jednotného přihlašování založeného na SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/application-saml-sso-configure-api).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---may-2020"></a>Nové zřizovací konektory v galerii aplikací Azure AD – květen 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** integrace třetích stran
 
Nyní můžete automatizovat vytváření, aktualizaci a odstraňování uživatelských účtů pro tyto nově integrované aplikace:

* [8x8](https://docs.microsoft.com/azure/active-directory/saas-apps/8x8-provisioning-tutorial)
* [Juno Journey](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-provisioning-tutorial)
* [MediusFlow](https://docs.microsoft.com/azure/active-directory/saas-apps/mediusflow-provisioning-tutorial)
* [New Relic podle organizace](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-by-organization-provisioning-tutorial)
* [Oracle Cloud Infrastructure Console](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-infratstructure-console-provisioning-tutorial)

Další informace o tom, jak lépe zabezpečit vaši organizaci pomocí automatizovaného zřizování uživatelských účtů, najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="saml-token-encryption-is-generally-available"></a>Šifrování tokenu SAML je všeobecně dostupné.

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ
 
[Šifrování tokenu SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption) umožňuje aplikacím nakonfigurovat, aby přijímaly šifrované kontrolní výrazy SAML. Tato funkce je teď obecně dostupná ve všech cloudech.
 
---

### <a name="group-name-claims-in-application-tokens-is-generally-available"></a>Deklarace identity názvu skupiny v tokenech aplikace jsou všeobecně dostupné.

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ
 
Deklarace skupin vystavené v tokenu se teď dají omezit jenom na tyto skupiny přiřazené k aplikaci.  To je obzvláště důležité, když jsou uživatelé členy velkého počtu skupin a hrozí riziko překročení omezení velikosti tokenu. Díky této nové funkci je všeobecně dostupná možnost [přidávat názvy skupin do tokenů](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-group-claims) .
 
---

### <a name="workday-writeback-now-supports-setting-work-phone-number-attributes"></a>Zpětný zápis do Workday teď podporuje nastavení atributů pracovní telefonní číslo.

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** Správa životního cyklu identit
 
Vylepšili jsme aplikaci pro zajištění zpětného zápisu do pracovních verzí, která teď podporuje zpětný zápis telefonních čísel a atributů mobilního čísla. Kromě e-mailu a uživatelského jména můžete teď v rámci služby Azure AD a Workday nakonfigurovat aplikaci pro zajištění zpětného zápisu do Workday k flowu hodnot telefonního čísla. Další podrobnosti o tom, jak nakonfigurovat zpětný zápis pro telefonní číslo, najdete v kurzu aplikace pro [zpětný zápis do Workday](https://aka.ms/WorkdayWriteback) . 

---

### <a name="publisher-verification-preview"></a>Ověřování vydavatele (Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Jiná  
**Schopnost produktu:** Vývojářské prostředí
 
Ověřování vydavatele (Preview) pomáhá správcům a koncovým uživatelům pochopit pravost vývojářů aplikací, které jsou integrovány s platformou Microsoft identity. Podrobnosti najdete v tématu [ověření vydavatele (Preview)](https://docs.microsoft.com/azure/active-directory/develop/publisher-verification-overview).
 
---

### <a name="authorization-code-flow-for-single-page-apps"></a>Tok autorizačního kódu pro jednostránkové aplikace

**Zadejte:** Kategorie změněné **služby funkcí:** ověřování **– schopnost produktu:** vývojové prostředí

Vzhledem k tomu, že se v moderních prohlížečích používají [omezení souborů cookie třetích stran](https://docs.microsoft.com/azure/active-directory/develop/reference-third-party-cookies-spas), jako je Safari ITP, bude muset jednostránkové používat tok autorizačního kódu místo implicitního toku pro zachování jednotného přihlašování. MSAL.js v 2. x teď bude podporovat tok autorizačního kódu. V takovém případě odpovídají aktualizace Azure Portal, takže můžete zabezpečené ověřování pomocí hesla zadat jako "Spa" a použít tok kódu ověřování. Pokyny najdete [v tématu rychlý Start: přihlášení uživatelů a získání přístupového tokenu v zabezpečeném kódu v jazyce JavaScript pomocí toku kódu ověřování](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-javascript-auth-code).

---

### <a name="improved-filtering-for-devices-is-in-public-preview"></a>Vylepšené filtrování pro zařízení je v Public Preview

**Zadejte:** Změněná funkce   
**Kategorie služby:** **Schopnost produktu** správy zařízení: Správa životního cyklu zařízení
 
Dřív jste mohli použít jenom filtry "povoleno" a "datum aktivity". Teď můžete [seznam zařízení filtrovat podle dalších vlastností](https://docs.microsoft.com/azure/active-directory/devices/device-management-azure-portal#device-list-filtering-preview), včetně typu operačního systému, typu spojení, dodržování předpisů a dalších. Tyto doplňky by měly zjednodušit vyhledání konkrétního zařízení.

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

Další informace najdete [v novém prostředí pro registraci aplikací pro Azure AD B2C](https://aka.ms/b2cappregtraining).

---

## <a name="april-2020"></a>Duben 2020

### <a name="combined-security-info-registration-experience-is-now-generally-available"></a>Kombinované prostředí pro registraci informací o zabezpečení je teď všeobecně dostupné.

**Zadejte:** Nová funkce

**Kategorie služby:** Ověřování (přihlášení)

**Schopnost produktu:** Zabezpečení identity & ochrana

Kombinované prostředí pro registraci pro Multi-Factor Authentication (MFA) a Samoobslužné resetování hesla (SSPR) je teď všeobecně dostupné. Toto nové prostředí pro registraci umožňuje uživatelům zaregistrovat se pro MFA a SSPR v jediném podrobném procesu. Když nasadíte nové prostředí pro vaši organizaci, můžou se uživatelé registrovat kratší dobu a s menším počtem problémů. Podívejte se na Blogový příspěvek [tady](https://bit.ly/3etiRyQ).

---

### <a name="continuous-access-evaluation"></a>Vyhodnocování průběžného přístupu

**Zadejte:** Nová funkce

**Kategorie služby:** Ověřování (přihlášení)

**Schopnost produktu:** Zabezpečení identity & ochrana

Vyhodnocování průběžného přístupu je nová funkce zabezpečení, která umožňuje v reálném čase provádět vynucování zásad pro předávající strany, které využívají přístupové tokeny Azure AD, když dojde k událostem v Azure AD (například odstranění uživatelského účtu). Tuto funkci pro týmy a klienty Outlooku nejprve vydáváme. Další podrobnosti najdete v našem [blogu](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933) a v [dokumentaci](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-continuous-access-evaluation).

---

### <a name="sms-sign-in-firstline-workers-can-sign-in-to-azure-ad-backed-applications-with-their-phone-number-and-no-password"></a>Přihlášení SMS: Firstline se můžou přihlásit k aplikacím zálohovaným přes Azure AD pomocí jejich telefonního čísla a bez hesla.

**Zadejte:** Nová funkce

**Kategorie služby:** Ověřování (přihlášení)

**Schopnost produktu:** Ověřování uživatelů

Office spouští řadu mobilních obchodních aplikací, které využívají v netradičních organizacích, a zaměstnancům ve velkých organizacích, které jako primární způsob komunikace nepoužívají e-mail. Tyto aplikace cílí na prvotní zaměstnanců, pracovních procesů bez pracovního oddělení nebo maloobchodních zaměstnanců, kteří nemusí dostávat e-mailovou adresu od svého zaměstnavatele, mít přístup k počítači nebo k němu. Tento projekt umožní těmto zaměstnancům přihlašovat se k obchodním aplikacím zadáním telefonního čísla a roundtripping kódu. Další podrobnosti najdete v [dokumentaci pro správce](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-sms-signin) a v [dokumentaci pro koncové uživatele](https://docs.microsoft.com/azure/active-directory/user-help/sms-sign-in-explainer).

---

### <a name="invite-internal-users-to-use-b2b-collaboration"></a>Pozvat interní uživatele, aby mohli používat spolupráci B2B

**Zadejte:** Nová funkce

**Kategorie služby:** B2B

**Schopnost produktu:**

Rozšiřujeme možnosti pozvánky B2B, aby bylo možné přizvat stávající interní účty k používání přihlašovacích údajů pro spolupráci B2B. To se provádí předáním objektu uživatele do rozhraní API pro pozvání kromě typických parametrů, jako je pozvaní e-mailová adresa. ID objektu uživatele, hlavní název uživatele (UPN), členství ve skupině, přiřazení aplikace atd. zůstane beze změny, ale bude se používat B2B k ověření s přihlašovacími údaji svého domovského tenanta, nikoli pomocí interních přihlašovacích údajů, které používali před pozvánkou. Podrobnosti najdete v [dokumentaci](https://docs.microsoft.com/azure/active-directory/b2b/invite-internal-users).

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>Režim jenom pro sestavy pro podmíněný přístup je teď všeobecně dostupný.

**Zadejte:** Nová funkce

**Kategorie služby:** Podmíněný přístup

**Schopnost produktu:** Zabezpečení identity & ochrana

[Režim pouze sestav pro podmíněný přístup Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-report-only) umožňuje vyhodnotit výsledek zásady bez vynucení řízení přístupu. V rámci vaší organizace můžete testovat zásady jenom pro sestavy a porozumět jejich dopadu, než je povolíte, aby bylo nasazení bezpečnější a jednodušší. Během posledních několika měsíců jsme se seznámili se silným přijetím režimu pouze pro sestavy s více než 26M uživateli, kteří už v oboru zásady jenom pro sestavy. V tomto oznámení se nové zásady podmíněného přístupu Azure AD vytvoří ve výchozím nastavení v režimu pouze sestavy. To znamená, že můžete sledovat dopad vašich zásad od okamžiku, kdy byly vytvořeny. A pro ty z vás, kteří používají rozhraní API MS graphu, můžete také [programově spravovat zásady jenom pro sestavy](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta). 

---

### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>Přehledy podmíněného přístupu a sešit vytváření sestav jsou všeobecně dostupné

**Zadejte:** Nová funkce

**Kategorie služby:** Podmíněný přístup

**Schopnost produktu:** Zabezpečení identity & ochrana

Sešit s přehledem podmíněného přístupu [a vytváření sestav](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-insights-reporting) poskytuje správcům přehled o podmíněném přístupu Azure AD ve svém tenantovi. Díky možnosti výběru jednotlivých zásad můžou správci lépe pochopit, co jednotlivé zásady dělá, a monitorovat všechny změny v reálném čase. Sešit streamuje data uložená v Azure Monitor, která můžete nastavit během několika minut [po těchto pokynech](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics). Aby byl řídicí panel lépe zjistitelný, přesunuli jsme ho na kartu nové přehledy a sestavy v nabídce podmíněný přístup Azure AD.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>Okno podrobností zásad pro podmíněný přístup je ve verzi Public Preview.

**Zadejte:** Nová funkce

**Kategorie služby:** Podmíněný přístup

**Schopnost produktu:** Zabezpečení identity & ochrana

V okně [Podrobnosti o nové zásadě](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access) se zobrazí, která přiřazení, podmínky a ovládací prvky byly splněné při vyhodnocování zásad podmíněného přístupu. Přístup k oknu můžete zobrazit tak, že na kartách **podmíněného přístupu** nebo **pouze sestavy** v podrobnostech pro přihlášení vyberete řádek.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2020"></a>Nové federované aplikace dostupné v Aplikace Azure AD galerii – duben 2020

**Zadejte:** Nová funkce

**Kategorie služby:** Podnikové aplikace

**Schopnost produktu:** integrace třetích stran

V dubnu 2020 jsme do Galerie aplikací přidali tyto 31 nových aplikací s podporou federace: 

[SincroPool Apps](https://www.sincropool.com/), [SmartDB](https://hibiki.dreamarts.co.jp/smartdb/trial/), [float](https://docs.microsoft.com/azure/active-directory/saas-apps/float-tutorial), [LMS365](https://lms.365.systems/), [iwt Suite Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/iwt-procurement-suite-tutorial), [Lunni](https://lunni.fi/), [EasySSO pro JIRA](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-jira-tutorial), [Virtual Train Academy](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft), [Meraki Dashboard](https://docs.microsoft.com/azure/active-directory/saas-apps/meraki-dashboard-tutorial), [Office 365 stěhovací](https://app.mover.io/login), [mluvčí zapojení](https://speakerengage.com/login.php), [upřímně](https://docs.microsoft.com/azure/active-directory/saas-apps/honestly-tutorial), [Ally](https://docs.microsoft.com/azure/active-directory/saas-apps/ally-tutorial), [DutyFlow](https://app.dutyflow.nl/), [AlertMedia](https://docs.microsoft.com/azure/active-directory/saas-apps/alertmedia-tutorial), [gr8 lidé](https://docs.microsoft.com/azure/active-directory/saas-apps/gr8-people-tutorial), [Pendo](https://docs.microsoft.com/azure/active-directory/saas-apps/pendo-tutorial), [HighGround](https://docs.microsoft.com/azure/active-directory/saas-apps/highground-tutorial), [harmonie](https://docs.microsoft.com/azure/active-directory/saas-apps/harmony-tutorial) [, Timetabling](https://docs.microsoft.com/azure/active-directory/saas-apps/timetabling-solutions-tutorial), SynchroNet, [litmus](https://docs.microsoft.com/azure/active-directory/saas-apps/mongodb-cloud-tutorial), GroupTalk [Fortes Change Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/fortes-change-cloud-tutorial) [, Frontify,](https://docs.microsoft.com/azure/active-directory/saas-apps/tickitlms-learn-tutorial) [MongoDB,](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/) [TickitLMS](https://docs.microsoft.com/azure/active-directory/saas-apps/nitro-productivity-suite-tutorial) , [GroupTalk](https://recorder.grouptalk.com/) [díky Coco, nitro](https://docs.microsoft.com/azure/active-directory/saas-apps/litmus-tutorial), [TMWS](https://docs.microsoft.com/azure/active-directory/saas-apps/frontify-tutorial), [SynchroNet CLICK](https://docs.microsoft.com/azure/active-directory/saas-apps/synchronet-click-tutorial) [empower](https://www.made-in-office.com/en/) [Trend Micro Web Security(TMWS)](https://review.docs.microsoft.com/azure/active-directory/saas-apps/trend-micro-tutorial)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>Podpora rozdílových dotazů Microsoft Graph pro oAuth2PermissionGrant k dispozici pro Public Preview

**Zadejte:** Nová funkce

**Kategorie služby:** MS Graph

**Schopnost produktu:** Vývojářské prostředí

Pro verzi Public Preview je k dispozici rozdílový dotaz pro oAuth2PermissionGrant. Nyní můžete sledovat změny bez nutnosti průběžného dotazování Microsoft Graph. [Další informace](https://docs.microsoft.com/graph/api/oAuth2PermissionGrant-delta?view=graph-rest-beta&tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>Obecně dostupná podpora Microsoft Graphch rozdílových dotazů pro kontakt organizace

**Zadejte:** Nová funkce

**Kategorie služby:** MS Graph

**Schopnost produktu:** Vývojářské prostředí

Rozdílový dotaz pro kontakty organizace je všeobecně k dispozici! Nyní můžete sledovat změny v produkčních aplikacích bez nutnosti průběžného dotazování Microsoft Graph. Nahraďte veškerý stávající kód, který se průběžně dotazuje orgContact data na rozdílový dotaz, aby významně zvýšil výkon. [Další informace](https://docs.microsoft.com/graph/api/orgcontact-delta?view=graph-rest-1.0&tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>Podpora rozdílových dotazů Microsoft Graph pro aplikaci všeobecně dostupná

**Zadejte:** Nová funkce

**Kategorie služby:** MS Graph

**Schopnost produktu:** Vývojářské prostředí

Rozdílový dotaz pro aplikace je všeobecně k dispozici! Nyní můžete sledovat změny v produkčních aplikacích bez nutnosti průběžného dotazování Microsoft Graph. Nahraďte veškerý stávající kód, který nepřetržitě dotazuje data aplikací podle rozdílového dotazu a významně tak vylepšit výkon. [Další informace](https://docs.microsoft.com/graph/api/application-delta?view=graph-rest-1.0)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>Podpora rozdílových dotazů Microsoft Graph pro jednotky pro správu, které jsou k dispozici pro Public Preview

**Zadejte:** Nová funkce

**Kategorie služby:** MS Graph

**Schopnost produktu:** Pro veřejné verze Preview je k dispozici rozdílový dotaz vývojářského prostředí pro jednotky pro správu. Nyní můžete sledovat změny bez nutnosti průběžného dotazování Microsoft Graph. [Další informace](https://docs.microsoft.com/graph/api/administrativeunit-delta?view=graph-rest-beta&tabs=http)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>Spravovat telefonní čísla pro ověřování a další informace v nových Microsoft Graphch rozhraní API beta verze

**Zadejte:** Nová funkce

**Kategorie služby:** MS Graph

**Schopnost produktu:** Vývojářské prostředí

Tato rozhraní API jsou klíčovým nástrojem pro správu metod ověřování vašich uživatelů. Nyní můžete programově předregistrovat a spravovat ověřovatele používané pro MFA a Samoobslužné resetování hesla (SSPR). To je jedna z nejžádanějších funkcí v rámci Azure MFA, SSPR a Microsoft Graphch prostorů. Nová rozhraní API, která jsme vydali v tomto Wave, vám poskytnou možnost:

- Čtení, přidávání, aktualizace a odebírání telefonických ověřování uživatele
- Resetování hesla uživatele
- Zapnutí a vypnutí služby SMS – přihlášení

Další informace najdete v tématu [Přehled rozhraní API metody ověřování Azure AD](https://docs.microsoft.com/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

---

### <a name="administrative-units-public-preview"></a>Public Preview jednotky pro správu

**Zadejte:** Nová funkce

**Kategorie služby:** RBAC

**Schopnost produktu:** Access Control

Jednotky pro správu umožňují udělit oprávnění správce, která jsou omezená na oddělení, oblast nebo jiný segment vaší organizace, kterou definujete. Jednotky pro správu můžete použít k delegování oprávnění pro místní správce nebo k nastavení zásad na podrobné úrovni. Správce uživatelských účtů může například aktualizovat informace o profilu, resetovat hesla a přiřazovat licence uživatelům pouze ve své jednotce pro správu.

Pomocí jednotek pro správu může centrální správce:

- Vytvoření jednotky pro správu pro decentralizovanou správu prostředků
- Přiřazení role s oprávněními správce jenom pro uživatele Azure AD v jednotce pro správu
- Podle potřeby naplňte jednotky pro správu uživateli a skupinami.

Další informace najdete v tématu [Správa jednotek pro správu v Azure Active Directory (Preview)](https://aka.ms/AdminUnitsDocs).

---

### <a name="printer-administrator-and-printer-technician-built-in-roles"></a>Správce tiskáren a předdefinované role technika tiskárny

**Zadejte:** Nová funkce

**Kategorie služby:** RBAC

**Schopnost produktu:** Access Control

**Správce tiskárny**: uživatelé s touto rolí můžou registrovat tiskárny a spravovat všechny aspekty všech konfigurací tiskáren v rámci univerzálního tiskového řešení Microsoftu, včetně nastavení univerzálního tiskového konektoru. Můžou si udělit souhlas s všemi delegovanými žádostmi o oprávnění k tisku. Správci tiskáren mají také přístup k tiskovým sestavám. 

**Technik tiskárny**: uživatelé s touto rolí můžou registrovat tiskárny a spravovat stav tiskáren v řešení univerzálního tisku Microsoftu. Můžou si taky přečíst všechny informace o konektoru. Klíčové úkoly, které technik tiskárny nemůže dělat, jsou nastavení oprávnění uživatele na tiskárnách a sdílení tiskáren. [Další informace](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>Integrovaná role správce hybridní identity

**Zadejte:** Nová funkce

**Kategorie služby:** RBAC

**Schopnost produktu:** Access Control

Uživatelé v této roli můžou povolit, konfigurovat a spravovat služby a nastavení související s povolením hybridní identity ve službě Azure AD. Tato role umožňuje nakonfigurovat Azure AD na jednu ze tří podporovaných metod ověřování&#8212;hesla pro synchronizaci hodnot hash hesel (KOSMETICE), předávacího ověřování (PTA) nebo federace (AD FS nebo poskytovatele federace třetí strany) &#8212;a nasazení souvisejících místních infrastruktur, aby je bylo možné povolit. Místní infrastruktura zahrnuje zřizování a PTA agenty. Tato role uděluje možnost Povolit bezproblémové jednotné přihlašování (S-SSO) k zajištění bezproblémového ověřování na zařízeních s jiným systémem než Windows 10 nebo na počítačích s jiným systémem než Windows Server 2016. Kromě toho tato role uděluje možnost Zobrazit protokoly přihlášení a získat přístup ke stavu a analýzám pro účely monitorování a řešení potíží. [Další informace](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>Integrovaná role správce sítě

**Zadejte:** Nová funkce

**Kategorie služby:** RBAC

**Schopnost produktu:** Access Control

Uživatelé s touto rolí můžou zkontrolovat doporučení k architektuře hraničního sítě od Microsoftu, která jsou založená na telemetrie sítě od jejich uživatelských umístění. Výkon sítě pro Office 365 spoléhá na pečlivou architekturu hraniční sítě zákazníka v podniku, která je obecně specifická pro konkrétní uživatelské umístění. Tato role umožňuje upravovat zjištěná umístění uživatelů a konfiguraci síťových parametrů pro tato umístění, aby bylo usnadněno lepší měření telemetrie a doporučení pro návrh. [Další informace](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#network-administrator)

---

### <a name="bulk-activity-and-downloads-in-the-azure-ad-admin-portal-experience"></a>Hromadná aktivita a stahování v prostředí portálu pro správu Azure AD

**Zadejte:** Nová funkce

**Kategorie služby:** Správa uživatelů

**Schopnost produktu:** Službě

Teď můžete provádět hromadné aktivity uživatelů a skupin ve službě Azure AD tak, že nahrajete soubor CSV na portálu pro správu Azure AD. Můžete vytvářet uživatele, odstraňovat uživatele a zvát uživatele typu Host. A můžete přidat nebo odebrat členy ze skupiny.

Můžete si také stáhnout seznamy prostředků Azure AD z prostředí portálu pro správu Azure AD. Můžete si stáhnout seznam uživatelů v adresáři, seznam skupin v adresáři a členy konkrétní skupiny.

Další informace najdete v následujících informacích:

- [Vytvoření uživatelů](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-add) nebo [pozvání uživatelů typu Host](https://docs.microsoft.com/azure/active-directory/b2b/tutorial-bulk-invite)
- [Odstranit uživatele](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-delete) nebo [Obnovit odstraněné uživatele](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-restore)
- [Stažení seznamu uživatelů](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-download) nebo [stažení seznamu skupin](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download)
- [Přidání (import) členů](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members) nebo [odebrání členů](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members) nebo [stažení seznamu členů](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members) pro skupinu

---

### <a name="my-staff-delegated-user-management"></a>Správa uživatelů delegovaná od zaměstnanců

**Zadejte:** Nová funkce

**Kategorie služby:** Správa uživatelů

**Schopnost produktu:**

Moji zaměstnanci umožňují správcům Firstline, jako je Správce úložiště, zajistit, aby jejich zaměstnanci měli přístup k účtům Azure AD. Namísto spoléhání na centrální Helpdesk můžou organizace delegovat běžné úlohy, jako je resetování hesel nebo změna telefonních čísel, na Firstline Manager. Uživatel, který nemá přístup ke svému účtu, může pomocí mých zaměstnanců znovu získat přístup jenom v několika kliknutích, aniž by to vyžadovalo Helpdesk nebo pracovníky IT. Další informace najdete v tématu [Správa uživatelů pomocí možnosti Moji zaměstnanci (Preview)](https://aka.ms/MyStaffAdminDocs) a [delegování správy uživatelů pomocí mých zaměstnanců (Preview)](https://aka.ms/MyStaffUserDocs).

---

### <a name="an-upgraded-end-user-experience-in-access-reviews"></a>Upgradované prostředí pro koncové uživatele v recenzích přístupu

**Zadejte:** Změněná funkce

**Kategorie služby:** Kontroly přístupu

**Schopnost produktu:** Zásady správného řízení identity

Na portálu moje aplikace jsme aktualizovali možnosti kontrolora pro kontroly přístupu ke službě Azure AD. Po skončení dubna se recenzenti, kteří se přihlásili ke kontrolám přístupu ke službě Azure AD, zobrazí informační zpráva s upozorněním, že si budou moct vyzkoušet aktualizované prostředí v mém přístupu. Upozorňujeme, že aktualizovaná prostředí kontroly přístupu nabízí stejné funkce jako aktuální prostředí, ale s vylepšeným uživatelským rozhraním nad novými funkcemi, které umožní vašim uživatelům zvýšit produktivitu. [Další informace o aktualizovaném prostředí najdete tady](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review). Tato verze Public Preview bude poslední až do konce července 2020. Na konci července budou recenzenti, kteří se nevyjádřili do prostředí verze Preview, automaticky přesměrováni na můj přístup, aby mohli provádět kontroly přístupu. Pokud chcete, aby se kontroloři trvale přepnuli do prostředí Preview v mém přístupu, [uveďte prosím žádost](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u).

---

### <a name="workday-inbound-user-provisioning-and-writeback-apps-now-support-the-latest-versions-of-workday-web-services-api"></a>Aplikace pro zřizování a zpětného zápisu příchozích uživatelů v Workday teď podporují nejnovější verze rozhraní API webových služeb Workday.

**Zadejte:** Změněná funkce

**Kategorie služby:** Zřizování aplikací

**Schopnost produktu:** 

Na základě zpětné vazby od zákazníků jsme teď v galerii podnikových aplikací aktualizovali aplikace pro zřizování a zpětný zápis uživatelů Workday na podporu nejnovějších verzí rozhraní API WWS (Workday Web Services). V této změně můžou zákazníci zadat verzi rozhraní API WWS, kterou by chtěli použít v připojovacím řetězci. Díky tomu můžou zákazníci získat další atributy pro personální přístup k dispozici v rámci vydání pracovního dne. Aplikace pro zpětný zápis v Workday teď používá doporučenou webovou službu Change_Work_Contact_Info Workday k překonání omezení Maintain_Contact_Info.

Pokud v připojovacím řetězci není zadaná žádná verze, aplikace ve výchozím nastavení pro příchozí zřizování v rámci Workday bude nadále používat WWS v 21.1 k přepínání na nejnovější rozhraní API Workday pro příchozí zřizování uživatelů, aby mohli zákazníci aktualizovat připojovací řetězec, jak je uvedeno [v kurzu](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles) , a také aktualizovat XPath používané pro atributy Workday, jak je popsáno v [Referenční příručce k atributům Workday](https://docs.microsoft.com/azure/active-directory/app-provisioning/workday-attribute-reference#xpath-values-for-workday-web-services-wws-api-v30). 

Pokud chcete používat nové rozhraní API pro zpětný zápis, nemusíte v aplikaci pro zřizování služby pro zápis do pracovního dne vyžadovat žádné změny. Na straně pracovního dne zajistěte, aby měl účet ISU (Workday Integration System User) oprávnění k vyvolání Change_Work_Contact obchodního procesu, jak je uvedeno v části kurz, v tématu [Konfigurace oprávnění zásad zabezpečení obchodních procesů](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#configuring-business-process-security-policy-permissions). 

Aktualizovali jsme náš [Průvodce kurzy](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial) , který by odrážel novou podporu verze API.

---

### <a name="users-with-default-access-role-are-now-in-scope-for-provisioning"></a>Uživatelé s výchozí rolí přístupu teď jsou v oboru pro zřizování.

**Zadejte:** Změněná funkce

**Kategorie služby:** Zřizování aplikací

**Schopnost produktu:** Správa životního cyklu identit

V minulosti byly uživatelé s výchozí rolí přístupu mimo obor pro zřizování. Slyšeli jsme, že zákazníci chtějí mít tuto roli v oboru pro zřizování. Od 16. dubna 2020 budou všechny nové konfigurace zřizování umožňovat zřízení uživatelů s výchozí rolí přístupu. Postupně změníme chování stávajících zřizovacích konfigurací tak, aby podporovaly zřizování uživatelů s touto rolí. [Další informace](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned)

---

### <a name="updated-provisioning-ui"></a>Aktualizované uživatelské rozhraní pro zřizování

**Zadejte:** Změněná funkce

**Kategorie služby:** Zřizování aplikací

**Schopnost produktu:** Správa životního cyklu identit

Naše prostředí pro zřizování jsme aktualizovali tak, aby se vytvořilo lépe zaměřené zobrazení správy. Když přejdete do okna zřizování pro podnikovou aplikaci, která už je nakonfigurovaná, budete moct snadno monitorovat průběh zřizování a spravovat akce, jako je spuštění, zastavení a restartování zřizování. [Další informace](https://docs.microsoft.com/azure/active-directory/app-provisioning/configure-automatic-user-provisioning-portal)

---

### <a name="dynamic-group-rule-validation-is-now-available-for-public-preview"></a>Ověřování pravidla dynamické skupiny je teď k dispozici pro Public Preview

**Zadejte:** Změněná funkce

**Kategorie služby:** Správa skupin

**Schopnost produktu:** Prostřednictvím

Azure Active Directory (Azure AD) nyní poskytuje způsob, jak ověřovat dynamická pravidla skupiny. Na kartě **ověřit pravidla** můžete ověřit své dynamické pravidlo proti ukázkovým členům skupiny a potvrdit, že pravidlo funguje podle očekávání. Správci chtějí při vytváření nebo aktualizaci pravidel dynamických skupin zjistit, jestli uživatel nebo zařízení bude členem skupiny. Tato možnost pomáhá vyhodnotit, jestli uživatel nebo zařízení splňuje kritéria pravidla a pomáhá při řešení potíží, když členství neočekáváte.

Další informace najdete v tématu [ověření pravidla členství v dynamické skupině (Preview)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-rule-validation).

---

### <a name="identity-secure-score---security-defaults-and-mfa-improvement-action-updates"></a>Skóre identity Secure – výchozí hodnoty zabezpečení a aktualizace pro akce zlepšování vícefaktorového ověřování

**Zadejte:** Změněná funkce

**Kategorie služby:** NENÍ K DISPOZICI

**Schopnost produktu:** Zabezpečení identity & ochrana

**Podpora výchozích hodnot zabezpečení pro akce zlepšování Azure AD:** Microsoft Secure skore bude aktualizovat akce vylepšení tak, aby podporovaly [výchozí nastavení zabezpečení ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults), což usnadňuje lepší ochranu vaší organizace s předem nakonfigurovaným nastavením zabezpečení pro běžné útoky. To bude mít vliv na následující akce vylepšení:

- Zajistěte, aby všichni uživatelé mohli pro zabezpečený přístup dokončit službu Multi-Factor Authentication
- Vyžadovat MFA pro administrativní role
- Povolit zásadu pro blokování starších verzí ověřování
 
**Aktualizace akcí zlepšování MFA:** Aby se zajistilo, že podniky budou při používání zásad, které pracují s jejich podnikáním, nemuseli mít jistotu, že společnost Microsoft Security skore odstranila tři akce zlepšení, které se centrují pro vícefaktorové ověřování a přidaly dvě.

Odebrané akce zlepšení:

- Registrovat všechny uživatele pro službu Multi-Factor Authentication
- Vyžadování MFA pro všechny uživatele
- Vyžadovat MFA pro privilegované role Azure AD

Přidání akcí vylepšení:

- Zajistěte, aby všichni uživatelé mohli pro zabezpečený přístup dokončit službu Multi-Factor Authentication
- Vyžadovat MFA pro administrativní role

Tyto nové akce zlepšování vyžadují registraci uživatelů nebo správců pro službu Multi-Factor Authentication (MFA) v rámci vašeho adresáře a vytvoření správné sady zásad, které vyhovují potřebám vaší organizace. Hlavním cílem je mít flexibilitu při současném zajištění, že se všichni uživatelé a správci můžou ověřit s více faktory nebo s výzvou k ověření identity na základě rizika. To může mít formu, že má více zásad, které používají vymezená rozhodnutí nebo nastavovat výchozí hodnoty zabezpečení (od 16. března), které Microsoftu pomohou rozhodnout, kdy chtějí uživatele pro MFA požádat. [Přečtěte si další informace o tom, co je nového ve službě Microsoft Secure skore](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score?view=o365-worldwide#whats-new).

---

## <a name="march-2020"></a>Březen 2020

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>Nespravované účty Azure Active Directory v aktualizaci B2B pro březen 2021

**Zadejte:** Plánování změn  
**Kategorie služby:** B2B  
**Schopnost produktu:** B2B/B2C
 
**Od 31. března 2021**přestane společnost Microsoft podporovat uplatnění pozvánky tím, že pro scénáře spolupráce B2B vytvoří neAzure Active Directory spravované účty a klienty služby Azure AD. V přípravné době doporučujeme, abyste se rozhodli, že se chcete přihlásit k [e-mailu ověřování heslem jednorázového hesla](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode).

---

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>Uživatelé s výchozí rolí přístupu budou v oboru pro zřizování.

**Zadejte:** Plánování změn  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** Správa životního cyklu identit
 
V minulosti byly uživatelé s výchozí rolí přístupu mimo obor pro zřizování. Slyšeli jsme, že zákazníci chtějí mít tuto roli v oboru pro zřizování. Pracujeme na nasazení změny, takže všechny nové konfigurace zřizování umožní zřídit uživatele s výchozí rolí přístupu. Postupně změníme chování stávajících zřizovacích konfigurací tak, aby podporovaly zřizování uživatelů s touto rolí. Není vyžadována žádná akce zákazníka. Až bude tato změna provedena, budeme po aktualizaci publikovat do naší [dokumentace](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned) .

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Spolupráce Azure AD B2B bude k dispozici v Microsoft Azure provozována v klientech 21Vianet (Azure Čína 21Vianet).

**Zadejte:** Plánování změn  
**Kategorie služby:** B2B  
**Schopnost produktu:** B2B/B2C
 
Možnosti spolupráce Azure AD B2B budou dostupné v Microsoft Azure provozovaných v klientech 21Vianet (Azure Čína 21Vianet) a umožní uživatelům v tenantovi Azure Čína 21Vianet spolupracovat bez problémů s uživateli v jiných klientech Azure Čína 21Vianet. [Přečtěte si další informace o spolupráci Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/).

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Předesignování e-mailu pozvánky pro spolupráci Azure AD B2B

**Zadejte:** Plánování změn  
**Kategorie služby:** B2B  
**Schopnost produktu:** B2B/B2C
 
[E-maily](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) odeslané službou pozvánky pro spolupráci Azure AD B2B pro pozvání uživatelů do tohoto adresáře budou přepracovány, aby se informace o pozvánce a další kroky uživatele vymažou.

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>Změny zásad HomeRealmDiscovery se zobrazí v protokolech auditu.

**Zadejte:** Určí  
**Kategorie služby:** Ověřen  
**Schopnost produktu:** Monitorování & vytváření sestav
 
Opravili jsme chybu, kdy se změny [zásady HomeRealmDiscovery](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal) nezahrnuly do protokolů auditu. Teď budete moct zobrazit, kdy a jak se zásady změnily a kým. 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Nové federované aplikace dostupné v Aplikace Azure AD galerii – březen 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
V březnu 2020 jsme do Galerie aplikací přidali tyto 51 nové aplikace s podporou federace: 

[Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect), [Zoho One Čína](https://docs.microsoft.com/azure/active-directory/saas-apps/zoho-one-china-tutorial), [PlusPlus](https://test.plusplus.app/auth/login/azuread-outlook/), [Profit.co aplikace SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/profitco-saml-app-tutorial) [IPoint poskytovatel služeb](https://docs.microsoft.com/azure/active-directory/saas-apps/ipoint-service-provider-tutorial), [contexxt.AI koule](https://contexxt-sphere.com/login) [Skills Workflow](https://docs.microsoft.com/azure/active-directory/saas-apps/skills-workflow-tutorial), [vyhodnocení informací získaných by Invictus](https://docs.microsoft.com/azure/active-directory/saas-apps/wisdom-by-invictus-tutorial), [odlesk digitálního podepisování](https://spark-dev.pixelnebula.com/login), [LOGZ.IO-Cloud pozorovatelé pro inženýry](https://docs.microsoft.com/azure/active-directory/saas-apps/logzio-cloud-observability-for-engineers-tutorial), [spektrum](https://docs.microsoft.com/azure/active-directory/saas-apps/spectrumu-tutorial), BizzContact [,](https://bizzcontact.app/) [Elqano SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/elqano-sso-tutorial) [, MarketSignShare](http://www.signshare.com/), [CrossKnowledge Learning Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/crossknowledge-learning-suite-tutorial), [Netvision COMPAS](https://docs.microsoft.com/azure/active-directory/saas-apps/netvision-compas-tutorial), [FCM hub](https://docs.microsoft.com/azure/active-directory/saas-apps/fcm-hub-tutorial) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/lift-tutorial) [žebr a/S Byggeweb Mobile,](https://apps.apple.com/us/app/docia/id529058757)GoLinks [,](https://docs.microsoft.com/azure/active-directory/saas-apps/golinks-tutorial)služby Datadog [,](https://docs.microsoft.com/azure/active-directory/saas-apps/datadog-tutorial)Zscaler [User Portal,](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-b2b-user-portal-tutorial)Planview Enterprise One [Aster](https://demo.asterapp.io/login) [platforma IP](https://docs.microsoft.com/azure/active-directory/saas-apps/ip-platform-tutorial), [Planview Enterprise One](https://docs.microsoft.com/azure/active-directory/saas-apps/planview-enterprise-one-tutorial) [Invision](https://docs.microsoft.com/azure/active-directory/saas-apps/invision-tutorial), [WatchTeams](https://www.devfinition.com/) [pipedrivu](https://docs.microsoft.com/azure/active-directory/saas-apps/pipedrive-tutorial), [prezentující Workshop](https://app.showcaseworkshop.com/), [platforma GreenLight Integration Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-integration-platform-tutorial), [Správa přístupu kompatibilní s GreenLight](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-compliant-access-management-tutorial), [vzdělávání grok](https://docs.microsoft.com/azure/active-directory/saas-apps/grok-learning-tutorial), [Miradore online](https://login.online.miradore.com/), Khoros [péče](https://docs.microsoft.com/azure/active-directory/saas-apps/khoros-care-tutorial) [Code42](https://docs.microsoft.com/azure/active-directory/saas-apps/code42-tutorial) , AskYourTeam, [TruNarrative](https://docs.microsoft.com/azure/active-directory/saas-apps/trunarrative-tutorial) [, Smartwaiver, bizagi](https://www.smartwaiver.com/m/user/sw_login.php?wms_login) [Studio pro automatizaci digitálních procesů](https://docs.microsoft.com/azure/active-directory/saas-apps/bizagi-studio-for-digital-process-automation-tutorial), [insuiteX, Sybo](https://docs.microsoft.com/azure/active-directory/saas-apps/askyourteam-tutorial) [, Britive](https://www.systexsoftware.com.tw/) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/britive-tutorial), [,](https://docs.microsoft.com/azure/active-directory/saas-apps/e-days-tutorial) [WhosOffice,](https://docs.microsoft.com/azure/active-directory/saas-apps/whosoffice-tutorial) [SDN](https://portal.kollective.app/login) [, Kollective,](https://app.witivio.com/) [Witivio](https://my.playvox.com/login), [Korn trajekt 360](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-360-tutorial), [areál kavárny](https://docs.microsoft.com/azure/active-directory/saas-apps/campus-cafe-tutorial) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/catchpoint-tutorial) [PlayVox, záchytný bod](https://www.insuite.jp/) [Node Insight](https://admin.nodeinsight.com/AADLogin.aspx)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Spolupráce Azure AD B2B dostupná v Azure Government tenantů

**Zadejte:** Nová funkce  
**Kategorie služby:** B2B  
**Schopnost produktu:** B2B/B2C
 
Funkce spolupráce Azure AD B2B jsou teď dostupné mezi některými Azure Government klienty.  Pokud chcete zjistit, jestli je váš tenant schopný používat tyto možnosti, postupujte podle pokynů v tématu [Jak poznám, jestli je spolupráce B2B dostupná ve svém Tenantovi Azure USA?](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant).

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>Azure Monitor Integration pro protokoly Azure je teď k dispozici v Azure Government

**Zadejte:** Nová funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav
 
Azure Monitor integrace s protokoly služby Azure AD je teď k dispozici v Azure Government. Protokoly služby Azure AD (protokoly auditu a přihlašování) můžete směrovat do účtu úložiště, centra událostí a Log Analytics. Projděte si [podrobnou dokumentaci](https://aka.ms/aadlogsinamd) a [plány nasazení pro vytváření sestav a monitorování](https://docs.microsoft.com/azure/active-directory/reports-monitoring/plan-monitoring-and-reporting) scénářů Azure AD.

---

### <a name="identity-protection-refresh-in-azure-government"></a>Aktualizace Identity Protection v Azure Government

**Zadejte:** Nová funkce  
**Kategorie služby:** Ochrana identity  
**Schopnost produktu:** Zabezpečení identity & ochrana

S radostí sdílíme, že teď jsme na Microsoft Azure Government portálu navedli aktualizované [Azure AD Identity Protection](https://aka.ms/IdentityProtectionDocs)   prostředí [Microsoft Azure Government portal](https://portal.azure.us/). Další informace najdete v příspěvku na [blogu o oznámení](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667).

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>Zotavení po havárii: Stáhněte si konfiguraci zřizování a uložte ji.

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** Správa životního cyklu identit
 
Služba zřizování Azure AD poskytuje bohatou sadu možností konfigurace. Zákazníci musí mít možnost uložit svou konfiguraci, aby na ně mohli později odkazovat nebo se vrátit na známou správnou verzi. Přidali jsme možnost stáhnout si vaši konfiguraci zřizování jako soubor JSON a nahrát ho, až ho budete potřebovat. [Přečtěte si další informace](https://docs.microsoft.com/azure/active-directory/app-provisioning/export-import-provisioning-configuration).

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR (Samoobslužné resetování hesla) teď vyžaduje dvě brány pro správce v Microsoft Azure provozované společností 21Vianet (Azure Čína 21Vianet). 

**Zadejte:** Změněná funkce  
**Kategorie služby:** Samoobslužné resetování hesla  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
Správci, kteří používají Samoobslužné resetování hesla (SSPR), dříve v Microsoft Azure pracovali pomocí samoobslužného resetování hesla () k resetování jejich identity, potřebují jenom jednu bránu (Challenge). Ve veřejných a dalších národních cloudech musí správci při použití SSPR vykazovat svoji identitu pomocí dvou bran. Protože ale nepodporujeme SMS nebo telefonní hovory v Azure Čína 21Vianet, povolili jsme správcům resetování hesla s jednou branou.

Vytváříme paritu funkcí SSPR mezi Azure Čína 21Vianet a veřejným cloudem. Při použití SSPR musí správci používat dvě brány. Podporuje se SMS, telefonní hovory a ověřovací oznámení a kódy aplikace ověřovatele. [Přečtěte si další informace](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#administrator-reset-policy-differences).

---

### <a name="password-length-is-limited-to-256-characters"></a>Délka hesla je omezená na 256 znaků.

**Zadejte:** Změněná funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů
 
Aby byla zajištěna spolehlivost služby Azure AD, uživatelská hesla jsou nyní omezena na délku až 256 znaků. Uživatelům s hesly delšími než se zobrazí výzva ke změně hesla při následném přihlášení, a to buď kontaktováním správce, nebo pomocí funkce samoobslužného resetování hesla.

Tato změna byla povolena v březnu nějak změnily 13, 2020, 10AM PST (18:00 UTC) a chyba je AADSTS 50052, InvalidPasswordExceedsMaxLength. Další podrobnosti najdete v [oznámení o změně](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#user-passwords-will-be-restricted-to-256-characters) .

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Protokoly pro přihlášení k Azure AD jsou teď dostupné pro všechny bezplatné klienty prostřednictvím Azure Portal

**Zadejte:** Změněná funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav
 
Od tohoto okamžiku můžou zákazníci, kteří mají bezplatné klienty, získat přístup k [protokolům přihlášení Azure AD z Azure Portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) po dobu až 7 dnů. Dříve byly protokoly přihlášení k dispozici pouze pro zákazníky s licencemi Azure Active Directory Premium. Díky této změně mají všichni klienti k těmto protokolům přístup prostřednictvím portálu.

> [!NOTE]
> Zákazníci ještě potřebují licenci Premium (Azure Active Directory Premium P1 nebo P2) pro přístup k protokolům přihlášení prostřednictvím rozhraní API Microsoft Graph a Azure Monitor.

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>Vyřazení možnosti skupiny v úrovni adresáře ze skupin Obecné nastavení v Azure Portal

**Zadejte:** Zastaralé  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Prostřednictvím

Abychom zákazníkům poskytli pružnější způsob vytváření skupin pro všechny adresáře, které nejlépe vyhovují jejich potřebám, nahradili jsme možnost **skupiny** pro všechny adresáře ze **Groups**  >  **obecných** nastavení skupiny v Azure Portal s odkazem na [dokumentaci k dynamické skupině](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership). Vylepšili jsme naši dokumentaci, aby zahrnovala další pokyny, aby správci mohli vytvářet skupiny všech uživatelů, které zahrnují nebo vylučují uživatele typu Host.

---

## <a name="february-2020"></a>Únor 2020

### <a name="upcoming-changes-to-custom-controls"></a>Nadcházející změny vlastních ovládacích prvků

**Zadejte:** Plánování změn  
**Kategorie služby:** VÍCEFAKTOROVÉHO  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
Plánujeme nahradit aktuální vlastní ovládací prvky jako v rámci přístupu, který umožňuje bezproblémovou funkčnost funkcí ověřování poskytovaných partnerem s Azure Active Directory správce a koncového uživatele. V dnešní době se Partnerská řešení MFA setkávají s následujícími omezeními: fungují až po zadání hesla; neslouží jako MFA pro podrobné ověřování v jiných klíčových scénářích. a neintegrují se do funkcí pro správu přihlašovacích údajů pro koncové uživatele nebo správce. Nová implementace umožní partnerským faktorům pro ověřování spolupracovat společně s integrovanými faktory pro klíčové scénáře, včetně registrace, využití, deklarací MFA, krokování ověřování, vytváření sestav a protokolování. 

Vlastní ovládací prvky budou nadále podporovány ve verzi Preview vedle nového návrhu, dokud nedosáhne obecné dostupnosti. V tomto okamžiku zákazníkům poskytneme čas na migraci na nový návrh. Z důvodu omezení současného přístupu nebudeme nové poskytovatele připravujeme, dokud nebude nový návrh k dispozici. Úzce spolupracujeme se zákazníky a poskytovateli a pošle vám časovou osu, jak získáme blíž. [Přečtěte si další informace](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#).

---

### <a name="identity-secure-score---mfa-improvement-action-updates"></a>Skóre identity Secure – aktualizace akcí zlepšování MFA

**Zadejte:** Plánování změn  
**Kategorie služby:** VÍCEFAKTOROVÉHO  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
Aby se zajistilo, že podniky budou při používání zásad, které pracují s jejich podnikáním, zajistila nejvyšší zabezpečení, odstraňuje Microsoft Security skore tři akce zlepšování na střed služby Multi-Factor Authentication (MFA) a přidává dvě.

Odeberou se následující akce vylepšení:

- Registrovat všechny uživatele pro MFA
- Vyžadování MFA pro všechny uživatele
- Vyžadovat MFA pro privilegované role Azure AD

Budou přidány následující akce vylepšení:

- Ujistěte se, že všichni uživatelé můžou pro zabezpečený přístup dokončit MFA.
- Vyžadovat MFA pro administrativní role

Tyto nové akce zlepšování budou vyžadovat registraci uživatelů nebo správců pro MFA napříč vaším adresářem a vytvoření správné sady zásad, které vyhovují potřebám vaší organizace. Hlavním cílem je mít flexibilitu při současném zajištění, že se všichni uživatelé a správci můžou ověřit s více faktory nebo s výzvou k ověření identity na základě rizika. To může mít formu nastavení výchozích hodnot zabezpečení, které Microsoftu umožní rozhodnout, kdy mají požádat uživatele o MFA, nebo jestli mají více zásad, které použijí rozhodnutí s vymezeným oborem. V rámci těchto aktualizací akcí vylepšení již nebudou zásady ochrany směrných plánů zahrnuty do výpočtů bodování. [Přečtěte si další informace o tom, co připravujeme v zabezpečeném skóre Microsoftu](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score-whats-coming?view=o365-worldwide).

---

### <a name="azure-ad-domain-services-sku-selection"></a>Výběr skladové položky Azure AD Domain Services

**Zadejte:** Nová funkce  
**Kategorie služby:** Azure AD Domain Services  
**Schopnost produktu:** Azure AD Domain Services
 
Slyšeli jsme zpětnou vazbu, že Azure AD Domain Services zákazníci chtějí větší flexibilitu při výběru úrovní výkonu pro své instance. Od 1. února 2020 jsme přešli z dynamického modelu (kde Azure AD určuje výkon a cenovou úroveň na základě počtu objektů) k modelu automatického výběru. Zákazníci si teď můžou vybrat úroveň výkonu, která odpovídá jejich prostředí. Tato změna nám taky umožňuje povolit nové scénáře, jako jsou doménové struktury prostředků, a prémiové funkce jako denní zálohy. Počet objektů je nyní neomezený pro všechny skladové položky, ale budeme pro každou úroveň dál nabízet návrhy počtu objektů.

**Není vyžadována žádná okamžitá akce zákazníka.** Pro stávající zákazníky je dynamická vrstva, která byla používána od 1. února 2020, určena pro novou výchozí úroveň. Výsledek této změny nemá žádný vliv na ceny nebo výkon. Až dál, zákazníci Azure služba AD DS budou muset vyhodnotit požadavky na výkon při změně velikosti adresáře a vlastností zatížení. Přepínání mezi úrovněmi služeb bude i nadále operací bez výpadků. zákazníci už nebudou automaticky přesouvat na nové úrovně na základě nárůstu jejich adresáře. Navíc se nebudou zvyšovat žádné ceny a nové ceny budou zarovnány s aktuálním modelem fakturace. Další informace najdete v dokumentaci ke [službě Azure služba AD DS SKU](https://docs.microsoft.com/azure/active-directory-domain-services/administration-concepts#azure-ad-ds-skus) a na [stránce s cenami Azure AD Domain Services](https://azure.microsoft.com/pricing/details/active-directory-ds/).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Nové federované aplikace dostupné v Aplikace Azure AD galerii – únor 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
V únoru 2020 jsme do Galerie aplikací přidali tyto 31 nových aplikací s podporou federace: 

[IamIP patentová platforma](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial), [Experience Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial), [ns1 jednotného přihlašování pro Azure](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial), [Barracuda e-mailové služby](https://ess.barracudanetworks.com/sso/azure), [vytváření sestav ABA](https://myaba.co.uk/client-access/signin/auth/msad), [v případě řešení krizí – online portál](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial), [BIC pro Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial), [Včelařský datový konektor Azure AD](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial), [vyhodnocování trajektů](https://www.kornferry.com/solutions/kf-digital/kf-assess), [Verkada příkaz](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial), [Splashtop](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial), [Syxsense](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial), [EAB Navigate](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial), [New Relic (omezená verze)](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial), [Thulium](https://admin.thulium.com/login/instance), [správce lístků](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial), [Výběr šablony pro týmy](https://links.officeatwork.com/templatechooser-download-teams), [včely](https://www.beesy.me/index.php/site/login), [systém podpory Health](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial), [MURAL](https://app.mural.co/signup), [podregistr](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial), [LavaDo](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview), [Wakelet](https://wakelet.com/login), [Firmex VDR](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial), [ThingLink pro učitele a školy](https://www.thinglink.com/), [Coda](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial), [NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product), [WEDO](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial), [InvitePeople](https://invitepeople.com/login), [REPRINTER – článek Galaxy](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial), [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial)

 
Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Nové zřizovací konektory v galerii aplikací Azure AD – únor 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
Nyní můžete automatizovat vytváření, aktualizaci a odstraňování uživatelských účtů pro tyto nově integrované aplikace:

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud by Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
- [Zapier](https://docs.microsoft.com/azure/active-directory/saas-apps/zapier-provisioning-tutorial)

Další informace o tom, jak lépe zabezpečit vaši organizaci pomocí automatizovaného zřizování uživatelských účtů, najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>Podpora klíčů zabezpečení FIDO2 v hybridních prostředích pro Azure AD

**Zadejte:** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů
 
Oznamujeme vydání verze Public Preview podpory Azure AD pro klíče zabezpečení FIDO2 v hybridních prostředích. Uživatelé teď můžou použít bezpečnostní klíče FIDO2 k přihlášení ke svým hybridním zařízením s Windows 10 připojeným k Azure AD a získat bezproblémové přihlašování k místním a cloudovým prostředkům. Podpora hybridních prostředí byla od našich zákazníků s nehesly nejdůležitější, protože zpočátku jsme na zařízeních připojených k Azure AD spustili verzi Public Preview pro FIDO2 support. Ověřování bez hesla pomocí pokročilých technologií, jako jsou biometrika a kryptografie veřejného a privátního klíče, poskytují pohodlí a snadné použití při zabezpečení. V této veřejné verzi Preview teď můžete pro přístup k tradičním prostředkům služby Active Directory použít moderní ověřování, jako jsou bezpečnostní klíče FIDO2. Další informace najdete [v jednotného přihlašování k místním prostředkům](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises). 

Informace o tom, jak začít, najdete v tématu [Povolení klíčů zabezpečení FIDO2 pro vašeho tenanta](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key) , kde najdete podrobné pokyny. 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>Nové prostředí můj účet je teď všeobecně dostupné.

**Zadejte:** Změněná funkce  
**Kategorie služby:** Můj profil/účet  
**Schopnost produktu:** Prostředí koncového uživatele
 
Tento účet je teď všeobecně k dispozici pro všechny potřeby správy účtů koncových uživatelů. Koncoví uživatelé mají přístup k tomuto novému webu přes adresu URL nebo v hlavičce nového prostředí moje aplikace. Přečtěte si další informace o všech funkcích samoobslužné služby, které nabízí nové možnosti na [portálu Můj účet](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview).

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>Adresa URL webu mého účtu se aktualizuje na myaccount.microsoft.com

**Zadejte:** Změněná funkce  
**Kategorie služby:** Můj profil/účet  
**Schopnost produktu:** Prostředí koncového uživatele
 
Nové prostředí koncového uživatele mého účtu aktualizuje svou adresu URL na `https://myaccount.microsoft.com` příští měsíc. Další informace o zkušenostech a všech samoobslužných funkcích pro účty, které nabízí koncovým uživatelům, najdete v [nápovědě k portálu Můj účet](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview).

---
 
## <a name="january-2020"></a>Leden 2020
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>Nový portál moje aplikace je teď všeobecně dostupný.

**Zadejte:** Plánování změn  
**Kategorie služby:** Moje aplikace  
**Schopnost produktu:** Prostředí koncového uživatele
 
Upgradujte svoji organizaci na nový portál moje aplikace, který je teď všeobecně dostupný. Další informace o novém portálu a kolekcích najdete v části [Vytvoření kolekcí na portálu moje aplikace](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Pracovní prostory ve službě Azure AD se přejmenovaly na kolekce.

**Zadejte:** Změněná funkce  
**Kategorie služby:** Moje aplikace   
**Schopnost produktu:** Prostředí koncového uživatele
 
Pracovní prostory můžou správci filtrů nakonfigurovat k uspořádání aplikací uživatelů, budou se teď označovat jako kolekce. Přečtěte si další informace o tom, jak je nakonfigurovat při [vytváření kolekcí na portálu moje aplikace](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Azure AD B2C registrace a přihlašování pomocí vlastní zásady (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C
 
Když se telefonní číslo a přihlášení k telefonnímu číslu, vývojáři a podniky můžou svým zákazníkům dovolit zaregistrovat se a přihlásit se pomocí jednorázového hesla, které se pošle na telefonní číslo uživatele přes SMS. Tato funkce také umožní zákazníkovi změnit své telefonní číslo, pokud ztratí přístup k telefonu. Díky vlastním zásadám, registraci telefonů a přihlašování umožňuje vývojářům a podnikům komunikovat svou značku prostřednictvím přizpůsobení stránky. Přečtěte si, jak [nastavit registraci a přihlašování telefonem pomocí vlastních zásad v Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication).
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Nové zřizovací konektory v galerii aplikací Azure AD – leden 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
Nyní můžete automatizovat vytváření, aktualizaci a odstraňování uživatelských účtů pro tyto nově integrované aplikace:

- [Promapp]( https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-provisioning-tutorial)
- [Zscaler Private Access](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

Další informace o tom, jak lépe zabezpečit vaši organizaci pomocí automatizovaného zřizování uživatelských účtů, najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Nové federované aplikace dostupné v Aplikace Azure AD galerii – leden 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
Do ledna 2020 jsme do Galerie aplikací přidali tyto 33 nové aplikace s podporou federace: 

[JOSA](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial), [Fast Edge Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial), [terraformu Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial), [Spintr SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial), [Abibot Netlogistik](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik), [SkyKick](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access), pro, LeaveBot, [LeaveBot](https://leavebot.io/#home) [DataCamp](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial), [TripActions](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial), [SMARTWORK](https://www.intumit.com/english/SmartWork.html), [DOTCOM-monitor](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial), [SSOGEN – Azure AD SSO Gateway pro Oracle E-Business Suite-EBS, PeopleSoft spouštěných místně a jde](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial), [hostované MyCirqa SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial), [platforma pro správu vlastností yuhu](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial), [LumApps](https://sites.lumapps.com/login), [Working Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial), [Talentsoft](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial), [SmartDB pro Microsoft Teams](http://teams.smartdb.jp/login/), [PressPage](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial), [ContractSafe typu Saml2 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial), [Maxient software Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial), [helpshift](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial), [PortalTalk 365](https://www.portaltalk.com/), [koview](https://portal.coreview.com/), [squelch Cloud Office 365 Connector](https://laxmi.squelch.io/login), PingFlow [Authentication](https://app-staging.pingview.io/), PrinterLogic [SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial), [Taskize Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial), Sandwai [,](https://app.sandwai.com/) [EZRentOut](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial), [AssetSonar](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial), [Akari Virtual Assistant](https://akari.io/akari-virtual-assistant/) [Upshotly](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="two-new-identity-protection-detections"></a>Dvě nové detekce ochrany identity

**Zadejte:** Nová funkce  
**Kategorie služby:** Ochrana identity  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
Přidali jsme dva nové typy detekce propojených s přihlašováním do ochrany identity: podezřelá pravidla pro manipulaci s doručenou poštou a možná cestování. Tyto detekce v režimu offline jsou zjišťovány Microsoft Cloud App Security (MCAS) a mají vliv na uživatele a přihlašování v rámci Identity Protection. Další informace o těchto detekcích najdete v našem [typu rizika přihlašování](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk).
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>Zásadní změna: fragmenty identifikátoru URI nebudou přeneseny prostřednictvím přesměrování přihlášení.

**Zadejte:** Změněná funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů
 
Od 8. února 2020 se při odeslání požadavku na login.microsoftonline.com pro přihlášení uživatele služba připojí k žádosti prázdný fragment.  Tím se zabrání třída útoků přesměrování tím, že zajistí, že prohlížeč vymaže všechny existující fragmenty v žádosti. Žádná aplikace by neměla mít závislost na tomto chování. Další informace najdete v tématu [přerušující změny](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#february-2020) v dokumentaci k platformě Microsoft Identity Platform.

