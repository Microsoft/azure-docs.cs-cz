---
title: Zabezpečení Azure funkce, které pomáhají se správou identit | Microsoft Docs
description: " Tento článek obsahuje přehled základních funkcí zabezpečení Azure, které pomáhají se správou identit. Microsoft identit a přístupu řešení Nápověda pro správu IT chránit přístup k aplikacím a prostředkům v podnikovém datovém centru a cloudu, povolení další úrovně ověřování, jako je vícefaktorové ověřování a zásady podmíněného přístupu. "
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 5aa0a7ac-8f18-4ede-92a1-ae0dfe585e28
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: dcfc295de39e909bf157edee4fec1ba51a583750
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="azure-identity-management-security-overview"></a>Přehled zabezpečení správy identit Azure
Microsoft identit a přístupu řešení Nápověda pro správu IT chránit přístup k aplikacím a prostředkům v podnikovém datovém centru a cloudu, povolení další úrovně ověřování, jako je vícefaktorové ověřování a zásady podmíněného přístupu. Monitorování podezřelé aktivity přes pokročilé zabezpečení vytváření sestav a auditování, výstrahy, pomáhá zmírnit potenciální potíže se zabezpečením. [Azure Active Directory Premium](../active-directory/active-directory-editions.md) poskytuje jednotné přihlašování k tisícům cloudové aplikace (SaaS) a přístup k webové aplikace můžete spustit místně.

Výhody zabezpečení služby Azure Active Directory (AD), zahrnují možnost:

* Vytvořit a spravovat jedinou identitu pro každého uživatele v rámci podniku hybridní udržování synchronizace uživatele, skupiny a zařízení
* Poskytnutí přístupu jeden přihlašování pro vaše aplikace, včetně tisícům předem integrovaných aplikací SaaS
* Povolit aplikaci přístup k zabezpečení vynucením vícefaktorového ověřování založeného na pravidlech pro místní i cloudové aplikace.
* Zřídit zabezpečený vzdálený přístup k místním webovým aplikacím prostřednictvím proxy aplikace služby Azure AD

Cílem tohoto článku je poskytovat přehled o základní funkce Azure zabezpečení, které pomáhají se správou identit. Poskytujeme také odkazy na články, které poskytují podrobnosti o každé funkce tak další informace.  

Článek se zaměřuje na tyto možnosti správy identit Azure jádra:

* Jednotné přihlašování
* Reverzní proxy server
* Ověřování pomocí služby Multi-Factor Authentication
* Sledování zabezpečení, výstrahy a sestavy na základě learning počítače
* Správa identit a přístupu zákazníků
* Registrace zařízení
* Správa privilegovaných identit
* Ochrana identit
* Hybridní Správa identit

## <a name="single-sign-on"></a>Jednotné přihlašování
Jeden přihlašování (SSO) znamená, nebudete mít přístup k všechny aplikace a prostředky, které musíte udělat podnikání, po přihlášení pouze jednou pomocí jediného uživatelského účtu. Jakmile se přihlásíte, dostanete všechny aplikace, bez nutnosti ověřování (zadejte například heslo) ještě jednou.

Mnoho organizací závisí software jako služba (SaaS) aplikace například Office 365, pole a Salesforce pro produktivita koncového uživatele. Historicky pracovníci IT potřebný jednotlivě vytvářet a aktualizovat uživatelské účty v každé aplikaci SaaS a uživatelé museli Zapamatovat heslo pro každou aplikaci SaaS.

Azure AD rozšiřuje prostředí místní služby Active Directory do cloudu, uživatelům umožníte používat jejich primární účet organizace nejen Přihlaste se k jejich zařízení připojených k doméně a prostředkům společnosti, ale také všechny webu a aplikace SaaS, které jsou potřebné pro jejich úlohy.

Jenom uživatelé nemají ke správě více sad uživatelských jmen a hesel, přístup k aplikaci lze automaticky zřízeného nebo zrušte zřízené na základě organizační skupiny a jejich stav jako zaměstnanec. Azure AD, představuje zabezpečení a řízení zásad správného řízení přístupu, které vám umožní centrálně spravovat přístup uživatelů v rámci aplikace SaaS.

Další informace:

* [Přehled jednotného přihlašování](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../active-directory/active-directory-appssoaccess-whatis.md)
* [Integrovat Azure Active Directory jednotné přihlašování s aplikacemi SaaS](../active-directory/active-directory-enterprise-apps-manage-sso.md)

