---
title: Funkce ve verzi Preview pro Azure Stream Analytics
description: V tomto článku jsou uvedené Azure Stream Analytics funkce, které jsou momentálně ve verzi Preview.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 4c265665be26dcc6868ea9a303b0c12c52dfe05b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80878302"
---
# <a name="azure-stream-analytics-preview-features"></a>Funkce ve verzi Preview pro Azure Stream Analytics

Tento článek shrnuje všechny funkce, které jsou v současnosti ve verzi Preview, k disAzure Stream Analytics. Používání funkcí verze Preview v produkčním prostředí se nedoporučuje.

## <a name="public-previews"></a>Public Preview

Následující funkce jsou ve verzi Public Preview. Tyto funkce můžete využívat ještě dnes, ale nepoužívejte je v produkčním prostředí.

### <a name="online-scaling"></a>Škálování online

Díky škálování online nemusíte úlohu zastavovat, pokud potřebujete změnit přidělení SU. Kapacitu SU spuštěné úlohy můžete zvýšit nebo snížit bez nutnosti jejich zastavení. Tím se stavíte na zákaznických proslibech dlouhotrvajících kanálů důležitých z hlediska provozu, které dnes Stream Analytics nabídky. Další informace najdete v tématu [konfigurace Azure Stream Analytics jednotek streamování](stream-analytics-streaming-unit-consumption.md#configure-stream-analytics-streaming-units-sus).

### <a name="c-custom-de-serializers"></a>Vlastní de-serializátory v jazyce C#
Vývojáři mohou využít sílu Azure Stream Analytics ke zpracování dat v Protobuf, XML nebo jakémkoli vlastním formátu. Můžete implementovat [vlastní de-serializátory](custom-deserializer-examples.md) v jazyce C#, které lze poté použít k deserializaci událostí přijatých Azure Stream Analytics.

### <a name="extensibility-with-c-custom-code"></a>Rozšiřitelnost pomocí vlastního kódu jazyka C#

Vývojáři vytvářející Stream Analytics moduly v cloudu nebo na IoT Edge můžou zapisovat nebo znovu používat vlastní funkce jazyka C# a vyvolat je přímo v dotazu prostřednictvím [uživatelsky definovaných funkcí](stream-analytics-edge-csharp-udf-methods.md).


### <a name="debug-query-steps-in-visual-studio"></a>Postup ladění dotazů v aplikaci Visual Studio

Při místním testování v Azure Stream Analyticsch nástrojích pro sadu Visual Studio můžete snadno zobrazit náhled sady pokročilých řádků v datovém diagramu. 

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>Místní testování s dynamickými daty v Visual Studio Code

Před odesláním úlohy do Azure můžete testovat své dotazy proti živým datům na místním počítači. Každá iterace testování trvá v průměru méně než dvě až tři sekundy, což vede k velmi efektivnímu procesu vývoje.

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio Code pro Azure Stream Analytics

Azure Stream Analytics úlohy lze vytvořit v Visual Studio Code. Podívejte se na náš úvodní [kurz vs Code](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code).


### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Bodování vysokého výkonu v reálném čase s vlastními modely ML spravovanými nástrojem Azure Machine Learning

Azure Stream Analytics podporuje vysoce výkonné bodování v reálném čase, protože využívá předem připravené Machine Learning modely spravované Azure Machine Learning a hostované ve službě Azure Kubernetes Service (AKS) nebo Azure Container Instances (ACI), a to pomocí pracovního postupu, který nevyžaduje psaní kódu. [Zaregistrujte](https://aka.ms/asapreview1) se do verze Preview


### <a name="live-data-testing-in-visual-studio"></a>Živé testování dat v aplikaci Visual Studio

Nástroje Visual Studio Tools for Azure Stream Analytics zlepšují funkci místního testování, která vám umožní testovat dotazy na proudy událostí živého vysílání z cloudových zdrojů, jako je centrum událostí nebo centrum IoT. Přečtěte si, jak [místně testovat živá data pomocí Azure Stream Analyticsch nástrojů pro Visual Studio](stream-analytics-live-data-local-testing.md).


### <a name="net-user-defined-functions-on-iot-edge"></a>Uživatelsky definované funkce rozhraní .NET v IoT Edge

S uživatelsky definovanými funkcemi .NET Standard můžete spustit kód .NET Standard jako součást kanálu streamování. Můžete vytvářet jednoduché třídy jazyka C# nebo importovat úplný projekt a knihovny. V aplikaci Visual Studio je podporováno úplné prostředí pro vytváření obsahu a ladění. Další informace najdete na webu [vývoj .NET Standard uživatelsky definovaných funkcí pro úlohy Azure Stream Analytics Edge](stream-analytics-edge-csharp-udf-methods.md).

## <a name="other-previews"></a>Další verze Preview

Následující funkce jsou k dispozici také ve verzi Preview na vyžádání.

### <a name="support-for-azure-stack"></a>Podpora Azure Stack
Tato funkce povolená na Azure IoT Edge runtime využívá vlastní Azure Stack funkce, jako je například nativní podpora místních vstupů a výstupů spuštěných v Azure Stack (například Event Hubs IoT Hub Blob Storage,). Tato nová integrace vám umožní sestavovat hybridní architektury, které mohou analyzovat data blízko místa, kde jsou vygenerována, snížit latenci a maximalizovat přehledy.
Tato funkce umožňuje vytvářet hybridní architektury, které mohou analyzovat data blízko místa, kde jsou vygenerována, snížit latenci a maximalizovat přehledy. Musíte se [zaregistrovat](https://aka.ms/asapreview1) v této verzi Preview.
