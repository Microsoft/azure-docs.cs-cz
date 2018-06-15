---
title: Začínáme se službou Azure AD AngularJS | Microsoft Docs
description: Jak sestavit aplikaci jednostránkové AngularJS, který se integruje s Azure AD pro přihlášení a zavolá rozhraní API Azure AD chráněné pomocí OAuth.
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
ms.topic: article
ms.date: 11/30/2017
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5b99ce605d9ecea6c7d67ab9a2ea679d531787d7
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
ms.locfileid: "34156958"
---
# <a name="azure-ad-angularjs-getting-started"></a>Začínáme se službou Azure AD AngularJS

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Azure Active Directory (Azure AD) díky jednoduchá a přímočará můžete přidat přihlášení, odhlášení a zabezpečení rozhraní API OAuth zavolá na váš jednostránkové aplikace. Umožňuje, aby vaše aplikace k ověření uživatelů pomocí jejich účtů systému Windows Server Active Directory a využívat žádné webové rozhraní API, které Azure AD pomáhá chránit, jako je například rozhraní API Office 365 nebo rozhraní API služby Azure.

Pro aplikace JavaScript spuštěnému v prohlížeči Azure AD poskytuje službě Active Directory Authentication Library (ADAL), nebo adal.js. Jediný účel adal.js je snadno pro aplikaci, kterou chcete získat přístupové tokeny. K předvedení právě jak je snadné, zde jsme budete sestavit AngularJS seznam úkolů aplikaci, který:

* Přihlášení uživatele k aplikaci pomocí Azure AD jako zprostředkovatele identity.

* Zobrazí některé informace o uživateli.
* Bezpečně zavolá aplikace k provést seznamu API pomocí nosných tokenů z Azure AD.
* Přihlásí uživatele mimo aplikaci.

Chcete-li sestavit kompletní, funkční aplikaci, je potřeba:

1. Registrovat aplikaci s Azure AD.
2. ADAL nainstalujte a nakonfigurujte jednostránkové aplikace.
3. Zabezpečené stránky v jednostránkové aplikace pomocí knihovny ADAL.

Abyste mohli začít, [stáhnout kostru aplikace](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) nebo [stažení je hotová ukázka](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip). Musíte taky klient služby Azure AD, ve kterém můžete vytvořit uživatele a zaregistrovat aplikaci. Pokud ještě nemáte klienta, [zjistěte, jak získat](active-directory-howto-tenant.md).

## <a name="step-1-register-the-directorysearcher-application"></a>Krok 1: Registrace aplikace DirectorySearcher
Chcete-li aplikace k ověření uživatelů a získat tokeny, musíte nejprve zaregistrovat v klientovi služby Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Pokud jste přihlášeni do více adresářů, musíte zajistit, že si prohlížíte správném adresáři. Uděláte to tak, že na horním panelu klikněte na váš účet. V části **Directory** vyberte klienta Azure AD, kam chcete registrace vaší aplikace.
3. Klikněte na tlačítko **všechny služby** v levém podokně a potom vyberte **Azure Active Directory**.
4. Klikněte na tlačítko **registrace aplikace**a potom vyberte **přidat**.
5. Postupujte podle výzev a vytvořte novou webovou aplikaci nebo webové rozhraní API:
  * **Název** popisuje vaší aplikace pro uživatele.
  * **Adresa URL přihlašování** je umístění, do které Azure AD vrátí tokeny. Výchozí umístění pro tato ukázka je `https://localhost:44326/`.
6. Po dokončení registrace Azure AD jedinečný Identifikátor aplikace přiřadí vaší aplikace. Tuto hodnotu budete potřebovat v další části, zkopírujte jej na kartě aplikace.
7. Adal.js implicitního toku OAuth používá ke komunikaci s Azure AD. Implicitní tok musíte povolit pro aplikace:
  1. Klikněte na aplikaci a vyberte **Manifest** otevřete editor pro vložené manifestu.
  2. Vyhledejte `oauth2AllowImplicitFlow` vlastnost. Jeho hodnotu nastavte `true`.
  3. Klikněte na tlačítko **Uložit** uložit manifest.
8. Udělení oprávnění vašeho klienta pro vaši aplikaci. Přejděte na **nastavení** > **požadovaných oprávnění**a klikněte na **udělit oprávnění** tlačítko na horním panelu. Pro potvrzení klikněte na tlačítko **Ano**.

## <a name="step-2-install-adal-and-configure-the-single-page-app"></a>Krok 2: Instalace ADAL a konfigurace jednostránkové aplikace
Teď, když máte aplikaci ve službě Azure AD, můžete nainstalovat adal.js a zadejte kód, týkající se identity.

