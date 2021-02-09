---
title: zahrnout soubor
description: zahrnout soubor
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: d9c2aea284a2ab84b5d45fe35a35785adfc88123
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832061"
---
### <a name="publish-the-device-template"></a>Publikování šablony zařízení

Předtím, než budete moci do aplikace přidat zařízení, je nutné publikovat šablonu zařízení:

1. V šabloně zařízení **lva Edge Gateway v2** vyberte **publikovat**.

1. Na stránce **Publikovat tuto šablonu zařízení na stránku aplikace** vyberte **publikovat**.

**Lva Edge Gateway v2** je teď k dispozici jako typ zařízení, který se má použít na stránce **zařízení** v aplikaci.

## <a name="migrate-the-gateway-device"></a>Migrace zařízení brány

Stávající zařízení **brány – 001** používá šablonu zařízení **lva Edge Gateway** . Pokud chcete použít nový manifest nasazení, migrujte zařízení do nové šablony zařízení:

Migrace zařízení **brány – 001** :

1. Přejděte na stránku **zařízení** a vyberte zařízení **brány – 001** , které chcete v seznamu zvýraznit.

1. Vyberte možnost **Migrate** (Migrovat). Pokud není ikona **migrace** zobrazená, vyberte **...** a podívejte se na další možnosti.

    :::image type="content" source="media/iot-central-video-analytics-part4/migrate-device.png" alt-text="Migrace zařízení brány na novou verzi":::

1. V seznamu v dialogovém okně **migrovat** vyberte **lva Edge Gateway v2** a pak vyberte **migrovat**.

Po několika sekundách se migrace dokončí. Vaše zařízení teď používá šablonu zařízení **lva Edge Gateway v2** s vlastním přizpůsobeným manifestem nasazení.

V tuto chvíli nejsou k dispozici žádná zařízení pomocí původní šablony zařízení **brány lva Edge** . Odstranit tuto šablonu zařízení:

1. Přejděte na stránku **šablony zařízení** a vyberte šablonu zařízení **lva Edge Gateway** .

1. Vyberte **Odstranit** a odstraňte šablonu zařízení.

### <a name="get-the-device-credentials"></a>Získání přihlašovacích údajů zařízení

Budete potřebovat přihlašovací údaje, které umožní zařízení připojit se k vaší IoT Central aplikaci. Přihlašovací údaje pro získání zařízení:

1. Na stránce **zařízení** vyberte zařízení **Gateway – 001** .

1. Vyberte **Connect** (Připojit).

1. Na stránce **připojení zařízení** si poznamenejte v souboru *scratchpad.txt* **rozsahu ID**, **ID zařízení** a **primární klíč** zařízení. Tyto hodnoty použijete později.

1. Ujistěte se, že je metoda připojení nastavená na **sdílený přístupový podpis**.

1. Vyberte **Zavřít**.

