---
title: Porovnání externích identit – Azure Active Directory | Microsoft Docs
description: Externí identity Azure AD umožňují lidem mimo vaši organizaci přistupovat k vašim aplikacím a prostředkům pomocí vlastní identity. Porovnejte řešení pro externí identity, včetně Azure Active Directory spolupráce B2B a Azure AD B2C.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 08/05/2020
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08623e6d69ac4db1790c1e9b46089f0c72c0526d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "87906048"
---
# <a name="what-are-external-identities-in-azure-active-directory"></a>Co jsou externí identity v Azure Active Directory?

S externími identitami v Azure AD můžete uživatelům mimo vaši organizaci umožnit přístup k vašim aplikacím a prostředkům a zároveň jim umožnit přihlašovat se pomocí libovolné identity, které preferuje. Vaši partneři, distributory, dodavatelé, dodavatelé a jiní uživatelé typu Host můžou přinášet vlastní identity. Bez ohledu na to, jestli jsou součástí Azure AD nebo jiného systému spravovaného IT nebo mají nespravovanou sociální identitu, jako je Google nebo Facebook, můžou k přihlášení použít svoje vlastní přihlašovací údaje. Zprostředkovatel identity spravuje identitu externího uživatele a Vy spravujete přístup k vašim aplikacím pomocí Azure AD, abyste zachovali své prostředky v ochraně. 

## <a name="external-identities-scenarios"></a>Scénáře externích identit

Azure AD External identity se v rámci vaší organizace liší od vztahu uživatele a další informace o tom, jak se jednotliví uživatelé chtějí přihlašovat k vašim aplikacím a prostředkům. V rámci této architektury Azure AD podporuje různé scénáře od spolupráce B2B (Business-to-Business) až po vývoj aplikací pro zákazníky a příjemce (od firmy po spotřebitele nebo B2C).

- **Sdílejte aplikace s externími uživateli (spolupráce B2B)**. Pozvání externích uživatelů do vlastního tenanta jako "host" uživatele, kterým můžete přiřadit oprávnění (pro autorizaci) a povolit jim používání stávajících přihlašovacích údajů (pro ověřování). Uživatelé se přihlásí ke sdíleným prostředkům pomocí jednoduchého procesu pozvání a uplatnění se svým pracovním účtem, školním účtem nebo libovolným e-mailovým účtem. A teď s dostupností uživatelských toků samoobslužného přihlašování (Preview) můžete také poskytnout svým externím uživatelům přihlašovací prostředí prostřednictvím aplikace, kterou chcete sdílet. Můžete nakonfigurovat nastavení toku uživatele, abyste mohli řídit, jak se uživatel zaregistruje do aplikace a který jim umožňuje používat svůj pracovní účet, školní účet nebo jakoukoli sociální identitu (například Google nebo Facebook), kterou chtějí používat.  Další informace najdete v [dokumentaci k Azure AD B2B](index.yml).

- **Vývoj aplikací, které jsou určené pro ostatní klienty Azure AD (jeden tenant nebo víceklientské tenanta)**. Při vývoji aplikací pro službu Azure AD můžete cílit uživatele z jedné organizace (jednoho tenanta) nebo uživatelů z jakékoli organizace, která už má tenanta Azure AD (označované jako aplikace s více klienty). Tyto víceklientské aplikace se registrují sami sami ve své vlastní službě Azure AD, ale můžou je použít libovolný uživatel Azure AD z jakékoli organizace bez jakékoli další práce na vaší straně.

- **Vývoj aplikací s prázdným označením pro uživatele a zákazníky (Azure AD B2C)** Pokud jste podnikem nebo vývojářem vytváření zákaznických aplikací, můžete pomocí Azure AD B2C škálovat na uživatele, zákazníky nebo občany. Vývojáři můžou použít Azure AD jako plně vybavený systém identit pro svoji aplikaci a zároveň umožnit zákazníkům, aby se přihlásili pomocí identity, kterou už vytvořili (například Facebook nebo Gmail). Pomocí Azure AD B2C můžete plně přizpůsobovat a řídit, jak se zákazníci při používání vašich aplikací registrují, přihlásí a spravují své profily. Další informace najdete v dokumentaci k [Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/).

## <a name="compare-external-identities-solutions"></a>Porovnání řešení externích identit

