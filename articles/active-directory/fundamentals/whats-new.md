---
title: Co je nového Poznámky k verzi – Azure Active Directory | Microsoft Docs
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
ms.date: 3/4/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f0b53fc47c6e93c0750ba729a1b4670289d7714
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105560399"
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
## <a name="february-2021"></a>Únor 2021

### <a name="email-one-time-passcode-authentication-on-by-default-starting-october-2021"></a>Poslat e-mail jednorázovým ověřováním hesla ve výchozím nastavení od října 2021

**Zadejte:** Plánování změn  
**Kategorie služby:** B2B  
**Schopnost produktu:** B2B/B2C
 

Od 31. října 2021 se Microsoft Azure Active Directory [e-mailové ověřování jednorázového hesla](../external-identities/one-time-passcode.md) stane výchozí metodou pro pozvání účtů a tenantů pro scénáře spolupráce B2B. V tuto chvíli už společnost Microsoft nebude moci uplatnit pozvánky pomocí nespravovaných Azure Active Directorych účtů. 

---

### <a name="unrequested-but-consented-permissions-will-no-longer-be-added-to-tokens-if-they-would-trigger-conditional-access"></a>Nežadatelové, ale oprávnění, která se jim budou zasílat, se už nebudou přidávat k tokenům, pokud by aktivovaly podmíněný přístup.

**Zadejte:** Plánování změn  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Platformy
 
