---
title: Vizualizace dat v reálném čase s daty ze snímačů ze služby Azure IoT Hub – Power BI | Dokumentace Microsoftu
description: Pomocí Power BI k vizualizaci dat teploty a vlhkosti, který se shromažďují ze senzoru a odeslané do služby Azure IoT hub.
author: robinsh
keywords: vizualizace dat reálném čase, vizualizace dynamických dat, vizualizace dat ze senzorů
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 6/06/2019
ms.author: robinsh
ms.openlocfilehash: 7deb1b501d30c8af0cb190f4722d46435afa9b8e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065811"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Vizualizace dat snímače v reálném čase ze služby Azure IoT Hub pomocí Power BI

![Diagram začátku do konce](./media/iot-hub-live-data-visualization-in-power-bi/1_end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Co se naučíte

Se dozvíte, jak k vizualizaci dat snímačů v reálném čase, který obdrží služby Azure IoT hub s využitím Power BI. Pokud budete chtít zkusit vizualizace dat ve službě IoT hub s webovou aplikací, přečtěte si téma [použijte nějakou webovou aplikaci k vizualizaci dat snímačů v reálném čase ze služby Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

## <a name="what-you-do"></a>Co můžete dělat

* Připravte se službu IoT hub pro přístup k datům tak, že přidáte skupinu příjemců.

* Vytvořte, nakonfigurujte a spusťte úlohu Stream Analytics pro přenos dat ze služby IoT hub ke svému účtu Power BI.

* Vytvoření a publikování sestav Power BI k vizualizaci dat.

## <a name="what-you-need"></a>Co potřebujete

* Dokončení [online simulátor Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) kurzu nebo jeden z kurzů zařízení; například [Raspberry Pi s node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Tyto články zahrnovat následující požadavky:
  
  * Aktivní předplatné Azure.
  * Azure IoT hub v rámci vašeho předplatného.
  * Klientská aplikace, která odesílá zprávy do služby Azure IoT hub.

* Účet Power BI. ([Zdarma vyzkoušejte si Power BI](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Vytvořte, nakonfigurujte a spusťte úlohu Stream Analytics

Začněme vytvořením úlohy Stream Analytics. Když vytvoříte úlohu, definujete vstupy, výstupy a dotaz, který chcete načíst data.

### <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics

1. V [webu Azure portal](https://portal.azure.com)vyberte **vytvořit prostředek** > **Internet of Things** > **úlohy Stream Analytics**.

2. Zadejte o úloze následující informace.

   **Název úlohy**: Název úlohy. Název musí být globálně jedinečný.

   **Skupina prostředků**: Použijte stejnou skupinu prostředků, která používá službu IoT hub.

   **Umístění**: Použijte stejné umístění jako skupina prostředků.

   ![Vytvoření úlohy Stream Analytics v Azure](./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job-azure.png)

3. Vyberte **Vytvořit**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Přidání vstupu úlohy Stream Analytics

1. Otevřete úlohu Stream Analytics.

2. V části **topologie úlohy**vyberte **vstupy**.

3. V **vstupy** vyberte **přidat vstup streamu**a pak vyberte **služby IoT Hub** z rozevíracího seznamu. V podokně nový vstupní zadejte následující informace:

   **Vstupní alias**: Zadejte jedinečný alias pro vstup.

   **Poskytuje služby IoT Hub ze svého předplatného**: Vyberte přepínač.

   **Předplatné**: Vyberte předplatné Azure, které používáte pro účely tohoto kurzu.

   **IoT Hub**: Vyberte centrum IoT používáte pro účely tohoto kurzu.

   **Koncový bod**: Vyberte **zasílání zpráv**.

   **Název zásad sdíleného přístupu**: Vyberte název zásady sdíleného přístupu má úloha Stream Analytics pro účely služby IoT hub. Pro účely tohoto kurzu můžete vybrat *služby*. *Služby* zásada se vytvoří ve výchozím nastavení nového centra IoT hub a udělí oprávnění odesílat a přijímat v koncových bodech na straně cloudu vystavený službou IoT hub. Další informace najdete v tématu [řízení přístupu a oprávnění](iot-hub-devguide-security.md#access-control-and-permissions).

   **Klíč zásad sdíleného přístupu**: Toto pole je automaticky vyplněný na základě vašeho výběru pro název zásad sdíleného přístupu.

   **Skupina uživatelů**: Vyberte skupinu příjemců, který jste vytvořili dříve.

   Ponechte všechna ostatní pole na jejich výchozích hodnotách.

   ![Přidat vstup do úlohy Stream Analytics v Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job-azure.png)

4. Vyberte **Uložit**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Přidání vstupu úlohy Stream Analytics

1. V části **topologie úlohy**vyberte **výstupy**.

2. V **výstupy** vyberte **přidat** a **Power BI**.

3. Na **Power BI – nový výstup** vyberte **Authorize** a postupujte podle pokynů se přihlaste ke svému účtu Power BI.

4. Po přihlášení k Power BI, zadejte následující informace:

   **Alias pro výstup**: Jedinečný alias pro výstup.

   **Pracovní prostor skupiny**: Vyberte pracovní prostor vaší cílové skupině.

   **Název datové sady**: Zadejte název datové sady.

   **Název tabulky**: Zadejte název tabulky.

   ![Přidat výstup do úlohy Stream Analytics v Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job-azure.png)

5. Vyberte **Uložit**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurace dotazu pro úlohu Stream Analytics

1. V části **Topologie úlohy** vyberte **Dotaz**.

2. Nahraďte `[YourInputAlias]` názvem aliasu pro vstup úlohy.

3. Nahraďte `[YourOutputAlias]` názvem aliasu pro výstup.

   ![Přidat dotaz do úlohy Stream Analytics v Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-query-stream-analytics-job-azure.png)

4. Vyberte **Uložit**.

### <a name="run-the-stream-analytics-job"></a>Spuštění úlohy Stream Analytics

V úloze Stream Analytics, vyberte **přehled**a pak vyberte **Start** > **nyní** > **Start**. Jakmile se úloha úspěšně spustí, stav úlohy se změní ze **Zastaveno** na **Spuštěno**.

![Spustit úlohu Stream Analytics v Azure](./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Vytvoření a publikování sestav Power BI k vizualizaci dat

1. Ujistěte se, že je ukázková aplikace spuštěná ve vašem zařízení. Pokud ne, můžete se podívat do kurzů v rámci [nastavit zařízení](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started).

2. Přihlaste se ke svému účtu [Power BI](https://powerbi.microsoft.com/en-us/).

3. Vyberte pracovní prostor, který jste použili, **pracovní prostor**.

4. Vyberte **datových sad**.

   Měli byste vidět datové sady, který jste zadali při vytváření výstup úlohy Stream Analytics.

5. Datové sady, který jste vytvořili, vyberte **přidat sestavu** (první ikona napravo od názvu datové sady).

   ![Vytvoření sestavy Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/start-power-bi.png)

6. Vytvořte spojnicový graf zobrazující v reálném čase vývoj teploty.

   1. Na **vizualizace** podokně na stránku pro vytvoření sestavy, vyberte ikonu spojnicového grafu pro přidání spojnicového grafu.

   2. V podokně **Pole** rozbalte tabulku, kterou jste určili při vytváření výstupu pro úlohu služby Stream Analytics.

   3. Přetáhněte **EventEnqueuedUtcTime** na **Osu** v podokně **Vizualizace**.

   4. Přetáhněte položku **temperature** na **Hodnoty**.

      Vytvoří spojnicový graf. Na ose x bude datum a čas v časovém pásmu UTC. Na ose y bude hodnota snímače teploty.

      ![Přidání spojnicového grafu pro teploty do sestavy Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temp.png)

7. Vytvořte jiný spojnicový graf zobrazující v reálném čase vývoj vlhkosti. Chcete-li to provést, postupujte podle stejných kroků výše a umístěte **EventEnqueuedUtcTime** na ose x a **vlhkosti** na ose y.

   ![Přidání spojnicového grafu pro vlhkost do sestavy Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-humidity.png)

8. Vyberte **Uložit** pro uložení sestavy.

9. Vyberte **sestavy** v levém podokně a pak vyberte sestavu, že jste právě vytvořili.

10. Vyberte **souboru** > **publikovat na webu**.

11. Vyberte **vytvořit kód pro vložení**a pak vyberte **publikovat**.

Už jste zadali odkaz na sestavu, které můžete sdílet s kýmkoli pro přístup k sestavě a fragment kódu, který můžete použít k integraci sestavy do blogu nebo webu.

![Publikování sestav Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-publish.png)

Microsoft taky nabízí [mobilních aplikacích Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) pro zobrazení a interakci s Power BI řídicí panely a sestavy na mobilním zařízení.

## <a name="next-steps"></a>Další postup

Úspěšně jste použili Power BI k vizualizaci dat snímačů v reálném čase ze služby Azure IoT hub.

Dalším způsobem, jak vizualizovat data ze služby Azure IoT Hub, najdete v části [použijte nějakou webovou aplikaci k vizualizaci dat snímačů v reálném čase ze služby Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
