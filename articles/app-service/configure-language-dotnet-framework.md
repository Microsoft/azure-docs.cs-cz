---
title: Konfigurace aplikací ASP.NET
description: Naučte se konfigurovat aplikaci ASP.NET v Azure App Service. Tento článek ukazuje nejběžnější konfigurační úlohy.
ms.devlang: dotnet
ms.custom: devx-track-csharp, devx-track-azurecli
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 8ed6835583cc4881b19eee14ed392b193324535e
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92744157"
---
# <a name="configure-an-aspnet-app-for-azure-app-service"></a>Konfigurace aplikace ASP.NET pro Azure App Service

> [!NOTE]
> ASP.NET Core najdete v tématu [konfigurace ASP.NET Core aplikace pro Azure App Service](configure-language-dotnetcore.md)

ASP.NET aplikace musí být nasazené, aby se Azure App Service jako zkompilované binární soubory. Nástroj pro publikování sady Visual Studio sestaví řešení a potom nasadí zkompilované binární soubory přímo, zatímco modul nasazení App Service nasadí úložiště kódu jako první a potom zkompiluje binární soubory.

Tato příručka poskytuje klíčové koncepty a pokyny pro vývojáře v ASP.NET. Pokud jste nikdy Azure App Service nepoužili, postupujte jako první v kurzu [rychlý start ASP.NET](quickstart-dotnet-framework.md) a [ASP.NET s SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md) .

## <a name="show-supported-net-framework-runtime-versions"></a>Zobrazit podporované verze modulu runtime .NET Framework

V App Service už jsou na instancích Windows nainstalované všechny podporované verze .NET Framework. Chcete-li zobrazit .NET Framework runtime a verze SDK, které jsou vám k dispozici, přejděte na adresu `https://<app-name>.scm.azurewebsites.net/DebugConsole` a spusťte příslušný příkaz v konzole založené na prohlížeči:

Pro běhové verze CLR 4 (.NET Framework 4 a novější):

```CMD
ls "D:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\"
```

Nejnovější verze .NET Framework nemusí být hned k dispozici.

Pro běhové verze CLR 2 (.NET Framework 3,5 a nižší):

```CMD
ls "D:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\"
```

## <a name="show-current-net-framework-runtime-version"></a>Zobrazit aktuální verzi .NET Framework runtime

Spusťte následující příkaz v [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query netFrameworkVersion
```

Hodnota `v4.0` znamená, že se používá nejnovější verze CLR 4 (.NET Framework 4. x). Hodnota znamená, `v2.0` že se používá verze CLR 2 (.NET Framework 3,5).

## <a name="set-net-framework-runtime-version"></a>Nastavit verzi .NET Framework runtime

Ve výchozím nastavení používá App Service k používání aplikace ASP.NET nejnovější podporovanou .NET Framework verzi. Chcete-li spustit aplikaci pomocí .NET Framework 3,5, spusťte následující příkaz v [Cloud Shell](https://shell.azure.com) (v 2.0 znamená CLR 2):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --net-framework-version v2.0
```

## <a name="access-environment-variables"></a>Přístup k proměnným prostředí

V App Service můžete [nastavit nastavení aplikace](configure-common.md#configure-app-settings) a připojovací řetězce mimo kód vaší aplikace. Pak k nim můžete přistupovat z libovolné třídy pomocí standardního vzoru ASP.NET:

```csharp
using System.Configuration;
...
// Get an app setting
ConfigurationManager.AppSettings["MySetting"];
// Get a connection string
ConfigurationManager.ConnectionStrings["MyConnection"];
}
```

Pokud nakonfigurujete nastavení aplikace se stejným názvem v App Service a v *web.config* , hodnota App Service má přednost před *web.configou* hodnotou. Hodnota místního *web.config* umožňuje místní ladění aplikace, ale hodnota App Service umožňuje spuštění aplikace v produktu s nastavením produkčního prostředí. Připojovací řetězce fungují stejným způsobem. Tímto způsobem můžete zachovat tajné klíče aplikace mimo vaše úložiště kódu a přistupovat k odpovídajícím hodnotám beze změny kódu.

## <a name="deploy-multi-project-solutions"></a>Nasazení řešení s více projekty

Pokud řešení sady Visual Studio obsahuje více projektů, proces publikování sady Visual Studio již zahrnuje výběr projektu k nasazení. Při nasazení do modulu nasazení App Service, jako je například s Git nebo s nasazením ZIP, je zapnutá automatizace sestavení, modul pro nasazení App Service vybere první web nebo projekt webové aplikace, který najde jako aplikaci App Service. Můžete určit, který projekt App Service má použít, zadáním `PROJECT` nastavení aplikace. Například spusťte následující příkaz v [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="get-detailed-exceptions-page"></a>Získat podrobné stránky výjimek

Když aplikace ASP.NET generuje výjimku v ladicím programu sady Visual Studio, prohlížeč zobrazí stránku podrobností o výjimce, ale v App Service tuto stránku nahradí obecná chybová zpráva. Chcete-li zobrazit stránku podrobností o výjimce v App Service, otevřete soubor *Web.config* a přidejte `<customErrors mode="Off"/>` prvek pod `<system.web>` element. Příklad:

```xml
<system.web>
    <customErrors mode="Off"/>
</system.web>
```

Znovu nasaďte aplikaci s aktualizovaným *Web.config* . Teď byste měli vidět stejnou stránku podrobností o výjimce.

## <a name="access-diagnostic-logs"></a>Přístup k diagnostickým protokolům

Můžete přidat diagnostické zprávy do kódu aplikace pomocí [System. Diagnostics. Trace](/dotnet/api/system.diagnostics.trace). Příklad: 

```csharp
Trace.TraceError("Record not found!"); // Error trace
Trace.TraceWarning("Possible data loss"); // Warning trace
Trace.TraceInformation("GET /Home/Index"); // Information trace
```

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: Vytvoření aplikace ASP.NET se službou SQL Database v Azure](app-service-web-tutorial-dotnet-sqldatabase.md)
