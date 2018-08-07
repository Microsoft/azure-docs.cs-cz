---
title: Začínáme se službou Azure AD AngularJS | Dokumentace Microsoftu
description: Postup pro sestavení jednostránková aplikace AngularJS, která se integruje s Azure AD pro přihlášení a volá rozhraní API Azure AD chráněné pomocí OAuth.
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
ms.openlocfilehash: 0c7f6a0e447e3b48cdd1df684dc105ece1e98f66
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39581267"
---
# <a name="azure-ad-angularjs-getting-started"></a>Začínáme se službou Azure AD AngularJS

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Azure Active Directory (Azure AD) umožňuje jednoduché a nekomplikované pro přidání OAuth pro přihlášení, odhlášení a zabezpečená rozhraní API volá do jednostránkové aplikace. To umožňuje aplikacím provádět ověřování uživatelů pomocí jejich účtů systému Windows Server Active Directory a využívat všechny webové rozhraní API, které Azure AD k ochraně, jako je například rozhraní API Office 365 nebo rozhraní API služby Azure.

Pro jazyk JavaScript aplikace spuštěné v prohlížeči Azure AD poskytuje Active Directory Authentication Library (ADAL), nebo adal.js. Jediným účelem adal.js je k tomu, aby pro vaši aplikaci k získání přístupových tokenů. Abychom si předvedli, jak snadné je, tady vytvoříme aplikace AngularJS seznam úkolů, které:

* Přihlášení uživatele k aplikaci pomocí Azure AD jako zprostředkovatele identity.

* Zobrazí některé informace o uživateli.
* Bezpečně volá aplikace chcete provést seznamu rozhraní API pomocí nosných tokenů z Azure AD.
* Uživatel z aplikace odhlásí.

Pokud chcete vytvořit kompletní, funkční aplikaci, budete muset:

1. Registrace aplikace v Azure AD.
2. ADAL instalace a konfigurace jednostránkové aplikace.
3. Zabezpečené stránky v jednostránkové aplikace pomocí knihovny ADAL.

Abyste mohli začít, [stáhnout kostru aplikace](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) nebo [stáhnout úplnou vzorovou](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip). Budete také potřebovat tenanta služby Azure AD, ve kterém můžete vytvářet uživatele a zaregistrovat aplikaci. Pokud ještě nemáte tenanta, [zjistěte, jak získat](quickstart-create-new-tenant.md).

## <a name="step-1-register-the-directorysearcher-application"></a>Krok 1: Registrace aplikace DirectorySearcher
Pokud chcete povolit své aplikace ověřovat uživatele a získat tokeny, musíte nejprve zaregistrovat ve vašem tenantovi Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Pokud jste přihlášeni k více adresářů, budete muset zajistit, aby že se vám zobrazuje na správný adresář. Provedete to tak, že na horním panelu klikněte na svůj účet. V části **Directory** , zvolte tenanta Azure AD, ve které chcete zaregistrovat aplikaci.
3. Klikněte na tlačítko **všechny služby** v levém podokně a pak vyberte **Azure Active Directory**.
4. Klikněte na tlačítko **registrace aplikací**a pak vyberte **přidat**.
5. Postupujte podle zobrazených výzev a vytvořte novou webovou aplikaci nebo webové rozhraní API:
  * **Název** popíše aplikaci uživatelům.
  * **Adresa URL přihlašování** je umístění, na který Azure AD vrátí tokeny. Výchozí umístění pro tuto ukázku je `https://localhost:44326/`.
6. Po dokončení registrace Azure AD a jedinečné ID přiřadí vaší aplikace. Tuto hodnotu budete potřebovat v následujících částech, proto zkopírujte ho na kartě aplikace.
7. Implicitní tok OAuth Adal.js používá ke komunikaci s Azure AD. Je nutné povolit implicitní tok pro vaši aplikaci:
  1. Klikněte na aplikaci a vyberte **Manifest** otevřete editor manifestu vložené.
  2. Vyhledejte `oauth2AllowImplicitFlow` vlastnost. Nastavte jej na hodnotu `true`.
  3. Klikněte na tlačítko **Uložit** uložit manifest.
8. Udělení oprávnění napříč vašeho tenanta pro vaši aplikaci. Přejděte na **nastavení** > **požadovaná oprávnění**a klikněte na tlačítko **udělit oprávnění** tlačítko na horním panelu. Pro potvrzení klikněte na tlačítko **Ano**.

## <a name="step-2-install-adal-and-configure-the-single-page-app"></a>Krok 2: Instalace ADAL a konfigurace jednostránkové aplikace
Teď, když máte aplikaci ve službě Azure AD, můžete nainstalovat adal.js a napsat kód souvisejícím s identitou.

