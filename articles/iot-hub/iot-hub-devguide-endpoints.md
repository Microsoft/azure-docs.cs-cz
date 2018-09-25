---
title: Vysvětlení koncové body služby Azure IoT Hub | Dokumentace Microsoftu
description: Příručka pro vývojáře – referenční informace o službě IoT Hub zařízení zákazníky i služba koncových bodů.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dobett
ms.openlocfilehash: e6b4ee3425f6a490f33f998cab4f33734b23df22
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982099"
---
# <a name="reference---iot-hub-endpoints"></a>Reference – koncové body IoT Hubu

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>Názvy služby IoT Hub

Název hostitele, který je hostitelem vašich koncových bodů na portálu na vaše centrum služby IoT hub můžete najít **přehled** stránky. Ve výchozím nastavení, jako název DNS služby IoT hub bude vypadat takto: `{your iot hub name}.azure-devices.net`.

Azure DNS můžete použít k vytvoření vlastního názvu DNS pro službu IoT hub. Další informace najdete v tématu popisujícím [použití Azure DNS k určení nastavení vlastní domény pro službu Azure](../dns/dns-custom-domain.md).

## <a name="list-of-built-in-iot-hub-endpoints"></a>Seznam předdefinovaných koncové body IoT Hubu

Azure IoT Hub je víceklientská služba, která zpřístupňuje jeho funkce pro různé objekty actor. Následující diagram znázorňuje různé koncové body, aby služba IoT Hub zpřístupní.

![Koncové body IoT Hubu][img-endpoints]

Následující seznam popisuje koncové body:

* **Poskytovatel prostředků**. Poskytovatel prostředků služby IoT Hub zpřístupní [Azure Resource Manageru] [ lnk-arm] rozhraní. Toto rozhraní umožňuje vlastníkům předplatného Azure vytvářet a odstraňovat centra IoT hub a k aktualizaci vlastností centra IoT. Vlastnosti služby IoT Hub určují [zásady zabezpečení na úrovni centra][lnk-accesscontrol], na rozdíl od řízení přístupu na úrovni zařízení a funkční možnosti pro zasílání zpráv typu cloud zařízení a zařízení cloud. Poskytovatel prostředků služby IoT Hub můžete také [exportovat identit zařízení][lnk-importexport].
* **Správa identit zařízení**. Každý IoT hub zveřejňuje sadu koncových bodů HTTPS REST pro správu identit zařízení (vytvářet, načítat, aktualizovat a odstranit). [Identit zařízení] [ lnk-device-identities] se používají pro účely řízení přístupu a ověřování zařízení.
* **Správa dvojčete zařízení**. Každé centrum IoT zpřístupňuje řadu koncový bod HTTPS REST služby směřujících k dotazu a aktualizace [dvojčata zařízení][lnk-twins] (aktualizace značky a vlastnosti).
* **Úlohy správy**. Každý IoT hub zpřístupní koncový bod HTTPS REST služby přístupem k dotazování a správu sady [úlohy][lnk-jobs].
* **Koncové body zařízení**. Pro každé zařízení v registru identit služby IoT Hub zveřejňuje sadu koncových bodů:

  * *Odesílání zpráv typu zařízení cloud*. Zařízení používá tento koncový bod pro [odesílání zpráv typu zařízení cloud][lnk-d2c].
  * *Příjem zpráv typu cloud zařízení*. Zařízení s používá tento koncový bod pro příjem cílové [zprávy typu cloud zařízení][lnk-c2d].
  * *Zahájení nahrávání souborů*. Zařízení s používá tento koncový bod pro příjem identifikátor URI SAS úložiště Azure pomocí služby IoT Hub [nahrát soubor][lnk-upload].
  * *Načíst a aktualizovat vlastnosti dvojčat zařízení*. Zařízení používá tento koncový bod pro přístup k jeho [dvojče zařízení][lnk-twins]jeho vlastnosti.
  * *Přijímat žádosti o přímé metody*. Zařízení používá k naslouchání pro tento koncový bod [přímá metoda][lnk-methods]vaší žádosti.

    Tyto koncové body jsou přístupné přes [protokoly MQTT v3.1.1][lnk-mqtt], HTTPS 1.1 a [protokolu AMQP 1.0] [ lnk-amqp] protokoly. Je také k dispozici prostřednictvím protokolu AMQP [objekty Websocket] [ lnk-websockets] na portu 443.

