---
title: Odeslání zprávy o zabezpečení Azure Security Center pro IoT ve verzi Preview | Dokumentace Microsoftu
description: Zjistěte, jak odeslat zabezpečení zprávy pomocí Azure Security Center pro IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: c611bb5c-b503-487f-bef4-25d8a243803d
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: a9974fd15ae9c8c420992c3ae1084feebae0f57d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797342"
---
# <a name="send-security-messages-sdk"></a>Odesílat zprávy o zabezpečení SDK

> [!IMPORTANT]
> Azure Security Center pro IoT je aktuálně ve verzi public preview.
> Tato verze preview je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se používat pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tato příručka vysvětluje Azure Security Center (ASC) pro možnosti služby IoT, pokud budete chtít shromažďují a zasílají zprávy o zabezpečení vašeho zařízení bez použití ASC pro agenta IoT a vysvětluje, jak postupovat.  

V této příručce se naučíte: 
> [!div class="checklist"]
> * Použít pro posílání zpráv zabezpečení rozhraní APIC#
> * Použití odeslat zprávu zabezpečení rozhraní API pro C

## <a name="asc-for-iot-capabilities"></a>ASC pro schopnosti IoT

Zpracovávat a analyzovat všech druhů dat zabezpečení zprávy, dokud data odeslaná odpovídá ASC pro IoT [ASC pro IoT schéma](https://aka.ms/iot-security-schemas) a zpráva je nastaven jako zabezpečení zpráv.

## <a name="security-message"></a>Zabezpečení zpráv

ASC pro IoT definuje zabezpečení zpráv pomocí následujících kritérií:
- Pokud zpráva byla odeslána s Azure IoT C /C# SDK
- Pokud zpráva odpovídá [schématu zabezpečení zpráv](https://aka.ms/iot-security-schemas)
- Pokud zpráva byla nastavena na zabezpečení zprávy před odesláním

Každá zpráva o zabezpečení, jako zahrnuje jejich metadata odesílatele `AgentId`, `AgentVersion`, `MessageSchemaVersion` a seznam událostí zabezpečení.
Schéma definuje platné a požadované vlastnosti zabezpečení zprávy, včetně typů událostí.

[!NOTE]
> Odesílat zprávy, která není v souladu s schématu jsou ignorovány. Ujistěte se, že k ověření schématu před zahájením odesílání dat nejsou aktuálně uložené ignorovaných zpráv. 
> Odesílat zprávy, které nebyly nastaveny jako zabezpečení zpráv pomocí Azure IoT C /C# SDK nebude možné směrovat do ASC pro IoT kanálu

## <a name="valid-message-example"></a>Příklad platná zpráva

Následující příklad ukazuje objekt zprávy platné zabezpečení. Tento příklad obsahuje metadata zprávy a jeden `ProcessCreate` událostí zabezpečení.

Po nastavení jako zabezpečení zpráv a odeslat, tato zpráva se zpracuje ASC pro IoT.

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
                    "Executable": "/usr/bin/echo",
                    "ProcessId": 11750,
                    "ParentProcessId": 1593,
                    "UserName": "nginx",
                    "CommandLine": "./backup .htaccess"
                }
            ]
    }
]
```

## <a name="send-security-messages"></a>Odesílat zprávy o zabezpečení 

Odesílat zprávy o zabezpečení bez agenta IoT, použije ASC pomocí [Azure IoT C# sada SDK zařízení](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview) nebo [zařízení Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview).

K odesílání dat zařízení ze všech zařízení pro účely zpracování ASC pro IoT, použijte jednu z následujících rozhraní API k označení zprávy pro správné směrování k ASC pro IoT zpracování kanálu. Zpráv odeslaných tímto způsobem bude zpracována a zobrazí jako přehledy o zabezpečení v rámci ASC pro IoT i služby IoT Hub nebo v rámci Azure Security Center. 

Všechna data, která se odešle, i když označené správné hlavičky, musí splňovat také [ASC pro schéma zpráv IoT](https://aka.ms/iot-security-schemas). 

### <a name="send-security-message-api"></a>Odeslat zprávu zabezpečení rozhraní API

**Odesílat zprávy o zabezpečení** rozhraní API je aktuálně dostupné v jazyce C a C#.  

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

## <a name="next-steps"></a>Další postup
- Přečtěte si ASC pro služby Azure IoT [– přehled](overview.md)
- Další informace o ASC pro IoT [architektury](architecture.md)
- Povolit [služby](quickstart-onboard-iot-hub.md)
- Přečtěte si [– nejčastější dotazy](resources-frequently-asked-questions.md)
- Zjistěte, jak získat přístup k [nezpracovaná zabezpečení dat](how-to-security-data-access.md)
- Vysvětlení [doporučení](concept-recommendations.md)
- Vysvětlení [výstrahy](concept-security-alerts.md)
