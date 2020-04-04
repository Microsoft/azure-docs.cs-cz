---
title: Kurz jednostránkových aplikací s úhlem – Azure
titleSuffix: Microsoft identity platform
description: Zjistěte, jak angular SPA aplikace můžete volat rozhraní API, které vyžaduje přístupové tokeny z koncového bodu platformy identity Microsoftu
services: active-directory
documentationcenter: dev-center-name
author: hahamil
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/05/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 81ade6f02b38a0a4d5ed04c8190b99216638c45d
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637844"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-an-angular-single-page-application-spa"></a>Přihlášení uživatelů a volání rozhraní Microsoft Graph API z úhlové jednostránkové aplikace (SPA)

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Některé aspekty této funkce se můžou před zveřejněním změnit.

Tato příručka ukazuje, jak může úhlová jednostránková aplikace (SPA):
- Přihlaste se k osobním účtům a pracovním a školním účtům
- Získání přístupového tokenu
- Volání rozhraní Microsoft Graph API nebo jiných rozhraní API, která vyžadují přístupové tokeny z *koncového bodu platformy identit microsoftu*

>[!NOTE]
>Tento výukový program vás provede, jak vytvořit nový úhlové SPA pomocí MSAL. Chcete-li si stáhnout ukázkovou aplikaci, podívejte se [na](quickstart-v2-angular.md)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Jak ukázková aplikace vygenerovaná touto příručkou funguje

![Ukazuje, jak ukázková aplikace vygenerovaná tímto kurzem funguje](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>Další informace

Ukázková aplikace vytvořená touto příručkou umožňuje rozhraní Angular SPA k dotazování rozhraní Microsoft Graph API nebo webového rozhraní API, které přijímá tokeny z koncového bodu platformy identity Microsoftu. MSAL pro úhlové knihovny je obálka základní knihovny MSAL.js. Umožňuje aplikacím Angular (6+) ověřovat podnikové uživatele pomocí služby Microsoft Azure Active Directory (AAD), uživatelů účtů Microsoft (MSA), uživatelů sociální identity (Facebook, Google, LinkedIn atd.) a získat přístup k Microsoft Cloudu nebo Microsoft Graphu. V tomto scénáři po přihlášení uživatele je požadován přístupový token a přidán do požadavků HTTP prostřednictvím hlavičky autorizace. Získání a obnovení tokenu zpracovává knihovna Microsoft Authentication Library (MSAL).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Knihovny

Tato příručka používá následující knihovnu:

|Knihovna|Popis|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Knihovna ověřování Microsoft pro trojúhelníkový obal jazyka JavaScript|

> [!NOTE]
> *Msal.js cílí* na koncový bod platformy identit microsoftu, který umožňuje osobním účtům a školním a pracovním účtům přihlásit se a získat tokeny. Koncový bod platformy identit y Microsoft má [určitá omezení](../azuread-dev/azure-ad-endpoint-comparison.md#limitations).
> Chcete-li porozumět rozdílům mezi koncovými body v1.0 a v2.0, přečtěte [si příručku pro porovnání koncových bodů](../azuread-dev/azure-ad-endpoint-comparison.md).

<!--end-collapse-->


## <a name="prerequisites"></a>Požadavky

* Ke spuštění tohoto kurzu potřebujete místní webový server, například [Node.js](https://nodejs.org/en/download/)

* Nainstalujte integrované vývojové prostředí (IDE), například [Visual Studio Code](https://code.visualstudio.com/download), chcete-li upravit soubory projektu.

* Pokyny v této příručce jsou založeny na souboru Node.js

## <a name="create-your-project"></a>Vytvoření projektu

Generovat novou úhlovou aplikaci pomocí následujících příkazů npm:

```Bash
npm install -g @angular/cli@8                    # Install the Angular CLI
npm install @angular/material@8 @angular/cdk@8   # Install the Angular Material component library (optional, for UI)
ng new my-application --routing=true --style=css # Generate a new Angular app
npm install msal @azure/msal-angular             # Install MSAL and MSAL Angular in your application
ng generate component page-name                  # To add a new page (such as a the home, profile page)
```

## <a name="register-your-application"></a>Registrace vaší aplikace

Podle pokynů [zaregistrujte jednostránkovou aplikaci](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-app-registration) na webu Azure Portal.

 Na stránce **Přehled** aplikace vaší registrace si poznamenejte hodnotu **ID aplikace (klienta)** pro pozdější použití.

 Zaregistrujte identifikátor URI `http://localhost:4200/` **přesměrování** a povolte implicitní nastavení udělení.

#### <a name="configure-your-angular-application"></a>Konfigurace úhlové aplikace

1. Ve *složce src/app* upravte *soubor app.module.ts* a přidejte `MSALModule` jej `imports` stejně jako `isIE` const, jak je znázorněno níže:

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
            authority: 'Enter_the_Cloud_Instance_Id_Here'/'Enter_the_Tenant_Info_Here', // This is your tenant id
            redirectUri: 'Enter_the_Redirect_Uri_Here'// This is your redirect URI
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE, // set to true for IE 11
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

    Nahraďte tyto hodnoty jako takové:

    |Název hodnoty|Informace|
    |---------|---------|
    |Enter_the_Application_Id_Here|Na stránce **Přehled** registrace vaší přihlášky je to Vaše **ID aplikace (klienta)** |
    |Enter_the_Cloud_Instance_Id_Here|Toto je instance cloudu Azure. Pro hlavní nebo globální cloud https://login.microsoftonline.comAzure zadejte . Národní cloudy (například Čína) viz [Národní cloudy](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).|
    |Enter_the_Tenant_Info_Here| Nastavte jednu z následujících možností: 1) Pokud vaše aplikace podporuje *účty v tomto organizačním adresáři*, nahraďte tuto hodnotu **ID adresáře (tenanta)** nebo **názvem klienta** (například *contoso.microsoft.com*). 2) Pokud vaše aplikace podporuje *účty v libovolném organizačním adresáři*, nahraďte tuto hodnotu **organizacemi**. 3) Pokud vaše aplikace podporuje *účty v libovolném organizačním adresáři a osobníúčty Microsoft*, nahraďte tuto hodnotu **běžnými**. 4) Chcete-li omezit podporu pouze na *osobní účty Microsoft*, nahraďte tuto hodnotu **spotřebiteli**. |
    |Enter_the_Redirect_Uri_Here|Nahradit`http://localhost:4200`|

    Další informace o dostupných konfigurovatelných možnostech naleznete [v tématu Inicializaci klientských aplikací](msal-js-initializing-client-applications.md).

