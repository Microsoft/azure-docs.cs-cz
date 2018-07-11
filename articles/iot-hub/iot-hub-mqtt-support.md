---
title: Podpora MQTT centra IoT Azure pochopit | Dokumentace Microsoftu
description: Příručka pro vývojáře – podpora pro zařízení, připojení na koncový bod služby IoT Hub přístupem k zařízení pomocí protokolu MQTT. Obsahuje informace o předdefinovaných podpora MQTT v SDK pro zařízení Azure IoT.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: elioda
ms.openlocfilehash: b553da54cd8ce63638fc52dd078bb517a1f9e713
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "34634654"
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>Komunikovat se službou IoT hub pomocí protokolu MQTT

IoT Hub umožňuje zařízením komunikovat s koncovými body zařízení služby IoT Hub pomocí:

* [Protokoly MQTT v3.1.1] [ lnk-mqtt-org] na portu 8883
* Protokoly MQTT v3.1.1 přes WebSocket na portu 443.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Veškerá komunikace zařízení pomocí služby IoT Hub musí být zabezpečená pomocí protokolu TLS/SSL. IoT Hub, proto nepodporuje nezabezpečené připojení přes port 1883.

## <a name="connecting-to-iot-hub"></a>Připojení ke službě IoT Hub

Zařízení můžete použít protokol MQTT pro připojení k centru IoT pomocí:

* Knihovny v [sad SDK Azure IoT][lnk-device-sdks].
* Nebo přímo protokolu MQTT.

## <a name="using-the-device-sdks"></a>Pomocí sady SDK pro zařízení

[Sady SDK pro zařízení] [ lnk-device-sdks] , které podporují protokol MQTT jsou k dispozici pro Javu, Node.js, C, C# a Python. Sady SDK pro zařízení používat standardní připojovací řetězec služby IoT Hub pro navázání připojení do služby IoT hub. Pokud chcete používat protokol MQTT, musí být parametr protokol klienta nastaven na **MQTT**. Ve výchozím nastavení, zařízení sady SDK pro připojení do služby IoT Hub s **CleanSession** příznak nastaven na **0** a používat **QoS 1** k výměně zpráv prostřednictvím služby IoT hub.

Když se zařízení připojí do služby IoT hub, sady SDK pro zařízení poskytují metody, které umožní zařízení umožňuje výměnu zpráv pomocí služby IoT hub.

Následující tabulka obsahuje odkazy na ukázky kódu pro každý podporovaný jazyk a určuje parametr se použije k navázání připojení ke službě IoT Hub pomocí protokolu MQTT.

