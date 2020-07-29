---
title: Použití Azure Portal k vyvolání přímých metod
description: Tento článek představuje přehled použití Azure Portal k vyvolání přímých metod.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: how-to
ms.custom: ''
ms.date: 07/24/2020
ms.author: inhenkel
ms.openlocfilehash: 763dd82c8263a5e180468f9fbd7f86526295a80d
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87279283"
---
# <a name="how-to-use-azure-portal-to-invoke-direct-methods"></a>Použití Azure Portal k vyvolání přímých metod

IoT Hub poskytuje možnost vyvolat [přímé metody](/azure/iot-hub/iot-hub-devguide-direct-methods#method-invocation-for-iot-edge-modules) na hraničních zařízeních z cloudu. Modul Live video Analytics na IoT Edge (LVA) zveřejňuje několik [přímých metod](/azure/media-services/live-video-analytics-edge/direct-methods) , které se dají použít k definování, nasazení a vytvoření instance různých pracovních postupů pro analýzu živého videa.

V tomto článku se dozvíte, jak volat přímé volání metod na živé analýzy videí pro IoT Edge modul přes Azure Portal.

## <a name="prerequisites"></a>Požadavky

* Máte živé video analýzy na IoT Edge modulu běžícím na hraničním zařízení, a to pomocí metod popsaných v tématu [rychlý Start: Live video Analytics na IoT Edge](/azure/media-services/live-video-analytics-edge/get-started-detect-motion-emit-events-quickstart) nebo pomocí [portálu.](/azure/media-services/live-video-analytics-edge/deploy-iot-edge-device)

* Porozumíte [Live video Analytics](/azure/media-services/live-video-analytics-edge/overview) a [koncept mediálního grafu](/azure/media-services/live-video-analytics-edge/media-graph-concept).

## <a name="invoking-direct-methods-via-azure-portal"></a>Vyvolání přímých metod prostřednictvím Azure Portal

Každá z [přímých metod](/azure/media-services/live-video-analytics-edge/direct-methods) vystavených modulem lva může být vyvolána prostřednictvím Azure Portal. Následující postup obsahuje podrobné informace o jedné přímé metodě. Pomocí podobných kroků můžete vyvolat další přímé metody. Každá přímá metoda však vyžaduje konkrétní tělo JSON.

Použijte `GraphTopologyList` volání metody k načtení seznamu všech topologií grafů aktuálně nasazených ve službě Live video Analytics v modulu IoT Edge. K vyvolání této přímé metody použijte následující postup:

1. Přihlaste se Azure Portal
1. Najděte příslušnou skupinu prostředků z domovské stránky portálu a Najděte své IoT Hub, nebo pokud víte, že IoT Hub, vyberte ho.
    ![Skupina prostředků na domovské stránce portálu](media/use-azure-portal-to-invoke-directs-methods/portal-rg-home.png)
1. Po na stránce IoT Hub vyberte IoT Edge v části Automatická správa zařízení a uveďte různá ID zařízení. Vyberte příslušné ID zařízení a uveďte moduly běžící na zařízení.
    ![stránka centra IoT Hub](media/use-azure-portal-to-invoke-directs-methods/iot-hub-page.png)
1. Pokud chcete vytvořit stránku konfigurace, vyberte modul Live video Analytics v IoT Edge.<br><br>
    ![Pokud chcete vytvořit stránku konfigurace, vyberte modul Live video Analytics v IoT Edge.](media/use-azure-portal-to-invoke-directs-methods/modules.png)
1. V nabídce přímá metoda vyberte možnost. <br><br>
    ![Klikněte na možnost nabídky přímé metody.](media/use-azure-portal-to-invoke-directs-methods/module-details.png)
    > [!NOTE]
    > Je možné, že budete muset přidat hodnotu v oddílech připojovacího řetězce, jak vidíte na aktuální stránce. V části název nastavení není nutné skrývat ani měnit žádné údaje. Je to v pořádku, aby bylo možné ho zveřejnit.

1. Do pole **název metody** zadejte *GraphTopologyList* .
1. Zkopírujte a vložte JSON níže v poli **datové části** .
    ```json
    {
    "@apiVersion":
    }
    ```
1. V horní části stránky vyberte tlačítko **vyvolat metodu** .<br><br>
    ![vyvolat metodu – tlačítko](media/use-azure-portal-to-invoke-directs-methods/direct-method.png)
1. V oblasti **výsledků** by se měla zobrazit zpráva o stavu 200.<br><br>
    ![časový limit připojení](media/use-azure-portal-to-invoke-directs-methods/connection-timeout.png)

## <a name="responses"></a>Odpovědi

| Podmínka             | Stavový kód | Podrobný kód chyby |
|-----------------------|-------------|---------------------|
| Success               | 200         | –                 |
| Obecné chyby uživatele   | Rozsah 400   |                     |
| Obecné chyby serveru | Rozsah 500   |                     |

## <a name="next-steps"></a>Další kroky

Další přímé metody lze nalézt na stránce [přímé metody](/azure/media-services/live-video-analytics-edge/direct-methods) .

> [!NOTE]
> Instance grafu vytvoří instanci konkrétní topologie, proto před vytvořením instance grafu zkontrolujte, zda máte nastavenou správnou topologii.

[Rychlý Start: detekce událostí vygenerování pohybu](/azure/media-services/live-video-analytics-edge/get-started-detect-motion-emit-events-quickstart) je dobrým odkazem pro porozumění přesné sekvenci volání přímých metod, které se mají provést.
