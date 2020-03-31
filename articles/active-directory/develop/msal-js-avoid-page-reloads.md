---
title: Vyhněte se opětovnému načtení stránky (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Zjistěte, jak se vyhnout opětovnému načtení stránky při získávání a obnovování tokenů tiše pomocí knihovny Microsoft Authentication Library for JavaScript (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/29/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 63944a5a9af34c2d4cf98eeb870a730df49654e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084951"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>Vyhněte se opětovnému načtení stránky při získávání a obnovování tokenů tiše pomocí souboru MSAL.js
Microsoft Authentication Library for JavaScript (MSAL.js) používá skryté `iframe` prvky k získání a obnovení tokenů tiše na pozadí. Azure AD vrátí token zpět do registrované redirect_uri zadaný v požadavku na token (ve výchozím nastavení se jedná o kořenovou stránku aplikace). Vzhledem k tomu, že odpověď je 302, výsledkem je HTML odpovídající `redirect_uri` získávání načten v `iframe`. Obvykle `redirect_uri` aplikace je kořenová stránka a to způsobí, že k opětovnému načtení.

V ostatních případech pokud přechod na kořenovou stránku aplikace vyžaduje `iframe` ověření, `X-Frame-Options: deny` může to vést k vnořené prvky nebo chyba.

Vzhledem k tomu, že MSAL.js nelze zavřít 302 vydané službou Azure `redirect_uri` AD a `iframe`je nutné zpracovat vrácený token, nemůže zabránit získání načtení v .

Chcete-li se vyhnout opětovnému načtení celé aplikace nebo jiným chybám způsobeným v důsledku tohoto, postupujte podle těchto řešení.

## <a name="specify-different-html-for-the-iframe"></a>Určení jiného kódu HTML pro iframe

Nastavte `redirect_uri` vlastnost na config na jednoduchou stránku, která nevyžaduje ověření. Musíte se ujistit, že `redirect_uri` odpovídá registrované na webu Azure Portal. To nebude mít vliv na přihlašovací prostředí uživatele jako MSAL uloží úvodní stránku, když uživatel začne proces přihlášení a přesměruje zpět na přesné místo po dokončení přihlášení.

## <a name="initialization-in-your-main-app-file"></a>Inicializace v hlavním souboru aplikace

Pokud je vaše aplikace strukturovaná tak, že existuje jeden centrální soubor Javascriptu, který definuje inicializaci, směrování a další věci aplikace, `iframe` můžete podmíněně načíst moduly aplikace na základě toho, jestli se aplikace načítá v aplikaci nebo ne. Například:

V AngularJS: app.js

```javascript
// Check that the window is an iframe and not popup
if (window !== window.parent && !window.opener) {
angular.module('todoApp', ['ui.router', 'MsalAngular'])
    .config(['$httpProvider', 'msalAuthenticationServiceProvider','$locationProvider', function ($httpProvider, msalProvider,$locationProvider) {
        msalProvider.init(
            // msal configuration
        );

        $locationProvider.html5Mode(false).hashPrefix('');
    }]);
}
else {
    angular.module('todoApp', ['ui.router', 'MsalAngular'])
        .config(['$stateProvider', '$httpProvider', 'msalAuthenticationServiceProvider', '$locationProvider', function ($stateProvider, $httpProvider, msalProvider, $locationProvider) {
            $stateProvider.state("Home", {
                url: '/Home',
                controller: "homeCtrl",
                templateUrl: "/App/Views/Home.html",
            }).state("TodoList", {
                url: '/TodoList',
                controller: "todoListCtrl",
                templateUrl: "/App/Views/TodoList.html",
                requireLogin: true
            })

            $locationProvider.html5Mode(false).hashPrefix('');

            msalProvider.init(
                // msal configuration
            );
        }]);
}
```

V úhlové: app.module.ts

```javascript
// Imports...
@NgModule({
  declarations: [
    AppComponent,
    MsalComponent,
    MainMenuComponent,
    AccountMenuComponent,
    OsNavComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    HttpClientModule,
    ServiceWorkerModule.register('ngsw-worker.js', { enabled: environment.production }),
    MsalModule.forRoot(environment.MsalConfig),
    SuiModule,
    PagesModule
  ],
  providers: [
    HttpServiceHelper,
    {provide: HTTP_INTERCEPTORS, useClass: MsalInterceptor, multi: true},
    AuthService
  ],
  entryComponents: [
    AppComponent,
    MsalComponent
  ]
})
export class AppModule {
  constructor() {
    console.log('APP Module Constructor!');
  }

  ngDoBootstrap(ref: ApplicationRef) {
    if (window !== window.parent && !window.opener)
    {
      console.log("Bootstrap: MSAL");
      ref.bootstrap(MsalComponent);
    }
    else
    {
    //this.router.resetConfig(RouterModule);
      console.log("Bootstrap: App");
      ref.bootstrap(AppComponent);
    }
  }
}
```

MsalComponent:

```javascript
import { Component} from '@angular/core';
import { MsalService } from '@azure/msal-angular';

// This component is used only to avoid Angular reload
// when doing acquireTokenSilent()

@Component({
  selector: 'app-root',
  template: '',
})
export class MsalComponent {
  constructor(private Msal: MsalService) {
  }
}
```

## <a name="next-steps"></a>Další kroky
Další informace o [vytváření jednostránkové aplikace (SPA)](scenario-spa-overview.md) pomocí souboru MSAL.js.