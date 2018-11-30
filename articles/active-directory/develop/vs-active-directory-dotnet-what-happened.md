---
title: Změny provedené na projekt MVC po připojení k Azure AD
description: Popisuje, co se stane do projektu MVC po připojení k Azure AD s použitím sady Visual Studio připojené služby
services: active-directory
author: ghogen
manager: douge
ms.assetid: 8b24adde-547e-4ffe-824a-2029ba210216
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: cc14f170a748f91289bbc644d5a9bef10697ce34
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52427357"
---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>Co se stalo s mým projektem MVC (Visual Studio Azure Active Directory připojená služba)?

> [!div class="op_single_selector"]
> - [Začínáme](vs-active-directory-dotnet-getting-started.md)
> - [Co se přihodilo](vs-active-directory-dotnet-what-happened.md)

Tento článek identifikuje přesný změny provedené v projektu ASP.NET MVC při přidávání am [Azure Active Directory připojenou službu pomocí sady Visual Studio](vs-active-directory-add-connected-service.md).

Informace o práci s připojenou službu, najdete v části [Začínáme](vs-active-directory-dotnet-getting-started.md).

## <a name="added-references"></a>Přidání odkazů

Ovlivňuje odkazy na soubory *.NET projekt) a `packages.config` (odkazy na NuGet).

| Typ | Referenční informace |
| --- | --- |
| .NET; NuGet | Microsoft.IdentityModel.Protocol.Extensions |
| .NET; NuGet | Microsoft.Owin |
| .NET; NuGet | Microsoft.Owin.Host.SystemWeb |
| .NET; NuGet | Microsoft.Owin.Security |
| .NET; NuGet | Microsoft.Owin.Security.Cookies |
| .NET; NuGet | Microsoft.Owin.Security.OpenIdConnect |
| .NET; NuGet | Owin |
| .NET        | System.IdentityModel |
| .NET; NuGet | System.IdentityModel.Tokens.Jwt |
| .NET        | System.Runtime.Serialization |

Další odkazy, pokud jste vybrali **čtení dat adresáře** možnost:

| Typ | Referenční informace |
| --- | --- |
| .NET; NuGet | Objektu EntityFramework |
| .NET        | EntityFramework.SqlServer (Visual Studio 2015 jenom) |
| .NET; NuGet | Microsoft.Azure.ActiveDirectory.GraphClient |
| .NET; NuGet | Microsoft.Data.Edm |
| .NET; NuGet | Microsoft.Data.OData |
| .NET; NuGet | Microsoft.Data.Services.Client |
| .NET; NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms (Visual Studio 2015 jenom) |
| .NET; NuGet | System.Spatial |

Odeberou se následující odkazy (technologii ASP.NET 4 pouze pro projekty, stejně jako v sadě Visual Studio 2015):

| Typ | Referenční informace |
| --- | --- |
| .NET; NuGet | Microsoft.AspNet.Identity.Core |
| .NET; NuGet | Microsoft.AspNet.Identity.EntityFramework |
| .NET; NuGet | Microsoft.AspNet.Identity.Owin |

## <a name="project-file-changes"></a>Změny v souboru projektu

- Nastavte vlastnost `IISExpressSSLPort` distinct číslo.
- Nastavte vlastnost `WebProject_DirectoryAccessLevelKey` na hodnotu 0 nebo 1, pokud jste vybrali **čtení dat adresáře** možnost.
- Nastavte vlastnost `IISUrl` k `https://localhost:<port>/` kde `<port>` odpovídá `IISExpressSSLPort` hodnotu.

## <a name="webconfig-or-appconfig-changes"></a>změny v souboru Web.config nebo app.config

