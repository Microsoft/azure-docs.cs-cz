---
title: Konfigurace aplikací Linux ASP.NET Core
description: Přečtěte si, jak pro vaši aplikaci nakonfigurovat předem sestavený kontejner ASP.NET jádra. Tento článek ukazuje nejběžnější úlohy konfigurace.
ms.devlang: dotnet
ms.topic: article
ms.date: 08/13/2019
ms.openlocfilehash: b1d9e59109f5ace25abb9840b48e44ff03d394e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255916"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>Konfigurace aplikace Linux ASP.NET Core pro azure app service

ASP.NET Základní aplikace musí být nasazeny jako zkompilované binární soubory. Nástroj pro publikování sady Visual Studio vytvoří řešení a pak nasadí zkompilované binární soubory přímo, zatímco modul nasazení služby App Service nejprve nasadí úložiště kódu a pak zkompiluje binární soubory.

Tato příručka obsahuje klíčové koncepty a pokyny pro vývojáře ASP.NET Core, kteří používají integrovaný kontejner Linuxu ve službě App Service. Pokud jste službu Azure App Service nikdy nepoužívali, postupujte nejprve podle [ASP.NET základního a](quickstart-dotnetcore.md) [ASP.NET core s kurzem databáze SQL.](tutorial-dotnetcore-sqldb-app.md)

## <a name="show-net-core-version"></a>Zobrazit verzi jádra rozhraní .NET

Chcete-li zobrazit aktuální verzi .NET Core, spusťte v [prostředí Cloud Shell](https://shell.azure.com)následující příkaz :

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Chcete-li zobrazit všechny podporované verze jádra .NET, spusťte v [prostředí Cloud Shell](https://shell.azure.com)následující příkaz :

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>Nastavit verzi jádra rozhraní .NET

Spusťte následující příkaz v [prostředí Cloud Shell](https://shell.azure.com) a nastavte verzi .NET Core na verzi 2.1:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="customize-build-automation"></a>Přizpůsobení automatizace sestavení

Pokud aplikaci nasadíte pomocí balíčků Git nebo zip se zapnutou automatizací sestavení, služba App Service provede automatizaci automatizace následujícím pořadím:

1. Spusťte vlastní `PRE_BUILD_SCRIPT_PATH`skript, pokud je určen programem .
1. Spusťte `dotnet restore` obnovení závislostí NuGet.
1. Spuštění `dotnet publish` sestavení binární ho pro produkční prostředí.
1. Spusťte vlastní `POST_BUILD_SCRIPT_PATH`skript, pokud je určen programem .

`PRE_BUILD_COMMAND`a `POST_BUILD_COMMAND` jsou proměnné prostředí, které jsou ve výchozím nastavení prázdné. Chcete-li spustit příkazy `PRE_BUILD_COMMAND`předběžného sestavení, definujte . Chcete-li spustit příkazy `POST_BUILD_COMMAND`po sestavení, definujte .

Následující příklad určuje dvě proměnné řady příkazů oddělených čárkami.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Další proměnné prostředí pro přizpůsobení automatizace sestavení naleznete v [tématu Konfigurace Oryx](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Další informace o tom, jak služba App Service běží a vytváří ASP.NET aplikace Core v Linuxu, najdete v [dokumentaci oryxu: Jak jsou detekovány a sestaveny aplikace .NET Core](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/dotnetcore.md).

## <a name="access-environment-variables"></a>Přístup k proměnným prostředí

Ve službě App Service můžete [nastavit nastavení aplikace](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) mimo kód aplikace. Pak k nim můžete přistupovat v libovolné třídě pomocí standardního vzoru vkládání závislostí ASP.NET jádra:

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
            // retrieve App Service app setting
            var myAppSetting = _configuration["MySetting"];
            // retrieve App Service connection string
            var myConnString = _configuration.GetConnectionString("MyDbConnection");
        }
    }
}
```

Pokud nakonfigurujete nastavení aplikace se stejným názvem ve službě App Service a v *appsettings.json*, hodnota Služby aplikace má přednost před hodnotou *appsettings.json.* Hodnota local *appsettings.json* umožňuje ladit aplikaci místně, ale hodnota Služby aplikace umožňuje spuštění aplikace v produktu s nastavením produkčního prostředí. Připojovací řetězce fungují stejným způsobem. Tímto způsobem můžete zachovat tajné kódy aplikace mimo úložiště kódu a přístup k příslušným hodnotám bez evidenčního kódu.

## <a name="get-detailed-exceptions-page"></a>Stránka Získat podrobné výjimky

Když vaše ASP.NET aplikace generuje výjimku v ladicím programu Sady Visual Studio, prohlížeč zobrazí podrobnou stránku výjimky, ale ve službě App Service je tato stránka nahrazena obecnou chybou **HTTP 500** nebo **při zpracování požadavku došlo k chybě.** zpráva. Chcete-li zobrazit stránku podrobné výjimky ve službě App Service, přidejte nastavení `ASPNETCORE_ENVIRONMENT` aplikace do aplikace spuštěním následujícího příkazu v prostředí Cloud <a target="_blank" href="https://shell.azure.com" >Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>Rozpoznat relaci HTTPS

Ve službě App Service dojde k [ukončení SSL](https://wikipedia.org/wiki/TLS_termination_proxy) v síťových nástrojích pro vyrovnávání zatížení, takže všechny požadavky HTTPS se dostanou do vaší aplikace jako nešifrované požadavky HTTP. Pokud vaše logika aplikace potřebuje vědět, jestli jsou požadavky uživatelů šifrované nebo ne, nakonfigurujte middlewar předávaných záhlaví v *Startup.cs*:

- Nakonfigurujte middleware s [možnostmi Předávaných záhlaví](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) a přeposílání `X-Forwarded-For` `X-Forwarded-Proto` a záhlaví v aplikaci `Startup.ConfigureServices`.
- Přidejte soukromé rozsahy IP adres do známých sítí, aby middleware mohl důvěřovat vyrovnávání zatížení služby App Service.
- Vyvoláte metodu [UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) před `Startup.Configure` voláním jiných middlewares.

Když vložíte všechny tři prvky dohromady, váš kód vypadá jako následující příklad:

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

Další informace naleznete v [tématu Configure ASP.NET Core to work with proxy servers and load balancers](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer).

## <a name="deploy-multi-project-solutions"></a>Nasazení řešení pro více projektů

Když nasadíte ASP.NET úložiště do modulu nasazení se souborem *.csproj* v kořenovém adresáři, modul nasadí projekt. Když nasadíte ASP.NET úložiště se souborem *.sln* v kořenovém adresáři, modul vybere první web nebo projekt webové aplikace, který najde jako aplikaci App Service. Je možné, že motor není vybrat projekt, který chcete.

Chcete-li nasadit řešení s více projekty, můžete určit projekt, který se má použít ve službě App Service dvěma různými způsoby:

### <a name="using-deployment-file"></a>Použití souboru nasazení

Přidejte soubor *.deployment* do kořenového adresáře úložiště a přidejte následující kód:

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>Použití nastavení aplikace

V <a target="_blank" href="https://shell.azure.com">Prostředí Azure Cloud Shell</a>přidejte nastavení aplikace do aplikace App Service spuštěním následujícího příkazu příkazu příkazu příkazu příkazu. Nahraďte * \<>názvu aplikace *, * \<>název skupiny prostředků *a * \<>název projektu* příslušnými hodnotami.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Přístup k diagnostickým protokolům

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Otevření relace SSH v prohlížeči

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: aplikace ASP.NET Core s databází SQL](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Nejčastější dotazy k aplikační službě Linux](app-service-linux-faq.md)
