---
title: 'Rychlý Start: ochrana ASP.NET Core webového rozhraní API s platformou Microsoft identity | Azure'
titleSuffix: Microsoft identity platform
description: V tomto rychlém startu si stáhnete a upravíte ukázku kódu, která ukazuje, jak chránit ASP.NET Core webového rozhraní API pomocí platformy Microsoft identity pro autorizaci.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/22/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: da53d6bad790e6b204fa2a2b045e7bfdd83e0cc9
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102525"
---
# <a name="quickstart-protect-an-aspnet-core-web-api-with-microsoft-identity-platform"></a>Rychlý Start: ochrana ASP.NET Core webového rozhraní API s platformou Microsoft identity

V tomto rychlém startu si stáhnete ukázku kódu ASP.NET Core webového rozhraní API a zkontrolujete jeho kód, který omezí přístup k prostředkům jenom na autorizované účty. Ukázka podporuje autorizaci osobních účtů a účtů Microsoft v jakékoli organizaci Azure Active Directory (Azure AD).

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Požadavky
>
> - Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> - [Tenant Azure Active Directory](quickstart-create-new-tenant.md)
> - [.NET Core SDK 3.1 +](https://dotnet.microsoft.com/)
> - [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) nebo [Visual Studio Code](https://code.visualstudio.com/)
>
> ## <a name="step-1-register-the-application"></a>Krok 1: registrace aplikace
>
> Nejdřív Zaregistrujte webové rozhraní API ve vašem tenantovi Azure AD a přidejte obor pomocí následujících kroků:
>
> 1. Přihlaste se na <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
> 1. Máte-li přístup k více klientům, použijte filtr **adresář + odběr** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: v horní nabídce a vyberte klienta, ve kterém chcete aplikaci zaregistrovat.
> 1. Vyhledejte a vyberte **Azure Active Directory**.
> 1. V části **Spravovat** vyberte **Registrace aplikací**  >  **Nová registrace**.
> 1. Zadejte **název** vaší aplikace, například `AspNetCoreWebApi-Quickstart` . Uživatel vaší aplikace může tento název zobrazit a později ho můžete změnit.
> 1. Vyberte **Zaregistrovat**.
> 1. V části **Spravovat** vyberte **zveřejnit rozhraní API**  >  **Přidat obor**. Přijměte výchozí **identifikátor URI ID aplikace** výběrem možnosti **Uložit a pokračovat** a zadejte následující podrobnosti:
>    - **Název oboru**: `access_as_user`
>    - **Kdo může udělit souhlas?**: **Správci a uživatelé**
>    - **Zobrazovaný název souhlasu správce**: `Access AspNetCoreWebApi-Quickstart`
>    - **Popis souhlasu správce**: `Allows the app to access AspNetCoreWebApi-Quickstart as the signed-in user.`
>    - **Zobrazovaný název souhlasu uživatele**: `Access AspNetCoreWebApi-Quickstart`
>    - **Popis souhlasu uživatele**: `Allow the application to access AspNetCoreWebApi-Quickstart on your behalf.`
>    - **Stav**: **povoleno**
> 1. Kliknutím na **Přidat obor** dokončete přidání oboru.

## <a name="step-2-download-the-aspnet-core-project"></a>Krok 2: stažení ASP.NET Coreho projektu

> [!div renderon="docs"]
> [Stáhněte si řešení ASP.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/archive/aspnetcore3-1.zip) z GitHubu.

> [!div renderon="docs"]
> ## <a name="step-3-configure-the-aspnet-core-project"></a>Krok 3: Konfigurace ASP.NET Coreho projektu
>
> V tomto kroku nakonfigurujte vzorový kód pro práci s registrací aplikace, kterou jste vytvořili dříve.
>
> 1. Extrahujte archiv zip do složky poblíž kořene jednotky. Například do *C:\Azure-Samples*.
> 1. Otevřete řešení ve složce *WebApi* v editoru kódu.
> 1. Otevřete *appsettings.jsv* souboru a upravte následující:
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "Enter_the_Tenant_Info_Here"
>    ```
>
>    - Nahraďte `Enter_the_Application_Id_here` **ID aplikace (klienta)** aplikace, kterou jste zaregistrovali v Azure Portal. **ID aplikace (klienta)** můžete najít na stránce **Přehled** aplikace.
>    - Nahraďte `Enter_the_Tenant_Info_Here` některou z následujících možností:
>       - Pokud vaše aplikace podporuje **jenom účty v tomto organizačním adresáři**, nahraďte tuto hodnotu **ID adresáře (TENANT)** (identifikátor GUID) nebo **názvem tenanta** (například `contoso.onmicrosoft.com` ). **ID adresáře (tenant)** najdete na stránce **Přehled** aplikace.
>       - Pokud vaše aplikace podporuje režim **Účty v libovolném organizačním adresáři**, nahraďte tuto hodnotu za `organizations`.
>       - Pokud vaše aplikace podporuje **všechny účet Microsoft uživatele**, ponechte tuto hodnotu jako `common`
>
> V tomto rychlém startu Neměňte žádné jiné hodnoty v *appsettings.js* souboru.

## <a name="how-the-sample-works"></a>Jak ukázka funguje

Webové rozhraní API obdrží token z klientské aplikace a kód ve webovém rozhraní API ověří token. Tento scénář se podrobněji vysvětluje ve [scénáři: chráněné webové rozhraní API](scenario-protected-web-api-overview.md).

### <a name="startup-class"></a>Spouštěcí třída

Middleware *Microsoft. AspNetCore. Authentication* používá `Startup` třídu, která se spustí při inicializaci hostitelského procesu. Ve své `ConfigureServices` metodě `AddMicrosoftIdentityWebApi` je volána metoda rozšíření poskytovaná *Microsoft. identity. Web* .

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
                .AddMicrosoftIdentityWebApi(Configuration, "AzureAd");
    }
```

`AddAuthentication()`Metoda nakonfiguruje službu pro přidání ověřování založeného na JwtBearer.

Řádek, který obsahuje `.AddMicrosoftIdentityWebApi` , přidá do vašeho webového rozhraní API autorizaci platformy Microsoft identity. Pak je nakonfigurovaná tak, aby ověřovala přístupové tokeny vydané platformou Microsoft identity na základě informací v `AzureAD` části *appsettings.jsv* konfiguračním souboru:

| *appsettings.jsna* klíč | Description                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | **ID aplikace (klienta)** aplikace zaregistrované v Azure Portal.                                                                                       |
| `Instance`             | Koncový bod služby tokenu zabezpečení (STS) pro ověření uživatele. Tato hodnota se obvykle `https://login.microsoftonline.com/` označuje jako veřejný cloud Azure. |
| `TenantId`             | Název vašeho tenanta nebo jeho ID klienta (identifikátor GUID) nebo *běžné* pro přihlašování uživatelů pomocí pracovních nebo školních účtů nebo osobních účtů společnosti Microsoft.                             |

`Configure()`Metoda obsahuje dvě důležité metody `app.UseAuthentication()` a `app.UseAuthorization()` , které umožňují jejich pojmenované funkce:

```csharp
// This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // more code
    app.UseAuthentication();
    app.UseAuthorization();
    // more code
}
```

### <a name="protect-a-controller-a-controllers-method-or-a-razor-page"></a>Ochrana kontroleru, metody kontroleru nebo stránky Razor

Pomocí atributu můžete chránit metody kontroleru nebo kontroleru `[Authorize]` . Tento atribut omezuje přístup k řadiči nebo metodám tím, že povoluje pouze ověřené uživatele, což znamená, že je možné spustit ověřovací výzvu pro přístup k řadiči, pokud se uživatel neověřuje.

```csharp
namespace webapi.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
```

### <a name="validate-the-scope-in-the-controller"></a>Ověřit obor v kontroleru

Kód v rozhraní API pak ověří, jestli jsou požadované obory v tokenu, pomocí `HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);`

```csharp
namespace webapi.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
    {
        // The Web API will only accept tokens 1) for users, and 2) having the "access_as_user" scope for this API
        static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

        [HttpGet]
        public IEnumerable<WeatherForecast> Get()
        {
            HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

            // some code here
        }
    }
}
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další kroky

Úložiště GitHub, které obsahuje tento ASP.NET Core ukázkový kód webového rozhraní API obsahuje pokyny a další ukázky kódu, které vám ukážou, jak:

- Přidání ověřování do nového ASP.NET Core webového rozhraní API
- Volání webového rozhraní API z desktopové aplikace
- Volání rozhraní API pro příjem dat, jako jsou Microsoft Graph a další rozhraní API Microsoftu

> [!div class="nextstepaction"]
> [Kurzy ASP.NET Core Web API na GitHubu](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2)
