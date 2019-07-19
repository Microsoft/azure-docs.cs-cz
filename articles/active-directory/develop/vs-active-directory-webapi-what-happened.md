---
title: Změny provedené v projektu WebAPI při připojení ke službě Azure AD
description: Popisuje, co se stane s vaším projektem WebAPI při připojení ke službě Azure AD pomocí sady Visual Studio.
services: active-directory
author: ghogen
manager: douge
ms.service: active-directory
ms.subservice: develop
ms.assetid: 57630aee-26a2-4326-9dbb-ea2a66daa8b0
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19a36ca4b194d8031b4a263a092ecb52be74cdd0
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68324278"
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>Co se stalo s mým projektem WebAPI (připojená služba sady Visual Studio Azure Active Directory)

> [!div class="op_single_selector"]
> - [Začínáme](vs-active-directory-webapi-getting-started.md)
> - [Co se přihodilo](vs-active-directory-webapi-what-happened.md)

Tento článek popisuje přesné změny provedené v projektech ASP.NET WebAPI, ASP.NET jednostránkové aplikace a ASP.NET API Azure při přidávání [Azure Active Directory připojené služby pomocí sady Visual Studio](vs-active-directory-add-connected-service.md). Platí také pro projekty ASP.NET Azure Mobile Service v aplikaci Visual Studio 2015.

Informace o práci s připojenou službou najdete v tématu [Začínáme](vs-active-directory-webapi-getting-started.md).

## <a name="added-references"></a>Přidané odkazy

Má vliv na soubor projektu *. NET References `packages.config` ) a (odkazy na NuGet).

| type | Reference |
| --- | --- |
| .NET; NuGet | Microsoft.Owin |
| .NET; NuGet | Microsoft.Owin.Host.SystemWeb |
| .NET; NuGet | Microsoft.Owin.Security |
| .NET; NuGet | Microsoft.Owin.Security.ActiveDirectory |
| .NET; NuGet | Microsoft.Owin.Security.Jwt |
| .NET; NuGet | Microsoft.Owin.Security.OAuth |
| .NET; NuGet | Owin |
| .NET; NuGet | System.IdentityModel.Tokens.Jwt |

Další odkazy, pokud jste vybrali možnost **data pro čtení adresáře** :

| type | Reference |
| --- | --- |
| .NET; NuGet | EntityFramework |
| .NET        | EntityFramework. SqlServer (pouze Visual Studio 2015) |
| .NET; NuGet | Microsoft.Azure.ActiveDirectory.GraphClient |
| .NET; NuGet | Microsoft.Data.Edm |
| .NET; NuGet | Microsoft.Data.OData |
| .NET; NuGet | Microsoft.Data.Services.Client |
| .NET; NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms<br>(Pouze Visual Studio 2015) |
| .NET; NuGet | System. prostor |

Následující odkazy jsou odebrány (pouze projekty ASP.NET 4, jako v aplikaci Visual Studio 2015):

| type | Reference |
| --- | --- |
| .NET; NuGet | Microsoft.AspNet.Identity.Core |
| .NET; NuGet | Microsoft.AspNet.Identity.EntityFramework |
| .NET; NuGet | Microsoft.AspNet.Identity.Owin |

## <a name="project-file-changes"></a>Změny souborů projektu

- Nastavte vlastnost `IISExpressSSLPort` na jedinečné číslo.
- Nastavte vlastnost `WebProject_DirectoryAccessLevelKey` na hodnotu 0 nebo 1, pokud jste vybrali možnost **číst data adresáře** .
- Nastavte vlastnost `IISUrl` na `https://localhost:<port>/` `IISExpressSSLPort` hodnotu, `<port>` kde odpovídá hodnotě.

## <a name="webconfig-or-appconfig-changes"></a>změny souboru Web. config nebo App. config

- Přidány následující položky konfigurace:

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- Pouze Visual Studio 2017: Přidali jsme také následující položku v `<appSettings>`části "

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- Do `<dependentAssembly>` `<runtime><assemblyBinding>` uzlu byly přidány prvky pro `System.IdentityModel.Tokens.Jwt`.

- Pokud jste vybrali možnost **číst data adresáře** , Přidali jsme následující položku `<appSettings>`konfigurace:

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>Změny kódu a přidání

- Přidání atributu do `Controllers/ValueController.cs` a dalších existujících řadičů. `[Authorize]`

- Přidali jsme třídu `App_Start/Startup.Auth.cs`pro spuštění ověření, která obsahuje spouštěcí logiku pro ověřování Azure AD, nebo ji odpovídajícím způsobem upravit. Pokud jste vybrali možnost **číst data adresáře** , tento soubor také obsahuje kód pro příjem kódu OAuth a jeho výměnu pro přístupový token.

- (Pouze aplikace Visual Studio 2015 s aplikací ASP.NET 4) Odebrané `App_Start/IdentityConfig.cs` a přidané `Controllers/AccountController.cs`, `Models/IdentityModel.cs`a .`Providers/ApplicationAuthProvider.cs`

- Přidáno `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) nebo `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015) obsahující informace, které Visual Studio používá ke sledování přidávání připojené služby.

### <a name="file-backup-visual-studio-2015"></a>Zálohování souborů (Visual Studio 2015)

Při přidávání připojené služby Visual Studio 2015 zálohované a odebrané soubory se změnily. Všechny ovlivněné soubory jsou uloženy ve složce `Backup/AzureAD`. Visual Studio 2017 nevytváří zálohy.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ApplicationOAuthProvider.cs`

## <a name="changes-on-azure"></a>Změny v Azure

- V doméně, kterou jste vybrali při přidávání připojené služby, se vytvořila aplikace služby Azure AD.
- Aplikace se aktualizovala tak, aby zahrnovala oprávnění **číst data adresáře** , pokud se tato možnost vybrala.

[Přečtěte si další informace o Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Další kroky

- [Scénáře ověřování pro Azure Active Directory](authentication-scenarios.md)
- [Přidání přihlašování do webové aplikace ASP.NET pomocí Microsoftu](quickstart-v1-aspnet-webapp.md)
