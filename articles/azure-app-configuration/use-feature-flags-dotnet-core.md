---
title: Kurz pro používání příznaků funkcí v aplikaci .NET Core | Dokumenty společnosti Microsoft
description: V tomto kurzu se dozvíte, jak implementovat příznaky funkcí v aplikacích .NET Core.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 3182961f928a9befc5a55fb6d58e22c74ba81089
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79473434"
---
# <a name="tutorial-use-feature-flags-in-an-aspnet-core-app"></a>Kurz: Použití příznaků funkcí v aplikaci ASP.NET Core

Knihovny správy základních funkcí .NET poskytují idiomatickou podporu pro implementaci příznaků funkcí v aplikaci .NET nebo ASP.NET Core. Tyto knihovny umožňují deklarativně přidat příznaky funkce do kódu, takže `if` není nutné psát všechny příkazy pro ně ručně.

Knihovny správy funkcí také spravují životní cykly příznaku funkcí na pozadí. Například knihovny aktualizovat a cache příznak stavy nebo zaručit stav příznaku neměnné během volání požadavku. Knihovna ASP.NET Core navíc nabízí předem po vybalení integrace, včetně akcí řadiče MVC, zobrazení, tras a middlewaru.

Úvodní [příznaky funkce do ASP.NET aplikace Core Úvodní příručka](./quickstart-feature-flag-aspnet-core.md) ukazuje několik způsobů, jak přidat příznaky funkcí v aplikaci ASP.NET Core. Tento kurz vysvětluje tyto metody podrobněji. Úplný odkaz naleznete v dokumentaci ke [správě funkcí ASP.NET Core](https://go.microsoft.com/fwlink/?linkid=2091410).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidejte příznaky funkcí v klíčových částech aplikace k řízení dostupnosti funkcí.
> * Integrujte s konfigurací aplikace, když ji používáte ke správě příznaků funkcí.

## <a name="set-up-feature-management"></a>Nastavit správu funkcí

Přidejte odkaz `Microsoft.FeatureManagement` na balíček NuGet pro využití správce funkcí .NET Core.
    
Správce `IFeatureManager` funkcí .NET Core získá příznaky funkcí z nativního konfiguračního systému rozhraní. V důsledku toho můžete definovat příznaky funkcí aplikace pomocí libovolného zdroje konfigurace, který podporuje rozhraní .NET Core, včetně místního souboru *appsettings.json* nebo proměnných prostředí. `IFeatureManager`spoléhá na vkládání závislostí jádra .NET. Služby správy funkcí můžete zaregistrovat pomocí standardních konvencí:

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

Ve výchozím nastavení správce funkcí načítá `"FeatureManagement"` příznaky funkcí z části konfiguračních dat jádra .NET. Následující příklad říká správci funkcí číst z `"MyFeatureFlags"` jiné hooddílu s názvem místo:

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

Pokud v příznaky funkce používáte filtry, musíte zahrnout další knihovnu a zaregistrovat ji. Následující příklad ukazuje, jak používat vestavěný filtr `PercentageFilter`funkcí s názvem :

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

Doporučujeme zachovat příznaky funkcí mimo aplikaci a spravovat je samostatně. To vám umožní kdykoli upravit stavy příznaku a tyto změny se projeví v aplikaci ihned. Konfigurace aplikace poskytuje centralizované místo pro uspořádání a řízení všech příznaků funkcí prostřednictvím vyhrazeného portálového uznatého. Konfigurace aplikace také doručuje příznaky do aplikace přímo prostřednictvím svých klientských knihoven .NET Core.

Nejjednodušší způsob, jak připojit ASP.NET základní aplikace k konfiguraci aplikace je prostřednictvím zprostředkovatele `Microsoft.Azure.AppConfiguration.AspNetCore`konfigurace . Postupujte podle následujících kroků, chcete-li použít tento balíček NuGet.

1. Otevřete *soubor Program.cs* a přidejte následující kód.

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

2. Otevřete *Startup.cs* `Configure` a aktualizujte metodu pro přidání middlewaru, aby se hodnoty příznaku funkce aktualizovaly v opakovaném intervalu, zatímco ASP.NET webová aplikace Core nadále přijímá požadavky.

   ```csharp
   public void Configure(IApplicationBuilder app, IHostingEnvironment env)
   {
       app.UseAzureAppConfiguration();
       app.UseMvc();
   }
   ```

Očekává se, že hodnoty příznaku prvku se v průběhu času mění. Ve výchozím nastavení jsou hodnoty příznaku prvku ukládány do mezipaměti po dobu 30 sekund, takže operace aktualizace spuštěná, když middleware obdrží požadavek, nebude hodnotu aktualizovat, dokud nevyprší platnost hodnoty uložené v mezipaměti. Následující kód ukazuje, jak změnit čas vypršení platnosti mezipaměti nebo interval dotazování na 5 minut ve `options.UseFeatureFlags()` volání.

```csharp
config.AddAzureAppConfiguration(options => {
    options.Connect(settings["ConnectionStrings:AppConfig"])
           .UseFeatureFlags(featureFlagOptions => {
                featureFlagOptions.CacheExpirationTime = TimeSpan.FromMinutes(5);
           });
});
```

## <a name="feature-flag-declaration"></a>Deklarace příznaku prvku

Každý příznak prvku má dvě části: název a seznam jednoho nebo více filtrů, které se používají k vyhodnocení, zda je stav prvku *zapnutý* (tj. pokud je `True`jeho hodnota ). Filtr definuje případ použití, kdy má být prvek zapnutý.

Pokud má příznak prvku více filtrů, je seznam filtrů provázán v pořadí, dokud jeden z filtrů neurčí, že by měla být funkce povolena. V tomto okamžiku je příznak prvku *zapnutý*a všechny zbývající výsledky filtru jsou přeskočeny. Pokud žádný filtr neindikuje, že by měl být prvek povolen, je příznak prvku *vypnutý*.

Správce funkcí podporuje *soubor appsettings.json* jako zdroj konfigurace pro příznaky funkcí. Následující příklad ukazuje, jak nastavit příznaky prvků v souboru JSON:

```JSON
"FeatureManagement": {
    "FeatureA": true, // Feature flag set to on
    "FeatureB": false, // Feature flag set to off
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

Podle konvence `FeatureManagement` se část tohoto dokumentu JSON používá pro nastavení příznaku prvku. Předchozí příklad ukazuje tři příznaky funkce s `EnabledFor` jejich filtry definované ve vlastnosti:

* `FeatureA`je *zapnuta*.
* `FeatureB`je *vypnuto*.
* `FeatureC`určuje filtr pojmenovaný `Percentage` s `Parameters` vlastností. `Percentage`je konfigurovatelný filtr. V tomto `Percentage` příkladu určuje 50% pravděpodobnost `FeatureC` příznaku, který má být *na*.

## <a name="feature-flag-references"></a>Odkazy na příznak prvku

Abyste mohli snadno odkazovat na příznaky funkce v `enum` kódu, měli byste je definovat jako proměnné:

```csharp
public enum MyFeatureFlags
{
    FeatureA,
    FeatureB,
    FeatureC
}
```

## <a name="feature-flag-checks"></a>Kontroly příznaku funkce

Základní vzor správy prvků je nejprve zkontrolovat, zda je příznak prvku nastaven *na*. Pokud ano, správce funkcí pak spustí akce, které funkce obsahuje. Například:

```csharp
IFeatureManager featureManager;
...
if (await featureManager.IsEnabledAsync(nameof(MyFeatureFlags.FeatureA)))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Injektáž závislosti

V ASP.NET Core MVC, můžete `IFeatureManager` přistupovat správce funkcí prostřednictvím vkládání závislostí:

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

## <a name="controller-actions"></a>Akce řadiče

V řadičích MVC můžete `FeatureGate` pomocí atributu řídit, zda je povolena celá třída řadiče nebo určitá akce. Následující `HomeController` řadič `FeatureA` vyžaduje, aby se *na* před jakoukoli akci controller třídy obsahuje může být provedena:

```csharp
[FeatureGate(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

Před `Index` spuštěním `FeatureA` je třeba *ji zapnout* následující akce:

```csharp
[FeatureGate(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

Pokud je řadič nebo akce MVC blokována, protože `IDisabledFeaturesHandler` je *vypnutý*příznak ovládací funkce , je voláno registrované rozhraní. Výchozí `IDisabledFeaturesHandler` rozhraní vrátí 404 stavový kód klientovi bez těla odezvy.

## <a name="mvc-views"></a>Zobrazení MVC

V zobrazeních MVC můžete `<feature>` použít značku k vykreslení obsahu na základě toho, zda je povolen příznak prvku:

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

Chcete-li zobrazit alternativní obsah, `negate` pokud nejsou splněny požadavky, lze použít atribut.

```html
<feature name="FeatureA" negate="true">
    <p>This will be shown if 'FeatureA' is disabled.</p>
</feature>
```

Značku `<feature>` prvku lze také použít k zobrazení obsahu, pokud jsou povoleny některé nebo všechny funkce v seznamu.

```html
<feature name="FeatureA, FeatureB" requirement="All">
    <p>This can only be seen if 'FeatureA' and 'FeatureB' are enabled.</p>
</feature>
<feature name="FeatureA, FeatureB" requirement="Any">
    <p>This can be seen if 'FeatureA', 'FeatureB', or both are enabled.</p>
</feature>
```

## <a name="mvc-filters"></a>Filtry MVC

Filtry MVC můžete nastavit tak, aby byly aktivovány na základě stavu příznaku prvku. Následující kód přidá filtr MVC s názvem `SomeMvcFilter`. Tento filtr se aktivuje v rámci `FeatureA` kanálu MVC pouze v případě, že je povolena. Tato funkce je `IAsyncActionFilter`omezena na . 

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

## <a name="middleware"></a>Middleware

Příznaky funkcí můžete také použít k podmíněnému přidání větví aplikací a middlewaru. Následující kód vloží součást middlewaru do kanálu `FeatureA` požadavků pouze v případě, že je povolena:

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(nameof(MyFeatureFlags.FeatureA));
```

Tento kód sestaví z obecnější schopnost i větvení celé aplikace na základě příznaku funkce:

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak implementovat `Microsoft.FeatureManagement` příznaky funkcí v aplikaci ASP.NET Core pomocí knihoven. Další informace o podpoře správy funkcí v ASP.NET core a konfigurace aplikací najdete v následujících zdrojích:

* [ukázkový kód příznaku funkce ASP.NET](/azure/azure-app-configuration/quickstart-feature-flag-aspnet-core)
* [Dokumentace microsoft.featuremanagement](https://docs.microsoft.com/dotnet/api/microsoft.featuremanagement)
* [Správa příznaků funkcí](./manage-feature-flags.md)
