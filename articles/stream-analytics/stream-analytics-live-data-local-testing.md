---
title: Testování živých dat pomocí Azure Stream Analytics pro Visual Studio
description: Naučte se, jak místně testovat úlohu Azure Stream Analytics pomocí živých streamování dat.
author: ajetasin
ms.author: ajetasi
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 99e9ce85bb1e59b5016e836ff16d03cb3326ec8c
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020362"
---
# <a name="test-live-data-locally-using-azure-stream-analytics-tools-for-visual-studio-preview"></a>Místní testování živých dat pomocí Azure Stream Analyticsch nástrojů pro Visual Studio (Preview)

Azure Stream Analytics Tools for Visual Studio umožňuje testovat úlohy místně z rozhraní IDE pomocí živých streamů událostí z centra událostí Azure, IoT Hub a Blob Storage. Lokální testování dat v reálném čase nemůže nahradit [testování výkonu a škálovatelnosti](stream-analytics-streaming-unit-consumption.md) , které můžete provádět v cloudu, ale můžete ušetřit čas během funkčního testování, aniž by bylo nutné odesílat data do cloudu pokaždé, když chcete testovat úlohu Stream Analytics. Tato funkce je ve verzi Preview a neměla by se používat pro produkční úlohy.

## <a name="testing-options"></a>Možnosti testování

Podporovány jsou následující možnosti místního testování:

|**Vstup**  |**Výstup**  |**Typ úlohy**  |
|---------|---------|---------|
|Místní statická data   |  Místní statická data   |   Cloud/Edge |
|Živá vstupní data   |  Místní statická data   |   Cloud |
|Živá vstupní data   |  Živá výstupní data   |   Cloud |

## <a name="local-testing-with-live-data"></a>Místní testování s dynamickými daty

1. Po vytvoření [cloudového projektu Azure Stream Analytics v aplikaci Visual Studio](stream-analytics-quick-create-vs.md)otevřete **skript Script. asaql**. Místní testování používá ve výchozím nastavení místní vstupní a místní výstup.

   ![Azure Stream Analytics místní vstup a místní výstup sady Visual Studio](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-local-input-output.png)

2. Pokud chcete testovat živá data, vyberte v rozevíracím seznamu **použít vstup z cloudu** .

   ![Vstup Azure Stream Analytics Visual Studio Live Cloud Input](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input.png)

3. Nastavte **čas spuštění** , který se má definovat, kdy se úloha začne zpracovávat vstupní data. Aby bylo zajištěno správné výsledky, úloha může vyžadovat, abyste si před časem četli vstupní data. Výchozí doba je nastavená na 30 minut před aktuálním časem.

   ![Čas spuštění sady Visual Studio Live data Azure Stream Analytics](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-start-time.png)

4. Klikněte na **spustit místně**. Zobrazí se okno konzoly s metrikami průběhu a úloh. Pokud chcete proces zastavit, můžete to provést ručně. 

   ![Azure Stream Analytics okno procesu Visual Studio Live data](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-process-window.png)

   Výsledky výstupu se aktualizují každé tři sekundy s první 500 výstupními řádky v místním okně výsledek spuštění a výstupní soubory jsou umístěny do složky pro cestu k projektu **ASALocalRun** . Můžete také otevřít výstupní soubory kliknutím na tlačítko **Otevřít složku výsledků** v okně výsledky místního spuštění.

   ![Azure Stream Analytics složku výsledků otevřít Visual Studio Live data](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-open-results-folder.png)

5. Pokud chcete výstup výsledků do výstupní jímky cloudu, vyberte z druhého rozevíracího seznamu **výstup do cloudu** . Power BI a Azure Data Lake Storage nejsou podporované výstupní jímky.

   ![Azure Stream Analytics výstup sady Visual Studio Live data do cloudu](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-output.png)
 
## <a name="limitations"></a>Omezení

* Power BI a Azure Data Lake Storage nejsou podporovány výstupní jímky z důvodu omezení modelu ověřování.

* Pouze možnosti cloudového vstupu mají podporu [časových zásad](./stream-analytics-time-handling.md) , zatímco možnosti místního vstupu ne.

## <a name="next-steps"></a>Další kroky

* [Vytvoření úlohy Stream Analytics pomocí nástrojů Azure Stream Analytics pro Visual Studio](stream-analytics-quick-create-vs.md)
* [Testování Stream Analytics dotazů v místním prostředí se sadou Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Použití sady Visual Studio k zobrazení Azure Stream Analyticsch úloh](stream-analytics-vs-tools.md)