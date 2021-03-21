---
title: Vizualizace dat v reálném čase z Azure IoT Hub – Power BI
description: Použijte Power BI k vizualizaci dat o teplotě a vlhkosti shromažďovaných ze senzoru a odeslání do služby Azure IoT Hub.
author: robinsh
keywords: vizualizace dat v reálném čase, vizualizace živých dat, vizualizace dat senzorů
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 6/08/2020
ms.author: robinsh
ms.openlocfilehash: 82caf13618fe8483ab8d3a622c6c0d51ab05a206
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102177330"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Vizualizace dat ze senzorů v reálném čase z Azure IoT Hub pomocí Power BI

![Komplexní diagram](./media/iot-hub-live-data-visualization-in-power-bi/end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Co se naučíte

Naučíte se vizualizovat data ze senzorů v reálném čase, která služba Azure IoT Hub přijímá pomocí Power BI. Pokud se chcete pokusit vizualizovat data ve službě IoT Hub pomocí webové aplikace, přečtěte si téma [použití webové aplikace k vizualizaci dat snímačů v reálném čase z Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

## <a name="what-you-do"></a>Co dělat

* Přidejte skupinu příjemců a připravte si tak přístup k datům do služby IoT Hub.

* Vytvořte, nakonfigurujte a spusťte úlohu Stream Analytics pro přenos dat ze služby IoT Hub do svého účtu Power BI.

* Vytvořte a publikujte sestavu Power BI k vizualizaci dat.

## <a name="what-you-need"></a>Co budete potřebovat

* Dokončete kurz [online simulátoru malin](iot-hub-raspberry-pi-web-simulator-get-started.md) . nebo v některém z kurzů zařízení; například [Malina Pi s node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Tyto články se týkají následujících požadavků:
  
  * Musíte mít aktivní předplatné Azure.
  * Azure IoT Hub v rámci vašeho předplatného.
  * Klientská aplikace, která odesílá zprávy do služby Azure IoT Hub.

* Účet Power BI. ([Vyzkoušejte Power BI zdarma](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Vytvoření, konfigurace a spuštění úlohy Stream Analytics

Pojďme začít vytvořením úlohy Stream Analytics. Po vytvoření úlohy definujete vstupy, výstupy a dotazy použité k načtení dat.

### <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics

1. V [Azure Portal](https://portal.azure.com)vyberte **vytvořit prostředek**  >  **Internet věcí**  >  **Stream Analytics úlohu**.

2. Zadejte o úloze následující informace.

   **Název úlohy:** Název, který chcete úloze dát. Název musí být globálně jedinečný.

   **Skupina prostředků**: použijte stejnou skupinu prostředků, kterou používá IoT Hub.

   **Umístění**: použijte stejné umístění jako vaše skupina prostředků.

   ![Vytvoření úlohy Stream Analytics v Azure](./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job.png)

3. Vyberte **Vytvořit**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Přidání vstupu úlohy Stream Analytics

1. Otevřete Stream Analytics úlohu.

2. V části **Topologie úlohy** vyberte **Vstupy**.

3. V podokně **vstupy** vyberte **Přidat vstup streamu** a pak v rozevíracím seznamu vyberte **IoT Hub** . V podokně nový vstup zadejte následující informace:

   **Vstupní alias**: Zadejte jedinečný alias pro vstup.

   **Vyberte IoT Hub z předplatného**: vyberte tento přepínač.

   **Předplatné**: vyberte předplatné Azure, které používáte pro tento kurz.

   **IoT Hub**: vyberte IoT Hub, které používáte pro tento kurz.

   **Koncový bod:** vyberte **Zasílání zpráv**.

   **Název zásady sdíleného přístupu**: vyberte název zásady sdíleného přístupu, kterou má Stream Analytics úloha použít pro Centrum IoT. Pro tento kurz můžete vybrat *službu*. Zásady *služby* se ve výchozím nastavení vytvoří v nových centrech IoT a udělují oprávnění k posílání a přijímání na koncových bodech na straně cloudu zveřejněné službou IoT Hub. Další informace najdete v tématu [řízení přístupu a oprávnění](iot-hub-devguide-security.md#access-control-and-permissions).

   **Klíč zásad sdíleného přístupu**: Toto pole se vyplní automaticky na základě vašeho výběru pro název zásady sdíleného přístupu.

   **Skupina příjemců**: vyberte skupinu uživatelů, kterou jste předtím vytvořili.

   Ve výchozím nastavení ponechte všechna ostatní pole.

   ![Přidání vstupu do úlohy Stream Analytics v Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job.png)

4. Vyberte **Uložit**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Přidání vstupu úlohy Stream Analytics

1. V části **Topologie úlohy** vyberte **Výstupy**.

2. V podokně **výstupy** vyberte **Přidat** a **Power BI**.

3. V podokně **Power BI – nový výstup** vyberte **autorizovat** a podle pokynů se přihlaste ke svému účtu Power BI.

4. Po přihlášení k Power BI zadejte následující informace:

   **Alias pro výstup**: jedinečný alias pro výstup.

   **Pracovní prostor skupiny**: vyberte pracovní prostor cílové skupiny.

   **Název datové sady**: zadejte název datové sady.

   **Název tabulky**: zadejte název tabulky.

   **Režim ověřování**: ponechte výchozí hodnotu.

   ![Přidání výstupu do úlohy Stream Analytics v Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job.png)

5. Vyberte **Uložit**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurace dotazu pro úlohu Stream Analytics

1. V části **Topologie úlohy** vyberte **Dotaz**.

2. Nahraďte `[YourInputAlias]` názvem aliasu pro vstup úlohy.

3. Nahraďte `[YourOutputAlias]` názvem aliasu pro výstup.

   ![Přidání dotazu do úlohy Stream Analytics v Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-query-to-stream-analytics-job.png)

4. Vyberte **Uložit dotaz**.

### <a name="run-the-stream-analytics-job"></a>Spuštění úlohy Stream Analytics

V Stream Analytics úlohy vyberte **Přehled** a pak vyberte **Spustit**  >    >  .**Spustit**. Jakmile se úloha úspěšně spustí, stav úlohy se změní ze **Zastaveno** na **Spuštěno**.

![Spuštění úlohy Stream Analytics v Azure](./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Vytvoření a publikování sestavy Power BI k vizualizaci dat

Následující kroky ukazují, jak vytvořit a publikovat sestavu pomocí služba Power BI. Pokud chcete v Power BI použít nový vzhled, můžete postupovat podle těchto kroků. Chcete-li porozumět rozdílům a postupům navigace v "novém hledání", přečtěte si téma ["nové hledání" služba Power BI](/power-bi/consumer/service-new-look).

1. Ujistěte se, že je na vašem zařízení spuštěná ukázková aplikace. Pokud ne, můžete se podívat na kurzy v části [nastavení zařízení](./iot-hub-raspberry-pi-kit-node-get-started.md).

2. Přihlaste se ke svému účtu [Power BI](https://powerbi.microsoft.com/en-us/).

3. Vyberte pracovní prostor, který jste použili, **pracovní prostor**.

4. Vyberte **datové sady**.

   Měla by se zobrazit datová sada, kterou jste zadali při vytváření výstupu pro úlohu Stream Analytics.

5. Pro datovou sadu, kterou jste vytvořili, vyberte **Přidat sestavu** (první ikonu napravo od názvu datové sady).

   ![Vytvoření sestavy Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-create-report.png)

6. Vytvořte spojnicový graf zobrazující v reálném čase vývoj teploty.

   1. V podokně **vizualizace** na stránce vytváření sestav vyberte ikonu spojnicového grafu a přidejte spojnicový graf.

   2. V podokně **Pole** rozbalte tabulku, kterou jste určili při vytváření výstupu pro úlohu služby Stream Analytics.

   3. Přetáhněte **EventEnqueuedUtcTime** na **Osu** v podokně **Vizualizace**.

   4. Přetáhněte položku **temperature** na **Hodnoty**.

      Vytvoří spojnicový graf. Na ose x bude datum a čas v časovém pásmu UTC. Na ose y bude hodnota snímače teploty.

      ![Přidání spojnicového grafu pro teplotu do sestavy Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temperature.png)

7. Vytvořte jiný spojnicový graf zobrazující v reálném čase vývoj vlhkosti. Provedete to tak, že kliknete na prázdnou část plátna a podle výše uvedeného postupu umístíte **EventEnqueuedUtcTime** na osu x a **vlhkost** na ose y.

   ![Přidání spojnicového grafu pro vlhkost do sestavy Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-humidity.png)

8. Kliknutím na **Uložit** sestavu uložíte.

9. V levém podokně vyberte **sestavy** a potom vyberte sestavu, kterou jste právě vytvořili.

10. Vyberte **soubor**  >  **publikovat na webu**.

    ![Vyberte možnost publikovat na webu pro sestavu Microsoft Power BI.](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-select-publish-to-web.png)

    > [!NOTE]
    > Pokud obdržíte oznámení o tom, že chcete povolit vytváření kódu pro vložení, budete se muset obrátit na oddělení IT. Aby bylo možné dokončit tento krok, musí být povoleno vytváření kódu pro vložení.
    >
    > ![Obraťte se na oznámení správce.](./media/iot-hub-live-data-visualization-in-power-bi/contact-admin.png)

11. Vyberte **vytvořit kód pro vložení** a pak vyberte **publikovat**.

Máte odkaz na sestavu, který můžete sdílet s kýmkoli pro přístup k sestavám a fragment kódu, který můžete použít k integraci sestavy do svého blogu nebo webu.

![Publikování sestavy Microsoft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-web-output.png)

Microsoft taky nabízí [Power BI mobilní aplikace](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) pro zobrazení a interakci s Power BI řídicími panely a sestavami na vašem mobilním zařízení.

## <a name="next-steps"></a>Další kroky

Úspěšně jste použili Power BI k vizualizaci dat snímačů v reálném čase z Azure IoT Hub.

Další způsob, jak vizualizovat data z Azure IoT Hub, najdete v tématu [použití webové aplikace k vizualizaci dat snímačů v reálném čase z azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
