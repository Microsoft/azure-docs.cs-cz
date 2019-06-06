---
title: Co je nového? Poznámky – Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, co je nové službě Azure Active Directory, jako je nejnovější poznámky k verzi, známé problémy, opravy chyb, zastaralé funkce a nadcházející změny.
services: active-directory
author: eross-msft
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6a66aefec69a0551f85b11a380c90d1915bd776
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66474204"
---
# <a name="whats-new-in-azure-active-directory"></a>Co je nového ve službě Azure Active Directory?

>Dostanete o tom, kdy revidovat tuto stránku aktualizace zkopírováním a vložením tuto adresu URL: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` do vaší ![čtečky ikonu informačního kanálu RSS](./media/whats-new/feed-icon-16x16.png) kanálů.

Azure AD obdrží vylepšení průběžně. Abyste mohli používat aktuální pomocí nejnovější vývoj, tento článek poskytuje informace o:

- Nejnovější vydané verzi
- Známé problémy
- Opravy chyb
- Zastaralé funkce
- Plány pro změny

Tato stránka se aktualizuje každý měsíc, takže návštěvě pravidelně. Pokud hledáte položky, které jsou starší než šest měsíců, najdete je [archiv co je nového ve službě Azure Active Directory](whats-new-archive.md).

---

## <a name="may-2019"></a>. Května 2019.

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Změna Service: Budoucí podpory pouze protokoly TLS 1.2 na službu Proxy aplikací

**Typ:** Plánování změn  
**Kategorie služby:** App Proxy  
**Funkce produktu:** Řízení přístupu

Jak zajistit ve své třídě nejlepší šifrování pro naše zákazníky, jsme se omezení přístupu k pouze protokoly TLS 1.2 na službu Proxy aplikací. Tato změna se postupně zavádí pro zákazníky, kteří používají již pouze protokoly TLS 1.2, takže byste neměli vidět žádné změny.

Vyřazení protokolu TLS 1.0 a TLS 1.1 se stane 31. srpna 2019, ale vám poskytneme další pokročilé oznámení, takže budete mít čas na tuto změnu připravit. Příprava pro tuto změnu, ujistěte se, že klient server a server prohlížeče kombinace, včetně všech klientů, které uživatelé používají pro přístup k aplikacím, které jsou publikované prostřednictvím Proxy aplikací, jsou aktualizované na používání protokolu TLS 1.2 pro zachování připojení k aplikaci Služba proxy. Další informace najdete v tématu [přidat místní aplikace pro vzdálený přístup prostřednictvím Proxy aplikací v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Zobrazit data související s aplikacemi přihlášení pomocí sestavy využití a přehledů

**Typ:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Funkce produktu:** Monitorování a vytváření sestav

Teď můžete použít sestavy využití a přehledy v **podnikové aplikace** části webu Azure portal, chcete-li získat zaměřená na aplikace zobrazit data přihlášení, včetně informací o:

- Horní používané aplikace pro vaši organizaci

- Aplikace s největší neúspěšných přihlášení

- Hlavní chyby přihlášení pro každou aplikaci

Další informace o této funkci najdete v tématu [využití a insights sestavy na portálu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatizace vašich zřizování uživatelů pro cloudové aplikace pomocí Azure AD

**Typ:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Funkce produktu:** Monitorování a vytváření sestav

Postupujte podle těchto nové kurzy, které umožňuje automatizovat vytvoření, odstranění, služba zřizování Azure AD a aktualizace uživatelských účtů pro tyto aplikace založené na cloudu:

- [Comeet](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

Můžete také použít tento nový [Dropboxu kurzu](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), který poskytuje informace o tom, jak zřídit skupiny objektů.

Další informace o tom, jak lépe zabezpečit svou organizaci prostřednictvím zřizování automatizované uživatelských účtů najdete v tématu [automatizace zřizování uživatelů pro aplikace SaaS ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Skóre zabezpečení identit je teď dostupná ve službě Azure AD (obecná dostupnost)

**Typ:** Nová funkce  
**Kategorie služby:** neuvedeno  
**Funkce produktu:** Zabezpečení a ochrana identity

Teď můžete sledovat a vylepšit svou identitu stavu zabezpečení s využitím identity skóre funkce zabezpečení ve službě Azure AD. Identita zabezpečení skóre funkce používá jeden řídicí panel můžete:

- Objektivně měření stavu zabezpečení identity, na základě skóre 1 až 223.

- Plán pro vylepšení zabezpečení vaší identity

- Zkontrolujte úspěšnost vaší vylepšení zabezpečení

Další informace o funkci skóre zabezpečení identity, najdete v části [novinky skóre zabezpečení identit v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Nové prostředí registrace aplikace je nyní k dispozici (obecná dostupnost)

**Typ:** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Funkce produktu:** Prostředí pro vývojáře

Nové [registrace aplikací](https://aka.ms/appregistrations) prostředí je teď všeobecně dostupná. Toto nové prostředí obsahuje všechny klíčové funkce, které už znáte z webu Azure portal a portálu pro registraci aplikace a dále to vylepšuje prostřednictvím:

- **Lepší správa aplikací.** Místo zobrazení vašich aplikací napříč různých portálů, uvidíte teď všechny aplikace na jednom místě.

- **Zjednodušená registrace aplikace.** Z prostředí Vylepšená navigace pro prostředí pro výběr přepracované oprávnění je teď jednodušší registrovat a spravovat vaše aplikace.

- **Podrobnější informace.** Další podrobnosti můžete najít informace o vaší aplikaci, jako jsou příručky rychlý úvod a další.

Další informace najdete v tématu [platforma identit Microsoft](https://docs.microsoft.com/azure/active-directory/develop/) a [prostředí registrace aplikací je teď obecně dostupná!](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) oznámení na blogu.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Nové funkce, které jsou k dispozici v rozhraní API rizikové uživatelé Identity Protection

**Typ:** Nová funkce  
**Kategorie služby:** Identity Protection  
**Funkce produktu:** Zabezpečení a ochrana identity

S radostí oznamujeme, že teď můžete nebezpečné rozhraní API uživatelé načíst historii rizik uživatelů, Zavřít rizikoví uživatelé a potvrďte uživatele tak, že dojde k ohrožení bezpečnosti. Tato změna pomáhá efektivněji aktualizovat stav rizika uživatele a pochopení jejich historie rizika.

Další informace najdete v tématu [referenční dokumentace rozhraní API rizikových uživatelů](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Nové federované aplikace k dispozici v galerii aplikací Azure AD –. května 2019.

**Typ:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Funkce produktu:** 3. stran integrace

V května 2019 přidali jsme podporu těchto 21 nové aplikace s federací do Galerie aplikací:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [reálné propojí](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [jednoduchého přihlašování](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [Displayr](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [zapojení Marketo Sales](https://toutapp.com/login), [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [OutSystems](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [Meta4 globální HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), [Quantum pracoviště](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [kobaltu](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), [prvkům webMethods cloudové rozhraní API](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [Ovládací prvek](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [Foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Další informace o aplikacích najdete v tématu [integrace aplikací SaaS pomocí Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete v tématu [uvedení aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Vylepšené skupiny vytváření a správa prostředí na portálu Azure AD

**Typ:** Nová funkce  
**Kategorie služby:** Správa skupin  
**Funkce produktu:** Spolupráce

Provedli jsme vylepšení prostředí pro skupiny související na portálu Azure AD. Tato vylepšení umožňují správci lépe spravovat skupiny seznamy, seznamy členů a poskytnout možnosti vytvoření další.

Mezi vylepšení patří:

- Základní filtrování podle typu členství a typ skupiny.

- Přidání nových sloupců, jako je například zdroj a e-mailovou adresu.

- Možnost vybrat víc skupin, členy a vlastník jsou uvedené pro snadné odstranění.

- Možnost přidání vlastníků během vytváření skupiny a zvolte e-mailovou adresu.

Další informace najdete v tématu [vytvoření základní skupiny a přidání členů pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Konfigurace zásad pojmenování pro skupiny Office 365 na portálu Azure AD (obecná dostupnost)

**Typ:** Změněná funkce  
**Kategorie služby:** Správa skupin  
**Funkce produktu:** Spolupráce

Správci teď můžete nakonfigurovat zásady pojmenování pro skupiny Office 365, pomocí portálu Azure AD. Tato změna pomáhá vynucovat konzistentní zásady vytváření názvů pro skupiny Office 365 vytvořených nebo upravených uživatelé ve vaší organizaci.

Zásady pojmenování pro skupiny Office 365 můžete nakonfigurovat dvěma způsoby:

- Definujte předpon a přípon, které se automaticky přidají do názvu skupiny.

- Nahrajte vlastní sadu blokované slova pro vaši organizaci, které nejsou povoleny v názvech skupin (například "generální ředitel, výplatních pásek, HR").

Další informace najdete v tématu [vynucení zásad pojmenování pro skupiny Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Koncové body rozhraní Microsoft Graph API jsou teď dostupné pro protokoly aktivit Azure AD (obecná dostupnost)

**Typ:** Změněná funkce  
**Kategorie služby:** Vytváření sestav  
**Funkce produktu:** Monitorování a vytváření sestav

Jsme s radostí oznamujeme obecnou dostupnost Microsoft Graph API podporu koncových bodů aktivit Azure AD protokoly. V této verzi teď můžete pomocí auditování služby Azure AD verze 1.0 protokoly a protokoly přihlášení rozhraní API.

Další informace najdete v tématu [přehled rozhraní API protokolu auditu Azure AD](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Správci můžou teď používat podmíněný přístup pro kombinované registraci (Public preview)

**Typ:** Nová funkce  
**Kategorie služby:** Podmíněný přístup  
**Funkce produktu:** Zabezpečení a ochrana identity  

Správci teď můžou vytvářet zásady podmíněného přístupu pro použití na stránce kombinovaná registrace. To zahrnuje, uplatnění zásad pro povolení registrace, pokud:

- Uživatelé jsou v důvěryhodné síti.

- Uživatelé jsou nízké riziko přihlášení.

- Uživatelé jsou na spravované zařízení.

- Uživatelé svůj souhlas s podmínkami použití (TOU) organizace.

Další informace o podmíněném přístupu a resetování hesla, zobrazí se [kombinovat podmíněného přístupu pro službu Azure AD MFA a příspěvek na blogu prostředí registrace pro resetování hesla](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348). Další informace o vytváření zásad podmíněného přístupu pro kombinované registraci, najdete v části [zásady podmíněného přístupu pro kombinované registrace](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration). Další informace o podmínkách služby Azure AD použít funkci najdete v tématu [podmínky služby Azure Active Directory použít funkci](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

## <a name="april-2019"></a>2019. dubna

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-in-refreshed-azure-ad-identity-protection"></a>Nová inteligentní funkce detekce hrozeb služby Azure AD je teď dostupná v aktualizaci Azure AD Identity Protection

**Typ:** Nová funkce  
**Kategorie služby:** Azure AD Identity Protection  
**Funkce produktu:** Zabezpečení a ochrana identity

Inteligentní funkce detekce hrozeb služby Azure AD je teď dostupná v aktualizaci Azure AD Identity Protection. Tato nová funkce umožňuje určit aktivity uživatelů, který neobvyklé, že u konkrétního uživatele nebo který je konzistentní s známým vzorcům útoku podle analýzy interních a externích hrozeb od Microsoftu.

Další informace o aktualizovaná verze služby Azure AD Identity Protection, najdete v článku [čtyři zásadními vylepšeními služby Azure AD Identity Protection je teď ve verzi public preview](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) blogu a [co je Azure Active Directory Ochrana identity (Aktualizovat)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) článek. Další informace o inteligentní funkce detekce hrozeb služby Azure AD, najdete v článku [rizikových událostech Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/risk-events-reference#azure-ad-threat-intelligence) článku.

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Správa oprávnění Azure AD je nyní k dispozici (Public preview)

**Typ:** Nová funkce  
**Kategorie služby:** Zásady správného řízení identit  
**Funkce produktu:** Zásady správného řízení identit

Správa oprávnění Azure AD, nyní ve verzi public preview, pomáhá zákazníkům delegovat správu balíčků přístup, který definuje, jak zaměstnancům a obchodními partnery požádat o přístup, který musí schválit a jak dlouho mají přístup. Přístup balíčky můžete spravovat členství ve službě Azure AD a Office 365 skupiny přiřazení rolí v oddílu podnikové aplikace a přiřazení rolí pro weby SharePoint Online. Další informace o Správa nároků na [přehled správy oprávnění Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Další informace o škálu funkcí správy identit Azure AD, včetně Privileged Identity Management, kontroly přístupu a podmínky použití, naleznete v tématu [novinky správy identit Azure AD?](../governance/identity-governance-overview.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Konfigurace zásad pojmenování pro skupiny Office 365 na portálu Azure AD (Public preview)

**Typ:** Nová funkce  
**Kategorie služby:** Správa skupin  
**Funkce produktu:** Spolupráce

Správci teď můžete nakonfigurovat zásady pojmenování pro skupiny Office 365, pomocí portálu Azure AD. Tato změna pomáhá vynucovat konzistentní zásady vytváření názvů pro skupiny Office 365 vytvořených nebo upravených uživatelé ve vaší organizaci.

Zásady pojmenování pro skupiny Office 365 můžete nakonfigurovat dvěma způsoby:

- Definujte předpon a přípon, které se automaticky přidají do názvu skupiny.

- Nahrajte vlastní sadu blokované slova pro vaši organizaci, které nejsou povoleny v názvech skupin (například "generální ředitel, výplatních pásek, HR").

Další informace najdete v tématu [vynucení zásad pojmenování pro skupiny Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Protokoly služby Azure AD aktivit jsou teď dostupné ve službě Azure Monitor (obecná dostupnost)

**Typ:** Nová funkce  
**Kategorie služby:** Vytváření sestav  
**Funkce produktu:** Monitorování a vytváření sestav

Pomáhají řešit svůj názor týkající se vizualizací protokolů aktivit Azure AD, jsme zavedli novou funkci Insights ve službě Log Analytics. Tato funkce umožňuje získat přehledy o vašich prostředků Azure AD s použitím našich interaktivní šablon, volá se sešity. Tyto předem připravené pracovní sešity poskytují podrobnosti pro aplikace nebo uživatele a zahrnují:

- **Přihlášení.** Poskytuje podrobnosti pro aplikace a uživatele, včetně místo přihlášení, používané operačního systému nebo prohlížeče klienta a verze a počet úspěšných nebo neúspěšných přihlášení.

- **Starší verze ověřování a podmíněný přístup.** Poskytuje podrobnosti pro aplikace a uživatele, kteří používají starší verze ověřování, včetně použití služby Multi-Factor Authentication aktivované zásady podmíněného přístupu, aplikace pomocí zásad podmíněného přístupu, a tak dále.

- **Analýza selhání přihlášení.** Umožňuje určit, pokud se vyskytnou chyby přihlášení z důvodu akce uživatele, zásad problémy nebo infrastruktury.

- **Vlastní sestavy.** Můžete vytvořit novou nebo upravit existující sešity a pomohou přizpůsobit funkce přehledů pro vaši organizaci.

Další informace najdete v tématu [jak používat Azure Monitor sešity sestav Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Nové federované aplikace k dispozici v galerii aplikací Azure AD – duben 2019

**Typ:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Funkce produktu:** 3. stran integrace

V dubnu 2019 jsme přidali podporu těchto 21 nové aplikace s federací do Galerie aplikací:

[Řešení SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [provede se perkolace](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [ Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [Benchling](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial), [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [RStudio Připojit](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [Mitel připojení](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [Alibaba cloudu (jednotné přihlašování založené na roli)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent jmění správu](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Správce certifikátů Sectigo](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [ SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [Indiggo](https://indiggolead.com/)

Další informace o aplikacích najdete v tématu [integrace aplikací SaaS pomocí Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete v tématu [uvedení aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Kontroly přístupu nové možnosti četnost a vícenásobný výběr role

**Typ:** Nová funkce  
**Kategorie služby:** Kontroly přístupu  
**Funkce produktu:** Zásady správného řízení identit

Nové aktualizace ve službě Azure AD kontroly přístupu umožňují:

- Změna revize frekvence váš přístup k **částečně annually**, kromě dříve existující možnosti jednou týdně, měsíčně, čtvrtletně a ročně.

- Vyberte několik služeb Azure AD a zkontrolujte role prostředků Azure při vytváření jeden přístup. V takovém případě jsou nastavené všechny role se stejnými nastaveními a všem jejím kontrolorům se zobrazí oznámení ve stejnou dobu.

Další informace o tom, jak vytvořit kontrolu přístupu najdete v tématu [vytvoření kontroly přístupu skupin nebo kontrol přístupu aplikací ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/governance/create-access-review).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect e-mailové výstrahy systémy se převádějí, odesílání nové informace odesílatele e-mailu pro některé zákazníky

**Typ:** Změněná funkce  
**Kategorie služby:** Synchronizace AD  
**Funkce produktu:** Platforma

Azure AD Connect se právě přechod naše systémy oznámení e-mailu potenciálně zobrazující někteří zákazníci nový odesílatel e-mailu. Chcete-li to vyřešit, je nutné přidat `azure-noreply@microsoft.com` do seznamu povolených tříd vaší organizace nebo nebudete moci i nadále zajistit důležité výstrahy z Office 365, Azure nebo služby synchronizace.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>(UPN) příponu změny jsou nyní úspěšné mezi doménami federativní ve službě Azure AD Connect

**Typ:** Oprava  
**Kategorie služby:** Synchronizace AD  
**Funkce produktu:** Platforma

Nyní můžete úspěšně změnit příponu UPN uživatele z jedné domény federativní do jiné domény federativní ve službě Azure AD Connect. Tato oprava znamená, že byste už prostředí FederatedDomainChangeError chybová zpráva při synchronizační cyklus nebo přijímat oznámení e-mailu s oznámením, "Nepodařilo se aktualizovat tento objekt ve službě Azure Active Directory, protože atribut [ FederatedUser.UserPrincipalName] není platný. Aktualizujte hodnotu v místních adresářových službách".

Další informace najdete v tématu [řešení chyb při synchronizaci](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Zvýšení zabezpečení pomocí zásady podmíněného přístupu na základě ochrany aplikací ve službě Azure AD (Public preview)

**Typ:** Nová funkce  
**Kategorie služby:** Podmíněný přístup  
**Funkce produktu:** Zabezpečení a ochrana identity

Podmíněný přístup založený na ochranu aplikace je teď dostupná s použitím **vyžadují ochranu aplikací** zásad. Tato nová zásada pomáhá zvýšit zabezpečení vaší organizace na ochranu:

- Uživatelé získají přístup k aplikacím bez licence služby Microsoft Intune.

- Uživatelům se nepovedlo se získat zásady ochrany aplikací Microsoft Intune.

- Uživatelé získají přístup k aplikacím bez nakonfigurované zásady ochrany aplikací Microsoft Intune.

Další informace najdete v tématu [vyžadování zásad ochrany aplikací pro přístup k aplikaci cloud s podmíněným přístupem](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Nová podpora pro Azure AD jednotného přihlašování a podmíněný přístup v Microsoft Edge (Public preview)

**Typ:** Nová funkce  
**Kategorie služby:** Podmíněný přístup  
**Funkce produktu:** Zabezpečení a ochrana identity

Rozšířili jsme naši podporu služby Azure AD pro Microsoft Edge, včetně poskytnutí novou podporu pro jednotné přihlašování a podmíněný přístup Azure AD. Pokud jste dříve používali Microsoft Intune Managed Browser, teď můžete Microsoft Edge místo toho.

Další informace o vytváření a správu zařízení a aplikací pomocí podmíněného přístupu najdete v tématu [vyžadují spravovaná zařízení pro přístup k aplikaci cloud s podmíněným přístupem](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) a [vyžaduje schválené klientské aplikace pro cloud přístup k aplikaci s podmíněným přístupem](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Další informace o tom, jak Správa přístupu k Microsoft Edge pomocí zásad Microsoft Intune najdete v tématu [Správa přístupu k Internetu pomocí prohlížeče chráněné zásad Microsoft Intune](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>2019. března

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Rozhraní prostředí pro identity a vlastních zásad pro podporu v Azure Active Directory B2C je nyní dostupná (GA)

**Typ:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Funkce produktu:** B2B/B2C

Nyní můžete vytvořit vlastní zásady v Azure AD B2C, včetně těchto úloh, které jsou podporovány ve velkém měřítku a za naše smlouvy SLA pro Azure:

- Vytvoření a nahrání jízdy vlastní ověřování uživatele pomocí vlastních zásad.

- Popis cesty uživatele krok za krokem jako výměny mezi zprostředkovatelem deklarací identity.

- Definuje podmíněné větvení v centrech uživatele.

- Transformace a mapování deklarací identity pro použití v reálném čase rozhodovat a komunikace.

- V centrech vaše vlastní ověřování uživatele pomocí služby podporující rozhraní REST API. Například s poskytovateli e-mailu, CRM a systémy proprietární autorizace.

- Vytvoření federace pomocí zprostředkovatelů identity, kteří jsou kompatibilní s protokolu OpenIDConnect. Například s více tenanty Azure AD, poskytovatelé služeb sociálních sítí účtu nebo poskytovatelé dvojúrovňového ověřování.

Další informace o vytváření vlastních zásad najdete v tématu [poznámky pro vývojáře pro vlastní zásady v Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom) si můžete přečíst [Alex Simon blogový příspěvek, včetně případové studie](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Nové federované aplikace k dispozici v galerii aplikací Azure AD – 2019. března

**Typ:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Funkce produktu:** 3. stran integrace

V březnu 2019 jsme přidali podporu těchto 14 nových aplikací s federací do Galerie aplikací:

[Delegát Exchange Mobile ISEC7](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [Auditování systému vysvětlení](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [Lean](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [otázky výkonu Powerschool](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode](https://cinode.com/), [Iris Intranet](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit možností](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [systému](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Další informace o aplikacích najdete v tématu [integrace aplikací SaaS pomocí Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete v tématu [uvedení aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Nové Zscalerem a zřizování konektorů v galerii Azure AD – březen 2019 od společnosti Atlassian

**Typ:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Funkce produktu:** 3. stran integrace

Automatizujte vytváření, aktualizace nebo odstranění uživatelských účtů pro následující aplikace:

[Zscalerem](https://aka.ms/ZscalerProvisioning), [Zscalerem Beta](https://aka.ms/ZscalerBetaProvisioning), [Zscalerem jeden](https://aka.ms/ZscalerOneProvisioning), [Zscalerem dvě](https://aka.ms/ZscalerTwoProvisioning), [Zscalerem tři](https://aka.ms/ZscalerThreeProvisioning), [Zscaler ZSCloud ](https://aka.ms/ZscalerZSCloudProvisioning), [Cloud od společnosti Atlassian](https://aka.ms/atlassianCloudProvisioning)

Další informace o tom, jak lépe zabezpečit svou organizaci prostřednictvím zřizování automatizované uživatelských účtů najdete v tématu [automatizace zřizování uživatelů pro aplikace SaaS ve službě Azure AD](https://aka.ms/ProvisioningDocumentation).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Obnovení a spravovat vaše odstraněné skupiny Office 365 na portálu Azure AD

**Typ:** Nová funkce  
**Kategorie služby:** Správa skupin  
**Funkce produktu:** Spolupráce

Teď můžete zobrazit a spravovat vaše odstraněné skupiny Office 365 z portálu Azure AD. Tato změna vám umožní zobrazit skupiny, které jsou k dispozici pro obnovení, spolu s umožňuje trvale odstranit všechny skupiny, které nejsou potřeba ve vaší organizaci.

Další informace najdete v tématu [obnovení platnost vypršela nebo odstranění skupin](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Jednotné přihlašování je teď dostupná pro Azure AD SAML zabezpečené místní aplikace prostřednictvím Proxy aplikací (public preview)

**Typ:** Nová funkce  
**Kategorie služby:** App Proxy  
**Funkce produktu:** Řízení přístupu

Teď můžete zadat možnosti jednotného přihlašování (SSO) pro místní, aplikace SAML ověřen, spolu s vzdálený přístup k těmto aplikacím prostřednictvím Proxy aplikací. Další informace o tom, jak nastavit jednotné přihlašování SAML s místním aplikacím, najdete v části [SAML jednotného přihlašování k místním aplikacím přes Proxy aplikací (Preview)](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Klientské aplikace ve smyčkách žádosti se odešle ke zlepšení spolehlivosti a činnost koncového uživatele

**Typ:** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Funkce produktu:** Ověřování uživatelů

Klientské aplikace může vydat nesprávně stovky stejné žádostí o přihlášení po krátkou dobu. Tyto požadavky, jestli jste úspěšně nebo Ne, všechny přispívat nízký uživatelské prostředí a zvýšenou úlohy pro zprostředkovatele identity, zvýšení latence pro všechny uživatele a snížit dostupnost zprostředkovatele identity.

Odešle tato aktualizace `invalid_grant` Chyba: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` do klientských aplikací, které zasílání požadavků na duplicitní několikrát během krátké doby dobu nad rámec běžné operace. Klientské aplikace, které tento problém nastane by měl zobrazit výzvu k interaktivní, by uživatel musel znovu přihlásit. Další informace o této změně a o tom, jak opravit vaší aplikace, pokud dojde k této chybě najdete v tématu [co je nového v ověřování?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Nové protokoly auditu činnost koncového uživatele teď k dispozici

**Typ:** Změněná funkce  
**Kategorie služby:** Vytváření sestav  
**Funkce produktu:** Monitorování a vytváření sestav

Vytvořili jsme nové služby Azure AD **protokoly auditu** stránku a Pomozte nám vylepšit čitelnost a jak hledat pro vaši informaci. Zobrazíte nové **protokoly auditu** stránce **protokoly auditu** v **aktivity** část Azure AD.

![Nová stránka protokolů auditu, s informacemi vzorku](media/whats-new/audit-logs-page.png)

Další informace o novém **protokoly auditu** stránky, přečtěte si téma [auditní sestavy aktivit na portálu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Nová upozornění a pokyny, aby se zabránilo nechtěnému správce uzamčení z nesprávně nakonfigurované zásady podmíněného přístupu

**Typ:** Změněná funkce  
**Kategorie služby:** Podmíněný přístup  
**Funkce produktu:** Zabezpečení a ochrana identity

Zabránit Správci omylem nezamezila svoje vlastní tenanty prostřednictvím nesprávně nakonfigurované zásady podmíněného přístupu, vytvořili jsme nová upozornění a aktualizované pokyny na webu Azure Portal. Další informace o nové pokyny najdete v tématu [co jsou závislosti služby podmíněného přístupu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Vylepšené podmínky použití prostředí na mobilních zařízeních

**Typ:** Změněná funkce  
**Kategorie služby:** Podmínky použití  
**Funkce produktu:** Zásady správného řízení

Aktualizovali jsme naše stávající podmínky použití prostředí ke zlepšování jak zkontrolovat a souhlas s podmínkami použití na mobilním zařízení. Můžete teď přiblížení a oddálení, vraťte, stáhněte si informace a vyberte hypertextové odkazy. Další informace o aktualizované podmínky použití, naleznete v tématu [podmínky služby Azure Active Directory použít funkci](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Možnosti, které jsou k dispozici stahování nové protokoly aktivit Azure AD

**Typ:** Změněná funkce  
**Kategorie služby:** Vytváření sestav  
**Funkce produktu:** Monitorování a vytváření sestav

Velké množství protokoly aktivit můžete stáhnout přímo z portálu Azure portal. Tato aktualizace umožňuje:

- Stáhněte si až 250 000 řádků.

- Dostanete oznámení po dokončení stahování.

- Upravit název souboru.

- Určení výstupní formát, JSON nebo CSV.

Další podrobnosti o této funkci najdete v tématu [rychlý start: Stáhněte si sestavu auditování pomocí webu Azure portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Zásadní změna: Aktualizace k vyhodnocení podmínky pomocí protokolu Exchange ActiveSync (EAS)

**Typ:** Plánování změn  
**Kategorie služby:** Podmíněný přístup  
**Funkce produktu:** Řízení přístupu

Teď aktualizujeme způsob, jak protokolu Exchange ActiveSync (EAS) vyhodnocuje následující podmínky:

- Umístění uživatele na základě země, oblast nebo IP adresa

- Riziko přihlášení

- Platforma zařízení

Pokud jste dříve používali tyto podmínky v zásad podmíněného přístupu, mějte na paměti, že může změnit podmínky chování. Například pokud jste dříve používali podmínka umístění uživatele v zásadách, můžete zjistit, že na umístění uživatele na základě zásad nyní přeskočeno.

---

## <a name="february-2019"></a>. Února 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Konfigurovat Azure AD šifrování tokenu SAML (Public preview) 

**Typ:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Funkce produktu:** SSO

Teď můžete nakonfigurovat žádnou z podporovaných aplikací SAML pro příjem šifrovaných tokenů SAML. Když nakonfigurovali a používali s aplikací, Azure AD zašifruje emitovaný kontrolní výrazy SAML pomocí veřejného klíče získané z certifikátu uloženého v Azure AD.

Další informace o konfiguraci šifrování tokenu SAML, naleznete v tématu [šifrování tokenu konfigurovat Azure AD SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Vytvoření kontroly přístupu pro skupiny nebo aplikace s využitím kontrol přístupu Azure AD

**Typ:** Nová funkce  
**Kategorie služby:** Kontroly přístupu  
**Funkce produktu:** Zásady správného řízení

Teď může obsahovat více skupin nebo kontrola členství ve skupině nebo přiřazení aplikace přístupu aplikace v jedné službě Azure AD. Kontroly přístupu s více skupin nebo aplikace se nastavují pomocí stejné nastavení a všem jejím kontrolorům zahrnuté upozorněni ve stejnou dobu.

Další informace o vytvoření kontroly přístupu pomocí kontrol přístupu Azure AD najdete v tématu [vytvoření kontroly přístupu skupinám nebo aplikacím v kontrol přístupu Azure AD](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Nové federované aplikace k dispozici v galerii aplikací Azure AD –. února 2019

**Typ:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Funkce produktu:** 3. stran integrace
 
Ve. února 2019 jsme přidali podporu těchto 27 nové aplikace s federací do Galerie aplikací:

[Euromonitor Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MindTickle](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [FAT PRSTEM](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [AirStack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [ IDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [Skyward Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [jednotného přihlašování Soloinsight CloudGate](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), Klikněte na oprávnění, [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [seismických ](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [Sdílené složky A sen](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [prvkům webMethods cloudové integrace](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [LMS znalostní báze kdekoli](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [Organizační jednotky Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [Data přes aplikaci Periscope](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [Netop portál](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud podle Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [ClickUp produktivní platforma](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Další informace o aplikacích najdete v tématu [integrace aplikací SaaS pomocí Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete v tématu [uvedení aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Vylepšené kombinovaná registrace MFA/samoobslužné resetování HESLA

**Typ:** Změněná funkce  
**Kategorie služby:** Samoobslužné resetování hesla  
**Funkce produktu:** Ověřování uživatelů

V reakci na zpětnou vazbu od zákazníků jsme vylepšili kombinované MFA/samoobslužné resetování HESLA registrace funkcí ve verzi preview, pomáhá uživatelům rychleji zaregistrovat svoje bezpečnostní údaje pro vícefaktorové ověřování a samoobslužné resetování HESLA. 

**Chcete-li na rozšířené prostředí pro vaše uživatele ještě dnes, postupujte podle těchto kroků:**

1. Jako globální správce nebo Správce uživatelů, přihlaste se k webu Azure portal a přejděte na **Azure Active Directory > Nastavení > Spravovat nastavení pro funkce ve verzi preview panel přístupu**. 

2. V **uživatelé, kteří můžou využívat verze preview funkce pro registraci a správu bezpečnostních údajů – aktualizovat** možnost, vybrat možnost zapnutí funkce pro **vybrané skupiny uživatelů** nebo **všichni uživatelé** .

V příštích týdnech jsme budete odebrání možnost zapnout starému kombinované MFA/samoobslužné resetování HESLA registraci ve verzi preview prostředí pro klienty, kteří nemají již je zapnuté.

**Pokud chcete zobrazit, pokud ovládací prvek se odeberou pro vašeho tenanta, postupujte podle těchto kroků:**

1. Jako globální správce nebo Správce uživatelů, přihlaste se k webu Azure portal a přejděte na **Azure Active Directory > Nastavení > Spravovat nastavení pro funkce ve verzi preview panel přístupu**.  

2. Pokud **uživatelé, kteří můžou používat funkce verze preview pro registraci a správu bezpečnostních údajů** je možnost nastavená na **žádný**, možnost se odebere z vašeho tenanta.

Bez ohledu na to, zda jste dříve zapnutá staré kombinovaná registrace MFA/samoobslužné resetování HESLA ve verzi preview prostředí pro uživatele, nebo Ne, starší prostředí se vypne v budoucnu. Proto důrazně doporučujeme, že přejdete na nové a vylepšené prostředí co nejdříve.

Další informace o možnosti lepší registrace, najdete v článku [kombinaci nástrojů rozšíření do služby Azure AD MFA a způsobu registrace pro resetování hesla](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Prostředí pro správu aktualizované zásady pro toky uživatelů

**Typ:** Změněná funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Funkce produktu:** B2B/B2C

Aktualizovali jsme proces vytváření a Správa zásad pro uživatele snadnější toků (dříve označované jako, integrované zásady). Toto nové prostředí slouží nyní jako výchozí pro všechny tenanty Azure AD.

Můžete poskytnout další názory a návrhy pomocí úsměv nebo Oblouček nahoru ikony v **pošlete nám svůj názor** oblasti v horní části portálu obrazovky.

Další informace o nové prostředí pro správu zásad, najdete v článku [Azure AD B2C teď má přizpůsobení jazyka JavaScript a mnoha dalšími novými funkcemi](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blogu.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Vyberte konkrétní stránku element verze poskytuje Azure AD B2C

**Typ:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Funkce produktu:** B2B/B2C

Nyní můžete na konkrétní verzi nástroje elementů stránek, poskytuje Azure AD B2C. Výběrem konkrétní verze můžete otestovat aktualizace předtím, než se zobrazují na stránce a získáte předvídatelný chování. Kromě toho můžete nyní přejít k vynucení verze konkrétní stránku povolit přizpůsobení jazyka JavaScript. Chcete-li tuto funkci zapnout, přejděte **vlastnosti** stránky v tocích uživatelů.

Další informace o volbě určité verze prvků na stránce, najdete v článku [Azure AD B2C teď má přizpůsobení jazyka JavaScript a mnoha dalšími novými funkcemi](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blogu.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Požadavky na heslo konfigurovatelné koncového uživatele pro B2C (GA)

**Typ:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Funkce produktu:** B2B/B2C

Složitost hesla vaší organizace teď můžete nastavit pro koncové uživatele, místo nutnosti použít nativního zásady hesel služby Azure AD. Z **vlastnosti** okno uživatelů toků (dříve označovanou jako integrované zásady), můžete použít složitost hesla **jednoduché** nebo **silné**, nebo můžete vytvoření **vlastní** sadu požadavků.

Další informace o konfiguraci požadavek na složitost hesla najdete v tématu [konfigurovat požadavky na složitost hesel v Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Nové výchozí šablony pro vlastní ověřování obchodní značku prostředí

**Typ:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Funkce produktu:** B2B/B2C

Můžete použít naše nové výchozí šablony, na **rozložení stránek** okno toky uživatelů (dříve označovanou jako integrované zásady), můžete vytvořit vlastní značky prostředí ověřování pro vaše uživatele.

Další informace o použití šablon najdete v tématu [Azure AD B2C teď má přizpůsobení jazyka JavaScript a mnoha dalšími novými funkcemi](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>2019. ledna

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Active Directory s B2B spolupráce pomocí ověřování jednorázovým heslem (Public preview)

**Typ:** Nová funkce  
**Kategorie služby:** B2B  
**Funkce produktu:** B2B/B2C

Zavedli jsme ověřování jednorázovým heslem (OTP) pro B2B uživatele typu Host, kteří nemohou být ověřeni pomocí jiným způsobem, jako je Azure AD, účet Microsoft (MSA) nebo federační služby Google. Tato nová metoda ověřování znamená, že hostovaném uživatelé nemuseli vytvářet nový účet Microsoft. Místo toho při uplatnění pozvání nebo přístup ke sdílenému prostředku, uživatele typu Host můžete požádat o dočasný kód k odeslání e-mailovou adresu. Pomocí tento dočasný kód, můžete pokračovat uživatele typu Host pro přihlášení.

Další informace najdete v tématu [e-mailu ověřování jednorázovým heslem (preview)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) a na blogu [Azure AD zajišťuje sdílení a spolupráce bezproblémovou pro libovolného uživatele pomocí libovolného účtu](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Nové nastavení souborů cookie Proxy aplikací Azure AD

**Typ:** Nová funkce  
**Kategorie služby:** App Proxy  
**Funkce produktu:** Řízení přístupu

Zavedli jsme tři nové nastavení souborů cookie, k dispozici pro vaše aplikace, které jsou publikované prostřednictvím Proxy aplikací:

- **Použijte soubor cookie jenom pro protokol HTTP.** Nastaví **HTTPOnly** příznak na soubory cookie přístupu a relace Proxy aplikací. Zapnutím tohoto nastavení poskytuje další bezpečnostní výhody, jako je například zabránění kopírování nebo úprava souborů cookie pomocí skriptování na straně klienta. Doporučujeme vám zapnout tento příznak (zvolte **Ano**) pro další výhody.

- **Použití zabezpečeného souboru cookie.** Nastaví **Secure** příznak na soubory cookie přístupu a relace Proxy aplikací. Zapnutím tohoto nastavení poskytuje další bezpečnostní výhody, ujistěte se, že soubory cookie jenom při přenosu TLS zabezpečené kanály, jako je například HTTPS. Doporučujeme vám zapnout tento příznak (zvolte **Ano**) pro další výhody.

- **Použijte soubor cookie trvalý.** Zabrání vypršení platnosti při zavření prohlížeče soubory cookie přístup. Tyto soubory cookie poslední po dobu platnosti přístupového tokenu. Soubory cookie jsou však obnovit, pokud je dosaženo času vypršení platnosti nebo pokud uživatel ručně odstraní soubor cookie. Doporučujeme ponechat výchozí nastavení **ne**, pouze zapnout v nastavení pro starší aplikace, které není sdílení souborů cookie mezi procesy.

Další informace o nové soubory cookie najdete v tématu [nastavení souborů Cookie pro přístup k místním aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Nové federované aplikace k dispozici v galerii aplikací Azure AD – leden 2019

**Typ:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Funkce produktu:** 3. stran integrace
 
V lednu 2019 jsme přidali podporu těchto 35 nových aplikací s federací do Galerie aplikací:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [talentu palety](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco zastřešující](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [Zscaler Správce přístupu k Internetu](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), [vypršení platnosti připomenutí](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [InstaVR prohlížeč](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [příkaz](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso digitální zavřete](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [ Cloudové služby PICCO](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [Workable](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial), [CallPlease](https://webapp.callplease.com/create-account/create-account.html), [Systém jednotného přihlašování GTNexus](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [ARECH pro podniky](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 pro Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD správce](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [Visitly](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [Korn Ferry ALP](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle cSaas platformy](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

Další informace o aplikacích najdete v tématu [integrace aplikací SaaS pomocí Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete v tématu [uvedení aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Nová vylepšení Azure AD Identity Protection (verze Public preview)

**Typ:** Změněná funkce  
**Kategorie služby:** Identity Protection  
**Funkce produktu:** Zabezpečení a ochrana identity

S potěšením oznamujeme, že jsme přidali tato vylepšení nabídky Azure AD Identity Protection ve verzi public preview, včetně:

- Aktualizované a více integrované uživatelské rozhraní

- Další rozhraní API

- Vylepšené rizika prostřednictvím strojového učení

- Celý produkt koordinaci rizikoví uživatelé a rizikových přihlášení

Další informace o rozšířeních najdete v části [co je Azure Active Directory Identity Protection (Aktualizovat)?](https://aka.ms/IdentityProtectionDocs) Další informace a sdílet své myšlenky pomocí pokynů v rámci produktu.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Nová funkce zámku aplikace pro aplikaci Microsoft Authenticator v Iosu a zařízení s Androidem

**Typ:** Nová funkce  
**Kategorie služby:** Aplikace Microsoft Authenticator  
**Funkce produktu:** Zabezpečení a ochrana identity

K lepšímu zabezpečení jednorázového hesla, informace o aplikaci a nastavení aplikace, můžete zapnout funkci App Lock v aplikaci Microsoft Authenticator. Zapnutí zámku aplikace znamená, že budete ke správě ověřování pomocí svůj PIN kód nebo biometrické pokaždé, když otevřete aplikaci Microsoft Authenticator.

Další informace najdete v tématu [aplikaci Microsoft Authenticator nejčastější dotazy k](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Vylepšené Azure AD Privileged Identity Management (PIM) exportovat funkce

**Typ:** Nová funkce  
**Kategorie služby:** Privileged Identity Management  
**Funkce produktu:** Privileged Identity Management

Správci služby privileged Identity Management (PIM) můžete nyní exportovat všechna přiřazení rolí aktivní a bude možné pro určitý prostředek, který obsahuje přiřazení rolí pro všechny podřízené prostředky. Dříve bylo obtížné pro správce, chcete-li získat úplný seznam přiřazení rolí pro předplatné a měli zajišťující export přiřazení rolí pro každou konkrétní prostředek.

Další informace najdete v tématu [zobrazit historii aktivit a auditu pro role prostředků Azure v PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

## <a name="novemberdecember-2018"></a>Listopad/prosince 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Uživatelé Odebraní z oboru synchronizace žádný delší přepínač na účty jenom cloudu

**Typ:** Oprava  
**Kategorie služby:** Správa uživatelů  
**Funkce produktu:** Adresář

>[!Important]
>Jsme zjistili a pochopení vaší frustrace kvůli tato oprava. Proto jsme jste vrátit zpět tuto změnu až do doby, že bychom mohli vyřešit snazší pro vás k implementaci ve vaší organizaci.

Opravili jsme chybu, ve kterém by se nesprávně přepnout příznaku DirSyncEnabled uživatele do **False** při objektu služby Active Directory Domain Services (AD DS) byl vyloučen ze synchronizace obor a poté přesunut do koše v Azure AD na následující cyklus synchronizace. V důsledku této opravy, pokud uživatel je vyloučen z rozsah synchronizace a potom obnovit z Azure Koš služby Active Directory, uživatelský účet zůstane synchronizovaná z místní služby AD, podle očekávání a nejde je spravovat v cloudu, protože bude nadála zdrojem authority (SoA) místní služby AD.

Před touto opravou došlo k problému při příznaku DirSyncEnabled byl přepnut na hodnotu False. To zadali nesprávné dojem, která tyto účty se převedly na objekty jenom cloudu a, který může spravovat účty v cloudu. Však účty stále zachována jejich SoA jako místní a všechny synchronizované vlastnosti (stínové atributy) pocházející z místní služby AD. Tuto podmínku způsobil několik problémů ve službě Azure AD a dalších cloudových úloh (jako je Exchange Online), která má zpracovávat tyto účty synchronizovat ze služby AD, ale se nyní chová podobně jako účty jenom cloudu.

V tuto chvíli je jediný způsob, jak skutečně převod účtu synchronizované z AD výhradně cloudový účet tím, že zakážete nástroje DirSync na úrovni tenanta, který aktivuje back-endová operace pro přenos SoA. Tento typ změny SoA vyžaduje (ale není omezena pouze na) čištění všechny místní související s atributy (jako je například LastDirSyncTime a atributy stínové) a odešle signál do jiných cloudových úloh jeho odpovídající objekt převést na výhradně cloudový účet příliš .

Tato oprava proto zabraňuje s přímým přístupem aktualizace u atributu ImmutableID uživatele synchronizovat ze služby AD, které se v některých scénářích v minulosti byly zapotřebí. Záměrné ImmutableID objektu ve službě Azure AD, jak již název napovídá, měl by být neměnné. Nové funkce, které jsou implementovány v klientovi Azure AD Connect Health a synchronizace Azure AD Connect jsou k dispozici v těchto situacích:

- **Ve velkém měřítku ImmutableID aktualizace pro mnoho uživatelů v rámci fázované přístupu**
  
  Například budete muset provést migraci zdlouhavé doménovými strukturami služby AD DS. Řešení: Použití služby Azure AD Connect k **konfigurovat zdrojové ukotvení** a jako uživatel provede migraci, zkopírovat existující hodnoty ImmutableID ze služby Azure AD do místní služby AD DS uživatele atribut ms-DS-konzistence – Guid nové doménové struktury. Další informace najdete v tématu [pomocí ms-DS-ConsistencyGuid jako parametru sourceAnchor](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor).

- **Ve velkém měřítku ImmutableID aktualizace pro mnoho uživatelů v jednom kroku**

  Například při implementaci služby Azure AD Connect uděláte chybu, a teď je potřeba změnit atribut SourceAnchor. Řešení: Zakázat nástroje DirSync na úrovni tenanta a zrušte všechny neplatné hodnoty ImmutableID. Další informace najdete v tématu [vypnout synchronizaci adresářů pro Office 365](/office365/enterprise/turn-off-directory-synchronization).

- **Rematch místní uživatel s existujícím uživatelem ve službě Azure AD** například uživatel, který je znovu vytvořit ve službě AD DS vygeneruje duplicitní v účtu služby Azure AD namísto rematching s existující účet služby Azure AD (osamocený objekt). Řešení: Přemapujte zdrojové ukotvení/ImmutableID pomocí Azure AD Connect Health na webu Azure Portal. Další informace najdete v tématu [Orphaned objekt scénář](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Zásadní změna: Aktualizace auditu a protokolů přihlášení schématu prostřednictvím služby Azure Monitor

**Typ:** Změněná funkce  
**Kategorie služby:** Vytváření sestav  
**Funkce produktu:** Monitorování a vytváření sestav

Audit i přihlášení datové proudy protokolu v nástroji Azure Monitor jsme aktuálně publikování, můžete bez problémů integrovat soubory protokolů pomocí nástrojů systému SIEM nebo pomocí Log Analytics. Na základě vaší zpětné vazby a během přípravy na tuto funkci ohlášení obecné dostupnosti, vám s tím následující změny naše schématu. První týden v lednu provede tyto změny ve schématu a jeho aktualizace související dokumentaci.

#### <a name="new-fields-in-the-audit-schema"></a>Nová pole ve schématu auditu
Přidáváme nový **typ operace** pole pro zadání typu operace provedena na prostředku. Například **přidat**, **aktualizace**, nebo **odstranit**.

#### <a name="changed-fields-in-the-audit-schema"></a>Změněné pole ve schématu auditu
Ve schématu auditu se mění následující pole:

|Název pole|Co se změnilo|Staré hodnoty|Nové hodnoty|
|----------|------------|----------|----------|
|Category|To bylo **název služby** pole. Je teď **kategorií auditu** pole. **Název služby** byl přejmenován na **loggedByService** pole.|<ul><li>Zřizování účtů</li><li>Základní adresář</li><li>Samoobslužné resetování hesla</li></ul>|<ul><li>Správa uživatelů</li><li>Správa skupin</li><li>Správa aplikací</li></ul>|
|targetResources|Zahrnuje **TargetResourceType** na nejvyšší úrovni.|&nbsp;|<ul><li>Zásada</li><li>Aplikace</li><li>Uživatel</li><li>Skupina</li></ul>|
|loggedByService|Obsahuje název služby, která vygeneruje protokol auditu.|Null|<ul><li>Zřizování účtů</li><li>Základní adresář</li><li>Samoobslužné resetování hesla</li></ul>|
|Výsledek|Poskytuje výsledek funkce protokolů auditu. Dříve to bylo uvedené, ale zobrazujeme skutečnou hodnotu.|<ul><li>0</li><li>1</li></ul>|<ul><li>Úspěch</li><li>Selhání</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Změněné pole ve schématu přihlášení
Následující pole jsou změny ve schématu přihlášení:

|Název pole|Co se změnilo|Staré hodnoty|Nové hodnoty|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|To bylo **conditionalaccessPolicies** pole. Je teď **appliedConditionalAccessPolicies** pole.|Žádné změny|Žádné změny|
|conditionalAccessStatus|Poskytuje výsledek funkce stav zásad podmíněného přístup při přihlášení. Dříve to bylo uvedené, ale zobrazujeme skutečnou hodnotu.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Úspěch</li><li>Selhání</li><li>Nevztahuje se.</li><li>Zakázáno</li></ul>|
|appliedConditionalAccessPolicies: výsledek|Poskytuje výsledek funkce jednotlivých stavu podmíněného přístupu zásad při přihlášení. Dříve to bylo uvedené, ale zobrazujeme skutečnou hodnotu.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Úspěch</li><li>Selhání</li><li>Nevztahuje se.</li><li>Zakázáno</li></ul>|

Další informace o schématu najdete v tématu [protokolů auditu Azure AD interpretaci schématu ve službě Azure Monitor (preview)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Vylepšení ochrany identit na pod dohledem modelu strojového učení a modul určení skóre rizika

**Typ:** Změněná funkce  
**Kategorie služby:** Identity Protection  
**Funkce produktu:** Skóre rizika

Vylepšení související s Identity Protection uživatele a přihlašování riziko posouzení modul může pomoct zlepšit přesnost riziko uživatele a pokrytí. Správci si všimnout, že úroveň rizika uživatele je již přímo propojený úroveň rizika konkrétních detekcí a že je zvýšení počet a úroveň události rizikových přihlášení.

Detekce rizik jsou nyní vyhodnocovány pomocí technik strojového učení modelu, který se vypočítává riziko uživatele model detekcí a další funkce přihlášení uživatele. Na základě tohoto modelu, Správce může najít uživatele s vysokým rizikem skóre, i v případě detekce přidružené k tomuto uživateli je střední nebo nízké riziko. 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Správci můžou resetovat vlastní hesla pomocí aplikace Microsoft Authenticator (Public preview)

**Typ:** Změněná funkce  
**Kategorie služby:** Samoobslužné resetování hesla  
**Funkce produktu:** Ověřování uživatelů

Správci služby Azure AD teď můžou resetovat svoje vlastní hesla použijte v oznámeních aplikací Microsoft Authenticator nebo kód z libovolné mobilní ověřovací aplikace nebo hardwarového tokenu. Pokud chcete resetovat vlastní heslo, správci teď budou moct používat dva z následujících metod:

- Oznámení aplikace Microsoft Authenticator

- Jiné mobilní ověřovací aplikace / hardwaru token kódu

- Email

- Telefonní hovor

- Textová zpráva

Další informace o používání aplikace Microsoft Authenticator k resetování hesel najdete v tématu [resetování hesla pomocí samoobslužné služby Azure AD – mobilní aplikace a samoobslužné resetování HESLA (ve verzi Preview)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr-preview)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Nová role správce cloudových zařízení Azure AD (Public preview)

**Typ:** Nová funkce  
**Kategorie služby:** Registrace a správa zařízení  
**Funkce produktu:** Řízení přístupu

Správci mohou přiřadit uživatele k nové roli správce cloudových zařízení provádět úlohy správce cloudové zařízení. Uživatelé s rolí správce v zařízení cloudu můžete povolit, zakázat a odstraňovat zařízení ve službě Azure AD, spolu se nebudou moct číst klíče nástroje BitLocker systému Windows 10 (pokud existuje) na webu Azure Portal.

Další informace o rolích a oprávněních najdete v tématu [přiřazení rolí správce v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Správa zařízení pomocí nové časové razítko aktivity ve službě Azure AD (Public preview)

**Typ:** Nová funkce  
**Kategorie služby:** Registrace a správa zařízení  
**Funkce produktu:** Správa životního cyklu zařízení

Uvědomujeme si, že v čase musíte aktualizace a vyřazení zařízení vaší organizace ve službě Azure AD, abyste se vyhnuli nutnosti zastaralá zařízení ve vašem prostředí. Smyslem tohoto procesu, teď Azure AD aktualizuje vaše zařízení s časovým razítkem nové aktivity pomáhají spravovat životní cyklus vašeho zařízení.

Další informace o tom, jak získat a použít tento časové razítko, naleznete v tématu [How To: Správa zastaralých zařízení ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Správci můžou vyžadovat, aby uživatelé přijali podmínky použití na všech zařízeních

**Typ:** Nová funkce  
**Kategorie služby:** Podmínky použití  
**Funkce produktu:** Zásady správného řízení
 
Správci mohou nyní zapnout **vyžadují, aby uživatelé vyjádřili souhlas na všech zařízeních** možnost vyžadovat, aby vaši uživatelé tak, aby přijímal vaše podmínky použití na všech zařízeních, že používáte ve svém tenantovi.

Další informace najdete v tématu [jednotlivá zařízení podmínky použití části Azure Active Directory podmínek použití funkce](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#per-device-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Správci můžou konfigurovat podmínky použití vypršení platnosti podle opakovaného plánu

**Typ:** Nová funkce  
**Kategorie služby:** Podmínky použití  
**Funkce produktu:** Zásady správného řízení
 

Správci mohou nyní zapnout **vyprší souhlasy** vyprší možnosti nastavte podmínky použití pro všechny vaše uživatele podle zadaného plánu opakování. Plán může být každoročně, bi – roční, čtvrtletně nebo každý měsíc. Po vypršení platnosti podmínky použití, musí uživatelé znovu přijali jako.

Další informace najdete v tématu [přidat podmínky použití části Azure Active Directory podmínek použití funkce](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Správci můžou konfigurovat podmínky použití vyprší na základě plánu každého uživatele

**Typ:** Nová funkce  
**Kategorie služby:** Podmínky použití  
**Funkce produktu:** Zásady správného řízení

Správci teď mohou určit dobu trvání tento uživatel musí znovu přijali jako podmínky použití. Například Správci mohou určit, že uživatelé musí znovu přijali jako podmínky použití každých 90 dní.

Další informace najdete v tématu [přidat podmínky použití části Azure Active Directory podmínek použití funkce](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>E-mailem na nový Azure AD Privileged Identity Management (PIM) pro role Azure Active Directory

**Typ:** Nová funkce  
**Kategorie služby:** Privileged Identity Management  
**Funkce produktu:** Privileged Identity Management
 
Zákazníci, kteří používají Azure AD Privileged Identity Management (PIM) teď můžou přijímat týdenní e-maily ověřování algoritmem digest, včetně následujících informací za posledních sedm dní:

- Přehled role nejvyšší oprávněných a trvalých přiřazení

- Počet uživatelů aktivace role

- Počet uživatelů přidružených k rolím v PIM

- Počet uživatelů přidružených k rolím mimo PIM

- Počet uživatelů "provedeny trvalé" v PIM

Další informace o PIM a k dispozici e-mailových oznámení najdete v tématu [e-mailová oznámení v PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications).

---

### <a name="group-based-licensing-is-now-generally-available"></a>Licencování na základě skupiny je teď obecně dostupná

**Typ:** Změněná funkce  
**Kategorie služby:** Ostatní  
**Funkce produktu:** Adresář

Licencování na základě skupiny je mimo verzi public preview a je teď obecně dostupná. Jako součást této obecné verzi jsme provedli tato funkce větší škálovatelnost a přidali možnost opětovné zpracování licencí přiřazení na základě skupin pro jednoho uživatele a možnost použití skupinové licencování s Office 365 E3 nebo A3 licence.

Další informace o licencování pro skupiny, najdete v části [co je založené na skupinách licencování v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Nové federované aplikace k dispozici v galerii aplikací Azure AD –. listopadu 2018

**Typ:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Funkce produktu:** 3. stran integrace
 
V listopadu 2018 jsme přidali podporu těchto 26 nových aplikací s federací do Galerie aplikací:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [stínovat Pe](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [upl.hodina](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [nekonečné Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya](https://alayagood.com/en/demo/), [ HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [odchylek](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy pro firmy centrální 365](https://accounting.zenegy.com/), [Everbridge člen portál](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [IDEO](https://profile.ideo.com/users/sign_up), [Ivanti portálu Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [jednotného přihlašování Allbound](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [složitých aplikací – Klasické testu](https://test.plexonline.com/signon), [složitých aplikací – klasické](https://www.plexonline.com/signon), [složitých aplikací – Test uživatelského rozhraní](https://test.cloud.plex.com/sso), [složitých aplikací – UX](https://cloud.plex.com/sso), [složitých aplikací – IAM](https://accounts.plex.com/), [Mě - pro péči o děti záznamů, účast a finanční sledování systému](https://getcrafts.ca/craftsregistration) 

Další informace o aplikacích najdete v tématu [integrace aplikací SaaS pomocí Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete v tématu [uvedení aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---