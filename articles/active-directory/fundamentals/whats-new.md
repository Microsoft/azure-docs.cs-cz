---
title: Co je nového? Poznámky – Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, co je nové službě Azure Active Directory, jako je nejnovější poznámky k verzi, známé problémy, opravy chyb, zastaralé funkce a nadcházející změny.
services: active-directory
author: msmimart
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cf12c28dc04d0dbb8a680d45c8d8f5f5faa2d82
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78377719"
---
# <a name="whats-new-in-azure-active-directory"></a>Co je nového ve službě Azure Active Directory?

>Přečtěte si informace o tom, kdy se tato stránka na aktualizace znovu navštíví zkopírováním a vložením této adresy URL: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` do ![ikona čtečky informačního kanálu RSS](./media/whats-new/feed-icon-16x16.png) čtečku informačního kanálu.

Azure AD obdrží vylepšení průběžně. V tomto článku najdete informace o tom, jak se můžete seznámit s nejnovějším vývojem.

- Nejnovější vydané verzi
- Známé problémy
- Opravy chyb
- Zastaralé funkce
- Plány pro změny

Tato stránka se aktualizuje každý měsíc, takže návštěvě pravidelně. Pokud hledáte položky, které jsou starší než šest měsíců, najdete je v archivu, abyste mohli [novinky v Azure Active Directory](whats-new-archive.md).

---

## <a name="february-2020"></a>Únor 2020
 
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
 
V únoru 2020 jsme přidali tyto 31 nových aplikací s podporou federace do Galerie aplikací: 

[IamIP patentová platforma](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial), [cloudová](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial)služba [ns1 SSO pro Azure](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial), [Barracuda e-mailové služby zabezpečení](https://ess.barracudanetworks.com/sso/azure), [vytváření sestav ABA](https://myaba.co.uk/client-access/signin/auth/msad), [v případě řešení krizí – online](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial), správce programu BIC pro [Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial), [včelařské datové konektory Azure AD](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial), funkce [Korn trajekty](https://www.kornferry.com/solutions/kf-digital/kf-assess), [](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial) [Verkada](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial) [, Splashtop,](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial) [správce lístků](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial) [](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial) [](https://admin.thulium.com/login/instance) [Šablona Výběr pro týmy](https://links.officeatwork.com/templatechooser-download-teams), [včely](https://www.beesy.me/index.php/site/login), [systém podpory healthu](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial), [MURAL](https://app.mural.co/signup), [podregistr](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial), [LavaDo](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview), [Wakelet](https://wakelet.com/login), [Firmex VDR](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial), [ThingLink pro učitele a školy](https://www.thinglink.com/), [Coda](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial), [NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product), [WEDO](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial), [InvitePeople](https://invitepeople.com/login), [REPRINTER – článek Galaxy](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial), [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial)

 
Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Nové zřizovací konektory v galerii aplikací Azure AD – únor 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
Nyní můžete automatizovat vytváření, aktualizaci a odstraňování uživatelských účtů pro tyto nově integrované aplikace:

- [Mixpanelu](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud podle Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
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
 
Činnost koncového uživatele nového účtu se aktualizuje jeho adresa URL na https://myaccount.microsoft.com v příštím měsíci. Další informace o zkušenostech a všech samoobslužných funkcích pro účty, které nabízí koncovým uživatelům, najdete v [nápovědě k portálu Můj účet](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview).

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
 
Pracovní prostory můžou správci filtrů nakonfigurovat k uspořádání svých uživatelských aplikací, budou se teď označovat jako kolekce. Přečtěte si další informace o tom, jak je nakonfigurovat při [vytváření kolekcí na portálu moje aplikace](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).

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
- [Zscaler privátní přístup](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

Další informace o tom, jak lépe zabezpečit vaši organizaci pomocí automatizovaného zřizování uživatelských účtů, najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Nové federované aplikace dostupné v Aplikace Azure AD galerii – leden 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
Do ledna 2020 jsme do Galerie aplikací přidali tyto 33 nové aplikace s podporou federace: 

[JOSA](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial), [Fast Edge Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial), [terraformu Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial), [Spintr SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial), [Abibot Netlogistik](https://1030-review-develop-3zknud.netlogistik.com/), [SkyKick Cloud Backup pro Office 365](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access), pro, [LeaveBot](https://leavebot.io/#home), [DataCamp](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial), [TripActions](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial), [SMARTWORK](https://www.intumit.com/english/SmartWork.html), [DOTCOM-monitor](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial), [SSOGEN – Azure AD SSO Gateway pro Oracle E-Business Suite-EBS, PeopleSoft spouštěných místně a jde](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial), [hostované MyCirqa SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial), [yuhu pro správu vlastností](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial) [, LumApps](https://sites.lumapps.com/login), [](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial) [Working Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial), [Talentsoft](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial), [SmartDB pro Microsoft Teams](http://teams.smartdb.jp/login/), [PressPage](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial), [ContractSafe typu Saml2 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial), [Maxient software správce](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial), [helpshift](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial), [PortalTalk 365](https://www.portaltalk.com/), [korecenze](https://portal.coreview.com/), [squelch Cloud Office 365 Connector](https://laxmi.squelch.io/login), [PingFlow Authentication](https://app-staging.pingview.io/), PrinterLogic [SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial), [Taskize Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial), [Sandwai](https://app.sandwai.com/), [EZRentOut](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial), [AssetSonar](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial), [Akari Virtuální asistent](https://akari.io/akari-virtual-assistant/)

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

---

## <a name="december-2019"></a>Prosinec 2019

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>Integrace SAP SuccessFactors zřizování do Azure AD a místní služby AD (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** Správa životního cyklu identit

V Azure AD teď můžete integrovat SAP SuccessFactors jako autoritativní zdroj identity. Tato integrace vám pomůže automatizovat ucelený životní cyklus identity, včetně použití událostí založených na standardech HR, jako jsou nová přijetí nebo ukončení, pro řízení zřizování účtů Azure AD.

Další informace o tom, jak nastavit příchozí zřizování SAP SuccessFactors do služby Azure AD, najdete v kurzu [konfigurace SAP SuccessFactors pro Automatické zřizování](https://aka.ms/SAPSuccessFactorsInboundTutorial) .

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Podpora přizpůsobených e-mailů v Azure AD B2C (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C

Teď můžete pomocí Azure AD B2C vytvářet přizpůsobené e-maily, když se uživatelé přihlásí k používání svých aplikací. Pomocí DisplayControls (aktuálně ve verzi Preview) a poskytovatele e-mailu jiného výrobce (například, [SendGrid](https://sendgrid.com/), [SparkPost](https://sparkpost.com/)nebo vlastní REST API) můžete použít vlastní e-mailovou šablonu **, adresu a** text předmětu a také lokalizaci a vlastní nastavení jednorázového hesla.

Další informace najdete v tématu [ověření vlastního e-mailu v Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-email).

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>Nahrazení standardních zásad výchozími hodnotami zabezpečení

**Zadejte:** Změněná funkce  
**Kategorie služby:** Jiná  
**Schopnost produktu:** Zabezpečení identity a ochrana

Jako součást zabezpečeného modelu pro ověřování odebíráme stávající zásady ochrany základní úrovně ze všech tenantů. Na toto odstranění cílí na dokončení února. Náhrada za tyto zásady ochrany základní úrovně je výchozí nastavení zabezpečení. Pokud používáte zásady základní ochrany, musíte se přesunout na nové zásady výchozího nastavení zabezpečení nebo na podmíněný přístup. Pokud jste tyto zásady nepoužili, nemusíte provádět žádnou akci.

Další informace o novém výchozím nastavení zabezpečení najdete v tématu [co jsou výchozí hodnoty zabezpečení?](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) Další informace o zásadách podmíněného přístupu najdete v tématu [běžné zásady podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common).

---

## <a name="november-2019"></a>Listopad 2019

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>Podpora pro atribut SameSite a Chrome 80

**Zadejte:** Plánování změn  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů

V rámci zabezpečeného modelu pro soubory cookie se v prohlížeči Chrome 80 mění způsob, jakým zpracovává soubory cookie bez atributu `SameSite`. Každý soubor cookie, který neurčuje atribut `SameSite`, bude zpracován, jako by byl nastaven na hodnotu `SameSite=Lax`, což bude mít za následek blokování určitých scénářů sdílení souborů cookie mezi doménami, na kterých může být vaše aplikace závislá. Chcete-li zachovat starší chování aplikace Chrome, můžete použít atribut `SameSite=None` a přidat další atribut `Secure`, aby soubory cookie pro více webů byly dostupné pouze prostřednictvím připojení HTTPS. Pro Chrome je naplánovaná Tato změna od 4. února 2020.

Doporučujeme, aby naši vývojáři otestovali své aplikace pomocí těchto pokynů:

- Nastavte výchozí hodnotu nastavení **použít zabezpečený soubor cookie** na **Ano**.

- Nastavte výchozí hodnotu atributu **SameSite** na **none**.

- Přidejte další atribut `SameSite` **zabezpečení**.

Další informace najdete v tématu [nadcházející změny souborů cookie SameSite v ASP.NET a ASP.NET Core](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) a [potenciální narušení na webech zákazníků a produktech a službách Microsoftu v Chrome verze 79 a novějších](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79).

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Nová oprava hotfix pro Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2)

**Zadejte:** Určí  
**Kategorie služby:** Microsoft Identity Manager  
**Schopnost produktu:** Správa životního cyklu identit

Kumulativní balíček oprav hotfix (Build 4.6.34.0) je k dispozici pro Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Tento kumulativní balíček řeší problémy a přidává vylepšení, která jsou popsána v části "vyřešené problémy a vylepšení v této aktualizaci".

Další informace a stažení balíčku oprav hotfix najdete v tématu [kumulativní aktualizace Microsoft Identity Manager 2016 Service Pack 2 (Build 4.6.34.0)](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r).

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>Nová sestava aktivity aplikace AD FS, která vám umožní migrovat aplikace do Azure AD (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ

Pomocí nové sestavy aktivity aplikace Active Directory Federation Services (AD FS) (AD FS) v Azure Portal můžete určit, které z vašich aplikací se můžou migrovat do služby Azure AD. Tato sestava posuzuje všechny aplikace AD FS kvůli kompatibilitě s Azure AD, kontroluje případné problémy a poskytuje pokyny k přípravě jednotlivých aplikací pro migraci.

Další informace najdete v tématu [použití sestavy aktivita aplikace AD FS k migraci aplikací do služby Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity).

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>Nový pracovní postup pro uživatele, kteří požadují souhlas správce (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** Access Control

Nový pracovní postup pro vyjádření souhlasu správce poskytuje správcům způsob, jak udělit přístup k aplikacím, které vyžadují schválení správcem. Pokud se uživatel pokusí o přístup k aplikaci, ale není schopen vyjádřit souhlas, může nyní odeslat žádost o schválení správcem. Požadavek se odešle e-mailem a umístí se do fronty, která je přístupná ze Azure Portal, všem správcům, kteří byli určeni jako kontroloři. Až kontrolor provede akci na nevyřízené žádosti, žádající uživatelé se o akci informují.

Další informace najdete v tématu [Konfigurace pracovního postupu pro vyjádření souhlasu správce (Preview)](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow).

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>Nové možnosti konfigurace tokenu pro registraci Aplikace Azure AD pro správu volitelných deklarací identity (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Jiná  
**Schopnost produktu:** Vývojářské prostředí

Nové okno **Konfigurace tokenu registrací aplikace Azure AD** v Azure Portal nyní zobrazuje vývojářům aplikací dynamický seznam volitelných deklarací identity pro své aplikace. Toto nové prostředí pomáhá zjednodušit migrace aplikací Azure AD a minimalizovat volitelné neškodné konfigurace deklarací identity.

Další informace najdete v tématu [poskytnutí volitelných deklarací identity vaší aplikaci Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Nový pracovní postup schvalování ve dvou fázích ve správě nároků ve službě Azure AD (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Jiná  
**Schopnost produktu:** Správa nároků

Představili jsme nový pracovní postup schvalování ve dvou fázích, který vám umožní vyžadovat od dvou schvalovatelů schválení požadavku uživatele na balíček přístupu. Můžete ji například nastavit tak, aby si správce žádajícího uživatele musel nejdřív schválit a potom můžete také vyžadovat schválení vlastníka prostředku. Pokud některý z schvalovatelů neschválí, přístup se neudělí.

Další informace najdete v tématu [Nastavení žádostí o změnu požadavků a schvalování balíčku pro přístup ve správě nároků ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-request-policy).

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Aktualizace stránky Moje aplikace spolu s novými pracovními prostory (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Moje aplikace  
**Schopnost produktu:** integrace třetích stran

Teď můžete přizpůsobit způsob zobrazení uživatelů vaší organizace a přístup k obnovenému prostředí moje aplikace. Toto nové prostředí obsahuje také funkci nové pracovní prostory, která uživatelům usnadňuje hledání a organizování aplikací.

Další informace o novém prostředí moje aplikace a vytváření pracovních prostorů najdete v tématu [vytvoření pracovních prostorů na portálu moje aplikace](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces).

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Podpora sociálních ID Google pro spolupráci Azure AD B2B (Obecná dostupnost)

**Zadejte:** Nová funkce  
**Kategorie služby:** B2B  
**Schopnost produktu:** Ověřování uživatelů

Nová podpora pro používání sociálních ID Google (účty Gmail) ve službě Azure AD pomáhá zjednodušit spolupráci vašim uživatelům a partnerům. Už není potřeba, aby partneři vytvářeli a spravovali nový účet specifický pro společnost Microsoft. Microsoft Teams teď plně podporuje uživatele Google na všech klientech a v rámci běžných koncových bodů ověřování a klientů.

Další informace najdete v tématu [Přidání Google jako zprostředkovatele identity pro uživatele typu Host B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Podpora Microsoft Edge Mobile pro podmíněný přístup a jednotné přihlašování (obecně dostupné)

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity & ochrana

Azure AD pro Microsoft Edge v iOS a Androidu teď podporuje jednotné přihlašování a podmíněný přístup Azure AD:

- **Jednotné přihlašování Microsoft Edge (SSO):** Pro všechny aplikace připojené k Azure AD je teď k dispozici jednotné přihlašování v rámci nativních klientů (například Microsoft Outlook a Microsoft Edge).

- **Podmíněný přístup Microsoft Edge:** Pomocí zásad podmíněného přístupu na základě aplikace musí vaši uživatelé používat prohlížeče chráněné Microsoft Intune, jako je Microsoft Edge.

Další informace o podmíněném přístupu a jednotném přihlašování s Microsoft Edgem najdete v článku [Podpora Microsoft Edge Mobile pro podmíněný přístup a jednotné přihlašování je teď všeobecně dostupný](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179) Blogový příspěvek. Další informace o tom, jak nastavit klientské aplikace pomocí [podmíněného přístupu založeného na aplikaci](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) nebo [podmíněného přístupu na základě zařízení](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices), najdete v tématu [Správa webového přístupu pomocí Microsoft Intune prohlížeče chráněného zásadami](https://docs.microsoft.com/intune/apps/app-configuration-managed-browser).

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Správa nároků Azure AD (Obecná dostupnost)

**Zadejte:** Nová funkce  
**Kategorie služby:** Jiná  
**Schopnost produktu:** Správa nároků

Azure AD – Správa nároků je nová funkce zásad správného řízení identity, která organizacím pomáhá spravovat životní cyklus identit a přístupu ve velkém měřítku. Tato nová funkce pomáhá automatizovat pracovní postupy žádostí o přístup, přiřazení přístupu, recenze a vypršení platnosti napříč skupinami, aplikacemi a weby SharePointu Online.

Díky správě nároků Azure AD můžete efektivněji spravovat přístup pro zaměstnance i pro uživatele mimo vaši organizaci, kteří potřebují přístup k těmto prostředkům.

Další informace najdete v tématu [co je Správa nároků na Azure AD?](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview#license-requirements)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizace zřizování uživatelských účtů pro tyto nově podporované aplikace SaaS

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran  

Nyní můžete automatizovat vytváření, aktualizaci a odstraňování uživatelských účtů pro tyto nově integrované aplikace:

[Služba SAP Cloud Platform identity Authentication Service](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-provisioning-tutorial), [SpaceIQ](https://docs.microsoft.com/azure/active-directory/saas-apps/spaceiq-provisioning-tutorial), [Miro](https://docs.microsoft.com/azure/active-directory/saas-apps/miro-provisioning-tutorial), [Cloudgate](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial), [info CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloudsuite-provisioning-tutorial), [OfficeSpace Software](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-software-provisioning-tutorial), [prioritní matice](https://docs.microsoft.com/azure/active-directory/saas-apps/priority-matrix-provisioning-tutorial)

Další informace o tom, jak lépe zabezpečit vaši organizaci pomocí automatizovaného zřizování uživatelských účtů, najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Nové federované aplikace dostupné v Aplikace Azure AD galerii – listopad 2019

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran

V listopadu 2019 jsme do Galerie aplikací přidali tyto 21 nových aplikací s podporou federace:

[](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial) [HootSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial), [Blue Access for Members (BAM)](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial), [bitly](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial), [Riva](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial), [ResLife Portal](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), [NegometrixPortal Single Signing (SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial), [TeamsChamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279), [Motus](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial), [MyAryaka](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial), [BlueMail](https://loginself1.bluemail.me/), [Beedle](https://teams-web.beedle.co/#/), [Visma](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial), [OneDesk](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial), [FOKO Retail](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial), Qmarkets [nápad & Správa inovací](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial), [Netskope ověřování uživatelů](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial), uniFLOW [online](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial), [Claromentis](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial) , [JISC student Voter Registration](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial), [e4enable](https://portal.e4enable.com/)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>Nová a vylepšená Galerie aplikací Azure AD

**Zadejte:** Změněná funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ

Aktualizovali jsme galerii aplikací Azure AD, abychom vám usnadnili hledání předem integrovaných aplikací, které podporují zřizování, OpenID připojení a SAML ve vašem tenantovi Azure Active Directory.

Další informace najdete v tématu [Přidání aplikace do tenanta Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal).

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>Zvýšení limitu délky definice role aplikace z 120 na 240 znaků

**Zadejte:** Změněná funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ

Od zákazníků jsem slyšeli, že limit délky pro hodnotu definice role aplikace v některých aplikacích a službách je moc krátký v 120 znacích. V reakci jsme zvýšili maximální délku definice hodnoty role na 240 znaků.

Další informace o použití definic rolí specifických pro aplikaci najdete v tématu [Přidání rolí aplikace v aplikaci a jejich přijetí v tokenu](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps).

---

## <a name="october-2019"></a>Říjen 2019

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Vyřazení rozhraní API identityRiskEvent pro detekci rizik Azure AD Identity Protection  

**Zadejte:** Plánování změn  
**Kategorie služby:** Ochrana identity  
**Schopnost produktu:** Zabezpečení identity & ochrana

V reakci na zpětnou vazbu od vývojářů teď můžou předplatitelé Azure AD Premium P2 provádět komplexní dotazy na data o detekci rizika Azure AD Identity Protection pomocí nového rozhraní API riskDetection pro Microsoft Graph. Stávající beta verze rozhraní [identityRiskEvent](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) API přestane vracet data z **10. ledna 2020**. Pokud vaše organizace používá rozhraní identityRiskEvent API, měli byste přejít na nové rozhraní riskDetection API.

Další informace o novém rozhraní riskDetection API najdete v [referenční dokumentaci k rozhraní API pro detekci rizik](https://aka.ms/RiskDetectionsAPI).

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>Podpora proxy aplikací pro atribut SameSite a Chrome 80

**Zadejte:** Plánování změn  
**Kategorie služby:** Proxy aplikace  
**Schopnost produktu:** Access Control

Několik týdnů před vydáním prohlížeče Chrome 80, plánujeme aktualizovat způsob, jakým soubory cookie proxy aplikace zacházejí s atributem **SameSite** . S vydáním Chrome 80 se všechny soubory cookie, které nespecifikují atribut **SameSite** , považují za, i když byly nastavené na `SameSite=Lax`.

Abychom se vyhnuli potenciálně negativním dopadům z důvodu této změny, aktualizujeme přístup k proxy aplikací a soubory cookie relace pomocí:

- Nastavení výchozí hodnoty pro nastavení **použít zabezpečený soubor cookie** na **Ano**.

- Nastavení výchozí hodnoty pro atribut **SameSite** na **hodnotu None**.

    >[!NOTE]
    > Soubory cookie přístupu k proxy aplikacím se vždycky přenesly výhradně přes zabezpečené kanály. Tyto změny se vztahují pouze na soubory cookie relace.

Další informace o nastavení souborů cookie proxy aplikace najdete v tématu [nastavení souborů cookie pro přístup k místním aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="app-registrations-legacy-and-converged-app-management-from-the-application-registration-portal-appsdevmicrosoftcom-will-no-longer-be-available"></a>Registrace aplikací (starší verze) a sblížená Správa aplikací z portálu pro registraci aplikací (apps.dev.microsoft.com) již nebudou k dispozici.

**Zadejte:** Plánování změn  
**Kategorie služby:** NENÍ K DISPOZICI  
**Schopnost produktu:** Vývojářské prostředí

V blízké budoucnosti již uživatelé s účty Azure AD nebudou moci registrovat a spravovat sblížené aplikace pomocí portálu pro registraci aplikací (apps.dev.microsoft.com) nebo registrovat a spravovat aplikace v Registrace aplikací (starší verze). prostředí Azure Portal.

Další informace o novém prostředí Registrace aplikací najdete v [Registrace aplikací v průvodci školením Azure Portal](../develop/app-registrations-training-guide-for-app-registrations-legacy-users.md).

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>Pro uživatele, kteří se při migraci z MFA na podmíněný přístup využívají, už se nevyžadují, aby se zaregistrovali znovu.

**Zadejte:** Určí  
**Kategorie služby:** VÍCEFAKTOROVÉHO  
**Schopnost produktu:** Zabezpečení identity & ochrana

Opravili jsme známý problém, kdy se uživatelé museli znovu zaregistrovat, pokud byli zakázáni pro jednotlivé uživatele Multi-Factor Authentication (MFA) a pak povolili MFA prostřednictvím zásad podmíněného přístupu.

Pokud chcete, aby se uživatelé znovu zaregistrovali, můžete z metod ověřování uživatele na portálu Azure AD vybrat **požadovanou možnost znovu zaregistrovat možnost MFA** . Další informace o migraci uživatelů z MFA na ověřování MFA na základě podmíněného přístupu najdete v tématu [převod uživatelů z MFA na ověřování MFA na základě vícefaktorového přístupu](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted#convert-users-from-per-user-mfa-to-conditional-access-based-mfa).

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Nové funkce pro transformaci a odeslání deklarací identity ve vašem tokenu SAML

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ

Přidali jsme další možnosti, které vám pomůžou přizpůsobit a poslat deklarace identity ve vašem tokenu SAML. Mezi tyto nové funkce patří:

- Další funkce pro transformaci deklarací identity, které vám pomůžou upravit hodnotu, kterou odešlete v deklaraci identity.

- Možnost použít více transformací na jednu deklaraci identity.

- Možnost zadat zdroj deklarací identity na základě typu uživatele a skupiny, do které uživatel patří

Podrobné informace o těchto nových funkcích, včetně způsobu jejich použití, najdete v tématu [přizpůsobení deklarací identity vystavených v tokenu SAML pro podnikové aplikace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Nová stránka moje přihlášení pro koncové uživatele v Azure AD

**Zadejte:** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Monitorování & vytváření sestav

Přidali jsme novou stránku **Moje přihlášení** (https://mysignins.microsoft.com), aby si uživatelé vaší organizace mohli zobrazit poslední historii přihlášení, aby zkontrolovali, že neobvyklá aktivita. Tato nová stránka umožňuje uživatelům zobrazit:

- Pokud se někdo pokusí uhodnout svoje heslo.

- Pokud se útočník úspěšně přihlásil ke svému účtu a z kterého umístění.

- K jakým aplikacím se útočník pokusil získat přístup.

Další informace najdete v tématu [Uživatelé teď můžou zkontrolovat historii přihlášení pro neobvyklý blog aktivity](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066) .

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Migrace Azure AD Domain Services (Azure služba AD DS) z klasických do Azure Resource Manager virtuálních sítí

**Zadejte:** Nová funkce  
**Kategorie služby:** Azure AD Domain Services  
**Schopnost produktu:** Azure AD Domain Services

Našim zákazníkům, kteří se zablokovali na klasických virtuálních sítích – máme skvělé novinky. Nyní můžete provést jednorázovou migraci z klasické virtuální sítě do existující Správce prostředků virtuální sítě. Po přesunu do Správce prostředků virtuální sítě budete moci využít výhod dalších a upgradovaných funkcí, jako jsou podrobné zásady pro hesla, e-mailová oznámení a protokoly auditu.

Další informace najdete v tématu [verze Preview – migrace Azure AD Domain Services z modelu klasických virtuálních sítí do Správce prostředků](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet).

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Aktualizace rozložení kontraktu stránky Azure AD B2C

**Zadejte:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C

Zavedli jsme některé nové změny verze 1.2.0 kontraktu stránky pro Azure AD B2C. V této aktualizované verzi teď můžete řídit pořadí načítání pro vaše prvky, což může také zabránit blikání, ke kterému dojde, když je načtena šablona stylů (CSS).

Úplný seznam změn provedených u kontraktu stránky najdete v [protokolu změn verze](https://docs.microsoft.com/azure/active-directory-b2c/page-layout#120).

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Aktualizace stránky Moje aplikace spolu s novými pracovními prostory (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Moje aplikace  
**Schopnost produktu:** Access Control

Nyní můžete přizpůsobit způsob, jakým uživatelé vaší organizace zobrazují a přistupují ke značce – nové prostředí moje aplikace, včetně použití funkce nové pracovní prostory, aby je bylo snazší najít. Funkce New Workspaces funguje jako filtr pro aplikace, ke kterým mají uživatelé vaší organizace přístup.

Další informace o tom, jak vytvářet nové prostředí moje aplikace a vytvářet pracovní prostory, najdete v tématu [vytvoření pracovních prostorů na portálu moje aplikace (Preview)](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces).

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>Podpora měsíčního modelu fakturace založeného na aktivních uživatelích (Obecná dostupnost)

**Zadejte:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C

Azure AD B2C teď podporuje fakturaci měsíčně aktivních uživatelů (MAU). Faktura MAU vychází z počtu jedinečných uživatelů s aktivitou ověřování v průběhu kalendářního měsíce. Stávající zákazníci můžou kdykoli přejít na tuto novou metodu fakturace.

Od 1. listopadu 2019 se budou všichni noví zákazníci automaticky účtovat pomocí této metody. Tato metoda účtování těží zákazníky díky cenovým výhodám a možnosti plánování předem.

Další informace najdete v tématu [upgrade modelu fakturace na měsíční aktivní uživatele](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing#upgrade-to-monthly-active-users-billing-model).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Nové federované aplikace dostupné v Aplikace Azure AD galerii – říjen 2019

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran

V říjnu 2019 jsme do Galerie aplikací přidali tyto 35 nové aplikace s podporou federace:

[V případě krize – Mobile](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-mobile-tutorial), [Juno cesta](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial), [ExponentHR](https://docs.microsoft.com/azure/active-directory/saas-apps/exponenthr-tutorial), [TACT](https://tact.ai/assistant/), [OpusCapita řízení hotovosti](http://cm1.opuscapita.com/tenantname), [Salestim](https://prd.salestim.io/forms), [Learnster](https://docs.microsoft.com/azure/active-directory/saas-apps/learnster-tutorial), [dynaTrace](https://docs.microsoft.com/azure/active-directory/saas-apps/dynatrace-tutorial), [HunchBuzz](https://login.hunchbuzz.com/integrations/azure/process), [Freshworks](https://docs.microsoft.com/azure/active-directory/saas-apps/freshworks-tutorial), [eCornell](https://docs.microsoft.com/azure/active-directory/saas-apps/ecornell-tutorial), [ShipHazmat](https://docs.microsoft.com/azure/active-directory/saas-apps/shiphazmat-tutorial), [Netskope Cloud Security](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial), [contentd](https://docs.microsoft.com/azure/active-directory/saas-apps/contentful-tutorial), [Bindtuning](https://bindtuning.com/login), HireVue, HireVue, USOnly, [HireVue,](https://www.hirevue.com/)WittyParrot – [USA](https://www.hirevue.com/) [](https://www.hirevue.com/) [, Znalostní báze](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), [Cloudmore](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudmore-tutorial), [Visit.org](https://docs.microsoft.com/azure/active-directory/saas-apps/visitorg-tutorial), [cambium Xirrus EasyPass Portal](https://login.xirrus.com/azure-signup), [Paylocity](https://docs.microsoft.com/azure/active-directory/saas-apps/paylocity-tutorial), [WEBLOC!](https://docs.microsoft.com/azure/active-directory/saas-apps/mail-luck-tutorial), [Týmová](https://theteamie.com/)schránka, [rychlost pro týmy](https://velocity.peakup.org/teams/login), [SIGNL4](https://account.signl4.com/manage), [EAB navigovat impl](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-impl-tutorial), [ScreenMeet](https://console.screenmeet.com/), [Omega Point](https://pi.ompnt.com/), [speaking E-mail pro Intune (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune), [speaking e-mail pro Office 365 Direct (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct), [ExactCare SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/exactcare-sso-tutorial), [iHealthHome pro navigační systémy](https://ihealthnav.com/account/signin) [ Qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Konsolidovaná položka nabídky zabezpečení na portálu Azure AD

**Zadejte:** Změněná funkce  
**Kategorie služby:** Ochrana identity  
**Schopnost produktu:** Zabezpečení identity & ochrana

Teď máte přístup ke všem dostupným funkcím zabezpečení Azure AD z nové položky nabídky **zabezpečení** a z panelu **hledání** v Azure Portal. Kromě toho nová úvodní stránka **zabezpečení** , která se nazývá **zabezpečení – Začínáme**, bude obsahovat odkazy na naši veřejnou dokumentaci, pokyny pro zabezpečení a příručky pro nasazení.

Nová nabídka **zabezpečení** zahrnuje:

- Podmíněný přístup
- Identity Protection
- Security Center
- Skóre Secure identity
- Metody ověřování
- MFA
- Sestavy rizik – rizikové uživatele, rizikové přihlašovacíky, detekce rizik
- a další...

Další informace najdete v tématu [zabezpečení – Začínáme](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted).

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Zásady vypršení platnosti skupin Office 365 se vylepšily pomocí automatické obnovy.

**Zadejte:** Změněná funkce  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Správa životního cyklu identit

Zásady vypršení platnosti skupin Office 365 se rozšířily tak, aby automaticky obnovily skupiny, které aktivně používají její členové. Skupiny se v závislosti na aktivitě uživatelů na všech aplikacích Office 365, včetně Outlooku, SharePointu a týmů, obnoví.

Toto vylepšení pomáhá snižovat vaše oznámení o vypršení platnosti skupiny a pomáhá zajistit, aby byly aktivní skupiny dál dostupné. Pokud pro skupiny Office 365 už máte aktivní zásady vypršení platnosti, nemusíte nic dělat, abyste tuto novou funkci zapnuli.

Další informace najdete v tématu [Konfigurace zásad vypršení platnosti pro skupiny Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-lifecycle).

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Prostředí pro vytváření aktualizací Azure AD Domain Services (Azure služba AD DS)

**Zadejte:** Změněná funkce  
**Kategorie služby:** Azure AD Domain Services  
**Schopnost produktu:** Azure AD Domain Services

Aktualizovali jsme Azure AD Domain Services (Azure služba AD DS), aby zahrnovala nové a vylepšené prostředí pro vytváření, což vám pomůže vytvořit spravovanou doménu pouhým třemi kliknutími! Kromě toho teď můžete nahrát a nasadit Azure služba AD DS ze šablony.

Další informace najdete v tématu [kurz: vytvoření a konfigurace instance Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance).

---

## <a name="september-2019"></a>Září 2019

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Plán změny: vyřazení Power BI balíčků obsahu

**Zadejte:** Plánování změn  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav

Od 1. října 2019 začne Power BI začínat všechny balíčky obsahu, včetně balíčku obsahu Power BI Azure AD. Jako alternativu k tomuto balíčku obsahu můžete použít sešity Azure AD a získat přehled o službách souvisejících s Azure AD. Další sešity se připravují, včetně sešitů o zásadách podmíněného přístupu v režimu pouze sestavy, přehledů na základě souhlasu aplikace a dalších.

Další informace o sešitech najdete v tématu [Jak používat Azure monitor sešity pro Azure Active Directory sestavy](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks). Další informace o vyřazení balíčků obsahu najdete v blogovém příspěvku s [oznámením o obecných dostupnosti pro Power BI šablonových aplikací](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/) .

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>Můj profil se přejmenovává a integruje se stránkou systém Microsoft Office účtu.

**Zadejte:** Plánování změn  
**Kategorie služby:** Můj profil/účet  
**Schopnost produktu:** Prostřednictvím

Od října se moje prostředí mého profilu stane účtem. V rámci této změny všude, kde se aktuálně říká, se **můj profil** změní na **můj účet**. Na základě změny názvů a některých vylepšení návrhu nabízí aktualizovaný způsob další integraci se stránkou systém Microsoft Office účtu. Konkrétně budete mít přístup k instalacím a předplatným systému Office na stránce **Přehled účtu** , a to spolu s preferencemi kontaktů souvisejících s Office ze stránky **soukromí** .

Další informace o prostředí můj profil (Preview) najdete v tématu [Přehled portálu Profile (Preview)](https://docs.microsoft.com/azure/active-directory/user-help/myprofile-portal-overview).

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Hromadná Správa skupin a členů pomocí souborů CSV na portálu Azure AD (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Prostřednictvím

S potěšením oznamujeme, že v portálu Azure AD je dostupná veřejná verze Preview prostředí pro správu hromadných skupin. Teď můžete použít soubor CSV a portál Azure AD ke správě skupin a seznamů členů, včetně těchto:

- Přidávání nebo odebírání členů ze skupiny.

- Stahuje se seznam skupin z adresáře.

- Stahuje se seznam členů skupiny pro konkrétní skupinu.

Další informace najdete v tématu [hromadné přidání členů](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members), [hromadné odebrání členů](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members), [seznam členů hromadného stahování](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)a [seznam hromadných stažení skupin](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download).

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>Dynamický souhlas se teď podporuje prostřednictvím nového koncového bodu souhlasu správce.

**Zadejte:** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů

Vytvořili jsme nového koncového bodu souhlasu správce, který podporuje dynamické vyjádření souhlasu, což je užitečné pro aplikace, které chtějí používat model dynamického souhlasu na platformě Microsoft identity.

Další informace o použití tohoto nového koncového bodu najdete v tématu [použití koncového bodu pro vyjádření souhlasu správce](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Nové federované aplikace dostupné v Aplikace Azure AD galerii – září 2019

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran

Do září 2019 jsme do Galerie aplikací přidali tyto 29 nových aplikací s podporou federace:

[ScheduleLook](https://schedulelook.bbsonlineservices.net/), [přístup k Microsoft Azure SSO pro dodržování předpisů Ethidex Office™ – jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial), [iServer portál](https://docs.microsoft.com/azure/active-directory/saas-apps/iserver-portal-tutorial), [SKYSITE](https://docs.microsoft.com/azure/active-directory/saas-apps/skysite-tutorial), [Concur cestovné a výdaje](https://docs.microsoft.com/azure/active-directory/saas-apps/concur-travel-and-expense-tutorial), [WorkBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/workboard-tutorial), [YeeFlow](https://apps.yeeflow.com/), [obloukové zařízení](https://docs.microsoft.com/azure/active-directory/saas-apps/arc-facilities-tutorial), [Luware Stratus tým](https://stratus.emea.luware.cloud/login), [nejrůznější nápady](https://wideideas.online/wideideas/), PRISMA Cloud [, JDLT](https://clients.jdlt.co.uk/login), [RENRAKU](https://app.testhtm.com/settings/email-integration), SealPath, PRISMA, [Cloud](https://aec.cintoo.com/login) [, Penneo](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive) [, Hiretual](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial) [, Cintoo](https://app.penneo.com/),,, [](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial) [](https://docs.microsoft.com/azure/active-directory/saas-apps/renraku-tutorial) [Whitesource](https://docs.microsoft.com/azure/active-directory/saas-apps/whitesource-tutorial), [hostovaný dědictví online jednotného přihlašování](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-heritage-online-sso-tutorial), [IDC](https://docs.microsoft.com/azure/active-directory/saas-apps/idc-tutorial), [CakeHR](https://docs.microsoft.com/azure/active-directory/saas-apps/cakehr-tutorial), [bis](https://docs.microsoft.com/azure/active-directory/saas-apps/bis-tutorial), [COO Kai týmový Build](https://ms-contacts.coo-kai.jp/), [SonarQube](https://docs.microsoft.com/azure/active-directory/saas-apps/sonarqube-tutorial), [Adobe Identity Management](https://docs.microsoft.com/azure/active-directory/saas-apps/adobe-identity-management-tutorial), [výhody zjišťování jednotného přihlašování](https://docs.microsoft.com/azure/active-directory/saas-apps/discovery-benefits-sso-tutorial), [Amelio](https://app.amelio.co/), [iTask](https://itask.yipinapp.com/)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-global-reader-role"></a>Nová role globálního čtecího modulu Azure AD

**Zadejte:** Nová funkce  
**Kategorie služby:** RBAC  
**Schopnost produktu:** Access Control

Od 24. září 2019 začneme zavádět novou roli Azure Active Directory (AD) s názvem globální čtenář. Tato implementace začíná v rámci produkčního a globálního cloudového zákazníka (RSZ), dokončuje se v říjnu po celém světě.

Role globálního čtecího modulu je protějškem jen pro čtení pro globálního správce. Uživatelé v této roli můžou číst nastavení a informace pro správu napříč Microsoft 365 službami, ale nemůžou provádět akce správy. Vytvořili jsme roli globálního čtenáře, která vám pomůže snížit počet globálních správců ve vaší organizaci. Vzhledem k tomu, že účty globálních správců jsou výkonné a zranitelné vůči útokům, doporučujeme, abyste měli méně než pět globálních správců. Pro plánování, audity a vyšetřování doporučujeme použít roli globálního čtenáře. Doporučujeme také používat roli globálního čtecího modulu v kombinaci s jinými rolemi s omezeným oprávněním správce, jako je třeba správce Exchange, a získat tak práci, aniž byste museli provádět globální role správce.

Role globálního čtecího modulu funguje s novým centrem pro správu Microsoft 365, centrem pro správu Exchange, centrem pro správu týmů, Security Center, centrem dodržování předpisů, centrem pro správu Azure AD a centrem pro správu správy zařízení.

>[!NOTE]
> Na začátku veřejné verze Preview nebude role globálního čtecího zařízení fungovat s: SharePoint, Privileged Access Management, Customer Lockbox, popisky s citlivostí, životní cyklus týmů, vytváření sestav & Analytics, týmy pro správu telefonních zařízení a týmy. Zařazení. Všechny tyto služby mají za cíl pracovat s rolí v budoucnu.

Další informace najdete v tématu [oprávnění role správce v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Přístup k místnímu serveru sestav z aplikace Power BI Mobile pomocí Proxy aplikací služby Azure Active Directory

**Zadejte:** Nová funkce  
**Kategorie služby:** Proxy aplikace  
**Schopnost produktu:** Access Control

Nová integrace mezi Power BI mobilní aplikací a Azure Proxy aplikací služby AD umožňuje bezpečné přihlášení k Power BI mobilní aplikaci a zobrazení všech sestav vaší organizace hostovaných na místních Server sestav Power BI.

Informace o aplikaci Power BI Mobile, včetně místa, odkud si aplikaci stáhnout, najdete na [webu Power BI](https://powerbi.microsoft.com/mobile/). Další informace o tom, jak nastavit mobilní aplikaci Power BI pomocí Azure Proxy aplikací služby AD, najdete v tématu [Povolení vzdáleného přístupu k Power BI Mobile s využitím azure proxy aplikací služby AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-power-bi).

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>Je dostupná nová verze modulu AzureADPreview PowerShellu.

**Zadejte:** Změněná funkce  
**Kategorie služby:** Jiná  
**Schopnost produktu:** Službě

Do modulu AzureADPreview byly přidány nové rutiny, které vám pomůžou definovat a přiřazovat vlastní role v Azure AD, včetně těchto:

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Nová verze Azure AD Connect

**Zadejte:** Změněná funkce  
**Kategorie služby:** Jiná  
**Schopnost produktu:** Službě

Vydali jsme aktualizovanou verzi Azure AD Connect pro zákazníky s automatickým upgradem. Tato nová verze zahrnuje několik nových funkcí, vylepšení a opravy chyb. Další informace o této nové verzi najdete v tématu [Azure AD Connect: Historie vydání verze](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#14250).

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Server Azure Multi-Factor Authentication (MFA), verze 8.0.2 je teď k dispozici.

**Zadejte:** Určí  
**Kategorie služby:** VÍCEFAKTOROVÉHO  
**Schopnost produktu:** Zabezpečení identity & ochrana

Pokud jste stávající zákazník, který vyaktivoval MFA Server před 1. červenci 2019, můžete si teď stáhnout nejnovější verzi MFA serveru (verze 8.0.2). V této nové verzi jsme:

- Opravili jsme problém, takže když Azure AD Sync změní uživatele na povoleno, pošle se uživateli e-mail.

- Opravili jsme problém, aby zákazníci mohli úspěšně upgradovat a přitom dál používat funkce značek.

- Přidání kódu země Kosova (+ 383).

- Do protokolu MultiFactorAuthSvc. log bylo přidáno protokolování auditu jednorázového přihlášení.

- Vylepšený výkon pro sadu SDK webové služby.

- Opravené další drobné chyby.

Od 1. července 2019 se Microsoftu zastavilo poskytování MFA serveru pro nová nasazení. Noví zákazníci, kteří vyžadují službu Multi-Factor Authentication, by měli používat cloudové Multi-Factor Authentication Azure. Další informace najdete v tématu [plánování cloudového nasazení Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---
