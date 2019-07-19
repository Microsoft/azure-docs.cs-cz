---
title: Kurz – udělení přístupu k ASP.NET Core webovému rozhraní API z jednostránkové aplikace – Azure Active Directory B2C | Microsoft Docs
description: Kurz popisující použití Active Directory B2C k ochraně webového rozhraní API .NET Core a jeho volání z jednostránkové aplikace.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: cbea29320c896637190766d1b2b60c09f7db5163
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68347166"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>Kurz: Udělení přístupu k ASP.NET Core webovému rozhraní API z jednostránkové aplikace pomocí Azure Active Directory B2C

V tomto kurzu se dozvíte, jak volat Azure Active Directory (Azure AD) chráněného ASP.NET Core prostředků webového rozhraní API z jednostránkové aplikace.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání aplikace webového rozhraní API
> * Konfigurace oborů pro webové rozhraní API
> * Udělení oprávnění webovému rozhraní API
> * Konfigurace ukázky pro použití aplikace

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Proveďte kroky a požadavky v [kurzu: Povolte ověřování jednostránkové aplikace s účty pomocí Azure Active Directory B2C](active-directory-b2c-tutorials-spa.md).

## <a name="add-a-web-api-application"></a>Přidání aplikace webového rozhraní API

Prostředky webového rozhraní API musí být ve vašem tenantovi zaregistrované, aby mohly přijímat a reagovat na požadavky na chráněné prostředky klientskými aplikacemi, které prezentují přístupový token.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **aplikace**a pak vyberte **Přidat**.
5. Zadejte název aplikace. Například *webapi1*.
6. V případě **zahrnutí webové aplikace/webového rozhraní API** a **Povolení implicitního toku**vyberte **Ano**.
7. V případě **adresy URL odpovědi**Zadejte koncový bod, kde Azure AD B2C by měl vracet jakékoli tokeny, které vaše aplikace požaduje. V tomto kurzu se ukázka spouští místně a naslouchá na `https://localhost:5000`.
8. Pro **identifikátor URI ID aplikace**zadejte identifikátor použitý pro vaše webové rozhraní API. Vygeneruje se pro vás úplný identifikátor URI včetně domény. Například, `https://contosotenant.onmicrosoft.com/api`.
9. Klikněte na možnost **Vytvořit**.
10. Na stránce Vlastnosti Poznamenejte ID aplikace, které použijete při konfiguraci webové aplikace.

## <a name="configure-scopes"></a>Konfigurace oborů

Obory poskytují způsob, jak řídit přístup k chráněným prostředkům. Webové rozhraní API používá obory k implementaci řízení přístupu na základě oboru. Někteří uživatelé například můžou mít oprávnění ke čtení i zápisu, zatímco jiní uživatelé můžou mít oprávnění jen pro čtení. V tomto kurzu nadefinujete pro webové rozhraní API oprávnění ke čtení.

1. Vyberte **aplikace**a pak vyberte *webapi1*.
2. Vyberte **publikované obory**.
3. Pro **Rozsah**zadejte `Hello.Read`, a pro Popis zadejte `Read access to hello`.
4. Pro **Rozsah**zadejte `Hello.Write`, a pro Popis zadejte `Write access to hello`.
5. Klikněte na **Uložit**.

Publikované obory je možné použít k udělení oprávnění k webovému rozhraní API klientské aplikaci.

## <a name="grant-permissions"></a>Udělení oprávnění

Chcete-li volat chráněné webové rozhraní API z aplikace, musíte aplikaci udělit oprávnění k rozhraní API. V kurzu požadavků jste vytvořili webovou aplikaci v Azure AD B2C s názvem *WebApp1*. Tuto aplikaci použijete pro volání webového rozhraní API.

1. Vyberte **aplikace**a pak vyberte *WebApp1*.
2. Vyberte **přístup přes rozhraní API**a pak vyberte **Přidat**.
3. V rozevíracím seznamu **Vyberte rozhraní API** vyberte možnost *webapi1*.
4. V rozevíracím seznamu **Vybrat obory** vyberte obory **Hello. Read** a **Hello. Write** , které jste předtím definovali.
5. Klikněte na **OK**.

Vaše aplikace **My sample single page app** je zaregistrovaná a může volat chráněné rozhraní **Hello Core API**. Uživatel se ověřuje pomocí Azure AD B2C, aby používal jednu stránkovou aplikaci. Jedna stránka aplikace získá udělení autorizace z Azure AD B2C pro přístup k chráněnému webovému rozhraní API.

## <a name="configure-the-sample"></a>Konfigurace ukázky

