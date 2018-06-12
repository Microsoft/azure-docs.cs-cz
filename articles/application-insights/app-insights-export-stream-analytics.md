---
title: Exportovat pomocí služby Stream Analytics z Azure Application Insights | Microsoft Docs
description: Stream Analytics můžete nepřetržitě transformace, filtrovat a směrování dat, které exportujete z Application Insights.
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
ms.openlocfilehash: 874a338c27262de29b1806352ec3ade068c188e0
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294230"
---
# <a name="use-stream-analytics-to-process-exported-data-from-application-insights"></a>Použití Stream Analytics ke zpracování exportovaná data ze služby Application Insights
[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) je ideální nástroj pro zpracování dat [exportovaný z Application Insights](app-insights-export-telemetry.md). Stream Analytics můžete načítat data z různých zdrojů. Může transformace a filtrovat data a pak směrovat celou řadu jímky.

V tomto příkladu vytvoříme adaptéru, která vezme všechna data ze služby Application Insights, přejmenuje a zpracovává některá pole a prostřednictvím kanálu předá ho do Power BI.

> [!WARNING]
> Existují mnohem lepší a jednodušší [doporučené způsoby, jak zobrazit data Application Insights v Power BI](app-insights-export-power-bi.md). Cesta zde znázorněný je jenom jako příklad k ukazují, jak zpracovat exportovaná data.
> 
> 

![Blokový diagram pro export prostřednictvím SA ke PBI](./media/app-insights-export-stream-analytics/020.png)

## <a name="create-storage-in-azure"></a>Vytvořte úložiště v Azure
Průběžné export vždy poskytuje data pro účet úložiště Azure, musíte nejprve vytvořit úložiště.

