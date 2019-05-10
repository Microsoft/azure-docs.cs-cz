---
title: Kurz pro používání příznaků funkcí v aplikaci .NET Core | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak implementovat příznaků funkcí v aplikacích .NET Core
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 28ba4397ca5a5fd3c281555238fc7eec8a82943d
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413677"
---
# <a name="tutorial-use-feature-flags-in-a-net-core-app"></a>Kurz: Použití příznaků funkcí v aplikaci .NET Core

Knihovny .NET Core funkce správy idiomatickou podporují implementace příznaků funkcí v aplikaci .NET nebo ASP.NET Core. Umožňují vám přidat příznaky funkcí pro váš kód více deklarativně tak, že není potřeba psát veškerý `if` příkazy pro ně ručně. Spravují životní cykly příznak funkce (například aktualizace a mezipaměti příznak státy, zajištění neměnných příznak stavu během volání žádosti) za scénu. Kromě toho knihovna ASP.NET Core nabízí integrace out-of-the-box včetně akce řadiče MVC, zobrazení, cesty a middlewaru.

[Přidat příznaků funkcí do aplikace ASP.NET Core](./quickstart-feature-flag-aspnet-core.md) rychlý start ukazuje několik způsobů, jak přidat příznaků funkcí v aplikaci ASP.NET Core. Tento kurz vysvětluje tyto další podrobnosti. Najdete v článku [dokumentaci k ASP.NET Core funkci správy](https://go.microsoft.com/fwlink/?linkid=2091410) úplný přehled.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidáte příznaků funkcí v klíčových částí aplikace pro řízení dostupnosti funkce.
> * Integrace s konfigurací aplikace při použití ke správě příznaky funkcí.

## <a name="setup"></a>Nastavení

Správce funkce .NET Core `IFeatureManager` získá příznaky funkcí z rozhraní framework nativní konfigurace systému. V důsledku toho můžete definovat příznaků funkcí aplikace používají libovolný zdroj konfigurace, který podporuje .NET Core, včetně místní *appsettings.json* proměnné souboru nebo prostředí. Správce funkce spoléhá na .NET Core vkládání závislostí. Můžete zaregistrovat funkce správy služeb, které využívají standardní konvence.

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement();
    }
}
```

Funkce správce načte příznaků funkcí z část "FeatureManagement".NET Core konfiguračních dat ve výchozím nastavení. Následující příklad dává pokyn jej číst z jiného oddílu volat "MyFeatureFlags" místo.

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement(options =>
        {
                options.UseConfiguration(Configuration.GetSection("MyFeatureFlags"));
        });
    }
}
```

Pokud používáte filtr v příznaků funkcí, musíte zahrnout další knihovny a zaregistrujte ho. Následující příklad ukazuje, jak použít filtr integrovanou funkci s názvem **PercentageFilter "**.

```csharp
using Microsoft.FeatureManagement;
using Microsoft.FeatureManagement.FeatureFilters;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement()
                .AddFeatureFilter<PercentageFilter>();
    }
}
```

Pracovat efektivně, musí zachovat příznaků funkcí mimo aplikaci a spravovat samostatně. To můžete kdykoli upravit příznak stavy a se tyto změny se neprojeví v aplikaci hned. Konfigurace aplikace poskytuje centrálním umístění pro uspořádání a řízení všech funkcí příznaky prostřednictvím vyhrazené uživatelské rozhraní portálu a zajišťuje příznaků pro vaši aplikaci přímo přes .NET Core klienta knihovny. Nejjednodušší způsob, jak připojit aplikaci ASP.NET Core do konfigurace aplikace je prostřednictvím poskytovatele konfigurace `Microsoft.Extensions.Configuration.AzureAppConfiguration`. Můžete použít tento balíček NuGet ve vašem kódu přidáním následujícího *Program.cs* souboru:

```csharp
using Microsoft.Extensions.Configuration.AzureAppConfiguration;

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
           .ConfigureAppConfiguration((hostingContext, config) => {
               var settings = config.Build();
               config.AddAzureAppConfiguration(options => {
                   options.Connect(settings["ConnectionStrings:AppConfig"])
                          .UseFeatureFlags();
                });
           })
           .UseStartup<Startup>();
```

Očekává se, že hodnoty příznak funkce v průběhu času měnit. Ve výchozím nastavení bude správce funkci aktualizace hodnot příznak funkce každých 30 sekund. Můžete použít jiný interval dotazování v `options.UseFeatureFlags()` volání výše.

```csharp
config.AddAzureAppConfiguration(options => {
    options.Connect(settings["ConnectionStrings:AppConfig"])
           .UseFeatureFlags(featureFlagOptions => {
                featureFlagOptions.PollInterval = TimeSpan.FromSeconds(5);
           });
});
```

## <a name="feature-flag-declaration"></a>Deklarace příznak funkce

