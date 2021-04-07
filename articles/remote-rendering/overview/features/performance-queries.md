---
title: Dotazy na výkon na straně serveru
description: Jak provádět dotazy na výkon na straně serveru prostřednictvím volání rozhraní API
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 30b8104a9596f0b32f731c507b513b204f5d1acd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594091"
---
# <a name="server-side-performance-queries"></a>Dotazy na výkon na straně serveru

Dobrý výkon vykreslování na serveru je zásadní pro stabilní kmitočty snímků a dobré uživatelské prostředí. Je důležité monitorovat charakteristiky výkonu na serveru pečlivě a optimalizovat tam, kde je to potřeba. Údaje o výkonu se dají dotazovat prostřednictvím vyhrazených funkcí rozhraní API.

Největší dopad na výkon vykreslování je vstupní data modelu. Vstupní data můžete upravit, jak je popsáno v tématu [konfigurace převodu modelu](../../how-tos/conversion/configure-model-conversion.md).

Výkon aplikace na straně klienta může být také kritickým bodem. Pro důkladnou analýzu výkonu na straně klienta doporučujeme provést [:::no-loc text="performance trace":::](../../how-tos/performance-tracing.md) .

## <a name="clientserver-timeline"></a>Časová osa klienta/serveru

Než přejdete k podrobnostem o různých hodnotách latence, je vhodné se podívat na body synchronizace mezi klientem a serverem na časové ose:

![Časová osa kanálu](./media/server-client-timeline.png)

Obrázek ukazuje, jak:

* od klienta je *odhadnuta hodnota odhadu pozice* 60-Hz snímková frekvence (každých 16,6 MS).
* Server pak spustí vykreslování na základě pozice
* Server pošle zpátky kódovaný obraz videa.
* klient dekóduje bitovou kopii, provádí v nich více PROCESORů a GPU a pak zobrazuje obrázek.

## <a name="frame-statistics-queries"></a>Dotazy na statistiky snímků

Statistiky snímků poskytují některé informace vysoké úrovně pro poslední rámec, například latenci. Data zadaná ve `FrameStatistics` struktuře se měří na straně klienta, takže rozhraní API je synchronní volání:

```cs
void QueryFrameData(RenderingSession session)
{
    FrameStatistics frameStatistics;
    if (session.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        // do something with the result
    }
}
```

```cpp
void QueryFrameData(ApiHandle<RenderingSession> session)
{
    FrameStatistics frameStatistics;
    if (session->GetGraphicsBinding()->GetLastFrameStatistics(&frameStatistics) == Result::Success)
    {
        // do something with the result
    }
}
```

Načtený `FrameStatistics` objekt obsahuje následující členy:

| Člen | Vysvětlení |
|:-|:-|
| LatencyPoseToReceive | Latence z fotoaparátu představuje odhad na klientském zařízení, dokud není rámec serveru pro tuto pozici plně dostupný klientské aplikaci. Tato hodnota zahrnuje zpáteční dobu v síti, čas vykreslování serveru, dekódování videa a kompenzaci kolísání. Viz **interval 1 na obrázku výše.**|
| LatencyReceiveToPresent | Latence z dostupnosti přijatého vzdáleného rámce, dokud klientská aplikace nevolá PresentFrame na procesor. Viz **interval 2 na obrázku výše.**|
| LatencyPresentToDisplay  | Latence v zobrazování snímku na CPU, dokud se nezobrazují indikátory Tato hodnota zahrnuje čas GPU klienta, všechny snímky uložené do vyrovnávací paměti prováděné operačním systémem, reprojektování hardwaru a čas vyzkoušení displeje závislého na zařízení. Viz **interval 3 na obrázku výše.**|
| TimeSinceLastPresent | Čas mezi následnými voláními PresentFrame na procesoru. Hodnoty větší než doba trvání displeje (například 16,6 MS na klientském zařízení 60-Hz) označují problémy způsobené klientskou aplikací v čase, kdy nedokončuje zatížení procesoru.|
| VideoFramesReceived | Počet rámců přijatých ze serveru za poslední sekundu. |
| VideoFrameReusedCount | Počet přijatých snímků za poslední sekundu, které byly použity na zařízení více než jednou. Nenulové hodnoty označují, že se snímky musely znovu použít a znovu promítnout z důvodu kolísání sítě nebo nadměrného vykreslování serveru. |
| VideoFramesSkipped | Počet přijatých rámců za poslední sekundu, které byly Dekódovatelné, ale nebyly zobrazeny při zobrazení, protože byl přijat novější rámec. Nenulové hodnoty označují, že kolísání sítě způsobilo zpoždění více rámců a pak dorazí do klientského zařízení dohromady v shluku. |
| VideoFramesDiscarded | Velmi podobné jako **VideoFramesSkipped**, ale důvod, který je zahozený, je, že snímek byl v tom pozdě, takže ještě není možné ho ještě vzájemně sladit s případnými nedokončenými pozicemi. Pokud k této zahození dojde, dojde k výraznému kolizi sítě.|
| VideoFrameMinDelta | Minimální doba mezi dvěma po sobě jdoucích snímky přicházejících během poslední sekundy. Tento rozsah spolu s VideoFrameMaxDelta poskytuje indikaci kolísání způsobený buď kodekem sítě, nebo kodekem videa. |
| VideoFrameMaxDelta | Maximální doba mezi dvěma po sobě jdoucích rámců přicházejících během poslední sekundy. Tento rozsah spolu s VideoFrameMinDelta poskytuje indikaci kolísání způsobený buď kodekem sítě, nebo kodekem videa. |

Součet všech hodnot latence je obvykle mnohem větší než dostupný čas snímku v 60 Hz. To je v pořádku, protože více snímků je současně souběžně a nové žádosti o snímek jsou v požadované snímkové sazbě aktivní, jak je znázorněno na obrázku. Pokud je latence příliš velká, ovlivňuje kvalitu [reprojekce za fáze v pozdní fázi](../../overview/features/late-stage-reprojection.md)a může ohrozit celkové prostředí.

`VideoFramesReceived`, `VideoFrameReusedCount` a `VideoFramesDiscarded` lze je použít k měření výkonu sítě a serveru. Kombinace nízké `VideoFramesReceived` hodnoty a vysoké `VideoFrameReusedCount` hodnoty může označovat zahlcení sítě nebo špatný výkon serveru. Vysoká `VideoFramesDiscarded` hodnota také označuje zahlcení sítě.

Nakonec, `TimeSinceLastPresent` `VideoFrameMinDelta` a `VideoFrameMaxDelta` Podělte se o odchylku příchozích snímků videa a místních současných volání. Vysoká odchylka znamená nestabilní snímkovou sazbu.

Žádná z výše uvedených hodnot neposkytuje jasné označení čisté latence sítě (červené šipky na obrázku), protože přesný čas, kdy je server zaneprázdněný vykreslováním, je nutné odečíst od hodnoty zpětného převodu `LatencyPoseToReceive` . Na straně serveru celkové latence jsou informace, které nejsou pro klienta k dispozici. Další odstavec ale vysvětluje, jak se tato hodnota blíží k dalšímu vstupu ze serveru a je vystavená prostřednictvím `NetworkLatency` hodnoty.

## <a name="performance-assessment-queries"></a>Dotazy na vyhodnocení výkonu

*Dotazy na vyhodnocení výkonu* poskytují podrobné informace o procesoru a úlohách GPU na serveru. Vzhledem k tomu, že data jsou požadována ze serveru, dotazování snímku výkonu následuje po běžném asynchronním vzoru:

```cs
async void QueryPerformanceAssessment(RenderingSession session)
{
    try
    {
        PerformanceAssessment result = await session.Connection.QueryServerPerformanceAssessmentAsync();
        // do something with result...
    }
    catch (RRException ex)
    {
    }
}
```

```cpp
void QueryPerformanceAssessment(ApiHandle<RenderingSession> session)
{
    session->Connection()->QueryServerPerformanceAssessmentAsync([](Status status, PerformanceAssessment result) {
        if (status == Status::OK)
        {
            // do something with result...
        }
    });
}
```

