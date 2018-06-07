---
title: Monitorovat metriky serveru Azure Analysis Services | Microsoft Docs
description: Naučte se monitorovat metriky serveru služby Analysis Services v Azure portal.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f7c4afc2b48d097c67d69ef0cee8a45a1fdeb557
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34596188"
---
# <a name="monitor-server-metrics"></a>Monitorování serveru metriky

Služba Analysis Services poskytuje metriky, které vám pomohu při monitorování výkonu a stavu vašich serverů. Například sledujte paměť a využití procesoru, počet připojení klientů a spotřeba prostředků dotazu. Služba Analysis Services použije stejný monitorování framework jako většina ostatních služeb Azure. Další informace najdete v tématu [metriky v Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

Pokud chcete provést další podrobné diagnostiky, sledovat výkon a identifikovat trendy na několika prostředcích service ve skupině prostředků nebo předplatného, použijte [Azure monitorování](https://azure.microsoft.com/services/monitor/). Azure monitorování (služba) může vést k fakturovatelný služby.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Ke sledování metriky pro serveru služby Analysis Services

1. Na portálu Azure, vyberte **metriky**.

    ![Monitorování na webu Azure Portal](./media/analysis-services-monitor/aas-monitor-portal.png)

2. V **dostupné metriky**, vybrat metriky pro zahrnutí do grafu. 

    ![Monitorování grafu](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>
## <a name="server-metrics"></a>Metriky serveru
Pomocí této tabulky můžete určit, které metriky jsou vhodné pro váš scénář monitorování. Na stejném grafu lze zobrazit pouze metriky stejné jednotky.

|Metrika|Metriky zobrazovaný název|Jednotka|Typ agregace|Popis|
|---|---|---|---|---|
|CommandPoolJobQueueLength|Příkaz délku fronty úloh|Počet|Průměr|Počet úloh ve frontě příkaz fondu vláken.|
|CurrentConnections|Připojení: Aktuální připojení|Počet|Průměr|Aktuální počet připojení klienta.|
|CurrentUserSessions|Aktuální relace uživatele|Počet|Průměr|Aktuální počet uživatelských relací navázat.|
|mashup_engine_memory_metric|M modul paměti|B|Průměr|Využití paměti procesy modul hybridní webové aplikace|
|mashup_engine_qpu_metric|Modul M QPU|Počet|Průměr|Využití QPU procesy modul hybridní webové aplikace|
|memory_metric|Memory (Paměť)|B|Průměr|Paměť. V rozsahu 0-25 GB pro S1, 0 – 50 GB pro S2 a 0 – 100 GB pro S4|
|memory_thrashing_metric|Zahlcení paměti|Procento|Průměr|Průměrná paměti zahlcení.|
|CleanerCurrentPrice|Paměť: Čisticí aktuální cena|Počet|Průměr|Aktuální cena paměti, $a bajtů/čas, normalizovány na 1000.|
|CleanerMemoryNonshrinkable|Paměť: Čisticí nonshrinkable paměti|B|Průměr|Množství paměti v bajtech, není v souladu čisticí vyprazdňování pozadím.|
|CleanerMemoryShrinkable|Paměť: Čisticí paměti vypočítat|B|Průměr|Množství paměti v bajtech, podstoupí čisticí vyprazdňování pozadím.|
|MemoryLimitHard|Paměti: Pevný Limit paměti|B|Průměr|Omezení pevné paměti z konfiguračního souboru.|
|MemoryLimitHigh|Paměť: Omezení paměti vysoká|B|Průměr|Limit velkého množství paměti, z konfiguračního souboru.|
|MemoryLimitLow|Paměti: Nízká Limit paměti|B|Průměr|Limit nedostatek paměti z konfiguračního souboru.|
|MemoryLimitVertiPaq|Paměti: VertiPaq Limit paměti|B|Průměr|Omezení v paměti z konfiguračního souboru.|
|Parametru MemoryUsage|Paměti: Využití paměti|B|Průměr|Využití paměti procesem serveru v rámci výpočet ceny čisticí paměti. Rovná se čítač Process\PrivateBytes plus velikost dat mapované paměti, ignoruje všechny paměti, které bylo namapované nebo přidělené stroj v paměti analytics (VertiPaq) překračující modul Limit paměti.|
|Kvóta|Paměť: kvóty|B|Průměr|Aktuální kvótu paměti, v bajtech. Kvótu paměti se také označuje jako rezervace paměti grant nebo paměti.|
|QuotaBlocked|Paměti: Blokované kvótu|Počet|Průměr|Aktuální počet požadavků kvóty, které jsou blokovaný, dokud jsou uvolněny kvóty další paměť.|
|VertiPaqNonpaged|Paměť: VertiPaq nestránkovaného fondu|B|Průměr|Bajtů paměti uzamčena v pracovní sadě pro použití stroj v paměti.|
|VertiPaqPaged|Paměť: VertiPaq stránkovaného fondu|B|Průměr|Bajty stránkovaného paměti používané pro data v paměti.|
|ProcessingPoolJobQueueLength|Délka fronty úloh zpracování fondu|Počet|Průměr|Počet úloh jiný I/O ve frontě fondu zpracování vláken.|
|RowsConvertedPerSec|Zpracování: Řádky převést za sekundu|CountPerSecond|Průměr|Počet řádků převést během zpracování.|
|RowsReadPerSec|Zpracování: Řádky čtení za sekundu|CountPerSecond|Průměr|Počet řádků přečíst ze všech relačních databází.|
|RowsWrittenPerSec|Zpracování: Řádků zapsaných za sekundu|CountPerSecond|Průměr|Počet řádků zapsaných během zpracování.|
|qpu_metric|QPU|Počet|Průměr|QPU. Rozsah 0-100 S1, 0 – 200 S2 a 0 – 400 pro S4|
|QueryPoolBusyThreads|Dotaz z fondu podprocesů zaneprázdněn|Počet|Průměr|Počet vytížených vláken ve fondu vláken dotazu.|
|SuccessfullConnectionsPerSec|Úspěšné připojení za sekundu|CountPerSecond|Průměr|Počet dokončených úspěšné připojení.|
|CommandPoolBusyThreads|Vláken: Příkaz zaneprázdněn z fondu podprocesů|Počet|Průměr|Počet vytížených vláken ve fondu vláken příkaz.|
|CommandPoolIdleThreads|Vláken: Příkaz nečinných vláken fondu|Počet|Průměr|Počet nečinných vláken ve fondu vláken příkaz.|
|LongParsingBusyThreads|Vláken: Analýza zaneprázdněn vláken dlouho|Počet|Průměr|Počet vytížených vláken ve fondu vláken dlouho analýzy.|
|LongParsingIdleThreads|Vláken: Analýza nečinných vláken dlouho|Počet|Průměr|Počet nečinných vláken ve fondu vláken dlouho analýzy.|
|LongParsingJobQueueLength|Vláken: Analýza dlouho délka fronty úloh|Počet|Průměr|Počet úloh ve frontě dlouho analýzy fondu vláken.|
|ProcessingPoolIOJobQueueLength|Vláken: Fond délka fronty vstupně-výstupní úlohy zpracování|Počet|Průměr|Počet vstupně-výstupních úloh ve frontě fondu zpracování vláken.|
|ProcessingPoolBusyIOJobThreads|Vláken: Fond zaneprázdněn vstupně-výstupní úlohy vláken zpracování|Počet|Průměr|Počet vláken, které jsou spuštěné úlohy vstupně-výstupních operací ve fondu zpracování vláken.|
|ProcessingPoolBusyNonIOThreads|Vláken: Zaneprázdněný jiný I/O vláken fondu zpracování|Počet|Průměr|Počet vláken, které jsou spuštěné úlohy bez I/O ve fondu zpracování vláken.|
|ProcessingPoolIdleIOJobThreads|Vláken: Fond nečinnosti vstupně-výstupní úlohy vláken zpracování|Počet|Průměr|Počet vstupně-výstupních úloh ve fondu zpracování vláken nečinných vláken.|
|ProcessingPoolIdleNonIOThreads|Vláken: Nečinnosti vláken jiný I/O fondu zpracování|Počet|Průměr|Počet nečinných vláken ve fondu zpracování vláken, který je vyhrazený pro jiný I/O úlohy.|
|QueryPoolIdleThreads|Vláken: Dotaz nečinných vláken fondu|Počet|Průměr|Počet vstupně-výstupních úloh ve fondu zpracování vláken nečinných vláken.|
|QueryPoolJobQueueLength|Vláken: Délka fronty úloh fondu dotazu|Počet|Průměr|Počet úloh ve frontě fondu vláken dotazu.|
|ShortParsingBusyThreads|Vláken: Analýza zaneprázdněn vláken krátké|Počet|Průměr|Počet vytížených vláken v krátké analýzy fondu vláken.|
|ShortParsingIdleThreads|Vláken: Analýza nečinných vláken krátké|Počet|Průměr|Počet nečinných vláken v krátké analýzy fondu vláken.|
|ShortParsingJobQueueLength|Vláken: Analýza délka fronty úloh krátké|Počet|Průměr|Počet úloh ve frontě krátké analýzy fondu vláken.|
|TotalConnectionFailures|Chyby připojení (celkem)|Počet|Průměr|Celkový počet neúspěšných pokusů o připojení.|
|TotalConnectionRequests|Žádosti o připojení (celkem)|Počet|Průměr|Požadavky na celkový počet připojení. |

## <a name="next-steps"></a>Další postup
[Monitorování v Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md)   
[Metriky v Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)   
[Metriky v Azure monitorování rozhraní REST API](https://msdn.microsoft.com/library/azure/dn931930.aspx)