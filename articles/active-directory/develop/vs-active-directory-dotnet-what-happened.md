---
title: Změny provedené v projektu MVC při připojení ke službě Azure AD
description: Popisuje, co se stane s projektem MVC při připojení ke službě Azure AD pomocí připojených služeb sady Visual Studio.
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: devx-track-csharp, aaddev, vs-azure
ms.openlocfilehash: ab72512b2e5d915dbd4b8a3d7d8446a5932eef9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88165443"
---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>Co se stalo s mým projektem MVC (připojená služba Visual Studio Azure Active Directory)?

> [!div class="op_single_selector"]
> - [Začínáme](vs-active-directory-dotnet-getting-started.md)
> - [Co se přihodilo](vs-active-directory-dotnet-what-happened.md)

Tento článek popisuje přesné změny provedené v projektu ASP.NET MVC při přidávání [Azure Active Directory připojené služby pomocí sady Visual Studio](vs-active-directory-add-connected-service.md).

Informace o práci s připojenou službou najdete v tématu [Začínáme](vs-active-directory-dotnet-getting-started.md).

## <a name="added-references"></a>Přidané odkazy

Má vliv na soubor projektu (*. NET References) a `packages.config` (odkazy na NuGet).

| Typ | Reference |
| --- | --- |
| Pohyby NuGet | Microsoft. IdentityModel. Protocol. Extensions |
| Pohyby NuGet | Microsoft. Owin |
| Pohyby NuGet | Microsoft.Owin.Host.SystemWeb |
| Pohyby NuGet | Microsoft. Owin. Security |
| Pohyby NuGet | Microsoft.Owin.Security.Cookies |
| Pohyby NuGet | Microsoft.Owin.Security.OpenIdConnect |
| Pohyby NuGet | Owin |
| .NET        | System. IdentityModel |
| Pohyby NuGet | System. IdentityModel. Tokens. JWT |
| .NET        | System.Runtime.Serialization |

Další odkazy, pokud jste vybrali možnost **data pro čtení adresáře** :

| Typ | Reference |
| --- | --- |
| Pohyby NuGet | EntityFramework |
| .NET        | EntityFramework. SqlServer (pouze Visual Studio 2015) |
| Pohyby NuGet | Microsoft. Azure. Active. GraphClient |
| Pohyby NuGet | Microsoft. data. Edm |
| Pohyby NuGet | Microsoft. data. OData |
| Pohyby NuGet | Microsoft. data. Services. Client |
| Pohyby NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft. IdentityModel. clients. Active. WindowsForms (pouze Visual Studio 2015) |
| Pohyby NuGet | System. prostor |

Následující odkazy jsou odebrány (pouze projekty ASP.NET 4, jako v aplikaci Visual Studio 2015):

| Typ | Reference |
| --- | --- |
| Pohyby NuGet | Microsoft. AspNet. identity. Core |
| Pohyby NuGet | Microsoft. AspNet. identity. EntityFramework |
| Pohyby NuGet | Microsoft. AspNet. identity. Owin |

## <a name="project-file-changes"></a>Změny souborů projektu

- Nastavte vlastnost `IISExpressSSLPort` na jedinečné číslo.
- Nastavte vlastnost `WebProject_DirectoryAccessLevelKey` na hodnotu 0 nebo 1, pokud jste vybrali možnost **číst data adresáře** .
- Nastavte vlastnost `IISUrl` na `https://localhost:<port>/` hodnotu, kde `<port>` odpovídá `IISExpressSSLPort` hodnotě.

## <a name="webconfig-or-appconfig-changes"></a>Změny web.config nebo app.config

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

- Přidány `<dependentAssembly>` prvky pod `<runtime><assemblyBinding>` uzlem pro `System.IdentityModel.Tokens.Jwt` a `Microsoft.IdentityModel.Protocol.Extensions` .

Další změny, pokud jste vybrali možnost **číst data adresáře** :

- Do této položky se přidala následující položka konfigurace `<appSettings>` :

    ```xml
    <add key="ida:ClientSecret" value="<Azure AD app's new client secret>" />
    ```

- Do části se přidaly následující prvky `<configuration>` : hodnoty pro projekt-MDF-File a Project-Catalog-ID se budou lišit:

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

- Přidány `<dependentAssembly>` prvky pod `<runtime><assemblyBinding>` uzlem pro `Microsoft.Data.Services.Client` , `Microsoft.Data.Edm` a `Microsoft.Data.OData` .

## <a name="code-changes-and-additions"></a>Změny kódu a přidání

- Přidání `[Authorize]` atributu do `Controllers/HomeController.cs` a dalších existujících řadičů.

- Přidala se třída pro spuštění ověřování, `App_Start/Startup.Auth.cs` která obsahuje logiku spouštění pro ověřování Azure AD. Pokud jste vybrali možnost **číst data adresáře** , tento soubor také obsahuje kód pro příjem kódu OAuth a jeho výměnu pro přístupový token.

- Přidala se třída kontroleru, `Controllers/AccountController.cs` která `SignIn` obsahuje `SignOut` metody a.

- Přidání částečného zobrazení, `Views/Shared/_LoginPartial.cshtml` které obsahuje odkaz akce pro `SignIn` a `SignOut` .

- Přidání částečného zobrazení, které `Views/Account/SignoutCallback.cshtml` obsahuje kód HTML pro přihlašovací uživatelské rozhraní.

- Metoda aktualizovala `Startup.Configuration` tak, aby zahrnovala volání, `ConfigureAuth(app)` Pokud třída již existovala; v opačném případě přidala `Startup` třídu, která obsahuje volání metody.

- Přidáno `Connected Services/AzureAD/ConnectedService.json` (Visual studio 2017) nebo `Service References/Azure AD/ConnectedService.json` (visual Studio 2015) obsahující informace, které Visual Studio používá ke sledování přidávání připojené služby.

- Pokud jste vybrali možnost **číst data adresáře** , přidali `Models/ADALTokenCache.cs` a `Models/ApplicationDbContext.cs` pro podporu ukládání tokenu do mezipaměti. Přidali jsme také další kontroler a zobrazení pro ilustraci přístupu k informacím o profilu uživatele pomocí rozhraní API Azure Graph: `Controllers/UserProfileController.cs` , `Views/UserProfile/Index.cshtml` a. `Views/UserProfile/Relogin.cshtml`

### <a name="file-backup-visual-studio-2015"></a>Zálohování souborů (Visual Studio 2015)

Při přidávání připojené služby Visual Studio 2015 zálohované a odebrané soubory se změnily. Všechny ovlivněné soubory jsou uloženy ve složce `Backup/AzureAD` . Visual Studio 2017 a novější nevytváří zálohy.

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

- [Scénáře ověřování pro Azure Active Directory](./authentication-vs-authorization.md)
- [Přidání přihlašování do webové aplikace ASP.NET pomocí Microsoftu](quickstart-v2-aspnet-webapp.md)