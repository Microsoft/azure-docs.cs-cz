---
title: Azure IoT Hub komunikační protokoly a porty | Dokumentace Microsoftu
description: Příručka pro vývojáře – popisuje podporované komunikační protokoly pro komunikace typu zařízení cloud a cloud zařízení a čísla portů, musí být otevřený.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 3079b2414c44fc97bc8aff4b207e0943e94c7457
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2018
ms.locfileid: "47584332"
---
# <a name="reference---choose-a-communication-protocol"></a>Odkaz – volba komunikačního protokolu

IoT Hub umožňuje zařízením použít následující protokoly pro komunikaci na straně zařízení:

* [MQTT](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf)
* MQTT přes WebSockets
* [AMQP](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
* AMQP přes WebSockets
* HTTPS

Informace o tom, jak tyto protokoly podporují konkrétní funkce služby IoT Hub najdete v tématu [doprovodných materiálech ke komunikaci typu zařízení cloud](iot-hub-devguide-d2c-guidance.md) a [doprovodných materiálech ke komunikaci typu Cloud zařízení](iot-hub-devguide-c2d-guidance.md).

Následující tabulka poskytuje podrobný doporučení podle vaší volby protokolu:

| Protocol (Protokol) | Pokud byste zvolit tento protokol |
| --- | --- |
| MQTT <br> Protokol MQTT přes protokol WebSocket |Použít na všech zařízeních, které nevyžadují připojení několika zařízení (každý má svoje vlastní přihlašovací údaje podle zařízení) přes stejného připojení protokolu TLS. |
| AMQP <br> AMQP přes WebSocket |Použít na pole a cloudové brány výhod připojení multiplexing napříč zařízeními. |
| HTTPS |Použijte u zařízení, které nemohou podporovat jiné protokoly. |

Pokud zvolíte protokol pro komunikaci na straně zařízení, zvažte následující body:

* **Model typu cloud zařízení**. HTTPS nemá účinný způsob, jak implementovat nabízená serveru. V důsledku toho při použití protokolu HTTPS, zařízení dotazovat služby IoT Hub pro zprávy typu cloud zařízení. Tento přístup je neefektivní pro zařízení a služby IoT Hub. Pod aktuální pokyny pro protokol HTTPS musí každé zařízení dotazovat na zprávy každých 25 minut nebo déle. MQTT a AMQP podporu nabízených oznámení serveru při příjmu zprávy typu cloud zařízení. Umožňují okamžitou nabízených oznámení v ceně zpráv ze služby IoT Hub do zařízení. Pokud latence doručování je důležitý, protokol MQTT nebo AMQP protokoly nejlepší používat. Pro zřídka připojená zařízení funguje i protokol HTTPS.

* **Pole brány**. Při použití MQTT a HTTPS, nemůžete se připojit více zařízení (každý má svoje vlastní přihlašovací údaje podle zařízení) pomocí stejného připojení protokolu TLS. Pro [pole scénářích brány](iot-hub-devguide-endpoints.md#field-gateways) , které vyžadují jedno TLS připojení mezi bránou pole a IoT Hub pro každé z připojených zařízení, tyto protokoly jsou neoptimální.

* **Nedostatek prostředků zařízení**. Knihovny MQTT a HTTPS mají menší nároky na místo než knihovny AMQP. V důsledku toho pokud zařízení má omezené prostředky (například menší než 1 MB paměti RAM), tyto protokoly mohou být k dispozici pouze implementace protokolu.

* **Sítě procházení**. Standardní protokol AMQP používá port 5671 a protokolu MQTT naslouchá na portu 8883. Pomocí těchto portů může způsobit problémy v sítích, které jsou uzavřeny do protokolů není HTTPS. Používání MQTT přes protokoly Websocket, AMQP přes Websocket, nebo HTTPS v tomto scénáři.

* **Velikost datové části**. MQTT a AMQP jsou binární protokoly, které za následek kompaktnějším datových částí než HTTPS.

> [!WARNING]
> Při použití protokolu HTTPS, musí každé zařízení dotazovat na zprávy typu cloud zařízení, každých 25 minut nebo déle. Během vývoje, je přijatelné posílat častěji než každých 25 minut.

## <a name="port-numbers"></a>Čísla portů

Zařízení může komunikovat s centrem IoT v Azure pomocí různých protokolů. Výběr protokolu obvykle doprovází konkrétních požadavků řešení. V následující tabulce jsou uvedeny Odchozí porty, které musí být otevřený, aby zařízení mohlo použít určitý protokol:

| Protocol (Protokol) | Port |
| --- | --- |
| MQTT |8883 |
| MQTT přes WebSockets |443 |
| AMQP |5671 |
| AMQP přes WebSockets |443 |
| HTTPS |443 |

Po vytvoření služby IoT hub v oblasti Azure IoT hub udržuje stejné IP adresy po dobu platnosti této služby IoT hub. Nicméně pokud Microsoft přesune na jiný škálovací jednotku, kterou chcete udržovat kvalitu služby IoT hub, se přiřadí se mu novou IP adresu.

## <a name="next-steps"></a>Další postup

Další informace o tom, jak služby IoT Hub implementuje protokol MQTT, naleznete v tématu [komunikovat s hostiteli IoT hubu pomocí protokolu MQTT](iot-hub-mqtt-support.md).
