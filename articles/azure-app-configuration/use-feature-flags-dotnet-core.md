---
title: Kurz použití příznaků funkcí v aplikaci .NET Core | Microsoft Docs
description: V tomto kurzu se naučíte implementovat příznaky funkcí v aplikacích .NET Core.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/17/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 701fe4ffc6147086dde740bfdb2dc7db92508e28
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100380232"
---
# <a name="tutorial-use-feature-flags-in-an-aspnet-core-app"></a>Kurz: používání příznaků funkcí v aplikaci ASP.NET Core

Knihovny pro správu funkcí .NET Core poskytují podporu idiomatickou pro implementaci příznaků funkcí v aplikaci .NET nebo ASP.NET Core. Tyto knihovny umožňují deklarativní Přidání příznaků funkcí do kódu, takže není nutné ručně psát kód pro povolení nebo zakázání funkcí s `if` příkazy.

Knihovny správy funkcí také spravují životní cykly příznaků funkcí na pozadí. Například knihovny aktualizace knihoven a příznak cache nebo zaručují, že stav příznaku by měl být během volání žádosti neměnný. Kromě toho knihovna ASP.NET Core nabízí předem připravené integrace, včetně akcí kontroleru MVC, zobrazení, tras a middlewaru.

[Příznaky funkce přidat do nástroje pro rychlý start ASP.NET Core aplikaci](./quickstart-feature-flag-aspnet-core.md) ukazují jednoduchý příklad použití příznaků funkcí v aplikaci ASP.NET Core. V tomto kurzu se dozvíte o dalších možnostech instalace a možnostech knihoven správy funkcí. Ukázkovou aplikaci vytvořenou v rychlém startu můžete použít k vyzkoušení ukázkového kódu zobrazeného v tomto kurzu. 

Referenční dokumentaci k rozhraní API pro správu funkcí ASP.NET Core najdete v tématu [obor názvů Microsoft. FeatureManagement](/dotnet/api/microsoft.featuremanagement).

V tomto kurzu se naučíte, jak:

> [!div class="checklist"]
> * Přidání příznaků funkcí v klíčových částech vaší aplikace pro řízení dostupnosti funkcí.
> * Integrujte s konfigurací aplikace, když ji používáte ke správě příznaků funkcí.

## <a name="set-up-feature-management"></a>Nastavení správy funkcí

Aby bylo možné získat přístup ke Správci funkcí .NET Core, vaše aplikace musí mít odkazy na `Microsoft.FeatureManagement.AspNetCore` balíček NuGet.

Správce funkcí .NET Core je nakonfigurovaný z nativního konfiguračního systému rozhraní. V důsledku toho můžete definovat nastavení příznaku funkce vaší aplikace pomocí libovolného zdroje konfigurace, který podporuje .NET Core, včetně místní *appsettings.jsv* proměnných souboru nebo prostředí.

Ve výchozím nastavení načte správce funkcí konfiguraci příznaků funkcí z `"FeatureManagement"` oddílu konfiguračních dat .NET Core. Chcete-li použít výchozí umístění konfigurace, zavolejte metodu [AddFeatureManagement](/dotnet/api/microsoft.featuremanagement.servicecollectionextensions.addfeaturemanagement) **IServiceCollection** předanou metodě **ConfigureServices** třídy **Startup** .


```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement();
    }
}
```

