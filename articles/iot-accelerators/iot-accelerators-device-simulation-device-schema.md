---
title: Schéma zařízení v řešení pro simulaci zařízení – Azure | Dokumentace Microsoftu
description: Tento článek popisuje schématu JSON, který definuje simulovaného zařízení v řešení pro simulaci zařízení.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: b325873819caff139727ec15d6aecd2d4be89c9e
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338151"
---
# <a name="understand-the-device-model-schema"></a>Pochopení schématu modelu zařízení

Simulace zařízení akcelerátoru řešení můžete použít ke generování testu zatížení pro řešení, která pomocí služby IoT Hub. Když nasadíte řešení simulace zařízení, je automaticky zřídí kolekce simulované zařízení. Můžete přizpůsobit existující Simulovaná zařízení nebo vytvořit vlastní.

Tento článek popisuje schématu modelu zařízení, která určuje funkce a chování simulovaných zařízení. Model zařízení je uložená v souboru JSON.

V následujících článcích se vztahují k aktuální článku:

* [Implementace chování modelu zařízení](iot-accelerators-device-simulation-device-behavior.md) popisuje soubory jazyka JavaScript, použijte k implementaci chování simulovaných zařízení.
* [Vytvoření nového simulovaného zařízení](iot-accelerators-device-simulation-create-simulated-device.md) vloží všechno dohromady a ukazuje, jak nasadit nový typ simulovaného zařízení do řešení.

V tomto článku získáte informace o těchto tématech:

>[!div class="checklist"]
> * Definovat model simulovaného zařízení pomocí souboru JSON
> * Zadejte vlastnosti simulovaného zařízení
> * Určete, které simulované zařízení odesílá telemetrická data
> * Určují metody typu cloud zařízení, které zařízení reaguje na

[!INCLUDE [iot-accelerators-device-schema](../../includes/iot-accelerators-device-schema.md)]

## <a name="next-steps"></a>Další postup

Tento článek popisuje jak vytvořit vlastní model vlastní simulované zařízení. Tento článek vám ukázali, jak do:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Definovat model simulovaného zařízení pomocí souboru JSON
> * Zadejte vlastnosti simulovaného zařízení
> * Určete, které simulované zařízení odesílá telemetrická data
> * Určují metody typu cloud zařízení, které zařízení reaguje na

Teď, když jste se dozvěděli o schématu JSON, navrhované dalším krokem je další způsob [implementují chování simulovaného zařízení](iot-accelerators-device-simulation-device-behavior.md).

Další informace pro vývojáře o řešení pro simulaci zařízení, najdete v článku [referenční příručka pro vývojáře](https://github.com/Azure/device-simulation-dotnet/wiki/Simulation-Service-Developer-Reference-Guide).
