---
title: Stavy úloh Azure Stream Analytics
description: Tento článek popisuje různé stavy úlohy Stream Analytics
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: bef21dc35bbd2b9b50cf7b362624321866773bfe
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331341"
---
# <a name="azure-stream-analytics-job-states"></a>Stavy úloh Azure Stream Analytics

Úlohu Stream Analytics může být v jednom ze čtyř stavů v daném okamžiku. Stav úlohy na stránce s přehledem vaší úlohy Stream Analytics najdete na webu Azure Portal. 

| Stav | Popis | Doporučené akce |
| --- | --- | --- |
| **Spuštění** | Vaše úloha běží na Azure události pocházející z definovaných vstupní zdroje, jejich zpracování a zápis výsledky do nakonfigurovaného výstupních jímek pro čtení. | Je osvědčeným postupem je sledovat výkon vaší úlohy monitorování [klíčové metriky](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor). |
| **Zastavení** | Vaše úloha se zastaví a nezpracovává události. | Není k dispozici | 
| **Degraded** | Můžete mít přerušované problémy s vstupní a výstupní připojení. Tyto chyby se říká přechodné chyby, které by mohlo způsobit nepoužitelnost snížený stavu úlohy. Stream Analytics se okamžitě pokusí zotavit z těchto chyb a vrátit do stavu spuštění (během několika minut). K těmto chybám může nastat kvůli problémům se sítí, dostupnosti jiných prostředků služby Azure deserializaci chyby atd. Výkon vaší úlohy může mít vliv, pokud úloha je v degradovaném stavu.| Můžete si prohlédnout [protokoly diagnostiky nebo aktivita](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) získat další informace o příčinách těchto přechodných chyb. V případech, jako jsou chyby deserializace doporučujeme k provedení nápravné akce k zajištění, že nejsou poškozené události. Pokud úloha udržuje dosažení limitu využití prostředků, zkuste zvýšit počet SU nebo [paralelní úlohy](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization). V ostatních případech, kdy nelze provádět žádnou akci, se pokusí obnovit do Stream Analytics *systémem* stavu. <br> Můžete použít [vodoznak zpoždění](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) metrika k porozumění, pokud tyto přechodné chyby mají vliv na výkon vaší úlohy.|
| **Se nezdařilo** | Vaše úloha došlo ke kritické chybě výsledná ve stavu selhání. Události nejsou číst a zpracovat. Chyby za běhu jsou běžnou příčinou pro úlohy s koncovkou stavu selhání. | Je možné [konfigurace výstrah](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#set-up-alerts-in-the-azure-portal) tak, aby se vám zobrazit oznámení, pokud úloha přejde do stavu selhání. <br> <br>Můžete ladit pomocí [aktivity a diagnostické protokoly](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) identifikovat hlavní příčinu a řešit potíže.|

## <a name="next-steps"></a>Další postup
* [Nastavení výstrah pro úlohy Azure Stream Analytics](stream-analytics-set-up-alerts.md)
* [Metriky, které jsou k dispozici ve službě Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
* [Řešení potíží pomocí aktivity a diagnostické protokoly](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)
