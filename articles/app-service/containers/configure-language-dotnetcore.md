---
title: Konfigurace aplikací pro ASP.NET Core – služba Azure App Service | Dokumentace Microsoftu
description: Další informace o konfiguraci aplikace ASP.NET Core pro práci ve službě Azure App Service
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: e203877b2bc939c1d7fb9390df39f3e2451d12d3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60852308"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>Konfigurace s Linuxem aplikace ASP.NET Core pro službu Azure App Service

Aplikace ASP.NET Core se musí nasadit jako kompilované binární soubory. Publikování nástroje Visual Studio sestaví řešení a pak nasadí kompilované binární soubory přímo, že modul pro nasazení služby App Service nejprve nasadí úložiště kódu a následně zkompiluje binární soubory.

Tato příručka obsahuje klíčové koncepty a pokyny k ASP.NET Core, vývojáře, kteří používají předdefinované kontejneru Linuxu ve službě App Service. Pokud jste nikdy použili službu Azure App Service, postupujte [rychlý start ASP.NET Core](quickstart-dotnetcore.md) a [ASP.NET Core s kurz k SQL Database](tutorial-dotnetcore-sqldb-app.md) první.

## <a name="show-net-core-version"></a>Zobrazit verzi .NET Core

Chcete-li zobrazit aktuální verzi .NET Core, spusťte následující příkaz [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Chcete-li zobrazit všechny podporované verze rozhraní .NET Core, spusťte následující příkaz [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>Verze sady .NET Core

Spuštěním následujícího příkazu [Cloud Shell](https://shell.azure.com) nastavit verzi rozhraní .NET Core 2.1:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="access-environment-variables"></a>Přístup k proměnným prostředí

Ve službě App Service můžete [nastavení aplikace](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings) mimo kód vaší aplikace. Potom se můžete dostat pomocí standardní vzor ASP.NET:

```csharp
include Microsoft.Extensions.Configuration;
// retrieve App Service app setting
System.Configuration.ConfigurationManager.AppSettings["MySetting"]
// retrieve App Service connection string
Configuration.GetConnectionString("MyDbConnection")
```

Pokud nakonfigurujete nastavení aplikace se stejným názvem ve službě App Service a v *Web.config*, hodnota služby App Service má přednost před hodnotu Web.config. Hodnota Web.config umožňuje ladit aplikaci místně, ale hodnota služby App Service umožňuje spuštění aplikace v produktu s produkčním prostředí. Připojovací řetězce fungovat stejným způsobem. Tímto způsobem můžete zachovat vaše tajných klíčů aplikací mimo váš kód úložiště a přístup k příslušné hodnoty beze změny kódu.

## <a name="get-detailed-exceptions-page"></a>Zobrazí podrobné výjimky

Aplikace ASP.NET v ladicím programu sady Visual Studio vygeneruje výjimku, prohlížeč zobrazí stránku podrobné výjimky, ale ve službě App Service se tuto stránku nahrazuje obecný **HTTP 500** chyba nebo **k chybě došlo zpracování vaší žádosti.** zpráva. Chcete-li zobrazit podrobné výjimky stránku ve službě App Service, přidejte `ASPNETCORE_ENVIRONMENT` aplikace spuštěním následujícího příkazu v nastavení aplikace nastavte <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>Zjistit relace HTTPS

Ve službě App Service [ukončení protokolu SSL](https://wikipedia.org/wiki/TLS_termination_proxy) se odehrává na nástroje pro vyrovnávání zatížení sítě, takže všechny požadavky HTTPS kontaktovat vaši aplikaci jako nešifrované požadavky HTTP. Pokud vaše aplikace logiky potřebuje vědět, pokud uživatel požaduje se nebo není zašifrované, nakonfigurujte záhlaví Middleware předané v *Startup.cs*:

- Nakonfigurujte middleware s [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) předávat `X-Forwarded-For` a `X-Forwarded-Proto` záhlaví v `Startup.ConfigureServices`.
- Přidáte rozsahy privátních IP adres do známé sítě tak, aby mohli spolehnout, middleware nástroje pro vyrovnávání zatížení služby App Service.
- Vyvolat [UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) metoda `Startup.Configure` před voláním metody jiné middlewares.

Sestavení všechny tři prvky, váš kód bude vypadat jako v následujícím příkladu:

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

## <a name="deploy-multi-project-solutions"></a>Nasazení řešení vícenásobného projektu

Při nasazení je úložiště na ASP.NET do modulu nasazení *.csproj* souboru v kořenovém adresáři modulu nasadí projekt. Když nasadíte úložiště ASP.NET pomocí *.sln* souboru v kořenovém adresáři, modul se vybere první web nebo projekt webové aplikace, které nalezne jako aplikace služby App Service. Je možné pro tento motor nechcete vyberte projekt, který chcete.

Pokud chcete nasadit řešení pro více projektů, můžete zadat projekt pro použití ve službě App Service dvěma různými způsoby:

### <a name="using-deployment-file"></a>Soubor .deployment pomocí

Přidat *.deployment* do kořenového adresáře úložiště a přidejte následující kód:

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>Pomocí nastavení aplikace

V <a target="_blank" href="https://shell.azure.com">Azure Cloud Shell</a>, přidání nastavení aplikace do aplikace služby App Service spuštěním následujícího příkazu rozhraní příkazového řádku. Nahraďte  *\<název aplikace >*,  *\<resource-group-name >*, a  *\<název projektu >* příslušnými hodnotami .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>Přístup k diagnostickým protokolům

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Otevřít relaci SSH v prohlížeči

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz: Aplikace ASP.NET Core s SQL Database](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [App Service Linuxu – nejčastější dotazy](app-service-linux-faq.md)