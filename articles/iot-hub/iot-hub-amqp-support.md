---
title: Principy Azure IoT Hub AMQP podpory | Dokumentace Microsoftu
description: Příručka pro vývojáře – podpora pro zařízení připojující se ke službě IoT Hub zařízení zákazníky i služba koncových bodů pomocí protokolu AMQP. Obsahuje informace o předdefinovaných AMQP podporují v SDK pro zařízení Azure IoT.
author: rezasherafat
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: rezas
ms.openlocfilehash: 703e2c842fb42bad8aa112d84c516a29c2327378
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2019
ms.locfileid: "65473503"
---
# <a name="communicate-with-your-iot-hub-using-the-amqp-protocol"></a>Komunikovat se službou IoT hub pomocí protokolu AMQP

IoT Hub podporuje [AMQP verze 1.0](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf) poskytovat celou řadu funkcí prostřednictvím zařízení zákazníky i služba koncových bodů. Tento dokument popisuje použití AMQP klienty pro připojení ke službě IoT Hub, aby bylo možné používat funkce služby IoT Hub.

## <a name="service-client"></a>Klient služby

### <a name="connection-and-authenticating-to-iot-hub-service-client"></a>Připojení a ověřování pro službu IoT Hub (klienta služby)
Pro připojení ke službě IoT Hub pomocí protokolu AMQP, může klient používat [zabezpečení na základě deklarací identity (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) nebo [jednoduché ověřování a zabezpečení vrstvy (SASL) ověřování](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer).

Tyto informace se vyžaduje pro klienta služby:

| Informace | Value | 
|-------------|--------------|
| Název hostitele služby IoT Hub | `<iot-hub-name>.azure-devices.net` |
| Název klíče | `service` |
| Přístupový klíč | Primární a sekundární klíč související se službou |
| Sdílený přístupový podpis | Krátkodobé a jednorázové SAS v následujícím formátu: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}` (kód tento podpis najdete [tady](./iot-hub-devguide-security.md#security-token-structure)).


Fragment kódu níže používá [uAMQP knihovny v jazyce Python](https://github.com/Azure/azure-uamqp-python) pro připojení ke službě IoT hub přes odkaz odesílatele.

```python
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '<operation-link-name>' # e.g., '/messages/devicebound'

