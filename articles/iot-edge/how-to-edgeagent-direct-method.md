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
ms.openlocfilehash: 57b9d46918414cef9e8cbcffb941b98c98f985ff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "80240350"
---
# <a name="communicate-with-edgeagent-using-built-in-direct-methods"></a>Komunikace s edgeAgent pomocí integrovaných přímých metod

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

## <a name="experimental-methods"></a>Experimentální metody

Nové možnosti přímé metody jsou k dispozici jako experimentální funkce k testování, včetně:

* [UploadLogs](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md): načtěte protokoly modulu a nahrajte je do Azure Blob Storage.
* [GetTaskStatus](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#gettaskstatus): Projděte si stav žádosti o nahrání protokolů.
* [Getlogs](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#getlogs): načte protokoly modulů vložené do odpovědi přímé metody.

## <a name="next-steps"></a>Další kroky

[Vlastnosti IoT Edge agenta a modulu IoT Edge centra pro vlákna](module-edgeagent-edgehub.md)
