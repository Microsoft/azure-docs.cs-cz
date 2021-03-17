---
title: Použití injektáže závislostí ve službě Azure Functions pro .NET
description: Naučte se používat vkládání závislostí k registraci a používání služeb ve funkcích .NET.
author: ggailey777
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 01/27/2021
ms.author: glenga
ms.reviewer: jehollan
ms.openlocfilehash: 66e2cd22f4bcb95be65d6d04345dcac622436a04
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955084"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Použití injektáže závislostí ve službě Azure Functions pro .NET

Azure Functions podporuje vzor návrhu pro vkládání závislostí (DI), což je technika pro dosažení [inverze řízení (IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mezi třídami a jejich závislostmi.

- Vkládání závislostí v Azure Functions je postavené na funkcích injektáže .NET Core Dependency vstřik. Doporučuje se používat [vkládání závislostí .NET Core](/aspnet/core/fundamentals/dependency-injection) . Existují rozdíly v tom, jak potlačíte závislosti a jak jsou čteny konfigurační hodnoty pomocí Azure Functions v plánu spotřeby.

- Podpora vkládání závislostí začíná Azure Functions 2. x.

## <a name="prerequisites"></a>Požadavky

Než budete moci použít vkládání závislostí, je nutné nainstalovat následující balíčky NuGet:

- [Microsoft. Azure. Functions. Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- Balíček [Microsoft. NET. SDK. Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) verze 1.0.28 nebo novější

- [Microsoft. Extensions. DependencyInjection](https://www.nuget.org/packages/Microsoft.Extensions.DependencyInjection/) (aktuálně se podporuje jenom verze 3. x a starší)

## <a name="register-services"></a>Registrovat služby

Chcete-li registrovat služby, vytvořte metodu pro konfiguraci a přidání součástí do `IFunctionsHostBuilder` instance.  Hostitel Azure Functions vytvoří instanci `IFunctionsHostBuilder` a předá ji přímo do vaší metody.

Chcete-li zaregistrovat metodu, přidejte `FunctionsStartup` atribut Assembly, který určuje název typu použitý při spuštění.

```csharp
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();

            builder.Services.AddSingleton<IMyService>((s) => {
                return new MyService();
            });

            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

V tomto příkladu se používá balíček [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) vyžadovaný k registraci `HttpClient` při spuštění.

### <a name="caveats"></a>Upozornění

Série kroků registrace se spouští před a po zpracování spouštěcí třídy. Proto Pamatujte na následující položky:

- *Spouštěcí třída je určena pouze pro instalaci a registraci.* Vyhněte se používání služeb zaregistrovaných při spuštění během procesu spuštění. Nesnažte se třeba protokolovat zprávu do protokolovacího nástroje, který se registruje při spuštění. Tento bod procesu registrace je příliš brzy, aby byly vaše služby k dispozici pro použití. Po `Configure` spuštění metody bude modul runtime funkcí nadále registrovat další závislosti, což může ovlivnit fungování služeb.

- *Kontejner pro vkládání závislostí obsahuje pouze explicitně registrované typy*. Jediné služby, které jsou k dispozici jako vložené typy, jsou to, co je nastaveno v `Configure` metodě. V důsledku toho typy specifické pro funkce, jako `BindingContext` a `ExecutionContext` nejsou k dispozici během instalace nebo jako vložené typy.

## <a name="use-injected-dependencies"></a>Použít vložené závislosti

K dispozici jsou závislosti pomocí injektáže konstruktoru ve funkci. Použití injektáže konstruktoru vyžaduje, abyste pro vložené služby nebo třídy funkcí nepoužívali statické třídy.

Následující příklad ukazuje, jak `IMyService` jsou tyto `HttpClient` závislosti vloženy do funkce aktivované protokolem HTTP.

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Net.Http;
using System.Threading.Tasks;

namespace MyNamespace
{
    public class MyHttpTrigger
    {
        private readonly HttpClient _client;
        private readonly IMyService _service;

        public MyHttpTrigger(HttpClient httpClient, IMyService service)
        {
            this._client = httpClient;
            this._service = service;
        }

        [FunctionName("MyHttpTrigger")]
        public async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            var response = await _client.GetAsync("https://microsoft.com");
            var message = _service.GetMessage();

            return new OkObjectResult("Response from function with injected dependencies.");
        }
    }
}
```

V tomto příkladu se používá balíček [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) vyžadovaný k registraci `HttpClient` při spuštění.

## <a name="service-lifetimes"></a>Životnost služeb

Azure Functions aplikace poskytují stejné životnosti služeb jako [vkládání závislostí ASP.NET](/aspnet/core/fundamentals/dependency-injection#service-lifetimes). U aplikace Functions se chovají různé životnosti služby následujícím způsobem:

- **Přechodný**: přechodné služby se vytvářejí při každém vyřešení služby.
- **Vymezené**: rozsah platnosti služby odpovídá době trvání spuštění funkce. Vymezené služby se vytvoří jednou za spuštění funkce. Pozdější požadavky na tuto službu během opakovaného použití existující instance služby.
- **Singleton**: životnost služby typu Singleton odpovídá době životnosti hostitele a je znovu používána napříč prováděním funkce v dané instanci. Pro připojení a klienty jsou doporučovány služby životnosti singleton, `DocumentClient` například `HttpClient` instance.

Zobrazit nebo stáhnout [ukázku různých životností služby](https://github.com/Azure/azure-functions-dotnet-extensions/tree/main/src/samples/DependencyInjection/Scopes) na GitHubu.

## <a name="logging-services"></a>Služby protokolování

Pokud potřebujete vlastního poskytovatele protokolování, zaregistrujte vlastní typ jako instanci nástroje [`ILoggerProvider`](/dotnet/api/microsoft.extensions.logging.iloggerfactory) , která je k dispozici prostřednictvím balíčku NuGet [Microsoft. Extensions. Logging. Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) .

Application Insights automaticky přidá Azure Functions.

> [!WARNING]
> - Nepřidávat `AddApplicationInsightsTelemetry()` do kolekce služeb, které registrují služby, které jsou v konfliktu se službami poskytovanými prostředím.
> - Neregistrujte svoji vlastní `TelemetryConfiguration` nebo `TelemetryClient` Pokud používáte integrovanou funkci Application Insights. Pokud potřebujete nakonfigurovat vlastní `TelemetryClient` instanci, vytvořte ji pomocí vloženého kódu, jak je `TelemetryConfiguration` znázorněno v části [vlastní telemetrie protokolu ve funkcích jazyka C#](functions-dotnet-class-library.md?tabs=v2%2Ccmd#log-custom-telemetry-in-c-functions).

### <a name="iloggert-and-iloggerfactory"></a>ILogger <T> a ILoggerFactory

Hostitel vloží `ILogger<T>` a `ILoggerFactory` služby do konstruktorů.  Ve výchozím nastavení se ale tyto nové filtry protokolování odfiltrují z protokolů funkcí.  Abyste se mohli `host.json` přihlásit k dalším filtrům a kategoriím, musíte upravit soubor.

Následující příklad ukazuje, jak přidat `ILogger<HttpTrigger>` s protokoly, které jsou vystaveny hostiteli.

```csharp
namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly ILogger<HttpTrigger> _log;

        public HttpTrigger(ILogger<HttpTrigger> log)
        {
            _log = log;
        }

        [FunctionName("HttpTrigger")]
        public async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req)
        {
            _log.LogInformation("C# HTTP trigger function processed a request.");

            // ...
    }
}
```

Následující příklad `host.json` souboru Přidá filtr protokolu.

```json
{
    "version": "2.0",
    "logging": {
        "applicationInsights": {
            "samplingSettings": {
                "isEnabled": true,
                "excludedTypes": "Request"
            }
        },
        "logLevel": {
            "MyNamespace.HttpTrigger": "Information"
        }
    }
}
```

Další informace o úrovních protokolu najdete v tématu [Configure log úrovně](configure-monitoring.md#configure-log-levels).

## <a name="function-app-provided-services"></a>Poskytnuté služby Function App

Hostitel funkce registruje mnoho služeb. V rámci vaší aplikace je možné v aplikaci provést zabezpečení těchto služeb:

|Typ služby|Doba platnosti|Description|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Singleton|Konfigurace modulu runtime|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Singleton|Zodpovídá za poskytnutí ID instance hostitele.|

Pokud existují další služby, na kterých chcete převzít závislost, [vytvořte problém a navrhněte je na GitHubu](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Přepsání hostitelských služeb

Přepsání služeb poskytovaných hostitelem není aktuálně podporováno.  Pokud existují služby, které chcete přepsat, [vytvořte problém a navrhněte je na GitHubu](https://github.com/azure/azure-functions-host).

## <a name="working-with-options-and-settings"></a>Práce s možnostmi a nastaveními

Hodnoty definované v [nastavení aplikace](./functions-how-to-use-azure-function-app-settings.md#settings) jsou k dispozici v `IConfiguration` instanci, která umožňuje číst hodnoty nastavení aplikace ve třídě Startup.

Hodnoty z instance můžete extrahovat `IConfiguration` do vlastního typu. Kopírování hodnot nastavení aplikace do vlastního typu usnadňuje testování služeb tím, že se tyto hodnoty vloží. Nastavení číst do instance konfigurace musí být jednoduché páry klíč-hodnota.

Vezměte v úvahu následující třídu, která obsahuje vlastnost s názvem konzistentní s nastavením aplikace:

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

A `local.settings.json` soubor, který může strukturovat vlastní nastavení, je následující:
```json
{
  "IsEncrypted": false,
  "Values": {
    "MyOptions:MyCustomSetting": "Foobar"
  }
}
```

V rámci `Startup.Configure` metody můžete extrahovat hodnoty z `IConfiguration` instance do vlastního typu pomocí následujícího kódu:

```csharp
builder.Services.AddOptions<MyOptions>()
    .Configure<IConfiguration>((settings, configuration) =>
    {
        configuration.GetSection("MyOptions").Bind(settings);
    });
```

Volání `Bind` kopíruje hodnoty, které mají odpovídající názvy vlastností z konfigurace do vlastní instance. Instance Options je teď k dispozici v kontejneru IoC pro vkládání do funkce.

Objekt Options je vložen do funkce jako instance obecného `IOptions` rozhraní. `Value`Pro přístup k hodnotám nalezeným ve vaší konfiguraci použijte vlastnost.

```csharp
using System;
using Microsoft.Extensions.Options;

public class HttpTrigger
{
    private readonly MyOptions _settings;

    public HttpTrigger(IOptions<MyOptions> options)
    {
        _settings = options.Value;
    }
}
```

Další podrobnosti týkající se práce s možnostmi najdete [v tématu vzor možností v ASP.NET Core](/aspnet/core/fundamentals/configuration/options) .

## <a name="using-aspnet-core-user-secrets"></a>Používání ASP.NET Core uživatelských tajných klíčů

Při vývoji místně ASP.NET Core poskytuje [Nástroj Správce tajných klíčů](/aspnet/core/security/app-secrets#secret-manager) , který umožňuje ukládat tajné informace mimo kořenový adresář projektu. Díky tomu je méně pravděpodobný, že tajné klíče jsou omylem svěřeny správě zdrojového kódu. Azure Functions Core Tools (verze 3.0.3233 nebo novější) automaticky přečtou tajné klíče vytvořené správcem ASP.NET Core tajných klíčů.

Chcete-li nakonfigurovat projekt Azure Functions .NET pro použití uživatelských tajných kódů, spusťte následující příkaz v kořenovém adresáři projektu.

```bash
dotnet user-secrets init
```

Pak použijte `dotnet user-secrets set` příkaz k vytvoření nebo aktualizaci tajných kódů.

```bash
dotnet user-secrets set MySecret "my secret value"
```

Chcete-li získat přístup k hodnotám tajných kódů uživatele v kódu aplikace Function App, použijte `IConfiguration` nebo `IOptions` .

## <a name="customizing-configuration-sources"></a>Přizpůsobení zdrojů konfigurace

> [!NOTE]
> Přizpůsobení zdroje konfigurace je k dispozici počínaje verzí Azure Functions hostitele 2.0.14192.0 a 3.0.14191.0.

Chcete-li určit další zdroje konfigurace, přepište `ConfigureAppConfiguration` metodu ve třídě aplikace Function App `StartUp` .

Následující ukázka přidá konfigurační hodnoty ze základní a volitelné soubory nastavení aplikace specifické pro konkrétní prostředí.

```csharp
using System.IO;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
        {
            FunctionsHostBuilderContext context = builder.GetContext();

            builder.ConfigurationBuilder
                .AddJsonFile(Path.Combine(context.ApplicationRootPath, "appsettings.json"), optional: true, reloadOnChange: false)
                .AddJsonFile(Path.Combine(context.ApplicationRootPath, $"appsettings.{context.EnvironmentName}.json"), optional: true, reloadOnChange: false)
                .AddEnvironmentVariables();
        }
    }
}
```

Přidejte poskytovatele konfigurace do `ConfigurationBuilder` vlastnosti `IFunctionsConfigurationBuilder` . Další informace o použití zprostředkovatelů konfigurace najdete [v tématu Konfigurace v ASP.NET Core](/aspnet/core/fundamentals/configuration/#configuration-providers).

`FunctionsHostBuilderContext`Je získán z `IFunctionsConfigurationBuilder.GetContext()` . Pomocí tohoto kontextu načtěte aktuální název prostředí a vyřešte umístění konfiguračních souborů ve složce Function App.

Ve výchozím nastavení se konfigurační soubory, jako je například *appsettings.js* , nekopírují automaticky do výstupní složky aplikace Function App. Aktualizujte soubor *. csproj* tak, aby odpovídal následující ukázce, aby bylo zajištěno, že budou soubory zkopírovány.

```xml
<None Update="appsettings.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>      
</None>
<None Update="appsettings.Development.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    <CopyToPublishDirectory>Never</CopyToPublishDirectory>
</None>
```

> [!IMPORTANT]
> Pro aplikace Function App spuštěné v plánech spotřeby nebo Premium můžou úpravy hodnot konfigurace používaných v aktivačních událostech způsobit chyby škálování. Jakékoli změny těchto vlastností `FunctionsStartup` třídou způsobí chybu při spuštění aplikace Function App.

## <a name="next-steps"></a>Další kroky

Další informace naleznete v následujících zdrojích:

- [Jak monitorovat aplikaci Function App](functions-monitoring.md)
- [Osvědčené postupy pro funkce](functions-best-practices.md)
