---
title: Archivujte, co je nového v Azure Active Directory? | Dokumentace Microsoftu
description: Poznámky k verzi s novinkami v části Přehled této sady obsahu obsahují 6 měsíců aktivity. Po 6 měsících budou položky odebrány z hlavního článku a vloženy do tohoto článku archivu.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 3/31/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca7fda6345356568d512b396c412603cf7d837f7
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532396"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Archivujte, co je nového v Azure Active Directory?

Část [co je nového ve službě Azure Active Directory?](whats-new.md) v článku poznámky k verzi obsahuje aktualizace za posledních šest měsíců, i když tento článek obsahuje všechny starší informace.

Co je nového v Azure Active Directory? Poznámky k verzi obsahují informace o:

- Nejnovější verze
- Známé problémy
- Opravy chyb
- Zastaralé funkce
- Plánuje změny

---

## <a name="september-2020"></a>Září 2020

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---september-2020"></a>Nové zřizovací konektory v galerii aplikací Azure AD – září 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** integrace třetích stran
 
Nyní můžete automatizovat vytváření, aktualizaci a odstraňování uživatelských účtů pro tyto nově integrované aplikace:

- [CODA](../saas-apps/coda-provisioning-tutorial.md)
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

- Možnosti mapování atributů prostřednictvím Azure Portal

    Díky této funkci můžou správci IT mapovat atributy uživatelů, skupin nebo kontaktů ze služby AD na Azure AD pomocí různých typů mapování, které jsou dnes přítomné. Mapování atributů je funkce používaná ke standardizaci hodnot atributů, které se přenášejí ze služby Active Directory na Azure Active Directory. Jedna z nich může určit, jestli má být hodnota atributu přímo namapována z AD do služby Azure AD, nebo použít výrazy k transformaci hodnot atributů při zřizování uživatelů. [Další informace](../cloud-sync/how-to-attribute-mapping.md)

- Zřizování na vyžádání nebo testování uživatelského prostředí

    Po nastavení konfigurace můžete chtít ověřit, zda transformace uživatele pracuje podle očekávání, a teprve potom ji použít pro všechny uživatele v oboru. Díky zřizování na vyžádání můžou správci IT zadat rozlišující název (DN) uživatele AD a zjistit, jestli se nesynchronizují podle očekávání. Zřizování na vyžádání poskytuje skvělý způsob, jak zajistit, že mapování atributů, které jste dříve pracovali, podle očekávání. [Další informace](../cloud-sync/how-to-on-demand-provision.md)
 
---

### <a name="audited-bitlocker-recovery-in-azure-ad---public-preview"></a>Auditované obnovení BitLockeru v Azure AD – Public Preview

**Zadejte:** Nová funkce  
**Kategorie služby:** Správa přístupu k zařízením  
**Schopnost produktu:** Správa životního cyklu zařízení
 
Když správci IT nebo koncoví uživatelé čtou klíče pro obnovení nástroje BitLocker, ke kterým mají přístup, Azure Active Directory nyní vygeneruje protokol auditu, který zachycuje přístup k obnovovacímu klíči. Stejný audit poskytuje podrobnosti o zařízení, ke kterému byl klíč BitLocker přidružený.

