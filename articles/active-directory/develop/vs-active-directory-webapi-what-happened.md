---
title: "Změny provedené na projekt WebAPI při připojení k Azure AD | Microsoft Docs"
description: "Popisuje, co se stane do projektu WebAPI při připojení k Azure AD pomocí sady Visual Studio"
services: active-directory
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 57630aee-26a2-4326-9dbb-ea2a66daa8b0
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: 140f555d28c4d5a923b9c255d8e61d7aea9bb23f
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>Co se stalo s WebAPI projektu (Visual Studio Azure Active Directory připojení služby)

> [!div class="op_single_selector"]
> - [Začínáme](vs-active-directory-webapi-getting-started.md)
> - [Co se přihodilo](vs-active-directory-webapi-what-happened.md)

Tento článek identifikuje přesný změny provedené projekty ASP.NET WebAPI, jednostránkové aplikace ASP.NET a rozhraní API ASP.NET Azure při přidávání [Azure Active Directory připojení služby pomocí sady Visual Studio](vs-active-directory-add-connected-service.md). Platí také pro projekty ASP.NET Azure Mobile Service ve Visual Studiu 2015.

Informace o práci s připojené služby najdete v tématu [Začínáme](vs-active-directory-webapi-getting-started.md).

## <a name="added-references"></a>Přidání odkazů

Ovlivňuje odkazy na projekt souboru *.NET) a `packages.config` (odkazů NuGet).

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
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms<br>(Visual Studio 2015 pouze) |
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
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- Visual Studio 2017 pouze: také přidat následující položku v části `<appSettings>`"

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- Přidat `<dependentAssembly>` elementů v rámci `<runtime><assemblyBinding>` uzel pro `System.IdentityModel.Tokens.Jwt`.

- Pokud jste vybrali **čtení dat adresáře** možnost, přidat následující položku konfigurace v rámci `<appSettings>`:

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>Změny kódu a přidání

- Přidat `[Authorize]` atribut `Controllers/ValueController.cs` a všechny ostatní existující řadiče.

- Přidat třídu spuštění ověřování `App_Start/Startup.Auth.cs`, obsahující logika spuštění pro ověřování Azure AD, nebo je změněn odpovídajícím způsobem. Pokud jste vybrali **čtení dat adresáře** možnost, tento soubor zároveň obsahuje kód pro příjem kódu OAuth a exchange pro přístupový token.

- (Visual Studio 2015 s technologií ASP.NET 4 pouze aplikace) Odebrat `App_Start/IdentityConfig.cs` a přidat `Controllers/AccountController.cs`, `Models/IdentityModel.cs`, a `Providers/ApplicationAuthProvider.cs`.

- Přidat `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) nebo `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015), obsahující informace, které Visual Studio používá ke sledování přidání připojených služeb.

### <a name="file-backup-visual-studio-2015"></a>Zálohování souborů (Visual Studio 2015)

Při přidání připojené služby, Visual Studio 2015 zálohuje změněné a odebrání souborů. Všechny zasažené soubory jsou uloženy ve složce `Backup/AzureAD`. Visual Studio 2017 nedojde k vytvoření zálohy.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ApplicationOAuthProvider.cs`

## <a name="changes-on-azure"></a>Změny v Azure

- Vytvořit aplikaci Azure AD v doméně, která jste vybrali při přidání připojené služby.
- Aktualizovat aplikaci zahrnout **čtení dat adresáře** oprávnění, pokud byla tato možnost vybraná.

[Další informace o službě Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Další postup

- [Scénáře ověřování pro Azure Active Directory](active-directory-authentication-scenarios.md)
- [Přidání přihlášení se společností Microsoft do webové aplikace ASP.NET](guidedsetups/active-directory-aspnetwebapp-v1.md)