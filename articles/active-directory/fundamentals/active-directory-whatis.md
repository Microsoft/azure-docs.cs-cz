---
title: Co je Azure Active Directory? -Azure Active Directory | Microsoft Docs
description: Přehled a koncepční informace o Azure Active Directory, včetně terminologie, jaké licence jsou k dispozici, a seznam přidružených funkcí s odkazy pro další informace.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: overview
ms.date: 06/05/2020
ms.author: ajburnle
ms.custom: it-pro, seodec18, seo-update-azuread-jan, contperf-fy20q4
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9e85960f3efc074eca18475c4a3e6c422c1990b
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090837"
---
# <a name="what-is-azure-active-directory"></a>Co je Azure Active Directory?

Azure Active Directory (Azure AD) je cloudová služba pro správu identit a přístupu od Microsoftu, která pomáhá vašim zaměstnancům přihlašovat se a přistupovat k prostředkům v nástroji:

- Externí prostředky, například Microsoft 365, Azure Portal a tisíce dalších aplikací SaaS.

- Interní prostředky, jako jsou aplikace ve vaší podnikové síti nebo intranetu, spolu s jakýmikoli cloudovou aplikací vyvinutou vaší organizací. Další informace o vytvoření tenanta pro vaši organizaci najdete v tématu [rychlý Start: vytvoření nového tenanta v Azure Active Directory](active-directory-access-create-new-tenant.md).

Pokud se chcete dozvědět rozdíl mezi Azure AD a Active Directory Domain Services, přečtěte si téma [porovnání služby Active Directory s Azure Active Directory](active-directory-compare-azure-ad-to-ad.md). K lepšímu pochopení základních služeb identit v Azure, Azure AD a Microsoft 365 můžete použít i různé [Microsoft Cloud pro plakáty řady Enterprise Architects](/microsoft-365/solutions/cloud-architecture-models) .

## <a name="who-uses-azure-ad"></a>Kdo používá Azure AD?

Služba Azure AD je určena pro:

- **Správci IT.** Jako správce IT můžete pomocí Azure AD řídit přístup k vašim aplikacím a prostředkům vaší aplikace, a to na základě vašich obchodních požadavků. Službu Azure AD můžete například použít k vyžádání vícefaktorového ověřování při přístupu k důležitým prostředkům organizace. Kromě toho můžete pomocí Azure AD automatizovat zřizování uživatelů mezi stávajícími službami Windows Server AD a vašimi cloudových aplikací, včetně Microsoft 365. Azure AD vám nakonec poskytuje výkonné nástroje, které automaticky pomůžou chránit identity a přihlašovací údaje uživatelů a splňovat požadavky na řízení přístupu. Pokud chcete začít, zaregistrujte si [bezplatnou 30denní zkušební verzi Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/).

- **Vývojáři aplikací** Jako vývojář aplikace můžete použít Azure AD jako přístup založený na standardech pro přidání jednotného přihlašování (SSO) do vaší aplikace a tím umožníte, aby fungoval s předem existujícími přihlašovacími údaji uživatele. Azure AD taky poskytuje rozhraní API, která vám pomůžou sestavovat přizpůsobená prostředí aplikací pomocí stávajících dat organizace. Pokud chcete začít, zaregistrujte si [bezplatnou 30denní zkušební verzi Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/). Další informace najdete také v tématu [Azure Active Directory pro vývojáře](../develop/index.yml).

- **Microsoft 365, předplatitelé Office 365, Azure nebo Dynamics CRM Online.** Jako předplatitel už službu Azure AD používáte. Každý klient Microsoft 365, Office 365, Azure nebo Dynamics CRM Online je automaticky klientem Azure AD. Můžete hned začít spravovat přístup k vašim integrovaným cloudovým aplikacím.

## <a name="what-are-the-azure-ad-licenses"></a>Jaké jsou licence Azure AD?

Služby Microsoft Online Business Services, například Microsoft 365 nebo Microsoft Azure, vyžadují pro přihlášení službu Azure AD a k usnadnění ochrany identity. Pokud se přihlásíte k odběru jakékoli obchodní služby Microsoft Online, automaticky získáte Azure AD s přístupem ke všem bezplatným funkcím.

