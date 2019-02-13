---
title: Vytvoření webového rozhraní API .NET, které se integruje s Azure AD pro ověřování a autorizaci | Microsoft Docs
description: Zjistěte, jak vytvořit webové rozhraní API .NET MVC, které se integruje s Azure AD pro ověřování a autorizaci.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f87573e23f2c0f48e54b6f03289969aab930e15c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56200594"
---
# <a name="quickstart-build-a-net-web-api-that-integrates-with-azure-ad-for-authentication-and-authorization"></a>Rychlý start: Vytvoření webového rozhraní .NET API, která se integruje s Azure AD pro ověřování a autorizace

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

Pokud vytváříte aplikaci, která poskytuje přístup k chráněným prostředkům, musíte vědět, jak zabránit neoprávněnému přístupu k těmto prostředkům. Azure Active Directory (Azure AD) snadno a jasně pomáhá chránit webové rozhraní API s využitím nosných přístupových tokenů OAuth 2.0 s jen pár řádky kódu.

Ve webových aplikacích ASP.NET můžete této ochrany dosáhnout pomocí implementace komunitního middlewaru OWIN, který je součástí rozhraní .NET Framework 4.5. Tady OWIN využijete k vytvoření webového rozhraní API „To Do List“ (Seznam úkolů), které:

* Určuje, která rozhraní API jsou chráněná
* Ověřuje, že volání webového rozhraní API obsahují platný přístupový token

V tomto rychlém startu vytvoříte rozhraní API To Do List a naučíte se:

1. Zaregistrovat aplikaci s Azure AD.
2. Nastavit, aby aplikace používala ověřovací kanál OWIN
3. Nakonfigurovat klientskou aplikaci, aby volala webové rozhraní API

## <a name="prerequisites"></a>Požadavky

Než začnete, musíte splnit následující požadavky:

