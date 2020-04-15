---
title: Odesílání zpráv o zabezpečení zařízení
description: Přečtěte si, jak posílat zprávy zabezpečení pomocí Azure Security Center pro IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: c611bb5c-b503-487f-bef4-25d8a243803d
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2020
ms.author: mlottner
ms.openlocfilehash: 4877493982671b1b5db686715ef854f25c2966ea
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310984"
---
# <a name="send-security-messages-sdk"></a>Odeslání zpráv zabezpečení sady SDK

Tento návod vysvětluje možnosti Služby Azure Security Center pro služby IoT, když se rozhodnete shromažďovat a odesílat zprávy zabezpečení zařízení bez použití agenta Azure Security Center pro IoT a vysvětluje, jak to udělat.

V této příručce se naučíte:

> [!div class="checklist"]
> * Odesílání zpráv zabezpečení pomocí sady Azure IoT C SDK
> * Odesílání zpráv zabezpečení pomocí sady Azure IoT C# SDK
> * Odesílání zpráv zabezpečení pomocí sady Azure IoT Python SDK
> * Odesílání zpráv zabezpečení pomocí sady Azure IoT Node.js SDK
> * Odesílání zpráv zabezpečení pomocí sady Azure IoT Java SDK

## <a name="azure-security-center-for-iot-capabilities"></a>Azure Security Center pro funkce IoT

