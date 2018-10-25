---
title: Co je Azure Active Directory (Azure AD)? | Microsoft Docs
description: Zjistěte, jak pomocí Azure Active Directory rozšířit stávající místní identity do cloudu nebo vyvíjet integrované aplikace Azure AD.
services: active-directory
author: eross-msft
manager: mtillman
ms.author: lizross
ms.assetid: 498820c4-9ebe-42be-bda2-ecf38cc514ca
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: overview
ms.date: 09/13/2018
ms.custom: it-pro
ms.openlocfilehash: 406baeac60c7c0cdf5f74876e5fc29ea23d3d6f6
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49957543"
---
# <a name="what-is-azure-active-directory"></a>Představení služby Azure Active Directory
Azure Active Directory (Azure AD) je cloudová adresářová služba určená pro více tenantů a služba pro správu identit od Microsoftu. Azure AD v jediném řešení kombinuje základní adresářové služby, správu přístupu k aplikacím a ochranu identity a nabízí tak platformu založenou na standardech, čímž pomáhá vývojářům integrovat do jejich aplikací řízení přístupu na základě centralizovaných zásad a pravidel.

![Sada komponent Azure AD Connect](./media/active-directory-whatis/Azure_Active_Directory.png)

## <a name="benefits-of-azure-ad"></a>Výhody Azure AD
Azure AD pomáhá:

-   Vytvořit a spravovat pro každého uživatele v celém podniku jedinou identitu a zajistit synchronizaci uživatelů, skupin a zařízení pomocí [Azure AD Connect](../connect/active-directory-aadconnect.md).

-   Poskytovat prostřednictvím jednotného přihlašování přístup k vašim aplikacím, včetně tisíců předem integrovaných aplikací SaaS, a poskytovat více zabezpečený vzdálený přístup k místním aplikacím SaaS pomocí [Proxy aplikací služby Azure AD](../manage-apps/application-proxy.md).

-   Umožnit zabezpečení přístupu k aplikacím tím, že u místních i cloudových aplikací budete vynucovat zásady [Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md) na základě pravidel.

-   Zvýšit produktivitu uživatelů pomocí [samoobslužného resetování hesla](../user-help/user-help-reset-password.md) a požadavků na přístup k aplikacím a skupinám pomocí [portálu MyApps](../user-help/active-directory-saas-access-panel-introduction.md).

