---
title: Změny provedené v projektu MVC při připojení ke službě Azure AD
description: Popisuje, co se stane s projektem MVC při připojení ke službě Azure AD pomocí připojených služeb sady Visual Studio.
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
ms.openlocfilehash: 0f426e7a87204acd4be6529f7dd11a954f23491a
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159467"
---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>Co se stalo s mým projektem MVC (připojená služba Visual Studio Azure Active Directory)?

> [!div class="op_single_selector"]
> - [Začínáme](vs-active-directory-dotnet-getting-started.md)
> - [Co se přihodilo](vs-active-directory-dotnet-what-happened.md)

Tento článek popisuje přesné změny provedené v ASP.NET projektu MVC při přidávání [propojené služby Azure Active Directory pomocí sady Visual Studio](vs-active-directory-add-connected-service.md).

Informace o práci s připojenou službou najdete v tématu [Začínáme](vs-active-directory-dotnet-getting-started.md).

## <a name="added-references"></a>Přidané odkazy

Má vliv na soubor projektu *. NET References) a `packages.config` (odkazy na NuGet).

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

Další odkazy, pokud jste vybrali možnost **data pro čtení adresáře** :

| Typ | Referenční informace |
| --- | --- |
| .NET; NuGet | EntityFramework |
| .NET        | EntityFramework. SqlServer (pouze Visual Studio 2015) |
| .NET; NuGet | Microsoft.Azure.ActiveDirectory.GraphClient |
| .NET; NuGet | Microsoft.Data.Edm |
| .NET; NuGet | Microsoft.Data.OData |
| .NET; NuGet | Microsoft.Data.Services.Client |
| .NET; NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft. IdentityModel. clients. Active. WindowsForms (pouze Visual Studio 2015) |
| .NET; NuGet | System. prostor |

Následující odkazy jsou odebrány (pouze projekty ASP.NET 4, jako v aplikaci Visual Studio 2015):

| Typ | Referenční informace |
| --- | --- |
| .NET; NuGet | Microsoft.AspNet.Identity.Core |
| .NET; NuGet | Microsoft.AspNet.Identity.EntityFramework |
| .NET; NuGet | Microsoft.AspNet.Identity.Owin |

## <a name="project-file-changes"></a>Změny souborů projektu

- Nastavte vlastnost `IISExpressSSLPort` na jedinečné číslo.
- Nastavte vlastnost `WebProject_DirectoryAccessLevelKey` na hodnotu 0 nebo 1, pokud jste vybrali možnost **číst data adresáře** .
- Nastavte vlastnost `IISUrl` na `https://localhost:<port>/`, kde `<port>` odpovídá hodnotě `IISExpressSSLPort`.

## <a name="webconfig-or-appconfig-changes"></a>změny souboru Web. config nebo App. config

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

- Přidány `<dependentAssembly>` prvky pod uzlem `<runtime><assemblyBinding>` pro `System.IdentityModel.Tokens.Jwt` a `Microsoft.IdentityModel.Protocol.Extensions`.

Další změny, pokud jste vybrali možnost **číst data adresáře** :

- Do `<appSettings>`přidat následující položku konfigurace:

    ```xml
    <add key="ida:ClientSecret" value="<Azure AD app's new client secret>" />
    ```

- Do `<configuration>`byly přidány následující prvky: hodnoty pro projekt-MDF-File a Project-Catalog-ID se budou lišit:

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

- Přidány `<dependentAssembly>` prvky pod uzlem `<runtime><assemblyBinding>` pro `Microsoft.Data.Services.Client`, `Microsoft.Data.Edm`a `Microsoft.Data.OData`.

## <a name="code-changes-and-additions"></a>Změny kódu a přidání

- Přidání atributu `[Authorize]` do `Controllers/HomeController.cs` a dalších existujících řadičů.

- Přidala se spouštěcí třída ověřování, `App_Start/Startup.Auth.cs`, která obsahuje spouštěcí logiku pro ověřování Azure AD. Pokud jste vybrali možnost **číst data adresáře** , tento soubor také obsahuje kód pro příjem kódu OAuth a jeho výměnu pro přístupový token.

- Přidala se třída kontroleru `Controllers/AccountController.cs`, která obsahuje metody `SignIn` a `SignOut`.

- Přidání částečného zobrazení `Views/Shared/_LoginPartial.cshtml`, které obsahuje odkaz akce pro `SignIn` a `SignOut`.

- Přidání částečného zobrazení, `Views/Account/SignoutCallback.cshtml`obsahujícího kód HTML pro přihlašovací uživatelské rozhraní.

- Byla aktualizována metoda `Startup.Configuration`, aby zahrnovala volání `ConfigureAuth(app)`, pokud třída již existovala; v opačném případě přidali třídu `Startup`, která obsahuje volání metody.

- Přidáno `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) nebo `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015) obsahující informace, které Visual Studio používá ke sledování přidávání připojené služby.

- Pokud jste vybrali možnost **číst data adresáře** , přidali `Models/ADALTokenCache.cs` a `Models/ApplicationDbContext.cs`, aby se podporovalo ukládání tokenu do mezipaměti. Přidali jsme také další kontroler a zobrazení pro ilustraci přístupu k informacím o profilu uživatele pomocí rozhraní API Azure Graph: `Controllers/UserProfileController.cs`, `Views/UserProfile/Index.cshtml`a `Views/UserProfile/Relogin.cshtml`

### <a name="file-backup-visual-studio-2015"></a>Zálohování souborů (Visual Studio 2015)

Při přidávání připojené služby Visual Studio 2015 zálohované a odebrané soubory se změnily. Všechny ovlivněné soubory jsou uloženy ve složce `Backup/AzureAD`. Visual Studio 2017 a novější nevytváří zálohy.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ManageViewModels.cs`
- `Views\Shared\_LoginPartial.cshtml`

## <a name="changes-on-azure"></a>Změny v Azure

- V doméně, kterou jste vybrali při přidávání připojené služby, se vytvořila aplikace služby Azure AD.
- Aplikace se aktualizovala tak, aby zahrnovala oprávnění **číst data adresáře** , pokud se tato možnost vybrala.

[Přečtěte si další informace o Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Další kroky

- [Scénáře ověřování pro Azure Active Directory](authentication-scenarios.md)
- [Přidání přihlašování do webové aplikace ASP.NET pomocí Microsoftu](quickstart-v2-aspnet-webapp.md)
