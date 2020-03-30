---
title: Kanály telemetrie v Přehledech aplikací Azure | Dokumenty společnosti Microsoft
description: Jak přizpůsobit telemetrické kanály v sadách Azure Application Insights SDK pro .NET a .NET Core.
ms.topic: conceptual
ms.date: 05/14/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 9c292246f947e4d3a364f79b31fe7a1deebd33d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275695"
---
# <a name="telemetry-channels-in-application-insights"></a>Kanály telemetrie v Application Insights

Kanály telemetrie jsou nedílnou součástí [sad SDK SDK SDK s Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Spravují ukládání do vyrovnávací paměti a přenos telemetrie do služby Application Insights. Verze sad SDK .NET a .NET Core mají dva předdefinované `InMemoryChannel` `ServerTelemetryChannel`telemetrické kanály: a . Tento článek podrobně popisuje každý kanál, včetně jak přizpůsobit chování kanálu.

## <a name="what-are-telemetry-channels"></a>Co jsou telemetrické kanály?

Telemetrické kanály jsou zodpovědné za ukládání položek telemetrie do vyrovnávací paměti a jejich odesílání do služby Application Insights, kde jsou uloženy pro dotazování a analýzu. Kanál telemetrie je libovolná [`Microsoft.ApplicationInsights.ITelemetryChannel`](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet) třída, která implementuje rozhraní.

Metoda `Send(ITelemetry item)` telemetrického kanálu je volána po volání všech telemetrických inicializátorů a telemetrických procesorů. Takže všechny položky vynechány telemetrického procesoru nedosáhne kanálu. `Send()`obvykle neodesílá položky do zadní části okamžitě. Obvykle je vyrovnávací paměti v paměti a odešle je v dávkách pro efektivní přenos.

[Live Metrics Stream](live-stream.md) má také vlastní kanál, který pohání živé streamování telemetrie. Tento kanál je nezávislý na pravidelné telemetrické kanálu a tento dokument se nevztahuje na něj.

## <a name="built-in-telemetry-channels"></a>Vestavěné telemetrické kanály

Sady Application Insights .NET a .NET Core SDK se doručují se dvěma vestavěnými kanály:

* `InMemoryChannel`: Lehký kanál, který ukládání položek do vyrovnávací paměti do vyrovnávací paměti, dokud nejsou odeslány. Položky jsou uloženy do vyrovnávací paměti a vyprázdněny jednou za 30 sekund nebo při každém ukládání do vyrovnávací paměti 500 položek. Tento kanál nabízí minimální záruky spolehlivosti, protože nezkusí po selhání opakovat odesílání telemetrie. Tento kanál také neuchovává položky na disku, takže všechny neodeslané položky jsou ztraceny trvale při vypnutí aplikace (řádné nebo ne). Tento kanál implementuje metodu, `Flush()` která lze použít k vynucení vyprázdnění všech položek telemetrie v paměti synchronně. Tento kanál je vhodný pro krátkodobé aplikace, kde je ideální synchronní vyprázdnění.

    Tento kanál je součástí většího balíčku Microsoft.ApplicationInsights NuGet a je výchozí kanál, který sada SDK používá, když není nakonfigurováno nic jiného.

* `ServerTelemetryChannel`: Pokročilejší kanál, který má zásady opakování a schopnost ukládat data na místním disku. Tento kanál opakuje odesílání telemetrie, pokud dojde k přechodným chybám. Tento kanál také používá místní diskové úložiště k udržení položek na disku během výpadků sítě nebo vysokých telemetrických svazků. Z důvodu těchto mechanismů opakování a místní diskové úložiště, tento kanál je považován za spolehlivější a je doporučeno pro všechny produkční scénáře. Tento kanál je výchozí pro [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) a [ASP.NET základní](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) aplikace, které jsou konfigurovány podle oficiální dokumentace. Tento kanál je optimalizován pro serverové scénáře s dlouhotrvajícími procesy. Metoda, [`Flush()`](#which-channel-should-i-use) která je implementována tímto kanálem není synchronní.

    Tento kanál je dodáván jako balíček Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel NuGet a je získán automaticky, když použijete Microsoft.ApplicationInsights.Web nebo Microsoft.ApplicationInsights.AspNetCore NuGet Balíček.

## <a name="configure-a-telemetry-channel"></a>Konfigurace telemetrického kanálu

Kanál telemetrie nakonfigurujete nastavením na aktivní konfiguraci telemetrie. Pro ASP.NET aplikace konfigurace zahrnuje nastavení instance `TelemetryConfiguration.Active`telemetrického `ApplicationInsights.config`kanálu na , nebo úpravou . Pro ASP.NET základní aplikace konfigurace zahrnuje přidání kanálu do kontejneru vkládání závislostí.

V následujících částech jsou uvedeny příklady konfigurace `StorageFolder` nastavení kanálu v různých typech aplikací. `StorageFolder`je pouze jedním z konfigurovatelných nastavení. Úplný seznam nastavení konfigurace naleznete [v části nastavení](telemetry-channels.md#configurable-settings-in-channels) dále v tomto článku.

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>Konfigurace pomocí souboru ApplicationInsights.config pro ASP.NET aplikace

Následující část z [ApplicationInsights.config](configuration-with-applicationinsights-config.md) zobrazuje `ServerTelemetryChannel` `StorageFolder` kanál nakonfigurovaný s nastaveným na vlastní umístění:

```xml
    <TelemetrySinks>
        <Add Name="default">
            <TelemetryProcessors>
                <!-- Telemetry processors omitted for brevity  -->
            </TelemetryProcessors>
            <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel">
                <StorageFolder>d:\temp\applicationinsights</StorageFolder>
            </TelemetryChannel>
        </Add>
    </TelemetrySinks>
```

### <a name="configuration-in-code-for-aspnet-applications"></a>Konfigurace v kódu pro ASP.NET aplikace

Následující kód nastaví instanci "ServerTelemetryChannel" s `StorageFolder` nastavenou na vlastní umístění. Přidejte tento kód na začátku aplikace, obvykle v `Application_Start()` metodě v Global.aspx.cs.

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
protected void Application_Start()
{
    var serverTelemetryChannel = new ServerTelemetryChannel();
    serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
    serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
    TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
}
```

### <a name="configuration-in-code-for-aspnet-core-applications"></a>Konfigurace v kódu pro ASP.NET základní aplikace

Upravte `ConfigureServices` metodu `Startup.cs` třídy, jak je znázorněno zde:

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

public void ConfigureServices(IServiceCollection services)
{
    // This sets up ServerTelemetryChannel with StorageFolder set to a custom location.
    services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel() {StorageFolder = @"d:\temp\applicationinsights" });

    services.AddApplicationInsightsTelemetry();
}

```

> [!IMPORTANT]
> Konfigurace kanálu pomocí `TelemetryConfiguration.Active` se nedoporučuje pro ASP.NET aplikace Core.

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>Konfigurace v kódu pro aplikace konzoly .NET/.NET Core

Pro konzolové aplikace je kód stejný pro rozhraní .NET i .NET Core:

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>Provozní podrobnosti servertelemetrieChannel

`ServerTelemetryChannel`ukládá příchozí položky do vyrovnávací paměti v paměti. Položky jsou serializovány, komprimovány `Transmission` a uloženy do instance jednou za 30 sekund nebo při ukládání do vyrovnávací paměti 500 položek. Jedna `Transmission` instance obsahuje až 500 položek a představuje dávku telemetrie, která je odeslána prostředpo jednoho volání HTTPS do služby Application Insights.

Ve výchozím nastavení lze `Transmission` paralelně odeslat maximálně 10 instancí. Pokud telemetrie přichází rychlejším tempem nebo pokud je síť nebo `Transmission` back-end Application Insights pomalý, instance jsou uloženy v paměti. Výchozí kapacita této vyrovnávací `Transmission` paměti v paměti je 5 MB. Po překročení kapacity v paměti `Transmission` jsou instance uloženy na místním disku s kapacitou až 50 MB. `Transmission`instance jsou uloženy na místním disku i v případě problémů se sítí. Selhání aplikace přetrvají pouze s položkami, které jsou uloženy na místním disku. Jsou odesílány vždy, když se aplikace znovu spustí.

## <a name="configurable-settings-in-channels"></a>Konfigurovatelná nastavení v kanálech

Úplný seznam konfigurovatelných nastavení pro každý kanál najdete v následujících tématech:

* [InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [ServerTelemetryKanál](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

Zde jsou nejčastěji používaná `ServerTelemetryChannel`nastavení pro :

1. `MaxTransmissionBufferCapacity`: Maximální velikost paměti v bajtů, který kanál používá k ukládání přenosů do vyrovnávací paměti v paměti. Po dosažení této kapacity jsou nové položky uloženy přímo na místním disku. Výchozí hodnota je 5 MB. Nastavení vyšší hodnoty vede k menšímu využití disku, ale nezapomeňte, že položky v paměti budou ztraceny, pokud dojde k chybě aplikace.

1. `MaxTransmissionSenderCapacity`: Maximální počet `Transmission` instancí, které budou odeslány do Application Insights ve stejnou dobu. Výchozí hodnota je 10. Toto nastavení lze nakonfigurovat na vyšší číslo, které se doporučuje při generování obrovského objemu telemetrie. K vysokému objemu obvykle dochází během zátěžového testování nebo při vypnutém vzorkování.

1. `StorageFolder`: Složka používaná kanálem k ukládání položek na disk podle potřeby. V systému Windows se používá %LOCALAPPDATA% nebo %TEMP%, pokud není explicitně zadána žádná jiná cesta. V jiných prostředích než windows, musíte zadat platné umístění nebo telemetrie nebudou uloženy na místní disk.

## <a name="which-channel-should-i-use"></a>Který kanál mám použít?

`ServerTelemetryChannel`je doporučeno pro většinu produkčních scénářů zahrnujících dlouhotrvající aplikace. Metoda `Flush()` implementovaná `ServerTelemetryChannel` společností není synchronní a také nezaručuje odesílání všech čekajících položek z paměti nebo disku. Pokud používáte tento kanál v situacích, kde se aplikace chystá vypnout, doporučujeme zavést některé zpoždění po volání `Flush()`. Přesné množství zpoždění, které můžete vyžadovat, není předvídatelné. Závisí na faktorech, jako `Transmission` je počet položek nebo instancí v paměti, kolik je na disku, kolik jsou přenášeny do back-endu a zda je kanál uprostřed exponenciálních scénářů back-off.

Pokud potřebujete provést synchronní vyprázdnění, `InMemoryChannel`doporučujeme použít .

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>Zaručuje kanál Application Insights doručování telemetrie? Pokud ne, jaké jsou scénáře, ve kterých může dojít ke ztrátě telemetrie?

Stručná odpověď je, že žádný z vestavěných kanálů nabízí záruku transakčního typu doručení telemetrie do back-endu. `ServerTelemetryChannel`je pokročilejší ve srovnání `InMemoryChannel` s pro spolehlivé doručení, ale také provede pouze nejlepší pokus o odeslání telemetrie. Telemetrie může být stále ztracena v několika situacích, včetně těchto běžných scénářů:

1. Položky v paměti jsou ztraceny při chybě aplikace.

1. Telemetrie je ztracena během delších období problémů se sítí. Telemetrie je uložena na místní disk během výpadků sítě nebo při problémech s back-endapplication insights. Položky starší než 48 hodin jsou však zahozeny.

1. Výchozí umístění disků pro ukládání telemetrie v systému Windows jsou %LOCALAPPDATA% nebo %TEMP%. Tato umístění jsou obvykle místní k počítači. Pokud aplikace migruje fyzicky z jednoho umístění do druhého, dojde ke ztrátě jakékoli telemetrie uložené v původním umístění.

1. Ve webových aplikacích v systému Windows je výchozím umístěním úložiště disku D:\local\LocalAppData. Toto umístění není trvalé. Je vymazána v restartování aplikace, horizontální navýšení kapacity a další takové operace, což vede ke ztrátě jakékoli telemetrie uložené tam. Můžete přepsat výchozí a určit úložiště do trvalého umístění, jako je D:\home. Tato trvalá umístění jsou však obsluhována vzdáleným úložištěm, a proto mohou být pomalá.

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>Funguje ServerTelemetryChannel v jiných systémech než v systému Windows?

Přestože název balíčku a oboru názvů obsahuje "WindowsServer", tento kanál je podporován v jiných systémech než Windows, s následující výjimkou. V jiných systémech než windows kanál ve výchozím nastavení nevytváří složku místního úložiště. Musíte vytvořit složku místního úložiště a nakonfigurovat kanál tak, aby ji používal. Po konfiguraci místního úložiště kanál funguje stejným způsobem ve všech systémech.

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>Vytvoří sada SDK dočasné místní úložiště? Jsou data šifrována v úložišti?

Sada SDK ukládá položky telemetrie v místním úložišti během problémů se sítí nebo během omezení. Tato data nejsou zašifrována místně.

V systémech Windows sada SDK automaticky vytvoří dočasnou místní složku v adresáři %TEMP% nebo %LOCALAPPDATA% a omezí přístup pouze správcům a aktuálnímu uživateli.

Pro jiné systémy než Windows není sada SDK automaticky vytvořena žádná místní úložiště, takže žádná data nejsou ve výchozím nastavení uložena místně. Adresář úložiště můžete vytvořit sami a nakonfigurovat kanál tak, aby jej používal. V takovém případě jste zodpovědní za zajištění, že adresář je zabezpečen.
Přečtěte si další informace o [ochraně osobních údajů a ochraně osobních údajů](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage).

## <a name="open-source-sdk"></a>Sada SDK s otevřeným zdrojovým kódem
Stejně jako každá sada SDK pro application insights jsou kanály open source. Přečtěte si kód a přispějte k něm nebo nahlaste problémy na [oficiálním úložišti GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet).

## <a name="next-steps"></a>Další kroky

* [Vzorkování](../../azure-monitor/app/sampling.md)
* [Poradce při potížích se sadou SDK](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
