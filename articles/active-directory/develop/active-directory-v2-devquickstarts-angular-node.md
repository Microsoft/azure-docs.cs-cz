---
title: Služba Azure AD v2.0 NodeJS AngularJS jednostránkové aplikace Začínáme | Microsoft Docs
description: Jak sestavit úhlová JS jednostránkové aplikace s přihlašováním uživatelů s i osobní účty Microsoft a pracovní nebo školní účty.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: d286aa33-8a94-452f-beb7-ddc6c6daa5c8
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/23/2017
ms.author: celested
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: c6f617c43cc5b4d471f3effb6f4a633604b8dde0
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157805"
---
# <a name="add-sign-in-to-an-angularjs-single-page-app---nodejs"></a>Přidejte přihlášení do AngularJS jednostránkové aplikace - NodeJS
V tomto článku přidáme přihlašují účty Microsoft používá technologii aplikace AngularJS pomocí koncového bodu v2.0 Azure Active Directory. koncový bod v2.0 umožňují provádět jeden integrace ve vaší aplikaci a ověřuje uživatele pomocí osobní i pracovní nebo školní účty.

Tato ukázka je jednoduchou aplikaci seznamu úkolů jednu stránku, která ukládá úkoly do back-end REST API, napsané v NodeJS a zabezpečit pomocí nosných tokenů OAuth z Azure AD. AngularJS aplikace bude používat naše knihovna JavaScript ověřování s otevřeným zdrojem [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js) ke zpracování celý proces přihlášení a získávat tokeny pro volání rozhraní REST API. Stejného vzoru lze použít k ověřování pro jiná rozhraní API REST, jako je třeba [Microsoft Graph](https://graph.microsoft.com) nebo rozhraní API Azure Resource Manager.

> [!NOTE]
> Ne všechny scénáře Azure Active Directory a funkce jsou podporovány koncového bodu v2.0. Pokud chcete zjistit, pokud byste měli používat koncový bod v2.0, přečtěte si informace o [v2.0 omezení](active-directory-v2-limitations.md).
> 
> 

## <a name="download"></a>Ke stažení
Abyste mohli začít, budete muset stáhnout a nainstalovat [node.js](https://nodejs.org). Potom můžete klonování nebo [Stáhnout](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-NodeJS/archive/skeleton.zip) kostru aplikace:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-NodeJS.git
```

Kostru aplikace zahrnuje všechny standardní kód pro jednoduchou aplikaci AngularJS, ale všechny součásti související s identity chybí. Pokud nechcete, aby se podle nich zorientujete, můžete místo toho klonovat nebo [Stáhnout](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-NodeJS/archive/complete.zip) je hotová ukázka.

```
git clone https://github.com/AzureADSamples/SinglePageApp-AngularJS-NodeJS.git
```

## <a name="register-an-app"></a>Registrace aplikace
Nejprve vytvořte aplikaci [portálu pro registraci aplikace](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), nebo postupujte podle těchto [podrobné kroky](active-directory-v2-app-registration.md). Zkontrolujte, že:

* Přidat **webové** platformu pro vaši aplikaci.
* Zadejte správný **identifikátor URI pro přesměrování**. Výchozí hodnota pro tato ukázka je `http://localhost:8080`.
* Ponechte **povolit implicitní tok** zaškrtávací políčko povoleno. 

Zkopírování **ID aplikace** přiřazené do vaší aplikace, budete ho potřebovat za chvíli. 

## <a name="install-adaljs"></a>Nainstalujte adal.js
Pokud chcete spustit, přejděte do projektu můžete stáhnout a nainstalovat adal.js. Pokud máte [bower](http://bower.io/) nainstalovaná, právě spuštěním tohoto příkazu. Pro žádné neshody verze závislosti vyberte právě vyšší verze.

```
bower install adal-angular#experimental
```

Alternativně můžete ručně stáhnout [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal.min.js) a [adal angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal-angular.min.js). Přidat oba soubory `app/lib/adal-angular-experimental/dist` adresáře.

Nyní otevřete projekt ve svém oblíbeném textovém editoru a načíst adal.js na konci obsahu stránky:

```html
<!--index.html-->

...

<script src="App/bower_components/dist/adal.min.js"></script>
<script src="App/bower_components/dist/adal-angular.min.js"></script>

...
```

## <a name="set-up-the-rest-api"></a>Nastavení rozhraní REST API
Když jste nastavujeme, umožní začít pracovat REST API back-end. V příkazovém řádku, nainstaluje všechny potřebné balíčky spuštěním (ujistěte se, zda pracujete v adresáři nejvyšší úrovně projektu):

```
npm install
```

Nyní otevřete `config.js` a nahraďte `audience` hodnotu:

```js
exports.creds = {

     // TODO: Replace this value with the Application ID from the registration portal
     audience: '<Your-application-id>',

     ...
}
```

Rozhraní REST API bude tato hodnota slouží k ověření tokeny, které obdrží z úhlová aplikace na požadavky AJAX. Všimněte si, že toto jednoduché rozhraní API REST ukládá data v paměti - takže každý čas stop pro server, budou ztraceny všechny dříve vytvořenou úlohy.

Je vždy, které vytvoříme zatěžovat hovoříte o tom, jak funguje rozhraní REST API. Nebojte se Vyvrtejte v kódu, ale pokud chcete získat další informace o zabezpečení webové rozhraní API s Azure AD, podívejte se na [v tomto článku](active-directory-v2-devquickstarts-node-api.md). 

## <a name="sign-users-in"></a>Přihlášení uživatelů
Čas napsat kód, identity. Možná jste již si všimli, že tento adal.js obsahuje poskytovatele AngularJS, který hraje vhodně s úhlová směrování mechanismy. Začněte přidáním modulu adal k aplikaci:

```js
// app/scripts/app.js

angular.module('todoApp', ['ngRoute','AdalAngular'])
.config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
 function ($routeProvider, $httpProvider, adalProvider) {

...
```

Nyní můžete inicializovat `adalProvider` s vaší aplikace ID:

```js
// app/scripts/app.js

...

adalProvider.init({

        // Use this value for the public instance of Azure AD
        instance: 'https://login.microsoftonline.com/', 

        // The 'common' endpoint is used for multi-tenant applications like this one
        tenant: 'common',

        // Your application id from the registration portal
        clientId: '<Your-application-id>',

        // If you're using IE, uncommment this line - the default HTML5 sessionStorage does not work for localhost.
        //cacheLocation: 'localStorage',

    }, $httpProvider);
```

Skvělé, teď adal.js má všechny informace, které je nutné zabezpečit vaši aplikaci a přihlášení uživatelů. Chcete-li vynutit přihlášení pro daný postup v aplikaci, jak dlouho trvá je jeden řádek kódu:

```js
// app/scripts/app.js

...

}).when("/TodoList", {
    controller: "todoListCtrl",
    templateUrl: "/static/views/TodoList.html",
    requireADLogin: true, // Ensures that the user must be logged in to access the route
})

...
```

Nyní když uživatel klikne na `TodoList` odkaz, adal.js automaticky přesměruje do služby Azure AD pro přihlášení v případě potřeby. Vyvoláním adal.js v řadičích můžete odeslat také explicitně požadavků na přihlášení a odhlášení:

```js
// app/scripts/homeCtrl.js

angular.module('todoApp')
// Load adal.js the same way for use in controllers and views   
.controller('homeCtrl', ['$scope', 'adalAuthenticationService','$location', function ($scope, adalService, $location) {
    $scope.login = function () {

        // Redirect the user to sign in
        adalService.login();

    };
    $scope.logout = function () {

        // Redirect the user to log out    
        adalService.logOut();

    };
...
```

## <a name="display-user-info"></a>Zobrazit informace o uživateli
Teď, když se uživatel přihlásí, budete pravděpodobně potřebovat pro přístup k datům ověřování přihlášeného uživatele ve vaší aplikaci. Adal.js zpřístupní tyto informace můžete v `userInfo` objektu. Chcete-li přístup k tomuto objektu v zobrazení, nejprve přidáte adal.js na kořenovém oboru odpovídající řadiče.

```js
// app/scripts/userDataCtrl.js

angular.module('todoApp')
// Load ADAL for use in view
.controller('userDataCtrl', ['$scope', 'adalAuthenticationService', function ($scope, adalService) {}]);
```

Pak můžete vyřešit přímo `userInfo` objekt ve svém zobrazení: 

```html
<!--app/views/UserData.html-->

...

    <!--Get the user's profile information from the ADAL userInfo object-->
    <tr ng-repeat="(key, value) in userInfo.profile">
        <td>{{key}}</td>
        <td>{{value}}</td>
    </tr>
...
```

Můžete také `userInfo` objektem pro určení, pokud se uživatel je přihlášen nebo ne.

```html
<!--index.html-->

...

    <!--Use the ADAL userInfo object to show the right login/logout button-->
    <ul class="nav navbar-nav navbar-right">
        <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
        <li><a class="btn btn-link" ng-hide="userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    </ul>
...
```

## <a name="call-the-rest-api"></a>Volání rozhraní REST API
Nakonec je čas získat některé tokeny a volání rozhraní REST API vytvářet, číst, aktualizovat a odstraňovat úkoly. A co s?  Není nutné provádět *co*. Adal.js se automaticky postará o získávání, ukládání do mezipaměti a aktualizaci tokeny. Je také postará o tyto tokeny se připojuje k odchozí požadavky AJAX, které odesílají do rozhraní REST API. 

Jak přesně to funguje? Je všechny díky magic z [AngularJS sběrače](https://docs.angularjs.org/api/ng/service/$http), což umožňuje adal.js k transformaci odchozí a příchozí zprávy http. Kromě toho adal.js předpokládá, že všechny žádosti odeslat do stejné domény jako okna měli používat tokeny, které jsou určené pro stejné ID aplikace jako aplikace AngularJS. Z tohoto důvodu jsme použili stejné ID aplikace v úhlová aplikace a rozhraní REST API NodeJS. Samozřejmě můžete toto chování potlačit a řekněte adal.js získat tokeny pro jiná rozhraní API REST v případě potřeby - ale pro tento scénář jednoduchého provede výchozí hodnoty.

Zde je fragment kódu, který ukazuje, jak snadné je k posílání požadavků s nosné tokeny z Azure AD:

```js
// app/scripts/todoListSvc.js

...
return $http.get('/api/tasks');
...
```

Blahopřejeme!  Integrované jednostránkové aplikace Azure AD je nyní dokončen. Pokračujte, proveďte předních. Ho můžete ověřovat uživatele, bezpečně volat jeho back-end pomocí OpenID Connect rozhraní REST API a získat základní informace o uživateli. Předinstalované podporuje každý uživatel s osobní Account Microsoft nebo pracovní nebo školní účet z Azure AD. Dejte aplikaci zkuste to spuštěním:

```
node server.js
```

V prohlížeči přejděte na `http://localhost:8080`. Přihlaste se pomocí osobního účtu Microsoft nebo pracovní nebo školní účet. Přidání úkolů do seznamu úkolů uživatele a odhlášení. Pokuste se přihlásit jiný typ účtu. Pokud potřebujete klienta Azure AD pro vytváření uživatelů pracovní nebo školní [zjistěte, jak získat tady](active-directory-howto-tenant.md) (je to zdarma).

A pokračujte ve čtení o koncový bod v2.0, přejděte zpět naše [Příručka vývojáře v2.0](active-directory-appmodel-v2-overview.md). Další zdroje projděte si:

* [Azure – ukázky z webu GitHub >>](https://github.com/Azure-Samples)
* [Azure AD na přetečení zásobníku >>](http://stackoverflow.com/questions/tagged/azure-active-directory)
* Dokumentace k Azure AD na [Azure.com >>](https://azure.microsoft.com/documentation/services/active-directory/)

## <a name="get-security-updates-for-our-products"></a>Získejte bezpečnostní aktualizace našich produktů
Doporučujeme vám získávat oznámení o bezpečnostních incidentech tak, že navštívíte [tuto stránku](https://technet.microsoft.com/security/dd252948) a přihlásíte se k odběru služby Security Advisory Alerts.

