---
title: Jednotné přihlašování (knihovna Microsoft Authentication Library pro JavaScript) | Azure
description: Další informace o vytváření jednotné přihlašování prostředí s využitím knihovna Microsoft Authentication Library pro JavaScript (MSAL.js).
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a0fb0731b7ac46210294e3766b33bd6a239dcc2
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075847"
---
# <a name="single-sign-on-with-msaljs"></a>Jednotné přihlašování s MSAL.js

Jednotné přihlašování (SSO) umožňuje uživatelům zadat své přihlašovací údaje jednou přihlásit a vytvořit relaci, což může být znovu použít napříč více aplikacemi bez nutnosti znovu provést ověření. To poskytuje bezproblémové prostředí pro uživatele a snižuje opakované výzvy k zadání přihlašovacích údajů.

Azure AD poskytuje funkce jednotného přihlašování k aplikacím tím, že nastavíte souboru cookie relace, když se uživatel ověřuje poprvé. Knihovna MSAL.js umožňuje aplikacím využívat to několika způsoby.

## <a name="sso-between-browser-tabs"></a>Jednotné přihlašování mezi záložkách prohlížeče

Když vaše aplikace je otevřen v několik karet a první přihlášení uživatele na jedné kartě, uživatel je také přihlášení na ostatních kartách bez zobrazování výzev. MSAL.js ukládá do mezipaměti token ID pro uživatele v prohlížeči `localStorage` a bude uživatele přihlásit do aplikace na otevřené karty.

Ve výchozím nastavení používá MSAL.js `sessionStorage` což nepovoluje relaci ke sdílení mezi kartami. Pokud chcete získat jednotné přihlašování mezi kartami, nezapomeňte nastavit `cacheLocation` v MSAL.js k `localStorage` jak je znázorněno níže.

