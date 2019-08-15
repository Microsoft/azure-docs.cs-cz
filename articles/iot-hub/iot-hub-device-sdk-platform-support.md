---
title: Podpora platforem sady SDK pro zařízení Azure IoT | Microsoft Docs
description: Koncepty – seznam platforem podporovaných sadami SDK pro zařízení Azure IoT
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: robinsh
ms.openlocfilehash: 1fbaa54be4419d493d5179a6d6d0884393c8138a
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967328"
---
# <a name="azure-iot-sdks-platform-support"></a>Podpora platforem sady SDK Azure IoT

Sady [SDK Azure IoT](iot-hub-devguide-sdks.md) představují sadu knihoven pro interakci s IoT Hub a službu Device Provisioning s širokou podporou jazyků a platforem. Sady SDK běží na většině běžných platforem a vývojáři můžou na konkrétní platformu přenést sadu C SDK podle [pokynů pro přenos](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md). 

Společnost Microsoft podporuje různé operační systémy, platformy a rozhraní a je možné ji rozšířit pomocí sady Azure IoT C SDK. Některé jsou podporovány v oficiálně týmu, seskupené do vrstev, které představují úroveň uživatelů podpory, které mohou očekávat. *Plně podporované platformy* znamená, že Microsoft:

- Průběžně sestavuje a spouští ucelené testy proti hlavní verzi a podporovaným verzím LTS.  Abychom zajistili pokrytí testů napříč různými verzemi, obecně se otestujeme na nejnovější verzi LTS a nejoblíbenější verzi.  Jiné verze stejné platformy můžou být podporované přes kompatibilitu verze platformy.
- Poskytuje pokyny k instalaci nebo balíčky, pokud jsou k dispozici.
- Plně podporuje platformy na GitHubu.

