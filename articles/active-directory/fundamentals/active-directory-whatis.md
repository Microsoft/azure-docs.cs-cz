---
title: Představení služby Azure Active Directory - Služba Azure Active Directory | Dokumenty společnosti Microsoft
description: Přehled a rámcové informace o službě Azure Active Directory, včetně terminologie, jaké licence jsou k dispozici, a seznam přidružených funkcí s odkazy na další informace.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240783"
---
# <a name="what-is-azure-active-directory"></a>Představení služby Azure Active Directory

Azure Active Directory (Azure AD) je cloudová služba správy identit a přístupu od Microsoftu, která vašim zaměstnancům pomáhá přihlašovat se a přistupovat k prostředkům v:

- Externí prostředky, jako je Microsoft Office 365, portál Azure a tisíce dalších aplikací SaaS.

- Interní prostředky, jako jsou aplikace v podnikové síti a intranetu, spolu se všemi cloudovými aplikacemi vyvinutými vaší vlastní organizací.

Pomocí různých plakátů [řady Microsoft Cloud for Enterprise Architects](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources#identity) můžete lépe porozumět základním službám identit v Azure, Azure AD a Office 365.

## <a name="who-uses-azure-ad"></a>Kdo používá Azure AD?

Azure AD je určen pro:

- **Správci IT.** Jako správce IT můžete pomocí Azure AD řídit přístup k vašim aplikacím a prostředkům aplikací na základě vašich obchodních požadavků. Azure AD můžete například použít k vyžadování vícefaktorového ověřování při přístupu k důležitým organizačním prostředkům. Kromě toho můžete pomocí Azure AD automatizovat zřizování uživatelů mezi stávající windows serverovou službou AD a cloudovými aplikacemi, včetně Office 365. Nakonec Azure AD poskytuje výkonné nástroje, které automaticky pomáhají chránit identity uživatelů a přihlašovací údaje a splňují vaše požadavky na zásady přístupu. Chcete-li začít, zaregistrujte si [bezplatnou 30denní zkušební verzi služby Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/).

- **Vývojáři aplikací.** Jako vývojář aplikace můžete azure ad používat jako přístup založený na standardech pro přidávání jednotného přihlašování (SSO) do vaší aplikace, což mu umožňuje pracovat s již existujícími přihlašovacími údaji uživatele. Azure AD také poskytuje api, která vám můžou pomoct vytvářet přizpůsobené prostředí aplikací pomocí existujících dat organizace. Chcete-li začít, zaregistrujte si [bezplatnou 30denní zkušební verzi služby Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/). Další informace najdete také [v tématu Azure Active Directory pro vývojáře](../develop/index.yml).

- **Předplatitelé Microsoft365, Office 365, Azure nebo Dynamics CRM Online.** Jako předplatitel už používáte Azure AD. Každý klient Microsoft 365, Office 365, Azure a Dynamics CRM Online je automaticky klientazure ad. Můžete okamžitě začít spravovat přístup k integrovaným cloudovým aplikacím.

## <a name="what-are-the-azure-ad-licenses"></a>Co jsou licence Azure AD?

Obchodní služby Microsoft Online, jako je Office 365 nebo Microsoft Azure, vyžadují Azure AD pro přihlášení a pomoc s ochranou identity. Pokud se přihlásíte k odběru jakékoli obchodní služby Microsoft Online, automaticky získáte Azure AD s přístupem ke všem bezplatným funkcím.

Chcete-li vylepšit implementaci Azure AD, můžete také přidat placené funkce upgradem na licence Azure Active Directory Premium P1 nebo Premium P2. Placené licence Azure AD jsou postavené na vašem stávajícím bezplatném adresáři a poskytují samoobslužné, vylepšené monitorování, vytváření sestav zabezpečení a zabezpečený přístup pro mobilní uživatele.

>[!Note]
>Cenové možnosti těchto licencí najdete v tématu [Azure Active Directory Pricing](https://azure.microsoft.com/pricing/details/active-directory/).
>
>Azure Active Directory Premium P1 a Premium P2 nejsou aktuálně podporované v Číně. Další informace o cenách Azure AD získáte na [fóru Azure Active Directory .](https://azure.microsoft.com/support/community/?product=active-directory)

- **Azure Active Directory zdarma.** Poskytuje správu uživatelů a skupin, místní synchronizaci adresářů, základní sestavy, samoobslužnou změnu hesla pro uživatele v cloudu a jednotné přihlašování v azure, Office 365 a mnoha populárních aplikacích SaaS.

- **Azure Active Directory Premium P1.** Kromě bezplatných funkcí umožňuje P1 také hybridním uživatelům přístup k místním i cloudovým prostředkům. Podporuje také pokročilou správu, jako jsou dynamické skupiny, samoobslužná správa skupin, Microsoft Identity Manager (místní sada pro správu identit a přístupu) a funkce pro zpětné prosnížení hodnoty v cloudu, které umožňují samoobslužné resetování hesla pro místní uživatele.

- **Azure Active Directory Premium P2.** Kromě bezplatných funkcí a funkcí P1 nabízí P2 také [azure služby Azure Active Directory Identity Protection,](../identity-protection/overview-identity-protection.md) která pomáhá poskytovat podmíněné mušce založené na rizicích vašim aplikacím a důležitým firemním datům a [privilegovanou správu identit,](../privileged-identity-management/pim-getting-started.md) která pomáhá odjišťovat, omezovat a monitorovat správce a jejich přístup k prostředkům a v případě potřeby poskytovat přístup za čas.

- **Licence funkcí "Průběžně plaťte".** Můžete také získat další licence na funkce, jako je azure Active Directory Business-to-Customer (B2C). B2C vám může pomoci poskytovat řešení pro správu identit a přístupu pro vaše aplikace orientované na zákazníky. Další informace naleznete v [dokumentaci k službě Azure Active Directory B2C](../../active-directory-b2c/index.yml).

Další informace o přidružení předplatného Azure ke službě Azure AD najdete v tématu [Postup: Přidružení nebo přidání předplatného Azure do služby Azure Active Directory](active-directory-how-subscriptions-associated-directory.md) a další informace o přiřazování licencí uživatelům najdete v [tématu Postup: Přiřazení nebo odebrání licencí Azure Active Directory](license-users-groups.md).

## <a name="terminology"></a>Terminologie

Chcete-li lépe porozumět Azure AD a jeho dokumentaci, doporučujeme zkontrolovat následující podmínky.

|Termín nebo koncept|Popis|
|---------------|-----------|
|Identita| Věc, která se může ověřit. Identitou může být uživatel s uživatelským jménem a heslem. Identity také zahrnují aplikace nebo jiné servery, které mohou vyžadovat ověření prostřednictvím tajných klíčů nebo certifikátů.|
|Účet| Identita, která má data s ní spojené. Nemůžete mít účet bez identity.|
|Účet Azure AD| Identita vytvořená prostřednictvím Azure AD nebo jiné cloudové služby Microsoftu, jako je Office 365. Identity jsou uložené ve službě Azure AD a přístupné pro předplatná cloudových služeb vaší organizace. Tento účet se také někdy nazývá pracovní nebo školní účet.|
|Předplatné Azure| Používá se k platbě za cloudové služby Azure. Můžete mít mnoho předplatných a jsou propojeny s platební kartou.|
|Tenant Azure| Vyhrazená a důvěryhodná instance Azure AD, která se automaticky vytvoří, když se vaše organizace zaregistruje k předplatnému cloudových služeb Microsoftu, jako je Microsoft Azure, Microsoft Intune nebo Office 365. Tenant Azure představuje jednu organizaci.|
|Jeden tenant| Tenanti Azure, kteří přistupují k jiným službám ve vyhrazeném prostředí, jsou považováni za jednoho klienta.|
|Víc klientů| Tenanti Azure, kteří přistupují k jiným službám ve sdíleném prostředí, napříč více organizacemi, jsou považováni za víceklientské.|
|Adresář Azure AD|Každý tenant Azure má vyhrazený a důvěryhodný adresář Azure AD. Adresář Azure AD zahrnuje uživatele klienta, skupiny a aplikace a používá se k provádění funkcí správy identit a přístupu pro prostředky klienta.|
|Vlastní doména|Každý nový adresář Azure AD je dodáván s názvem počáteční domény, domainname.onmicrosoft.com. Kromě tohoto počátečního názvu můžete do seznamu přidat také názvy domén vaší organizace, které zahrnují názvy, které používáte k podnikání a uživatelé pro přístup k prostředkům vaší organizace. Přidání vlastních názvů domén vám pomůže vytvořit uživatelská jména, která jsou uživatelům známá, například alain@contoso.com.|
|Správce účtu|Tato klasická role správce předplatného je koncepčně vlastníkem fakturace předplatného. Tato role má přístup k [Centru účtů Azure](https://account.azure.com/Subscriptions) a umožňuje spravovat všechna předplatná v účtu. Další informace najdete [v tématu Klasické role správce předplatného, role řízení přístupu na základě rolí Azure (RBAC) a role správce Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Správce služeb|Tato klasická role správce předplatného umožňuje spravovat všechny prostředky Azure, včetně přístupu. Tato role má ekvivalentní přístup uživatele, kterému je přiřazena role vlastníka v oboru předplatného. Další informace najdete [v tématu Klasické role správce předplatného, role Azure RBAC a role správce Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Vlastník|Tato role vám pomůže spravovat všechny prostředky Azure, včetně přístupu. Tato role je postavena na novějším autorizačním systému nazývaném řízení přístupu na základě základny rolí (RBAC), který poskytuje jemně odstupňovanou správu přístupu k prostředkům Azure. Další informace najdete [v tématu Klasické role správce předplatného, role Azure RBAC a role správce Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Globální správce Azure AD|Tato role správce se automaticky přiřadí tomu, kdo vytvořil klienta Azure AD. Globální správci můžou dělat všechny funkce správy pro Azure AD a všechny služby, které federate do Azure AD, jako je Exchange Online, SharePoint Online a Skype pro firmy Online. Můžete mít více globálních správců, ale pouze globální správci mohou přiřadit role správce (včetně přiřazení dalších globálních správců) uživatelům.<br><br>**Poznámka**<br>Tato role správce se nazývá globální správce na webu Azure Portal, ale nazývá se **správce společnosti** v rozhraní Microsoft Graph API a Azure AD PowerShell.<br><br>Další informace o různých rolích správce najdete [v tématu Oprávnění rolí správce ve službě Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).|
|Účet Microsoft (také nazývaný, MSA)|Osobní účty, které poskytují přístup k vašim produktům a cloudovým službám Microsoftu orientovaným na spotřebitele, jako je Outlook, OneDrive, Xbox LIVE nebo Office 365. Váš účet Microsoft je vytvořen a uložen v systému účtů identit y microsoftu, který provozuje microsoft.|

## <a name="which-features-work-in-azure-ad"></a>Které funkce fungují ve službě Azure AD?

Po výběru licence Azure AD získáte přístup k některým nebo všem následujícím funkcím pro vaši organizaci:

|Kategorie|Popis|
|-------|-----------|
|Správa aplikací|Spravujte své cloudové a místní aplikace pomocí proxy aplikace, jednotného přihlášení, portálu Moje aplikace (označovaného také jako panel Access) a aplikací Software jako služba (SaaS). Další informace naleznete v tématu [Jak zajistit zabezpečený vzdálený přístup k místním aplikacím](../manage-apps/application-proxy.md) a [dokumentaci ke správě aplikací](../manage-apps/index.yml).|
|Ověřování|Spravujte samoobslužné resetování hesla služby Azure Active Directory, vícefaktorové ověřování, vlastní seznam zakázaných hesel a inteligentní uzamčení. Další informace naleznete v [dokumentaci k ověřování azure ad](../authentication/index.yml).|
|B2B (Business-to-Business)|Spravujte své uživatele typu Host a externí partnery a zároveň si zachovejte kontrolu nad vlastními podnikovými daty. Další informace naleznete v [dokumentaci k službě Azure Active Directory B2B](../b2b/index.yml).|
|B2C (Business-to-Customer)|Přizpůsobte si a řiďte, jak se uživatelé při používání aplikací přihlašují, přihlašují a spravují své profily. Další informace naleznete v [dokumentaci k službě Azure Active Directory B2C](../../active-directory-b2c/index.yml).|
|Podmíněný přístup|Spravujte přístup ke svým cloudovým aplikacím. Další informace naleznete v [dokumentaci k podmíněnému přístupu Azure AD](../conditional-access/index.yml).|
|Azure Active Directory pro vývojáře|Vytvářejte aplikace, které přihlašují všechny identity Microsoftu, získají tokeny pro volání Microsoft Graphu, jiných rozhraní API Microsoftu nebo vlastních rozhraní API. Další informace naleznete [v tématu Platforma identit Microsoftu (Azure Active Directory pro vývojáře)](../develop/index.yml).|
|Správa zařízení|Spravuje, jak cloudová nebo místní zařízení přistupují k vašim firemním datům. Další informace naleznete v [dokumentaci ke správě zařízení Azure AD](../devices/index.yml).|
|Doménové služby|Připojte virtuální počítače Azure k doméně bez použití řadičů domény. Další informace naleznete v [dokumentaci k službě Azure AD Domain Services](../../active-directory-domain-services/index.yml).|
|Podnikoví uživatelé|Spravujte přiřazení licencí, získejte přístup k aplikacím a nastavte delegáty pomocí skupin a rolí správců. Další informace naleznete v [dokumentaci ke správě uživatelů služby Azure Active Directory](../users-groups-roles/index.yml).|
|Hybridní identita|Azure Active Directory Connect and Connect Health můžete zajistit identitu jednoho uživatele pro ověřování a autorizaci pro všechny prostředky bez ohledu na umístění (cloud nebo místní). Další informace naleznete [v dokumentaci k hybridní identitě](../hybrid/index.yml).|
|Zásady správného řízení identit|Spravujte identitu vaší organizace prostřednictvím ovládacích prvků pro přístup zaměstnanců, obchodních partnerů, dodavatelů, služeb a aplikací. Můžete také provádět kontroly přístupu. Další informace najdete [v tématu dokumentace zásad správného řízení identit Azure AD](../governance/identity-governance-overview.md) a [kontroly přístupu KAD Azure .](../governance/access-reviews-overview.md)|
|Ochrana identit|Zjistěte potenciální chyby zabezpečení ovlivňující identitu vaší organizace, nakonfigurujte zásady tak, aby reagovaly na podezřelé akce, a pak proveďte příslušná opatření k jejich vyřešení. Další informace naleznete v [tématu Azure AD Identity Protection](../identity-protection/index.yml).|
|Spravované identity pro prostředky Azure|Poskytuje vašim službám Azure automaticky spravovanou identitu ve službě Azure AD, která může ověřovat libovolnou ověřovací službu podporovanou službou Azure AD, včetně trezoru klíčů. Další informace najdete v tématu [Co je spravované identity pro prostředky Azure?](../managed-identities-azure-resources/overview.md).|
|Privileged Identity Management (PIM)|Spravujte, kontrolujte a monitorujte přístup v rámci organizace. Tato funkce zahrnuje přístup k prostředkům ve službě Azure AD a Azure a dalším službám Microsoft Online Services, jako je Office 365 nebo Intune. Další informace naleznete v [tématu Azure AD Privileged Identity Management](../privileged-identity-management/index.yml).|
|Sestavy a sledování|Získejte přehled o vzorcích zabezpečení a používání ve vašem prostředí. Další informace naleznete v [tématu Sestavy a monitorování služby Azure Active Directory](../reports-monitoring/index.yml).|

## <a name="next-steps"></a>Další kroky

- [Registrace služby Azure Active Directory Premium](active-directory-get-started-premium.md)

- [Přidružení předplatného Azure ke službě Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)

- [Přístup k Azure Active Directory a vytvoření nového klienta](active-directory-access-create-new-tenant.md)

- [Kontrolní seznam funkcí služby Azure Active Directory Premium P2](active-directory-deployment-checklist-p2.md)
