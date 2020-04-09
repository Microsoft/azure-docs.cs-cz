---
title: Funkce preview Azure Stream Analytics
description: V tomto článku jsou uvedeny funkce Azure Stream Analytics, které jsou aktuálně ve verzi Preview.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 4c265665be26dcc6868ea9a303b0c12c52dfe05b
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878302"
---
# <a name="azure-stream-analytics-preview-features"></a>Funkce preview Azure Stream Analytics

Tento článek shrnuje všechny funkce, které jsou aktuálně ve verzi Preview pro Azure Stream Analytics. Použití funkcí náhledu v produkčním prostředí se nedoporučuje.

## <a name="public-previews"></a>Public Preview

Následující funkce jsou ve verzi Public Preview. Tyto funkce můžete využít ještě dnes, ale nepoužívejte je ve vašem produkčním prostředí.

### <a name="online-scaling"></a>Online škálování

S online škálování, není nutné zastavit svou úlohu, pokud potřebujete změnit přidělení SU. Můžete zvýšit nebo snížit kapacitu SU spuštěné úlohy, aniž byste ji museli zastavit. To vychází z příslibu zákazníků dlouhotrvajících klíčových kanálů, které stream analytics dnes nabízí. Další informace najdete [v tématu Konfigurace jednotek streamování Azure Stream Analytics](stream-analytics-streaming-unit-consumption.md#configure-stream-analytics-streaming-units-sus).

### <a name="c-custom-de-serializers"></a>Vlastní deserializátory jazyka C#
Vývojáři můžou využít výkon Azure Stream Analytics ke zpracování dat v Protobufu, XML nebo v libovolném vlastním formátu. Můžete implementovat [vlastní deserializátory](custom-deserializer-examples.md) v C#, které pak můžete použít k deserializaci událostí přijatých službou Azure Stream Analytics.

### <a name="extensibility-with-c-custom-code"></a>Rozšiřitelnost s vlastním kódem Jazyka C#

Vývojáři, kteří vytvářejí moduly Stream Analytics v cloudu nebo na IoT Edge, můžou psát nebo znovu používat vlastní funkce Jazyka C# a vyvolat je přímo v dotazu prostřednictvím [uživatelem definovaných funkcí](stream-analytics-edge-csharp-udf-methods.md).


### <a name="debug-query-steps-in-visual-studio"></a>Kroky ladění dotazu v sadě Visual Studio

Můžete snadno zobrazit náhled zprostředkující řádek nastavit na datový diagram při provádění místní testování v Azure Stream Analytics nástroje pro Visual Studio. 

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>Místní testování s živými daty v kódu sady Visual Studio

Před odesláním úlohy do Azure můžete otestovat dotazy na živá data v místním počítači. Každá testovací iterace trvá v průměru méně než dvě až tři sekundy, což má za následek velmi efektivní proces vývoje.

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Kód Visual Studia pro Azure Stream Analytics

Úlohy Azure Stream Analytics můžou být vytvořené v kódu Visual Studia. Podívejte se na náš [VS Kód začínáme tutorial](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code).


### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Vysoké hodnocení v reálném čase s vlastními modely ML spravovanými azure machine learningem

Azure Stream Analytics podporuje vysoce výkonné vyhodnocování v reálném čase využitím vlastních předem trénovaných modelů machine learningu spravovaných Azure Machine Learning a hostovaných ve službě Azure Kubernetes Service (AKS) nebo Azure Container Instances (ACI) pomocí pracovního postupu, který nevyžaduje psaní kódu. [Zaregistrujte se k](https://aka.ms/asapreview1) náhledu


### <a name="live-data-testing-in-visual-studio"></a>Testování živých dat v sadě Visual Studio

Nástroje Visual Studia pro Azure Stream Analytics vylepšují funkci místního testování, která umožňuje testovat dotazy proti streamům živých událostí z cloudových zdrojů, jako je Centrum událostí nebo Služba IoT hub. Přečtěte si, jak [testovat živá data místně pomocí nástrojů Azure Stream Analytics pro Visual Studio](stream-analytics-live-data-local-testing.md).


### <a name="net-user-defined-functions-on-iot-edge"></a>Uživatelem definované funkce rozhraní .NET na okraji IoT

Pomocí standardních uživatelem definovaných funkcí rozhraní .NET můžete spustit kód .NET Standard jako součást kanálu streamování. Můžete vytvořit jednoduché třídy Jazyka C# nebo importovat celý projekt a knihovny. Úplné vytváření a ladění prostředí je podporována v sadě Visual Studio. Další informace najdete v článku [Vývoj uživatelem definovaných funkcí standardu .NET Pro úlohy Azure Stream Analytics Edge](stream-analytics-edge-csharp-udf-methods.md).

## <a name="other-previews"></a>Další náhledy

Následující funkce jsou také k dispozici ve verzi preview na vyžádání.

### <a name="support-for-azure-stack"></a>Podpora pro Azure Stack
Tato funkce povolená v runtime Azure IoT Edge využívá vlastní funkce Azure Stacku, jako je nativní podpora místních vstupů a výstupů spuštěných ve službě Azure Stack (například Event Hubs, IoT Hub, Blob Storage). Tato nová integrace umožňuje vytvářet hybridní architektury, které mohou analyzovat vaše data v blízkosti místa, kde jsou generována, snižuje latenci a maximalizuje přehledy.
Tato funkce umožňuje vytvářet hybridní architektury, které mohou analyzovat data v blízkosti místa, kde jsou generována, snižuje latenci a maximalizuje přehledy. Do této verze preview se musíte [zaregistrovat.](https://aka.ms/asapreview1)
