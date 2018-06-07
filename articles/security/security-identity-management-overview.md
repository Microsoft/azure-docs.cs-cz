---
title: Zabezpečení Azure funkce, které pomáhají se správou identit | Microsoft Docs
description: " Tento článek obsahuje přehled základních funkcí zabezpečení Azure, které pomáhají se správou identit. Microsoft identit a přístupu řešení Nápověda pro správu IT chránit přístup k aplikacím a prostředkům v podnikovém datovém centru a cloudu, povolení další úrovně ověřování, jako je Vícefaktorové ověřování a podmíněného přístupu zásady. "
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
ms.openlocfilehash: 22b233e23c5c4609990bb6ba6148fe24d3d82c4b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34641345"
---
# <a name="azure-identity-management-security-overview"></a>Přehled zabezpečení správy identit Azure
Microsoft identit a přístupu řešení Nápověda pro správu IT chránit přístup k aplikacím a prostředkům v podnikové datovém centru a do cloudu. Tato ochrana umožňuje další úrovně ověřování, jako je Vícefaktorové ověřování a zásady podmíněného přístupu. Monitorování podezřelé aktivity přes pokročilé zabezpečení vytváření sestav, auditování a upozornění, že pomáhá zmírnit potenciální potíže se zabezpečením. [Azure Active Directory Premium](../active-directory/active-directory-editions.md) poskytuje jednotné přihlašování (SSO) k tisícům cloud software jako služba (SaaS) aplikace a přístup k webové aplikace, kterou jste spustit místně.

Využití výhod výhody zabezpečení Azure Active Directory (Azure AD), můžete:

* Vytvořit a spravovat jedinou identitu pro každého uživatele v rámci podniku hybridní udržování synchronizace uživatele, skupiny a zařízení.
* Poskytovat přístup k jednotné přihlašování pro vaše aplikace, včetně tisícům předem integrovaných aplikací SaaS.
* Povolit aplikaci přístup k zabezpečení vynucením vícefaktorového ověřování založeného na pravidlech pro místní i cloudové aplikace.
* Poskytnout zabezpečený vzdálený přístup k místním webovým aplikacím prostřednictvím proxy aplikace služby Azure AD.

Cílem tohoto článku je poskytovat přehled o základní funkce Azure zabezpečení, které pomáhají se správou identit. Poskytujeme také odkazy na články, které poskytují podrobnosti o každé funkce tak další informace.  

Článek se zaměřuje na tyto možnosti správy identit Azure jádra:

* Jednotné přihlašování
* Reverzní proxy server
* Multi-Factor Authentication
* Sledování zabezpečení, výstrahy a sestavy na základě learning počítače
* Správa identit a přístupu zákazníků
* Registrace zařízení
* Správa privilegovaných identit
* Ochrana identit
* Hybridní Správa identit

## <a name="single-sign-on"></a>Jednotné přihlašování
Jednotné přihlašování znamená, nebudete mít přístup k všechny aplikace a prostředky, které musíte udělat podnikání, po přihlášení pouze po pomocí jediného uživatelského účtu. Jakmile se přihlásíte, dostanete všechny aplikace, bez nutnosti ověřování (zadejte například heslo) ještě jednou.

Mnoho organizací závisí aplikace SaaS například Office 365, pole a Salesforce pro snížení produktivity uživatelů. Historicky pracovníci IT potřebný jednotlivě vytvářet a aktualizovat uživatelské účty v každé aplikaci SaaS a uživatelé museli Zapamatovat heslo pro každou aplikaci SaaS.

Azure AD rozšiřuje prostředí místní služby Active Directory do cloudu, uživatelům umožníte používat jejich primární organizační účet pro přihlášení nejen do jejich zařízení připojených k doméně a prostředkům společnosti, ale také pro všechny webové a SaaS aplikace potřebují pro svou práci.

Jenom uživatelé nemají ke správě více sad uživatelských jmen a hesel, můžete zřídit nebo zrušte přístup k aplikaci automaticky zřídí, na základě jejich organizační skupiny a jejich stav zaměstnanců. Azure AD, představuje zabezpečení a řízení zásad správného řízení přístupu, pomocí kterých můžete centrálně spravovat přístup uživatelů napříč aplikací SaaS.

Další informace:

* [Přehled jednotného přihlašování](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../active-directory/manage-apps/what-is-single-sign-on.md)
* [Integrovat Azure Active Directory jednotné přihlašování s aplikacemi SaaS](../active-directory/active-directory-enterprise-apps-manage-sso.md)

