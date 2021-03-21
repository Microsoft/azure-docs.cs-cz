---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 04/03/2020
ms.author: crtreasu
ms.openlocfilehash: 43c8c578587c65b6e0317caf77ff2d0604cb4fa3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102044570"
---
### <a name="build-and-deploy-the-app"></a>Sestavení a nasazení aplikace

Otevřete `.sln` soubor vygenerovaný Unity. Změňte konfiguraci buildu na následující.

:::image type="content" source="./media/object-anchors-quickstarts-unity/aoa-unity-vs-build-config.png" alt-text="konfigurace sestavení":::

V dalším kroku budete muset nakonfigurovat **IP adresu vzdáleného počítače** pro nasazení a ladění aplikace.

Klikněte pravým tlačítkem na projekt aplikace a vyberte **vlastnosti**. Na stránce Vlastnosti vyberte možnost **Vlastnosti konfigurace-> ladění**. Změňte hodnotu **název počítače** na IP adresu vašeho zařízení HoloLens a klikněte na **použít**.

:::image type="content" source="./media/object-anchors-quickstarts-unity/aoa-unity-vs-remote-debug.png" alt-text="vzdálené ladění":::

Zavřete stránku vlastností. Klikněte na **vzdálený počítač**. Aplikace by měla začít sestavovat a nasazovat na vzdálené zařízení. Ujistěte se, že je zařízení aktivní.
