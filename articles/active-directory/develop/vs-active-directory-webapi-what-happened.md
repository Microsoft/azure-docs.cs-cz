---
title: Změny provedené v projektech WebAPI při připojování k Azure AD
description: Popisuje, co se stane s projektem WebAPI, když se připojíte k Azure AD pomocí Sady Visual Studio.
author: ghogen
manager: jillfra
ms.workload: azure-vs
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 46fb0ad37b872a1d7ca72114f2f263df776aabf1
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886054"
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>Co se stalo s mým projektem WebAPI (připojená služba Visual Studio Azure Active Directory)

> [!div class="op_single_selector"]
> - [Začínáme](vs-active-directory-webapi-getting-started.md)
> - [Co se přihodilo](vs-active-directory-webapi-what-happened.md)

Tento článek identifikuje přesné změny provedené v ASP.NET WebAPI, ASP.NET jednostránkové aplikace a ASP.NET projekty rozhraní Azure API při přidávání [připojené služby Azure Active Directory pomocí sady Visual Studio](vs-active-directory-add-connected-service.md). Platí také pro projekty ASP.NET Azure Mobile Service v Visual Studiu 2015.

Informace o práci s připojenou službou naleznete v [tématu Začínáme](vs-active-directory-webapi-getting-started.md).

## <a name="added-references"></a>Přidané odkazy

Ovlivňuje odkazy na soubor projektu *.NET) a `packages.config` (odkazy NuGet).

| Typ | Referenční informace |
| --- | --- |
| .NET; NuGet | Microsoft.Owin |
| .NET; NuGet | Microsoft.Owin.Host.SystemWeb |
| .NET; NuGet | Microsoft.Owin.Security |
| .NET; NuGet | Adresář Microsoft.Owin.Security.ActiveDirectory |
| .NET; NuGet | Soubor Microsoft.Owin.Security.Jwt |
| .NET; NuGet | Microsoft.Owin.Security.OAuth |
| .NET; NuGet | Owin (Owin) |
| .NET; NuGet | System.IdentityModel.Tokens.Jwt |

Další odkazy, pokud jste vybrali možnost **Číst data adresáře:**

| Typ | Referenční informace |
| --- | --- |
| .NET; NuGet | EntityFramework |
| .NET        | EntityFramework.SqlServer (pouze Visual Studio 2015) |
| .NET; NuGet | Klient Microsoft.Azure.ActiveDirectory.Graph |
| .NET; NuGet | Microsoft.Data.Edm |
| .NET; NuGet | Microsoft.Data.OData |
| .NET; NuGet | Microsoft.data.Services.client |
| .NET; NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms<br>(Pouze Visual Studio 2015) |
| .NET; NuGet | System.Spatial |

Následující odkazy jsou odebrány (ASP.NET pouze 4 projekty, jako v Sadě Visual Studio 2015):

| Typ | Referenční informace |
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
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- Pouze Visual Studio 2017: Také `<appSettings>`přidána následující položka pod "

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- Přidány `<dependentAssembly>` prvky `<runtime><assemblyBinding>` pod `System.IdentityModel.Tokens.Jwt`uzlem pro .

- Pokud jste vybrali možnost **Číst data adresáře,** přidali jste v části `<appSettings>`:

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>Změny kódu a dodatky

- Byl `[Authorize]` přidán `Controllers/ValueController.cs` atribut a všechny ostatní existující řadiče.

- Byla přidána třída `App_Start/Startup.Auth.cs`spuštění ověřování , obsahující logiku spuštění pro ověřování Azure AD nebo ji odpovídajícím způsobem upravila. Pokud jste vybrali možnost **Číst data adresáře,** tento soubor také obsahuje kód pro příjem kódu OAuth a jeho výměnu za přístupový token.

- (Visual Studio 2015 s jenom ASP.NET 4) Odebráno `App_Start/IdentityConfig.cs` `Controllers/AccountController.cs`a `Models/IdentityModel.cs`přidáno , a `Providers/ApplicationAuthProvider.cs`.

- Přidáno `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) nebo `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015), obsahující informace, které Visual Studio používá ke sledování přidání připojené služby.

### <a name="file-backup-visual-studio-2015"></a>Zálohování souborů (Visual Studio 2015)

Při přidávání připojené služby Visual Studio 2015 zálohy změněné a odebrané soubory. Všechny ohrožené soubory jsou `Backup/AzureAD`uloženy ve složce . Visual Studio 2017 nevytváří zálohy.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ApplicationOAuthProvider.cs`

## <a name="changes-on-azure"></a>Změny v Azure

- Vytvořil aplikaci Azure AD v doméně, kterou jste vybrali při přidávání připojené služby.
- Byla aktualizována aplikace tak, aby zahrnovala oprávnění **Číst data adresáře,** pokud byla tato možnost vybrána.

[Další informace o službě Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Další kroky

- [Scénáře ověřování pro Službu Azure Active Directory](authentication-scenarios.md)
- [Přidání přihlášení s Microsoftem do ASP.NET webové aplikace](quickstart-v2-aspnet-webapp.md)