* **Koncové body služby**. Každý IoT hub zveřejňuje sadu koncových bodů pro back-endem řešení pro komunikaci ve vašich zařízeních. S jednou výjimkou tyto koncové body jsou dostupná jenom v případě použití [AMQP] [ lnk-amqp] protokolu. Koncový bod vyvolání metody je přístupná přes protokol HTTPS.
  
  * *Příjem zpráv typu zařízení cloud*. Tento koncový bod je kompatibilní s [Azure Event Hubs][lnk-event-hubs]. Back-end služby ji můžete použít ke čtení [zpráv typu zařízení cloud] [ lnk-d2c] zaslaná z vašich zařízení. Můžete vytvořit vlastní koncové body ve službě IoT hub kromě této integrovaný koncový bod.
  * *Odesílat zprávy typu cloud zařízení a přijímat doručování potvrzování*. Tyto koncové body umožňují vaší back-end řešení odesílat spolehlivé [zprávy typu cloud zařízení][lnk-c2d]a přijímat odpovídající doručení nebo vypršení platnosti potvrzování.
  * *Dostávat oznámení soubor*. Tento koncový bod zasílání zpráv můžete dostávat upozornění z vašich zařízení úspěšně nahrát soubor. 
  * *Přímá volání metody*. Tento koncový bod umožňuje službě back-end, který má být vyvolán [přímá metoda] [ lnk-methods] na zařízení.
  * *Monitorování událostí operace příjmu*. Tento koncový bod umožňuje přijímat operace sledování událostí, pokud byl nakonfigurován váš IoT hub a vygenerovat je. Další informace najdete v tématu [monitorování operací služby IoT Hub][lnk-operations-mon].

[Sad SDK Azure IoT] [ lnk-sdks] článek popisuje různé způsoby pro přístup k tyto koncové body.

Použít všechny koncové body IoT Hubu [TLS] [ lnk-tls] protokol a žádný koncový bod je někdy zveřejněné na nešifrované/nezabezpečené kanály.

## <a name="custom-endpoints"></a>Vlastní koncové body

Můžete propojit existující služby Azure ve vašem předplatném tak, aby fungoval jako koncové body pro směrování zpráv služby IoT hub. Tyto koncové body fungují jako koncové body služby a slouží jako jímky pro směrování zpráv. Zařízení nemůže zapisovat přímo do další koncové body. Další informace o [směrování zpráv](../iot-hub/iot-hub-devguide-messages-d2c.md).

Následující služby Azure IoT Hub aktuálně podporuje jako další koncové body:

* Kontejnery služby Azure Storage
* Event Hubs
* Fronty služby Service Bus
* Témata služby Service Bus

Omezení pro počet koncových bodů můžete přidat, naleznete v tématu [kvóty a omezování][lnk-devguide-quotas].

## <a name="field-gateways"></a>Bran v terénu

V řešení IoT *hraniční brána* umístěná mezi vaším zařízením a vaše koncové body IoT Hubu. To je obvykle nachází v blízkosti vašich zařízení. Vaše zařízení komunikují přímo s brány pole pomocí protokolu podporovaných zařízení. Brána pole se připojí k koncový bod služby IoT Hub pomocí protokolu, který je podporovaný službou IoT Hub. Brána pole může být vyhrazené hardwarové zařízení nebo počítač nízkým výkonem softwarem vlastní bránu.

Můžete použít [Azure IoT Edge] [ lnk-iot-edge] implementovat hraniční brána. IoT Edge a nabízí funkce, jako je například multiplexní komunikaci od více zařízení do stejného připojení ke službě IoT Hub.

## <a name="next-steps"></a>Další postup

Další referenční témata v této příručce pro vývojáře IoT Hub patří:

* [Dotazovací jazyk služby IoT Hub pro dvojčata zařízení, úlohy a směrování zpráv][lnk-devguide-query]
* [Kvóty a omezování][lnk-devguide-quotas]
* [Podpora MQTT centra IoT][lnk-devguide-mqtt]

[lnk-iot-edge]: https://github.com/Azure/iot-edge

[img-endpoints]: ./media/iot-hub-devguide-endpoints/endpoints.png
[lnk-amqp]: https://www.amqp.org/
[lnk-mqtt]: http://mqtt.org/
[lnk-websockets]: https://tools.ietf.org/html/rfc6455
[lnk-arm]: ../azure-resource-manager/resource-group-overview.md
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/

[lnk-tls]: https://tools.ietf.org/html/rfc5246


[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-accesscontrol]: iot-hub-devguide-security.md#access-control-and-permissions
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-device-identities]: iot-hub-devguide-identity-registry.md
[lnk-upload]: iot-hub-devguide-file-upload.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md

[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[lnk-operations-mon]: iot-hub-operations-monitoring.md