V rozporu s `FrameStatistics` objektem `PerformanceAssessment` obsahuje objekt informace na straně serveru:

| Člen | Vysvětlení |
|:-|:-|
| TimeCPU | Průměrná doba procesoru serveru na rámec v milisekundách |
| TimeGPU | Průměrná doba GPU serveru na rámec v milisekundách |
| UtilizationCPU | Celkové využití procesoru serveru v procentech |
| UtilizationGPU | Celkové využití GPU serveru v procentech |
| MemoryCPU | Celkové využití hlavní paměti serveru v procentech |
| MemoryGPU | Celkové využití vyhrazené paměti pro video v procentech GPU serveru |
| Latence sítě | Přibližná Průměrná doba odezvy sítě v milisekundách Na ilustraci výše tato hodnota odpovídá součtu červené šipky. Hodnota je vypočítána odečtením skutečného času vykreslování serveru od `LatencyPoseToReceive` hodnoty `FrameStatistics` . I když tato aproximace není přesná, poskytuje určitou indikaci latence sítě, která je izolovaná od hodnot latence vypočítaných v klientovi. |
| PolygonsRendered | Počet trojúhelníků vykreslených v jednom snímku. Toto číslo zahrnuje také trojúhelníky, které byly po vygenerování později poraženy. To znamená, že toto číslo se neliší v různých polohách kamery, ale výkon se může výrazně lišit v závislosti na sazbě pro odstranení trojúhelníku.|

Pro lepší vyhodnocení hodnot zahrnuje každá část klasifikaci kvality, jako je **Skvělé**, **dobrá**, **Mediocre** nebo **špatná**.
Tato metrika posouzení poskytuje přibližnou indikaci stavu serveru, ale neměla by být zobrazena jako absolutní. Předpokládejme například, že se zobrazí skóre Mediocre pro čas GPU. Je považován za Mediocre, protože se blíží limitu pro celkové časové náklady v rámci rámce. V takovém případě může být nicméně dobrá hodnota, protože vykreslujete složitý model.

## <a name="statistics-debug-output"></a>Statistika ladění výstupu

Třída `ServiceStatistics` je třída jazyka C#, která se zalomí kolem testů rámce a dotazů na vyhodnocení výkonu a poskytuje pohodlný funkce pro vracení statistik jako agregovaných hodnot nebo jako předem sestavený řetězec. Následující kód představuje nejjednodušší způsob, jak zobrazit statistiky na straně serveru v klientské aplikaci.

```cs
ServiceStatistics _stats = null;

void OnConnect()
{
    _stats = new ServiceStatistics();
}

void OnDisconnect()
{
    _stats = null;
}

void Update()
{
    if (_stats != null)
    {
        // update once a frame to retrieve new information and build average values
        _stats.Update(Service.CurrentActiveSession);

        // retrieve a string with relevant stats information
        InfoLabel.text = _stats.GetStatsString();
    }
}
```

Výše uvedený kód naplní textový popisek následujícím textem:

![Výstup řetězce ArrServiceStats](./media/arr-service-stats.png)

`GetStatsString`Rozhraní API formátuje řetězec všech hodnot, ale každá jediná hodnota může být z instance také dotazována prostřednictvím kódu programu `ServiceStatistics` .

Existují také varianty členů, které hodnoty agreguje v průběhu času. Viz členy s příponou `*Avg` , `*Max` nebo `*Total` . Člen indikuje, kolik `FramesUsedForAverage` snímků bylo pro tuto agregaci použito.

## <a name="api-documentation"></a>Dokumentace k rozhraní API

* [C# RenderingConnection. QueryServerPerformanceAssessmentAsync ()](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.queryserverperformanceassessmentasync)
* [C++ RenderingConnection:: QueryServerPerformanceAssessmentAsync ()](/cpp/api/remote-rendering/renderingconnection#queryserverperformanceassessmentasync)

## <a name="next-steps"></a>Další kroky

* [Vytváření trasování výkonu](../../how-tos/performance-tracing.md)
* [Konfigurace převodu modelu](../../how-tos/conversion/configure-model-conversion.md)