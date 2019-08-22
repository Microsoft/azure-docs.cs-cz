---
title: Přidání skutečného zařízení do aplikace Azure IoT Central | Microsoft Docs
description: Jako operátor přidejte skutečné zařízení do aplikace Azure IoT Central.
author: sarahhubbard
ms.author: sahubbar
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 7191c98cfc522068dbea576a8f81776ae4301da8
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878170"
---
# <a name="tutorial-add-a-simulated-device-to-your-azure-iot-central-application-preview-features"></a>Kurz: Přidání simulovaného zařízení do aplikace Azure IoT Central (funkce ve verzi Preview)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

V tomto kurzu se dozvíte, jak přidat a nakonfigurovat simulované zařízení do IoT Central aplikace Microsoft Azure.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání nového simulovaného zařízení
> * Použití simulovaného zařízení v prostředí pro sestavování

## <a name="prerequisites"></a>Požadavky

Než začnete, je potřeba, aby tvůrce dokončil kurz prvního tvůrce, který vytvořil aplikaci Azure IoT Central:

* [Definování nového typu zařízení](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) (povinné)

## <a name="add-a-simulated-device"></a>Přidání simulovaného zařízení

Pokud chcete do aplikace přidat reálné zařízení, použijte šablonu zařízení **snímače prostředí** , kterou jste vytvořili v kurzu [definování nového typu zařízení](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

1. Pokud chcete přidat nové zařízení jako operátor, vyberte v levé navigační nabídce možnost **zařízení** . Karta **zařízení** zobrazuje **všechna zařízení** a šablonu zařízení **snímače životního prostředí** .

1. Pokud chcete přidat simulované zařízení snímače životního prostředí, vyberte **+ Nový**. Použijte navržené **ID zařízení** nebo zadejte svoje **ID zařízení**s malými písmeny. Můžete také zadat název nového zařízení. Přepněte **simulované** přepnutí na **zapnuto** a pak vyberte **vytvořit**.

    ![Simulované zařízení](./media/tutorial-add-device-pnp/simulated-device.png)

Nyní můžete pracovat se zobrazeními vytvořenými tvůrcem pro šablonu zařízení pomocí simulovaných dat.

## <a name="use-a-simulated-device-to-improve-views"></a>Použití simulovaného zařízení ke zlepšení zobrazení

Po vytvoření nového simulovaného zařízení může tvůrce pomocí tohoto zařízení pokračovat ve zlepšování a sestavování zobrazení pro šablonu zařízení.

1. V zobrazení zařízení zkopírujte **ID zařízení** simulovaného zařízení, které jste vytvořili.

1. Zvolte kartu **šablony zařízení** v levé navigační nabídce a vyberte šablonu senzoru pro **životní prostředí** .

1. Vyberte libovolné zobrazení, které chcete upravit, nebo vytvořte nové zobrazení. Klikněte na **Konfigurovat verzi Preview zařízení**. Tady si můžete vybrat, jestli nemá žádné zařízení ve verzi Preview, a to pomocí reálného zařízení, které můžete nakonfigurovat pro testování, nebo z existujícího zařízení, které jste přidali do IoT Central.

1. Zvolte **možnost vybrat ze spuštěného zařízení** a zadejte do **ID zařízení** simulovaného zařízení, které jste zkopírovali.

1. Zvolte **Použít**. Teď můžete vidět stejné simulované zařízení v zobrazeních šablon zařízení. Toto zobrazení je zvláště užitečné pro grafy a další vizualizace.

    ![Konfigurace zařízení Preview](./media/tutorial-add-device-pnp/configure-preview.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

* Přidání nového simulovaného zařízení
* Použití simulovaného zařízení v prostředí pro sestavování

Teď, když jste připojili simulované zařízení k aplikaci Azure IoT Central, tady je několik navrhovaných dalších kroků.

Jako operátor se naučíte tyto postupy:

> [!div class="nextstepaction"]
> [Konfigurace pravidel](tutorial-configure-rules.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

Jako vývojář zařízení se naučíte tyto postupy:

> [!div class="nextstepaction"]
> [Připojení zařízení IoT DevKit MXChip k aplikaci Azure IoT Central](howto-connect-devkit-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)



