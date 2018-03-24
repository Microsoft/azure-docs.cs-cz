---
title: Zabezpečení webového rozhraní API – ASP.NET – Azure Active Directory B2C | Dokumentace Microsoftu
description: Postup pro sestavení webového rozhraní API .NET pomocí Azure Active Directory B2C zabezpečeného použitím přístupových tokenů OAuth 2.0 pro ověřování.
services: active-directory-b2c
documentationcenter: .net
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 01/14/2018
ms.author: davidmu
ms.openlocfilehash: c64ca3546c5c2d21b00b379c297c9265b264cb9a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-build-a-net-web-api"></a>Azure Active Directory B2C: Sestavení webového rozhraní API .NET

S Azure Active Directory (Azure AD) B2C můžete zabezpečit webové rozhraní API pomocí přístupových tokenů OAuth 2.0. Tyto tokeny umožňují ověření přístupu klientských aplikací do rozhraní API. Tento článek ukazuje, jak vytvořit rozhraní API „seznam úkolů“ .NET MVC, které umožňuje uživatelům vaší klientské aplikace provádět CRUD u úloh. Webové rozhraní API je zabezpečeno pomocí Azure AD B2C a umožňuje pouze ověřeným uživatelům spravovat své seznamy úkolů.

## <a name="create-an-azure-ad-b2c-directory"></a>Vytvoření adresáře Azure AD B2C

Před použitím Azure AD B2C musíte vytvořit adresář, nebo klienta. Adresář je kontejner pro všechny vaše uživatele, aplikace, skupiny a další. Pokud ho ještě nemáte, [vytvořte adresář B2C](active-directory-b2c-get-started.md) předtím, než budete pokračovat.

> [!NOTE]
> Klientská aplikace a webové rozhraní API musí používat stejný adresář Azure AD B2C.
>

## <a name="create-a-web-api"></a>Vytvoření webového rozhraní API

Dále musíte vytvořit aplikaci webového rozhraní API v adresáři B2C. Azure AD díky tomu získá informace potřebné k bezpečné komunikaci s vaší aplikací. Chcete-li vytvořit aplikaci, postupujte podle [těchto pokynů](active-directory-b2c-app-registration.md). Ujistěte se, že:

* Jste do aplikace zahrnuli **webovou aplikaci** nebo **webové rozhraní API**.
* Použijte **Identifikátor URI pro přesměrování** `https://localhost:44332/` pro webovou aplikaci. Toto je výchozí umístění klienta webové aplikace pro tuto ukázku kódu.
* Poznamenejte si **ID aplikace** přiřazené vaší aplikaci. Budete ho potřebovat později.
* Zadejte identifikátor aplikace do **Identifikátor URI ID aplikace**. Zkopírujte celý **Identifikátor URI ID aplikace**. Budete ho potřebovat později.
* Přidejte oprávnění prostřednictvím nabídky **Publikované obory**.

## <a name="create-your-policies"></a>Vytvořte svoje zásady

V Azure AD B2C je každé uživatelské rozhraní definováno [zásadou](active-directory-b2c-reference-policies.md). Budete muset vytvořit zásadu pro komunikaci s Azure AD B2C. Doporučujeme používat kombinovanou zásadu registrace/přihlášení, jak je popsáno v [článku o zásadách](active-directory-b2c-reference-policies.md). Při vytváření zásady nezapomeňte na následující:

* Zvolit **Zobrazovaný název** a další atributy registrace ve svojí zásadě.
* Zvolit **Zobrazovaný název** a deklarace identity **ID objektu** jako deklarace identity aplikace v každé zásadě. Můžete zvolit i další deklarace identity.
* Po vytvoření každé zásady si poznamenejte její **Název**. Název zásady budete potřebovat později.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Po úspěšném vytvoření této zásady jste připraveni k sestavení aplikace.

## <a name="download-the-code"></a>Stáhněte si kód

Kód k tomuto kurzu se udržuje na [GitHubu](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi). Ukázku můžete klonovat spuštěním:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Po stažení ukázkového kódu otevřete soubor Visual Studio .sln, abyste mohli začít. Soubor řešení obsahuje dva projekty: `TaskWebApp` a `TaskService`. `TaskWebApp` je webová aplikace MVC, se kterou uživatel komunikuje. `TaskService` je back-endové webové rozhraní API aplikace, které ukládá seznam úkolů každého uživatele. Tento článek bude probírat pouze aplikaci `TaskService`. Způsob, jak vytvořit aplikaci `TaskWebApp` pomocí Azure AD B2C, najdete v [kurzu webových aplikací .NET](active-directory-b2c-devquickstarts-web-dotnet-susi.md).

### <a name="update-the-azure-ad-b2c-configuration"></a>Aktualizace konfigurace Azure AD B2C

Naše ukázka je nakonfigurovaná k použití zásad a ID klienta naše ukázkového tenanta. Pokud chcete použít vlastního tenanta, musíte provést následující akce:

1. Otevřete `web.config` v projektu `TaskService` a nahraďte následující hodnoty:
    * `ida:Tenant` názvem vašeho tenanta
    * `ida:ClientId` identifikátorem aplikace webového rozhraní API
    * `ida:SignUpSignInPolicyId` názvem zásady registrace/přihlášení

2. Otevřete `web.config` v projektu `TaskWebApp` a nahraďte následující hodnoty:
    * `ida:Tenant` názvem vašeho tenanta
    * `ida:ClientId` identifikátorem webového aplikace
    * `ida:ClientSecret` tajným klíčem webové aplikace
    * `ida:SignUpSignInPolicyId` názvem zásady registrace/přihlášení
    * `ida:EditProfilePolicyId` názvem zásady pro úpravu profilu
    * `ida:ResetPasswordPolicyId` názvem zásady pro resetování hesla
    * `api:ApiIdentifier` identifikátorem URI ID aplikace


