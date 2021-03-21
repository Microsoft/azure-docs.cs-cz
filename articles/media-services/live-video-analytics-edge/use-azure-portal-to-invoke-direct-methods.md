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
ms.openlocfilehash: ddd6a94015829a0b5dd4053f4d3a2fd6d2650d32
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99492650"
---
# <a name="how-to-use-azure-portal-to-invoke-direct-methods"></a>Použití Azure Portal k vyvolání přímých metod

IoT Hub poskytuje možnost vyvolat [přímé metody](../../iot-hub/iot-hub-devguide-direct-methods.md#method-invocation-for-iot-edge-modules) na hraničních zařízeních z cloudu. Modul Live video Analytics na IoT Edge (LVA) zveřejňuje několik [přímých metod](./direct-methods.md) , které se dají použít k definování, nasazení a vytvoření instance různých pracovních postupů pro analýzu živého videa.

V tomto článku se dozvíte, jak volat přímé volání metod na živé analýzy videí pro IoT Edge modul přes Azure Portal.

## <a name="prerequisites"></a>Předpoklady

* Máte živé video analýzy na IoT Edge modulu běžícím na hraničním zařízení, a to pomocí metod popsaných v tématu [rychlý Start: Live video Analytics na IoT Edge](./get-started-detect-motion-emit-events-quickstart.md) nebo pomocí [portálu.](./deploy-iot-edge-device.md)

* Porozumíte [Live video Analytics](./overview.md) a [koncept mediálního grafu](./media-graph-concept.md).

## <a name="invoking-direct-methods-via-azure-portal"></a>Vyvolání přímých metod prostřednictvím Azure Portal

Každá z [přímých metod](./direct-methods.md) vystavených modulem lva může být vyvolána prostřednictvím Azure Portal. Následující postup obsahuje podrobné informace o jedné přímé metodě. Pomocí podobných kroků můžete vyvolat další přímé metody. Každá přímá metoda však vyžaduje konkrétní tělo JSON.

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
    "@apiVersion": "2.0"
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

Další přímé metody lze nalézt na stránce [přímé metody](./direct-methods.md) .

> [!NOTE]
> Instance grafu vytvoří instanci konkrétní topologie, proto před vytvořením instance grafu zkontrolujte, zda máte nastavenou správnou topologii.

[Rychlý Start: detekce událostí vygenerování pohybu](./get-started-detect-motion-emit-events-quickstart.md) je dobrým odkazem pro porozumění přesné sekvenci volání přímých metod, které se mají provést.