## <a name="reverse-proxy"></a>Reverzní proxy server
Proxy aplikace služby Azure AD umožňuje publikovat místní aplikace, jako například [SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) lokalit, [Outlook Web App](https://technet.microsoft.com/library/jj657718.aspx), a [IIS](http://www.iis.net/)-aplikací uvnitř vaší privátní sítě a zajišťuje zabezpečený přístup pro uživatele mimo vaši síť. Proxy aplikací poskytuje vzdálený přístup a jednotné přihlašování (SSO) pro mnoho typů místní webové aplikace s tisíci aplikace SaaS, které podporuje Azure AD. Zaměstnancům umožní přihlásit se k aplikacím z domácí na jejich vlastní zařízení a ověřování prostřednictvím této cloudové proxy.

Další informace:

* [Povolení proxy aplikace služby Azure AD](../active-directory/active-directory-application-proxy-enable.md)
* [Publikování aplikací pomocí proxy aplikace služby Azure AD](../active-directory/active-directory-application-proxy-publish.md)
* [Jednotné přihlášení pomocí Proxy aplikace](../active-directory/active-directory-application-proxy-sso-using-kcd.md)
* [Práce s podmíněným přístupem](../active-directory/application-proxy-enable-remote-access-sharepoint.md)

## <a name="multi-factor-authentication"></a>Ověřování pomocí služby Multi-Factor Authentication
Azure Multi-Factor authentication (MFA) je metoda ověřování, který vyžaduje použití víc než jednu metodu ověřování a přidá velmi důležitou druhou vrstvu zabezpečení uživatelská přihlášení a transakce. MFA pomáhá chránit přístup k datům a aplikacím při splnění požadavků uživatelů pro jednoduchý proces přihlášení. Zajišťuje silné ověřování přes celou řadu možností ověření – telefonní hovor, textová zpráva nebo mobilní aplikace oznámení nebo ověřovací kód a třetích stran tokeny OAuth.

Další informace:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Co je Azure Multi-Factor Authentication?](../active-directory/authentication/multi-factor-authentication.md)
* [Jak funguje Azure Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Sledování zabezpečení, výstrahy a sestavy na základě learning počítače
Sledování zabezpečení a výstrah a na základě learning sestav počítače, které identifikují nekonzistentní přístupové vzorce vám umožní chránit vaši firmu. Přístup k Azure Active Directory a sestavy využití, které slouží k získat přehled o integrity a zabezpečení adresáři vaší organizace. Tyto informace a správce directory pomohou určit, kde může být bezpečnostním rizikům, tak, aby adekvátní můžete naplánovat zmírnění.

Na portálu Azure sestavy jsou rozdělené do následujících způsobů:

* Sestavy anomálií – obsahovat přihlášení události, které je neobvyklé. Naším cílem je mít budete vědět, tyto aktivity a díky kterému budete mít možnost provádět rozhodnutí o tom, zda je událost podezřelé.
* Integrované sestavy aplikací – poskytují přehled o tom, jak cloudové aplikace jsou používány ve vaší organizaci. Azure Active Directory umožňuje integraci s tisíci cloudových aplikací.
* Zprávy o chybách – označení chyb, které mohou nastat při zřizování účtů k externím aplikacím.
* Uživatelská sestavy – zobrazí zařízení nebo přihlášení v data aktivit pro konkrétního uživatele.
* Protokoly aktivity – obsahovat záznam všech auditované události v posledních 24 hodin, posledních 7 dnů, nebo posledních 30 dnů a změny aktivity skupiny a aktivita resetování a registraci hesla.

Další informace:

* [Zobrazení sestav přístupů a používání](../active-directory/active-directory-view-access-usage-reports.md)
* [Začínáme s Azure Active Directory vytváření sestav](../active-directory/active-directory-reporting-getting-started.md)
* [Průvodce vytvářením sestav Azure Active Directory](../active-directory/active-directory-reporting-guide.md)

## <a name="consumer-identity-and-access-management"></a>Správa identit a přístupu zákazníků
Azure Active Directory B2C je služba správy vysoce dostupný, globální, identity pro určených aplikace, která je škálovatelná pro stovky milionů identit. Dá se integrovat do mobilních i webových platforem. Zákazníci se můžou ke všem vašim aplikacím přihlašovat přes přizpůsobitelné prostředí pomocí svých existujících účtů v sociálních sítích nebo pomocí nově vytvořených přihlašovacích údajů.

V minulosti by si vývojáři aplikací napsali vlastní kód pro registraci a přihlašování uživatelů do svojí aplikace. A použili by místní databáze nebo systémy k ukládání uživatelských jmen a hesel. Azure Active Directory B2C nabízí lepší způsob, jak integrovat správu identit uživatelů do aplikací s pomocí bezpečné, na standardech postavené platformy a velké sady rozšiřitelných zásad vaší organizace.

Pokud používáte Azure Active Directory B2C, vaši uživatelé mohou zaregistrovat do pro vaše aplikace pomocí svých účtů na sociálních (Facebook, Google, Amazon, LinkedIn) nebo vytvořením nové přihlašovací údaje (e-mailovou adresu a heslo, nebo uživatelské jméno a heslo).

Další informace:

* [Co je Azure Active Directory B2C?](https://azure.microsoft.com/services/active-directory-b2c/)
* [Azure Active Directory B2C ve verzi preview: Přihlaste registrace a přihlašování uživatelů aplikace](../active-directory-b2c/active-directory-b2c-overview.md)
* [Azure Active Directory B2C ve verzi Preview: Typy aplikací](../active-directory-b2c/active-directory-b2c-apps.md)

## <a name="device-registration"></a>Registrace zařízení
Registrace zařízení služby Azure AD je základem pro zařízení na základě [podmíněného přístupu](../active-directory/active-directory-conditional-access-device-registration-overview.md) scénáře. Když je zařízení registrováno, poskytuje Azure Active Directory Device Registration zařízení s identitou, která se používá k ověření zařízení při přihlášení uživatele. Ověřené zařízení a jeho atributy pak lze použít k vynucení zásad podmíněného přístupu u aplikací, které jsou hostovány na cloudu a v místním úložišti.

V kombinaci s řešením pro správu (MDM) mobilních zařízení, jako je například Intune, budou atributy zařízení ve službě Azure Active Directory jsou aktualizované o další informace o zařízení. To vám umožňuje vytvořit pravidla podmíněného přístupu, která vynucují, aby přístup měla pouze taková zařízení, která splňují vaše standardy zabezpečení a dodržování předpisů. 

Další informace:

* [Začínáme s Azure Active Directory Device Registration](../active-directory/active-directory-conditional-access-device-registration-overview.md)
* [Automatická registrace zařízení v Azure Active Directory pro Windows připojená k doméně](../active-directory/active-directory-conditional-access-automatic-device-registration.md)
* [Nastavit automatické registrace Windows připojená k doméně se službou Azure Active Directory](../active-directory/active-directory-conditional-access-automatic-device-registration-setup.md)

## <a name="privileged-identity-management"></a>Správa privilegovaných identit
Aplikace Azure Active Directory (AD) Privileged Identity Management vám umožní spravovat, řídit a monitorovat privilegované identity a přístup k prostředkům v Azure AD i v jiných online službách Microsoft, jako jsou Office 365 nebo Microsoft Intune.

Uživatelé někdy potřebovat provádět privilegované operace v Azure nebo Office 365 prostředků nebo jiných aplikací SaaS. To často znamená, že organizace mají jim dát trvalé privilegovaného přístupu ve službě Azure AD. Toto je rostoucí bezpečnostní riziko pro hostované cloudové prostředky, protože organizace nelze monitorovat dostatečně tyto činnosti uživatelů s jejich oprávněními správce. Kromě toho pokud uživatelský účet s privilegovaného přístupu je ohrožen, že jeden porušení zabezpečení by mohlo mít vliv jejich celkové zabezpečení cloudu. Azure AD Privileged Identity Management pomáhá vyřešit toto riziko.

Azure AD Privileged Identity Management vám umožní:

* Uvidíte, kteří uživatelé jsou správci Azure AD
* Povolit na vyžádání "právě v čase" pro správu přístup k Online službám společnosti Microsoft, jako třeba Office 365 a Intune
* Získání sestavy o historii přístup správce a změny v přiřazení správců
* Získání výstrahy týkající se přístupu k privilegované role.

Další informace:

* [Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-configure.md)
* [Role v Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-roles.md)
* [Azure AD Privileged Identity Management: Jak přidat nebo odebrat roli uživatele](../active-directory/active-directory-privileged-identity-management-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Ochrana identit
Azure AD Identity Protection je služba zabezpečení, která poskytuje ucelený přehled o rizikových událostech a potenciální ohrožení zabezpečení, které ovlivňují identity ve vaší organizaci. Ochrany identit využívá možnosti detekce anomálií existující Azure služby Active Directory (k dispozici prostřednictvím neobvyklé aktivity sestav Azure AD) a zavádí nové typy událostí rizik, které můžete zjišťovat anomálie v reálném čase.

Další informace:

* [Ochrany identit Azure Active Directory](../active-directory/active-directory-identityprotection.md)
* [Kanál 9: Azure AD a Identity zobrazení: Identity Protection verze Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-management"></a>Hybridní Správa identit
Přístup společnosti Microsoft k identitě rozdělena na místě a cloudu, vytváření identitu jednoho uživatele pro ověřování a autorizaci pro všechny prostředky, bez ohledu na umístění.

Další informace:

* [Dokument white paper hybridní identity](http://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
* [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Blog týmu Active Directory](https://blogs.technet.microsoft.com/ad/)
