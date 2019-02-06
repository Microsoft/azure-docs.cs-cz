---
title: Kurz – poskytnutí přístupu k webovému rozhraní API ASP.NET Core z jednostránkové aplikace – Azure Active Directory B2C | Dokumentace Microsoftu
description: Kurz popisující použití Active Directory B2C k ochraně webového rozhraní API .NET Core a jeho volání z jednostránkové aplikace.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 13cbf1e81e0d203c181efb0881ec2a437cbaef24
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55752173"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>Kurz: Udělení přístupu k webovému rozhraní API ASP.NET Core z jednostránkové aplikace pomocí Azure Active Directory B2C

V tomto kurzu se dozvíte, jak volání Azure Active Directory (Azure AD) B2C ASP.NET Core chráněný prostředek webového rozhraní API z jednostránkové aplikace.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání webového rozhraní API aplikaci
> * Konfigurace oborů pro webové rozhraní API
> * Udělení oprávnění k webovému rozhraní API
> * Konfigurace ukázky aplikace k používání aplikace

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Dokončete kroky a požadavky [kurzu: Povolit jednostránkové aplikace ověřování účtů pomocí Azure Active Directory B2C](active-directory-b2c-tutorials-spa.md).

## <a name="add-a-web-api-application"></a>Přidání webového rozhraní API aplikaci

Webové rozhraní API prostředky musí být zaregistrovaní ve vašem tenantovi předtím, než může přijímat a reagovat na požadavky na chráněný prostředek v klientských aplikacích, které se přístupový token.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
3. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **aplikací**a pak vyberte **přidat**.
5. Zadejte název aplikace. Například *webapi1*.
6. Pro **zahrnout webovou aplikaci / webové rozhraní API** a **povolit implicitní tok**vyberte **Ano**.
7. Pro **adresy URL odpovědi**, zadejte koncový bod kam by měl Azure AD B2C vrátí všechny tokeny, které vaše aplikace požaduje. V tomto kurzu se ukázka spouští místně a naslouchá na `https://localhost:5000`.
8. Pro **identifikátor ID URI aplikace**, zadejte identifikátor použitý pro vaše webového rozhraní API. Vygeneruje se pro vás úplný identifikátor URI včetně domény. Například, `https://contosotenant.onmicrosoft.com/api`.
9. Klikněte na možnost **Vytvořit**.
10. Na stránce Vlastnosti zaznamenejte ID aplikace, které budete používat při konfiguraci webové aplikace.

## <a name="configure-scopes"></a>Konfigurace oborů

Obory poskytují způsob řízení přístupu k chráněným prostředkům. Webové rozhraní API používá obory k implementaci řízení přístupu na základě oboru. Někteří uživatelé například můžou mít oprávnění ke čtení i zápisu, zatímco jiní uživatelé můžou mít oprávnění jen pro čtení. V tomto kurzu nadefinujete pro webové rozhraní API oprávnění ke čtení.

1. Vyberte **aplikací**a pak vyberte *webapi1*.
2. Vyberte **publikované obory**.
3. Pro **oboru**, zadejte `Hello.Read`a popis, zadejte `Read access to hello`.
4. Pro **oboru**, zadejte `Hello.Write`a popis, zadejte `Write access to hello`.
5. Klikněte na **Uložit**.

Publikované obory je možné použít k udělení oprávnění k webovému rozhraní API klientské aplikaci.

## <a name="grant-permissions"></a>Udělení oprávnění

Pokud chcete volat chráněné webové rozhraní API z aplikace, budete muset udělit oprávnění aplikace rozhraní API. V požadovaném kurzu vytvořili webovou aplikaci v Azure AD B2C s názvem *webapp1*. Volání webového rozhraní API pomocí této aplikace.

1. Vyberte **aplikací**a pak vyberte *webapp1*.
2. Vyberte **přístup přes rozhraní API**a pak vyberte **přidat**.
3. V **vybrat rozhraní API** rozevíracím seznamu vyberte *webapi1*.
4. V **vyberte obory** rozevírací seznam, vyberte **Hello.Read** a **Hello.Write** obory, které jste dříve definovali.
5. Klikněte na **OK**.

Vaše aplikace **My sample single page app** je zaregistrovaná a může volat chráněné rozhraní **Hello Core API**. Uživatel se ověřuje pomocí Azure AD B2C pro jednostránkovou aplikaci používat. Jednostránkové aplikace získá z Azure AD B2C pro přístup k chráněné webové rozhraní API udělení autorizace.

## <a name="configure-the-sample"></a>Konfigurace ukázky aplikace

Teď, když je webové rozhraní API zaregistrované a máte definované obory, nakonfigurujte webové rozhraní API kódu, aby používala vašeho tenanta Azure AD B2C. V tomto kurzu nakonfigurujete ukázkovou webovou aplikaci .NET Core, kterou si můžete stáhnout z Githubu. [Stáhněte soubor .zip](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip) nebo naklonujte ukázkovou webovou aplikaci z GitHubu.

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

Aby jednostránkové aplikace volat webové rozhraní API ASP.NET Core, je potřeba povolit [CORS](https://docs.microsoft.com/aspnet/core/security/cors).

1. V souboru **Startup.cs** přidejte CORS do metody `ConfigureServices()`.

    ```C#
    public void ConfigureServices(IServiceCollection services) {
      services.AddCors();
    ```

2. V souboru **Startup.cs** nakonfigurujte CORS v metodě `Configure()`.

    ```C#
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory) {
      app.UseCors(builder =>
        builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

3. Otevřete soubor **launchSettings.json** v části **Vlastnosti**, vyhledejte nastavení **iisSettings** *adresa_URL_aplikace* a potom nastavte číslo portu na port registrovaný pro adresu URL odpovědi rozhraní API `http://localhost:5000`.

### <a name="configure-the-single-page-application"></a>Konfigurace jednostránkové aplikace

Jednostránková aplikace používá registrace a přihlášení uživatele Azure AD B2C a volá chráněné webové rozhraní API ASP.NET Core. Aktualizujete jednostránková aplikace volat webové rozhraní API .NET Core.

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

## <a name="run-the-spa-application-and-web-api"></a>Spuštění aplikace SPA aplikace a webové rozhraní API

Musíte spustit jednostránkovou aplikaci Node.js i webové rozhraní API .NET Core.

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

    V okně konzoly se zobrazí číslo portu je hostitelem aplikace.
    
    ```
    Listening on port 6420...
    ```

4. Použít prohlížeč a přejděte na adresu `http://localhost:6420` zobrazíte aplikaci.
5. Přihlaste se pomocí e-mailové adresy a hesla, které jste použili v kurzu [Ověřování uživatelů pomocí Azure Active Directory D2C v jednostránkové aplikaci (JavaScript)](active-directory-b2c-tutorials-spa.md).
6. Klikněte na tlačítko **volání rozhraní API**.

Jakmile se zaregistrujete nebo přihlásíte pomocí uživatelského účtu, ukázka zavolá chráněné webové rozhraní API a vrátí výsledek.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Přidání webového rozhraní API aplikaci
> * Konfigurace oborů pro webové rozhraní API
> * Udělení oprávnění k webovému rozhraní API
> * Konfigurace ukázky aplikace k používání aplikace

> [!div class="nextstepaction"]
> [Kurz: Přidat zprostředkovatele identity pro vaše aplikace v Azure Active Directory B2C](tutorial-add-identity-providers.md)
