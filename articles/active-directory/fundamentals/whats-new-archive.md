---
title: Archivujte, co je nového v Azure Active Directory? | Dokumenty Microsoft
description: Co je nového poznámky k verzi v přehledu, že část této sady obsahu obsahuje 6 měsíců aktivity. Po 6 měsíců položky se odeberou z hlavní článek a vložit do tohoto článku archivu.
services: active-directory
author: msmimart
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64229e4433ee029787ebd88eba264833827e706c
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136461"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Archivujte, co je nového v Azure Active Directory?

Část [co je nového ve službě Azure Active Directory?](whats-new.md) v článku poznámky k verzi obsahuje aktualizace za posledních šest měsíců, i když tento článek obsahuje všechny starší informace.

Co je nového v Azure Active Directory? Poznámky k verzi obsahují informace o:

- Nejnovější vydané verzi
- Známé problémy
- Opravy chyb
- Zastaralé funkce
- Plány pro změny

---

## <a name="august-2019"></a>Srpen 2019

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>Rozšířené hledání, filtrování a řazení pro skupiny je dostupné na portálu Azure AD (Public Preview).

**Zadejte:** Nová funkce  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Prostřednictvím

S radostí oznamujeme dostupnost verze Public Preview pro rozšířené prostředí související se skupinami na portálu Azure AD. Tato vylepšení vám pomůžou lépe spravovat skupiny a seznamy členů tím, že poskytují:

- Rozšířené možnosti vyhledávání, jako je hledání v podřetězcůch v seznamech skupin.
- Rozšířené možnosti filtrování a řazení v seznamech členů a vlastníků.
- Nové možnosti vyhledávání pro seznamy členů a vlastníků.
- Přesnější počty skupin pro velké skupiny.