2. Ve stejném souboru přidejte do horní části souboru následující import:

    ```javascript
    import { MsalModule, MsalInterceptor } from '@azure/msal-angular';
    ```

    ### <a name="import-modules"></a>Import modulů
    Přidejte následující příkazy importu na začátek`src/app/app.component.ts`
    ```javascript
    import { MsalService } from '@azure/msal-angular';
    import { Component, OnInit } from '@angular/core';
    ```
    ## <a name="sign-in-a-user"></a>Přihlášení uživatele

    Přidejte následující `AppComponent` kód pro přihlášení uživatele:

    ```javascript
    export class AppComponent implements OnInit {
        constructor(private broadcastService: BroadcastService, private authService: MsalService) { }

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
> Doporučujeme `loginRedirect` používat pro uživatele aplikace Internet Explorer.

## <a name="acquire-a-token"></a>Získání tokenu

### <a name="angular-interceptor"></a>Úhlový zachycovač

MSAL Angular `Interceptor` poskytuje třídu, která automaticky získává tokeny pro `http` odchozí požadavky, které používají klienta Angular ke známým chráněným prostředkům.

Nejprve zahrňte třídu `Interceptor` jako zprostředkovatele do vaší aplikace:

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

Dále poskytněte mapu `MsalModule.forRoot()` chráněných prostředků as `protectedResourceMap` `consentScopes`a zahrňte tyto obory do :

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
        storeAuthStateInCookie: isIE, // set to true for IE 11
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

Nakonec načtěte profil uživatele pomocí požadavku HTTP.

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
MSAL používá tři metody k `acquireTokenRedirect` `acquireTokenPopup`získání `acquireTokenSilent`tokenů: , , a . Doporučujeme však použít interceptor místo pro úhlové aplikace, jak je znázorněno v předchozí části.

#### <a name="get-a-user-token-silently"></a>Získání tokenu uživatele bez upozornění

Metoda `acquireTokenSilent` zpracovává token akvizice a obnovení bez interakce s uživatelem. Po `loginRedirect` spuštění `loginPopup` metody or poprvé se `acquireTokenSilent` běžně používá k získání tokenů používaných pro přístup k chráněným prostředkům v následných voláních. Volání požadavku nebo obnovení tokenů jsou prováděny tiše.

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

Kde `scopes` obsahuje obory, které jsou požadovány, aby byly vráceny v přístupovém tokenu pro rozhraní API.

Například:

* `["user.read"]`pro microsoft graph
* `["<Application ID URL>/scope"]`pro vlastní webová api `api://<Application ID>/access_as_user`(to znamená)

