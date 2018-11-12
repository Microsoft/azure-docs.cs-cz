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
ms.openlocfilehash: 43e2101f413985974b964f2261d852692bcac61d
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51251436"
---
# <a name="reference---iot-hub-endpoints"></a>Reference – koncové body IoT Hubu

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>Názvy služby IoT Hub

Název hostitele, který je hostitelem vašich koncových bodů na portálu na vaše centrum služby IoT hub můžete najít **přehled** stránky. Ve výchozím nastavení, jako název DNS služby IoT hub bude vypadat takto: `{your iot hub name}.azure-devices.net`.

Azure DNS můžete použít k vytvoření vlastního názvu DNS pro službu IoT hub. Další informace najdete v tématu popisujícím [použití Azure DNS k určení nastavení vlastní domény pro službu Azure](../dns/dns-custom-domain.md).

## <a name="list-of-built-in-iot-hub-endpoints"></a>Seznam předdefinovaných koncové body IoT Hubu

Azure IoT Hub je víceklientská služba, která zpřístupňuje jeho funkce pro různé objekty actor. Následující diagram znázorňuje různé koncové body, aby služba IoT Hub zpřístupní.

![Koncové body IoT Hubu](./media/iot-hub-devguide-endpoints/endpoints.png)

Následující seznam popisuje koncové body:

* **Poskytovatel prostředků**. Poskytovatel prostředků služby IoT Hub zpřístupní [Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md) rozhraní. Toto rozhraní umožňuje vlastníkům předplatného Azure vytvářet a odstraňovat centra IoT hub a k aktualizaci vlastností centra IoT. Vlastnosti služby IoT Hub určují [zásady zabezpečení na úrovni centra](iot-hub-devguide-security.md#access-control-and-permissions), na rozdíl od řízení přístupu na úrovni zařízení a funkční možnosti pro zasílání zpráv typu cloud zařízení a zařízení cloud. Poskytovatel prostředků služby IoT Hub můžete také [exportovat identit zařízení](iot-hub-devguide-identity-registry.md#import-and-export-device-identities).

* **Správa identit zařízení**. Každý IoT hub zveřejňuje sadu koncových bodů HTTPS REST pro správu identit zařízení (vytvářet, načítat, aktualizovat a odstranit). [Identit zařízení](iot-hub-devguide-identity-registry.md) se používají pro účely řízení přístupu a ověřování zařízení.

* **Správa dvojčete zařízení**. Každý IoT hub zpřístupní koncový bod HTTPS REST služby přístupem k dotazování a aktualizaci sady [dvojčata zařízení](iot-hub-devguide-device-twins.md) (aktualizace značek a vlastnosti).

* **Úlohy správy**. Každý IoT hub zpřístupní koncový bod HTTPS REST služby přístupem k dotazování a správu sady [úlohy](iot-hub-devguide-jobs.md).

* **Koncové body zařízení**. Pro každé zařízení v registru identit služby IoT Hub zveřejňuje sadu koncových bodů:

  * *Odesílání zpráv typu zařízení cloud*. Zařízení používá tento koncový bod pro [odesílání zpráv typu zařízení cloud](iot-hub-devguide-messages-d2c.md).

  * *Příjem zpráv typu cloud zařízení*. Zařízení s používá tento koncový bod pro příjem cílové [zprávy typu cloud zařízení](iot-hub-devguide-messages-c2d.md).

  * *Zahájení nahrávání souborů*. Zařízení s používá tento koncový bod pro příjem identifikátor URI SAS úložiště Azure pomocí služby IoT Hub [nahrát soubor](iot-hub-devguide-file-upload.md).

  * *Načíst a aktualizovat vlastnosti dvojčat zařízení*. Zařízení používá tento koncový bod pro přístup k jeho [dvojče zařízení](iot-hub-devguide-device-twins.md)jeho vlastnosti.

  * *Přijímat žádosti o přímé metody*. Zařízení používá k naslouchání pro tento koncový bod [přímá metoda](iot-hub-devguide-direct-methods.md)vaší žádosti.

    Tyto koncové body jsou přístupné přes [protokoly MQTT v3.1.1](http://mqtt.org/), HTTPS 1.1 a [protokolu AMQP 1.0](https://www.amqp.org/) protokoly. Je také k dispozici prostřednictvím protokolu AMQP [objekty Websocket](https://tools.ietf.org/html/rfc6455) na portu 443.

* **Koncové body služby**. Každý IoT hub zveřejňuje sadu koncových bodů pro back-endem řešení pro komunikaci ve vašich zařízeních. S jednou výjimkou tyto koncové body jsou dostupná jenom v případě použití [AMQP](https://www.amqp.org/) protokolu. Koncový bod vyvolání metody je přístupná přes protokol HTTPS.
  
  * *Příjem zpráv typu zařízení cloud*. Tento koncový bod je kompatibilní s [Azure Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/). Back-end služby ji můžete použít ke čtení [zpráv typu zařízení cloud](iot-hub-devguide-messages-d2c.md) zaslaná z vašich zařízení. Můžete vytvořit vlastní koncové body ve službě IoT hub kromě této integrovaný koncový bod.
  
  * *Odesílat zprávy typu cloud zařízení a přijímat doručování potvrzování*. Tyto koncové body umožňují vaší back-end řešení odesílat spolehlivé [zprávy typu cloud zařízení](iot-hub-devguide-messages-c2d.md)a přijímat odpovídající doručení nebo vypršení platnosti potvrzování.
  
  * *Dostávat oznámení soubor*. Tento koncový bod zasílání zpráv můžete dostávat upozornění z vašich zařízení úspěšně nahrát soubor. 
  
  * *Přímá volání metody*. Tento koncový bod umožňuje službě back-end, který má být vyvolán [přímá metoda](iot-hub-devguide-direct-methods.md) na zařízení.
  
  * *Monitorování událostí operace příjmu*. Tento koncový bod umožňuje přijímat operace sledování událostí, pokud byl nakonfigurován váš IoT hub a vygenerovat je. Další informace najdete v tématu [monitorování operací služby IoT Hub](iot-hub-operations-monitoring.md).

[Sad SDK Azure IoT](iot-hub-devguide-sdks.md) článek popisuje různé způsoby pro přístup k tyto koncové body.

Použít všechny koncové body IoT Hubu [TLS](https://tools.ietf.org/html/rfc5246) protokol a žádný koncový bod je někdy zveřejněné na nešifrované/nezabezpečené kanály.

## <a name="custom-endpoints"></a>Vlastní koncové body

Můžete propojit existující služby Azure ve vašem předplatném tak, aby fungoval jako koncové body pro směrování zpráv služby IoT hub. Tyto koncové body fungují jako koncové body služby a slouží jako jímky pro směrování zpráv. Zařízení nemůže zapisovat přímo do další koncové body. Další informace o [směrování zpráv](../iot-hub/iot-hub-devguide-messages-d2c.md).

Následující služby Azure IoT Hub aktuálně podporuje jako další koncové body:

* Kontejnery služby Azure Storage
* Event Hubs
* Fronty služby Service Bus
* Témata služby Service Bus

Omezení pro počet koncových bodů můžete přidat, naleznete v tématu [kvóty a omezování](iot-hub-devguide-quotas-throttling.md).

## <a name="field-gateways"></a>Bran v terénu

V řešení IoT *hraniční brána* umístěná mezi vaším zařízením a vaše koncové body IoT Hubu. To je obvykle nachází v blízkosti vašich zařízení. Vaše zařízení komunikují přímo s brány pole pomocí protokolu podporovaných zařízení. Brána pole se připojí k koncový bod služby IoT Hub pomocí protokolu, který je podporovaný službou IoT Hub. Brána pole může být vyhrazené hardwarové zařízení nebo počítač nízkým výkonem softwarem vlastní bránu.

Můžete použít [Azure IoT Edge](/azure/iot-edge/) implementovat hraniční brána. IoT Edge a nabízí funkce, jako je například multiplexní komunikaci od více zařízení do stejného připojení ke službě IoT Hub.

## <a name="next-steps"></a>Další postup

Další referenční témata v této příručce pro vývojáře IoT Hub patří:

* [Dotazovací jazyk služby IoT Hub pro dvojčata zařízení, úlohy a směrování zpráv](iot-hub-devguide-query-language.md)
* [Kvóty a omezování](iot-hub-devguide-quotas-throttling.md)
* [Podpora MQTT centra IoT](iot-hub-mqtt-support.md)