1. Vytvořit účet úložiště "classic" v rámci vašeho předplatného v [portál Azure](https://portal.azure.com).
   
   ![Na portálu Azure zvolte nový, Data, úložiště](./media/app-insights-export-stream-analytics/030.png)
2. Vytvoření kontejneru
   
    ![V novém úložišti vyberte kontejnery, klikněte na dlaždici kontejnery a potom přidat](./media/app-insights-export-stream-analytics/040.png)
3. Kopii přístupového klíče úložiště.
   
    Musíte ho brzy k nastavení vstup do služby stream analytics.
   
    ![V úložišti otevře se nastavení klíče a proveďte kopii primární přístupový klíč](./media/app-insights-export-stream-analytics/045.png)

## <a name="start-continuous-export-to-azure-storage"></a>Spustit průběžné export do úložiště Azure
[Průběžné export](app-insights-export-telemetry.md) přesune data z Application Insights do úložiště Azure.

1. Na portálu Azure přejděte do prostředku Application Insights, kterou jste vytvořili pro vaši aplikaci.
   
    ![Vyberte možnost Procházet, Application Insights, vaše aplikace](./media/app-insights-export-stream-analytics/050.png)
2. Vytvořte průběžné export.
   
    ![Vyberte nastavení, průběžné exportu, přidejte](./media/app-insights-export-stream-analytics/060.png)

    Vyberte účet úložiště, které jste vytvořili dříve:

    ![Nastavit cíl exportu](./media/app-insights-export-stream-analytics/070.png)

    Nastavte typy událostí, které chcete zobrazit:

    ![Vyberte typy událostí](./media/app-insights-export-stream-analytics/080.png)

1. Některá data hromadí let. Sledujte a umožnit lidem nějakou dobu používat vaši aplikaci. Telemetrická data se odešlou a zobrazí statistické grafy v [metriky explorer](app-insights-metrics-explorer.md) a jednotlivé události v [diagnostické vyhledávání](app-insights-diagnostic-search.md). 
   
    A navíc bude exportovat data do úložiště. 
2. Zkontrolujte exportovaná data. V sadě Visual Studio, vyberte **zobrazení / cloudu Explorer**a otevřete Azure nebo úložiště. (Pokud nemáte tento příkaz nabídky, budete muset nainstalovat sadu Azure SDK: Otevřete dialogové okno Nový projekt a otevřete Visual C# / Cloud / získat Microsoft Azure SDK pro .NET.)
   
    ![](./media/app-insights-export-stream-analytics/04-data.png)
   
    Poznamenejte si část běžný název cesty, které je odvozeno z klíče název a instrumentace aplikací. 

Události se zapisují do objektu blob soubory ve formátu JSON. Každý soubor může obsahovat jeden nebo více událostí. Proto jsme chtěli číst data události a filtrovat pole, která má být. Jsou k dispozici všechny typy věcí, které můžeme udělat s daty, ale naše plán dnes je použití Stream Analytics k předat data do Power BI.

## <a name="create-an-azure-stream-analytics-instance"></a>Vytvoření instance služby Azure Stream Analytics
Z [portál Azure](https://portal.azure.com/), vyberte službu Azure Stream Analytics a vytvořit novou úlohu služby Stream Analytics:

![](./media/app-insights-export-stream-analytics/SA001.png)

![](./media/app-insights-export-stream-analytics/SA002.png)

Při vytváření nové úlohy, vyberte **přejděte k prostředku**.

![](./media/app-insights-export-stream-analytics/SA003.png)

### <a name="add-a-new-input"></a>Přidat nové vstup

![](./media/app-insights-export-stream-analytics/SA004.png)

Nastavte ji tak, aby vstupní z objektu blob služby průběžné Export:

![](./media/app-insights-export-stream-analytics/SA005.png)

Nyní budete potřebovat primární přístupový klíč z vašeho účtu úložiště, který jste si předtím poznamenali. Nastavením této hodnoty jako klíč účtu úložiště.

### <a name="set-path-prefix-pattern"></a>Sada cesta předpona vzoru

**Nezapomeňte nastavit formát data do rrrr-MM-DD (s pomlčkami).**

Vzor předpony cesta Určuje, kde Stream Analytics vyhledá vstupní soubory v úložišti. Budete muset nastavit tak, aby odpovídaly jak průběžné exportovat data uloží. Nastavte takto:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

V tomto příkladu:

* `webapplication27` je název prostředku Application Insights **malá všechny**.
* `1234...` je klíč instrumentace prostředku Application Insights **vynechání pomlčky**. 
* `PageViews` je typu dat, který chcete analyzovat. Dostupné typy závisí na filtr, který nastavíte v průběžné exportovat. Zkontrolujte exportovaná data zobrazit dostupné typy a zobrazit [Exportovat datový model](app-insights-export-data-model.md).
* `/{date}/{time}` vzor zapsána oznámena.

> [!NOTE]
> Zkontrolujte úložiště a ujistěte se, že správné získání cesty.
> 

## <a name="add-new-output"></a>Přidat nové výstup
Nyní vyberte úlohu > **výstupy** > **přidat**.

![](./media/app-insights-export-stream-analytics/SA006.png)


![Vyberte nový kanál, klikněte na výstupy, přidat, Power BI](./media/app-insights-export-stream-analytics/SA010.png)

Zadejte vaše **pracovní nebo školní účet** k autorizaci Stream Analytics pro přístup k prostředku Power BI. Pak vytvořte název pro výstup a pro datovou sadu cíl Power BI a tabulku.

## <a name="set-the-query"></a>Nastavení dotazu
Dotaz řídí překlad ze vstupu na výstup.

Zkontrolujte, že dostanete správné výstup, použijte funkci Test. Poskytněte ukázková data, která trvala ze stránky vstupy. 

### <a name="query-to-display-counts-of-events"></a>Spočítá počet dotazů k zobrazení událostí
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

* Export vstup je alias, který jsme uvedl do datového proudu vstup
* pbi-output se výstup alias, který jsme definovali
* Používáme [vnější použít GetElements](https://msdn.microsoft.com/library/azure/dn706229.aspx) vzhledem k tomu, že je z názvu události ve vnořených pole JSON. Potom Select vybere název události, společně s počet instancí s tímto názvem v časovém období. [Group By](https://msdn.microsoft.com/library/azure/dn835023.aspx) klauzule skupiny elementy do časových období jednu minutu.

### <a name="query-to-display-metric-values"></a>Dotaz k zobrazení hodnot metriky
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

* Tento dotaz prochází do telemetrie metriky získat čas události a metriky hodnotu. Metriky hodnoty jsou uvnitř pole, takže jsme použít vzor vnější GetElements použít k extrahování řádky. "myMetric" v tomto případě je název metriky. 

### <a name="query-to-include-values-of-dimension-properties"></a>Dotaz pro zahrnout hodnoty vlastností dimenze
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

* Tento dotaz obsahuje hodnoty vlastností dimenze bez v závislosti na konkrétní dimenzi probíhá na pevnou index v poli dimenze.

## <a name="run-the-job"></a>Spustit úlohu
Vybrat datum v minulosti při spuštění úlohy z. 

![Vyberte úlohu a klikněte na dotaz. Vložte následující ukázka.](./media/app-insights-export-stream-analytics/SA008.png)

Počkejte, dokud je úloha spuštěna.

## <a name="see-results-in-power-bi"></a>Zobrazte výsledky v Power BI
> [!WARNING]
> Existují mnohem lepší a jednodušší [doporučené způsoby, jak zobrazit data Application Insights v Power BI](app-insights-export-power-bi.md). Cesta zde znázorněný je jenom jako příklad k ukazují, jak zpracovat exportovaná data.
> 
> 

Otevřít Power BI se váš pracovní nebo školní účet a vyberte datovou sadu a tabulky, který jste definovali jako výstup úlohy Stream Analytics.

![V Power BI vyberte pole a datové sady.](./media/app-insights-export-stream-analytics/200.png)

Teď můžete použít tuto datovou sadu v sestavy a řídicí panely v [Power BI](https://powerbi.microsoft.com).

![V Power BI vyberte pole a datové sady.](./media/app-insights-export-stream-analytics/210.png)

## <a name="no-data"></a>Žádná data?
* Zkontrolujte, které [nastaven formát data](#set-path-prefix-pattern) správně na rrrr-MM-DD (s pomlčkami).

## <a name="video"></a>Video
Noam Ben Zeev ukazuje, jak zpracovat exportovaná data pomocí služby Stream Analytics.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Export-to-Power-BI-from-Application-Insights/player]
> 
> 

## <a name="next-steps"></a>Další postup
* [Průběžný export](app-insights-export-telemetry.md)
* [Podrobný datový model referenční informace pro vlastnost typů a hodnot.](app-insights-export-data-model.md)
* [Application Insights](app-insights-overview.md)