| Jazyk | Parametr Protocol |
| --- | --- |
| [Node.js][lnk-sample-node] |azure-iot-device-mqtt |
| [Java][lnk-sample-java] |IotHubClientProtocol.MQTT |
| [C][lnk-sample-c] |MQTT_Protocol |
| [C#][lnk-sample-csharp] |TransportType.Mqtt |
| [Python][lnk-sample-python] |IoTHubTransportProvider.MQTT |

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Migrace aplikace pro zařízení z protokolu AMQP k MQTT

Pokud používáte [sady SDK pro zařízení][lnk-device-sdks], přechod z pomocí protokolu AMQP k MQTT vyžaduje změnu parametr inicializace klienta do protokolu, jak bylo uvedeno dříve.

Pokud tak učiníte, ujistěte se, že zkontrolujte následující položky:

* AMQP vrátí chyby pro mnoho jiných podmínek, zatímco MQTT ukončí připojení. V důsledku výjimky logika zpracování může vyžadovat některé změny.
* MQTT nepodporuje *odmítnout* operace při přijímání [zprávy typu cloud zařízení][lnk-messaging]. Pokud back endové aplikace potřebuje k přijetí odpovědi z aplikace pro zařízení, zvažte použití [přímé metody][lnk-methods].

## <a name="using-the-mqtt-protocol-directly"></a>Přímo pomocí protokolu MQTT

Pokud zařízení nemůže používat sady SDK pro zařízení, se může pořád připojit ke koncovým bodům veřejné zařízení pomocí protokolu MQTT na portu 8883. V **připojit** paketů zařízení by měl použít následující hodnoty:

* Pro **ClientId** , použijte **deviceId**.

* Pro **uživatelské jméno** použijte `{iothubhostname}/{device_id}/api-version=2016-11-14`, kde `{iothubhostname}` je úplný záznam CName služby IoT hub.

    Například, pokud je název služby IoT hub **contoso.azure-devices.net** a pokud je název vašeho zařízení **MyDevice01**, kompletní **uživatelské jméno** pole by mělo obsahovat:

    `contoso.azure-devices.net/MyDevice01/api-version=2016-11-14`

* Pro **heslo** pole, pomocí tokenu SAS. Formát tokenu SAS je stejná jako protokoly HTTPS i AMQP:

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > Pokud používáte ověřování pomocí certifikátu X.509, nejsou nutné hesla tokenu SAS. Další informace najdete v tématu [nastavit X.509 zabezpečení ve službě Azure IoT Hub][lnk-x509]

  Další informace o tom, jak generovat tokeny SAS, najdete v části zařízení [tokeny zabezpečení pomocí služby IoT Hub][lnk-sas-tokens].

  Při testování, můžete použít také [Průzkumník zařízení] [ lnk-device-explorer] nástroj rychle vytvořit token SAS, který můžete zkopírovat a vložit vlastní kód:

  1. Přejděte **správu** kartu **Device Explorer**.
  2. Klikněte na tlačítko **tokenů SAS** (pravého horního).
  3. Na **SASTokenForm**, vyberte své zařízení do služby **DeviceID** rozevírací seznam. Nastavte vaše **TTL**.
  4. Klikněte na tlačítko **generovat** k vytvoření tokenu.

     Token SAS, který je generován má následující strukturu:

     `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

     Součástí tohoto tokenu, který se použije jako **heslo** je pole, které chcete připojit pomocí protokolu MQTT:

     `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

MQTT připojit a odpojit pakety, IoT Hub vydá událost na **monitorování Operations** kanálu. Tato událost má další informace, které vám mohou pomoci při řešení problémů s připojením.

Můžete určit aplikace pro zařízení **bude** zprávy v **připojit** paketů. Používejte aplikace pro zařízení s `devices/{device_id}/messages/events/{property_bag}` nebo `devices/{device_id}/messages/events/{property_bag}` jako **bude** název tématu k definování **bude** předávat jako telemetrické zprávy zprávy. V takovém případě pokud se zavře připojení k síti, ale **ODPOJIT** nebyl dříve přijat paket ze zařízení a pak odešle služby IoT Hub **bude** zprávy zadaný v **připojit** paketů do kanálu telemetrická data. Kanál telemetrických dat může být buď výchozí **události** koncového bodu nebo vlastní koncový bod definovaný ve službě IoT Hub směrování. Zpráva obsahuje **iothub-MessageType** vlastnost s hodnotou **bude** přiřazené.

### <a name="tlsssl-configuration"></a>Konfigurace TLS/SSL

Použít přímo, protokol MQTT vašeho klienta *musí* připojují přes TLS/SSL. Tento krok můžete přeskočit neúspěšných pokusů s chybami připojení.

Aby bylo možné navázat připojení TLS, budete muset stáhnout a odkazovat na DigiCert Baltimore kořenový certifikát. Tento certifikát je ten, který využívá Azure k zabezpečení připojení. Tento certifikát můžete najít [Azure-iot-sdk-c] [ lnk-sdk-c-certs] úložiště. Další informace o těchto certifikátech najdete na [Digicertu webu][lnk-digicert-root-certs].

Příklad toho, jak implementovat to pomocí verze Pythonu [Paho MQTT knihovny] [ lnk-paho] nadací Eclipse může vypadat následovně.

Nejdřív nainstalujte knihovnu Paho z prostředí příkazového řádku:

```cmd/sh
pip install paho-mqtt
```

Klient pak, implementujte ve skriptu v jazyce Python. Nahraďte zástupné symboly následujícím způsobem:

* `<local path to digicert.cer>` představuje cestu do místního souboru, který obsahuje DigiCert Baltimore kořenový certifikát. Tento soubor můžete vytvořit zkopírováním informace o certifikátu z [certs.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) v sadě SDK Azure IoT pro C. zahrnout řádky `-----BEGIN CERTIFICATE-----` a `-----END CERTIFICATE-----`, odeberte `"` značky na začátek a konec každého řádku, a Odeberte `\r\n` znaky na konec každého řádku.
* `<device id from device registry>` je ID zařízení, které jste přidali do služby IoT hub.
* `<generated SAS token>` je token SAS pro zařízení vytvořené, jak je popsáno výše v tomto článku.
* `<iot hub name>` název služby IoT hub.

```python
from paho.mqtt import client as mqtt
import ssl

path_to_root_cert = "<local path to digicert.cer>"
device_id = "<device id from device registry>"
sas_token = "<generated SAS token>"
iot_hub_name = "<iot hub name>"

def on_connect(client, userdata, flags, rc):
  print ("Device connected with result code: " + str(rc))
def on_disconnect(client, userdata, rc):
  print ("Device disconnected with result code: " + str(rc))
def on_publish(client, userdata, mid):
  print ("Device sent message")

client = mqtt.Client(client_id=device_id, protocol=mqtt.MQTTv311)

client.on_connect = on_connect
client.on_disconnect = on_disconnect
client.on_publish = on_publish

client.username_pw_set(username=iot_hub_name+".azure-devices.net/" + device_id, password=sas_token)

client.tls_set(ca_certs=path_to_root_cert, certfile=None, keyfile=None, cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1, ciphers=None)
client.tls_insecure_set(False)

client.connect(iot_hub_name+".azure-devices.net", port=8883)

client.publish("devices/" + device_id + "/messages/events/", "{id=123}", qos=1)
client.loop_forever()
```

### <a name="sending-device-to-cloud-messages"></a>Odesílání zpráv typu zařízení cloud

Po provedení úspěšného připojení zařízení odesílat zprávy do služby IoT Hub pomocí `devices/{device_id}/messages/events/` nebo `devices/{device_id}/messages/events/{property_bag}` jako **název tématu**. `{property_bag}` Element umožňuje zařízení odesílat zprávy s použitím dalších vlastností ve formátu kódování url. Příklad:

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> To `{property_bag}` prvek používá stejný kódování pro řetězce dotazu v protokolu HTTPS.

Následuje seznam chování specifický pro implementaci služby IoT Hub:

* IoT Hub nepodporuje QoS 2 zprávy. Pokud zařízení aplikace publikuje zprávu o **QoS 2**, IoT Hub ukončí připojení k síti.
* Doba uchování zpráv služby IoT Hub není zachována. Pokud zařízení pošle zprávu s **zachovat** příznak nastaven na hodnotu 1, přidá službu IoT Hub **x-opt – zachovat** vlastnost aplikace ke zprávě. V tomto případě namísto uchování doba uchování zpráv služby IoT Hub předává je do back-endu aplikace.
* IoT Hub podporuje pouze jedno aktivní připojení MQTT jedno zařízení. Všechny nové připojení MQTT jménem stejným ID zařízení způsobí, že služby IoT Hub, vyřaďte existující připojení.

Další informace najdete v tématu [zasílání zpráv – Příručka vývojáře][lnk-messaging].

### <a name="receiving-cloud-to-device-messages"></a>Příjem zpráv typu cloud zařízení

Pro příjem zpráv ze služby IoT Hub, zařízení by měl přihlášení k odběru `devices/{device_id}/messages/devicebound/#` jako **tématu filtr**. Víceúrovňové zástupné `#` ve filtru tématu se používá jenom k umožňuje zařízení přijímat další vlastnosti v název tématu. IoT Hub neumožňuje použití `#` nebo `?` zástupných znaků pro filtrování dílčí témata. Protože služby IoT Hub není zprostředkovatel zasílání zpráv pro obecné účely pub-sub, podporuje pouze názvy témat zdokumentované a filtry témat.

Zařízení přijme všechny zprávy ze služby IoT Hub, dokud ho se úspěšně připojila ke svůj koncový bod specifický pro zařízení, reprezentovaný `devices/{device_id}/messages/devicebound/#` tématu filtru. Po vytvoření předplatného se zařízení přijímá zprávy typu cloud zařízení, které byly odeslány do ní od chvíle, kdy předplatné. Pokud se zařízení připojuje pomocí **CleanSession** příznak nastaven na **0**, předplatné se uchovávají napříč různými relacemi. V takovém případě při příštím připojení zařízení s **CleanSession 0** přijme všechny nevyřízené zprávy odeslané do ní odpojené. Pokud zařízení používá **CleanSession** příznak nastaven na **1** však neobdrží všechny zprávy ze služby IoT Hub až si přihlásila, koncový bod jeho zařízení.

IoT Hub doručí zprávy s **název tématu** `devices/{device_id}/messages/devicebound/`, nebo `devices/{device_id}/messages/devicebound/{property_bag}` po vlastnosti zprávy. `{property_bag}` obsahuje dvojice klíč/hodnota adresy url kóduje zprávy vlastností. Pouze vlastnosti aplikace a vlastnosti nastavit uživatele systému (například **messageId** nebo **correlationId**) jsou zahrnuty v kontejneru objektů a. Názvy vlastností systému mají předponu **$**, vlastnosti aplikace používat původní název vlastnosti se žádná předpona.

Když aplikace pro zařízení se přihlásí k odběru tématu se **QoS 2**, uděluje maximální QoS úrovně 1 do služby IoT Hub **SUBACK** paketů. IoT Hub poté předává zprávy zařízení s využitím technologie QoS 1.

### <a name="retrieving-a-device-twins-properties"></a>Načítají se vlastnosti dvojčete zařízení

Nejprve, zařízení se přihlásí k odběru `$iothub/twin/res/#`, pro příjem odpovědí operaci. Potom pošle prázdnou zprávu do tématu `$iothub/twin/GET/?$rid={request id}`, mají údaj vyplněný hodnotou pro **ID požadavku**. Služba potom odešle zprávu odpovědi, který obsahuje data dvojčete zařízení k tématu `$iothub/twin/res/{status}/?$rid={request id}`, pomocí stejných **ID požadavku** jako požadavek.

ID požadavku může být libovolná platná hodnota pro hodnoty vlastnosti zprávy, jak je uvedeno [– Příručka vývojáře pro zasílání zpráv služby IoT Hub][lnk-messaging], a ověřuje stav jako celé číslo.

Text odpovědi obsahuje části vlastností v dvojčeti zařízení. Následující fragment kódu ukazuje text položky registru identit omezené "properties" členu, například:

```json
{
    "properties": {
        "desired": {
            "telemetrySendFrequency": "5m",
            "$version": 12
        },
        "reported": {
            "telemetrySendFrequency": "5m",
            "batteryLevel": 55,
            "$version": 123
        }
    }
}
```

Je to možné stavové kódy jsou:

|Status | Popis |
| ----- | ----------- |
| 200 | Úspěch |
| 429 | Příliš mnoho požadavků (omezený), jak je uvedeno [omezení šířky pásma služby IoT Hub][lnk-quotas] |
| 5** | Chyby serveru |

Další informace najdete v tématu [– Příručka vývojáře dvojčata zařízení][lnk-devguide-twin].

### <a name="update-device-twins-reported-properties"></a>Aktualizovat ohlášené vlastnosti dvojčete zařízení

Následující text popisuje, jak zařízení aktualizuje ohlášené vlastnosti v dvojčeti zařízení ve službě IoT Hub:

1. Zařízení musí nejdřív přihlásit k odběru `$iothub/twin/res/#` tématu pro příjem odpovědí operaci ze služby IoT Hub.

1. Zařízení odesílá zprávu, která obsahuje aktualizace dvojčete zařízení `$iothub/twin/PATCH/properties/reported/?$rid={request id}` tématu. Tato zpráva obsahuje **ID požadavku** hodnotu.

1. Služba potom odešle zprávu odpovědi, která obsahuje novou hodnotu značky ETag pro kolekci ohlášené vlastnosti k tématu `$iothub/twin/res/{status}/?$rid={request id}`. Tuto zprávu s odpovědí používá stejná **ID požadavku** jako požadavek.

Tělo zprávy požadavku obsahuje dokument JSON, který obsahuje nové hodnoty ohlášených vlastností. Každý člen v dokumentu JSON aktualizací nebo přidejte odpovídající člen v dvojčeti zařízení dokumentu. Nastavte na člen `null`, odstraní člena z nadřazeného objektu. Příklad:

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

Je to možné stavové kódy jsou:

|Status | Popis |
| ----- | ----------- |
| 200 | Úspěch |
| 400 | Chybný požadavek. Nesprávný formát JSON |
| 429 | Příliš mnoho požadavků (omezený), jak je uvedeno [omezení šířky pásma služby IoT Hub][lnk-quotas] |
| 5** | Chyby serveru |

Další informace najdete v tématu [– Příručka vývojáře dvojčata zařízení][lnk-devguide-twin].

### <a name="receiving-desired-properties-update-notifications"></a>Přijímání oznámení o aktualizacích požadované vlastnosti

Pokud je zařízení připojené, IoT Hub odesílá oznámení do tématu `$iothub/twin/PATCH/properties/desired/?$version={new version}`, které obsahují obsah aktualizace prováděné back-endu řešení. Příklad:

```json
{
    "telemetrySendFrequency": "5m",
    "route": null
}
```

Jako u aktualizace vlastností `null` hodnoty znamená, že člen objektu JSON se odstraňuje.

> [!IMPORTANT]
> IoT Hub generuje oznámení o změnách pouze v případě, že zařízení je připojených. Ujistěte se, že k implementaci [zařízení opětovné připojení toku] [ lnk-devguide-twin-reconnection] zajistit synchronizaci mezi službou IoT Hub a aplikace pro zařízení s požadované vlastnosti.

Další informace najdete v tématu [– Příručka vývojáře dvojčata zařízení][lnk-devguide-twin].

### <a name="respond-to-a-direct-method"></a>Reagovat na přímé metody

Nejprve, má zařízení pro přihlášení k odběru `$iothub/methods/POST/#`. IoT Hub odesílá požadavky metody do tématu `$iothub/methods/POST/{method name}/?$rid={request id}`s platným formátem JSON nebo prázdným textem zprávy.

Reagovat, zařízení odešle zprávu o platný kód JSON nebo prázdným textem zprávy do tématu `$iothub/methods/res/{status}/?$rid={request id}`. V této zprávě **ID požadavku** musí odpovídat tomu ve zprávě požadavku a **stav** musí být celé číslo.

Další informace najdete v tématu [přímé Příručka pro vývojáře metoda][lnk-methods].

### <a name="additional-considerations"></a>Další aspekty

Jako konečné potřeba, pokud je třeba přizpůsobit chování protokolu MQTT na straně cloudu byste měli zkontrolovat [brány protokolu Azure IoT][lnk-azure-protocol-gateway]. Tento software umožňuje nasadit vlastní protokol vysoce výkonné brány přímo službou IoT Hub tohoto rozhraní. Brána protokolu Azure IoT umožňuje přizpůsobit protokol zařízení tak, aby vyhovovaly brownfield MQTT nasazení nebo jiné vlastní protokoly. Tento přístup však vyžaduje spouštět a provozovat vlastní protokol brány.

## <a name="next-steps"></a>Další postup

Další informace o protokolu MQTT, najdete v článku [MQTT dokumentaci][lnk-mqtt-docs].

Další informace o plánování nasazení služby IoT Hub, najdete v tématech:

* [Azure IoT katalog zařízení Certified for][lnk-devices]
* [Podpora dalších protokolů][lnk-protocols]
* [Porovnání s Event Hubs][lnk-compare]
* [Škálování, vysokou dostupnost a zotavení po Havárii][lnk-scaling]

Podrobněji prozkoumat možnosti služby IoT Hub, najdete v tématech:

* [Příručka vývojáře pro IoT Hub][lnk-devguide]
* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge][lnk-iotedge]

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-mqtt-org]: http://mqtt.org/
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples
[lnk-sample-python]: https://github.com/Azure/azure-iot-sdk-python/tree/master/device/samples
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer
[lnk-sas-tokens]: iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-devices]: https://catalog.azureiotsuite.com/
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-x509]: iot-hub-security-x509-get-started.md

[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-messaging]: iot-hub-devguide-messaging.md

[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-twin-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow
[lnk-devguide-twin]: iot-hub-devguide-device-twins.md
[lnk-sdk-c-certs]: https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c
[lnk-digicert-root-certs]: https://www.digicert.com/digicert-root-certificates.htm
[lnk-paho]: https://pypi.python.org/pypi/paho-mqtt
