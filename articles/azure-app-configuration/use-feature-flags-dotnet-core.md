---
title: Kurz pro používání příznaků funkcí v aplikaci .NET Core | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak implementovat příznaků funkcí v aplikacích .NET Core.
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
ms.openlocfilehash: 99559c0c77c3e4b29badec1c0be2d741df1f0621
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798379"
---
# <a name="tutorial-use-feature-flags-in-an-aspnet-core-app"></a>Kurz: Použití příznaků funkcí v aplikaci ASP.NET Core

Knihovny .NET Core funkce správy idiomatickou podporují implementace příznaků funkcí v aplikaci .NET nebo ASP.NET Core. Tyto knihovny umožňují deklarativně přidat příznaky funkcí pro váš kód tak, že nemusíte psát veškerý `if` příkazy pro ně ručně.

Knihovny správy funkce také spravovat životní cyklus příznak funkce na pozadí. Například knihovny aktualizovat a mezipaměti příznak stavy nebo zajištění neměnných příznak stavu během volání žádosti. Kromě toho knihovna ASP.NET Core nabízí integrace out-of-the-box, včetně akce řadiče MVC, zobrazení, cesty a middlewaru.

[Přidat příznaků funkcí do aplikace ASP.NET Core pro rychlý Start](./quickstart-feature-flag-aspnet-core.md) ukazuje několik způsobů, jak přidat příznaků funkcí v aplikaci ASP.NET Core. Tento kurz vysvětluje tyto metody podrobněji. Úplný popis najdete v článku [dokumentaci k ASP.NET Core funkci správy](https://go.microsoft.com/fwlink/?linkid=2091410).

V tomto kurzu se naučíte, jak:

> [!div class="checklist"]
> * Přidáte příznaků funkcí v klíčových částí aplikace pro řízení dostupnosti funkce.
> * Integrace s konfigurace aplikace, když ho používáte ke správě příznaky funkcí.

## <a name="set-up-feature-management"></a>Nastavení správy pro funkce

Správce funkce .NET Core `IFeatureManager` získá příznaky funkcí z rozhraní framework nativní konfigurace systému. V důsledku toho můžete definovat příznaků funkcí vaší aplikace pomocí libovolného konfigurace zdroje, který podporuje .NET Core, včetně místní *appsettings.json* proměnné souboru nebo prostředí. `IFeatureManager` spoléhá na .NET Core vkládání závislostí. Funkce služby správy můžete registrovat pomocí standardní konvence:

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

Ve výchozím nastavení, funkce správce načte příznaků funkcí z `"FeatureManagement"` části .NET Core konfigurační data. Následující příklad určuje funkci správce ke čtení z jiného oddílu volat `"MyFeatureFlags"` místo:

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

Pokud použijete filtry ve vaší příznaky funkcí, musíte zahrnout další knihovny a zaregistrujte ho. Následující příklad ukazuje, jak použít filtr integrovanou funkci s názvem `PercentageFilter`:

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

Doporučujeme zachovat příznaků funkcí mimo aplikaci a spravovat samostatně. To můžete kdykoli upravit příznak stavy a se tyto změny se projeví v aplikaci hned. Konfigurace aplikace poskytuje centrálním umístění pro uspořádání a řízení všech příznaků funkcí přes vyhrazené uživatelské rozhraní portálu. Konfigurace aplikace také nabízí příznaky do vaší aplikace přímo prostřednictvím klienta .NET Core knihovny.

Nejjednodušší způsob, jak připojit aplikaci ASP.NET Core do konfigurace aplikace je prostřednictvím poskytovatele konfigurace `Microsoft.Azure.AppConfiguration.AspNetCore`. Použijte následující postup použijte tento balíček NuGet.

1. Otevřít *Program.cs* a přidejte následující kód.

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

2. Otevřít *Startup.cs* a aktualizovat `Configure` způsob, jak přidat middleware pro povolení funkce hodnot příznaku se aktualizují v opakovaných intervalech při ASP.NET Core webová aplikace i nadále přijímat požadavky.

   ```csharp
   public void Configure(IApplicationBuilder app, IHostingEnvironment env)
   {
       app.UseAzureAppConfiguration();
       app.UseMvc();
   }
   ```

Očekává se, že hodnoty příznak funkce v průběhu času měnit. Ve výchozím nastavení hodnoty příznaků funkcí jsou uložené v mezipaměti po dobu 30 sekund, takže operace aktualizace aktivuje, když middleware obdrží žádost by aktualizovat hodnotu, dokud nevyprší hodnota uložená v mezipaměti. Následující kód ukazuje, jak změnit čas vypršení platnosti mezipaměti nebo interval cyklického dotazování na 5 minut `options.UseFeatureFlags()` volání.

```csharp
config.AddAzureAppConfiguration(options => {
    options.Connect(settings["ConnectionStrings:AppConfig"])
           .UseFeatureFlags(featureFlagOptions => {
                featureFlagOptions.CacheExpirationTime = TimeSpan.FromMinutes(5);
           });
});
```

## <a name="feature-flag-declaration"></a>Deklarace příznak funkce

Každý příznak funkce má dvě části: název a seznam jednoho nebo více filtrů, které se používají k vyhodnocení, jestli je stav funkce *na* (to znamená, když je jeho hodnota `True`). Filtr definuje pro případ použití, když se funkce by měla být nastavená na on.

Pokud příznak funkce má několik filtrů, je seznam filtrů Procházet v pořadí, dokud jeden z filtrů zjistí, že je že povolena funkce. V tomto okamžiku je příznak funkce *na*, a všechny zbývající výsledky filtru jsou vynechány. Pokud žádný filtr. Tato funkce povolena, je příznak funkce *vypnout*.

Podporuje funkce správce *appsettings.json* jako zdroj konfigurace pro příznaky funkcí. Následující příklad ukazuje, jak nastavit příznaky funkcí v souboru JSON:

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

Podle konvence `FeatureManagement` část tohoto dokumentu JSON se používá pro nastavení příznaku funkce. Předchozí příklad ukazuje tři příznaků funkcí s jejich filtrům definovaným v `EnabledFor` vlastnost:

* `FeatureA` je *na*.
* `FeatureB` je *vypnout*.
* `FeatureC` Určuje filtr s názvem `Percentage` s `Parameters` vlastnost. `Percentage` se dají Konfigurovat filtr. V tomto příkladu `Percentage` určuje pravděpodobnost 50 procent `FeatureC` příznak *na*.

## <a name="feature-flag-references"></a>Odkazy příznak funkce

Tak, aby příznaky funkcí v kódu můžete snadno odkazovat, měli byste je definovat jako `enum` proměnné:

```csharp
public enum MyFeatureFlags
{
    FeatureA,
    FeatureB,
    FeatureC
}
```

## <a name="feature-flag-checks"></a>Kontroly příznak funkce

Základní princip funkce správy, je nejprve zkontrolujte Pokud příznak funkce nastavená na *na*. Pokud ano, funkce Správce pak spustí akce, který obsahuje funkci. Příklad:

```csharp
IFeatureManager featureManager;
...
if (featureManager.IsEnabled(nameof(MyFeatureFlags.FeatureA)))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Injektáž závislosti

V ASP.NET Core MVC, získáte přístup k funkci správce `IFeatureManager` pomocí vkládání závislostí:

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

## <a name="controller-actions"></a>Akce kontroleru

V řadiče MVC, je použít `FeatureGate` atribut pro ovládací prvek, jestli je povolená třída celý kontroleru nebo konkrétní akci. Následující `HomeController` kontroler vyžaduje `FeatureA` bude *na* předtím, než je možné provést žádnou akci, která obsahuje třídy kontroleru:

```csharp
[FeatureGate(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

Následující `Index` akce vyžaduje `FeatureA` bude *na* před spuštěním:

```csharp
[FeatureGate(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

Když MVC kontroler nebo akce je zablokovaná, protože je příznak řídící funkce *vypnout*, registrovaný `IDisabledFeaturesHandler` je názvem rozhraní. Výchozí hodnota `IDisabledFeaturesHandler` rozhraní vrátí stavový kód 404 klientovi s žádnou odpovědí.

## <a name="mvc-views"></a>Zobrazení MVC

V zobrazení MVC, můžete použít `<feature>` značky k vykreslení obsahu založené na tom, zda je povoleno příznak funkce:

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

K zobrazení alternativní obsahu, pokud nejsou splněny požadavky `negate` atribut lze použít.

```html
<feature name="FeatureA" negate="true">
    <p>This will be shown if 'FeatureA' is disabled.</p>
</feature>
```

Funkci `<feature>` značky lze použít také k zobrazení obsahu, pokud existuje nebo jsou povoleny všechny funkce v seznamu.

```html
<feature name="FeatureA, FeatureB" requirement="All">
    <p>This can only be seen if 'FeatureA' and 'FeatureB' are enabled.</p>
</feature>
<feature name="FeatureA, FeatureB" requirement="Any">
    <p>This can be seen if 'FeatureA', 'FeatureB', or both are enabled.</p>
</feature>
```

## <a name="mvc-filters"></a>Filtry MVC

MVC filtry můžete nastavit tak, že jsou aktivovány, na základě stavu příznak funkce. Následující kód přidá filtr MVC s názvem `SomeMvcFilter`. Tento filtr se aktivuje v rámci pouze pokud kanál MVC `FeatureA` je povolená.

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

## <a name="routes"></a>Trasy

Příznaky funkcí můžete dynamicky vystavit trasy. Následující kód přidá trasu, která nastavuje hodnoty `Beta` jako výchozí kontroler pouze tehdy, když `FeatureA` zapnutá:

```csharp
app.UseMvc(routes => {
    routes.MapRouteForFeature(nameof(MyFeatureFlags.FeatureA), "betaDefault", "{controller=Beta}/{action=Index}/{id?}");
});
```

## <a name="middleware"></a>Middleware

Příznaky funkcí můžete použít také podmíněně přidání větve aplikace a middleware. Následující kód vloží komponenta middlewaru v požadavku kanálu pouze tehdy, když `FeatureA` zapnutá:

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(nameof(MyFeatureFlags.FeatureA));
```

Tento kód vytvoří mimo funkce obecné informace větvení celé aplikace založené na příznak funkce:

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak implementovat příznaků funkcí v aplikaci ASP.NET Core s použitím `Microsoft.FeatureManagement` knihovny. Další informace o řízení podporovaných funkcích v ASP.NET Core a konfigurace aplikací najdete v článku na následujících odkazech:

* [Příznak funkce ASP.NET Core ukázkový kód](/azure/azure-app-configuration/quickstart-feature-flag-aspnet-core)
* [Dokumentace ke službě Microsoft.FeatureManagement](https://docs.microsoft.com/dotnet/api/microsoft.featuremanagement)
* [Správa příznaků funkcí](./manage-feature-flags.md)
