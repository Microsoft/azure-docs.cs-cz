---
title: Kanály telemetrie v Azure Application Insights | Microsoft Docs
description: Postup přizpůsobení kanálů telemetrie v Azure Application Insights SDK pro .NET a .NET Core
ms.topic: conceptual
ms.date: 05/14/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 9c292246f947e4d3a364f79b31fe7a1deebd33d9
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78368290"
---
# <a name="telemetry-channels-in-application-insights"></a>Kanály telemetrie v Application Insights

Kanály telemetrie jsou nedílnou součástí [sad Azure Application Insights SDK](../../azure-monitor/app/app-insights-overview.md). Spravují ukládání do vyrovnávací paměti a přenos telemetrie do služby Application Insights. Verze sady SDK pro .NET a .NET Core mají dva integrované kanály telemetrie: `InMemoryChannel` a `ServerTelemetryChannel`. Tento článek podrobně popisuje jednotlivé kanály, včetně postupu přizpůsobení chování kanálu.

## <a name="what-are-telemetry-channels"></a>Co jsou kanály telemetrie?

Kanály telemetrie jsou zodpovědné za ukládání položek telemetrie do vyrovnávací paměti a jejich posílání do služby Application Insights, kde jsou uložené pro dotazování a analýzu. Kanál telemetrie je libovolná třída, která implementuje rozhraní [`Microsoft.ApplicationInsights.ITelemetryChannel`](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet) .

Metoda `Send(ITelemetry item)` kanálu telemetrie se volá po volání všech inicializátorů telemetrie a procesorů telemetrie. Všechny položky vynechané procesorem telemetrie tak nedosáhnou kanálu. `Send()` obvykle odesílá položky do back-endu okamžitě. Obvykle je ukládá do vyrovnávací paměti a odesílá je v dávkách za účelem efektivního přenosu.

[Live Metrics Stream](live-stream.md) má také vlastní kanál, který využívá živé streamování telemetrie. Tento kanál je nezávislý na běžném kanálu telemetrie a tento dokument se na něj nevztahuje.

## <a name="built-in-telemetry-channels"></a>Integrované kanály telemetrie

Sady SDK Application Insights .NET a .NET Core jsou dodávány se dvěma integrovanými kanály:

* `InMemoryChannel`: odlehčený kanál, který ukládá položky do vyrovnávací paměti až do jejich odeslání. Položky jsou uloženy do vyrovnávací paměti a vyprázdněny každých 30 sekund nebo vždy, když je 500 položek uloženo do vyrovnávací paměti. Tento kanál nabízí minimální záruky pro spolehlivost, protože se po selhání neopakuje odeslání telemetrie. Tento kanál také neuchovává položky na disku, takže všechny neodeslané položky jsou trvale ztraceny při vypnutí aplikace (korektně nebo ne). Tento kanál implementuje metodu `Flush()`, která se dá použít k vynucení vyprázdnit jakékoli položky telemetrie v paměti synchronně. Tento kanál je vhodný pro krátkodobé aplikace, u kterých je vhodné synchronní vyprázdnění.

    Tento kanál je součástí většího balíčku NuGet Microsoft. ApplicationInsights a je výchozím kanálem, který sada SDK používá, když nic jiného není nakonfigurované.

