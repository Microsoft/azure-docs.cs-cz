---
title: 'Rychlý Start: Přidání přihlašování pomocí Microsoftu do webové aplikace ASP.NET | Azure'
titleSuffix: Microsoft identity platform
description: V tomto rychlém startu se dozvíte, jak implementovat přihlášení Microsoftu do webové aplikace v ASP.NET pomocí OpenID Connect.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET, contperf-fy21q1
ms.openlocfilehash: 87948ed04f7b50820d94993d4c4fbcf2dfd94b31
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "104578681"
---
# <a name="quickstart-add-microsoft-identity-platform-sign-in-to-an-aspnet-web-app"></a>Rychlý Start: přidání přihlášení k platformě Microsoft Identity Platform do webové aplikace v ASP.NET

V tomto rychlém startu si stáhnete a spustíte ukázku kódu, která ukazuje, jak se webová aplikace ASP.NET může přihlašovat uživatelům z jakékoli organizace služby Azure Active Directory (Azure AD). 

> [!div renderon="docs"]
> Následující diagram ukazuje, jak ukázková aplikace funguje:
>
> ![Diagram interakce mezi webovým prohlížečem, webovou aplikací a platformou Microsoft identity v ukázkové aplikaci.](media/quickstart-v2-aspnet-webapp/aspnetwebapp-intro.svg)
>
> ## <a name="prerequisites"></a>Předpoklady
>
> * Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)
> * [.NET Framework 4.7.2 +](https://dotnet.microsoft.com/download/visual-studio-sdks)
>
> ## <a name="register-and-download-the-app"></a>Registrace a stažení aplikace
> Máte dvě možnosti, jak začít sestavovat aplikaci: Automatická nebo ruční konfigurace.
>
> ### <a name="automatic-configuration"></a>Automatická konfigurace
> Pokud chcete svou aplikaci automaticky nakonfigurovat a potom stáhnout ukázku kódu, postupujte podle následujících kroků:
>
> 1. Přejít na <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs" target="_blank">stránku Azure Portal pro registraci aplikace</a>.
> 1. Zadejte název vaší aplikace a Vyberte **Zaregistrovat**.
> 1. Podle pokynů stáhněte a automaticky nakonfigurujte novou aplikaci jediným kliknutím.
>
> ### <a name="manual-configuration"></a>Ruční konfigurace
> Pokud chcete ručně nakonfigurovat ukázku aplikace a kódu, použijte následující postupy.
>
> #### <a name="step-1-register-your-application"></a>Krok 1: Registrace aplikace
>
> 1. Přihlaste se na <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
> 1. Máte-li přístup k více klientům, použijte filtr **adresář a odběr** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: v horní nabídce a vyberte klienta, ve kterém chcete aplikaci zaregistrovat.
> 1. Vyhledejte a vyberte **Azure Active Directory**.
> 1. V části **Spravovat** vyberte **Registrace aplikací**  >  **Nová registrace**.
> 1. Jako **název** zadejte název vaší aplikace. Zadejte například **ASPNET-Start**. Uživatelé vaší aplikace uvidí tento název a později ho můžete změnit.
> 1. Přidejte **https://localhost:44368/** do **identifikátoru URI přesměrování** a vyberte **Registrovat**.
> 1. V části **Spravovat** vyberte **ověřování**.
> 1. V části **implicitní udělení a hybridní toky** vyberte **tokeny ID**.
> 1. Vyberte **Uložit**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1: Konfigurace aplikace v Azure Portal
> Aby ukázka kódu v tomto rychlém startu fungovala, zadejte **https://localhost:44368/** pro **identifikátor URI přesměrování**.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Udělat změnu za mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![](media/quickstart-v2-aspnet-webapp/green-check.png)Vaše aplikace je už nakonfigurovaná s tímto atributem.

#### <a name="step-2-download-the-project"></a>Krok 2: Stažení projektu

> [!div renderon="docs"]
> [Stažení řešení pro Visual Studio 2019](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Spusťte projekt pomocí sady Visual Studio 2019.
> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Stažení ukázky kódu](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Krok 3: vaše aplikace je nakonfigurovaná a připravená ke spuštění.
> Nakonfigurovali jsme projekt s hodnotami vlastností vaší aplikace.

> [!div renderon="docs"]
> #### <a name="step-3-run-your-visual-studio-project"></a>Krok 3: spuštění projektu sady Visual Studio

1. Extrahujte soubor. zip do místní složky, která je blízko kořenové složky. Například extrahujte do *C:\Azure-Samples*.
   
   K tomu, aby se předešlo chybám způsobeným omezeními délky cest ve Windows, doporučujeme extrakci archivu do adresáře v blízkosti vaší jednotky.
2. Otevřete řešení v aplikaci Visual Studio (*AppModelv2-WebApp-OpenIDConnect-dotnet. sln*).
3. V závislosti na verzi sady Visual Studio možná budete muset kliknout pravým tlačítkem na projekt **AppModelv2-WebApp-OpenIDConnect-dotnet** a pak vybrat **obnovit balíčky NuGet**.
4. Otevřete konzolu Správce balíčků tak, že vyberete **Zobrazit**  >  **jinou**  >  **konzolu Správce balíčků** Windows. Potom spusťte `Update-Package Microsoft.CodeDom.Providers.DotNetCompilerPlatform -r`.

> [!div renderon="docs"]
> 5. Upravte *Web.config* a nahraďte parametry `ClientId` , `Tenant` a `redirectUri` pomocí:
>    ```xml
>    <add key="ClientId" value="Enter_the_Application_Id_here" />
>    <add key="Tenant" value="Enter_the_Tenant_Info_Here" />
>    <add key="redirectUri" value="https://localhost:44368/" />
>    ```
>    V tomto kódu:
>
>    - `Enter_the_Application_Id_here` je ID aplikace (klienta) registrace aplikace, kterou jste vytvořili dříve. Vyhledejte ID aplikace (klienta) na stránce **Přehled** aplikace v **Registrace aplikací** Azure Portal.
>    - `Enter_the_Tenant_Info_Here` je jedna z následujících možností:
>      - Pokud vaše aplikace podporuje **pouze moji organizaci**, nahraďte tuto hodnotu ID adresáře (tenant) nebo názvem tenanta (například `contoso.onmicrosoft.com` ). Vyhledejte ID adresáře (tenant) na stránce **Přehled** aplikace v **Registrace aplikací** Azure Portal.
>      - Pokud vaše aplikace podporuje **účty v jakémkoli organizačním adresáři**, nahraďte tuto hodnotu hodnotou `organizations` .
>      - Pokud vaše aplikace podporuje **všechny účet Microsoft uživatele**, nahraďte tuto hodnotu hodnotou `common` .
>    - `redirectUri` je **identifikátor URI přesměrování** , který jste zadali dříve v **Registrace aplikací** Azure Portal.
>

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

## <a name="more-information"></a>Další informace

V této části najdete přehled kódu potřebného k přihlášení uživatelů. Tento přehled může být užitečný pro pochopení, jak kód funguje, co jsou hlavní argumenty a jak přidat přihlášení do existující aplikace ASP.NET.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>Jak ukázka funguje
>
> ![Diagram interakce mezi webovým prohlížečem, webovou aplikací a platformou Microsoft identity v ukázkové aplikaci.](media/quickstart-v2-aspnet-webapp/aspnetwebapp-intro.svg)

### <a name="owin-middleware-nuget-packages"></a>Balíčky NuGet middlewaru OWIN

Ověřovací kanál můžete nastavit pomocí ověřování založeného na souborech cookie pomocí OpenID Connect v ASP.NET s balíčky middlewaru OWIN. Tyto balíčky můžete nainstalovat spuštěním následujících příkazů v konzole správce balíčků v sadě Visual Studio:

```powershell
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="owin-startup-class"></a>Třída OWIN Startup

Middleware OWIN používá *spouštěcí třídu* , která se spouští při spuštění hostitelského procesu. V tomto rychlém startu se soubor *Startup. cs* nachází v kořenové složce. Následující kód ukazuje parametry, které tento rychlý Start používá:

```csharp
public void Configuration(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());
    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            // Sets the client ID, authority, and redirect URI as obtained from Web.config
            ClientId = clientId,
            Authority = authority,
            RedirectUri = redirectUri,
            // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it's using the home page
            PostLogoutRedirectUri = redirectUri,
            Scope = OpenIdConnectScope.OpenIdProfile,
            // ResponseType is set to request the code id_token, which contains basic information about the signed-in user
            ResponseType = OpenIdConnectResponseType.CodeIdToken,
            // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
            // To only allow users from a single organization, set ValidateIssuer to true and the 'tenant' setting in Web.config to the tenant name
            // To allow users from only a list of specific organizations, set ValidateIssuer to true and use the ValidIssuers parameter
            TokenValidationParameters = new TokenValidationParameters()
            {
                ValidateIssuer = false // Simplification (see note below)
            },
            // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to the OnAuthenticationFailed method
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = OnAuthenticationFailed
            }
        }
    );
}
```

> |Kde  | Description |
> |---------|---------|
> | `ClientId`     | ID aplikace z aplikace zaregistrované v Azure Portal. |
> | `Authority`    | Koncový bod služby tokenu zabezpečení (STS) pro uživatele, který se má ověřit Obvykle je `https://login.microsoftonline.com/{tenant}/v2.0` pro veřejný cloud. V této adrese URL *{tenant}* je název vašeho TENANTA, ID tenanta nebo `common` odkaz na společný koncový bod. (Společný koncový bod se používá pro víceklientské aplikace.) |
> | `RedirectUri`  | Adresa URL, kam se po ověření na platformě Microsoft Identity odesílají uživatelé. |
> | `PostLogoutRedirectUri`     | Adresa URL, kam se po odhlášení odesílají uživatelé |
> | `Scope`     | Seznam požadovaných oborů oddělených mezerami |
> | `ResponseType`     | Požadavek, který odpověď z ověřování obsahuje autorizační kód a token ID. |
> | `TokenValidationParameters`     | Seznam parametrů pro ověřování tokenů; V tomto případě `ValidateIssuer` je nastaveno, aby `false` označoval, že může přijímat přihlášení z libovolného typu osobního, pracovního nebo školního účtu. |
> | `Notifications`     | Seznam delegátů, které lze spustit u `OpenIdConnect` zpráv. |


