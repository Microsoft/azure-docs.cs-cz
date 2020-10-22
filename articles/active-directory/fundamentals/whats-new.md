---
title: Co je nového Poznámky k verzi – Azure Active Directory | Microsoft Docs
description: Zjistěte, co je nového v Azure Active Directory; například nejnovější poznámky k verzi, známé problémy, opravy chyb, zastaralé funkce a nadcházející změny.
services: active-directory
author: ajburnle
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37dc60fd14eb26ab4c8f5a867b97369a066b743b
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92362752"
---
# <a name="whats-new-in-azure-active-directory"></a>Co je nového v Azure Active Directory?

>Přečtěte si informace o tom, kdy se má tato stránka na aktualizace znovu navštívit zkopírováním a vložením této adresy URL: `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` do čtečky informačního kanálu ![ RSS ](./media/whats-new/feed-icon-16x16.png) .

Služba Azure AD průběžně přijímá vylepšení. V tomto článku najdete informace o tom, jak se můžete seznámit s nejnovějším vývojem.

- Nejnovější verze
- Známé problémy
- Opravy chyb
- Zastaralé funkce
- Plánuje změny

Tato stránka se aktualizuje měsíčně, takže ji můžete pravidelně znovu navštěvovat. Pokud hledáte položky starší než šest měsíců, najdete je v archivu, abyste mohli [novinky v Azure Active Directory](whats-new-archive.md).

---

## <a name="september-2020"></a>Září 2020

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---september-2020"></a>Nové zřizovací konektory v galerii aplikací Azure AD – září 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** integrace třetích stran
 
Nyní můžete automatizovat vytváření, aktualizaci a odstraňování uživatelských účtů pro tyto nově integrované aplikace:

- [Coda](../saas-apps/coda-provisioning-tutorial.md)
- [Cofense Recipient Sync](../saas-apps/cofense-provision-tutorial.md)
- [InVision](../saas-apps/invision-provisioning-tutorial.md)
- [myday](../saas-apps/myday-provision-tutorial.md)
- [SAP Analytics Cloud](../saas-apps/sap-analytics-cloud-provisioning-tutorial.md)
- [Povědomí o zabezpečení Webroot](../saas-apps/webroot-security-awareness-training-provisioning-tutorial.md)

Další informace o tom, jak lépe zabezpečit vaši organizaci pomocí automatizovaného zřizování uživatelských účtů, najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](../app-provisioning/user-provisioning.md).
 
---
### <a name="cloud-provisioning-public-preview-refresh"></a>Zřizování cloudu se Public Preview aktualizovat.

**Zadejte:** Nová funkce  
**Kategorie služby:** **Schopnost produktu** Azure AD zřizování cloudu: Správa životního cyklu identit
 
Azure AD Connect aktualizace cloudového zřizování ve verzi Public Preview dvě hlavní vylepšení vyvinutá na základě zpětné vazby od zákazníků: 

- Možnosti mapování atributů prostřednictvím webu Azure Portal

    Díky této funkci můžou správci IT mapovat atributy uživatelů, skupin nebo kontaktů ze služby AD na Azure AD pomocí různých typů mapování, které jsou dnes přítomné. Mapování atributů je funkce používaná ke standardizaci hodnot atributů, které se přenášejí ze služby Active Directory na Azure Active Directory. Jedna z nich může určit, jestli má být hodnota atributu přímo namapována z AD do služby Azure AD, nebo použít výrazy k transformaci hodnot atributů při zřizování uživatelů. [Další informace](../cloud-provisioning/how-to-attribute-mapping.md)

- Zřizování na vyžádání nebo testování uživatelského prostředí

    Po nastavení konfigurace můžete chtít ověřit, zda transformace uživatele pracuje podle očekávání, a teprve potom ji použít pro všechny uživatele v oboru. Díky zřizování na vyžádání můžou správci IT zadat rozlišující název (DN) uživatele AD a zjistit, jestli se synchronizují podle očekávání. Zřizování na vyžádání poskytuje skvělý způsob, jak zajistit, že mapování atributů, které jste dříve pracovali, podle očekávání. [Další informace](../cloud-provisioning/how-to-on-demand-provision.md)
 
---

### <a name="audited-bitlocker-recovery-in-azure-ad---public-preview"></a>Auditované obnovení BitLockeru v Azure AD – Public Preview

**Zadejte:** Nová funkce  
**Kategorie služby:** Správa přístupu k zařízením  
**Schopnost produktu:** Správa životního cyklu zařízení
 
Když správci IT nebo koncoví uživatelé čtou klíče pro obnovení nástroje BitLocker, ke kterým mají přístup, Azure Active Directory nyní vygeneruje protokol auditu, který zachycuje přístup k obnovovacímu klíči. Stejný audit poskytuje podrobnosti o zařízení, ke kterému byl klíč BitLocker přidružený.

