---
title: Vysvětlení přímých metod Azure IoT Hub | Microsoft Docs
description: Příručka pro vývojáře – k vyvolání kódu v zařízeních z aplikace služby použijte přímé metody.
author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: philmea
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: f68705309d15548dc9728686e2e0ecd6aaad3fba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91334102"
---
# <a name="understand-and-invoke-direct-methods-from-iot-hub"></a>Vysvětlení a volání přímých metod ze služby IoT Hub

IoT Hub poskytuje možnost vyvolat z cloudu přímé metody ze zařízení. Přímé metody představují interakci požadavek-odpověď se zařízením podobným volání HTTP v tom, že je úspěšné nebo neúspěšné (po uplynutí časového limitu zadaného uživatelem). Tento přístup je užitečný pro scénáře, ve kterých je průběh okamžité akce odlišný v závislosti na tom, jestli zařízení bylo schopné reagovat.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Každá metoda zařízení cílí na jedno zařízení. [Plánování úloh na několika zařízeních](iot-hub-devguide-jobs.md) ukazuje, jak poskytnout způsob volání přímých metod na více zařízení a naplánovat vyvolání metod pro odpojená zařízení.

Kdokoli s oprávněním **služby Service Connect** v IoT Hub může na zařízení vyvolat metodu.

Přímé metody jsou založené na vzoru Request-response a jsou určeny pro komunikaci, která vyžaduje okamžité potvrzení jejich výsledku. Například interaktivní ovládání zařízení, například zapnutí ventilátoru.

Pokud jste nejistí mezi použitím požadovaných vlastností, přímých metod nebo zpráv z cloudu na zařízení, přečtěte si téma [doprovodné materiály k komunikaci z cloudu na zařízení](iot-hub-devguide-c2d-guidance.md) .

## <a name="method-lifecycle"></a>Životní cyklus metody

V zařízení jsou implementovány přímé metody a mohou vyžadovat nula nebo více vstupů v datové části metody pro správné vytvoření instance. Pomocí identifikátoru URI () s přístupem ke službě jste vyvolali přímou metodu `{iot hub}/twins/{device id}/methods/` . Zařízení přijímá přímé metody pomocí MQTTho tématu ( `$iothub/methods/POST/{method name}/` ) nebo prostřednictvím odkazů AMQP ( `IoThub-methodname` `IoThub-status` Vlastnosti aplikace a).

> [!NOTE]
> Když na zařízení vyvoláte přímou metodu, názvy vlastností a hodnoty můžou obsahovat jen tisknutelné alfanumerické znaky US-ASCII, s výjimkou jakékoli v následující sadě: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``
> 

Přímé metody jsou synchronní a buď úspěšné, nebo neúspěšné, po uplynutí časového limitu (výchozí: 30 sekund, nastavitelné v rozmezí 5 až 300 sekund). Přímé metody jsou užitečné v interaktivních scénářích, kde chcete, aby zařízení fungovalo pouze v případě, že je zařízení online a přijímá příkazy. Můžete například zapnout světlo od telefonu. V těchto scénářích se chcete podívat na bezprostřední úspěch nebo neúspěch, aby cloudová služba mohla co nejdříve fungovat s výsledkem. Zařízení může vracet tělo zprávy jako výsledek metody, ale není nutné, aby to metoda provedla. Není zaručeno řazení ani žádná sémantika souběžnosti při voláních metod.

Přímé metody jsou pouze HTTPS ze strany cloudu a MQTT, AMQP, MQTT přes objekty WebSockets a AMQP přes objekty WebSocket na straně zařízení.

Datová část pro žádosti o metody a odpovědi je dokument JSON až 128 KB.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>Vyvolání přímé metody z back-endové aplikace

Nyní vyvolejte přímou metodu z back-endové aplikace.

### <a name="method-invocation"></a>Vyvolání metody

Přímým voláním metod v zařízení jsou volání HTTPS, která se skládají z následujících položek:

