---
title: Simulované zařízení chování v řešení vzdáleného monitorování – Azure | Dokumentace Microsoftu
description: Tento článek popisuje, jak pomocí JavaScriptu můžete definovat chování Simulovaná zařízení v řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: 70f9ccbbe737bad4d6f88365e804d4421c418d28
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53164003"
---
# <a name="implement-the-device-model-behavior"></a>Implementace chování modelu zařízení

Tento článek [pochopení schématu modelu zařízení](iot-accelerators-remote-monitoring-device-schema.md) popisuje schéma definující model simulované zařízení. Tento článek uvedené dva typy soubor jazyka JavaScript, které implementují chování Simulovaná zařízení:

- **Stav** soubory jazyka JavaScript, která spustí v pravidelných intervalech aktualizovat vnitřní stav zařízení.
- **Metoda** soubory jazyka JavaScript, která se spouští při řešení volá metodu na zařízení.

V tomto článku získáte informace o těchto tématech:

>[!div class="checklist"]
> * Ovládací prvek stavu simulovaného zařízení
> * Definujte, jak Simulovaná zařízení reaguje na volání metody v řešení vzdáleného monitorování
> * Ladění skriptů

[!INCLUDE [iot-accelerators-device-behavior](../../includes/iot-accelerators-device-behavior.md)]

## <a name="next-steps"></a>Další postup

Tento článek popisuje jak definovat chování modelu vlastní simulované zařízení. Tento článek vám ukázali, jak do:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Ovládací prvek stavu simulovaného zařízení
> * Definujte, jak Simulovaná zařízení reaguje na volání metody v řešení vzdáleného monitorování
> * Ladění skriptů

Teď, když jste zjistili, jak můžete určit chování Simulovaná zařízení, navrhované dalším krokem je další způsob [vytvoření simulovaného zařízení](iot-accelerators-remote-monitoring-create-simulated-device.md).

Další informace pro vývojáře o řešení vzdáleného monitorování najdete v tématu:

* [Referenční příručka pro vývojáře](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Průvodce řešením potíží pro vývojáře](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->