Koncoví uživatelé mají [přístup ke svým klíčům pro obnovení prostřednictvím svého účtu](../user-help/my-account-portal-devices-page.md#view-a-bitlocker-key). Správci IT můžou získat přístup k klíčům pro obnovení prostřednictvím [rozhraní API pro obnovení pomocí obnovovacího klíče BitLockeru ve verzi beta](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta) nebo prostřednictvím portálu Azure AD. Další informace najdete v tématu [zobrazení nebo kopírování klíčů nástroje BitLocker na portálu Azure AD](../devices/device-management-azure-portal.md#view-or-copy-bitlocker-keys).

---

### <a name="teams-devices-administrator-built-in-role"></a>Integrovaná role Správce zařízení Teams

**Zadejte:** Nová funkce  
**Kategorie služby:** RBAC  
**Schopnost produktu:** Access Control
 
Uživatelé s rolí [Správce zařízení týmů](../roles/permissions-reference.md#teams-devices-administrator) můžou spravovat [zařízení certifikovaná](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) v centru pro správu týmů. 

Tato role umožňuje uživateli zobrazit všechna zařízení v jednoduchém přehledu s možností vyhledávání a filtrování zařízení. Uživatel může také zkontrolovat podrobnosti každého zařízení, včetně přihlášeného účtu a značka a modelu zařízení. Uživatel může změnit nastavení na zařízení a aktualizovat verze softwaru. Tato role neuděluje oprávnění kontrolovat aktivity týmů a zavolá kvalitu zařízení.
 
---

### <a name="advanced-query-capabilities-for-directory-objects"></a>Pokročilé možnosti dotazů pro objekty adresáře

**Zadejte:** Nová funkce  
**Kategorie služby:** MS Graph  
**Schopnost produktu:** Vývojářské prostředí
 
Všechny nové možnosti dotazů, které jsou představené pro objekty adresáře v rozhraních API Azure AD, jsou teď dostupné v koncovém bodu verze 1.0 a připravené k výrobě. Vývojáři mohou pomocí standardních operátorů OData spočítat, Hledat, filtrovat a řadit objekty adresáře a související odkazy.

Další informace najdete v dokumentaci [zde](https://aka.ms/BlogPostMezzoGA)a můžete také poslat názor na tento [stručný průzkum](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_yN8EPoGo5OpR1hgmCp1XxUMENJRkNQTk5RQkpWTE44NEk2U0RIV0VZRy4u).
 
---

### <a name="public-preview-continuous-access-evaluation-for-tenants-who-configured-conditional-access-policies"></a>Public Preview: vyhodnocení průběžného přístupu pro klienty, kteří konfigurují zásady podmíněného přístupu

**Zadejte:** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
Vyhodnocování průběžného přístupu (CAE) je teď k dispozici ve verzi Public Preview pro klienty Azure AD se zásadami podmíněného přístupu. V CAE se kritické události a zásady zabezpečení vyhodnocují v reálném čase. To zahrnuje zakázání účtu, resetování hesla a změnu umístění. Další informace najdete v tématu [vyhodnocení průběžného přístupu](../conditional-access/concept-continuous-access-evaluation.md).

---

### <a name="public-preview-ask-users-requesting-an-access-package-additional-questions-to-improve-approval-decisions"></a>Verze Public Preview: vyžádání dalších otázek pro přístup uživatelů k balíčku pro zlepšení rozhodnutí o schválení

**Zadejte:** Nová funkce  
**Kategorie služby:** Správa přístupu uživatelů  
**Schopnost produktu:** Správa nároků
 
Správci teď můžou vyžadovat, aby si uživatelé, kteří žádají o přístup k balíčku, odpověděli na další otázky mimo obchodní odůvodnění na portálu pro správu nároků služby Azure AD. Odpovědi uživatelů se pak zobrazí schvalovatelům, kteří jim pomohou zajistit přesnější rozhodnutí o schválení přístupu. Další informace najdete v tématu [shromáždění dalších informací o žadatelích ke schválení (Preview)](../governance/entitlement-management-access-package-approval-policy.md#collect-additional-requestor-information-for-approval-preview).
 
---

### <a name="public-preview-enhanced-user-management"></a>Verze Public Preview: Rozšířená správa uživatelů

**Zadejte:** Nová funkce  
**Kategorie služby:** Správa uživatelů  
**Schopnost produktu:** Správa uživatelů
 

Portál Azure AD je aktualizovaný, aby bylo snazší najít uživatele na stránkách všichni uživatelé a odstranění uživatelé. Změny ve verzi Preview zahrnují: 
- Více viditelných vlastností uživatele včetně ID objektu, stavu synchronizace adresáře, typu vytvoření a vystavitele identity.
- Hledání teď umožňuje kombinované hledání názvů, e-mailů a ID objektů.
- Rozšířené filtrování podle typu uživatele (Member, Guest a None), stav synchronizace adresářů, typ vytvoření, název společnosti a název domény.
- Nové možnosti řazení ve vlastnostech, jako je název, hlavní název uživatele a datum odstranění.
- Nový celkový počet uživatelů, kteří se aktualizují pomocí všech hledání nebo filtrů.

Další informace najdete [v tématu vylepšení správy uživatelů (Preview) v Azure Active Directory](../enterprise-users/users-search-enhanced.md).

---

### <a name="new-notes-field-for-enterprise-applications"></a>Nové pole poznámky pro podnikové aplikace

**Zadejte:** Nová funkce  
**Kategorie služby:** **Funkce produktu** podnikové aplikace: jednotné přihlašování

Můžete přidat volné textové poznámky k podnikovým aplikacím. Můžete přidat všechny relevantní informace, které vám pomohou aplikace manažera v rámci podnikových aplikací. Další informace najdete v tématu [rychlý Start: Konfigurace vlastností aplikace ve vašem tenantovi Azure Active Directory (Azure AD)](../manage-apps/add-application-portal-configure.md). 

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---september-2020"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – září 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran

V září 2020 jsme do Galerie aplikací přidali následující 34 nových aplikací s podporou federace:

[VMware Horizon – Unified Access Gateway](), [Pulse Secure PC](../saas-apps/vmware-horizon-unified-access-gateway-tutorial.md), [Inventory360](../saas-apps/pulse-secure-pcs-tutorial.md), [Frontitude](https://services.enteksystems.de/sso/microsoft/signup), [BookWidgets](https://www.bookwidgets.com/sso/office365), [ZVD_SERVER](https://zaas.zenmutech.com/user/signin), [HashData for Business](https://hashdata.app/login.xhtml), [SecureLogin](https://securelogin.securelogin.nu/sso/azure/login), [CyberSolutions MAILBASEΣ/CMS](../saas-apps/cybersolutions-mailbase-tutorial.md), [CyberSolutions CYBERMAILΣ](../saas-apps/cybersolutions-cybermail-tutorial.md), [LimbleCMMS](https://auth.limblecmms.com/), [Glint Inc](../saas-apps/glint-inc-tutorial.md), [zeroheight](../saas-apps/zeroheight-tutorial.md), [vhodnosti pro pohlaví](https://app.genderfitness.com/), [Coeo portál](https://my.coeo.com/), [gramaticky](../saas-apps/grammarly-tutorial.md), [Fivetran](../saas-apps/fivetran-tutorial.md), [Kumolus](../saas-apps/kumolus-tutorial.md), [RSA Archer Suite](../saas-apps/rsa-archer-suite-tutorial.md), [TeamzSkill](../saas-apps/teamzskill-tutorial.md), [raumfürraum](../saas-apps/raumfurraum-tutorial.md), [Saviynt](../saas-apps/saviynt-tutorial.md), [BizMerlinHR](https://marketplace.bizmerlin.net/bmone/signup), [Mobile Locker](../saas-apps/mobile-locker-tutorial.md), [Zengine](../saas-apps/zengine-tutorial.md), [CloudCADI](https://app.cloudcadi.com/login), [Simfoni Analytics](https://simfonianalytics.com/accounts/microsoft/login/), [Secure a](../saas-apps/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial.md) [identity & Access Management](https://my.priva.com/), [nitro pro](https://www.gonitro.com/nps/product-details/downloads), [Eventfinity](../saas-apps/eventfinity-tutorial.md), [Fexa](../saas-apps/fexa-tutorial.md) [,](https://wisteconline.com/auth/oidc) [zabezpečené podepisování Enterprise Portal](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), zabezpečené podepisování [Enterprise Portal](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal)

Dokumentaci ke všem aplikacím můžete najít také zde: https://aka.ms/AppsTutorial .

Pokud chcete zobrazit seznam aplikací v galerii aplikací Azure AD, přečtěte si podrobnosti tady: https://aka.ms/AzureADAppRequest .

---

### <a name="new-delegation-role-in-azure-ad-entitlement-management-access-package-assignment-manager"></a>Nová role delegování ve správě nároků v Azure AD: přístup k balíčku přiřazení balíčků

**Zadejte:** Nová funkce  
**Kategorie služby:** Správa přístupu uživatelů  
**Schopnost produktu:** Správa nároků
 
Do správy opravňujících k Azure AD se přidala nová role správce přiřazení balíčku přístupu, která poskytuje přesnější oprávnění ke správě přiřazení. Nyní můžete delegovat úkoly na uživatele v této roli, kteří mohou delegovat správu pro přístup balíčku k vlastníkovi firmy. Správce přiřazení balíčku pro přístup ale nemůže změnit zásady balíčků přístupu ani jiné vlastnosti, které jsou nastavené správci. 

S touto novou rolí budete mít k výhodu nejmenších oprávnění potřebných k delegování správy přiřazení a udržování administrativního řízení pro všechny ostatní konfigurace balíčků přístupu. Další informace najdete v tématu [role správy oprávnění](../governance/entitlement-management-delegate.md#entitlement-management-roles).
 
---

### <a name="changes-to-privileged-identity-managements-onboarding-flow"></a>Změny v toku připojování Privileged Identity Management

**Zadejte:** Změněná funkce  
**Kategorie služby:** Privileged Identity Management  
**Schopnost produktu:** Privileged Identity Management
 
Dříve vyžadovala připojení uživatele k Privileged Identity Management (PIM) a postup připojování v okně PIM, které zahrnovalo registraci v Azure MFA. Díky nedávné integraci PIM v okně role a správci služby Azure AD odebíráme toto prostředí. Každý tenant s platnou licencí P2 se automaticky připojí k PIM.

Připojování k PIM nemá žádný přímý negativní dopad na vašeho tenanta. Můžete očekávat následující změny:
- Další možnosti přiřazení, jako je aktivní vs. s nárokem v podobě počátečního a koncového času, když provedete přiřazení v okně role PIM nebo Azure AD a správci. 
- Další mechanismy vytváření oborů, jako jsou jednotky pro správu a vlastní role, se zavedly přímo do prostředí pro přiřazení. 
- Pokud jste správcem globálního správce nebo privilegovaného správce role, můžete začít s několika dalšími e-maily, jako je týdenní přehled o PIM. 
- V protokolu auditu souvisejícím s přiřazením role můžete také vidět instanční objekt MS-PIM. Tato očekávaná změna by neměla mít vliv na běžný pracovní postup.

 Další informace najdete v tématu [Začínáme používat Privileged Identity Management](../privileged-identity-management/pim-getting-started.md).

---

### <a name="azure-ad-entitlement-management-the-select-pane-of-access-package-resources-now-shows-by-default-the-resources-currently-in-the-selected-catalog"></a>Správa nároků na Azure AD: ve výchozím nastavení se nyní zobrazuje podokno vybrat prostředky balíčku přístupu. aktuálně se jedná o prostředky, které jsou aktuálně ve vybraném katalogu.

**Zadejte:** Změněná funkce  
**Kategorie služby:** Správa přístupu uživatelů  
**Schopnost produktu:** Správa nároků
 

V toku vytváření balíčku přístupu se změna chování podokna vybrat na kartě role prostředků. V současné době je výchozím chováním zobrazení všech prostředků vlastněných uživatelem a prostředky přidanými do vybraného katalogu. 

Toto prostředí se změní tak, aby se ve výchozím nastavení zobrazovaly jenom prostředky aktuálně přidané do katalogu, aby uživatelé mohli snadno vybírat prostředky z katalogu. Tato aktualizace vám pomůže s zjistitelnými prostředky, které se mají přidat do balíčků pro přístup, a snižuje riziko neúmyslného přidání prostředků vlastněných uživatelem, kteří nejsou součástí katalogu. Další informace najdete v tématu [Vytvoření nového balíčku pro přístup ve správě nároků ve službě Azure AD](../governance/entitlement-management-access-package-create.md#resource-roles).
 
---

## <a name="august-2020"></a>Srpen 2020 
 
### <a name="updates-to-azure-multi-factor-authentication-server-firewall-requirements"></a>Aktualizace požadavků na bránu firewall pro Azure Multi-Factor Authentication Server

**Zadejte:** Plánování změn  
**Kategorie služby:** VÍCEFAKTOROVÉHO  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
Od 1. října 2020 budou požadavky na bránu firewall pro Azure MFA Server vyžadovat další rozsahy IP adres.

Pokud máte odchozí pravidla brány firewall ve vaší organizaci, aktualizujte pravidla tak, aby servery MFA mohly komunikovat se všemi potřebnými rozsahy IP adres. Rozsahy IP adres najdete v části [požadavky na bránu firewall pro Azure Multi-Factor Authentication Server](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements).

---

### <a name="upcoming-changes-to-user-experience-in-identity-secure-score"></a>Nadcházející změny uživatelského prostředí v hodnocení identity Secure

**Zadejte:** Plánování změn  
**Kategorie služby:** **Schopnost produktu** Identity Protection: zabezpečení identity & Protection

Aktualizujeme portál pro zabezpečený skóre identit, který bude zarovnaný se změnami zavedenými v [nové verzi](/microsoft-365/security/mtp/microsoft-secure-score-whats-new?view=o365-worldwide)Microsoft Secure skore. 

Verze Preview se změnami bude k dispozici na začátku září. Změny ve verzi Preview zahrnují:
- "Bezpečné skóre identity" přejmenováno na "zabezpečené skóre pro identitu" pro přizpůsobení značky s Microsoft Secure skore
- Body jsou normalizovány na standardní měřítko a nahlášené v procentech namísto bodů.

V této verzi Preview můžou zákazníci přepínat mezi stávajícím prostředím a novým prostředím. Tato verze Preview bude poslední až do konce listopadu 2020. Po verzi Preview budou zákazníci automaticky přesměrováni na nové prostředí pro uživatelské prostředí.

---

### <a name="new-restricted-guest-access-permissions-in-azure-ad---public-preview"></a>Nová omezená oprávnění k přístupu hosta v Azure AD – Public Preview

**Zadejte:** Nová funkce  
**Kategorie služby:** Access Control   
**Schopnost produktu:** Správa uživatelů

Aktualizovali jsme oprávnění na úrovni adresáře pro uživatele typu Host. Tato oprávnění umožňují správcům vyžadovat další omezení a ovládací prvky při přístupu k externímu uživateli typu Host. Správci teď můžou přidat další omezení přístupu externích hostů k informacím o profilu a členství uživatelů a skupin. Díky této funkci veřejné verze Preview můžou zákazníci spravovat přístup externích uživatelů ve velkém měřítku tím, že zakazují členství ve skupině, včetně omezení uživatelů typu Host, aby viděli členství skupin, ve kterých jsou.

Další informace najdete v tématu [omezená oprávnění k přístupu hosta](../enterprise-users/users-restrict-guest-permissions.md) a [výchozí oprávnění uživatelů](./users-default-permissions.md).
 
---

### <a name="general-availability-of-delta-queries-for-service-principals"></a>Obecná dostupnost rozdílových dotazů u instančních objektů

**Zadejte:** Nová funkce  
**Kategorie služby:** MS Graph  
**Schopnost produktu:** Vývojářské prostředí
 
Microsoft Graph rozdílový dotaz teď podporuje typ prostředku v v 1.0:
- Instanční objekt

Klienti teď můžou sledovat změny těchto prostředků efektivně a poskytovat nejlepší řešení pro synchronizaci změn těchto prostředků s místním úložištěm dat. Informace o tom, jak nakonfigurovat tyto prostředky v dotazu, najdete v tématu [použití rozdílového dotazu ke sledování změn v Microsoft Graphch datech](/graph/delta-query-overview).
 
---

### <a name="general-availability-of-delta-queries-for-oauth2permissiongrant"></a>Obecná dostupnost rozdílových dotazů pro oAuth2PermissionGrant

**Zadejte:** Nová funkce  
**Kategorie služby:** MS Graph  
**Schopnost produktu:** Vývojářské prostředí

Microsoft Graph rozdílový dotaz teď podporuje typ prostředku v v 1.0:
- OAuth2PermissionGrant

Klienti teď můžou sledovat změny těchto prostředků efektivně a poskytovat nejlepší řešení pro synchronizaci změn těchto prostředků s místním úložištěm dat. Informace o tom, jak nakonfigurovat tyto prostředky v dotazu, najdete v tématu [použití rozdílového dotazu ke sledování změn v Microsoft Graphch datech](/graph/delta-query-overview).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---august-2020"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – srpen 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran

V srpnu 2020 jsme do Galerie aplikací přidali následující 25 nových aplikací s podporou federace:

[Backup365](https://portal.backup365.io/login), [SOAPBOX](https://app.soapboxhq.com/create?step=auth&provider=azure-ad2-oauth2), [Alma SIS](https://almau.getalma.com/), [konektor Enlyft Dynamics 365](http://enlyft.com/), [Serraview místo pro využívání softwarových řešení](../saas-apps/serraview-space-utilization-software-solutions-tutorial.md), [uniq](https://web.uniq.app/), [viditelně](../saas-apps/visibly-tutorial.md), [Zylo](../saas-apps/zylo-tutorial.md), [Edmentum – hodnocení přesná cesta](https://auth.edmentum.com/elf/login), [CyberLAB](https://cyberlab.evolvesecurity.com/#/welcome), [Altamira HRM](../saas-apps/altamira-hrm-tutorial.md), WireWheel, [ZIX dodržování předpisů a zachytávání](https://sminstall.zixcorp.com/teams/teams.php?install_request=true&tenant_id=common), [GreenLight podnikových ovládacích prvků](../saas-apps/greenlight-enterprise-business-controls-platform-tutorial.md) [,](https://goto.bpanda.com/login)Genetec, [volný prostor](https://www.clearance.network/) [,](../saas-apps/isams-tutorial.md) [VeraSMART, Amiko](../saas-apps/wirewheel-tutorial.md), [Funnel Leasing](https://nestiolistings.com/sso/oidc/azure/authorize/) [Twingate,](../saas-apps/verasmart-tutorial.md) [Scalefusion](https://amiko.web.rivero.app/) [,](../saas-apps/fortigate-ssl-vpn-tutorial.md) [Bpanda,](https://auth.twingate.com/signup) [Vivun Calendar Connect](https://app.vivun.com/dashboard/calendar/connect)pro [koncové uživatele](https://www.wandera.com/) [, Vivun](https://scalefusion.com/users/sign_in/)

Dokumentaci pro všechny aplikace můžete také najít tady. https://aka.ms/AppsTutorial

Pokud chcete zobrazit seznam aplikací v galerii aplikací Azure AD, přečtěte si podrobnosti tady. https://aka.ms/AzureADAppRequest

---

### <a name="resource-forests-now-available-for-azure-ad-ds"></a>Doménové struktury prostředků jsou teď dostupné pro Azure služba AD DS 

**Zadejte:** Nová **Kategorie služby funkcí:** Azure AD Domain Services   
**Schopnost produktu:** Azure AD Domain Services
 
Schopnost doménových struktur prostředků v Azure AD Domain Services je teď všeobecně dostupná. Nyní můžete povolit autorizaci bez synchronizace hodnot hash hesel, aby bylo možné použít Azure AD Domain Services, včetně autorizace pomocí čipové karty. Další informace najdete v tématu [Koncepty a funkce sady replik pro Azure Active Directory Domain Services (Preview)](../../active-directory-domain-services/concepts-replica-sets.md).
 
---

### <a name="regional-replica-support-for-azure-ad-ds-managed-domains-now-available"></a>Podpora místní repliky pro Azure služba AD DS spravované domény je teď k dispozici.

**Zadejte:** Nová funkce   
**Kategorie služby:** Azure AD Domain Services  
**Schopnost produktu:** Azure AD Domain Services
 
Spravovanou doménu můžete rozšířit tak, aby měla více než jednu sadu replik na jeden tenant služby Azure AD. Sady replik se dají přidat do všech partnerských virtuálních sítí v libovolné oblasti Azure, která podporuje Azure AD Domain Services. Další sady replik v různých oblastech Azure poskytují geografickou obnovu po havárii pro starší aplikace, pokud oblast Azure přejde do režimu offline. Další informace najdete v tématu [Koncepty a funkce sady replik pro Azure Active Directory Domain Services (Preview)](../../active-directory-domain-services/concepts-replica-sets.md).

---

### <a name="general-availability-of-azure-ad-my-sign-ins"></a>Obecná dostupnost služby Azure AD moje Sign-Ins

**Zadejte:** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Prostředí koncového uživatele
 
Azure AD moje Sign-Ins je nová funkce, která umožňuje podnikovým uživatelům zkontrolovat historii přihlášení, aby zkontrolovala jakoukoli neobvyklou aktivitu. Kromě toho tato funkce umožňuje koncovým uživatelům hlásit zprávu "This jsem já" nebo "to jsem já" na podezřelých aktivitách. Další informace o použití této funkce najdete v tématu [zobrazení a hledání poslední přihlašovací aktivity na stránce Sign-Ins](../user-help/my-account-portal-sign-ins-page.md#confirm-unusual-activity).
 
---

### <a name="sap-successfactors-hr-driven-user-provisioning-to-azure-ad-is-now-generally-available"></a>Zřizování uživatelů SAP SuccessFactors na základě lidských zdrojů v Azure AD je teď všeobecně dostupné.

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** Správa životního cyklu identit
 
Službu SAP SuccessFactors teď můžete integrovat jako autoritativní zdroj identity s Azure AD a automatizovat kompletní životní cyklus identity pomocí událostí souvisejících s hodinami, jako jsou noví zaměstnanci a ukončení, při zřizování a rušení zřizování účtů v Azure AD. 

Další informace o tom, jak nakonfigurovat příchozí zřizování SAP SuccessFactors do služby Azure AD, najdete v kurzu [konfigurace SAP SuccessFactors pro zřizování uživatelů služby Active Directory](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md).
 
---

### <a name="custom-open-id-connect-ms-graph-api-support-for-azure-ad-b2c"></a>Vlastní otevřené ID připojit podporu MS Graph API pro Azure AD B2C

**Zadejte:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C
 
Dřív se vlastní Open ID Connect Provider mohl přidat nebo spravovat jenom prostřednictvím Azure Portal. Nyní mohou zákazníci Azure AD B2C přidávat a spravovat prostřednictvím rozhraní API beta verze Microsoft Graph. Informace o tom, jak nakonfigurovat tento prostředek pomocí rozhraní API, najdete v tématu [identityProvider Resource Type](/graph/api/resources/identityprovider?view=graph-rest-beta).
 
---

### <a name="assign-azure-ad-built-in-roles-to-cloud-groups"></a>Přiřazení předdefinovaných rolí Azure AD ke skupinám cloudu

**Zadejte:** Nová funkce  
**Kategorie služby:** Role Azure AD  
**Schopnost produktu:** Access Control

Pomocí této nové funkce teď můžete přiřadit předdefinované role Azure AD ke skupinám cloudu. Můžete například přiřadit roli správce služby SharePoint k Contoso_SharePoint_Admins skupině. Správce osobních informací můžete použít také k seskupení oprávněných členů role místo udělení přístupu. Informace o tom, jak nakonfigurovat tuto funkci, najdete v tématu [použití skupin cloudu ke správě přiřazení rolí v Azure Active Directory (Preview)](../roles/groups-concept.md).
 
---

### <a name="insights-business-leader-built-in-role-now-available"></a>Předdefinovaná role Insights Business vedoucího společnosti je teď dostupná.

**Zadejte:** Nová funkce  
**Kategorie služby:** Role Azure AD  
**Schopnost produktu:** Access Control
 
Uživatelé v roli vedoucí firmy Insights mají přístup k sadě řídicích panelů a přehledů prostřednictvím [aplikace M365 Insights](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). To zahrnuje plný přístup ke všem řídicím panelům a prezentovaným přehledům a funkcím průzkumu dat. Uživatelé v této roli ale nemají přístup k nastavení konfigurace produktu, což je zodpovědností role správce Insights. Další informace o této roli najdete v tématu [oprávnění role správce v Azure Active Directory](../roles/permissions-reference.md#insights-business-leader)
 
---

### <a name="insights-administrator-built-in-role-now-available"></a>Integrovaná role správce Insights je teď dostupná.

**Zadejte:** Nová funkce  
**Kategorie služby:** Role Azure AD  
**Schopnost produktu:** Access Control
 
Uživatelé v roli správce Insights mají přístup k plné sadě možností správy v [aplikaci M365 Insights](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). Uživatel v této roli může číst informace o adresáři, monitorovat stav služby, lístky podpory souborů a přistupovat k aspektům nastavení pro správce Insights. Další informace o této roli najdete v tématu [oprávnění role správce v Azure Active Directory](../roles/permissions-reference.md#insights-administrator)
 
--- 

### <a name="application-admin-and-cloud-application-admin-can-manage-extension-properties-of-applications"></a>Správce aplikace a správce cloudových aplikací můžou spravovat vlastnosti rozšíření aplikací.

**Zadejte:** Změněná funkce  
**Kategorie služby:** Role Azure AD  
**Schopnost produktu:** Access Control
 
Dříve mohl [vlastnost Extension](/graph/api/application-post-extensionproperty?view=graph-rest-beta&tabs=http)spravovat jenom globální správce. Nyní tuto funkci povolujeme také pro Správce aplikací a správce cloudových aplikací.
 
---

### <a name="mim-2016-sp2-hotfix-462630-and-connectors-1113010"></a>1.1.1301.0 4.6.263.0 a konektory MIM 2016 SP2

**Zadejte:** Změněná funkce  
**Kategorie služby:** Microsoft Identity Manager  
**Schopnost produktu:** Správa životního cyklu identit

[Kumulativní balíček oprav hotfix (Build 4.6.263.0)](https://support.microsoft.com/help/4576473/hotfix-rollup-package-build-4-6-263-0-is-available-for-microsoft-ident) je k dispozici pro Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Tento balíček kumulativní aktualizace obsahuje aktualizace pro MIM CM, Správce synchronizace MIM a komponenty PAM. Kromě toho sestavování obecných konektorů MIM 1.1.1301.0 zahrnuje aktualizace konektoru grafu.

---
 
## <a name="july-2020"></a>Červenec 2020

### <a name="as-an-it-admin-i-want-to-target-client-apps-using-conditional-access"></a>Jako správce IT chci cílit na klientské aplikace pomocí podmíněného přístupu.

**Zadejte:** Plánování změn   
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
V případě, že je v podmíněném přístupu dostupná verze GA klientských aplikací, nové zásady se teď ve výchozím nastavení použijí pro všechny klientské aplikace. Patří sem starší klienti ověřování. Existující zásady zůstanou beze změny, ale přepínač *Konfigurovat ano/ne* se odebere z existujících zásad, aby bylo možné snadno zjistit, které klientské aplikace jsou zásadami aplikovány. 

Při vytváření nové zásady se ujistěte, že vyloučíte uživatele a účty služeb, které pořád používají starší verze ověřování. Pokud to neuděláte, budou zablokovány. [Další informace](../conditional-access/concept-conditional-access-conditions.md).
 
---

### <a name="upcoming-scim-compliance-fixes"></a>Nadcházející opravy dodržování předpisů SCIM

**Zadejte:** Plánování změn  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** Správa životního cyklu identit
 
Služba zřizování Azure AD využívá standard SCIM pro integraci s aplikacemi. Naše implementace standardu SCIM se vyvíjí a očekáváme, že provedeme změny v našem chování, jak provedeme operace opravy, a také nastavit vlastnost "aktivní" na prostředku. [Další informace](../app-provisioning/application-provisioning-config-problem-scim-compatibility.md).
 
---

### <a name="group-owner-setting-on-azure-admin-portal-will-be-changed"></a>Nastavení vlastník skupiny na portálu pro správu Azure se změní.

**Zadejte:** Plánování změn  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Prostřednictvím

Na portálu pro správu Azure a přístupovém panelu můžete nakonfigurovat nastavení vlastníka na stránce Obecné nastavení skupiny, aby se omezila oprávnění přiřazování vlastníků k omezené skupině uživatelů. Brzy bude možné přiřazovat oprávnění vlastníka skupiny nejen na těchto dvou portálech UX, ale taky vymáhat zásady na back-endu, aby poskytovaly konzistentní chování v rámci koncových bodů, jako je například PowerShell a Microsoft Graph. 

Začneme deaktivovat aktuální nastavení pro zákazníky, kteří ho nepoužívají, a nabídne vám možnost určit v následujících několika měsících možnost oboru uživatelů pro oprávnění vlastníka skupiny. Pokyny k aktualizaci nastavení skupiny najdete v tématu Úprava informací o skupině pomocí [Azure Active Directory](./active-directory-groups-settings-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context).

---

### <a name="azure-active-directory-registration-service-is-ending-support-for-tls-10-and-11"></a>Služba Azure Active Directory Registration Service končí na podporu TLS 1,0 a 1,1

**Zadejte:** Plánování změn  
**Kategorie služby:** Registrace a Správa zařízení  
**Schopnost produktu:** Platformy

Protokol TLS (Transport Layer Security) 1,2 a servery aktualizací a klienti budou brzy komunikovat se službou Azure Active Directory Device Registration. Podpora TLS 1,0 a 1,1 pro komunikaci se službou Device Registration Service pro Azure AD vyřadí:
- 31. srpna 2020 ve všech cloudech v svrchovaném moři (na úrovni RSZ, DoD atd.)
- 30. října 2020 ve všech komerčních cloudech

[Přečtěte si další informace](../devices/reference-device-registration-tls-1-2.md) o TLS 1,2 pro službu Azure AD Registration Service.

---

### <a name="windows-hello-for-business-sign-ins-visible-in-azure-ad-sign-in-logs"></a>Přihlašování Windows Hello pro firmy zobrazené v protokolech přihlášení Azure AD

**Zadejte:** Určí  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav
 
Windows Hello pro firmy umožňuje koncovým uživatelům přihlašovat se k počítačům s Windows pomocí gesta (například PIN nebo biometriky). Správci Azure AD můžou chtít odlišit přihlášení Windows Hello pro firmy od jiných přihlášení k Windows v rámci cesty organizace k ověřování pomocí hesla. 

Správci teď můžou zjistit, jestli ověřování Windows používalo Windows Hello pro firmy, a to tak, že na kartě Podrobnosti ověření pro přihlašovací událost Windows v okně Azure AD Sign-Ins v Azure Portal. Ověřování Windows Hello pro firmy bude v poli Metoda ověřování zahrnovat "WindowsHelloForBusiness". Další informace o interpretaci Sign-Inch protokolů najdete v [dokumentaci k přihlašovacím protokolům](../reports-monitoring/concept-sign-ins.md).
 
---

### <a name="fixes-to-group-deletion-behavior-and-performance-improvements"></a>Opravy chování při odstraňování skupin a zlepšení výkonu

**Zadejte:** Určí  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** Správa životního cyklu identit
 
Když jste dřív změnili skupinu z "in-Scope" na "out-of-Scope" a správce klikl na restart před dokončením změny, objekt skupiny se neodstraní. Objekt skupiny se teď odstraní z cílové aplikace, když se dostane mimo rozsah (zakázaný, odstraněný, nepřiřazený nebo neprojde filtr oboru). [Další informace](../app-provisioning/how-provisioning-works.md#incremental-cycles).
 
---

### <a name="public-preview-admins-can-now-add-custom-content-in-the-email-to-reviewers-when-creating-an-access-review"></a>Public Preview: Správci teď můžou do e-mailu přidat vlastní obsah při vytváření kontroly přístupu.

**Zadejte:** Nová funkce  
**Kategorie služby:** Kontroly přístupu  
**Schopnost produktu:** Zásady správného řízení identity
 
Při vytvoření nové kontroly přístupu obdrží kontrolor e-mail s žádostí o dokončení kontroly přístupu. Řada našich zákazníků požádala o možnost přidat vlastní obsah do e-mailu, jako jsou kontaktní údaje nebo jiný další podpůrný obsah, který vás provede kontrolorem. 

V rámci veřejné verze Preview můžou správci zadat vlastní obsah e-mailu odeslanému revidujícím přidáním obsahu v části Upřesnit v tématu kontroly přístupu Azure AD. Pokyny k vytváření kontrol přístupu najdete v tématu [Vytvoření kontroly přístupu skupin a aplikací v prohlídekch Azure AD Access](../governance/create-access-review.md).
 
---

### <a name="authorization-code-flow-for-single-page-apps-available"></a>Tok autorizačního kódu pro jednostránkové aplikace k dispozici

**Zadejte:** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Vývojářské prostředí
 
Vzhledem k tomu, že se v moderních prohlížečích používají omezení souborů cookie třetích stran, jako je Safari ITP, bude muset jednostránkové používat tok autorizačního kódu místo implicitního toku pro udržování jednotného přihlašování a MSAL.js v 2. x teď bude podporovat tok autorizačního kódu. 

Existují odpovídající aktualizace Azure Portal, takže můžete zabezpečené ověřování hesla aktualizovat tak, aby bylo typu "Spa", a používat tok kódu ověřování. Další pokyny najdete v tématech [přihlášení uživatelů a získání přístupového tokenu v zabezpečeném kódu v JavaScriptu pomocí toku kódu ověřování](../develop/quickstart-v2-javascript-auth-code.md) .
 
---

### <a name="azure-ad-application-proxy-now-supports-the-remote-desktop-services-web-client"></a>Služba Azure Proxy aplikací služby AD nyní podporuje webového klienta vzdálené plochy.

**Zadejte:** Nová funkce  
**Kategorie služby:** Proxy aplikace  
**Schopnost produktu:** Access Control

Služba Azure Proxy aplikací služby AD nyní podporuje webového klienta vzdálené plochy (RDS). Webový klient RDS umožňuje uživatelům přístup k infrastruktuře vzdálené plochy prostřednictvím libovolného prohlížeče podporujícího HTLM5, jako je Microsoft Edge, Internet Explorer 11, Google Chrome atd. Uživatelé můžou komunikovat se vzdálenými aplikacemi nebo počítači, jako by se k nim měli místní zařízení odkudkoli. Pomocí Azure Proxy aplikací služby AD můžete zvýšit zabezpečení nasazení RDS tím, že vynucujete zásady podmíněného ověřování a podmíněného přístupu pro všechny typy bohatých klientských aplikací. Pokyny najdete v tématu [publikování vzdálené plochy pomocí Azure proxy aplikací služby AD](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md).
 
---

### <a name="next-generation-azure-ad-b2c-user-flows-in-public-preview"></a>Nové generace Azure AD B2C toky uživatelů ve verzi Public Preview

**Zadejte:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C
 
Zjednodušené uživatelské prostředí nabízí paritu funkcí s funkcemi verze Preview a je domovkou pro všechny nové funkce. Uživatelé budou moci povolit nové funkce v rámci stejného toku uživatelů a snížit tak nutnost vytvářet více verzí s každou novou verzí funkce. Nakonec nově uživatelsky přívětivé uživatelské prostředí zjednodušuje výběr a vytváření toků uživatelů. Vyzkoušejte si to hned [vytvořením toku uživatele](../../active-directory-b2c/tutorial-create-user-flows.md). 

Další informace o tokůch uživatelů najdete [v tématu verze toku uživatele v Azure Active Directory B2C](../../active-directory-b2c/user-flow-versions.md#:~:text=    User flow  ,account. Usi ...  1 more rows ).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---july-2020"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – červenec 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
V červenci 2020 jsme do Galerie aplikací přidali následující 55 nových aplikací s podporou federace:

[CLAP své ruce](http://www.rmit.com.ar/), [Appreiz](https://microsoftteams.appreiz.com/), [Inextor trezor](https://inexto.com/inexto-suite/inextor), [Beekast](https://my.beekast.com/), [Templafy OpenID Connect](https://app.templafy.com/), [PeterConnects recepční](https://msteams.peterconnects.com/), [AlohaCloud](https://appfusions.alohacloud.com/auth), [Control Tower](https://bpm.tnxcorp.com/sso/microsoft), [Cocoom](https://start.cocoom.com/), [mince konstrukcí Cloud](https://sso.coinsconstructioncloud.com/#login/), [Medxnote MT](https://task.teamsmain.medx.im/authorization), [Reflekt](https://reflekt.konsolute.com/login), [rever](https://app.reverscore.net/access), [MyCompanyArchive](https://login.mycompanyarchive.com/), [GReminders](https://app.greminders.com/o365-oauth), [Titanfile, Wootric](../saas-apps/titanfile-tutorial.md) [, SolarWinds](../saas-apps/wootric-tutorial.md) [Orion](https://support.solarwinds.com/SuccessCenter/s/orion-platform?language=en_US),  [OpenText adresářových služeb](../saas-apps/opentext-directory-services-tutorial.md), [DataSite](../saas-apps/datasite-tutorial.md) [, BlogIn, IntSights](../saas-apps/blogin-tutorial.md) [, KPIFIRE](../saas-apps/intsights-tutorial.md) [, TextLine](../saas-apps/kpifire-tutorial.md) [,](../saas-apps/textline-tutorial.md) [Cloud Academy – SSO](../saas-apps/cloud-academy-sso-tutorial.md), [Community Spark](../saas-apps/community-spark-tutorial.md), [ChatWork](../saas-apps/chatwork-tutorial.md) [, CloudSign,](../saas-apps/cloudsign-tutorial.md) [C3M Cloud Control](../saas-apps/c3m-cloud-control-tutorial.md), [SmartHR](https://smarthr.jp/), [NumlyEngage™](../saas-apps/numlyengage-tutorial.md), [Michigan jednotné přihlašování ke službě data hub](../saas-apps/michigan-data-hub-single-sign-on-tutorial.md), [výstup](../saas-apps/egress-tutorial.md), [SendSafely](../saas-apps/sendsafely-tutorial.md), [Eletive](https://app.eletive.com/), [pravá ruka kyberbezpečnosti ADI](https://right-hand.ai/), [Fyde Enterprise Authentication](https://enterprise.fyde.com/), [verme](../saas-apps/verme-tutorial.md), [lenses.IO](../saas-apps/lensesio-tutorial.md), [chvilka](../saas-apps/momenta-tutorial.md), [zvýšení](https://app.uprise.co/sign-in), [Q](https://q.moduleq.com/login), [CloudCords](../saas-apps/cloudcords-tutorial.md), [Tellme robot](https://tellme365liteweb.azurewebsites.net/), [inspirovat](https://app.inspiresoftware.com/), [Maverics identity Orchestrator – konektor SAML](https://www.strata.io/identity-fabric/), [Smartschool (systém pro správu školy)](https://smartschoolz.com/login), [Zepto – inteligentní Timekeeping](https://user.zepto-ai.com/signin), [Studi.ly](https://studi.ly/), [Trackplan](http://www.trackplanfm.com/), [Skedda](../saas-apps/skedda-tutorial.md), [WhosOnLocation](../saas-apps/whos-on-location-tutorial.md), [Coggle](../saas-apps/coggle-tutorial.md), [kemp LoadMaster](https://kemptechnologies.com/cloud-load-balancer/), [jednotné přihlašování browserstackem](../saas-apps/browserstack-single-sign-on-tutorial.md)

Dokumentaci pro všechny aplikace můžete také najít tady. https://aka.ms/AppsTutorial

Pokud chcete zobrazit seznam aplikací v galerii aplikací Azure AD, přečtěte si prosím podrobnosti. https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---july-2020"></a>Nové zřizovací konektory v galerii aplikací Azure AD – červenec 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** integrace třetích stran

Nyní můžete automatizovat vytváření, aktualizaci a odstraňování uživatelských účtů pro nově integrovanou [výuku LinkedInu](../saas-apps/linkedin-learning-provisioning-tutorial.md)aplikace.

Další informace o tom, jak lépe zabezpečit vaši organizaci pomocí automatizovaného zřizování uživatelských účtů, najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="view-role-assignments-across-all-scopes-and-ability-to-download-them-to-a-csv-file"></a>Zobrazení přiřazení rolí napříč všemi obory a možnost je stáhnout do souboru CSV

**Zadejte:** Změněná funkce  
**Kategorie služby:** Role Azure AD  
**Schopnost produktu:** Access Control
 
Nyní můžete zobrazit přiřazení rolí napříč všemi obory role na kartě role a správci na portálu Azure AD. Tato přiřazení rolí můžete také stáhnout pro každou roli do souboru CSV. Pokyny k zobrazení a přidání přiřazení rolí najdete v tématu [zobrazení a přiřazení rolí správce v Azure Active Directory](../roles/manage-roles-portal.md).
 
---

### <a name="azure-multi-factor-authentication-software-development-azure-mfa-sdk-deprecation"></a>Azure Multi-Factor Authentication Software Development (Azure MFA SDK) – vyřazení

**Zadejte:** Zastaralé  
**Kategorie služby:** VÍCEFAKTOROVÉHO  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
Vývoj softwaru Azure Multi-Factor Authentication (Azure MFA SDK) dosáhl konce životnosti v listopadu 14 2018, jak je uvedeno poprvé v listopadu 2017. Microsoft vypíná platnost služby SDK až do 30. září 2020. Všechna volání, která sada SDK provede, selžou.

Pokud vaše organizace používá sadu Azure MFA SDK, je nutné migrovat do 30. září 2020:
- Azure MFA SDK pro MIM: Pokud používáte sadu SDK s MIM, měli byste migrovat na Azure MFA Server a aktivovat Privileged Access Management (PAM) podle těchto [pokynů](/microsoft-identity-manager/working-with-mfaserver-for-mim).   
- Azure MFA SDK pro vlastní aplikace: Zvažte integraci vaší aplikace do služby Azure AD a použití podmíněného přístupu k prosazování MFA. Pokud chcete začít, podívejte se na tuto [stránku](../manage-apps/plan-an-application-integration.md). 

---

## <a name="june-2020"></a>Červen 2020 

### <a name="user-risk-condition-in-conditional-access-policy"></a>Podmínka rizika uživatele v zásadách podmíněného přístupu

**Zadejte:** Plánování změn  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity & ochrana
 

Podpora rizik uživatelů v zásadách podmíněného přístupu Azure AD umožňuje vytvořit několik uživatelských zásad založených na rizicích. Pro různé uživatele a aplikace se můžou vyžadovat jiné minimální úrovně rizika uživatele. Na základě rizika uživatele můžete vytvořit zásady pro blokování přístupu, vyžadovat vícefaktorové ověřování, zabezpečenou změnu hesla nebo přesměrovat na Microsoft Cloud App Security k vynucení zásad relace, jako je například další auditování.

Podmínka rizika uživatele vyžaduje Azure AD Premium P2, protože používá Azure Identity Protection, což je nabídka P2. Další informace o podmíněném přístupu najdete v [dokumentaci k podmíněnému přístupu Azure AD](../conditional-access/index.yml).

---

### <a name="saml-sso-now-supports-apps-that-require-spnamequalifier-to-be-set-when-requested"></a>Jednotné přihlašování SAML teď podporuje aplikace, které vyžadují, aby se v případě vyžádání nastavil SPNameQualifier

**Zadejte:** Určí  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ
 
Některé aplikace SAML vyžadují, aby se v předmětu kontrolního výrazu vrátilo SPNameQualifier, pokud je to požadováno. Služba Azure AD teď odpoví správně, když se v zásadách NameID žádosti vyžádá SPNameQualifier. To funguje taky pro přihlášení iniciované pomocí SP a zahájí se přihlášení IdP.  Další informace o protokolu SAML v Azure Active Directory najdete v tématu [Single Sign-On SAML Protocol](../develop/single-sign-on-saml-protocol.md).

---

### <a name="azure-ad-b2b-collaboration-supports-inviting-msa-and-google-users-in-azure-government-tenants"></a>Spolupráce B2B Azure AD podporuje pozvání uživatelů MSA a Google v Azure Government tenantů.

**Zadejte:** Nová funkce  
**Kategorie služby:** B2B  
**Schopnost produktu:** B2B/B2C
 

Klienti Azure Government pomocí funkcí spolupráce B2B teď můžou pozvat uživatele, kteří mají účet Microsoft nebo Google. Pokud chcete zjistit, jestli váš tenant může tyto možnosti využít, postupujte podle pokynů v tématu [Jak poznám, jestli je spolupráce B2B dostupná ve svém Tenantovi Azure USA?](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)

 
---
 
### <a name="user-object-in-ms-graph-v1-now-includes-externaluserstate-and-externaluserstatechangeddatetime-properties"></a>Objekt uživatele v MS Graph v1 nyní zahrnuje vlastnosti externalUserState a externalUserStateChangedDateTime.

**Zadejte:** Nová funkce  
**Kategorie služby:** B2B  
**Schopnost produktu:** B2B/B2C
 

Vlastnosti externalUserState a externalUserStateChangedDateTime lze použít k vyhledání pozvaných hostů B2B, kteří ještě nepřijali své pozvánky, a také automatizaci sestavení, jako je odstranění uživatelů, kteří nepřijali své pozvánky po určitém počtu dnů. Tyto vlastnosti jsou teď dostupné v MS Graph v1. Pokyny k používání těchto vlastností najdete v tématu [typ prostředku uživatele](/graph/api/resources/user?view=graph-rest-1.0).
 
---

### <a name="manage-authentication-sessions-in-azure-ad-conditional-access-is-now-generally-available"></a>Správa relací ověřování ve službě Azure AD podmíněný přístup je teď všeobecně dostupná.

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
Možnosti správy relace ověřování vám umožňují nakonfigurovat, jak často uživatelé potřebují zadat přihlašovací údaje a jestli potřebují zadat přihlašovací údaje po zavření a opětovném otevření prohlížečů, aby ve vašem prostředí nabízely větší zabezpečení a flexibilitu.
 
Kromě toho se Správa relace ověřování používá jenom pro první ověření v připojené službě Azure AD, připojené k hybridní službě Azure AD a k zařízením registrovaným v Azure AD. Nyní se Správa relace ověřování uplatní i na MFA. Další informace najdete v tématu [Konfigurace správy relace ověřování pomocí podmíněného přístupu](../conditional-access/howto-conditional-access-session-lifetime.md).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2020"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – červen 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
V červnu 2020 jsme do Galerie aplikací přidali následující 29 nových aplikací s podporou federace:

[Shopify plus](../saas-apps/shopify-plus-tutorial.md), [Ekarda](../saas-apps/ekarda-tutorial.md), [MailGates](../saas-apps/mailgates-tutorial.md), [BullseyeTDP](../saas-apps/bullseyetdp-tutorial.md), [raketa](../saas-apps/raketa-tutorial.md), [segment](../saas-apps/segment-tutorial.md), [AI auditor](https://www.mindbridge.ai/products/ai-auditor/), [Pobuca Connect](https://app.pobu.ca/), [proto.IO](../saas-apps/proto.io-tutorial.md), [Gatekeeper](https://www.gatekeeperhq.com/)Webmarketo, [Plánovač centra](../saas-apps/hub-planner-tutorial.md), Ansira, možnosti [pro uvedení na trh](https://ansira.com/technology/channel-engagement), [IBM Digital Business Automation v cloudu](../saas-apps/ibm-digital-business-automation-on-cloud-tutorial.md), [Kisi fyzické zabezpečení](../saas-apps/kisi-physical-security-tutorial.md), [ViewpointOne](https://team.viewpoint.com/), [IntelligenceBank](../saas-apps/intelligencebank-tutorial.md), [pymetrics](../saas-apps/pymetrics-tutorial.md), [nula](https://www.teamzero.com/) [Curricula SAML](../saas-apps/curricula-saml-tutorial.md), [instanice](https://instation.invillia.com/), [EDX for Business SAML 2,0 Integration](../saas-apps/edx-for-business-saml-integration-tutorial.md), mooc SmartKargo, PKIsigning [Perforce Helix Core - Helix Authentication Service](../saas-apps/perforce-helix-core-tutorial.md) [, SiteIntel](../saas-apps/smartkargo-tutorial.md), [Perforce, Helix](../saas-apps/siteintel-tutorial.md) [, Cloud](../saas-apps/field-id-tutorial.md) [,](https://cloud.metacompliance.com/) [MOOC Office 365](https://mooc.office365-training.com/en/) [PKIsigning platform](https://platform.pkisigning.nl/) [Helix 365](https://smallstep.com/sso-ssh/)  

Dokumentaci ke všem aplikacím si můžete také najít tady https://aka.ms/AppsTutorial . Pokud chcete zobrazit seznam aplikací v galerii aplikací Azure AD, přečtěte si prosím podrobnosti tady: https://aka.ms/AzureADAppRequest .

---

### <a name="api-connectors-for-external-identities-self-service-sign-up-are-now-in-public-preview"></a>Konektory rozhraní API pro samoobslužné registrace externích identit jsou teď ve verzi Public Preview.

**Zadejte:** Nová funkce  
**Kategorie služby:** B2B  
**Schopnost produktu:** B2B/B2C
 
Externí identity API konektory umožňují využívat webová rozhraní API pro integraci samoobslužné registrace do externích cloudových systémů. To znamená, že teď můžete vyvolat webová rozhraní API jako konkrétní kroky v toku registrace a aktivovat tak cloudové vlastní pracovní postupy. Konektory rozhraní API můžete například použít k těmto akcím:

- Proveďte integraci s vlastními pracovními postupy schvalování.
- Provést kontrolu identity
- Ověřit data vstupu uživatele
- Přepsat atributy uživatele
- Spustit vlastní obchodní logiku

Další informace o všech dostupných prostředích pomocí konektorů rozhraní API najdete v tématu [Použití konektorů rozhraní API k přizpůsobení a rozšiřování samoobslužné registrace](../external-identities/api-connectors-overview.md)nebo [přizpůsobení samoobslužné registrace externích identit pomocí integrace webového rozhraní API](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-external-identities-self-service-sign-up-with-web-api/ba-p/1257364#.XvNz2fImuQg.linkedin).
 
---

### <a name="provision-on-demand-and-get-users-into-your-apps-in-seconds"></a>Zřizování na vyžádání a získání uživatelů do vašich aplikací během několika sekund

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** Správa životního cyklu identit
 
Služba zřizování Azure AD v současné době pracuje cyklicky. Služba se spouští každých 40 minut. [Funkce zřizování na vyžádání](https://aka.ms/provisionondemand) umožňuje vybrat uživatele a zřídit je během několika sekund. Díky této možnosti můžete rychle řešit problémy s zřizováním, aniž byste museli provést restart, aby se cyklus zřizování znovu spustil. 
 
---

### <a name="new-permission-for-using-azure-ad-entitlement-management-in-graph"></a>Nové oprávnění pro používání správy nároků Azure AD v grafu

**Zadejte:** Nová funkce  
**Kategorie služby:** Jiná  
**Schopnost produktu:** Správa nároků
 
Nové delegované oprávnění EntitlementManagement. Read. All je teď k dispozici pro použití s rozhraním API pro správu oprávnění ve službě Microsoft Graph beta. Další informace o dostupných rozhraních API najdete v tématu [práce s rozhraním API pro správu opravňujících k Azure AD](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta).

---

### <a name="identity-protection-apis-available-in-v10"></a>Rozhraní API pro ochranu identit dostupné v 1.0

**Zadejte:** Nová funkce  
**Kategorie služby:** Ochrana identity  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
Rozhraní API pro Microsoft Graph riskyUsers a riskDetections jsou teď všeobecně dostupná. Teď, když jsou k dispozici na koncovém bodu v 1.0, vás pozvaní, abyste je mohli používat v produkčním prostředí. Další informace najdete v [dokumentaci Microsoft Graph](/graph/api/resources/identityprotectionroot?view=graph-rest-1.0).
 
---

### <a name="sensitivity-labels-to-apply-policies-to-microsoft-365-groups-is-now-generally-available"></a>Popisky citlivosti na uplatnění zásad na Microsoft 365 skupiny jsou teď všeobecně dostupné.

**Zadejte:** Nová funkce  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Prostřednictvím
 

Nyní můžete vytvořit popisky citlivosti a použít nastavení popisku k uplatnění zásad pro Microsoft 365 skupiny, včetně ochrany osobních údajů (veřejných nebo privátních) a zásad přístupu k externím uživatelům. Můžete vytvořit popisek se zásadami ochrany osobních údajů jako privátní a zásady přístupu externích uživatelů, aby nepovolovaly přidávání uživatelů typu Host. Když uživatel použije tento popisek na skupinu, skupina bude soukromá a do skupiny nebude povoleno přidávat žádné uživatele typu Host. 

Popisky citlivosti jsou důležité pro ochranu důležitých podnikových dat a umožňují vám spravovat skupiny ve velkém měřítku, a to v souladu a zabezpečeném způsobem. Pokyny k používání popisků citlivosti najdete [v tématu Přiřazení popisků citlivosti Microsoft 365 skupinám v Azure Active Directory (Preview)](../enterprise-users/groups-assign-sensitivity-labels.md).
 
---

### <a name="updates-to-support-for-microsoft-identity-manager-for-azure-ad-premium-customers"></a>Aktualizace pro podporu Microsoft Identity Manager pro zákazníky Azure AD Premium

**Zadejte:** Změněná funkce  
**Kategorie služby:** Microsoft Identity Manager  
**Schopnost produktu:** Správa životního cyklu identit
 
Podpora Azure je teď k dispozici pro komponenty integrace služby Azure AD Microsoft Identity Manager 2016 na konci rozšířené podpory pro Microsoft Identity Manager 2016. Další informace najdete v článku [o podpoře aktualizace pro Azure AD Premium zákazníky pomocí Microsoft Identity Manager](/microsoft-identity-manager/support-update-for-azure-active-directory-premium-customers).

---

### <a name="the-use-of-group-membership-conditions-in-sso-claims-configuration-is-increased"></a>Bylo zvýšeno použití podmínek členství ve skupině v konfiguraci deklarací SSO.

**Zadejte:** Změněná funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ
 
Dříve počet skupin, které můžete použít při podmíněné změně deklarací identity na základě členství ve skupině v rámci každé konfigurace jedné aplikace, byl omezený na 10. Použití podmínek členství ve skupině v konfiguraci deklarací SSO se teď zvýšilo na maximálně 50 skupin. Další informace o tom, jak nakonfigurovat deklarace identity, najdete v tématu [Konfigurace deklarací pro jednotné přihlašování k podnikovým aplikacím](../develop/active-directory-saml-claims-customization.md#emitting-claims-based-on-conditions). 

---

### <a name="enabling-basic-formatting-on-the-sign-in-page-text-component-in-company-branding"></a>Povolení základního formátování na součásti textu přihlašovací stránky v brandingu společnosti.

**Zadejte:** Změněná funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů
 
Funkce brandingu společnosti na přihlašovacím jméně Azure AD/Microsoft 365 se aktualizovala tak, aby zákazník mohl přidávat hypertextové odkazy a jednoduché formátování, včetně tučného písma, podtržení a kurzívy. Pokyny k použití této funkce najdete v tématu [Přidání brandingu na přihlašovací stránku Azure Active Directory vaší organizace](./customize-branding.md).

---

### <a name="provisioning-performance-improvements"></a>Vylepšení výkonu zřizování

**Zadejte:** Změněná funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** Správa životního cyklu identit
 
Služba zřizování se aktualizovala tak, aby snižovala čas dokončení [přírůstkového cyklu](../app-provisioning/how-provisioning-works.md#incremental-cycles) . To znamená, že uživatelé a skupiny budou k aplikacím zřízeni rychleji než dříve. Všem novým zřizovacím úlohám vytvořeným po 6/10/2020 budou automaticky těžit z vylepšení výkonu. Všechny aplikace, které jsou nakonfigurované pro zřizování před 6/10/2020, se budou muset restartovat jednou po 6/10/2020, aby se využilo zvýšení výkonu. 

---

### <a name="announcing-the-deprecation-of-adal-and-ms-graph-parity"></a>Oznamujeme vyřazení ADAL a parity MS graphu.

**Zadejte:** Zastaralé  
**Kategorie služby:** NENÍ K DISPOZICI  
**Schopnost produktu:** Správa životního cyklu zařízení

Teď, když jsou k dispozici knihovny Microsoft Authentication Library (MSAL), už nepřidáme Azure Active Directory do Authentication Library (ADAL) nové funkce a budou ukončeny opravy zabezpečení na 30. června 2022. Další informace o tom, jak migrovat na MSAL, najdete v článku [migrace aplikací do knihovny Microsoft Authentication Library (MSAL)](../develop/msal-migration.md).

Kromě toho jsme dokončili práci, aby všechny funkce grafu služby Azure AD byly dostupné prostřednictvím MS graphu. Rozhraní Azure AD Graph API tak budou přijímat jenom opravu chyb a opravy zabezpečení až do 30. června 2022. Další informace najdete v tématu [aktualizace aplikací pro použití knihovny Microsoft Authentication Library a rozhraní Microsoft Graph API](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363) .
 
---
 
## <a name="may-2020"></a>Květen 2020

### <a name="retirement-of-properties-in-signins-riskyusers-and-riskdetections-apis"></a>Vyřazení vlastností v rozhraních API nenašla, riskyUsers a riskDetections

**Zadejte:** Plánování změn  
**Kategorie služby:** Ochrana identity  
**Schopnost produktu:** Zabezpečení identity & ochrana

V současné době výčtové typy slouží k reprezentaci vlastnosti riskType v rozhraní riskDetections API i v riskyUserHistoryItem (ve verzi Preview). Výčtové typy se používají také pro vlastnost riskEventTypes v rozhraní nenašla API. Po přeposlání budeme tyto vlastnosti zastupovat jako řetězce. 

Zákazníci by měli přejít na vlastnost riskEventType v rozhraní beta riskDetections a riskyUserHistoryItem API a na vlastnost riskEventTypes_v2 v rozhraní beta nenašla API do 9. září 2020. V tomto datu bude vycházet z aktuálních vlastností riskType a riskEventTypes. Další informace najdete [v tématu změny vlastností rizikových událostí a rozhraní API ochrany identity na Microsoft Graph](https://developer.microsoft.com/graph/blogs/changes-to-risk-event-properties-and-identity-protection-apis-on-microsoft-graph/).

--- 

### <a name="deprecation-of-riskeventtypes-property-in-signins-v10-api-on-microsoft-graph"></a>Vyřazení vlastnosti riskEventTypes v rozhraní API nenašla v 1.0 v Microsoft Graph

**Zadejte:** Plánování změn  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Zabezpečení identity & ochrana

Výčtové typy budou přepnuty na typy řetězců při reprezentaci vlastností rizikové události Microsoft Graph září 2020. Kromě ovlivnění rozhraní API verze Preview Tato změna ovlivní také nenašla rozhraní API v produkčním prostředí.

Zavedli jsme novou vlastnost riskEventsTypes_v2 (String) na rozhraní API pro nenašla v 1.0. Aktuální vlastnost riskEventTypes (Enum) vyřadíme 11. června 2022 v souladu s našimi zásadami pro vyřazení z provozu Microsoft Graph. Zákazníci by měli přejít na vlastnost riskEventTypes_v2 v rozhraní nenašla API v 1.0 od 11. června 2022. Další informace najdete v tématu vyřazení [vlastnosti riskEventTypes v rozhraní API nenašla v 1.0 na Microsoft Graph](https://developer.microsoft.com/graph/blogs/deprecation-of-riskeventtypes-property-in-signins-v1-0-api-on-microsoft-graph//).

--- 

### <a name="upcoming-changes-to-mfa-email-notifications"></a>Nadcházející změny e-mailových oznámení MFA

**Zadejte:** Plánování změn  
**Kategorie služby:** VÍCEFAKTOROVÉHO  
**Schopnost produktu:** Zabezpečení identity & ochrana
 

U e-mailových oznámení pro Cloud MFA provádíme tyto změny:

E-mailová oznámení budou odesílána z následující adresy: azure-noreply@microsoft.com a msonlineservicesteam@microsoftonline.com . Aktualizujeme obsah e-mailů s výstrahou týkající se podvodů, aby lépe označovaly požadované kroky odblokování použití.

---

### <a name="new-self-service-sign-up-for-users-in-federated-domains-who-cant-access-microsoft-teams-because-they-arent-synced-to-azure-active-directory"></a>Nové samoobslužné registrace pro uživatele ve federovaných doménách, kteří nemají přístup k Microsoft teams, protože nejsou synchronizované s Azure Active Directory.

**Zadejte:** Plánování změn  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů
 

V současné době jsou uživatelé, kteří jsou v doméně federované v Azure AD, ale nesynchronizované do tenanta, nemůžou získat přístup k týmům. Od konce června Tato nová funkce jim umožní učinit rozšířením stávající funkce pro registraci e-mailu. To umožní uživatelům, kteří se budou přihlašovat k federovaným IdP, ale kteří ještě nemají v Azure ID objekt uživatele, aby se objekt uživatele automaticky vytvořil a ověřil pro týmy. Objekt uživatele bude označen jako "samoobslužná registrace". Jedná se o rozšíření stávající možnosti ověřování pomocí e-mailu, které můžou uživatelé ve spravovaných doménách dělat a můžou se řídit pomocí stejného příznaku. Tato změna se dokončí během následujících dvou měsíců. Podívejte se na aktualizace dokumentace [zde](../enterprise-users/directory-self-service-signup.md).
 
---

### <a name="upcoming-fix-the-oidc-discovery-document-for-the-azure-government-cloud-is-being-updated-to-reference-the-correct-graph-endpoints"></a>Nadcházející Oprava: dokument zjišťování OIDC pro cloud Azure Government se aktualizuje tak, aby odkazoval na správné koncové body grafu.

**Zadejte:** Plánování změn  
**Kategorie služby:** Cloudy svrchovan  
**Schopnost produktu:** Ověřování uživatelů
 
Od června začne dokument zjišťování OIDC [Microsoft Identity Platform a OpenID Connect](../develop/v2-protocols-oidc.md) na koncovém bodu [cloudu Azure Government](../develop/authentication-national-cloud.md) (login.microsoftonline.us) vracet správný národní koncový bod [cloudového grafu](/graph/deployments) ( https://graph.microsoft.us nebo na https://dod-graph.microsoft.us) základě poskytnutého tenanta).  V současné době poskytuje nesprávný msgraph_host pole koncového bodu grafu (graph.microsoft.com).  

Oprava této chyby se postupně provede přibližně 2 měsíce.  

---

### <a name="azure-government-users-will-no-longer-be-able-to-sign-in-on-loginmicrosoftonlinecom"></a>Azure Government se uživatelé už nebudou moct přihlašovat v login.microsoftonline.com.

**Zadejte:** Plánování změn  
**Kategorie služby:** Cloudy svrchovan  
**Schopnost produktu:** Ověřování uživatelů
 
Od 1. června 2018 se oficiální autorita Azure Active Directory (Azure AD) pro Azure Government změnila z https://login-us.microsoftonline.com na https://login.microsoftonline.us . Pokud vlastníte aplikaci v rámci klienta Azure Government, musíte aplikaci aktualizovat, aby se uživatelé mohli podepisovat na koncovém bodu. us.

Od 5. května Azure AD zahájí vynucování změny koncového bodu, blokuje Azure Government uživatelům přihlášení k aplikacím hostovaným v Azure Government klienty pomocí veřejného koncového bodu (microsoftonline.com). Ovlivněné aplikace začnou zobrazovat chybu AADSTS900439-USGClientNotSupportedOnPublicEndpoint. 

Dojde k postupnému zavedení této změny s tím, že se očekává, že bude dokončeno v rámci všech aplikací od června 2020. Další podrobnosti najdete v [příspěvku blogu Azure Government](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/).

---

### <a name="saml-single-logout-request-now-sends-nameid-in-the-correct-format"></a>Požadavek na jednotné odhlášení SAML teď pošle NameID ve správném formátu.

**Zadejte:** Určí  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů
 
Když uživatel klikne na odhlášení (například na portálu Mojeapl), pošle služba Azure AD zprávu o jednotném odhlášení SAML do každé aplikace, která je v uživatelské relaci aktivní a má nakonfigurovanou adresu URL pro odhlášení. Tyto zprávy obsahují NameID v trvalém formátu.

Pokud původní token pro přihlášení SAML používá jiný formát pro NameID (např. e-mail nebo hlavní název uživatele (UPN)), pak aplikace SAML nemůže korelovat NameID zprávy ve zprávě pro odhlášení do existující relace (protože NameIDs použitá v obou zprávách je odlišná), což způsobilo, že se zpráva o odhlášení zahodila aplikací SAML a uživatel zůstane přihlášený. Tato oprava vytvoří zprávu pro odhlášení konzistentní s NameID nakonfigurovaným pro aplikaci.

---

### <a name="hybrid-identity-administrator-role-is-now-available-with-cloud-provisioning"></a>Role správce hybridní identity je teď k dispozici s zřizováním cloudu.

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování cloudu Azure AD  
**Schopnost produktu:** Správa životního cyklu identit
 
Správci IT můžou začít používat novou roli Hybrid admin jako nejnižší privilegovanou roli pro nastavení cloudového zřizování Azure ADConnect. U této nové role už nemusíte k instalaci a konfiguraci zřizování cloudu používat roli globálního správce. [Další informace](../roles/delegate-by-task.md#connect).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2020"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – květen 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
V květnu 2020 jsme do Galerie aplikací přidali následující 36 nové aplikace s podporou federace:

[Moula](https://moula.com.au/pay/merchants), [Surveypal](https://www.surveypal.com/app), [Kbot365](https://www.konverso.ai/virtual-assistant-digital-workplace/), [TackleBox](http://www.tacklebox.app/), [Powell Teams](https://powell-software.com/en/powell-teams-en/), [Talentsoft Assistant](https://msteams.talent-soft.com/), [přehledy](https://teams.asc-recording.app/product)o prostředcích ASC, [Humanage](../saas-apps/humanage-tutorial.md) [GO1](https://www.go1.com/), [B](https://b-engaged.se/), [COMPETELLA kontaktní centra](http://www.competella.com/) [, ASITE,](http://www.asite.com/) [ImageSoft identity](https://identity.imagesoftinc.com/), [My IBISWorld](https://identity.imagesoftinc.com/), [unsuite](../saas-apps/insuite-tutorial.md), [Change Process Management](../saas-apps/change-process-management-tutorial.md), [Cyara CX](../saas-apps/cyara-cx-assurance-platform-tutorial.md), [Smart Global vládnutí](../saas-apps/smart-global-governance-tutorial.md), [Prezi](../saas-apps/prezi-tutorial.md), [Mapbox](../saas-apps/mapbox-tutorial.md), [Datava Enterprise Service Platform](../saas-apps/datava-enterprise-service-platform-tutorial.md), [Whimsical](../saas-apps/whimsical-tutorial.md), [Trelica](../saas-apps/trelica-tutorial.md), [EasySSO pro Confluence](../saas-apps/easysso-for-confluence-tutorial.md), [EasySSO](../saas-apps/easysso-for-bitbucket-tutorial.md), Bitbucket [, EasySSO](../saas-apps/easysso-for-bamboo-tutorial.md), Bamboo [, Torii,](../saas-apps/torii-tutorial.md)Axiad, ColorTokens, ZTNA [ColorTokens ZTNA](../saas-apps/colortokens-ztna-tutorial.md) [ShareVault](../saas-apps/sharevault-tutorial.md), [Všichni domů CRM](../saas-apps/anyone-home-crm-tutorial.md) [, CCH,](../saas-apps/cch-tagetik-tutorial.md) [Vyond](../saas-apps/vyond-tutorial.md) [Contact Center](../saas-apps/ice-contact-center-tutorial.md) [Axiad Cloud](../saas-apps/axiad-cloud-tutorial.md) [TextExpander](../saas-apps/textexpander-tutorial.md) [askSpoke](../saas-apps/askspoke-tutorial.md)

Dokumentaci ke všem aplikacím si můžete také najít tady https://aka.ms/AppsTutorial .

Pokud chcete zobrazit seznam aplikací v galerii aplikací Azure AD, přečtěte si prosím podrobnosti tady https://aka.ms/AzureADAppRequest .

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>Režim jenom pro sestavy pro podmíněný přístup je teď všeobecně dostupný.

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity & ochrana

[Režim pouze sestav pro podmíněný přístup Azure AD](../conditional-access/concept-conditional-access-report-only.md) umožňuje vyhodnotit výsledek zásady bez vynucení řízení přístupu. V rámci vaší organizace můžete testovat zásady jenom pro sestavy a porozumět jejich dopadu, než je povolíte, aby bylo nasazení bezpečnější a jednodušší. Během posledních několika měsíců jsme se seznámili se silným přijetím režimu jenom pro sestavy – přes 26M uživatelé už jsou v oboru zásady jenom pro sestavy. V dnešní době se nové zásady podmíněného přístupu Azure AD vytvoří ve výchozím nastavení v režimu pouze sestavy. To znamená, že můžete sledovat dopad vašich zásad od okamžiku, kdy byly vytvořeny. A pro ty z vás, kteří používají rozhraní API MS graphu, můžete také [programově spravovat zásady jenom pro sestavy](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta) . 

---

### <a name="self-service-sign-up-for-guest-users"></a>Samoobslužná registrace pro uživatele typu Host

**Zadejte:** Nová funkce  
**Kategorie služby:** B2B  
**Schopnost produktu:** B2B/B2C
 
S externími identitami v Azure AD můžete uživatelům mimo vaši organizaci umožnit přístup k vašim aplikacím a prostředkům a zároveň jim umožnit přihlašovat se pomocí libovolné identity, které preferuje. Když sdílíte aplikaci s externími uživateli, možná nebudete vždy znát předem, který bude potřebovat přístup k aplikaci. Díky [samoobslužné registraci](../external-identities/self-service-sign-up-overview.md)můžete uživatelům typu Host povolit registraci a získat účet hosta pro vaše obchodní aplikace (LOB). Registrační tok se dá vytvořit a přizpůsobit tak, aby podporoval Azure AD a sociální identity. Během registrace můžete také shromažďovat další informace o uživateli.

---

 ### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>Přehledy podmíněného přístupu a sešit vytváření sestav jsou všeobecně dostupné

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity & ochrana

[Sešit přehledů a sestav](../conditional-access/howto-conditional-access-insights-reporting.md) poskytuje správcům souhrnné zobrazení podmíněného přístupu Azure AD ve svém tenantovi. Díky možnosti výběru jednotlivých zásad můžou správci lépe pochopit, co jednotlivé zásady dělá, a monitorovat všechny změny v reálném čase. Sešit streamuje data uložená v Azure Monitor, která můžete nastavit během několika minut [po těchto pokynech](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md). Aby byl řídicí panel lépe zjistitelný, přesunuli jsme ho na kartu nové přehledy a sestavy v nabídce podmíněný přístup Azure AD.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>Okno podrobností zásad pro podmíněný přístup je ve verzi Public Preview.

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity & ochrana

V okně [Podrobnosti o nové zásadě](../conditional-access/troubleshoot-conditional-access.md) se zobrazí přiřazení, podmínky a ovládací prvky splněné během hodnocení zásad podmíněného přístupu. Přístup k oknu můžete zobrazit tak, že na kartách podmíněného přístupu nebo pouze sestavy v podrobnostech pro přihlášení vyberete řádek.

---

### <a name="new-query-capabilities-for-directory-objects-in-microsoft-graph-are-in-public-preview"></a>Nové funkce dotazů pro objekty adresáře v Microsoft Graph jsou v Public Preview

**Zadejte:** Nová funkce  
**Kategorie služby:** **Funkce produktu** MS Graph: vývojářské prostředí

Zavádí se nové možnosti pro Microsoft Graph rozhraní API objektů adresáře, což povoluje operace počet, vyhledávání, filtrování a řazení. To vývojářům umožní rychle se dotazovat na naše objekty adresáře bez řešení, jako je filtrování a řazení v paměti. Další informace najdete v tomto [blogovém příspěvku](https://aka.ms/CountFilterMSGraphAAD).

V současné době je Public Preview a hledáme zpětnou vazbu. Pošlete prosím své komentáře s tímto [stručným průzkumem](https://aka.ms/MsGraphAADSurveyDocs).

---

### <a name="configure-saml-based-single-sign-on-using-microsoft-graph-api-beta"></a>Konfigurace jednotného přihlašování založeného na SAML pomocí rozhraní Microsoft Graph API (beta verze)

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ
 
K dispozici je teď podpora pro vytváření a konfiguraci aplikace z Galerie Azure AD pomocí rozhraní API MS graphu ve verzi beta. Pokud pro více instancí aplikace potřebujete nastavit jednotné přihlašování založené na SAML, Ušetřete čas pomocí rozhraní Microsoft Graph API pro [automatizaci konfigurace jednotného přihlašování založeného na SAML](/graph/application-saml-sso-configure-api).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---may-2020"></a>Nové zřizovací konektory v galerii aplikací Azure AD – květen 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** integrace třetích stran
 
Nyní můžete automatizovat vytváření, aktualizaci a odstraňování uživatelských účtů pro tyto nově integrované aplikace:

* [8x8](../saas-apps/8x8-provisioning-tutorial.md)
* [Juno Journey](../saas-apps/juno-journey-provisioning-tutorial.md)
* [MediusFlow](../saas-apps/mediusflow-provisioning-tutorial.md)
* [New Relic podle organizace](../saas-apps/new-relic-by-organization-provisioning-tutorial.md)
* [Oracle Cloud Infrastructure Console](../saas-apps/oracle-cloud-infratstructure-console-provisioning-tutorial.md)

Další informace o tom, jak lépe zabezpečit vaši organizaci pomocí automatizovaného zřizování uživatelských účtů, najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="saml-token-encryption-is-generally-available"></a>Šifrování tokenu SAML je všeobecně dostupné.

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ
 
[Šifrování tokenu SAML](../manage-apps/howto-saml-token-encryption.md) umožňuje aplikacím nakonfigurovat, aby přijímaly šifrované kontrolní výrazy SAML. Tato funkce je teď obecně dostupná ve všech cloudech.
 
---

### <a name="group-name-claims-in-application-tokens-is-generally-available"></a>Deklarace identity názvu skupiny v tokenech aplikace jsou všeobecně dostupné.

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ
 
Deklarace skupin vystavené v tokenu se teď dají omezit jenom na tyto skupiny přiřazené k aplikaci.  To je obzvláště důležité, když jsou uživatelé členy velkého počtu skupin a hrozí riziko překročení omezení velikosti tokenu. Díky této nové funkci je všeobecně dostupná možnost [přidávat názvy skupin do tokenů](../hybrid/how-to-connect-fed-group-claims.md) .
 
---

### <a name="workday-writeback-now-supports-setting-work-phone-number-attributes"></a>Zpětný zápis do Workday teď podporuje nastavení atributů pracovní telefonní číslo.

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** Správa životního cyklu identit
 
Vylepšili jsme aplikaci pro zajištění zpětného zápisu do pracovních verzí, která teď podporuje zpětný zápis telefonních čísel a atributů mobilního čísla. Kromě e-mailu a uživatelského jména můžete teď v rámci služby Azure AD a Workday nakonfigurovat aplikaci pro zajištění zpětného zápisu do Workday k flowu hodnot telefonního čísla. Další podrobnosti o tom, jak nakonfigurovat zpětný zápis pro telefonní číslo, najdete v kurzu aplikace pro [zpětný zápis do Workday](../saas-apps/workday-writeback-tutorial.md) . 

---

### <a name="publisher-verification-preview"></a>Ověřování vydavatele (Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Jiná  
**Schopnost produktu:** Vývojářské prostředí
 
Ověřování vydavatele (Preview) pomáhá správcům a koncovým uživatelům pochopit pravost vývojářů aplikací, které jsou integrovány s platformou Microsoft identity. Podrobnosti najdete v tématu [ověření vydavatele (Preview)](../develop/publisher-verification-overview.md).
 
---

### <a name="authorization-code-flow-for-single-page-apps"></a>Tok autorizačního kódu pro jednostránkové aplikace

**Zadejte:** Kategorie změněné **služby funkcí:** ověřování **– schopnost produktu:** vývojové prostředí

Vzhledem k tomu, že se v moderních prohlížečích používají [omezení souborů cookie třetích stran](../develop/reference-third-party-cookies-spas.md), jako je Safari ITP, bude muset jednostránkové používat tok autorizačního kódu místo implicitního toku pro zachování jednotného přihlašování. MSAL.js v 2. x teď bude podporovat tok autorizačního kódu. V takovém případě odpovídají aktualizace Azure Portal, takže můžete zabezpečené ověřování pomocí hesla zadat jako "Spa" a použít tok kódu ověřování. Pokyny najdete [v tématu rychlý Start: přihlášení uživatelů a získání přístupového tokenu v zabezpečeném kódu v jazyce JavaScript pomocí toku kódu ověřování](../develop/quickstart-v2-javascript-auth-code.md).

---

### <a name="improved-filtering-for-devices-is-in-public-preview"></a>Vylepšené filtrování pro zařízení je v Public Preview

**Zadejte:** Změněná funkce   
**Kategorie služby:** **Schopnost produktu** správy zařízení: Správa životního cyklu zařízení
 
Dřív jste mohli použít jenom filtry "povoleno" a "datum aktivity". Teď můžete [seznam zařízení filtrovat podle dalších vlastností](../devices/device-management-azure-portal.md#device-list-filtering-preview), včetně typu operačního systému, typu spojení, dodržování předpisů a dalších. Tyto doplňky by měly zjednodušit vyhledání konkrétního zařízení.

---

### <a name="the-new-app-registrations-experience-for-azure-ad-b2c-is-now-generally-available"></a>Nové prostředí Registrace aplikací pro Azure AD B2C je teď všeobecně dostupné

**Zadejte:** Změněná funkce   
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** Správa životního cyklu identit
 
Nové prostředí Registrace aplikací pro Azure AD B2C je teď všeobecně dostupné. 

Dřív jste museli spravovat aplikace B2C pro spotřebitele odděleně od ostatních aplikací, které využívají starší možnosti aplikací. Který na různých místech v Azure znamenal jiné prostředí pro vytváření aplikací.

Nové prostředí zobrazuje všechny registrace aplikací B2C a registrace aplikací Azure AD na jednom místě a poskytuje konzistentní způsob jejich správy. Bez ohledu na to, jestli potřebujete spravovat zákaznickou aplikaci nebo aplikaci, která má přístup k Microsoft Graph pro programovou správu prostředků Azure AD B2C, stačí se naučit jenom jeden ze způsobů, jak to udělat.

Nové prostředí můžete oslovit tak, že přejdete na službu Azure AD B2C a vyberete okno Registrace aplikací. Prostředí je dostupné taky z Azure Active Directory služby.

Prostředí Azure AD B2C Registrace aplikací je založené na obecném [prostředí pro registraci aplikací](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) pro klienty Azure AD, ale je přizpůsobené pro Azure AD B2C. Starší verze aplikací budou v budoucnu zastaralé.

Další informace najdete [v novém prostředí pro registraci aplikací pro Azure AD B2C](../../active-directory-b2c/app-registrations-training-guide.md).

---

## <a name="april-2020"></a>Duben 2020

### <a name="combined-security-info-registration-experience-is-now-generally-available"></a>Kombinované prostředí pro registraci informací o zabezpečení je teď všeobecně dostupné.

**Zadejte:** Nová funkce

**Kategorie služby:** Ověřování (přihlášení)

**Schopnost produktu:** Zabezpečení identity & ochrana

Kombinované prostředí pro registraci pro Multi-Factor Authentication (MFA) a Self-Service resetování hesla (SSPR) je teď všeobecně dostupné. Toto nové prostředí pro registraci umožňuje uživatelům zaregistrovat se pro MFA a SSPR v jediném podrobném procesu. Když nasadíte nové prostředí pro vaši organizaci, můžou se uživatelé registrovat kratší dobu a s menším počtem problémů. Podívejte se na Blogový příspěvek [tady](https://bit.ly/3etiRyQ).

---

### <a name="continuous-access-evaluation"></a>Vyhodnocování průběžného přístupu

**Zadejte:** Nová funkce

**Kategorie služby:** Ověřování (přihlášení)

**Schopnost produktu:** Zabezpečení identity & ochrana

Vyhodnocování průběžného přístupu je nová funkce zabezpečení, která umožňuje v reálném čase provádět vynucování zásad pro předávající strany, které využívají přístupové tokeny Azure AD, když dojde k událostem v Azure AD (například odstranění uživatelského účtu). Tuto funkci pro týmy a klienty Outlooku nejprve vydáváme. Další podrobnosti najdete v našem [blogu](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933) a v  [dokumentaci](../conditional-access/concept-continuous-access-evaluation.md).

---

### <a name="sms-sign-in-firstline-workers-can-sign-in-to-azure-ad-backed-applications-with-their-phone-number-and-no-password"></a>Přihlášení SMS: Firstline se můžou přihlásit k aplikacím zálohovaným přes Azure AD pomocí jejich telefonního čísla a bez hesla.

**Zadejte:** Nová funkce

**Kategorie služby:** Ověřování (přihlášení)

**Schopnost produktu:** Ověřování uživatelů

Office spouští řadu mobilních obchodních aplikací, které využívají v netradičních organizacích, a zaměstnancům ve velkých organizacích, které jako primární způsob komunikace nepoužívají e-mail. Tyto aplikace cílí na prvotní zaměstnanců, pracovních procesů bez pracovního oddělení nebo maloobchodních zaměstnanců, kteří nemusí dostávat e-mailovou adresu od svého zaměstnavatele, mít přístup k počítači nebo k němu. Tento projekt umožní těmto zaměstnancům přihlašovat se k obchodním aplikacím zadáním telefonního čísla a roundtripping kódu. Další podrobnosti najdete v [dokumentaci pro správce](../authentication/howto-authentication-sms-signin.md) a v [dokumentaci pro koncové uživatele](../user-help/sms-sign-in-explainer.md).

---

### <a name="invite-internal-users-to-use-b2b-collaboration"></a>Pozvat interní uživatele, aby mohli používat spolupráci B2B

**Zadejte:** Nová funkce

**Kategorie služby:** B2B

**Schopnost produktu:**

Rozšiřujeme možnosti pozvánky B2B, aby bylo možné přizvat stávající interní účty k používání přihlašovacích údajů pro spolupráci B2B. To se provádí předáním objektu uživatele do rozhraní API pro pozvání kromě typických parametrů, jako je pozvaní e-mailová adresa. ID objektu uživatele, hlavní název uživatele (UPN), členství ve skupině, přiřazení aplikace atd. zůstane beze změny, ale bude se používat B2B k ověření s přihlašovacími údaji svého domovského tenanta, nikoli pomocí interních přihlašovacích údajů, které používali před pozvánkou. Podrobnosti najdete v [dokumentaci](../external-identities/invite-internal-users.md).

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>Režim jenom pro sestavy pro podmíněný přístup je teď všeobecně dostupný.

**Zadejte:** Nová funkce

**Kategorie služby:** Podmíněný přístup

**Schopnost produktu:** Zabezpečení identity & ochrana

[Režim pouze sestav pro podmíněný přístup Azure AD](../conditional-access/concept-conditional-access-report-only.md) umožňuje vyhodnotit výsledek zásady bez vynucení řízení přístupu. V rámci vaší organizace můžete testovat zásady jenom pro sestavy a porozumět jejich dopadu, než je povolíte, aby bylo nasazení bezpečnější a jednodušší. Během posledních několika měsíců jsme se seznámili se silným přijetím režimu pouze pro sestavy s více než 26M uživateli, kteří už v oboru zásady jenom pro sestavy. V tomto oznámení se nové zásady podmíněného přístupu Azure AD vytvoří ve výchozím nastavení v režimu pouze sestavy. To znamená, že můžete sledovat dopad vašich zásad od okamžiku, kdy byly vytvořeny. A pro ty z vás, kteří používají rozhraní API MS graphu, můžete také [programově spravovat zásady jenom pro sestavy](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta). 

---

### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>Přehledy podmíněného přístupu a sešit vytváření sestav jsou všeobecně dostupné

**Zadejte:** Nová funkce

**Kategorie služby:** Podmíněný přístup

**Schopnost produktu:** Zabezpečení identity & ochrana

Sešit s přehledem podmíněného přístupu [a vytváření sestav](../conditional-access/howto-conditional-access-insights-reporting.md) poskytuje správcům přehled o podmíněném přístupu Azure AD ve svém tenantovi. Díky možnosti výběru jednotlivých zásad můžou správci lépe pochopit, co jednotlivé zásady dělá, a monitorovat všechny změny v reálném čase. Sešit streamuje data uložená v Azure Monitor, která můžete nastavit během několika minut [po těchto pokynech](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md). Aby byl řídicí panel lépe zjistitelný, přesunuli jsme ho na kartu nové přehledy a sestavy v nabídce podmíněný přístup Azure AD.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>Okno podrobností zásad pro podmíněný přístup je ve verzi Public Preview.

**Zadejte:** Nová funkce

**Kategorie služby:** Podmíněný přístup

**Schopnost produktu:** Zabezpečení identity & ochrana

V okně [Podrobnosti o nové zásadě](../conditional-access/troubleshoot-conditional-access.md) se zobrazí, která přiřazení, podmínky a ovládací prvky byly splněné při vyhodnocování zásad podmíněného přístupu. Přístup k oknu můžete zobrazit tak, že na kartách **podmíněného přístupu** nebo **pouze sestavy** v podrobnostech pro přihlášení vyberete řádek.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2020"></a>Nové federované aplikace dostupné v Aplikace Azure AD galerii – duben 2020

**Zadejte:** Nová funkce

**Kategorie služby:** Podnikové aplikace

**Schopnost produktu:** integrace třetích stran

V dubnu 2020 jsme do Galerie aplikací přidali tyto 31 nových aplikací s podporou federace: 

[SincroPool Apps](https://www.sincropool.com/), [SmartDB](https://hibiki.dreamarts.co.jp/smartdb/trial/), [float](../saas-apps/float-tutorial.md), [LMS365](https://lms.365.systems/), [iwt Suite Suite](../saas-apps/iwt-procurement-suite-tutorial.md), [Lunni](https://lunni.fi/), [EasySSO pro JIRA](../saas-apps/easysso-for-jira-tutorial.md), [Virtual Training Academy](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft), [Meraki Dashboard](../saas-apps/meraki-dashboard-tutorial.md), [Microsoft 365 stěhovací](https://app.mover.io/login), [mluvčí zapojení](https://speakerengage.com/login.php), [upřímně](../saas-apps/honestly-tutorial.md), [Ally](../saas-apps/ally-tutorial.md), [DutyFlow](https://app.dutyflow.nl/), [AlertMedia](../saas-apps/alertmedia-tutorial.md), [gr8 lidé](../saas-apps/gr8-people-tutorial.md), [Pendo](../saas-apps/pendo-tutorial.md), [HighGround](../saas-apps/highground-tutorial.md), [harmonie](../saas-apps/harmony-tutorial.md), Timetabling [, SynchroNet](../saas-apps/synchronet-click-tutorial.md), [empower](https://www.made-in-office.com/en/) [litmus, GroupTalk](../saas-apps/timetabling-solutions-tutorial.md) [, Frontify](../saas-apps/mongodb-cloud-tutorial.md) [, MongoDB,](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/) [TickitLMS](../saas-apps/nitro-productivity-suite-tutorial.md) , [díky Coco, nitro](../saas-apps/litmus-tutorial.md) [, TMWS](https://recorder.grouptalk.com/) [,](../saas-apps/frontify-tutorial.md)( [TickitLMS Learn](../saas-apps/tickitlms-learn-tutorial.md) [)](https://review.docs.microsoft.com/azure/active-directory/saas-apps/trend-micro-tutorial) [Fortes Change Cloud](../saas-apps/fortes-change-cloud-tutorial.md)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](../azuread-dev/howto-app-gallery-listing.md).

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>Podpora rozdílových dotazů Microsoft Graph pro oAuth2PermissionGrant k dispozici pro Public Preview

**Zadejte:** Nová funkce

**Kategorie služby:** MS Graph

**Schopnost produktu:** Vývojářské prostředí

Pro verzi Public Preview je k dispozici rozdílový dotaz pro oAuth2PermissionGrant. Nyní můžete sledovat změny bez nutnosti průběžného dotazování Microsoft Graph. [Další informace](/graph/api/oAuth2PermissionGrant-delta?tabs=http&view=graph-rest-beta)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>Obecně dostupná podpora Microsoft Graphch rozdílových dotazů pro kontakt organizace

**Zadejte:** Nová funkce

**Kategorie služby:** MS Graph

**Schopnost produktu:** Vývojářské prostředí

Rozdílový dotaz pro kontakty organizace je všeobecně k dispozici! Nyní můžete sledovat změny v produkčních aplikacích bez nutnosti průběžného dotazování Microsoft Graph. Nahraďte veškerý stávající kód, který se průběžně dotazuje orgContact data na rozdílový dotaz, aby významně zvýšil výkon. [Další informace](/graph/api/orgcontact-delta?tabs=http&view=graph-rest-1.0)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>Podpora rozdílových dotazů Microsoft Graph pro aplikaci všeobecně dostupná

**Zadejte:** Nová funkce

**Kategorie služby:** MS Graph

**Schopnost produktu:** Vývojářské prostředí

Rozdílový dotaz pro aplikace je všeobecně k dispozici! Nyní můžete sledovat změny v produkčních aplikacích bez nutnosti průběžného dotazování Microsoft Graph. Nahraďte veškerý stávající kód, který nepřetržitě dotazuje data aplikací podle rozdílového dotazu a významně tak vylepšit výkon. [Další informace](/graph/api/application-delta?view=graph-rest-1.0)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>Podpora rozdílových dotazů Microsoft Graph pro jednotky pro správu, které jsou k dispozici pro Public Preview

**Zadejte:** Nová funkce

**Kategorie služby:** MS Graph

**Schopnost produktu:** Pro veřejné verze Preview je k dispozici rozdílový dotaz vývojářského prostředí pro jednotky pro správu. Nyní můžete sledovat změny bez nutnosti průběžného dotazování Microsoft Graph. [Další informace](/graph/api/administrativeunit-delta?tabs=http&view=graph-rest-beta)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>Spravovat telefonní čísla pro ověřování a další informace v nových Microsoft Graphch rozhraní API beta verze

**Zadejte:** Nová funkce

**Kategorie služby:** MS Graph

**Schopnost produktu:** Vývojářské prostředí

Tato rozhraní API jsou klíčovým nástrojem pro správu metod ověřování vašich uživatelů. Nyní můžete programově předregistrovat a spravovat ověřovatele používané pro MFA a Samoobslužné resetování hesla (SSPR). To je jedna z nejžádanějších funkcí v rámci Azure MFA, SSPR a Microsoft Graphch prostorů. Nová rozhraní API, která jsme vydali v tomto Wave, vám poskytnou možnost:

- Čtení, přidávání, aktualizace a odebírání telefonických ověřování uživatele
- Resetování hesla uživatele
- Zapnutí a vypnutí služby SMS – přihlášení

Další informace najdete v tématu [Přehled rozhraní API metody ověřování Azure AD](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

---

### <a name="administrative-units-public-preview"></a>Public Preview jednotky pro správu

**Zadejte:** Nová funkce

**Kategorie služby:** Role Azure AD

**Schopnost produktu:** Access Control

Jednotky pro správu umožňují udělit oprávnění správce, která jsou omezená na oddělení, oblast nebo jiný segment vaší organizace, kterou definujete. Jednotky pro správu můžete použít k delegování oprávnění pro místní správce nebo k nastavení zásad na podrobné úrovni. Správce uživatelských účtů může například aktualizovat informace o profilu, resetovat hesla a přiřazovat licence uživatelům pouze ve své jednotce pro správu.

Pomocí jednotek pro správu může centrální správce:

- Vytvoření jednotky pro správu pro decentralizovanou správu prostředků
- Přiřazení role s oprávněními správce jenom pro uživatele Azure AD v jednotce pro správu
- Podle potřeby naplňte jednotky pro správu uživateli a skupinami.

Další informace najdete v tématu [Správa jednotek pro správu v Azure Active Directory (Preview)](../users-groups-roles/directory-administrative-units.md).

---

### <a name="printer-administrator-and-printer-technician-built-in-roles"></a>Správce tiskáren a předdefinované role technika tiskárny

**Zadejte:** Nová funkce

**Kategorie služby:** Role Azure AD

**Schopnost produktu:** Access Control

**Správce tiskárny**: uživatelé s touto rolí můžou registrovat tiskárny a spravovat všechny aspekty všech konfigurací tiskáren v rámci univerzálního tiskového řešení Microsoftu, včetně nastavení univerzálního tiskového konektoru. Můžou si udělit souhlas s všemi delegovanými žádostmi o oprávnění k tisku. Správci tiskáren mají také přístup k tiskovým sestavám. 

**Technik tiskárny**: uživatelé s touto rolí můžou registrovat tiskárny a spravovat stav tiskáren v řešení univerzálního tisku Microsoftu. Můžou si taky přečíst všechny informace o konektoru. Klíčové úkoly, které technik tiskárny nemůže dělat, jsou nastavení oprávnění uživatele na tiskárnách a sdílení tiskáren. [Další informace](../roles/permissions-reference.md#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>Integrovaná role správce hybridní identity

**Zadejte:** Nová funkce

**Kategorie služby:** Role Azure AD

**Schopnost produktu:** Access Control

Uživatelé v této roli můžou povolit, konfigurovat a spravovat služby a nastavení související s povolením hybridní identity ve službě Azure AD. Tato role umožňuje nakonfigurovat Azure AD na jednu ze tří podporovaných metod ověřování&#8212;hesla pro synchronizaci hodnot hash hesel (KOSMETICE), předávacího ověřování (PTA) nebo federace (AD FS nebo poskytovatele federace třetí strany) &#8212;a nasazení souvisejících místních infrastruktur, aby je bylo možné povolit. Místní infrastruktura zahrnuje zřizování a PTA agenty. Tato role uděluje možnost Povolit bezproblémové jednoduché Sign-On (S-SSO), aby se zajistilo bezproblémové ověřování na zařízeních s jiným systémem než Windows 10 nebo na počítačích s jiným systémem než Windows Server 2016. Kromě toho tato role uděluje možnost Zobrazit protokoly přihlášení a získat přístup ke stavu a analýzám pro účely monitorování a řešení potíží. [Další informace](../roles/permissions-reference.md#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>Integrovaná role správce sítě

**Zadejte:** Nová funkce

**Kategorie služby:** Role Azure AD

**Schopnost produktu:** Access Control

Uživatelé s touto rolí můžou zkontrolovat doporučení k architektuře hraničního sítě od Microsoftu, která jsou založená na telemetrie sítě od jejich uživatelských umístění. Výkon sítě pro Microsoft 365 spoléhá na pečlivou architekturu hraniční sítě zákazníka v podniku, která je obecně specifická pro konkrétní uživatelské umístění. Tato role umožňuje upravovat zjištěná umístění uživatelů a konfiguraci síťových parametrů pro tato umístění, aby bylo usnadněno lepší měření telemetrie a doporučení pro návrh. [Další informace](../roles/permissions-reference.md#network-administrator)

---

### <a name="bulk-activity-and-downloads-in-the-azure-ad-admin-portal-experience"></a>Hromadná aktivita a stahování v prostředí portálu pro správu Azure AD

**Zadejte:** Nová funkce

**Kategorie služby:** Správa uživatelů

**Schopnost produktu:** Službě

Teď můžete provádět hromadné aktivity uživatelů a skupin ve službě Azure AD tak, že nahrajete soubor CSV na portálu pro správu Azure AD. Můžete vytvářet uživatele, odstraňovat uživatele a zvát uživatele typu Host. A můžete přidat nebo odebrat členy ze skupiny.

Můžete si také stáhnout seznamy prostředků Azure AD z prostředí portálu pro správu Azure AD. Můžete si stáhnout seznam uživatelů v adresáři, seznam skupin v adresáři a členy konkrétní skupiny.

Další informace najdete v následujících informacích:

- [Vytvoření uživatelů](../enterprise-users/users-bulk-add.md) nebo [pozvání uživatelů typu Host](../external-identities/tutorial-bulk-invite.md)
- [Odstranit uživatele](../enterprise-users/users-bulk-delete.md) nebo [Obnovit odstraněné uživatele](../enterprise-users/users-bulk-restore.md)
- [Stažení seznamu uživatelů](../enterprise-users/users-bulk-download.md) nebo [stažení seznamu skupin](../enterprise-users/groups-bulk-download.md)
- [Přidání (import) členů](../enterprise-users/groups-bulk-import-members.md) nebo [odebrání členů](../enterprise-users/groups-bulk-remove-members.md) nebo [stažení seznamu členů](../enterprise-users/groups-bulk-download-members.md) pro skupinu

---

### <a name="my-staff-delegated-user-management"></a>Správa uživatelů delegovaná od zaměstnanců

**Zadejte:** Nová funkce

**Kategorie služby:** Správa uživatelů

**Schopnost produktu:**

Moji zaměstnanci umožňují správcům Firstline, jako je Správce úložiště, zajistit, aby jejich zaměstnanci měli přístup k účtům Azure AD. Namísto spoléhání na centrální Helpdesk můžou organizace delegovat běžné úlohy, jako je resetování hesel nebo změna telefonních čísel, na Firstline Manager. Uživatel, který nemá přístup ke svému účtu, může pomocí mých zaměstnanců znovu získat přístup jenom v několika kliknutích, aniž by to vyžadovalo Helpdesk nebo pracovníky IT. Další informace najdete v tématu [Správa uživatelů pomocí možnosti Moji zaměstnanci (Preview)](../users-groups-roles/my-staff-configure.md) a [delegování správy uživatelů pomocí mých zaměstnanců (Preview)](../user-help/my-staff-team-manager.md).

---

### <a name="an-upgraded-end-user-experience-in-access-reviews"></a>Upgradované prostředí pro koncové uživatele v recenzích přístupu

**Zadejte:** Změněná funkce

**Kategorie služby:** Kontroly přístupu

**Schopnost produktu:** Zásady správného řízení identity

Na portálu moje aplikace jsme aktualizovali možnosti kontrolora pro kontroly přístupu ke službě Azure AD. Po skončení dubna se recenzenti, kteří se přihlásili ke kontrolám přístupu ke službě Azure AD, zobrazí informační zpráva s upozorněním, že si budou moct vyzkoušet aktualizované prostředí v mém přístupu. Upozorňujeme, že aktualizovaná prostředí kontroly přístupu nabízí stejné funkce jako aktuální prostředí, ale s vylepšeným uživatelským rozhraním nad novými funkcemi, které umožní vašim uživatelům zvýšit produktivitu. [Další informace o aktualizovaném prostředí najdete tady](../governance/perform-access-review.md). Tato verze Public Preview bude poslední až do konce července 2020. Na konci července budou recenzenti, kteří se nevyjádřili do prostředí verze Preview, automaticky přesměrováni na můj přístup, aby mohli provádět kontroly přístupu. Pokud chcete, aby se kontroloři trvale přepnuli do prostředí Preview v mém přístupu, [uveďte prosím žádost](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u).

---

### <a name="workday-inbound-user-provisioning-and-writeback-apps-now-support-the-latest-versions-of-workday-web-services-api"></a>Aplikace pro zřizování a zpětného zápisu příchozích uživatelů v Workday teď podporují nejnovější verze rozhraní API webových služeb Workday.

**Zadejte:** Změněná funkce

**Kategorie služby:** Zřizování aplikací

**Schopnost produktu:** 

Na základě zpětné vazby od zákazníků jsme teď v galerii podnikových aplikací aktualizovali aplikace pro zřizování a zpětný zápis uživatelů Workday na podporu nejnovějších verzí rozhraní API WWS (Workday Web Services). V této změně můžou zákazníci zadat verzi rozhraní API WWS, kterou by chtěli použít v připojovacím řetězci. Díky tomu můžou zákazníci získat další atributy pro personální přístup k dispozici v rámci vydání pracovního dne. Aplikace pro zpětný zápis v Workday teď používá doporučenou webovou službu Change_Work_Contact_Info Workday k překonání omezení Maintain_Contact_Info.

Pokud v připojovacím řetězci není zadaná žádná verze, aplikace ve výchozím nastavení pro příchozí zřizování v rámci Workday bude nadále používat WWS v 21.1 k přepínání na nejnovější rozhraní API Workday pro příchozí zřizování uživatelů, aby mohli zákazníci aktualizovat připojovací řetězec, jak je uvedeno [v kurzu](../saas-apps/workday-inbound-tutorial.md#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles) , a také aktualizovat XPath používané pro atributy Workday, jak je popsáno v [Referenční příručce k atributům Workday](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30). 

Pokud chcete používat nové rozhraní API pro zpětný zápis, nemusíte v aplikaci pro zřizování služby pro zápis do pracovního dne vyžadovat žádné změny. Na straně pracovního dne zajistěte, aby měl účet ISU (Workday Integration System User) oprávnění k vyvolání Change_Work_Contact obchodního procesu, jak je uvedeno v části kurz, v tématu [Konfigurace oprávnění zásad zabezpečení obchodních procesů](../saas-apps/workday-inbound-tutorial.md#configuring-business-process-security-policy-permissions). 

Aktualizovali jsme náš [Průvodce kurzy](../saas-apps/workday-inbound-tutorial.md) , který by odrážel novou podporu verze API.

---

### <a name="users-with-default-access-role-are-now-in-scope-for-provisioning"></a>Uživatelé s výchozí rolí přístupu teď jsou v oboru pro zřizování.

**Zadejte:** Změněná funkce

**Kategorie služby:** Zřizování aplikací

**Schopnost produktu:** Správa životního cyklu identit

V minulosti byly uživatelé s výchozí rolí přístupu mimo obor pro zřizování. Slyšeli jsme, že zákazníci chtějí mít tuto roli v oboru pro zřizování. Od 16. dubna 2020 budou všechny nové konfigurace zřizování umožňovat zřízení uživatelů s výchozí rolí přístupu. Postupně změníme chování stávajících zřizovacích konfigurací tak, aby podporovaly zřizování uživatelů s touto rolí. [Další informace](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

---

### <a name="updated-provisioning-ui"></a>Aktualizované uživatelské rozhraní pro zřizování

**Zadejte:** Změněná funkce

**Kategorie služby:** Zřizování aplikací

**Schopnost produktu:** Správa životního cyklu identit

Naše prostředí pro zřizování jsme aktualizovali tak, aby se vytvořilo lépe zaměřené zobrazení správy. Když přejdete do okna zřizování pro podnikovou aplikaci, která už je nakonfigurovaná, budete moct snadno monitorovat průběh zřizování a spravovat akce, jako je spuštění, zastavení a restartování zřizování. [Další informace](../app-provisioning/configure-automatic-user-provisioning-portal.md)

---

### <a name="dynamic-group-rule-validation-is-now-available-for-public-preview"></a>Ověřování pravidla dynamické skupiny je teď k dispozici pro Public Preview

**Zadejte:** Změněná funkce

**Kategorie služby:** Správa skupin

**Schopnost produktu:** Prostřednictvím

Azure Active Directory (Azure AD) nyní poskytuje způsob, jak ověřovat dynamická pravidla skupiny. Na kartě **ověřit pravidla** můžete ověřit své dynamické pravidlo proti ukázkovým členům skupiny a potvrdit, že pravidlo funguje podle očekávání. Správci chtějí při vytváření nebo aktualizaci pravidel dynamických skupin zjistit, jestli uživatel nebo zařízení bude členem skupiny. Tato možnost pomáhá vyhodnotit, jestli uživatel nebo zařízení splňuje kritéria pravidla a pomáhá při řešení potíží, když členství neočekáváte.

Další informace najdete v tématu [ověření pravidla členství v dynamické skupině (Preview)](../enterprise-users/groups-dynamic-rule-validation.md).

---

### <a name="identity-secure-score---security-defaults-and-mfa-improvement-action-updates"></a>Skóre identity Secure – výchozí hodnoty zabezpečení a aktualizace pro akce zlepšování vícefaktorového ověřování

**Zadejte:** Změněná funkce

**Kategorie služby:** NENÍ K DISPOZICI

**Schopnost produktu:** Zabezpečení identity & ochrana

**Podpora výchozích hodnot zabezpečení pro akce zlepšování Azure AD:** Microsoft Secure skore bude aktualizovat akce vylepšení tak, aby podporovaly [výchozí nastavení zabezpečení ve službě Azure AD](./concept-fundamentals-security-defaults.md), což usnadňuje lepší ochranu vaší organizace s předem nakonfigurovaným nastavením zabezpečení pro běžné útoky. To bude mít vliv na následující akce vylepšení:

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

Tyto nové akce zlepšování vyžadují registraci uživatelů nebo správců pro službu Multi-Factor Authentication (MFA) v rámci vašeho adresáře a vytvoření správné sady zásad, které vyhovují potřebám vaší organizace. Hlavním cílem je mít flexibilitu při současném zajištění, že se všichni uživatelé a správci můžou ověřit s více faktory nebo s výzvou k ověření identity na základě rizika. To může mít formu, že má více zásad, které používají vymezená rozhodnutí nebo nastavovat výchozí hodnoty zabezpečení (od 16. března), které Microsoftu pomohou rozhodnout, kdy chtějí uživatele pro MFA požádat. [Přečtěte si další informace o tom, co je nového ve službě Microsoft Secure skore](/microsoft-365/security/mtp/microsoft-secure-score?view=o365-worldwide#whats-new).

---