* *Identifikátor URI žádosti* specifický pro zařízení společně s [verzí rozhraní API](https://docs.microsoft.com/rest/api/iothub/service/devices/invokemethod):

    ```http
    https://fully-qualified-iothubname.azure-devices.net/twins/{deviceId}/methods?api-version=2018-06-30
    ```

* *Metoda* post

* *Hlavičky* , které obsahují autorizaci, ID požadavku, typ obsahu a kódování obsahu.

* Transparentní *tělo* JSON v následujícím formátu:

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

Hodnota poskytnutá jako `responseTimeoutInSeconds` v požadavku je doba, po kterou IoT Hub služba musí čekat na dokončení přímé metody provádění na zařízení. Nastavte tento časový limit aspoň tak dlouho, dokud je očekávaná doba provádění přímé metody v zařízení. Pokud není zadán časový limit, je použita výchozí hodnota 30 sekund. Minimální a maximální hodnoty pro `responseTimeoutInSeconds` jsou 5 a 300 sekund v uvedeném pořadí.

Hodnota poskytnutá jako `connectTimeoutInSeconds` v požadavku je doba, po kterou IoT Hub služba musí čekat na to, aby odpojené zařízení bylo online. Výchozí hodnota je 0, což znamená, že zařízení už musí být online při volání přímé metody. Maximální hodnota pro `connectTimeoutInSeconds` je 300 sekund.

#### <a name="example"></a>Příklad

Tento příklad vám umožní bezpečně iniciovat požadavek na vyvolání přímé metody v zařízení IoT, které je zaregistrované ve službě Azure IoT Hub.

Začněte tím, že pomocí [rozšíření Microsoft Azure IoT pro Azure CLI](https://github.com/Azure/azure-iot-cli-extension) vytvoříte SharedAccessSignature.

```bash
az iot hub generate-sas-token -n <iothubName> -du <duration>
```

Potom nahraďte hlavičku autorizace nově vytvořeným SharedAccessSignature a pak změňte `iothubName` `deviceId` `methodName` parametry, a tak, `payload` aby odpovídaly vaší implementaci v `curl` níže uvedeném příkazu příkladu.  

```bash
curl -X POST \
  https://<iothubName>.azure-devices.net/twins/<deviceId>/methods?api-version=2018-06-30 \
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

Spusťte upravený příkaz pro vyvolání zadané přímé metody. Úspěšné požadavky vrátí stavový kód HTTP 200.

> [!NOTE]
> Výše uvedený příklad ukazuje vyvolání přímé metody v zařízení.  Pokud chcete vyvolat přímou metodu v modulu IoT Edge, je nutné upravit požadavek adresy URL, jak je uvedeno níže:

```bash
https://<iothubName>.azure-devices.net/twins/<deviceId>/modules/<moduleName>/methods?api-version=2018-06-30
```
### <a name="response"></a>Odpověď

Back-endové aplikace obdrží odpověď, která se skládá z následujících položek:

* *Stavový kód HTTP*:
  * 200 indikuje úspěšné provedení přímé metody;
  * 404 znamená, že buď ID zařízení není platné, nebo že zařízení nebylo online při volání přímé metody a `connectTimeoutInSeconds` následně (k pochopení hlavní příčiny použijte doplněnou chybovou zprávu);
  * 504 označuje časový limit brány způsobený tím, že zařízení nereaguje na přímé volání metody v rámci `responseTimeoutInSeconds` .

* *Hlavičky* , které obsahují ETag, ID požadavku, typ obsahu a kódování obsahu.

* *Tělo* JSON v následujícím formátu:

    ```json
    {
        "status" : 201,
        "payload" : {...}
    }
    ```

    `status`A `body` jsou k dispozici v zařízení a slouží k reagování na vlastní stavový kód a/nebo popis zařízení.

### <a name="method-invocation-for-iot-edge-modules"></a>Vyvolání metody pro IoT Edge moduly

Vyvolání přímých metod pomocí ID modulu je podporované v [sadě SDK pro klienta služby IoT v jazyce C#](https://www.nuget.org/packages/Microsoft.Azure.Devices/).

Pro účely tohoto účelu použijte `ServiceClient.InvokeDeviceMethodAsync()` metodu a předejte `deviceId` `moduleId` parametry a jako.

## <a name="handle-a-direct-method-on-a-device"></a>Zpracování přímé metody v zařízení

Pojďme se podívat, jak zpracovat přímou metodu na zařízení IoT.

### <a name="mqtt"></a>MQTT

Následující část je určena pro protokol MQTT.

#### <a name="method-invocation"></a>Vyvolání metody

Zařízení dostávají požadavky přímých metod v MQTT tématu: `$iothub/methods/POST/{method name}/?$rid={request id}` . Počet předplatných na zařízení je omezený na 5. Proto se doporučuje neodebírat každou přímo metodu jednotlivě. Místo toho zvažte přihlášení `$iothub/methods/POST/#` a pak filtrování doručených zpráv na základě požadovaných názvů metod.

Tělo, které zařízení obdrží, má následující formát:

```json
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

Požadavky metody jsou QoS 0.

#### <a name="response"></a>Odpověď

Zařízení odesílá odpovědi do `$iothub/methods/res/{status}/?$rid={request id}` , kde:

* `status`Vlastnost je stavem poskytovaných metod, které jsou zadány zařízením.

* `$rid`Vlastnost je ID žádosti z volání metody přijaté z IoT Hub.

Tělo je nastaveno zařízením a může být libovolný stav.

### <a name="amqp"></a>AMQP

Následující část je určena pro protokol AMQP.

#### <a name="method-invocation"></a>Vyvolání metody

Zařízení přijímá požadavky přímých metod vytvořením odkazu pro příjem na adrese `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound` .

Zpráva AMQP dorazí na odkaz Receive, který reprezentuje požadavek metody. Obsahuje následující oddíly:

* Vlastnost ID korelace, která obsahuje ID žádosti, která se má zpětně předat odpovídající odezva metody.

* Vlastnost aplikace s názvem `IoThub-methodname` , která obsahuje název volané metody.

* Tělo zprávy AMQP obsahující datovou část metody jako JSON.

#### <a name="response"></a>Odpověď

Zařízení vytvoří odkaz pro odeslání, který vrátí odpověď metody na adrese `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound` .

Odpověď metody je vrácena na odesílacím odkazu a je strukturována takto:

* Vlastnost ID korelace, která obsahuje ID žádosti předané ve zprávě požadavku metody.

* Vlastnost aplikace s názvem `IoThub-status` , která obsahuje stav metody dodané uživatelem

* Tělo zprávy AMQP obsahující odpověď metody jako JSON.

## <a name="additional-reference-material"></a>Další referenční materiály

Další referenční témata v IoT Hub příručce pro vývojáře zahrnují:

* [IoT Hub koncové body](iot-hub-devguide-endpoints.md) popisují různé koncové body, které jednotlivé služby IoT Hub zpřístupňují pro operace run-time a Management.

* [Omezení a kvóty](iot-hub-devguide-quotas-throttling.md) popisují kvóty, které platí, a omezení chování, které se má očekávat při použití IoT Hub.

* Sady [SDK pro zařízení a služby Azure IoT](iot-hub-devguide-sdks.md) obsahují různé jazykové sady SDK, které můžete použít při vývoji aplikací pro zařízení i služby, které komunikují s IoT Hub.

* [IoT Hub dotazovací jazyk pro vlákna zařízení, úlohy a směrování zpráv](iot-hub-devguide-query-language.md) popisuje dotazovací jazyk IoT Hub, který můžete použít k načtení informací z IoT Hub o nečinnosti zařízení a úlohách.

* [Podpora IoT Hub MQTT](iot-hub-mqtt-support.md) poskytuje další informace o podpoře IoT Hub pro protokol MQTT.

## <a name="next-steps"></a>Další kroky

Naučili jste se, jak používat přímé metody, může vás zajímat následující článek IoT Hub příručka pro vývojáře:

* [Plánování úloh na několika zařízeních](iot-hub-devguide-jobs.md)

Pokud si chcete vyzkoušet některé z konceptů popsaných v tomto článku, může vás zajímat následující IoT Hub kurz:

* [Použití přímých metod](quickstart-control-device-node.md)
* [Správa zařízení s využitím sady Azure IoT Tools pro VS Code](iot-hub-device-management-iot-toolkit.md)
