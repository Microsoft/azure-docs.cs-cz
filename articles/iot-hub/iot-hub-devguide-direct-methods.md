---
title: Principy Azure IoT Hub přímých metod | Dokumentace Microsoftu
description: Příručka pro vývojáře – použití přímých metod pro vyvolání kódu na vaše zařízení z aplikace app service.
author: nberdy
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: nberdy
ms.openlocfilehash: 17402911fee5363d707547e73df7da30be6dcd0e
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53343798"
---
# <a name="understand-and-invoke-direct-methods-from-iot-hub"></a>Pochopení a vyvolání přímých metod ze služby IoT Hub

IoT Hub vám umožňuje vyvolání přímých metod v zařízení z cloudu. Přímé metody představují požadavek odpověď interakce se zařízením podobný volání protokolu HTTP, úspěch nebo selhání okamžitě (po uživatelem zadaný časový). Tento přístup je užitečný pro scénáře, ve kterém se liší v závislosti na tom, jestli je zařízení schopné reagovat kurzu okamžitý zásah.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Každá metoda zařízení cílí na jedno zařízení. [Plánování úloh na několika zařízeních](iot-hub-devguide-jobs.md) ukazuje, jak poskytnout způsob, jak vyvolání přímých metod v různých zařízeních a naplánovat volání metody pro odpojená zařízení.

Každý, kdo má **služba připojit** oprávnění ve službě IoT Hub může vyvolat metody v zařízení.

Přímé metody se řídí vzorem typu žádost odpověď a jsou určené pro komunikaci, které vyžadují okamžitou potvrzení jejich výsledek. Například interaktivního ovládání zařízení, jako je například zapnutí ventilátoru.

Odkazovat na [pokyny komunikaci typu Cloud zařízení](iot-hub-devguide-c2d-guidance.md) v případě pochybností mezi použitím požadované vlastnosti přímé metody nebo zprávy typu cloud zařízení.

## <a name="method-lifecycle"></a>Životní cyklus – metoda

Přímé metody jsou implementovány v zařízení a může vyžadovat nula nebo více vstupů v datové části metody správně vytvořit instanci. Vyvolání přímé metody prostřednictvím služby přístupem k identifikátoru URI (`{iot hub}/twins/{device id}/methods/`). Zařízení obdrží přímých metod v tématu MQTT konkrétní zařízení (`$iothub/methods/POST/{method name}/`) nebo prostřednictvím protokolu AMQP odkazů ( `IoThub-methodname` a `IoThub-status` vlastnosti aplikace). 

> [!NOTE]
> Při vyvolání přímé metody v zařízení, názvy a hodnoty vlastností mohou obsahovat pouze US-ASCII tisknutelný alfanumerické znaky, s výjimkou těch následující: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``
> 

Přímé metody jsou synchronní a buď úspěšné nebo neúspěšné po uplynutí časového limitu (výchozí: 30 sekund, nastavit až na 3 600 sekund). Přímé metody jsou užitečné pro interaktivní scénáře, ve kterém chcete zařízení tak, aby fungoval pouze v případě zařízení je online a příjem příkazů. Například zapnutí světla z telefonu. V těchto scénářích platí budete chtít najdete v článku okamžité úspěch nebo neúspěch, cloudové služby můžou jednat výsledek co nejdříve. Zařízení může vrátit některé zprávy jako výsledek metody, ale není to nutné pro metodu Uděláte to tak. Neexistuje žádná záruka na řazení nebo jakékoli souběžnosti sémantiky pro volání metody.

Přímé metody jsou jenom HTTPS z cloudu na straně a protokol MQTT nebo AMQP ze strany zařízení.

Datová část pro metodu požadavky a odpovědi je až 128 KB dokumentu JSON.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>Vyvolání přímé metody z back endové aplikace

Nyní vyvolání přímé metody z back endové aplikace.

### <a name="method-invocation"></a>Volání metody

Vyvolání přímé metody v zařízení jsou HTTPS volání, která se skládá z následujících položek:

* *Identifikátor URI žádosti* specifické pro zařízení společně s [verze rozhraní API](/rest/api/iothub/service/invokedevicemethod):

    ```http
    https://fully-qualified-iothubname.azure-devices.net/twins/{deviceId}/methods?api-version=2018-06-30
    ```

* PŘÍSPĚVEK *– metoda*

* *Záhlaví* , které obsahují autorizaci, identifikátor ID, typu obsahu a kódování obsahu.

* Transparentní JSON *tělo* v následujícím formátu:

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

Časový limit je počet sekund. Pokud časový limit není nastavená, použije se výchozí 30 sekund.

#### <a name="example"></a>Příklad:

Níže jsou uvedeny barebone příklad použití `curl`. 

```bash
curl -X POST \
  https://iothubname.azure-devices.net/twins/myfirstdevice/methods?api-version=2018-06-30 \
  -H 'Authorization: SharedAccessSignature sr=iothubname.azure-devices.net&sig=x&se=x&skn=iothubowner' \
  -H 'Content-Type: application/json' \
  -d '{
    "methodName": "reboot",
    "responseTimeoutInSeconds": 200,
    "payload": {
        "input1": "someInput",
        "input2": "anotherInput"
    }
}'
```

### <a name="response"></a>Odpověď

