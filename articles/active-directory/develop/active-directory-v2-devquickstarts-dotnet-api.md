---
title: Přidání přihlášení do .NET MVC webového rozhraní API pomocí koncového bodu v2.0 Azure AD | Microsoft Docs
description: Jak sestavit Web Api MVC .NET, které přijímá tokeny z obou osobní Account Microsoft a pracovní nebo školní účty.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: e77bc4e0-d0c9-4075-a3f6-769e2c810206
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/07/2017
ms.author: celested
ms.reviewer: dastrock
ms.custom: aaddev
ms.openlocfilehash: aa73e918cbd49fee850e402859708ba0c4185a19
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="secure-an-mvc-web-api"></a>Zabezpečení webového rozhraní API MVC
S Azure Active Directory koncový bod v2.0, budete moci chránit webového rozhraní API pomocí [OAuth 2.0](active-directory-v2-protocols.md) přístup tokeny, povolení uživatelé s i osobní účet Microsoft a pracovní nebo školní účty k bezpečnému přístupu k vaší webové rozhraní API.

> [!NOTE]
> Ne všechny scénáře Azure Active Directory a funkce jsou podporovány koncového bodu v2.0. Pokud chcete zjistit, pokud byste měli používat koncový bod v2.0, přečtěte si informace o [v2.0 omezení](active-directory-v2-limitations.md).
>
>

V rozhraní ASP.NET web API můžete to provést pomocí middlewaru OWIN společnosti Microsoft, zahrnutá v rozhraní .NET Framework 4.5. Zde použijeme OWIN sestavit Web API MVC "Seznam úkolů", která umožňuje klientům vytváření a čtení úkolů ze seznamu úkolů uživatele. Webové rozhraní API ověří, že příchozí požadavky obsahovat platné přístupový token a odmítnout všechny požadavky, které nepředávejte ověření na chráněných trase. Tato ukázka byla vytvořená s využitím sady Visual Studio 2015.

## <a name="download"></a>Ke stažení
Kód k tomuto kurzu je udržovaný [na GitHubu](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet). Chcete-li sledovat, můžete [stáhnout kostru aplikace jako ZIP](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) nebo tuto kostru klonovat:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git
```

Kostru aplikace zahrnuje často používaný kód pro jednoduché rozhraní API, ale všechny součásti související s identity chybí. Pokud nechcete, aby se podle nich zorientujete, můžete místo toho klonovat nebo [stažení je hotová ukázka](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/complete.zip).

```
git clone https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git
```

## <a name="register-an-app"></a>Registrace aplikace
Vytvoření nové aplikace v [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), nebo postupujte podle těchto [podrobné kroky](active-directory-v2-app-registration.md). Zkontrolujte, že:

* Zkopírování **Id aplikace** přiřazené vaší aplikaci, budete ho potřebovat brzy k dispozici.

Toto řešení sady visual studio také obsahuje "TodoListClient", což je jednoduchou aplikaci WPF. TodoListClient se používá k předvedení jak uživatel přihlásí, a jak můžete vydat požadavky pro webové rozhraní API klienta. V takovém případě TodoListClient i TodoListService jsou reprezentované pomocí stejné aplikaci. Pokud chcete nakonfigurovat TodoListClient, měli byste také:

* Přidat **Mobile** platformu pro vaši aplikaci.

## <a name="install-owin"></a>Instalace OWIN
Teď, když jste registrováni aplikace, musíte nastavit aplikaci ke komunikaci s koncovým bodem v2.0, aby bylo možné ověřit příchozí žádosti a tokeny.

* Pokud chcete začít, otevřete řešení a přidání balíčků NuGet middleware OWIN projekt TodoListService pomocí konzoly Správce balíčků.

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
PM> Install-Package Microsoft.IdentityModel.Protocol.Extensions -ProjectName TodoListService
```

## <a name="configure-oauth-authentication"></a>Konfigurace ověřování OAuth
* Přidejte třídu OWIN při spuštění do projektu TodoListService názvem `Startup.cs`. Klikněte pravým tlačítkem na projekt--> **přidat** --> **nová položka** --> vyhledejte "OWIN". Middleware OWIN při spuštění vaší aplikace vyvolá metodu `Configuration(…)`.
* Změňte deklaraci třídy k `public partial class Startup` -již implementovali jsme součástí této třídy pro vás v jiném souboru. V `Configuration(…)` metoda, zkontrolujte zavolá ConfgureAuth(...) nastavení ověřování pro webovou aplikaci.

