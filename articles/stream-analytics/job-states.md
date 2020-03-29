---
title: Stavy úloh Azure Stream Analytics
description: Tento článek popisuje čtyři různé stavy úlohy Stream Analytics; spuštěno, zastaveno, znehodnoceno a selhalo.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 454474333cac94dc25deae8196e9ba45bcb3a574
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75359762"
---
# <a name="azure-stream-analytics-job-states"></a>Stavy úloh Azure Stream Analytics

Úloha Služby Stream Analytics může být v daném okamžiku v jednom ze čtyř stavů: spuštění, zastavení, snížení nebo selhání. Stav úlohy najdete na stránce Přehled úlohy Stream Analytics na webu Azure Portal. 

| Stav | Popis | Doporučené akce |
| --- | --- | --- |
| **Spuštěno** | Vaše úloha běží na události čtení Azure pocházející z definovaných vstupních zdrojů, jejich zpracování a zápis výsledků do nakonfigurovaných výstupních propadů. | Je osvědčeným postupem sledovat výkon vaší práce sledováním [klíčových metrik](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor). |
| **Zastaveno** | Vaše úloha je zastavena a nezpracovává události. | Není k dispozici | 
| **Snížený výkon** | Může být občasné problémy se vstupnía výstupní připojení. Tyto chyby se nazývají přechodné chyby, které mohou způsobit, že vaše úloha přejde do stavu Degradované. Stream Analytics se okamžitě pokusí zotavit z těchto chyb a vrátí se do stavu Spuštěno (během několika minut). K těmto chybám může dojít kvůli problémům se sítí, dostupnosti jiných prostředků Azure, chybám při rekonstrukci atd. Výkon vaší úlohy může být ovlivněn, pokud je úloha ve zhoršeném stavu.| Můžete se podívat na [diagnostické nebo protokoly aktivit](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) se dozvíte více o příčině těchto přechodných chyb. V případech, jako jsou chyby deserializace, se doporučuje provést nápravná opatření k zajištění události nejsou poškozeny. Pokud úloha stále dosahuje limitu využití prostředků, pokuste se zvýšit číslo SU nebo [paralelizovat úlohu](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization). V ostatních případech, kdy nelze provést žádnou akci, stream analytics se pokusí obnovit do *stavu Spuštěno.* <br> Metriku [zpoždění vodoznaku](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) můžete použít k pochopení, pokud tyto přechodné chyby ovlivňují výkon vaší úlohy.|
| **Failed** | Při úloze došlo ke kritické chybě, která vedla k selhání stavu. Události nejsou čteny a zpracovávány. Chyby za běhu jsou běžnou příčinou úloh, které končí ve stavu selhání. | Můžete [nakonfigurovat výstrahy](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#set-up-alerts-in-the-azure-portal) tak, abyste byli upozorněni, když úloha přejde do stavu Selhání. <br> <br>Můžete ladit pomocí [aktivity a diagnostické protokoly](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) k identifikaci hlavní příčiny a řešení problému.|

## <a name="next-steps"></a>Další kroky
* [Upozornění na nastavení pro úlohy Azure Stream Analytics](stream-analytics-set-up-alerts.md)
* [Metriky dostupné v Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
* [Poradce při potížích s použitím protokolů aktivit a diagnostiky](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)
