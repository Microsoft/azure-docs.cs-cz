---
title: Azure IoT Edge a Azure IoT Central | Dokumenty společnosti Microsoft
description: Zjistěte, jak používat Azure IoT Edge s aplikací IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 12/12/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 89d068fbd835a5b4f8886a2ed77691f94a701bb0
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759106"
---
# <a name="connect-azure-iot-edge-devices-to-an-azure-iot-central-application"></a>Připojení zařízení Azure IoT Edge k aplikaci Azure IoT Central

*Tento článek se vztahuje na tvůrce řešení a vývojáře zařízení.*

IoT Edge se skládá ze tří součástí:

* **Moduly IoT Edge jsou kontejnery,** které spouštějí služby Azure, partnerské služby nebo vlastní kód. Moduly se nasazují do zařízení IoT Edge a na těchto zařízeních se spouštějí místně.
* **Modul runtime IoT Edge** běží na každém zařízení IoT Edge a spravuje moduly nasazené do každého zařízení.
* **Cloudové rozhraní** umožňuje vzdáleně monitorovat a spravovat zařízení IoT Edge. IoT Central je cloudové rozhraní.

Zařízení **Azure IoT Edge** může být zařízení brány s zařízeními pro příjem dat, která se připojují k zařízení IoT Edge. Tento článek sdílí další informace o vzorcích připojení zařízení pro příjem dat.

**Šablona zařízení** definuje možnosti vašeho zařízení a modulů IoT Edge. Mezi možnosti patří telemetrie, kterou modul odesílá, vlastnosti modulu a příkazy, na které modul reaguje.

## <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>Vztahy navazujících zařízení s bránou a moduly

Zařízení pro příjem dat se můžou prostřednictvím `$edgeHub` modulu připojit k zařízení brány IoT Edge. Toto zařízení IoT Edge se stane transparentní brány v tomto scénáři.

![Diagram transparentní brány](./media/concepts-iot-edge/gateway-transparent.png)

Zařízení pro příjem dat se můžou taky připojit k zařízení brány IoT Edge prostřednictvím vlastního modulu. V následujícím scénáři se zařízení pro příjem dat připojují prostřednictvím vlastního modulu Modbus.

![Diagram připojení vlastního modulu](./media/concepts-iot-edge/gateway-module.png)

Následující diagram znázorňuje připojení k zařízení brány IoT Edge `$edgeHub`prostřednictvím obou typů modulů (vlastní a ).  

![Schéma připojení přes oba připojovací moduly](./media/concepts-iot-edge/gateway-module-transparent.png)

Nakonec se zařízení pro příjem dat mohou připojit k zařízení brány IoT Edge prostřednictvím několika vlastních modulů. Následující diagram znázorňuje zařízení pro připojení prostřednictvím vlastního modulu Modbus, vlastního modulu BLE a modulu. `$edgeHub` 

![Diagram připojení přes více vlastních modulů](./media/concepts-iot-edge/gateway-module2-transparent.png)

## <a name="deployment-manifests-and-device-templates"></a>Manifesty nasazení a šablony zařízení

V IoT Edge můžete nasadit a spravovat obchodní logiku ve formě modulů. Moduly IoT Edge jsou nejmenší výpočetní jednotkou spravovanou ioT Edge a můžou obsahovat služby Azure (například Azure Stream Analytics) nebo vlastní kód specifický pro řešení. Informace o vývoji, nasazování a údržbě modulů, které jsou vyvíjeny, nasazovány a udržovány, naleznete v [tématu Moduly IoT Edge](../../iot-edge/iot-edge-modules.md).

Na vysoké úrovni manifest nasazení je seznam dvojčat modulu, které jsou nakonfigurovány s jejich požadované vlastnosti. Manifest nasazení říká zařízení IoT Edge (nebo skupině zařízení), které moduly nainstalovat a jak je nakonfigurovat. Manifesty nasazení zahrnují požadované vlastnosti pro každý dvojče modulu. Zařízení IoT Edge hlásí ohlášené vlastnosti pro každý modul.

Pomocí kódu sady Visual Studio vytvořte manifest nasazení. Další informace najdete v [tématu Azure IoT Edge for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

Ve Službě Azure IoT Central můžete importovat manifest nasazení a vytvořit šablonu zařízení. Následující vývojový diagram ukazuje životní cyklus manifestu nasazení v IoT Central.

![Vývojový diagram životního cyklu manifestu nasazení](./media/concepts-iot-edge/dmflow.png)

IoT Plug and Play (náhled) modeluje zařízení IoT Edge takto:

* Každá šablona zařízení IoT Edge má model schopností zařízení.
* Pro každý vlastní modul uvedený v manifestu nasazení je generován model schopností modulu.
* Existuje vztah mezi každým modelem schopností modulu a modelem schopností zařízení.
* Model schopnosti modulu implementuje rozhraní modulu.
* Každé rozhraní modulu obsahuje telemetrii, vlastnosti a příkazy.

![Diagram modelování IoT Edge](./media/concepts-iot-edge/edgemodelling.png)

## <a name="iot-edge-gateway-devices"></a>Zařízení brány IoT Edge

Pokud jste jako zařízení brány vybrali zařízení IoT Edge, můžete přidat následné vztahy do modelů schopností zařízení pro zařízení, která chcete připojit k zařízení brány.

## <a name="next-steps"></a>Další kroky

Pokud jste vývojář zařízení, dalším krokem je informace o [typech zařízení brány v IoT Central](./tutorial-define-gateway-device-type.md).
