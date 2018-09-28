---
title: Vytvoření jednostránková aplikace AngularJS pro přihlašování a odhlašování pomocí Azure Active Directory | Microsoft Docs
description: Zjistěte, jak vytvořit jednostránkovou aplikaci AngularJS, která se integruje s Azure AD pro přihlašování a s využitím OAuth volá rozhraní API chráněná pomocí Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: f2991054-8146-4718-a5f7-59b892230ad7
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 23912f9d004d051c422f93e8b10f1aa6cb8b2626
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46959491"
---
# <a name="quickstart-build-an-angularjs-single-page-app-for-sign-in-and-sign-out-with-azure-active-directory"></a>Rychlý start: Vytvoření jednostránková aplikace AngularJS pro přihlašování a odhlašování pomocí Azure Active Directory

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Azure Active Directory (Azure AD) vám umožňuje snadno a jasně přidat do jednostránkových aplikací volání API pro přihlášení, odhlášení a zabezpečené OAuth. Umožňuje aplikacím ověřovat uživatele pomocí jejich účtů Windows Server Active Directory a využívat všechna webová rozhraní API, která Azure AD pomáhá chránit, například rozhraní API Office 365 nebo rozhraní API Azure.

Pro javascriptové aplikace spuštěné v prohlížeči Azure AD poskytuje Active Directory Authentication Library (ADAL) neboli adal.js. Jediným účelem adal.js je usnadnit aplikaci získání přístupových tokenů.

V tomto rychlém startu se naučíte vytvořit aplikaci AngularJS pro seznam úkolů, která:

* Přihlásí uživatele k aplikaci pomocí Azure AD jako zprostředkovatel identity.
* Zobrazí některé informace o uživateli.
* Bezpečně volá API seznamu úkolů aplikace pomocí nosných tokenů z Azure AD.
* Odhlásí uživatele z aplikace.

Pokud chcete sestavit úplnou funkční aplikaci, budete k tomu potřebovat:

1. Zaregistrovat aplikaci v Azure AD
2. Nainstalovat ADAL a konfigurovat jednostránkovou aplikaci
3. Použít ADAL k zabezpečení stránek v jednostránkové aplikaci

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, potřebujete:

* Stáhnout si [kostru aplikace](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) nebo [úplnou ukázku](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).
* Tenanta služby Azure AD, ve kterém můžete vytvářet uživatele a zaregistrovat aplikaci. Pokud ještě tenanta nemáte, [zjistěte, jako ho získat](quickstart-create-new-tenant.md).

## <a name="step-1-register-the-directorysearcher-application"></a>Krok 1: Registrace aplikace DirectorySearcher

Pokud chcete aplikaci umožnit ověřování uživatelů a získání tokenů, musíte ji nejdřív zaregistrovat ve vašem tenantovi Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Pokud jste přihlášení k více adresářům, měli byste zajistit, že máte zobrazený správný adresář. Provedete to tak, že na horním panelu kliknete na svůj účet. Ze seznamu **Adresář** vyberte tenanta Azure AD, do kterého chcete aplikaci zaregistrovat.
1. V levém podokně klikněte na **Všechny služby** a pak vyberte **Azure Active Directory**.
1. Klikněte na **Registrace aplikací** a pak vyberte **Přidat**.
1. Postupujte podle zobrazených výzev a vytvořte novou webovou aplikaci nebo webové rozhraní API:

    * **Název** popisuje aplikaci uživatelům.
    * **Přihlašovací adresa URL** je umístění, kam bude Azure AD vracet tokeny. Výchozí umístění pro tuto ukázku je `https://localhost:44326/`.

1. Po dokončení registrace přiřadí Azure AD aplikaci jedinečné ID aplikace. Tuto hodnotu budete potřebovat v následujících částech, proto si ji z karty aplikace zkopírujte.
1. Adal.js používá implicitní tok OAuth ke komunikaci s Azure AD. Implicitní tok musíte pro vaši aplikaci aktivovat:

    1. Klikněte na aplikaci a vyberte **Manifest**. Tím otevřete vložený editor manifestu.
    1. Najděte vlastnost `oauth2AllowImplicitFlow`. Nastavte její hodnotu na `true`.
    1. Kliknutím na **Uložit** uložte manifest.

1. Udělte oprávnění pro aplikaci napříč vaším tenantem. Přejděte na **Nastavení > Požadovaná oprávnění** a na horním panelu vyberte tlačítko **Udělit oprávnění**.
1. Výběrem **Ano** potvrďte.

## <a name="step-2-install-adal-and-configure-the-single-page-app"></a>Krok 2: Instalace ADALu a konfigurace jednostránkové aplikace

Když už máte v Azure AD aplikaci, můžete nainstalovat knihovnu adal.js a zapsat svůj kód související s identitou.

### <a name="configure-the-javascript-client"></a>Konfigurace javascriptového klienta

Začněte tím, že přidáte adal.js do projektu TodoSPA pomocí konzoly Správce balíčků:

1. Stáhněte si [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) a přidejte ho do adresáře projektu `App/Scripts/`.
2. Stáhněte si [adal-angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) a přidejte ho do adresáře projektu `App/Scripts/`.
3. Načtěte každý skript před konec `</body>` v `index.html`:

    ```js
    ...
    <script src="App/Scripts/adal.js"></script>
    <script src="App/Scripts/adal-angular.js"></script>
    ...
    ```

