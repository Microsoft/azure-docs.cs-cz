---
title: Funkce zabezpečení Azure, které pomáhají se správou identit | Dokumenty společnosti Microsoft
description: " Tento článek obsahuje přehled základních funkcí zabezpečení Azure, které pomáhají se správou identit. Řešení microsoftu pro správu identit a přístupu pomáhají IT chránit přístup k aplikacím a prostředkům v podnikovém datovém centru a v cloudu a umožňují další úrovně ověřování, jako je vícefaktorové ověřování a podmíněný přístup. Politiky. "
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 5aa0a7ac-8f18-4ede-92a1-ae0dfe585e28
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2018
ms.author: terrylan
Customer intent: As an IT Pro or decision maker I am trying to learn about identity management capabilities in Azure
ms.openlocfilehash: f61b6193a0d2082296a17128b41d7220f9b7e05f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565889"
---
# <a name="azure-identity-management-security-overview"></a>Přehled zabezpečení správy identit Azure

 Správa identit je proces ověřování a autorizace [objektů zabezpečení](/windows/security/identity-protection/access-control/security-principals). Zahrnuje také kontrolu informací o těchto objektech (identitách). Objekty zabezpečení (identity) mohou zahrnovat služby, aplikace, uživatele, skupiny atd. Řešení microsoftu pro správu identit a přístupu pomáhají IT chránit přístup k aplikacím a prostředkům v podnikovém datovém centru a do cloudu. Tato ochrana umožňuje další úrovně ověření, jako je například vícefaktorové ověřování a zásady podmíněného přístupu. Sledování podezřelé aktivity prostřednictvím rozšířeného vytváření sestav zabezpečení, auditování a upozorňování pomáhá zmírnit potenciální problémy se zabezpečením. [Azure Active Directory Premium](/azure/active-directory/active-directory-editions) poskytuje jednotné přihlašování (SSO) tisícům cloudového softwaru jako služby (SaaS) aplikací a přístup k webovým aplikacím, které spouštěte místně.
 
Díky výhodám zabezpečení služby Azure Active Directory (Azure AD) můžete:

* Umožňuje vytvořit a spravovat jednu identitu pro každého uživatele v celém podniku a přitom zajistit synchronizaci uživatelů, skupin a zařízení. 
* Poskytněte přístup k vašim aplikacím, včetně tisíců předem integrovaných aplikací SaaS.
* Zajistit zabezpečení přístupu k aplikacím tím, že u místních i cloudových aplikací budete vynucovat vícefaktorové ověřování na základě pravidel.
* Zřizování zabezpečeného vzdáleného přístupu k místním webovým aplikacím prostřednictvím proxy aplikací Azure AD.

Cílem tohoto článku je poskytnout přehled základních funkcí zabezpečení Azure, které pomáhají se správou identit. Poskytujeme také odkazy na články, které obsahují podrobnosti o jednotlivých funkcích, takže se můžete dozvědět více.  

Článek se zaměřuje na následující základní možnosti správy azure identit:

* Jednotné přihlašování
* Reverzní proxy server
* Multi-factor Authentication
* Řízení přístupu na základě rolí (RBAC)
* Monitorování zabezpečení, výstrahy a sestavy založené na strojovém učení
* Správa identit y a přístupu spotřebitelů
* Registrace zařízení
* Privileged Identity Management
* Ochrana identit
* Hybridní správa identit/připojení Azure AD
* Kontroly přístupu Azure AD

## <a name="single-sign-on"></a>Jednotné přihlašování

Jednotné přihlašování znamená mít přístup ke všem aplikacím a prostředkům, které potřebujete k podnikání, přihlášením pouze jednou pomocí jednoho uživatelského účtu. Po přihlášení máte přístup ke všem aplikacím, které potřebujete, aniž byste museli podruhé ověřovat (například zadávat heslo).

Mnoho organizací spoléhá na aplikace SaaS, jako jsou Office 365, Box a Salesforce, pro produktivitu uživatelů. Historicky pracovníci IT potřebovali individuálně vytvářet a aktualizovat uživatelské účty v každé aplikaci SaaS a uživatelé si museli pamatovat heslo pro každou aplikaci SaaS.

