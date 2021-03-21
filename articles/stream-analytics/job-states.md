---
title: Stavy úlohy Azure Stream Analytics
description: Tento článek popisuje čtyři různé stavy Stream Analytics úlohy. spuštění, zastavení, degradace a selhání.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: dd298fae148b9e1e51072e98a4e0e5ece10967f3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020447"
---
# <a name="azure-stream-analytics-job-states"></a>Stavy úlohy Azure Stream Analytics

Stream Analytics úloha může být v každém okamžiku v jednom ze čtyř stavů: spuštěno, zastaveno, snížené nebo neúspěšné. Stav úlohy můžete najít na stránce přehledu Stream Analytics úlohy v Azure Portal. 

| State | Popis | Doporučené akce |
| --- | --- | --- |
| **Spuštěno** | Vaše úloha běží na Azure čtení událostí přicházejících z definovaných vstupních zdrojů, jejich zpracování a zápis výsledků do nakonfigurovaných výstupních umyvadel. | Osvědčeným postupem je sledovat výkon úlohy monitorováním [klíčových metrik](./stream-analytics-set-up-alerts.md#scenarios-to-monitor). |
| **Zastaveno** | Vaše úloha se zastavila a nezpracovává události. | NA | 
| **Snížený výkon** | Vstupní a výstupní připojení můžou mít přerušované problémy. Tyto chyby se označují jako přechodné chyby, což může vést k tomu, že vaše úloha vstoupí do sníženého stavu. Stream Analytics se okamžitě pokusí o zotavení z takových chyb a návrat do běžícího stavu (během několika minut). K těmto chybám může dojít kvůli problémům se sítí, dostupnosti dalších prostředků Azure, chybám deserializaci atd. Výkon úlohy může být ovlivněný, pokud je úloha ve stavu sníženo.| Další informace o příčině těchto přechodných chyb najdete v [protokolech diagnostiky nebo činnosti](./stream-analytics-job-diagnostic-logs.md#debugging-using-activity-logs) . V případech, jako jsou chyby deserializace, se doporučuje podniknout nápravná opatření, aby se zajistilo, že události nejsou poškozeny. Pokud úloha dosáhne limitu využití prostředků, zkuste zvýšit číslo SU nebo [paralelizovat vaši úlohu](./stream-analytics-parallelization.md). V jiných případech, kdy nemůžete provést žádnou akci, Stream Analytics se pokusí o obnovení do *běžícího* stavu. <br> Pomocí metriky [zpoždění vodoznaku](./stream-analytics-set-up-alerts.md#scenarios-to-monitor) můžete zjistit, jestli tyto přechodné chyby ovlivnily výkon vaší úlohy.|
| **Neúspěšný** | U úlohy došlo k závažné chybě, která má za následek selhání stavu. Události nejsou čteny a zpracovávány. Chyby za běhu představují běžnou příčinu úloh, které skončí ve stavu selhání. | Můžete [nakonfigurovat výstrahy](./stream-analytics-set-up-alerts.md#set-up-alerts-in-the-azure-portal) , abyste se dostali na oznámení, že se úloha dostane do stavu selhání. <br> <br>Můžete ladit pomocí [protokolů aktivit a prostředků](./stream-analytics-job-diagnostic-logs.md#debugging-using-activity-logs) k identifikaci hlavní příčiny a vyřešení problému.|

## <a name="next-steps"></a>Další kroky
* [Nastavení výstrah pro úlohy Azure Stream Analytics](stream-analytics-set-up-alerts.md)
* [Metriky dostupné v Stream Analytics](./stream-analytics-monitoring.md#metrics-available-for-stream-analytics)
* [Řešení potíží pomocí protokolů aktivit a prostředků](./stream-analytics-job-diagnostic-logs.md)