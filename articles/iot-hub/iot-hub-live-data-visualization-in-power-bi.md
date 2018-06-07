---
title: Vizualizace dat v reálném čase s daty ze snímačů z Azure IoT Hub – Power BI | Microsoft Docs
description: Pomocí Power BI vizualizovat data teploty a vlhkosti, který se shromažďují ze senzoru a odesílá do služby Azure IoT hub.
author: rangv
manager: ''
keywords: vizualizace dat reálném čase, vizualizace dat za provozu, vizualizace dat snímačů
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: a3c54fe635fe0f8988c321684a815e9896922587
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634341"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Vizualizovat data snímačů v reálném čase ze služby Azure IoT Hub pomocí Power BI

![Diagram začátku do konce](media/iot-hub-get-started-e2e-diagram/4.png)


[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Co se naučíte

Zjistíte, jak k vizualizaci dat snímačů v reálném čase, který Azure IoT hub přijímá pomocí Power BI. Pokud budete chtít zkusit znázorňovat data ve službě IoT hub s webovými aplikacemi, naleznete v tématu [použití Azure Web Apps k vizualizaci dat snímačů v reálném čase ze služby Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

## <a name="what-you-do"></a>Co dělat

- Služby IoT hub připravte pro přístup k datům přidáním skupiny příjemců.
- Vytvoření, konfiguraci a spusťte úlohu služby Stream Analytics pro přenos dat ze služby IoT hub ke svému účtu Power BI.
- Vytvoření a publikování sestavy Power BI můžete vizualizovat data.

## <a name="what-you-need"></a>Co potřebujete

- Kurz [nastavit vaše zařízení](iot-hub-raspberry-pi-kit-node-get-started.md) dokončit, která zahrnuje následující požadavky:
  - Aktivní předplatné Azure.
  - V rámci svého předplatného služby Azure IoT hub.
  - Klientská aplikace, která odesílá zprávy do služby Azure IoT hub.
- Účet Power BI. ([Vyzkoušejte zdarma Power BI](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Vytvoření, konfigurace a spuštění úlohy Stream Analytics

### <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics

1. Na webu [Azure Portal](https://portal.azure.com) klikněte na **Vytvořit prostředek** > **Internet věcí** > **Úloha Stream Analytics**.
1. Zadejte o úloze následující informace.

   **Název úlohy:** Název, který chcete úloze dát. Název musí být globálně jedinečný.

   **Skupina prostředků**: použijte stejnou skupinu prostředků, která používá službu IoT hub.

   **Umístění**: používalo stejné umístění jako vaší skupiny prostředků.

   **Připnout na řídicí panel:** Zaškrtněte tuto možnost pro snadný přístup k centru IoT z řídicího panelu.

   ![Vytvořit úlohu služby Stream Analytics v Azure](media/iot-hub-live-data-visualization-in-power-bi/2_create-stream-analytics-job-azure.png)

1. Klikněte na možnost **Vytvořit**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Přidání vstupu úlohy Stream Analytics

1. Spusťte úlohu služby Stream Analytics.
1. V části **Topologie úlohy** klikněte na **Vstupy**.
1. V **vstupy** podokně klikněte na tlačítko **přidat**a potom zadejte následující informace:

   **Vstupní alias**: jedinečný odkaz pro vstup.

   **Zdroj**: vyberte **služby IoT hub**.

   **Skupiny příjemců**: Vyberte skupinu příjemců, kterou jste právě vytvořili.
1. Klikněte na možnost **Vytvořit**.

   ![Přidat vstup do úlohy Stream Analytics v Azure](media/iot-hub-live-data-visualization-in-power-bi/3_add-input-to-stream-analytics-job-azure.png)

### <a name="add-an-output-to-the-stream-analytics-job"></a>Přidání vstupu úlohy Stream Analytics

1. V části **Topologie úlohy** klikněte na **Výstupy**.
1. V **výstupy** podokně klikněte na tlačítko **přidat**a potom zadejte následující informace:

   **Alias pro výstup:** Jedinečný alias pro výstup.

   **Jímky**: vyberte **Power BI**.
1. Klikněte na tlačítko **Authorize**a pak se přihlaste ke svému účtu Power BI.
1. Po ověření, zadejte následující informace:

   **Skupina pracovního prostoru**: Vyberte pracovní prostor cílové skupiny.

   **Název datové sady**: Zadejte název datové sady.

   **Název tabulky**: Zadejte název tabulky.
1. Klikněte na možnost **Vytvořit**.

   ![Přidat výstup do úlohy Stream Analytics v Azure](media/iot-hub-live-data-visualization-in-power-bi/4_add-output-to-stream-analytics-job-azure.png)

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurace dotazu pro úlohu Stream Analytics

1. V části **Topologie úlohy** klikněte na **Dotaz**.
1. Nahraďte `[YourInputAlias]` názvem aliasu pro vstup úlohy.
1. Nahraďte `[YourOutputAlias]` názvem aliasu pro výstup.
1. Klikněte na **Uložit**.

   ![Přidat dotaz do úlohy Stream Analytics v Azure](media/iot-hub-live-data-visualization-in-power-bi/5_add-query-stream-analytics-job-azure.png)

### <a name="run-the-stream-analytics-job"></a>Spuštění úlohy Stream Analytics

V úloze Stream Analytics klikněte na **Spustit** > **Nyní** > **Spustit**. Jakmile se úloha úspěšně spustí, stav úlohy se změní ze **Zastaveno** na **Spuštěno**.

![Spustit úlohu služby Stream Analytics v Azure](media/iot-hub-live-data-visualization-in-power-bi/6_run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Vytvoření a publikování sestavy Power BI k vizualizaci dat

1. Zkontrolujte, jestli že ukázkové aplikace běží na vašem zařízení. Pokud není, můžete se podívat do kurzy pod [nastavit vaše zařízení](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started).
1. Přihlaste se k vaší [Power BI](https://powerbi.microsoft.com/en-us/) účtu.
1. Přejděte do pracovního prostoru skupiny, které jste nastavili při vytváření výstupu pro úlohu služby Stream Analytics.
1. Klikněte na tlačítko **streamování datové sady**.

   Měli byste vidět datovou sadu určenou při vytváření výstupu pro úlohu služby Stream Analytics.
1. V části **AKCE** kliknutím na první ikonu vytvořte sestavu.

   ![Vytvoření sestavy Microsoft Power BI](media/iot-hub-live-data-visualization-in-power-bi/7_create-power-bi-report-microsoft.png)

1. Vytvořte spojnicový graf zobrazující v reálném čase vývoj teploty.
   1. Na stránce pro vytvoření sestavy přidáte spojnicový graf.
   1. V podokně **Pole** rozbalte tabulku, kterou jste určili při vytváření výstupu pro úlohu služby Stream Analytics.
   1. Přetáhněte **EventEnqueuedUtcTime** na **Osu** v podokně **Vizualizace**.
   1. Přetáhněte položku **temperature** na **Hodnoty**.

      Teď se vytvoří spojnicový graf. Na ose x bude datum a čas v časovém pásmu UTC. Na ose y bude hodnota snímače teploty.

      ![Přidání spojnicový graf pro teploty do sestavy Microsoft Power BI](media/iot-hub-live-data-visualization-in-power-bi/8_add-line-chart-for-temperature-to-power-bi-report-microsoft.png)

1. Vytvořte jiný spojnicový graf zobrazující v reálném čase vývoj vlhkosti. To pokud chcete udělat, postupujte podle stejných kroků výše a umístěte **EventEnqueuedUtcTime** na ose x a **vlhkosti** na ose y.

   ![Přidání spojnicový graf pro vlhkosti do sestavy Microsoft Power BI](media/iot-hub-live-data-visualization-in-power-bi/9_add-line-chart-for-humidity-to-power-bi-report-microsoft.png)

1. Kliknutím na **Uložit** sestavu uložte.
1. Klikněte na tlačítko **soubor** > **publikovat na webu**.
1. Klikněte na tlačítko **kód pro vložení vytvořit**a potom klikněte na **publikovat**.

Odkaz na sestavu jste zadali, že můžete sdílet s kýmkoli pro přístup k sestavě a fragmentu kódu pro integraci sestavy na blog nebo Web.

![Publikovat sestavy Microsoft Power BI](media/iot-hub-live-data-visualization-in-power-bi/10_publish-power-bi-report-microsoft.png)

Společnost Microsoft nabízí [mobilních aplikací Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) pro zobrazení a interakci s řídicí panely Power BI a sestavy na vašem mobilním zařízení.

## <a name="next-steps"></a>Další postup

Power BI jste úspěšně použili k vizualizaci dat snímačů v reálném čase ze služby Azure IoT hub.
Neexistuje jiný způsob, jak k vizualizaci dat ze služby Azure IoT Hub. V tématu [použití Azure Web Apps k vizualizaci dat snímačů v reálném čase ze služby Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