#### <a name="get-a-user-token-interactively"></a>Interaktivní získání tokenu uživatele

Někdy potřebujete, aby uživatel komunikoval s koncovým bodem platformy identit y Microsoft. Například:

* Uživatelé mohou potřebovat znovu zadat svá pověření, protože jejich heslo vypršelo.
* Vaše aplikace požaduje přístup k dalším oborům prostředků, se kterými musí uživatel souhlasit.
* Je vyžadováno dvoufaktorové ověřování.

Doporučený vzor pro většinu aplikací `acquireTokenSilent` je nejprve volat, pak `acquireTokenPopup` zachytit `acquireTokenRedirect`výjimku a potom volání (nebo ) spustit interaktivní požadavek.

Výsledky `acquireTokenPopup` volání v přihlašovacím okně místního okna. Alternativně `acquireTokenRedirect` přesměruje uživatele na koncový bod platformy identit microsoftu. V tomto okně musí uživatelé potvrdit svá pověření, udělit souhlas s požadovaným prostředkem nebo dokončit dvoufaktorové ověřování.

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
> Tento rychlý start `loginRedirect` `acquireTokenRedirect` používá metody a s aplikací Microsoft Internet Explorer z důvodu [známého problému](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) souvisejícího se zpracováním automaticky otevíraných oken aplikací Internet Explorer.

## <a name="log-out"></a>Odhlásit se

Přidejte následující kód k odhlášení uživatele.

```javascript
logout() {
  this.authService.logout();
}
```

#### <a name="add-ui"></a>Přidat ui
Pokladna [ukázkové aplikace](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) pro jednoduchý příklad, jak přidat uI pomocí knihovny komponent úhlového materiálu.

## <a name="test-your-code"></a>Testování kódu

1.  Spusťte webový server pro poslech portu spuštěním následujících příkazů na příkazovém řádku ze složky aplikace:

    ```bash
    npm install
    npm start
    ```
1. V prohlížeči **http://localhost:4200** zadejte nebo **http://localhost:{port}**, *kde* port je port, na kterém webový server naslouchá.


### <a name="provide-consent-for-application-access"></a>Poskytnout souhlas s přístupem k aplikaci

Při prvním přihlášení k aplikaci budete vyzváni k udělení přístupu k profilu a přihlášení:

![Okno "Požadovaná oprávnění"](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)



<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Další informace o oborech a delegovaných oprávněních

Rozhraní Microsoft Graph API vyžaduje, aby obor *user.read* četl profil uživatele. Ve výchozím nastavení je tento obor automaticky přidán do každé aplikace, která je registrována na registračním portálu. Další rozhraní API pro Microsoft Graph, stejně jako vlastní rozhraní API pro back-end server, může vyžadovat další obory. Například rozhraní API aplikace Microsoft Graph vyžaduje obor *Calendars.Read,* aby bylo možné zobrazit seznam kalendářů uživatele.

Chcete-li získat přístup ke kalendářům uživatele v kontextu aplikace, přidejte k informacím o registraci aplikace delegované oprávnění *Calendars.Read.* Potom přidejte *Calendars.Read* obor `acquireTokenSilent` volání.

>[!NOTE]
>Při zvýšení počtu oborů může být uživatel vyzván k zadání dalších souhlasů.

Pokud rozhraní API back-end nevyžaduje obor (nedoporučuje se), můžete použít *clientId* jako obor v volání získat tokeny.

<!--end-collapse-->

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další kroky

Projděte si repo MSAL pro dokumentaci, nejčastější dotazy, problémy a další:

> [!div class="nextstepaction"]
> [Úložiště MSAL.js GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js)
