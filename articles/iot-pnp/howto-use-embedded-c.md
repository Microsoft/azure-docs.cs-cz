---
title: Použití IoT technologie Plug and Play na omezených zařízeních | Microsoft Docs
description: Přečtěte si, jak můžete implementovat IoT technologie Plug and Play na omezených zařízeních pomocí sady SDK pro Embedded C nebo Azure RTO.
author: elhorton
ms.author: elhorton
ms.date: 09/23/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: edcfcda8de1fade605c409537155231d90d5849e
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91610071"
---
# <a name="implement-iot-plug-and-play-on-constrained-devices"></a>Implementace IoT technologie Plug and Play na omezených zařízeních

Pokud vyvíjíte pro *omezená zařízení*, můžete použít IoT technologie Plug and Play se sadou [Azure SDK pro integrované klientské knihovny C IoT](https://aka.ms/embeddedcsdk) nebo s [Azure RTO](https://docs.microsoft.com/azure/rtos/overview-rtos). Tento článek obsahuje odkazy a prostředky pro tyto omezené scénáře.

## <a name="use-the-sdk-for-embedded-c"></a>Použití sady SDK pro Embedded C

Sada SDK pro Embedded C nabízí zjednodušené řešení pro připojení omezených zařízení ke službám Azure IoT, včetně použití [konvencí technologie Plug and Play IoT](concepts-convention.md). Následující odkazy obsahují ukázky pro reálné zařízení a pro účely vzdělávání a ladění.

### <a name="use-a-real-device"></a>Použití skutečného zařízení

Úplný kurz, který používá sadu SDK pro Embedded C, službu Device Provisioning a IoT technologie Plug and Play na reálném zařízení, najdete v tématu o tom, jak se ke službě [Azure připojit pomocí IoT Hub Device Provisioning Service pro WX](https://github.com/Azure-Samples/Microchip-PIC-IoT-Wx).

### <a name="introductory-samples"></a>Úvodní ukázky

Sada SDK pro vložené úložiště C obsahuje [několik ukázek](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/samples/iot#iot-hub-plug-and-play-sample) , které ukazují, jak používat IoT technologie Plug and Play:

> [!NOTE]
> Tyto ukázky jsou spuštěné v systému Windows a Linux pro účely vzdělávání a ladění. V produkčním scénáři jsou ukázky určeny jenom pro omezená zařízení.

- [Příklad termostatu s SDK pro Embedded C](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_sample.c)

- [Příklad řadiče teploty se sadou SDK pro Embedded C](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_component_sample.c)

## <a name="using-azure-rtos"></a>Používání Azure RTO

Azure RTO zahrnuje odlehčenou vrstvu, která přidává nativní připojení ke cloudovým službám Azure IoT. Tato vrstva poskytuje jednoduchý mechanismus pro připojení omezených zařízení ke službě Azure IoT při použití pokročilých funkcí služby Azure RTO. Další informace najdete v tématu [co je Microsoft Azure RTO](https://docs.microsoft.com/azure/rtos/overview-rtos).

### <a name="toolchains"></a>Sady nástrojů

Ukázky Azure RTO jsou k dispozici s různými druhy rozhraní IDE a kombinací sada nástrojů, jako jsou:

- IAR: rozhraní IDE pro [Embedded Workbench](https://www.iar.com/iar-embedded-workbench/) IAR
- RSZ/CMake: Sestavte se na open source systém sestavení [cmake](https://cmake.org/) a v systému [GNU ARM Embedded sada nástrojů](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm).
- MCUExpresso: [rozhraní IDE pro MCUXPRESSO](https://www.nxp.com/design/software/development-software/mcuxpresso-software-and-tools-/mcuxpresso-integrated-development-environment-ide:MCUXpresso-IDE) NXP
- STM32Cube: [rozhraní IDE pro STM32Cube](https://www.st.com/en/development-tools/stm32cubeide.html) STMicroelectronic
- MPLAB: mikročip [IDE MPLAB X](https://www.microchip.com/mplab/mplab-x-ide)

### <a name="samples"></a>ukázky

Ukázky, které ukazují, jak začít pracovat na různých zařízeních s Azure RTO a IoT technologie Plug and Play, najdete v následující tabulce:

Manufacturer | Zařízení | ukázky |
| --- | --- | --- |
| Umístěný | [ATSAME54-XPRO](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro) | [RSZ/cmake](https://github.com/azure-rtos/getting-started/tree/master/Microchip/ATSAME54-XPRO) • [IAR](https://aka.ms/azrtos-sample/e54-iar) • [MPLAB](https://aka.ms/azrtos-sample/e54-mplab)
| MXCHIP | [AZ3166](https://aka.ms/iot-devkit) | [RSZ/CMake](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)
| NXP | [MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK) | [RSZ/cmake](https://github.com/azure-rtos/getting-started/tree/master/NXP/MIMXRT1060-EVK) • [IAR](https://aka.ms/azrtos-sample/rt1060-iar) • [MCUXpresso](https://aka.ms/azrtos-sample/rt1060-mcuxpresso)
| STMicroelectronics | [32F746GDISCOVERY](https://www.st.com/en/evaluation-tools/32f746gdiscovery.html) | [IAR](https://aka.ms/azrtos-sample/f746g-iar) • [STM32Cube](https://aka.ms/azrtos-sample/f746g-cubeide)
| STMicroelectronics | [B – L475E – IOT01](https://www.st.com/en/evaluation-tools/b-l475e-iot01a.html) | [RSZ/cmake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)
| STMicroelectronics | [B – L4S5I – IOT01](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html) | [RSZ/cmake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s možnostmi pro implementaci IoT technologie Plug and Play na omezených zařízeních, je doporučený další krok informace o [konvencích technologie Plug and Play IoT](concepts-convention.md).