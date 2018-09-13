---
title: Export prostřednictvím Stream Analytics z Azure Application Insights | Dokumentace Microsoftu
description: Stream Analytics můžete průběžně transformace, filtrování a směrování dat, které exportujete z Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 31594221-17bd-4e5e-9534-950f3b022209
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 01/04/2018
ms.author: mbullwin
ms.openlocfilehash: 86e37a186536a2037d3cc46cb1cc69c1a7a651f6
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44719422"
---
# <a name="use-stream-analytics-to-process-exported-data-from-application-insights"></a>Pomocí Stream Analytics zpracovávat data exportovaná z Application Insights
[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) je ideálním nástrojem pro zpracování dat [exportovat ze služby Application Insights](app-insights-export-telemetry.md). Stream Analytics můžete vyžádat data z různých zdrojů. Můžete transformovat a filtrovat data a pak směrovat na různé jímky.

V tomto příkladu vytvoříme adaptér, který přijímá data ze služby Application Insights, přejmenování a zpracovává některá pole a přesměrují do Power BI.

> [!WARNING]
> Jsou mnohem lepšímu a snazšímu [doporučené způsoby, jak zobrazit data Application Insights v Power BI](app-insights-export-power-bi.md). Cesta znázorněno zde je uvedené jenom jako příklad si ukážeme, jak zpracovat exportovaná data.
> 
> 

![Blokový diagram pro export prostřednictvím SA PBI](./media/app-insights-export-stream-analytics/020.png)

## <a name="create-storage-in-azure"></a>Vytvoření úložiště v Azure
Průběžný export vždy výstupů dat do účtu služby Azure Storage, musíte nejprve vytvořit úložiště.

