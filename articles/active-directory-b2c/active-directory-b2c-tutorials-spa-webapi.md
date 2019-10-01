---
title: Kurz – udělení přístupu k ASP.NET Core webovému rozhraní API z jednostránkové aplikace – Azure Active Directory B2C
description: Naučte se používat Active Directory B2C k ochraně webového rozhraní API .NET Core a volání rozhraní API z jediné stránky aplikace Node. js.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 07/24/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: f940893a5328db65549b40269578399655f8539e
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679273"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>Kurz: poskytnutí přístupu k ASP.NET Core webovému rozhraní API z jednostránkové aplikace pomocí Azure Active Directory B2C

V tomto kurzu se dozvíte, jak volat ASP.NET Core prostředku webového rozhraní API chráněného Azure Active Directory B2C (Azure AD B2C) z jednostránkové aplikace.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání aplikace webového rozhraní API
> * Konfigurace oborů pro webové rozhraní API
> * Udělení oprávnění webovému rozhraní API
> * Konfigurace ukázky pro použití aplikace

## <a name="prerequisites"></a>Požadavky

* Proveďte kroky a požadavky v [kurzu: povolení ověřování v aplikaci s jednou stránkou pomocí Azure Active Directory B2C](active-directory-b2c-tutorials-spa.md).
* Visual Studio 2019 nebo novější nebo Visual Studio Code
* .NET Core 2,2 nebo novější
* Node.js

## <a name="add-a-web-api-application"></a>Přidání aplikace webového rozhraní API

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Konfigurace oborů

Obory poskytují způsob, jak řídit přístup k chráněným prostředkům. K implementaci řízení přístupu na základě oboru používá webové rozhraní API obory. Někteří uživatelé například můžou mít přístup pro čtení i zápis, zatímco jiní uživatelé mohou mít oprávnění jen pro čtení. V tomto kurzu definujete pro webové rozhraní API oprávnění ke čtení i zápisu.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Poznamenejte si **úplnou hodnotu oboru** `demo.read` pro použití v pozdějším kroku při konfiguraci jednostránkové aplikace. Úplná hodnota oboru je podobná `https://yourtenant.onmicrosoft.com/api/demo.read`.

## <a name="grant-permissions"></a>Udělit oprávnění

Chcete-li volat chráněné webové rozhraní API z jiné aplikace, je třeba udělit oprávnění aplikace webovému rozhraní API.

V kurzu požadavků jste vytvořili webovou aplikaci s názvem *WebApp1*. V tomto kurzu nakonfigurujete tuto aplikaci tak, aby volala webové rozhraní API, které jste vytvořili v předchozí části, *webapi1*.

1. V Azure Portal přejděte do svého tenanta B2C
1. Vyberte **aplikace**a pak vyberte *WebApp1*.
1. Vyberte **přístup přes rozhraní API**a pak vyberte **Přidat**.
1. V rozevíracím seznamu **Vyberte rozhraní API** vyberte možnost *webapi1*.
1. V rozevíracím seznamu **Vybrat obory** vyberte obory, které jste definovali dříve. Například *demo. Read* a *demo. Write*.
1. Vyberte **OK**.

Webová aplikace s jednou stránkou je zaregistrovaná pro volání chráněného webového rozhraní API. Uživatel se ověřuje pomocí Azure AD B2C pro použití jednostránkové aplikace. Jednostránkové aplikace získá udělení autorizace z Azure AD B2C pro přístup k chráněnému webovému rozhraní API.

## <a name="configure-the-sample"></a>Konfigurace ukázky

Když je teď webové rozhraní API zaregistrované a máte definované obory, nakonfigurujete kód webového rozhraní API tak, aby používal vašeho tenanta Azure AD B2C. V tomto kurzu nakonfigurujete ukázkovou webovou aplikaci .NET Core, kterou stáhnete z GitHubu.

[Stáhněte si @no__t archiv -1. zip](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip) nebo naklonujte ukázkový projekt webového rozhraní API z GitHubu.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>Konfigurace webového rozhraní API

