---
title: Vysvětlení podpory Azure IoT Hub MQTT | Microsoft Docs
description: Příručka pro vývojáře – podpora pro zařízení, která se připojují k IoT Hub koncovému bodu s přístupem k zařízení pomocí protokolu MQTT. Obsahuje informace o integrované podpoře MQTT v sadách SDK pro zařízení Azure IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: robinsh
ms.openlocfilehash: 9a6b3a538304f2d09941650e3087130c21422dc0
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946350"
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>Komunikace se službou IoT Hub pomocí protokolu MQTT

IoT Hub umožňuje zařízením komunikovat s koncovými body IoT Hub zařízení pomocí:

* [MQTT v 3.1.1](https://mqtt.org/) na portu 8883
* MQTT v 3.1.1 přes WebSocket na portu 443.

IoT Hub není plnohodnotným zprostředkovatelem MQTT a nepodporuje všechna chování zadaná ve standardu MQTT v 3.1.1. Tento článek popisuje, jak můžou zařízení používat podporovaná MQTT chování ke komunikaci s IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Všechna komunikace zařízení s IoT Hub musí být zabezpečená pomocí protokolu TLS/SSL. Proto IoT Hub nepodporuje nezabezpečená připojení přes port 1883.

## <a name="connecting-to-iot-hub"></a>Připojování k IoT Hub

Zařízení může používat protokol MQTT pro připojení ke službě IoT Hub pomocí kterékoli z následujících možností.

* Knihovny v sadách [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks).
* Protokol MQTT přímo.

## <a name="using-the-device-sdks"></a>Používání sad SDK pro zařízení

Sady [SDK pro zařízení](https://github.com/Azure/azure-iot-sdks) , které podporují protokol MQTT, jsou k dispozici pro jazyky Java, C#Node. js, C, a Python. Sady SDK pro zařízení používají standardní připojovací řetězec IoT Hub k navázání připojení ke službě IoT Hub. Aby bylo možné používat protokol MQTT, musí být parametr klientského protokolu nastaven na hodnotu **MQTT**. Sady SDK pro zařízení se ve výchozím nastavení připojují k IoT Hub s příznakem **CleanSession** nastaveným na **hodnotu 0** a používají **technologii QoS 1** pro výměnu zpráv se službou IoT Hub.

Když je zařízení připojené ke službě IoT Hub, poskytují sady SDK pro zařízení metody, které umožní zařízení vyměňovat zprávy se službou IoT Hub.

Následující tabulka obsahuje odkazy na ukázky kódu pro každý podporovaný jazyk a určuje parametr, který se použije k navázání připojení k IoT Hub pomocí protokolu MQTT.

| Jazyk | Parametr protokolu |
| --- | --- |
| [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js) |azure-iot-device-mqtt |
| [Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java) |IotHubClientProtocol.MQTT |
| [C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm) |MQTT_Protocol |
| [C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples) |TransportType.Mqtt |
| [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/device/samples) |IoTHubTransportProvider. MQTT |

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Migrace aplikace zařízení z AMQP do MQTT

Pokud používáte sady SDK pro [zařízení](https://github.com/Azure/azure-iot-sdks), přepnutí z použití AMQP na MQTT vyžaduje změnu parametru protokolu v inicializaci klienta, jak je uvedeno výše.

V takovém případě nezapomeňte zkontrolovat následující položky:

* AMQP vrací chyby pro mnoho podmínek, zatímco MQTT ukončí připojení. V důsledku toho může logika zpracování výjimek vyžadovat nějaké změny.

* MQTT nepodporuje operace *odmítnutí* při přijímání [zpráv z cloudu na zařízení](iot-hub-devguide-messaging.md). Pokud vaše back-end aplikace potřebuje přijmout odpověď z aplikace zařízení, zvažte použití [přímých metod](iot-hub-devguide-direct-methods.md).

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Přímé použití protokolu MQTT (jako zařízení)

Pokud zařízení nemůže používat sady SDK pro zařízení, může se stále připojit k koncovým bodům veřejných zařízení pomocí protokolu MQTT na portu 8883. V paketu **připojení** by mělo zařízení používat následující hodnoty:

* V poli **ClientID** použijte **deviceId**.

* V poli **uživatelské jméno** použijte `{iothubhostname}/{device_id}/?api-version=2018-06-30`, kde `{iothubhostname}` je kompletní záznam CNAME služby IoT Hub.

    Například pokud je název vašeho centra IoT **contoso.Azure-Devices.NET** a pokud je název vašeho zařízení **MyDevice01**, pole úplné **uživatelské jméno** by mělo obsahovat následující:

    `contoso.azure-devices.net/MyDevice01/?api-version=2018-06-30`

* V poli **heslo** použijte token SAS. Formát tokenu SAS je stejný jako u protokolů HTTPS a AMQP:

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > Pokud používáte ověřování pomocí certifikátu X. 509, hesla tokenů SAS se nevyžadují. Další informace najdete v tématu [nastavení zabezpečení X. 509 ve službě Azure IoT Hub](iot-hub-security-x509-get-started.md) a [níže](#tlsssl-configuration)v následujících pokynech pro kód.

  Další informace o tom, jak generovat tokeny SAS, najdete v části zařízení [použití tokenů zabezpečení IoT Hub](iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app).

  Při testování můžete také použít [nástroje Azure IoT Tools pro různé platformy pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) nebo nástroj [Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) k rychlému vygenerování tokenu SAS, který můžete zkopírovat a vložit do vlastního kódu:

### <a name="for-azure-iot-tools"></a>Pro Azure IoT Tools

1. Rozbalte kartu **zařízení Azure IoT Hub** v levém dolním rohu Visual Studio Code.
  
2. Klikněte pravým tlačítkem na zařízení a vyberte **Generovat token SAS pro zařízení**.
  
3. Nastavte **čas vypršení platnosti** a stiskněte klávesu ENTER.
  
4. Token SAS je vytvořen a zkopírován do schránky.

### <a name="for-device-explorer"></a>Pro Device Explorer

1. V **Device Explorer**otevřete kartu **Správa** .

2. Klikněte na **token SAS** (vpravo nahoře).

3. V **SASTokenForm**vyberte zařízení v rozevíracím seznamu **DeviceID** . Nastavte **hodnotu TTL**.

4. Kliknutím na vytvořit vytvořte token.

   Vygenerovaný token SAS má následující strukturu:

   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

   Část tohoto tokenu, která se má použít jako pole **hesla** pro připojení pomocí MQTT:

   `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

V případě MQTT připojení a odpojení paketů IoT Hub vydá událost na kanálu **monitorování operací** . Tato událost obsahuje další informace, které vám můžou pomoct vyřešit problémy s připojením.

Aplikace zařízení může v paketu **Connect** zadat zprávu. Aplikace zařízení by se měla `devices/{device_id}/messages/events/` použít `devices/{device_id}/messages/events/{property_bag}` , nebo jako název tématu, který se má definovat, **bude** zprávy předávané jako zpráva telemetrie. V takovém případě, pokud je síťové připojení ukončeno, ale ze zařízení nebyl dříve přijat paket pro **odpojení** , IoT Hub odešle zprávu dodanou v paketu **připojit** k kanálu telemetrie. Kanál telemetrie může být buď koncovým bodem výchozí **události** , nebo vlastním koncovým bodem definovaným IoT Hub směrováním. Zpráva obsahuje vlastnost **iothub-MessageType** s hodnotou, která se mu přiřadí.

## <a name="using-the-mqtt-protocol-directly-as-a-module"></a>Přímé použití protokolu MQTT (jako modul)

Připojení k IoT Hub přes MQTT pomocí identity modulu je podobné zařízení (popsané [výše](#using-the-mqtt-protocol-directly-as-a-device)), ale musíte použít následující:

* Nastavte ID klienta na `{device_id}/{module_id}`.

* Při ověřování pomocí uživatelského jména a hesla nastavte uživatelské jméno na `<hubname>.azure-devices.net/{device_id}/{module_id}/?api-version=2018-06-30` a jako heslo použijte token SAS přidružený k identitě modulu.

* Použijte `devices/{device_id}/modules/{module_id}/messages/events/` jako téma pro publikování telemetrie.

* Použijte `devices/{device_id}/modules/{module_id}/messages/events/` jako téma.

* Dvojitá témata GET a PATCH jsou pro moduly a zařízení shodná.

* Téma s dvojitým stavem je pro moduly a zařízení stejné.

## <a name="tlsssl-configuration"></a>Konfigurace protokolu TLS/SSL

Pokud chcete protokol MQTT použít přímo, *musí* se klient připojit přes protokol TLS/SSL. Pokusy o přeskočení tohoto kroku selžou s chybami připojení.

Aby bylo možné vytvořit připojení TLS, bude pravděpodobně nutné stáhnout a odkazovat na kořenový certifikát DigiCert Baltimore. Tento certifikát je ten, který Azure používá k zabezpečení připojení. Tento certifikát najdete v úložišti [Azure-IoT-SDK-c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) . Další informace o těchto certifikátech najdete na [webu DigiCert](https://www.digicert.com/digicert-root-certificates.htm).

Příkladem toho, jak implementovat to pomocí verze Python [knihovny PAHO MQTT](https://pypi.python.org/pypi/paho-mqtt) v rámci služby zatmění Foundation, může vypadat takto.

Nejdřív nainstalujte knihovnu PAHO z prostředí příkazového řádku:

```cmd/sh
pip install paho-mqtt
```

Potom implementujte klienta ve skriptu Python. Zástupné symboly nahraďte následujícím způsobem:

* `<local path to digicert.cer>`je cesta k místnímu souboru, který obsahuje kořenový certifikát DigiCert Baltimore. Tento soubor můžete vytvořit zkopírováním informací o certifikátu z části [certifikáty. c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) v sadě Azure IoT SDK pro c. Přidejte řádky `-----BEGIN CERTIFICATE-----` `"` a `-----END CERTIFICATE-----`odstraňte značky na začátku a konci každého řádku a odeberte `\r\n`znaků na konci každého řádku.

* `<device id from device registry>`je ID zařízení, které jste přidali do služby IoT Hub.

* `<generated SAS token>`je token SAS pro zařízení vytvořený, jak je popsáno dříve v tomto článku.

* `<iot hub name>`název centra IoT.

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
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1, ciphers=None)
client.tls_insecure_set(False)

client.connect(iot_hub_name+".azure-devices.net", port=8883)

client.publish("devices/" + device_id + "/messages/events/", "{id=123}", qos=1)
client.loop_forever()
```

Níže jsou uvedené pokyny k instalaci požadovaných součástí.

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

Pokud chcete provést ověření pomocí certifikátu zařízení, aktualizujte výše uvedený fragment kódu pomocí následujících změn (Další informace najdete v tématu [Jak získat certifikát certifikační autority X. 509](./iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate) pro přípravu ověřování na základě certifikátů):

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
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1, ciphers=None)

# Connect as before
client.connect(iot_hub_name+".azure-devices.net", port=8883)
```

## <a name="sending-device-to-cloud-messages"></a>Posílání zpráv ze zařízení do cloudu

Po úspěšném připojení může zařízení posílat zprávy, které se IoT Hub pomocí `devices/{device_id}/messages/events/` **názvu tématu**nebo. `devices/{device_id}/messages/events/{property_bag}` `{property_bag}` Prvek umožňuje zařízení odesílat zprávy s dalšími vlastnostmi ve formátu kódovaném adresou URL. Příklad:

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> Tento `{property_bag}` prvek používá stejné kódování jako řetězce dotazů v protokolu HTTPS.

Následuje seznam IoT Hub chování specifické pro implementaci:

* IoT Hub nepodporuje zprávy technologie QoS 2. Pokud aplikace pro zařízení publikuje zprávu s **technologií QoS 2**, IoT Hub ukončí síťové připojení.

* IoT Hub nezachovají uchování zpráv. Pokud zařízení pošle zprávu s příznakem **uchovat** nastaveným na hodnotu 1, IoT Hub do zprávy přidá vlastnost aplikace **x-opt-rechovává** . V takovém případě se místo uchování zprávy o zachovává IoT Hub předává do back-endové aplikace.

* IoT Hub podporuje jenom jedno aktivní připojení MQTT pro každé zařízení. Jakékoli nové připojení MQTT pro stejné ID zařízení způsobí, že IoT Hub vynechá existující připojení.

Další informace najdete v tématu [Příručka pro vývojáře pro zasílání zpráv](iot-hub-devguide-messaging.md).

## <a name="receiving-cloud-to-device-messages"></a>Příjem zpráv z cloudu na zařízení

Pokud chcete dostávat zprávy z IoT Hub, zařízení by se mělo `devices/{device_id}/messages/devicebound/#` přihlásit jako **Filtr tématu**. Zástupný znak `#` na více úrovních v rámci filtru tématu slouží pouze k tomu, aby zařízení přijímalo další vlastnosti v názvu tématu. IoT Hub nepovoluje použití `#` zástupných znaků nebo `?` pro filtrování dílčích témat. Vzhledem k tomu, že IoT Hub není modul pro zasílání zpráv typu Pub pro obecné účely, podporuje pouze dokumentované názvy témat a filtry témat.

Zařízení neobdrží žádné zprávy od IoT Hub, dokud se úspěšně přihlásí ke koncovému bodu pro konkrétní zařízení, reprezentovanému `devices/{device_id}/messages/devicebound/#` filtrem tématu. Po navázání předplatného zařízení obdrží zprávy typu cloud-zařízení, které se do ní poslaly po uplynutí doby platnosti předplatného. Pokud se zařízení připojí s příznakem **CleanSession** nastaveným na **hodnotu 0**, předplatné se trvale zachová napříč různými relacemi. V takovém případě se při příštím připojení zařízení k **CleanSession 0** obdrží všechny nedokončené zprávy, které se do ní odesílají během odpojení. Pokud zařízení používá příznak **CleanSession** nastavené na **1** , ale neobdrží žádné zprávy z IoT Hub, dokud se přihlásí k odběru koncového bodu zařízení.

IoT Hub doručuje zprávy s **názvem** `devices/{device_id}/messages/devicebound/`tématu, nebo `devices/{device_id}/messages/devicebound/{property_bag}` Pokud jsou vlastnosti zprávy. `{property_bag}`obsahuje páry klíč/hodnota zakódované adresy URL vlastností zprávy. Do kontejneru objektů a dat jsou zahrnuty pouze vlastnosti aplikace a uživatelsky nastavitelované systémové vlastnosti (například **MessageID** nebo **ID korelace**). Názvy systémových vlastností mají předponu **$** , vlastnosti aplikace používají název původní vlastnosti bez předpony.

Když se aplikace zařízení přihlásí k odběru tématu s **QoS 2**, IoT Hub v paketu **SUBACK** udělí maximální úroveň QoS úrovně 1. Potom IoT Hub doručuje zprávy na zařízení pomocí technologie QoS 1.

## <a name="retrieving-a-device-twins-properties"></a>Načítají se vlastnosti vlákna zařízení.

Nejdřív se zařízení přihlásí k odběru `$iothub/twin/res/#`, aby mohl přijímat odpovědi na operaci. Pak pošle prázdnou zprávu do tématu `$iothub/twin/GET/?$rid={request id}`s vyplněnou hodnotou **ID žádosti**. Služba pak pošle zprávu odpovědi obsahující data zařízení s dvojitým obsahem v `$iothub/twin/res/{status}/?$rid={request id}`tématu s použitím stejného **ID žádosti** jako požadavek.

ID žádosti může být libovolná platná hodnota pro hodnotu vlastnosti zprávy, jak je to [IoT Hub Příručka vývojáře pro zasílání zpráv](iot-hub-devguide-messaging.md)a stav je ověřen jako celé číslo.

Tělo odpovědi obsahuje oddíl vlastností vlákna zařízení, jak je znázorněno v následujícím příkladu odpovědi:

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

Možné stavové kódy:

|Stav | Popis |
| ----- | ----------- |
| 204 | Úspěch (není vrácen žádný obsah) |
| 429 | Příliš mnoho požadavků (omezení) podle [omezení pro IoT Hub](iot-hub-devguide-quotas-throttling.md) |
| 5** | Chyby serveru |

Další informace najdete v tématu [Příručka vývojáře pro vlákna v zařízení](iot-hub-devguide-device-twins.md).

## <a name="update-device-twins-reported-properties"></a>Aktualizace hlášených vlastností nezaznamenaného vlákna zařízení

K aktualizaci hlášených vlastností zařízení vydá požadavek na IoT Hub prostřednictvím publikace v rámci určeného MQTT tématu. Po zpracování žádosti IoT Hub odpoví na stav úspěch nebo selhání operace aktualizace prostřednictvím publikace v jiném tématu. Toto téma může být přihlášené k odběru zařízení za účelem upozornění na výsledek jeho vyplynulé žádosti o aktualizaci. Pro implementaci tohoto typu interakce s žádostmi a odpověďmi v MQTT využíváme pojem ID žádosti (`$rid`), který je na začátku zařízení v žádosti o aktualizaci. Toto ID žádosti je také zahrnuté v odpovědi z IoT Hub, aby bylo možné zařízení korelovat s odpovědí na jeho konkrétní předchozí požadavek.

Následující text popisuje, jak zařízení aktualizuje hlášené vlastnosti v zařízení, které je v IoT Hub.

1. Zařízení se musí nejdřív přihlásit k odběru `$iothub/twin/res/#` tématu, aby přijímalo odpovědi operace od IoT Hub.

2. Zařízení pošle zprávu s informacemi o tom, že se v zařízení `$iothub/twin/PATCH/properties/reported/?$rid={request id}` nachází aktualizace s dvojitou příponou. Tato zpráva obsahuje hodnotu **ID žádosti** .

3. Služba pak pošle zprávu odpovědi, která obsahuje novou hodnotu ETag pro nahlášenou kolekci vlastností v tématu `$iothub/twin/res/{status}/?$rid={request id}`. Tato zpráva odpovědi používá stejné **ID požadavku** jako požadavek.

Tělo zprávy požadavku obsahuje dokument JSON, který obsahuje nové hodnoty pro hlášené vlastnosti. Každý člen v dokumentu JSON aktualizuje nebo přidá odpovídajícího člena do dokumentu vlákna v zařízení. Člen nastavený na odstraní `null`člena z objektu, který ho obsahuje. Příklad:

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

Možné stavové kódy:

|Stav | Popis |
| ----- | ----------- |
| 200 | Úspěch |
| 400 | Chybný požadavek. Chybně vytvořený kód JSON |
| 429 | Příliš mnoho požadavků (omezení) podle [omezení pro IoT Hub](iot-hub-devguide-quotas-throttling.md) |
| 5** | Chyby serveru |

Níže uvedený fragment kódu Pythonu demonstruje nedokončený proces aktualizace vlastností prostřednictvím MQTT (pomocí klienta PAHO MQTT):

```python
from paho.mqtt import client as mqtt

# authenticate the client with IoT Hub (not shown here)

client.subscribe("$iothub/twin/res/#")
rid = "1"
twin_reported_property_patch = "{\"firmware_version\": \"v1.1\"}"
client.publish("$iothub/twin/PATCH/properties/reported/?$rid=" +
               rid, twin_reported_property_patch, qos=0)
```

Po úspěchu výše nahlášené operace aktualizace vlastností výše bude mít zpráva publikace z IoT Hub následující téma:, kde `$iothub/twin/res/204/?$rid=1&$version=6` `204` je stavový kód indikující úspěch, `$rid=1` odpovídá ID žádosti. je poskytováno zařízením v kódu a `$version` odpovídá části verze hlášených vlastností, které jsou v zařízení po aktualizaci vyzdvojené.

Další informace najdete v tématu [Příručka vývojáře pro vlákna v zařízení](iot-hub-devguide-device-twins.md).

## <a name="receiving-desired-properties-update-notifications"></a>Přijímání oznámení o aktualizaci požadovaných vlastností

Když je zařízení připojené, IoT Hub odesílá oznámení do tématu `$iothub/twin/PATCH/properties/desired/?$version={new version}`, které obsahuje obsah aktualizace provedené back-endu řešení. Příklad:

```json
{
    "telemetrySendFrequency": "5m",
    "route": null,
    "$version": 8
}
```

Jako u aktualizací `null` vlastností hodnoty znamená, že je odstraněn člen objektu JSON. Všimněte si také, `$version` že označuje novou verzi oddílu požadovaných vlastností vlákna.

> [!IMPORTANT]
> IoT Hub generuje oznámení o změnách jenom v případě, že jsou zařízení připojená. Ujistěte se, že jste implementovali [Postup opětovného připojení zařízení](iot-hub-devguide-device-twins.md#device-reconnection-flow) , abyste zachovali požadované vlastnosti synchronizované mezi IoT Hub a aplikací zařízení.

Další informace najdete v tématu [Příručka vývojáře pro vlákna v zařízení](iot-hub-devguide-device-twins.md).

## <a name="respond-to-a-direct-method"></a>Reakce na přímou metodu

Nejdřív se zařízení musí přihlásit k odběru `$iothub/methods/POST/#`. IoT Hub odesílá požadavky metody do tématu `$iothub/methods/POST/{method name}/?$rid={request id}`s platným JSON nebo prázdným tělem.

V případě, že zařízení odpoví, odešle do tématu `$iothub/methods/res/{status}/?$rid={request id}`zprávu s platným kódem JSON nebo prázdným textem. V této zprávě musí **ID žádosti** odpovídat hodnotě ve zprávě požadavku a **stav** musí být celé číslo.

Další informace najdete v tématu [Průvodce pro vývojáře Direct Method](iot-hub-devguide-direct-methods.md).

## <a name="additional-considerations"></a>Další aspekty

Pokud potřebujete přizpůsobit chování protokolu MQTT na straně cloudu, měli byste si prohlédnout [bránu protokolu Azure IoT](iot-hub-protocol-gateway.md). Tento software vám umožní nasadit vysoce výkonnou bránu vlastního protokolu, kterou rozhraní přímo s IoT Hub. Brána protokolu Azure IoT umožňuje přizpůsobit protokol zařízení tak, aby vyhovoval nasazením brownfield MQTT nebo jiným vlastním protokolům. Tento přístup ale vyžaduje, abyste spustili a provozoval vlastní bránu protokolu.

## <a name="next-steps"></a>Další postup

Další informace o protokolu MQTT najdete v [dokumentaci k MQTT](https://mqtt.org/documentation).

Další informace o plánování nasazení IoT Hub najdete v tématech:

* [Katalog zařízení Azure Certified for IoT](https://catalog.azureiotsolutions.com/)
* [Podpora dalších protokolů](iot-hub-protocol-gateway.md)
* [Porovnat s Event Hubs](iot-hub-compare-event-hubs.md)
* [Škálování, HA a zotavení po havárii](iot-hub-scaling.md)

Chcete-li dále prozkoumat možnosti IoT Hub, přečtěte si:

* [IoT Hub příručka pro vývojáře](iot-hub-devguide.md)
* [Nasazení AI do hraničních zařízení pomocí Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
