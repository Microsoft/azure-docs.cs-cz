---
title: Řešení potíží s příjemců u centra událostí v Azure Stream Analytics
description: Tento článek popisuje, jak používat více skupin uživatelů pro službu Event Hubs vstupy do úlohy Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/27/2018
ms.openlocfilehash: aaa8c4e8d273b44f453d3f63f0be1d4baf980649
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2018
---
# <a name="troubleshoot-event-hub-receivers-in-azure-stream-analytics"></a>Řešení potíží s příjemců u centra událostí v Azure Stream Analytics

Azure Event Hubs můžete v Azure Stream Analytics ingestování nebo výstupní data z úlohy. Doporučený postup pro použití služby Event Hubs je použití více skupin příjemce zajistit škálovatelnost úlohy. Jedním z důvodů je, že počet čtenářů v úloze Stream Analytics pro specifický vstup ovlivní počet čtenářů v skupinu jednoho příjemce. Přesné počet příjemců vychází podrobnosti interní implementace pro logiku topologie Škálováním na více systémů. Počet příjemců nebude vystavena, externě. Při spuštění úlohy nebo během upgradů úlohy, můžete změnit počet čtenářů.

Chyba zobrazí, když počet příjemců překračuje maximální počet je: `The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.`

> [!NOTE]
> Při změně počet čtenářů během upgradu úlohy přechodný upozornění se zapisují do protokolů auditů. Úlohy Stream Analytics automaticky zotavit tyto přechodné problémy.

## <a name="add-a-consumer-group-in-event-hubs"></a>Přidat skupinu uživatelů ve službě Event Hubs
Pokud chcete přidat novou skupinu uživatelů v instanci služby Event Hubs, postupujte takto:

1. Přihlaste se k portálu Azure.

2. Vyhledejte Event Hubs.

3. Vyberte **Event Hubs** pod **entity** záhlaví.

4. Vyberte centra událostí podle názvu.

5. Na **Instance centra událostí** v části **entity** záhlaví, vyberte **skupiny příjemců**. Skupina uživatelů s názvem **$Default** je uveden.

6. Vyberte **+ skupiny příjemců** přidat nové skupiny příjemců. 

   ![Přidat skupinu uživatelů ve službě Event Hubs](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. Když vytvoříte vstup do úlohy Stream Analytics tak, aby odkazovaly do centra událostí, jste zadali existuje skupiny příjemců. $Default se používá, když není zadaný žádný. Jakmile vytvoříte novou skupinu uživatelů, upravte centra událostí vstup do úlohy Stream Analytics a zadejte název nové skupiny příjemců.


## <a name="number-of-readers-per-partition-exceeds-event-hubs-limit-of-five"></a>Počet čtenářů na oddíl překračuje limit služby Event Hubs pět

Pokud streamování syntaxi dotazu odkazuje na stejný vstupní prostředek centra událostí vícekrát, můžete použít modul úlohy více čtečky za dotaz z této stejné skupiny příjemců. Pokud je příliš mnoho odkazů na stejnou skupinu uživatelů, úloha může být vyšší než limitu pěti a došlo k chybě. V těchto případech lze dále rozdělit s použitím více vstupů v několika skupinách pro příjemce pomocí řešení popsaných v následující části. 

Scénáře, ve kterých počet čtenářů na oddíl překračuje limit služby Event Hubs pět zahrnují následující:

* Více příkazů SELECT: Pokud používáte více příkazů SELECT, které odkazují na **stejné** vstupní centra událostí, každý příkaz SELECT způsobí, že nový příjemce, který se má vytvořit.
* SJEDNOCENÍ: Při použití spojení, je možné, že více vstupů, které odkazují na **stejné** skupina rozbočovače a příjemce událostí.
* SPOJENÍ sama: Při použití připojení k SAMOOBSLUŽNÉ operace, je možné odkazovat **stejné** centra událostí vícekrát.

## <a name="solution"></a>Řešení

Následující osvědčené postupy může pomoci zmírnit scénáře, ve kterých počet čtenářů na oddíl překračuje limit služby Event Hubs pět.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>Rozdělením více kroků dotazu pomocí klauzule WITH

V klauzuli WITH určuje je sada výsledků dotazu dočasné s názvem, který lze odkazovat pomocí klauzule FROM v dotazu. V klauzuli WITH definujete v oboru provedení jednoho příkazu SELECT.

Například místo tento dotaz:

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

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>Ujistěte se, že vstupy vázat na skupiny různých uživatelů

Pro dotazy, v nichž jsou propojeny tři nebo více vstupů do stejné skupiny příjemců Event Hubs vytvořte skupiny samostatné příjemce. To vyžaduje vytvoření další Stream Analytics vstupy.


## <a name="next-steps"></a>Další postup
* [Škálování úlohy Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka jazyka dotazů Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
