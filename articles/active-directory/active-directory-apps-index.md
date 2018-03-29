---
title: Článek Index pro správu aplikací v Azure Active Directory | Microsoft Azure
description: Zjistěte, jak přizpůsobit datum vypršení platnosti federačních certifikátů a jak obnovit certifikáty, jejichž platnost brzy vyprší.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 5321b8e4-2afa-4dfe-8d53-4add7abb5ec8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: markvi
ms.reviewer: asteen
ms.openlocfilehash: ffb8e822644ba8f0bccc3e81edc4da6085ff3ec7
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="article-index-for-application-management-in-azure-active-directory"></a>Rejstřík článků o správě aplikací ve službě Azure Active Directory
Tato stránka obsahuje úplný seznam každému dokumentu zapsána o různých funkcích týkající se aplikace v Azure Active Directory (Azure AD).

Je stručný úvod do každé oblasti hlavní funkce, jakož i pokyny na články, které ke čtení, v závislosti na tom, jaké informace se díváte.

## <a name="overview-articles"></a>Přehled článků
Článků níže jsou dobré počáteční body pro ty, kteří pouze stručné vysvětlení funkce správy aplikací Azure AD.

| Článek Průvodce |  |
|:---:| --- |
| Úvod do správy problémů aplikace, které řeší Azure AD |[Správa aplikací pomocí služby Azure Active Directory (AD)](active-directory-enable-sso-scenario.md) |
| Přehled různých funkcí ve službě Azure AD, vztahuje k povolování jednotné přihlašování, definovat, kdo má přístup k aplikacím a jak uživatelé spustí aplikace |[Přístup k aplikaci a jednotné přihlašování v Azure Active Directory](active-directory-appssoaccess-whatis.md) |
| Podívejte se na různé kroky při integraci aplikace do služby Azure AD |[Integrace s aplikacemi Azure Active Directory](active-directory-integrating-applications-getting-started.md)<br /><br />[Povolení jednotného přihlašování k aplikacím SaaS](active-directory-enterprise-apps-manage-sso.md)<br /><br />[Správa přístupu k aplikacím](active-directory-managing-access-to-apps.md) |
| Technické vysvětlení, jak jsou reprezentována aplikací ve službě Azure AD |[Jak a proč se aplikace přidávají do služby Azure AD](active-directory-how-applications-are-added.md) |

## <a name="troubleshooting-articles"></a>Řešení potíží s články
Tato část poskytuje rychlý přístup k příslušné příručky pro řešení potíží. Další informace o jednotlivých oblast funkcí najdete ve zbývající části této stránky.

