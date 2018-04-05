---
title: Pochopení zasílání zpráv typu cloud zařízení Azure IoT Hub | Microsoft Docs
description: Příručka vývojáře - použití službou IoT Hub zasílání zpráv typu cloud zařízení. Obsahuje informace o životní cyklus zpráv a možnosti konfigurace.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/15/2018
ms.author: dobett
ms.openlocfilehash: 670cf45a48ca4b72576cedddd4678c0d569401cd
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="send-cloud-to-device-messages-from-iot-hub"></a>Odesílání zpráv typu cloud zařízení ze služby IoT Hub

Odesílat jednosměrný oznámení do aplikace zařízení z vaší back-end řešení, odesílání zpráv typu cloud zařízení z služby IoT hub na vaše zařízení. Informace dalších možností cloudu zařízení podporované službou IoT Hub, naleznete v [Cloud zařízení komunikace pokyny][lnk-c2d-guidance].

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Odesílání zpráv typu cloud zařízení prostřednictvím koncového bodu služby přístupem (**/zprávy/devicebound**). Zařízení pak přijímá zprávy prostřednictvím koncového bodu konkrétní zařízení (**/devices/ {deviceId} / zprávy/devicebound**).

Pro každou zprávu cloud zařízení na jedno zařízení, nastaví IoT Hub **k** vlastnost **/devices/ {deviceId} / zprávy/devicebound**.

Každá fronta zařízení obsahuje maximálně 50 zprávy typu cloud zařízení. Došlo k pokusu o odeslání zprávy na stejné zařízení dojde k chybě.

## <a name="the-cloud-to-device-message-lifecycle"></a>Životní cyklus zpráv typu cloud zařízení

Pokud chcete zajistit doručení zpráv v aspoň jednou, trvá IoT Hub zpráv typu cloud zařízení ve frontách vázané na zařízení. Zařízení musí explicitně potvrdit *dokončení* pro IoT Hub je odebrat z fronty. Tento postup zaručuje odolnost proti připojení a selhání zařízení.

Následující diagram znázorňuje stavu grafu životního cyklu zprávy typu cloud zařízení IoT hub.

![Životní cyklus zpráv typu cloud zařízení][img-lifecycle]

Pokud službu IoT Hub odešle zprávu do zařízení, služba nastaví stav zpráva **zařazených do fronty**. Když zařízení chce *přijímat* zprávu IoT Hub *zámky* zprávy (nastavením stavu na **neviditelná**), což umožňuje jiná vlákna v zařízení začít přijímat další zprávy. Po dokončení zpracování zpráv vlákno zařízení IoT Hub pomocí upozorní *dokončení* zprávy. IoT Hub poté nastaví stav na **dokončeno**.

Zařízení můžete také zvolit:

* *Odmítnout* zpráva, což způsobí, že IoT Hub, která ji nastavte na **mrtvých lettered** stavu. Zařízení, která se připojují přes protokol MQTT nelze odmítnout zprávy typu cloud zařízení.
* *Zrušte* zpráva, což způsobí, že IoT Hub uvést zprávy zpět do fronty, se stavem nastavena na **zařazených do fronty**. Zařízení, která se připojují přes protokol MQTT nelze abandon zprávy typu cloud zařízení.

Vlákno může dojít k selhání zpracování zprávy bez upozornění služby IoT Hub. V takovém případě zprávy automaticky přechod z **neviditelná** stavu zpět **zařazených do fronty** stavu po *časový limit viditelnosti (nebo zámku)*. Výchozí hodnota tohoto limitu je jedna minuta.

**Maximální počet doručení** vlastnost na IoT Hub určuje maximální počet časy zprávu můžete přecházet mezi **zařazených do fronty** a **neviditelná** stavy. Po tomto počtu přechodů IoT Hub, nastaví stav zprávy **mrtvých lettered**. Podobně IoT Hub nastaví stav zprávu, která **mrtvých lettered** po jeho čas vypršení platnosti (viz [hodnota Time to live][lnk-ttl]).

[Odesílání zpráv typu cloud zařízení s centrem IoT] [ lnk-c2d-tutorial] ukazuje, jak k odesílání zpráv typu cloud zařízení z cloudu a přijímat na zařízení.

Zařízení se obvykle dokončí zprávy typu cloud zařízení po ztrátě zprávy nemá vliv na aplikační logiku. Například pokud zařízení obsahuje trvalé obsahu zprávy místně nebo se úspěšně provést operace. Zpráva může mít také přechodný informace, jejichž ztrátě nebude mít vliv na funkci aplikace. Pro dlouhotrvající úlohy, můžete v některých případech:

* Dokončete zpráv typu cloud zařízení po uložením popis úlohy v místním úložišti.
* Upozorněte back-end řešení s jeden nebo více zpráv typu zařízení cloud v různých fázích průběh úlohy.

## <a name="message-expiration-time-to-live"></a>Vypršení platnosti zprávy (hodnota time to live)

Všechny zprávy typu cloud zařízení mají čas vypršení platnosti. Tento čas se nastavuje pomocí jedné z:

* **ExpiryTimeUtc** vlastnost ve službě.
* IoT Hub pomocí výchozího *hodnota time to live* zadán jako vlastnost IoT Hub.

V tématu [možnosti konfigurace Cloud zařízení][lnk-c2d-configuration].

