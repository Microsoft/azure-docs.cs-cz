---
title: Použití existujícího centra IoT hub s řešením Simulace zařízení – Azure | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak nakonfigurovat akcelerátor řešení simulace zařízení pro použití existujícího centra IoT Hub.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 1f89e23d7bb279e7cce5c104060cc7898517f8b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889181"
---
# <a name="use-an-existing-iot-hub-with-the-device-simulation-solution-accelerator"></a>Použití stávajícího centra IoT s akcelerátorem řešení simulace zařízení

Při nasazení simulace zařízení můžete také nasadit centrum IoT, které se bude používat v simulaci. Tato možnost nasazuje [centrum IoT úrovně S2 s jednou jednotkou škálování](../iot-hub/iot-hub-scaling.md). Pokud nasadíte toto volitelné služby IoT hub, můžete se stále rozhodnout cílit na jiné centrum IoT hub pro spuštění simulace.

Pokud se rozhodnete nenasadit volitelné služby IoT Hub, musíte použít vlastní rozbočovač pro všechny simulace, které spustíte.

Pokud nemáte službu IoT hub, můžete vždy vytvořit nové z [webu Azure Portal](https://portal.azure.com).

Chcete-li použít již existující centrum IoT hub, potřebujete připojovací řetězec pro zásady sdíleného přístupu **iothubowner.** Tento připojovací řetězec můžete získat z [webu Azure Portal](https://portal.azure.com):

1. Na konfigurační stránce centra na portálu klikněte na **Zásady sdíleného přístupu**.

1. Klikněte na **iothubowner**.

1. Zkopírujte primární nebo sekundární připojovací řetězec.

[![Získat připojovací řetězec](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-expanded.png#lightbox)

Použijte připojovací řetězec, který jste zkopírovali při konfiguraci simulace:

![Konfigurace simulace](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation.png)

### <a name="next-steps"></a>Další kroky

V tomto návodu jste se naučili používat existující centrum IoT hub v simulaci. Dále se můžete dozvědět, jak [vytvořit pokročilý model zařízení](iot-accelerators-device-simulation-advanced-device.md) pro simulaci.
