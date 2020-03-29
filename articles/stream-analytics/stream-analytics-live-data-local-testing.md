---
title: Testování živých dat pomocí Azure Stream Analytics pro Visual Studio
description: Přečtěte si, jak otestovat úlohu Azure Stream Analytics místně pomocí živých streamovaných dat.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: f2876ea32bdcd900a454ae6b7ac58c11b8ec67c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840481"
---
# <a name="test-live-data-locally-using-azure-stream-analytics-tools-for-visual-studio-preview"></a>Testování živých dat místně pomocí nástrojů Azure Stream Analytics pro Visual Studio (Preview)

Nástroje Azure Stream Analytics pro Visual Studio umožňují testovat úlohy místně z ide pomocí živých datových proudů událostí z Azure Event Hub, IoT Hub a Blob Storage. Místní testování živých dat nemůže nahradit [testování výkonu a škálovatelnosti,](stream-analytics-streaming-unit-consumption.md) které můžete provádět v cloudu, ale můžete ušetřit čas během funkčního testování tím, že nebudete muset odesílat do cloudu pokaždé, když chcete otestovat úlohu Stream Analytics. Tato funkce je ve verzi Preview a neměla by se používat pro produkční úlohy.

## <a name="testing-options"></a>Možnosti testování

Jsou podporovány následující místní možnosti testování:

|**Vstup**  |**Výstup**  |**Typ úlohy**  |
|---------|---------|---------|
|Místní statická data   |  Místní statická data   |   Oblačnost/hrana |
|Živá vstupní data   |  Místní statická data   |   Cloud |
|Živá vstupní data   |  Živá výstupní data   |   Cloud |

## <a name="local-testing-with-live-data"></a>Místní testování s živými daty

1. Po vytvoření [cloudového projektu Azure Stream Analytics ve Visual Studiu](stream-analytics-quick-create-vs.md)otevřete **soubor script.asaql**. Místní testování používá místní vstup a místní výstup ve výchozím nastavení.

   ![Azure Stream Analytics Visual Studio místní vstup a místní výstup](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-local-input-output.png)

2. Chcete-li testovat živá data, zvolte **Použít cloudový vstup** z rozevíracího pole.

   ![Přímý vstup živého cloudu ve Visual Studiu Azure Stream Analytics](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input.png)

3. Nastavte **počáteční čas,** který definuje, kdy úloha začne zpracovávat vstupní data. Úloha může být nutné číst vstupní data předem, aby bylo zajištěno přesné výsledky. Výchozí čas je nastaven na 30 minut před aktuálním časem.

   ![Čas zahájení živého spuštění dat ve Visual Studiu Azure Stream Analytics](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-start-time.png)

4. Klepněte na **tlačítko Spustit místně**. Zobrazí se okno konzoly s metrikami průběhu běhu a úlohy. Pokud chcete proces zastavit, můžete tak učinit ručně. 

   ![Okno živého datového procesu Azure Stream Analytics Visual Studia](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-process-window.png)

   Výsledky výstupu jsou aktualizovány každé tři sekundy s prvními 500 výstupních řádků v okně výsledek místní spuštění a výstupní soubory jsou umístěny v cestě projektu **ASALocalRun** složky. Výstupní soubory můžete také otevřít klepnutím na tlačítko **Otevřít složku výsledků** v okně výsledků místního spuštění.

   ![Azure Stream Analytics Visual Studio živé výsledky dat](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-open-results-folder.png)

5. Pokud chcete výstup výsledků do vašeho cloudu výstup propady, zvolte **Výstup do cloudu** z druhého rozevíracího pole. Power BI a Azure Data Lake Storage nejsou podporované jímky pro výstupy.

   ![Výstup živých dat Azure Stream Analytics Visual Studio do cloudu](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-output.png)
 
## <a name="limitations"></a>Omezení

* Power BI a Azure Data Lake Storage nejsou podporované výstupní propady z důvodu omezení modelu ověřování.

* Pouze možnosti vstupu do cloudu mají podporu [zásad času,](stream-analytics-out-of-order-and-late-events.md) zatímco místní vstupní možnosti nemají.

## <a name="next-steps"></a>Další kroky

* [Vytvoření úlohy Stream Analytics pomocí nástrojů Azure Stream Analytics pro Visual Studio](stream-analytics-quick-create-vs.md)
* [Test Stream Analytics dotazy místně s Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Zobrazení úloh Azure Stream Analytics pomocí Visual Studia](stream-analytics-vs-tools.md)
