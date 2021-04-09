---
title: Integrované metody edgeAgent Direct – Azure IoT Edge
description: Monitorování a Správa nasazení IoT Edge pomocí integrovaných přímých metod v modulu runtime agenta IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 52cd7817594c5c2a1d4e3a4ca9c56891df594cd6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201110"
---
# <a name="communicate-with-edgeagent-using-built-in-direct-methods"></a>Komunikace s edgeAgent pomocí integrovaných přímých metod

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Monitorování a Správa nasazení IoT Edge pomocí přímých metod, které jsou součástí modulu IoT Edge agent. Přímé metody jsou implementovány v zařízení a je možné je vyvolávat z cloudu. Agent IoT Edge obsahuje přímé metody, které vám pomůžou vzdáleně monitorovat a spravovat vaše IoT Edge zařízení.

Chcete-li získat další informace o přímých metodách, jejich použití a způsobu jejich implementace ve vlastních modulech, přečtěte si téma [pochopení a vyvolání přímých metod z IoT Hub](../iot-hub/iot-hub-devguide-direct-methods.md).

Názvy těchto přímých metod zpracovávají nerozlišování velkých a malých písmen.

## <a name="ping"></a>Ping

Metoda nástroje **příkazového testu** je užitečná pro kontrolu, zda je na zařízení spuštěný IoT Edge nebo zda má zařízení otevřené připojení k IoT Hub. Pomocí této přímé metody otestujete agenta IoT Edge a získejte jeho stav. Úspěšný test příkazu pro ověřování vrátí prázdnou datovou část a **"stav": 200**.

Například:

```azurecli
az iot hub invoke-module-method --method-name 'ping' -n <hub name> -d <device name> -m '$edgeAgent'
```

V Azure Portal volejte metodu s názvem metody `ping` a prázdnou datovou částí JSON `{}` .

![Vyvolat přímo metodu "příkaz" příkazu "test" v Azure Portal](./media/how-to-edgeagent-direct-method/ping-direct-method.png)

## <a name="restart-module"></a>Restartovat modul

Metoda **RestartModule** umožňuje vzdálenou správu modulů spuštěných na zařízení IoT Edge. Pokud modul hlásí stav selhání nebo jiné špatné chování, můžete aktivovat agenta IoT Edge k jeho restartování. Příkaz k úspěšnému restartování vrátí prázdnou datovou část a **"stav": 200**.

Metoda RestartModule je k dispozici v IoT Edge verze 1.0.9 a novější. 

Metodu RestartModule Direct můžete použít v jakémkoli modulu běžícím na zařízení IoT Edge, včetně samotného modulu edgeAgent. Pokud však použijete tuto přímou metodu pro vypnutí edgeAgent, nepřijdete o úspěch, protože během restartování modulu dojde k přerušení připojení.

Například:

```azurecli
az iot hub invoke-module-method --method-name 'RestartModule' -n <hub name> -d <device name> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "id": "<module name>"
    }
'
```

V Azure Portal volejte metodu s názvem metody `RestartModule` a následující datovou částí JSON:

```json
{
    "schemaVersion": "1.0",
    "id": "<module name>"
}
```

![Vyvolat přímo metodu RestartModule v Azure Portal](./media/how-to-edgeagent-direct-method/restartmodule-direct-method.png)

## <a name="diagnostic-direct-methods"></a>Diagnostické metody Direct

* [GetModuleLogs](how-to-retrieve-iot-edge-logs.md#retrieve-module-logs): načte protokoly modulů vložené do odpovědi přímé metody.
* [UploadModuleLogs](how-to-retrieve-iot-edge-logs.md#upload-module-logs): načtěte protokoly modulu a nahrajte je do Azure Blob Storage.
* [UploadSupportBundle](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics): načtěte protokoly modulů pomocí sady prostředků podpory a nahrajte soubor zip do Azure Blob Storage.
* [GetTaskStatus](how-to-retrieve-iot-edge-logs.md#get-upload-request-status): Projděte si stav protokolů odeslání nebo podpory sady prostředků.

Tyto diagnostické přímé metody jsou k dispozici ve verzi 1.0.10.

## <a name="next-steps"></a>Další kroky

[Vlastnosti IoT Edge agenta a modulu IoT Edge centra pro vlákna](module-edgeagent-edgehub.md)