username = '{policy_name}@sas.root.{iot_hub_name}'.format(iot_hub_name=iot_hub_name, policy_name=policy_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

# Create a send or receive client
send_client = uamqp.SendClient(uri, debug=True)
receive_client = uamqp.ReceiveClient(uri, debug=True)
```

### <a name="invoking-cloud-to-device-messages-service-client"></a>Vyvolání zprávy typu cloud zařízení (klienta služby)
Zprávy typu cloud zařízení exchange mezi službou a IoT Hub, stejně jako mezi zařízením a centrem IoT je popsán [tady](iot-hub-devguide-messages-c2d.md). Klient služby používá dva odkazy, které jsou popsané níže, aby zprávy odesílat a přijímat zpětnou vazbu pro dříve odeslané zprávy ze zařízení.

| Vytvořil(a) | Typ odkazu | Cesta odkazu | Popis |
|------------|-----------|-----------|-------------|
| Služba | Odesílatel odkaz | `/messages/devicebound` | C2D zprávy určené do zařízení jsou na tomto odkazu odeslaných službou. Zprávy odeslané přes tato spojení mají jejich `To` nastavenou na cestu propojení příjemce cílové zařízení: například `/devices/<deviceID>/messages/devicebound`. |
| Služba | Příjemce odkaz | `/messages/serviceBound/feedback` | Dokončení, zamítnutí a zřeknutí zpětné vazby zprávy přicházející ze zařízení na tento odkaz přijaté službou. Zobrazit [tady](./iot-hub-devguide-messages-c2d.md#message-feedback) Další informace o zprávy se zpětnou vazbou. |

Následující fragment kódu ukazuje, jak vytvořit zprávu C2D a odeslat do zařízení pomocí [uAMQP knihovny v jazyce Python](https://github.com/Azure/azure-uamqp-python).

```python
import uuid
# Create a message and set message property 'To' to the devicebound link on device
msg_id = str(uuid.uuid4())
msg_content = b"Message content goes here!"
device_id = '<device-id>'
to = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
ack = 'full' # Alternative values are 'positive', 'negative', and 'none'
app_props = { 'iothub-ack': ack }
msg_props = uamqp.message.MessageProperties(message_id=msg_id, to=to)
msg = uamqp.Message(msg_content, properties=msg_props, application_properties=app_props)

# Send the message using the send client created and connected IoT Hub earlier
send_client.queue_message(msg)
results = send_client.send_all_messages()

# Close the client if not needed
send_client.close()
```

Pokud chcete dostávat zpětnou vazbu, vytvoří klienta služby příjemce odkaz. Následující fragment kódu ukazuje, jak to udělat pomocí [uAMQP knihovny v jazyce Python](https://github.com/Azure/azure-uamqp-python).

```python
import json

operation = '/messages/serviceBound/feedback'

# ...
# Recreate the URI using the feedback path above and authenticate
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
batch = receive_client.receive_message_batch(max_batch_size=10)
for msg in batch:
  print('received a message')
  # Check content_type in message property to identify feedback messages coming from device
  if msg.properties.content_type == 'application/vnd.microsoft.iothub.feedback.json':
    msg_body_raw = msg.get_data()
    msg_body_str = ''.join(msg_body_raw)
    msg_body = json.loads(msg_body_str)
    print(json.dumps(msg_body, indent=2))
    print('******************')
    for feedback in msg_body:
      print('feedback received')
      print('\tstatusCode: ' + str(feedback['statusCode']))
      print('\toriginalMessageId: ' + str(feedback['originalMessageId']))
      print('\tdeviceId: ' + str(feedback['deviceId']))
      print
  else:
    print('unknown message:', msg.properties.content_type)
```

Jak uvádíme výš, C2D zpětnou vazbu zpráva obsahuje typ obsahu `application/vnd.microsoft.iothub.feedback.json` a vlastnosti v jeho textu JSON můžete použít k odvození stav doručování původní zprávy:
* Klíč `statusCode` je součástí zpětné vazby text má některý z těchto hodnot: `['Success', 'Expired', 'DeliveryCountExceeded', 'Rejected', 'Purged']`.
* Klíč `deviceId` je součástí zpětné vazby text má ID cílové zařízení.
* Klíč `originalMessageId` je součástí zpětné vazby text má ID původní zprávy C2D odeslaných službou. To lze použít ke korelaci zpětnou vazbu, která C2D zprávy.

### <a name="receive-telemetry-messages-service-client"></a>Přijímat telemetrické zprávy (klient služby)


### <a name="additional-notes"></a>Další poznámky
* Připojení AMQP k přerušení kvůli sítě poruchu nebo uplynutí ověřovací token (vygenerovat v kódu). Klient služby musí zpracovat tyto okolnosti a znovu navázat připojení a odkazy v případě potřeby. Pro případ vypršení platnosti tokenu ověřování může klient také aktivně obnovení tokenu před jeho vypršení platnosti, aby se zabránilo přímé připojení.
* V některých případech musí být schopen správně zpracovat odkaz přesměrování klienta. V dokumentaci klienta protokolu AMQP o tom, jak to provést.

### <a name="receive-cloud-to-device-messages-device-and-module-client"></a>Příjem zpráv typu cloud zařízení (zařízení a modul klienta)
AMQP odkazů použité na straně zařízení jsou následující:

| Vytvořil(a) | Typ odkazu | Cesta odkazu | Popis |
|------------|-----------|-----------|-------------|
| Zařízení | Příjemce odkaz | `/devices/<deviceID>/messages/devicebound` | C2D směřující do zařízení dostávají zprávy na tento odkaz každé cílové zařízení. |
| Zařízení | Odesílatel odkaz | `/messages/serviceBound/feedback` | C2D zpráva váš názor byl odeslán službě přes tento odkaz na zařízeních. |
| Moduly | Příjemce odkaz | `/devices/<deviceID>/modules/<moduleID>/messages/devicebound` | C2D směřující k modulům dostávají zprávy na tento odkaz na každý cílový modul. |
| Moduly | Odesílatel odkaz | `/messages/serviceBound/feedback` | C2D zpráva váš názor byl odeslán službě přes tato spojení moduly. |


## <a name="next-steps"></a>Další postup

Další informace o protokolu AMQP, najdete v článku [specifikace protokolu AMQP 1.0](http://www.amqp.org/sites/amqp.org/files/amqp.pdf).

Další informace o zasílání zpráv služby IoT Hub, najdete v tématech:

* [Zprávy typu cloud zařízení](./iot-hub-devguide-messages-c2d.md)
* [Podpora dalších protokolů](iot-hub-protocol-gateway.md)
* [Podpora protokolu MQTT](./iot-hub-mqtt-support.md)
