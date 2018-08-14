---
title: Co je Azure Active Directory (Azure AD)? | Microsoft Docs
description: Pomocí Azure Active Directory můžete rozšířit stávající místní identity do cloudu nebo vyvíjet integrované aplikace Azure AD.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.author: lizross
ms.assetid: 498820c4-9ebe-42be-bda2-ecf38cc514ca
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/09/2018
ms.custom: it-pro
ms.openlocfilehash: 5087f759d682382bc22b5f95f462fe0f08619cc8
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449990"
---
# <a name="what-is-azure-active-directory"></a>Představení služby Azure Active Directory
Azure Active Directory (Azure AD) od Microsoftu je víceklientská cloudová adresářová služba a služba pro správu identit. V rámci jednoho řešení kombinuje základní adresářové služby, správu přístupu k aplikacím a ochranu identity. Azure AD také nabízí bohatou platformu založenou na standardech, ve které můžou vývojáři řídit přístup k aplikacím na základě centralizovaných zásad a pravidel.

![Sada komponent Azure AD Connect](./media/active-directory-whatis/Azure_Active_Directory.png)

- **Pro správce IT.** Azure AD poskytuje bezpečnější řešení pro vaši organizaci tím, že využívá silnější správu identit a umožňuje přístup k tisícům [cloudových aplikací SaaS](../saas-apps/tutorial-list.md) a místních aplikací prostřednictvím jednotného přihlašování. Prostřednictvím těchto aplikací také získáte cloudové zabezpečení aplikací, bezproblémový přístup, vylepšenou spolupráci a automatizaci životního cyklu identit pro vaše zaměstnance, což vám pomůže zvýšit úroveň zabezpečení i dodržování předpisů.

    Navíc můžete pomocí pouhých [čtyř kliknutí](./../connect/active-directory-aadconnect-get-started-express.md) integrovat Azure AD se stávající službou Active Directory na Windows Serveru a umožnit tak vaší organizaci využít investice do stávajících místních identit ke správě přístupu ke cloudovým aplikacím SaaS.

- **Pro vývojáře aplikací.** Azure AD vám umožní soustředit se na vytváření aplikací tím, že vám umožní integraci s řešením pro správu identit, které používají miliony organizací po celém světě.

- **Pro zákazníky Office 365, Azure nebo Dynamics CRM Online.** Už používáte Azure AD. Každý tenant Office 365, Azure a Dynamics CRM Online je ve skutečnosti tenantem Azure AD. Díky tomu můžete okamžitě začít se správou přístupu zaměstnanců k vašim integrovaným cloudovým aplikacím.

## <a name="how-reliable-is-azure-ad"></a>Jak spolehlivá je služba Azure AD?
Víceklientský, geograficky distribuovaný a vysoce dostupný návrh služby Azure AD znamená, že se na ni můžete spolehnout i se svými nejdůležitějšími obchodními potřebami. Azure AD se provozuje ve 28 datových centrech po celém světě a poskytuje automatizované převzetí služeb při selhání. Díky tomu můžete být bez obav s vědomím, že je služba Azure AD vysoce spolehlivá a i když dojde k výpadku nějakého datového centra, stále máte k dispozici okamžitý přístup ke kopiím dat ve vašem adresáři alespoň ve dvou regionálně více vzdálených datových centrech.

