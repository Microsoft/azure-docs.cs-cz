---
title: Azure AD webového rozhraní API .NET Začínáme | Dokumentace Microsoftu
description: Postup pro sestavení .NET MVC webového rozhraní API, která se integruje s Azure AD pro ověřování a autorizaci.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: celested
ms.reviewer: hirsin, dastrock
ms.custom: aaddev
ms.openlocfilehash: ca506d821fe3534468c0d370dd51464e5df90f79
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39504657"
---
# <a name="azure-ad-net-web-api-getting-started"></a>Azure AD webového rozhraní API .NET Začínáme
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Pokud vytváříte aplikaci, která poskytuje přístup k chráněným prostředkům, musíte vědět, jak zabránit neoprávněné přístupu k těmto prostředkům.
Díky Azure Active Directory (Azure AD) je jednoduché a nekomplikované k ochraně webového rozhraní API s využitím přístupu nosiče OAuth 2.0 tokeny pomocí jenom pár řádků kódu.

Ve webové aplikace v ASP.NET lze provádět tuto ochranu pomocí implementace společnosti Microsoft zahrnuté v rozhraní .NET Framework 4.5 komunitní middlewaru OWIN. Zde použijeme OWIN pro sestavení webového rozhraní API "Seznam úkolů", který:

* Určuje, které rozhraní API jsou chráněny.
* Ověřuje, že volání webového rozhraní API obsahují platným přístupovým tokenem.

Pro sestavení do proveďte seznamu rozhraní API, nejprve potřebujete:

1. Zaregistrovat aplikaci s Azure AD.
2. Nastavení aplikace pro použití ověřovacího kanálu OWIN.
3. Konfigurovat klientskou aplikaci pro volání webového rozhraní API.

