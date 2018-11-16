---
title: Archiv co je nového? ve službě Azure Active Directory | Dokumentace Microsoftu
description: Co je nového poznámky k verzi v přehledu, že část této sady obsahu obsahuje 6 měsíců aktivity. Po 6 měsíců položky se odeberou z hlavní článek a vložit do tohoto článku archivu.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: lizross
ms.reviewer: dhanyahk
ms.openlocfilehash: 22e0f95b1afec39574309a8deed8a020145c38ec
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51708735"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Archiv co je nového? ve službě Azure Active Directory

Primární [co je nového zpráva k vydání verze](whats-new.md) článku obsahují nejnovější 6 měsíců informace, pokud tento článek obsahuje starší informace.

Co je nového zpráva k vydání verze poskytování informací o:

- Nejnovější vydané verzi
- Známé problémy
- Opravy chyb
- Zastaralé funkce
- Plány pro změny

---
 
## <a name="april-2018"></a>Duben 2018 

### <a name="azure-ad-b2c-access-token-are-ga"></a>Azure AD B2C přístupový Token jsou obecně dostupné

**Typ:** nová funkce  
**Kategorie služby:** B2C - správu identit zákazníků  
**Funkce produktu:** B2B a B2C 

Teď přístup k webovým rozhraním API zabezpečené pomocí Azure AD B2C použitím přístupových tokenů. Tato funkce se přesouvá z verze public preview na všeobecně dostupnou Vylepšili jsme prostředí uživatelského rozhraní pro konfiguraci aplikací Azure AD B2C a webových rozhraní API a další drobná vylepšení byly provedeny.
 
Další informace najdete v tématu [Azure AD B2C: žádost o přístupové tokeny](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-access-tokens).

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Testování konfigurace jednotného přihlašování pro aplikace založené na SAML

**Typ:** nová funkce  
**Kategorie služby:** podnikových aplikací  
**Funkce produktu:** jednotného přihlašování

Při konfiguraci aplikace založené na SAML jednotného přihlašování, budete moci testovat integraci na stránce konfigurace. Pokud narazíte na chybu během svého přihlašování, můžete zadat chyby v testovacím prostředí a Azure AD poskytuje řešení kroky k vyřešení konkrétního problému.

Další informace naleznete v tématu:

