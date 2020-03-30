---
title: Jednotné přihlašování (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Další informace o vytváření možností jednotného přihlašování pomocí knihovny Microsoft Authentication Library for JavaScript (MSAL.js).
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 8080d4cf4c3f0091f7837b3fccead5474c42db55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262851"
---
# <a name="single-sign-on-with-msaljs"></a>Jednotné přihlašování s využitím MSAL.js

Jednotné přihlašování (SSO) umožňuje uživatelům zadat svá pověření jednou k přihlášení a vytvořit relaci, která lze znovu použít ve více aplikacích bez nutnosti znovu ověřit. To poskytuje bezproblémové prostředí pro uživatele a snižuje opakované výzvy k zadání pověření.

Azure AD poskytuje možnosti služby Přihlašování uživatelů nastavením souboru cookie relace, když se uživatel ověří poprvé. Knihovna MSAL.js umožňuje aplikacím využít to v několika způsoby.

## <a name="sso-between-browser-tabs"></a>Připoutává kaso mezi kartami prohlížeče

Pokud je aplikace otevřená na více kartách a uživatel se poprvé přihlásíte na jedné kartě, je uživatel také přihlášen na ostatních kartách, aniž by byl vyzván. Soubor MSAL.js uloží token ID pro `localStorage` uživatele do mezipaměti v prohlížeči a přihlásí jej k aplikaci na ostatních otevřených kartách.

Ve výchozím nastavení používá `sessionStorage` soubor MSAL.js, který neumožňuje sdílení relace mezi kartami. Chcete-li získat přisychy jako `cacheLocation` přihrádky mezi `localStorage` kartami, ujistěte se, že nastavit v MSAL.js, jak je znázorněno níže.

```javascript
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

## <a name="sso-between-apps"></a>Připřipomene meze mezi aplikacemi

Když se uživatel ověří, soubor cookie relace se nadolá v prohlížeči Azure AD. Soubor MSAL.js spoléhá na tento soubor cookie relace, který poskytuje uživatelské přihlašování pro uživatele mezi různými aplikacemi. Soubor MSAL.js také ukládá tokeny ID a přístupové tokeny uživatele do úložiště prohlížeče pro jednotlivé domény aplikace. V důsledku toho chování spřimit se liší pro různé případy:  

### <a name="applications-on-the-same-domain"></a>Aplikace ve stejné doméně

Když jsou aplikace hostované ve stejné doméně, uživatel se může přihlásit k aplikaci jednou a pak se bez výzvy ověřovat do ostatních aplikací. MSAL.js využívá tokeny uložené v mezipaměti pro uživatele v doméně k poskytování přisuzování a připojetí.

### <a name="applications-on-different-domain"></a>Aplikace v jiné doméně

Pokud jsou aplikace hostovány v různých doménách, tokeny uložené v mezipaměti v doméně A nelze přistupovat msal.js v doméně B.

To znamená, že když uživatelé přihlášení do domény A přejdou do aplikace v doméně B, budou přesměrováni nebo vyzváni pomocí stránky Azure AD. Vzhledem k tomu, že Azure AD má stále soubor cookie relace uživatele, bude se přihlásit k uživateli a nebudou muset znovu zadat přihlašovací údaje. Pokud má uživatel více uživatelských účtů v relaci s Azure AD, uživatel bude vyzván k výběru příslušného účtu pro přihlášení.

### <a name="automatically-select-account-on-azure-ad"></a>Automatické vyvýběru účtu ve službě Azure AD

V některých případech má aplikace přístup k kontextu ověřování uživatele a chce se vyhnout výzvu k výběru účtu Azure AD, když je přihlášeno více účtů.  To lze provést několika různými způsoby:

**Použití ID relace (SID)**

ID relace je [volitelná deklarace,](active-directory-optional-claims.md) kterou lze nakonfigurovat v tokenech ID. Toto tvrzení umožňuje aplikaci identifikovat relaci Azure AD uživatele nezávisle na názvu účtu nebo uživatelském jménu uživatele. Můžete předat SID v parametrech požadavku `acquireTokenSilent` volání. To umožní Azure AD obejít výběr účtu. IDENTIFIKÁTOR SID je vázán na soubor cookie relace a nebude procházet kontexty prohlížeče.

```javascript
var request = {
    scopes: ["user.read"],
    sid: sid
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

> [!Note]
> SID lze použít pouze s požadavky `acquireTokenSilent` na tiché ověření provedené voláním v Souboru MSAL.js.
Postup konfigurace volitelných deklarací identity naleznete v manifestu aplikace [zde](active-directory-optional-claims.md).

**Použití nápovědy pro přihlášení**

Pokud nemáte nakonfigurovanou deklaraci SID nebo potřebujete obejít výzvu k `login_hint` výběru účtu v interaktivních `domain_hint` `extraQueryParameters` voláních ověřování, můžete tak učinit`loginPopup` `loginRedirect`zadáním parametrů požadavku a volitelně a jako v interaktivních metodách MSAL.js ( , , `acquireTokenPopup` a `acquireTokenRedirect`). Například:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

Hodnoty pro login_hint a domain_hint můžete získat přečtením deklarací vrácených v tokenu ID pro uživatele.

* **loginHint** by měl `preferred_username` být nastaven na deklaraci v tokenu ID.

* **domain_hint** je nutné předat pouze při použití /common autority. Nápověda domény je určena ID klienta(tid).  Pokud `tid` je `9188040d-6c67-4c5b-b112-36a304b66dad` deklarace v tokenu ID je spotřebitelé. V opačném případě se jedná o organizace.

Přečtěte si [zde](v2-oauth2-implicit-grant-flow.md) další informace o hodnotách pro nápovědu pro přihlášení a nápovědu domény.

> [!Note]
> Nelze předat SID a login_hint současně. Výsledkem bude chyba odpovědi.

## <a name="sso-without-msaljs-login"></a>Přihlašuje meze bez přihlášení msal.js

Podle návrhu MSAL.js vyžaduje, aby metoda přihlášení je volána k vytvoření kontextu uživatele před získáním tokenů pro rozhraní API. Vzhledem k tomu, že metody přihlášení jsou interaktivní, uživatel i ono zobrazí výzvu.

Existují určité případy, ve kterých aplikace mají přístup k kontextu ověřeného uživatele nebo token ID prostřednictvím ověřování iniciované v jiné aplikaci a chcete využít přihlašování k získání tokenů bez předchozího přihlášení prostřednictvím msal.js.

Příkladem je: Uživatel je přihlášen do nadřazené webové aplikace, která je hostitelem jiné javascriptové aplikace spuštěné jako doplněk nebo plugin.

Zkušenosti s přiřazujícím se k so v tomto scénáři lze dosáhnout následujícím způsobem:

Předat `sid` if k `login_hint` dispozici (nebo a volitelně `domain_hint`) jako parametry `acquireTokenSilent` požadavku na volání MSAL.js takto:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

## <a name="sso-in-adaljs-to-msaljs-update"></a>SSO v ADAL.js aktualizace MSAL.js

MSAL.js přináší paritu funkcí s ADAL.js pro scénáře ověřování Azure AD. Chcete-li usnadnit migraci z souboru ADAL.js do souboru MSAL.js a vyhnout se zobrazení výzvy uživatelům k dalšímu přihlášení, knihovna přečte token ID představující relaci uživatele v mezipaměti ADAL.js a bezproblémově se přihlásí k uživateli v souboru MSAL.js.  

Chcete-li využít chování jednotného přihlašování (SSO) při aktualizaci z ADAL.js, budete muset zajistit, že knihovny používají `localStorage` pro ukládání tokenů do mezipaměti. Nastavte `cacheLocation` do `localStorage` v konfiguraci MSAL.js a ADAL.js při inicializaci následujícím způsobem:


```javascript
// In ADAL.js
window.config = {
   clientId: 'g075edef-0efa-453b-997b-de1337c29185',
   cacheLocation: 'localStorage'
};

var authContext = new AuthenticationContext(config);


// In latest MSAL.js version
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

Jakmile je tato konfigurace, MSAL.js bude moci číst stavu ověřeného uživatele v mezipaměti v ADAL.js a použít k poskytování služby SSO v MSAL.js.

## <a name="next-steps"></a>Další kroky

Další informace o [jedné přihlašování relace a token životnost](active-directory-configurable-token-lifetimes.md) hodnoty ve službě Azure AD.
