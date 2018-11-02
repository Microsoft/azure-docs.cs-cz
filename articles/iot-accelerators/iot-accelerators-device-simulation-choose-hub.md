---
title: Použít stávající služby IoT hub s řešením simulace zařízení – Azure | Dokumentace Microsoftu
description: Tento článek popisuje, jak nakonfigurovat akcelerátor řešení simulace zařízení používat stávající služby IoT Hub.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 38cde750ce07741a433baa1b8607a584f94ad9b1
ms.sourcegitcommit: 3dcb1a3993e51963954194ba2a5e42260d0be258
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50753912"
---
# <a name="use-an-existing-iot-hub-with-the-device-simulation-solution-accelerator"></a>Akcelerátor řešení simulace zařízení pomocí stávající služby IoT hub

Při nasazení simulace zařízení můžete také nasadit služby IoT hub pro použití v simulaci. Tato možnost nasadí [IoT hub úrovně S2 jedné škálovací jednotce je přidružená](../iot-hub/iot-hub-scaling.md). Pokud provádíte nasazení této volitelné služby IoT hub, můžete stále cílit na jiné služby IoT Hub pro spuštění simulace.

Pokud se rozhodnete nasadit volitelné služby IoT Hub, musíte použít vlastní rozbočovače pro jakékoli simulace, které provozujete.

Pokud nemáte k dispozici služby IoT hub, můžete vždy vytvořit nový štítek z [webu Azure portal](https://portal.azure.com).

Pokud chcete použít existující centrum IoT, potřebujete připojovací řetězec pro **iothubowner** sdílené zásady přístupu. Tento připojovací řetězec z můžete získat [webu Azure portal](https://portal.azure.com):

1. Na stránce Konfigurace centra na portálu klikněte na tlačítko **zásady sdíleného přístupu**.

1. Klikněte na tlačítko **iothubowner**.

1. Zkopírujte primární nebo sekundární připojovací řetězec.

[![Získání připojovacího řetězce](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-expanded.png#lightbox)

Použijte připojovací řetězec, který jste zkopírovali, když konfigurujete simulace:

![Konfigurace simulace](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation.png)

### <a name="next-steps"></a>Další postup

V této příručce s postupy jste zjistili, jak používat stávající služby IoT hub v simulaci. V dalším kroku můžete chtít zjistěte, jak [vytvoření modelu pokročilé zařízení](iot-accelerators-device-simulation-advanced-device.md) pro simulaci.
