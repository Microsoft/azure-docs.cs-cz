---
title: Telemetrie kanály ve službě Azure Application Insights | Dokumentace Microsoftu
description: Jak přizpůsobit kanály telemetrická data sady SDK služby Azure Application Insights pro .NET a .NET Core.
services: application-insights
documentationcenter: .net
author: cijothomas
manager: carmonm
ms.assetid: 015ab744-d514-42c0-8553-8410eef00368
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/14/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: af00641123354831c7bf174a743ded2886343579
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561344"
---
# <a name="telemetry-channels-in-application-insights"></a>Telemetrie kanály ve službě Application Insights

Kanály telemetrická data jsou nedílnou součástí toho, [sady SDK služby Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Spravují ukládání do vyrovnávací paměti a přenos telemetrických dat do služby Application Insights. Verze sady SDK pro .NET a .NET Core mají dva kanály integrované telemetrie: `InMemoryChannel` a `ServerTelemetryChannel`. Tento článek popisuje každý kanál podrobně, jak přizpůsobit chování kanálu.

## <a name="what-are-telemetry-channels"></a>Co jsou kanály telemetrie?

Telemetrie kanály zodpovídají za ukládání do vyrovnávací paměti položky telemetrie a jejich odeslání do služby Application Insights, kde jsou uloženy pro dotazování a analýzy. Kanál telemetrie je všechny třídy, která implementuje [ `Microsoft.ApplicationInsights.ITelemetryChannel` ](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet) rozhraní.

`Send(ITelemetry item)` Metoda kanálu telemetrie se nazývá po všech inicializátory telemetrická data a telemetrii procesory jsou. Tedy všechny položky vyřadit procesorem telemetrie nedostanou kanálu. `Send()` není obvykle položky do back-endu okamžité odesílání. Obvykle ukládá je do vyrovnávací paměti v paměti a odešle je do dávek pro efektivní přenos.

[Live Metrics Stream](live-stream.md) má také vlastní kanál, který využívá živé streamování telemetrická data. Tento kanál je nezávisle na kanál regulární telemetrie a tohoto dokumentu se nevztahuje na ni.

## <a name="built-in-telemetry-channels"></a>Kanály integrované telemetrie

Application Insights .NET a .NET Core SDK dodávají spolu s dvěma integrované kanály:

* `InMemoryChannel`: Odlehčený kanál, který vyrovnávacích pamětí položek v paměti, dokud se nepošle. Položky jsou uložena do vyrovnávací paměti a vyprázdní každých 30 sekund, nebo vždy, když se neruší 500 položek. Tento kanál nabízí spolehlivost minimální záruky vzhledem k tomu, že ji nebude opakovat odesílání telemetrických dat po selhání. Tento kanál také nebude zachovat položky na disku, tak, aby byly všechny položky unsent trvale ztraceny ukončení aplikace (řádné nebo ne). Tento kanál implementuje `Flush()` metodu, která slouží k vynucení – vyprázdnění všech položek v paměti telemetrie synchronně. Tento kanál se výborně hodí pro krátce běžící aplikace, kde je synchronní vyprázdnění ideální.

    Tento kanál je součástí větší balíček Microsoft.ApplicationInsights NuGet a je výchozím kanálu, které SDK využívá při else není nic nakonfigurované.

* `ServerTelemetryChannel`: Pokročilejší kanál, který má zásady opakování a možnost ukládat data na místním disku. Tento kanál pokusí znovu odesílání telemetrických dat, pokud dojde k přechodným chybám. Tento kanál také využívá úložiště místního disku pro během výpadků sítě nebo vysokou telemetrie svazky na disku uchovávat položky. Protože tyto mechanismy opakování a úložiště na místním disku v tomto kanálu se považuje za spolehlivější a se doporučuje pro všechna produkční scénáře. Tento kanál je výchozí nastavení pro [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) a [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) aplikace, které jsou nakonfigurované podle oficiální dokumentace. Tento kanál je optimalizovaný pro server scénáře se dlouho běžící procesy. [ `Flush()` ](#which-channel-should-i-use) Se synchronní metoda, která je implementována pomocí tohoto kanálu.

    Tento kanál je dodáván jako balíček Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel NuGet a je vytvořit automaticky při použití Microsoft.ApplicationInsights.Web nebo Microsoft.ApplicationInsights.AspNetCore NuGet balíček.

## <a name="configure-a-telemetry-channel"></a>Konfigurace telemetrie kanálu

Nakonfigurujte kanál telemetrie nastavením v konfiguraci aktivní telemetrii. Pro aplikace ASP.NET, konfigurace zahrnuje nastavení telemetrie instanci channel `TelemetryConfiguration.Active`, nebo tak, že upravíte `ApplicationInsights.config`. Pro aplikace ASP.NET Core konfigurace zahrnuje přidání kanál do kontejneru pro vkládání závislostí.

Následující části vysvětlují příkladem konfigurace `StorageFolder` nastavení pro kanálu v různé typy aplikací. `StorageFolder` je to jedna z konfigurovatelné nastavení. Úplný seznam nastavení konfigurace, najdete v části [v části Nastavení](telemetry-channels.md#configurable-settings-in-channels) dále v tomto článku.

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>Konfigurace pomocí souboru ApplicationInsights.config pro aplikace ASP.NET

Následující část [soubor ApplicationInsights.config](configuration-with-applicationinsights-config.md) ukazuje `ServerTelemetryChannel` kanál nakonfigurovanou `StorageFolder` nastavit vlastní umístění:

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

Následující kód nastaví instanci na "ServerTelemetryChannel" `StorageFolder` nastavit do vlastního umístění. Přidejte tento kód na začátek aplikace, obvykle v `Application_Start()` metody v souboru Global.aspx.cs.

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

Upravit `ConfigureServices` metodu `Startup.cs` třídy, jak je znázorněno zde:

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
> Konfigurace kanálu s využitím `TelemetryConfiguration.Active` nedoporučuje se používat pro aplikace ASP.NET Core.

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>Konfigurace v kódu pro.NET/.NET základní konzolové aplikace

Pro konzolové aplikace kód je stejný pro .NET a .NET Core:

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>Provozní údaje ServerTelemetryChannel

`ServerTelemetryChannel` úložiště přicházejících položky v vyrovnávací paměť v paměti. Serializován, komprimované a uložena do položky `Transmission` instance každých 30 sekund, nebo když 500 položek mají byla uložená do vyrovnávací paměti. Jediný `Transmission` instance obsahuje až 500 položek a představuje dávku telemetrických dat, která odešle přes jedním voláním HTTPS ve službě Application Insights.

Ve výchozím nastavení, maximálně 10 `Transmission` instancí lze odesílat souběžně. Pokud je telemetrických dat přicházejících rychlejší sazby nebo pokud síť nebo Application Insights zpět end je pomalá, `Transmission` instancí jsou uloženy v paměti. Výchozí kapacita tento v paměti `Transmission` vyrovnávací paměť je 5 MB. Pokud došlo k překročení kapacity v paměti, `Transmission` instancí jsou uložené na místním disku, až limitu 50 MB. `Transmission` instance jsou uložené na místním disku i po problémy se sítí. Pouze ty položky, které jsou uložené na místním disku překonání při selhání aplikace. Odeslali jste pokaždé, když se aplikace znovu spustí.

## <a name="configurable-settings-in-channels"></a>Konfigurovat nastavení v kanálech

Úplný seznam konfigurovatelných nastavení pro každý kanál naleznete v tématu:

* [InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

Tady je nejčastěji používaná nastavení pro `ServerTelemetryChannel`:

1. `MaxTransmissionBufferCapacity`: Maximální množství paměti v bajtech, používaných kanálem do vyrovnávací paměti přenosů v paměti. Když je dosaženo této kapacitě, nové položky se ukládají přímo na místní disk. Výchozí hodnota je 5 MB. Nastavení vyšší hodnoty vede k méně využití disku, ale mějte na paměti, že položky v paměti budou ztraceny, pokud dojde k chybě aplikace.

1. `MaxTransmissionSenderCapacity`: Maximální počet `Transmission` instancí, které se odesílají do Application Insights ve stejnou dobu. Výchozí hodnota je 10. Toto nastavení můžete nakonfigurovat vyšší číslo, které se doporučuje, když se vygeneruje velký objem telemetrických dat. Velký objem obvykle dochází při zátěžovém testování nebo při vzorkování je vypnutý.

1. `StorageFolder`: Složka, která používá kanál pro uložení položky, které chcete na disku podle potřeby. Ve Windows % TEMP % nebo % LOCALAPPDATA % je použit, pokud žádná cesta je explicitně zadán. V prostředí než Windows musíte zadat že platné umístění nebo telemetrie se nebudou ukládat na místní disk.

## <a name="which-channel-should-i-use"></a>Použití se kanálu

`ServerTelemetryChannel` se doporučuje pro většinu produkčních scénářích zahrnující dlouho běžící aplikace. `Flush()` Implementované metody `ServerTelemetryChannel` není synchronní, a také není zárukou toho odesílá všechny nevyřízené položky z paměti a disku. Pokud použijete tento kanál ve scénářích se aplikaci Chystáte se vypnout, doporučujeme zavést určitá prodleva po volání `Flush()`. Přesné množství zpoždění, které můžete potřebovat není předvídatelný. To závisí na faktorech, jako je počet kusů nebo `Transmission` instance jsou v paměti, kolik je na disku, kolik se přenesená do back-endu a určuje, zda kanál je uprostřed exponenciální regresní scénáře.

Pokud je potřeba provést synchronní vyprázdnění, doporučujeme použít `InMemoryChannel`.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>Kanál Application Insights zaručit doručování telemetrie? Pokud ne, jaké jsou scénáře, ve kterých může dojít ke ztrátě telemetrie?

Stručná odpověď je, že žádná z předdefinovaných kanály nabízejí doručování telemetrická data do back-endu se zárukou typ transakce. `ServerTelemetryChannel` je další pokročilé ve srovnání s `InMemoryChannel` pro spolehlivé doručování, ale také znamená pouze best effort pokus o odeslání telemetrie. Telemetrie může stále dojít ke ztrátě v několika situacích, včetně těchto běžných scénářů:

1. Položky v paměti jsou ztraceny, pokud dojde k chybě aplikace.

1. Telemetrická data nejsou ztracena během delší dobu problémy se sítí. Telemetrická data se ukládají na místní disk během výpadků sítě nebo pokud dojde k problémům s back-endu služby Application Insights. Nicméně položky starší než 24 hodin se zahodí.

1. Výchozí umístění disku pro ukládání telemetrických dat ve Windows se % LOCALAPPDATA % nebo % TEMP %. Tato místa jsou obvykle místní počítač. Pokud aplikace migruje z jednoho umístění do druhého fyzicky, dojde ke ztrátě žádnou telemetrii, uložená v původním umístění.

1. Ve službě Web Apps ve Windows je výchozí umístění úložiště disku D:\local\LocalAppData. Toto umístění není trvalý. To je dojde k vymazání v restartování aplikace, škálování nasazení a další takové operace, což vede ke ztrátě žádnou telemetrii, uložených. Můžete přepsat výchozí nastavení a zadat úložiště pro trvalé umístění, jako je D:\home. Ale takové trvalého umístění obsluhuje vzdálené úložiště a tak může být pomalé.

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>Funguje na systémech než Windows ServerTelemetryChannel?

I když název balíčku a obor názvů obsahuje "WindowsServer", v tomto kanálu je podporovaná v systémech než Windows, s následující výjimkou. V systémech než Windows kanál nevytváří složky místní úložiště ve výchozím nastavení. Musíte vytvořit složku místní úložiště a nakonfigurovat kanál pro použití. Po dokončení konfigurace místní úložiště, kanál funguje stejně jako ve všech systémech.

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>SDK vytvořit místní dočasné úložiště? Má tato data šifrovat úložiště?

Sada SDK ukládá položky telemetrie v místním úložišti během problémy se sítí nebo omezení šířky pásma. Tato data nejsou šifrována místně.

Pro systémy Windows sady SDK automaticky vytvoří dočasné místní složky % TEMP % nebo % LOCALAPPDATA % adresář a omezuje přístup ke Správci a pouze pro aktuálního uživatele.

Pro systémy než Windows žádné místní úložiště se automaticky vytvoří sada SDK a proto se žádná data neukládají místně ve výchozím nastavení. Můžete vytvořit adresář úložiště sami a nakonfigurovat kanál pro použití. V tomto případě jste za zajištění, že je zabezpečený adresář.
Další informace o [ochranu dat a ochrany osobních údajů](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage).

## <a name="open-source-sdk"></a>Open source sad SDK
Stejně jako všechny sady SDK pro Application Insights kanály jsou open source. Číst a přidávat do kódu nebo nahlásit problémy v [oficiální úložiště GitHub se vzorovými](https://github.com/Microsoft/ApplicationInsights-dotnet).

## <a name="next-steps"></a>Další postup

* [Vzorkování](../../azure-monitor/app/sampling.md)
* [Řešení potíží s SDK](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
