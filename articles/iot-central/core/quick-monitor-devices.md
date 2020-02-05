---
title: Rychlý Start – monitorování zařízení v Azure IoT Central
description: Jako operátor se naučíte, jak pomocí aplikace IoT Central v Azure monitorovat vaše zařízení.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: e5185d78b0ffc17e861f49d294af65ddcd200d07
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027745"
---
# <a name="quickstart-use-azure-iot-central-to-monitor-your-devices"></a>Rychlý Start: použití IoT Central Azure ke sledování vašich zařízení

*Tento článek je pro operátory, tvůrce a správce.*

V tomto rychlém startu se dozvíte, jak pomocí Microsoft Azure IoT Central aplikace monitorovat vaše zařízení a měnit nastavení.

## <a name="prerequisites"></a>Požadavky

Než začnete, měli byste provést tři předchozí rychlé starty a [vytvořit aplikaci Azure IoT Central](./quick-deploy-iot-central.md), [Přidat simulované zařízení do aplikace IoT Central](./quick-create-pnp-device.md) a [nakonfigurovat pravidla a akce pro vaše zařízení](quick-configure-rules.md).

## <a name="receive-a-notification"></a>Přijmout oznámení

Azure IoT Central posílá oznámení o zařízení v podobě e-mailových zpráv. Tvůrce přidal pravidlo pro odeslání oznámení v případě, že teplota v snímači připojeného zařízení překročila prahovou hodnotu. Zprávu najdete v e-mailech odeslaných na účet, který tvůrce zvolil pro příjem oznámení.

Otevřete e-mailovou zprávu, kterou jste dostali na konci [pravidla konfigurace a akcí pro](quick-configure-rules.md) rychlý Start zařízení. V e-mailu vyberte odkaz na zařízení:

![E-mail s oznámením výstrah](media/quick-monitor-devices/email.png)

Zobrazení **přehledu** pro simulované zařízení, které jste vytvořili v předchozích rychlých startech, se otevře v prohlížeči:

![Zařízení, které aktivovalo e-mailovou zprávu s oznámením](media/quick-monitor-devices/dashboard.png)

## <a name="investigate-an-issue"></a>Průzkum problému

Jako operátor můžete zobrazit informace o zařízení v zobrazeních **Přehled**, **informace**a **příkazy** . Tvůrce vytvořil zobrazení pro **správu zařízení** , ve kterém můžete upravovat informace o zařízení a nastavovat vlastnosti zařízení.

Kliknutím na zobrazení **přehledu** zobrazíte informace o zařízení.

Graf na řídicím panelu vykresluje teplotní křivku zařízení. Rozhodnete se, že je teplota zařízení příliš vysoká.

## <a name="remediate-an-issue"></a>Oprava problému

Chcete-li provést změnu zařízení, použijte stránku **spravovat zařízení** .

Změňte **rychlost ventilátoru** na 500, aby se zařízení vychlado. Pokud chcete zařízení aktualizovat, klikněte na **Uložit** . Když zařízení potvrdí změnu nastavení, stav vlastnosti se změní na **Synchronizovaný**:

![Aktualizace nastavení](media/quick-monitor-devices/change-settings.png)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili:

* Přijmout oznámení
* Průzkum problému
* Oprava problému

Teď, když už víte, jak zařízení monitorovat, je navržený další krok:

> [!div class="nextstepaction"]
> [Sestavování a Správa šablon zařízení](howto-set-up-template.md).