Následující tabulka obsahuje podrobné porovnání různých scénářů, které můžete povolit pomocí externích identit Azure AD.

| Víceklientské aplikace  | Spolupráce externích uživatelů (B2B) | Aplikace pro spotřebitele nebo zákazníky (B2C)  |
| ---- | --- | --- |
| Primární scénář: podnikový software jako služba (SaaS) | Primární scénář: spolupráce pomocí aplikací Microsoftu (Microsoft 365, týmů,...) nebo vlastního softwaru pro spolupráci.  | Primární scénář: transakční aplikace využívající vlastní vyvíjené aplikace   |
| Určeno pro: organizace, které chtějí poskytovat softwaru mnoho podnikových zákazníků.    | Komu je určená: Organizacím, které chtějí ověřovat uživatele z partnerských organizací bez ohledu na zprostředkovatele identity.    | Určeno pro: pozvání zákazníků vašich mobilních a webových aplikací, ať mezi jednotlivci, institucionálními nebo organizačními zákazníky v adresáři služby Azure AD oddělené od adresáře vaší organizace. |
| Podporované identity: zaměstnanci s účty Azure AD. | Podporované identity: U zaměstnanců a partnerů jsou to pracovní nebo školní účty nebo jakákoli e-mailová adresa. Brzy bude podporované přímé federování.      | Podporované identity: Koncoví uživatelé s účty místních aplikací (jakákoli e-mailová adresa nebo uživatelské jméno) nebo jiná podporovaná sociální identita s přímým federováním.       |
| Externí uživatelé jsou spravováni ve svém vlastním adresáři izolovaně z adresáře, ve kterém byla aplikace zaregistrována.    | Externí uživatelé jsou spravováni ve stejném adresáři jako zaměstnanci, ale speciálně s poznámkami. Můžou být spravované stejným způsobem jako zaměstnanci, můžou se přidávat do stejných skupin a tak dále.    | Externí uživatelé jsou spravováni v adresáři aplikace. Spravují se odděleně od zaměstnance organizace a adresáře partnerů (pokud existuje).  |
| Jednotné přihlašování: jednotné přihlašování pro všechny aplikace připojené k Azure AD je podporované.          | Jednotné přihlašování: jednotné přihlašování pro všechny aplikace připojené k Azure AD je podporované. Můžete například poskytnout přístup k Microsoft 365 nebo místním aplikacím a dalším aplikacím SaaS, jako je Salesforce nebo Workday.    | Jednotné přihlašování: jednotné přihlašování pro aplikace vlastněné zákazníkem v rámci Azure AD B2Cch tenantů je podporované. Jednotné přihlašování k Microsoft 365 nebo jiným aplikacím Microsoft SaaS není podporováno.    |
| Životní cyklus zákazníka: spravuje se v domovské organizaci uživatele.      | Životní cyklus partnera: Spravuje ho hostitelská organizace nebo organizace, která odesílá pozvánky.    | Životní cyklus zákazníka: Zajišťovaný samostatně nebo spravovaný aplikací.      |
| Zásady zabezpečení a dodržování předpisů: spravované organizací hostitele nebo pozváním (například pomocí [zásad podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)).           | Zásady zabezpečení a dodržování předpisů: spravované organizací hostitele nebo pozváním (například pomocí [zásad podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)). | Zásady zabezpečení a dodržování předpisů: Spravuje je aplikace.        |
| Branding: používá se značka organizace hostitele/pozvání.   | Branding: používá se značka organizace hostitele/pozvání.    | Značka: Spravuje ji aplikace. Většinou se používá značka produktu. Značka organizace ustupuje do pozadí.   |
| Další informace: [Správa identit v aplikacích s více klienty](https://docs.microsoft.com/azure/architecture/multitenant-identity/), [Průvodce postupy](https://docs.microsoft.com/azure/active-directory/develop/howto-convert-app-to-be-multi-tenant) | Další informace: [blogový příspěvek](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [dokumentace](what-is-b2b.md)                   | Další informace: [stránka produktu](https://azure.microsoft.com/services/active-directory-b2c/), [dokumentace](https://docs.microsoft.com/azure/active-directory-b2c/)       |

Zabezpečte a spravujte zákazníky a partnery nad rámec vašich organizačních hranic pomocí externích identit Azure AD.

## <a name="next-steps"></a>Další kroky

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- [Informace o Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/overview)