Další informace o smlouvách SLA najdete v tématu [Smlouvy SLA](https://azure.microsoft.com/support/legal/sla/).

## <a name="choose-an-edition"></a>Volba edice
Všechny obchodní služby Microsoft Online se s přihlašováním a dalšími potřebami souvisejícími s identitami spoléhají na Azure Active Directory (Azure AD). Pokud se přihlásíte k odběru jakékoli obchodní služby Microsoft Online (například Office 365 nebo Microsoft Azure), získáte Azure AD s přístupem ke všem funkcím úrovně Free. S edicí Azure Active Directory Free můžete spravovat uživatele a skupiny, synchronizovat soubory s místními adresáři a využívat jednotné přihlašování v Azure, Office 365 a tisících oblíbených aplikací SaaS, jako jsou Salesforce, Workday, Concur, DocuSign, Google Apps, Box, ServiceNow, Dropbox a další. 

Pokud chcete zvýšit úroveň Azure Active Directory, můžete přidat placené funkce prostřednictvím edic Azure Active Directory Basic, Premium P1 a Premium P2. Placené edice Azure Active Directory jsou postavené na vašem stávajícím bezplatném adresáři a poskytují vašim pracovníkům na mobilních zařízeních funkce podnikové třídy, mezi které patří samoobslužnost, rozšířené monitorování, generování sestav zabezpečení, služba Multi-Factor Authentication (MFA) a zabezpečený přístup.

> [!NOTE]
> Cenové možnosti těchto edic najdete na stránce s [cenami Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). Azure Active Directory Premium P1, Premium P2 a Azure Active Directory Basic se aktuálně nepodporují v Číně. Další informace o cenách Azure AD můžete získat na fóru služby Azure Active Directory.
>

* **Azure Active Directory Basic** – Tato edice je navržená pro pracovníky, kteří zpracovávají konkrétní úkoly a u kterých je využívání cloudu na prvním místě. Poskytuje přístup k aplikacím orientovaný na cloud a samoobslužná řešení pro správu identit. Edice Basic služby Azure Active Directory vám poskytne funkce pro zvýšení produktivity a snížení nákladů, jako je správa přístupu na základě skupin, samoobslužné resetování hesel pro cloudové aplikace a Proxy aplikací služby Azure Active Directory (umožňující publikování místních webových aplikací pomocí Azure Active Directory), a to vše se smlouvou SLA na podnikové úrovni zaručující 99,9% dostupnost.
* **Azure Active Directory Premium P1** – Tato edice je navržená tak, aby uspokojila náročnější požadavky organizací na správu identit a přístupu. Zajišťuje bohaté možnosti správy identit na podnikové úrovni a umožňuje hybridním uživatelům bezproblémový přístup k místním i cloudovým funkcím. Tato edice obsahuje vše, co informační pracovníci a správci identit potřebují v hybridním prostředí k zajištění přístupu k aplikacím, samoobslužné správě identit a přístupu (IAM), ochraně identit a zabezpečení v cloudu. Podporuje pokročilou správu a prostředky pro delegování, jako jsou dynamické skupiny a samoobslužná správa skupin. Zahrnujte sadu Microsoft Identity Manager (sada pro místní správu identit a přístupu) a poskytuje cloudové možnosti zpětného zápisu, které místním uživatelům umožňují využívat řešení, jako je samoobslužné resetování hesla.
* **Azure Active Directory Premium P2** – Tato edice je navržená s rozšířenou ochranou všech uživatelů a správců. Tato nová nabídka zahrnuje všechny funkce v edici Azure AD Premium P1 a také službu Identity Protection a technologii Privileged Identity Management. Azure Active Directory Identity Protection využívá miliardy signálů a poskytuje podmíněný přístup na základě rizika k vašim aplikacím a důležitým datům společnosti. Prostřednictvím technologie Azure Active Directory Privileged Identity Management také pomáháme spravovat a chránit privilegované účty, takže můžete zjišťovat, omezovat a monitorovat správce a jejich přístup k prostředkům a v případě potřeby poskytovat přístup za běhu.  

> [!NOTE]
> Několik funkcí Azure Active Directory je k dispozici v edicích s průběžnými platbami:
>
> * Active Directory B2C je řešení pro správu identit a přístupu pro vaše zákaznické aplikace. Další informace najdete v tématu [Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/).
> * Azure Multi-Factor Authentication je možné využívat prostřednictvím poskytovatelů pro jednotlivé uživatele nebo jednotlivá ověřování. Další informace najdete v tématu [Co je Azure Multi-Factor Authentication?](../authentication/multi-factor-authentication.md)
>

## <a name="how-can-i-get-started"></a>Jak začít?

**Pokud jste správce IT:**

* [Vyzkoušejte si to.](https://azure.microsoft.com/trial/get-started-active-directory/) – Pomocí tohoto odkazu si můžete ještě dnes zaregistrovat bezplatnou 30denní zkušební verzi a nasadit první cloudové řešení za méně než 5 minut.

* Přečtěte si téma [Začínáme s Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-get-started-premium), kde najdete tipy a triky k rychlému zprovoznění tenanta Azure AD.

**Pokud jste vývojář:**
 
* Přečtěte si [příručku pro vývojáře](../develop/azure-ad-developers-guide.md) k Azure Active Directory.

* [Začněte se zkušební verzí](https://azure.microsoft.com/trial/get-started-active-directory/) – Zaregistrujte si bezplatnou 30denní zkušební verzi ještě dnes a začněte integrovat své aplikace se službou Azure AD.

## <a name="next-steps"></a>Další kroky
[Další informace o základech správy identit a přístupu v Azure](https://docs.microsoft.com/azure/active-directory/identity-fundamentals)
