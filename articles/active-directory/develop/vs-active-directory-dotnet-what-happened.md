---
title: "Změny provedené na projekt MVC při připojení k Azure AD | Microsoft Docs"
description: "Popisuje, co se stane do projektu MVC při připojení k Azure AD pomocí sady Visual Studio připojené služby"
services: active-directory
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 8b24adde-547e-4ffe-824a-2029ba210216
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: b17c5fe500f3e2a8370ec5c4a09b62737d9afb84
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>Co se stalo s MVC projektu (Visual Studio Azure Active Directory připojeno service)?

> [!div class="op_single_selector"]
> - [Začínáme](vs-active-directory-dotnet-getting-started.md)
> - [Co se přihodilo](vs-active-directory-dotnet-what-happened.md)

Tento článek identifikuje přesný změny provedené v projektu ASP.NET MVC při přidávání am [Azure Active Directory připojení služby pomocí sady Visual Studio](vs-active-directory-add-connected-service.md).

Informace o práci s připojené služby najdete v tématu [Začínáme](vs-active-directory-dotnet-getting-started.md).

## <a name="added-references"></a>Přidání odkazů

Ovlivňuje odkazy na projekt souboru *.NET) a `packages.config` (odkazů NuGet).

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

Další informace, pokud jste vybrali **čtení dat adresáře** možnost:

| Typ | Referenční informace |
| --- | --- |
| .NET; NuGet | EntityFramework |
| .NET        | EntityFramework.SqlServer (Visual Studio 2015 pouze) |
| .NET; NuGet | Microsoft.Azure.ActiveDirectory.GraphClient |
| .NET; NuGet | Microsoft.Data.Edm |
| .NET; NuGet | Microsoft.Data.OData |
| .NET; NuGet | Microsoft.Data.Services.Client |
| .NET; NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms (Visual Studio 2015 pouze) |
| .NET; NuGet | System.Spatial |

Následující odkazy jsou odebrány (technologii ASP.NET 4 projekty pouze jako v sadě Visual Studio 2015):

| Typ | Referenční informace |
| --- | --- |
| .NET; NuGet | Microsoft.AspNet.Identity.Core |
| .NET; NuGet | Microsoft.AspNet.Identity.EntityFramework |
| .NET; NuGet | Microsoft.AspNet.Identity.Owin |

## <a name="project-file-changes"></a>Změny souborů projektu

- Nastavte vlastnost `IISExpressSSLPort` jedinečné číslo.
- Nastavte vlastnost `WebProject_DirectoryAccessLevelKey` na hodnotu 0 nebo 1, pokud jste vybrali **čtení dat adresáře** možnost.
- Nastavte vlastnost `IISUrl` k `https://localhost:<port>/` kde `<port>` odpovídá `IISExpressSSLPort` hodnotu.

## <a name="webconfig-or-appconfig-changes"></a>soubor Web.config nebo app.config změny

- Byla přidána následující položky konfigurace:

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="<your selected Azure domain>" />
        <add key="ida:TenantId" value="<the Id of your selected Azure AD tenant>" />
        <add key="ida:PostLogoutRedirectUri" value="<project start page, such as https://localhost:44335>" />
    </appSettings>
    ```

- Přidat `<dependentAssembly>` elementů v rámci `<runtime><assemblyBinding>` uzel pro `System.IdentityModel.Tokens.Jwt` a `Microsoft.IdentityModel.Protocol.Extensions`.

Další změny, pokud jste vybrali **čtení dat adresáře** možnost:

- Přidat tuto položku konfigurace v rámci `<appSettings>`:

    ```xml
    <add key="ida:ClientSecret" value="<Azure AD app's new client secret>" />
    ```

- Přidat následující prvky pod `<configuration>`; hodnoty pro soubor mdf projektu a id katalogu projektu se liší:

    ```xml
    <configSections>
      <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
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

- Přidat `<dependentAssembly>` elementů v rámci `<runtime><assemblyBinding>` uzel pro `Microsoft.Data.Services.Client`, `Microsoft.Data.Edm`, a `Microsoft.Data.OData`.

## <a name="code-changes-and-additions"></a>Změny kódu a přidání

- Přidat `[Authorize]` atribut `Controllers/HomeController.cs` a všechny ostatní existující řadiče.

- Přidat třídu spuštění ověřování `App_Start/Startup.Auth.cs`, obsahující logika spuštění pro ověřování Azure AD. Pokud jste vybrali **čtení dat adresáře** možnost, tento soubor zároveň obsahuje kód pro příjem kódu OAuth a exchange pro přístupový token.

- Přidání třídy kontroleru, `Controllers/AccountController.cs`, obsahující `SignIn` a `SignOut` metody.

- Přidat částečné zobrazení `Views/Shared/_LoginPartial.cshtml`, který obsahuje odkaz akce pro `SignIn` a `SignOut`.

- Přidat částečné zobrazení `Views/Account/SignoutCallback.cshtml`, obsahující HTML pro odhlášení uživatelského rozhraní.

- Aktualizovat `Startup.Configuration` tak, aby zahrnoval volání `ConfigureAuth(app)` Pokud třída již existuje; jinak přidán `Startup` třídu, která zahrnuje volá metodu.

- Přidat `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) nebo `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015), obsahující informace, které Visual Studio používá ke sledování přidání připojených služeb.

- Pokud jste vybrali **čtení dat adresáře** možnost Přidat `Models/ADALTokenCache.cs` a `Models/ApplicationDbContext.cs` k podpoře ukládání tokenu do mezipaměti. Také přidat dalšího řadiče a zobrazení pro znázornění informace o přístupu k profilu uživatele pomocí Azure graph API: `Controllers/UserProfileController.cs`, `Views/UserProfile/Index.cshtml`, a `Views/UserProfile/Relogin.cshtml`

### <a name="file-backup-visual-studio-2015"></a>Zálohování souborů (Visual Studio 2015)

Při přidání připojené služby, Visual Studio 2015 zálohuje změněné a odebrání souborů. Všechny zasažené soubory jsou uloženy ve složce `Backup/AzureAD`. Visual Studio 2017 nedojde k vytvoření zálohy.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ManageViewModels.cs`
- `Views\Shared\_LoginPartial.cshtml`

## <a name="changes-on-azure"></a>Změny v Azure

- Vytvořit aplikaci Azure AD v doméně, která jste vybrali při přidání připojené služby.
- Aktualizovat aplikaci zahrnout **čtení dat adresáře** oprávnění, pokud byla tato možnost vybraná.

[Další informace o službě Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Další postup

- [Scénáře ověřování pro Azure Active Directory](active-directory-authentication-scenarios.md)
- [Přidání přihlášení se společností Microsoft do webové aplikace ASP.NET](guidedsetups/active-directory-aspnetwebapp-v1.md)
