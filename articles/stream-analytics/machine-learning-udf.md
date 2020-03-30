---
title: Integrace Azure Stream Analytics s Azure Machine Learning
description: Tento článek popisuje, jak integrovat úlohu Azure Stream Analytics s modely Azure Machine Learning.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 354b0eae1620c49006b350371aae38f8867c18b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124660"
---
# <a name="integrate-azure-stream-analytics-with-azure-machine-learning-preview"></a>Integrace Azure Stream Analytics s Azure Machine Learning (Preview)

Modely strojového učení můžete implementovat jako uživatelem definovanou funkci (UDF) ve svých úlohách Azure Stream Analytics, abyste mohli provádět vyhodnocování v reálném čase a předpovědi na vstupních datech streamování. [Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md) umožňuje používat jakýkoli populární open source nástroj, jako je Tensorflow, scikit-learn nebo PyTorch, k přípravě, trénování a nasazování modelů.

> [!NOTE]
> Tato funkce je ve verzi Public Preview. K této funkci můžete přistupovat na webu Azure Portal pouze pomocí [odkazu náhledu portálu Stream Analytics](https://aka.ms/asaportalpreview). Tato funkce je k dispozici také v nejnovější verzi [nástrojů Stream Analytics pro Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install).

## <a name="prerequisites"></a>Požadavky

Před přidáním modelu strojového učení jako funkce do úlohy Stream Analytics proveďte následující kroky:

1. Azure Machine Learning slouží k [nasazení modelu jako webové služby](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where).

2. Váš bodovací skript by měl mít [ukázkové vstupy a výstupy,](../machine-learning/how-to-deploy-and-where.md#example-entry-script) které používá Azure Machine Learning ke generování specifikace schématu. Stream Analytics používá schéma k pochopení podpisu funkce vaší webové služby.

3. Ujistěte se, že vaše webová služba přijímá a vrací serializovaná data JSON.

4. Nasaďte svůj model ve [službě Azure Kubernetes](../machine-learning/how-to-deploy-and-where.md#choose-a-compute-target) pro nasazení ve velkém měřítku. Pokud webová služba není schopna zpracovat počet požadavků přicházejících z vaší úlohy, výkon úlohy Stream Analytics se sníží, což má vliv na latenci.

## <a name="add-a-machine-learning-model-to-your-job"></a>Přidání modelu strojového učení do své úlohy

Funkce Azure Machine Learning můžete přidat do úlohy Stream Analytics přímo z portálu Azure.

1. Přejděte na úlohu Stream Analytics na webu Azure Portal a v části **Topologie úloh**vyberte **Funkce** . Potom vyberte **službu Azure ML** v rozevírací nabídce **+ Přidat.**

   ![Přidání Azure ML UDF](./media/machine-learning-udf/add-azureml-udf.png)

2. Vyplňte formulář **funkce Služby Strojového učení Azure** s následujícími hodnotami vlastností:

   ![Konfigurace Azure ML UDF](./media/machine-learning-udf/configure-azureml-udf.png)

Následující tabulka popisuje každou vlastnost funkcí služby Azure ML v Stream Analytics.

|Vlastnost|Popis|
|--------|-----------|
|Alias funkce|Zadejte název pro vyvolání funkce v dotazu.|
|Předplatné|Vaše předplatné Azure..|
|Pracovní prostor Azure ML|Pracovní prostor Azure Machine Learning, který jste použili k nasazení modelu jako webové služby.|
|Nasazení|Webová služba hostující váš model.|
|Podpis funkce|Podpis webové služby odvozený ze specifikace schématu rozhraní API. Pokud se váš podpis nenačte, zkontrolujte, zda jste ve skriptu pro vyhošťování zadali ukázkový vstup a výstup, abyste automaticky vygenerovali schéma.|
|Počet paralelních požadavků na oddíl|Jedná se o pokročilou konfiguraci pro optimalizaci vysoké propustnost. Toto číslo představuje souběžné požadavky odeslané z každého oddílu úlohy webové službě. Úlohy se šesti streamovacími jednotkami (SU) a nižšími mají jeden oddíl. Úlohy s 12 SU mají dva oddíly, 18 SU mají tři oddíly a tak dále.<br><br> Pokud má například vaše úloha dva oddíly a tento parametr nastavíte na čtyři, bude osm souběžných požadavků z úlohy do webové služby. V tomto okamžiku public preview tato hodnota výchozí 20 a nelze aktualizovat.|
|Maximální počet dávek|Toto je pokročilá konfigurace pro optimalizaci propustnost ve vysokém měřítku. Toto číslo představuje maximální počet událostí, které budou dávkovány společně v jednom požadavku odeslaném webové službě.|

## <a name="supported-input-parameters"></a>Podporované vstupní parametry

Když váš dotaz Stream Analytics vyvolá UDF Azure Machine Learning, úloha vytvoří serializovaný požadavek JSON na webovou službu. Požadavek je založen na schématu specifické pro model. Musíte zadat ukázkový vstup a výstup ve skriptu hodnocení, abyste [automaticky vygenerovali schéma](../machine-learning/how-to-deploy-and-where.md). Schéma umožňuje Stream Analytics vytvořit serializovaný požadavek JSON pro některý z podporovaných datových typů, jako je numpy, pandas a PySpark. Více vstupních událostí lze dávkově sestavit v jednom požadavku.

Následující dotaz Stream Analytics je příkladem, jak vyvolat UDF Azure Machine Learning:

```SQL
SELECT udf.score(<model-specific-data-structure>)
INTO output
FROM input
```

Stream Analytics podporuje jenom předávání jednoho parametru pro funkce Azure Machine Learning. Možná budete muset připravit data před předáním jako vstup do strojového učení UDF.

## <a name="pass-multiple-input-parameters-to-the-udf"></a>Předání více vstupních parametrů do UDF

Nejběžnější příklady vstupů do modelů strojového učení jsou numpy pole a DataFrames. Můžete vytvořit pole pomocí JavaScript UDF a vytvořit JSON serializované `WITH` DataFrame pomocí klauzule.

### <a name="create-an-input-array"></a>Vytvoření vstupního pole

Můžete vytvořit UDF JavaScript, který přijímá *N* počet vstupů a vytvoří pole, které lze použít jako vstup do Azure Machine Learning UDF.

```javascript
function createArray(vendorid, weekday, pickuphour, passenger, distance) {
    'use strict';
    var array = [vendorid, weekday, pickuphour, passenger, distance]
    return array;
}
```

Po přidání JavaScript UDF do úlohy můžete vyvolat Azure Machine Learning UDF pomocí následujícího dotazu:

```SQL
SELECT udf.score(
udf.createArray(vendorid, weekday, pickuphour, passenger, distance)
)
INTO output
FROM input
```

Následující JSON je příklad požadavku:

```JSON
{
    "data": [
        ["1","Mon","12","1","5.8"],
        ["2","Wed","10","2","10"]
    ]
}
```

### <a name="create-a-pandas-or-pyspark-dataframe"></a>Vytvoření datového rámce Pand nebo PySpark

`WITH` Klauzuli můžete použít k vytvoření json serializované DataFrame, který může být předán jako vstup do Azure Machine Learning UDF, jak je znázorněno níže.

Následující dotaz vytvoří DataFrame výběrem polí potřebné a používá DataFrame jako vstup do Azure Machine Learning UDF.

```SQL
WITH 
Dataframe AS (
SELECT vendorid, weekday, pickuphour, passenger, distance
FROM input
)

SELECT udf.score(Dataframe)
INTO output
FROM input
```

Následující JSON je příklad požadavku z předchozího dotazu:

```JSON
{
    "data": [{
            "vendorid": "1",
            "weekday": "Mon",
            "pickuphour": "12",
            "passenger": "1",
            "distance": "5.8"
        }, {
            "vendorid": "2",
            "weekday": "Tue",
            "pickuphour": "10",
            "passenger": "2",
            "distance": "10"
        }
    ]
}
```

## <a name="optimize-the-performance-for-azure-machine-learning-udfs"></a>Optimalizace výkonu pro UDF Azure Machine Learning

Když nasadíte model do služby Azure Kubernetes, můžete [profilovat model k určení využití prostředků](../machine-learning/how-to-deploy-and-where.md#profilemodel). Můžete také [povolit App Insights pro vaše nasazení](../machine-learning/how-to-enable-app-insights.md) pochopit rychlost požadavků, doby odezvy a míry selhání.

Pokud máte scénář s vysokou propustností událostí, možná budete muset v Stream Analytics změnit následující parametry, abyste dosáhli optimálního výkonu s nízkými latencemi od konce ke konci:

1. Maximální počet dávek.
2. Počet paralelních požadavků na oddíl.

### <a name="determine-the-right-batch-size"></a>Určení správné velikosti dávky

Po nasazení webové služby odešlete ukázkový požadavek s různými velikostmi dávek od 50 a zvýšíte ji v pořadí stovek. Například 200, 500, 1000, 2000 a tak dále. Všimněte si, že po určité velikosti dávky se zvyšuje latence odpovědi. Bod, po kterém latence zvýšení odezvy by měla být maximální počet dávek pro vaši úlohu.

### <a name="determine-the-number-of-parallel-requests-per-partition"></a>Určení počtu paralelních požadavků na oddíl

Při optimálním škálování by vaše úloha Stream Analytics měla být schopna odesílat více paralelních požadavků webové službě a získat odpověď během několika milisekund. Latence odpovědi webové služby může mít přímý vliv na latenci a výkon úlohy Stream Analytics. Pokud volání z vaší úlohy do webové služby trvá dlouhou dobu, pravděpodobně uvidíte zvýšení zpoždění vodoznaku a může také vidět zvýšení počtu nevyřízených vstupních událostí.

Chcete-li zabránit takové latence, ujistěte se, že váš cluster Azure Kubernetes Service (AKS) byl zřízen se [správným počtem uzlů a replik](../machine-learning/how-to-deploy-azure-kubernetes-service.md#using-the-cli). Je důležité, aby vaše webová služba byla vysoce dostupná a vrátila úspěšné odpovědi. Pokud vaše úloha obdrží nedostupnou odpověď služby (503) z webové služby, bude průběžně opakovat s exponenciálním zpět. Jakákoli jiná odpověď než úspěch (200) a služba není k dispozici (503) způsobí, že vaše úloha přejde do neúspěšného stavu.

## <a name="next-steps"></a>Další kroky

* [Kurz: Uživatelem definované funkce jazyka JavaScript v Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Škálování úlohy Stream Analytics pomocí funkce Azure Machine Learning Studio (klasické)](stream-analytics-scale-with-machine-learning-functions.md)

