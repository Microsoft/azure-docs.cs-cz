---
title: Kurz jednostránkových aplikací s úhlem – Azure
titleSuffix: Microsoft identity platform
description: Zjistěte, jak angular SPA aplikace můžete volat rozhraní API, které vyžaduje přístupové tokeny z koncového bodu platformy identity Microsoftu.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 03/05/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: c3c12f78118734c31641b90e6fcb8469ddda30b0
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678011"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-an-angular-single-page-application"></a>Kurz: Přihlášení uživatelů a volání rozhraní Microsoft Graph API z jednostránkové aplikace s úhlem

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Některé aspekty této funkce se mohou před všeobecnou dostupností změnit.

Tento kurz ukazuje, jak může úhlová jednostránková aplikace (SPA):
- Přihlaste se k osobním účtům, pracovním účtům nebo školním účtům.
- Získejte přístupový token.
- Volání rozhraní Microsoft Graph API nebo jiných rozhraní API, které vyžadují přístupové tokeny z *koncového bodu platformy identity Microsoftu*.

>[!NOTE]
>Tento kurz vás provede, jak vytvořit nové úhlové SPA pomocí Knihovny ověřování Microsoft (MSAL). Pokud si chcete stáhnout ukázkovou aplikaci, podívejte se na [úvodní příručku](quickstart-v2-angular.md).

## <a name="how-the-sample-app-works"></a>Jak ukázková aplikace funguje

