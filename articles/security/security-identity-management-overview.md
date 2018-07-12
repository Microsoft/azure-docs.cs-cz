---
title: Zabezpečení Azure funkce, které pomáhají se správou identit | Dokumentace Microsoftu
description: " Tento článek obsahuje základní informace o základní funkce zabezpečení Azure, které pomáhají se správou identit. Microsoft identity a přístupu k řešení Nápověda pro funkci správy IT chránit přístup k aplikacím a prostředkům v podnikovém datovém centru a cloudu, povolení další úrovně ověřování, jako je například Vícefaktorové ověřování a podmíněného přístupu zásady. "
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
ms.openlocfilehash: cbf6ac736db9935f5ec52fa4507dab1e56bcde43
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611183"
---
# <a name="azure-identity-management-security-overview"></a>Přehled zabezpečení správy identit Azure
Microsoft identity a přístupu k řešení Nápověda pro funkci správy IT chránit přístup k aplikacím a prostředkům v podnikovém datovém centru a cloudu. Tento typ ochrany umožňuje další úrovně ověřování, jako je například Vícefaktorové ověřování a zásady podmíněného přístupu. Prostřednictvím rozšířeného zabezpečení vytváření sestav, auditování a upozornění, že pomáhá zmírnit potenciální potíže se zabezpečením monitorování podezřelou aktivitu. [Azure Active Directory Premium](../active-directory/active-directory-editions.md) poskytuje jednotné přihlašování (SSO) k tisícům cloudový software jako služba (SaaS) aplikací a přístup k webovým aplikacím, které běží místně.

S využitím výhod zabezpečení služby Azure Active Directory (Azure AD), můžete:

* Vytvářejte a spravujte jedinou identitu pro každého uživatele ve vaší organizaci hybridní udržování synchronizace uživatele, skupiny a zařízení.
* Poskytuje přístup přes jednotné přihlašování k aplikacím, včetně tisíce předem integrovaných aplikací SaaS.
* Povolit zabezpečení přístupu aplikací tím, že vynucuje založeného na pravidlech ověřování službou Multi-Factor Authentication v místním i cloudovým aplikacím.
* Zřízení zabezpečený vzdálený přístup k místním webovým aplikacím prostřednictvím Proxy aplikací Azure AD.

Cílem tohoto článku je poskytnout přehled o základní funkce zabezpečení Azure, které pomáhají se správou identit. Poskytujeme také odkazy na články, které poskytují podrobnosti o každé funkce tak další informace.  

Tento článek se zaměřuje na následující možnosti správy identit Azure core:

* Jednotné přihlašování
* Reverzní proxy server
* Multi-Factor Authentication
* Monitorování zabezpečení, oznámení a sestavy založené na učení počítače
* Správa identit a přístupu zákazníků
* Registrace zařízení
* Privileged identity management.
* Ochrana identit
* Hybridní Správa identit

## <a name="single-sign-on"></a>Jednotné přihlašování
Jednotné přihlašování znamená, že schopnost přistupovat ke všem aplikacím a prostředkům, které je potřeba udělat firmy, tak, že stačí, když pomocí jediného uživatelského účtu. Jakmile se přihlásíte, dostanete všechny aplikace, které potřebujete, aniž byste museli znovu ověřovat (třeba zadávat heslo) podruhé.

Mnoho organizací spoléhat na aplikace SaaS, jako je například Salesforce, Office 365 a pole pro snížení produktivity uživatelů. V minulosti pracovníky technické potřeby jednotlivě vytvářet a aktualizovat uživatelské účty v jednotlivých aplikací SaaS a uživatelé museli Zapamatovat heslo pro každou aplikaci SaaS.

Azure AD rozšiřuje místní služby Active Directory prostředí do cloudu, uživatelům umožníte používat jejich primární účet organizace k přihlášení nejen do jejich zařízení připojených k doméně a firemním prostředkům, ale také musí pro všechny webové a aplikacím SaaS pro svou práci.

Nejen uživatele není potřeba spravovat víc kopií uživatelská jména a hesla, můžete zřizovat nebo zrušit zřízení přístup k aplikaci automaticky, na základě jejich organizační skupiny a jejich stav zaměstnance. Azure AD přináší zabezpečení a řízení přístupu zásad správného řízení, se kterými můžete centrálně spravovat přístup uživatelů v aplikacích SaaS.

Další informace:

* [Přehled jednotného přihlašování](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../active-directory/manage-apps/what-is-single-sign-on.md)
* [Integrace Azure Active Directory jednotné přihlašování s aplikacemi SaaS](../active-directory/manage-apps/configure-single-sign-on-portal.md)

