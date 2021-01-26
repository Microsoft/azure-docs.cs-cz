---
title: Aplikační model | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si o procesu registrace aplikace, aby ji bylo možné integrovat s platformou Microsoft identity.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/28/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 86543b961698e736b2211553b0dca367b28158ef
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98795657"
---
# <a name="application-model"></a>Aplikační model

Aplikace se můžou přihlašovat sami nebo delegovat přihlášení k poskytovateli identity. Tento článek popisuje kroky, které jsou nutné k registraci aplikace s platformou Microsoft identity.

## <a name="register-an-application"></a>Registrace aplikace

Aby mohl poskytovatel identity zjistit, jestli má uživatel přístup ke konkrétní aplikaci, musí být uživatel i aplikace zaregistrované u poskytovatele identity. Při registraci aplikace pomocí služby Azure Active Directory (Azure AD) poskytujete konfiguraci identity pro vaši aplikaci, která umožňuje integraci s platformou Microsoft identity. Registrace aplikace vám taky umožní:

* Přizpůsobte si branding své aplikace v dialogovém okně přihlášení. Tato značka je důležitá, protože přihlašování je první prostředí, které bude mít uživatel s vaší aplikací.
* Rozhodněte, jestli chcete uživatelům dovolit, aby se přihlásili jenom v případě, že patří do vaší organizace. Tato architektura se označuje jako aplikace pro jednoho tenanta. Nebo můžete uživatelům dovolit, aby se přihlásili pomocí pracovního nebo školního účtu, který se označuje jako aplikace pro více tenantů. Z LinkedInu, Google atd. můžete také umožnit osobní účty Microsoft nebo sociální účet.
* Požádat o oprávnění rozsahu. Můžete například požádat o obor "User. Read", který uděluje oprávnění ke čtení profilu přihlášeného uživatele.
* Definujte obory definující přístup k webovému rozhraní API. Když aplikace chce získat přístup k vašemu rozhraní API, obvykle bude potřebovat požádat o oprávnění k definovaným oborům.
* Sdílejte tajný klíč s platformou Microsoft identity, který prokáže identitu aplikace. Použití tajného klíče je důležité v případě, kdy je aplikace důvěrná klientská aplikace. Důvěrná klientská aplikace je aplikace, která může bezpečně uchovávat přihlašovací údaje. K uložení přihlašovacích údajů se vyžaduje důvěryhodný back-end Server.

