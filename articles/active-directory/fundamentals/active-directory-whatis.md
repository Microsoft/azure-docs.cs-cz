---
title: Představení služby Azure Active Directory | Dokumenty Microsoft
description: Přečtěte si o Azure Active Directory, včetně požadované terminologie cílovou skupinu, licencování základy a související funkce.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.topic: overview
ms.date: 11/13/2018
ms.author: lizross
ms.custom: it-pro, seodec18
ms.openlocfilehash: 80c3108d468465e68d554b3d36c527c5ccd4a13c
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53085922"
---
# <a name="what-is-azure-active-directory"></a>Představení služby Azure Active Directory 
Azure Active Directory (Azure AD) je společnosti Microsoft cloudových identit a přístupu služba pro správu. Azure AD pomáhá zaměstnancům přihlásit a získat přístup k prostředkům v:

- Externí prostředky, jako je například Microsoft Office 365, na webu Azure portal a tisícům dalších aplikací SaaS.

- Interním prostředkům, jako jsou například aplikace v podnikové síti a intranetu, spolu s všechny cloudové aplikace vyvinuté ve vaší vlastní organizaci.

Můžete použít různé [Microsoft Cloud for Enterprise Architects řady](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources#identity) plakáty pro lepší pochopení základních služeb identity v Azure, Azure AD a Office 365.

## <a name="who-uses-azure-ad"></a>Kdo používá Azure AD?
Azure AD je určený pro:

- **Správci IT.** Jako správce IT můžete použít Azure AD pro řízení přístupu pro vaše aplikace a prostředky aplikace založené na vašich obchodních požadavcích. Například můžete použít Azure AD tak, aby vyžadovala vícefaktorové ověřování při přístupu k důležitým prostředkům organizace. Kromě toho můžete použít Azure AD k automatizaci zřizování uživatelů mezi stávající Windows Server AD a cloudovým aplikacím, včetně Office 365. A konečně Azure AD poskytuje výkonné nástroje k automatické ochraně identit uživatelů a přihlašovacích údajů a aby splňovaly vaše požadavky zásad správného řízení přístupu. Abyste mohli začít, zaregistrujte si [bezplatné 30denní zkušební verze Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/).

- **Vývojáři aplikací.** Jako vývojář aplikací Azure AD vám přístup založené na standardech pro přidání jednotného přihlašování (SSO) do vaší aplikace, díky kterému jej pro práci s již existujících přihlašovacích údajů uživatele. Azure AD také poskytuje rozhraní API, které vám pomůžou začít vytvářet přizpůsobené aplikace prostředí využívat existující data organizace. Abyste mohli začít, zaregistrujte si [bezplatné 30denní zkušební verze Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/). Další informace, můžete také zobrazit [Azure Active Directory pro vývojáře](../develop/index.yml).

- **Odběratelé, kteří Microsoft 365, Office 365, Azure nebo Dynamics CRM Online.** Jako předplatitel které už používáte Azure AD. Každý tenant Microsoft 365, Office 365, Azure a Dynamics CRM Online je automaticky tenanta služby Azure AD. Můžete okamžitě začít spravovat přístup k integrované cloudové aplikace.

## <a name="what-are-the-azure-ad-licenses"></a>Co jsou licence Azure AD?
Firemní služby Microsoft Online, jako je Office 365 nebo Microsoft Azure vyžadují služby Azure AD pro přihlášení a které vám pomůžou s identity protection. Proto pokud se přihlásíte k odběru všechny firemní služby Microsoft Online, automaticky získáte Azure AD s přístupem ke všem funkcím zdarma.

Pokud chcete zlepšit vaše implementace služby Azure AD, můžete také přidat placené funkce díky upgradu na licence Azure Active Directory Basic, Premium P1 nebo P2. Placené licence služby Azure AD jsou postavené na existující adresář zdarma, poskytování samoobslužných služeb, rozšířené monitorování, vytváření sestav zabezpečení a bezpečného přístupu pro mobilní pracovní síly.

>[!Note]
>Cenové možnosti z těchto licencí najdete v tématu [cenami Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).<br><br>Azure Active Directory Premium P1, Premium P2 a Azure Active Directory Basic se aktuálně nepodporují v Číně. Další informace o cenách služby Azure AD, můžete kontaktovat [fóru služby Azure Active Directory](https://azure.microsoft.com/support/community/?product=active-directory).

- **Azure Active Directory Free.** Poskytuje správu uživatelů a skupin, synchronizace místních adresářů, základní sestavy a jednotné přihlašování v Azure, Office 365 a mnoho oblíbených aplikací SaaS.

- **Azure Active Directory Basic.** Kromě bezplatných funkcí Basic poskytuje také přístup k aplikaci zaměřené na cloud, řízení přístupu na základě skupin, samoobslužné resetování hesla pro cloudové aplikace a Azure AD Application Proxy, který umožňuje publikování místních webových aplikací pomocí služby Azure AD.

- **Azure Active Directory Premium P1.** Kromě Free a základními funkcemi P1 také umožňuje hybridním uživatelům, přístup k místním a cloudovým prostředkům. Podporuje také pokročilou správu, jako je například dynamických skupin, samoobslužné správy skupin, Microsoft Identity Manageru (místních identit a přístupu správy sady) a možností cloudu u zpětný zápis, které umožňují samoobslužné resetování hesla pro Místní uživatelé.

- **Azure Active Directory Premium P2.** Kromě funkcí Free, Basic a P1, P2 také nabízí [Azure Active Directory Identity Protection](../identity-protection/enable.md) pomáhají na základě rizik podmíněného přístupu pro vaše aplikace a důležitými firemními daty a [Privileged Identity Správa](../privileged-identity-management/pim-getting-started.md) ke zjišťování, omezte a monitorujte správce a jejich přístup k prostředkům a k poskytování just-in-time v případě potřeby přístup.

- **Licence funkce "Platby".** Můžete také získat licence další funkce, jako je Azure Active Directory Business na zákazníka (B2C). B2C můžete poskytovat identitu a přístup k řešení pro správu vašich aplikací určených pro zákazníky. Další informace najdete v tématu [dokumentace ke službě Azure Active Directory B2C](../../active-directory-b2c/index.yml).

Další informace o přiřazení předplatného Azure do služby Azure AD najdete v tématu [postupy: spojení nebo přidání předplatného Azure ke službě Azure Active Directory](active-directory-how-subscriptions-associated-directory.md) a další informace o přiřazování licencí uživatelům, naleznete v tématu [Postupy: přiřazení nebo odebrání licence Azure Active Directory](license-users-groups.md).

## <a name="terminology"></a>Terminologie
Abyste lépe pochopili, Azure AD a jeho dokumentace, měli byste si přečíst následující podmínky.

|Ukončení nebo koncept|Popis|
|---------------|-----------|
|Předplatné Azure| Použité k úhradě služby Azure cloud services. Máte více předplatných a jsou propojeny na platební kartu.|
|Tenant Azure| Vyhrazené a důvěryhodné instance služby Azure AD, který se automaticky vytvoří, pokud vaše organizace zaregistruje předplatné služby cloud Microsoftu, jako je například Microsoft Azure, Microsoft Intune nebo Office 365. Klient služby Azure představuje jednu organizaci.|
|Jeden tenant| Klienty Azure, které přístupu k jiným službám ve vyhrazeném prostředí jsou považovány za jednoho tenanta.|
|Víc klientů| Klienty Azure, které v několika organizacích, přístupu k jiným službám ve sdíleném prostředí, jsou považovány za více tenantů.|
|Adresář Azure AD|Vyhrazené a důvěryhodné Azure má každý tenant Azure AD directory. Adresář Azure AD obsahuje uživatele, skupiny nebo aplikace vašeho tenanta a slouží k provádění identita a přístup k funkcím správy prostředků tenanta.|
|Účet Azure AD | Identita vytvořené pomocí Azure AD nebo jinou cloudovou službu Microsoftu, jako je Office 365. Identity jsou uložené ve službě Azure AD a jejich zpřístupnění předplaceným cloudovým službám vaší organizace. Tento účet se také někdy označuje jako pracovní nebo školní účet.|
|Vlastní doména|Každý nový Azure AD directory je k dispozici s počáteční název domény, domainname.onmicrosoft.com. Kromě toho počáteční název, můžete také přidat názvy, které zahrnují názvy, které používáte pro firmy a vaši uživatelé používat pro přístup k prostředkům ve vaší organizaci, do seznamu domény vaší organizace. Přidání vlastních názvů domén vám pomůže vytvořit uživatelská jména, které jsou vaši uživatelé znají, jako je například alain@contoso.com.|
|Správce účtu|Tato role správce v klasickém modelu předplatného se koncepčně fakturační vlastník předplatného. Tato role má přístup k [centra účtů Azure](https://account.azure.com/Subscriptions) a umožňuje spravovat všechny odběry v rámci účtu. Další informace najdete v tématu [Classic role správců předplatného, role Azure RBAC a rolích správce Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Správce služeb|Tato role správce v klasickém modelu předplatného umožňuje spravovat všechny prostředky Azure, včetně přístupu. Tato role má ekvivalentní přístupu uživatele, který má přiřazenou roli vlastník v oboru předplatného. Další informace najdete v tématu [Classic role správců předplatného, role Azure RBAC a rolích správce Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Vlastník|Tato role umožňuje spravovat všechny prostředky Azure, včetně přístupu. Tato role je založená na novější systém autorizace nazývá řízení přístupu na základ rolí (RBAC), který poskytuje propracovanou správu přístupu k prostředkům Azure. Další informace najdete v tématu [Classic role správců předplatného, role Azure RBAC a rolích správce Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Globální správce Azure AD|Tato role správce je automaticky přiřazené pro všechny uživatele, kteří vytvořili tenanta Azure AD. Globální správci mohou provádět všechny funkce správy pro službu Azure AD a služeb, které vytvořit federaci s Azure AD, jako je Exchange Online, SharePoint Online a Skype for Business Online. Můžete mít víc globálních správců, ale jenom globální správci (včetně přiřazování další globální správce) role správců můžou přiřazovat uživatelům.<br><br>**Poznámka**<br>Tato role správce se nazývá globální správce na webu Azure Portal, ale je volána **správce společnosti** v rozhraní Microsoft Graph API, Azure AD Graph API a Azure AD PowerShell.<br><br>Další informace o různých rolích správce najdete v tématu [oprávnění role správce v Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).|
|Účet Microsoft (také nazývané, MSA)|Osobní účty, které poskytují přístup k vaší spotřebitelské produkty společnosti Microsoft a cloudovým službám, jako je Outlook, OneDrive, Xbox LIVE nebo Office 365. Váš účet Microsoft se vytvoří a uloží v Microsoft systémem identit uživatelů účtu, který se spouští společností Microsoft.|

## <a name="what-features-work-in-azure-ad"></a>Jaké funkce fungují ve službě Azure AD?
Po zvolení licenci Azure AD je ve vaší organizaci získají přístup k některé nebo všechny z následujících funkcí:

|Kategorie|Popis|
|-------|-----------|
|Správa aplikací|Správa cloudových a místních aplikací pomocí Proxy aplikací, jednotného přihlašování portálu Moje aplikace (označované také jako na přístupovém panelu) a Software jako služba (SaaS) aplikací. Další informace najdete v tématu [jak poskytnout zabezpečený vzdálený přístup k místním aplikacím](../manage-apps/application-proxy.md) a [dokumentace ke službě Application Management](../manage-apps/index.yml).|
|Authentication|Správa Azure Active Directory, samoobslužné resetování hesla, ověření službou Multi-Factor Authentication, seznam vlastních zakázaných hesel a inteligentním uzamčením. Další informace najdete v tématu [dokumentace ke službě Azure AD Authentication](../authentication/index.yml).|
|Business-to-Business (B2B)|Spravujte uživatele typu Host a externími partnery zachováním potřebné kontroly nad firemní data. Další informace najdete v tématu [dokumentace ke službě Azure Active Directory s B2B](../b2b/index.yml).|
|Firmy zákazníka (B2C)|Přizpůsobení a řízení způsobe registrace, přihlaste se a při používání vašich aplikací spravují své profily. Další informace najdete v tématu [dokumentace ke službě Azure Active Directory B2C](../../active-directory-b2c/index.yml).|
|Podmíněný přístup|Správa přístupu k vašim cloudovým aplikacím. Další informace najdete v tématu [dokumentace podmíněného přístupu Azure AD](../conditional-access/index.yml).|
|Azure Active Directory pro vývojáře|Sestavení aplikace, které ve všech identitách Microsoftu, získat tokeny pro volání Microsoft Graphu, jiné APIs Microsoft nebo vlastních rozhraní API. Další informace najdete v tématu [platforma identit Microsoft (Azure Active Directory pro vývojáře)](../develop/index.yml).|
|Správa zařízení|Správa přístupu k podnikovým datům na místní i cloudové zařízení. Další informace najdete v tématu [dokumentace ke službě Azure AD Device Management](../devices/index.yml).|
|Doménové služby|Připojení virtuálních počítačů Azure k doméně bez použití řadiče domény. Další informace najdete v tématu [dokumentace ke službě Azure AD Domain Services](../../active-directory-domain-services/index.yml).|
|Podnikoví uživatelé|Spravovat přiřazení licencí, přístup k aplikacím a nastavit delegátů pomocí skupin a rolí správce. Další informace najdete v tématu [dokumentace ke službě Azure Active Directory uživatele management](../users-groups-roles/index.yml).|
|Hybridní identita|Pomocí Azure Active Directory Connect a Connect Health k poskytnutí jednou identitou uživatele pro ověřování a autorizaci pro všechny prostředky, bez ohledu na umístění (cloudu nebo místně). Další informace najdete v tématu [dokumentace ke službě identity hybridní](../hybrid/index.yml).|
|Zásady správného řízení identit|Správa identity vaší organizace prostřednictvím zaměstnance, obchodním partnerům, Dodavatel, služby a řízení přístupu k aplikaci. Můžete také provádět kontroly přístupu. Další informace najdete v tématu [dokumentace ke službě Azure AD identity zásad správného řízení](../governance/identity-governance-overview.md) a [kontroly přístupu Azure AD](../governance/access-reviews-overview.md).|
|Ochrana identit|Zjistit potenciální ohrožení zabezpečení dopadem na identity vaší organizace, nakonfigurovat zásady reakce na podezřelé akce a poté přijmout vhodná opatření k jejich řešení. Další informace najdete v tématu [Azure AD Identity Protection](../identity-protection/index.yml).|
|Spravované identity pro prostředky Azure|Poskytuje služby Azure se automaticky spravovanou identitu ve službě Azure AD, který může ověřit libovolnou službu Azure AD nepodporuje ověřování, včetně služby Key Vault. Další informace najdete v tématu [co je spravované identity pro prostředky Azure?](../managed-identities-azure-resources/overview.md).|
|Privileged identity management (PIM)|Spravovat, řídit a sledovat přístup v rámci vaší organizace. Tato funkce zahrnuje přístup k prostředkům v Azure AD, prostředky Azure a dalších Microsoft Online Services, jako je Office 365 nebo Intune. Další informace najdete v tématu [Azure AD Privileged Identity Management](../privileged-identity-management/index.yml).|
|Sestavy a sledování|Získejte přehled o zabezpečení a využití vzory ve vašem prostředí. Další informace najdete v tématu [sestav Azure Active Directory a monitorování](../reports-monitoring/index.yml).|


## <a name="next-steps"></a>Další postup
- [Registrace pro Azure Active Directory Premium](active-directory-get-started-premium.md)

- [Přidružte předplatné služby Azure do Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)

- [Přístup k Azure Active Directory a vytvořit nového tenanta](active-directory-access-create-new-tenant.md)

- [Kontrolní seznam nasazení funkce Azure Active Directory Premium P2](active-directory-deployment-checklist-p2.md)
