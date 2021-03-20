---
title: Změny provedené v projektech WebAPI při připojování k Azure AD
description: Popisuje, co se stane s vaším projektem WebAPI při připojování k Azure AD pomocí sady Visual Studio.
author: ghogen
manager: jillfra
ms.workload: azure-vs
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.topic: how-to
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: devx-track-csharp, aaddev, vs-azure
ms.openlocfilehash: a070bee7a9fa836eeac7c739cf2757295533ad7f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88165358"
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>Co se stalo s mým projektem WebAPI (připojená služba sady Visual Studio Azure Active Directory)

> [!div class="op_single_selector"]
> - [Začínáme](vs-active-directory-webapi-getting-started.md)
> - [Co se přihodilo](vs-active-directory-webapi-what-happened.md)

Tento článek popisuje přesné změny provedené v projektech ASP.NET WebAPI, ASP.NET Single-Page Application a ASP.NET Azure API při přidávání [Azure Active Directory připojené služby pomocí sady Visual Studio](vs-active-directory-add-connected-service.md). Platí také pro projekty ASP.NET Azure Mobile Service v aplikaci Visual Studio 2015.

Informace o práci s připojenou službou najdete v tématu [Začínáme](vs-active-directory-webapi-getting-started.md).

## <a name="added-references"></a>Přidané odkazy

Má vliv na soubor projektu *. NET References) a `packages.config` (odkazy na NuGet).

| Typ | Reference |
| --- | --- |
| Pohyby NuGet | Microsoft. Owin |
| Pohyby NuGet | Microsoft.Owin.Host.SystemWeb |
| Pohyby NuGet | Microsoft. Owin. Security |
| Pohyby NuGet | Microsoft. Owin. Security. dataactive |
| Pohyby NuGet | Microsoft. Owin. Security. JWT |
| Pohyby NuGet | Microsoft. Owin. Security. OAuth |
| Pohyby NuGet | Owin |
| Pohyby NuGet | System. IdentityModel. Tokens. JWT |

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
| .NET        | Microsoft. IdentityModel. clients. Active. WindowsForms<br>(Pouze Visual Studio 2015) |
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
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- Pouze Visual Studio 2017: do části `<appSettings>` "

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- Do `<dependentAssembly>` uzlu byly přidány prvky `<runtime><assemblyBinding>` pro `System.IdentityModel.Tokens.Jwt` .

- Pokud jste vybrali možnost **číst data adresáře** , Přidali jsme následující položku konfigurace `<appSettings>` :

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>Změny kódu a přidání

- Přidání `[Authorize]` atributu do `Controllers/ValueController.cs` a dalších existujících řadičů.

- Přidali jsme třídu pro spuštění ověření, `App_Start/Startup.Auth.cs` která obsahuje spouštěcí logiku pro ověřování Azure AD, nebo ji odpovídajícím způsobem upravit. Pokud jste vybrali možnost **číst data adresáře** , tento soubor také obsahuje kód pro příjem kódu OAuth a jeho výměnu pro přístupový token.

- (Pouze aplikace Visual Studio 2015 s aplikací ASP.NET 4) Odebrané `App_Start/IdentityConfig.cs` a přidané `Controllers/AccountController.cs` , `Models/IdentityModel.cs` a `Providers/ApplicationAuthProvider.cs` .

- Přidáno `Connected Services/AzureAD/ConnectedService.json` (Visual studio 2017) nebo `Service References/Azure AD/ConnectedService.json` (visual Studio 2015) obsahující informace, které Visual Studio používá ke sledování přidávání připojené služby.

### <a name="file-backup-visual-studio-2015"></a>Zálohování souborů (Visual Studio 2015)

Při přidávání připojené služby Visual Studio 2015 zálohované a odebrané soubory se změnily. Všechny ovlivněné soubory jsou uloženy ve složce `Backup/AzureAD` . Visual Studio 2017 nevytváří zálohy.

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

- [Scénáře ověřování pro Azure Active Directory](./authentication-vs-authorization.md)
- [Přidání přihlašování do webové aplikace ASP.NET pomocí Microsoftu](quickstart-v2-aspnet-webapp.md)