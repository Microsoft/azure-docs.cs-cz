---
title: Porovnání externích identit – Azure Active Directory | Microsoft Docs
description: Externí identity Azure AD umožňují lidem mimo vaši organizaci přistupovat k vašim aplikacím a prostředkům pomocí vlastní identity. Porovnejte řešení pro externí identity, včetně Azure Active Directory spolupráce B2B a Azure AD B2C.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 10/23/2020
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 420cc2bc38d079fa95f6b90ed20cb31e994f4ea3
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "93027070"
---
# <a name="what-are-external-identities-in-azure-active-directory"></a>Co jsou externí identity v Azure Active Directory?

S externími identitami v Azure AD můžete uživatelům mimo vaši organizaci umožnit přístup k vašim aplikacím a prostředkům a zároveň jim umožnit přihlašovat se pomocí libovolné identity, které preferuje. Vaši partneři, distributory, dodavatelé, dodavatelé a jiní uživatelé typu Host můžou přinášet vlastní identity. Bez ohledu na to, jestli mají digitální identitu Podniková nebo státní správy nebo nespravovanou sociální identitu, jako je Google nebo Facebook, můžou k přihlášení použít svoje vlastní přihlašovací údaje. Zprostředkovatel identity externího uživatele spravuje svou identitu a Vy spravujete přístup k vašim aplikacím pomocí Azure AD, abyste zachovali svoje prostředky.

## <a name="external-identities-scenarios"></a>Scénáře externích identit

U externích identit Azure AD se u vaší organizace méně zaměřuje na vztah uživatele a další informace o tom, jak se uživatel chce přihlásit k vašim aplikacím a prostředkům. V rámci této architektury Azure AD podporuje řadu scénářů od spolupráce B2B (Business-to-Business) pro přístup ke správě pro aplikace pro spotřebitele, zákazníky a občany (z firmy na zákazníka nebo B2C).