- Přidá následující položky konfigurace:

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="<your selected Azure domain>" />
        <add key="ida:TenantId" value="<the Id of your selected Azure AD tenant>" />
        <add key="ida:PostLogoutRedirectUri" value="<project start page, such as https://localhost:44335>" />
    </appSettings>
    ```

- Přidání `<dependentAssembly>` prvků `<runtime><assemblyBinding>` uzel `System.IdentityModel.Tokens.Jwt` a `Microsoft.IdentityModel.Protocol.Extensions`.

Další změny, pokud jste vybrali **čtení dat adresáře** možnost:

- Přidá následující položku konfigurace v rámci `<appSettings>`:

    ```xml
    <add key="ida:ClientSecret" value="<Azure AD app's new client secret>" />
    ```

- Přidat následující prvky v rámci `<configuration>`; hodnoty pro soubor mdf projektu a projekt id katalogu se liší:

    ```xml
    <configSections>
      <!-- For more information on Entity Framework configuration, visit https://go.microsoft.com/fwlink/?LinkID=237468 -->
      <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
    </configSections>

    <connectionStrings>
      <add name="DefaultConnection" connectionString="Data Source=(localdb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\<project-mdf-file>.mdf;Initial Catalog=<project-catalog-id>;Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>

    <entityFramework>
      <defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
        <parameters>
          <parameter value="mssqllocaldb" />
        </parameters>
      </defaultConnectionFactory>
      <providers>
        <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
      </providers>
    </entityFramework>
    ```

- Přidání `<dependentAssembly>` prvků `<runtime><assemblyBinding>` uzel `Microsoft.Data.Services.Client`, `Microsoft.Data.Edm`, a `Microsoft.Data.OData`.

## <a name="code-changes-and-additions"></a>Změny kódu a doplňky

- Přidá `[Authorize]` atribut `Controllers/HomeController.cs` a žádné stávající řadiče.

- Přidá třídu spuštění ověřování `App_Start/Startup.Auth.cs`, který obsahuje spouštěcí logiky ověřování Azure AD. Pokud jste vybrali **čtení dat adresáře** možnost, tento soubor zároveň obsahuje kód pro příjem kódu OAuth a výměně pro přístupový token.

- Přidá třídu kontroleru `Controllers/AccountController.cs`, obsahující `SignIn` a `SignOut` metody.

- Přidání částečné zobrazení `Views/Shared/_LoginPartial.cshtml`, který obsahuje odkaz akce pro `SignIn` a `SignOut`.

- Přidání částečné zobrazení `Views/Account/SignoutCallback.cshtml`, obsahuje kód HTML pro odhlášení uživatelského rozhraní.

- Aktualizovat `Startup.Configuration` tak, aby zahrnoval volání `ConfigureAuth(app)` Pokud třída již existuje; jinak se přidat `Startup` třídu, která obsahuje volání metody.

- Přidání `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) nebo `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015), obsahuje informace, které Visual Studio používá ke sledování přidání připojené služby.

- Pokud jste vybrali **čtení dat adresáře** možnost Přidat `Models/ADALTokenCache.cs` a `Models/ApplicationDbContext.cs` pro podporu ukládání tokenu do mezipaměti. Také přidat další řadiče a zobrazení pro ilustraci informace o přístupu k profilu uživatele pomocí Azure graph API služby: `Controllers/UserProfileController.cs`, `Views/UserProfile/Index.cshtml`, a `Views/UserProfile/Relogin.cshtml`

### <a name="file-backup-visual-studio-2015"></a>Zálohování souborů (Visual Studio 2015)

Při přidání připojené služby Visual Studio 2015 zálohuje změněné a odebrané soubory. Všechny ovlivněné soubory jsou uloženy ve složce `Backup/AzureAD`. Visual Studio 2017 se nevytváří žádné zálohy.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ManageViewModels.cs`
- `Views\Shared\_LoginPartial.cshtml`

## <a name="changes-on-azure"></a>Změny v Azure

- Vytvoří aplikaci Azure AD v doméně, která jste vybrali při přidání připojené služby.
- Aktualizace aplikace, aby zahrnovala **čtení dat adresáře** oprávnění, pokud jste vybrali tuto možnost.

[Další informace o službě Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Další postup

- [Scénáře ověřování pro Azure Active Directory](authentication-scenarios.md)
- [Přidání přihlašování s Microsoftem do webové aplikace ASP.NET](quickstart-v1-aspnet-webapp.md)