Azure AD rozšiřuje místní prostředí služby Active Directory do cloudu, což uživatelům umožňuje používat svůj primární účet organizace k přihlášení nejen ke svým zařízením a prostředkům společnosti, ale také ke všem webovým a saas aplikacím, které potřebují. pro jejich práci.

Nejen, že uživatelé nemusí spravovat více sad uživatelských jmen a hesel, můžete zřídit nebo de-provision přístup k aplikacím automaticky, na základě jejich organizačnískupiny a jejich stav zaměstnance. Azure AD zavádí zabezpečení a řízení přístupu, pomocí kterých můžete centrálně spravovat přístup uživatelů napříč aplikacemi SaaS.

Další informace:

* [Přehled jednotného přihlašování](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../../active-directory/manage-apps/what-is-single-sign-on.md)
* [Integrace jednotného přihlašování služby Azure Active Directory s aplikacemi SaaS](../../active-directory/manage-apps/configure-single-sign-on-non-gallery-applications.md)

## <a name="reverse-proxy"></a>Reverzní proxy server

Proxy aplikace Azure AD umožňuje publikovat místní aplikace, jako jsou [weby SharePointu,](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) [Outlook Web App](https://technet.microsoft.com/library/jj657718.aspx)a aplikace založené na službě [IIS](https://www.iis.net/)v privátní síti a poskytuje zabezpečený přístup uživatelům mimo vaši síť. Proxy aplikace poskytuje vzdálený přístup a přihlašování pro mnoho typů místních webových aplikací s tisíci aplikací SaaS, které podporuje Azure AD. Zaměstnanci se můžou přihlásit k vašim aplikacím z domova na vlastních zařízeních a ověřit se prostřednictvím tohoto cloudového proxy serveru.

Další informace:

* [Povolení proxy aplikace Azure AD](/azure/active-directory/manage-apps/application-proxy-enable)
* [Publikování aplikací pomocí proxy aplikace služby Azure AD](/azure/active-directory/active-directory-application-proxy-publish)
* [Jednotné přihlašování s využitím proxy aplikací](../../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
* [Práce s podmíněným přístupem](../../active-directory/manage-apps/application-proxy-integrate-with-sharepoint-server.md)

## <a name="multi-factor-authentication"></a>Multi-factor Authentication

Azure Multi-Factor Authentication je metoda ověřování, která vyžaduje použití více než jedné metody ověření a přidá kritickou druhou vrstvu zabezpečení pro přihlášení uživatelů a transakce. Vícefaktorové ověřování pomáhá chránit přístup k datům a aplikacím a zároveň uspokojovat požadavky uživatelů na jednoduchý proces přihlášení. Poskytuje silné ověřování prostřednictvím řady možností ověření: telefonní hovory, textové zprávy nebo oznámení mobilních aplikací nebo ověřovací kódy a tokeny OAuth třetích stran.

Další informace:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Co je Azure Multi-Factor Authentication?](/azure/active-directory/authentication/multi-factor-authentication)
* [Jak funguje Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="rbac"></a>RBAC

RBAC je systém autorizace založený na Azure Resource Manageru, který poskytuje přesnou správu přístupu k prostředkům v Azure. RBAC umožňuje podrobné řízení úrovně přístupu, který uživatelé mají. Můžete například omezit uživatele pouze na správu virtuálních sítí a jiného uživatele pro správu všech prostředků ve skupině prostředků. Azure obsahuje několik předdefinovaných rolí, které můžete využít. V následujícím seznamu najdete čtyři základní předdefinované role. První tři se vztahují ke všem typům prostředků.

Další informace:

* [Co je řízení přístupu na základě role (RBAC)?](/azure/role-based-access-control/overview)
* [Předdefinované role pro prostředky Azure](/azure/role-based-access-control/built-in-roles)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Monitorování zabezpečení, výstrahy a sestavy založené na strojovém učení

Monitorování zabezpečení, výstrahy a sestavy založené na strojovém učení, které identifikují nekonzistentní přístupové vzorce, vám mohou pomoci chránit vaši firmu. Sestavy přístupu a využití azure a služby Azure AD můžete získat přehled o integritě a zabezpečení adresáře vaší organizace. Pomocí těchto informací může správce adresáře lépe určit, kde by mohla být možná bezpečnostní rizika, aby mohl odpovídajícím způsobem plánovat zmírnění těchto rizik.

Na webu Azure Portal se sestavy spadají do následujících kategorií:

* **Sestavy anomálií**: Obsahují události přihlášení, které jsme zjistili, že jsou neobvyklé. Naším cílem je upozornit vás na takovou činnost a umožnit vám určit, zda je událost podezřelá.
* **Integrované sestavy aplikací**: Poskytují přehled o tom, jak se cloudové aplikace používají ve vaší organizaci. Azure AD nabízí integraci s tisíci cloudových aplikací.
* **Zprávy o chybách**: Označte chyby, ke kterým může dojít při zřizování účtů externím aplikacím.
* **Sestavy specifické pro uživatele**: Zobrazení dat o aktivitě přihlášení zařízení pro konkrétního uživatele.
* **Protokoly aktivit**: Obsahují záznam všech auditovaných událostí za posledních 24 hodin, posledních 7 dní nebo posledních 30 dní a skupinových změn aktivit a aktivity resetování hesla a registrace.

Další informace:

* [Zobrazení sestav přístupů a používání](/azure/active-directory/active-directory-view-access-usage-reports)
* [Začínáme s vytvářením sestav služby Azure Active Directory](/azure/active-directory/active-directory-reporting-getting-started)
* [Průvodce vytvářením sestav služby Azure Active Directory](/azure/active-directory/active-directory-reporting-guide)

## <a name="consumer-identity-and-access-management"></a>Správa identit y a přístupu spotřebitelů

Azure AD B2C je vysoce dostupná globální služba správy identit pro aplikace orientované na spotřebitele, která se škáluje na stovky milionů identit. Dá se integrovat do mobilních i webových platforem. Vaši spotřebitelé se mohou přihlásit ke všem vašim aplikacím prostřednictvím přizpůsobitelných prostředí pomocí svých stávajících účtů na sociálních sítích nebo vytvořením nových přihlašovacích údajů.

V minulosti by vývojáři aplikací, kteří chtěli zaregistrovat zákazníky a přihlásit je do svých aplikací, napsali svůj vlastní kód. A použili by místní databáze nebo systémy k ukládání uživatelských jmen a hesel. Azure AD B2C nabízí vaší organizaci lepší způsob, jak integrovat správu identit spotřebitelů do aplikací pomocí zabezpečené platformy založené na standardech a velké sady rozšiřitelných zásad.

Když používáte Azure AD B2C, vaši spotřebitelé se můžou zaregistrovat do vašich aplikací pomocí svých stávajících účtů na sociálních sítích (Facebook, Google, Amazon, LinkedIn) nebo vytvořením nových přihlašovacích údajů (e-mailová adresa a heslo nebo uživatelské jméno a heslo).

Další informace:

* [Co je Azure Active Directory B2C?](https://azure.microsoft.com/services/active-directory-b2c/)
* [Azure Active Directory B2C ve verzi Preview: Registrace a přihlašování uživatelů aplikace](../../active-directory-b2c/overview.md)
* [Azure Active Directory B2C Preview: Typy aplikací](../../active-directory-b2c/application-types.md)

## <a name="device-registration"></a>Registrace zařízení

Registrace zařízení Azure AD je základem pro scénáře [podmíněného přístupu](/azure/active-directory/active-directory-conditional-access-device-registration-overview) založené na zařízení. Když je zařízení registrované, registrace zařízení Azure AD poskytuje zařízení s identitou, která se používá k ověření zařízení při přihlášení uživatele. Ověřené zařízení a atributy zařízení pak lze použít k vynucení zásad podmíněného přístupu pro aplikace, které jsou hostované v cloudu a místně.

V kombinaci s řešením pro správu mobilních zařízení, jako je Intune, jsou atributy zařízení ve službě Azure AD aktualizovány o další informace o zařízení. Pak můžete vytvořit pravidla podmíněného přístupu, která vynucují přístup ze zařízení tak, aby splňovala vaše standardy zabezpečení a dodržování předpisů.

Další informace:

* [Začínáme s registrací zařízení Azure AD](/azure/active-directory/active-directory-conditional-access-device-registration-overview)
* [Automatická registrace zařízení pomocí Azure AD pro zařízení připojená k doméně Windows](/azure/active-directory/active-directory-conditional-access-automatic-device-registration)
* [Nastavení automatické registrace zařízení s windows pro doménu pomocí Azure AD](/azure/active-directory/active-directory-conditional-access-automatic-device-registration-setup)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Díky azure ad privilegované správy identit můžete spravovat, řídit a monitorovat své privilegované identity a přístup k prostředkům ve službě Azure AD a dalším online službám Microsoftu, jako jsou Office 365 a Microsoft Intune.

Uživatelé někdy potřebují provádět privilegované operace v azure nebo office 365 prostředky nebo v jiných aplikacích SaaS. Tato potřeba často znamená, že organizace musí poskytnout uživatelům trvalý privilegovaný přístup ve službě Azure AD. Takový přístup je rostoucí bezpečnostní riziko pro prostředky hostované v cloudu, protože organizace nemohou dostatečně sledovat, co uživatelé dělají s jejich oprávnění správce. Navíc pokud dojde k ohrožení uživatelského účtu s privilegovaným přístupem, může jedno porušení ovlivnit celkové zabezpečení cloudu organizace. Azure AD Privileged Identity Management pomáhá zmírnit toto riziko.

S Azure AD Privilegované Správy identit, můžete:

* Podívejte se, kteří uživatelé jsou správci Azure AD.
* Povolte přístup pro správu na vyžádání, za chvíli (JIT) ke službám Microsoftu, jako je Office 365 a Intune.
* Získejte sestavy o historii přístupu správce a změnách v přiřazeních správců.
* Získejte upozornění na přístup k privilegované roli.

Další informace:

* [Co je Azure AD Privileged Identity Management?](../../active-directory/privileged-identity-management/pim-configure.md)
* [Přiřazení rolí adresáře Azure AD v PIM](../../active-directory/privileged-identity-management/pim-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Ochrana identit

Azure AD Identity Protection je služba zabezpečení, která poskytuje konsolidované zobrazení detekce rizik a potenciální chyby zabezpečení, které ovlivňují identity vaší organizace. Ochrana identity využívá existující funkce azure ad detekce anomálií, které jsou k dispozici prostřednictvím sestav anomální aktivity Azure AD. Ochrana identity také zavádí nové typy detekce rizik, které mohou detekovat anomálie v reálném čase.

Další informace:

* [Azure AD Identity Protection](/azure/active-directory/identity-protection/overview)
* [Kanál 9: Azure AD a identity Zobrazit: Ochrana identity Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-managementazure-ad-connect"></a>Hybridní správa identit/připojení Azure AD

Řešení pro správu identit od Microsoftu pokrývá místní i cloudové funkce a vytvářejí jedinou identitu uživatele pro ověřování a autorizaci u všech prostředků bez ohledu na umístění. Tomu se říká hybridní identita. Azure AD Connect je nástroj od Microsoftu, jehož účelem je splnit a zajistit cíle hybridní identity. To umožní poskytovat společnou identitu pro uživatele pro aplikace Office 365, Azure a SaaS integrované s Azure AD. Má následující funkce:

* Synchronizace
* AD FS a federace integrace
* Průchod ověřováním
* Monitorování stavu

Další informace:

* [Dokument white paper hybridní identity](https://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
* [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Blog týmu Azure AD](https://blogs.technet.microsoft.com/ad/)

## <a name="azure-ad-access-reviews"></a>Kontroly přístupu Azure AD

Kontroly přístupu Azure Active Directory (Azure AD) umožňují organizacím efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazení privilegovaných rolí.

Další informace:

* [Kontroly přístupu Azure AD](../../active-directory/governance/access-reviews-overview.md)
* [Správa přístupu uživatelů pomocí kontrol přístupu Azure AD](../../active-directory/governance/access-reviews-overview.md)