* `ServerTelemetryChannel`: pokročilejší kanál, který má zásady opakování a možnost ukládat data na místní disk. Tento kanál pokusy o odeslání telemetrie, pokud dojde k přechodným chybám. Tento kanál taky využívá úložiště na místním disku k udržení položek na disku během výpadků sítě nebo svazků s vysokými telemetriemi. Z důvodu těchto mechanismů opakování a místního diskového úložiště se tento kanál považuje za spolehlivější a doporučuje se pro všechny produkční scénáře. Tento kanál je výchozím nastavením pro [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) a [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) aplikace, které jsou nakonfigurované podle oficiální dokumentace. Tento kanál je optimalizovaný pro scénáře serveru s dlouhotrvajícími procesy. Metoda [`Flush()`](#which-channel-should-i-use) , která je implementovaná tímto kanálem, není synchronní.

    Tento kanál se dodává jako balíček NuGet Microsoft. ApplicationInsights. WindowsServer. TelemetryChannel a automaticky se získá, když použijete Microsoft. ApplicationInsights. Web nebo Microsoft. ApplicationInsights. AspNetCore NuGet. balíček.

## <a name="configure-a-telemetry-channel"></a>Konfigurace kanálu telemetrie

Kanál telemetrie nakonfigurujete nastavením na aktivní konfiguraci telemetrie. V případě aplikací ASP.NET zahrnuje konfigurace nastavení instance kanálu telemetrie na `TelemetryConfiguration.Active`nebo úpravou `ApplicationInsights.config`. Pro ASP.NET Core aplikace konfigurace zahrnuje přidání kanálu do kontejneru vkládání závislostí.

V následujících částech jsou uvedeny příklady konfigurace nastavení `StorageFolder` pro kanál v různých typech aplikací. `StorageFolder` je pouze jedno z konfigurovatelných nastavení. Úplný seznam nastavení konfigurace najdete v [části nastavení](telemetry-channels.md#configurable-settings-in-channels) dále v tomto článku.

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>Konfigurace pomocí ApplicationInsights. config pro aplikace ASP.NET

V následující části [souboru ApplicationInsights. config](configuration-with-applicationinsights-config.md) se zobrazuje kanál `ServerTelemetryChannel` nakonfigurovaný s `StorageFolder` nastaveným na vlastní umístění:

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

### <a name="configuration-in-code-for-aspnet-applications"></a>Konfigurace v kódu pro aplikace ASP.NET

Následující kód nastaví instanci ServerTelemetryChannel s `StorageFolder` nastavenou na vlastní umístění. Přidejte tento kód na začátek aplikace, obvykle v `Application_Start()` metoda v Global.aspx.cs.

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

### <a name="configuration-in-code-for-aspnet-core-applications"></a>Konfigurace v kódu pro aplikace ASP.NET Core

Upravte metodu `ConfigureServices` třídy `Startup.cs`, jak je znázorněno zde:

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
> Konfigurace kanálu pomocí `TelemetryConfiguration.Active` se nedoporučuje pro ASP.NET Core aplikace.

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>Konfigurace v kódu pro konzolové aplikace .NET/.NET Core

U konzolových aplikací je kód stejný pro rozhraní .NET i .NET Core:

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>Provozní podrobnosti o ServerTelemetryChannel

`ServerTelemetryChannel` ukládá přicházející položky do vyrovnávací paměti. Položky jsou serializovány, komprimovány a uloženy do instance `Transmission`, a to každých 30 sekund nebo v případě, že byly 500 položky uloženy do vyrovnávací paměti. Jedna instance `Transmission` obsahuje až 500 položek a představuje dávku telemetrie, která se odesílá přes jedno volání HTTPS do služby Application Insights.

Ve výchozím nastavení lze současně odeslat maximálně 10 `Transmission` instancí. Pokud se telemetrie dorazí za rychlejší, nebo pokud je síť nebo Application Insights back-end pomalé, jsou `Transmission` instance uloženy v paměti. Výchozí kapacita této vyrovnávací paměti `Transmission` v paměti je 5 MB. Po překročení kapacity v paměti se `Transmission` instance ukládají na místní disk až do limitu 50 MB. instance `Transmission` se ukládají na místní disk, i když dojde k problémům se sítí. Pouze ty položky, které jsou uloženy na místním disku, budou mít při selhání aplikace. Odesílají se při každém spuštění aplikace.

## <a name="configurable-settings-in-channels"></a>Konfigurovatelná nastavení v kanálech

Úplný seznam konfigurovatelných nastavení pro každý kanál najdete v tématu:

* [InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

Tady jsou nejčastěji používaná nastavení pro `ServerTelemetryChannel`:

1. `MaxTransmissionBufferCapacity`: maximální množství paměti (v bajtech), které kanál používá pro přenos vyrovnávací paměti v paměti. Po dosažení této kapacity se nové položky ukládají přímo na místní disk. Výchozí hodnota je 5 MB. Nastavení vyšší hodnoty vede k menšímu využití disku, ale mějte na paměti, že pokud dojde k chybě aplikace, ztratí se položky v paměti.

1. `MaxTransmissionSenderCapacity`: maximální počet `Transmission` instancí, které se budou odesílat do Application Insights ve stejnou dobu. Výchozí hodnota je 10. Toto nastavení lze nakonfigurovat na vyšší číslo, což se doporučuje při vygenerování obrovských objemů telemetrie. K velkému objemu obvykle dochází během zátěžového testování nebo při vypnutí vzorkování.

1. `StorageFolder`: složka, kterou kanál používá k ukládání položek na disk podle potřeby. Ve Windows se používá buď% LOCALAPPDATA%, nebo% TEMP%, pokud není explicitně zadaná žádná cesta. V jiných prostředích než Windows je nutné zadat platné umístění nebo telemetrii nebudou uloženy na místní disk.

## <a name="which-channel-should-i-use"></a>Který kanál mám použít?

`ServerTelemetryChannel` se doporučuje pro většinu produkčních scénářů, které zahrnují dlouhotrvající aplikace. Metoda `Flush()` implementovaná pomocí `ServerTelemetryChannel` není synchronní a zároveň nezaručuje posílání všech položek, které čekají na vyřízení z paměti nebo disku. Pokud použijete tento kanál ve scénářích, kdy se má aplikace vypnout, doporučujeme, abyste po volání `Flush()`zavedli nějaké zpoždění. Přesné množství prodlevy, které možná budete potřebovat, není předvídatelné. Závisí na faktorech, jako je počet položek nebo `Transmission` instancí v paměti, kolik je na disku, kolik se přenáší do back-endu a zda je kanál uprostřed exponenciálních scénářů regrese.

Pokud potřebujete provést synchronní vyprázdnění, doporučujeme použít `InMemoryChannel`.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>Garantuje Application Insights kanál záruku doručení telemetrie? Pokud ne, jaké jsou scénáře, ve kterých může dojít ke ztrátě telemetrie?

Krátká odpověď znamená, že žádný z vestavěných kanálů nenabízí záruku doručení telemetrie do back-endu typu transakce. `ServerTelemetryChannel` je v porovnání s `InMemoryChannel`em spolehlivého doručování mnohem rozšířené, ale také se snaží o odeslání telemetrie jenom na nejvyšší úsilí. Telemetrii je stále možné ztratit v několika situacích, včetně těchto běžných scénářů:

1. Položky v paměti jsou ztraceny, když dojde k chybě aplikace.

1. Během delších období sítě dochází ke ztrátě telemetrie. Telemetrie se při výpadkech sítě uloží na místní disk nebo dojde k problémům s Application Insights back-endu. Položky starší než 48 hodin jsou však zahozeny.

1. Výchozí umístění disků pro ukládání telemetrie ve Windows jsou% LOCALAPPDATA% nebo% TEMP%. Tato umístění jsou obvykle místní pro počítač. Pokud se aplikace migruje fyzicky z jednoho umístění do druhého, dojde ke ztrátě všech telemetrie uložených v původním umístění.

1. V Web Apps ve Windows je výchozí umístění diskového úložiště D:\local\LocalAppData. Toto umístění není trvalé. Vymaže se při restartování aplikace, škálování a dalších takových operací, což vede ke ztrátě jakékoliv telemetrie, která je v něm uložená. Můžete přepsat výchozí nastavení a zadat úložiště do trvalého umístění, jako je D:\home. Tato trvalá umístění jsou ale obsluhováná vzdáleným úložištěm, takže může být pomalé.

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>Pracuje ServerTelemetryChannel v jiných systémech než Windows?

I když název jeho balíčku a oboru názvů obsahuje "WindowsServer", tento kanál je podporován v jiných systémech než Windows, a to s následující výjimkou. V jiných systémech než Windows kanál ve výchozím nastavení nevytvoří místní složku úložiště. Musíte vytvořit místní složku úložiště a nakonfigurovat ji tak, aby ji používala. Po nakonfigurování místního úložiště funguje kanál na všech systémech stejným způsobem.

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>Vytváří SDK dočasné místní úložiště? Jsou data zašifrovaná v úložišti?

Sada SDK ukládá položky telemetrie do místního úložiště během problémů se sítí nebo během omezování. Tato data nejsou šifrována místně.

V systémech Windows sada SDK automaticky vytvoří dočasné místní složky v adresáři% TEMP% nebo% LOCALAPPDATA% a omezí přístup jenom na správce a aktuálního uživatele.

V jiných systémech než Windows není sada SDK automaticky vytvořena ani místní úložiště, takže ve výchozím nastavení nejsou místně ukládána žádná data. Můžete vytvořit adresář úložiště sami a nakonfigurovat ho tak, aby ho používal. V takovém případě zodpovídáte za zajištění zabezpečení adresáře.
Přečtěte si další informace o [ochraně dat a ochraně osobních údajů](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage).

## <a name="open-source-sdk"></a>Open-Source sada SDK
Stejně jako u každé sady SDK pro Application Insights jsou kanály open source. Číst a přispívat k kódu nebo nahlásit problémy v [oficiálním úložišti GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet).

## <a name="next-steps"></a>Další kroky

* [Vzorkování](../../azure-monitor/app/sampling.md)
* [Řešení potíží s SDK](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