```javascript
const config = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

## <a name="sso-between-apps"></a>Jednotné přihlašování mezi aplikacemi

Při ověření uživatele soubor cookie relace je nastavený na doménu Azure AD v prohlížeči. MSAL.js spoléhá na tento soubor cookie relace k zajištění jednotného přihlašování pro uživatele mezi různými aplikacemi. MSAL.js také ukládá, tokeny typu ID a přístupové tokeny uživatele v prohlížeči úložiště pro doménu aplikace. Chování jednotného přihlašování v důsledku toho se liší pro různé případy:  

### <a name="applications-on-the-same-domain"></a>Aplikace ve stejné doméně.

Když aplikace hostuje ve stejné doméně, uživatel se můžete přihlásit do aplikace jednou a potom získá ostatním aplikacím bez výzvy k ověření. MSAL.js využívá tokeny v mezipaměti pro uživatele v doméně mohla používat jednotné přihlašování.

### <a name="applications-on-different-domain"></a>Aplikace v jiné doméně

Když aplikace jsou hostované v různých doménách, tokeny v mezipaměti v doméně A není přístupná MSAL.js v doméně B.

To znamená, že když uživatelé přihlášení domény nebo přejde na aplikaci v doméně B a, bude přesměrován nebo výzva se stránkou Azure AD. Protože Azure AD má stále souboru cookie relace uživatele, se přihlásit uživatele a nebudete muset znovu zadat přihlašovací údaje. Pokud má uživatel více uživatelských účtů v relaci s Azure AD, uživateli zobrazí výzva k výběru příslušné účtu se přihlásit pomocí.

### <a name="automatically-select-account-on-azure-ad"></a>Automaticky vyberte účet v Azure AD

V některých případech aplikace má přístup k kontext ověřování uživatele a chce vyhnout řádku výběr účtu služby Azure AD se při přihlášení více účtů.  To lze provést několika různými způsoby:

**Pomocí ID relace (SID)**

ID relace je [volitelnou deklaraci](active-directory-optional-claims.md) , který se dá nakonfigurovat v ID tokenů. Tato deklarace identity umožňuje, aby aplikace k identifikaci uživatele Azure AD relace nezávisle na název účtu uživatele nebo uživatelské jméno. Identifikátor SID můžete předat parametry požadavku a `acquireTokenSilent` volání. To vám umožní obejít výběru účtu Azure AD. Identifikátor SID je vázán na soubor cookie relace a nebude pro různé prohlížeče kontexty.

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
> Identifikátor SID jde použít jenom s bezobslužné ověření požadavků provedených `acquireTokenSilent` volání v MSAL.js.
Postup pro konfiguraci nepovinných deklarací identity v manifestu aplikace můžete najít [tady](active-directory-optional-claims.md).

**Pomocí pokynů pro přihlášení**

Pokud nemáte SID deklarace identity nakonfigurovaná nebo potřebujete obejít výzvu výběru účtu ve voláních interaktivní ověřování, lze provést zadáním `login_hint` parametry požadavku a volitelně `domain_hint` jako `extraQueryParameters` v MSAL.js interaktivních metod (`loginPopup`, `loginRedirect`, `acquireTokenPopup` a `acquireTokenRedirect`). Příklad:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

Můžete získat hodnoty pro login_hint a domain_hint načtením deklarací identity vrátí token ID pro uživatele.

* **loginHint** by mělo být nastavené `preferred_username` deklarací identity v tokenu ID.

* **domain_hint** jenom je potřeba předávat při použití / Common autoritu. Nápověda domény je určeno tenanta ID(tid).  Pokud `tid` deklarace identity v tokenu ID je `9188040d-6c67-4c5b-b112-36a304b66dad` je příjemci. V opačném případě je organizace.

Čtení [tady](v2-oauth2-implicit-grant-flow.md) Další informace o hodnotách pomocný parametr přihlášení a nápovědu k doméně.

> [!Note]
> Identifikátor SID a login_hint nelze předat ve stejnou dobu. Výsledkem bude chybová odpověď.

## <a name="sso-without-msaljs-login"></a>Jednotné přihlašování bez MSAL.js přihlášení

Návrh MSAL.js vyžaduje, že způsob přihlášení je volána k vytvoření kontextu uživatele před získáním tokenů pro rozhraní API. Protože způsobů přihlášení jsou interaktivní, uživateli se zobrazí výzva.

Existují určité případy, ve kterých aplikace mají přístup ke kontextu ověřeného uživatele nebo ID tokenu ověřování zahájeno v jiné aplikaci a chcete využít jednotné přihlašování k získání tokenů bez první přihlášení pomocí MSAL.js.

Příkladem je: Uživatel je přihlášený do nadřazené webové aplikace, který je hostitelem jiná aplikace JavaScript spuštěna jako doplněk nebo modulu plug-in.

Jednotné prostředí v tomto scénáři můžete dosáhnout následujícím způsobem:

Předání `sid` Pokud je k dispozici (nebo `login_hint` a volitelně `domain_hint`) tak, jak požádat o parametry se mají MSAL.js `acquireTokenSilent` volání následujícím způsobem:

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

## <a name="sso-in-adaljs-to-msaljs-update"></a>Jednotné přihlašování v ADAL.js MSAL.js Update

MSAL.js přináší paritu funkcí s ADAL.js pro scénáře ověřování služby Azure AD. Provést migraci z ADAL.js MSAL.js snadné a vyhnuli zobrazení výzvy k zadání se znovu přihlásit, knihovně přečte ID token představující uživatelské relace v mezipaměti ADAL.js a bez problémů přihlásí uživatele v MSAL.js.  

Abyste mohli využívat jednotné přihlašování (SSO) chování při aktualizaci z ADAL.js, budete muset na knihovny musí používat `localStorage` pro ukládání do mezipaměti tokenů. Nastavte `cacheLocation` k `localStorage` v konfiguraci MSAL.js i ADAL.js při inicializaci následujícím způsobem:


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
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

Po ta se nakonfiguruje, bude moct číst v mezipaměti stavu ověřeného uživatele v ADAL.js a použijte ho k zajištění jednotného přihlašování v MSAL.js MSAL.js.

## <a name="next-steps"></a>Další postup

Další informace o [jednotné přihlašování relace a životnosti tokenu](active-directory-configurable-token-lifetimes.md) hodnoty ve službě Azure AD.
