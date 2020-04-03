---
title: Co je nového? Poznámky k verzi – Azure Active Directory | Dokumenty společnosti Microsoft
description: Zjistěte, co je nového ve službě Azure Active Directory, jako jsou nejnovější poznámky k verzi, známé problémy, opravy chyb, zastaralé funkce a nadcházející změny.
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
ms.date: 04/01/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b960bf63cae8b55ae2f66b6b809ee0d11229a312
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582782"
---
# <a name="whats-new-in-azure-active-directory"></a>Co je nového ve službě Azure Active Directory?

>Získejte upozornění o tom, kdy se má tato stránka znovu `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` aktualizovat ![zkopírováním](./media/whats-new/feed-icon-16x16.png) a vložením této adresy URL: do čtečky informačních kanálů informačního kanálu informačního kanálu rss.

Azure AD obdrží vylepšení průběžně. Chcete-li mít aktuální informace o nejnovějším vývoji, tento článek obsahuje informace o:

- Nejnovější verze
- Známé problémy
- Opravy chyb
- Zastaralé funkce
- Plány změn

Tato stránka je aktualizována měsíčně, takže ji pravidelně přepínat. Pokud hledáte položky, které jsou starší než šest měsíců, najdete je v [archivu pro co je nového ve službě Azure Active Directory](whats-new-archive.md).

---

## <a name="march-2020"></a>Březen 2020

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>Nespravované účty Azure Active Directory v aktualizaci B2B pro březen 2021

**Typ:** Plán změn  
**Kategorie služeb:** B2b  
**Možnost i možnosti produktu:** B2B/B2C
 
**března 2021**microsoft už nebude podporovat uplatnění pozvánek vytvořením nespravovaných účtů Azure Active Directory (Azure AD) a tenantů pro scénáře spolupráce B2B. V rámci přípravy na toto doporučujeme, abyste se přihlásili k [e-mailu a uzamykat e-mailem ověření pomocí hesla](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode).

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>Uživatelé s výchozí přístupovou rolí budou mít prostor pro zřizování.

**Typ:** Plán změn  
**Kategorie služeb:** Zřizování aplikací  
**Možnost i možnosti produktu:** Správa životního cyklu identity
 
Historicky uživatelé s výchozí přístupovou rolí byly mimo rozsah pro zřizování. Slyšeli jsme zpětnou vazbu, že zákazníci chtějí, aby uživatelé s touto rolí byli v oboru pro zřizování. Pracujeme na nasazení změny tak, aby všechny nové konfigurace zřizování umožní uživatelům s výchozí přístupovou roli, které mají být zřízeny. Postupně změníme chování pro stávající konfigurace zřizování pro podporu zřizování uživatelů s touto rolí. Není vyžadována žádná akce zákazníka. Jakmile bude tato změna zavedena, zveřejníme aktualizaci naší [dokumentace.](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned)

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Spolupráce Azure AD B2B bude dostupná v Microsoft Azure provozovaném klienty 21Vianet (Azure China 21Vianet)

**Typ:** Plán změn  
**Kategorie služeb:** B2b  
**Možnost i možnosti produktu:** B2B/B2C
 
Funkce spolupráce Azure AD B2B budou dostupné v Microsoft Azure provozovaném klienty 21Vianet (Azure China 21Vianet), což uživatelům v tenantovi Azure China 21Vianet umožní bezproblémovou spolupráci s uživateli v jiných tenantech Azure China 21Vianet. [Další informace o spolupráci Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/).

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Redesign e-mailu s pozvánkou na spolupráci Azure AD B2B

**Typ:** Plán změn  
**Kategorie služeb:** B2b  
**Možnost i možnosti produktu:** B2B/B2C
 
[E-maily,](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) které jsou odesílány službou pozvání ke spolupráci Azure AD B2B pozvat uživatele do adresáře bude přepracován tak, aby informace o pozvání a další kroky uživatele jasnější.

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>Změny zásad Služby HomeRealmDiscovery se zobrazí v protokolech auditu.

**Typ:** Dlouhodobého  
**Kategorie služeb:** Auditu  
**Možnost i možnosti produktu:** Monitorování & reportingu
 
Opravili jsme chybu, kdy změny [zásad HomeRealmDiscovery](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal) nebyly zahrnuty do protokolů auditu. Nyní budete moci zjistit, kdy a jak byla zásada změněna a kým. 

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Spolupráce Azure AD B2B, která je dostupná v tenantech Azure Government

**Typ:** Nová funkce  
**Kategorie služeb:** B2b  
**Možnost i možnosti produktu:** B2B/B2C
 
Funkce spolupráce Azure AD B2B jsou teď dostupné mezi některými tenanty Azure Government.  Chcete-li zjistit, jestli váš tenant může používat tyto funkce, postupujte podle pokynů na [webu Jak poznám, jestli je spolupráce B2B dostupná v mém tenantovi Azure US Government?](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant).

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>Integrace Azure Monitoru pro protokoly Azure je teď dostupná v Azure Government

**Typ:** Nová funkce  
**Kategorie služeb:** Vykazování  
**Možnost i možnosti produktu:** Monitorování & reportingu
 
