---
title: Azure IoT Edge a Azure IoT Central | Microsoft Docs
description: Pochopte, jak použít Azure IoT Edge s aplikací IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 12/19/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- device-developer
- iot-edge
ms.openlocfilehash: 9a7c886ba4dd6e7ab4bd62700f5437855a16a5ad
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2020
ms.locfileid: "97796563"
---
# <a name="connect-azure-iot-edge-devices-to-an-azure-iot-central-application"></a>Připojení zařízení Azure IoT Edge k aplikaci Azure IoT Central

*Tento článek se týká tvůrců řešení a vývojářů zařízení.*

IoT Edge se skládá ze tří součástí:

* **Moduly IoT Edge** jsou kontejnery, ve kterých běží služby Azure, partnerské služby nebo vlastní kód. Moduly se nasazují na zařízení IoT Edge a spustí se místně na těchto zařízeních.
* Modul **runtime IoT Edge** běží na každém zařízení IoT Edge a spravuje moduly nasazené na každé zařízení.
* **Cloudové rozhraní** umožňuje vzdáleně monitorovat a spravovat IoT Edge zařízení. IoT Central je cloudové rozhraní.

Zařízení **Azure IoT Edge** může být zařízení brány se zařízeními, která se připojují k IoT Edgemu zařízení. Tento článek sdílí Další informace o vzorech připojení k navazujícím zařízením.

**Šablona zařízení** definuje možnosti zařízení a IoT Edge moduly. Mezi možnosti patří telemetrie, kterou modul odesílá, vlastnosti modulu a příkazy, na které modul reaguje.

## <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>Vztahy navazujících zařízení s bránou a moduly

Podřízená zařízení se můžou prostřednictvím modulu připojit k zařízení IoT Edge brány `$edgeHub` . Toto zařízení IoT Edge se v tomto scénáři stávají transparentní bránou.

![Diagram transparentní brány](./media/concepts-iot-edge/gateway-transparent.png)

Podřízená zařízení se taky můžou připojit k zařízení IoT Edge brány prostřednictvím vlastního modulu. V následujícím scénáři se pro zařízení se zařízením připojí vlastní modul Modbus.

![Diagram připojení vlastního modulu](./media/concepts-iot-edge/gateway-module.png)

Následující diagram znázorňuje připojení k zařízení IoT Edge brány prostřednictvím obou typů modulů (vlastní a `$edgeHub` ).  

![Diagram připojení prostřednictvím obou připojovacích modulů](./media/concepts-iot-edge/gateway-module-transparent.png)

V konečném případě se mohou zařízení pro příjem dat připojit k zařízení IoT Edge brány prostřednictvím několika vlastních modulů. Následující diagram zobrazuje zařízení, která se připojují prostřednictvím vlastního modulu Modbus, vlastního modulu v police a `$edgeHub` modulu. 

![Diagram připojení přes více vlastních modulů](./media/concepts-iot-edge/gateway-module2-transparent.png)

## <a name="deployment-manifests-and-device-templates"></a>Manifesty nasazení a šablony zařízení

V IoT Edge můžete nasadit a spravovat obchodní logiku ve formě modulů. IoT Edge moduly jsou nejmenší jednotkou výpočtu spravovanou pomocí IoT Edge a můžou obsahovat služby Azure (například Azure Stream Analytics) nebo vlastní kód specifický pro řešení. Pokud chcete pochopit, jak se moduly vyvíjí, nasazují a udržují, přečtěte si téma [IoT Edge moduly](../../iot-edge/iot-edge-modules.md).

Manifest nasazení je na vysoké úrovni seznamem vláken modulu, které jsou nakonfigurovány s požadovanými vlastnostmi. Manifest nasazení oznamuje IoT Edge zařízení (nebo skupině zařízení), které moduly se mají nainstalovat, a jak je nakonfigurovat. Manifesty nasazení obsahují požadované vlastnosti pro každý modul s dvojitou vlastností. IoT Edge zařízení hlásí zpět ohlášené vlastnosti pro každý modul.

Pomocí Visual Studio Code vytvořit manifest nasazení. Další informace najdete v tématu [Azure IoT Edge Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

V Azure IoT Central můžete importovat manifest nasazení a vytvořit šablonu zařízení. Následující vývojový diagram znázorňuje životní cyklus manifestu nasazení v IoT Central.

![Vývojový diagram životního cyklu manifestu nasazení](./media/concepts-iot-edge/dmflow.png)

IoT Central modeluje IoT Edge zařízení následujícím způsobem:

* Každá šablona zařízení IoT Edge má model zařízení.
* Pro každý vlastní modul uvedený v manifestu nasazení je vygenerován model schopností modulu.
* Mezi jednotlivými modely schopností modulu a modelem zařízení je vytvořen vztah.
* Model schopnosti modulu implementuje rozhraní modulů.
* Každé rozhraní modulů obsahuje telemetrii, vlastnosti a příkazy.

![Diagram modelování IoT Edge](./media/concepts-iot-edge/edgemodelling.png)

## <a name="iot-edge-gateway-devices"></a>Zařízení IoT Edge brány

Pokud jste jako zařízení brány vybrali IoT Edge zařízení, můžete pro zařízení, která chcete připojit k zařízení brány, přidat podřízené vztahy k modelům zařízení.

## <a name="next-steps"></a>Další kroky

Pokud jste vývojářem zařízení, navržený další krok se dozvíte o [typech zařízení brány v IoT Central](./tutorial-define-gateway-device-type.md).
