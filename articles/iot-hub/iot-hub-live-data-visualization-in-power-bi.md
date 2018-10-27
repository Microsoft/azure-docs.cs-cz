---
title: Vizualizace dat v reálném čase s daty ze snímačů ze služby Azure IoT Hub – Power BI | Dokumentace Microsoftu
description: Pomocí Power BI k vizualizaci dat teploty a vlhkosti, který se shromažďují ze senzoru a odeslané do služby Azure IoT hub.
author: rangv
keywords: vizualizace dat reálném čase, vizualizace dynamických dat, vizualizace dat ze senzorů
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: a533bd6ee447479f08add23833bf5acdde5c4d40
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50155095"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Vizualizace dat snímače v reálném čase ze služby Azure IoT Hub pomocí Power BI

![Diagram začátku do konce](./media/iot-hub-live-data-visualization-in-power-bi/1_end-to-end-diagram.png)


[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Co se naučíte

Se dozvíte, jak k vizualizaci dat snímačů v reálném čase, který obdrží služby Azure IoT hub s využitím Power BI. Pokud budete chtít zkusit porážky vizualizace dat ve službě IoT hub s využitím Web Apps, najdete v [pomocí Azure Web Apps k vizualizaci dat snímačů v reálném čase ze služby Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

## <a name="what-you-do"></a>Co můžete dělat

* Připravte se službu IoT hub pro přístup k datům tak, že přidáte skupinu příjemců.

* Vytvořte, nakonfigurujte a spusťte úlohu Stream Analytics pro přenos dat ze služby IoT hub ke svému účtu Power BI.

* Vytvoření a publikování sestav Power BI k vizualizaci dat.

## <a name="what-you-need"></a>Co potřebujete

 * Kurz [nastavit zařízení](iot-hub-raspberry-pi-kit-node-get-started.md) dokončeno, která zahrnuje následující požadavky:

   * Aktivní předplatné Azure.
   * Azure IoT hub v rámci vašeho předplatného.
   * Klientská aplikace, která odesílá zprávy do služby Azure IoT hub.

* Účet Power BI. ([Zdarma vyzkoušejte si Power BI](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Vytvořte, nakonfigurujte a spusťte úlohu Stream Analytics

Začněme vytvořením úlohy Stream Analytics. Když vytvoříte úlohu, definujete vstupy, výstupy a dotaz, který chcete načíst data.

### <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics

1. Na webu [Azure Portal](https://portal.azure.com) klikněte na **Vytvořit prostředek** > **Internet věcí** > **Úloha Stream Analytics**.

2. Zadejte o úloze následující informace.

   **Název úlohy:** Název, který chcete úloze dát. Název musí být globálně jedinečný.

   **Skupina prostředků**: použijte stejnou skupinu prostředků, která používá službu IoT hub.

   **Umístění**: určit stejné umístění jako skupina prostředků.

   **Připnout na řídicí panel:** Zaškrtněte tuto možnost pro snadný přístup k centru IoT z řídicího panelu.

   ![Vytvoření úlohy Stream Analytics v Azure](./media/iot-hub-live-data-visualization-in-power-bi/2_create-stream-analytics-job-azure.png)

3. Klikněte na možnost **Vytvořit**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Přidání vstupu úlohy Stream Analytics

1. Otevřete úlohu Stream Analytics.

2. V části **Topologie úlohy** klikněte na **Vstupy**.

3. V **vstupy** podokně klikněte na tlačítko **přidat**a potom zadejte následující informace:

   **Vstupní alias**: jedinečný alias pro vstup.

   **Zdroj**: vyberte **služby IoT hub**.

   **Skupina uživatelů**: Vyberte skupinu příjemců, který jste právě vytvořili.

4. Klikněte na možnost **Vytvořit**.

   ![Přidat vstup do úlohy Stream Analytics v Azure](./media/iot-hub-live-data-visualization-in-power-bi/3_add-input-to-stream-analytics-job-azure.png)

### <a name="add-an-output-to-the-stream-analytics-job"></a>Přidání vstupu úlohy Stream Analytics

1. V části **Topologie úlohy** klikněte na **Výstupy**.

2. V **výstupy** podokně klikněte na tlačítko **přidat**a potom zadejte následující informace:

   **Alias pro výstup:** Jedinečný alias pro výstup.

   **Jímka**: vyberte **Power BI**.

3. Klikněte na tlačítko **Authorize**a pak se přihlaste ke svému účtu Power BI.

4. Po ověření zadejte následující informace:

   **Pracovní prostor skupiny**: Vyberte pracovní prostor vaší cílové skupině.

   **Název datové sady**: Zadejte název datové sady.

   **Název tabulky**: Zadejte název tabulky.

5. Klikněte na možnost **Vytvořit**.

   ![Přidat výstup do úlohy Stream Analytics v Azure](./media/iot-hub-live-data-visualization-in-power-bi/4_add-output-to-stream-analytics-job-azure.png)

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurace dotazu pro úlohu Stream Analytics

1. V části **Topologie úlohy** klikněte na **Dotaz**.

2. Nahraďte `[YourInputAlias]` názvem aliasu pro vstup úlohy.

3. Nahraďte `[YourOutputAlias]` názvem aliasu pro výstup.

4. Klikněte na **Uložit**.

   ![Přidat dotaz do úlohy Stream Analytics v Azure](./media/iot-hub-live-data-visualization-in-power-bi/5_add-query-stream-analytics-job-azure.png)

### <a name="run-the-stream-analytics-job"></a>Spuštění úlohy Stream Analytics

V úloze Stream Analytics klikněte na **Spustit** > **Nyní** > **Spustit**. Jakmile se úloha úspěšně spustí, stav úlohy se změní ze **Zastaveno** na **Spuštěno**.

![Spustit úlohu Stream Analytics v Azure](./media/iot-hub-live-data-visualization-in-power-bi/6_run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Vytvoření a publikování sestav Power BI k vizualizaci dat

1. Ujistěte se, že je ukázková aplikace spuštěná ve vašem zařízení. Pokud ne, můžete se podívat do kurzů v rámci [nastavit zařízení](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started).

2. Přihlaste se ke svému účtu [Power BI](https://powerbi.microsoft.com/en-us/).

3. Přejděte do pracovního prostoru skupiny, které jste nastavili při vytváření výstup úlohy Stream Analytics.

4. Klikněte na tlačítko **streamované datové sady**.

   Měli byste vidět datovou sadu určenou při vytváření výstupu pro úlohu služby Stream Analytics.

5. V části **AKCE** kliknutím na první ikonu vytvořte sestavu.

   ![Vytvoření sestavy Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/7_create-power-bi-report-microsoft.png)

6. Vytvořte spojnicový graf zobrazující v reálném čase vývoj teploty.

   1. Na stránce vytváření sestav přidejte spojnicový graf.

   2. V podokně **Pole** rozbalte tabulku, kterou jste určili při vytváření výstupu pro úlohu služby Stream Analytics.
   
   3. Přetáhněte **EventEnqueuedUtcTime** na **Osu** v podokně **Vizualizace**.
   
   4. Přetáhněte položku **temperature** na **Hodnoty**.

      Vytvoří spojnicový graf. Na ose x bude datum a čas v časovém pásmu UTC. Na ose y bude hodnota snímače teploty.

      ![Přidání spojnicového grafu pro teploty do sestavy Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/8_add-line-chart-for-temperature-to-power-bi-report-microsoft.png)

7. Vytvořte jiný spojnicový graf zobrazující v reálném čase vývoj vlhkosti. Chcete-li to provést, postupujte podle stejných kroků výše a umístěte **EventEnqueuedUtcTime** na ose x a **vlhkosti** na ose y.

   ![Přidání spojnicového grafu pro vlhkost do sestavy Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/9_add-line-chart-for-humidity-to-power-bi-report-microsoft.png)

8. Kliknutím na **Uložit** sestavu uložte.

9. Klikněte na tlačítko **souboru** > **publikovat na webu**.

10. Klikněte na tlačítko **vytvořit kód pro vložení**a potom klikněte na tlačítko **publikovat**.

Zadáte odkaz na sestavu, že můžete sdílet s kýmkoli pro přístup k sestavě a fragment kódu pro integraci sestavy do blogu nebo webu.

![Publikování sestav Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/10_publish-power-bi-report-microsoft.png)

Microsoft taky nabízí [mobilních aplikacích Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) pro zobrazení a interakci s Power BI řídicí panely a sestavy na mobilním zařízení.

## <a name="next-steps"></a>Další postup

Úspěšně jste použili Power BI k vizualizaci dat snímačů v reálném čase ze služby Azure IoT hub.

Existuje alternativní způsob vizualizace dat ze služby Azure IoT Hub. Zobrazit [pomocí Azure Web Apps k vizualizaci dat snímačů v reálném čase ze služby Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]