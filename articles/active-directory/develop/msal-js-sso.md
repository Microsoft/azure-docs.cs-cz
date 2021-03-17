---
title: Jednotné přihlašování (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Naučte se vytvářet prostředí jednotného přihlašování pomocí knihovny Microsoft Authentication Library pro JavaScript (MSAL.js).
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84690774"
---
# <a name="single-sign-on-with-msaljs"></a>Jednotné přihlašování s využitím MSAL.js

Single Sign-On (SSO) umožňuje uživatelům zadat své přihlašovací údaje jednou pro přihlášení a navázat relaci, která se dá znovu použít napříč více aplikacemi, aniž by se musela znovu ověřit. Tím se uživatelům poskytne bezproblémové prostředí a zkracuje opakované výzvy k zadání přihlašovacích údajů.

Azure AD poskytuje možnostem jednotného přihlašování pro aplikace nastavením souboru cookie relace při prvním ověření uživatele. Knihovna MSAL.js umožňuje aplikacím využívat tyto možnosti několika způsoby.

## <a name="sso-between-browser-tabs"></a>Jednotné přihlašování mezi kartami prohlížeče

Když je vaše aplikace otevřená na více kartách a nejdřív se přihlásíte na jednu kartu, uživatel se na dalších kartách přihlásí i bez výzvy. MSAL.js ukládá do mezipaměti token ID pro uživatele v prohlížeči `localStorage` a přihlásí uživatele do aplikace na ostatních otevřených kartách.

Ve výchozím nastavení MSAL.js používá, `sessionStorage` což neumožňuje sdílení relace mezi kartami. K získání jednotného přihlašování mezi kartami se ujistěte, že jste nastavili `cacheLocation` v MSAL.js na `localStorage` , jak je uvedeno níže.

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

## <a name="sso-between-apps"></a>Jednotné přihlašování mezi aplikacemi

Po ověření uživatele se v doméně Azure AD v prohlížeči nastaví soubor cookie relace. MSAL.js spoléhá na tento soubor cookie relace k poskytnutí jednotného přihlašování pro uživatele mezi různými aplikacemi. MSAL.js také ukládá do mezipaměti tokeny ID a přístupové tokeny uživatele v úložišti prohlížeče na aplikační doménu. V důsledku toho se chování jednotného přihlašování liší v různých případech:  

### <a name="applications-on-the-same-domain"></a>Aplikace ve stejné doméně

Když se aplikace hostují ve stejné doméně, uživatel se může přihlásit k aplikaci jednou a pak je ověřit i v ostatních aplikacích bez výzvy. MSAL.js využívá tokeny v mezipaměti pro uživatele v doméně k zajištění jednotného přihlašování.

### <a name="applications-on-different-domain"></a>Aplikace v jiné doméně

Když se aplikace hostují v různých doménách, k tokenům uloženým v mezipaměti v doméně A nelze přistupovat MSAL.js v doméně B.

To znamená, že když se uživatelé přihlásili v doméně A chtějí přejít k aplikaci v doméně B, budou přesměrováni nebo vyzváni na stránce Azure AD. Vzhledem k tomu, že Azure AD stále obsahuje soubor cookie uživatelské relace, přihlásí se uživateli a nebude muset přihlašovací údaje znovu zadávat. Pokud má uživatel v relaci s Azure AD víc uživatelských účtů, zobrazí se uživateli výzva k výběru příslušného účtu pro přihlášení.

### <a name="automatically-select-account-on-azure-ad"></a>Automaticky vybrat účet ve službě Azure AD

V některých případech má aplikace přístup k kontextu ověřování uživatele a chce se vyhnout zobrazování výzvy k výběru účtu Azure AD, když se přihlásí více účtů.  To lze provést několika různými způsoby:

**Použití ID relace (SID)**

ID relace je [volitelná deklarace identity](active-directory-optional-claims.md) , která se dá nakonfigurovat v tokenech ID. Tato deklarace umožňuje aplikaci identifikovat relaci Azure AD uživatele nezávisle na názvu nebo uživatelském účtu uživatele. Do volání můžete předat identifikátor SID v parametrech požadavku `acquireTokenSilent` . To umožní službě Azure AD obejít výběr účtu. Identifikátor SID je svázán se souborem cookie relace a nebude mezi kontexty prohlížeče.

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
> Identifikátor SID lze použít pouze s požadavky na tiché ověření provedené `acquireTokenSilent` voláním MSAL.js.
Postup pro konfiguraci volitelných deklarací v manifestu aplikace najdete [tady](active-directory-optional-claims.md).

**Použití pomocného parametru přihlášení**

Pokud nemáte nakonfigurovanou deklaraci SID nebo potřebujete obejít výzvu k výběru účtu při volání interaktivního ověřování, můžete to udělat tak, že `login_hint` v parametrech žádosti zadáte a volitelně také `domain_hint` jako `extraQueryParameters` v MSAL.js interaktivní metody ( `loginPopup` , `loginRedirect` a `acquireTokenPopup` `acquireTokenRedirect` ). Například:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

Hodnoty pro login_hint a domain_hint můžete získat tak, že si přečtete deklarace vracené v tokenu ID pro uživatele.

* **loginHint** by měl být nastaven na `preferred_username` deklaraci identity v tokenu ID.

* **domain_hint** se musí předávat jenom při použití autority/běžné. Pomocný parametr domény je určený ID tenanta (TID).  Pokud je `tid` deklarace identity v tokenu ID `9188040d-6c67-4c5b-b112-36a304b66dad` příjemcem. V opačném případě se jedná o organizace.

Další informace o hodnotách pomocného parametru přihlašovacích údajů a doméně najdete [tady](v2-oauth2-implicit-grant-flow.md) .

> [!Note]
> Nelze předávat identifikátory SID a login_hint současně. Výsledkem bude chybná odpověď.

## <a name="sso-without-msaljs-login"></a>Jednotné přihlašování bez MSAL.jsho přihlášení

MSAL.js vyžaduje, aby před získáním tokenů pro rozhraní API byla volána metoda přihlašování, aby navázala kontext uživatele. Vzhledem k tomu, že metody přihlášení jsou interaktivní, uživatel uvidí výzvu.

Existují určité případy, kdy aplikace mají přístup k kontextu nebo ID ověřeného uživatele prostřednictvím ověřování iniciované v jiné aplikaci a chtějí pomocí jednotného přihlašování získat tokeny bez prvního přihlášení prostřednictvím MSAL.js.

Příklad: uživatel je přihlášený k nadřazené webové aplikaci, která hostuje jinou aplikaci JavaScriptu spuštěnou jako doplněk nebo modul plug-in.

Možnosti jednotného přihlašování v tomto scénáři můžete dosáhnout následujícím způsobem:

Předejte rozhraní, `sid` Pokud je k dispozici (nebo `login_hint` volitelně `domain_hint` ) jako parametry žádosti pro MSAL.js `acquireTokenSilent` volání, následovně:

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

## <a name="sso-in-adaljs-to-msaljs-update"></a>Jednotné přihlašování v ADAL.js pro MSAL.js aktualizace

MSAL.js přináší paritu funkcí ADAL.js pro scénáře ověřování Azure AD. Aby migrace z ADAL.js MSAL.js snadná a nemusela se uživatelům zobrazit výzva k opětovnému přihlášení, knihovna přečte token ID představující relaci uživatele v ADAL.js mezipaměti a hladce se přihlásí uživatele v MSAL.js.  

Pokud chcete při aktualizaci z ADAL.js využít výhod jednotného přihlašování (SSO), budete muset zajistit, aby se knihovny používaly `localStorage` pro ukládání tokenů do mezipaměti. Nastavte na `cacheLocation` Jak `localStorage` v MSAL.js, tak v konfiguraci ADAL.js při inicializaci následujícím způsobem:


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

Po nakonfigurování MSAL.js bude moci číst stav uložený v mezipaměti ověřeného uživatele v ADAL.js a použít ho k poskytnutí jednotného přihlašování v MSAL.js.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [relaci jednotného přihlašování a hodnotách životnosti tokenů](active-directory-configurable-token-lifetimes.md) ve službě Azure AD.
