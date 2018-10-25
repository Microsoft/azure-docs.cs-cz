---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: ac8e678d8ca006fc5b0ab52f007590ac4e334bf7
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988515"
---
## <a name="set-up-your-project"></a>Nastavení projektu

Tato část popisuje postup instalace a konfigurace ověřovacího kanálu prostřednictvím middlewaru OWIN v projektu aplikace ASP.NET pomocí OpenID Connect.

> Stáhněte si tento ukázkový projekt sady Visual Studio místo toho raději? [Stáhnete projekt](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip) a pokračujte [potřeba provádět krok konfigurace](#register-your-application) konfigurace ukázkového kódu před spuštěním.

### <a name="create-your-aspnet-project"></a>Vytvoření projektu ASP.NET

1. V sadě Visual Studio: `File` > `New` > `Project`
2. V části *Visual C# \Web*vyberte `ASP.NET Web Application (.NET Framework)`.
3. Pojmenujte svoji aplikaci a klikněte na tlačítko *OK*
4. Vyberte `Empty` a vyberte zaškrtávací políčko a přidáním `MVC` odkazy

## <a name="add-authentication-components"></a>Přidání součástí ověřování

1. V sadě Visual Studio: `Tools` > `Nuget Package Manager` > `Package Manager Console`
2. Přidejte *balíčky NuGet middlewaru OWIN* tak, že do okna konzoly Správce balíčků zadáte toto:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-libraries"></a>Informace o těchto knihoven
> Knihovny uvedené výše umožňují jednotné přihlašování pomocí OpenID Connect přes ověřování na základě souborů cookie. Po dokončení ověřování a odeslání tokenu, který reprezentuje uživatele, do aplikace, vytvoří middleware OWIN soubor cookie relace. Tento soubor cookie v prohlížeči pak používá na následné žádosti, takže uživatel nebude muset znovu zadejte heslo a je potřeba žádné další ověření.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>Konfigurace ověřovacího kanálu
Následující postup slouží k vytvoření middleware OWIN při spuštění třída ke konfiguraci ověřování OpenID Connect. Tato třída se spustí automaticky při spuštění procesu služby IIS.

> [!TIP]
> Pokud projekt nemá soubor `Startup.cs` v kořenové složce:
> 1. Klikněte pravým tlačítkem na kořenové složky projektu: > `Add` > `New Item...` > `OWIN Startup class`<br/>
> 2. Pojmenujte ji `Startup.cs`
>
>> Zkontrolujte, že vybraná třída je počáteční třídou OWIN a nikoli standardní třídou C#. Ověříte si to tak, že nad oborem názvů bude uvedeno `[assembly: OwinStartup(typeof({NameSpace}.Startup))]`.

1. Přidat *OWIN* a *Microsoft.IdentityModel* odkazuje na `Startup.cs`:

    ```csharp
    using Microsoft.Owin;
    using Owin;
    using Microsoft.IdentityModel.Protocols.OpenIdConnect;
    using Microsoft.IdentityModel.Tokens;
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    using Microsoft.Owin.Security.Notifications;
    ```

2. Počáteční třída nahraďte následujícím kódem:

    ```csharp
    public class Startup
    {
        // The Client ID is used by the application to uniquely identify itself to Azure AD.
        string clientId = System.Configuration.ConfigurationManager.AppSettings["ClientId"];

        // RedirectUri is the URL where the user will be redirected to after they sign in.
        string redirectUri = System.Configuration.ConfigurationManager.AppSettings["RedirectUri"];

        // Tenant is the tenant ID (e.g. contoso.onmicrosoft.com, or 'common' for multi-tenant)
        static string tenant = System.Configuration.ConfigurationManager.AppSettings["Tenant"];

        // Authority is the URL for authority, composed by Azure Active Directory v2.0 endpoint and the tenant name (e.g. https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0)
        string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, System.Configuration.ConfigurationManager.AppSettings["Authority"], tenant);

        /// <summary>
        /// Configure OWIN to use OpenIdConnect 
        /// </summary>
        /// <param name="app"></param>
        public void Configuration(IAppBuilder app)
        {
            app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

            app.UseCookieAuthentication(new CookieAuthenticationOptions());
            app.UseOpenIdConnectAuthentication(
                new OpenIdConnectAuthenticationOptions
                {
                    // Sets the ClientId, authority, RedirectUri as obtained from web.config
                    ClientId = clientId,
                    Authority = authority,
                    RedirectUri = redirectUri,
                    // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it is using the home page
                    PostLogoutRedirectUri = redirectUri,
                    Scope = OpenIdConnectScope.OpenIdProfile,
                    // ResponseType is set to request the id_token - which contains basic information about the signed-in user
                    ResponseType = OpenIdConnectResponseType.IdToken,
                    // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
                    // To only allow users from a single organizations, set ValidateIssuer to true and 'tenant' setting in web.config to the tenant name
                    // To allow users from only a list of specific organizations, set ValidateIssuer to true and use ValidIssuers parameter 
                    TokenValidationParameters = new TokenValidationParameters()
                    {
                        ValidateIssuer = false
                    },
                    // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to OnAuthenticationFailed method
                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed
                    }
                }
            );
        }

        /// <summary>
        /// Handle failed authentication requests by redirecting the user to the home page with an error in the query string
        /// </summary>
        /// <param name="context"></param>
        /// <returns></returns>
        private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> context)
        {
            context.HandleResponse();
            context.Response.Redirect("/?errormessage=" + context.Exception.Message);
            return Task.FromResult(0);
        }
    }
    ```

<!--start-collapse-->
> ### <a name="more-information"></a>Další informace
> Parametry, které zadáte v *OpenIDConnectAuthenticationOptions*, slouží jako souřadnice pro komunikaci aplikace s Azure AD. Vzhledem k tomu middleware OpenID Connect používá soubory cookie na pozadí, budete také muset nastavit ověřování souborů cookie jako kódu nad ukazuje. Hodnota *ValidateIssuer* dává middlewaru OpenIDConnect pokyn, aby neomezoval přístup na jednu konkrétní organizaci.
<!--end-collapse-->