-   Využívat výhod [vysoké dostupnosti a spolehlivosti](https://docs.microsoft.com/azure/architecture/checklist/availability) celosvětového cloudového řešení pro správu identit a přístupu na podnikové úrovni.

## <a name="who-uses-azure-ad"></a>Kdo používá Azure AD
Služba Azure AD je určená pro správce IT, vývojáře aplikací a pro uživatele Office 365, Azure nebo Dynamics CRM Online.

- **Správci IT.** Azure AD poskytuje bezpečnější řešení pro vaši organizaci tím, že využívá silnější správu identit a umožňuje přístup k tisícům [cloudových aplikací SaaS](../saas-apps/tutorial-list.md) a místních aplikací prostřednictvím jednotného přihlašování. Prostřednictvím těchto aplikací také získáte cloudové zabezpečení aplikací, bezproblémový přístup, vylepšenou spolupráci a automatizaci životního cyklu identit pro vaše uživatele, což vám pomůže zvýšit úroveň zabezpečení i dodržování předpisů.

    Navíc můžete pomocí [Azure AD Connect](../connect/active-directory-aadconnect-get-started-express.md) integrovat Azure AD se stávající službou Active Directory na Windows Serveru a umožnit tak vaší organizaci využít investice do stávajících místních identit ke správě přístupu ke cloudovým aplikacím SaaS.

- **Pro vývojáře aplikací.** Azure AD vám pomůže soustředit se na vytváření aplikací tím, že vám poskytne integraci s řešením pro správu identit, které používají miliony organizací po celém světě.

- **Pro zákazníky Office 365, Azure nebo Dynamics CRM Online.** Už používáte Azure AD. Každý tenant Office 365, Azure a Dynamics CRM Online je ve skutečnosti tenantem Azure AD. Díky tomu můžete okamžitě začít se správou přístupu uživatelů k vašim integrovaným cloudovým aplikacím.

## <a name="how-reliable-is-azure-ad"></a>Jak spolehlivá je služba Azure AD?
Geograficky distribuovaný a vysoce dostupný návrh služby Azure AD určený pro více tenantů znamená, že se na ni můžete spolehnout i v souvislosti se svými nejdůležitějšími obchodními potřebami. Služba Azure AD je provozována z 28 datacenter po celém světě s automatickým převzetím služeb při selhání. To znamená, že pokud dojde k výpadku nějakého datového centra, stále bude k dispozici okamžitý přístup ke kopiím dat ve vašem adresáři alespoň ve dvou regionálně více vzdálených datových centrech.

Další informace o smlouvách SLA najdete v tématu [Smlouvy SLA](https://azure.microsoft.com/support/legal/sla/).

## <a name="choose-an-edition"></a>Volba edice
Všechny obchodní služby Microsoft Online se s přihlašováním a dalšími potřebami souvisejícími s identitami spoléhají na Azure AD. Pokud se přihlásíte k odběru jakékoli obchodní služby Microsoft Online (například Office 365 nebo Microsoft Azure), automaticky získáte Azure AD s přístupem ke všem funkcím úrovně Free. Pomocí edice Azure Active Directory Free můžete spravovat uživatele a skupiny, synchronizovat soubory s místními adresáři a využívat jednotné přihlašování v Azure, Office 365 a tisících oblíbených aplikací SaaS, jako jsou Salesforce, Workday, Concur, DocuSign, Google Apps, Box, ServiceNow, Dropbox a další. 

Pokud chcete zvýšit úroveň implementace Azure AD, můžete také přidat placené funkce upgradem na edici Azure Active Directory Basic, Premium P1 nebo Premium P2. Placené edice Azure AD jsou postavené na vašem stávajícím bezplatném adresáři a poskytují vašim pracovníkům na mobilních zařízeních funkce podnikové třídy, mezi které patří samoobslužnost, rozšířené monitorování, generování sestav zabezpečení, služba Multi-Factor Authentication (MFA) a zabezpečený přístup.

> [!NOTE]
> Cenové možnosti těchto edic najdete na stránce s [cenami Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). Azure Active Directory Premium P1, Premium P2 a Azure Active Directory Basic se aktuálně nepodporují v Číně. Další informace o cenách Azure AD můžete získat na fóru služby Azure Active Directory.

- **Azure Active Directory Basic.** Tato edice je zamýšlená pro pracovníky, kteří zpracovávají konkrétní úkoly a u kterých je využívání cloudu na prvním místě. Poskytuje přístup k aplikacím orientovaný na cloud a samoobslužná řešení správy identit. Edice Basic vám poskytne funkce pro zvýšení produktivity a snížení nákladů, jako je správa přístupu na základě skupin, samoobslužné resetování hesel pro cloudové aplikace a proxy aplikací služby Azure Active Directory (umožňující publikování místních webových aplikací pomocí Azure AD), a to vše s podnikovou smlouvou o úrovni služeb (SLA) zaručující 99,9% dostupnost.

- **Azure Active Directory Premium P1.** Edice Azure Active Directory Premium je zamýšlená tak, aby uspokojila náročnějších požadavků organizací na správu identit a přístupů. Zajišťuje bohaté možnosti správy identit na úrovni podniku a umožňuje hybridním uživatelům bezproblémový přístup k místním i cloudovým funkcím. Tato edice obsahuje vše, co informační pracovníci a správci identit potřebují v hybridním prostředí k zajištění přístupu k aplikacím, samoobslužné správě identit a přístupu (IAM), ochraně identit a zabezpečení v cloudu. Podporuje pokročilou správu a prostředky pro delegování, jako jsou dynamické skupiny a samoobslužná správa skupin. Zahrnujte sadu Microsoft Identity Manager (sada pro místní správu identit a přístupu) a poskytuje cloudové možnosti zpětného zápisu, které místním uživatelům umožňují využívat řešení, jako je samoobslužné resetování hesla.

- **Azure Active Directory Premium P2.** Tato edice je navržená kolem rozšířené ochrany uživatelů a správců. Tato nová nabídka zahrnuje všechny funkce v edici Azure AD Premium P1 a také službu Identity Protection a technologii Privileged Identity Management. Azure Active Directory Identity Protection využívá miliardy signálů a poskytuje podmíněný přístup na základě rizika k vašim aplikacím a důležitým datům společnosti. Prostřednictvím technologie Azure Active Directory Privileged Identity Management také pomáháme spravovat a chránit privilegované účty, takže můžete zjišťovat, omezovat a monitorovat správce a jejich přístup k prostředkům a v případě potřeby poskytovat přístup za běhu.  

> [!NOTE]
> Několik funkcí Azure Active Directory je také k dispozici v edicích s průběžnými platbami:<ul><li>**Azure Active Directory B2C.** Řešení pro správu identit a přístupu pro vaše zákaznické aplikace. Další informace najdete v tématu [Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/).</li><li>**Azure Multi-Factor Authentication.** Používá poskytovatele podle uživatele nebo ověření. Další informace najdete v tématu vysvětlujícím, [co je Azure Multi-Factor Authentication](../authentication/multi-factor-authentication.md).

## <a name="as-an-admin-how-do-i-get-started"></a>Jak mám začít jako správce?
Zaregistrujte si bezplatnou 30denní zkušební verzi a nasaďte svoje první cloudové řešení. Více informací najdete v tématu věnovaném [zkušební verzi Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/).

## <a name="as-a-developer-how-do-i-get-started"></a>Jak mám začít jako vývojář?
Zaregistrujte si bezplatnou 30denní zkušební verzi ještě dnes a začněte integrovat své aplikace se službou Azure AD. Více informací najdete v tématu věnovaném [zkušební verzi Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/). Další informace najdete také v [příručce pro vývojáře](../develop/v1-overview.md) pro Azure Active Directory.

## <a name="next-steps"></a>Další kroky
- [Další informace o základech správy identit a přístupu v Azure](identity-fundamentals.md).

- [Integrace Azure AD se službou Windows Server Active Directory](../hybrid/how-to-connect-install-express.md).
