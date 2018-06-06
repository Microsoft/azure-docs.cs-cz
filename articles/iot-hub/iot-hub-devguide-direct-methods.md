---
title: Pochopení přímé metody Azure IoT Hub | Microsoft Docs
description: Příručka vývojáře - použijte přímý metody k vyvolání kódu na zařízení z aplikace služby.
author: nberdy
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/01/2018
ms.author: nberdy
ms.openlocfilehash: da9672c7a924411136928d8d04e54c2c62a014b9
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736673"
---
# <a name="understand-and-invoke-direct-methods-from-iot-hub"></a>Rady pro pochopení a volat přímé metody ze služby IoT Hub
Centrum IoT vám dává možnost volat přímé metody na zařízení z cloudu. Přímé metody představuje požadavek odpověď interakci s zařízení podobné volání protokolu HTTP v, které budou úspěch nebo neúspěch okamžitě (po časový limit definované uživatelem). Tento přístup je užitečné v případech, kde se liší v závislosti na tom, jestli je zařízení schopné reagovat během okamžitý zásah.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Každá metoda zařízení cílí jedno zařízení. [Úlohy] [ lnk-devguide-jobs] poskytnout způsob, jak volat přímé metody na několika zařízeních a naplánovat volání metody pro odpojené zařízení.

Každý, kdo má **služba připojit** oprávnění na IoT Hub může vyvolat metodu na zařízení.

Přímé metody postupujte podle vzoru požadavků a odpovědí a jsou určené pro komunikaci, které vyžadují okamžitou potvrzení jejich výsledku. Například interaktivní kontrolu zařízení, například zapnutí ventilátoru.

Odkazovat na [Cloud zařízení komunikace pokyny] [ lnk-c2d-guidance] v případě pochybností mezi použitím požadované vlastnosti přímé metody nebo zprávy typu cloud zařízení.

## <a name="method-lifecycle"></a>Životní cyklus – metoda
Přímé metody jsou implementované v zařízení a může vyžadovat nula nebo více vstupů v datové části Metoda správně vytvořit instanci. Vyvolání přímá metoda prostřednictvím identifikátoru URI služby přístupem (`{iot hub}/twins/{device id}/methods/`). Zařízení obdrží přímé metod v tématu MQTT konkrétní zařízení (`$iothub/methods/POST/{method name}/`) nebo prostřednictvím protokolu AMQP odkazů (`IoThub-methodname` a `IoThub-status` vlastnosti aplikace). 

> [!NOTE]
> Při vyvolání přímá metoda na zařízení, názvů a hodnot vlastností mohou obsahovat pouze US-ASCII tisknutelná alfanumerické znaky, s výjimkou těch následující sady: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``.
> 
> 

Přímé metody jsou synchronní a buď úspěšné nebo neúspěšné po uplynutí časového limitu (výchozí: 30 sekund, nastavit až na 3 600 sekund). Přímé metody jsou užitečné v interaktivní scénářích, kde chcete zařízení tak, aby fungoval pouze v případě zařízení je online a přijímající příkazy. Například zapnutí light z telefonního čísla. V těchto scénářích chcete zobrazit okamžité úspěšné nebo neúspěšné, takže cloudové služby může fungovat na výsledek co nejdříve. Zařízení může vrátit některé tělo zprávy v důsledku metodu, ale není nutné u metodu Uděláte to tak. Neexistuje žádná záruka, řazení v nebo všechny souběžnosti sémantiky pro volání metody.

Přímé metody jsou HTTPS jen z cloudu straně a MQTT nebo AMQP ze strany zařízení.

Datová část pro metoda požadavky a odpovědi je až 128 KB dokumentu JSON.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>Volání metody přímé z back-end aplikace
### <a name="method-invocation"></a>Volání metody
Přímá metoda volání na zařízení jsou volání protokolu HTTPS, které tvoří:

* *URI* specifické pro zařízení (`{iot hub}/twins/{device id}/methods/`)
* V příspěvku *– metoda*
* *Hlavičky* , obsahovat autorizaci, žádat o ID, typu obsahu a kódování obsahu
* Transparentní JSON *textu* v následujícím formátu:

    ```json
    {
        "methodName": "reboot",
        "responseTimeoutInSeconds": 200,
        "payload": {
            "input1": "someInput",
            "input2": "anotherInput"
        }
    }
    ```

Časový limit je počet sekund. Pokud není nastavený časový limit, bude výchozí 30 sekund.

### <a name="response"></a>Odpověď
Back-end aplikace obdrží odpověď, který zahrnuje:

* *Stavový kód HTTP*, který se používá pro chyby přicházející ze služby IoT Hub, včetně chybu 404 pro zařízení není aktuálně připojený
* *Hlavičky* , obsahovat značku ETag, žádat o ID, typu obsahu a kódování obsahu
* JSON *textu* v následujícím formátu:

    ```json
    {
        "status" : 201,
        "payload" : {...}
    }
    ```

    Obě `status` a `body` je poskytovanému zařízením a používá odešle odpověď se stavovým kódem zařízení vlastní nebo popis.

### <a name="method-invocation-for-iot-edge-modules"></a>Volání metody pro IoT Edge moduly
Náhled SDK volajícím přímé metody použití modulu ID je podporována v jazyce C# (k dispozici [sem](https://www.nuget.org/packages/Microsoft.Azure.Devices/1.16.0-preview-004)).

Pro tento účel použít `ServiceClient.InvokeDeviceMethodAsync()` metoda a předejte jí `deviceId` a `moduleId` jako parametry.

## <a name="handle-a-direct-method-on-a-device"></a>Popisovač přímá metoda na zařízení
### <a name="mqtt"></a>MQTT
#### <a name="method-invocation"></a>Volání metody
Zařízení obdrží přímá metoda žádosti k tématu MQTT: `$iothub/methods/POST/{method name}/?$rid={request id}`

Text, který zařízení obdrží je v následujícím formátu:

```json
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

Požadavky metod jsou QoS 0.

#### <a name="response"></a>Odpověď
Zařízení odesílá odpovědí `$iothub/methods/res/{status}/?$rid={request id}`, kde:

* `status` Vlastnost je stav zařízení zadaná metoda spouštění.
* `$rid` Vlastnost je ID žádosti z volání metody přijatých ze služby IoT Hub.

Text se nastavuje pomocí zařízení a může být jakékoli stavu.

### <a name="amqp"></a>AMQP
#### <a name="method-invocation"></a>Volání metody
Zařízení přijímá požadavky přímá metoda vytvořením receive odkaz na adresu `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`

AMQP zpráva dorazí na tento odkaz receive, který představuje požadavek metody. Obsahuje následující:
* Vlastnost ID korelace, který obsahuje ID žádosti, které mají být předány zpět s odpovídající reakce – metoda
* Vlastnost aplikací s názvem `IoThub-methodname`, který obsahuje název metody volané
* Tělo zprávy AMQP obsahující datové části Metoda jako JSON

#### <a name="response"></a>Odpověď
Zařízení vytvoří propojení odesílání vrátit odpověď metody na adrese `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`

Metoda odpověď se vrátí na odesílání odkaz a struktura je následující:
* Vlastnost ID korelace, který obsahuje ID požadavku předán ve zprávě požadavku metoda
* Vlastnost aplikací s názvem `IoThub-status`, která obsahuje uživatele zadaná metoda stav
* AMQP tělo zprávy s odpovědí metoda jako JSON

## <a name="additional-reference-material"></a>Odkaz na další materiály
Další témata referenční příručka vývojáře IoT Hub patří:

* [Koncové body centra IoT] [ lnk-endpoints] popisuje různé koncových bodů, které každý IoT hub zpřístupní pro spuštění a management operace.
* [Omezování a kvóty] [ lnk-quotas] popisuje kvóty, které se vztahují a omezení chování se očekává při použití služby IoT Hub.
* [Azure IoT zařízení a služby sady SDK] [ lnk-sdks] uvádí různé jazykové sady SDK můžete použít při vývoji aplikace zařízení a služby, které interakci s centrem IoT.
* [IoT Hub dotazovacího jazyka pro dvojčata zařízení, úlohy a směrování zpráv] [ lnk-query] popisuje dotazovací jazyk Centrum IoT, můžete použít k načtení informací ze služby IoT Hub o úlohách a dvojčata zařízení.
* [Podpora IoT Hub MQTT] [ lnk-devguide-mqtt] poskytuje další informace o podpoře služby IoT Hub pro protokol MQTT.

## <a name="next-steps"></a>Další postup
Nyní jste se naučili použití přímé metod, může zajímat v následujícím článku Příručka vývojáře IoT Hub:

* [Plánování úloh na několika zařízeních][lnk-devguide-jobs]

Pokud chcete vyzkoušet některé konceptů popsaných v tomto článku, může zajímat v následujícím kurzu IoT Hub:

* [Používat přímé metody][lnk-methods-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-devguide-messages]: iot-hub-devguide-messaging.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
