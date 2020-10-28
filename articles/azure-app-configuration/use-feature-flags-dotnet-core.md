---
title: Kurz použití příznaků funkcí v aplikaci .NET Core | Microsoft Docs
description: V tomto kurzu se naučíte implementovat příznaky funkcí v aplikacích .NET Core.
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
ms.date: 09/17/2020
ms.author: lcozzens
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 6da2aa645549920cce2f5c0cfe8a32c98dc04708
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746132"
---
# <a name="tutorial-use-feature-flags-in-an-aspnet-core-app"></a>Kurz: používání příznaků funkcí v aplikaci ASP.NET Core

Knihovny pro správu funkcí .NET Core poskytují podporu idiomatickou pro implementaci příznaků funkcí v aplikaci .NET nebo ASP.NET Core. Tyto knihovny umožňují deklarativní Přidání příznaků funkcí do kódu, takže není nutné zapisovat všechny `if` příkazy pro ně ručně.

Knihovny správy funkcí také spravují životní cykly příznaků funkcí na pozadí. Například knihovny aktualizace knihoven a příznak cache nebo zaručují, že stav příznaku by měl být během volání žádosti neměnný. Kromě toho knihovna ASP.NET Core nabízí předem připravené integrace, včetně akcí kontroleru MVC, zobrazení, tras a middlewaru.

[Příznaky funkce přidat ASP.NET Core aplikace v rychlém](./quickstart-feature-flag-aspnet-core.md) startu zobrazují několik způsobů přidávání příznaků funkcí do aplikace ASP.NET Core. V tomto kurzu se tyto metody vysvětlují podrobněji. Úplný odkaz najdete v [dokumentaci ke správě funkcí ASP.NET Core](/dotnet/api/microsoft.featuremanagement).

V tomto kurzu se naučíte, jak:

> [!div class="checklist"]
> * Přidání příznaků funkcí v klíčových částech vaší aplikace pro řízení dostupnosti funkcí.
> * Integrujte s konfigurací aplikace, když ji používáte ke správě příznaků funkcí.

## <a name="set-up-feature-management"></a>Nastavení správy funkcí

Přidejte odkaz na `Microsoft.FeatureManagement.AspNetCore` `Microsoft.FeatureManagement` balíčky a NuGet, abyste mohli využít správce funkcí .NET Core.
    
Správce funkcí .NET Core `IFeatureManager` získá příznaky funkcí z nativního konfiguračního systému rozhraní. V důsledku toho můžete definovat příznaky funkcí vaší aplikace pomocí libovolného zdroje konfigurace, který podporuje .NET Core, včetně místní *appsettings.jsv* proměnných souboru nebo prostředí. `IFeatureManager` spoléhá na vkládání závislostí .NET Core. Služby správy funkcí můžete zaregistrovat pomocí standardních konvencí:

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

Ve výchozím nastavení nástroj Správce funkcí načítá příznaky funkcí z `"FeatureManagement"` oddílu konfiguračních dat .NET Core. Následující příklad oznamuje správci funkcí, aby načetl z jiného oddílu s názvem `"MyFeatureFlags"` místo toho:

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

Pokud používáte filtry v příznacích funkcí, je třeba zahrnout další knihovnu a zaregistrovat ji. Následující příklad ukazuje, jak použít vestavěný filtr funkcí nazvaný `PercentageFilter` :

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

Doporučujeme zachovat příznaky funkcí mimo aplikaci a spravovat je samostatně. V takovém případě vám umožní kdykoli změnit stav příznaku a tyto změny se projeví v aplikaci hned. Konfigurace aplikací poskytuje centralizované místo pro organizování a řízení všech příznaků funkcí prostřednictvím vyhrazeného uživatelského rozhraní portálu. Konfigurace aplikace také poskytuje příznaky vaší aplikaci přímo prostřednictvím svých klientských knihoven .NET Core.

Nejjednodušší způsob, jak připojit aplikaci ASP.NET Core ke konfiguraci aplikace, je prostřednictvím poskytovatele konfigurace `Microsoft.Azure.AppConfiguration.AspNetCore` . Pro použití tohoto balíčku NuGet postupujte podle těchto kroků.

1. Otevřete soubor *program.cs* a přidejte následující kód.

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

2. Otevřete *Startup.cs* a aktualizujte `Configure` metodu tak, aby se přidal vestavěný middleware s názvem `UseAzureAppConfiguration` . Tento middleware umožňuje aktualizovat hodnoty příznaků funkcí v opakovaném intervalu, zatímco ASP.NET Core webová aplikace nadále přijímá požadavky.

   ```csharp
   public void Configure(IApplicationBuilder app, IHostingEnvironment env)
   {
       app.UseAzureAppConfiguration();
       app.UseMvc();
   }
   ```

Očekává se, že se v průběhu času mění hodnoty příznaků funkcí. Ve výchozím nastavení se hodnoty příznaku funkce ukládají do mezipaměti po dobu 30 sekund, takže se operace aktualizace aktivuje, když middleware obdrží žádost, aby neaktualizovala hodnotu, dokud nevyprší hodnota v mezipaměti. Následující kód ukazuje, jak změnit čas vypršení platnosti mezipaměti nebo interval dotazování na 5 minut ve `options.UseFeatureFlags()` volání.

```csharp
config.AddAzureAppConfiguration(options => {
    options.Connect(settings["ConnectionStrings:AppConfig"])
           .UseFeatureFlags(featureFlagOptions => {
                featureFlagOptions.CacheExpirationTime = TimeSpan.FromMinutes(5);
           });
});
```

## <a name="feature-flag-declaration"></a>Deklarace příznaku funkce

