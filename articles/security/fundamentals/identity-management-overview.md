---
title: Funkce zabezpečení Azure, které vám pomůžou se správou identit | Microsoft Docs
description: Přečtěte si o základních funkcích zabezpečení Azure, které vám pomůžou se správou identit. Podívejte se na informace o tématech, jako je jednotné přihlašování a reverzní proxy.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 5aa0a7ac-8f18-4ede-92a1-ae0dfe585e28
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/05/2021
ms.author: terrylan
Customer intent: As an IT Pro or decision maker I am trying to learn about identity management capabilities in Azure
ms.openlocfilehash: d931d3923ff49dde2bea234278c995e79670429f
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627629"
---
# <a name="azure-identity-management-security-overview"></a>Přehled zabezpečení správy identit v Azure

 Správa identit je proces ověřování a autorizace [objektů zabezpečení](/windows/security/identity-protection/access-control/security-principals). Zahrnuje také řízení informací o těchto objektech zabezpečení (identity). Objekty zabezpečení (identity) můžou zahrnovat služby, aplikace, uživatele, skupiny atd. Řešení pro správu identit a přístupu od Microsoftu umožňují IT chránit přístup k aplikacím a prostředkům v podnikovém datovém centru a do cloudu. Taková ochrana umožňuje další úrovně ověřování, například Multi-Factor Authentication a zásady podmíněného přístupu. Monitorování podezřelých aktivit prostřednictvím pokročilých sestav zabezpečení, auditování a upozorňování pomáhá zmírnit možné problémy se zabezpečením. [Azure Active Directory Premium](../../active-directory/fundamentals/active-directory-whatis.md) poskytuje jednotné přihlašování (SSO) k tisícům aplikací SaaS (Cloud software as a Service) a přístup k webovým aplikacím, které spouštíte místně.
 
Díky výhodám zabezpečení Azure Active Directory (Azure AD) můžete:

* Umožňuje vytvořit a spravovat jednu identitu pro každého uživatele v celém podniku a přitom zajistit synchronizaci uživatelů, skupin a zařízení. 
* Poskytněte přístup k vašim aplikacím bez jednotného přihlašování, včetně tisíců předem integrovaných aplikací SaaS.
* Zajistit zabezpečení přístupu k aplikacím tím, že u místních i cloudových aplikací budete vynucovat vícefaktorové ověřování na základě pravidel.
* Zajištění zabezpečeného vzdáleného přístupu k místním webovým aplikacím prostřednictvím Azure Proxy aplikací služby AD.

Cílem tohoto článku je poskytnout přehled základních funkcí zabezpečení Azure, které vám pomůžou se správou identit. Poskytujeme také odkazy na články, které poskytují podrobné informace o jednotlivých funkcích, abyste se mohli dozvědět víc.  

Článek se zaměřuje na následující základní možnosti správy identit Azure:

* Jednotné přihlašování
* Reverzní proxy server
* Multi-Factor Authentication
* Řízení přístupu na základě role Azure (Azure RBAC)
* Monitorování zabezpečení, výstrahy a sestavy založené na strojovém učení
* Správa identit a přístupu uživatelů
* Registrace zařízení
* Privileged Identity Management
* Ochrana identit
* Hybridní Správa identit/Azure AD Connect
* Kontroly přístupu Azure AD

## <a name="single-sign-on"></a>Jednotné přihlašování

SSO znamená přístup k všem aplikacím a prostředkům, které musíte udělat v podniku, a to tak, že se jednou přihlásíte pomocí jednoho uživatelského účtu. Jakmile se přihlásíte, budete mít přístup ke všem aplikacím, které potřebujete, aniž by bylo nutné je ověřit (například zadat heslo) podruhé.

Mnoho organizací spoléhá na SaaS aplikace, jako jsou Microsoft 365, box a Salesforce, pro produktivitu uživatelů. Historicky zaměstnanci IT potřebují pro jednotlivé aplikace SaaS vytvářet a aktualizovat uživatelské účty a uživatelé si museli pamatovat heslo pro každou aplikaci SaaS.

Azure AD rozšiřuje místní prostředí Active Directory do cloudu a umožňuje uživatelům používat jejich primární účet organizace k přihlašování nejen ke svým zařízením připojeným k doméně a prostředkům společnosti, ale také ke všem webovým a SaaS aplikacím, které potřebují pro své úlohy.