### <a name="configure-the-javascript-client"></a>Konfigurace klienta JavaScript
Začněte tím, že přidání adal.js do projektu TodoSPA pomocí konzoly Správce balíčků:
  1. Stáhněte si [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) a přidejte ho do `App/Scripts/` adresáři projektu.
  2. Stáhněte si [adal angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) a přidejte ho do `App/Scripts/` adresáři projektu.
  3. Načíst každý skript před koncem `</body>` v `index.html`:

    ```js
    ...
    <script src="App/Scripts/adal.js"></script>
    <script src="App/Scripts/adal-angular.js"></script>
    ...
    ```

### <a name="configure-the-back-end-server"></a>Konfigurace back-end serveru
Back-end pro jednostránkové aplikace back-end pro provést seznamu rozhraní API přijímat tokeny z prohlížeče, musí konfigurace informace o registraci aplikace. Otevřete v projektu TodoSPA `web.config`. Nahraďte hodnoty elementů v `<appSettings>` oddílu tak, aby odrážela hodnoty, které jste použili na webu Azure portal. Váš kód bude odkazovat tyto hodnoty vždy, když ho využívá ADAL.
  * `ida:Tenant` je doména klienta služby Azure AD – například contoso.onmicrosoft.com.
  * `ida:Audience` je ID klienta aplikace, který jste zkopírovali z portálu.

## <a name="step-3-use-adal-to-help-secure-pages-in-the-single-page-app"></a>Krok 3: Použití ADAL pomohou zabezpečené stránky v jednostránkové aplikace
Adal.js se integruje s AngularJS trasy a zprostředkovatele protokolu HTTP, můžete pomoct zabezpečené jednotlivých zobrazení v jednostránkové aplikace.

1. V `App/Scripts/app.js`, převeďte v modulu adal.js:

    ```js
    angular.module('todoApp', ['ngRoute','AdalAngular'])
    .config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
     function ($routeProvider, $httpProvider, adalProvider) {
    ...
    ```
2. Inicializace `adalProvider` také pomocí hodnoty konfigurace registraci vaší aplikace v `App/Scripts/app.js`:

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
3. Pomoc se zabezpečením `TodoList` zobrazení v aplikaci s použitím pouze jeden řádek kódu: `requireADLogin`.

    ```js
    ...
    }).when("/TodoList", {
            controller: "todoListCtrl",
            templateUrl: "/App/Views/TodoList.html",
            requireADLogin: true,
    ...
    ```

## <a name="summary"></a>Souhrn
Nyní máte zabezpečené jednostránkové aplikace, který se může přihlásit uživatele a vydat požadavky na chráněný token nosiče pro její rozhraní API back-end. Když uživatel klikne **TodoList** odkaz, adal.js automaticky přesměruje do služby Azure AD pro přihlášení v případě potřeby. Kromě toho adal.js automaticky připojí token přístupu pro všechny požadavky Ajax, které se odesílají do aplikace back-end. 

Předchozí kroky jsou úplné minimální nezbytné pro sestavení jednostránkové aplikace s použitím adal.js. Ale několik dalších funkcí jsou užitečné při jednostránkové aplikace:

* Chcete-li explicitně zasílání požadavků na přihlášení a odhlášení, můžete definovat funkce v řadičích, které vyvolají adal.js. V `App/Scripts/homeCtrl.js`:

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
* Můžete chtít prezentovat informace o uživateli v uživatelském rozhraní aplikace. ADAL služby již byla přidána do `userDataCtrl` řadiče, které umožní přístup `userInfo` objekt v přidružené zobrazení `App/Views/UserData.html`:

    ```js
    <p>{{userInfo.userName}}</p>
    <p>aud:{{userInfo.profile.aud}}</p>
    <p>iss:{{userInfo.profile.iss}}</p>
    ...
    ```

* Existuje mnoho scénářů, ve kterých budete chtít vědět, pokud je uživatel přihlášený nebo ne. Můžete také `userInfo` objektu o shromáždění těchto informací. Například v `index.html`, můžete zobrazit buď **přihlášení** nebo **odhlášení** tlačítko na základě ověření stavu:

    ```js
    <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
    <li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    ```

Jednostránkové aplikace Azure integrované s Active Directory můžete ověřovat uživatele, bezpečně volat jeho back-end pomocí standardu OAuth 2.0 a získat základní informace o uživateli. Pokud jste to ještě neudělali, nyní je čas k naplnění vašeho klienta s některými uživateli. Spuštění jednostránkové aplikace seznam úkolů a přihlaste se pomocí jeden z těchto uživatelů. Přidání úkolů do seznamu úkolů uživatele, odhlásit se a přihlaste se zpět.

Adal.js usnadňuje začlenit běžné funkce identity do vaší aplikace. Postará všechny dirty činností, které pro vás: Správa mezipaměti, podpora protokolu OAuth, uživatel prezentuje přihlášení uživatelského rozhraní, aktualizace vypršení platnosti tokenů a další.

Pro srovnání je hotová ukázka (bez vašich hodnot nastavení) k dispozici v [Githubu](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).

## <a name="next-steps"></a>Další postup
Nyní se můžete přesunout dalších scénářů. Můžete chtít zkuste: [volání CORS webového rozhraní API z jednostránkové aplikace](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
