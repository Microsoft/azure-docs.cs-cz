---
title: Konfigurace aplikací pro Windows ASP.NET Core
description: Naučte se konfigurovat aplikaci ASP.NET Core v nativních instancích systému Windows App Service. Tento článek ukazuje nejběžnější konfigurační úlohy.
ms.devlang: dotnet
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 5819fc5b2d6e64d1812dacd88a2a4f840f6e03c5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84908077"
---
# <a name="configure-a-windows-aspnet-core-app-for-azure-app-service"></a>Konfigurace aplikace ASP.NET Core pro Windows pro Azure App Service

> [!NOTE]
> ASP.NET v .NET Framework najdete v tématu [Konfigurace aplikace ASP.NET pro Azure App Service](configure-language-dotnet-framework.md)

ASP.NET Core aplikace musí být nasazené, aby se Azure App Service jako zkompilované binární soubory. Nástroj pro publikování sady Visual Studio sestaví řešení a potom nasadí zkompilované binární soubory přímo, zatímco modul nasazení App Service nasadí úložiště kódu jako první a potom zkompiluje binární soubory. Informace o aplikacích pro Linux najdete v tématu [Konfigurace aplikace ASP.NET Core pro Linux pro Azure App Service](containers/configure-language-dotnetcore.md).

Tato příručka poskytuje klíčové koncepty a pokyny pro ASP.NET Core vývojářům. Pokud jste Azure App Service nikdy nepoužili, postupujte jako první v kurzu [rychlý start ASP.NET](app-service-web-get-started-dotnet.md) a [ASP.NET Core SQL Database](app-service-web-tutorial-dotnetcore-sqldb.md) .

## <a name="show-supported-net-core-runtime-versions"></a>Zobrazit podporované verze modulu runtime .NET Core

V App Service instance Windows již mají nainstalované všechny podporované verze rozhraní .NET Core. Pokud chcete zobrazit dostupné verze rozhraní .NET Core Runtime a sady SDK, přejděte na adresu `https://<app-name>.scm.azurewebsites.net/DebugConsole` a spusťte následující příkaz v konzole založené na prohlížeči:

```azurecli-interactive
dotnet --info
```

## <a name="set-net-core-version"></a>Nastavit verzi .NET Core

Nastavte cílovou architekturu v souboru projektu pro váš ASP.NET Core projekt. Další informace najdete v tématu [Výběr verze .NET Core pro použití](https://docs.microsoft.com/dotnet/core/versions/selection) v dokumentaci k .NET Core.

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
> Všimněte si, že [Hierarchická konfigurační data](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/#hierarchical-configuration-data) v *appsettings.js* jsou k dispozici pomocí `:` oddělovače standard pro .NET Core. Pokud chcete v App Service přepsat konkrétní hierarchické nastavení konfigurace, v klíči nastavte název nastavení aplikace na stejný formát s oddělovači. v [Cloud Shell](https://shell.azure.com)můžete spustit následující příklad:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings My:Hierarchical:Config:Data="some value"
```

## <a name="deploy-multi-project-solutions"></a>Nasazení řešení s více projekty

Pokud řešení sady Visual Studio obsahuje více projektů, proces publikování sady Visual Studio již zahrnuje výběr projektu k nasazení. Při nasazení do modulu nasazení App Service, jako je například s Git nebo s nasazením ZIP, je zapnutá automatizace sestavení, modul pro nasazení App Service vybere první web nebo projekt webové aplikace, který najde jako aplikaci App Service. Můžete určit, který projekt App Service má použít, zadáním `PROJECT` nastavení aplikace. Například spusťte následující příkaz v [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Přístup k diagnostickým protokolům

ASP.NET Core poskytuje [integrovaného zprostředkovatele protokolování pro App Service](https://docs.microsoft.com/aspnet/core/fundamentals/logging/#azure-app-service). V *program.cs* projektu přidejte poskytovatele do aplikace prostřednictvím `ConfigureLogging` metody rozšíření, jak je znázorněno v následujícím příkladu:

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

Pak můžete nakonfigurovat a vygenerovat protokoly se [standardním vzorem .NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

Další informace o řešení potíží s ASP.NET Core aplikacemi v App Service najdete v tématu věnovaném [řešení potíží ASP.NET Core na Azure App Service a IIS](https://docs.microsoft.com/aspnet/core/test/troubleshoot-azure-iis) .

## <a name="get-detailed-exceptions-page"></a>Získat podrobné stránky výjimek

Když vaše aplikace ASP.NET Core generuje výjimku v ladicím programu sady Visual Studio, prohlížeč zobrazí stránku podrobností o výjimce, ale v App Service tuto stránku nahradila Obecná chyba **HTTP 500** nebo při **zpracování vaší žádosti došlo k chybě.** zpráva. Pokud chcete zobrazit stránku podrobností o výjimce v App Service, přidejte do `ASPNETCORE_ENVIRONMENT` aplikace nastavení aplikace spuštěním následujícího příkazu v <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>Zjistit relaci HTTPS

V App Service dojde k [ukončení protokolu SSL](https://wikipedia.org/wiki/TLS_termination_proxy) v nástrojích pro vyrovnávání zatížení sítě, takže všechny požadavky HTTPS dosáhnou vaší aplikace jako nešifrované požadavky HTTP. Pokud vaše logika aplikace potřebuje zjistit, jestli jsou požadavky uživatele zašifrované, nebo ne, nakonfigurujte v *Startup.cs*middleware pro předávané hlavičky:

- Nakonfigurujte middleware pomocí [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) k přeposílání `X-Forwarded-For` `X-Forwarded-Proto` hlaviček a v `Startup.ConfigureServices` .
- Přidejte do známých sítí rozsahy privátních IP adres, aby middleware mohl důvěřovat nástroji pro vyrovnávání zatížení App Service.
- Vyvolat metodu [UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) v v `Startup.Configure` před voláním jiných middlewarů.

Vložení všech tří prvků dohromady, váš kód vypadá jako v následujícím příkladu:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders =
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
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

Další informace najdete v tématu [konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: ASP.NET Core aplikace s SQL Database](app-service-web-tutorial-dotnetcore-sqldb.md)
