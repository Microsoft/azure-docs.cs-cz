---
title: Kurz – Poskytnutí přístupu k webovému rozhraní API ASP.NET Core z jednostránkové aplikace pomocí Azure Active Directory B2C | Microsoft Docs
description: Kurz popisující použití Active Directory B2C k ochraně webového rozhraní API .NET Core a jeho volání z jednostránkové aplikace.
services: active-directory-b2c
author: davidmu1
ms.author: davidmu
ms.date: 3/02/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory-b2c
ms.openlocfilehash: 0e9e3074e2cdd9ec3adc814779811d150cd11010
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-app-using-azure-active-directory-b2c"></a>Kurz: Poskytnutí přístupu k webovému rozhraní API ASP.NET Core z jednostránkové aplikace pomocí Azure Active Directory B2C

V tomto kurzu se dozvíte, jak volat prostředek webového rozhraní API ASP.NET Core chráněný službou Azure Active Directory (Azure AD) B2C z jednostránkové aplikace.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Registrace webového rozhraní API v tenantovi Azure AD B2C
> * Definice a konfigurace oborů pro webové rozhraní API
> * Udělení oprávnění k webovému rozhraní API aplikaci
> * Aktualizace vzorového kódu tak, aby pomocí Azure AD B2C chránil webové rozhraní API

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Dokončete [kurz Ověřování uživatelů pomocí Azure Active Directory B2C v jednostránkové aplikaci](active-directory-b2c-tutorials-spa.md).
* Nainstalujte sadu [Visual Studio 2017](https://www.visualstudio.com/downloads/) se sadou funkcí **Vývoj pro ASP.NET a web**.
* Sada [.NET Core SDK 2.0.0](https://www.microsoft.com/net/core) nebo novější
* Instalovat [Node.js](https://nodejs.org/en/download/)

## <a name="register-web-api"></a>Registrace webového rozhraní API

Prostředky webového rozhraní API je nejprve potřeba zaregistrovat ve vašem tenantovi, a až pak můžou přijímat a reagovat na [požadavky na chráněné prostředky](../active-directory/develop/active-directory-dev-glossary.md#resource-server) od [klientských aplikací](../active-directory/develop/active-directory-dev-glossary.md#client-application), které obsahují [přístupový token](../active-directory/develop/active-directory-dev-glossary.md#access-token) ze služby Azure Active Directory. Registrací se ve vašem tenantovi vytvoří [objekt aplikace a instanční objekt](../active-directory/develop/active-directory-dev-glossary.md#application-object). 

Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Ze seznamu služeb na webu Azure Portal vyberte **Azure AD B2C**.

2. V nastavení B2C klikněte na **Aplikace** a pak klikněte na **Přidat**.

    K registraci ukázkového webového rozhraní API ve vašem tenantovi použijte následující nastavení.
    
    ![Přidání nového rozhraní API](media/active-directory-b2c-tutorials-spa-webapi/web-api-registration.png)
    
    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Název** | Hello Core API | Zadejte **Název**, který popíše vaše webové rozhraní API pro vývojáře. |
    | **Zahrnout webovou aplikaci nebo webové rozhraní API** | Ano | Pro webové rozhraní API vyberte **Ano**. |
    | **Povolit implicitní tok** | Ano | Vyberte **Ano**, protože rozhraní API používá [Přihlášení OpenID Connect](active-directory-b2c-reference-oidc.md). |
    | **Adresa URL odpovědi** | `http://localhost:44332` | Adresy URL odpovědí jsou koncové body, kam Azure AD B2C vrací všechny tokeny, které vaše rozhraní API požaduje. V tomto kurzu se ukázkové webové rozhraní API spouští místně (localhost) a naslouchá na portu 5000. |
    | **Identifikátor URI ID aplikace** | HelloCoreAPI | Tento identifikátor URI jednoznačně identifikuje rozhraní API v tenantovi. Díky tomu můžete v jednom tenantovi registrovat více rozhraní API. [Obory](../active-directory/develop/active-directory-dev-glossary.md#scopes) řídí přístup k chráněnému prostředku rozhraní API a definují se pro jednotlivé identifikátory URI ID aplikace. |
    | **Nativní klient** | Ne | Vzhledem k tomu, že se jedná o webové rozhraní API, a ne nativního klienta, vyberte Ne. |
    
3. Kliknutím na **Vytvořit** své rozhraní API zaregistrujte.

Zaregistrovaná rozhraní API se zobrazí v seznamu aplikací pro příslušného tenanta Azure AD B2C. Vyberte ze seznamu vaše webové rozhraní API. Zobrazí se podokno vlastností webového rozhraní API.

![Vlastnosti webového rozhraní API](./media/active-directory-b2c-tutorials-spa-webapi/b2c-web-api-properties.png)

Poznamenejte si **ID klienta aplikace**. Toto ID jednoznačně identifikuje rozhraní API a je potřeba při konfiguraci rozhraní API později v tomto kurzu.

Registrací webového rozhraní API v Azure AD B2C se nadefinuje vztah důvěryhodnosti. Vzhledem k tomu, že je rozhraní API zaregistrované v B2C, může teď důvěřovat přístupovým tokenům B2C přijímaným z jiných aplikací.

## <a name="define-and-configure-scopes"></a>Definice a konfigurace oborů

[Obory](../active-directory/develop/active-directory-dev-glossary.md#scopes) poskytují způsob řízení přístupu k chráněným prostředkům. Webové rozhraní API používá obory k implementaci řízení přístupu na základě oboru. Někteří uživatelé například můžou mít oprávnění ke čtení i zápisu, zatímco jiní uživatelé můžou mít oprávnění jen pro čtení. V tomto kurzu nadefinujete pro webové rozhraní API oprávnění ke čtení.

### <a name="define-scopes-for-the-web-api"></a>Definice oborů pro webové rozhraní API

Zaregistrovaná rozhraní API se zobrazí v seznamu aplikací pro příslušného tenanta Azure AD B2C. Vyberte ze seznamu vaše webové rozhraní API. Zobrazí se podokno vlastností webového rozhraní API.

Klikněte na **Publikované obory (Preview)**.

Nakonfigurujte obory pro rozhraní API přidáním následujících záznamů. 

![obory definované ve webovém rozhraní API](media/active-directory-b2c-tutorials-spa-webapi/scopes-web-api.png)

| Nastavení      | Navrhovaná hodnota  | Popis                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Rozsah** | demo.read | Oprávnění ke čtení pro ukázkové rozhraní API |

Klikněte na **Uložit**.

Publikované obory je možné použít k udělení oprávnění k webovému rozhraní API klientské aplikaci.

### <a name="grant-app-permissions-to-web-api"></a>Udělení oprávnění k webovému rozhraní API aplikaci

Pokud chcete volat chráněné webové rozhraní API z aplikace, musíte aplikaci udělit oprávnění k rozhraní API. V tomto kurzu použijete jednostránkovou aplikaci vytvořenou v kurzu [Ověřování uživatelů pomocí Azure Active Directory D2C v jednostránkové aplikaci (JavaScript)](active-directory-b2c-tutorials-spa.md).

1. Na webu Azure Portal vyberte ze seznamu služeb **Azure AD B2C** a kliknutím na **Aplikace** zobrazte seznam zaregistrovaných aplikací.

2. Ze seznamu aplikací vyberte **My sample single page app**, klikněte na **Přístup přes rozhraní API (Preview)** a pak na **Přidat**.

3. V rozevíracím seznamu **Vybrat rozhraní API** vyberte své zaregistrované webové rozhraní API **Hello Core API**.

4. V rozevíracím seznamu **Vybrat obory** vyberte obory, které jste definovali v registraci webového rozhraní API.

    ![výběr oborů pro aplikaci](media/active-directory-b2c-tutorials-spa-webapi/selecting-scopes-for-app.png)

5. Klikněte na **OK**.

Vaše aplikace **My sample single page app** je zaregistrovaná a může volat chráněné rozhraní **Hello Core API**. Uživatel se [ověří](../active-directory/develop/active-directory-dev-glossary.md#authentication) v Azure AD B2C, aby mohl použít desktopovou aplikaci WPF. Desktopová aplikace získá z Azure AD B2C [udělení autorizace](../active-directory/develop/active-directory-dev-glossary.md#authorization-grant) pro přístup k chráněnému webovému rozhraní API.

## <a name="update-code"></a>Aktualizace kódu

Když je teď webové rozhraní API zaregistrované a máte definované obory, je potřeba nakonfigurovat kód webového rozhraní API tak, aby používalo vašeho tenanta Azure AD B2C. V tomto kurzu nakonfigurujete ukázkovou webovou aplikaci .NET Core, kterou si můžete stáhnout z GitHubu. 

[Stáhněte soubor .zip](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip) nebo naklonujte ukázkovou webovou aplikaci z GitHubu.

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
        "ClientId": "<The Application ID for your web API obtained from the Azure portal>",
        "Policy": "<Your sign up sign in policy e.g. B2C_1_SiUpIn>",
        "ScopeRead": "demo.read"  
      },
    ```

#### <a name="enable-cors"></a>Povolení CORS

Pokud chcete své jednostránkové aplikaci povolit volání webového rozhraní API ASP.NET Core, je potřeba povolit [CORS](https://docs.microsoft.com/aspnet/core/security/cors).

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

### <a name="configure-the-single-page-app"></a>Konfigurace jednostránkové aplikace

Jednostránková aplikace používá Azure AD B2C k registraci a přihlašování uživatelů a volání chráněného webového rozhraní API ASP.NET Core. Jednostránkovou aplikaci je potřeba aktualizovat tak, aby volala webové rozhraní API .NET Core.
Nastavení aplikace můžete změnit následujícím způsobem:

1. Otevřete soubor `index.html` v ukázce jednostránkové aplikace Node.js.
2. Nakonfigurujte ukázku s použitím informací o registraci tenanta Azure AD B2C. V následujících řádcích kódu změňte hodnoty **b2cScopes** a **webApi**:

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var applicationConfig = {
        clientID: '<Application ID for your SPA obtained from portal app registration>',
        authority: "https://login.microsoftonline.com/tfp/<your-tenant-name>.onmicrosoft.com/B2C_1_SiUpIn",
        b2cScopes: ["https://<Your tenant name>.onmicrosoft.com/HelloCoreAPI/demo.read"],
        webApi: 'http://localhost:58553/api/values',
    };
    ```

## <a name="run-the-spa-app-and-web-api"></a>Spuštění aplikace SPA a webového rozhraní API

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

    V okně konzoly se zobrazí číslo portu, na kterém je aplikace hostovaná.
    
    ```
    Listening on port 6420...
    ```

4. V prohlížeči přejděte na adresu `http://localhost:6420` a zobrazte aplikaci.
5. Přihlaste se pomocí e-mailové adresy a hesla, které jste použili v kurzu [Ověřování uživatelů pomocí Azure Active Directory D2C v jednostránkové aplikaci (JavaScript)](active-directory-b2c-tutorials-spa.md).
6. Klikněte na tlačítko **Call API** (Zavolat rozhraní API).

Jakmile se zaregistrujete nebo přihlásíte pomocí uživatelského účtu, ukázka zavolá chráněné webové rozhraní API a vrátí výsledek.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Svého tenanta Azure AD B2C můžete použít i k vyzkoušení dalších kurzů k Azure AD B2C. Jakmile už ho nebudete potřebovat, můžete [svého tenanta Azure AD B2C odstranit](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Další kroky

Tento článek vás provedl ochranou webového rozhraní API prostřednictvím registrace a definice oborů v Azure AD B2C. Další informace najdete v dostupných ukázkách kódu pro Azure AD B2C.

> [!div class="nextstepaction"]
> [Ukázky kódu pro Azure AD B2C](https://azure.microsoft.com/resources/samples/?service=active-directory-b2c&sort=0)
