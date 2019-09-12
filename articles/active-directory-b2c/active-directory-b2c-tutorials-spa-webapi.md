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
ms.openlocfilehash: b53ce30f4c49580bcd8ad3e259adf0300d8bd4a6
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68369302"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>Kurz: Udělení přístupu k ASP.NET Core webovému rozhraní API z jednostránkové aplikace pomocí Azure Active Directory B2C

V tomto kurzu se dozvíte, jak volat prostředek webového rozhraní API ASP.NET Core Azure Active Directory (Azure AD) s ochranou B2C z jednostránkové aplikace.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání aplikace webového rozhraní API
> * Konfigurace oborů pro webové rozhraní API
> * Udělení oprávnění webovému rozhraní API
> * Konfigurace ukázky pro použití aplikace

## <a name="prerequisites"></a>Požadavky

* Proveďte kroky a požadavky v [kurzu: Povolte ověřování v aplikaci s jednou stránkou pomocí Azure Active Directory B2C](active-directory-b2c-tutorials-spa.md).
* Visual Studio 2019 nebo novější nebo Visual Studio Code
* .NET Core 2,2 nebo novější
* Node.js

## <a name="add-a-web-api-application"></a>Přidání aplikace webového rozhraní API

Prostředky webového rozhraní API musí být ve vašem tenantovi zaregistrované, aby mohly přijímat a reagovat na požadavky na chráněné prostředky klientskými aplikacemi, které prezentují přístupový token.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **aplikace**a pak vyberte **Přidat**.
1. Zadejte název aplikace. Například *webapi1*.
1. V případě **zahrnutí webové aplikace/webového rozhraní API** a **Povolení implicitního toku**vyberte **Ano**.
1. V případě **adresy URL odpovědi**Zadejte koncový bod, kde Azure AD B2C by měl vracet jakékoli tokeny, které vaše aplikace požaduje. V tomto kurzu se ukázka spouští místně a naslouchá na `https://localhost:5000`.
1. Pro **identifikátor URI ID aplikace**zadejte identifikátor koncového bodu rozhraní API k ZOBRAZENÉmu identifikátoru URI. V tomto kurzu zadejte `api`, aby úplný identifikátor URI byl `https://contosotenant.onmicrosoft.com/api`podobný.
1. Klikněte na možnost **Vytvořit**.
1. Výběrem aplikace *webapi1* otevřete její stránku vlastností.
1. Poznamenejte si **ID aplikace** zobrazené na stránce Vlastnosti. Toto ID budete potřebovat v pozdějším kroku při konfiguraci webové aplikace.

## <a name="configure-scopes"></a>Konfigurace oborů

Obory poskytují způsob, jak řídit přístup k chráněným prostředkům. Webové rozhraní API používá obory k implementaci řízení přístupu na základě oboru. Někteří uživatelé například můžou mít oprávnění ke čtení i zápisu, zatímco jiní uživatelé můžou mít oprávnění jen pro čtení. V tomto kurzu definujete pro webové rozhraní API oprávnění ke čtení i zápisu.

1. Vyberte možnost **aplikace**a pak výběrem možnosti *webapi1* otevřete stránku vlastností, pokud ještě není otevřená.
1. Vyberte **publikované obory**.
1. Pro **Rozsah**zadejte `Hello.Read`, a pro **Popis**zadejte `Read access to hello`.
1. Pro **Rozsah**zadejte `Hello.Write`, a pro **Popis**zadejte `Write access to hello`.
1. Vyberte **Uložit**.
1. Zaznamenejte **celou hodnotu oboru** pro `Hello.Read` obor, který má být použit v pozdějším kroku při konfiguraci jednostránkové aplikace. Úplná hodnota oboru je podobná `https://yourtenant.onmicrosoft.com/api/Hello.Read`.

Publikované obory je možné použít k udělení oprávnění k webovému rozhraní API klientské aplikaci.

## <a name="grant-permissions"></a>Udělení oprávnění

Chcete-li volat chráněné webové rozhraní API z jiné aplikace, je třeba udělit oprávnění aplikace webovému rozhraní API.

V kurzu požadavků jste vytvořili webovou aplikaci s názvem *WebApp1*. V tomto kurzu nakonfigurujete tuto aplikaci tak, aby volala webové rozhraní API, které jste vytvořili v předchozí části, *webapi1*.

1. V Azure Portal přejděte do svého tenanta B2C
1. Vyberte **aplikace**a pak vyberte *WebApp1*.
1. Vyberte **přístup přes rozhraní API**a pak vyberte **Přidat**.
1. V rozevíracím seznamu **Vyberte rozhraní API** vyberte možnost *webapi1*.
1. V rozevíracím seznamu **Vybrat obory** vyberte obory **Hello. Read** a **Hello. Write** , které jste předtím definovali.
1. Klikněte na **OK**.

Webová aplikace s jednou stránkou je zaregistrovaná pro volání chráněného webového rozhraní API. Uživatel se ověřuje pomocí Azure AD B2C pro použití jednostránkové aplikace. Jednostránkové aplikace získá udělení autorizace z Azure AD B2C pro přístup k chráněnému webovému rozhraní API.

## <a name="configure-the-sample"></a>Konfigurace ukázky

Když je teď webové rozhraní API zaregistrované a máte definované obory, nakonfigurujete kód webového rozhraní API tak, aby používal vašeho tenanta Azure AD B2C. V tomto kurzu nakonfigurujete ukázkovou webovou aplikaci .NET Core, kterou stáhnete z GitHubu.

