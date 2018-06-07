---
title: Pochopení koncové body Azure IoT Hub | Microsoft Docs
description: Příručka vývojáře – referenční informace o IoT Hub směřujících zařízení a služby přístupem koncových bodů.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 37e1bd0b479e27f0d871495c0725fc865cbb8572
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34632573"
---
# <a name="reference---iot-hub-endpoints"></a>Odkaz – koncové body centra IoT

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>Názvy IoT Hub

Název služby IoT hub, který je hostitelem koncové body na portálu na můžete najít **přehled** okno. Ve výchozím nastavení, jako název DNS služby IoT hub vypadá takto: `{your iot hub name}.azure-devices.net`.

Azure DNS můžete použít k vytvoření vlastní název DNS pro službu IoT hub. Další informace najdete v tématu [Azure DNS používá k poskytování nastavení vlastní domény pro službu Azure](../dns/dns-custom-domain.md).

## <a name="list-of-built-in-iot-hub-endpoints"></a>Seznam předdefinovaných koncové body centra IoT

Azure IoT Hub je víceklientské služby, který zveřejňuje jeho funkce pro různé účastníky. Následující diagram znázorňuje různé koncových bodů, aby IoT Hub zpřístupní.

![Koncové body IoT Hubu][img-endpoints]

Následující seznam popisuje koncových bodů:

* **Poskytovatel prostředků**. Zprostředkovatel prostředků služby IoT Hub zpřístupní [Azure Resource Manager] [ lnk-arm] rozhraní. Toto rozhraní umožňuje vlastníky předplatného Azure k vytváření a odstraňování centra IoT a k aktualizaci vlastností centra IoT. Vlastnosti služby IoT Hub řídí [zásad zabezpečení na úrovni centra][lnk-accesscontrol], na rozdíl od řízení přístupu na úrovni zařízení a funkční nastavení pro zasílání zpráv typu cloud zařízení a zařízení cloud. Zprostředkovatel prostředků služby IoT Hub můžete také [exportovat identit zařízení][lnk-importexport].
* **Správa identit zařízení**. Každé centrum IoT zpřístupňuje řadu koncové body HTTPS REST pro správu identit zařízení (vytvořit, získat, aktualizovat a odstranit). [Identit zařízení] [ lnk-device-identities] se používají pro řízení přístupu a ověřování a zařízení.
* **Správa zařízení twin**. Každé centrum IoT zpřístupňuje řadu koncový bod HTTPS REST služby směřujících k dotazu a aktualizace [dvojčata zařízení][lnk-twins] (aktualizace značky a vlastnosti).
* **Úlohy správy**. Každý IoT hub zpřístupní koncový bod HTTPS REST služby směřujících k dotazování a správu sadu [úlohy][lnk-jobs].
* **Koncové body zařízení**. Pro každé zařízení v registru identit služby IoT Hub zpřístupní sada koncových bodů:

  * *Odesílání zpráv typu zařízení cloud*. Zařízení používá tento koncový bod pro [odesílání zpráv typu zařízení cloud][lnk-d2c].
  * *Příjem zpráv typu cloud zařízení*. Zařízení používá tento koncový bod pro příjem cílové [zprávy typu cloud zařízení][lnk-c2d].
  * *Zahájení nahrávání souborů*. Zařízení používá tento koncový bod pro příjem identifikátor URI SAS úložiště Azure ze služby IoT Hub na [nahrát soubor][lnk-upload].
  * *Načíst a aktualizovat vlastnosti twin zařízení*. Zařízení používá tento koncový bod pro přístup k jeho [dvojče zařízení][lnk-twins]na vlastnosti.
  * *Přijímat požadavky přímá metoda*. Zařízení používá k naslouchání pro tento koncový bod [přímá metoda][lnk-methods]na požadavky.

    Tyto koncové body jsou vystavené pomocí [protokoly MQTT v3.1.1][lnk-mqtt], HTTPS 1.1 a [protokolu AMQP 1.0] [ lnk-amqp] protokoly. Je také k dispozici prostřednictvím protokolu AMQP [Websocket] [ lnk-websockets] na portu 443.

* **Koncové body služby**. Každé centrum IoT zpřístupňuje řadu koncové body pro váš back-end řešení pro komunikaci ve vašich zařízeních. S jednou výjimkou těchto koncových bodů jsou přístupné pouze pomocí [AMQP] [ lnk-amqp] protokolu. Koncový bod volání metoda vystavený přes protokol HTTPS.
  
  * *Příjem zpráv typu zařízení cloud*. Tento koncový bod je kompatibilní s [Azure Event Hubs][lnk-event-hubs]. Back-end služby můžete použít ke čtení [zpráv typu zařízení cloud] [ lnk-d2c] poslal zařízení. Můžete vytvořit vlastní koncové body ve službě IoT hub kromě tento předdefinovaný koncový bod.
  * *Zprávy typu cloud zařízení odesílat a přijímat potvrzování doručení*. Tyto koncové body povolit back-end vašeho řešení odeslat spolehlivé [zprávy typu cloud zařízení][lnk-c2d]a získat odpovídající doručení nebo vypršení platnosti potvrzování.
  * *Přijímat oznámení souboru*. Tento koncový bod zasílání zpráv umožňuje dostávat oznámení o, když vaše zařízení úspěšně odeslat soubor. 
  * *Přímé volání metody*. Tento koncový bod umožňuje službě back-end pro vyvolání [přímá metoda] [ lnk-methods] na zařízení.
  * *Zobrazí operace sledování událostí*. Tento koncový bod umožňuje sledování událostí, pokud je nakonfigurované služby IoT hub pro vydávání je operace příjmu. Další informace najdete v tématu [IoT Hub operations monitorování][lnk-operations-mon].

