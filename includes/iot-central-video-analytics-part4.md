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
ms.openlocfilehash: de916fcbe0623185821e2f5da15a8f9cf71dfd4e
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94426738"
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

### <a name="get-the-device-credentials"></a>Získání přihlašovacích údajů zařízení

Budete potřebovat přihlašovací údaje, které umožní zařízení připojit se k vaší IoT Central aplikaci. Přihlašovací údaje pro získání zařízení:

1. Na stránce **zařízení** vyberte zařízení **Gateway – 001** .

1. Vyberte **Connect** (Připojit).

1. Na stránce **připojení zařízení** si poznamenejte v souboru *scratchpad.txt* **rozsahu ID** , **ID zařízení** a **primární klíč** zařízení. Tyto hodnoty použijete později.

1. Ujistěte se, že je metoda připojení nastavená na **sdílený přístupový podpis**.

1. Vyberte **Zavřít**.

## <a name="next-steps"></a>Další kroky

Nyní jste vytvořili aplikaci IoT Central pomocí šablony aplikace **video Analytics – objekt a detekce pohybu** , vytvořili jste šablonu zařízení pro zařízení brány a do aplikace jste přidali zařízení brány.

Pokud chcete vyzkoušet video Analytics – objekt a detekci pohybu pomocí IoT Edge modulů, na kterých běží cloudový virtuální počítač s simulovanými video streamy:

> [!div class="nextstepaction"]
> [Vytvoření instance IoT Edge pro video Analytics (virtuální počítač se systémem Linux)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-vm.md)

Pokud si chcete vyzkoušet video Analytics – objekt a detekci pohybu pomocí IoT Edgech modulů, na kterých běží reálné zařízení s reálným **ONVIF** fotoaparátem:

> [!div class="nextstepaction"]
> [Vytvoření instance IoT Edge pro video Analytics (Intel NUC)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-nuc.md)
