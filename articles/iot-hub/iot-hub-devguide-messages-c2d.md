---
title: Principy zasílání zpráv azure ioT hub u cloudu na zařízení | Dokumenty společnosti Microsoft
description: Tento průvodce pro vývojáře popisuje, jak používat zasílání zpráv cloud-to-device s centrem IoT. Obsahuje informace o životním cyklu zprávy a možnostech konfigurace.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.custom: mqtt
ms.openlocfilehash: 307ab47c1f7498f71e61108a616d35ef1d4f61c9
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729996"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>Odesílání zpráv z cloudu na zařízení z centra IoT hub

Pokud chcete do aplikace zařízení odesílat jednosměrná oznámení z back-endu vašeho řešení, odesílejte do zařízení zprávy z cloudu na zařízení. Diskuse o dalších možnostech cloudu zařízení podporovaných službou Azure IoT Hub najdete v [tématu Pokyny pro komunikaci mezi cloudy](iot-hub-devguide-c2d-guidance.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Zprávy mezi cloudy a zařízení se odesílají prostřednictvím koncového bodu směřujícího ke službě */messages/devicebound*. Zařízení pak přijímá zprávy prostřednictvím koncového bodu specifického pro *zařízení/devices/{deviceId}/messages/devicebound*.

Chcete-li cílit na každou zprávu typu cloud zařízení na jednom zařízení, nastaví služba IoT hub vlastnost */devices/{deviceId}/messages/devicebound*. **to**

Každá fronta zařízení obsahuje maximálně 50 zpráv z cloudu na zařízení. Pokus o odeslání dalších zpráv do stejného zařízení má za následek chybu.

## <a name="the-cloud-to-device-message-life-cycle"></a>Životní cyklus zpráv mezi cloudy

Chcete-li zaručit doručení alespoň jednou zprávy, vaše služba IoT hub přetrvává zprávy z cloudu na zařízení ve frontách na zařízení. Chcete-li, aby centrum IoT centrum odebrala zprávy z fronty, musí zařízení explicitně potvrdit *dokončení*. Tento přístup zaručuje odolnost proti připojení a selhání zařízení.

Graf stavu životního cyklu je zobrazen v následujícím diagramu:

![Životní cyklus zpráv mezi cloudy](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

Když služba Služby Služby IoT hub odešle zprávu do zařízení, služba nastaví stav zprávy *do fronty*. Když zařízení chce *přijímat* zprávy, služba IoT hub *zamkne* zprávu nastavením stavu *Neviditelné*. Tento stav umožňuje ostatním vláknům v zařízení začít přijímat další zprávy. Když vlákno zařízení dokončí zpracování zprávy, upozorní službu IoT hub *dokončením* zprávy. Centrum IoT pak nastaví stav na *Dokončeno*.

Zařízení může také:

* *Odmítnout* zprávu, která způsobí, že centrum IoT nastavit do stavu *Dead lettered.* Zařízení, která se připojují prostřednictvím protokolu MQTT (Message Queuing Telemetry Transport) nemůže odmítnout zprávy z cloudu na zařízení.

* *Opustit* zprávu, která způsobí, že centrum IoT umístit zprávu zpět do fronty, se stavem *nastaveným na zařazené*do fronty . Zařízení, která se připojují přes protokol MQTT, nemohou opustit zprávy z cloudu na zařízení.

Podproces může selhat ke zpracování zprávy bez upozornění služby IoT hub. V tomto případě zprávy automaticky přechod z *neviditelnéstavu* zpět do stavu *Zařazeno* do fronty po *viditelnosti* časový limit (nebo *uzamčení* časového limitu). Hodnota tohoto časového odpoznění je jedna minuta a nelze ji změnit.

Vlastnost **maximálnípočet doručení** v centru IoT určuje maximální počet, kolikrát může zpráva přejít mezi *stavy Enqueued* a *Invisible.* Po tomto počtu přechodů služba IoT hub nastaví stav zprávy na *Dead lettered*. Podobně služba IoT hub nastaví stav zprávy *na dead lettered* po vypršení jeho vypršení platnosti. Další informace naleznete v [tématu Time to live](#message-expiration-time-to-live).

[Jak odesílat zprávy z cloudu na zařízení pomocí služby IoT Hub,](iot-hub-csharp-csharp-c2d.md) ukazuje, jak odesílat zprávy z cloudu na zařízení a přijímat je na zařízení.

Zařízení obvykle dokončí zprávu cloud zařízení, když ztráta zprávy nemá vliv na aplikační logiku. Příkladem může být, když zařízení trvale obsah zprávy místně nebo úspěšně provedl operaci. Zpráva může také přenášet přechodné informace, jejichž ztráta by neměla vliv na funkčnost aplikace. V některých případě můžete u dlouhotrvajících úloh:

* Dokončete zprávu o cloudu zařízení poté, co zařízení v místním úložišti zatrvalo popis úlohy.

* Upozorněte back-end řešení jednou nebo více zprávami zařízení cloud v různých fázích průběhu úkolu.

## <a name="message-expiration-time-to-live"></a>Vypršení platnosti zprávy (doba života)

Každá zpráva cloud-to-device má čas vypršení platnosti. Tento čas je nastaven jedním z následujících:

* Vlastnost **ExpiryTimeUtc** ve službě
* Centrum IoT hub pomocí výchozího *času pro živé vysílání,* který je určen jako vlastnost služby IoT hub

Viz [Možnosti konfigurace cloud-zařízení](#cloud-to-device-configuration-options).

Běžným způsobem, jak využít vypršení platnosti zprávy a vyhnout se odesílání zpráv do odpojených zařízení, je nastavit krátkou *dobu na živé* hodnoty. Tento přístup dosahuje stejného výsledku jako udržování stavu připojení zařízení, ale je efektivnější. Když požádáte o potvrzení zprávy, služba IoT hub vás upozorní, která zařízení jsou:

* Možnost přijímat zprávy.
* Nejsou online nebo selhaly.

## <a name="message-feedback"></a>Zpětná vazba zprávy

Při odeslání zprávy z cloudu na zařízení může služba požádat o doručení zpětné vazby za zprávu o konečném stavu této zprávy. To provést nastavením vlastnosti aplikace **iothub-ack** ve zprávě cloud-to-device, která je odesílána na jednu z následujících čtyř hodnot:

| Hodnota vlastnosti Ack | Chování |
| ------------ | -------- |
| Žádná     | Centrum IoT negeneruje zprávu o zpětné vazbě (výchozí chování). |
| pozitivní | Pokud zpráva cloud-zařízení dosáhne stavu *Dokončeno,* služba IoT hub vygeneruje zprávu o zpětné vazbě. |
| negativní | Pokud zpráva cloud-to-device dosáhne stavu *Nedoručené,* služba IoT hub vygeneruje zprávu o zpětné vazbě. |
| Plné     | Centrum IoT generuje zprávu o zpětné vazbě v obou případech. |

Pokud je hodnota **Potvrzení** *plná*a neobdržíte zprávu o zpětné vazbě, znamená to, že platnost zprávy o zpětné vazbě vypršela. Služba nemůže vědět, co se stalo s původní zprávou. V praxi by služba měla zajistit, že může zpětnou vazbu zpracovat před vypršením její platnosti. Maximální doba vypršení platnosti je dva dny, což ponechává čas na znovu spuštění služby, pokud dojde k chybě.

Jak je vysvětleno v [koncových bodech](iot-hub-devguide-endpoints.md), služba IoT hub poskytuje zpětnou vazbu prostřednictvím koncového bodu směřujícího ke službě, */messages/servicebound/feedback*, jako zprávy. Sémantika pro příjem zpětné vazby jsou stejné jako pro zprávy z cloudu na zařízení. Kdykoli je to možné, je zpětná vazba zpráv dávkována v jedné zprávě v následujícím formátu:

| Vlastnost     | Popis |
| ------------ | ----------- |
| EnqueuedTime | Časové razítko, které označuje, kdy byla zpráva o zpětné vazbě přijata centrem |
| UserId       | `{iot hub name}` |
| Contenttype  | `application/vnd.microsoft.iothub.feedback.json` |

Tělo je serializované pole záznamů JSON, z nichž každý má následující vlastnosti:

| Vlastnost           | Popis |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | Časové razítko, které označuje, kdy došlo k výsledku zprávy (například centrum obdrželo zprávu o zpětné vazbě nebo původní zprávu vypršela) |
| OriginalMessageId  | *MessageId* zprávy cloud-to-device, ke kterému se tyto informace o zpětné vazbě vztahují |
| Statuscode         | Požadovaný řetězec používaný ve zprávách zpětné vazby, které jsou generovány centrem IoT hub: <br/> *Úspěch* <br/> *Platnost vypršela* <br/> *DeliveryCountExceeded* <br/> *Rejected* <br/> *Vymazány* |
| Popis        | Řetězcové hodnoty pro *statuscode* |
| DeviceId           | *DeviceId* cílového zařízení zprávy cloud-to-device, ke kterému se vztahuje tato část zpětné vazby |
| DeviceGenerationId | *DeviceGenerationId* cílového zařízení zprávy cloud-to-device, ke kterému se vztahuje tato část zpětné vazby |

Pro zprávu typu cloud zařízení korelovat jeho zpětnou vazbu s původní zprávy, služba musí zadat *MessageId*.

Text zprávy o zpětné vazbě je zobrazen v následujícím kódu:

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

**Nevyřízená zpětná vazba pro odstraněná zařízení**

Při odstranění zařízení se odstraní také všechny nevyřízené názory. Zpětná vazba zařízení se odesílá v dávkách. Pokud je zařízení odstraněno v úzkém okně (často méně než 1 sekunda) mezi tím, kdy zařízení potvrdí přijetí zprávy, a při přípravě další dávky zpětné vazby, nedojde k zpětné vazbě.

Toto chování můžete vyřešit tak, že před odstraněním zařízení čekáte na určitou dobu, než dorazí nevyřízená zpětná vazba. Související zpětná vazba zpráv by měla být považována za ztracenou po odstranění zařízení.

## <a name="cloud-to-device-configuration-options"></a>Možnosti konfigurace mezi cloudy

Každé centrum IoT zpřístupňuje následující možnosti konfigurace pro zasílání zpráv mezi cloudy:

| Vlastnost                  | Popis | Rozsah a výchozí |
| ------------------------- | ----------- | ----------------- |
| výchozí TtlAsIso8601       | Výchozí Hodnota TTL pro zprávy mezi cloudy | ISO_8601 interval až 2 dny (minimálně 1 minuta); výchozí: 1 hodina |
| maxDeliveryCount          | Maximální počet doručení pro fronty mezi cloud-zařízení na zařízení | 1 až 100; výchozí: 10 |
| feedback.ttlAsIso8601     | Uchovávání informací pro zprávy zpětné vazby vázané na služby | ISO_8601 interval až 2 dny (minimálně 1 minuta); výchozí: 1 hodina |
| feedback.maxDeliveryCount | Maximální počet doručení pro frontu zpětné vazby | 1 až 100; výchozí: 10 |
| feedback.lockDurationAsIso8601 | Maximální počet doručení pro frontu zpětné vazby | ISO_8601 interval od 5 do 300 sekund (minimálně 5 sekund); výchozí: 60 sekund. |

Možnosti konfigurace můžete nastavit jedním z následujících způsobů:

* **Portál Azure**: V části **Nastavení** ve službě IoT hub vyberte integrované **koncové body** a **rozbalte Cloud na zasílání zpráv o zařízení**. (Nastavení vlastností **feedback.maxDeliveryCount** a **feedback.lockDurationAsIso8601** není aktuálně podporováno na webu Azure Portal.)

    ![Nastavení možností konfigurace pro zasílání zpráv mezi cloudy na webu na portálu](./media/iot-hub-devguide-messages-c2d/c2d-configuration-portal.png)

* **Azure CLI**: Použijte příkaz [aktualizace centra az iot:](https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az-iot-hub-update)

    ```azurecli
    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.defaultTtlAsIso8601=PT1H0M0S

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.maxDeliveryCount=10

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.ttlAsIso8601=PT1H0M0S

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.maxDeliveryCount=10

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.lockDurationAsIso8601=PT0H1M0S
    ```

## <a name="next-steps"></a>Další kroky

Informace o sadách SDK, které můžete použít k příjmu zpráv mezi cloudy, naleznete v [tématu sady Azure IoT SDK](iot-hub-devguide-sdks.md).

Informace o tom, jak se vám budou přiskytovat zprávy z cloudu na zařízení, najdete v tématu Odeslání kurzu [o připojení ke zařízení.](iot-hub-csharp-csharp-c2d.md)
