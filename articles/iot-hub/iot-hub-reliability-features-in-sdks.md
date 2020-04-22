---
title: Správa připojení služby IoT Hub & spolehlivé zasílání zpráv w/zařízení sady SDK
description: Zjistěte, jak zlepšit připojení a zasílání zpráv zařízení při používání sad SDK zařízení Azure IoT Hub
services: iot-hub
author: robinsh
ms.author: robinsh
ms.date: 07/07/2018
ms.topic: article
ms.service: iot-hub
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: a645ab1fa4a1be3ec8e939452a1457e84bbafe15
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759674"
---
# <a name="manage-connectivity-and-reliable-messaging-by-using-azure-iot-hub-device-sdks"></a>Správa připojení a spolehlivého zasílání zpráv pomocí sad SDK zařízení Azure IoT Hub

Tento článek obsahuje pokyny na vysoké úrovni, které vám pomohou navrhnout aplikace zařízení, které jsou odolnější. Ukazuje, jak využít výhod připojení a spolehlivé funkce zasílání zpráv v sadách SDK zařízení Azure IoT. Cílem této příručky je pomoci vám spravovat následující scénáře:

* Oprava zrušeného síťového připojení

* Přepínání mezi různými síťovými připojeními

* Opětovné připojení z důvodu chyb přechodného připojení služby

Podrobnosti implementace se mohou lišit podle jazyka. Další informace naleznete v dokumentaci k rozhraní API nebo v konkrétní sdk:

* [C/iOS SDK](https://github.com/azure/azure-iot-sdk-c)

* [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/retrypolicy.md)

* [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

* [Python SDK](https://github.com/Azure/azure-iot-sdk-python) (Spolehlivost ještě není implementována)

## <a name="designing-for-resiliency"></a>Návrh zohledňující odolnost proti chybám

Zařízení IoT se často spoléhají na nespojitá nebo nestabilní síťová připojení (například GSM nebo satelit). K chybám může dojít, když zařízení interagují s cloudovými službami z důvodu občasné dostupnosti služby a chyb na úrovni infrastruktury nebo přechodných chyb. Aplikace, která běží na zařízení má spravovat mechanismy pro připojení, opětovné připojení a logiku opakování pro odesílání a přijímání zpráv. Požadavky strategie opakování také závisí do značné míry na scénáři ioT zařízení, kontextu, možnostech.

Sady SDK zařízení Azure IoT Hub mají za cíl zjednodušit připojení a komunikaci z cloudu na zařízení a zařízení do cloudu. Tyto sady SDK poskytují robustní způsob připojení k azure iot hub a komplexní sadu možností pro odesílání a přijímání zpráv. Vývojáři mohou také upravit existující implementaci přizpůsobit lepší strategii opakování pro daný scénář.

Příslušné funkce sady SDK, které podporují připojení a spolehlivé zasílání zpráv, jsou popsány v následujících částech.

## <a name="connection-and-retry"></a>Připojení a opakování

Tato část poskytuje přehled o opětovnépřipojení a opakování vzory k dispozici při správě připojení. Podrobně popisuje pokyny k implementaci pro použití různých zásad opakování v aplikaci zařízení a uvádí příslušná rozhraní API ze sad SDK zařízení.

### <a name="error-patterns"></a>Vzory chyb

K selhání připojení může dojít na mnoha úrovních:

* Chyby sítě: odpojené sokety a chyby překladu názvů

* Chyby na úrovni protokolu pro přenos y HTTP, AMQP a MQTT: odpojené odkazy nebo relace s ukončenou platností

* Chyby na úrovni aplikace, které vyplývají z místních chyb: neplatná pověření nebo chování služby (například překročení kvóty nebo omezení)

Sady SDK zařízení detekují chyby na všech třech úrovních. Sady SDK související s oS a hardwarovými chybami nejsou rozpoznány a zpracovány sadami SDK. Návrh sady SDK je založen na [příkazech pro zpracování přechodných chyb](/azure/architecture/best-practices/transient-faults#general-guidelines) z Centra architektury Azure.

### <a name="retry-patterns"></a>Opakování vzorků

Následující kroky popisují proces opakování při zjištění chyb připojení:

1. Sada SDK zjistí chybu a přidruženou chybu v síti, protokolu nebo aplikaci.

2. Sada SDK používá filtr chyb k určení typu chyby a k určení, zda je nutné opakování.

3. Pokud sada SDK identifikuje **neopravitelnou chybu**, operace, jako je připojení, odesílání a přijímání, jsou zastaveny. Sada SDK upozorní uživatele. Příklady neopravitelných chyb zahrnují chybu ověřování a chybu koncového bodu.

4. Pokud sada SDK identifikuje **obnovitelnou chybu**, opakuje se podle zadané zásady opakování, dokud neuplyne definovaný časový limit.  Všimněte si, že sada SDK používá **exponenciální back-off s třetivou** zásady opakování ve výchozím nastavení.
5. Po vypršení definovaného časového limitu se sada SDK přestane pokoušet o připojení nebo odeslání. Upozorní uživatele.

6. Sada SDK umožňuje uživateli připojit zpětné volání pro příjem změn stavu připojení.

Sady SDK poskytují tři zásady opakování:

* **Exponenciální back-off s chvěním**: Tato výchozí zásada opakování má tendenci být agresivní na začátku a zpomalit v průběhu času, dokud nedosáhne maximální zpoždění. Návrh je založen na [pokynech k opakování z Centra architektury Azure](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific). 

* **Vlastní opakování**: Pro některé jazyky sady SDK můžete navrhnout vlastní zásady opakování, které jsou vhodnější pro váš scénář, a potom je vložit do zásad y Opakování. Vlastní opakování není k dispozici na C SDK a není aktuálně podporována v Pythonu SDK. Sada Python SDK se znovu připojí podle potřeby.

* **Žádný pokus :** Zásadu opakování můžete nastavit na "no retry", která zakáže logiku opakování. Sada SDK se pokusí připojit jednou a odeslat zprávu jednou za předpokladu, že připojení je navázáno. Tato zásada se obvykle používá ve scénářích s šířkou pásma nebo náklady obavy. Pokud zvolíte tuto možnost, zprávy, které se nepodaří odeslat, budou ztraceny a nelze je obnovit.

### <a name="retry-policy-apis"></a>Opakovat nastavení api zásad

   | Sada SDK | Metoda SetRetryPolicy | Provádění politik | Prováděcí pokyny |
   |-----|----------------------|--|--|
   |  C/iOS  | [IOTHUB_CLIENT_RESULT IoTHubClient_SetRetryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/2018-05-04/iothub_client/inc/iothub_client.h#L188)        | **Výchozí**: [IOTHUB_CLIENT_RETRY_EXPONENTIAL_BACKOFF](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Vlastní:** použití [dostupného zásady opakování](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Žádné opakování:** [IOTHUB_CLIENT_RETRY_NONE](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)  | [Implementace C/iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#)  |
   | Java| [Zásady Nastavení Retry](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.deviceclientconfig.setretrypolicy?view=azure-java-stable)        | **Výchozí**: [ExponenciálníBackoffWithJitter třída](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)<BR>**Vlastní:** implementace [rozhraní RetryPolicy](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/RetryPolicy.java)<BR>**Žádná opakování:** [Třída NoRetry](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)  | [Implementace Javy](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md) |
   | .NET| [DeviceClient.SetRetryPolicy](/dotnet/api/microsoft.azure.devices.client.deviceclient.setretrypolicy?view=azure-dotnet) | **Výchozí**: [ExponenciálníBackoff třída](/dotnet/api/microsoft.azure.devices.client.exponentialbackoff?view=azure-dotnet)<BR>**Vlastní:** implementace [rozhraní IRetryPolicy](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.iretrypolicy?view=azure-dotnet)<BR>**Žádná opakování:** [Třída NoRetry](/dotnet/api/microsoft.azure.devices.client.noretry?view=azure-dotnet) | [Implementace jazyka C#](https://github.com/Azure/azure-iot-sdk-csharp) | |
   | Node| [setRetryPolicy](/javascript/api/azure-iot-device/client?view=azure-iot-typescript-latest) | **Výchozí**: [ExponenciálníBackoffWithJitter třída](/javascript/api/azure-iot-common/exponentialbackoffwithjitter?view=azure-iot-typescript-latest)<BR>**Vlastní:** implementace [rozhraní RetryPolicy](/javascript/api/azure-iot-common/retrypolicy?view=azure-iot-typescript-latest)<BR>**Žádná opakování:** [Třída NoRetry](/javascript/api/azure-iot-common/noretry?view=azure-iot-typescript-latest) | [Implementace uzlu](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them) |
   | Python| Aktuálně se nepodporuje. | Aktuálně se nepodporuje. | Aktuálně se nepodporuje. |

Následující ukázky kódu ilustrují tento tok:

#### <a name="net-implementation-guidance"></a>Pokyny k implementaci rozhraní .NET

Následující ukázka kódu ukazuje, jak definovat a nastavit výchozí zásady opakování:

   ```csharp
   // define/set default retry policy
   IRetryPolicy retryPolicy = new ExponentialBackoff(int.MaxValue, TimeSpan.FromMilliseconds(100), TimeSpan.FromSeconds(10), TimeSpan.FromMilliseconds(100));
   SetRetryPolicy(retryPolicy);
   ```

Chcete-li se vyhnout vysoké využití procesoru, opakování jsou omezeny, pokud kód selže okamžitě. Například pokud neexistuje žádná síť nebo trasa k cíli. Minimální doba pro provedení dalšího opakování je 1 sekunda.

Pokud služba odpoví chybou omezení, zásady opakování se liší a nelze je změnit prostřednictvím veřejného rozhraní API:

   ```csharp
   // throttled retry policy
   IRetryPolicy retryPolicy = new ExponentialBackoff(RetryCount, TimeSpan.FromSeconds(10), 
     TimeSpan.FromSeconds(60), TimeSpan.FromSeconds(5)); SetRetryPolicy(retryPolicy);
   ```

Mechanismus opakování se `DefaultOperationTimeoutInMilliseconds`zastaví po , který je aktuálně nastaven na 4 minuty.

#### <a name="other-languages-implementation-guidance"></a>Pokyny k implementaci dalších jazyků

Ukázky kódu v jiných jazycích naleznete v následujících implementačních dokumentech. Úložiště obsahuje ukázky, které ukazují použití nastavení API zásad opakování.

* [C/iOS SDK](https://github.com/azure/azure-iot-sdk-c)

* [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/retrypolicy.md)

* [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

* [Python SDK](https://github.com/Azure/azure-iot-sdk-python)

## <a name="next-steps"></a>Další kroky

* [Použití sad SDK pro zařízení a služby](./iot-hub-devguide-sdks.md)

* [Použití sady SDK pro zařízení IoT pro C](./iot-hub-device-sdk-c-intro.md)

* [Vývoj pro zařízení s omezením](./iot-hub-devguide-develop-for-constrained-devices.md)

* [Vývoj pro mobilní zařízení](./iot-hub-how-to-develop-for-mobile-devices.md)

* [Poradce při potížích s odpojením zařízení](iot-hub-troubleshoot-connectivity.md)
