---
title: Správa připojení a spolehlivé zasílání zpráv s použitím sady SDK pro zařízení Azure IoT Hub
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
ms.openlocfilehash: cf8db02bca5e7fb8c206f23d6e13aa3eec6f2267
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54845805"
---
# <a name="manage-connectivity-and-reliable-messaging-by-using-azure-iot-hub-device-sdks"></a>Spravovat připojení a spolehlivé zasílání zpráv s použitím sady SDK pro zařízení Azure IoT Hub

Tento článek obsahuje základní pokyny k návrhu aplikací zařízení, které jsou odolnější. To ukazuje, jak využít výhod připojení a spolehlivé zasílání zpráv funkcí sady SDK pro zařízení Azure IoT. Cílem této příručky je vám pomohou spravovat následující scénáře:

- Opravit připojení k síti
- Přepínání mezi různých síťových připojení
- Opětovné připojení z důvodu chyby služby přechodného připojení

Podrobnosti implementace může lišit podle jazyka. Další informace najdete v dokumentaci k rozhraní API nebo konkrétní sady SDK:

- [C/Python/iOS SDK](https://github.com/azure/azure-iot-sdk-c)
- [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
- [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)
- [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

## <a name="designing-for-resiliency"></a>Návrh zohledňující odolnost proti chybám

Zařízení IoT často využívají nesouvislé nebo nestabilní připojení k síti (například GSM nebo satelitní). Zařízení pracovat s cloudovým službám z důvodu chyby dostupnosti a úrovni infrastruktury nebo přechodný přerušované služby může dojít k chybě. Aplikace, která běží na zařízení má ke správě mechanismy pro připojení, opětovné připojení a logika opakovaných pokusů pro odesílání a příjem zpráv. Navíc požadavky strategie opakování závisí silně na scénář IoT zařízení, kontext, možnosti.

Sady SDK pro zařízení Azure IoT Hub za cíl zjednodušit připojení a komunikaci typu cloud zařízení a zařízení cloud. Tyto sady SDK poskytují robustní způsob připojení k Azure IoT Hub a komplexní sadu možností pro odesílání a příjem zpráv. Vývojářům můžete také upravit stávající implementaci přizpůsobení lepší strategie opakování v daném scénáři.

V následujících částech jsou zahrnuty relevantní funkce sady SDK, které podporují připojení a spolehlivé zasílání zpráv.

## <a name="connection-and-retry"></a>Připojení a zkuste to znovu

Tato část poskytuje přehled o opětovné připojení a zkuste to znovu vzorky k dispozici při správě připojení. Podrobnosti o pokyny k implementaci pro použití jiné zásady opakování v zařízení aplikaci a uvádí relevantní rozhraní API sady SDK pro zařízení.

### <a name="error-patterns"></a>Vzory chyb
Selhání připojení můžou probíhat současně mnoho úrovní:

- Chyby sítě: odpojení soketu a název řešení chyb
- Úroveň protokolu chyby pro přenos HTTP, AMQP a protokolu MQTT: odpojit, odkazy nebo relace s vypršelou platností
- Chyby na úrovni aplikace, které jsou výsledkem buď místní chyb: Neplatné přihlašovací údaje nebo chování služby (například přesahuje kvótu nebo omezení)

Sady SDK pro zařízení zjistit chyby ve všech třech úrovních. Chyby související s operačním systémem a hardwarových chyb nejsou zjištěny a zpracovány sad SDK pro zařízení. Sada SDK design je založen na [The přechodné selhání zpracování pokyny](/azure/architecture/best-practices/transient-faults#general-guidelines) z Azure Architecture Center.

### <a name="retry-patterns"></a>Zkuste vzory

Následující kroky popisují proces opakovat při zjištění chyb připojení:

1. Sada SDK zjistí chyby a související chybu v síti, protokol nebo aplikace.
1. Sada SDK využívá filtr chyby určete typ chyby a rozhodněte, pokud je potřeba zkuste to znovu.
1. Pokud sada SDK identifikuje **neopravitelné chybě**, operace, jako je připojení, odesílat a přijímat je zastavené. Sadu SDK upozorní uživatele. Mezi příklady neopravitelným chybám patří chybu ověřování a chyba špatné koncový bod.
1. Pokud sada SDK identifikuje **zotavitelné chyby**, opakování podle zásady opakovaných pokusů zadaný, až uplyne definovaný časový limit.  Všimněte si, že sada SDK používá **exponenciální regresní s kolísání** zásady opakování ve výchozím nastavení.
1. Když definovaný časový limit vyprší, zastaví sady SDK, pokusu o připojení nebo odeslání. Upozorní uživatele.
1. Tato sada SDK nabízí uživatelům připojit zpětné volání pro získání změn stavu připojení.

Sady SDK poskytují že tři zásady opakování:

- **Exponenciální regrese s kolísání**: Tento výchozí zásady opakování je spíše agresivní na začátku a zpomalit v čase, dokud nedosáhne maximální zpoždění. Návrh vychází [z Azure Architecture Center pokyny pro opakování](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific). 
- **Vlastní opakování**: Pro některé jazyky sady SDK můžete navrhnout vlastní zásady opakování, který je vhodnější pro váš scénář a vložit ho do RetryPolicy. Vlastní opakování není k dispozici na SDK pro jazyk C.
- **Žádné opakování**: Můžete nastavit zásady opakování pro "žádná opakování," Zakáže logika opakovaných pokusů. Sada SDK se pokusí připojit jednou a odešle zprávu jednou, za předpokladu, že se připojení. Tyto zásady se obvykle používá ve scénářích s aspekty šířky pásma nebo nákladů. Pokud zvolíte tuto možnost, dojde ke ztrátě zpráv, které se nepodařilo odeslat, který nepůjde obnovit.

### <a name="retry-policy-apis"></a>Zásady rozhraní API pro opakování

   | Sada SDK | SetRetryPolicy – metoda | Implementace zásad | Pokyny k implementaci |
   |-----|----------------------|--|--|
   |  C/Python/iOS  | [IOTHUB_CLIENT_RESULT IoTHubClient_SetRetryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/2018-05-04/iothub_client/inc/iothub_client.h#L188)        | **Výchozí**: [IOTHUB_CLIENT_RETRY_EXPONENTIAL_BACKOFF](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Vlastní:** použijte k dispozici [retryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Žádné opakování:** [IOTHUB_CLIENT_RETRY_NONE](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)  | [Implementace jazyka C/Python/iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#)  |
   | Java| [SetRetryPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.deviceclientconfig.setretrypolicy?view=azure-java-stable)        | **Výchozí**: [Třída ExponentialBackoffWithJitter](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)<BR>**Vlastní:** implementovat [RetryPolicy rozhraní](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/RetryPolicy.java)<BR>**Žádné opakování:** [Třída NoRetry](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)  | [Implementace jazyka Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md) |[.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
   | .NET| [DeviceClient.SetRetryPolicy](/dotnet/api/microsoft.azure.devices.client.deviceclient.setretrypolicy?view=azure-dotnet) | **Výchozí**: [Třída ExponentialBackoff](/dotnet/api/microsoft.azure.devices.client.exponentialbackoff?view=azure-dotnet)<BR>**Vlastní:** implementovat [IRetryPolicy rozhraní](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.iretrypolicy?view=azure-dotnet)<BR>**Žádné opakování:** [Třída NoRetry](/dotnet/api/microsoft.azure.devices.client.noretry?view=azure-dotnet) | [Implementace jazyka C#](https://github.com/Azure/azure-iot-sdk-csharp) |
   | Node| [setRetryPolicy](/javascript/api/azure-iot-device/client?view=azure-iot-typescript-latest#azure_iot_device_Client_setRetryPolicy) | **Výchozí**: [Třída ExponentialBackoffWithJitter](/javascript/api/azure-iot-common/exponentialbackoffwithjitter?view=azure-iot-typescript-latest)<BR>**Vlastní:** implementovat [RetryPolicy rozhraní](/javascript/api/azure-iot-common/retrypolicy?view=azure-iot-typescript-latest)<BR>**Žádné opakování:** [Třída NoRetry](/javascript/api/azure-iot-common/noretry?view=azure-iot-typescript-latest) | [Implementace uzlu](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them) |

Následující ukázky kódu znázorňují tento tok:

#### <a name="net-implementation-guidance"></a>Pokyny k implementaci rozhraní .NET

Následující příklad kódu ukazuje, jak definovat a nastavit výchozí zásady opakování:

   ```csharp
   # define/set default retry policy
   RetryPolicy retryPolicy = new ExponentialBackoff(int.MaxValue, TimeSpan.FromMilliseconds(100), TimeSpan.FromSeconds(10), TimeSpan.FromMilliseconds(100));
   SetRetryPolicy(retryPolicy);
   ```

Aby se zabránilo vysoké využití procesoru, opakované pokusy se omezují, pokud kód selže okamžitě. Například, pokud neexistuje žádné sítě nebo trasa do cíle. Minimální čas ke spuštění na další opakování je 1 sekunda.

Pokud služba jako odpověď vrátí chybu omezení, zásady opakování se liší a nelze změnit prostřednictvím veřejného rozhraní API:

   ```csharp
   # throttled retry policy
   RetryPolicy retryPolicy = new ExponentialBackoff(RetryCount, TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(60), TimeSpan.FromSeconds(5));
   SetRetryPolicy(retryPolicy);
   ```

Mechanismus opakování po zastaví `DefaultOperationTimeoutInMilliseconds`, který je aktuálně nastaven na 4 minuty.

#### <a name="other-languages-implementation-guidance"></a>Pokyny k implementaci dalších jazyků

Ukázky kódu v jiných jazycích přečtěte si následující dokumenty, implementace. Úložiště obsahuje ukázky, které demonstrují použití zásady opakování rozhraní API.

- [C/Python/iOS SDK](https://github.com/azure/azure-iot-sdk-c)
- [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
- [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)
- [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

## <a name="next-steps"></a>Další postup
- [Použití sad SDK pro zařízení a služby](./iot-hub-devguide-sdks.md)
- [Použití sady SDK pro zařízení IoT pro C](./iot-hub-device-sdk-c-intro.md)
- [Vývoj pro zařízení s omezením](./iot-hub-devguide-develop-for-constrained-devices.md)
- [Vývoj pro mobilní zařízení](./iot-hub-how-to-develop-for-mobile-devices.md)
- [Řešení potíží s odpojí zařízení](iot-hub-troubleshoot-connectivity.md)