Další informace najdete v tématu [Správa skupin v Azure Portal](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Pro správu registrace aplikací jsou k dispozici nové vlastní role (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** RBAC  
**Schopnost produktu:** Access Control

Vlastní role (k dispozici v rámci předplatného Azure AD P1 nebo P2) vám teď můžou pomáhat s jemnou přístupem tím, že vám umožní vytvořit definice rolí s konkrétními oprávněními a pak tyto role přiřadit konkrétním prostředkům. V současné době vytvoříte vlastní role pomocí oprávnění pro správu registrací aplikací a potom přiřadíte roli ke konkrétní aplikaci. Další informace o vlastních rolích najdete v tématu [vlastní role správců v Azure Active Directory (Preview)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview).

Pokud potřebujete další oprávnění nebo prostředky, které se v tuto chvíli nezobrazuje, můžete poslat svůj názor na náš [web Azure Feedback](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) a my přidáme vaši žádost do naší mapy aktualizace.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>Nové protokoly zřizování vám můžou pomoct monitorovat a řešit potíže s nasazením zřizování aplikací (Public Preview).

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** Správa životního cyklu identit

K dispozici jsou nové protokoly zřizování, které vám pomůžou monitorovat a řešit potíže s nasazením zřizování uživatelů a skupin. Tyto nové soubory protokolu obsahují informace o:

- Které skupiny byly úspěšně vytvořeny v [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)
- Jaké role byly naimportovány z [Amazon Web Services (AWS)](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial#configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws)
- Kteří zaměstnanci nebyli naimportovali z [Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)

Další informace najdete v tématu [zřizování sestav na portálu Azure Active Directory (Preview)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs).

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Nové sestavy zabezpečení pro všechny správce Azure AD (všeobecně dostupné)

**Zadejte:** Nová funkce  
**Kategorie služby:** Ochrana identity  
**Schopnost produktu:** Zabezpečení identity & ochrana

Ve výchozím nastavení budou mít všichni správci služby Azure AD brzy přístup k moderním sestavám zabezpečení v rámci Azure AD. Až do konce září budete moct použít banner v horní části moderních sestav zabezpečení pro návrat k původním sestavám.

Moderní sestavy zabezpečení poskytují další možnosti ze starších verzí, včetně těchto:

- Rozšířené filtrování a řazení
- Hromadné akce, jako je například chybějící riziko uživatele
- Potvrzení napadených nebo bezpečných entit
- Stav rizika, který pokrývá: ohrožené, neúspěšné, opravené a potvrzené ohrožení
- Nové detekce související s riziky (k dispozici pro Azure AD Premium předplatitele)

Další informace najdete v tématech [rizikové uživatele](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users), [rizikové přihlašovací](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins)údaje a [detekce rizik](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risk-detections).

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>Spravovaná identita přiřazená uživatelem je k dispozici pro Virtual Machines a Virtual Machine Scale Sets (Obecná dostupnost).

**Zadejte:** Nová funkce  
**Kategorie služby:** Spravované identity pro prostředky Azure  
**Schopnost produktu:** Vývojářské prostředí

Spravované identity přiřazené uživatelem jsou teď všeobecně dostupné pro Virtual Machines a Virtual Machine Scale Sets. V rámci této služby může Azure vytvořit identitu v tenantovi Azure AD, která je důvěryhodná pro používané předplatné, a dá se přiřadit k jedné nebo více instancím služby Azure. Další informace o spravovaných identitách přiřazených uživateli najdete v tématu [co jsou spravované identity pro prostředky Azure](https://aka.ms/azuremanagedidentity).

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>Uživatelé můžou resetovat svá hesla pomocí mobilní aplikace nebo hardwarového tokenu (všeobecně dostupná).

**Zadejte:** Změněná funkce  
**Kategorie služby:** Samoobslužné resetování hesla  
**Schopnost produktu:** Ověřování uživatelů

Uživatelé, kteří si zaregistrovali mobilní aplikaci ve vaší organizaci, teď můžou resetovat oznámení z aplikace Microsoft Authenticator nebo zadáním kódu z mobilní aplikace nebo hardwarového tokenu.

Další informace najdete v tématu [jak to funguje: Samoobslužné resetování hesla služby Azure AD](https://aka.ms/authappsspr). Další informace o uživatelském prostředí najdete v tématu [resetování vlastního pracovního nebo školního hesla](https://docs.microsoft.com/azure/active-directory/user-help/user-help-password-reset-overview).

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET ignoruje sdílenou mezipaměť MSAL.NET pro scénáře v zastoupení.

**Zadejte:** Určí  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů

Počínaje službou Azure AD Authentication Library (ADAL.NET) verze 5.0.0-Preview musí vývojáři aplikací [serializovat jednu mezipaměť na účet pro webové aplikace a webová rozhraní API](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api). V opačném případě můžou některé scénáře, které používají [tok](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow)za běhu, spolu s některými konkrétními případy použití `UserAssertion`, způsobit zvýšení oprávnění. Aby se zabránilo této chybě, ADAL.NET nyní ignoruje sdílenou mezipaměť Microsoft Authentication Library for dotnet (MSAL.NET) pro scénáře v zastoupení.

Další informace o tomto problému naleznete v tématu [Azure Active Directory zvýšení úrovně zabezpečení oprávnění v knihovně ověřování](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Nové federované aplikace dostupné v Aplikace Azure AD galerii – srpen 2019

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran

V srpnu 2019 jsme do Galerie aplikací přidali tyto 26 nových aplikací s podporou federace:

[Civic Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/civic-platform-tutorial), [Amazon Business](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-business-tutorial), [ProNovos](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-ops-manager-tutorial)Operations Manager [, Cognidox](https://docs.microsoft.com/azure/active-directory/saas-apps/cognidox-tutorial), [Viareport Inativ Portal (Evropa)](https://docs.microsoft.com/azure/active-directory/saas-apps/viareports-inativ-portal-europe-tutorial), [Azure Databricks](https://azure.microsoft.com/services/databricks), [Robin](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial), [Academy docházka](https://docs.microsoft.com/azure/active-directory/saas-apps/academy-attendance-tutorial), [prioritní matrice](https://sync.appfluence.com/pmwebng/), [Cousto MySpace](https://cousto.platformers.be/account/login), [Uploadcare](https://uploadcare.com/accounts/signup/), [Carbonite Backup Endpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/carbonite-endpoint-backup-tutorial), [CPQSync](https://docs.microsoft.com/azure/active-directory/saas-apps/cpqsync-by-cincom-tutorial), Cincom, [Chargebee](https://docs.microsoft.com/azure/active-directory/saas-apps/chargebee-tutorial), [doručovat. Media&trade; Portal](https://portal.deliver.media), [prvotní vzdělávání](https://docs.microsoft.com/azure/active-directory/saas-apps/frontline-education-tutorial), [F5](https://www.f5.com/products/security/access-policy-manager), [ stashcat AD Connect](https://www.stashcat.com), [Blink](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial), [Vocoli](https://docs.microsoft.com/azure/active-directory/saas-apps/vocoli-tutorial), [ProNovos Analytics](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-analytics-tutorial), [Sigstr](https://docs.microsoft.com/azure/active-directory/saas-apps/sigstr-tutorial), [Darwinbox](https://docs.microsoft.com/azure/active-directory/saas-apps/darwinbox-tutorial), [Sledujte barvy](https://docs.microsoft.com/azure/active-directory/saas-apps/watch-by-colors-tutorial) [, rozpracovat](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial), [EAB přejít na strategický péči](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-strategic-care-tutorial)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>K dispozici jsou nové verze modulů PowerShellu pro AzureAD a prostředí AzureADPreview PowerShell.

**Zadejte:** Změněná funkce  
**Kategorie služby:** Jiná  
**Schopnost produktu:** Službě

K dispozici jsou nové aktualizace modulů PowerShellu pro AzureAD a AzureAD ve verzi Preview:

- Do parametru `Get-AzureADDirectoryRole` v modulu AzureAD byl přidán nový parametr `-Filter`. Tento parametr vám pomůže filtrovat role adresáře vrácené rutinou.
- Do modulu AzureADPreview byly přidány nové rutiny, které vám pomůžou definovat a přiřazovat vlastní role v Azure AD, včetně těchto:

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>Vylepšení uživatelského rozhraní tvůrce pravidla dynamické skupiny v Azure Portal

**Zadejte:** Změněná funkce  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Prostřednictvím

Provedli jsme některá vylepšení uživatelského rozhraní pro tvůrce pravidel dynamické skupiny, který je k dispozici v Azure Portal, abyste mohli snadněji nastavit nové pravidlo nebo změnit stávající pravidla. Toto vylepšení návrhu umožňuje vytvořit pravidla s až pěti výrazy, nikoli jenom s jedním. Aktualizovali jsme také seznam vlastností zařízení, aby se odebraly zastaralé vlastnosti zařízení.

Další informace najdete v tématu [Správa pravidel dynamického členství](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership).

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>Nové oprávnění aplikace Microsoft Graph k dispozici pro použití s recenzemi přístupu

**Zadejte:** Změněná funkce  
**Kategorie služby:** Kontroly přístupu  
**Schopnost produktu:** Zásady správného řízení identity

Zavedli jsme nové oprávnění aplikace Microsoft Graph `AccessReview.ReadWrite.Membership`, které umožňuje aplikacím automaticky vytvářet a načítat kontroly přístupu pro členství ve skupině a přiřazení aplikací. Toto oprávnění můžou použít naplánované úlohy nebo v rámci automatizace, aniž by bylo nutné přihlášený kontext uživatele.

Další informace najdete v [příkladu vytváření kontrol přístupu Azure AD pomocí Microsoft Graph oprávnění aplikace s blogem PowerShellu](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241).

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Protokoly aktivit Azure AD jsou teď dostupné pro státní cloudové instance v Azure Monitor

**Zadejte:** Změněná funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav

S radostí oznamujeme, že protokoly aktivit Azure AD jsou teď dostupné pro státní cloudové instance v Azure Monitor. Protokoly Azure AD teď můžete odesílat do svého účtu úložiště nebo do centra událostí, abyste je mohli integrovat s SIEM nástroji, jako je [SumoLogic](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic), [Splunk](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-splunk)a [ArcSight](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-arcsight). 

Další informace o nastavení Azure Monitor najdete [v tématu protokoly aktivit Azure AD v Azure monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor#cost-considerations).

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Aktualizace uživatelů na nové a rozšířené prostředí informací o zabezpečení

**Zadejte:** Změněná funkce  
**Kategorie služby:**  Ověřování (přihlášení)   
**Schopnost produktu:** Ověřování uživatelů

25. září 2019 se vypíná staré, nerozšířené informace o zabezpečení pro registraci a správu bezpečnostních údajů uživatelů a zapínání nové [Rozšířené verze](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271). To znamená, že uživatelé již nebudou moci používat staré prostředí.

Další informace o prostředí rozšířené informace o zabezpečení najdete v naší [dokumentaci pro správu](https://aka.ms/securityinfodocs) a v [dokumentaci k uživateli](https://aka.ms/securityinfoguide).

#### <a name="to-turn-on-this-new-experience-you-must"></a>Pokud chcete toto nové prostředí zapnout, musíte:

1. Přihlaste se k Azure Portal jako globální správce nebo Správce uživatelů.

2. Přejděte na **Azure Active Directory > nastavení uživatele > spravovat nastavení pro přístupové panely funkce verze Preview**.

3. V části **Uživatelé můžou používat funkce verze Preview pro registraci a správu informací o zabezpečení** , vyberte možnost **vybrané**a pak zvolte skupinu uživatelů nebo zvolte možnost **vše** , pokud chcete tuto funkci zapnout pro všechny uživatele v tenantovi.

4. V části * * Uživatelé můžou používat funkce verze Preview pro registraci a správu zabezpečení * * * * * *, vyberte **žádné**.

5. Uložte nastavení.

    Po uložení nastavení už nebudete mít přístup k původnímu prostředí bezpečnostní informace.

>[!Important]
>Pokud tento postup nedokončíte před 25. září 2019, bude se váš tenant Azure Active Directory automaticky povolit pro vylepšené prostředí. Pokud máte nějaké otázky, kontaktujte nás prosím na registrationpreview@microsoft.com.

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>Požadavky na ověřování pomocí přihlašovacích údajů po přihlášení budou striktně ověřeny.

**Zadejte:** Změněná funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Zvyšování

Od 2. září 2019 budou žádosti o ověření pomocí metody POST u standardů protokolu HTTP striktně ověřeny. Konkrétně mezery a dvojité uvozovky (") již nebudou odebrány z hodnot formuláře žádosti. Tyto změny se neočekávají pro přerušení stávajících klientů a pomůžou zajistit, aby se požadavky odeslané do služby Azure AD spolehlivě zpracovávají pokaždé.

Další informace najdete v tématu o [nejnovějších změnách Azure AD](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored).

---

## <a name="july-2019"></a>Červenec 2019

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Plánování změn: aktualizace služby proxy aplikací pro podporu pouze TLS 1,2

**Zadejte:** Plánování změn  
**Kategorie služby:** Proxy aplikace  
**Schopnost produktu:** Access Control

Abychom vám pomohli získat naše nejsilnější šifrování, zahajte omezení přístupu služby proxy aplikací jenom na protokoly TLS 1,2. Toto omezení se zpočátku zavede na zákazníky, kteří už používají protokoly TLS 1,2, takže se jeho dopad neprojeví. Dokončení vyřazení protokolů TLS 1,0 a TLS 1,1 bude dokončeno 31. srpna 2019. Zákazníci, kteří pořád používají TLS 1,0 a TLS 1,1, budou dostávat Pokročilá oznámení o přípravě na tuto změnu.

Chcete-li zachovat připojení ke službě proxy aplikací v rámci této změny, doporučujeme, abyste se ujistili, že jsou kombinace klientských serverů a prohlížeče a serveru aktualizovány tak, aby používaly protokol TLS 1,2. Doporučujeme také, abyste měli všechny systémy klientů používané vašimi zaměstnanci k přístupu k aplikacím, které jsou publikované prostřednictvím služby proxy aplikací.

Další informace najdete v tématu [Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikace v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application).

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Plán změny: aktualizace návrhu přicházejí do Galerie aplikací.

**Zadejte:** Plánování změn  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ

Nové změny uživatelského rozhraní přicházejí do návrhu **Přidat z oblasti Galerie** v okně **Přidat aplikaci** . Tyto změny vám pomůžou snadněji najít vaše aplikace, které podporují Automatické zřizování, OpenID Connect, Security Assertion Markup Language (SAML) a jednotné přihlašování (SSO) k heslu.

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Plánování změn: odebrání IP adresy serveru MFA z IP adresy Office 365

**Zadejte:** Plánování změn  
**Kategorie služby:** VÍCEFAKTOROVÉHO  
**Schopnost produktu:** Zabezpečení identity & ochrana

Odebíráme IP adresu MFA serveru z [webové služby IP adresa a adresa URL sady Office 365](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service). Pokud při aktualizaci nastavení brány firewall aktuálně spoléháte na tyto stránky, musíte se ujistit, že máte také seznam IP adres, které jsou popsané v části **požadavky na bránu firewall pro azure Multi-Factor Authentication Server** v článku [začínáme s Azure Multi-Factor Authentication Server](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements) .

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Tokeny jenom pro aplikace teď vyžadují, aby klientská aplikace existovala v tenantovi prostředků.

**Zadejte:** Určí  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů

26. července 2019 jsme změnili, jak poskytujeme tokeny jenom pro aplikace prostřednictvím [udělení přihlašovacích údajů klienta](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow). Dříve mohli aplikace získat tokeny pro volání jiných aplikací bez ohledu na to, jestli klientská aplikace byla v tenantovi. Toto chování jsme aktualizovali tak, aby se prostředky jednoho tenanta, někdy označované jako webové rozhraní API, mohly volat jenom pro klientské aplikace, které existují v tenantovi prostředků.

Pokud se vaše aplikace nenachází v tenantovi prostředků, zobrazí se chybová zpráva s oznámením, že `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` tento problém vyřešit, musíte vytvořit instanční objekt klientské aplikace v tenantovi pomocí [koncového bodu souhlasu správce](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint) nebo [prostřednictvím PowerShellu](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell), který zaručuje, že má tenant oprávnění aplikace pro provoz v rámci tenanta.

Další informace najdete v tématu [co je nového pro ověřování?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant).

> [!NOTE]
> Stávající souhlas mezi klientem a rozhraním API se dál nepožaduje. Aplikace by měly stále provádět vlastní kontroly autorizace.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Nové přihlášení ke službě Azure AD pomocí klíčů zabezpečení FIDO2 pro nové heslo

**Zadejte:** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů

Zákazníci Azure AD teď můžou nastavit zásady pro správu FIDO2 bezpečnostních klíčů pro uživatele a skupiny organizace. Koncoví uživatelé také mohou své bezpečnostní klíče zaregistrovat sami, pomocí klíčů se přihlásí ke svým účtům Microsoft na webech, zatímco na zařízeních s podporou FIDO, a také přihlašování ke svým zařízením s Windows 10 připojeným k Azure AD.

Další informace najdete v tématu [Povolení přihlášení bez hesla pro Azure AD (Preview)](/azure/active-directory/authentication/concept-authentication-passwordless) pro informace týkající se správců a [Nastavení bezpečnostních údajů pro použití bezpečnostního klíče (Preview)](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) pro informace týkající se koncových uživatelů.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Nové federované aplikace dostupné v Aplikace Azure AD galerii – červenec 2019

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran

V červenci 2019 jsme do Galerie aplikací přidali tyto 18 nových aplikací s podporou federace:

[Ungerboeck software](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial), [jasný vzor Omnichannel kontakt Center](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial), [chytřejší Nelly](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial), [AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial), [LOOOP](https://www.looop.co/schedule-a-demo/), [Productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial), [přístup k aplikaci MS Azure SSO pro Ethidex dodržování předpisů Office&trade;](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso), [Hype](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial), [abstract](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial), [stoupání](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial), [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [Wandera](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial), [TwineSocial](https://twinesocial.com/), [Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial), [HyperAnna](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial), [PharmID WasteWitness](https://www.pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), JFrog [Artifactory](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizace zřizování uživatelských účtů pro tyto nově podporované aplikace SaaS

**Zadejte:** Nová funkce  
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

**Zadejte:** Nová funkce  
**Kategorie služby:** Azure AD Domain Services  
**Schopnost produktu:** Azure AD Domain Services

Pokud už vás unavuje správu dlouhých seznamů IP adres a rozsahů, můžete použít novou značku síťové služby **AzureActiveDirectoryDomainServices** ve skupině zabezpečení sítě Azure, která vám pomůžou zabezpečit příchozí provoz do podsítě vaší Azure AD Domain Services virtuální sítě.

Další informace o této nové značce služby najdete v tématu [skupiny zabezpečení sítě pro Azure AD Domain Services](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nové audity zabezpečení pro Azure AD Domain Services (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Azure AD Domain Services  
**Schopnost produktu:** Azure AD Domain Services

S radostí oznamujeme vydání auditu zabezpečení Azure AD Domain Service do veřejné verze Preview. Auditování zabezpečení pomáhá poskytnout důležitý pohled na vaše služby ověřování – streamování událostí auditu zabezpečení do cílových prostředků, včetně Azure Storage, pracovních prostorů Azure Log Analytics a centra událostí Azure s využitím služby Azure AD Domain Services. bran.

Další informace najdete v tématu [Povolení auditů zabezpečení pro Azure AD Domain Services (Preview)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Nové použití metod ověřování & Insights (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Samoobslužné resetování hesla  
**Schopnost produktu:** Monitorování & vytváření sestav

Nové metody ověřování & sestavy Insights vám pomůžou pochopit, jak se registrují a používají funkce jako Azure Multi-Factor Authentication a Samoobslužné resetování hesla ve vaší organizaci, včetně počtu registrovaných Uživatelé pro každou funkci, jak často se Samoobslužné resetování hesla používá k resetování hesel a podle toho, jakou metodu se resetuje.

Další informace najdete v tématu [použití metod ověřování & Insights (Preview)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Pro všechny správce Azure AD jsou k dispozici nové sestavy zabezpečení (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Ochrana identity  
**Schopnost produktu:** Zabezpečení identity & ochrana

Všichni Správci služby Azure AD teď můžou v horní části existujících sestav zabezpečení vybrat hlavičku, jako je například zpráva **Uživatelé označení příznakem rizika** , abyste mohli začít používat nové prostředí zabezpečení, jak je znázorněno v sestavách **rizikové uživatele** a **rizikových přihlášení** . V průběhu času se všechny sestavy zabezpečení přesunou ze starších verzí do nových verzí a nové sestavy poskytují následující další možnosti:

- Rozšířené filtrování a řazení

- Hromadné akce, jako je například chybějící riziko uživatele

- Potvrzení napadených nebo bezpečných entit

- Stav rizika, který pokrývá: ohrožené, neúspěšné, opravené a potvrzené ohrožení

Další informace najdete v tématu Sestava [rizikových uživatelů](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users) a [sestavy rizikových přihlášení](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nové audity zabezpečení pro Azure AD Domain Services (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Azure AD Domain Services  
**Schopnost produktu:** Azure AD Domain Services

S radostí oznamujeme vydání auditu zabezpečení Azure AD Domain Service do veřejné verze Preview. Auditování zabezpečení pomáhá poskytnout důležitý pohled na vaše služby ověřování – streamování událostí auditu zabezpečení do cílových prostředků, včetně Azure Storage, pracovních prostorů Azure Log Analytics a centra událostí Azure s využitím služby Azure AD Domain Services. bran.

Další informace najdete v tématu [Povolení auditů zabezpečení pro Azure AD Domain Services (Preview)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Nová federace B2B Direct pomocí SAML/WS-dodávání (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** B2B  
**Schopnost produktu:** B2B/B2C

Přímá federace pomáhá lépe pracovat s partnery, jejichž řešení identity spravované IT není Azure AD, a to díky práci se systémy identit, které podporují standardy SAML nebo WS-dodávání. Po nastavení přímého federačního vztahu s partnerem může každý nový uživatel typu Host, kterého budete pozvat z této domény, spolupracovat s vámi pomocí stávajícího účtu organizace, takže uživatelské prostředí pro hosty bude bezproblémové.

Další informace najdete v tématu [Přímá federace pomocí AD FS a poskytovatelů třetích stran pro uživatele typu Host (Preview)](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizace zřizování uživatelských účtů pro tyto nově podporované aplikace SaaS

**Zadejte:** Nová funkce  
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

**Zadejte:** Nová funkce  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Prostřednictvím

Když teď vytváříte nebo aktualizujete název skupiny z portálu Azure AD, provedeme kontrolu a zjistíme, jestli duplikujete existující název skupiny v prostředku. Pokud určíme, že se tento název už používá v jiné skupině, zobrazí se výzva, abyste změnili své jméno.

Další informace najdete v tématu [Správa skupin na portálu Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Azure AD teď podporuje statické parametry dotazů v identifikátorech URI odpovědi (přesměrování).

**Zadejte:** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů

Aplikace Azure AD teď můžou registrovat a používat identifikátory URI odpovědí (přesměrování) s parametry statického dotazu (například `https://contoso.com/oauth2?idp=microsoft`) pro žádosti OAuth 2,0. Parametr statického dotazu podléhá shodě řetězců pro identifikátory URI odpovědi, stejně jako všechny ostatní části identifikátoru URI odpovědi. Pokud neexistuje žádný registrovaný řetězec, který by odpovídal identifikátoru URI přesměrování adresy URL s dekódováním, požadavek se odmítne. Pokud se najde identifikátor URI odpovědi, použije se celý řetězec pro přesměrování uživatele, včetně parametru statického dotazu.

Dynamické identifikátory URI odpovědi jsou stále zakázané, protože představují bezpečnostní riziko a nelze je použít k uchování informací o stavu v rámci žádosti o ověření. Pro účely tohoto účelu použijte parametr `state`.

V současné době se na obrazovkách registrace aplikace Azure Portal pořád zablokují parametry dotazu. Manifest aplikace ale můžete upravit ručně a přidat a otestovat parametry dotazu v aplikaci. Další informace najdete v tématu [co je nového pro ověřování?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters).

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Protokoly aktivit (rozhraní API MS Graph) pro službu Azure AD jsou teď dostupné prostřednictvím rutin PowerShellu.

**Zadejte:** Nová funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav

S radostí oznamujeme, že protokoly aktivit Azure AD (sestavy auditu a přihlášení) jsou teď dostupné prostřednictvím modulu Azure AD PowerShellu. Dřív jste mohli vytvořit vlastní skripty pomocí koncových bodů MS Graph API a teď jsme tuto možnost rozšířili na rutiny PowerShellu.

Další informace o tom, jak tyto rutiny použít, najdete v tématu [rutiny Azure AD PowerShell pro vytváření sestav](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting).

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Aktualizované ovládací prvky filtru pro audit a protokoly přihlašování v Azure AD

**Zadejte:** Změněná funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav

Aktualizovali jsme sestavy protokolu auditu a přihlašování, takže teď můžete použít různé filtry, aniž byste je museli přidávat jako sloupce na obrazovkách sestavy. Navíc teď můžete rozhodnout, kolik filtrů chcete na obrazovce zobrazit. Tyto aktualizace společně usnadňují čtení a větší rozsah vašich potřeb vašich sestav.

Další informace o těchto aktualizacích najdete v tématech [filtrování protokolů auditu](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs) a [filtrování aktivit přihlášení](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities).

---

## <a name="june-2019"></a>Červen 2019

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Nové rozhraní riskDetections API pro Microsoft Graph (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Ochrana identity  
**Schopnost produktu:** Zabezpečení identity & ochrana

S potěšením oznamujeme, že nové rozhraní riskDetections API pro Microsoft Graph je teď ve verzi Public Preview. Toto nové rozhraní API můžete použít k zobrazení seznamu uživatelů s ochranou identity v organizaci a zjišťování rizik přihlašování. Toto rozhraní API můžete použít také k efektivnějšímu dotazování detekce rizik, včetně podrobností o typu detekce, stavu, úrovni a dalších.

Další informace najdete v [referenční dokumentaci k rozhraní API pro detekci rizik](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – červen 2019

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran

Do června 2019 jsme do Galerie aplikací přidali tyto 22 nových aplikací s podporou federace:

[Azure AD SAML Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial), [Otsuka Shokai (大塚商会)](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial), [ANAQUA](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial), [klient VPN pro Azure](https://portal.azure.com/), [ExpenseIn](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial), pomocná [pomoc](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial), [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial), [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial), [Mercedes-Benz](https://me.secure.mercedes-benz.com/), skore, [CYBERARK, scrible](https://app.justskore.it/) [cloudová infrastruktura](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [PandaDoc ověřování SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), Perceptyx [edu](https://www.scrible.com/sign-in/#/create-account), [Proptimise](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [Vtiger](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [OS](https://proptimise.co.uk/software/), [CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), Oracle Access Manager pro Oracle Maloobchodní prodej, Oracle Access Manager pro Oracle E-Business Suite, Oracle IDCS pro E-Business Suite, Oracle IDCS pro PeopleSoft spouštěných místně, Oracle IDCS pro řešení JD Edwards

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizace zřizování uživatelských účtů pro tyto nově podporované aplikace SaaS

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** Monitorování & vytváření sestav

Nyní můžete automatizovat vytváření, aktualizaci a odstraňování uživatelských účtů pro tyto nově integrované aplikace:

- [Přibliž](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Zástupné](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

Další informace o tom, jak lépe zabezpečit organizaci pomocí automatizovaného zřizování uživatelských účtů, najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) .

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Zobrazit průběh služby zřizování Azure AD v reálném čase

**Zadejte:** Změněná funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** Správa životního cyklu identit

Aktualizovali jsme prostředí pro zřizování Azure AD tak, aby obsahovalo nový indikátor průběhu, který vám ukáže, jak daleko jste v procesu zřizování uživatelů. Toto aktualizované prostředí obsahuje také informace o počtu uživatelů zřízených během aktuálního cyklu a také o tom, kolik uživatelů bylo zřízeno.

Další informace najdete v tématu o [kontrole stavu zřizování uživatelů](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user).

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>Branding společnosti se teď zobrazuje při odhlašování a chybových obrazovkách.

**Zadejte:** Změněná funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů

Službu Azure AD jsme aktualizovali tak, aby se vaše firemní logo zobrazovalo na obrazovkách pro odhlášení a chyb a taky na přihlašovací stránce. Nemusíte nic dělat, abyste tuto funkci zapnuli, Azure AD jednoduše používá prostředky, které jste už nastavili v oblasti **značky společnosti** Azure Portal.

Další informace o nastavení firemního brandingu najdete v tématu [Přidání brandingu na stránky Azure Active Directory vaší organizace](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding).

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Server Azure Multi-Factor Authentication (MFA) už není k dispozici pro nová nasazení.

**Zadejte:** Zastaralé  
**Kategorie služby:** VÍCEFAKTOROVÉHO  
**Schopnost produktu:** Zabezpečení identity & ochrana

Od 1. července 2019 už Microsoft nenabídne MFA Server pro nová nasazení. Noví zákazníci, kteří chtějí vyžadovat službu Multi-Factor Authentication ve své organizaci, teď musí používat cloudové Multi-Factor Authentication Azure. Zákazníci, kteří si server MFA vyaktivovali před 1. července, se nezmění. Pořád budete moct stáhnout nejnovější verzi, získat budoucí aktualizace a vygenerovat přihlašovací údaje pro aktivaci.

Další informace najdete v tématu [Začínáme s Azure Multi-Factor Authentication Server](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy). Další informace o cloudových Multi-Factor Authenticationch Azure najdete v tématu [plánování cloudového nasazení azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---

## <a name="may-2019"></a>Květen 2019

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Změna služby: budoucí podpora pro protokoly TLS 1,2 ve službě proxy aplikací

**Zadejte:** Plánování změn  
**Kategorie služby:** Proxy aplikace  
**Schopnost produktu:** Access Control

Abychom zákazníkům poskytli nejlepší šifrování v rámci své třídy, omezujeme přístup pouze na protokoly TLS 1,2 ve službě proxy aplikací. Tato změna se postupně zavádí pro zákazníky, kteří už používají jenom protokoly TLS 1,2, takže byste neměli vidět žádné změny.

Vyřazení TLS 1,0 a TLS 1,1 proběhne od 31. srpna 2019, ale budeme poskytovat další pokročilá oznámení, takže budete mít čas na tuto změnu připravovat. Aby se tato změna připravila, ujistěte se, že kombinace mezi klientem a serverem a prohlížečem, včetně všech klientů, které uživatelé používají pro přístup k aplikacím publikovaným pomocí proxy aplikací, se aktualizují tak, aby používaly protokol TLS 1,2 k údržbě připojení k aplikaci. Proxy služba. Další informace najdete v tématu [Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikace v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Pomocí sestavy využití a přehled můžete zobrazit přihlašovací údaje související s aplikacemi.

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** Monitorování & vytváření sestav

Teď můžete použít sestavu využití a přehledů, která se nachází v oblasti **podnikové aplikace** Azure Portal, a získat tak pohled na vaše přihlašovací údaje zaměřené na aplikaci, včetně informací o:

- Nejpoužívanější aplikace používané pro vaši organizaci

- Aplikace s neúspěšnými přihlášeními

- Nejčastější chyby při přihlašování pro každou aplikaci

Další informace o této funkci najdete v tématu [Sestava využití a přehledů na portálu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report) .

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatizace zřizování uživatelů pro cloudové aplikace pomocí Azure AD

**Zadejte:** Nová funkce  
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

**Zadejte:** Nová funkce  
**Kategorie služby:** NENÍ K DISPOZICI  
**Schopnost produktu:** Zabezpečení identity & ochrana

Teď můžete monitorovat a zdokonalovat stav zabezpečení identity pomocí funkce skóre Secure identity ve službě Azure AD. Funkce skóre Secure identity využívá jeden řídicí panel, který vám může pomáhat:

- Objektivně změřte stav zabezpečení identity, a to na základě skóre mezi 1 a 223.

- Plánování vylepšení zabezpečení identity

- Kontrola úspěšnosti vašich vylepšení zabezpečení

Další informace o funkci skóre zabezpečení identity najdete v tématu [co je bezpečné skóre identity v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Nyní je k dispozici nové prostředí Registrace aplikací (Obecná dostupnost).

**Zadejte:** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Vývojářské prostředí

Nové prostředí [Registrace aplikací](https://aka.ms/appregistrations) je teď všeobecně dostupné. Toto nové prostředí zahrnuje všechny klíčové funkce, se kterými jste obeznámeni z Azure Portal a portálu pro registraci aplikací a vylepšuje je prostřednictvím:

- **Lepší správa aplikací.** Místo prohlížení aplikací napříč různými portály teď můžete zobrazit všechny aplikace na jednom místě.

- **Zjednodušená registrace aplikace** Z vylepšeného prostředí navigace pro výběr oprávnění přepracované je teď snazší registrovat a spravovat vaše aplikace.

- **Podrobnější informace.** Můžete najít další podrobnosti o vaší aplikaci, včetně příruček pro rychlý Start a dalších.

Další informace najdete v části [Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/) a [prostředí registrace aplikací je teď všeobecně dostupné.](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) oznámení blogu

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Nové funkce dostupné v rozhraní API pro rizikové uživatele pro identitu Protection

**Zadejte:** Nová funkce  
**Kategorie služby:** Ochrana identity  
**Schopnost produktu:** Zabezpečení identity & ochrana

S radostí oznamujeme, že teď můžete pomocí rozhraní API pro rizikové uživatele načítat historii rizik uživatelů, odvolat rizikové uživatele a potvrdit ohrožení uživatelů. Tato změna vám pomůže efektivněji aktualizovat stav rizika vašich uživatelů a pochopit jejich historii rizik.

Další informace najdete v [referenční dokumentaci k rozhraní API pro rizikové uživatele](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – květen 2019

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran

V květnu 2019 jsme do Galerie aplikací přidali tyto 21 nových aplikací s podporou federace:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [skutečné odkazy](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [Simple Sign](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [Replay](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [Marketo Sales](https://toutapp.com/login)propojování, [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [subsystéms](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [META4 Global HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), na [pracovišti](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [kobalt](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), [webMethods API Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [Control](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [Foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Vylepšené vytváření a Správa skupin na portálu Azure AD

**Zadejte:** Nová funkce  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Prostřednictvím

Provedli jsme vylepšení prostředí souvisejících se skupinami na portálu Azure AD. Tato vylepšení umožňují správcům lépe spravovat seznamy skupin, seznamy členů a poskytovat další možnosti vytváření.

Mezi vylepšení patří:

- Základní filtrování podle typu členství a typu skupiny.

- Přidání nových sloupců, jako je zdrojová a e-mailová adresa.

- Možnost vícenásobného výběru skupin, členů a seznamů vlastníků pro snadné odstranění.

- Možnost výběru e-mailové adresy a přidání vlastníků během vytváření skupiny.

Další informace najdete v tématu [Vytvoření základní skupiny a přidání členů pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Konfigurace zásady pojmenování pro skupiny Office 365 na portálu Azure AD (Obecná dostupnost)

**Zadejte:** Změněná funkce  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Prostřednictvím

Správci teď můžou nakonfigurovat zásady pojmenování pro skupiny Office 365 pomocí portálu Azure AD. Tato změna pomáhá vymáhat konzistentní zásady vytváření názvů pro skupiny Office 365 vytvořené nebo upravované uživateli ve vaší organizaci.

Zásady pojmenování pro skupiny Office 365 můžete nakonfigurovat dvěma různými způsoby:

- Zadejte předpony nebo přípony, které se automaticky přidají do názvu skupiny.

- Nahrajte přizpůsobenou sadu blokovaných slov pro vaši organizaci, která nejsou povolená v názvech skupin (například generální ředitel, výplata, HR).

Další informace najdete v tématu [vymáhání zásady pojmenování pro skupiny Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Pro protokoly aktivit Azure AD jsou teď dostupné koncové body rozhraní Microsoft Graph API (Obecná dostupnost).

**Zadejte:** Změněná funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav

S radostí oznamujeme obecnou dostupnost Microsoft Graph Podpora koncových bodů rozhraní API pro protokoly aktivit služby Azure AD. V této verzi teď můžete používat protokol auditu Azure AD verze 1,0 jak v protokolu, tak i v protokolech rozhraní API pro přihlášení.

Další informace najdete v tématu [Přehled rozhraní API protokolu auditu služby Azure AD](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Správci teď můžou používat podmíněný přístup pro kombinovaný proces registrace (Public Preview).

**Zadejte:** Nová funkce  
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

**Zadejte:** Nová funkce  
**Kategorie služby:** Azure AD Identity Protection  
**Schopnost produktu:** Zabezpečení identity & ochrana

Detekce analýzy hrozeb Azure AD je teď k dispozici jako součást aktualizované Azure AD Identity Protection funkce. Tato nová funkce pomáhá indikovat neobvyklou aktivitu uživatelů pro konkrétního uživatele nebo aktivitu, která je konzistentní se známými vzory útoku na základě interních a externích zdrojů analýzy hrozeb Microsoftu.

Další informace o aktualizované verzi Azure AD Identity Protection najdete na blogu o [čtyřech hlavních Azure AD Identity Protection vylepšeních, která jsou teď ve verzi Public Preview](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) , a na [Azure Active Directory Identity Protection (aktualizované)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) předmětu. Další informace o detekci analýzy hrozeb v Azure AD najdete v článku [Azure Active Directory Identity Protection detekce rizik](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks) .

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Služba Azure AD nároking Management je teď dostupná (verze Public Preview).

**Zadejte:** Nová funkce  
**Kategorie služby:** Zásady správného řízení identity  
**Schopnost produktu:** Zásady správného řízení identity

Správa nároků služby Azure AD, která je teď ve verzi Public Preview, pomáhá zákazníkům delegovat správu balíčků přístupu, který definuje, jak můžou zaměstnanci a obchodní partneři požádat o přístup, kteří musí schvalovat a jak dlouho mají přístup. Přístup k balíčkům může spravovat členství v Azure AD a skupinách Office 365, přiřazování rolí v podnikových aplikacích a přiřazování rolí pro weby SharePointu Online. Přečtěte si další informace o správě nároků v tématu [Přehled správy nároků služby Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Další informace o šířce funkcí Azure AD Identity Governance, včetně Privileged Identity Management, kontrolách přístupu a podmínek použití, najdete v tématu [co je Azure AD identity governance?](../governance/identity-governance-overview.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Konfigurace zásady pojmenování pro skupiny Office 365 na portálu Azure AD (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Prostřednictvím

Správci teď můžou nakonfigurovat zásady pojmenování pro skupiny Office 365 pomocí portálu Azure AD. Tato změna pomáhá vymáhat konzistentní zásady vytváření názvů pro skupiny Office 365 vytvořené nebo upravované uživateli ve vaší organizaci.

Zásady pojmenování pro skupiny Office 365 můžete nakonfigurovat dvěma různými způsoby:

- Zadejte předpony nebo přípony, které se automaticky přidají do názvu skupiny.

- Nahrajte přizpůsobenou sadu blokovaných slov pro vaši organizaci, která nejsou povolená v názvech skupin (například generální ředitel, výplata, HR).

Další informace najdete v tématu [vymáhání zásady pojmenování pro skupiny Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Protokoly aktivit Azure AD jsou teď dostupné v Azure Monitor (Obecná dostupnost).

**Zadejte:** Nová funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav

Abychom vám pomohli vyřešit svůj názor na vizualizace pomocí protokolů aktivit Azure AD, zavádíme nové funkce Insights v Log Analytics. Tato funkce vám pomůže získat přehled o prostředcích Azure AD pomocí našich interaktivních šablon nazývaných sešity. Tyto předem připravené sešity můžou poskytovat podrobné informace pro aplikace nebo uživatele a zahrnovat:

- **Přihlášení.** Obsahuje podrobné informace o aplikacích a uživatelích, včetně umístění pro přihlášení, klientského operačního systému nebo klienta a verze prohlížeče a počtu úspěšných nebo neúspěšných přihlášení.

- **Starší verze ověřování a podmíněný přístup.** Obsahuje podrobné informace o aplikacích a uživatelích využívajících starší ověřování, včetně Multi-Factor Authenticationho použití aktivovaných zásadami podmíněného přístupu, aplikacích, které používají zásady podmíněného přístupu a tak dále.

- **Analýza neúspěšných přihlášení.** Pomáhá určit, jestli dochází k chybám přihlášení kvůli akci uživatele, problémům se zásadami nebo vaší infrastruktuře.

- **Vlastní sestavy.** Můžete vytvořit nové nebo upravit existující sešity, které vám pomůžou přizpůsobit funkci Insights pro vaši organizaci.

Další informace najdete v tématu [použití Azure Monitorch sešitů pro sestavy Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – duben 2019

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran

V dubnu 2019 jsme do Galerie aplikací přidali tyto 21 nových aplikací s podporou federace:

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks Single Sign-on](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [lavic](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial), [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), RStudio [Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [Mitel Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [Alibaba Cloud (jednotné přihlašování na základě rolí)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent pro správu kapitálu](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Sectigo Certificate Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [Monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [ SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [Indiggo](https://indiggolead.com/)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Nová možnost četnosti kontrol přístupu a výběr více rolí

**Zadejte:** Nová funkce  
**Kategorie služby:** Kontroly přístupu  
**Schopnost produktu:** Zásady správného řízení identity

Nové aktualizace v recenzích přístupu Azure AD umožňují:

- Kromě dříve existujících možností týdně, měsíčně, čtvrtletně a ročně můžete změnit četnost kontrol přístupu na **částečně ročně**.

- Při vytváření jedné kontroly přístupu vyberte víc rolí Azure AD a prostředků Azure. V této situaci se všechny role nastavují se stejným nastavením a všichni revidující se budou oznamovat současně.

Další informace o tom, jak vytvořit kontrolu přístupu, najdete v tématu [Vytvoření kontroly přístupu skupin nebo aplikací v kontrolách přístupu k Azure AD](https://docs.microsoft.com/azure/active-directory/governance/create-access-review).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect se převádějí e-mailové systémy upozornění, odesílají zákazníkům nové e-mailové informace o odesílateli.

**Zadejte:** Změněná funkce  
**Kategorie služby:** AD Sync  
**Schopnost produktu:** Platformy

Azure AD Connect právě přechází na naše systémy e-mailových výstrah, které si můžou někteří zákazníci zobrazit jako nového e-mailového odesílatele. Pokud to chcete vyřešit, musíte přidat `azure-noreply@microsoft.com` do seznamu povolených aplikací vaší organizace, nebo nebudete moct nadále přijímat důležité výstrahy ze svých služeb Office 365, Azure nebo služby synchronizace.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Změny přípony hlavního názvu uživatele (UPN) jsou teď mezi federované domény v Azure AD Connect úspěšné.

**Zadejte:** Určí  
**Kategorie služby:** AD Sync  
**Schopnost produktu:** Platformy

Teď můžete úspěšně změnit příponu UPN uživatele z jedné federované domény na jinou federované domény v Azure AD Connect. Tato oprava znamená, že během synchronizačního cyklu nebo při obdržení e-mailu s oznámením, "nelze aktualizovat tento objekt v Azure Active Directory, nemusíte mít nadále zkušenosti s chybovou zprávou FederatedDomainChangeError, protože atribut [ FederatedUser. UserPrincipalName], není platný. Aktualizujte hodnotu v místních adresářových službách.

Další informace najdete v tématu [řešení chyb během synchronizace](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Zvýšené zabezpečení pomocí zásad podmíněného přístupu na základě ochrany aplikací ve službě Azure AD (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity & ochrana

Podmíněný přístup na základě ochrany aplikací je teď k dispozici pomocí zásad **vyžadovat ochranu aplikací** . Tato nová zásada pomáhá zvýšit zabezpečení vaší organizace tím, že pomáhá zabránit:

- Uživatelé získají přístup k aplikacím bez Microsoft Intune licence.

- Uživatelé nemůžou získat Microsoft Intune zásady ochrany aplikací.

- Uživatelé získají přístup k aplikacím bez nakonfigurovaného Microsoft Intune zásady ochrany aplikací.

Další informace najdete v tématu [Postup při vyžadování zásad ochrany aplikací pro cloudový přístup k aplikaci pomocí podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Nová podpora jednotného přihlašování a podmíněného přístupu Azure AD v Microsoft Edge (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity & ochrana

Vylepšili jsme podporu Azure AD pro Microsoft Edge, včetně poskytování nové podpory pro jednotné přihlašování a podmíněný přístup Azure AD. Pokud jste už dřív používali Microsoft Intune Managed Browser, teď můžete místo toho použít Microsoft Edge.

Další informace o nastavení a správě zařízení a aplikací pomocí podmíněného přístupu najdete v tématu [vyžadování spravovaných zařízení pro cloudovou aplikaci přístup s](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) podmíněným přístupem a [vyžadování schválených klientských aplikací pro přístup k cloudovým aplikacím s podmíněným přístupem](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Další informace o správě přístupu pomocí Microsoft Edge se zásadami Microsoft Intune najdete v tématu [Správa přístupu k Internetu pomocí Microsoft Intune prohlížeče chráněného zásadami](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>Březen 2019

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Architektura prostředí identit a podpora vlastních zásad v Azure Active Directory B2C je teď k dispozici (GA)

**Zadejte:** Nová funkce  
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

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran

V březnu 2019 jsme do Galerie aplikací přidali tyto 14 nových aplikací s podporou federace:

[ISEC7](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [auditování na základě vysvětlení systém](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [štíhlé](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [PowerSchool výkonnostní otázky](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode](https://cinode.com/), Iris v [intranetu](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit horizonts](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [ú](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Nové konektory zřizování Zscaler a Atlassian v galerii Azure AD – březen 2019

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** integrace třetích stran

Automatizace vytváření, aktualizace a odstraňování uživatelských účtů pro následující aplikace:

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler Beta](https://aka.ms/ZscalerBetaProvisioning), [Zscaler One](https://aka.ms/ZscalerOneProvisioning), [Zscaler 2](https://aka.ms/ZscalerTwoProvisioning), [Zscaler tři](https://aka.ms/ZscalerThreeProvisioning), [Zscaler ZSCloud](https://aka.ms/ZscalerZSCloudProvisioning), Atlassian [Cloud](https://aka.ms/atlassianCloudProvisioning)

Další informace o tom, jak lépe zabezpečit organizaci prostřednictvím automatizovaného zřizování uživatelských účtů, najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](https://aka.ms/ProvisioningDocumentation).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Obnovení a Správa odstraněných skupin Office 365 na portálu Azure AD

**Zadejte:** Nová funkce  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Prostřednictvím

Odstraněné skupiny Office 365 teď můžete zobrazit a spravovat z portálu Azure AD. Tato změna vám pomůže zjistit, které skupiny jsou k dispozici pro obnovení, spolu s tím, že vám umožní trvale odstranit všechny skupiny, které nepotřebuje vaše organizace.

Další informace najdete v tématu [obnovení skupin s ukončenou platností nebo odstraněných](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Pro místní aplikace zabezpečené službou SAML v Azure AD prostřednictvím proxy aplikací (Public Preview) je teď k dispozici jednotné přihlašování.

**Zadejte:** Nová funkce  
**Kategorie služby:** Proxy aplikace  
**Schopnost produktu:** Access Control

Teď můžete pro místní aplikace ověřené pomocí SAML a vzdálený přístup k těmto aplikacím prostřednictvím proxy aplikací poskytnout jednotné přihlašování (SSO). Další informace o tom, jak nastavit jednotné přihlašování SAML v místních aplikacích, najdete v tématu [jednotné přihlašování SAML pro místní aplikace s proxy aplikací (Preview)](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Klientské aplikace v cyklech žádostí budou přerušeny, aby se zlepšila spolehlivost a činnost koncového uživatele.

**Zadejte:** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů

Klientské aplikace mohou v krátké době nesprávně vydávat stovky stejných žádostí o přihlášení. Tyto požadavky, ať už jsou úspěšné nebo ne, přispívají ke zhoršení uživatelského prostředí a zvýšenému zatížení pro IDP, zvyšují latenci pro všechny uživatele a snižují dostupnost IDP.

Tato aktualizace pošle chybu `invalid_grant`: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` klientským aplikacím, které vydávají duplicitní žádosti několikrát v krátké době, mimo rozsah běžné operace. Klientské aplikace, které narazí na tento problém, by měly zobrazit interaktivní výzvu a vyžadovat, aby se uživatel znovu přihlásil. Další informace o této změně a o tom, jak opravit aplikaci, pokud dojde k této chybě, najdete v tématu [co je nového pro ověřování?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Nové protokoly auditu – uživatelské prostředí teď k dispozici

**Zadejte:** Změněná funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav

Vytvořili jsme novou stránku **protokolů auditu** Azure AD, které vám pomůžou zlepšit čitelnost a jak hledat vaše informace. Pokud chcete zobrazit stránku nové **protokoly auditu** , vyberte v části **aktivita** v Azure AD možnost **protokoly auditu** .

![Stránka nové protokoly auditu s ukázkovými informacemi](media/whats-new/audit-logs-page.png)

Další informace o nové stránce **protokoly auditu** najdete [v tématu sestavy aktivit auditu na portálu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Nová upozornění a pokyny, které vám pomůžou zabránit nechtěnému uzamknutí správce z chybně konfigurovaných zásad podmíněného přístupu

**Zadejte:** Změněná funkce  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity & ochrana

Abychom správcům zabránili v náhodném uzamykání ze svých klientů prostřednictvím nesprávně nakonfigurovaných zásad podmíněného přístupu, vytvořili jsme nová upozornění a aktualizované pokyny v Azure Portal. Další informace o nových pokynech najdete v tématu [co jsou závislosti služby v Azure Active Directory podmíněný přístup](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Vylepšené používání funkcí pro koncové uživatele na mobilních zařízeních

**Zadejte:** Změněná funkce  
**Kategorie služby:** Podmínky použití  
**Schopnost produktu:** Řádnou

Aktualizovali jsme naše stávající prostředí s využitím, abychom vylepšili, jak si vyzkoušíte používání a souhlasíte s podmínkami použití v mobilním zařízení. Nyní se můžete přiblížit nebo oddálit, přejít zpátky, stáhnout informace a vybrat hypertextové odkazy. Další informace o aktualizovaných podmínek použití najdete v tématu [funkce Azure Active Directory podmínek použití](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Nové prostředí pro stažení protokolů aktivit služby Azure AD k dispozici

**Zadejte:** Změněná funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav

Nyní můžete stahovat velké objemy protokolů aktivit přímo z Azure Portal. Tato aktualizace vám umožní:

- Stáhněte si až 250 000 řádků.

- Po dokončení stahování se zobrazí oznámení.

- Přizpůsobte název souboru.

- Určete výstupní formát buď JSON, nebo CSV.

Další informace o této funkci najdete v tématu [rychlý Start: stažení sestavy auditu pomocí Azure Portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Zásadní změna: aktualizace pro vyhodnocení podmínek pomocí protokolu Exchange ActiveSync (EAS)

**Zadejte:** Plánování změn  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Access Control

Právě probíhá aktualizace způsobu, jakým protokol Exchange ActiveSync (EAS) vyhodnocuje následující podmínky:

- Umístění uživatele v závislosti na zemi, oblasti nebo IP adrese

- Riziko přihlášení

- Platforma zařízení

Pokud jste tyto podmínky dříve používali v rámci zásad podmíněného přístupu, mějte na paměti, že chování podmínky se může změnit. Pokud jste například v zásadě používali podmínku umístění uživatele v zásadě, můžete zjistit, že se zásady nyní přeskočí v závislosti na umístění uživatele.

---

## <a name="february-2019"></a>Únor 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Konfigurovatelné šifrování tokenů SAML Azure AD (Public Preview) 

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ

Teď můžete nakonfigurovat libovolnou podporovanou aplikaci SAML, aby přijímala šifrované tokeny SAML. Při konfiguraci a použití s aplikací Azure AD šifruje emitované kontrolní výrazy SAML pomocí veřejného klíče získaného z certifikátu uloženého v Azure AD.

Další informace o konfiguraci šifrování tokenu SAML najdete v tématu [Konfigurace šifrování tokenů SAML v Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Vytvoření kontroly přístupu pro skupiny nebo aplikace pomocí kontrol přístupu Azure AD

**Zadejte:** Nová funkce  
**Kategorie služby:** Kontroly přístupu  
**Schopnost produktu:** Řádnou

Teď můžete do jedné kontroly přístupu Azure AD přidat více skupin nebo aplikací pro členství ve skupině nebo přiřazení aplikace. Kontroly přístupu s více skupinami nebo aplikacemi se nastavují pomocí stejného nastavení a všichni zahrnutí recenzenti se oznamují současně.

Další informace o tom, jak vytvořit kontrolu přístupu pomocí kontrol přístupu ke službě Azure AD, najdete v tématu [Vytvoření kontroly přístupu skupin nebo aplikací v kontrolách přístupu Azure AD](https://docs.microsoft.com/azure/active-directory/governance/create-access-review) .

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD – únor 2019

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
V únoru 2019 jsme do Galerie aplikací přidali tyto 27 nových aplikací s podporou federace:

[Euromonitor Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MindTickle](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [Finger](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), Finger [,](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [iDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [Skyward Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight-CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), kliknutí, [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), Pexip, Stormboard [,](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial)seismických procesů, [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [sdílení](https://www.shareadream.org/how-it-works)na [,](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial) [webMethods Integration Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [znalostní báze LMS odkudkoli](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [organizační areál](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [Periscope data](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [portál Netop](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [smartvid.IO](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud podle Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [ClickUp produktivita platformy](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Rozšířená kombinovaná registrace MFA/SSPR

**Zadejte:** Změněná funkce  
**Kategorie služby:** Samoobslužné resetování hesla  
**Schopnost produktu:** Ověřování uživatelů

V reakci na zpětnou vazbu od zákazníků jsme vylepšili kombinované možnosti registrace MFA/SSPR, které pomáhají uživatelům rychleji registrovat své bezpečnostní údaje pro MFA i SSPR. 

**Chcete-li zapínat rozšířené možnosti pro uživatele dnes, postupujte podle následujících kroků:**

1. Jako globální správce nebo Správce uživatelů se přihlaste k Azure Portal a přejděte do **Azure Active Directory > nastavení uživatele > spravovat nastavení pro přístupové panely funkce ve verzi Preview**. 

2. V části **Uživatelé, kteří můžou používat funkce verze Preview pro registraci a správu informací o zabezpečení – možnost aktualizovat** , vyberte, pokud chcete zapnout funkce pro **vybranou skupinu uživatelů** nebo pro **všechny uživatele**.

V průběhu několika následujících týdnů odebereme možnost zapnout pro klienty, kteří ještě nemají zapnuté, možnost zapínat staré prostředí Preview s možností registrace pro MFA/SSPR.

**Pokud chcete zjistit, jestli se ovládací prvek pro vašeho tenanta odebere, postupujte podle těchto kroků:**

1. Jako globální správce nebo Správce uživatelů se přihlaste k Azure Portal a přejděte do **Azure Active Directory > nastavení uživatele > spravovat nastavení pro přístupové panely funkce ve verzi Preview**.  

2. Pokud jsou **Uživatelé, kteří můžou používat funkce verze Preview pro registraci a správu informací o zabezpečení** , nastavené na možnost **žádné**, bude možnost odebrána z vašeho tenanta.

Bez ohledu na to, jestli jste dříve zapnuli staré prostředí pro náhled registrace MFA/SSPR pro uživatele, nebo ne, se stará prostředí vypne v budoucím datu. Z tohoto důvodu důrazně doporučujeme, abyste se co nejdříve přesunuli na nové a rozšířené možnosti.

Další informace o tom, jak rozšířené možnosti registrace, najdete v tématu o tom, jaká vylepšení se týkají [služby Azure AD kombinovaná MFA a registrace resetování hesel](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Aktualizované prostředí pro správu zásad pro toky uživatelů

**Zadejte:** Změněná funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C

Aktualizovali jsme proces vytváření a správy zásad pro toky uživatelů (dříve označované jako integrované zásady). Toto nové prostředí je teď výchozím nastavením pro všechny klienty Azure AD.

Pomocí ikon úsměvu nebo zamračení v oblasti **poslat zpětnou vazbu** v horní části obrazovky portálu můžete zadat další názory a návrhy.

Další informace o novém prostředí pro správu zásad najdete v tématu [Azure AD B2C nyní má vlastní nastavení JavaScriptu a mnoho dalších nových funkcí](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blogu.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Zvolit konkrétní verze prvků stránky poskytované Azure AD B2C

**Zadejte:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C

Nyní můžete zvolit konkrétní verzi prvků stránky, které poskytuje Azure AD B2C. Výběrem konkrétní verze můžete otestovat své aktualizace, než se zobrazí na stránce, a můžete dosáhnout předvídatelného chování. Kromě toho teď můžete vyjádřit výslovný souhlas s tím, že vynutili konkrétní verze stránek, aby bylo možné přizpůsobit JavaScript. Pokud chcete tuto funkci zapnout, ve vašich uživatelských tocích klikněte na stránku **vlastností** .

Další informace o volbě konkrétní verze prvků stránky najdete v tématu [Azure AD B2C nyní má vlastní nastavení JavaScriptu a mnoho dalších nových funkcí](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blogu.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Konfigurovatelné požadavky na heslo pro koncové uživatele pro B2C (GA)

**Zadejte:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C

Pro koncové uživatele teď můžete nastavit složitost hesla vaší organizace, místo abyste museli používat vaše nativní zásady hesel Azure AD. V okně **vlastností** toků uživatelů (dříve označované jako integrované zásady) můžete zvolit složitost hesla **jednoduchého** nebo **silného**, nebo můžete vytvořit **vlastní** sadu požadavků.

Další informace o konfiguraci požadavků na složitost hesla najdete [v tématu Konfigurace požadavků na složitost pro hesla v Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Nové výchozí šablony pro prostředí pro ověřování vlastních značek

**Zadejte:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C

Můžete použít naše nové výchozí šablony, které jsou umístěné v okně **rozložení stránky** vašich uživatelských toků (dříve označované jako předdefinované zásady), a vytvořit tak pro uživatele vlastní prostředí pro ověřování značkou.

Další informace o používání šablon najdete v tématu [Azure AD B2C nyní má vlastní nastavení JavaScriptu a mnoho dalších nových funkcí](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>Leden 2019

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Spolupráce služby Active Directory B2B s použitím jednorázového ověřování pomocí hesla (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** B2B  
**Schopnost produktu:** B2B/B2C

Představili jsme JEDNORÁZOVé ověřování pomocí hesla pro uživatele typu Host B2B, kteří se nemůžou ověřit jiným způsobem jako Azure AD, účet Microsoft (MSA) nebo Google Federation. Tato nová metoda ověřování znamená, že uživatelé typu Host nemusejí vytvářet nové účet Microsoft. Místo toho může uživatel typu Host při uplatnění pozvánky nebo přístupu ke sdílenému prostředku požádat o dočasný kód, který se pošle e-mailové adrese. Pomocí tohoto dočasného kódu se uživatel typu Host může i nadále přihlašovat.

Další informace najdete v tématu [e-mailové ověřování heslem jednorázového hesla (Preview)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) a blogu. [Azure AD zajišťuje bezproblémové sdílení a spolupráci pro všechny uživatele s libovolným účtem](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Nové nastavení souborů cookie pro Azure Proxy aplikací služby AD

**Zadejte:** Nová funkce  
**Kategorie služby:** Proxy aplikace  
**Schopnost produktu:** Access Control

Zavedli jsme tři nové nastavení souborů cookie, která jsou k dispozici pro vaše aplikace, které jsou publikované prostřednictvím proxy aplikací:

- **Použijte soubor cookie pouze s protokolem HTTP.** Nastaví příznak **HttpOnly** pro přístup k proxy aplikací a soubory cookie relace. Zapnutím tohoto nastavení získáte další výhody zabezpečení, jako je například pomoc při kopírování a úpravách souborů cookie prostřednictvím skriptování na straně klienta. Pro přidané výhody doporučujeme zapnout tento příznak (vyberte **Ano**).

- **Použijte zabezpečený soubor cookie.** Nastaví **zabezpečený** příznak pro přístup k proxy aplikací a soubory cookie relace. Zapnutím tohoto nastavení získáte další výhody zabezpečení tím, že zajistíte, aby se soubory cookie přenesly přes zabezpečené kanály TLS, jako je například HTTPS. Pro přidané výhody doporučujeme zapnout tento příznak (vyberte **Ano**).

- **Použijte trvalý soubor cookie.** Zabrání vypršení platnosti přístupových souborů cookie, když je webový prohlížeč zavřený. Tyto soubory cookie jsou poslední po dobu životnosti přístupového tokenu. Soubory cookie se ale resetují, pokud je dosažený čas vypršení platnosti nebo pokud uživatel ručně odstraní soubor cookie. Doporučujeme ponechat výchozí nastavení **ne**. Zapněte jenom nastavení pro starší aplikace, které nesdílejí soubory cookie mezi procesy.

Další informace o nových souborech cookie najdete v tématu [nastavení souborů cookie pro přístup k místním aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Dostupné nové federované aplikace v galerii aplikací Azure AD – leden 2019

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
Do ledna 2019 jsme do Galerie aplikací přidali tyto 35 nové aplikace s podporou federace:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [talentůová paleta](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [informační CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco deštník](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [Zscaler Internet Access Administrator](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), [připomenutí vypršení platnosti](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [prohlížeč instavů](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [sloveso](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso Digital Close](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [Cloud Service Picco](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [funkční](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial) [, CallPlease](https://webapp.callplease.com/create-account/create-account.html), [GTNexus (SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial)), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [arů pro podniky](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 pro Office 365](https://www.k2.com/O365), [XLEDGER](https://www.xledger.net/), [iDiD Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial),, [na návštěvu](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [Korn trajekty Alp](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle cSaas Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Vylepšení nových Azure AD Identity Protection (verze Public Preview)

**Zadejte:** Změněná funkce  
**Kategorie služby:** Ochrana identity  
**Schopnost produktu:** Zabezpečení identity & ochrana

S radostí oznamujeme, že jsme do nabídky Azure AD Identity Protection Public Preview přidali následující vylepšení, včetně těchto:

- Aktualizované a více integrovaných uživatelských rozhraní

- Další rozhraní API

- Vylepšené hodnocení rizik prostřednictvím strojového učení

- Zarovnání celého produktu v rámci rizikových uživatelů a rizikových přihlášení

Další informace o vylepšeních najdete v tématu [co je Azure Active Directory Identity Protection (Aktualizováno)?](https://aka.ms/IdentityProtectionDocs) k získání dalších informací a ke sdílení nápadů můžete využít výzvy v rámci produktu.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Nová funkce zámku aplikace pro Microsoft Authenticator aplikaci na zařízeních s iOS a Androidem

**Zadejte:** Nová funkce  
**Kategorie služby:** Aplikace Microsoft Authenticator  
**Schopnost produktu:** Zabezpečení identity & ochrana

Chcete-li zachovat jednorázová hesla, informace o aplikaci a nastavení aplikace, můžete zapnout funkci zámku aplikace v aplikaci Microsoft Authenticator. Zapnutí zámku aplikace znamená, že budete požádáni o ověření pomocí PIN kódu nebo biometriky pokaždé, když otevřete aplikaci Microsoft Authenticator.

Další informace najdete v tématu [Nejčastější dotazy k aplikaci Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Rozšířené možnosti exportu Azure AD Privileged Identity Management (PIM)

**Zadejte:** Nová funkce  
**Kategorie služby:** Privileged Identity Management  
**Schopnost produktu:** Privileged Identity Management

Správci Privileged Identity Management (PIM) teď můžou exportovat všechna aktivní a oprávněná přiřazení rolí pro konkrétní prostředek, který zahrnuje přiřazení rolí pro všechny podřízené prostředky. Dřív bylo obtížné, aby správci získali úplný seznam přiřazení rolí pro předplatné a museli exportovat přiřazení rolí pro každý konkrétní prostředek.

Další informace najdete v tématu [zobrazení historie aktivit a auditu pro role prostředků Azure v PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

## <a name="novemberdecember-2018"></a>Listopad/prosinec 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Uživatelé odebrání z oboru synchronizace už nepřešli na účty jenom pro Cloud.

**Zadejte:** Určí  
**Kategorie služby:** Správa uživatelů  
**Schopnost produktu:** Službě

>[!Important]
>Z důvodu této opravy jsme slyšeli a porozuměli vašemu frustrace. Proto jsme tuto změnu vrátili až do doby, kdy ji můžeme pro implementaci ve vaší organizaci usnadnit.

Opravili jsme chybu, ve které by příznak nastavení dirsyncenabled uživatele byl chybně přepnut na **hodnotu false** , pokud byl objekt Active Directory Domain Services (služba AD DS) vyloučen z oboru synchronizace a následně přesunut do koše v Azure AD v následujícím synchronizačním cyklu. V důsledku této opravy platí, že pokud je uživatel vyloučen z rozsahu synchronizace a následně obnoven ze složky Koš služby Azure AD, uživatelský účet zůstane synchronizovaný z místní služby AD, jak je očekáván a nemůže být spravován v cloudu, protože jeho zdroj autority (SoA) zůstává jako místní služba AD.

Před touto opravou došlo k problému, když byl příznak nastavení dirsyncenabled přepnut na hodnotu false. Došlo k chybnému dojmu o tom, že se tyto účty převedly na objekty pouze cloudu a že je možné spravovat účty v cloudu. Účty ale pořád uchovávají svůj záznam SoA jako místní a všechny synchronizované vlastnosti (atributy stínů) pocházející z místní služby AD. Tento stav způsobilo více problémů v Azure AD a dalších cloudových úlohách (jako je Exchange Online), u kterých se tyto účty očekávaly jako synchronizované z AD, ale teď se chovají stejně jako účty jenom pro Cloud.

V současné době je jediným způsobem, jak skutečně převést účet synchronizovaných z AD na účet jenom pro Cloud, zakázáním DirSync na úrovni tenanta, což spustí back-end operaci přenosu prostředku SoA. Tento typ změny SoA vyžaduje (ale neplatí jenom pro) čištění všech místních atributů v relaci (například LastDirSyncTime a stínových atributů) a odesílání signálu do dalších cloudových úloh, aby měl příslušný objekt převedený jenom na cloudový účet. .

Tato oprava proto brání přímým aktualizacím atributu ImmutableID uživatele synchronizovaného ze služby AD, který v některých scénářích v minulosti byl nutný. V rámci návrhu ImmutableID objektu v Azure AD, jak název naznačuje, má být neměnné. Pro řešení těchto scénářů jsou k dispozici nové funkce implementované v Azure AD Connect Health a klientovi Azure AD Connect synchronizace.

- **ImmutableID aktualizace velkého rozsahu pro mnoho uživatelů v dvoufázovém přístupu**
  
  Například je třeba provést zdlouhavou migraci služba AD DS mezi doménovými strukturami. Řešení: použijte Azure AD Connect ke **konfiguraci kotvy zdrojového kódu** a, když uživatel migruje, zkopíruje existující hodnoty ImmutableID z Azure AD do atributu ms-DS-Consistency-GUID místního uživatele služba AD DS v nové doménové struktuře. Další informace najdete v tématu [použití MS-DS-ConsistencyGuid jako sourceAnchor](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor).

- **Rozsáhlé aktualizace ImmutableID pro mnoho uživatelů v jednom snímku**

  Například při implementaci Azure AD Connect uděláte chybu a teď potřebujete změnit atribut SourceAnchor. Řešení: zakažte DirSync na úrovni tenanta a vymažte všechny neplatné hodnoty ImmutableID. Další informace najdete v tématu [vypnutí synchronizace adresářů pro Office 365](/office365/enterprise/turn-off-directory-synchronization).

- **Přesouhlasení místního uživatele s existujícím uživatelem v Azure AD** Například uživatel, který byl znovu vytvořen v služba AD DS vygeneruje duplicitní hodnotu v účtu Azure AD, místo aby se přeshodoval s existujícím účtem služby Azure AD (osamocený objekt). Řešení: k přemapování zdrojového ukotvení/ImmutableID použijte Azure AD Connect Health v Azure Portal. Další informace najdete v tématu [scénář osamoceného objektu](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Zásadní změna: aktualizace schématu auditu a přihlášení prostřednictvím Azure Monitor

**Zadejte:** Změněná funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav

V současné době publikujete datové proudy protokolu auditu i přihlašování prostřednictvím Azure Monitor, takže můžete hladce integrovat soubory protokolů pomocí nástrojů pro SIEM nebo pomocí Log Analytics. V závislosti na vaší zpětné vazbě a při přípravě na obecné oznámení o dostupnosti této funkce provádíme v našem schématu následující změny. Tyto změny schématu a související aktualizace dokumentace budou provedeny první týden v lednu.

#### <a name="new-fields-in-the-audit-schema"></a>Nová pole ve schématu auditu
Přidáváme nové pole **typu operace** , které poskytne typ operace prováděné na prostředku. Například **přidejte**, **aktualizujte**nebo **odstraňte**.

#### <a name="changed-fields-in-the-audit-schema"></a>Změněná pole ve schématu auditu
Ve schématu auditu se mění následující pole:

|Název pole|Co se změnilo|Staré hodnoty|Nové hodnoty|
|----------|------------|----------|----------|
|Kategorie|Toto bylo pole **název služby** . Teď je to pole **kategorie auditu** . **Název služby** byl přejmenován na pole **loggedByService** .|<ul><li>Zřizování účtů</li><li>Základní adresář</li><li>Samoobslužné resetování hesla</li></ul>|<ul><li>Správa uživatelů</li><li>Správa skupin</li><li>Správa aplikací</li></ul>|
|targetResources|Zahrnuje **TargetResourceType** na nejvyšší úrovni.|&nbsp;|<ul><li>Zásada</li><li>Aplikace</li><li>Uživatel</li><li>Skupina</li></ul>|
|loggedByService|Poskytuje název služby, která vygenerovala protokol auditu.|Null|<ul><li>Zřizování účtů</li><li>Základní adresář</li><li>Samoobslužné resetování hesla</li></ul>|
|Výsledek|Poskytuje výsledek protokolů auditu. Dřív byl tento výčet vyhodnocený, ale nyní zobrazujeme skutečnou hodnotu.|<ul><li>0</li><li>1</li></ul>|<ul><li>Úspěch</li><li>Selhání</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Změněná pole ve schématu přihlášení
Ve schématu přihlášení se mění následující pole:

|Název pole|Co se změnilo|Staré hodnoty|Nové hodnoty|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|Toto bylo pole **conditionalaccessPolicies** . Teď je to pole **appliedConditionalAccessPolicies** .|Žádné změny|Žádné změny|
|conditionalAccessStatus|Poskytuje výsledek stavu zásad podmíněného přístupu při přihlášení. Dřív byl tento výčet vyhodnocený, ale nyní zobrazujeme skutečnou hodnotu.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Úspěch</li><li>Selhání</li><li>Nepoužito</li><li>Zakázáno</li></ul>|
|appliedConditionalAccessPolicies: výsledek|Poskytuje výsledek jednotlivých stavů zásad podmíněného přístupu při přihlášení. Dřív byl tento výčet vyhodnocený, ale nyní zobrazujeme skutečnou hodnotu.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Úspěch</li><li>Selhání</li><li>Nepoužito</li><li>Zakázáno</li></ul>|

Další informace o schématu najdete v tématu [Interpretace schématu protokolu auditu Azure AD v Azure monitor (Preview)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema) .

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Vylepšení Identity Protection pro model strojového učení pod dohledem a modul hodnocení rizik

**Zadejte:** Změněná funkce  
**Kategorie služby:** Ochrana identity  
**Schopnost produktu:** Hodnocení rizik

Vylepšení pro uživatele, kteří souvisejí s ochranou identity, a modul hodnocení rizik přihlašování, mohou pomoci zlepšit přesnost a rozsah rizik uživatelů. Správci mohou všimnout, že úroveň rizika uživatele již není přímo propojena s úrovní rizika konkrétních detekcí a že dojde ke zvýšení počtu a úrovně rizik přihlašování.

Detekce rizik se teď vyhodnocuje pomocí modelu strojového učení pod dohledem, který počítá riziko pro uživatele pomocí dalších funkcí přihlášení uživatele a vzoru detekce. Na základě tohoto modelu může správce najít uživatele s vysokým rizikovým skóre, a to i v případě, že zjištění přidružená tomuto uživateli mají nízké nebo střední riziko. 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Správci můžou resetovat svoje vlastní heslo pomocí aplikace Microsoft Authenticator (Public Preview).

**Zadejte:** Změněná funkce  
**Kategorie služby:** Samoobslužné resetování hesla  
**Schopnost produktu:** Ověřování uživatelů

Správci Azure AD teď můžou resetovat svoje vlastní heslo pomocí oznámení aplikace Microsoft Authenticator nebo kódu z jakékoli aplikace pro mobilní data nebo hardwarového tokenu. Aby mohli správci resetovat svoje vlastní heslo, bude teď moct použít dvě z následujících metod:

- Oznámení aplikace Microsoft Authenticator

- Jiná aplikace Mobile Authenticator/kód hardwarového tokenu

- Email

- Telefonní hovor

- Textová zpráva

Další informace o použití aplikace Microsoft Authenticator k resetování hesel najdete v tématu [Samoobslužné resetování hesla v Azure AD – mobilní aplikace a SSPR (Preview)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr) .

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Nová role správce cloudového zařízení služby Azure AD (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Registrace a Správa zařízení  
**Schopnost produktu:** Řízení přístupu

Správci můžou k nové roli správce cloudového zařízení přiřadit uživatele, aby mohli provádět úlohy správce cloudového zařízení. Uživatelé s rolí správce cloudových zařízení můžou povolit, zakázat a odstranit zařízení ve službě Azure AD, společně s možnostmi číst klíče Windows 10 BitLocker (pokud jsou k dispozici) v Azure Portal.

Další informace o rolích a oprávněních najdete v tématu [přiřazení rolí správce v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Správa zařízení pomocí časového razítka nové aktivity ve službě Azure AD (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Registrace a Správa zařízení  
**Schopnost produktu:** Správa životního cyklu zařízení

Uvědomujeme si, že v průběhu času musíte aktualizovat a vyřadit zařízení organizace ve službě Azure AD, abyste se vyhnuli používání zastaralých zařízení ve vašem prostředí. Pro pomoc s tímto procesem Azure AD teď aktualizuje vaše zařízení pomocí nového časového razítka aktivity, které vám pomůže se správou životního cyklu zařízení.

Další informace o tom, jak získat a použít toto časové razítko, najdete v tématu [Postupy: Správa zastaralých zařízení v Azure AD.](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Správci můžou od uživatelů vyžadovat, aby na každém zařízení přijali podmínky použití.

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmínky použití  
**Schopnost produktu:** Řádnou
 
Správci teď můžou zapnout možnost **vyžadovat, aby uživatelé souhlasí na všech možnostech zařízení** , aby vaši uživatelé mohli přijmout vaše podmínky použití na všech zařízeních, která používají ve vašem tenantovi.

Další informace najdete v [části používání podmínek použití funkce Azure Active Directory podmínek použití](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#per-device-terms-of-use)v rámci zařízení.

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Správci můžou nakonfigurovat podmínek použití, jejichž platnost vyprší na základě plánu opakování.

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmínky použití  
**Schopnost produktu:** Řádnou
 

Správci teď můžou zapnout možnost souhlasu s **vypršením platnosti** , aby vyprší platnost podmínek použití pro všechny vaše uživatele na základě zadaného plánu opakování. Plán může být jednou ročně, každý rok, čtvrtletně nebo měsíčně. Po vypršení platnosti podmínek použití musí uživatelé znovu přijmout.

Další informace najdete v [části Přidání podmínek použití funkce Azure Active Directory podmínek použití](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Správci můžou nakonfigurovat platnost podmínek použití na základě plánu každého uživatele.

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmínky použití  
**Schopnost produktu:** Řádnou

Správci teď můžou zadat dobu, po kterou musí uživatel znovu přijmout podmínky použití. Správci můžou například určit, že uživatelé musí znovu přijmout podmínky použití každých 90 dní.

Další informace najdete v [části Přidání podmínek použití funkce Azure Active Directory podmínek použití](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Nové e-maily Azure AD Privileged Identity Management (PIM) pro Azure Active Directory role

**Zadejte:** Nová funkce  
**Kategorie služby:** Privileged Identity Management  
**Schopnost produktu:** Privileged Identity Management
 
Zákazníci, kteří používají Azure AD Privileged Identity Management (PIM), teď můžou dostávat týdenní e-mailové zprávy o Digest, včetně následujících informací za posledních sedm dní:

- Přehled hlavních přiřazení s nárokem a trvalými rolemi

- Počet uživatelů, kteří aktivují role

- Počet uživatelů přiřazených k rolím v PIM

- Počet uživatelů přiřazených k rolím mimo PIM

- Počet uživatelů, kteří jsou trvalé, v PIM

Další informace o PIM a dostupných e-mailových oznámeních najdete [v tématu e-mailová oznámení v PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications).

---

### <a name="group-based-licensing-is-now-generally-available"></a>Licencování na základě skupin je teď všeobecně dostupné.

**Zadejte:** Změněná funkce  
**Kategorie služby:** Jiná  
**Schopnost produktu:** Službě

Licencování na základě skupin je ve verzi Public Preview a je teď všeobecně dostupná. V rámci tohoto obecného vydání jsme tuto funkci udělali lépe škálovatelnou a přidali jsme možnost znovu zpracovat přiřazení licencování na základě skupin pro jednoho uživatele a možnost používat licencování na základě skupin s licencemi Office 365 E3/a3.

Další informace o licencování na základě skupin najdete [v tématu Co je licencování na základě skupin v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD – listopad 2018

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
V listopadu 2018 jsme do Galerie aplikací přidali tyto 26 nových aplikací s podporou federace:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [gettam](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [GRA-PE](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [eHour](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [infinité areály](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya](https://alayagood.com/en/demo/), [HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [posun](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy pro Business Central 365](https://accounting.zenegy.com/), [EverBridge member Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [ideo](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [Allbound SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [aplikace objektů plex – klasický test ](https://test.plexonline.com/signon) [Aplikace objektů plex – Classic](https://www.plexonline.com/signon), [aplikace Plex – test uživatelského rozhraní](https://test.cloud.plex.com/sso), [aplikace Plex – UX](https://cloud.plex.com/sso), [aplikace Plex – IAM](https://accounts.plex.com/), [řemesla – záznamy o péči, docházka, & finanční sledování](https://getcrafts.ca/craftsregistration) 

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

## <a name="october-2018"></a>Říjen 2018

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Protokoly služby Azure AD teď pracovat s Azure Log Analytics (Public preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav

S potěšením oznamujeme, že teď můžou přesměrovávat protokolů služby Azure AD do služby Azure Log Analytics. Tato funkce nejžádanějších pomáhá poskytnout ještě lepší přístup k analytics pro vaši firmu, operace a zabezpečení, jakož i způsob, jak pomoc při sledování infrastruktury. Další informace najdete v tématu [protokoly aktivit Azure Active Directory v Azure Log Analytics nyní dostupném](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) blogu.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Nové federované aplikace k dispozici v galerii aplikací Azure AD –. října 2018

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran

V říjnu 2018 jsme přidali podporu těchto 14 nových aplikací s federací do Galerie aplikací:

[Body ocenění](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), ambyint, [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), Dialpad, [ON24 Virtual Environment](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), RingCentral [,](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial) [Zscaler tři](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial), [hodnocení](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), Workspot [Control](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-domain-services-email-notifications"></a>Azure AD Domain Services e-mailových oznámení

**Zadejte:** Nová funkce  
**Kategorie služby:** Azure AD Domain Services  
**Schopnost produktu:** Azure AD Domain Services

Azure AD Domain Services poskytuje upozornění na portálu Azure portal o nesprávné konfigurace nebo problémy s vaší spravované domény. Tyto výstrahy obsahují podrobné pokyny, takže se můžete pokusit opravit problémy, aniž by museli kontaktovat podporu.

Od října, budete mít k přizpůsobení nastavení oznámení pro spravované domény, pokud dojde k nové výstrahy, e-mail je odeslán do určeným skupiny lidí, tím eliminuje nutnost neustále podívejte se na portál pro aktualizace.

Další informace najdete v tématu [Nastavení oznámení v Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Azure AD portal podporuje použití domény ForceDelete rozhraní API pro odstranění vlastních domén 

**Zadejte:** Změněná funkce  
**Kategorie služby:** Správa adresářů  
**Schopnost produktu:** Službě

S radostí oznamujeme, že teď můžete použít domény ForceDelete rozhraní API odstranit vaši vlastní názvy domén asynchronně přejmenováním odkazy, jako jsou uživatelé, skupiny a aplikace z vlastního názvu domény (contoso.com) zpět na počáteční výchozí doména název ( contoso.onmicrosoft.com).

Tato změna vám umožní rychleji odstranit názvů vlastních domén, pokud vaše organizace již název používá, nebo pokud je třeba použít název domény pomocí jiné služby Azure AD.

Další informace najdete v tématu [odstranění vlastního názvu domény](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name).

---

## <a name="september-2018"></a>Září 2018
 
### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Aktualizovala se oprávnění role správce pro dynamické skupiny

**Zadejte:** Určí  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Prostřednictvím

Vyřešili jsme problém, konkrétní správce rolí teď můžete vytvářet a aktualizovat pravidla dynamického členství, aniž byste museli být vlastníkem skupiny.

Role jsou:

- Globální správce

- Správce Intune

- Správce uživatele

Další informace najdete v tématu [Vytvoření dynamické skupiny a kontroly stavu](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule) .

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Zjednodušená nastavení konfigurace jednotného přihlašování pro některé aplikace třetích stran

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ

Uvědomujeme si, že nastavení si jednotné přihlašování (SSO) k softwaru jako služby (SaaS) aplikací může být náročné vzhledem k jedinečné povaze konfiguraci jednotlivých aplikací. Sestavili jsme zjednodušené konfigurační prostředí k automaticky vyplní nastavení konfigurace jednotného přihlašování pro následující aplikace SaaS třetích stran:

- Zendesk

- ArcGis Online

- Jamf Pro

Pokud chcete začít používat toto prostředí jedním kliknutím, přejděte na stránku konfigurace **Azure Portal** > **SSO** pro danou aplikaci. Další informace najdete v tématu [Integrace aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) .

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Azure Active Directory – kde se nachází vaše data? stránka

**Zadejte:** Nová funkce  
**Kategorie služby:** Jiná  
**Schopnost produktu:** GoLocal

Vyberte oblast vaší společnosti z **Azure Active Directory – kde se nachází vaše data na stránce, kde se nachází vaše** datové centrum Azure pro všechny služby Azure AD. Informace můžete filtrovat podle konkrétní služby Azure AD pro oblasti vaší společnosti.

Pro přístup k této funkci a další informace najdete v tématu [Azure Active Directory – kde se nacházejí vaše data](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Pro přístupový panel Moje aplikace je k dispozici nový plán nasazení

**Zadejte:** Nová funkce  
**Kategorie služby:** Moje aplikace  
**Schopnost produktu:** JEDNOTNÉ

Podívejte se na nový plán nasazení, který je k dispozici pro přístupový panel Moje aplikace (https://aka.ms/deploymentplans).
Moje aplikace přístupový panel poskytuje uživatelům na jednom místě vyhledat a získat přístup k jejich aplikacím. Tento portál poskytuje také uživatelům samoobslužné služby příležitosti, jako je žádost o přístup k aplikacím a skupinám nebo správy přístupu k těmto prostředkům jménem jiných uživatelů.

Další informace najdete v tématu [co je to portál moje aplikace?](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Nová karta Řešení potíží a podpora na stránce Protokoly přihlášení na portálu Azure Portal

**Zadejte:** Nová funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav

Nová karta **Poradce při potížích a podpoře** na stránce pro **přihlášení** Azure Portal má pomoct správcům a pracovníkům podpory řešit problémy související s přihlášeními Azure AD. Tato nová karta poskytuje kód chyby, chybovou zprávu a doporučení pro nápravu (pokud existuje), které vám pomůžou problém vyřešit. Pokud tento problém nemůžete vyřešit, poskytujeme vám také nový způsob vytvoření lístku podpory pomocí prostředí **pro kopírování do schránky** , které vyplní pole s **ID žádosti** a **datem (UTC)** pro soubor protokolu ve vašem lístku podpory.  

![Přihlašovací protokoly zobrazující novou kartu](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Vylepšená podpora vlastních vlastností rozšíření, které se používají k vytvoření pravidel dynamického členství

**Zadejte:** Změněná funkce  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Prostřednictvím

V této aktualizaci teď můžete kliknout na odkaz **získat vlastnosti vlastního rozšíření** z Tvůrce pravidla dynamické skupiny uživatelů, zadat jedinečné ID aplikace a získat úplný seznam vlastností vlastního rozšíření, které se použijí při vytváření dynamického pravidla členství pro uživatele. Tento seznam můžete také aktualizovat, k získání nových vlastností vlastního rozšíření pro tuto aplikaci.

Další informace o použití vlastních vlastností rozšíření pro pravidla dynamického členství najdete v tématu [vlastnosti rozšíření a vlastnosti vlastního rozšíření](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties) .

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nové schválené klientské aplikace pro podmíněný přístup na základě aplikace Azure AD

**Zadejte:** Plánování změn  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity a ochrana

V seznamu [schválených klientských aplikací](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)jsou následující aplikace:

- Microsoft To-Do

- Microsoft Stream

Další informace naleznete v tématu:

- [Podmíněný přístup na základě aplikace Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Nová podpora samoobslužného resetování hesla ze zamykací obrazovky ve Windows 7/8/8.1

**Zadejte:** Nová funkce  
**Kategorie služby:** SSPR  
**Schopnost produktu:** Ověřování uživatelů

Po nastavení této nové funkce se uživatelům zobrazí odkaz pro resetování hesla z **zamykací** obrazovky zařízení se systémem Windows 7, Windows 8 nebo Windows 8.1. Když kliknete na tento odkaz, může uživatel v průvodci pomocí stejného procesu resetování hesla pomocí webového prohlížeče.

Další informace najdete v tématu [Jak povolit resetování hesla ze systému Windows 7, 8 a 8,1](https://aka.ms/ssprforwindows78) .

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Oznámení o změně: Autorizační kódy už nepůjde používat opakovaně 

**Zadejte:** Plánování změn  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů

Od 15. listopadu 2018, Azure AD, přestane přijímat předchozích ověřovací kódy pro aplikace. Tato změna zabezpečení pomáhá vám Azure AD podle specifikace OAuth a vynutí u koncových bodů v1 i v2.

Pokud vaše aplikace opakovaně používá autorizační kódy k získání tokenů pro několik prostředků, doporučujeme použít kód pro získání tokenu obnovení a pak pomocí tohoto tokenu obnovení získat další tokeny pro jiné prostředky. Autorizační kódy lze použít pouze jednou, ale obnovovací tokeny lze použít více než jednou v několika prostředcích. Aplikace, která se pokusí znovu použít ověřovacího kódu během toku OAuth kódu se zobrazí chyba invalid_grant.

U těchto a dalších změn souvisejících s protokoly si prohlédněte [úplný seznam novinek pro ověřování](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – září 2018

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
V září 2018 jsme přidali podporu těchto 16 nové aplikace s federací do Galerie aplikací:

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [vyhovující náborový software](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial), [Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), [ArcGIS Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), [JDA Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial), [Snowflake](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), NavigoCloud, [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial), JOIN.me, [ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial), [Silverback](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial), Riverbed Xirrus EasyPass, [Rackspace SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial), Enlyft SSO pro Azure, SurveyMonkey, [svolat](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial), dmarcian [](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="support-for-additional-claims-transformations-methods"></a>Podporují se další metody transformace deklarací identity

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ

Zavedli jsme nové metody transformace deklarace identity, ToLower () a ToUpper (), které je možné použít na tokeny SAML z **konfigurační stránky jednotného přihlašování** založené na SAML.

Další informace najdete v tématu [přizpůsobení deklarací identity vystavených v tokenu SAML pro podnikové aplikace v Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization) .

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Aktualizovalo se uživatelské rozhraní konfigurace aplikací založených na SAML (Preview)

**Zadejte:** Změněná funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ

Jako součást naší uživatelské rozhraní pro konfiguraci aktualizovaná aplikace založené na SAML získáte:

- Názorný postup aktualizované prostředí pro konfiguraci vaší aplikace založené na SAML.

- Větší přehled o tom, co je chybějící nebo nesprávné ve vaší konfiguraci.

- Možnost přidat více e-mailové adresy pro oznámení o vypršení platnosti certifikátu.

- Nové metody transformace deklarací identity, ToLower() byly a ToUpper() a další.

- Způsob, jak nahrát vlastní token podpisový certifikát pro podnikové aplikace.

- Způsob, jak nastavit formátu NameID pro aplikace SAML a způsob, jak nastavit hodnotu NameID jako rozšíření adresáře.

Pokud chcete zapnout toto aktualizované zobrazení, klikněte na odkaz **vyzkoušet náš nový zážitek** z horní části stránky **jednotného přihlašování** . Další informace najdete v tématu [kurz: Konfigurace jednotného přihlašování založeného na SAML pro aplikaci s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications).

---

## <a name="august-2018"></a>Srpen 2018

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Změnily se rozsahy IP adres služby Azure Active Directory

**Zadejte:** Plánování změn  
**Kategorie služby:** Jiná  
**Schopnost produktu:** Platformy

Zavádíme větší rozsahy IP adres do služby Azure AD, což znamená, že pokud jste nakonfigurovali rozsahy adres IP adres Azure AD pro brány firewall, směrovače nebo skupin zabezpečení sítě, musíte je aktualizovat. Tato aktualizace nyní proto nebudete muset změnit vaše brána firewall, směrovače nebo konfigurace rozsah IP skupiny zabezpečení sítě znovu, když přidá nové koncové body služby Azure AD. 

Síťový provoz se přesouvá na tyto nové oblasti v následujících dvou měsících. Pokud chcete pokračovat bez přerušení služby, je nutné přidat tyto aktualizované hodnoty na IP adresy před 10. září 2018:

- 20.190.128.0/18 

- 40.126.0.0/18 

Důrazně doporučujeme není odebírání staré rozsahy IP adres, dokud všechny přenosy v síti se přesunula do nové oblasti. Aktualizace informací o přesunutí a zjištění, kdy můžete odebrat staré rozsahy, najdete v tématu [adresy URL a rozsahy IP adres pro Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Oznámení o změně: Autorizační kódy už nepůjde používat opakovaně 

**Zadejte:** Plánování změn  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů

Od 15. listopadu 2018, Azure AD, přestane přijímat předchozích ověřovací kódy pro aplikace. Tato změna zabezpečení pomáhá vám Azure AD podle specifikace OAuth a vynutí u koncových bodů v1 i v2.

Pokud vaše aplikace opakovaně používá autorizační kódy k získání tokenů pro několik prostředků, doporučujeme použít kód pro získání tokenu obnovení a pak pomocí tohoto tokenu obnovení získat další tokeny pro jiné prostředky. Autorizační kódy lze použít pouze jednou, ale obnovovací tokeny lze použít více než jednou v několika prostředcích. Aplikace, která se pokusí znovu použít ověřovacího kódu během toku OAuth kódu se zobrazí chyba invalid_grant.

U těchto a dalších změn souvisejících s protokoly si prohlédněte [úplný seznam novinek pro ověřování](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).
 
---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Sloučená správa bezpečnostních údajů pro samoobslužné resetování hesla (SSPR) a Multi-Factor Authentication (MFA)

**Zadejte:** Nová funkce  
**Kategorie služby:** SSPR  
**Schopnost produktu:** Ověřování uživatelů
 
Tato nová funkce umožňuje uživatelům spravovat svoje bezpečnostní údaje (například telefonní číslo, mobilní aplikace a tak dále) pro samoobslužné resetování HESLA a vícefaktorové ověřování v jednom umístění a prostředí; ve srovnání s dříve, ve kterém bylo provedeno jako ve dvou různých umístěních.

Tato Sblížená funkce také funguje pro uživatele, kteří používají samoobslužné resetování HESLA nebo vícefaktorové ověřování. Navíc pokud vaše organizace nemá vynucení registrace MFA nebo samoobslužné resetování HESLA, osoby můžete zaregistrovat i jakékoli MFA nebo samoobslužné resetování HESLA metody informace o zabezpečení povolené ve vaší organizaci na portálu Moje aplikace.

Toto je vyjádření souhlasu se ve verzi public preview. Správci můžou zapnout nové prostředí (v případě potřeby) pro vybranou skupinu nebo pro všechny uživatele v tenantovi. Další informace o sblížení prostředí najdete na blogu věnovaném [sbližování zkušeností](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/) .

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Nové nastavení souborů cookie HTTP-Only v aplikacích Proxy aplikací služby Azure AD

**Zadejte:** Nová funkce  
**Kategorie služby:** Proxy aplikace  
**Schopnost produktu:** Access Control

Ve vašich aplikacích proxy aplikací se zavolalo nové nastavení s názvem **soubory cookie jen pro http** . Toto nastavení zajišťuje dodatečné zabezpečení včetně příznak HTTPOnly v hlavičce odpovědi protokolu HTTP pro obě aplikace Proxy přístupu a relace soubory cookie, zastavením přístup k souboru cookie ze skriptu na straně klienta a další brání akcím, jako je kopírování nebo Úprava souboru cookie. I když tento příznak nebyla použita dříve, soubory cookie byly vždy šifrovaný a přenáší prostřednictvím připojení SSL k ochraně proti nesprávné změny.

Toto nastavení není kompatibilní s aplikacemi využívajícími ovládací prvky ActiveX, jako je vzdálené plochy. Pokud jste v této situaci, doporučujeme vám, vypněte toto nastavení.

Další informace o nastavení souborů cookie pouze pro protokol HTTP najdete v tématu [publikování aplikací pomocí Azure proxy aplikací služby AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Privileged Identity Management (PIM) pro prostředky Azure podporuje typy prostředků Skupina pro správu

**Zadejte:** Nová funkce  
**Kategorie služby:** Privileged Identity Management  
**Schopnost produktu:** Privileged Identity Management
 
Aktivace a přiřazení nastavení Just-In-Time se teď může používat pro skupinu pro správu typy prostředků, stejně jako již máte pro předplatná, skupiny prostředků a prostředky (jako jsou virtuální počítače, aplikační služby a další). Každý, kdo má roli, která poskytuje přístup správce pro skupinu pro správu kromě toho zjišťovat a spravovat daný prostředek v PIM.

Další informace o prostředcích PIM a Azure najdete v tématu [zjišťování a Správa prostředků Azure pomocí Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources)
 
---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>Přístup k aplikacím (Preview) nabízí rychlejší přístup k portálu služby Azure AD

**Zadejte:** Nová funkce  
**Kategorie služby:** Privileged Identity Management  
**Schopnost produktu:** Privileged Identity Management
 
V současné době při aktivaci role pomocí PIM, může trvat více než 10 minut, než se oprávnění, než se projeví. Pokud se rozhodnete použít přístup k aplikaci, která je aktuálně ve verzi public preview, správci mohou přistupovat k portálu Azure AD, jakmile se dokončí požadavek na aktivaci.

Přístup k aplikacím v současné době pouze podporuje prostředí portálu Azure AD a prostředků Azure. Další informace o přístupu PIM a aplikace najdete v tématu [co je Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)
 
---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – srpen 2018

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
V srpna 2018 jsme přidali podporu těchto 16 nové aplikace s federací do Galerie aplikací:

[Hornbill](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial), [bridgeline bez vazby](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial), [Omáčk Labs – mobilní a webové testování](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial), [konektor meta sítě](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial), [způsob, jakým](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial)je, [Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial), [prohlavní (podle Inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial), SchoolBooking, [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial), [dokumentace](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial), [N2F – sestavy výdajů](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial), [Comm100 Live chat](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial) [, SafeConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial), [ZenQMS](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial), [eLuminate](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial), [Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial).

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>V Proxy aplikací služby Azure AD je teď k dispozici nativní podpora Tableau

**Zadejte:** Změněná funkce  
**Kategorie služby:** Proxy aplikace  
**Schopnost produktu:** Access Control

S naší aktualizaci z OpenID Connect protokolu udělení kódu OAuth 2.0 pro naše předběžné ověřovací protokol máte již provádět žádnou další konfiguraci Tableau pomocí Proxy aplikací. Tato změna protokol vám také pomůže Proxy aplikací, lepší podpora více moderních aplikací s použitím pouze přesměrování protokolu HTTP, které jsou běžně podporovány značky jazyka JavaScript a HTML.

Další informace o naší nativní podpoře pro Tableau najdete v tématu [Azure proxy aplikací služby AD nyní s nativní podporou Tableau](https://blogs.technet.microsoft.com/applicationproxyblog/2018/08/14/azure-ad-application-proxy-now-with-native-tableau-support).

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Nově se podporuje přidání Googlu jako zprostředkovatele identity pro uživatele B2B typu host v Azure Active Directory (Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** B2B  
**Schopnost produktu:** B2B/B2C

Nastavením federace se službou Google ve vaší organizaci, můžete nechat pozvaný Gmail při přihlášení uživatelů sdílené aplikacím a prostředkům pomocí svých existujících účtů Google, aniž byste museli vytvářet osobní Account Microsoft (MSA) nebo účet Azure AD.

Toto je vyjádření souhlasu se ve verzi public preview. Další informace o Google Federation najdete v tématu [Přidání Google jako zprostředkovatele identity pro uživatele typu Host B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

## <a name="july-2018"></a>Červenec 2018

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Vylepšení e-mailových oznámení služby Azure Active Directory

**Zadejte:** Změněná funkce  
**Kategorie služby:** Jiná  
**Schopnost produktu:** Správa životního cyklu identit
 
Azure Active Directory (Azure AD) e-mailů nyní funkce aktualizovaný návrh, jakož i změny odesílatele e-mailové adresy a zobrazované jméno odesílatele, při odeslání z následující služby:
 
- Kontroly přístupu Azure AD
- Azure AD Connect Health 
- Azure AD Identity Protection 
- Azure AD Privileged Identity Management
- Oznámení podnikových aplikací vypršení platnosti certifikátu
- Oznámení služby zřizování podnikových aplikací
 
E-mailová oznámení se odešlou z následujících e-mailové adresy a zobrazované jméno:

- E-mailová adresa: azure-noreply@microsoft.com
- Zobrazovaný název: Microsoft Azure
 
Příklad některých nových návrhů e-mailů a další informace najdete v tématu [e-mailová oznámení v Azure AD PIM](https://go.microsoft.com/fwlink/?linkid=2005832).

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Protokoly aktivit Azure AD jsou teď k dispozici prostřednictvím Azure Monitoru

**Zadejte:** Nová funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav

Protokoly aktivit Azure AD jsou teď dostupné ve verzi public preview pro Azure Monitor (Sledování služby celou platformu Azure). Azure Monitor nabízí dlouhodobé uchování a bezproblémové integrace. kromě tato vylepšení:

- Dlouhodobé uchovávání přesměrováním souborů protokolu do účtu Azure storage.

- Bezproblémová integrace SIEM, aniž by bylo potřeba psát nebo udržovat vlastní skripty.

- Bezproblémová integrace s vlastními řešeními, analytické nástroje nebo řešení správy incidentů.

Další informace o těchto nových funkcích najdete v článku [protokoly aktivit služby Azure AD ve službě Azure monitor Diagnostics jsou teď ve verzi Public Preview](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) a v naší dokumentaci [Azure Active Directory protokoly aktivit v Azure monitor (Preview)](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Informace o podmíněném přístupu přidávané do sestavy přihlášení k Azure AD

**Zadejte:** Nová funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
Umožňuje tato aktualizace můžete zjistit, které zásady jsou vyhodnocovány, když se uživatel přihlásí spolu s výsledky zásady. Kromě toho sestavu teď obsahuje typ klientské aplikace použitých uživatelem, abyste mohli identifikovat provozu starší verzi protokolu. Položky sestavy lze nyní také vyhledávat ID korelace, které najdete v chybové zprávě přístupných a slouží k identifikaci a řešení potíží s odpovídající žádost o přihlášení.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Zobrazení starších verzí ověřování prostřednictvím protokolů aktivit přihlášení

**Zadejte:** Nová funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav
 
Díky zavedení pole **klientské aplikace** v protokolech aktivit přihlašování zákazníci teď můžou zobrazit uživatele, kteří používají starší verze ověřování. Zákazníci budou mít přístup k těmto informacím pomocí Microsoft Graph rozhraní API pro přihlášení nebo prostřednictvím protokolů aktivit přihlašování na portálu Azure AD, kde můžete pomocí ovládacího prvku **klientská aplikace** filtrovat starší verze ověřování. Přečtěte si další podrobnosti naleznete v dokumentaci.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – červenec 2018

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
V červenci 2018 jsme přidali podporu těchto 16 nové aplikace s federací do Galerie aplikací:

[Centrum inovace](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial), [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial), [určité jednotné přihlašování pro správce](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial), PSUC fázování, [IPASS SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial), [záznam dění na více koncových matic](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial), PowerSchool sjednocené učebny, [Blažková](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial)registrace, [Bomgar vzdálená podpora](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial), [NimbleX](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial), [Představte webvision](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial), [Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial), [SecureW2 JoinNow Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial), [Kanbanize](../saas-apps/kanbanize-tutorial.md), [SmartLPA](../saas-apps/smartlpa-tutorial.md), [dovednosti – základ](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---
 
### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Nové integrace aplikací SaaS pro zřizování uživatelů – červenec 2018

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** integrace třetích stran
 
Azure AD umožňuje automatizovat vytváření, údržbu a odebírání uživatelských identit v aplikacích SaaS, jako je například Dropbox, Salesforce, ServiceNow a další. Pro července 2018 jsme přidali podporu pro následující aplikace v galerii aplikací Azure AD zřizování uživatelů:

- [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Bonusně](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

Seznam všech aplikací, které podporují zřizování uživatelů v galerii Azure AD, najdete v tématu [Integrace aplikací SaaS s Azure Active Directory](https://aka.ms/appstutorial).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Connect Health for Sync – Snazší způsob, jak opravovat chyby synchronizace osamocených a duplicitních atributů

**Zadejte:** Nová funkce  
**Kategorie služby:** Služba AD Connect  
**Schopnost produktu:** Monitorování & vytváření sestav
 
Azure AD Connect Health uvádí samoobslužnou nápravu můžete zvýraznit a oprava chyb synchronizace. Tato funkce odstraňování potíží s duplicitním atributem chyby synchronizace a opravy objekty, které jsou osamocené ze služby Azure AD. Tuto diagnostiku má následující výhody:

- Chyby synchronizace duplicitním atributem, poskytují konkrétní opravy může zúžit oblast

- Oprava se vztahuje vyhrazené scénáře služby Azure AD, řešení chyb v jediném kroku

- Žádný upgrade nebo konfigurace je potřeba zapnout a použít tuto funkci

Další informace najdete v tématu [Diagnostika a náprava chyb synchronizace duplicitních atributů](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors) .

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Vizuální aktualizace prostředí pro přihlašování k Azure AD a MSA

**Zadejte:** Změněná funkce  
**Kategorie služby:** Služba Azure AD  
**Schopnost produktu:** Ověřování uživatelů

Aktualizovali jsme v uživatelském rozhraní společnosti Microsoft online services přihlašovacího prostředí, například pro Office 365 a Azure. Díky této změně obrazovky, méně zaplněnou a jednodušší. Další informace o této změně najdete v článku o [nadcházejících vylepšeních na blogu věnovaném přihlašovacím zkušenostem služby Azure AD](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/) .

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Nová verze služby Azure AD Connect – červenec 2018

**Zadejte:** Změněná funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** Správa životního cyklu identit

Nejnovější verze služby Azure AD Connect zahrnuje: 

- Opravy chyb a aktualizací možnosti podpory 

- Obecná dostupnost integrace Federovat Ping

- Aktualizace na nejnovější verzi klienta SQL 2012 

Další informace o této aktualizaci najdete v tématu [Azure AD Connect: Historie vydání verze.](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history)

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>Aktualizace podmínek použití uživatelského rozhraní pro koncové uživatele

**Zadejte:** Změněná funkce  
**Kategorie služby:** Podmínky použití  
**Schopnost produktu:** Řádnou

Aktualizujeme přijetí řetězce v uživatelském rozhraní podmínek použití koncového uživatele.

**Aktuální text.** Aby bylo možné získat přístup k prostředkům [tenantName], je nutné přijmout podmínky použití.<br>**Nový text.** Aby bylo možné získat přístup k prostředku [tenantName], musí přečíst podmínky použití.

**Aktuální text:** Zvolíte-li možnost přijmout, znamená to, že souhlasíte se všemi výše uvedenými podmínkami použití.<br>**Nový text:** Kliknutím na přijmout potvrďte, že jste si přečetli a rozumíte podmínky použití.

---
 
### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>Předávací ověřování podporuje starší verze protokolů a aplikací

**Zadejte:** Změněná funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů
 
Předávací ověřování nyní podporuje starší protokoly a aplikace. Tato omezení jsou nyní plně podporovány:

- Přihlášení uživatelů do starší klientské aplikace Office, Office 2010 a Office 2013, bez nutnosti moderní ověřování.

- Přístup k sdílení kalendáře a volném informací v Exchangi hybridní prostředí s Office 2010 pouze.

- Uživatelská přihlášení ke Skypu pro firmy klientských aplikací bez moderního ověřování.

- Přihlašování uživatelů k prostředí PowerShell verze 1.0.

- Programu registrace zařízení Apple (Apple DEP), pomocí Průvodce nastavením iOS. 

---
 
### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Sloučená správa bezpečnostních údajů pro samoobslužné resetování hesla a Multi-Factor Authentication

**Zadejte:** Nová funkce  
**Kategorie služby:** SSPR  
**Schopnost produktu:** Ověřování uživatelů

Tato nová funkce umožňuje uživatelům spravovat svoje bezpečnostní údaje (například telefonní číslo, e-mailovou adresu, mobilní aplikace a tak dále) pro samoobslužné resetování hesla (SSPR) a Vícefaktorové ověřování (MFA) v jednom prostředí. Uživatelé už muset zaregistrovat stejné bezpečnostní údaje pro samoobslužné resetování HESLA a vícefaktorové ověřování ve dvou různých prostředích. Toto nové prostředí platí také pro uživatele, kteří mají samoobslužné resetování HESLA nebo vícefaktorové ověřování.

Pokud organizace nevynucována registrace MFA nebo SSPR, můžou si uživatelé zaregistrovat svoje bezpečnostní údaje prostřednictvím portálu **Moje aplikace** . Odtud uživatelé můžou registrovat jakékoli metody pro vícefaktorové ověřování a samoobslužné resetování HESLA povoleno. 

Toto je vyjádření souhlasu se ve verzi public preview. Správce můžete pro vybrané skupiny uživatelů nebo všechny uživatele v tenantovi zapnout na nové prostředí (v případě potřeby).

---
 
### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Ověřování identity pomocí aplikace Microsoft Authenticator při resetování hesla

**Zadejte:** Změněná funkce  
**Kategorie služby:** SSPR  
**Schopnost produktu:** Ověřování uživatelů

Tato funkce umožňuje bez oprávnění správce, potvrdit svou identitu při resetování hesla pomocí oznámení nebo kód v aplikaci Microsoft Authenticator (nebo jakoukoli ověřovací aplikaci). Po zapnutí admins tento samoobslužné resetování hesla – metoda, uživatelé, kteří se zaregistrovali prostřednictvím aka.ms/mfasetup nebo aka.ms/setupsecurityinfo mobilní aplikace můžete použít své mobilní aplikace jako metodu ověřování při resetování hesla.

Oznámení mobilní aplikace je možné zapnout jenom na jako součást zásady, které vyžaduje dvě metody k resetování hesla.

---

## <a name="june-2018"></a>Červen 2018

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Všimněte si, že změna: oprava zabezpečení do toku delegované autorizace pro aplikace využívající rozhraní API protokoly aktivit Azure AD

**Zadejte:** Plánování změn  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav

Z důvodu našeho silnějšího vynucování zabezpečení jsme museli provést změnu oprávnění pro aplikace, které používají delegovaný tok autorizace pro přístup k [rozhraním API protokolů aktivit služby Azure AD](https://aka.ms/aadreportsapi). Tato změna se projeví v **26. června 2018**.

Pokud některá z vašich aplikací používá rozhraní API protokolů aktivit Azure AD, zajistěte, aby se aplikace po změně nepřerušila.

**Aktualizace oprávnění aplikace**

1. Přihlaste se k Azure Portal, vyberte **Azure Active Directory**a pak vyberte **Registrace aplikací**.
2. Vyberte aplikaci, která používá rozhraní API protokolů aktivit Azure AD, vyberte **Nastavení**, vyberte **požadovaná oprávnění**a pak vyberte rozhraní **Windows Azure Active Directory** API.
3. V oblasti **delegovaná oprávnění** v okně **Povolit přístup** zaškrtněte políčko u **číst data adresáře** a pak vyberte **Uložit**.
4. Vyberte **udělit oprávnění**a pak vyberte **Ano**.
    
    >[!Note]
    >Musíte být globální správce k udělení oprávnění k aplikaci.

Další informace najdete v části [udělení oprávnění](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-prerequisites-azure-portal#grant-permissions) pro přístup k rozhraní API pro vytváření sestav Azure AD.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>Konfigurace nastavení protokolu TLS pro připojení ke službám Azure AD pro dodržování předpisů PCI DSS

**Zadejte:** Nová funkce  
**Kategorie služby:** NENÍ K DISPOZICI  
**Schopnost produktu:** Platformy

Zabezpečení TLS (Transport Layer) je protokol, který poskytuje ochranu osobních údajů a integrita dat mezi dvěma aplikacemi komunikaci a je nejčastěji nasazené zabezpečení protokol použitý ještě dnes.

[Rada standardů zabezpečení sběrnice PCI](https://www.pcisecuritystandards.org/) zjistila, že je třeba zakázat dřívější verze TLS a SSL (Secure SOCKETS Layer) (SSL), a to v souladu s tím, že zajišťují nové a bezpečnější protokoly aplikací, od **30. června 2018**. Tato změna znamená, že pokud se připojíte ke službám Azure AD a vyžadovat dodržování PCI DSS, je nutné zakázat protokol TLS 1.0. Více verzí TLS jsou k dispozici, ale je dostupná pro Azure Active Directory Services nejnovější verze TLS 1.2. Důrazně doporučujeme přechod přímo na TLS 1.2 pro kombinace klientem a serverem a serverem a prohlížečem.

Zastaralé prohlížeče nemusí podporovat novější verze TLS, jako je protokol TLS 1.2. Pokud chcete zjistit, které verze TLS podporuje váš prohlížeč, přejděte na web [Qualys SSL Labs](https://www.ssllabs.com/) a klikněte na **testovat prohlížeč**. Doporučujeme upgradovat na nejnovější verzi webového prohlížeče a pokud možno povolit jenom TLS 1.2.

**Povolení protokolu TLS 1,2 podle prohlížeče**

- **Microsoft Edge a Internet Explorer (obě se nastavují pomocí Internet Exploreru)**

    1. Otevřete Internet Explorer, vyberte **nástroje** > **Možnosti Internetu** > **Upřesnit**.
    2. V oblasti **zabezpečení** vyberte **použít TLS 1,2**a pak vyberte **OK**.
    3. Zavřete všechna okna prohlížeče a znovu spusťte aplikaci Internet Explorer. 

- **Google Chrome**

    1. Otevřete Google Chrome, do adresního řádku zadejte *Chrome://Settings/* a stiskněte **ENTER**.
    2. Rozbalte **Rozšířené** možnosti, klikněte na oblast **systému** a vyberte **Otevřít nastavení proxy serveru**.
    3. V poli **Vlastnosti Internetu** vyberte kartu **Upřesnit** , v oblasti **zabezpečení** vyberte možnost **použít TLS 1,2**a pak vyberte **OK**.
    4. Zavřete všechna okna prohlížeče a znovu spusťte Google Chrome.

- **Mozilla Firefox**

    1. Otevřete Firefox, do adresního řádku zadejte *About: config* a potom stiskněte klávesu **ENTER**.
    2. Vyhledejte termín, *TLS*a pak vyberte položku **Security. TLS. Version. Max** .
    3. Nastavte hodnotu na **3** , pokud chcete, aby prohlížeč používal až verzi TLS 1,2, a pak vyberte **OK**.

        >[!NOTE]
        >Firefox verze 60,0 podporuje TLS 1,3, takže můžete také nastavit hodnotu Security. TLS. Version. Max na **4**.

    4. Zavřete všechna okna prohlížeče a znovu spusťte Mozilla Firefox.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Nové federované aplikace k dispozici v galerii aplikací Azure AD –. června 2018

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
V červnu 2018 jsme přidali podporu těchto 15 nových aplikací s federací do Galerie aplikací:

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial), [Vyrovnávání hudby](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial), obchodní [aplikace s povoleným tokenem SAML 1,1](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial), [nálada](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial), automatického [úkolu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [zálohování koncových bodů](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Skyhigh sítě](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial), Smartway2, [TonicDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial), [Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial), [Zoho One](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial), [místní SharePoint](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial), [předvídat sadu CX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial), [Vidyard](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial), [ChronicX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Ochrana hesel Azure AD je k dispozici ve verzi public preview

**Zadejte:** Nová funkce  
**Kategorie služby:** Ochrana identity  
**Schopnost produktu:** Ověřování uživatelů

Pomocí hesel služby Azure AD Protection pomůže omezit problémy, snadno uhádnout hesel z vašeho prostředí. Odstranění tato hesla pomáhá snížit riziko ohrožení zabezpečení z typu útoku heslo zařízení.

Konkrétně ochrana hesel Azure AD vám pomůže:

- Ochrana vaší organizace účty v Azure AD a Windows Server Active Directory (AD). 
- Zastaví uživatele pomocí hesla na seznam více než 500 nejčastěji používaných hesel a více než 1 milion znaků nahrazení variace tato hesla.
- Správa hesel služby Azure AD ochrany z jednoho místa na portálu Azure AD pro Azure AD a místní Windows Server AD.

Další informace o ochraně heslem Azure AD najdete v tématu [odstranění chybných hesel ve vaší organizaci](https://aka.ms/aadpasswordprotectiondocs).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Nová šablona zásad podmíněného přístupu všichni hosté vytvořená během vytváření podmínek použití

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmínky použití  
**Schopnost produktu:** Řádnou

Při vytváření podmínek použití se vytvoří taky nová šablona zásad podmíněného přístupu pro všechny hosty a všechny aplikace. Tato nová šablona zásad platí nově vytvořený podmínek použití, usnadňuje vytváření a vynucování procesů pro hosty.

Další informace najdete v tématu [funkce Azure Active Directory podmínky použití](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Nová šablona zásad podmíněného přístupu vytvořená během vytváření podmínek použití

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmínky použití  
**Schopnost produktu:** Řádnou

Při vytváření podmínek použití je vytvořena také nová šablona zásad podmíněného přístupu "vlastní". Tato nová šablona zásad vám umožní vytvořit podmínky použití a pak hned přejít do okna pro vytvoření zásady podmíněného přístupu, aniž byste museli ručně procházet portálem.

Další informace najdete v tématu [funkce Azure Active Directory podmínky použití](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>Nové a komplexní informace o nasazení Azure Multi-Factor Authentication

**Zadejte:** Nová funkce  
**Kategorie služby:** Jiná  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
Vydali jsme nové podrobné pokyny o tom, jak nasadit Azure Multi-Factor Authentication (MFA) ve vaší organizaci.

Průvodce nasazením MFA zobrazíte tak, že přejdete do úložiště průvodce [nasazením identity](https://aka.ms/DeploymentPlans) na GitHubu. Chcete-li poskytnout zpětnou vazbu k průvodcům při nasazení, použijte [formulář zpětné vazby plánu nasazení](https://aka.ms/deploymentplanfeedback). Pokud máte nějaké dotazy týkající se průvodců nasazením, kontaktujte nás na adrese [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Delegovaná správa aplikací, které role jsou ve verzi public preview služby Azure AD

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** Access Control

Správci teď můžete delegovat úkoly při správě aplikací bez přiřazení rolí globálního správce. Nové role a funkce jsou:

- **Nové standardní role správce Azure AD:**

    - **Správce aplikace.** Umožňuje spravovat všechny aspekty všechny aplikace, včetně registrace, nastavení jednotného přihlašování, přiřazení aplikace a licencování, nastavení proxy aplikací a vyjádření souhlasu (s výjimkou k prostředkům Azure AD).

    - **Správce cloudové aplikace.** Uděluje všechny schopnosti Správce aplikací, s výjimkou proxy aplikací, protože neposkytuje přístup k místnímu.

    - **Vývojář aplikace** Udělí možnost vytvářet registrace aplikací i v případě, že je vypnutá možnost **Povolit uživatelům registraci aplikací** .

- **Vlastnictví (nastavení registrace jednotlivých aplikací a aplikace na podnikové úrovni, podobně jako u procesu vlastnictví skupiny:**
 
    - **Vlastník registrace aplikace** Umožňuje spravovat všechny aspekty registrace vlastní aplikace, včetně manifest aplikace a přidání dalších vlastníků.

    - **Vlastník podnikové aplikace** Uděluje možnost spravovat mnoho aspektů vlastní podnikové aplikace, včetně nastavení jednotného přihlašování, přiřazení aplikace a vyjádření souhlasu (s výjimkou k prostředkům Azure AD).

Další informace o veřejné verzi Preview najdete v tématu [role správy delegovaných aplikací Azure AD ve verzi Public Preview.](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) blog. Další informace o rolích a oprávněních najdete v tématu [přiřazení rolí správce v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal).

---

## <a name="may-2018"></a>Květen 2018

### <a name="expressroute-support-changes"></a>Změny podpory pro ExpressRoute

**Zadejte:** Plánování změn  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Platformy  

Software jako služba nabízí, jako je Azure Active Directory (Azure AD) jsou navrženy pro práci nejlepší tak, že přejdete přímo přes Internet, bez nutnosti ExpressRoute nebo jiné privátní tunely VPN. V důsledku toho od **1. srpna 2018**zastavíme podporu ExpressRoute pro služby Azure AD s využitím veřejných partnerských vztahů Azure a komunit Azure v partnerském vztahu Microsoftu. Všechny služby vliv tato změna může Všimněte si, že Azure AD provoz postupně posunutí z okruhu ExpressRoute k Internetu.

Zatímco měníme naši podporu Víme také, že s jsou stále situacích, kde možná budete muset použít vyhrazenou sadu okruhy pro ověřovací provoz. Z toho důvodu bude Azure AD nadále podporovat omezení rozsahu IP adres na tenanta už pomocí ExpressRoute a služby na partnerský vztah Microsoftu s komunitou "Jiné služby Office 365 Online". Pokud se to týká vaší služby, ale vyžadují ExpressRoute, postupujte takto:

- **Pokud pracujete ve veřejném partnerském vztahu Azure.** Přejděte na partnerský vztah Microsoftu a zaregistrujte se do **jiné komunity Office 365 Online Services (12076:5100)** . Další informace o tom, jak přejít z veřejného partnerského vztahu Azure na partnerský vztah Microsoftu, najdete v článku [přesunutí veřejného partnerského vztahu do partnerského vztahu Microsoftu](https://docs.microsoft.com/azure/expressroute/how-to-move-peering) .

- **Pokud jste v partnerském vztahu Microsoftu.** Zaregistrujte se do **jiné komunity služeb Office 365 online (12076:5100)** . Další informace o požadavcích směrování najdete v [části Podpora komunit protokolu BGP](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp) v článku požadavky na směrování ExpressRoute.

Pokud musíte i nadále používat vyhrazené okruhy, budete muset kontaktovat tým účtu Microsoft o tom, jak získat autorizaci k používání **jiné komunity služeb Office 365 online (12076:5100)** . Panelu MS Office spravované kontrola ověří, jestli potřebujete těchto okruhů a ujistěte se, že rozumíte technické důsledcích jejich. Neoprávněné předplatná pokusu o vytvoření filtrů tras pro Office 365 se zobrazí chybová zpráva. 
 
---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>Rozhraní Microsoft Graph API pro správu scénáře pro podmínky použití

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmínky použití  
**Schopnost produktu:** Vývojářské prostředí
 
Přidali jsme Microsoft Graph rozhraní API pro operace správy podmínek použití Azure AD. Můžete vytvářet, aktualizovat a odstraňovat podmínek použití objektu.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Přidat koncový bod pro více tenantů Azure AD jako zprostředkovatele identity v Azure AD B2C

**Zadejte:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C
 
Pomocí vlastních zásad, můžete nyní přidejte společný koncový bod Azure AD jako zprostředkovatele identity v Azure AD B2C. To umožňuje, abyste měli jediný bod položka pro všechny uživatele Azure AD, které se přihlašujete do svých aplikací. Další informace najdete v tématu [Azure Active Directory B2C: povolení přihlášení uživatelů k víceklientskému zprostředkovateli identit Azure AD pomocí vlastních zásad](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-commonaad-custom).

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Pomocí interní adresy URL pro přístup k aplikacím z libovolného místa s naše rozšíření Moje aplikace přihlásit a Azure AD Application Proxy

**Zadejte:** Nová funkce  
**Kategorie služby:** Moje aplikace  
**Schopnost produktu:** JEDNOTNÉ
 
Uživatelé mohou nyní přístup k aplikacím pomocí interní adresy URL, i když mimo vaši firemní síť s použitím rozšíření Moje aplikace zabezpečené přihlášení pro službu Azure AD. Bude to fungovat s libovolnou aplikací, které publikujete pomocí Proxy aplikací Azure AD a v jakémkoli prohlížeči, který má také nainstalované rozšíření prohlížeče přístupového panelu. Po přihlášení uživatele do rozšíření, se automaticky povolí funkci adresy URL přesměrování. Rozšíření je k dispozici ke stažení v [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176), [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)a [Firefox](https://go.microsoft.com/fwlink/?linkid=866366).

---
 
### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory – Data v Evropě pro zákazníky, Evropa

**Zadejte:** Nová funkce  
**Kategorie služby:** Jiná  
**Schopnost produktu:** GoLocal

Zákazníci v Evropě zůstat v Evropě se jejich data a nejsou replikovány mimo Evropských datových centrech zákony Evropské a ochrany osobních údajů schůzky. Tento [článek](https://go.microsoft.com/fwlink/?linkid=872328) obsahuje podrobné informace o tom, jaké informace o identitě se budou ukládat v rámci Evropy, a poskytuje také podrobnosti o informacích, které se uloží mimo Evropská datová centra. 

---
 
### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Nový uživatel zřizování integrace aplikací SaaS –. května 2018.

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** integrace třetích stran
 
Azure AD umožňuje automatizovat vytváření, údržbu a odebírání uživatelských identit v aplikacích SaaS, jako je například Dropbox, Salesforce, ServiceNow a další. Pro květen 2018 jsme přidali podporu pro následující aplikace v galerii aplikací Azure AD zřizování uživatelů:

- [BlueJeans](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bluejeans-provisioning-tutorial)

- [Základ – OnDemand](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Zendesk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

Seznam všech aplikací, které podporují zřizování uživatelů v galerii Azure AD, najdete v tématu [https://aka.ms/appstutorial](https://aka.ms/appstutorial).

---
 
### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>Kontroly přístupu Azure AD skupin a přístup k aplikaci nyní obsahuje opakované kontroly

**Zadejte:** Nová funkce  
**Kategorie služby:** Kontroly přístupu  
**Schopnost produktu:** Řádnou
 
Kontrola přístupu skupiny a aplikace je teď obecně dostupná jako součást Azure AD Premium P2.  Správci budou moct konfigurovat kontrol přístupu členství ve skupinách a přiřazení aplikací, které se automaticky opakují v pravidelných intervalech, jako je například měsíční nebo čtvrtletně.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Protokoly služby Azure AD aktivity (přihlášení a auditování) jsou teď k dispozici prostřednictvím MS Graphu

**Zadejte:** Nová funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav
 
Protokoly aktivit Azure AD, které obsahují protokoly přihlášení a auditu, jsou teď dostupné prostřednictvím rozhraní Microsoft Graph API. Pro přístup k těmto protokolům jsme nastavili dva koncové body prostřednictvím rozhraní Microsoft Graph API. Pokud chcete začít, podívejte se na naše [dokumenty](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) pro programový přístup k rozhraním API pro generování sestav Azure AD. 

---
 
### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Vylepšení uplatnění B2B prostředí a nechat organizací

**Zadejte:** Nová funkce  
**Kategorie služby:** B2B  
**Schopnost produktu:** B2B/B2C

**Doba uplatnění:** Po sdílení prostředku s uživatelem typu Host pomocí rozhraní B2B API – nemusíte posílat speciální e-mail s pozvánkou. Uživatel typu Host ve většině případů můžete přístup k prostředku a provedou prostřednictvím uplatnění prostředí za běhu. Žádné další dopad z důvodu chybějící e-mailů. Nemusíte už nic dalšího zeptat na uživatele typu Host. po kliknutí na odkaz na toto uplatnění vás systém poslal? ". To znamená, že jakmile SPO použije Pozvánka správce – zakalená přílohy můžete mít stejné kanonické adresy URL pro všechny uživatele – interní i externí – v libovolném státu uplatnění.

**Moderní prostředí pro uplatnění:** Žádná další cílová stránka pro vyplacení obrazovky Uživatelům se zobrazí jako moderní souhlas zkušenosti s zvoucí organizaci zásady ochrany osobních údajů, stejně jako u aplikací třetích stran.

**Uživatelé typu Host můžou organizaci opustit:** Jakmile se vztah uživatele s organizací převezme na více, můžou ho opustit. Žádné další volání správce pro pozvání k odebrání se neprovádí, žádné další vyzvednutí lístků podpory.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Nové federované aplikace k dispozici v galerii aplikací Azure AD –. května 2018.

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
V květnu 2018 jsme přidali podporu těchto 18 nových aplikací s federací do naší Galerie aplikací:

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial), Infogix [Data3Sixty se řídí](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial), [Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial), [Jamf pro](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial), [KnowledgeOwl](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial), [ENVI MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial), [LaunchDarkly,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial) [Adobe Captivate](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial), [MONTAGE online](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial), まなびポケット, OpenReel, [ARC Publishing-SSO](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial), [PlanGrid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial), [iWellnessNow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial), [Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial), [riskware](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial), [hejno](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial) [, ReviewSnap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Nový Průvodce podrobný postup nasazení služby Azure Active Directory

**Zadejte:** Nová funkce  
**Kategorie služby:** Jiná  
**Schopnost produktu:** Službě
 
Nové, podrobné pokyny k nasazení Azure Active Directory (Azure AD), včetně samoobslužného resetování hesla (SSPR), jednotného přihlašování (SSO), podmíněného přístupu (CA), proxy aplikací, zřizování uživatelů Active Directory Federation Services (AD FS) (ADFS) pro Předávací ověřování (PTA) a ADFS na synchronizaci hodnot hash hesel (KOSMETICE).

Průvodce nasazením zobrazíte tak, že přejdete do úložiště [Průvodce nasazením identity](https://aka.ms/DeploymentPlans) na GitHubu. Chcete-li poskytnout zpětnou vazbu k průvodcům při nasazení, použijte [formulář zpětné vazby plánu nasazení](https://aka.ms/deploymentplanfeedback). Pokud máte nějaké dotazy týkající se průvodců nasazením, kontaktujte nás na adrese [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Podnikové aplikace hledat – načtení dalších aplikací

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ
 
Máte potíže najít vaše aplikace / služby objekty zabezpečení? Přidali jsme možnost načíst další aplikace v oddílu podnikové aplikace seznam všech aplikací. Ve výchozím nastavení vám ukážeme, 20 aplikací. Teď můžete kliknout na **načíst další** a zobrazit další aplikace. 

---
 
### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>Květnové vydání AADConnect obsahuje verze public preview služby integrace s PingFederate, důležité aktualizace zabezpečení, řada oprav chyb a nové skvělé nové řešení potíží s nástroji. 

**Zadejte:** Změněná funkce  
**Kategorie služby:** Služba AD Connect  
**Schopnost produktu:** Správa životního cyklu identit
 
Květnové vydání AADConnect obsahuje verze public preview služby integrace s PingFederate, důležité aktualizace zabezpečení, řada oprav chyb a nové skvělé nové řešení potíží s nástroji. Poznámky k verzi najdete [tady](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#118190).

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Kontroly přístupu Azure AD: automaticky použít

**Zadejte:** Změněná funkce  
**Kategorie služby:** Kontroly přístupu  
**Schopnost produktu:** Řádnou

Kontroly přístupu skupin a aplikací jsou teď obecně dostupné v rámci Azure AD Premium P2. Správce může nakonfigurovat automaticky kontrola přístupu skončí použít recenzenta změny do této skupiny nebo aplikace. Správce můžete také určit, co se stane přístup uživatele, když revidující neměli reagovat, odebrat přístup, zachovat přístup nebo přijmout doporučení systému. 

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>Tokeny typu ID může už být vráceny za použití dotazu response_mode pro nové aplikace. 

**Zadejte:** Změněná funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů
 
Aplikace vytvořené od 25. dubna 2018 už nebudou moct požádat o **id_token** pomocí response_mode **dotazů** .  To přináší služby Azure AD vložená specifikace OIDC a pomáhá zmenšit prostor pro napadení vašich aplikací.  Aplikace vytvořené před 25. dubna 2018 nejsou zablokovány pomocí response_mode **dotazů** s response_type **id_token**.  Při žádosti o id_token z AAD se vrátila Chyba, je **AADSTS70007: dotaz není podporovaná hodnota response_mode při požadavku na token**.

**Fragment** a **form_post** response_modes nadále fungovat – při vytváření nových aplikačních objektů (například pro využití proxy aplikací) zajistěte použití jednoho z těchto response_modes před vytvořením nové aplikace.  

---
 
## <a name="april-2018"></a>Duben 2018 

### <a name="azure-ad-b2c-access-token-are-ga"></a>Azure AD B2C přístupový Token jsou obecně dostupné

**Zadejte:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C 

Teď přístup k webovým rozhraním API zabezpečené pomocí Azure AD B2C použitím přístupových tokenů. Tato funkce se přesouvá z verze public preview na všeobecně dostupnou Vylepšili jsme prostředí uživatelského rozhraní pro konfiguraci aplikací Azure AD B2C a webových rozhraní API a další drobná vylepšení byly provedeny.
 
Další informace najdete v tématu [Azure AD B2C: vyžádání přístupových tokenů](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-access-tokens).

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Testování konfigurace jednotného přihlašování pro aplikace založené na SAML

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ

Při konfiguraci aplikace založené na SAML jednotného přihlašování, budete moci testovat integraci na stránce konfigurace. Pokud narazíte na chybu během svého přihlašování, můžete zadat chyby v testovacím prostředí a Azure AD poskytuje řešení kroky k vyřešení konkrétního problému.

Další informace naleznete v tématu:

- [Konfigurace jednotného přihlašování k aplikacím, které nejsou v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)
- [Jak ladit jednotné přihlašování založené na SAML pro aplikace v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

---
 
### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Služba Azure AD terms of use teď má vytváření sestav pro jednotlivé uživatele.

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmínky použití  
**Schopnost produktu:** Dodržování
 
Správci teď můžete vybrat dané podmínky použití a zobrazení všech uživatelů, které mají souhlasil se podmínky použití a co datu a času byla provedena.

Další informace najdete v tématu [funkce Azure AD terms of use](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---
 
### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: Riziková IP adresa pro ochranu před uzamčením extranetu AD FS 

**Zadejte:** Nová funkce  
**Kategorie služby:** Jiná  
**Schopnost produktu:** Monitorování & vytváření sestav

Connect Health teď podporuje schopnost detekovat IP adresy, které by překračovaly prahovou hodnotu neúspěšných přihlášení U/P na hodinové nebo denní bázi. Možnosti poskytované touto funkcí jsou:

- Komplexní sestavu zobrazující IP adresu a počet neúspěšných přihlášení, které se pak generuje každý hodin nebo dnů s přizpůsobitelné prahovou hodnotu.
- E mailových výstrah, zobrazení překročení prahové hodnoty neúspěšných přihlášení U/P na základě hodin nebo dnů konkrétní IP adresu.
- Možnost provádět podrobné analýzy dat.

Další informace najdete v tématu [riziková zpráva protokolu IP](https://aka.ms/aadchriskyip).

---
 
### <a name="easy-app-config-with-metadata-file-or-url"></a>Snadná konfigurace aplikace pomocí souboru metadat nebo adresy URL

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ

Na stránce aplikace organizace můžete správci nahrát soubor metadat SAML konfigurace přihlašování SAML podle pro aplikaci Galerie AAD a mimo galerii.

Kromě toho adresa URL federačních metadat aplikace Azure AD můžete nakonfigurovat jednotné přihlašování s cílovou aplikací.

Další informace najdete v tématu [Konfigurace jednotného přihlašování k aplikacím, které nejsou v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Azure AD podmínky použití, které jsou teď obecně dostupná

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmínky použití  
**Schopnost produktu:** Dodržování
 

Azure AD terms of use se přesunuly z verze Public Preview na všeobecně dostupné.

Další informace najdete v tématu [funkce Azure AD terms of use](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Povolení nebo blokování pozvánek uživatelů B2B z konkrétních organizací

**Zadejte:** Nová funkce  
**Kategorie služby:** B2B  
**Schopnost produktu:** B2B/B2C
 

Teď můžete zadat které partnerských organizacích, kterou chcete sdílet a spolupracovat v spolupráce B2B ve službě Azure AD. K tomuto účelu můžete vytvořit seznam konkrétních povolují nebo odpírají domén. Domény blokován, pomocí těchto možností, zaměstnanci už odeslat pozvánky uživatelům v této doméně.

Díky tomu můžete řídit přístup k vašim prostředkům při povolování hladký pro schválení uživatelé.

Tato funkce spolupráce B2B je dostupná pro všechny Azure Active Directory zákazníky a dá se používat ve spojení s Azure AD Premium funkcemi, jako je podmíněný přístup a ochrana identity, pro podrobnější kontrolu nad tím, kdy a jak se budou externí uživatelé přihlašovat. v a získáte přístup.

Další informace najdete v tématu [Povolení nebo blokování pozvánek uživatelům B2B z konkrétních organizací](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-allow-deny-list).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nové federované aplikace k dispozici v galerii aplikací Azure AD

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran

V dubnu 2018 přidali jsme podporu těchto 13 nové aplikace s federací do naší Galerie aplikací:

Kritérium HCM, [FiscalNote](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fiscalnote-tutorial), [Secret Server (on-premises)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-secretserver-on-premises-tutorial), [dynamický signál](https://docs.microsoft.com/azure/active-directory/active-directory-saas-dynamicsignal-tutorial), [mindWireless](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mindwireless-tutorial), [organizační diagram nyní](https://docs.microsoft.com/azure/active-directory/active-directory-saas-orgchartnow-tutorial), [Ziflow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ziflow-tutorial), [AppNeta Performance Monitor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-appneta-tutorial), [Elium](https://docs.microsoft.com/azure/active-directory/active-directory-saas-elium-tutorial) , [Fluxx Labs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fluxxlabs-tutorial), [Cisco Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ciscocloud-tutorial), Policy, [SafetyNet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-safetynet-tutorial)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Přístup k uživatelům udělit B2B ve službě Azure AD pro vaše místní aplikace (public preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** B2B  
**Schopnost produktu:** B2B/B2C

Jako organizace, která používá možnosti spolupráce B2B Azure Active Directory (Azure AD) se pozvat uživatele typu Host z partnerských organizací do služby Azure AD můžete nyní zadat těmto uživatelům B2B přístup k místním aplikacím. Tyto místní aplikace můžou používat ověřování založené na SAML nebo integrované ověřování Windows (IWA) s omezené delegování protokolu Kerberos (KCD).

Další informace najdete v tématu [udělení přístupu k místním aplikacím uživatelům B2B v Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-hybrid-cloud-to-on-premises).

---
 
### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Získejte kurzy integrace jednotného přihlašování z Azure Marketplace

**Zadejte:** Změněná funkce  
**Kategorie služby:** Jiná  
**Schopnost produktu:** integrace třetích stran

Pokud aplikace, která je uvedena na [webu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) , podporuje jednotné přihlašování založené na SAML, kliknutím na **získat teď** získáte kurz integrace přidružený k této aplikaci. 

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Rychlejší výkon služby Azure AD automatické zřizování uživatelů pro aplikace SaaS

**Zadejte:** Změněná funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** integrace třetích stran
 
Dřív se zákazníci, kteří používají zřizování konektorů aplikací SaaS (například Salesforce, ServiceNow a pole) uživatelů Azure Active Directory může zaznamenat zpomalení výkonu více než 100 000 kombinované uživatelé s omezením jejich tenantů Azure AD a skupiny a jejich používali přiřazení uživatelů a skupin k určení, kteří by mělo proběhnout zřízení.

2\. dubna 2018 se nasadily významných vylepšení výkonu ke službě zřizování Azure AD, který výrazně omezit množství času potřebného k provedení počáteční synchronizace mezi Azure Active Directory a cílovým aplikacím SaaS.

V důsledku toho mnoho zákazníků, které má počáteční synchronizace do aplikace, které trvalo počtu dnů nebo nikdy dokončeno, jsou teď dokončení v řádu minut nebo hodin.

Další informace najdete v tématu [co se stane při zřizování?](/azure//active-directory/app-provisioning/how-provisioning-works)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Počítače připojené k samoobslužné resetování hesla ze zamykací obrazovky Windows 10 k hybridní službě Azure AD

**Zadejte:** Změněná funkce  
**Kategorie služby:** Samoobslužné resetování hesla  
**Schopnost produktu:** Ověřování uživatelů
 
Aktualizovali jsme funkci samoobslužného resetování HESLA Windows 10 zahrnují podporu pro počítače, které jsou připojená k hybridní Azure AD. Tato funkce je dostupná ve Windows 10 RS4 umožňuje uživatelům resetovat jejich hesla ze zamykací obrazovky z počítače s Windows 10. Uživatelé, kteří jsou povoleni a zaregistrován pro resetování hesla pomocí samoobslužné služby mohou tuto funkci využít.

Další informace najdete v tématu [resetování hesla Azure AD na přihlašovací obrazovce](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows).

---

## <a name="march-2018"></a>. Března 2018
 
### <a name="certificate-expire-notification"></a>Certifikát vypršel oznámení

**Zadejte:** Určí  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ
 
Azure AD posílá oznámení, když se certifikát pro galerii nebo aplikaci mimo Galerii brzy vyprší platnost. 

Někteří uživatelé nepřijala oznámení pro podnikové aplikace, které jsou nakonfigurované pro založené na SAML jednotného přihlašování. Tento problém byl vyřešen. Azure AD posílá oznámení pro certifikáty vyprší za 7, 30 a 60 dnů. Budete moct této události v protokolech auditování. 

Další informace naleznete v tématu:

- [Správa certifikátů pro federované jednotné přihlašování v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Sestavy aktivit auditu na portálu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
 
---
 
### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Zprostředkovatelé identity twitteru a Githubu v Azure AD B2C

**Zadejte:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C
 
Nyní můžete přidat Twitter nebo Githubu jako zprostředkovatele identity v Azure AD B2C. Twitter se přesouvá z verze public preview na všeobecně dostupnou GitHub se vydává ve verzi public preview.

Další informace najdete v tématu [co je spolupráce B2B Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
 
---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Omezení přístupu přes prohlížeč pomocí Intune Managed Browser s podmíněným přístupem založeným na aplikaci Azure AD pro iOS a Android

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
**Nyní ve verzi Public Preview!**

**Intune MANAGED Browser jednotné přihlašování:** Vaši zaměstnanci můžou používat jednotné přihlašování v rámci nativních klientů (například Microsoft Outlook) a Intune Managed Browser pro všechny aplikace připojené ke službě Azure AD.

**Intune Managed Browser podpora podmíněného přístupu:** Nyní můžete vyžadovat, aby zaměstnanci používali spravované prohlížeče Intune s využitím zásad podmíněného přístupu na základě aplikace.

Další informace najdete v našem [blogovém příspěvku](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

Další informace naleznete v tématu:

- [Nastavení podmíněného přístupu na základě aplikace](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

- [Konfigurace zásad spravovaného prohlížeče](https://aka.ms/managedbrowser)  

---
 
### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>Rutiny proxy aplikací v modulu PowerShell GA

**Zadejte:** Nová funkce  
**Kategorie služby:** Proxy aplikace  
**Schopnost produktu:** Access Control
 
Podpora pro rutiny proxy aplikací je teď v modulu PowerShell GA. To vyžaduje, abyste si udrželi přehled o modulech PowerShellu – Pokud se stanete více než rokem, některé rutiny můžou přestat fungovat. 

Další informace najdete v tématu [AzureAD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0).
 
---
 
### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Podporuje nativní klienty Office 365 pomocí neinteraktivního protokolu bezproblémového jednotného přihlašování

**Zadejte:** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů
 
Uživatele s využitím nativní klienty Office 365 (verze 16.0.8730.xxxx a vyšší) získat bezobslužné možnosti přihlašování pomocí bezproblémového jednotného přihlašování. Tato podpora je poskytována přidání jako neinteraktivní protokol (WS-Trust) do služby Azure AD.

Další informace najdete v tématu [jak se přihlašuje na nativním klientovi s bezproblémovým PŘIhlašováním](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work) .
 
---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>Uživatelé získají tiché přihlašování, pomocí bezproblémového jednotného přihlašování, když aplikace pošle žádostí o přihlášení ke koncovým bodům tenantů Azure AD

**Zadejte:** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů
 
Když aplikace (například `https://contoso.sharepoint.com`) odesílá žádosti o přihlášení do koncových bodů tenanta Azure AD – to znamená `https://login.microsoftonline.com/contoso.com/<..>` nebo `https://login.microsoftonline.com/<tenant_ID>/<..>` – místo společného koncového`https://login.microsoftonline.com/common/<...>`bodu služby Azure AD, se uživatelům zobrazí tiché přihlašování s bezproblémové jednotné přihlašování.

Další informace najdete v tématu [Azure Active Directory bezproblémové jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 

---
 
### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Třeba přidat pouze jednu adresu URL služby Azure AD, namísto dřívějších dvou. do nastavení zóny Intranet uživatele k zavedení bezproblémového jednotného přihlašování

**Zadejte:** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů
 
K zavedení bezproblémového jednotného přihlašování pro vaše uživatele musíte do nastavení zóny intranetu uživatele přidat jenom jednu adresu URL služby Azure AD pomocí zásad skupiny ve službě Active Directory: `https://autologon.microsoftazuread-sso.com`. Zákazníci byly dříve, povinné pro přidání dvě adresy URL.

Další informace najdete v tématu [Azure Active Directory bezproblémové jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 
 
---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nové federované aplikace k dispozici v galerii aplikací Azure AD

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran

V březnu 2018 přidali jsme podporu těchto 15 nových aplikací s federací do naší Galerie aplikací:

[Boxcryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial), [CylancePROTECT](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial), Wrike, [SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial), Assistant podle FirstAgenda, [YardiOne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial), Vtiger CRM, inmrknutí, [amplituda](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial), [Spacio](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial), [ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial), [Bersin](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial), [Mercell](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial), [Trisotech Digital Enterprise Server](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial), Qumu [Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial).
 
Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="pim-for-azure-resources-is-generally-available"></a>PIM pro Azure Resources je obecně dostupná

**Zadejte:** Nová funkce  
**Kategorie služby:** Privileged Identity Management  
**Schopnost produktu:** Privileged Identity Management
 
Pokud používáte Azure AD Privileged Identity Management pro role adresáře, můžete nyní použít PIM pro časově vázaná přístup a možnosti přiřazení pro role prostředků Azure, jako je například předplatná, skupiny prostředků, virtuální počítače a dalších prostředků, podporované pomocí Azure Resource Manageru. Vynutit ověřování Multi-Factor Authentication při aktivaci role Just-In-Time a plánování aktivací ve spolupráci s windows schválené změny. Kromě toho tato verze přidává vylepšení ve verzi public preview, včetně aktualizované uživatelské rozhraní, pracovních postupů schvalování a možnosti pro rozšíření role zanedlouho vyprší platnost a prodloužit platnost rolí není k dispozici.

Další informace najdete v tématu [prostředky PIM pro Azure (Preview)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac) .
 
---
 
### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Přidávání nepovinných deklarací identity do tokenů aplikací (public preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů
 
Vaše aplikace Azure AD můžete nyní žádosti o vlastní nebo nepovinné deklarace identity v tokeny Jwt nebo SAML tokeny.  Jedná se o deklarace identity o uživateli nebo tenantovi, které nejsou zahrnuté ve výchozím nastavení v tokenu, kvůli omezení velikosti nebo použitelnosti.  Toto je momentálně ve verzi public preview pro aplikace Azure AD na koncových bodech v1.0 a v2.0.  Najdete v dokumentaci pro informace na jaké deklarace identity je možné přidat a jak upravit manifest aplikace na vyžádání.  

Další informace najdete v tématu [volitelné deklarace identity ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).
 
---
 
### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Azure AD podporuje PKCE pro bezpečnější toky OAuth.

**Zadejte:** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů
 
Dokumentace služby Azure AD se aktualizovaly poznamenat podpora PKCE, který umožňuje bezpečnější komunikaci během toku udělení autorizačního kódu OAuth 2.0.  V koncových bodech v1.0 a v2.0 podporují jak S256 tak ve formátu prostého textu code_challenges. 

Další informace najdete v tématu [vyžádání autorizačního kódu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code). 
 
---
 
### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>Podpora zřizování všech hodnot atributů uživatelů dostupných v rozhraní Workday Get_Workers API

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** integrace třetích stran
 
Verze public preview příchozího zřizování z Workday do Active Directory a Azure AD teď podporuje možnost extrahovat a zřizování pro všechny hodnoty atributů k dispozici v rozhraní Workday Get_Workers API. Tento postup přidá podporuje pro stovky dalších standardní a vlastní atributy nad rámec těch, které jsou součástí počáteční verze pracovního dne příchozí zřizování konektoru.

Další informace najdete v tématu [přizpůsobení seznamu atributů uživatele Workday](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes) .

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Změna členství ve skupině z dynamického na statické a naopak

**Zadejte:** Nová funkce  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Prostřednictvím
 
Je možné změnit, jak se spravuje členství ve skupině. To je užitečné, pokud chcete zachovat stejný název skupiny a ID sady, tak, aby byly všechny existující odkazy na skupiny stále platná. vytváří se nová skupina bude vyžadovat aktualizaci těchto odkazů.
Aktualizovali jsme Centrum pro správu Azure AD pro podporu této funkce. Teď zákazníci můžou převést stávající skupiny z dynamického členství na přiřazené členství a naopak. Existující rutiny Powershellu jsou stále dostupné.

Další informace najdete v tématu [dynamická pravidla členství pro skupiny v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Vylepšené chování při odhlašování pomocí bezproblémového jednotného přihlašování

**Zadejte:** Změněná funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů
 
Dříve i v případě, že uživatelé explicitně odhlásili z aplikace zabezpečené pomocí Azure AD, jsou by být automaticky přihlášeni zpět pomocí bezproblémového jednotného přihlašování, pokud se pokoušeli pro přístup k aplikaci Azure AD znovu v rámci své podnikové síti z jejich zařízení připojených k doméně. Díky této změně se podporuje odhlašování.  To umožňuje uživatelům zvolit stejný nebo jiný Azure AD účtu se přihlásit zpátky pomocí, namísto automaticky Probíhá přihlašování pomocí bezproblémového jednotného přihlašování.

Další informace najdete v tématu [Azure Active Directory bezproblémové jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso) .
 
---
 
### <a name="application-proxy-connector-version-154020-released"></a>Verze konektoru Proxy aplikací 1.5.402.0 všeobecně dostupné

**Zadejte:** Změněná funkce  
**Kategorie služby:** Proxy aplikace  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
Tato verze konektoru se postupně zavádí do listopadu. Tato nová verze konektoru obsahuje následující změny:

- Konektor teď nastavuje soubory cookie úrovni domény místo toho úrovni subdomény. To zajistí, že plynulost jednotného přihlašování a zabraňuje výzev k ověření redundantní.
- Podpora pro požadavky na blokové kódování
- Vylepšený konektor monitorování stavu 
- Několik oprav chyb a vylepšení stability

Další informace najdete v tématu [vysvětlení konektorů služby Azure proxy aplikací služby AD](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors).
 
---

## <a name="february-2018"></a>Únor 2018
 
### <a name="improved-navigation-for-managing-users-and-groups"></a>Vylepšená navigace pro správu uživatelů a skupin

**Zadejte:** Plánování změn  
**Kategorie služby:** Správa adresářů  
**Schopnost produktu:** Službě

Navigační prostředí pro správu uživatelů a skupin je teď jednodušší. Z přehledu adresáře se teď můžete přejít přímo na seznam všech uživatelů, jednodušší přístup k seznamu odstraněných uživatelů. Z přehledu adresáře můžete také přejít přímo na seznam všech skupin, jednodušší přístup k nastavení skupiny pro správu. A také na stránce přehledu adresáře můžete hledat uživatele, skupiny, Podniková aplikace nebo registrace aplikace. 

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Dostupnost přihlašování a auditu sestavy v Microsoft Azure provozované společností 21Vianet (Azure China 21Vianet)

**Zadejte:** Nová funkce  
**Kategorie služby:** Azure Stack  
**Schopnost produktu:** Monitorování & vytváření sestav

Sestavy protokolu Azure AD aktivit jsou teď dostupné v Microsoft Azure provozované společností 21Vianet (Azure China 21Vianet) instance. Tyto protokoly jsou zahrnuty:

- **Protokoly aktivit přihlášení** – obsahuje všechny protokoly přihlášení přidružené k vašemu tenantovi.

- **Samoobslužné protokoly pro audit hesel** – obsahuje všechny protokoly auditu SSPR.

- **Protokoly auditu správy adresářů** – zahrnuje všechny protokoly auditu související se správou adresářů, jako je Správa uživatelů, Správa aplikací a další.

Pomocí těchto protokolů umožňují získat přehled o tom, jak stavu vašeho prostředí. Poskytnutá data vám umožní:

- Zjistěte, jak vaše aplikace a služby využít vašich uživatelů.

- Řešení potíží s problémy, které brání uživatelům v přístupu svou práci.

Další informace o tom, jak tyto sestavy používat, najdete v tématu [Azure Active Directory vytváření sestav](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal).

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Zobrazení sestav aktivit Azure AD pomocí role "Čtenáře sestav" (bez oprávnění správce role)

**Zadejte:** Nová funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav

V rámci zpětné vazby od zákazníků po povolení přístupu k protokolům aktivit Azure AD uživatelům, kteří jsou v roli Čtenář sestav, povolili uživatelům, kteří jsou v rámci této služby, přístup k přihlašovacím a auditovým aktivitám v rámci Azure Portal a používání Microsoft Graph API. 

Další informace o tom, jak tyto sestavy používat, najdete v tématu [Azure Active Directory vytváření sestav](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Deklarace identity EmployeeID dostupná jako atribut a identifikátor uživatele

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ
 
Pro uživatele a hosty B2B v přihlašovacích aplikacích založených na SAML můžete nakonfigurovat **ČísloZaměstnance** jako identifikátor uživatele a atribut uživatele pro uživatele v rámci aplikace pro přihlašování pomocí SAML.

Další informace najdete v tématu [přizpůsobení deklarací identity vystavených v tokenu SAML pro podnikové aplikace v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Zjednodušená správa aplikací pomocí zástupných znaků v Azure AD Application Proxy

**Zadejte:** Nová funkce  
**Kategorie služby:** Proxy aplikace  
**Schopnost produktu:** Ověřování uživatelů
 
K usnadnění nasazení aplikací a snížit vaše režijní náklady na správu, podporujeme nyní možnost publikovat aplikace pomocí zástupných znaků. Pokud chcete publikovat aplikaci se zástupnými znaky, můžete postupovat podle toku publikování standardní aplikace, ale použít zástupný znak v interní a externí adresy URL.

Další informace najdete v tématu [aplikace se zástupnými znaky v proxy aplikace Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-wildcard) .

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Nové rutiny, které podporují konfiguraci Proxy aplikací

**Zadejte:** Nová funkce  
**Kategorie služby:** Proxy aplikace  
**Schopnost produktu:** Platformy

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

**Zadejte:** Nová funkce  
**Kategorie služby:** Proxy aplikace  
**Schopnost produktu:** Platformy

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

**Zadejte:** Nová funkce  
**Kategorie služby:** AD Sync  
**Schopnost produktu:** Platformy
 
Azure AD Connect je preferovaný nástroj pro synchronizaci dat mezi službami Azure AD a na místní zdroje dat, včetně systému Windows Server Active Directory a protokolu LDAP.

>[!Important]
>Toto sestavení představuje schématu a synchronizační pravidlo změny. Službu Azure AD Connect synchronizaci aktivuje úplný Import a úplnou synchronizaci kroků po upgradu. Informace o tom, jak toto chování změnit, najdete v tématu [jak pozdržet úplnou synchronizaci po upgradu](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade).

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
 
### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Aplikace podporující zásady Intune App Protection přidané pro použití s podmíněným přístupem na základě aplikace Azure AD

**Zadejte:** Změněná funkce  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity & ochrana

Přidali jsme další aplikace, které podporují podmíněný přístup na základě aplikace. Teď můžete získat přístup k Office 365 a dalším aplikacím Azure AD připojení cloudu pomocí těchto schválených klientských aplikací.

Následující aplikace se přidají koncem února:

- Microsoft Power BI

- Spouštěcí program společnosti Microsoft

- Microsoft fakturace

Další informace naleznete v tématu:

- [Požadavek na schválenou aplikaci klienta](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Podmíněný přístup na základě aplikace Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Podmínky použití aktualizace na mobilní prostředí 

**Zadejte:** Změněná funkce  
**Kategorie služby:** Podmínky použití  
**Schopnost produktu:** Dodržování

Po zobrazení podmínek použití teď můžete kliknout na možnost **mít problémy se zobrazením? Klikněte sem**. Kliknutím na tento odkaz otevře podmínky použití nativně na vašem zařízení. Bez ohledu na velikost písma v dokumentu nebo rozlišení obrazovky zařízení můžete přiblížit a přečteme dokument podle potřeby. 

---
 
## <a name="january-2018"></a>. Ledna 2018
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nové federované aplikace k dispozici v galerii aplikací Azure AD 

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran

V lednu 2018 byly přidány následující nové aplikace s podporou federace v galerii aplikací:

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [OneTrust software pro správu ochrany osobních údajů](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [IriusRisk federovaný adresář a [věrnost NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701).

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="sign-in-with-additional-risk-detected"></a>Přihlaste se pomocí další zjištěné riziko

**Zadejte:** Nová funkce  
**Kategorie služby:** Ochrana identity  
**Schopnost produktu:** Zabezpečení identity & ochrana

Přehled, který získáte pro zjištění zjištěného rizika, je svázán s vaším předplatným služby Azure AD. V edici Azure AD Premium P2 získáte nejpodrobnější informace o základní všechna nalezení.

U edice Azure AD Premium P1 se detekce, která není pokrytá vaší licencí, zobrazuje jako přihlášení k detekci rizik s dalšími zjištěnými riziky.

Další informace najdete v tématu [Azure Active Directory detekci rizik](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events).
 
---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Skrýt aplikace Office 365 z přístupových panelech koncového uživatele

**Zadejte:** Nová funkce  
**Kategorie služby:** Moje aplikace  
**Schopnost produktu:** JEDNOTNÉ

Teď můžete lépe spravovat jak aplikacím Office 365 objeví na přístupových panelech uživatele pomocí nového nastavení uživatele. Tato možnost je užitečná pro snížení počtu aplikací v přístupových panelech uživatele, pokud chcete zobrazit jenom aplikace Office na portálu Office. Nastavení se nachází v **nastavení uživatele** a je označeno, **Uživatelé mohou na portálu Office 365 zobrazit pouze aplikace Office 365**.

Další informace najdete v tématu [skrytí aplikace z uživatelského prostředí v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

---
 
### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Bezproblémové přihlašování do aplikací, které jsou povolené pro jednotné přihlašování hesla přímo z adresy URL vaší aplikace 

**Zadejte:** Nová funkce  
**Kategorie služby:** Moje aplikace  
**Schopnost produktu:** JEDNOTNÉ

Rozšíření prohlížeče Moje aplikace je teď dostupná přes praktický nástroj, který poskytuje jednotné přihlašování v Moje aplikace na funkce jako zástupce v prohlížeči. Po instalaci se uživatele se zobrazuje ikona dlaždice ve svém prohlížeči, který jim poskytuje rychlý přístup k aplikacím. Uživatelé teď mohou využít výhod:

- Možnost přímého přihlašování k aplikacím založeným na JEDNOTNÉm přihlašování na přihlašovací stránce aplikace
- Spuštění libovolné aplikace pomocí funkce rychlého vyhledávání
- Klávesové zkratky pro naposledy použité aplikace z rozšíření
- Rozšíření je k dispozici pro Microsoft Edge, Chrome a Firefox.
 
Další informace najdete v tématu [zabezpečený přihlašovací rozšíření pro moje aplikace](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Správa služby Azure AD byl vyřazen prostředí portálu Azure Classic

**Zadejte:** Zastaralé   
**Kategorie služby:** Služba Azure AD  
**Schopnost produktu:** Službě

Od 8. ledna 2018, Správa služby Azure AD byl vyřazen prostředí na portálu Azure classic. To konal úplně ve spojení s vyřazením na portálu Azure classic, samotného. V budoucnu byste měli použít [Centrum pro správu Azure AD](https://aad.portal.azure.com) pro veškerou správu Azure AD založenou na portálu.
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>Na webovém portálu PhoneFactor byl vyřazen.

**Zadejte:** Zastaralé  
**Kategorie služby:** Služba Azure AD  
**Schopnost produktu:** Službě
 
Od 8. ledna 2018 je vyřazený na webovém portálu PhoneFactor. Byl použit tento portál pro správu MFA serveru, ale tato funkce se přesunuly na web Azure Portal na adrese portal.azure.com. 

Konfigurace MFA se nachází tady: **Azure Active Directory \> MFA Server**
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Vyřazení sestav Azure AD

**Zadejte:** Zastaralé  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Správa životního cyklu identit  


Obecná dostupnost nové konzoly Správa služby Azure Active Directory a nová rozhraní API pro aktivity a zabezpečení sestavy, sestava rozhraní API teď k dispozici v části "/ reports" koncový bod byl vyřazen od konce 31. prosince 2017.

**Co je k dispozici?**

V rámci přechodu na Nová konzola pro správu jsme zpřístupnili 2 nová rozhraní API pro načítání protokolů aktivit Azure AD. Nová sada rozhraní API poskytuje širší filtrování a řazení funkce kromě toho, že bohatší auditu a aktivit přihlašování. Data dříve dostupná prostřednictvím sestav zabezpečení teď můžou být přístupná prostřednictvím rozhraní API detekce rizik ochrany identity v Microsoft Graph.

Další informace naleznete v tématu:

- [Začínáme s rozhraním API pro vytváření sestav Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Začínáme s Azure Active Directory Identity Protection a Microsoft Graph](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)

---

## <a name="december-2017"></a>Prosinec 2017

### <a name="terms-of-use-in-the-access-panel"></a>Podmínky použití na přístupovém panelu

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmínky použití  
**Schopnost produktu:** Dodržování
 
Teď můžete přejít na přístupovém panelu a zobrazit podmínky použití, které jste dříve přijali.

Postupujte následovně:

1. Přejít na [portál MyApp](https://myapps.microsoft.com)a přihlásit se.

2. V pravém horním rohu vyberte své jméno a pak v seznamu vyberte možnost **profil** . 

3. V **profilu**vyberte možnost **přezkoumat podmínek použití**. 

4. Nyní můžete přečíst podmínky použití přijmout. 

Další informace najdete v tématu [funkce Azure AD terms of use (Preview)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---
 
### <a name="new-azure-ad-sign-in-experience"></a>Nové prostředí přihlášení Azure AD

**Zadejte:** Nová funkce  
**Kategorie služby:** Služba Azure AD  
**Schopnost produktu:** Ověřování uživatelů
 
Azure AD a systému identit účtů Microsoft uživatelská rozhraní byla znovu navržena tak, aby měly jednotný vzhled a chování. Kromě toho na přihlašovací stránku Azure AD shromažďuje uživatelské jméno nejprve následuje přihlašovací údaje, které na druhé obrazovce.

Další informace najdete v tématu [nové přihlášení k Azure AD je teď ve verzi Public Preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/).
 
---
 
### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Menším počtu výzev přihlášení: O nové "neodhlašovat" prostředí pro přihlašování Azure AD

**Zadejte:** Nová funkce  
**Kategorie služby:** Služba Azure AD  
**Schopnost produktu:** Ověřování uživatelů
 
Zaškrtávací políčko **zůstat** přihlášení na přihlašovací stránce služby Azure AD bylo nahrazeno novou výzvou, která se zobrazí po úspěšném ověření. 

Pokud odpovíte **Ano** na tuto výzvu, služba vám nabídne trvalou obnovovací token. Toto chování se shoduje s tím, jak jste zaškrtli políčko **zůstat přihlášeni** ve starém prostředí. Případě federovaných tenantů tato výzva zobrazí po úspěšně ověřit federované službě.

Další informace najdete v tématu [méně výzev k přihlášení: nové prostředí "zůstat přihlášené" pro Azure AD je ve verzi Preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/). 

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Přidat konfiguraci tak, aby vyžadovala podmínky použití, které mají být rozbaleny před přijetím

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmínky použití  
**Schopnost produktu:** Dodržování
 
Možnost pro správce vyžaduje, aby jejich uživatelé rozbalili podmínky použití před přijetím podmínek.

Vyberte **zapnout** nebo **vypnout** , pokud chcete, aby uživatelé rozšířili podmínkami použití. Nastavení **zapnuto** vyžaduje, aby uživatelé před přijetím zobrazili podmínek použití.

Další informace najdete v tématu [funkce Azure AD terms of use (Preview)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---

### <a name="scoped-activation-for-eligible-role-assignments"></a>S vymezeným oborem aktivaci přiřazení oprávněné role

**Zadejte:** Nová funkce  
**Kategorie služby:** Privileged Identity Management  
**Schopnost produktu:** Privileged Identity Management
 
Vám pomůže s vymezeným oborem aktivace aktivovat přiřazení role oprávněné prostředků Azure s méně autonomie než původní výchozí přiřazení. Příkladem je, pokud je vám přiřazena jako vlastník předplatného ve vašem tenantovi. Díky aktivaci s vymezeným oborem můžete aktivovat role vlastníka až o pěti prostředků obsažených v rámci předplatného (například skupiny prostředků a virtuální počítače). Oborů s aktivací může omezit možnost provedení nežádoucí změny k důležitým prostředkům Azure.

Další informace najdete v tématu [co je Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure).
 
---
 
### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Nové federované aplikace v galerii aplikací Azure AD

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran

V prosinci 2017 jsme přidali podporu těchto nových aplikacích s federací do naší Galerie aplikací:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager, [EFI Digital prezentace](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [Image funguje](https://go.microsoft.com/fwlink/?linkid=863517), [mobi](https://go.microsoft.com/fwlink/?linkid=863521), [MobileIron integraci Azure AD](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [SAML SSO pro Bamboo, podle rezoluce GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [SSO pro BitBucket, a to pomocí](https://go.microsoft.com/fwlink/?linkid=863519)řešení GmbH, [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, Zenegy Integration pro Azure AD.

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 
 
---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Pracovní postupy schválení pro role adresáře Azure AD

**Zadejte:** Změněná funkce  
**Kategorie služby:** Privileged Identity Management  
**Schopnost produktu:** Privileged Identity Management
 
Pracovní postup schválení pro role adresáře Azure AD je obecně dostupná.

Pomocí pracovního postupu schvalování oprávnění role Správci můžete vyžadovat, aby členové role oprávněné žádosti o aktivaci role před použitím privilegovaných rolí. Více uživatelů a skupin může být delegovaná schválení odpovědnosti. Členové role oprávněné dostávat upozornění na dokončení schválení a jejich role není aktivní.

---
 
### <a name="pass-through-authentication-skype-for-business-support"></a>Předávací ověřování: Skype pro firmy podpory

**Zadejte:** Změněná funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů

Předávací ověřování nyní podporuje uživatelská přihlášení ke Skypu pro firmy klientské aplikace, které podporují moderní ověřování, který obsahuje online a hybridní topologie. 

Další informace najdete v tématu [topologie Skypu pro firmy podporované moderním ověřováním](https://technet.microsoft.com/library/mt803262.aspx).
 
---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Aktualizace Azure AD Privileged Identity Management pro Azure RBAC (preview)

**Zadejte:** Změněná funkce  
**Kategorie služby:** Privileged Identity Management  
**Schopnost produktu:** Privileged Identity Management
 
Při aktualizaci ve verzi public preview z Azure AD Privileged Identity Management (PIM) pro řízení přístupu Azure Role-Based (RBAC) teď můžete:

* Pomocí funkce Just Enough Administration.
* K aktivaci vyžadovat schválení role prostředků.
* Plánovat budoucí aktivace role, která vyžaduje schválení pro Azure AD a role Azure RBAC.
 
Další informace najdete v tématu [Privileged Identity Management pro prostředky Azure (Preview)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---
 
## <a name="november-2017"></a>. Listopadu 2017
 
### <a name="access-control-service-retirement"></a>Vyřazení služby Řízení přístupu

**Zadejte:** Plánování změn  
**Kategorie služby:** Služba Access Control  
**Schopnost produktu:** Služba Access Control 

V pozdní 2018 se vyřadí z Azure Active Directory Access Control (označované také jako služba Access Control). V příštích týdnech vám poskytneme Další informace, které obsahuje podrobný plán a pokyny k migraci vysoké úrovně. Komentáře na této stránce můžete nechat jakékoli dotazy týkající se služby Access Control a člen týmu odpoví.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Omezit přístup z prohlížeče pro Intune Managed Browser 

**Zadejte:** Plánování změn  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity a ochrana

Můžete omezit přístup z prohlížeče k Office 365 a dalším aplikacím Azure AD připojení cloudu s využitím jako schválenou aplikaci Intune Managed Browser. 

Pro podmíněný přístup na základě aplikace teď můžete nakonfigurovat tuto podmínku:

**Klientské aplikace:** Prohlížeee

**Jaký je účinek změny?**

Použijete-li tento stav je v současné době zablokován přístup. Pokud ve verzi preview je k dispozici, veškerý přístup bude vyžadovat použití aplikace spravovaného prohlížeče. 

Vyhledejte tuto funkci a další informace najdete v nadcházející blogy a vydání poznámek. 

Další informace najdete v tématu [podmíněný přístup ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nové schválené klientské aplikace pro podmíněný přístup na základě aplikace Azure AD

**Zadejte:** Plánování změn  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity a ochrana

V seznamu [schválených klientských aplikací](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)jsou následující aplikace:

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

Další informace naleznete v tématu:

- [Požadavek na schválenou aplikaci klienta](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Podmíněný přístup na základě aplikace Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Podmínky použití podpora více jazyků

**Zadejte:** Nová funkce    
**Kategorie služby:** Podmínky použití  
**Schopnost produktu:** Dodržování

Správci teď můžou vytvářet nové podmínky použití, které obsahují více dokumentů PDF. Můžete označit tyto dokumenty PDF s odpovídající jazyk. Uživatelům se zobrazí soubor PDF s odpovídající jazyk podle jejích preferencí. Pokud není nalezena žádná shoda, zobrazí se výchozí jazyk.

---
 
### <a name="real-time-password-writeback-client-status"></a>Stav klienta zpětného zápisu hesla v reálném čase

**Zadejte:** Nová funkce  
**Kategorie služby:** Samoobslužné resetování hesla  
**Schopnost produktu:** Ověřování uživatelů

Nyní můžete zkontrolovat stav vaší klientovi zpětného zápisu hesla v místním. Tato možnost je k dispozici v části **Místní integrace** stránky pro [resetování hesla](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) . 

Pokud dojde k problémům s připojením k vašemu klientovi zpětného zápisu v místním, se zobrazí chybová zpráva, která vám nabízí:

- Informace o Proč se nejde připojit k vašemu klientovi zpětného zápisu místní.
- Odkaz na dokumentaci, která vám pomůže při řešení problému. 

Další informace najdete v tématu věnovaném [místní integraci](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration).

---

### <a name="azure-ad-app-based-conditional-access"></a>Podmíněný přístup na základě aplikace Azure AD 
 
**Zadejte:** Nová funkce  
**Kategorie služby:** Služba Azure AD  
**Schopnost produktu:** Zabezpečení identity a ochrana

Teď můžete omezit přístup k Office 365 a dalším cloudovým aplikacím připojeným k Azure AD na [schválené klientské aplikace](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview) , které podporují zásady ochrany aplikací Intune pomocí [podmíněného přístupu založeného na aplikaci Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Zásady ochrany aplikací Intune se používají ke konfiguraci a ochraně dat společnosti na těchto klientských aplikací.

Kombinací [aplikací na základě](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) zásad podmíněného přístupu [na základě zařízení](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) získáte flexibilitu při ochraně dat pro osobní zařízení a zařízení společnosti.

Pro použití podmíněného přístupu na základě aplikace jsou teď dostupné následující podmínky a ovládací prvky:

**Podporovaná podmínka platformy**

- iOS
- Android

**Podmínky klientských aplikací**

- Mobilní aplikace a desktopoví klienti

**Řízení přístupu**

- Vyžaduje se klientem schválená aplikace.

Další informace najdete v tématu [podmíněný přístup založený na aplikaci Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access).
 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Správa zařízení Azure AD na webu Azure Portal

**Zadejte:** Nová funkce  
**Kategorie služby:** Registrace a Správa zařízení  
**Schopnost produktu:** Zabezpečení identity a ochrana

Nyní můžete najít všechna zařízení připojená ke službě Azure AD a činnosti týkající se zařízení na jednom místě. Je nové prostředí pro správu ke správě identit zařízení a nastavení na portálu Azure portal. V této verzi můžete:

- Zobrazí všechna zařízení, která jsou k dispozici pro podmíněný přístup ve službě Azure AD.
- Zobrazit vlastnosti, které obsahují vaše hybridní Azure zařízení připojených k doméně AD.
- Vyhledání klíče Bitlockeru pro zařízení připojená k AD Azure, spravovat zařízení s Intune a další.
- Správa nastavení zařízení: služby Azure AD.

Další informace najdete v tématu [Správa zařízení pomocí Azure Portal](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Podpora macOS jako platformy zařízení pro podmíněný přístup Azure AD 

**Zadejte:** Nová funkce    
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity a ochrana 

V rámci zásad podmíněného přístupu Azure AD teď můžete zahrnout (nebo vyloučit) macOS jako podmínku pro platformu zařízení. Přidání macOS na platformách podporovaných zařízení vám umožňuje:

- **Registrace a Správa zařízení macOS pomocí Intune** Podobně jako jiné platformy jako iOS a Android, aplikace portálu společnosti je k dispozici pro macOS provést jednotné přihlášení. Nová aplikace portál společnosti pro macOS můžete použít k registraci zařízení v Intune a zaregistrovat ji pomocí služby Azure AD.
- **Zajistěte, aby zařízení macOS vyhovovala zásadám dodržování předpisů vaší organizace definovaným v Intune.** V Intune na portálu Azure portal můžete teď můžete nastavit zásady dodržování předpisů pro zařízení s macOS. 
- **Omezte přístup k aplikacím ve službě Azure AD tak, aby splňovala jenom vyhovující zařízení macOS.** Při vytváření zásad podmíněného přístupu se macOS jako samostatná možnost platformy zařízení. Nyní můžete vytvářet zásady podmíněného přístupu specifické pro macOS pro cílovou aplikaci určenou v Azure.

Další informace naleznete v tématu:

- [Vytvoření zásady dodržování předpisů pro zařízení s macOS pomocí Intune](https://aka.ms/macoscompliancepolicy)
- [Podmíněný přístup ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)
 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Sítě rozšíření zásad serveru pro ověřování Azure Multi-Factor Authentication 

**Zadejte:** Nová funkce    
**Kategorie služby:**  Multi-Factor Authentication  
**Schopnost produktu:** Ověřování uživatelů

Rozšíření serveru Network Policy Server pro ověřování Azure Multi-Factor Authentication cloudové možnosti ověřování službou Multi-Factor Authentication přidá do vaší infrastruktury ověřování pomocí svých stávajících serverech. Rozšíření serveru Network Policy Server můžete přidat telefonní hovor, textová zpráva nebo ověřovacích telefonních aplikací do vašeho existujícího toku ověřování. Není nutné instalovat, konfigurovat a Udržovat nové servery. 

Toto rozšíření byla vytvořena pro organizace, které chcete chránit připojení virtuální privátní sítě bez nutnosti nasazovat Azure Multi-Factor Authentication Server. Serveru Network Policy Server, který rozšíření funguje jako adaptér mezi RADIUS a cloudové ověřování Azure Multi-Factor Authentication, aby zajišťoval druhý faktor ověřování pro federované nebo synchronizovaných uživatelů.

Další informace najdete v tématu [integrace stávající infrastruktury serveru NPS (Network Policy Server) s Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension).
 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Obnovení nebo trvale odebrat odstraněného uživatele

**Zadejte:** Nová funkce    
**Kategorie služby:** Správa uživatelů  
**Schopnost produktu:** Službě 

V Centru pro správu Azure AD teď můžete:

- Obnovení odstraněného uživatele. 
- Trvale odstraníte uživatele.

**Vyzkoušejte:**

1. V centru pro správu Azure AD vyberte [Všichni uživatelé](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) v části **Spravovat** . 

2. V seznamu **Zobrazit** vyberte **Nedávno odstraněné uživatele**. 

3. Vyberte jeden nebo více naposledy odstranění uživatelé a potom je obnovit buď nebo je trvale odstranit.
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nové schválené klientské aplikace pro podmíněný přístup na základě aplikace Azure AD
 
**Zadejte:** Změněná funkce  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity a ochrana

Do seznamu [schválených klientských aplikací](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)byly přidány následující aplikace:

- Aplikace Microsoft Planner
- Azure Information Protection 

Další informace naleznete v tématu:

- [Požadavek na schválenou aplikaci klienta](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Podmíněný přístup na základě aplikace Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Použití "nebo" mezi ovládacími prvky v zásadách podmíněného přístupu 

**Zadejte:** Změněná funkce    
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity a ochrana
 
Nyní můžete použít "nebo" (pro řízení podmíněného přístupu vyžadovat jeden z vybraných ovládacích prvků). Tato funkce slouží k vytváření zásad s "nebo" mezi řízení přístupu. Například můžete použít tuto funkci můžete vytvořit zásadu, která vyžaduje uživatele k přihlášení pomocí služby Multi-Factor Authentication "nebo" na vyhovující zařízení.

Další informace najdete v tématu věnovaném [ovládacím prvkům v Azure AD podmíněný přístup](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls).
 
---

### <a name="aggregation-of-real-time-risk-detections"></a>Agregace zjišťování rizik v reálném čase

**Zadejte:** Změněná funkce    
**Kategorie služby:** Ochrana identity  
**Schopnost produktu:** Zabezpečení identity a ochrana

V Azure AD Identity Protection se teď všechny detekce rizik v reálném čase, které pocházejí ze stejné IP adresy v daném dni, agreguje pro každý typ detekce rizik. Tato změna omezuje množství zjištěných detekcí rizik bez změny zabezpečení uživatele.

Základní detekce v reálném čase funguje pokaždé, když uživatel přihlásí. Pokud máte zásady rizik přihlašování zabezpečení nastavit tak, aby ověřování službou Multi-Factor Authentication nebo blokování přístupu, se stále aktivují během každé rizikových přihlášení.
 
---
 
## <a name="october-2017"></a>Říjen 2017

### <a name="deprecate-azure-ad-reports"></a>Vyřazení sestav Azure AD

**Zadejte:** Plánování změn  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Správa životního cyklu identit  

Na webu Azure portal vám nabízí:

- Nová konzola pro správu Azure AD.
- Nová rozhraní API pro sestavy aktivit a zabezpečení.
 
Díky těmto novým funkcím byly dostupné sestavy rozhraní API v rámci koncový bod/Reports jenom do 10. prosince 2017. 

---

### <a name="automatic-sign-in-field-detection"></a>Zjišťování pole Automatické přihlášení

**Zadejte:** Určí   
**Kategorie služby:** Moje aplikace  
**Schopnost produktu:** Jednotné přihlašování  

Azure AD podporuje zjišťování pole automatické přihlašování pro aplikace, které vykreslují pole HTML uživatelské jméno a heslo. Tyto kroky jsou popsány v tématu [Automatické zachytávání přihlašovacích polí pro aplikaci](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-password-single-sign-on-non-gallery-applications-problems#manually-capture-sign-in-fields-for-an-app). Tuto možnost můžete najít přidáním aplikace *mimo galerii* na stránce **podnikové aplikace** v [Azure Portal](https://aad.portal.azure.com). Kromě toho můžete v této nové aplikaci nakonfigurovat režim **jednotného přihlašování** na **jednotné přihlašování založené na heslech**, zadat adresu URL webu a pak stránku uložit.
 
Kvůli problému služba byla tato funkce dočasně zakázané. Tento problém byl vyřešen a zjišťování pole Automatické přihlášení je opět k dispozici.

---

### <a name="new-multi-factor-authentication-features"></a>Nové funkce služby Multi-Factor Authentication

**Zadejte:** Nová funkce  
**Kategorie služby:** Multi-Factor Authentication  
**Schopnost produktu:** Zabezpečení identity a ochrana  

Vícefaktorové ověřování (MFA) je důležitou součástí ochrany vaší organizace. Chcete-li více adaptive přihlašovací údaje a prostředí pohodlnější, byly přidány následující funkce: 

- Výsledky výzvu služby Multi-Factor Authentication jsou integrované přímo do sestavy Azure AD přihlášení, která zahrnuje programový přístup k výsledkům vícefaktorové ověřování.
- Konfigurace MFA je těsněji integrované do služby Azure AD konfigurace prostředí na webu Azure Portal.

V této verzi public preview jsou nedílnou součást prostředí základní konfigurace Azure AD MFA správu a vytváření sestav. Nyní můžete spravovat funkce portálu správy vícefaktorové ověřování v rámci prostředí Azure AD.

Další informace najdete v tématu [referenční informace pro generování sestav MFA v Azure Portal](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa). 

---

### <a name="terms-of-use"></a>Podmínky použití

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmínky použití  
**Schopnost produktu:** Dodržování  

Můžete použít Azure AD podmínek použití zobrazíte informace, jako je příslušná právní omezení pro zákonné požadavky na uživatele.

Podmínky použití služby Azure AD můžete použít v následujících scénářích:

- Obecné podmínky použití pro všechny uživatele ve vaší organizaci
- Konkrétní podmínky použití založené na atributech uživatelů (například lékařů vs. sestry) nebo domácích a mezinárodních zaměstnanců, provádí dynamické skupiny
- Konkrétní podmínky použití pro přístup k vysoce účinné obchodní aplikace, jako je Salesforce

Další informace najdete v tématu věnovaném [podmínkám použití Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="enhancements-to-privileged-identity-management"></a>Vylepšení Privileged Identity Management

**Zadejte:** Nová funkce  
**Kategorie služby:** Privileged Identity Management  
**Schopnost produktu:** Privileged Identity Management  

S Azure AD Privileged Identity Management můžete spravovat, řídit a monitorovat přístup k prostředkům Azure (preview) v rámci vaší organizace:

- Předplatná
- Skupiny prostředků
- Virtuální počítače 

Všechny prostředky v rámci webu Azure portal, které používají funkci Azure RBAC můžete využít výhod zabezpečení a možnosti správy životního cyklu, které Azure AD Privileged Identity Management může nabídnout.

Další informace najdete v tématu [Privileged Identity Management pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

### <a name="access-reviews"></a>Kontroly přístupu

**Zadejte:** Nová funkce  
**Kategorie služby:** Kontroly přístupu  
**Schopnost produktu:** Dodržování  

Organizace můžete efektivně spravovat členství ve skupinách a přístup k podnikovým aplikacím použít kontroly přístupu (preview): 

- Můžete znovu certifikovat přístup uživatelů typu host pomocí kontrol jejich přístupu k aplikacím a jejich členství ve skupinách. Recenzenti můžete efektivně rozhodnout, zda povolit hosté nadále přístup na základě informací poskytnutých kontroly přístupu.
- Pomocí kontrol přístupu je možné provést opětovnou certifikaci přístupu k aplikacím a členství ve skupinách i pro zaměstnance.

Kontroly přístupu lze shromažďovat v programech sledujících dodržování předpisů a přístup k citlivým aplikacím v rámci organizace.

Další informace najdete v tématu kontroly [přístupu Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview).

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Skrytí aplikace třetí strany z Moje aplikace a Spouštěči aplikací Office 365

**Zadejte:** Nová funkce  
**Kategorie služby:** Moje aplikace  
**Schopnost produktu:** Jednotné přihlašování  

Teď můžete lépe spravovat aplikace, které se zobrazují na portálech uživatelů, prostřednictvím nové vlastnosti **Skrýt aplikaci** . Můžete skrýt aplikacemi a pomáhají v případech, kdy dlaždic aplikací zobrazí pro back endovým službám nebo duplicitní dlaždice a Spouštěč nepořádku uživatelé aplikaci oken. Přepínač je v části **vlastnosti** aplikace třetí strany a je označen jako **viditelný pro uživatele?** Také můžete skrýt aplikace prostřednictvím kódu programu přes PowerShell. 

Další informace najdete v tématu [skrytí aplikace třetí strany od uživatele ze zkušeností uživatelů v Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app). 


**Co je k dispozici?**

 Jako součást přechodu na Nová konzola pro správu, dvě nová rozhraní API pro načítání aktivit Azure AD jsou k dispozici protokoly. Nová sada rozhraní API poskytuje širší filtrování a řazení funkce kromě toho, že bohatší auditu a aktivit přihlašování. Data dříve dostupná prostřednictvím sestav zabezpečení jsou teď přístupná prostřednictvím rozhraní API detekce rizik ochrany identity v Microsoft Graph.


## <a name="september-2017"></a>Září 2017

### <a name="hotfix-for-identity-manager"></a>Oprava hotfix pro Identity Manager

**Zadejte:** Změněná funkce  
**Kategorie služby:** Správce identit  
**Schopnost produktu:** Správa životního cyklu identit  

Balíček kumulativní oprava hotfix (build 4.4.1642.0) je k dispozici od 25. září 2017, pro Identity Manager 2016 Service Pack 1. Tento balíček Shrnutí:

- Řeší problémy a přidává vylepšení.
- Je kumulativní aktualizace, která nahradí všechny aktualizace Identity Manager 2016 Service Pack 1 až po sestavení 4.4.1459.0 pro Identity Manager 2016. 
- Je potřeba mít sestavení 4.4.1302.0 Identity Manager 2016. 

Další informace najdete v tématu [kumulativní balíček oprav hotfix (Build 4.4.1642.0) pro nástroj Identity Manager 2016 Service Pack 1](https://support.microsoft.com/help/4021562). 

---
