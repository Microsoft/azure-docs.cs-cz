---
title: Správa připojení a spolehlivé zasílání zpráv pomocí sady SDK pro zařízení Azure IoT Hub
description: Zjistěte, jak zlepšit zasílání zpráv při používání sad SDK pro zařízení Azure IoT Hub a připojení k zařízení
services: iot-hub
keywords: ''
author: yzhong94
ms.author: yizhon
ms.date: 07/07/2018
ms.topic: article
ms.service: iot-hub
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: a318a1ef8b13b8fcb4f4401ac4d0e45037958d63
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2018
ms.locfileid: "39127579"
---
# <a name="how-to-manage-connectivity-and-reliable-messaging-using-azure-iot-hub-device-sdks"></a>Správa připojení a spolehlivé zasílání zpráv pomocí sady SDK pro zařízení Azure IoT Hub

Tato příručka obsahuje obecné pokyny k návrhu aplikace odolné zařízení s využitím spolehlivého zasílání zpráv funkcí a připojení sady SDK pro zařízení Azure IoT. Cílem tohoto článku je pomoct zodpovědět otázky a zpracování těchto scénářů:

- Správa připojení k síti
- Správa přepínání mezi různých síťových připojení
- Správa opětovné připojení z důvodu chyby služby přechodného připojení

Podrobnosti implementace může lišit podle jazyka naleznete v tématu propojené dokumentace k rozhraní API, nebo konkrétní sady SDK pro další podrobnosti.

