---
title: Integrované přímé metody edgeAgent – Azure IoT Edge
description: Monitorování a správa nasazení IoT Edge pomocí integrovaných přímých metod v modulu runtime agenta IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 57b9d46918414cef9e8cbcffb941b98c98f985ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240350"
---
# <a name="communicate-with-edgeagent-using-built-in-direct-methods"></a>Komunikace s edgeAgent pomocí vestavěných přímých metod

Monitorujte a spravujte nasazení IoT Edge pomocí přímých metod zahrnutých v modulu agenta IoT Edge. Přímé metody jsou implementovány na zařízení a pak lze vyvolat z cloudu. Agent IoT Edge obsahuje přímé metody, které vám pomohou vzdáleně monitorovat a spravovat vaše zařízení IoT Edge.

Další informace o přímých metodách, jak je používat a jak je implementovat ve vlastních modulech, najdete v [tématu Principy a vyvolání přímých metod z ioT hubu](../iot-hub/iot-hub-devguide-direct-methods.md).

Názvy těchto přímých metod jsou zpracovány bez rozlišování velkých a malých písmen.

## <a name="ping"></a>Ping

Metoda **ping** je užitečná pro kontrolu, jestli je IoT Edge spuštěný na zařízení nebo jestli má zařízení otevřené připojení k IoT Hubu. Pomocí této přímé metody ping agenta IoT Edge a získat jeho stav. Úspěšný příkaz ping vrátí prázdnou datovou část a **stav: 200**.

Například:

```azurecli
az iot hub invoke-module-method --method-name 'ping' -n <hub name> -d <device name> -m '$edgeAgent'
```

Na webu Azure Portal vyvoláte metodu s názvem `ping` metody `{}`a prázdnou datovou částjkou JSON .

![Vyvolání přímé metody ping na webu Azure Portal](./media/how-to-edgeagent-direct-method/ping-direct-method.png)

## <a name="restart-module"></a>Restartovat modul

Metoda **RestartModule** umožňuje vzdálenou správu modulů spuštěných na zařízení IoT Edge. Pokud modul hlásí stav selhání nebo jiné chování není v pořádku, můžete aktivovat agenta IoT Edge k jeho restartování. Příkaz úspěšného restartování vrátí prázdnou datovou část a **stav: 200**.

Metoda RestartModule je k dispozici ve verzi IoT Edge verze 1.0.9 a novější. 

Metodu RestartModule direct můžete použít na libovolném modulu spuštěném na zařízení IoT Edge, včetně samotného modulu edgeagent. Pokud však použijete tuto přímou metodu k vypnutí edgeAgent, neobdržíte výsledek úspěchu, protože připojení je přerušeno při restartování modulu.

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

Na webu Azure Portal vyvoláte metodu s názvem `RestartModule` metody a následující datovou částí JSON:

```json
{
    "schemaVersion": "1.0",
    "id": "<module name>"
}
```

![Vyvolat přímou metodu "RestartModule" na webu Azure Portal](./media/how-to-edgeagent-direct-method/restartmodule-direct-method.png)

## <a name="experimental-methods"></a>Experimentální metody

Nové přímé metody možnosti jsou k dispozici jako experimentální funkce pro testování, včetně:

* [UploadLogs](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md): Načíst protokoly modulů a nahrát je do úložiště objektů blob Azure.
* [GetTaskStatus](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#gettaskstatus): Zkontrolujte stav požadavku na nahrávání protokolů.
* [GetLogs](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#getlogs): Načíst protokoly modulu v řádku v odpovědi přímé metody.

## <a name="next-steps"></a>Další kroky

[Vlastnosti agenta IoT Edge a dvojčata modulů centra IoT Edge](module-edgeagent-edgehub.md)