[Stáhněte si archiv .zipnebonaklonujteukázkovýprojektwebovéhorozhraníAPIzGitHubu.\*](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip)

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>Konfigurace webového rozhraní API

1. Otevřete soubor *B2C-WebApi/* * appSettings. JSON** * v aplikaci Visual Studio nebo Visual Studio Code.
1. `AzureAdB2C` Upravte blok tak, aby odrážel název vašeho tenanta, ID aplikace webové aplikace API, název vaší zásady registrace a přihlašování a obory, které jste definovali dříve. Blok by měl vypadat podobně jako v následujícím příkladu (s `Tenant` odpovídajícími `ClientId` hodnotami a):

    ```json
    "AzureAdB2C": {
      "Tenant": "<your-tenant-name>.onmicrosoft.com",
      "ClientId": "<webapi-application-ID>",
      "Policy": "B2C_1_signupsignin1",

      "ScopeRead": "Hello.Read",
      "ScopeWrite": "Hello.Write"
    },
    ```

#### <a name="enable-cors"></a>Povolení CORS

Aby mohla vaše jednostránková aplikace volat ASP.NET Core webové rozhraní API, je potřeba povolit [CORS](https://docs.microsoft.com/aspnet/core/security/cors) ve webovém rozhraní API.

1. V souboru *Startup.cs* přidejte CORS do metody `ConfigureServices()`.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddCors();
    ```

1. Také v rámci `ConfigureServices()` metody `jwtOptions.Authority` nastavte hodnotu na následující identifikátor URI vystavitele tokenu.

    Nahraďte `<your-tenant-name>` názvem vašeho tenanta B2C.

    ```csharp
    jwtOptions.Authority = $"https://<your-tenant-name>.b2clogin.com/{Configuration["AzureAdB2C:Tenant"]}/{Configuration["AzureAdB2C:Policy"]}/v2.0";
    ```

1. `Configure()` V metodě nakonfigurujte CORS.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        app.UseCors(builder =>
            builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

1. (Pouze Visual Studio) V části **vlastnosti** v Průzkumník řešení otevřete soubor *launchSettings. JSON* a `iisExpress` pak najděte blok.
1. (Pouze Visual Studio) Aktualizujte hodnotu číslem portu, který jste zadali při registraci aplikace webapi1 v předchozím kroku. `applicationURL` Příklad:

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
1. Nakonfigurujte ukázku s identifikátorem URI pro obor *Hello. Read* , který jste vytvořili dříve, a adresu URL webového rozhraní API.
    1. V definici nahraďte hodnotu úplným identifikátorem URI pro obor ( **úplnou hodnotu oboru** , kterou jste si poznamenali dříve). `b2cScopes` `appConfig`
    1. `webApi` Změňte hodnotu`applicationURL` na hodnotu zadanou v předchozí části.

    Definice by měla vypadat podobně jako následující blok kódu (s názvem vašeho tenanta v `<your-tenant-name>`místě): `appConfig`

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var appConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/Hello.Read"],
      webApi: "http://localhost:5000/"
    };
    ```

## <a name="run-the-spa-and-web-api"></a>Spuštění ZABEZPEČENÉho a webového rozhraní API

Nakonec na místním počítači spustíte webové rozhraní API ASP.NET Core a jednostránkové aplikaci Node. js. Potom se přihlásíte k jednostránkové aplikaci a stisknutím tlačítka zahájíte požadavek na chráněné rozhraní API.

I když se obě aplikace v tomto kurzu spouštějí místně, používají Azure AD B2C pro zabezpečené registrace, přihlašování a pro udělení přístupu k chráněnému webovému rozhraní API.

### <a name="run-the-aspnet-core-web-api"></a>Spuštění ASP.NET Core webového rozhraní API

V aplikaci Visual Studio stiskněte klávesu **F5** pro sestavení a ladění řešení *B2C-WebAPI. sln* . Po spuštění projektu se ve výchozím prohlížeči zobrazí webová stránka s oznámením, že webové rozhraní API je k dispozici pro požadavky.

Pokud dáváte přednost použití rozhraní `dotnet` příkazového řádku místo sady Visual Studio:

1. Otevřete okno konzoly a přejděte do adresáře, který obsahuje  *\*soubor. csproj* . Příklad:

    `cd active-directory-b2c-dotnetcore-webapi/B2C-WebApi`

1. Sestavte a spusťte webové rozhraní API `dotnet run`spuštěním.

    Když je rozhraní API v provozu, měl by se zobrazit výstup podobný následujícímu (pro kurz můžete ignorovat všechna `NETSDK1059` upozornění):

    ```console
    $ dotnet run
    Hosting environment: Production
    Content root path: /home/user/active-directory-b2c-dotnetcore-webapi/B2C-WebApi
    Now listening on: http://localhost:5000
    Application started. Press Ctrl+C to shut down.
    ```

### <a name="run-the-single-page-app"></a>Spuštění jednostránkové aplikace

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

1. Přejděte do `http://localhost:6420` prohlížeče, aby se zobrazila aplikace.
1. Přihlaste se pomocí e-mailové adresy a hesla, které jste použili v [předchozím kurzu](active-directory-b2c-tutorials-spa.md). Po úspěšném přihlášení by se měla zobrazit `User 'Your Username' logged-in` zpráva.
1. Vyberte tlačítko **zavolat webové rozhraní API** . SPA získá udělení autorizace z Azure AD B2C a pak přístup k chráněnému webovému rozhraní API, aby zobrazil obsah stránky jeho indexu:

    ```Output
    Web APi returned:
    "<html>\r\n<head>\r\n  <title>Azure AD B2C API Sample</title>\r\n ...
    ```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

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
