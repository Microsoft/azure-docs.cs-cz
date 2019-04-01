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
ms.date: 02/28/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5077f09cba3548b03045011a7b8f16844156de55
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2019
ms.locfileid: "58757644"
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
**Funkce produktu:** Integrace třetích stran

V březnu 2019 jsme přidali podporu těchto 14 nových aplikací s federací do Galerie aplikací:

[Delegát Exchange Mobile ISEC7](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [Auditování systému vysvětlení](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [Lean](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [otázky výkonu Powerschool](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode](https://cinode.com/), [Iris Intranet](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit možností](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [systému](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Další informace o aplikacích najdete v tématu [integrace aplikací SaaS pomocí Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete v tématu [uvedení aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Nové Zscalerem a zřizování konektorů v galerii Azure AD – březen 2019 od společnosti Atlassian

**Typ:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Funkce produktu:** Integrace třetích stran

Automatizujte vytváření, aktualizace nebo odstranění uživatelských účtů pro následující aplikace:

[Zscalerem](https://aka.ms/ZscalerProvisioning), [Zscalerem Beta](http://aka.ms/ZscalerBetaProvisioning), [Zscalerem jeden](https://aka.ms/ZscalerOneProvisioning), [Zscalerem dvě](http://aka.ms/ZscalerTwoProvisioning), [Zscalerem tři](http://aka.ms/ZscalerThreeProvisioning), [Zscaler ZSCloud ](http://aka.ms/ZscalerZSCloudProvisioning), [Cloud od společnosti Atlassian](http://aka.ms/atlassianCloudProvisioning)

Další informace o tom, jak lépe zabezpečit svou organizaci prostřednictvím zřizování automatizované uživatelských účtů najdete v tématu [automatizace zřizování uživatelů pro aplikace SaaS ve službě Azure AD](http://aka.ms/ProvisioningDocumentation).

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
**Kategorie služby:** Proxy aplikace  
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
**Funkce produktu:** Monitorování a sestavy

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

Aktualizovali jsme naše stávající podmínky použití prostředí ke zlepšování jak zkontrolovat a souhlas s podmínkami použití na mobilním zařízení. Můžete teď přiblížení a oddálení, vraťte, stáhněte si informace a vyberte hypertextové odkazy. Další informace o aktualizované podmínky použití, naleznete v tématu [Azure Active Directory Terms of použít funkci](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Možnosti, které jsou k dispozici stahování nové protokoly aktivit Azure AD

**Typ:** Změněná funkce  
**Kategorie služby:** Vytváření sestav  
**Funkce produktu:** Monitorování a sestavy

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

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Konfigurovatelné šifrování tokenů SAML v Azure AD (public preview) 

**Typ:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Funkce produktu:** Jednotné přihlašování

Teď můžete nakonfigurovat žádnou z podporovaných aplikací SAML pro příjem šifrovaných tokenů SAML. Když nakonfigurovali a používali s aplikací, Azure AD zašifruje emitovaný kontrolní výrazy SAML pomocí veřejného klíče získané z certifikátu uloženého v Azure AD.

Další informace o konfiguraci šifrování tokenu SAML, naleznete v tématu [šifrování tokenu konfigurovat Azure AD SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Vytvoření kontroly přístupu pro skupiny nebo aplikace pomocí Kontrol přístupu Azure AD

**Typ:** Nová funkce  
**Kategorie služby:** Kontroly přístupu  
**Funkce produktu:** Zásady správného řízení

Teď může obsahovat více skupin nebo kontrola členství ve skupině nebo přiřazení aplikace přístupu aplikace v jedné službě Azure AD. Kontroly přístupu s více skupin nebo aplikace se nastavují pomocí stejné nastavení a všem jejím kontrolorům zahrnuté upozorněni ve stejnou dobu.

Další informace o vytvoření kontroly přístupu pomocí kontrol přístupu Azure AD najdete v tématu [vytvoření kontroly přístupu skupinám nebo aplikacím v kontrol přístupu Azure AD](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – únor 2019

**Typ:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Funkce produktu:** Integrace třetích stran
 
Ve. února 2019 jsme přidali podporu těchto 27 nové aplikace s federací do Galerie aplikací:

[Euromonitor Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MindTickle](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [FAT PRSTEM](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [AirStack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [ IDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [Skyward Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [jednotného přihlašování Soloinsight CloudGate](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), Klikněte na oprávnění, [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [seismických ](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [Sdílené složky A sen](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [prvkům webMethods cloudové integrace](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [LMS znalostní báze kdekoli](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [Organizační jednotky Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [Data přes aplikaci Periscope](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [Netop portál](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud podle Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [ClickUp produktivní platforma](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Další informace o aplikacích najdete v tématu [integrace aplikací SaaS pomocí Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete v tématu [uvedení aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Vylepšená kombinovaná registrace MFA a SSPR

**Typ:** Změněná funkce  
**Kategorie služby:** Samoobslužné resetování hesla  
**Funkce produktu:** Ověřování uživatelů
 
V reakci na zpětnou vazbu od zákazníků jsme vylepšili kombinované MFA/samoobslužné resetování HESLA registrace funkcí ve verzi preview, pomáhá uživatelům rychleji zaregistrovat svoje bezpečnostní údaje pro vícefaktorové ověřování a samoobslužné resetování HESLA. 

**Chcete-li na rozšířené prostředí pro uživatele ještě dnes, postupujte takto:**

1. Jako globální správce nebo Správce uživatelů, přihlaste se k webu Azure portal a přejděte na **Azure Active Directory > Nastavení > Spravovat nastavení pro funkce ve verzi preview panel přístupu**. 

2. V **uživatelé, kteří můžou využívat verze preview funkce pro registraci a správu bezpečnostních údajů – aktualizovat** možnost, vybrat možnost zapnutí funkce pro **vybrané skupiny uživatelů** nebo **všichni uživatelé** .

V příštích týdnech jsme budete odebrání možnost zapnout starému kombinované MFA/samoobslužné resetování HESLA registraci ve verzi preview prostředí pro klienty, kteří nemají již je zapnuté.

**Pokud chcete zobrazit, pokud ovládací prvek se odeberou pro vašeho tenanta, postupujte podle těchto kroků:**

1. Jako globální správce nebo Správce uživatelů, přihlaste se k webu Azure portal a přejděte na **Azure Active Directory > Nastavení > Spravovat nastavení pro funkce ve verzi preview panel přístupu**.  

2.  Pokud **uživatelé, kteří můžou používat funkce verze preview pro registraci a správu bezpečnostních údajů** je možnost nastavená na **žádný**, možnost se odebere z vašeho tenanta.

Bez ohledu na to, zda jste dříve zapnutá staré kombinovaná registrace MFA/samoobslužné resetování HESLA ve verzi preview prostředí pro uživatele, nebo Ne, starší prostředí se vypne v budoucnu. Proto důrazně doporučujeme, že přejdete na nové a vylepšené prostředí co nejdříve.

Další informace o možnosti lepší registrace, najdete v článku [kombinaci nástrojů rozšíření do služby Azure AD MFA a způsobu registrace pro resetování hesla](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Aktualizované prostředí pro správu zásad pro toky uživatele

**Typ:** Změněná funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Funkce produktu:** B2B/B2C

Aktualizovali jsme proces vytváření a Správa zásad pro uživatele snadnější toků (dříve označované jako, integrované zásady). Toto nové prostředí slouží nyní jako výchozí pro všechny tenanty Azure AD.

Můžete poskytnout další názory a návrhy pomocí úsměv nebo Oblouček nahoru ikony v **pošlete nám svůj názor** oblasti v horní části portálu obrazovky.

Další informace o nové prostředí pro správu zásad, najdete v článku [Azure AD B2C teď má přizpůsobení jazyka JavaScript a mnoha dalšími novými funkcemi](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blogu.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Volba konkrétních verzí elementů stránek poskytovaných službou Azure AD B2C

**Typ:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Funkce produktu:** B2B/B2C

Nyní můžete na konkrétní verzi nástroje elementů stránek, poskytuje Azure AD B2C. Výběrem konkrétní verze můžete otestovat aktualizace předtím, než se zobrazují na stránce a získáte předvídatelný chování. Kromě toho můžete nyní přejít k vynucení verze konkrétní stránku povolit přizpůsobení jazyka JavaScript. Chcete-li tuto funkci zapnout, přejděte **vlastnosti** stránky v tocích uživatelů.

Další informace o volbě určité verze prvků na stránce, najdete v článku [Azure AD B2C teď má přizpůsobení jazyka JavaScript a mnoha dalšími novými funkcemi](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blogu.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Konfigurovatelné požadavky na heslo koncových uživatelů pro B2C (GA)

**Typ:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Funkce produktu:** B2B/B2C

Složitost hesla vaší organizace teď můžete nastavit pro koncové uživatele, místo nutnosti použít nativního zásady hesel služby Azure AD. Z **vlastnosti** okno uživatelů toků (dříve označovanou jako integrované zásady), můžete použít složitost hesla **jednoduché** nebo **silné**, nebo můžete vytvoření **vlastní** sadu požadavků.

Další informace o konfiguraci požadavek na složitost hesla najdete v tématu [konfigurovat požadavky na složitost hesel v Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Nové výchozí šablony pro prostředí pro ověřování pod vlastní značkou

**Typ:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Funkce produktu:** B2B/B2C

Můžete použít naše nové výchozí šablony, na **rozložení stránek** okno toky uživatelů (dříve označovanou jako integrované zásady), můžete vytvořit vlastní značky prostředí ověřování pro vaše uživatele.

Další informace o použití šablon najdete v tématu [Azure AD B2C teď má přizpůsobení jazyka JavaScript a mnoha dalšími novými funkcemi](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>2019. ledna

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Spolupráce Active Directory B2B pomocí ověřování jednorázovým heslem (public preview)

**Typ:** Nová funkce  
**Kategorie služby:** B2B  
**Funkce produktu:** B2B/B2C

Zavedli jsme ověřování jednorázovým heslem (OTP) pro B2B uživatele typu Host, kteří nemohou být ověřeni pomocí jiným způsobem, jako je Azure AD, účet Microsoft (MSA) nebo federační služby Google. Tato nová metoda ověřování znamená, že hostovaném uživatelé nemuseli vytvářet nový účet Microsoft. Místo toho při uplatnění pozvání nebo přístup ke sdílenému prostředku, uživatele typu Host můžete požádat o dočasný kód k odeslání e-mailovou adresu. Pomocí tento dočasný kód, můžete pokračovat uživatele typu Host pro přihlášení.

Další informace najdete v tématu [e-mailu ověřování jednorázovým heslem (preview)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) a na blogu [Azure AD zajišťuje sdílení a spolupráce bezproblémovou pro libovolného uživatele pomocí libovolného účtu](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Nové nastavení souborů cookie Proxy aplikací Azure AD

**Typ:** Nová funkce  
**Kategorie služby:** Proxy aplikace  
**Funkce produktu:** Řízení přístupu

Zavedli jsme tři nové nastavení souborů cookie, k dispozici pro vaše aplikace, které jsou publikované prostřednictvím Proxy aplikací:

- **Použijte soubor cookie jenom pro protokol HTTP.** Nastaví **HTTPOnly** příznak na soubory cookie přístupu a relace Proxy aplikací. Zapnutím tohoto nastavení poskytuje další bezpečnostní výhody, jako je například zabránění kopírování nebo úprava souborů cookie pomocí skriptování na straně klienta. Doporučujeme vám zapnout tento příznak (zvolte **Ano**) pro další výhody.

- **Použití zabezpečeného souboru cookie.** Nastaví **Secure** příznak na soubory cookie přístupu a relace Proxy aplikací. Zapnutím tohoto nastavení poskytuje další bezpečnostní výhody, ujistěte se, že soubory cookie jenom při přenosu TLS zabezpečené kanály, jako je například HTTPS. Doporučujeme vám zapnout tento příznak (zvolte **Ano**) pro další výhody.

- **Použijte soubor cookie trvalý.** Zabrání vypršení platnosti při zavření prohlížeče soubory cookie přístup. Tyto soubory cookie poslední po dobu platnosti přístupového tokenu. Soubory cookie jsou však obnovit, pokud je dosaženo času vypršení platnosti nebo pokud uživatel ručně odstraní soubor cookie. Doporučujeme ponechat výchozí nastavení **ne**, pouze zapnout v nastavení pro starší aplikace, které není sdílení souborů cookie mezi procesy.

Další informace o nové soubory cookie najdete v tématu [nastavení souborů Cookie pro přístup k místním aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – leden 2019

**Typ:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Funkce produktu:** Integrace třetích stran
 
V lednu 2019 jsme přidali podporu těchto 35 nových aplikací s federací do Galerie aplikací:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [talentu palety](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco zastřešující](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [Zscaler Správce přístupu k Internetu](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), [vypršení platnosti připomenutí](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [InstaVR prohlížeč](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [příkaz](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso digitální zavřete](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [ Cloudové služby PICCO](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [Workable](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial), [CallPlease](https://webapp.callplease.com/create-account/create-account.html), [Systém jednotného přihlašování GTNexus](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [ARECH pro podniky](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 pro Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD správce](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [Visitly](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [Korn Ferry ALP](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle cSaas platformy](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

Další informace o aplikacích najdete v tématu [integrace aplikací SaaS pomocí Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete v tématu [uvedení aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Nová vylepšení Azure AD Identity Protection (Public Preview)

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

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Nová funkce Zámek aplikace pro aplikaci Microsoft Authenticator na zařízeních s iOSem a Androidem

**Typ:** Nová funkce  
**Kategorie služby:** Aplikace Microsoft Authenticator  
**Funkce produktu:** Zabezpečení a ochrana identity

K lepšímu zabezpečení jednorázového hesla, informace o aplikaci a nastavení aplikace, můžete zapnout funkci App Lock v aplikaci Microsoft Authenticator. Zapnutí zámku aplikace znamená, že budete ke správě ověřování pomocí svůj PIN kód nebo biometrické pokaždé, když otevřete aplikaci Microsoft Authenticator.

Další informace najdete v tématu [aplikaci Microsoft Authenticator nejčastější dotazy k](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Vylepšené funkce exportování ve službě Azure AD Privileged Identity Management (PIM)

**Typ:** Nová funkce  
**Kategorie služby:** Privileged Identity Management  
**Funkce produktu:** Privileged Identity Management

Správci služby privileged Identity Management (PIM) můžete nyní exportovat všechna přiřazení rolí aktivní a bude možné pro určitý prostředek, který obsahuje přiřazení rolí pro všechny podřízené prostředky. Dříve bylo obtížné pro správce, chcete-li získat úplný seznam přiřazení rolí pro předplatné a měli zajišťující export přiřazení rolí pro každou konkrétní prostředek.

Další informace najdete v tématu [zobrazit historii aktivit a auditu pro role prostředků Azure v PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

## <a name="novemberdecember-2018"></a>Listopad/prosince 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Uživatelé, kteří se odebrali z oboru synchronizace, se už nepřepínají na čistě cloudové účty

**Typ:** Pevné  
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
**Funkce produktu:** Monitorování a sestavy

Audit i přihlášení datové proudy protokolu v nástroji Azure Monitor jsme aktuálně publikování, můžete bez problémů integrovat soubory protokolů pomocí nástrojů systému SIEM nebo pomocí Log Analytics. Na základě vaší zpětné vazby a během přípravy na tuto funkci ohlášení obecné dostupnosti, vám s tím následující změny naše schématu. První týden v lednu provede tyto změny ve schématu a jeho aktualizace související dokumentaci.

#### <a name="new-fields-in-the-audit-schema"></a>Nová pole ve schématu auditu
Přidáváme nový **typ operace** pole pro zadání typu operace provedena na prostředku. Například **přidat**, **aktualizace**, nebo **odstranit**.

#### <a name="changed-fields-in-the-audit-schema"></a>Změněné pole ve schématu auditu
Ve schématu auditu se mění následující pole:

|Název pole|Co se změnilo|Staré hodnoty|Nové hodnoty|
|----------|------------|----------|----------|
|Kategorie|To bylo **název služby** pole. Je teď **kategorií auditu** pole. **Název služby** byl přejmenován na **loggedByService** pole.|<ul><li>Zřizování účtů</li><li>Základní adresář</li><li>Samoobslužné resetování hesla</li></ul>|<ul><li>Správa uživatelů</li><li>Správa skupin</li><li>Správa aplikací</li></ul>|
|targetResources|Zahrnuje **TargetResourceType** na nejvyšší úrovni.|&nbsp;|<ul><li>Zásada</li><li>Aplikace</li><li>Uživatel</li><li>Skupina</li></ul>|
|loggedByService|Obsahuje název služby, která vygeneruje protokol auditu.|Null|<ul><li>Zřizování účtů</li><li>Základní adresář</li><li>Samoobslužné resetování hesla</li></ul>|
|Výsledek|Poskytuje výsledek funkce protokolů auditu. Dříve to bylo uvedené, ale zobrazujeme skutečnou hodnotu.|<ul><li>0</li><li>1</li></ul>|<ul><li>Úspěch</li><li>Selhání</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Změněné pole ve schématu přihlášení
Následující pole jsou změny ve schématu přihlášení:

|Název pole|Co se změnilo|Staré hodnoty|Nové hodnoty|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|To bylo **conditionalaccessPolicies** pole. Je teď **appliedConditionalAccessPolicies** pole.|Beze změny|Beze změny|
|conditionalAccessStatus|Poskytuje výsledek funkce stav zásad podmíněného přístup při přihlášení. Dříve to bylo uvedené, ale zobrazujeme skutečnou hodnotu.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Úspěch</li><li>Selhání</li><li>Nevztahuje se.</li><li>Zakázáno</li></ul>|
|appliedConditionalAccessPolicies: výsledek|Poskytuje výsledek funkce jednotlivých stavu podmíněného přístupu zásad při přihlášení. Dříve to bylo uvedené, ale zobrazujeme skutečnou hodnotu.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Úspěch</li><li>Selhání</li><li>Nevztahuje se.</li><li>Zakázáno</li></ul>|

Další informace o schématu najdete v tématu [protokolů auditu Azure AD interpretaci schématu ve službě Azure Monitor (preview)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Vylepšení Identity Protection pro model strojového učení s učitelem a modul skóre rizika

**Typ:** Změněná funkce  
**Kategorie služby:** Identity Protection  
**Funkce produktu:** Skóre rizika

Vylepšení související s Identity Protection uživatele a přihlašování riziko posouzení modul může pomoct zlepšit přesnost riziko uživatele a pokrytí. Správci si všimnout, že úroveň rizika uživatele je již přímo propojený úroveň rizika konkrétních detekcí a že je zvýšení počet a úroveň události rizikových přihlášení.

Detekce rizik jsou nyní vyhodnocovány pomocí technik strojového učení modelu, který se vypočítává riziko uživatele model detekcí a další funkce přihlášení uživatele. Na základě tohoto modelu, Správce může najít uživatele s vysokým rizikem skóre, i v případě detekce přidružené k tomuto uživateli je střední nebo nízké riziko. 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Správci můžou resetovat své vlastní heslo pomocí aplikace Microsoft Authenticator (Public Preview)

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

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Nová role Správce cloudových zařízení Azure AD (Public preview)

**Typ:** Nová funkce  
**Kategorie služby:** Registrace a správa zařízení  
**Funkce produktu:** Řízení přístupu

Správci mohou přiřadit uživatele k nové roli správce cloudových zařízení provádět úlohy správce cloudové zařízení. Uživatelé s rolí správce v zařízení cloudu můžete povolit, zakázat a odstraňovat zařízení ve službě Azure AD, spolu se nebudou moct číst klíče nástroje BitLocker systému Windows 10 (pokud existuje) na webu Azure Portal.

Další informace o rolích a oprávněních najdete v tématu [přiřazení rolí správce v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Správa zařízení pomocí nových časových razítek aktivity v Azure AD (Public Preview)

**Typ:** Nová funkce  
**Kategorie služby:** Registrace a správa zařízení  
**Funkce produktu:** Správa životního cyklu zařízení

Uvědomujeme si, že v čase musíte aktualizace a vyřazení zařízení vaší organizace ve službě Azure AD, abyste se vyhnuli nutnosti zastaralá zařízení předsazení ve vašem prostředí. Smyslem tohoto procesu, teď Azure AD aktualizuje vaše zařízení s časovým razítkem nové aktivity pomáhají spravovat životní cyklus vašeho zařízení.

Další informace o tom, jak získat a použít tento časové razítko, naleznete v tématu [How To: Správa zastaralých zařízení ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Správci můžou vyžadovat, aby uživatelé přijali podmínky použití na každém zařízení

**Typ:** Nová funkce  
**Kategorie služby:** Podmínky použití  
**Funkce produktu:** Zásady správného řízení
 
Správci mohou nyní zapnout **vyžadují, aby uživatelé vyjádřili souhlas na všech zařízeních** možnost vyžadovat, aby vaši uživatelé tak, aby přijímal vaše podmínky použití na všech zařízeních, že používáte ve svém tenantovi.

Další informace najdete v tématu [jednotlivá zařízení podmínky použití části Azure Active Directory Terms of použít funkci](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou#per-device-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Správci můžou nakonfigurovat vypršení platnosti podmínek použití podle plánu opakování

**Typ:** Nová funkce  
**Kategorie služby:** Podmínky použití  
**Funkce produktu:** Zásady správného řízení
 

Správci mohou nyní zapnout **vyprší souhlasy** vyprší možnosti nastavte podmínky použití pro všechny vaše uživatele podle zadaného plánu opakování. Plán může být každoročně, bi – roční, čtvrtletně nebo každý měsíc. Po vypršení platnosti podmínky použití, musí uživatelé znovu přijali jako.

Další informace najdete v tématu [přidat podmínky použití části Azure Active Directory Terms of použít funkci](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Správci můžou nakonfigurovat vypršení platnosti podmínek použití podle plánu jednotlivých uživatelů

**Typ:** Nová funkce  
**Kategorie služby:** Podmínky použití  
**Funkce produktu:** Zásady správného řízení

Správci teď mohou určit dobu trvání tento uživatel musí znovu přijali jako podmínky použití. Například Správci mohou určit, že uživatelé musí znovu přijali jako podmínky použití každých 90 dní.

Další informace najdete v tématu [přidat podmínky použití části Azure Active Directory Terms of použít funkci](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou#add-terms-of-use).
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Nové e-maily služby Azure AD Privileged Identity Management (PIM) pro role Azure Active Directory

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

### <a name="group-based-licensing-is-now-generally-available"></a>Licencování na základě skupin je teď obecně dostupné

**Typ:** Změněná funkce  
**Kategorie služby:** Ostatní  
**Funkce produktu:** Adresář

Licencování na základě skupiny je mimo verzi public preview a je teď obecně dostupná. Jako součást této obecné verzi jsme provedli tato funkce větší škálovatelnost a přidali možnost opětovné zpracování licencí přiřazení na základě skupin pro jednoho uživatele a možnost použití skupinové licencování s Office 365 E3 nebo A3 licence.

Další informace o licencování pro skupiny, najdete v části [co je založené na skupinách licencování v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – listopad 2018

**Typ:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Funkce produktu:** Integrace třetích stran
 
V listopadu 2018 jsme přidali podporu těchto 26 nových aplikací s federací do Galerie aplikací:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [stínovat Pe](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [upl.hodina](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://www.drivedollar.com/Account/Login), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [nekonečné Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya](https://alayagood.com/en/demo/), [ HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [odchylek](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy pro firmy centrální 365](https://accounting.zenegy.com/), [Everbridge člen portál](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [IDEO](https://profile.ideo.com/users/sign_up), [Ivanti portálu Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [jednotného přihlašování Allbound](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [složitých aplikací – Klasické testu](https://test.plexonline.com/signon), [složitých aplikací – klasické](https://www.plexonline.com/signon), [složitých aplikací – Test uživatelského rozhraní](https://test.cloud.plex.com/sso), [složitých aplikací – UX](https://cloud.plex.com/sso), [složitých aplikací – IAM](https://accounts.plex.com/), [Mě - pro péči o děti záznamů, účast a finanční sledování systému](https://getcrafts.ca/craftsregistration) 

Další informace o aplikacích najdete v tématu [integrace aplikací SaaS pomocí Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete v tématu [uvedení aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

## <a name="october-2018"></a>Říjen 2018

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Protokoly Azure AD teď fungují s Azure Log Analytics (Public Preview)

**Typ:** Nová funkce  
**Kategorie služby:** Vytváření sestav  
**Funkce produktu:** Monitorování a sestavy

S potěšením oznamujeme, že teď můžou přesměrovávat protokolů služby Azure AD do služby Azure Log Analytics. Tato funkce nejžádanějších pomáhá poskytnout ještě lepší přístup k analytics pro vaši firmu, operace a zabezpečení, jakož i způsob, jak pomoc při sledování infrastruktury. Další informace najdete v tématu [protokoly aktivit Azure Active Directory v Azure Log Analytics teď k dispozici](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) blogu.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – říjen 2018

**Typ:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Funkce produktu:** Integrace třetích stran
 
V říjnu 2018 jsme přidali podporu těchto 14 nových aplikací s federací do Galerie aplikací:

[Mé body ocenění](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), ambyint, [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), Dialpad, [virtuální prostředí ON24](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), [Zscalerem tři](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial), [Appraisd](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), [Workspot řízení](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

Další informace o aplikacích najdete v tématu [integrace aplikací SaaS pomocí Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete v tématu [uvedení aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-domain-services-email-notifications"></a>E-mailová oznámení Azure AD Domain Services

**Typ:** Nová funkce  
**Kategorie služby:** Azure AD Domain Services  
**Funkce produktu:** Azure AD Domain Services

Azure AD Domain Services poskytuje upozornění na portálu Azure portal o nesprávné konfigurace nebo problémy s vaší spravované domény. Tyto výstrahy obsahují podrobné pokyny, takže se můžete pokusit opravit problémy, aniž by museli kontaktovat podporu.

Od října, budete mít k přizpůsobení nastavení oznámení pro spravované domény, pokud dojde k nové výstrahy, e-mail je odeslán do určeným skupiny lidí, tím eliminuje nutnost neustále podívejte se na portál pro aktualizace.

Další informace najdete v tématu [nastavení oznámení ve službě Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Portál Azure AD podporuje odstraňování vlastních domén pomocí rozhraní API domény ForceDelete 

**Typ:** Změněná funkce  
**Kategorie služby:** Správa adresářů  
**Funkce produktu:** Adresář

S radostí oznamujeme, že teď můžete použít domény ForceDelete rozhraní API odstranit vaši vlastní názvy domén asynchronně přejmenováním odkazy, jako jsou uživatelé, skupiny a aplikace z vlastního názvu domény (contoso.com) zpět na počáteční výchozí doména název ( contoso.onmicrosoft.com).

Tato změna vám umožní rychleji odstranit názvů vlastních domén, pokud vaše organizace již název používá, nebo pokud je třeba použít název domény pomocí jiné služby Azure AD.

Další informace najdete v tématu [odstranit vlastní název domény](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name).

---

## <a name="september-2018"></a>Září 2018
 
### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Aktualizovala se oprávnění role správce pro dynamické skupiny

**Typ:** Pevné  
**Kategorie služby:** Správa skupin  
**Funkce produktu:** Spolupráce

Vyřešili jsme problém, konkrétní správce rolí teď můžete vytvářet a aktualizovat pravidla dynamického členství, aniž byste museli být vlastníkem skupiny.

Role jsou:

- Globální správce

- Správce Intune

- Správce uživatelů

Další informace najdete v tématu [vytvoření dynamické skupiny a zkontrolovat stav](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Zjednodušená nastavení konfigurace jednotného přihlašování pro některé aplikace třetích stran

**Typ:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Funkce produktu:** Jednotné přihlašování

Uvědomujeme si, že nastavení si jednotné přihlašování (SSO) k softwaru jako služby (SaaS) aplikací může být náročné vzhledem k jedinečné povaze konfiguraci jednotlivých aplikací. Sestavili jsme zjednodušené konfigurační prostředí k automaticky vyplní nastavení konfigurace jednotného přihlašování pro následující aplikace SaaS třetích stran:

- Zendesk

- ArcGis Online

- Jamf Pro

Chcete-li začít používat toto prostředí jedním kliknutím, přejděte **webu Azure portal** > **Konfigurace jednotného přihlašování** stránku pro aplikaci. Další informace najdete v tématu [integrace aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Azure Active Directory – Kde se ukládají vaše data? stránka

**Typ:** Nová funkce  
**Kategorie služby:** Ostatní  
**Funkce produktu:** GoLocal

Vyberte oblast vaší společnosti **Azure Active Directory – kde se nachází vaše data** stránky k zobrazení, které datové centrum Azure jsou uloženy vaše data služby Azure AD v klidovém stavu pro všechny služby Azure AD. Informace můžete filtrovat podle konkrétní služby Azure AD pro oblasti vaší společnosti.

Přístup k této funkci a další informace najdete v tématu [Azure Active Directory – kde se nachází vaše data](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Pro přístupový panel Moje aplikace je k dispozici nový plán nasazení

**Typ:** Nová funkce  
**Kategorie služby:** Moje aplikace  
**Funkce produktu:** Jednotné přihlašování

Podívejte se na nový plán nasazení, který je k dispozici pro Moje aplikace přístupového panelu (https://aka.ms/deploymentplans).
Moje aplikace přístupový panel poskytuje uživatelům na jednom místě vyhledat a získat přístup k jejich aplikacím. Tento portál poskytuje také uživatelům samoobslužné služby příležitosti, jako je žádost o přístup k aplikacím a skupinám nebo správy přístupu k těmto prostředkům jménem jiných uživatelů.

Další informace najdete v tématu [novinky na portálu Moje aplikace?](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Nová karta Řešení potíží a podpora na stránce Protokoly přihlášení na portálu Azure Portal

**Typ:** Nová funkce  
**Kategorie služby:** Vytváření sestav  
**Funkce produktu:** Monitorování a sestavy

Nové **řešení potíží a podpora** kartě **přihlášení** stránky Azure portal, má pomoci správce a pracovníky technické podpory řešit problémy spojené s přihlášení Azure AD. Tato nová karta poskytuje kód chyby, chybová zpráva a doporučeními k nápravě (pokud existuje) vám pomohou při řešení problému. Pokud nemůžete vyřešit problém, jsme také získáte nový způsob, jak vytvořit lístek podpory pomocí **kopírování do schránky** prostředí, která naplní **ID žádosti** a **Datum(UTC)** pole pro soubor protokolu v vašeho lístku podpory.  

![Protokoly přihlášení ukazující na nové kartě](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Vylepšená podpora vlastních vlastností rozšíření, které se používají k vytvoření pravidel dynamického členství

**Typ:** Změněná funkce  
**Kategorie služby:** Správa skupin  
**Funkce produktu:** Spolupráce

S touto aktualizací můžete kliknout **získat vlastnosti rozšíření vlastních** propojení z Tvůrce dynamický uživatel pravidlo skupiny, zadejte svoje ID a jedinečných aplikací a zobrazit úplný seznam vlastností vlastního rozšíření má použít při vytváření dynamické pravidla členství pro uživatele. Tento seznam můžete také aktualizovat, k získání nových vlastností vlastního rozšíření pro tuto aplikaci.

Další informace o použití rozšíření vlastních vlastností pro pravidla dynamického členství, naleznete v tématu [rozšíření vlastností a vlastností vlastního rozšíření](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nové schválené klientské aplikace pro podmíněný přístup na základě aplikace Azure AD

**Typ:** Plánování změn  
**Kategorie služby:** Podmíněný přístup  
**Funkce produktu:** Identita zabezpečení a ochrany

Tyto aplikace jsou na seznamu [schválené klientské aplikace](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference#approved-client-app-requirement):

- Microsoft To-Do

- Microsoft Stream

Další informace naleznete v tématu:

- [Na základě aplikace podmíněný přístup Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Nová podpora samoobslužného resetování hesla ze zamykací obrazovky ve Windows 7/8/8.1

**Typ:** Nová funkce  
**Kategorie služby:** SSPR  
**Funkce produktu:** Ověřování uživatelů

Po nastavení této nové funkce, se uživatelům zobrazí odkaz na resetování hesla z **Zámek** obrazovky zařízení se systémem Windows 7, Windows 8 nebo Windows 8.1. Když kliknete na tento odkaz, může uživatel v průvodci pomocí stejného procesu resetování hesla pomocí webového prohlížeče.

Další informace najdete v tématu [povolení heslo resetovat z Windows 7, 8 a 8.1](https://aka.ms/ssprforwindows78)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Oznámení o změně: Autorizační kódy se již nebude k dispozici pro opakované použití 

**Typ:** Plánování změn  
**Kategorie služby:** Ověřování (přihlášení)  
**Funkce produktu:** Ověřování uživatelů

Od 15. listopadu 2018, Azure AD, přestane přijímat předchozích ověřovací kódy pro aplikace. Tato změna zabezpečení pomáhá vám Azure AD podle specifikace OAuth a vynutí u koncových bodů v1 i v2.

Pokud vaše aplikace opakovaně používá autorizační kódy k získání tokenů pro několik prostředků, doporučujeme použít kód pro získání tokenu obnovení a pak pomocí tohoto tokenu obnovení získat další tokeny pro jiné prostředky. Autorizační kódy lze použít pouze jednou, ale obnovovací tokeny lze použít více než jednou v několika prostředcích. Aplikace, která se pokusí znovu použít ověřovacího kódu během toku OAuth kódu se zobrazí chyba invalid_grant.

Tato a další související s protokoly změny najdete v tématu [úplný seznam co je nového v ověřování](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – září 2018

**Typ:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Funkce produktu:** Integrace třetích stran
 
V září 2018 jsme přidali podporu těchto 16 nové aplikace s federací do Galerie aplikací:

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [Comeet náboru softwaru](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial), [Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), [ArcGIS Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), [ JDA Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial), [Snowflake](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), NavigoCloud, [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial), join.me, [ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial), [Silverback](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial), Riverbed Xirrus EasyPass, [jednotného přihlašování Rackspace](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial), Enlyft jednotné přihlašování pro Azure, SurveyMonkey [Convene](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial), [dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial)

Další informace o aplikacích najdete v tématu [integrace aplikací SaaS pomocí Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete v tématu [uvedení aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="support-for-additional-claims-transformations-methods"></a>Podporují se další metody transformace deklarací identity

**Typ:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Funkce produktu:** Jednotné přihlašování

Zavedli jsme nové metody transformace deklarací identity, ToLower() byly a ToUpper(), který je možné použít na tokeny SAML ze SAML podle **Konfigurace jednotného přihlašování** stránky.

Další informace najdete v tématu [přizpůsobení deklarací identity vystavených v tokenu SAML pro podnikové aplikace ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Aktualizovalo se uživatelské rozhraní konfigurace aplikací založených na SAML (Preview)

**Typ:** Změněná funkce  
**Kategorie služby:** Podnikové aplikace  
**Funkce produktu:** Jednotné přihlašování

Jako součást naší uživatelské rozhraní pro konfiguraci aktualizovaná aplikace založené na SAML získáte:

- Názorný postup aktualizované prostředí pro konfiguraci vaší aplikace založené na SAML.

- Větší přehled o tom, co je chybějící nebo nesprávné ve vaší konfiguraci.

- Možnost přidat více e-mailové adresy pro oznámení o vypršení platnosti certifikátu.

- Nové metody transformace deklarací identity, ToLower() byly a ToUpper() a další.

- Způsob, jak nahrát vlastní token podpisový certifikát pro podnikové aplikace.

- Způsob, jak nastavit formátu NameID pro aplikace SAML a způsob, jak nastavit hodnotu NameID jako rozšíření adresáře.

K zapnutí nastavení v této aktualizaci zobrazení, klikněte na tlačítko **vyzkoušet naše nové rozhraní** odkaz z horní části **Single Sign-On** stránky. Další informace najdete v tématu [kurzu: Konfigurace založené na SAML jednotného přihlašování pro aplikaci pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-portal).