Azure Security Center pro IoT můžete zpracovat a analyzovat jakýkoli druh dat zpráv zabezpečení tak dlouho, dokud data odeslaná odpovídá [Azure Security Center pro ioT schéma](https://aka.ms/iot-security-schemas) a zpráva je nastavena jako zpráva zabezpečení.

## <a name="security-message"></a>Zpráva zabezpečení

Azure Security Center pro IoT definuje zprávu zabezpečení pomocí následujících kritérií:

- Pokud byla zpráva odeslána pomocí sady Azure IoT SDK
- Pokud zpráva odpovídá [schématu zprávy zabezpečení](https://aka.ms/iot-security-schemas)
- Pokud byla zpráva před odesláním nastavena jako zpráva zabezpečení

Každá zpráva zabezpečení obsahuje metadata odesílatele, `AgentVersion` `MessageSchemaVersion` například `AgentId`, a seznam událostí zabezpečení.
Schéma definuje platné a požadované vlastnosti zprávy zabezpečení včetně typů událostí.

> [!NOTE]
> Odeslané zprávy, které nejsou v souladu se schématem, jsou ignorovány. Před zahájením odesílání dat nezapomeňte ověřit schéma, protože ignorované zprávy nejsou aktuálně uloženy.

> [!NOTE]
> Odeslané zprávy, které nebyly nastaveny jako zpráva zabezpečení pomocí sady Azure IoT SDK, nebudou směrovány do kanálu Centra zabezpečení Azure pro IoT.

## <a name="valid-message-example"></a>Platný příklad zprávy

Následující příklad ukazuje platný objekt zprávy zabezpečení. Příklad obsahuje metadata zprávy `ProcessCreate` a jednu událost zabezpečení.

Po nastavení jako zpráva zabezpečení a odeslané, tato zpráva bude zpracována Azure Security Center pro IoT.

```json
"AgentVersion": "0.0.1",
"AgentId": "e89dc5f5-feac-4c3e-87e2-93c16f010c25",
"MessageSchemaVersion": "1.0",
"Events": [
    {
        "EventType": "Security",
        "Category": "Triggered",
        "Name": "ProcessCreate",
        "IsEmpty": false,
        "PayloadSchemaVersion": "1.0",
        "Id": "21a2db0b-44fe-42e9-9cff-bbb2d8fdf874",
        "TimestampLocal": "2019-01-27 15:48:52Z",
        "TimestampUTC": "2019-01-27 13:48:52Z",
        "Payload":
            [
                {
                    "Executable": "/usr/bin/myApp",
                    "ProcessId": 11750,
                    "ParentProcessId": 1593,
                    "UserName": "aUser",
                    "CommandLine": "myApp -a -b"
                }
            ]
    }
]
```

## <a name="send-security-messages"></a>Odesílání zpráv zabezpečení

Posílejte zprávy o zabezpečení *bez* použití Azure Security Center pro agenta IoT, pomocí [sady Azure IoT C device SDK](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview), Azure [IoT C# device SDK](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview), [Azure IoT Node.js SDK](https://github.com/Azure/azure-iot-sdk-node), [Azure IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python)nebo [Azure IoT Java SDK](https://github.com/Azure/azure-iot-sdk-java).

Pokud chcete odeslat data zařízení ze svých zařízení ke zpracování pomocí Azure Security Center pro IoT, použijte jedno z následujících rozhraní API k označení zpráv pro správné směrování do kanálu pro zpracování IoT centra zabezpečení Azure.

Všechna data, která jsou odeslána, i když jsou označena správnou hlavičkou, musí být také v souladu se [schématem zpráv Centra zabezpečení Azure pro IoT](https://aka.ms/iot-security-schemas).

### <a name="send-security-message-api"></a>Odeslat rozhraní API zprávy zabezpečení

Rozhraní **API odesílání zpráv zabezpečení** je aktuálně k dispozici v jazycích C a C#, Pythonu, Node.js a jazyce Java.

#### <a name="c-api"></a>C API

```c
bool SendMessageAsync(IoTHubAdapter* iotHubAdapter, const void* data, size_t dataSize) {

    bool success = true;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;

    messageHandle = IoTHubMessage_CreateFromByteArray(data, dataSize);

    if (messageHandle == NULL) {
        success = false;
        goto cleanup;
    }

    if (IoTHubMessage_SetAsSecurityMessage(messageHandle) != IOTHUB_MESSAGE_OK) {
        success = false;
        goto cleanup;
    }

    if (IoTHubModuleClient_SendEventAsync(iotHubAdapter->moduleHandle, messageHandle, SendConfirmCallback, iotHubAdapter) != IOTHUB_CLIENT_OK) {
        success = false;
        goto cleanup;
    }

cleanup:
    if (messageHandle != NULL) {
        IoTHubMessage_Destroy(messageHandle);
    }

    return success;
}

static void SendConfirmCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback) {
    if (userContextCallback == NULL) {
        //error handling
        return;
    }

    if (result != IOTHUB_CLIENT_CONFIRMATION_OK){
        //error handling
    }
}
```

#### <a name="c-api"></a>Rozhraní API C#

```cs

private static async Task SendSecurityMessageAsync(string messageContent)
{
    ModuleClient client = ModuleClient.CreateFromConnectionString("<connection_string>");
    Message  securityMessage = new Message(Encoding.UTF8.GetBytes(messageContent));
    securityMessage.SetAsSecurityMessage();
    await client.SendEventAsync(securityMessage);
}
```

#### <a name="nodejs-api"></a>Node.js API

```typescript
var Protocol = require('azure-iot-device-mqtt').Mqtt

function SendSecurityMessage(messageContent)
{
  var client = Client.fromConnectionString(connectionString, Protocol);

  var connectCallback = function (err) {
    if (err) {
      console.error('Could not connect: ' + err.message);
    } else {
      var message = new Message(messageContent);
      message.setAsSecurityMessage();
      client.sendEvent(message);
  
      client.on('error', function (err) {
        console.error(err.message);
      });
  
      client.on('disconnect', function () {
        clearInterval(sendInterval);
        client.removeAllListeners();
        client.open(connectCallback);
      });
    }
  };

  client.open(connectCallback);
}
```

#### <a name="python-api"></a>Rozhraní API pro Python

Chcete-li použít rozhraní API Pythonu, musíte nainstalovat balíček [azure-iot-device](https://pypi.org/project/azure-iot-device/).

Při použití rozhraní Python API můžete zprávu zabezpečení odeslat prostřednictvím modulu nebo prostřednictvím zařízení pomocí jedinečného připojovacího řetězce zařízení nebo modulu. Při použití následujícího příkladu skriptu Pythonu se zařízením použijte **IoTHubDeviceClient**a s modulem použijte **IoTHubModuleClient**.

```python
from azure.iot.device.aio import IoTHubDeviceClient, IoTHubModuleClient
from azure.iot.device import Message

async def send_security_message_async(message_content):
    conn_str = os.getenv("<connection_string>")
    device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)
    await device_client.connect()
    security_message = Message(message_content)
    security_message.set_as_security_message()
    await device_client.send_message(security_message)
    await device_client.disconnect()
```

#### <a name="java-api"></a>Java API

```java
public void SendSecurityMessage(string message)
{
    ModuleClient client = new ModuleClient("<connection_string>", IotHubClientProtocol.MQTT);
    Message msg = new Message(message);
    msg.setAsSecurityMessage();
    EventCallback callback = new EventCallback();
    string context = "<user_context>";
    client.sendEventAsync(msg, callback, context);
}
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si [přehled](overview.md) služby Azure Security Center for IoT
- Další informace o Azure Security Center pro [architekturu](architecture.md) IoT
- Povolení [služby](quickstart-onboard-iot-hub.md)
- Přečtěte si [nejčastější dotazy](resources-frequently-asked-questions.md)
- Zjistěte, jak získat přístup k [nezpracovaným bezpečnostním datům](how-to-security-data-access.md)
- Principy [doporučení](concept-recommendations.md)
- Principy [výstrah](concept-security-alerts.md)
