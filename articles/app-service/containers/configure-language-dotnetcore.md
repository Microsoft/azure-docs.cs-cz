---
title: Konfigurace aplikací ASP.NET Core – Azure App Service | Microsoft Docs
description: Naučte se konfigurovat aplikace ASP.NET Core pro práci v Azure App Service
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/13/2019
ms.author: cephalin
ms.openlocfilehash: b05120148d3b82829c465effbcdc948da950aaf0
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990265"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>Konfigurace aplikace ASP.NET Core pro Linux pro Azure App Service

ASP.NET Core aplikace musí být nasazeny jako zkompilované binární soubory. Nástroj pro publikování sady Visual Studio sestaví řešení a potom nasadí zkompilované binární soubory přímo, zatímco modul nasazení App Service nasadí úložiště kódu jako první a potom zkompiluje binární soubory.

Tato příručka poskytuje klíčové koncepty a pokyny pro ASP.NET Core vývojářů, kteří používají integrovaný kontejner Linux v App Service. Pokud jste Azure App Service nikdy nepoužili, postupujte jako první v kurzu [ASP.NET Core rychlý Start](quickstart-dotnetcore.md) a [ASP.NET Core SQL Database](tutorial-dotnetcore-sqldb-app.md) .

## <a name="show-net-core-version"></a>Zobrazit verzi .NET Core

Chcete-li zobrazit aktuální verzi rozhraní .NET Core, spusťte následující příkaz v [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Pokud chcete zobrazit všechny podporované verze .NET Core, spusťte v [Cloud Shell](https://shell.azure.com)následující příkaz:

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>Nastavit verzi .NET Core

Spuštěním následujícího příkazu v [Cloud Shell](https://shell.azure.com) nastavte verzi .NET Core na 2,1:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="access-environment-variables"></a>Přístup k proměnným prostředí

V App Service můžete [nastavit nastavení aplikace](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) mimo kód vaší aplikace. Pak k nim můžete přistupovat v libovolné třídě pomocí vzoru pro vkládání závislostí Standard ASP.NET Core:

```csharp
include Microsoft.Extensions.Configuration;

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

Pokud nakonfigurujete nastavení aplikace se stejným názvem v App Service a v souboru *appSettings. JSON*, má například hodnota App Service přednost před hodnotou *appSettings. JSON* . Lokální hodnota *appSettings. JSON* vám umožní místní ladění aplikace, ale hodnota App Service umožňuje spustit aplikaci v produktu s nastavením produkčního prostředí. Připojovací řetězce fungují stejným způsobem. Tímto způsobem můžete zachovat tajné klíče aplikace mimo vaše úložiště kódu a přistupovat k odpovídajícím hodnotám beze změny kódu.

## <a name="get-detailed-exceptions-page"></a>Získat podrobné stránky výjimek

Když aplikace ASP.NET vygeneruje výjimku v ladicím programu sady Visual Studio, prohlížeč zobrazí stránku podrobností o výjimce, ale v App Service tuto stránku nahradila Obecná chyba **HTTP 500** nebo při **zpracování vaší žádosti došlo k chybě.** Zpráva. Pokud chcete zobrazit stránku podrobností o výjimce v App Service, `ASPNETCORE_ENVIRONMENT` přidejte do aplikace nastavení aplikace spuštěním následujícího příkazu v <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>Zjistit relaci HTTPS

V App Service dojde k [ukončení protokolu SSL](https://wikipedia.org/wiki/TLS_termination_proxy) v nástrojích pro vyrovnávání zatížení sítě, takže všechny požadavky HTTPS dosáhnou vaší aplikace jako nešifrované požadavky HTTP. Pokud vaše logika aplikace potřebuje zjistit, jestli jsou požadavky uživatele zašifrované, nebo ne, nakonfigurujte v *Startup.cs*middleware pro předávané hlavičky:

- Nakonfigurujte middleware pomocí [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) k `X-Forwarded-For` přeposílání `X-Forwarded-Proto` hlaviček a `Startup.ConfigureServices`v.
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

Další informace najdete v tématu [konfigurace ASP.NET Core práci se servery proxy a nástroje pro vyrovnávání zatížení](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer).

## <a name="deploy-multi-project-solutions"></a>Nasazení řešení s více projekty

Při nasazení úložiště ASP.NET do modulu nasazení se souborem *. csproj* v kořenovém adresáři modul nasadí projekt. Když nasadíte úložiště ASP.NET se souborem *. sln* v kořenovém adresáři, modul vybere první web nebo projekt webové aplikace, který se najde jako aplikace App Service. Je možné, že stroj nechce vybrat požadovaný projekt.

Chcete-li nasadit řešení pro více projektů, můžete určit projekt, který se má použít v App Service dvěma různými způsoby:

### <a name="using-deployment-file"></a>Použití souboru. Deployment

Přidejte soubor *. Deployment* do kořenového adresáře úložiště a přidejte následující kód:

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>Použití nastavení aplikace

V <a target="_blank" href="https://shell.azure.com">Azure Cloud Shell</a>přidejte do aplikace App Service nastavení aplikace spuštěním následujícího příkazu CLI. Nahraďte  *\<> název aplikace*,  *\<název skupiny prostředků >* a  *\<> název projektu* příslušnými hodnotami.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Přístup k diagnostickým protokolům

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Otevřít relaci SSH v prohlížeči

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: ASP.NET Core aplikace s SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Nejčastější dotazy k App Service Linux](app-service-linux-faq.md)