![Diagram, který ukazuje, jak ukázková aplikace vygenerovaná v tomto kurzu funguje](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>Další informace

Ukázková aplikace vytvořená v tomto kurzu umožňuje úhlové spa dotaz ovat rozhraní Microsoft Graph API nebo webové rozhraní API, které přijímá tokeny z koncového bodu platformy identity Microsoftu. MSAL pro úhlové knihovny je obálka základní knihovny MSAL.js. Umožňuje aplikacím Angular (6+) ověřovat podnikové uživatele pomocí služby Microsoft Azure Active Directory, uživatelů účtů Microsoft a uživatelů sociální identity (například Facebook, Google a LinkedIn). Knihovna také umožňuje aplikacím získat přístup ke cloudovým službám Microsoftu nebo microsoft graphu.

V tomto scénáři po přihlášení uživatele je požadován přístupový token a přidán do požadavků HTTP prostřednictvím hlavičky autorizace. Pořízení a obnovení tokenu zpracovává společnost MSAL.

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Knihovny

Tento kurz používá následující knihovnu:

|Knihovna|Popis|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Knihovna ověřování Microsoft pro trojúhelníkový obal jazyka JavaScript|

Zdrojový kód knihovny MSAL.js najdete v úložišti [AzureAD/microsoft-authentication-library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) na GitHubu.

<!--end-collapse-->


## <a name="prerequisites"></a>Požadavky

Chcete-li spustit tento kurz, potřebujete:

* Místní webový server, například [Node.js](https://nodejs.org/en/download/). Pokyny v tomto kurzu jsou založeny na Souboru Node.js.
* Integrované vývojové prostředí (IDE), jako je například [Visual Studio Code](https://code.visualstudio.com/download), chcete-li upravit soubory projektu.

## <a name="create-your-project"></a>Vytvoření projektu

Vygenerujte novou úhlovou aplikaci pomocí následujících příkazů npm:

```Bash
npm install -g @angular/cli@8                    # Install the Angular CLI
npm install @angular/material@8 @angular/cdk@8   # Install the Angular Material component library (optional, for UI)
ng new my-application --routing=true --style=css # Generate a new Angular app
npm install msal @azure/msal-angular             # Install MSAL and MSAL Angular in your application
ng generate component page-name                  # To add a new page (such as a home or profile page)
```

## <a name="register-your-application"></a>Registrace vaší aplikace

Podle [pokynů zaregistrujte jednostránkovou aplikaci](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-app-registration) na webu Azure Portal.

Na stránce **Přehled** aplikace vaší registrace si poznamenejte hodnotu **ID aplikace (klienta)** pro pozdější použití.

Zaregistrujte hodnotu **IDENTIFIKÁTORURI přesměrování** a **http://localhost:4200/** povolte implicitní nastavení udělení.

## <a name="configure-the-application"></a>Konfigurace aplikace

1. Ve složce *src/app* upravte *soubor app.module.ts* a přidejte `MSALModule` do `imports` i konstanty: `isIE`

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
    |Enter_the_Application_Id_Here|Na stránce **Přehled** registrace aplikace se jedná **o hodnotu ID aplikace (klienta).** |
    |Enter_the_Cloud_Instance_Id_Here|Toto je instance cloudu Azure. Pro hlavní nebo globální cloud **https://login.microsoftonline.com**Azure zadejte . Národní cloudy (například Čína) viz [Národní cloudy](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).|
    |Enter_the_Tenant_Info_Here| Nastavte jednu z následujících možností: Pokud vaše aplikace podporuje *účty v tomto organizačním adresáři*, nahraďte tuto hodnotu ID adresáře (tenanta) nebo názvem klienta (například **contoso.microsoft.com**). Pokud vaše aplikace podporuje *účty v libovolném organizačním adresáři*, nahraďte tuto hodnotu **organizacemi**. Pokud vaše aplikace podporuje *účty v libovolném organizačním adresáři a osobních účtech Microsoft*, nahraďte tuto hodnotu **běžnými**. Chcete-li omezit podporu pouze na *osobní účty Microsoft*, nahraďte tuto hodnotu **spotřebiteli**. |
    |Enter_the_Redirect_Uri_Here|Nahradit **http://localhost:4200**.|

    Další informace o dostupných konfigurovatelných možnostech naleznete [v tématu Inicializaci klientských aplikací](msal-js-initializing-client-applications.md).

2. V horní části stejného souboru přidejte následující příkaz importu:

    ```javascript
    import { MsalModule, MsalInterceptor } from '@azure/msal-angular';
    ```

3. Přidejte následující příkazy importu na začátek `src/app/app.component.ts`:

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

Nakonec načtěte profil uživatele pomocí požadavku HTTP:

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
MSAL používá tři metody k `acquireTokenRedirect` `acquireTokenPopup`získání `acquireTokenSilent`tokenů: , , a . Doporučujeme však `MsalInterceptor` použít třídu místo pro angular aplikace, jak je znázorněno v předchozí části.

#### <a name="get-a-user-token-silently"></a>Získání tokenu uživatele bez upozornění

Metoda `acquireTokenSilent` zpracovává token akvizice a obnovení bez interakce s uživatelem. Po `loginRedirect` spuštění `loginPopup` metody or poprvé se `acquireTokenSilent` běžně používá k získání tokenů používaných pro přístup k chráněným prostředkům v pozdějších voláních. Volání požadavku nebo obnovení tokenů jsou prováděny tiše.

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

V tomto `scopes` kódu obsahuje obory, které jsou požadovány, aby byly vráceny v přístupovém tokenu pro rozhraní API.

Příklad:

* `["user.read"]`pro microsoft graph
* `["<Application ID URL>/scope"]`pro vlastní webová api `api://<Application ID>/access_as_user`(to znamená)

#### <a name="get-a-user-token-interactively"></a>Interaktivní získání tokenu uživatele

Někdy potřebujete, aby uživatel komunikoval s koncovým bodem platformy identit y Microsoft. Příklad:

* Uživatelé mohou potřebovat znovu zadat svá pověření, protože jejich heslo vypršelo.
* Vaše aplikace požaduje přístup k dalším oborům prostředků, se kterými musí uživatel souhlasit.
* Je vyžadováno dvoufaktorové ověřování.

Doporučený vzor pro většinu aplikací `acquireTokenSilent` je nejprve volat, pak `acquireTokenPopup` zachytit `acquireTokenRedirect`výjimku a potom volání (nebo ) spustit interaktivní požadavek.

Výsledky `acquireTokenPopup` volání v automaticky otevírané přihlašovací okno. Alternativně `acquireTokenRedirect` přesměruje uživatele na koncový bod platformy identit microsoftu. V tomto okně musí uživatelé potvrdit svá pověření, udělit souhlas s požadovaným prostředkem nebo dokončit dvoufaktorové ověřování.

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

Přidejte následující kód pro odhlášení uživatele:

```javascript
logout() {
  this.authService.logout();
}
```

## <a name="add-ui"></a>Přidat ui
Příklad přidání ui pomocí knihovny komponent úhlového materiálu naleznete v [ukázkové aplikaci](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular).

## <a name="test-your-code"></a>Testování kódu

1.  Spusťte webový server pro poslech portu spuštěním následujících příkazů na příkazovém řádku ze složky aplikace:

    ```bash
    npm install
    npm start
    ```
1. V prohlížeči **http://localhost:4200** zadejte nebo **http://localhost:{port}**, *kde* port je port, na kterém webový server naslouchá.


### <a name="provide-consent-for-application-access"></a>Poskytnout souhlas s přístupem k aplikaci

Při prvním přihlášení k aplikaci budete vyzváni k udělení přístupu k vašemu profilu a povolení k přihlášení:

![Okno "Požadovaná oprávnění"](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)



<!--start-collapse-->
### <a name="add-scopes-and-delegated-permissions"></a>Přidání oborů a delegovaných oprávnění

Rozhraní Microsoft Graph API vyžaduje, aby obor *user.read* četl profil uživatele. Ve výchozím nastavení je tento obor automaticky přidán do každé aplikace, která je registrována na registračním portálu. Další rozhraní API pro Microsoft Graph, stejně jako vlastní rozhraní API pro back-end server, může vyžadovat další obory. Například rozhraní API aplikace Microsoft Graph vyžaduje obor *Calendars.Read,* aby bylo možné zobrazit seznam kalendářů uživatele.

Chcete-li získat přístup ke kalendářům uživatele v kontextu aplikace, přidejte k informacím o registraci aplikace delegované oprávnění *Calendars.Read.* Potom přidejte *Calendars.Read* obor `acquireTokenSilent` volání.

>[!NOTE]
>Při zvýšení počtu oborů může být uživatel vyzván k zadání dalších souhlasů.

Pokud rozhraní API back-end nevyžaduje obor (nedoporučuje se), můžete použít *clientId* jako obor v volání získat tokeny.

<!--end-collapse-->

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další kroky

Dále se dozvíte, jak se přihlásit k uživateli a získat tokeny v kurzu angular:

> [!div class="nextstepaction"]
> [Úhlové výukové program](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-angular)
