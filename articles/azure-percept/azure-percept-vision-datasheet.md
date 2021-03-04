---
title: Datový list Azure Percept Vision
description: Podrobné specifikace zařízení najdete v datovém listu Azure Percept Vision.
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: reference
ms.date: 02/16/2021
ms.openlocfilehash: 54dae3341910bf863de7e2a2cef832ae670ca09a
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097563"
---
# <a name="azure-percept-vision-datasheet"></a>Datový list Azure Percept Vision

Níže uvedené specifikace jsou pro zařízení Azure Percept Vision, které je součástí [Azure PERCEPT DK](./azure-percept-dk-datasheet.md).

|Specifikace produktu           |Hodnota     |
|--------------------------------|---------------------|
|Cílové odvětví               |Výroba <br> Inteligentní budovy <br> Auto <br> Retail |
|Scénáře Hero                  |Nakupujících Analytics <br> Dostupnost na police <br> Zmenšení zmenšení <br> Monitorování pracovní plochy|
|Dimenze                      |42mm × 42mm x 40mm (sestavení Azure Percept Vision SoM s bydlením) <br> 42mm × 42mm × 6mm (čip pro model SoM Vision)|
|Rovina ovládacího prvku správy        |Aktualizace zařízení Azure (ADU)          |
|Podporovaný software a služby |[Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) <br> [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) <br> [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) <br> [Modul runtime ONNX](https://www.onnxruntime.ai/) <br> [OpenVINO](https://docs.openvinotoolkit.org/latest/index.html) <br> Aktualizace zařízení Azure |
|Akcelerace AI                 |Intel Movidius nesčetných X (MA2085) Vision Processing Unit (VPU) s integrovaným poskytovatelem internetových služeb Intel, 0,7 nahoru |
|Senzory a vizuální indikátory   |Senzor kamery Sony IMX219 s 00 Lensem<br>Řešení: 8MP na 30FPS, distance: 50cm-Infinity<br>FoV: 120 stupňů dolů, barva: celosvětový dynamický rozsah, pevně zaostření – válcování|
|Podpora kamery                  |RGB (aktuálně) a IR (v budoucnu) <br> 2 kamery můžou běžet souběžně. |
|Crypto-Controller zabezpečení      |ST-Micro STM32L462CE      |
|Verze a komponenta ID       |64 – paměť EEPROM |
|Memory (Paměť)                          |LPDDR4 2 GB     |
|Napájení                           |3,5 W     |
|Porty                           |1x USB 3,0 typ C <br> 2x MIPI 4 Lane (až 1,5 GB/s na jednu dráhu)     |
|Řídicí rozhraní              |2x I2C <br> 2x SPI <br> 6x PWM (GPIOs: 2x, 2x Sync, 2x nepoužito) <br> 2x náhradní GPIO |
|Certifikace                   |ROZPOZNÁN <br> Přehled <br> RoHS <br> RAZÍ <br> UL   |
|Provozní teplota           |0 až 27 stupňů C (sestavení Azure Percept Vision SoM s bydlením) <br> -10 až 70 stupňů C (čip pro SoM Vision) |
|Dotyková teplota               |<= 48 stupňů C |
|Relativní vlhkost               |8% až 90%    |