Kromě toho seznam partnerů přestavil naši sadu C SDK na více platforem a udržuje vrstvu abstrakce platformy (PAL). [Katalog zařízení Azure Certified for IoT](https://catalog.azureiotsolutions.com/) také nabízí seznam platforem operačních systémů, u kterých byly různé sady SDK testovány proti. Sady SDK se také pravidelně vytvářejí na těchto platformách s omezeným testováním a podporou:

* MBED2
* Arduino
* Systém Windows CE 2013 (zastaralé v říjnu 2018)
* .NET Standard 1,3 s rozhraním .NET Core 2,1 a .NET Framework 4,7
* Xamarin iOS, Android, UWP

## <a name="supported-platforms"></a>Podporované platformy

Podporuje se několik platforem.

### <a name="c-sdk"></a>SADA C SDK

| OS                  | Arch | Přepínač             | Knihovna TLS       |
|---------------------|------|----------------------|-------------------|
| Ubuntu 16.04 LTS    | X64  | gcc-5.4.0            | OpenSSL-1.0.2 g |
| Ubuntu 18,04 LTS    | X64  | gcc-7.3              | WolfSSL – 1,13    |
| Ubuntu 18,04 LTS    | X64  | Clang 6.0. X          | OpenSSL – 1.1.0 g  |
| OSX 10.13.4         | x64  | XCode 9.4.1          | Nativní OSX        |
| Windows Server 2016 | x64  | Visual Studio 14.0.X | SChannel          |
| Windows Server 2016 | x86  | Visual Studio 14.0.X | SChannel          |
| Debian 9 Stretch    | x64  | gcc-7.3              | OpenSSL – 1.1.0 f  |

### <a name="python-sdk"></a>Python SDK

| OS                  | Arch | Přepínač   | Knihovna TLS |
|---------------------|------|------------|-------------|
| Windows Server 2016 | x86  | Python 2.7 | OpenSSL     |
| Windows Server 2016 | x64  | Python 2.7 | OpenSSL     |
| Windows Server 2016 | x86  | Python 3,5 | OpenSSL     |
| Windows Server 2016 | x64  | Python 3,5 | OpenSSL     |
| Ubuntu 18,04 LTS    | x86  | Python 2.7 | OpenSSL     |
| Ubuntu 18,04 LTS    | x86  | Python 3,4 | OpenSSL     |
| MacOS High Sierra   | x64  | Python 2.7 | OpenSSL     |

### <a name="net-sdk"></a>.NET SDK

| OS                  | Arch | Rozhraní .NET Framework            | Standard          |
|---------------------|------|----------------------|-------------------|
| Ubuntu 16.04 LTS    | X64  | .NET Core 2.1        | .NET Standard 2,0 |
| Windows Server 2016 | X64  | .NET Core 2.1        | .NET Standard 2,0 |
| Windows Server 2016 | X64  | Rozhraní .NET framework 4.7   | .NET Standard 2,0 |
| Windows Server 2016 | X64  | .NET Framework 4.5.1 | Není k dispozici               |

### <a name="nodejs-sdk"></a>Node.js SDK

| OS                                           | Arch | Verze uzlu |
|----------------------------------------------|------|--------------|
| Ubuntu 16,04 LTS (použití image Docker v uzlu 6) | X64  | Uzel 6       |
| Windows Server 2016                          | X64  | Uzel 6       |

### <a name="java-sdk"></a>Java SDK

| OS                  | Arch | Verze Javy |
|---------------------|------|--------------|
| Ubuntu 16.04 LTS    | X64  | Java 8       |
| Windows Server 2016 | X64  | Java 8       |
| Android API 28 | X64  | Java 8       |
| Android – věci | X64  | Java 8      |

## <a name="partner-supported-platforms"></a>Podporované platformy partnerů

Zákazníci můžou podporu naší platformy rozšíří pomocí přenosu sady SDK Azure IoT C, konkrétně vytvořením vrstvy pro abstrakci platforem (PAL) sady SDK. Microsoft spolupracuje s partnery a poskytuje rozšířenou podporu. Seznam partnerů přestavil sadu C SDK na více platforem a zachovala PAL.

| Partner             | Zařízení                            | Odkaz                     | Podpora |
|---------------------|------------------------------------|--------------------------|---------|
| Espressif           | ESP32 <br/> ESP8266                              | [Esp-azure](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  
| ASUS            | Modem Qualcomm MDM9206 LTE IoT     | [Qualcomm LTE pro sadu IoT SDK](https://developer.qualcomm.com/software/lte-iot-sdk) | [Fórum](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| Svatý mikroelektronické | Řada STM32L4 <br/> Řada STM32F4 <br/>  Řada STM32F7 <br/>  STM32L4 Discovery kit pro uzel IoT    | [X-CUBE-CLOUD](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32cube-expansion-packages/x-cube-cloud.html) <br/> [X-DATOVÁ KRYCHLE – AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32cube-expansion-packages/x-cube-azure.html) <br/> [P-NUCLEO – AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html) <br/> [FP-CLD – AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            | [Podpora](https://www.st.com/content/st_com/en/support/support-home.html)
| Texas Instruments   | Hlavní panel CC3220SF </br> Hlavní panel CC3220S </br> Hlavní panel CC3235SF </br> Hlavní panel CC3235S </br> Hlavní panel MSP432E4 | [Modul plug-in Azure IoT pro SimpleLink](https://github.com/TexasInstruments/azure-iot-pal-simplelink) | [Fórum TI E2E](https://e2e.ti.com) <br/> [Fórum TI E2E pro CC3220](https://e2e.ti.com/support/wireless_connectivity/simplelink_wifi_cc31xx_cc32xx/) <br/> [Fórum TI E2E pro MSP432E4](https://e2e.ti.com/support/microcontrollers/msp430/) |

## <a name="next-steps"></a>Další postup

* [Sady SDK pro zařízení a služby](iot-hub-devguide-sdks.md)
* [Pokyny k přenosu](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