* Stáhnout si [kostru aplikace](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip) nebo [úplnou ukázku](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Obě jsou řešeními sady Visual Studio 2013.
* Mít tenanta Azure AD, ve kterém chcete aplikaci zaregistrovat. Pokud ho ještě nemáte, [zjistěte, jak ho získat](quickstart-create-new-tenant.md).

## <a name="step-1-register-an-application-with-azure-ad"></a>Krok 1: Registrace aplikace v Azure AD

Abyste pomohli aplikaci zabezpečit, musíte nejdříve aplikaci vytvořit ve vašem tenantovi a službě Azure AD poskytnout pár klíčových informací.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Zvolte svého tenanta Azure AD tak, že vyberete svůj účet v pravém horním rohu stránky, vyberete **Přepnout adresář** a pak vyberete příslušného tenanta.
    * Tento krok přeskočte, pokud v rámci účtu máte jenom jednoho tenanta Azure AD nebo pokud jste už odpovídajícího tenanta Azure AD vybrali.

3. V levém navigačním podokně vyberte **Azure Active Directory**.
4. Vyberte **Registrace aplikací** a pak vyberte **Přidat**.
5. Postupujte podle zobrazených výzev a vytvořte novou **Webovou aplikaci / webové rozhraní API**:
    * **Název** popisuje aplikaci uživatelům. Zadejte **To Do List Service**.
    * **Identifikátor URI pro přesměrování** je schéma a kombinace řetězců, které Azure AD používá k vrácení tokenů, o které vaše aplikace požádala. Pro tuto hodnotu zadejte `https://localhost:44321/`.

6. Ze stránky **Nastavení > Vlastnosti** pro vaši aplikaci aktualizujte identifikátor URI ID aplikace. Zadejte identifikátor specifický pro tenanta. Zadejte například `https://contoso.onmicrosoft.com/TodoListService`.
7. Konfiguraci uložte. Nechte portál otevřený, protože brzy budete muset zaregistrovat klientskou aplikaci.

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>Krok 2: Nastavení aplikace pro použití ověřovacího kanálu OWIN

K ověření příchozích požadavků a tokenů musíte nastavit, aby aplikace komunikovala s Azure AD.

1. Začněte tak, že otevřete řešení a přidáte balíčky NuGet middlewaru OWIN do projektu TodoListService pomocí konzoly Správce balíčků.

    ```
    PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
    PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
    ```

2. Do projektu TodoListService přidejte třídu Startup pro spuštění OWIN s názvem `Startup.cs`.  Pravým tlačítkem myši klikněte na projekt, vyberte **Přidat > Nová položka** a pak vyhledejte **OWIN**. Middleware OWIN při spuštění vaší aplikace vyvolá metodu `Configuration(…)`.

3. Změňte deklaraci třídy na `public partial class Startup`. Část této třídy už jsme pro vás implementovali do jiného souboru. V metodě `Configuration(…)` proveďte volání do `ConfgureAuth(…)`, abyste nastavili ověřování pro vaši webovou aplikaci.

    ```csharp
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
    ```

4. Otevřete soubor `App_Start\Startup.Auth.cs` a implementujte metodu `ConfigureAuth(…)`. Parametry, které zadáte v `WindowsAzureActiveDirectoryBearerAuthenticationOptions`, budou sloužit jako souřadnice pro vaši aplikaci ke komunikaci s Azure AD.

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

5. Použijte atributy `[Authorize]` k ochraně vašich kontrolerů a akcí pomocí ověřování nosných tokenů JSON Web Token (JWT). Přidejte k třídě `Controllers\TodoListController.cs` značku Authorize, která uživatele donutí, aby se před přístupem k této stránce přihlásil.

    ```csharp
    [Authorize]
    public class TodoListController : ApiController
    {
    ```

    Když oprávněný volající úspěšně vyvolá některé z rozhraní API `TodoListController`, může akce potřebovat přístup k informacím o volajícím. OWIN poskytuje přístup k deklaraci identity uvnitř nosného tokenu prostřednictvím objektu `ClaimsPrincpal`.  

6. Běžným požadavkem pro webová rozhraní API je ověření „oborů“ přítomných v tokenu, aby se zajistilo, že uživatel souhlasil s oprávněními požadovanými pro přístup k To Do List Service.

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

7. V kořenovém adresáři projektu TodoListService otevřete soubor `web.config` a v oddílu `<appSettings>` zadejte hodnoty konfigurace.
    * `ida:Tenant` je název vašeho tenanta Azure AD – například contoso.onmicrosoft.com.
    * `ida:Audience` je identifikátor URI ID aplikace, který jste zadali na portálu Azure Portal.

## <a name="step-3-configure-a-client-application-and-run-the-service"></a>Krok 3: Konfigurovat klientskou aplikaci a spustit službu

Než uvidíte To Do List Service v akci, musíte nakonfigurovat klienta To Do List, aby mohl získávat tokeny z Azure AD a provádět volání do služby.

1. Vraťte se na portál [Azure Portal](https://portal.azure.com).
1. Vytvořte novou aplikaci v tenantovi Azure AD a ve výsledné nabídce vyberte **Nativní klientská aplikace**.
    * **Název** popisuje aplikaci uživatelům.
    * Jako hodnotu pro **Identifikátor URI pro přesměrování** zadejte `http://TodoListClient/`.

1. Po dokončení registrace přiřadí Azure AD aplikaci jedinečné ID aplikace. Tuto hodnotu budete potřebovat v následujících krocích, proto si ji ze stránky aplikace zkopírujte.
1. Na stránce **Nastavení** vyberte **Požadovaná oprávnění** a pak vyberte **Přidat**. Najděte a vyberte To Do List Service, přidejte oprávnění **Přístup k TodoListService** v rámci **Delegovaných oprávnění** a pak vyberte **Hotovo**.
1. V sadě Visual Studio otevřete `App.config` v projektu TodoListClient a pak zadejte hodnoty konfigurace v oddílu `<appSettings>`.

    * `ida:Tenant` je název vašeho tenanta Azure AD, například contoso.onmicrosoft.com.
    * `ida:ClientId` je ID aplikace, které jste zkopírovali z portálu Azure Portal.
    * `todo:TodoListResourceId` je identifikátor URI ID aplikace To Do List Service, který jste zadali na portálu Azure Portal.

1. Vyčistěte, sestavte a spusťte každý projekt.
1. Pokud jste to ještě neudělali, vytvořte ve vašem tenantovi nového uživatele s doménou *. onmicrosoft.com.
1. Pomocí tohoto uživatele se přihlaste ke klientovi To Do List a přidejte nějaké úkoly do seznamu úkolů uživatele.

## <a name="next-steps"></a>Další postup

* Pro srovnání si můžete stáhnout hotovou ukázku (bez vašich hodnot) z [GitHubu](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip). Teď můžete přejít k dalším scénářům identity.
