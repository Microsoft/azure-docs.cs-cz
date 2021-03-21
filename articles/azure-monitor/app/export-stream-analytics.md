---
title: Export pomocí Stream Analytics z Azure Application Insights | Microsoft Docs
description: Stream Analytics může průběžně transformovat, filtrovat a směrovat data, která exportujete z Application Insights.
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: a517bddd8981554b7fb5044d33b6c6777df51e36
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101719793"
---
# <a name="use-stream-analytics-to-process-exported-data-from-application-insights"></a>Použití Stream Analytics k zpracování exportovaných dat z Application Insights

[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) je ideálním nástrojem pro zpracování dat [exportovaných z Application Insights](export-telemetry.md). Stream Analytics může vyžádat data z nejrůznějších zdrojů. Může data transformovat a filtrovat a pak je směrovat na celou řadu umyvadel.

V tomto příkladu vytvoříme adaptér, který převezme data z Application Insights, přejmenuje a zpracuje některá pole a pošle je do Power BI.

> [!WARNING]
> K dispozici je mnohem lepší a jednodušší [způsob zobrazení Application Insights dat v Power BI](./export-power-bi.md). Cesta znázorněná tady je pouze příklad, který ilustruje zpracování exportovaných dat.

![Blokový diagram pro export prostřednictvím SA do PBI](./media/export-stream-analytics/020.png)

## <a name="create-storage-in-azure"></a>Vytvoření úložiště v Azure
Průběžný export vždycky ukládá data do účtu Azure Storage, takže nejdřív musíte úložiště vytvořit.

