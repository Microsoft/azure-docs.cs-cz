---
title: Správa zařízení Azure IoT pomocí Průzkumníka iothub | Microsoft Docs
description: Použijte nástroj iothub Průzkumník rozhraní příkazového řádku pro správu zařízení Azure IoT Hub, poskytuje funkci přímé metody a možnosti správy Twin požadované vlastnosti.
author: rangv
manager: nasing
keywords: Správa zařízení Azure iot, správou zařízení azure iot hub, iot správy zařízení, správou zařízení iot hub
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: 0603976312ecab4f6974f51f0e181d2438681255
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634195"
---
# <a name="use-iothub-explorer-for-azure-iot-hub-device-management"></a>Použití iothub Průzkumníka pro správu zařízení Azure IoT Hub

![Diagram začátku do konce](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[iothub-explorer](https://github.com/azure/iothub-explorer) je nástroj příkazového řádku, který spustíte na hostiteli pro správu identit zařízení v registru centra IoT. Součástí možnosti správy, které můžete provádět různé úlohy.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| Možnost správy          | Úkol                                                                                                                            |
|----------------------------|------------------------------------------------------------------------------------------------------------------------------|
| Přímé metody             | Nastavit zařízení fungovat jako je například spuštění nebo zastavení odesílání zpráv nebo restartování zařízení.                                        |
| Vlastnosti Twin potřeby    | Umístěte zařízení do určité stavy, například nastavit DIODU na zelenou nebo nastavení intervalu odeslání telemetrie do 30 minut.         |
| Twin hlášené vlastnosti   | Zjištění hlášené stavu zařízení. Například zařízení ohlásí, že nyní bliká Indikátor.                                    |
| Značky Twin                  | Metadata specifická pro zařízení ukládat v cloudu. Například nasazení umístění prodejních počítače.                         |
| Zprávy typu cloud zařízení   | Odesílání oznámení do zařízení. Například "je velmi pravděpodobně déšť ještě dnes. Nezapomeňte uvést zastřešující."              |
| Dotazy twin zařízení        | Dotaz na všechny dvojčata zařízení k načtení ty s libovolné podmínky, jako je identifikace zařízení, která jsou k dispozici pro použití. |

Podrobnější vysvětlení na rozdíly a pokyny k použití těchto možností najdete v článku [pokyny komunikace zařízení cloud](iot-hub-devguide-d2c-guidance.md) a [Cloud zařízení komunikace pokyny](iot-hub-devguide-c2d-guidance.md).

Dvojčata zařízení jsou dokumenty JSON, které obsahují informace o stavu zařízení (metadata, konfigurace a podmínky). IoT Hub trvá dvojče zařízení pro každé zařízení, která se k němu připojuje. Další informace o dvojčata zařízení najdete v tématu [začít pracovat s dvojčata zařízení](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>Co se naučíte

Zjistíte pomocí různých možností správy na vývojovém počítači iothub-explorer.

## <a name="what-you-do"></a>Co dělat

Spusťte Průzkumníka iothub s různé možnosti správy.

## <a name="what-you-need"></a>Co potřebujete

- Kurz [nastavit vaše zařízení](iot-hub-raspberry-pi-kit-node-get-started.md) dokončit, která zahrnuje následující požadavky:
- Aktivní předplatné Azure.
- V rámci svého předplatného služby Azure IoT hub.
- Klientská aplikace, která odesílá zprávy do služby Azure IoT hub.
- Zajistěte, aby že vaše zařízení se systémem s klienta aplikace v průběhu tohoto kurzu.
- iothub-explorer [nainstalovat iothub-explorer](https://github.com/azure/iothub-explorer) na vývojovém počítači.

## <a name="connect-to-your-iot-hub"></a>Připojení ke službě IoT hub.

Připojení do služby IoT hub spuštěním následujícího příkazu:

```bash
iothub-explorer login <your IoT hub connection string>
```

## <a name="use-iothub-explorer-with-direct-methods"></a>Pomocí Průzkumníka iothub přímé metody

Vyvolání `start` metoda v aplikaci zařízení k odesílání zpráv do služby IoT hub spuštěním následujícího příkazu:

```bash
iothub-explorer device-method <your device Id> start
```

Vyvolání `stop` metoda v aplikaci zařízení zastavit odesílání zpráv do služby IoT hub spuštěním následujícího příkazu:

```bash
iothub-explorer device-method <your device Id> stop
```

## <a name="use-iothub-explorer-with-twins-desired-properties"></a>Pomocí Průzkumníka iothub požadované vlastnosti pro dvojici

Nastavení intervalu požadovanou vlastnost = 3000 spuštěním následujícího příkazu:

```bash
iothub-explorer update-twin <your device id> {\"properties\":{\"desired\":{\"interval\":3000}}}
```

Tuto vlastnost lze číst zařízením.

## <a name="use-iothub-explorer-with-twins-reported-properties"></a>Použití iothub Průzkumníka s vlastnostmi hlášené pro dvojici

Umožňuje získáte vlastnosti hlášených zařízení tak, že spustíte následující příkaz:

```bash
iothub-explorer get-twin <your device id>
```

Jedna z vlastností je $metadata. $lastUpdated který ukazuje čas poslední toto zařízení odeslání nebo přijetí zprávy.

## <a name="use-iothub-explorer-with-twins-tags"></a>Použití iothub Průzkumníka pomocí značek pro dvojici

Zobrazení značek a vlastnosti zařízení tak, že spustíte následující příkaz:

```bash
iothub-explorer get-twin <your device id>
```

Přidání role pole = teploty a vlhkosti do zařízení tak, že spustíte následující příkaz:

```bash
iothub-explorer update-twin <your device id> "{\"tags\":{\"role\":\"temperature&humidity\"}}"
```

## <a name="use-iothub-explorer-with-cloud-to-device-messages"></a>Pomocí Průzkumníka iothub zprávy typu Cloud zařízení

Odeslat zprávu "Hello World" do zařízení tak, že spustíte následující příkaz:

```bash
iothub-explorer send <device-id> "Hello World"
```

V tématu [Průzkumníkovi iothub-odesílat a přijímat zprávy mezi zařízením a IoT Hub](iot-hub-explorer-cloud-device-messaging.md) pro skutečné scénář použití tohoto příkazu.

## <a name="use-iothub-explorer-with-device-twins-queries"></a>Použití iothub Průzkumníka s dotazy dvojčata zařízení

Dotaz na zařízení s značkou role = 'teploty a vlhkosti' spuštěním následujícího příkazu:

```bash
iothub-explorer query-twin "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Dotaz na všechna zařízení kromě těch, které se značkou role = 'teploty a vlhkosti' spuštěním následujícího příkazu:

```bash
iothub-explorer query-twin "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Další postup

Když jste se naučili použití iothub Průzkumníka pomocí různých možností správy.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
