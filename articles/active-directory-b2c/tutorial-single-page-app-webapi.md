---
title: 'Kurz: Udělení přístupu k webovému rozhraní API ASP.NET Core z jednostránkové aplikace'
titleSuffix: Azure AD B2C
description: V tomto kurzu se dozvíte, jak pomocí služby Active Directory B2C chránit webové rozhraní API .NET Core a volat rozhraní API z jednostránkové aplikace Node.js.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 07/24/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: f6f9ff7bb0d504ecc163f6ce1f87477b1ea9c2d1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78186134"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>Kurz: Udělení přístupu k webovému rozhraní API ASP.NET Core z jednostránkové aplikace pomocí služby Azure Active Directory B2C

Tento kurz ukazuje, jak volat prostředek ASP.NET webového rozhraní API chráněného službou Azure Active Directory B2C (Azure AD B2C) z jednostránkové aplikace.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání aplikace webového rozhraní API
> * Konfigurace oborů webového rozhraní API
> * Udělení oprávnění webovému rozhraní API
> * Konfigurace ukázky pro použití aplikace

## <a name="prerequisites"></a>Požadavky

* V kurzu proveďte kroky a [předpoklady: Povolte ověřování v jednostránkové aplikaci pomocí služby Azure Active Directory B2C](tutorial-single-page-app.md).
* Visual Studio 2019 nebo novější, nebo Visual Studio Code
* .NET Core 2.2 nebo novější
* Node.js

## <a name="add-a-web-api-application"></a>Přidání aplikace webového rozhraní API

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Konfigurace oborů

Obory poskytují způsob řízení přístupu k chráněným prostředkům. Webové rozhraní API používá obory k implementaci řízení přístupu na základě oboru. Někteří uživatelé například můžou mít oprávnění ke čtení i zápisu, zatímco jiní uživatelé můžou mít oprávnění jen pro čtení. V tomto kurzu definujete oprávnění ke čtení i zápisu pro webové rozhraní API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Zaznamenejte hodnotu `demo.read` v rámci **scopes** pro obor použít v pozdějším kroku při konfiguraci jednostránkové aplikace. Hodnota celého oboru `https://contosob2c.onmicrosoft.com/api/demo.read`je podobná hodnotě .

## <a name="grant-permissions"></a>Udělení oprávnění

Chcete-li volat chráněné webové rozhraní API z jiné aplikace, musíte této aplikaci udělit oprávnění k webovému rozhraní API.

V požadovaném kurzu jste vytvořili webovou aplikaci s názvem *webapp1*. V tomto kurzu nakonfigurujete tuto aplikaci tak, aby volala webové rozhraní API, které jste vytvořili v předchozí části *webapi1*.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Jednostránková webová aplikace je zaregistrována pro volání chráněného webového rozhraní API. Uživatel se ověřuje pomocí Azure AD B2C a použije jednostránkovou aplikaci. Jednostránková aplikace získá udělení autorizace od Azure AD B2C pro přístup k chráněnému webovému rozhraní API.

## <a name="configure-the-sample"></a>Konfigurace ukázky

Teď, když je webové rozhraní API registrované a máte definované obory, nakonfigurujete kód webového rozhraní API tak, aby používal vašeho klienta Azure AD B2C. V tomto kurzu nakonfigurujete ukázkovou webovou aplikaci .NET Core, kterou stáhnete z GitHubu.

[Stáhněte \*si archiv ZIP](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip) nebo naklonujte ukázkový projekt webového rozhraní API z GitHubu.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>Konfigurace webového rozhraní API

