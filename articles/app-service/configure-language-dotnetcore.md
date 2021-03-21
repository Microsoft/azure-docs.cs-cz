---
title: Konfigurace aplikací ASP.NET Core
description: Naučte se konfigurovat aplikaci ASP.NET Core v nativních instancích systému Windows nebo v předem sestaveném kontejneru Linux v Azure App Service. Tento článek ukazuje nejběžnější konfigurační úlohy.
ms.devlang: dotnet
ms.custom: devx-track-csharp, devx-track-azurecli
ms.topic: article
ms.date: 06/02/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 1223ff5c56d3c7d58b324d2099980bc0b5408125
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97655964"
---
# <a name="configure-an-aspnet-core-app-for-azure-app-service"></a>Konfigurace aplikace ASP.NET Core pro Azure App Service

> [!NOTE]
> ASP.NET v .NET Framework najdete v tématu [Konfigurace aplikace ASP.NET pro Azure App Service](configure-language-dotnet-framework.md)

ASP.NET Core aplikace musí být nasazené, aby se Azure App Service jako zkompilované binární soubory. Nástroj pro publikování sady Visual Studio sestaví řešení a potom nasadí zkompilované binární soubory přímo, zatímco modul nasazení App Service nasadí úložiště kódu jako první a potom zkompiluje binární soubory.

Tato příručka poskytuje klíčové koncepty a pokyny pro ASP.NET Core vývojářům. Pokud jste Azure App Service nikdy nepoužili, postupujte jako první v kurzu [ASP.NET Core rychlý Start](quickstart-dotnetcore.md) a [ASP.NET Core SQL Database](tutorial-dotnetcore-sqldb-app.md) .

::: zone pivot="platform-windows"  

## <a name="show-supported-net-core-runtime-versions"></a>Zobrazit podporované verze modulu runtime .NET Core

V App Service instance Windows již mají nainstalované všechny podporované verze rozhraní .NET Core. Pokud chcete zobrazit dostupné verze rozhraní .NET Core Runtime a sady SDK, přejděte na adresu `https://<app-name>.scm.azurewebsites.net/DebugConsole` a spusťte následující příkaz v konzole založené na prohlížeči:

```azurecli-interactive
dotnet --info
```

::: zone-end

::: zone pivot="platform-linux"

## <a name="show-net-core-version"></a>Zobrazit verzi .NET Core