Každý příznak funkce má dvě části: název a seznam jednoho nebo více filtrů, které se používají k vyhodnocení, jestli je stav funkce *na* (to znamená, když je jeho hodnota `True`). Filtr definuje případ použití, pro který funkce by měla být nastavená na on. Pokud příznak funkce má několik filtrů, je seznam filtrů Procházet v pořadí, dokud jeden z filtrů určí, že tato funkce povolena. V tomto okamžiku se považuje za příznakem funkce jako *na* a všechny zbývající výsledky filtru jsou vynechány. Pokud žádný filtr označuje, že tato funkce povolena, je příznak funkce *vypnout*.

Podporuje funkce správce *appsettings.json* jako zdroj konfigurace pro příznaky funkcí. Následující příklad ukazuje, jak nastavit příznaky funkcí v souboru json.

```JSON
"FeatureManagement": {
    "FeatureX": true, // Feature flag set to on
    "FeatureY": false, // Feature flag set to off
    "FeatureC": {
        "EnabledFor": [
            {
                "Name": "Percentage",
                "Parameters": {
                    "Value": 50
                }
            }
        ]
    }
}
```

Podle konvence `FeatureManagement` část tohoto dokumentu json se používá pro nastavení příznaku funkce. Výše uvedený příklad ukazuje tři příznaků funkcí s jejich filtrům definovaným v *EnabledFor* vlastnost:

* **FeatureA** je *na*.
* **FeatureB** je *vypnout*.
* **FeatureC** Určuje filtr s názvem *procento* s *parametry* vlastnost. *Procento* je příkladem Konfigurovat filtr a určuje s 50 % pravděpodobností **FeatureC** příznak *na*.

## <a name="referencing"></a>Odkazování na

I když není nutné, příznaky funkcí musí být definován jako `enum` proměnné tak, aby ně dalo snadno odkazovat v kódu.

```csharp
public enum MyFeatureFlags
{
    FeatureA,
    FeatureB,
    FeatureC
}
```

## <a name="feature-flag-check"></a>Kontrola příznak funkce

Základní princip funkce správy, je nejprve zkontrolujte Pokud příznak funkce nastavená na *na* a pak proveďte uzavřené akce, pokud je to tento případ.

```csharp
IFeatureManager featureManager;
...
if (featureManager.IsEnabled(nameof(MyFeatureFlags.FeatureA)))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Injektáž závislostí

V ASP.NET Core MVC, funkce správce `IFeatureManager` lze přistupovat pomocí vkládání závislostí.

```csharp
public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

## <a name="controller-action"></a>Akce kontroleru

V kontrolery MVC `Feature` atribut můžete slouží ke kontrole, jestli je povolená třída celý kontroleru nebo konkrétní akci. Následující `HomeController` kontroler vyžaduje *FeatureA* bude *na* předtím, než je možné provést žádnou akci, která ho obsahuje.

```csharp
[Feature(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

Následující `Index` výše uvedenou akci vyžaduje *FeatureA* bude *na* před spuštěním.

```csharp
[Feature(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

Když MVC kontroler nebo akce je zablokovaná, protože je příznak řídící funkce *vypnout*, registrovaný `IDisabledFeatureHandler` je volána. Výchozí hodnota `IDisabledFeatureHandler` vrátí stavový kód 404 klientovi s žádnou odpovědí.

## <a name="view"></a>Zobrazení

V zobrazení MVC `<feature>` značky lze použít k vykreslení obsahu založené na tom, jestli je nebo není povolená příznak funkce.

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

## <a name="mvc-filter"></a>Filtr MVC

MVC filtry můžete nastavit tak, aby se aktivují, na základě stavu příznak funkce. Následující Přidá filtr MVC s názvem `SomeMvcFilter`. Tento filtr se aktivuje v rámci pouze pokud kanál MVC *FeatureA* je povolená.

```csharp
using Microsoft.FeatureManagement.FeatureFilters;

IConfiguration Configuration { get; set;}

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options => {
        options.Filters.AddForFeature<SomeMvcFilter>(nameof(MyFeatureFlags.FeatureA));
    });
}
```

## <a name="route"></a>Trasa

Trasy můžou zveřejnit dynamicky podle příznaky funkcí. Následující přidá trasu, která nastavuje hodnoty `Beta` jako výchozí kontroler pouze tehdy, když *FeatureA* je povolená.

```csharp
app.UseMvc(routes => {
    routes.MapRouteForFeature(nameof(MyFeatureFlags.FeatureA), "betaDefault", "{controller=Beta}/{action=Index}/{id?}");
});
```

## <a name="middleware"></a>Middleware

Příznaky funkcí je možné přidat větve aplikace a middleware podmíněně. Následující operace vložení komponenta middlewaru v požadavku kanálu pouze tehdy, když *FeatureA* je povolená.

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(nameof(MyFeatureFlags.FeatureA));
```

Tento postup sestaví vypnout obecnější schopnost větvit celé aplikace založené na příznak funkce.

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak implementovat příznaků funkcí v aplikaci ASP.NET Core s využitím `Microsoft.FeatureManagement` knihovny. Naleznete v následujících zdrojích pro další informace o podporovaných funkcích správy v ASP.NET Core a konfigurace aplikací.

* [Příznak funkce ASP.NET Core ukázkový kód]()
* [Dokumentace ke službě Microsoft.FeatureManagement]()
* [Správa příznaků funkcí](./manage-feature-flags.md)
