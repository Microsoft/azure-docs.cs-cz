---
title: Zobrazení provádění vrcholů v Data Lakech nástrojích pro Visual Studio
description: Tento článek popisuje, jak použít zobrazení spuštění vrcholu pro zkoušku Data Lake Analytics úloh.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: 5366d852-e7d6-44cf-a88c-e9f52f15f7df
ms.topic: conceptual
ms.date: 10/13/2016
ms.openlocfilehash: f5adbb75e6852551976aa040a1a1c723d2e3f59b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "71309718"
---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>Použití zobrazení spuštění vrcholu v Data Lakech nástrojích pro Visual Studio
Naučte se používat zobrazení spuštění vrcholu pro zkoušku Data Lake Analytics úloh.


## <a name="open-the-vertex-execution-view"></a>Otevření zobrazení spuštění vrcholu
V Data Lakech nástrojích pro Visual Studio otevřete úlohu U-SQL. V levém dolním rohu klikněte na **zobrazení spouštění vrcholů** . Může se zobrazit výzva k načtení profilů a v závislosti na připojení k síti může trvat delší dobu.

![Zobrazení spuštění vrcholu Data Lake Analytics nástrojů](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>Principy zobrazení spouštění vrcholu
Zobrazení spuštění vrcholu má tři části:

![Zobrazení spuštění vrcholu Data Lake Analytics nástrojů](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

**Selektor vrcholů** na levé straně umožňuje vybrat vrcholy podle funkcí (například přečtených prvních 10 dat nebo zvolit podle fáze). Jedním z nejčastěji používaných filtrů je zobrazit **vrcholy v kritické cestě**. **Kritická cesta** je nejdelší řetěz vrcholů úlohy U-SQL. Porozumění důležité cestě je užitečné pro optimalizaci vašich úloh pomocí kontroly, který vrchol trvá nejdelší dobu.
  
![Zobrazení spuštění vrcholu Data Lake Analytics nástrojů](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)

Horní prostřední podokno zobrazuje **stav spuštění všech vrcholů**.
  
![Zobrazení spuštění vrcholu Data Lake Analytics nástrojů](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)

Dolní prostřední podokno zobrazuje informace o každém vrcholu:
* Název procesu: název instance vrcholu. Skládá se z různých částí v části Stage | Vrchol | VertexRunInstance. Například vrchol SV7_Split [62]. v1 představuje druhou spuštěnou instanci (. v1, index začínající na 0) číslo vrcholu 62 ve fázi SV7_Split.
* Celkem přečtených a zapsaných dat: data byla pomocí tohoto vrcholu načtena nebo zapsána.
* Stav/ukončení stavu: konečný stav po ukončení vrcholu.
* Ukončovací kód/selhání typ: Chyba při selhání vrcholu.
* Důvod vytvoření: Proč byl vrchol vytvořen.
* Latence a latence prostředku/latence/PN fronty: doba trvání vrcholu pro čekání na prostředky, zpracování dat a udržování ve frontě.
* Identifikátor GUID procesu/tvůrce: GUID pro aktuální běžící vrchol nebo jeho tvůrce.
* Verze: N-tou instancí běžícího vrcholu (systém může naplánovat nové instance vrcholu z mnoha důvodů, třeba převzetí služeb při selhání, redundanci výpočtů atd.)
* Čas vytvoření verze
* Čas spuštění procesu vytvoření/procesu a čas spuštění procesu/dokončení procesu: když proces vrcholu spustí vytváření, Když se proces vrcholu začne zařadit do fronty; Při spuštění určitého procesu vrcholu; Po dokončení konkrétního vrcholu.

## <a name="next-steps"></a>Další kroky
* Pokud chcete protokolovat diagnostické informace, přečtěte si téma [Zobrazení protokolů diagnostiky pro Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Pokud chcete zobrazit komplexnější dotaz, přejděte k tématu [Analýza webových protokolů pomocí Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Podrobnosti o úlohách najdete v tématu [použití prohlížeče úloh a zobrazení úloh pro úlohy Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md) .