Abyste mohli začít, [stáhnout kostru aplikace](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip) nebo [stáhnout úplnou vzorovou](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Každá je řešení sady Visual Studio 2013. Budete také potřebovat tenanta služby Azure AD, ve kterém chcete zaregistrovat aplikaci. Pokud je nemáte, [zjistěte, jak získat](quickstart-create-new-tenant.md).

## <a name="step-1-register-an-application-with-azure-ad"></a>Krok 1: Registrace aplikace v Azure AD
Pomáhají zabezpečit vaše aplikace, musíte nejprve vytvořit aplikaci ve vašem tenantovi a poskytnout pár klíčových informací o službě Azure AD.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Kliknutím na váš účet v pravém horním rohu stránky, a potom kliknutím na zvolte tenanta Azure AD **přepnout adresář** navigace a pak vyberte příslušný tenant.
 * Tento krok přeskočte, pokud jste v rámci vašeho účtu jenom k jednomu tenantovi Azure AD nebo pokud jste již vybrali odpovídající tenanta Azure AD.

3. V levém navigačním podokně klikněte na **Azure Active Directory**.

4. Klikněte na tlačítko **registrace aplikací**a pak vyberte **přidat**.

5. Postupujte podle zobrazených výzev a vytvořte nový **webovou aplikaci nebo webové rozhraní API**.
  * **Název** popíše aplikaci uživatelům. Zadejte **seznam úkolů služby**.
  * **Identifikátor Uri pro přesměrování** je kombinace schématu a řetězec, který používá Azure AD k vrátí všechny tokeny, které vaše aplikace vyžaduje. Zadejte `https://localhost:44321/` pro tuto hodnotu.

6. Z **nastavení** -> **vlastnosti** stránce pro vaši aplikaci, aktualizujte identifikátor URI ID aplikace. Zadejte identifikátor specifickým pro tenanta. Zadejte například `https://contoso.onmicrosoft.com/TodoListService`.

7. Uložte konfiguraci. Opustit portál otevřít, protože je potřeba také zaregistrovat klientské aplikace za chvíli.

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>Krok 2: Nastavení aplikace pro použití ověřovacího kanálu OWIN
K ověření příchozích požadavků a tokeny, musíte nastavit aplikaci komunikovat s Azure AD.

1. Pokud chcete začít, otevřete řešení a přidání balíčků NuGet middleware OWIN TodoListService projektu pomocí konzoly Správce balíčků.

    ```
    PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
    PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
    ```

2. Přidat třídu OWIN Startup TodoListService projekt s názvem `Startup.cs`.  Klikněte pravým tlačítkem na projekt, vyberte **přidat** > **nová položka**a poté vyhledejte **OWIN**. Middleware OWIN při spuštění vaší aplikace vyvolá metodu `Configuration(…)`.

3. Změňte deklaraci třídy na `public partial class Startup`. Už jsme implementovali část této třídy za vás do jiného souboru. V `Configuration(…)` metodu, nastavení volání `ConfgureAuth(…)` nastavení ověřování pro vaši webovou aplikaci.

    ```csharp
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
    ```

4. Otevřete soubor `App_Start\Startup.Auth.cs` a implementovat `ConfigureAuth(…)` metody. Parametry, které jste zadali v `WindowsAzureActiveDirectoryBearerAuthenticationOptions` bude sloužit jako souřadnice pro vaši aplikaci ke komunikaci s Azure AD.

    ```csharp
    public void ConfigureAuth(IAppBuilder app)
    {
        app.UseWindowsAzureActiveDirectoryBearerAuthentication(
            new WindowsAzureActiveDirectoryBearerAuthenticationOptions
            {
                Audience = ConfigurationManager.AppSettings["ida:Audience"],
                Tenant = ConfigurationManager.AppSettings["ida:Tenant"]
            });
    }
    ```

5. Nyní můžete pomocí `[Authorize]` atributy k ochraně vašich kontrolerech a akcích se ověřování nosných tokenů JSON Web Token (JWT). Uspořádání `Controllers\TodoListController.cs` třída s atributem značku authorize. Tato akce vynutí uživateli umožní přihlásit se před přístupem k této stránce.

    ```csharp
    [Authorize]
    public class TodoListController : ApiController
    {
    ```

    Když oprávnění volající úspěšně vyvolá jednu z `TodoListController` rozhraní API, tato akce může být nutné přístup k informacím o volajícím. OWIN poskytuje přístup k deklarace uvnitř tokenu nosiče prostřednictvím `ClaimsPrincpal` objektu.  

6. Běžné požadavky pro webová rozhraní API jsou ověření „oborů“ v tokenu. Tím se zajistí, že uživatel souhlasil s oprávněními požadovanými pro přístup k To udělat seznamu Service.

    ```csharp
    public IEnumerable<TodoItem> Get()
    {
        // user_impersonation is the default permission exposed by applications in Azure AD
        if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "user_impersonation")
        {
            throw new HttpResponseException(new HttpResponseMessage {
              StatusCode = HttpStatusCode.Unauthorized,
              ReasonPhrase = "The Scope claim does not contain 'user_impersonation' or scope claim not found"
            });
        }
        ...
    }
    ```

7. Otevřít `web.config` souboru v kořenovém adresáři projektu TodoListService a zadejte hodnoty konfigurace v `<appSettings>` oddílu.
  * `ida:Tenant` je název vašeho tenanta Azure AD – například contoso.onmicrosoft.com.
  * `ida:Audience` je identifikátor URI ID aplikace, aplikace, která jste zadali na webu Azure Portal.

## <a name="step-3-configure-a-client-application-and-run-the-service"></a>Krok 3: Konfigurace klientské aplikace a spustit službu
Předtím, než je vidět To udělat seznamu Service v akci, budete muset nakonfigurovat klientovi seznam úkolů tak, aby se daly tokenů z Azure AD a provádět volání do služby.

1. Přejděte zpět [webu Azure portal](https://portal.azure.com).

2. Vytvoření nové aplikace ve vašem tenantovi Azure AD a vyberte **nativní klientská aplikace** výsledný příkazovém řádku.
  * **Název** popíše aplikaci uživatelům.
  * Zadejte `http://TodoListClient/` pro **identifikátor Uri pro přesměrování** hodnotu.

3. Po dokončení registrace Azure AD a jedinečné ID přiřadí vaší aplikace. Tuto hodnotu budete potřebovat v dalších krocích, takže zkopírujte ho ze stránky aplikace.

4. Z **nastavení** stránce **požadovaná oprávnění**a pak vyberte **přidat**. Vyhledejte a vyberte k proveďte seznamu službu, přidejte **přístup TodoListService** oprávnění v rámci **delegovaná oprávnění**a potom klikněte na tlačítko **provádí**.

5. V sadě Visual Studio, otevřete `App.config` v TodoListClient projektu a poté zadejte hodnoty konfigurace v `<appSettings>` oddílu.

  * `ida:Tenant` je název vašeho tenanta Azure AD – například contoso.onmicrosoft.com.
  * `ida:ClientId` je ID aplikace, který jste zkopírovali z portálu Azure portal.
  * `todo:TodoListResourceId` je identifikátor ID URI aplikace aplikace do služby provést seznam, který jste zadali na webu Azure Portal.

## <a name="next-steps"></a>Další postup
A konečně vyčistit, sestavte a spusťte každý projekt. Pokud jste tak dosud neučinili, nyní je čas při vytváření nového uživatele ve vašem tenantovi s *. onmicrosoft.com domény. Přihlaste se ke klientovi seznam úkolů s tímto uživatelem a přidá úkoly do seznamu úkolů uživatele.

Pro srovnání je hotová ukázka (bez vašich hodnot nastavení) je k dispozici v [Githubu](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Nyní se můžete přesunout další scénářů identity.

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
