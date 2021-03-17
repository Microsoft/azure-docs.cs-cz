---
title: Vysvětlení podpory Azure IoT Hub AMQP | Microsoft Docs
description: Příručka pro vývojáře – podpora pro zařízení, která se připojují k IoT Hub koncovým bodům s přístupem k zařízením a službám s využitím protokolu AMQP. Obsahuje informace o integrované podpoře AMQP v sadách SDK pro zařízení Azure IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: robinsh
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 7b3dcfc51df7f0fe4291e9c5babccc1444ad32e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "81730753"
---
# <a name="communicate-with-your-iot-hub-by-using-the-amqp-protocol"></a>Komunikace se službou IoT Hub pomocí protokolu AMQP

Azure IoT Hub podporuje [OASIS rozšířený protokol řízení front zpráv (AMQP) (AMQP) verze 1,0](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf) k dodávání nejrůznějších funkcí prostřednictvím koncových bodů s přístupem k zařízením a službám. Tento dokument popisuje použití AMQP klientů pro připojení ke službě IoT Hub pro použití funkcí IoT Hub.

## <a name="service-client"></a>Klient služby

### <a name="connect-and-authenticate-to-an-iot-hub-service-client"></a>Připojení a ověření ve službě IoT Hub (klient služby)

Aby bylo možné se připojit ke službě IoT Hub pomocí AMQP, může klient použít ověřování [CBS (based Based Security)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) nebo [rozhraní SASL (Simple Authentication and Security Layer) (SASL)](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer).

Pro klienta služby jsou vyžadovány následující informace:

| Informace | Hodnota |
|-------------|--------------|
| Název hostitele IoT Hub | `<iot-hub-name>.azure-devices.net` |
| Název klíče | `service` |
| Přístupový klíč | Primární nebo sekundární klíč, který je přidružený ke službě |
| Sdílený přístupový podpis | Krátkodobý podpis sdíleného přístupu v následujícím formátu: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}` . Chcete-li získat kód pro generování tohoto podpisu, přečtěte si téma [řízení přístupu k IoT Hub](./iot-hub-devguide-security.md#security-token-structure).

Následující fragment kódu používá [knihovnu uAMQP v Pythonu](https://github.com/Azure/azure-uamqp-python) pro připojení ke službě IoT Hub prostřednictvím odkazu odesílatele.

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

### <a name="invoke-cloud-to-device-messages-service-client"></a>Vyvolání zpráv z cloudu na zařízení (klient služby)

Další informace o výměně zpráv z cloudu na zařízení mezi službou a centrem IoT a mezi zařízením a službou IoT Hub najdete v tématu [posílání zpráv z cloudu na zařízení ze služby IoT Hub](iot-hub-devguide-messages-c2d.md). Klient služby používá dva odkazy k posílání zpráv a přijímání zpětné vazby pro dříve odeslané zprávy ze zařízení, jak je popsáno v následující tabulce:

| Vytvořil(a) | Typ odkazu | Cesta odkazu | Description |
|------------|-----------|-----------|-------------|
| Služba | Odkaz odesílatele | `/messages/devicebound` | Zprávy z cloudu do zařízení, které jsou určené pro zařízení, se na tento odkaz odesílají prostřednictvím služby. Zprávy odeslané prostřednictvím tohoto odkazu mají svou `To` vlastnost nastavenou na cestu k přijímači cílového zařízení `/devices/<deviceID>/messages/devicebound` . |
| Služba | Odkaz na přijímače | `/messages/serviceBound/feedback` | Zprávy o dokončení, odmítnutí a zrušení zpětné vazby, které pocházejí ze zařízení přijatých prostřednictvím tohoto odkazu, se službou. Další informace o zprávách zpětné vazby najdete v tématu [posílání zpráv z cloudu na zařízení ze služby IoT Hub](./iot-hub-devguide-messages-c2d.md#message-feedback). |

Následující fragment kódu ukazuje, jak vytvořit zprávu typu cloud-zařízení a odeslat ji do zařízení pomocí [knihovny uAMQP v Pythonu](https://github.com/Azure/azure-uamqp-python).

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

Pro příjem zpětné vazby vytvoří klient služby odkaz na přijímač. Následující fragment kódu ukazuje, jak vytvořit propojení pomocí [knihovny uAMQP v Pythonu](https://github.com/Azure/azure-uamqp-python):

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

Jak je uvedeno v předchozím kódu, zpráva o zpětné vazbě z cloudu na zařízení má typ obsahu *aplikace/vnd.microsoft.iothub.feedback.js*. Stav doručení původní zprávy můžete odvodit pomocí vlastností v těle zprávy JSON:

* Klíč `statusCode` v těle zpětné vazby má jednu z následujících hodnot: *úspěch*, *vypršení platnosti*, *DeliveryCountExceeded*, *zamítnutí*nebo *vyprázdnění*.

* Klíč `deviceId` v těle zpětné vazby má ID cílového zařízení.

* Klíč `originalMessageId` v těle zpětné vazby má ID původní zprávy typu cloud-zařízení, která byla odeslána službou. Tento stav doručování můžete použít ke korelaci zpětné vazby ke zprávám z cloudu na zařízení.

### <a name="receive-telemetry-messages-service-client"></a>Dostávat zprávy telemetrie (klient služby)

Ve výchozím nastavení ukládá služba IoT Hub zprávy o telemetrie zařízení do integrovaného centra událostí. Váš klient služby může k přijímání uložených událostí použít protokol AMQP.

Pro tento účel se musí klient služby nejdřív připojit ke koncovému bodu služby IoT Hub a získat adresu přesměrování na integrovaná centra událostí. Klient služby pak pomocí zadané adresy připojí k integrovanému centru událostí.

V každém kroku musí klient prezentovat následující informace:

* Platná pověření služby (token sdíleného přístupového podpisu služby).

* Dobře formátovaná cesta k oddílu skupiny příjemců, ze které zamýšlí načíst zprávy. Pro danou skupinu uživatelů a ID oddílu má cesta následující formát: `/messages/events/ConsumerGroups/<consumer_group>/Partitions/<partition_id>` (výchozí skupina uživatelů je `$Default` ).

* Volitelný predikát filtru k určení počátečního bodu v oddílu. Tento predikát může být ve formě pořadového čísla, posunu nebo časového razítka ve frontě.

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

Pro dané ID zařízení používá centrum IoT k určení oddílu, ve kterém se mají ukládat zprávy, hodnotu hash ID zařízení. Předchozí fragment kódu ukazuje, jak jsou události přijímány z jednoho takového oddílu. Nicméně Všimněte si, že Typická aplikace často potřebuje načítat události, které jsou uložené ve všech oddílech centra událostí.

## <a name="device-client"></a>Klient zařízení

### <a name="connect-and-authenticate-to-an-iot-hub-device-client"></a>Připojení a ověření ve službě IoT Hub (klient zařízení)

Pokud se chcete připojit ke službě IoT Hub pomocí AMQP, může zařízení používat ověřování protokolem [CBS (Claim Based Security)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) nebo [rozhraní SASL (Simple Authentication and Security Layer) (SASL)](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer) .

Pro klienta zařízení jsou vyžadovány následující informace:

| Informace | Hodnota |
|-------------|--------------|
| Název hostitele IoT Hub | `<iot-hub-name>.azure-devices.net` |
| Přístupový klíč | Primární nebo sekundární klíč, který je přidružený k zařízení |
| Sdílený přístupový podpis | Krátkodobý podpis sdíleného přístupu v následujícím formátu: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}` . Chcete-li získat kód pro generování tohoto podpisu, přečtěte si téma [řízení přístupu k IoT Hub](./iot-hub-devguide-security.md#security-token-structure).

Následující fragment kódu používá [knihovnu uAMQP v Pythonu](https://github.com/Azure/azure-uamqp-python) pro připojení ke službě IoT Hub prostřednictvím odkazu odesílatele.

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

Následující cesty odkazů jsou podporovány jako operace zařízení:

| Vytvořil(a) | Typ odkazu | Cesta odkazu | Description |
|------------|-----------|-----------|-------------|
| Zařízení | Odkaz na přijímače | `/devices/<deviceID>/messages/devicebound` | Zprávy z cloudu do zařízení, které jsou určené pro zařízení, se na tomto odkazu přijímají na základě každého cílového zařízení. |
| Zařízení | Odkaz odesílatele | `/devices/<deviceID>/messages/events` | Zprávy typu zařízení-Cloud, které jsou odesílány ze zařízení, se odesílají prostřednictvím tohoto odkazu. |
| Zařízení | Odkaz odesílatele | `/messages/serviceBound/feedback` | Zpětná vazba ze zprávy z cloudu na zařízení, kterou zařízení odesílá prostřednictvím tohoto odkazu. |

### <a name="receive-cloud-to-device-commands-device-client"></a>Příjem příkazů z cloudu na zařízení (klient zařízení)

Příkazy typu cloud-zařízení, které se odesílají do zařízení, přicházejí na `/devices/<deviceID>/messages/devicebound` odkaz. Zařízení mohou přijímat tyto zprávy v dávkách a v případě potřeby ve zprávě použít datovou část dat zprávy, vlastnosti zprávy, poznámky nebo vlastnosti aplikace.

Následující fragment kódu používá [knihovnu uAMQP v Pythonu](https://github.com/Azure/azure-uamqp-python)k přijímání zpráv z cloudu na zařízení podle zařízení.

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

### <a name="send-telemetry-messages-device-client"></a>Poslat zprávy telemetrie (klient zařízení)

Zprávy telemetrie můžete také odeslat ze zařízení pomocí AMQP. Zařízení může volitelně poskytnout slovník vlastností aplikace nebo různé vlastnosti zpráv, jako je ID zprávy.

Následující fragment kódu používá [knihovnu uAMQP v Pythonu](https://github.com/Azure/azure-uamqp-python) k posílání zpráv typu zařízení-Cloud ze zařízení.

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

* Připojení AMQP může být přerušeno kvůli síťovému porucha nebo vypršení platnosti ověřovacího tokenu (vygenerovaného v kódu). Klient služby musí tyto okolnosti zpracovat a v případě potřeby znovu vytvořit připojení a odkazy. Pokud platnost ověřovacího tokenu vyprší, klient se může vyhnout odpojení jeho odpojením před vypršením platnosti tokenu.

* Je možné, že klient může občas zpracovat přesměrování propojení správně. Pokud chcete tuto operaci pochopit, přečtěte si dokumentaci ke klientovi AMQP.

## <a name="next-steps"></a>Další kroky

Další informace o protokolu AMQP najdete v tématu [specifikace AMQP v 1.0](https://www.amqp.org/sites/amqp.org/files/amqp.pdf).

Další informace o IoT Hub zasílání zpráv najdete v tématech:

* [Zprávy z cloudu na zařízení](./iot-hub-devguide-messages-c2d.md)
* [Podpora dalších protokolů](iot-hub-protocol-gateway.md)
* [Podpora protokolu pro přenos telemetrie služby Řízení front zpráv (MQTT)](./iot-hub-mqtt-support.md)