Pokud chcete zlepšit implementaci Azure AD, můžete také přidat placené možnosti tím, že upgradujete na licence Azure Active Directory Premium P1 nebo Premium P2. Placené licence Azure AD jsou postavené na svém stávajícím volném adresáři, který poskytuje samoobslužné a rozšířené monitorování, generování sestav zabezpečení a zabezpečený přístup pro mobilní uživatele.

>[!Note]
>Cenové možnosti těchto licencí najdete v tématu [Azure Active Directory ceny](https://azure.microsoft.com/pricing/details/active-directory/).
>
>Azure Active Directory Premium P1 a Premium P2 se v Číně aktuálně nepodporují. Pokud chcete získat další informace o cenách služby Azure AD, obraťte se na [fórum Azure Active Directory](https://azure.microsoft.com/support/community/?product=active-directory).

- **Azure Active Directory Free.** Poskytuje správu uživatelů a skupin, místní synchronizaci adresářů, základní sestavy, samoobslužnou změnu hesla pro cloudové uživatele a jednotné přihlašování v Azure, Microsoft 365 a spoustě oblíbených aplikací SaaS.

- **Azure Active Directory Premium P1.** Kromě bezplatných funkcí umožňuje zařízení P1 také vašim hybridním uživatelům přístup k místním i cloudovým prostředkům. Podporuje taky pokročilou správu, jako jsou dynamické skupiny, Samoobslužná správa skupin, Microsoft Identity Manager (místní sada pro správu identit a přístupu) a možnosti zpětného zápisu do cloudu, které umožňují Samoobslužné resetování hesla pro místní uživatele.

- **Azure Active Directory Premium P2.** Kromě funkcí Free a P1 nabízí P2 také [Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md) , které vám pomůžou zajistit podmíněný přístup na základě rizik pro vaše aplikace a kritická podniková data a [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md) , aby mohli zjišťovat, omezovat a monitorovat správce a jejich přístup k prostředkům a v případě potřeby poskytovat přístup za běhu.

- **Licence k funkcím průběžné platby.** Můžete také získat další licence k funkcím, jako je například Azure Active Directory B2C (Business-to-Custom). B2C vám může pomáhat zajistit řešení pro správu identit a přístupu pro vaše zákaznické aplikace. Další informace najdete v [dokumentaci Azure Active Directory B2C](../../active-directory-b2c/index.yml).

Další informace o tom, jak přidružit předplatné Azure ke službě Azure AD, najdete v tématu [přidružení nebo přidání předplatného Azure k Azure Active Directory](active-directory-how-subscriptions-associated-directory.md) a další informace o přiřazování licencí vašim uživatelům najdete v tématu [Postup: přiřazení nebo odebrání licencí Azure Active Directory](license-users-groups.md).

## <a name="which-features-work-in-azure-ad"></a>Které funkce fungují ve službě Azure AD?

Po výběru licence Azure AD získáte přístup k některým nebo všem následujícím funkcím vaší organizace:

|Kategorie|Popis|
|-------|-----------|
|Správa aplikací|Spravujte své cloudové a místní aplikace pomocí proxy aplikací, jednotného přihlašování, portálu moje aplikace (označovaného také jako přístupový panel) a aplikací SaaS (software jako služba). Další informace najdete v tématu [jak poskytnout zabezpečený vzdálený přístup k místním aplikacím](../manage-apps/application-proxy.md) a [dokumentaci správy aplikací](../manage-apps/index.yml).|
|Authentication|Spravujte Azure Active Directory Samoobslužné resetování hesla, Multi-Factor Authentication, vlastní seznam zakázaných hesel a inteligentní uzamčení. Další informace najdete v [dokumentaci k ověřování Azure AD](../authentication/index.yml).|
|Azure Active Directory pro vývojáře|Vytvářejte aplikace, které přihlásí všechny identity Microsoftu, získávají tokeny pro volání Microsoft Graph, dalších rozhraní API Microsoftu nebo vlastních rozhraní API. Další informace najdete v tématu [Microsoft Identity Platform (Azure Active Directory pro vývojáře)](../develop/index.yml).|
|B2B (Business-to-Business)|Spravujte uživatele typu Host a externí partnery a udržujte si kontrolu nad vašimi podnikovými daty. Další informace najdete v tématu [Azure Active Directory dokumentaci B2B](../external-identities/index.yml).|
|B2C (Business-to-Customer)|Přizpůsobte a určete, jak se uživatelé při používání vašich aplikací přihlásí, přihlásí a spravují své profily. Další informace najdete v [dokumentaci Azure Active Directory B2C](../../active-directory-b2c/index.yml).|
|Podmíněný přístup|Spravujte přístup k vašim cloudovým aplikacím. Další informace najdete v [dokumentaci k podmíněnému přístupu v Azure AD](../conditional-access/index.yml).|
|Správa zařízení|Umožňuje spravovat, jak mají cloudová nebo místní zařízení přístup k vašim firemním datům. Další informace najdete v [dokumentaci správy zařízení v Azure AD](../devices/index.yml).|
|Doménové služby|Připojte virtuální počítače Azure k doméně bez použití řadičů domény. Další informace najdete v [dokumentaci Azure AD Domain Services](../../active-directory-domain-services/index.yml).|
|Podnikoví uživatelé|Spravujte přiřazení licencí, přístup k aplikacím a nastavte delegáty pomocí skupin a rolí správce. Další informace najdete v tématu [Azure Active Directory dokumentaci správy uživatelů](../enterprise-users/index.yml).|
|Hybridní identita|Použijte Azure Active Directory Connect a připojte stav a poskytněte jednu identitu uživatele pro ověřování a autorizaci pro všechny prostředky bez ohledu na umístění (Cloud nebo místní). Další informace najdete v tématu [dokumentace k hybridní identitě](../hybrid/index.yml).|
|Zásady správného řízení identit|Spravujte identitu vaší organizace prostřednictvím řízení přístupu zaměstnanců, obchodních partnerů, dodavatelů, služeb a aplikací. Můžete také provádět kontroly přístupu. Další informace najdete v [dokumentaci zásad správného řízení identit Azure AD](../governance/identity-governance-overview.md) a v tématu kontroly [přístupu Azure AD](../governance/access-reviews-overview.md).|
|Ochrana identit|Detekuje potenciální ohrožení zabezpečení, která mají vliv na identity vaší organizace, nakonfigurujte zásady tak, aby odpovídaly podezřelým akcím, a pak proveďte odpovídající opatření k jejich vyřešení. Další informace najdete v tématu [Azure AD Identity Protection](../identity-protection/index.yml).|
|Spravované identity pro prostředky Azure|Poskytuje službám Azure automatickou spravovanou identitu ve službě Azure AD, která dokáže ověřit jakoukoli ověřovací službu podporovanou službou Azure AD, včetně Key Vault. Další informace najdete v tématu [co jsou spravované identity pro prostředky Azure?](../managed-identities-azure-resources/overview.md).|
|Privileged Identity Management (PIM)|Spravujte, kontrolujte a sledujte přístup v rámci vaší organizace. Tato funkce zahrnuje přístup k prostředkům v Azure AD a Azure a dalších online službách Microsoftu, jako je Microsoft 365 nebo Intune. Další informace najdete v tématu [Azure AD Privileged Identity Management](../privileged-identity-management/index.yml).|
|Sestavy a sledování|Získejte přehled o zabezpečení a vzorcích použití ve vašem prostředí. Další informace najdete v tématu [Azure Active Directory sestav a monitorování](../reports-monitoring/index.yml).|

## <a name="terminology"></a>Terminologie

Abyste lépe pochopili službu Azure AD a její dokumentaci, doporučujeme, abyste provedli následující výrazy.

|Termín nebo koncept|Popis|
|---------------|-----------|
|Identita| Věc, která může získat ověření. Identitou může být uživatel s uživatelským jménem a heslem. Identity také obsahují aplikace nebo jiné servery, které mohou vyžadovat ověření prostřednictvím tajných klíčů nebo certifikátů.|
|Účet| Identita, která obsahuje data, která jsou k němu přidružená. Nemůžete mít účet bez identity.|
|Účet Azure AD| Identita vytvořená prostřednictvím Azure AD nebo jiné cloudové služby Microsoftu, například Microsoft 365. Identity se ukládají v Azure AD a jsou dostupné pro předplatné cloudových služeb vaší organizace. Tento účet se taky někdy označuje jako pracovní nebo školní účet.|
|Správce účtu|Tato klasická role Správce předplatného je koncepční pro platební majitele předplatného. Tato role má přístup k [centrum účtů Azure](https://account.azure.com/Subscriptions) a umožňuje spravovat všechna předplatná v účtu. Další informace najdete v tématech [role správců klasického předplatného, role Azure a role správce Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Správce služeb|Tato klasická role Správce předplatného umožňuje spravovat všechny prostředky Azure, včetně přístupu. Tato role má ekvivalentní přístup k uživateli, kterému je přiřazena role vlastníka v oboru předplatného. Další informace najdete v tématech [role správců klasického předplatného, role Azure a role správce Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Vlastník|Tato role vám pomůže se správou všech prostředků Azure, včetně přístupu. Tato role je postavená na novějším autorizačním systému nazývaném řízení přístupu na základě role Azure (Azure RBAC), které poskytuje jemně odstupňovanou správu přístupu k prostředkům Azure. Další informace najdete v tématech [role správců klasického předplatného, role Azure a role správce Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).|
|Globální správce Azure AD|Tato role správce se automaticky přiřadí službě whomever vytvořeného tenanta Azure AD. Globální správci můžou dělat všechny funkce správy pro Azure AD a jakékoli služby, které federovat do Azure AD, jako je Exchange Online, SharePoint Online a Online Skype pro firmy. Můžete mít několik globálních správců, ale k uživatelům může přiřazovat role správce (včetně přiřazování jiných globálních správců). Další informace o různých rolích správců najdete [v tématu oprávnění role správce v Azure Active Directory](../roles/permissions-reference.md).|
|Předplatné Azure| Používá se pro platby za Azure Cloud Services. Můžete mít mnoho předplatných a jsou propojeny s platební kartou.|
|Tenant Azure| Vyhrazená a důvěryhodná instance služby Azure AD, která se automaticky vytvoří, když si vaše organizace zaregistruje předplatné cloudové služby Microsoftu, například Microsoft Azure, Microsoft Intune nebo Microsoft 365. Tenant Azure představuje jednu organizaci.|
|Jeden tenant| Klienti Azure, kteří přistupují k dalším službám ve vyhrazeném prostředí, se považují za jediného tenanta.|
|Vícetenantové| Klienti Azure, kteří přistupují k dalším službám ve sdíleném prostředí v různých organizacích, se považují za více tenantů.|
|Adresář Azure AD|Každý tenant Azure má vyhrazený a důvěryhodný adresář služby Azure AD. Adresář Azure AD obsahuje uživatele, skupiny a aplikace tenanta a používá se k provádění funkcí správy identit a přístupu pro prostředky tenanta.|
|Vlastní doména|Každý nový adresář služby Azure AD obsahuje počáteční název domény domainname.onmicrosoft.com. Kromě tohoto počátečního názvu můžete také přidat názvy domén vaší organizace, včetně názvů používaných k podnikání a uživatelů, kteří používají pro přístup k prostředkům vaší organizace, do seznamu. Přidání vlastních názvů domén vám pomůže vytvořit uživatelská jména, která jsou pro vaše uživatele obeznámená, například alain@contoso.com .|
|Účet Microsoft (označovaný také jako MSA)|Osobní účty, které poskytují přístup k vašim zákaznickým produktům a cloudovým službám Microsoftu, jako je například Outlook, OneDrive, Xbox LIVE nebo Microsoft 365. Vaše účet Microsoft je vytvořená a uložená v systému účtu Microsoft Consumer identity, který je spuštěný Microsoftem.|

## <a name="next-steps"></a>Další kroky

- [Registrace služby Azure Active Directory Premium](active-directory-get-started-premium.md)

- [Přidružte k vašemu Azure Active Directory předplatné Azure](active-directory-how-subscriptions-associated-directory.md)

- [Azure Active Directory Premium – kontrolní seznam nasazení funkce P2](active-directory-deployment-checklist-p2.md)