- [Konfigurace jednotného přihlašování k aplikacím, které nejsou v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)
- [Ladění založené na SAML jednotného přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

---
 
### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Azure AD Terms of Use teď obsahuje generování sestav podle uživatelů

**Typ:** nová funkce  
**Kategorie služby:** podmínky použití  
**Funkce produktu:** dodržování předpisů
 
Správci teď můžete vybrat dané podmínky použití a zobrazení všech uživatelů, které mají souhlasil se podmínky použití a co datu a času byla provedena.

Další informace najdete v tématu [funkcí Azure AD podmínky použití](https://docs.microsoft.com/azure/active-directory/active-directory-tou).

---
 
### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: Riziková IP adresa pro ochranu před uzamčením extranetu AD FS 

**Typ:** nová funkce  
**Kategorie služby:** další  
**Funkce produktu:** monitorování a vytváření sestav

Connect Health teď podporuje schopnost detekovat IP adresy, které by překračovaly prahovou hodnotu neúspěšných přihlášení U/P na hodinové nebo denní bázi. Možnosti poskytované touto funkcí jsou:

- Komplexní sestavu zobrazující IP adresu a počet neúspěšných přihlášení, které se pak generuje každý hodin nebo dnů s přizpůsobitelné prahovou hodnotu.
- E mailových výstrah, zobrazení překročení prahové hodnoty neúspěšných přihlášení U/P na základě hodin nebo dnů konkrétní IP adresu.
- Možnost provádět podrobné analýzy dat.

Další informace najdete v tématu [sestavě rizikových IP adres](https://aka.ms/aadchriskyip).

---
 
### <a name="easy-app-config-with-metadata-file-or-url"></a>Snadná konfigurace aplikace pomocí souboru metadat nebo adresy URL

**Typ:** nová funkce  
**Kategorie služby:** podnikových aplikací  
**Funkce produktu:** jednotného přihlašování

Na stránce aplikace organizace můžete správci nahrát soubor metadat SAML konfigurace přihlašování SAML podle pro aplikaci Galerie AAD a mimo galerii.

Kromě toho adresa URL federačních metadat aplikace Azure AD můžete nakonfigurovat jednotné přihlašování s cílovou aplikací.

Další informace najdete v tématu [Konfigurace jednotného přihlašování k aplikacím, které nejsou v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Azure AD podmínky použití, které jsou teď obecně dostupná

**Typ:** nová funkce  
**Kategorie služby:** podmínky použití  
**Funkce produktu:** dodržování předpisů
 

Azure AD Terms of Use přešli z verze public preview na všeobecně dostupná.

Další informace najdete v tématu [funkcí Azure AD podmínky použití](https://docs.microsoft.com/azure/active-directory/active-directory-tou).

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Povolení nebo blokování pozvánek uživatelů B2B z konkrétních organizací

**Typ:** nová funkce  
**Kategorie služby:** B2B  
**Funkce produktu:** B2B a B2C
 

Teď můžete zadat které partnerských organizacích, kterou chcete sdílet a spolupracovat v spolupráce B2B ve službě Azure AD. K tomuto účelu můžete vytvořit seznam konkrétních povolují nebo odpírají domén. Domény blokován, pomocí těchto možností, zaměstnanci už odeslat pozvánky uživatelům v této doméně.

Díky tomu můžete řídit přístup k vašim prostředkům při povolování hladký pro schválení uživatelé.

Tato funkce spolupráce B2B je k dispozici pro všechny zákazníky Azure Active Directory a můžou používat ve spojení s funkcí Azure AD Premium, jako je podmíněný přístup a identity protection pro podrobnější kontrolu nad kdy a jak podepsat externí podnikoví uživatelé v a získat přístup.

Další informace najdete v tématu [povolení nebo blokování pozvánek uživatelů B2B z konkrétních organizací](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-allow-deny-list).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nové federované aplikace k dispozici v galerii aplikací Azure AD

**Typ:** nová funkce  
**Kategorie služby:** podnikových aplikací  
**Funkce produktu:** 3. stran integrace

V dubnu 2018 přidali jsme podporu těchto 13 nové aplikace s federací do naší Galerie aplikací:

Kritérium HCM, [FiscalNote](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fiscalnote-tutorial), [tajný klíč serveru (místní)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-secretserver-on-premises-tutorial), [dynamické signál](https://docs.microsoft.com/azure/active-directory/active-directory-saas-dynamicsignal-tutorial), [mindWireless](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mindwireless-tutorial), [organizačního diagramu Nyní](https://docs.microsoft.com/azure/active-directory/active-directory-saas-orgchartnow-tutorial), [Ziflow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ziflow-tutorial), [sledování výkonu AppNeta](https://docs.microsoft.com/azure/active-directory/active-directory-saas-appneta-tutorial), [Elium](https://docs.microsoft.com/azure/active-directory/active-directory-saas-elium-tutorial) , [Fluxx Labs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fluxxlabs-tutorial), [ Cisco cloudu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ciscocloud-tutorial), police, [SafetyNet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-safetynet-tutorial)

Další informace o aplikacích najdete v tématu [integrace aplikací SaaS pomocí Azure Active Directory](https://aka.ms/appstutorial).

Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete v tématu [uvedení aplikace v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Přístup k uživatelům udělit B2B ve službě Azure AD pro vaše místní aplikace (public preview)

**Typ:** nová funkce  
**Kategorie služby:** B2B  
**Funkce produktu:** B2B a B2C

Jako organizace, která používá možnosti spolupráce B2B Azure Active Directory (Azure AD) se pozvat uživatele typu Host z partnerských organizací do služby Azure AD můžete nyní zadat těmto uživatelům B2B přístup k místním aplikacím. Tyto místní aplikace můžou používat ověřování založené na SAML nebo integrované ověřování Windows (IWA) s omezené delegování protokolu Kerberos (KCD).

Další informace najdete v tématu [uživatele udělit B2B ve službě Azure AD přístup k místním aplikacím](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-hybrid-cloud-to-on-premises).

---
 
### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Získejte kurzy integrace jednotného přihlašování z Azure Marketplace

**Typ:** změněné funkce  
**Kategorie služby:** další  
**Funkce produktu:** 3. stran integrace

Pokud aplikace, která je uvedena v [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) podporuje založené na SAML jednotného přihlašování, kliknutím na **získat** vám poskytne kurz integrace přidruženou k této aplikaci. 

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Rychlejší výkon služby Azure AD automatické zřizování uživatelů pro aplikace SaaS

**Typ:** změněné funkce  
**Kategorie služby:** zřizování aplikací  
**Funkce produktu:** 3. stran integrace
 
Dřív se zákazníci, kteří používají zřizování konektorů aplikací SaaS (například Salesforce, ServiceNow a pole) uživatelů Azure Active Directory může zaznamenat zpomalení výkonu více než 100 000 kombinované uživatelé s omezením jejich tenantů Azure AD a skupiny a jejich používali přiřazení uživatelů a skupin k určení, kteří by mělo proběhnout zřízení.

2. dubna 2018 se nasadily významných vylepšení výkonu ke službě zřizování Azure AD, který výrazně omezit množství času potřebného k provedení počáteční synchronizace mezi Azure Active Directory a cílovým aplikacím SaaS.

V důsledku toho mnoho zákazníků, které má počáteční synchronizace do aplikace, které trvalo počtu dnů nebo nikdy dokončeno, jsou teď dokončení v řádu minut nebo hodin.

Další informace najdete v tématu [co se stane při zřizování?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning#what-happens-during-provisioning)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Počítače připojené k samoobslužné resetování hesla ze zamykací obrazovky Windows 10 k hybridní službě Azure AD

**Typ:** změněné funkce  
**Kategorie služby:** samoobslužného resetování hesla  
**Funkce produktu:** ověřování uživatelů
 
Aktualizovali jsme funkci samoobslužného resetování HESLA Windows 10 zahrnují podporu pro počítače, které jsou připojená k hybridní Azure AD. Tato funkce je dostupná ve Windows 10 RS4 umožňuje uživatelům resetovat jejich hesla ze zamykací obrazovky z počítače s Windows 10. Uživatelé, kteří jsou povoleni a zaregistrován pro resetování hesla pomocí samoobslužné služby mohou tuto funkci využít.

Další informace najdete v tématu [hesla Azure AD z přihlašovací obrazovky resetování](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows).

---

## <a name="march-2018"></a>. Března 2018
 
### <a name="certificate-expire-notification"></a>Certifikát vypršel oznámení

**Typ:** oprava  
**Kategorie služby:** podnikových aplikací  
**Funkce produktu:** jednotného přihlašování
 
Azure AD posílá oznámení, když se certifikát pro galerii nebo aplikaci mimo Galerii brzy vyprší platnost. 

Někteří uživatelé nepřijala oznámení pro podnikové aplikace, které jsou nakonfigurované pro založené na SAML jednotného přihlašování. Tento problém byl vyřešen. Azure AD posílá oznámení pro certifikáty vyprší za 7, 30 a 60 dnů. Budete moct této události v protokolech auditování. 

Další informace naleznete v tématu:

- [Správa certifikátů pro federované jednotné přihlašování v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Sestavy aktivit auditu na portálu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
 
---
 
### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Zprostředkovatelé identity twitteru a Githubu v Azure AD B2C

**Typ:** nová funkce  
**Kategorie služby:** B2C - správu identit zákazníků  
**Funkce produktu:** B2B a B2C
 
Nyní můžete přidat Twitter nebo Githubu jako zprostředkovatele identity v Azure AD B2C. Twitter se přesouvá z verze public preview na všeobecně dostupnou GitHub se vydává ve verzi public preview.

Další informace najdete v tématu [co je spolupráce B2B ve službě Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
 
---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Omezit přístup z prohlížeče pomocí Intune Managed Browseru s podmíněným přístupem založeným na aplikaci Azure AD pro iOS a Android

**Typ:** nová funkce  
**Kategorie služby:** podmíněného přístupu  
**Funkce produktu:** zabezpečení a ochrana Identity
 
**Nyní ve verzi public preview!**

**Intune Managed Browser jednotného přihlašování:** vaši zaměstnanci můžete použít jednotné přihlašování napříč nativní klienty (třeba Microsoft Outlook) a spravovaný prohlížeč Intune pro aplikace všechny propojené s AD Azure.

**Spravovaný prohlížeč podmíněného přístupu podpory Intune:** můžete nyní vyžadovat, aby uživatelům používat Intune Managed browser pomocí zásady podmíněného přístupu na základě aplikace.

Další informace najdete v našich [blogový příspěvek](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

Další informace naleznete v tématu:

- [Nastavení podmíněného přístupu na základě aplikace](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

- [Konfigurace zásad spravovaného prohlížeče](https://aka.ms/managedbrowser)  

---
 
### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>Rutiny Proxy aplikací v modulu Powershell GA Module

**Typ:** nová funkce  
**Kategorie služby:** Proxy aplikací  
**Funkce produktu:** řízení přístupu
 
Podpora pro rutiny Proxy aplikací je teď v modul Powershell GA Module. To vyžaduje, abyste aktualizace v prostředí Powershell moduly – Pokud je více než rok, některé rutiny můžou přestat fungovat. 

Další informace najdete v tématu [AzureAD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0).
 
---
 
### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Podporuje nativní klienty Office 365 pomocí neinteraktivního protokolu bezproblémového jednotného přihlašování

**Typ:** nová funkce  
**Kategorie služby:** ověřování (přihlášení)  
**Funkce produktu:** ověřování uživatelů
 
Uživatele s využitím nativní klienty Office 365 (verze 16.0.8730.xxxx a vyšší) získat bezobslužné možnosti přihlašování pomocí bezproblémového jednotného přihlašování. Tato podpora je poskytována přidání jako neinteraktivní protokol (WS-Trust) do služby Azure AD.

Další informace najdete v tématu [jak přihlásit na nativní klientovi pomocí bezproblémového jednotného přihlašování práce?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work)
 
---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>Uživatelé získají tiché přihlašování, pomocí bezproblémového jednotného přihlašování, když aplikace pošle žádostí o přihlášení ke koncovým bodům tenantů Azure AD

**Typ:** nová funkce  
**Kategorie služby:** ověřování (přihlášení)  
**Funkce produktu:** ověřování uživatelů
 
Uživatelé získají tiché přihlašování, pomocí bezproblémového jednotného přihlašování, pokud manifest aplikace (například `https://contoso.sharepoint.com`) odešle žádost o přihlášení ke koncovým bodům tenantů Azure AD – to znamená `https://login.microsoftonline.com/contoso.com/<..>` nebo `https://login.microsoftonline.com/<tenant_ID>/<..>` – namísto společný koncový bod Azure AD (`https://login.microsoftonline.com/common/<...>`).

Další informace najdete v tématu [Azure Active Directory bezproblémové jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 

---
 
### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Třeba přidat pouze jednu adresu URL služby Azure AD, namísto dřívějších dvou. do nastavení zóny Intranet uživatele k zavedení bezproblémového jednotného přihlašování

**Typ:** nová funkce  
**Kategorie služby:** ověřování (přihlášení)  
**Funkce produktu:** ověřování uživatelů
 
K zavedení bezproblémového jednotného přihlašování pro vaše uživatele, budete muset přidat pouze jeden URL Azure AD k intranetu uživatelů nastavení zón s využitím zásad skupiny ve službě Active Directory: `https://autologon.microsoftazuread-sso.com`. Zákazníci byly dříve, povinné pro přidání dvě adresy URL.

Další informace najdete v tématu [Azure Active Directory bezproblémové jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 
 
---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nové federované aplikace k dispozici v galerii aplikací Azure AD

**Typ:** nová funkce  
**Kategorie služby:** podnikových aplikací  
**Funkce produktu:** 3. stran integrace

V březnu 2018 přidali jsme podporu těchto 15 nových aplikací s federací do naší Galerie aplikací:

[Boxcryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial), [CylancePROTECT](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial), Wrike, [SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial), Průvodce nastavením podle FirstAgenda, [YardiOne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial), Vtiger CRM, inwink, [amplitudě](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial), [Spacio](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial), [ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial), [Bersin](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial), [Mercell](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial), [Trisotech digitální Podnikový Server](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial), [Qumu cloudu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial).
 
Další informace o aplikacích najdete v tématu [integrace aplikací SaaS pomocí Azure Active Directory](https://aka.ms/appstutorial).

Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete v tématu [uvedení aplikace v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="pim-for-azure-resources-is-generally-available"></a>PIM pro Azure Resources je obecně dostupná

**Typ:** nová funkce  
**Kategorie služby:** Privileged Identity Management  
**Funkce produktu:** Privileged Identity Management
 
Pokud používáte Azure AD Privileged Identity Management pro role adresáře, můžete nyní použít PIM pro časově vázaná přístup a možnosti přiřazení pro role prostředků Azure, jako je například předplatná, skupiny prostředků, virtuální počítače a dalších prostředků, podporované pomocí Azure Resource Manageru. Vynutit ověřování Multi-Factor Authentication při aktivaci role Just-In-Time a plánování aktivací ve spolupráci s windows schválené změny. Kromě toho tato verze přidává vylepšení ve verzi public preview, včetně aktualizované uživatelské rozhraní, pracovních postupů schvalování a možnosti pro rozšíření role zanedlouho vyprší platnost a prodloužit platnost rolí není k dispozici.

Další informace najdete v tématu [PIM pro prostředky Azure (Preview)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)
 
---
 
### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Přidávání nepovinných deklarací identity do tokenů aplikací (public preview)

**Typ:** nová funkce  
**Kategorie služby:** ověřování (přihlášení)  
**Funkce produktu:** ověřování uživatelů
 
Vaše aplikace Azure AD můžete nyní žádosti o vlastní nebo nepovinné deklarace identity v tokeny Jwt nebo SAML tokeny.  Jedná se o deklarace identity o uživateli nebo tenantovi, které nejsou zahrnuté ve výchozím nastavení v tokenu, kvůli omezení velikosti nebo použitelnosti.  Toto je momentálně ve verzi public preview pro aplikace Azure AD na koncových bodech v1.0 a v2.0.  Najdete v dokumentaci pro informace na jaké deklarace identity je možné přidat a jak upravit manifest aplikace na vyžádání.  

Další informace najdete v tématu [nepovinné deklarace identity ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).
 
---
 
### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Azure AD podporuje PKCE pro bezpečnější toky OAuth.

**Typ:** nová funkce  
**Kategorie služby:** ověřování (přihlášení)  
**Funkce produktu:** ověřování uživatelů
 
Dokumentace služby Azure AD se aktualizovaly poznamenat podpora PKCE, který umožňuje bezpečnější komunikaci během toku udělení autorizačního kódu OAuth 2.0.  V koncových bodech v1.0 a v2.0 podporují jak S256 tak ve formátu prostého textu code_challenges. 

Další informace najdete v tématu [požádat o autorizační kód](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code). 
 
---
 
### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-getworkers-api"></a>Podpora zřizování všech hodnot atributů uživatelů dostupných v rozhraní Workday Get_Workers API

**Typ:** nová funkce  
**Kategorie služby:** zřizování aplikací  
**Funkce produktu:** 3. stran integrace
 
Verze public preview příchozího zřizování z Workday do Active Directory a Azure AD teď podporuje možnost extrahovat a zřizování pro všechny hodnoty atributů k dispozici v rozhraní Workday Get_Workers API. Tento postup přidá podporuje pro stovky dalších standardní a vlastní atributy nad rámec těch, které jsou součástí počáteční verze pracovního dne příchozí zřizování konektoru.

Další informace najdete v tématu: [přizpůsobení seznamu atributů uživatelů Workday](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Změna členství ve skupině z dynamického na statické a naopak

**Typ:** nová funkce  
**Kategorie služby:** Správa skupiny  
**Funkce produktu:** spolupráce
 
Je možné změnit, jak se spravuje členství ve skupině. To je užitečné, pokud chcete zachovat stejný název skupiny a ID sady, tak, aby byly všechny existující odkazy na skupiny stále platná. vytváří se nová skupina bude vyžadovat aktualizaci těchto odkazů.
Aktualizovali jsme Centrum pro správu Azure AD pro podporu této funkce. Teď zákazníci můžou převést stávající skupiny z dynamického členství na přiřazené členství a naopak. Existující rutiny Powershellu jsou stále dostupné.

Další informace najdete v tématu [změnu dynamického členství na statické a naopak](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal#changing-dynamic-membership-to-static-and-vice-versa)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Vylepšené chování při odhlašování pomocí bezproblémového jednotného přihlašování

**Typ:** změněné funkce  
**Kategorie služby:** ověřování (přihlášení)  
**Funkce produktu:** ověřování uživatelů
 
Dříve i v případě, že uživatelé explicitně odhlásili z aplikace zabezpečené pomocí Azure AD, jsou by být automaticky přihlášeni zpět pomocí bezproblémového jednotného přihlašování, pokud se pokoušeli pro přístup k aplikaci Azure AD znovu v rámci své podnikové síti z jejich zařízení připojených k doméně. Díky této změně se podporuje odhlašování.  To umožňuje uživatelům zvolit stejný nebo jiný Azure AD účtu se přihlásit zpátky pomocí, namísto automaticky Probíhá přihlašování pomocí bezproblémového jednotného přihlašování.

Další informace najdete v tématu [Azure Active Directory bezproblémové jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)
 
---
 
### <a name="application-proxy-connector-version-154020-released"></a>Verze konektoru Proxy aplikací 1.5.402.0 všeobecně dostupné

**Typ:** změněné funkce  
**Kategorie služby:** Proxy aplikací  
**Funkce produktu:** zabezpečení a ochrana Identity
 
Tato verze konektoru se postupně zavádí do listopadu. Tato nová verze konektoru obsahuje následující změny:

- Konektor teď nastavuje soubory cookie úrovni domény místo toho úrovni subdomény. To zajistí, že plynulost jednotného přihlašování a zabraňuje výzev k ověření redundantní.
- Podpora pro požadavky na blokové kódování
- Vylepšený konektor monitorování stavu 
- Několik oprav chyb a vylepšení stability

Další informace najdete v tématu [pochopit Azure AD Application Proxy konektory](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors).
 
---

## <a name="february-2018"></a>. Února 2018
 
### <a name="improved-navigation-for-managing-users-and-groups"></a>Vylepšená navigace pro správu uživatelů a skupin

**Typ:** plánování změn  
**Kategorie služby:** Správa adresářů  
**Funkce produktu:** adresáře

Navigační prostředí pro správu uživatelů a skupin je teď jednodušší. Z přehledu adresáře se teď můžete přejít přímo na seznam všech uživatelů, jednodušší přístup k seznamu odstraněných uživatelů. Z přehledu adresáře můžete také přejít přímo na seznam všech skupin, jednodušší přístup k nastavení skupiny pro správu. A také na stránce přehledu adresáře můžete hledat uživatele, skupiny, Podniková aplikace nebo registrace aplikace. 

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Dostupnost přihlašování a auditu sestavy v Microsoft Azure provozované společností 21Vianet (Azure China 21Vianet)

**Typ:** nová funkce  
**Kategorie služby:** Azure Stack  
**Funkce produktu:** monitorování a vytváření sestav

Sestavy protokolu Azure AD aktivit jsou teď dostupné v Microsoft Azure provozované společností 21Vianet (Azure China 21Vianet) instance. Tyto protokoly jsou zahrnuty:

- **Protokoly aktivit přihlášení** – zahrnuje všechna přihlášení protokolům přidruženým vašeho tenanta.

- **Protokoly auditu heslo pro samoobslužné** – zahrnuje všechny protokoly auditu samoobslužného resetování HESLA.

- **Protokoly auditu správy adresáře** – zahrnuje všechny protokoly auditu souvisejících se správou adresář jako uživatel správy, Správa aplikací a dalších.

Pomocí těchto protokolů umožňují získat přehled o tom, jak stavu vašeho prostředí. Poskytnutá data vám umožní:

- Zjistěte, jak vaše aplikace a služby využít vašich uživatelů.

- Řešení potíží s problémy, které brání uživatelům v přístupu svou práci.

Další informace o tom, jak tyto sestavy použít, najdete v části [generování sestav Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal).

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Zobrazení sestav aktivit Azure AD pomocí role "Čtenáře sestav" (bez oprávnění správce role)

**Typ:** nová funkce  
**Kategorie služby:** generování sestav  
**Funkce produktu:** monitorování a vytváření sestav

Jako součást zpětné vazby zákazníků bez oprávnění správce rolí má přístup k aktivit Azure AD povolit protokoly, zavedli jsme možnost pro uživatele, kteří jsou v roli "Čtenáře sestav" přístup k přihlášení a auditování aktivit v rámci webu Azure portal, jakož i pomocí našich rozhraní Graph API. 

Další informace o použití těchto sestav najdete v tématu [generování sestav Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Deklarace identity EmployeeID dostupná jako atribut a identifikátor uživatele

**Typ:** nová funkce  
**Kategorie služby:** podnikových aplikací  
**Funkce produktu:** jednotného přihlašování
 
Můžete nakonfigurovat **EmployeeID** jako uživatelský identifikátor a atribut uživatele pro uživatele člena a hosty B2B aplikace založené na SAML přihlašování z podnikové aplikace uživatelského rozhraní.

Další informace najdete v tématu [přizpůsobování deklarací identity vystavených v tokenu SAML pro podnikové aplikace v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Zjednodušená správa aplikací pomocí zástupných znaků v Azure AD Application Proxy

**Typ:** nová funkce  
**Kategorie služby:** Proxy aplikací  
**Funkce produktu:** ověřování uživatelů
 
K usnadnění nasazení aplikací a snížit vaše režijní náklady na správu, podporujeme nyní možnost publikovat aplikace pomocí zástupných znaků. Pokud chcete publikovat aplikaci se zástupnými znaky, můžete postupovat podle toku publikování standardní aplikace, ale použít zástupný znak v interní a externí adresy URL.

Další informace najdete v tématu [aplikace se zástupnými znaky v proxy aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-wildcard)

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Nové rutiny, které podporují konfiguraci Proxy aplikací

**Typ:** nová funkce  
**Kategorie služby:** Proxy aplikací  
**Funkce produktu:** platformy

Nejnovější vydaná verze modulu AzureAD PowerShell Preview obsahuje nové rutiny, které umožňují zákazníkům konfigurovat aplikace Proxy aplikací pomocí prostředí PowerShell.

Jsou nové rutiny: 

- Get-AzureADApplicationProxyApplication
- Get-AzureADApplicationProxyApplicationConnectorGroup
- Get-AzureADApplicationProxyConnector
- Get-AzureADApplicationProxyConnectorGroup
- Get-AzureADApplicationProxyConnectorGroupMembers
- Get-AzureADApplicationProxyConnectorMemberOf
- New-AzureADApplicationProxyApplication
- New-AzureADApplicationProxyConnectorGroup
- Remove-AzureADApplicationProxyApplication
- Remove-AzureADApplicationProxyApplicationConnectorGroup
- Remove-AzureADApplicationProxyConnectorGroup
- Set-AzureADApplicationProxyApplication
- Set-AzureADApplicationProxyApplicationConnectorGroup
- Set-AzureADApplicationProxyApplicationCustomDomainCertificate
- Set-AzureADApplicationProxyApplicationSingleSignOn
- Set-AzureADApplicationProxyConnector
- Set-AzureADApplicationProxyConnectorGroup

---
 
### <a name="new-cmdlets-to-support-configuration-of-groups"></a>Nové rutiny, které podporují konfiguraci skupin

**Typ:** nová funkce  
**Kategorie služby:** Proxy aplikací  
**Funkce produktu:** platformy

Nejnovější vydaná verze modulu AzureAD PowerShell obsahuje rutiny pro správu skupin ve službě Azure AD. Tyto rutiny byly dříve k dispozici v modulu AzureADPreview a teď se přidají do modulu AzureAD

Jsou rutiny skupiny, které jsou nyní verze pro obecná dostupnost: 

- Get-AzureADMSGroup
- New-AzureADMSGroup
- Remove-AzureADMSGroup
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup   
- Get-AzureADMSLifecyclePolicyGroup

---
 
### <a name="a-new-release-of-azure-ad-connect-is-available"></a>Je k dispozici nová verze služby Azure AD Connect

**Typ:** nová funkce  
**Kategorie služby:** AD Sync  
**Funkce produktu:** platformy
 
Azure AD Connect je preferovaný nástroj pro synchronizaci dat mezi službami Azure AD a na místní zdroje dat, včetně systému Windows Server Active Directory a protokolu LDAP.

>[!Important]
>Toto sestavení představuje schématu a synchronizační pravidlo změny. Službu Azure AD Connect synchronizaci aktivuje úplný Import a úplnou synchronizaci kroků po upgradu. Informace o tom, jak toto chování změnit, naleznete v tématu [jak odložit úplnou synchronizaci po upgradu](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade).

Tato verze má následující aktualizace a změny:

**Opravené problémy**

- Opravte časové okno na úlohy na pozadí pro filtrování oddílů stránku při přechodu na další stránku.

- Je opravená chyba, která způsobila narušení přístupu při ConfigDB vlastní akce.

- Je opravená chyba, provést obnovení při vypršení časového limitu připojení sql.

- Oprava chyby, které certifikáty se zástupnými znaky SAN nezdaří Kontrola předpokladů.

- Opravili jsme chybu, která způsobí, že miiserver.exe selhání během exportu konektoru AAD.

- Oprava chyby, kde pokusů o zadání hesla chybná, protokolují na řadiči domény při spuštění způsobit AAD connect průvodce a změňte konfiguraci

**Nové funkce a vylepšení**
 
- Telemetrie Application - správci můžete přepínat Tato třída dat zapnout nebo vypnout.

- Azure AD Health dat – správci musí navštivte portál stavu řídit jejich nastavení stavu. Po změně zásad služby se agenti přečte a vynutit.

- Přidání akce konfigurace zpětného zápisu zařízení a indikátor průběhu inicializace stránky.

- Vylepšená Diagnostika obecné sestavu ve formátu HTML a úplná kolekci písmem ZIP / sestavu ve formátu HTML.

- Vylepšení spolehlivosti automaticky upgradovat a přidat další telemetrické údaje do Ujistěte se, že se dá určit stav serveru.

- Omezte oprávnění k dispozici k privilegovaným účtům na účet AD Connector. Průvodce pro nové instalace omezuje oprávnění, která privilegované účty na svém účtu MSOL máte po vytvoření účtu služby MSOL. Změny se projeví u Expresní instalace a vlastní zařízení s automaticky vytvořit účet.

- Změnit instalační program a nevyžaduje oprávnění správce systému při čisté instalaci AADConnect.

- Nový nástroj pro řešení potíží se synchronizací pro konkrétní objekt. V současné době nástroj vyhledá následující věci:

    - UserPrincipalName Neshoda mezi objekt synchronizované uživatele a uživatelský účet v Tenantovi Azure AD.
  
    - Pokud objekt je filtrováno z synchronizace kvůli filtrování domény
  
    - Pokud objekt je filtrováno z synchronizace, protože organizační jednotka (OU) filtrování

- Nový nástroj pro synchronizaci aktuální hodnoty hash hesla uložená v místním Active Directory pro konkrétní uživatelský účet. Nástroj nevyžaduje, aby změnu hesla. 

---
 
### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Aplikace, které jsou přidány podpůrné zásady ochrany aplikací Intune pro použití s podmíněným přístupem založeným na aplikaci Azure AD

**Typ:** změněné funkce  
**Kategorie služby:** podmíněného přístupu  
**Funkce produktu:** zabezpečení a ochrana Identity

Přidali jsme další aplikace, které podporují podmíněný přístup založený na aplikaci. Teď můžete získat přístup k Office 365 a dalším aplikacím Azure AD připojení cloudu pomocí těchto schválených klientských aplikací.

Následující aplikace se přidají koncem února:

- Microsoft Power BI

- Spouštěcí program společnosti Microsoft

- Microsoft fakturace

Další informace naleznete v tématu:

- [Požadavek schválené klientské aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Na základě aplikace podmíněný přístup Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Aktualizace podmínek použití pro mobilní prostředí 

**Typ:** změněné funkce  
**Kategorie služby:** podmínky použití  
**Funkce produktu:** dodržování předpisů

Když se zobrazí podmínky použití, můžete nyní kliknout na **máte problémy se zobrazením? Kliknutím sem**. Kliknutím na tento odkaz otevře podmínky použití nativně na vašem zařízení. Bez ohledu na velikost písma v dokumentu nebo rozlišení obrazovky zařízení můžete přiblížit a přečteme dokument podle potřeby. 

---
 
## <a name="january-2018"></a>. Ledna 2018
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nové federované aplikace k dispozici v galerii aplikací Azure AD 

**Typ:** nová funkce  
**Kategorie služby:** podnikových aplikací  
**Funkce produktu:** 3. stran integrace

V lednu 2018 byly přidány následující nové aplikace s podporou federace v galerii aplikací:

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [Software pro správu ochrany osobních údajů OneTrust](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [IriusRisk federované adresáře, a [věrnost NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701).

Další informace o aplikacích najdete v tématu [integrace aplikací SaaS pomocí Azure Active Directory](https://aka.ms/appstutorial).

Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete v tématu [uvedení aplikace v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="sign-in-with-additional-risk-detected"></a>Přihlaste se pomocí další zjištěné riziko

**Typ:** nová funkce  
**Kategorie služby:** Identity Protection  
**Funkce produktu:** zabezpečení a ochrana Identity

Podrobnější informace o získání zjištěné rizikové události se váže k předplatnému služby Azure AD. V edici Azure AD Premium P2 získáte nejpodrobnější informace o základní všechna nalezení.

V edici Azure AD Premium P1 detekcí, které nejsou pokryty všemi licence jeví jako riziková událost přihlášení s dalšími riziky. zjistili.

Další informace najdete v tématu věnovaném [rizikovým událostem služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events).
 
---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Skrýt aplikace Office 365 z přístupových panelech koncového uživatele

**Typ:** nová funkce  
**Kategorie služby:** Moje aplikace  
**Funkce produktu:** jednotného přihlašování

Teď můžete lépe spravovat jak aplikacím Office 365 objeví na přístupových panelech uživatele pomocí nového nastavení uživatele. Tato možnost je užitečná pro snížení počtu aplikací v přístupových panelech uživatele, pokud chcete zobrazit jenom aplikace Office na portálu Office. Toto nastavení se nachází v **uživatelská nastavení** a je označený, **uživatelé vidí pouze aplikace Office 365 na portálu Office 365**.

Další informace najdete v tématu [skrytí aplikace prostředí uživatele v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

---
 
### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Bezproblémové přihlašování do aplikací, které jsou povolené pro jednotné přihlašování hesla přímo z adresy URL vaší aplikace 

**Typ:** nová funkce  
**Kategorie služby:** Moje aplikace  
**Funkce produktu:** jednotného přihlašování

Rozšíření prohlížeče Moje aplikace je teď dostupná přes praktický nástroj, který poskytuje jednotné přihlašování v Moje aplikace na funkce jako zástupce v prohlížeči. Po instalaci se uživatele se zobrazuje ikona dlaždice ve svém prohlížeči, který jim poskytuje rychlý přístup k aplikacím. Uživatelé teď mohou využít výhod:

- Umožňuje přímo se přihlásit k aplikacím na základě heslo – jednotné přihlašování z aplikace přihlašovací stránky
- Spuštění libovolné aplikace pomocí funkce rychlého vyhledávání
- Klávesové zkratky pro naposledy použité aplikace z rozšíření
- Rozšíření je k dispozici pro Edge, Chrome a Firefox.
 
Další informace najdete v tématu [Moje aplikace zabezpečené přihlašování rozšíření](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction#my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Správa služby Azure AD byl vyřazen prostředí portálu Azure Classic

**Typ:** zastaralé   
**Kategorie služby:** Azure AD  
**Funkce produktu:** adresáře

Od 8. ledna 2018, Správa služby Azure AD byl vyřazen prostředí na portálu Azure classic. To konal úplně ve spojení s vyřazením na portálu Azure classic, samotného. V budoucnu, měli byste použít [centrum pro správu Azure AD](https://aad.portal.azure.com) pro všechny vaše založené na portálu pro správu služby Azure AD.
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>Na webovém portálu PhoneFactor byl vyřazen.

**Typ:** zastaralé  
**Kategorie služby:** Azure AD  
**Funkce produktu:** adresáře
 
Od 8. ledna 2018 je vyřazený na webovém portálu PhoneFactor. Byl použit tento portál pro správu MFA serveru, ale tato funkce se přesunuly na web Azure Portal na adrese portal.azure.com. 

Konfigurace vícefaktorového ověřování se nachází v: **Azure Active Directory \> MFA serveru**
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Vyřazení sestav Azure AD

**Typ:** zastaralé  
**Kategorie služby:** generování sestav  
**Funkce produktu:** Správa životního cyklu identit  


Obecná dostupnost nové konzoly Správa služby Azure Active Directory a nová rozhraní API pro aktivity a zabezpečení sestavy, sestava rozhraní API teď k dispozici v části "/ reports" koncový bod byl vyřazen od konce 31. prosince 2017.

**Co je k dispozici?**

V rámci přechodu na Nová konzola pro správu jsme zpřístupnili 2 nová rozhraní API pro načítání protokolů aktivit Azure AD. Nová sada rozhraní API poskytuje širší filtrování a řazení funkce kromě toho, že bohatší auditu a aktivit přihlašování. Dříve dostupné v sestavách zabezpečení dat teď přístupný prostřednictvím události rizika Identity Protection rozhraní API v Microsoft Graphu.

Další informace naleznete v tématu:

- [Začínáme s Azure Active Directory API pro vytváření sestav](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Začínáme s Azure Active Directory Identity Protection a Microsoft Graph](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)

---

## <a name="december-2017"></a>. Prosince 2017

### <a name="terms-of-use-in-the-access-panel"></a>Podmínky použití na přístupovém panelu

**Typ:** nová funkce  
**Kategorie služby:** podmínky použití  
**Funkce produktu:** dodržování předpisů
 
Teď můžete přejít na přístupovém panelu a zobrazit podmínky použití, které jste dříve přijali.

Postupujte následovně:

1. Přejděte [portálu MyApps](https://myapps.microsoft.com)a přihlaste se.

2. V pravém horním rohu vyberte své jméno a pak vyberte **profilu** ze seznamu. 

3. Na vaše **profilu**vyberte **přečíst podmínky použití**. 

4. Nyní můžete přečíst podmínky použití přijmout. 

Další informace najdete v tématu [podmínky služby Azure AD použít funkci (preview)](https://docs.microsoft.com/azure/active-directory/active-directory-tou).
 
---
 
### <a name="new-azure-ad-sign-in-experience"></a>Nové prostředí přihlášení Azure AD

**Typ:** nová funkce  
**Kategorie služby:** Azure AD  
**Funkce produktu:** ověřování uživatelů
 
Azure AD a systému identit účtů Microsoft uživatelská rozhraní byla znovu navržena tak, aby měly jednotný vzhled a chování. Kromě toho na přihlašovací stránku Azure AD shromažďuje uživatelské jméno nejprve následuje přihlašovací údaje, které na druhé obrazovce.

Další informace najdete v tématu [nové prostředí přihlášení Azure AD je teď ve verzi public preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/).
 
---
 
### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Menším počtu výzev přihlášení: O nové "neodhlašovat" prostředí pro přihlašování Azure AD

**Typ:** nová funkce  
**Kategorie služby:** Azure AD  
**Funkce produktu:** ověřování uživatelů
 
**Neodhlašovat** zaškrtávací políčko na přihlašovací stránce služby Azure AD byl nahrazen nového řádku, který se zobrazí po úspěšném ověření. 

Pokud vám odpoví **Ano** tuto výzvu, tato služba poskytuje trvalé obnovovací token. Toto chování je stejné jako když jste vybrali **neodhlašovat** zaškrtávací políčko starému prostředí. Případě federovaných tenantů tato výzva zobrazí po úspěšně ověřit federované službě.

Další informace najdete v tématu [menším počtu výzev přihlášení: nové prostředí "zůstat přihlášení" pro službu Azure AD je ve verzi preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/). 

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Přidat konfiguraci tak, aby vyžadovala podmínky použití, které mají být rozbaleny před přijetím

**Typ:** nová funkce  
**Kategorie služby:** podmínky použití  
**Funkce produktu:** dodržování předpisů
 
Možnost pro správce vyžaduje, aby jejich uživatelé rozbalili podmínky použití před přijetím podmínek.

Vyberte buď **na** nebo **vypnout** požadujete, aby uživatelé rozbalili podmínky použití. **Na** nastavení vyžaduje, aby uživatelé zobrazit podmínky použití před přijetím.

Další informace najdete v tématu [podmínky služby Azure AD použít funkci (preview)](https://docs.microsoft.com/azure/active-directory/active-directory-tou).
 
---

### <a name="scoped-activation-for-eligible-role-assignments"></a>S vymezeným oborem aktivaci přiřazení oprávněné role

**Typ:** nová funkce  
**Kategorie služby:** Privileged Identity Management  
**Funkce produktu:** Privileged Identity Management
 
Vám pomůže s vymezeným oborem aktivace aktivovat přiřazení role oprávněné prostředků Azure s méně autonomie než původní výchozí přiřazení. Příkladem je, pokud je vám přiřazena jako vlastník předplatného ve vašem tenantovi. Díky aktivaci s vymezeným oborem můžete aktivovat role vlastníka až o pěti prostředků obsažených v rámci předplatného (například skupiny prostředků a virtuální počítače). Oborů s aktivací může omezit možnost provedení nežádoucí změny k důležitým prostředkům Azure.

Další informace najdete v tématu [co je Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure).
 
---
 
### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Nové federované aplikace v galerii aplikací Azure AD

**Typ:** nová funkce  
**Kategorie služby:** podnikových aplikací  
**Funkce produktu:** 3. stran integrace

V prosinci 2017 jsme přidali podporu těchto nových aplikacích s federací do naší Galerie aplikací:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager [digitální StoreFront EFI](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [OBRAZE umožňuje](https://go.microsoft.com/fwlink/?linkid=863517), [MOBI](https://go.microsoft.com/fwlink/?linkid=863521), [integrace Azure AD MobileIron](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [jednotné přihlašování SAML pro bambus podle rozlišení GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [Jednotné přihlašování SAML pro Bitbucket podle rozlišení GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, integrace se službou Zenegy Azure AD.

Další informace o aplikacích najdete v tématu [integrace aplikací SaaS pomocí Azure Active Directory](https://aka.ms/appstutorial).

Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete v tématu [uvedení aplikace v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 
 
---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Pracovní postupy schválení pro role adresáře Azure AD

**Typ:** změněné funkce  
**Kategorie služby:** Privileged Identity Management  
**Funkce produktu:** Privileged Identity Management
 
Pracovní postup schválení pro role adresáře Azure AD je obecně dostupná.

Pomocí pracovního postupu schvalování oprávnění role Správci můžete vyžadovat, aby členové role oprávněné žádosti o aktivaci role před použitím privilegovaných rolí. Více uživatelů a skupin může být delegovaná schválení odpovědnosti. Členové role oprávněné dostávat upozornění na dokončení schválení a jejich role není aktivní.

---
 
### <a name="pass-through-authentication-skype-for-business-support"></a>Předávací ověřování: Skype pro firmy podpory

**Typ:** změněné funkce  
**Kategorie služby:** ověřování (přihlášení)  
**Funkce produktu:** ověřování uživatelů

Předávací ověřování nyní podporuje uživatelská přihlášení ke Skypu pro firmy klientské aplikace, které podporují moderní ověřování, který obsahuje online a hybridní topologie. 

Další informace najdete v tématu [Skype pro firmy topologie podporované s moderním ověřováním](https://technet.microsoft.com/library/mt803262.aspx).
 
---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Aktualizace Azure AD Privileged Identity Management pro Azure RBAC (preview)

**Typ:** změněné funkce  
**Kategorie služby:** Privileged Identity Management  
**Funkce produktu:** Privileged Identity Management
 
Při aktualizaci ve verzi public preview z Azure AD Privileged Identity Management (PIM) pro řízení přístupu Azure Role-Based (RBAC) teď můžete:

* Pomocí funkce Just Enough Administration.
* K aktivaci vyžadovat schválení role prostředků.
* Plánovat budoucí aktivace role, která vyžaduje schválení pro Azure AD a role Azure RBAC.
 
Další informace najdete v tématu [Privileged Identity Management pro prostředky Azure (preview)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---
 
## <a name="november-2017"></a>. Listopadu 2017
 
### <a name="access-control-service-retirement"></a>Vyřazení služby Řízení přístupu

**Typ:** plánování změn  
**Kategorie služby:** služby Řízení přístupu  
**Funkce produktu:** služby Řízení přístupu 

V pozdní 2018 se vyřadí z Azure Active Directory Access Control (označované také jako služba Access Control). V příštích týdnech vám poskytneme Další informace, které obsahuje podrobný plán a pokyny k migraci vysoké úrovně. Komentáře na této stránce můžete nechat jakékoli dotazy týkající se služby Access Control a člen týmu odpoví.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Omezit přístup z prohlížeče pro Intune Managed Browser 

**Typ:** plánování změn  
**Kategorie služby:** podmíněného přístupu  
**Funkce produktu:** Identity zabezpečení a ochrany

Můžete omezit přístup z prohlížeče k Office 365 a dalším aplikacím Azure AD připojení cloudu s využitím jako schválenou aplikaci Intune Managed Browser. 

Teď můžete nakonfigurovat následující podmínka pro podmíněný přístup založený na aplikaci:

**Klientské aplikace:** prohlížeče

**Co je vliv změny?**

Použijete-li tento stav je v současné době zablokován přístup. Pokud ve verzi preview je k dispozici, veškerý přístup bude vyžadovat použití aplikace spravovaného prohlížeče. 

Vyhledejte tuto funkci a další informace najdete v nadcházející blogy a vydání poznámek. 

Další informace najdete v tématu [podmíněný přístup ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nové schválené klientské aplikace pro podmíněný přístup na základě aplikace Azure AD

**Typ:** plánování změn  
**Kategorie služby:** podmíněného přístupu  
**Funkce produktu:** Identity zabezpečení a ochrany

Tyto aplikace jsou na seznamu [schválené klientské aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- [Microsoft Staffhubu](https://staffhub.office.com/what-it-is)

Další informace naleznete v tématu:

- [Požadavek schválené klientské aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Na základě aplikace podmíněný přístup Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Podmínky použití podpora více jazyků

**Typ:** nová funkce    
**Kategorie služby:** podmínky použití  
**Funkce produktu:** dodržování předpisů

Správci teď můžou vytvářet nové podmínky použití, které obsahují více dokumentů PDF. Můžete označit tyto dokumenty PDF s odpovídající jazyk. Uživatelům se zobrazí soubor PDF s odpovídající jazyk podle jejích preferencí. Pokud není nalezena žádná shoda, zobrazí se výchozí jazyk.

---
 
### <a name="real-time-password-writeback-client-status"></a>Stav klienta zpětného zápisu hesla v reálném čase

**Typ:** nová funkce  
**Kategorie služby:** samoobslužné resetování hesla  
**Funkce produktu:** ověřování uživatelů

Nyní můžete zkontrolovat stav vaší klientovi zpětného zápisu hesla v místním. Tato možnost je dostupná v **integrace v místním** část [resetování hesla](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) stránky. 

Pokud dojde k problémům s připojením k vašemu klientovi zpětného zápisu v místním, se zobrazí chybová zpráva, která vám nabízí:

- Informace o Proč se nejde připojit k vašemu klientovi zpětného zápisu místní.
- Odkaz na dokumentaci, která vám pomůže při řešení problému. 

Další informace najdete v tématu [integrace v místním](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration).

---

### <a name="azure-ad-app-based-conditional-access"></a>Na základě aplikace podmíněný přístup Azure AD 
 
**Typ:** nová funkce  
**Kategorie služby:** Azure AD  
**Funkce produktu:** Identity zabezpečení a ochrany

Teď můžete omezit přístup k Office 365 a dalším aplikacím cloudu Azure AD připojené k [schválené klientské aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement) , které podporují zásady ochrany aplikací Intune s využitím [podmíněný přístup na základě aplikace Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Zásady ochrany aplikací Intune se používají ke konfiguraci a ochraně dat společnosti na těchto klientských aplikací.

Tím, že zkombinujete [založeného na aplikacích](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) s [na základě zařízení](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) zásady podmíněného přístupu, máte možnost k ochraně dat pro osobní a firemní zařízení.

Následující ovládací prvky a podmínky jsou teď k dispozici pro použití s podmíněným přístupem na základě aplikace:

**Podmínka podporované platformy**

- iOS
- Android

**Stav klienta aplikace**

- Mobilní aplikace a desktopoví klienti

**Řízení přístupu**

- Vyžaduje se klientem schválená aplikace.

Další informace najdete v tématu [podmíněný přístup na základě aplikace Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access).
 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Správa zařízení Azure AD na webu Azure Portal

**Typ:** nová funkce  
**Kategorie služby:** registrace a správa zařízení  
**Funkce produktu:** Identity zabezpečení a ochrany

Nyní můžete najít všechna zařízení připojená ke službě Azure AD a činnosti týkající se zařízení na jednom místě. Je nové prostředí pro správu ke správě identit zařízení a nastavení na portálu Azure portal. V této verzi můžete:

- Zobrazte všechna zařízení, které jsou k dispozici pro podmíněný přístup ve službě Azure AD.
- Zobrazit vlastnosti, které obsahují vaše hybridní Azure zařízení připojených k doméně AD.
- Vyhledání klíče Bitlockeru pro zařízení připojená k AD Azure, spravovat zařízení s Intune a další.
- Správa nastavení zařízení: služby Azure AD.

Další informace najdete v tématu [spravovat zařízení pomocí webu Azure portal](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Podpora pro macOS jako platformu zařízení pro podmíněný přístup Azure AD 

**Typ:** nová funkce    
**Kategorie služby:** podmíněného přístupu  
**Funkce produktu:** Identity zabezpečení a ochrany 

Nyní můžete zahrnout (nebo vyloučit) macOS jako podmínku platformy zařízení ve svojí zásadě podmíněného přístupu Azure AD. Přidání macOS na platformách podporovaných zařízení vám umožňuje:

- **Registrovat a spravovat zařízení s macOS pomocí Intune.** Podobně jako jiné platformy jako iOS a Android, aplikace portálu společnosti je k dispozici pro macOS provést jednotné přihlášení. Nová aplikace portál společnosti pro macOS můžete použít k registraci zařízení v Intune a zaregistrovat ji pomocí služby Azure AD.
- **Ujistěte se, že zařízení s macOS splňovat zásady dodržování předpisů vaší organizace definované v Intune.** V Intune na portálu Azure portal můžete teď můžete nastavit zásady dodržování předpisů pro zařízení s macOS. 
- **Omezení přístupu k aplikacím v Azure AD pro pouze zařízení s macOS kompatibilní.** Vytváření zásad podmíněného přístupu má macOS jako možnost platformy samostatném zařízení. Nyní můžete vytvářet zásady podmíněného přístupu s macOS specifické pro cílové aplikace, nastavte v Azure.

Další informace naleznete v tématu:

- [Vytvoření zásady dodržování předpisů pro zařízení s macOS pomocí Intune](https://aka.ms/macoscompliancepolicy)
- [Podmíněný přístup ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)
 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Sítě rozšíření zásad serveru pro ověřování Azure Multi-Factor Authentication 

**Typ:** nová funkce    
**Kategorie služby:** ověřování službou Multi-Factor Authentication  
**Funkce produktu:** ověřování uživatelů

Rozšíření serveru Network Policy Server pro ověřování Azure Multi-Factor Authentication cloudové možnosti ověřování službou Multi-Factor Authentication přidá do vaší infrastruktury ověřování pomocí svých stávajících serverech. Rozšíření serveru Network Policy Server můžete přidat telefonní hovor, textová zpráva nebo ověřovacích telefonních aplikací do vašeho existujícího toku ověřování. Není nutné instalovat, konfigurovat a Udržovat nové servery. 

Toto rozšíření byla vytvořena pro organizace, které chcete chránit připojení virtuální privátní sítě bez nutnosti nasazovat Azure Multi-Factor Authentication Server. Serveru Network Policy Server, který rozšíření funguje jako adaptér mezi RADIUS a cloudové ověřování Azure Multi-Factor Authentication, aby zajišťoval druhý faktor ověřování pro federované nebo synchronizovaných uživatelů.

Další informace najdete v tématu [integrace vaší stávající infrastruktury serveru Network Policy Server Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension).
 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Obnovení nebo trvale odebrat odstraněného uživatele

**Typ:** nová funkce    
**Kategorie služby:** Správa uživatelů  
**Funkce produktu:** adresáře 

V Centru pro správu Azure AD teď můžete:

- Obnovení odstraněného uživatele. 
- Trvale odstraníte uživatele.

**Vyzkoušejte si to:**

1. V Centru pro správu Azure AD, vyberte [všichni uživatelé](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) v **spravovat** oddílu. 

2. Z **zobrazit** seznamu vyberte **nedávno odstraněných uživatelů**. 

3. Vyberte jeden nebo více naposledy odstranění uživatelé a potom je obnovit buď nebo je trvale odstranit.
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nové schválené klientské aplikace pro podmíněný přístup na základě aplikace Azure AD
 
**Typ:** změněné funkce  
**Kategorie služby:** podmíněného přístupu  
**Funkce produktu:** Identity zabezpečení a ochrany

Tyto aplikace byly přidány do seznamu [schválené klientské aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- Aplikace Microsoft Planner
- Azure Information Protection 

Další informace naleznete v tématu:

- [Požadavek schválené klientské aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Na základě aplikace podmíněný přístup Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Použití "Nebo" mezi ovládacími prvky v zásadách podmíněného přístupu 

**Typ:** změněné funkce    
**Kategorie služby:** podmíněného přístupu  
**Funkce produktu:** Identity zabezpečení a ochrany
 
Teď můžete použít "nebo" (vyžadovat jeden z vybraných ovládacích prvků) pro řízení podmíněného přístupu. Tato funkce slouží k vytváření zásad s "nebo" mezi řízení přístupu. Například můžete použít tuto funkci můžete vytvořit zásadu, která vyžaduje uživatele k přihlášení pomocí služby Multi-Factor Authentication "nebo" na vyhovující zařízení.

Další informace najdete v tématu [ovládacích prvků v podmíněný přístup Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls).
 
---

### <a name="aggregation-of-real-time-risk-events"></a>Agregace v reálném čase rizikových událostí

**Typ:** změněné funkce    
**Kategorie služby:** Identity protection  
**Funkce produktu:** Identity zabezpečení a ochrany

Ve službě Azure AD Identity Protection se teď všechny v reálném čase rizikové události, které pocházely ze stejné IP adresy v daném dni agregují pro každý typ rizikové události. Tato změna omezení objemu rizikové události zobrazeny bez jakékoli změny v zabezpečení uživatele.

Základní detekce v reálném čase funguje pokaždé, když uživatel přihlásí. Pokud máte zásady rizik přihlašování zabezpečení nastavit tak, aby ověřování službou Multi-Factor Authentication nebo blokování přístupu, se stále aktivují během každé rizikových přihlášení.
 
---
 
## <a name="october-2017"></a>Říjen 2017

### <a name="deprecate-azure-ad-reports"></a>Vyřazení sestav Azure AD

**Typ:** plánování změn  
**Kategorie služby:** generování sestav  
**Funkce produktu:** Správa životního cyklu identit  

Na webu Azure portal vám nabízí:

- Nová konzola pro správu Azure AD.
- Nová rozhraní API pro sestavy aktivit a zabezpečení.
 
Díky těmto novým funkcím byly dostupné sestavy rozhraní API v rámci koncový bod/Reports jenom do 10. prosince 2017. 

---

### <a name="automatic-sign-in-field-detection"></a>Zjišťování pole Automatické přihlášení

**Typ:** oprava   
**Kategorie služby:** Moje aplikace  
**Funkce produktu:** jednotného přihlašování  

Azure AD podporuje zjišťování pole automatické přihlašování pro aplikace, které vykreslují pole HTML uživatelské jméno a heslo. Tyto kroky jsou popsané v [automaticky zachycení pole přihlášení pro aplikaci](https://docs.microsoft.com/azure/active-directory/application-config-sso-problem-configure-password-sso-non-gallery#how-to-manually-capture-sign-in-fields-for-an-application). Tuto možnost můžete najít tak, že přidáte *mimo galerii* aplikace na **podnikové aplikace** stránku [webu Azure portal](http://aad.portal.azure.com). Kromě toho můžete nakonfigurovat **Single Sign-on** režimu zobrazení na tuto novou aplikaci do **založené na heslech Single Sign-on**, zadejte adresu URL webu a potom uložte na stránce.
 
Kvůli problému služba byla tato funkce dočasně zakázané. Tento problém byl vyřešen a zjišťování pole Automatické přihlášení je opět k dispozici.

---

### <a name="new-multi-factor-authentication-features"></a>Nové funkce služby Multi-Factor Authentication

**Typ:** nová funkce  
**Kategorie služby:** ověřování službou Multi-Factor Authentication  
**Funkce produktu:** Identity zabezpečení a ochrany  

Vícefaktorové ověřování (MFA) je důležitou součástí ochrany vaší organizace. Chcete-li více adaptive přihlašovací údaje a prostředí pohodlnější, byly přidány následující funkce: 

- Výsledky výzvu služby Multi-Factor Authentication jsou integrované přímo do sestavy Azure AD přihlášení, která zahrnuje programový přístup k výsledkům vícefaktorové ověřování.
- Konfigurace MFA je těsněji integrované do služby Azure AD konfigurace prostředí na webu Azure Portal.

V této verzi public preview jsou nedílnou součást prostředí základní konfigurace Azure AD MFA správu a vytváření sestav. Nyní můžete spravovat funkce portálu správy vícefaktorové ověřování v rámci prostředí Azure AD.

Další informace najdete v tématu [Reference o vytváření sestav vícefaktorového ověřování na webu Azure Portal](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa). 

---

### <a name="terms-of-use"></a>Podmínky použití

**Typ:** nová funkce  
**Kategorie služby:** podmínky použití  
**Funkce produktu:** dodržování předpisů  

Můžete použít Azure AD podmínek použití zobrazíte informace, jako je příslušná právní omezení pro zákonné požadavky na uživatele.

Podmínky použití služby Azure AD můžete použít v následujících scénářích:

- Obecné podmínky použití pro všechny uživatele ve vaší organizaci
- Konkrétní podmínky použití založené na atributech uživatelů (například lékařů vs. sestry) nebo domácích a mezinárodních zaměstnanců, provádí dynamické skupiny
- Konkrétní podmínky použití pro přístup k vysoce účinné obchodní aplikace, jako je Salesforce

Další informace najdete v tématu [podmínek použití služby Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-tou).

---

### <a name="enhancements-to-privileged-identity-management"></a>Vylepšení Privileged Identity Management

**Typ:** nová funkce  
**Kategorie služby:** Privileged Identity Management  
**Funkce produktu:** Privileged Identity Management  

S Azure AD Privileged Identity Management můžete spravovat, řídit a monitorovat přístup k prostředkům Azure (preview) v rámci vaší organizace:

- Předplatná
- Skupiny prostředků
- Virtuální počítače 

Všechny prostředky v rámci webu Azure portal, které používají funkci Azure RBAC můžete využít výhod zabezpečení a možnosti správy životního cyklu, které Azure AD Privileged Identity Management může nabídnout.

Další informace najdete v tématu [Privileged Identity Management pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

### <a name="access-reviews"></a>Kontroly přístupu

**Typ:** nová funkce  
**Kategorie služby:** kontroly přístupu  
**Funkce produktu:** dodržování předpisů  

Organizace můžete efektivně spravovat členství ve skupinách a přístup k podnikovým aplikacím použít kontroly přístupu (preview): 

- Můžete znovu certifikovat přístup uživatelů typu host pomocí kontrol jejich přístupu k aplikacím a jejich členství ve skupinách. Recenzenti můžete efektivně rozhodnout, zda povolit hosté nadále přístup na základě informací poskytnutých kontroly přístupu.
- Pomocí kontrol přístupu je možné provést opětovnou certifikaci přístupu k aplikacím a členství ve skupinách i pro zaměstnance.

Kontroly přístupu lze shromažďovat v programech sledujících dodržování předpisů a přístup k citlivým aplikacím v rámci organizace.

Další informace najdete v tématu [kontroly přístupu Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview).

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Skrytí aplikace třetí strany z Moje aplikace a Spouštěči aplikací Office 365

**Typ:** nová funkce  
**Kategorie služby:** Moje aplikace  
**Funkce produktu:** jednotného přihlašování  

Nyní můžete lépe spravovat aplikace, které se zobrazí na portálech pro vaše uživatele pomocí nového **skrýt aplikace** vlastnost. Můžete skrýt aplikacemi a pomáhají v případech, kdy dlaždic aplikací zobrazí pro back endovým službám nebo duplicitní dlaždice a Spouštěč nepořádku uživatelé aplikaci oken. Tento přepínač je v **vlastnosti** část aplikace třetí strany a je označená **viditelné pro uživatele?** Také můžete skrýt aplikace prostřednictvím kódu programu přes PowerShell. 

Další informace najdete v tématu [skrytí aplikace třetí strany z uživatelského prostředí ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app). 


**Co je k dispozici?**

 Jako součást přechodu na Nová konzola pro správu, dvě nová rozhraní API pro načítání aktivit Azure AD jsou k dispozici protokoly. Nová sada rozhraní API poskytuje širší filtrování a řazení funkce kromě toho, že bohatší auditu a aktivit přihlašování. Dříve dostupné v sestavách zabezpečení nyní dat přístupné prostřednictvím rozhraní Identity Protection riziko události API v Microsoft Graphu.


## <a name="september-2017"></a>Září 2017

### <a name="hotfix-for-identity-manager"></a>Oprava hotfix pro Identity Manager

**Typ:** změněné funkce  
**Kategorie služby:** Identity Manageru  
**Funkce produktu:** Správa životního cyklu identit  

Balíček kumulativní oprava hotfix (build 4.4.1642.0) je k dispozici od 25. září 2017, pro Identity Manager 2016 Service Pack 1. Tento balíček Shrnutí:

- Řeší problémy a přidává vylepšení.
- Je kumulativní aktualizace, která nahradí všechny aktualizace Identity Manager 2016 Service Pack 1 až po sestavení 4.4.1459.0 pro Identity Manager 2016. 
- Je potřeba mít sestavení 4.4.1302.0 Identity Manager 2016. 

Další informace najdete v tématu [balíčku opravy Hotfix rollup (build 4.4.1642.0) je k dispozici pro Identity Manager 2016 Service Pack 1](https://support.microsoft.com/help/4021562). 

---