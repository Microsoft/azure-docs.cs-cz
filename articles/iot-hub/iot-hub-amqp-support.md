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
ms.openlocfilehash: d256faa42161e276e165f95c944b9f58ac4a8927
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2019
ms.locfileid: "66297418"
---
# <a name="communicate-with-your-iot-hub-using-the-amqp-protocol"></a>Komunikovat se službou IoT hub pomocí protokolu AMQP

IoT Hub podporuje [AMQP verze 1.0](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf) poskytovat celou řadu funkcí prostřednictvím zařízení zákazníky i služba koncových bodů. Tento dokument popisuje použití AMQP klienty pro připojení ke službě IoT Hub, aby bylo možné používat funkce služby IoT Hub.

## <a name="service-client"></a>Klient služby

### <a name="connection-and-authenticating-to-iot-hub-service-client"></a>Připojení a ověřování pro službu IoT Hub (klienta služby)
Pro připojení ke službě IoT Hub pomocí protokolu AMQP, může klient používat [zabezpečení na základě deklarací identity (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) nebo [jednoduché ověřování a zabezpečení vrstvy (SASL) ověřování](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer).

Tyto informace se vyžaduje pro klienta služby:

| Informace | Hodnota | 
|-------------|--------------|
| IoT Hub Hostname | `<iot-hub-name>.azure-devices.net` |
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

| Vytvořil | Typ odkazu | Cesta odkazu | Popis |
|------------|-----------|-----------|-------------|
| Služba | Odesílatel odkaz | `/messages/devicebound` | C2D zprávy určené do zařízení jsou na tomto odkazu odeslaných službou. Zprávy odeslané přes tato spojení mají jejich `To` nastavenou na cestu propojení příjemce cílové zařízení: například `/devices/<deviceID>/messages/devicebound`. |
| Služba | Příjemce odkaz | `/messages/serviceBound/feedback` | Dokončení, zamítnutí a zřeknutí zpětné vazby zprávy přicházející ze zařízení na tento odkaz přijaté službou. Další informace o zprávy se zpětnou vazbou, naleznete v tématu [tady](./iot-hub-devguide-messages-c2d.md#message-feedback). |

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
Ve výchozím nastavení ukládá služby IoT Hub telemetrické zprávy zařízení přijaté v předdefinované centra událostí. Klient služby můžete použít protokol AMQP přijímat uložených událostí.

V tomto případě klient služby nejprve musí připojit ke koncovému bodu služby IoT Hub a příjem adresu přesměrování do integrované služby Event Hubs. Zadaná adresa klienta služby pak používá pro připojení k integrované centra událostí.

V každém kroku klient musí k dispozici následující údaje:
* Přihlašovací údaje platné služby (token SAS služby).
* Správně naformátovaný cestu k oddílu skupiny uživatelů chce načítat zprávy z. Pro ID skupiny a oddílu dané příjemce cesta má následující formát: `/messages/events/ConsumerGroups/<consumer_group>/Partitions/<partition_id>` (výchozí skupina příjemců je `$Default`).
* Volitelný predikát filtru k určení výchozí bod v oddílu (to může být ve formě časové razítko číslo, offset nebo zařazených do fronty pořadí).

Fragment kódu níže používá [uAMQP knihovny v jazyce Python](https://github.com/Azure/azure-uamqp-python) k předvedení výše uvedené kroky.

```python
import json
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '/messages/events/ConsumerGroups/{consumer_group}/Partitions/{p_id}'.format(consumer_group='$Default', p_id=0)

username = '{policy_name}@sas.root.{iot_hub_name}'.format(policy_name=policy_name, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

# Optional filtering predicates can be specified using endpiont_filter
# Valid predicates include:
# - amqp.annotation.x-opt-sequence-number
# - amqp.annotation.x-opt-offset
# - amqp.annotation.x-opt-enqueued-time
# Set endpoint_filter variable to None if no filter is needed
endpoint_filter = b'amqp.annotation.x-opt-sequence-number > 2995'

# Helper function to set the filtering predicate on the source URI
def set_endpoint_filter(uri, endpoint_filter=''):
  source_uri = uamqp.address.Source(uri)
  source_uri.set_filter(endpoint_filter)
  return source_uri

receive_client = uamqp.ReceiveClient(set_endpoint_filter(uri, endpoint_filter), debug=True)
try:
  batch = receive_client.receive_message_batch(max_batch_size=5)
except uamqp.errors.LinkRedirect as redirect:
  # Once a redirect error is received, close the original client and recreate a new one to the re-directed address
  receive_client.close()

  sas_auth = uamqp.authentication.SASTokenAuth.from_shared_access_key(redirect.address, policy_name, access_key)
  receive_client = uamqp.ReceiveClient(set_endpoint_filter(redirect.address, endpoint_filter), auth=sas_auth, debug=True)

# Start receiving messages in batches
batch = receive_client.receive_message_batch(max_batch_size=5)
for msg in batch:
  print('*** received a message ***')
  print(''.join(msg.get_data()))
  print('\t: ' + str(msg.annotations['x-opt-sequence-number']))
  print('\t: ' + str(msg.annotations['x-opt-offset']))
  print('\t: ' + str(msg.annotations['x-opt-enqueued-time']))
```

Pro Identifikátor dané zařízení služby IoT Hub používá hodnoty hash ID zařízení k určení oddíl, který se k ukládání svých zpráv. Výše uvedeném fragmentu kódu ukazuje přijímání událostí z jedné těchto oddílů. Všimněte si však, že Typická aplikace často potřebuje k načtení událostí uložených ve všech oddílů centra událostí.


## <a name="device-client"></a>Klient zařízení

### <a name="connection-and-authenticating-to-iot-hub-device-client"></a>Připojení a ověřování pro službu IoT Hub (klient zařízení)
Pro připojení ke službě IoT Hub pomocí protokolu AMQP, můžete použít zařízení [zabezpečení na základě deklarací identity (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) nebo [jednoduché ověřování a zabezpečení vrstvy (SASL) ověřování](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer).

Tyto informace se vyžaduje pro klienty zařízení:

| Informace | Hodnota | 
|-------------|--------------|
| IoT Hub Hostname | `<iot-hub-name>.azure-devices.net` |
| Přístupový klíč | Primární nebo sekundární klíč přidružený k zařízení |
| Sdílený přístupový podpis | Krátkodobé a jednorázové SAS v následujícím formátu: `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}` (kód tento podpis najdete [tady](./iot-hub-devguide-security.md#security-token-structure)).


Fragment kódu níže používá [uAMQP knihovny v jazyce Python](https://github.com/Azure/azure-uamqp-python) pro připojení ke službě IoT hub přes odkaz odesílatele.

```python
import uamqp
import urllib
import uuid

# Use generate_sas_token implementation available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
device_id = '<device-id>'
access_key = '<primary-or-secondary-key>'
username = '{device_id}@sas.{iot_hub_name}'.format(device_id=device_id, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token('{hostname}/devices/{device_id}'.format(hostname=hostname, device_id=device_id), access_key, None)

operation = '<operation-link-name>' # e.g., '/devices/{device_id}/messages/devicebound'
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
send_client = uamqp.SendClient(uri, debug=True)
```

Jako operace zařízení jsou podporovány následující cesty odkazů:

| Vytvořil | Typ odkazu | Cesta odkazu | Popis |
|------------|-----------|-----------|-------------|
| Zařízení | Příjemce odkaz | `/devices/<deviceID>/messages/devicebound` | C2D směřující do zařízení dostávají zprávy na tento odkaz každé cílové zařízení. |
| Zařízení | Odesílatel odkaz | `/devices/<deviceID>messages/events` | D2C zprávy odeslané ze zařízení se odesílají přes tato spojení. |
| Zařízení | Odesílatel odkaz | `/messages/serviceBound/feedback` | C2D zpráva váš názor byl odeslán službě přes tento odkaz na zařízeních. |


### <a name="receive-c2d-commands-device-client"></a>Přijímá příkazy C2D (klient zařízení)
Doručení C2D příkazů odesílaných do zařízení na `/devices/<deviceID>/messages/devicebound` odkaz. Zařízení můžete tyto zprávy zobrazit v dávkách a podle potřeby použijte datovou část zprávy, vlastnosti zprávy, poznámek nebo vlastnosti aplikace ve zprávě.

Fragment kódu níže používá [uAMQP knihovny v jazyce Python](https://github.com/Azure/azure-uamqp-python) pro příjem zpráv C2D zařízení.

```python
# ... 
# Create a receive client for the C2D receive link on the device
operation = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
while True:
  batch = receive_client.receive_message_batch(max_batch_size=5)
  for msg in batch:
    print('*** received a message ***')
    print(''.join(msg.get_data()))

    # Property 'to' is set to: '/devices/device1/messages/devicebound',
    print('\tto:                     ' + str(msg.properties.to))

    # Property 'message_id' is set to value provided by the service
    print('\tmessage_id:             ' + str(msg.properties.message_id))

    # Other properties are present if they were provided by the service
    print('\tcreation_time:          ' + str(msg.properties.creation_time))
    print('\tcorrelation_id:         ' + str(msg.properties.correlation_id))
    print('\tcontent_type:           ' + str(msg.properties.content_type))
    print('\treply_to_group_id:      ' + str(msg.properties.reply_to_group_id))
    print('\tsubject:                ' + str(msg.properties.subject))
    print('\tuser_id:                ' + str(msg.properties.user_id))
    print('\tgroup_sequence:         ' + str(msg.properties.group_sequence))
    print('\tcontent_encoding:       ' + str(msg.properties.content_encoding))
    print('\treply_to:               ' + str(msg.properties.reply_to))
    print('\tabsolute_expiry_time:   ' + str(msg.properties.absolute_expiry_time))
    print('\tgroup_id:               ' + str(msg.properties.group_id))

    # Message sequence number in the built-in Event hub
    print('\tx-opt-sequence-number:  ' + str(msg.annotations['x-opt-sequence-number']))
```

### <a name="send-telemetry-messages-device-client"></a>Odesílání telemetrických zpráv (klient zařízení)
Telemetrické zprávy dají posílat i přes protokol AMQP ze zařízení. Zařízení můžete volitelně zadat slovníku vlastností aplikace nebo různé zprávy vlastnosti, jako je ID zprávy.

Fragment kódu níže používá [uAMQP knihovny v jazyce Python](https://github.com/Azure/azure-uamqp-python) odesílat D2C zprávy ze zařízení.


```python
# ... 
# Create a send client for the D2C send link on the device
operation = '/devices/{device_id}/messages/events'.format(device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

send_client = uamqp.SendClient(uri, debug=True)

# Set any of the applicable message properties
msg_props = uamqp.message.MessageProperties()
msg_props.message_id = str(uuid.uuid4())
msg_props.creation_time = None
msg_props.correlation_id = None
msg_props.content_type = None
msg_props.reply_to_group_id = None
msg_props.subject = None
msg_props.user_id = None
msg_props.group_sequence = None
msg_props.to = None
msg_props.content_encoding = None
msg_props.reply_to = None
msg_props.absolute_expiry_time = None
msg_props.group_id = None

# Application properties in the message (if any)
application_properties = { "app_property_key": "app_property_value" }

# Create message
msg_data = b"Your message payload goes here"
message = uamqp.Message(msg_data, properties=msg_props, application_properties=application_properties)

send_client.queue_message(message)
results = send_client.send_all_messages()

for result in results:
    if result == uamqp.constants.MessageState.SendFailed:
        print result
```

## <a name="additional-notes"></a>Další poznámky
* Připojení AMQP k přerušení kvůli sítě poruchu nebo uplynutí ověřovací token (vygenerovat v kódu). Klient služby musí zpracovat tyto okolnosti a znovu navázat připojení a odkazy v případě potřeby. Pro případ vypršení platnosti tokenu ověřování může klient také aktivně obnovení tokenu před jeho vypršení platnosti, aby se zabránilo přímé připojení.
* V některých případech musí být schopen správně zpracovat odkaz přesměrování klienta. V dokumentaci klienta protokolu AMQP o tom, jak zpracovat tuto operaci.

## <a name="next-steps"></a>Další postup

Další informace o protokolu AMQP, najdete v článku [specifikace protokolu AMQP 1.0](http://www.amqp.org/sites/amqp.org/files/amqp.pdf).

Další informace o zasílání zpráv služby IoT Hub, najdete v tématech:

* [Zprávy typu cloud zařízení](./iot-hub-devguide-messages-c2d.md)
* [Podpora dalších protokolů](iot-hub-protocol-gateway.md)
* [Podpora protokolu MQTT](./iot-hub-mqtt-support.md)