### <a name="configure-the-back-end-server"></a>Konfigurace back-endového serveru

Aby API seznamu úkolů back-endu jednostránkové aplikace přijímalo tokeny z prohlížeče, potřebuje back-end konfigurační informace o registraci aplikace. V projektu TodoSPA otevřete `web.config`. Nahraďte hodnoty prvků v části `<appSettings>` tak, aby odrážely hodnoty, které jste použili na portálu Azure Portal. Váš kód bude tyto hodnoty využívat vždy, když bude používat ADAL.

   * `ida:Tenant` je doména tenanta Azure AD – například contoso.onmicrosoft.com.
   * `ida:Audience` je ID klienta vaší aplikace, které jste zkopírovali z portálu.

## <a name="step-3-use-adal-to-help-secure-pages-in-the-single-page-app"></a>Krok 3: Použití ADALu k zabezpečení stránek v jednostránkové aplikaci

Adal.js se integruje s trasou AngularJS a zprostředkovateli protokolu HTTP, abyste mohli pomáhat zabezpečit jednotlivá zobrazení v jednostránkové aplikaci.

1. V `App/Scripts/app.js` zaveďte modul adal.js:

    ```js
    angular.module('todoApp', ['ngRoute','AdalAngular'])
    .config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
     function ($routeProvider, $httpProvider, adalProvider) {
    ...
    ```
2. V `App/Scripts/app.js` také inicializujte `adalProvider` pomocí konfiguračních hodnot vaší registrace aplikace:

    ```js
    adalProvider.init(
      {
          instance: 'https://login.microsoftonline.com/',
          tenant: 'Enter your tenant name here e.g. contoso.onmicrosoft.com',
          clientId: 'Enter your client ID here e.g. e9a5a8b6-8af7-4719-9821-0deef255f68e',
          extraQueryParameter: 'nux=1',
          //cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
      },
      $httpProvider
    );
    ```
3. Pomozte zabezpečit zobrazení `TodoList` v aplikaci pomocí jenom jednoho řádku kódu: `requireADLogin`.

    ```js
    ...
    }).when("/TodoList", {
            controller: "todoListCtrl",
            templateUrl: "/App/Views/TodoList.html",
            requireADLogin: true,
    ...
    ```

## <a name="summary"></a>Souhrn

Teď máte zabezpečenou jednostránkovou aplikaci, která může přihlašovat uživatele a vydávat požadavky chráněné nosnými tokeny pro své back-endové rozhraní API. Když uživatel klikne na odkaz **TodoList**, adal.js se v případě potřeby automaticky přesměruje do Azure AD pro přihlášení. Kromě toho adal.js automaticky připojí přístupový token ke vše požadavkům Ajax, které se odesílají do back-endu aplikace.

Předchozí kroky jsou naprostým minimem nutným pro vytvoření jednostránkové aplikace pomocí adal.js. V jednostránkové aplikaci se ale hodí několik dalších funkcí:

* Pokud chcete explicitně vydávat požadavky na přihlášení a odhlášení, můžete definovat funkce ve vašich kontrolerech, které vyvolají adal.js. V `App/Scripts/homeCtrl.js`:

    ```js
    ...
    $scope.login = function () {
        adalService.login();
    };
    $scope.logout = function () {
        adalService.logOut();
    };
    ...
    ```
* Možná budete chtít zobrazit informace o uživateli v uživatelském rozhraní aplikace. Služba ADAL už je přidaná do kontroleru `userDataCtrl`, takže máte přístup k objektu `userInfo` v přidruženém zobrazení `App/Views/UserData.html`:

    ```js
    <p>{{userInfo.userName}}</p>
    <p>aud:{{userInfo.profile.aud}}</p>
    <p>iss:{{userInfo.profile.iss}}</p>
    ...
    ```

* Existuje mnoho scénářů, ve kterých budete chtít vědět, jestli je uživatel přihlášený, nebo ne. Tyto informace můžete získat také pomocí objektu `userInfo`. Například v `index.html` můžete zobrazit buď tlačítko **Přihlásit se** nebo **Odhlásit se** na základě stavu ověření:

    ```js
    <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
    <li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    ```

Jednostránková aplikace integrovaná s Azure AD může ověřovat uživatele, bezpečně volat svůj back-end pomocí OAuth 2.0 a získat základní informace o uživateli. Pokud jste to ještě neudělali, je teď čas naplnit tenanta několika uživateli. Spusťte jednostránkovou aplikaci seznamu úkolů a přihlaste se pomocí jednoho z těchto uživatelů. Přidejte úkoly do uživatelova seznamu úkolů, odhlaste se a zase se přihlaste.

Adal.js zahrnutí běžných funkcí identity do aplikace usnadňuje. Udělá za vás všechnu tu náročnou práci, jako je správa mezipaměti, podpora protokolu OAuth, zobrazení uživatelského rozhraní pro přihlášení, aktualizace tokenů s vypršenou platností a další úkony.

Pro srovnání je hotová ukázka (bez vašich hodnot nastavení) k dispozici na [GitHubu](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).

## <a name="next-steps"></a>Další kroky

Můžete teď přejít na další scénáře.

> [!div class="nextstepaction"]
> [Volání webového rozhraní API CORS z jednostránkové aplikace](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet)