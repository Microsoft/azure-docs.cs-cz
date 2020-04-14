---
title: Archivujete co je nového ve službě Azure Active Directory? | Dokumentace Microsoftu
description: Poznámky k nové verzi v části Přehled této sady obsahu obsahují 6 měsíců aktivity. Po 6 měsících jsou položky odstraněny z hlavního článku a vloženy do tohoto článku archivu.
services: active-directory
author: msmimart
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25191951472e30492606ad97c440a13359c8ef24
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253165"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Archivujete co je nového ve službě Azure Active Directory?

Primární [Článek Co je nového ve službě Azure Active Directory?](whats-new.md) obsahuje aktualizace za posledních šest měsíců, zatímco tento článek obsahuje všechny starší informace.

Co je nového ve službě Azure Active Directory? Poznámky k verzi poskytují informace o:

- Nejnovější verze
- Známé problémy
- Opravy chyb
- Zastaralé funkce
- Plány změn

---

## <a name="september-2019"></a>Září 2019

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Plán změn: Vyřazení balíčků obsahu Power BI

**Typ:** Plán změn  
**Kategorie služeb:** Vykazování  
**Možnost i možnosti produktu:** Monitorování & reportingu

října 2019 začne Power BI odsuzovat všechny balíčky obsahu, včetně balíčku obsahu Azure AD Power BI. Jako alternativu k tomuto balíčku obsahu můžete pomocí sešitů Azure AD získat přehled o vašich službách souvisejících s Azure AD. Přicházejí další sešity, včetně sešitů o zásadách podmíněného přístupu v režimu pouze pro sestavu, přehledů založených na souhlasu aplikací a dalších.

Další informace o sešitech najdete v [tématu Jak používat sešity Azure Monitoru pro sestavy Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks). Další informace o vyřazení balíčků obsahu najdete v [příspěvku blogu Oznamující šablony Power BI.](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/)

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>Můj profil přejmenovává a integruje se se stránkou účtu Microsoft Office

**Typ:** Plán změn  
**Kategorie služeb:** Můj profil/účet  
**Možnost i možnosti produktu:** Spolupráci

Počínaje říjnem se prostředí Můj profil stane mým účtem. V rámci této změny se všude, kde je aktuálně uvedeno, změní **můj profil** na **můj účet**. Kromě změny pojmenování a některých vylepšení návrhu nabídne aktualizované prostředí další integraci se stránkou účtu Microsoft Office. Konkrétně budete mít přístup k instalacím a předplatným Office na stránce **Účet přehledu** spolu s předvolbami kontaktů souvisejících s Office na stránce **Ochrana osobních údajů.**