- [C/Python/iOS SDK](https://github.com/azure/azure-iot-sdk-c)
- [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
- [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)
- [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)


## <a name="designing-for-resiliency"></a>Návrh zohledňující odolnost proti chybám

Zařízení IoT často využívají nesouvislé a/nebo nestabilní síťová připojení, jako je například GSM nebo satelitní. Kromě toho při interakci s cloudovým službám, může dojít k chybám kvůli dočasné situace, jako je například dostupnosti krátkodobých služby a úrovni infrastruktury chyby (obvykle označuje jako přechodné chyby). Aplikace běžící na zařízeních potřebovat spravovat připojení a mechanismy pro opětovné připojení, jakož i logika opakovaných pokusů pro odesílání nebo přijímání zprávy. Kromě toho požadavky strategie opakování závisí silně na scénář IoT, jejichž součástí zařízení a kontext zařízení a možnosti.

Sady SDK pro zařízení Azure IoT Hub za cíl zjednodušit připojení a komunikaci typu cloud zařízení a zařízení cloud tím, že poskytuje robustní a komplexní způsob připojení a posílají nebo přijímají zprávy do a ze služby Azure IoT Hub. Vývojářům můžete také upravit stávající implementaci pro vývoj strategie opakování vpravo v daném scénáři.

V následujících částech jsou zahrnuty relevantní funkce sady SDK, které podporují připojení a spolehlivé zasílání zpráv.

## <a name="connection-and-retry"></a>Připojení a zkuste to znovu

Tato část obsahuje přehled vzory opětovné připojení a zkuste to znovu, které jsou k dispozici při správě připojení, pokyny k implementaci pro použití jiné zásady opakování v zařízení aplikace a příslušná rozhraní API pro sady SDK pro zařízení.

### <a name="error-patterns"></a>Vzory chyb
Chyby připojení může dojít v mnoha úrovních:

-  Chyby sítě, jako jsou odpojené soketu a řešení chyb
- Chyby na úrovni protokolu HTTP, AMQP a protokolu MQTT přenosu, jako jsou odkazy odpojit nebo vypršela platnost relace
- Chyby na úrovni aplikace, které vyplývají z buď místní chyby, jako je neplatným přihlašovacím údajům nebo služeb chování jako překročení kvóty nebo omezení šířky pásma

Sady SDK pro zařízení zjistit chyby ve všech třech úrovních.  Chyby související s operačním systémem a hardwarových chyb nejsou zjištěny a zpracovány sad SDK pro zařízení.  Návrh vychází [The přechodné selhání zpracování pokyny](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines) z Azure Architecture Center.

### <a name="retry-patterns"></a>Zkuste vzory

Celkový proces opakovat při zjištění chyb připojení je: 
1. Sada SDK zjistí chyby a související chyby v síti, protokol nebo aplikace.
2. Podle typu chyby, sada SDK používá chyba filtrování se rozhodnout, pokud opakování musí provést.  Pokud **neopravitelné chybě** identifikovaný pomocí sady SDK operations (připojení a odeslání/přijetí) se zastaví a sady SDK uživateli oznámí. Neopravitelná chyba je chybu, která můžete identifikovat a zjistit, že nelze obnovit, například sady SDK, ověřování nebo Chyba špatné koncový bod.
3. Pokud **zotavitelné chyby** je identifikovat, sada SDK zahájí to chcete zkusit znovu, dokud definovaný časový limit vyprší platnost určené zásady opakování pomocí.
4. Když vyprší platnost definovaný časový limit, sady SDK zastaví pokusu o připojení nebo odeslat a upozorní uživatele.
5.  Tato sada SDK nabízí uživatelům připojit zpětné volání pro získání změn stavu připojení. 

Zásady tří opakování jsou k dispozici:
- **Exponenciální regrese s kolísání**: Toto je výchozí zásady opakování použít.  Je spíše agresivní na začátku, může zpomalit a pak vyšle Maximální zpoždění, které není překročen limit.  Návrh vychází [z Azure Architecture Center pokyny pro opakování](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific).
- **Vlastní opakování**: můžete implementovat vlastní zásady opakování a vložení v RetryPolicy v závislosti na jazyku, který zvolíte. Můžete navrhnout zásady opakování, která je vhodná pro váš scénář.  Toto není k dispozici na SDK pro jazyk C.
- **Žádné opakování**: je možné nastavit zásady opakování pro "žádná opakování," Zakáže logika opakovaných pokusů.  Sada SDK se pokusí připojit jednou a odešle zprávu jednou, za předpokladu, že se připojení. Tato zásada by se obvykle používá v případech, kdy existuje aspekty šířky pásma nebo nákladů.   Pokud je tato možnost zvolená, dojde ke ztrátě zpráv, které se nepodařilo odeslat a nelze jej obnovit. 

### <a name="retry-policy-apis"></a>Zásady rozhraní API pro opakování

   | Sada SDK | SetRetryPolicy – metoda | Implementace zásad | Pokyny k implementaci |
   |-----|----------------------|--|--|
   |  C/Python/iOS  | [IOTHUB_CLIENT_RESULT IoTHubClient_SetRetryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/2018-05-04/iothub_client/inc/iothub_client.h#L188)        | **Výchozí**: [IOTHUB_CLIENT_RETRY_EXPONENTIAL_BACKOFF](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Vlastní:** použijte k dispozici [retryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Žádné opakování:** [IOTHUB_CLIENT_RETRY_NONE](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)  | [Implementace jazyka C/Python/iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#)  |
   | Java| [SetRetryPolicy](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.sdk.iot.device._device_client_config.setretrypolicy?view=azure-java-stable)        | **Výchozí**: [ExponentialBackoffWithJitter třídy](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)<BR>**Vlastní:** implementovat [RetryPolicy rozhraní](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/RetryPolicy.java)<BR>**Žádné opakování:** [třída NoRetry](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)  | [Implementace jazyka Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md) |[.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
   | .NET| [DeviceClient.SetRetryPolicy](/dotnet/api/microsoft.azure.devices.client.deviceclient.setretrypolicy?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_SetRetryPolicy_Microsoft_Azure_Devices_Client_IRetryPolicy) | **Výchozí**: [ExponentialBackoff třídy](/dotnet/api/microsoft.azure.devices.client.exponentialbackoff?view=azure-dotnet)<BR>**Vlastní:** implementovat [IRetryPolicy rozhraní](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.iretrypolicy?view=azure-dotnet)<BR>**Žádné opakování:** [třída NoRetry](/dotnet/api/microsoft.azure.devices.client.noretry?view=azure-dotnet) | [Implementace jazyka C#]() |
   | Node| [setRetryPolicy](/javascript/api/azure-iot-device/client?view=azure-iot-typescript-latest#azure_iot_device_Client_setRetryPolicy) | **Výchozí**: [ExponentialBackoffWithJitter třídy](/javascript/api/azure-iot-common/exponentialbackoffwithjitter?view=azure-iot-typescript-latest)<BR>**Vlastní:** implementovat [RetryPolicy rozhraní](/javascript/api/azure-iot-common/retrypolicy?view=azure-iot-typescript-latest)<BR>**Žádné opakování:** [třída NoRetry](/javascript/api/azure-iot-common/noretry?view=azure-iot-typescript-latest) | [Implementace uzlu](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them) |
   

Níže jsou uvedeny ukázky kódu, které ilustrují tento tok. 

#### <a name="net-implementation-guidance"></a>Pokyny k implementaci rozhraní .NET

Následující vzorový kód ukazuje, jak definovat a nastavit výchozí zásady opakování:

   ```csharp
   # define/set default retry policy
   RetryPolicy retryPolicy = new ExponentialBackoff(int.MaxValue, TimeSpan.FromMilliseconds(100), TimeSpan.FromSeconds(10), TimeSpan.FromMilliseconds(100));
   SetRetryPolicy(retryPolicy);
   ```

Aby se zabránilo vysoké využití procesoru, opakované pokusy se omezují selže okamžitě (například když neexistuje žádné sítě nebo trasa do cíle) kód tak, aby minimální dobu pro spuštění na další opakování je 1 sekunda. 

Pokud služba reaguje omezení chybou, zásady opakování se liší a nelze změnit prostřednictvím veřejného rozhraní API:

   ```csharp
   # throttled retry policy
   RetryPolicy retryPolicy = new ExponentialBackoff(RetryCount, TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(60), TimeSpan.FromSeconds(5));
   SetRetryPolicy(retryPolicy);
   ```

Mechanismus opakování se zastaví po `DefaultOperationTimeoutInMilliseconds`, který je aktuálně nastaven na 4 minuty.

#### <a name="other-languages-implementation-guidance"></a>Pokyny k implementaci dalších jazyků
V jiných jazycích najdete v dokumentaci implementace níže.  Ukázky používání zásady opakování, rozhraní API jsou k dispozici v úložišti.
- [C/Python/iOS SDK](https://github.com/azure/azure-iot-sdk-c)
- [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
- [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)
- [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

