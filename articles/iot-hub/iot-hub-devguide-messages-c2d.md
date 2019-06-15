---
title: Vysvětlení, zasílání zpráv typu cloud zařízení Azure IoT Hub | Dokumentace Microsoftu
description: Tato příručka pro vývojáře popisuje způsob použití zasílání zpráv typu cloud zařízení pomocí služby IoT hub. Obsahuje informace o životním cyklu zprávy a možnosti konfigurace.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 0fc1b65a4ba1c8a3d76b48206d6a4703035e05bc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055319"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>Odesílat zprávy typu cloud zařízení ze služby IoT hub

Neodesílat jednosměrné oznámení z back-endem řešení do aplikace pro zařízení, odesílání zpráv typu cloud zařízení ze služby IoT hub do vašeho zařízení. Informace o možnostech typu cloud zařízení podporované ve službě Azure IoT Hub, najdete v článku [doprovodných materiálech ke komunikaci typu Cloud zařízení](iot-hub-devguide-c2d-guidance.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Odesílat zprávy typu cloud zařízení přes koncový bod služby přístupem */zpráv/devicebound*. Zařízení pak přijme zprávy přes koncový bod specifický pro zařízení */devices/ {deviceId} / zpráv/devicebound*.

Cílit na každou zprávu typu cloud zařízení na jedno zařízení, nastaví služby IoT hub **k** vlastnost */devices/ {deviceId} / zpráv/devicebound*.

Každá fronta zařízení obsahuje maximálně 50 zprávy typu cloud zařízení. Se pokouší odeslat další zprávy do stejného zařízení dojde k chybě.

## <a name="the-cloud-to-device-message-life-cycle"></a>Životním cyklu zprávy typu cloud zařízení

Zaručit doručování zpráv v alespoň jedno, váš IoT hub udržuje takové zprávy typu cloud zařízení do fronty jednotlivá zařízení. Pro službu IoT hub k odebrání zprávy z fronty, musí zařízení explicitně potvrdit *dokončení*. Tento postup zaručuje, což zvyšuje odolnost vůči připojení a selhání zařízení.

Životní cyklus stavu grafu se zobrazí v následujícím diagramu:

![Životní cyklus zpráv typu cloud zařízení](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

Když služby Azure IoT hub odesílá zprávy do zařízení, nastaví služba stavu zprávy na *zařazených do fronty*. Když zařízení chce *přijímat* zprávu, služby IoT hub *zámky* zprávy podle nastavení stavu na *neviditelné*. Tento stav umožňuje ostatní vlákna v zařízení a začít přijímat další zprávy. Po dokončení zpracování zprávy typu zařízení vlákno upozorní služby IoT hub pomocí *dokončení* zprávy. IoT hub poté nastaví stav *dokončeno*.

Může také zařízení:

* *Odmítnout* zpráva, což způsobí, že služby IoT hub ji nastavit na hodnotu *Dead lettered* stavu. Zařízení, která se připojují přes protokol zpráv služby Řízení front MQTT (Telemetry Transport) nelze odmítnout zprávy typu cloud zařízení.

* *Opustit* zpráva, což způsobí, že služby IoT hub do zprávy zpět ve frontě, se stavem nastavena na *zařazených do fronty*. Zařízení, která se připojují přes protokol MQTT nejde opustit zprávy typu cloud zařízení.

Vlákno se nemusí zpracovávat zprávy bez upozornění služby IoT hub. V takovém případě zprávy automaticky přechod ze *neviditelné* zpět do stavu *zařazených do fronty* stavu po *viditelnost* vypršení časového limitu (nebo *zámku* časový limit). Výchozí hodnota tento časový limit je jedna minuta.

**Maximální počet doručení** vlastnost v IoT hubu určuje maximální počet pokusů zprávu můžete přecházet mezi *zařazených do fronty* a *neviditelné* stavy. Po tomto počtu přechody služby IoT hub, nastaví stav zprávy, která se *Dead lettered*. Obdobně služby IoT hub nastaví stav zprávy *Dead lettered* po dobu jeho platnosti. Další informace najdete v tématu [TTL](#message-expiration-time-to-live).

[Jak odesílat zprávy typu cloud zařízení pomocí služby IoT Hub](iot-hub-csharp-csharp-c2d.md) článku se dozvíte, jak odesílat zprávy typu cloud zařízení z cloudu a přijímat je na zařízení.

Zařízení se obvykle dokončí zprávu typu cloud zařízení po ztrátě zpráva nemá vliv na aplikaci logiky. Příkladem může být, pokud zařízení obsahuje trvalé místně obsah zprávy nebo po úspěšném provedení operace. Zpráva může mít také přechodných informací, jejichž ztrátu nebude mít vliv na funkčnost aplikace. Pro dlouho běžící úlohy můžete v některých případech:

* Dokončete zprávu typu cloud zařízení po něm obsahuje trvalé popis úlohy v místním úložišti.

* Upozornění pro back-end řešení pomocí jednoho nebo více zpráv typu zařízení cloud v různých fázích průběh úlohy.

## <a name="message-expiration-time-to-live"></a>Vypršení platnosti zpráv (hodnota time to live)

Všechny zprávy typu cloud zařízení má čas vypršení platnosti. Tentokrát nastavená buď z následujících akcí:

* **ExpiryTimeUtc** vlastnosti ve službě
* Služby IoT hub s využitím výchozí *TTL* , který je zadán jako vlastnost centra IoT

Zobrazit [možnosti konfigurace Cloud zařízení](#cloud-to-device-configuration-options).

Je běžným způsobem, abyste mohli využívat vypršení platnosti zpráv a aby se zabránilo odesílání zpráv do odpojená zařízení nastavit krátký *TTL* hodnoty. Tento přístup dosáhne stejného výsledku jako zachování stavu připojení zařízení, ale je mnohem efektivnější. Pokud budete požadovat zprávy potvrzení, upozorní služby IoT hub, které jsou zařízení:

* Může přijímat zprávy.
* Nejsou v režimu online nebo se nezdařilo.

## <a name="message-feedback"></a>Zprávy zpětné vazby

Při odesílání zpráv typu cloud zařízení služby můžou požádat o doručení zpětnou vazbu za zprávy o konečný stav této zprávy. To provedete tak, že nastavíte **iothub-ack** vlastnost aplikace ve zprávě typu cloud zařízení, která je odesíláno na jednu z následujících čtyř hodnot:

| Hodnota vlastnosti objektu ACK. | Chování |
| ------------ | -------- |
| None     | IoT hub nebude generovat zprávu zpětnou vazbu (výchozí chování). |
| pozitivní | Pokud dosáhne zprávy typu cloud zařízení *dokončeno* stavu služby IoT hub vygeneruje zprávu zpětnou vazbu. |
| Záporná | Pokud dosáhne zprávy typu cloud zařízení *Dead lettered* stavu služby IoT hub vygeneruje zprávu zpětnou vazbu. |
| úplná     | IoT hub vygeneruje zprávu zpětnou vazbu v obou případech. |

Pokud **Ack** hodnotu *úplné*a neobdrželi zprávu zpětnou vazbu, znamená to, že vypršela platnost zprávy zpětné vazby. Služba nemůže vědět, co se stalo s původní zprávy. V praxi služba zajistil zpětnou vazbu ji může zpracovat, než vyprší její platnost. Čas vypršení platnosti maximální dva dny, které nechá je načase začít službu opětovném zprovoznění Pokud dojde k chybě.

Jak je vysvětleno v [koncové body](iot-hub-devguide-endpoints.md), IoT hub doručí zpětné vazby prostřednictvím koncového bodu služby přístupem, */messages/servicebound/feedback*, jako zprávy. Sémantika pro příjem zpětná vazba je stejný jako u zprávy typu cloud zařízení. Kdykoli je to možné, zpětnou vazbu zpráv v dávce do jedné zprávy v následujícím formátu:

| Vlastnost     | Popis |
| ------------ | ----------- |
| EnqueuedTime | Časové razítko, které určuje, kdy byla přijata zpráva zpětné vazby pomocí centra |
| UserId       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

Text je serializací JSON pole záznamů, každý s následujícími vlastnostmi:

| Vlastnost           | Popis |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | Časové razítko, které určuje, kdy se stalo výsledek zprávy (například centra zobrazila zpráva zpětnou vazbu nebo vypršení platnosti na původní zprávu o) |
| OriginalMessageId  | *MessageId* zprávy typu cloud zařízení, ke kterému se vztahuje tyto informace zpětné vazby |
| StatusCode         | Povinný řetězec použitý v zpráv se zpětnou vazbou, které jsou generovány ve službě IoT hub: <br/> *Úspěch* <br/> *Vypršela platnost* <br/> *DeliveryCountExceeded* <br/> *Zamítnuto* <br/> *Vymazat* |
| Popis        | Hodnoty pro řetězce *StatusCode* |
| DeviceId           | *DeviceId* cílového zařízení zprávy typu cloud zařízení, na který se týká tato složka zpětné vazby |
| DeviceGenerationId | *DeviceGenerationId* cílového zařízení zprávy typu cloud zařízení, na který se týká tato složka zpětné vazby |

Pro zprávy typu cloud zařízení je možné korelovat s na původní zprávu o jeho zpětné vazby, musíte zadat službu *MessageId*.

Text zprávy zpětné vazby můžete vidět v následujícím kódu:

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
| defaultTtlAsIso8601       | Výchozí hodnota TTL pro zprávy typu cloud zařízení | ISO_8601 interval až dvou dnů (minimální 1 minuta); Výchozí hodnota: 1 hodina |
| maxDeliveryCount          | Maximální počet doručení zpráv typu cloud zařízení na zařízení fronty | 1 až 100; Výchozí hodnota: 10 |
| feedback.ttlAsIso8601     | Uchovávání zpráv se zpětnou vazbou vázané služby | ISO_8601 interval až dvou dnů (minimální 1 minuta); Výchozí hodnota: 1 hodina |
| feedback.maxDeliveryCount | Maximální počet doručení zpráv do fronty zpětné vazby | 1 až 100; Výchozí hodnota: 100 |

Další informace o tom, jak nastavit tyto možnosti konfigurace najdete v tématu [vytvořit IoT hub](iot-hub-create-through-portal.md).

## <a name="next-steps"></a>Další postup

Informace o sadách SDK, které můžete použít pro příjem zpráv typu cloud zařízení najdete v tématu [sad SDK Azure IoT](iot-hub-devguide-sdks.md).

Vyzkoušejte si příjem zpráv z cloudu do zařízení, najdete v článku [odesílání typu cloud zařízení](iot-hub-csharp-csharp-c2d.md) kurzu.