Když je teď webové rozhraní API zaregistrované a máte definované obory, nakonfigurujete kód webového rozhraní API tak, aby používal vašeho tenanta Azure AD B2C. V tomto kurzu nakonfigurujete ukázkovou webovou aplikaci .NET Core, kterou si můžete stáhnout z GitHubu. [Stáhněte soubor .zip](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip) nebo naklonujte ukázkovou webovou aplikaci z GitHubu.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>Konfigurace webového rozhraní API

1. Otevřete řešení **B2C-WebAPI.sln** v sadě Visual Studio.
2. Otevřete soubor **appsettings.json**. Aktualizací následujících hodnot nakonfigurujte webové rozhraní API pro použití vašeho tenanta:

    ```javascript
    "AzureAdB2C": 
      {
        "Tenant": "<your tenant name>.onmicrosoft.com", 
        "ClientId": "<application-ID>",
        "Policy": "B2C_1_signupsignin1>",
        "ScopeRead": "Hello.Read"  
      },
    ```

#### <a name="enable-cors"></a>Povolení CORS

Pokud chcete, aby jedna stránková aplikace volala ASP.NET Core webové rozhraní API, je potřeba povolit [CORS](https://docs.microsoft.com/aspnet/core/security/cors).

1. V souboru **Startup.cs** přidejte CORS do metody `ConfigureServices()`.

    ```csharp
    public void ConfigureServices(IServiceCollection services) {
      services.AddCors();
    ```

2. V souboru **Startup.cs** nakonfigurujte CORS v metodě `Configure()`.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory) {
      app.UseCors(builder =>
        builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

3. Otevřete soubor **launchSettings.json** v části **Vlastnosti**, vyhledejte nastavení **iisSettings** *adresa_URL_aplikace* a potom nastavte číslo portu na port registrovaný pro adresu URL odpovědi rozhraní API `http://localhost:5000`.

### <a name="configure-the-single-page-application"></a>Konfigurace jednostránkové aplikace

Jednostránkové aplikace používá Azure AD B2C pro registraci a přihlašování uživatelů a volá chráněné ASP.NET Core webové rozhraní API. Aktualizujete jednostránkové aplikaci tak, aby volala webové rozhraní API .NET Core.

Nastavení aplikace můžete změnit následujícím způsobem:

1. Otevřete soubor `index.html`.
2. Nakonfigurujte ukázku s použitím informací o registraci tenanta Azure AD B2C. V následujícím kódu přidejte název tenanta do **b2cScopes** a změňte hodnotu **webApi** na hodnotu *applicationURL*, kterou jste si předtím poznamenali:

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var applicationConfig = {
        clientID: '<application-ID>',
        authority: "https://<your-tenant-name>.b2clogin.com/tfp/<your-tenant-name>.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://<Your tenant name>.onmicrosoft.com/api/Hello.Read"],
        webApi: 'http://localhost:5000/api/values',
    };
    ```

## <a name="run-the-spa-application-and-web-api"></a>Spuštění aplikace SPA a webového rozhraní API

Je nutné spustit aplikaci Node. js Single Page i rozhraní Web API .NET Core.

### <a name="run-the-aspnet-core-web-api"></a>Spuštění webového rozhraní API ASP.NET Core 

Stisknutím klávesy **F5** spusťte ladění řešení **B2C-WebAPI.sln** v sadě Visual Studio.

Po spuštění projektu se ve výchozím webovém prohlížeči zobrazí webová stránka s oznámením, že je webové rozhraní API k dispozici pro požadavky.

### <a name="run-the-single-page-app"></a>Spuštění jednostránkové aplikace

1. Spusťte příkazový řádek Node.js.
2. Přejděte do adresáře obsahujícího ukázku Node.js. Příklad: `cd c:\active-directory-b2c-javascript-msal-singlepageapp`
3. Spusťte následující příkazy:
    ```
    npm install && npm update
    node server.js
    ```

    V okně konzoly se zobrazí číslo portu, ve kterém je aplikace hostovaná.
    
    ```
    Listening on port 6420...
    ```

4. Pomocí prohlížeče přejděte na adresu `http://localhost:6420` a zobrazte aplikaci.
5. Přihlaste se pomocí e-mailové adresy a hesla, které jste použili v kurzu [Ověřování uživatelů pomocí Azure Active Directory D2C v jednostránkové aplikaci (JavaScript)](active-directory-b2c-tutorials-spa.md).
6. Klikněte na **volat rozhraní API**.

Jakmile se zaregistrujete nebo přihlásíte pomocí uživatelského účtu, ukázka zavolá chráněné webové rozhraní API a vrátí výsledek.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Přidání aplikace webového rozhraní API
> * Konfigurace oborů pro webové rozhraní API
> * Udělení oprávnění webovému rozhraní API
> * Konfigurace ukázky pro použití aplikace

> [!div class="nextstepaction"]
> [Kurz: Přidejte do svých aplikací zprostředkovatele identity v Azure Active Directory B2C](tutorial-add-identity-providers.md)
