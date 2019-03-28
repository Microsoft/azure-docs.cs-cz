---
title: Odeslání zprávy o zabezpečení ASC pro náhled IoT | Dokumentace Microsoftu
description: Zjistěte, jak odeslat zabezpečení zpráv pomocí ASC pro IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: c611bb5c-b503-487f-bef4-25d8a243803d
ms.service: ascforiot
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 3daef0bfb2b9108e2b3c28ca0f6975a6f5768e92
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541553"
---
# <a name="tutorial-send-security-messages-sdk"></a>Kurz: Odesílat zprávy o zabezpečení SDK

> [!IMPORTANT]
> ASC pro IoT je aktuálně ve verzi public preview.
> Tato verze preview je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se používat pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tento kurz vysvětluje ASC pro IoT možnosti analýzy dat, pokud se rozhodnete shromažďují a zasílají zprávy o zabezpečení vašeho zařízení bez použití ASC pro agenta IoT a vysvětluje, jak postupovat.  

V tomto kurzu se naučíte: 
> [!div class="checklist"]
> * Použít pro posílání zpráv zabezpečení rozhraní APIC#
> * Použití odeslat zprávu zabezpečení rozhraní API pro C

## <a name="asc-for-iot"></a>ASC for IoT 

Zpracovávat a analyzovat všech druhů dat zabezpečení zprávy, dokud data odeslaná odpovídá ASC pro IoT [ASC pro IoT schéma](https://github.com/Azure/ASC-for-IoT-Schemas). 

## <a name="send-security-messages"></a>Odesílat zprávy o zabezpečení 

Odesílat zprávy o zabezpečení bez agenta IoT, použije ASC pomocí [zařízení Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-csharp).

K odesílání dat zařízení ze všech zařízení pro účely zpracování ASC pro IoT, použijte jednu z následujících rozhraní API k označení zprávy pro správné směrování k ASC pro IoT zpracování kanálu. Zpráv odeslaných tímto způsobem bude zpracována a zobrazí jako přehledy o zabezpečení v rámci ASC pro IoT i služby IoT Hub nebo v rámci Azure Security Center. 

Všechna data, která se odešle, i když označené správné hlavičky, musí splňovat také [ASC pro schéma zpráv IoT](https://github.com/Azure/ASC-for-IoT-Schemas). 

> [!NOTE]
> Odesílat zprávy, která není v souladu s schématu jsou ignorovány. Ujistěte se, že k ověření schématu před zahájením odesílání dat nejsou aktuálně uložené ignorovaných zpráv. 

### <a name="send-security-message-api"></a>Odeslat zprávu zabezpečení rozhraní API

**Odesílat zprávy o zabezpečení** rozhraní API je aktuálně dostupné v jazyce C a C#.  

#### <a name="c-api"></a>Rozhraní API C#

```cs
private static async Task SendSecurityMessageAsync()
{
    string messageContent = "Security Data";
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