---
title: Změny provedené s projektem WebAPI při připojování ke službě Azure AD
description: Popisuje, co se stane projektu WebAPI, když se připojíte ke službě Azure AD s použitím sady Visual Studio
services: active-directory
author: ghogen
manager: douge
ms.assetid: 57630aee-26a2-4326-9dbb-ea2a66daa8b0
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 04732d6541fd6132360d4c235b35979c70772922
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2018
ms.locfileid: "42054433"
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>Co se stalo s mým projektem WebAPI (Visual Studio Azure Active Directory připojená služba)

> [!div class="op_single_selector"]
> - [Začínáme](vs-active-directory-webapi-getting-started.md)
> - [Co se přihodilo](vs-active-directory-webapi-what-happened.md)

Tento článek identifikuje přesný změny provedené pro projekty ASP.NET WebAPI, jednostránkové aplikace ASP.NET a ASP.NET rozhraní API služby Azure, při přidávání [Azure Active Directory připojenou službu pomocí sady Visual Studio](vs-active-directory-add-connected-service.md). Také se vztahují na projekty ASP.NET Azure Mobile Service ve Visual Studiu 2015.

Informace o práci s připojenou službu, najdete v části [Začínáme](vs-active-directory-webapi-getting-started.md).

## <a name="added-references"></a>Přidání odkazů

Ovlivňuje odkazy na soubory *.NET projekt) a `packages.config` (odkazy na NuGet).

| Typ | Referenční informace |
| --- | --- |
| .NET; NuGet | Microsoft.Owin |
| .NET; NuGet | Microsoft.Owin.Host.SystemWeb |
| .NET; NuGet | Microsoft.Owin.Security |
| .NET; NuGet | Microsoft.Owin.Security.ActiveDirectory |
| .NET; NuGet | Microsoft.Owin.Security.Jwt |
| .NET; NuGet | Microsoft.Owin.Security.OAuth |
| .NET; NuGet | Owin |
| .NET; NuGet | System.IdentityModel.Tokens.Jwt |

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
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms<br>(Visual Studio 2015 jenom) |
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
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- Visual Studio 2017 pouze: také přidá následující položku v rámci `<appSettings>`"

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- Přidání `<dependentAssembly>` prvků `<runtime><assemblyBinding>` uzel `System.IdentityModel.Tokens.Jwt`.

- Pokud jste vybrali **čtení dat adresáře** možnost, přidá následující položku konfigurace v rámci `<appSettings>`:

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>Změny kódu a doplňky

- Přidá `[Authorize]` atribut `Controllers/ValueController.cs` a žádné stávající řadiče.

- Přidá třídu spuštění ověřování `App_Start/Startup.Auth.cs`, který obsahuje spouštěcí logiky ověřování Azure AD nebo odpovídajícím způsobem změněny. Pokud jste vybrali **čtení dat adresáře** možnost, tento soubor zároveň obsahuje kód pro příjem kódu OAuth a výměně pro přístupový token.

- (Visual Studio 2015 s ASP.NET 4 pouze aplikace) Odebrat `App_Start/IdentityConfig.cs` a přidali `Controllers/AccountController.cs`, `Models/IdentityModel.cs`, a `Providers/ApplicationAuthProvider.cs`.

- Přidání `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) nebo `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015), obsahuje informace, které Visual Studio používá ke sledování přidání připojené služby.

### <a name="file-backup-visual-studio-2015"></a>Zálohování souborů (Visual Studio 2015)

Při přidání připojené služby Visual Studio 2015 zálohuje změněné a odebrané soubory. Všechny ovlivněné soubory jsou uloženy ve složce `Backup/AzureAD`. Visual Studio 2017 se nevytváří žádné zálohy.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ApplicationOAuthProvider.cs`

## <a name="changes-on-azure"></a>Změny v Azure

- Vytvoří aplikaci Azure AD v doméně, která jste vybrali při přidání připojené služby.
- Aktualizace aplikace, aby zahrnovala **čtení dat adresáře** oprávnění, pokud jste vybrali tuto možnost.

[Další informace o službě Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Další postup

- [Scénáře ověřování pro Azure Active Directory](authentication-scenarios.md)
- [Přidání přihlašování s Microsoftem do webové aplikace ASP.NET](quickstart-v1-aspnet-webapp.md)