## <a name="secure-the-api"></a>Zabezpečení rozhraní API

Pokud máte klienta, který volá vaše rozhraní API, můžete rozhraní API (například `TaskService`) zabezpečit pomocí nosných tokenů OAuth 2.0. To zajistí, že každý požadavek na vaše rozhraní API bude platný pouze tehdy, pokud požadavek má nosný token. Vaše rozhraní API může přijímat a ověřovat nosné tokeny pomocí knihovny Microsoft's Open Web Interface pro .NET (OWIN).

### <a name="install-owin"></a>Instalace OWIN

Začněte instalací ověřovacího kanálu OWIN OAuth pomocí konzoly Správce balíčků sady Visual Studio.

```Console
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskService
```

Tím se nainstaluje middleware OWIN, který bude přijímat a ověřovat nosné tokeny.

### <a name="add-an-owin-startup-class"></a>Přidání třídy pro spuštění OWIN

Přidejte třídu pro spuštění OWIN s názvem `Startup.cs` do rozhraní API.  Klikněte pravým tlačítkem myši na projekt, vyberte **Přidat** a **Nová položka**, a poté vyhledejte OWIN. Middleware OWIN při spuštění vaší aplikace vyvolá metodu `Configuration(…)`.

V naší ukázce jsme změnili deklaraci třídy na `public partial class Startup` a implementovali druhou část třídy v `App_Start\Startup.Auth.cs`. Uvnitř metody `Configuration` jsme přidali volání metody `ConfigureAuth`, která je definovaná v `Startup.Auth.cs`. Po úpravách vypadá `Startup.cs` následovně:

```CSharp
// Startup.cs

public partial class Startup
{
    // The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        // ConfigureAuth defined in other part of the class
        ConfigureAuth(app);
    }
}
```

### <a name="configure-oauth-20-authentication"></a>Konfigurace ověřování OAuth 2.0

Otevřete soubor `App_Start\Startup.Auth.cs` a implementujte metodu `ConfigureAuth(...)`. Například může vypadat takto:

```CSharp
// App_Start\Startup.Auth.cs

 public partial class Startup
    {
        // These values are pulled from web.config
        public static string AadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
        public static string Tenant = ConfigurationManager.AppSettings["ida:Tenant"];
        public static string ClientId = ConfigurationManager.AppSettings["ida:ClientId"];
        public static string SignUpSignInPolicy = ConfigurationManager.AppSettings["ida:SignUpSignInPolicyId"];
        public static string DefaultPolicy = SignUpSignInPolicy;

        /*
         * Configure the authorization OWIN middleware.
         */
        public void ConfigureAuth(IAppBuilder app)
        {
            TokenValidationParameters tvps = new TokenValidationParameters
            {
                // Accept only those tokens where the audience of the token is equal to the client ID of this app
                ValidAudience = ClientId,
                AuthenticationType = Startup.DefaultPolicy
            };

            app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
            {
                // This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
                AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(AadInstance, Tenant, DefaultPolicy)))
            });
        }
    }
```

### <a name="secure-the-task-controller"></a>Zabezpečení ovladače úkolů

Jakmile je aplikace nakonfigurovaná pro použití ověřování OAuth 2.0, můžete svoje webové rozhraní API zabezpečit přidáním značky `[Authorize]` do ovladače úkolů. To je ovladač, kde se odehrává veškerá manipulace se seznamem úkolů, takže byste měli zabezpečit celý ovladač na úrovni tříd. Pro větší kontrolu můžete značku `[Authorize]` přidat také k jednotlivým akcím.

```CSharp
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
    ...
}
```

### <a name="get-user-information-from-the-token"></a>Získání informací o uživateli z tokenu

`TasksController` ukládá úkoly do databáze, kde má každý úkol přidruženého uživatele, který úkol „vlastní“. Vlastník je identifikován pomocí **ID objektu** uživatele. (To je důvod, proč bylo nutné přidat ID objektu jako deklaraci identity aplikace ve všech vašich zásadách.)

```CSharp
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
    string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
    return userTasks;
}
```

### <a name="validate-the-permissions-in-the-token"></a>Ověření oprávnění v tokenu

Běžné požadavky pro webová rozhraní API jsou ověření „oborů“ v tokenu. Tím se zajistí, že uživatel souhlasil s oprávněními požadovanými pro přístup ke službě seznamu úkolů.

```CSharp
public IEnumerable<Models.Task> Get()
{
    if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "read")
    {
        throw new HttpResponseException(new HttpResponseMessage {
            StatusCode = HttpStatusCode.Unauthorized,
            ReasonPhrase = "The Scope claim does not contain 'read' or scope claim not found"
        });
    }
    ...
}
```

## <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

Nakonec sestavte a spusťte `TaskWebApp` a `TaskService`. Vytvořte nějaké úkoly v seznamu úkolů uživatele a všimněte si, že jsou zachované v rozhraní API i po vypnutí a restartování klienta.

## <a name="edit-your-policies"></a>Úprava zásad

Po zabezpečení rozhraní API pomocí Azure AD B2C můžete experimentovat se zásadou registrace/přihlášení a zjistit, jaký vliv mají (nebo nemají) na rozhraní API. Můžete také manipulovat s deklaracemi identity aplikace v zásadách a změnit, jaké informace o uživateli mají být přístupné v rozhraní API. Jakékoli přidané deklarace identity budou dostupné vašemu webovému rozhraní API .NET MVC v objektu `ClaimsPrincipal`, jak je popsáno výše.