Chcete-li zobrazit aktuální verzi rozhraní .NET Core, spusťte následující příkaz v [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Pokud chcete zobrazit všechny podporované verze .NET Core, spusťte v [Cloud Shell](https://shell.azure.com)následující příkaz:

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

::: zone-end

## <a name="set-net-core-version"></a>Nastavit verzi .NET Core

::: zone pivot="platform-windows"  

Nastavte cílovou architekturu v souboru projektu pro váš ASP.NET Core projekt. Další informace najdete v tématu [Výběr verze .NET Core pro použití](/dotnet/core/versions/selection) v dokumentaci k .NET Core.

::: zone-end

::: zone pivot="platform-linux"

Spuštěním následujícího příkazu v [Cloud Shell](https://shell.azure.com) nastavte verzi .NET Core na 3,1:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|3.1"
```

::: zone-end

::: zone pivot="platform-linux"

## <a name="customize-build-automation"></a>Přizpůsobení automatizace sestavení

Pokud nasadíte aplikaci s použitím balíčků Git nebo zip se zapnutou možností automatizace sestavení, App Service sestavování kroků automatizace pomocí následujícího postupu:

1. Spusťte vlastní skript, pokud je určen `PRE_BUILD_SCRIPT_PATH` .
1. Spusťte `dotnet restore` pro obnovení závislostí NuGet.
1. Spusťte příkaz `dotnet publish` pro sestavení binárního souboru pro produkční prostředí.
1. Spusťte vlastní skript, pokud je určen `POST_BUILD_SCRIPT_PATH` .

`PRE_BUILD_COMMAND` a `POST_BUILD_COMMAND` jsou proměnné prostředí, které jsou ve výchozím nastavení prázdné. Chcete-li spustit příkazy před sestavením, definujte `PRE_BUILD_COMMAND` . Chcete-li spustit příkazy po sestavení, definujte `POST_BUILD_COMMAND` .

Následující příklad určuje dvě proměnné pro řadu příkazů, které jsou odděleny čárkami.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Další proměnné prostředí pro přizpůsobení automatizace sestavení naleznete v tématu [Oryx Configuration](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Další informace o tom, jak App Service spouštět a sestavují ASP.NET Core aplikace v systému Linux, najdete v [dokumentaci k Oryx: jak se zjišťují a vytváří aplikace .NET Core](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/dotnetcore.md).

::: zone-end

## <a name="access-environment-variables"></a>Přístup k proměnným prostředí

V App Service můžete [nastavit nastavení aplikace](configure-common.md#configure-app-settings) mimo kód vaší aplikace. Pak k nim můžete přistupovat v libovolné třídě pomocí vzoru pro vkládání závislostí Standard ASP.NET Core:

```csharp
using Microsoft.Extensions.Configuration;

namespace SomeNamespace 
{
    public class SomeClass
    {
        private IConfiguration _configuration;
    
        public SomeClass(IConfiguration configuration)
        {
            _configuration = configuration;
        }
    
        public SomeMethod()
        {
            // retrieve nested App Service app setting
            var myHierarchicalConfig = _configuration["My:Hierarchical:Config:Data"];
            // retrieve App Service connection string
            var myConnString = _configuration.GetConnectionString("MyDbConnection");
        }
    }
}
```

Pokud nakonfigurujete nastavení aplikace se stejným názvem v App Service a v *appsettings.js*, například hodnota App Service má přednost před *appsettings.jsna* hodnotě. Místní *appsettings.js* hodnota umožňuje ladit aplikaci místně, ale hodnota App Service umožňuje spuštění aplikace v produktu s nastavením produkčního prostředí. Připojovací řetězce fungují stejným způsobem. Tímto způsobem můžete zachovat tajné klíče aplikace mimo vaše úložiště kódu a přistupovat k odpovídajícím hodnotám beze změny kódu.

> [!NOTE]
> Všimněte si, že [Hierarchická konfigurační data](/aspnet/core/fundamentals/configuration/#hierarchical-configuration-data) v *appsettings.js* jsou k dispozici pomocí `:` oddělovače standard pro .NET Core. Pokud chcete v App Service přepsat konkrétní hierarchické nastavení konfigurace, v klíči nastavte název nastavení aplikace na stejný formát s oddělovači. v [Cloud Shell](https://shell.azure.com)můžete spustit následující příklad:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings My:Hierarchical:Config:Data="some value"
```

## <a name="deploy-multi-project-solutions"></a>Nasazení řešení s více projekty

Pokud řešení sady Visual Studio obsahuje více projektů, proces publikování sady Visual Studio již zahrnuje výběr projektu k nasazení. Při nasazení do modulu nasazení App Service, jako je například s Git nebo s nasazením ZIP, je zapnutá automatizace sestavení, modul pro nasazení App Service vybere první web nebo projekt webové aplikace, který najde jako aplikaci App Service. Můžete určit, který projekt App Service má použít, zadáním `PROJECT` nastavení aplikace. Například spusťte následující příkaz v [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Přístup k diagnostickým protokolům

ASP.NET Core poskytuje [integrovaného zprostředkovatele protokolování pro App Service](/aspnet/core/fundamentals/logging/#azure-app-service). V *programu program. cs* vašeho projektu přidejte poskytovatele do aplikace prostřednictvím `ConfigureLogging` metody rozšíření, jak je znázorněno v následujícím příkladu:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureLogging(logging =>
        {
            logging.AddAzureWebAppDiagnostics();
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

Pak můžete nakonfigurovat a vygenerovat protokoly se [standardním vzorem .NET Core](/aspnet/core/fundamentals/logging).

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

Další informace o řešení potíží s ASP.NET Core aplikacemi v App Service najdete v tématu věnovaném [řešení potíží ASP.NET Core na Azure App Service a IIS](/aspnet/core/test/troubleshoot-azure-iis) .

## <a name="get-detailed-exceptions-page"></a>Získat podrobné stránky výjimek

Když vaše aplikace ASP.NET Core generuje výjimku v ladicím programu sady Visual Studio, prohlížeč zobrazí stránku podrobností o výjimce, ale v App Service tuto stránku nahradila Obecná chyba **HTTP 500** nebo při **zpracování vaší žádosti došlo k chybě.** . Pokud chcete zobrazit stránku podrobností o výjimce v App Service, přidejte do `ASPNETCORE_ENVIRONMENT` aplikace nastavení aplikace spuštěním následujícího příkazu v <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>Zjistit relaci HTTPS

V App Service dojde k [ukončení protokolu SSL](https://wikipedia.org/wiki/TLS_termination_proxy) v nástrojích pro vyrovnávání zatížení sítě, takže všechny požadavky HTTPS dosáhnou vaší aplikace jako nešifrované požadavky HTTP. Pokud vaše logika aplikace potřebuje zjistit, jestli se požadavky uživatelů šifrují, nebo ne, nakonfigurujte Middlewari předávaných hlaviček při *spuštění. cs*:

- Nakonfigurujte middleware pomocí [ForwardedHeadersOptions](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) k přeposílání `X-Forwarded-For` `X-Forwarded-Proto` hlaviček a v `Startup.ConfigureServices` .
- Přidejte do známých sítí rozsahy privátních IP adres, aby middleware mohl důvěřovat nástroji pro vyrovnávání zatížení App Service.
- Volejte metodu [UseForwardedHeaders](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) v v `Startup.Configure` před voláním jiného middleware.

Vložení všech tří prvků dohromady, váš kód vypadá jako v následujícím příkladu:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders =
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
        // These three subnets encapsulate the applicable Azure subnets. At the moment, it's not possible to narrow it down further.
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:10.0.0.0"), 104));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:192.168.0.0"), 112));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:172.16.0.0"), 108));
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    ...

    app.UseMvc();
}
```

Další informace najdete v tématu [konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení](/aspnet/core/host-and-deploy/proxy-load-balancer).

::: zone pivot="platform-linux"

## <a name="open-ssh-session-in-browser"></a>Otevření relace SSH v prohlížeči

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: ASP.NET Core aplikace s SQL Database](tutorial-dotnetcore-sqldb-app.md)

::: zone pivot="platform-linux"

> [!div class="nextstepaction"]
> [Nejčastější dotazy k App Service v Linuxu](faq-app-service-linux.md)

::: zone-end