1. Otevřete soubor *B2C-WebApi/* * appSettings. JSON** * v aplikaci Visual Studio nebo Visual Studio Code.
1. Upravte blok `AzureAdB2C` tak, aby odrážel název vašeho tenanta, ID aplikace webového rozhraní API, název vaší zásady registrace a přihlašování a obory, které jste definovali dříve. Blok by měl vypadat podobně jako v následujícím příkladu (s odpovídajícími hodnotami `Tenant` a `ClientId`):

    ```json
    "AzureAdB2C": {
      "Tenant": "<your-tenant-name>.onmicrosoft.com",
      "ClientId": "<webapi-application-ID>",
      "Policy": "B2C_1_signupsignin1",

      "ScopeRead": "demo.read",
      "ScopeWrite": "demo.write"
    },
    ```

#### <a name="enable-cors"></a>Povolit CORS

Aby mohla vaše jednostránková aplikace volat ASP.NET Core webové rozhraní API, je potřeba povolit [CORS](https://docs.microsoft.com/aspnet/core/security/cors) ve webovém rozhraní API.

1. Do *Startup.cs*přidejte CORS do metody `ConfigureServices()`.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddCors();
    ```

1. Také v rámci metody `ConfigureServices()` nastavte hodnotu `jwtOptions.Authority` na následující identifikátor URI vystavitele tokenu.

    Nahraďte `<your-tenant-name>` názvem vašeho tenanta B2C.

    ```csharp
    jwtOptions.Authority = $"https://<your-tenant-name>.b2clogin.com/{Configuration["AzureAdB2C:Tenant"]}/{Configuration["AzureAdB2C:Policy"]}/v2.0";
    ```

1. V metodě `Configure()` nakonfigurujte CORS.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        app.UseCors(builder =>
            builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

1. (Pouze Visual Studio) V části **vlastnosti** v Průzkumník řešení otevřete soubor *launchSettings. JSON* a vyhledejte blok `iisExpress`.
1. (Pouze Visual Studio) Aktualizujte hodnotu `applicationURL` číslem portu, který jste zadali při registraci aplikace *webapi1* v předchozím kroku. Příklad:

    ```json
    "iisExpress": {
      "applicationUrl": "http://localhost:5000/",
      "sslPort": 0
    }
    ```

### <a name="configure-the-single-page-application"></a>Konfigurace jednostránkové aplikace

Jednostránkové aplikace (SPA) z [předchozího kurzu](active-directory-b2c-tutorials-spa.md) v řadě používá Azure AD B2C pro registraci a přihlašování uživatelů a volá webové rozhraní API ASP.NET Core chráněné *frabrikamb2c* ukázkovým klientem.

V této části provedete aktualizaci jednostránkové aplikace pro volání ASP.NET Core webového rozhraní API chráněného *vaším* klientem Azure AD B2C a spuštěním na místním počítači.

Změna nastavení v zabezpečeném hesla:

1. Otevřete soubor *index. html* v projektu [Active-Directory-B2C-JavaScript-msal-singlepageapp][github-js-spa] , který jste stáhli nebo naklonoval v předchozím kurzu.
1. Nakonfigurujte ukázku s identifikátorem URI pro obor *demo. Read* , který jste vytvořili dříve, a adresu URL webového rozhraní API.
    1. V definici `appConfig` nahraďte hodnotu `b2cScopes` úplným identifikátorem URI pro obor ( **úplnou hodnotu rozsahu** , kterou jste si poznamenali dříve).
    1. Hodnotu `webApi` změňte na hodnotu `applicationURL`, kterou jste zadali v předchozí části.

    Definice `appConfig` by měla vypadat podobně jako následující blok kódu (s názvem vašeho tenanta v místě `<your-tenant-name>`):

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var appConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/"
    };
    ```

## <a name="run-the-spa-and-web-api"></a>Spuštění ZABEZPEČENÉho a webového rozhraní API

Nakonec na místním počítači spustíte webové rozhraní API ASP.NET Core a jednostránkové aplikaci Node. js. Potom se přihlásíte k jednostránkové aplikaci a stisknutím tlačítka zahájíte požadavek na chráněné rozhraní API.

I když se obě aplikace v tomto kurzu spouštějí místně, používají Azure AD B2C pro zabezpečené registrace, přihlašování a pro udělení přístupu k chráněnému webovému rozhraní API.

### <a name="run-the-aspnet-core-web-api"></a>Spuštění ASP.NET Core webového rozhraní API

V aplikaci Visual Studio stiskněte klávesu **F5** pro sestavení a ladění řešení *B2C-WebAPI. sln* . Po spuštění projektu se ve výchozím prohlížeči zobrazí webová stránka s oznámením, že webové rozhraní API je k dispozici pro požadavky.

Pokud upřednostňujete použití rozhraní příkazového řádku `dotnet` místo sady Visual Studio:

1. Otevřete okno konzoly a přejděte do adresáře obsahujícího soubor *@no__t -1. csproj* . Příklad:

    `cd active-directory-b2c-dotnetcore-webapi/B2C-WebApi`

1. Sestavte a spusťte webové rozhraní API spuštěním `dotnet run`.

    Když je rozhraní API v provozu, měl by se zobrazit výstup podobný následujícímu (pro kurz můžete bez obav ignorovat `NETSDK1059`):

    ```console
    $ dotnet run
    Hosting environment: Production
    Content root path: /home/user/active-directory-b2c-dotnetcore-webapi/B2C-WebApi
    Now listening on: http://localhost:5000
    Application started. Press Ctrl+C to shut down.
    ```

### <a name="run-the-single-page-app"></a>Spustit jednu stránku aplikace

1. Otevřete okno konzoly a přejděte do adresáře obsahujícího ukázku Node. js. Příklad:

    `cd active-directory-b2c-javascript-msal-singlepageapp`

1. Spusťte následující příkazy:

    ```console
    npm install && npm update
    node server.js
    ```

    V okně konzoly se zobrazí číslo portu, ve kterém je aplikace hostovaná.

    ```console
    Listening on port 6420...
    ```

1. Pokud chcete zobrazit aplikaci, přejděte do `http://localhost:6420` v prohlížeči.
1. Přihlaste se pomocí e-mailové adresy a hesla, které jste použili v [předchozím kurzu](active-directory-b2c-tutorials-spa.md). Po úspěšném přihlášení by se měla zobrazit zpráva `User 'Your Username' logged-in`.
1. Vyberte tlačítko **zavolat webové rozhraní API** . SPA získá udělení autorizace z Azure AD B2C a pak přístup k chráněnému webovému rozhraní API, aby zobrazil obsah stránky jeho indexu:

    ```Output
    Web APi returned:
    "<html>\r\n<head>\r\n  <title>Azure AD B2C API Sample</title>\r\n ...
    ```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak:

> [!div class="checklist"]
> * Přidání aplikace webového rozhraní API
> * Konfigurace oborů pro webové rozhraní API
> * Udělení oprávnění webovému rozhraní API
> * Konfigurace ukázky pro použití aplikace

Teď, když jste se seznámili s požadavkem na prostředek z chráněného webového rozhraní API, získáte lepší přehled o tom, jak tyto typy aplikací vzájemně komunikují a s Azure AD B2C.

> [!div class="nextstepaction"]
> [Typy aplikací, které lze použít v Active Directory B2C >](active-directory-b2c-apps.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