Každý příznak funkce má dvě části: název a seznam jednoho nebo více filtrů, které se používají k vyhodnocení, zda je stav funkce *zapnut* (tj. je-li jeho hodnota `True` ). Filtr definuje případ použití, kdy má být funkce zapnuta.

Pokud má příznak funkce více filtrů, seznam filtrů se prochází v pořadí, dokud jeden z filtrů neurčí, že by měla být funkce povolená. V tomto okamžiku je příznak funkce *zapnutý* a všechny zbývající výsledky filtru se přeskočí. Pokud žádný filtr neindikuje, že by měla být funkce povolená, příznak funkce je *vypnutý* .

Správce funkcí podporuje *appsettings.js* jako zdroj konfigurace pro příznaky funkcí. Následující příklad ukazuje, jak nastavit příznaky funkcí v souboru JSON:

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

Podle konvence se `FeatureManagement` oddíl tohoto dokumentu JSON používá pro nastavení příznaku funkce. Předchozí příklad ukazuje tři příznaky funkcí s jejich filtry definovanými ve `EnabledFor` vlastnosti:

* `FeatureA` je *zapnuto* .
* `FeatureB` je *vypnutý* .
* `FeatureC` Určuje filtr s názvem `Percentage` s `Parameters` vlastností. `Percentage` je konfigurovatelný filtr. V tomto příkladu `Percentage` určujeme pravděpodobnost 50-Percent pro příznak, `FeatureC` který má být *zapnut* .

## <a name="feature-flag-references"></a>Odkazy na příznak funkce

Abyste mohli snadno odkazovat na příznaky funkcí v kódu, měli byste je definovat jako `enum` proměnné:

```csharp
public enum MyFeatureFlags
{
    FeatureA,
    FeatureB,
    FeatureC
}
```

## <a name="feature-flag-checks"></a>Kontroly příznaků funkcí

Základním vzorem správy funkcí je nejprve zjistit, zda je příznak funkce nastaven na hodnotu *zapnuto* . V takovém případě správce funkcí spustí akce, které funkce obsahuje. Příklad:

```csharp
IFeatureManager featureManager;
...
if (await featureManager.IsEnabledAsync(nameof(MyFeatureFlags.FeatureA)))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Injektáž závislostí

Ve službě ASP.NET Core MVC můžete ke Správci funkcí přistupovat `IFeatureManager` pomocí injektáže závislosti:

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

V řadičích MVC pomocí atributu určíte, `FeatureGate` zda je povolena celá třída řadiče nebo konkrétní akce. Následující `HomeController` kontroler vyžaduje, `FeatureA` aby *bylo možné* provést všechny akce, které třída Controller obsahuje:

```csharp
using Microsoft.FeatureManagement.Mvc;

[FeatureGate(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

Než bude `Index` možné spustit tuto akci, musí `FeatureA` být *zapnutá* následující akce:

```csharp
using Microsoft.FeatureManagement.Mvc;

[FeatureGate(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

Když je kontroler MVC nebo akce zablokovaná, protože příznak řídicí funkce je *vypnutý* , `IDisabledFeaturesHandler` zavolá se registrované rozhraní. Výchozí `IDisabledFeaturesHandler` rozhraní vrátí klientovi stav 404 bez těla odpovědi.

## <a name="mvc-views"></a>Zobrazení MVC

Otevřete *_ViewImports. cshtml* v adresáři *zobrazení* a přidejte pomocníka značek správce funkcí:

```html
@addTagHelper *, Microsoft.FeatureManagement.AspNetCore
```

V zobrazeních MVC můžete použít `<feature>` značku k vykreslení obsahu na základě toho, zda je povolen příznak funkce:

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

Chcete-li zobrazit alternativní obsah v případě, že požadavky nejsou splněny, `negate` lze atribut použít.

```html
<feature name="FeatureA" negate="true">
    <p>This will be shown if 'FeatureA' is disabled.</p>
</feature>
```

Značku funkce `<feature>` lze také použít k zobrazení obsahu, pokud jsou povoleny některé nebo všechny funkce v seznamu.

```html
<feature name="FeatureA, FeatureB" requirement="All">
    <p>This can only be seen if 'FeatureA' and 'FeatureB' are enabled.</p>
</feature>
<feature name="FeatureA, FeatureB" requirement="Any">
    <p>This can be seen if 'FeatureA', 'FeatureB', or both are enabled.</p>
</feature>
```

## <a name="mvc-filters"></a>Filtry MVC

Filtry MVC můžete nastavit tak, aby byly aktivovány na základě stavu příznaku funkce. Následující kód přidá filtr MVC s názvem `SomeMvcFilter` . Tento filtr se aktivuje v rámci kanálu MVC jenom v případě `FeatureA` , že je povolený. Tato funkce je omezená na `IAsyncActionFilter` . 

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

Příznaky funkcí můžete použít také k podmíněnému přidávání větví aplikace a middlewaru. Následující kód Vloží komponentu middlewaru do kanálu žádosti pouze v případě, že `FeatureA` je povolena:

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(nameof(MyFeatureFlags.FeatureA));
```

Tento kód se sestaví na základě obecnější funkce pro vytvoření větve celé aplikace na základě příznaku funkce:

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak implementovat příznaky funkcí do aplikace ASP.NET Core pomocí `Microsoft.FeatureManagement` knihoven. Další informace o podpoře správy funkcí v ASP.NET Core a konfiguraci aplikací najdete v následujících zdrojích informací:

* [Ukázkový kód příznaku funkce ASP.NET Core](./quickstart-feature-flag-aspnet-core.md)
* [Dokumentace k Microsoft. FeatureManagement](/dotnet/api/microsoft.featuremanagement)
* [Správa příznaků funkcí](./manage-feature-flags.md)