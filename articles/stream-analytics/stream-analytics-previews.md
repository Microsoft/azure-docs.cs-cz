---
title: Funkce Azure Stream Analytics ve verzi preview
description: Tento článek obsahuje seznam funkcí Azure Stream Analytics, které jsou aktuálně ve verzi preview.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2019
ms.openlocfilehash: f3838bf6b9f7daa24c0cdb5b0c5a08d41d164530
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/13/2019
ms.locfileid: "65561138"
---
# <a name="azure-stream-analytics-preview-features"></a>Funkce Azure Stream Analytics ve verzi preview

Tento článek shrnuje všechny funkce aktuálně ve verzi preview pro Azure Stream Analytics. Pomocí funkce ve verzi preview v produkčním prostředí se nedoporučuje.

## <a name="public-previews"></a>Verze Public Preview

Následující funkce jsou ve verzi public preview. Můžete využít výhod těchto funkcích ještě dnes, ale nemusíte používat v produkčním prostředí.

### <a name="visual-studio-code-for-azure-stream-analytics-released-may-2019"></a>Visual Studio Code pro Azure Stream Analytics (všeobecně dostupné. května 2019)

Úlohy Azure Stream Analytics se můžou vytvořit ve Visual Studio Code. Najdete v našich [VS Code úvodní kurz](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code).

### <a name="anomaly-detection"></a>Detekce anomálií

Azure Stream Analytics zavádí nové modely strojového učení s podporou *zásobníku* a *poklesy* detekce kromě detekce obousměrný, pomalé kladné a pomalé negativních trendů. Další informace najdete v článku [detekce anomálií ve službě Azure Stream Analytics](stream-analytics-machine-learning-anomaly-detection.md).

### <a name="sql-database-reference-data"></a>SQL Database referenčních dat

Azure Stream Analytics podporuje Azure SQL Database jako zdroj vstupu pro referenční data. SQL Database můžete použít jako referenční data pro vaši úlohu Stream Analytics na portálu Azure portal a v sadě Visual Studio pomocí nástroje Stream Analytics. Další informace najdete, [pomocí referenčních dat z databáze serveru SQL pro úlohy Azure Stream Analytics](sql-reference-data.md).

### <a name="integration-with-azure-machine-learning"></a>Integrace s Azure Machine Learning

Je možné škálovat úlohy Stream Analytics s funkcemi Machine Learning (ML). Další informace o použití funkce ML v úloze Stream Analytics, najdete v tématu [škálovat úlohy Stream Analytics s funkcemi Azure Machine Learning](stream-analytics-scale-with-machine-learning-functions.md). Podívejte se na skutečném scénáři s [provádět analýzu subjektivního hodnocení s využitím Azure Stream Analytics a Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).

### <a name="javascript-user-defined-aggregate"></a>Uživatelem definovaná agregace jazyka JavaScript

Azure Stream Analytics podporuje uživatelem definované agregace (UDA) napsané v jazyce JavaScript, které vám umožní implementovat komplexní stavové obchodní logiku. Další informace o použití UDA z [jazyka JavaScript v Azure Stream Analytics uživatelem definovaných agregacích](stream-analytics-javascript-user-defined-aggregates.md) dokumentaci. 

### <a name="live-data-testing-in-visual-studio"></a>Živé testování data v sadě Visual Studio

Visual Studio tools pro Azure Stream Analytics rozšířit místní testování funkce, která využijete k otestování dotazů vůči datové proudy živou událost z cloudových zdrojů, jako je například Event Hubu nebo služby IoT hub. Zjistěte, jak [živá data místně pomocí nástroje Azure Stream Analytics pro Visual Studio Test](stream-analytics-live-data-local-testing.md).

### <a name="net-user-defined-functions-on-iot-edge"></a>.NET uživatelsky definovaných funkcí na hraničních zařízeních IoT

Pomocí .NET standard uživatelem definované funkce můžete spustit .NET Standard kódu jako součást vašeho streamovacího kanálu. Můžete vytvořit jednoduché třídy jazyka C# nebo import projektů a knihovny. Úplné pro vytváření a ladění je podporováno v sadě Visual Studio. Další informace najdete v článku [vývoj .NET Standard uživatelsky definovaných funkcí pro úlohy Azure Stream Analytics Edge](stream-analytics-edge-csharp-udf-methods.md).

## <a name="other-previews"></a>Jiné verze Preview

Následující funkce jsou také k dispozici ve verzi preview.

### <a name="c-custom-deserializer-for-azure-stream-analytics-on-iot-edge-and-cloud-announced-may-2019"></a>C#vlastní deserializátor pro Azure Stream Analytics na hraničních zařízeních IoT a cloudem (Announced. května 2019)

Vývojáři můžou implementovat vlastní deserializers v C# k deserializaci událostí přijatých službou Azure Stream Analytics. Příklady formátů, které lze deserializovat: Parquet, Protobuf, XML nebo libovolný binární formát. Zaregistrovat v této verzi preview [tady](https://aka.ms/asapreview1).

### <a name="parquet-output-announced-may-2019"></a>Výstup parquet (oznámili. května 2019)
Parquet je sloupcovém formátu, která umožňuje efektivní zpracování velkých objemů dat. Ve výstupu data ve formátu Parquet ve službě data lake, můžete využít Azure Stream Analytics streaming ETL velkého rozsahu power a spustit dávkové zpracování, trénování algoritmů strojového učení nebo spouštění interaktivních dotazů na historická data. Zaregistrovat v této verzi preview [tady](https://aka.ms/asapreview1).

### <a name="one-click-integration-with-event-hubs-announced-may-2019"></a>Jedním kliknutím integraci s Event Hubs (Announced. května 2019) 
Tato integrace teď bude možné vizualizaci příchozích dat a začít psát dotazu Stream Analytics s jedním kliknutím na portálu centra událostí. Když váš dotaz je připravená, budou moct productize několika kliknutími a začít získat přehledy v reálném čase. Tím se výrazně snížit čas a náklady na vývoj řešení analýz v reálném čase. Zaregistrovat v této verzi preview [tady](https://aka.ms/asapreview1).

### <a name="support-for-azure-stack-announced-may-2019"></a>Podpora pro Azure Stack (oznámili. května 2019)
Tato funkce povolena na modul runtime Azure IoT Edge, využívá vlastní funkce služby Azure Stack, jako je nativní podporu pro místní vstupy a výstupy spuštěná ve službě Azure Stack (například služby Event Hubs, služby IoT Hub, Blob Storage). Tato nová integrace vám umožní sestavovat hybridní architektury, které můžete analyzovat vaše data co nejblíž místu jejího vygenerování, což snižuje latenci a maximalizovat insights.
Zaregistrovat v této verzi preview [tady](https://aka.ms/asapreview1).

