---
title: Použití zobrazení provádění vrcholů v Data Lake Tools pro Visual Studio
description: Tento článek popisuje postup použití zobrazení provádění vrcholů na zkoušku úloh Data Lake Analytics.
services: data-lake-analytics
ms.service: data-lake-analytics
author: mumian
ms.author: jgao
ms.reviewer: jasonwhowell
ms.assetid: 5366d852-e7d6-44cf-a88c-e9f52f15f7df
ms.topic: conceptual
ms.date: 10/13/2016
ms.openlocfilehash: 9f834d697c0d3fe537bbdb190b0ba0d0f294ac87
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43041009"
---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>Použití zobrazení provádění vrcholů v Data Lake Tools pro Visual Studio
Další informace o použití zobrazení provádění vrcholů na zkoušku úloh Data Lake Analytics.


## <a name="open-the-vertex-execution-view"></a>Otevřete zobrazení provádění vrcholů
Otevřete úlohu U-SQL v nástrojích Data Lake pro Visual Studio. Klikněte na tlačítko **zobrazení provádění vrcholů** v levém dolním rohu. Můžete být vyzváni, abyste nejdřív načíst profily a může trvat nějakou dobu v závislosti na připojení k síti.

![Nástroje data Lake Analytics zobrazení provádění vrcholů](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>Principy zobrazení provádění vrcholů
Zobrazení provádění vrcholů má tři části:

![Nástroje data Lake Analytics zobrazení provádění vrcholů](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

**Pro výběr vrcholu** na levém umožňuje vybrat vrcholy funkcemi (, jako je prvních 10 data číst, nebo zvolte podle fáze). Jeden z filtrů nejčastěji používaná je zjistit, **vrcholy na kritické cestě**. **Kritickou cestu** je nejdelší řetězec vrcholy úlohy U-SQL. Principy kritickou cestu je užitečné pro optimalizaci vašich úloh tak, že zkontrolujete, které vrcholu zabere nejvíce času.
  
![Nástroje data Lake Analytics zobrazení provádění vrcholů](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)

Zobrazí se podokno nahoře uprostřed **stav všech vrcholů spuštění**.
  
![Nástroje data Lake Analytics zobrazení provádění vrcholů](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)

V dolním podokně center zobrazí informace o všech vrcholů:
* Název procesu: Název instance vrcholů. Se skládá z různých částí v StageName | VertexName | VertexRunInstance. Například vrcholu [62] .v1 SV7_Split zastupuje druhý spuštěné instance (.v1, index počínaje 0) čísla vrcholu 62 v SV7_Split fázi.
* Celkový počet dat pro čtení/Written: Data byla načtených/zapsaných pomocí tohoto vrcholu.
* Stav a její opuštění stavu: Konečný stav po ukončení vrchol.
* Ukončovací kód nebo selhání typ: Chyba při vrcholu se nepovedlo.
* Důvod vytvoření: Proč vrchol byl vytvořen.
* Latence fronty latence/PN prostředků latence/Process: čas potřebný pro vrchol k čekání na prostředky, ke zpracování dat a zůstat ve frontě.
* GUID procesu nebo Tvůrce: Identifikátor GUID aktuálního vrcholu spuštěné nebo jeho autor.
* Verze: N-tém instance spuštěné vrcholu (v systému naplánovat nové instance vrcholu pro mnoho důvodů, třeba převzetí služeb při selhání, výpočetní redundance, atd.)
* Čas vytvoření verze.
* Zpracování vytvořit počáteční čas/Process zařazeno do fronty čas/Process počáteční čas a zpracovávat Complete čas: při vytváření; spuštění procesu vrcholu Při spuštění procesu vrcholů do fronty Při spuštění určitého vrcholu postupu; Po dokončení některých vrcholu.

## <a name="next-steps"></a>Další postup
* Pokud chcete protokolovat diagnostické informace, přečtěte si téma [Zobrazení protokolů diagnostiky pro Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Pokud chcete zobrazit komplexnější dotaz, přejděte k tématu [Analýza webových protokolů pomocí Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Chcete-li zobrazit podrobnosti o úloze, naleznete v tématu [použití prohlížeče úloh a zobrazení úloh pro úlohy Azure Data lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md)
