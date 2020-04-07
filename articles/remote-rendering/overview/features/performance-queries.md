---
title: Dotazy na výkon na straně serveru
description: Jak provést dotazy na výkon na straně serveru prostřednictvím volání rozhraní API
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 9a28dee2d1e6d1355b729a56e8eeb8447e4ed8c8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80682023"
---
# <a name="server-side-performance-queries"></a>Dotazy na výkon na straně serveru

Dobrý výkon vykreslování na serveru je rozhodující pro stabilní snímkové frekvence a dobré uživatelské zkušenosti. Je důležité pečlivě sledovat charakteristiky výkonu na serveru a v případě potřeby optimalizovat. Údaje o výkonu lze dotazovat prostřednictvím vyhrazených funkcí rozhraní API.

Nejpůsobivější pro výkon vykreslování je vstupní data modelu. Vstupní data můžete vyladit, jak je popsáno v [části Konfigurace převodu modelu](../../how-tos/conversion/configure-model-conversion.md).

Výkon aplikace na straně klienta může být také kritickým bodem. Pro hloubkovou analýzu výkonu na straně klienta se doporučuje provést [trasování výkonu](../../how-tos/performance-tracing.md).

## <a name="clientserver-timeline"></a>Časová osa klient/server

Než půjdete do podrobností týkajících se různých hodnot latence, stojí za to podívat se na synchronizační body mezi klientem a serverem na časové ose:

![Časová osa kanálu](./media/server-client-timeline.png)

Obrázek ukazuje, jak:

* *odhad Pose* je klientem odstartován při konstantní snímkové frekvenci 60 Hz (každých 16,6 ms)
* server pak spustí vykreslování na základě
* server odešle zpět kódovaný obraz videa
* klient dekóduje obraz, provádí některé CPU a GPU práce na vrcholu to a pak zobrazí obraz

## <a name="frame-statistics-queries"></a>Dotazy na statistiku rámce

Statistiky rámce poskytují některé informace na vysoké úrovni pro poslední rámec, jako je například latence. Data poskytnutá ve `FrameStatistics` struktuře jsou měřena na straně klienta, takže rozhraní API je synchronní volání:

````c#
void QueryFrameData(AzureSession session)
{
    FrameStatistics frameStatistics;
    if (session.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        // do something with the result
    }
}
````

Načtený `FrameStatistics` objekt obsahuje následující členy:

| Člen | Vysvětlení |
|:-|:-|
| latencyPoseToReceive | Latence z aplikace kamery představují odhad na klientském zařízení, dokud není pro klientskou aplikaci plně k dispozici rámeček serveru pro tuto pózu. Tato hodnota zahrnuje síťový roundtrip, čas vykreslení serveru, dekódování videa a kompenzaci kolísání. Viz **interval 1 na obrázku výše.**|
| latencyReceiveToPresent | Latence z dostupnosti přijatého vzdáleného rámce, dokud klientská aplikace nezavolá PresentFrame na procesoru. |
| latencyPresentToDisplay  | Latence z prezentace rámce na procesoru, dokud se nerozsvítí displej. Tato hodnota zahrnuje čas gpu klienta, jakékoli ukládání rámce do vyrovnávací paměti prováděné osem, reprojekce hardwaru a čas prohledávání displeje závislé na zařízení. Viz **interval 2 na obrázku výše.**|
| timeSinceLastPresent | Doba mezi následnými voláními PresentFrame na procesoru. Hodnoty větší než doba zobrazení (například 16,6 ms na klientském zařízení 60 Hz) označují problémy způsobené klientskou aplikací, která nedokončila své zatížení procesoru v čase. Viz **interval 3 na obrázku výše.**|
| videoFramesReceived | Počet rámců přijatých ze serveru v poslední sekundě. |
| videoFrameReusedCount | Počet přijatých snímků v poslední sekundě, které byly v zařízení použity více než jednou. Nenulové hodnoty označují, že rámce musely být znovu použity a znovu promítnuty z důvodu kolísání sítě nebo nadměrné doby vykreslování serveru. |
| videoFramesSkippedipped | Počet přijatých rámců v poslední sekundě, které byly dekódovány, ale nebyly zobrazeny na displeji, protože dorazil novější snímek. Nenulové hodnoty označují, že kolísání sítě způsobilo zpoždění více rámců a následné doručení do klientského zařízení společně v shlukování. |
| videoFramesDiscarded | Velmi podobné **videoFramesSkipped**, ale důvodem pro vyřazení je, že snímek přišel tak pozdě, že to nemůže být ani koreluje s probíhající představovat už. Pokud k tomu dojde, je některé závažné síťové tvrzení.|
| videoFrameMinDelta | Minimální doba mezi dvěma po sobě jdoucími snímky přicházejícími během poslední sekundy. Spolu s videoFrameMaxDelta, tento rozsah poskytuje údaj o chvění způsobené buď sítě nebo video kodek. |
| videoFrameMaxDelta | Maximální doba mezi dvěma po sobě jdoucími snímky přicházejícími během poslední sekundy. Spolu s videoFrameMinDelta, tento rozsah poskytuje údaj o chvění způsobené sítě nebo video kodek. |

