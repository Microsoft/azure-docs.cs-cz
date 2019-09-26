---
title: Odeslání zpráv zabezpečení Azure Security Center pro IoT | Microsoft Docs
description: Naučte se odesílat zprávy zabezpečení pomocí Azure Security Center pro IoT.
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
ms.date: 09/26/2019
ms.author: mlottner
ms.openlocfilehash: b291e2392f0756cb2d3ec294db37206d32216959
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315926"
---
# <a name="send-security-messages-sdk"></a>Odeslat sadu SDK pro zprávy zabezpečení

Tato příručka vysvětluje Azure Security Center možnosti služby IoT, když se rozhodnete shromažďovat a odesílat zprávy o zabezpečení zařízení bez použití Azure Security Center pro agenta IoT a vysvětluje, jak to udělat.  

V této příručce se naučíte: 
> [!div class="checklist"]
> * Posílání zpráv o zabezpečení pomocí sady Azure IoT C SDK
> * Posílání zpráv o zabezpečení pomocí sady C# Azure IoT SDK
> * Posílání zpráv o zabezpečení pomocí sady SDK Azure IoT Python
> * Posílání zpráv o zabezpečení pomocí sady Azure IoT Node. js SDK
> * Posílání zpráv o zabezpečení pomocí sady Azure IoT Java SDK


## <a name="azure-security-center-for-iot-capabilities"></a>Azure Security Center pro možnosti IoT

Azure Security Center pro IoT může zpracovávat a analyzovat jakýkoli druh dat zprávy zabezpečení, pokud se odesílají data v souladu se [schématem Azure Security Center pro IoT](https://aka.ms/iot-security-schemas) a zpráva je nastavena jako bezpečnostní zpráva.

## <a name="security-message"></a>Zpráva zabezpečení

Azure Security Center pro IoT definuje bezpečnostní zprávu pomocí následujících kritérií:
- Pokud byla zpráva odeslána pomocí sady Azure IoT SDK
- Pokud zpráva odpovídá [schématu zprávy zabezpečení](https://aka.ms/iot-security-schemas)
- Pokud byla zpráva před odesláním nastavena jako bezpečnostní zpráva

Každá zpráva zabezpečení obsahuje metadata odesílatele `AgentId`, jako je, `AgentVersion`, `MessageSchemaVersion` a seznam událostí zabezpečení.
Schéma definuje platné a požadované vlastnosti zprávy zabezpečení, včetně typů událostí.

>[!Note]
> Zprávy odeslané v nedodržení schématu jsou ignorovány. Před zahájením odesílání dat Nezapomeňte ověřit schéma, protože ignorované zprávy se momentálně neukládají. 

>[!Note]
> Zprávy odeslané jako bezpečnostní zpráva s použitím sady Azure IoT SDK nebudou směrovány do Azure Security Center pro kanál IoT.

## <a name="valid-message-example"></a>Příklad platné zprávy

Následující příklad ukazuje platný objekt zprávy zabezpečení. Příklad obsahuje metadata zprávy a jednu `ProcessCreate` událost zabezpečení.

Po nastavení zprávy o zabezpečení a odeslání bude tato zpráva zpracována Azure Security Center pro IoT.

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

## <a name="send-security-messages"></a>Odeslat zprávy zabezpečení 

Posílání zpráv o zabezpečení *bez* použití Azure Security Center pro agenta IoT pomocí [sady SDK pro zařízení Azure IoT C](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview), [sady C# SDK pro zařízení Azure](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview)IoT, Azure IoT [Node. js SDK](https://github.com/Azure/azure-iot-sdk-node), [Azure IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python)nebo [Azure IoT Java SDK ](https://github.com/Azure/azure-iot-sdk-java).

Pokud chcete odesílat data zařízení z vašich zařízení ke zpracování Azure Security Center pro IoT, použijte jedno z následujících rozhraní API k označení zpráv pro správné směrování do Azure Security Center kanálu zpracování IoT. 

Všechna data, která jsou odeslána, i když jsou označena správnou hlavičkou, musí také vyhovovat [Azure Security Center schématu zpráv IoT](https://aka.ms/iot-security-schemas). 

### <a name="send-security-message-api"></a>Poslat rozhraní API pro zprávy zabezpečení 

Rozhraní API pro **odesílání zpráv zabezpečení** je aktuálně k dispozici C#v jazycích C a, Python, Node. js a Java.  

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
#### <a name="nodejs-api"></a>Rozhraní API pro Node. js

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

```python
async def send_security_message_async(message_content):
    conn_str = os.getenv("<connection_string>")
    device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)
    await device_client.connect()
    security_message = Message(message_content)
    security_message.set_as_security_message()
    await device_client.send_d2c_message(security_message)
    await device_client.disconnect()
```



## <a name="next-steps"></a>Další kroky
- Přečtěte si [Přehled](overview.md) služby Azure Security Center for IoT.
- Další informace o [architektuře](architecture.md) Azure Security Center pro IoT
- Povolení [služby](quickstart-onboard-iot-hub.md)
- Přečtěte si [Nejčastější dotazy](resources-frequently-asked-questions.md) .
- Přečtěte si, jak získat přístup k nezpracovaným [datům zabezpečení](how-to-security-data-access.md)
- Vysvětlení [doporučení](concept-recommendations.md)
- Vysvětlení [výstrah](concept-security-alerts.md)
