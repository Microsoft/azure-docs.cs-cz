---
title: Článek Index pro správu aplikací v Azure Active Directory | Microsoft Azure
description: Zjistěte, jak přizpůsobit datum vypršení platnosti federačních certifikátů a jak obnovit certifikáty, které budou brzy vyprší.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 38e5e37111c6996fcf1342b123b5fc034b58fc0b
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39430990"
---
# <a name="article-index-for-application-management-in-azure-active-directory"></a>Rejstřík článků o správě aplikací ve službě Azure Active Directory
Tato stránka obsahuje úplný seznam všech dokumentech zapisovat o různé funkce související s aplikací v Azure Active Directory (Azure AD).

Zde je stručný úvod do každé oblasti hlavní funkce, jakož i pokyny, které články ke čtení, v závislosti na tom, jaké informace hledáte.

## <a name="overview-articles"></a>Přehledové články
Následující články jsou dobré počáteční body pro ty, kteří pouze stručné vysvětlení funkcí správy aplikací Azure AD.

| Průvodce článku |  |
|:---:| --- |
| Úvod do správy problémy aplikací, které řeší Azure AD |[Správa aplikací pomocí Azure Active Directory (AD)](manage-apps/what-is-application-management.md) |
| Přehled různých funkcí ve službě Azure AD, vztahuje k povolování jednotného přihlašování, definovat, kdo má přístup k aplikacím a jak uživatelé spouštět aplikace |[Přístup k aplikaci a jednotné přihlašování ve službě Azure Active Directory](manage-apps/what-is-single-sign-on.md) |
| Podívejte se na různé kroky při integraci aplikace do služby Azure AD |[Integrace Azure Active Directory s aplikacemi](manage-apps/plan-an-application-integration.md)<br /><br />[Povolení jednotného přihlašování k aplikacím SaaS](manage-apps/configure-single-sign-on-portal.md)<br /><br />[Správa přístupu k aplikacím](manage-apps/what-is-access-management.md) |
| Technické vysvětlení, jak je aplikace reprezentovaná ve službě Azure AD |[Jak a proč se aplikace přidávají do služby Azure AD](active-directory-how-applications-are-added.md) |

## <a name="troubleshooting-articles"></a>Články o řešení problémů
Tato část poskytuje rychlý přístup k příslušné Průvodce řešením potíží. Další informace o každé oblasti funkcí najdete ve zbývající části této stránky.