Součet všech hodnot latence je obvykle mnohem větší než dostupný čas snímků v 60 Hz. To je v pořádku, protože více snímků je v letu paralelně a nové požadavky na snímek jsou zahájeny při požadovaném kmitočeti snímků, jak je znázorněno na obrázku. Pokud se však latence stane příliš velkou, ovlivní kvalitu [reprojekce pozdní fáze](../../overview/features/late-stage-reprojection.md)a může ohrozit celkové prostředí.

`videoFramesReceived`, `videoFrameReusedCount`a `videoFramesDiscarded` lze jej použít k měření výkonu sítě a serveru. Pokud `videoFramesReceived` je `videoFrameReusedCount` nízká a vysoká, může to znamenat zahlcení sítě nebo nízký výkon serveru. Vysoká `videoFramesDiscarded` hodnota také označuje zahlcení sítě.

A konečně `videoFrameMinDelta`, `videoFrameMaxDelta` `timeSinceLastPresent`a uveďte představu o rozptylu příchozích snímků videa a místních současných volání. Vysoká odchylka znamená nestabilní kmitočet snímků.

Žádná z výše uvedených hodnot neposkytuje jasnou indikaci čisté latence sítě (červené šipky na obrázku), protože přesný `latencyPoseToReceive`čas, po který je server zaneprázdněn vykreslováním, musí být odečten od hodnoty roundtrip . Část celkové latence na straně serveru je informace, které není k dispozici pro klienta. Další odstavec však vysvětluje, jak je tato hodnota aproximována prostřednictvím dalšího vstupu ze serveru a vystavena `networkLatency` prostřednictvím hodnoty.

## <a name="performance-assessment-queries"></a>Dotazy na posouzení výkonu

*Dotazy na posouzení výkonu* poskytují podrobnější informace o zatížení procesoru a GPU na serveru. Vzhledem k tomu, že data jsou požadována ze serveru, dotazování na snímek výkonu následuje obvyklý asynchronní vzor:

``` cs
PerformanceAssessmentAsync _assessmentQuery = null;

void QueryPerformanceAssessment(AzureSession session)
{
    _assessmentQuery = session.Actions.QueryServerPerformanceAssessmentAsync();
    _assessmentQuery.Completed += (PerformanceAssessmentAsync res) =>
    {
        // do something with the result:
        PerformanceAssessment result = res.Result;
        // ...

        _assessmentQuery = null;
    };
}
```

Na rozdíl `FrameStatistics` od objektu `PerformanceAssessment` objekt obsahuje informace na straně serveru:

| Člen | Vysvětlení |
|:-|:-|
| timeCPU | Průměrný čas procesoru serveru na snímek v milisekundách |
| timeGPU | Průměrný čas gpu serveru na snímek v milisekundách |
| využitíCPU | Celkové využití procesoru serveru v procentech |
| využitíGPU | Celkové využití gpu serveru v procentech |
| memoryCPU | Celkové využití hlavní paměti serveru v procentech |
| memoryGPU | Celkové využití vyhrazené grafické paměti v procentech gpu serveru |
| networkLatency | Přibližná průměrná latence sítě roundtrip v milisekundách. Na obrázku výše to odpovídá součtu červených šipek. Hodnota se vypočítá odečtením skutečné doby vykreslování serveru od `latencyPoseToReceive` hodnoty . `FrameStatistics` I když tato aproximace není přesná, poskytuje některé informace o latenci sítě, izolované od hodnoty latence vypočítané na straně klienta. |
| polygonyVykresleno | Počet trojúhelníků vykreslených v jednom snímku. Toto číslo také zahrnuje trojúhelníky, které jsou vyřazeny později během vykreslování. To znamená, že toto číslo se v různých polohách kamery mnoho nemění, ale výkon se může výrazně lišit v závislosti na míře utracení trojúhelníku.|

Abychom vám pomohli posoudit hodnoty, každá část je dodávána s klasifikací kvality, jako **je Velký**, **Dobrý**, **Průměrný**nebo **Špatný**.
Tato metrika hodnocení poskytuje hrubé označení stavu serveru, ale neměla by být považována za absolutní. Předpokládejme například, že se zobrazí "průměrné" skóre pro čas GPU. To je považováno za průměrné, protože se blíží limitpro celkový časový rozpočet rámce. Ve vašem případě však může být dobrá hodnota nicméně, protože vykreslujete složitý model.

## <a name="statistics-debug-output"></a>Výstup ladění statistiky

Třída `ARRServiceStats` obtéká statistiky rámce a dotazy na hodnocení výkonu a poskytuje pohodlné funkce pro vrácení statistiky jako agregované hodnoty nebo jako předem sestavený řetězec. Následující kód je nejjednodušší způsob, jak zobrazit statistiky na straně serveru v klientské aplikaci.

``` cs
ARRServiceStats _stats = null;

void OnConnect()
{
    _stats = new ARRServiceStats();
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

Rozhraní `GetStatsString` API formátuje řetězec všech hodnot, ale každá jedna hodnota může být `ARRServiceStats` také dotazován programově z instance.

Existují také varianty členů, které agregují hodnoty v čase. Viz členy s `*Avg`příponou , `*Max`nebo `*Total`. Člen `FramesUsedForAverage` označuje, kolik rámců bylo použito pro tuto agregaci.

## <a name="next-steps"></a>Další kroky

* [Vytvoření trasování výkonu](../../how-tos/performance-tracing.md)
* [Konfigurace převodu modelu](../../how-tos/conversion/configure-model-conversion.md)
