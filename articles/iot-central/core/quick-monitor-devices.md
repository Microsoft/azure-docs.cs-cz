---
title: Rychlý Start – monitorování zařízení v Azure IoT Central
description: Rychlý Start – jako operátor, Naučte se používat vaši aplikaci Azure IoT Central k monitorování vašich zařízení.
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 4c63a9833e6b9a9b243d289d79428ddef1468253
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99833877"
---
# <a name="quickstart-use-azure-iot-central-to-monitor-your-devices"></a>Rychlý Start: použití IoT Central Azure ke sledování vašich zařízení

*Tento článek je pro operátory, tvůrce a správce.*

V tomto rychlém startu se dozvíte, jak pomocí Microsoft Azure IoT Central aplikace monitorovat vaše zařízení a měnit nastavení.

## <a name="prerequisites"></a>Předpoklady

Než začnete, měli byste provést tři předchozí rychlé starty a [vytvořit aplikaci Azure IoT Central](./quick-deploy-iot-central.md), [Přidat simulované zařízení do aplikace IoT Central](./quick-create-simulated-device.md) a [nakonfigurovat pravidla a akce pro vaše zařízení](quick-configure-rules.md).

## <a name="receive-a-notification"></a>Přijmout oznámení

Azure IoT Central posílá oznámení o zařízení v podobě e-mailových zpráv. Jako tvůrce jste přidali pravidlo pro odeslání oznámení do operátoru, když vlhkost ve snímači připojeného zařízení překročila prahovou hodnotu. Jako operátor zkontrolujete e-maily s oznámením.

Otevřete e-mailovou zprávu, kterou jste dostali na konci [pravidla konfigurace a akcí pro](quick-configure-rules.md) rychlý Start zařízení. V e-mailu vyberte odkaz na zařízení:

:::image type="content" source="media/quick-monitor-devices/email.png" alt-text="Snímek obrazovky, který zobrazuje e-mail s oznámením":::

Zobrazení **přehledu** pro simulované zařízení, které jste vytvořili v předchozích rychlých startech, se otevře v prohlížeči:

:::image type="content" source="media/quick-monitor-devices/dashboard.png" alt-text="Snímek obrazovky zobrazující přehled zařízení, které spustilo oznámení":::

## <a name="investigate-an-issue"></a>Průzkum problému

Jako operátor můžete zobrazit informace o zařízení v zobrazeních **Přehled**, **informace** a **příkazy** . Tvůrce vytvořil zobrazení pro **správu zařízení** , ve kterém můžete upravovat informace o zařízení a nastavovat vlastnosti zařízení.

V grafu na řídicím panelu se zobrazuje graf vlhkosti zařízení. Rozhodnete se, že je vlhkost zařízení příliš vysoká.

## <a name="remediate-an-issue"></a>Oprava problému

Chcete-li provést změnu zařízení, použijte stránku **spravovat zařízení** .

Změňte **cílovou teplotu** na 80, aby se zařízení zahřívání a snížila vlhkost. Pokud chcete zařízení aktualizovat, klikněte na **Uložit** . Když zařízení potvrdí změnu nastavení, stav vlastnosti se změní na **Synchronizovaný**:

:::image type="content" source="media/quick-monitor-devices/change-settings.png" alt-text="Snímek obrazovky se stavem aktualizované cílové teploty pro zařízení":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili:

* Přijmout oznámení
* Průzkum problému
* Oprava problému

Teď, když už víte, jak zařízení monitorovat, je navržený další krok:

> [!div class="nextstepaction"]
> [Sestavování a Správa šablon zařízení](howto-set-up-template.md).
