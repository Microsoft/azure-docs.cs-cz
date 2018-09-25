---
title: Živá data test místně pomocí nástroje Azure Stream Analytics pro Visual Studio (Preview)
description: Zjistěte, jak k otestování místně pomocí dynamických streamovaných dat úlohy Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: f0a8978a9c2e0538a2e7bc4eab202604913e700b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984156"
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

   ![Azure Stream Analytics Visual Studio místní testování s místní vstup a výstup místní](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-local-input-output.png)

2. Chcete-li otestovat dynamických dat, zvolte **použití cloudu vstup** v rozevíracím.

   ![Azure Stream Analytics Visual Studio místní testování se vstupem živého cloudu](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input.png)


3. Nastavte **čas zahájení** k definování, kdy úloha spustí zpracování vstupní data. Úloha může potřebovat číst vstupní data předem, aby přesné výsledky. Výchozí doba je nastavena na 30 minut před aktuálním časem.

   ![Azure Stream Analytics Visual Studio místní testování s časem zahájení živých dat](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-start-time.png)

4. Klikněte na tlačítko **místní spuštění**. Okna konzoly se zobrazí s běžící metriky průběh a úlohy. Pokud chcete zastavit proces, lze provést ručně. 

   ![Azure Stream Analytics Visual Studio místní testování s oknem procesu živých dat](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-process-window.png)

   Výstupní výsledky se aktualizují s první řádky 500 výstup v okně výsledků místního spuštění každé tři sekundy a výstupní soubory jsou umístěny v cestě projektu **ASALocalRun** složky. Výstupní soubory můžete otevřít také kliknutím **otevřít složku výsledků** tlačítka v okně výsledků místního spuštění.

   ![Azure Stream Analytics Visual Studio místní testování s dynamickými daty otevřete výsledky složky](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-open-results-folder.png)

5. Pokud chcete k vypsání výsledků do výstupní jímky váš cloud, zvolte **výstup do cloudu** z druhého rozevíracího seznamu pole. Power BI a Azure Data Lake Storage nejsou podporované výstupní jímky.

   ![Azure Stream Analytics Visual Studio místní testování s dynamickými daty výstup do cloudu](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-output.png)
 
## <a name="limitations"></a>Omezení

* Power BI a Azure Data Lake Storage nejsou podporované výstupních jímek z důvodu omezení ověření modelu.

* Mít pouze vstupní možnosti cloudu [čas zásady](stream-analytics-out-of-order-and-late-events.md) podporují, zatímco místní možnosti vstupu, tomu tak není.

## <a name="next-steps"></a>Další postup

* [Vytvoření úlohy Stream Analytics pomocí Azure Stream Analytics tools for Visual Studio](stream-analytics-quick-create-vs.md)
* [Instalace nástroje Azure Stream Analytics pro Visual Studio](stream-analytics-tools-for-visual-studio-install.md)
* [Testování dotazů Stream Analytics místně pomocí sady Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Chcete-li zobrazit úlohy Azure Stream Analytics pomocí sady Visual Studio](stream-analytics-vs-tools.md)