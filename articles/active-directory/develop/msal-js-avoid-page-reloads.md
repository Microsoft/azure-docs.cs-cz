---
title: Vyhnout se opětovnému načtení stránky (MSAL. js) | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si, jak se vyhnout opětovnému načítání stránek při opakovaném získání a obnovování tokenů pomocí knihovny Microsoft Authentication Library pro JavaScript (MSAL. js).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/29/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: e68798861d5799a4314bd9cd9b2eeeadb926a90f
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76696142"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>Vyhněte se opětovnému načítání stránek při získávání a obnovování tokenů v tichém režimu pomocí MSAL. js.
Knihovna Microsoft Authentication Library pro JavaScript (MSAL. js) používá skryté prvky `iframe` k získání a obnovení tokenů v tichém režimu na pozadí. Azure AD vrátí token zpátky do registrované redirect_uri zadaného v žádosti o token (ve výchozím nastavení se jedná o kořenovou stránku aplikace). Vzhledem k tomu, že odpověď je 302, má za následek, že kód HTML odpovídá `redirect_uri` načítání v `iframe`. Obvykle je `redirect_uri` aplikace kořenovou stránkou a to způsobí, že se znovu načte.

V ostatních případech se při přechodu na kořenovou stránku aplikace vyžaduje ověření, může to vést k vnořeným `iframe`m prvkům nebo chybě `X-Frame-Options: deny`.

Vzhledem k tomu, že MSAL. js nemůže přeskočit 302 vydaný službou Azure AD a je vyžadován ke zpracování vráceného tokenu, nemůže zabránit `redirect_uri` načítání do `iframe`.

Chcete-li se vyhnout opětovnému opětovnému načtení celé aplikace nebo k jiným chybám způsobeným tímto způsobem, postupujte podle těchto alternativních řešení.

## <a name="specify-different-html-for-the-iframe"></a>Zadejte pro prvek IFRAME jiný kód HTML.

Nastavte vlastnost `redirect_uri` v konfiguraci na jednoduchou stránku, která nevyžaduje ověření. Musíte zajistit, aby odpovídaly `redirect_uri` zaregistrovanému v Azure Portal. Nebude to mít vliv na přihlašovací uživatelské prostředí, protože MSAL ukládá úvodní stránku, když uživatel zahájí proces přihlášení a přesměrovává zpátky do přesného umístění po dokončení přihlášení.

## <a name="initialization-in-your-main-app-file"></a>Inicializace v hlavním souboru aplikace

Pokud je vaše aplikace strukturována tak, že existuje jeden centrální soubor JavaScriptu, který definuje inicializaci, směrování a další věci aplikace, můžete podmíněně načíst moduly aplikací na základě toho, jestli se aplikace načítá v `iframe`. Například:

V AngularJS: App. js

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
Přečtěte si další informace o [vytváření jednostránkové aplikace (Spa)](scenario-spa-overview.md) pomocí MSAL. js.