---
title: Podpora platforem sady SDK pro zařízení Azure IoT | Microsoft Docs
description: Sady SDK pro open source zařízení jsou k dispozici na GitHubu v jazyce C, .NET (C#), Java, Node.js a Python pro připojení zařízení k Azure IoT Hub a službě Device Provisioning (DPS).
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: robinsh
ms.openlocfilehash: 5df36cdb3c02fd4c813625c4869c21581f009b02
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92152220"
---
# <a name="azure-iot-device-sdks-platform-support"></a>Podpora platforem sady SDK pro zařízení Azure IoT

Microsoft usiluje o průběžnou rozšiřování celé části zařízení s technologií Azure IoT Hub, která podporuje. Microsoft publikuje open source sady SDK pro zařízení na GitHubu, aby bylo možné lépe připojit zařízení k Azure IoT Hub a službě Device Provisioning. Sady SDK pro zařízení jsou k dispozici pro jazyky C, .NET (C#), Java, Node.js a Python. Microsoft testuje každou sadu SDK, aby se zajistilo, že běží na podporovaných konfiguracích, a to v části [Podpora platforem Microsoft SDK a platformy zařízení](#microsoft-sdks-and-device-platform-support) .

Kromě sad SDK pro zařízení poskytuje společnost Microsoft několik dalších cesty vedoucí, které zákazníkům a vývojářům umožňují připojit svá zařízení k Azure IoT:

* Microsoft spolupracuje s několika partnerskými společnostmi, aby jim usnadnil publikování vývojových sad založených na sadě Azure IoT C SDK pro své hardwarové platformy.

* Microsoft spolupracuje s důvěryhodnými partnery Microsoftu k poskytování stále se rozšiřující sady zařízení, která byla testována a certifikována pro Azure IoT. Aktuální seznam těchto zařízení najdete v [katalogu zařízení Azure Certified for IoT](https://catalog.azureiotsolutions.com/).

* Microsoft poskytuje vrstvu abstrakce (PAL) na platformě Azure IoT Hub zařízení C SDK, která vývojářům pomáhá snadno přenést sadu SDK na platformu. Další informace najdete v tématu [pokyny pro přenos v sadě C SDK](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md).

Toto téma poskytuje informace o sadách Microsoft SDK a podporovaných konfiguracích platforem a také o všech dalších možnostech uvedených výše.

## <a name="microsoft-sdks-and-device-platform-support"></a>Microsoft SDK a podpora platforem zařízení

Microsoft publikuje open source sady SDK na GitHubu pro následující jazyky: C, .NET (C#), Node.js, Java a Python. Sady SDK a jejich závislosti jsou uvedeny v této části. Sady SDK jsou podporované na všech platformách zařízení, které tyto závislosti splňují.

Pro každou z uvedených sad SDK Microsoft:

* Průběžně sestavuje a spouští ucelené testy s hlavní větví příslušné sady SDK v GitHubu na několika oblíbených platformách.  Abychom zajistili pokrytí testů napříč různými verzemi kompilátoru, obecně se otestujeme na nejnovější verzi LTS a nejoblíbenější verzi.

* Poskytuje pokyny k instalaci nebo instalační balíčky, pokud jsou k dispozici.

* Plně podporuje sady SDK na GitHubu s otevřeným zdrojovým kódem, cestou pro zákaznické příspěvky a produktovým týmem s problémy GitHubu.

### <a name="c-sdk"></a>SADA C SDK

[Sada SDK pro zařízení Azure IoT Hub C](https://github.com/Azure/azure-iot-sdk-c) je testována pomocí nástroje a podporuje následující konfigurace.

| Operační systém                  | Knihovna TLS                  | Další požadavky                                                                     |
|---------------------|------------------------------|---------------------------------------------------------------------------------------------|
| Linux               | OpenSSL, WolfSSL nebo BearSSL | Berkeley Sockets</br></br>Rozhraní standardu POSIX (Portable Operating System Interface)                       |
| iOS 12,2            | OpenSSL                      | XCode emulované ve OSX 10.13.4                                                               |
| Řada Windows 10   | SChannel                     |                                                                                             |
| Mbed OS 5,4         | Mbed TLS 2                   | [MXChip IoT dev Kit](https://microsoft.github.io/azure-iot-developer-kit/)                  |
| Azure Sphere operační systém     | WolfSSL                      | [Azure Sphere MT3620](https://azure.microsoft.com/services/azure-sphere/get-started/) |
| Arduino             | BearSSL                      | [ESP32 nebo ESP8266](https://github.com/Azure/azure-iot-arduino#simple-sample-instructions) 

### <a name="python-sdk"></a>Python SDK

[Sada SDK pro zařízení služby Azure IoT Hub Python](https://github.com/Azure/azure-iot-sdk-python) je testována s nástrojem a podporuje následující konfigurace.

| Operační systém                  | Compiler                          |
|---------------------|-----------------------------------|
| Linux               | Python 2,7. *, 3,5 nebo novější |
| macOS High Sierra   | Python 2,7. *, 3,5 nebo novější |
| Řada Windows 10   | Python 2,7. *, 3,5 nebo novější |

Jenom Python verze 3.5.3 nebo novější podporuje asynchronní rozhraní API, doporučujeme použít verzi 3,7 nebo novější.

### <a name="net-sdk"></a>.NET SDK

[Sada SDK pro zařízení Azure IoT Hub .NET (C#)](https://github.com/Azure/azure-iot-sdk-csharp) je testována pomocí nástroje a podporuje následující konfigurace.

| Operační systém                                   | Standard                                                   |
|--------------------------------------|------------------------------------------------------------|
| Linux                                | .NET Core 2.1                                              |
| SKU Windows 10 Desktop a Server   | .NET Core 2,1, .NET Framework 4.5.1 nebo .NET Framework 4,7 |

Sadu .NET SDK je také možné použít s Windows IoT Core s [agentem zařízení Azure](https://github.com/ms-iot/azure-client-tools/blob/master/docs/device-agent/device-agent.md) nebo [vlastním NTService, který umožňuje komunikaci s aplikacemi UWP pomocí protokolu RPC](/samples/microsoft/windows-iotcore-samples/ntservice-rpc/).

### <a name="nodejs-sdk"></a>Node.js SDK

[Sada SDK pro zařízení Azure IoT Hub Node.js](https://github.com/Azure/azure-iot-sdk-node) se testuje pomocí a podporuje následující konfigurace.

| Operační systém                  | Verze uzlu    |
|---------------------|-----------------|
| Linux               | LTS a aktuální |
| Řada Windows 10   | LTS a aktuální |

### <a name="java-sdk"></a>Java SDK

[Sada SDK pro zařízení s Azure IoT Hub Java](https://github.com/Azure/azure-iot-sdk-java) je testována s nástrojem a podporuje následující konfigurace.

| Operační systém                     | Verze Java |
|------------------------|--------------|
| Rozhraní Android API 28         | Java 8       |
| Linux x64             | Java 8       |
| Řada Windows 10 řady x64  | Java 8       |

## <a name="partner-supported-development-kits"></a>Podporované vývojové sady partnerů

Microsoft spolupracuje s různými partnery na poskytování vývojových sad pro několik architektur mikroprocesorů. Tito partneři nastavili na svou platformu sadu Azure IoT C SDK. Partneři vytvoří a udržují vrstvu abstrakce platformy (PAL) sady SDK. Microsoft spolupracuje s těmito partnery a poskytuje rozšířenou podporu.

| Partner             | Zařízení                            | Odkaz                     | Podpora |
|---------------------|------------------------------------|--------------------------|---------|
| Espressif           | ESP32 <br/> ESP8266                              | [ESP – Azure](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  
| ASUS            | Modem Qualcomm MDM9206 LTE IoT     | [Qualcomm LTE pro sadu IoT SDK](https://developer.qualcomm.com/software/lte-iot-sdk) | [Fórum](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| Svatý mikroelektronické | Řada STM32L4 <br/> Řada STM32F4 <br/>  Řada STM32F7 <br/>  STM32L4 Discovery kit pro uzel IoT    | [X-DATOVÁ KRYCHLE – AZURE](https://www.st.com/en/embedded-software/x-cube-azure.html) <br/>  <br/> [P-NUCLEO – AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html) <br/> [FP-CLD – AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            | [Podpora](https://www.st.com/content/st_com/en/support/support-home.html)
| Texas instrumentů   | Hlavní panel CC3220SF </br> Hlavní panel CC3220S </br> Hlavní panel CC3235SF </br> Hlavní panel CC3235S </br> Hlavní panel MSP432E4 | [Modul plug-in Azure IoT pro SimpleLink](https://github.com/TexasInstruments/azure-iot-pal-simplelink) | [Fórum TI E2E](https://e2e.ti.com) <br/> [Fórum TI E2E pro CC3220](https://e2e.ti.com/support/wireless_connectivity/simplelink_wifi_cc31xx_cc32xx/) <br/> [Fórum TI E2E pro MSP432E4](https://e2e.ti.com/support/microcontrollers/msp430/) |

## <a name="porting-the-microsoft-azure-iot-c-sdk"></a>Přenos Microsoft Azure IoT C SDK

Pokud vaše platforma zařízení nepokrývá jednu z předchozích sekcí, můžete zvážit přenos sady SDK pro Azure IoT C. Portování sady C SDK primárně zahrnuje implementaci vrstvy HAL (Platform Abstraction Layer) sady SDK. PAL definuje primitivní prvky, které v sadě SDK poskytují propojení mezi vašimi zařízeními a funkcemi vyšší úrovně. Další informace najdete v tématu [pokyny k přenosu](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md).

## <a name="microsoft-partners-and-certified-azure-iot-devices"></a>Partneři Microsoftu a certifikovaná zařízení Azure IoT

Microsoft spolupracuje s řadou partnerů, aby mohli průběžně rozšiřovat Azure IoT Universe pomocí testovaných a certifikovaných zařízení Azure IoT.

* Pokud chcete procházet zařízení s certifikací Azure IoT Certified, přečtěte si téma [Microsoft Azure Certified for IoT katalogu zařízení](https://catalog.azureiotsolutions.com/).

* Další informace o ekosystému Azure Certified for IoT najdete v tématu [připojení k ekosystému Certified for IoT](https://catalog.azureiotsolutions.com/register).

## <a name="connecting-to-iot-hub-without-an-sdk"></a>Připojení k IoT Hub bez sady SDK

Pokud nemůžete použít některou ze sad SDK pro IoT Hub zařízení, můžete se přímo připojit k IoT Hub pomocí podporovaného komunikačního protokolu. Další informace najdete v tématu [vývoj bez sady Azure IoT SDK](iot-hub-devguide-no-sdk.md).

## <a name="support-and-other-resources"></a>Podpora a další zdroje informací

Pokud při používání sad SDK pro zařízení Azure IoT dochází k problémům, existuje několik způsobů, jak najít podporu shrnutou níže. Úplné informace o všech možnostech podpory najdete v tématu [Podpora a možnosti pomoci Azure IoT](../iot-fundamentals/iot-support-help.md). 

**Hlášení chyb** – chyby v sadách SDK zařízení je možné ohlásit na stránce problémy v příslušném projektu GitHubu. Opravy se rychle dostanou z projektu na aktualizace produktů.

* [Problémy s SDK pro Azure IoT Hub C](https://github.com/Azure/azure-iot-sdk-c/issues)

* [Problémy sady SDK pro Azure IoT Hub .NET (C#)](https://github.com/Azure/azure-iot-sdk-csharp/issues)

* [Problémy s Azure IoT Hub Java SDK](https://github.com/Azure/azure-iot-sdk-java/issues)

* [Problémy s Node.js SDK pro Azure IoT Hub](https://github.com/Azure/azure-iot-sdk-node/issues)

* [Problémy sady SDK pro Azure IoT Hub Python](https://github.com/Azure/azure-iot-sdk-python/issues)

**Technické dotazy** – můžete klást technické dotazy k [Microsoft Q&](/answers/topics/azure-iot-sdk.html) a a [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-sdk) pomocí značky *Azure-IoT-SDK*.

**Tým zákaznické podpory Microsoftu** – uživatelé, kteří mají [plán podpory](https://azure.microsoft.com/support/plans/) , můžou tým zákaznické podpory společnosti Microsoft zahájit vytvořením nové žádosti o podporu přímo z [Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

**Žádosti o funkce** – požadavky na funkce Azure IoT jsou sledovány prostřednictvím [uživatelské hlasové stránky](https://feedback.azure.com/forums/321918-azure-iot).

## <a name="next-steps"></a>Další kroky

* [Sady SDK pro zařízení a služby](iot-hub-devguide-sdks.md)
* [Pokyny k přenosu](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)