---
title: Kurz pro jednořádkový jednostránkové aplikaci – Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si, jak se v aplikacích úhlového ověřování můžou volat rozhraní API, které vyžaduje přístupové tokeny z koncového bodu Microsoft Identity Platform.
services: active-directory
author: hamiltonha
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 03/05/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40, devx-track-js
ms.openlocfilehash: 76e82a474d2575325b09e6e82c7319b22f451715
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91256921"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-an-angular-single-page-application"></a>Kurz: přihlášení uživatelů a volání rozhraní Microsoft Graph API z úhlové aplikace s jednou stránkou

V tomto kurzu se dozvíte, jak může jednorozměrná aplikace s jednou stránkou (SPA):
- Přihlaste se do osobních účtů, pracovních účtů nebo školních účtů.
- Získání přístupového tokenu
- Zavolejte Microsoft Graph rozhraní API nebo jiná rozhraní API, která vyžadují přístupové tokeny z *koncového bodu Microsoft Identity Platform*.

>[!NOTE]
>Tento kurz vás provede vytvořením nové úhlové lázně pomocí knihovny Microsoft Authentication Library (MSAL). Pokud si chcete stáhnout ukázkovou aplikaci, přečtěte si [rychlý Start](quickstart-v2-angular.md).

## <a name="how-the-sample-app-works"></a>Jak ukázková aplikace funguje

![Diagram znázorňující, jak ukázková aplikace vygenerovaná v tomto kurzu funguje](./media/tutorial-v2-angular/diagram-auth-flow-spa-angular.svg)

### <a name="more-information"></a>Další informace

Ukázková aplikace vytvořená v tomto kurzu umožňuje použití úhlového hesla pro dotazování rozhraní Microsoft Graph API nebo webového rozhraní API, které přijímá tokeny z koncového bodu Microsoft Identity Platform. MSAL pro úhlovou knihovnu je obálkou základní knihovny MSAL.js. Umožňuje, aby se k ověřování podnikových uživatelů používaly úhlové (6 +) aplikace pomocí Microsoft Azure Active Directory, účet Microsoft uživatelů a uživatelů sociálních identit (například Facebooku, Google a LinkedIn). Knihovna také umožňuje aplikacím získat přístup k cloudovým službám nebo Microsoft Graph Microsoftu.

V tomto scénáři se po přihlášení uživatele vyžádá přístupový token a přidá se do požadavků HTTP prostřednictvím autorizační hlavičky. Získání a obnovení tokenu jsou zpracovávány pomocí MSAL.

### <a name="libraries"></a>Knihovny

V tomto kurzu se používá následující knihovna:

|Knihovna|Popis|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Microsoft Authentication Library pro JavaScript – úhlová obálka|

Zdrojový kód knihovny MSAL.js můžete najít v úložišti [AzureAD/Microsoft-Authentication-Library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) na GitHubu.

## <a name="prerequisites"></a>Požadavky

Pro spuštění tohoto kurzu budete potřebovat:

* Místní webový server, například [Node.js](https://nodejs.org/en/download/). Pokyny v tomto kurzu jsou založené na Node.js.
* Integrované vývojové prostředí (IDE), jako je například [Visual Studio Code](https://code.visualstudio.com/download), pro úpravu souborů projektu.

## <a name="create-your-project"></a>Vytvoření projektu

Vygenerujte novou úhlovou aplikaci pomocí následujících příkazů npm:

```bash
npm install -g @angular/cli@8                    # Install the Angular CLI
ng new my-application --routing=true --style=css # Generate a new Angular app
cd my-application                                # Change to the app directory
npm install @angular/material@8 @angular/cdk@8   # Install the Angular Material component library (optional, for UI)
npm install msal @azure/msal-angular             # Install MSAL and MSAL Angular in your application
ng generate component page-name                  # To add a new page (such as a home or profile page)
```

## <a name="register-your-application"></a>Registrace aplikace

Postupujte podle [pokynů k registraci jednostránkové aplikace](./scenario-spa-app-registration.md) v Azure Portal.

Na stránce **Přehled** aplikace vaší registrace si poznamenejte hodnotu **ID aplikace (klienta)** pro pozdější použití.

Zaregistrujte hodnotu **identifikátoru URI přesměrování** jako **http://localhost:4200/** a povolte nastavení implicitní udělení.

## <a name="configure-the-application"></a>Konfigurace aplikace

1. Ve složce *Src/App* upravte *App. Module. TS* a přidejte `MSALModule` do `imports` něj i `isIE` konstantu:

    ```javascript
    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;
    @NgModule({
      declarations: [
        AppComponent
      ],
      imports: [
        BrowserModule,
        AppRoutingModule,
        MsalModule.forRoot({
          auth: {
            clientId: 'Enter_the_Application_Id_here', // This is your client ID
            authority: 'Enter_the_Cloud_Instance_Id_Here'/'Enter_the_Tenant_Info_Here', // This is your tenant ID
            redirectUri: 'Enter_the_Redirect_Uri_Here'// This is your redirect URI
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE, // Set to true for Internet Explorer 11
          },
        }, {
          popUp: !isIE,
          consentScopes: [
            'user.read',
            'openid',
            'profile',
          ],
          unprotectedResources: [],
          protectedResourceMap: [
            ['https://graph.microsoft.com/v1.0/me', ['user.read']]
          ],
          extraQueryParameters: {}
        })
      ],
      providers: [],
      bootstrap: [AppComponent]
    })
    ```

    Nahraďte tyto hodnoty:

    |Název hodnoty|Informace|
    |---------|---------|
    |Enter_the_Application_Id_Here|Na stránce **Přehled** registrace vaší aplikace se jedná o hodnotu ID vaší **aplikace (klienta)** . |
    |Enter_the_Cloud_Instance_Id_Here|Toto je instance cloudu Azure. V případě hlavního nebo globálního cloudu Azure zadejte **https://login.microsoftonline.com** . Pro národní cloudy (například Čína) si přečtěte téma [národní cloudy](./authentication-national-cloud.md).|
    |Enter_the_Tenant_Info_Here| Nastavte na jednu z následujících možností: Pokud vaše aplikace podporuje *účty v tomto organizačním adresáři*, nahraďte tuto hodnotu ID adresáře (tenant) nebo názvem tenanta (například **contoso.Microsoft.com**). Pokud vaše aplikace podporuje *účty v jakémkoli organizačním adresáři*, nahraďte tuto hodnotu **organizacemi**. Pokud vaše aplikace podporuje *účty v libovolném organizačním adresáři a osobních účtech Microsoft*, nahraďte tuto hodnotu **běžnými**. Pokud chcete omezit podporu *jenom na osobní účty Microsoft*, nahraďte tuto hodnotu **příjemci**. |
    |Enter_the_Redirect_Uri_Here|Nahraďte parametrem **http://localhost:4200** .|

    Další informace o dostupných konfigurovatelných možnostech najdete v tématu [inicializace klientských aplikací](msal-js-initializing-client-applications.md).

2. V horní části stejného souboru přidejte následující příkaz import:

    ```javascript
    import { MsalModule, MsalInterceptor } from '@azure/msal-angular';
    ```

3. Přidejte následující příkazy pro import do horní části `src/app/app.component.ts` :

    ```javascript
    import { MsalService, BroadcastService } from '@azure/msal-angular';
    import { Component, OnInit } from '@angular/core';
    ```
## <a name="sign-in-a-user"></a>Přihlášení uživatele

Přidejte následující kód pro `AppComponent` přihlášení uživatele:

```javascript
export class AppComponent implements OnInit {
    constructor(private broadcastService: BroadcastService, private authService: MsalService) { }

    ngOnInit() { }

    login() {
        const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

        if (isIE) {
          this.authService.loginRedirect({
            extraScopesToConsent: ["user.read", "openid", "profile"]
          });
        } else {
          this.authService.loginPopup({
            extraScopesToConsent: ["user.read", "openid", "profile"]
          });
        }
    }
}
```

> [!TIP]
> Doporučujeme používat `loginRedirect` pro uživatele Internet Exploreru.

## <a name="acquire-a-token"></a>Získání tokenu

### <a name="angular-interceptor"></a>Úhlové zachycování

MSAL úhlová `Interceptor` Třída poskytuje třídu, která automaticky získá tokeny pro odchozí požadavky, které používají úhlového `http` klienta ke známým chráněným prostředkům.

Nejprve zahrňte `Interceptor` třídu jako poskytovatele do vaší aplikace:

```javascript
import { MsalInterceptor, MsalModule } from "@azure/msal-angular";
import { HTTP_INTERCEPTORS, HttpClientModule } from "@angular/common/http";

@NgModule({
    // ...
    providers: [
        {
            provide: HTTP_INTERCEPTORS,
            useClass: MsalInterceptor,
            multi: true
        }
    ]
}
```

Dále zadejte mapu chráněných prostředků `MsalModule.forRoot()` jako `protectedResourceMap` a přidejte tyto obory do `consentScopes` :

```javascript
@NgModule({
  // ...
  imports: [
    // ...
    MsalModule.forRoot({
      auth: {
        clientId: 'Enter_the_Application_Id_here', // This is your client ID
        authority: 'https://login.microsoftonline.com/Enter_the_Tenant_Info_Here', // This is your tenant info
        redirectUri: 'Enter_the_Redirect_Uri_Here' // This is your redirect URI
      },
      cache: {
        cacheLocation: 'localStorage',
        storeAuthStateInCookie: isIE, // Set to true for Internet Explorer 11
      },
    },
    {
      popUp: !isIE,
      consentScopes: [
        'user.read',
        'openid',
        'profile',
      ],
      unprotectedResources: [],
      protectedResourceMap: [
        ['https://graph.microsoft.com/v1.0/me', ['user.read']]
      ],
      extraQueryParameters: {}
    })
  ],
});
```

Nakonec načtěte profil uživatele s požadavkem HTTP:

```JavaScript
const graphMeEndpoint = "https://graph.microsoft.com/v1.0/me";

getProfile() {
  this.http.get(graphMeEndpoint).toPromise()
    .then(profile => {
      this.profile = profile;
    });
}
```

### <a name="acquiretokensilent-acquiretokenpopup-acquiretokenredirect"></a>acquireTokenSilent, acquireTokenPopup, acquireTokenRedirect
MSAL používá tři metody pro získání tokenů: `acquireTokenRedirect` , `acquireTokenPopup` a `acquireTokenSilent` . Doporučujeme však `MsalInterceptor` místo toho použít třídu pro úhlové aplikace, jak je znázorněno v předchozí části.

#### <a name="get-a-user-token-silently"></a>Získání tokenu uživatele bez upozornění

`acquireTokenSilent`Metoda zpracovává získání a obnovení tokenu bez zásahu uživatele. Po `loginRedirect` `loginPopup` prvním spuštění metody nebo se `acquireTokenSilent` běžně používá k získání tokenů používaných pro přístup k chráněným prostředkům v pozdějších voláních. Volání požadavků na požadavky nebo obnovení tokenů se provádí tiše.

```javascript
const requestObj = {
    scopes: ["user.read"]
};

this.authService.acquireTokenSilent(requestObj).then(function (tokenResponse) {
    // Callback code here
    console.log(tokenResponse.accessToken);
}).catch(function (error) {
    console.log(error);
});
```

V tomto kódu `scopes` obsahuje obory, které jsou požadovány k vrácení do přístupového tokenu pro rozhraní API.

Příklad:

* `["user.read"]` pro Microsoft Graph
* `["<Application ID URL>/scope"]` pro vlastní webová rozhraní API (tj. `api://<Application ID>/access_as_user` )

#### <a name="get-a-user-token-interactively"></a>Interaktivní získání tokenu uživatele

Někdy potřebujete, aby uživatel spolupracoval s koncovým bodem Microsoft Identity Platform. Příklad:

* Uživatelé možná budou muset znovu zadat svoje přihlašovací údaje, protože vypršela platnost hesla.
* Vaše aplikace požaduje přístup k dalším oborům prostředků, ke kterým uživatel musí vyjádřit souhlas.
* Je vyžadováno dvojúrovňové ověřování.

Doporučený vzor pro většinu aplikací je zavolat jako `acquireTokenSilent` první a pak zachytit výjimku a pak voláním `acquireTokenPopup` (nebo `acquireTokenRedirect` ) spustit interaktivní požadavek.

`acquireTokenPopup`Výsledkem volání je automaticky otevírané okno pro přihlášení. Případně `acquireTokenRedirect` přesměruje uživatele na koncový bod Microsoft Identity Platform. V tomto okně si uživatelé musí potvrdit své přihlašovací údaje, udělit souhlas požadovanému prostředku nebo dokončit dvojúrovňové ověřování.

```javascript
  const requestObj = {
      scopes: ["user.read"]
  };

  this.authService.acquireTokenPopup(requestObj).then(function (tokenResponse) {
      // Callback code here
      console.log(tokenResponse.accessToken);
  }).catch(function (error) {
      console.log(error);
  });
```

> [!NOTE]
> Tento rychlý Start používá `loginRedirect` metody a v `acquireTokenRedirect` aplikaci Microsoft Internet Explorer kvůli [známému problému](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) souvisejícímu se zpracováním automaticky otevíraných oken v aplikaci Internet Explorer.

## <a name="log-out"></a>Odhlásit se

Přidejte následující kód pro odhlášení uživatele:

```javascript
logout() {
  this.authService.logout();
}
```

## <a name="add-ui"></a>Přidat uživatelské rozhraní
Příklad, jak přidat uživatelské rozhraní pomocí knihovny komponent na základě úhlových materiálů, najdete v [ukázkové aplikaci](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular).

## <a name="test-your-code"></a>Testování kódu

1.  Spuštěním následujících příkazů na příkazovém řádku ve složce aplikace spusťte webový server a naslouchat portu.

    ```bash
    npm install
    npm start
    ```
1. V prohlížeči zadejte **http://localhost:4200** nebo **http://localhost:{port}** , kde *port* je port, na kterém naslouchá webový server.


### <a name="provide-consent-for-application-access"></a>Poskytnutí souhlasu pro přístup k aplikaci

Při prvním přihlášení do aplikace se zobrazí výzva, abyste udělili přístup k vašemu profilu a povolili vám přihlášení:

![Okno požadovaná oprávnění](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

## <a name="add-scopes-and-delegated-permissions"></a>Přidat obory a delegovaná oprávnění

Rozhraní Microsoft Graph API vyžaduje, aby *uživatel. přečetl* obor pro čtení profilu uživatele. Ve výchozím nastavení se tento obor automaticky přidá do každé aplikace, která je zaregistrovaná na portálu pro registraci. Jiná rozhraní API pro Microsoft Graph a také vlastní rozhraní API pro back-end Server můžou vyžadovat další obory. Například rozhraní Microsoft Graph API vyžaduje kalendáře. Pokud chcete zobrazit seznam kalendářů uživatelů, je nutné obor *číst.*

Chcete-li získat přístup k kalendářům uživatele v kontextu aplikace, přidejte *kalendáře. Přečtěte si* delegované oprávnění k informacím o registraci aplikace. Pak přidejte *kalendáře. Přečtěte* si rozsah `acquireTokenSilent` volání.

>[!NOTE]
>Uživatel může být vyzván k dalšímu souhlasu při zvýšení počtu oborů.

Pokud rozhraní API back-endu nevyžaduje obor (nedoporučuje se), můžete použít *ClientID* jako obor v voláních pro získání tokenů.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další kroky

Pokud se správou identit a přístupů ještě nepoužíváte, máme několik článků, které vám pomůžou naučit se moderní koncepty ověřování, počínaje [ověřováním vs. Authorization](authentication-vs-authorization.md).

Pokud byste chtěli podrobně hlubší vývoj aplikací na platformě Microsoft Identity Platform, scénář s více částmi: řada článků s [jednou stránkou](scenario-spa-overview.md) vám může pomáhat začít.