V současné době jsou aplikacím, které používají [Dynamická oprávnění](../develop/v2-permissions-and-consent.md#requesting-individual-user-consent) , udělena všechna oprávnění, ke kterým mají přístup. To zahrnuje aplikace, které jsou Nevyžadovány, a to i v případě, že budou aktivovat podmíněný přístup. Například to může způsobit, že aplikace požadující jenom `user.read` , která má souhlas pro `files.read` , má být vynucená předat podmíněný přístup přiřazený k tomuto `files.read` oprávnění. 

Azure AD mění způsob, jakým jsou v aplikacích poskytovány nepožadované obory, aby se snížil počet nepotřebných výzev pro podmíněný přístup. Aplikace budou aktivovat podmíněný přístup jenom pro oprávnění, která explicitně vyžádají. Další informace najdete v článku [co je nového v ověřování](../develop/reference-breaking-changes.md#conditional-access-will-only-trigger-for-explicitly-requested-scopes).
 
---
 
### <a name="public-preview---use-a-temporary-access-pass-to-register-passwordless-credentials"></a>Public Preview – k registraci přihlašovacích údajů bez hesla použijte dočasný přístupový průchod

**Zadejte:** Nová funkce  
**Kategorie služby:** VÍCEFAKTOROVÉHO  
**Schopnost produktu:** Zabezpečení identity & ochrana

Dočasný přístupový průchod je časově omezený přístupový kód, který slouží jako silné přihlašovací údaje a umožňuje připojení přihlašovacích údajů a obnovení bez hesla, když uživatel ztratí nebo zapomněl svůj faktor ověřování (například FIDO2 Security Key nebo Microsoft Authenticator) a musí se přihlásit, aby registroval nové silné metody ověřování. [Další informace](../authentication/howto-authentication-temporary-access-pass.md).

---

### <a name="public-preview---keep-me-signed-in-kmsi-in-next-generation-of-user-flows"></a>Public Preview – při nové generaci uživatelských toků si nechte zůstat přihlášeni (políčko zůstat přihlášeni).

**Zadejte:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C

Další generace uživatelských toků B2C nyní podporuje funkce [Keep jsem přihlášená (políčko zůstat přihlášeni)](../../active-directory-b2c/session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) , která zákazníkům umožňuje prodloužení životnosti relace pro uživatele svých webových a nativních aplikací pomocí trvalého souboru cookie.  funkce udržuje aktivní relaci i v případě, že uživatel zavře a znovu otevře prohlížeč a při odhlášení uživatele se odvolá.

---

### <a name="public-preview---external-identities-self-service-sign-up-in-aad-using-msa-accounts"></a>Verze Public Preview – externí identity Self-Service registraci v AAD pomocí účtů MSA

**Zadejte:** Nová funkce  
**Kategorie služby:** B2B  
**Schopnost produktu:** B2B/B2C
 
Externí uživatelé teď můžou pomocí účtů Microsoft přihlašovat se k aplikacím Azure AD First stran a LOB. [Další informace](../external-identities/self-service-sign-up-overview.md).

---

### <a name="public-preview---reset-redemption-status-for-a-guest-user"></a>Public Preview – resetovat stav uplatnění pro uživatele typu Host

**Zadejte:** Nová funkce  
**Kategorie služby:** B2B  
**Schopnost produktu:** B2B/B2C
 
Zákazníci teď můžou znovu pozvat stávající externí uživatele typu Host, aby obnovili stav jejich uplatnění, což umožňuje, aby uživatelský účet hosta zůstal bez ztráty přístupu. [Další informace](../external-identities/reset-redemption-status.md).
 
---

### <a name="public-preview---synchronization-provisioning-apis-now-support-application-permissions"></a>Rozhraní API pro Public Preview/Synchronization (zřizování) teď podporují oprávnění aplikací.

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** Správa životního cyklu identit
 
Zákazníci teď můžou používat Application. ownedby jako oprávnění aplikace pro volání synchronizačních rozhraní API. Poznámka: Tato možnost se podporuje jenom pro zřizování z Azure AD na aplikace třetích stran (například AWS, datové cihly atd.). V současné době není podporována pro zřizování lidských zdrojů (Workday/SuccessFactors) ani pro cloudovou synchronizaci (AD do Azure AD). [Další informace](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta).
 
---

### <a name="general-availability---authentication-policy-administrator-built-in-role"></a>Obecná dostupnost – integrovaná role správce zásad ověřování

**Zadejte:** Nová funkce  
**Kategorie služby:** RBAC  
**Schopnost produktu:** Access Control
 
Uživatelé s touto rolí můžou konfigurovat zásady metod ověřování, nastavení VÍCEFAKTOROVÉHO ověřování na úrovni tenanta a zásady ochrany heslem. Tato role uděluje oprávnění ke správě nastavení ochrany heslem: Konfigurace inteligentního uzamknutí a aktualizace vlastního seznamu zakázaných hesel. [Další informace](../roles/permissions-reference.md#authentication-policy-administrator).

---

### <a name="general-availability---user-collections-on-my-apps-are-available-now"></a>Obecná dostupnost – kolekce uživatelů v mých aplikacích je teď k dispozici!

**Zadejte:** Nová funkce  
**Kategorie služby:** Moje aplikace  
**Schopnost produktu:** Prostředí koncového uživatele
 
Uživatelé teď můžou vytvářet vlastní seskupení aplikací ve Spouštěči aplikací Moje aplikace. Mohou také změnit pořadí a skrytí kolekcí, které jsou s nimi sdíleny, správcem. [Další informace](../user-help/my-apps-portal-user-collections.md).

---

### <a name="general-availability---autofill-in-authenticator"></a>Obecná dostupnost – automatické vyplnění ověřovacích dat

**Zadejte:** Nová funkce  
**Kategorie služby:** Aplikace Microsoft Authenticator  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
Microsoft Authenticator poskytuje službu Multi-Factor Authentication (MFA) a možnosti správy účtů a teď také zadá automatické vyplňování hesel na webech a aplikacích, které uživatelé navštěvují na mobilních zařízeních (iOS a Android). 

Chcete-li použít automatické vyplňování ověřovacích dat, uživatelé musí do ověřovatele přidat své osobní účet Microsoft a použít je k synchronizaci hesel. Pracovní nebo školní účty nejde v tuto chvíli použít k synchronizaci hesel. [Další informace](../user-help/user-help-auth-app-faq.md#autofill-for-it-admins).

---

### <a name="general-availability---invite-internal-users-to-b2b-collaboration"></a>Obecná dostupnost – pozvání interních uživatelů ke spolupráci B2B

**Zadejte:** Nová funkce  
**Kategorie služby:** B2B  
**Schopnost produktu:** B2B/B2C
 
Zákazníci teď můžou pozvat interní hosty, aby použili spolupráci B2B místo odeslání pozvánky stávajícímu internímu účtu. To zákazníkům umožňuje zachovat ID objektu, hlavní název uživatele (UPN), členství ve skupinách a přiřazení aplikací. [Další informace](../external-identities/invite-internal-users.md).

---

### <a name="general-availability---domain-name-administrator-built-in-role"></a>Obecná dostupnost – předdefinovaná role Správce názvů domén

**Zadejte:** Nová funkce  
**Kategorie služby:** RBAC  
**Schopnost produktu:** Access Control
 
Uživatelé s touto rolí můžou spravovat názvy domén (čtení, přidávání, ověřování, aktualizace a odstraňování). Můžou také číst informace o uživatelích, skupinách a aplikacích, protože tyto objekty mají závislosti na doméně. 

Pro místní prostředí můžou uživatelé s touto rolí konfigurovat názvy domén pro federaci, aby se přidružení uživatelé vždycky místně ověřili místně. Tito uživatelé se pak mohou přihlašovat ke službám založeným na službě Azure AD s místními hesly prostřednictvím jednotného přihlašování. Nastavení federace je třeba synchronizovat prostřednictvím Azure AD Connect, takže uživatelé mají také oprávnění ke správě Azure AD Connect. [Další informace](../roles/permissions-reference.md#domain-name-administrator).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---february-2021"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD – únor 2021

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
V únoru 2021 jsme do Galerie aplikací přidali následující 37 nových aplikací s podporou federace:

[Rozšíření pro kurýrní](https://loopworks.com/loop-flow-messenger/) [službu, Silverfort adaptér Azure AD](http://www.silverfort.com/), [souhře Learning](https://skilledtrades.interplaylearning.com/#login), [Nura prostor](https://dashboard.nuraspace.com/login), [Yooz EU](https://eu1.getyooz.com/?kc_idp_hint=microsoft), [UXPressia](https://uxpressia.com/users/sign-in), [introDus pre-and-Desking](http://app.introdus.dk/login), [Happybot, LeaksID](https://login.microsoftonline.com/organizations/oauth2/v2.0/authorize?client_id=34353e1e-dfe5-4d2f-bb09-2a5e376270c8&response_type=code&redirect_uri=https://api.happyteams.io/microsoft/integrate&response_mode=query&scope=offline_access%20User.Read%20User.Read.All), [SHIFTWIZARD](http://www.shiftwizard.com/), [PingFlow SSO](https://app.pingview.io/), [](https://admin.swiftlane.com/login) [Swiftlane, Quasydoc](https://app.leaksid.com/) [SSO](https://www.quasydoc.eu/login), Fenwick [Gold Account](https://businesscentral.dynamics.com/), [SeamlessDesk](https://www.seamlessdesk.com/login), [Learnsoft LMS & organizaci TMS](http://www.learnsoft.com/), [P-th +](https://p-th.jp/), [MyViewBoard](https://api.myviewboard.com/auth/microsoft/), [Tartabit IoT most](https://bridge-us.tartabit.com/), [AKASHI](../saas-apps/akashi-tutorial.md), [rewatch](../saas-apps/rewatch-tutorial.md), [Zuddl](../saas-apps/zuddl-tutorial.md), Parkaloter [Parking pro parkování](../saas-apps/parkalot-car-park-management-tutorial.md), [HSB ThoughtSpot](../saas-apps/hsb-thoughtspot-tutorial.md), [IBMid](../saas-apps/ibmid-tutorial.md), [SharingCloud](../saas-apps/sharingcloud-tutorial.md), [PoolParty sémantická sada](../saas-apps/poolparty-semantic-suite-tutorial.md), [GlobeSmart,](../saas-apps/globesmart-tutorial.md) [Samsung KNOX a Business Services](../saas-apps/samsung-knox-and-business-services-tutorial.md) [,](../saas-apps/sigma-computing-tutorial.md) [Penji](../saas-apps/penji-tutorial.md) [, Kendis](../saas-apps/olfeo-saas-tutorial.md) [– škálování pro agilní platformu](../saas-apps/kendis-scaling-agile-platform-tutorial.md) [, Maptician](../saas-apps/maptician-tutorial.md) [SAML](../saas-apps/klaxoon-saml-tutorial.md), [Olfeo](../saas-apps/enablon-tutorial.md) [](../saas-apps/cloudknox-permissions-management-platform-tutorial.md)

Dokumentaci pro všechny aplikace můžete také najít tady: https://aka.ms/AppsTutorial

Pokud chcete zobrazit seznam aplikací v galerii aplikací Azure AD, přečtěte si podrobnosti tady: https://aka.ms/AzureADAppRequest

--- 

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2021"></a>Nové zřizovací konektory v galerii aplikací Azure AD – únor 2021

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** integrace třetích stran
 

Nyní můžete automatizovat vytváření, aktualizaci a odstraňování uživatelských účtů pro tyto nově integrované aplikace:

- [Atea](../saas-apps/atea-provisioning-tutorial.md)
- [Getabstract](../saas-apps/getabstract-provisioning-tutorial.md)
- [HelloID](../saas-apps/helloid-provisioning-tutorial.md)
- [Hoxhunt](../saas-apps/hoxhunt-provisioning-tutorial.md)
- [Iris Intranet](../saas-apps/iris-intranet-provisioning-tutorial.md)
- [Preciate](../saas-apps/preciate-provisioning-tutorial.md)

Další informace najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="general-availability---10-azure-active-directory-roles-now-renamed"></a>Obecná dostupnost – 10 Azure Active Directory rolí se teď přejmenovaly.

**Zadejte:** Změněná funkce  
**Kategorie služby:** RBAC  
**Schopnost produktu:** Access Control
 
10 integrovaných rolí Azure AD se přejmenovaly tak, aby byly zarovnané v [centru pro správu Microsoft 365](/microsoft-365/admin/microsoft-365-admin-center-preview), na [portálu Azure AD](https://portal.azure.com/)a na [Microsoft Graph](https://developer.microsoft.com/graph/). Další informace o nových rolích najdete [v tématu oprávnění role správce v Azure Active Directory](../roles/permissions-reference.md#all-roles).

![Tabulka znázorňující názvy rolí v MS Graph API a Azure Portal a navrhovaný konečný název napříč rozhraním API, Azure Portal a Mac.](media/whats-new/roles-table-rbac.png)

---

### <a name="new-company-branding-in-mfasspr-combined-registration"></a>Nové logo společnosti v kombinované registraci MFA/SSPR

**Zadejte:** Změněná funkce  
**Kategorie služby:** Uživatelské prostředí a Správa  
**Schopnost produktu:** Prostředí koncového uživatele
 
V minulosti se loga společnosti nepoužila na Azure Active Directory přihlašovací stránky. Branding společnosti se teď nachází v levém horním rohu kombinované registrace MFA/SSPR. Branding společnosti je také součástí mého Sign-Ins a na stránce informace o zabezpečení. [Další informace](../fundamentals/customize-branding.md).

---

### <a name="general-availability---second-level-manager-can-be-set-as-alternate-approver"></a>Obecná dostupnost – správce druhé úrovně se dá nastavit jako alternativní schvalovatel.

**Zadejte:** Změněná funkce  
**Kategorie služby:** Správa přístupu uživatelů  
**Schopnost produktu:** Správa nároků
 
Po výběru možnosti schvalovatelé je teď v nástroji Správa nároků dostupná možnost Další. Pokud pro prvního schvalovatele vyberete "manažer jako schvalovatele", budete mít další možnost "druhý manažer na úrovni jako alternativní schvalovatel", která se dá vybrat v poli alternativní schvalovatel. Vyberete-li tuto možnost, je nutné přidat záložního schvalovatele, kterému bude žádost předána, v případě, že systém nemůže najít správce druhé úrovně. [Další informace](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers).
 
---

### <a name="authentication-methods-activity-dashboard"></a>Řídicí panel aktivity metod ověřování

**Zadejte:** Změněná funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav
 

Řídicí panel aktivita aktualizovat metody ověřování poskytuje správcům přehled o registraci metod ověřování a aktivitě využití ve svém tenantovi. Tato sestava shrnuje počet uživatelů registrovaných pro každou metodu a také metody používané během přihlašování a resetování hesla. [Další informace](../authentication/howto-authentication-methods-activity.md).
 
---

### <a name="refresh-and-session-token-lifetimes-configurability-in-configurable-token-lifetime-ctl-are-retired"></a>Je vyřazena možnost aktualizace a životnosti tokenů relace v konfigurovatelném života tokenu (CTL).

**Zadejte:** Zastaralé  
**Kategorie služby:** Jiná  
**Schopnost produktu:** Ověřování uživatelů
 
V seznamu CTL jsou vyřazeny aktualizace a životnosti tokenů relace. Azure Active Directory už v existujících zásadách nedodržuje aktualizace a konfiguraci tokenu relace. [Další informace](../develop/active-directory-configurable-token-lifetimes.md#token-lifetime-policies-for-refresh-tokens-and-session-tokens).
 
---
 
## <a name="january-2021"></a>Leden 2021

### <a name="secret-token-will-be-a-mandatory-field-when-configuring-provisioning"></a>Při konfiguraci zřizování bude token tajného klíče povinným polem.

**Zadejte:** Plánování změn  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** Správa životního cyklu identit

V minulosti bylo pole tajného tokenu při nastavování zřizování u vlastní/BYOAé aplikace zachované prázdné. Tato funkce byla určena pouze pro testování. Aktualizujeme uživatelské rozhraní, aby bylo pole povinné. 

Zákazníci můžou tento požadavek pro účely testování obejít pomocí příznaku funkce v adrese URL prohlížeče. [Další informace](../app-provisioning/use-scim-to-provision-users-and-groups.md#authorization-to-provisioning-connectors-in-the-application-gallery).
 
---

### <a name="public-preview---customize-and-configure-android-shared-devices-for-frontline-workers-at-scale"></a>Public Preview – přizpůsobení a konfigurace sdílených zařízení s Androidem pro pracovní procesy prvotní ve velkém měřítku

**Zadejte:** Nová funkce  
**Kategorie služby:** Registrace a Správa zařízení  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
Týmy služby Azure AD a Microsoft Endpoint Manager se kombinují, aby bylo možné přizpůsobit, škálovat a zabezpečit vaše pracovní zařízení prvotní.

Následující možnosti verze Preview vám umožní:
- Zřizování sdílených zařízení s Androidem ve velkém měřítku pomocí Microsoft Endpoint Manageru
- Zabezpečení přístupu pro pracovníky služby Shift pomocí podmíněného přístupu na základě zařízení
- Přizpůsobení přihlašovacích prostředí pro pracovníky posunutí pomocí spravované domovské obrazovky

Další informace najdete [v tématu přizpůsobení a konfigurace sdílených zařízení pro pracovní procesy prvotní ve velkém měřítku](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-and-configure-shared-devices-for-firstline-workers-at/ba-p/1751708).

---

### <a name="public-preview---provisioning-logs-can-now-be-downloaded-as-a-csv-or-json"></a>Veřejná verze Preview – protokoly zřizování se teď dají stáhnout jako CSV nebo JSON.

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** Správa životního cyklu identit

Zákazníci si můžou protokoly zřizování stáhnout jako soubor CSV nebo JSON prostřednictvím uživatelského rozhraní a přes rozhraní Graph API. Další informace najdete [v tématu zřizování sestav na portálu Azure Active Directory](../reports-monitoring/concept-provisioning-logs.md).

---

### <a name="public-preview---assign-cloud-groups-to-azure-ad-custom-roles-and-admin-unit-scoped-roles"></a>Public Preview – přiřazení cloudových skupin k vlastním rolím Azure AD a rolím oboru správců vymezeným pro jednotky

**Zadejte:** Nová funkce  
**Kategorie služby:** RBAC  
**Schopnost produktu:** Access Control
 
Zákazníci můžou přiřadit cloudovou skupinu k vlastním rolím Azure AD nebo k roli správce s vymezenou jednotkou. Pokud se chcete dozvědět, jak tuto funkci používat, přečtěte si téma [použití skupin cloudu ke správě přiřazení rolí v Azure Active Directory](../roles/groups-concept.md).

---

### <a name="general-availability---azure-ad-connect-cloud-sync-previously-known-as-cloud-provisioning"></a>Obecná dostupnost – Azure AD Connect cloudová synchronizace (dříve označovaná jako zřizování cloudu)

**Zadejte:** Nová funkce  
**Kategorie služby:** Synchronizace Azure AD Connect cloudu  
**Schopnost produktu:** Správa životního cyklu identit
 
Azure AD Connect synchronizace cloudu je teď všeobecně dostupná pro všechny zákazníky.

Azure AD Connect Cloud přesouvá velkou zvedací logiku transformace do cloudu, což snižuje vaše místní nároky. Pro zajištění vyšší dostupnosti synchronizace je navíc k dispozici více nasazení agentů s vysokou váhou. [Další informace](https://aka.ms/cloudsyncGA).
 
---
### <a name="general-availability---attack-simulation-administrator-and-attack-payload-author-built-in-roles"></a>Obecná dostupnost – správce simulace útoků a šifrování datové části útoků – vytváření předdefinovaných rolí

**Zadejte:** Nová funkce  
**Kategorie služby:** RBAC  
**Schopnost produktu:** Access Control
 
K přiřazení uživatelů jsou k dispozici dvě nové role v Role-Based Access Control, správce simulace útoku a autor datové části útoku. 

Uživatelé s rolí [správce simulace útoku](../roles/permissions-reference.md#attack-simulation-administrator) mají přístup pro všechny simulace v tenantovi a můžou:
- vytváření a správa všech aspektů vytvoření simulace útoků
- spuštění nebo plánování simulace
-  Zkontrolujte výsledky simulace. 

Uživatelé v roli [autora datové části útoku](../roles/permissions-reference.md#attack-payload-author) můžou vytvořit datovou část útoku, ale nebudou je ve skutečnosti spouštět nebo naplánovali. Datová část útoku je pak k dispozici všem správcům v tenantovi, kteří je můžou použít k vytvoření simulace.

---

### <a name="general-availability---usage-summary-reports-reader-built-in-role"></a>Obecná dostupnost – Souhrnná sestava pro sestavy Souhrn využití – předdefinovaná role

**Zadejte:** Nová funkce  
**Kategorie služby:** RBAC  
**Schopnost produktu:** Access Control
 
Uživatelé s rolí čtenáře sestav Souhrn využití mají přístup ke agregovaným datům na úrovni tenanta a k souvisejícím přehledům v centru pro správu Microsoft 365 pro hodnocení využití a produktivity. Nemůžou ale získat přístup k podrobnostem na úrovni uživatele nebo přehledům. 

V centru pro správu Microsoft 365 pro dvě sestavy rozlišujeme mezi agregovanými daty na úrovni tenanta a podrobnostmi na úrovni uživatele. Tato role přidává další vrstvu ochrany jednotlivých uživatelsky identifikovatelných dat. [Další informace](../roles/permissions-reference.md#usage-summary-reports-reader).

---

### <a name="general-availability---require-app-protection-policy-grant-in-azure-ad-conditional-access"></a>Obecná dostupnost – vyžaduje zásady ochrany aplikací udělit v podmíněném přístupu Azure AD.

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
Udělení podmíněného přístupu Azure AD pro "vyžadovat zásadu ochrany aplikací" je teď GA. 

Tato zásada nabízí následující možnosti:
- Povoluje přístup jenom při použití mobilní aplikace, která podporuje Intune App Protection.
- Povolí přístup jenom v případě, že má uživatel v mobilní aplikaci doručenou zásadu ochrany aplikací Intune.

Další informace o tom, jak nastavit zásady podmíněného přístupu pro ochranu aplikací, [najdete tady](../conditional-access/app-protection-based-conditional-access.md).
 
---

### <a name="general-availability---email-one-time-passcode"></a>Obecná dostupnost – E-mail One-Time heslo

**Zadejte:** Nová funkce  
**Kategorie služby:** B2B  
**Schopnost produktu:** B2B/B2C
 
E-mailová hesla umožňují organizacím po celém světě spolupracovat s kýmkoli odesláním odkazu nebo pozvánky prostřednictvím e-mailu. Pozvaní uživatelé můžou ověřit svoji identitu s jednorázovým heslem odesílaným do jejich e-mailu, aby měli přístup k prostředkům jejich partnera. [Další informace](../external-identities/one-time-passcode.md). 
 
---

 ### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2021"></a>Nové zřizovací konektory v galerii aplikací Azure AD – leden 2021

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** integrace třetích stran
 
Nyní můžete automatizovat vytváření, aktualizaci a odstraňování uživatelských účtů pro tyto nově integrované aplikace:
- [Fortes Change Cloud](../saas-apps/fortes-change-cloud-provisioning-tutorial.md)
- [Gtmhub](../saas-apps/gtmhub-provisioning-tutorial.md)
- [monday.com](../saas-apps/mondaycom-provisioning-tutorial.md)
- [Splashtop](../saas-apps/splashtop-provisioning-tutorial.md)
- [Templafy OpenID Connect](../saas-apps/templafy-openid-connect-provisioning-tutorial.md)
- [WEDO](../saas-apps/wedo-provisioning-tutorial.md)

Další informace najdete v tématu [co je automatizované zřizování uživatelů aplikací SaaS ve službě Azure AD?](../app-provisioning/user-provisioning.md)

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---january-2021"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – leden 2021

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran

V lednu 2021 jsme do Galerie aplikací přidali následující 29 nových aplikací s podporou federace:

[mySCView](https://dev.myscview.com/), [talentech](https://talentech.com/contact/), [Bipsync](https://www.bipsync.com/), [OroTimesheet](https://app.orotimesheet.com/login.php), [Mio](https://app.m.io/auth/install/microsoft?scopetype=hub), [Sovelto Easy](https://login.soveltoeasy.fi/), [Supportbench](https://account.supportbench.net/agent/login/),[vytváření Bienvenue](https://formation.bienvenue.pro/login), [jednotné přihlašování Aida pro zdravotní péči](https://aidaforparents.com/login/organizations), [mezinárodní produkty pro SOS pomoc](../saas-apps/international-sos-assistance-products-tutorial.md), [NAVEX One](../saas-apps/navex-one-tutorial.md), [LabLog](../saas-apps/lablog-tutorial.md), [Oktopost SAML](../saas-apps/oktopost-saml-tutorial.md), [EPHOTO přehradit](../saas-apps/ephoto-dam-tutorial.md), vyrozuměl, Syndio, [Yello Enterprise](../saas-apps/yello-enterprise-tutorial.md), [timeclock 365 SAML](../saas-apps/timeclock-365-saml-tutorial.md), [NALCO E-data](https://www.ecolab.com/), [doplňování](https://app.vacancy-filler.co.uk/VFMVC/Account/Login) [,](../saas-apps/proware-tutorial.md) [Synerise, Imperva](../saas-apps/syndio-tutorial.md)pro [růst](../saas-apps/synerise-ai-growth-ecosystem-tutorial.md) [](../saas-apps/notion-tutorial.md) [uživatelského prostředí](../saas-apps/aruba-user-experience-insight-tutorial.md), [](../saas-apps/splan-visitor-tutorial.md) [Illusive](../saas-apps/imperva-data-security-tutorial.md) [SSO](../saas-apps/contentsquare-sso-tutorial.md), [hraniční 81](../saas-apps/perimeter-81-tutorial.md) [,](../saas-apps/illusive-networks-tutorial.md) [Splan Suite Enterprise Edition](../saas-apps/burp-suite-enterprise-edition-tutorial.md)

Dokumentaci pro všechny aplikace můžete také najít tady. https://aka.ms/AppsTutorial

Pokud chcete zobrazit seznam aplikací v galerii aplikací Azure AD, přečtěte si podrobnosti tady. https://aka.ms/AzureADAppRequest 

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>Veřejná verze Preview – druhý správce úrovně můžete nastavit jako alternativní schvalovatel.

**Zadejte:** Změněná funkce  
**Kategorie služby:** Správa přístupu uživatelů  
**Schopnost produktu:** Správa nároků
 
Po výběru možnosti schvalovatelé je teď v nástroji Správa nároků dostupná možnost Další. Pokud pro prvního schvalovatele vyberete "manažer jako schvalovatele", budete mít další možnost "druhý manažer na úrovni jako alternativní schvalovatel", která se dá vybrat v poli alternativní schvalovatel. Vyberete-li tuto možnost, je nutné přidat záložního schvalovatele, kterému bude žádost předána, v případě, že systém nemůže najít správce druhé úrovně. [Další informace](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers)
 
---

### <a name="general-availability---navigate-to-teams-directly-from-my-access-portal"></a>Obecná dostupnost – přechod na týmy přímo z portálu pro přístup

**Zadejte:** Změněná funkce  
**Kategorie služby:** Správa přístupu uživatelů  
**Schopnost produktu:** Správa nároků
 
Týmy teď můžete spouštět přímo z portálu pro přístup. 

Provedete to tak, že se přihlásíte k přístupu ( https://myaccess.microsoft.com/) , přejdete na přístupové balíčky a pak přejdete na kartu aktivní) a zobrazíte všechny balíčky pro přístup, ke kterým už máte přístup. Když rozbalíte vybraný balíček pro přístup a najedete na týmy, můžete ho spustit kliknutím na tlačítko otevřít. [Další informace](../governance/entitlement-management-request-access.md).
 
---

### <a name="improved-logging--end-user-prompts-for-risky-guest-users"></a>Vylepšené protokolování & End-User výzvy pro rizikové uživatele typu Host

**Zadejte:** Změněná funkce  
**Kategorie služby:** Ochrana identity  
**Schopnost produktu:** Zabezpečení identity & ochrana
 

Protokolování a End-User výzvy pro rizikové uživatele typu Host byly aktualizovány. Další informace najdete v [ochraně identit a uživatelů B2B](../identity-protection/concept-identity-protection-b2b.md).
 
---
 
## <a name="december-2020"></a>Prosinec 2020

### <a name="public-preview---azure-ad-b2c-phone-sign-up-and-sign-in-using-built-in-policy"></a>Verze Public Preview – registrace a přihlašování Azure AD B2Cho telefonu pomocí integrovaných zásad

**Zadejte:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C
 
B2C a přihlašování telefonem pomocí integrovaných zásad umožňuje správcům IT a vývojářům organizací povolit, aby se koncovým uživatelům přihlásili a mohli se zaregistrovat pomocí telefonního čísla v uživatelských tocích. Pokud se chcete dozvědět víc, přečtěte si téma [Nastavení registrace telefonu a přihlášení pro toky uživatelů (Preview)](../../active-directory-b2c/phone-authentication-user-flows.md) .

---

### <a name="general-availability---security-defaults-now-enabled-for-all-new-tenants-by-default"></a>Obecná dostupnost – výchozí hodnoty zabezpečení jsou teď povolené pro všechny nové klienty ve výchozím nastavení.

**Zadejte:** Nová funkce  
**Kategorie služby:** Jiná  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
Aby se chránily uživatelské účty, budou mít všichni noví klienti vytvoření 12. listopadu 2020 a budou mít povolené výchozí hodnoty zabezpečení. Výchozí nastavení zabezpečení vynutilo více zásad, včetně:
- Vyžaduje, aby všichni uživatelé a správci se zaregistrovali pro MFA pomocí aplikace Microsoft Authenticator.
- Vyžaduje, aby při každém přihlašování používaly zásadní role správce k ověřování MFA. Všichni ostatní uživatelé budou v případě potřeby vyzváni k ověřování MFA. 
- Starší verze ověřování bude zablokována u tenanta. 

Další informace najdete v článku [co jsou výchozí hodnoty zabezpečení?](../fundamentals/concept-fundamentals-security-defaults.md)

---

### <a name="general-availability---support-for-groups-with-up-to-250k-members-in-aadconnect"></a>Obecná dostupnost – podpora pro skupiny s až 250 tisíc členy v AADConnect

**Zadejte:** Změněná funkce  
**Kategorie služby:** Služba AD Connect  
**Schopnost produktu:** Správa životního cyklu identit
 
Společnost Microsoft nasadila nový koncový bod (rozhraní API) pro Azure AD Connect, který vylepšuje výkon operací synchronizační služby Azure Active Directory. Když použijete nový [koncový bod v2](../hybrid/how-to-connect-sync-endpoint-api-v2.md), budete mít při exportu a importu do Azure AD patrné zvýšení výkonu. Tento nový koncový bod podporuje následující scénáře:

- Synchronizace skupin s až 250 tisíc členy
- Zisky z výkonu při exportu a importu do Azure AD

---

### <a name="general-availability---entitlement-management-available-for-tenants-in-azure-china-cloud"></a>Obecná dostupnost – Správa opravňujících k dispozici pro klienty v Azure Čína cloudu

**Zadejte:** Nová funkce  
**Kategorie služby:** Správa přístupu uživatelů  
**Schopnost produktu:** Správa nároků
 

Možnosti správy oprávnění jsou nyní k dispozici pro všechny klienty v cloudu Azure Čína. Informace najdete na webu [dokumentace zásad správného řízení identit](https://docs.azure.cn/zh-cn/active-directory/governance/) .

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---december-2020"></a>Nové zřizovací konektory v galerii aplikací Azure AD – prosinec 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** integrace třetích stran

Nyní můžete automatizovat vytváření, aktualizaci a odstraňování uživatelských účtů pro tyto nově integrované aplikace:

- [Bizagi Studio for Digital Process Automation](../saas-apps/bizagi-studio-for-digital-process-automation-provisioning-tutorial.md)
- [CybSafe](../saas-apps/cybsafe-provisioning-tutorial.md)
- [GroupTalk](../saas-apps/grouptalk-provisioning-tutorial.md)
- [PaperCut Cloud Print Management](../saas-apps/papercut-cloud-print-management-provisioning-tutorial.md)
- [Parsable](../saas-apps/parsable-provisioning-tutorial.md)
- [Shopify Plus](../saas-apps/shopify-plus-provisioning-tutorial.md)

Další informace o tom, jak lépe zabezpečit vaši organizaci pomocí automatizovaného zřizování uživatelských účtů, najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](../app-provisioning/user-provisioning.md).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---december-2020"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD – prosinec 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
V prosinci 2020 jsme do Galerie aplikací přidali následující 18 nových aplikací s podporou federace:

[AwareGo](../saas-apps/awarego-tutorial.md), [HowNow SSO](https://gethownow.com/), [ZyLAB One právní blokování](https://www.zylab.com/en/product/legal-hold), [Průvodce](http://www.guider-ai.com/), [Softcrisis](https://www.softcrisis.se/sv/), [Pims 365](http://www.omega365.com/pims), [InformaCast](../saas-apps/informacast-tutorial.md), [RetrieverMediaDatabase](../saas-apps/retrievermediadatabase-tutorial.md), [Vonage](../saas-apps/vonage-tutorial.md), [počet já v provozním řídicím panelu](../saas-apps/count-me-in-operations-dashboard-tutorial.md), [ProProfs znalostní báze](../saas-apps/proprofs-knowledge-base-tutorial.md), [RightCrowd](../saas-apps/rightcrowd-workforce-management-tutorial.md)pracovní síly [, JLL TRIRIGA,](../saas-apps/jll-tririga-tutorial.md) [Shutterstock](../saas-apps/shutterstock-tutorial.md), [FortiWeb webové aplikace firewall](../saas-apps/linkedin-talent-solutions-tutorial.md), [LinkedIn talentů Solutions](../saas-apps/linkedin-talent-solutions-tutorial.md), [Equinix Federation App](../saas-apps/equinix-federation-app-tutorial.md) [, KFAdvance](../saas-apps/kfadvance-tutorial.md)

Dokumentaci pro všechny aplikace můžete také najít tady. https://aka.ms/AppsTutorial

Pokud chcete zobrazit seznam aplikací v galerii aplikací Azure AD, přečtěte si podrobnosti tady. https://aka.ms/AzureADAppRequest

---

### <a name="navigate-to-teams-directly-from-my-access-portal"></a>Přechod na týmy přímo z portálu pro přístup

**Zadejte:** Změněná funkce  
**Kategorie služby:** **Schopnost produktu** správy přístupu uživatele: Správa nároků

Týmy teď můžete spouštět přímo z portálu pro přístup. Uděláte to tak, že se přihlásíte k [přístupu](https://myaccess.microsoft.com/), přejdete na **přístup k balíčkům** a pak přejdete na **aktivní** kartu a zobrazíte všechny balíčky pro přístup, ke kterým už máte přístup. Když rozbalíte balíček pro přístup a najedete na týmy, můžete ho spustit kliknutím na tlačítko **otevřít** . 

Pokud chcete získat další informace o používání portálu pro přístup, přejděte na [vyžádání přístupu k balíčku přístupu ve správě nároků služby Azure AD](../governance/entitlement-management-request-access.md#sign-in-to-the-my-access-portal).

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>Veřejná verze Preview – druhý správce úrovně můžete nastavit jako alternativní schvalovatel.

**Zadejte:** Změněná funkce  
**Kategorie služby:** Správa přístupu uživatelů  
**Schopnost produktu:** Správa nároků

Ve správě nároků je teď k dispozici další možnost v procesu schvalování. Pokud jako schvalovatele vyberete pro prvního schvalovatele možnost manažer, budete mít k dispozici další možnost jako alternativního schvalovatele, kterou si můžete vybrat v poli alternativní schvalovatel. Když vyberete tuto možnost, musíte přidat záložního schvalovatele, aby se žádost přeposlala v případě, že systém nemůže najít správce druhé úrovně.

Další informace najdete [v části Změna nastavení schválení pro balíček přístupu v Azure AD – Správa nároků](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers).

--- 

## <a name="november-2020"></a>Listopad 2020

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation"></a>Azure Active Directory TLS 1,0, TLS 1,1 a 3DES (zastaralý)

**Zadejte:** Plánování změn  
**Kategorie služby:** Všechny aplikace Azure AD  
**Schopnost produktu:** Zvyšování

Azure Active Directory vyřadí následující protokoly v Azure Active Directory celosvětových oblastech do 30. června 2021:

- TLS 1.0
- TLS 1.1
- šifrovací sada 3DES (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Ovlivněná prostředí jsou:
- Komerční cloud Azure
- Office 365 RSZ a TT

Související oznámení: kombinace všech klientských a serverových a prohlížečových serverů by měly používat protokol TLS 1,2 a moderní šifrovací sady k údržbě zabezpečeného připojení k Azure Active Directory pro Azure, Office 365 a služby Microsoft 365. Tato změna se týká [Azure Active Directory TLS 1,0 & 1,1 a vyřazení šifrovací sady 3DES v US gov cloudu](whats-new.md#azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---november-2020"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – listopad 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran

V listopadu 2020 jsme do Galerie aplikací přidali následující 52 nových aplikací s podporou federace:

[Cestovní & Správa výdajů](https://app.expenseonce.com/Account/Login), [Tribeloo](../saas-apps/tribeloo-tutorial.md), [Itslearning pro výběr souborů](https://pmteam.itslearning.com/), [krize – řízení](../saas-apps/crises-control-tutorial.md) [CourtAlert](https://www.courtalert.com/), [StealthMail](https://stealthmail.com/), [Edmentum – studijní ostrov](https://app.studyisland.com/cfw/login/), [Virtual rizikový Manager](../saas-apps/virtual-risk-manager-tutorial.md), [TIMU](../saas-apps/timu-tutorial.md), [Looker Analytics Platform](../saas-apps/looker-analytics-platform-tutorial.md), [Talview-nábor](https://recruit.talview.com/login), překladatel v reálném čase, [Klaxoon](https://access.klaxoon.com/login), [podbean](../saas-apps/podbean-tutorial.md), [zcal](https://zcal.co/signup), [expensemanager](https://api.expense-manager.com/), [Netsparker Enterprise](../saas-apps/netsparker-enterprise-tutorial.md), [EN-Trak Experience Platform](https://portal.en-trak.app/) [,](../saas-apps/lucid-tutorial.md) [Appian](../saas-apps/appian-tutorial.md), Panorays [, Builterra,](https://portal.builterra.com/)Eva se [změnami](https://my.evacheckin.com/organization), [HowNow](../saas-apps/hownow-webapp-sso-tutorial.md), [WebApp](../saas-apps/sailpoint-identitynow-tutorial.md) [, Coupa](https://portal.brightbooking.eu/) [, čtyřkrát](../saas-apps/coupa-risk-assess-tutorial.md)[střed](../saas-apps/resource-central-tutorial.md), [UiPathStudioO365App](https://www.uipath.com/product/platform), [Jedox](../saas-apps/jedox-tutorial.md), [Cequence aplikace zabezpečení](../saas-apps/cequence-application-security-tutorial.md), [PerimeterX](../saas-apps/perimeterx-tutorial.md), [TrendMiner](../saas-apps/trendminer-tutorial.md), [Lexion](../saas-apps/lexion-tutorial.md), [WorkWare](../saas-apps/workware-tutorial.md), [ProdPad](../saas-apps/prodpad-tutorial.md), [AWS ClientVPN](../saas-apps/aws-clientvpn-tutorial.md), [AppSec Flow SSO](../saas-apps/appsec-flow-sso-tutorial.md), [Luum](../saas-apps/luum-tutorial.md), [dopravné opatření](https://www.gpcsl.com/freight.html), terraformu [Cloud](../saas-apps/terraform-cloud-tutorial.md), RemotePC, Prolorus [,](https://www.teamgage.com/Account/ExternalLoginAzure) [digitálního podepisování](https://login.playsignage.com/login) [, Hirebridge,](../saas-apps/remotepc-tutorial.md) [Teamgage](../saas-apps/prolorus-tutorial.md), [Roadmunk ATS](../saas-apps/hirebridge-ats-tutorial.md) [, Procaire](../saas-apps/nature-research-tutorial.md), [eDriving](../saas-apps/roadmunk-tutorial.md), [slunce software Relations CRM](https://cloud.relations-crm.com/), [Gradle](../saas-apps/procaire-tutorial.md), [instruktor®: Business](https://www.edriving.com/), [Enterprise](https://gradle.com/) [](../saas-apps/panorays-tutorial.md)

Dokumentaci pro všechny aplikace můžete také najít tady. https://aka.ms/AppsTutorial

Pokud chcete zobrazit seznam aplikací v galerii aplikací Azure AD, přečtěte si podrobnosti tady. https://aka.ms/AzureADAppRequest

---

### <a name="public-preview---custom-roles-for-enterprise-apps"></a>Veřejná verze Preview – vlastní role pro podnikové aplikace

**Zadejte:** Nová funkce  
**Kategorie služby:** RBAC  
**Schopnost produktu:** Access Control
 
 [Vlastní role RBAC pro delegovanou správu podnikových aplikací](../roles/custom-available-permissions.md) je teď ve verzi Public Preview. Tato nová oprávnění se vytvářejí na vlastní role pro správu registrace aplikací, což umožňuje detailní kontrolu nad tím, jaký má váš správce k přístupu. V průběhu času budou vydána další oprávnění pro delegování správy služby Azure AD.

Některé běžné scénáře delegování:
- přiřazení uživatelů a skupin, které mají přístup k aplikacím jednotného přihlašování založené na SAML
- vytváření aplikací Galerie Azure AD
- aktualizace a čtení základních konfigurací SAML pro aplikace jednotného přihlašování založené na SAML
- Správa podpisových certifikátů pro aplikace jednotného přihlašování založené na SAML
- aktualizace e-mailových adres oznámení o přihlášení k vypršení platnosti pro aplikace jednotného přihlašování založené na SAML
- aktualizace signatury tokenu SAML a algoritmu přihlášení pro aplikace jednotného přihlašování založené na SAML
- vytváření, odstraňování a aktualizace atributů a deklarací uživatelů pro aplikace jednotného přihlašování založené na SAML
- možnost zapnout, vypnout a restartovat úlohy zřizování
- aktualizace mapování atributů
- možnost číst nastavení zřizování přidružená k objektu
- možnost číst nastavení zřizování přidružená k vašemu objektu služby
- možnost autorizovat přístup k aplikaci pro zřizování

---

### <a name="public-preview---azure-ad-application-proxy-natively-supports-single-sign-on-access-to-applications-that-use-headers-for-authentication"></a>Verze Public Preview – Azure Proxy aplikací služby AD nativně podporuje přístup s jednotným přihlašováním k aplikacím, které používají hlavičky pro ověřování.

**Zadejte:** Nová funkce  
**Kategorie služby:** Proxy aplikace  
**Schopnost produktu:** Access Control
 
Proxy aplikace Azure Active Directory (Azure AD) nativně podporují přístup k jednotnému přihlašování k aplikacím, které používají hlavičky pro ověřování. V Azure AD můžete nakonfigurovat hodnoty hlaviček vyžadované vaší aplikací. Hodnoty hlaviček budou do aplikace odesílány prostřednictvím proxy aplikace. Další informace najdete v tématu [jednotné přihlašování založené na hlavičkách pro místní aplikace pomocí proxy serveru aplikace Azure AD](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md) .
 
---

### <a name="general-availability---azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy"></a>Obecná dostupnost – registrace Azure AD B2Cho telefonu a přihlášení pomocí vlastních zásad

**Zadejte:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C

Když se telefonní číslo a přihlášení k telefonnímu číslu, vývojáři a podniky můžou svým zákazníkům dovolit zaregistrovat se a přihlásit se pomocí jednorázového hesla, které se pošle na telefonní číslo uživatele přes SMS. Tato funkce také umožní zákazníkovi změnit své telefonní číslo, pokud ztratí přístup k telefonu. S výkonem vlastních zásad umožňují vývojářům a podnikům komunikovat svou značku prostřednictvím přizpůsobení stránky. Přečtěte si, jak [nastavit registraci a přihlašování telefonem pomocí vlastních zásad v Azure AD B2C](../../active-directory-b2c/phone-authentication-user-flows.md).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---november-2020"></a>Nové zřizovací konektory v galerii aplikací Azure AD – listopad 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** integrace třetích stran
 
Nyní můžete automatizovat vytváření, aktualizaci a odstraňování uživatelských účtů pro tyto nově integrované aplikace:

- [Adobe Identity Management](../saas-apps/adobe-identity-management-provisioning-tutorial.md)
- [Blogin](../saas-apps/blogin-provisioning-tutorial.md)
- [Clarizen One](../saas-apps/clarizen-one-provisioning-tutorial.md)
- [Contentful](../saas-apps/contentful-provisioning-tutorial.md)
- [GitHub AE](../saas-apps/github-ae-provisioning-tutorial.md)
- [Playvox](../saas-apps/playvox-provisioning-tutorial.md)
- [PrinterLogic SaaS](../saas-apps/printer-logic-saas-provisioning-tutorial.md)
- [Tic – Tac – mobilní zařízení](../saas-apps/tic-tac-mobile-provisioning-tutorial.md)
- [Visibly](../saas-apps/visibly-provisioning-tutorial.md)

Další informace najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](../app-provisioning/user-provisioning.md).
 
---

### <a name="public-preview---email-sign-in-with-proxyaddresses-now-deployable-via-staged-rollout"></a>Public Preview – e-mailová Sign-In s ProxyAddresses se teď nasazují prostřednictvím připraveného zavedení

**Zadejte:** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů
 
Správci klientů teď můžou k nasazení e-mailových Sign-In s ProxyAddresses do konkrétních skupin Azure AD použít dvoufázové zavedení. To může pomoci při vyzkoušení funkce před jejich nasazením do celého klienta prostřednictvím zásad zjišťování domovské sféry. Pokyny k nasazení e-mailových Sign-In s ProxyAddresses prostřednictvím připraveného zavedení najdete v [dokumentaci](../authentication/howto-authentication-use-email-signin.md).
 
---

### <a name="limited-preview---sign-in-diagnostic"></a>Omezená verze Preview – Diagnostika přihlášení

**Zadejte:** Nová funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav
 
V úvodní verzi Preview diagnostické diagnostiky teď můžou správci zkontrolovat přihlašování uživatelů. Správci mohou získat kontext, konkrétní a relevantní podrobnosti a pokyny, co se stalo během přihlašování a jak opravit problémy. Diagnostika je k dispozici na úrovni služby Azure AD a v okně diagnostikovat a řešit podmíněný přístup. Diagnostickými scénáři popsanými v této verzi jsou podmíněný přístup, Multi-Factor Authentication a úspěšné přihlášení.

Další informace najdete v tématu [co je diagnostika přihlášení ve službě Azure AD?](../reports-monitoring/overview-sign-in-diagnostics.md).
 
---

### <a name="improved-unfamiliar-sign-in-properties"></a>Vylepšené neznámé vlastnosti přihlášení

**Zadejte:** Změněná funkce  
**Kategorie služby:** Ochrana identity  
**Schopnost produktu:** Zabezpečení identity & ochrana

  Zjištění neznámých vlastností přihlášení bylo aktualizováno. Zákazníci si můžou všimnout většího vysokého rizika neznámých detekcí vlastností přihlášení. Další informace najdete v tématu [co je riziko?](../identity-protection/concept-identity-protection-risks.md)
 
---

### <a name="public-preview-refresh-of-cloud-provisioning-agent-now-available-version-112810"></a>Public Preview aktualizace agenta zřizování cloudu je teď k dispozici (verze: 1.1.281.0).

**Zadejte:** Změněná funkce  
**Kategorie služby:** Zřizování cloudu Azure AD  
**Schopnost produktu:** Správa životního cyklu identit
 
Agent zřizování cloudu byl vydán ve verzi Public Preview a je nyní k dispozici prostřednictvím portálu. Tato verze obsahuje několik vylepšení, včetně podpory GMSA pro vaše domény, což zajišťuje lepší zabezpečení, lepší počáteční cyklus synchronizace a podporu velkých skupin. Další podrobnosti najdete v [historii](../app-provisioning/provisioning-agent-release-version-history.md) verzí pro vydání. 
 
---

### <a name="bitlocker-recovery-key-api-endpoint-now-under-informationprotection"></a>Koncový bod rozhraní API pro obnovení BitLockeru hned pod/informationProtection

**Zadejte:** Změněná funkce  
**Kategorie služby:** Správa přístupu k zařízením  
**Schopnost produktu:** Správa životního cyklu zařízení
 
Dřív bylo možné obnovit klíče nástroje BitLocker prostřednictvím koncového bodu/BitLocker. Nakonec už tento koncový bod nebudeme používat a zákazníci by měli začít spotřebovávat rozhraní API, které teď spadá do/informationProtection.. 

Změny v dokumentaci najdete v tématu [rozhraní API pro obnovení nástroje BitLocker](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta) pro aktualizace dokumentace.

---

### <a name="general-availability-of-application-proxy-support-for-remote-desktop-services-html5-web-client"></a>Obecná dostupnost podpory aplikačního proxy serveru pro webový klient služby Vzdálená plocha pro HTML5

**Zadejte:** Změněná funkce  
**Kategorie služby:** Proxy aplikace  
**Schopnost produktu:** Access Control
 
Podpora Azure Proxy aplikací služby AD pro webový klient služby Vzdálená plocha (RDS) je teď obecně dostupná. Webový klient RDS umožňuje uživatelům přístup k infrastruktuře vzdálené plochy prostřednictvím libovolného prohlížeče podporujícího HTLM5, jako je Microsoft Edge, Internet Explorer 11, Google Chrome a tak dále. Uživatelé můžou komunikovat se vzdálenými aplikacemi nebo počítači, jako by se k nim měli místní zařízení odkudkoli. 

Pomocí Azure Proxy aplikací služby AD můžete zvýšit zabezpečení nasazení služby Vzdálená plocha tím, že vynutíte předběžné ověřování a zásady podmíněného přístupu pro všechny typy bohatých klientských aplikací. Další informace najdete v tématu [publikování vzdálené plochy pomocí Azure proxy aplikací služby AD](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
 
---

### <a name="new-enhanced-dynamic-group-service-is-in-public-preview"></a>Nová rozšířená služba dynamické skupiny je v Public Preview

**Zadejte:** Změněná funkce  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Prostřednictvím
 
Vylepšená Dynamická služba skupiny je teď v Public Preview. Noví zákazníci, kteří ve svých klientech vytvářejí dynamické skupiny, budou používat novou službu. Čas potřebný k vytvoření dynamické skupiny bude úměrný velikosti skupiny, která se vytváří místo velikosti tenanta. Tato aktualizace zvýší výkon pro velké klienty významně, když zákazníci vytvoří menší skupiny. 

Nová služba také chce dokončit přidávání a odebírání členů z důvodu změn atributů během několika minut. Selhání v jednom zpracování také neblokuje zpracování tenanta. Další informace o vytváření dynamických skupin najdete v naší [dokumentaci](../enterprise-users/groups-create-rule.md).
 
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
> To nemá vliv na žádné události v protokolech auditu mimo události synchronizace emitované službou zřizování. Události, jako je vytvoření aplikace, zásada podmíněného přístupu, uživatel v adresáři atd. budou nadále vydávány v protokolech auditu. [Další informace](../reports-monitoring/concept-provisioning-logs.md?context=azure%2factive-directory%2fapp-provisioning%2fcontext%2fapp-provisioning-context).
 

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
**Kategorie služby:** Všechny aplikace Azure AD  
**Schopnost produktu:** Zvyšování
 
Azure Active Directory zařadí následující protokoly do 31. března 2021:
- TLS 1.0
- TLS 1.1
- šifrovací sada 3DES (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Všechny kombinace klient-server a prohlížeč-Server by měly používat protokol TLS 1,2 a moderní šifrovací sady k údržbě zabezpečeného připojení k Azure Active Directory pro Azure, Office 365 a služby Microsoft 365.

Ovlivněná prostředí jsou:
- US Gov Azure
- [Office 365 RSZ High & DoD](/microsoft-365/compliance/tls-1-2-in-office-365-gcc)
 
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

Další informace o této funkci najdete v tématu [zakázání a odstranění externích identit pomocí kontrol přístupu Azure AD](../governance/access-reviews-external-users.md#disable-and-delete-external-identities-with-azure-ad-access-reviews).
 
---

### <a name="access-review-creators-can-add-custom-messages-in-emails-to-reviewers"></a>Tvůrci kontroly přístupu můžou přidat vlastní zprávy e-mailů k kontrolorům.

**Zadejte:** Nová funkce  
**Kategorie služby:** Kontroly přístupu  
**Schopnost produktu:** Zásady správného řízení identity
 
V rámci kontrol přístupu ke službě Azure AD teď správci vytvářející recenze můžou napsat vlastní zprávu kontrolorům. Revidující uvidí zprávu v e-mailu, které obdrží, a vyzve k dokončení kontroly. Další informace o použití této funkce najdete v části Krok 14 oddílu [vytvoření jednoho nebo více kontrol přístupu](../governance/create-access-review.md#create-one-or-more-access-reviews) .

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

Doporučujeme, aby zákazníci používali ID šablon rolí ve svém skriptu PowerShellu a kódu místo zobrazovaného názvu. ID šablony role je podporované pro použití pro objekty [directoryRoles](/graph/api/resources/directoryrole) a [rutiny roledefinition](/graph/api/resources/unifiedroledefinition?view=graph-rest-beta) . Další informace o ID šablon rolí najdete v tématu [předdefinované role Azure AD](../roles/permissions-reference.md).

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

Ručně vytvořené propojené organizace budou mít nastavené výchozí nastavení. Mezitím budou automaticky vytvořená (vytvořená prostřednictvím zásad, které umožňují jakémukoli uživateli z Internetu vyžadovat přístup) výchozí nastavení "navrženo".  Všechny připojené organizace vytvořené před září 9 2020 budou nastavené na nakonfigurováno. Správci mohou tuto vlastnost podle potřeby aktualizovat. [Další informace](../governance/entitlement-management-organization.md#managing-a-connected-organization-programmatically).
 

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

[Sentry](../saas-apps/sentry-tutorial.md) [Bumblebee – produktivita superapp](https://app.yellowmessenger.com/user/login), [ABBYY FlexiCapture Cloud](../saas-apps/abbyy-flexicapture-cloud-tutorial.md), [EAComposer](../saas-apps/eacomposer-tutorial.md), [Genesys cloud integration for Azure](https://apps.mypurecloud.com/msteams-integration/), [portál pro zóny](https://portail.zonetechnologie.com/signin), [Beautiful.AI](../saas-apps/beautiful.ai-tutorial.md), [Datawiza pro přístup k](https://console.datawiza.com/)poskytovateli přístupu, [ZOKRI](https://app.zokri.com/), [CheckProof](../saas-apps/checkproof-tutorial.md), [ecoChallenge.org](https://events.ecochallenge.org/users/login), [atSpoke](http://atspoke.com/login), [připomenutí schůzky](https://app.appointmentreminder.co.nz/account/login), [Cloud. trh](https://cloud.market/), [TravelPerk](../saas-apps/travelperk-tutorial.md), [Greetings](https://app.greetly.com/) [, OrgVitality](../saas-apps/orgvitality-sso-tutorial.md) [, Starmind](../saas-apps/starmind-tutorial.md) [,](https://hrm.workstem.com/login) [maloobchodního](../saas-apps/retail-zipline-tutorial.md) [vedení](../saas-apps/loop-flow-crm-tutorial.md) [, Workstem](../saas-apps/web-cargo-air-tutorial.md) [, Zipline](../saas-apps/hoxhunt-tutorial.md) [, Hoxhunt](../saas-apps/mevisio-tutorial.md) [, MEVISIO](../saas-apps/samsara-tutorial.md) [,](../saas-apps/nimbus-tutorial.md) [Pulse Secure Virtual Traffic Manager](../saas-apps/pulse-secure-virtual-traffic-manager-tutorial.md)

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
 
Nyní můžete vlastníkům aplikace dovolit monitorovat aktivity pomocí služby zřizování a řešit problémy bez poskytnutí privilegované role nebo zrušení kritického bodu. [Další informace](../reports-monitoring/concept-provisioning-logs.md).
 
---

### <a name="renaming-10-azure-active-directory-roles"></a>Přejmenování 10 Azure Active Directory rolí

**Zadejte:** Změněná funkce  
**Kategorie služby:** Role Azure  
**Schopnost produktu:** Access Control
 
Některé předdefinované role Azure Active Directory (AD) mají názvy, které se liší od těch, které se zobrazují v centru pro správu Microsoft 365, na portálu Azure AD a na Microsoft Graph. Tato nekonzistence může způsobit problémy v automatizovaných procesech. V této aktualizaci přejmenováváme 10 názvů rolí, aby se zajistila konzistence. Nové názvy rolí mají následující tabulka:

![Tabulka znázorňující názvy rolí v MS Graph API a Azure Portal a navrhovaný název nové role v centru pro správu M365, Azure Portal a v rozhraní API.](media/whats-new/azure-role.png)

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

- [CODA](../saas-apps/coda-provisioning-tutorial.md)
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

    Díky této funkci můžou správci IT mapovat atributy uživatelů, skupin nebo kontaktů ze služby AD na Azure AD pomocí různých typů mapování, které jsou dnes přítomné. Mapování atributů je funkce používaná ke standardizaci hodnot atributů, které se přenášejí ze služby Active Directory na Azure Active Directory. Jedna z nich může určit, jestli má být hodnota atributu přímo namapována z AD do služby Azure AD, nebo použít výrazy k transformaci hodnot atributů při zřizování uživatelů. [Další informace](../cloud-sync/how-to-attribute-mapping.md)

- Zřizování na vyžádání nebo testování uživatelského prostředí

    Po nastavení konfigurace můžete chtít ověřit, zda transformace uživatele pracuje podle očekávání, a teprve potom ji použít pro všechny uživatele v oboru. Díky zřizování na vyžádání můžou správci IT zadat rozlišující název (DN) uživatele AD a zjistit, jestli se nesynchronizují podle očekávání. Zřizování na vyžádání poskytuje skvělý způsob, jak zajistit, že mapování atributů, které jste dříve pracovali, podle očekávání. [Další informace](../cloud-sync/how-to-on-demand-provision.md)
 
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