Nejen uživatelé nepotřebují spravovat více sad uživatelských jmen a hesel, můžete automaticky zřídit nebo zrušit přístup k aplikaci na základě jejich organizačních skupin a jejich stavu zaměstnanců. Azure AD zavádí zabezpečení a přístup k řízení zásad správného řízení, pomocí kterých můžete centrálně spravovat přístup uživatelů napříč SaaS aplikacemi.

Další informace:

* [Přehled jednotného přihlašování](../../active-directory/manage-apps/what-is-single-sign-on.md)
* [Video o základech ověřování](https://www.youtube.com/watch?v=fbSVgC8nGz4&feature=emb_title)
* [Série rychlý Start při správě aplikací](../../active-directory/manage-apps/view-applications-portal.md)

## <a name="reverse-proxy"></a>Reverzní proxy server

Azure Proxy aplikací služby AD umožňuje publikovat místní aplikace, jako jsou weby [služby SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) , [aplikace Outlook Web App](/Exchange/clients/outlook-on-the-web/outlook-on-the-web)a aplikace založené na [službě IIS](https://www.iis.net/), v rámci privátní sítě a poskytuje zabezpečený přístup uživatelům mimo vaši síť. Proxy aplikací poskytuje vzdálený přístup a jednotné přihlašování pro mnoho typů místních webových aplikací s tisíci SaaS aplikacemi, které Azure AD podporuje. Zaměstnanci se můžou k aplikacím přihlašovat z domova na svých vlastních zařízeních a ověřovat prostřednictvím tohoto cloudového proxy serveru.

Další informace:

* [Povolení služby Azure Proxy aplikací služby AD](../../active-directory/manage-apps/application-proxy-add-on-premises-application.md)
* [Publikování aplikací pomocí proxy aplikace služby Azure AD](../../active-directory/manage-apps/application-proxy-add-on-premises-application.md)
* [Jednotné přihlašování s využitím proxy aplikací](../../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
* [Práce s podmíněným přístupem](../../active-directory/manage-apps/application-proxy-integrate-with-sharepoint-server.md)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Azure AD Multi-Factor Authentication je metoda ověřování, která vyžaduje použití více než jedné metody ověřování a přidává kritickou druhou vrstvu zabezpečení pro přihlášení a transakce uživatelů. Multi-Factor Authentication pomáhá chránit přístup k datům a aplikacím a současně plnit požadavky uživatelů na jednoduchý proces přihlašování. Poskytuje silné ověřování prostřednictvím řady možností ověřování: telefonní hovory, textové zprávy nebo oznámení mobilní aplikace nebo ověřovací kódy a tokeny OAuth třetích stran.

Další informace:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Co je Azure AD Multi-Factor Authentication?](../../active-directory/authentication/concept-mfa-howitworks.md)
* [Jak funguje vícefaktorové ověřování Azure AD](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="azure-rbac"></a>Azure RBAC

Azure RBAC je autorizační systém založený na Azure Resource Manager, který poskytuje jemně odstupňovanou správu přístupu k prostředkům v Azure. Azure RBAC umožňuje detailní kontrolu úrovně přístupu uživatelů. Můžete například omezit uživatele tak, aby spravovali jenom virtuální sítě a jiného uživatele a spravovali všechny prostředky ve skupině prostředků. Azure obsahuje několik předdefinovaných rolí, které můžete využít. V následujícím seznamu najdete čtyři základní předdefinované role. První tři se vztahují ke všem typům prostředků.

- [Vlastník](../../role-based-access-control/built-in-roles.md#owner) – má plný přístup ke všem prostředkům, včetně práva delegovat přístup na ostatní uživatele. 
- [Přispěvatel](../../role-based-access-control/built-in-roles.md#contributor) – může vytvářet a spravovat všechny typy prostředků Azure, ale nemůže udělovat přístup ostatním.
- [Čtenář](../../role-based-access-control/built-in-roles.md#reader) – může zobrazit existující prostředky Azure.
- [Správce uživatelských přístupů](../../role-based-access-control/built-in-roles.md#user-access-administrator) – může spravovat uživatelský přístup k prostředkům Azure.

Další informace:

* [Co je řízení přístupu na základě role v Azure (Azure RBAC)?](../../role-based-access-control/overview.md)
* [Předdefinované role Azure](../../role-based-access-control/built-in-roles.md)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Monitorování zabezpečení, výstrahy a sestavy založené na strojovém učení

Monitorování zabezpečení, výstrahy a sestavy založené na strojovém učení, které identifikují nekonzistentní vzory přístupu, vám můžou přispět k ochraně vaší firmy. K získání přehledu o integritě a zabezpečení adresáře vaší organizace můžete použít sestavy přístupu a využití Azure AD. S těmito informacemi může správce adresáře lépe určit, kde můžou být potenciální bezpečnostní rizika, aby mohli přiměřeně naplánovat zmírnění těchto rizik.

Sestavy v Azure Portal spadají do následujících kategorií:

* **Sestavy anomálií**: obsahují události přihlášení, které jsme zjistili, že jsou neobvyklé. Naším cílem je, abyste věděli o takové činnosti a bylo možné určit, jestli je událost podezřelá.
* **Integrované sestavy aplikací**: poskytují přehled o tom, jak se ve vaší organizaci používají cloudové aplikace. Azure AD nabízí integraci s tisíci cloudových aplikací.
* **Zprávy o chybách**: označuje chyby, které mohou nastat při zřizování účtů pro externí aplikace.
* **Sestavy specifické pro uživatele**: zobrazení dat aktivit přihlašování zařízení pro určitého uživatele.
* **Protokoly aktivit**: obsahují záznam všech auditovaných událostí za posledních 24 hodin, posledních 7 dní nebo posledních 30 dnů, a změny aktivity skupiny a aktivity resetování hesla a registrace.

Další informace:

* [Zobrazení sestav přístupů a používání](../../active-directory/reports-monitoring/overview-reports.md)
* [Začínáme s vytvářením sestav Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md)
* [Azure Active Directory průvodce vytvářením sestav](../../active-directory/reports-monitoring/overview-reports.md)

## <a name="consumer-identity-and-access-management"></a>Správa identit a přístupu uživatelů

Azure AD B2C je vysoce dostupná, globální služba pro správu identit pro zákaznické aplikace, která se škáluje na stovky milionů identit. Dá se integrovat do mobilních i webových platforem. Vaši uživatelé se můžou přihlásit ke všem aplikacím prostřednictvím přizpůsobitelného prostředí pomocí svých stávajících účtů sociálních sítí nebo vytvořením nových přihlašovacích údajů.

V minulosti si vývojáři aplikací, kteří chtěli zaregistrovat zákazníky a přihlásili jim své aplikace, museli napsat svůj vlastní kód. A použili by místní databáze nebo systémy k ukládání uživatelských jmen a hesel. Azure AD B2C nabízí vaší organizaci lepší způsob integrace správy identit uživatelů do aplikací s využitím zabezpečené, standardizované platformy a rozsáhlé sady rozšiřitelných zásad.

Když použijete Azure AD B2C, můžou si vaši zákazníci zaregistrovat své aplikace pomocí svých stávajících účtů sociálních sítí (Facebook, Google, Amazon, LinkedIn) nebo vytvořením nových přihlašovacích údajů (e-mailová adresa a heslo nebo uživatelského jména a hesla).

Další informace:

* [Co je Azure Active Directory B2C?](https://azure.microsoft.com/services/active-directory-b2c/)
* [Azure Active Directory B2C ve verzi Preview: Registrace a přihlašování uživatelů aplikace](../../active-directory-b2c/overview.md)
* [Azure Active Directory B2C Preview: typy aplikací](../../active-directory-b2c/application-types.md)

## <a name="device-registration"></a>Registrace zařízení

Registrace zařízení Azure AD je základem pro scénáře [podmíněného přístupu](../../active-directory/devices/device-management-azure-portal.md) na základě zařízení. Když je zařízení zaregistrované, poskytne vám registrace zařízení Azure AD identitu, kterou používá k ověření zařízení, když se uživatel přihlásí. Ověřené zařízení a atributy zařízení se pak dají použít k prosazování zásad podmíněného přístupu pro aplikace, které jsou hostované v cloudu i v místním prostředí.

Při kombinaci s řešením správy mobilních zařízení, jako je Intune, se atributy zařízení ve službě Azure AD aktualizují o další informace o zařízení. Pak můžete vytvořit pravidla podmíněného přístupu, která vynutily přístup ze zařízení, aby splňovaly vaše standardy zabezpečení a dodržování předpisů.

Další informace:

* [Začínáme s registrací zařízení Azure AD](../../active-directory/devices/device-management-azure-portal.md)
* [Automatická registrace zařízení ve službě Azure AD pro zařízení připojená k doméně Windows](../../active-directory/devices/hybrid-azuread-join-plan.md)
* [Nastavení automatické registrace zařízení se systémem Windows připojených k doméně pomocí Azure AD](../../active-directory/devices/hybrid-azuread-join-plan.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Pomocí Azure AD Privileged Identity Management můžete spravovat, řídit a monitorovat své privilegované identity a přistupovat k prostředkům v Azure AD a také k dalším online služby Microsoftu, jako je třeba Microsoft 365 a Microsoft Intune.

Uživatelé někdy potřebují provádět privilegované operace v Azure nebo Microsoft 365ch prostředcích nebo v jiných aplikacích SaaS. To často znamená, že organizace musí uživatelům udělit trvalý privilegovaný přístup ve službě Azure AD. Takový přístup je rostoucí bezpečnostní riziko pro prostředky hostované v cloudu, protože organizace nemůžou dostatečně monitorovat, co dělají uživatelé s oprávněními správce. Kromě toho platí, že pokud dojde k ohrožení bezpečnosti uživatelského účtu s privilegovaným přístupem, může toto porušení ovlivnit celkové cloudové zabezpečení organizace. Azure AD Privileged Identity Management pomáhá zmírnit toto riziko.

Pomocí Azure AD Privileged Identity Management můžete:

* Podívejte se, kteří uživatelé jsou správci služby Azure AD.
* Povolit přístup pro správu JIT (just-in-time) na vyžádání ke službám Microsoftu, jako je například Microsoft 365 a Intune.
* Získejte sestavy o historii přístupu správce a změnách v přiřazeních správce.
* Získejte výstrahy týkající se přístupu k privilegované roli.

Další informace:

* [Co je Azure AD Privileged Identity Management?](../../active-directory/privileged-identity-management/pim-configure.md)
* [Přiřazení rolí adresáře Azure AD v PIM](../../active-directory/privileged-identity-management/pim-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Ochrana identit

Azure AD Identity Protection je služba zabezpečení, která poskytuje konsolidovanou pohled na detekci rizik a potenciální ohrožení zabezpečení, která ovlivňují identity vaší organizace. Identity Protection využívá stávající možnosti detekce anomálií Azure AD, které jsou k dispozici prostřednictvím sestav aktivit Azure AD neobvyklé. Identity Protection také přináší nové typy detekce rizik, které mohou detekovat anomálie v reálném čase.

Další informace:

* [Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md)
* [Kanál 9: Azure AD a identity show: identita Protection Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-managementazure-ad-connect"></a>Hybridní Správa identit/Azure AD Connect

Řešení pro správu identit od Microsoftu pokrývá místní i cloudové funkce a vytvářejí jedinou identitu uživatele pro ověřování a autorizaci u všech prostředků bez ohledu na umístění. Tomu se říká hybridní identita. Azure AD Connect je nástroj od Microsoftu, jehož účelem je splnit a zajistit cíle hybridní identity. Umožňuje zajistit pro vaše uživatele společnou identitu pro Microsoft 365, Azure a aplikace SaaS integrované s využitím Azure AD. Má následující funkce:

* Synchronizace
* Integrace AD FS a federace
* Předávací ověřování
* Monitorování stavu

Další informace:

* [Dokument White Paper hybridní identity](https://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
* [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Blog týmu Azure AD](https://blogs.technet.microsoft.com/ad/)

## <a name="azure-ad-access-reviews"></a>Kontroly přístupu Azure AD

Kontroly přístupu Azure Active Directory (Azure AD) umožňují organizacím efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazení privilegovaných rolí.

Další informace:

* [Kontroly přístupu Azure AD](../../active-directory/governance/access-reviews-overview.md)
* [Správa přístupu uživatelů pomocí kontrol přístupu Azure AD](../../active-directory/governance/access-reviews-overview.md)