Back endové aplikace obdrží odpověď, která se skládá z následujících položek:

* *Stavový kód HTTP*, který se používá pro chyby pocházející ze služby IoT Hub, včetně chybu 404 pro zařízení aktuálně připojeno.

* *Záhlaví* , které obsahují značku ETag, identifikátor ID, typu obsahu a kódování obsahu.

* JSON *tělo* v následujícím formátu:

    ```json
    {
        "status" : 201,
        "payload" : {...}
    }
    ```

    Obě `status` a `body` poskytovanému zařízením a použít k reakci kód stavu zařízení vlastní a/nebo popis.

### <a name="method-invocation-for-iot-edge-modules"></a>Volání metody pro moduly IoT Edge

Vyvolání přímých metod pomocí ID je podporována v modulu [klienta jazyka C# sady SDK služby IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/).

Pro tento účel použít `ServiceClient.InvokeDeviceMethodAsync()` metoda a předejte jí `deviceId` a `moduleId` jako parametry.

## <a name="handle-a-direct-method-on-a-device"></a>Popisovač přímé metody v zařízení

Podívejme se na tom, jak se zpracovávají přímé metody v zařízení IoT.

### <a name="mqtt"></a>MQTT

Následující část se týká protokolu MQTT.

#### <a name="method-invocation"></a>Volání metody

Zařízení přijímat žádosti o přímé metody v tématu MQTT: `$iothub/methods/POST/{method name}/?$rid={request id}`. Počet předplatných za zařízení, která je omezena na 5. Proto doporučujeme se přihlaste se k odběru každého přímé metody jednotlivě. Místo toho zvažte přihlášení k odběru `$iothub/methods/POST/#` a vyfiltrujte doručené zprávy založené na názvy požadovanou metodu.

Text, který se má zařízení přijímat je v následujícím formátu:

```json
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

Metoda žádosti se QoS 0.

#### <a name="response"></a>Odpověď

Zařízení pošle odpovědi na `$iothub/methods/res/{status}/?$rid={request id}`, kde:

* `status` Vlastnost je stav provádění metody poskytované zařízení.

* `$rid` Vlastnost představuje ID žádosti z volání metody přijatých ze služby IoT Hub.

Text je nastavena podle zařízení a může být libovolný stav.

### <a name="amqp"></a>AMQP

Následující část se týká protokolu AMQP.

#### <a name="method-invocation"></a>Volání metody

Zařízení obdrží požadavky na přímou metodu tak, že vytvoříte receive odkaz na adrese `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`.

AMQP zpráva dorazí na přijímání odkaz, který představuje požadavek metody. Obsahuje následující části:

* Vlastnost ID korelace, který obsahuje ID žádosti, které by měly být předány zpět s odpovídající odpověď metody.

* Vlastnost aplikací s názvem `IoThub-methodname`, který obsahuje název volané metody.

* Tělo zprávy protokolu AMQP obsahující datové části metody jako dokumenty JSON.

#### <a name="response"></a>Odpověď

Zařízení vytvoří odesílání odkaz k návratu metoda odpověď na adrese `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`.

Metody odpověď se vrátí odesílající odkaz a strukturovaná následujícím způsobem:

* Vlastnost ID korelace, který obsahuje ID žádosti předané zprávy s požadavkem metodu.

* Vlastnost aplikací s názvem `IoThub-status`, který obsahuje uživatele, zadaný stav metody.

* AMQP text zprávy s odpovědí metoda jako dokumenty JSON.

## <a name="additional-reference-material"></a>Další referenční materiál

Další referenční témata v příručce pro vývojáře IoT Hub patří:

* [Koncové body IoT Hubu](iot-hub-devguide-endpoints.md) popisuje různé koncové body, které každý IoT hub zpřístupní pro operace za běhu a správy.

* [Omezování a kvótách](iot-hub-devguide-quotas-throttling.md) popisuje, které se vztahují kvóty a omezování chování očekávat, když pomocí služby IoT Hub.

* [Azure IoT zařízení a služby sady SDK](iot-hub-devguide-sdks.md) uvádí různé jazykové sady SDK můžete použít při vývoji aplikace s zařízení i služby, které pracují s centrem IoT.

* [Dotazovací jazyk služby IoT Hub pro dvojčata zařízení, úlohy a směrování zpráv](iot-hub-devguide-query-language.md) popisuje dotazovací jazyk služby IoT Hub můžete použít k načtení informací ze služby IoT Hub o dvojčata zařízení a úlohy.

* [Podpora IoT Hub MQTT](iot-hub-mqtt-support.md) poskytuje další informace o podpoře služby IoT Hub pro protokolu MQTT.

## <a name="next-steps"></a>Další postup

Nyní jste se naučili, jak použití přímých metod, vás může zajímat v následujícím článku Příručka pro vývojáře IoT Hub:

* [Plánování úloh na několika zařízeních](iot-hub-devguide-jobs.md)

Pokud chcete vyzkoušet si některé koncepty popsané v tomto článku, vás může zajímat v následujícím kurzu služby IoT Hub:

* [Použití přímých metod](quickstart-control-device-node.md)
* [Správa zařízení pomocí sady Azure IoT Hub Toolkit pro VS Code](iot-hub-device-management-iot-toolkit.md)
