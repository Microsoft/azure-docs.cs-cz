---
title: Použít stávající služby IoT hub s řešením simulace zařízení – Azure | Dokumentace Microsoftu
description: Tento článek popisuje, jak nakonfigurovat akcelerátor řešení simulace zařízení používat stávající služby IoT Hub.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 07/06/2018
ms.topic: conceptual
ms.openlocfilehash: ee96173ca5f36dee0f08c38e4b6e29da6fee804e
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967526"
---
# <a name="use-an-existing-iot-hub-with-the-device-simulation-solution-accelerator"></a>Akcelerátor řešení simulace zařízení pomocí stávající služby IoT hub

Když si zřídíte akcelerátor řešení simulace zařízení, můžete nasadit službu IoT hub ve skupině prostředků akcelerátor řešení pro použití v simulaci.

Pokud není možnost nasadit volitelné služby IoT Hub, musíte použít vlastní rozbočovače pro jakékoli simulace, které provozujete. Pokud se rozhodnete nasadit volitelné služby IoT Hub, můžete použít toto volitelné centrum nebo vlastní rozbočovače.

Pokud nemáte k dispozici služby IoT hub, můžete vždy vytvořit nový štítek z [webu Azure portal](https://portal.azure.com).

Pokud chcete použít existující centrum IoT, potřebujete připojovací řetězec pro **iothubowner** sdílené zásady přístupu. Tento připojovací řetězec z můžete získat [webu Azure portal](https://portal.azure.com):

1. Na stránce Konfigurace centra na portálu klikněte na tlačítko **zásady sdíleného přístupu**.
1. Klikněte na tlačítko **iothubowner**.
1. Zkopírujte primární nebo sekundární připojovací řetězec.

[![Získání připojovacího řetězce](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-expanded.png#lightbox)

Použijte připojovací řetězec, který jste zkopírovali, když konfigurujete simulace:

[![Konfigurace simulace](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation-expanded.png#lightbox)

## <a name="next-steps"></a>Další postup

V této příručce s postupy jste zjistili, jak používat stávající služby IoT hub v simulaci. V dalším kroku můžete chtít zjistěte, jak [konfiguraci vlastního modelu](iot-accelerators-device-simulation-custom-model.md) pro simulaci.