- **Sdílejte své aplikace a prostředky pomocí externích uživatelů (spolupráce B2B)** . Pozvání externích uživatelů do vlastního tenanta jako "host" uživatele, ke kterým můžete přiřadit oprávnění (pro autorizaci) a současně jim umožnit použití jejich stávajících přihlašovacích údajů (pro ověřování). Uživatelé se přihlásí ke sdíleným prostředkům pomocí jednoduchého procesu pozvání a uplatnění se svým pracovním, školním nebo jiným e-mailovým účtem. Můžete také použít [správu nároků Azure AD](../governance/entitlement-management-overview.md) ke konfiguraci zásad, které [spravují přístup pro externí uživatele](../governance/entitlement-management-external-users.md#how-access-works-for-external-users). A teď s dostupností [uživatelských toků samoobslužného přihlašování (Preview)](self-service-sign-up-overview.md)můžete dovolit externím uživatelům zaregistrovat se k aplikacím sami. Prostředí je možné přizpůsobit tak, aby umožňovalo registraci pomocí pracovní, školní nebo sociální identity (například Google nebo Facebook). Během procesu registrace můžete také shromažďovat informace o uživateli. Další informace najdete v [dokumentaci k Azure AD B2B](index.yml).

- **Sestavování cest uživatelů pomocí řešení pro správu identit z bílého popisku pro aplikace zaměřené na uživatele a zákazníky (Azure AD B2C)** . Pokud jste podnikem nebo vývojářem vytváření zákaznických aplikací, můžete škálovat na miliony spotřebitelů, zákazníků nebo občanů pomocí Azure AD B2C. Vývojáři můžou Azure AD používat jako plně funkční systém pro správu zákaznických identit a přístupu (CIAM) pro své aplikace. Zákazníci se můžou přihlásit pomocí identity, kterou už vytvořili (například Facebook nebo Gmail). Pomocí Azure AD B2C můžete plně přizpůsobovat a řídit, jak se zákazníci při používání vašich aplikací registrují, přihlásí a spravují své profily. Další informace najdete v dokumentaci k [Azure AD B2C](../../active-directory-b2c/index.yml).

## <a name="compare-external-identities-solutions"></a>Porovnání řešení externích identit

Následující tabulka obsahuje podrobné porovnání scénářů, které můžete povolit s externími identitami služby Azure AD.

|   | Spolupráce externích uživatelů (B2B) | Přístup k aplikacím pro spotřebitele a zákazníky (B2C)  |
| ---- | --- | --- |
| **Primární scénář** | Spolupráce pomocí aplikací Microsoftu (Microsoft 365, týmů atd.) nebo vašich vlastních aplikací (aplikace SaaS, vlastní aplikace vyvinuté atd.).  | Správa identit a přístupu pro moderní SaaS nebo vlastní aplikace vyvinuté aplikací (ne jako aplikace Microsoftu od první strany).   |
| **Určeno pro**    | Spolupráce s obchodními partnery z externích organizací, jako jsou dodavatelé, partneři a dodavatelé. Uživatelé se v adresáři zobrazí jako uživatelé typu Host. Tito uživatelé mohou nebo nemusí být spravováni.  | Zákazníci vašeho produktu. Tito uživatelé jsou spravováni v samostatném adresáři služby Azure AD.  |
| **Podporovaná zprostředkovatelé identity** | Externí uživatelé můžou spolupracovat pomocí pracovních účtů, školních účtů, všech e-mailových adres, SAML a WS-Fed zprostředkovatelů identity založených na službě Gmail a Facebooku.  | Uživatelé spotřebitelů s místními účty aplikací (libovolná e-mailová adresa nebo uživatelské jméno), různými podporovanými sociálními identitami a uživateli s podnikovými a vládními identitami prostřednictvím přímé federace.       |
| **Správa externích uživatelů**   | Externí uživatelé jsou spravováni ve stejném adresáři jako zaměstnanci, ale obvykle jsou poznámí jako uživatelé typu Host. Uživatele typu Host lze spravovat stejným způsobem jako zaměstnanci, přidaných do stejných skupin atd.    | Externí uživatelé jsou spravováni v adresáři Azure AD B2C. Spravují se odděleně od zaměstnance organizace a adresáře partnerů (pokud existuje).  |
| **Jednotné přihlašování (SSO)**      | Podporuje se jednotné přihlašování pro všechny aplikace připojené k Azure AD. Můžete například poskytnout přístup k Microsoft 365 nebo místním aplikacím a dalším aplikacím SaaS, jako je Salesforce nebo Workday.    | V rámci tenantů Azure AD B2C je podporované jednotné přihlašování k aplikacím patřícím zákazníkům. Jednotné přihlašování pro Microsoft 365 nebo jiné aplikace Microsoft SaaS se nepodporuje.    |
| **Zásady zabezpečení a dodržování předpisů**        | Spravované organizací hostitele nebo pozváním (například pomocí [zásad podmíněného přístupu](conditional-access.md)). | Spravováno organizací prostřednictvím podmíněného přístupu a Identity Protection.        |
| **Značka**  | Používá se značka hostitele/pozvání organizace.    | Plně přizpůsobitelné branding na aplikaci nebo organizaci   |
| **Model fakturace** | [Ceny za externí identity](https://azure.microsoft.com/en-us/pricing/details/active-directory/external-identities/) na základě měsíčních aktivních uživatelů (MAU). <br>(Viz také: [Podrobnosti nastavení B2B](external-identities-pricing.md)) | [Ceny za externí identity](https://azure.microsoft.com/en-us/pricing/details/active-directory/external-identities/) na základě měsíčních aktivních uživatelů (MAU). <br>(Viz také: [Podrobnosti o nastavení B2C](../../active-directory-b2c/billing.md)) |
| **Další informace** | [Příspěvek na blogu](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [dokumentace](what-is-b2b.md)                   | [Stránka produktu](https://azure.microsoft.com/services/active-directory-b2c/), [dokumentace](../../active-directory-b2c/index.yml)       |

Zabezpečte a spravujte zákazníky a partnery nad rámec vašich organizačních hranic pomocí externích identit Azure AD.

## <a name="about-multitenant-applications"></a>O víceklientské aplikacích

Pokud poskytujete aplikaci jako službu a nechcete spravovat uživatelské účty zákazníků, je pro vás nejvhodnější volbou pro víceklientské aplikace. Když vyvíjíte aplikace určené pro jiné klienty Azure AD, můžete cílit na uživatele z jedné organizace (jednoho tenanta) nebo uživatelů z jakékoli organizace, která už má tenanta Azure AD (víceklientské aplikace). Registrace aplikací ve službě Azure AD jsou ve výchozím nastavení jeden tenant, ale můžete vytvořit víceklientského tenanta registrace. Tato víceklientské aplikace se jednou zaregistruje ve vaší vlastní službě Azure AD. Ale pak všichni uživatelé Azure AD z libovolné organizace můžou aplikaci používat bez další práce na vaší straně. Další informace najdete v tématu [Správa identit ve víceklientské aplikaci](https://docs.microsoft.com/azure/architecture/multitenant-identity/), [Průvodce postupy](../develop/howto-convert-app-to-be-multi-tenant.md).

## <a name="next-steps"></a>Další kroky

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- [Informace o Azure AD B2C](../../active-directory-b2c/overview.md)
