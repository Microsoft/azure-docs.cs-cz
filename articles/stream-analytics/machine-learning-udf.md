---
title: Integrace Azure Stream Analytics s Azure Machine Learning
description: Tento článek popisuje, jak integrovat Azure Stream Analytics úlohu s modelem Azure Machine Learning.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/19/2020
ms.custom: devx-track-js
ms.openlocfilehash: a24d1716448363a199151a50a260bbdbd1e8e634
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91249480"
---
# <a name="integrate-azure-stream-analytics-with-azure-machine-learning-preview"></a>Integrace Azure Stream Analytics s Azure Machine Learning (Preview)

Modely strojového učení můžete implementovat jako uživatelsky definovanou funkci (UDF) ve svých úlohách Azure Stream Analytics, abyste v reálném čase provedli bodování a předpovědi se na vašich vstupních datech streamování. [Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md) vám umožňuje používat libovolný oblíbený open source nástroj, jako je Tensorflow, scikit-učení nebo PyTorch, pro přípravu, výuku a nasazení modelů.

## <a name="prerequisites"></a>Požadavky

Před přidáním modelu Machine Learning jako funkce do Stream Analytics úlohy proveďte následující kroky:

1. K [nasazení modelu jako webové služby](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)použijte Azure Machine Learning.

2. Váš skript bodování by měl mít [ukázkové vstupy a výstupy](../machine-learning/how-to-deploy-and-where.md) , které Azure Machine Learning používá ke generování specifikace schématu. Stream Analytics používá schéma pro pochopení signatury funkce webové služby. Tuto [ukázkovou definici Swagger](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/AzureML/swagger-example.json) můžete použít jako referenci, abyste měli jistotu, že je správně nastavená.

3. Ujistěte se, že webová služba přijímá a vrací Serializovaná data JSON.

