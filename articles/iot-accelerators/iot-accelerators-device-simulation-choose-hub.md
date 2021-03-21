---
title: Použití existujícího centra IoT s řešením pro simulaci zařízení – Azure | Microsoft Docs
description: Tento článek popisuje, jak nakonfigurovat akcelerátor řešení pro simulaci zařízení, aby používal existující IoT Hub.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 1f89e23d7bb279e7cce5c104060cc7898517f8b7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009360"
---
# <a name="use-an-existing-iot-hub-with-the-device-simulation-solution-accelerator"></a>Použití stávajícího centra IoT s akcelerátorem řešení simulace zařízení

Když nasadíte simulaci zařízení, můžete také zvolit nasazení služby IoT Hub pro použití v simulaci. Tato možnost nasadí [Centrum IoT úrovně S2 s jednou jednotkou škálování](../iot-hub/iot-hub-scaling.md). Pokud tuto volitelnou IoT Hub nasadíte, můžete si i nadále cílit na jiné IoT Hub pro spuštění simulace.

Pokud se rozhodnete nenasadit volitelné IoT Hub, musíte pro všechny simulace, které spustíte, použít vlastní rozbočovač.

Pokud Centrum IoT nemáte, můžete z [Azure Portal](https://portal.azure.com)vždy vytvořit nový.

Pokud chcete použít existující centrum IoT, potřebujete připojovací řetězec pro zásady sdíleného přístupu **iothubowner** . Tento připojovací řetězec můžete získat z [Azure Portal](https://portal.azure.com):

1. Na stránce konfigurace centra na portálu klikněte na **zásady sdíleného přístupu**.

1. Klikněte na **iothubowner**.

1. Zkopírujte buď primární nebo sekundární připojovací řetězec.

[![Získání připojovacího řetězce](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-expanded.png#lightbox)

Použijte připojovací řetězec, který jste zkopírovali při konfiguraci simulace:

![Konfigurace simulace](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation.png)

### <a name="next-steps"></a>Další kroky

V této příručce se naučíte používat existující centrum IoT v simulaci. Dále můžete chtít zjistit, jak [vytvořit pokročilý model zařízení](iot-accelerators-device-simulation-advanced-device.md) pro simulaci.
