---
title: Co je nového? Poznámky k verzi – Azure Active Directory | Microsoft Docs
description: Přečtěte si, co je nového v Azure Active Directory, jako jsou například nejnovější poznámky k verzi, známé problémy, opravy chyb, zastaralé funkce a nadcházející změny.
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
ms.date: 04/06/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 491908b651cd2b875fcfeed4c55d34f0e8c5cfa1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80802505"
---
# <a name="whats-new-in-azure-active-directory"></a>Co je nového v Azure Active Directory?

>Přečtěte si informace o tom, kdy se má tato stránka na aktualizace znovu navštívit zkopírováním `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` a vložením této adresy URL: do čtečky informačního kanálu ![RSS](./media/whats-new/feed-icon-16x16.png) .

Služba Azure AD průběžně přijímá vylepšení. V tomto článku najdete informace o tom, jak se můžete seznámit s nejnovějším vývojem.

- Nejnovější verze
- Známé problémy
- Opravy chyb
- Zastaralé funkce
- Plánuje změny

Tato stránka se aktualizuje měsíčně, takže ji můžete pravidelně znovu navštěvovat. Pokud hledáte položky, které jsou starší než šest měsíců, najdete je v archivu, abyste mohli [novinky v Azure Active Directory](whats-new-archive.md).

---

## <a name="march-2020"></a>Březen 2020

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>Nespravované účty Azure Active Directory v aktualizaci B2B pro březen 2021

**Zadejte:** Plánování změn  
**Kategorie služby:** B2B  
**Schopnost produktu:** B2B/B2C
 
**Od 31. března 2021**přestane společnost Microsoft podporovat uplatnění pozvánky tím, že pro scénáře spolupráce B2B vytvoří neAzure Active Directory spravované účty a klienty služby Azure AD. V přípravné době doporučujeme, abyste se rozhodli, že se chcete přihlásit k [e-mailu ověřování heslem jednorázového hesla](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode).

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