Další informace o prostředí Můj profil (náhled) najdete v tématu [Přehled portálu Můj profil (preview).](https://docs.microsoft.com/azure/active-directory/user-help/myprofile-portal-overview)

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Hromadná správa skupin a členů pomocí souborů CSV na portálu Azure AD (Public Preview)

**Typ:** Nová funkce  
**Kategorie služeb:** Správa skupiny  
**Možnost i možnosti produktu:** Spolupráci

S potěšením oznamujeme dostupnost veřejné verze preview možností hromadné správy skupin na portálu Azure AD. Teď můžete ke správě skupin a seznamů členů použít soubor CSV a portál Azure AD, včetně:

- Přidání nebo odebrání členů ze skupiny.

- Stažení seznamu skupin z adresáře.

- Stažení seznamu členů skupiny pro určitou skupinu.

Další informace naleznete [v tématech Hromadné přidávání členů](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members), Hromadné odebrání [členů](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members), Seznam [členů hromadného stahování](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)a Seznam skupin [hromadného stahování](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download).

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>Dynamický souhlas je nyní podporován prostřednictvím nového koncového bodu souhlasu správce.

**Typ:** Nová funkce  
**Kategorie služeb:** Ověřování (přihlášení)  
**Možnost i možnosti produktu:** Ověřování uživatelů

Vytvořili jsme nový koncový bod souhlasu správce pro podporu dynamického souhlasu, což je užitečné pro aplikace, které chtějí používat model dynamického souhlasu na platformě Microsoft Identity.

Další informace o použití tohoto nového koncového bodu naleznete [v tématu Použití koncového bodu souhlasu správce](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD – září 2019

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnosti produktu:** Integrace třetí strany

V září 2019 jsme do galerie aplikací přidali těchto 29 nových aplikací s podporou Federace:

[ScheduleLook](https://schedulelook.bbsonlineservices.net/), [MS Azure SSO Access for&trade; Ethidex Compliance Office – jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial), [portál iServer](https://docs.microsoft.com/azure/active-directory/saas-apps/iserver-portal-tutorial), [SKYSITE](https://docs.microsoft.com/azure/active-directory/saas-apps/skysite-tutorial), [Concur Travel and Expense](https://docs.microsoft.com/azure/active-directory/saas-apps/concur-travel-and-expense-tutorial), [WorkBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/workboard-tutorial), `https://apps.yeeflow.com/`ARC [Facilities](https://docs.microsoft.com/azure/active-directory/saas-apps/arc-facilities-tutorial), [Luware Stratus Team](https://stratus.emea.luware.cloud/login), Wide [Ideas](https://wideideas.online/wideideas/), [Prisma Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial), [JDLT Client Hub](https://clients.jdlt.co.uk/login), [RENRAKU](https://docs.microsoft.com/azure/active-directory/saas-apps/renraku-tutorial), [SealPath Secure Browser](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive), [Prisma Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial), `https://app.penneo.com/`, `https://app.testhtm.com/settings/email-integration`, [Cintoo Cloud](https://aec.cintoo.com/login), [Whitesource](https://docs.microsoft.com/azure/active-directory/saas-apps/whitesource-tutorial), [Hosted Heritage Online SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-heritage-online-sso-tutorial), [IDC](https://docs.microsoft.com/azure/active-directory/saas-apps/idc-tutorial), [CakeHR,](https://docs.microsoft.com/azure/active-directory/saas-apps/cakehr-tutorial) [BIS,](https://docs.microsoft.com/azure/active-directory/saas-apps/bis-tutorial) [Coo Kai Team Build](https://ms-contacts.coo-kai.jp/), [Sonarqube](https://docs.microsoft.com/azure/active-directory/saas-apps/sonarqube-tutorial), Adobe [Identity Management](https://docs.microsoft.com/azure/active-directory/saas-apps/adobe-identity-management-tutorial), Discovery Benefits [SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/discovery-benefits-sso-tutorial), [Amelio](https://app.amelio.co/),`https://itask.yipinapp.com/`

Další informace o aplikacích najdete v tématu [Integrace aplikací SaaS se službou Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete [v tématu Seznam aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-global-reader-role"></a>Nová role globální čtečky Azure AD

**Typ:** Nová funkce  
**Kategorie služeb:** RBAC  
**Možnost i možnosti produktu:** Řízení přístupu

září 2019 začneme zavádět novou roli Služby Azure Active Directory (AD) nazvanou Globální čtečka. Toto zavedení začne s výrobou a globálními cloudovými zákazníky (GCC), kteří skončí po celém světě v říjnu.

Role Globální čtenář je jen pro čtení protějšek globálního správce. Uživatelé v této roli mohou číst informace o nastavení a správě ve službách Microsoft 365, ale nemohou přijímat akce správy. Vytvořili jsme roli Globální čtenář, která pomáhá snížit počet globálních správců ve vaší organizaci. Vzhledem k tomu, že účty globálního správce jsou výkonné a náchylné k útoku, doporučujeme, abyste měli méně než pět globálních správců. Doporučujeme použít roli Globální čtenář pro plánování, audity nebo vyšetřování. Doporučujeme také použít roli globální čtečky v kombinaci s dalšími rolemi omezeného správce, jako je správce serveru Exchange, abyste mohli pracovat bez nutnosti role globálního správce.

Role Globální čtenářfunguje s novým Centrem pro správu Microsoft 365, Centrem pro správu Exchange, Centrem pro správu teams, Centrem zabezpečení, Centrem dodržování předpisů, Centrem pro správu Azure AD a Centrem správy pro správu zařízení.

>[!NOTE]
> Na začátku veřejné verze Preview nebude role Globální čtenář fungovat s: SharePoint, Správa privilegovaného přístupu, Customer Lockbox, popisky citlivosti, Životní cyklus týmů, Teams Reporting & Call Analytics, Správa zařízení IP teams a Katalog aplikací Teams. 

Další informace naleznete [v tématu Oprávnění rolí správce ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Přístup k místnímu reportovému serveru z mobilní aplikace Power BI pomocí proxy aplikace Azure Active Directory

**Typ:** Nová funkce  
**Kategorie služeb:** Proxy aplikace  
**Možnost i možnosti produktu:** Řízení přístupu

Nová integrace mezi mobilní aplikací Power BI a proxy aplikací Azure AD umožňuje bezpečné přihlášení k mobilní aplikaci Power BI a zobrazení všech sestav vaší organizace hostovaných na místním serveru sestav Power BI.

Informace o aplikaci Power BI Mobile, včetně toho, kde si ji stáhnout, najdete na [webu Power BI](https://powerbi.microsoft.com/mobile/). Další informace o tom, jak nastavit mobilní aplikaci Power BI pomocí proxy aplikací Azure AD, najdete v [tématu Povolení vzdáleného přístupu k Power BI Mobile pomocí proxy aplikací Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-power-bi).

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>K dispozici je nová verze modulu PowerShell AzureADPreview

**Typ:** Změněná funkce  
**Kategorie služeb:** Další  
**Možnost i možnosti produktu:** Adresář

Do modulu AzureADPreview byly přidány nové rutiny, které pomáhají definovat a přiřazovat vlastní role ve službě Azure AD, včetně:

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Nová verze Azure AD Connect

**Typ:** Změněná funkce  
**Kategorie služeb:** Další  
**Možnost i možnosti produktu:** Adresář

Vydali jsme aktualizovanou verzi Služby Azure AD Connect pro zákazníky s automatickým upgradem. Tato nová verze obsahuje několik nových funkcí, vylepšení a oprav chyb. Další informace o této nové verzi najdete [v tématu Azure AD Connect: Historie verzí](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#14250).

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Server Azure Multi-Factor Authentication (MFA), verze 8.0.2 je teď dostupná

**Typ:** Dlouhodobého  
**Kategorie služeb:** Mfa  
**Možnost i možnosti produktu:** Ochrana & zabezpečení identity

Pokud jste stávající zákazník, který aktivoval server MFA před 1. V této nové verzi:

- Opraven problém, takže když synchronizace Azure AD změní uživatele z Depozitní na Povoleno, odešle se uživateli e-mail.

- Byl opraven problém, takže zákazníci mohou úspěšně upgradovat a zároveň nadále používat funkci Značky.

- Přidán kód země Kosovo (+383).

- Do souboru MultiFactorAuthSvc.log byl přidán jednorázový protokol ování auditu.

- Zlepšený výkon sady SDK webové služby.

- Opraveny další drobné chyby.

července 2019 společnost Microsoft přestala nabízet server MFA pro nová nasazení. Noví zákazníci, kteří vyžadují vícefaktorové ověřování, by měli používat vícefaktorové ověřování Azure na cloudu. Další informace najdete [v tématu Plánování cloudového nasazení Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---

## <a name="august-2019"></a>Srpen 2019

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>Rozšířené vyhledávání, filtrování a řazení skupin je dostupné na portálu Azure AD (Public Preview).

**Typ:** Nová funkce  
**Kategorie služeb:** Správa skupiny  
**Možnost i možnosti produktu:** Spolupráci

S potěšením oznamujeme dostupnost veřejné verze preview rozšířených prostředí souvisejících se skupinami na portálu Azure AD. Tato vylepšení vám pomohou lépe spravovat skupiny a seznamy členů tím, že poskytují:

- Rozšířené možnosti vyhledávání, jako je například vyhledávání podřetězců v seznamech skupin.
- Rozšířené možnosti filtrování a řazení v seznamech členů a vlastníků.
- Nové možnosti vyhledávání pro seznamy členů a vlastníků.
- Přesnější počty skupin pro velké skupiny.

Další informace najdete [v tématu Správa skupin na webu Azure Portal](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Nové vlastní role jsou k dispozici pro správu registrace aplikací (Public Preview)

**Typ:** Nová funkce  
**Kategorie služeb:** RBAC  
**Možnost i možnosti produktu:** Řízení přístupu

Vlastní role (dostupné s předplatným Azure AD P1 nebo P2) vám teď můžou pomoct s jemně odstupňovaným přístupem tím, že vám umožní vytvářet definice rolí s konkrétními oprávněními a pak tyto role přiřadit konkrétním prostředkům. V současné době vytvoříte vlastní role pomocí oprávnění pro správu registrací aplikací a následnépřiřazení role konkrétní aplikaci. Další informace o vlastních rolích najdete [v tématu Vlastní role správce ve službě Azure Active Directory (preview).](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview)

Pokud potřebujete další podporovaná oprávnění nebo prostředky, které momentálně nevidíte, můžete poslat zpětnou vazbu na náš [web pro zpětnou vazbu Azure](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) a my váš požadavek přidáme do naší aktualizace.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>Nové protokoly zřizování vám můžou pomoct sledovat a řešit potíže s nasazením zřizování aplikací (Public Preview)

**Typ:** Nová funkce  
**Kategorie služeb:** Zřizování aplikací  
**Možnost i možnosti produktu:** Správa životního cyklu identity

K dispozici jsou nové protokoly zřizování, které vám pomohou sledovat a řešit potíže s nasazením zřizování uživatelů a skupin. Tyto nové soubory protokolu obsahují informace o:

- Jaké skupiny byly úspěšně vytvořeny v [servicenow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)
- Jaké role byly importovány z [Amazon Web Services (AWS)](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial#configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws)
- Co zaměstnanci nebyli importováni z [Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)

Další informace najdete [v tématu Zřizování sestav na portálu Azure Active Directory (preview).](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Nové sestavy zabezpečení pro všechny správce Azure AD (obecná dostupnost)

**Typ:** Nová funkce  
**Kategorie služeb:** Ochrana identity  
**Možnost i možnosti produktu:** Ochrana & zabezpečení identity

Ve výchozím nastavení budou mít všichni správci Azure AD brzy přístup k moderním sestavám zabezpečení v rámci Azure AD. Až do konce září budete moci použít banner v horní části moderních bezpečnostních zpráv, abyste se vrátili ke starým zprávám.

Moderní zprávy o zabezpečení budou poskytovat další funkce ze starších verzí, včetně:

- Pokročilé filtrování a řazení
- Hromadné akce, jako je například odmítnutí rizika uživatele
- Potvrzení ohrožených nebo bezpečných subjektů
- Rizikový stav, zahrnující: V ohrožení, propuštěn, opravena a potvrzeno ohrožena
- Nové detekce související s rizikem (k dispozici předplatitelům Azure AD Premium)

Další informace naleznete [v tématu Risky users](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users), [Risky sign-ins](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins)a [Risk Detections](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risk-detections).

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>Spravovaná identita přiřazená uživatelem je dostupná pro virtuální počítače a škálovací sady virtuálních počítačů (obecná dostupnost)

**Typ:** Nová funkce  
**Kategorie služeb:** Spravované identity pro prostředky Azure  
**Možnost i možnosti produktu:** Vývojářské prostředí

Uživatelem přiřazené spravované identity jsou teď obecně dostupné pro virtuální počítače a škálovací sady virtuálních počítačů. V rámci toho Azure můžete vytvořit identitu v tenantovi Azure AD, který je důvěryhodný podle předplatného v použití a lze přiřadit k jedné nebo více instancí služby Azure. Další informace o spravovaných identitách přiřazených uživatelem najdete v tématu [Co je spravované identity pro prostředky Azure?](https://aka.ms/azuremanagedidentity).

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>Uživatelé mohou resetovat svá hesla pomocí mobilní aplikace nebo hardwarového tokenu (Obecná dostupnost)

**Typ:** Změněná funkce  
**Kategorie služeb:** Samoobslužné resetování hesla  
**Možnost i možnosti produktu:** Ověřování uživatelů

Uživatelé, kteří zaregistrovali mobilní aplikaci ve vaší organizaci, si teď můžou resetovat vlastní heslo schválením oznámení z aplikace Microsoft Authenticator nebo zadáním kódu ze svého mobilního aplikace nebo hardwarového tokenu.

Další informace najdete v tématu [Jak to funguje: Azure AD samoobslužné heslo resetování](https://aka.ms/authappsspr). Další informace o uživatelském prostředí naleznete v tématu [Resetování vlastního přehledu pracovních nebo školních hesel](https://docs.microsoft.com/azure/active-directory/user-help/user-help-password-reset-overview).

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET ignoruje MSAL.NET sdílené mezipaměti pro scénáře jménem

**Typ:** Dlouhodobého  
**Kategorie služeb:** Ověřování (přihlášení)  
**Možnost i možnosti produktu:** Ověřování uživatelů

Počínaje azure ad ověřování knihovny (ADAL.NET) verze 5.0.0-preview, vývojáři aplikací musí [serializovat jednu mezipaměť na účet pro webové aplikace a webová rozhraní API](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api). V opačném případě některé scénáře pomocí [on-behalf-of toku](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow), spolu s některé konkrétní případy použití `UserAssertion`, může mít za následek zvýšení oprávnění. Chcete-li se této chybě zabezpečení vyhnout, ADAL.NET nyní ignoruje knihovnu ověřování společnosti Microsoft pro sdílenou mezipaměť Dotnet (MSAL.NET) pro scénáře.

Další informace o tomto problému naleznete v [tématu Azure Active Directory Authentication Library Elevation of Privilege Vulnerability](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD – srpen 2019

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnosti produktu:** Integrace třetí strany

V srpnu 2019 jsme do galerie aplikací přidali těchto 26 nových aplikací s podporou Federace:

[Občanská platforma](https://docs.microsoft.com/azure/active-directory/saas-apps/civic-platform-tutorial), [Amazon Business](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-business-tutorial), [ProNovos Ops Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-ops-manager-tutorial), [Cognidox](https://docs.microsoft.com/azure/active-directory/saas-apps/cognidox-tutorial), [Viareport Inativ Portal (Evropa)](https://docs.microsoft.com/azure/active-directory/saas-apps/viareports-inativ-portal-europe-tutorial), [Azure Databricks](https://azure.microsoft.com/services/databricks), [Robin](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial), [Academy Attendance](https://docs.microsoft.com/azure/active-directory/saas-apps/academy-attendance-tutorial), [Priority Matrix](https://sync.appfluence.com/pmwebng/), [Cousto MySpace](https://cousto.platformers.be/account/login), [Uploadcare](https://uploadcare.com/accounts/signup/), [Carbonite Endpoint Backup](https://docs.microsoft.com/azure/active-directory/saas-apps/carbonite-endpoint-backup-tutorial), [CPQ Sync by Cincom](https://docs.microsoft.com/azure/active-directory/saas-apps/cpqsync-by-cincom-tutorial), [Chargebee](https://docs.microsoft.com/azure/active-directory/saas-apps/chargebee-tutorial), [deliver.media&trade; Portal](https://portal.deliver.media), [Frontline Education](https://docs.microsoft.com/azure/active-directory/saas-apps/frontline-education-tutorial), [F5](https://www.f5.com/products/security/access-policy-manager), [stashcat AD connect](https://www.stashcat.com), [Blink](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial), [Vocoli](https://docs.microsoft.com/azure/active-directory/saas-apps/vocoli-tutorial), [ProNovos Analytics](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-analytics-tutorial), [Sigstr](https://docs.microsoft.com/azure/active-directory/saas-apps/sigstr-tutorial), [Darwinbox](https://docs.microsoft.com/azure/active-directory/saas-apps/darwinbox-tutorial), [Watch by Colors](https://docs.microsoft.com/azure/active-directory/saas-apps/watch-by-colors-tutorial), [Harness](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial), [EAB Navigate Strategic Care](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-strategic-care-tutorial)

Další informace o aplikacích najdete v tématu [Integrace aplikací SaaS se službou Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete [v tématu Seznam aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>K dispozici jsou nové verze modulů AzureAD PowerShell a AzureADPreview PowerShell

**Typ:** Změněná funkce  
**Kategorie služeb:** Další  
**Možnost i možnosti produktu:** Adresář

Nové aktualizace modulů PowerShell AzureAD a AzureAD Preview jsou dostupné:

- Nový `-Filter` parametr byl přidán `Get-AzureADDirectoryRole` do parametru v modulu AzureAD. Tento parametr vám pomůže filtrovat role adresáře vrácené rutinou.
- Do modulu AzureADPreview byly přidány nové rutiny, které pomáhají definovat a přiřazovat vlastní role ve službě Azure AD, včetně:

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>Vylepšení ui tvůrce pravidel dynamické skupiny na webu Azure Portal

**Typ:** Změněná funkce  
**Kategorie služeb:** Správa skupiny  
**Možnost i možnosti produktu:** Spolupráci

Provedli jsme některá vylepšení ui pro tvůrce dynamických pravidel skupiny, které jsou k dispozici na webu Azure Portal, abychom vám pomohli snadněji nastavit nové pravidlo nebo změnit stávající pravidla. Toto vylepšení návrhu umožňuje vytvářet pravidla s až pěti výrazy, nikoli pouze jedním. Aktualizovali jsme také seznam vlastností zařízení, abychom odstranili zastaralé vlastnosti zařízení.

Další informace naleznete v [tématu Správa dynamických pravidel členství](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership).

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>Nová oprávnění aplikace Microsoft Graph, která jsou k dispozici pro použití s recenzemi přístupu

**Typ:** Změněná funkce  
**Kategorie služeb:** Recenze přístupu  
**Možnost i možnosti produktu:** Zásady správného řízení identity

Zavedli jsme nové oprávnění aplikace Microsoft `AccessReview.ReadWrite.Membership`Graph , která aplikacím umožňuje automaticky vytvářet a načítat kontroly přístupu pro členství ve skupinách a přiřazení aplikací. Toto oprávnění lze použít naplánované úlohy nebo jako součást automatizace, aniž by bylo nutné přihlášený uživatelský kontext.

Další informace najdete v [tématu Příklad, jak vytvořit kontroly přístupu k Azure AD pomocí oprávnění aplikace Microsoft Graph s blogem PowerShellu](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241).

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Protokoly aktivit Azure AD jsou teď dostupné pro instance vládního cloudu v Azure Monitoru

**Typ:** Změněná funkce  
**Kategorie služeb:** Vykazování  
**Možnost i možnosti produktu:** Monitorování & reportingu

S nadšením oznamujeme, že protokoly aktivit Azure AD jsou teď dostupné pro instance vládního cloudu ve službě Azure Monitor. Teď můžete odesílat protokoly Azure AD do vašeho účtu úložiště nebo do centra událostí pro integraci s nástroji SIEM, jako [je Sumologic](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic), [Splunk](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-splunk)a [ArcSight](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-arcsight). 

Další informace o nastavení Azure Monitoru najdete [v tématu protokoly aktivit Azure AD v Azure Monitoru](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor#cost-considerations).

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Aktualizace uživatelů na nové rozšířené prostředí s bezpečnostními údaji

**Typ:** Změněná funkce  
**Kategorie služeb:**  Ověřování (přihlášení)   
**Možnost i možnosti produktu:** Ověřování uživatelů

25. září 2019 vypneme staré, nerozšířené bezpečnostní informace pro registraci a správu bezpečnostních informací uživatelů a pouze zapnutí [nové, vylepšené verze](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271). To znamená, že uživatelé již nebudou moci používat staré prostředí.

Další informace o rozšířeném prostředí s informacemi o zabezpečení naleznete v [naší dokumentaci k administrátoru](https://aka.ms/securityinfodocs) a [v uživatelské dokumentaci](https://aka.ms/securityinfoguide).

#### <a name="to-turn-on-this-new-experience-you-must"></a>Chcete-li tuto novou zkušenost zapnout, musíte:

1. Přihlaste se k portálu Azure jako globální správce nebo správce uživatelů.

2. Přejděte na **nastavení služby Azure Active Directory > Uživatel > Správa nastavení funkcí náhledu přístupového panelu**.

3. V **uživatelé mohou používat funkce náhledu pro registraci a správu bezpečnostních informací – rozšířená** oblast, vyberte **Vybraná**a pak buď zvolte skupinu uživatelů, nebo zvolte **Vše,** chcete-li tuto funkci zapnout pro všechny uživatele v tenantovi.

4. V oblasti **Uživatelé mohou používat funkce náhledu pro registraci a správu zabezpečení **info**** vyberte **Žádný**.

5. Uložte nastavení.

    Po uložení nastavení již nebudete mít přístup ke starému prostředí s bezpečnostními údaji.

>[!Important]
>Pokud tyto kroky nedokončíte před 25. Máte-li dotazy, kontaktujte registrationpreview@microsoft.comnás na adrese .

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>Požadavky na ověření pomocí přihlášení POST budou přísněji ověřeny

**Typ:** Změněná funkce  
**Kategorie služeb:** Ověřování (přihlášení)  
**Možnost i možnosti produktu:** Standardy

září 2019 budou požadavky na ověření pomocí metody POST přísněji ověřeny podle standardů HTTP. Konkrétně mezery a dvojité uvozovky (") již nebudou odebrány z hodnot formuláře požadavku. Neočekává se, že tyto změny přeruší všechny existující klienty a pomohou zajistit, aby požadavky odeslané do služby Azure AD byly spolehlivě zpracovány pokaždé.

Další informace naleznete v oznámení o [změnách změny Azure AD](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored).

---

## <a name="july-2019"></a>Červenec 2019

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Plán pro změnu: Aktualizace služby Proxy aplikace pro podporu pouze TLS 1.2

**Typ:** Plán změn  
**Kategorie služeb:** Proxy aplikace  
**Možnost i možnosti produktu:** Řízení přístupu

Abychom vám pomohli zajistit naše nejsilnější šifrování, začneme omezovat přístup služby Proxy aplikace pouze na protokoly TLS 1.2. Toto omezení bude zpočátku zavedeno zákazníkům, kteří již používají protokoly TLS 1.2, takže neuvidíte dopad. Úplné vyřazení protokolů TLS 1.0 a TLS 1.1 bude dokončeno 31. Zákazníci, kteří stále používají tls 1.0 a TLS 1.1, obdrží předběžné oznámení, aby se připravili na tuto změnu.

Chcete-li zachovat připojení ke službě Proxy aplikace po celou tuto změnu, doporučujeme, abyste se ujistili, že kombinace klient-server a prohlížeč-server jsou aktualizovány tak, aby používaly tls 1.2. Doporučujeme také, abyste zahrnuli všechny klientské systémy používané zaměstnanci pro přístup k aplikacím publikovaným prostřednictvím služby Proxy aplikace.

Další informace najdete [v tématu Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikace ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application).

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Plán změn: Pro Galerii aplikací přicházejí aktualizace návrhu

**Typ:** Plán změn  
**Kategorie služeb:** Podnikové aplikace  
**Možnost i možnosti produktu:** Sso

Nové změny uživatelského rozhraní přicházejí do návrhu **Přidat z oblasti galerie** přidat okno **aplikace.** Tyto změny vám pomohou snadněji najít vaše aplikace, které podporují automatické zřizování, OpenID Connect, Bezpečnostní assertion Markup Language (SAML) a heslo jednotné ho přihlašování (SSO).

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Plán změny: Odebrání IP adresy serveru MFA z IP adresy Office 365

**Typ:** Plán změn  
**Kategorie služeb:** Mfa  
**Možnost i možnosti produktu:** Ochrana & zabezpečení identity

Odstraňujeme IP adresu serveru MFA z [webové služby IP office 365 a url](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service). Pokud se na těchto stránkách momentálně spoléháte při aktualizaci nastavení brány firewall, musíte se ujistit, že zahrnujete také seznam IP adres, které jsou popsány v části **Požadavky na bránu firewall serveru azure vícefaktorového ověřování** v článku [Začínáme s vícefaktorovým ověřovacím serverem Azure.](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements)

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Tokeny pouze pro aplikace teď vyžadují, aby klientská aplikace existovala v tenantovi prostředků.

**Typ:** Dlouhodobého  
**Kategorie služeb:** Ověřování (přihlášení)  
**Možnost i možnosti produktu:** Ověřování uživatelů

26. července 2019 jsme změnili způsob, jakým poskytujeme tokeny pouze pro aplikace prostřednictvím [grantu pověření klienta](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow). Dříve aplikace mohly získat tokeny pro volání jiných aplikací, bez ohledu na to, zda byla klientská aplikace v tenantovi. Aktualizovali jsme toto chování tak, aby prostředky jednoho tenanta, někdy nazývané webová api, mohou být volány pouze klientskými aplikacemi, které existují v tenantovi prostředků.

Pokud vaše aplikace není umístěna v tenantovi prostředků, zobrazí se `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` chybová zpráva s nápisem: Chcete-li tento problém vyřešit, musíte vytvořit objekt zabezpečení služby klientské aplikace v tenantovi, a to buď pomocí [koncového bodu souhlasu správce,](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint) nebo [prostřednictvím prostředí PowerShell](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell), který zajistí, že váš klient udělil aplikaci oprávnění k provozu v rámci klienta.

Další informace naleznete v [tématu Co je nového pro ověřování?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant).

> [!NOTE]
> Existující souhlas mezi klientem a rozhraní API nadále není vyžadováno. Aplikace by měly stále dělat vlastní kontroly autorizace.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Nové bezhelná přihlášení k Azure AD pomocí klíčů zabezpečení FIDO2

**Typ:** Nová funkce  
**Kategorie služeb:** Ověřování (přihlášení)  
**Možnost i možnosti produktu:** Ověřování uživatelů

Zákazníci Azure AD teď můžou nastavit zásady pro správu klíčů zabezpečení FIDO2 pro uživatele a skupiny jejich organizace. Koncoví uživatelé si taky můžou sami zaregistrovat své klíče zabezpečení, pomocí klíčů se přihlásit ke svým účtům Microsoft na webech na zařízeních podporujících FIDO a přihlásit se ke svým zařízením s Windows 10, která se připojili k Azure AD.

Další informace najdete [v tématu Povolení přihlášení bez hesla pro Azure AD (preview)](/azure/active-directory/authentication/concept-authentication-passwordless) pro informace související se správcem a [nastavení bezpečnostních údajů pro použití klíče zabezpečení (Preview)](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) pro informace související s koncovým uživatelem.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD – červenec 2019

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnosti produktu:** Integrace třetí strany

V červenci 2019 jsme do galerie aplikací přidali těchto 18 nových aplikací s podporou Federace:

[Ungerboeck Software](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial), [Bright Pattern Omnichannel Contact Center](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial), Clever [Nelly](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial), [AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial), [Looop](https://www.looop.co/schedule-a-demo/), [productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial), [MS Azure SSO Access for Ethidex Compliance&trade;Office](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso), [Hype](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial), [Abstract](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial), [Ascentis](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial), [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [Wandera](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial), [TwineSocial](https://twinesocial.com/), [Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial), [HyperAnna](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial), [PharmID WasteWitness](https://pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), [JFrog Artifactory](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

Další informace o aplikacích najdete v tématu [Integrace aplikací SaaS se službou Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete [v tématu Seznam aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizace zřizování uživatelských účtů pro tyto nově podporované aplikace SaaS

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnost i možnosti produktu:** Monitorování & reportingu

Nyní můžete automatizovat vytváření, aktualizaci a odstranění uživatelských účtů pro tyto nově integrované aplikace:

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Federated Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Další informace o tom, jak lépe zabezpečit vaši organizaci pomocí automatického zřizování uživatelských účtů, najdete [v tématu Automatizace zřizování uživatelů pro aplikace SaaS pomocí Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Nová značka služby Azure AD Domain Services pro skupinu zabezpečení sítě

**Typ:** Nová funkce  
**Kategorie služeb:** Služby domény Azure AD  
**Možnost i možnosti produktu:** Služby domény Azure AD

Pokud už vás nebaví spravovat dlouhé seznamy IP adres a rozsahů, můžete použít novou značku síťové hospodařící služby **AzureActiveDirectoryServices** ve skupině zabezpečení sítě Azure, která vám pomůže zabezpečit příchozí provoz do podsítě virtuální sítě Služby Azure AD Domain Services.

Další informace o této nové značce služby naleznete v [tématu Skupiny zabezpečení sítě pro služby Azure AD Domain Services](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nové audity zabezpečení pro službu Azure AD Domain Services (Public Preview)

**Typ:** Nová funkce  
**Kategorie služeb:** Služby domény Azure AD  
**Možnost i možnosti produktu:** Služby domény Azure AD

S potěšením oznamujeme vydání auditování zabezpečení služby Azure AD Domain Service ve verzi Public Preview. Auditování zabezpečení pomáhá poskytnout vám kritický přehled o vašich ověřovacích službách streamováním událostí auditu zabezpečení do cílených prostředků, včetně Azure Storage, pracovních prostorů Azure Log Analytics a Centra událostí Azure, pomocí portálu služby Azure AD Domain Service.

Další informace najdete [v tématu Povolení auditů zabezpečení pro služby Azure AD Domain Services (Preview).](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events)

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Nové metody ověřování využití & přehledy (Public Preview)

**Typ:** Nová funkce  
**Kategorie služeb:** Samoobslužné resetování hesla  
**Možnost i možnosti produktu:** Monitorování & reportingu

Nové metody ověřování využití & přehledy sestavy vám může pomoci pochopit, jak funkce, jako je Azure Multi-Factor Authentication a samoobslužné resetování hesla jsou registrovány a používány ve vaší organizaci, včetně počtu registrovaných uživatelů pro každou funkci, jak často samoobslužné resetování hesla se používá k resetování hesla a jakým způsobem resetování se stane.

Další informace naleznete v [tématu Ověřování metod využití & přehledy (náhled)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Nové sestavy zabezpečení jsou k dispozici pro všechny správce Azure AD (Public Preview)

**Typ:** Nová funkce  
**Kategorie služeb:** Ochrana identity  
**Možnost i možnosti produktu:** Ochrana & zabezpečení identity

Všichni správci služby Azure AD teď můžou vybrat banner v horní části existujících sestav zabezpečení, jako jsou například **uživatelé označeni pro** sestavu rizik, abyste mohli začít používat nové prostředí zabezpečení, jak je znázorněno v **přehledech rizikových uživatelů** a **rizikových přihlášení.** V průběhu času se všechny zprávy o zabezpečení přesunou ze starších verzí na nové verze a nové sestavy vám poskytnou následující další funkce:

- Pokročilé filtrování a řazení

- Hromadné akce, jako je například odmítnutí rizika uživatele

- Potvrzení ohrožených nebo bezpečných subjektů

- Rizikový stav, zahrnující: V ohrožení, propuštěn, opravena a potvrzeno ohrožena

Další informace naleznete v [tématu Zprávy rizikových uživatelů](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users) a [Sestava rizikových přihlášení](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nové audity zabezpečení pro službu Azure AD Domain Services (Public Preview)

**Typ:** Nová funkce  
**Kategorie služeb:** Služby domény Azure AD  
**Možnost i možnosti produktu:** Služby domény Azure AD

S potěšením oznamujeme vydání auditování zabezpečení služby Azure AD Domain Service ve verzi Public Preview. Auditování zabezpečení pomáhá poskytnout vám kritický přehled o vašich ověřovacích službách streamováním událostí auditu zabezpečení do cílených prostředků, včetně Azure Storage, pracovních prostorů Azure Log Analytics a Centra událostí Azure, pomocí portálu služby Azure AD Domain Service.

Další informace najdete [v tématu Povolení auditů zabezpečení pro služby Azure AD Domain Services (Preview).](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events)

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Nová b2b přímá federace pomocí SAML/WS-Fed (Public Preview)

**Typ:** Nová funkce  
**Kategorie služeb:** B2b  
**Možnost i možnosti produktu:** B2B/B2C

Přímá federace usnadňuje práci s partnery, jejichž řešení identit spravovaných IT není Azure AD, a to díky práci se systémy identit, které podporují standardy SAML nebo WS-Fed. Po nastavení přímého federačního vztahu s partnerem s vámi může každý nový uživatel typu Host, kterého z této domény pozvete, spolupracovat pomocí stávajícího účtu organizace, což uživatelům zajistí bezproblémovost.

Další informace naleznete [v tématu Přímá federace se službou AD FS a poskytovateli třetích stran pro uživatele typu Host (náhled).](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizace zřizování uživatelských účtů pro tyto nově podporované aplikace SaaS

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnost i možnosti produktu:** Monitorování & reportingu

Nyní můžete automatizovat vytváření, aktualizaci a odstranění uživatelských účtů pro tyto nově integrované aplikace:

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Federated Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Další informace o tom, jak lépe zabezpečit vaši organizaci pomocí automatického zřizování uživatelských účtů, najdete [v tématu Automatizace zřizování uživatelů pro aplikace SaaS pomocí Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Nová kontrola duplicitních názvů skupin na portálu Azure AD

**Typ:** Nová funkce  
**Kategorie služeb:** Správa skupiny  
**Možnost i možnosti produktu:** Spolupráci

Teď, když vytvoříte nebo aktualizujete název skupiny z portálu Azure AD, provedeme kontrolu, abychom zjistili, jestli ve vašem prostředku duplikujete název existující skupiny. Pokud zjistíme, že název již používá jiná skupina, budete požádáni o změnu vašeho jména.

Další informace najdete [v tématu Správa skupin na portálu Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Azure AD teď podporuje parametry statického dotazu v odpovědích (přesměrování) identifikátorů URI

**Typ:** Nová funkce  
**Kategorie služeb:** Ověřování (přihlášení)  
**Možnost i možnosti produktu:** Ověřování uživatelů

Aplikace Azure AD teď můžou registrovat a používat identifikátory URI odpovědí `https://contoso.com/oauth2?idp=microsoft`(přesměrování) se statickými parametry dotazu (například) pro požadavky OAuth 2.0. Parametr statického dotazu podléhá porovnávání řetězců pro identifikátory URI odpovědi, stejně jako jakákoli jiná část identifikátoru URI odpovědi. Pokud neexistuje žádný registrovaný řetězec, který odpovídá přesměrování url-uri, požadavek je odmítnut. Pokud je nalezen identifikátor URI odpovědi, celý řetězec se používá k přesměrování uživatele, včetně parametru statického dotazu.

Identifikátory URI dynamické odpovědi jsou stále zakázány, protože představují bezpečnostní riziko a nelze je použít k uchování informací o stavu v rámci požadavku na ověření. Pro tento účel `state` použijte parametr.

V současné době obrazovky registrace aplikací na webu Azure Portal stále blokují parametry dotazu. Manifest aplikace však můžete ručně upravit a přidat a otestovat parametry dotazu ve vaší aplikaci. Další informace naleznete v [tématu Co je nového pro ověřování?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters).

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Protokoly aktivit (rozhraní API grafu MS) pro Azure AD jsou teď dostupné prostřednictvím rutin prostředí PowerShell

**Typ:** Nová funkce  
**Kategorie služeb:** Vykazování  
**Možnost i možnosti produktu:** Monitorování & reportingu

S nadšením oznamujeme, že protokoly aktivit Azure AD (sestavy auditování a přihlášení) jsou teď dostupné prostřednictvím modulu Azure AD PowerShell. Dříve jste mohli vytvořit vlastní skripty pomocí koncových bodů rozhraní MS Graph API a nyní jsme tuto možnost rozšířili na rutiny prostředí PowerShell.

Další informace o použití těchto rutin naleznete v [tématu Rutiny prostředí Azure AD PowerShell pro vytváření sestav](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting).

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Aktualizované ovládací prvky filtrů pro protokoly auditování a přihlášení ve službě Azure AD

**Typ:** Změněná funkce  
**Kategorie služeb:** Vykazování  
**Možnost i možnosti produktu:** Monitorování & reportingu

Aktualizovali jsme sestavy protokolů auditu a přihlášení, takže teď můžete použít různé filtry, aniž byste je museli přidávat jako sloupce na obrazovkách sestav. Kromě toho se nyní můžete rozhodnout, kolik filtrů chcete zobrazit na obrazovce. Tyto aktualizace spolupracují, aby byly přehledy snadněji čitelné a lépe vymezeny podle vašich potřeb.

Další informace o těchto aktualizacích naleznete [v tématu Filtrování protokolů auditu](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs) a [aktivit přihlášení k filtru](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities).

---

## <a name="june-2019"></a>Červen 2019

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Nové rozhraní API riskDetections pro Microsoft Graph (Veřejná verze preview)

**Typ:** Nová funkce  
**Kategorie služeb:** Ochrana identity  
**Možnost i možnosti produktu:** Ochrana & zabezpečení identity

S potěšením oznamujeme, že nové rozhraní API riskDetections pro Microsoft Graph je nyní ve verzi Public Preview. Toto nové rozhraní API můžete použít k zobrazení seznamu uživatele souvisejícího s ochranou identity vaší organizace a detekcí rizik přihlášení. Toto rozhraní API můžete také použít k efektivnějšímu dotazování na zjišťování rizik, včetně podrobností o typu zjišťování, stavu, úrovni a dalších.

Další informace naleznete v [referenční dokumentaci rozhraní API pro zjišťování rizik](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD – červen 2019

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnosti produktu:** Integrace třetí strany

V červnu 2019 jsme do galerie aplikací přidali těchto 22 nových aplikací s podporou Federace:

[Azure AD SAML Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial), [Otsuka Shokai (中中中)](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial), [ANAQUA](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial), [Azure VPN Client](https://portal.azure.com/), [ExpenseIn](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial), [Helper](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial), [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial), [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial), [Mercedes-Benz In-Car Office](https://me.secure.mercedes-benz.com/), [Skore](https://app.justskore.it/), [Oracle Cloud Infrastructure Console](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [CyberArk SAML Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), [Scrible Edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimize OS](https://proptimise.co.uk/software/), [Vtiger CRM (SAML),](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial)Oracle Access Manager for Oracle Retail Merchandising, Oracle Access Manager for Oracle E-Business Suite, Oracle IDCS pro sadu E-Business Suite, Oracle IDCS for PeopleSoft, Oracle IDCS for JD Edwards

Další informace o aplikacích najdete v tématu [Integrace aplikací SaaS se službou Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete [v tématu Seznam aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizace zřizování uživatelských účtů pro tyto nově podporované aplikace SaaS

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnost i možnosti produktu:** Monitorování & reportingu

Nyní můžete automatizovat vytváření, aktualizaci a odstranění uživatelských účtů pro tyto nově integrované aplikace:

- [Zoom](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Envoy](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

Další informace o tom, jak lépe zabezpečit vaši organizaci pomocí automatického zřizování uživatelských účtů, najdete [v tématu Automatizace zřizování uživatelů pro aplikace SaaS pomocí Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Zobrazení průběhu zřizování Služby Azure AD v reálném čase

**Typ:** Změněná funkce  
**Kategorie služeb:** Zřizování aplikací  
**Možnost i možnosti produktu:** Správa životního cyklu identity

Aktualizovali jsme prostředí zřizování Azure AD tak, aby zahrnovalo nový indikátor průběhu, který ukazuje, jak daleko jste v procesu zřizování uživatelů. Toto aktualizované prostředí také poskytuje informace o počtu uživatelů zřízených během aktuálního cyklu a o tom, kolik uživatelů bylo zřízeno k dnešnímu dni.

Další informace naleznete [v tématu Kontrola stavu zřizování uživatelů](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user).

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>Firemní značka se nyní zobrazuje na přihlašovacích a chybových obrazovkách

**Typ:** Změněná funkce  
**Kategorie služeb:** Ověřování (přihlášení)  
**Možnost i možnosti produktu:** Ověřování uživatelů

Aktualizovali jsme Azure AD tak, aby vaše společnost značky značky se zobrazí na odhlášení a chybové obrazovky, stejně jako přihlašovací stránku. Nemusíte dělat nic zapnout tuto funkci, Azure AD jednoduše používá prostředky, které jste už nastavili v oblasti **značky společnosti** na webu Azure Portal.

Další informace o nastavení firemní značky najdete v tématu [Přidání značky na stránky Azure Active Directory vaší organizace](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding).

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Server Azure Multi-Factor Authentication (MFA) už není k dispozici pro nová nasazení

**Typ:** Zastaralé  
**Kategorie služeb:** Mfa  
**Možnost i možnosti produktu:** Ochrana & zabezpečení identity

července 2019 již společnost Microsoft nebude nabízet server MFA pro nová nasazení. Noví zákazníci, kteří chtějí ve své organizaci vyžadovat vícefaktorové ověřování, teď musí používat vícefaktorové ověřování Azure na cloudu. Zákazníci, kteří aktivovali server MFA před 1. Stále budete moci stáhnout nejnovější verzi, získat budoucí aktualizace a generovat přihlašovací údaje k aktivaci.

Další informace najdete [v tématu Začínáme s vícefaktorovým ověřovacím serverem Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy). Další informace o cloudovém vícefaktorovém ověřování Azure najdete [v tématu Plánování vícefaktorového ověřování Azure na základě cloudového ověřování](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---

## <a name="may-2019"></a>Květen 2019

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Změna služby: Budoucí podpora pouze protokolů TLS 1.2 ve službě Proxy aplikace

**Typ:** Plán změn  
**Kategorie služeb:** Proxy aplikace  
**Možnost i možnosti produktu:** Řízení přístupu

Abychom našim zákazníkům pomohli zajistit nejlepší šifrování ve své třídě, omezujeme přístup pouze na protokoly TLS 1.2 ve službě Proxy aplikací. Tato změna se postupně zavádí pro zákazníky, kteří již používají pouze protokoly TLS 1.2, takže byste neměli vidět žádné změny.

Vyřazení TLS 1.0 a TLS 1.1 se stane v srpnu 31, 2019, ale poskytneme další předběžné oznámení, takže budete mít čas se připravit na tuto změnu. Chcete-li se připravit na tuto změnu, ujistěte se, že kombinace klient-server a prohlížeč-server, včetně všech klientů, které uživatelé používají pro přístup k aplikacím publikovaným prostřednictvím proxy aplikace, jsou aktualizovány tak, aby používaly protokol TLS 1.2 k zachování připojení ke službě Proxy aplikace. Další informace najdete [v tématu Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikace ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Zobrazení přihlašovacích údajů souvisejících s aplikací pomocí přehledu využití a přehledů

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnost i možnosti produktu:** Monitorování & reportingu

Teď můžete použít přehled využití a přehledy, který se nachází v oblasti **podnikové aplikace** na webu Azure Portal, abyste získali zobrazení přihlašovacích dat zaměřené na aplikace, včetně informací o:

- Nejlepší použité aplikace pro vaši organizaci

- Aplikace s nejvíce neúspěšnými přihlášeními

- Hlavní chyby přihlášení pro každou aplikaci

Další informace o této funkci najdete [v tématu Přehled využití a přehledů na portálu Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatizace zřizování uživatelů do cloudových aplikací pomocí Azure AD

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnost i možnosti produktu:** Monitorování & reportingu

Postupujte podle těchto nových kurzů a pomocí služby Azure AD Provisioning Service automatizujte vytváření, odstraňování a aktualizaci uživatelských účtů pro následující cloudové aplikace:

- [Comeet](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [Dynamicsignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [Zabezpečení keeperu](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

Můžete také sledovat tento nový [Dropbox kurz](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), který poskytuje informace o tom, jak zřídit objekty skupiny.

Další informace o tom, jak lépe zabezpečit vaši organizaci prostřednictvím automatického zřizování uživatelských účtů, najdete [v tématu Automatizace zřizování uživatelů do aplikací SaaS pomocí Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Skóre zabezpečení identity je teď dostupné ve službě Azure AD (obecná dostupnost)

**Typ:** Nová funkce  
**Kategorie služeb:** N/a  
**Možnost i možnosti produktu:** Ochrana & zabezpečení identity

Teď můžete sledovat a vylepšovat stav zabezpečení identity pomocí funkce skóre zabezpečení identity ve službě Azure AD. Funkce skóre zabezpečení identity používá jeden řídicí panel, který vám pomůže:

- Objektivně změřte stav zabezpečení identity na základě skóre mezi 1 a 223.

- Plánování vylepšení zabezpečení identity

- Zkontrolujte úspěšnost vylepšení zabezpečení

Další informace o funkci skóre zabezpečení identity najdete [v tématu Co je skóre zabezpečení identity ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Nové prostředí pro registraci aplikací je nyní k dispozici (obecná dostupnost)

**Typ:** Nová funkce  
**Kategorie služeb:** Ověřování (přihlášení)  
**Možnost i možnosti produktu:** Vývojářské prostředí

Nové prostředí [registrace aplikací](https://aka.ms/appregistrations) je nyní v obecné dostupnosti. Toto nové prostředí zahrnuje všechny klíčové funkce, které znáte z portálu Azure a portálu registrace aplikací, a vylepšuje je prostřednictvím:

- **Lepší správa aplikací.** Místo toho, abyste své aplikace viděli na různých portálech, můžete teď vidět všechny aplikace na jednom místě.

- **Zjednodušená registrace aplikace.** Od vylepšeného prostředí navigace až po vylepšené prostředí pro výběr oprávnění – nyní je snazší zaregistrovat a spravovat vaše aplikace.

- **Podrobnější informace.** Další podrobnosti o aplikaci najdete, včetně úvodních příruček a dalších.

Další informace najdete v tématu [Platforma identit Microsoftu](https://docs.microsoft.com/azure/active-directory/develop/) a [prostředí registrace aplikací je teď obecně dostupné!](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) blog oznámení.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Nové funkce dostupné v rozhraní API rizikových uživatelů pro ochranu identity

**Typ:** Nová funkce  
**Kategorie služeb:** Ochrana identity  
**Možnost i možnosti produktu:** Ochrana & zabezpečení identity

S potěšením oznamujeme, že nyní můžete pomocí rozhraní API rizikových uživatelů načíst historii rizik uživatelů, propustit rizikové uživatele a potvrdit, že uživatelé byli ohroženi. Tato změna vám pomůže efektivněji aktualizovat stav rizika uživatelů a pochopit jejich historii rizik.

Další informace naleznete v [referenční dokumentaci rozhraní API rizikových uživatelů](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD – květen 2019

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnosti produktu:** Integrace třetí strany

V květnu 2019 jsme do galerie aplikací přidali těchto 21 nových aplikací s podporou Federace:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [Real Odkazy](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [Jednoduché znamení,](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial) [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [Displayr](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [Marketo Prodej Zapojit](https://toutapp.com/login), [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [OutSystems](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [Meta4 Globální HR,](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial) [Quantum Workplace](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [Kobalt](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), [webMethods API](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial)Cloud , [RedFlag](https://pocketstop.com/redflag/), [Whatfix,](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial) [Control](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [Foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Další informace o aplikacích najdete v tématu [Integrace aplikací SaaS se službou Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete [v tématu Seznam aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Vylepšené možnosti vytváření a správy skupin na portálu Azure AD

**Typ:** Nová funkce  
**Kategorie služeb:** Správa skupiny  
**Možnost i možnosti produktu:** Spolupráci

Na portálu Azure AD jsme vylepšili prostředí související se skupinami. Tato vylepšení umožňují správcům lépe spravovat seznamy skupin, seznamy členů a poskytovat další možnosti vytváření.

Mezi vylepšení patří:

- Základní filtrování podle typu členství a typu skupiny.

- Přidání nových sloupců, například Zdroj a e-mailová adresa.

- Možnost vícenásobných výběrových skupin, členů a seznamů vlastníků pro snadné odstranění.

- Možnost vybrat si e-mailovou adresu a přidat vlastníky během vytváření skupiny.

Další informace najdete [v tématu Vytvoření základní skupiny a přidání členů pomocí služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Konfigurace zásad pojmenování pro skupiny Office 365 na portálu Azure AD (Obecná dostupnost)

**Typ:** Změněná funkce  
**Kategorie služeb:** Správa skupiny  
**Možnost i možnosti produktu:** Spolupráci

Správci teď můžou nakonfigurovat zásady pojmenování pro skupiny Office 365 pomocí portálu Azure AD. Tato změna pomáhá vynutit konzistentní konvence pojmenování pro skupiny Office 365 vytvořené nebo upravené uživateli ve vaší organizaci.

Zásady pojmenování pro skupiny Office 365 můžete nakonfigurovat dvěma různými způsoby:

- Definujte předpony nebo přípony, které jsou automaticky přidány do názvu skupiny.

- Nahrajte přizpůsobenou sadu blokovaných slov pro vaši organizaci, která nejsou povolena v názvech skupin (například "CEO, Payroll, HR").

Další informace naleznete [v tématu Enforce a Naming Policy for Office 365 groups](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Koncové body rozhraní API Microsoft Graphu jsou teď dostupné pro protokoly aktivit Azure AD (obecná dostupnost)

**Typ:** Změněná funkce  
**Kategorie služeb:** Vykazování  
**Možnost i možnosti produktu:** Monitorování & reportingu

S radostí oznamujeme obecnou dostupnost podpory koncových bodů rozhraní API Microsoft Graphu pro protokoly aktivit Azure AD. V této verzi teď můžete použít verzi 1.0 protokolů auditu Azure AD, stejně jako přihlášení protokoly API.

Další informace naleznete v [tématu Přehled rozhraní API protokolu auditu Azure AD](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Správci nyní mohou použít podmíněný přístup pro kombinovaný proces registrace (Public Preview)

**Typ:** Nová funkce  
**Kategorie služeb:** Podmíněný přístup  
**Možnost i možnosti produktu:** Ochrana & zabezpečení identity  

Správci nyní mohou vytvářet zásady podmíněného přístupu pro použití na stránce kombinované registrace. To zahrnuje použití zásad umožňujících registraci, pokud:

- Uživatelé jsou v důvěryhodné síti.

- Uživatelé jsou nízké riziko přihlášení.

- Uživatelé jsou na spravovaném zařízení.

- Uživatelé souhlasí s podmínkami použití (TOU) organizace.

Další informace o podmíněném přístupu a resetování hesla najdete v [tématu Podmíněný přístup pro kombinovaný příspěvek blogu služby Azure AD pro kombinované vícefaktorové ověřování a obnovení hesla](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348). Další informace o zásadách podmíněného přístupu pro proces kombinované registrace naleznete v [tématu zásady podmíněného přístupu pro kombinovanou registraci](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration). Další informace o funkci používání podmínek používání služby Azure AD najdete v [tématu Funkce použití podmínek použití služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

## <a name="april-2019"></a>Duben 2019

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>Nová detekce analýzy hrozeb Azure AD je teď dostupná jako součást ochrany identity Azure AD

**Typ:** Nová funkce  
**Kategorie služeb:** Ochrana identity Azure AD  
**Možnost i možnosti produktu:** Ochrana & zabezpečení identity

Azure AD detekce analýzy hrozeb je teď k dispozici jako součást aktualizované funkce Azure AD Identity Protection. Tato nová funkce pomáhá označit neobvyklou aktivitu uživatele pro konkrétního uživatele nebo aktivitu, která je konzistentní se známými vzory útoků na základě interních a externích zdrojů analýzy hrozeb společnosti Microsoft.

Další informace o aktualizovanou verzi služby Azure AD Identity Protection najdete v tématu [čtyři hlavní vylepšení ochrany identity Azure AD jsou teď ve veřejném blogu preview](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) a co je ochrana [identity Azure Active Directory (aktualizováno)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) Článku. Další informace o zjišťování analýzy hrozeb Azure AD najdete v článku [zjišťování rizik ochrany identity služby Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks)

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Správa oprávnění Azure AD je teď dostupná (Veřejná verze Preview)

**Typ:** Nová funkce  
**Kategorie služeb:** Zásady správného řízení identity  
**Možnost i možnosti produktu:** Zásady správného řízení identity

Správa nároků Azure AD, která je teď ve verzi Public Preview, pomáhá zákazníkům delegovat správu přístupových balíčků, která definuje, jak mohou zaměstnanci a obchodní partneři požadovat přístup, kdo musí schválit a jak dlouho mají přístup. Balíčky accessu můžou spravovat členství ve skupinách Azure AD a Office 365, přiřazení rolí v podnikových aplikacích a přiřazení rolí pro weby SharePointu Online. Další informace o správě nároků najdete v [přehledu správy oprávnění Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Další informace o šíři funkcí zásad správného řízení identit Azure AD, včetně správy privilegovaných identit, kontrol přístupu a podmínek použití, najdete v [tématu Co je zásadsprávné řízení identit Azure AD?](../governance/identity-governance-overview.md)

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Konfigurace zásad pojmenování pro skupiny Office 365 na portálu Azure AD (Veřejná verze preview)

**Typ:** Nová funkce  
**Kategorie služeb:** Správa skupiny  
**Možnost i možnosti produktu:** Spolupráci

Správci teď můžou nakonfigurovat zásady pojmenování pro skupiny Office 365 pomocí portálu Azure AD. Tato změna pomáhá vynutit konzistentní konvence pojmenování pro skupiny Office 365 vytvořené nebo upravené uživateli ve vaší organizaci.

Zásady pojmenování pro skupiny Office 365 můžete nakonfigurovat dvěma různými způsoby:

- Definujte předpony nebo přípony, které jsou automaticky přidány do názvu skupiny.

- Nahrajte přizpůsobenou sadu blokovaných slov pro vaši organizaci, která nejsou povolena v názvech skupin (například "CEO, Payroll, HR").

Další informace naleznete [v tématu Enforce a Naming Policy for Office 365 groups](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Protokoly aktivit Azure AD jsou teď dostupné ve Službě Azure Monitor (obecná dostupnost)

**Typ:** Nová funkce  
**Kategorie služeb:** Vykazování  
**Možnost i možnosti produktu:** Monitorování & reportingu

Abychom vám pomohli vyřešit vaši zpětnou vazbu o vizualizacích pomocí protokolů aktivit Azure AD, zavádíme novou funkci Přehledy v Log Analytics. Tato funkce vám pomůže získat přehled o vašich prostředcích Azure AD pomocí našich interaktivních šablon, nazývaných Sešity. Tyto předem vyestavěné sešity mohou obsahovat podrobnosti o aplikacích nebo uživatelích a zahrnují:

- **Přihlášení.** Obsahuje podrobnosti o aplikacích a uživatelích, včetně umístění přihlášení, použitého operačního systému nebo klienta a verze prohlížeče a počtu úspěšných nebo neúspěšných přihlášení.

- **Starší verze ověřování a podmíněný přístup.** Obsahuje podrobnosti o aplikacích a uživatelích, kteří používají starší verze ověřování, včetně využití vícefaktorového ověřování aktivovaného zásadami podmíněného přístupu, aplikací používajících zásady podmíněného přístupu a tak dále.

- **Analýza selhání přihlášení.** Pomáhá určit, zda se vaše přihlašovací chyby vyskytují v důsledku akce uživatele, problémů se zásadami nebo infrastruktury.

- **Vlastní sestavy.** Můžete vytvořit nové nebo upravit existující sešity, které vám pomůžou přizpůsobit funkci Přehledy pro vaši organizaci.

Další informace najdete v tématu [Jak používat sešity Azure Monitoru pro sestavy Služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD – duben 2019

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnosti produktu:** Integrace třetí strany

V dubnu 2019 jsme do galerie aplikací přidali těchto 21 nových aplikací s podporou Federace:

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [Percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [Benchling](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial), [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [RStudio Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [Mitel Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [Alibaba Cloud (Jednotné měnové služby založené na rolích)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent Equity Management](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Sectigo Certificate Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), SurveyMonkey , [Indiggo](https://indiggolead.com/) [SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial)

Další informace o aplikacích najdete v tématu [Integrace aplikací SaaS se službou Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete [v tématu Seznam aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Možnost frekvence nových kontrol přístupu a výběr více rolí

**Typ:** Nová funkce  
**Kategorie služeb:** Recenze přístupu  
**Možnost i možnosti produktu:** Zásady správného řízení identity

Nové aktualizace v recenzích přístupu azure ad umožňují:

- Změňte frekvenci kontrol přístupu na **pololetní**, kromě dříve existujících možností týdenní, měsíční, čtvrtletní a roční.

- Při vytváření jedné kontroly přístupu vyberte více rolí prostředků Azure AD a Azure. V takovém případě jsou všechny role nastaveny se stejným nastavením a všichni recenzenti jsou upozorněni současně.

Další informace o tom, jak vytvořit kontrolu přístupu, najdete [v tématu Vytvoření kontroly přístupu skupin nebo aplikací v azure ad kontroly přístupu](https://docs.microsoft.com/azure/active-directory/governance/create-access-review).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Systém e-mailových výstražných systémů Azure AD Connect se přemisťuje a některým zákazníkům odesílají nové informace o odesílateli e-mailu.

**Typ:** Změněná funkce  
**Kategorie služeb:** Synchronizace reklam  
**Možnost i možnosti produktu:** Platforma

Azure AD Connect je v procesu přechodu našeho e-mailového výstražného systému, potenciálně zobrazuje některé zákazníky nový odesílatel e-mailu. Chcete-li tento problém `azure-noreply@microsoft.com` vyřešit, musíte přidat do seznamu povolených položek vaší organizace, jinak nebudete moct dál dostávat důležitá upozornění ze služeb Office 365, Azure nebo synchronizace.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Změny přípony UPN jsou nyní úspěšné mezi federovanými doménami ve službě Azure AD Connect

**Typ:** Dlouhodobého  
**Kategorie služeb:** Synchronizace reklam  
**Možnost i možnosti produktu:** Platforma

Nyní můžete úspěšně změnit příponu UPN uživatele z jedné federované domény do jiné federované domény ve službě Azure AD Connect. Tato oprava znamená, že byste již neměli docházet k chybě FederatedDomainChangeError během cyklu synchronizace nebo obdržet e-mail s oznámením" Nelze aktualizovat tento objekt ve službě Azure Active Directory, protože atribut [FederatedUser.UserPrincipalName] není platný. Aktualizujte hodnotu v místních adresářových službách".

Další informace naleznete v [tématu Poradce při potížích s chybami během synchronizace](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Zvýšené zabezpečení pomocí zásad podmíněného přístupu založeného na ochraně aplikací ve službě Azure AD (Public Preview)

**Typ:** Nová funkce  
**Kategorie služeb:** Podmíněný přístup  
**Možnost i možnosti produktu:** Ochrana & zabezpečení identity

Podmíněný přístup založený na ochraně aplikací je teď dostupný pomocí zásad **vyžadovat ochranu aplikací.** Tato nová zásada pomáhá zvýšit zabezpečení vaší organizace tím, že pomáhá předcházet:

- Uživatelé, kteří získají přístup k aplikacím bez licence Microsoft Intune.

- Uživatelé nemohou získat zásady ochrany aplikací Microsoft Intune.

- Uživatelé, kteří získají přístup k aplikacím bez nakonfigurovaných zásad ochrany aplikací Microsoft Intune.

Další informace najdete v [tématu Jak vyžadovat zásady ochrany aplikací pro přístup ke cloudovým aplikacím pomocí podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Nová podpora jednotného přihlášení a podmíněného přístupu Azure AD v Microsoft Edge (veřejná verze preview)

**Typ:** Nová funkce  
**Kategorie služeb:** Podmíněný přístup  
**Možnost i možnosti produktu:** Ochrana & zabezpečení identity

Vylepšili jsme naši podporu Azure AD pro Microsoft Edge, včetně poskytování nové podpory pro jednotné přihlašování azure a podmíněný přístup. Pokud jste dříve používali Microsoft Intune Managed Browser, můžete teď místo toho použít Microsoft Edge.

Další informace o nastavení a správě zařízení a aplikací pomocí podmíněného přístupu najdete [v tématu Vyžadovat spravovaná zařízení pro přístup ke cloudovým aplikacím s podmíněným přístupem](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) a [Vyžadovat schválené klientské aplikace pro přístup ke cloudovým aplikacím pomocí podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Další informace o správě přístupu pomocí microsoft edge pomocí zásad Microsoft Intune najdete v [tématu Správa přístupu k Internetu pomocí prohlížeče chráněného zásadami Microsoft Intune](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>Březen 2019

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Rozhraní Identity Experience Framework a podpora vlastních zásad ve službě Azure Active Directory B2C je teď k dispozici (GA)

**Typ:** Nová funkce  
**Kategorie služeb:** B2C - Správa identit y spotřebitele  
**Možnost i možnosti produktu:** B2B/B2C

Teď můžete ve Službě Azure AD B2C vytvářet vlastní zásady, včetně následujících úkolů, které jsou podporované ve velkém měřítku a v rámci naší smlouvy Azure SLA:

- Vytvořte a nahrajte vlastní cesty uživatelů ověřování pomocí vlastních zásad.

- Popište cesty uživatele krok za krokem jako výměny mezi poskytovateli deklarací identity.

- Definujte podmíněné větvení v cestách uživatelů.

- Transformujte a mapujte nároky pro použití v rozhodnutích a komunikacích v reálném čase.

- Ve vlastních cestách uživatelů ověřování používejte služby s podporou rozhraní REST API. Například s poskytovateli e-mailu, CRM a proprietárními autorizačními systémy.

- Federate s poskytovateli identit, kteří jsou kompatibilní s protokolem OpenIDConnect. Například s víceklientské Azure AD, poskytovatelé účtů na sociální chod nebo dvoufaktorové ověřování zprostředkovatelů.

Další informace o vytváření vlastních zásad najdete [v tématu Poznámky pro vývojáře pro vlastní zásady ve službě Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom) a přečtěte si [blogový příspěvek Alexe Simona, včetně případových studií](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD – březen 2019

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnosti produktu:** Integrace třetí strany

V březnu 2019 jsme do galerie aplikací přidali těchto 14 nových aplikací s podporou Federace:

[ISEC7 Mobile Exchange Delegate](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [Vysvětlení-založené auditování systém](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [Lean](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [Powerschool výkon věcech](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode](https://cinode.com/), [Iris Intranet](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit Horizons](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [TAS](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Další informace o aplikacích najdete v tématu [Integrace aplikací SaaS se službou Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete [v tématu Seznam aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Nové zřizovací konektory Zscaler a Atlassian v galerii Azure AD – březen 2019

**Typ:** Nová funkce  
**Kategorie služeb:** Zřizování aplikací  
**Možnosti produktu:** Integrace třetí strany

Automatizujte vytváření, aktualizaci a mazání uživatelských účtů pro následující aplikace:

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler Beta](https://aka.ms/ZscalerBetaProvisioning), [Zscaler One,](https://aka.ms/ZscalerOneProvisioning) [Zscaler Dva](https://aka.ms/ZscalerTwoProvisioning), [Zscaler Tři](https://aka.ms/ZscalerThreeProvisioning), [Zscaler ZSCloud](https://aka.ms/ZscalerZSCloudProvisioning), [Atlassian Cloud](https://aka.ms/atlassianCloudProvisioning)

Další informace o tom, jak lépe zabezpečit vaši organizaci prostřednictvím automatického zřizování uživatelských účtů, najdete [v tématu Automatizace zřizování uživatelů do aplikací SaaS pomocí Azure AD](https://aka.ms/ProvisioningDocumentation).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Obnovení a správa odstraněných skupin Office 365 na portálu Azure AD

**Typ:** Nová funkce  
**Kategorie služeb:** Správa skupiny  
**Možnost i možnosti produktu:** Spolupráci

Odstraněné skupiny Office 365 teď můžete zobrazit a spravovat z portálu Azure AD. Tato změna vám pomůže zjistit, které skupiny jsou k dispozici k obnovení, spolu s tím, že vám umožní trvale odstranit všechny skupiny, které vaše organizace nepotřebuje.

Další informace naleznete v tématu [Obnovení, jehož platnost vypršela nebo byly odstraněny .](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore)

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Jednotné přihlašování je teď dostupné pro místní aplikace zabezpečené azure ad SAML prostřednictvím proxy aplikace (public preview)

**Typ:** Nová funkce  
**Kategorie služeb:** Proxy aplikace  
**Možnost i možnosti produktu:** Řízení přístupu

Teď můžete poskytnout jednotné přihlašování (SSO) prostředí pro místní aplikace ověřené SAML, spolu se vzdáleným přístupem k těmto aplikacím prostřednictvím proxy aplikace. Další informace o tom, jak nastavit jednotné přihlašování SAML v místních aplikacích, najdete v [tématu jednotné přihlašování SAML pro místní aplikace s proxy aplikací (Preview).](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps)

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Klientské aplikace v smyčkách požadavků budou přerušeny, aby se zlepšila spolehlivost a uživatelské prostředí

**Typ:** Nová funkce  
**Kategorie služeb:** Ověřování (přihlášení)  
**Možnost i možnosti produktu:** Ověřování uživatelů

Klientské aplikace mohou nesprávně vydávat stovky stejných požadavků na přihlášení v krátkém časovém období. Tyto požadavky, ať už jsou úspěšné nebo ne, všechny přispívají ke špatnému uživatelskému prostředí a zvýšeným úlohám pro IDP, zvyšují latenci pro všechny uživatele a snižují dostupnost IDP.

Tato aktualizace `invalid_grant` odešle `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` chybu: klientským aplikacím, které vydávají duplicitní požadavky vícekrát za krátkou dobu, nad rámec běžného provozu. Klientské aplikace, u kterých se tento problém vyskytne, by měly zobrazovat interaktivní výzvu, která vyžaduje, aby se uživatel znovu přihlásil. Další informace o této změně a o tom, jak opravit aplikaci, pokud se s touto chybou setká, najdete v [tématu Co je nového pro ověřování?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Nové uživatelské prostředí protokolů auditu je nyní k dispozici

**Typ:** Změněná funkce  
**Kategorie služeb:** Vykazování  
**Možnost i možnosti produktu:** Monitorování & reportingu

Vytvořili jsme novou stránku **protokolů auditování** Azure AD, která pomáhá zlepšit čitelnost i způsob vyhledávání informací. Pokud chcete zobrazit novou stránku **protokolů auditu,** vyberte **protokoly auditování** v části **Aktivita** ve službě Azure AD.

![Nová stránka protokolů auditu s ukázkovými informacemi](media/whats-new/audit-logs-page.png)

Další informace o nové stránce **protokolů auditu** najdete [v tématu Sestavy auditovacích aktivit na portálu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Nová upozornění a pokyny, které pomáhají zabránit náhodnému uzamčení správce z nesprávně nakonfigurovaných zásad podmíněného přístupu

**Typ:** Změněná funkce  
**Kategorie služeb:** Podmíněný přístup  
**Možnost i možnosti produktu:** Ochrana & zabezpečení identity

Abychom zabránili správcům v náhodném uzamčení vlastních klientů prostřednictvím chybně nakonfigurovaných zásad podmíněného přístupu, vytvořili jsme nová upozornění a aktualizované pokyny na webu Azure Portal. Další informace o nových pokynech najdete v tématu [Co jsou závislosti služeb v podmíněném přístupu služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Vylepšené podmínky používání koncových uživatelů na mobilních zařízeních

**Typ:** Změněná funkce  
**Kategorie služeb:** Podmínky použití  
**Možnost i možnosti produktu:** Správy

Aktualizovali jsme naše stávající podmínky použití, abychom vám pomohli zlepšit způsob kontroly a souhlasu s podmínkami použití na mobilním zařízení. Nyní můžete zobrazení přiblížit a oddálit, vrátit se zpět, stáhnout informace a vybrat hypertextové odkazy. Další informace o aktualizovaných podmínkách použití naleznete v [tématu Azure Active Directory funkce použití](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Nové protokoly aktivit Azure AD protokoly ke stažení prostředí k dispozici

**Typ:** Změněná funkce  
**Kategorie služeb:** Vykazování  
**Možnost i možnosti produktu:** Monitorování & reportingu

Teď můžete stáhnout velké množství protokolů aktivit přímo z webu Azure Portal. Tato aktualizace umožňuje:

- Stáhněte si až 250 000 řádků.

- Získejte upozornění po dokončení stahování.

- Přizpůsobte název souboru.

- Určete výstupní formát, buď JSON nebo CSV.

Další informace o této funkci najdete [v tématu Úvodní příručka: Stažení sestavy auditu pomocí portálu Azure Portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Narušující změny: Aktualizace vyhodnocení stavu exchange activesync (EAS)

**Typ:** Plán změn  
**Kategorie služeb:** Podmíněný přístup  
**Možnost i možnosti produktu:** Řízení přístupu

Právě aktualizujeme, jak Exchange ActiveSync (EAS) vyhodnocuje následující podmínky:

- Umístění uživatele na základě země, oblasti nebo IP adresy

- Riziko přihlášení

- Platforma zařízení

Pokud jste tyto podmínky dříve použili v zásadách podmíněného přístupu, uvědomte si, že chování podmínky se může změnit. Pokud jste například dříve použili podmínku umístění uživatele v zásadách, může se zobrazit možnost přeskočené zásady na základě umístění uživatele.

---

## <a name="february-2019"></a>Únor 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Konfigurovatelné šifrování tokenů Azure AD SAML (Veřejná verze Preview) 

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnost i možnosti produktu:** Sso

Nyní můžete nakonfigurovat libovolnou podporovanou aplikaci SAML pro příjem šifrovaných tokenů SAML. Při konfiguraci a použití s aplikací, Azure AD šifruje vyzařované SAML kontrolní výrazy pomocí veřejného klíče získaného z certifikátu uloženého ve službě Azure AD.

Další informace o konfiguraci šifrování tokenů SAML najdete [v tématu Konfigurace šifrování tokenu Azure AD SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Vytvoření kontroly přístupu pro skupiny nebo aplikace pomocí recenzí přístupu azure ad access

**Typ:** Nová funkce  
**Kategorie služeb:** Recenze přístupu  
**Možnost i možnosti produktu:** Správy

Teď můžete zahrnout více skupin nebo aplikací do jedné kontroly přístupu azure ad pro členství ve skupině nebo přiřazení aplikace. Kontroly přístupu s více skupinami nebo aplikacemi jsou nastaveny pomocí stejného nastavení a všichni zahrnutí recenzenti jsou upozorněni současně.

Další informace o tom, jak vytvořit kontrolu přístupu pomocí kontroly přístupu k Azure AD, najdete [v tématu Vytvoření kontroly přístupu skupin nebo aplikací v recenzích přístupu k Azure AD](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD – únor 2019

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnosti produktu:** Integrace třetí strany
 
V únoru 2019 jsme do galerie aplikací přidali těchto 27 nových aplikací s podporou Federace:

[Euromonitor Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MindTickle,](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial) [FAT FINGER,](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7) [AirStack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [IDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [Skyward Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight-CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), Povolení Klikněte, [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [Pexip,](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial) [Stormboard,](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial) [Seismic,](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial) [Share A Dream](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [webMethods Integration Cloud,](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial) [Knowledge Anywhere LMS,](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial)OU [Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [Periscope Data](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [Netop Portal,](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial) [smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud by Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [ClickUp Productivity Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Další informace o aplikacích najdete v tématu [Integrace aplikací SaaS se službou Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete [v tématu Seznam aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Vylepšená kombinovaná registrace vícefaktorové registrace/SSPR

**Typ:** Změněná funkce  
**Kategorie služeb:** Samoobslužné resetování hesla  
**Možnost i možnosti produktu:** Ověřování uživatelů

V reakci na zpětnou vazbu od zákazníků jsme vylepšili kombinované prostředí náhledu registrace vícefaktorových a snopů, což uživatelům pomáhá rychleji zaregistrovat jejich bezpečnostní údaje pro vícefaktorové a sspr. 

**Chcete-li zapnout rozšířené prostředí pro dnešní uživatele, postupujte takto:**

1. Jako globální správce nebo správce uživatelů se přihlaste na portál Azure portal a přejděte na **Azure Active Directory > uživatelská nastavení > Spravovat nastavení pro funkce náhledu přístupového panelu**. 

2. V **uživatelé, kteří mohou používat funkce náhledu pro registraci a správu bezpečnostních údajů – možnost aktualizovat,** zvolte zapnout funkce pro **vybranou skupinu uživatelů** nebo pro **všechny uživatele**.

Během několika příštích týdnů budeme odebírání možnosti zapnout staré kombinované prostředí náhledu registrace vícefaktorových a snop pro klienty, kteří ji ještě nemají zapnutou.

**Chcete-li zjistit, zda bude ovládací prvek odebrán pro vašeho tenanta, postupujte takto:**

1. Jako globální správce nebo správce uživatelů se přihlaste na portál Azure portal a přejděte na **Azure Active Directory > uživatelská nastavení > Spravovat nastavení pro funkce náhledu přístupového panelu**.  

2. Pokud **uživatelé, kteří mohou používat funkce náhledu pro registraci a správu bezpečnostních informací** možnost **je nastavena**na žádný , možnost bude odebrána z vašeho tenanta.

Bez ohledu na to, zda jste dříve zapnuli staré kombinované prostředí náhledu registrace vícefaktorových a snopů pro uživatele nebo ne, staré prostředí bude vypnuto k budoucímu datu. Z tohoto důvodu důrazně doporučujeme, abyste se co nejdříve přesunuli na nový, vylepšený zážitek.

Další informace o rozšířené registraci najdete v [tématu Vylepšení cool kombinované Azure AD MFA a nastavení hesla registrace zkušenosti](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Aktualizované prostředí správy zásad pro toky uživatelů

**Typ:** Změněná funkce  
**Kategorie služeb:** B2C - Správa identit y spotřebitele  
**Možnost i možnosti produktu:** B2B/B2C

Aktualizovali jsme proces vytváření a správy zásad pro toky uživatelů (dříve označované jako integrované zásady) jednodušší. Toto nové prostředí je teď výchozí pro všechny klienty Azure AD.

Další zpětnou vazbu a návrhy můžete poskytnout pomocí ikon úsměvu nebo zamračení v oblasti **Poslat nám zpětnou vazbu** v horní části obrazovky portálu.

Další informace o novém prostředí správy zásad najdete v tématu [Azure AD B2C má nyní javascriptové přizpůsobení a mnoho dalších nových funkcí](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blogu.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Výběr konkrétníverze prvků stránky poskytované Azure AD B2C

**Typ:** Nová funkce  
**Kategorie služeb:** B2C - Správa identit y spotřebitele  
**Možnost i možnosti produktu:** B2B/B2C

Teď můžete zvolit konkrétní verzi prvků stránky poskytované Azure AD B2C. Výběrem konkrétní verze můžete otestovat aktualizace dříve, než se zobrazí na stránce a můžete získat předvídatelné chování. Kromě toho se nyní můžete přihlásit k vynucení konkrétních verzí stránek, které umožní vlastní nastavení javascriptu. Chcete-li tuto funkci zapnout, přejděte v tocích uživatelů na stránku **Vlastnosti.**

Další informace o výběru konkrétních verzí prvků stránky najdete v tématu [Azure AD B2C má nyní javascriptové přizpůsobení a mnoho dalších nových funkcí](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blogu.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Konfigurovatelné požadavky na heslo koncového uživatele pro B2C (GA)

**Typ:** Nová funkce  
**Kategorie služeb:** B2C - Správa identit y spotřebitele  
**Možnost i možnosti produktu:** B2B/B2C

Teď můžete nastavit složitost hesla vaší organizace pro koncové uživatele, místo toho, abyste museli používat nativní zásady hesel Azure AD. Z rozhraní **Vlastnosti** toků uživatelů (dříve označované jako integrované zásady) můžete zvolit složitost hesla **Jednoduché** nebo **Silné**nebo můžete vytvořit **vlastní** sadu požadavků.

Další informace o konfiguraci požadavků na složitost hesla naleznete [v tématu Konfigurace požadavků na složitost hesel ve službě Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Nové výchozí šablony pro vlastní prostředí ověřování značky

**Typ:** Nová funkce  
**Kategorie služeb:** B2C - Správa identit y spotřebitele  
**Možnost i možnosti produktu:** B2B/B2C

Pomocí našich nových výchozích šablon umístěných v okně **Rozložení stránky** vašich uživatelských toků (dříve označovaných jako předdefinované zásady) můžete pro uživatele vytvořit vlastní prostředí ověřování pod značkou.

Další informace o používání šablon najdete v [tématu Azure AD B2C má nyní javascriptové přizpůsobení a mnoho dalších nových funkcí](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>Leden 2019

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Spolupráce služby Active Directory B2B pomocí jednorázového ověřování přístupového kódu (Public Preview)

**Typ:** Nová funkce  
**Kategorie služeb:** B2b  
**Možnost i možnosti produktu:** B2B/B2C

Zavedli jsme jednorázové ověřování pomocí hesla (OTP) pro uživatele typu Host b2B, kteří nemohou být ověřeni jinými prostředky, jako je Azure AD, účet Microsoft (MSA) nebo federace Google. Tato nová metoda ověřování znamená, že uživatelé typu Host nemusí vytvářet nový účet Microsoft. Místo toho může uživatel typu Host požádat o dočasné odeslání dočasného kódu na e-mailovou adresu, zatímco uplatní pozvánku nebo přistupuje ke sdílenému prostředku. Pomocí tohoto dočasného kódu může uživatel typu Host pokračovat v přihlášení.

Další informace najdete v [tématu E-mail jednorázové ověřování přístupových kódu (preview)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) a blogu, [Azure AD umožňuje sdílení a spolupráci bezproblémové pro všechny uživatele s libovolným účtem](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Nové nastavení souborů cookie proxy aplikace Azure AD

**Typ:** Nová funkce  
**Kategorie služeb:** Proxy aplikace  
**Možnost i možnosti produktu:** Řízení přístupu

Zavedli jsme tři nová nastavení souborů cookie, která jsou dostupná pro vaše aplikace, která jsou publikována prostřednictvím proxy aplikace:

- **Používejte soubor cookie pouze http.** Nastaví příznak **HTTPOnly** v souborech cookie přístupu a relací proxy aplikace. Zapnutí tohoto nastavení poskytuje další výhody zabezpečení, jako je například pomoc zabránit kopírování nebo úpravám souborů cookie prostřednictvím skriptování na straně klienta. Doporučujeme zapnout tento příznak (zvolte **Ano)** pro další výhody.

- **Používejte zabezpečený soubor cookie.** Nastaví příznak **Zabezpečení** v souborech cookie přístupu a relací proxy aplikace. Zapnutí tohoto nastavení poskytuje další výhody zabezpečení tím, že zajistíte, aby soubory cookie byly přenášeny pouze prostřednictvím zabezpečených kanálů TLS, jako je například protokol HTTPS. Doporučujeme zapnout tento příznak (zvolte **Ano)** pro další výhody.

- **Použijte trvalý soubor cookie.** Zabrání vypršení platnosti přístupových souborů cookie při zavření webového prohlížeče. Tyto soubory cookie vydrží po dobu životnosti přístupového tokenu. Soubory cookie jsou však resetovány, pokud je dosaženo doby vypršení platnosti nebo pokud uživatel ručně odstraní soubor cookie. Doporučujeme ponechat výchozí nastavení **Ne**, pouze zapnutí nastavení pro starší aplikace, které nesdílejí soubory cookie mezi procesy.

Další informace o nových souborech cookie najdete v [tématu Nastavení souborů cookie pro přístup k místním aplikacím ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD – leden 2019

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnosti produktu:** Integrace třetí strany
 
V lednu 2019 jsme do galerie aplikací přidali těchto 35 nových aplikací s podporou Federace:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [Talent Palette](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco Umbrella](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [Zscaler Správce přístupu k Internetu](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), Připomenutí vypršení [platnosti](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [InstaVR Viewer](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [Verb](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso Digital Close](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [Cloud Service PICCO](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [Funkční](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial), [CallPlease](https://webapp.callplease.com/create-account/create-account.html), [GTNexus SSO system](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [ARES for Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 pro Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [Visitly](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [Korn Ferry ALP](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle cSaas Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

Další informace o aplikacích najdete v tématu [Integrace aplikací SaaS se službou Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete [v tématu Seznam aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Nová vylepšení ochrany identity Azure AD (veřejná verze Preview)

**Typ:** Změněná funkce  
**Kategorie služeb:** Ochrana identity  
**Možnost i možnosti produktu:** Ochrana & zabezpečení identity

S potěšením oznamujeme, že jsme do nabídky veřejné verze Azure AD Identity Protection přidali následující vylepšení, včetně:

- Aktualizované a integrovanější uživatelské rozhraní

- Další rozhraní API

- Lepší hodnocení rizik prostřednictvím strojového učení

- Zarovnání celého produktu napříč rizikovými uživateli a rizikovými přihlášeními

Další informace o vylepšeních najdete v tématu [Co je ochrana identity služby Azure Active Directory (aktualizováno)?](https://aka.ms/IdentityProtectionDocs) abyste se dozvěděli více a podělili se o své myšlenky prostřednictvím výzev v produktu.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Nová funkce Uzamčení aplikací pro aplikaci Microsoft Authenticator na zařízeních se systémem iOS a Android

**Typ:** Nová funkce  
**Kategorie služeb:** Microsoft Authenticator App  
**Možnost i možnosti produktu:** Ochrana & zabezpečení identity

Chcete-li, aby byly vaše jednorázové přístupové kódy, informace o aplikacích a nastavení aplikací bezpečnější, můžete zapnout funkci Zámek aplikací v aplikaci Microsoft Authenticator. Zapnutí zámku aplikací znamená, že budete při každém otevření aplikace Microsoft Authenticator požádáni o ověření pomocí kódu PIN nebo biometrického kódu.

Další informace naleznete v [nejčastějších dotazech k aplikaci Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Rozšířené možnosti exportu správy privilegovaných identit Azure AD (PIM)

**Typ:** Nová funkce  
**Kategorie služeb:** Správa privilegovaných identit  
**Možnost i možnosti produktu:** Správa privilegovaných identit

Správci správy privilegovaných identit (PIM) nyní mohou exportovat všechna aktivní a způsobilá přiřazení rolí pro konkrétní zdroj, která zahrnují přiřazení rolí pro všechny podřízené prostředky. Dříve bylo pro správce obtížné získat úplný seznam přiřazení rolí pro předplatné a museli exportovat přiřazení rolí pro každý konkrétní prostředek.

Další informace najdete [v tématu Zobrazení historie aktivit a auditu pro role prostředků Azure v PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

## <a name="novemberdecember-2018"></a>Listopad/prosinec 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Uživatelé odebraní z oboru synchronizace již nepřecházejí na účty pouze pro cloud

**Typ:** Dlouhodobého  
**Kategorie služeb:** Správa uživatelů  
**Možnost i možnosti produktu:** Adresář

>[!Important]
>Slyšeli jsme a pochopit vaši frustraci, protože tato oprava. Proto jsme tuto změnu vrátili zpět do té doby, než vám můžeme usnadnit implementaci opravy ve vaší organizaci.

Opravili jsme chybu, při které by byl příznak DirSyncEnabled uživatele chybně přepnut na **False,** když byl objekt Služby AD DS (Active Directory Domain Services) vyloučen z oboru synchronizace a poté přesunut do koše ve službě Azure AD v následujícím cyklu synchronizace. V důsledku této opravy, pokud je uživatel vyloučen z oboru synchronizace a poté obnoven z koše Služby Azure AD, uživatelský účet zůstane jako synchronizovaný z místní služby AD podle očekávání a nelze jej spravovat v cloudu, protože jeho zdroj autority (SoA) zůstane jako místní služba AD.

Před touto opravou došlo k problému, když byl příznak DirSyncEnabled přepnut na False. To dalo špatný dojem, že tyto účty byly převedeny na objekty pouze pro cloud a že účty by mohly být spravovány v cloudu. Účty však stále zachovány jejich SoA jako místní a všechny synchronizované vlastnosti (stínové atributy) pocházející z místní služby AD. Tento stav způsobil několik problémů ve službě Azure AD a dalších cloudových úlohách (jako je Exchange Online), které očekávaly, že budou tyto účty považovat za synchronizované ze služby AD, ale teď se chovají jako účty pouze pro cloud.

V tuto chvíli jediný způsob, jak skutečně převést účet synchronizované ze služby AD na účet pouze pro cloud je zakázáním DirSync na úrovni klienta, který aktivuje operaci back-endu k přenosu SoA. Tento typ změny SoA vyžaduje (ale není omezena na) čištění všech místních souvisejících atributů (například LastDirSyncTime a stínové atributy) a odeslání signálu do jiných cloudových úloh, aby jeho příslušný objekt převeden y na účet pouze pro cloud.

Tato oprava následně zabraňuje přímé aktualizace atributu ImmutableID uživatele synchronizované ze služby AD, které v některých scénářích v minulosti byly požadovány. Podle návrhu ImmutableID objektu ve službě Azure AD, jak název napovídá, má být neměnné. Nové funkce implementované v Azure AD Connect Health a Azure AD Connect Synchronizační klient jsou k dispozici k řešení těchto scénářů:

- **Rozsáhlá aktualizace ImmutableID pro mnoho uživatelů v fázovaný přístup**
  
  Například je třeba provést zdlouhavou migraci mezi doménovými strukturami služby AD DS. Řešení: Použijte Azure AD Connect ke **konfiguraci zdrojové kotvy** a při migraci uživatele zkopírujte existující hodnoty ImmutableID z Azure AD do místního atributu ms-DS-Consistency-Guid místního uživatele služby AD DS nové doménové struktury. Další informace naleznete [v tématu Using ms-DS-ConsistencyGuid as sourceAnchor](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor).

- **Rozsáhlé aktualizace ImmutableID pro mnoho uživatelů v jednom snímku**

  Například při implementaci Azure AD Connect uděláte chybu a teď je potřeba změnit SourceAnchor atribut. Řešení: Dekonejte DirSync na úrovni klienta a zrušte všechny neplatné hodnoty ImmutableID. Další informace najdete v [tématu Vypnutí synchronizace adresářů pro Office 365](/office365/enterprise/turn-off-directory-synchronization).

- **Odpověď místního uživatele s existujícím uživatelem ve službě Azure AD** Například uživatel, který byl znovu vytvořen ve službě AD DS generuje duplikát v účtu Azure AD namísto jeho opětovného porovnání s existujícím účtem Azure AD (osamocený objekt). Řešení: Pomocí Azure AD Connect Health na webu Azure Portal přemapujte zdrojovou kotvu/Imemitatikum. Další informace naleznete v tématu [Scénář osamoceného objektu](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Narušující změny: Aktualizace schématu protokolů auditu a přihlášení prostřednictvím Azure Monitoru

**Typ:** Změněná funkce  
**Kategorie služeb:** Vykazování  
**Možnost i možnosti produktu:** Monitorování & reportingu

V současné době publikujeme datové proudy protokolů auditu i přihlášení prostřednictvím Azure Monitoru, takže můžete bez problémů integrovat soubory protokolu s nástroji SIEM nebo pomocí Log Analytics. Na základě vaší zpětné vazby a při přípravě na oznámení o obecné dostupnosti této funkce provádíme následující změny našeho schématu. Tyto změny schématu a související aktualizace dokumentace se stane první týden v lednu.

#### <a name="new-fields-in-the-audit-schema"></a>Nová pole ve schématu auditu
Přidáváme nové pole **Typ operace,** které poskytuje typ operace prováděné s zdrojem. Například **Přidat**, **Aktualizovat**nebo **Odstranit**.

#### <a name="changed-fields-in-the-audit-schema"></a>Změněná pole ve schématu auditu
Ve schématu auditování se mění následující pole:

|Název pole|Co se změnilo|Staré hodnoty|Nové hodnoty|
|----------|------------|----------|----------|
|Kategorie|Toto bylo pole **Název služby.** Nyní je to pole **Kategorie auditu.** **Název služby** byl přejmenován na pole **LoggedByService.**|<ul><li>Zřizování účtů</li><li>Základní adresář</li><li>Samoobslužné resetování hesla</li></ul>|<ul><li>Správa uživatelů</li><li>Správa skupin</li><li>Správa aplikací</li></ul>|
|targetZdroje|Zahrnuje **TargetResourceType** na nejvyšší úrovni.|&nbsp;|<ul><li>Zásada</li><li>Aplikace</li><li>Uživatel</li><li>Skupina</li></ul>|
|přihlášenaByService|Obsahuje název služby, která vygenerovala protokol auditu.|Null|<ul><li>Zřizování účtů</li><li>Základní adresář</li><li>Samoobslužné resetování hesla</li></ul>|
|Výsledek|Poskytuje výsledek protokolů auditu. Dříve to byl výčtu, ale nyní zobrazit skutečnou hodnotu.|<ul><li>0</li><li>1</li></ul>|<ul><li>Úspěch</li><li>Selhání</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Změněná pole ve schématu přihlášení
Ve schématu přihlášení se mění následující pole:

|Název pole|Co se změnilo|Staré hodnoty|Nové hodnoty|
|----------|------------|----------|----------|
|použité zásady podmíněného přístupu|Toto bylo pole **conditionalaccessPolicies.** Nyní je to použité pole **ConditionalAccessPolicies.**|Beze změny|Beze změny|
|conditionalAccessStatus|Poskytuje výsledek stav zásad podmíněného přístupu při přihlášení. Dříve to byl výčtu, ale nyní zobrazit skutečnou hodnotu.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Úspěch</li><li>Selhání</li><li>Nepoužito</li><li>Zakázáno</li></ul>|
|appliedConditionalAccessPolicies: výsledek|Poskytuje výsledek jednotlivých podmínek podmíněného přístupu stav zásad při přihlášení. Dříve to byl výčtu, ale nyní zobrazit skutečnou hodnotu.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Úspěch</li><li>Selhání</li><li>Nepoužito</li><li>Zakázáno</li></ul>|

Další informace o schématu najdete v [tématu interpretace schématu protokolů auditu Azure AD v Azure Monitor (preview)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Vylepšení ochrany identity pro model strojového učení pod dohledem a modul skóre rizik

**Typ:** Změněná funkce  
**Kategorie služeb:** Ochrana identity  
**Možnost i možnosti produktu:** Skóre rizika

Vylepšení uživatele souvisejícího s ochranou identity a modulhodnocení rizik přihlášení mohou pomoci zlepšit přesnost a pokrytí rizik uživatelů. Správci si mohou všimnout, že úroveň rizika uživatele již není přímo spojena s úrovní rizika konkrétních zjišťování a že se zvyšuje počet a úroveň rizikových událostí přihlášení.

Detekce rizik jsou nyní vyhodnocovány modelem strojového učení pod dohledem, který vypočítává riziko uživatele pomocí dalších funkcí přihlášení uživatele a vzoru detekcí. Na základě tohoto modelu může správce najít uživatele s vysokým rizikem, i když detekce spojené s tímto uživatelem jsou nízké nebo střední riziko. 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Správci mohou resetovat své vlastní heslo pomocí aplikace Microsoft Authenticator (Public Preview)

**Typ:** Změněná funkce  
**Kategorie služeb:** Samoobslužné resetování hesla  
**Možnost i možnosti produktu:** Ověřování uživatelů

Správci Služby Azure AD teď můžou resetovat vlastní heslo pomocí oznámení aplikace Microsoft Authenticator nebo kódu z libovolné mobilní ověřovací aplikace nebo hardwarového tokenu. Chcete-li obnovit své vlastní heslo, správci budou nyní moci použít dvě z následujících metod:

- Oznámení aplikace Microsoft Authenticator

- Další mobilní ověřovací aplikace / kód hardwarového tokenu

- E-mail

- Telefonát

- Textová zpráva

Další informace o používání aplikace Microsoft Authenticator k resetování hesel najdete v [tématu Samoobslužné resetování hesla služby Azure AD – Mobilní aplikace a Samoobslužná resetovací služba (Preview).](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Nová role správce cloudových zařízení Azure AD (veřejná verze Preview)

**Typ:** Nová funkce  
**Kategorie služeb:** Registrace a správa zařízení  
**Možnost i možnosti produktu:** Řízení přístupu

Správci mohou přiřadit uživatele k nové roli správce cloudových zařízení k provádění úloh správce cloudových zařízení. Uživatelé, kterým je přiřazena role Správci cloudových zařízení, můžou povolit, zakázat a odstranit zařízení ve službě Azure AD spolu s tím, že můžou číst klíče nástroje Windows 10 BitLocker (pokud jsou k dispozici) na webu Azure Portal.

Další informace o rolích a oprávněních najdete v tématu [Přiřazení rolí správce ve službě Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Správa zařízení pomocí nového časového razítka aktivity ve službě Azure AD (Public Preview)

**Typ:** Nová funkce  
**Kategorie služeb:** Registrace a správa zařízení  
**Možnost i možnosti produktu:** Správa životního cyklu zařízení

Uvědomujeme si, že v průběhu času je nutné aktualizovat a vyřadit zařízení vašich organizací ve službě Azure AD, aby se zabránilo zastaralá zařízení ve vašem prostředí. Abychom vám s tímto procesem pomohli, Azure AD teď aktualizuje vaše zařízení pomocí nového časového razítka aktivity, které vám pomůže spravovat životní cyklus zařízení.

Další informace o tom, jak získat a používat toto časové razítko, najdete v [tématu How To: Manage the zastaralé zařízení v Azure AD](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Správci mohou vyžadovat, aby uživatelé přijali podmínky použití na každém zařízení.

**Typ:** Nová funkce  
**Kategorie služeb:** Podmínky použití  
**Možnost i možnosti produktu:** Správy
 
Správci teď můžou **zapnout možnost Vyžadovat, aby uživatelé u každé** možnosti zařízení souhlasili a aby uživatelé přijali vaše podmínky použití na všech zařízeních, které ve vašem tenantovi používají.

Další informace najdete v [části Podmínky použití pro zařízení v funkci podmínek použití služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#per-device-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Správci mohou nakonfigurovat podmínky použití tak, aby vypršely na základě opakovaného plánu.

**Typ:** Nová funkce  
**Kategorie služeb:** Podmínky použití  
**Možnost i možnosti produktu:** Správy
 

Správci nyní mohou zapnout možnost **Vypršení platnosti souhlasů** a na základě zadaného opakovaného plánu vyprší platnost podmínek použití pro všechny uživatele. Plán může být každoročně, dvakrát ročně, čtvrtletně nebo měsíčně. Po vypršení podmínek používání musí uživatelé znovu přijmout.

Další informace najdete v [části Přidání podmínek použití ve funkci podmínek použití služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Správci mohou nakonfigurovat podmínky použití tak, aby vypršely na základě plánu každého uživatele.

**Typ:** Nová funkce  
**Kategorie služeb:** Podmínky použití  
**Možnost i možnosti produktu:** Správy

Správci nyní mohou určit dobu trvání, po kterou musí uživatel znovu přijmout podmínky použití. Správci mohou například určit, že uživatelé musí znovu přijmout podmínky použití každých 90 dní.

Další informace najdete v [části Přidání podmínek použití ve funkci podmínek použití služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Nové e-maily azure ad privilegované správy identit (PIM) pro role Služby Azure Active Directory

**Typ:** Nová funkce  
**Kategorie služeb:** Správa privilegovaných identit  
**Možnost i možnosti produktu:** Správa privilegovaných identit
 
Zákazníci, kteří používají azure ad privilegované správy identit (PIM) nyní můžete přijímat týdenní digest e-mail, včetně následujících informací za posledních sedm dní:

- Přehled nejlepších způsobilých a trvalých přiřazení rolí

- Počet uživatelů, kteří aktivují role

- Počet uživatelů přiřazených k rolím v PIM

- Počet uživatelů přiřazených k rolím mimo PIM

- Počet uživatelů "trvalých" v PIM

Další informace o PIM a dostupných e-mailových oznámeních naleznete [v tématu E-mailová oznámení v PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications).

---

### <a name="group-based-licensing-is-now-generally-available"></a>Skupinové licencování je nyní obecně dostupné

**Typ:** Změněná funkce  
**Kategorie služeb:** Další  
**Možnost i možnosti produktu:** Adresář

Skupinové licencování je mimo verzi Public Preview a je nyní obecně dostupné. V rámci této obecné verze jsme tuto funkci učinili škálovatelnější a přidali jsme možnost znovu zpracovat skupinová licenční přiřazení pro jednoho uživatele a možnost používat skupinové licencování s licencemi Office 365 E3/A3.

Další informace o licencích založených na skupinách najdete v tématu [Co je skupinové licencování ve službě Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD – listopad 2018

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnosti produktu:** Integrace třetí strany
 
V listopadu 2018 jsme do galerie aplikací přidali těchto 26 nových aplikací s podporou Federace:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [Gra-Pe,](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial) [eHour,](https://getehour.com/try-now) [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar,](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview) [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [Infinite Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya](https://alayagood.com/en/demo/), [HeyBuddy,](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial) [Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [Drift](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy pro Business Central 365](https://accounting.zenegy.com/), [Everbridge člen Portál](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [IDEO](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [Allbound SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [Plex Apps - Classic Test](https://test.plexonline.com/signon), Plex Apps - [Classic](https://www.plexonline.com/signon), Plex Apps [- UX Test](https://test.cloud.plex.com/sso), Plex Apps - [UX](https://cloud.plex.com/sso), [Plex Apps - IAM](https://accounts.plex.com/), [CRAFTS - Péče o děti Records, Docházka, & finanční sledovací systém](https://getcrafts.ca/craftsregistration) 

Další informace o aplikacích najdete v tématu [Integrace aplikací SaaS se službou Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete [v tématu Seznam aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

## <a name="october-2018"></a>Říjen 2018

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Protokoly Azure AD teď fungují s Azure Log Analytics (Veřejná preview)

**Typ:** Nová funkce  
**Kategorie služeb:** Vykazování  
**Možnost i možnosti produktu:** Monitorování & reportingu

S nadšením oznamujeme, že teď můžete své protokoly Azure AD předávat do Azure Log Analytics! Tato špičková funkce vám pomůže ještě lépe získat přístup k analýzám pro vaši firmu, provoz a zabezpečení a také způsob, jak zlepšit vaši infrastrukturu. Další informace najdete v [protokolech aktivit Služby Azure Active Directory v Azure Log Analytics, který je teď dostupný.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD – říjen 2018

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnosti produktu:** Integrace třetí strany

V říjnu 2018 jsme do galerie aplikací přidali těchto 14 nových aplikací s podporou Federace:

[My Award Body](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), ambyint, [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), Dialpad, [ON24 Virtuální prostředí](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), [Zscaler tři](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial), [Appraisd](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), [Workspot Control](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

Další informace o aplikacích najdete v tématu [Integrace aplikací SaaS se službou Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete [v tématu Seznam aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-domain-services-email-notifications"></a>E-mailová oznámení služby Azure AD Domain Services

**Typ:** Nová funkce  
**Kategorie služeb:** Služby domény Azure AD  
**Možnost i možnosti produktu:** Služby domény Azure AD

Azure AD Domain Services poskytuje výstrahy na webu Azure Portal o chybných konfiguracích nebo problémech se spravovanou doménou. Tyto výstrahy zahrnují podrobné příručky, takže se můžete pokusit problémy vyřešit, aniž byste museli kontaktovat podporu.

Od října budete moct přizpůsobit nastavení oznámení pro spravovanou doménu, takže když nastanou nová upozornění, bude určeným skupinám lidí odeslán e-mail, což eliminuje potřebu neustále kontrolovat aktualizace na portálu.

Další informace najdete v tématu [Nastavení oznámení ve službě Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Portál Azure AD podporuje použití rozhraní API domény ForceDelete k odstranění vlastních domén 

**Typ:** Změněná funkce  
**Kategorie služeb:** Správa adresářů  
**Možnost i možnosti produktu:** Adresář

S potěšením oznamujeme, že nyní můžete pomocí rozhraní ForceDelete domain API odstranit vlastní názvy domén asynchronním přejmenováním odkazů, jako jsou uživatelé, skupiny a aplikace z vašeho vlastního názvu domény (contoso.com) zpět na původní výchozí název domény (contoso.onmicrosoft.com).

Tato změna vám pomůže rychleji odstranit vlastní názvy domén, pokud vaše organizace již nepoužívá název, nebo pokud potřebujete použít název domény s jiným Azure AD.

Další informace naleznete [v tématu Odstranění vlastního názvu domény](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name).

---

## <a name="september-2018"></a>Září 2018
 
### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Aktualizovaná oprávnění role správce pro dynamické skupiny

**Typ:** Dlouhodobého  
**Kategorie služeb:** Správa skupiny  
**Možnost i možnosti produktu:** Spolupráci

Opravili jsme problém, takže konkrétní role správce teď můžou vytvářet a aktualizovat pravidla dynamického členství, aniž by bylo nutné být vlastníkem skupiny.

Role jsou:

- Globální správce

- Správce Intune

- Správce uživatele

Další informace naleznete [v tématu Vytvoření dynamické skupiny a kontrola stavu](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Zjednodušené nastavení konfigurace jednotného přihlašování (SSO) pro některé aplikace třetích stran

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnost i možnosti produktu:** Sso

Uvědomujeme si, že nastavení aplikací jednotného přihlašování (SSO) pro software jako službu (SaaS) může být náročné vzhledem k jedinečné povaze každé konfigurace aplikací. Vytvořili jsme zjednodušené možnosti konfigurace pro automatické vyplnění nastavení konfigurace přizpůsobování služeb připřimit pro následující aplikace SaaS od jiných výrobců:

- Zendesk

- ArcGis Online

- Jamf Pro

Pokud chcete začít používat toto prostředí jedním kliknutím, přejděte na stránku**konfigurace přihlašování k webu** Azure **Portal** > pro aplikaci. Další informace najdete v tématu [Integrace aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Azure Active Directory – kde se nacházejí vaše data? Stránka

**Typ:** Nová funkce  
**Kategorie služeb:** Další  
**Možnost i možnosti produktu:** PřejítMístní

Vyberte oblast vaší společnosti ze **služby Azure Active Directory – kde se nacházejí vaše data,** abyste zobrazili, ve kterém datovém centru Azure jsou data Azure AD v klidovém stavu pro všechny služby Azure AD. Informace můžete filtrovat podle konkrétních služeb Azure AD pro oblast vaší společnosti.

Přístup k této funkci a další informace naleznete [v tématu Azure Active Directory – kde se nacházejí vaše data](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Nový plán nasazení dostupný pro panel Přístup ke aplikacím

**Typ:** Nová funkce  
**Kategorie služeb:** Moje aplikace  
**Možnost i možnosti produktu:** Sso

Podívejte se na nový plán nasazení, který jehttps://aka.ms/deploymentplans)k dispozici pro panel Přístup ke aplikacím ( .
Panel Přístup ke aplikacím poskytuje uživatelům jediné místo pro vyhledání a přístup ke svým aplikacím. Tento portál také poskytuje uživatelům samoobslužné příležitosti, jako je například žádost o přístup k aplikacím a skupinám nebo správa přístupu k těmto prostředkům jménem jiných.

Další informace najdete [v tématu Co je portál Moje aplikace?](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Nová karta Poradce při potížích a podpora na stránce Přihlášení přihlášení na portálu Azure

**Typ:** Nová funkce  
**Kategorie služeb:** Vykazování  
**Možnost i možnosti produktu:** Monitorování & reportingu

Nová karta **Poradce při potížích a podpora** na stránce Přihlášení **na** webu Azure Portal je určena k tomu, aby správcům a technikům podpory pomohla řešit problémy související s přihlášením k Azure AD. Tato nová karta obsahuje kód chyby, chybovou zprávu a doporučení pro nápravu (pokud existuje) s cílem pomoci problém vyřešit. Pokud se vám nedaří problém vyřešit, poskytneme vám také nový způsob, jak vytvořit lístek podpory pomocí prostředí **Kopírovat do schránky,** které naplní pole **ID požadavku** a datum **(UTC)** pro soubor protokolu v lístku podpory.  

![Protokoly přihlášení zobrazující novou kartu](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Rozšířená podpora vlastních vlastností rozšíření používaných k vytvoření dynamických pravidel členství

**Typ:** Změněná funkce  
**Kategorie služeb:** Správa skupiny  
**Možnost i možnosti produktu:** Spolupráci

Pomocí této aktualizace můžete nyní kliknout na odkaz **Získat vlastní vlastnosti rozšíření** z tvůrce pravidel dynamické skupiny uživatelů, zadat jedinečné ID aplikace a získat úplný seznam vlastních vlastností rozšíření, které se použijí při vytváření pravidla dynamického členství pro uživatele. Tento seznam lze také aktualizovat získat všechny nové vlastní vlastnosti rozšíření pro tuto aplikaci.

Další informace o použití vlastních vlastností rozšíření pro dynamická pravidla členství naleznete v [tématu Vlastnosti rozšíření a vlastní vlastnosti rozšíření](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nové schválené klientské aplikace pro podmíněný přístup založený na aplikacích Azure AD

**Typ:** Plán změn  
**Kategorie služeb:** Podmíněný přístup  
**Možnost i možnosti produktu:** Zabezpečení a ochrana identity

Následující aplikace jsou na seznamu [schválených klientských aplikací](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview):

- Microsoft To-Do

- Microsoft Stream

Další informace naleznete v tématu:

- [Podmíněný přístup založený na aplikacích Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Nová podpora samoobslužného resetování hesla ze zamykací obrazovky Windows 7/8/8.1

**Typ:** Nová funkce  
**Kategorie služeb:** SSPR  
**Možnost i možnosti produktu:** Ověřování uživatelů

Po nastavení této nové funkce se uživatelům zobrazí odkaz pro resetování hesla na **zamykací** obrazovce zařízení se systémem Windows 7, Windows 8 nebo Windows 8.1. Kliknutím na tento odkaz je uživatel veden stejným tokem resetování hesla jako prostřednictvím webového prohlížeče.

Další informace najdete v tématu [Jak povolit resetování hesla ze systému Windows 7, 8 a 8.1](https://aka.ms/ssprforwindows78)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Oznámení o změně: Autorizační kódy již nebudou k dispozici pro opakované použití. 

**Typ:** Plán změn  
**Kategorie služeb:** Ověřování (přihlášení)  
**Možnost i možnosti produktu:** Ověřování uživatelů

listopadu 2018 Azure AD přestane přijímat dříve používané ověřovací kódy pro aplikace. Tato změna zabezpečení pomáhá, aby Azure AD v souladu se specifikací OAuth a bude vynuceno na v1 a v2 koncové body.

Pokud vaše aplikace opakovaně používá autorizační kódy pro získání tokenů pro více prostředků, doporučujeme použít kód k získání aktualizačního tokenu a pak použít tento obnovovací token k získání dalších tokenů pro jiné prostředky. Autorizační kódy lze použít pouze jednou, ale tokeny aktualizace lze použít vícekrát napříč více prostředky. Aplikace, která se pokusí znovu použít ověřovací kód během toku kódu OAuth, získá invalid_grant chybu.

Tyto a další změny související s protokoly naleznete [v úplném seznamu novinek pro ověřování](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD – září 2018

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnosti produktu:** Integrace třetí strany
 
V září 2018 jsme do galerie aplikací přidali těchto 16 nových aplikací s podporou Federace:

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [Comeet Recruiting Software](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial), [Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), [ArcGIS Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), [JDA Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial), [Snowflake](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), NavigoCloud, [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial), join.me, [ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial), [Silverback](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial), Riverbed Xirrus EasyPass, [Rackspace SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial), Enlyft SSO for Azure, SurveyMonkey, [Convene](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial), [dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial)

Další informace o aplikacích najdete v tématu [Integrace aplikací SaaS se službou Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete [v tématu Seznam aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="support-for-additional-claims-transformations-methods"></a>Podpora dalších metod transformace deklarací

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnost i možnosti produktu:** Sso

Zavedli jsme nové metody transformace deklarace, ToLower() a ToUpper(), které lze použít na tokeny SAML ze stránky **jednotné přihlašování založené na** SAML.

Další informace najdete v tématu [Jak přizpůsobit deklarace vydané v tokenu SAML pro podnikové aplikace ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Aktualizované uonfigurované nastavení konfigurace aplikace založené na SAML (náhled)

**Typ:** Změněná funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnost i možnosti produktu:** Sso

Jako součást našeho aktualizovaného konfiguračního usystémého aplikace založeného na SAML získáte:

- Aktualizované možnosti návodu pro konfiguraci aplikací založených na SAML.

- Větší přehled o tom, co chybí nebo je nesprávné ve vaší konfiguraci.

- Možnost přidat více e-mailových adres pro oznámení certifikátu vypršení platnosti.

- Nové metody transformace deklarací, ToLower() a ToUpper(), a další.

- Způsob, jak nahrát vlastní podpisový certifikát tokenu pro podnikové aplikace.

- Způsob, jak nastavit formát NameID pro aplikace SAML a způsob, jak nastavit hodnotu NameID jako rozšíření adresáře.

Pokud chcete toto aktualizované zobrazení zapnout, klikněte na odkaz **Vyzkoušet naše nové prostředí** v horní části stránky **Jednotné přihlašování.** Další informace najdete [v tématu Výuka: Konfigurace jednotného přihlašování na saml pro aplikaci pomocí služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications).

---

## <a name="august-2018"></a>Srpen 2018

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Změny rozsahů IP adres služby Azure Active Directory

**Typ:** Plán změn  
**Kategorie služeb:** Další  
**Možnost i možnosti produktu:** Platforma

Zavádíme větší rozsahy IP adres do Azure AD, což znamená, že pokud jste nakonfigurovali rozsahy IP adres Azure AD pro brány firewall, směrovače nebo skupiny zabezpečení sítě, budete je muset aktualizovat. Tuto aktualizaci provádíme, takže při přidání nových koncových bodů nebude třeba znovu měnit konfigurace oblasti ochrany firewall, směrovače nebo skupin zabezpečení sítě. 

Síťový provoz se přesouvá do těchto nových řad v průběhu příštích dvou měsíců. Chcete-li pokračovat v nepřetržitém provozu, musíte tyto aktualizované hodnoty přidat do adres IP před 10.

- 20.190.128.0/18 

- 40.126.0.0/18 

Důrazně doporučujeme neodstraňovat staré rozsahy IP adres, dokud se veškerý síťový provoz nepřesune do nových rozsahů. Aktualizace o přesunutí a informace o tom, kdy můžete odebrat staré rozsahy, najdete v [tématu Adresy URL office 365 a rozsahy IP adres](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Oznámení o změně: Autorizační kódy již nebudou k dispozici pro opakované použití. 

**Typ:** Plán změn  
**Kategorie služeb:** Ověřování (přihlášení)  
**Možnost i možnosti produktu:** Ověřování uživatelů

listopadu 2018 Azure AD přestane přijímat dříve používané ověřovací kódy pro aplikace. Tato změna zabezpečení pomáhá, aby Azure AD v souladu se specifikací OAuth a bude vynuceno na v1 a v2 koncové body.

Pokud vaše aplikace opakovaně používá autorizační kódy pro získání tokenů pro více prostředků, doporučujeme použít kód k získání aktualizačního tokenu a pak použít tento obnovovací token k získání dalších tokenů pro jiné prostředky. Autorizační kódy lze použít pouze jednou, ale tokeny aktualizace lze použít vícekrát napříč více prostředky. Aplikace, která se pokusí znovu použít ověřovací kód během toku kódu OAuth, získá invalid_grant chybu.

Tyto a další změny související s protokoly naleznete [v úplném seznamu novinek pro ověřování](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).
 
---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Konvergovaná správa bezpečnostních informací pro samoobslužné heslo (SSPR) a vícefaktorové ověřování (MFA)

**Typ:** Nová funkce  
**Kategorie služeb:** SSPR  
**Možnost i možnosti produktu:** Ověřování uživatelů
 
Tato nová funkce pomáhá lidem spravovat jejich bezpečnostní údaje (například telefonní číslo, mobilní aplikace a tak dále) pro sspr a vícefaktorové informace na jednom místě a prostředí; ve srovnání s předchozími, kde to bylo provedeno ve dvou různých místech.

Toto konvergované prostředí funguje také pro lidi, kteří používají sspr nebo vícefaktorové. Navíc pokud vaše organizace nevynucuje registraci vícefaktorové analýzy nebo snop, mohou lidé stále zaregistrovat všechny metody informací o zabezpečení vícefaktorových informací nebo sspr povolené vaší organizací z portálu Moje aplikace.

Toto je opt-in public preview. Správci můžete zapnout nové prostředí (v případě potřeby) pro vybranou skupinu nebo pro všechny uživatele v tenantovi. Další informace o konvergovaném prostředí najdete v [blogu Konvergované zkušenosti](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Nové nastavení souborů cookie pouze http v aplikacích proxy aplikací Azure AD

**Typ:** Nová funkce  
**Kategorie služeb:** Proxy aplikace  
**Možnost i možnosti produktu:** Řízení přístupu

V aplikacích Proxy aplikace se nazývá nové nastavení s názvem **Soubory cookie pouze http.** Toto nastavení pomáhá zajistit další zabezpečení zahrnutím příznaku HTTPOnly do hlavičky odpovědi HTTP pro přístup k serveru cookie proxy aplikace i pro soubory cookie relace, zastavení přístupu k souboru cookie ze skriptu na straně klienta a další zabránění akcím, jako je kopírování nebo úprava souboru cookie. I když tento příznak nebyl dříve použit, vaše soubory cookie byly vždy šifrovány a přenášeny pomocí připojení TLS, aby se zabránilo nesprávným úpravám.

Toto nastavení není kompatibilní s aplikacemi používajícími ovládací prvky ActiveX, jako je vzdálená plocha. Pokud se v této situaci uvidíte, doporučujeme toto nastavení vypnout.

Další informace o nastavení souborů cookie pouze http, najdete [v tématu publikování aplikací pomocí proxy aplikace Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Privilegovaná správa identit (PIM) pro prostředky Azure podporuje typy prostředků skupiny managementu

**Typ:** Nová funkce  
**Kategorie služeb:** Správa privilegovaných identit  
**Možnost i možnosti produktu:** Správa privilegovaných identit
 
Nastavení aktivace a přiřazení just-in-time lze teď použít na typy prostředků skupiny pro správu, stejně jako to už děláte u předplatných, skupin prostředků a prostředků (jako jsou virtuální počítače, služby App Services a další). Kromě toho může kdokoli s rolí, která poskytuje přístup správce pro skupinu pro správu, zjistit a spravovat tento prostředek v PIM.

Další informace o pim a azure prostředky, najdete [v tématu zjišťování a správa prostředků Azure pomocí privileged identity management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources)
 
---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>Přístup k aplikacím (preview) poskytuje rychlejší přístup k portálu Azure AD

**Typ:** Nová funkce  
**Kategorie služeb:** Správa privilegovaných identit  
**Možnost i možnosti produktu:** Správa privilegovaných identit
 
Dnes při aktivaci role pomocí PIM může trvat více než 10 minut, než se oprávnění projeví. Pokud se rozhodnete použít přístup k aplikacím, který je aktuálně ve verzi Public Preview, správci mají přístup k portálu Azure AD, jakmile se žádost o aktivaci dokončí.

V současné době přístup k aplikacím podporuje jenom prostředí portálu Azure AD a prostředky Azure. Další informace o přístupu PIM a aplikací, najdete [v tématu Co je Azure AD privilegované správy identit?](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)
 
---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD – srpen 2018

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnosti produktu:** Integrace třetí strany
 
V srpnu 2018 jsme do galerie aplikací přidali těchto 16 nových aplikací s podporou Federace:

[Hornbill](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial), [Bridgeline Unbound](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial), [Sauce Labs - Mobilní a webové testování](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial), Meta Networks [Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial), Way [We Do](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial), [Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial), [ProMaster (podle Inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial), SchoolBooking, [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial), [Dokumentace](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial), [N2F - Expense reports](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial), [Comm100 Live Chat](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial), [SafeConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial), [ZenQMS](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial), [eLuminate](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial), [Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial).

Další informace o aplikacích najdete v tématu [Integrace aplikací SaaS se službou Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete [v tématu Seznam aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>Nativní podpora Tableau je teď dostupná v proxy aplikacích Azure AD.

**Typ:** Změněná funkce  
**Kategorie služeb:** Proxy aplikace  
**Možnost i možnosti produktu:** Řízení přístupu

S naší aktualizací z OpenID Connect na protokol OAuth 2.0 Code Grant pro náš předověřovací protokol již nemusíte dělat žádnou další konfiguraci, abyste mohli používat Tableau s aplikačním proxy serverem. Tato změna protokolu také pomáhá proxy aplikacím lépe podporovat modernější aplikace pomocí pouze přesměrování HTTP, která jsou běžně podporována ve značkách JavaScript a HTML.

Další informace o naší nativní podpoře pro Tableau najdete v [tématu Proxy aplikací Azure AD nyní s nativní podporou Tableau](https://blogs.technet.microsoft.com/applicationproxyblog/2018/08/14/azure-ad-application-proxy-now-with-native-tableau-support).

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Nová podpora pro přidání Google jako poskytovatele identity pro uživatele typu Host B2B ve službě Azure Active Directory (preview)

**Typ:** Nová funkce  
**Kategorie služeb:** B2b  
**Možnost i možnosti produktu:** B2B/B2C

Nastavením federace se společností Google ve vaší organizaci můžete pozvaným uživatelům Gmailu povolit přihlášení ke sdíleným aplikacím a prostředkům pomocí stávajícího účtu Google, aniž byste museli vytvářet osobní účet Microsoft (MSA) nebo účet Azure AD.

Toto je opt-in public preview. Další informace o federaci Google najdete v [tématu Přidání Googlu jako poskytovatele identity pro uživatele typu Host B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

## <a name="july-2018"></a>Červenec 2018

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Vylepšení e-mailových oznámení služby Azure Active Directory

**Typ:** Změněná funkce  
**Kategorie služeb:** Další  
**Možnost i možnosti produktu:** Správa životního cyklu identity
 
E-maily služby Azure Active Directory (Azure AD) jsou nyní vybaveny aktualizovaným návrhem a také změnami e-mailové adresy odesílatele a zobrazovaným názvem odesílatele při odeslání z následujících služeb:
 
- Recenze přístupu k Azure AD
- Azure AD Connect Health 
- Azure AD Identity Protection 
- Azure AD Privileged Identity Management
- Oznámení o certifikátu s platností certifikátu podnikové aplikace
- Oznámení služby zřizování podnikových aplikací
 
E-mailová oznámení budou odeslána z následující e-mailové adresy a zobrazované jméno:

- E-mailová adresa:azure-noreply@microsoft.com
- Zobrazovaný název: Microsoft Azure
 
Příklad některých nových návrhů e-mailů a další informace najdete [v tématu E-mailová oznámení v azure ad PIM](https://go.microsoft.com/fwlink/?linkid=2005832).

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Protokoly aktivit Azure AD jsou teď dostupné prostřednictvím Azure Monitoru

**Typ:** Nová funkce  
**Kategorie služeb:** Vykazování  
**Možnost i možnosti produktu:** Monitorování & reportingu

Protokoly aktivit Azure AD jsou teď dostupné ve verzi Public Preview pro Azure Monitor (služba monitorování celé platformy Azure). Azure Monitor nabízí dlouhodobé uchovávání a bezproblémovou integraci, kromě těchto vylepšení:

- Dlouhodobá uchovávání směrováním souborů protokolu do vlastního účtu úložiště Azure.

- Bezproblémová integrace SIEM, aniž byste museli psát nebo udržovat vlastní skripty.

- Bezproblémová integrace s vlastními řešeními, analytickými nástroji nebo řešeními pro správu incidentů.

Další informace o těchto nových funkcích najdete v našem blogu [protokoly aktivit Azure AD v diagnostice Azure Monitoru je teď ve verzi Public Preview](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) a naše dokumentace, [protokoly aktivit Služby Azure Active Directory ve službě Azure Monitor (preview).](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview)

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Informace podmíněného přístupu přidané do sestavy přihlášení azure ad

**Typ:** Nová funkce  
**Kategorie služeb:** Vykazování  
**Možnost i možnosti produktu:** Ochrana & zabezpečení identity
 
Tato aktualizace umožňuje zobrazit, které zásady jsou vyhodnocovány, když se uživatel přihlásí spolu s výsledkem zásad. Kromě toho sestava nyní obsahuje typ klientské aplikace používané uživatelem, takže můžete identifikovat přenosy starších protokolů. Položky sestavy lze nyní také vyhledávat pro ID korelace, které lze nalézt v chybové zprávě pro uživatele a lze je použít k identifikaci a řešení potíží s odpovídajícím požadavkem na přihlášení.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Zobrazení starších ověřování prostřednictvím protokolů aktivit přihlášení

**Typ:** Nová funkce  
**Kategorie služeb:** Vykazování  
**Možnost i možnosti produktu:** Monitorování & reportingu
 
Se zavedením pole **Klientská aplikace** v protokolech přihlašovacích aktivit zákazníci nyní mohou zobrazit uživatele, kteří používají starší verze ověřování. Zákazníci budou mít přístup k těmto informacím pomocí rozhraní Microsoft Graph API pro přihlášení nebo prostřednictvím protokolů přihlašovacích aktivit na portálu Azure AD, kde můžete pomocí ovládacího prvku **Klientské aplikace** filtrovat starší ověřování. Další podrobnosti naleznete v dokumentaci.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD – červenec 2018

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnosti produktu:** Integrace třetí strany
 
V červenci 2018 jsme do galerie aplikací přidali těchto 16 nových aplikací s podporou Federace:

[Innovation Hub](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial), [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial), [Některé Admin SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial), PSUC Staging, [iPass SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial), [Screencast-O-Matic](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial), PowerSchool Sjednocená učebna, [Eli Onboarding](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial), [Bomgar vzdálená podpora,](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial) [Nimblex](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial), [Imagineer WebVision](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial), [Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial), [SecureW2 JoinNow Konektor](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial), [Kanbanize](../saas-apps/kanbanize-tutorial.md), [SmartLPA](../saas-apps/smartlpa-tutorial.md), [Skills Base](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

Další informace o aplikacích najdete v tématu [Integrace aplikací SaaS se službou Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete [v tématu Seznam aplikace v galerii aplikací Azure Active Directory](https://aka.ms/azureadapprequest).

---
 
### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Integrace aplikací SaaS pro zřizování nových uživatelů – červenec 2018

**Typ:** Nová funkce  
**Kategorie služeb:** Zřizování aplikací  
**Možnosti produktu:** Integrace třetí strany
 
Azure AD umožňuje automatizovat vytváření, údržbu a odebrání identit uživatelů v aplikacích SaaS, jako je Dropbox, Salesforce, ServiceNow a další. Pro červenec 2018 jsme přidali podporu zřizování uživatelů pro následující aplikace v galerii aplikací Azure AD:

- [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Bonusly](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

Seznam všech aplikací, které podporují zřizování uživatelů v galerii Azure AD, najdete v [tématu integrace aplikací SaaS s Azure Active Directory](https://aka.ms/appstutorial).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Stav připojení pro synchronizaci – jednodušší způsob, jak opravit osamocené chyby synchronizace atributů a duplicitních atributů

**Typ:** Nová funkce  
**Kategorie služeb:** Připojení služby AD  
**Možnost i možnosti produktu:** Monitorování & reportingu
 
Azure AD Connect Health zavádí samoobslužné nápravy, které vám pomohou zvýraznit a opravit chyby synchronizace. Tato funkce odstraňuje duplicitní chyby synchronizace atributů a opravuje objekty, které jsou osamocené z Azure AD. Tato diagnóza má následující výhody:

- Zúží duplicitní chyby synchronizace atributů a poskytuje konkrétní opravy

- Použije opravu pro vyhrazené scénáře Azure AD, řešení chyb v jednom kroku

- K zapnutí a používání této funkce není nutný žádný upgrade ani konfigurace.

Další informace naleznete v [tématu Diagnostika a náprava duplicitních chyb synchronizace atributů](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Vizuální aktualizace přihlašovacích prostředí Azure AD a MSA

**Typ:** Změněná funkce  
**Kategorie služeb:** Azure AD  
**Možnost i možnosti produktu:** Ověřování uživatelů

Aktualizovali jsme uživatelské rozhraní pro online služby Microsoftu s přihlášením, jako je například pro Office 365 a Azure. Díky této změně jsou obrazovky méně přeplněné a přímočařejší. Další informace o této změně najdete v tématu nadcházející vylepšení blogu [prostředí přihlášení Azure AD.](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/)

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Nová verze Azure AD Connect – červenec 2018

**Typ:** Změněná funkce  
**Kategorie služeb:** Zřizování aplikací  
**Možnost i možnosti produktu:** Správa životního cyklu identity

Nejnovější verze Azure AD Connect zahrnuje: 

- Opravy chyb a aktualizace podpory 

- Obecná dostupnost integrace Ping-Federate

- Aktualizace nejnovějšího klienta SQL 2012 

Další informace o této aktualizaci najdete [v tématu Azure AD Connect: Historie verzí verzí](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history)

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>Aktualizace podmínek použití uživatelského rozhraní koncového uživatele

**Typ:** Změněná funkce  
**Kategorie služeb:** Podmínky použití  
**Možnost i možnosti produktu:** Správy

Aktualizujeme řetězec přijetí v uživatelském rozhraní koncového uživatele tou.

**Aktuální text.** Chcete-li získat přístup k prostředkům [tenantName], musíte přijmout podmínky použití.<br>**Nový text.** Chcete-li získat přístup k prostředku [tenantName], musíte si přečíst podmínky použití.

**Aktuální text:** Volba přijmout znamená, že souhlasíte se všemi výše uvedenými podmínkami použití.<br>**Nový text:** Chcete-li potvrdit, že jste si přečetli podmínky použití a porozuměli vám, klepněte na tlačítko Přijmout.

---
 
### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>Předávací ověřování podporuje starší protokoly a aplikace

**Typ:** Změněná funkce  
**Kategorie služeb:** Ověřování (přihlášení)  
**Možnost i možnosti produktu:** Ověřování uživatelů
 
Předávací ověřování teď podporuje starší protokoly a aplikace. Následující omezení jsou nyní plně podporována:

- Přihlášení uživatelů ke starším klientským aplikacím Office, Office 2010 a Office 2013, bez nutnosti moderního ověřování.

- Přístup ke sdílení kalendáře a informacím o volném čase v hybridních prostředích Exchange pouze v Office 2010.

- Přihlášení uživatelů ke klientským aplikacím Skypu pro firmy bez nutnosti moderního ověřování.

- Přihlášení uživatele k prostředí PowerShell verze 1.0.

- Program registrace zařízení Apple (Apple DEP) pomocí Pomocníka pro nastavení iOS. 

---
 
### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Konvergovaná správa bezpečnostních informací pro samoobslužné resetování hesla a vícefaktorové ověřování

**Typ:** Nová funkce  
**Kategorie služeb:** SSPR  
**Možnost i možnosti produktu:** Ověřování uživatelů

Tato nová funkce umožňuje uživatelům spravovat jejich bezpečnostní údaje (například telefonní číslo, e-mailovou adresu, mobilní aplikaci a tak dále) pro samoobslužné resetování hesla (SSPR) a vícefaktorové ověřování (MFA) v jednom prostředí. Uživatelé již nebudou muset registrovat stejné bezpečnostní údaje pro samoohra a vícefaktorové informace ve dvou různých prostředích. Toto nové prostředí platí také pro uživatele, kteří mají sspr nebo vícefaktorové finanční hod.

Pokud organizace nevynucuje registraci vícefaktorové registrace nebo snop, mohou uživatelé zaregistrovat své bezpečnostní údaje prostřednictvím portálu **Moje aplikace.** Odtud mohou uživatelé zaregistrovat všechny metody povolené pro vícefaktorové nebo sspr. 

Toto je opt-in public preview. Správci můžete zapnout nové prostředí (v případě potřeby) pro vybranou skupinu uživatelů nebo všechny uživatele v tenantovi.

---
 
### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Ověření identity při resetování hesla pomocí aplikace Microsoft Authenticator

**Typ:** Změněná funkce  
**Kategorie služeb:** SSPR  
**Možnost i možnosti produktu:** Ověřování uživatelů

Tato funkce umožňuje nesprávcům ověřit jejich identitu při resetování hesla pomocí oznámení nebo kódu od microsoft authenticator (nebo jiné ověřovací aplikace). Poté, co správci zapnou tuto samoobslužnou metodu resetování hesla, mohou uživatelé, kteří zaregistrovali mobilní aplikaci prostřednictvím aka.ms/mfasetup nebo aka.ms/setupsecurityinfo, používat svou mobilní aplikaci jako metodu ověření při resetování hesla.

Oznámení o mobilní aplikaci lze zapnout pouze jako součást zásady, která k resetování hesla vyžaduje dvě metody.

---

## <a name="june-2018"></a>Červen 2018

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Oznámení o změně: Oprava zabezpečení delegovaného toku autorizace pro aplikace pomocí rozhraní API protokolů aktivit Azure AD

**Typ:** Plán změn  
**Kategorie služeb:** Vykazování  
**Možnost i možnosti produktu:** Monitorování & reportingu

Kvůli našemu silnějšímu vynucení zabezpečení jsme museli změnit oprávnění pro aplikace, které používají delegovaný tok autorizace pro přístup k [souborům API protokolů aktivit Azure AD](https://aka.ms/aadreportsapi). K této změně dojde do **26.6.2018**.

Pokud některá z vašich aplikací používá api protokolu aktivit Azure AD, postupujte takto, abyste zajistili, že se aplikace po změně nepřeruší.

**Aktualizace oprávnění aplikace**

1. Přihlaste se k portálu Azure, vyberte **Azure Active Directory**a pak vyberte Registrace **aplikací**.
2. Vyberte aplikaci, která používá rozhraní API protokolů aktivit Azure AD, vyberte **Nastavení**, vyberte **Požadovaná oprávnění**a pak vyberte rozhraní **API služby Windows Azure Active Directory.**
3. V oblasti **Delegovaných oprávnění** v okně **Povolit přístup** zaškrtněte políčko vedle **položky Číst** data adresáře a pak vyberte **Uložit**.
4. Vyberte **Udělit oprávnění**a pak vyberte **Ano**.
    
    >[!Note]
    >Chcete-li aplikaci udělit oprávnění, musíte být globálním správcem.

Další informace najdete v oblasti [Udělení oprávnění](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-prerequisites-azure-portal#grant-permissions) požadavky pro přístup k článku rozhraní API pro vytváření sestav Azure AD.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>Konfigurace nastavení TLS pro připojení ke službám Azure AD pro dodržování předpisů PCI DSS

**Typ:** Nová funkce  
**Kategorie služeb:** N/a  
**Možnost i možnosti produktu:** Platforma

Zabezpečení transportní vrstvy (TLS) je protokol, který poskytuje ochranu osobních údajů a integritu dat mezi dvěma komunikujícími aplikacemi a je nejrozšířenějším dnes používaným bezpečnostním protokolem.

[Rada pro bezpečnostní standardy PCI](https://www.pcisecuritystandards.org/) rozhodla, že rané verze TLS a SSL (SL) musí být zakázány ve prospěch povolení nových a bezpečnějších aplikačních protokolů, s dodržováním předpisů počínaje **červnem 30, 2018**. Tato změna znamená, že pokud se připojíte ke službám Azure AD a budete vyžadovat dodržování předpisů PCI DSS, musíte zakázat TLS 1.0. K dispozici je více verzí protokolu TLS, ale tls 1.2 je nejnovější verze dostupná pro službu Azure Active Directory Services. Důrazně doporučujeme přejít přímo na TLS 1.2 pro kombinaci klient/server i prohlížeč/server.

Zastaralé prohlížeče nemusí podporovat novější verze TLS, například TLS 1.2. Chcete-li zjistit, které verze systému TLS váš prohlížeč podporuje, přejděte na web [Qualys SSL Labs](https://www.ssllabs.com/) a klepněte na tlačítko **Otestovat prohlížeč**. Doporučujeme upgradovat na nejnovější verzi webového prohlížeče a pokud možno povolit pouze TLS 1.2.

**Chcete-li povolit TLS 1.2, podle prohlížeče**

- **Microsoft Edge a Internet Explorer (obě jsou nastaveny pomocí aplikace Internet Explorer)**

    1. Sem internet explorer, vyberte **Nástroje** > **Možnosti** > Internetu**Upřesnit**.
    2. V oblasti **Zabezpečení** vyberte **použít TLS 1.2**a pak vyberte **OK**.
    3. Zavřete všechna okna prohlížeče a restartujte aplikaci Internet Explorer. 

- **Google Chrome**

    1. Otevřete Google Chrome, zadejte *chrome://settings/* do adresního řádku a stiskněte **Enter**.
    2. Rozbalte **rozšířené možnosti,** přejděte do oblasti **Systém** a vyberte Otevřít nastavení **proxy serveru**.
    3. V poli **Vlastnosti Internetu** vyberte kartu **Upřesnit,** přejděte do oblasti **Zabezpečení,** vyberte **použít TLS 1.2**a pak vyberte **OK**.
    4. Zavřete všechna okna prohlížeče a restartujte prohlížeč Google Chrome.

- **Mozilla Firefox**

    1. Otevřete Firefox, do adresního řádku zadejte *about:config* a stiskněte **Enter**.
    2. Vyhledejte termín *TLS*a vyberte položku **security.tls.version.max.**
    3. Nastavte hodnotu na **3,** chcete-li prohlížeč použít až do verze TLS 1.2, a pak vyberte **OK**.

        >[!NOTE]
        >Firefox verze 60.0 podporuje TLS 1.3, takže můžete také nastavit hodnotu security.tls.version.max na **4**.

    4. Zavřete všechna okna prohlížeče a restartujte Mozilla Firefox.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD – červen 2018

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnosti produktu:** Integrace třetí strany
 
V červnu 2018 jsme do galerie aplikací přidali těchto 15 nových aplikací s podporou Federace:

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial), [Usazovací hudba](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial), [SAML 1.1 Token povoleno LOB App](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial), [Supermood](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial), [Autotask](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Endpoint Backup](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Skyhigh Networks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial), Smartway2, [TonicDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial), [Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial), [Zoho One](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial), [SharePoint on-premises](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial), [ForeSee CX Suite](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial), [Vidyard](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial), [ChronicX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

Další informace o aplikacích najdete v tématu [Integrace aplikací SaaS se službou Azure Active Directory](https://aka.ms/appstutorial). Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete [v tématu Seznam aplikace v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Azure AD Password Protection je k dispozici ve verzi Public Preview

**Typ:** Nová funkce  
**Kategorie služeb:** Ochrana identity  
**Možnost i možnosti produktu:** Ověřování uživatelů

Pomocí azure ad ochrana heslem pomoci eliminovat snadno uhodnout hesla z vašeho prostředí. Odstranění těchto hesel pomáhá snížit riziko ohrožení zabezpečení z typu útoku typu stříkacího spreje heslem.

Konkrétně Azure AD Password Protection vám pomůže:

- Chraňte účty vaší organizace ve službě Azure AD i ve službě AD systému Windows Server. 
- Zabrání uživatelům používat hesla v seznamu více než 500 nejčastěji používaných hesel a více než 1 milion variant nahrazení znaků těchto hesel.
- Spravujte Azure AD Password Protection z jednoho umístění na portálu Azure AD, a to jak pro Azure AD, tak pro místní Windows Server AD.

Další informace o azure ad ochrana heslem, najdete v [tématu odstranění chybných hesel ve vaší organizaci](https://aka.ms/aadpasswordprotectiondocs).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Nová šablona zásad podmíněného přístupu "všichni hosté" vytvořená během vytváření podmínek použití

**Typ:** Nová funkce  
**Kategorie služeb:** Podmínky použití  
**Možnost i možnosti produktu:** Správy

Při vytváření podmínek použití se vytvoří nová šablona zásad podmíněného přístupu také pro "všechny hosty" a "všechny aplikace". Tato nová šablona zásad používá nově vytvořené tou, zefektivnění procesu vytváření a vynucování pro hosty.

Další informace naleznete v [tématu Azure Active Directory Podmínky použití funkce](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Nová šablona zásad "vlastní" podmíněný přístup vytvořená během vytváření podmínek použití

**Typ:** Nová funkce  
**Kategorie služeb:** Podmínky použití  
**Možnost i možnosti produktu:** Správy

Při vytváření podmínek použití je také vytvořena nová šablona zásad "vlastní" podmíněný přístup. Tato nová šablona zásad umožňuje vytvořit tou a okamžitě přejít na vytvoření zásad podmíněného přístupu, aniž byste museli ručně procházet portálem.

Další informace naleznete v [tématu Azure Active Directory Podmínky použití funkce](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>Nové a komplexní pokyny k nasazení azure multifaktorového ověřování

**Typ:** Nová funkce  
**Kategorie služeb:** Další  
**Možnost i možnosti produktu:** Ochrana & zabezpečení identity
 
Vydali jsme nové podrobné pokyny o tom, jak nasadit Azure Multi-Factor Authentication (MFA) ve vaší organizaci.

Chcete-li zobrazit průvodce nasazením mfa, přejděte na úložiště [průvodců nasazení identity](https://aka.ms/DeploymentPlans) na GitHubu. Chcete-li poskytnout zpětnou vazbu k průvodcům nasazení, použijte [formulář Zpětná vazba plánu nasazení](https://aka.ms/deploymentplanfeedback). Máte-li jakékoli dotazy týkající se průvodce nasazením, kontaktujte nás na [adrese IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Role správy delegovaných aplikací Azure AD jsou ve verzi Public Preview

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnost i možnosti produktu:** Řízení přístupu

Správci teď můžou delegovat úlohy správy aplikací bez přiřazení role globálního správce. Nové role a možnosti jsou:

- **Nové standardní role správce Azure AD:**

    - **Správce aplikace.** Uděluje možnost spravovat všechny aspekty všech aplikací, včetně registrace, nastavení připřiponesení k sociálnímu zabezpečení, přiřazení aplikací a licencování, nastavení proxy aplikace a souhlasu (s výjimkou prostředků Azure AD).

    - **Správce cloudových aplikací.** Uděluje všechny možnosti správce aplikace, s výjimkou proxy aplikace, protože neposkytuje místní přístup.

    - **Vývojář aplikací.** Uděluje možnost vytvářet registrace aplikací, i když je vypnutá možnost **povolit uživatelům registraci aplikací.**

- **Vlastnictví (nastavení registrace podle aplikací a aplikace pro podniky, podobně jako proces vlastnictví skupiny:**
 
    - **Vlastník registrace aplikace.** Uděluje možnost spravovat všechny aspekty registrace vlastněných aplikací, včetně manifestu aplikace a přidání dalších vlastníků.

    - **Vlastník podnikové aplikace.** Uděluje možnost spravovat mnoho aspektů vlastněných podnikových aplikací, včetně nastavení připřiponek, přiřazení aplikací a souhlasu (s výjimkou prostředků Azure AD).

Další informace o verzi Public Preview najdete v tématu [Azure AD delegované role správy aplikací jsou ve verzi Public Preview!](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) Blog. Další informace o rolích a oprávněních najdete [v tématu Přiřazení rolí správce ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal).

---

## <a name="may-2018"></a>Květen 2018

### <a name="expressroute-support-changes"></a>Změny podpory ExpressRoute

**Typ:** Plán změn  
**Kategorie služeb:** Ověřování (přihlášení)  
**Možnost i možnosti produktu:** Platforma  

Nabídka softwaru jako služby, jako je Azure Active Directory (Azure AD), jsou navržené tak, aby fungovaly nejlépe, a to tak, že půjdete přímo přes Internet, aniž byste museli vyžadovat ExpressRoute nebo jiné privátní tunely VPN. Z tohoto **důvodu v srpnu 1, 2018**, přestaneme podporovat ExpressRoute pro služby Azure AD pomocí azure public peering a Azure komunity v partnerské společnosti Microsoft. Všechny služby ovlivněné touto změnou může všimnout, provoz Azure AD postupně přesouvá z ExpressRoute na Internet.

Zatímco měníme naši podporu, víme také, že stále existují situace, kdy budete muset použít vyhrazenou sadu obvodů pro váš ověřovací provoz. Z tohoto důvodu bude Azure AD nadále podporovat omezení rozsahu IP adres pro jednotlivé klienty pomocí ExpressRoute a služeb, které už jsou v partnerské společnosti Microsoft s komunitou "Ostatní služby Office 365 Online". Pokud jsou vaše služby ovlivněny, ale vyžadují ExpressRoute, musíte provést následující kroky:

- **Pokud jste na Azure veřejné partnerský vztah.** Přejděte do partnerského vztahu Microsoftu a zaregistrujte se do komunity **ostatních služeb Office 365 Online (12076:5100).** Další informace o tom, jak přejít z veřejného partnerského vztahu Azure do partnerského vztahu Microsoftu, najdete v článku [Přesunutí veřejného partnerského vztahu do partnerského vztahu Microsoftu.](https://docs.microsoft.com/azure/expressroute/how-to-move-peering)

- **Pokud jste na microsoft peeringu.** Zaregistrujte se ke **komunitě jiné služby Office 365 Online (12076:5100).** Další informace o požadavcích na směrování naleznete v [části Podpora komunit Protokolu BGP](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp) v článku Požadavky na směrování expressroute.

Pokud musíte dál používat vyhrazené okruhy, budete si muset promluvit se svým týmem účtu Microsoft o tom, jak získat oprávnění k používání komunity **jiné služby Office 365 Online (12076:5100).** Revizní komise spravovaná ms Office ověří, zda tyto obvody potřebujete, a ujistí se, že rozumíte technickým důsledkům jejich uchovávání. Neautorizovaná předplatná, která se pokoušejí vytvořit filtry tras pro Office 365, se zobrazí chybová zpráva. 
 
---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>Rozhraní API aplikace Microsoft Graph pro scénáře správy pro tou

**Typ:** Nová funkce  
**Kategorie služeb:** Podmínky použití  
**Možnost i možnosti produktu:** Vývojářské prostředí
 
Přidali jsme rozhraní API Microsoft Graphu pro správu podmínek použití Azure AD. Můžete vytvořit, aktualizovat, odstranit podmínky použití objektu.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Přidání koncového bodu azure ad pro více klientů jako zprostředkovatele identity ve Službě Azure AD B2C

**Typ:** Nová funkce  
**Kategorie služeb:** B2C - Správa identit y spotřebitele  
**Možnost i možnosti produktu:** B2B/B2C
 
Pomocí vlastních zásad teď můžete přidat společný koncový bod Azure AD jako poskytovatele identity v Azure AD B2C. To umožňuje mít jeden vstupní bod pro všechny uživatele Azure AD, kteří se přihlašují do vašich aplikací. Další informace najdete [v tématu Azure Active Directory B2C: Povolit uživatelům přihlásit se k víceklientského zprostředkovatele identity Azure AD pomocí vlastních zásad](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-commonaad-custom).

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Pomocí interních adres URL můžete přistupovat k aplikacím odkudkoli s naším rozšířením o přihlášení ke službě Moje aplikace a proxy aplikací Azure AD

**Typ:** Nová funkce  
**Kategorie služeb:** Moje aplikace  
**Možnost i možnosti produktu:** Sso
 
Uživatelé teď můžou přistupovat k aplikacím prostřednictvím interních adres URL, i když jsou mimo vaši podnikovou síť, a to pomocí rozšíření Zabezpečení přihlašování moje aplikace pro Azure AD. To bude fungovat s jakoukoli aplikaci, kterou jste publikovali pomocí proxy aplikace Azure AD, v libovolném prohlížeči, který má také nainstalované rozšíření prohlížeče přístupového panelu. Funkce přesměrování adresy URL je automaticky povolena, jakmile se uživatel přihlásí k rozšíření. Rozšíření je k dispozici ke stažení na [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176), [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)a [Firefox](https://go.microsoft.com/fwlink/?linkid=866366).

---
 
### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory – Data v Evropě pro evropské zákazníky

**Typ:** Nová funkce  
**Kategorie služeb:** Další  
**Možnost i možnosti produktu:** PřejítMístní

Zákazníci v Evropě vyžadují, aby jejich data zůstala v Evropě a nebyla replikována mimo evropská datacentra, aby splnili zákony na ochranu soukromí a evropské zákony. Tento [článek](https://go.microsoft.com/fwlink/?linkid=872328) obsahuje konkrétní podrobnosti o tom, jaké informace o identitě budou uloženy v rámci Evropy a také podrobnosti o informacích, které budou uloženy mimo evropská datacentra. 

---
 
### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Integrace aplikací SaaS pro zřizování nových uživatelů – květen 2018

**Typ:** Nová funkce  
**Kategorie služeb:** Zřizování aplikací  
**Možnosti produktu:** Integrace třetí strany
 
Azure AD umožňuje automatizovat vytváření, údržbu a odebrání identit uživatelů v aplikacích SaaS, jako je Dropbox, Salesforce, ServiceNow a další. Pro květen 2018 jsme přidali podporu zřizování uživatelů pro následující aplikace v galerii aplikací Azure AD:

- [BlueJeans](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bluejeans-provisioning-tutorial)

- [Cornerstone OnDemand](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Zendesk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

Seznam všech aplikací, které podporují zřizování uživatelů v [https://aka.ms/appstutorial](https://aka.ms/appstutorial)galerii Azure AD, najdete v tématu .

---
 
### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>Azure AD přístup recenze skupin a přístup k aplikacím nyní poskytuje opakované recenze

**Typ:** Nová funkce  
**Kategorie služeb:** Recenze přístupu  
**Možnost i možnosti produktu:** Správy
 
Kontrola přístupu skupin a aplikací je teď obecně dostupná jako součást Azure AD Premium P2.  Správci budou moci konfigurovat kontroly přístupu členství ve skupinách a přiřazení aplikací tak, aby se automaticky opakovaly v pravidelných intervalech, například měsíčně nebo čtvrtletně.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Protokoly aktivit Azure AD (přihlášení a audit) jsou teď dostupné prostřednictvím MS Graphu

**Typ:** Nová funkce  
**Kategorie služeb:** Vykazování  
**Možnost i možnosti produktu:** Monitorování & reportingu
 
Protokoly aktivit Azure AD, které zahrnují přihlášení a protokoly auditování, jsou teď dostupné prostřednictvím rozhraní Microsoft Graph API. Jsme odhalili dva koncové body prostřednictvím rozhraní API Microsoft Graph pro přístup k těmto protokolům. Podívejte se na naše [dokumenty](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) pro programový přístup k azure ad sestav api pro started. 

---
 
### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Vylepšení zkušeností s vykoupením B2B a opuštění organizace

**Typ:** Nová funkce  
**Kategorie služeb:** B2b  
**Možnost i možnosti produktu:** B2B/B2C

**Právě v čase vykoupení:** Jakmile sdílíte prostředek s uživatelem typu Host pomocí rozhraní B2B API – nemusíte odesílat speciální e-mail s pozvánkou. Ve většině případů může uživatel typu Host získat přístup k prostředku a bude proveden prostřednictvím prostředí pro využití právě včas. Žádný další dopad kvůli zmeškané e-maily. Už se neptejte uživatelů typu Host: "Klikli jste na odkaz pro uplatnění, který vám systém poslal?". To znamená, že jakmile SPO použije správce pozvánek – zakalené přílohy mohou mít stejnou kanonické URL pro všechny uživatele – interní i externí – v jakémkoli stavu uplatnění.

**Moderní zkušenost s vykoupením:** Už žádná vstupní stránka pro uplatnění na rozdělené obrazovce. Uživatelům se zobrazí moderní prostředí pro souhlas s prohlášením o zásadách ochrany osobních údajů zvoucí organizace, stejně jako u aplikací třetích stran.

**Uživatelé typu Host mohou opustit organizaci:** Jakmile je vztah uživatele s organizací u konce, může samoobslužné opuštění organizace. Žádné další volání pozvat org admin "být odstraněny", žádné další zvyšování podpory vstupenky.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD – květen 2018

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnosti produktu:** Integrace třetí strany
 
V květnu 2018 jsme do galerie aplikací přidali těchto 18 nových aplikací s podporou Federace:

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial), Infogix Data3Sixty Govern, [Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial), [Jamf Pro](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial), [KnowledgeOwl](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial), [Envi MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial), [LaunchDarkly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial), [Adobe Captivate Prime](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial), [Montage Online](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial), 中[中中中](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial)中 , OpenReel, [Arc Publishing - SSO](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial), [PlanGrid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial), [iWellnessNow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial), [Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial), [Riskware](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial), [Flock](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial), [Reviewsnap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial)

Další informace o aplikacích najdete v tématu [Integrace aplikací SaaS se službou Azure Active Directory](https://aka.ms/appstutorial).

Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete [v tématu Seznam aplikace v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Nové podrobné příručky pro nasazení služby Azure Active Directory

**Typ:** Nová funkce  
**Kategorie služeb:** Další  
**Možnost i možnosti produktu:** Adresář
 
Nové podrobné pokyny o nasazení služby Azure Active Directory (Azure AD), včetně samoobslužného resetování hesla (SSPR), jednotného přihlašování (SSO), podmíněného přístupu (CA), proxy aplikace, zřizování uživatelů, služby ADFS (ADFS) do předávacího ověřování (PTA) a synchronizace hash hash adfs do hesla (PHS).

Chcete-li zobrazit průvodce nasazením, přejděte na úložiště [průvodců nasazením identit](https://aka.ms/DeploymentPlans) na GitHubu. Chcete-li poskytnout zpětnou vazbu k průvodcům nasazení, použijte [formulář Zpětná vazba plánu nasazení](https://aka.ms/deploymentplanfeedback). Máte-li jakékoli dotazy týkající se průvodce nasazením, kontaktujte nás na [adrese IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Hledání podnikových aplikací – načtení dalších aplikací

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnost i možnosti produktu:** Sso
 
Máte potíže s hledáním aplikací / instančních objektů? Přidali jsme možnost načíst více aplikací ve vašich podnikových aplikacích seznam všech aplikací. Ve výchozím nastavení zobrazujeme 20 aplikací. Nyní můžete kliknout, **Načíst více** pro zobrazení dalších aplikací. 

---
 
### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>Květnová verze AADConnect obsahuje veřejnou náhled integrace s PingFederate, důležité aktualizace zabezpečení, mnoho oprav chyb a nové skvělé nové nástroje pro řešení problémů. 

**Typ:** Změněná funkce  
**Kategorie služeb:** Připojení služby AD  
**Možnost i možnosti produktu:** Správa životního cyklu identity
 
Květnová verze AADConnect obsahuje veřejnou náhled integrace s PingFederate, důležité aktualizace zabezpečení, mnoho oprav chyb a nové skvělé nové nástroje pro řešení problémů. Poznámky k verzi naleznete [zde](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#118190).

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Kontroly přístupu azure ad: automatické použití

**Typ:** Změněná funkce  
**Kategorie služeb:** Recenze přístupu  
**Možnost i možnosti produktu:** Správy

Recenze přístupu skupin a aplikací jsou teď obecně dostupné jako součást Azure AD Premium P2. Správce může nakonfigurovat automatické použití změn recenzenta v této skupině nebo aplikaci po dokončení kontroly přístupu. Správce může také určit, co se stane s trvalým přístupem uživatele, pokud recenzenti neodpověděli, neodebrali přístup, neudrželi přístup nebo nepřijali systémová doporučení. 

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>ID tokeny již nelze vrátit pomocí response_mode dotazu pro nové aplikace. 

**Typ:** Změněná funkce  
**Kategorie služeb:** Ověřování (přihlášení)  
**Možnost i možnosti produktu:** Ověřování uživatelů
 
Aplikace vytvořené 2 **response_mode** **id_token5.**  To přináší Azure AD inline se specifikacemi OIDC a pomáhá snížit vaše aplikace útok povrchu.  Aplikace vytvořené před 2 **id_token**response_type **response_mode5.**  Vrácená chyba při požadavku na id_token z AAD je **AADSTS70007: 'dotaz' není podporovaná hodnota 'response_mode' při požadování tokenu**.

**Fragment** a **form_post** response_modes pokračovat v práci – při vytváření nových aplikačních objektů (například pro použití proxy aplikací) zajistěte použití jednoho z těchto response_modes před vytvořením nové aplikace.  

---
 
## <a name="april-2018"></a>Duben 2018 

### <a name="azure-ad-b2c-access-token-are-ga"></a>Přístupový token Azure AD B2C jsou ga

**Typ:** Nová funkce  
**Kategorie služeb:** B2C - Správa identit y spotřebitele  
**Možnost i možnosti produktu:** B2B/B2C 

Teď můžete přistupovat k webovým apim zabezpečeným azure ad B2C pomocí přístupových tokenů. Tato funkce se přesouvá z náhledu public do ga. Prostředí uživatelského rozhraní pro konfiguraci aplikací Azure AD B2C a webových rozhraní API bylo vylepšeno a byla provedena další drobná vylepšení.
 
Další informace najdete [v tématu Azure AD B2C: Vyžádání přístupových tokenů](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-access-tokens).

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Testování konfigurace jednotného přihlášení pro aplikace založené na saml

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnost i možnosti produktu:** Sso

Při konfiguraci aplikací přihlašování pomocí služby Saml můžete otestovat integraci na konfigurační stránce. Pokud při přihlašování narazíte na chybu, můžete ji zadat v testovacím prostředí a služba Azure AD vám poskytne kroky k vyřešení konkrétního problému.

Další informace naleznete v tématu:

- [Konfigurace jednotného přihlašování k aplikacím, které nejsou v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)
- [Jak ladit jednotné přihlašování na saml k aplikacím ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

---
 
### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Podmínky použití Azure AD teď má pro jednotlivé sestavy uživatelů.

**Typ:** Nová funkce  
**Kategorie služeb:** Podmínky použití  
**Možnost i možnosti produktu:** Dodržování
 
Správci si nyní mohou vybrat daný ToU a zobrazit všechny uživatele, kteří s tímto ToU souhlasili a jaké datum a čas se uskutečnil.

Další informace naleznete v [tématu Azure AD podmínky použití funkce](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---
 
### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: Riziková IP adresa pro ochranu předblokování extranetové služby AD FS 

**Typ:** Nová funkce  
**Kategorie služeb:** Další  
**Možnost i možnosti produktu:** Monitorování & reportingu

Connect Health nyní podporuje schopnost detekovat IP adresy, které překračují prahovou hodnotu neúspěšných přihlášení U /P každou hodinu nebo denně. Funkce poskytované touto funkcí jsou:

- Komplexní zpráva zobrazující IP adresu a počet neúspěšných přihlášení generovaných každou hodinu / denně s přizpůsobitelnou prahovou hodnotou.
- E-mailová upozornění ukazující, kdy konkrétní IP adresa překročila prahovou hodnotu neúspěšných přihlášení U / P každou hodinu / denně.
- Možnost stažení pro podrobnou analýzu dat

Další informace naleznete v [tématu Risky IP Report](https://aka.ms/aadchriskyip).

---
 
### <a name="easy-app-config-with-metadata-file-or-url"></a>Snadná konfigurace aplikace se souborem metadat nebo adresou URL

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnost i možnosti produktu:** Sso

Na stránce Podnikové aplikace mohou správci nahrát soubor metadat SAML a nakonfigurovat přihlášení na základě saml pro aplikaci AAD Gallery a Non-Gallery.

Kromě toho můžete použít adresu URL metadat federace aplikace Azure AD ke konfiguraci s přihlašování s cílovou aplikací.

Další informace naleznete [v tématu Konfigurace jednotného přihlašování k aplikacím, které nejsou v galerii aplikací služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Podmínky použití Azure AD jsou teď obecně dostupné

**Typ:** Nová funkce  
**Kategorie služeb:** Podmínky použití  
**Možnost i možnosti produktu:** Dodržování
 

Podmínky použití Azure AD se přesunuly z public preview na obecně dostupné.

Další informace naleznete v [tématu Azure AD podmínky použití funkce](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Povolení nebo blokování pozvánek pro uživatele B2B z konkrétních organizací

**Typ:** Nová funkce  
**Kategorie služeb:** B2b  
**Možnost i možnosti produktu:** B2B/B2C
 

Teď můžete určit, které partnerské organizace, se kterými chcete sdílet a spolupracovat, můžete ve spolupráci Azure AD B2B. Chcete-li to provést, můžete vytvořit seznam konkrétních povolit nebo odepřít domény. Pokud je doména pomocí těchto funkcí blokována, zaměstnanci již nemohou odesílat pozvánky osobám v této doméně.

To vám pomůže řídit přístup k prostředkům a zároveň umožnit plynulé prostředí pro schválené uživatele.

Tato funkce spolupráce B2B je dostupná pro všechny zákazníky služby Azure Active Directory a dá se použít ve spojení s funkcemi Azure AD Premium, jako je podmíněný přístup a ochrana identity, pro podrobnější kontrolu nad tím, kdy a jak se externí podnikoví uživatelé přihlašují a získají přístup.

Další informace naleznete v [tématu Povolit nebo blokovat pozvánky uživatelům B2B z konkrétních organizací](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-allow-deny-list).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnosti produktu:** Integrace třetí strany

V dubnu 2018 jsme do galerie aplikací přidali těchto 13 nových aplikací s podporou Federace:

Kritérium HCM, [FiscalNote](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fiscalnote-tutorial), [Secret Server (On-Premises)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-secretserver-on-premises-tutorial), [Dynamický signál](https://docs.microsoft.com/azure/active-directory/active-directory-saas-dynamicsignal-tutorial), [mindWireless](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mindwireless-tutorial), [OrgChart Now](https://docs.microsoft.com/azure/active-directory/active-directory-saas-orgchartnow-tutorial), [Ziflow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ziflow-tutorial), [AppNeta Performance Monitor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-appneta-tutorial), [Elium](https://docs.microsoft.com/azure/active-directory/active-directory-saas-elium-tutorial) , [Fluxx Labs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fluxxlabs-tutorial), [Cisco Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ciscocloud-tutorial), Police, [SafetyNet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-safetynet-tutorial)

Další informace o aplikacích najdete v tématu [Integrace aplikací SaaS se službou Azure Active Directory](https://aka.ms/appstutorial).

Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete [v tématu Seznam aplikace v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Udělte uživatelům B2B ve službě Azure AD přístup k místním aplikacím (public preview)

**Typ:** Nová funkce  
**Kategorie služeb:** B2b  
**Možnost i možnosti produktu:** B2B/B2C

Jako organizace, která používá možnosti spolupráce B2B služby Azure Active Directory (Azure AD) k pozvání uživatelů typu Host z partnerských organizací do služby Azure AD, můžete teď těmto uživatelům B2B poskytnout přístup k místním aplikacím. Tyto místní aplikace můžou používat ověřování založené na SAML nebo integrované ověřování windows (IWA) s delegováním s omezením protokolu Kerberos (KCD).

Další informace najdete [v tématu Udělení B2B uživatelům ve službě Azure AD přístup k místním aplikacím](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-hybrid-cloud-to-on-premises).

---
 
### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Získejte kurzy integrace při přisuzovaného na webu Azure Marketplace

**Typ:** Změněná funkce  
**Kategorie služeb:** Další  
**Možnosti produktu:** Integrace třetí strany

Pokud aplikace, která je uvedená na [webu Azure Marketplace,](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) podporuje jednotné přihlašování saml, kliknutím na **získat nyní** poskytuje kurz integrace přidružený k této aplikaci. 

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Vyšší výkon automatického zřizování uživatelů Azure AD do aplikací SaaS

**Typ:** Změněná funkce  
**Kategorie služeb:** Zřizování aplikací  
**Možnosti produktu:** Integrace třetí strany
 
Dříve zákazníci, kteří používají konektory zřizování uživatelů služby Azure Active Directory pro aplikace SaaS (například Salesforce, ServiceNow a Box), mohli zaznamenat pomalý výkon, pokud jejich tenantové Azure AD obsahovali více než 100 000 kombinovaných uživatelů a skupin a používali přiřazení uživatelů a skupin k určení, kteří uživatelé by měli být zřízeni.

2. dubna 2018 byla do zřizovací služby Azure AD nasazena významná vylepšení výkonu, která výrazně zkracují dobu potřebnou k provedení počátečnísynchronizace mezi službou Azure Active Directory a cílovými aplikacemi SaaS.

V důsledku toho mnoho zákazníků, kteří měli počáteční synchronizace s aplikacemi, které trvalo mnoho dní nebo nikdy dokončeny, jsou nyní dokončení během několika minut nebo hodin.

Další informace najdete v tématu [Co se stane během zřizování?](/azure//active-directory/app-provisioning/how-provisioning-works)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Samoobslužné resetování hesla ze zamykací obrazovky Windows 10 pro hybridní počítače s Azure AD

**Typ:** Změněná funkce  
**Kategorie služeb:** Samoobslužné resetování hesla  
**Možnost i možnosti produktu:** Ověřování uživatelů
 
Aktualizovali jsme funkci Windows 10 SSPR tak, aby zahrnovala podporu pro počítače, které jsou hybridní Azure AD připojen. Tato funkce je k dispozici v systému Windows 10 RS4 umožňuje uživatelům obnovit své heslo ze zamykací obrazovky počítače se systémem Windows 10. Tuto funkci mohou využívat uživatelé, kteří jsou povoleni a registrováni pro samoobslužné resetování hesla.

Další informace najdete v [tématu Obnovení hesla Azure AD z přihlašovací obrazovky](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows).

---

## <a name="march-2018"></a>Březen 2018
 
### <a name="certificate-expire-notification"></a>Oznámení o vypršení platnosti certifikátu

**Typ:** Dlouhodobého  
**Kategorie služeb:** Podnikové aplikace  
**Možnost i možnosti produktu:** Sso
 
Azure AD odešle oznámení, když vyprší platnost certifikátu pro galerii nebo aplikaci mimo galerii. 

Někteří uživatelé neobdrželi oznámení pro podnikové aplikace nakonfigurované pro jednotné přihlašování založené na saml. Tento problém byl vyřešen. Azure AD odešle oznámení o certifikáty končí za 7, 30 a 60 dnů. Tuto událost můžete zobrazit v protokolech auditu. 

Další informace naleznete v tématu:

- [Správa certifikátů pro federované jednotné přihlašování ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Sestavy aktivit auditu na portálu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
 
---
 
### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Poskytovatelé identit Twitteru a GitHubu ve službě Azure AD B2C

**Typ:** Nová funkce  
**Kategorie služeb:** B2C - Správa identit y spotřebitele  
**Možnost i možnosti produktu:** B2B/B2C
 
Teď můžete přidat Twitter nebo GitHub jako poskytovatele identity v Azure AD B2C. Twitter se pohybuje od veřejného náhledu na GA. GitHub je vydáván ve verzi Public Preview.

Další informace najdete v tématu [Co je spolupráce Azure AD B2B?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
 
---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Omezení přístupu prohlížeče pomocí spravovaného prohlížeče Intune pomocí podmíněného přístupu založeného na aplikacích Azure AD pro iOS a Android

**Typ:** Nová funkce  
**Kategorie služeb:** Podmíněný přístup  
**Možnost i možnosti produktu:** Ochrana & zabezpečení identity
 
**Nyní ve veřejné verzi!**

**Sondované spoje prohlížeče Intune:** Vaši zaměstnanci můžou používat jednotné přihlašování napříč nativními klienty (jako je Microsoft Outlook) a spravovaný prohlížeč Intune pro všechny aplikace připojené k Azure AD.

**Podpora podmíněného přístupu spravovaného prohlížeče Intune:** Teď můžete od zaměstnanců vyžadovat, aby používali spravovaný prohlížeč Intune pomocí zásad podmíněného přístupu založených na aplikacích.

Přečtěte si více o tom v našem [blogu](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

Další informace naleznete v tématu:

- [Nastavení podmíněného přístupu založeného na aplikaci](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

- [Konfigurace zásad spravovaného prohlížeče](https://aka.ms/managedbrowser)  

---
 
### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>Rutiny proxy aplikace v modulu PowerShell GA

**Typ:** Nová funkce  
**Kategorie služeb:** Proxy aplikace  
**Možnost i možnosti produktu:** Řízení přístupu
 
Podpora rutin proxy aplikací je nyní v modulu PowerShell GA! To vyžaduje, abyste zůstali aktualizováni na modulech Prostředí PowerShell - pokud se stanete více než rok pozadu, některé rutiny mohou přestat fungovat. 

Další informace naleznete v [tématu AzureAD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0).
 
---
 
### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Nativní klienti Office 365 jsou podporovaní bezproblémovým jednotném přihlašování pomocí neinteraktivního protokolu

**Typ:** Nová funkce  
**Kategorie služeb:** Ověřování (přihlášení)  
**Možnost i možnosti produktu:** Ověřování uživatelů
 
Uživatel používající nativní klienty Office 365 (verze 16.0.8730.xxxx a vyšší) získá tiché prostředí pro přihlašování pomocí bezproblémového jednotného přihlašování. Tato podpora je poskytována přidáním neinteraktivní protokol (WS-Trust) do Azure AD.

Další informace najdete [v tématu Jak funguje přihlášení k nativnímu klientovi pomocí bezproblémového jednotného přihlašování?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work)
 
---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>Uživatelé získají tiché přihlašovací prostředí pomocí bezproblémového jednotného přihlašování, pokud aplikace odesílá požadavky na přihlášení do koncových bodů klienta Azure AD

**Typ:** Nová funkce  
**Kategorie služeb:** Ověřování (přihlášení)  
**Možnost i možnosti produktu:** Ověřování uživatelů
 
Uživatelé získat tiché přihlašovací prostředí, s bezproblémové jednotné přihlašování, pokud `https://contoso.sharepoint.com`aplikace (například) odešle požadavky na `https://login.microsoftonline.com/contoso.com/<..>` `https://login.microsoftonline.com/<tenant_ID>/<..>` přihlášení do koncových bodů klienta`https://login.microsoftonline.com/common/<...>`Azure AD – to je nebo - namísto společného koncového bodu Azure AD ( ).

Další informace naleznete [v tématu Azure Active Directory Seamless Single Sign-On](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 

---
 
### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Je potřeba přidat jenom jednu adresu URL Azure AD namísto dvou adres URL dříve do nastavení zóny Intranet uživatelů, aby bylo možné zavést bezproblémové jednotné přihlašování

**Typ:** Nová funkce  
**Kategorie služeb:** Ověřování (přihlášení)  
**Možnost i možnosti produktu:** Ověřování uživatelů
 
Chcete-li uživatelům zavést bezproblémové jednotné přihlašování, musíte do nastavení zóny intranetu uživatelů přidat `https://autologon.microsoftazuread-sso.com`pouze jednu adresu URL Azure AD pomocí zásad skupiny ve službě Active Directory: . Dříve museli zákazníci přidat dvě adresy URL.

Další informace naleznete [v tématu Azure Active Directory Seamless Single Sign-On](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 
 
---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnosti produktu:** Integrace třetí strany

V březnu 2018 jsme do galerie aplikací přidali těchto 15 nových aplikací s podporou Federace:

[Boxcryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial), [CylancePROTECT](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial), Wrike, [SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial), Asistent FirstAgenda, [YardiOne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial), Vtiger CRM, inwink, [Amplitude](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial), [Spacio](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial), [ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial), [Bersin](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial), [Mercell](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial), [Trisotech Digital Enterprise Server](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial), [Qumu Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial).
 
Další informace o aplikacích najdete v tématu [Integrace aplikací SaaS se službou Azure Active Directory](https://aka.ms/appstutorial).

Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete [v tématu Seznam aplikace v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="pim-for-azure-resources-is-generally-available"></a>PIM pro prostředky Azure je obecně k dispozici

**Typ:** Nová funkce  
**Kategorie služeb:** Správa privilegovaných identit  
**Možnost i možnosti produktu:** Správa privilegovaných identit
 
Pokud používáte Azure AD Privileged Identity Management pro role adresáře, můžete teď použít PIM časově vázaný přístup a možnosti přiřazení pro role prostředků Azure, jako jsou předplatná, skupiny prostředků, virtuální počítače a jakékoli jiné prostředky podporované Správce prostředků Azure. Vynuťte vícefaktorové ověřování při aktivaci rolí Just-In-Time a naplánujte aktivace v koordinaci se schválenými okny změn. Kromě toho tato verze přidává vylepšení, která nejsou k dispozici během náhledu Public Preview, včetně aktualizovaného hlavního rozhraní, pracovních postupů schvalování a možnosti prodloužit brzy vypršení platnosti rolí a obnovit role, jejichž platnost vypršela.

Další informace najdete v tématu [PIM pro prostředky Azure (Preview)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)
 
---
 
### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Přidání volitelných deklarací identity do tokenů aplikací (public preview)

**Typ:** Nová funkce  
**Kategorie služeb:** Ověřování (přihlášení)  
**Možnost i možnosti produktu:** Ověřování uživatelů
 
Vaše aplikace Azure AD teď může požadovat vlastní nebo volitelné deklarace identity v Tokenech JWT nebo SAML.  Jedná se o deklarace identity o uživateli nebo tenantovi, které nejsou ve výchozím nastavení zahrnuty v tokenu z důvodu omezení velikosti nebo použitelnosti.  To je aktuálně ve verzi Public Preview pro aplikace Azure AD na v1.0 a v2.0 koncové body.  Informace o tom, jaké deklarace lze přidat a jak upravit manifest aplikace a požádat o ně, naleznete v dokumentaci.  

Další informace najdete [v tématu Volitelné deklarace identity ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).
 
---
 
### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Azure AD podporuje PKCE pro bezpečnější toky OAuth

**Typ:** Nová funkce  
**Kategorie služeb:** Ověřování (přihlášení)  
**Možnost i možnosti produktu:** Ověřování uživatelů
 
Dokumenty Azure AD byly aktualizovány na vědomí podporu pro PKCE, který umožňuje bezpečnější komunikaci během Toku udělení autorizačního kódu OAuth 2.0.  S256 a code_challenges ve formátu prostého textu jsou podporovány na koncových bodech v1.0 a v2.0. 

Další informace naleznete v [tématu Žádost o autorizační kód](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code). 
 
---
 
### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>Podpora pro zřizování všech hodnot atributů uživatelů dostupných v rozhraní Workday Get_Workers API

**Typ:** Nová funkce  
**Kategorie služeb:** Zřizování aplikací  
**Možnosti produktu:** Integrace třetí strany
 
Public preview příchozí zřizování z Workday do Služby Active Directory a Azure AD nyní podporuje možnost extrahovat a zřizování všechny hodnoty atributů, které jsou k dispozici v rozhraní Workday Get_Workers API. To přidává podporuje stovky dalších standardních a vlastních atributů nad rámec ty dodávané s počáteční verzí konektoru příchozího zřizování Workday.

Další informace naleznete v [tématu: Přizpůsobení seznamu atributů uživatele Workday](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Změna členství ve skupině z dynamického na statický a naopak

**Typ:** Nová funkce  
**Kategorie služeb:** Správa skupiny  
**Možnost i možnosti produktu:** Spolupráci
 
Je možné změnit způsob, jakým je členství spravováno ve skupině. To je užitečné, pokud chcete zachovat stejný název skupiny a ID v systému, takže všechny existující odkazy na skupinu jsou stále platné; vytvoření nové skupiny by vyžadovalo aktualizaci těchto odkazů.
Aktualizovali jsme Centrum pro správu Azure AD, abychom tuto funkci podporovali. Nyní mohou zákazníci převést stávající skupiny z dynamického členství na přiřazené členství a naopak. Existující rutiny prostředí PowerShell jsou také stále k dispozici.

Další informace najdete [v tématu Dynamic kárová pravidla členství pro skupiny ve službě Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Vylepšené chování při odhlašování pomocí bezproblémového jednotného přihlašování

**Typ:** Změněná funkce  
**Kategorie služeb:** Ověřování (přihlášení)  
**Možnost i možnosti produktu:** Ověřování uživatelů
 
Dříve i v případě, že uživatelé explicitně odhlásila z aplikace zabezpečené službou Azure AD, by se automaticky přihlásili zpět pomocí bezproblémové jednotnépřihlaže, pokud se pokoušeli o přístup k aplikaci Azure AD znovu v rámci jejich corpnet z jejich zařízení spojených s doménou. S touto změnou odhlášení je podporována.  To umožňuje uživatelům zvolit stejný nebo jiný účet Azure AD pro přihlášení zpět, namísto automatického přihlášení pomocí bezproblémového jednotného přihlašování.

Další informace najdete v tématu [Azure Active Directory Seamless Single Sign-On](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)
 
---
 
### <a name="application-proxy-connector-version-154020-released"></a>Uvolněna verze konektoru proxy aplikace 1.5.402.0

**Typ:** Změněná funkce  
**Kategorie služeb:** Proxy aplikace  
**Možnost i možnosti produktu:** Ochrana & zabezpečení identity
 
Tato verze konektoru se postupně zavádí až do listopadu. Tato nová verze konektoru obsahuje následující změny:

- Konektor nyní nastavuje soubory cookie na úrovni domény místo úrovně subdomény. To zajišťuje hladší prostředí jednotného přihlašovacího zařízení a zabraňuje redundantní výzvy ověřování.
- Podpora pro požadavky na blokové kódování
- Vylepšené monitorování stavu konektorů 
- Několik oprav chyb a vylepšení stability

Další informace naleznete [v tématu Understand Azure AD Application Proxy konektory](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors).
 
---

## <a name="february-2018"></a>Únor 2018
 
### <a name="improved-navigation-for-managing-users-and-groups"></a>Vylepšená navigace pro správu uživatelů a skupin

**Typ:** Plán změn  
**Kategorie služeb:** Správa adresářů  
**Možnost i možnosti produktu:** Adresář

Prostředí navigace pro správu uživatelů a skupin bylo zjednodušeno. Nyní můžete přejít z přehledu adresářů přímo do seznamu všech uživatelů, se snadnějším přístupem k seznamu odstraněných uživatelů. Můžete také přejít z přehledu adresářů přímo do seznamu všech skupin, se snadnějším přístupem k nastavení správy skupin. A také na stránce s přehledem adresářů můžete vyhledat uživatele, skupinu, podnikovou aplikaci nebo registraci aplikace. 

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Dostupnost zpráv o přihlášení a auditu v Microsoft Azure provozovaných společností 21Vianet (Azure China 21Vianet)

**Typ:** Nová funkce  
**Kategorie služeb:** Azure Zásobník  
**Možnost i možnosti produktu:** Monitorování & reportingu

Sestavy protokolu aktivit Azure AD jsou teď dostupné v Microsoft Azure provozovaném instancemi 21Vianet (Azure China 21Vianet). Jsou zahrnuty následující protokoly:

- **Protokoly aktivit přihlášení** – zahrnuje všechny protokoly přihlášení přidružené k vašemu tenantovi.

- **Samoobslužné protokoly auditu hesla** – zahrnuje všechny protokoly auditu samoobslužného ověřování.

- **Protokoly auditu správy adresářů** – zahrnuje všechny protokoly auditu související se správou adresářů, jako je správa uživatelů, správa aplikací a další.

Pomocí těchto protokolů můžete získat přehled o tom, jak se daří vašemu prostředí. Poskytnutá data vám umožní:

- Určete, jak budou vaše aplikace a služby využívány uživateli.

- Řešení problémů, které uživatelům brání v tom, aby svou práci odváděli.

Další informace o používání těchto sestav naleznete v [tématu Vytváření sestav služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal).

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Použití role "Čtečka sestav" (role bez správce) k zobrazení sestav aktivit azure ad

**Typ:** Nová funkce  
**Kategorie služeb:** Vykazování  
**Možnost i možnosti produktu:** Monitorování & reportingu

Jako součást zpětné vazby zákazníků, aby chomáč rolí bez oprávnění správce přístup k protokolům aktivit Azure AD, jsme povolili možnost pro uživatele, kteří jsou v roli "Čtečka sestav" přístup k přihlášení a auditování činnosti v rámci portálu Azure, stejně jako pomocí rozhraní API Microsoft Graph. 

Další informace o použití těchto sestav naleznete v [tématu Vytváření sestav služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Deklarace ID zaměstnance je k dispozici jako atribut uživatele a identifikátor uživatele

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnost i možnosti produktu:** Sso
 
Id **zaměstnance** můžete nakonfigurovat jako identifikátor uživatele a atribut User pro členské uživatele a hosty B2B v přihlašujících aplikacích založených na SAML z uživatelského rozhraní podnikové aplikace.

Další informace naleznete [v tématu Přizpůsobení deklarací vydaných v tokenu SAML pro podnikové aplikace ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Zjednodušená správa aplikací pomocí zástupných znaků v proxy aplikacích Azure AD

**Typ:** Nová funkce  
**Kategorie služeb:** Proxy aplikace  
**Možnost i možnosti produktu:** Ověřování uživatelů
 
Abychom usnadnili nasazení aplikací a snížili administrativní nároky, podporujeme nyní možnost publikovat aplikace pomocí zástupných znaků. Chcete-li publikovat aplikaci se zástupnými kódy, můžete sledovat standardní tok publikování aplikací, ale použít zástupný znak v interních a externích adresách URL.

Další informace najdete v tématu [Zástupné aplikace v proxy serveru aplikace Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-wildcard)

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Nové rutiny pro podporu konfigurace proxy aplikace

**Typ:** Nová funkce  
**Kategorie služeb:** Proxy aplikace  
**Možnost i možnosti produktu:** Platforma

Nejnovější verze modulu AzureAD PowerShell Preview obsahuje nové rutiny, které zákazníkům umožňují konfigurovat aplikace proxy aplikací pomocí prostředí PowerShell.

Nové rutiny jsou: 

- Get-AzureADApplicationProxyApplication
- Get-AzureADApplicationProxyApplicationConnectorGroup
- Get-AzureADApplicationProxyConnector
- Get-AzureADApplicationProxyConnectorGroup
- Get-AzureadApplicationProxyConnectorGroupMembers
- Get-AzureadApplicationProxyConnectorMemberof
- New-AzureADApplicationProxyApplication
- New-AzureADApplicationProxyConnectorGroup
- Odebrat-AzureADApplicationProxyApplication
- Remove-AzureadApplicationProxyApplicationConnectorGroup
- Odebrat-AzureADApplicationProxyConnectorGroup
- Set-AzureaDApplicationProxyApplication
- Set-AzureadApplicationProxyApplicationConnectorGroup
- Set-AzureadApplicationProxyApplicationCustomDomainCertificate
- Set-AzureadApplicationProxyApplicationSingleSignon
- Set-AzureADApplicationProxyConnector
- Set-AzureadApplicationProxyConnectorGroup

---
 
### <a name="new-cmdlets-to-support-configuration-of-groups"></a>Nové rutiny pro podporu konfigurace skupin

**Typ:** Nová funkce  
**Kategorie služeb:** Proxy aplikace  
**Možnost i možnosti produktu:** Platforma

Nejnovější verze modulu AzureAD PowerShell obsahuje rutiny pro správu skupin ve službě Azure AD. Tyto rutiny byly dříve k dispozici v modulu AzureADPreview a jsou nyní přidány do modulu AzureAD

Rutiny skupiny, které jsou nyní vydány pro obecnou dostupnost, jsou: 

- Get-AzureadmSGroup
- Nová skupina AzureADDSGroup
- Odebrat-AzureadmSGroup
- Set-AzureadmSGroup
- Zásady životního cyklu get-AzureADMSGroup
- New-AzureADMSGroupZásady životního cyklu
- Odebrat AzureADMSGroupZásadživotního cyklu
- Add-AzureADMSSkupina zásad životního cyklu
- Odebrat AzureADMSSkupina zásad životního cyklu
- Skupina obnovení životního cyklu AzureaDMS   
- Get-AzureADMSSkupina zásad životního cyklu

---
 
### <a name="a-new-release-of-azure-ad-connect-is-available"></a>K dispozici je nová verze Služby Azure AD Connect

**Typ:** Nová funkce  
**Kategorie služeb:** Synchronizace reklam  
**Možnost i možnosti produktu:** Platforma
 
Azure AD Connect je upřednostňovaný nástroj pro synchronizaci dat mezi Azure AD a místními zdroji dat, včetně Windows Server Active Directory a LDAP.

>[!Important]
>Toto sestavení zavádí změny schématu a synchronizačních pravidel. Služba synchronizace připojení Azure AD spustí po upgradu kroky úplného importu a úplné synchronizace. Informace o tom, jak toto chování změnit, naleznete v [tématu Jak odložit úplnou synchronizaci po upgradu](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade).

Tato verze obsahuje následující aktualizace a změny:

**Oprava potíží**

- Oprava časového okna na úlohách na pozadí pro stránku Filtrování oddílů při přepnutí na další stránku

- Opravena chyba, která způsobila narušení přístupu během vlastní akce ConfigDB.

- Opravena chyba pro obnovení časového limitu připojení SQL.

- Opravena chyba, kdy certifikáty se zástupnými znaky SÍTĚ SAN selhaly před req kontrolou.

- Opravena chyba, která způsobila selhání miiserver.exe během exportu konektoru AAD.

- Opravena chyba, kdy byl při spuštění zaznamenán chybný pokus o heslo, který způsobil, že průvodce připojením AAD změnil konfiguraci

**Nové funkce a vylepšení**
 
- Telemetrie aplikací – Správci mohou tuto třídu dat zapnout/vypnout.

- Data o stavu Azure AD – správci musí navštívit portál stavu a řídit jejich nastavení stavu. Po změně zásad služby agenti přečte a vynucují ji.

- Přidány akce konfigurace zpětného zápisu zařízení a indikátor průběhu pro inicializaci stránky.

- Vylepšená obecná diagnostika se zprávou HTML a úplným sběrem dat v sestavě ZIP-Text / HTML.

- Vylepšená spolehlivost automatického upgradu a přidání další telemetrie, aby bylo zajištěno, že lze určit stav serveru.

- Omezte oprávnění dostupná privilegovaným účtům v účtu služby AD Connector. U nových instalací průvodce omezuje oprávnění, která mají privilegované účty k účtu MSOL po vytvoření účtu MSOL. Změny ovlivňují expresní instalace a vlastní instalace s účtem automatického vytváření.

- Instalační program byl změněn tak, aby při čisté instalaci služby AADConnect nevyžadoval oprávnění SA.

- Nový nástroj pro řešení problémů se synchronizací pro konkrétní objekt. V současné době nástroj kontroluje následující věci:

    - Neshoda userPrincipalName mezi synchronizovaným objektem uživatele a uživatelským účtem v tenantu Azure AD.
  
    - Pokud je objekt filtrován ze synchronizace z důvodu filtrování domény
  
    - Pokud je objekt filtrován ze synchronizace z důvodu filtrování organizační jednotky (OU)

- Nový nástroj pro synchronizaci aktuálního hodnoty hash hesla uloženého v místní službě Active Directory pro konkrétní uživatelský účet. Nástroj nevyžaduje změnu hesla. 

---
 
### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Aplikace podporující zásady ochrany aplikací Intune přidané pro použití s podmíněným přístupem založeným na aplikacích Azure AD

**Typ:** Změněná funkce  
**Kategorie služeb:** Podmíněný přístup  
**Možnost i možnosti produktu:** Ochrana & zabezpečení identity

Přidali jsme další aplikace, které podporují podmíněný přístup založený na aplikaci. Teď můžete získat přístup k Office 365 a dalším cloudovým aplikacím připojeným k Azure AD pomocí těchto schválených klientských aplikací.

Do konce února budou doplněny tyto žádosti:

- Microsoft Power BI

- Spouštěč Microsoftu

- Fakturace společnosti Microsoft

Další informace naleznete v tématu:

- [Schválený požadavek na klientskou aplikaci](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Podmíněný přístup založený na aplikacích Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Podmínky použití aktualizovat mobilní prostředí 

**Typ:** Změněná funkce  
**Kategorie služeb:** Podmínky použití  
**Možnost i možnosti produktu:** Dodržování

Když se zobrazí podmínky použití, můžete nyní kliknout na **Potíže se zobrazením? Klikněte zde**. Kliknutím na tento odkaz se nativně otevřou podmínky použití ve vašem zařízení. Bez ohledu na velikost písma v dokumentu nebo velikost obrazovky zařízení můžete dokument podle potřeby přiblížit a přečíst. 

---
 
## <a name="january-2018"></a>Leden 2018
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD 

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnosti produktu:** Integrace třetí strany

V lednu 2018 byly v galerii aplikací přidány následující nové aplikace s federační podporou:

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [OneTrust Software pro správu osobních údajů](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [IriusRisk Federated Directory a [Fidelity NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701).

Další informace o aplikacích najdete v tématu [Integrace aplikací SaaS se službou Azure Active Directory](https://aka.ms/appstutorial).

Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete [v tématu Seznam aplikace v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---
 
### <a name="sign-in-with-additional-risk-detected"></a>Přihlášení s zjištěnou další rizika

**Typ:** Nová funkce  
**Kategorie služeb:** Ochrana identity  
**Možnost i možnosti produktu:** Ochrana & zabezpečení identity

Přehled, který získáte pro detekci zjištěných rizik, je vázaný na vaše předplatné Azure AD. S edicí Azure AD Premium P2 získáte nejpodrobnější informace o všech základních detekcích.

S edicí Azure AD Premium P1 se zjišťování, na která se nevztahuje vaše licence, zobrazí jako přihlášení ke zjišťování rizika s dalším zjištěným rizikem.

Další informace naleznete v [tématu Azure Active Directory risk detections](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events).
 
---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Skrytí aplikací Office 365 na přístupových panelech koncových uživatelů

**Typ:** Nová funkce  
**Kategorie služeb:** Moje aplikace  
**Možnost i možnosti produktu:** Sso

Nyní můžete lépe spravovat, jak se aplikace Office 365 zobrazují na přístupových panelech uživatele, a to prostřednictvím nového uživatelského nastavení. Tato možnost je užitečná pro snížení počtu aplikací na přístupových panelech uživatele, pokud dáváte přednost jenom zobrazení aplikací Office na portálu Office. Toto nastavení je umístěné v **uživatelském nastavení** a je označeno, **uživatelé uvidí jenom aplikace Office 365 na portálu Office 365**.

Další informace najdete [v tématu Skrytí aplikace před uživatelským prostředím ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

---
 
### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Bezproblémové přihlašování k aplikacím povoleným pro jednotné přihlašování hesel přímo z adresy URL aplikace 

**Typ:** Nová funkce  
**Kategorie služeb:** Moje aplikace  
**Možnost i možnosti produktu:** Sso

Rozšíření prohlížeče Moje aplikace je nyní k dispozici prostřednictvím pohodlného nástroje, který vám jako zkratku v prohlížeči poskytuje funkci jednotného přihlašování my apps. Po instalaci se uživateli v prohlížeči zobrazí ikona vaflí, která jim poskytuje rychlý přístup k aplikacím. Uživatelé nyní mohou využít:

- Možnost přímého přihlášení k aplikacím založeným na zabezpečení hesla z přihlašovací stránky aplikace
- Spuštění libovolné aplikace pomocí funkce rychlého vyhledávání
- Zkratky k naposledy použitým aplikacím z rozšíření
- Rozšíření je k dispozici pro Microsoft Edge, Chrome a Firefox.
 
Další informace naleznete v tématu [Moje aplikace Zabezpečené přihlašovací rozšíření](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Prostředí správy Azure AD na portálu Azure Classic Portal bylo vyřazeno

**Typ:** Zastaralé   
**Kategorie služeb:** Azure AD  
**Možnost i možnosti produktu:** Adresář

ledna 8, 2018 prostředí správy Azure AD na klasickém portálu Azure bylo vyřazeno. To se odehrálo ve spojení s vyřazením samotného klasického portálu Azure. V budoucnu byste měli centrum [pro správu Azure AD](https://aad.portal.azure.com) používat pro veškerou správu Azure AD založenou na portálu.
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>Webový portál PhoneFactor byl vyřazen

**Typ:** Zastaralé  
**Kategorie služeb:** Azure AD  
**Možnost i možnosti produktu:** Adresář
 
ledna 2018 byl webový portál PhoneFactor vyřazen. Tento portál byl použit pro správu serveru MFA, ale tyto funkce byly přesunuty do portálu Azure na portal.azure.com. 

Konfigurace mfa je umístěna na adrese: **Server MFA \> služby Azure Active Directory**
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Zavržení sestav Azure AD

**Typ:** Zastaralé  
**Kategorie služeb:** Vykazování  
**Možnost i možnosti produktu:** Správa životního cyklu identity  


S obecnou dostupností nové konzoly Správy služby Azure Active Directory a novými rozhraními API, která jsou nyní k dispozici pro sestavy aktivit i zabezpečení, byla rozhraní API sestavy v rámci koncového bodu "/reports" vyřazena ke konci 31.

**Co je k dispozici?**

V rámci přechodu na novou konzolu pro správu jsme zpřístupnili 2 nová rozhraní API pro načítání protokolů aktivit Azure AD. Nová sada api poskytuje bohatší filtrování a řazení funkce kromě poskytování bohatší audit a přihlášení aktivity. K datům, která byla dříve k dispozici prostřednictvím sestav zabezpečení, lze nyní přistupovat prostřednictvím rozhraní API pro detekci rizik ochrany identit v aplikaci Microsoft Graph.

Další informace naleznete v tématu:

- [Začínáme s rozhraním API pro vytváření sestav služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Začínáme s Azure Active Directory Identity Protection a Microsoft Graph](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)

---

## <a name="december-2017"></a>Prosinec 2017

### <a name="terms-of-use-in-the-access-panel"></a>Podmínky použití na přístupovém panelu

**Typ:** Nová funkce  
**Kategorie služeb:** Podmínky použití  
**Možnost i možnosti produktu:** Dodržování
 
Nyní můžete přejít na přístupový panel a zobrazit podmínky použití, které jste dříve přijali.

Postupujte následovně:

1. Přejděte na [portál MyApps](https://myapps.microsoft.com)a přihlaste se.

2. V pravém horním rohu vyberte své jméno a ze seznamu vyberte **Profil.** 

3. Na svém **profilu**vyberte **Zkontrolovat podmínky použití**. 

4. Nyní si můžete přečíst podmínky použití, které jste přijali. 

Další informace naleznete v [azure ad podmínky použití funkce (náhled)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---
 
### <a name="new-azure-ad-sign-in-experience"></a>Nové prostředí pro přihlášení do Azure AD

**Typ:** Nová funkce  
**Kategorie služeb:** Azure AD  
**Možnost i možnosti produktu:** Ověření uživatele
 
Rozhraní Azure AD a Microsoft account identity system byly přepracovány tak, aby měly konzistentní vzhled a chování. Kromě toho přihlašovací stránka Azure AD shromažďuje uživatelské jméno jako první, následovaný přihlašovací údaje na druhé obrazovce.

Další informace najdete [v tématu nové prostředí přihlášení Azure AD je teď ve verzi Public Preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/).
 
---
 
### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Méně výzev k přihlášení: Nové prostředí "keep me signed in" pro přihlášení do Azure AD

**Typ:** Nová funkce  
**Kategorie služeb:** Azure AD  
**Možnost i možnosti produktu:** Ověření uživatele
 
Zaškrtávací políčko **Zachovat přihlášení** na přihlašovací stránce Azure AD bylo nahrazeno novou výzvou, která se zobrazí po úspěšném ověření. 

Pokud odpovíte **Ano** na tuto výzvu, služba vám poskytuje trvalý obnovovací token. Toto chování je stejné, jako když jste zaškrtli zaškrtávací políčko **Ponechat přihlášeni** ve starém prostředí. Pro federované klienty se tato výzva zobrazí po úspěšném ověření federovní službou.

Další informace najdete v tématu [Méně výzev k přihlášení: Nové prostředí "keep me signed in" pro Azure AD je ve verzi preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/). 

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Přidejte konfiguraci, která bude vyžadovat rozšíření podmínek použití před přijetím

**Typ:** Nová funkce  
**Kategorie služeb:** Podmínky použití  
**Možnost i možnosti produktu:** Dodržování
 
Možnost pro správce vyžaduje, aby jejich uživatelé před přijetím podmínek rozbalili podmínky použití.

Výběrem **možnosti Zapnuto** nebo **Vypnuto** budete vyžadovat, aby uživatelé rozbalili podmínky použití. Nastavení **Zapnuto** vyžaduje, aby uživatelé před jejich přijetím zobrazili podmínky použití.

Další informace naleznete v [azure ad podmínky použití funkce (náhled)](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---

### <a name="scoped-activation-for-eligible-role-assignments"></a>Aktivace s vymezenou rozsahem pro způsobilá přiřazení rolí

**Typ:** Nová funkce  
**Kategorie služeb:** Správa privilegovaných identit  
**Možnost i možnosti produktu:** Správa privilegovaných identit
 
Aktivaci s vymezeným oborem můžete použít k aktivaci způsobilých přiřazení rolí prostředků Azure s menší autonomií než původní výchozí hodnoty přiřazení. Příkladem je, pokud jste přiřazeni jako vlastník předplatného ve vašem tenantovi. S aktivací s vymezeným oborem můžete aktivovat roli vlastníka až pro pět prostředků obsažených v předplatném (například skupiny prostředků a virtuální počítače). Vymezení vaší aktivace může snížit možnost provádění nežádoucích změn kritických prostředků Azure.

Další informace naleznete v tématu [Co je správa privilegovaných identit Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure).
 
---
 
### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Nové federované aplikace v galerii aplikací Azure AD

**Typ:** Nová funkce  
**Kategorie služeb:** Podnikové aplikace  
**Možnosti produktu:** Integrace třetí strany

V prosinci 2017 jsme do galerie aplikací přidali tyto nové aplikace s podporou Federace:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager, [EFI Digital StoreFront](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [IMAGE WORKS](https://go.microsoft.com/fwlink/?linkid=863517), [MOBI](https://go.microsoft.com/fwlink/?linkid=863521), [MobileIron Azure AD integrace](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [SAML SSO pro Bamboo usnesením GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [SAML SSO pro Bitbucket usnesením GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, Zenegy Azure AD Integrace.

Další informace o aplikacích najdete v tématu [Integrace aplikací SaaS se službou Azure Active Directory](https://aka.ms/appstutorial).

Další informace o zobrazení aplikace v galerii aplikací Azure AD najdete [v tématu Seznam aplikace v galerii aplikací Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 
 
---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Pracovní postupy schvalování rolí adresářů Azure AD

**Typ:** Změněná funkce  
**Kategorie služeb:** Správa privilegovaných identit  
**Možnost i možnosti produktu:** Správa privilegovaných identit
 
Pracovní postup schválení pro role adresáře Azure AD je obecně k dispozici.

Pomocí pracovního postupu schválení mohou správci privilegovaných rolí vyžadovat, aby členové způsobilých rolí požádali o aktivaci role předtím, než budou moci používat privilegovanou roli. Více uživatelům a skupinám lze delegovat odpovědnosti za schválení. Oprávnění členové role obdrží oznámení po dokončení schválení a jejich role je aktivní.

---
 
### <a name="pass-through-authentication-skype-for-business-support"></a>Předákověřování: Podpora Skypu pro firmy

**Typ:** Změněná funkce  
**Kategorie služeb:** Ověřování (přihlášení)  
**Možnost i možnosti produktu:** Ověření uživatele

Předávací ověřování teď podporuje přihlášení uživatelů ke klientským aplikacím Skypu pro firmy, které podporují moderní ověřování, které zahrnuje online a hybridní topologie. 

Další informace najdete v [tématu Topologie Skypu pro firmy podporované moderním ověřováním](https://technet.microsoft.com/library/mt803262.aspx).
 
---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Aktualizace správy privilegovaných identit Azure AD pro Azure RBAC (preview)

**Typ:** Změněná funkce  
**Kategorie služeb:** Správa privilegovaných identit  
**Možnost i možnosti produktu:** Správa privilegovaných identit
 
S veřejnou aktualizací náhledu správy privilegovaných identit Azure AD (PIM) pro řízení přístupu na základě rolí Azure (RBAC) teď můžete:

* Používejte jen dostatek správy.
* K aktivaci rolí prostředků vyžadovat schválení.
* Naplánujte budoucí aktivaci role, která vyžaduje schválení pro role Azure AD i Azure RBAC.
 
Další informace najdete [v tématu Privilegovaná správa identit pro prostředky Azure (preview).](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)

---
 
## <a name="november-2017"></a>Listopad 2017
 
### <a name="access-control-service-retirement"></a>Vyřazení služby řízení přístupu

**Typ:** Plán změn  
**Kategorie služeb:** Služba řízení přístupu  
**Možnost i možnosti produktu:** Služba řízení přístupu 

Azure Active Directory Access Control (označované také jako služba řízení přístupu) bude vyřazena na konci roku 2018. Další informace, které obsahují podrobný harmonogram a pokyny pro migraci na vysoké úrovni, budou poskytnuty v příštích několika týdnech. Můžete zanechat komentáře na této stránce s dotazy týkající se služby řízení přístupu a člen týmu na ně odpoví.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Omezení přístupu prohlížeče ke spravovanému prohlížeči Intune 

**Typ:** Plán změn  
**Kategorie služeb:** Podmíněný přístup  
**Možnost i možnosti produktu:** Zabezpečení a ochrana identity

Přístup prohlížeče k Office 365 a dalším cloudovým aplikacím připojeným k Azure AD můžete omezit pomocí spravovaného prohlížeče Intune jako schválené aplikace. 

Nyní můžete nakonfigurovat následující podmínku pro podmíněný přístup založený na aplikaci:

**Klientské aplikace:** Prohlížeče

**Jaký je účinek změny?**

Dnes je přístup blokován při použití této podmínky. Jakmile bude náhled k dispozici, bude veškerý přístup vyžadovat použití spravované aplikace prohlížeče. 

Podívejte se na tuto schopnost a další informace v nadcházejících blogů a poznámky k verzi. 

Další informace najdete [v tématu Podmíněný přístup ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nové schválené klientské aplikace pro podmíněný přístup založený na aplikacích Azure AD

**Typ:** Plán změn  
**Kategorie služeb:** Podmíněný přístup  
**Možnost i možnosti produktu:** Zabezpečení a ochrana identity

Následující aplikace jsou na seznamu [schválených klientských aplikací](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview):

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

Další informace naleznete v tématu:

- [Schválený požadavek na klientskou aplikaci](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Podmíněný přístup založený na aplikacích Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Podpora podmínek použití pro více jazyků

**Typ:** Nová funkce    
**Kategorie služeb:** Podmínky použití  
**Možnost i možnosti produktu:** Dodržování

Správci nyní mohou vytvářet nové podmínky použití, které obsahují více dokumentů PDF. Tyto dokumenty PDF můžete označit odpovídajícím jazykem. Uživatelům se zobrazí PDF s odpovídajícím jazykem na základě jejich preferencí. Pokud neexistuje žádná shoda, zobrazí se výchozí jazyk.

---
 
### <a name="real-time-password-writeback-client-status"></a>Stav klienta zpětného zápisu hesla v reálném čase

**Typ:** Nová funkce  
**Kategorie služeb:** Samoobslužné resetování hesla  
**Možnost i možnosti produktu:** Ověření uživatele

Nyní můžete zkontrolovat stav vašeho místního klienta pro zpětný zápis hesla. Tato možnost je **dostupná** v části Místní integrace na stránce [Resetování hesla.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) 

Pokud se problémy s připojením k místnímu klientovi zpětného zápisu, zobrazí se chybová zpráva, která vám poskytuje:

- Informace o tom, proč se nemůžete připojit k místnímu klientovi zpětného zápisu.
- Odkaz na dokumentaci, která vám pomůže vyřešit problém. 

Další informace naleznete [v tématu místní integrace](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration).

---

### <a name="azure-ad-app-based-conditional-access"></a>Podmíněný přístup založený na aplikacích Azure AD 
 
**Typ:** Nová funkce  
**Kategorie služeb:** Azure AD  
**Možnost i možnosti produktu:** Zabezpečení a ochrana identity

Teď můžete omezit přístup k Office 365 a dalším cloudovým aplikacím připojeným k Azure AD na [schválené klientské aplikace,](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview) které podporují zásady ochrany aplikací Intune, pomocí [podmíněného přístupu založeného na aplikacích Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Zásady ochrany aplikací Intune se používají ke konfiguraci a ochraně firemních dat v těchto klientských aplikacích.

Kombinací [zásad podmíněného přístupu založeného na aplikacích](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) se zásadami podmíněného přístupu [založeným na zařízení](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) máte flexibilitu při ochraně dat pro osobní a firemní zařízení.

Následující podmínky a ovládací prvky jsou nyní k dispozici pro použití s podmíněným přístupem založeným na aplikacích:

**Podmínka podporované platformy**

- iOS
- Android

**Podmínka klientských aplikací**

- Mobilní aplikace a klienti pro stolní počítače

**Řízení přístupu**

- Vyžadovat schválenou klientskou aplikaci

Další informace najdete v [tématu Podmíněný přístup založený na aplikacích Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access).
 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Správa zařízení Azure AD na webu Azure Portal

**Typ:** Nová funkce  
**Kategorie služeb:** Registrace a správa zařízení  
**Možnost i možnosti produktu:** Zabezpečení a ochrana identity

Teď můžete najít všechna vaše zařízení připojená k Azure AD a aktivity související se zařízením na jednom místě. K dispozici je nové prostředí pro správu pro správu všech identit a nastavení zařízení na webu Azure Portal. V této verzi můžete:

- Zobrazte všechna zařízení, která jsou dostupná pro podmíněný přístup ve službě Azure AD.
- Zobrazení vlastností, které zahrnují hybridní zařízení připojená k Azure AD.
- Najděte klíče nástroje BitLocker pro zařízení připojená k Azure AD, spravujte zařízení pomocí Intune a další.
- Spravujte nastavení související se zařízeními Azure AD.

Další informace najdete [v tématu Správa zařízení pomocí portálu Azure](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Podpora pro macOS jako platformu zařízení pro podmíněný přístup Azure AD 

**Typ:** Nová funkce    
**Kategorie služeb:** Podmíněný přístup  
**Možnost i možnosti produktu:** Zabezpečení a ochrana identity 

Nyní můžete zahrnout (nebo vyloučit) macOS jako podmínku platformy zařízení v zásadách podmíněného přístupu Azure AD. S přidáním macOS na podporované platformy zařízení můžete:

- **Registrace a správa zařízení s macOS pomocí Intune.** Podobně jako u jiných platforem, jako je iOS a Android, je pro macOS k dispozici aplikace portálu společnosti, která provádí jednotné registrace. Pomocí nové aplikace portálu společnosti pro macOS můžete zaregistrovat zařízení pomocí Intune a zaregistrovat ho ve službě Azure AD.
- **Zajistěte, aby zařízení macOS dodržovala zásady dodržování předpisů vaší organizace definované v Intune.** V Intune na webu Azure Portal teď můžete nastavit zásady dodržování předpisů pro zařízení s macOS. 
- **Omezte přístup k aplikacím ve službě Azure AD jenom na kompatibilní zařízení s macOS.** Vytváření zásad podmíněného přístupu má macOS jako samostatnou platformu zařízení. Teď můžete vytvářet zásady podmíněného přístupu specifické pro macOS pro cílovou sadu aplikací v Azure.

Další informace naleznete v tématu:

- [Vytvoření zásady dodržování předpisů pro zařízení s macOS pomocí Intune](https://aka.ms/macoscompliancepolicy)
- [Podmíněný přístup ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)
 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Rozšíření serveru síťových zásad pro vícefaktorové ověřování Azure 

**Typ:** Nová funkce    
**Kategorie služeb:**  Vícefaktorové ověřování  
**Možnost i možnosti produktu:** Ověření uživatele

Rozšíření Network Policy Server pro azure multi-factor authentication přidává do infrastruktury ověřování cloudové vícefaktorové ověřování pomocí stávajících serverů. Pomocí rozšíření Server zásad sítě můžete do stávajícího toku ověřování přidat telefonní hovor, textovou zprávu nebo ověření telefonní aplikace. Není nutné instalovat, konfigurovat a udržovat nové servery. 

Toto rozšíření bylo vytvořeno pro organizace, které chtějí chránit připojení virtuální privátní sítě bez nasazení serveru Azure Multi-Factor Authentication Server. Rozšíření server síťových zásad funguje jako adaptér mezi RADIUS a cloudovým vícefaktorovým ověřováním Azure, které poskytuje druhý faktor ověřování pro federované nebo synchronizované uživatele.

Další informace najdete [v tématu Integrace stávající infrastruktury serveru síťových zásad s azure multi-factor authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension).
 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Obnovení nebo trvalé odebrání odstraněných uživatelů

**Typ:** Nová funkce    
**Kategorie služeb:** Správa uživatelů  
**Možnost i možnosti produktu:** Adresář 

V Centru pro správu Azure AD teď můžete:

- Obnovit odstraněného uživatele. 
- Trvale odstraňte uživatele.

**Chcete-li to vyzkoušet:**

1. V Centru pro správu Azure AD vyberte v části **Spravovat** [všichni uživatelé.](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) 

2. V seznamu **Zobrazit** vyberte **naposledy smazaní uživatelé**. 

3. Vyberte jednoho nebo více naposledy odstraněných uživatelů a pak je obnovte nebo trvale odstraňte.
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nové schválené klientské aplikace pro podmíněný přístup založený na aplikacích Azure AD
 
**Typ:** Změněná funkce  
**Kategorie služeb:** Podmíněný přístup  
**Možnost i možnosti produktu:** Zabezpečení a ochrana identity

Do seznamu [schválených klientských aplikací](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)byly přidány následující aplikace :

- Microsoft Planner
- Azure Information Protection 

Další informace naleznete v tématu:

- [Schválený požadavek na klientskou aplikaci](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Podmíněný přístup založený na aplikacích Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Použití "OR" mezi ovládacími prvky v zásadách podmíněného přístupu 

**Typ:** Změněná funkce    
**Kategorie služeb:** Podmíněný přístup  
**Možnost i možnosti produktu:** Zabezpečení a ochrana identity
 
Nyní můžete použít "OR" (vyžadují jeden z vybraných ovládacích prvků) pro řízení podmíněného přístupu. Tuto funkci můžete použít k vytvoření zásad s "OR" mezi ovládacími prvky přístupu. Pomocí této funkce můžete například vytvořit zásadu, která vyžaduje, aby se uživatel přihlásil pomocí vícefaktorového ověřování "OR", aby byl na kompatibilním zařízení.

Další informace najdete [v tématu Ovládací prvky v podmíněném přístupu Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls).
 
---

### <a name="aggregation-of-real-time-risk-detections"></a>Agregace detekcí rizik v reálném čase

**Typ:** Změněná funkce    
**Kategorie služeb:** Ochrana identity  
**Možnost i možnosti produktu:** Zabezpečení a ochrana identity

V Azure AD Identity Protection, všechny detekce rizik v reálném čase, které pocházejí ze stejné IP adresy v daný den jsou nyní agregovány pro každý typ zjišťování rizik. Tato změna omezuje objem detekce rizik zobrazených bez jakékoli změny v zabezpečení uživatele.

Základní detekce v reálném čase funguje při každém přihlášení uživatele. Pokud máte nastavenou zásadu zabezpečení rizika přihlášení na vícefaktorové ověřování nebo blokový přístup, je stále spuštěna během každého rizikového přihlášení.
 
---
 
## <a name="october-2017"></a>Říjen 2017

### <a name="deprecate-azure-ad-reports"></a>Zavržení sestav Azure AD

**Typ:** Plán změn  
**Kategorie služeb:** Vykazování  
**Možnost i možnosti produktu:** Správa životního cyklu identity  

Portál Azure vám poskytuje:

- Nová konzola pro správu Azure AD.
- Nová api pro sestavy aktivit a zabezpečení.
 
Z důvodu těchto nových funkcí byla koncová místa sestavy v rámci koncového bodu /reports vyřazena 10. 

---

### <a name="automatic-sign-in-field-detection"></a>Automatická detekce přihlašovacího pole

**Typ:** Dlouhodobého   
**Kategorie služeb:** Moje aplikace  
**Možnost i možnosti produktu:** Jednotné přihlašování  

Azure AD podporuje automatické zjišťování přihlašovacích polí pro aplikace, které vykreslují pole uživatelského jména a hesla HTML. Tyto kroky jsou popsány v [článku Automatické zachycení přihlašovacích polí pro aplikaci](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-password-single-sign-on-non-gallery-applications-problems#manually-capture-sign-in-fields-for-an-app). Tuto možnost můžete najít přidáním aplikace *mimo galerii* na stránce **Podnikové aplikace** na webu [Azure Portal](https://aad.portal.azure.com). Kromě toho můžete nakonfigurovat režim **jednotného přihlašování** v této nové aplikaci na **jednotné přihlašování založené na heslech**, zadat webovou adresu URL a stránku uložit.
 
Z důvodu problému se službou byla tato funkce dočasně zakázána. Problém byl vyřešen a automatické zjišťování přihlašovacích polí je opět k dispozici.

---

### <a name="new-multi-factor-authentication-features"></a>Nové funkce vícefaktorového ověřování

**Typ:** Nová funkce  
**Kategorie služeb:** Vícefaktorové ověřování  
**Možnost i možnosti produktu:** Zabezpečení a ochrana identity  

Vícefaktorové ověřování (MFA) je nezbytnou součástí ochrany vaší organizace. Aby byla pověření přizpůsobivější a prostředí bezproblémovější, byly přidány následující funkce: 

- Vícefaktorové výzvy výsledky jsou přímo integrované do sestavy přihlášení Azure AD, která zahrnuje programový přístup k výsledkům Vícefaktorové.
- Konfigurace mfa je hlouběji integrované do prostředí konfigurace Azure AD na webu Azure Portal.

Díky této verzi Public Preview je správa vícefaktorové analýzy a vytváření sestav integrovanou součástí základního prostředí konfigurace Azure AD. Teď můžete spravovat funkce portálu pro správu MFA v rámci prostředí Azure AD.

Další informace najdete [v tématu Reference for MFA reporting in the Azure portal](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa). 

---

### <a name="terms-of-use"></a>Podmínky použití

**Typ:** Nová funkce  
**Kategorie služeb:** Podmínky použití  
**Možnost i možnosti produktu:** Dodržování  

Podmínky použití Azure AD můžete použít k prezentaci informací, jako jsou relevantní zřeknutí se odpovědnosti za právní požadavky nebo požadavky na dodržování předpisů pro uživatele.

Podmínky použití Azure AD můžete použít v následujících scénářích:

- Všeobecné podmínky použití pro všechny uživatele ve vaší organizaci
- Specifické podmínky použití založené na atributech uživatele (například lékaři vs. zdravotní sestry nebo domácí vs. mezinárodní zaměstnanci, provádí dynamické skupiny)
- Konkrétní podmínky použití pro přístup k podnikovým aplikacím s velkým dopadem, jako je Salesforce

Další informace naleznete [v tématu podmínky použití Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="enhancements-to-privileged-identity-management"></a>Vylepšení správy privilegovaných identit

**Typ:** Nová funkce  
**Kategorie služeb:** Správa privilegovaných identit  
**Možnost i možnosti produktu:** Správa privilegovaných identit  

Díky správě privilegovaných identit Azure AD můžete spravovat, řídit a monitorovat přístup k prostředkům Azure (preview) v rámci vaší organizace tak, aby:

- Předplatná
- Skupiny prostředků
- Virtuální počítače 

Všechny prostředky v rámci portálu Azure, které používají funkci Azure RBAC, můžou využít všechny možnosti správy zabezpečení a životního cyklu, které správa privilegovaných identit Azure AD nabízí.

Další informace najdete [v tématu Správa privilegovaných identit pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

### <a name="access-reviews"></a>Kontroly přístupu

**Typ:** Nová funkce  
**Kategorie služeb:** Přístup k recenzím  
**Možnost i možnosti produktu:** Dodržování  

Organizace mohou pomocí kontrol přístupu (preview) efektivně spravovat členství ve skupinách a přístup k podnikovým aplikacím: 

- Můžete znovu certifikovat přístup uživatelů typu host pomocí kontrol jejich přístupu k aplikacím a jejich členství ve skupinách. Recenzenti se mohou efektivně rozhodnout, zda povolit hostům trvalý přístup na základě přehledů poskytnutých recenzemi přístupu.
- Pomocí kontrol přístupu je možné provést opětovnou certifikaci přístupu k aplikacím a členství ve skupinách i pro zaměstnance.

Kontroly přístupu lze shromažďovat v programech sledujících dodržování předpisů a přístup k citlivým aplikacím v rámci organizace.

Další informace najdete [v tématu kontroly přístupu k Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview).

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Skrytí aplikací třetích stran ze spouštěče aplikací Office 365

**Typ:** Nová funkce  
**Kategorie služeb:** Moje aplikace  
**Možnost i možnosti produktu:** Jednotné přihlašování  

Nyní můžete lépe spravovat aplikace, které se zobrazují na portálech uživatelů, prostřednictvím nové **vlastnosti skrýt aplikaci.** Můžete skrýt aplikace, které vám pomůžou v případech, kdy se dlaždice aplikací zobrazují pro back-endové služby nebo duplikují dlaždice a nepřehledné spouštěče aplikací uživatelů. Přepínač je v části **Vlastnosti** aplikace jiného výrobce a je označen viditelný **pro uživatele?** Aplikaci můžete také programově skrýt prostřednictvím prostředí PowerShell. 

Další informace najdete [v tématu Skrytí aplikace jiného výrobce z uživatelského prostředí ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app). 


**Co je k dispozici?**

 Jako součást přechodu na novou konzolu pro správu jsou k dispozici dvě nová rozhraní API pro načítání protokolů aktivit Azure AD. Nová sada api poskytuje bohatší filtrování a řazení funkce kromě poskytování bohatší audit a přihlášení aktivity. K datům, která byla dříve k dispozici prostřednictvím sestav zabezpečení, lze získat přístup prostřednictvím rozhraní API pro detekci rizik ochrany identity v aplikaci Microsoft Graph.


## <a name="september-2017"></a>Září 2017

### <a name="hotfix-for-identity-manager"></a>Oprava hotfix pro Správce identit

**Typ:** Změněná funkce  
**Kategorie služeb:** Správce identit  
**Možnost i možnosti produktu:** Správa životního cyklu identity  

Kumulativní balíček oprav hotfix (sestavení 4.4.1642.0) je k dispozici od září 25, 2017, pro Service Pack 1 správce identit y. Tento souhrnný balíček:

- Řeší problémy a přidává vylepšení.
- Je kumulativní aktualizace, která nahradí všechny aktualizace Identity Manager 2016 Service Pack 1 až k sestavení 4.4.1459.0 pro Identity Manager 2016. 
- Vyžaduje, abyste měli Identity Manager 2016 sestavení 4.4.1302.0. 

Další informace naleznete v tématu [Kumulativní balíček opravy hotfix (sestavení 4.4.1642.0) je k dispozici pro Identity Manager 2016 Service Pack 1](https://support.microsoft.com/help/4021562). 

---
