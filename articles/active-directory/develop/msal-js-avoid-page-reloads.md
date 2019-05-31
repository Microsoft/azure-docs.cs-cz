---
title: Vyhněte se opětovné načtení stránky (knihovna Microsoft Authentication Library pro JavaScript) | Azure
description: Zjistěte, jak se vyhnout opětovné načtení stránky při získávání a obnovování tokeny tiše pomocí knihovna Microsoft Authentication Library pro JavaScript (MSAL.js).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/29/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 162811221e6dde89ad11f358b2ec8f32f3c82522
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420465"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>Při získávání a obnovování tokeny tiše využitím MSAL.js vyhnout opětovné načtení stránky
Knihovna Microsoft Authentication Library pro JavaScript (MSAL.js) používá skryté `iframe` prvků, které mají získat a obnovit tokeny tiše na pozadí. Azure AD vrátí token do registrované redirect_uri zadaný v požadavku na token (ve výchozím nastavení je to aplikace kořenovou stránku). Protože odpověď je 302, výsledkem HTML odpovídající `redirect_uri` načítán v `iframe`. Obvykle aplikace `redirect_uri` je kořenovou stránku, a to způsobí, že jej znovu načíst.

V ostatních případech, pokud přejdete na stránku kořenové aplikace vyžaduje ověření, to může vést na vnořených `iframe` elementy nebo `X-Frame-Options: deny` chyby.

Protože MSAL.js nelze zrušit 302 vydané službou Azure AD a k procesu vrácený token je potřeba, nelze zabránit `redirect_uri` z načítán v `iframe`.

Aby se zabránilo celé aplikace znovu načíst znovu nebo jiné chyby způsobené z toho důvodu, postupujte podle těchto alternativních řešení.

## <a name="specify-different-html-for-the-iframe"></a>Zadejte jiný kód HTML pro element iframe

Nastavte `redirect_uri` vlastnosti konfigurace pro jednoduché stránky, který nevyžaduje ověření. Abyste měli jistotu, že se shoduje s máte `redirect_uri` zaregistrovaný na webu Azure portal. Přihlášení uživatele to nebude mít vliv jak MSAL uloží na úvodní stránce, když uživatel zahájí proces přihlášení a přesměruje zpátky na přesné umístění po dokončení přihlášení.

## <a name="initialization-in-your-main-app-file"></a>Inicializace v souboru hlavní aplikace

Pokud vaše aplikace je strukturována tak, že existuje jedna centrální soubor jazyka Javascript, který definuje inicializace aplikace, směrování a další věci, můžete načíst podmíněně aplikace modulů na základě na, jestli se aplikace načítá `iframe` nebo ne. Příklad:

In AngularJS: app.js

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

V Angular: app.module.ts

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

## <a name="next-steps"></a>Další postup
Další informace o [sestavení jednostránkové aplikace (SPA)](scenario-spa-overview.md) využitím MSAL.js.