---
title: Telemetrie kanály ve službě Azure Application Insights | Dokumentace Microsoftu
description: Jak přizpůsobit kanály telemetrická data sady SDK služby Azure Application Insights pro.NET/.NET Core.
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
ms.openlocfilehash: fa99e29e9ec6c2bef7296a50dd381ee5fc60a1cb
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255812"
---
# <a name="telemetrychannel-in-application-insights"></a>TelemetryChannel ve službě Application Insights

TelemetryChannel je nedílnou součástí [sady SDK služby Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Spravuje ukládání do vyrovnávací paměti a přenos telemetrických dat do služby Application Insights. Verze sady SDK pro .NET a .NET Core mají dvě předdefinované TelemetryChannels - `InMemoryChannel` a `ServerTelemetryChannel`. Tento článek popisuje každý kanál podrobně, včetně toho, jak mohou uživatelé přizpůsobit chování kanálu.

## <a name="what-is-a-telemetrychannel"></a>Co je TelemetryChannel?

`TelemetryChannel` je zodpovědná za ukládání do vyrovnávací paměti a odesílání položky telemetrie do služby Application Insights, kde jsou uložená pro dotazování a analýzy. Se jakékoli třídy implementace rozhraní [`Microsoft.ApplicationInsights.ITelemetryChannel`](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet)

`Send(ITelemetry item)` Po volání metody TelemetryChannel `TelemetryInitializer`s a `TelemetryProcessor`s jsou volány. To znamená, že všechny položky zrušených správcem `TelemetryProcessor` nedostanou kanálu. `Send()` neodesílá obvykle položky okamžitě back-endu. Jsou obvykle ve vyrovnávací paměti v paměti a odesílají v dávkách, efektivní přenosu.

[LiveMetrics](live-stream.md) má také vlastní kanál, kterým zajišťuje živého streamování telemetrická data. Tento kanál je nezávislý na kanál regulární telemetrie a tohoto dokumentu se nevztahují na kanál používá `LiveMetrics`.

## <a name="built-in-telemetrychannels"></a>Integrované TelemetryChannels

Sadu SDK Application Insights.NET/.NET Core se dodává s integrovanou dva kanály:

* **InMemoryChannel** 
 `InMemoryChannel` je odlehčené kanál, který vyrovnávacích pamětí položek v paměti, dokud se nepošle. Položky jsou uložena do vyrovnávací paměti a vyprázdní každých 30 sekund nebo pokaždé, když máte 500 položek do vyrovnávací paměti. Tento kanál nabízí záruky minimální spolehlivosti se nebude opakovat odesílání telemetrických dat při selhání. Tento kanál nepodporuje zachovat položky na disku, tak, aby žádné neodeslané položky byly trvale ztraceny ukončení aplikace (bez výpadku nebo ne). Je `Flush()` metoda implementovaná pomocí tohoto kanálu, který slouží k vynucení – vyprázdnění všech položek v paměti telemetrie synchronně. Jedná se skvěle hodí pro krátce běžící aplikace, kde je synchronní vyprázdnění ideální.

    Tento kanál je dodáván jako součást `Microsoft.ApplicationInsights` samotný balíček nuget a je výchozím kanálu, které SDK využívá při else není nic nakonfigurované.

* **ServerTelemetryChannel** 
 `ServerTelemetryChannel` pokročilejší kanál, který má zásady opakování a možnost ukládat data na místním disku. Tento kanál zopakuje pokus o odesílání telemetrických dat, pokud dojde k přechodným chybám. Tento kanál také využívá úložiště místního disku pro během výpadků sítě nebo vysokou telemetrie svazky na disku uchovávat položky. Protože tyto mechanismy opakování a úložiště na místním disku tento kanál se považuje za spolehlivější a se doporučuje pro všechna produkční scénáře. Tento kanál je výchozí nastavení pro [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) a [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) aplikace, které jsou nakonfigurované podle propojené oficiální dokumentace. Tento kanál je optimalizovaný pro server scénáře dlouho běžící procesy. [ `Flush()` ](#which-channel-should-i-use) Není synchronní metoda implementovaná v tomto kanálu.

    Tento kanál je dodáván jako balíček NuGet `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel`a automaticky nepřipojí při použití některý z balíčků NuGet `Microsoft.ApplicationInsights.Web` nebo `Microsoft.ApplicationInsights.AspNetCore`.

## <a name="configuring-telemetrychannel"></a>Konfigurace TelemetryChannel

Telemetrie kanál se dá nakonfigurovat pomocí nastavení požadovaný `TelemetryChannel` na aktivním `TelemetryConfiguration`. Pro aplikace Asp.Net, konfigurace zahrnuje nastavení `TelemetryChannel` na `TelemetryConfiguration.Active`, nebo úpravě `ApplicationInsights.config`. Pro aplikace ASP.NET Core konfigurace zahrnuje přidání požadovaný kanál do kontejneru pro vkládání závislostí.

Následující znázorňuje příklad, ve kterém uživatel konfiguruje `StorageFolder` pro kanál. `StorageFolder` je to jedna z konfigurovatelné nastavení. Úplný seznam nastavení konfigurace je popsaná [v sekci nastavení](telemetry-channels.md#configurable-settings-in-channel).

## <a name="configuration-using-applicationinsightsconfig-for-aspnet-applications"></a>Konfigurace pomocí souboru ApplicationInsights.Config pro aplikace ASP.NET

Následující část [soubor ApplicationInsights.config](configuration-with-applicationinsights-config.md) ukazuje ServerTelemetryChannel nakonfigurovanou `StorageFolder` nastavit do vlastního umístění.

```xml
    <TelemetrySinks>
        <Add Name="default">
            <TelemetryProcessors>
                <!--TelemetryProcessors omitted for brevity  -->
            </TelemetryProcessors>
            <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel">
                <StorageFolder>d:\temp\applicationinsights</StorageFolder>
            </TelemetryChannel>
        </Add>
    </TelemetrySinks>
```

## <a name="configuration-in-code-for-aspnet-applications"></a>Konfigurace v kódu pro aplikace ASP.NET

Následující kód nastaví ServerTelemetryChannel s `StorageFolder` nastavit do vlastního umístění. Tento kód by měl být na začátek přidána číslice aplikace, obvykle v metoda Application_Start() v `Global.aspx.cs`

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

## <a name="configuration-in-code-for-aspnet-core-applications"></a>Konfigurace v kódu pro aplikace ASP.NET Core

Upravit `ConfigureServices` metoda `Startup.cs` třídy, jak je znázorněno níže.

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

> [!NOTE]
> Je důležité si uvědomit, že konfigurace kanálu pomocí `TelemetryConfiguration.Active` nedoporučuje se používat pro aplikace ASP.NET Core.

## <a name="configuring-telemetrychannel-in-code-for-netnet-core-console-applications"></a>Konfigurace TelemetryChannel v kódu pro.NET/.NET základní konzolové aplikace

Pro konzolové aplikace kód je stejný pro .NET a .NET Core a je uveden níže.

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>Provozní údaje ServerTelemetryChannel

`ServerTelemetryChannel` Vyrovnávacích pamětí opravovány položky v vyrovnávací paměť v paměti. To je serializován, zkomprimovat a uložit do `Transmission` instance po každých 30 sekund, nebo když 500 položek jsou do vyrovnávací paměti. Jediný `Transmission` instance obsahuje až 500 položek a představuje dávku telemetrie odesílané přes jeden https volání služby Application Insights. Ve výchozím nastavení, může být maximálně 10 `Transmission`s odesílané paralelně. Pokud je telemetrických dat přicházejících rychlejší sazby nebo back-endové síti/Application Insights je pomalé, potom `Transmission`s získat ukládají do paměti. Výchozí kapacitu této vyrovnávací paměti přenosu v paměti je 5 MB. Jakmile se překročí kapacitu v paměti, `Transmission`s jsou uložené na místním disku pro až 50 MB. `Transmission`s jsou uložené na místním disku, při problémech se sítí také. Pouze položky, které jsou uložené na místním disku překonání při selhání aplikace, které jsou odesílány vždy, když je aplikace spuštěna znovu.

## <a name="configurable-settings-in-channel"></a>Konfigurovat nastavení v kanálu

Úplný seznam konfigurovatelných nastavení pro každý kanál jsou tady:

[InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

[ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

Nejčastěji používaná nastavení pro `ServerTelemetryChannel` jsou uvedeny níže:

1. `MaxTransmissionBufferCapacity` -Maximální množství paměti v bajtech, používaných kanálem do vyrovnávací paměti přenosů v paměti. Po dosažení této kapacitě nové položky se uloží přímo na místní disk. Výchozí hodnota je 5 MB. Nastavení na vyšší hodnotu potenciálních zákazníků na nižší využití disku, ale je důležité si uvědomit, že položky v paměti budou ztraceny, pokud dojde k chybě aplikace.

2. `MaxTransmissionSenderCapacity` -Maximální množství `Transmission`, které se odesílají do Application Insights ve stejnou dobu. Výchozí hodnota je 10, ale můžete ji nakonfigurovat vyšší číslo. To se doporučuje, když se vygeneruje velký objem telemetrických dat, obvykle při provádění zátěžové testování a/nebo když je vypnutý vzorkování.

3. `StorageFolder` – Složka používá kanál pro uložení položky, které chcete na disku podle potřeby. Ve Windows % LocalAppData % nebo % Temp % se používá, pokud není nic nakonfigurované explicitně. V prostředích Windows bez uživatele **musí** konfigurace platné umístění, bez kterých se nebudou ukládat telemetrie na místní disk.

## <a name="which-channel-should-i-use"></a>Použití se kanálu

`ServerTelemetryChannel` se doporučuje pro většinu produkčních scénářů dlouhodobě spuštěných aplikací. `Flush()` Implementaci metod `ServerTelemetryChannel` není synchronní, a `Flush()` nezaručuje odesílání všechny nevyřízené položky z paměti a disku. Pokud se tento kanál používá ve scénářích, kde aplikace je vypnout, pak doporučujeme provést po volání některých zpoždění `Flush()` na tomto kanálu. Přesné množství zpoždění požadované není předvídatelné, protože závisí na faktorech, jako je počet položek nebo `Transmissions` v paměti, kolik se v disku, kolik se přenesená do využívající, a pokud je kanál uprostřed exponenciální regresní scénáře. Pokud je potřeba provést synchronní vyprázdnění, pak `InMemoryChannel` se doporučuje.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="does-applicationinsights-channel-offer-guaranteed-telemetry-delivery-or-what-are-the-scenarios-where-telemetry-can-be-lost"></a>*Nabízí ApplicationInsights kanál telemetrie zaručené doručení nebo jaké jsou scénáře, kde může dojít ke ztrátě telemetrie?*

* Stručná odpověď je, že žádná z předdefinovaných kanály nabízejí transakce typ záruky ohledně toho, doručování telemetrická data do back-endu. Zatímco `ServerTelemetryChannel` je další pokročilé ve srovnání s `InMemoryChannel` telemetrie spolehlivé doručování, také udržuje best effort pokus odesílání telemetrických dat a telemetrických dat můžete stále ztraceno v několika situacích. Mezi běžné scénáře, kdy dojde ke ztrátě telemetrická data patří:

1. Položky v paměti se ztratí pokaždé, když dojde k chybě aplikace.
1. Telemetrická data se uloží na místní disk během výpadků sítě nebo problémy s back-endu služby Application Insights. Nicméně položky starší než 24 hodin se zahodí. Telemetrie je ztraceny během delší dobu problémy se sítí.
1. Výchozí umístění disku pro ukládání telemetrických dat ve Windows se % LocalAppData % nebo % Temp %. Tato místa jsou obvykle místní počítač. Pokud aplikace migruje z jednoho umístění do druhého fyzicky, dojde ke ztrátě žádnou telemetrii, uložené v tomto umístění.
1. Ve službě Azure Web Apps (Windows) je výchozí umístění disku "D:\local\LocalAppData". Toto umístění není trvalý a dojde k vymazání v aplikaci se restartuje, škálování výpisů a tak dále, což vede ke ztrátě telemetrická data uložená v těchto umístěních. Můžou uživatelé potlačit úložiště pro trvalé umístění, jako je "D:\home", ale tato trvalého umístění pod obsluhovat vzdálené úložiště a může být pomalé.

### <a name="does-servertelemetrychannel-work-in-non-windows-systems"></a>*Funguje ServerTelemetryChannel v systémech než Windows?*

* Bez ohledu na název v balíčku nebo obor názvů se WindowsServer tohoto kanálu je podporována v systémech než Windows a vyvolala následující výjimku. V jiných – Windows kanál nevytváří složky místní úložiště ve výchozím nastavení. Uživatelé musí vytvořit složku místní úložiště a nakonfigurovat kanál pro použití. Po nakonfigurování místní úložiště kanál stejné funguje v systémech než Windows a Windows.

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>*SDK vytvořit místní dočasné úložiště? Má tato data šifrovat úložiště?*

* Sada SDK ukládá položky telemetrie v místním úložišti během problémy se sítí nebo omezení šířky pásma. Tato data nejsou šifrována místně.
Pro systémy Windows sady SDK automaticky vytvoří dočasné místní složky v adresáři TEMP nebo data aplikací a omezuje přístup ke Správci a pouze pro aktuálního uživatele.
Pro Non-Windows žádné místní úložiště se automaticky vytvoří sada SDK, a proto se žádná data neukládají místně ve výchozím nastavení. Uživatelé mohou vytvářet adresář úložiště, sami a nakonfigurovat kanál pro použití. Uživatel v tomto případě zodpovídá za to, že tento adresář je zabezpečená.
Další informace o [ochranu dat a ochrany osobních údajů](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage).

## <a name="open-source-sdk"></a>Open source sad SDK
Stejně jako každý sady Application Insights SDK kanály jsou také open source. Čtení a přispívání ke kódu nebo hlášení problémů v [oficiální úložiště GitHub se vzorovými](https://github.com/Microsoft/ApplicationInsights-dotnet).

## <a name="next-steps"></a>Další kroky

* [Vzorkování](../../azure-monitor/app/sampling.md)
* [Řešení potíží s SDK](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
