---
title: Principy podpory AMQP služby Azure IoT Hub | Dokumenty společnosti Microsoft
description: Průvodce pro vývojáře – podpora zařízení, která se připojují ke koncovým bodům pro zařízení a ke službám služby služby služby izoslužby pomocí protokolu AMQP. Obsahuje informace o integrované podpoře AMQP v sadách SDK zařízení Azure IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: robinsh
ms.openlocfilehash: 7f7e957502419b766f7da63048e8168192ea20da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284782"
---
# <a name="communicate-with-your-iot-hub-by-using-the-amqp-protocol"></a>Komunikujte s centrem IoT pomocí protokolu AMQP

Azure IoT Hub podporuje [OASIS Advanced Message Queuing Protocol (AMQP) verze 1.0](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf) poskytovat celou řadu funkcí prostřednictvím koncových bodů směřujících k zařízení a službám. Tento dokument popisuje použití klientů AMQP pro připojení k centru IoT k použití funkcí služby IoT Hub.

## <a name="service-client"></a>Klient služby

### <a name="connect-and-authenticate-to-an-iot-hub-service-client"></a>Připojení a ověření k centru IoT (klient služby)

Chcete-li se připojit k centru IoT pomocí služby AMQP, může klient použít [ověřování zabezpečení založené na deklaracích (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) nebo jednoduché ověřování a zabezpečení [(SASL).](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer)

Pro klienta služby jsou vyžadovány následující informace:

| Informace | Hodnota |
|-------------|--------------|
| Název hostitele centra IoT | `<iot-hub-name>.azure-devices.net` |
| Název klíče | `service` |
| Přístupový klíč | Primární nebo sekundární klíč přidružený ke službě |
| Sdílený přístupový podpis | Krátkodobý sdílený přístupový podpis v následujícím `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`formátu: . Pokud chcete získat kód pro generování tohoto podpisu, [přečtěte si téma Řízení přístupu k centru IoT Hub](./iot-hub-devguide-security.md#security-token-structure).

Následující fragment kódu používá [knihovnu uAMQP v Pythonu](https://github.com/Azure/azure-uamqp-python) k připojení k centru IoT prostřednictvím odkazu odesílatele.

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
operation = '<operation-link-name>'  # example: '/messages/devicebound'

username = '{policy_name}@sas.root.{iot_hub_name}'.format(
    iot_hub_name=iot_hub_name, policy_name=policy_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

# Create a send or receive client
send_client = uamqp.SendClient(uri, debug=True)
receive_client = uamqp.ReceiveClient(uri, debug=True)
```

### <a name="invoke-cloud-to-device-messages-service-client"></a>Vyvolání zpráv mezi cloudy (klient služby)

Další informace o výměně zpráv mezi službou a službou IoT hub a mezi zařízením a službou IoT hub najdete v tématu [Odesílání zpráv z cloudu na zařízení z centra IoT](iot-hub-devguide-messages-c2d.md). Klient služby používá dva odkazy k odesílání zpráv a přijímání zpětné vazby pro dříve odeslané zprávy ze zařízení, jak je popsáno v následující tabulce:

| Autor | Typ odkazu | Cesta k propojení | Popis |
|------------|-----------|-----------|-------------|
| Služba | Odkaz odesílatele | `/messages/devicebound` | Zprávy z cloudu na zařízení, které jsou určeny pro zařízení, jsou odesílány na tento odkaz službou. Zprávy odeslané prostřednictvím `To` tohoto odkazu mají svou vlastnost nastavenou na cestu odkazu příjemce cílového zařízení . `/devices/<deviceID>/messages/devicebound` |
| Služba | Odkaz na přijímač | `/messages/serviceBound/feedback` | Dokončení, odmítnutí a opuštění zpětnou vazbu zprávy, které pocházejí ze zařízení přijatých na tento odkaz podle služby. Další informace o zprávách o zpětné vazbě najdete v [tématu Odesílání zpráv z cloudu na zařízení z centra IoT](./iot-hub-devguide-messages-c2d.md#message-feedback). |

Následující fragment kódu ukazuje, jak vytvořit zprávu z cloudu na zařízení a odeslat ji do zařízení pomocí [knihovny uAMQP v Pythonu](https://github.com/Azure/azure-uamqp-python).

```python
import uuid
# Create a message and set message property 'To' to the device-bound link on device
msg_id = str(uuid.uuid4())
msg_content = b"Message content goes here!"
device_id = '<device-id>'
to = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
ack = 'full'  # Alternative values are 'positive', 'negative', and 'none'
app_props = {'iothub-ack': ack}
msg_props = uamqp.message.MessageProperties(message_id=msg_id, to=to)
msg = uamqp.Message(msg_content, properties=msg_props,
                    application_properties=app_props)

# Send the message by using the send client that you created and connected to the IoT hub earlier
send_client.queue_message(msg)
results = send_client.send_all_messages()

# Close the client if it's not needed
send_client.close()
```

Chcete-li získat zpětnou vazbu, klient služby vytvoří odkaz příjemce. Následující fragment kódu ukazuje, jak vytvořit propojení pomocí [knihovny uAMQP v Pythonu](https://github.com/Azure/azure-uamqp-python):

```python
import json

operation = '/messages/serviceBound/feedback'

# ...
# Re-create the URI by using the preceding feedback path and authenticate it
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

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

Jak je znázorněno v předchozím kódu, zpráva zpětné vazby typu cloud-to-device má typ obsahu *aplikace/vnd.microsoft.iothub.feedback.json*. Vlastnosti v těle JSON zprávy můžete použít k odvodit stav doručení původní zprávy:

* Klíč `statusCode` v těle zpětné vazby má jednu z následujících hodnot: *Úspěch*, *Vypršela platnost*, *DeliveryCountExceedExceeded*, *Odmítnuto*nebo *Vymazáno*.

* Klíč `deviceId` v těle zpětné vazby má ID cílového zařízení.

* Klíč `originalMessageId` v těle zpětné vazby má ID původní zprávy cloud zařízení, která byla odeslána službou. Tento stav doručení můžete použít ke korelaci zpětné vazby se zprávami z cloudu na zařízení.

### <a name="receive-telemetry-messages-service-client"></a>Příjem telemetrických zpráv (klient služby)

Ve výchozím nastavení vaše centrum IoT ukládá zprávy telemetrie ingedu zařízení ve integrovaném centru událostí. Klient služby může použít protokol AMQP k přijímání uložených událostí.

Za tímto účelem se klient služby nejprve musí připojit ke koncovému bodu centra IoT a obdržet adresu přesměrování do předdefinovaných rozbočovačů událostí. Klient služby pak použije zajišťovnou adresu pro připojení k integrovanému centru událostí.

V každém kroku musí klient předložit následující informace:

* Platná pověření služby (token podpisu sdíleného přístupu služby).

* Dobře formátovaná cesta k oddílu skupiny spotřebitelů, ze kterého má v úmyslu načíst zprávy. Pro danou skupinu spotřebitelů a ID oddílu `/messages/events/ConsumerGroups/<consumer_group>/Partitions/<partition_id>` má cesta následující `$Default`formát: (výchozí skupina příjemce je ).

* Volitelný přefiltrovací predikát pro určení počátečního bodu v oddílu. Tento predikát může být ve formě pořadového čísla, posunu nebo zařazeného časového razítka.

Následující fragment kódu používá [knihovnu uAMQP v Pythonu](https://github.com/Azure/azure-uamqp-python) k předvedení předchozích kroků:

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
operation = '/messages/events/ConsumerGroups/{consumer_group}/Partitions/{p_id}'.format(
    consumer_group='$Default', p_id=0)

username = '{policy_name}@sas.root.{iot_hub_name}'.format(
    policy_name=policy_name, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

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


receive_client = uamqp.ReceiveClient(
    set_endpoint_filter(uri, endpoint_filter), debug=True)
try:
    batch = receive_client.receive_message_batch(max_batch_size=5)
except uamqp.errors.LinkRedirect as redirect:
    # Once a redirect error is received, close the original client and recreate a new one to the re-directed address
    receive_client.close()

    sas_auth = uamqp.authentication.SASTokenAuth.from_shared_access_key(
        redirect.address, policy_name, access_key)
    receive_client = uamqp.ReceiveClient(set_endpoint_filter(
        redirect.address, endpoint_filter), auth=sas_auth, debug=True)

# Start receiving messages in batches
batch = receive_client.receive_message_batch(max_batch_size=5)
for msg in batch:
    print('*** received a message ***')
    print(''.join(msg.get_data()))
    print('\t: ' + str(msg.annotations['x-opt-sequence-number']))
    print('\t: ' + str(msg.annotations['x-opt-offset']))
    print('\t: ' + str(msg.annotations['x-opt-enqueued-time']))
```

Pro dané ID zařízení používá centrum IoT hub hodnotu hash ID zařízení k určení, do kterého oddílu má být jeho zprávy ukládáno. Předchozí fragment kódu ukazuje, jak jsou události přijímány z jednoho takového oddílu. Všimněte si však, že typické aplikace často potřebuje načíst události, které jsou uloženy ve všech oddílech centra událostí.

## <a name="device-client"></a>Klient zařízení

### <a name="connect-and-authenticate-to-an-iot-hub-device-client"></a>Připojení a ověření k centru IoT hub (klient zařízení)

Chcete-li se připojit k centru IoT pomocí služby AMQP, může zařízení používat [ověřování na základě deklarací identity (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) nebo [jednoduché ověřování a zabezpečení (SASL).](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer)

Pro klienta zařízení jsou vyžadovány následující informace:

| Informace | Hodnota |
|-------------|--------------|
| Název hostitele centra IoT | `<iot-hub-name>.azure-devices.net` |
| Přístupový klíč | Primární nebo sekundární klíč přidružený k zařízení |
| Sdílený přístupový podpis | Krátkodobý sdílený přístupový podpis v následujícím `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`formátu: . Pokud chcete získat kód pro generování tohoto podpisu, [přečtěte si téma Řízení přístupu k centru IoT Hub](./iot-hub-devguide-security.md#security-token-structure).

Následující fragment kódu používá [knihovnu uAMQP v Pythonu](https://github.com/Azure/azure-uamqp-python) k připojení k centru IoT prostřednictvím odkazu odesílatele.

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
username = '{device_id}@sas.{iot_hub_name}'.format(
    device_id=device_id, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token('{hostname}/devices/{device_id}'.format(
    hostname=hostname, device_id=device_id), access_key, None)

# e.g., '/devices/{device_id}/messages/devicebound'
operation = '<operation-link-name>'
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
send_client = uamqp.SendClient(uri, debug=True)
```

Následující cesty propojení jsou podporovány jako operace zařízení:

| Autor | Typ odkazu | Cesta k propojení | Popis |
|------------|-----------|-----------|-------------|
| Zařízení | Odkaz na přijímač | `/devices/<deviceID>/messages/devicebound` | Zprávy cloud-to-device, které jsou určeny pro zařízení jsou přijímány na tomto odkazu každé cílové zařízení. |
| Zařízení | Odkaz odesílatele | `/devices/<deviceID>/messages/events` | Prostřednictvím tohoto odkazu jsou odesílány zprávy mezi zařízeními a cloudy, které jsou odesílány ze zařízení. |
| Zařízení | Odkaz odesílatele | `/messages/serviceBound/feedback` | Zpětná vazba zpráv z cloudu na zařízení odeslaná službě prostřednictvím tohoto odkazu zařízeními. |

### <a name="receive-cloud-to-device-commands-device-client"></a>Příjem příkazů cloud-to-device (klient zařízení)

Příkazy cloud-to-device, které jsou odesílány do zařízení dorazí na `/devices/<deviceID>/messages/devicebound` odkaz. Zařízení mohou přijímat tyto zprávy v dávkách a podle potřeby používat datovou část zprávy, vlastnosti zpráv, poznámky nebo vlastnosti aplikace ve zprávě.

Následující fragment kódu používá [knihovnu uAMQP v Pythonu](https://github.com/Azure/azure-uamqp-python)) k přijímání zpráv z cloudu na zařízení zařízením zařízením.

```python
# ...
# Create a receive client for the cloud-to-device receive link on the device
operation = '/devices/{device_id}/messages/devicebound'.format(
    device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

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
        print('\tcorrelation_id:         ' +
              str(msg.properties.correlation_id))
        print('\tcontent_type:           ' + str(msg.properties.content_type))
        print('\treply_to_group_id:      ' +
              str(msg.properties.reply_to_group_id))
        print('\tsubject:                ' + str(msg.properties.subject))
        print('\tuser_id:                ' + str(msg.properties.user_id))
        print('\tgroup_sequence:         ' +
              str(msg.properties.group_sequence))
        print('\tcontent_encoding:       ' +
              str(msg.properties.content_encoding))
        print('\treply_to:               ' + str(msg.properties.reply_to))
        print('\tabsolute_expiry_time:   ' +
              str(msg.properties.absolute_expiry_time))
        print('\tgroup_id:               ' + str(msg.properties.group_id))

        # Message sequence number in the built-in event hub
        print('\tx-opt-sequence-number:  ' +
              str(msg.annotations['x-opt-sequence-number']))
```

### <a name="send-telemetry-messages-device-client"></a>Odesílání telemetrických zpráv (klient zařízení)

Telemetrické zprávy ze zařízení můžete také odesílat pomocí služby AMQP. Zařízení může volitelně poskytnout slovník vlastností aplikace nebo různé vlastnosti zprávy, například ID zprávy.

Následující fragment kódu používá [knihovnu uAMQP v Pythonu](https://github.com/Azure/azure-uamqp-python) k odesílání zpráv mezi zařízeními a cloudy ze zařízení.

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

* Připojení AMQP může být přerušeno z důvodu chyby sítě nebo vypršení platnosti ověřovacího tokenu (generovaného v kódu). Klient služby musí tyto okolnosti zpracovat a v případě potřeby obnovit připojení a propojení. Pokud vyprší platnost ověřovacího tokenu, klient se může vyhnout přetažení připojení proaktivním obnovením tokenu před vypršením jeho platnosti.

* Váš klient musí být příležitostně schopen správně zpracovat přesměrování odkazů. Chcete-li pochopit takové operace, naleznete v dokumentaci klienta AMQP.

## <a name="next-steps"></a>Další kroky

Další informace o protokolu AMQP naleznete ve [specifikaci AMQP v1.0](https://www.amqp.org/sites/amqp.org/files/amqp.pdf).

Další informace o zasílání zpráv služby IoT Hub najdete v tématu:

* [Zprávy z cloudu na zařízení](./iot-hub-devguide-messages-c2d.md)
* [Podpora dalších protokolů](iot-hub-protocol-gateway.md)
* [Podpora protokolu MQTT (Message Queuing Telemetry Transport) (Message Queuing Telemetry Transport)](./iot-hub-mqtt-support.md)
