---
title: Zobrazení spuštění vrcholu v nástrojích datového jezera pro Visual Studio
description: Tento článek popisuje, jak použít zobrazení spuštění vrcholu ke zkoušce úlohY Data Lake Analytics.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: 5366d852-e7d6-44cf-a88c-e9f52f15f7df
ms.topic: conceptual
ms.date: 10/13/2016
ms.openlocfilehash: f5adbb75e6852551976aa040a1a1c723d2e3f59b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71309718"
---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>Použití zobrazení spuštění vrcholu v nástrojích datového jezera pro Visual Studio
Přečtěte si, jak pomocí zobrazení spuštění vrcholu prozkoušení úloh Data Lake Analytics.


## <a name="open-the-vertex-execution-view"></a>Otevření zobrazení spuštění vrcholu
Otevřete úlohu U-SQL v nástrojích datového jezera pro Visual Studio. V levém dolním rohu klikněte na **Příkaz spuštění vrcholu.** V závislosti na připojení k síti může být výzva k načtení profilů.

![Zobrazení spuštění nástroje analýzy datových jezer](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>Pochopit zobrazení spuštění vrcholu
Zobrazení spuštění vrcholu má tři části:

![Zobrazení spuštění nástroje analýzy datových jezer](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

**Volič vrcholu** vlevo umožňuje vybrat vrcholy podle prvků (například 10 nejlepších dat nebo zvolit podle fáze). Jedním z nejčastěji používaných filtrů je zobrazení **vrcholů na kritické cestě**. **Kritická cesta** je nejdelší řetězec vrcholů úlohy U-SQL. Pochopení kritické cesty je užitečné pro optimalizaci úloh kontrolou, který vrchol trvá nejdéle.
  
![Zobrazení spuštění nástroje analýzy datových jezer](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)

Horní středové podokno zobrazuje **provozní stav všech vrcholů**.
  
![Zobrazení spuštění nástroje analýzy datových jezer](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)

Dolní prostřední podokno zobrazuje informace o jednotlivých vrcholech:
* Název procesu: Název instance vrcholu. Skládá se z různých částí v StageName| Název vrcholu| Instance VertexRun. Například vrchol SV7_Split[62].v1 je pro druhou spuštěnou instanci (.v1, index začínající od 0) vrcholu číslo 62 ve fázi SV7_Split.
* Celkový počet přečtených/zapsatdat: Data byla přečtena/zapsána tímto vrcholem.
* Stav/Stav ukončení: Konečný stav po ukončení vrcholu.
* Ukončovací kód/typ selhání: Chyba, když se vrchol nezdařil.
* Důvod vytvoření: Proč byl vytvořen vrchol.
* Latence prostředků/latence procesu/latence pn fronty: doba, kterou vrchol trval na prostředky, zpracování dat a zůstat ve frontě.
* Identifikátor GUID procesu/autora: IDENTIFIKÁTOR GUID pro aktuální běžící vrchol nebo jeho tvůrce.
* Verze: N-tého instance spuštěného vrcholu (systém může naplánovat nové instance vrcholu z mnoha důvodů, například převzetí služeb při selhání, redundance výpočetních prostředků atd.)
* Čas vytvoření verze.
* Proces Vytvořit čas zahájení/čas zahájení procesu/čas zahájení procesu/čas dokončení procesu: když proces vrcholu spustí vytváření; když proces vrcholu začne frontovat; při zahájení určitého procesu vrcholu; po dokončení určitého vrcholu.

## <a name="next-steps"></a>Další kroky
* Pokud chcete protokolovat diagnostické informace, přečtěte si téma [Zobrazení protokolů diagnostiky pro Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Pokud chcete zobrazit komplexnější dotaz, přejděte k tématu [Analýza webových protokolů pomocí Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Podrobnosti o úloze najdete v [tématu Použití prohlížeče úloh a zobrazení úloh pro úlohy Azure Data lake Analytics.](data-lake-analytics-data-lake-tools-view-jobs.md)