```csharp
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

* Otevřete soubor `App_Start\Startup.Auth.cs` a implementovat `ConfigureAuth(…)` metoda, která bude přijímat tokeny z koncového bodu v2.0 nastavení webového rozhraní API.

```csharp
public void ConfigureAuth(IAppBuilder app)
{
        var tvps = new TokenValidationParameters
        {
                // In this app, the TodoListClient and TodoListService
                // are represented using the same Application Id - we use
                // the Application Id to represent the audience, or the
                // intended recipient of tokens.

                ValidAudience = clientId,

                // In a real applicaiton, you might use issuer validation to
                // verify that the user's organization (if applicable) has
                // signed up for the app. Here, we'll just turn it off.

                ValidateIssuer = false,
        };

        // Set up the OWIN pipeline to use OAuth 2.0 Bearer authentication.
        // The options provided here tell the middleware about the type of tokens
        // that will be recieved, which are JWTs for the v2.0 endpoint.

        // NOTE: The usual WindowsAzureActiveDirectoryBearerAuthenticaitonMiddleware uses a
        // metadata endpoint which is not supported by the v2.0 endpoint. Instead, this
        // OpenIdConenctCachingSecurityTokenProvider can be used to fetch & use the OpenIdConnect
        // metadata document.

        app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
        {
                AccessTokenFormat = new Microsoft.Owin.Security.Jwt.JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider("https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration")),
        });
}
```

* Nyní můžete pomocí `[Authorize]` atributy chránit řadiče a akce s ověřování nosiče OAuth 2.0. Uspořádání `Controllers\TodoListController.cs` se značky autorizovat. Tato akce vynutí uživatele k přihlášení před přístupem k této stránce.

```csharp
[Authorize]
public class TodoListController : ApiController
{
```

* Když oprávnění volající úspěšně vyvolá jeden z `TodoListController` rozhraní API, akce může potřebovat přístup k informacím o volajícím. OWIN poskytuje přístup k deklarace identity uvnitř tokenu nosiče prostřednictvím `ClaimsPrincipal` objektu. 

```csharp
public IEnumerable<TodoItem> Get()
{
    // You can use the ClaimsPrincipal to access information about the
    // user making the call. In this case, we use the 'sub' or
    // NameIdentifier claim to serve as a key for the tasks in the data store.

    Claim subject = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier);

    return from todo in todoBag
           where todo.Owner == subject.Value
           select todo;
}
```

* Nakonec otevřete `web.config` souboru v kořenovém adresáři projektu TodoListService a zadejte svoje hodnoty konfigurace v `<appSettings>` oddílu.
  * Vaše `ida:Audience` je **Id aplikace** aplikace, kterou jste zadali v portálu.

## <a name="configure-the-client-app"></a>Nakonfigurovat klientské aplikace
Než budete moct vidět službu seznamu úkolů v akci, budete muset nakonfigurovat klienta seznamu úkolů, můžete získat tokeny z koncového bodu v2.0 a provádět volání do služby.

* Otevřete v projektu TodoListClient `App.config` a zadejte svoje hodnoty konfigurace v `<appSettings>` oddílu.
  * Vaše `ida:ClientId` Id aplikace, které jste zkopírovali z portálu.

Nakonec vyčistit, sestavte a spusťte každý projekt!  Nyní máte rozhraní .NET MVC webové rozhraní API, které přijímá tokeny z obou osobní účty Microsoft a pracovní nebo školní účty. Přihlaste se k TodoListClient a volání webového rozhraní api pro přidání úkolů do seznamu úkolů uživatele.

Pro srovnání je hotová ukázka (bez vašich hodnot nastavení) [je k dispozici jako ZIP zde](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/complete.zip), nebo ji můžete klonovat z Githubu:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git```

## <a name="next-steps"></a>Další postup
Nyní se můžete přesunout na další témata. Můžete se pokusit:

[Volání webového rozhraní API z webové aplikace >>](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md)

Další zdroje projděte si:

* [Příručka vývojáře v2.0 >>](active-directory-appmodel-v2-overview.md)
* [Značka StackOverflow "azure-active-directory" >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Získejte bezpečnostní aktualizace našich produktů
Doporučujeme vám získávat oznámení o bezpečnostních incidentech tak, že navštívíte [tuto stránku](https://technet.microsoft.com/security/dd252948) a přihlásíte se k odběru služby Security Advisory Alerts.