### <a name="configure-the-javascript-client"></a>Konfigurace klienta jazyka JavaScript
Začněte tím, že přidání adal.js TodoSPA projektu pomocí konzole Správce balíčků:
  1. Stáhněte si [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) a přidejte ji tak `App/Scripts/` adresář projektu.
  2. Stáhnout [adal angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) a přidejte ji tak `App/Scripts/` adresář projektu.
  3. Načíst každý skript do konce `</body>` v `index.html`:

    ```js
    ...
    <script src="App/Scripts/adal.js"></script>
    <script src="App/Scripts/adal-angular.js"></script>
    ...
    ```

### <a name="configure-the-back-end-server"></a>Konfigurace back-end serveru
Back-endu pro jednostránkové aplikace back-end na proveďte seznamu rozhraní API přijímat tokeny z prohlížeče, musí Konfigurace informací o registraci aplikace. Otevřete v projektu TodoSPA `web.config`. Nahraďte hodnoty prvků v `<appSettings>` části tak, aby odrážely hodnoty, které jste použili na webu Azure Portal. Váš kód bude odkazovat na tyto hodnoty pokaždé, když se používá ADAL.
  * `ida:Tenant` je doména tenanta Azure AD – například contoso.onmicrosoft.com.
  * `ida:Audience` je ID klienta aplikace, které jste zkopírovali z portálu.

## <a name="step-3-use-adal-to-help-secure-pages-in-the-single-page-app"></a>Krok 3: Použití knihovny ADAL na zabezpečené stránky v jednostránkové aplikaci nápovědy
Adal.js integruje s AngularJS trasy a zprostředkovatele protokolu HTTP, mohli pomoct zabezpečení jednotlivých zobrazení v jednostránkové aplikace.

1. V `App/Scripts/app.js`, načtení modulu adal.js:

    ```js
    angular.module('todoApp', ['ngRoute','AdalAngular'])
    .config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
     function ($routeProvider, $httpProvider, adalProvider) {
    ...
    ```
2. Inicializovat `adalProvider` také pomocí hodnoty konfigurace vaší registrace aplikace v `App/Scripts/app.js`:

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
Teď máte zabezpečené jednostránkovou aplikaci, která může přihlašování uživatelů a zasílání požadavků na chráněný token nosiče k jeho back endového rozhraní API. Když uživatel klikne **TodoList** odkaz, adal.js automaticky přesměruje do služby Azure AD pro přihlášení v případě potřeby. Kromě toho adal.js automaticky připojí přístupového tokenu do všech Ajax požadavků, které se odesílají do back-endu aplikace. 

V předchozích krocích jsou úplné minimum nutné pro sestavení jednostránkové aplikace s použitím adal.js. Ale několik dalších funkcí jsou užitečné pro jednostránkovou aplikaci:

* Pokud chcete explicitně zasílání požadavků na přihlášení a odhlášení, můžete definovat funkce ve vašich kontrolerech, které vyvolají adal.js. V `App/Scripts/homeCtrl.js`:

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
* Můžete chtít zobrazit informace o uživateli v Uživatelském rozhraní aplikace. ADAL služby již byla přidána do `userDataCtrl` kontroleru, aby měli přístup k `userInfo` objektu v přidružené zobrazení `App/Views/UserData.html`:

    ```js
    <p>{{userInfo.userName}}</p>
    <p>aud:{{userInfo.profile.aud}}</p>
    <p>iss:{{userInfo.profile.iss}}</p>
    ...
    ```

* Existuje mnoho scénářů, ve kterých budete chtít vědět, pokud je uživatel přihlášený, nebo ne. Můžete také použít `userInfo` objekt ke shromažďování těchto informací. Například v `index.html`, můžete zobrazit buď **přihlášení** nebo **odhlášení** tlačítko na základě ověřování stavu:

    ```js
    <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
    <li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    ```

Jednostránkové aplikace Azure integrované s Active Directory můžete ověřovat uživatele, bezpečně volat back-endu pomocí standardu OAuth 2.0 a získat základní informace o uživateli. Pokud jste tak dosud neučinili, teď je čas k naplnění vašeho tenanta s některými uživateli. Spuštění jednostránkové aplikace seznam úkolů a přihlaste se pomocí jedné z těchto uživatelů. Přidání úkolů do seznamu úkolů uživatele odhlásit a zase přihlásit.

Adal.js umožňuje snadno začlenit do aplikace běžné funkce identity. Ho za vás postará o všechnu práci čistý: Správa mezipaměti, podporu protokolu OAuth, nabízí ten samý uživatel s přihlašovací uživatelské rozhraní, aktualizace vypršení platnosti tokenů a další.

Pro srovnání je hotová ukázka (bez vašich hodnot nastavení) je k dispozici v [Githubu](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).

## <a name="next-steps"></a>Další postup
Se teď můžete přejít k další scénáře. Můžete zkusit: [volání CORS webového rozhraní API z jednostránkové aplikace](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