Běžný způsob, jak využít výhod vypršení platnosti zprávy a vyhnout se odesílání zpráv do odpojeného zařízení, je nastavit krátké hodnota time to live hodnoty. Tento přístup dosáhne stejného výsledku jako zachování stavu připojení zařízení, aniž by byly efektivnější. Pokud budete požadovat potvrzení zprávy, IoT Hub upozorní vás, které jsou zařízení:

* Může přijímat zprávy.
* Nejsou v režimu online nebo se nezdařilo.

## <a name="message-feedback"></a>Zpráva zpětné vazby

Při odesílání zpráv typu cloud zařízení služby může požádat o doručení zpráv zpětnou vazbu týkající se konečného stavu této zprávy.

| Vlastnost objektu ACK. | Chování |
| ------------ | -------- |
| **Kladné** | Pokud dosáhne zpráv typu cloud zařízení **dokončeno** stavu služby IoT Hub vytvoří zprávu zpětnou vazbu. |
| **Záporná** | Pokud dosáhne zpráv typu cloud zařízení **mrtvých lettered** stavu služby IoT Hub vytvoří zprávu zpětnou vazbu. |
| **Úplná**     | IoT Hub vytvoří zprávu zpětné vazby v obou případech. |

Pokud **Ack** je **úplné**a jste neobdrželi zprávu zpětné vazby, což znamená, že vypršela platnost zprávy zpětnou vazbu. Službu nelze vědět, co se stalo s původní zprávy. V praxi služby zkontrolujte, že zvládne zpracovat zpětné vazby než vyprší její platnost. Čas vypršení platnosti maximální dva dny, které opustí čas získat službu běží znovu Pokud dojde k chybě.

Jak je popsáno v [koncové body][lnk-endpoints], IoT Hub zajišťuje zpětnou vazbu prostřednictvím koncový bod služby přístupem (**/messages/servicebound/feedback**) jako zprávy. Sémantika pro příjem zpětné vazby jsou stejné jako pro zprávy typu cloud zařízení. Kdykoli je to možné, je zpracovat v dávce zpětnou vazbu zprávy do jedné zprávy v následujícím formátu:

| Vlastnost     | Popis |
| ------------ | ----------- |
| EnqueuedTime | Časové razítko označující při byl zpětnou vazbu zprávy přijaté službou. |
| ID uživatele       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

Text je serializací JSON pole záznamů, každý s následujícími vlastnostmi:

| Vlastnost           | Popis |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | Časové razítko označující, když se stalo výsledek zprávy. Například rozbočovače zobrazila zpráva zpětnou vazbu nebo vypršela její platnost původní zprávy. |
| OriginalMessageId  | **MessageId** zprávy cloud zařízení, ke kterému má vztah tyto informace zpětnou vazbu. |
| statusCode         | Požadovaný řetězec. Použít v zpětnou vazbu zprávy generované IoT Hub. <br/> 'Success' <br/> "Platnost vypršela. <br/> 'DeliveryCountExceeded' <br/> 'Odmítnut. <br/> 'Vyprázdní. |
| Popis        | Řetězce hodnoty pro **StatusCode**. |
| ID zařízení           | **DeviceId** cílového zařízení, k němuž se vztahuje toto připomínek zprávy cloud zařízení. |
| DeviceGenerationId | **DeviceGenerationId** cílového zařízení, k němuž se vztahuje toto připomínek zprávy cloud zařízení. |

Musíte zadat službu **MessageId** zprávy typu cloud zařízení mohli ke korelaci jeho zpětnou vazbu s původní zprávy.

Následující příklad ukazuje textu zprávy zpětnou vazbu.

```json
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0,
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

## <a name="cloud-to-device-configuration-options"></a>Možnosti konfigurace cloudu na zařízení

Každý IoT hub zpřístupní následující možnosti konfigurace pro zasílání zpráv typu cloud zařízení:

| Vlastnost                  | Popis | Rozsah a výchozí |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | Výchozí hodnota TTL zprávy typu cloud zařízení. | Interval ISO_8601 až 2D (minimální 1 minutu). Výchozí hodnota: 1 hodina. |
| maxDeliveryCount          | Doručení maximální počet pro fronty cloud zařízení na zařízení. | 1 až 100. Výchozí: 10. |
| feedback.ttlAsIso8601     | Uchování pro zpětnou vazbu služby vázané zprávy. | Interval ISO_8601 až 2D (minimální 1 minutu). Výchozí hodnota: 1 hodina. |
| feedback.maxDeliveryCount |Doručení maximální počet pro frontu zpětné vazby. | 1 až 100. Výchozí: 100. |

Další informace o tom, jak nastavit tyto možnosti konfigurace najdete v tématu [centra IoT vytvořit][lnk-portal].

## <a name="next-steps"></a>Další postup

Informace o sadách SDK, můžete použít pro příjem zpráv typu cloud zařízení najdete v tématu [SDK služby Azure IoT][lnk-sdks].

Můžete vyzkoušet na přijímání zpráv typu cloud zařízení, najdete v článku [odeslat cloud zařízení] [ lnk-c2d-tutorial] kurzu.

[img-lifecycle]: ./media/iot-hub-devguide-messages-c2d/lifecycle.png

[lnk-portal]: iot-hub-create-through-portal.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-ttl]: #message-expiration-time-to-live
[lnk-c2d-configuration]: #cloud-to-device-configuration-options
[lnk-lifecycle]: #message-lifecycle
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
