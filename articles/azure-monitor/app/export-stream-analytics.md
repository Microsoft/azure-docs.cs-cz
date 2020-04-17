---
title: Export pomocí Stream Analytics z Azure Application Insights | Dokumenty společnosti Microsoft
description: Stream Analytics může průběžně transformovat, filtrovat a směrovat data, která exportujete z Application Insights.
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: 15d1efa3a632024429d41f27fc23c569cd85bec2
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536875"
---
# <a name="use-stream-analytics-to-process-exported-data-from-application-insights"></a>Zpracování exportovaných dat z přehledů aplikací pomocí služby Stream Analytics
[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) je ideální nástroj pro zpracování dat [exportovaných z Application Insights](export-telemetry.md). Stream Analytics může přitahovat data z různých zdrojů. Může transformovat a filtrovat data a pak je směrovat do různých jímek.

V tomto příkladu vytvoříme adaptér, který převezme data z Application Insights, přejmenuje a zpracuje některá pole a přenese je do Power BI.

> [!WARNING]
> Existují mnohem lepší a jednodušší [doporučené způsoby zobrazení dat Application Insights v Power BI](../../azure-monitor/app/export-power-bi.md ). Cesta ilustrovaná zde je pouze příkladem pro ilustraci, jak zpracovat exportovaná data.
> 
> 

![Blokový diagram pro export přes sa do pbi](./media/export-stream-analytics/020.png)

## <a name="create-storage-in-azure"></a>Vytvoření úložiště v Azure
Nepřetržitý export vždy vydezkuje data do účtu Azure Storage, takže nejdřív musíte vytvořit úložiště.

