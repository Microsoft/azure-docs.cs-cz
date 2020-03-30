---
title: Vizualizace dat v reálném čase v centru Azure IoT Hub – Power BI
description: Pomocí Power BI můžete vizualizovat data o teplotě a vlhkosti, která se shromažďují ze senzoru a odesílají do vašeho centra Azure IoT hub.
author: robinsh
keywords: vizualizace dat v reálném čase, vizualizace živých dat, vizualizace dat senzorů
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 6/06/2019
ms.author: robinsh
ms.openlocfilehash: f0b909d10790511408e090546fd3359889ea5aca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954631"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Vizualizace dat senzorů v reálném čase z Azure IoT Hub pomocí Power BI

![Diagram koncového bodu](./media/iot-hub-live-data-visualization-in-power-bi/1_end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Co se naučíte

Dozvíte se, jak vizualizovat data senzorů v reálném čase, která vaše centrum Azure IoT hub obdrží pomocí Power BI. Pokud se chcete pokusit vizualizovat data ve vašem centru IoT pomocí webové aplikace, přečtěte si informace [o tom, že pomocí webové aplikace můžete vizualizovat data senzorů v reálném čase z Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

## <a name="what-you-do"></a>Co děláte

* Přidejte skupinu spotřebitelů, abyste si připravili centrum IoT hub pro přístup k datům.

* Vytvořte, nakonfigurujte a spusťte úlohu Stream Analytics pro přenos dat z centra IoT hub u vašeho účtu Power BI.

* Vytvořte a publikujte sestavu Power BI pro vizualizaci dat.

## <a name="what-you-need"></a>Co potřebujete

* Dokončete kurz [online simulátoru Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) nebo jeden z výukových programů zařízení; například [Raspberry Pi s node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Tyto články pokrývají následující požadavky:
  
  * Aktivní předplatné Azure.
  * Azure IoT hub v rámci vašeho předplatného.
  * Klientská aplikace, která odesílá zprávy do centra Azure IoT hub.

* Účet Power BI. [(Vyzkoušejte Power BI zdarma)](https://powerbi.microsoft.com/)

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Vytvoření, konfigurace a spuštění úlohy Stream Analytics

Začněme vytvořením úlohy Stream Analytics. Po vytvoření úlohy definujete vstupy, výstupy a dotaz použitý k načtení dat.

### <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics

1. Na [webu Azure Portal](https://portal.azure.com)vyberte **Vytvořit úlohu** > Internet**of Things** > **Stream Analytics**.

2. Zadejte o úloze následující informace.

   **Název úlohy:** Název, který chcete úloze dát. Název musí být globálně jedinečný.

   **Skupina prostředků**: Použijte stejnou skupinu prostředků, kterou používá vaše centrum IoT Hub.

   **Umístění:** Použijte stejné umístění jako skupina prostředků.

   ![Vytvoření úlohy Stream Analytics v Azure](./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job-azure.png)

3. Vyberte **Vytvořit**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Přidání vstupu úlohy Stream Analytics

1. Otevřete úlohu Stream Analytics.

2. V části **Topologie úlohy**vyberte **Vstupy**.

3. V podokně **Vstupy** vyberte **Přidat vstup datového proudu**a v rozevíracím seznamu vyberte **IoT Hub.** V novém vstupním podokně zadejte následující informace:

   **Vstupní alias**: Zadejte jedinečný alias pro vstup.

   **Poskytněte službu IoT Hub ze svého předplatného:** Vyberte toto přepínací tlačítko.

   **Předplatné**: Vyberte předplatné Azure, které používáte pro tento kurz.

   **IoT Hub**: Vyberte službu IoT Hub, kterou používáte pro tento kurz.

   **Koncový bod:** vyberte **Zasílání zpráv**.

   **Název zásad sdíleného přístupu**: Vyberte název zásady sdíleného přístupu, které má úloha Stream Analytics použít pro vaše centrum IoT. V tomto kurzu můžete vybrat *službu*. Zásady *služby* se ve výchozím nastavení vytvářejí v nových službách IoT hub a udělují oprávnění k odesílání a přijímání na koncových bodech na straně cloudu vystavených službou IoT hub. Další informace najdete [v tématu Řízení přístupu a oprávnění](iot-hub-devguide-security.md#access-control-and-permissions).

   **Klíč zásad sdíleného přístupu**: Toto pole je automaticky vyplněno na základě vašeho výběru pro název zásady sdíleného přístupu.

   **Skupina spotřebitelů**: Vyberte skupinu spotřebitelů, kterou jste vytvořili dříve.

   Všechna ostatní pole ponechejte ve výchozím nastavení.

   ![Přidání vstupu do úlohy Stream Analytics v Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job-azure.png)

4. Vyberte **Uložit**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Přidání vstupu úlohy Stream Analytics

1. V části **Topologie úlohy**vyberte **Výstupy**.

2. V podokně **Výstupy** vyberte **Přidat** a **Power BI**.

3. V **podokně Power BI – Nový výstup** vyberte **Autorizovat** a postupujte podle pokynů pro přihlášení k účtu Power BI.

4. Po přihlášení k Power BI zadejte následující informace:

   **Alias výstupu**: Jedinečný alias výstupu.

   **Pracovní prostor skupiny:** Vyberte pracovní prostor cílové skupiny.

   **Název datové sady**: Zadejte název datové sady.

   **Název tabulky**: Zadejte název tabulky.

   ![Přidání výstupu do úlohy Stream Analytics v Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job-azure.png)

5. Vyberte **Uložit**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurace dotazu pro úlohu Stream Analytics

1. V části **Topologie úlohy** vyberte **Dotaz**.

2. Nahraďte `[YourInputAlias]` názvem aliasu pro vstup úlohy.

3. Nahraďte `[YourOutputAlias]` názvem aliasu pro výstup.

   ![Přidání dotazu k úloze Stream Analytics v Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-query-stream-analytics-job-azure.png)

4. Vyberte **Uložit**.

### <a name="run-the-stream-analytics-job"></a>Spuštění úlohy Stream Analytics

V úloze Stream Analytics vyberte **Přehled**a pak vyberte **Spustit** > **nyní** > **.** Jakmile se úloha úspěšně spustí, stav úlohy se změní ze **Zastaveno** na **Spuštěno**.

![Spuštění úlohy Stream Analytics v Azure](./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Vytvoření a publikování sestavy Power BI pro vizualizaci dat

1. Zkontrolujte, zda je v zařízení spuštěna ukázková aplikace. Pokud ne, můžete odkazovat na výukové programy v části [Nastavení zařízení](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started).

2. Přihlaste se ke svému účtu [Power BI](https://powerbi.microsoft.com/en-us/).

3. Vyberte pracovní prostor, který jste **použili, Můj pracovní prostor**.

4. Vyberte **datové sady**.

   Měli byste vidět datovou sadu, kterou jste zadali při vytváření výstupu pro úlohu Stream Analytics.

5. U vytvořené datové sady vyberte **Přidat sestavu** (první ikonu vpravo od názvu datové sady).

   ![Vytvoření sestavy Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/start-power-bi.png)

6. Vytvořte spojnicový graf zobrazující v reálném čase vývoj teploty.

   1. V podokně **Vizualizace** na stránce vytvoření sestavy vyberte ikonu spojnicového grafu a přidejte spojnicový graf.

   2. V podokně **Pole** rozbalte tabulku, kterou jste určili při vytváření výstupu pro úlohu služby Stream Analytics.

   3. Přetáhněte **EventEnqueuedUtcTime** na **Osu** v podokně **Vizualizace**.

   4. Přetáhněte položku **temperature** na **Hodnoty**.

      Vytvoří spojnicový graf. Na ose x bude datum a čas v časovém pásmu UTC. Na ose y bude hodnota snímače teploty.

      ![Přidání spojnicového grafu teploty do sestavy Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temp.png)

7. Vytvořte jiný spojnicový graf zobrazující v reálném čase vývoj vlhkosti. Chcete-li to provést, postupujte podle výše uvedených kroků a umístěte **EventEnqueuedUtcTime** na osu x a **vlhkost** na ose y.

   ![Přidání spojnicového grafu vlhkosti do sestavy Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-humidity.png)

8. Chcete-li sestavu uložit, vyberte **uložit.**

9. V levém podokně vyberte **Sestavy** a pak vyberte sestavu, kterou jste právě vytvořili.

10. Vyberte **Publikovat soubor** > **na webu**.

11. Vyberte **Vytvořit kód pro vložení**a pak vyberte **Publikovat**.

Je k dispozici odkaz na sestavu, který můžete sdílet s kýmkoli pro přístup k sestavám a fragment kódu, který můžete použít k integraci sestavy do vašeho blogu nebo webu.

![Publikování sestavy Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-publish.png)

Microsoft také nabízí [mobilní aplikace Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) pro prohlížení a interakci s řídicími panely AHI a sestavami na vašem mobilním zařízení.

## <a name="next-steps"></a>Další kroky

Power BI jste úspěšně využili k vizualizaci dat senzorů v reálném čase z centra Azure IoT hub.

Další způsob vizualizace dat z Azure IoT Hub najdete v [tématu Použití webové aplikace k vizualizaci dat senzorů v reálném čase z Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