Můžete zadat, aby se konfigurace správy funkcí načetla z jiného konfiguračního oddílu, a to voláním metody [Configuration. GetSection](/dotnet/api/microsoft.web.administration.configuration.getsection) a předáním názvu požadované části. Následující příklad oznamuje správci funkcí, aby načetl z jiného oddílu s názvem `"MyFeatureFlags"` místo toho:

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement(Configuration.GetSection("MyFeatureFlags"));
    }
}
```


Použijete-li v příznacích funkce filtry, je nutné zahrnout obor názvů [Microsoft. FeatureManagement. FeatureFilters](/dotnet/api/microsoft.featuremanagement.featurefilters) a přidat volání [AddFeatureFilters](/dotnet/api/microsoft.featuremanagement.ifeaturemanagementbuilder.addfeaturefilter) určující název typu filtru, který chcete použít jako obecný typ metody. Další informace o používání filtrů funkcí k dynamickému povolení a zakázání funkcí najdete v tématu [Povolení připraveného zavedení funkcí pro cílové cílové skupiny](/azure/azure-app-configuration/howto-targetingfilter-aspnet-core).

Následující příklad ukazuje, jak použít vestavěný filtr funkcí nazvaný `PercentageFilter` :



```csharp
using Microsoft.FeatureManagement;
using Microsoft.FeatureManagement.FeatureFilters;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement()
                .AddFeatureFilter<PercentageFilter>();
    }
}
```

Místo hardwarového kódování příznaků vaší aplikace doporučujeme zachovat příznaky funkcí mimo aplikaci a spravovat je samostatně. V takovém případě vám umožní kdykoli změnit stav příznaku a tyto změny se projeví v aplikaci hned. Služba Azure App Configuration poskytuje vyhrazené uživatelské rozhraní portálu pro správu všech příznaků funkcí. Služba Azure App Configuration taky do vaší aplikace poskytuje příznaky funkcí přímo prostřednictvím svých klientských knihoven .NET Core.

Nejjednodušší způsob, jak připojit aplikaci ASP.NET Core ke konfiguraci aplikace, je prostřednictvím poskytovatele konfigurace, který je součástí `Microsoft.Azure.AppConfiguration.AspNetCore` balíčku NuGet. Po zahrnutí odkazu na balíček použijte následující postup k použití tohoto balíčku NuGet.

1. Otevřete soubor *program.cs* a přidejte následující kód.
    > [!IMPORTANT]
    > `CreateHostBuilder` nahrazuje `CreateWebHostBuilder` v .NET Core 3. x. Vyberte správnou syntaxi na základě vašeho prostředí.

    ### <a name="net-5x"></a>[.NET 5. x](#tab/core5x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
                }).UseStartup<Startup>());
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration(config =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
            }).UseStartup<Startup>());
    ```
        
    ### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                    .ConfigureAppConfiguration(config =>
                    {
                        var settings = config.Build();
                        config.AddAzureAppConfiguration(options =>
                            options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
                    }).UseStartup<Startup>();
    ```
    ---

2. Otevřete *Startup.cs* a aktualizujte `Configure` metodu a, `ConfigureServices` abyste přidali vestavěný middleware s názvem `UseAzureAppConfiguration` . Tento middleware umožňuje aktualizovat hodnoty příznaků funkcí v opakovaném intervalu, zatímco ASP.NET Core webová aplikace nadále přijímá požadavky.



    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        app.UseAzureAppConfiguration();
    }
    ```

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddAzureAppConfiguration();
   }
   ```
   
V typickém scénáři aktualizujete hodnoty příznaků funkcí pravidelně při nasazení a povolení a používání různých funkcí aplikace. Ve výchozím nastavení se hodnoty příznaku funkce ukládají do mezipaměti po dobu 30 sekund, takže se operace aktualizace aktivuje, když middleware obdrží žádost, aby neaktualizovala hodnotu, dokud nevyprší hodnota v mezipaměti. Následující kód ukazuje, jak změnit čas vypršení platnosti mezipaměti nebo interval dotazování na 5 minut nastavením [CacheExpirationInterval](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.featuremanagement.featureflagoptions.cacheexpirationinterval) ve volání **UseFeatureFlags**.


    
```csharp
config.AddAzureAppConfiguration(options =>
    options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags(featureFlagOptions => {
        featureFlagOptions.CacheExpirationInterval = TimeSpan.FromMinutes(5);
    }));
});
```


## <a name="feature-flag-declaration"></a>Deklarace příznaku funkce

Každá deklarace příznaku funkce má dvě části: název a seznam jednoho nebo více filtrů, které se používají k vyhodnocení, jestli je stav funkce *zapnutý* (to znamená, když je jeho hodnota `True` ). Filtr definuje kritérium, kdy má být funkce zapnuta.

Pokud má příznak funkce více filtrů, seznam filtrů se prochází v pořadí, dokud jeden z filtrů neurčí, že by měla být funkce povolená. V tomto okamžiku je příznak funkce *zapnutý* a všechny zbývající výsledky filtru se přeskočí. Pokud žádný filtr neindikuje, že by měla být funkce povolená, příznak funkce je *vypnutý*.

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

* `FeatureA` je *zapnuto*.
* `FeatureB` je *vypnutý*.
* `FeatureC` Určuje filtr s názvem `Percentage` s `Parameters` vlastností. `Percentage` je konfigurovatelný filtr. V tomto příkladu `Percentage` určujeme pravděpodobnost 50-Percent pro příznak, `FeatureC` který má být *zapnut*. Návod, jak používat filtry funkcí, najdete v tématu [použití filtrů funkcí k zapnutí příznaků podmíněné funkce](/azure/azure-app-configuration/howto-feature-filters-aspnet-core).




## <a name="use-dependency-injection-to-access-ifeaturemanager"></a>Použití injektáže závislosti pro přístup k IFeatureManager 

U některých operací, jako je například ruční kontrola hodnot příznaku funkce, je třeba získat instanci [IFeatureManager](https://docs.microsoft.com/dotnet/api/microsoft.featuremanagement.ifeaturemanager?view=azure-dotnet-preview). Ve službě ASP.NET Core MVC můžete ke Správci funkcí přistupovat `IFeatureManager` pomocí injektáže závislosti. V následujícím příkladu `IFeatureManager` je do podpisu konstruktoru pro řadič přidán argument typu. Modul runtime automaticky vyřeší odkaz a poskytuje rozhraní při volání konstruktoru. Pokud používáte šablonu aplikace, ve které kontroler již obsahuje jeden nebo více argumentů vkládání závislostí v konstruktoru, například `ILogger` , můžete přidat `IFeatureManager` jako další argument:

### <a name="net-5x"></a>[.NET 5. x](#tab/core5x)
    
```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(ILogger<HomeController> logger, IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(ILogger<HomeController> logger, IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```
    
### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

---

## <a name="feature-flag-references"></a>Odkazy na příznak funkce

Definujte příznaky funkcí jako řetězcové proměnné, aby na ně odkazovaly z kódu:

```csharp
public static class MyFeatureFlags
{
    public const string FeatureA = "FeatureA";
    public const string FeatureB = "FeatureB";
    public const string FeatureC = "FeatureC";
}
```

## <a name="feature-flag-checks"></a>Kontroly příznaků funkcí

Běžným vzorem správy funkcí je zjištění, zda je příznak funkce nastaven na hodnotu *zapnuto* a pokud ano, spusťte oddíl kódu. Příklad:

```csharp
IFeatureManager featureManager;
...
if (await featureManager.IsEnabledAsync(MyFeatureFlags.FeatureA))
{
    // Run the following code
}
```

## <a name="controller-actions"></a>Akce kontroleru

S řadiči MVC můžete použít `FeatureGate` atribut k řízení, zda je povolena celá třída řadiče nebo konkrétní akce. Následující `HomeController` kontroler vyžaduje, `FeatureA` aby *bylo možné* provést všechny akce, které třída Controller obsahuje:

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

Když je kontroler MVC nebo akce zablokovaná, protože příznak řídicí funkce je *vypnutý*, zavolá se registrované rozhraní [IDisabledFeaturesHandler](/dotnet/api/microsoft.featuremanagement.mvc.idisabledfeatureshandler?view=azure-dotnet-preview) . Výchozí `IDisabledFeaturesHandler` rozhraní vrátí klientovi stav 404 bez těla odpovědi.

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

Filtry MVC můžete nastavit tak, aby byly aktivovány na základě stavu příznaku funkce. Tato funkce je omezená na filtry, které implementují [IAsyncActionFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncactionfilter). Následující kód přidá filtr MVC s názvem `ThirdPartyActionFilter` . Tento filtr se aktivuje v rámci kanálu MVC jenom v případě `FeatureA` , že je povolený.  

```csharp
using Microsoft.FeatureManagement.FeatureFilters;

IConfiguration Configuration { get; set;}

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options => {
        options.Filters.AddForFeature<ThirdPartyActionFilter>(MyFeatureFlags.FeatureA);
    });
}
```

## <a name="middleware"></a>Middleware

Příznaky funkcí můžete použít také k podmíněnému přidávání větví aplikace a middlewaru. Následující kód Vloží komponentu middlewaru do kanálu žádosti pouze v případě, že `FeatureA` je povolena:

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(MyFeatureFlags.FeatureA);
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
