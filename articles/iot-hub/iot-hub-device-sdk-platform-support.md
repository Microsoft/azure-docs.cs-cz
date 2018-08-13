---
title: Podpora platformy sady SDK pro zařízení Azure IoT | Dokumentace Microsoftu
description: Principy – seznam platforem podporovaných sad SDK zařízení IoT Azure
author: yzhong94
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: yizhon
ms.openlocfilehash: cf3c80424c4626b62317bda537f9491cafc8198c
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/11/2018
ms.locfileid: "40043712"
---
# <a name="azure-iot-sdks-platform-support"></a>Podpora platformy sady SDK Azure IoT

[Sady SDK Azure IoT](iot-hub-devguide-sdks.md) jsou sada knihoven pro interakci s centrem IoT a služby Device Provisioning pomocí široké jazyka a platformní podpory.  Sady SDK spustit na většinu běžných platforem a vývojáři můžou port C SDK pro konkrétní platformu podle [pokyny k přenesení](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md). 

Společnost Microsoft podporuje různé operační systémy/platformy/rozhraní a je možné rozšířit pomocí sady Azure IoT C SDK.  Některé jsou od týmu, seskupených do vrstev, které představují úroveň podpory, které uživatelé můžou očekávat oficiálně podporována.  Plně podporované platformami znamená, že Microsoft:
    - Průběžné sestavování a tak kompletní test na hlavní server a LTS podporované verze
    - Poskytuje průvodce instalací nebo balíčky, pokud je k dispozici
    - Plnou podporu na Githubu

Kromě toho obsahuje seznam partnerů nabízejících přenáší naší sady SDK jazyka C k více platforem a udržuje vrstva abstrakce platformy (PAL).  [Azure IoT katalog zařízení Certified for](https://catalog.azureiotsolutions.com/) také funkce seznam platforem operačních systémů různých sadách SDK byly testovány s.  Kromě toho se sadami SDK služby také pravidelně vytvářejte na těchto platformách, s omezenou testování a podporu:
- MBED2
- Arduino
- Windows CE 2013 (přestat používat v října 2018)
- .NET standard 1.3 s .NET Core 2.1 a rozhraní .NET Framework 4.7
- Xamarin pro iOS, Android, UPW
- Android s Javou

## <a name="supported-platforms"></a>Podporované platformy
### <a name="c-sdk"></a>SDK PRO JAZYK C
| Operační systém                  | Architektura | Kompilátor             | Knihovna TLS       |
|---------------------|------|----------------------|-------------------|
| Ubuntu 16.04 LTS    | X64  | gcc 5.4.0            | OpenSSL - 1.0.2g |
| Ubuntu 18.04 LTS    | X64  | gcc 7.3              | WolfSSL – 1.13    |
| Ubuntu 18.04 LTS    | X64  | Clang 6.0.X          | OpenSSL – 1.1.0g  |
| OSX 10.13.4         | x64  | XCode 9.4.1          | Nativní OSX        |
| Windows Server 2016 | x64  | Visual Studio 14.0.X | SChannel          |
| Windows Server 2016 | x86  | Visual Studio 14.0.X | SChannel          |
| Debian 9 Stretch    | x64  | gcc 7.3              | OpenSSL – 1.1.0f  |

### <a name="python-sdk"></a>Python SDK
| Operační systém                  | Architektura | Kompilátor   | Knihovna TLS |
|---------------------|------|------------|-------------|
| Windows Server 2016 | x86  | Python 2.7 | OpenSSL     |
| Windows Server 2016 | x64  | Python 2.7 | OpenSSL     |
| Windows Server 2016 | x86  | Python 3.5 | OpenSSL     |
| Windows Server 2016 | x64  | Python 3.5 | OpenSSL     |
| Ubuntu 18.04 LTS    | x86  | Python 2.7 | OpenSSL     |
| Ubuntu 18.04 LTS    | x86  | Python 3.4 | OpenSSL     |
| Vysoce MacOS Sierra   | x64  | Python 2.7 | OpenSSL     |

### <a name="net-sdk"></a>.NET SDK
| Operační systém                  | Architektura | Rozhraní Framework            | Standard          |
|---------------------|------|----------------------|-------------------|
| Ubuntu 16.04 LTS    | X64  | .NET core 2.1        | .NET standard 2.0 |
| Windows Server 2016 | X64  | .NET core 2.1        | .NET standard 2.0 |
| Windows Server 2016 | X64  | Rozhraní .NET framework 4.7   | .NET standard 2.0 |
| Windows Server 2016 | X64  | Rozhraní .NET framework 4.5.1 | neuvedeno               |

### <a name="nodejs-sdk"></a>Node.js SDK
| Operační systém                                           | Architektura | Uzel verze |
|----------------------------------------------|------|--------------|
| Ubuntu 16.04 LTS (s použitím image dockeru uzel 6) | X64  | Uzel 6       |
| Windows Server 2016                          | X64  | Uzel 6       |

### <a name="java-sdk"></a>Java SDK
| Operační systém                  | Architektura | Verze Javy |
|---------------------|------|--------------|
| Ubuntu 16.04 LTS    | X64  | Java 8       |
| Windows Server 2016 | X64  | Java 8       |

## <a name="partner-supported-platforms"></a>Podpora partnerů platformy
| Partner             | Zařízení                            | Odkaz                     | Podpora |
|---------------------|------------------------------------|--------------------------|---------|
| Qualcomm            | Qualcomm MDM9206 LTE IoT Modem     | [Qualcomm LTE pro IoT SDK](https://developer.qualcomm.com/software/lte-iot-sdk) | [Fórum](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| ST Microelectronics | STM32L4 řady STM32F4 řady      | [X-DATOVÉ KRYCHLE CLOUD](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32cube-expansion-packages/x-cube-cloud.html)             | [Podpora](https://www.st.com/content/st_com/en/support/support-home.html) |
|                     | STM32F7 řady                     | [X-DATOVÉ KRYCHLE AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32cube-expansion-packages/x-cube-azure.html)             |         |
|                     | Sada zjišťování STM32L4 pro uzel IoT | [P-NUCLEO AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html)          |         |
|                     |                                    | [FP CLD AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            |         |
| Espressif           | ESP32                              | [ESP azure](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  |

## <a name="next-steps"></a>Další postup
- [Sady SDK pro zařízení a služby](iot-hub-devguide-sdks.md)
- [Pokyny k portaci](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)