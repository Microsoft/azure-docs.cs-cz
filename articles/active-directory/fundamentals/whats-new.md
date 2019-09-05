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
ms.date: 07/31/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 197df5c5467a9cea9a76c888569752ed2576d2d2
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70375746"
---
# <a name="whats-new-in-azure-active-directory"></a>Co je nového ve službě Azure Active Directory?

>Přečtěte si informace o tom, kdy se má tato stránka na aktualizace znovu navštívit zkopírováním `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` a vložením této adresy URL](./media/whats-new/feed-icon-16x16.png) : ![do čtečky informačního kanálu RSS.

Azure AD obdrží vylepšení průběžně. Abyste mohli používat aktuální pomocí nejnovější vývoj, tento článek poskytuje informace o:

- Nejnovější vydané verzi
- Známé problémy
- Opravy chyb
- Zastaralé funkce
- Plány pro změny

Tato stránka se aktualizuje každý měsíc, takže návštěvě pravidelně. Pokud hledáte položky, které jsou starší než šest měsíců, najdete je v archivu, abyste mohli [novinky v Azure Active Directory](whats-new-archive.md).

---

## <a name="august-2019"></a>Srpen 2019

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Plán změny: Vyřazení balíčků obsahu Power BI

**Textový** Plánování změn  
**Kategorie služby:** Vytváření sestav  
**Schopnost produktu:** Monitorování & vytváření sestav

Od 1. října 2019 začne Power BI začínat všechny balíčky obsahu, včetně balíčku obsahu Power BI Azure AD. Jako alternativu k tomuto balíčku obsahu můžete použít sešity Azure AD a získat přehled o službách souvisejících s Azure AD. Další sešity se připravují, včetně sešitů o zásadách podmíněného přístupu v režimu pouze sestavy, přehledů na základě souhlasu aplikace a dalších.

Další informace o sešitech najdete v tématu [Jak používat Azure monitor sešity pro Azure Active Directory sestavy](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks). Další informace o vyřazení balíčků obsahu najdete v blogovém příspěvku s [oznámením o obecných dostupnosti pro Power BI šablonových aplikací](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/) .

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Pro správu registrace aplikací jsou k dispozici nové vlastní role (Public Preview)

**Textový** Nová funkce  
**Kategorie služby:** RBAC  
**Schopnost produktu:** Řízení přístupu

Vlastní role (k dispozici v rámci předplatného Azure AD P1 nebo P2) vám teď můžou pomáhat s jemnou přístupem tím, že vám umožní vytvořit definice rolí s konkrétními oprávněními a pak tyto role přiřadit konkrétním prostředkům. V současné době vytvoříte vlastní role pomocí oprávnění pro správu registrací aplikací a potom přiřadíte roli ke konkrétní aplikaci. Další informace o vlastních rolích najdete v tématu [vlastní role správců v Azure Active Directory (Preview)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview).

Pokud potřebujete další oprávnění nebo prostředky, které se v tuto chvíli nezobrazuje, můžete poslat svůj názor na náš [web Azure Feedback](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) a my přidáme vaši žádost do naší mapy aktualizace.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>Nové protokoly zřizování vám můžou pomoct monitorovat a řešit potíže s nasazením zřizování aplikací (Public Preview).

**Textový** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** Správa životního cyklu identit

K dispozici jsou nové protokoly zřizování, které vám pomůžou monitorovat a řešit potíže s nasazením zřizování uživatelů a skupin. Tyto nové soubory protokolu obsahují informace o:

- Které skupiny byly úspěšně vytvořeny v [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)
- Jaké role byly naimportovány z [Amazon Web Services (AWS)](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial#configure-and-test-azure-ad-single-sign-on)
- Kteří zaměstnanci nebyli naimportovali z [Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)

Další informace najdete v tématu [zřizování sestav na portálu Azure Active Directory (Preview)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs).

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Nové sestavy zabezpečení pro všechny správce Azure AD (všeobecně dostupné)

**Textový** Nová funkce  
**Kategorie služby:** Identity Protection  
**Schopnost produktu:** Zabezpečení identity & ochrana

Ve výchozím nastavení budou mít všichni správci služby Azure AD brzy přístup k moderním sestavám zabezpečení v rámci Azure AD. Až do konce září budete moct použít banner v horní části moderních sestav zabezpečení pro návrat k původním sestavám.

Moderní sestavy zabezpečení poskytují další možnosti ze starších verzí, včetně těchto:

- Rozšířené filtrování a řazení
- Hromadné akce, jako je například chybějící riziko uživatele
- Potvrzení napadených nebo bezpečných entit
- Stav rizika, pokrytí: Ohrožené, neúspěšné, opravené a potvrzené ohrožení
- Nové detekce související s riziky (k dispozici pro Azure AD Premium předplatitele)

Další informace najdete v tématech [rizikové uživatele](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users-report), [rizikové přihlašovací](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins-report)údaje a [detekce rizik](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risk-detections-report).

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>Spravovaná identita přiřazená uživatelem je k dispozici pro Virtual Machines a Virtual Machine Scale Sets (Obecná dostupnost).

**Textový** Nová funkce  
**Kategorie služby:** Spravované identity pro prostředky Azure  
**Schopnost produktu:** Prostředí pro vývojáře

Spravované identity přiřazené uživatelem jsou teď všeobecně dostupné pro Virtual Machines a Virtual Machine Scale Sets. V rámci této služby může Azure vytvořit identitu v tenantovi Azure AD, která je důvěryhodná pro používané předplatné, a dá se přiřadit k jedné nebo více instancím služby Azure. Další informace o spravovaných identitách přiřazených uživateli najdete v tématu [co jsou spravované identity pro prostředky Azure](https://aka.ms/azuremanagedidentity).

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>Uživatelé můžou resetovat svá hesla pomocí mobilní aplikace nebo hardwarového tokenu (všeobecně dostupná).

**Textový** Změněná funkce  
**Kategorie služby:** Samoobslužné resetování hesla  
**Schopnost produktu:** Ověřování uživatelů

Uživatelé, kteří si zaregistrovali mobilní aplikaci ve vaší organizaci, teď můžou resetovat oznámení z aplikace Microsoft Authenticator nebo zadáním kódu z mobilní aplikace nebo hardwarového tokenu.

Další informace najdete v tématu [jak funguje: Samoobslužné resetování](https://aka.ms/authappsspr)hesla služby Azure AD. Další informace o uživatelském prostředí najdete v tématu [resetování vlastního pracovního nebo školního hesla](https://docs.microsoft.com/azure/active-directory/user-help/user-help-password-reset-overview).

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET ignoruje sdílenou mezipaměť MSAL.NET pro scénáře v zastoupení.

**Textový** Pevné  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů

Počínaje službou Azure AD Authentication Library (ADAL.NET) verze 5.0.0-Preview musí vývojáři aplikací [serializovat jednu mezipaměť na účet pro webové aplikace a webová rozhraní API](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api). V opačném případě může dojít ke zvýšení oprávnění v některých scénářích využívajících [tok](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow), a to spolu `UserAssertion`s některými konkrétními případy použití. Aby se zabránilo této chybě, ADAL.NET nyní ignoruje sdílenou mezipaměť Microsoft Authentication Library for dotnet (MSAL.NET) pro scénáře v zastoupení.

Další informace o tomto problému naleznete v tématu [Azure Active Directory zvýšení úrovně zabezpečení oprávnění v knihovně ověřování](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Nové federované aplikace dostupné v Aplikace Azure AD galerii – srpen 2019

**Textový** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** Integrace třetích stran

V srpnu 2019 jsme do Galerie aplikací přidali tyto 26 nových aplikací s podporou federace:

[Civic Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/civic-platform-tutorial), [Amazon Business](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-business-tutorial), [ProNovos OPS Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-ops-manager-tutorial), [Cognidox](https://docs.microsoft.com/azure/active-directory/saas-apps/cognidox-tutorial), [Viareport portál Inativ (Evropa)](https://docs.microsoft.com/azure/active-directory/saas-apps/viareports-inativ-portal-europe-tutorial), [Azure Databricks](https://azure.microsoft.com/services/databricks), [Robin](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial), [Docházka pro Academy](https://docs.microsoft.com/azure/active-directory/saas-apps/academy-attendance-tutorial), [prioritní matrice](https://sync.appfluence.com/pmwebng/), [Cousto MySpace](https://cousto.platformers.be/account/login), [Uploadcare](https://uploadcare.com/accounts/signup/), [Carbonite Backup pro koncové body](https://docs.microsoft.com/azure/active-directory/saas-apps/carbonite-endpoint-backup-tutorial), [CPQSync podle Cincom](https://docs.microsoft.com/azure/active-directory/saas-apps/cpqsync-by-cincom-tutorial), [Chargebee](https://docs.microsoft.com/azure/active-directory/saas-apps/chargebee-tutorial), [Delivery. Media™ Portal](https://portal.deliver.media), [prvotní vzdělávání](https://docs.microsoft.com/azure/active-directory/saas-apps/frontline-education-tutorial), [F5](https://www.f5.com/products/security/access-policy-manager), [stashcat AD připojení](https://www.stashcat.com), [blikání](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial), [Vocoli](https://docs.microsoft.com/azure/active-directory/saas-apps/vocoli-tutorial), [ProNovos Analytics](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-analytics-tutorial), [Sigstr](https://docs.microsoft.com/azure/active-directory/saas-apps/sigstr-tutorial), [Darwinbox](https://docs.microsoft.com/azure/active-directory/saas-apps/darwinbox-tutorial), [sledování pomocí barev](https://docs.microsoft.com/azure/active-directory/saas-apps/watch-by-colors-tutorial), [](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial) [přecházení a EAB navigace strategické péče](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-strategic-care-tutorial)

Další informace o aplikacích najdete v tématu [integrace aplikací SaaS pomocí Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete v tématu [uvedení aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>K dispozici jsou nové verze modulů PowerShellu pro AzureAD a prostředí AzureADPreview PowerShell.

**Textový** Změněná funkce  
**Kategorie služby:** Ostatní  
**Schopnost produktu:** Adresář

K dispozici jsou nové aktualizace modulů PowerShellu pro AzureAD a AzureAD ve verzi Preview:

- Do parametru v modulu AzureAD byl přidán nový `-Filter` parametr. `Get-AzureADDirectoryRole` Tento parametr vám pomůže filtrovat role adresáře vrácené rutinou.
- Do modulu AzureADPreview byly přidány nové rutiny, které vám pomůžou definovat a přiřazovat vlastní role v Azure AD, včetně těchto:

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>Vylepšení uživatelského rozhraní tvůrce pravidla dynamické skupiny na webu Azure Portal

**Textový** Změněná funkce  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Spolupráce

Provedli jsme některá vylepšení uživatelského rozhraní pro tvůrce pravidel dynamické skupiny, který je k dispozici v Azure Portal, abyste mohli snadněji nastavit nové pravidlo nebo změnit stávající pravidla. Toto vylepšení návrhu umožňuje vytvořit pravidla s až pěti výrazy, nikoli jenom s jedním. Aktualizovali jsme také seznam vlastností zařízení, aby se odebraly zastaralé vlastnosti zařízení.

Další informace najdete v tématu [Správa pravidel dynamického členství](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-update-rule).

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>Nové oprávnění aplikace Microsoft Graph k dispozici pro použití s recenzemi přístupu

**Textový** Změněná funkce  
**Kategorie služby:** Kontroly přístupu  
**Schopnost produktu:** Zásady správného řízení identit

Zavedli jsme nové oprávnění `AccessReview.ReadWrite.Membership`aplikace Microsoft Graph, které umožňuje aplikacím automaticky vytvářet a načítat kontroly přístupu pro členství ve skupinách a přiřazení aplikací. Toto oprávnění můžou použít naplánované úlohy nebo v rámci automatizace, aniž by bylo nutné přihlášený kontext uživatele.

Další informace najdete v [příkladu vytváření kontrol přístupu Azure AD pomocí Microsoft Graph oprávnění aplikace s blogem PowerShellu](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241).

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Protokoly aktivit Azure AD jsou teď dostupné pro státní cloudové instance v Azure Monitor

**Textový** Změněná funkce  
**Kategorie služby:** Vytváření sestav  
**Schopnost produktu:** Monitorování & vytváření sestav

S radostí oznamujeme, že protokoly aktivit Azure AD jsou teď dostupné pro státní cloudové instance v Azure Monitor. Protokoly Azure AD teď můžete odesílat do svého účtu úložiště nebo do centra událostí, abyste je mohli integrovat s SIEM nástroji, jako je [SumoLogic](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic), [Splunk](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-splunk)a [ArcSight](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-arcsight). 

Další informace o nastavení Azure Monitor najdete [v tématu protokoly aktivit Azure AD v Azure monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor#cost-considerations).

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Aktualizace uživatelů na nové a rozšířené prostředí informací o zabezpečení

**Textový** Změněná funkce  
**Kategorie služby:**  Ověřování (přihlášení)   
**Schopnost produktu:** Ověřování uživatelů

25. září 2019 se vypíná staré, nerozšířené informace o zabezpečení pro registraci a správu bezpečnostních údajů uživatelů a zapínání nové [Rozšířené verze](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271). To znamená, že uživatelé již nebudou moci používat staré prostředí.

Další informace o prostředí rozšířené informace o zabezpečení najdete v naší [dokumentaci](https://aka.ms/securityinfodocs) pro správu a v [dokumentaci k uživateli](https://aka.ms/securityinfoguide).

#### <a name="to-turn-on-this-new-experience-you-must"></a>Pokud chcete toto nové prostředí zapnout, musíte:

1. Přihlaste se k Azure Portal jako globální správce nebo Správce uživatelů.

2. Přejděte na **Azure Active Directory > nastavení uživatele > spravovat nastavení pro přístupové panely funkce verze Preview**.

3. V části **Uživatelé můžou používat funkce verze Preview pro registraci a správu informací o zabezpečení** , vyberte **vybrané**a potom buď zvolte skupinu uživatelů, nebo zvolte možnost **vše** , pokud chcete tuto funkci zapnout pro všechny uživatele v tenantovi.

4. V části * * Uživatelé můžou používat funkce verze Preview pro registraci a správu zabezpečení * * * * * *, vyberte **žádné**.

5. Uložte nastavení.

    Po uložení nastavení už nebudete mít přístup k původnímu prostředí bezpečnostní informace.

>[!Important]
>Pokud tento postup nedokončíte před 25. září 2019, bude se váš tenant Azure Active Directory automaticky povolit pro vylepšené prostředí. Pokud máte nějaké otázky, kontaktujte nás na adrese registrationpreview@microsoft.com.

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>Požadavky na ověřování pomocí přihlašovacích údajů po přihlášení budou striktně ověřeny.

**Textový** Změněná funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Standardy

Od 2. září 2019 budou žádosti o ověření pomocí metody POST u standardů protokolu HTTP striktně ověřeny. Konkrétně mezery a dvojité uvozovky (") již nebudou odebrány z hodnot formuláře žádosti. Tyto změny se neočekávají pro přerušení stávajících klientů a pomůžou zajistit, aby se požadavky odeslané do služby Azure AD spolehlivě zpracovávají pokaždé.

Další informace najdete v tématu o [nejnovějších změnách Azure AD](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored).

---

## <a name="july-2019"></a>Červenec 2019

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Plán změny: Aktualizace služby proxy aplikací pro podporu pouze TLS 1,2

**Textový** Plánování změn  
**Kategorie služby:** Proxy aplikace  
**Schopnost produktu:** Řízení přístupu

Abychom vám pomohli získat naše nejsilnější šifrování, zahajte omezení přístupu služby proxy aplikací jenom na protokoly TLS 1,2. Toto omezení se zpočátku zavede na zákazníky, kteří už používají protokoly TLS 1,2, takže se jeho dopad neprojeví. Dokončení vyřazení protokolů TLS 1,0 a TLS 1,1 bude dokončeno 31. srpna 2019. Zákazníci, kteří pořád používají TLS 1,0 a TLS 1,1, budou dostávat Pokročilá oznámení o přípravě na tuto změnu.

Chcete-li zachovat připojení ke službě proxy aplikací v rámci této změny, doporučujeme, abyste se ujistili, že jsou kombinace klientských serverů a prohlížeče a serveru aktualizovány tak, aby používaly protokol TLS 1,2. Doporučujeme také, abyste měli všechny systémy klientů používané vašimi zaměstnanci k přístupu k aplikacím, které jsou publikované prostřednictvím služby proxy aplikací.

Další informace najdete v tématu [Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikace v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application).

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Plán změny: Pro galerii aplikací přicházejí aktualizace návrhu.

**Textový** Plánování změn  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** Jednotné přihlašování

Nové změny uživatelského rozhraní přicházejí do návrhu **Přidat z oblasti Galerie** v okně **Přidat aplikaci** . Tyto změny vám pomůžou snadněji najít vaše aplikace, které podporují Automatické zřizování, OpenID Connect, Security Assertion Markup Language (SAML) a jednotné přihlašování (SSO) k heslu.

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Plán změny: Odebrání IP adresy serveru MFA z IP adresy Office 365

**Textový** Plánování změn  
**Kategorie služby:** MFA  
**Schopnost produktu:** Zabezpečení identity & ochrana

Odebíráme IP adresu MFA serveru z [webové služby IP adresa a adresa URL sady Office 365](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service). Pokud aktuálně spoléháte na tyto stránky, abyste mohli aktualizovat nastavení brány firewall, musíte se ujistit, že máte také seznam IP adres, které jsou popsány v části **požadavky na bránu firewall pro Azure Multi-Factor Authentication Server** v části [Začínáme. s](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements) využitím článku Azure Multi-Factor Authentication Server.

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Tokeny jenom pro aplikace teď vyžadují, aby klientská aplikace existovala v tenantovi prostředků.

**Textový** Pevné  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů

26. července 2019 jsme změnili, jak poskytujeme tokeny jenom pro aplikace prostřednictvím [udělení přihlašovacích údajů klienta](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow). Dříve mohli aplikace získat tokeny pro volání jiných aplikací bez ohledu na to, jestli klientská aplikace byla v tenantovi. Toto chování jsme aktualizovali tak, aby se prostředky jednoho tenanta, někdy označované jako webové rozhraní API, mohly volat jenom pro klientské aplikace, které existují v tenantovi prostředků.

Pokud se vaše aplikace nenachází v tenantovi prostředků, zobrazí se chybová zpráva s informacemi o tom, `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` jak tento problém vyřešit, musíte vytvořit instanční objekt klientské aplikace v tenantovi, a to pomocí [koncového bodu souhlasu správce](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint) nebo [přes PowerShell. ](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell), který zajišťuje, aby měl váš tenant oprávnění aplikace pro práci v rámci tenanta.

Další informace najdete v tématu [co je nového pro ověřování?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant).

> [!NOTE]
> Stávající souhlas mezi klientem a rozhraním API se dál nepožaduje. Aplikace by měly stále provádět vlastní kontroly autorizace.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Nové přihlášení ke službě Azure AD pomocí klíčů zabezpečení FIDO2 pro nové heslo

**Textový** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů

Zákazníci Azure AD teď můžou nastavit zásady pro správu FIDO2 bezpečnostních klíčů pro uživatele a skupiny organizace. Koncoví uživatelé také mohou zaregistrovat své bezpečnostní klíče sami, pomocí klíčů se přihlásíte ke svým účtům Microsoft na webech, zatímco na zařízeních s podporou FIDO, a také se přihlásíte ke svým zařízením s Windows 10 připojeným k Azure AD.

Další informace najdete v tématu [Povolení přihlášení bez hesla pro Azure AD (Preview)](/azure/active-directory/authentication/concept-authentication-passwordless) pro informace týkající se správců a [Nastavení bezpečnostních údajů pro použití bezpečnostního klíče (Preview)](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) pro informace týkající se koncových uživatelů.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Nové federované aplikace dostupné v Aplikace Azure AD galerii – červenec 2019

**Textový** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** Integrace třetích stran

V červenci 2019 jsme do Galerie aplikací přidali tyto 18 nových aplikací s podporou federace:

[Ungerboeck software](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial), [jasný vzor Omnichannel kontakt Center](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial), [chytřejší Nelly](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial), [AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial), [LOOOP](https://www.looop.co/schedule-a-demo/), [Productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial), [přístup k aplikaci MS Azure SSO pro Ethidex dodržování předpisů Hype™](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso), [](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial), [ Abstraktní](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial), [stoupání](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial), [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [Wandera](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial), [TwineSocial](https://twinesocial.com/), [Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial), [HyperAnna](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial), [PharmID WasteWitness](https://www.pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), [JFrog Artifactory](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

Další informace o aplikacích najdete v tématu [integrace aplikací SaaS pomocí Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete v tématu [uvedení aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizace zřizování uživatelských účtů pro tyto nově podporované aplikace SaaS

**Textový** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** Monitorování & vytváření sestav

Nyní můžete automatizovat vytváření, aktualizaci a odstraňování uživatelských účtů pro tyto nově integrované aplikace:

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Federovaný adresář](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Další informace o tom, jak lépe zabezpečit organizaci pomocí automatizovaného zřizování uživatelských účtů, najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) .

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Nová značka služby Azure AD Domain Services pro skupinu zabezpečení sítě

**Textový** Nová funkce  
**Kategorie služby:** Azure AD Domain Services  
**Schopnost produktu:** Azure AD Domain Services

Pokud už vás unavuje správu dlouhých seznamů IP adres a rozsahů, můžete použít novou značku síťové služby **AzureActiveDirectoryDomainServices** ve skupině zabezpečení sítě Azure, která vám pomůžou zabezpečit příchozí provoz na Azure AD Domain Services Virtual. podsíť sítě.

Další informace o této nové značce služby najdete v tématu [skupiny zabezpečení sítě pro Azure AD Domain Services](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nové audity zabezpečení pro Azure AD Domain Services (Public Preview)

**Textový** Nová funkce  
**Kategorie služby:** Azure AD Domain Services  
**Schopnost produktu:** Azure AD Domain Services

S radostí oznamujeme vydání auditu zabezpečení Azure AD Domain Service do veřejné verze Preview. Auditování zabezpečení pomáhá poskytnout důležitý pohled na vaše služby ověřování – streamování událostí auditu zabezpečení do cílových prostředků, včetně Azure Storage, pracovních prostorů Azure Log Analytics a centra událostí Azure s využitím služby Azure AD Domain Services. bran.

Další informace najdete v tématu [Povolení auditů zabezpečení pro Azure AD Domain Services (Preview)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Nové použití metod ověřování & Insights (Public Preview)

**Textový** Nová funkce  
**Kategorie služby:** Samoobslužné resetování hesla  
**Schopnost produktu:** Monitorování & vytváření sestav

Nové metody ověřování & sestavy Insights vám pomůžou pochopit, jak se registrují a používají funkce jako Azure Multi-Factor Authentication a Samoobslužné resetování hesla ve vaší organizaci, včetně počtu registrovaných Uživatelé pro každou funkci, jak často se Samoobslužné resetování hesla používá k resetování hesel a podle toho, jakou metodu se resetuje.

Další informace najdete v tématu [použití metod ověřování & Insights (Preview)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Pro všechny správce Azure AD jsou k dispozici nové sestavy zabezpečení (Public Preview)

**Textový** Nová funkce  
**Kategorie služby:** Identity Protection  
**Schopnost produktu:** Zabezpečení identity & ochrana

Všichni Správci služby Azure AD teď můžou v horní části existujících sestav zabezpečení vybrat hlavičku, jako je například zpráva **uživatelů označených příznakem rizika** , aby mohli začít používat nové prostředí zabezpečení, jak je znázorněno v sestavách **rizikové uživatele** a **rizikových přihlášení** . . V průběhu času se všechny sestavy zabezpečení přesunou ze starších verzí do nových verzí a nové sestavy poskytují následující další možnosti:

- Rozšířené filtrování a řazení

- Hromadné akce, jako je například chybějící riziko uživatele

- Potvrzení napadených nebo bezpečných entit

- Stav rizika, pokrytí: Ohrožené, neúspěšné, opravené a potvrzené ohrožení

Další informace najdete v tématu Sestava [rizikových uživatelů](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users-report) a [sestavy rizikových přihlášení](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins-report).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nové audity zabezpečení pro Azure AD Domain Services (Public Preview)

**Textový** Nová funkce  
**Kategorie služby:** Azure AD Domain Services  
**Schopnost produktu:** Azure AD Domain Services

S radostí oznamujeme vydání auditu zabezpečení Azure AD Domain Service do veřejné verze Preview. Auditování zabezpečení pomáhá poskytnout důležitý pohled na vaše služby ověřování – streamování událostí auditu zabezpečení do cílových prostředků, včetně Azure Storage, pracovních prostorů Azure Log Analytics a centra událostí Azure s využitím služby Azure AD Domain Services. bran.

Další informace najdete v tématu [Povolení auditů zabezpečení pro Azure AD Domain Services (Preview)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Nová federace B2B Direct pomocí SAML/WS-dodávání (Public Preview)

**Textový** Nová funkce  
**Kategorie služby:** B2B  
**Schopnost produktu:** B2B/B2C

Přímá federace pomáhá lépe pracovat s partnery, jejichž řešení identity spravované IT není Azure AD, a to díky práci se systémy identit, které podporují standardy SAML nebo WS-dodávání. Po nastavení přímého federačního vztahu s partnerem může každý nový uživatel typu Host, kterého budete pozvat z této domény, spolupracovat s vámi pomocí stávajícího účtu organizace, takže uživatelské prostředí pro hosty bude bezproblémové.

Další informace najdete v tématu [Přímá federace pomocí AD FS a poskytovatelů třetích stran pro uživatele typu Host (Preview)](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizace zřizování uživatelských účtů pro tyto nově podporované aplikace SaaS

**Textový** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** Monitorování & vytváření sestav

Nyní můžete automatizovat vytváření, aktualizaci a odstraňování uživatelských účtů pro tyto nově integrované aplikace:

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Federovaný adresář](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Další informace o tom, jak lépe zabezpečit vaši organizaci pomocí automatizovaného zřizování uživatelských účtů, najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Nová kontrolu duplicitních názvů skupin na portálu Azure AD

**Textový** Nová funkce  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Spolupráce

Když teď vytváříte nebo aktualizujete název skupiny z portálu Azure AD, provedeme kontrolu a zjistíme, jestli duplikujete existující název skupiny v prostředku. Pokud určíme, že se tento název už používá v jiné skupině, zobrazí se výzva, abyste změnili své jméno.

Další informace najdete v tématu [Správa skupin na portálu Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Azure AD teď podporuje statické parametry dotazů v identifikátorech URI odpovědi (přesměrování).

**Textový** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů

Aplikace Azure AD teď můžou registrovat a používat identifikátory URI odpovědi (přesměrování) s parametry statického dotazu (například `https://contoso.com/oauth2?idp=microsoft`) pro žádosti OAuth 2,0. Parametr statického dotazu podléhá shodě řetězců pro identifikátory URI odpovědi, stejně jako všechny ostatní části identifikátoru URI odpovědi. Pokud neexistuje žádný registrovaný řetězec, který by odpovídal identifikátoru URI přesměrování adresy URL s dekódováním, požadavek se odmítne. Pokud se najde identifikátor URI odpovědi, použije se celý řetězec pro přesměrování uživatele, včetně parametru statického dotazu.

Dynamické identifikátory URI odpovědi jsou stále zakázané, protože představují bezpečnostní riziko a nelze je použít k uchování informací o stavu v rámci žádosti o ověření. Pro účely tohoto účelu použijte `state` parametr.

V současné době se na obrazovkách registrace aplikace Azure Portal pořád zablokují parametry dotazu. Manifest aplikace ale můžete upravit ručně a přidat a otestovat parametry dotazu v aplikaci. Další informace najdete v tématu [co je nového pro ověřování?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters).

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Protokoly aktivit (rozhraní API MS Graph) pro službu Azure AD jsou teď dostupné prostřednictvím rutin PowerShellu.

**Textový** Nová funkce  
**Kategorie služby:** Vytváření sestav  
**Schopnost produktu:** Monitorování & vytváření sestav

S radostí oznamujeme, že protokoly aktivit Azure AD (sestavy auditu a přihlášení) jsou teď dostupné prostřednictvím modulu Azure AD PowerShellu. Dřív jste mohli vytvořit vlastní skripty pomocí koncových bodů MS Graph API a teď jsme tuto možnost rozšířili na rutiny PowerShellu.

Další informace o tom, jak tyto rutiny použít, najdete v tématu [rutiny Azure AD PowerShell pro vytváření sestav](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting).

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Aktualizované ovládací prvky filtru pro audit a protokoly přihlašování v Azure AD

**Textový** Změněná funkce  
**Kategorie služby:** Vytváření sestav  
**Schopnost produktu:** Monitorování & vytváření sestav

Aktualizovali jsme sestavy protokolu auditu a přihlašování, takže teď můžete použít různé filtry, aniž byste je museli přidávat jako sloupce na obrazovkách sestavy. Navíc teď můžete rozhodnout, kolik filtrů chcete na obrazovce zobrazit. Tyto aktualizace společně usnadňují čtení a větší rozsah vašich potřeb vašich sestav.

Další informace o těchto aktualizacích najdete v tématech [filtrování protokolů auditu](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs) a [filtrování aktivit přihlášení](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities).

---

## <a name="june-2019"></a>Červeně 2019

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Nové rozhraní riskDetections API pro Microsoft Graph (Public Preview)

**Textový** Nová funkce  
**Kategorie služby:** Identity Protection  
**Schopnost produktu:** Zabezpečení identity & ochrana

S potěšením oznamujeme, že nové rozhraní riskDetections API pro Microsoft Graph je teď ve verzi Public Preview. Toto nové rozhraní API můžete použít k zobrazení seznamu uživatelů s ochranou identity v organizaci a zjišťování rizik přihlašování. Toto rozhraní API můžete použít také k efektivnějšímu dotazování detekce rizik, včetně podrobností o typu detekce, stavu, úrovni a dalších.

Další informace najdete v [referenční dokumentaci k rozhraní API pro detekci rizik](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – červen 2019

**Textový** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** Integrace třetích stran

Do června 2019 jsme do Galerie aplikací přidali tyto 22 nových aplikací s podporou federace:

[Azure AD SAML Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial), [Otsuka Shokai (大塚商会)](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial), [ANAQUA](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial), [klient VPN Azure](https://portal.azure.com/), [ExpenseIn](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial), pomocná [pomocná](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial)sada, [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial), [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial), [Mercedes-Benz](https://me.secure.mercedes-benz.com/), skore [,,](https://app.justskore.it/) [Konzola cloudové infrastruktury Oracle](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [CyberArk ověřování SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), [scrible edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise OS](https://proptimise.co.uk/software/), [Vtiger CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), Oracle Access Manager pro Oracle Maloobchodní prodej, Oracle Access Manager pro Oracle E-Business Suite, Oracle IDCS pro E-Business Suite, Oracle IDCS pro PeopleSoft spouštěných místně, Oracle IDCS pro řešení JD Edwards

Další informace o aplikacích najdete v tématu [integrace aplikací SaaS pomocí Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete v tématu [uvedení aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizace zřizování uživatelských účtů pro tyto nově podporované aplikace SaaS

**Textový** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** Monitorování & vytváření sestav

Nyní můžete automatizovat vytváření, aktualizaci a odstraňování uživatelských účtů pro tyto nově integrované aplikace:

- [Přiblížení](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Zástupné](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

Další informace o tom, jak lépe zabezpečit organizaci pomocí automatizovaného zřizování uživatelských účtů, najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) .

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Zobrazit průběh služby zřizování Azure AD v reálném čase

**Textový** Změněná funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** Správa životního cyklu identit

Aktualizovali jsme prostředí pro zřizování Azure AD tak, aby obsahovalo nový indikátor průběhu, který vám ukáže, jak daleko jste v procesu zřizování uživatelů. Toto aktualizované prostředí obsahuje také informace o počtu uživatelů zřízených během aktuálního cyklu a také o tom, kolik uživatelů bylo zřízeno.

Další informace najdete v tématu o [kontrole stavu zřizování uživatelů](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user).

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>Branding společnosti se teď zobrazuje při odhlašování a chybových obrazovkách.

**Textový** Změněná funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů

Službu Azure AD jsme aktualizovali tak, aby se vaše firemní logo zobrazovalo na obrazovkách pro odhlášení a chyb a taky na přihlašovací stránce. Nemusíte nic dělat, abyste tuto funkci zapnuli, Azure AD jednoduše používá prostředky, které jste už nastavili v oblasti **značky společnosti** Azure Portal.

Další informace o nastavení firemního brandingu najdete v tématu [Přidání brandingu na stránky Azure Active Directory vaší organizace](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding).

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Server Azure Multi-Factor Authentication (MFA) už není k dispozici pro nová nasazení.

**Textový** Zastaralé  
**Kategorie služby:** MFA  
**Schopnost produktu:** Zabezpečení identity & ochrana

Od 1. července 2019 už Microsoft nenabídne MFA Server pro nová nasazení. Noví zákazníci, kteří chtějí vyžadovat službu Multi-Factor Authentication ve své organizaci, teď musí používat cloudové Multi-Factor Authentication Azure. Zákazníci, kteří si server MFA vyaktivovali před 1. července, se nezmění. Pořád budete moct stáhnout nejnovější verzi, získat budoucí aktualizace a vygenerovat přihlašovací údaje pro aktivaci.

Další informace najdete v tématu [Začínáme s Azure Multi-Factor Authentication Server](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy). Další informace o cloudových Multi-Factor Authenticationch Azure najdete v tématu [plánování cloudového nasazení azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---

## <a name="may-2019"></a>Květen 2019

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Změna služby: Budoucí podpora jenom pro protokoly TLS 1,2 ve službě proxy aplikací

**Textový** Plánování změn  
**Kategorie služby:** Proxy aplikace  
**Schopnost produktu:** Řízení přístupu

Abychom zákazníkům poskytli nejlepší šifrování v rámci své třídy, omezujeme přístup pouze na protokoly TLS 1,2 ve službě proxy aplikací. Tato změna se postupně zavádí pro zákazníky, kteří už používají jenom protokoly TLS 1,2, takže byste neměli vidět žádné změny.

Vyřazení TLS 1,0 a TLS 1,1 proběhne od 31. srpna 2019, ale budeme poskytovat další pokročilá oznámení, takže budete mít čas na tuto změnu připravovat. Aby se tato změna připravila, ujistěte se, že kombinace mezi klientem a serverem a prohlížečem, včetně všech klientů, které uživatelé používají pro přístup k aplikacím publikovaným pomocí proxy aplikací, se aktualizují tak, aby používaly protokol TLS 1,2 k údržbě připojení k aplikaci. Proxy služba. Další informace najdete v tématu [Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikace v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Pomocí sestavy využití a přehled můžete zobrazit přihlašovací údaje související s aplikacemi.

**Textový** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** Monitorování & vytváření sestav

Teď můžete použít sestavu využití a přehledů, která se nachází v oblasti **podnikové aplikace** Azure Portal, a získat tak pohled na vaše přihlašovací údaje zaměřené na aplikaci, včetně informací o:

- Nejpoužívanější aplikace používané pro vaši organizaci

- Aplikace s neúspěšnými přihlášeními

- Nejčastější chyby při přihlašování pro každou aplikaci

Další informace o této funkci najdete v tématu [Sestava využití a přehledů na portálu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report) .

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatizace zřizování uživatelů pro cloudové aplikace pomocí Azure AD

**Textový** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** Monitorování & vytváření sestav

Podle těchto nových kurzů použijte službu zřizování Azure AD k automatizaci vytváření, odstraňování a aktualizace uživatelských účtů pro následující cloudové aplikace:

- [Vyhovující](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

Můžete také postupovat podle tohoto nového [kurzu Dropboxu](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), který poskytuje informace o tom, jak zřídit objekty skupiny.

Další informace o tom, jak lépe zabezpečit organizaci prostřednictvím automatizovaného zřizování uživatelských účtů, najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Skóre Secure identity je teď dostupné ve službě Azure AD (Obecná dostupnost).

**Textový** Nová funkce  
**Kategorie služby:** Není k dispozici  
**Schopnost produktu:** Zabezpečení identity & ochrana

Teď můžete monitorovat a zdokonalovat stav zabezpečení identity pomocí funkce skóre Secure identity ve službě Azure AD. Funkce skóre Secure identity využívá jeden řídicí panel, který vám může pomáhat:

- Objektivně změřte stav zabezpečení identity, a to na základě skóre mezi 1 a 223.

- Plánování vylepšení zabezpečení identity

- Kontrola úspěšnosti vašich vylepšení zabezpečení

Další informace o funkci skóre zabezpečení identity najdete v tématu [co je bezpečné skóre identity v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Nyní je k dispozici nové prostředí Registrace aplikací (Obecná dostupnost).

**Textový** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Prostředí pro vývojáře

Nové prostředí [Registrace aplikací](https://aka.ms/appregistrations) je teď všeobecně dostupné. Toto nové prostředí zahrnuje všechny klíčové funkce, se kterými jste obeznámeni z Azure Portal a portálu pro registraci aplikací a vylepšuje je prostřednictvím:

- **Lepší správa aplikací.** Místo prohlížení aplikací napříč různými portály teď můžete zobrazit všechny aplikace na jednom místě.

- **Zjednodušená registrace aplikace** Z vylepšeného prostředí navigace pro výběr oprávnění přepracované je teď snazší registrovat a spravovat vaše aplikace.

- **Podrobnější informace.** Můžete najít další podrobnosti o vaší aplikaci, včetně příruček pro rychlý Start a dalších.

Další informace najdete v části [Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/) a [prostředí registrace aplikací je teď všeobecně dostupné.](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) oznámení blogu

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Nové funkce dostupné v rozhraní API pro rizikové uživatele pro identitu Protection

**Textový** Nová funkce  
**Kategorie služby:** Identity Protection  
**Schopnost produktu:** Zabezpečení identity & ochrana

S radostí oznamujeme, že teď můžete pomocí rozhraní API pro rizikové uživatele načítat historii rizik uživatelů, odvolat rizikové uživatele a potvrdit ohrožení uživatelů. Tato změna vám pomůže efektivněji aktualizovat stav rizika vašich uživatelů a pochopit jejich historii rizik.

Další informace najdete v [referenční dokumentaci k rozhraní API pro rizikové uživatele](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – květen 2019

**Textový** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** Integrace třetích stran

V květnu 2019 jsme do Galerie aplikací přidali tyto 21 nových aplikací s podporou federace:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [skutečné odkazy](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [Simple Sign](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [DisplayName](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [Marketo Sales](https://toutapp.com/login)propojování, [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [subsystémy](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [META4 Global HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), [ Pracoviště](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [kobalt](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), [webMethods API Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [Control](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [Foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Další informace o aplikacích najdete v tématu [integrace aplikací SaaS pomocí Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete v tématu [uvedení aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Vylepšené vytváření a Správa skupin na portálu Azure AD

**Textový** Nová funkce  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Spolupráce

Provedli jsme vylepšení prostředí souvisejících se skupinami na portálu Azure AD. Tato vylepšení umožňují správcům lépe spravovat seznamy skupin, seznamy členů a poskytovat další možnosti vytváření.

Mezi vylepšení patří:

- Základní filtrování podle typu členství a typu skupiny.

- Přidání nových sloupců, jako je zdrojová a e-mailová adresa.

- Možnost vícenásobného výběru skupin, členů a seznamů vlastníků pro snadné odstranění.

- Možnost výběru e-mailové adresy a přidání vlastníků během vytváření skupiny.

Další informace najdete v tématu [Vytvoření základní skupiny a přidání členů pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Konfigurace zásady pojmenování pro skupiny Office 365 na portálu Azure AD (Obecná dostupnost)

**Textový** Změněná funkce  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Spolupráce

Správci teď můžou nakonfigurovat zásady pojmenování pro skupiny Office 365 pomocí portálu Azure AD. Tato změna pomáhá vymáhat konzistentní zásady vytváření názvů pro skupiny Office 365 vytvořené nebo upravované uživateli ve vaší organizaci.

Zásady pojmenování pro skupiny Office 365 můžete nakonfigurovat dvěma různými způsoby:

- Zadejte předpony nebo přípony, které se automaticky přidají do názvu skupiny.

- Nahrajte přizpůsobenou sadu blokovaných slov pro vaši organizaci, která nejsou povolená v názvech skupin (například generální ředitel, výplata, HR).

Další informace najdete v tématu [vymáhání zásady pojmenování pro skupiny Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Pro protokoly aktivit Azure AD jsou teď dostupné koncové body rozhraní Microsoft Graph API (Obecná dostupnost).

**Textový** Změněná funkce  
**Kategorie služby:** Vytváření sestav  
**Schopnost produktu:** Monitorování & vytváření sestav

S radostí oznamujeme obecnou dostupnost Microsoft Graph Podpora koncových bodů rozhraní API pro protokoly aktivit služby Azure AD. V této verzi teď můžete používat protokol auditu Azure AD verze 1,0 jak v protokolu, tak i v protokolech rozhraní API pro přihlášení.

Další informace najdete v tématu [Přehled rozhraní API protokolu auditu služby Azure AD](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Správci teď můžou používat podmíněný přístup pro kombinovaný proces registrace (Public Preview).

**Textový** Nová funkce  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity & ochrana  

Správci teď můžou vytvořit zásady podmíněného přístupu pro použití v rámci kombinované registrační stránky. To zahrnuje použití zásad pro povolení registrace v těchto případech:

- Uživatelé jsou v důvěryhodné síti.

- Uživatelé jsou nízkým rizikem při přihlašování.

- Uživatelé jsou na spravovaném zařízení.

- Uživatelé souhlasí s podmínkami použití organizace (podmínky použití).

Další informace o podmíněném přístupu a resetování hesla najdete v [blogovém příspěvku o podmíněném přístupu pro kombinované MFA a registraci resetování hesel služby Azure AD](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348). Další informace o zásadách podmíněného přístupu pro kombinovaný proces registrace najdete v tématu [zásady podmíněného přístupu pro kombinovanou registraci](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration). Další informace o funkci Azure AD terms of use najdete v tématu [funkce Azure Active Directory podmínek použití](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

## <a name="april-2019"></a>Duben 2019

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>Nové zjišťování analýz Azure AD Threat je teď k dispozici jako součást Azure AD Identity Protection

**Textový** Nová funkce  
**Kategorie služby:** Azure AD Identity Protection  
**Schopnost produktu:** Zabezpečení identity & ochrana

Detekce analýzy hrozeb Azure AD je teď k dispozici jako součást aktualizované Azure AD Identity Protection funkce. Tato nová funkce pomáhá indikovat neobvyklou aktivitu uživatelů pro konkrétního uživatele nebo aktivitu, která je konzistentní se známými vzory útoku na základě interních a externích zdrojů analýzy hrozeb Microsoftu.

Další informace o aktualizované verzi Azure AD Identity Protection najdete na blogu o [čtyřech hlavních Azure AD Identity Protection vylepšeních, která jsou teď ve verzi Public Preview](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) , a na [Azure Active Directory Identity Protection (aktualizované)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) předmětu. Další informace o detekci analýzy hrozeb v Azure AD najdete v článku [Azure Active Directory Identity Protection detekce rizik](https://docs.microsoft.com/azure/active-directory/identity-protection/risk-events-reference#azure-ad-threat-intelligence) .

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Služba Azure AD nároking Management je teď dostupná (verze Public Preview).

**Textový** Nová funkce  
**Kategorie služby:** Zásady správného řízení identit  
**Schopnost produktu:** Zásady správného řízení identit

Správa nároků služby Azure AD, která je teď ve verzi Public Preview, pomáhá zákazníkům delegovat správu balíčků přístupu, který definuje, jak můžou zaměstnanci a obchodní partneři požádat o přístup, kteří musí schvalovat a jak dlouho mají přístup. Přístup k balíčkům může spravovat členství v Azure AD a skupinách Office 365, přiřazování rolí v podnikových aplikacích a přiřazování rolí pro weby SharePointu Online. Přečtěte si další informace o správě nároků v tématu [Přehled správy nároků služby Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Další informace o šířce funkcí Azure AD Identity Governance, včetně Privileged Identity Management, kontrolách přístupu a podmínek použití, najdete v tématu [co je Azure AD identity governance?](../governance/identity-governance-overview.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Konfigurace zásady pojmenování pro skupiny Office 365 na portálu Azure AD (Public Preview)

**Textový** Nová funkce  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Spolupráce

Správci teď můžou nakonfigurovat zásady pojmenování pro skupiny Office 365 pomocí portálu Azure AD. Tato změna pomáhá vymáhat konzistentní zásady vytváření názvů pro skupiny Office 365 vytvořené nebo upravované uživateli ve vaší organizaci.

Zásady pojmenování pro skupiny Office 365 můžete nakonfigurovat dvěma různými způsoby:

- Zadejte předpony nebo přípony, které se automaticky přidají do názvu skupiny.

- Nahrajte přizpůsobenou sadu blokovaných slov pro vaši organizaci, která nejsou povolená v názvech skupin (například generální ředitel, výplata, HR).

Další informace najdete v tématu [vymáhání zásady pojmenování pro skupiny Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Protokoly aktivit Azure AD jsou teď dostupné v Azure Monitor (Obecná dostupnost).

**Textový** Nová funkce  
**Kategorie služby:** Vytváření sestav  
**Schopnost produktu:** Monitorování & vytváření sestav

Abychom vám pomohli vyřešit svůj názor na vizualizace pomocí protokolů aktivit Azure AD, zavádíme nové funkce Insights v Log Analytics. Tato funkce vám pomůže získat přehled o prostředcích Azure AD pomocí našich interaktivních šablon nazývaných sešity. Tyto předem připravené sešity můžou poskytovat podrobné informace pro aplikace nebo uživatele a zahrnovat:

- **Přihlášení.** Obsahuje podrobné informace o aplikacích a uživatelích, včetně umístění pro přihlášení, klientského operačního systému nebo klienta a verze prohlížeče a počtu úspěšných nebo neúspěšných přihlášení.

- **Starší verze ověřování a podmíněný přístup.** Obsahuje podrobné informace o aplikacích a uživatelích využívajících starší ověřování, včetně Multi-Factor Authenticationho použití aktivovaných zásadami podmíněného přístupu, aplikacích, které používají zásady podmíněného přístupu a tak dále.

- **Analýza neúspěšných přihlášení.** Pomáhá určit, jestli dochází k chybám přihlášení kvůli akci uživatele, problémům se zásadami nebo vaší infrastruktuře.

- **Vlastní sestavy.** Můžete vytvořit nové nebo upravit existující sešity, které vám pomůžou přizpůsobit funkci Insights pro vaši organizaci.

Další informace najdete v tématu [použití Azure Monitorch sešitů pro sestavy Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – duben 2019

**Textový** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** Integrace třetích stran

V dubnu 2019 jsme do Galerie aplikací přidali tyto 21 nových aplikací s podporou federace:

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks Single Signing](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), MileIQ [, PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [EduBrite](https://mileiq.onelink.me/991934284/7e980085), [](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial) [RStudio LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial)a [Connect ](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [Mitel Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [Alibaba Cloud (jednotné přihlašování založené na rolích)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Správa Certent akcií](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Sectigo Certificate Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [Monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [Indiggo](https://indiggolead.com/)

Další informace o aplikacích najdete v tématu [integrace aplikací SaaS pomocí Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete v tématu [uvedení aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Nová možnost četnosti kontrol přístupu a výběr více rolí

**Textový** Nová funkce  
**Kategorie služby:** Kontroly přístupu  
**Schopnost produktu:** Zásady správného řízení identit

Nové aktualizace v recenzích přístupu Azure AD umožňují:

- Kromě dříve existujících možností týdně, měsíčně, čtvrtletně a ročně můžete změnit četnost kontrol přístupu na **částečně ročně**.

- Při vytváření jedné kontroly přístupu vyberte víc rolí Azure AD a prostředků Azure. V této situaci se všechny role nastavují se stejným nastavením a všichni revidující se budou oznamovat současně.

Další informace o tom, jak vytvořit kontrolu přístupu, najdete v tématu [Vytvoření kontroly přístupu skupin nebo aplikací v kontrolách přístupu k Azure AD](https://docs.microsoft.com/azure/active-directory/governance/create-access-review).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect se převádějí e-mailové systémy upozornění, odesílají zákazníkům nové e-mailové informace o odesílateli.

**Textový** Změněná funkce  
**Kategorie služby:** AD Sync  
**Schopnost produktu:** Platforma

Azure AD Connect právě přechází na naše systémy e-mailových výstrah, které si můžou někteří zákazníci zobrazit jako nového e-mailového odesílatele. Pokud to chcete vyřešit, musíte přidat `azure-noreply@microsoft.com` do seznamu povolených organizací nebo nebudete moci nadále přijímat důležité výstrahy ze sady Office 365, Azure nebo služby synchronizace.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Změny přípony hlavního názvu uživatele (UPN) jsou teď mezi federované domény v Azure AD Connect úspěšné.

**Textový** Pevné  
**Kategorie služby:** AD Sync  
**Schopnost produktu:** Platforma

Teď můžete úspěšně změnit příponu UPN uživatele z jedné federované domény na jinou federované domény v Azure AD Connect. Tato oprava znamená, že během synchronizačního cyklu nebo při obdržení e-mailu s oznámením, "nelze aktualizovat tento objekt v Azure Active Directory, nemusíte mít nadále zkušenosti s chybovou zprávou FederatedDomainChangeError, protože atribut [ FederatedUser. UserPrincipalName], není platný. Aktualizujte hodnotu v místních adresářových službách.

Další informace najdete v tématu [řešení chyb během synchronizace](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Zvýšené zabezpečení pomocí zásad podmíněného přístupu na základě ochrany aplikací ve službě Azure AD (Public Preview)

**Textový** Nová funkce  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity & ochrana

Podmíněný přístup na základě ochrany aplikací je teď k dispozici pomocí zásad **vyžadovat ochranu aplikací** . Tato nová zásada pomáhá zvýšit zabezpečení vaší organizace tím, že pomáhá zabránit:

- Uživatelé získají přístup k aplikacím bez Microsoft Intune licence.

- Uživatelé nemůžou získat Microsoft Intune zásady ochrany aplikací.

- Uživatelé získají přístup k aplikacím bez nakonfigurovaného Microsoft Intune zásady ochrany aplikací.

Další informace najdete v tématu [Postup při vyžadování zásad ochrany aplikací pro cloudový přístup k aplikaci pomocí podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Nová podpora jednotného přihlašování a podmíněného přístupu Azure AD v Microsoft Edge (Public Preview)

**Textový** Nová funkce  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity & ochrana

Vylepšili jsme podporu Azure AD pro Microsoft Edge, včetně poskytování nové podpory pro jednotné přihlašování a podmíněný přístup Azure AD. Pokud jste už dřív používali Microsoft Intune Managed Browser, teď můžete místo toho použít Microsoft Edge.

Další informace o nastavení a správě zařízení a aplikací pomocí podmíněného přístupu najdete v tématu [vyžadování spravovaných zařízení pro cloudovou aplikaci přístup s podmíněným přístupem](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) a [vyžadování schválených klientských aplikací pro přístup k cloudovým aplikacím s podmíněným přístupem. ](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Další informace o správě přístupu pomocí Microsoft Edge se zásadami Microsoft Intune najdete v tématu [Správa přístupu k Internetu pomocí Microsoft Intune prohlížeče chráněného zásadami](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>Březen 2019

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Architektura prostředí identit a podpora vlastních zásad v Azure Active Directory B2C je teď k dispozici (GA)

**Textový** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C

V Azure AD B2C teď můžete vytvářet vlastní zásady, včetně následujících úloh, které jsou podporované v škále a v rámci naší smlouvy SLA pro Azure:

- Vytvoření a nahrání cest uživatelů s vlastním ověřováním pomocí vlastních zásad.

- Popište cesty uživatelů krok za krokem jako výměny mezi poskytovateli deklarací identity.

- Definujte podmíněné větvení v cestě uživatele.

- Transformujte a mapujte deklarace identity pro použití v rozhodnutích a komunikacích v reálném čase.

- Ve svých uživatelských cestách pro ověřování použijte REST API služby s povoleným použitím. Například s poskytovateli e-mailu, CRMs a proprietárními autorizačními systémy.

- Federovat se zprostředkovateli identity, kteří jsou kompatibilní s protokolem OpenIDConnect. Například s více klienty Azure AD, poskytovateli účtů sociálních sítí nebo poskytovateli dvojúrovňového ověřování.

Další informace o vytváření vlastních zásad najdete v tématu [poznámky pro vývojáře pro vlastní zásady v Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom) a čtení [blogového příspěvku Alex Simon, včetně případných studií](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD – březen 2019

**Textový** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** Integrace třetích stran

V březnu 2019 jsme do Galerie aplikací přidali tyto 14 nových aplikací s podporou federace:

[ISEC7](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [auditování na základě vysvětlení systém](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [Lean](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [PowerSchool výkonové otázky](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode ](https://cinode.com/),, [Intranet](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit horizonts](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [ú](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Další informace o aplikacích najdete v tématu [integrace aplikací SaaS pomocí Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete v tématu [uvedení aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Nové konektory zřizování Zscaler a Atlassian v galerii Azure AD – březen 2019

**Textový** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** Integrace třetích stran

Automatizace vytváření, aktualizace a odstraňování uživatelských účtů pro následující aplikace:

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler Beta](https://aka.ms/ZscalerBetaProvisioning), [Zscaler One](https://aka.ms/ZscalerOneProvisioning), [Zscaler 2](https://aka.ms/ZscalerTwoProvisioning), [Zscaler tři](https://aka.ms/ZscalerThreeProvisioning), [Zscaler ZSCloud](https://aka.ms/ZscalerZSCloudProvisioning), Atlassian [Cloud](https://aka.ms/atlassianCloudProvisioning)

Další informace o tom, jak lépe zabezpečit organizaci prostřednictvím automatizovaného zřizování uživatelských účtů, najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](https://aka.ms/ProvisioningDocumentation).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Obnovení a Správa odstraněných skupin Office 365 na portálu Azure AD

**Textový** Nová funkce  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Spolupráce

Odstraněné skupiny Office 365 teď můžete zobrazit a spravovat z portálu Azure AD. Tato změna vám pomůže zjistit, které skupiny jsou k dispozici pro obnovení, spolu s tím, že vám umožní trvale odstranit všechny skupiny, které nepotřebuje vaše organizace.

Další informace najdete v tématu [obnovení skupin s ukončenou platností nebo odstraněných](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Pro místní aplikace zabezpečené službou SAML v Azure AD prostřednictvím proxy aplikací (Public Preview) je teď k dispozici jednotné přihlašování.

**Textový** Nová funkce  
**Kategorie služby:** Proxy aplikace  
**Schopnost produktu:** Řízení přístupu

Teď můžete pro místní aplikace ověřené pomocí SAML a vzdálený přístup k těmto aplikacím prostřednictvím proxy aplikací poskytnout jednotné přihlašování (SSO). Další informace o tom, jak nastavit jednotné přihlašování SAML v místních aplikacích, najdete v tématu [jednotné přihlašování SAML pro místní aplikace s proxy aplikací (Preview)](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Klientské aplikace v cyklech žádostí budou přerušeny, aby se zlepšila spolehlivost a činnost koncového uživatele.

**Textový** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů

Klientské aplikace mohou v krátké době nesprávně vydávat stovky stejných žádostí o přihlášení. Tyto požadavky, ať už jsou úspěšné nebo ne, přispívají ke zhoršení uživatelského prostředí a zvýšenému zatížení pro IDP, zvyšují latenci pro všechny uživatele a snižují dostupnost IDP.

Tato aktualizace pošle `invalid_grant` chybu: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` klientským aplikacím, které vydávají duplicitní žádosti několikrát v krátké době, mimo rozsah běžné operace. Klientské aplikace, které narazí na tento problém, by měly zobrazit interaktivní výzvu a vyžadovat, aby se uživatel znovu přihlásil. Další informace o této změně a o tom, jak opravit aplikaci, pokud dojde k této chybě, najdete v tématu [co je nového pro ověřování?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Nové protokoly auditu – uživatelské prostředí teď k dispozici

**Textový** Změněná funkce  
**Kategorie služby:** Vytváření sestav  
**Schopnost produktu:** Monitorování & vytváření sestav

Vytvořili jsme novou stránku **protokolů auditu** Azure AD, které vám pomůžou zlepšit čitelnost a jak hledat vaše informace. Pokud chcete zobrazit stránku nové **protokoly auditu** , vyberte v části **aktivita** v Azure AD možnost **protokoly auditu** .

![Stránka nové protokoly auditu s ukázkovými informacemi](media/whats-new/audit-logs-page.png)

Další informace o nové stránce **protokoly auditu** najdete [v tématu sestavy aktivit auditu na portálu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Nová upozornění a pokyny, které vám pomůžou zabránit nechtěnému uzamknutí správce z chybně konfigurovaných zásad podmíněného přístupu

**Textový** Změněná funkce  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity & ochrana

Abychom správcům zabránili v náhodném uzamykání ze svých klientů prostřednictvím nesprávně nakonfigurovaných zásad podmíněného přístupu, vytvořili jsme nová upozornění a aktualizované pokyny v Azure Portal. Další informace o nových pokynech najdete v tématu [co jsou závislosti služby v Azure Active Directory podmíněný přístup](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Vylepšené používání funkcí pro koncové uživatele na mobilních zařízeních

**Textový** Změněná funkce  
**Kategorie služby:** Podmínky použití  
**Schopnost produktu:** Zásady správného řízení

Aktualizovali jsme naše stávající prostředí s využitím, abychom vylepšili, jak si vyzkoušíte používání a souhlasíte s podmínkami použití v mobilním zařízení. Nyní se můžete přiblížit nebo oddálit, přejít zpátky, stáhnout informace a vybrat hypertextové odkazy. Další informace o aktualizovaných podmínek použití najdete v tématu [funkce Azure Active Directory podmínek použití](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Nové prostředí pro stažení protokolů aktivit služby Azure AD k dispozici

**Textový** Změněná funkce  
**Kategorie služby:** Vytváření sestav  
**Schopnost produktu:** Monitorování & vytváření sestav

Nyní můžete stahovat velké objemy protokolů aktivit přímo z Azure Portal. Tato aktualizace vám umožní:

- Stáhněte si až 250 000 řádků.

- Po dokončení stahování se zobrazí oznámení.

- Přizpůsobte název souboru.

- Určete výstupní formát buď JSON, nebo CSV.

Další informace o této funkci najdete v tématu [rychlý Start: Stažení sestavy auditu pomocí Azure Portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Změna způsobující chybu: Aktualizace vyhodnocení podmínky pomocí protokolu Exchange ActiveSync (EAS)

**Textový** Plánování změn  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Řízení přístupu

Právě probíhá aktualizace způsobu, jakým protokol Exchange ActiveSync (EAS) vyhodnocuje následující podmínky:

- Umístění uživatele v závislosti na zemi, oblasti nebo IP adrese

- Riziko přihlášení

- Platforma zařízení

Pokud jste tyto podmínky dříve používali v rámci zásad podmíněného přístupu, mějte na paměti, že chování podmínky se může změnit. Pokud jste například v zásadě používali podmínku umístění uživatele v zásadě, můžete zjistit, že se zásady nyní přeskočí v závislosti na umístění uživatele.

---
