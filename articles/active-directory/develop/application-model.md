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
ms.openlocfilehash: 2ba41e36d12b58da2e572cf870195716eacaddef
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2021
ms.locfileid: "98755683"
---
# <a name="application-model"></a>Aplikační model

Aplikace se můžou přihlašovat sami nebo delegovat přihlášení k poskytovateli identity. Toto téma popisuje kroky, které jsou nutné k registraci aplikace s platformou Microsoft identity.

## <a name="registering-an-application"></a>Registrace aplikace

Aby mohl poskytovatel identity zjistit, jestli má uživatel přístup ke konkrétní aplikaci, musí být uživatel i aplikace zaregistrované u poskytovatele identity. Při registraci aplikace ve službě Azure AD poskytujete konfiguraci identity pro vaši aplikaci, která umožňuje integraci s platformou Microsoft identity. Registrace aplikace vám taky umožní:

* Přizpůsobte si branding své aplikace v dialogovém okně přihlášení. To je důležité, protože se jedná o první prostředí, které bude mít uživatel s vaší aplikací.
* Rozhodněte se, jestli chcete uživatelům umožnit, aby se přihlásili jenom v případě, že patří do vaší organizace. Toto je jediná klientská aplikace. Nebo Umožněte uživatelům, aby se přihlásili pomocí pracovního nebo školního účtu. Toto je víceklientské aplikace. Můžete také umožnit osobní účty Microsoft nebo sociální účet z LinkedInu, Google atd.
* Požádat o oprávnění rozsahu. Můžete například požádat o obor "User. Read", který uděluje oprávnění ke čtení profilu přihlášeného uživatele.
* Definujte obory definující přístup k webovému rozhraní API. Když aplikace chce získat přístup k vašemu rozhraní API, obvykle bude potřebovat požádat o oprávnění k definovaným oborům.
* Sdílejte tajný klíč s platformou Microsoft identity, který prokáže identitu aplikace.  To je důležité v případě, kdy je aplikace důvěrná klientská aplikace. Důvěrná klientská aplikace je aplikace, která může bezpečně uchovávat přihlašovací údaje. Pro uložení přihlašovacích údajů vyžadují důvěryhodný back-end Server.

Po registraci se aplikaci udělí jedinečný identifikátor, který aplikace sdílí s platformou Microsoft identity při žádosti o tokeny. Pokud je aplikace [důvěrná klientská aplikace](developer-glossary.md#client-application), bude také sdílet tajný klíč nebo veřejný klíč v závislosti na tom, zda byly použity certifikáty nebo tajné klíče.

Platforma Microsoft Identity reprezentuje aplikace pomocí modelu, který splňuje dvě hlavní funkce:

* Identifikujte aplikaci pomocí ověřovacích protokolů, které podporuje.
* Zadejte všechny identifikátory, adresy URL, tajné klíče a související informace, které jsou potřeba k ověření.

Platforma Microsoft identity:

* Obsahuje všechna data potřebná k podpoře ověřování za běhu.
* Obsahuje všechna data pro rozhodování o tom, k jakým prostředkům může aplikace potřebovat přístup, a za jakých okolností by měla být daná žádost splněna.
* Poskytuje infrastrukturu pro implementaci zřizování aplikací v tenantovi vývojáře aplikace a na jakéhokoli jiného tenanta Azure AD.
* Zpracovává souhlas uživatele během doby žádosti o tokeny a usnadňuje dynamické zřizování aplikací napříč klienty.

**Souhlas** je proces vlastníka prostředku, který uděluje autorizaci pro klientské aplikace pro přístup k chráněným prostředkům v rámci konkrétních oprávnění jménem vlastníka prostředku. Platforma Microsoft identity:

* Umožňuje uživatelům a správcům dynamicky udělovat nebo odepírat souhlas s tím, aby aplikace jejich jménem měla přístup k prostředkům.
* Umožňuje správcům nakonec rozhodnout, co můžou aplikace provádět a kteří uživatelé můžou konkrétní aplikace používat a jak se přistupuje k prostředkům adresáře.

## <a name="multi-tenant-apps"></a>Aplikace s více tenanty

Na platformě Microsoft identity je [objekt aplikace](developer-glossary.md#application-object) popisuje aplikaci. V době nasazení používá platforma Microsoft identity objekt aplikace jako podrobný plán k vytvoření [instančního](developer-glossary.md#service-principal-object)objektu, který představuje konkrétní instanci aplikace v rámci adresáře nebo tenanta. Instanční objekt definuje, co může aplikace dělat v určitém cílovém adresáři, kdo ho může používat, k jakým prostředkům má přístup, a tak dále. Platforma Microsoft Identity vytvoří instanční objekt z objektu aplikace prostřednictvím [souhlasu](developer-glossary.md#consent).

Následující diagram znázorňuje zjednodušený postup zřizování platformy Microsoft Identity Platform založený na základě souhlasu. Zobrazuje dva *klienty: a* a *B*.

* *Tenant A* vlastní aplikaci.
* *Tenant B* vytváří instanci aplikace prostřednictvím instančního objektu.

![Zjednodušený tok zřizování s využitím souhlasu](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

V tomto toku zřizování:

1. Uživatel z tenanta B se pokusí přihlásit k aplikaci, koncový bod autorizace požaduje token pro aplikaci.
1. Přihlašovací údaje uživatele se získávají a ověřují pro ověřování.
1. Uživatel je vyzván k poskytnutí souhlasu aplikace, aby získal přístup k tenantovi B.
1. Platforma Microsoft identity používá aplikační objekt v tenantovi A jako plán pro vytvoření instančního objektu v tenantovi B.
1. Uživatel obdrží požadovaný token.

Tento postup můžete opakovat pro další klienty. Tenant A uchovává podrobný plán aplikace (objekt aplikace). Uživatelům a správcům všech ostatních tenantů, na kterých se aplikace uděluje, si můžete řídit, co může aplikace provádět přes odpovídající objekt instančního objektu v každém tenantovi. Další informace najdete v tématu [aplikační a instanční objekty v platformě Microsoft Identity Platform](app-objects-and-service-principals.md).

## <a name="next-steps"></a>Další kroky

Další témata týkající se ověřování a základů autorizace:

* V tématu [ověřování vs. autorizace](authentication-vs-authorization.md) se dozvíte o základních konceptech ověřování a autorizace na platformě Microsoft identity.
* V tématu [tokeny zabezpečení](security-tokens.md) se dozvíte, jak se v ověřování a autorizaci používají přístupové tokeny, aktualizují tokeny a tokeny ID.
* V tématu [Flow pro přihlášení k aplikaci](app-sign-in-flow.md) se dozvíte o procesu přihlašování webových, desktopových a mobilních aplikací na platformě Microsoft identity.

Další informace o modelu aplikace:

* Podívejte se [, jak a proč se aplikace přidávají do služby Azure AD](active-directory-how-applications-are-added.md) , kde najdete další informace o objektech aplikace a instančních objektech v platformě Microsoft identity.
* Další informace o aplikacích pro jediné klienty a víceklientské aplikace najdete v tématu tenant [v Azure Active Directory](single-and-multi-tenant-apps.md) .
* Další informace o tom, jak Azure AD poskytuje Azure Active Directory B2C, najdete v [dokumentaci Azure Active Directory B2C](../../active-directory-b2c/index.yml) , aby se organizace mohli přihlašovat uživatelům, obvykle zákazníkům, pomocí sociálních identit, jako je například účet Google.