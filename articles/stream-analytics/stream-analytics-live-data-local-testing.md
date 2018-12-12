---
title: Test živých dat pomocí služby Azure Stream Analytics pro Visual Studio
description: Zjistěte, jak k otestování místně pomocí dynamických streamovaných dat úlohy Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: ea55d2f96a87503d43a69d288ce85dcff32a39ce
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090325"
---
# <a name="test-live-data-locally-using-azure-stream-analytics-tools-for-visual-studio-preview"></a>Živá data test místně pomocí nástroje Azure Stream Analytics pro Visual Studio (Preview)

Nástroje Azure Stream Analytics pro Visual Studio umožňuje testovat úloh místně z integrovaného vývojového prostředí pomocí datové proudy živé události z Azure Event Hub, IoT Hub a Blob Storage. Živá data nelze nahradit místní testování [testování výkonu a škálovatelnosti](stream-analytics-streaming-unit-consumption.md) můžete provádět v cloudu, ale můžete ušetřit čas při testování funkčnosti tím, že pro odeslání do cloudu pokaždé, když chcete testovat váš Stream Úlohy analýzy. Tato funkce je ve verzi preview a neměl by se používat pro produkční úlohy.

## <a name="testing-options"></a>Možnosti testování

Podporovány jsou následující možnosti místního testování:

|**Input** (Vstup)  |**Výstup**  |**Typ úlohy**  |
|---------|---------|---------|
|Statická místní data   |  Statická místní data   |   Cloud/Edge |
|Za vstupní data   |  Statická místní data   |   Cloud |
|Za vstupní data   |  Za výstupní data   |   Cloud |

## <a name="local-testing-with-live-data"></a>Místní testování s dynamickými daty

1. Po vytvoření [projekt cloudové Azure Stream Analytics v sadě Visual Studio](stream-analytics-quick-create-vs.md), otevřete **script.asaql**. Místní testování používá místní vstup a výstup místní ve výchozím nastavení.

   ![Azure Stream Analytics Visual Studio místní vstup a výstup místní](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-local-input-output.png)

2. Chcete-li otestovat dynamických dat, zvolte **použití cloudu vstup** v rozevíracím.

   ![Vstup za cloud Azure Stream Analytics Visual Studio](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input.png)


3. Nastavte **čas zahájení** k definování, kdy úloha spustí zpracování vstupní data. Úloha může potřebovat číst vstupní data předem, aby přesné výsledky. Výchozí doba je nastavena na 30 minut před aktuálním časem.

   ![Čas zahájení dynamických dat v Azure Stream Analytics Visual Studio](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-start-time.png)

4. Klikněte na tlačítko **místní spuštění**. Okna konzoly se zobrazí s běžící metriky průběh a úlohy. Pokud chcete zastavit proces, lze provést ručně. 

   ![Okno procesu živá data Azure Stream Analytics Visual Studio](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-process-window.png)

   Výstupní výsledky se aktualizují s první řádky 500 výstup v okně výsledků místního spuštění každé tři sekundy a výstupní soubory jsou umístěny v cestě projektu **ASALocalRun** složky. Výstupní soubory můžete otevřít také kliknutím **otevřít složku výsledků** tlačítka v okně výsledků místního spuštění.

   ![Živá data v Azure Stream Analytics Visual Studio otevřete složku výsledků](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-open-results-folder.png)

5. Pokud chcete k vypsání výsledků do výstupní jímky váš cloud, zvolte **výstup do cloudu** z druhého rozevíracího seznamu pole. Power BI a Azure Data Lake Storage nejsou podporované výstupní jímky.

   ![Azure Stream Analytics Visual Studio živá data výstup do cloudu](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-output.png)
 
## <a name="limitations"></a>Omezení

* Power BI a Azure Data Lake Storage nejsou podporované výstupních jímek z důvodu omezení ověření modelu.

* Mít pouze vstupní možnosti cloudu [čas zásady](stream-analytics-out-of-order-and-late-events.md) podporují, zatímco místní možnosti vstupu, tomu tak není.

## <a name="next-steps"></a>Další postup

* [Vytvoření úlohy Stream Analytics pomocí Azure Stream Analytics tools for Visual Studio](stream-analytics-quick-create-vs.md)
* [Instalace nástroje Azure Stream Analytics pro Visual Studio](stream-analytics-tools-for-visual-studio-install.md)
* [Testování dotazů Stream Analytics místně pomocí sady Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Chcete-li zobrazit úlohy Azure Stream Analytics pomocí sady Visual Studio](stream-analytics-vs-tools.md)