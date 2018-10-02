---
title: Vysvětlení, zasílání zpráv typu cloud zařízení Azure IoT Hub | Dokumentace Microsoftu
description: Průvodce pro vývojáře – jak používat zasílání zpráv typu cloud zařízení pomocí služby IoT Hub. Obsahuje informace o životní cyklus zpráv a možnosti konfigurace.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.author: dobett
ms.openlocfilehash: 3f137ea80dc67bb075f34846e5563fb72c72b69a
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2018
ms.locfileid: "47585641"
---
# <a name="send-cloud-to-device-messages-from-iot-hub"></a>Odesílání zpráv z cloudu do zařízení z IoT Hubu

Odesílat jednosměrné oznámení z back-endem řešení do aplikace pro zařízení, odesílat zprávy typu cloud zařízení ze služby IoT hub do vašeho zařízení. Diskuzi o další možnosti cloudu na zařízení podporované ve službě IoT Hub, najdete v článku [doprovodných materiálech ke komunikaci typu Cloud zařízení](iot-hub-devguide-c2d-guidance.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Odesílat zprávy typu cloud zařízení prostřednictvím koncového bodu připojeného k služby (**/zpráv/devicebound**). Zařízení pak přijme zprávy přes koncový bod specifický pro zařízení (**/devices/ {deviceId} / zpráv/devicebound**).

Cílit na každou zprávu typu cloud zařízení na jedno zařízení, nastaví služby IoT Hub **k** vlastnost **/devices/ {deviceId} / zpráv/devicebound**.

Každá fronta zařízení obsahuje maximálně 50 zprávy typu cloud zařízení. Došlo k pokusu o odeslání více zpráv do stejného zařízení dojde k chybě.

## <a name="the-cloud-to-device-message-lifecycle"></a>Životní cyklus zpráv typu cloud zařízení

Chcete-li zaručit doručování zpráv v alespoň jedno, IoT Hub udržuje takové zprávy typu cloud zařízení do fronty jednotlivá zařízení. Zařízení, musíte explicitně potvrdit *dokončení* pro službu IoT Hub k jejich odstranění z fronty. Tento postup zaručuje, což zvyšuje odolnost vůči připojení a selhání zařízení.

Následující diagram znázorňuje graf stavu životního cyklu zprávy typu cloud zařízení ve službě IoT Hub.

![Životní cyklus zpráv typu cloud zařízení](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

Když služba IoT Hub odešle zprávu do zařízení, nastaví službu stavu zprávy na **zařazených do fronty**. Když zařízení chce *přijímat* zprávu služby IoT Hub *zámky* zprávy (podle nastavení stavu na **neviditelné**), tomu ostatní vlákna v zařízení začít přijímat Další zprávy. Po dokončení zpracování zprávy typu zařízení vlákno upozorní služby IoT Hub pomocí *dokončení* zprávy. IoT Hub poté nastaví stav **dokončeno**.

Zařízení můžete také zvolit:

* *Odmítnout* zpráva, což způsobí, že se IoT Hub a nastavte ho na **Dead lettered** stavu. Zařízení, která se připojují přes protokol MQTT nelze odmítnout zprávy typu cloud zařízení.

* *Opustit* zpráva, což způsobí, že IoT Hubu umístit zprávy zpět ve frontě, se stavem nastavena na **zařazených do fronty**. Zařízení, která se připojují přes protokol MQTT nejde opustit zprávy typu cloud zařízení.

Vlákno se nemusí zpracovávat zprávy bez upozornění služby IoT Hub. V takovém případě zprávy automaticky přechod ze **neviditelné** zpět do stavu **zařazených do fronty** stavu po *časový limit viditelnosti (nebo lock)*. Výchozí hodnota tohoto limitu je jedna minuta.

**Maximální počet doručení** vlastnost v IoT Hubu určuje maximální počet pokusů zprávu můžete přecházet mezi **zařazených do fronty** a **neviditelné** stavy. Po tomto počtu přechody služby IoT Hub, nastaví stav zprávy, která se **Dead lettered**. Obdobně služby IoT Hub nastaví stav zprávy **Dead lettered** po jeho času vypršení platnosti (naleznete v tématu [TTL](#message-expiration-time-to-live)).

[Jak odesílat zprávy typu cloud zařízení pomocí služby IoT Hub](iot-hub-csharp-csharp-c2d.md) ukazuje, jak odesílat zprávy typu cloud zařízení z cloudu a přijímat je na zařízení.

Zařízení obvykle dokončí zprávu typu cloud zařízení, když ke ztrátě zprávy nemá vliv na aplikaci logiky. Například pokud zařízení obsahuje trvalé zpráva obsahu místně nebo byla úspěšně provedeny operace. Zpráva může mít také přechodných informací, jejichž ztrátu nebude mít vliv na funkčnost aplikace. Pro dlouho běžící úlohy můžete v některých případech:

* Dokončete zprávu typu cloud zařízení po trvalém ukládání popis úlohy v místním úložišti.

* Upozornění pro back-end řešení pomocí jednoho nebo více zpráv typu zařízení cloud v různých fázích průběh úlohy.

## <a name="message-expiration-time-to-live"></a>Vypršení platnosti zpráv (hodnota time to live)

Všechny zprávy typu cloud zařízení má čas vypršení platnosti. Tento čas se nastavuje pomocí jedné z:

* **ExpiryTimeUtc** vlastnosti ve službě.
* IoT Hub pomocí výchozí *TTL* zadán jako vlastnost služby IoT Hub.

Zobrazit [možnosti konfigurace Cloud zařízení](#cloud-to-device-configuration-options).

Běžný způsob, jak využít výhod vypršení platnosti zpráv a vyhnout se odesílání zpráv do odpojená zařízení, je nastavit krátkého formátu času hodnoty TTL. Tento přístup dosáhne stejného výsledku jako při zachování efektivnější zachování stavu připojení zařízení. Pokud budete požadovat zprávy potvrzení, upozorní služby IoT Hub, které jsou zařízení:

* Může přijímat zprávy.
* Nejsou v režimu online nebo se nezdařilo.

## <a name="message-feedback"></a>Zprávy zpětné vazby

Při odesílání zpráv typu cloud zařízení služby můžou požádat o doručení zpětnou vazbu za zprávy týkající se koncový stav této zprávy.

| Vlastnosti objektu ACK. | Chování |
| ------------ | -------- |
| **Pozitivní** | Pokud dosáhne zprávy typu cloud zařízení **dokončeno** stavu služby IoT Hub vygeneruje zprávu zpětnou vazbu. |
| **Záporná** | Pokud dosáhne zprávy typu cloud zařízení **Dead lettered** stavu služby IoT Hub vygeneruje zprávu zpětnou vazbu. |
| **Úplné**     | IoT Hub vygeneruje zprávu zpětnou vazbu v obou případech. |

Pokud **Ack** je **úplné**a neobdrželi zprávu zpětnou vazbu, znamená to, že vypršela platnost zprávy zpětné vazby. Služba nemůže vědět, co se stalo s původní zprávy. V praxi služba zajistil zpětnou vazbu ji může zpracovat, než vyprší její platnost. Čas vypršení platnosti maximální dva dny, které nechá je načase začít službu opětovném zprovoznění Pokud dojde k chybě.

Jak je vysvětleno v [koncové body](iot-hub-devguide-endpoints.md), poskytuje zpětnou vazbu prostřednictvím koncového bodu připojeného k služby IoT Hub (**/messages/servicebound/feedback**) jako zprávy. Sémantika pro příjem zpětná vazba je stejný jako u zprávy typu cloud zařízení. Kdykoli je to možné, zpětnou vazbu zpráv v dávce do jedné zprávy v následujícím formátu:

| Vlastnost     | Popis |
| ------------ | ----------- |
| EnqueuedTime | Časové razítko určující, kdy byla přijata zpráva zpětnou vazbu v centru. |
| UserId       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

Text je serializací JSON pole záznamů, každý s následujícími vlastnostmi:

| Vlastnost           | Popis |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | Časové razítko určující, kdy se stalo výsledek zprávy. Například centra zobrazila zpráva zpětnou vazbu nebo vypršela její platnost původní zprávy. |
| ID původní zprávy  | **ID zprávy** zprávy typu cloud zařízení, ke kterému se vztahuje tyto informace zpětnou vazbu. |
| statusCode         | Povinný řetězec. Používá ve vygenerované službou IoT Hub zpráv se zpětnou vazbou. <br/> 'Success' <br/> "Platnost vypršela. <br/> "DeliveryCountExceeded. <br/> "Odmítl. <br/> "Vymazat" |
| Popis        | Hodnoty pro řetězce **StatusCode**. |
| DeviceId           | **ID zařízení** cílového zařízení zprávy typu cloud zařízení, na který se týká tato složka zpětné vazby. |
| DeviceGenerationId | **DeviceGenerationId** cílového zařízení zprávy typu cloud zařízení, na který se týká tato složka zpětné vazby. |

Musíte zadat službu **MessageId** pro zprávy typu cloud zařízení moct sladit její zpětnou vazbu s původní zprávou.

Následující příklad ukazuje, text zprávy zpětné vazby.

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

## <a name="cloud-to-device-configuration-options"></a>Možnosti konfigurace cloud zařízení

Každé centrum IoT zpřístupňuje následující možnosti konfigurace pro zasílání zpráv typu cloud zařízení:

| Vlastnost                  | Popis | Rozsah a výchozí |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | Výchozí hodnota TTL pro zprávy typu cloud zařízení. | Interval ISO_8601 až 2D (minimální 1 minuta). Výchozí hodnota: 1 hodina. |
| MaxDeliveryCount          | Maximální počet doručení zpráv typu cloud zařízení na zařízení fronty. | 1 až 100. Výchozí: 10. |
| feedback.ttlAsIso8601     | Uchovávání zpráv se zpětnou vazbou vázané na služby. | Interval ISO_8601 až 2D (minimální 1 minuta). Výchozí hodnota: 1 hodina. |
| feedback.maxDeliveryCount |Maximální počet doručení pro frontu zpětné vazby. | 1 až 100. Výchozí: 100. |

Další informace o tom, jak nastavit tyto možnosti konfigurace najdete v tématu [vytvořit IoT hub](iot-hub-create-through-portal.md).

## <a name="next-steps"></a>Další postup

Informace o sadách SDK můžete použít pro příjem zpráv typu cloud zařízení najdete v tématu [sad SDK Azure IoT](iot-hub-devguide-sdks.md).

Vyzkoušejte si příjem zpráv z cloudu do zařízení, najdete v článku [odesílání typu cloud zařízení](iot-hub-csharp-csharp-c2d.md) kurzu.