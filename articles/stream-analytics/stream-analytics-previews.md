---
title: Funkce Azure Stream Analytics ve verzi preview
description: Tento článek obsahuje seznam funkcí Azure Stream Analytics, které jsou aktuálně ve verzi preview.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: 587304968cdf3a3763e47b9f8b614fe67aebf534
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798036"
---
# <a name="azure-stream-analytics-preview-features"></a>Funkce Azure Stream Analytics ve verzi preview

Tento článek shrnuje všechny funkce aktuálně ve verzi preview pro Azure Stream Analytics. Pomocí funkce ve verzi preview v produkčním prostředí se nedoporučuje.

## <a name="public-previews"></a>Verze Public Preview

Následující funkce jsou ve verzi public preview. Můžete využít výhod těchto funkcích ještě dnes, ale nemusíte používat v produkčním prostředí.

### <a name="one-click-integration-with-event-hubs"></a>Jedním kliknutím integraci s Event Hubs 
Tato integrace teď bude možné vizualizaci příchozích dat a začít psát dotazu Stream Analytics s jedním kliknutím na portálu centra událostí. Když váš dotaz je připravená, budou moct productize několika kliknutími a začít získat přehledy v reálném čase. Tím se výrazně snížit čas a náklady na vývoj řešení analýz v reálném čase. Dokumentace je k dispozici [tady](https://docs.microsoft.com/azure/event-hubs/process-data-azure-stream-analytics).

### <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio Code pro Azure Stream Analytics

Úlohy Azure Stream Analytics se můžou vytvořit ve Visual Studio Code. Najdete v našich [VS Code úvodní kurz](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code).

### <a name="anomaly-detection"></a>Detekce anomálií

Azure Stream Analytics zavádí nové modely strojového učení s podporou *zásobníku* a *poklesy* detekce kromě detekce obousměrný, pomalé kladné a pomalé negativních trendů. Další informace najdete v článku [detekce anomálií ve službě Azure Stream Analytics](stream-analytics-machine-learning-anomaly-detection.md).

### <a name="integration-with-azure-machine-learning"></a>Integrace s Azure Machine Learning

Je možné škálovat úlohy Stream Analytics s funkcemi Machine Learning (ML). Další informace o použití funkce ML v úloze Stream Analytics, najdete v tématu [škálovat úlohy Stream Analytics s funkcemi Azure Machine Learning](stream-analytics-scale-with-machine-learning-functions.md). Podívejte se na skutečném scénáři s [provádět analýzu subjektivního hodnocení s využitím Azure Stream Analytics a Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).

### <a name="javascript-user-defined-aggregate"></a>Uživatelem definovaná agregace jazyka JavaScript

Azure Stream Analytics podporuje uživatelem definované agregace (UDA) napsané v jazyce JavaScript, které vám umožní implementovat komplexní stavové obchodní logiku. Další informace o použití UDA z [jazyka JavaScript v Azure Stream Analytics uživatelem definovaných agregacích](stream-analytics-javascript-user-defined-aggregates.md) dokumentaci. 

### <a name="live-data-testing-in-visual-studio"></a>Živé testování data v sadě Visual Studio

Visual Studio tools pro Azure Stream Analytics rozšířit místní testování funkce, která využijete k otestování dotazů vůči datové proudy živou událost z cloudových zdrojů, jako je například Event Hubu nebo služby IoT hub. Zjistěte, jak [živá data místně pomocí nástroje Azure Stream Analytics pro Visual Studio Test](stream-analytics-live-data-local-testing.md).

### <a name="net-user-defined-functions-on-iot-edge"></a>.NET uživatelsky definovaných funkcí na hraničních zařízeních IoT

Pomocí .NET standard uživatelem definované funkce můžete spustit .NET Standard kódu jako součást vašeho streamovacího kanálu. Můžete vytvořit jednoduché třídy jazyka C# nebo import projektů a knihovny. Úplné pro vytváření a ladění je podporováno v sadě Visual Studio. Další informace najdete v článku [vývoj .NET Standard uživatelsky definovaných funkcí pro úlohy Azure Stream Analytics Edge](stream-analytics-edge-csharp-udf-methods.md).

## <a name="other-previews"></a>Jiné verze Preview

Následující funkce jsou také k dispozici ve verzi preview na vyžádání.

### <a name="c-custom-deserializer-for-azure-stream-analytics-on-iot-edge-and-cloud"></a>C#vlastní deserializátor pro Azure Stream Analytics na hraničních zařízeních IoT a cloudem

Vývojáři můžou implementovat vlastní deserializers v C# k deserializaci událostí přijatých službou Azure Stream Analytics. Příklady formátů, které lze deserializovat: Parquet, Protobuf, XML nebo libovolný binární formát. Zaregistrovat v této verzi preview [tady](https://aka.ms/asapreview1).

### <a name="support-for-azure-stack"></a>Podpora pro Azure Stack
Tato funkce povolena na modul runtime Azure IoT Edge, využívá vlastní funkce služby Azure Stack, jako je nativní podporu pro místní vstupy a výstupy spuštěná ve službě Azure Stack (například služby Event Hubs, služby IoT Hub, Blob Storage). Tato nová integrace vám umožní sestavovat hybridní architektury, které můžete analyzovat vaše data co nejblíž místu jejího vygenerování, což snižuje latenci a maximalizovat insights.
Zaregistrovat v této verzi preview [tady](https://aka.ms/asapreview1).