> [!NOTE]
> Nastavení `ValidateIssuer = false` je zjednodušení pro tento rychlý Start. V reálných aplikacích ověřte vystavitele. V ukázkách si můžete uvědomit, jak to udělat.

### <a name="authentication-challenge"></a>Ověřovací výzva

Uživatele můžete k přihlášení přinutit tím, že si v řadiči vyžádáte výzvu ověřování:

```csharp
public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties{ RedirectUri = "/" },
            OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

> [!TIP]
> Vyžadování výzvy ověřování pomocí této metody je volitelné. Normálně ho použijete, když chcete, aby zobrazení bylo dostupné z ověřených i neověřených uživatelů. Řadiče můžete zároveň chránit způsobem popsaným v další části.

### <a name="attribute-for-protecting-a-controller-or-a-controller-actions"></a>Atribut pro ochranu kontroléru nebo akcí kontroleru

Pomocí atributu můžete chránit akce kontroleru nebo kontroleru `[Authorize]` . Tento atribut omezuje přístup k řadiči nebo akcím tím, že umožňuje přístup k akcím v řadiči pouze ověřeným uživatelům. K ověření se pak automaticky dojde, když se neověřený uživatel pokusí získat přístup k jedné z akcí nebo kontrolám, které `[Authorize]` atribut dekoruje.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další kroky

Úplný podrobný průvodce vytvářením aplikací a nových funkcí, včetně úplného vysvětlení tohoto rychlého startu, najdete v kurzu ASP.NET.

> [!div class="nextstepaction"]
> [Přidání přihlašování do webové aplikace v ASP.NET](tutorial-v2-asp-webapp.md)
