---
title: Představení služby Azure Active Directory – Azure Active Directory | Dokumentace Microsoftu
description: Přehled a koncepční informace o Azure Active Directory, včetně terminologie, jaké licence jsou k dispozici, a seznam přidružených funkcí s odkazy pro další informace.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: overview
ms.date: 07/31/2019
ms.author: ajburnle
ms.custom: it-pro, seodec18, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd7b412e99526935738c2494d31a16fded7101a6
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78386036"
---
# <a name="what-is-azure-active-directory"></a>Představení služby Azure Active Directory

Azure Active Directory (Azure AD) je cloudová služba pro správu identit a přístupu od Microsoftu, která pomáhá vašim zaměstnancům přihlašovat se a přistupovat k prostředkům v nástroji:

- Externí prostředky, jako je například Microsoft Office 365, na webu Azure portal a tisícům dalších aplikací SaaS.

- Interním prostředkům, jako jsou například aplikace v podnikové síti a intranetu, spolu s všechny cloudové aplikace vyvinuté ve vaší vlastní organizaci.

K lepšímu pochopení základních služeb identit v Azure, Azure AD a Office 365 můžete použít různé [Microsoft Cloud pro plakáty řady Enterprise Architects](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources#identity) .

## <a name="who-uses-azure-ad"></a>Kdo používá Azure AD?

Azure AD je určený pro:

- **Správci IT.** Jako správce IT můžete použít Azure AD pro řízení přístupu pro vaše aplikace a prostředky aplikace založené na vašich obchodních požadavcích. Například můžete použít Azure AD tak, aby vyžadovala vícefaktorové ověřování při přístupu k důležitým prostředkům organizace. Kromě toho můžete použít Azure AD k automatizaci zřizování uživatelů mezi stávající Windows Server AD a cloudovým aplikacím, včetně Office 365. A konečně Azure AD poskytuje výkonné nástroje k automatické ochraně identit uživatelů a přihlašovacích údajů a aby splňovaly vaše požadavky zásad správného řízení přístupu. Pokud chcete začít, zaregistrujte si [bezplatnou 30denní zkušební verzi Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/).

- **Vývojáři aplikací** Jako vývojář aplikace můžete použít Azure AD jako přístup založený na standardech pro přidání jednotného přihlašování (SSO) do vaší aplikace a tím umožníte, aby fungoval s předem existujícími přihlašovacími údaji uživatele. Azure AD taky poskytuje rozhraní API, která vám pomůžou sestavovat přizpůsobená prostředí aplikací pomocí stávajících dat organizace. Pokud chcete začít, zaregistrujte si [bezplatnou 30denní zkušební verzi Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/). Další informace najdete také v tématu [Azure Active Directory pro vývojáře](../develop/index.yml).

- **Microsoft 365, předplatitelé Office 365, Azure nebo Dynamics CRM Online.** Jako předplatitel které už používáte Azure AD. Každý tenant Microsoft 365, Office 365, Azure a Dynamics CRM Online je automaticky tenanta služby Azure AD. Můžete okamžitě začít spravovat přístup k integrované cloudové aplikace.

## <a name="what-are-the-azure-ad-licenses"></a>Co jsou licence Azure AD?

Firemní služby Microsoft Online, jako je Office 365 nebo Microsoft Azure vyžadují služby Azure AD pro přihlášení a které vám pomůžou s identity protection. Pokud se přihlásíte k odběru jakékoli obchodní služby Microsoft Online, automaticky získáte Azure AD s přístupem ke všem bezplatným funkcím.

Pokud chcete zlepšit implementaci Azure AD, můžete také přidat placené možnosti tím, že upgradujete na licence Azure Active Directory Premium P1 nebo Premium P2. Placené licence Azure AD jsou postavené na svém stávajícím volném adresáři, který poskytuje samoobslužné a rozšířené monitorování, generování sestav zabezpečení a zabezpečený přístup pro mobilní uživatele.

>[!Note]
>Cenové možnosti těchto licencí najdete v tématu [Azure Active Directory ceny](https://azure.microsoft.com/pricing/details/active-directory/).
>
>Azure Active Directory Premium P1 a Premium P2 se v Číně aktuálně nepodporují. Pokud chcete získat další informace o cenách služby Azure AD, obraťte se na [fórum Azure Active Directory](https://azure.microsoft.com/support/community/?product=active-directory).

- **Azure Active Directory Free.** Poskytuje správu uživatelů a skupin, místní synchronizaci adresářů, základní sestavy, samoobslužnou změnu hesla pro cloudové uživatele a jednotné přihlašování v Azure, Office 365 a spoustě oblíbených aplikací SaaS.

- **Azure Active Directory Premium P1.** Kromě bezplatných funkcí umožňuje zařízení P1 také vašim hybridním uživatelům přístup k místním i cloudovým prostředkům. Podporuje také pokročilou správu, jako je například dynamických skupin, samoobslužné správy skupin, Microsoft Identity Manageru (místních identit a přístupu správy sady) a možností cloudu u zpětný zápis, které umožňují samoobslužné resetování hesla pro Místní uživatelé.

- **Azure Active Directory Premium P2.** Kromě funkcí Free a P1 nabízí P2 také [Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md) , které vám pomůžou zajistit podmíněný přístup na základě rizik pro vaše aplikace a kritická podniková data a [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md) , aby mohli zjišťovat, omezovat a monitorovat správce a jejich přístup k prostředkům a v případě potřeby poskytovat přístup za běhu.

- **Licence k funkcím průběžné platby.** Můžete také získat licence další funkce, jako je Azure Active Directory Business na zákazníka (B2C). B2C můžete poskytovat identitu a přístup k řešení pro správu vašich aplikací určených pro zákazníky. Další informace najdete v [dokumentaci Azure Active Directory B2C](../../active-directory-b2c/index.yml).

Další informace o tom, jak přidružit předplatné Azure ke službě Azure AD, najdete v tématu Postup [: přidružení nebo přidání předplatného Azure k Azure Active Directory](active-directory-how-subscriptions-associated-directory.md) a další informace o přiřazování licencí vašim uživatelům najdete v tématu [Postup: přiřazení nebo odebrání licencí Azure Active Directory](license-users-groups.md).

## <a name="terminology"></a>Terminologie

Abyste lépe pochopili službu Azure AD a její dokumentaci, doporučujeme, abyste provedli následující výrazy.

|Ukončení nebo koncept|Popis|
|---------------|-----------|
|Identita| Věc, která může získat ověření. Identitou může být uživatel s uživatelským jménem a heslem. Identity také obsahují aplikace nebo jiné servery, které mohou vyžadovat ověření prostřednictvím tajných klíčů nebo certifikátů.|
|Účet| Identita, která obsahuje data, která jsou k němu přidružená. Nemůžete mít účet bez identity.|
|Účet Azure AD| Identita vytvořené pomocí Azure AD nebo jinou cloudovou službu Microsoftu, jako je Office 365. Identity jsou uložené ve službě Azure AD a jejich zpřístupnění předplaceným cloudovým službám vaší organizace. Tento účet se také někdy označuje jako pracovní nebo školní účet.|
|Předplatné Azure| Použité k úhradě služby Azure cloud services. Máte více předplatných a jsou propojeny na platební kartu.|
|Tenant Azure| Vyhrazené a důvěryhodné instance služby Azure AD, který se automaticky vytvoří, pokud vaše organizace zaregistruje předplatné služby cloud Microsoftu, jako je například Microsoft Azure, Microsoft Intune nebo Office 365. Klient služby Azure představuje jednu organizaci.|
|Jeden tenant| Klienty Azure, které přístupu k jiným službám ve vyhrazeném prostředí jsou považovány za jednoho tenanta.|
|Víc klientů| Klienty Azure, které v několika organizacích, přístupu k jiným službám ve sdíleném prostředí, jsou považovány za více tenantů.|
|Adresář Azure AD|Vyhrazené a důvěryhodné Azure má každý tenant Azure AD directory. Adresář Azure AD obsahuje uživatele, skupiny nebo aplikace vašeho tenanta a slouží k provádění identita a přístup k funkcím správy prostředků tenanta.|
|Vlastní doména|Každý nový Azure AD directory je k dispozici s počáteční název domény, domainname.onmicrosoft.com. Kromě toho počáteční název, můžete také přidat názvy, které zahrnují názvy, které používáte pro firmy a vaši uživatelé používat pro přístup k prostředkům ve vaší organizaci, do seznamu domény vaší organizace. Přidání vlastních názvů domén vám pomůže vytvořit uživatelská jména, která se uživatelům znají, například alain@contoso.com.|
|Správce účtu|Tato role správce v klasickém modelu předplatného se koncepčně fakturační vlastník předplatného. Tato role má přístup k [centrum účtů Azure](https://account.azure.com/Subscriptions) a umožňuje spravovat všechna předplatná v účtu. Další informace najdete v tématech [role správců klasických předplatných, role řízení přístupu na základě role (RBAC) Azure a role správce Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Správce služeb|Tato role správce v klasickém modelu předplatného umožňuje spravovat všechny prostředky Azure, včetně přístupu. Tato role má ekvivalentní přístupu uživatele, který má přiřazenou roli vlastník v oboru předplatného. Další informace najdete v tématu [role správců pro klasický odběr, role Azure RBAC a role správce Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Vlastník|Tato role umožňuje spravovat všechny prostředky Azure, včetně přístupu. Tato role je založená na novější systém autorizace nazývá řízení přístupu na základ rolí (RBAC), který poskytuje propracovanou správu přístupu k prostředkům Azure. Další informace najdete v tématu [role správců pro klasický odběr, role Azure RBAC a role správce Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Globální správce Azure AD|Tato role správce je automaticky přiřazené pro všechny uživatele, kteří vytvořili tenanta Azure AD. Globální správci můžou dělat všechny funkce správy pro Azure AD a jakékoli služby, které federovat do Azure AD, jako je Exchange Online, SharePoint Online a Online Skype pro firmy. Můžete mít víc globálních správců, ale jenom globální správci (včetně přiřazování další globální správce) role správců můžou přiřazovat uživatelům.<br><br>**Poznámka**<br>Tato role správce se v Azure Portal nazývá globální správce, ale nazývá se to **Správce společnosti** v rozhraní Microsoft Graph API a Azure AD PowerShellu.<br><br>Další informace o různých rolích správců najdete [v tématu oprávnění role správce v Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).|
|Účet Microsoft (také nazývané, MSA)|Osobní účty, které poskytují přístup k vaší spotřebitelské produkty společnosti Microsoft a cloudovým službám, jako je Outlook, OneDrive, Xbox LIVE nebo Office 365. Váš účet Microsoft se vytvoří a uloží v Microsoft systémem identit uživatelů účtu, který se spouští společností Microsoft.|

## <a name="which-features-work-in-azure-ad"></a>Které funkce fungují ve službě Azure AD?

Po výběru licence Azure AD získáte přístup k některým nebo všem následujícím funkcím vaší organizace:

|Kategorie|Popis|
|-------|-----------|
|Správa aplikací|Správa cloudových a místních aplikací pomocí Proxy aplikací, jednotného přihlašování portálu Moje aplikace (označované také jako na přístupovém panelu) a Software jako služba (SaaS) aplikací. Další informace najdete v tématu [jak poskytnout zabezpečený vzdálený přístup k místním aplikacím](../manage-apps/application-proxy.md) a [dokumentaci správy aplikací](../manage-apps/index.yml).|
|Authentication|Správa Azure Active Directory, samoobslužné resetování hesla, ověření službou Multi-Factor Authentication, seznam vlastních zakázaných hesel a inteligentním uzamčením. Další informace najdete v [dokumentaci k ověřování Azure AD](../authentication/index.yml).|
|Business-to-Business (B2B)|Spravujte uživatele typu Host a externími partnery zachováním potřebné kontroly nad firemní data. Další informace najdete v tématu [Azure Active Directory dokumentaci B2B](../b2b/index.yml).|
|Firmy zákazníka (B2C)|Přizpůsobení a řízení způsobe registrace, přihlaste se a při používání vašich aplikací spravují své profily. Další informace najdete v [dokumentaci Azure Active Directory B2C](../../active-directory-b2c/index.yml).|
|Podmíněný přístup|Správa přístupu k vašim cloudovým aplikacím. Další informace najdete v [dokumentaci k podmíněnému přístupu v Azure AD](../conditional-access/index.yml).|
|Azure Active Directory pro vývojáře|Sestavení aplikace, které ve všech identitách Microsoftu, získat tokeny pro volání Microsoft Graphu, jiné APIs Microsoft nebo vlastních rozhraní API. Další informace najdete v tématu [Microsoft Identity Platform (Azure Active Directory pro vývojáře)](../develop/index.yml).|
|Správa zařízení|Správa přístupu k podnikovým datům na místní i cloudové zařízení. Další informace najdete v [dokumentaci správy zařízení v Azure AD](../devices/index.yml).|
|Doménové služby|Připojení virtuálních počítačů Azure k doméně bez použití řadiče domény. Další informace najdete v [dokumentaci Azure AD Domain Services](../../active-directory-domain-services/index.yml).|
|Podnikoví uživatelé|Spravovat přiřazení licencí, přístup k aplikacím a nastavit delegátů pomocí skupin a rolí správce. Další informace najdete v tématu [Azure Active Directory dokumentaci správy uživatelů](../users-groups-roles/index.yml).|
|Hybridní identita|Pomocí Azure Active Directory Connect a Connect Health k poskytnutí jednou identitou uživatele pro ověřování a autorizaci pro všechny prostředky, bez ohledu na umístění (cloudu nebo místně). Další informace najdete v tématu [dokumentace k hybridní identitě](../hybrid/index.yml).|
|Zásady správného řízení identit|Správa identity vaší organizace prostřednictvím zaměstnance, obchodním partnerům, Dodavatel, služby a řízení přístupu k aplikaci. Můžete také provádět kontroly přístupu. Další informace najdete v [dokumentaci zásad správného řízení identit Azure AD](../governance/identity-governance-overview.md) a v tématu kontroly [přístupu Azure AD](../governance/access-reviews-overview.md).|
|Ochrana identit|Zjistit potenciální ohrožení zabezpečení dopadem na identity vaší organizace, nakonfigurovat zásady reakce na podezřelé akce a poté přijmout vhodná opatření k jejich řešení. Další informace najdete v tématu [Azure AD Identity Protection](../identity-protection/index.yml).|
|Spravované identity pro prostředky Azure|Poskytuje služby Azure se automaticky spravovanou identitu ve službě Azure AD, který může ověřit libovolnou službu Azure AD nepodporuje ověřování, včetně služby Key Vault. Další informace najdete v tématu [co jsou spravované identity pro prostředky Azure?](../managed-identities-azure-resources/overview.md).|
|Privileged identity management (PIM)|Spravovat, řídit a sledovat přístup v rámci vaší organizace. Tato funkce zahrnuje přístup k prostředkům v Azure AD a Azure a dalších online službách Microsoftu, jako je Office 365 nebo Intune. Další informace najdete v tématu [Azure AD Privileged Identity Management](../privileged-identity-management/index.yml).|
|Sestavy a sledování|Získejte přehled o zabezpečení a využití vzory ve vašem prostředí. Další informace najdete v tématu [Azure Active Directory sestav a monitorování](../reports-monitoring/index.yml).|

## <a name="next-steps"></a>Další kroky

- [Zaregistrujte se Azure Active Directory Premium](active-directory-get-started-premium.md)

- [Přidružte k vašemu Azure Active Directory předplatné Azure](active-directory-how-subscriptions-associated-directory.md)

- [Přístup Azure Active Directory a vytvoření nového tenanta](active-directory-access-create-new-tenant.md)

- [Azure Active Directory Premium – kontrolní seznam nasazení funkce P2](active-directory-deployment-checklist-p2.md)