1. Vytvořte ve svém předplatném "klasický" účet úložiště v [Azure Portal](https://portal.azure.com).
   
   ![V Azure Portal vyberte nový, data, úložiště.](./media/export-stream-analytics/030.png)
2. Vytvoření kontejneru
   
    ![V novém úložišti vyberte kontejnery, klikněte na dlaždici kontejnery a pak přidat](./media/export-stream-analytics/040.png)
3. Zkopírování přístupového klíče úložiště
   
    Budete ji potřebovat brzy pro nastavení vstupu ke službě Stream Analytics.
   
    ![V úložišti otevřete nastavení, klíče a proveďte kopii primárního přístupového klíče.](./media/export-stream-analytics/045.png)

## <a name="start-continuous-export-to-azure-storage"></a>Spustit průběžný export do služby Azure Storage

[Průběžný export](export-telemetry.md) přesouvá data z Application Insights do služby Azure Storage.

1. V Azure Portal vyhledejte prostředek Application Insights, který jste vytvořili pro vaši aplikaci.
   
    ![Vyberte možnost Procházet, Application Insights a aplikace.](./media/export-stream-analytics/050.png)
2. Vytvořte průběžný export.
   
    ![Zvolit nastavení, průběžný export, přidat](./media/export-stream-analytics/060.png)

    Vyberte účet úložiště, který jste vytvořili dříve:

    ![Nastavit cíl exportu](./media/export-stream-analytics/070.png)

    Nastavte typy událostí, které chcete zobrazit:

    ![Výběr typů událostí](./media/export-stream-analytics/080.png)

1. Nechte si shromažďovat data. Nasaďte se zpátky a umožněte lidem, aby na chvíli používali vaši aplikaci. Telemetrie se objeví v a v [diagnostickém vyhledávání](./diagnostic-search.md)uvidíte statistické grafy v [Průzkumníkovi metrik](../essentials/metrics-charts.md) a jednotlivé události.
   
    Data budou také exportována do vašeho úložiště. 
2. Zkontrolujte exportovaná data. V aplikaci Visual Studio vyberte možnost **Zobrazit/Cloud Průzkumník** a otevřete Azure/úložiště. (Pokud nemáte tuto možnost nabídky, musíte nainstalovat sadu Azure SDK: otevřete dialogové okno Nový projekt a otevřete Visual C#/Cloud/získat Microsoft Azure SDK pro .NET.)
   
    ![Snímek obrazovky ukazující, jak nastavit typy událostí, které chcete zobrazit.](./media/export-stream-analytics/04-data.png)
   
    Poznamenejte si běžnou část názvu cesty, která je odvozena z názvu aplikace a klíče instrumentace.

Události se zapisují do souborů BLOB ve formátu JSON. Každý soubor může obsahovat jednu nebo více událostí. Proto bychom chtěli číst data událostí a vyfiltrovat požadovaná pole. Existují nejrůznější věci, které jsme s daty mohli dělat, ale náš plán dnes používá Stream Analytics k přesměrování dat do Power BI.

## <a name="create-an-azure-stream-analytics-instance"></a>Vytvoření instance Azure Stream Analytics

Z [Azure Portal](https://portal.azure.com/)vyberte službu Azure Stream Analytics a vytvořte novou Stream Analytics úlohu:

![Snímek obrazovky, který zobrazuje hlavní stránku pro vytváření Stream Analyticsch úloh v Azure Portal.](./media/export-stream-analytics/SA001.png)

![Snímek obrazovky, který zobrazuje podrobnosti potřebné při vytváření nové Stream Analytics úlohy.](./media/export-stream-analytics/SA002.png)

Po vytvoření nové úlohy vyberte **Přejít k prostředku**.

![Snímek obrazovky zobrazující zprávu přijatou v případě úspěchu nového nasazení úlohy Stream Analytics](./media/export-stream-analytics/SA003.png)

### <a name="add-a-new-input"></a>Přidat nový vstup

![Snímek obrazovky, který ukazuje, jak přidat vstupy do úlohy Stream Analytics.](./media/export-stream-analytics/SA004.png)

Nastavte ho tak, aby převzal výstup z objektu BLOB průběžného exportu:

![Snímek obrazovky, který ukazuje konfiguraci Stream Analytics úlohy, aby probrala vstup z objektu BLOB průběžného exportu.](./media/export-stream-analytics/SA0005.png)

Teď budete potřebovat primární přístupový klíč z účtu úložiště, který jste si poznamenali dříve. Nastavte tuto hodnotu jako klíč účtu úložiště.

### <a name="set-path-prefix-pattern"></a>Nastavit vzor předpony cesty

**Nezapomeňte nastavit formát data na RRRR-MM-DD (s pomlčkami).**

Vzor předpony cesty určuje, kde Stream Analytics najde vstupní soubory v úložišti. Musíte ho nastavit tak, aby odpovídal způsobu průběžného exportu uložených dat. Nastavte to takto:

`webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}`

V tomto příkladu:

* `webapplication27` je název Application Insights prostředku **malými písmeny**.
* `1234...` je klíč instrumentace prostředku Application Insights a **vynechává se pomlčky**. 
* `PageViews` je typ dat, která chcete analyzovat. Dostupné typy závisí na filtru, který jste nastavili při průběžném exportu. Prohlédněte si exportovaná data a podívejte se na další dostupné typy a podívejte se na [model exportu dat](export-data-model.md).
* `/{date}/{time}` je vzor psaný doslova.

> [!NOTE]
> Zkontrolujte úložiště, abyste se ujistili, že jste získali správnou cestu.

## <a name="add-new-output"></a>Přidat nový výstup

Nyní vyberte úlohu > **výstupy**  >  **Přidat**.

![Snímek obrazovky, který zobrazuje výběr úlohy Stream Analytics pro přidání nového výstupu.](./media/export-stream-analytics/SA006.png)


![Vyberte nový kanál, klikněte na výstupy, přidat Power BI](./media/export-stream-analytics/SA010.png)

Zadejte svůj **pracovní nebo školní účet** pro autorizaci Stream Analytics k přístupu k prostředkům Power BI. Potom dozásobí název výstupu a pro cílovou Power BI datovou sadu a tabulku.

## <a name="set-the-query"></a>Nastavení dotazu

Dotaz řídí převod ze vstupu na výstup.

Pomocí funkce test zkontrolujte, zda se zobrazí správný výstup. Poskytněte IT ukázková data, která jste trvali na stránce vstupy.

### <a name="query-to-display-counts-of-events"></a>Dotaz na zobrazení počtu událostí

Vložit tento dotaz:

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

* Export-vstup je alias, který jsme přiřadili vstupu datového proudu.
* PBI-Output je výstupní alias, který jsme definovali.
* Použijeme [vnější prvky Apply](/stream-analytics-query/apply-azure-stream-analytics) , protože název události je ve vnořeném poli JSON. Pak příkaz SELECT vybere název události spolu s počtem instancí s tímto názvem v daném časovém období. Klauzule [Group by](/stream-analytics-query/group-by-azure-stream-analytics) seskupuje prvky do časových období jedné minuty.

### <a name="query-to-display-metric-values"></a>Dotaz pro zobrazení hodnot metriky

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

* Tento dotaz přejde do telemetrie metrik a získá čas události a hodnotu metriky. Hodnoty metriky jsou uvnitř pole, takže pro extrakci řádků používáme vzor VNĚJŠÍho prvku APPLy. "myMetric" je název metriky v tomto případě.

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

* Tento dotaz zahrnuje hodnoty vlastností dimenze bez závislosti na konkrétní dimenzi v pevně daném indexu v poli dimenzí.

## <a name="run-the-job"></a>Spuštění úlohy

Můžete vybrat datum v minulosti, od kterého se má úloha spustit.

![Vyberte úlohu a klikněte na dotaz. Níže uvedený příklad vložte.](./media/export-stream-analytics/SA008.png)

Počkejte, dokud nebude úloha spuštěna.

## <a name="see-results-in-power-bi"></a>Zobrazit výsledky v Power BI

> [!WARNING]
> K dispozici je mnohem lepší a jednodušší [způsob zobrazení Application Insights dat v Power BI](./export-power-bi.md). Cesta znázorněná tady je pouze příklad, který ilustruje zpracování exportovaných dat.


Otevřete Power BI u svého pracovního nebo školního účtu a vyberte datovou sadu a tabulku, kterou jste definovali jako výstup úlohy Stream Analytics.

![V Power BI vyberte datovou sadu a pole.](./media/export-stream-analytics/200.png)

Nyní můžete tuto datovou sadu použít v sestavách a řídicích panelech v [Power BI](https://powerbi.microsoft.com).

![Snímek obrazovky ukazuje příklad sestavy vytvořený z datové sady v Power BI.](./media/export-stream-analytics/210.png)

## <a name="no-data"></a>Žádná data?

* Ověřte, že jste správně [nastavili formát data](#set-path-prefix-pattern) na rrrr-mm-dd (s pomlčkami).

## <a name="next-steps"></a>Další kroky
* [Průběžný export](export-telemetry.md)
* [Podrobný odkaz na datový model pro typy a hodnoty vlastností.](export-data-model.md)
* [Application Insights](./app-insights-overview.md)
