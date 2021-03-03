---
title: Datový list Azure Percept Vision
description: Podrobné specifikace zařízení najdete v datovém listu Azure Percept Vision.
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: reference
ms.date: 02/16/2021
ms.openlocfilehash: 8814192274a81938d53ffc68c02dfaa9ac1da8ad
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662303"
---
# <a name="azure-percept-vision-datasheet"></a>Datový list Azure Percept Vision

Níže uvedené specifikace jsou pro zařízení Azure Percept Vision, které je součástí [Azure PERCEPT DK](./azure-percept-dk-datasheet.md).

|Specifikace produktu           |Hodnota     |
|--------------------------------|---------------------|
|Cílové odvětví               |Výroba <br> Inteligentní budovy <br> Auto <br> Retail |
|Scénáře Hero                  |Nakupujících Analytics <br> Dostupnost na police <br> Zmenšení zmenšení <br> Zabezpečení/dohled <br> Bezpečnost na pracovišti|
|Dimenze                      |42mm × 42mm x 40mm (sestavení Azure Percept Vision SoM s bydlením) <br> 42mm × 42mm × 6mm (čip pro model SoM Vision)|
|Rovina ovládacího prvku správy        |Aktualizace zařízení Azure (ADU)          |
|Podporovaný software a služby |[Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) <br> [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) <br> [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) <br> [Modul runtime ONNX](https://www.onnxruntime.ai/) <br> [OpenVINO](https://docs.openvinotoolkit.org/latest/index.html) <br> Aktualizace zařízení Azure |
|Akcelerace AI                 |Intel Movidius nesčetných X (MA2085) Vision Processing Unit (VPU) s integrovaným poskytovatelem internetových služeb Intel, 0,7 nahoru |
|Senzory a vizuální indikátory   |Kamera Omni Vision 5670 <br> GSO GS8882AA Lens (rozlišení: 5MP v 30FPS, vzdálenost: 50cm-nekonečno, FoV: 120 stupňů, barva: celosvětový dynamický rozsah, pevně zaostřený postupný výběr)          |
|Podpora kamery                  |Kamery RGB, IR a hloubky <br> 2 kamery můžou běžet souběžně. |
|Crypto-Controller zabezpečení      |ST-Micro STM32L462CE      |
|Verze a komponenta ID       |64 – paměť EEPROM |
|Memory (Paměť)                          |LPDDR4 2 GB     |
|Napájení                           |3,5 W     |
|Porty                           |1x USB 3,0 typ C <br> 2x MIPI 4 Lane (až 1,5 GB/s na jednu dráhu)     |
|Řídicí rozhraní              |2x I2C <br> 2x SPI <br> 6x PWM (GPIOs: 2x, 2x Sync, 2x nepoužito) <br> 2x náhradní GPIO |
|Certifikace                   |CE <br> ROZPOZNÁN      |
|Provozní teplota           |0 až 27 stupňů C (sestavení Azure Percept Vision SoM s bydlením) <br> -10 až 70 stupňů C (čip pro SoM Vision) |
|Dotyková teplota               |<= 48 stupňů C |
|Relativní vlhkost               |8% až 90%    |