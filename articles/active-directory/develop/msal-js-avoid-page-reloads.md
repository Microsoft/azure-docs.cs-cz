---
title: Vyhnout se opětovnému načtení stránky (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si, jak se vyhnout opětovnému načítání stránek při opakovaném získání a obnovování tokenů pomocí knihovny Microsoft Authentication Library pro JavaScript (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 05/29/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 0c96d161e55261af1bbe04eae6ead1d245158d02
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98064823"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>Vyhněte se opětovnému načítání stránek při získávání a obnovování tokenů v tichém režimu pomocí MSAL.js
Knihovna Microsoft Authentication Library pro JavaScript (MSAL.js) používá skryté `iframe` prvky pro získání a obnovení tokenů v tichém režimu na pozadí. Azure AD vrátí token zpátky do registrované redirect_uri zadaného v žádosti o token (ve výchozím nastavení se jedná o kořenovou stránku aplikace). Vzhledem k tomu, že odpověď je 302, má za následek, že kód HTML odpovídá načtení `redirect_uri` v `iframe` . Obvykle `redirect_uri` je kořenová stránka aplikace a to způsobí, že se znovu načte.

V ostatních případech se při přechodu na kořenovou stránku aplikace vyžaduje ověření, může to vést k vnořeným `iframe` prvkům nebo `X-Frame-Options: deny` chybě.

Vzhledem k tomu, že MSAL.js nemůže zrušit 302 vydaný službou Azure AD a je vyžadován ke zpracování vráceného tokenu, nemůže zabránit v `redirect_uri` načtení z `iframe` .

Chcete-li se vyhnout opětovnému opětovnému načtení celé aplikace nebo k jiným chybám způsobeným tímto způsobem, postupujte podle těchto alternativních řešení.

## <a name="specify-different-html-for-the-iframe"></a>Zadejte pro prvek IFRAME jiný kód HTML.

Nastavte `redirect_uri` vlastnost na konfiguraci na jednoduchou stránku, která nevyžaduje ověření. Musíte zajistit, aby odpovídaly `redirect_uri` zaregistrovanému v Azure Portal. Nebude to mít vliv na přihlašovací uživatelské prostředí, protože MSAL ukládá úvodní stránku, když uživatel zahájí proces přihlášení a přesměrovává zpátky do přesného umístění po dokončení přihlášení.

## <a name="initialization-in-your-main-app-file"></a>Inicializace v hlavním souboru aplikace

Pokud je vaše aplikace strukturována tak, že existuje jeden centrální soubor JavaScriptu, který definuje inicializaci, směrování a další věci aplikace, můžete podmíněně načíst moduly aplikací na základě toho, jestli se aplikace načítá `iframe` nebo ne. Například:

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

V úhlovém: App. Module. TS

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
Přečtěte si další informace o [vytváření jednostránkové aplikace (Spa)](scenario-spa-overview.md) pomocí MSAL.js.