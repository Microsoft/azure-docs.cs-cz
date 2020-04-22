---
title: Principy podpory Azure IoT Hub MQTT | Dokumenty společnosti Microsoft
description: Průvodce pro vývojáře – podpora zařízení, která se připojují ke koncovému bodu orientovanému na zařízení služby IoT Hub pomocí protokolu MQTT. Obsahuje informace o integrované podpoře MQTT v sadách SDK zařízení Azure IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: robinsh
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 86fc5d4845e746604c1ba69f661d1b9ea9d8dca4
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732318"
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>Komunikace s centrem IoT pomocí protokolu MQTT

IoT Hub umožňuje zařízením komunikovat s koncovými body zařízení služby IoT Hub pomocí:

* [MQTT v3.1.1](https://mqtt.org/) na portu 8883
* MQTT v3.1.1 přes WebSocket na portu 443.

IoT Hub není plnohodnotný zprostředkovatel protokolu MQTT a nepodporuje všechna chování uvedená ve standardu protokolu MQTT v3.1.1. Tento článek popisuje, jak zařízení můžete použít podporované chování MQTT ke komunikaci s IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Veškerá komunikace zařízení s ioT hubem musí být zabezpečena pomocí TLS/SSL. Proto IoT Hub nepodporuje nezabezpečené připojení přes port 1883.

## <a name="connecting-to-iot-hub"></a>Připojení k centru IoT Hub

Zařízení může použít protokol MQTT pro připojení k centru IoT pomocí některé z následujících možností.

* Knihovny v sadách [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks).
* Protokol MQTT přímo.

Port MQTT (8883) je blokován v mnoha firemních a vzdělávacích síťových prostředích. Pokud se vám nepodaří otevřít port 8883 v bráně firewall, doporučujeme použít MQTT přes webové sokety. MQTT přes webové sokety komunikuje přes port 443, který je téměř vždy otevřený v síťových prostředích. Informace o tom, jak zadat protokoly MQTT a MQTT přes webové sokety při použití sad Azure IoT SDK, najdete [v tématu Použití sad SDK zařízení](#using-the-device-sdks).

## <a name="using-the-device-sdks"></a>Používání sad SDK zařízení

[Sady SDK zařízení,](https://github.com/Azure/azure-iot-sdks) které podporují protokol MQTT, jsou k dispozici pro jazyk Java, Node.js, C, C# a Python. Sady SDK zařízení používají standardní připojovací řetězec ioT hub unavit připojení k centru IoT Hub. Chcete-li použít protokol MQTT, musí být parametr protokolu klienta nastaven na **hodnotu MQTT**. V parametru klientského protokolu můžete také zadat MQTT přes webové sokety. Ve výchozím nastavení se sady SDK zařízení připojují k centru IoT Hub s příznakem **CleanSession** nastaveným na **hodnotu 0** a používají **QoS 1** pro výměnu zpráv s centrem IoT.

Když je zařízení připojené k centru IoT, sady SDK zařízení poskytují metody, které umožňují zařízení vyměňovat si zprávy s centrem IoT.

Následující tabulka obsahuje odkazy na ukázky kódu pro každý podporovaný jazyk a určuje parametr, který se má použít k navázání připojení k centru IoT Hub pomocí protokolu MQTT nebo MQTT přes webové sokety.

| Jazyk | Parametr protokolu MQTT | Parametr protokolu MQTT přes webové sokety
| --- | --- | --- |
| [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js) | azure-iot-device-mqtt. Mqtt | azure-iot-device-mqtt. MqttWs |
| [Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java) |[Protokol IotHubClientProtocol](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.iothubclientprotocol?view=azure-java-stable). MQTT | Protokol IotHubClientProtocol.MQTT_WS |
| [C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm) | [MQTT_Protocol](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-h/mqtt-protocol) | [MQTT_WebSocket_Protocol](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-websockets-h/mqtt-websocket-protocol) |
| [C #](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples) | [TransportType](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.transporttype?view=azure-dotnet). Mqtt | TransportType.Mqtt přejde zpět na MQTT přes webové sokety, pokud MQTT selže. Chcete-li zadat mqtt pouze přes webové sokety, použijte TransportType.Mqtt_WebSocket_Only |
| [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) | Standardně podporuje MQTT | Přidat `websockets=True` do volání k vytvoření klienta |

Následující fragment ukazuje, jak určit protokol MQTT přes webové sokety při použití sady Azure IoT Node.js SDK:

```javascript
var Client = require('azure-iot-device').Client;
var Protocol = require('azure-iot-device-mqtt').MqttWs;
var client = Client.fromConnectionString(deviceConnectionString, Protocol);
```

Následující fragment ukazuje, jak určit protokol MQTT přes webové sokety při použití sady Azure IoT Python SDK:

```python
from azure.iot.device.aio import IoTHubDeviceClient
device_client = IoTHubDeviceClient.create_from_connection_string(deviceConnectionString, websockets=True)
```

### <a name="default-keep-alive-timeout"></a>Výchozí časový limit udržování

Chcete-li zajistit, aby připojení klienta/služby IoT Hub zůstalo naživu, služba i klient si pravidelně odesílají *příkaz* ping udržování. Klient pomocí sady IoT SDK odešle keep-alive v intervalu definovaném v této tabulce níže:

|Jazyk  |Výchozí interval udržování  |Konfigurovatelné  |
|---------|---------|---------|
|Node.js     |   180 sekund      |     Ne    |
|Java     |    230 sekund     |     Ne    |
|C     | 240 sekund |  [Ano](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md#mqtt-transport)   |
|C#     | 300 sekund |  [Ano](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/Transport/Mqtt/MqttTransportSettings.cs#L89)   |
|Krajně (V2)   | 60 sekund |  Ne   |

Po [Specifikam MQTT](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718081)je interval udržování příkazu služby IoT Hub 1,5násobek hodnoty udržování klienta. Služba IoT Hub však omezuje maximální časový limit na straně serveru na 29,45 minut (1767 sekund), protože všechny služby Azure jsou vázány na časový limit nečinnosti tcp služby Azure pro vyrovnávání zatížení, což je 29,45 minuty. 

Například zařízení používající sadu Java SDK odešle příkaz ping keep-alive a pak ztratí připojení k síti. O 230 sekund později zařízení zmešká ping, protože je offline. Služba IoT Hub však neukončí připojení okamžitě `(230 * 1.5) - 230 = 115` – čeká další sekundy před odpojením zařízení s chybou [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md). 

Maximální hodnota udržování klienta, kterou `1767 / 1.5 = 1177` můžete nastavit, je sekund. Jakýkoli provoz obnoví udržování. Například úspěšná aktualizace tokenu SAS obnoví udržování.

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Migrace aplikace zařízení z AMQP na MQTT

Pokud používáte [sady SDK zařízení](https://github.com/Azure/azure-iot-sdks), přepnutí z používání amqp na MQTT vyžaduje změnu parametru protokolu v inicializaci klienta, jak je uvedeno dříve.

Při tom zkontrolujte následující položky:

* AMQP vrátí chyby pro mnoho podmínek, zatímco MQTT ukončí připojení. V důsledku toho může logika zpracování výjimek vyžadovat některé změny.

* MQTT nepodporuje *vyřazovací* operace při příjmu [zpráv z cloudu na zařízení](iot-hub-devguide-messaging.md). Pokud vaše back-endová aplikace potřebuje obdržet odpověď z aplikace pro zařízení, zvažte použití [přímých metod](iot-hub-devguide-direct-methods.md).

* AMQP není v sadě Python SDK podporován

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Přímé použití protokolu MQTT (jako zařízení)

Pokud zařízení nemůže používat sady SDK zařízení, může se stále připojit ke koncovým bodům veřejného zařízení pomocí protokolu MQTT na portu 8883. V paketu **CONNECT** by zařízení mělo používat následující hodnoty:

* Pro pole **ClientId** použijte **deviceId**.

* Pro pole **Uživatelské** jméno `{iothubhostname}/{device_id}/?api-version=2018-06-30`použijte `{iothubhostname}` , kde je celý CName centra IoT.

    Pokud je například název vašeho centra IoT **hub contoso.azure-devices.net** a název vašeho zařízení je **MyDevice01**, mělo by úplné pole **Uživatelské jméno** obsahovat:

    `contoso.azure-devices.net/MyDevice01/?api-version=2018-06-30`

* Pro pole **Heslo** použijte token SAS. Formát tokenu SAS je stejný jako pro protokoly HTTPS i AMQP:

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > Pokud používáte ověřování certifikátu X.509, hesla tokenů SAS nejsou vyžadována. Další informace najdete [v tématu Nastavení zabezpečení X.509 ve službě Azure IoT Hub](iot-hub-security-x509-get-started.md) a postupujte [podle pokynů](#tlsssl-configuration)kódu níže .

  Další informace o tom, jak generovat tokeny SAS, naleznete v části zařízení [pomocí tokenů zabezpečení služby IoT Hub](iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app).

  Při testování můžete také použít nástroje [Azure IoT pro vizuální studio](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) pro různé platformy nebo příkaz rozšíření příkazu CLI [az iot hub generate-sas-token,](/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-generate-sas-token) abyste rychle vygenerovali token SAS, který můžete zkopírovat a vložit do vlastního kódu:

### <a name="for-azure-iot-tools"></a>Pro nástroje Azure IoT

1. Rozbalte kartu **AZURE IOT HUB DEVICES** v levém dolním rohu kódu Visual Studia.
  
2. Klikněte pravým tlačítkem myši na zařízení a vyberte **generovat token SAS pro zařízení**.
  
3. Nastavte **čas vypršení platnosti** a stiskněte klávesu Enter.
  
4. Token SAS je vytvořen a zkopírován do schránky.

   Vygenerovaný token SAS má následující strukturu:

   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

   Část tohoto tokenu, která se má použít jako pole **Heslo** pro připojení pomocí nástroje MQTT, je:

   `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

Pro mqtt připojení a odpojení paketů, IoT Hub vydá událost na kanálu **sledování operací.** Tato událost má další informace, které vám mohou pomoci při řešení problémů s připojením.

Aplikace zařízení může zadat zprávu **Vůle** v paketu **CONNECT.** Aplikace zařízení by `devices/{device_id}/messages/events/` `devices/{device_id}/messages/events/{property_bag}` měla použít nebo jako název tématu **Bude** definovat **Will** zprávy, které mají být předány jako telemetrická zpráva. V tomto případě pokud je síťové připojení uzavřeno, ale paket **DISCONNECT** nebyl dříve přijat ze zařízení, odešle služba IoT Hub zprávu **Will** zadanou v paketu **CONNECT** telemetrickému kanálu. Kanál telemetrie může být buď výchozí **události** koncový bod nebo vlastní koncový bod definovaný směrování služby IoT Hub. Zpráva má **vlastnost iothub-MessageType** s hodnotou **Will,** která je k ní přiřazena.

### <a name="an-example-of-c-code-using-mqtt-without-azure-iot-c-sdk"></a>Příklad kódu C pomocí MQTT bez sady Azure IoT C SDK
V tomto úložišti najdete několik ukázkových projektů C/C++, které ukazují, jak odesílat telemetrické zprávy, přijímat události pomocí [služby](https://github.com/Azure-Samples/IoTMQTTSample)IoT hub bez použití sady Azure IoT C SDK. 

Tyto ukázky používají knihovnu Eclipse Mosquitto k odeslání zprávy zprostředkovateli MQTT implementovanému v centru IoT.

Toto úložiště obsahuje:

**Ve Windows:**

* TelemetryMQTTWin32: obsahuje kód pro odeslání telemetrické zprávy do centra Azure IoT hub, postavený a spuštěný na počítači s Windows.

* SubscribeMQTTWin32: obsahuje kód k přihlášení k odběru událostí daného centra IoT hub na počítači se systémem Windows.

* DeviceTwinMQTTWin32: obsahuje kód pro dotazování a přihlášení k odběru událostí dvojčete zařízení zařízení v centru Azure IoT na počítači s Windows.

* PnPMQTTWin32: obsahuje kód pro odeslání telemetrické zprávy pomocí funkce zařízení IoT Plug plug & Play preview do centra Azure IoT, které je sestavené a spouštěné na počítači s Windows. Více o IoT Plug & Play [zde](https://docs.microsoft.com/azure/iot-pnp/overview-iot-plug-and-play)

**Pro Linux:**

* MQTTLinux: obsahuje kód a sestavit skript pro spuštění na Linuxu (WSL, Ubuntu a Raspbian byly testovány tak daleko).

* LinuxConsoleVS2019: obsahuje stejný kód, ale v projektu VS2019 zaměřeném na WSL (podsystém Windows Linux). Tento projekt umožňuje ladit kód běžící na Linuxu krok za krokem z Visual Studia.

**Pro mosquitto_pub:**

Tato složka obsahuje dva ukázkové příkazy používané s nástrojem mosquitto_pub poskytovaným Mosquitto.org.

* Mosquitto_sendmessage: chcete-li odeslat jednoduchou textovou zprávu do centra Azure IoT hub umíte jako zařízení.

* Mosquitto_subscribe: chcete-li zobrazit události, ke kterým dochází v centru Azure IoT.

## <a name="using-the-mqtt-protocol-directly-as-a-module"></a>Použití protokolu MQTT přímo (jako modul)

Připojení k ioT hubu přes MQTT pomocí identity modulu je podobné zařízení (popsané [výše),](#using-the-mqtt-protocol-directly-as-a-device)ale musíte použít následující:

* Nastavte ID klienta na `{device_id}/{module_id}`.

* Pokud se ověřujete pomocí uživatelského `<hubname>.azure-devices.net/{device_id}/{module_id}/?api-version=2018-06-30` jména a hesla, nastavte uživatelské jméno a použijte token SAS přidružený k identitě modulu jako heslo.

* Slouží `devices/{device_id}/modules/{module_id}/messages/events/` jako téma pro publikování telemetrie.

* Použít `devices/{device_id}/modules/{module_id}/messages/events/` jako téma VŮLE.

* Twin GET a PATCH témata jsou identické pro moduly a zařízení.

* Téma stavu dvojčete je identické pro moduly a zařízení.

## <a name="tlsssl-configuration"></a>Konfigurace TLS/SSL

Chcete-li protokol MQTT používat přímo, *musí* se klient připojit přes protokol TLS/SSL. Pokusy o přeskočení tohoto kroku se nezdaří s chybami připojení.

Chcete-li navázat připojení TLS, možná budete muset stáhnout a odkazovat na kořenový certifikát DigiCert Baltimore. Tento certifikát je ten, který Azure používá k zabezpečení připojení. Tento certifikát najdete v úložišti [Azure-iot-sdk-c.](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) Více informací o těchto certifikátech naleznete na [webových stránkách společnosti Digicert](https://www.digicert.com/digicert-root-certificates.htm).

Příklad, jak to implementovat pomocí verze Pythonu [knihovny Paho MQTT od](https://pypi.python.org/pypi/paho-mqtt) Eclipse Foundation může vypadat takto.

Nejprve nainstalujte knihovnu Paho z prostředí příkazového řádku:

```cmd/sh
pip install paho-mqtt
```

Potom implementujte klienta ve skriptu Pythonu. Nahraďte zástupné symboly takto:

* `<local path to digicert.cer>`je cesta k místnímu souboru, který obsahuje kořenový certifikát DigiCert Baltimore. Tento soubor můžete vytvořit zkopírováním informací o certifikátu z [certs.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) v sadě `-----BEGIN CERTIFICATE-----` Azure `-----END CERTIFICATE-----`IoT SDK pro C. Zahrnout řádky a , odebrat `"` značky na začátku a na konci každého řádku a odebrat `\r\n` znaky na konci každého řádku.

* `<device id from device registry>`je ID zařízení, které jste přidali do centra IoT hub.

* `<generated SAS token>`je token SAS pro zařízení vytvořené, jak je popsáno výše v tomto článku.

* `<iot hub name>`název vašeho centra IoT.

```python
from paho.mqtt import client as mqtt
import ssl

path_to_root_cert = "<local path to digicert.cer file>"
device_id = "<device id from device registry>"
sas_token = "<generated SAS token>"
iot_hub_name = "<iot hub name>"


def on_connect(client, userdata, flags, rc):
    print("Device connected with result code: " + str(rc))


def on_disconnect(client, userdata, rc):
    print("Device disconnected with result code: " + str(rc))


def on_publish(client, userdata, mid):
    print("Device sent message")


client = mqtt.Client(client_id=device_id, protocol=mqtt.MQTTv311)

client.on_connect = on_connect
client.on_disconnect = on_disconnect
client.on_publish = on_publish

client.username_pw_set(username=iot_hub_name+".azure-devices.net/" +
                       device_id + "/?api-version=2018-06-30", password=sas_token)

client.tls_set(ca_certs=path_to_root_cert, certfile=None, keyfile=None,
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1_2, ciphers=None)
client.tls_insecure_set(False)

client.connect(iot_hub_name+".azure-devices.net", port=8883)

client.publish("devices/" + device_id + "/messages/events/", "{id=123}", qos=1)
client.loop_forever()
```

Chcete-li se ověřit pomocí certifikátu zařízení, aktualizujte výše uvedený fragment kódu následujícími změnami (viz [Jak získat certifikát certifikační autority X.509](./iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate) o tom, jak připravit na ověřování založené na certifikátu):

```python
# Create the client as before
# ...

# Set the username but not the password on your client
client.username_pw_set(username=iot_hub_name+".azure-devices.net/" +
                       device_id + "/?api-version=2018-06-30", password=None)

# Set the certificate and key paths on your client
cert_file = "<local path to your certificate file>"
key_file = "<local path to your device key file>"
client.tls_set(ca_certs=path_to_root_cert, certfile=cert_file, keyfile=key_file,
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1_2, ciphers=None)

# Connect as before
client.connect(iot_hub_name+".azure-devices.net", port=8883)
```

## <a name="sending-device-to-cloud-messages"></a>Odesílání zpráv mezi zařízeními a cloudy

Po úspěšném připojení může zařízení odesílat zprávy do `devices/{device_id}/messages/events/` `devices/{device_id}/messages/events/{property_bag}` služby IoT Hub pomocí nebo jako **název tématu**. Prvek `{property_bag}` umožňuje zařízení odesílat zprávy s dalšími vlastnostmi ve formátu kódovaném adresou URL. Příklad:

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> Tento `{property_bag}` prvek používá stejné kódování jako řetězce dotazu v protokolu HTTPS.

Následuje seznam chování specifických pro implementaci centra IoT Hub:

* Služba IoT Hub nepodporuje zprávy QoS 2. Pokud aplikace zařízení publikuje zprávu pomocí **QoS 2**, Služba IoT Hub ukončí síťové připojení.

* IoT Hub nezachová Zachovat zprávy. Pokud zařízení odešle zprávu s příznakem **RETAIN** nastaveným na hodnotu 1, služba IoT Hub přidá ke zprávě vlastnost aplikace **x-opt-retain.** V tomto případě namísto zachování zachycovací zprávy IoT Hub předá do back-endové aplikace.

* IoT Hub podporuje pouze jedno aktivní připojení MQTT na jedno zařízení. Jakékoli nové připojení MQTT jménem stejného ID zařízení způsobí, že služba IoT Hub vypustí stávající připojení.

Další informace naleznete v [příručce pro vývojáře zpráv](iot-hub-devguide-messaging.md).

## <a name="receiving-cloud-to-device-messages"></a>Příjem zpráv z cloudu na zařízení

Chcete-li přijímat zprávy z služby IoT Hub, zařízení by se mělo přihlásit k odběru pomocí `devices/{device_id}/messages/devicebound/#` filtru **tématu**. Víceúrovňový zástupný `#` znak ve filtru tématu se používá pouze k tomu, aby zařízení mohlo přijímat další vlastnosti v názvu tématu. IoT Hub neumožňuje použití `#` nebo `?` zástupné znaky pro filtrování dílčích témat. Vzhledem k tomu, že IoT Hub není zprostředkovatel zasílání zpráv pro obecné účely pub-sub, podporuje pouze zdokumentované názvy témat a filtry témat.

Zařízení nepřijímá žádné zprávy z ioT hubu, dokud se úspěšně přihlásit k odběru `devices/{device_id}/messages/devicebound/#` jeho koncový bod specifický pro zařízení, reprezentované filtru tématu. Po vytvoření předplatného zařízení obdrží zprávy z cloudu na zařízení, které mu byly odeslány po době předplatného. Pokud se zařízení připojí s **příznakem CleanSession** nastaveným na **0**, předplatné bude trvalé v různých relacích. V takovém případě při příštím připojení zařízení s **CleanSession 0** obdrží všechny nevyřízené zprávy odeslané do něj při odpojení. Pokud zařízení používá **příznak CleanSession** nastavený na **1,** neobdrží žádné zprávy ze služby IoT Hub, dokud se nepřihlásí ke svému koncovému bodu zařízení.

Služba IoT Hub doručuje zprávy s **názvem** `devices/{device_id}/messages/devicebound/`tématu nebo `devices/{device_id}/messages/devicebound/{property_bag}` pokud existují vlastnosti zprávy. `{property_bag}`obsahuje dvojice vlastností zpráv kódované adresou URL. Do vaku vlastností jsou zahrnuty pouze vlastnosti aplikace a uživatelsky situovatelné vlastnosti systému (například **messageId** nebo **correlationId).** Názvy vlastností systému mají předponu **$**, vlastnosti aplikace používají původní název vlastnosti bez předpony.

Když se aplikace zařízení přihlásí k odběru tématu s **QoS 2**, Služba IoT Hub uděluje maximální úroveň QoS 1 v paketu **SUBACK.** Poté služba IoT Hub doručuje zprávy do zařízení pomocí QoS 1.

## <a name="retrieving-a-device-twins-properties"></a>Načítání vlastností dvojčete zařízení

Nejprve se zařízení `$iothub/twin/res/#`přihlásí k odběru , aby obdrželo odpovědi operace. Potom odešle prázdnou zprávu `$iothub/twin/GET/?$rid={request id}`tématu , s vyplněnou hodnotou pro **ID požadavku**. Služba pak odešle zprávu s odpovědí obsahující `$iothub/twin/res/{status}/?$rid={request id}`data dvojčete zařízení v tématu pomocí stejného **ID požadavku** jako požadavek.

ID požadavku může být libovolná platná hodnota pro hodnotu vlastnosti zprávy, podle [průvodce vývojáře zasílání zpráv služby IoT Hub](iot-hub-devguide-messaging.md)a stav je ověřen jako celé číslo.

Tělo odpovědi obsahuje část vlastností dvojčete zařízení, jak je znázorněno v následujícím příkladu odpovědi:

```json
{
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
```

Možné stavové kódy jsou:

|Status | Popis |
| ----- | ----------- |
| 200 | Úspěch |
| 429 | Příliš mnoho požadavků (omezené), podle [omezení služby IoT Hub](iot-hub-devguide-quotas-throttling.md) |
| 5** | Chyby serveru |

Další informace naleznete v [příručce pro vývojáře dvojčat zařízení](iot-hub-devguide-device-twins.md).

## <a name="update-device-twins-reported-properties"></a>Aktualizovat ohlášené vlastnosti dvojčete zařízení

Chcete-li aktualizovat hlášené vlastnosti, zařízení vydá požadavek na službu IoT Hub prostřednictvím publikace přes určené téma MQTT. Po zpracování požadavku služba IoT Hub odpoví na stav úspěchu nebo selhání operace aktualizace prostřednictvím publikace na jiné téma. Toto téma může být přihlášeno k odběru zařízení, aby bylo možné jej upozornit na výsledek jeho žádosti o aktualizaci dvojčete. K implementaci tohoto typu interakce požadavku a odpovědi v MQTT využíváme pojem ID žádosti (`$rid`) původně poskytnutý zařízením v jeho žádosti o aktualizaci. Toto ID požadavku je také součástí odpovědi ze služby IoT Hub, aby zařízení mohlo korelovat odpověď na jeho konkrétní dřívější požadavek.

Následující sekvence popisuje, jak zařízení aktualizuje hlášené vlastnosti v dvojčeti zařízení v centru IoT Hub:

1. Zařízení se musí nejprve přihlásit k odběru tématu, `$iothub/twin/res/#` aby získalo odpovědi operace ze služby IoT Hub.

2. Zařízení odešle zprávu, která obsahuje aktualizaci dvojčete zařízení k tématu. `$iothub/twin/PATCH/properties/reported/?$rid={request id}` Tato zpráva obsahuje hodnotu **ID požadavku.**

3. Služba pak odešle zprávu odpovědi, která obsahuje novou hodnotu `$iothub/twin/res/{status}/?$rid={request id}`ETag pro kolekci ohlášených vlastností na téma . Tato zpráva odpovědi používá stejné **ID požadavku** jako požadavek.

Text zprávy požadavku obsahuje dokument JSON, který obsahuje nové hodnoty pro ohlášené vlastnosti. Každý člen dokumentu JSON aktualizuje nebo přidat odpovídající člen v dokumentu dvojčete zařízení. Člen nastavenna `null`na , odstraní člen z obsahující objekt. Příklad:

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

Možné stavové kódy jsou:

|Status | Popis |
| ----- | ----------- |
| 204 | Úspěch (není vrácen žádný obsah) |
| 400 | Špatná žádost. Poškozený JSON |
| 429 | Příliš mnoho požadavků (omezené), podle [omezení služby IoT Hub](iot-hub-devguide-quotas-throttling.md) |
| 5** | Chyby serveru |

Fragment kódu pythonu níže demonstruje proces aktualizace vlastností twin hlášeny přes MQTT (pomocí klienta Paho MQTT):

```python
from paho.mqtt import client as mqtt

# authenticate the client with IoT Hub (not shown here)

client.subscribe("$iothub/twin/res/#")
rid = "1"
twin_reported_property_patch = "{\"firmware_version\": \"v1.1\"}"
client.publish("$iothub/twin/PATCH/properties/reported/?$rid=" +
               rid, twin_reported_property_patch, qos=0)
```

Po úspěchu předchozí operace aktualizace vlastností hlášeny výše, zpráva publikace z `$iothub/twin/res/204/?$rid=1&$version=6`ioT Hub u sebe bude mít následující téma: , kde `204` je stavový kód označující úspěch, `$rid=1` odpovídá ID požadavku poskytované zařízení v kódu a `$version` odpovídá verzi ohlášené vlastnosti části dvojčat zařízení po aktualizaci.

Další informace naleznete v [příručce pro vývojáře dvojčat zařízení](iot-hub-devguide-device-twins.md).

## <a name="receiving-desired-properties-update-notifications"></a>Příjem oznámení o aktualizaci požadovaných vlastností

Když je připojeno zařízení, služba IoT `$iothub/twin/PATCH/properties/desired/?$version={new version}`Hub odešle oznámení tématu , která obsahují obsah aktualizace prováděné back-endem řešení. Příklad:

```json
{
    "telemetrySendFrequency": "5m",
    "route": null,
    "$version": 8
}
```

Pokud jde o `null` aktualizace vlastností, hodnoty znamenají, že člen objektu JSON je odstraněn. Všimněte si `$version` také, že označuje novou verzi požadované vlastnosti části dvojčete.

> [!IMPORTANT]
> IoT Hub generuje oznámení o změnách pouze v případě, že jsou připojena zařízení. Ujistěte se, že implementovat [tok opětovného připojení zařízení](iot-hub-devguide-device-twins.md#device-reconnection-flow) zachovat požadované vlastnosti synchronizované mezi IoT Hub a aplikace zařízení.

Další informace naleznete v [příručce pro vývojáře dvojčat zařízení](iot-hub-devguide-device-twins.md).

## <a name="respond-to-a-direct-method"></a>Reakce na přímou metodu

Nejprve se zařízení musí `$iothub/methods/POST/#`přihlásit k odběru . IoT Hub odesílá požadavky `$iothub/methods/POST/{method name}/?$rid={request id}`na metody tématu , s platným JSON nebo prázdným tělem.

Chcete-li odpovědět, zařízení odešle zprávu s platnou JSON nebo prázdné tělo na téma `$iothub/methods/res/{status}/?$rid={request id}`. V této zprávě musí **ID požadavku** odpovídat id ve zprávě požadavku a **stav** musí být celé číslo.

Další informace naleznete [v příručce pro vývojáře metod Direct](iot-hub-devguide-direct-methods.md).

## <a name="additional-considerations"></a>Další aspekty

Jako poslední úvahu, pokud potřebujete přizpůsobit chování protokolu MQTT na straně cloudu, měli byste zkontrolovat [bránu protokolu Azure IoT](iot-hub-protocol-gateway.md). Tento software umožňuje nasadit vysoce výkonnou bránu vlastního protokolu, která je propojena přímo s službou IoT Hub. Brána protokolu Azure IoT umožňuje přizpůsobit protokol zařízení tak, aby vyhovoval nasazením Brownfield MQTT nebo jiným vlastním protokolům. Tento přístup však vyžaduje spuštění a provoz vlastní brány protokolu.

## <a name="next-steps"></a>Další kroky

Další informace o protokolu MQTT naleznete v [dokumentaci K MQTT](https://mqtt.org/documentation).

Další informace o plánování nasazení služby IoT Hub najdete v tématu:

* [Katalog zařízení Azure Certified for IoT](https://catalog.azureiotsolutions.com/)
* [Podpora dalších protokolů](iot-hub-protocol-gateway.md)
* [Porovnat s centry událostí](iot-hub-compare-event-hubs.md)
* [Změna velikosti, HA a zotavení po havárii](iot-hub-scaling.md)

Další informace o možnostech IoT Hubu najdete v následujících tématech:

* [Průvodce vývojáři ioT Hubu](iot-hub-devguide.md)
* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
