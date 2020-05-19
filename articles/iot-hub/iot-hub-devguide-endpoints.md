---
title: Vysvětlení koncových bodů Azure IoT Hub | Microsoft Docs
description: Příručka pro vývojáře – referenční informace o IoT Hub koncové body s přístupem k zařízením a ke službám.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/10/2019
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 9554713e50e7a2ead2e25f274428ad0ecba4934d
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996956"
---
# <a name="reference---iot-hub-endpoints"></a>Odkazy-IoT Hub koncové body

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>Názvy IoT Hub

Název hostitele služby IoT Hub, který je hostitelem vašich koncových bodů, najdete na stránce s **přehledem** vašeho rozbočovače. Ve výchozím nastavení vypadá název DNS služby IoT Hub takto: `{your iot hub name}.azure-devices.net` .

## <a name="list-of-built-in-iot-hub-endpoints"></a>Seznam předdefinovaných koncových bodů IoT Hub

Azure IoT Hub je víceklientské služby, která zpřístupňuje svou funkci různým aktérům. Následující diagram znázorňuje různé koncové body, které IoT Hub zpřístupňuje.

![Koncové body IoT Hubu](./media/iot-hub-devguide-endpoints/endpoints.png)

Následující seznam popisuje koncové body:

* **Poskytovatel prostředků**. Poskytovatel prostředků IoT Hub zpřístupňuje rozhraní [Azure Resource Manager](../azure-resource-manager/management/overview.md) . Toto rozhraní umožňuje vlastníkům předplatného Azure vytvářet a odstraňovat centra IoT a aktualizovat vlastnosti služby IoT Hub. IoT Hub vlastnosti řídí [zásady zabezpečení na úrovni centra](iot-hub-devguide-security.md#access-control-and-permissions), na rozdíl od řízení přístupu na úrovni zařízení a funkční možnosti pro zasílání zpráv z cloudu na zařízení a ze zařízení do cloudu. Poskytovatel prostředků IoT Hub taky umožňuje [exportovat identity zařízení](iot-hub-devguide-identity-registry.md#import-and-export-device-identities).

* **Správa identit zařízení** Každé centrum IoT zveřejňuje sadu koncových bodů protokolu HTTPS REST pro správu identit zařízení (vytváření, načítání, aktualizace a odstraňování). [Identity zařízení](iot-hub-devguide-identity-registry.md) se používají pro ověřování zařízení a řízení přístupu.

* **Správa s dvojitou**podobou zařízení Každé centrum IoT zveřejňuje sadu koncových bodů protokolu HTTPS s přístupem k službě pro dotazování a aktualizaci [vláken zařízení](iot-hub-devguide-device-twins.md) (aktualizace značek a vlastností).

* **Správa úloh**. Každá služba IoT Hub zpřístupňuje sadu koncových bodů protokolu HTTPS s přístupem k službě pro dotazování a správu [úloh](iot-hub-devguide-jobs.md).

* **Koncové body zařízení**. Pro každé zařízení, které je v registru identit, zveřejní IoT Hub sadu koncových bodů:

  * *Posílání zpráv ze zařízení do cloudu*. Zařízení pomocí tohoto koncového bodu [odesílá zprávy typu zařízení-Cloud](iot-hub-devguide-messages-d2c.md).

  * *Příjem zpráv z cloudu do zařízení*. Zařízení pomocí tohoto koncového bodu přijímá cílené [zprávy typu cloud-zařízení](iot-hub-devguide-messages-c2d.md).

  * *Iniciujte nahrávání souborů*. Zařízení používá tento koncový bod pro příjem Azure Storage identifikátor URI SAS z IoT Hub pro [nahrání souboru](iot-hub-devguide-file-upload.md).

  * *Načte a aktualizuje vlastnosti vlákna zařízení*. Zařízení používá tento koncový bod pro přístup k vlastnostem, které jsou v [zařízení](iot-hub-devguide-device-twins.md).

  * *Přijmout požadavky přímých metod*. Zařízení používá tento koncový bod k naslouchání požadavkům [přímých metod](iot-hub-devguide-direct-methods.md).

    Tyto koncové body jsou vystaveny pomocí protokolů [MQTT v 3.1.1](https://mqtt.org/), https 1,1 a [AMQP 1,0](https://www.amqp.org/) . AMQP a MQTT jsou dostupné i přes [objekty WebSockets](https://tools.ietf.org/html/rfc6455) na portu 443.

* **Koncové body služby**. Každé centrum IoT Hub zpřístupňuje sadu koncových bodů pro back-end řešení ke komunikaci s vašimi zařízeními. S jednou výjimkou se tyto koncové body zveřejňují jenom pomocí protokolů WebSockets [AMQP](https://www.amqp.org/) a AMQP. Koncový bod volání přímé metody se zveřejňuje prostřednictvím protokolu HTTPS.
  
  * *Příjem zpráv ze zařízení do cloudu*. Tento koncový bod je kompatibilní s [Azure Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/). Back-endové služba ho může používat ke čtení [zpráv typu zařízení-Cloud](iot-hub-devguide-messages-d2c.md) odesílaných vašimi zařízeními. Kromě tohoto integrovaného koncového bodu můžete vytvořit vlastní koncové body ve službě IoT Hub.
  
  * *Posílání zpráv z cloudu na zařízení a příjem potvrzení o doručení*. Tyto koncové body umožňují back-endu vašeho řešení odesílat spolehlivé [zprávy z cloudu na zařízení](iot-hub-devguide-messages-c2d.md)a získávat odpovídající potvrzení o doručení nebo vypršení platnosti.
  
  * *Přijímání oznámení o souborech*. Tento koncový bod pro zasílání zpráv umožňuje dostávat oznámení o tom, kdy zařízení úspěšně nahrála soubor. 
  
  * *Přímé volání metody*. Tento koncový bod umožňuje back-endové službě vyvolat přímo na zařízení [přímou metodu](iot-hub-devguide-direct-methods.md) .
  
  * *Příjem událostí monitorování operací*. Tento koncový bod umožňuje přijímat události monitorování operací, pokud vaše centrum IoT je nakonfigurované tak, aby je generovalo. Další informace najdete v tématu [monitorování provozu IoT Hub](iot-hub-operations-monitoring.md).

Článek o sadách [SDK Azure IoT](iot-hub-devguide-sdks.md) popisuje různé způsoby přístupu k těmto koncovým bodům.

Všechny koncové body IoT Hub používají protokol [TLS](https://tools.ietf.org/html/rfc5246) a v nešifrovaných nebo nezabezpečených kanálech se nikdy nezveřejňuje žádný koncový bod.

## <a name="custom-endpoints"></a>Vlastní koncové body

Existující služby Azure v rámci vašeho předplatného můžete propojit se službou IoT Hub, aby fungovaly jako koncové body pro směrování zpráv. Tyto koncové body slouží jako koncové body služby a slouží jako jímky pro trasy zpráv. Zařízení nemůžou zapisovat přímo do dalších koncových bodů. Přečtěte si další informace o [směrování zpráv](../iot-hub/iot-hub-devguide-messages-d2c.md).

IoT Hub aktuálně podporuje následující služby Azure jako další koncové body:

* Kontejnery Azure Storage
* Event Hubs
* Fronty služby Service Bus
* Témata služby Service Bus

Omezení počtu koncových bodů, které můžete přidat, najdete v tématu [kvóty a omezování](iot-hub-devguide-quotas-throttling.md).

Ke zjištění stavu koncových bodů můžete použít REST API [získat stav koncových](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) bodů. Doporučujeme použít [IoT Hub metriky](iot-hub-metrics.md) týkající se latence zprávy směrování k identifikaci a ladění chyb, když je stav koncového bodu v pořádku nebo není v pořádku, protože při použití koncového bodu v jednom z těchto stavů očekáváme, že latence bude vyšší.

|Stav|Popis|
|---|---|
|v pořádku|Koncový bod přijímá zprávy podle očekávání.|
|není v pořádku|Koncový bod nepřijímá zprávy podle očekávání a IoT Hub se pokouší odeslat data do tohoto koncového bodu. Stav koncového bodu, který není v pořádku, bude aktualizován na stav v pořádku, když IoT Hub navázala trvalý stav stavu.|
|Neznámý|IoT Hub nevytvořil připojení ke koncovému bodu. Z tohoto koncového bodu nebyly doručeny ani zamítnuty žádné zprávy.|
|Zpráva|Koncový bod nepřijímá zprávy, jakmile IoT Hub znovu pokusí odeslat zprávy o zkušebním období.|

## <a name="field-gateways"></a>Brány polí

V řešení IoT je mezi zařízeními a koncovými body IoT Hub umístěná *Brána pole* . Obvykle se nachází blízko vašich zařízení. Vaše zařízení komunikují přímo s bránou pole pomocí protokolu podporovaného zařízeními. Brána Field se připojuje k IoT Hub koncovému bodu pomocí protokolu, který podporuje IoT Hub. Brána pole může být vyhrazené hardwarové zařízení nebo počítač s nízkou spotřebou, na kterém je spuštěný vlastní software brány.

K implementaci brány pole můžete použít [Azure IoT Edge](/azure/iot-edge/) . IoT Edge nabízí funkce, jako je například multiplexace komunikace z více zařízení do stejného IoT Hub připojení.

## <a name="next-steps"></a>Další kroky

Další referenční témata v tomto IoT Hub příručce pro vývojáře zahrnují:

* [IoT Hub dotazovací jazyk pro vlákna, úlohy a směrování zpráv v zařízeních](iot-hub-devguide-query-language.md)
* [Kvóty a omezování](iot-hub-devguide-quotas-throttling.md)
* [Podpora IoT Hub MQTT](iot-hub-mqtt-support.md)
* [Informace o IP adrese služby IoT Hub](iot-hub-understand-ip-address.md)