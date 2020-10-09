---
title: Stavy úlohy Azure Stream Analytics
description: Tento článek popisuje čtyři různé stavy Stream Analytics úlohy. spuštění, zastavení, degradace a selhání.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: fc46b4f24c3c21cda190f80556373991f6c711c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "82130927"
---
# <a name="azure-stream-analytics-job-states"></a>Stavy úlohy Azure Stream Analytics

Stream Analytics úloha může být v každém okamžiku v jednom ze čtyř stavů: spuštěno, zastaveno, snížené nebo neúspěšné. Stav úlohy můžete najít na stránce přehledu Stream Analytics úlohy v Azure Portal. 

| State | Popis | Doporučené akce |
| --- | --- | --- |
| **Spuštěno** | Vaše úloha běží na Azure čtení událostí přicházejících z definovaných vstupních zdrojů, jejich zpracování a zápis výsledků do nakonfigurovaných výstupních umyvadel. | Osvědčeným postupem je sledovat výkon úlohy monitorováním [klíčových metrik](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor). |
| **Zastaveno** | Vaše úloha se zastavila a nezpracovává události. | Není k dispozici | 
| **Snížený výkon** | Vstupní a výstupní připojení můžou mít přerušované problémy. Tyto chyby se označují jako přechodné chyby, což může vést k tomu, že vaše úloha vstoupí do sníženého stavu. Stream Analytics se okamžitě pokusí o zotavení z takových chyb a návrat do běžícího stavu (během několika minut). K těmto chybám může dojít kvůli problémům se sítí, dostupnosti dalších prostředků Azure, chybám deserializaci atd. Výkon úlohy může být ovlivněný, pokud je úloha ve stavu sníženo.| Další informace o příčině těchto přechodných chyb najdete v [protokolech diagnostiky nebo činnosti](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) . V případech, jako jsou chyby deserializace, se doporučuje podniknout nápravná opatření, aby se zajistilo, že události nejsou poškozeny. Pokud úloha dosáhne limitu využití prostředků, zkuste zvýšit číslo SU nebo [paralelizovat vaši úlohu](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization). V jiných případech, kdy nemůžete provést žádnou akci, Stream Analytics se pokusí o obnovení do *běžícího* stavu. <br> Pomocí metriky [zpoždění vodoznaku](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) můžete zjistit, jestli tyto přechodné chyby ovlivnily výkon vaší úlohy.|
| **Neúspěšný** | U úlohy došlo k závažné chybě, která má za následek selhání stavu. Události nejsou čteny a zpracovávány. Chyby za běhu představují běžnou příčinu úloh, které skončí ve stavu selhání. | Můžete [nakonfigurovat výstrahy](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#set-up-alerts-in-the-azure-portal) , abyste se dostali na oznámení, že se úloha dostane do stavu selhání. <br> <br>Můžete ladit pomocí [protokolů aktivit a prostředků](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) k identifikaci hlavní příčiny a vyřešení problému.|

## <a name="next-steps"></a>Další kroky
* [Nastavení výstrah pro úlohy Azure Stream Analytics](stream-analytics-set-up-alerts.md)
* [Metriky dostupné v Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
* [Řešení potíží pomocí protokolů aktivit a prostředků](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)
