---
title: Podpora platformy SDK zařízení Azure IoT | Dokumenty společnosti Microsoft
description: Sady SDK s otevřeným zdrojovým kódem jsou dostupné na GitHubu v C, .NET (C#), Vuvě, Node.js a Pythonu pro připojení zařízení k Azure IoT Hub a službě Zřizování zařízení (DPS).
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: robinsh
ms.openlocfilehash: 496b890cc49b6b6b9f15213a48472447f801b1c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045116"
---
# <a name="azure-iot-device-sdks-platform-support"></a>Podpora platformy Azure IoT Device SDKS

Microsoft se snaží neustále rozšiřovat vesmír zařízení podporujících Azure IoT Hub. Microsoft publikuje sady SDK s otevřeným zdrojovým kódem na GitHubu, aby pomohl připojit zařízení k Azure IoT Hub a službě Device Provisioning. Sady SDK zařízení jsou k dispozici pro c, .NET (C#), Java, Node.js a Python. Společnost Microsoft testuje každou sadu SDK, aby zajistila, že je spuštěna na podporovaných konfiguracích, které jsou pro něj podrobně popsány v části [Microsoft SDK a podpora platformy zařízení.](#microsoft-sdks-and-device-platform-support)

Kromě sad SDK zařízení poskytuje Microsoft několik dalších způsobů, jak zákazníkům a vývojářům poumožňuje připojit svá zařízení k Azure IoT:

* Microsoft spolupracuje s několika partnerskými společnostmi, aby jim pomohl publikovat vývojové sady založené na sadě Azure IoT C SDK pro jejich hardwarové platformy.

* Společnost Microsoft spolupracuje s důvěryhodnými partnery microsoftu na poskytování stále se rozšiřující sady zařízení, která byla testována a certifikována pro Azure IoT. Aktuální seznam těchto zařízení najdete v tématu [Azure certified for IoT device catalog](https://catalog.azureiotsolutions.com/).

* Microsoft poskytuje vrstvu abstrakce platformy (PAL) v sadě Azure IoT Hub Device C SDK, která vývojářům pomáhá snadno přenést sadu SDK na jejich platformu. Další informace naleznete v [pokynech k přenosu sady C SDK](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md).

Toto téma obsahuje informace o sadách Microsoft SDK a konfiguracích platformy, které podporují, stejně jako všechny další výše uvedené možnosti.

## <a name="microsoft-sdks-and-device-platform-support"></a>Sady Microsoft SDK a podpora platformy zařízení

Microsoft publikuje open source Sady SDK na GitHubu pro následující jazyky: C, .NET (C#), Node.js, Java a Python. Sady SDK a jejich závislosti jsou uvedeny v této části. Sady SDK jsou podporovány na libovolné platformě zařízení, která splňuje tyto závislosti.

Pro každou z uvedených sad SDK, Microsoft:

* Průběžně sestavuje a spouští end-to-end testy proti hlavní větvi příslušné sady SDK v GitHubu na několika oblíbených platformách.  Chcete-li poskytnout pokrytí testování v různých verzích kompilátoru, obvykle testujeme s nejnovější verzí LTS a nejoblíbenější verzí.

* Obsahuje instalační pokyny nebo instalační balíčky, pokud je to možné.

* Plně podporuje sady SDK na GitHubu s otevřeným zdrojovým kódem, cestou pro příspěvky zákazníků a zapojením produktového týmu do problémů s GitHubem.

### <a name="c-sdk"></a>C SDK

Sada [Azure IoT Hub C zařízení SDK](https://github.com/Azure/azure-iot-sdk-c) je testována a podporuje následující konfigurace.

| Operační systém                  | Knihovna TLS                  | Dodatečné požadavky                                                                     |
|---------------------|------------------------------|---------------------------------------------------------------------------------------------|
| Linux               | OpenSSL, WolfSSL nebo BearSSL | Berkeley zásuvky</br></br>Rozhraní přenosného operačního systému (POSIX)                       |
| iOS 12.2            | OpenSSL                      | XCode emulovaný v OSX 10.13.4                                                               |
| Windows 10 rodina   | Schannel                     |                                                                                             |
| Mbed OS 5,4         | Mbed TLS 2                   | [Sada pro dev(mxChip IoT)](https://microsoft.github.io/azure-iot-developer-kit/)                  |
| Operační o službě Azure Sphere     | Vlčí svinský                      | [Azure Sphere MT3620](https://azure.microsoft.com/services/azure-sphere/get-started/) |
| Arduino             | Medvědissl                      | [ESP32 nebo ESP8266](https://github.com/Azure/azure-iot-arduino#simple-sample-instructions) 

### <a name="python-sdk"></a>Python SDK

Sada [Azure IoT Hub Python, sada SDK,](https://github.com/Azure/azure-iot-sdk-python) je testována a podporuje následující konfigurace.

| Operační systém                  | Kompilátoru                          |
|---------------------|-----------------------------------|
| Linux               | Python 2.7.*, 3.5 nebo novější |
| MacOS Vysoká Sierra   | Python 2.7.*, 3.5 nebo novější |
| Windows 10 rodina   | Python 2.7.*, 3.5 nebo novější |

Pouze Python verze 3.5.3 nebo novější podporují asynchronní API, doporučujeme použít verzi 3.7 nebo novější.

### <a name="net-sdk"></a>.NET SDK

Sada [SDK zařízení Azure IoT Hub .NET (C#)](https://github.com/Azure/azure-iot-sdk-csharp) je testována a podporuje následující konfigurace.

| Operační systém                                   | Standard                                                   |
|--------------------------------------|------------------------------------------------------------|
| Linux                                | .NET Core 2.1                                              |
| SKU plochy a serveru Windows 10   | Rozhraní .NET Core 2.1, .NET Framework 4.5.1 nebo .NET Framework 4.7 |

SDK .NET lze také použít s Windows IoT Core s [Agent zařízení Azure](https://github.com/ms-iot/azure-client-tools/blob/master/docs/device-agent/device-agent.md) nebo vlastní [NTService, který můžete použít Vzdálené volání procedur ke komunikaci s aplikacemi UPW](https://docs.microsoft.com/samples/microsoft/windows-iotcore-samples/ntservice-rpc/).

### <a name="nodejs-sdk"></a>Node.js SDK

Sada [Azure IoT Hub Node.js device SDK](https://github.com/Azure/azure-iot-sdk-node) je testována a podporuje následující konfigurace.

| Operační systém                  | Verze uzlu    |
|---------------------|-----------------|
| Linux               | LTS a aktuální |
| Windows 10 rodina   | LTS a aktuální |

### <a name="java-sdk"></a>Java SDK

Sada [Azure IoT Hub Java device SDK](https://github.com/Azure/azure-iot-sdk-java) je testována a podporuje následující konfigurace.

| Operační systém                     | Java verze |
|------------------------|--------------|
| Android API 28         | Java 8       |
| Linux x64             | Java 8       |
| Windows 10 rodina x64  | Java 8       |

## <a name="partner-supported-development-kits"></a>Partnerem podporované vývojové sady

Společnost Microsoft spolupracuje s různými partnery na poskytování vývojových sad pro několik architektur mikroprocesorů. Tito partneři přenesli azure iot c sdk na svou platformu. Partneři vytvořit a udržovat vrstvu abstrakce platformy (PAL) sady SDK. Společnost Microsoft spolupracuje s těmito partnery na poskytování rozšířené podpory.

| Partner             | Zařízení                            | Odkaz                     | Podpora |
|---------------------|------------------------------------|--------------------------|---------|
| Espressif           | ESP32 <br/> ESP8266                              | [Esp-azure](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  
| Qualcomm            | Qualcomm MDM9206 LTE IoT Modem     | [Qualcomm LTE pro IoT SDK](https://developer.qualcomm.com/software/lte-iot-sdk) | [Fórum](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| MIKROelektronika ST | Řada STM32L4 <br/> Řada STM32F4 <br/>  Řada STM32F7 <br/>  StM32L4 Discovery Kit pro uzel IoT    | [X-CUBE-AZURE](https://www.st.com/en/embedded-software/x-cube-azure.html) <br/>  <br/> [P-NUCLEO-AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html) <br/> [FP-CLD-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            | [Podpora](https://www.st.com/content/st_com/en/support/support-home.html)
| Texas nástroje   | Cc3220SF odpalovací rampa </br> CC3220S LaunchPad </br> Cc3235SF odpalovací rampa </br> CC3235S LaunchPad </br> Odpalovací rampa MSP432E4 | [Azure IoT Plugin pro SimpleLink](https://github.com/TexasInstruments/azure-iot-pal-simplelink) | [TI E2E fórum](https://e2e.ti.com) <br/> [TI E2E fórum pro CC3220](https://e2e.ti.com/support/wireless_connectivity/simplelink_wifi_cc31xx_cc32xx/) <br/> [TI E2E fórum pro MSP432E4](https://e2e.ti.com/support/microcontrollers/msp430/) |

## <a name="porting-the-microsoft-azure-iot-c-sdk"></a>Portování sady Microsoft Azure IoT C SDK

Pokud vaše platforma zařízení není pokryta některou z předchozích částí, můžete zvážit přenesení sady Azure IoT C SDK. Portování sady C SDK zahrnuje především implementaci vrstvy abstrakce platformy (PAL) sady SDK. Pal definuje primitiva, které poskytují připevnění mezi zařízením a vyšší úrovně funkce v sdk. Další informace naleznete v [tématu Porting Guidance](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md).

## <a name="microsoft-partners-and-certified-azure-iot-devices"></a>Partneři Microsoftu a certifikovaná zařízení Azure IoT

Microsoft spolupracuje s řadou partnerů na neustálém rozšiřování vesmíru Azure IoT pomocí zařízení testovaných a certifikovaných pro Azure IoT.

* Informace o procházení zařízení s certifikací Azure IoT najdete v tématu [Katalog zařízení Microsoft Azure Certified for IoT](https://catalog.azureiotsolutions.com/).

* Další informace o ekosystému Azure Certified for IoT najdete [v tématu Připojení k ekosystému Certified for IoT](https://catalog.azureiotsolutions.com/register).

## <a name="connecting-to-iot-hub-without-an-sdk"></a>Připojení k centru IoT Hub bez sady SDK

Pokud nemůžete použít některou z sad SDK zařízení IoT Hub, můžete se připojit přímo k ioT hubu pomocí [rozhraní API REST služby IoT Hub](https://docs.microsoft.com/rest/api/iothub/) z libovolné aplikace schopné odesílat a přijímat požadavky a odpovědi HTTPS.

## <a name="support-and-other-resources"></a>Podpora a další zdroje

Pokud při používání sad SDK zařízení Azure IoT dochází k problémům, existuje několik způsobů, jak vyhledat podporu. Můžete vyzkoušet jeden z následujících kanálů:

**Hlášení chyb** – chyby v sadách SDK zařízení lze oznamovat na stránce problémů příslušného projektu GitHub. Opravy rychle, aby jejich cestu z projektu do aktualizací produktu.

* [Problémy s sadou Azure IoT Hub C SDK](https://github.com/Azure/azure-iot-sdk-c/issues)

* [Azure IoT Hub .NET (C#) SDK problémy](https://github.com/Azure/azure-iot-sdk-csharp/issues)

* [Problémy s sadnou Azure IoT Hub Java SDK](https://github.com/Azure/azure-iot-sdk-java/issues)

* [Problémy s sadou Azure IoT Hub Node.js SDK](https://github.com/Azure/azure-iot-sdk-node/issues)

* [Problémy s sadnou Azure IoT Hub Python SDK](https://github.com/Azure/azure-iot-sdk-python/issues)

**Tým zákaznické podpory Microsoftu** – uživatelé, kteří mají [plán podpory,](https://azure.microsoft.com/support/plans/) mohou zapojit tým zákaznické podpory microsoftu vytvořením nové žádosti o podporu přímo z [portálu Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

**Požadavky na funkce** – požadavky na funkce Azure IoT jsou sledovány prostřednictvím [stránky User Voice](https://feedback.azure.com/forums/321918-azure-iot)produktu .

## <a name="next-steps"></a>Další kroky

* [Sady SDK pro zařízení a služby](iot-hub-devguide-sdks.md)
* [Pokyny pro přenos portů](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
