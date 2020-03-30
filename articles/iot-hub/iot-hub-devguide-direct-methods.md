---
title: Principy přímých metod služby Azure IoT Hub | Dokumenty společnosti Microsoft
description: Průvodce pro vývojáře – pomocí přímých metod můžete vyvolat kód na vašich zařízeních ze servisní aplikace.
author: nberdy
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: rezas
ms.openlocfilehash: d4040a4d0cf3fadf7a6e07c0e03e105975d17040
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499262"
---
# <a name="understand-and-invoke-direct-methods-from-iot-hub"></a>Vysvětlení a volání přímých metod ze služby IoT Hub

Služba IoT Hub umožňuje vyvolat přímé metody na zařízeních z cloudu. Přímé metody představují interakci požadavku a odpovědi se zařízením podobným volání HTTP v tom, že se jim podaří nebo okamžitě selže (po časovém udůsledku zadaném uživatelem). Tento přístup je užitečný pro scénáře, kde se průběh okamžité akce liší v závislosti na tom, zda zařízení bylo schopno reagovat.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Každá metoda zařízení cílí na jedno zařízení. [Naplánovat úlohy na více zařízeních](iot-hub-devguide-jobs.md) ukazuje, jak poskytnout způsob, jak vyvolat přímé metody na více zařízeních a naplánovat vyvolání metody pro odpojená zařízení.

Kdokoli s oprávněními **připojení služby** na IoT Hub může vyvolat metodu na zařízení.

Přímé metody postupujte podle vzoru požadavku a odpovědi a jsou určeny pro komunikaci, které vyžadují okamžité potvrzení jejich výsledku. Například interaktivní ovládání zařízení, například zapnutí ventilátoru.

V případě pochybností mezi použitím požadovaných vlastností, přímými metodami nebo zprávami z cloudu na zařízení se podívejte na pokyny pro komunikaci mezi [cloudovými](iot-hub-devguide-c2d-guidance.md) zařízeními.

## <a name="method-lifecycle"></a>Životní cyklus metody

Přímé metody jsou implementovány na zařízení a může vyžadovat nula nebo více vstupů v datové části metody správně konstanci. Můžete vyvolat přímou metodu prostřednictvím`{iot hub}/twins/{device id}/methods/`identifikátoru URI směřujícího ke službě ( ). Zařízení přijímá přímé metody prostřednictvím tématu MQTT specifického pro zařízení`$iothub/methods/POST/{method name}/`( `IoThub-methodname` `IoThub-status` ) nebo prostřednictvím propojení AMQP (vlastnosti a vlastnosti aplikace). 

> [!NOTE]
> Při vyvolání přímé metody na zařízení mohou názvy a hodnoty vlastností obsahovat pouze tisknutelné alfanumerické funkce US-ASCII, s výjimkou jakékoli v následující sadě:``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``
> 

Přímé metody jsou synchronní a buď úspěšné nebo neúspěšné po vypršení časového limitu (výchozí: 30 sekund, settable mezi 5 a 300 sekund). Přímé metody jsou užitečné v interaktivních scénářích, kde chcete, aby zařízení jednalo pouze v případě, že je zařízení online a přijímá příkazy. Například zapnutí světla z telefonu. V těchto scénářích chcete zobrazit okamžitý úspěch nebo neúspěch, aby cloudová služba mohla co nejdříve fungovat na výsledek. Zařízení může vrátit některé tělo zprávy v důsledku metody, ale není nutné pro metodu, aby tak učinily. Neexistuje žádná záruka na řazení nebo jakékoli souběžnosti sémantiku na volání metody.

Přímé metody jsou pouze https z cloudu straně a MQTT nebo AMQP ze strany zařízení.

Datová část pro požadavky metod a odpovědi je dokument JSON až do 128 KB.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>Vyvolání přímé metody z back-endové aplikace

Nyní vyvolat přímou metodu z back-endové aplikace.

### <a name="method-invocation"></a>Vyvolání metody

Přímé vyvolání metody na zařízení jsou volání HTTPS, které se skládají z následujících položek:

* *Požadavek URI* specifický pro zařízení spolu s [verzí rozhraní API](/rest/api/iothub/service/devicemethod/invokedevicemethod):

    ```http
    https://fully-qualified-iothubname.azure-devices.net/twins/{deviceId}/methods?api-version=2018-06-30
    ```

* *Metoda* POST

* *Záhlaví,* která obsahují autorizaci, ID žádosti, typ obsahu a kódování obsahu.

* Průhledný *tělo* JSON v následujícím formátu:

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

Hodnota zadanou `responseTimeoutInSeconds` jako v požadavku je doba, kterou služba Služby IoT Hub musí čekat na dokončení přímého spuštění metody na zařízení. Nastavte tento časový plán být alespoň tak dlouho, jak očekávaná doba spuštění přímé metody zařízením. Pokud časový limit není k dispozici, použije se výchozí hodnota 30 sekund. Minimální a maximální `responseTimeoutInSeconds` hodnoty pro jsou 5 a 300 sekund.

Hodnota zadanou `connectTimeoutInSeconds` jako v požadavku je doba po vyvolání přímé metody, kterou musí služba IoT Hub čekat na odpojené zařízení, které má být online. Výchozí hodnota je 0, což znamená, že zařízení musí být již online při vyvolání přímé metody. Maximální hodnota `connectTimeoutInSeconds` pro je 300 sekund.


#### <a name="example"></a>Příklad

Viz níže pro barebone `curl`příklad pomocí . 

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