4. Nasazení modelu ve [službě Azure Kubernetes](../machine-learning/how-to-deploy-and-where.md#choose-a-compute-target) pro vysoce škálovatelná produkční nasazení. Pokud webová služba nemůže zpracovat počet požadavků přicházejících z vaší úlohy, výkon vaší Stream Analytics úlohy se sníží, což má vliv na latenci. Modely nasazené v Azure Container Instances jsou podporovány pouze při použití Azure Portal. Modely vytvořené pomocí [Azure Machine Learning designeru](https://docs.microsoft.com/azure/machine-learning/concept-designer) se v Stream Analytics ještě nepodporují.

## <a name="add-a-machine-learning-model-to-your-job"></a>Přidání modelu Machine Learning do úlohy

Do úlohy Stream Analytics můžete přidat funkce Azure Machine Learning přímo z Azure Portal nebo Visual Studio Code.

### <a name="azure-portal"></a>portál Azure

1. V Azure Portal přejděte na svou Stream Analytics úlohu a v části **topologie úlohy**vyberte **funkce** . Pak z rozevírací nabídky **+ Přidat** vyberte **Azure Machine Learning Service** .

   ![Přidat Azure Machine Learning systému souborů UDF](./media/machine-learning-udf/add-azure-machine-learning-udf.png)

2. Do formuláře **funkce služby Azure Machine Learning** zadejte následující hodnoty vlastností:

   ![Konfigurace Azure Machine Learning systému souborů UDF](./media/machine-learning-udf/configure-azure-machine-learning-udf.png)

### <a name="visual-studio-code"></a>Visual Studio Code

1. Otevřete projekt Stream Analytics v Visual Studio Code a klikněte pravým tlačítkem na složku **Functions** . Pak zvolte **Přidat funkci**. V rozevíracím seznamu vyberte **Machine Learning UDF** .

   :::image type="content" source="media/machine-learning-udf/visual-studio-code-machine-learning-udf-add-function.png" alt-text="Přidat UDF do VS Code":::

   :::image type="content" source="media/machine-learning-udf/visual-studio-code-machine-learning-udf-add-function-2.png" alt-text="Přidat Azure Machine Learning UDF v VS Code":::

2. Zadejte název funkce a vyplňte nastavení v konfiguračním souboru pomocí **možnosti vybrat z předplatných** v CodeLens.

   :::image type="content" source="media/machine-learning-udf/visual-studio-code-machine-learning-udf-function-name.png" alt-text="Vyberte Azure Machine Learning UDF v VS Code":::

   :::image type="content" source="media/machine-learning-udf/visual-studio-code-machine-learning-udf-configure-settings.png" alt-text="Konfigurace Azure Machine Learning systému souborů UDF v VS Code":::

V následující tabulce jsou popsány jednotlivé vlastnosti služby Azure Machine Learning Service Functions v Stream Analytics.

|Vlastnost|Popis|
|--------|-----------|
|Alias funkce|Zadejte název pro vyvolání funkce v dotazu.|
|Předplatné|Vaše předplatné Azure..|
|Pracovní prostor služby Azure Machine Learning|Azure Machine Learning pracovní prostor, který jste použili k nasazení modelu jako webové služby.|
|Nasazení|Webová služba hostující váš model.|
|Signatura funkce|Signatura webové služby odvozená ze specifikace schématu rozhraní API. Pokud se Váš podpis nepovede načíst, ověřte, že jste ve svém skriptu pro hodnocení zadali vzorový vstup a výstup pro automatické generování schématu.|
|Počet paralelních požadavků na oddíl|Toto je pokročilá konfigurace pro optimalizaci propustnosti ve velkém měřítku. Toto číslo představuje souběžné požadavky odeslané z každého oddílu vaší úlohy do webové služby. Úlohy s šesti jednotkami streamování (SU) a nižší mají jeden oddíl. Úlohy s 12 službami SUs mají dva oddíly, 18 SUs mají tři oddíly a tak dále.<br><br> Pokud má vaše úloha například dva oddíly a nastavíte tento parametr na čtyři, bude z vaší úlohy pro vaši webovou službu osm souběžných požadavků. V současnosti ve verzi Public Preview je tato hodnota standardně 20 a nelze ji aktualizovat.|
|Maximální počet dávek|Toto je pokročilá konfigurace pro optimalizaci vysoce škálovatelné propustnosti. Toto číslo představuje maximální počet událostí dávkování v jedné žádosti odeslané webové službě.|

## <a name="supported-input-parameters"></a>Podporované vstupní parametry

Když váš Stream Analytics dotaz vyvolá Azure Machine Learning UDF, úloha vytvoří pro webovou službu serializovanou žádost JSON. Požadavek je založen na schématu specifickém pro model. Pro [Automatické generování schématu](../machine-learning/how-to-deploy-and-where.md)musíte zadat vzorový vstup a výstup do skriptu hodnocení. Schéma umožňuje Stream Analytics vytvořit serializovanou žádost JSON pro jakýkoli z podporovaných datových typů, jako je například numpy, PANDAS a PySpark. V jednom požadavku lze dávkovat více vstupních událostí.

Následující Stream Analytics dotaz představuje příklad, jak vyvolat Azure Machine Learning systému souborů UDF:

```SQL
SELECT udf.score(<model-specific-data-structure>)
INTO output
FROM input
```

Stream Analytics podporuje pouze předávání jednoho parametru pro funkce Azure Machine Learning. Možná budete muset připravit vaše data, než je předáte do strojového učení UDF.

## <a name="pass-multiple-input-parameters-to-the-udf"></a>Předání více vstupních parametrů do systému souborů UDF

Nejběžnějšími příklady vstupů do modelů strojového učení jsou numpy pole a dataframes. Můžete vytvořit pole pomocí jazyka JavaScript UDF a vytvořit pomocí klauzule datový rámec serializovaný ve formátu JSON `WITH` .

### <a name="create-an-input-array"></a>Vytvoření vstupního pole

Můžete vytvořit jazyk JavaScript UDF, který přijímá *N* počet vstupů, a vytvoří pole, které lze použít jako vstup do Azure Machine Learning UDF.

```javascript
function createArray(vendorid, weekday, pickuphour, passenger, distance) {
    'use strict';
    var array = [vendorid, weekday, pickuphour, passenger, distance]
    return array;
}
```

Po přidání JavaScriptu pro systém souborů UDF do úlohy můžete Azure Machine Learning UDF vyvolat pomocí následujícího dotazu:

```SQL
SELECT udf.score(
udf.createArray(vendorid, weekday, pickuphour, passenger, distance)
)
INTO output
FROM input
```

Následující kód JSON je příkladem požadavku:

```JSON
{
    "data": [
        ["1","Mon","12","1","5.8"],
        ["2","Wed","10","2","10"]
    ]
}
```

### <a name="create-a-pandas-or-pyspark-dataframe"></a>Vytvoření datového rámce PANDAS nebo PySpark

Pomocí `WITH` klauzule můžete vytvořit serializovaný datový rámec JSON, který lze předat jako vstup do Azure Machine Learning UDF, jak je znázorněno níže.

Následující dotaz vytvoří datový rámec výběrem nezbytných polí a použitím datového rámce jako vstupu do Azure Machine Learning UDF.

```SQL
WITH 
Dataframe AS (
SELECT vendorid, weekday, pickuphour, passenger, distance
FROM input
)

SELECT udf.score(Dataframe)
INTO output
FROM Dataframe
```

Následující kód JSON je příkladem požadavku z předchozího dotazu:

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

## <a name="optimize-the-performance-for-azure-machine-learning-udfs"></a>Optimalizujte výkon pro Azure Machine Learning UDF

Při nasazení modelu do služby Azure Kubernetes můžete [profilovat model, abyste zjistili využití prostředků](../machine-learning/how-to-deploy-profile-model.md). Službu [Application Insights pro vaše nasazení](../machine-learning/how-to-enable-app-insights.md) taky můžete povolit pro pochopení sazeb požadavků, dob odezvy a sazeb selhání.

Pokud máte scénář s vysokou propustností událostí, možná budete muset změnit následující parametry v Stream Analytics, abyste dosáhli optimálního výkonu s nižšími koncovými latencemi:

1. Maximální počet dávek
2. Počet paralelních požadavků na oddíl

### <a name="determine-the-right-batch-size"></a>Určení velikosti správné dávky

Po nasazení webové služby odešlete vzorový požadavek s proměnlivými velikostmi dávek od 50 a zvýšíte ho v řádu stovky. Například 200, 500, 1000, 2000 a tak dále. Všimnete si, že po určité velikosti dávky se zvyšuje latence odezvy. Bod, po kterém se zvýší latence odezvy, by měl být maximálním počtem dávek pro vaši úlohu.

### <a name="determine-the-number-of-parallel-requests-per-partition"></a>Určení počtu paralelních požadavků na oddíl

U optimálního škálování by vaše Stream Analytics úloha měla být schopná odesílat více paralelních požadavků webové službě a získat odpověď během několika milisekund. Latence odpovědi webové služby může mít přímý vliv na latenci a výkon vaší Stream Analytics úlohy. Pokud volání z vaší úlohy do webové služby trvá dlouhou dobu, pravděpodobně se zobrazí zvýšení prodlevy vodoznaku a může se zobrazit také zvýšení počtu nevyřízených událostí vstupu.

Aby se zabránilo takové latenci, ujistěte se, že se cluster služby Azure Kubernetes (AKS) zřídil se [správným počtem uzlů a replik](../machine-learning/how-to-deploy-azure-kubernetes-service.md#using-the-cli). Je důležité, aby byla vaše webová služba vysoce dostupná a vracela úspěšné odpovědi. Pokud vaše úloha obdrží z webové služby odpověď nedostupnou od služby (503), bude se průběžně opakovat s exponenciálním přechodem. Jakákoli jiná odpověď než úspěch (200) a nedostupná služba (503) způsobí, že vaše úloha přejde do stavu selhání.

## <a name="next-steps"></a>Další kroky

* [Kurz: Uživatelem definované funkce jazyka JavaScript v Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Škálování Stream Analytics úlohy pomocí funkce Azure Machine Learning Studio (Classic)](stream-analytics-scale-with-machine-learning-functions.md)