## <a name="reverse-proxy"></a>Reverzní proxy server
Proxy aplikace služby Azure AD umožňuje publikovat místní aplikace, jako například [SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) lokalit, [Outlook Web App](https://technet.microsoft.com/library/jj657718.aspx), a [IIS](http://www.iis.net/)-aplikací uvnitř vaší privátní sítě a zajišťuje zabezpečený přístup pro uživatele mimo vaši síť. Proxy aplikací poskytuje vzdáleného přístupu a jednotné přihlašování pro mnoho typů místní webové aplikace s tisíci aplikace SaaS, které podporuje Azure AD. Zaměstnanci můžou přihlásit k aplikacím z domácí na jejich vlastní zařízení a ověřování prostřednictvím této cloudové proxy.

Další informace:

* [Povolení proxy aplikace služby Azure AD](../active-directory/manage-apps/application-proxy-enable.md)
* [Publikování aplikací pomocí proxy aplikace služby Azure AD](../active-directory/active-directory-application-proxy-publish.md)
* [Jednotné přihlašování pomocí Proxy aplikace](../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
* [Práce s podmíněným přístupem](../active-directory/manage-apps/application-proxy-integrate-with-sharepoint-server.md)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication
Azure Multi-Factor Authentication je metoda ověřování, který vyžaduje použití víc než jednu metodu ověřování a přidá velmi důležitou druhou vrstvu zabezpečení uživatelská přihlášení a transakce. Vícefaktorové ověřování pomáhá chránit přístup k datům a aplikacím při splnění požadavků uživatelů pro jednoduchý proces přihlášení. Zajišťuje silné ověřování přes celou řadu možností ověřování: telefonní hovory, textové zprávy nebo oznámení mobilní aplikace nebo ověřovací kódy a tokenů OAuth třetích stran.

Další informace:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Co je Azure Multi-Factor Authentication?](../active-directory/authentication/multi-factor-authentication.md)
* [Jak funguje Azure Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Sledování zabezpečení, výstrahy a sestavy na základě learning počítače
Sledování zabezpečení, výstrahy a machine learning sestav na základě které identifikují nekonzistentní přístupové vzorce vám umožní chránit vaši firmu. Můžete použít Azure AD přístup a použití sestav získat přehled o integrity a zabezpečení adresáři vaší organizace. Pomocí těchto informací správce adresáře může lépe určit, kde může ležet bezpečnostním rizikům, tak, aby adekvátní můžete naplánovat zmírnění.

Na portálu Azure sestavy spadají do následujících kategorií:

* **Sestavy anomálií**: obsahovat přihlášení události, které je neobvyklé. Naším cílem je mít budete vědět, tyto aktivity a umožňují určit, zda je událost podezřelé.
* **Integrované sestavy aplikací**: poskytují přehled o tom, jak cloudové aplikace jsou používány ve vaší organizaci. Azure AD umožňuje integraci s tisíci cloudových aplikací.
* **Zprávy o chybách**: označení chyb, které můžou nastat při zřizování účtů k externím aplikacím.
* **Uživatelská sestavy**: zobrazit data přihlašovací aktivita zařízení pro konkrétního uživatele.
* **Protokoly aktivity**: obsahovat záznam všech auditované události v posledních 24 hodin, posledních 7 dnů, nebo posledních 30 dnů a změny aktivity skupiny a aktivita resetování a registraci hesla.

Další informace:

* [Zobrazení sestav přístupů a používání](../active-directory/active-directory-view-access-usage-reports.md)
* [Začínáme s vytvářením sestav Azure Active Directory](../active-directory/active-directory-reporting-getting-started.md)
* [Průvodce vytvářením sestav Azure Active Directory](../active-directory/active-directory-reporting-guide.md)

## <a name="consumer-identity-and-access-management"></a>Správa identit a přístupu zákazníků
Azure AD B2C je služba správy vysoce dostupný, globální, identity pro určených aplikace, která je škálovatelná pro stovky milionů identit. Dá se integrovat do mobilních i webových platforem. Uživatele můžete přihlásit ke všem aplikacím, které prostřednictvím přizpůsobitelné pomocí svých účtů na sociálních nebo vytvořením nové přihlašovací údaje.

V minulosti by chtěli zaregistrovat zákazníků a jejich přihlášení do svých aplikací vývojáři aplikací napsali vlastní kód. A použili by místní databáze nebo systémy k ukládání uživatelských jmen a hesel. Azure AD B2C nabízí lepší způsob, jak integrovat správu identit uživatelů do aplikací s pomocí bezpečné, na standardech postavené platformy a velké sady rozšiřitelných zásad vaší organizace.

Pokud používáte Azure AD B2C, vaši uživatelé mohou zaregistrovat do pro vaše aplikace pomocí svých účtů na sociálních (Facebook, Google, Amazon, LinkedIn) nebo vytvořením nové přihlašovací údaje (e-mailovou adresu a heslo, nebo uživatelské jméno a heslo).

Další informace:

* [Co je Azure Active Directory B2C?](https://azure.microsoft.com/services/active-directory-b2c/)
* [Azure Active Directory B2C ve verzi preview: Přihlaste registrace a přihlašování uživatelů aplikace](../active-directory-b2c/active-directory-b2c-overview.md)
* [Azure Active Directory B2C ve verzi Preview: Typy aplikací](../active-directory-b2c/active-directory-b2c-apps.md)

## <a name="device-registration"></a>Registrace zařízení
Registrace zařízení služby Azure AD je základem pro zařízení na základě [podmíněného přístupu](../active-directory/active-directory-conditional-access-device-registration-overview.md) scénáře. Když je zařízení registrováno, poskytne mu nástroj registrace zařízení služby Azure AD zařízení s identitou, která se používá k ověření zařízení, když se uživatel přihlásí. Ověřené zařízení a atributů zařízení pak slouží k vynucení zásad podmíněného přístupu pro aplikace, které jsou hostované v cloudu nebo místně.

V kombinaci s řešením pro správu mobilních zařízení, jako je například Intune, budou atributy zařízení ve službě Azure AD jsou aktualizované o další informace o zařízení. Potom můžete vytvořit pravidla podmíněného přístupu, které vynucují přístup ze zařízení, která splňují vaše standardy zabezpečení a dodržování předpisů.

Další informace:

* [Začínáme s registrací zařízení ve službě Azure AD](../active-directory/active-directory-conditional-access-device-registration-overview.md)
* [Automatická registrace zařízení s Azure AD pro zařízení připojená k doméně systému Windows](../active-directory/active-directory-conditional-access-automatic-device-registration.md)
* [Nastavit automatické registrace Windows připojená k doméně se službou Azure AD](../active-directory/active-directory-conditional-access-automatic-device-registration-setup.md)

## <a name="privileged-identity-management"></a>Správa privilegovaných identit
S Azure AD Privileged Identity Management můžete spravovat, řízení a monitorovat privilegované identity a přístup k prostředkům v Azure AD, jakož i jiných služeb Microsoft online services, jako je například Office 365 a Microsoft Intune.

Uživatelé někdy potřebovat provádět privilegované operace v Azure nebo Office 365 prostředky nebo v jiných aplikací SaaS. Tato potřebná často znamená, že organizace mají uživatelům poskytnout trvalé privilegovaný přístup v Azure AD. Tento přístup je rostoucí bezpečnostní riziko pro hostované cloudové prostředky, protože organizace nelze monitorovat dostatečně co uživatelé dělají s jejich oprávněními správce. Navíc pokud uživatelský účet s privilegovaného přístupu je ohrožen, že jeden porušení mohou ovlivnit organizace celkové zabezpečení cloudu. Azure AD Privileged Identity Management pomáhá zmírnit toto riziko.

S Azure AD Privileged Identity Management můžete:

* Podívejte se uživatelů, kteří jsou správci služby Azure AD.
* Povolení na vyžádání, za běhu (JIT) pro správu přístupu ke službám Microsoft, jako je Office 365 a Intune.
* Získání sestavy o historii přístup správce a změny v přiřazení správců.
* Získáte výstrahy týkající se přístupu k privilegované role.

Další informace:

* [Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-configure.md)
* [Role v Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-roles.md)
* [Azure AD Privileged Identity Management: Jak přidat nebo odebrat roli uživatele](../active-directory/active-directory-privileged-identity-management-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Ochrana identit
Azure AD Identity Protection je služba zabezpečení, která poskytuje ucelený přehled o rizikových událostech a potenciální ohrožení zabezpečení, které ovlivňují identity ve vaší organizaci. Ochrany identit využívá existující funkce Azure AD detekce anomálií, které jsou k dispozici prostřednictvím Azure AD neobvyklé aktivity sestavy. Ochrana identity také zavádí nové typy událostí rizik, které můžete zjišťovat anomálie v reálném čase.

Další informace:

* [Azure AD Identity Protection](../active-directory/active-directory-identityprotection.md)
* [Kanál 9: Azure AD a Identity zobrazení: Identity Protection verze Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-management"></a>Hybridní Správa identit
Přístup Microsoft identitě rozdělena na místě a cloudu, vytváření identitu jednoho uživatele pro ověřování a autorizaci pro všechny prostředky, bez ohledu na umístění.

Další informace:

* [Dokument white paper hybridní identity](http://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
* [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Blog týmu Azure AD](https://blogs.technet.microsoft.com/ad/)