Back-endová aplikace obdrží odpověď, která se skládá z následujících položek:

* *Stavový kód HTTP*:
  * 200 označuje úspěšné provedení přímé metody;
  * 404 označuje, že buď ID zařízení je neplatný, nebo že zařízení `connectTimeoutInSeconds` nebylo on-line na vyvolání přímé metody a pro poté (použijte doprovázené chybové zprávy pochopit hlavní příčinu);
  * 504 označuje časový čas brány způsobený tím, že `responseTimeoutInSeconds`zařízení nereaguje na přímé volání metody v rámci .

* *Záhlaví,* která obsahují eTag, ID žádosti, typ obsahu a kódování obsahu.

* Tělo *JSON* v následujícím formátu:

    ```json
    {
        "status" : 201,
        "payload" : {...}
    }
    ```

    Oba `status` `body` a jsou poskytovány zařízením a slouží k odpovědi s vlastním stavovým kódem a / nebo popisem zařízení.

### <a name="method-invocation-for-iot-edge-modules"></a>Vyvolání metody pro moduly IoT Edge

Vyvolání přímých metod pomocí ID modulu je podporováno v sadě [SDK klienta služby IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/).

Pro tento účel `ServiceClient.InvokeDeviceMethodAsync()` použijte metodu `deviceId` a `moduleId` předat a jako parametry.

## <a name="handle-a-direct-method-on-a-device"></a>Zpracování přímé metody na zařízení

Podívejme se na to, jak zpracovat přímou metodu na zařízení IoT.

### <a name="mqtt"></a>MQTT

Následující část je pro protokol MQTT.

#### <a name="method-invocation"></a>Vyvolání metody

Zařízení přijímat přímé požadavky na metody na `$iothub/methods/POST/{method name}/?$rid={request id}`téma MQTT: . Počet předplatných na zařízení je omezen na 5. Proto se doporučuje nepřihlašovat se k odběru každé přímé metody jednotlivě. Místo toho zvažte `$iothub/methods/POST/#` přihlášení k odběru a filtrování dodaných zpráv na základě požadovaných názvů metod.

Tělo, které zařízení obdrží, je v následujícím formátu:

```json
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

Požadavky na metody jsou QoS 0.

#### <a name="response"></a>Odpověď

Zařízení odesílá odpovědi `$iothub/methods/res/{status}/?$rid={request id}`do aplikace , kde:

* Vlastnost `status` je stav spuštění metody dodané zařízením.

* Vlastnost `$rid` je ID požadavku z vyvolání metody přijaté z ioT Hub.

Tělo je nastaveno zařízením a může být libovolný stav.

### <a name="amqp"></a>AMQP

Následující část je pro protokol AMQP.

#### <a name="method-invocation"></a>Vyvolání metody

Zařízení přijímá přímé požadavky na metody vytvořením `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`přijínacího odkazu na adrese .

Zpráva AMQP dorazí na odkaz pro příjem, který představuje požadavek na metodu. Obsahuje následující oddíly:

* Vlastnost ID korelace, která obsahuje ID požadavku, který by měl být předán zpět s odpovídající odpověď metody.

* Vlastnost aplikace `IoThub-methodname`s názvem , která obsahuje název volané metody.

* Tělo zprávy AMQP obsahující datovou část metody jako JSON.

#### <a name="response"></a>Odpověď

Zařízení vytvoří odkaz pro odesílání, který `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`vrátí odpověď metody na adresu .

Odpověď metody je vrácena na odesílající odkaz a je strukturována takto:

* Vlastnost ID korelace, která obsahuje ID požadavku předané ve zprávě požadavku metody.

* Vlastnost aplikace `IoThub-status`s názvem , která obsahuje stav metody zadané uživatelem.

* Tělo zprávy AMQP obsahující odpověď metody jako JSON.

## <a name="additional-reference-material"></a>Doplňkový referenční materiál

Mezi další referenční témata v průvodci vývojáři služby IoT Hub patří:

* [Koncové body ioT hubu](iot-hub-devguide-endpoints.md) popisují různé koncové body, které každý ioT hub zveřejňuje pro operace za běhu a správy.

* [Omezení a kvóty](iot-hub-devguide-quotas-throttling.md) popisuje kvóty, které platí a omezení chování očekávat při použití ioT hub.

* [Sady SDK zařízení a služeb Azure IoT](iot-hub-devguide-sdks.md) uvádí různé sady SDK, které můžete použít při vývoji aplikací pro zařízení i služeb, které interagují s službou IoT Hub.

* [Dotazovací jazyk služby IoT Hub pro dvojčata zařízení, úlohy a směrování zpráv](iot-hub-devguide-query-language.md) popisuje dotazovací jazyk služby IoT Hub, který můžete použít k načtení informací z ioT hubu o dvojčatech a úlohách vašeho zařízení.

* [Podpora Služby IoT Hub MQTT](iot-hub-mqtt-support.md) poskytuje další informace o podpoře služby IoT Hub pro protokol MQTT.

## <a name="next-steps"></a>Další kroky

Nyní jste se naučili používat přímé metody, možná vás bude zajímat následující článek s průvodcem pro vývojáře služby IoT Hub:

* [Plánování úloh na několika zařízeních](iot-hub-devguide-jobs.md)

Pokud byste chtěli vyzkoušet některé koncepty popsané v tomto článku, může vás zajímat následující kurz ioT hubu:

* [Použití přímých metod](quickstart-control-device-node.md)
* [Správa zařízení s využitím sady Azure IoT Tools pro VS Code](iot-hub-device-management-iot-toolkit.md)