1. Vytvořte si "klasický" účet úložiště ve svém předplatném na [webu Azure Portal](https://portal.azure.com).
   
   ![Na Webu Azure Portal zvolte Nový, Data, Úložiště](./media/export-stream-analytics/030.png)
2. Vytvoření kontejneru
   
    ![V novém úložišti vyberte Kontejnery, klikněte na dlaždici Kontejnery a pak přidejte](./media/export-stream-analytics/040.png)
3. Kopírování přístupového klíče úložiště
   
    Budete ji brzy potřebovat k nastavení vstupu do služby analýzy datového proudu.
   
    ![V úložišti otevřete Nastavení, klíče a pořažte kopii primárního přístupového klíče](./media/export-stream-analytics/045.png)

## <a name="start-continuous-export-to-azure-storage"></a>Spuštění průběžného exportu do úložiště Azure
[Nepřetržitý export](export-telemetry.md) přesune data z application insights do úložiště Azure.

1. Na webu Azure Portal přejděte k prostředku Application Insights, který jste vytvořili pro svou aplikaci.
   
    ![Zvolte Procházet, Application Insights, vaše aplikace](./media/export-stream-analytics/050.png)
2. Vytvořte nepřetržitý export.
   
    ![Zvolte Nastavení, Nepřetržitý export, Přidat](./media/export-stream-analytics/060.png)

    Vyberte účet úložiště, který jste vytvořili dříve:

    ![Nastavení cíle exportu](./media/export-stream-analytics/070.png)

    Nastavte typy událostí, které chcete zobrazit:

    ![Výběr typů událostí](./media/export-stream-analytics/080.png)

1. Nechte některá data hromadit. Sednout si a nechat lidi používat vaši žádost na chvíli. Telemetrie přijde a uvidíte statistické grafy v [průzkumníku metrik](../../azure-monitor/platform/metrics-charts.md) a jednotlivé události v [diagnostickévyhledávání](../../azure-monitor/app/diagnostic-search.md). 
   
    A také se data exportují do vašeho úložiště. 
2. Zkontrolujte exportovaná data. Ve Visual Studiu zvolte **Zobrazit / Průzkumníka Cloudu**a otevřete Azure / Storage. (Pokud nemáte tuto možnost nabídky, musíte nainstalovat sadu Azure SDK: Otevřete dialogové okno Nový projekt a otevřete Visual C# / Cloud / Get Microsoft Azure SDK pro .NET.)
   
    ![](./media/export-stream-analytics/04-data.png)
   
    Poznamenejte si společnou část názvu cesty, která je odvozena od názvu aplikace a klíče instrumentace. 

Události jsou zapsány do souborů objektů blob ve formátu JSON. Každý soubor může obsahovat jednu nebo více událostí. Takže bychom si rádi přečetli data o událostech a odfiltrovali pole, která chceme. S daty bychom mohli dělat spoustu věcí, ale naším dnešním plánem je použít Stream Analytics k tomu, aby se data přepojila na Power BI.

## <a name="create-an-azure-stream-analytics-instance"></a>Vytvoření instance Azure Stream Analytics
Na [webu Azure Portal](https://portal.azure.com/)vyberte službu Azure Stream Analytics a vytvořte novou úlohu Stream Analytics:

![](./media/export-stream-analytics/SA001.png)

![](./media/export-stream-analytics/SA002.png)

Po vytvoření nové úlohy vyberte **přejít na zdroj**.

![](./media/export-stream-analytics/SA003.png)

### <a name="add-a-new-input"></a>Přidání nového vstupu

![](./media/export-stream-analytics/SA004.png)

Nastavte ji tak, aby přebíhal vstup z objektu blob průběžného exportu:

![](./media/export-stream-analytics/SA0005.png)

Teď budete potřebovat primární přístupový klíč ze svého účtu úložiště, který jste si poznamenali dříve. Nastavte to jako klíč účtu úložiště.

### <a name="set-path-prefix-pattern"></a>Nastavit vzorek předpony cesty

**Nezapomeňte nastavit formát data na YYYY-MM-DD (s pomlčkami).**

Vzor předpony cesty určuje, kde Stream Analytics najde vstupní soubory v úložišti. Je třeba nastavit tak, aby odpovídala způsobu nepřetržitého exportu ukládá data. Nastavte to takto:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

V tomto příkladu:

* `webapplication27`je název prostředku Application Insights **všechna malá písmena**.
* `1234...`je instrumentační klíč prostředku Application Insights, **vynechání pomlčky**. 
* `PageViews`je typ dat, která chcete analyzovat. Dostupné typy závisí na filtru nastaveném v režimu Nepřetržitý export. Zkontrolujte exportovaná data, abyste viděli další dostupné typy a podívejte se na [model exportu dat](export-data-model.md).
* `/{date}/{time}`je vzor psaný doslova.

> [!NOTE]
> Zkontrolujte úložiště, abyste se ujistili, že máte správnou cestu.
> 

## <a name="add-new-output"></a>Přidání nového výstupu
Nyní vyberte úlohu > **výstupy** > **Přidat**.

![](./media/export-stream-analytics/SA006.png)


![Vyberte nový kanál, klikněte na Výstupy, Přidat, Power BI](./media/export-stream-analytics/SA010.png)

Poskytněte svému **pracovnímu nebo školnímu účtu** autorizaci Stream Analytics pro přístup k vašemu prostředku Power BI. Pak vynaléhej název pro výstup a pro cílovou datovou sadu a tabulku Power BI.

## <a name="set-the-query"></a>Nastavení dotazu
Dotaz řídí překlad ze vstupu do výstupu.

Pomocí funkce Test zkontrolujte, zda máte správný výstup. Dejte mu ukázková data, která jste vzali ze stránky vstupů. 

### <a name="query-to-display-counts-of-events"></a>Dotaz pro zobrazení počtu událostí
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

* export-vstup je alias, který jsme dali vstupu datového proudu
* pbi-output je výstupní alias, který jsme definovali
* Používáme [OUTER APPLY GetElements,](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) protože název události je v nosné pole JSON. Potom Select vybere název události spolu s počtem instancí s tímto názvem v časovém období. Klauzule [Group By](https://docs.microsoft.com/stream-analytics-query/group-by-azure-stream-analytics) seskupuje prvky do časových období po jedné minutě.

### <a name="query-to-display-metric-values"></a>Dotaz pro zobrazení hodnot metrik
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

* Tento dotaz přejde do telemetrie metriky získat čas události a hodnotu metriky. Hodnoty metriky jsou uvnitř pole, takže používáme outer apply GetElements vzor extrahovat řádky. "myMetric" je název metriky v tomto případě. 

### <a name="query-to-include-values-of-dimension-properties"></a>Dotaz na zahrnutí hodnot vlastností dimenze
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

* Tento dotaz obsahuje hodnoty vlastností dimenze bez závislosti na určité dimenzi, která je v pevném indexu v poli dimenzí.

## <a name="run-the-job"></a>Spuštění úlohy
Můžete vybrat datum v minulosti, ze kterých má být úloha zahájena. 

![Vyberte úlohu a klikněte na Dotaz. Vložte ukázku níže.](./media/export-stream-analytics/SA008.png)

Počkejte, až bude úloha spuštěna.

## <a name="see-results-in-power-bi"></a>Zobrazení výsledků v Power BI
> [!WARNING]
> Existují mnohem lepší a jednodušší [doporučené způsoby zobrazení dat Application Insights v Power BI](../../azure-monitor/app/export-power-bi.md ). Cesta ilustrovaná zde je pouze příkladem pro ilustraci, jak zpracovat exportovaná data.
> 
> 

Otevřete Power BI pomocí pracovního nebo školního účtu a vyberte datovou sadu a tabulku, kterou jste definovali jako výstup úlohy Stream Analytics.

![V Power BI vyberte datovou sadu a pole.](./media/export-stream-analytics/200.png)

Teď můžete tuto datovou sadu používat v sestavách a řídicích panelech v [Power BI](https://powerbi.microsoft.com).

![V Power BI vyberte datovou sadu a pole.](./media/export-stream-analytics/210.png)

## <a name="no-data"></a>Žádná data?
* Zkontrolujte, zda jste správně [nastavili formát data](#set-path-prefix-pattern) na YYYY-MM-DD (s pomlčkami).

## <a name="video"></a>Video
Noam Ben Zeev ukazuje, jak zpracovávat exportovaná data pomocí Stream Analytics.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Export-to-Power-BI-from-Application-Insights/player]
> 
> 

## <a name="next-steps"></a>Další kroky
* [Průběžný export](export-telemetry.md)
* [Podrobný odkaz datového modelu pro typy vlastností a hodnoty.](export-data-model.md)
* [Application Insights](../../azure-monitor/app/app-insights-overview.md)

