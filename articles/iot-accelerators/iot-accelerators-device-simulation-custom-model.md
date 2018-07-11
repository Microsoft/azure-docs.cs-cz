---
title: Konfigurace modelu vlastní zařízení – Azure | Dokumentace Microsoftu
description: Tento článek popisuje postup konfigurace modelu vlastních zařízení v akcelerátoru řešení simulace zařízení.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 07/06/2018
ms.topic: conceptual
ms.openlocfilehash: 61a7c5314df541b4b44a286efe982f4bf93256e3
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967522"
---
# <a name="device-models"></a>Modely zařízení

Při konfiguraci simulace, které můžete použít existující model zařízení se i předdefinovanou sadu senzorů nebo vytvořit vlastní model s libovolným simulovaných senzorů. Vlastními senzory umožňují věrně modelovala skutečných zařízení.

## <a name="pre-configured-device-models"></a>Modely předem nakonfigurované zařízení

Simulace zařízení solution accelerator poskytuje tři modely předem nakonfigurované zařízení: dochlazovače, výtahů a nákladních vozidel.

Modely předem nakonfigurované zařízení mít řadu senzorů s Upřesnit chování definované v souboru jazyka JavaScript. Tyto chování nelze konfigurovat ve webovém uživatelském rozhraní.

Následující tabulka uvádí požadavky na konfiguraci pro každý model předem nakonfigurované zařízení:

| Model zařízení  | Senzor           | Jednotka  |
| ------------- | ---------------- | ----- |
| Chladič       | vlhkost         | %     |
|               | pressure         | psig  |
|               | Teplota      | F     |
| Hodnocení      | Dolní mez            |       |
|               | Vibrace        | mm    |
|               | Teplota      | F     |
| Truck         | Zeměpisná šířka         |       |
|               | Zeměpisná délka        |       |
|               | rychlost            | mph   |
|               | cargotemperature | F     |

## <a name="custom-device-models"></a>Modely vlastní zařízení

Zařízení vlastní modely umožňují modelovat snímače, které více věrně modelovala vlastní zařízení. Vlastní zařízení může mít až 10 vlastními senzory.

Když vyberete typ vlastního modelu, přidáte nový senzorů kliknutím **+ přidat senzor**.

[![Konfigurace senzorů](./media/iot-accelerators-device-simulation-custom-model/configuresensors-inline.png)](./media/iot-accelerators-device-simulation-custom-model/configuresensors-expanded.png#lightbox)

Vlastními senzory mají následující vlastnosti:

| Pole     | Popis |
| --------- | ----------- |
| Název snímače | Popisný název senzoru, jako **teploty** nebo **rychlost**.  |
| Chování  | Chování povolit telemetrická data se liší od jedné zprávy na další simulace data z reálného světa. **Přírůstek** zvýší hodnotu jednou v každé zprávě odeslán začínají na minimální hodnotu. Maximální hodnota je splněny, pak spustí přes znovu na minimální hodnotu. **Snížení** se chová stejně jako **přírůstek** ale počítá směrem dolů. **Random** chování vygeneruje náhodnou hodnotu mezi hodnotou minimální a maximální hodnoty. |
| Minimální hodnota | Nejnižší číslo v přípustném rozsahu. |
| Maximální hodnota | Nejvyšší číslo v přípustném rozsahu. |
| Jednotka      | Jednotka měření, jako je například ° F nebo MPH senzoru. |

## <a name="next-steps"></a>Další postup

V této příručce s postupy jste zjistili, jak nakonfigurovat vlastní zařízení model pro simulaci. V dalším kroku můžete chtít prozkoumat některé z nich [akcelerátory řešení IoT](about-iot-accelerators.md).