Koncoví uživatelé mají [přístup ke svým klíčům pro obnovení prostřednictvím svého účtu](../user-help/my-account-portal-devices-page.md#view-a-bitlocker-key). Správci IT můžou získat přístup k klíčům pro obnovení prostřednictvím [rozhraní API pro obnovení pomocí obnovovacího klíče BitLockeru ve verzi beta](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta&preserve-view=true) nebo prostřednictvím portálu Azure AD. Další informace najdete v tématu [zobrazení nebo kopírování klíčů nástroje BitLocker na portálu Azure AD](../devices/device-management-azure-portal.md#view-or-copy-bitlocker-keys).

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
 
V minulosti byl vyžadován souhlas uživatele s připojováním k Privileged Identity Management (PIM) a postup připojování v okně PIM, které zahrnovalo registraci v Azure AD MFA. Díky nedávné integraci PIM v okně role a správci služby Azure AD odebíráme toto prostředí. Každý tenant s platnou licencí P2 se automaticky připojí k PIM.

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

Aktualizujeme portál pro zabezpečený skóre identit, který bude zarovnaný se změnami zavedenými v [nové verzi](/microsoft-365/security/mtp/microsoft-secure-score-whats-new)Microsoft Secure skore. 

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

[Backup365](https://portal.backup365.io/login), [SOAPBOX](https://app.soapboxhq.com/create?step=auth&provider=azure-ad2-oauth2), [Alma SIS](https://almau.getalma.com/), [konektor Enlyft Dynamics 365](http://enlyft.com/), [Serraview místo pro využívání softwarových řešení](../saas-apps/serraview-space-utilization-software-solutions-tutorial.md), [uniq](https://web.uniq.app/), [viditelně](../saas-apps/visibly-tutorial.md), [Zylo](../saas-apps/zylo-tutorial.md), [Edmentum – hodnocení přesná cesta](https://auth.edmentum.com/elf/login), [CyberLAB](https://cyberlab.evolvesecurity.com/#/welcome), [Altamira HRM](../saas-apps/altamira-hrm-tutorial.md), WireWheel, [ZIX dodržování předpisů a zachytávání](https://sminstall.zixcorp.com/teams/teams.php?install_request=true&tenant_id=common), [GreenLight podnikových ovládacích prvků](../saas-apps/greenlight-enterprise-business-controls-platform-tutorial.md) [,](https://goto.bpanda.com/login)Genetec, [volný prostor](https://www.clearance.network/) [,](../saas-apps/isams-tutorial.md) [VeraSMART, Amiko](../saas-apps/wirewheel-tutorial.md), [](https://nestiolistings.com/sso/oidc/azure/authorize/) [Twingate,](../saas-apps/verasmart-tutorial.md) [Scalefusion](https://amiko.web.rivero.app/) [,](../saas-apps/fortigate-ssl-vpn-tutorial.md) [Bpanda,](https://auth.twingate.com/signup) [](https://app.vivun.com/dashboard/calendar/connect)pro [koncové uživatele](https://www.wandera.com/) [, Vivun](https://scalefusion.com/users/sign_in/)

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
 
Dřív se vlastní Open ID Connect Provider mohl přidat nebo spravovat jenom prostřednictvím Azure Portal. Nyní mohou zákazníci Azure AD B2C přidávat a spravovat prostřednictvím rozhraní API beta verze Microsoft Graph. Informace o tom, jak nakonfigurovat tento prostředek pomocí rozhraní API, najdete v tématu [identityProvider Resource Type](/graph/api/resources/identityprovider?view=graph-rest-beta&preserve-view=true).
 
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
 
Dříve mohl [vlastnost Extension](/graph/api/application-post-extensionproperty?view=graph-rest-beta&tabs=http&preserve-view=true)spravovat jenom globální správce. Nyní tuto funkci povolujeme také pro Správce aplikací a správce cloudových aplikací.
 
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

Další informace o tokůch uživatelů najdete [v tématu verze toku uživatele v Azure Active Directory B2C](../../active-directory-b2c/user-flow-versions.md).

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
 

Vlastnosti externalUserState a externalUserStateChangedDateTime lze použít k vyhledání pozvaných hostů B2B, kteří ještě nepřijali své pozvánky, a také automatizaci sestavení, jako je odstranění uživatelů, kteří nepřijali své pozvánky po určitém počtu dnů. Tyto vlastnosti jsou teď dostupné v MS Graph v1. Pokyny k používání těchto vlastností najdete v tématu [typ prostředku uživatele](/graph/api/resources/user).
 
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

[Shopify plus](../saas-apps/shopify-plus-tutorial.md), [Ekarda](../saas-apps/ekarda-tutorial.md), [MailGates](../saas-apps/mailgates-tutorial.md), [BullseyeTDP](../saas-apps/bullseyetdp-tutorial.md), [raketa](../saas-apps/raketa-tutorial.md), [segment](../saas-apps/segment-tutorial.md), [AI auditor](https://www.mindbridge.ai/products/ai-auditor/), [Pobuca Connect](https://app.pobu.ca/), [proto.IO](../saas-apps/proto.io-tutorial.md), [](https://www.gatekeeperhq.com/)Webmarketo, [Plánovač centra](../saas-apps/hub-planner-tutorial.md), Ansira, možnosti [pro uvedení na trh](https://ansira.com/technology/channel-engagement), [IBM Digital Business Automation v cloudu](../saas-apps/ibm-digital-business-automation-on-cloud-tutorial.md), [Kisi fyzické zabezpečení](../saas-apps/kisi-physical-security-tutorial.md), [ViewpointOne](https://team.viewpoint.com/), [IntelligenceBank](../saas-apps/intelligencebank-tutorial.md), [pymetrics](../saas-apps/pymetrics-tutorial.md), [nula](https://www.teamzero.com/) [](../saas-apps/curricula-saml-tutorial.md), [instanice](https://instation.invillia.com/), [EDX for Business SAML 2,0 Integration](../saas-apps/edx-for-business-saml-integration-tutorial.md), mooc SmartKargo, PKIsigning [](../saas-apps/perforce-helix-core-tutorial.md) [, SiteIntel](../saas-apps/smartkargo-tutorial.md), [Perforce, Helix](../saas-apps/siteintel-tutorial.md) [, Cloud](../saas-apps/field-id-tutorial.md) [,](https://cloud.metacompliance.com/) [](https://mooc.office365-training.com/en/) [](https://platform.pkisigning.nl/) [Helix 365](https://smallstep.com/sso-ssh/)  

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
 
Nové delegované oprávnění EntitlementManagement. Read. All je teď k dispozici pro použití s rozhraním API pro správu oprávnění ve službě Microsoft Graph beta. Další informace o dostupných rozhraních API najdete v tématu [práce s rozhraním API pro správu opravňujících k Azure AD](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta&preserve-view=true).

---

### <a name="identity-protection-apis-available-in-v10"></a>Rozhraní API pro ochranu identit dostupné v 1.0

**Zadejte:** Nová funkce  
**Kategorie služby:** Ochrana identity  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
Rozhraní API pro Microsoft Graph riskyUsers a riskDetections jsou teď všeobecně dostupná. Teď, když jsou k dispozici na koncovém bodu v 1.0, vás pozvaní, abyste je mohli používat v produkčním prostředí. Další informace najdete v [dokumentaci Microsoft Graph](/graph/api/resources/identityprotectionroot).
 
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
 
Správci IT můžou začít používat novou roli Hybrid admin jako nejnižší privilegovanou roli pro nastavení Azure AD Connectho zřizování cloudu. U této nové role už nemusíte k instalaci a konfiguraci zřizování cloudu používat roli globálního správce. [Další informace](../roles/delegate-by-task.md#connect).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2020"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – květen 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
V květnu 2020 jsme do Galerie aplikací přidali následující 36 nové aplikace s podporou federace:

[Moula](https://moula.com.au/pay/merchants), [Surveypal](https://www.surveypal.com/app), [Kbot365](https://www.konverso.ai/virtual-assistant-digital-workplace/), [TackleBox](http://www.tacklebox.app/), [Powell Teams](https://powell-software.com/en/powell-teams-en/), [Talentsoft Assistant](https://msteams.talent-soft.com/), [přehledy](https://teams.asc-recording.app/product)o prostředcích ASC, [](../saas-apps/humanage-tutorial.md) [GO1](https://www.go1.com/), [B](https://b-engaged.se/), [COMPETELLA kontaktní centra](http://www.competella.com/) [, ASITE,](http://www.asite.com/) [ImageSoft identity](https://identity.imagesoftinc.com/), [My IBISWorld](https://identity.imagesoftinc.com/), [unsuite](../saas-apps/insuite-tutorial.md), [Change Process Management](../saas-apps/change-process-management-tutorial.md), [Cyara CX](../saas-apps/cyara-cx-assurance-platform-tutorial.md), [Smart Global vládnutí](../saas-apps/smart-global-governance-tutorial.md), [Prezi](../saas-apps/prezi-tutorial.md), [Mapbox](../saas-apps/mapbox-tutorial.md), [Datava Enterprise Service Platform](../saas-apps/datava-enterprise-service-platform-tutorial.md), [Whimsical](../saas-apps/whimsical-tutorial.md), [Trelica](../saas-apps/trelica-tutorial.md), [EasySSO pro Confluence](../saas-apps/easysso-for-confluence-tutorial.md), [EasySSO](../saas-apps/easysso-for-bitbucket-tutorial.md), Bitbucket [, EasySSO](../saas-apps/easysso-for-bamboo-tutorial.md), Bamboo [, Torii,](../saas-apps/torii-tutorial.md)Axiad, ColorTokens, ZTNA [](../saas-apps/colortokens-ztna-tutorial.md) [](../saas-apps/sharevault-tutorial.md), [Všichni domů CRM](../saas-apps/anyone-home-crm-tutorial.md) [, CCH,](../saas-apps/cch-tagetik-tutorial.md) [](../saas-apps/vyond-tutorial.md) [Contact Center](../saas-apps/ice-contact-center-tutorial.md) [](../saas-apps/axiad-cloud-tutorial.md) [](../saas-apps/textexpander-tutorial.md) [](../saas-apps/askspoke-tutorial.md)

Dokumentaci ke všem aplikacím si můžete také najít tady https://aka.ms/AppsTutorial .

Pokud chcete zobrazit seznam aplikací v galerii aplikací Azure AD, přečtěte si prosím podrobnosti tady https://aka.ms/AzureADAppRequest .

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>Režim jenom pro sestavy pro podmíněný přístup je teď všeobecně dostupný.

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity & ochrana

[Režim pouze sestav pro podmíněný přístup Azure AD](../conditional-access/concept-conditional-access-report-only.md) umožňuje vyhodnotit výsledek zásady bez vynucení řízení přístupu. V rámci vaší organizace můžete testovat zásady jenom pro sestavy a porozumět jejich dopadu, než je povolíte, aby bylo nasazení bezpečnější a jednodušší. Během posledních několika měsíců jsme se seznámili se silným přijetím režimu jenom pro sestavy – přes 26M uživatelé už jsou v oboru zásady jenom pro sestavy. V dnešní době se nové zásady podmíněného přístupu Azure AD vytvoří ve výchozím nastavení v režimu pouze sestavy. To znamená, že můžete sledovat dopad vašich zásad od okamžiku, kdy byly vytvořeny. A pro ty z vás, kteří používají rozhraní API MS graphu, můžete také [programově spravovat zásady jenom pro sestavy](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta&preserve-view=true) . 

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
* [Oracle Cloud Infrastructure Console](../saas-apps/oracle-cloud-infrastructure-console-provisioning-tutorial.md)

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

[Režim pouze sestav pro podmíněný přístup Azure AD](../conditional-access/concept-conditional-access-report-only.md) umožňuje vyhodnotit výsledek zásady bez vynucení řízení přístupu. V rámci vaší organizace můžete testovat zásady jenom pro sestavy a porozumět jejich dopadu, než je povolíte, aby bylo nasazení bezpečnější a jednodušší. Během posledních několika měsíců jsme se seznámili se silným přijetím režimu pouze pro sestavy s více než 26M uživateli, kteří už v oboru zásady jenom pro sestavy. V tomto oznámení se nové zásady podmíněného přístupu Azure AD vytvoří ve výchozím nastavení v režimu pouze sestavy. To znamená, že můžete sledovat dopad vašich zásad od okamžiku, kdy byly vytvořeny. A pro ty z vás, kteří používají rozhraní API MS graphu, můžete také [programově spravovat zásady jenom pro sestavy](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta&preserve-view=true). 

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

[SincroPool Apps](https://www.sincropool.com/), [SmartDB](https://hibiki.dreamarts.co.jp/smartdb/trial/), [float](../saas-apps/float-tutorial.md), [LMS365](https://lms.365.systems/), [iwt Suite Suite](../saas-apps/iwt-procurement-suite-tutorial.md), [Lunni](https://lunni.fi/), [EasySSO pro JIRA](../saas-apps/easysso-for-jira-tutorial.md), [Virtual Training Academy](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft), [Meraki Dashboard](../saas-apps/meraki-dashboard-tutorial.md), [Microsoft 365 stěhovací](https://app.mover.io/login), [mluvčí zapojení](https://speakerengage.com/login.php), [upřímně](../saas-apps/honestly-tutorial.md), [Ally](../saas-apps/ally-tutorial.md), [DutyFlow](https://app.dutyflow.nl/), [AlertMedia](../saas-apps/alertmedia-tutorial.md), [gr8 lidé](../saas-apps/gr8-people-tutorial.md), [Pendo](../saas-apps/pendo-tutorial.md), [HighGround](../saas-apps/highground-tutorial.md), [harmonie](../saas-apps/harmony-tutorial.md), Timetabling [, SynchroNet](../saas-apps/synchronet-click-tutorial.md), [](https://www.made-in-office.com/en/) [litmus, GroupTalk](../saas-apps/timetabling-solutions-tutorial.md) [, Frontify](../saas-apps/mongodb-cloud-tutorial.md) [, MongoDB,](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/) [TickitLMS](../saas-apps/nitro-productivity-suite-tutorial.md), [díky Coco, nitro](../saas-apps/litmus-tutorial.md) [, TMWS](https://recorder.grouptalk.com/) [,](../saas-apps/frontify-tutorial.md)( [](../saas-apps/tickitlms-learn-tutorial.md) [)](../saas-apps/trend-micro-tutorial.md) [](../saas-apps/fortes-change-cloud-tutorial.md)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>Podpora rozdílových dotazů Microsoft Graph pro oAuth2PermissionGrant k dispozici pro Public Preview

**Zadejte:** Nová funkce

**Kategorie služby:** MS Graph

**Schopnost produktu:** Vývojářské prostředí

Pro verzi Public Preview je k dispozici rozdílový dotaz pro oAuth2PermissionGrant. Nyní můžete sledovat změny bez nutnosti průběžného dotazování Microsoft Graph. [Další informace](/graph/api/oAuth2PermissionGrant-delta?tabs=http&view=graph-rest-beta&preserve-view=true)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>Obecně dostupná podpora Microsoft Graphch rozdílových dotazů pro kontakt organizace

**Zadejte:** Nová funkce

**Kategorie služby:** MS Graph

**Schopnost produktu:** Vývojářské prostředí

Rozdílový dotaz pro kontakty organizace je všeobecně k dispozici! Nyní můžete sledovat změny v produkčních aplikacích bez nutnosti průběžného dotazování Microsoft Graph. Nahraďte veškerý stávající kód, který se průběžně dotazuje orgContact data na rozdílový dotaz, aby významně zvýšil výkon. [Další informace](/graph/api/orgcontact-delta?tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>Podpora rozdílových dotazů Microsoft Graph pro aplikaci všeobecně dostupná

**Zadejte:** Nová funkce

**Kategorie služby:** MS Graph

**Schopnost produktu:** Vývojářské prostředí

Rozdílový dotaz pro aplikace je všeobecně k dispozici! Nyní můžete sledovat změny v produkčních aplikacích bez nutnosti průběžného dotazování Microsoft Graph. Nahraďte veškerý stávající kód, který nepřetržitě dotazuje data aplikací podle rozdílového dotazu a významně tak vylepšit výkon. [Další informace](/graph/api/application-delta)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>Podpora rozdílových dotazů Microsoft Graph pro jednotky pro správu, které jsou k dispozici pro Public Preview

**Zadejte:** Nová funkce

**Kategorie služby:** MS Graph

**Schopnost produktu:** Pro veřejné verze Preview je k dispozici rozdílový dotaz vývojářského prostředí pro jednotky pro správu. Nyní můžete sledovat změny bez nutnosti průběžného dotazování Microsoft Graph. [Další informace](/graph/api/administrativeunit-delta?tabs=http&view=graph-rest-beta&preserve-view=true)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>Spravovat telefonní čísla pro ověřování a další informace v nových Microsoft Graphch rozhraní API beta verze

**Zadejte:** Nová funkce

**Kategorie služby:** MS Graph

**Schopnost produktu:** Vývojářské prostředí

Tato rozhraní API jsou klíčovým nástrojem pro správu metod ověřování vašich uživatelů. Nyní můžete programově předregistrovat a spravovat ověřovatele používané pro MFA a Samoobslužné resetování hesla (SSPR). To je jedna z nejžádanějších funkcí v rámci Azure AD MFA, SSPR a Microsoft Graphch prostorů. Nová rozhraní API, která jsme vydali v tomto Wave, vám poskytnou možnost:

- Čtení, přidávání, aktualizace a odebírání telefonických ověřování uživatele
- Resetování hesla uživatele
- Zapnutí a vypnutí služby SMS – přihlášení

Další informace najdete v tématu [Přehled rozhraní API metody ověřování Azure AD](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true).

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

Další informace najdete v tématu [Správa jednotek pro správu v Azure Active Directory (Preview)](../roles/administrative-units.md).

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

Moji zaměstnanci umožňují správcům Firstline, jako je Správce úložiště, zajistit, aby jejich zaměstnanci měli přístup k účtům Azure AD. Namísto spoléhání na centrální Helpdesk můžou organizace delegovat běžné úlohy, jako je resetování hesel nebo změna telefonních čísel, na Firstline Manager. Uživatel, který nemá přístup ke svému účtu, může pomocí mých zaměstnanců znovu získat přístup jenom v několika kliknutích, aniž by to vyžadovalo Helpdesk nebo pracovníky IT. Další informace najdete v tématu [Správa uživatelů pomocí možnosti Moji zaměstnanci (Preview)](../roles/my-staff-configure.md) a [delegování správy uživatelů pomocí mých zaměstnanců (Preview)](../user-help/my-staff-team-manager.md).

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

Tyto nové akce zlepšování vyžadují registraci uživatelů nebo správců pro službu Multi-Factor Authentication (MFA) v rámci vašeho adresáře a vytvoření správné sady zásad, které vyhovují potřebám vaší organizace. Hlavním cílem je mít flexibilitu při současném zajištění, že se všichni uživatelé a správci můžou ověřit s více faktory nebo s výzvou k ověření identity na základě rizika. To může mít formu, že má více zásad, které používají vymezená rozhodnutí nebo nastavovat výchozí hodnoty zabezpečení (od 16. března), které Microsoftu pomohou rozhodnout, kdy chtějí uživatele pro MFA požádat. [Přečtěte si další informace o tom, co je nového ve službě Microsoft Secure skore](/microsoft-365/security/mtp/microsoft-secure-score#whats-new).

---

## <a name="march-2020"></a>Březen 2020

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march--2021"></a>Nespravované účty Azure Active Directory v aktualizaci B2B pro březen 2021

**Zadejte:** Plánování změn  
**Kategorie služby:** B2B  
**Schopnost produktu:** B2B/B2C
 
**Od 31. března 2021** přestane společnost Microsoft podporovat uplatnění pozvánky tím, že pro scénáře spolupráce B2B vytvoří neAzure Active Directory spravované účty a klienty služby Azure AD. V přípravné době doporučujeme, abyste se rozhodli, že se chcete přihlásit k [e-mailu ověřování heslem jednorázového hesla](../external-identities/one-time-passcode.md).

---

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>Uživatelé s výchozí rolí přístupu budou v oboru pro zřizování.

**Zadejte:** Plánování změn  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** Správa životního cyklu identit
 
V minulosti byly uživatelé s výchozí rolí přístupu mimo obor pro zřizování. Slyšeli jsme, že zákazníci chtějí mít tuto roli v oboru pro zřizování. Pracujeme na nasazení změny, takže všechny nové konfigurace zřizování umožní zřídit uživatele s výchozí rolí přístupu. Postupně změníme chování stávajících zřizovacích konfigurací tak, aby podporovaly zřizování uživatelů s touto rolí. Není vyžadována žádná akce zákazníka. Až bude tato změna provedena, budeme po aktualizaci publikovat do naší [dokumentace](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md) .

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Spolupráce Azure AD B2B bude k dispozici v Microsoft Azure provozována v klientech 21Vianet (Azure Čína 21Vianet).

**Zadejte:** Plánování změn  
**Kategorie služby:** B2B  
**Schopnost produktu:** B2B/B2C
 
Možnosti spolupráce Azure AD B2B budou dostupné v Microsoft Azure provozovaných v klientech 21Vianet (Azure Čína 21Vianet) a umožní uživatelům v tenantovi Azure Čína 21Vianet spolupracovat bez problémů s uživateli v jiných klientech Azure Čína 21Vianet. [Přečtěte si další informace o spolupráci Azure AD B2B](/azure/active-directory/b2b/).

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Předesignování e-mailu pozvánky pro spolupráci Azure AD B2B

**Zadejte:** Plánování změn  
**Kategorie služby:** B2B  
**Schopnost produktu:** B2B/B2C
 
[E-maily](../external-identities/invitation-email-elements.md) odeslané službou pozvánky pro spolupráci Azure AD B2B pro pozvání uživatelů do tohoto adresáře budou přepracovány, aby se informace o pozvánce a další kroky uživatele vymažou.

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>Změny zásad HomeRealmDiscovery se zobrazí v protokolech auditu.

**Zadejte:** Určí  
**Kategorie služby:** Ověřen  
**Schopnost produktu:** Monitorování & vytváření sestav
 
Opravili jsme chybu, kdy se změny [zásady HomeRealmDiscovery](../manage-apps/configure-authentication-for-federated-users-portal.md) nezahrnuly do protokolů auditu. Teď budete moct zobrazit, kdy a jak se zásady změnily a kým. 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Nové federované aplikace dostupné v Aplikace Azure AD galerii – březen 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
V březnu 2020 jsme do Galerie aplikací přidali tyto 51 nové aplikace s podporou federace: 

[Cisco AnyConnect](../saas-apps/cisco-anyconnect.md), [Zoho One Čína](../saas-apps/zoho-one-china-tutorial.md), [PlusPlus](https://test.plusplus.app/auth/login/azuread-outlook/), [Profit.co aplikace SAML](../saas-apps/profitco-saml-app-tutorial.md) [IPoint poskytovatel služeb](../saas-apps/ipoint-service-provider-tutorial.md), [contexxt.AI koule](https://contexxt-sphere.com/login) [](../saas-apps/skills-workflow-tutorial.md), [vyhodnocení informací získaných by Invictus](../saas-apps/wisdom-by-invictus-tutorial.md), [odlesk digitálního podepisování](https://spark-dev.pixelnebula.com/login), [LOGZ.IO-Cloud pozorovatelé pro inženýry](../saas-apps/logzio-cloud-observability-for-engineers-tutorial.md), [spektrum](../saas-apps/spectrumu-tutorial.md), BizzContact [,](https://bizzcontact.app/) [Elqano SSO](../saas-apps/elqano-sso-tutorial.md) [, MarketSignShare](http://www.signshare.com/), [CrossKnowledge Learning Suite](../saas-apps/crossknowledge-learning-suite-tutorial.md), [Netvision COMPAS](../saas-apps/netvision-compas-tutorial.md), [FCM hub](../saas-apps/fcm-hub-tutorial.md) [,](../saas-apps/lift-tutorial.md) [žebr a/S Byggeweb Mobile,](https://apps.apple.com/us/app/docia/id529058757)GoLinks [,](../saas-apps/golinks-tutorial.md)služby Datadog [,](../saas-apps/datadog-tutorial.md)Zscaler [User Portal,](../saas-apps/zscaler-b2b-user-portal-tutorial.md)Planview Enterprise One [](https://demo.asterapp.io/login) [platforma IP](../saas-apps/ip-platform-tutorial.md), [](../saas-apps/planview-enterprise-one-tutorial.md) [Invision](../saas-apps/invision-tutorial.md), [](https://www.devfinition.com/) [pipedrivu](../saas-apps/pipedrive-tutorial.md), [prezentující Workshop](https://app.showcaseworkshop.com/), [platforma GreenLight Integration Platform](../saas-apps/greenlight-integration-platform-tutorial.md), [Správa přístupu kompatibilní s GreenLight](../saas-apps/greenlight-compliant-access-management-tutorial.md), [vzdělávání grok](../saas-apps/grok-learning-tutorial.md), [Miradore online](https://login.online.miradore.com/), Khoros [péče](../saas-apps/khoros-care-tutorial.md) [](../saas-apps/code42-tutorial.md) , AskYourTeam, [TruNarrative](../saas-apps/trunarrative-tutorial.md) [, Smartwaiver, bizagi](https://www.smartwaiver.com/m/user/sw_login.php?wms_login) [Studio pro automatizaci digitálních procesů](../saas-apps/bizagi-studio-for-digital-process-automation-tutorial.md), [insuiteX, Sybo](../saas-apps/askyourteam-tutorial.md) [, Britive](https://www.systexsoftware.com.tw/) [,](../saas-apps/britive-tutorial.md), [,](../saas-apps/e-days-tutorial.md) [WhosOffice,](../saas-apps/whosoffice-tutorial.md) [SDN](https://portal.kollective.app/login) [, Kollective,](https://app.witivio.com/) [Witivio](https://my.playvox.com/login), [Korn trajekt 360](../saas-apps/korn-ferry-360-tutorial.md), [areál kavárny](../saas-apps/campus-cafe-tutorial.md) [,](../saas-apps/catchpoint-tutorial.md) [PlayVox, záchytný bod](https://www.insuite.jp/) [](https://admin.nodeinsight.com/AADLogin.aspx)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Spolupráce Azure AD B2B dostupná v Azure Government tenantů

**Zadejte:** Nová funkce  
**Kategorie služby:** B2B  
**Schopnost produktu:** B2B/B2C
 
Funkce spolupráce Azure AD B2B jsou teď dostupné mezi některými Azure Government klienty.  Pokud chcete zjistit, jestli je váš tenant schopný používat tyto možnosti, postupujte podle pokynů v tématu [Jak poznám, jestli je spolupráce B2B dostupná ve svém Tenantovi Azure USA?](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant).

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>Azure Monitor Integration pro protokoly Azure je teď k dispozici v Azure Government

**Zadejte:** Nová funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav
 
Azure Monitor integrace s protokoly služby Azure AD je teď k dispozici v Azure Government. Protokoly služby Azure AD (protokoly auditu a přihlašování) můžete směrovat do účtu úložiště, centra událostí a Log Analytics. Projděte si [podrobnou dokumentaci](../reports-monitoring/concept-activity-logs-azure-monitor.md) a [plány nasazení pro vytváření sestav a monitorování](../reports-monitoring/plan-monitoring-and-reporting.md) scénářů Azure AD.

---

### <a name="identity-protection-refresh-in-azure-government"></a>Aktualizace Identity Protection v Azure Government

**Zadejte:** Nová funkce  
**Kategorie služby:** Ochrana identity  
**Schopnost produktu:** Zabezpečení identity & ochrana

S radostí sdílíme, že teď jsme na Microsoft Azure Government portálu navedli aktualizované [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md)   prostředí [](https://portal.azure.us/). Další informace najdete v příspěvku na [blogu o oznámení](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667).

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>Zotavení po havárii: Stáhněte si konfiguraci zřizování a uložte ji.

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** Správa životního cyklu identit
 
Služba zřizování Azure AD poskytuje bohatou sadu možností konfigurace. Zákazníci musí mít možnost uložit svou konfiguraci, aby na ně mohli později odkazovat nebo se vrátit na známou správnou verzi. Přidali jsme možnost stáhnout si vaši konfiguraci zřizování jako soubor JSON a nahrát ho, až ho budete potřebovat. [Další informace](../app-provisioning/export-import-provisioning-configuration.md).

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR (Samoobslužné resetování hesla) teď vyžaduje dvě brány pro správce v Microsoft Azure provozované společností 21Vianet (Azure Čína 21Vianet). 

**Zadejte:** Změněná funkce  
**Kategorie služby:** resetování hesla Self-Service  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
Správci, kteří používají Samoobslužné resetování hesla (SSPR), dříve v Microsoft Azure pracovali pomocí samoobslužného resetování hesla () k resetování jejich identity, potřebují jenom jednu bránu (Challenge). Ve veřejných a dalších národních cloudech musí správci při použití SSPR vykazovat svoji identitu pomocí dvou bran. Protože ale nepodporujeme SMS nebo telefonní hovory v Azure Čína 21Vianet, povolili jsme správcům resetování hesla s jednou branou.

Vytváříme paritu funkcí SSPR mezi Azure Čína 21Vianet a veřejným cloudem. Při použití SSPR musí správci používat dvě brány. Podporuje se SMS, telefonní hovory a ověřovací oznámení a kódy aplikace ověřovatele. [Další informace](../authentication/concept-sspr-policy.md#administrator-reset-policy-differences).

---

### <a name="password-length-is-limited-to-256-characters"></a>Délka hesla je omezená na 256 znaků.

**Zadejte:** Změněná funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů
 
Aby byla zajištěna spolehlivost služby Azure AD, uživatelská hesla jsou nyní omezena na délku až 256 znaků. Uživatelům s hesly delšími než se zobrazí výzva ke změně hesla při následném přihlášení, a to buď kontaktováním správce, nebo pomocí funkce samoobslužného resetování hesla.

Tato změna byla povolena v březnu nějak změnily 13, 2020, 10AM PST (18:00 UTC) a chyba je AADSTS 50052, InvalidPasswordExceedsMaxLength. Další podrobnosti najdete v [oznámení o změně](../develop/reference-breaking-changes.md#user-passwords-will-be-restricted-to-256-characters) .

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Protokoly pro přihlášení k Azure AD jsou teď dostupné pro všechny bezplatné klienty prostřednictvím Azure Portal

**Zadejte:** Změněná funkce  
**Kategorie služby:** Zpravodajský  
**Schopnost produktu:** Monitorování & vytváření sestav
 
Od tohoto okamžiku můžou zákazníci, kteří mají bezplatné klienty, získat přístup k [protokolům přihlášení Azure AD z Azure Portal](../reports-monitoring/concept-sign-ins.md) po dobu až 7 dnů. Dříve byly protokoly přihlášení k dispozici pouze pro zákazníky s licencemi Azure Active Directory Premium. Díky této změně mají všichni klienti k těmto protokolům přístup prostřednictvím portálu.

> [!NOTE]
> Zákazníci ještě potřebují licenci Premium (Azure Active Directory Premium P1 nebo P2) pro přístup k protokolům přihlášení prostřednictvím rozhraní API Microsoft Graph a Azure Monitor.

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>Vyřazení možnosti skupiny v úrovni adresáře ze skupin Obecné nastavení v Azure Portal

**Zadejte:** Zastaralé  
**Kategorie služby:** Správa skupin  
**Schopnost produktu:** Prostřednictvím

Abychom zákazníkům poskytli pružnější způsob vytváření skupin pro všechny adresáře, které nejlépe vyhovují jejich potřebám, nahradili jsme možnost **skupiny** pro všechny adresáře ze   >  **obecných** nastavení skupiny v Azure Portal s odkazem na [dokumentaci k dynamické skupině](../enterprise-users/groups-dynamic-membership.md). Vylepšili jsme naši dokumentaci, aby zahrnovala další pokyny, aby správci mohli vytvářet skupiny všech uživatelů, které zahrnují nebo vylučují uživatele typu Host.

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

Tyto nové akce zlepšování budou vyžadovat registraci uživatelů nebo správců pro MFA napříč vaším adresářem a vytvoření správné sady zásad, které vyhovují potřebám vaší organizace. Hlavním cílem je mít flexibilitu při současném zajištění, že se všichni uživatelé a správci můžou ověřit s více faktory nebo s výzvou k ověření identity na základě rizika. To může mít formu nastavení výchozích hodnot zabezpečení, které Microsoftu umožní rozhodnout, kdy mají požádat uživatele o MFA, nebo jestli mají více zásad, které použijí rozhodnutí s vymezeným oborem. V rámci těchto aktualizací akcí vylepšení již nebudou zásady ochrany směrných plánů zahrnuty do výpočtů bodování. [Přečtěte si další informace o tom, co připravujeme v zabezpečeném skóre Microsoftu](/microsoft-365/security/mtp/microsoft-secure-score-whats-coming).

---

### <a name="azure-ad-domain-services-sku-selection"></a>Výběr skladové položky Azure AD Domain Services

**Zadejte:** Nová funkce  
**Kategorie služby:** Azure AD Domain Services  
**Schopnost produktu:** Azure AD Domain Services
 
Slyšeli jsme zpětnou vazbu, že Azure AD Domain Services zákazníci chtějí větší flexibilitu při výběru úrovní výkonu pro své instance. Od 1. února 2020 jsme přešli z dynamického modelu (kde Azure AD určuje výkon a cenovou úroveň na základě počtu objektů) k modelu automatického výběru. Zákazníci si teď můžou vybrat úroveň výkonu, která odpovídá jejich prostředí. Tato změna nám taky umožňuje povolit nové scénáře, jako jsou doménové struktury prostředků, a prémiové funkce jako denní zálohy. Počet objektů je nyní neomezený pro všechny skladové položky, ale budeme pro každou úroveň dál nabízet návrhy počtu objektů.

**Není vyžadována žádná okamžitá akce zákazníka.** Pro stávající zákazníky je dynamická vrstva, která byla používána od 1. února 2020, určena pro novou výchozí úroveň. Výsledek této změny nemá žádný vliv na ceny nebo výkon. Až dál, zákazníci Azure služba AD DS budou muset vyhodnotit požadavky na výkon při změně velikosti adresáře a vlastností zatížení. Přepínání mezi úrovněmi služeb bude i nadále operací bez výpadků. zákazníci už nebudou automaticky přesouvat na nové úrovně na základě nárůstu jejich adresáře. Navíc se nebudou zvyšovat žádné ceny a nové ceny budou zarovnány s aktuálním modelem fakturace. Další informace najdete v dokumentaci ke [službě Azure služba AD DS SKU](../../active-directory-domain-services/administration-concepts.md#azure-ad-ds-skus) a na [stránce s cenami Azure AD Domain Services](https://azure.microsoft.com/pricing/details/active-directory-ds/).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Nové federované aplikace dostupné v Aplikace Azure AD galerii – únor 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
V únoru 2020 jsme do Galerie aplikací přidali tyto 31 nových aplikací s podporou federace: 

[IamIP patentová platforma](../saas-apps/iamip-patent-platform-tutorial.md), [Experience Cloud](../saas-apps/experience-cloud-tutorial.md), [ns1 jednotného přihlašování pro Azure](../saas-apps/ns1-sso-azure-tutorial.md), [Barracuda e-mailové služby](https://ess.barracudanetworks.com/sso/azure), [vytváření sestav ABA](https://myaba.co.uk/client-access/signin/auth/msad), [v případě řešení krizí – online portál](../saas-apps/in-case-of-crisis-online-portal-tutorial.md), [BIC pro Cloud](../saas-apps/bic-cloud-design-tutorial.md), [Včelařský datový konektor Azure AD](../saas-apps/beekeeper-azure-ad-data-connector-tutorial.md), [vyhodnocování trajektů](https://www.kornferry.com/solutions/kf-digital/kf-assess), [Verkada příkaz](../saas-apps/verkada-command-tutorial.md), [Splashtop](../saas-apps/splashtop-tutorial.md), [Syxsense](../saas-apps/syxsense-tutorial.md), [EAB Navigate](../saas-apps/eab-navigate-tutorial.md), [New Relic (omezená verze)](../saas-apps/new-relic-limited-release-tutorial.md), [Thulium](https://admin.thulium.com/login/instance), [správce lístků](../saas-apps/ticketmanager-tutorial.md), [Výběr šablony pro týmy](https://links.officeatwork.com/templatechooser-download-teams), [včely](https://www.beesy.me/index.php/site/login), [systém podpory Health](../saas-apps/health-support-system-tutorial.md), [MURAL](https://app.mural.co/signup), [podregistr](../saas-apps/hive-tutorial.md), [LavaDo](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview), [Wakelet](https://wakelet.com/login), [Firmex VDR](../saas-apps/firmex-vdr-tutorial.md), [ThingLink pro učitele a školy](https://www.thinglink.com/), [Coda](../saas-apps/coda-tutorial.md), [NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product), [WEDO](../saas-apps/wedo-tutorial.md), [InvitePeople](https://invitepeople.com/login), [REPRINTER – článek Galaxy](../saas-apps/reprints-desk-article-galaxy-tutorial.md), [TeamViewer](../saas-apps/teamviewer-tutorial.md)

 
Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Nové zřizovací konektory v galerii aplikací Azure AD – únor 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
Nyní můžete automatizovat vytváření, aktualizaci a odstraňování uživatelských účtů pro tyto nově integrované aplikace:

- [Mixpanel](../saas-apps/mixpanel-provisioning-tutorial.md)
- [TeamViewer](../saas-apps/teamviewer-provisioning-tutorial.md)
- [Azure Databricks](/azure/databricks/administration-guide/users-groups/scim/aad)
- [PureCloud by Genesys](../saas-apps/purecloud-by-genesys-provisioning-tutorial.md)
- [Zapier](../saas-apps/zapier-provisioning-tutorial.md)

Další informace o tom, jak lépe zabezpečit vaši organizaci pomocí automatizovaného zřizování uživatelských účtů, najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](../app-provisioning/user-provisioning.md).

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>Podpora klíčů zabezpečení FIDO2 v hybridních prostředích pro Azure AD

**Zadejte:** Nová funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů
 
Oznamujeme vydání verze Public Preview podpory Azure AD pro klíče zabezpečení FIDO2 v hybridních prostředích. Uživatelé teď můžou použít bezpečnostní klíče FIDO2 k přihlášení ke svým hybridním zařízením s Windows 10 připojeným k Azure AD a získat bezproblémové přihlašování k místním a cloudovým prostředkům. Podpora hybridních prostředí byla od našich zákazníků s nehesly nejdůležitější, protože zpočátku jsme na zařízeních připojených k Azure AD spustili verzi Public Preview pro FIDO2 support. Ověřování bez hesla pomocí pokročilých technologií, jako jsou biometrika a kryptografie veřejného a privátního klíče, poskytují pohodlí a snadné použití při zabezpečení. V této veřejné verzi Preview teď můžete pro přístup k tradičním prostředkům služby Active Directory použít moderní ověřování, jako jsou bezpečnostní klíče FIDO2. Další informace najdete [v jednotného přihlašování k místním prostředkům](../authentication/howto-authentication-passwordless-security-key-on-premises.md). 

Informace o tom, jak začít, najdete v tématu [Povolení klíčů zabezpečení FIDO2 pro vašeho tenanta](../authentication/howto-authentication-passwordless-security-key.md) , kde najdete podrobné pokyny. 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>Nové prostředí můj účet je teď všeobecně dostupné.

**Zadejte:** Změněná funkce  
**Kategorie služby:** Můj profil/účet  
**Schopnost produktu:** Prostředí koncového uživatele
 
Tento účet je teď všeobecně k dispozici pro všechny potřeby správy účtů koncových uživatelů. Koncoví uživatelé mají přístup k tomuto novému webu přes adresu URL nebo v hlavičce nového prostředí moje aplikace. Přečtěte si další informace o všech funkcích samoobslužné služby, které nabízí nové možnosti na [portálu Můj účet](../user-help/my-account-portal-overview.md).

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>Adresa URL webu mého účtu se aktualizuje na myaccount.microsoft.com

**Zadejte:** Změněná funkce  
**Kategorie služby:** Můj profil/účet  
**Schopnost produktu:** Prostředí koncového uživatele
 
Nové prostředí koncového uživatele mého účtu aktualizuje svou adresu URL na `https://myaccount.microsoft.com` příští měsíc. Další informace o zkušenostech a všech samoobslužných funkcích pro účty, které nabízí koncovým uživatelům, najdete v [nápovědě k portálu Můj účet](../user-help/my-account-portal-overview.md).

---

## <a name="january-2020"></a>Leden 2020
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>Nový portál moje aplikace je teď všeobecně dostupný.

**Zadejte:** Plánování změn  
**Kategorie služby:** Moje aplikace  
**Schopnost produktu:** Prostředí koncového uživatele
 
Upgradujte svoji organizaci na nový portál moje aplikace, který je teď všeobecně dostupný. Další informace o novém portálu a kolekcích najdete v části [Vytvoření kolekcí na portálu moje aplikace](../manage-apps/access-panel-collections.md).

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Pracovní prostory ve službě Azure AD se přejmenovaly na kolekce.

**Zadejte:** Změněná funkce  
**Kategorie služby:** Moje aplikace   
**Schopnost produktu:** Prostředí koncového uživatele
 
Pracovní prostory můžou správci filtrů nakonfigurovat k uspořádání aplikací uživatelů, budou se teď označovat jako kolekce. Přečtěte si další informace o tom, jak je nakonfigurovat při [vytváření kolekcí na portálu moje aplikace](../manage-apps/access-panel-collections.md).

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Azure AD B2C registrace a přihlašování pomocí vlastní zásady (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C
 
Když se telefonní číslo a přihlášení k telefonnímu číslu, vývojáři a podniky můžou svým zákazníkům dovolit zaregistrovat se a přihlásit se pomocí jednorázového hesla, které se pošle na telefonní číslo uživatele přes SMS. Tato funkce také umožní zákazníkovi změnit své telefonní číslo, pokud ztratí přístup k telefonu. S výkonem vlastních zásad a přihlášením a přihlášením pomocí telefonu umožňuje vývojářům a podnikům komunikovat svoji značku prostřednictvím přizpůsobení stránky. Přečtěte si, jak [nastavit registraci a přihlašování telefonem pomocí vlastních zásad v Azure AD B2C](../../active-directory-b2c/phone-authentication-user-flows.md).
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Nové zřizovací konektory v galerii aplikací Azure AD – leden 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
Nyní můžete automatizovat vytváření, aktualizaci a odstraňování uživatelských účtů pro tyto nově integrované aplikace:

- [Promapp]( ../saas-apps/promapp-provisioning-tutorial.md)
- [Zscaler Private Access](../saas-apps/zscaler-private-access-provisioning-tutorial.md)

Další informace o tom, jak lépe zabezpečit vaši organizaci pomocí automatizovaného zřizování uživatelských účtů, najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](../app-provisioning/user-provisioning.md).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Nové federované aplikace dostupné v Aplikace Azure AD galerii – leden 2020

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran
 
Do ledna 2020 jsme do Galerie aplikací přidali tyto 33 nové aplikace s podporou federace: 

[JOSA](../saas-apps/josa-tutorial.md), [Fast Edge Cloud](../saas-apps/fastly-edge-cloud-tutorial.md), [terraformu Enterprise](../saas-apps/terraform-enterprise-tutorial.md), [Spintr SSO](../saas-apps/spintr-sso-tutorial.md), [Abibot Netlogistik](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik), [SkyKick](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access), pro, LeaveBot, [](https://appsource.microsoft.com/en-us/product/office/WA200001175) [DataCamp](../saas-apps/datacamp-tutorial.md), [TripActions](../saas-apps/tripactions-tutorial.md), [SMARTWORK](https://www.intumit.com/teams-smartwork/), [DOTCOM-monitor](../saas-apps/dotcom-monitor-tutorial.md), [SSOGEN – Azure AD SSO Gateway pro Oracle E-Business Suite-EBS, PeopleSoft spouštěných místně a jde](../saas-apps/ssogen-tutorial.md), [hostované MyCirqa SSO](../saas-apps/hosted-mycirqa-sso-tutorial.md), [platforma pro správu vlastností yuhu](../saas-apps/yuhu-property-management-platform-tutorial.md), [LumApps](https://sites.lumapps.com/login), [Working Enterprise](../saas-apps/upwork-enterprise-tutorial.md), [Talentsoft](../saas-apps/talentsoft-tutorial.md), [SmartDB pro Microsoft Teams](http://teams.smartdb.jp/login/), [PressPage](../saas-apps/presspage-tutorial.md), [ContractSafe typu Saml2 SSO](../saas-apps/contractsafe-saml2-sso-tutorial.md), [Maxient software Manager](../saas-apps/maxient-conduct-manager-software-tutorial.md), [helpshift](../saas-apps/helpshift-tutorial.md), [PortalTalk 365](https://www.portaltalk.com/), [koview](https://portal.coreview.com/), squelch Cloud Office 365 Connector, PingFlow [Authentication](https://app-staging.pingview.io/), PrinterLogic [SaaS](../saas-apps/printerlogic-saas-tutorial.md), [Taskize Connect](../saas-apps/taskize-connect-tutorial.md), Sandwai [,](https://app.sandwai.com/) [EZRentOut](../saas-apps/ezrentout-tutorial.md), [AssetSonar](../saas-apps/assetsonar-tutorial.md), [Akari Virtual Assistant](https://akari.io/akari-virtual-assistant/) [](../saas-apps/upshotly-tutorial.md)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="two-new-identity-protection-detections"></a>Dvě nové detekce ochrany identity

**Zadejte:** Nová funkce  
**Kategorie služby:** Ochrana identity  
**Schopnost produktu:** Zabezpečení identity & ochrana
 
Přidali jsme dva nové typy detekce propojených s přihlašováním do ochrany identity: podezřelá pravidla pro manipulaci s doručenou poštou a možná cestování. Tyto detekce v režimu offline jsou zjišťovány Microsoft Cloud App Security (MCAS) a mají vliv na uživatele a přihlašování v rámci Identity Protection. Další informace o těchto detekcích najdete v našem [typu rizika přihlašování](../identity-protection/concept-identity-protection-risks.md#sign-in-risk).
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>Zásadní změna: fragmenty identifikátoru URI nebudou přeneseny prostřednictvím přesměrování přihlášení.

**Zadejte:** Změněná funkce  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů
 
Od 8. února 2020 se při odeslání požadavku na login.microsoftonline.com pro přihlášení uživatele služba připojí k žádosti prázdný fragment.  Tím se zabrání třída útoků přesměrování tím, že zajistí, že prohlížeč vymaže všechny existující fragmenty v žádosti. Žádná aplikace by neměla mít závislost na tomto chování. Další informace najdete v tématu [přerušující změny](../develop/reference-breaking-changes.md#february-2020) v dokumentaci k platformě Microsoft Identity Platform.

---

## <a name="december-2019"></a>Prosinec 2019

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>Integrace SAP SuccessFactors zřizování do Azure AD a místní služby AD (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Zřizování aplikací  
**Schopnost produktu:** Správa životního cyklu identit

V Azure AD teď můžete integrovat SAP SuccessFactors jako autoritativní zdroj identity. Tato integrace vám pomůže automatizovat ucelený životní cyklus identity, včetně použití událostí založených na standardech HR, jako jsou nová přijetí nebo ukončení, pro řízení zřizování účtů Azure AD.

Další informace o tom, jak nastavit příchozí zřizování SAP SuccessFactors do služby Azure AD, najdete v kurzu [konfigurace SAP SuccessFactors pro Automatické zřizování](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md) .

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Podpora přizpůsobených e-mailů v Azure AD B2C (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** B2C – Správa identit uživatelů  
**Schopnost produktu:** B2B/B2C

Teď můžete pomocí Azure AD B2C vytvářet přizpůsobené e-maily, když se uživatelé přihlásí k používání svých aplikací. Pomocí DisplayControls (aktuálně ve verzi Preview) a poskytovatele e-mailu jiného výrobce (například, [SendGrid](https://sendgrid.com/), [SparkPost](https://sparkpost.com/)nebo vlastní REST API) můžete použít vlastní e-mailovou šablonu **, adresu a** text předmětu a také lokalizaci a vlastní nastavení jednorázového hesla.

Další informace najdete v tématu [ověření vlastního e-mailu v Azure Active Directory B2C](../../active-directory-b2c/custom-email-sendgrid.md).

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>Nahrazení standardních zásad výchozími hodnotami zabezpečení

**Zadejte:** Změněná funkce  
**Kategorie služby:** Jiná  
**Schopnost produktu:** Zabezpečení identity a ochrana

Jako součást zabezpečeného modelu pro ověřování odebíráme stávající zásady ochrany základní úrovně ze všech tenantů. Na toto odstranění cílí na dokončení února. Náhrada za tyto zásady ochrany základní úrovně je výchozí nastavení zabezpečení. Pokud používáte zásady základní ochrany, musíte se přesunout na nové zásady výchozího nastavení zabezpečení nebo na podmíněný přístup. Pokud jste tyto zásady nepoužili, nemusíte provádět žádnou akci.

Další informace o novém výchozím nastavení zabezpečení najdete v tématu [co jsou výchozí hodnoty zabezpečení?](./concept-fundamentals-security-defaults.md) Další informace o zásadách podmíněného přístupu najdete v tématu [běžné zásady podmíněného přístupu](../conditional-access/concept-conditional-access-policy-common.md).

---

## <a name="november-2019"></a>Listopad 2019

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>Podpora pro atribut SameSite a Chrome 80

**Zadejte:** Plánování změn  
**Kategorie služby:** Ověřování (přihlášení)  
**Schopnost produktu:** Ověřování uživatelů

V rámci zabezpečeného modelu pro soubory cookie se v prohlížeči Chrome 80 mění způsob, jakým zpracovává soubory cookie bez `SameSite` atributu. Každý soubor cookie, který neurčuje `SameSite` atribut, bude zpracován, jako by byl nastaven na hodnotu `SameSite=Lax` , což bude mít za následek blokování určitých scénářů sdílení souborů cookie mezi doménami, na kterých vaše aplikace může záviset. Chcete-li zachovat starší chování aplikace Chrome, můžete použít `SameSite=None` atribut a přidat další `Secure` atribut, aby soubory cookie pro více webů byly dostupné pouze přes připojení HTTPS. Pro Chrome je naplánovaná Tato změna od 4. února 2020.

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

Další informace najdete v tématu [použití sestavy aktivita aplikace AD FS k migraci aplikací do služby Azure AD](../manage-apps/migrate-adfs-application-activity.md).

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>Nový pracovní postup pro uživatele, kteří požadují souhlas správce (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** Access Control

Nový pracovní postup pro vyjádření souhlasu správce poskytuje správcům způsob, jak udělit přístup k aplikacím, které vyžadují schválení správcem. Pokud se uživatel pokusí o přístup k aplikaci, ale není schopen vyjádřit souhlas, může nyní odeslat žádost o schválení správcem. Požadavek se odešle e-mailem a umístí se do fronty, která je přístupná ze Azure Portal, všem správcům, kteří byli určeni jako kontroloři. Až kontrolor provede akci na nevyřízené žádosti, žádající uživatelé se o akci informují.

Další informace najdete v tématu [Konfigurace pracovního postupu pro vyjádření souhlasu správce (Preview)](../manage-apps/configure-admin-consent-workflow.md).

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>Nové možnosti konfigurace tokenu pro registraci Aplikace Azure AD pro správu volitelných deklarací identity (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Jiná  
**Schopnost produktu:** Vývojářské prostředí

Nové okno **Konfigurace tokenu registrací aplikace Azure AD** v Azure Portal nyní zobrazuje vývojářům aplikací dynamický seznam volitelných deklarací identity pro své aplikace. Toto nové prostředí pomáhá zjednodušit migrace aplikací Azure AD a minimalizovat volitelné neškodné konfigurace deklarací identity.

Další informace najdete v tématu [poskytnutí volitelných deklarací identity vaší aplikaci Azure AD](../develop/active-directory-optional-claims.md).

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Nový pracovní postup schvalování ve dvou fázích ve správě nároků ve službě Azure AD (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Jiná  
**Schopnost produktu:** Správa nároků

Představili jsme nový pracovní postup schvalování ve dvou fázích, který vám umožní vyžadovat od dvou schvalovatelů schválení požadavku uživatele na balíček přístupu. Můžete ji například nastavit tak, aby si správce žádajícího uživatele musel nejdřív schválit a potom můžete také vyžadovat schválení vlastníka prostředku. Pokud některý z schvalovatelů neschválí, přístup se neudělí.

Další informace najdete v tématu [Nastavení žádostí o změnu požadavků a schvalování balíčku pro přístup ve správě nároků ve službě Azure AD](../governance/entitlement-management-access-package-request-policy.md).

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Aktualizace stránky Moje aplikace spolu s novými pracovními prostory (Public Preview)

**Zadejte:** Nová funkce  
**Kategorie služby:** Moje aplikace  
**Schopnost produktu:** integrace třetích stran

Teď můžete přizpůsobit způsob zobrazení uživatelů vaší organizace a přístup k obnovenému prostředí moje aplikace. Toto nové prostředí obsahuje také funkci nové pracovní prostory, která uživatelům usnadňuje hledání a organizování aplikací.

Další informace o novém prostředí moje aplikace a vytváření pracovních prostorů najdete v tématu [vytvoření pracovních prostorů na portálu moje aplikace](../manage-apps/access-panel-collections.md).

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Podpora sociálních ID Google pro spolupráci Azure AD B2B (Obecná dostupnost)

**Zadejte:** Nová funkce  
**Kategorie služby:** B2B  
**Schopnost produktu:** Ověřování uživatelů

Nová podpora pro používání sociálních ID Google (účty Gmail) ve službě Azure AD pomáhá zjednodušit spolupráci vašim uživatelům a partnerům. Už není potřeba, aby partneři vytvářeli a spravovali nový účet specifický pro společnost Microsoft. Microsoft Teams teď plně podporuje uživatele Google na všech klientech a v rámci běžných koncových bodů ověřování a klientů.

Další informace najdete v tématu [Přidání Google jako zprostředkovatele identity pro uživatele typu Host B2B](../external-identities/google-federation.md).

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Podpora Microsoft Edge Mobile pro podmíněný přístup a jednotné přihlašování (obecně dostupné)

**Zadejte:** Nová funkce  
**Kategorie služby:** Podmíněný přístup  
**Schopnost produktu:** Zabezpečení identity & ochrana

Azure AD pro Microsoft Edge v iOS a Androidu teď podporuje jednotné Sign-On a podmíněný přístup Azure AD:

- **Jednotné přihlašování Microsoft Edge (SSO):** Pro všechny aplikace připojené k Azure AD je teď k dispozici jednotné přihlašování v rámci nativních klientů (například Microsoft Outlook a Microsoft Edge).

- **Podmíněný přístup Microsoft Edge:** Pomocí zásad podmíněného přístupu na základě aplikace musí vaši uživatelé používat prohlížeče chráněné Microsoft Intune, jako je Microsoft Edge.

Další informace o podmíněném přístupu a jednotném přihlašování s Microsoft Edgem najdete v článku [Podpora Microsoft Edge Mobile pro podmíněný přístup a jednotné přihlašování je teď všeobecně dostupný](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179) Blogový příspěvek. Další informace o tom, jak nastavit klientské aplikace pomocí [podmíněného přístupu založeného na aplikaci](../conditional-access/app-based-conditional-access.md) nebo [podmíněného přístupu na základě zařízení](../conditional-access/require-managed-devices.md), najdete v tématu [Správa webového přístupu pomocí Microsoft Intune prohlížeče chráněného zásadami](/intune/apps/app-configuration-managed-browser).

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Správa nároků Azure AD (Obecná dostupnost)

**Zadejte:** Nová funkce  
**Kategorie služby:** Jiná  
**Schopnost produktu:** Správa nároků

Azure AD – Správa nároků je nová funkce zásad správného řízení identity, která organizacím pomáhá spravovat životní cyklus identit a přístupu ve velkém měřítku. Tato nová funkce pomáhá automatizovat pracovní postupy žádostí o přístup, přiřazení přístupu, recenze a vypršení platnosti napříč skupinami, aplikacemi a weby SharePointu Online.

Díky správě nároků Azure AD můžete efektivněji spravovat přístup pro zaměstnance i pro uživatele mimo vaši organizaci, kteří potřebují přístup k těmto prostředkům.

Další informace najdete v tématu [co je Správa nároků na Azure AD?](../governance/entitlement-management-overview.md#license-requirements)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizace zřizování uživatelských účtů pro tyto nově podporované aplikace SaaS

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran  

Nyní můžete automatizovat vytváření, aktualizaci a odstraňování uživatelských účtů pro tyto nově integrované aplikace:

[Služba SAP Cloud Platform identity Authentication Service](../saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial.md), [RingCentral](../saas-apps/ringcentral-provisioning-tutorial.md), [SpaceIQ](../saas-apps/spaceiq-provisioning-tutorial.md), [Miro](../saas-apps/miro-provisioning-tutorial.md), [Cloudgate](../saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial.md), [info CloudSuite](../saas-apps/infor-cloudsuite-provisioning-tutorial.md), [OfficeSpace Software](../saas-apps/officespace-software-provisioning-tutorial.md), [prioritní matice](../saas-apps/priority-matrix-provisioning-tutorial.md)

Další informace o tom, jak lépe zabezpečit vaši organizaci pomocí automatizovaného zřizování uživatelských účtů, najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Nové federované aplikace dostupné v Aplikace Azure AD galerii – listopad 2019

**Zadejte:** Nová funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** integrace třetích stran

V listopadu 2019 jsme do Galerie aplikací přidali tyto 21 nových aplikací s podporou federace:

Studijní [tabulka](../saas-apps/airtable-tutorial.md), [HootSuite](../saas-apps/hootsuite-tutorial.md), [modrý přístup pro členy (BAM)](../saas-apps/blue-access-for-members-tutorial.md), [bitly](../saas-apps/bitly-tutorial.md), [Riva](../saas-apps/riva-tutorial.md), [ResLife Portal](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), [NegometrixPortal jednotné přihlašování (SSO)](../saas-apps/negometrixportal-tutorial.md), [TeamsChamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279), [Motus](../saas-apps/motus-tutorial.md), [MyAryaka](../saas-apps/myaryaka-tutorial.md), [BlueMail](https://loginself1.bluemail.me/), [Beedle](https://teams-web.beedle.co/#/), [Visma](../saas-apps/visma-tutorial.md), [OneDesk](../saas-apps/onedesk-tutorial.md), [FOKO Retail](../saas-apps/foko-retail-tutorial.md), Qmarkets [nápad & Správa inovací](../saas-apps/qmarkets-idea-innovation-management-tutorial.md), [Netskope ověřování uživatelů](../saas-apps/netskope-user-authentication-tutorial.md), uniFLOW [online](../saas-apps/uniflow-online-tutorial.md), [Claromentis](../saas-apps/claromentis-tutorial.md), [JISC pro registraci Voter](../saas-apps/jisc-student-voter-registration-tutorial.md), [e4enable](https://portal.e4enable.com/)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>Nová a vylepšená Galerie aplikací Azure AD

**Zadejte:** Změněná funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ

Aktualizovali jsme galerii aplikací Azure AD, abychom vám usnadnili hledání předem integrovaných aplikací, které podporují zřizování, OpenID připojení a SAML ve vašem tenantovi Azure Active Directory.

Další informace najdete v tématu [Přidání aplikace do tenanta Azure Active Directory](../manage-apps/add-application-portal.md).

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>Zvýšení limitu délky definice role aplikace z 120 na 240 znaků

**Zadejte:** Změněná funkce  
**Kategorie služby:** Podnikové aplikace  
**Schopnost produktu:** JEDNOTNÉ

Od zákazníků jsem slyšeli, že limit délky pro hodnotu definice role aplikace v některých aplikacích a službách je moc krátký v 120 znacích. V reakci jsme zvýšili maximální délku definice hodnoty role na 240 znaků.

Další informace o použití definic rolí specifických pro aplikaci najdete v tématu [Přidání rolí aplikace v aplikaci a jejich přijetí v tokenu](../develop/howto-add-app-roles-in-azure-ad-apps.md).

---

## <a name="october-2019"></a>Říjen 2019

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Vyřazení rozhraní API identityRiskEvent pro detekci rizik Azure AD Identity Protection

**Zadejte:** Plán pro změnu **Kategorie služby:** **schopnost produktu** Identity protection: zabezpečení identity & ochrana

V reakci na zpětnou vazbu od vývojářů teď můžou předplatitelé Azure AD Premium P2 provádět komplexní dotazy na data o detekci rizika Azure AD Identity Protection pomocí nového rozhraní API riskDetection pro Microsoft Graph. Stávající beta verze rozhraní [identityRiskEvent](/graph/api/resources/identityriskevent?view=graph-rest-beta&preserve-view=true) API přestane vracet data z **10. ledna 2020**. Pokud vaše organizace používá rozhraní identityRiskEvent API, měli byste přejít na nové rozhraní riskDetection API.

Další informace o novém rozhraní riskDetection API najdete v [referenční dokumentaci k rozhraní API pro detekci rizik](/graph/api/resources/riskdetection).

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>Podpora proxy aplikací pro atribut SameSite a Chrome 80

**Zadejte:** Plánování **Kategorie služby Change Service:** **schopnost produktu** proxy aplikace: Access Control

Několik týdnů před vydáním prohlížeče Chrome 80, plánujeme aktualizovat způsob, jakým soubory cookie proxy aplikace zacházejí s atributem **SameSite** . S vydáním Chrome 80 se všechny soubory cookie, které nespecifikují atribut **SameSite** , zpracují, jako kdyby byla nastavena na `SameSite=Lax` .

Abychom se vyhnuli potenciálně negativním dopadům z důvodu této změny, aktualizujeme přístup k proxy aplikací a soubory cookie relace pomocí:

- Nastavení výchozí hodnoty pro nastavení **použít zabezpečený soubor cookie** na **Ano**.

- Nastavení výchozí hodnoty pro atribut **SameSite** na **hodnotu None**.

    >[!NOTE]
    > Soubory cookie přístupu k proxy aplikacím se vždycky přenesly výhradně přes zabezpečené kanály. Tyto změny se vztahují pouze na soubory cookie relace.

Další informace o nastavení souborů cookie proxy aplikace najdete v tématu [nastavení souborů cookie pro přístup k místním aplikacím v Azure Active Directory](../manage-apps/application-proxy-configure-cookie-settings.md).

---

### <a name="app-registrations-legacy-and-app-management-in-the-application-registration-portal-appsdevmicrosoftcom-is-no-longer-available"></a>Registrace aplikací (starší verze) a Správa aplikací na portálu pro registraci aplikací (apps.dev.microsoft.com) již nejsou k dispozici.

**Zadejte:** Plánování **Kategorie služby Change Service:** není k dispozici **možnost produktu:** vývojové prostředí

Uživatelé s účty Azure AD už nemůžou registrovat ani spravovat aplikace pomocí portálu pro registraci aplikací (apps.dev.microsoft.com) nebo registrovat a spravovat aplikace v Azure Portal Registrace aplikací (starší verze).

Další informace o novém prostředí Registrace aplikací najdete v [Registrace aplikací v průvodci školením Azure Portal](../develop/quickstart-register-app.md).

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>Pro uživatele, kteří se při migraci z MFA na podmíněný přístup využívají, už se nevyžadují, aby se zaregistrovali znovu.

**Zadejte:** **Kategorie pevné služby:** **schopnost produktu MFA:** zabezpečení identity & ochrana

Opravili jsme známý problém, kdy se uživatelé museli znovu zaregistrovat, pokud byli zakázáni pro jednotlivé uživatele Multi-Factor Authentication (MFA) a pak povolili MFA prostřednictvím zásad podmíněného přístupu.

Pokud chcete, aby se uživatelé znovu zaregistrovali, můžete z metod ověřování uživatele na portálu Azure AD vybrat **požadovanou možnost znovu zaregistrovat možnost MFA** . Další informace o migraci uživatelů z MFA na ověřování MFA na základě podmíněného přístupu najdete v tématu [převod uživatelů z MFA na ověřování MFA na základě vícefaktorového přístupu](../authentication/howto-mfa-getstarted.md#convert-users-from-per-user-mfa-to-conditional-access-based-mfa).

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Nové funkce pro transformaci a odeslání deklarací identity ve vašem tokenu SAML

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu podnikové aplikace **:** jednotné přihlašování

Přidali jsme další možnosti, které vám pomůžou přizpůsobit a poslat deklarace identity ve vašem tokenu SAML. Mezi tyto nové funkce patří:

- Další funkce pro transformaci deklarací identity, které vám pomůžou upravit hodnotu, kterou odešlete v deklaraci identity.

- Možnost použít více transformací na jednu deklaraci identity.

- Možnost zadat zdroj deklarací identity na základě typu uživatele a skupiny, do které uživatel patří

Podrobné informace o těchto nových funkcích, včetně způsobu jejich použití, najdete v tématu [přizpůsobení deklarací identity vystavených v tokenu SAML pro podnikové aplikace](../develop/active-directory-saml-claims-customization.md).

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Nová stránka moje přihlášení pro koncové uživatele v Azure AD

**Zadejte:** Nová **Kategorie služby funkcí:** ověřování (přihlášení) **schopnost produktu:** monitorování &ch sestav

Přidali jsme novou stránku **Moje přihlášení** ( https://mysignins.microsoft.com) aby si uživatelé vaší organizace mohli zobrazit historii jejich nedávného přihlášení, aby zkontrolovali, že neobvyklá aktivita. Tato nová stránka umožňuje uživatelům zobrazit:

- Pokud se někdo pokusí uhodnout svoje heslo.

- Pokud se útočník úspěšně přihlásil ke svému účtu a z kterého umístění.

- K jakým aplikacím se útočník pokusil získat přístup.

Další informace najdete v tématu [Uživatelé teď můžou zkontrolovat historii přihlášení pro neobvyklý blog aktivity](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066) .

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Migrace Azure AD Domain Services (Azure služba AD DS) z klasických do Azure Resource Manager virtuálních sítí

**Zadejte:** Nová **Kategorie služby funkcí:** Azure AD Domain Services **možnosti produktu:** Azure AD Domain Services

Našim zákazníkům, kteří se zablokovali na klasických virtuálních sítích – máme skvělé novinky. Nyní můžete provést jednorázovou migraci z klasické virtuální sítě do existující Správce prostředků virtuální sítě. Po přesunu do Správce prostředků virtuální sítě budete moci využít výhod dalších a upgradovaných funkcí, jako jsou podrobné zásady pro hesla, e-mailová oznámení a protokoly auditu.

Další informace najdete v tématu [verze Preview – migrace Azure AD Domain Services z modelu klasických virtuálních sítí do Správce prostředků](../../active-directory-domain-services/migrate-from-classic-vnet.md).

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Aktualizace rozložení kontraktu stránky Azure AD B2C

**Zadejte:** Nová **Kategorie služby funkcí:** B2C-Consumer Identity Management **Product schopnost:** B2B/B2C

Zavedli jsme některé nové změny verze 1.2.0 kontraktu stránky pro Azure AD B2C. V této aktualizované verzi teď můžete řídit pořadí načítání pro vaše prvky, což může také zabránit blikání, ke kterému dojde, když je načtena šablona stylů (CSS).

Úplný seznam změn provedených u kontraktu stránky najdete v [protokolu změn verze](../../active-directory-b2c/page-layout.md#other-pages-providerselection-claimsconsent-unifiedssd).

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Aktualizace stránky Moje aplikace spolu s novými pracovními prostory (Public Preview)

**Zadejte:** Nová **Kategorie služby funkcí:** **funkce produktu** moje aplikace: Access Control

Nyní můžete přizpůsobit způsob, jakým uživatelé vaší organizace zobrazují a přistupují ke značce – nové prostředí moje aplikace, včetně použití funkce nové pracovní prostory, aby je bylo snazší najít. Funkce New Workspaces funguje jako filtr pro aplikace, ke kterým mají uživatelé vaší organizace přístup.

Další informace o tom, jak vytvářet nové prostředí moje aplikace a vytvářet pracovní prostory, najdete v tématu [vytvoření pracovních prostorů na portálu moje aplikace (Preview)](../manage-apps/access-panel-collections.md).

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>Podpora měsíčního modelu fakturace založeného na aktivních uživatelích (Obecná dostupnost)

**Zadejte:** Nová **Kategorie služby funkcí:** B2C-Consumer Identity Management **Product schopnost:** B2B/B2C

Azure AD B2C teď podporuje fakturaci měsíčně aktivních uživatelů (MAU). Faktura MAU vychází z počtu jedinečných uživatelů s aktivitou ověřování v průběhu kalendářního měsíce. Stávající zákazníci můžou kdykoli přejít na tuto novou metodu fakturace.

Od 1. listopadu 2019 se budou všichni noví zákazníci automaticky účtovat pomocí této metody. Tato metoda účtování těží zákazníky díky cenovým výhodám a možnosti plánování předem.

Další informace najdete v tématu [upgrade modelu fakturace na měsíční aktivní uživatele](../../active-directory-b2c/billing.md#switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Nové federované aplikace dostupné v Aplikace Azure AD galerii – říjen 2019

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu podnikové aplikace **:** integrace třetích stran

V říjnu 2019 jsme do Galerie aplikací přidali tyto 35 nové aplikace s podporou federace:

[V případě krize – Mobile](../saas-apps/in-case-of-crisis-mobile-tutorial.md), [Juno cest](../saas-apps/juno-journey-tutorial.md), [ExponentHR](../saas-apps/exponenthr-tutorial.md), [TACT](https://www.tact.ai/products/tact-assistant), [OpusCapita řízení pokladní správy](https://appsource.microsoft.com/product/web-apps/opuscapitagroupoy-1036255.opuscapita-cm), [Salestim](https://www.salestim.com/), [Learnster](../saas-apps/learnster-tutorial.md), [dynaTrace](../saas-apps/dynatrace-tutorial.md), [HunchBuzz](https://login.hunchbuzz.com/integrations/azure/process), [Freshworks](../saas-apps/freshworks-tutorial.md), [ECornell](../saas-apps/ecornell-tutorial.md), [ShipHazmat](../saas-apps/shiphazmat-tutorial.md), [Netskope Cloud Security](../saas-apps/netskope-cloud-security-tutorial.md), [contentd](../saas-apps/contentful-tutorial.md), [Bindtuning](https://bindtuning.com/login), HireVue [ová souřadnice – Evropa](https://www.hirevue.com/), [HireVue – koordinace – USOnly](https://www.hirevue.com/), [HireVue souřadnice – USA](https://www.hirevue.com/), WittyParrot, [Cloudmore](../saas-apps/cloudmore-tutorial.md), [](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), Visit.org, [cambium,](../saas-apps/visitorg-tutorial.md) [Xirrus EasyPass Paylocity](https://login.xirrus.com/azure-signup) [, SIGNL4](../saas-apps/paylocity-tutorial.md), vyjímá [!](../saas-apps/mail-luck-tutorial.md), [Týmová](https://theteamie.com/)schránka, [rychlost pro týmy](https://velocity.peakup.org/teams/login), [EAB](https://account.signl4.com/manage), [impl, ScreenMeet](../saas-apps/eab-navigate-impl-tutorial.md) [, ExactCare,,](https://console.screenmeet.com/) [bod Omega](https://pi.ompnt.com/), [Hlasová pošta pro Intune (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune) [,](https://ihealthnav.com/account/signin) [hlasová zpráva pro Office 365 Direct (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct) [,](https://qubie.azurewebsites.net/static/adminTab/authorize.html) [iHealthHome SSO](../saas-apps/exactcare-sso-tutorial.md)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Konsolidovaná položka nabídky zabezpečení na portálu Azure AD

**Zadejte:** Kategorie změněné **služby funkcí:** **schopnost produktu** Identity Protection & ochrana identity – zabezpečení

Teď máte přístup ke všem dostupným funkcím zabezpečení Azure AD z nové položky nabídky **zabezpečení** a z panelu **hledání** v Azure Portal. Kromě toho nová úvodní stránka **zabezpečení** , která se nazývá **zabezpečení – Začínáme**, bude obsahovat odkazy na naši veřejnou dokumentaci, pokyny pro zabezpečení a příručky pro nasazení.

Nová nabídka **zabezpečení** zahrnuje:

- Podmíněný přístup
- Identity Protection
- Security Center
- Skóre Secure identity
- Metody ověřování
- MFA
- Sestavy rizik – rizikové uživatele, rizikové přihlašovacíky, detekce rizik
- A další...

Další informace najdete v tématu [zabezpečení – Začínáme](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted).

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Zásady vypršení platnosti skupin Office 365 se vylepšily pomocí automatické obnovy.

**Zadejte:** Kategorie změněné **služby funkcí:** **schopnost produktů** správy skupin: Správa životního cyklu identit

Zásady vypršení platnosti skupin Office 365 se rozšířily tak, aby automaticky obnovily skupiny, které aktivně používají její členové. Skupiny se v závislosti na aktivitě uživatelů na všech aplikacích Office 365, včetně Outlooku, SharePointu a týmů, obnoví.

Toto vylepšení pomáhá snižovat vaše oznámení o vypršení platnosti skupiny a pomáhá zajistit, aby byly aktivní skupiny dál dostupné. Pokud pro skupiny Office 365 už máte aktivní zásady vypršení platnosti, nemusíte nic dělat, abyste tuto novou funkci zapnuli.

Další informace najdete v tématu [Konfigurace zásad vypršení platnosti pro skupiny Office 365](../enterprise-users/groups-lifecycle.md).

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Prostředí pro vytváření aktualizací Azure AD Domain Services (Azure služba AD DS)

**Zadejte:** Kategorie změněné **služby funkcí:** Azure AD Domain Services **možnosti produktu:** Azure AD Domain Services

Aktualizovali jsme Azure AD Domain Services (Azure služba AD DS), aby zahrnovala nové a vylepšené prostředí pro vytváření, což vám pomůže vytvořit spravovanou doménu pouhým třemi kliknutími! Kromě toho teď můžete nahrát a nasadit Azure služba AD DS ze šablony.

Další informace najdete v tématu [kurz: vytvoření a konfigurace instance Azure Active Directory Domain Services](../../active-directory-domain-services/tutorial-create-instance.md).

---

## <a name="september-2019"></a>Září 2019

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Plán změny: vyřazení Power BI balíčků obsahu

**Zadejte:** Plánování **Kategorie služby Change Service:** **funkce produktu** pro sestavy: monitorování & vytváření sestav

Od 1. října 2019 začne Power BI začínat všechny balíčky obsahu, včetně balíčku obsahu Power BI Azure AD. Jako alternativu k tomuto balíčku obsahu můžete použít sešity Azure AD a získat přehled o službách souvisejících s Azure AD. Další sešity se připravují, včetně sešitů o zásadách podmíněného přístupu v režimu pouze sestavy, přehledů na základě souhlasu aplikace a dalších.

Další informace o sešitech najdete v tématu [Jak používat Azure monitor sešity pro Azure Active Directory sestavy](../reports-monitoring/howto-use-azure-monitor-workbooks.md). Další informace o vyřazení balíčků obsahu najdete v blogovém příspěvku s [oznámením o obecných dostupnosti pro Power BI šablonových aplikací](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/) .

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>Můj profil se přejmenovává a integruje se stránkou systém Microsoft Office účtu.

**Zadejte:** Plánování **Kategorie služby Change Service:** můj profil nebo **možnost produktu účtu:** spolupráce

Od října se moje prostředí mého profilu stane účtem. V rámci této změny všude, kde se aktuálně říká, se **můj profil** změní na **můj účet**. Na základě změny názvů a některých vylepšení návrhu nabízí aktualizovaný způsob další integraci se stránkou systém Microsoft Office účtu. Konkrétně budete mít přístup k instalacím a předplatným systému Office na stránce **Přehled účtu** , a to spolu s preferencemi kontaktů souvisejících s Office ze stránky **soukromí** .

Další informace o prostředí můj profil (Preview) najdete v tématu [Přehled portálu Profile (Preview)](../user-help/my-account-portal-overview.md).

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Hromadná Správa skupin a členů pomocí souborů CSV na portálu Azure AD (Public Preview)

**Zadejte:** Nová **Kategorie služby funkcí:** **schopnost produktu** správy skupin: spolupráce

S potěšením oznamujeme, že v portálu Azure AD je dostupná veřejná verze Preview prostředí pro správu hromadných skupin. Teď můžete použít soubor CSV a portál Azure AD ke správě skupin a seznamů členů, včetně těchto:

- Přidávání nebo odebírání členů ze skupiny.

- Stahuje se seznam skupin z adresáře.

- Stahuje se seznam členů skupiny pro konkrétní skupinu.

Další informace najdete v tématu [hromadné přidání členů](../enterprise-users/groups-bulk-import-members.md), [hromadné odebrání členů](../enterprise-users/groups-bulk-remove-members.md), [seznam členů hromadného stahování](../enterprise-users/groups-bulk-download-members.md)a [seznam hromadných stažení skupin](../enterprise-users/groups-bulk-download.md).

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>Dynamický souhlas se teď podporuje prostřednictvím nového koncového bodu souhlasu správce.

**Zadejte:** Nová **Kategorie služby funkcí:** ověřování (přihlášení) **schopnost produktu:** ověřování uživatelů

Vytvořili jsme nového koncového bodu souhlasu správce, který podporuje dynamické vyjádření souhlasu, což je užitečné pro aplikace, které chtějí používat model dynamického souhlasu na platformě Microsoft identity.

Další informace o použití tohoto nového koncového bodu najdete v tématu [použití koncového bodu pro vyjádření souhlasu správce](../develop/v2-admin-consent.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Nové federované aplikace dostupné v Aplikace Azure AD galerii – září 2019

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu podnikové aplikace **:** integrace třetích stran

Do září 2019 jsme do Galerie aplikací přidali tyto 29 nových aplikací s podporou federace:

[ScheduleLook](https://schedulelook.bbsonlineservices.net/), Microsoft [Azure SSO Access pro dodržování předpisů Ethidex &trade; – jednotné přihlašování](../saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial.md), [iServer Portal](../saas-apps/iserver-portal-tutorial.md), [SKYSITE](../saas-apps/skysite-tutorial.md), [Concur cestovné a výdaje](../saas-apps/concur-travel-and-expense-tutorial.md), [WorkBoard](../saas-apps/workboard-tutorial.md), `https://apps.yeeflow.com/` , [zařízení ARC](../saas-apps/arc-facilities-tutorial.md), [Luware Stratus tým](https://stratus.emea.luware.cloud/login), [nejrůznějších nápadů](https://wideideas.online/wideideas/), [Prisma Cloud](../saas-apps/prisma-cloud-tutorial.md), [JDLT klienta](https://clients.jdlt.co.uk/login), [RENRAKU](../saas-apps/renraku-tutorial.md), [SealPath zabezpečený prohlížeč](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive), [Prisma Cloud](../saas-apps/prisma-cloud-tutorial.md),, Cintoo, Whitesource, CakeHR, COO, týmu, Kai, `https://app.penneo.com/` `https://app.testhtm.com/settings/email-integration` [](../saas-apps/sonarqube-tutorial.md) [Adobe Identity Management](../saas-apps/tutorial-list.md), možnosti zjišťování pro [](../saas-apps/whitesource-tutorial.md) [SSO](../saas-apps/discovery-benefits-sso-tutorial.md) [](../saas-apps/bis-tutorial.md) [](https://ms-contacts.coo-kai.jp/) [](https://app.amelio.co/) [](../saas-apps/cakehr-tutorial.md) [](https://aec.cintoo.com/login) [](../saas-apps/hosted-heritage-online-sso-tutorial.md) [](../saas-apps/idc-tutorial.md), SonarQube,`https://itask.yipinapp.com/`

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-azure-ad-global-reader-role"></a>Nová role globálního čtecího modulu Azure AD

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu role Azure AD **:** Access Control

Od 24. září 2019 začneme zavádět novou roli Azure Active Directory (AD) s názvem globální čtenář. Tato implementace začíná v rámci produkčního a globálního cloudového zákazníka (RSZ), dokončuje se v říjnu po celém světě.

Role globálního čtecího modulu je protějškem jen pro čtení pro globálního správce. Uživatelé v této roli můžou číst nastavení a informace pro správu napříč Microsoft 365 službami, ale nemůžou provádět akce správy. Vytvořili jsme roli globálního čtenáře, která vám pomůže snížit počet globálních správců ve vaší organizaci. Vzhledem k tomu, že účty globálních správců jsou výkonné a zranitelné vůči útokům, doporučujeme, abyste měli méně než pět globálních správců. Pro plánování, audity a vyšetřování doporučujeme použít roli globálního čtenáře. Doporučujeme také používat roli globálního čtecího modulu v kombinaci s jinými rolemi s omezeným oprávněním správce, jako je třeba správce Exchange, a získat tak práci, aniž byste museli provádět globální role správce.

Role globálního čtecího modulu funguje s novým centrem pro správu Microsoft 365, centrem pro správu Exchange, centrem pro správu týmů, Security Center, centrem dodržování předpisů, centrem pro správu Azure AD a centrem pro správu správy zařízení.

>[!NOTE]
> Na začátku veřejné verze Preview nebude role globálního čtecího zařízení fungovat s: SharePoint, Privileged Access Management, Customer Lockbox, popisky s citlivostí, životní cyklus týmů, vytváření sestav & Analytics, týmy pro správu telefonních zařízení a týmy katalogu aplikací.

Další informace najdete v tématu [oprávnění role správce v Azure Active Directory](../roles/permissions-reference.md).

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Přístup k místnímu serveru sestav z aplikace Power BI Mobile pomocí Proxy aplikací služby Azure Active Directory

**Zadejte:** Nová **Kategorie služby funkcí:** **schopnost produktu** proxy aplikace: Access Control

Nová integrace mezi Power BI mobilní aplikací a Azure Proxy aplikací služby AD umožňuje bezpečné přihlášení k Power BI mobilní aplikaci a zobrazení všech sestav vaší organizace hostovaných na místních Server sestav Power BI.

Informace o aplikaci Power BI Mobile, včetně místa, odkud si aplikaci stáhnout, najdete na [webu Power BI](https://powerbi.microsoft.com/mobile/). Další informace o tom, jak nastavit mobilní aplikaci Power BI pomocí Azure Proxy aplikací služby AD, najdete v tématu [Povolení vzdáleného přístupu k Power BI Mobile s využitím azure proxy aplikací služby AD](../manage-apps/application-proxy-integrate-with-power-bi.md).

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>Je dostupná nová verze modulu AzureADPreview PowerShellu.

**Zadejte:** Kategorie změněné **služby funkcí:** další **schopnost produktu:** adresář

Do modulu AzureADPreview byly přidány nové rutiny, které vám pomůžou definovat a přiřazovat vlastní role v Azure AD, včetně těchto:

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Nová verze Azure AD Connect

**Zadejte:** Kategorie změněné **služby funkcí:** další **schopnost produktu:** adresář

Vydali jsme aktualizovanou verzi Azure AD Connect pro zákazníky s automatickým upgradem. Tato nová verze zahrnuje několik nových funkcí, vylepšení a opravy chyb. Další informace o této nové verzi najdete v tématu [Azure AD Connect: Historie vydání verze](../hybrid/reference-connect-version-history.md#14250).

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Server Azure Multi-Factor Authentication (MFA), verze 8.0.2 je teď k dispozici.

**Zadejte:** **Kategorie pevné služby:** **schopnost produktu MFA:** zabezpečení identity & ochrana

Pokud jste stávající zákazník, který vyaktivoval MFA Server před 1. červenci 2019, můžete si teď stáhnout nejnovější verzi MFA serveru (verze 8.0.2). V této nové verzi jsme:

- Opravili jsme problém, takže když Azure AD Sync změní uživatele na povoleno, pošle se uživateli e-mail.

- Opravili jsme problém, aby zákazníci mohli úspěšně upgradovat a přitom dál používat funkce značek.

- Přidání kódu země Kosova (+ 383).

- Do protokolu MultiFactorAuthSvc. log bylo přidáno protokolování auditu jednorázového přihlášení.

- Vylepšený výkon pro sadu SDK webové služby.

- Opravené další drobné chyby.

Od 1. července 2019 se Microsoftu zastavilo poskytování MFA serveru pro nová nasazení. Noví zákazníci, kteří vyžadují službu Multi-Factor Authentication, by měli používat cloudovou Multi-Factor Authentication služby Azure AD. Další informace najdete v tématu [Plánování nasazení služby Azure AD založené na cloudu Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md).

---

## <a name="august-2019"></a>Srpen 2019

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>Rozšířené hledání, filtrování a řazení pro skupiny je dostupné na portálu Azure AD (Public Preview).

**Zadejte:** Nová **Kategorie služby funkcí:** **schopnost produktu** správy skupin: spolupráce

S radostí oznamujeme dostupnost verze Public Preview pro rozšířené prostředí související se skupinami na portálu Azure AD. Tato vylepšení vám pomůžou lépe spravovat skupiny a seznamy členů tím, že poskytují:

- Rozšířené možnosti vyhledávání, jako je hledání v podřetězcůch v seznamech skupin.
- Rozšířené možnosti filtrování a řazení v seznamech členů a vlastníků.
- Nové možnosti vyhledávání pro seznamy členů a vlastníků.
- Přesnější počty skupin pro velké skupiny.

Další informace najdete v tématu [Správa skupin v Azure Portal](./active-directory-groups-members-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context).

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Pro správu registrace aplikací jsou k dispozici nové vlastní role (Public Preview)

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu role Azure AD **:** Access Control

Vlastní role (k dispozici v rámci předplatného Azure AD P1 nebo P2) vám teď můžou pomáhat s jemnou přístupem tím, že vám umožní vytvořit definice rolí s konkrétními oprávněními a pak tyto role přiřadit konkrétním prostředkům. V současné době vytvoříte vlastní role pomocí oprávnění pro správu registrací aplikací a potom přiřadíte roli ke konkrétní aplikaci. Další informace o vlastních rolích najdete v tématu [vlastní role správců v Azure Active Directory (Preview)](../roles/custom-overview.md).

Pokud potřebujete další oprávnění nebo prostředky, které se v tuto chvíli nezobrazuje, můžete poslat svůj názor na náš [web Azure Feedback](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) a my přidáme vaši žádost do naší mapy aktualizace.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>Nové protokoly zřizování vám můžou pomoct monitorovat a řešit potíže s nasazením zřizování aplikací (Public Preview).

**Zadejte:** Nová **Kategorie služby funkcí:** zřizování aplikací **:** Správa životního cyklu identit

K dispozici jsou nové protokoly zřizování, které vám pomůžou monitorovat a řešit potíže s nasazením zřizování uživatelů a skupin. Tyto nové soubory protokolu obsahují informace o:

- Které skupiny byly úspěšně vytvořeny v [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md)
- Jaké role byly naimportovány z [AWS Single-Account přístupu](../saas-apps/amazon-web-service-tutorial.md#configure-and-test-azure-ad-sso-for-aws-single-account-access)
- Kteří zaměstnanci nebyli naimportovali z [Workday](../saas-apps/workday-inbound-tutorial.md)

Další informace najdete v tématu [zřizování sestav na portálu Azure Active Directory (Preview)](../reports-monitoring/concept-provisioning-logs.md).

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Nové sestavy zabezpečení pro všechny správce Azure AD (všeobecně dostupné)

**Zadejte:** Nová **Kategorie služby funkcí:** **schopnost produktu** Identity Protection & ochrana identity zabezpečení

Ve výchozím nastavení budou mít všichni správci služby Azure AD brzy přístup k moderním sestavám zabezpečení v rámci Azure AD. Až do konce září budete moct použít banner v horní části moderních sestav zabezpečení pro návrat k původním sestavám.

Moderní sestavy zabezpečení poskytují další možnosti ze starších verzí, včetně těchto:

- Rozšířené filtrování a řazení
- Hromadné akce, jako je například chybějící riziko uživatele
- Potvrzení napadených nebo bezpečných entit
- Stav rizika, který pokrývá: ohrožené, neúspěšné, opravené a potvrzené ohrožení
- Nové detekce související s riziky (k dispozici pro Azure AD Premium předplatitele)

Další informace najdete v tématech [rizikové uživatele](../identity-protection/howto-identity-protection-investigate-risk.md#risky-users), [rizikové přihlašovací](../identity-protection/howto-identity-protection-investigate-risk.md#risky-sign-ins)údaje a [detekce rizik](../identity-protection/howto-identity-protection-investigate-risk.md#risk-detections).

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>Spravovaná identita přiřazená uživatelem je k dispozici pro Virtual Machines a Virtual Machine Scale Sets (Obecná dostupnost).

**Zadejte:** Nová **Kategorie služby funkcí:** spravované identity pro prostředky Azure **schopnost produktu:** vývojové prostředí

Spravované identity přiřazené uživatelem jsou teď všeobecně dostupné pro Virtual Machines a Virtual Machine Scale Sets. V rámci této služby může Azure vytvořit identitu v tenantovi Azure AD, která je důvěryhodná pro používané předplatné, a dá se přiřadit k jedné nebo více instancím služby Azure. Další informace o spravovaných identitách přiřazených uživateli najdete v tématu [co jsou spravované identity pro prostředky Azure](../managed-identities-azure-resources/overview.md).

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>Uživatelé můžou resetovat svá hesla pomocí mobilní aplikace nebo hardwarového tokenu (všeobecně dostupná).

**Zadejte:** Kategorie změněné **služby funkcí:** Samoobslužné resetování hesla **produkt:** ověřování uživatelů

Uživatelé, kteří si zaregistrovali mobilní aplikaci ve vaší organizaci, teď můžou resetovat oznámení z aplikace Microsoft Authenticator nebo zadáním kódu z mobilní aplikace nebo hardwarového tokenu.

Další informace najdete v tématu [jak to funguje: Samoobslužné resetování hesla služby Azure AD](../authentication/concept-sspr-howitworks.md). Další informace o uživatelském prostředí najdete v tématu [resetování vlastního pracovního nebo školního hesla](../user-help/active-directory-passwords-reset-register.md).

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET ignoruje sdílenou mezipaměť MSAL.NET pro scénáře v zastoupení.

**Zadejte:** **Kategorie pevné služby:** ověřování (přihlášení) **schopnost produktu:** ověřování uživatelů

Počínaje službou Azure AD Authentication Library (ADAL.NET) verze 5.0.0-Preview musí vývojáři aplikací [serializovat jednu mezipaměť na účet pro webové aplikace a webová rozhraní API](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api). V opačném případě může dojít ke zvýšení oprávnění v některých scénářích využívajících [tok](../develop/scenario-web-api-call-api-app-configuration.md?tabs=java) pro jazyk Java, společně s některými konkrétními případy použití `UserAssertion` . Aby se zabránilo této chybě, ADAL.NET nyní ignoruje sdílenou mezipaměť Microsoft Authentication Library for dotnet (MSAL.NET) pro scénáře v zastoupení.

Další informace o tomto problému naleznete v tématu [Azure Active Directory zvýšení úrovně zabezpečení oprávnění v knihovně ověřování](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Nové federované aplikace dostupné v Aplikace Azure AD galerii – srpen 2019

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu podnikové aplikace **:** integrace třetích stran

V srpnu 2019 jsme do Galerie aplikací přidali tyto 26 nových aplikací s podporou federace:

[Civic Platform](../saas-apps/civic-platform-tutorial.md), [Amazon Business](../saas-apps/amazon-business-tutorial.md), [ProNovos](../saas-apps/pronovos-ops-manager-tutorial.md)Operations Manager, Cognidox, [Viareport – portál Inativ (Evropa)](../saas-apps/viareports-inativ-portal-europe-tutorial.md), [Azure Databricks](https://azure.microsoft.com/services/databricks), [Robin](../saas-apps/robin-tutorial.md), [](../saas-apps/cognidox-tutorial.md) [,](../saas-apps/academy-attendance-tutorial.md) [prioritní matrice](https://sync.appfluence.com/pmwebng/), [Academy Cousto](https://cousto.platformers.be/account/login), [MySpace](https://uploadcare.com/accounts/signup/), [Uploadcare Backup Endpoint](../saas-apps/carbonite-endpoint-backup-tutorial.md), [Carbonite](../saas-apps/cpqsync-by-cincom-tutorial.md)CPQSync, [Cincom](../saas-apps/chargebee-tutorial.md), [doručovat. Media &trade; Portal](https://portal.deliver.media), Chargebee [vzdělávání](../saas-apps/frontline-education-tutorial.md), [F5](https://www.f5.com/products/security/access-policy-manager), [prvotní AD Connect](https://www.stashcat.com), Blink, [stashcat](../saas-apps/vocoli-tutorial.md), [Vocoli Analytics](../saas-apps/pronovos-analytics-tutorial.md), [ProNovos](../saas-apps/sigstr-tutorial.md), [Sigstr](../saas-apps/darwinbox-tutorial.md), [Sledujte barvy](../saas-apps/watch-by-colors-tutorial.md), [rozsvítit](../saas-apps/blink-tutorial.md), [](../saas-apps/eab-navigate-strategic-care-tutorial.md) [Darwinbox](../saas-apps/harness-tutorial.md)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>K dispozici jsou nové verze modulů PowerShellu pro AzureAD a prostředí AzureADPreview PowerShell.

**Zadejte:** Kategorie změněné **služby funkcí:** další **schopnost produktu:** adresář

K dispozici jsou nové aktualizace modulů PowerShellu pro AzureAD a AzureAD ve verzi Preview:

- `-Filter`Do `Get-AzureADDirectoryRole` parametru v modulu AzureAD byl přidán nový parametr. Tento parametr vám pomůže filtrovat role adresáře vrácené rutinou.
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

**Zadejte:** Kategorie změněné **služby funkcí:** **schopnost produktu** Správa skupin: spolupráce

Provedli jsme některá vylepšení uživatelského rozhraní pro tvůrce pravidel dynamické skupiny, který je k dispozici v Azure Portal, abyste mohli snadněji nastavit nové pravidlo nebo změnit stávající pravidla. Toto vylepšení návrhu umožňuje vytvořit pravidla s až pěti výrazy, nikoli jenom s jedním. Aktualizovali jsme také seznam vlastností zařízení, aby se odebraly zastaralé vlastnosti zařízení.

Další informace najdete v tématu [Správa pravidel dynamického členství](../enterprise-users/groups-dynamic-membership.md).

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>Nové oprávnění aplikace Microsoft Graph k dispozici pro použití s recenzemi přístupu

**Zadejte:** Kategorie změněné **služby funkcí:** kontroly přístupu **Možnosti produktu:** zásady správného řízení identity

Zavedli jsme nové oprávnění aplikace Microsoft Graph, `AccessReview.ReadWrite.Membership` které umožňuje aplikacím automaticky vytvářet a načítat kontroly přístupu pro členství ve skupinách a přiřazení aplikací. Toto oprávnění můžou použít naplánované úlohy nebo v rámci automatizace, aniž by bylo nutné přihlášený kontext uživatele.

Další informace najdete v [příkladu vytváření kontrol přístupu Azure AD pomocí Microsoft Graph oprávnění aplikace s blogem PowerShellu](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241).

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Protokoly aktivit Azure AD jsou teď dostupné pro státní cloudové instance v Azure Monitor

**Zadejte:** Kategorie změněné **služby funkcí:** hlášení **možností produktu:** monitorování & vytváření sestav

S radostí oznamujeme, že protokoly aktivit Azure AD jsou teď dostupné pro státní cloudové instance v Azure Monitor. Protokoly Azure AD teď můžete odesílat do svého účtu úložiště nebo do centra událostí, abyste je mohli integrovat s SIEM nástroji, jako je [SumoLogic](../reports-monitoring/howto-integrate-activity-logs-with-sumologic.md), [Splunk](../reports-monitoring/howto-integrate-activity-logs-with-splunk.md)a [ArcSight](../reports-monitoring/howto-integrate-activity-logs-with-arcsight.md).

Další informace o nastavení Azure Monitor najdete [v tématu protokoly aktivit Azure AD v Azure monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md#cost-considerations).

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Aktualizace uživatelů na nové a rozšířené prostředí informací o zabezpečení

**Zadejte:** Kategorie změněné **služby funkcí:**  ověřování (přihlášení) **schopnost produktu:** ověřování uživatelů

25. září 2019 se vypíná staré, nerozšířené informace o zabezpečení pro registraci a správu bezpečnostních údajů uživatelů a zapínání nové [Rozšířené verze](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271). To znamená, že uživatelé již nebudou moci používat staré prostředí.

Další informace o prostředí rozšířené informace o zabezpečení najdete v naší [dokumentaci pro správu](../authentication/concept-registration-mfa-sspr-combined.md) a v [dokumentaci k uživateli](../user-help/security-info-setup-signin.md).

#### <a name="to-turn-on-this-new-experience-you-must"></a>Pokud chcete toto nové prostředí zapnout, musíte:

1. Přihlaste se k Azure Portal jako globální správce nebo Správce uživatelů.

2. Přejděte na **Azure Active Directory > nastavení uživatele > spravovat nastavení pro přístupové panely funkce verze Preview**.

3. V části **Uživatelé můžou používat funkce verze Preview pro registraci a správu informací o zabezpečení** , vyberte možnost **vybrané** a pak zvolte skupinu uživatelů nebo zvolte možnost **vše** , pokud chcete tuto funkci zapnout pro všechny uživatele v tenantovi.

4. V části * * Uživatelé můžou používat funkce verze Preview pro registraci a správu zabezpečení * * * * * *, vyberte **žádné**.

5. Uložte nastavení.

    Po uložení nastavení už nebudete mít přístup k původnímu prostředí bezpečnostní informace.

>[!Important]
>Pokud tento postup nedokončíte před 25. září 2019, bude se váš tenant Azure Active Directory automaticky povolit pro vylepšené prostředí. Pokud máte nějaké otázky, kontaktujte nás na adrese registrationpreview@microsoft.com .

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>Požadavky na ověřování pomocí přihlašovacích údajů po přihlášení budou striktně ověřeny.

**Zadejte:** Kategorie změněné **služby funkcí:** ověřování (přihlášení) **schopnost produktu:** standardy

Od 2. září 2019 budou žádosti o ověření pomocí metody POST u standardů protokolu HTTP striktně ověřeny. Konkrétně mezery a dvojité uvozovky (") již nebudou odebrány z hodnot formuláře žádosti. Tyto změny se neočekávají pro přerušení stávajících klientů a pomůžou zajistit, aby se požadavky odeslané do služby Azure AD spolehlivě zpracovávají pokaždé.

Další informace najdete v tématu o [nejnovějších změnách Azure AD](../develop/reference-breaking-changes.md#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored).

---

## <a name="july-2019"></a>Červenec 2019

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Plánování změn: aktualizace služby proxy aplikací pro podporu pouze TLS 1,2

**Zadejte:** Plánování **Kategorie služby Change Service:** **schopnost produktu** proxy aplikace: Access Control

Abychom vám pomohli získat naše nejsilnější šifrování, zahajte omezení přístupu služby proxy aplikací jenom na protokoly TLS 1,2. Toto omezení se zpočátku zavede na zákazníky, kteří už používají protokoly TLS 1,2, takže se jeho dopad neprojeví. Dokončení vyřazení protokolů TLS 1,0 a TLS 1,1 bude dokončeno 31. srpna 2019. Zákazníci, kteří pořád používají TLS 1,0 a TLS 1,1, budou dostávat Pokročilá oznámení o přípravě na tuto změnu.

Chcete-li zachovat připojení ke službě proxy aplikací v rámci této změny, doporučujeme, abyste se ujistili, že jsou kombinace klientských serverů a prohlížeče a serveru aktualizovány tak, aby používaly protokol TLS 1,2. Doporučujeme také, abyste měli všechny systémy klientů používané vašimi zaměstnanci k přístupu k aplikacím, které jsou publikované prostřednictvím služby proxy aplikací.

Další informace najdete v tématu [Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikace v Azure Active Directory](../manage-apps/application-proxy-add-on-premises-application.md).

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Plán změny: aktualizace návrhu přicházejí do Galerie aplikací.

**Zadejte:** Plánování **Kategorie služby Change Service:** **možnost produktu** podnikové aplikace: jednotné přihlašování

Nové změny uživatelského rozhraní přicházejí do návrhu **Přidat z oblasti Galerie** v okně **Přidat aplikaci** . Tyto změny vám pomůžou snadněji najít vaše aplikace, které podporují Automatické zřizování, OpenID Connect, Security Assertion Markup Language (SAML) a jednotné přihlašování (SSO) k heslu.

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Plánování změn: odebrání IP adresy serveru MFA z IP adresy Office 365

**Zadejte:** Plánování **Kategorie služby Change Service:** **schopnost produktu MFA:** zabezpečení identity & ochrana

Odebíráme IP adresu MFA serveru z [webové služby IP adresa a adresa URL sady Office 365](/office365/enterprise/office-365-ip-web-service). Pokud při aktualizaci nastavení brány firewall aktuálně spoléháte na tyto stránky, musíte se ujistit, že máte také seznam IP adres, které jsou popsané v části **požadavky na bránu firewall pro azure Multi-Factor Authentication Server** v článku [začínáme s Azure Multi-Factor Authentication Server](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements) .

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Tokeny jenom pro aplikace teď vyžadují, aby klientská aplikace existovala v tenantovi prostředků.

**Zadejte:** **Kategorie pevné služby:** ověřování (přihlášení) **schopnost produktu:** ověřování uživatelů

26. července 2019 jsme změnili, jak poskytujeme tokeny jenom pro aplikace prostřednictvím [udělení přihlašovacích údajů klienta](../azuread-dev/v1-oauth2-client-creds-grant-flow.md). Dříve mohli aplikace získat tokeny pro volání jiných aplikací bez ohledu na to, jestli klientská aplikace byla v tenantovi. Toto chování jsme aktualizovali tak, aby se prostředky jednoho tenanta, někdy označované jako webové rozhraní API, mohly volat jenom pro klientské aplikace, které existují v tenantovi prostředků.

Pokud se vaše aplikace nenachází v tenantovi prostředků, zobrazí se chybová zpráva s informacemi `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` o tom, jak tento problém vyřešit, musíte vytvořit instanční objekt klientské aplikace v tenantovi pomocí [koncového bodu souhlasu správce](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint) nebo [prostřednictvím PowerShellu](../develop/howto-authenticate-service-principal-powershell.md), který klientovi udělí oprávnění k provozu v rámci tenanta.

Další informace najdete v tématu [co je nového pro ověřování?](../develop/reference-breaking-changes.md#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant).

> [!NOTE]
> Stávající souhlas mezi klientem a rozhraním API se dál nepožaduje. Aplikace by měly stále provádět vlastní kontroly autorizace.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Nové přihlášení ke službě Azure AD pomocí klíčů zabezpečení FIDO2 pro nové heslo

**Zadejte:** Nová **Kategorie služby funkcí:** ověřování (přihlášení) **schopnost produktu:** ověřování uživatelů

Zákazníci Azure AD teď můžou nastavit zásady pro správu FIDO2 bezpečnostních klíčů pro uživatele a skupiny organizace. Koncoví uživatelé také mohou své bezpečnostní klíče zaregistrovat sami, pomocí klíčů se přihlásí ke svým účtům Microsoft na webech, zatímco na zařízeních s podporou FIDO, a také přihlašování ke svým zařízením s Windows 10 připojeným k Azure AD.

Další informace najdete v tématu [Povolení přihlášení bez hesla pro Azure AD (Preview)](../authentication/concept-authentication-passwordless.md) pro informace týkající se správců a [Nastavení bezpečnostních údajů pro použití bezpečnostního klíče (Preview)](../user-help/security-info-setup-security-key.md) pro informace týkající se koncových uživatelů.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Nové federované aplikace dostupné v Aplikace Azure AD galerii – červenec 2019

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu podnikové aplikace **:** integrace třetích stran

V červenci 2019 jsme do Galerie aplikací přidali tyto 18 nových aplikací s podporou federace:

[Ungerboeck software](../saas-apps/ungerboeck-software-tutorial.md), [jasný vzor Omnichannel kontakt Center](../saas-apps/bright-pattern-omnichannel-contact-center-tutorial.md), [chytřejší Nelly](../saas-apps/clever-nelly-tutorial.md), [AcquireIO](../saas-apps/acquireio-tutorial.md), [Looop](https://www.looop.co/schedule-a-demo/), [](../saas-apps/productboard-tutorial.md) [](../saas-apps/abstract-tutorial.md) [](https://twinesocial.com/) [](../saas-apps/hyperanna-tutorial.md) [](../saas-apps/wandera-tutorial.md) [](../saas-apps/jfrog-artifactory-tutorial.md) [](../saas-apps/hype-tutorial.md) [](https://www.flipsnack.com/accounts/sign-in-sso.html) [](../saas-apps/kallidus-tutorial.md) [ &trade; productboard, přístup k aplikaci MS Azure SSO pro Ethidex dodržování předpisů pro Hype](../saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial.md), Flipsnack, abstrakt, [prostoupání](../saas-apps/ascentis-tutorial.md), Wandera, TwineSocial, Kallidus, HyperAnna, PharmID, [WasteWitness i2B](https://pharmid.com/), [JFrog připojit](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), Artifactory

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizace zřizování uživatelských účtů pro tyto nově podporované aplikace SaaS

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu podnikové aplikace **:** monitorování &ch sestav

Nyní můžete automatizovat vytváření, aktualizaci a odstraňování uživatelských účtů pro tyto nově integrované aplikace:

- [Dialpad](../saas-apps/dialpad-provisioning-tutorial.md)

- [Federated Directory](../saas-apps/federated-directory-provisioning-tutorial.md)

- [Figma](../saas-apps/figma-provisioning-tutorial.md)

- [Leapsome](../saas-apps/leapsome-provisioning-tutorial.md)

- [Peakon](../saas-apps/peakon-provisioning-tutorial.md)

- [Smartsheet](../saas-apps/smartsheet-provisioning-tutorial.md)

Další informace o tom, jak lépe zabezpečit organizaci pomocí automatizovaného zřizování uživatelských účtů, najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](../app-provisioning/user-provisioning.md) .

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Nová značka služby Azure AD Domain Services pro skupinu zabezpečení sítě

**Zadejte:** Nová **Kategorie služby funkcí:** Azure AD Domain Services **možnosti produktu:** Azure AD Domain Services

Pokud už vás unavuje správu dlouhých seznamů IP adres a rozsahů, můžete použít novou značku síťové služby **AzureActiveDirectoryDomainServices** ve skupině zabezpečení sítě Azure, která vám pomůžou zabezpečit příchozí provoz do podsítě vaší Azure AD Domain Services virtuální sítě.

Další informace o této nové značce služby najdete v tématu [skupiny zabezpečení sítě pro Azure AD Domain Services](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nové audity zabezpečení pro Azure AD Domain Services (Public Preview)

**Zadejte:** Nová **Kategorie služby funkcí:** Azure AD Domain Services **možnosti produktu:** Azure AD Domain Services

S radostí oznamujeme vydání auditu zabezpečení Azure AD Domain Service do veřejné verze Preview. Auditování zabezpečení pomáhá poskytnout důležitý přehled o vašich ověřovacích službách díky streamování událostí auditu zabezpečení do cílových prostředků, včetně Azure Storage, pracovních prostorů Azure Log Analytics a centra událostí Azure pomocí portálu služby Azure AD Domain Services.

Další informace najdete v tématu [Povolení auditů zabezpečení pro Azure AD Domain Services (Preview)](../../active-directory-domain-services/security-audit-events.md).

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Nové použití metod ověřování & Insights (Public Preview)

**Zadejte:** Nová **Kategorie služby funkcí:** Samoobslužné resetování hesla **produkt:** sledování & vytváření sestav

Nové metody ověřování & sestavy Insights vám můžou porozumět tomu, jak se funkce, jako je Azure AD Multi-Factor Authentication a Samoobslužné resetování hesla, registrují a používají ve vaší organizaci, včetně počtu registrovaných uživatelů pro jednotlivé funkce, jak často se resetování hesla samoobslužné služby používá k resetování hesel a podle toho, jakou metodu resetování dojde.

Další informace najdete v tématu [použití metod ověřování & Insights (Preview)](../authentication/howto-authentication-methods-activity.md).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Pro všechny správce Azure AD jsou k dispozici nové sestavy zabezpečení (Public Preview)

**Zadejte:** Nová **Kategorie služby funkcí:** **schopnost produktu** Identity Protection & ochrana identity zabezpečení

Všichni Správci služby Azure AD teď můžou v horní části existujících sestav zabezpečení vybrat hlavičku, jako je například zpráva **Uživatelé označení příznakem rizika** , abyste mohli začít používat nové prostředí zabezpečení, jak je znázorněno v sestavách **rizikové uživatele** a **rizikových přihlášení** . V průběhu času se všechny sestavy zabezpečení přesunou ze starších verzí do nových verzí a nové sestavy poskytují následující další možnosti:

- Rozšířené filtrování a řazení

- Hromadné akce, jako je například chybějící riziko uživatele

- Potvrzení napadených nebo bezpečných entit

- Stav rizika, který pokrývá: ohrožené, neúspěšné, opravené a potvrzené ohrožení

Další informace najdete v tématu Sestava [rizikových uživatelů](../identity-protection/howto-identity-protection-investigate-risk.md#risky-users) a [sestavy rizikových přihlášení](../identity-protection/howto-identity-protection-investigate-risk.md#risky-sign-ins).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nové audity zabezpečení pro Azure AD Domain Services (Public Preview)

**Zadejte:** Nová **Kategorie služby funkcí:** Azure AD Domain Services **možnosti produktu:** Azure AD Domain Services

S radostí oznamujeme vydání auditu zabezpečení Azure AD Domain Service do veřejné verze Preview. Auditování zabezpečení pomáhá poskytnout důležitý přehled o vašich ověřovacích službách díky streamování událostí auditu zabezpečení do cílových prostředků, včetně Azure Storage, pracovních prostorů Azure Log Analytics a centra událostí Azure pomocí portálu služby Azure AD Domain Services.

Další informace najdete v tématu [Povolení auditů zabezpečení pro Azure AD Domain Services (Preview)](../../active-directory-domain-services/security-audit-events.md).

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Nová federace B2B Direct pomocí SAML/WS-dodávání (Public Preview)

**Zadejte:** Nová **Kategorie služby funkcí:** **funkce produktu B2B:** B2B/B2C

Přímá federace pomáhá lépe pracovat s partnery, jejichž řešení identity spravované IT není Azure AD, a to díky práci se systémy identit, které podporují standardy SAML nebo WS-Fed. Po nastavení přímého federačního vztahu s partnerem může každý nový uživatel typu Host, kterého budete pozvat z této domény, spolupracovat s vámi pomocí stávajícího účtu organizace, takže uživatelské prostředí pro hosty bude bezproblémové.

Další informace najdete v tématu [Přímá federace pomocí AD FS a poskytovatelů třetích stran pro uživatele typu Host (Preview)](../external-identities/direct-federation.md).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizace zřizování uživatelských účtů pro tyto nově podporované aplikace SaaS

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu podnikové aplikace **:** monitorování &ch sestav

Nyní můžete automatizovat vytváření, aktualizaci a odstraňování uživatelských účtů pro tyto nově integrované aplikace:

- [Dialpad](../saas-apps/dialpad-provisioning-tutorial.md)

- [Federated Directory](../saas-apps/federated-directory-provisioning-tutorial.md)

- [Figma](../saas-apps/figma-provisioning-tutorial.md)

- [Leapsome](../saas-apps/leapsome-provisioning-tutorial.md)

- [Peakon](../saas-apps/peakon-provisioning-tutorial.md)

- [Smartsheet](../saas-apps/smartsheet-provisioning-tutorial.md)

Další informace o tom, jak lépe zabezpečit vaši organizaci pomocí automatizovaného zřizování uživatelských účtů, najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Nová kontrolu duplicitních názvů skupin na portálu Azure AD

**Zadejte:** Nová **Kategorie služby funkcí:** **schopnost produktu** správy skupin: spolupráce

Když teď vytváříte nebo aktualizujete název skupiny z portálu Azure AD, provedeme kontrolu a zjistíme, jestli duplikujete existující název skupiny v prostředku. Pokud určíme, že se tento název už používá v jiné skupině, zobrazí se výzva, abyste změnili své jméno.

Další informace najdete v tématu [Správa skupin na portálu Azure AD](./active-directory-groups-create-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context).

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Azure AD teď podporuje statické parametry dotazů v identifikátorech URI odpovědi (přesměrování).

**Zadejte:** Nová **Kategorie služby funkcí:** ověřování (přihlášení) **schopnost produktu:** ověřování uživatelů

Aplikace Azure AD teď můžou registrovat a používat identifikátory URI odpovědi (přesměrování) s parametry statického dotazu (například `https://contoso.com/oauth2?idp=microsoft` ) pro žádosti OAuth 2,0. Parametr statického dotazu podléhá shodě řetězců pro identifikátory URI odpovědi, stejně jako všechny ostatní části identifikátoru URI odpovědi. Pokud neexistuje žádný registrovaný řetězec, který by odpovídal identifikátoru URI přesměrování adresy URL s dekódováním, požadavek se odmítne. Pokud se najde identifikátor URI odpovědi, použije se celý řetězec pro přesměrování uživatele, včetně parametru statického dotazu.

Dynamické identifikátory URI odpovědi jsou stále zakázané, protože představují bezpečnostní riziko a nelze je použít k uchování informací o stavu v rámci žádosti o ověření. Pro účely tohoto účelu použijte `state` parametr.

V současné době se na obrazovkách registrace aplikace Azure Portal pořád zablokují parametry dotazu. Manifest aplikace ale můžete upravit ručně a přidat a otestovat parametry dotazu v aplikaci. Další informace najdete v tématu [co je nového pro ověřování?](../develop/reference-breaking-changes.md#redirect-uris-can-now-contain-query-string-parameters).

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Protokoly aktivit (rozhraní API MS Graph) pro službu Azure AD jsou teď dostupné prostřednictvím rutin PowerShellu.

**Zadejte:** Nová **Kategorie služby funkce:** hlášení **schopnosti produktu:** monitorování & vytváření sestav

S radostí oznamujeme, že protokoly aktivit Azure AD (sestavy auditu a přihlášení) jsou teď dostupné prostřednictvím modulu Azure AD PowerShellu. Dřív jste mohli vytvořit vlastní skripty pomocí koncových bodů MS Graph API a teď jsme tuto možnost rozšířili na rutiny PowerShellu.

Další informace o tom, jak tyto rutiny použít, najdete v tématu [rutiny Azure AD PowerShell pro vytváření sestav](../reports-monitoring/reference-powershell-reporting.md).

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Aktualizované ovládací prvky filtru pro audit a protokoly přihlašování v Azure AD

**Zadejte:** Kategorie změněné **služby funkcí:** hlášení **možností produktu:** monitorování & vytváření sestav

Aktualizovali jsme sestavy protokolu auditu a přihlašování, takže teď můžete použít různé filtry, aniž byste je museli přidávat jako sloupce na obrazovkách sestavy. Navíc teď můžete rozhodnout, kolik filtrů chcete na obrazovce zobrazit. Tyto aktualizace společně usnadňují čtení a větší rozsah vašich potřeb vašich sestav.

Další informace o těchto aktualizacích najdete v tématech [filtrování protokolů auditu](../reports-monitoring/concept-audit-logs.md#filtering-audit-logs) a [filtrování aktivit přihlášení](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities).

---

## <a name="june-2019"></a>Červen 2019

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Nové rozhraní riskDetections API pro Microsoft Graph (Public Preview)

**Zadejte:** Nová **Kategorie služby funkcí:** **schopnost produktu** Identity Protection & ochrana identity zabezpečení

S potěšením oznamujeme, že nové rozhraní riskDetections API pro Microsoft Graph je teď ve verzi Public Preview. Toto nové rozhraní API můžete použít k zobrazení seznamu uživatelů s ochranou identity v organizaci a zjišťování rizik přihlašování. Toto rozhraní API můžete použít také k efektivnějšímu dotazování detekce rizik, včetně podrobností o typu detekce, stavu, úrovni a dalších.

Další informace najdete v [referenční dokumentaci k rozhraní API pro detekci rizik](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – červen 2019

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu podnikové aplikace **:** integrace třetích stran

Do června 2019 jsme do Galerie aplikací přidali tyto 22 nových aplikací s podporou federace:

[Azure AD SAML Toolkit](../saas-apps/saml-toolkit-tutorial.md), [Otsuka Shokai (大塚商会)](../saas-apps/otsuka-shokai-tutorial.md), [ANAQUA](../saas-apps/anaqua-tutorial.md), [klient VPN Azure](https://portal.azure.com/), [ExpenseIn](../saas-apps/expensein-tutorial.md), pomocná [pomocná Nápověda](../saas-apps/helper-helper-tutorial.md), [Costpoint](../saas-apps/costpoint-tutorial.md), [GlobalOne](../saas-apps/globalone-tutorial.md), [Mercedes-Benz In-Car Office](https://me.secure.mercedes-benz.com/), [skore](https://app.justskore.it/), [konzole pro cloudová infrastruktura Oracle](../saas-apps/oracle-cloud-tutorial.md), [CyberArk ověřování SAML](../saas-apps/cyberark-saml-authentication-tutorial.md), [scrible edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](../saas-apps/pandadoc-tutorial.md), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise OS](https://proptimise.co.uk/), [Vtiger CRM (SAML)](../saas-apps/vtiger-crm-saml-tutorial.md), Oracle Access Manager pro Oracle pro E-Business Suite, Oracle Access Manager pro Oracle E-Business Suite, Oracle IDCS pro IDCS PeopleSoft spouštěných místně

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizace zřizování uživatelských účtů pro tyto nově podporované aplikace SaaS

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu podnikové aplikace **:** monitorování &ch sestav

Nyní můžete automatizovat vytváření, aktualizaci a odstraňování uživatelských účtů pro tyto nově integrované aplikace:

- [Zoom](../saas-apps/zoom-provisioning-tutorial.md)

- [Envoy](../saas-apps/envoy-provisioning-tutorial.md)

- [Proxyclick](../saas-apps/proxyclick-provisioning-tutorial.md)

- [4me](../saas-apps/4me-provisioning-tutorial.md)

Další informace o tom, jak lépe zabezpečit organizaci pomocí automatizovaného zřizování uživatelských účtů, najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](../app-provisioning/user-provisioning.md) .

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Zobrazit průběh služby zřizování Azure AD v reálném čase

**Zadejte:** Kategorie změněné **služby funkcí:** **funkce produktu** zřizování aplikací: Správa životního cyklu identit

Aktualizovali jsme prostředí pro zřizování Azure AD tak, aby obsahovalo nový indikátor průběhu, který vám ukáže, jak daleko jste v procesu zřizování uživatelů. Toto aktualizované prostředí obsahuje také informace o počtu uživatelů zřízených během aktuálního cyklu a také o tom, kolik uživatelů bylo zřízeno.

Další informace najdete v tématu o [kontrole stavu zřizování uživatelů](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>Branding společnosti se teď zobrazuje při odhlašování a chybových obrazovkách.

**Zadejte:** Kategorie změněné **služby funkcí:** ověřování (přihlášení) **schopnost produktu:** ověřování uživatelů

Službu Azure AD jsme aktualizovali tak, aby se vaše firemní logo zobrazovalo na obrazovkách pro odhlášení a chyb a taky na přihlašovací stránce. Nemusíte nic dělat, abyste tuto funkci zapnuli, Azure AD jednoduše používá prostředky, které jste už nastavili v oblasti **značky společnosti** Azure Portal.

Další informace o nastavení firemního brandingu najdete v tématu [Přidání brandingu na stránky Azure Active Directory vaší organizace](./customize-branding.md).

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Server Azure Multi-Factor Authentication (MFA) už není k dispozici pro nová nasazení.

**Zadejte:** Zastaralá **Kategorie služby:** **schopnost produktu MFA:** zabezpečení identity & ochrana

Od 1. července 2019 už Microsoft nenabídne MFA Server pro nová nasazení. Noví zákazníci, kteří chtějí vyžadovat službu Multi-Factor Authentication ve své organizaci, teď musí používat cloudovou Multi-Factor Authentication služby Azure AD. Zákazníci, kteří si server MFA vyaktivovali před 1. července, se nezmění. Pořád budete moct stáhnout nejnovější verzi, získat budoucí aktualizace a vygenerovat přihlašovací údaje pro aktivaci.

Další informace najdete v tématu [Začínáme s Azure Multi-Factor Authentication Server](../authentication/howto-mfaserver-deploy.md). Další informace o cloudové Multi-Factor Authentication Azure AD najdete v tématu [Plánování nasazení cloudových Multi-Factor Authentication Azure AD](../authentication/howto-mfa-getstarted.md).

---

## <a name="may-2019"></a>Květen 2019

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Změna služby: budoucí podpora pro protokoly TLS 1,2 ve službě proxy aplikací

**Zadejte:** Plánování **Kategorie služby Change Service:** **schopnost produktu** proxy aplikace: Access Control

Abychom zákazníkům poskytli nejlepší šifrování v rámci své třídy, omezujeme přístup pouze na protokoly TLS 1,2 ve službě proxy aplikací. Tato změna se postupně zavádí pro zákazníky, kteří už používají jenom protokoly TLS 1,2, takže byste neměli vidět žádné změny.

Vyřazení TLS 1,0 a TLS 1,1 proběhne od 31. srpna 2019, ale budeme poskytovat další pokročilá oznámení, takže budete mít čas na tuto změnu připravovat. Aby se tato změna připravila, ujistěte se, že kombinace mezi klientem a serverem a prohlížečem, včetně všech klientů, které uživatelé používají pro přístup k aplikacím publikovaným pomocí proxy aplikací, se aktualizují tak, aby používaly protokol TLS 1,2 k údržbě připojení ke službě proxy aplikací. Další informace najdete v tématu [Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikace v Azure Active Directory](../manage-apps/application-proxy-add-on-premises-application.md#prerequisites).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Pomocí sestavy využití a přehled můžete zobrazit přihlašovací údaje související s aplikacemi.

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu podnikové aplikace **:** monitorování &ch sestav

Teď můžete použít sestavu využití a přehledů, která se nachází v oblasti **podnikové aplikace** Azure Portal, a získat tak pohled na vaše přihlašovací údaje zaměřené na aplikaci, včetně informací o:

- Nejpoužívanější aplikace používané pro vaši organizaci

- Aplikace s neúspěšnými přihlášeními

- Nejčastější chyby při přihlašování pro každou aplikaci

Další informace o této funkci najdete v tématu [Sestava využití a přehledů na portálu Azure Active Directory](../reports-monitoring/concept-usage-insights-report.md) .

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatizace zřizování uživatelů pro cloudové aplikace pomocí Azure AD

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu podnikové aplikace **:** monitorování &ch sestav

Podle těchto nových kurzů použijte službu zřizování Azure AD k automatizaci vytváření, odstraňování a aktualizace uživatelských účtů pro následující cloudové aplikace:

- [Vyhovující](../saas-apps/comeet-recruiting-software-provisioning-tutorial.md)

- [DynamicSignal](../saas-apps/dynamic-signal-provisioning-tutorial.md)

- [KeeperSecurity](../saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial.md)

Můžete také postupovat podle tohoto nového [kurzu Dropboxu](../saas-apps/dropboxforbusiness-provisioning-tutorial.md), který poskytuje informace o tom, jak zřídit objekty skupiny.

Další informace o tom, jak lépe zabezpečit organizaci prostřednictvím automatizovaného zřizování uživatelských účtů, najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Skóre Secure identity je teď dostupné ve službě Azure AD (Obecná dostupnost).

**Zadejte:** Nová **Kategorie služby funkcí:** není k dispozici **produkt:** zabezpečení identity & ochrana.

Teď můžete monitorovat a zdokonalovat stav zabezpečení identity pomocí funkce skóre Secure identity ve službě Azure AD. Funkce skóre Secure identity využívá jeden řídicí panel, který vám může pomáhat:

- Objektivně změřte stav zabezpečení identity, a to na základě skóre mezi 1 a 223.

- Plánování vylepšení zabezpečení identity

- Kontrola úspěšnosti vašich vylepšení zabezpečení

Další informace o funkci skóre zabezpečení identity najdete v tématu [co je bezpečné skóre identity v Azure Active Directory?](./identity-secure-score.md).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Nyní je k dispozici nové prostředí Registrace aplikací (Obecná dostupnost).

**Zadejte:** Nová **Kategorie služby funkcí:** ověřování (přihlášení) **schopnost produktu:** vývojové prostředí

Nové prostředí [Registrace aplikací](https://aka.ms/appregistrations) je teď všeobecně dostupné. Toto nové prostředí zahrnuje všechny klíčové funkce, se kterými jste obeznámeni z Azure Portal a portálu pro registraci aplikací a vylepšuje je prostřednictvím:

- **Lepší správa aplikací.** Místo prohlížení aplikací napříč různými portály teď můžete zobrazit všechny aplikace na jednom místě.

- **Zjednodušená registrace aplikace** Z vylepšeného prostředí navigace pro výběr oprávnění přepracované je teď snazší registrovat a spravovat vaše aplikace.

- **Podrobnější informace.** Můžete najít další podrobnosti o vaší aplikaci, včetně příruček pro rychlý Start a dalších.

Další informace najdete v části [Microsoft Identity Platform](../develop/index.yml) a [prostředí registrace aplikací je teď všeobecně dostupné.](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) oznámení blogu

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Nové funkce dostupné v rozhraní API pro rizikové uživatele pro identitu Protection

**Zadejte:** Nová **Kategorie služby funkcí:** **schopnost produktu** Identity Protection & ochrana identity zabezpečení

S radostí oznamujeme, že teď můžete pomocí rozhraní API pro rizikové uživatele načítat historii rizik uživatelů, odvolat rizikové uživatele a potvrdit ohrožení uživatelů. Tato změna vám pomůže efektivněji aktualizovat stav rizika vašich uživatelů a pochopit jejich historii rizik.

Další informace najdete v [referenční dokumentaci k rozhraní API pro rizikové uživatele](/graph/api/resources/riskyuser?view=graph-rest-beta&preserve-view=true).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – květen 2019

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu podnikové aplikace **:** integrace třetích stran

V květnu 2019 jsme do Galerie aplikací přidali tyto 21 nových aplikací s podporou federace:

[Freedcamp](../saas-apps/freedcamp-tutorial.md), [skutečné odkazy](../saas-apps/real-links-tutorial.md), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [Simple Sign](../saas-apps/simple-sign-tutorial.md), [Braze](../saas-apps/braze-tutorial.md), [Replay](../saas-apps/displayr-tutorial.md), [Templafy](../saas-apps/templafy-tutorial.md), [Marketo Sales](https://toutapp.com/login)propojování, [ACLP](../saas-apps/aclp-tutorial.md), [subsystéms](../saas-apps/outsystems-tutorial.md), [META4 Global HR](../saas-apps/meta4-global-hr-tutorial.md), na [pracovišti](../saas-apps/quantum-workplace-tutorial.md), [kobalt](../saas-apps/cobalt-tutorial.md), [webMethods API Cloud](../saas-apps/webmethods-integration-cloud-tutorial.md), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](../saas-apps/whatfix-tutorial.md), [Control](../saas-apps/control-tutorial.md), [JOBHUB](../saas-apps/jobhub-tutorial.md), [NEOGOV](../saas-apps/neogov-tutorial.md), [Foodee](../saas-apps/foodee-tutorial.md), [MyVR](../saas-apps/myvr-tutorial.md)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Vylepšené vytváření a Správa skupin na portálu Azure AD

**Zadejte:** Nová **Kategorie služby funkcí:** **schopnost produktu** správy skupin: spolupráce

Provedli jsme vylepšení prostředí souvisejících se skupinami na portálu Azure AD. Tato vylepšení umožňují správcům lépe spravovat seznamy skupin, seznamy členů a poskytovat další možnosti vytváření.

Mezi vylepšení patří:

- Základní filtrování podle typu členství a typu skupiny.

- Přidání nových sloupců, jako je zdrojová a e-mailová adresa.

- Možnost vícenásobného výběru skupin, členů a seznamů vlastníků pro snadné odstranění.

- Možnost výběru e-mailové adresy a přidání vlastníků během vytváření skupiny.

Další informace najdete v tématu [Vytvoření základní skupiny a přidání členů pomocí Azure Active Directory](./active-directory-groups-create-azure-portal.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Konfigurace zásady pojmenování pro skupiny Office 365 na portálu Azure AD (Obecná dostupnost)

**Zadejte:** Kategorie změněné **služby funkcí:** **schopnost produktu** Správa skupin: spolupráce

Správci teď můžou nakonfigurovat zásady pojmenování pro skupiny Office 365 pomocí portálu Azure AD. Tato změna pomáhá vymáhat konzistentní zásady vytváření názvů pro skupiny Office 365 vytvořené nebo upravované uživateli ve vaší organizaci.

Zásady pojmenování pro skupiny Office 365 můžete nakonfigurovat dvěma různými způsoby:

- Zadejte předpony nebo přípony, které se automaticky přidají do názvu skupiny.

- Nahrajte přizpůsobenou sadu blokovaných slov pro vaši organizaci, která nejsou povolená v názvech skupin (například generální ředitel, výplata, HR).

Další informace najdete v tématu [vymáhání zásady pojmenování pro skupiny Office 365](../enterprise-users/groups-naming-policy.md).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Pro protokoly aktivit Azure AD jsou teď dostupné koncové body rozhraní Microsoft Graph API (Obecná dostupnost).

**Zadejte:** Kategorie změněné **služby funkcí:** hlášení **možností produktu:** monitorování & vytváření sestav

S radostí oznamujeme obecnou dostupnost Microsoft Graph Podpora koncových bodů rozhraní API pro protokoly aktivit služby Azure AD. V této verzi teď můžete používat protokol auditu Azure AD verze 1,0 jak v protokolu, tak i v protokolech rozhraní API pro přihlášení.

Další informace najdete v tématu [Přehled rozhraní API protokolu auditu služby Azure AD](/graph/api/resources/azure-ad-auditlog-overview).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Správci teď můžou používat podmíněný přístup pro kombinovaný proces registrace (Public Preview).

**Zadejte:** Nová **Kategorie služby funkcí:** **možnost produktu** pro podmíněný přístup: zabezpečení identity & ochrana

Správci teď můžou vytvořit zásady podmíněného přístupu pro použití v rámci kombinované registrační stránky. To zahrnuje použití zásad pro povolení registrace v těchto případech:

- Uživatelé jsou v důvěryhodné síti.

- Uživatelé jsou nízkým rizikem při přihlašování.

- Uživatelé jsou na spravovaném zařízení.

- Uživatelé souhlasí s podmínkami použití organizace (podmínky použití).

Další informace o podmíněném přístupu a resetování hesla najdete v [blogovém příspěvku o podmíněném přístupu pro kombinované MFA a registraci resetování hesel služby Azure AD](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348). Další informace o zásadách podmíněného přístupu pro kombinovaný proces registrace najdete v tématu [zásady podmíněného přístupu pro kombinovanou registraci](../authentication/howto-registration-mfa-sspr-combined.md#conditional-access-policies-for-combined-registration). Další informace o funkci Azure AD terms of use najdete v tématu [funkce Azure Active Directory podmínek použití](../conditional-access/terms-of-use.md).

---

## <a name="april-2019"></a>Duben 2019

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>Nové zjišťování analýz Azure AD Threat je teď k dispozici jako součást Azure AD Identity Protection

**Zadejte:** Nová **Kategorie služby funkcí:** Azure AD Identity Protection **Možnosti produktu:** zabezpečení identity & ochrana

Detekce analýzy hrozeb Azure AD je teď k dispozici jako součást aktualizované Azure AD Identity Protection funkce. Tato nová funkce pomáhá indikovat neobvyklou aktivitu uživatelů pro konkrétního uživatele nebo aktivitu, která je konzistentní se známými vzory útoku na základě interních a externích zdrojů analýzy hrozeb Microsoftu.

Další informace o aktualizované verzi Azure AD Identity Protection najdete na blogu o [čtyřech hlavních Azure AD Identity Protection vylepšeních, která jsou teď ve verzi Public Preview](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) , a na [Azure Active Directory Identity Protection (aktualizované)?](../identity-protection/overview-identity-protection.md) předmětu. Další informace o detekci analýzy hrozeb v Azure AD najdete v článku [Azure Active Directory Identity Protection detekce rizik](../identity-protection/concept-identity-protection-risks.md) .

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Služba Azure AD nároking Management je teď dostupná (verze Public Preview).

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu pro řízení identit **:** zásady správného řízení identity

Správa nároků služby Azure AD, která je teď ve verzi Public Preview, pomáhá zákazníkům delegovat správu balíčků přístupu, který definuje, jak můžou zaměstnanci a obchodní partneři požádat o přístup, kteří musí schvalovat a jak dlouho mají přístup. Přístup k balíčkům může spravovat členství v Azure AD a skupinách Office 365, přiřazování rolí v podnikových aplikacích a přiřazování rolí pro weby SharePointu Online. Přečtěte si další informace o správě nároků v tématu [Přehled správy nároků služby Azure AD](../governance/entitlement-management-overview.md). Další informace o šířce funkcí Azure AD Identity Governance, včetně Privileged Identity Management, kontrolách přístupu a podmínek použití, najdete v tématu [co je Azure AD identity governance?](../governance/identity-governance-overview.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Konfigurace zásady pojmenování pro skupiny Office 365 na portálu Azure AD (Public Preview)

**Zadejte:** Nová **Kategorie služby funkcí:** **schopnost produktu** správy skupin: spolupráce

Správci teď můžou nakonfigurovat zásady pojmenování pro skupiny Office 365 pomocí portálu Azure AD. Tato změna pomáhá vymáhat konzistentní zásady vytváření názvů pro skupiny Office 365 vytvořené nebo upravované uživateli ve vaší organizaci.

Zásady pojmenování pro skupiny Office 365 můžete nakonfigurovat dvěma různými způsoby:

- Zadejte předpony nebo přípony, které se automaticky přidají do názvu skupiny.

- Nahrajte přizpůsobenou sadu blokovaných slov pro vaši organizaci, která nejsou povolená v názvech skupin (například generální ředitel, výplata, HR).

Další informace najdete v tématu [vymáhání zásady pojmenování pro skupiny Office 365](../enterprise-users/groups-naming-policy.md).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Protokoly aktivit Azure AD jsou teď dostupné v Azure Monitor (Obecná dostupnost).

**Zadejte:** Nová **Kategorie služby funkce:** hlášení **schopnosti produktu:** monitorování & vytváření sestav

Abychom vám pomohli vyřešit svůj názor na vizualizace pomocí protokolů aktivit Azure AD, zavádíme nové funkce Insights v Log Analytics. Tato funkce vám pomůže získat přehled o prostředcích Azure AD pomocí našich interaktivních šablon nazývaných sešity. Tyto předem připravené sešity můžou poskytovat podrobné informace pro aplikace nebo uživatele a zahrnovat:

- **Přihlášení.** Obsahuje podrobné informace o aplikacích a uživatelích, včetně umístění pro přihlášení, klientského operačního systému nebo klienta a verze prohlížeče a počtu úspěšných nebo neúspěšných přihlášení.

- **Starší verze ověřování a podmíněný přístup.** Obsahuje podrobné informace o aplikacích a uživatelích využívajících starší ověřování, včetně Multi-Factor Authenticationho použití aktivovaných zásadami podmíněného přístupu, aplikacích, které používají zásady podmíněného přístupu a tak dále.

- **Analýza neúspěšných přihlášení.** Pomáhá určit, jestli dochází k chybám přihlášení kvůli akci uživatele, problémům se zásadami nebo vaší infrastruktuře.

- **Vlastní sestavy.** Můžete vytvořit nové nebo upravit existující sešity, které vám pomůžou přizpůsobit funkci Insights pro vaši organizaci.

Další informace najdete v tématu [použití Azure Monitorch sešitů pro sestavy Azure Active Directory](../reports-monitoring/howto-use-azure-monitor-workbooks.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – duben 2019

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu podnikové aplikace **:** integrace třetích stran

V dubnu 2019 jsme do Galerie aplikací přidali tyto 21 nových aplikací s podporou federace:

[SAP Fiori](../saas-apps/sap-fiori-tutorial.md), [HRworks Single Signing](../saas-apps/hrworks-single-sign-on-tutorial.md), [percolate](../saas-apps/percolate-tutorial.md), [MobiControl](../saas-apps/mobicontrol-tutorial.md), [Citrix NetScaler](../saas-apps/citrix-netscaler-tutorial.md), [shibumi](../saas-apps/shibumi-tutorial.md), MileIQ [, PageDNA](../saas-apps/benchling-tutorial.md), [EduBrite](https://mileiq.onelink.me/991934284/7e980085), [](../saas-apps/pagedna-tutorial.md) [RStudio LMS](../saas-apps/edubrite-lms-tutorial.md), AMMS [Connect](../saas-apps/rstudio-connect-tutorial.md), [Mitel](../saas-apps/amms-tutorial.md), [Alibaba Connect](../saas-apps/mitel-connect-tutorial.md), [Certent Cloud (SSO založený na rolích)](../saas-apps/alibaba-cloud-service-role-based-sso-tutorial.md), Sectigo [jmění Management](../saas-apps/certent-equity-management-tutorial.md), [GreenOrbit Certificate Manager](../saas-apps/sectigo-certificate-manager-tutorial.md), [Workgrid](../saas-apps/greenorbit-tutorial.md), [Monday.com](../saas-apps/workgrid-tutorial.md), [SurveyMonkey](../saas-apps/mondaycom-tutorial.md), [Indiggo Enterprise](../saas-apps/surveymonkey-enterprise-tutorial.md), [](https://indiggolead.com/)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Nová možnost četnosti kontrol přístupu a výběr více rolí

**Zadejte:** Nová **Kategorie služby funkcí:** kontroly přístupu **Možnosti produktu:** zásady správného řízení identity

Nové aktualizace v recenzích přístupu Azure AD umožňují:

- Kromě dříve existujících možností týdně, měsíčně, čtvrtletně a ročně můžete změnit četnost kontrol přístupu na **částečně ročně**.

- Při vytváření jedné kontroly přístupu vyberte víc rolí Azure AD a prostředků Azure. V této situaci se všechny role nastavují se stejným nastavením a všichni revidující se budou oznamovat současně.

Další informace o tom, jak vytvořit kontrolu přístupu, najdete v tématu [Vytvoření kontroly přístupu skupin nebo aplikací v kontrolách přístupu k Azure AD](../governance/create-access-review.md).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect se převádějí e-mailové systémy upozornění, odesílají zákazníkům nové e-mailové informace o odesílateli.

**Zadejte:** Kategorie změněné **služby funkcí:** **možnost AD Sync produktu:** platforma

Azure AD Connect právě přechází na naše systémy e-mailových výstrah, které si můžou někteří zákazníci zobrazit jako nového e-mailového odesílatele. Pokud to chcete vyřešit, musíte přidat `azure-noreply@microsoft.com` k povolených vaší organizace nebo nebudete moct nadále přijímat důležité výstrahy ze svých služeb Office 365, Azure nebo služby Sync.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Změny přípony hlavního názvu uživatele (UPN) jsou teď mezi federované domény v Azure AD Connect úspěšné.

**Zadejte:** **Kategorie pevné služby:** **možnost AD Sync produktu:** platforma

Teď můžete úspěšně změnit příponu UPN uživatele z jedné federované domény na jinou federované domény v Azure AD Connect. Tato oprava znamená, že během synchronizačního cyklu nebo při obdržení oznamovacího e-mailu s oznámením, "nelze aktualizovat tento objekt v Azure Active Directory, protože atribut [FederatedUser. UserPrincipalName] není platný. Aktualizujte hodnotu v místních adresářových službách.

Další informace najdete v tématu [řešení chyb během synchronizace](../hybrid/tshoot-connect-sync-errors.md#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Zvýšené zabezpečení pomocí zásad podmíněného přístupu na základě ochrany aplikací ve službě Azure AD (Public Preview)

**Zadejte:** Nová **Kategorie služby funkcí:** **možnost produktu** pro podmíněný přístup: zabezpečení identity & ochrana

Podmíněný přístup na základě ochrany aplikací je teď k dispozici pomocí zásad **vyžadovat ochranu aplikací** . Tato nová zásada pomáhá zvýšit zabezpečení vaší organizace tím, že pomáhá zabránit:

- Uživatelé získají přístup k aplikacím bez Microsoft Intune licence.

- Uživatelé nemůžou získat Microsoft Intune zásady ochrany aplikací.

- Uživatelé získají přístup k aplikacím bez nakonfigurovaného Microsoft Intune zásady ochrany aplikací.

Další informace najdete v tématu [Postup při vyžadování zásad ochrany aplikací pro cloudový přístup k aplikaci pomocí podmíněného přístupu](../conditional-access/app-protection-based-conditional-access.md).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Nová podpora jednotného přihlašování a podmíněného přístupu Azure AD v Microsoft Edge (Public Preview)

**Zadejte:** Nová **Kategorie služby funkcí:** **možnost produktu** pro podmíněný přístup: zabezpečení identity & ochrana

Vylepšili jsme podporu Azure AD pro Microsoft Edge, včetně poskytování nové podpory pro jednotné přihlašování a podmíněný přístup Azure AD. Pokud jste už dřív používali Microsoft Intune Managed Browser, teď můžete místo toho použít Microsoft Edge.

Další informace o nastavení a správě zařízení a aplikací pomocí podmíněného přístupu najdete v tématu [vyžadování spravovaných zařízení pro cloudovou aplikaci přístup s](../conditional-access/require-managed-devices.md) podmíněným přístupem a [vyžadování schválených klientských aplikací pro přístup k cloudovým aplikacím s podmíněným přístupem](../conditional-access/app-based-conditional-access.md). Další informace o správě přístupu pomocí Microsoft Edge se zásadami Microsoft Intune najdete v tématu [Správa přístupu k Internetu pomocí Microsoft Intune prohlížeče chráněného zásadami](/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>Březen 2019

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Architektura prostředí identit a podpora vlastních zásad v Azure Active Directory B2C je teď k dispozici (GA)

**Zadejte:** Nová **Kategorie služby funkcí:** B2C-Consumer Identity Management **Product schopnost:** B2B/B2C

V Azure AD B2C teď můžete vytvářet vlastní zásady, včetně následujících úloh, které jsou podporované v škále a v rámci naší smlouvy SLA pro Azure:

- Vytvoření a nahrání cest uživatelů s vlastním ověřováním pomocí vlastních zásad.

- Popište cesty uživatelů krok za krokem jako výměny mezi poskytovateli deklarací identity.

- Definujte podmíněné větvení v cestě uživatele.

- Transformujte a mapujte deklarace identity pro použití v rozhodnutích a komunikacích v reálném čase.

- Ve svých uživatelských cestách pro ověřování použijte REST API služby s povoleným použitím. Například s poskytovateli e-mailu, CRMs a proprietárními autorizačními systémy.

- Federovat se zprostředkovateli identity, kteří jsou kompatibilní s protokolem OpenIDConnect. Například s více klienty Azure AD, poskytovateli účtů sociálních sítí nebo poskytovateli dvojúrovňového ověřování.

Další informace o vytváření vlastních zásad najdete v tématu [poznámky pro vývojáře pro vlastní zásady v Azure Active Directory B2C](../../active-directory-b2c/custom-policy-developer-notes.md) a čtení [blogového příspěvku Alex Simon, včetně případných studií](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD – březen 2019

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu podnikové aplikace **:** integrace třetích stran

V březnu 2019 jsme do Galerie aplikací přidali tyto 14 nových aplikací s podporou federace:

[ISEC7](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](../saas-apps/eplatform-tutorial.md), [Fulcrum](../saas-apps/fulcrum-tutorial.md), [ExcelityGlobal](../saas-apps/excelityglobal-tutorial.md), [auditování na základě vysvětlení systém](../saas-apps/explanation-based-auditing-system-tutorial.md), [štíhlé](../saas-apps/lean-tutorial.md), [PowerSchool výkonnostní otázky](../saas-apps/powerschool-performance-matters-tutorial.md), [Cinode](https://cinode.com/), Iris v [intranetu](../saas-apps/iris-intranet-tutorial.md), [Empactis](../saas-apps/empactis-tutorial.md), [SmartDraw](../saas-apps/smartdraw-tutorial.md), [Confirmit horizonts](../saas-apps/confirmit-horizons-tutorial.md), [ú](../saas-apps/tas-tutorial.md)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Nové konektory zřizování Zscaler a Atlassian v galerii Azure AD – březen 2019

**Zadejte:** Nová **Kategorie služby funkcí:** zřizování aplikací **– možnost produktu:** integrace třetích stran

Automatizace vytváření, aktualizace a odstraňování uživatelských účtů pro následující aplikace:

[Zscaler](../saas-apps/zscaler-provisioning-tutorial.md), [Zscaler Beta](../saas-apps/zscaler-beta-provisioning-tutorial.md), [Zscaler One](../saas-apps/zscaler-one-provisioning-tutorial.md), [Zscaler 2](../saas-apps/zscaler-two-provisioning-tutorial.md), [Zscaler tři](../saas-apps/zscaler-three-provisioning-tutorial.md), [Zscaler ZSCloud](../saas-apps/zscaler-zscloud-provisioning-tutorial.md), Atlassian [Cloud](../saas-apps/atlassian-cloud-provisioning-tutorial.md)

Další informace o tom, jak lépe zabezpečit organizaci prostřednictvím automatizovaného zřizování uživatelských účtů, najdete v tématu [Automatizace zřizování uživatelů při SaaS aplikací pomocí Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Obnovení a Správa odstraněných skupin Office 365 na portálu Azure AD

**Zadejte:** Nová **Kategorie služby funkcí:** **schopnost produktu** správy skupin: spolupráce

Odstraněné skupiny Office 365 teď můžete zobrazit a spravovat z portálu Azure AD. Tato změna vám pomůže zjistit, které skupiny jsou k dispozici pro obnovení, spolu s tím, že vám umožní trvale odstranit všechny skupiny, které nepotřebuje vaše organizace.

Další informace najdete v tématu [obnovení skupin s ukončenou platností nebo odstraněných](../enterprise-users/groups-restore-deleted.md#view-and-manage-the-deleted-microsoft-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Pro místní aplikace zabezpečené službou SAML v Azure AD prostřednictvím proxy aplikací (Public Preview) je teď k dispozici jednotné přihlašování.

**Zadejte:** Nová **Kategorie služby funkcí:** **schopnost produktu** proxy aplikace: Access Control

Teď můžete pro místní aplikace ověřené pomocí SAML a vzdálený přístup k těmto aplikacím prostřednictvím proxy aplikací poskytnout jednotné přihlašování (SSO). Další informace o tom, jak nastavit jednotné přihlašování SAML v místních aplikacích, najdete v tématu [jednotné přihlašování SAML pro místní aplikace s proxy aplikací (Preview)](../manage-apps/application-proxy-configure-single-sign-on-on-premises-apps.md).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Klientské aplikace v cyklech žádostí budou přerušeny, aby se zlepšila spolehlivost a činnost koncového uživatele.

**Zadejte:** Nová **Kategorie služby funkcí:** ověřování (přihlášení) **schopnost produktu:** ověřování uživatelů

Klientské aplikace mohou v krátké době nesprávně vydávat stovky stejných žádostí o přihlášení. Tyto požadavky, ať už jsou úspěšné nebo ne, přispívají ke zhoršení uživatelského prostředí a zvýšenému zatížení pro IDP, zvyšují latenci pro všechny uživatele a snižují dostupnost IDP.

Tato aktualizace pošle `invalid_grant` chybu: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` klientským aplikacím, které vydávají duplicitní žádosti několikrát v krátké době, mimo rozsah běžné operace. Klientské aplikace, které narazí na tento problém, by měly zobrazit interaktivní výzvu a vyžadovat, aby se uživatel znovu přihlásil. Další informace o této změně a o tom, jak opravit aplikaci, pokud dojde k této chybě, najdete v tématu [co je nového pro ověřování?](../develop/reference-breaking-changes.md#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Nové protokoly auditu – uživatelské prostředí teď k dispozici

**Zadejte:** Kategorie změněné **služby funkcí:** hlášení **možností produktu:** monitorování & vytváření sestav

Vytvořili jsme novou stránku **protokolů auditu** Azure AD, které vám pomůžou zlepšit čitelnost a jak hledat vaše informace. Pokud chcete zobrazit stránku nové **protokoly auditu** , vyberte v části **aktivita** v Azure AD možnost **protokoly auditu** .

![Stránka nové protokoly auditu s ukázkovými informacemi](media/whats-new/audit-logs-page.png)

Další informace o nové stránce **protokoly auditu** najdete [v tématu sestavy aktivit auditu na portálu Azure Active Directory](../reports-monitoring/concept-audit-logs.md#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Nová upozornění a pokyny, které vám pomůžou zabránit nechtěnému uzamknutí správce z chybně konfigurovaných zásad podmíněného přístupu

**Zadejte:** Kategorie změněné **služby funkcí:** **možnost produktu** pro podmíněný přístup: zabezpečení identity & ochrana

Abychom správcům zabránili v náhodném uzamykání ze svých klientů prostřednictvím nesprávně nakonfigurovaných zásad podmíněného přístupu, vytvořili jsme nová upozornění a aktualizované pokyny v Azure Portal. Další informace o nových pokynech najdete v tématu [co jsou závislosti služby v Azure Active Directory podmíněný přístup](../conditional-access/service-dependencies.md).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Vylepšené používání funkcí pro koncové uživatele na mobilních zařízeních

**Zadejte:** Kategorie změněné **služby funkcí:** **možnost podmínky použití produktu:** zásad správného řízení

Aktualizovali jsme naše stávající prostředí s využitím, abychom vylepšili, jak si vyzkoušíte používání a souhlasíte s podmínkami použití v mobilním zařízení. Nyní se můžete přiblížit nebo oddálit, přejít zpátky, stáhnout informace a vybrat hypertextové odkazy. Další informace o aktualizovaných podmínek použití najdete v tématu [funkce Azure Active Directory podmínek použití](../conditional-access/terms-of-use.md#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Nové prostředí pro stažení protokolů aktivit služby Azure AD k dispozici

**Zadejte:** Kategorie změněné **služby funkcí:** hlášení **možností produktu:** monitorování & vytváření sestav

Nyní můžete stahovat velké objemy protokolů aktivit přímo z Azure Portal. Tato aktualizace vám umožní:

- Stáhněte si až 250 000 řádků.

- Po dokončení stahování se zobrazí oznámení.

- Přizpůsobte název souboru.

- Určete výstupní formát buď JSON, nebo CSV.

Další informace o této funkci najdete v tématu [rychlý Start: stažení sestavy auditu pomocí Azure Portal](../reports-monitoring/quickstart-download-audit-report.md)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Zásadní změna: aktualizace pro vyhodnocení podmínek pomocí protokolu Exchange ActiveSync (EAS)

**Zadejte:** Plánování **Kategorie služby Change Service:** **schopnost produktu** podmíněného přístupu: Access Control

Právě probíhá aktualizace způsobu, jakým protokol Exchange ActiveSync (EAS) vyhodnocuje následující podmínky:

- Umístění uživatele v závislosti na zemi, oblasti nebo IP adrese

- Riziko přihlášení

- Platforma zařízení

Pokud jste tyto podmínky dříve používali v rámci zásad podmíněného přístupu, mějte na paměti, že chování podmínky se může změnit. Pokud jste například v zásadě používali podmínku umístění uživatele v zásadě, můžete zjistit, že se zásady nyní přeskočí v závislosti na umístění uživatele.

---

## <a name="february-2019"></a>Únor 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Konfigurovatelné šifrování tokenů SAML Azure AD (Public Preview)

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu podnikové aplikace **:** jednotné přihlašování

Teď můžete nakonfigurovat libovolnou podporovanou aplikaci SAML, aby přijímala šifrované tokeny SAML. Při konfiguraci a použití s aplikací Azure AD šifruje emitované kontrolní výrazy SAML pomocí veřejného klíče získaného z certifikátu uloženého v Azure AD.

Další informace o konfiguraci šifrování tokenu SAML najdete v tématu [Konfigurace šifrování tokenů SAML v Azure AD](../manage-apps/howto-saml-token-encryption.md).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Vytvoření kontroly přístupu pro skupiny nebo aplikace pomocí kontrol přístupu Azure AD

**Zadejte:** Nová **Kategorie služby funkcí:** kontroly přístupu **produkt:** zásady správného řízení

Teď můžete do jedné kontroly přístupu Azure AD přidat více skupin nebo aplikací pro členství ve skupině nebo přiřazení aplikace. Kontroly přístupu s více skupinami nebo aplikacemi se nastavují pomocí stejného nastavení a všichni zahrnutí recenzenti se oznamují současně.

Další informace o tom, jak vytvořit kontrolu přístupu pomocí kontrol přístupu ke službě Azure AD, najdete v tématu [Vytvoření kontroly přístupu skupin nebo aplikací v kontrolách přístupu Azure AD](../governance/create-access-review.md) .

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD – únor 2019

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu podnikové aplikace **:** integrace třetích stran

V únoru 2019 jsme do Galerie aplikací přidali tyto 27 nových aplikací s podporou federace:

[Euromonitor Passport](../saas-apps/euromonitor-passport-tutorial.md), [MindTickle](../saas-apps/mindtickle-tutorial.md), [Finger](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [](../saas-apps/airstack-tutorial.md),, [iDrive](../saas-apps/idrive-tutorial.md), [Skyward Qmlativ](../saas-apps/skyward-qmlativ-tutorial.md), [](../saas-apps/knowledge-anywhere-lms-tutorial.md) [Brightidea](../saas-apps/brightidea-tutorial.md), [AlertOps](../saas-apps/alertops-tutorial.md), [Soloinsight-CloudGate SSO](../saas-apps/soloinsight-cloudgate-sso-tutorial.md) [, Click](../saas-apps/oracle-fusion-erp-tutorial.md), [Brandfolder](../saas-apps/brandfolder-tutorial.md), [StoregateSmartFile](../saas-apps/smartfile-tutorial.md), Pexip, [Stormboard, seismických procesů](../saas-apps/stormboard-tutorial.md) [, Bugsnag](../saas-apps/seismic-tutorial.md) [, Periscope,](../saas-apps/clickup-productivity-platform-tutorial.md) [Netop, smartvid.IO](../saas-apps/bugsnag-tutorial.md) [,](../saas-apps/netop-portal-tutorial.md) [PureCloud, Genesys](../saas-apps/pexip-tutorial.md) [,](../saas-apps/purecloud-by-genesys-tutorial.md) [ClickUp,](../saas-apps/smartvid.io-tutorial.md), [pro](https://www.shareadream.org/how-it-works) [integraci](../saas-apps/webmethods-integration-cloud-tutorial.md) [dat](../saas-apps/periscope-data-tutorial.md) [](../saas-apps/ou-campus-tutorial.md)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Rozšířená kombinovaná registrace MFA/SSPR

**Zadejte:** Kategorie změněné **služby funkcí:** Samoobslužné resetování hesla **produkt:** ověřování uživatelů

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

**Zadejte:** Kategorie změněné **služby funkcí:** B2C-Consumer identity Service Management – **funkce produktu:** B2B/B2C

Aktualizovali jsme proces vytváření a správy zásad pro toky uživatelů (dříve označované jako integrované zásady). Toto nové prostředí je teď výchozím nastavením pro všechny klienty Azure AD.

Pomocí ikon úsměvu nebo zamračení v oblasti **poslat zpětnou vazbu** v horní části obrazovky portálu můžete zadat další názory a návrhy.

Další informace o novém prostředí pro správu zásad najdete v tématu [Azure AD B2C nyní má vlastní nastavení JavaScriptu a mnoho dalších nových funkcí](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blogu.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Zvolit konkrétní verze prvků stránky poskytované Azure AD B2C

**Zadejte:** Nová **Kategorie služby funkcí:** B2C-Consumer Identity Management **Product schopnost:** B2B/B2C

Nyní můžete zvolit konkrétní verzi prvků stránky, které poskytuje Azure AD B2C. Výběrem konkrétní verze můžete otestovat své aktualizace, než se zobrazí na stránce, a můžete dosáhnout předvídatelného chování. Kromě toho teď můžete vyjádřit výslovný souhlas s tím, že vynutili konkrétní verze stránek, aby bylo možné přizpůsobit JavaScript. Pokud chcete tuto funkci zapnout, ve vašich uživatelských tocích klikněte na stránku **vlastností** .

Další informace o volbě konkrétní verze prvků stránky najdete v tématu [Azure AD B2C nyní má vlastní nastavení JavaScriptu a mnoho dalších nových funkcí](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blogu.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Konfigurovatelné požadavky na heslo pro koncové uživatele pro B2C (GA)

**Zadejte:** Nová **Kategorie služby funkcí:** B2C-Consumer Identity Management **Product schopnost:** B2B/B2C

Pro koncové uživatele teď můžete nastavit složitost hesla vaší organizace, místo abyste museli používat vaše nativní zásady hesel Azure AD. V okně **vlastností** toků uživatelů (dříve označované jako integrované zásady) můžete zvolit složitost hesla **jednoduchého** nebo **silného**, nebo můžete vytvořit **vlastní** sadu požadavků.

Další informace o konfiguraci požadavků na složitost hesla najdete [v tématu Konfigurace požadavků na složitost pro hesla v Azure Active Directory B2C](../../active-directory-b2c/password-complexity.md).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Nové výchozí šablony pro prostředí pro ověřování vlastních značek

**Zadejte:** Nová **Kategorie služby funkcí:** B2C-Consumer Identity Management **Product schopnost:** B2B/B2C

Můžete použít naše nové výchozí šablony, které jsou umístěné v okně **rozložení stránky** vašich uživatelských toků (dříve označované jako předdefinované zásady), a vytvořit tak pro uživatele vlastní prostředí pro ověřování značkou.

Další informace o používání šablon najdete v tématu [Azure AD B2C nyní má vlastní nastavení JavaScriptu a mnoho dalších nových funkcí](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>Leden 2019

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Spolupráce služby Active Directory B2B s použitím jednorázového ověřování pomocí hesla (Public Preview)

**Zadejte:** Nová **Kategorie služby funkcí:** **funkce produktu B2B:** B2B/B2C

Představili jsme JEDNORÁZOVé ověřování pomocí hesla pro uživatele typu Host B2B, kteří se nemůžou ověřit jiným způsobem jako Azure AD, účet Microsoft (MSA) nebo Google Federation. Tato nová metoda ověřování znamená, že uživatelé typu Host nemusejí vytvářet nové účet Microsoft. Místo toho může uživatel typu Host při uplatnění pozvánky nebo přístupu ke sdílenému prostředku požádat o dočasný kód, který se pošle e-mailové adrese. Pomocí tohoto dočasného kódu se uživatel typu Host může i nadále přihlašovat.

Další informace najdete v tématu [e-mailové ověřování heslem jednorázového hesla (Preview)](../external-identities/one-time-passcode.md) a blogu. [Azure AD zajišťuje bezproblémové sdílení a spolupráci pro všechny uživatele s libovolným účtem](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Nové nastavení souborů cookie pro Azure Proxy aplikací služby AD

**Zadejte:** Nová **Kategorie služby funkcí:** **schopnost produktu** proxy aplikace: Access Control

Zavedli jsme tři nové nastavení souborů cookie, která jsou k dispozici pro vaše aplikace, které jsou publikované prostřednictvím proxy aplikací:

- **Použijte HTTP-Only soubor cookie.** Nastaví příznak **HttpOnly** pro přístup k proxy aplikací a soubory cookie relace. Zapnutím tohoto nastavení získáte další výhody zabezpečení, jako je například pomoc při kopírování a úpravách souborů cookie prostřednictvím skriptování na straně klienta. Pro přidané výhody doporučujeme zapnout tento příznak (vyberte **Ano**).

- **Použijte zabezpečený soubor cookie.** Nastaví **zabezpečený** příznak pro přístup k proxy aplikací a soubory cookie relace. Zapnutím tohoto nastavení získáte další výhody zabezpečení tím, že zajistíte, aby se soubory cookie přenesly přes zabezpečené kanály TLS, jako je například HTTPS. Pro přidané výhody doporučujeme zapnout tento příznak (vyberte **Ano**).

- **Použijte trvalý soubor cookie.** Zabrání vypršení platnosti přístupových souborů cookie, když je webový prohlížeč zavřený. Tyto soubory cookie jsou poslední po dobu životnosti přístupového tokenu. Soubory cookie se ale resetují, pokud je dosažený čas vypršení platnosti nebo pokud uživatel ručně odstraní soubor cookie. Doporučujeme ponechat výchozí nastavení **ne**. Zapněte jenom nastavení pro starší aplikace, které nesdílejí soubory cookie mezi procesy.

Další informace o nových souborech cookie najdete v tématu [nastavení souborů cookie pro přístup k místním aplikacím v Azure Active Directory](../manage-apps/application-proxy-configure-cookie-settings.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Dostupné nové federované aplikace v galerii aplikací Azure AD – leden 2019

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu podnikové aplikace **:** integrace třetích stran

Do ledna 2019 jsme do Galerie aplikací přidali tyto 35 nové aplikace s podporou federace:

[Firstbird](../saas-apps/firstbird-tutorial.md), [Folloze](../saas-apps/folloze-tutorial.md), [talentůová paleta](../saas-apps/talent-palette-tutorial.md), [informační CloudSuite](../saas-apps/infor-cloud-suite-tutorial.md), [Cisco deštník](../saas-apps/cisco-umbrella-tutorial.md), [Zscaler Internet Access Administrator](../saas-apps/zscaler-internet-access-administrator-tutorial.md), [připomenutí vypršení platnosti](../saas-apps/expiration-reminder-tutorial.md), prohlížeč pro [instavování](../saas-apps/instavr-viewer-tutorial.md), [CorpTax](../saas-apps/corptax-tutorial.md), [sloveso](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso Digital Close](../saas-apps/pavaso-digital-close-tutorial.md), [GoodPractice Toolkit](../saas-apps/goodpractice-toolkit-tutorial.md), [Cloud Service Picco](../saas-apps/cloud-service-picco-tutorial.md), [AuditBoard](../saas-apps/auditboard-tutorial.md) [, iProva,](../saas-apps/iprova-tutorial.md) [funkční](../saas-apps/workable-tutorial.md), [CallPlease](https://webapp.callplease.com/create-account/create-account.html), [GTNexus SSO System](../saas-apps/gtnexus-sso-module-tutorial.md), [CBRE ServiceInsight](../saas-apps/cbre-serviceinsight-tutorial.md), Deskradar, [Coralogixv](../saas-apps/coralogix-tutorial.md) [](../saas-apps/acadia-tutorial.md), [Signagelive](../saas-apps/signagelive-tutorial.md), [arů pro Enterprise](../saas-apps/ares-for-enterprise-tutorial.md), [K2 pro Office 365](https://www.k2.com/O365), [](../saas-apps/highgear-tutorial.md) [Xledger](https://www.xledger.net/), [](../saas-apps/visitly-tutorial.md) [iDiD](../saas-apps/idid-manager-tutorial.md) [HighGear, Alp](../saas-apps/deskradar-tutorial.md) [](../saas-apps/korn-ferry-alp-tutorial.md) [](../saas-apps/adoddle-csaas-platform-tutorial.md)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Vylepšení nových Azure AD Identity Protection (verze Public Preview)

**Zadejte:** Kategorie změněné **služby funkcí:** **schopnost produktu** Identity Protection & ochrana identity – zabezpečení

S radostí oznamujeme, že jsme do nabídky Azure AD Identity Protection Public Preview přidali následující vylepšení, včetně těchto:

- Aktualizované a více integrovaných uživatelských rozhraní

- Další rozhraní API

- Vylepšené hodnocení rizik prostřednictvím strojového učení

- Zarovnání celého produktu v rámci rizikových uživatelů a rizikových přihlášení

Další informace o vylepšeních najdete v tématu [co je Azure Active Directory Identity Protection (Aktualizováno)?](../identity-protection/overview-identity-protection.md) k získání dalších informací a ke sdílení nápadů můžete využít výzvy v rámci produktu.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Nová funkce zámku aplikace pro Microsoft Authenticator aplikaci na zařízeních s iOS a Androidem

**Zadejte:** Nová **Kategorie služby funkcí:** Microsoft Authenticator **Možnosti produktu:** zabezpečení identity & ochrana

Chcete-li zachovat jednorázová hesla, informace o aplikaci a nastavení aplikace, můžete zapnout funkci zámku aplikace v aplikaci Microsoft Authenticator. Zapnutí zámku aplikace znamená, že budete požádáni o ověření pomocí PIN kódu nebo biometriky pokaždé, když otevřete aplikaci Microsoft Authenticator.

Další informace najdete v tématu [Nejčastější dotazy k aplikaci Microsoft Authenticator](../user-help/user-help-auth-app-faq.md).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Rozšířené možnosti exportu Azure AD Privileged Identity Management (PIM)

**Zadejte:** Nová **Kategorie služby funkcí:** Privileged Identity Management **možnosti produktu:** Privileged Identity Management

Správci Privileged Identity Management (PIM) teď můžou exportovat všechna aktivní a oprávněná přiřazení rolí pro konkrétní prostředek, který zahrnuje přiřazení rolí pro všechny podřízené prostředky. Dřív bylo obtížné, aby správci získali úplný seznam přiřazení rolí pro předplatné a museli exportovat přiřazení rolí pro každý konkrétní prostředek.

Další informace najdete v tématu [zobrazení historie aktivit a auditu pro role prostředků Azure v PIM](../privileged-identity-management/azure-pim-resource-rbac.md).

---

## <a name="novemberdecember-2018"></a>Listopad/prosinec 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Uživatelé odebrání z oboru synchronizace už nepřešli na účty jenom pro Cloud.

**Zadejte:** **Kategorie pevné služby:** **schopnost produktu** správy uživatelů: adresář

>[!Important]
>Z důvodu této opravy jsme slyšeli a porozuměli vašemu frustrace. Proto jsme tuto změnu vrátili až do doby, kdy ji můžeme pro implementaci ve vaší organizaci usnadnit.

Opravili jsme chybu, ve které by příznak nastavení dirsyncenabled uživatele byl chybně přepnut na **hodnotu false** , pokud byl objekt Active Directory Domain Services (služba AD DS) vyloučen z oboru synchronizace a následně přesunut do koše v Azure AD v následujícím synchronizačním cyklu. V důsledku této opravy platí, že pokud je uživatel vyloučen z rozsahu synchronizace a následně obnoven ze složky Koš služby Azure AD, uživatelský účet zůstane synchronizovaný z místní služby AD, jak očekáváte, a nelze ho spravovat v cloudu, protože jeho zdrojovou autoritou (SoA) zůstane místní služba AD.

Před touto opravou došlo k problému, když byl příznak nastavení dirsyncenabled přepnut na hodnotu false. Došlo k chybnému dojmu o tom, že se tyto účty převedly na objekty pouze cloudu a že je možné spravovat účty v cloudu. Účty ale pořád uchovávají svůj záznam SoA jako místní a všechny synchronizované vlastnosti (atributy stínů) pocházející z místní služby AD. Tento stav způsobilo více problémů v Azure AD a dalších cloudových úlohách (jako je Exchange Online), u kterých se tyto účty očekávaly jako synchronizované z AD, ale teď se chovají stejně jako účty jenom pro Cloud.

V současné době je jediným způsobem, jak skutečně převést účet synchronizovaných z AD na účet jenom pro Cloud, zakázáním DirSync na úrovni tenanta, což spustí back-end operaci přenosu prostředku SoA. Tento typ změny SoA vyžaduje (ale neplatí jenom pro) čištění všech místních atributů (například LastDirSyncTime a stínových atributů) a odesílání signálu do dalších cloudových úloh, aby měl příslušný objekt převedený na účet jenom pro Cloud.

Tato oprava proto brání přímým aktualizacím atributu ImmutableID uživatele synchronizovaného ze služby AD, který v některých scénářích v minulosti byl nutný. V rámci návrhu ImmutableID objektu v Azure AD, jak název naznačuje, má být neměnné. Pro řešení těchto scénářů jsou k dispozici nové funkce implementované v Azure AD Connect Health a klientovi Azure AD Connect synchronizace.

- **ImmutableID aktualizace velkého rozsahu pro mnoho uživatelů v dvoufázovém přístupu**

  Například je třeba provést zdlouhavou migraci služba AD DS mezi doménovými strukturami. Řešení: použijte Azure AD Connect ke **konfiguraci kotvy zdrojového kódu** a, když uživatel migruje, zkopíruje existující hodnoty ImmutableID z Azure AD do atributu ms-DS-Consistency-GUID místního uživatele služba AD DS v nové doménové struktuře. Další informace najdete v tématu [použití MS-DS-ConsistencyGuid jako sourceAnchor](../hybrid/plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor).

- **Rozsáhlé aktualizace ImmutableID pro mnoho uživatelů v jednom snímku**

  Například při implementaci Azure AD Connect uděláte chybu a teď potřebujete změnit atribut SourceAnchor. Řešení: zakažte DirSync na úrovni tenanta a vymažte všechny neplatné hodnoty ImmutableID. Další informace najdete v tématu [vypnutí synchronizace adresářů pro Office 365](/office365/enterprise/turn-off-directory-synchronization).

- **Přesouhlasení místního uživatele s existujícím uživatelem v Azure AD** Například uživatel, který byl znovu vytvořen v služba AD DS vygeneruje duplicitní hodnotu v účtu Azure AD, místo aby se přeshodoval s existujícím účtem služby Azure AD (osamocený objekt). Řešení: k přemapování zdrojového ukotvení/ImmutableID použijte Azure AD Connect Health v Azure Portal. Další informace najdete v tématu [scénář osamoceného objektu](../hybrid/how-to-connect-health-diagnose-sync-errors.md#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Zásadní změna: aktualizace schématu auditu a přihlášení prostřednictvím Azure Monitor

**Zadejte:** Kategorie změněné **služby funkcí:** hlášení **možností produktu:** monitorování & vytváření sestav

V současné době publikujete datové proudy protokolu auditu i přihlašování prostřednictvím Azure Monitor, takže můžete hladce integrovat soubory protokolů pomocí nástrojů pro SIEM nebo pomocí Log Analytics. V závislosti na vaší zpětné vazbě a při přípravě na obecné oznámení o dostupnosti této funkce provádíme v našem schématu následující změny. Tyto změny schématu a související aktualizace dokumentace budou provedeny první týden v lednu.

#### <a name="new-fields-in-the-audit-schema"></a>Nová pole ve schématu auditu
Přidáváme nové pole **typu operace** , které poskytne typ operace prováděné na prostředku. Například **přidejte**, **aktualizujte** nebo **odstraňte**.

#### <a name="changed-fields-in-the-audit-schema"></a>Změněná pole ve schématu auditu
Ve schématu auditu se mění následující pole:

|Název pole|Co se změnilo|Staré hodnoty|Nové hodnoty|
|----------|------------|----------|----------|
|Kategorie|Toto bylo pole **název služby** . Teď je to pole **kategorie auditu** . **Název služby** byl přejmenován na pole **loggedByService** .|<ul><li>Zřizování účtů</li><li>Základní adresář</li><li>Samoobslužné resetování hesla</li></ul>|<ul><li>Správa uživatelů</li><li>Správa skupin</li><li>Správa aplikací</li></ul>|
|targetResources|Zahrnuje **TargetResourceType** na nejvyšší úrovni.|&nbsp;|<ul><li>Zásady</li><li>Aplikace</li><li>Uživatel</li><li>Group (Skupina)</li></ul>|
|loggedByService|Poskytuje název služby, která vygenerovala protokol auditu.|Null|<ul><li>Zřizování účtů</li><li>Základní adresář</li><li>Samoobslužné resetování hesla</li></ul>|
|Výsledek|Poskytuje výsledek protokolů auditu. Dřív byl tento výčet vyhodnocený, ale nyní zobrazujeme skutečnou hodnotu.|<ul><li>0</li><li>1</li></ul>|<ul><li>Success</li><li>Selhání</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Změněná pole ve schématu přihlášení
Ve schématu přihlášení se mění následující pole:

|Název pole|Co se změnilo|Staré hodnoty|Nové hodnoty|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|Toto bylo pole **conditionalaccessPolicies** . Teď je to pole **appliedConditionalAccessPolicies** .|Beze změny|Beze změny|
|conditionalAccessStatus|Poskytuje výsledek stavu zásad podmíněného přístupu při přihlášení. Dřív byl tento výčet vyhodnocený, ale nyní zobrazujeme skutečnou hodnotu.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Success</li><li>Selhání</li><li>Nepoužito</li><li>Zakázáno</li></ul>|
|appliedConditionalAccessPolicies: výsledek|Poskytuje výsledek jednotlivých stavů zásad podmíněného přístupu při přihlášení. Dřív byl tento výčet vyhodnocený, ale nyní zobrazujeme skutečnou hodnotu.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Success</li><li>Selhání</li><li>Nepoužito</li><li>Zakázáno</li></ul>|

Další informace o schématu najdete v tématu [Interpretace schématu protokolu auditu Azure AD v Azure monitor (Preview)](../reports-monitoring/reference-azure-monitor-audit-log-schema.md) .

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Vylepšení Identity Protection pro model strojového učení pod dohledem a modul hodnocení rizik

**Zadejte:** Kategorie změněné **služby funkcí:** **schopnost produktu** Identity Protection: hodnocení rizik

Vylepšení pro uživatele, kteří souvisejí s ochranou identity, a modul hodnocení rizik přihlašování, mohou pomoci zlepšit přesnost a rozsah rizik uživatelů. Správci mohou všimnout, že úroveň rizika uživatele již není přímo propojena s úrovní rizika konkrétních detekcí a že dojde ke zvýšení počtu a úrovně rizik přihlašování.

Detekce rizik se teď vyhodnocuje pomocí modelu strojového učení pod dohledem, který počítá riziko pro uživatele pomocí dalších funkcí přihlášení uživatele a vzoru detekce. Na základě tohoto modelu může správce najít uživatele s vysokým rizikovým skóre, a to i v případě, že zjištění přidružená tomuto uživateli mají nízké nebo střední riziko.

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Správci můžou resetovat svoje vlastní heslo pomocí aplikace Microsoft Authenticator (Public Preview).

**Zadejte:** Kategorie změněné **služby funkcí:** Samoobslužné resetování hesla **produkt:** ověřování uživatelů

Správci Azure AD teď můžou resetovat svoje vlastní heslo pomocí oznámení aplikace Microsoft Authenticator nebo kódu z jakékoli aplikace pro mobilní data nebo hardwarového tokenu. Aby mohli správci resetovat svoje vlastní heslo, bude teď moct použít dvě z následujících metod:

- Oznámení aplikace Microsoft Authenticator

- Jiná aplikace Mobile Authenticator/kód hardwarového tokenu

- E-mail

- Telefonní hovor

- Textová zpráva

Další informace o použití aplikace Microsoft Authenticator k resetování hesel najdete v tématu [Samoobslužné resetování hesla v Azure AD – mobilní aplikace a SSPR (Preview)](../authentication/concept-sspr-howitworks.md#mobile-app-and-sspr) .

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Nová role správce cloudového zařízení služby Azure AD (Public Preview)

**Zadejte:** Nová **Kategorie služby funkcí:** registrace zařízení a **funkce produktu** pro správu: řízení přístupu

Správci můžou k nové roli správce cloudového zařízení přiřadit uživatele, aby mohli provádět úlohy správce cloudového zařízení. Uživatelé s rolí správce cloudových zařízení můžou povolit, zakázat a odstranit zařízení ve službě Azure AD, společně s možnostmi číst klíče Windows 10 BitLocker (pokud jsou k dispozici) v Azure Portal.

Další informace o rolích a oprávněních najdete v tématu [přiřazení rolí správce v Azure Active Directory](../roles/permissions-reference.md) .

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Správa zařízení pomocí časového razítka nové aktivity ve službě Azure AD (Public Preview)

**Zadejte:** Nová **Kategorie služby funkcí:** registrace zařízení a **možnost produktu** pro správu: Správa životního cyklu zařízení

Uvědomujeme si, že v průběhu času musíte aktualizovat a vyřadit zařízení organizace ve službě Azure AD, abyste se vyhnuli používání zastaralých zařízení ve vašem prostředí. Pro pomoc s tímto procesem Azure AD teď aktualizuje vaše zařízení pomocí nového časového razítka aktivity, které vám pomůže se správou životního cyklu zařízení.

Další informace o tom, jak získat a použít toto časové razítko, najdete v tématu [Postupy: Správa zastaralých zařízení v Azure AD.](../devices/manage-stale-devices.md)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Správci můžou od uživatelů vyžadovat, aby na každém zařízení přijali podmínky použití.

**Zadejte:** Nová **Kategorie služby funkcí:** **možnost podmínky použití produktu:** zásad správného řízení

Správci teď můžou zapnout možnost **vyžadovat, aby uživatelé souhlasí na všech možnostech zařízení** , aby vaši uživatelé mohli přijmout vaše podmínky použití na všech zařízeních, která používají ve vašem tenantovi.

Další informace najdete v [části používání podmínek použití funkce Azure Active Directory podmínek použití](../conditional-access/terms-of-use.md#per-device-terms-of-use)v rámci zařízení.

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Správci můžou nakonfigurovat podmínek použití, jejichž platnost vyprší na základě plánu opakování.

**Zadejte:** Nová **Kategorie služby funkcí:** **možnost podmínky použití produktu:** zásad správného řízení


Správci teď můžou zapnout možnost souhlasu s **vypršením platnosti** , aby vyprší platnost podmínek použití pro všechny vaše uživatele na základě zadaného plánu opakování. Plán může být jednou ročně, každý rok, čtvrtletně nebo měsíčně. Po vypršení platnosti podmínek použití musí uživatelé znovu přijmout.

Další informace najdete v [části Přidání podmínek použití funkce Azure Active Directory podmínek použití](../conditional-access/terms-of-use.md#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Správci můžou nakonfigurovat platnost podmínek použití na základě plánu každého uživatele.

**Zadejte:** Nová **Kategorie služby funkcí:** **možnost podmínky použití produktu:** zásad správného řízení

Správci teď můžou zadat dobu, po kterou musí uživatel znovu přijmout podmínky použití. Správci můžou například určit, že uživatelé musí znovu přijmout podmínky použití každých 90 dní.

Další informace najdete v [části Přidání podmínek použití funkce Azure Active Directory podmínek použití](../conditional-access/terms-of-use.md#add-terms-of-use).

---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Nové e-maily Azure AD Privileged Identity Management (PIM) pro Azure Active Directory role

**Zadejte:** Nová **Kategorie služby funkcí:** Privileged Identity Management **možnosti produktu:** Privileged Identity Management

Zákazníci, kteří používají Azure AD Privileged Identity Management (PIM), teď můžou dostávat týdenní e-mailové zprávy o Digest, včetně následujících informací za posledních sedm dní:

- Přehled hlavních přiřazení s nárokem a trvalými rolemi

- Počet uživatelů, kteří aktivují role

- Počet uživatelů přiřazených k rolím v PIM

- Počet uživatelů přiřazených k rolím mimo PIM

- Počet uživatelů, kteří jsou trvalé, v PIM

Další informace o PIM a dostupných e-mailových oznámeních najdete [v tématu e-mailová oznámení v PIM](../privileged-identity-management/pim-email-notifications.md).

---

### <a name="group-based-licensing-is-now-generally-available"></a>Licencování na základě skupin je teď všeobecně dostupné.

**Zadejte:** Kategorie změněné **služby funkcí:** další **schopnost produktu:** adresář

Licencování na základě skupin je ve verzi Public Preview a je teď všeobecně dostupná. V rámci tohoto obecného vydání jsme tuto funkci udělali lépe škálovatelnou a přidali jsme možnost znovu zpracovat přiřazení licencování na základě skupin pro jednoho uživatele a možnost používat licencování na základě skupin s licencemi Office 365 E3/a3.

Další informace o licencování na základě skupin najdete [v tématu Co je licencování na základě skupin v Azure Active Directory?](./active-directory-licensing-whatis-azure-portal.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD – listopad 2018

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu podnikové aplikace **:** integrace třetích stran

V listopadu 2018 jsme do Galerie aplikací přidali tyto 26 nových aplikací s podporou federace:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](../saas-apps/hubspot-tutorial.md), [gettam](../saas-apps/getthere-tutorial.md), [GRA-PE](../saas-apps/grape-tutorial.md), [eHour](https://getehour.com/try-now), [Consent2Go](../saas-apps/consent2go-tutorial.md), [Appinux](../saas-apps/appinux-tutorial.md), [DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [Useall](../saas-apps/useall-tutorial.md), [nekonečné areály](../saas-apps/infinitecampus-tutorial.md), [Alaya](https://alayagood.com), [HEYBUDDY](../saas-apps/heybuddy-tutorial.md), [Wrike SAML](../saas-apps/wrike-tutorial.md), [unášená](../saas-apps/drift-tutorial.md), [Zenegy pro Business Central 365](https://accounting.zenegy.com/), [EverBridge Member Portal](../saas-apps/everbridge-tutorial.md), [ideo](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](../saas-apps/ivanti-service-manager-tutorial.md), [Peakon](../saas-apps/peakon-tutorial.md), [Allbound SSO](../saas-apps/allbound-sso-tutorial.md), [aplikace objektů plex – klasický test](https://test.plexonline.com/signon), [aplikace Plex – klasický](https://www.plexonline.com/signon), [aplikace Plex](https://test.cloud.plex.com/sso)– test pro UX, aplikace [plex – UX,](https://cloud.plex.com/sso)aplikace pro vytváření objektů plex – [IAM,](https://accounts.plex.com/) [řemesla](https://getcrafts.ca/craftsregistration) &

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

## <a name="october-2018"></a>Říjen 2018

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Protokoly Azure AD teď pracují s Azure Log Analytics (Public Preview)

**Zadejte:** Nová **Kategorie služby funkce:** hlášení **schopnosti produktu:** monitorování & vytváření sestav

S radostí oznamujeme, že teď můžete svoje protokoly Azure AD přeslat do Azure Log Analytics! Tato funkce, která je nejvhodnější, vám pomůže zajistit ještě lepší přístup k analýzám vaší firmy, provozu a zabezpečení a také způsob, jak vám pomůže monitorovat infrastrukturu. Další informace najdete v tématu [protokoly aktivit Azure Active Directory v Azure Log Analytics nyní dostupném](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) blogu.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – říjen 2018

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu podnikové aplikace **:** integrace třetích stran

V říjnu 2018 jsme do Galerie aplikací přidali tyto 14 nových aplikací s podporou federace:

[Body ocenění](../saas-apps/myawardpoints-tutorial.md), [Vibe HCM](../saas-apps/vibehcm-tutorial.md), ambyint, [MyWorkDrive](../saas-apps/myworkdrive-tutorial.md), [BorrowBox](../saas-apps/borrowbox-tutorial.md), Dialpad, [ON24 Virtual Environment](../saas-apps/on24-tutorial.md), RingCentral [,](../saas-apps/ringcentral-tutorial.md) [Zscaler tři](../saas-apps/zscaler-three-tutorial.md), [Phraseanet](../saas-apps/phraseanet-tutorial.md), [hodnocení](../saas-apps/appraisd-tutorial.md), Workspot [Control](../saas-apps/workspotcontrol-tutorial.md), [Shuccho Navi](../saas-apps/shucchonavi-tutorial.md), [Glassfrog](../saas-apps/glassfrog-tutorial.md)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="azure-ad-domain-services-email-notifications"></a>Azure AD Domain Services e-mailových oznámení

**Zadejte:** Nová **Kategorie služby funkcí:** Azure AD Domain Services **možnosti produktu:** Azure AD Domain Services

Azure AD Domain Services poskytuje výstrahy na Azure Portal o nepřesných konfiguracích nebo problémech se spravovanou doménou. Tyto výstrahy obsahují podrobné návody, pomocí kterých můžete zkusit problémy vyřešit, aniž byste museli kontaktovat podporu.

Od října budete moct přizpůsobit nastavení oznámení pro spravovanou doménu, takže když dojde k novým výstrahám, pošle se na určenou skupinu lidí e-mail, takže se nemusíte neustále kontrolovat portál, aby se aktualizace zobrazovaly.

Další informace najdete v tématu [Nastavení oznámení v Azure AD Domain Services](../../active-directory-domain-services/notifications.md).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Portál Azure AD podporuje používání doménového rozhraní API ForceDelete k odstraňování vlastních domén.

**Zadejte:** Kategorie změněné **služby funkcí:** **schopnost produktu** Správa adresářů: adresář

S radostí oznamujeme, že teď můžete pomocí rozhraní ForceDelete Domain API odstranit vlastní názvy domén pomocí asynchronního přejmenování odkazů, jako jsou uživatelé, skupiny a aplikace z vlastního názvu domény (contoso.com) zpátky na počáteční výchozí název domény (contoso.onmicrosoft.com).

Tato změna vám pomůže rychleji odstranit vlastní názvy domén, pokud vaše organizace už nepoužívá název, nebo pokud potřebujete název domény použít pro jinou službu Azure AD.

Další informace najdete v tématu [odstranění vlastního názvu domény](../enterprise-users/domains-manage.md#delete-a-custom-domain-name).

---

## <a name="september-2018"></a>Září 2018

### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Aktualizovaná oprávnění role správce pro dynamické skupiny

**Zadejte:** **Kategorie pevné služby:** **schopnost produktu** správy skupin: spolupráce

Opravili jsme problém, takže konkrétní role správce teď můžou vytvářet a aktualizovat dynamická pravidla členství, aniž by museli být vlastníkem skupiny.

Role jsou:

- Globální správce

- Správce Intune

- Správce uživatelů

Další informace najdete v tématu [Vytvoření dynamické skupiny a kontroly stavu](../enterprise-users/groups-create-rule.md) .

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Zjednodušené nastavení konfigurace jednotného Sign-On (SSO) pro některé aplikace třetích stran

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu podnikové aplikace **:** jednotné přihlašování

Uvědomujeme si, že nastavení jednotného Sign-On (SSO) pro aplikace SaaS (software jako služba) může být náročné vzhledem k jedinečné povaze jednotlivých konfigurací aplikací. Sestavili jsme zjednodušené prostředí pro konfiguraci, které automaticky vyplní nastavení konfigurace jednotného přihlašování pro tyto aplikace SaaS třetích stran:

- Zendesk

- ArcGis online

- Jamf Pro

Pokud chcete začít používat toto prostředí jedním kliknutím, přejděte na stránku   >  **konfigurace Azure Portal jednotného přihlašování** pro danou aplikaci. Další informace najdete v tématu [Integrace aplikací SaaS s Azure Active Directory](../saas-apps/tutorial-list.md) .

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Azure Active Directory – kde se vaše data nacházejí? Page

**Zadejte:** Nová **Kategorie služby funkcí:** další funkce **produktu:** GoLocal

Vyberte oblast vaší společnosti z **Azure Active Directory – kde se nachází vaše data na stránce, kde se nachází vaše** datové centrum Azure pro všechny služby Azure AD. Informace můžete filtrovat podle konkrétních služeb Azure AD pro oblast vaší společnosti.

Pro přístup k této funkci a další informace najdete v tématu [Azure Active Directory – kde se nacházejí vaše data](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>K dispozici je nový plán nasazení pro přístupový panel Moje aplikace.

**Zadejte:** Nová **Kategorie služby funkcí:** **funkce produktu** moje aplikace: jednotné přihlašování

Podívejte se na nový plán nasazení, který je k dispozici pro přístupový panel Moje aplikace ( https://aka.ms/deploymentplans) .
Přístupový panel Moje aplikace poskytuje uživatelům jediné místo pro vyhledání a přístup ke svým aplikacím. Tento portál také poskytuje uživatelům možnosti samoobslužných služeb, jako je třeba vyžadování přístupu k aplikacím a skupinám nebo Správa přístupu k těmto prostředkům jménem jiné.

Další informace najdete v tématu [co je to portál moje aplikace?](../user-help/my-apps-portal-end-user-access.md)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Nová karta Poradce při potížích a podpoře na stránce protokoly přihlášení Azure Portal

**Zadejte:** Nová **Kategorie služby funkce:** hlášení **schopnosti produktu:** monitorování & vytváření sestav

Nová karta **Poradce při potížích a podpoře** na stránce pro **přihlášení** Azure Portal má pomoct správcům a pracovníkům podpory řešit problémy související s přihlášeními Azure AD. Tato nová karta poskytuje kód chyby, chybovou zprávu a doporučení pro nápravu (pokud existuje), které vám pomůžou problém vyřešit. Pokud tento problém nemůžete vyřešit, poskytujeme vám také nový způsob vytvoření lístku podpory pomocí prostředí **pro kopírování do schránky** , které vyplní pole s **ID žádosti** a **datem (UTC)** pro soubor protokolu ve vašem lístku podpory.

![Přihlašovací protokoly zobrazující novou kartu](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Rozšířená podpora vlastních vlastností rozšíření používaných k vytváření pravidel dynamického členství

**Zadejte:** Kategorie změněné **služby funkcí:** **schopnost produktu** Správa skupin: spolupráce

V této aktualizaci teď můžete kliknout na odkaz **získat vlastnosti vlastního rozšíření** z Tvůrce pravidla dynamické skupiny uživatelů, zadat jedinečné ID aplikace a získat úplný seznam vlastností vlastního rozšíření, které se použijí při vytváření dynamického pravidla členství pro uživatele. Tento seznam je také možné aktualizovat, aby získal všechny nové vlastnosti rozšíření pro danou aplikaci.

Další informace o použití vlastních vlastností rozšíření pro pravidla dynamického členství najdete v tématu [vlastnosti rozšíření a vlastnosti vlastního rozšíření](../enterprise-users/groups-dynamic-membership.md#extension-properties-and-custom-extension-properties) .

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nové schválené klientské aplikace pro podmíněný přístup na základě aplikace Azure AD

**Zadejte:** Plánování **Kategorie služby Change Service:** **možnost produktu** pro podmíněný přístup: zabezpečení identity a ochrana

V seznamu [schválených klientských aplikací](../conditional-access/concept-conditional-access-conditions.md#client-apps)jsou následující aplikace:

- Microsoft To-Do

- Microsoft Stream

Další informace naleznete v tématu:

- [Podmíněný přístup na základě aplikace Azure AD](../conditional-access/app-based-conditional-access.md)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Nová podpora pro Self-Service resetování hesla z zamykací obrazovky Windows 7/8/8.1

**Zadejte:** Nová **Kategorie služby funkcí:** SSPR **produkt:** ověřování uživatele

Po nastavení této nové funkce se uživatelům zobrazí odkaz pro resetování hesla z **zamykací** obrazovky zařízení se systémem Windows 7, Windows 8 nebo Windows 8.1. Kliknutím na tento odkaz uživatel prochází stejným tokem resetování hesla jako ve webovém prohlížeči.

Další informace najdete v tématu [Jak povolit resetování hesla ze systému Windows 7, 8 a 8,1](../authentication/howto-sspr-windows.md) .

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Upozornění na změnu: autorizační kódy už nebudou dostupné pro opakované použití.

**Zadejte:** Plán pro změnu **Kategorie služby:** ověřování (přihlášení) **schopnost produktu:** ověřování uživatelů

Od 15. listopadu 2018 přestane Azure AD přijímat dříve použité ověřovací kódy pro aplikace. Tato změna zabezpečení pomáhá přenést službu Azure AD v souladu se specifikací OAuth a vynucuje se v koncových bodech V1 a v2.

Pokud vaše aplikace znovu používá autorizační kódy k získání tokenů pro více prostředků, doporučujeme použít kód k získání obnovovacího tokenu a pak pomocí tohoto aktualizačního tokenu získat další tokeny pro jiné prostředky. Autorizační kódy se dají použít jenom jednou, ale aktualizace tokenů se dají použít víckrát napříč několika prostředky. Aplikace, která se pokusí znovu použít ověřovací kód během toku kódu OAuth, získá invalid_grantou chybu.

U těchto a dalších změn souvisejících s protokoly si prohlédněte [úplný seznam novinek pro ověřování](../develop/reference-breaking-changes.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD – září 2018

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu podnikové aplikace **:** integrace třetích stran

Do září 2018 jsme do Galerie aplikací přidali tyto 16 nových aplikací s podporou federace:

[Uberflip](../saas-apps/uberflip-tutorial.md), [vyhovující náborový software](../saas-apps/comeetrecruitingsoftware-tutorial.md), [Workteam](../saas-apps/workteam-tutorial.md), [ArcGIS Enterprise](../saas-apps/arcgisenterprise-tutorial.md), [Nuclino](../saas-apps/nuclino-tutorial.md), [JDA Cloud](../saas-apps/jdacloud-tutorial.md), [Snowflake](../saas-apps/snowflake-tutorial.md), NavigoCloud, [Figma](../saas-apps/figma-tutorial.md), JOIN.me, [ZephyrSSO](../saas-apps/zephyrsso-tutorial.md), [Silverback](../saas-apps/silverback-tutorial.md), Riverbed Xirrus EasyPass, [Rackspace SSO](../saas-apps/rackspacesso-tutorial.md), Enlyft SSO pro Azure, SurveyMonkey, [svolat](../saas-apps/convene-tutorial.md), dmarcian [](../saas-apps/dmarcian-tutorial.md)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="support-for-additional-claims-transformations-methods"></a>Podpora dalších metod transformace deklarací identity

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu podnikové aplikace **:** jednotné přihlašování

Zavedli jsme nové metody transformace deklarace identity, ToLower () a ToUpper (), které je možné použít na tokeny SAML z **jedné Sign-On konfigurační** stránky založené na SAML.

Další informace najdete v tématu [přizpůsobení deklarací identity vystavených v tokenu SAML pro podnikové aplikace v Azure AD](../develop/active-directory-saml-claims-customization.md) .

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Aktualizované uživatelské rozhraní konfigurace aplikace založené na SAML (Preview)

**Zadejte:** Kategorie změněné **služby funkcí:** **možnost produktu** podnikové aplikace: jednotné přihlašování

Jako součást aktualizovaného uživatelského rozhraní konfigurace aplikace založeného na SAML získáte:

- Aktualizovaný návod pro konfiguraci aplikací založených na SAML.

- Lepší přehled o tom, co v konfiguraci chybí nebo je nesprávné.

- Možnost přidat více e-mailových adres pro oznámení o certifikátu vypršení platnosti.

- Nové metody transformace deklarace identity, ToLower () a ToUpper () a další.

- Způsob, jak nahrát vlastní podpisový certifikát tokenu pro podnikové aplikace.

- Způsob nastavení formátu NameID pro aplikace SAML a způsob nastavení hodnoty NameID jako přípon adresářů.

Pokud chcete zapnout toto aktualizované zobrazení, klikněte na odkaz **vyzkoušet náš nový zážitek** z horní části stránky **jednotného přihlašování** . Další informace najdete v tématu [kurz: Konfigurace jednotného přihlašování založeného na SAML pro aplikaci s Azure Active Directory](../manage-apps/view-applications-portal.md).

---

## <a name="august-2018"></a>Srpen 2018

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Změny Azure Active Directory rozsahy IP adres

**Zadejte:** Plánování **Kategorie služby Change Service:** jiné **Možnosti produktu:** platforma

Zavádíme větší rozsahy IP adres do Azure AD, což znamená, že pokud jste nakonfigurovali rozsahy IP adres Azure AD pro brány firewall, směrovače nebo skupiny zabezpečení sítě, budete je muset aktualizovat. Tuto aktualizaci provádíme, takže nebudete muset znovu měnit konfiguraci rozsahu IP adres brány firewall, směrovače ani skupiny zabezpečení sítě, když Azure AD přidá nové koncové body.

Síťové přenosy se v následujících dvou měsících pohybují do těchto nových rozsahů. Pokud chcete pokračovat v nepřerušované službě, musíte tyto aktualizované hodnoty přidat k IP adresám před 10. září 2018:

- 20.190.128.0/18

- 40.126.0.0/18

Důrazně doporučujeme, abyste staré rozsahy IP adres neodebrali, dokud se veškerý síťový provoz nepřesunul do nových rozsahů. Aktualizace informací o přesunutí a zjištění, kdy můžete odebrat staré rozsahy, najdete v tématu [adresy URL a rozsahy IP adres pro Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Upozornění na změnu: autorizační kódy už nebudou dostupné pro opakované použití.

**Zadejte:** Plán pro změnu **Kategorie služby:** ověřování (přihlášení) **schopnost produktu:** ověřování uživatelů

Od 15. listopadu 2018 přestane Azure AD přijímat dříve použité ověřovací kódy pro aplikace. Tato změna zabezpečení pomáhá přenést službu Azure AD v souladu se specifikací OAuth a vynucuje se v koncových bodech V1 a v2.

Pokud vaše aplikace znovu používá autorizační kódy k získání tokenů pro více prostředků, doporučujeme použít kód k získání obnovovacího tokenu a pak pomocí tohoto aktualizačního tokenu získat další tokeny pro jiné prostředky. Autorizační kódy se dají použít jenom jednou, ale aktualizace tokenů se dají použít víckrát napříč několika prostředky. Aplikace, která se pokusí znovu použít ověřovací kód během toku kódu OAuth, získá invalid_grantou chybu.

U těchto a dalších změn souvisejících s protokoly si prohlédněte [úplný seznam novinek pro ověřování](../develop/reference-breaking-changes.md).

---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Sblížená Správa informací o zabezpečení pro samoobslužné heslo (SSPR) a Multi-Factor Authentication (MFA)

**Zadejte:** Nová **Kategorie služby funkcí:** SSPR **produkt:** ověřování uživatele

Tato nová funkce pomáhá lidem spravovat své bezpečnostní údaje (například telefonní číslo, mobilní aplikace atd.) pro SSPR a MFA v jednom umístění a prostředí. ve srovnání s dřív, kde byla provedena ve dvou různých umístěních.

Toto sblížené prostředí funguje taky pro lidi, kteří používají SSPR nebo MFA. Pokud navíc vaše organizace nevynutila ověřování MFA nebo SSPR, můžou si dál zaregistrovat jakékoli metody zabezpečení MFA nebo SSPR, které vaše organizace povoluje, na portálu moje aplikace.

Toto je veřejná verze Preview. Správci můžou nové prostředí zapnout (Pokud je potřeba) pro vybranou skupinu nebo pro všechny uživatele v tenantovi. Další informace o sblížení prostředí najdete na blogu věnovaném [sbližování zkušeností](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/) .

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Nastavení nových souborů cookie HTTP-Only v aplikacích Azure AD Application proxy

**Zadejte:** Nová **Kategorie služby funkcí:** **schopnost produktu** proxy aplikace: Access Control

Ve vašich aplikacích proxy aplikací se zavolalo nové nastavení s názvem **soubory cookie jen pro http** . Toto nastavení pomáhá zajistit dodatečné zabezpečení zahrnutím příznaku HTTPOnly do hlavičky HTTP Response pro přístup k proxy aplikací a souborů cookie relace, zastavením přístupu k souboru cookie ze skriptu na straně klienta a dalším zabráněním akcím, jako je kopírování nebo úprava souboru cookie. I když jste tento příznak ještě nepoužili, soubory cookie se vždycky šifrují a přenášejí pomocí připojení TLS, aby se chránila před nesprávnými úpravami.

Toto nastavení není kompatibilní s aplikacemi, které používají ovládací prvky ActiveX, jako je třeba Vzdálená plocha. Pokud jste v této situaci, doporučujeme toto nastavení vypnout.

Další informace o nastavení souborů cookie HTTP-Only najdete v tématu [publikování aplikací pomocí Azure proxy aplikací služby AD](../manage-apps/application-proxy-add-on-premises-application.md).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Privileged Identity Management (PIM) pro prostředky Azure podporují typy prostředků skupiny pro správu.

**Zadejte:** Nová **Kategorie služby funkcí:** Privileged Identity Management **možnosti produktu:** Privileged Identity Management

Nastavení aktivace za běhu a přiřazení se teď dají použít u typů prostředků skupiny pro správu, stejně jako jste už u předplatných, skupin prostředků a prostředků (například virtuálních počítačů, App Services a dalších). Kromě toho může kdokoli s rolí, která poskytuje přístup správcům pro skupinu pro správu, zjišťovat a spravovat tyto prostředky v PIM.

Další informace o prostředcích PIM a Azure najdete v tématu [zjišťování a Správa prostředků Azure pomocí Privileged Identity Management](../privileged-identity-management/pim-resource-roles-discover-resources.md)

---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>Přístup k aplikaci (Preview) poskytuje rychlejší přístup k portálu Azure AD.

**Zadejte:** Nová **Kategorie služby funkcí:** Privileged Identity Management **možnosti produktu:** Privileged Identity Management

Když dnes aktivujete roli pomocí PIM, může to trvat déle než 10 minut, než se oprávnění projeví. Pokud se rozhodnete použít přístup k aplikaci, který je aktuálně ve verzi Public Preview, můžou správci získat přístup k portálu Azure AD hned po dokončení žádosti o aktivaci.

Přístup k aplikaci v současné době podporuje jenom prostředí portálu Azure AD a prostředky Azure. Další informace o přístupu PIM a aplikace najdete v tématu [co je Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Nové federované aplikace dostupné v galerii aplikací Azure AD – srpen 2018

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu podnikové aplikace **:** integrace třetích stran

V srpnu 2018 jsme do Galerie aplikací přidali tyto 16 nových aplikací s podporou federace:

[Hornbill](../saas-apps/hornbill-tutorial.md), [bridgeline bez vazby](../saas-apps/bridgelineunbound-tutorial.md), [Omáčk Labs – mobilní a webové testování](../saas-apps/saucelabs-mobileandwebtesting-tutorial.md), [konektor meta sítě](../saas-apps/metanetworksconnector-tutorial.md), [způsob, jakým](../saas-apps/waywedo-tutorial.md)je, [Spotinst](../saas-apps/spotinst-tutorial.md), [prohlavní (podle Inlogik)](../saas-apps/promaster-tutorial.md), SchoolBooking, [4me](../saas-apps/4me-tutorial.md), [dokumentace](../saas-apps/dossier-tutorial.md), [N2F – sestavy výdajů](../saas-apps/n2f-expensereports-tutorial.md), [Comm100 Live chat](../saas-apps/comm100livechat-tutorial.md) [, SafeConnect](../saas-apps/safeconnect-tutorial.md), [ZenQMS](../saas-apps/zenqms-tutorial.md), [eLuminate](../saas-apps/eluminate-tutorial.md), [Dovetale](../saas-apps/dovetale-tutorial.md).

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>Nativní podpora Tableau je teď dostupná v Azure Proxy aplikací služby AD

**Zadejte:** Kategorie změněné **služby funkcí:** **schopnost produktu** proxy aplikace: Access Control

Díky naší aktualizaci z OpenID se připojte k protokolu OAuth 2,0 Code pro náš protokol předběžného ověřování, už nemusíte provádět žádnou další konfiguraci, abyste mohli Tableau použít s proxy aplikací. Tato změna protokolu také pomáhá proxy aplikací lépe podporovat moderní aplikace pomocí pouze přesměrování HTTP, která jsou běžně podporována v jazycích JavaScript a HTML.

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Nová podpora pro přidání Google jako zprostředkovatele identity pro uživatele typu Host B2B v Azure Active Directory (Preview)

**Zadejte:** Nová **Kategorie služby funkcí:** **funkce produktu B2B:** B2B/B2C

Když nastavíte federaci s Google ve vaší organizaci, můžete pozvaní uživatelé Gmail přihlašovat ke sdíleným aplikacím a prostředkům pomocí svého stávajícího účtu Google, aniž byste museli vytvářet osobní účet Microsoft (účty spravované služby) nebo účet Azure AD.

Toto je veřejná verze Preview. Další informace o Google Federation najdete v tématu [Přidání Google jako zprostředkovatele identity pro uživatele typu Host B2B](../external-identities/google-federation.md).

---

## <a name="july-2018"></a>Červenec 2018

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Vylepšení Azure Active Directory e-mailových oznámení

**Zadejte:** Kategorie změněné **služby funkcí:** další **funkce produktu:** Správa životního cyklu identit

Azure Active Directory (Azure AD) – e-maily teď při odeslání z následujících služeb obsahují aktualizovaný návrh a také změny e-mailové adresy odesílatele a zobrazovaného jména odesílatele:

- Kontroly přístupu Azure AD
- Azure AD Connect Health
- Azure AD Identity Protection
- Azure AD Privileged Identity Management
- Oznámení o certifikátu vypršení platnosti podnikové aplikace
- Oznámení služby zřizování podnikových aplikací

E-mailová oznámení budou odeslána z následující e-mailové adresy a zobrazovaného jména:

- E-mailová adresa: azure-noreply@microsoft.com
- Zobrazovaný název: Microsoft Azure

Příklad některých nových návrhů e-mailů a další informace najdete v tématu [e-mailová oznámení v Azure AD PIM](../privileged-identity-management/pim-email-notifications.md).

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Protokoly aktivit Azure AD jsou teď dostupné prostřednictvím Azure Monitor

**Zadejte:** Nová **Kategorie služby funkce:** hlášení **schopnosti produktu:** monitorování & vytváření sestav

Protokoly aktivit Azure AD jsou teď dostupné ve verzi Public Preview pro Azure Monitor (služba Azure pro monitorování v rámci platformy). Azure Monitor nabízí dlouhodobou dobu uchovávání a bezproblémové integrace, kromě těchto vylepšení:

- Dlouhodobé uchovávání směrováním souborů protokolů do vlastního účtu úložiště Azure.

- Bezproblémová integrace SIEM, aniž byste museli psát nebo udržovat vlastní skripty.

- Bezproblémová integrace s vašimi vlastními řešeními, analytickými nástroji nebo řešeními pro správu incidentů.

Další informace o těchto nových funkcích najdete v článku [protokoly aktivit služby Azure AD ve službě Azure monitor Diagnostics jsou teď ve verzi Public Preview](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) a v naší dokumentaci [Azure Active Directory protokoly aktivit v Azure monitor (Preview)](../reports-monitoring/concept-activity-logs-azure-monitor.md).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Informace o podmíněném přístupu přidávané do sestavy přihlášení k Azure AD

**Zadejte:** Nová **Kategorie služby funkcí:** funkce **produktu** pro vytváření sestav: zabezpečení identity & ochrana

Tato aktualizace vám umožní zjistit, které zásady se vyhodnotí, když se uživatel přihlásí společně s výsledkem zásady. Sestava nyní zahrnuje typ klientské aplikace používané uživatelem, takže můžete identifikovat starší verze protokolu. V položkách sestavy se teď dají vyhledat ID korelace, které se dá najít v uživatelské chybové zprávě a dá se použít k identifikaci a řešení potíží se shodným přihlašovacím požadavkem.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Zobrazit starší ověřování prostřednictvím protokolů aktivit přihlášení

**Zadejte:** Nová **Kategorie služby funkce:** hlášení **schopnosti produktu:** monitorování & vytváření sestav

Díky zavedení pole **klientské aplikace** v protokolech aktivit přihlašování zákazníci teď můžou zobrazit uživatele, kteří používají starší verze ověřování. Zákazníci budou mít přístup k těmto informacím pomocí Microsoft Graph rozhraní API pro přihlášení nebo prostřednictvím protokolů aktivit přihlašování na portálu Azure AD, kde můžete pomocí ovládacího prvku **klientská aplikace** filtrovat starší verze ověřování. Další podrobnosti najdete v dokumentaci.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – červenec 2018

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu podnikové aplikace **:** integrace třetích stran

V červenci 2018 jsme do Galerie aplikací přidali tyto 16 nových aplikací s podporou federace:

[Centrum inovace](../saas-apps/innovationhub-tutorial.md), [Leapsome](../saas-apps/leapsome-tutorial.md), [určité jednotné přihlašování pro správce](../saas-apps/certainadminsso-tutorial.md), PSUC fázování, [IPASS SmartConnect](../saas-apps/ipasssmartconnect-tutorial.md), [záznam dění na více koncových matic](../saas-apps/screencast-tutorial.md), PowerSchool sjednocené učebny, [Blažková](../saas-apps/elionboarding-tutorial.md)registrace, [Bomgar vzdálená podpora](../saas-apps/bomgarremotesupport-tutorial.md), [NimbleX](../saas-apps/nimblex-tutorial.md), [Představte webvision](../saas-apps/imagineerwebvision-tutorial.md), [Insight4GRC](../saas-apps/insight4grc-tutorial.md), [SecureW2 JoinNow Connector](../saas-apps/securejoinnow-tutorial.md), [Kanbanize](../saas-apps/kanbanize-tutorial.md), [SmartLPA](../saas-apps/smartlpa-tutorial.md), [dovednosti – základ](../saas-apps/skillsbase-tutorial.md)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Nová integrace aplikací SaaS pro zřizování uživatelů – červenec 2018

**Zadejte:** Nová **Kategorie služby funkcí:** zřizování aplikací **– možnost produktu:** integrace třetích stran

Azure AD umožňuje automatizovat vytváření, údržbu a odebírání identit uživatelů v aplikacích SaaS, jako jsou Dropbox, Salesforce, ServiceNow a další. Pro červenec 2018 jsme přidali podporu zřizování uživatelů pro následující aplikace v galerii aplikací Azure AD:

- [Cisco WebEx](../saas-apps/cisco-webex-provisioning-tutorial.md)

- [Bonusly](../saas-apps/bonusly-provisioning-tutorial.md)

Seznam všech aplikací, které podporují zřizování uživatelů v galerii Azure AD, najdete v tématu [Integrace aplikací SaaS s Azure Active Directory](../saas-apps/tutorial-list.md).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Připojení stavu k synchronizaci – jednodušší způsob, jak opravit osamocené a duplicitní chyby synchronizace atributů

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu AD Connect **:** monitorování & vytváření sestav

Azure AD Connect Health přináší samoobslužnou nápravu, která vám umožní zvýrazňovat a opravovat chyby synchronizace. Tato funkce řeší chyby synchronizace duplicitních atributů a opravuje objekty, které jsou osamocené z Azure AD. Tato diagnóza má následující výhody:

- Zúží chyby synchronizace duplicitních atributů a poskytuje konkrétní opravy.

- Aplikuje opravu vyhrazených scénářů služby Azure AD a řeší chyby v jednom kroku.

- K zapnutí a použití této funkce není nutný žádný upgrade nebo konfigurace.

Další informace najdete v tématu [Diagnostika a náprava chyb synchronizace duplicitních atributů](../hybrid/how-to-connect-health-diagnose-sync-errors.md) .

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Vizuální aktualizace prostředí pro přihlášení k Azure AD a MSA

**Zadejte:** Kategorie změněné **služby funkcí:** **schopnost produktu** Azure AD: ověřování uživatelů

Aktualizovali jsme uživatelské rozhraní pro online služby přihlašovací prostředí Microsoftu, například pro Office 365 a Azure. Tato změna způsobí, že obrazovky budou méně zaplněny a přehlednější. Další informace o této změně najdete v článku o [nadcházejících vylepšeních na blogu věnovaném přihlašovacím zkušenostem služby Azure AD](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/) .

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Nové vydání Azure AD Connect – červenec 2018

**Zadejte:** Kategorie změněné **služby funkcí:** **funkce produktu** zřizování aplikací: Správa životního cyklu identit

Nejnovější vydaná verze Azure AD Connect zahrnuje:

- Opravy chyb a aktualizace podpory

- Obecná dostupnost Ping-Federate integrace

- Aktualizace nejnovějšího klienta SQL 2012

Další informace o této aktualizaci najdete v tématu [Azure AD Connect: Historie vydání verze.](../hybrid/reference-connect-version-history.md)

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>Aktualizace podmínek použití uživatelského rozhraní pro koncové uživatele

**Zadejte:** Kategorie změněné **služby funkcí:** **možnost podmínky použití produktu:** zásad správného řízení

Aktualizujeme řetězec přijetí v uživatelském rozhraní podmínky použití koncového uživatele.

**Aktuální text.** Aby bylo možné získat přístup k prostředkům [tenant], musíte přijmout podmínky použití.<br>**Nový text.** Aby bylo možné získat přístup k prostředku [tenant], je nutné přečíst si podmínek použití.

**Aktuální text:** Zvolíte-li možnost přijmout, znamená to, že souhlasíte se všemi výše uvedenými podmínkami použití.<br>**Nový text:** Kliknutím na přijmout potvrďte, že jste si přečetli a rozumíte podmínky použití.

---

### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>Předávací ověřování podporuje starší protokoly a aplikace

**Zadejte:** Kategorie změněné **služby funkcí:** ověřování (přihlášení) **schopnost produktu:** ověřování uživatelů

Předávací ověřování teď podporuje starší protokoly a aplikace. Následující omezení jsou nyní plně podporovaná:

- Přihlášení uživatelů ke starším klientským aplikacím Office, Office 2010 a Office 2013 bez nutnosti moderního ověřování.

- Přístup k informacím o sdílení kalendáře a volném a zaneprázdněném čase v hybridních prostředích Exchange v Office 2010.

- Přihlášení uživatelů k klientským aplikacím Skypu pro firmy bez nutnosti moderního ověřování.

- Přihlášení uživatelů do prostředí PowerShell verze 1,0.

- Apple Program registrace zařízení (DEP), používá pomocníka s nastavením iOS.

---

### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Sblížená Správa informací o zabezpečení pro Samoobslužné resetování hesla a Multi-Factor Authentication

**Zadejte:** Nová **Kategorie služby funkcí:** SSPR **produkt:** ověřování uživatele

Tato nová funkce umožňuje uživatelům spravovat své bezpečnostní údaje (například telefonní číslo, e-mailovou adresu, mobilní aplikaci atd.) pro Samoobslužné resetování hesla (SSPR) a Multi-Factor Authentication (MFA) v jednom prostředí. Uživatelé už nebudou muset registrovat stejné bezpečnostní údaje pro SSPR a MFA ve dvou různých prostředích. Toto nové prostředí platí také pro uživatele, kteří mají buď SSPR nebo MFA.

Pokud organizace nevynucována registrace MFA nebo SSPR, můžou si uživatelé zaregistrovat svoje bezpečnostní údaje prostřednictvím portálu **Moje aplikace** . Odtud si uživatelé můžou zaregistrovat jakékoli metody, které jsou povolené pro MFA nebo SSPR.

Toto je veřejná verze Preview. Správci můžou nové prostředí zapnout (Pokud je potřeba) pro vybranou skupinu uživatelů nebo všechny uživatele v tenantovi.

---

### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Použití aplikace Microsoft Authenticator k ověření vaší identity při resetování hesla

**Zadejte:** Kategorie změněné **služby funkcí:** SSPR **produkt:** ověřování uživatele

Tato funkce umožňuje neoprávněným správcům ověřovat své identity během obnovování hesla pomocí oznámení nebo kódu z Microsoft Authenticator (nebo jakékoli jiné ověřovací aplikace). Když správci tuto metodu samoobslužného resetování hesla zapnou, uživatelé, kteří si zaregistrují mobilní aplikaci prostřednictvím aka.ms/mfasetup nebo aka.ms/setupsecurityinfo, můžou svou mobilní aplikaci použít jako metodu ověření při resetování hesla.

Oznámení mobilní aplikace lze zapnout pouze v rámci zásady, která vyžaduje dvě metody resetování hesla.

---

## <a name="june-2018"></a>Červen 2018

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Upozornění na změnu: Oprava zabezpečení pro delegovaný tok autorizace pro aplikace s využitím rozhraní API protokolů aktivit Azure AD

**Zadejte:** Plánování **Kategorie služby Change Service:** **funkce produktu** pro sestavy: monitorování & vytváření sestav

Z důvodu našeho silnějšího vynucování zabezpečení jsme museli provést změnu oprávnění pro aplikace, které používají delegovaný tok autorizace pro přístup k [rozhraním API protokolů aktivit služby Azure AD](../reports-monitoring/concept-reporting-api.md). Tato změna se projeví v **26. června 2018**.

Pokud některá z vašich aplikací používá rozhraní API protokolů aktivit Azure AD, zajistěte, aby se aplikace po změně nepřerušila.

**Aktualizace oprávnění aplikace**

1. Přihlaste se k Azure Portal, vyberte **Azure Active Directory** a pak vyberte **Registrace aplikací**.
2. Vyberte aplikaci, která používá rozhraní API protokolů aktivit Azure AD, vyberte **Nastavení**, vyberte **požadovaná oprávnění** a pak vyberte rozhraní **Windows Azure Active Directory** API.
3. V oblasti **delegovaná oprávnění** v okně **Povolit přístup** zaškrtněte políčko u **číst data adresáře** a pak vyberte **Uložit**.
4. Vyberte **udělit oprávnění** a pak vyberte **Ano**.

    >[!Note]
    >Abyste mohli udělit oprávnění k aplikaci, musíte být globálním správcem.

Další informace najdete v části [udělení oprávnění](../reports-monitoring/howto-configure-prerequisites-for-reporting-api.md#grant-permissions) pro přístup k rozhraní API pro vytváření sestav Azure AD.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>Konfigurace nastavení TLS pro připojení ke službám Azure AD pro PCI DSS dodržování předpisů

**Zadejte:** Nová **Kategorie služby funkcí:** není k dispozici **Produktová schopnost:** platforma

TLS (Transport Layer Security) je protokol, který poskytuje ochranu osobních údajů a dat mezi dvěma komunikujícími aplikacemi a jedná se o široce nasazený protokol zabezpečení, který se dnes používá.

[Rada standardů zabezpečení sběrnice PCI](https://www.pcisecuritystandards.org/) zjistila, že je třeba zakázat dřívější verze TLS a SSL (Secure SOCKETS Layer) (SSL), a to v souladu s tím, že zajišťují nové a bezpečnější protokoly aplikací, od **30. června 2018**. Tato změna znamená, že pokud se připojíte ke službám Azure AD a vyžadujete PCI DSS dodržování předpisů, je nutné zakázat TLS 1,0. K dispozici je více verzí protokolu TLS, ale protokol TLS 1,2 je nejnovější verze dostupná pro Azure Active Directory služby. Důrazně doporučujeme přesunovat se přímo na protokol TLS 1,2 pro kombinace klienta i serveru a prohlížeče/serveru.

Neaktuální prohlížeče nemusí podporovat novější verze TLS, jako je například TLS 1,2. Pokud chcete zjistit, které verze TLS podporuje váš prohlížeč, přejděte na web [Qualys SSL Labs](https://www.ssllabs.com/) a klikněte na **testovat prohlížeč**. Doporučujeme upgradovat na nejnovější verzi webového prohlížeče a nejlépe povolit pouze TLS 1,2.

**Povolení protokolu TLS 1,2 podle prohlížeče**

- **Microsoft Edge a Internet Explorer (obě se nastavují pomocí Internet Exploreru)**

    1. Otevřete Internet Explorer, vyberte **nástroje**  >  **Možnosti Internetu**  >  **Upřesnit**.
    2. V oblasti **zabezpečení** vyberte **použít TLS 1,2** a pak vyberte **OK**.
    3. Zavřete všechna okna prohlížeče a restartujte aplikaci Internet Explorer.

- **Google Chrome**

    1. Otevřete Google Chrome, do adresního řádku zadejte *Chrome://Settings/* a stiskněte **ENTER**.
    2. Rozbalte **Rozšířené** možnosti, klikněte na oblast **systému** a vyberte **Otevřít nastavení proxy serveru**.
    3. V poli **Vlastnosti Internetu** vyberte kartu **Upřesnit** , v oblasti **zabezpečení** vyberte možnost **použít TLS 1,2** a pak vyberte **OK**.
    4. Zavřete všechna okna prohlížeče a restartujte Google Chrome.

- **Mozilla Firefox**

    1. Otevřete Firefox, do adresního řádku zadejte *About: config* a potom stiskněte klávesu **ENTER**.
    2. Vyhledejte termín, *TLS* a pak vyberte položku **Security. TLS. Version. Max** .
    3. Nastavte hodnotu na **3** , pokud chcete, aby prohlížeč používal až verzi TLS 1,2, a pak vyberte **OK**.

        >[!NOTE]
        >Firefox verze 60,0 podporuje TLS 1,3, takže můžete také nastavit hodnotu Security. TLS. Version. Max na **4**.

    4. Zavřete všechna okna prohlížeče a restartujte Mozilla Firefox.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – červen 2018

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu podnikové aplikace **:** integrace třetích stran

Do června 2018 jsme do Galerie aplikací přidali tyto 15 nových aplikací s podporou federace:

[Skytap](../saas-apps/skytap-tutorial.md), [Vyrovnávání hudby](../saas-apps/settlingmusic-tutorial.md), obchodní [aplikace s povoleným tokenem SAML 1,1](../saas-apps/saml-tutorial.md), [nálada](../saas-apps/supermood-tutorial.md), automatického [úkolu](../saas-apps/autotaskendpointbackup-tutorial.md), [zálohování koncových bodů](../saas-apps/autotaskendpointbackup-tutorial.md), [Skyhigh sítě](../saas-apps/skyhighnetworks-tutorial.md), Smartway2, [TonicDM](../saas-apps/tonicdm-tutorial.md), [Moconavi](../saas-apps/moconavi-tutorial.md), [Zoho One](../saas-apps/zohoone-tutorial.md), [místní SharePoint](../saas-apps/sharepoint-on-premises-tutorial.md), [předvídat sadu CX](../saas-apps/foreseecxsuite-tutorial.md), [Vidyard](../saas-apps/vidyard-tutorial.md), [ChronicX](../saas-apps/chronicx-tutorial.md)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md). Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Ochrana heslem Azure AD je dostupná ve verzi Public Preview.

**Zadejte:** Nová **Kategorie služby funkcí:** **schopnost produktu** Identity Protection: ověřování uživatelů

Pomocí ochrany heslem v Azure AD můžete eliminovat snadné odhadované heslo z vašeho prostředí. Tato hesla se neodstraňují, což pomáhá snížit riziko napadení útokem typu spreje hesla.

Konkrétně ochrana heslem Azure AD vám pomůže:

- Chraňte účty vaší organizace v Azure AD i ve službě Active Directory (AD) ve Windows serveru.
- Zabrání uživatelům používat hesla na seznamu více než 500 nejčastěji používaných hesel a více než 1 000 000 nahrazování znaků u těchto hesel.
- Spravujte ochranu heslem Azure AD z jednoho místa na portálu Azure AD pro Azure AD i místní Windows Server AD.

Další informace o ochraně heslem Azure AD najdete v tématu [odstranění chybných hesel ve vaší organizaci](../authentication/concept-password-ban-bad.md).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Nová šablona zásad podmíněného přístupu všichni hosté vytvořená během vytváření podmínek použití

**Zadejte:** Nová **Kategorie služby funkcí:** **možnost podmínky použití produktu:** zásad správného řízení

Při vytváření podmínek použití se vytvoří taky nová šablona zásad podmíněného přístupu pro všechny hosty a všechny aplikace. Tato nová šablona zásad aplikuje nově vytvořené podmínky použití, což zjednodušuje proces vytváření a vynucení hostů.

Další informace najdete v tématu [funkce Azure Active Directory podmínky použití](../conditional-access/terms-of-use.md).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Nová šablona zásad podmíněného přístupu vytvořená během vytváření podmínek použití

**Zadejte:** Nová **Kategorie služby funkcí:** **možnost podmínky použití produktu:** zásad správného řízení

Při vytváření podmínek použití je vytvořena také nová šablona zásad podmíněného přístupu "vlastní". Tato nová šablona zásad vám umožní vytvořit podmínky použití a pak hned přejít do okna pro vytvoření zásady podmíněného přístupu, aniž byste museli ručně procházet portálem.

Další informace najdete v tématu [funkce Azure Active Directory podmínky použití](../conditional-access/terms-of-use.md).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-ad-multi-factor-authentication"></a>Nové a komplexní pokyny k nasazení služby Azure AD Multi-Factor Authentication

**Zadejte:** Nová **Kategorie služby funkce:** další **funkce produktu:** zabezpečení identity & ochrana

Vydali jsme nové podrobné pokyny k nasazení služby Azure AD Multi-Factor Authentication (MFA) ve vaší organizaci.

Průvodce nasazením MFA zobrazíte tak, že přejdete do úložiště průvodce [nasazením identity](./active-directory-deployment-plans.md) na GitHubu. Chcete-li poskytnout zpětnou vazbu k průvodcům při nasazení, použijte [formulář zpětné vazby plánu nasazení](https://aka.ms/deploymentplanfeedback). Pokud máte nějaké dotazy týkající se průvodců nasazením, kontaktujte nás na adrese [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Role správy aplikací delegovaných službou Azure AD jsou ve verzi Public Preview.

**Zadejte:** Nová **Kategorie služby funkcí:** **funkce produktu** podnikové aplikace: Access Control

Správci teď můžou delegovat úlohy správy aplikací bez přiřazení role globálního správce. Nové role a možnosti jsou:

- **Nové standardní role správce Azure AD:**

    - **Správce aplikace.** Uděluje možnost spravovat všechny aspekty všech aplikací, včetně registrace, nastavení jednotného přihlašování, přiřazení a Licencování aplikací, nastavení proxy aplikací a souhlasu (s výjimkou prostředků Azure AD).

    - **Správce cloudové aplikace.** Udělí všem schopnostem správce aplikace, s výjimkou proxy aplikací, protože neposkytuje místní přístup.

    - **Vývojář aplikace** Udělí možnost vytvářet registrace aplikací i v případě, že je vypnutá možnost **Povolit uživatelům registraci aplikací** .

- **Vlastnictví (nastavení registrace jednotlivých aplikací a aplikace na podnikové úrovni, podobně jako u procesu vlastnictví skupiny:**

    - **Vlastník registrace aplikace** Umožňuje spravovat všechny aspekty vlastní registrace aplikace, včetně manifestu aplikace a přidání dalších vlastníků.

    - **Vlastník podnikové aplikace** Uděluje možnost spravovat spoustu aspektů vlastněných podnikových aplikací, včetně nastavení jednotného přihlašování, přiřazení aplikací a souhlasu (s výjimkou prostředků Azure AD).

Další informace o veřejné verzi Preview najdete v tématu [role správy delegovaných aplikací Azure AD ve verzi Public Preview.](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) webový. Další informace o rolích a oprávněních najdete v tématu [přiřazení rolí správce v Azure Active Directory](../roles/permissions-reference.md).

---

## <a name="may-2018"></a>Květen 2018

### <a name="expressroute-support-changes"></a>Změny podpory ExpressRoute

**Zadejte:** Plán pro změnu **Kategorie služby:** ověřování (přihlášení) **schopnost produktu:** platforma

Software jako nabídka služeb, jako je například Azure Active Directory (Azure AD), je navržený tak, aby fungoval nejlépe přímo přes Internet, aniž by to vyžadovalo ExpressRoute nebo jiné privátní tunely VPN. V důsledku toho od **1. srpna 2018** zastavíme podporu ExpressRoute pro služby Azure AD s využitím veřejných partnerských vztahů Azure a komunit Azure v partnerském vztahu Microsoftu. Jakékoli služby, na které se tato změna týká, může znamenat, že se provoz Azure AD postupně přesouvá z ExpressRoute na Internet.

I když měníme naši podporu, ví, že stále existují situace, kdy možná budete muset pro svůj ověřovací provoz použít vyhrazenou sadu okruhů. Z tohoto důvodu bude Azure AD dál podporovat omezení rozsahu IP adres na základě klientů pomocí ExpressRoute a služeb, které už jsou v partnerském vztahu Microsoftu, s "jinými komunitními službami Office 365 online". Pokud jsou ovlivněné vaše služby, ale vyžadujete ExpressRoute, musíte provést následující:

- **Pokud pracujete ve veřejném partnerském vztahu Azure.** Přejděte na partnerský vztah Microsoftu a zaregistrujte se do **jiné komunity Office 365 Online Services (12076:5100)** . Další informace o tom, jak přejít z veřejného partnerského vztahu Azure na partnerský vztah Microsoftu, najdete v článku [přesunutí veřejného partnerského vztahu do partnerského vztahu Microsoftu](../../expressroute/how-to-move-peering.md) .

- **Pokud jste v partnerském vztahu Microsoftu.** Zaregistrujte se do **jiné komunity služeb Office 365 online (12076:5100)** . Další informace o požadavcích směrování najdete v [části Podpora komunit protokolu BGP](../../expressroute/expressroute-routing.md#bgp) v článku požadavky na směrování ExpressRoute.

Pokud musíte i nadále používat vyhrazené okruhy, budete muset kontaktovat tým účtu Microsoft o tom, jak získat autorizaci k používání **jiné komunity služeb Office 365 online (12076:5100)** . Prověřená deska spravovaná sadou MS Office ověří, zda potřebujete tyto okruhy, a ujistěte se, že rozumíte technickým dopadům jejich uchování. Neoprávněná předplatná, která se pokoušejí vytvořit filtry tras pro Office 365, obdrží chybovou zprávu.

---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>Microsoft Graph rozhraní API pro scénáře správy pro podmínky použití

**Zadejte:** Nová **Kategorie služby funkcí:** podmínky použití **Možnosti produktu:** vývojové prostředí

Přidali jsme Microsoft Graph rozhraní API pro operace správy podmínek použití Azure AD. Můžete vytvářet, aktualizovat a odstraňovat podmínek použití objektu.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Přidání koncového bodu Azure AD s více klienty jako zprostředkovatele identity v Azure AD B2C

**Zadejte:** Nová **Kategorie služby funkcí:** B2C-Consumer Identity Management **Product schopnost:** B2B/B2C

Pomocí vlastních zásad teď můžete do Azure AD B2C přidat běžný koncový bod Azure AD jako zprostředkovatele identity. To vám umožní mít pro všechny uživatele Azure AD, kteří se přihlásí k vašim aplikacím, jediný bod. Další informace najdete v tématu [Azure Active Directory B2C: povolení přihlášení uživatelů k víceklientskému zprostředkovateli identit Azure AD pomocí vlastních zásad](../../active-directory-b2c/identity-provider-azure-ad-multi-tenant.md).

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Použití interních adres URL k přístupu k aplikacím odkudkoli pomocí našeho přihlašovacího rozšíření moje aplikace a Azure Proxy aplikací služby AD

**Zadejte:** Nová **Kategorie služby funkcí:** **funkce produktu** moje aplikace: jednotné přihlašování

Uživatelé teď můžou k aplikacím přistupovat prostřednictvím interních adres URL i v případě, že jsou mimo podnikovou síť pomocí rozšíření pro zabezpečené přihlášení k aplikacím pro Azure AD. To bude fungovat u všech aplikací, které jste publikovali pomocí Azure Proxy aplikací služby AD, v jakémkoli prohlížeči, který má nainstalované rozšíření prohlížeče přístupového panelu. Funkce přesměrování adresy URL se automaticky povolí po přihlášení uživatele k rozšíření. Rozšíření je k dispozici ke stažení v [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176), [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)a [Firefox](https://go.microsoft.com/fwlink/?linkid=866366).

---

### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory – data v Evropě pro zákazníky v Evropě

**Zadejte:** Nová **Kategorie služby funkcí:** další funkce **produktu:** GoLocal

Zákazníci v Evropě vyžadují, aby jejich data zůstaly v Evropě a nereplikují se mimo Evropská datacentra pro ochranu osobních údajů a Evropské zákony. Tento [článek](./active-directory-data-storage-eu.md) obsahuje podrobné informace o tom, jaké informace o identitě se budou ukládat v rámci Evropy, a poskytuje také podrobnosti o informacích, které se uloží mimo Evropská datová centra.

---

### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Nová integrace aplikací SaaS pro zřizování uživatelů – květen 2018

**Zadejte:** Nová **Kategorie služby funkcí:** zřizování aplikací **– možnost produktu:** integrace třetích stran

Azure AD umožňuje automatizovat vytváření, údržbu a odebírání identit uživatelů v aplikacích SaaS, jako jsou Dropbox, Salesforce, ServiceNow a další. Pro květen 2018 jsme přidali podporu zřizování uživatelů pro následující aplikace v galerii aplikací Azure AD:

- [BlueJeans](../saas-apps/bluejeans-provisioning-tutorial.md)

- [Cornerstone OnDemand](../saas-apps/cornerstone-ondemand-provisioning-tutorial.md)

- [Zendesk](../saas-apps/zendesk-provisioning-tutorial.md)

Seznam všech aplikací, které podporují zřizování uživatelů v galerii Azure AD, najdete v tématu [https://aka.ms/appstutorial](../saas-apps/tutorial-list.md) .

---

### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>Kontroly přístupu skupin a přístupu k aplikacím v Azure AD teď poskytují opakované recenze.

**Zadejte:** Nová **Kategorie služby funkcí:** kontroly přístupu **produkt:** zásady správného řízení

Kontrola přístupu skupin a aplikací je teď obecně dostupná jako součást Azure AD Premium P2.  Správci budou moct nakonfigurovat kontroly přístupu pro členství ve skupinách a přiřazení aplikací tak, aby se automaticky opakovaly v pravidelných intervalech, například měsíčně nebo čtvrtletně.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Protokoly aktivit Azure AD (přihlášení a audit) jsou teď dostupné prostřednictvím MS graphu.

**Zadejte:** Nová **Kategorie služby funkce:** hlášení **schopnosti produktu:** monitorování & vytváření sestav

Protokoly aktivit Azure AD, které obsahují protokoly přihlášení a auditu, jsou teď dostupné prostřednictvím rozhraní Microsoft Graph API. Pro přístup k těmto protokolům jsme nastavili dva koncové body prostřednictvím rozhraní Microsoft Graph API. Pokud chcete začít, podívejte se na naše [dokumenty](../reports-monitoring/concept-reporting-api.md) pro programový přístup k rozhraním API pro generování sestav Azure AD.

---

### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Vylepšení prostředí pro uplatnění B2B a opuštění organizace

**Zadejte:** Nová **Kategorie služby funkcí:** **funkce produktu B2B:** B2B/B2C

**Doba uplatnění:** Po sdílení prostředku s uživatelem typu Host pomocí rozhraní B2B API – nemusíte posílat speciální e-mail s pozvánkou. Ve většině případů může uživatel typu Host získat přístup k prostředku a provede se v prostředí pro vyplacení za běhu. Žádný další vliv v důsledku nezmeškaných e-mailů. Nemusíte už nic dalšího zeptat na uživatele typu Host. po kliknutí na odkaz na toto uplatnění vás systém poslal? ". To znamená, že když SPO použije správce pozvání – cloudové přílohy můžou mít stejnou kanonickou adresu URL pro všechny uživatele – interní a externí – v jakémkoli stavu uplatnění.

**Moderní prostředí pro uplatnění:** Žádná další cílová stránka pro vyplacení obrazovky S prohlášením o zásadách ochrany osobních údajů v organizaci se uživatelům zobrazí moderní možnosti souhlasu, stejně jako u aplikací třetích stran.

**Uživatelé typu Host můžou organizaci opustit:** Jakmile se vztah uživatele s organizací převezme na více, můžou ho opustit. Žádné další volání správce pro pozvání k odebrání se neprovádí, žádné další vyzvednutí lístků podpory.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace – květen 2018

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu podnikové aplikace **:** integrace třetích stran

V květnu 2018 jsme do Galerie aplikací přidali tyto 18 nových aplikací s podporou federace:

[AwardSpring](../saas-apps/awardspring-tutorial.md), Infogix [Data3Sixty se řídí](../saas-apps/manabipocket-tutorial.md), [Yodeck](../saas-apps/infogix-tutorial.md), [Jamf pro](../saas-apps/jamfprosamlconnector-tutorial.md), [KnowledgeOwl](../saas-apps/knowledgeowl-tutorial.md), [ENVI MMIS](../saas-apps/envimmis-tutorial.md), [LaunchDarkly,](../saas-apps/launchdarkly-tutorial.md) [Adobe Captivate](../saas-apps/adobecaptivateprime-tutorial.md), [MONTAGE online](../saas-apps/montageonline-tutorial.md), まなびポケット, OpenReel, [ARC Publishing-SSO](../saas-apps/arc-tutorial.md), [PlanGrid](../saas-apps/plangrid-tutorial.md), [iWellnessNow](../saas-apps/iwellnessnow-tutorial.md), [Proxyclick](../saas-apps/proxyclick-tutorial.md), [riskware](../saas-apps/riskware-tutorial.md), [hejno](../saas-apps/flock-tutorial.md) [, ReviewSnap](../saas-apps/reviewsnap-tutorial.md)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md).

Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Nové podrobné pokyny k nasazení pro Azure Active Directory

**Zadejte:** Nová **Kategorie služby funkce:** další **schopnost produktu:** adresář

Nové, podrobné pokyny k nasazení Azure Active Directory (Azure AD), včetně samoobslužného resetování hesla (SSPR), jednotného přihlašování (SSO), podmíněného přístupu, proxy aplikace, zřizování uživatelů, Active Directory Federation Services (AD FS) (AD FS) k předávacímu ověřování (PTA) a synchronizaci hodnot hash (kosmetice) ve službě AD FS ().

Průvodce nasazením zobrazíte tak, že přejdete do úložiště [Průvodce nasazením identity](./active-directory-deployment-plans.md) na GitHubu. Chcete-li poskytnout zpětnou vazbu k průvodcům při nasazení, použijte [formulář zpětné vazby plánu nasazení](https://aka.ms/deploymentplanfeedback). Pokud máte nějaké dotazy týkající se průvodců nasazením, kontaktujte nás na adrese [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Vyhledávání podnikových aplikací – načtení dalších aplikací

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu podnikové aplikace **:** jednotné přihlašování

Nedaří se vám najít vaše aplikace nebo instanční objekty? Přidali jsme možnost načíst další aplikace v seznamu všechny aplikace v podnikových aplikacích. Ve výchozím nastavení se zobrazuje 20 aplikací. Teď můžete kliknout na **načíst další** a zobrazit další aplikace.

---

### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>Vydaná verze AADConnect obsahuje veřejnou verzi Preview integrace s PingFederate, důležitými aktualizacemi zabezpečení, mnoha opravami chyb a novými skvělými novými nástroji pro řešení potíží.

**Zadejte:** Kategorie změněné **služby funkcí:** funkce produktu AD Connect **:** Správa životního cyklu identit

Vydaná verze AADConnect obsahuje veřejnou verzi Preview integrace s PingFederate, důležitými aktualizacemi zabezpečení, mnoha opravami chyb a novými skvělými novými nástroji pro řešení potíží. Poznámky k verzi najdete [tady](../hybrid/reference-connect-version-history.md).

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Kontroly přístupu ke službě Azure AD: automatické použití

**Zadejte:** Kategorie změněné **služby funkcí:** kontroly přístupu **Možnosti produktu:** zásady správného řízení

Kontroly přístupu skupin a aplikací jsou teď obecně dostupné jako součást Azure AD Premium P2. Správce se může nakonfigurovat tak, aby automaticky pro tuto skupinu nebo aplikaci použili změny kontrolora při dokončení kontroly přístupu. Správce může také určit, co se stane s pokračováním přístupu uživatele, pokud kontroloři nereagovali, odebrali přístup, zanechali přístup nebo mají doporučení k systému.

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>Tokeny ID již nelze vracet pomocí response_mode dotazů pro nové aplikace.

**Zadejte:** Kategorie změněné **služby funkcí:** ověřování (přihlášení) **schopnost produktu:** ověřování uživatelů

Aplikace vytvořené od 25. dubna 2018 už nebudou moct požádat o **id_token** pomocí response_mode **dotazů** .  Tím se dokončí služba Azure AD inline se specifikacemi OIDC a pomůže vám omezit plochu pro útoky na vaše aplikace.  Aplikace vytvořené před 25. dubna 2018 nejsou zablokovány pomocí response_mode **dotazů** s response_type **id_token**.  Při žádosti o id_token z Azure AD se vrátila Chyba, je **AADSTS70007: dotaz není podporovaná hodnota response_mode při požadavku na token**.

**Fragment** a **form_post** response_modes nadále fungovat – při vytváření nových aplikačních objektů (například pro využití proxy aplikací) zajistěte použití jednoho z těchto response_modes před vytvořením nové aplikace.

---

## <a name="april-2018"></a>Duben 2018

### <a name="azure-ad-b2c-access-token-are-ga"></a>Přístupový token Azure AD B2C je GA.

**Zadejte:** Nová **Kategorie služby funkcí:** B2C-Consumer Identity Management **Product schopnost:** B2B/B2C

Přístup k webovým rozhraním API se teď dá zabezpečit Azure AD B2C pomocí přístupových tokenů. Funkce se přesouvá z verze Public Preview na GA. Prostředí uživatelského rozhraní pro konfiguraci Azure AD B2Cch aplikací a webových rozhraní API bylo vylepšeno a bylo provedeno další menší vylepšení.

Další informace najdete v tématu [Azure AD B2C: vyžádání přístupových tokenů](../../active-directory-b2c/access-tokens.md).

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Testování konfigurace jednotného přihlašování pro aplikace založené na SAML

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu podnikové aplikace **:** jednotné přihlašování

Při konfiguraci aplikací jednotného přihlašování založeného na SAML budete moci otestovat integraci na stránce konfigurace. Pokud během přihlašování dojde k chybě, můžete zadat chybu v prostředí testování a Azure AD vám poskytne postup řešení pro vyřešení konkrétního problému.

Další informace naleznete v tématu:

- [Konfigurace jednotného přihlašování k aplikacím, které nejsou v galerii aplikací Azure Active Directory](../manage-apps/view-applications-portal.md)
- [Jak ladit jednotné přihlašování založené na SAML pro aplikace v Azure Active Directory](../manage-apps/debug-saml-sso-issues.md)

---

### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Služba Azure AD terms of use teď má vytváření sestav pro jednotlivé uživatele.

**Zadejte:** Nová **Kategorie služby funkcí:** **možnost podmínky použití produktu:** dodržování předpisů

Správci teď můžou vybrat konkrétní podmínky použití a zobrazit všechny uživatele, kteří se k danému podmínky použitíu poslali, a datum a čas, ke kterému došlo.

Další informace najdete v tématu [funkce Azure AD terms of use](../conditional-access/terms-of-use.md).

---

### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: riziková IP adresa pro AD FS uzamknutí ochrany extranetu

**Zadejte:** Nová **Kategorie služby funkce:** další funkce **produktu:** monitorování &ch sestav

Funkce Connect Health teď podporuje detekci IP adres, které překročí prahovou hodnotu neúspěšných přihlášení U/P na každou hodinu nebo každý den. K dispozici jsou tyto funkce:

- Obsáhlá sestava ukazující IP adresu a počet neúspěšných přihlášení vygenerovaných každou hodinu a každý den pomocí přizpůsobitelné prahové hodnoty.
- Výstrahy na základě e-mailu, které ukazují, kdy konkrétní IP adresa překročila prahovou hodnotu neúspěšných přihlášení U/P za hodinu a každý den.
- Možnost stažení, která provede podrobnou analýzu dat

Další informace najdete v tématu [riziková zpráva protokolu IP](../hybrid/how-to-connect-health-adfs.md).

---

### <a name="easy-app-config-with-metadata-file-or-url"></a>Snadná konfigurace aplikace se souborem metadat nebo adresou URL

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu podnikové aplikace **:** jednotné přihlašování

Na stránce podnikové aplikace mohou správci nahrát soubor metadat SAML pro konfiguraci přihlašování založeného na SAML pro aplikace Galerie Azure AD a mimo galerii.

Navíc můžete použít adresu URL federačních metadat služby Azure AD ke konfiguraci jednotného přihlašování s cílovou aplikací.

Další informace najdete v tématu [Konfigurace jednotného přihlašování k aplikacím, které nejsou v galerii aplikací Azure Active Directory](../manage-apps/view-applications-portal.md).

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Služba Azure AD Podmínky použití je teď všeobecně dostupná

**Zadejte:** Nová **Kategorie služby funkcí:** **možnost podmínky použití produktu:** dodržování předpisů


Azure AD terms of use se přesunuly z verze Public Preview na všeobecně dostupné.

Další informace najdete v tématu [funkce Azure AD terms of use](../conditional-access/terms-of-use.md).

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Povolení nebo blokování pozvánek uživatelům B2B z konkrétních organizací

**Zadejte:** Nová **Kategorie služby funkcí:** **funkce produktu B2B:** B2B/B2C


Teď můžete určit, které partnerské organizace chcete sdílet, a spolupracovat s nimi ve spolupráci Azure AD B2B. Chcete-li to provést, můžete zvolit vytvoření seznamu konkrétních domén povolení nebo odepření. Když je doména zablokovaná pomocí těchto možností, zaměstnanci už nebudou moct posílat pozvánky lidem v této doméně.

To vám pomůže řídit přístup k vašim prostředkům a zároveň zajistit hladké prostředí pro schválené uživatele.

Tato funkce spolupráce B2B je dostupná pro všechny Azure Active Directory zákazníky a dá se používat ve spojení s Azure AD Premium funkcemi, jako je podmíněný přístup a ochrana identity, pro přesnější kontrolu nad tím, kdy a jak se externí obchodní uživatelé přihlásí a získají přístup.

Další informace najdete v tématu [Povolení nebo blokování pozvánek uživatelům B2B z konkrétních organizací](../external-identities/allow-deny-list.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu podnikové aplikace **:** integrace třetích stran

V dubnu 2018 jsme do Galerie aplikací přidali tyto 13 nových aplikací s podporou federace:

Kritérium HCM, [FiscalNote](../saas-apps/fiscalnote-tutorial.md), [Secret Server (on-premises)](../saas-apps/secretserver-on-premises-tutorial.md), [dynamický signál](../saas-apps/dynamicsignal-tutorial.md), [mindWireless](../saas-apps/mindwireless-tutorial.md), [organizační diagram nyní](../saas-apps/orgchartnow-tutorial.md), [Ziflow](../saas-apps/ziflow-tutorial.md), [AppNeta Performance Monitor](../saas-apps/appneta-tutorial.md), [Elium](../saas-apps/elium-tutorial.md), [Fluxx Labs](../saas-apps/fluxxlabs-tutorial.md), [Cisco Cloud](../saas-apps/ciscocloud-tutorial.md), Policy, [SafetyNet](../saas-apps/safetynet-tutorial.md)

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md).

Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>Udělení přístupu k místním aplikacím uživatelům B2B v Azure AD (Public Preview)

**Zadejte:** Nová **Kategorie služby funkcí:** **funkce produktu B2B:** B2B/B2C

Jako organizace, která používá funkce spolupráce B2B v Azure Active Directory (Azure AD) k pozvání uživatelů typu host z partnerských organizací do služby Azure AD, teď můžete uživatelům B2B poskytnout přístup k místním aplikacím. Tyto místní aplikace můžou používat ověřování založené na SAML nebo integrované ověřování systému Windows (IWA) s omezeným delegováním protokolu Kerberos (KCD).

Další informace najdete v tématu [udělení přístupu k místním aplikacím uživatelům B2B v Azure AD](../external-identities/hybrid-cloud-to-on-premises.md).

---

### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>Získat kurzy pro integraci jednotného přihlašování z Azure Marketplace

**Zadejte:** Kategorie změněné **služby funkcí:** další **funkce produktu:** integrace třetích stran

Pokud aplikace uvedená v [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) podporuje jednotné přihlašování založené na SAML, kliknutím na **získat teď** získáte kurz integrace přidružený k této aplikaci.

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Rychlejší výkon automatického zřizování uživatelů Azure AD pro aplikace SaaS

**Zadejte:** Kategorie změněné **služby funkcí:** **funkce produktu** zřizování aplikací: Integrace třetích stran

Dříve zákazníci, kteří používají Azure Active Directorych konektorů pro zřizování uživatelů pro aplikace SaaS (například Salesforce, ServiceNow a box), můžou mít pomalý výkon, pokud jejich klienti Azure AD uvedli více než 100 000 kombinovaných uživatelů a skupin a používali přiřazení uživatelů a skupin k určení uživatelů, kteří mají být zřízeni.

Od 2. dubna 2018 se do služby zřizování Azure AD nasadila významná vylepšení výkonu, která významně zkracují dobu potřebnou k provádění počátečních synchronizací mezi Azure Active Directory a cílovými SaaS aplikacemi.

V důsledku toho mnoho zákazníků, kteří používali počáteční synchronizaci s aplikacemi, které trvalo mnoho dnů nebo nikdy nedokončili, je teď dokončené do několika minut nebo hodin.

Další informace najdete v tématu [co se stane při zřizování?](../..//active-directory/app-provisioning/how-provisioning-works.md)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Samoobslužné resetování hesla z zamykací obrazovky Windows 10 pro počítače připojené k hybridní službě Azure AD

**Zadejte:** Kategorie změněné **služby funkcí:** Samoobslužné resetování hesla **produkt:** ověřování uživatelů

Aktualizovali jsme funkci Windows 10 SSPR tak, aby zahrnovala podporu pro počítače, které jsou připojené k hybridní službě Azure AD. Tato funkce je k dispozici ve Windows 10 RS4 umožňuje uživatelům resetovat heslo z zamykací obrazovky počítače s Windows 10. Tato funkce může využívat uživatelé, kteří jsou povoleni a registrováni pro Samoobslužné resetování hesla.

Další informace najdete v tématu [resetování hesla Azure AD na přihlašovací obrazovce](../authentication/howto-sspr-windows.md).

---

## <a name="march-2018"></a>Březen 2018

### <a name="certificate-expire-notification"></a>Oznámení o vypršení platnosti certifikátu

**Zadejte:** **Kategorie pevné služby:** **funkce produktu** podnikové aplikace: jednotné přihlašování

Azure AD pošle oznámení, když brzy vyprší platnost certifikátu pro galerii nebo aplikaci mimo galerii.

Někteří uživatelé neobdrželi oznámení pro podnikové aplikace nakonfigurované pro jednotné přihlašování založené na SAML. Tento problém byl vyřešen. Azure AD odesílá oznámení o vypršení platnosti certifikátů do 7, 30 a 60 dnů. Tuto událost můžete zobrazit v protokolech auditu.

Další informace naleznete v tématu:

- [Správa certifikátů pro federované jednotné přihlašování v Azure Active Directory](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
- [Sestavy aktivit auditu na portálu Azure Active Directory](../reports-monitoring/concept-audit-logs.md)

---

### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Poskytovatelé identit na Twitteru a GitHubu v Azure AD B2C

**Zadejte:** Nová **Kategorie služby funkcí:** B2C-Consumer Identity Management **Product schopnost:** B2B/B2C

Nyní můžete do Azure AD B2C přidat Twitter nebo GitHub jako zprostředkovatele identity. Twitter se přesouvá z verze Public Preview na GA. GitHub se uvolňuje ve verzi Public Preview.

Další informace najdete v tématu [co je spolupráce B2B Azure AD?](../external-identities/what-is-b2b.md).

---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Omezení přístupu přes prohlížeč pomocí Intune Managed Browser s podmíněným přístupem založeným na aplikaci Azure AD pro iOS a Android

**Zadejte:** Nová **Kategorie služby funkcí:** **možnost produktu** pro podmíněný přístup: zabezpečení identity & ochrana

**Nyní ve verzi Public Preview!**

**Intune MANAGED Browser jednotné přihlašování:** Vaši zaměstnanci můžou používat jednotné přihlašování v rámci nativních klientů (například Microsoft Outlook) a Intune Managed Browser pro všechny aplikace připojené ke službě Azure AD.

**Intune Managed Browser podpora podmíněného přístupu:** Nyní můžete vyžadovat, aby zaměstnanci používali spravované prohlížeče Intune s využitím zásad podmíněného přístupu na základě aplikace.

Další informace najdete v našem [blogovém příspěvku](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/).

Další informace naleznete v tématu:

- [Nastavení podmíněného přístupu na základě aplikace](../conditional-access/app-based-conditional-access.md)

- [Konfigurace zásad spravovaného prohlížeče](/mem/intune/apps/manage-microsoft-edge)

---

### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>Rutiny proxy aplikací v modulu PowerShell GA

**Zadejte:** Nová **Kategorie služby funkcí:** **schopnost produktu** proxy aplikace: Access Control

Podpora pro rutiny proxy aplikací je teď v modulu PowerShell GA. To vyžaduje, abyste si udrželi přehled o modulech PowerShellu – Pokud se stanete více než rokem, některé rutiny můžou přestat fungovat.

Další informace najdete v tématu [AzureAD](/powershell/module/Azuread/).

---

### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Bezproblémové jednotné přihlašování podporuje nativní klienty Office 365 pomocí neinteraktivního protokolu.

**Zadejte:** Nová **Kategorie služby funkcí:** ověřování (přihlášení) **schopnost produktu:** ověřování uživatelů

Uživatel, který používá nativní klienty Office 365 (verze 16.0.8730. xxxx a vyšší), získá pro vás tiché přihlašování pomocí bezproblémového jednotného přihlašování. Tato podpora je poskytována přidáním neinteraktivního protokolu (WS-Trust) do služby Azure AD.

Další informace najdete v tématu [jak se přihlašuje na nativním klientovi s bezproblémovým PŘIhlašováním](../hybrid/how-to-connect-sso-how-it-works.md#how-does-sign-in-on-a-native-client-with-seamless-sso-work) .

---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>Pokud aplikace odesílá žádosti o přihlášení do koncových bodů tenanta služby Azure AD, získá uživatelům tiché přihlašování s bezproblémové jednotné přihlašování.

**Zadejte:** Nová **Kategorie služby funkcí:** ověřování (přihlášení) **schopnost produktu:** ověřování uživatelů

Uživatelé získají tiché přihlašování s bezproblémové jednotné přihlašování, pokud aplikace (například `https://contoso.sharepoint.com` ) odesílá žádosti o přihlášení koncovým bodům klienta služby Azure AD – to znamená, že je `https://login.microsoftonline.com/contoso.com/<..>` `https://login.microsoftonline.com/<tenant_ID>/<..>` místo společného koncového bodu služby Azure AD ( `https://login.microsoftonline.com/common/<...>` ).

Další informace najdete v tématu [Azure Active Directory bezproblémové jednotné přihlašování](../hybrid/how-to-connect-sso.md).

---

### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>K zavedení bezproblémového jednotného přihlašování je potřeba přidat jenom jednu adresu URL služby Azure AD místo dvou adres URL do nastavení zóny intranetu uživatelů.

**Zadejte:** Nová **Kategorie služby funkcí:** ověřování (přihlášení) **schopnost produktu:** ověřování uživatelů

K zavedení bezproblémového jednotného přihlašování pro uživatele musíte do nastavení zóny intranetu uživatele přidat jenom jednu adresu URL služby Azure AD pomocí zásad skupiny ve službě Active Directory: `https://autologon.microsoftazuread-sso.com` . Dřív museli zákazníci přidat dvě adresy URL.

Další informace najdete v tématu [Azure Active Directory bezproblémové jednotné přihlašování](../hybrid/how-to-connect-sso.md).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu podnikové aplikace **:** integrace třetích stran

V březnu 2018 jsme do Galerie aplikací přidali tyto 15 nových aplikací s podporou federace:

[Boxcryptor](../saas-apps/boxcryptor-tutorial.md), [CylancePROTECT](../saas-apps/cylanceprotect-tutorial.md), Wrike, [SignalFx](../saas-apps/signalfx-tutorial.md), Assistant podle FirstAgenda, [YardiOne](../saas-apps/yardione-tutorial.md), Vtiger CRM, inmrknutí, [amplituda](../saas-apps/amplitude-tutorial.md), [Spacio](../saas-apps/spacio-tutorial.md), [ContractWorks](../saas-apps/contractworks-tutorial.md), [Bersin](../saas-apps/bersin-tutorial.md), [Mercell](../saas-apps/mercell-tutorial.md), [Trisotech Digital Enterprise Server](../saas-apps/trisotechdigitalenterpriseserver-tutorial.md), Qumu [Cloud](../saas-apps/qumucloud-tutorial.md).

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md).

Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="pim-for-azure-resources-is-generally-available"></a>PIM je všeobecně dostupná pro prostředky Azure.

**Zadejte:** Nová **Kategorie služby funkcí:** Privileged Identity Management **možnosti produktu:** Privileged Identity Management

Pokud používáte Azure AD Privileged Identity Management pro role adresáře, můžete teď pro role prostředků Azure, jako jsou předplatná, skupiny prostředků, Virtual Machines a všechny další prostředky, které Azure Resource Manager podporuje, používat funkce přístupu a přiřazování s časovým omezením PIM. Vyjistěte Multi-Factor Authentication při aktivaci rolí za běhu a naplánujte aktivace v koordinaci se schválenými okny změn. Kromě toho tato verze přidává vylepšení, která nejsou během veřejné verze Preview k dispozici, včetně aktualizovaného uživatelského rozhraní, pracovních postupů schválení a možnosti prodloužit platnost rolí, jejichž platnost brzy vyprší a obnoví.

Další informace najdete v tématu [prostředky PIM pro Azure (Preview)](../privileged-identity-management/azure-pim-resource-rbac.md) .

---

### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Přidání volitelných deklarací identit do tokenů aplikací (Public Preview)

**Zadejte:** Nová **Kategorie služby funkcí:** ověřování (přihlášení) **schopnost produktu:** ověřování uživatelů

Vaše aplikace Azure AD teď může vyžádat vlastní nebo volitelné deklarace identity v JWTs nebo tokenech SAML.  Jedná se o deklarace identity uživatele nebo tenanta, které nejsou ve výchozím nastavení v tokenu zahrnuty, z důvodu velikosti nebo omezení použitelnosti.  Tato verze je v současnosti ve verzi Public Preview pro aplikace Azure AD v koncových bodech v 1.0 a v 2.0.  V dokumentaci naleznete informace o tom, jaké deklarace identity je možné přidat a jak upravit manifest aplikace pro vyžádání.

Další informace najdete v tématu [volitelné deklarace identity ve službě Azure AD](../develop/active-directory-optional-claims.md).

---

### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Azure AD podporuje PKCE pro bezpečnější toky OAuth.

**Zadejte:** Nová **Kategorie služby funkcí:** ověřování (přihlášení) **schopnost produktu:** ověřování uživatelů

Dokumentace k Azure AD se aktualizovala tak, aby poznamenala podporu pro PKCE, která umožňuje bezpečnější komunikaci během procesu udělení autorizačního kódu OAuth 2,0.  V koncových bodech v 1.0 a v 2.0 jsou podporovány jak S256, tak code_challenges ve formátu prostého textu.

Další informace najdete v tématu [vyžádání autorizačního kódu](../develop/v2-oauth2-auth-code-flow.md#request-an-authorization-code).

---

### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>Podpora zřizování všech hodnot atributů uživatelů dostupných v Get_Workers rozhraní API pro Workday

**Zadejte:** Nová **Kategorie služby funkcí:** zřizování aplikací **– možnost produktu:** integrace třetích stran

Verze Public Preview pro příchozí zřizování z Workday do Active Directory a Azure AD teď podporuje možnost extrahovat a zřídit všechny hodnoty atributů dostupné v Get_Workers rozhraní API pro Workday. Tím se přidá podpora stovky dalších standardních a vlastních atributů nad rámec těch dodaných s počáteční verzí konektoru pro příchozí zřizování Workday.

Další informace najdete v tématu [přizpůsobení seznamu atributů uživatele Workday](../saas-apps/workday-inbound-tutorial.md#customizing-the-list-of-workday-user-attributes) .

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Změna členství ve skupině z dynamického na statickou a naopak

**Zadejte:** Nová **Kategorie služby funkcí:** **schopnost produktu** správy skupin: spolupráce

Je možné změnit způsob správy členství ve skupině. To je užitečné, pokud chcete zachovat stejný název skupiny a ID v systému, takže všechny existující odkazy na skupinu jsou stále platné. Vytvoření nové skupiny by vyžadovalo aktualizaci těchto odkazů.
Aktualizovali jsme centrum pro správu Azure AD, abychom tuto funkci podporovali. Zákazníci teď můžou převést existující skupiny z dynamického členství na přiřazené členství a naopak. Existující rutiny prostředí PowerShell jsou také stále k dispozici.

Další informace najdete v tématu [dynamická pravidla členství pro skupiny v Azure Active Directory](../enterprise-users/groups-dynamic-membership.md)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Vylepšení chování při odhlášení pomocí bezproblémového jednotného přihlašování

**Zadejte:** Kategorie změněné **služby funkcí:** ověřování (přihlášení) **schopnost produktu:** ověřování uživatelů

Dříve i v případě, že se uživatelé explicitně odhlásili z aplikace zabezpečené službou Azure AD, budou automaticky znovu podepsáni pomocí bezproblémového jednotného přihlašování, pokud se pokusí o přístup k aplikaci Azure AD znovu v rámci jejich Corpnet ze svých zařízení připojených k doméně. S touto změnou se podporuje odhlášení.  Díky tomu mohou uživatelé zvolit stejný nebo jiný účet služby Azure AD, aby se mohli znovu přihlásit pomocí bezproblémového přihlašování (SSO).

Další informace najdete v tématu [Azure Active Directory bezproblémové jednotné přihlašování](../hybrid/how-to-connect-sso.md) .

---

### <a name="application-proxy-connector-version-154020-released"></a>Vydaná verze konektoru proxy aplikací 1.5.402.0

**Zadejte:** Kategorie změněné **služby funkcí:** **možnost produktu** proxy aplikace: zabezpečení identity & ochrana

Tato verze konektoru je postupně zahrnuta do listopadu. Tato nová verze konektoru obsahuje následující změny:

- Konektor nyní nastaví soubory cookie na úrovni domény namísto úrovně subdomény. Tím se zajistí plynulejší přístup k jednotnému přihlašování a vyhne se redundantním výzvám ověřování.
- Podpora pro požadavky na kódování v bloku
- Vylepšené monitorování stavu konektoru
- Několik oprav chyb a vylepšení stability

Další informace najdete v tématu [vysvětlení konektorů služby Azure proxy aplikací služby AD](../manage-apps/application-proxy-connectors.md).

---

## <a name="february-2018"></a>Únor 2018

### <a name="improved-navigation-for-managing-users-and-groups"></a>Vylepšená navigace pro správu uživatelů a skupin

**Zadejte:** Plánování **Kategorie služby Change Service:** **schopnost produktu** Správa adresářů: adresář

Navigační prostředí pro správu uživatelů a skupin bylo zjednodušené. Nyní můžete přejít z přehledu adresáře přímo na seznam všech uživatelů, s jednodušším přístupem k seznamu odstraněných uživatelů. Můžete také přejít z přehledu adresáře přímo do seznamu všech skupin s jednodušším přístupem k nastavením správy skupin. A také ze stránky přehled adresáře můžete vyhledat uživatele, skupinu, podnikovou aplikaci nebo registraci aplikace.

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Dostupnost přihlášení a sestav auditu v Microsoft Azure provozovaných společností 21Vianet (Azure Čína 21Vianet)

**Zadejte:** Nová **Kategorie služby funkcí:** Azure Stack **Možnosti produktu:** monitorování & vytváření sestav

Sestavy protokolu aktivit služby Azure AD jsou teď dostupné v Microsoft Azure provozovaných pomocí instancí 21Vianet (Azure Čína 21Vianet). K dispozici jsou tyto protokoly:

- **Protokoly aktivit přihlášení**  – obsahuje všechny protokoly přihlášení přidružené k vašemu tenantovi.

- **Samoobslužné protokoly pro audit hesel** – obsahuje všechny protokoly auditu SSPR.

- **Protokoly auditu správy adresářů** – zahrnuje všechny protokoly auditu související se správou adresářů, jako je Správa uživatelů, Správa aplikací a další.

Pomocí těchto protokolů můžete získat přehled o tom, jak vaše prostředí funguje. Poskytnutá data vám umožní:

- Určete, jak vaše aplikace a služby využívají vaše uživatelé.

- Řešení problémů, které uživatelům brání v práci.

Další informace o tom, jak tyto sestavy používat, najdete v tématu [Azure Active Directory vytváření sestav](../reports-monitoring/overview-reports.md).

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Použití role čtenář sestav (role bez správce) k zobrazení sestav aktivit služby Azure AD

**Zadejte:** Nová **Kategorie služby funkce:** hlášení **schopnosti produktu:** monitorování & vytváření sestav

V rámci zpětné vazby od zákazníků po povolení přístupu k protokolům aktivit Azure AD uživatelům, kteří jsou v roli Čtenář sestav, povolili uživatelům, kteří jsou v rámci této Azure Portal, přístup k přihlašovacím aktivitám a auditům a také k používání rozhraní Microsoft Graph API.

Další informace o tom, jak tyto sestavy používat, najdete v tématu [Azure Active Directory vytváření sestav](../reports-monitoring/overview-reports.md).

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Deklarace ČísloZaměstnance je dostupná jako atribut uživatele a identifikátor uživatele.

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu podnikové aplikace **:** jednotné přihlašování

Pro uživatele a hosty B2B v přihlašovacích aplikacích založených na SAML můžete nakonfigurovat **ČísloZaměstnance** jako identifikátor uživatele a atribut uživatele pro uživatele v rámci aplikace pro přihlašování pomocí SAML.

Další informace najdete v tématu [přizpůsobení deklarací identity vystavených v tokenu SAML pro podnikové aplikace v Azure Active Directory](../develop/active-directory-saml-claims-customization.md).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Zjednodušená správa aplikací pomocí zástupných znaků v Azure Proxy aplikací služby AD

**Zadejte:** Nová **Kategorie služby funkcí:** **možnost produktu** proxy aplikací: ověřování uživatelů

Abychom usnadnili nasazení aplikace a snížili režijní náklady na správu, teď podporujeme možnost publikování aplikací pomocí zástupných znaků. Chcete-li publikovat aplikaci se zástupnými znaky, můžete postupovat podle standardního toku publikování aplikace, ale použít zástupný znak v interních a externích adresách URL.

Další informace najdete v tématu [aplikace se zástupnými znaky v proxy aplikace Azure Active Directory](../manage-apps/application-proxy-wildcard.md) .

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Nové rutiny, které podporují konfiguraci proxy aplikací

**Zadejte:** Nová **Kategorie služby funkcí:** **schopnost produktu** proxy aplikace: platforma

Nejnovější verze modulu AzureAD PowerShell Preview obsahuje nové rutiny, které zákazníkům umožňují konfigurovat aplikace proxy aplikací pomocí PowerShellu.

Nové rutiny jsou:

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

**Zadejte:** Nová **Kategorie služby funkcí:** **schopnost produktu** proxy aplikace: platforma

Nejnovější verze modulu AzureAD PowerShell obsahuje rutiny pro správu skupin ve službě Azure AD. Tyto rutiny byly dřív dostupné v modulu AzureADPreview a teď se přidají do modulu AzureAD.

Rutiny skupiny, které jsou nyní vydány pro obecnou dostupnost:

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

### <a name="a-new-release-of-azure-ad-connect-is-available"></a>K dispozici je nová verze Azure AD Connect.

**Zadejte:** Nová **Kategorie služby funkcí:** AD Sync **Možnosti produktu:** platforma

Azure AD Connect je preferovaný nástroj pro synchronizaci dat mezi Azure AD a místními zdroji dat, včetně Windows Server Active Directory a LDAP.

>[!Important]
>Toto sestavení zavádí změny pravidla schématu a synchronizace. Služba Azure AD Connect Synchronization spouští úplný postup importu a úplné synchronizace po upgradu. Informace o tom, jak toto chování změnit, najdete v tématu [jak pozdržet úplnou synchronizaci po upgradu](../hybrid/how-to-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade).

Tato verze obsahuje následující aktualizace a změny:

**Opravené problémy**

- Oprava okna časování na úkolech na pozadí pro stránku filtrování oddílů při přechodu na další stránku

- Opravili jsme chybu, která způsobila porušení přístupu během vlastní akce ConfigDB.

- Opravili jsme chybu pro obnovení z časového limitu připojení SQL.

- Opravili jsme chybu, kdy se u certifikátů se zástupnými znaky sítě SAN nedaří ověřit kontrolu požadavků.

- Opravili jsme chybu, která během exportu konektoru služby Azure AD způsobuje miiserver.exe chybě.

- Opravili jsme chybu, při které se při spuštění do řadiče domény přihlásil špatný pokus o přihlášení, protože průvodce službou Azure AD Connect změnil konfiguraci.

**Nové funkce a vylepšení**

- Telemetrie aplikací – správci můžou tuto třídu dat zapnout nebo vypnout.

- Data o stavu Azure AD – správci musí navštívit portál stavu, aby mohli řídit jeho nastavení stavu. Po změně zásady služby ji budou agenti číst a vymáhat.

- Pro inicializaci stránky byly přidány akce konfigurace zpětného zápisu zařízení a indikátor průběhu.

- Vylepšená Obecná diagnostika se sestavou HTML a úplným shromažďováním dat v sestavě ZIP-Text/HTML.

- Zlepšila se spolehlivost automatického upgradu a přidala se další telemetrie, aby bylo zajištěno, že je možné určit stav serveru.

- Omezte oprávnění dostupná pro privilegované účty na účtu konektoru služby AD. Pro nové instalace omezuje Průvodce oprávnění, která mají privilegované účty v účtu MSOL po vytvoření účtu MSOL. Změny mají vliv na rychlé instalace a vlastní instalace pomocí automatického vytváření účtu.

- Změnil instalační program, aby při čisté instalaci AADConnect nevyžadoval oprávnění správce.

- Nový nástroj pro řešení potíží se synchronizací pro určitý objekt. V současné době nástroj kontroluje následující věci:

    - Hodnota UserPrincipalName nesouhlasí mezi synchronizovaným objektem uživatele a uživatelským účtem v Tenantovi Azure AD.

    - Pokud je objekt filtrován z synchronizace z důvodu filtrování domény

    - Pokud je objekt filtrován z synchronizace kvůli filtrování organizační jednotky (OU)

- Nový nástroj pro synchronizaci hodnoty hash aktuálního hesla uložené v místní službě Active Directory pro konkrétní uživatelský účet. Nástroj nevyžaduje změnu hesla.

---

### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Aplikace podporující zásady Intune App Protection přidané pro použití s podmíněným přístupem na základě aplikace Azure AD

**Zadejte:** Kategorie změněné **služby funkcí:** **možnost produktu** pro podmíněný přístup: zabezpečení identity & ochrana

Přidali jsme další aplikace, které podporují podmíněný přístup na základě aplikace. Nyní můžete získat přístup k Office 365 a dalším cloudovým aplikacím připojeným k Azure AD pomocí těchto schválených klientských aplikací.

Do konce února budou přidány následující aplikace:

- Microsoft Power BI

- Spouštěč Microsoftu

- Fakturace Microsoftu

Další informace naleznete v tématu:

- [Požadavek na schválenou aplikaci klienta](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Podmíněný přístup na základě aplikace Azure AD](../conditional-access/app-based-conditional-access.md)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Podmínky použití aktualizace na mobilní prostředí

**Zadejte:** Kategorie změněné **služby funkcí:** **možnost podmínky použití produktu:** dodržování předpisů

Po zobrazení podmínek použití teď můžete kliknout na možnost **mít problémy se zobrazením? Klikněte sem**. Kliknutím na tento odkaz otevřete na svém zařízení nativně požadavky na použití. Bez ohledu na velikost písma v dokumentu nebo na velikosti obrazovky můžete dokument podle potřeby zvětšit a přečíst.

---

## <a name="january-2018"></a>Leden 2018

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>V galerii aplikací Azure AD jsou k dispozici nové federované aplikace

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu podnikové aplikace **:** integrace třetích stran

V lednu 2018 byly do Galerie aplikací přidány následující nové aplikace s podporou federace:

[IBM OpenPages](../saas-apps/ibmopenpages-tutorial.md), [OneTrust software pro správu ochrany osobních údajů](../saas-apps/onetrust-tutorial.md), [Dealpath](../saas-apps/dealpath-tutorial.md), [IriusRisk federovaný adresář a [věrnost NetBenefits](../saas-apps/fidelitynetbenefits-tutorial.md).

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md).

Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="sign-in-with-additional-risk-detected"></a>Přihlášení pomocí zjištěného dalšího rizika

**Zadejte:** Nová **Kategorie služby funkcí:** **schopnost produktu** Identity Protection & ochrana identity zabezpečení

Přehled, který získáte pro zjištění zjištěného rizika, je svázán s vaším předplatným služby Azure AD. V případě Azure AD Premiumho P2 Edition získáte nejpodrobnější informace o všech základních detekcích.

U edice Azure AD Premium P1 se detekce, která není pokrytá vaší licencí, zobrazuje jako přihlášení k detekci rizik s dalšími zjištěnými riziky.

Další informace najdete v tématu [Azure Active Directory detekci rizik](../identity-protection/overview-identity-protection.md).

---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Skrýt aplikace Office 365 z přístupových panelů koncového uživatele

**Zadejte:** Nová **Kategorie služby funkcí:** **funkce produktu** moje aplikace: jednotné přihlašování

Teď můžete lépe spravovat, jak se aplikace Office 365 zobrazují na přístupových panelech uživatelů pomocí nového nastavení uživatele. Tato možnost je užitečná pro omezení počtu aplikací v přístupových panelech uživatele, pokud upřednostňujete, aby se na portálu Office zobrazovaly jenom aplikace Office. Nastavení se nachází v **nastavení uživatele** a je označeno, **Uživatelé mohou na portálu Office 365 zobrazit pouze aplikace Office 365**.

Další informace najdete v tématu [skrytí aplikace z uživatelského prostředí v Azure Active Directory](../manage-apps/hide-application-from-user-portal.md).

---

### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Bezproblémové přihlašování k aplikacím povoleným pro jednotné přihlašování pomocí hesla přímo z adresy URL aplikace

**Zadejte:** Nová **Kategorie služby funkcí:** **funkce produktu** moje aplikace: jednotné přihlašování

Rozšíření prohlížeče moje aplikace je teď k dispozici prostřednictvím pohodlnýho nástroje, který poskytuje možnost jednotného přihlašování mých aplikací jako zástupce v prohlížeči. Po instalaci se uživateli ve svém prohlížeči zobrazí ikona dlaždice, která jim umožní rychlý přístup k aplikacím. Uživatelé teď můžou využít výhody:

- Možnost přímého přihlašování k aplikacím založeným na JEDNOTNÉm přihlašování na přihlašovací stránce aplikace
- Spuštění libovolné aplikace pomocí funkce rychlého vyhledávání
- Zástupci naposledy použitých aplikací z rozšíření
- Rozšíření je k dispozici pro Microsoft Edge, Chrome a Firefox.

Další informace najdete v tématu [zabezpečený přihlašovací rozšíření pro moje aplikace](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Prostředí pro správu Azure AD v portál Azure Classic bylo vyřazeno

**Zadejte:** Zastaralá **Kategorie služby:** **schopnost produktu** Azure AD: adresář

Od 8. ledna 2018 bylo prostředí pro správu Azure AD na portálu Azure Classic vyřazeno. K tomu došlo ve spojení s vyřazením portálu Azure Classic. V budoucnu byste měli použít [Centrum pro správu Azure AD](https://aad.portal.azure.com) pro veškerou správu Azure AD založenou na portálu.

---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>Webový portál PhoneFactor je vyřazený.

**Zadejte:** Zastaralá **Kategorie služby:** **schopnost produktu** Azure AD: adresář

Od 8. ledna 2018 byl webový portál PhoneFactor vyřazený. Tento portál byl použit pro správu MFA serveru, ale tyto funkce byly přesunuty do Azure Portal na adrese portal.azure.com.

Konfigurace MFA se nachází v: **Azure Active Directory \> MFA Server**

---

### <a name="deprecate-azure-ad-reports"></a>Zastaralé sestavy služby Azure AD

**Zadejte:** Zastaralá **Kategorie služby:** **schopnost produktu hlášení:** Správa životního cyklu identit


Díky obecné dostupnosti nové konzoly pro správu Azure Active Directory a novým rozhraním API, která jsou teď k dispozici pro sestavy aktivit a zabezpečení, jsou rozhraní API sestav v rámci koncového bodu "/Reports" vyřazena od 31. prosince 2017.

**Co je k dispozici?**

V rámci přechodu na novou konzolu správce jsme vytvořili 2 nová rozhraní API k načtení protokolů aktivit Azure AD. Nová sada rozhraní API nabízí lepší funkce pro filtrování a řazení, které poskytují lepší aktivity auditu a přihlašování. Data dříve dostupná prostřednictvím sestav zabezpečení teď můžou být přístupná prostřednictvím rozhraní API detekce rizik ochrany identity v Microsoft Graph.

Další informace naleznete v tématu:

- [Začínáme s rozhraním API pro vytváření sestav Azure Active Directory](../reports-monitoring/concept-reporting-api.md)

- [Začínáme s Azure Active Directory Identity Protection a Microsoft Graph](../identity-protection/howto-identity-protection-graph-api.md)

---

## <a name="december-2017"></a>Prosinec 2017

### <a name="terms-of-use-in-the-access-panel"></a>Podmínky použití na přístupovém panelu

**Zadejte:** Nová **Kategorie služby funkcí:** **možnost podmínky použití produktu:** dodržování předpisů

Teď můžete přejít na přístupový panel a zobrazit si dřív přijaté podmínkami použití.

Postupujte takto:

1. Přejít na [portál MyApp](https://myapps.microsoft.com)a přihlásit se.

2. V pravém horním rohu vyberte své jméno a pak v seznamu vyberte možnost **profil** .

3. V **profilu** vyberte možnost **přezkoumat podmínek použití**.

4. Teď si můžete projít podmínkami použití, které jste přijali.

Další informace najdete v tématu [funkce Azure AD terms of use (Preview)](../conditional-access/terms-of-use.md).

---

### <a name="new-azure-ad-sign-in-experience"></a>Nové prostředí pro přihlášení k Azure AD

**Zadejte:** Nová **Kategorie služby funkcí:** **schopnost produktu** Azure AD: ověřování uživatelů

Uživatelská rozhraní systému identit Azure AD a účet Microsoft se přepracovali tak, aby měly konzistentní vzhled a chování. Kromě toho stránka pro přihlášení k Azure AD nejprve shromažďuje uživatelské jméno a za ním následuje přihlašovací údaje na druhé obrazovce.

Další informace najdete v tématu [nové přihlášení k Azure AD je teď ve verzi Public Preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/).

---

### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Méně výzev k přihlášení: nové prostředí pro přihlášení k Azure AD, které je přihlášené

**Zadejte:** Nová **Kategorie služby funkcí:** **schopnost produktu** Azure AD: ověřování uživatelů

Zaškrtávací políčko **zůstat** přihlášení na přihlašovací stránce služby Azure AD bylo nahrazeno novou výzvou, která se zobrazí po úspěšném ověření.

Pokud odpovíte **Ano** na tuto výzvu, služba vám nabídne trvalou obnovovací token. Toto chování se shoduje s tím, jak jste zaškrtli políčko **zůstat přihlášeni** ve starém prostředí. Pro federované klienty se tato výzva zobrazí po úspěšném ověření pomocí federované služby.

Další informace najdete v tématu [méně výzev k přihlášení: nové prostředí "zůstat přihlášené" pro Azure AD je ve verzi Preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/).

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Přidání konfigurace, která vyžaduje rozšíření podmínek použití před přijetím

**Zadejte:** Nová **Kategorie služby funkcí:** **možnost podmínky použití produktu:** dodržování předpisů

Možnost správců vyžaduje, aby uživatelé před přijetím podmínek rozšířili podmínkami použití.

Vyberte **zapnout** nebo **vypnout** , pokud chcete, aby uživatelé rozšířili podmínkami použití. Nastavení **zapnuto** vyžaduje, aby uživatelé před přijetím zobrazili podmínek použití.

Další informace najdete v tématu [funkce Azure AD terms of use (Preview)](../conditional-access/terms-of-use.md).

---

### <a name="scoped-activation-for-eligible-role-assignments"></a>Aktivace s vymezeným oborem pro přiřazení oprávněných rolí

**Zadejte:** Nová **Kategorie služby funkcí:** Privileged Identity Management **možnosti produktu:** Privileged Identity Management

Pomocí vymezené aktivace můžete aktivovat opravňující přiřazení rolí prostředků Azure s menší autonomií, než je původní výchozí nastavení přiřazení. Příkladem je, že jste přiřazeni jako vlastník předplatného ve vašem tenantovi. S vymezenou aktivací můžete roli vlastníka aktivovat až pro pět prostředků obsažených v rámci předplatného (například skupiny prostředků a virtuální počítače). Rozsah aktivace může snížit možnost provádění nechtěných změn v kritických prostředcích Azure.

Další informace najdete v tématu [co je Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md).

---

### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Nové federované aplikace v galerii aplikací Azure AD

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu podnikové aplikace **:** integrace třetích stran

Do prosince 2017 jsme do Galerie aplikací přidali tyto nové aplikace s podporou federace:

[Accredible](../saas-apps/accredible-tutorial.md), Adobe Experience Manager, [EFI Digital prezentace](../saas-apps/efidigitalstorefront-tutorial.md), [Communifire](../saas-apps/communifire-tutorial.md) CybSafe, [FactSet](../saas-apps/factset-tutorial.md), [Image funguje](../saas-apps/imageworks-tutorial.md), [mobi](../saas-apps/mobi-tutorial.md), [MobileIron integraci Azure AD](../saas-apps/mobileiron-tutorial.md), [Reflektive](../saas-apps/reflektive-tutorial.md), [SAML SSO pro Bamboo, podle rezoluce GmbH](../saas-apps/bamboo-tutorial.md), [SSO pro BitBucket, a to pomocí](../saas-apps/bitbucket-tutorial.md)řešení GmbH, [Vodeclic](../saas-apps/vodeclic-tutorial.md), WebHR, Zenegy Integration pro Azure AD.

Další informace o aplikacích naleznete v tématu [SaaS Application Integration with Azure Active Directory](../saas-apps/tutorial-list.md).

Další informace o výpisu vaší aplikace v galerii aplikací Azure AD najdete v tématu [seznam aplikací v galerii aplikací Azure Active Directory](../develop/v2-howto-app-gallery-listing.md).

---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Pracovní postupy schvalování pro role adresáře Azure AD

**Zadejte:** Kategorie změněné **služby funkcí:** Privileged Identity Management **možnosti produktu:** Privileged Identity Management

Pracovní postup schvalování pro role adresáře Azure AD je všeobecně dostupný.

Pomocí schvalovacího pracovního postupu můžou správci privilegovaných rolí vyžadovat, aby před použitím privilegované role požádali o aktivaci role. Zodpovědnost schválení může delegovat více uživatelů a skupin. Způsobilí členové role obdrží oznámení po dokončení schválení a jejich role je aktivní.

---

### <a name="pass-through-authentication-skype-for-business-support"></a>Předávací ověřování: podpora Skypu pro firmy

**Zadejte:** Kategorie změněné **služby funkcí:** ověřování (přihlášení) **schopnost produktu:** ověřování uživatelů

Předávací ověřování teď podporuje přihlášení uživatelů k klientským aplikacím Skypu pro firmy, které podporují moderní ověřování, včetně online a hybridních topologií.

Další informace najdete v tématu [topologie Skypu pro firmy podporované moderním ověřováním](/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported).

---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Aktualizace Azure AD Privileged Identity Management pro službu Azure RBAC (Preview)

**Zadejte:** Kategorie změněné **služby funkcí:** Privileged Identity Management **možnosti produktu:** Privileged Identity Management

Díky aktualizaci Public Preview Azure AD Privileged Identity Management (PIM) pro řízení přístupu na základě role Azure (Azure RBAC) teď můžete:

* Používejte jen dostatečně správu.
* Vyžaduje schválení pro aktivaci rolí prostředků.
* Naplánujte budoucí aktivaci role, která vyžaduje schválení pro Azure AD i role Azure.

Další informace najdete v tématu [Privileged Identity Management pro prostředky Azure (Preview)](../privileged-identity-management/azure-pim-resource-rbac.md).

---

## <a name="november-2017"></a>Listopad 2017

### <a name="access-control-service-retirement"></a>Vyřazení služby Access Control

**Zadejte:** Plánování **Kategorie služby Change Service:** schopnost služby Access Control service **:** Access Control Service

Azure Active Directory Access Control (označované také jako služba Access Control) budou vyřazeny v 2018. Další informace, které obsahují podrobné pokyny k migraci plánu a vysoké úrovně, budou uvedené v následujících několika týdnech. Komentáře na této stránce můžete ponechat s případnými dotazy týkajícími se služby Access Control a člen týmu je bude odpovídat.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Omezit přístup prohlížeče k Intune Managed Browser

**Zadejte:** Plánování **Kategorie služby Change Service:** **možnost produktu** pro podmíněný přístup: zabezpečení identity a ochrana

Přístup z prohlížeče k Office 365 a k dalším cloudovým aplikacím připojeným k Azure AD můžete omezit pomocí Intune Managed Browser jako schválené aplikace.

Pro podmíněný přístup na základě aplikace teď můžete nakonfigurovat tuto podmínku:

**Klientské aplikace:** Prohlížeee

**Jaký je účinek změny?**

V dnešní době je přístup při použití této podmínky zablokován. Pokud je verze Preview k dispozici, veškerý přístup bude vyžadovat použití aplikace Managed Browser.

Podívejte se na tuto funkci a další informace v nadcházejících blogů a poznámkách k verzi.

Další informace najdete v tématu [podmíněný přístup ve službě Azure AD](../conditional-access/overview.md).

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nové schválené klientské aplikace pro podmíněný přístup na základě aplikace Azure AD

**Zadejte:** Plánování **Kategorie služby Change Service:** **možnost produktu** pro podmíněný přístup: zabezpečení identity a ochrana

V seznamu [schválených klientských aplikací](../conditional-access/concept-conditional-access-conditions.md#client-apps)jsou následující aplikace:

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

Další informace naleznete v tématu:

- [Požadavek na schválenou aplikaci klienta](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Podmíněný přístup na základě aplikace Azure AD](../conditional-access/app-based-conditional-access.md)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Podpora podmínek použití v různých jazycích

**Zadejte:** Nová **Kategorie služby funkcí:** **možnost podmínky použití produktu:** dodržování předpisů

Správci teď mohou vytvořit nové podmínkami použití, které obsahují více dokumentů PDF. Tyto dokumenty PDF můžete označit odpovídajícím jazykem. Uživatelům se v závislosti na svých preferencích zobrazí PDF s odpovídajícím jazykem. Pokud se neshoduje, zobrazí se výchozí jazyk.

---

### <a name="real-time-password-writeback-client-status"></a>Stav klienta zpětného zápisu hesla v reálném čase

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu samoobslužného resetování hesla **:** ověřování uživatelů

Nyní můžete zkontrolovat stav místního klienta zpětného zápisu hesla. Tato možnost je k dispozici v části **Místní integrace** stránky pro [resetování hesla](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) .

Pokud dojde k problémům s připojením k místnímu zpětnému zápisu, zobrazí se chybová zpráva, která vám poskytne:

- Informace o tom, proč se nemůžete připojit k místnímu klientovi zpětného zápisu.
- Odkaz na dokumentaci, která vám pomůže při řešení problému.

Další informace najdete v tématu věnovaném [místní integraci](../authentication/concept-sspr-howitworks.md#on-premises-integration).

---

### <a name="azure-ad-app-based-conditional-access"></a>Podmíněný přístup na základě aplikace Azure AD

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu Azure AD **:** zabezpečení identity a ochrana

Teď můžete omezit přístup k Office 365 a dalším cloudovým aplikacím připojeným k Azure AD na [schválené klientské aplikace](../conditional-access/concept-conditional-access-conditions.md#client-apps) , které podporují zásady ochrany aplikací Intune pomocí [podmíněného přístupu založeného na aplikaci Azure AD](../conditional-access/app-based-conditional-access.md). Zásady ochrany aplikací Intune se používají ke konfiguraci a ochraně firemních dat v těchto klientských aplikacích.

Kombinací [aplikací na základě](../conditional-access/app-based-conditional-access.md) zásad podmíněného přístupu [na základě zařízení](../conditional-access/require-managed-devices.md) získáte flexibilitu při ochraně dat pro osobní zařízení a zařízení společnosti.

Pro použití podmíněného přístupu na základě aplikace jsou teď dostupné následující podmínky a ovládací prvky:

**Podporovaná podmínka platformy**

- iOS
- Android

**Podmínky klientských aplikací**

- Mobilní aplikace a klienti klasické pracovní plochy

**Řízení přístupu**

- Vyžadovat klientskou aplikaci schválenou

Další informace najdete v tématu [podmíněný přístup založený na aplikaci Azure AD](../conditional-access/app-based-conditional-access.md).

---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Správa zařízení Azure AD v Azure Portal

**Zadejte:** Nová **Kategorie služby funkcí:** registrace zařízení a **funkce produktu** pro správu: zabezpečení identity a ochrana

Teď můžete najít všechna vaše zařízení připojená k Azure AD a aktivity související se zařízením na jednom místě. Existují nové možnosti správy, které vám umožní spravovat všechny identity a nastavení vašich zařízení v Azure Portal. V této verzi můžete:

- Zobrazí všechna zařízení, která jsou k dispozici pro podmíněný přístup ve službě Azure AD.
- Zobrazení vlastností, které zahrnují vaše zařízení připojená k hybridní službě Azure AD.
- Najděte si klíče BitLockeru pro zařízení připojená k Azure AD, Spravujte svoje zařízení v Intune a další.
- Správa nastavení týkajících se zařízení v Azure AD

Další informace najdete v tématu [Správa zařízení pomocí Azure Portal](../devices/device-management-azure-portal.md).

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Podpora macOS jako platformy zařízení pro podmíněný přístup Azure AD

**Zadejte:** Nová **Kategorie služby funkcí:** **možnost produktu** pro podmíněný přístup: zabezpečení identity a ochrana

V rámci zásad podmíněného přístupu Azure AD teď můžete zahrnout (nebo vyloučit) macOS jako podmínku pro platformu zařízení. Díky přidání macOS k podporovaným platformám zařízení můžete:

- **Registrace a Správa zařízení macOS pomocí Intune** Podobně jako u jiných platforem, jako je iOS a Android, je k dispozici aplikace Portál společnosti, která macOS umožňuje sjednocené registrace. Pomocí nové aplikace Portál společnosti pro macOS můžete zaregistrovat zařízení v Intune a zaregistrovat ho ve službě Azure AD.
- **Zajistěte, aby zařízení macOS vyhovovala zásadám dodržování předpisů vaší organizace definovaným v Intune.** V Intune na Azure Portal teď můžete nastavit zásady dodržování předpisů pro zařízení macOS.
- **Omezte přístup k aplikacím ve službě Azure AD tak, aby splňovala jenom vyhovující zařízení macOS.** Při vytváření zásad podmíněného přístupu se macOS jako samostatná možnost platformy zařízení. Nyní můžete vytvářet zásady podmíněného přístupu specifické pro macOS pro cílovou aplikaci určenou v Azure.

Další informace naleznete v tématu:

- [Vytvoření zásady dodržování předpisů pro zařízení s macOS pomocí Intune](/mem/intune/protect/compliance-policy-create-mac-os)
- [Podmíněný přístup ve službě Azure AD](../conditional-access/overview.md)

---

### <a name="network-policy-server-extension-for-azure-ad-multi-factor-authentication"></a>Rozšíření serveru NPS (Network Policy Server) pro Azure AD Multi-Factor Authentication

**Zadejte:** Nová **Kategorie služby funkcí:** **schopnost produktu** Multi-Factor Authentication: ověřování uživatelů

Rozšíření serveru NPS (Network Policy Server) pro Azure AD Multi-Factor Authentication do vaší ověřovací infrastruktury přidávají cloudové možnosti Multi-Factor Authentication pomocí stávajících serverů. S rozšířením serveru NPS (Network Policy Server) můžete do stávajícího toku ověřování přidat ověřování pomocí telefonního hovoru, textové zprávy nebo aplikace pro telefon. Nemusíte instalovat, konfigurovat a udržovat nové servery.

Toto rozšíření bylo vytvořeno pro organizace, které chtějí chránit připojení k virtuální privátní síti bez nutnosti nasazení Multi-Factor Authentication Server Azure. Rozšíření serveru NPS (Network Policy Server) funguje jako adaptér mezi POLOMĚRem a cloudovou Multi-Factor Authentication služby Azure AD, aby poskytovala druhý faktor ověřování pro federované nebo synchronizované uživatele.

Další informace najdete v tématu [integrace stávající infrastruktury serveru NPS (Network Policy Server) se službou Azure AD Multi-Factor Authentication](../authentication/howto-mfa-nps-extension.md).

---

### <a name="restore-or-permanently-remove-deleted-users"></a>Obnovit nebo trvale odebrat odstraněné uživatele

**Zadejte:** Nová **Kategorie služby funkcí:** **schopnost produktu** Správa uživatelů: adresář

V centru pro správu Azure AD teď můžete:

- Obnovení odstraněného uživatele.
- Trvale odstraní uživatele.

**Vyzkoušejte:**

1. V centru pro správu Azure AD vyberte [Všichni uživatelé](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) v části **Spravovat** .

2. V seznamu **Zobrazit** vyberte **Nedávno odstraněné uživatele**.

3. Vyberte jednoho nebo více nedávno odstraněných uživatelů a pak je buď obnovte, nebo je trvale odstraňte.

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nové schválené klientské aplikace pro podmíněný přístup na základě aplikace Azure AD

**Zadejte:** Kategorie změněné **služby funkcí:** **možnost produktu** podmíněný přístup: zabezpečení identity a ochrana

Do seznamu [schválených klientských aplikací](../conditional-access/concept-conditional-access-conditions.md#client-apps)byly přidány následující aplikace:

- Microsoft Planner
- Azure Information Protection

Další informace naleznete v tématu:

- [Požadavek na schválenou aplikaci klienta](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Podmíněný přístup na základě aplikace Azure AD](../conditional-access/app-based-conditional-access.md)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Použití "nebo" mezi ovládacími prvky v zásadách podmíněného přístupu

**Zadejte:** Kategorie změněné **služby funkcí:** **možnost produktu** podmíněný přístup: zabezpečení identity a ochrana

Nyní můžete použít "nebo" (pro řízení podmíněného přístupu vyžadovat jeden z vybraných ovládacích prvků). Tuto funkci můžete použít k vytvoření zásad pomocí "nebo" mezi ovládacími prvky přístupu. Pomocí této funkce můžete například vytvořit zásadu, která vyžaduje, aby se uživatel přihlásil pomocí Multi-Factor Authentication "nebo" na zařízení vyhovujícím předpisům.

Další informace najdete v tématu věnovaném [ovládacím prvkům v Azure AD podmíněný přístup](../conditional-access/controls.md).

---

### <a name="aggregation-of-real-time-risk-detections"></a>Agregace zjišťování rizik v reálném čase

**Zadejte:** Kategorie změněné **služby funkcí:** **schopnost produktu** Identity Protection: zabezpečení identity a ochrana

V Azure AD Identity Protection se teď všechny detekce rizik v reálném čase, které pocházejí ze stejné IP adresy v daném dni, agreguje pro každý typ detekce rizik. Tato změna omezuje množství zjištěných detekcí rizik bez změny zabezpečení uživatele.

Základní zjišťování v reálném čase funguje při každém přihlášení uživatele. Pokud máte zásady zabezpečení rizik přihlašování nastavené tak, aby Multi-Factor Authentication nebo zablokovaly přístup, pořád se aktivuje při každé rizikové přihlašování.

---

## <a name="october-2017"></a>Říjen 2017

### <a name="deprecate-azure-ad-reports"></a>Zastaralé sestavy služby Azure AD

**Zadejte:** Plánování **Kategorie služby Change Service:** **funkce produktu** pro generování životního cyklu identity

Azure Portal vám poskytne:

- Nová konzola pro správu služby Azure AD.
- Nová rozhraní API pro aktivity a sestavy zabezpečení.

Z důvodu těchto nových schopností byly rozhraní API sestav pod koncovým bodem/Reports vyřazena 10. prosince 2017.

---

### <a name="automatic-sign-in-field-detection"></a>Automatické zjišťování polí přihlašování

**Zadejte:** **Kategorie pevné služby:** **schopnost produktu** moje aplikace: jednotné přihlašování

Azure AD podporuje automatické rozpoznávání polí pro aplikace, které vykreslí pole uživatelské jméno a heslo HTML. Tyto kroky jsou popsány v tématu [Automatické zachytávání přihlašovacích polí pro aplikaci](../manage-apps/troubleshoot-password-based-sso.md#manually-capture-sign-in-fields-for-an-app). Tuto možnost můžete najít přidáním aplikace *mimo galerii* na stránce **podnikové aplikace** v [Azure Portal](https://aad.portal.azure.com). Kromě toho můžete v této nové aplikaci nakonfigurovat režim **jednotného přihlašování** na **jednotné přihlašování založené na heslech**, zadat adresu URL webu a pak stránku uložit.

Kvůli problému se službou byla tato funkce dočasně zakázána. Problém byl vyřešen a znovu je k dispozici automatické rozpoznávání polí pro přihlášení.

---

### <a name="new-multi-factor-authentication-features"></a>Nové funkce Multi-Factor Authentication

**Zadejte:** Nová **Kategorie služby funkcí:** **možnost produktu** Multi-Factor Authentication: zabezpečení identity a ochrana

Multi-Factor Authentication (MFA) je podstatnou součástí ochrany vaší organizace. Aby se přihlašovací údaje lépe přizpůsoboval a bylo tak plynulější, byly přidány následující funkce:

- Výsledky Multi-Factor Challenge jsou přímo integrované do sestavy přihlášení služby Azure AD, která zahrnuje programový přístup k výsledkům vícefaktorového ověřování.
- Konfigurace MFA je podrobněji integrovaná do prostředí konfigurace Azure AD v Azure Portal.

V této veřejné verzi Preview jsou Správa MFA a vytváření sestav integrovaná součástí základních možností konfigurace služby Azure AD. Teď můžete spravovat funkce portálu pro správu MFA v rámci prostředí Azure AD.

Další informace najdete v tématu [referenční informace pro generování sestav MFA v Azure Portal](../authentication/howto-mfa-reporting.md).

---

### <a name="terms-of-use"></a>Podmínky použití

**Zadejte:** Nová **Kategorie služby funkcí:** **možnost podmínky použití produktu:** dodržování předpisů

Podmínky použití Azure AD můžete použít k předložení informací, jako jsou relevantní právní omezení pro dodržování právních předpisů nebo dodržování předpisů pro uživatele.

Pomocí podmínek použití Azure AD můžete v těchto scénářích:

- Obecné podmínek použití pro všechny uživatele ve vaší organizaci
- Konkrétní požadavky na použití na základě atributů uživatele (například lékaři a zdravotní sestry nebo domácí vs. mezinárodní zaměstnanci prováděné dynamickými skupinami)
- Konkrétní pravidla použití pro přístup k obchodním aplikacím s vysokým dopadem, jako je Salesforce

Další informace najdete v tématu věnovaném [podmínkám použití Azure AD](../conditional-access/terms-of-use.md).

---

### <a name="enhancements-to-privileged-identity-management"></a>Vylepšení Privileged Identity Management

**Zadejte:** Nová **Kategorie služby funkcí:** Privileged Identity Management **možnosti produktu:** Privileged Identity Management

Pomocí Azure AD Privileged Identity Management můžete v rámci organizace spravovat, řídit a monitorovat přístup k prostředkům Azure (Preview):

- Předplatná
- Skupiny prostředků
- Virtuální počítače

Všechny prostředky v rámci Azure Portal, které používají funkci Azure RBAC, můžou využívat všechny možnosti správy zabezpečení a životního cyklu, které Azure AD Privileged Identity Management nabízí.

Další informace najdete v tématu [Privileged Identity Management pro prostředky Azure](../privileged-identity-management/azure-pim-resource-rbac.md).

---

### <a name="access-reviews"></a>Kontroly přístupu

**Zadejte:** Nová **Kategorie služby funkcí:** kontroly přístupu **funkce produktu:** dodržování předpisů

Organizace můžou pomocí kontrol přístupu (Preview) efektivně spravovat členství ve skupinách a přístup k podnikovým aplikacím:

- Můžete znovu certifikovat přístup uživatelů typu host pomocí kontrol jejich přístupu k aplikacím a jejich členství ve skupinách. Recenzenti se můžou efektivně rozhodnout, jestli má hostům pokračovat v přístupu na základě přehledů poskytovaných přezkoumáním přístupu.
- Pomocí kontrol přístupu je možné provést opětovnou certifikaci přístupu k aplikacím a členství ve skupinách i pro zaměstnance.

Kontroly přístupu lze shromažďovat v programech sledujících dodržování předpisů a přístup k citlivým aplikacím v rámci organizace.

Další informace najdete v tématu kontroly [přístupu Azure AD](../governance/access-reviews-overview.md).

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Skrýt aplikace třetích stran z mých aplikací a spouštěče aplikací pro Office 365

**Zadejte:** Nová **Kategorie služby funkcí:** funkce produktu moje aplikace **:** jednotné přihlašování

Teď můžete lépe spravovat aplikace, které se zobrazují na portálech uživatelů, prostřednictvím nové vlastnosti **Skrýt aplikaci** . V případech, kdy se dlaždice aplikací zobrazují pro back-endové služby nebo pro duplikované dlaždice a v případě spouštěčů aplikací uživatelů bez zbytečného spouštění, můžete aplikace skrýt. Přepínač je v části **vlastnosti** aplikace třetí strany a je označen jako **viditelný pro uživatele?** Aplikaci můžete také programově skrýt prostřednictvím prostředí PowerShell.

Další informace najdete v tématu [skrytí aplikace třetí strany od uživatele ze zkušeností uživatelů v Azure AD](../manage-apps/hide-application-from-user-portal.md).


**Co je k dispozici?**

 V rámci přechodu na novou konzolu správce jsou k dispozici dvě nová rozhraní API pro načítání protokolů aktivit služby Azure AD. Nová sada rozhraní API nabízí lepší funkce pro filtrování a řazení, které poskytují lepší aktivity auditu a přihlašování. Data dříve dostupná prostřednictvím sestav zabezpečení jsou teď přístupná prostřednictvím rozhraní API detekce rizik ochrany identity v Microsoft Graph.


## <a name="september-2017"></a>Září 2017

### <a name="hotfix-for-identity-manager"></a>Oprava hotfix pro nástroj Identity Manager

**Zadejte:** Kategorie změněné **služby funkcí:** **schopnost produktu** Identity Manager: Správa životního cyklu identit

Balíček kumulativní aktualizace hotfix (Build 4.4.1642.0) je k dispozici od 25. září 2017 pro nástroj Identity Manager 2016 Service Pack 1. Tento balíček Shrnutí:

- Řeší problémy a přidává vylepšení.
- Je kumulativní aktualizace, která nahrazuje všechny aktualizace Identity Manager 2016 Service Pack 1 až po Build 4.4.1459.0 pro Identity Manager 2016.
- Vyžaduje, abyste měli nástroj Identity Manager 2016 Build 4.4.1302.0.

Další informace najdete v tématu [kumulativní balíček oprav hotfix (Build 4.4.1642.0) pro nástroj Identity Manager 2016 Service Pack 1](https://support.microsoft.com/help/4021562).

---