Integrace Azure Monitoru s protokoly Azure AD je teď dostupná ve službě Azure Government. Protokoly Azure AD (protokoly auditování a přihlášení) můžete směrovat do účtu úložiště, centra událostí a analýzy protokolů. Podívejte se na [podrobnou dokumentaci](https://aka.ms/aadlogsinamd) a [plány nasazení pro vytváření sestav a monitorování](https://docs.microsoft.com/azure/active-directory/reports-monitoring/plan-monitoring-and-reporting) pro scénáře Azure AD.

---

### <a name="identity-protection-refresh-in-azure-government"></a>Aktualizace ochrany identity ve vládě Azure

**Typ:** Nová funkce  
**Kategorie služeb:** Ochrana identity  
**Možnost i možnosti produktu:** Ochrana & zabezpečení identity

Rádi se můžeme podělit o to, že jsme teď zavedli obnovené prostředí [Azure AD Identity Protection](https://aka.ms/IdentityProtectionDocs) na [portálu Microsoft Azure Government](https://portal.azure.us/). Další informace naleznete v našem [příspěvku na blogu s oznámením](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667).

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>Zotavení po havárii: Stažení a uložení konfigurace zřizování

**Typ:** Nová funkce  
**Kategorie služeb:** Zřizování aplikací  
**Možnost i možnosti produktu:** Správa životního cyklu identity
 
Služba zřizování Azure AD poskytuje bohatou sadu možností konfigurace. Zákazníci musí mít možnost uložit svou konfiguraci, aby na ni mohli později odkazovat nebo se vrátit k známé dobré verzi. Přidali jsme možnost stáhnout konfiguraci zřizování jako soubor JSON a nahrát ji, když ji potřebujete. [Další informace](https://docs.microsoft.com/azure/active-directory/app-provisioning/export-import-provisioning-configuration).

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Samoobslužné resetování hesla (samoobslužné resetování hesla) nyní vyžaduje dvě brány pro správce v Microsoft Azure provozované společností 21Vianet (Azure China 21Vianet). 

**Typ:** Změněná funkce  
**Kategorie služeb:** Samoobslužné resetování hesla  
**Možnost i možnosti produktu:** Ochrana & zabezpečení identity
 
Dříve v Microsoft Azure provozovaném společností 21Vianet (Azure China 21Vianet) potřebují správci pomocí samoobslužného resetování hesla (SSPR) k resetování vlastních hesel pouze jednu "bránu" (výzvu), aby prokázali svou identitu. Ve veřejných a jiných národních cloudů, správci obecně musí používat dvě brány prokázat svou identitu při používání sspr. Ale protože jsme v Azure China 21Vianet nepodporovali SMS nebo telefonní hovory, povolili jsme resetování hesla jednou bránou ze strany správců.

Vytváříme paritu funkcí sspr mezi Azure China 21Vianet a veřejným cloudem. Do budoucna musí správci při použití sspr používat dvě brány. Sms, telefonní hovory a oznámení aplikací Authenticator a kódy budou podporovány. [Další informace](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#administrator-reset-policy-differences).

---

### <a name="password-length-is-limited-to-256-characters"></a>Délka hesla je omezena na 256 znaků

**Typ:** Změněná funkce  
**Kategorie služeb:** Ověřování (přihlášení)  
**Možnost i možnosti produktu:** Ověřování uživatelů
 
Chcete-li zajistit spolehlivost služby Azure AD, uživatelská hesla jsou nyní omezeny na délku 256 znaků. Uživatelé s hesly delšími, než je tato, budou při následném přihlášení požádáni o změnu hesla, a to buď kontaktováním správce, nebo pomocí samoobslužné funkce resetování hesla.

Tato změna byla povolena v březnu 13th, 2020, v 10AM PST (18:00 UTC) a chyba je AADSTS 50052, InvalidPasswordExceedsMaxLength. Další podrobnosti najdete v [oznámení o porušení změny.](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#user-passwords-will-be-restricted-to-256-characters)

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Protokoly přihlášení Azure AD jsou teď dostupné pro všechny klienty zdarma prostřednictvím portálu Azure

**Typ:** Změněná funkce  
**Kategorie služeb:** Vykazování  
**Možnost i možnosti produktu:** Monitorování & reportingu
 
Od této chvíle mají zákazníci, kteří mají bezplatné klienty, přístup k [protokolům přihlášení Azure AD z portálu Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) až na 7 dní. Dříve byly přihlašovací protokoly dostupné jenom pro zákazníky s licencemi služby Azure Active Directory Premium. S touto změnou všichni klienti přístup k těmto protokolům prostřednictvím portálu.

> [!NOTE]
> Zákazníci stále potřebují prémiovou licenci (Azure Active Directory Premium P1 nebo P2) pro přístup k přihlašovacím protokolům prostřednictvím rozhraní Microsoft Graph API a Azure Monitor.

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>Vyřazení možností skupin pro celé adresáře z obecného nastavení skupin na webu Azure Portal

**Typ:** Zastaralé  
**Kategorie služeb:** Správa skupiny  
**Možnost i možnosti produktu:** Spolupráci

Abychom zákazníkům poskytli flexibilnější způsob vytváření skupin pro celý adresář, které nejlépe vyhovují jejich potřebám, nahradili jsme možnost **Skupiny pro celý adresář** z nastavení **Skupiny** > **obecné** na portálu Azure odkazem na [dokumentaci dynamické skupiny](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership). Naši dokumentaci jsme vylepšili tak, aby obsahovala další pokyny, aby správci mohli vytvářet skupiny pro všechny uživatele, které zahrnují nebo vylučují uživatele typu Host.

---

## <a name="february-2020"></a>Únor 2020

### <a name="upcoming-changes-to-custom-controls"></a>Nadcházející změny vlastních ovládacích prvků

**Typ:** Plán změn  
**Kategorie služeb:** Mfa  
**Možnost i možnosti produktu:** Ochrana & zabezpečení identity
 
Plánujeme nahradit aktuální vlastní ovládací prvky náhled s přístupem, který umožňuje možnosti ověřování poskytované partnerem bezproblémově pracovat se správcem služby Azure Active Directory a koncových uživatelů prostředí. V současné době partner mfa řešení čelit následujícím omezením: fungují pouze po zadání hesla; neslouží jako vícefaktorové ověřování pro zintenzivnit ověřování v jiných klíčových scénářích; a neintegrují se s funkcemi správy pověření pro koncové uživatele nebo s právu. Nová implementace umožní faktorům ověřování poskytovaným partnerem pracovat společně s předdefinovanými faktory pro klíčové scénáře, včetně registrace, využití, deklarací mfa, zvýšení ověřování, vytváření sestav a protokolování. 

Vlastní ovládací prvky budou i nadále podporovány ve verzi preview spolu s novým návrhem, dokud nedosáhne obecné dostupnosti. V tomto okamžiku poskytneme zákazníkům čas na migraci na nový design. Vzhledem k omezením současného přístupu nepřijmeme nové poskytovatele, dokud nebude k dispozici nový návrh. Úzce spolupracujeme se zákazníky a poskytovateli a budeme komunikovat o časové ose, jak se blížíme. [Další informace](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#).

---

### <a name="identity-secure-score---mfa-improvement-action-updates"></a>Skóre zabezpečení identity – aktualizace akcí pro zlepšení vícefaktorové informace

**Typ:** Plán změn  
**Kategorie služeb:** Mfa  
**Možnost i možnosti produktu:** Ochrana & zabezpečení identity
 
Aby se zohlednila potřeba, aby podniky zajistily nejvyšší zabezpečení a zároveň používaly zásady, které fungují s jejich podnikáním, microsoft secure score odstraňuje tři akce vylepšení zaměřené na vícefaktorové ověřování (MFA) a přidává dvě.

Budou odebrány následující akce vylepšení:

- Registrace všech uživatelů pro vícefaktorové finanční účty
- Vyžadování MFA pro všechny uživatele
- Vyžadovat vícefaktorové informace pro privilegované role Azure AD

Budou přidány následující akce ke zlepšení:

- Ujistěte se, že všichni uživatelé mohou dokončit vícefaktorové povolení pro zabezpečený přístup
- Vyžadovat vícefaktorové řízení pro role pro správu

Tyto nové akce vylepšení bude vyžadovat registraci uživatelů nebo správců pro vícefaktorové informace v adresáři a vytvoření správné sady zásad, které odpovídají potřebám vaší organizace. Hlavním cílem je flexibilita a zároveň zajistit, aby se všichni uživatelé a správci mohli ověřovat pomocí několika faktorů nebo výzev k ověření identity na základě rizika. To může mít podobu nastavení výchozích nastavení zabezpečení, které umožňují společnosti Microsoft rozhodnout, kdy napadnout uživatele pro vícefaktorové informace nebo mají více zásad, které používají rozhodnutí s vymezenou matouškou. V rámci těchto aktualizací akcí zlepšení zásady ochrany podle směrného plánu již nebudou zahrnuty do výpočtů hodnocení. [Přečtěte si další informace o tom, co přichází v microsoft secure score](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score-whats-coming?view=o365-worldwide).

---

### <a name="azure-ad-domain-services-sku-selection"></a>Výběr skladové položky služby Azure AD Domain Services

**Typ:** Nová funkce  
**Kategorie služeb:** Služby domény Azure AD  
**Možnost i možnosti produktu:** Služby domény Azure AD
 
Slyšeli jsme zpětnou vazbu, že zákazníci služby Azure AD Domain Services chtějí větší flexibilitu při výběru úrovní výkonu pro své instance. února 2020 jsme přešli z dynamického modelu (kde Azure AD určuje výkon a cenovou úroveň na základě počtu objektů) na model vlastního výběru. Zákazníci si teď můžou vybrat úroveň výkonu, která odpovídá jejich prostředí. Tato změna nám také umožňuje povolit nové scénáře, jako jsou doménové struktury prostředků a prémiové funkce, jako jsou denní zálohy. Počet objektů je nyní neomezený pro všechny sku, ale budeme i nadále nabízet návrhy počtu objektů pro každou úroveň.

**Není nutná žádná okamžitá akce zákazníka.** Pro stávající zákazníky určuje novou výchozí úroveň dynamická úroveň, která byla používána 1. V důsledku této změny neexistuje žádný dopad na ceny nebo výkon. Do budoucna budou zákazníci Azure AD DS muset vyhodnotit požadavky na výkon, jak se mění jejich velikost adresáře a charakteristiky pracovního vytížení. Přepínání mezi úrovněmi služeb bude i nadále operace bez prostojů a již nebudeme automaticky přesouvat zákazníky na nové úrovně na základě růstu jejich adresáře. Kromě toho nedojde k žádnému zvýšení cen a nové ceny budou v souladu s naším současným fakturačním modelem. Další informace najdete v dokumentaci k [dokumentům ke skutu stonu služby Azure AD DS](https://docs.microsoft.com/azure/active-directory-domain-services/administration-concepts#azure-ad-ds-skus) a [na stránce s cenami služby Azure AD Domain Services](https://azure.microsoft.com/pricing/details/active-directory-ds/).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD – únor 2020

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnosti produktu:** Integrace třetí strany
 
V únoru 2020 jsme do galerie aplikací přidali těchto 31 nových aplikací s podporou Federace: 

[Patentová platforma IamIP](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial), [Experience Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial), [NS1 SSO For Azure](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial), [Barracuda Email Security Service](https://ess.barracudanetworks.com/sso/azure), [ABa Reporting](https://myaba.co.uk/client-access/signin/auth/msad), V případě krize - Online [portál,](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial) [BIC Cloud Design](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial), [Včelař Azure AD Data Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial), Korn Ferry [Assessments](https://www.kornferry.com/solutions/kf-digital/kf-assess), [Verkada Command](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial), [Splashtop](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial), [Syxsense](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial), [EAB Navigate](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial), New Re [(Omezená verze)](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial), [Thulium](https://admin.thulium.com/login/instance), [Ticket Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial), Template [Chooser pro týmy](https://links.officeatwork.com/templatechooser-download-teams), [Včely](https://www.beesy.me/index.php/site/login), [Systém podpory zdraví](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial), [NÁSTĚNNÝ MRÁZ](https://app.mural.co/signup), [Hive](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial), [LavaDo,](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview) [Wakelet](https://wakelet.com/login), [Firmex VDR](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial), [ThingLink pro učitele a školy](https://www.thinglink.com/), [Coda](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial), [NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product), [WEDO](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial), [InvitePeople](https://invitepeople.com/login), [Reprints Desk - Článek Galaxy](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial), [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial)

 
Další informace o aplikacích najdete v tématu [Integrace aplikací SaaS se službou Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete [v tématu Seznam aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Nové zřizovací konektory v Galerii aplikací Azure AD – únor 2020

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnosti produktu:** Integrace třetí strany
 
Nyní můžete automatizovat vytváření, aktualizaci a odstranění uživatelských účtů pro tyto nově integrované aplikace:

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud by Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
- [Zapier](https://docs.microsoft.com/azure/active-directory/saas-apps/zapier-provisioning-tutorial)

Další informace o tom, jak lépe zabezpečit vaši organizaci pomocí automatického zřizování uživatelských účtů, najdete [v tématu Automatizace zřizování uživatelů pro aplikace SaaS pomocí Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>Podpora Azure AD pro klíče zabezpečení FIDO2 v hybridních prostředích

**Typ:** Nová funkce  
**Kategorie služeb:** Ověřování (přihlášení)  
**Možnost i možnosti produktu:** Ověřování uživatelů
 
Oznamujeme veřejnou verzi preview podpory Azure AD pro klíče zabezpečení FIDO2 v hybridních prostředích. Uživatelé teď můžou používat klíče zabezpečení FIDO2 k přihlášení ke svým hybridním zařízením Azure AD, která se připojila k zařízením s Windows 10, a získat bezproblémové přihlašování k místním a cloudovým prostředkům. Podpora hybridních prostředí je nejžádanější funkcí od našich zákazníků bez hesel od doby, kdy jsme původně spustili veřejnou verzi preview pro podporu FIDO2 v zařízeních spojených s Azure AD. Ověřování bez hesla pomocí pokročilých technologií, jako je biometrie a kryptografie s veřejným/soukromým klíčem, poskytuje pohodlí a snadné použití při zabezpečení. Pomocí této verze Public Preview můžete nyní používat moderní ověřování, jako jsou bezpečnostní klíče FIDO2, pro přístup k tradičním prostředkům služby Active Directory. Další informace najdete v [místním objektu pro](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises)místní prostředky . 

Chcete-li začít, navštivte [povolit fido2 bezpečnostní klíče pro vašeho tenanta](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key) pro podrobné pokyny. 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>Nové prostředí můj účet je nyní obecně dostupné

**Typ:** Změněná funkce  
**Kategorie služeb:** Můj profil/účet  
**Možnost i možnosti produktu:** Zkušenosti koncových uživatelů
 
Můj účet, one stop shop pro všechny potřeby správy účtu koncového uživatele, je nyní obecně k dispozici! Koncoví uživatelé mohou k tomuto novému webu přistupovat prostřednictvím adresy URL nebo v záhlaví nového prostředí Moje aplikace. Další informace o všech samoobslužných funkcích, které nové prostředí nabízí, najdete v části [Přehled portálu můj účet](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview).

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>Aktualizace adresy URL webu klienta na myaccount.microsoft.com

**Typ:** Změněná funkce  
**Kategorie služeb:** Můj profil/účet  
**Možnost i možnosti produktu:** Zkušenosti koncových uživatelů
 
Nové prostředí pro koncové uživatele služby `https://myaccount.microsoft.com` Můj účet bude v příštím měsíci aktualizovat adresu URL. Další informace o prostředí a všech samoobslužných funkcích účtu, které nabízí koncovým uživatelům, najdete v [nápovědě k portálu Můj účet](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview).

---
 
## <a name="january-2020"></a>Leden 2020
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>Nový portál Moje aplikace je nyní obecně dostupný

**Typ:** Plán změn  
**Kategorie služeb:** Moje aplikace  
**Možnost i možnosti produktu:** Zkušenosti koncových uživatelů
 
Upgradujte svou organizaci na nový portál Moje aplikace, který je nyní obecně dostupný! Další informace o novém portálu a kolekcích najdete na webu [Vytvoření kolekcí na portálu Moje aplikace](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Pracovní prostory ve službě Azure AD byly přejmenovány na kolekce

**Typ:** Změněná funkce  
**Kategorie služeb:** Moje aplikace   
**Možnost i možnosti produktu:** Zkušenosti koncových uživatelů
 
Pracovní prostory, filtry správci mohou nakonfigurovat uspořádat své uživatele aplikace, bude nyní označována jako kolekce. Další informace o jejich konfiguraci najdete na webu [Vytvoření kolekcí na portálu Moje aplikace](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Registrace a přihlášení k telefonu Azure AD B2C Phone pomocí vlastních zásad (Public Preview)

**Typ:** Nová funkce  
**Kategorie služeb:** B2C - Správa identit y spotřebitele  
**Možnost i možnosti produktu:** B2B/B2C
 
S registrací a přihlášením na telefonní číslo mohou vývojáři a podniky umožnit svým zákazníkům zaregistrovat se a přihlásit se pomocí jednorázového hesla odeslaného na telefonní číslo uživatele prostřednictvím SMS. Tato funkce také umožňuje zákazníkovi změnit své telefonní číslo, pokud ztratí přístup k telefonu. Díky možnosti vlastních zásad umožňuje registrace a přihlášení k telefonu vývojářům a podnikům komunikovat o své značce prostřednictvím přizpůsobení stránky. Přečtěte si, jak nastavit registraci a přihlášení pomocí [vlastních zásad v Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication).
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Nové zřizovací konektory v Galerii aplikací Azure AD – leden 2020

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnosti produktu:** Integrace třetí strany
 
Nyní můžete automatizovat vytváření, aktualizaci a odstranění uživatelských účtů pro tyto nově integrované aplikace:

- [Promapp]( https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-provisioning-tutorial)
- [Zscaler Private Access](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

Další informace o tom, jak lépe zabezpečit vaši organizaci pomocí automatického zřizování uživatelských účtů, najdete [v tématu Automatizace zřizování uživatelů pro aplikace SaaS pomocí Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD – leden 2020

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnosti produktu:** Integrace třetí strany
 
V lednu 2020 jsme do galerie aplikací přidali těchto 33 nových aplikací s podporou Federace: 

[JOSA](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial), [Fastly Edge Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial), [Terraform Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial), [Spintr SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial), [Abibot Netlogistik](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik), `https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access` [, Upshotly](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial), [LeaveBot](https://leavebot.io/#home), [DataCamp](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial), [TripActions](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial), [SmartWork](https://www.intumit.com/english/SmartWork.html), [Dotcom-Monitor](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial), [SSOGEN - Azure AD SSO Gateway pro Oracle E-Business Suite - EBS, PeopleSoft, a JDE](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial), [Hosted MyCirqa SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial), [Yuhu Property Management Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial), `https://sites.lumapps.com/login` [Upwork Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial), [Talentsoft](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial), [SmartDB pro Microsoft Teams](http://teams.smartdb.jp/login/), [PressPage](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial), [ContractSafe Saml2 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial), [Maxient Conduct Manager Software](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial), [Helpshift](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial), [PortalTalk 365](https://www.portaltalk.com/), [CoreView](https://portal.coreview.com/), [Squelch Cloud Office365 Connector](https://laxmi.squelch.io/login), [PingFlow Authentication](https://app-staging.pingview.io/), [PrinterLogic SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial), [Taskize Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial), `https://app.sandwai.com/` [EZRentOut](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial), [AssetSonar](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial), [Akari Virtual Assistant](https://akari.io/akari-virtual-assistant/)

Další informace o aplikacích najdete v tématu [Integrace aplikací SaaS se službou Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete [v tématu Seznam aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="two-new-identity-protection-detections"></a>Dvě nové detekce ochrany identity

**Typ:** Nová funkce  
**Kategorie služeb:** Ochrana identity  
**Možnost i možnosti produktu:** Ochrana & zabezpečení identity
 
Do ochrany identity jsme přidali dva nové typy propojených přihlašovacích údajů: podezřelá pravidla pro manipulaci s doručenou poštou a nemožné cestování. Tyto offline detekce jsou zjištěny Microsoft Cloud App Security (MCAS) a ovlivňují uživatele a riziko přihlášení v identity protection. Další informace o těchto detekcích naleznete v našich [typech rizik přihlášení](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk).
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>Narušující změna: Fragmenty identifikátorů URI nebudou přeneseny prostřednictvím přesměrování přihlášení.

**Typ:** Změněná funkce  
**Kategorie služeb:** Ověřování (přihlášení)  
**Možnost i možnosti produktu:** Ověřování uživatelů
 
února 2020, kdy je odeslánpožadavek na login.microsoftonline.com k přihlášení uživatele, služba připojí prázdný fragment k požadavku.  Tím se zabrání třídy přesměrování útoky tím, že zajistí, že prohlížeč vymaže všechny existující fragment v požadavku. Žádná aplikace by měla mít závislost na toto chování. Další informace naleznete v [tématu Breaking changes](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#february-2020) in the Microsoft identity platformy documentation.

---

## <a name="december-2019"></a>Prosinec 2019

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>Integrace sap successfactors zřizování do Azure AD a místní AD (Public Preview)

**Typ:** Nová funkce  
**Kategorie služeb:** Zřizování aplikací  
**Možnost i možnosti produktu:** Správa životního cyklu identity

Teď můžete integrovat SAP SuccessFactors jako autoritativní zdroj identity ve službě Azure AD. Tato integrace vám pomůže automatizovat životní cyklus koncových identit, včetně použití událostí založených na hr, jako jsou nové přijetí nebo ukončení, k řízení zřizování účtů Azure AD.

Další informace o tom, jak nastavit SAP SuccessFactors příchozí zřizování do Azure AD, najdete [v tématu Konfigurace SAP SuccessFactors automatické zřizování](https://aka.ms/SAPSuccessFactorsInboundTutorial) kurzu.

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Podpora přizpůsobených e-mailů ve službě Azure AD B2C (Public Preview)

**Typ:** Nová funkce  
**Kategorie služeb:** B2C - Správa identit y spotřebitele  
**Možnost i možnosti produktu:** B2B/B2C

Teď můžete použít Azure AD B2C k vytvoření přizpůsobených e-mailů, když se uživatelé zaregistrují k používání vašich aplikací. Pomocí DisplayControls (aktuálně ve verzi preview) a poskytovatele e-mailu třetí strany (například [SendGrid](https://sendgrid.com/), [SparkPost](https://sparkpost.com/)nebo vlastní rozhraní REST API) můžete použít vlastní šablonu e-mailu, **z** adresy a textu předmětu, stejně jako podporovat lokalizaci a vlastní nastavení jednorázového hesla (OTP).

Další informace najdete [v tématu Vlastní ověření e-mailu ve službě Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-email).

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>Nahrazení zásad směrného plánu výchozími hodnotami zabezpečení

**Typ:** Změněná funkce  
**Kategorie služeb:** Další  
**Možnost i možnosti produktu:** Zabezpečení a ochrana identity

Jako součást modelu zabezpečeného podle výchozího stavu pro ověřování odebíráme stávající zásady ochrany podle směrného plánu ze všech klientů. Toto odstranění je určeno k dokončení na konci února. Náhradou za tyto zásady ochrany podle směrného plánu je výchozí nastavení zabezpečení. Pokud používáte zásady ochrany podle směrného plánu, musíte naplánovat přechod na novou zásadu výchozího zabezpečení nebo na podmíněný přístup. Pokud jste tyto zásady nepoužili, není k dispozici žádná akce, kterou byste měli provést.

Další informace o nových výchozích hodnotách zabezpečení naleznete v tématu [Co jsou výchozí hodnoty zabezpečení?](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) Další informace o zásadách podmíněného přístupu naleznete [v tématu Běžné zásady podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common).

---

## <a name="november-2019"></a>Listopad 2019

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>Podpora atributu SameSite a Chromu 80

**Typ:** Plán změn  
**Kategorie služeb:** Ověřování (přihlášení)  
**Možnost i možnosti produktu:** Ověřování uživatelů

Prohlížeč Chrome 80 jako součást zabezpečeného modelu souborů cookie ve výchozím nastavení mění `SameSite` způsob, jakým zachází se soubory cookie bez atributu. S jakýmkoli souborem cookie, který atribut `SameSite` nespecifikuje, `SameSite=Lax`bude zacházeno tak, jako by byl nastaven na , což bude mít za následek, že Chrome zablokuje určité scénáře sdílení souborů cookie mezi doménami, na kterých může vaše aplikace záviset. Chcete-li zachovat starší chování Chromu, `SameSite=None` můžete `Secure` použít atribut a přidat další atribut, takže soubory cookie pro více webů lze přistupovat pouze přes připojení HTTPS. Chrome má tuto změnu dokončit do 4. února 2020.

Doporučujeme všem našim vývojářům testovat své aplikace pomocí tohoto návodu:

- Nastavte výchozí hodnotu pro nastavení **Použít zabezpečený soubor cookie** na **hodnotu Ano**.

- Nastavte výchozí hodnotu atributu **SameSite** na **hodnotu Žádný**.

- Přidejte `SameSite` další atribut **Secure**.

Další informace naleznete [v tématu Nadcházející změny souborů cookie na webu SameSite v ASP.NET a ASP.NET jádra](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) a [potenciální narušení webových stránek zákazníků a produktů a služeb společnosti Microsoft v Chromu verze 79 a novějších](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79).

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Nová oprava hotfix pro Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2)

**Typ:** Dlouhodobého  
**Kategorie služeb:** Správce identit společnosti Microsoft  
**Možnost i možnosti produktu:** Správa životního cyklu identity

Kumulativní balíček oprav hotfix (sestavení 4.6.34.0) je k dispozici pro Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Tento souhrnný balíček řeší problémy a přidává vylepšení, která jsou popsána v části "Opraveny problémy a vylepšení přidaná v této aktualizaci".

Další informace a stažení balíčku oprav hotfix naleznete v tématu [Microsoft Identity Manager 2016 Service Pack 2 (build 4.6.34.0) Kumulativní aktualizace je k dispozici](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r).

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>Nová sestava aktivit aplikace AD FS, která pomáhá migrovat aplikace do Azure AD (Public Preview)

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnost i možnosti produktu:** Sso

Pomocí nové sestavy aktivity aplikace Služby AD FS (AD FS) na webu Azure Portal určete, které z vašich aplikací se můžou migrovat do Azure AD. Sestava vyhodnotí kompatibilitu všech aplikací služby AD FS z hlediska kompatibility se službou Azure AD, zkontroluje všechny problémy a poskytne pokyny k přípravě jednotlivých aplikací na migraci.

Další informace najdete [v tématu Použití sestavy aktivity aplikace služby AD FS k migraci aplikací do služby Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity).

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>Nový pracovní postup, ve kterých mohou uživatelé požádat o souhlas správce (Public Preview)

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnost i možnosti produktu:** Řízení přístupu

Nový pracovní postup souhlasu správce poskytuje správcům způsob, jak udělit přístup k aplikacím, které vyžadují schválení správcem. Pokud se uživatel pokusí o přístup k aplikaci, ale nemůže poskytnout souhlas, může nyní odeslat žádost o schválení správcem. Žádost se odesílá e-mailem a umístí se do fronty, která je přístupná z webu Azure Portal, všem správcům, kteří byli označeni jako recenzenti. Poté, co recenzent provede akci v rámci nevyřízené žádosti, budou žádající uživatelé na akci upozorněni.

Další informace naleznete [v tématu Konfigurace pracovního postupu souhlasu správce (preview).](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow)

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>Nové prostředí konfigurace tokenu registrace aplikací Azure AD pro správu volitelných deklarací identity (Public Preview)

**Typ:** Nová funkce  
**Kategorie služeb:** Další  
**Možnost i možnosti produktu:** Vývojářské prostředí

Nové okno **konfigurace tokenu registrace aplikací Azure AD** na webu Azure Portal teď zobrazuje vývojářům aplikací dynamický seznam volitelných deklarací identity pro jejich aplikace. Toto nové prostředí pomáhá zefektivnit migrace aplikací Azure AD a minimalizovat volitelné deklarace chybné konfigurace.

Další informace najdete [v tématu Poskytování volitelných deklarací identity do aplikace Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Nový dvoufázový pracovní postup schvalování ve správě nároků Azure AD (Public Preview)

**Typ:** Nová funkce  
**Kategorie služeb:** Další  
**Možnost i možnosti produktu:** Správa nároků

Zavedli jsme nový dvoufázový schvalovací pracovní postup, který umožňuje vyžadovat, aby dva schvalovatelé schválili požadavek uživatele na přístupový balíček. Můžete ji například nastavit tak, aby vedoucí žádajícího uživatele musel nejprve schválit a pak můžete také požadovat, aby vlastník prostředku schválil. Pokud jeden z schvalovatelů neschválí, přístup není udělen.

Další informace najdete [v tématu Změna nastavení žádosti a schválení pro balíček přístupu ve správě oprávnění Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-request-policy).

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Aktualizace stránky Moje aplikace spolu s novými pracovními prostory (Public Preview)

**Typ:** Nová funkce  
**Kategorie služeb:** Moje aplikace  
**Možnosti produktu:** Integrace třetí strany

Nyní můžete přizpůsobit způsob, jakým uživatelé vaší organizace zobrazují a přistupují k obnovenému prostředí Moje aplikace. Toto nové prostředí také zahrnuje novou funkci pracovních prostorů, která uživatelům usnadňuje hledání a uspořádání aplikací.

Další informace o novém prostředí Moje aplikace a vytváření pracovních prostorů najdete [v tématu Vytváření pracovních prostorů na portálu Moje aplikace](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces).

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Podpora Google sociálních ID pro spolupráci Azure AD B2B (obecná dostupnost)

**Typ:** Nová funkce  
**Kategorie služeb:** B2b  
**Možnost i možnosti produktu:** Ověřování uživatelů

Nová podpora používání ID Google sociálních ID (účtů Gmail) ve službě Azure AD pomáhá usnadnit spolupráci uživatelům a partnerům. Partneři už nemusí vytvářet a spravovat nový účet specifický pro Microsoft. Microsoft Teams teď plně podporuje uživatele Google ve všech klientech a napříč běžnými koncovými body ověřování souvisejícími s klientem.

Další informace naleznete v [tématu Přidání Googlu jako poskytovatele identity pro uživatele typu Host B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Mobilní podpora Microsoft Edge pro podmíněný přístup a jednotné přihlašování (obecná dostupnost)

**Typ:** Nová funkce  
**Kategorie služeb:** Podmíněný přístup  
**Možnost i možnosti produktu:** Ochrana & zabezpečení identity

Azure AD pro Microsoft Edge na iOS a Android teď podporuje jednotné přihlašování a podmíněný přístup Azure AD:

- **Jednotné přihlašování k Microsoft Edge (Jednotné přihlašování):** Jednotné přihlašování je teď dostupné nativních klientech (například Microsoft Outlook a Microsoft Edge) pro všechny aplikace připojené k Azure AD.

- **Podmíněný přístup Microsoft Edge:** Prostřednictvím zásad podmíněného přístupu založených na aplikacích musí uživatelé používat prohlížeče chráněné microsoftem Intune, jako je například Microsoft Edge.

Další informace o podmíněném přístupu a jednotném přihlašování pomocí Microsoft Edge najdete v tématu [Microsoft Edge Mobile Support for Conditional Access and Single Sign-on Now Generally Available](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179) blog post. Další informace o tom, jak nastavit klientské aplikace pomocí [podmíněného přístupu založeného na aplikacích](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) nebo [podmíněného přístupu na zařízení](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices), najdete v [tématu Správa webového přístupu pomocí prohlížeče chráněného zásadami Microsoft Intune](https://docs.microsoft.com/intune/apps/app-configuration-managed-browser).

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Správa nároků Azure AD (obecná dostupnost)

**Typ:** Nová funkce  
**Kategorie služeb:** Další  
**Možnost i možnosti produktu:** Správa nároků

Správa nároků Azure AD je nová funkce zásad správného řízení identit, která organizacím pomáhá spravovat identitu a přístup k životnímu cyklu ve velkém měřítku. Tato nová funkce pomáhá automatizací pracovních postupů žádostí o přístup, přiřazení přístupu, recenzí a vypršení platnosti napříč skupinami, aplikacemi a weby SharePointu Online.

Díky správě nároků Azure AD můžete efektivněji spravovat přístup zaměstnanců i uživatelů mimo vaši organizaci, kteří potřebují přístup k těmto prostředkům.

Další informace najdete v tématu [Co je správa oprávnění Azure AD?](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview#license-requirements)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizace zřizování uživatelských účtů pro tyto nově podporované aplikace SaaS

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnosti produktu:** Integrace třetí strany  

Nyní můžete automatizovat vytváření, aktualizaci a odstranění uživatelských účtů pro tyto nově integrované aplikace:

[Služba ověřování identity cloudové platformy SAP](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-provisioning-tutorial), [SpaceIQ](https://docs.microsoft.com/azure/active-directory/saas-apps/spaceiq-provisioning-tutorial), [Miro](https://docs.microsoft.com/azure/active-directory/saas-apps/miro-provisioning-tutorial), [Cloudgate](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial), [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloudsuite-provisioning-tutorial), [Software OfficeSpace](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-software-provisioning-tutorial), [Prioritní matice](https://docs.microsoft.com/azure/active-directory/saas-apps/priority-matrix-provisioning-tutorial)

Další informace o tom, jak lépe zabezpečit vaši organizaci pomocí automatického zřizování uživatelských účtů, najdete [v tématu Automatizace zřizování uživatelů pro aplikace SaaS pomocí Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD – listopad 2019

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnosti produktu:** Integrace třetí strany

V listopadu 2019 jsme do galerie aplikací přidali těchto 21 nových aplikací s podporou Federace:

[Airtable](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial), [Hootsuite](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial), [Blue Access pro členy (BAM)](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial), [Bitly](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial), [Riva](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial), [ResLife Portal](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), [NegometrixPortal Jednotné přihlášení On (SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial), `https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279`, [Motus](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial), [MyAryaka](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial), [BlueMail](https://loginself1.bluemail.me/), [Beedle](https://teams-web.beedle.co/#/), [Visma](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial), [OneDesk](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial), [Foko Retail](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial), [Qmarkets Idea & Innovation Management](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial), [Netskope User Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial), [uniFLOW Online](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial), [Claromentis](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial), [Jisc Voter Student Registrace](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial), [e4enable](https://portal.e4enable.com/)

Další informace o aplikacích najdete v tématu [Integrace aplikací SaaS se službou Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete [v tématu Seznam aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>Nová a vylepšená galerie aplikací Azure AD

**Typ:** Změněná funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnost i možnosti produktu:** Sso

Aktualizovali jsme galerii aplikací Azure AD, abychom vám usnadnili hledání předem integrovaných aplikací, které podporují zřizování, OpenID Connect a SAML ve vašem tenantovi Azure Active Directory.

Další informace najdete [v tématu Přidání aplikace do klienta Služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal).

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>Zvětšení limitu délky definice role aplikace ze 120 na 240 znaků

**Typ:** Změněná funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnost i možnosti produktu:** Sso

Od zákazníků jsme slyšeli, že omezení délky pro hodnotu definice role aplikace v některých aplikacích a službách je příliš krátké na 120 znaků. V reakci na to jsme zvýšili maximální délku definice hodnoty role na 240 znaků.

Další informace o použití definic rolí specifických pro aplikaci najdete v tématu [Přidání rolí aplikace v aplikaci a jejich přijetí v tokenu](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps).

---

## <a name="october-2019"></a>Říjen 2019

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Vyřazení rozhraní identityRiskEvent API pro zjišťování rizik azure ad identity protection  

**Typ:** Plán změn  
**Kategorie služeb:** Ochrana identity  
**Možnost i možnosti produktu:** Ochrana & zabezpečení identity

V reakci na zpětnou vazbu od vývojářů můžou předplatitelé Azure AD Premium P2 teď provádět složité dotazy na data detekce rizik služby Azure AD Identity Protection pomocí nového rozhraní API riskDetection pro Microsoft Graph. Existující beta verze [rozhraní identityRiskEvent](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) API přestane vracet data kolem **10.** Pokud vaše organizace používá rozhraní identityRiskEvent API, měli byste přejít na nové rozhraní api riskDetection.

Další informace o novém rozhraní API pro detekci rizik naleznete v [referenční dokumentaci rozhraní API pro zjišťování rizik](https://aka.ms/RiskDetectionsAPI).

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>Podpora proxy aplikace pro atribut SameSite Attribute a Chrome 80

**Typ:** Plán změn  
**Kategorie služeb:** Proxy aplikace  
**Možnost i možnosti produktu:** Řízení přístupu

Pár týdnů před vydáním prohlížeče Chrome 80 plánujeme aktualizovat způsob, jakým soubory cookie proxy aplikace zacházejí s atributem **SameSite.** S vydáním Prohlížeče Chrome 80 bude s libovolným souborem cookie, který nespecifikuje atribut **SameSite,** zacházeno tak, jako by byl nastaven na `SameSite=Lax`.

Abychom se vyhnuli potenciálně negativním dopadům způsobeným touto změnou, aktualizujeme přístup k proxy aplikaci a soubory cookie relace pomocí:

- Nastavení výchozí hodnoty pro nastavení **Použít zabezpečený soubor cookie** na **hodnotu Ano**.

- Nastavení výchozí hodnoty atributu **SameSite** na **hodnotu None**.

    >[!NOTE]
    > Soubory cookie pro přístup k proxy aplikaci byly vždy přenášeny výhradně prostřednictvím zabezpečených kanálů. Tyto změny se vztahují pouze na soubory cookie relace.

Další informace o nastavení souborů cookie proxy aplikace najdete v tématu [Nastavení souborů cookie pro přístup k místním aplikacím ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="app-registrations-legacy-and-converged-app-management-from-the-application-registration-portal-appsdevmicrosoftcom-will-no-longer-be-available"></a>Registrace aplikací (starší verze) a správu konvergovaných aplikací z portálu registrace aplikací (apps.dev.microsoft.com) již nebudou k dispozici.

**Typ:** Plán změn  
**Kategorie služeb:** N/a  
**Možnost i možnosti produktu:** Vývojářské prostředí

V blízké budoucnosti už uživatelé s účty Azure AD už nebudou moct registrovat a spravovat konvergované aplikace pomocí portálu pro registraci aplikací (apps.dev.microsoft.com) nebo registrovat a spravovat aplikace v prostředí registrace aplikací (starší verze) na webu Azure Portal.

Další informace o novém prostředí registrace aplikací najdete [v tématu Registrace aplikací v průvodci školením na portálu Azure](../develop/app-registrations-training-guide-for-app-registrations-legacy-users.md).

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>Uživatelé již nejsou povinni znovu zaregistrovat během migrace z vícefaktorové ověřování na uživatele na podmíněné přístupové ověřování

**Typ:** Dlouhodobého  
**Kategorie služeb:** Mfa  
**Možnost i možnosti produktu:** Ochrana & zabezpečení identity

Opravili jsme známý problém, kdy uživatelé museli znovu zaregistrovat, pokud byli zakázáni pro vícefaktorové ověřování na uživatele (MFA) a pak povolili vícefaktorové ověřování prostřednictvím zásad podmíněného přístupu.

Chcete-li vyžadovat, aby se uživatelé znovu registrovali, můžete vybrat možnost **Povinná opětovná registrace mfa** z metod ověřování uživatele na portálu Azure AD. Další informace o migraci uživatelů z vícefaktorové ověřování podle uživatelů na mfa založené na podmíněném přístupu naleznete [v tématu Převod uživatelů z vícefaktorového faktoru pro jednotlivé uživatele na vícefaktorové ověřování založené na podmíněném přístupu](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted#convert-users-from-per-user-mfa-to-conditional-access-based-mfa).

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Nové funkce pro transformaci a odesílání deklarací identity v tokenu SAML

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnost i možnosti produktu:** Sso

Přidali jsme další funkce, které vám pomohou přizpůsobit a odeslat deklarace identity v tokenu SAML. Mezi tyto nové funkce patří:

- Další funkce transformace deklarací identity, které vám pomohou upravit hodnotu, kterou odešlete v deklaraci.

- Možnost použít více transformací na jednu deklaraci.

- Možnost zadat zdroj deklarace podle typu uživatele a skupiny, do které uživatel patří.

Podrobné informace o těchto nových funkcích, včetně jejich použití, naleznete [v tématu Přizpůsobení deklarací vydaných v tokenu SAML pro podnikové aplikace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Nová stránka Moje přihlášení pro koncové uživatele ve službě Azure AD

**Typ:** Nová funkce  
**Kategorie služeb:** Ověřování (přihlášení)  
**Možnost i možnosti produktu:** Monitorování & reportingu

Přidali jsme novou stránku **Moje** přihlášeníhttps://mysignins.microsoft.com) ( aby si uživatelé vaší organizace mohli zobrazit historii přihlášení v poslední době a mohli zkontrolovat neobvyklou aktivitu. Tato nová stránka umožňuje uživatelům zobrazit:

- Pokud se někdo pokouší uhodnout své heslo.

- Pokud se útočník úspěšně přihlásil ke svému účtu a z jakého místa.

- K jakým aplikacím se útočník pokusil získat přístup.

Další informace naleznete v [tématu Uživatelé nyní můžete zkontrolovat jejich historii přihlášení pro neobvyklé aktivity](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066) blog.

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Migrace služeb Azure AD Domain Services (Azure AD DS) z klasických virtuálních sítí Azure Resource Manager

**Typ:** Nová funkce  
**Kategorie služeb:** Služby domény Azure AD  
**Možnost i možnosti produktu:** Služby domény Azure AD

Pro naše zákazníky, kteří uvízli na klasických virtuálních sítích - máme pro vás skvělou zprávu! Nyní můžete provést jednorázovou migraci z klasické virtuální sítě do existující virtuální sítě Resource Manageru. Po přechodu do virtuální sítě Resource Manager, budete moci využít další a upgradované funkce, jako jsou jemně odstupňované zásady hesel, e-mailová oznámení a protokoly auditu.

Další informace najdete [v tématu Preview – migrace služby Azure AD Domain Services z modelu klasické virtuální sítě do Správce prostředků](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet).

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Aktualizace rozložení smlouvy stránky Azure AD B2C

**Typ:** Nová funkce  
**Kategorie služeb:** B2C - Správa identit y spotřebitele  
**Možnost i možnosti produktu:** B2B/B2C

Zavedli jsme některé nové změny verze 1.2.0 smlouvy o stránce pro Azure AD B2C. V této aktualizované verzi můžete nyní řídit pořadí načítání prvků, což může také pomoci zastavit blikání, ke kterému dojde při načtení šablony stylů (CSS).

Úplný seznam změn provedených ve smlouvě o stránce naleznete v [protokolu změn verze](https://docs.microsoft.com/azure/active-directory-b2c/page-layout#120).

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Aktualizace na stránku Moje aplikace spolu s novými pracovními prostory (veřejný náhled)

**Typ:** Nová funkce  
**Kategorie služeb:** Moje aplikace  
**Možnost i možnosti produktu:** Řízení přístupu

Nyní můžete přizpůsobit způsob, jakým uživatelé vaší organizace zobrazují a přistupují ke zbrusu novému prostředí Moje aplikace, včetně použití nové funkce pracovních prostorů, která jim usnadní hledání aplikací. Nová funkce pracovních prostorů funguje jako filtr pro aplikace, ke kterých už mají uživatelé vaší organizace přístup.

Další informace o zavádění nového prostředí Moje aplikace a vytváření pracovních prostorů najdete [v tématu Vytváření pracovních prostorů na portálu Moje aplikace (preview).](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces)

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>Podpora měsíčního aktivního uživatelského fakturačního modelu (obecná dostupnost)

**Typ:** Nová funkce  
**Kategorie služeb:** B2C - Správa identit y spotřebitele  
**Možnost i možnosti produktu:** B2B/B2C

Azure AD B2C teď podporuje měsíční aktivní uživatele (MAU) fakturace. Fakturace MAU je založena na počtu jedinečných uživatelů s aktivitou ověřování během kalendářního měsíce. Stávající zákazníci mohou na tuto novou metodu fakturace kdykoli přejít.

listopadu 2019 budou všichni noví zákazníci automaticky účtováni touto metodou. Tato metoda fakturace přináší zákazníkům výhody výhod a možnost plánovat dopředu.

Další informace naleznete v [tématu Upgrade na měsíční model fakturace aktivních uživatelů](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing#upgrade-to-monthly-active-users-billing-model).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD – říjen 2019

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnosti produktu:** Integrace třetí strany

V říjnu 2019 jsme do galerie aplikací přidali těchto 35 nových aplikací s podporou Federace:

[V případě krize - Mobile](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-mobile-tutorial), [Juno Journey](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial), [ExponentHR](https://docs.microsoft.com/azure/active-directory/saas-apps/exponenthr-tutorial), [Tact](https://tact.ai/assistant/), [OpusCapita Cash Management](http://cm1.opuscapita.com/tenantname), [Salestim](https://prd.salestim.io/forms), [Learnster](https://docs.microsoft.com/azure/active-directory/saas-apps/learnster-tutorial), [Dynatrace](https://docs.microsoft.com/azure/active-directory/saas-apps/dynatrace-tutorial), [HunchBuzz](https://login.hunchbuzz.com/integrations/azure/process), [Freshworks](https://docs.microsoft.com/azure/active-directory/saas-apps/freshworks-tutorial), [eCornell](https://docs.microsoft.com/azure/active-directory/saas-apps/ecornell-tutorial), [ShipHazmat](https://docs.microsoft.com/azure/active-directory/saas-apps/shiphazmat-tutorial), [Netskope Cloud Security](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial), [Spokojený](https://docs.microsoft.com/azure/active-directory/saas-apps/contentful-tutorial), [Bindtuning](https://bindtuning.com/login), [HireVue Coordinate – Evropa](https://www.hirevue.com/), [HireVue Coordinate - USOnly](https://www.hirevue.com/), [HireVue Coordinate - USA](https://www.hirevue.com/), [WittyParrot Knowledge Box](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), [Cloudmore](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudmore-tutorial), [Visit.org](https://docs.microsoft.com/azure/active-directory/saas-apps/visitorg-tutorial), [Cambium Xirrus EasyPass Portal](https://login.xirrus.com/azure-signup), [Paylocity](https://docs.microsoft.com/azure/active-directory/saas-apps/paylocity-tutorial), Mail [Luck!](https://docs.microsoft.com/azure/active-directory/saas-apps/mail-luck-tutorial), [Teamie,](https://theteamie.com/) [Velocity pro týmy](https://velocity.peakup.org/teams/login), [SIGNL4](https://account.signl4.com/manage), [EAB Navigate IMPL](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-impl-tutorial), [ScreenMeet](https://console.screenmeet.com/), [Omega Point,](https://pi.ompnt.com/) [Mluvící e-mail pro Intune (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune), [Mluvící e-mail pro Office 365 Direct (iPhone / Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct), [ExactCare SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/exactcare-sso-tutorial), [iHealthHome Care Navigační systém](https://ihealthnav.com/account/signin), [Qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

Další informace o aplikacích najdete v tématu [Integrace aplikací SaaS se službou Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete [v tématu Seznam aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Položka nabídky konsolidované zabezpečení na portálu Azure AD

**Typ:** Změněná funkce  
**Kategorie služeb:** Ochrana identity  
**Možnost i možnosti produktu:** Ochrana & zabezpečení identity

Teď máte přístup ke všem dostupným funkcím zabezpečení Azure AD z nové položky nabídky **zabezpečení** a z panelu **Hledání** na webu Azure Portal. Nová vstupní stránka **zabezpečení** s názvem **Zabezpečení – začíná**bude navíc poskytovat odkazy na naši veřejnou dokumentaci, pokyny k zabezpečení a průvodce nasazením.

Nová nabídka **Zabezpečení** obsahuje:

- Podmíněný přístup
- Identity Protection
- Security Center
- Skóre zabezpečení identity
- Metody ověřování
- Mfa
- Zprávy o rizicích - Riziková uživatelé, Riskantní přihlášení, Detekce rizik
- A další...

Další informace naleznete v [tématu Zabezpečení – začínáme](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted).

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Zásady vypršení platnosti skupin Office 365 rozšířené o automatické obnovení

**Typ:** Změněná funkce  
**Kategorie služeb:** Správa skupiny  
**Možnost i možnosti produktu:** Správa životního cyklu identity

Zásady vypršení platnosti skupin Office 365 byly vylepšeny tak, aby automaticky obnovovaly skupiny, které jsou aktivně používány členy. Skupiny se budou automaticky obnovovat na základě aktivity uživatelů ve všech aplikacích Office 365, včetně Outlooku, SharePointu a Teams.

Toto vylepšení pomáhá snížit oznámení o vypršení platnosti skupiny a pomáhá zajistit, aby aktivní skupiny byly i nadále k dispozici. Pokud už máte aktivní zásady vypršení platnosti pro skupiny Office 365, nemusíte dělat nic, abyste tuto novou funkci zapnuli.

Další informace najdete [v tématu Konfigurace zásad vypršení platnosti pro skupiny Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-lifecycle).

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Aktualizované prostředí pro vytváření domén služby Azure AD (Azure AD DS)

**Typ:** Změněná funkce  
**Kategorie služeb:** Služby domény Azure AD  
**Možnost i možnosti produktu:** Služby domény Azure AD

Aktualizovali jsme služby Azure AD Domain Services (Azure AD DS), aby zahrnovaly nové a vylepšené prostředí pro vytváření, které vám pomůže vytvořit spravovanou doménu pouhými třemi kliknutími! Kromě toho teď můžete nahrát a nasadit Azure AD DS ze šablony.

Další informace naleznete v [tématu: Vytvoření a konfigurace instance služby Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance).

---
