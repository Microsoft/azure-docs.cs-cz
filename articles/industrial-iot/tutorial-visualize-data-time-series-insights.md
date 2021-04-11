---
title: Vizualizace dat OPC UA v Azure Time Series Insights
description: V tomto kurzu se naučíte vizualizovat data pomocí Time Series Insights.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 5bd218c0d94922b6137a964e3993f516216ca4b7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104787668"
---
# <a name="tutorial-visualize-data-with-time-series-insights-tsi"></a>Kurz: vizualizace dat pomocí Time Series Insights (TSI)

Modul vydavatele OPC se připojuje k serverům OPC UA a publikuje data z těchto serverů do IoT Hub. Procesor telemetrie v průmyslové platformě IoT zpracovává tyto události a předávají kontextové vzorky do TSI a dalších uživatelů.  

V této příručce se dozvíte, jak vizualizovat a analyzovat telemetrii OPC UA pomocí tohoto Time Series Insights prostředí.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Všechny kurzy obsahují seznam Shrnutí kroků k dokončení.
> * Každý z těchto bodů odrážek je zarovnán ke klíči H2
> * Použití těchto zelených zaškrtávacích políček v kurzu

## <a name="prerequisite"></a>Požadavek

* Nasazení platformy IIoT pro automatické vytvoření prostředí Time Series Insights
* Data se publikují do IoT Hub

## <a name="time-series-insights-explorer"></a>Průzkumník Time Series Insights

Time Series Insights Explorer je webová aplikace, kterou můžete použít k vizualizaci telemetrie. Chcete-li načíst adresu URL aplikace, otevřete `.env` soubor uložený v důsledku nasazení.  Otevřete prohlížeč na adrese URL v `PCS_TSI_URL` proměnné.  

Před použitím Time Series Insights Exploreru musíte udělit přístup k datům TSI uživatelům, kteří mají oprávnění k vizualizaci dat. Všimněte si, že v případě nového nasazení nejsou ve výchozím nastavení nastaveny žádné zásady přístupu k datům, takže tato data nikdo neuvidí. Zásady přístupu k datům je potřeba nastavit v Azure Portal v prostředí Time Series Insights nasazeném ve skupině prostředků nasazené platformou IIoT, a to takto:

   ![Time Series Insights Explorer 1](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-1.png)

Vyberte zásady přístupu k datům:

   ![Time Series Insights Explorer 2](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-2.png)

Přiřaďte požadované uživatele:

   ![Time Series Insights Explorer 3](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-3.png)


V Průzkumníkovi TSI si všimněte nepřiřazených instancí časových řad. Instance TSI odpovídá řadě čas/hodnota pro konkrétní datový bod pocházející z publikovaného uzlu na serveru OPC. Instance TSI v tomto datovém bodě OPC UA je jednoznačně identifikována EndpointId, SubscriptionId a NodeId. Modely instancí TSI se automaticky zjišťují a zobrazují v Průzkumníkovi v závislosti na datech telemetrie, která se ingestují z centra událostí procesoru telemetrie IIoT Platform.

   ![Time Series Insights Explorer 4](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-0.png)

Data telemetrie lze v grafu vizuálně vyhodnotit tak, že kliknete pravým tlačítkem na instanci TSI a vyberete hodnotu. Časový rámec, který se má použít v grafu, lze upravit v pravém horním rohu. Hodnota více instancí může být vizuálně vizuálního výběru na základě stejného časového intervalu.

Další informace najdete v tématu [rychlý Start: prozkoumání Azure Time Series Insights Preview.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)

## <a name="define-and-apply-a-new-model"></a>Definování a použití nového modelu

Vzhledem k tomu, že instance telemetrie jsou nyní právě v nezpracovaném formátu, musí být kontextem příslušné 

Podrobné informace o modelech TSI najdete [v tématu model časové řady v Azure Time Series Insights Preview](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm) .

1. Krok 1 – na kartě model v Průzkumníkovi Definujte novou hierarchii pro ingestovaná data telemetrie. Hierarchie je Logická stromová struktura, která umožňuje uživateli vložit meta-informace požadované pro intuitivnější navigaci prostřednictvím instancí TSI. uživatel může vytvořit, odstranit nebo upravit šablony hierarchie, které mohou být později vytvořeny pro různé instance TSI.

   ![Krok 1](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-1.png)

2. Krok 2 – definujte nový typ pro hodnoty. V našem příkladu zpracováváme jenom číselné datové typy.

   ![Krok 2](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-2.png)

3. Krok 3 – vyberte novou instanci TSI, která musí být zařazená do kategorie v dřív definované hierarchii.

   ![Krok 3](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-3.png)

4. Krok 4 – vyplnění vlastností instancí – název, popis, hodnota dat a také pole hierarchie, aby se shodovala s logickou strukturou 

   ![Krok 4](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-4.png)

5. Krok 5 – opakování kroku 5 pro všechny instance Nezařazeno do kategorie TSI

   ![Krok 5](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-5.png)

6. Krok 6 – zpátky na hlavní stránce Průzkumníka TSI, Projděte si hierarchii instancí kategorií a vyberte hodnoty pro datové body, které se mají analyzovat

   ![Step6](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-6.png)

## <a name="connect-time-series-insights-to-power-bi"></a>Připojit Time Series Insights k Power BI

Time Series Insights prostředí můžete také připojit k Power BI.  Další informace najdete v tématech [Postup připojení TSI k Power BI](https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi) a [vizualizace dat z TSI v Power BI](https://docs.microsoft.com/azure/time-series-insights/concepts-power-bi).


## <a name="next-steps"></a>Další kroky
Teď, když jste se seznámili s tím, jak vizualizovat data v TSI, si můžete prohlédnout úložiště GitHub pro průmyslové IoT:

> [!div class="nextstepaction"]
> [Úložiště GitHub platformy IIoT](https://github.com/Azure/iot-edge-opc-publisher)