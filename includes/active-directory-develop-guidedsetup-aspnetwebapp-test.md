---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: d3f2efc0ae3dcf9bdcae3f82258b28b761944487
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988498"
---
## <a name="test-your-code"></a>Testování kódu

Chcete-li testovat aplikace v sadě Visual Studio, stiskněte **F5** ke spuštění projektu. V prohlížeči se otevře http://<span></span>localhost: {port} umístění a **přihlásit se účtem Microsoft** tlačítko. Vyberte tlačítko Spustit proces přihlašování.

Jakmile budete připraveni ke spuštění testu, použít účet Microsoft Azure Active Directory (Azure AD) (pracovní nebo školní účet) nebo osobního účtu Microsoft (<span>live.</span> com nebo <span>aplikace outlook.</span> com) a přihlaste se.

![Přihlásit se účtem Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Přihlaste se ke svému účtu Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="view-application-results"></a>Zobrazení výsledků aplikace

Po přihlášení, bude uživatel přesměrován na domovské stránce vašeho webu. Domovská stránka není adresu URL HTTPS, který je zadán v informace o registraci aplikace v portálu pro registraci aplikací společnosti Microsoft. Domovská stránka obsahuje zobrazení uvítací zprávy *"Hello \<uživatele >,"* odkaz Odhlásit se a odkaz k zobrazení deklarací identity uživatele. Přejde na odkaz pro deklarace identity uživatele k *deklarace identity* kontroler, který jste vytvořili dříve.

### <a name="browse-to-see-the-users-claims"></a>Procházením zobrazit deklarací identity uživatele

Deklarace identity uživatele zobrazíte výběrem odkazu a přejděte do zobrazení kontroleru, který je k dispozici jen ověřeným uživatelům.

#### <a name="view-the-claims-results"></a>Zobrazení výsledků deklarací identity

Až přejdete na zobrazení kontroleru, byste měli vidět tabulku, která obsahuje základní vlastnosti pro uživatele:

|Vlastnost |Hodnota |Popis |
|---|---|---|
|**Název** |Celé jméno uživatele | Uživatel a příjmení název.
|**Uživatelské jméno** |Uživatel<span>@domain.com</span> | Uživatelské jméno, který se používá k identifikaci uživatele.
|**Předmět** |Subjekt |Řetězec, který jednoznačně identifikuje uživatele na webu.|
|**ID tenanta** |Guid | A **guid** , který jedinečně reprezentuje uživatele Azure AD organizace.|

Kromě toho byste měli vidět tabulku všechny deklarace identity, které jsou v žádosti o ověření. Další informace najdete v tématu [seznam deklarací identity, které jsou v tokenu ID pro Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Testování přístupu k metodě, která má atribut Authorize (volitelné)

Testování přístupu jako anonymní uživatel k řadiči chráněný pomocí `Authorize` atribut, postupujte podle těchto kroků:

1. Výběrem odkazu neodhlásí uživatele a dokončit proces odhlašování.
2. V prohlížeči, zadat http://<span></span>localhost: {port} / deklarace identity pro přístup k řadiči, který je pak chráněn rozhraním `Authorize` atribut.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Po přístupu k řadiči chráněné očekávané výsledky

Zobrazí se výzva k ověření použijte zobrazení chráněných kontroleru.

## <a name="advanced-options"></a>Rozšířené možnosti

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>Ochranu celého webu
Pro ochranu celého webu, v **Global.asax** přidejte `AuthorizeAttribute` atribut `GlobalFilters` filtru v `Application_Start` metody:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-who-can-sign-in-to-your-application"></a>Omezit, kdo se přihlásit do aplikace

Ve výchozím nastavení při sestavování aplikace vytvořené v této příručce se vaše aplikace bude přijímat přihlášení osobní účty (včetně outlook.com, live.com a další) a také pracovní a školní účty z jakéhokoli společnosti nebo organizace, která obsahuje integrované Azure Active Directory. Toto je doporučená volba pro aplikace SaaS.

Pokud chcete omezit přístup přihlašování uživatelů pro vaši aplikaci, jsou k dispozici několik možností:

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Možnost 1: Omezení uživatelů z instance Active Directory pouze jedna organizace k přihlášení do aplikace (jeden tenant)

Tato možnost je běžným scénářem *aplikace LOB*: Pokud chcete, aby vaše aplikace tak, aby přijímal přihlášení pouze z účtů, které patří k určité instanci Azure Active Directory (včetně *účty hostů*dané instance) proveďte následující:

1. V **web.config** souboru, změňte hodnotu `Tenant` parametr z `Common` název tenanta organizace, jako například `contoso.onmicrosoft.com`.
2. Ve vaší [třída OWIN Startup](#configure-the-authentication-pipeline), nastavte `ValidateIssuer` argument `true`.

#### <a name="option-2-restrict-access-to-your-application-to-users-in-a-specific-list-of-organizations"></a>Možnost 2: Omezení přístupu k aplikaci pro uživatele v konkrétním seznamu organizací

Můžete omezit přístup k přihlášení k pouze uživatelské účty, které jsou v organizaci Azure AD, který je v seznamu povolených organizací:
1. Ve vaší [třída OWIN Startup](#configure-the-authentication-pipeline), nastavte `ValidateIssuer` argument `true`.
2. Nastavte hodnotu `ValidIssuers` parametru do seznamu povolených organizací.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>Možnost 3: Použití vlastní metodu k ověření vystavitele

Můžete implementovat vlastní metodu k ověření vystavitele s použitím **IssuerValidator** parametru. Další informace o tom, jak tento parametr použijte, přečtěte si informace o [parametry tokenvalidationparameters třída](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx) na webové stránce MSDN.

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]