1. Vytvoření účtu úložiště v "klasickém" ve vašem předplatném v [webu Azure portal](https://portal.azure.com).
   
   ![Na webu Azure portal zvolte nový, dat, úložiště](./media/app-insights-export-stream-analytics/030.png)
2. Vytvoření kontejneru
   
    ![V novém úložišti vyberte kontejnery, klikněte na dlaždici kontejnery a pak přidat](./media/app-insights-export-stream-analytics/040.png)
3. Zkopírovat přístupový klíč úložiště
   
    Musíte ji nejdříve k nastavení vstupu do služby stream analytics.
   
    ![V úložišti otevřete nastavení, klíče a zkopírujte primární přístupový klíč](./media/app-insights-export-stream-analytics/045.png)

## <a name="start-continuous-export-to-azure-storage"></a>Spustit průběžný export do služby Azure storage
[Průběžný export](app-insights-export-telemetry.md) přesouvá data z Application Insights do služby Azure storage.

1. Na webu Azure Portal přejděte do prostředku Application Insights, který jste vytvořili pro vaši aplikaci.
   
    ![Klikněte na tlačítko Procházet, Application Insights, vaše aplikace](./media/app-insights-export-stream-analytics/050.png)
2. Vytvoření průběžný export.
   
    ![Zvolte možnost přidat nastavení průběžný Export](./media/app-insights-export-stream-analytics/060.png)

    Vyberte účet úložiště, který jste vytvořili dříve:

    ![Nastavte cíl exportu](./media/app-insights-export-stream-analytics/070.png)

    Nastavte typy událostí, které chcete zobrazit:

    ![Vyberte typy událostí](./media/app-insights-export-stream-analytics/080.png)

1. Umožnit shromažďování některá dat. Opřete se a umožnit lidem nějakou dobu používat vaši aplikaci. Telemetrická data budou přicházet a zobrazí statistické grafy v [Průzkumník metrik](app-insights-metrics-explorer.md) a jednotlivé události v [diagnostické vyhledávání](app-insights-diagnostic-search.md). 
   
    A také se exportovat data do úložiště. 
2. Kontrola exportovaná data. V sadě Visual Studio, zvolte **zobrazení / Cloud Explorer**a otevřete Azure / Storage. (Pokud nemáte k dispozici tato možnost nabídky, je potřeba nainstalovat sadu Azure SDK: Otevřete dialogové okno Nový projekt a otevřete Visual C# / cloudu / získat Microsoft Azure SDK pro .NET.)
   
    ![](./media/app-insights-export-stream-analytics/04-data.png)
   
    Poznamenejte si běžné část názvu cesty, které je odvozeno z názvu a Instrumentační klíče aplikace. 

Události se zapisují do objektu blob soubory ve formátu JSON. Každý soubor může obsahovat jeden nebo více událostí. Tak rádi bychom číst data události a filtrování pole, která chceme. Existují všechny druhy věcí, které můžeme udělat s daty, ale náš plán ještě dnes je předat data do Power BI pomocí Stream Analytics.

## <a name="create-an-azure-stream-analytics-instance"></a>Vytvoření instance Azure Stream Analytics
Z [webu Azure portal](https://portal.azure.com/), vyberte službu Azure Stream Analytics a vytvoření nové úlohy Stream Analytics:

![](./media/app-insights-export-stream-analytics/SA001.png)

![](./media/app-insights-export-stream-analytics/SA002.png)

Když se vytvoří novou úlohu, vyberte **přejít k prostředku**.

![](./media/app-insights-export-stream-analytics/SA003.png)

### <a name="add-a-new-input"></a>Přidat nový vstup

![](./media/app-insights-export-stream-analytics/SA004.png)

K provedení vstup z objektu blob služby průběžného exportu nastavení:

![](./media/app-insights-export-stream-analytics/SA0005.png)

Teď budete potřebovat primární přístupový klíč z vašeho účtu úložiště, který jste si předtím poznamenali. Nastavíte jako klíč účtu úložiště.

### <a name="set-path-prefix-pattern"></a>Vzor předpony cesty sady

**Ujistěte se, že nastavení formát data rrrr-MM-DD (s pomlčkami).**

Vzor předpony cesty určuje, kde najde Stream Analytics vstupních souborů v úložišti. Je nutné nastavit ji tak, aby odpovídaly průběžný Export způsobu, jakým ukládá data. Nastavte ho následujícím způsobem:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

V tomto příkladu:

* `webapplication27` název prostředku Application Insights je **malými písmeny**.
* `1234...` je Instrumentační klíč tohoto prostředku Application Insights **vynechání pomlčky**. 
* `PageViews` je typ dat, který chcete analyzovat. Dostupné typy závisí na filtru, který jste nastavili v průběžný Export. Prozkoumejte exportovaná data zobrazíte dostupné typy a podívejte se [export datového modelu](app-insights-export-data-model.md).
* `/{date}/{time}` je vzorek doslova zapsán.

> [!NOTE]
> Zkontrolujte úložiště, abyste měli jistotu, že získáte cesty ke správné.
> 

## <a name="add-new-output"></a>Přidat nový výstup
Teď vyberte úlohu > **výstupy** > **přidat**.

![](./media/app-insights-export-stream-analytics/SA006.png)


![Vyberte nový kanál, klikněte na výstupy, přidat, Power BI](./media/app-insights-export-stream-analytics/SA010.png)

Zadejte vaše **pracovního nebo školního účtu** k autorizaci Stream Analytics pro přístup k prostředku Power BI. Potom vytvořte název pro výstup a pro cílový dataset Power BI a tabulku.

## <a name="set-the-query"></a>Nastavit dotaz
Dotaz se řídí překlad ze vstupu do výstupu.

Použijte funkci testu ke kontrole, že dostanete správné výstup. Pojmenujte ji ukázková data, který je předáván ze stránky vstupy. 

### <a name="query-to-display-counts-of-events"></a>Chcete-li zobrazit počty událostí dotazu
Vložte tento dotaz:

```SQL

    SELECT
      flat.ArrayValue.name,
      count(*)
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.[event]) as flat
    GROUP BY TumblingWindow(minute, 1), flat.ArrayValue.name
```

* Export vstup je alias, který jsme zadali pro vstupní datový proud
* pbi výstup je alias pro výstup, který jsme definovali
* Používáme [vnější metody GetElements použít](https://msdn.microsoft.com/library/azure/dn706229.aspx) vzhledem k tomu, že je název události v vnořeného pole JSON. Potom vyberte vybere název události, společně se počet instancí s tímto názvem v časovém období. [Group](https://msdn.microsoft.com/library/azure/dn835023.aspx) klauzule seskupuje prvky do časových období délce jedné minuty.

### <a name="query-to-display-metric-values"></a>Dotaz, který zobrazí hodnoty metrik
```SQL

    SELECT
      A.context.data.eventtime,
      avg(CASE WHEN flat.arrayvalue.myMetric.value IS NULL THEN 0 ELSE  flat.arrayvalue.myMetric.value END) as myValue
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.context.custom.metrics) as flat
    GROUP BY TumblingWindow(minute, 1), A.context.data.eventtime

``` 

* Tento dotaz operací do telemetrie metrik zobrazíte času události a metriky hodnotu. Hodnoty metrik jsou uvnitř pole, takže použijeme vzor vnější metody GetElements použít k extrahování řádky. "myMetric" v tomto případě je název metriky. 

### <a name="query-to-include-values-of-dimension-properties"></a>Dotaz pro přidání hodnoty vlastnosti dimenze
```SQL

    WITH flat AS (
    SELECT
      MySource.context.data.eventTime as eventTime,
      InstanceId = MyDimension.ArrayValue.InstanceId.value,
      BusinessUnitId = MyDimension.ArrayValue.BusinessUnitId.value
    FROM MySource
    OUTER APPLY GetArrayElements(MySource.context.custom.dimensions) MyDimension
    )
    SELECT
     eventTime,
     InstanceId,
     BusinessUnitId
    INTO AIOutput
    FROM flat

```

* Tento dotaz obsahuje hodnoty z vlastnosti dimenze bez v závislosti na konkrétní dimenzi se oprava indexem dimenze pole.

## <a name="run-the-job"></a>Spuštění úlohy
Vyberte datum v minulosti, spustit úlohu. 

![Vyberte úlohu a klikněte na dotaz. Vložte následující ukázce.](./media/app-insights-export-stream-analytics/SA008.png)

Počkejte, dokud je úloha spuštěná.

## <a name="see-results-in-power-bi"></a>Zobrazení výsledků v Power BI
> [!WARNING]
> Jsou mnohem lepšímu a snazšímu [doporučené způsoby, jak zobrazit data Application Insights v Power BI](app-insights-export-power-bi.md). Cesta znázorněno zde je uvedené jenom jako příklad si ukážeme, jak zpracovat exportovaná data.
> 
> 

Otevřete Power BI přes svůj pracovní nebo školní účet a vyberte datovou sadu a tabulku, která jste definovali jako výstup úlohy Stream Analytics.

![V Power BI vyberte datovou sadu a pole.](./media/app-insights-export-stream-analytics/200.png)

Teď můžete tuto datovou sadu v sestavách a řídicích panelů v [Power BI](https://powerbi.microsoft.com).

![V Power BI vyberte datovou sadu a pole.](./media/app-insights-export-stream-analytics/210.png)

## <a name="no-data"></a>Žádná data?
* Zkontrolujte, které jste [nastavit formát data](#set-path-prefix-pattern) správně na rrrr-MM-DD (s pomlčkami).

## <a name="video"></a>Video
Noam Robert Zeev ukazuje, jak zpracovat exportovaná data pomocí Stream Analytics.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Export-to-Power-BI-from-Application-Insights/player]
> 
> 

## <a name="next-steps"></a>Další postup
* [Průběžný export](app-insights-export-telemetry.md)
* [Podrobný datový model referenční informace pro typy vlastností a hodnot.](app-insights-export-data-model.md)
* [Application Insights](app-insights-overview.md)

