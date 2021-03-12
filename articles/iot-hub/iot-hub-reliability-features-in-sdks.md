---
title: Správa připojení IoT Hub & spolehlivé zasílání zpráv s SDK pro zařízení
description: Zjistěte, jak vylepšit připojení zařízení a zprávy při používání sad SDK pro zařízení Azure IoT Hub.
services: iot-hub
author: robinsh
ms.author: robinsh
ms.date: 07/07/2018
ms.topic: article
ms.service: iot-hub
ms.custom:
- amqp
- mqtt
- devx-track-csharp
ms.openlocfilehash: efd16f0e8dd6ba952b647fbfbd6f35fc05ffd4be
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102615835"
---
# <a name="manage-connectivity-and-reliable-messaging-by-using-azure-iot-hub-device-sdks"></a>Správa připojení a spolehlivého zasílání zpráv pomocí sad SDK pro zařízení Azure IoT Hub

Tento článek poskytuje podrobné pokyny, které vám pomůžou navrhovat aplikace pro zařízení, které jsou odolnější. Dozvíte se, jak využít výhod funkcí připojení a spolehlivého zasílání zpráv v sadách SDK pro zařízení Azure IoT. Cílem tohoto průvodce je pomáhat při správě následujících scénářů:

* Oprava vyřazeného síťového připojení

* Přepínání mezi různými síťovými připojeními

* Opětovné připojení kvůli chybám přechodného připojení služby

Podrobnosti implementace se můžou lišit podle jazyka. Další informace najdete v dokumentaci k rozhraní API nebo v konkrétní sadě SDK:

* [Sada C/iOS SDK](https://github.com/azure/azure-iot-sdk-c)

* [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/retrypolicy.md)

* [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

* [Python SDK](https://github.com/Azure/azure-iot-sdk-python) (ještě není naimplementovaná spolehlivost)

## <a name="designing-for-resiliency"></a>Návrh zohledňující odolnost proti chybám

Zařízení IoT často spoléhají na nesouvislá nebo nestabilní síťová připojení (například GSM nebo satelitní). K chybám může docházet, když zařízení komunikují s cloudovým službám kvůli občasné dostupnosti služeb a na úrovni infrastruktury nebo přechodným chybám. Aplikace, která běží na zařízení, musí spravovat mechanismy pro připojení, opětovné připojení a logiku opakování pro odesílání a příjem zpráv. Požadavky na strategii opakování jsou také silně závislé na scénáři IoT, kontextu a funkcích zařízení.

Sady SDK pro zařízení Azure IoT Hub mají za cíl zjednodušit připojení a komunikaci z cloudu do zařízení a ze zařízení do cloudu. Tyto sady SDK poskytují robustní způsob připojení k Azure IoT Hub a komplexní sadu možností pro posílání a přijímání zpráv. Vývojáři mohou také upravit existující implementaci a přizpůsobit tak lepší strategii opakování pro daný scénář.

V následujících částech jsou uvedeny příslušné funkce sady SDK, které podporují připojení a spolehlivé zasílání zpráv.

## <a name="connection-and-retry"></a>Připojení a zkuste to znovu.

Tato část poskytuje přehled o opětovném připojení a vzorech opakování, které jsou k dispozici při správě připojení. Podrobně popisuje pokyny k implementaci pro použití různých zásad opakování v aplikaci zařízení a seznam relevantních rozhraní API ze sad SDK pro zařízení.

### <a name="error-patterns"></a>Vzorce chyb

Selhání připojení může probíhat na mnoha úrovních:

* Chyby sítě: odpojené sokety a chyby překladu názvů

* Chyby na úrovni protokolu pro přenos HTTP, AMQP a MQTT: odpojené odkazy nebo relace s vypršenou platností

* Chyby na úrovni aplikace, které jsou výsledkem buď místních chyb: neplatné přihlašovací údaje nebo chování služby (například překročení kvóty nebo omezení)

Sady SDK pro zařízení zjišťují chyby na všech třech úrovních. Chyby související s operačním systémem a hardwarové chyby nejsou zjištěny a zpracovávány sadami SDK pro zařízení. Návrh SDK je založený na [pokynech pro zpracování přechodných chyb](/azure/architecture/best-practices/transient-faults#general-guidelines) z cetrum architektury Azure.

### <a name="retry-patterns"></a>Vzory opakování

Následující kroky popisují proces opakování při zjištění chyb připojení:

1. Sada SDK detekuje chybu a přidruženou chybu v síti, protokolu nebo aplikaci.

2. Sada SDK používá filtr chyb k určení typu chyby a rozhodne, zda je opakování požadováno.

3. Pokud sada SDK identifikuje **neodstranitelnou chybu**, operace jako připojení, odeslání a příjem se zastaví. Sada SDK upozorní uživatele. Mezi příklady neodstranitelné chyby patří chyba ověřování a chybná chyba koncového bodu.

4. Pokud sada SDK identifikuje **neodstranitelnou chybu**, opakuje se podle zadaných zásad opakování, dokud neuplyne stanovený časový limit.  Všimněte si, že sada SDK ve výchozím nastavení používá **exponenciální back-vypnuté zásady pro opakování chvění** .
5. Po vypršení časového limitu se sada SDK přestane pokoušet připojit nebo odeslat. Upozorní uživatele.

6. Sada SDK umožňuje uživateli připojit zpětné volání pro příjem změn stavu připojení.

Sady SDK poskytují tři zásady opakování:

* **Exponenciální regrese se kolísáním**: tyto výchozí zásady opakování mají na začátku hodnotu agresivní a zpomalit v čase, dokud nedosáhne maximálního zpoždění. Návrh je založen na [pokynech pro opakování z cetrum architektury Azure](/azure/architecture/best-practices/retry-service-specific). 

* **Vlastní opakování**: u některých jazyků SDK můžete navrhnout vlastní zásady opakování, které jsou vhodnější pro váš scénář, a pak je vložit do RetryPolicy. Vlastní opakování není v sadě C SDK k dispozici a v současnosti není podporována v sadě Python SDK. Sada Python SDK se znovu připojí podle potřeby.

* **Bez opakování**: zásady opakování můžete nastavit na "bez opakování", což zakáže logiku opakování. Sada SDK se pokusí připojit jednou a pošle zprávu jednou za předpokladu, že je připojení navázáno. Tyto zásady se obvykle používají ve scénářích se šířkou pásma nebo náklady. Pokud zvolíte tuto možnost, ztratí se zprávy, které se nepodařilo odeslat, a nelze je obnovit.

### <a name="retry-policy-apis"></a>Rozhraní API zásad opakování

   | Sada SDK | Metoda SetRetryPolicy | Implementace zásad | Pokyny k implementaci |
   |-----|----------------------|--|--|
   |  C/iOS  | [IOTHUB_CLIENT_RESULT IoTHubClient_SetRetryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/2018-05-04/iothub_client/inc/iothub_client.h#L188)        | **Výchozí**: [IOTHUB_CLIENT_RETRY_EXPONENTIAL_BACKOFF](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Vlastní:** použijte dostupné [retryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Bez opakování:** [IOTHUB_CLIENT_RETRY_NONE](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)  | [Implementace C/iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#)  |
   | Java| [SetRetryPolicy](/java/api/com.microsoft.azure.sdk.iot.device.deviceclientconfig.setretrypolicy)        | **Výchozí**: [Třída ExponentialBackoffWithJitter](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)<BR>**Vlastní:** implementace [rozhraní RetryPolicy](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/RetryPolicy.java)<BR>**Bez opakování:** [Třída neopakovat](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)  | [Implementace Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md) |
   | .NET| [DeviceClient. SetRetryPolicy](/dotnet/api/microsoft.azure.devices.client.deviceclient.setretrypolicy) | **Výchozí**: [Třída ExponentialBackoff](/dotnet/api/microsoft.azure.devices.client.exponentialbackoff)<BR>**Vlastní:** implementace [rozhraní IRetryPolicy](/dotnet/api/microsoft.azure.devices.client.iretrypolicy)<BR>**Bez opakování:** [Třída neopakovat](/dotnet/api/microsoft.azure.devices.client.noretry) | [Implementace jazyka C#](https://github.com/Azure/azure-iot-sdk-csharp) | |
   | Uzel| [setRetryPolicy](/javascript/api/azure-iot-device/client) | **Výchozí**: [Třída ExponentialBackoffWithJitter](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java) | [Implementace uzlu](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them) |
   | Python| Aktuálně se nepodporuje. | Aktuálně se nepodporuje. | Aktuálně se nepodporuje. |

Následující ukázky kódu ilustrují tento tok:

#### <a name="net-implementation-guidance"></a>Pokyny k implementaci rozhraní .NET

Následující ukázka kódu ukazuje, jak definovat a nastavit výchozí zásady opakování:

   ```csharp
   // define/set default retry policy
   IRetryPolicy retryPolicy = new ExponentialBackoff(int.MaxValue, TimeSpan.FromMilliseconds(100), TimeSpan.FromSeconds(10), TimeSpan.FromMilliseconds(100));
   SetRetryPolicy(retryPolicy);
   ```

Aby se předešlo vysokému využití procesoru, jsou opakované pokusy omezené, pokud se kód okamžitě nezdařil. Například pokud není k dispozici žádná síť nebo trasa k cíli. Minimální čas, kdy se má spustit další pokus, je 1 sekunda.

Pokud služba odpoví s chybou omezení, zásady opakování se liší a nelze je změnit prostřednictvím veřejného rozhraní API:

   ```csharp
   // throttled retry policy
   IRetryPolicy retryPolicy = new ExponentialBackoff(RetryCount, TimeSpan.FromSeconds(10), 
     TimeSpan.FromSeconds(60), TimeSpan.FromSeconds(5)); SetRetryPolicy(retryPolicy);
   ```

Mechanismus opakování se zastaví po `DefaultOperationTimeoutInMilliseconds` , který je aktuálně nastavený na 4 minuty.

#### <a name="other-languages-implementation-guidance"></a>Průvodce implementací dalších jazyků

Ukázky kódu v jiných jazycích najdete v následujících implementačních dokumentech. Úložiště obsahuje ukázky, které ukazují použití rozhraní API zásad opakování.

* [Sada C/iOS SDK](https://github.com/azure/azure-iot-sdk-c)

* [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/retrypolicy.md)

* [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

* [Python SDK](https://github.com/Azure/azure-iot-sdk-python) (ještě není naimplementovaná spolehlivost)

## <a name="next-steps"></a>Další kroky

* [Použití sad SDK pro zařízení a služby](./iot-hub-devguide-sdks.md)

* [Použití sady SDK pro zařízení IoT pro C](./iot-hub-device-sdk-c-intro.md)

* [Vývoj pro zařízení s omezením](./iot-hub-devguide-develop-for-constrained-devices.md)

* [Vývoj pro mobilní zařízení](./iot-hub-how-to-develop-for-mobile-devices.md)

* [Řešení potíží s odpojením zařízení](iot-hub-troubleshoot-connectivity.md)