1. Otevřete soubor <em>B2C-WebApi/**appsettings.json** </em> v sadě Visual Studio nebo Visual Studio Code.
1. Upravte `AzureAdB2C` blok tak, aby odrážel název vašeho klienta, ID aplikace webového rozhraní API, název zásady registrace/přihlášení a obory, které jste definovali dříve. Blok by měl vypadat podobně jako `Tenant` v `ClientId` následujícím příkladu (s příslušnými a hodnotami):

    ```json
    "AzureAdB2C": {
      "Tenant": "<your-tenant-name>.onmicrosoft.com",
      "ClientId": "<webapi-application-ID>",
      "Policy": "B2C_1_signupsignin1",

      "ScopeRead": "demo.read",
      "ScopeWrite": "demo.write"
    },
    ```

#### <a name="enable-cors"></a>Povolení CORS

Chcete-li povolit jednostránkové aplikaci volat ASP.NET základní webové rozhraní API, musíte povolit [CORS](https://docs.microsoft.com/aspnet/core/security/cors) ve webovém rozhraní API.

1. V souboru *Startup.cs* přidejte CORS do metody `ConfigureServices()`.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddCors();
    ```

1. Také v `ConfigureServices()` rámci metody `jwtOptions.Authority` nastavte hodnotu na následující identifikátor URI vystavitvystavitetele tokenu.

    Nahraďte `<your-tenant-name>` se názvem vašeho klienta B2C.

    ```csharp
    jwtOptions.Authority = $"https://<your-tenant-name>.b2clogin.com/{Configuration["AzureAdB2C:Tenant"]}/{Configuration["AzureAdB2C:Policy"]}/v2.0";
    ```

1. V `Configure()` metodě nakonfigurujte CORS.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        app.UseCors(builder =>
            builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

1. (Pouze visual studio) V **části Vlastnosti** v Průzkumníku řešení otevřete soubor *launchSettings.json* a najděte `iisExpress` blok.
1. (Pouze visual studio) Aktualizujte `applicationURL` hodnotu číslem portu, které jste zadali při registraci aplikace *webapi1* v předchozím kroku. Například:

    ```json
    "iisExpress": {
      "applicationUrl": "http://localhost:5000/",
      "sslPort": 0
    }
    ```

### <a name="configure-the-single-page-application"></a>Konfigurace jednostránkové aplikace

Jednostránková aplikace (SPA) z [předchozího kurzu](tutorial-single-page-app.md) v řadě používá Azure AD B2C pro registraci a přihlášení uživatele a volá ASP.NET core webové rozhraní API chráněné ukázkovým tenantem *frabrikamb2c.*

V této části aktualizujete jednostránkovou aplikaci tak, aby volala webové rozhraní API ASP.NET Core chráněné *vaším* tenantem Azure AD B2C a které spouštěte v místním počítači.

Změna nastavení ve spa:

1. Otevřete soubor *index.html* v projektu [active-directory-b2c-javascript-msal-singlepageapp,][github-js-spa] který jste stáhli nebo naklonovali v předchozím kurzu.
1. Nakonfigurujte ukázku pomocí identifikátoru URI pro dříve vytvořený obor *demo.read* a adresu URL webového rozhraní API.
    1. V `appConfig` definici nahraďte hodnotu `b2cScopes` úplným identifikátorem URI pro obor (hodnota **SCOPE,** kterou jste zaznamenali dříve).
    1. Změňte `webApi` hodnotu na identifikátor URI přesměrování, který jste přidali při registraci webové aplikace rozhraní API v předchozím kroku.

    Definice `appConfig` by měla vypadat podobně jako následující blok kódu (s názvem klienta `<your-tenant-name>`v místě ):

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var appConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/"
    };
    ```

## <a name="run-the-spa-and-web-api"></a>Spuštění spa a webového rozhraní API

Nakonec spustíte ASP.NET základní webové rozhraní API a jednostránkovou aplikaci Node.js v místním počítači. Potom se přihlásíte k jednostránkové aplikaci a stisknutím tlačítka zahájíte požadavek na chráněné rozhraní API.

I když obě aplikace spustit místně v tomto kurzu, které používají Azure AD B2C pro zabezpečené registrace nebo přihlášení a udělit přístup k chráněné webové rozhraní API.

### <a name="run-the-aspnet-core-web-api"></a>Spuštění webového rozhraní API ASP.NET Core

V sadě Visual Studio stisknutím **klávesy F5** vytvořte a laděte řešení *B2C-WebAPI.sln.* Při spuštění projektu se ve výchozím prohlížeči zobrazí webová stránka oznamující, že webové rozhraní API je k dispozici pro požadavky.

Pokud dáváte přednost `dotnet` použití vykreslování-li to místo Visual Studio:

1. Otevřete okno konzoly a změňte adresář obsahující soubor * \*.csproj.* Například:

    `cd active-directory-b2c-dotnetcore-webapi/B2C-WebApi`

1. Vytvořte a spusťte `dotnet run`webové rozhraní API spuštěním .

    Když je rozhraní API v provozu, měli byste vidět výstup podobný následujícímu `NETSDK1059` (pro kurz můžete bezpečně ignorovat všechna upozornění):

    ```console
    $ dotnet run
    Hosting environment: Production
    Content root path: /home/user/active-directory-b2c-dotnetcore-webapi/B2C-WebApi
    Now listening on: http://localhost:5000
    Application started. Press Ctrl+C to shut down.
    ```

### <a name="run-the-single-page-app"></a>Spuštění jednostránkové aplikace

1. Otevřete okno konzoly a změňte adresář obsahující ukázku Node.js. Například:

    `cd active-directory-b2c-javascript-msal-singlepageapp`

1. Spusťte následující příkazy:

    ```console
    npm install && npm update
    node server.js
    ```

    V okně konzoly se zobrazí číslo portu, kde je aplikace hostována.

    ```console
    Listening on port 6420...
    ```

1. Chcete-li aplikaci zobrazit, přejděte `http://localhost:6420` v prohlížeči.
1. Přihlaste se pomocí e-mailové adresy a hesla, které jste použili v [předchozím kurzu](tutorial-single-page-app.md). Po úspěšném přihlášení, `User 'Your Username' logged-in` měli byste vidět zprávu.
1. Vyberte tlačítko **Rozhraní API pro volání.** Spa získá udělení autorizace od Azure AD B2C, pak přistupuje k chráněné webové rozhraní API k zobrazení obsahu jeho index stránky:

    ```Output
    Web APi returned:
    "<html>\r\n<head>\r\n  <title>Azure AD B2C API Sample</title>\r\n ...
    ```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Přidání aplikace webového rozhraní API
> * Konfigurace oborů webového rozhraní API
> * Udělení oprávnění webovému rozhraní API
> * Konfigurace ukázky pro použití aplikace

Teď, když jste viděli SPA požádat o prostředek z chráněného webového rozhraní API, získat hlubší představu o tom, jak tyto typy aplikací vzájemně spolupracují a s Azure AD B2C.

> [!div class="nextstepaction"]
> [Typy aplikací, které lze použít ve službě Active Directory B2C >](application-types.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
