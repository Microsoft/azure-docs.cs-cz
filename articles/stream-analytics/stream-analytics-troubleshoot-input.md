---
title: Řešení potíží s vstupů pro službu Azure Stream Analytics
description: Tento článek popisuje postupy řešení potíží s vstupní připojení v úlohách Azure Stream Analytics.
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/11/2018
ms.openlocfilehash: 2b2dc3ba78cfa682c4a326754bdddfa9bc81f836
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49346641"
---
# <a name="troubleshoot-input-connections"></a>Řešení potíží s připojeními vstupu

Tato stránka popisuje běžné problémy s vstupní připojení a postupy jejich řešení.

## <a name="input-events-not-received-by-job"></a>Nebyl přijat úlohou vstupní události 
1.  Test připojení. Ověření připojení ke vstupům a výstupům pomocí **Test připojení** tlačítko pro každý vstupní a výstupní.

2.  Zkontrolujte vstupní data.

    Chcete-li ověřit, že se výstupní data přenášejí do centra událostí, použijte [Service Bus Exploreru](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a) pro připojení k Azure Eventhub (Pokud je vstup Centrum událostí se používá).
        
    Použití [ **ukázková Data** ](stream-analytics-sample-data-input.md) pro každý vstupní tlačítko a stáhněte ukázková vstupní data.
        
    Zkontrolujte ukázková data a porozumět struktuře dat: schéma a [datové typy](https://msdn.microsoft.com/library/azure/dn835065.aspx).

## <a name="malformed-input-events-causes-deserialization-errors"></a>Chyby deserializace způsobí, že vstupní události nemají správný formát 
Deserializace potíže jsou způsobeny, když vstupní datový proud vaší úlohy Stream Analytics obsahuje špatně vytvořené zprávy. Například může být způsobeno poškozená zpráva. chybí pravá závorka. nebo závorka v objektu JSON nebo nesprávný časové razítko formátu v poli Doba. 
 
Když úloha Stream Analytics přijímá chybnou zprávu z vstup, zahodí a upozorní uživatele s upozorněním. Symbol upozornění se zobrazí na **vstupy** dlaždici vaší úlohy Stream Analytics. Tento znak upozornění existuje za předpokladu, že je úloha ve spuštěném stavu:

![Dlaždici vstupy Azure Stream Analytics](media/stream-analytics-malformed-events/inputs_tile.png)

Povolte diagnostické protokoly, chcete-li zobrazit podrobnosti upozornění. Pro vstupní události nemají správný formát protokoly spuštění obsahovat zprávu, která bude vypadat takto: 
<code>Could not deserialize the input event(s) from resource <blob URI> as json.</code>

### <a name="what-caused-the-deserialization-error"></a>Tom, co způsobilo chyby deserializace
Můžete využít následující kroky a analyzovat události vstupu podrobně lepší přehled o tom, co způsobilo chyba deserializace. Potom můžete opravit zdroj událostí k vygenerování událostí ve správném formátu a tím vám znemožnit tím tento problém opakujte.

1. Přejděte na vstupní dlaždice a klikněte na symboly upozornění zobrazíte seznam problémů.

2. Podrobnosti vstupu dlaždici se zobrazuje seznam upozornění s podrobnostmi o jednotlivých problémů. Upozornění následující příklad obsahuje oddíl, posun a pořadová čísla níž se nachází poškozená data JSON. 

   ![Upozornění s posunem](media/stream-analytics-malformed-events/warning_message_with_offset.png)

3. Vyhledat potřebná data JSON s nesprávný formát, spusťte CheckMalformedEvents.cs kód, který je k dispozici v [úložiště ukázek Githubu](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Tento kód čte ID oddílu, posun a vytiskne data, která se nachází v tento posun. 

4. Jakmile načtete data, můžete analyzovat a opravit formát serializace.

5. Můžete také [číst události ze služby IoT Hub pomocí Průzkumníka Service Bus](https://code.msdn.microsoft.com/How-to-read-events-from-an-1641eb1b).

## <a name="job-exceeds-maximum-event-hub-receivers"></a>Úloha překračuje maximální přijímače centra událostí
Doporučený postup pro použití služby Event Hubs je použít víc skupin konzumentů zajistit škálovatelnost úloh. Počet čtenářů v rámci úlohy Stream Analytics pro určitý vstup ovlivňuje počet čtenářů v skupinu jednoho příjemce. Přesný počet přijímačů je založen na podrobnosti interní implementace pro horizontální navýšení kapacity topologie logiku a není dostupná externě. Při spuštění úlohy nebo během upgradu projektu můžete změnit počet čtenářů.

Pokud překročí maximální počet přijímačů zobrazit tato chyba je: `The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.`

> [!NOTE]
> Když se počet čtenářů změní během úlohy upgradu, přechodných upozornění se zapisují do protokolů auditu. Úlohy Stream Analytics automaticky zotavit tyto přechodné problémy.

### <a name="add-a-consumer-group-in-event-hubs"></a>Přidat skupinu uživatelů ve službě Event Hubs
Chcete-li přidat novou skupinu uživatelů ve vaší instanci služby Event Hubs, postupujte podle těchto kroků:

1. Přihlaste se k portálu Azure.

2. Vyhledejte vaši službu Event Hubs.

3. Vyberte **Event Hubs** pod **entity** záhlaví.

4. Vyberte Centrum událostí podle názvu.

5. Na **Instance služby Event Hubs** stránce v části **entity** záhlaví, vyberte **skupiny příjemců**. Skupina uživatelů s názvem **$Default** je uvedena.

6. Vyberte **+ skupina uživatelů** se přidat novou skupinu uživatelů. 

   ![Přidat skupinu uživatelů ve službě Event Hubs](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. Při vytváření vstup do úlohy Stream Analytics tak, aby odkazoval do centra událostí, zadat skupinu příjemců existuje. $Default se používá, když není zadaný žádný. Jakmile vytvoříte novou skupinu uživatelů, upravit vstup Centrum událostí v úloze Stream Analytics a zadejte název nové skupiny příjemců.


## <a name="readers-per-partition-exceeds-event-hubs-limit"></a>Čtenáři na oddíl překračuje limit služby Event Hubs

Pokud streamování syntaxi dotazu odkazuje na stejný vstupní prostředek centra událostí více než jednou, modul úlohy můžete použít několik čtenářů každý dotaz z tu samou skupinu příjemců. Pokud existuje příliš mnoho odkazů na stejnou skupinu uživatelů, můžete úlohy překročily maximální počet pět a vyvolána k chybě. V těchto případech můžete dál rozdělit pomocí více vstupů napříč více skupin konzumentů, pomocí řešení popsaných v následující části. 

Scénáře, ve kterých počet čtenářů na oddíl překračuje limit služby Event Hubs pěti patří:

* Více příkazů SELECT: Pokud používáte více příkazů SELECT, které odkazují na **stejné** vstup Centrum událostí, každý příkaz SELECT způsobí, že nového příjemce, který se má vytvořit.
* SJEDNOCENÍ: Při použití SJEDNOCENÍ je možné mít více vstupů, které odkazují na **stejné** skupiny centra a příjemce událostí.
* SPOJENÍ sama: Když použijete operaci JOIN SAMOOBSLUŽNÉHO, je možné odkazovat **stejné** centra událostí více než jednou.

Následující osvědčené postupy může pomoci zmírnit scénáře, ve kterých počet čtenářů na oddíl překračuje limit služby Event Hubs pět.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>Rozdělit několik kroků dotazu pomocí klauzule WITH

V klauzuli WITH určuje sadu dočasné pojmenované výsledků, které lze odkazovat pomocí klauzule FROM v dotazu. Definujete v rámci provedení jednoho příkazu SELECT s klauzulí with.

Například místo tohoto dotazu:

```
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

Pomocí tohoto dotazu:

```
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

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>Ujistěte se, že vstupy vytvořit vazbu na jiný příjemce skupiny

Pro dotazy, které jsou připojené tři nebo více vstupů do stejné skupiny příjemců centra událostí vytvořte samostatné příjemce skupiny. To vyžaduje vytvoření další vstupy Stream Analytics.

## <a name="get-help"></a>Podpora

Potřebujete další pomoc, vyzkoušejte naše [fóru Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další postup

* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)