## <a name="reverse-proxy"></a>Reverzní proxy server
Proxy aplikací Azure AD umožňuje publikování místních aplikací, jako například [SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) lokalit, [Outlook Web App](https://technet.microsoft.com/library/jj657718.aspx), a [IIS](http://www.iis.net/)– na základě aplikace v rámci vaší privátní sítě a poskytuje zabezpečený přístup uživatelům mimo vaši síť. Proxy aplikací poskytuje vzdálený přístup a jednotné přihlašování pro mnoho typů místních webových aplikací s nepřeberným množstvím aplikací SaaS, které podporuje Azure AD. Zaměstnanci můžou přihlásit k aplikacím z domácí na svá vlastní zařízení a ověřovat přes tento proxy server založený na cloudu.

Další informace:

* [Povolení Proxy aplikace služby Azure AD](../active-directory/manage-apps/application-proxy-enable.md)
* [Publikování aplikací pomocí Proxy aplikací Azure AD](../active-directory/active-directory-application-proxy-publish.md)
* [Jednotné přihlašování s Proxy aplikací](../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
* [Práce s podmíněným přístupem](../active-directory/manage-apps/application-proxy-integrate-with-sharepoint-server.md)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication
Azure Multi-Factor Authentication je metoda ověřování, který vyžaduje použití víc ověřovacích metod a přidá do uživatelská přihlášení a transakce velmi důležitou druhou vrstvu zabezpečení. Multi-Factor Authentication pomáhá chránit přístup k datům a aplikace a současně plní požadavky uživatelů na jednoduchý přihlašovací proces. Nabízí silné ověřování přes celou řadu možností ověřování: telefonních hovorů, textových zpráv nebo oznámení mobilní aplikace nebo ověřovací kódy a tokenů OAuth třetích stran.

Další informace:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Co je Azure Multi-Factor Authentication?](../active-directory/authentication/multi-factor-authentication.md)
* [Jak funguje ověřování Azure Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Monitorování zabezpečení, oznámení a sestavy založené na učení počítače
Monitorování zabezpečení, výstrah a machine learning sestav založených na, které rozpoznávají nekonzistentní vzorce přístupu můžete pomoct chránit vaši firmu. Můžete použít přístup služby Azure AD a sestavy využití na získat náhled do integrity a zabezpečení adresáři vaší organizace. Pomocí těchto informací správce adresáře může zjistit, kde můžou být potenciální bezpečnostní rizika, mohli odpovídajícím způsobem plánovat pro zmírnění rizika.

Na webu Azure Portal sestavy spadají do následujících kategorií:

* **Sestavy anomálií**: obsahovat události přihlášení nalezené na neobvyklé. Naším cílem je informovat vás tyto aktivity a umožní určit, zda je podezřelé události.
* **Integrované sestavy aplikací**: poskytují přehled o používání cloudových aplikací ve vaší organizaci. Azure AD umožňuje integraci s tisíci cloudových aplikací.
* **Zprávy o chybách**: označení chyb, které mohou nastat při zřizování účtů do externí aplikace.
* **Sestavy pro konkrétní uživatele**: zobrazit data aktivit přihlašování zařízení pro konkrétního uživatele.
* **Protokoly aktivit**: obsahují záznam všechny auditované události v posledních 24 hodin, posledních 7 dnů, nebo posledních 30 dní a aktivitu změny skupiny a aktivita resetování a registraci hesla.

Další informace:

* [Zobrazení sestav přístupů a používání](../active-directory/active-directory-view-access-usage-reports.md)
* [Začínáme s vytvářením sestav Azure Active Directory](../active-directory/active-directory-reporting-getting-started.md)
* [Průvodce vytvářením sestav Azure Active Directory](../active-directory/active-directory-reporting-guide.md)

## <a name="consumer-identity-and-access-management"></a>Správa identit a přístupu zákazníků
Azure AD B2C je vysoce dostupný, globální služba pro správu identit pro zákaznické aplikace s možností škálování na stovky milionů identit. Dá se integrovat do mobilních i webových platforem. Zákazníky můžete přihlásit ke všem vašim aplikacím přes přizpůsobitelné prostředí pomocí svých existujících účtů v sociálních sítích nebo pomocí nově vytvořených přihlašovacích údajů.

V minulosti by vývojáři aplikací k podepisování zákazníci se těmito věcmi a přihlásí jej do svých aplikací napsali vlastní kód. A použili by místní databáze nebo systémy k ukládání uživatelských jmen a hesel. Azure AD B2C nabízí lepší způsob, jak integrovat správu identit uživatelů do aplikací pomocí zabezpečené, na standardech postavené platformy a velké škály rozšiřitelných zásad vaší organizace.

Při použití Azure AD B2C, zákazníci můžou si zaregistrovat pro vaše aplikace pomocí jejich účtů na sociálních sítích (Facebook, Google, Amazon, LinkedIn) nebo pomocí nově vytvořených přihlašovacích údajů (e-mailovou adresu a heslo, nebo uživatelské jméno a heslo).

Další informace:

* [Co je Azure Active Directory B2C?](https://azure.microsoft.com/services/active-directory-b2c/)
* [Azure Active Directory B2C ve verzi preview: přihlášení a přihlašování uživatelů aplikace](../active-directory-b2c/active-directory-b2c-overview.md)
* [Azure Active Directory B2C ve verzi Preview: Typy aplikací](../active-directory-b2c/active-directory-b2c-apps.md)

## <a name="device-registration"></a>Registrace zařízení
Registrace zařízení služby Azure AD je základem pro na základě zařízení [podmíněného přístupu](../active-directory/active-directory-conditional-access-device-registration-overview.md) scénáře. Když je zařízení registrováno, registrace zařízení služby Azure AD nabízí zařízení s identitou, která se používá k ověření zařízení při přihlášení uživatele. Ověřené zařízení a atributy zařízení pak slouží k vynucení zásad podmíněného přístupu pro aplikace, které jsou hostované v cloudu i lokálně.

V kombinaci s řešením pro správu mobilních zařízení, jako je například Intune, budou atributy zařízení ve službě Azure AD jsou aktualizovány o další informace o zařízení. Potom můžete vytvořit pravidla podmíněného přístupu, které vynucují přístup ze zařízení, která splňují vaše standardy zabezpečení a dodržování předpisů.

Další informace:

* [Začínáme s registrací zařízení ve službě Azure AD](../active-directory/active-directory-conditional-access-device-registration-overview.md)
* [Automatická registrace zařízení ve službě Azure AD pro zařízení s Windows připojených k doméně](../active-directory/active-directory-conditional-access-automatic-device-registration.md)
* [Nastavte si Automatická registrace Windows zařízení připojených k doméně pomocí Azure AD](../active-directory/active-directory-conditional-access-automatic-device-registration-setup.md)

## <a name="privileged-identity-management"></a>Privileged identity management.
S Azure AD Privileged Identity Management můžete spravovat, řídit a monitorovat privilegované identity a přístup k prostředkům v Azure AD a dalších online službách Microsoftu, jako je Office 365 a Microsoft Intune.

Uživatelé někdy budou muset provádět privilegované operace prostředků Azure nebo Office 365, nebo v jiných aplikacích SaaS. Tyto potřeby často znamená, že organizace mají uživatelům poskytnout trvalé privilegovaný přístup v Azure AD. Takový přístup je rostoucí bezpečnostní riziko pro prostředky hostované v cloudu, protože organizace nemůže monitorovat dostatečně co uživatelé dělají s jejich oprávněními správce. Navíc pokud dojde k ohrožení uživatelský účet s privilegovaným přístupem, tento jeden porušení zabezpečení mohou ovlivnit celkové zabezpečení cloudu organizace. Azure AD Privileged Identity Management pomáhá zmírnit rizika.

S Azure AD Privileged Identity Management můžete:

* Zjistěte, kteří jsou správci služby Azure AD.
* Povolte na vyžádání, just-in-time (JIT) pro správu přístupu ke službám Microsoftu, jako je Office 365 a Intune.
* Získejte sestavy obsahující historii přístupů správce a změny v přiřazení správce.
* Dostávejte upozornění týkající se přístupu k privilegovanou roli.

Další informace:

* [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md)
* [Role ve službě Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-roles.md)
* [Azure AD Privileged Identity Management: Jak přidat nebo odebrat roli uživatele](../active-directory/privileged-identity-management/pim-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Ochrana identit
Služba Azure AD Identity Protection je služba zabezpečení, která poskytuje umožní získat přehled o rizikových událostech a potenciálních chybách zabezpečení, které ovlivňují identity ve vaší organizaci. Identity Protection využívá stávající možnosti detekce anomálií služby Azure AD, které jsou k dispozici prostřednictvím sestav neobvyklých aktivit služby Azure AD. Identity Protection také zavádí nové typy rizikových událostí, které dokáží detekovat anomálie v reálném čase.

Další informace:

* [Azure AD Identity Protection](../active-directory/active-directory-identityprotection.md)
* [Kanál 9: Azure AD a Identity Show: Identity Protection ve verzi Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-management"></a>Hybridní Správa identit
Přístup Microsoftu k identitě zahrnuje na místě i v cloudu, vytváření identity jednoho uživatele pro ověřování a autorizaci ke všem prostředkům, bez ohledu na umístění.

Další informace:

* [Dokument white paper k hybridní identity](http://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
* [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Blog týmu Azure AD](https://blogs.technet.microsoft.com/ad/)
