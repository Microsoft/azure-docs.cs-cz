---
title: Funkce Azure Stream Analytics ve verzi preview
description: Tento článek obsahuje seznam funkcí Azure Stream Analytics, které jsou aktuálně ve verzi preview.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 3e99263f6bf472c256e1747b8567249bbd62a445
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2020
ms.locfileid: "78969621"
---
# <a name="azure-stream-analytics-preview-features"></a>Funkce Azure Stream Analytics ve verzi preview

Tento článek shrnuje všechny funkce aktuálně ve verzi preview pro Azure Stream Analytics. Pomocí funkce ve verzi preview v produkčním prostředí se nedoporučuje.

## <a name="public-previews"></a>Verze Public Preview

Následující funkce jsou ve verzi public preview. Můžete využít výhod těchto funkcích ještě dnes, ale nemusíte používat v produkčním prostředí.

### <a name="online-scaling"></a>Škálování online

Díky škálování online nemusíte úlohu zastavovat, pokud potřebujete změnit přidělení SU. Kapacitu SU spuštěné úlohy můžete zvýšit nebo snížit bez nutnosti jejich zastavení. Tím se stavíte na zákaznických proslibech dlouhotrvajících kanálů důležitých z hlediska provozu, které dnes Stream Analytics nabídky. Další informace najdete v tématu [konfigurace Azure Stream Analytics jednotek streamování](stream-analytics-streaming-unit-consumption.md#configure-stream-analytics-streaming-units-sus).

### <a name="c-custom-de-serializers"></a>C#vlastní de-serializátory
Vývojáři mohou využít sílu Azure Stream Analytics ke zpracování dat v Protobuf, XML nebo jakémkoli vlastním formátu. Můžete implementovat [vlastní deserializátory](custom-deserializer-examples.md) v C#, které lze poté použít k deserializaci událostí přijatých Azure Stream Analytics.

### <a name="extensibility-with-c-custom-code"></a>Rozšiřitelnost C# pomocí vlastního kódu

Vývojáři vytvářející Stream Analytics moduly v cloudu nebo na IoT Edge můžou zapisovat nebo znovu používat C# vlastní funkce a vyvolat je přímo v dotazu prostřednictvím [uživatelsky definovaných funkcí](stream-analytics-edge-csharp-udf-methods.md).


### <a name="debug-query-steps-in-visual-studio"></a>Postup ladění dotazů v aplikaci Visual Studio

Při místním testování v Azure Stream Analyticsch nástrojích pro sadu Visual Studio můžete snadno zobrazit náhled sady pokročilých řádků v datovém diagramu. 

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>Místní testování s dynamickými daty v Visual Studio Code

Před odesláním úlohy do Azure můžete testovat své dotazy proti živým datům na místním počítači. Každá iterace testování trvá v průměru méně než dvě až tři sekundy, což vede k velmi efektivnímu procesu vývoje.

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio Code pro Azure Stream Analytics

Azure Stream Analytics úlohy lze vytvořit v Visual Studio Code. Podívejte se na náš úvodní [kurz vs Code](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code).


### <a name="integration-with-azure-machine-learning"></a>Integrace s Azure Machine Learning

Je možné škálovat úlohy Stream Analytics s funkcemi Machine Learning (ML). Další informace o tom, jak můžete používat funkce ML v úloze Stream Analytics, najdete v tématu [škálování úlohy Stream Analytics pomocí Azure Machine Learning funkcí](stream-analytics-scale-with-machine-learning-functions.md). Projděte si reálný scénář s [prováděním analýz mínění pomocí Azure Stream Analytics a Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).


### <a name="live-data-testing-in-visual-studio"></a>Živé testování data v sadě Visual Studio

Visual Studio tools pro Azure Stream Analytics rozšířit místní testování funkce, která využijete k otestování dotazů vůči datové proudy živou událost z cloudových zdrojů, jako je například Event Hubu nebo služby IoT hub. Přečtěte si, jak [místně testovat živá data pomocí Azure Stream Analyticsch nástrojů pro Visual Studio](stream-analytics-live-data-local-testing.md).


### <a name="net-user-defined-functions-on-iot-edge"></a>.NET uživatelsky definovaných funkcí na hraničních zařízeních IoT

Pomocí .NET standard uživatelem definované funkce můžete spustit .NET Standard kódu jako součást vašeho streamovacího kanálu. Můžete vytvořit jednoduché třídy jazyka C# nebo import projektů a knihovny. Úplné pro vytváření a ladění je podporováno v sadě Visual Studio. Další informace najdete na webu [vývoj .NET Standard uživatelsky definovaných funkcí pro úlohy Azure Stream Analytics Edge](stream-analytics-edge-csharp-udf-methods.md).

## <a name="other-previews"></a>Další verze Preview

Následující funkce jsou k dispozici také ve verzi Preview na vyžádání.

### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Bodování vysokého výkonu v reálném čase s vlastními modely ML spravovanými nástrojem Azure Machine Learning

Azure Stream Analytics podporuje vysoce výkonné bodování v reálném čase, protože využívá předem připravené Machine Learning modely, které spravuje Azure Machine Learning a jsou hostované ve službě Azure Kubernetes Service (AKS) nebo Azure Container Instances (ACI), pomocí pracovního postupu. To nevyžaduje psaní kódu. [Zaregistrujte](https://aka.ms/asapreview1) se do verze Preview

### <a name="support-for-azure-stack"></a>Podpora Azure Stack
Tato funkce povolená na Azure IoT Edge runtime využívá vlastní Azure Stack funkce, jako je například nativní podpora místních vstupů a výstupů spuštěných v Azure Stack (například Event Hubs IoT Hub Blob Storage,). Tato nová integrace vám umožní sestavovat hybridní architektury, které mohou analyzovat data blízko místa, kde jsou vygenerována, snížit latenci a maximalizovat přehledy.
Tato funkce umožňuje vytvářet hybridní architektury, které mohou analyzovat data blízko místa, kde jsou vygenerována, snížit latenci a maximalizovat přehledy. Musíte se [zaregistrovat](https://aka.ms/asapreview1) v této verzi Preview.