| Oblast funkcí |  |
|:---:| --- |
| Federované jednotné přihlašování |[Řešení potíží s založené na SAML jednotného přihlašování](active-directory-saml-debugging.md) |
| Založené na heslech jednotného přihlašování |[Řešení potíží s rozšíření přístupového panelu pro aplikaci Internet Explorer](active-directory-saas-ie-troubleshooting.md) |
| Proxy aplikací |[Průvodce odstraňováním potíží pro Proxy aplikací](manage-apps/application-proxy-troubleshoot.md) |
| Jednotné přihlašování mezi místním AD a Azure AD |[Řešení potíží s synchronizaci hodnot Hash hesel](connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md#troubleshoot-password-hash-synchronization)<br /><br />[Řešení potíží se zpětným zápisem hesla](authentication/active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| Dynamické členství ve skupinách |[Řešení potíží s dynamické členství ve skupinách](users-groups-roles/groups-troubleshooting.md) |

## <a name="single-sign-on-sso"></a>Jednotné přihlašování
### <a name="federated-single-sign-on-sign-into-many-apps-using-one-identity"></a>Federované jednotné přihlašování: Přihlaste se k použití jedné identity velký počet aplikací
Jednotné přihlašování umožňuje uživatelům přístup k různým aplikacím a službám pomocí pouze jednu sadu přihlašovacích údajů. Federace se nachází jedna metoda, pomocí kterého můžete povolit jednotné přihlašování. Při pokusu o přihlášení k federovaným aplikacím uživatelům, budou získat přesměrováni na jejich organizaci oficiální přihlašovací stránky zobrazené pomocí Azure Active Directory a se pak přesměrují do aplikace po úspěšném ověření.

| Průvodce článku |  |
|:---:| --- |
| Úvod k federaci a další typy přihlašování |[Jednotné přihlašování s Azure AD](manage-apps/what-is-single-sign-on.md) |
| Tisíce aplikací SaaS, které jsou předem integrovány se službou Azure AD s zjednodušené kroky konfigurace jednotného přihlašování |[Začínáme s Galerie aplikací Azure AD](manage-apps/what-is-single-sign-on.md#get-started-with-the-azure-ad-application-gallery)<br /><br />[Úplný seznam předem integrovaných aplikací, které podporují federace](saas-apps/tutorial-list.md)<br /><br />[Postup přidání aplikace do Galerie aplikací Azure AD](active-directory-app-gallery-listing.md) |
| Víc než 150 kurzy aplikací o tom, jak nakonfigurovat jednotné přihlašování pro aplikace, jako [Salesforce](saas-apps/salesforce-tutorial.md), [ServiceNow](saas-apps/servicenow-tutorial.md), [Google Apps](saas-apps/google-apps-tutorial.md), [Workday](saas-apps/workday-tutorial.md)a mnoho dalšího |[Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](saas-apps/tutorial-list.md) |
| Jak ručně nastavit a přizpůsobit vaší konfigurace jednotného přihlašování |[Jak do nakonfigurovat federované jednotné přihlašování k aplikacím, které nejsou v galerii aplikací Azure Active Directory](application-config-sso-how-to-configure-federated-sso-non-gallery.md)<br /><br />[Přizpůsobení deklarací identity vystavených v tokenu SAML pro předběžně integrované aplikace](active-directory-saml-claims-customization.md) |
| Průvodce odstraňováním potíží pro federované aplikace, které používají protokol SAML |[Řešení potíží s založené na SAML jednotného přihlašování](active-directory-saml-debugging.md) |
| Jak nakonfigurovat datum vypršení platnosti certifikátu vaší aplikace a jak obnovit své certifikáty |[Správa certifikátů pro federované jednotné přihlašování ve službě Azure Active Directory](manage-apps/manage-certificates-for-federated-single-sign-on.md) |

Federované jednotné přihlašování je k dispozici pro všechny edice Azure AD pro až do deseti aplikací na uživatele. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) podporuje neomezený počet aplikací. Pokud má vaše organizace [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) nebo [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), pak můžete [pomocí skupin můžete přiřadit přístup k federovaným aplikacím](#managing-access-to-applications).

### <a name="password-based-single-sign-on-account-sharing-and-sso-for-non-federated-apps"></a>Založené na heslech Single Sign-On: sdílení účtů a jednotné přihlašování pro jiné než federované aplikace
Pokud chcete povolit jednotné přihlašování k aplikacím, které nepodporují federace, Azure AD nabízí funkce pro správu hesel, které můžete bezpečně ukládat hesla k aplikacím SaaS a automaticky přihlášení uživatelů do těchto aplikací. Můžete snadno distribuovat přihlašovací údaje pro nově vytvořené účty a sdílet účty týmu s více uživateli. Uživatelé nemusí nutně znát přihlašovací údaje, které se, že získá přístup k účtům.

| Průvodce článku |  |
|:---:| --- |
| Úvod do jednotné přihlašování funguje jak založené na heslech a technický přehled (BRIEF) |[Založené na heslech jednotné přihlašování s Azure AD](manage-apps/what-is-single-sign-on.md#password-based-single-sign-on) |
| Přehled scénáře týkající se účtu pro sdílení obsahu a jak se tyto problémy vyřešit pomocí Azure AD |[Sdílení účtů pomocí Azure AD](active-directory-sharing-accounts.md) |
| Automaticky změňte heslo pro některé aplikace v pravidelných intervalech |[Automatická změna hesel (preview)](https://blogs.technet.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/) |
| Nasazení a řešení potíží s pokyny pro aplikaci Internet Explorer verze rozšíření pro správu hesel Azure AD |[Jak nasadit rozšíření přístupového panelu pro aplikaci Internet Explorer pomocí zásad skupiny](active-directory-saas-ie-group-policy.md)<br /><br />[Řešení potíží s rozšíření přístupového panelu pro aplikaci Internet Explorer](active-directory-saas-ie-troubleshooting.md) |

Založené na heslech jednotného přihlašování je k dispozici pro všechny edice Azure AD pro až do deseti aplikací na uživatele. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) podporuje neomezený počet aplikací. Pokud má vaše organizace [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) nebo [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), pak můžete [pomocí skupin můžete přiřadit přístup k aplikacím](#managing-access-to-applications). Automatická změna hesel je [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) funkce.

### <a name="app-proxy-single-sign-on-and-remote-access-to-on-premises-applications"></a>Proxy aplikací: Jednotné přihlašování a vzdálený přístup k místním aplikacím
Pokud máte aplikace v soukromé síti, potřebujete přístup uživatele a zařízení mimo síť, můžete použít Proxy aplikace služby Azure AD povolit zabezpečený vzdálený přístup k těmto aplikacím.

| Průvodce článku |  |
|:---:| --- |
| Přehled služby Azure AD Application Proxy a jak to funguje |[Poskytuje zabezpečený vzdálený přístup k místním aplikacím](manage-apps/application-proxy.md) |
| Kurzy o tom, jak nakonfigurovat Proxy aplikací a publikování vaší první aplikace |[Jak nastavit Proxy aplikace služby Azure AD](manage-apps/application-proxy-enable.md)<br /><br />[Postup při bezobslužné instalaci konektoru Proxy aplikace](manage-apps/application-proxy-register-connector-powershell.md)<br /><br />[Postup publikování aplikací pomocí Proxy aplikací](manage-apps/application-proxy-publish-azure-portal.md)<br /><br />[Jak používat vlastní název domény](manage-apps/application-proxy-configure-custom-domain.md) |
| Jak povolit jednotné přihlašování a podmíněný přístup pro aplikace publikované pomocí Proxy aplikace |[Single-sign-on pomocí Proxy aplikace](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)<br /><br />[Podmíněný přístup a Proxy aplikací](manage-apps/application-proxy-integrate-with-sharepoint-server.md) |
| Pokyny k používání Proxy aplikace v následujících scénářích |[Jak podporovat nativní klientské aplikace](manage-apps/application-proxy-configure-native-client-application.md)<br /><br />[Jak podporovat s deklaracemi identity aplikace](manage-apps/application-proxy-configure-for-claims-aware-applications.md)<br /><br />[Jak podporovat aplikace, které jsou publikovány v samostatných sítích a umístění](manage-apps/application-proxy-connector-groups.md) |
| Průvodce odstraňováním potíží pro Proxy aplikací |[Průvodce odstraňováním potíží pro Proxy aplikací](manage-apps/application-proxy-troubleshoot.md) |

Proxy aplikací je k dispozici pro všechny edice Azure AD pro až do deseti aplikací na uživatele. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) podporuje neomezený počet aplikací. Pokud má vaše organizace [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) nebo [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), pak můžete [pomocí skupin můžete přiřadit přístup k aplikacím](#managing-access-to-applications).

Vás také zajímat [Azure AD Domain Services](../active-directory-domain-services/active-directory-ds-overview.md), která vám umožňuje migrovat místní aplikace do Azure a současně stále uspokojit požadavky identity těchto aplikací.

### <a name="enabling-single-sign-on-between-azure-ad-and-on-premises-ad"></a>Povolení jednotného přihlašování mezi Azure AD a místní AD
Pokud vaše organizace udržuje Windows Server Active Directory v místním prostředí, spolu s Azure Active Directory v cloudu, pak budete pravděpodobně chtít povolit jednotné přihlašování mezi těmito dvěma systémy. Azure AD Connect (nástroj, který integruje tyto dva systémy společně) poskytuje několik možností pro nastavení jednotné přihlašování: vytvořit federaci se službou AD FS nebo jiného zprostředkovatele federace, nebo povolení synchronizace hesel.

| Průvodce článku |  |
|:---:| --- |
| Nabízí přehled o možnostech jednotné přihlašování v Azure AD Connect, jakož i informace o správě hybridní prostředí |[Přihlášení uživatele možnosti ve službě Azure AD Connect](active-directory-aadconnect-user-signin.md) |
| Obecné pokyny pro správu prostředí se službami v místním Active Directory a Azure Active Directory |[Aspekty návrhu hybridní služby Azure AD Identity](active-directory-hybrid-identity-design-considerations-overview.md)<br /><br />[Integrace místních identit s Azure Active Directory](active-directory-aadconnect.md) |
| Pokyny k používání synchronizace hesel pro povolení jednotného přihlašování |[Implementace synchronizace hesel se službou Azure AD Connect](connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)<br /><br />[Řešení potíží s synchronizace hesel](https://support.microsoft.com/en-us/kb/2855271) |
| Pokyny k povolení jednotného přihlašování pomocí zpětného zápisu hesla |[Začínáme se správou hesel ve službě Azure AD](authentication/quickstart-sspr.md)<br /><br />[Řešení potíží se zpětným zápisem hesla](authentication/active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| Pokyny k povolení jednotného přihlašování pomocí poskytovatelů identit třetích stran |[Seznam zprostředkovatelů Identity kompatibilní třetích stran, které slouží k povolení jednotného přihlašování](https://aka.ms/ssoproviders) |
| Jak uživatelé Windows 10 budou moct využívat výhod jednotného přihlašování přes Azure AD Join |[Rozšíření možností cloudu u Windows 10 zařízení prostřednictvím služby Azure Active Directory Join](active-directory-azureadjoin-overview.md) |

Azure AD Connect je k dispozici pro [všechny edice Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). Samoobslužné resetování hesla Azure AD je k dispozici pro [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) a [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/). Zpětný zápis hesla do místní AD je [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) funkce.

### <a name="conditional-access-enforce-additional-security-requirements-for-high-risk-apps"></a>Podmíněného přístupu: Další požadavky na zabezpečení pro aplikace s vysokým rizikem pro vynucení
Jakmile nastavíte jednotné přihlašování k aplikacím a prostředkům, můžete pak dále svázat citlivých aplikací tím, že vynucuje specifické požadavky na zabezpečení v každé přihlášení do této aplikace. Například můžete použít Azure AD na vyžádání, že veškerý přístup k dané aplikace vždy vyžadují ověřování službou Multi-Factor Authentication, bez ohledu na to, jestli tato aplikace podporuje innately, které tuto funkci. Dalším běžným příkladem podmíněného přístupu je vyžadují, aby uživatelé připojen k důvěryhodné síti organizace za účelem přístupu k zvláště citlivé aplikaci.

| Průvodce článku |  |
|:---:| --- |
| Nabízí úvod do možnosti podmíněného přístupu v Azure AD, Office 365 a Intune |[Řízení rizik pomocí podmíněného přístupu](active-directory-conditional-access-azure-portal.md) |
| Jak povolit podmíněný přístup pro následující typy prostředků |[Podmíněný přístup pro aplikace SaaS](active-directory-conditional-access-azure-portal-get-started.md)<br /><br />[Podmíněný přístup pro služby Office 365](active-directory-conditional-access-device-policies.md)<br /><br />[Podmíněný přístup pro místní aplikace](active-directory-conditional-access-azure-portal.md)<br /><br />[Podmíněný přístup pro místní aplikace publikované prostřednictvím Proxy aplikací Azure AD](manage-apps/application-proxy-integrate-with-sharepoint-server.md) |
| Postup při registraci zařízení s Azure Active Directory, chcete-li povolit zásady podmíněného přístupu podle zařízení |[Přehled služby Azure Active Directory Device Registration](active-directory-conditional-access-device-registration-overview.md)<br /><br />[Zařízení s Windows připojená k povolení automatické registrace zařízení pro doménu](active-directory-conditional-access-automatic-device-registration.md)<br />– [Zařízení kroky pro Windows 8.1](active-directory-conditional-access-automatic-device-registration-setup.md)<br />– [Zařízení kroky pro Windows 7](active-directory-conditional-access-automatic-device-registration-setup.md) |

| Použití aplikace Microsoft Authenticator pro dvoustupňové ověřování. | [Microsoft Authenticator](user-help/microsoft-authenticator-app-how-to.md) |

Je podmíněný přístup [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) funkce.

## <a name="apps--azure-ad"></a>Aplikace a služby Azure AD
### <a name="cloud-discovery-find-which-saas-apps-are-being-used-in-your-organization"></a>Cloud Discovery: Vyhledání aplikace SaaS, které se používají ve vaší organizaci
Cloud Discovery analyzuje protokoly přenosů oproti katalogu cloudových aplikací Microsoft Cloud App Security z více než 16 000 cloudových aplikací s řazením a skóre na základě více než 70 rizikových faktorů, poskytovat přehled o cloudových použití, stínového IT a riziko Představuje stínové IT ve vaší organizaci.

| Průvodce článku |  |
|:---:| --- |
| Obecný přehled o tom, jak to funguje |[Nastavení Cloud Discovery](/cloud-app-security/set-up-cloud-discovery) |


### <a name="automatically-provision-and-deprovision-user-accounts-in-saas-apps"></a>Automaticky zřizovat a zrušit jejich zřízení uživatelských účtů v aplikacích SaaS
Automatizujte vytváření, údržbu a odebírání uživatelských identit v aplikacích SaaS, jako je například Dropbox, Salesforce, ServiceNow a další. Odpovídá a synchronizovat existujících identit mezi službami Azure AD a aplikacemi SaaS a řízení přístupu tím, že když někteří uživatelé opustí organizaci automaticky zakážete účty.

| Průvodce článku |  |
|:---:| --- |
| Další informace o tom, jak funguje a najít odpovědi na nejčastější dotazy |[Automatizace zřizování a jeho rušení pro aplikace SaaS uživatele](active-directory-saas-app-provisioning.md) |
| Nakonfigurujte, jak je mapována informace mezi službami Azure AD a vaše aplikace SaaS |[Přizpůsobení mapování atributů](active-directory-saas-customizing-attribute-mappings.md)<br><br>[Zápis výrazů pro mapování atributů](active-directory-saas-writing-expressions-for-attribute-mappings.md) |
| Povolení automatického zřizování pro každou aplikaci, která podporuje protokol SCIM |[Nastavení automatické zřizování uživatelů pro každou aplikaci SCIM-Enabled](manage-apps/use-scim-to-provision-users-and-groups.md) |
| Postup vykázat a řešení potíží s zřizování uživatelů |[Vytváření sestav pro automatické zřizování uživatelů](active-directory-saas-provisioning-reporting.md)<br><br>[Řešení potíží s zřizování uživatelů](active-directory-application-provisioning-content-map.md) |
| Omezení toho, kdo získá přiřazeni k aplikaci na základě jejich hodnot atributu |[Filtry oborů](active-directory-saas-scoping-filters.md) |

Zřizování automatizované uživatelů je k dispozici pro všechny edice Azure AD pro až do deseti aplikací na uživatele. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) podporuje neomezený počet aplikací. Pokud má vaše organizace [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) nebo [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), pak můžete [použití skupin pro správu, kteří uživatelé se zřídil](#managing-access-to-applications).

### <a name="building-applications-that-integrate-with-azure-ad"></a>Vytváření aplikací, které se integrují s Azure AD
Pokud vaše organizace je vývoj nebo udržování – obchodní aplikace (LoB), nebo pokud jste vývojář aplikací se zákazníky, kteří používají Azure Active Directory, vám pomůže v následujících kurzech integrujte své aplikace pomocí Azure AD.

| Průvodce článku |  |
|:---:| --- |
| Doprovodné materiály pro IT profesionály a vývojáře aplikací na integrace aplikací s Azure AD |[IT specialisté příručky pro vývoj aplikací pro službu Azure AD](active-directory-applications-guiding-developers-for-lob-applications.md)<br /><br />[Příručka pro vývojáře pro službu Azure Active Directory](develop/azure-ad-developers-guide.md) |
| Jak do aplikace dodavatelů můžete přidat svoje aplikace do Galerie aplikací Azure AD |[Zobrazení aplikace v galerii aplikací Azure Active Directory](active-directory-app-gallery-listing.md) |
| Jak spravovat přístup k vyvinuté aplikace pomocí Azure Active Directory |[Jak povolit přiřazení uživatelů pro aplikace vyvinuté](active-directory-applications-guiding-developers-requiring-user-assignment.md)<br /><br />[Přiřazování uživatelů do vaší aplikace](active-directory-applications-guiding-developers-assigning-users.md)<br /><br />[Přiřazení skupiny k aplikaci](active-directory-applications-guiding-developers-assigning-groups.md) |

Pokud vyvíjíte zákaznické aplikace, může zajímat pomocí [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) tak, že není nutné vyvíjet vlastní systém identit, ke správě uživatelů. [Další informace](../active-directory-b2c/active-directory-b2c-overview.md).

## <a name="managing-access-to-applications"></a>Správa přístupu k aplikacím
### <a name="using-groups-and-self-service-to-manage-who-has-access-to-which-apps"></a>Používání skupin a samoobslužné funkce pro správu, který má přístup k aplikacím, které
Můžete spravovat, kdo má k jakým prostředkům přístup, umožňuje Azure Active Directory nastavit přiřazení a oprávnění ve velkém měřítku pomocí skupin. IT může vybrat povolení samoobslužné funkce tak, aby uživatelé mohou jednoduše požadovat oprávnění, kdy ho potřebují.

| Průvodce článku |  |
|:---:| --- |
| Přehled funkce řízení přístupu Azure AD |[Úvod ke správě přístupu k aplikacím](manage-apps/what-is-access-management.md)<br /><br />[Jak funguje správa přístupu v Azure AD](fundamentals/active-directory-manage-groups.md)<br /><br />[Použití skupin ke správě přístupu k aplikacím SaaS](users-groups-roles/groups-saasapps.md) |
| Povolení samoobslužné správy aplikací a skupin |[Správa samoobslužných služeb aplikací](active-directory-self-service-application-access.md)<br /><br />[Samoobslužná správa skupin](users-groups-roles/groups-self-service-management.md) |
| Pokyny k nastavení skupiny ve službě Azure AD |[Vytvoření skupin zabezpečení](fundamentals/active-directory-groups-create-azure-portal.md)<br /><br />[Jak určit vlastníků pro skupinu](fundamentals/active-directory-accessmanagement-managing-group-owners.md)<br /><br />[Jak používat "všichni uživatelé"](active-directory-accessmanagement-dedicated-groups.md) |
| Použít k automatickému vyplnění členství ve skupině pomocí pravidel založených na atributech členství dynamické skupiny |[Dynamického členství: Rozšířená pravidla](active-directory-groups-dynamic-membership-azure-portal.md)<br /><br />[Řešení potíží s dynamické členství ve skupinách](users-groups-roles/groups-troubleshooting.md) |

Správa přístupu na základě skupin aplikací je k dispozici pro [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) a [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/). Samoobslužná správa skupin, Správa samoobslužných služeb aplikací a dynamické skupiny jsou [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) funkce.

### <a name="b2b-collaboration-enable-partner-access-to-applications"></a>Spolupráce B2B: Povolení přístupu partnerů k aplikacím
Pokud váš podnik uzavřel partnerství s jinými společnostmi dohody, je pravděpodobné, že je potřeba spravovat přístupu partnerů k vašim podnikovým aplikacím. Azure Active Directory B2B spolupráce poskytuje snadný a bezpečný způsob sdílení aplikací s partnery.

| Průvodce článku |  |
|:---:| --- |
| Přehled různých služeb Azure AD nabízí, můžete snadněji spravovat externích uživatelů, jako partneři, zákazníci atd. |[Porovnání možností správy externí identity ve službě Azure AD](active-directory-b2b-compare-external-identities.md) |
| Úvod do spolupráce B2B a jak začít |[Jednoduchý, zabezpečený Cloud integrace partnerských řešení pomocí Azure AD](active-directory-b2b-what-is-azure-ad-b2b.md)<br /><br />[Spolupráce B2B ve službě Azure Active Directory](active-directory-b2b-collaboration-overview.md) |
| Prozkoumejte podrobněji spolupráce B2B ve službě Azure AD a jak ji používat |[Spolupráce B2B: Jak to funguje](active-directory-b2b-how-it-works.md)<br /><br />[Aktuální omezení spolupráce Azure AD B2B](active-directory-b2b-current-limitations.md)<br /><br />[Podrobný návod k používání spolupráce B2B ve službě Azure AD](active-directory-b2b-detailed-walkthrough.md) |
| Odkaz na články s technické podrobnosti o tom, jak spolupráce B2B ve službě Azure AD funguje |[Formát souboru CSV pro přidání partnerů](active-directory-b2b-references-csv-file-format.md)<br /><br />[Atributy uživatele, které jsou ovlivněny spolupráce Azure AD B2B](active-directory-b2b-references-external-user-object-attribute-changes.md)<br /><br />[Formát tokenu uživatele pro uživatele partnera](active-directory-b2b-references-external-user-token-format.md) |

Spolupráce B2B je momentálně dostupná pro [všechny edice Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="access-panel-a-portal-for-accessing-apps-and-self-service-features"></a>Přístupový Panel: Portál pro přístup k aplikacím a samoobslužných funkcí
Panel přístupu Azure AD je, kde můžou koncoví uživatelé spouštění svých aplikací a přístup k funkcím samoobslužných služeb, které zajistí, aby spravovat jejich aplikace a členství ve skupinách. Kromě přístupovém panelu a další možnosti pro přístup k aplikacím s podporou jednotného přihlašování jsou zahrnuty v níže uvedeném seznamu.

| Průvodce článku |  |
|:---:| --- |
| Porovnání různých možností dostupných pro nasazování aplikací jednotné přihlašování pro uživatele |[Nasazení služby Azure AD integrovaných aplikací pro uživatele](manage-apps/what-is-single-sign-on.md#deploying-azure-ad-integrated-applications-to-users) |
| Přehled na přístupovém panelu a jeho mobilní ekvivalentní MyApps |[Úvod do přístupového panelu a MyApps](user-help/active-directory-saas-access-panel-introduction.md)<br />— [iOS](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8)<br />– [s androidem](https://play.google.com/store/apps/details?id=com.microsoft.myapps) |
| Jak získat přístup k aplikacím Azure AD na webu Office 365 |[Pomocí Spouštěč aplikací Office 365](https://support.office.com/en-us/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a) |
| Jak získat přístup k aplikacím Azure AD z mobilní aplikace Intune Managed Browser |[Intune Managed Browser](https://technet.microsoft.com/library/dn878029.aspx)<br />— [iOS](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8)<br />– [s androidem](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser) |
| Jak získat přístup k Azure AD aplikace s využitím přímé odkazy pro zahájení jednotného přihlašování |[Získat přímé odkazy přihlašování k aplikacím](manage-apps/what-is-single-sign-on.md#direct-sign-on-links-for-federated-password-based-or-existing-apps) |

Přístupový Panel je k dispozici pro [všechny edice Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="reports-easily-audit-app-access-changes-and-monitor-sign-ins-to-apps"></a>Sestavy: Snadno auditovat změny aplikace access a sledovat přihlášení do aplikace
Azure Active Directory poskytuje několik sestav a výstrahám, které vám pomůžou monitorovat vaše organizace umožňuje přístup k aplikacím. Můžete dostávat upozornění na neobvyklé přihlašovací aktivity pro vaše aplikace a můžete sledovat, kdy a proč byla změněna uživatelům přístup k aplikaci.

| Průvodce článku |  |
|:---:| --- |
| Přehled funkce generování sestav v Azure Active Directory |[Začínáme s vytvářením sestav Azure AD](active-directory-reporting-getting-started.md) |
| Jak monitorovat přihlášení a uživatelů využití aplikací |[Zobrazit sestavy využití a přístupu](active-directory-view-access-usage-reports.md) |
| Sledovat změny provedené, kdo má přístup k určité aplikaci |[Události sestavy auditování služby Azure Active Directory](active-directory-reporting-audit-events.md) |
| Exportovat data z těchto sestav do vašich oblíbených nástrojů, pomocí rozhraní API pro generování sestav |[Začínáme s API pro generování sestav Azure AD](active-directory-reporting-api-getting-started.md) |

Chcete-li zjistit, jaké zprávy jsou zahrnuty v různých edicích služby Azure Active Directory, [kliknutím sem](active-directory-view-access-usage-reports.md).

## <a name="see-also"></a>Další informace najdete v tématech
[Představení služby Azure Active Directory](fundamentals/active-directory-whatis.md)

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/)

[Azure Active Directory Domain Services](https://azure.microsoft.com/services/active-directory-ds/)

[Azure Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/)
