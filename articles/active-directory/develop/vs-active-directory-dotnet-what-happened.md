---
title: Změny provedené v projektu MVC při připojení ke službě Azure AD
description: Popisuje, co se stane s vaším projektem MVC při připojení k Azure AD pomocí připojených služeb Visual Studia
author: ghogen
manager: jillfra
ms.assetid: 8b24adde-547e-4ffe-824a-2029ba210216
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: ac38adba4ca0d978dd48a546bed1b1faf4fe40fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79036957"
---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>Co se stalo s mým projektem MVC (propojená služba Visual Studio Azure Active Directory)?

> [!div class="op_single_selector"]
> - [Začínáme](vs-active-directory-dotnet-getting-started.md)
> - [Co se přihodilo](vs-active-directory-dotnet-what-happened.md)

Tento článek identifikuje přesné změny provedené v projektu ASP.NET MVC při přidávání [připojené služby Azure Active Directory pomocí sady Visual Studio](vs-active-directory-add-connected-service.md).

Informace o práci s připojenou službou naleznete v [tématu Začínáme](vs-active-directory-dotnet-getting-started.md).

## <a name="added-references"></a>Přidané odkazy

Ovlivňuje soubor projektu (*.NET odkazy) a `packages.config` (NuGet odkazy).

| Typ | Odkaz |
| --- | --- |
| .NET; NuGet | Rozšíření Microsoft.IdentityModel.Protocol.Extensions |
| .NET; NuGet | Microsoft.Owin |
| .NET; NuGet | Microsoft.Owin.Host.SystemWeb |
| .NET; NuGet | Microsoft.Owin.Security |
| .NET; NuGet | Microsoft.Owin.Security.Cookies |
| .NET; NuGet | Microsoft.Owin.Security.OpenIdConnect |
| .NET; NuGet | Owin (Owin) |
| .NET        | Model System.IdentityModel |
| .NET; NuGet | System.IdentityModel.Tokens.Jwt |
| .NET        | System.Runtime.Serialization |

Další odkazy, pokud jste vybrali možnost **Číst data adresáře:**

| Typ | Odkaz |
| --- | --- |
| .NET; NuGet | EntityFramework |
| .NET        | EntityFramework.SqlServer (pouze Visual Studio 2015) |
| .NET; NuGet | Klient Microsoft.Azure.ActiveDirectory.Graph |
| .NET; NuGet | Microsoft.Data.Edm |
| .NET; NuGet | Microsoft.Data.OData |
| .NET; NuGet | Microsoft.data.Services.client |
| .NET; NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms (pouze Visual Studio 2015) |
| .NET; NuGet | System.Spatial |

Následující odkazy jsou odebrány (ASP.NET pouze 4 projekty, jako v Sadě Visual Studio 2015):

| Typ | Odkaz |
| --- | --- |
| .NET; NuGet | Microsoft.AspNet.Identity.Core |
| .NET; NuGet | Microsoft.AspNet.Identity.EntityFramework |
| .NET; NuGet | Soubor Microsoft.AspNet.Identity.Owin |

## <a name="project-file-changes"></a>Změny souboru projektu

- Nastavte vlastnost `IISExpressSSLPort` na odlišné číslo.
- Nastavte vlastnost `WebProject_DirectoryAccessLevelKey` na 0 nebo 1, pokud jste vybrali možnost **Číst data adresáře.**
- Nastavte vlastnost `IISUrl` `https://localhost:<port>/` na `<port>` místo, kde odpovídá hodnotě. `IISExpressSSLPort`

## <a name="webconfig-or-appconfig-changes"></a>web.config nebo app.config se mění

- Přidány následující položky konfigurace:

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="<your selected Azure domain>" />
        <add key="ida:TenantId" value="<the Id of your selected Azure AD tenant>" />
        <add key="ida:PostLogoutRedirectUri" value="<project start page, such as https://localhost:44335>" />
    </appSettings>
    ```

- Přidány `<dependentAssembly>` prvky `<runtime><assemblyBinding>` pod `System.IdentityModel.Tokens.Jwt` uzlem pro a `Microsoft.IdentityModel.Protocol.Extensions`.

Další změny, pokud jste vybrali možnost **Číst data adresáře:**

- V části byla `<appSettings>`přidána následující položka konfigurace :

    ```xml
    <add key="ida:ClientSecret" value="<Azure AD app's new client secret>" />
    ```

- Do aplikace byly `<configuration>`přidány následující prvky ; hodnoty pro project-mdf-file a project-catalog-id se budou lišit:

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

- Přidány `<dependentAssembly>` prvky `<runtime><assemblyBinding>` pod `Microsoft.Data.Services.Client`uzel `Microsoft.Data.Edm`pro `Microsoft.Data.OData`, a .

## <a name="code-changes-and-additions"></a>Změny kódu a dodatky

- Byl `[Authorize]` přidán `Controllers/HomeController.cs` atribut a všechny ostatní existující řadiče.

- Byla přidána třída `App_Start/Startup.Auth.cs`spuštění ověřování , která obsahuje logiku spuštění pro ověřování Azure AD. Pokud jste vybrali možnost **Číst data adresáře,** tento soubor také obsahuje kód pro příjem kódu OAuth a jeho výměnu za přístupový token.

- Přidána třída `Controllers/AccountController.cs`kontroleru `SignIn` , `SignOut` obsahující a metody.

- Bylo přidáno `Views/Shared/_LoginPartial.cshtml`částečné zobrazení , obsahující `SignIn` `SignOut`odkaz na akci pro a .

- Přidáno částečné `Views/Account/SignoutCallback.cshtml`zobrazení , obsahující HTML pro odhlašovací uI.

- Byla `Startup.Configuration` aktualizována metoda tak, aby zahrnovala volání, `ConfigureAuth(app)` pokud třída již existovala; jinak přidal `Startup` třídu, která zahrnuje volání metody.

- Přidáno `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) nebo `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015), obsahující informace, které Visual Studio používá ke sledování přidání připojené služby.

- Pokud jste vybrali možnost Číst `Models/ADALTokenCache.cs` data `Models/ApplicationDbContext.cs` **adresáře,** byla přidána a podporovala ukládání tokenů do mezipaměti. Přidali také další řadič a zobrazení pro ilustraci `Controllers/UserProfileController.cs`přístupu k informacím o profilu uživatele pomocí rozhraní API grafu Azure: , `Views/UserProfile/Index.cshtml`a`Views/UserProfile/Relogin.cshtml`

### <a name="file-backup-visual-studio-2015"></a>Zálohování souborů (Visual Studio 2015)

Při přidávání připojené služby Visual Studio 2015 zálohy změněné a odebrané soubory. Všechny ohrožené soubory jsou `Backup/AzureAD`uloženy ve složce . Visual Studio 2017 a novější nevytváří zálohy.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ManageViewModels.cs`
- `Views\Shared\_LoginPartial.cshtml`

## <a name="changes-on-azure"></a>Změny v Azure

- Vytvořil aplikaci Azure AD v doméně, kterou jste vybrali při přidávání připojené služby.
- Byla aktualizována aplikace tak, aby zahrnovala oprávnění **Číst data adresáře,** pokud byla tato možnost vybrána.

[Další informace o službě Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Další kroky

- [Scénáře ověřování pro Službu Azure Active Directory](authentication-scenarios.md)
- [Přidání přihlášení s Microsoftem do ASP.NET webové aplikace](quickstart-v2-aspnet-webapp.md)
