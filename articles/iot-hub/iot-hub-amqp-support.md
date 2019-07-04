---
title: Principy Azure IoT Hub AMQP podpory | Dokumentace Microsoftu
description: Příručka pro vývojáře – podpora pro zařízení připojující se ke službě IoT Hub zařízení zákazníky i služba koncových bodů pomocí protokolu AMQP. Obsahuje informace o předdefinovaných AMQP podporují v SDK pro zařízení Azure IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: robinsh
ms.openlocfilehash: e0c7b6aa9745beaf7a7d336e8308d12348bb274b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67432619"
---
# <a name="communicate-with-your-iot-hub-by-using-the-amqp-protocol"></a>Komunikovat se službou IoT hub pomocí protokolu AMQP

Azure IoT Hub podporuje [OASIS pokročilé řízení front zpráv protokolu (AMQP) verze 1.0](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf) poskytovat celou řadu funkcí prostřednictvím zařízení zákazníky i služba koncových bodů. Tento dokument popisuje použití AMQP klientům připojit se do služby IoT hub, jak používat funkce služby IoT Hub.

## <a name="service-client"></a>Klient služby

### <a name="connect-and-authenticate-to-an-iot-hub-service-client"></a>Připojit a provést ověření do služby IoT hub (klienta služby)

Připojení do služby IoT hub pomocí protokolu AMQP, může klient používat [zabezpečení na základě deklarací identity (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) nebo [jednoduché ověřování a zabezpečení vrstvy (SASL) ověřování](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer).

Tyto informace se vyžaduje pro klienta služby:

| Informace | Hodnota |
|-------------|--------------|
| Název hostitele služby IoT hub | `<iot-hub-name>.azure-devices.net` |
| Název klíče | `service` |
| Přístupový klíč | Primární nebo sekundární klíč, který je spojen se službou |
| Sdílený přístupový podpis | Krátkodobé a jednorázové sdíleného přístupového podpisu v následujícím formátu: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`. Získat kód pro generování tento podpis, naleznete v tématu [řízení přístupu ke službě IoT Hub](./iot-hub-devguide-security.md#security-token-structure).

Následující kód používá fragment kódu [uAMQP knihovny v jazyce Python](https://github.com/Azure/azure-uamqp-python) k připojení do služby IoT hub přes odkaz odesílatele.

```python
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here: 
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '<operation-link-name>' # example: '/messages/devicebound'

username = '{policy_name}@sas.root.{iot_hub_name}'.format(iot_hub_name=iot_hub_name, policy_name=policy_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

# Create a send or receive client
send_client = uamqp.SendClient(uri, debug=True)
receive_client = uamqp.ReceiveClient(uri, debug=True)
```

### <a name="invoke-cloud-to-device-messages-service-client"></a>Vyvolat zprávy typu cloud zařízení (klienta služby)

Další informace o výměně zpráv typu cloud zařízení, mezi služby a služby IoT hub a mezi zařízením a centrem IoT, najdete v článku [odesílat zprávy typu cloud zařízení ze služby IoT hub](iot-hub-devguide-messages-c2d.md). Služba Klient používá dva odkazy na odesílání zpráv a zpětná vazba pro příjem dříve odeslané zprávy ze zařízení, jak je popsáno v následující tabulce:

| Vytvořil | Typ odkazu | Cesta odkazu | Popis |
|------------|-----------|-----------|-------------|
| Služba | Odesílatel odkaz | `/messages/devicebound` | Zprávy typu cloud zařízení, které jsou určené pro zařízení jsou na tomto odkazu odeslaných službou. Zprávy odeslané přes tato spojení mají jejich `To` nastavenou na cestu propojení příjemce cílové zařízení, `/devices/<deviceID>/messages/devicebound`. |
| Služba | Příjemce odkaz | `/messages/serviceBound/feedback` | Dokončení, zamítnutí a zřeknutí zprávy zpětné vazby, které pocházejí z zařízení přijaté službou na tento odkaz. Další informace o zprávy se zpětnou vazbou, naleznete v tématu [odesílat zprávy typu cloud zařízení ze služby IoT hub](./iot-hub-devguide-messages-c2d.md#message-feedback). |

Následující fragment kódu ukazuje, jak vytvořit zprávu typu cloud zařízení a jejich odesílání do zařízení s použitím [uAMQP knihovny v jazyce Python](https://github.com/Azure/azure-uamqp-python).

```python
import uuid
# Create a message and set message property 'To' to the device-bound link on device
msg_id = str(uuid.uuid4())
msg_content = b"Message content goes here!"
device_id = '<device-id>'
to = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
ack = 'full' # Alternative values are 'positive', 'negative', and 'none'
app_props = { 'iothub-ack': ack }
msg_props = uamqp.message.MessageProperties(message_id=msg_id, to=to)
msg = uamqp.Message(msg_content, properties=msg_props, application_properties=app_props)

# Send the message by using the send client that you created and connected to the IoT hub earlier
send_client.queue_message(msg)
results = send_client.send_all_messages()

# Close the client if it's not needed
send_client.close()
```

Pokud chcete dostávat zpětnou vazbu, vytvoří klienta služby příjemce odkaz. Následující fragment kódu ukazuje, jak vytvořit pomocí odkazu [uAMQP knihovny v jazyce Python](https://github.com/Azure/azure-uamqp-python):

```python
import json

operation = '/messages/serviceBound/feedback'

# ...
# Re-create the URI by using the preceding feedback path and authenticate it
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

Jak je znázorněno v předchozím kódu, zpětnou vazbu typu cloud zařízení zpráva má typ obsahu *application/vnd.microsoft.iothub.feedback.json*. Použijte vlastnosti v textu JSON zprávy k odvození stav doručování původní zprávy:

* Klíč `statusCode` zpětné vazby text má jednu z následujících hodnot: *Úspěch*, *platnost*, *DeliveryCountExceeded*, *odmítl*, nebo *vymazat*.

* Klíč `deviceId` zpětné vazby text má ID cílového zařízení.

* Klíč `originalMessageId` zpětné vazby text má ID původní zprávy typu cloud zařízení, která byla odeslána služby. Tento stav doručování můžete použít ke korelaci zpětnou vazbu pro zprávy typu cloud zařízení.

### <a name="receive-telemetry-messages-service-client"></a>Přijímat telemetrické zprávy (klient služby)

Ve výchozím nastavení ukládá služby IoT hub telemetrické zprávy zařízení přijaté v předdefinované eventhub. Klient služby můžete použít protokol AMQP přijímat uložených událostí.

V tomto případě klient služby nejprve musí připojit ke koncovému bodu IoT hub a příjem přesměrování adres do vestavěné event hubs. Klient služby pro připojení k Centru událostí integrovanou použije zadaná adresa.

V každém kroku klient musí k dispozici následující údaje:

* Přihlašovací údaje platné služby (token sdíleného přístupového podpisu služby).

* Správně naformátovaný cestu k oddílu skupiny příjemců, který chce načítat zprávy z. Pro ID skupiny a oddílu dané příjemce cesta má následující formát: `/messages/events/ConsumerGroups/<consumer_group>/Partitions/<partition_id>` (výchozí skupina příjemců je `$Default`).

* Volitelný predikát filtru k určení výchozí bod v oddílu. Ve formě časové razítko číslo, offset nebo zařazených do fronty pořadí může být tento predikát.

Následující kód používá fragment kódu [uAMQP knihovny v jazyce Python](https://github.com/Azure/azure-uamqp-python) k předvedení v předchozích krocích:

```python
import json
import uamqp
import urllib
import time

# Use the generate_sas_token implementation that's available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '/messages/events/ConsumerGroups/{consumer_group}/Partitions/{p_id}'.format(consumer_group='$Default', p_id=0)

username = '{policy_name}@sas.root.{iot_hub_name}'.format(policy_name=policy_name, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

# Optional filtering predicates can be specified by using endpoint_filter
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

Pro Identifikátor dané zařízení služby IoT hub používá hodnoty hash ID zařízení k určení oddíl, který se k ukládání svých zpráv. Předchozí fragment kódu ukazuje, jak přijaté události z jedné těchto oddílů. Mějte však na paměti, že Typická aplikace často potřebuje načíst události, které jsou uložené ve všech oddílů centra událostí.

## <a name="device-client"></a>Klient zařízení

### <a name="connect-and-authenticate-to-an-iot-hub-device-client"></a>Připojit a provést ověření do služby IoT hub (klient zařízení)

Pro připojení do služby IoT hub pomocí protokolu AMQP, můžete použít zařízení [zabezpečení (CBS) na základě deklarací](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) nebo [jednoduché ověřování a zabezpečení vrstvy (SASL)](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer) ověřování.

Tyto informace se vyžaduje pro klienty zařízení:

| Informace | Hodnota |
|-------------|--------------|
| Název hostitele služby IoT hub | `<iot-hub-name>.azure-devices.net` |
| Přístupový klíč | Primární nebo sekundární klíč, který je spojen se zařízením |
| Sdílený přístupový podpis | Krátkodobé a jednorázové sdíleného přístupového podpisu v následujícím formátu: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`. Získat kód pro generování tento podpis, naleznete v tématu [řízení přístupu ke službě IoT Hub](./iot-hub-devguide-security.md#security-token-structure).

Následující kód používá fragment kódu [uAMQP knihovny v jazyce Python](https://github.com/Azure/azure-uamqp-python) k připojení do služby IoT hub přes odkaz odesílatele.

```python
import uamqp
import urllib
import uuid

# Use generate_sas_token implementation available here: 
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
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
| Zařízení | Příjemce odkaz | `/devices/<deviceID>/messages/devicebound` | Zprávy typu cloud zařízení, které jsou určené pro zařízení se dostávají na tento odkaz každé cílové zařízení. |
| Zařízení | Odesílatel odkaz | `/devices/<deviceID>messages/events` | Prostřednictvím tohoto odkazu se odesílají zprávy typu zařízení cloud odesílaných ze zařízení. |
| Zařízení | Odesílatel odkaz | `/messages/serviceBound/feedback` | Váš názor zprávy typu cloud zařízení byl odeslán službě přes tato spojení zařízení. |

### <a name="receive-cloud-to-device-commands-device-client"></a>Příjem příkazů typu cloud zařízení (zařízení klienta)

Příkazy typu cloud zařízení, které se odesílají do zařízení dorazit `/devices/<deviceID>/messages/devicebound` odkaz. Zařízení můžete tyto zprávy zobrazit v dávkách a podle potřeby použijte datovou část zprávy, vlastnosti zprávy, poznámek nebo vlastnosti aplikace ve zprávě.

Následující kód používá fragment kódu [uAMQP knihovny v jazyce Python](https://github.com/Azure/azure-uamqp-python)) pro příjem zpráv typu cloud zařízení na zařízení.

```python
# ...
# Create a receive client for the cloud-to-device receive link on the device
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

    # Message sequence number in the built-in event hub
    print('\tx-opt-sequence-number:  ' + str(msg.annotations['x-opt-sequence-number']))
```

### <a name="send-telemetry-messages-device-client"></a>Odesílání telemetrických zpráv (klient zařízení)

Můžete také odesílat telemetrických zpráv ze zařízení pomocí protokolu AMQP. Zařízení můžete volitelně zadat slovníku vlastností aplikace nebo různé zprávy vlastnosti, jako je například ID zprávy.

Následující kód používá fragment kódu [uAMQP knihovny v jazyce Python](https://github.com/Azure/azure-uamqp-python) k odesílání zpráv typu zařízení cloud ze zařízení.

```python
# ...
# Create a send client for the device-to-cloud send link on the device
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

* Z důvodu poruchu sítě nebo vypršení platnosti ověřování může přerušit připojení AMQP token (vygenerovat v kódu). Klient služby musí zpracovat těchto okolností a znovu vytvořit připojení a odkazy, v případě potřeby. Pokud vyprší platnost ověřovacího tokenu, klient se vyhnout přímé připojení aktivně obnovuje se token před vypršením jeho platnosti.

* Váš klient musí být čas od času schopný zvládnout přesměrování odkazu správně. Informace o tom tato operace, najdete v dokumentaci k klienta protokolu AMQP.

## <a name="next-steps"></a>Další postup

Další informace o protokolu AMQP, najdete v článku [specifikace protokolu AMQP 1.0](https://www.amqp.org/sites/amqp.org/files/amqp.pdf).

Další informace o zasílání zpráv služby IoT Hub, najdete v tématech:

* [Zprávy typu cloud zařízení](./iot-hub-devguide-messages-c2d.md)
* [Podpora dalších protokolů](iot-hub-protocol-gateway.md)
* [Podpora protokolu přenosu (protokolu MQTT) telemetrická data pro služby Řízení front zpráv](./iot-hub-mqtt-support.md)