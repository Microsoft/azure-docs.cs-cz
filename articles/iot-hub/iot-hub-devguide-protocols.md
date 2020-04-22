---
title: Komunikační protokoly a porty služby Azure IoT Hub | Dokumenty společnosti Microsoft
description: Průvodce pro vývojáře – popisuje podporované komunikační protokoly pro komunikaci mezi zařízeními a cloud-zařízení a čísla portů, která musí být otevřená.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 3e7f31371a0582a6f4941efbfa0087119278d2d1
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729112"
---
# <a name="reference---choose-a-communication-protocol"></a>Reference - výběr komunikačního protokolu

IoT Hub umožňuje zařízením používat ke komunikaci ze strany zařízení následující protokoly:

* [MQTT](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf)
* MQTT přes WebSockets
* [AMQP](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
* AMQP přes WebSockets
* HTTPS

Informace o tom, jak tyto protokoly podporují konkrétní funkce služby IoT Hub, naleznete v [tématu Pokyny pro komunikaci mezi zařízeními](iot-hub-devguide-d2c-guidance.md) a [pokyny pro komunikaci mezi cloudy](iot-hub-devguide-c2d-guidance.md).

V následující tabulce jsou uvedeny doporučení na vysoké úrovni pro výběr protokolu:

| Protocol (Protokol) | Kdy byste měli zvolit tento protokol |
| --- | --- |
| MQTT <br> MQTT přes WebSocket |Používejte na všech zařízeních, která nevyžadují připojení více zařízení (každé s vlastními přihlašovacími údaji pro zařízení) přes stejné připojení TLS. |
| AMQP <br> AMQP přes websocket |Využijte výhod multiplexování připojení mezi zařízeními pomocí na polích a cloudových branách. |
| HTTPS |Používá se pro zařízení, která nepodporují jiné protokoly. |

Při výběru protokolu pro komunikaci na straně zařízení zvažte následující body:

* **Vzor cloud-to-device**. Protokol HTTPS nemá efektivní způsob, jak implementovat nabízenou nabídku serveru. V důsledku toho, když používáte protokol HTTPS, zařízení dotazování IoT Hub pro zprávy z cloudu na zařízení. Tento přístup je neefektivní pro zařízení i IoT Hub. Podle aktuálních pokynů HTTPS by každé zařízení mělo dotazovat na zprávy každých 25 minut nebo více. MQTT a AMQP server podpory push při příjmu zpráv z cloudu na zařízení. Umožňují okamžité nabízení zpráv z ioT hubu do zařízení. Pokud je problém latence doručení, MQTT nebo AMQP jsou nejlepší protokoly použít. Pro zřídka připojená zařízení funguje také protokol HTTPS.

* **Polní brány**. MQTT a HTTPS podporují pouze jednu identitu zařízení (ID zařízení plus pověření) na připojení TLS. Z tohoto důvodu tyto protokoly nejsou podporovány pro [scénáře brány pole,](iot-hub-devguide-endpoints.md#field-gateways) které vyžadují multiplexní zprávy pomocí více identit zařízení přes jeden nebo fond upstream připojení k centru IoT Hub. Tyto brány mohou používat protokol, který podporuje více identit zařízení na připojení, jako je AMQP, pro jejich upstream provozu.

* **Zařízení s nízkými zdroji**. Knihovny MQTT a HTTPS mají menší nároky než knihovny AMQP. V důsledku toho, pokud zařízení má omezené prostředky (například méně než 1 MB paměti RAM), tyto protokoly mohou být pouze implementace protokolu k dispozici.

* **Průchod sítě**. Standardní protokol AMQP používá port 5671 a MQTT naslouchá na portu 8883. USe těchto portů může způsobit problémy v sítích, které jsou uzavřeny pro protokoly bez protokolu HTTPS. V tomto scénáři použijte MQTT přes websockets, AMQP přes WebSockets nebo HTTPS.

* **Velikost datové části**. MQTT a AMQP jsou binární protokoly, jejichž výsledkem je kompaktnější datové části než HTTPS.

> [!WARNING]
> Při použití protokolu HTTPS by každé zařízení mělo dotazování na zprávy z cloudu na zařízení maximálně jednou za 25 minut. Ve vývoji může každé zařízení v případě potřeby častěji dotazovat.

## <a name="port-numbers"></a>Čísla portů

Zařízení můžou komunikovat s IoT Hubem v Azure pomocí různých protokolů. Volba protokolu je obvykle řízena specifickými požadavky řešení. V následující tabulce jsou uvedeny odchozí porty, které musí být otevřené, aby zařízení bylo možné používat určitý protokol:

| Protocol (Protokol) | Port |
| --- | --- |
| MQTT |8883 |
| MQTT přes WebSockets |443 |
| AMQP |5671 |
| AMQP přes WebSockets |443 |
| HTTPS |443 |

Po vytvoření centra IoT v oblasti Azure, ioT hub udržuje stejnou IP adresu po dobu životnosti tohoto centra IoT. Pokud však Microsoft přesune službu IoT hub do jiné jednotky škálování, aby byla zachována kvalita služeb, bude mu přiřazena nová IP adresa.

## <a name="next-steps"></a>Další kroky

Další informace o implementaci služby IoT Hub protokol MQTT najdete v tématu [Komunikace s centrem IoT pomocí protokolu MQTT](iot-hub-mqtt-support.md).