Po registraci aplikace se mu přidělí jedinečný identifikátor, který se při žádosti o tokeny sdílí s platformou Microsoft identity. Pokud je aplikace [důvěrná klientská aplikace](developer-glossary.md#client-application), bude také sdílet tajný klíč nebo veřejný klíč v závislosti na tom, zda byly použity certifikáty nebo tajné klíče.

Platforma Microsoft Identity reprezentuje aplikace pomocí modelu, který splňuje dvě hlavní funkce:

* Identifikujte aplikaci pomocí ověřovacích protokolů, které podporuje.
* Zadejte všechny identifikátory, adresy URL, tajné klíče a související informace, které jsou potřeba k ověření.

Platforma Microsoft identity:

* Obsahuje všechna data potřebná k podpoře ověřování za běhu.
* Obsahuje všechna data pro rozhodování o tom, k jakým prostředkům může aplikace potřebovat přístup, a za jakých okolností by měla být daná žádost splněna.
* Poskytuje infrastrukturu pro implementaci zřizování aplikací v tenantovi vývojáře aplikace a na jakéhokoli jiného tenanta Azure AD.
* Zpracovává souhlas uživatele během doby žádosti o token a usnadňuje dynamické zřizování aplikací napříč klienty.

*Souhlas* je proces vlastníka prostředku, který uděluje autorizaci pro klientské aplikace pro přístup k chráněným prostředkům v rámci konkrétních oprávnění jménem vlastníka prostředku. Platforma Microsoft Identity Platform umožňuje:

* Uživatelé a správci můžou k přístupu k prostředkům za účelem dynamického udělení nebo odmítnutí souhlasu aplikace získat přístup k prostředkům.
* Správci se nakonec rozhodují, jaké aplikace můžou dělat a kteří uživatelé můžou používat konkrétní aplikace a jak se k nim mají přistupovaly prostředky adresáře.

## <a name="multi-tenant-apps"></a>Aplikace s více tenanty

Na platformě Microsoft identity je [objekt aplikace](developer-glossary.md#application-object) popisuje aplikaci. V době nasazení používá platforma Microsoft identity objekt aplikace jako podrobný plán k vytvoření [instančního](developer-glossary.md#service-principal-object)objektu, který představuje konkrétní instanci aplikace v rámci adresáře nebo tenanta. Instanční objekt definuje, co může aplikace dělat v určitém cílovém adresáři, kdo ho může používat, k jakým prostředkům má přístup, a tak dále. Platforma Microsoft Identity vytvoří instanční objekt z objektu aplikace prostřednictvím [souhlasu](developer-glossary.md#consent).

Následující diagram znázorňuje zjednodušený postup zřizování platformy Microsoft Identity Platform založený na základě souhlasu. Zobrazuje dva *klienty: a* a *B*.

* *Tenant A* vlastní aplikaci.
* *Tenant B* vytváří instanci aplikace prostřednictvím instančního objektu.

![Diagram, který znázorňuje zjednodušený tok zřizování na základě souhlasu.](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

V tomto toku zřizování:

1. Uživatel z tenanta B se pokusí přihlásit pomocí aplikace. Koncový bod autorizace požaduje token pro aplikaci.
1. Přihlašovací údaje uživatele se získávají a ověřují pro ověřování.
1. Uživatel je vyzván k poskytnutí souhlasu aplikace, aby získal přístup k tenantovi B.
1. Platforma Microsoft identity používá aplikační objekt v tenantovi A jako plán pro vytvoření instančního objektu v tenantovi B.
1. Uživatel obdrží požadovaný token.

Tento postup můžete opakovat pro více tenantů. Tenant A uchovává podrobný plán aplikace (objekt aplikace). Uživatelům a správcům všech ostatních tenantů, na kterých se aplikace uděluje, si můžete řídit, co může aplikace provádět přes odpovídající objekt instančního objektu v každém tenantovi. Další informace najdete v tématu [aplikace a instanční objekty služby na platformě Microsoft Identity](app-objects-and-service-principals.md).

## <a name="next-steps"></a>Další kroky

Další informace o ověřování a autorizaci na platformě Microsoft identity najdete v následujících článcích:

* Další informace o základních principech ověřování a autorizace najdete v tématu [ověřování vs. Authorization](authentication-vs-authorization.md).
* Další informace o tom, jak se v ověřování a autorizaci používají přístupové tokeny, aktualizovat tokeny a tokeny ID, najdete v tématu [tokeny zabezpečení](security-tokens.md).
* Další informace o procesu přihlašování webových, desktopových a mobilních aplikací najdete v tématu [Flow přihlašování k aplikacím](app-sign-in-flow.md).

Další informace o modelu aplikace naleznete v následujících článcích:

* Další informace o objektech aplikace a instančních objektech na platformě Microsoft identity najdete v tématu [jak a proč se aplikace přidávají do služby Azure AD](active-directory-how-applications-are-added.md).
* Další informace o aplikacích pro jednoho tenanta a víceklientské aplikace najdete v tématu věnovaném tenantovi [v Azure Active Directory](single-and-multi-tenant-apps.md).
* Další informace o tom, jak Azure AD také poskytuje Azure Active Directory B2C, aby se organizace mohli přihlašovat uživatelům, obvykle zákazníkům pomocí sociálních identit, jako je například účet Google, najdete v [dokumentaci k Azure Active Directory B2C](../../active-directory-b2c/index.yml).