S radostí sdílíme, že teď jsme na [Microsoft Azure Government portálu](https://portal.azure.us/)navedli aktualizované [Azure AD Identity Protection](https://aka.ms/IdentityProtectionDocs) prostředí. Další informace najdete v příspěvku na [blogu o oznámení](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667).

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>Zotavení po havárii: Stáhněte si konfiguraci zřizování a uložte ji.

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** Správa životního cyklu identit
 
Služba zřizování Azure AD poskytuje bohatou sadu možností konfigurace. Zákazníci musí mít možnost uložit svou konfiguraci, aby na ně mohli později odkazovat nebo se vrátit na známou správnou verzi. Přidali jsme možnost stáhnout si vaši konfiguraci zřizování jako soubor JSON a nahrát ho, až ho budete potřebovat. [Další informace](https://docs.microsoft.com/azure/active-directory/app-provisioning/export-import-provisioning-configuration).

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR (Samoobslužné resetování hesla) teď vyžaduje dvě brány pro správce v Microsoft Azure provozované společností 21Vianet (Azure Čína 21Vianet). 

**Zadejte:** Změněná funkce  
**Kategorie služby:** Samoobslužné resetování hesla  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
Správci, kteří používají Samoobslužné resetování hesla (SSPR), dříve v Microsoft Azure pracovali pomocí samoobslužného resetování hesla () k resetování jejich identity, potřebují jenom jednu bránu (Challenge). Ve veřejných a dalších národních cloudech musí správci při použití SSPR vykazovat svoji identitu pomocí dvou bran. Protože ale nepodporujeme SMS nebo telefonní hovory v Azure Čína 21Vianet, povolili jsme správcům resetování hesla s jednou branou.

Vytváříme paritu funkcí SSPR mezi Azure Čína 21Vianet a veřejným cloudem. Při použití SSPR musí správci používat dvě brány. Podporuje se SMS, telefonní hovory a ověřovací oznámení a kódy aplikace ověřovatele. [Další informace](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#administrator-reset-policy-differences).

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

Abychom zákazníkům poskytli pružnější způsob vytváření skupin pro všechny adresáře, které nejlépe vyhovují jejich potřebám, nahradili jsme možnost **skupiny** pro všechny adresáře ze**obecných** nastavení **skupiny** > v Azure Portal s odkazem na dokumentaci k [dynamické skupině](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership). Vylepšili jsme naši dokumentaci, aby zahrnovala další pokyny, aby správci mohli vytvářet skupiny všech uživatelů, které zahrnují nebo vylučují uživatele typu Host.

---

## <a name="february-2020"></a>Únor 2020

### <a name="upcoming-changes-to-custom-controls"></a>Nadcházející změny vlastních ovládacích prvků

**Zadejte:** Plánování změn  
**Kategorie služby:** VÍCEFAKTOROVÉHO  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
Plánujeme nahradit aktuální vlastní ovládací prvky jako v rámci přístupu, který umožňuje bezproblémovou funkčnost funkcí ověřování poskytovaných partnerem s Azure Active Directory správce a koncového uživatele. V dnešní době se Partnerská řešení MFA setkávají s následujícími omezeními: fungují až po zadání hesla; neslouží jako MFA pro podrobné ověřování v jiných klíčových scénářích. a neintegrují se do funkcí pro správu přihlašovacích údajů pro koncové uživatele nebo správce. Nová implementace umožní partnerským faktorům pro ověřování spolupracovat společně s integrovanými faktory pro klíčové scénáře, včetně registrace, využití, deklarací MFA, krokování ověřování, vytváření sestav a protokolování. 

Vlastní ovládací prvky budou nadále podporovány ve verzi Preview vedle nového návrhu, dokud nedosáhne obecné dostupnosti. V tomto okamžiku zákazníkům poskytneme čas na migraci na nový návrh. Z důvodu omezení současného přístupu nebudeme nové poskytovatele připravujeme, dokud nebude nový návrh k dispozici. Úzce spolupracujeme se zákazníky a poskytovateli a pošle vám časovou osu, jak získáme blíž. [Další informace](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#).

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

V rámci zabezpečeného modelu pro soubory cookie se v prohlížeči Chrome 80 mění způsob, jakým zpracovává soubory cookie bez `SameSite` atributu. Každý soubor cookie, který neurčuje `SameSite` atribut, bude zpracován, jako by byl nastaven `SameSite=Lax`na hodnotu, což bude mít za následek blokování určitých scénářů sdílení souborů cookie mezi doménami, na kterých vaše aplikace může záviset. Chcete-li zachovat starší chování aplikace Chrome, můžete použít `SameSite=None` atribut a přidat další `Secure` atribut, aby soubory cookie pro více webů byly dostupné pouze přes připojení HTTPS. Pro Chrome je naplánovaná Tato změna od 4. února 2020.

Doporučujeme, aby naši vývojáři otestovali své aplikace pomocí těchto pokynů:

- Nastavte výchozí hodnotu nastavení **použít zabezpečený soubor cookie** na **Ano**.

- Nastavte výchozí hodnotu atributu **SameSite** na **none**.

- Přidejte další `SameSite` atribut **zabezpečení**.

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

Studijní [tabulka](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial), [HootSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial), [modrý přístup pro členy (BAM)](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial), [bitly](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial), [Riva](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial), [ResLife Portal](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), [NegometrixPortal jednotné přihlašování (SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial), [TeamsChamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279), [Motus](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial), [MyAryaka](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial), [BlueMail](https://loginself1.bluemail.me/), [Beedle](https://teams-web.beedle.co/#/), [Visma](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial), [OneDesk](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial), [FOKO Retail](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial), Qmarkets [nápad & Správa inovací](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial), [Netskope ověřování uživatelů](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial), uniFLOW [online](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial), [Claromentis](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial), [JISC pro registraci Voter](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial), [e4enable](https://portal.e4enable.com/)

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

Několik týdnů před vydáním prohlížeče Chrome 80, plánujeme aktualizovat způsob, jakým soubory cookie proxy aplikace zacházejí s atributem **SameSite** . S vydáním Chrome 80 se všechny soubory cookie, které nespecifikují atribut **SameSite** , zpracují, jako kdyby byla nastavena na `SameSite=Lax`.

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

V blízké budoucnosti již uživatelé s účty Azure AD nebudou moci registrovat a spravovat sblížené aplikace pomocí portálu pro registraci aplikací (apps.dev.microsoft.com) nebo registrovat a spravovat aplikace v Azure Portal Registrace aplikací (starší verze).

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

Přidali jsme novou stránku **Moje přihlášení** (https://mysignins.microsoft.com) aby si uživatelé vaší organizace mohli zobrazit historii jejich nedávného přihlášení, aby zkontrolovali, že neobvyklá aktivita. Tato nová stránka umožňuje uživatelům zobrazit:

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

[V případě krize – Mobile](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-mobile-tutorial), [Juno cest](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial), [ExponentHR](https://docs.microsoft.com/azure/active-directory/saas-apps/exponenthr-tutorial), [TACT](https://tact.ai/assistant/), [OpusCapita řízení pokladní správy](http://cm1.opuscapita.com/tenantname), [Salestim](https://prd.salestim.io/forms), [Learnster](https://docs.microsoft.com/azure/active-directory/saas-apps/learnster-tutorial), [dynaTrace](https://docs.microsoft.com/azure/active-directory/saas-apps/dynatrace-tutorial), [HunchBuzz](https://login.hunchbuzz.com/integrations/azure/process), [Freshworks](https://docs.microsoft.com/azure/active-directory/saas-apps/freshworks-tutorial), [ECornell](https://docs.microsoft.com/azure/active-directory/saas-apps/ecornell-tutorial), [ShipHazmat](https://docs.microsoft.com/azure/active-directory/saas-apps/shiphazmat-tutorial), [Netskope Cloud Security](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial), [contentd](https://docs.microsoft.com/azure/active-directory/saas-apps/contentful-tutorial), [Bindtuning](https://bindtuning.com/login), HireVue [ová souřadnice – Evropa](https://www.hirevue.com/), [HireVue – koordinace – USOnly](https://www.hirevue.com/), [HireVue souřadnice – USA](https://www.hirevue.com/), WittyParrot, [Cloudmore](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudmore-tutorial), [WittyParrot Knowledge Box](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), Visit.org, [cambium,](https://docs.microsoft.com/azure/active-directory/saas-apps/visitorg-tutorial) [Xirrus EasyPass Paylocity](https://login.xirrus.com/azure-signup) [, SIGNL4](https://docs.microsoft.com/azure/active-directory/saas-apps/paylocity-tutorial), vyjímá [!](https://docs.microsoft.com/azure/active-directory/saas-apps/mail-luck-tutorial), [Týmová](https://theteamie.com/)schránka, [rychlost pro týmy](https://velocity.peakup.org/teams/login), [EAB](https://account.signl4.com/manage), [impl, ScreenMeet](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-impl-tutorial) [, ExactCare,,](https://console.screenmeet.com/) [bod Omega](https://pi.ompnt.com/), [Hlasová pošta pro Intune (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune) [,](https://ihealthnav.com/account/signin) [hlasová zpráva pro Office 365 Direct (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct) [,](https://qubie.azurewebsites.net/static/adminTab/authorize.html) [iHealthHome SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/exactcare-sso-tutorial)

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
- VÍCEFAKTOROVÉHO
- Sestavy rizik – rizikové uživatele, rizikové přihlašovacíky, detekce rizik
- A další...

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
