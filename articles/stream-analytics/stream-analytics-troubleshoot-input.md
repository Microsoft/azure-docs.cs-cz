---
title: Řešení potíží se vstupy pro Azure Stream Analytics
description: Tento článek popisuje techniky řešení potíží se vstupními připojeními v Azure Stream Analytics úlohách.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 05/01/2020
ms.custom: seodec18
ms.openlocfilehash: 2d7171c9ec1e60447fb3342caa72098fb2eb9337
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019903"
---
# <a name="troubleshoot-input-connections"></a>Řešení potíží se vstupními připojeními

Tento článek popisuje běžné problémy s Azure Stream Analytics vstupními připojeními, postupy při řešení problémů se vstupem a jak tyto problémy vyřešit. Mnoho kroků pro řešení potíží vyžaduje, aby byly pro vaši úlohu Stream Analytics Povolené protokoly prostředků. Pokud nemáte Povolené protokoly prostředků, přečtěte si téma [řešení potíží s Azure Stream Analytics pomocí protokolů prostředků](stream-analytics-job-diagnostic-logs.md).

## <a name="input-events-not-received-by-job"></a>Úloha nepřijala vstupní události. 

1.  Otestujte vstupní a výstupní připojení. Ověřte připojení ke vstupům a výstupům pomocí tlačítka **Testovat připojení** pro jednotlivé vstupy a výstupy.

2.  Prověřte vstupní data.

    1. Pro jednotlivé vstupy použijte tlačítko [**ukázková data**](./stream-analytics-test-query.md) . Stáhněte si vstupní ukázková data.
        
    1. Zkontrolujte ukázková data, abyste porozuměli schématu a [datovým typům](/stream-analytics-query/data-types-azure-stream-analytics).
    
    1. Zkontrolujte [metriky centra událostí](../event-hubs/event-hubs-metrics-azure-monitor.md) a ujistěte se, že jsou události odesílány. Pokud Event Hubs přijímá zprávy, musí být metrika zpráv větší než nula.

3.  Ujistěte se, že jste ve vstupní verzi Preview vybrali časový rozsah. Zvolte **možnost vybrat časový rozsah** a pak před testováním dotazu zadejte dobu trvání vzorku.

## <a name="malformed-input-events-causes-deserialization-errors"></a>Poškozená vstupní událost způsobuje chyby deserializace 

Problémy s deserializací jsou způsobeny tím, že vstupní datový proud vaší Stream Analytics úlohy obsahuje poškozené zprávy. Poškozená zpráva může být například způsobena chybějícími závorkami nebo závorkami v objektu JSON nebo nesprávným formátem časového razítka v poli čas. 
 
Když úloha Stream Analytics obdrží ze vstupu poškozenou zprávu, zpráva se uvolní a upozorní vás upozorněním. Na dlaždici **vstupy** Stream Analytics úlohy se zobrazí výstražný symbol. Následující symbol upozornění existuje, dokud je úloha ve stavu spuštěno:

![Dlaždice Azure Stream Analytics vstupy](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Povolte protokoly prostředků, abyste zobrazili podrobnosti o chybě a zprávě (datové části), která způsobila chybu. Existuje několik důvodů, proč může docházet k chybám deserializace. Další informace o konkrétních chybách deserializace najdete v tématu [chyby vstupních dat](data-errors.md#input-data-errors). Pokud nejsou povoleny protokoly prostředků, bude v Azure Portal k dispozici krátké oznámení.

![Upozornění na zadání podrobností o vstupu](media/stream-analytics-malformed-events/warning-message-with-offset.png)

V případech, kdy je datová část zprávy větší než 32 KB nebo v binárním formátu, spusťte kód CheckMalformedEvents. cs dostupný v [úložišti ukázek GitHubu](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Tento kód přečte ID oddílu, posun a vytiskne data, která jsou umístěna v tomto posunu. 

## <a name="job-exceeds-maximum-event-hub-receivers"></a>Úloha překročila maximální počet přijímačů centra událostí.

Osvědčeným postupem použití Event Hubs je použití více skupin uživatelů pro škálovatelnost úloh. Počet čtenářů v Stream Analytics úlohy pro konkrétní vstup ovlivňuje počet čtenářů v jedné skupině příjemců. Přesný počet přijímačů je založený na podrobnostech o interní implementaci logiky topologie se škálováním na více instancí a nezveřejňuje se externě. Počet čtenářů se může změnit při spuštění úlohy nebo během upgradování úlohy.

Pokud počet přijímačů překročí maximum, zobrazí se následující chybové zprávy. Chybová zpráva obsahuje seznam existujících připojení provedených do centra událostí v rámci skupiny příjemců. Tato značka `AzureStreamAnalytics` označuje, že připojení ze služby Azure Streaming Service.

```
The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.

The following information may be helpful in identifying the connected receivers: Exceeded the maximum number of allowed receivers per partition in a consumer group which is 5. List of connected receivers – 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1.
```

> [!NOTE]
> Když se během upgradu úlohy změní počet čtenářů, do protokolů auditu se zapisují přechodná upozornění. Stream Analytics úlohy se automaticky Obnovují z těchto přechodných problémů.

### <a name="add-a-consumer-group-in-event-hubs"></a>Přidat skupinu příjemců v Event Hubs

Pokud chcete do instance Event Hubs přidat novou skupinu příjemců, postupujte takto:

1. Přihlaste se k webu Azure Portal.

2. Vyhledejte centrum událostí.

3. V záhlaví **entity** vyberte **Event Hubs** .

4. Vyberte centrum událostí podle názvu.

5. Na stránce **Event Hubs instance** pod nadpisem **entity** vyberte **skupiny uživatelů**. V seznamu je uvedena skupina uživatelů s názvem **$Default** .

6. Vyberte **+ Skupina uživatelů** a přidejte novou skupinu příjemců. 

   ![Přidat skupinu příjemců v Event Hubs](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. Když jste vytvořili vstup v úloze Stream Analytics, abyste odkazovali na centrum událostí, zadali jste skupinu příjemců. **$Default** se používá v případě, že není zadán parametr None. Jakmile vytvoříte novou skupinu příjemců, upravte vstup centra událostí v úloze Stream Analytics a zadejte název nové skupiny příjemců.

## <a name="readers-per-partition-exceeds-event-hubs-limit"></a>Počet čtenářů na oddíl překračuje limit Event Hubs.

Pokud syntaxe dotazu streamování odkazuje na stejný prostředek centra událostí vstupu víckrát, může modul úloh použít více čtenářů na dotaz ze stejné skupiny příjemců. Pokud existuje příliš mnoho odkazů na stejnou skupinu uživatelů, může úloha překročit limit pět a vyvolala chybu. V těchto případech můžete dál dělit pomocí více vstupů napříč více skupinami příjemců pomocí řešení popsaného v následující části. 

Scénáře, ve kterých počet čtenářů na oddíl přesáhne Event Hubs limitu pěti, zahrnují následující:

* Vícenásobné příkazy SELECT: Pokud použijete více příkazů SELECT, které odkazují na **stejný** vstup centra událostí, každý příkaz SELECT způsobí vytvoření nového příjemce.

* SJEDNOCENí: když použijete SJEDNOCENí, je možné mít několik vstupů, které odkazují na **stejné** centrum událostí a skupinu uživatelů.

* Připojovat se k sobě: když použijete operaci připojovat do sebe, je možné, že se na **stejné** centrum událostí odkazuje víckrát.

Následující osvědčené postupy mohou přispět ke zmírnění scénářů, ve kterých počet čtenářů na oddíl překračuje limit Event Hubs 5.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>Rozdělit dotaz do více kroků pomocí klauzule WITH

Klauzule WITH určuje dočasnou pojmenovanou sadu výsledků, na kterou může odkazovat klauzule FROM v dotazu. Definujte klauzuli WITH v oboru provádění jednoho příkazu SELECT.

Například místo tohoto dotazu:

```SQL
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

Použijte tento dotaz:

```SQL
WITH data AS (
   SELECT * FROM inputEventHub
)

SELECT foo
INTO output1
FROM data

SELECT bar
INTO output2
FROM data
…
```

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>Zajistěte, aby vstupy byly vázány na různé skupiny příjemců.

Pro dotazy, ve kterých jsou tři nebo více vstupů připojeni ke stejné Event Hubs skupině uživatelů, vytvořte samostatné skupiny příjemců. To vyžaduje vytvoření dalších vstupů Stream Analytics.

### <a name="create-separate-inputs-with-different-consumer-groups"></a>Vytvoření různých vstupů s různými skupinami příjemců

Pro stejné centrum událostí můžete vytvořit samostatné vstupy s různými skupinami uživatelů. Následující sjednocovací dotaz je příkladem, kde *InputOne* a *InputTwo* odkazují na stejný zdroj centra událostí. Každý dotaz může mít samostatné vstupy s různými skupinami uživatelů. SJEDNOCOVACÍ dotaz je pouze jeden příklad.

```sql
WITH 
DataOne AS 
(
SELECT * FROM InputOne 
),

DataTwo AS 
(
SELECT * FROM InputTwo 
),

SELECT foo FROM DataOne
UNION 
SELECT foo FROM DataTwo

```

## <a name="readers-per-partition-exceeds-iot-hub-limit"></a>Počet čtenářů na oddíl překračuje limit IoT Hub.

Úlohy Stream Analytics používají integrovaný [koncový bod kompatibilní](../iot-hub/iot-hub-devguide-messages-read-builtin.md) s centrem událostí IoT Hub k připojení a čtení událostí z IoT Hub. Pokud vaše čtení na oddíl překročí limity IoT Hub, můžete ho vyřešit pomocí [řešení pro centrum událostí](#readers-per-partition-exceeds-event-hubs-limit) . Můžete vytvořit skupinu příjemců pro integrovaný koncový bod prostřednictvím relace koncového bodu portálu IoT Hub nebo prostřednictvím [sady IoT Hub SDK](/rest/api/iothub/IotHubResource/CreateEventHubConsumerGroup).

## <a name="get-help"></a>Získání pomoci

Pokud chcete získat další pomoc, vyzkoušejte si naši [stránku Microsoft Q&Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Další kroky

* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](/rest/api/streamanalytics/)