[SDK služby Azure IoT] [ lnk-sdks] článek popisuje různé způsoby, kterými pro přístup k těchto koncových bodů.

Použít všechny koncové body centra IoT [TLS] [ lnk-tls] protokol a žádný koncový bod je někdy zveřejněné na bez šifrování nezabezpečené kanály.

## <a name="custom-endpoints"></a>Vlastní koncové body

Do služby IoT hub tak, aby fungoval jako koncové body pro směrování zpráv můžete propojit stávající služby Azure v rámci vašeho předplatného. Tyto koncové body fungovat jako koncové body služby a jsou použity jako jímky pro směrování zpráv. Zařízení nemůže zapisovat přímo do další koncové body. Další informace o směrování zpráv, naleznete v příspěvku Příručka vývojáře na [odesílání a přijímání zpráv službou IoT hub][lnk-devguide-messaging].

Následující služby Azure IoT Hub aktuálně podporuje jako další koncové body:

* Kontejnery Azure Storage
* Event Hubs
* Fronty služby Service Bus
* Témata služby Service Bus

IoT Hub potřebuje přístup k zápisu do těchto koncových bodů služby pro směrování zpráv pro práci. Pokud nakonfigurujete koncové body prostřednictvím portálu Azure, jsou pro vás přidat potřebná oprávnění. Zajistěte, aby že konfigurace vaší služby pro podporu očekávané propustnost. Při první konfiguraci řešení IoT, můžete sledovat další koncové body a průběhu skutečné zátěže.

Pokud zpráva odpovídá více tras všechny přejděte na stejný koncový bod, IoT Hub zajišťuje zprávy do tohoto koncového bodu pouze jednou. Proto není potřeba konfigurace odstranění duplicitních dat na fronty sběrnice nebo téma. Do oddílů front oddílu spřažení zaručuje, řazení zpráv.

Omezení pro počet koncových bodů můžete přidat, naleznete v části [kvóty a omezení][lnk-devguide-quotas].

### <a name="when-using-azure-storage-containers"></a>Při použití kontejnery Azure Storage

IoT Hub podporuje pouze zápis dat do Azure Storage kontejnerů jako objekty BLOB v [Apache Avro](http://avro.apache.org/) formátu. IoT Hub dávek zprávy a zapisuje data do objektu blob vždy, když:

* Dávka dosáhne určité velikosti.
* Nebo uplynutí časového intervalu.

IoT Hub budou zapisovat do prázdný objekt blob, pokud nejsou žádná data k zápisu.

IoT Hub výchozí nastavení této zásady vytváření názvů souborů:

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Můžete použít libovolnou souboru konvence, které chcete, ale je nutné použít všechny uvedené tokeny.

### <a name="when-using-service-bus-queues-and-topics"></a>Při použití front Service Bus a témat

Fronty sběrnice a témata použít jako koncové body centra IoT nesmí mít **relací** nebo **duplicitní detekce** povolena. Pokud některá z těchto možností jsou povolené, koncový bod se zobrazí jako **Unreachable** na portálu Azure.

## <a name="field-gateways"></a>Pole brány

V řešení IoT *brána pole* nachází mezi zařízeními a vaše koncové body centra IoT. Je obvykle nachází blízko zařízení. Vaše zařízení komunikují přímo s brány pole pomocí protokol podporovaný zařízení. Brána pole se připojí k koncový bod služby IoT Hub pomocí protokolu, který je podporován službou IoT Hub. Brána pole může být vyhrazené hardwarové zařízení nebo počítač úsporného režimu softwarem vlastní brány.

Můžete použít [Azure IoT Edge] [ lnk-iot-edge] implementovat brána pole. Okraj IoT nabízí funkce, jako je multiplexní komunikaci od více zařízení do stejné připojení služby IoT Hub.

## <a name="next-steps"></a>Další postup

Zahrnout další referenční témata v této příručce pro vývojáře IoT Hub:

* [IoT Hub dotazovacího jazyka pro dvojčata zařízení, úlohy a směrování zpráv][lnk-devguide-query]
* [Kvóty a omezení][lnk-devguide-quotas]
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
