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
ms.openlocfilehash: 164f5803b6e9e62447423735e98f6e4c36c73f13
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91877175"
---
### <a name="add-relationships"></a>Přidat relace

V šabloně zařízení **brány lva Edge** v části **moduly/lva Edge Gateway**vyberte **relace**. Vyberte **+ Přidat relaci** a přidejte následující dvě relace:

|Zobrazovaný název               |Name          |Cíl |
|-------------------------- |------------- |------ |
|Detektor pohybu LVA Edge   |Použijte výchozí.   |Zařízení detektoru pohybu LVA Edge |
|Detektor objektu LVA Edge   |Použijte výchozí.   |Zařízení LVA Edge pro detektor objektů |

Pak vyberte **Uložit**.

:::image type="content" source="media/iot-central-video-analytics-part4/relationships.png" alt-text="Přidat relace":::

### <a name="add-views"></a>Přidání zobrazení

Šablona zařízení **brány lva Edge** neobsahuje žádné definice zobrazení.

Přidání zobrazení do šablony zařízení:

1. V šabloně zařízení **brány lva Edge** přejděte do **zobrazení** a vyberte **vizualizaci dlaždice zařízení** .

1. Jako název zobrazení zadejte *zařízení brány lva Edge* .

1. Přidejte do zobrazení následující dlaždice:

    * Dlaždice s vlastnostmi **informací o zařízení** : **model zařízení**, **výrobce**, **operační systém**, **Architektura procesoru**, **verze softwaru**, **Celková paměť**a **Celková velikost úložiště**.
    * Dlaždice spojnicového grafu s **volnou pamětí** a hodnotami telemetrie **prezenčního signálu systému**
    * Dlaždice historie událostí s následujícími událostmi: **vytvořit kameru**, **Odstranit kameru**, **restartovat modul**, **modul spuštěný**, **modul se zastavil**.
    * Dlaždice 2x1 Poslední známá hodnota znázorňující IoT Central telemetrie **stavu klienta** .
    * Dlaždice Poslední známá hodnota 2x1 zobrazující telemetrii **stavu modulu** .
    * Dlaždice Poslední známá hodnota 1x1 zobrazující telemetrii **prezenčního signálu systému** .
    * Dlaždice Poslední známá hodnota 1x1 zobrazující telemetrii **připojených fotoaparátů** .

    :::image type="content" source="media/iot-central-video-analytics-part4/gateway-dashboard.png" alt-text="Přidat relace":::

1. Vyberte **Uložit**.

### <a name="publish-the-device-template"></a>Publikování šablony zařízení

Předtím, než budete moci do aplikace přidat zařízení, je nutné publikovat šablonu zařízení:

1. V šabloně zařízení **brány lva Edge** vyberte **publikovat**.

1. Na stránce **Publikovat tuto šablonu zařízení na stránku aplikace** vyberte **publikovat**.

**Brána lva Edge** je teď k dispozici jako typ zařízení, který se má použít na stránce **zařízení** v aplikaci.

## <a name="add-a-gateway-device"></a>Přidat zařízení brány

Přidání zařízení **brány lva Edge** do aplikace:

1. Přejděte na stránku **zařízení** a vyberte šablonu zařízení **lva Edge Gateway** .

1. Vyberte **+ Nový**.

1. V dialogovém okně **vytvořit nové zařízení** změňte název zařízení na *lva Gateway 001*a změňte ID zařízení na *lva-Gateway-001*.

    > [!NOTE]
    > ID zařízení musí být v aplikaci jedinečné.

1. Vyberte **Vytvořit**.

Stav zařízení je **zaregistrovaný**.

### <a name="get-the-device-credentials"></a>Získání přihlašovacích údajů zařízení

Budete potřebovat přihlašovací údaje, které umožní zařízení připojit se k vaší IoT Central aplikaci. Přihlašovací údaje pro získání zařízení:

1. Na stránce **zařízení** vyberte zařízení **lva-Gateway-001** , které jste vytvořili.

1. Vyberte **Připojit**.

1. Na stránce **připojení zařízení** si poznamenejte v souboru *scratchpad.txt* **rozsahu ID**, **ID zařízení**a **primární klíč**zařízení. Tyto hodnoty použijete později.

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
