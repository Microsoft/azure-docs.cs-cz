---
title: Ladění se příjemců u centra událostí Azure Stream Analytics | Microsoft Docs
description: Osvědčené postupy pro Event Hubs skupiny uživatelů v úlohy Stream Analytics využívá dotaz.
keywords: limit centra událostí, skupiny uživatelů
services: stream-analytics
documentationcenter: ''
author: jseb225
manager: ryanw
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: ba54e4c0146e797e300579415db170fd6d290034
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="debug-azure-stream-analytics-with-event-hub-receivers"></a>Ladění Azure Stream Analytics se příjemců u centra událostí

Azure Event Hubs můžete v Azure Stream Analytics ingestování nebo výstupní data z úlohy. Doporučený postup pro použití služby Event Hubs je použití více skupin příjemce zajistit škálovatelnost úlohy. Jedním z důvodů je, že počet čtenářů v úloze Stream Analytics pro specifický vstup ovlivní počet čtenářů v skupinu jednoho příjemce. Přesné počet příjemců vychází podrobnosti interní implementace pro logiku topologie Škálováním na více systémů. Počet příjemců nebude vystavena, externě. Při spuštění úlohy nebo během upgradů úlohy, můžete změnit počet čtenářů.

> [!NOTE]
> Při změně počet čtenářů během upgradu úlohy přechodný upozornění se zapisují do protokolů auditů. Úlohy Stream Analytics automaticky zotavit tyto přechodné problémy.

## <a name="number-of-readers-per-partition-exceeds-event-hubs-limit-of-five"></a>Počet čtenářů na oddíl překračuje limit služby Event Hubs pět

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
WITH input (
   SELECT * FROM inputEventHub
) as data

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


## <a name="get-help"></a>Podpora
Pro další pomoc, vyzkoušejte naše [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další postup
* [Úvod do služby Stream Analytics](stream-analytics-introduction.md)
* [Začínáme s Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování úlohy Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka jazyka dotazů Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Stream Analytics správu odkazu k REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
