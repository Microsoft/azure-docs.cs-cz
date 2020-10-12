---
title: Komunikační protokoly a porty služby Azure IoT Hub | Microsoft Docs
description: Příručka pro vývojáře – popisuje podporované komunikační protokoly pro komunikaci mezi zařízeními a cloudem a čísla portů, která musí být otevřená.
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
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 1792535fab79ed20bdf77f96b4fc39f13b0c7bbb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90015992"
---
# <a name="reference---choose-a-communication-protocol"></a>Referenční informace – volba komunikačního protokolu

IoT Hub umožňuje zařízením používat ke komunikaci ze strany zařízení následující protokoly:

* [MQTT](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf)
* MQTT přes WebSockets
* [AMQP](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
* AMQP přes WebSockets
* HTTPS

Informace o tom, jak tyto protokoly podporují konkrétní funkce IoT Hub, najdete v tématu [pokyny pro komunikaci mezi zařízeními a cloudem](iot-hub-devguide-d2c-guidance.md) a [pokyny pro komunikaci z cloudu na zařízení](iot-hub-devguide-c2d-guidance.md).

Následující tabulka uvádí doporučení nejvyšší úrovně pro výběr protokolu:

| Protokol | Pokud byste měli zvolit tento protokol |
| --- | --- |
| MQTT <br> MQTT přes WebSocket |Použijte na všech zařízeních, která nevyžadují připojení více zařízení (s vlastními přihlašovacími údaji pro jednotlivá zařízení) přes stejné připojení TLS. |
| AMQP <br> AMQP přes WebSocket |Používejte pro pole a cloudové brány, abyste mohli využít výhod multiplexování připojení mezi zařízeními. |
| HTTPS |Používá se pro zařízení, která nepodporují jiné protokoly. |

Při volbě protokolu pro komunikaci na straně zařízení Vezměte v úvahu následující body:

* **Vzor typu cloud-zařízení**. Protokol HTTPS nemá účinný způsob implementace nabízených oznámení serveru. Pokud například používáte protokol HTTPS, zařízení se IoT Hub dotazování na zprávy typu cloud-zařízení. Tento přístup je neefektivní pro zařízení i IoT Hub. V rámci aktuálních pokynů protokolu HTTPS by se každé zařízení mělo dotazovat na zprávy každých 25 minut nebo i déle. MQTT a AMQP nabízená oznámení serveru podporují při přijímání zpráv z cloudu na zařízení. Umožňují okamžité vložení zpráv z IoT Hub do zařízení. Pokud je latence doručení obavou, MQTT nebo AMQP jsou nejlepší protokoly, které se mají použít. Pro zřídka připojená zařízení funguje i HTTPS.

* **Brány polí**. MQTT a HTTPS podporují jenom jednu identitu zařízení (ID zařízení plus přihlašovací údaje) na připojení TLS. Z tohoto důvodu se tyto protokoly nepodporují u [scénářů pro pole brány](iot-hub-devguide-endpoints.md#field-gateways) , které vyžadují multiplexování zpráv pomocí více identit zařízení v rámci jednoho nebo více fondů nadřazených připojení k IoT Hub. Takové brány můžou používat protokol, který podporuje více identit zařízení na připojení, jako je AMQP, pro jejich nadřazený provoz.

* **Zařízení s nízkou úrovní prostředků**. Knihovny MQTT a HTTPS mají menší nároky než knihovny AMQP. Pokud má zařízení omezené prostředky (například méně než 1 MB paměti RAM), může se jednat o jedinou dostupnou implementaci protokolu.

* **Procházení sítě**. Standardní protokol AMQP používá port 5671 a MQTT naslouchá na portu 8883. Použití těchto portů může způsobit problémy v sítích, které jsou zavřené do jiných protokolů než HTTPS. V tomto scénáři použijte MQTT přes WebSockets, AMQP přes WebSockets nebo HTTPS.

* **Velikost datové části**. MQTT a AMQP jsou binární protokoly, jejichž výsledkem jsou kompaktnější datové části, než HTTPS.

> [!WARNING]
> Při použití protokolu HTTPS by se každé zařízení mělo dotazovat na zprávy z cloudu na zařízení, a to více než jednou za 25 minut. V případě vývoje se každé zařízení může dotazovat častěji, pokud je to potřeba.

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="port-numbers"></a>Čísla portů

Zařízení můžou komunikovat s IoT Hub v Azure pomocí různých protokolů. Volba protokolu se obvykle řídí konkrétními požadavky řešení. V následující tabulce jsou uvedeny Odchozí porty, které musí být otevřené, aby bylo možné používat konkrétní protokol pro zařízení:

| Protokol | Port |
| --- | --- |
| MQTT |8883 |
| MQTT přes WebSockets |443 |
| AMQP |5671 |
| AMQP přes WebSockets |443 |
| HTTPS |443 |

Po vytvoření centra IoT v oblasti Azure udržuje IoT Hub stejnou IP adresu pro celou dobu životnosti služby IoT Hub. Pokud však společnost Microsoft přesune službu IoT Hub do jiné jednotky škálování za účelem zachování kvality služby, je jim přiřazena nová IP adresa.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak IoT Hub implementuje protokol MQTT, najdete v tématu [komunikace se službou IoT Hub pomocí protokolu MQTT](iot-hub-mqtt-support.md).
