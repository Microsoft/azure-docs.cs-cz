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
ms.date: 04/30/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c58c99faa83d6f04ac37d03789202e111f1b06d
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611156"
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