| Oblast funkcí |  |
|:---:| --- |
| Federované jednotné přihlašování |[Řešení potíží s na základě SAML jednotné přihlašování](active-directory-saml-debugging.md) |
| Založené na heslech jednotné přihlašování |[Řešení potíží s příponou Panel přístupu pro Internet Explorer](active-directory-saas-ie-troubleshooting.md) |
| Proxy aplikací |[Průvodce odstraňováním potíží Proxy aplikace](active-directory-application-proxy-troubleshoot.md) |
| Jednotné přihlašování mezi místní AD a Azure AD |[Řešení potíží s synchronizaci hodnoty Hash hesla](connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md#troubleshoot-password-hash-synchronization)<br /><br />[Řešení potíží s zpětný zápis hesla](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| Dynamické členství ve skupinách |[Řešení potíží s dynamické členství ve skupinách](active-directory-accessmanagement-troubleshooting.md) |

## <a name="single-sign-on-sso"></a>Jednotné přihlašování
### <a name="federated-single-sign-on-sign-into-many-apps-using-one-identity"></a>Federované jednotné přihlašování: Přihlaste se k mnoho aplikací pomocí jednu identitu
Jednotné přihlašování umožňuje uživatelům přístup k celou řadu aplikací a služeb pomocí jen jednu sadu přihlašovacích údajů. Federace se nachází jedna metoda, pomocí kterého můžete povolit jednotné přihlašování. Když uživatelé se pokusí přihlásit k federovaným aplikacím, budou přesměrováni na jejich organizace oficiální přihlašovací stránka vykresluje službou Azure Active Directory a potom přesměrováni zpět do aplikace po úspěšném ověření.

| Článek Průvodce |  |
|:---:| --- |
| Úvod do federační a dalších typů přihlášení |[Jednotné přihlašování s Azure AD](active-directory-appssoaccess-whatis.md) |
| Tisíce aplikacemi SaaS, které jsou předem integrované se službou Azure AD s zjednodušené kroky konfigurace přihlášení |[Začínáme s galerii aplikací Azure AD](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery)<br /><br />[Úplný seznam předem integrovaných aplikací, které podporují Federation](active-directory-saas-tutorial-list.md)<br /><br />[Postup přidání aplikace do galerii aplikací Azure AD](active-directory-app-gallery-listing.md) |
| Víc než 150 aplikace kurzy o tom, jak nakonfigurovat jednotné přihlašování pro aplikace, jako například [Salesforce](active-directory-saas-salesforce-tutorial.md), [ServiceNow](active-directory-saas-servicenow-tutorial.md), [Google Apps](active-directory-saas-google-apps-tutorial.md), [Workday](active-directory-saas-workday-tutorial.md)a mnoho dalších |[Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md) |
| Ruční nastavení a konfiguraci jednoho přihlášení přizpůsobit |[Jak na nakonfigurovat federovaného jednotného přihlašování k aplikacím, které nejsou v galerii aplikací Azure Active Directory](application-config-sso-how-to-configure-federated-sso-non-gallery.md)<br /><br />[Postup přizpůsobení deklarace identity vystavené v tokenu SAML pro předběžně integrované aplikace](active-directory-saml-claims-customization.md) |
| Příručka pro řešení potíží pro federované aplikace, které používají protokol SAML |[Řešení potíží s na základě SAML jednotné přihlašování](active-directory-saml-debugging.md) |
| Postup konfigurace datum vypršení platnosti certifikátu vaší aplikace a jak obnovit certifikáty |[Správa certifikátů pro federované jednotné přihlašování v Azure Active Directory](active-directory-sso-certs.md) |

Federované jednotné přihlašování je k dispozici pro všechny edice Azure AD pro až deset aplikace na uživatele. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) podporuje neomezená aplikace. Pokud má vaše organizace [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) nebo [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), pak můžete [použití skupin pro přidělit přístup k federovaným aplikacím](#managing-access-to-applications).

### <a name="password-based-single-sign-on-account-sharing-and-sso-for-non-federated-apps"></a>Založené na heslech jednotné přihlašování: účet sdílení a jednotné přihlašování pro aplikace, nefederovaných
Umožňující jednotného přihlašování k aplikacím, které nepodporují federační služby Azure AD nabízí funkce správy heslo, které se dají bezpečně uložit hesla k aplikacím SaaS a automaticky přihlášení uživatelé do těchto aplikací. Snadno můžete distribuovat pověření pro nově vytvořené účty a sdílení účtů tým s více uživateli. Uživatelé nepotřebují nutně znáte přihlašovací údaje k účtům, které získá přístup k.

| Článek Průvodce |  |
|:---:| --- |
| Stručný technický přehled a základní informace o tom, jak založené na heslech funguje jednotné přihlašování |[Založené na heslech jednotné přihlašování s Azure AD](active-directory-appssoaccess-whatis.md#password-based-single-sign-on) |
| Souhrn scénáře související s účet sdílení a jak jsou tyto problémy vyřešit pomocí Azure AD |[Sdílení účtů s Azure AD](active-directory-sharing-accounts.md) |
| Automaticky změňte heslo pro určité aplikace v pravidelných intervalech |[Automatizované výměny heslo (preview)](https://blogs.technet.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/) |
| Nasazení a řešení potíží s příručky pro Internet Explorer verze rozšíření správy hesel Azure AD |[Postup nasazení rozšíření Panel přístupu pro Internet Explorer pomocí zásad skupiny](active-directory-saas-ie-group-policy.md)<br /><br />[Řešení potíží s příponou Panel přístupu pro Internet Explorer](active-directory-saas-ie-troubleshooting.md) |

Založené na heslech jednotné přihlašování je k dispozici pro všechny edice Azure AD pro až deset aplikace na uživatele. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) podporuje neomezená aplikace. Pokud má vaše organizace [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) nebo [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), pak můžete [použití skupin pro přiřazení přístupu k aplikacím](#managing-access-to-applications). Výměna automatizované heslo je [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) funkce.

### <a name="app-proxy-single-sign-on-and-remote-access-to-on-premises-applications"></a>Proxy aplikace: Jednotné přihlašování a vzdálený přístup k místním aplikacím
Pokud máte aplikace ve vaší privátní sítě, které je nutné mít přístup uživatelé a zařízení mimo síť, můžete povolit zabezpečený vzdálený přístup pro tyto aplikace použít Azure AD Application Proxy.

| Článek Průvodce |  |
|:---:| --- |
| Přehled proxy aplikace služby Azure AD a jak to funguje |[Poskytuje zabezpečený vzdálený přístup k místním aplikacím](active-directory-application-proxy-get-started.md) |
| Kurzy o tom, jak nakonfigurovat Proxy aplikace a jak publikovat první aplikace |[Jak vytvořit Proxy aplikace Azure AD](active-directory-application-proxy-enable.md)<br /><br />[Postup při bezobslužné instalaci konektoru Proxy aplikace](active-directory-application-proxy-silent-installation.md)<br /><br />[Postup publikování aplikací pomocí Proxy aplikace](active-directory-application-proxy-publish.md)<br /><br />[Jak používat vlastní název domény](active-directory-application-proxy-custom-domains.md) |
| Postup povolení jednoho přihlášení a podmíněného přístupu pro aplikace publikované s Proxy aplikace |[Jednotné přihlášení pomocí Proxy aplikace](active-directory-application-proxy-sso-using-kcd.md)<br /><br />[Podmíněný přístup a Proxy aplikací](application-proxy-enable-remote-access-sharepoint.md) |
| Pokyny k použití aplikace Proxy pro tyto scénáře |[Jak podporovat nativní klientské aplikace](active-directory-application-proxy-native-client.md)<br /><br />[Jak podporovat deklaracemi identity aplikace](active-directory-application-proxy-claims-aware-apps.md)<br /><br />[Jak podporovat aplikace, které jsou publikovány v samostatných sítí a umístění](active-directory-application-proxy-connectors-azure-portal.md) |
| Průvodce řešením potíží pro Proxy aplikace |[Průvodce odstraňováním potíží Proxy aplikace](active-directory-application-proxy-troubleshoot.md) |

Proxy aplikace je k dispozici pro všechny edice Azure AD pro až deset aplikace na uživatele. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) podporuje neomezená aplikace. Pokud má vaše organizace [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) nebo [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), pak můžete [použití skupin pro přiřazení přístupu k aplikacím](#managing-access-to-applications).

Také může být zájem o [Azure AD Domain Services](../active-directory-domain-services/active-directory-ds-overview.md), která vám umožňuje migrovat místní aplikace do Azure při stále splňující potřeby identity těchto aplikací.

### <a name="enabling-single-sign-on-between-azure-ad-and-on-premises-ad"></a>Povolení jednotného přihlašování mezi službou Azure AD a místní AD
Pokud vaše organizace udržuje Windows Server Active Directory místní společně s Azure Active Directory v cloudu, pak budete pravděpodobně chtít povolit jednotné přihlašování mezi těmito dvěma systémy. Azure AD Connect (nástroj, který se integruje se tyto dvě systémy společně) poskytuje několik možností nastavení jednotného přihlašování: navázání federaci se službou AD FS nebo jiného zprostředkovatele federace, nebo povolení synchronizace hesel.

| Článek Průvodce |  |
|:---:| --- |
| Přehled na volby jednotného přihlašování k dispozici v Azure AD Connect a také informace o správě hybridní prostředí |[Přihlášení uživatele na možnosti v Azure AD Connect](active-directory-aadconnect-user-signin.md) |
| Obecné pokyny pro správu prostředí s oběma místní služby Active Directory a Azure Active Directory |[Aspekty návrhu Azure AD hybridní Identity](active-directory-hybrid-identity-design-considerations-overview.md)<br /><br />[Integrace místních identit s Azure Active Directory](active-directory-aadconnect.md) |
| Pokyny k povolení jednotného přihlašování pomocí synchronizace hesla |[Implementace synchronizace hesel s Azure AD Connect](connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)<br /><br />[Řešení potíží s synchronizace hesel](https://support.microsoft.com/en-us/kb/2855271) |
| Pokyny k použití zpětný zápis hesla k povolení přihlášení SSO |[Začínáme se správou hesel ve službě Azure AD](active-directory-passwords-getting-started.md)<br /><br />[Řešení potíží se zpětným zápisem hesla](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| Pokyny k povolení jednotného přihlašování pomocí poskytovatelů identit třetích stran |[Seznam zprostředkovatelů Identity kompatibilní třetích stran, které slouží k povolení jednotného přihlašování](https://aka.ms/ssoproviders) |
| Jak uživatelé Windows 10 můžete využívat výhod jednotné přihlašování přes Azure AD Join |[Rozšíření cloudových funkcí na Windows 10 zařízení pomocí služby Azure Active Directory Join](active-directory-azureadjoin-overview.md) |

Azure AD Connect je k dispozici pro [všechny edice služby Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). Azure AD funkce samoobslužného resetování hesla je k dispozici pro [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) a [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/). Zpětný zápis hesla pro místní AD je [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) funkce.

### <a name="conditional-access-enforce-additional-security-requirements-for-high-risk-apps"></a>Podmíněného přístupu: Dodatečné požadavky na zabezpečení pro aplikace s vysokým rizikem pro vynucení
Jakmile nastavíte jednotné přihlašování k aplikacím a prostředkům, můžete pak další zabezpečit citlivých aplikací vynucením specifické požadavky na zabezpečení v každé přihlášení k aplikaci. Například můžete použít Azure AD na vyžádání, že veškerý přístup k určité aplikaci vždy vyžadovat vícefaktorové ověřování, bez ohledu na to, jestli tuto aplikaci podporuje innately této funkce. Další běžné příklad podmíněného přístupu je potřeba, aby uživatelé připojen k důvěryhodné síti organizace pro přístup k zvláště citlivé aplikaci.

| Článek Průvodce |  |
|:---:| --- |
| Úvod do funkce podmíněného přístupu nabízí přes Azure AD, Office 365 a Intune |[Řízení rizik pomocí podmíněného přístupu](active-directory-conditional-access-azure-portal.md) |
| Postup povolení podmíněného přístupu pro následující typy prostředků |[Podmíněný přístup pro aplikace SaaS](active-directory-conditional-access-azure-portal-get-started.md)<br /><br />[Podmíněný přístup pro služby Office 365](active-directory-conditional-access-device-policies.md)<br /><br />[Podmíněný přístup pro místní aplikace](active-directory-conditional-access-azure-portal.md)<br /><br />[Podmíněný přístup pro místní aplikace publikované prostřednictvím Proxy aplikace Azure AD](application-proxy-enable-remote-access-sharepoint.md) |
| Postup registrace zařízení s Azure Active Directory, chcete-li povolit zásady podmíněného přístupu podle zařízení |[Přehled registrace zařízení služby Azure Active Directory](active-directory-conditional-access-device-registration-overview.md)<br /><br />[Postup povolení automatické registrace zařízení pro doménu připojené zařízení s Windows](active-directory-conditional-access-automatic-device-registration.md)<br />– [Kroky pro Windows 8.1 zařízení](active-directory-conditional-access-automatic-device-registration-setup.md)<br />– [Zařízení kroky pro systém Windows 7](active-directory-conditional-access-automatic-device-registration-setup.md) |

| Jak používat aplikaci Microsoft Authenticator pro dvoustupňové ověření | [Microsoft Authenticator](../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md) |

Podmíněný přístup [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) funkce.

## <a name="apps--azure-ad"></a>Aplikace a služby Azure AD
### <a name="cloud-app-discovery-find-which-saas-apps-are-being-used-in-your-organization"></a>Cloud App Discovery: Najít, které aplikace SaaS jsou používány ve vaší organizaci
Cloud App Discovery pomáhá IT oddělení informace, které aplikace SaaS jsou používány v rámci organizace. Můžete ho měření využití aplikací a oblíbenosti, které můžete určit, které aplikace bude mít prospěch nejvíc nebudou převedené pod řízení IT a probíhá integrované s Azure AD.

| Článek Průvodce |  |
|:---:| --- |
| Obecný přehled o tom, jak funguje |[Vyhledání aplikace neschválená cloudových aplikací s Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md) |
| Podrobnější informace o tom, jak funguje, se odpovědi na dotazy na ochranu osobních údajů |[Zabezpečení a důležité informace o ochraně osobních údajů](active-directory-cloudappdiscovery-security-and-privacy-considerations.md) |
| Nejčastější dotazy |[Nejčastější dotazy ke Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/24037.cloud-app-discovery-frequently-asked-questions.aspx) |
| Podrobné pokyny pro nasazení Cloud App Discovery |[Příručka pro nasazení zásad skupiny](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx)<br /><br />[Průvodci nasazením aplikace System Center](http://social.technet.microsoft.com/wiki/contents/articles/30968.cloud-app-discovery-system-center-deployment-guide.aspx)<br /><br />[Instalace na Proxy serverech s vlastní porty](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md) |
| Protokol změn aktualizace agenta Cloud App Discovery |[Protokol změn](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx) |

Cloud App Discovery je [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) funkce.

### <a name="automatically-provision-and-deprovision-user-accounts-in-saas-apps"></a>Automaticky zřizovat a zrušit jejich zřízení uživatelských účtů ve SaaS aplikace
Automatizovat vytváření, údržbu a odebírání uživatelských identit v aplikace SaaS, jako je Dropbox, Salesforce, ServiceNow a další. Odpovídající a synchronizovat existující identit mezi službou Azure AD a aplikace SaaS a řízení přístupu tím, že když někteří uživatelé opustí organizaci automaticky zakážete účty.

| Článek Průvodce |  |
|:---:| --- |
| Další informace o tom, jak funguje a najděte odpovědi na nejčastější dotazy |[Automatizovat uživatele zřizování & zrušení zřízení k aplikacím SaaS](active-directory-saas-app-provisioning.md) |
| Nakonfigurujte, jak je namapovaný informace mezi službou Azure AD a aplikace SaaS |[Přizpůsobení mapování atributů](active-directory-saas-customizing-attribute-mappings.md)<br><br>[Zapisují se výrazy pro mapování atributů](active-directory-saas-writing-expressions-for-attribute-mappings.md) |
| Postup povolení automatického zřizování do žádné aplikace, který podporuje protokol SCIM |[Nastavit automatické zřizování uživatelů do všech aplikací SCIM-Enabled](active-directory-scim-provisioning.md) |
| Vytvoření sestavy a odstraňování souvisejících potíží zřizování uživatelů |[Zprávy o zřizování automatické uživatelů](active-directory-saas-provisioning-reporting.md)<br><br>[Řešení potíží s zřizování uživatelů](active-directory-application-provisioning-content-map.md) |
| Omezení, kdo získá zřídit aplikace na základě jejich hodnot atributů |[Filtry oborů](active-directory-saas-scoping-filters.md) |

Zřizování automatizované uživatelů je k dispozici pro všechny edice Azure AD pro až deset aplikace na uživatele. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) podporuje neomezená aplikace. Pokud má vaše organizace [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) nebo [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), pak můžete [použití skupin pro správu, kteří uživatelé získat zřízený](#managing-access-to-applications).

### <a name="building-applications-that-integrate-with-azure-ad"></a>Vytváření aplikací, které se integrují s Azure AD
Pokud vaše organizace je vývoj nebo udržování-obchodní aplikace (LoB), nebo pokud jste vývojář aplikace se zákazníky, kteří používají Azure Active Directory, vám pomůže následující kurzy integraci vašich aplikací s Azure AD.

| Článek Průvodce |  |
|:---:| --- |
| Pokyny pro odborníky v oblasti IT a vývojáři aplikace k integraci aplikací s Azure AD |[IT specialisté Průvodce pro vývoj aplikací pro Azure AD](active-directory-applications-guiding-developers-for-lob-applications.md)<br /><br />[Příručka pro vývojáře pro Azure Active Directory](active-directory-developers-guide.md) |
| Jak do aplikace dodavatelé můžete přidat svoje aplikace galerii aplikací Azure AD |[Výpis aplikace v galerii aplikací Azure Active Directory](active-directory-app-gallery-listing.md) |
| Jak spravovat přístup vyvinuté aplikací pomocí služby Azure Active Directory |[Postup povolení přiřazení uživatelů pro aplikace vyvinuté](active-directory-applications-guiding-developers-requiring-user-assignment.md)<br /><br />[Přiřazování uživatelů do vaší aplikace](active-directory-applications-guiding-developers-assigning-users.md)<br /><br />[Přiřazení skupiny do vaší aplikace](active-directory-applications-guiding-developers-assigning-groups.md) |

Pokud vyvíjíte aplikace určených, může zajímat pomocí [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) tak, že nemáte vyvíjet vlastní identitu systému a spravovat vaše uživatele. [Další informace](../active-directory-b2c/active-directory-b2c-overview.md).

## <a name="managing-access-to-applications"></a>Správa přístupu k aplikacím
### <a name="using-groups-and-self-service-to-manage-who-has-access-to-which-apps"></a>Pomocí skupin a samoobslužné služby pro správu, který má přístup k aplikacím, které
Můžete spravovat, kdo by měl mít přístup k jakým prostředkům, Azure Active Directory umožňuje nastavit oprávnění a přiřazení škálované pomocí skupin. IT může vybrat možnost povolení samoobslužných funkcí tak, aby uživatelé můžete jednoduše požadovat oprávnění, když ji potřebují.

| Článek Průvodce |  |
|:---:| --- |
| Přehled funkcí řízení přístupu Azure AD |[Úvod do správy přístupu k aplikacím](active-directory-managing-access-to-apps.md)<br /><br />[Jak funguje správa přístupu ve službě Azure AD](active-directory-manage-groups.md)<br /><br />[Postup použití skupin pro správu přístupu k aplikacím SaaS](active-directory-accessmanagement-group-saasapps.md) |
| Povolení samoobslužné správy skupin a aplikací |[Správa aplikací samoobslužné služby](active-directory-self-service-application-access.md)<br /><br />[Samoobslužná správa skupin](active-directory-accessmanagement-self-service-group-management.md) |
| Pokyny k nastavení skupin ve službě Azure AD |[Postup vytvoření skupin zabezpečení](active-directory-groups-create-azure-portal.md)<br /><br />[Postup určení vlastníků pro skupinu](active-directory-accessmanagement-managing-group-owners.md)<br /><br />[Jak používat skupiny "Všichni uživatelé"](active-directory-accessmanagement-dedicated-groups.md) |
| Použití dynamických skupin se automaticky naplňte členství skupiny pomocí pravidla členství na základě atributů |[Členství ve skupině dynamické: Rozšířená pravidla](active-directory-groups-dynamic-membership-azure-portal.md)<br /><br />[Řešení potíží s dynamické členství ve skupinách](active-directory-accessmanagement-troubleshooting.md) |

Správa přístupu na základě skupin aplikací je k dispozici pro [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) a [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/). Samoobslužná správa skupin, Správa aplikací samoobslužné služby a dynamických skupin se [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) funkce.

### <a name="b2b-collaboration-enable-partner-access-to-applications"></a>Spolupráce B2B: Umožnit partnera přístup k aplikacím
Pokud váš podnik má ve spolupráci s jinými společnostmi, je pravděpodobné, že budete muset spravovat partnera přístup k podnikovým aplikacím. Azure Active Directory spolupráce B2B ve poskytuje snadný a bezpečný způsob vaše aplikace sdílet s partnery.

| Článek Průvodce |  |
|:---:| --- |
| Přehled různých Azure AD funkce, že můžete snadněji tak můžete spravovat externích uživatelů, jako partnerům, zákazníky, atd. |[Porovnání funkcí pro správu externí identity ve službě Azure AD](active-directory-b2b-compare-external-identities.md) |
| Úvod do spolupráce B2B a jak začít |[Jednoduchý, zabezpečení, cloudu partnera integraci s Azure AD](active-directory-b2b-what-is-azure-ad-b2b.md)<br /><br />[Spolupráce B2B Azure Active Directory](active-directory-b2b-collaboration-overview.md) |
| O podrobnější prohlídku do spolupráce Azure AD B2B a způsobu jeho použití |[Spolupráce B2B: Jak to funguje](active-directory-b2b-how-it-works.md)<br /><br />[Aktuální omezení spolupráce Azure AD B2B](active-directory-b2b-current-limitations.md)<br /><br />[Podrobný návod k používání spolupráce Azure AD B2B](active-directory-b2b-detailed-walkthrough.md) |
| Odkaz na články s technické informace o tom, jak funguje spolupráce Azure AD B2B |[Formát souboru CSV pro přidávání uživatelů partnera](active-directory-b2b-references-csv-file-format.md)<br /><br />[Uživatelské atributy vliv spolupráce Azure AD B2B](active-directory-b2b-references-external-user-object-attribute-changes.md)<br /><br />[Formát tokenu uživatele pro uživatele partnera](active-directory-b2b-references-external-user-token-format.md) |

Spolupráce B2B je aktuálně dostupné pro [všechny edice služby Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="access-panel-a-portal-for-accessing-apps-and-self-service-features"></a>Přístupový Panel: Portál pro přístup k aplikacím a samoobslužných funkcí
Přístupový Panel Azure AD je, kde se koncoví uživatelé můžou spouštět svoje aplikace a přístup k samoobslužné funkce, které mohly ke správě svých aplikací a členství ve skupinách. Kromě přístupový Panel obsahuje další možnosti pro přístup k aplikací podporujících jednotné přihlašování v níže uvedeném seznamu.

| Článek Průvodce |  |
|:---:| --- |
| Porovnání různé možnosti, které jsou k dispozici pro nasazení aplikace přihlášení pro uživatele |[Nasazení služby Azure AD integrovaných aplikací pro uživatele](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users) |
| Přehled na přístupovém panelu a jeho mobilních ekvivalentní MyApps |[Úvod do přístupového panelu a MyApps](active-directory-saas-access-panel-introduction.md)<br />— [iOS](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8)<br />— [Android](https://play.google.com/store/apps/details?id=com.microsoft.myapps) |
| Jak získat přístup z webu Office 365 aplikace Azure AD. |[Pomocí Spouštěč aplikace Office 365](https://support.office.com/en-us/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a) |
| Jak získat přístup z mobilní aplikace Intune Managed Browser aplikace Azure AD. |[Spravovaný prohlížeč Intune](https://technet.microsoft.com/en-us/library/dn878029.aspx)<br />— [iOS](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8)<br />— [Android](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser) |
| Jak získat přístup k zahájení jednotné přihlašování pomocí přímých odkazů aplikace Azure AD. |[Získávání přímé odkazy přihlašování pro vaše aplikace](active-directory-appssoaccess-whatis.md#direct-sign-on-links-for-federated-password-based-or-existing-apps) |

Přístupový Panel je k dispozici pro [všechny edice služby Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="reports-easily-audit-app-access-changes-and-monitor-sign-ins-to-apps"></a>Sestavy: Snadno auditovat přístup změny aplikace a sledovat přihlášení do aplikace
Azure Active Directory poskytuje několik sestavy a výstrahy, které vám pomůžou monitorovat vaši organizaci přístup k aplikacím. Může přijímat výstrahy pro neobvyklých přihlášení pro vaše aplikace a můžete sledovat, kdy a proč došlo ke změně uživatelům přístup k aplikaci.

| Článek Průvodce |  |
|:---:| --- |
| Přehled funkce vytváření sestav v Azure Active Directory |[Začínáme s vytvářením sestav Azure AD](active-directory-reporting-getting-started.md) |
| Postup sledování přihlášení a používání aplikace uživatelů |[Zobrazit sestavy o využití a přístupu](active-directory-view-access-usage-reports.md) |
| Sledovat změny provedené, kdo má přístup k určité aplikace |[Události sestavy auditování Azure Active Directory](active-directory-reporting-audit-events.md) |
| Exportovat data tyto sestavy do vaší upřednostňované nástroje pomocí rozhraní API pro vytváření sestav |[Začínáme s Azure AD Reporting rozhraní API](active-directory-reporting-api-getting-started.md) |

Chcete-li zobrazit sestavy se dodává s různými edicemi služby Azure Active Directory, [kliknutím sem](active-directory-view-access-usage-reports.md).

## <a name="see-also"></a>Další informace najdete v tématech
[Představení služby Azure Active Directory](active-directory-whatis.md)

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/)

[Azure Active Directory Domain Services](https://azure.microsoft.com/services/active-directory-ds/)

[Azure Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/)
