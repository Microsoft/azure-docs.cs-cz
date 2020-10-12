---
title: Vysvětlení zasílání zpráv z cloudu na zařízení v Azure IoT Hub | Microsoft Docs
description: Tato příručka pro vývojáře popisuje, jak používat zasílání zpráv z cloudu na zařízení ve službě IoT Hub. Obsahuje informace o životním cyklu zprávy a možnostech konfigurace.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.custom: mqtt
ms.openlocfilehash: 307ab47c1f7498f71e61108a616d35ef1d4f61c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "81729996"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>Posílání zpráv z cloudu na zařízení ze služby IoT Hub

Pokud chcete poslat jednosměrová oznámení do aplikace zařízení z back-endu vašeho řešení, pošlete do svého zařízení zprávy typu cloud-zařízení ze služby IoT Hub. Diskuzi o dalších možnostech cloudu na zařízení, které podporuje Azure IoT Hub, najdete v tématu [pokyny pro komunikaci z cloudu na zařízení](iot-hub-devguide-c2d-guidance.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Zprávy typu cloud-zařízení odesíláte prostřednictvím koncového bodu s přístupem ke službě */Messages/devicebound*. Zařízení pak obdrží zprávy přes koncový bod specifický pro zařízení */Devices/{deviceId}/Messages/devicebound*.

Chcete-li zacílit každou zprávu typu cloud-zařízení na jednom zařízení, vaše centrum IoT nastaví vlastnost **na** hodnotu */Devices/{deviceId}/Messages/devicebound*.

Každá fronta zařízení obsahuje maximálně 50 zpráv z cloudu na zařízení. Chcete-li se pokusit odeslat více zpráv na stejné zařízení, dojde k chybě.

## <a name="the-cloud-to-device-message-life-cycle"></a>Životní cyklus zpráv z cloudu do zařízení

Abychom zajistili, že vaše centrum IoT udržuje zprávy z cloudu na zařízení ve frontách jednotlivých zařízení, zaručí vám nepřesnou doručení zpráv. Aby služby IoT Hub odebrala zprávy z fronty, zařízení musí explicitně potvrdit *dokončení*. Tento přístup zaručuje odolnost proti chybám připojení a zařízení.

Graf stavu životního cyklu se zobrazí v následujícím diagramu:

![Životní cyklus zpráv z cloudu do zařízení](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

Když služba IoT Hub pošle zprávu na zařízení, služba nastaví stav zprávy na *zařazování do fronty*. Když zařízení chce *přijmout* zprávu, služba IoT Hub zprávu *zamkne* nastavením stavu na *neviditelné*. Tento stav umožňuje ostatním vláknům na zařízení začít přijímat další zprávy. Když vlákno zařízení dokončí zpracování zprávy, upozorní centrum IoT o *dokončení* zprávy. Centrum IoT pak nastaví stav na *dokončeno*.

Zařízení může také:

* *Odmítněte* zprávu, což způsobí, že centrum IoT ho nastaví na *nedoručený* stav. Zařízení, která se připojují přes přenos telemetrie služby Řízení front zpráv (MQTT), nemůžou odmítat zprávy z cloudu na zařízení.

* *Abandon* Zrušte zprávu, která způsobí, že centrum IoT vloží zprávu zpátky do fronty s stavem nastaveným na *zařazování*do fronty. Zařízení, která se připojují přes protokol MQTT, nemůžou opustit zprávy z cloudu na zařízení.

Vlákno se nepovedlo zpracovat zprávu bez upozorňování centra IoT. V takovém případě zprávy automaticky přecházejí z *neviditelného* stavu zpátky do *fronty* po vypršení časového limitu *viditelnosti* (nebo vypršení časového limitu *zámku* ). Hodnota časového limitu je jedna minuta a nedá se změnit.

Vlastnost **maximální počet doručení** ve službě IoT Hub určuje maximální počet pokusů, které může zpráva mezi *zařazováním* a *neviditelnými* stavy přecházet. Po tomto počtu přechodů centrum IoT nastaví stav zprávy na *nedoručené písmeno*. Podobně centrum IoT nastaví stav zprávy na *nedoručené písmeno* po uplynutí doby jeho platnosti. Další informace najdete v tématu [Time to Live](#message-expiration-time-to-live).

[Postup posílání zpráv typu cloud-zařízení pomocí IoT Hub](iot-hub-csharp-csharp-c2d.md) článku ukazuje, jak odesílat zprávy z cloudu na zařízení z cloudu a přijímat je na zařízení.

Zařízení obvykle dokončí zprávu typu cloud-zařízení, pokud ztráta zprávy nemá vliv na logiku aplikace. To může být například v případě, že zařízení trvale zachovalo obsah zprávy nebo úspěšně provedlo operaci. Zpráva může také vést k přechodným informacím, jejichž ztráta by ovlivnila funkčnost aplikace. V případě dlouhotrvajících úloh je někdy možné:

* Dokončete zprávu Cloud-zařízení poté, co zařízení trvale zachová popis úlohy v místním úložišti.

* Upozorněte back-endu řešení s jednou nebo více zprávami typu zařízení-Cloud v různých fázích postupu úkolu.

## <a name="message-expiration-time-to-live"></a>Vypršení platnosti zprávy (Time to Live)

Každé zprávě z cloudu na zařízení je čas vypršení platnosti. Tato doba je nastavena jedním z následujících způsobů:

* Vlastnost **ExpiryTimeUtc** ve službě
* Služba IoT Hub, která používá výchozí hodnotu *TTL (Time to Live* ), která je určená jako vlastnost služby IoT Hub

Viz [Možnosti konfigurace Cloud-zařízení](#cloud-to-device-configuration-options).

Běžný způsob, jak využít výhod vypršení platnosti zprávy a zabránit posílání zpráv na odpojená zařízení, je nastavit krátkou *dobu na živé* hodnoty. Tento přístup dosahuje stejného výsledku jako udržování stavu připojení zařízení, ale je efektivnější. Po podání žádosti o potvrzení zpráv vás Centrum IoT upozorní na to, jaká zařízení jsou:

* Může přijímat zprávy.
* Nejsou online nebo se nezdařily.

## <a name="message-feedback"></a>Zpráva o zpětné vazbě

Když odešlete zprávu typu cloud-zařízení, může služba požádat o doručení zpětné vazby na základě zprávy o konečném stavu této zprávy. To provedete nastavením vlastnosti aplikace **iothub-ACK** ve zprávě typu cloud-zařízení, která se odesílá do jedné z následujících čtyř hodnot:

| Hodnota vlastnosti ACK | Chování |
| ------------ | -------- |
| žádné     | Centrum IoT negeneruje zprávu zpětné vazby (výchozí chování). |
| pozitivní | Pokud zpráva typu cloud-zařízení dosáhne stavu *dokončeno* , Centrum IoT vygeneruje zprávu o zpětné vazbě. |
| negativní | Pokud zpráva typu cloud-zařízení dosáhne *nedoručeného písmena* , Centrum IoT vygeneruje zprávu zpětné vazby. |
| kompletní     | Centrum IoT v obou případech vygeneruje zprávu zpětné vazby. |

Pokud je hodnota **ACK** *plná*a neobdržíte zprávu o zpětné vazbě, znamená to, že vypršela platnost zprávy zpětné vazby. Služba nemůže zjistit, co se stalo s původní zprávou. V praxi by služba měla zajistit, aby mohla zpracovat zpětnou vazbu před vypršením platnosti. Maximální doba vypršení platnosti je dva dny, což ponechá čas k opětovnému spuštění služby, pokud dojde k selhání.

Jak je vysvětleno v [koncových bodech](iot-hub-devguide-endpoints.md), služba IoT Hub poskytuje zpětnou vazbu prostřednictvím koncového bodu s přístupem ke službě, */Messages/servicebound/Feedback*jako zprávy. Sémantika pro příjem zpětné vazby je stejná jako u zpráv z cloudu na zařízení. Kdykoli je to možné, zpětná vazba je dávkovaná v jedné zprávě s následujícím formátem:

| Vlastnost     | Popis |
| ------------ | ----------- |
| EnqueuedTime | Časové razítko, které indikuje, že centrum přijalo zprávu zpětné vazby |
| UserId       | `{iot hub name}` |
| Třída  | `application/vnd.microsoft.iothub.feedback.json` |

Tělo je pole záznamů serializovaných ve formátu JSON, z nichž každá má následující vlastnosti:

| Vlastnost           | Popis |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | Časové razítko, které indikuje, kdy došlo k výsledku zprávy (například centrum přijalo zprávu o zpětné vazbě nebo původní zpráva vypršela) |
| OriginalMessageId  | Parametr *MessageID* zprávy typu cloud-zařízení, na kterou se vztahují tyto informace o zpětné vazbě |
| StatusCode         | Požadovaný řetězec, který se používá ve zprávách zpětné vazby, které jsou generovány službou IoT Hub: <br/> *Nástup* <br/> *Platnost vypršela* <br/> *DeliveryCountExceeded* <br/> *Zamítnuto* <br/> *Odstraněna* |
| Description        | Hodnoty řetězce pro *StatusCode* |
| DeviceId           | *DeviceID* cílového zařízení zprávy typu cloud-zařízení, na které se vztahuje tato zpětná vazba |
| DeviceGenerationId | *DeviceGenerationId* cílového zařízení zprávy typu cloud-zařízení, na které se vztahuje tato zpětná vazba |

Aby zpráva typu cloud-zařízení mohla korelovat svou zpětnou vazbu s původní zprávou, musí služba specifikovat parametr *MessageID*.

Text zprávy zpětné vazby je zobrazen v následujícím kódu:

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

**Nedokončená zpětná vazba pro Odstraněná zařízení**

Při odstranění zařízení se odstraní také všechny nedokončené názory. Zpětná vazba ze zařízení se posílá v dávkách. Pokud se zařízení odstraní v úzkém okně (často méně než 1 sekunda) mezi tím, kdy zařízení potvrdí příjem zprávy a když se dokončí dávka dalšího názoru, zpětná vazba se neuskuteční.

Toto chování můžete vyřešit tak, že před odstraněním svého zařízení počkáte na dobu, po kterou přijdete o zpětnou vazbu. Po odstranění zařízení by se měla v související zpětné vazbě zprávy předpokládat prohra.

## <a name="cloud-to-device-configuration-options"></a>Možnosti konfigurace z cloudu na zařízení

Každá služba IoT Hub zpřístupňuje následující možnosti konfigurace pro zasílání zpráv z cloudu na zařízení:

| Vlastnost                  | Popis | Rozsah a výchozí |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | Výchozí hodnota TTL pro zprávy z cloudu na zařízení | Interval ISO_8601 až 2 dny (minimálně 1 minuta); Výchozí hodnota: 1 hodina |
| maxDeliveryCount          | Maximální počet doručení pro fronty z cloudu na zařízení podle zařízení | 1 až 100; výchozí: 10 |
| Feedback. ttlAsIso8601     | Uchovávání zpráv o zpětné vazbě vázaných na služby | Interval ISO_8601 až 2 dny (minimálně 1 minuta); Výchozí hodnota: 1 hodina |
| Feedback. maxDeliveryCount | Maximální počet doručení pro frontu zpětné vazby | 1 až 100; výchozí: 10 |
| Feedback. lockDurationAsIso8601 | Maximální počet doručení pro frontu zpětné vazby | Interval ISO_8601 od 5 do 300 sekund (minimálně 5 sekund); Výchozí hodnota: 60 sekund. |

Možnosti konfigurace můžete nastavit jedním z následujících způsobů:

* **Azure Portal**: v části **Nastavení** ve službě IoT Hub vyberte **Předdefinované koncové body** a rozbalte položku **Cloud na zasílání zpráv zařízení**. (Nastavení vlastností **Feedback. maxDeliveryCount** a **Feedback. lockDurationAsIso8601** aktuálně není v Azure Portal podporován.)

    ![Nastavení možností konfigurace pro zasílání zpráv z cloudu na zařízení na portálu](./media/iot-hub-devguide-messages-c2d/c2d-configuration-portal.png)

* **Azure CLI**: použijte příkaz [AZ IoT Hub Update](https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az-iot-hub-update) :

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

Informace o sadách SDK, které můžete použít pro příjem zpráv z cloudu na zařízení, najdete v tématu sady [SDK služby Azure IoT](iot-hub-devguide-sdks.md).

Pokud si chcete vyzkoušet příjem zpráv z cloudu na zařízení, přečtěte si kurz [odeslání cloudu na zařízení](iot-hub-csharp-csharp-c2d.md) .
